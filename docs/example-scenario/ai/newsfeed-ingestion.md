---
title: Inserimento e analisi di massa di feed di notizie in Azure
description: Creare una pipeline per l'inserimento e l'analisi di testo, immagini, sentiment e altri dati dei feed di notizie RSS usando solo i servizi Azure, tra cui Azure Cosmos DB e Servizi cognitivi di Azure.
author: njray
ms.date: 2/1/2019
ms.custom: azcat-ai, AI
social_image_url: /azure/architecture/example-scenario/ai/media/mass-ingestion-newsfeeds-architecture.png
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.openlocfilehash: e1bc636103753d474b545a6e3e9118b2ef302b34
ms.sourcegitcommit: ea97ac004c38c6b456794c1a8eef29f8d2b77d50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58489269"
---
# <a name="mass-ingestion-and-analysis-of-news-feeds-on-azure"></a>Inserimento e analisi di massa di feed di notizie in Azure

Questo scenario di esempio descrive una pipeline per l'inserimento di massa e quasi in tempo reale analisi di documenti usando i feed di notizie RSS pubblici.  Usa servizi cognitivi di Azure per offrire informazioni dettagliate utili tra cui rilevamento dei sentiment, riconoscimento facciale e traduzione testuale.

Questo scenario è disponibili esempi per [inglese][english], [russo][russian], e [tedesco] [ german] notizie su feed, ma è possibile estenderlo facilmente da altri feed RSS. Per semplificare lo sviluppo, la raccolta dei dati, elaborazione e analisi sono basati interamente su servizi di Azure.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Sebbene questo scenario si basa sull'elaborazione del feed RSS, è rilevante per qualsiasi documento, un sito Web o un articolo in cui è necessario:

* Tradurre un testo per il linguaggio preferito.
* Trovare frasi chiave, entità e sentiment utente nel contenuto digitale.
* Rilevare gli oggetti, il testo e punti di riferimento in immagini associate a un articolo digitale.
* Rilevare le persone da loro genere ed età di qualsiasi immagine associato al contenuto digitale.

## <a name="architecture"></a>Architettura

![][architecture]

Il flusso dei dati attraverso la soluzione avviene come segue:

1. Un feed di notizie RSS agisce come il generatore che ottiene dati da un documento o un articolo. Ad esempio, con un articolo, dati includono in genere un titolo, un riepilogo del corpo originale dell'elemento di notizie e talvolta di immagini.

2. Un generatore o l'inserimento di un processo inserisce l'articolo e tutte le immagini associate in un Azure Cosmos DB [Collection][collection].

3. Una notifica attiva una funzione di inserimento in funzioni di Azure che archivia il testo dell'articolo in COSMOS DB e le immagini di articolo (se presente) in archiviazione Blob di Azure.  L'articolo viene quindi passato alla coda successiva.

4. Una funzione translate viene attivata dall'evento della coda. Usa il [tradurre API traduzione testuale] [ translate-text] di servizi cognitivi di Azure per rilevare la lingua, tradurre se necessario e raccogliere i sentimenti, frasi chiave e le entità dal corpo e il titolo. Passa quindi l'articolo alla coda successiva.

5. Una funzione di rilevamento viene attivata dall'articolo in coda. Usa il [visione artificiale] [ vision] servizio per rilevare gli oggetti, riferimenti e parole scritte nell'immagine associato, quindi passa l'articolo alla coda successiva.

6. Viene attivata una funzione di volti viene attivato dall'articolo in coda. Usa il [Azure l'API viso] [ face] servizio per rilevare i volti per sesso ed età nell'immagine associato, quindi passa l'articolo alla coda successiva.

7. Dopo aver complete tutte le funzioni, la funzione di cui inviare la notifica viene attivata. Carica il record elaborati per l'articolo e li esegue l'analisi dei risultati desiderati. Se viene trovato, viene contrassegnato il contenuto e viene inviata una notifica al sistema di propria scelta.

A ogni passaggio di elaborazione, la funzione scrive i risultati in Azure Cosmos DB. In definitiva, i dati possono essere usati come desiderato. Ad esempio, è possibile usarlo per migliorare i processi di business, individuare i nuovi clienti o identificare i problemi di soddisfazione dei clienti.

