---
title: Classificazione delle immagini per richieste di indennizzo assicurativo
titleSuffix: Azure Example Scenarios
description: Integrare l'elaborazione di immagini nelle applicazioni Azure.
author: david-stanford
ms.date: 07/05/2018
ms.openlocfilehash: 12dd197c6df4a8d7a90a09436d86ce4a9e5ccc72
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643446"
---
# <a name="image-classification-for-insurance-claims-on-azure"></a>Classificazione delle immagini per richieste di indennizzo assicurativo in Azure

Questo scenario è pertinente per le aziende che devono elaborare immagini.

Le potenziali applicazioni includono la classificazione di immagini per un sito Web di abbigliamento, l'analisi di testo e immagini per richieste di indennizzo assicurativo e la comprensione di dati di telemetria provenienti da screenshot di giochi. Generalmente le aziende devono sviluppare competenze nei modelli di apprendimento automatico, eseguire il training dei modelli e infine eseguire un processo personalizzato sulle immagini per ricavarne dati.

Con servizi di Azure come l'API Visione artificiale e Funzioni di Azure, le aziende possono eliminare l'esigenza di gestire singoli server riducendo al tempo stesso i costi e sfruttando le competenze già sviluppate da Microsoft in relazione all'elaborazione di immagini con Servizi cognitivi. Questo scenario di esempio riguarda specificamente un caso d'uso di elaborazione di immagini. Per altre esigenze di intelligenza artificiale, prendere in considerazione l'intera famiglia di prodotti [Servizi cognitivi](/azure/#pivot=products&panel=ai).

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

- Classificazione delle immagini in un sito Web di abbigliamento.
- Classificazione dei dati di telemetria provenienti da screenshot di giochi.

## <a name="architecture"></a>Architettura

![Architettura per la classificazione delle immagini][architecture]

Questo scenario include i componenti back-end di un'applicazione Web o per dispositivi mobili. Il flusso dei dati nello scenario avviene come segue:

1. Il livello API viene compilato usando Funzioni di Azure. Le API consentono all'applicazione di caricare immagini e recuperare dati da Cosmos DB.
2. Un'immagine caricata tramite una chiamata API viene archiviata nell'archivio BLOB.
3. L'aggiunta di nuovi file all'archivio BLOB attiva una notifica di Griglia di eventi che verrà inviata a una funzione di Azure.
4. Funzioni di Azure invia un collegamento al file appena caricato all'API Visione artificiale per l'analisi.
5. I dati restituiti dall'API Visione artificiale vengono immessi da Funzioni di Azure in Cosmos DB in modo da rendere persistenti i risultati dell'analisi insieme ai metadati dell'immagine.

### <a name="components"></a>Componenti

- L'[API Visione artificiale](/azure/cognitive-services/computer-vision/home), appartenente alla famiglia di prodotti Servizi cognitivi, viene usata per recuperare informazioni su ogni immagine.
- [Funzioni di Azure](/azure/azure-functions/functions-overview) fornisce l'API back-end per l'applicazione Web, nonché l'elaborazione di eventi per le immagini caricate.
- [Griglia di eventi](/azure/event-grid/overview) attiva un evento quando una nuova immagine viene caricata nell'archivio BLOB. L'immagine viene quindi elaborata con Funzioni di Azure.
- Nell'[archivio BLOB](/azure/storage/blobs/storage-blobs-introduction) vengono archiviati tutti i file di immagine caricati nell'applicazione Web, nonché i file statici da essa utilizzati.
- In [Cosmos DB](/azure/cosmos-db/introduction) vengono archiviati i metadati relativi a ogni immagine caricata, inclusi i risultati dell'elaborazione dell'API Visione artificiale.

## <a name="alternatives"></a>Alternative

- [Servizio visione artificiale personalizzato](/azure/cognitive-services/custom-vision-service/home). L'API Visione artificiale restituisce un set di [categorie basate sulla tassonomia][cv-categories]. Se è necessario elaborare informazioni non restituite dall'API Visione artificiale, prendere in considerazione il Servizio visione artificiale personalizzato, che consente di creare classificatori di immagini personalizzati.
- [Ricerca di Azure](/azure/search/search-what-is-azure-search). Se il caso d'uso include l'esecuzione di query sui metadati per trovare le immagini che soddisfano criteri specifici, valutare la possibilità di usare Ricerca di Azure. La funzionalità di [ricerca cognitiva](/azure/search/cognitive-search-concept-intro), attualmente in anteprima, integra perfettamente questo flusso di lavoro.

