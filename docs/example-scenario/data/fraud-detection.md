---
title: Rilevamento delle frodi in tempo reale
titleSuffix: Azure Example Scenarios
description: Rilevare le attività fraudolente in tempo reale con Hub eventi e Analisi di flusso di Azure.
author: alexbuckgit
ms.date: 07/05/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
social_image_url: /azure/architecture/example-scenario/data/media/architecture-fraud-detection.png
ms.openlocfilehash: fc2222874b807a53b5e575973aa6dd4eca63fa3b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640049"
---
# <a name="real-time-fraud-detection-on-azure"></a>Rilevamento delle frodi in tempo reale in Azure

Questo scenario di esempio è utile per le organizzazioni che devono analizzare i dati in tempo reale per rilevare transazioni fraudolente o altre attività anomale.

Le potenziali applicazioni includono l'identificazione di attività con carte di credito o chiamate da telefono cellulare fraudolente. I sistemi di analisi online tradizionali possono impiegare ore per trasformare e analizzare i dati per l'identificazione di attività anomale.

Con servizi di Azure completamente gestiti come Hub eventi e Analisi di flusso, le aziende possono eliminare l'esigenza di gestire singoli server riducendo al tempo stesso i costi e sfruttando le competenze di Microsoft nell'inserimento dati e l'analisi in tempo reale a livello di cloud. Questo scenario riguarda specificamente il rilevamento di attività fraudolente. Per altre esigenze di analisi dei dati, è consigliabile esaminare l'elenco dei [servizi di analisi di Azure][product-category] disponibili.

Questo esempio rappresenta una parte di un'architettura e una strategia di elaborazione dati più ampie. Più avanti in questo articolo sono illustrate altre opzioni per questo aspetto dell'architettura complessiva.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

- Rilevamento di chiamate da telefono cellulare fraudolente in scenari di telecomunicazioni.
- Identificazione di transazioni con carta di credito fraudolente per istituti bancari.
- Identificazione di acquisti fraudolenti in scenari di vendita al dettaglio o e-commerce.

## <a name="architecture"></a>Architettura

