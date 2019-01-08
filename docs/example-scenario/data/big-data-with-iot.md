---
title: IoT e analisi dei dati nell'industria edilizia
titleSuffix: Azure Example Scenarios
description: Usare dispositivi IoT e analisi dei dati per offrire una gestione e un funzionamento completo dei progetti edilizi.
author: alexbuckgit
ms.date: 08/29/2018
ms.openlocfilehash: 6c997a4f3396fe7ba04f68f8521fd7a006937a27
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643884"
---
# <a name="iot-and-data-analytics-in-the-construction-industry"></a>IoT e analisi dei dati nell'industria edilizia

Questo scenario di esempio è rilevante per le organizzazioni che creano soluzioni per l'integrazione dei dati di più dispositivi IoT in un'architettura di analisi dei dati completa per migliorare e automatizzare i processi decisionali. Le applicazioni potenziali includono soluzioni per l'industria edilizia, mineraria, manifatturiera e altre che usano grandi volumi di dati provenienti da più input di dati basati su IoT.

In questo scenario un produttore di macchinari per l'edilizia costruisce veicoli, contatori e droni che usano le tecnologie IoT e GPS per generare dati di telemetria. L'azienda vuole modernizzare l'architettura dei dati per monitorare meglio l'integrità dei macchinari e le condizioni operative. La sostituzione della soluzione legacy aziendale che usa l'infrastruttura locale sarebbe lunga e laboriosa e non sarebbe possibile eseguire un ridimensionamento sufficiente per gestire il volume di dati previsto.

La società vuole creare una soluzione per l'"edilizia intelligente" basata sul cloud, che dovrà raccogliere un set completo di dati relativi a un cantiere e automatizzare le operazioni e la manutenzione dei vari elementi del sito. Gli obiettivi dell'azienda includono:

- Integrazione e analisi di tutti i macchinari e i dati del cantiere per ridurre al minimo i tempi di inattività dei macchinari e ridurre il rischio di furto.
- Controllo remoto e automatico dei macchinari per attenuare gli effetti di una carenza di manodopera, rendendo sostanzialmente necessario un minor numero di operai e consentendo anche agli operai meno qualificati di lavorare in modo efficiente.
- Riduzione massima dei costi operativi e dei requisiti della manodopera per l'infrastruttura di supporto e aumento della produttività e della sicurezza.
- Facilità di ridimensionamento dell'infrastruttura per supportare quantità più elevate dei dati di telemetria.
- Conformità a tutti i requisiti legali pertinenti tramite il provisioning delle risorse all'interno del paese senza compromettere la disponibilità del sistema.
- Uso di software open source per ottimizzare l'investimento nelle competenze correnti dei lavoratori.

L'uso dei servizi di Azure gestiti, ad esempio l'hub IoT e HDInsight, permetterà al cliente di creare e distribuire rapidamente una soluzione completa con un costo operativo inferiore. Se si hanno altre esigenze di analisi dei dati, è consigliabile vedere l'elenco di [servizi di analisi dei dati completamente gestiti di Azure][product-category] disponibili.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

- Scenari relativi a edilizia, attività mineraria o produzione di macchinari
- Raccolta su larga scala dei dati dei dispositivi per l'archiviazione e l'analisi
- Inserimento e analisi di set di dati di grandi dimensioni

## <a name="architecture"></a>Architettura

