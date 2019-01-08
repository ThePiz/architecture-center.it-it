---
title: Front-end per e-commerce
titleSuffix: Azure Example Scenarios
description: Ospitare un sito di e-commerce in Azure.
author: masonch
ms.date: 7/13/18
ms.custom: fasttrack
ms.openlocfilehash: d6587218813fa450b284f3a300c7254a3c9fe41f
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643952"
---
# <a name="an-e-commerce-front-end-on-azure"></a>Front-end per e-commerce in Azure

Questo scenario di esempio descrive l'implementazione di un front-end di e-commerce usando gli strumenti della piattaforma distribuita come servizio (PaaS) di Azure. Molti siti di e-commerce sono soggetti a stagionalità e variabilità del traffico nel tempo. Quando la richiesta di prodotti o servizi decolla, in modo prevedibile o imprevedibile, l'uso di strumenti PaaS consente di gestire più clienti e più transazioni automaticamente. Questo scenario sfrutta inoltre l'economia del cloud, pagando solo la capacità effettivamente usata.

Questo documento illustra i diversi componenti di Azure PaaS e le considerazioni relative alla distribuzione di un'applicazione di e-commerce di esempio, *Relecloud Concerts*, ovvero una piattaforma online per l'acquisto di biglietti per concerti.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

- Compilazione di un'applicazione che necessita di scalabilità elastica per gestire i picchi di utenti in momenti diversi.
- Compilazione di un'applicazione progettata per funzionare a disponibilità elevata in diverse aree di Azure in tutto il mondo.

## <a name="architecture"></a>Architettura