![Panoramica dell'architettura dei componenti di Azure in uno scenario di rilevamento delle frodi in tempo reale][architecture]

Questo scenario include i componenti back-end di una pipeline di analisi in tempo reale. Il flusso dei dati nello scenario avviene come segue:

1. I metadati delle chiamate da telefono cellulare vengono inviati dal sistema di origine a un'istanza di Hub eventi di Azure.
2. Viene avviato un processo di Analisi di flusso che riceve i dati tramite l'origine dell'hub eventi.
3. Il processo di Analisi di flusso esegue una query predefinita per trasformare il flusso di input e analizzarlo in base a un algoritmo per le transazioni fraudolente. Questa query usa una finestra a cascata per segmentare il flusso in unità temporali distinte.
4. Il processo di Analisi di flusso scrive il flusso trasformato che rappresenta le chiamate fraudolente rilevate in un sink di output nell'archivio BLOB di Azure.

### <a name="components"></a>Componenti

- [Hub eventi di Azure][docs-event-hubs] è una piattaforma di streaming in tempo reale e un servizio di inserimento di eventi in grado di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. In questo scenario, Hub eventi riceve tutti i metadati delle telefonate da analizzare per individuare le attività fraudolente.
- [Analisi di flusso di Azure][docs-stream-analytics] è un motore di elaborazione di eventi che può analizzare volumi elevati di dati trasmessi da dispositivi e altre origini dati. Supporta anche l'estrazione di informazioni dai flussi di dati per identificare modelli e relazioni. Tali modelli possono attivare altre azioni downstream. In questo scenario, Analisi di flusso trasforma il flusso di input da Hub eventi per identificare le chiamate fraudolente.
- L'[archivio BLOB](/azure/storage/blobs/storage-blobs-introduction) viene usato in questo scenario per archiviare i risultati del processo di Analisi di flusso di Azure.

## <a name="considerations"></a>Considerazioni

### <a name="alternatives"></a>Alternative

Per l'inserimento di messaggi in tempo reale, l'archiviazione dei dati, l'elaborazione dei flussi, l'archiviazione dei dati analitici, l'analisi e la creazione di report sono disponibili molte opzioni tecnologiche. Per una panoramica di queste opzioni, delle relative funzionalità e dei principali criteri di selezione, vedere [Architetture per Big Data Inserimento di messaggi in tempo reale](/azure/architecture/data-guide/technology-choices/real-time-ingestion) nella Guida all'architettura dei dati di Azure.

Algoritmi più complessi per il rilevamento delle frodi possono inoltre essere offerti da vari servizi di apprendimento automatico in Azure. Per una panoramica di queste opzioni, vedere l'articolo relativo alle [opzioni tecnologiche per l'apprendimento automatico](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning) in [Guida all'architettura dei dati di Azure](../../data-guide/index.md).

### <a name="availability"></a>Disponibilità

Monitoraggio di Azure offre interfacce utente unificate per il monitoraggio di diversi servizi di Azure. Per altre informazioni, vedere l'articolo relativo al [monitoraggio in Microsoft Azure](/azure/monitoring-and-diagnostics/monitoring-overview). Sia Hub eventi che Analisi di flusso sono integrati in Monitoraggio di Azure.

### <a name="scalability"></a>Scalabilità

I componenti di questo scenario sono progettati per l'inserimento con iperscalabilità e l'analisi parallela elevata in tempo reale. Hub eventi di Azure è una piattaforma altamente scalabile in grado di ricevere ed elaborare milioni di eventi al secondo con bassa latenza. Hub eventi può [aumentare automaticamente](/azure/event-hubs/event-hubs-auto-inflate) il numero di unità elaborate per soddisfare le esigenze di utilizzo. Analisi di flusso di Azure può analizzare volumi elevati di dati in streaming da molte origini. È possibile aumentare le prestazioni di Analisi di flusso aumentando il numero di [unità di streaming](/azure/stream-analytics/stream-analytics-streaming-unit-consumption) allocate per l'esecuzione del processo di streaming.

Per indicazioni generali sulla progettazione di soluzioni scalabili, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Security

Hub eventi di Azure protegge i dati tramite un [modello di sicurezza e autenticazione][docs-event-hubs-security-model] basato su una combinazione di token di firma di accesso condiviso e autori di eventi. Un autore di eventi definisce un endpoint virtuale per un hub eventi. L'autore è utilizzabile solo per inviare messaggi a un hub eventi. Non è possibile ricevere messaggi da un autore.

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [progettazione di applicazioni Azure affidabili](../../reliability/index.md).

## <a name="deploy-the-scenario"></a>Distribuire lo scenario

Per distribuire questo scenario, è possibile seguire l'[esercitazione dettagliata][tutorial] che illustra come distribuirne manualmente ogni componente. L'esercitazione offre anche un'applicazione client .NET per generare metadati di telefonate di esempio e inviare tali dati a un'istanza di hub eventi.

## <a name="pricing"></a>Prezzi

Per esaminare il costo di esecuzione dello scenario, nel calcolatore dei costi sono preconfigurati tutti i servizi. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base al volume di dati previsto.

Sono stati definiti tre profili di costo di esempio in base alla quantità di traffico prevista.

- [Small][small-pricing]: elaborazione di un milione di eventi tramite un'unità di streaming standard al mese.
- [Medium][medium-pricing]: elaborazione di 100 milioni di eventi tramite cinque unità di streaming standard al mese.
- [Large][large-pricing]: elaborazione di 999 milioni di eventi tramite 20 unità di streaming standard al mese.

## <a name="related-resources"></a>Risorse correlate

In scenari di rilevamento delle frodi più complessi può essere utile un modello di apprendimento automatico. Per informazioni sugli scenari basati su Machine Learning Server, vedere l'articolo relativo al [rilevamento delle frodi con Machine Learning Server][r-server-fraud-detection]. Per altri modelli di soluzioni con Machine Learning Server, vedere l'articolo relativo a [scenari di data science e modelli di soluzioni][docs-r-server-sample-solutions]. Per una soluzione di esempio con Azure Data Lake Analytics, vedere [Using Azure Data Lake and R for Fraud Detection][technet-fraud-detection] (Uso di Azure Data Lake e R per il rilevamento delle frodi).

<!-- links -->
[product-category]: https://azure.microsoft.com/product-categories/analytics/
[tutorial]: /azure/stream-analytics/stream-analytics-real-time-fraud-detection
[small-pricing]: https://azure.com/e/74149ec312c049ccba79bfb3cfa67606
[medium-pricing]: https://azure.com/e/4fc94f7376de484d8ae67a6958cae60a
[large-pricing]: https://azure.com/e/7da8804396f9428a984578700003ba42
[architecture]: ./media/architecture-fraud-detection.png
[docs-event-hubs]: /azure/event-hubs/event-hubs-what-is-event-hubs
[docs-event-hubs-security-model]: /azure/event-hubs/event-hubs-authentication-and-security-model-overview
[docs-stream-analytics]: /azure/stream-analytics/stream-analytics-introduction
[docs-r-server-sample-solutions]: /machine-learning-server/r/sample-solutions
[r-server-fraud-detection]: https://microsoft.github.io/r-server-fraud-detection/
[technet-fraud-detection]: https://blogs.technet.microsoft.com/machinelearning/2017/06/28/using-azure-data-lake-and-r-for-fraud-detection/
[scalability]: /azure/architecture/checklist/scalability
[security]: /azure/security/