![Architettura per IoT e analisi dei dati nell'industria edilizia][architecture]

Il flusso dei dati attraverso la soluzione avviene come segue:

1. I macchinari raccolgono a intervalli regolari i dati dei sensori e inviano i dati dei risultati a servizi Web con carico bilanciato ospitati in un cluster di macchine virtuali di Azure.
2. I servizi Web personalizzati inseriscono i dati dei risultati e li archiviano in un cluster Apache Cassandra anch'esso in esecuzione nelle macchine virtuali di Azure.
3. Un altro set di dati viene raccolto dai sensori IoT di diversi macchinari e inviato all'hub IoT.
4. I dati non elaborati raccolti vengono inviati direttamente dall'hub IoT all'archivio BLOB di Azure e sono immediatamente disponibili per la visualizzazione e l'analisi.
5. I dati raccolti tramite l'hub IoT vengono elaborati quasi in tempo reale da un processo di Analisi di flusso di Azure e archiviati in un database SQL di Azure.
6. L'applicazione Web Smart Construction Cloud consente ad analisti e utenti finali di visualizzare e analizzare i dati dei sensori e le immagini.
7. I processi batch vengono avviati su richiesta degli utenti dell'applicazione Web. Il processo batch viene eseguito in Apache Spark su HDInsight e analizza i nuovi dati archiviati nel cluster Cassandra.

### <a name="components"></a>Componenti

- L'[hub IoT](/azure/iot-hub/about-iot-hub) funge da hub di messaggistica centrale per la comunicazione sicura bidirezionale con identità per dispositivo tra la piattaforma cloud da un lato e i macchinari e gli altri elementi del cantiere dall'altro. L'hub IoT può raccogliere rapidamente i dati per ogni dispositivo per l'inserimento nella pipeline di analisi dei dati.
- [Analisi di flusso di Azure](/azure/stream-analytics/stream-analytics-introduction) è un motore di elaborazione di eventi che può analizzare volumi elevati di dati trasmessi da dispositivi e altre origini dati. Supporta anche l'estrazione di informazioni dai flussi di dati per identificare modelli e relazioni. In questo scenario Analisi di flusso inserisce e analizza i dati dai dispositivi IoT e archivia i risultati nel database SQL di Azure.
- Il [database SQL di Azure](/azure/sql-database/sql-database-technical-overview) contiene i risultati dei dati analizzati dai dispositivi IoT e dai contatori, che possono essere visualizzati da analisti e utenti tramite un'applicazione Web basata su Azure.
- L'[archivio BLOB](/azure/storage/blobs/storage-blobs-introduction) archivia i dati delle immagini raccolti dai dispositivi dell'hub IoT. I dati delle immagini possono essere visualizzati tramite l'applicazione Web.
- [Gestione traffico](/azure/traffic-manager/traffic-manager-overview) consente di controllare la distribuzione del traffico degli utenti per gli endpoint di servizio in diverse aree di Azure.
- [Load Balancer](/azure/load-balancer/load-balancer-overview) distribuisce gli invii di dati dai dispositivi dei macchinari nei servizi Web basati su macchine virtuali per garantire la disponibilità elevata.
- Le [macchine virtuali di Azure](/azure/virtual-machines) ospitano i servizi Web che ricevono e inseriscono i dati dei risultati nel database Apache Cassandra.
- [Apache Cassandra](https://cassandra.apache.org) è un database NoSQL distribuito usato per archiviare i dati dei macchinari che potranno essere elaborati da Apache Spark in un secondo momento.
- [App Web](/azure/app-service/app-service-web-overview) ospita l'applicazione Web per l'utente finale, che può essere usata per le query e la visualizzazione dei dati di origine e delle immagini. Gli utenti possono anche avviare i processi batch in Apache Spark tramite l'applicazione.
- [Apache Spark in HDInsight](/azure/hdinsight/spark/apache-spark-overview) supporta l'elaborazione in memoria per migliorare le prestazioni delle applicazioni di analisi dei Big Data. In questo scenario Spark viene usato per eseguire algoritmi complessi sui dati archiviati in Apache Cassandra.

### <a name="alternatives"></a>Alternative

- [Cosmos DB](/azure/cosmos-db/introduction) è una tecnologia di database NoSQL alternativa. Cosmos DB offre il [supporto multimaster su scala globale](/azure/cosmos-db/multi-region-writers) con [più livelli di coerenza ben definiti](/azure/cosmos-db/consistency-levels) per soddisfare i diversi requisiti dei clienti. Supporta anche l'[API Cassandra](/azure/cosmos-db/cassandra-introduction).
- [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) è una piattaforma di analisi basata su Apache Spark ottimizzata per Azure. È integrata con Azure per consentire la configurazione con un solo clic e offrire flussi di lavoro semplificati e un'area di lavoro collaborativa interattiva.
- [Data Lake Storage](/azure/storage/data-lake-storage) è un'alternativa all'archivio BLOB. Per questo scenario, Data Lake Storage non è disponibile nell'area di destinazione.
- [App Web](/azure/app-service) può essere usato anche per ospitare i servizio Web per l'inserimento dei dati dei risultati dei macchinari.
- Per l'inserimento di messaggi in tempo reale, l'archiviazione dei dati, l'elaborazione dei flussi, l'archiviazione dei dati analitici, l'analisi e la creazione di report sono disponibili molte opzioni tecnologiche. Per una panoramica di queste opzioni, delle relative funzionalità e dei principali criteri di selezione, vedere [Architetture per Big Data Inserimento di messaggi in tempo reale](/azure/architecture/data-guide/technology-choices/real-time-ingestion) in [Guida all'architettura dei dati di Azure](/azure/architecture/data-guide).

## <a name="considerations"></a>Considerazioni

L'ampia disponibilità di aree di Azure è un fattore importante per questo scenario. Avere più di un'area in un singolo paese può garantire il ripristino di emergenza consentendo anche di rispettare la conformità ai requisiti legali e agli obblighi contrattuali. La comunicazione ad alta velocità di Azure tra le aree è un altro fattore importante in questo scenario.

Il supporto di Azure per le tecnologie open source ha consentito al cliente di sfruttare le competenze esistenti della forza lavoro. Rispetto a una soluzione locale, il cliente può anche accelerare l'adozione di nuove tecnologie con costi ridotti e carichi di lavoro operativi.

## <a name="pricing"></a>Prezzi

Le considerazioni seguenti determineranno una parte sostanziale dei costi per questa soluzione.

- I costi delle macchine virtuali di Azure aumenteranno in modo lineare man mano che viene effettuato il provisioning di istanze aggiuntive. Per le macchine virtuali deallocate verranno addebitati solo i costi di archiviazione e non i costi di calcolo. Queste macchine virtuali deallocate potranno essere riallocate quando la richiesta è elevata.
- I costi dell'[hub IoT](https://azure.microsoft.com/pricing/details/iot-hub) dipendono dal numero di unità IoT di cui viene effettuato il provisioning e dal livello di servizio scelto, che determina il numero di messaggi giornalieri consentiti per ogni unità.
- I prezzi per [Analisi di flusso](https://azure.microsoft.com/pricing/details/stream-analytics) dipendono dal numero di unità di streaming necessarie per elaborare i dati nel servizio.

## <a name="related-resources"></a>Risorse correlate

Per vedere un'implementazione di un'architettura simile, leggere il [caso di successo del cliente Komatsu][customer-story].

Per indicazioni sulle architetture per Big Data, vedere [Guida all'architettura dei dati di Azure](/azure/architecture/data-guide).

<!-- links -->

[product-category]: https://azure.microsoft.com/product-categories/analytics/
[customer-site]: https://home.komatsu/en/
[customer-story]: https://customers.microsoft.com/story/komatsu-manufacturing-azure-iot-hub-japan
[architecture]: ./media/architecture-big-data-with-iot.png