### <a name="components"></a>Componenti

In questo esempio viene usato il seguente elenco di componenti di Azure.

* [Archiviazione di Azure] [ storage] viene utilizzato per i file video associati a un articolo e l'immagine non elaborata. Un account di archiviazione secondario viene creato con il servizio App di Azure e viene usato per ospitare il codice della funzione di Azure e log.

* [Azure Cosmos DB] [ cosmos-db] l'articolo contiene testo, immagini e video, informazioni di rilevamento. I risultati delle operazioni di servizi cognitivi vengono anche archiviati qui.

* [Funzioni di Azure] [ functions] esegue il codice della funzione usato per rispondere ai messaggi in coda e trasformare il contenuto in ingresso. [Servizio App di Azure] [ aas] ospita il codice della funzione ed elabora i record in modo seriale. Questo scenario include cinque funzioni: Inserire, trasformare, oggetto, viso, di rilevare e inviare una notifica.

* [Il Bus di servizio di Azure] [ service-bus] ospita le code del Bus di servizio di Azure usate dalle funzioni.

* [Servizi cognitivi di Azure] [ acs] offre l'intelligenza artificiale per la pipeline in base alle implementazioni del [visione artificiale] [ vision] servizio [viso API][face], e [Traduci testo] [ translate-text] servizio di traduzione.

* [Azure Application Insights] [ aai] fornisce analitica che consentono di diagnosticare i problemi e comprendere la funzionalità dell'applicazione.

### <a name="alternatives"></a>Alternative

* Invece di usare un modello basato su notifica della coda e funzioni di Azure, usare un altro modello per questo flusso di dati. Ad esempio, [gli argomenti del Bus di servizio di Azure] [ topics] può essere utilizzato per le varie parti dell'articolo in parallelo anziché l'elaborazione seriale eseguito in questo esempio i processi. Per altre informazioni, confrontare [code e argomenti][queues-topics].

* Uso [App per la logica di Azure] [ logic-app] per implementare il codice della funzione e implementare, ad esempio il blocco a livello di record [Redlock] [ redlock] (necessario per paralleli l'elaborazione fino al Azure Cosmos DB supporta [parziale di documenti sono stati aggiornati][partial]). Per altre informazioni, [confrontare funzioni e App per la logica][compare].

* Implementare questa architettura usando i componenti di intelligenza artificiale personalizzati anziché limitarsi a servizi di Azure. Ad esempio, estendere la pipeline usando un modello personalizzato che consente di rilevare determinate persone in un'immagine invece il numero di persone generico, sesso e i dati raccolti in questo esempio l'età. Per usare apprendimento automatico personalizzati o modelli di intelligenza artificiale con questa architettura, compilare i modelli come endpoint RESTful in modo che possono essere chiamati da funzioni di Azure.

* Utilizzare un diverso meccanismo di input anziché feed RSS. Usare più generatori o i processi di inserimento di feed di Azure Cosmos DB e archiviazione di Azure.

## <a name="considerations"></a>Considerazioni

Per semplicità, questo scenario di esempio Usa solo alcune API disponibili e i servizi da servizi cognitivi di Azure. Ad esempio, testo nelle immagini può essere analizzato con il [API traduzione testuale Analitica][text-analytics]. Lingua di destinazione in questo scenario si presuppone che sia in lingua inglese, ma è possibile modificare l'input a uno [lingua supportata] [ language] di propria scelta.

### <a name="scalability"></a>Scalabilità

Funzioni di Azure la scalabilità dipende il [piano di hosting] [ plan] è utilizzare. Questa soluzione presuppone una [piano a consumo][plan-c], in cui calcolo power viene allocato automaticamente per le funzioni quando necessario. Si paga solo quando le funzioni sono in esecuzione. Un'altra opzione consiste nell'usare un [servizio App di Azure] [ plan-aas] piano, che consente di applicare la scalabilità tra livelli per allocare una quantità di risorse diversa.

Con Azure Cosmos DB, la chiave consiste nel distribuire il carico di lavoro più o meno in modo uniforme tra un numero sufficientemente elevato di [chiavi di partizione][keys]. Non esiste un limite alla quantità totale di dati che può archiviare un contenitore o alla quantità totale di [velocità effettiva] [ throughput] in grado di supportare un contenitore.

### <a name="management-and-logging"></a>Gestione e registrazione

Questa soluzione Usa [Application Insights] [ aai] per raccogliere informazioni su prestazioni e la registrazione. Con la distribuzione nello stesso gruppo di risorse come gli altri servizi necessari per questa distribuzione viene creata un'istanza di Application Insights.

Per visualizzare i log generati dalla soluzione:

1. Passare a [portale di Azure] [ portal] e passare al gruppo di risorse creato per la distribuzione.

2. Scegliere il **Application Insights** istanza.

3. Dal **Application Insights** sezione, passare alla **indagare\\ricerca** e cercare i dati.

### <a name="security"></a>Security

Azure Cosmos DB Usa una connessione protetta e una firma di accesso condiviso tramite C\# SDK forniti da Microsoft. Sono disponibili altre aree area accessibile pubblicamente. Altre informazioni sulla sicurezza [procedure consigliate] [ db-practices] per Azure Cosmos DB.

## <a name="pricing"></a>Prezzi

Il costo stimato giornaliero per mantenere questa distribuzione disponibile è di circa \$20 degli Stati Uniti senza dati passano attraverso il sistema.

Azure Cosmos DB è potente, ma comporta il maggior [costo] [ db-cost] in questa distribuzione. È possibile usare un'altra soluzione di archiviazione per il refactoring del codice di funzioni di Azure fornito.

Prezzi per funzioni di Azure varia a seconda del [piano] [ function-plan] è in esecuzione in.

## <a name="deploy-the-scenario"></a>Distribuire lo scenario

> [!NOTE]
> È necessario un account Azure esistente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free] prima di iniziare.