![Architettura dello scenario di esempio per un'applicazione di e-commerce][architecture]

Questo scenario descrive l'acquisto di biglietti da un sito di e-commerce. Il flusso di dati dello scenario è il seguente:

1. Gestione traffico di Azure indirizza la richiesta di un utente al sito di e-commerce ospitato nel servizio app di Azure.
2. La rete CDN di Azure fornisce immagini e contenuti statici all'utente.
3. L'utente accede all'applicazione tramite un tenant di Azure Active Directory B2C.
4. L'utente cerca i concerti usando la funzione Ricerca di Azure.
5. Il sito Web restituisce i dettagli dei concerti dal database SQL di Azure.
6. Il sito Web fa riferimento alle immagini dei biglietti acquistati presenti in Archiviazione BLOB.
7. I risultati delle query sul database vengono memorizzati nella Cache Redis di Azure per ottenere prestazioni migliori.
8. L'utente invia ordini per biglietti e recensioni sul concerto che vengono inseriti nella coda.
9. Funzioni di Azure elabora il pagamento dell'ordine e le recensioni sul concerto.
10. Servizi cognitivi fornisce un'analisi della recensione del concerto per determinare il sentimento (positivo o negativo).
11. Application Insights fornisce metriche delle prestazioni per il monitoraggio dell'integrità dell'applicazione Web.

### <a name="components"></a>Componenti

- La [rete CDN di Azure][docs-cdn] fornisce contenuti statici memorizzati nella cache da posizioni vicine agli utenti per ridurre la latenza.
- [Gestione traffico di Azure][docs-traffic-manager] consente di controllare la distribuzione del traffico degli utenti per gli endpoint del servizio in diverse aree di Azure.
- Le [app Web del servizio app][docs-webapps] ospitano applicazioni Web con scalabilità automatica e disponibilità elevata senza che sia necessario gestire l'infrastruttura.
- [Azure Active Directory - B2C][docs-b2c] è un servizio di gestione delle identità che consente di personalizzare e controllare il modo in cui i clienti si iscrivono, accedono e gestiscono i profili in un'applicazione.
- Le [code di archiviazione][docs-storage-queues] archiviano un numero elevato di messaggi in coda ai quali è possibile accedere tramite un'applicazione.
- Le [funzioni][docs-functions] sono opzioni di calcolo senza server che consentono l'esecuzione di applicazioni su richiesta senza dover gestire l'infrastruttura.
- [Servizi cognitivi - Analisi del sentiment][docs-sentiment-analysis] usa API di Machine Learning e consente agli sviluppatori di aggiungere facilmente funzionalità intelligenti alle applicazioni, quali il rilevamento di emozioni e video, il riconoscimento facciale, vocale e visivo e la comprensione del parlato e del linguaggio.
- [Ricerca di Azure][docs-search] è una soluzione cloud di ricerca distribuita come servizio che offre un'esperienza di ricerca avanzata su contenuti eterogenei e privati nelle applicazioni Web, per dispositivi mobili e aziendali.
- I [BLOB di archiviazione][docs-storage-blobs] sono ottimizzati per l'archiviazione di grandi quantità di dati non strutturati, ad esempio testo o dati binari.
- La [cache Redis][docs-redis-cache] migliora le prestazioni e la scalabilità dei sistemi che si affidano fortemente agli archivi dati di back-end, copiando temporaneamente i dati a cui si accede di frequente in uno spazio di archiviazione rapida situato vicino all'applicazione.
- Il [database SQL][docs-sql-database] è un servizio gestito di database relazionale per uso generico in Microsoft Azure che supporta strutture come dati relazionali, JSON, dati spaziali e XML.
- [Application Insights][docs-application-insights] è progettato per migliorare continuamente le prestazioni e l'usabilità rilevando automaticamente le anomalie delle prestazioni con strumenti di analisi integrati che consentono di rilevare le operazioni eseguite dagli utenti con un'app.

### <a name="alternatives"></a>Alternative

Sono disponibili molte altre tecnologie per compilare un'applicazione rivolta ai clienti focalizzata sull'e-commerce su larga scala. Queste tecnologie coprono sia il front-end dell'applicazione che il livello dati.

Altre opzioni per il livello Web e le funzioni includono:

- [Service Fabric][docs-service-fabric]: piattaforma incentrata su componenti distribuiti per i quali sono utili la distribuzione e l'esecuzione in un cluster con un livello elevato di controllo. È possibile usare Service Fabric anche per l'hosting di contenitori.
- [Servizio Kubernetes di Azure][docs-kubernetes-service]: piattaforma per la creazione e la distribuzione di soluzioni basate su contenitori che possono essere usate come un'unica implementazione di un'architettura di microservizi. Ciò consente la scalabilità indipendente dei diversi componenti dell'applicazione su richiesta.
- [Istanze di contenitore di Azure][docs-container-instances]: modalità di distribuzione ed esecuzione rapida di contenitori con ciclo di vita breve. Questi contenitori vengono distribuiti per eseguire un processo di elaborazione rapido, come l'elaborazione di un messaggio o l'esecuzione di un calcolo, quindi vengono immediatamente sottoposti a deprovisioning al termine dell'operazione.
- È possibile usare il bus di servizio al posto delle code di archiviazione.

Altre opzioni per il livello dati includono:

- [Cosmos DB](/azure/cosmos-db/introduction): database multimodello di Microsoft distribuito a livello globale. Questo servizio offre una piattaforma per eseguire altri modelli di dati come Mongo DB, Cassandra, dati Graph o un semplice archivio tabelle.

## <a name="considerations"></a>Considerazioni

### <a name="availability"></a>Disponibilità

- Durante la creazione di un'applicazione cloud, valutare la possibilità di sfruttare i [modelli di progettazione tipici per la disponibilità][design-patterns-availability].
- Esaminare le considerazioni sulla disponibilità riportate in relazione all'[architettura di riferimento per applicazioni Web del servizio app][app-service-reference-architecture] appropriata.
- Per altre considerazioni sulla disponibilità, vedere l'[elenco di controllo della disponibilità][availability] in Centro architetture di Azure.

### <a name="scalability"></a>Scalabilità

- Durante la creazione di un'applicazione cloud, tenere presenti i [modelli di progettazione tipici per la scalabilità][design-patterns-scalability].
- Esaminare le considerazioni sulla scalabilità riportate in relazione all'[architettura di riferimento per applicazioni Web del servizio app][app-service-reference-architecture] appropriata.
- Per altri argomenti relativi alla scalabilità, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture di Azure.

### <a name="security"></a>Sicurezza

- Valutare la possibilità di sfruttare i [modelli di progettazione tipici per la sicurezza][design-patterns-security], quando opportuno.
- Esaminare le considerazioni sulla sicurezza riportate in relazione all'[architettura di riferimento per applicazioni Web del servizio app][app-service-reference-architecture] appropriata.
- Valutare la possibilità di seguire un [ciclo di vita di sviluppo sicuro][secure-development] per consentire agli sviluppatori di creare software più sicuro e di soddisfare requisiti di conformità agli standard di sicurezza riducendo al contempo i costi di sviluppo.
- Esaminare l'architettura di progetto per la [conformità PCI DSS di Azure][pci-dss-blueprint].

### <a name="resiliency"></a>Resilienza

- Valutare la possibilità di sfruttare il [modello a interruttore][circuit-breaker] per offrire una gestione degli errori senza interruzioni se una parte dell'applicazione non è disponibile.
- Esaminare i [modelli di progettazione tipici per la resilienza][design-patterns-resiliency] e valutare la possibilità di implementarli, quando opportuno.
- In Centro architetture di Azure sono disponibili diverse [procedure consigliate per il servizio app][resiliency-app-service].
- Valutare la possibilità di usare la [replica geografica attiva][sql-geo-replication] per il livello dati e l'archiviazione [con ridondanza geografica][storage-geo-redudancy] per le immagini e le code.
- Per una discussione più approfondita sulla [resilienza][resiliency], vedere l'articolo corrispondente in Centro architetture di Azure.

## <a name="deploy-the-scenario"></a>Distribuire lo scenario

Per distribuire questo scenario, è possibile seguire l'[esercitazione dettagliata][end-to-end-walkthrough] che illustra come distribuire manualmente ogni componente. Questa esercitazione fornisce anche un'applicazione .NET di esempio che esegue una semplice applicazione per l'acquisto di biglietti. È anche disponibile un modello di Resource Manager per automatizzare la distribuzione della maggior parte delle risorse di Azure.

## <a name="pricing"></a>Prezzi

Esaminare il costo di esecuzione dello scenario. Nel calcolatore dei costi sono preconfigurati tutti i servizi. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base al traffico previsto.

Sono stati definiti tre profili di costo di esempio in base alla quantità di traffico prevista.

- [Small][small-pricing]: questo esempio di prezzi rappresenta i componenti necessari per un'istanza di livello produzione minima. In questo caso si ipotizza un numero limitato di utenti, ovvero solo alcune migliaia al mese. L'app usa una singola istanza di un'app Web standard, sufficiente per abilitare la scalabilità automatica. Ognuno degli altri componenti viene ridimensionato a un livello Basic che consente un costo minimo, garantendo comunque un supporto con contratto di servizio e capacità sufficiente per gestire un carico di lavoro a livello di produzione.
- [Medium][medium-pricing]: questo esempio di prezzi rappresenta i componenti indicativi di una distribuzione di medie dimensioni. In questo caso si stimano circa 100.000 utenti che usano il sistema nel corso di un mese. Il traffico previsto viene gestito in una singola istanza del servizio app con un livello Standard moderato. Al calcolatore vengono anche aggiunti livelli moderati di servizi cognitivi e di ricerca.
- [Large][large-pricing]: questo esempio di prezzi rappresenta un'applicazione su larga scala, nell'ordine di milioni di utenti al mese, con il trasferimento di terabyte di dati. Questo livello di utilizzo richiede app Web di livello Premium con prestazioni elevate, distribuite in più aree e gestite da Gestione traffico. I dati sono costituiti da archivi, database e rete CDN, configurati per terabyte di dati.

## <a name="related-resources"></a>Risorse correlate

- [Architettura di riferimento per applicazioni Web per più aree][multi-region-web-app]
- [Esempio di riferimento eShopOnContainers][microservices-ecommerce]

<!-- links -->
[architecture]: ./media/architecture-ecommerce-scenario.png
[small-pricing]: https://azure.com/e/90fbb6a661a04888a57322985f9b34ac
[medium-pricing]: https://azure.com/e/38d5d387e3234537b6859660db1c9973
[large-pricing]: https://azure.com/e/f07f99b6c3134803a14c9b43fcba3e2f
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[availability]: /azure/architecture/checklist/availability
[circuit-breaker]: /azure/architecture/patterns/circuit-breaker
[design-patterns-availability]: /azure/architecture/patterns/category/availability
[design-patterns-resiliency]: /azure/architecture/patterns/category/resiliency
[design-patterns-scalability]: /azure/architecture/patterns/category/performance-scalability
[design-patterns-security]: /azure/architecture/patterns/category/security
[docs-application-insights]: /azure/application-insights/app-insights-overview
[docs-b2c]: /azure/active-directory-b2c/active-directory-b2c-overview
[docs-cdn]: /azure/cdn/cdn-overview
[docs-container-instances]: /azure/container-instances/
[docs-kubernetes-service]: /azure/aks/
[docs-functions]: /azure/azure-functions/functions-overview
[docs-redis-cache]: /azure/redis-cache/cache-overview
[docs-search]: /azure/search/search-what-is-azure-search
[docs-service-fabric]: /azure/service-fabric/
[docs-sentiment-analysis]: /azure/cognitive-services/welcome
[docs-sql-database]: /azure/sql-database/sql-database-technical-overview
[docs-storage-blobs]: /azure/storage/blobs/storage-blobs-introduction
[docs-storage-queues]: /azure/storage/queues/storage-queues-introduction
[docs-traffic-manager]: /azure/traffic-manager/traffic-manager-overview
[docs-webapps]: /azure/app-service/app-service-web-overview
[end-to-end-walkthrough]: https://github.com/Azure/fta-customerfacingapps/tree/master/ecommerce/articles
[microservices-ecommerce]: https://github.com/dotnet-architecture/eShopOnContainers
[multi-region-web-app]: /azure/architecture/reference-architectures/app-service-web-app/multi-region
[pci-dss-blueprint]: /azure/security/blueprints/payment-processing-blueprint
[resiliency-app-service]: /azure/architecture/checklist/resiliency-per-service#app-service
[resiliency]: /azure/architecture/checklist/resiliency
[scalability]: /azure/architecture/checklist/scalability
[secure-development]: https://www.microsoft.com/SDL/process/design.aspx
[sql-geo-replication]: /azure/sql-database/sql-database-geo-replication-overview
[storage-geo-redudancy]: /azure/storage/common/storage-redundancy-grs
