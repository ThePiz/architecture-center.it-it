---
title: Classificazione delle immagini per richieste di indennizzo assicurativo in Azure
description: Scenario collaudato per integrare l'elaborazione di immagini nelle applicazioni Azure.
author: david-stanford
ms.date: 07/05/2018
ms.openlocfilehash: 361a88234fd9ed918ab7664893f86666b4328b8c
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060830"
---
# <a name="image-classification-for-insurance-claims-on-azure"></a>Classificazione delle immagini per richieste di indennizzo assicurativo in Azure

Questo scenario di esempio è applicabile ad aziende che devono elaborare immagini.

Le potenziali applicazioni includono la classificazione di immagini per un sito Web di abbigliamento, l'analisi di testo e immagini per richieste di indennizzo assicurativo e la comprensione di dati di telemetria provenienti da screenshot di giochi. Generalmente le aziende devono sviluppare competenze nei modelli di apprendimento automatico, eseguire il training dei modelli e infine eseguire un processo personalizzato sulle immagini per ricavarne dati.

Con servizi di Azure come l'API Visione artificiale e Funzioni di Azure, le aziende possono eliminare l'esigenza di gestire singoli server riducendo al tempo stesso i costi e sfruttando le competenze già sviluppate da Microsoft in relazione all'elaborazione di immagini con Servizi cognitivi. Questo scenario riguarda specificamente l'elaborazione di immagini. Per altre esigenze di intelligenza artificiale, prendere in considerazione l'intera famiglia di prodotti [Servizi cognitivi][cognitive-docs].

## <a name="related-use-cases"></a>Casi d'uso correlati

Prendere in considerazione questo scenario per i casi d'uso seguenti:

* Classificare le immagini in un sito Web di abbigliamento.
* Classificare i dati di telemetria provenienti da screenshot di giochi.

## <a name="architecture"></a>Architettura

![Architettura di app intelligenti: visione artificiale][architecture-computer-vision]

Questo scenario include i componenti back-end di un'applicazione Web o per dispositivi mobili. Il flusso dei dati nello scenario avviene come segue:

1. Funzioni di Azure funge da livello API. Le API consentono all'applicazione di caricare immagini e recuperare dati da Cosmos DB.

2. Un'immagine caricata tramite una chiamata API viene archiviata nell'archivio BLOB.

3. L'aggiunta di nuovi file all'archivio BLOB attiva una notifica di Griglia di eventi che verrà inviata a una funzione di Azure.

4. Funzioni di Azure invia un collegamento al file appena caricato all'API Visione artificiale per l'analisi.

5. I dati restituiti dall'API Visione artificiale vengono immessi da Funzioni di Azure in Cosmos DB in modo da rendere persistenti i risultati dell'analisi insieme ai metadati dell'immagine.

### <a name="components"></a>Componenti

* L'[API Visione artificiale][computer-vision-docs], appartenente alla famiglia di prodotti Servizi cognitivi, viene usata per recuperare informazioni su ogni immagine.

* [Funzioni di Azure][functions-docs] fornisce l'API back-end per l'applicazione Web, nonché l'elaborazione di eventi per le immagini caricate.

* [Griglia di eventi][eventgrid-docs] attiva un evento quando una nuova immagine viene caricata nell'archivio BLOB. L'immagine viene quindi elaborata con Funzioni di Azure.

* Nell'[archivio BLOB][storage-docs] vengono archiviati tutti i file di immagine caricati nell'applicazione Web, nonché i file statici da essa utilizzati.

* In [Cosmos DB][cosmos-docs] vengono archiviati i metadati relativi a ogni immagine caricata, inclusi i risultati dell'elaborazione dell'API Visione artificiale.

## <a name="alternatives"></a>Alternative

* [Servizio visione artificiale personalizzato][custom-vision-docs]. L'API Visione artificiale restituisce un set di [categorie basate sulla tassonomia][cv-categories]. Se è necessario elaborare informazioni non restituite dall'API Visione artificiale, prendere in considerazione il Servizio visione artificiale personalizzato, che consente di creare classificatori di immagini personalizzati.