È disponibile in tutto il codice per questo scenario il [GitHub] [ github] repository. Questo repository contiene il codice sorgente utilizzato per compilare l'applicazione di generazione che la pipeline per questa demo di feed.

[architecture]: ./media/mass-ingestion-newsfeeds-architecture.png
[aai]: /azure/azure-monitor/app/app-insights-overview
[aas]: https://azure.microsoft.com/try/app-service/
[acs]: https://azure.microsoft.com/services/cognitive-services/directory/
[collection]: /rest/api/cosmos-db/collections
[compare]: /azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs#compare-azure-functions-and-azure-logic-apps
[cosmos-db]: /azure/cosmos-db/introduction
[db-cost]: https://azure.microsoft.com/pricing/details/cosmos-db/
[db-practices]: /azure/cosmos-db/database-security
[db-collection]: /azure/cosmos-db/databases-containers-items
[english]: https://www.nasa.gov/rss/dyn/breaking_news.rss
[face]: /azure/cognitive-services/face/overview
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[functions]: /azure/azure-functions/functions-overview
[function-plan]: /azure/azure-functions/functions-scale
[german]: http://www.bamf.de/SiteGlobals/Functions/RSS/DE/Feed/RSSNewsfeed_Meldungen
[github]: https://github.com/Azure/cognitive-services
[keys]: /azure/cosmos-db/partition-data
[language]: /azure/cognitive-services/translator/reference/v3-0-languages
[logic-app]: /azure/logic-apps/logic-apps-overview
[queues-topics]: /azure/service-bus-messaging/service-bus-queues-topics-subscriptions
[partial]: https://feedback.azure.com/forums/263030-azure-cosmos-db/suggestions/6693091-be-able-to-do-partial-updates-on-document
[plan]: /azure/azure-functions/functions-scale
[plan-aas]: /azure/azure-functions/functions-scale#app-service-plan
[plan-c]: /azure/azure-functions/functions-scale#consumption-plan
[portal]: http://portal.azure.com
[redlock]: https://redis.io/topics/distlock
[russian]: http://government.ru/all/rss/
[service-bus]: /azure/service-bus-messaging/
[storage]: /azure/storage/common/storage-account-overview 
[throughput]: /azure/cosmos-db/scaling-throughput
[topics]: /azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions
[text-analytics]: /azure/cognitive-services/text-analytics/
[translate-text]: /azure/cognitive-services/translator/translator-info-overview
[vision]: /azure/cognitive-services/computer-vision/home