## <a name="considerations"></a>Considerazioni

### <a name="scalability"></a>Scalabilità

La maggior parte dei componenti di questo scenario è costituita da servizi gestiti con scalabilità automatica. Esistono un paio di eccezioni importanti: Funzioni di Azure ha un limite massimo di 200 istanze. Se è necessaria una scalabilità superiore, prendere in considerazione più aree o piani app.

Cosmos DB non offre scalabilità automatica in termini di unità richiesta (UR) sottoposte a provisioning. Per indicazioni su come stimare i propri requisiti, vedere l'articolo relativo alle [unità richiesta](/azure/cosmos-db/request-units) nella documentazione. Per sfruttare appieno la scalabilità in Cosmos DB, esaminare il funzionamento delle [chiavi di partizione](/azure/cosmos-db/partition-data) in Cosmos DB.

I database NoSQL spesso privilegiano la disponibilità, la scalabilità e la partizione rispetto alla coerenza (nel senso del teorema CAP). In questo scenario di esempio viene usato un modello di dati chiave-valore e la coerenza delle transazione è raramente necessaria perché le operazioni sono per la maggior parte atomiche per definizione. Materiale sussidiario per [scegliere l'archivio dati corretto](../../guide/technology-choices/data-store-overview.md) sono disponibili in Centro architetture di Azure. Se l'implementazione richiede una coerenza elevata, è possibile [scegliere il livello di coerenza](/azure/cosmos-db/consistency-levels) in Cosmos DB.

Per indicazioni generali sulla progettazione di soluzioni scalabili, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Sicurezza

[Identità gestite per le risorse di Azure][msi] vengono usate per offrire l'accesso ad altre risorse interne all'account e quindi assegnate a Funzioni di Azure. In tali identità consentire l'accesso solo alle risorse necessarie in modo da non esporre nient'altro alle funzioni e potenzialmente ai clienti.

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

Tutti i componenti di questo scenario sono gestiti e quindi automaticamente resilienti a livello di area.

Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].

## <a name="pricing"></a>Prezzi

Per esaminare il costo di esecuzione dello scenario, nel calcolatore dei costi sono preconfigurati tutti i servizi. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base

Sono stati definiti tre profili di costo di esempio in base alla quantità di traffico, supponendo che tutte le immagini siano di 100 KB:

- [Small][small-pricing]: questo esempio di prezzi è correlato all'elaborazione di &lt; 5000 immagini al mese.
- [Medium][medium-pricing]: questo esempio di prezzi è correlato all'elaborazione di 500.000 immagini al mese.
- [Large][large-pricing]: questo esempio di prezzi è correlato all'elaborazione di 50 milioni di immagini al mese.

## <a name="related-resources"></a>Risorse correlate

Per un percorso di apprendimento guidato, vedere [Creare un'app Web serverless in Azure][serverless].

Prima di distribuire questo scenario di esempio in un ambiente di produzione, esaminare le procedure consigliate per l'[ottimizzazione delle prestazioni e dell'affidabilità di Funzioni di Azure][functions-best-practices].

<!-- links -->
[architecture]: ./media/architecture-intelligent-apps-image-processing.png
[small-pricing]: https://azure.com/e/f9b59d238b43423683db73f4a31dc380
[medium-pricing]: https://azure.com/e/7c7fc474db344b87aae93bc29ae27108
[large-pricing]: https://azure.com/e/cbadbca30f8640d6a061f8457a74ba7d
[cognitive-search]: /azure/search/cognitive-search-concept-intro
[serverless]: /azure/functions/tutorial-static-website-serverless-api-with-database
[cv-categories]: /azure/cognitive-services/computer-vision/home#the-86-category-concept
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[functions-best-practices]: /azure/azure-functions/functions-best-practices
[msi]: /azure/app-service/app-service-managed-service-identity