* [Ricerca di Azure][azure-search-docs]. Se il caso d'uso include l'esecuzione di query sui metadati per trovare le immagini che soddisfano criteri specifici, valutare la possibilità di usare Ricerca di Azure. La funzionalità di [ricerca cognitiva][cognitive-search], attualmente in anteprima, integra perfettamente questo flusso di lavoro.

## <a name="considerations"></a>Considerazioni

### <a name="scalability"></a>Scalabilità

Per la maggior parte, tutti i componenti di questo scenario sono servizi gestiti con scalabilità automatica. Esistono alcune eccezioni significative. Funzioni di Azure ha un limite massimo di 200 istanze. Se è necessaria una scalabilità superiore, prendere in considerazione più aree o piani app.

Cosmos DB non offre scalabilità automatica in termini di unità richiesta (UR) sottoposte a provisioning.  Per indicazioni su come stimare i propri requisiti, vedere l'articolo relativo alle [unità richiesta][request-units] nella documentazione. Per sfruttare appieno la scalabilità in Cosmos DB, è consigliabile esaminare anche le [chiavi di partizione][partition-key].

I database NoSQL spesso privilegiano la disponibilità, la scalabilità e la partizione rispetto alla coerenza (nel senso del teorema CAP).  Nel caso dei modelli di dati chiave-valore usati in questo scenario, tuttavia,la coerenza delle transazione è raramente necessaria perché le operazioni sono per la maggior parte atomiche per definizione. Altre indicazioni per [scegliere l'archivio dati corretto](../../guide/technology-choices/data-store-overview.md) sono disponibili in Centro architetture.

Per indicazioni generali sulla progettazione di soluzioni scalabili, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Sicurezza

[Identità del servizio gestite][msi] vengono usate per offrire l'accesso ad altre risorse interne all'account e quindi assegnate a Funzioni di Azure. In tali identità consentire l'accesso solo alle risorse necessarie in modo da non esporre nient'altro alle funzioni e potenzialmente ai clienti.  

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

Tutti i componenti di questo scenario sono gestiti e quindi automaticamente resilienti a livello di area.

Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].

## <a name="pricing"></a>Prezzi

Per esaminare il costo di esecuzione dello scenario, nel calcolatore dei costi sono preconfigurati tutti i servizi. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base

Sono stati definiti tre profili di costo di esempio in base alla quantità di traffico, supponendo che tutte le immagini siano di 100 KB.

* [Small][pricing]: correlato all'elaborazione di meno di 5000 immagini al mese.
* [Medium][medium-pricing]: correlato all'elaborazione di 500.000 immagini al mese.
* [Large][large-pricing]: correlato all'elaborazione di 50 milioni di immagini al mese.

## <a name="related-resources"></a>Risorse correlate

Per un percorso di apprendimento guidato per questo scenario, vedere [Build a serverless web app in Azure][serverless] (Creare un'app Web senza server in Azure).  

Prima dell'inclusione in un ambiente di produzione, esaminare le [procedure consigliate][functions-best-practices] per Funzioni di Azure.

<!-- links -->
[pricing]: https://azure.com/e/f9b59d238b43423683db73f4a31dc380
[medium-pricing]: https://azure.com/e/7c7fc474db344b87aae93bc29ae27108
[large-pricing]: https://azure.com/e/cbadbca30f8640d6a061f8457a74ba7d
[functions-docs]: /azure/azure-functions/
[computer-vision-docs]: /azure/cognitive-services/computer-vision/home
[storage-docs]: /azure/storage/
[azure-search-docs]: /azure/search/
[cognitive-search]: /azure/search/cognitive-search-concept-intro
[architecture-computer-vision]: ./media/architecture-computer-vision.png
[serverless]: /azure/functions/tutorial-static-website-serverless-api-with-database
[cosmos-docs]: /azure/cosmos-db/
[eventgrid-docs]: /azure/event-grid/
[cognitive-docs]: /azure/#pivot=products&panel=ai
[custom-vision-docs]: /azure/cognitive-services/Custom-Vision-Service/home
[cv-categories]: /azure/cognitive-services/computer-vision/home#the-86-category-concept
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[functions-best-practices]: /azure/azure-functions/functions-best-practices
[msi]: /azure/app-service/app-service-managed-service-identity
[request-units]: /azure/cosmos-db/request-units
[partition-key]: /azure/cosmos-db/partition-data