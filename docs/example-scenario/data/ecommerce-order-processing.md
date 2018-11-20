---
title: Elaborazione degli ordini scalabile in Azure
description: Creare una pipeline di elaborazione degli ordini altamente scalabile con Azure Cosmos DB.
author: alexbuckgit
ms.date: 07/10/2018
ms.openlocfilehash: 1c3bb2cc33be74f5ff8ee0513de4c3f7df70aa37
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610856"
---
# <a name="scalable-order-processing-on-azure"></a>Elaborazione degli ordini scalabile in Azure

Questo scenario di esempio è rivolto alle organizzazioni che necessitano di un'architettura altamente scalabile e resiliente per l'elaborazione degli ordini online. Le potenziali applicazioni includono e-commerce e punti vendita al dettaglio, evasione degli ordini e prenotazione e tracking dell'inventario. 

Questo scenario adotta un approccio di origine eventi, usando un modello di programmazione funzionale implementato tramite microservizi. Ogni microservizio viene gestito come un elaboratore di flusso e tutta la logica di business viene implementata tramite microservizi. Questo approccio consente disponibilità e resilienza elevate, replica geografica e prestazioni rapide.

L'uso di servizi gestiti di Azure come Cosmos DB e HDInsight può contribuire a ridurre i costi, sfruttando l'esperienza di Microsoft nell'archiviazione e nel recupero di dati su scala cloud distribuiti a livello globale. Questo scenario è rivolto specificamente a un contesto di e-commerce o vendita al dettaglio. Se si hanno altre esigenze per i servizi di dati, è necessario esaminare l'elenco dei [servizi di database intelligenti completamente gestiti in Azure][product-category].

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

* Sistemi di back-end per e-commerce o punti vendita al dettaglio.
* Sistemi di gestione dell'inventario.
* Sistemi di evasione degli ordini.
* Altri scenari di integrazione correlati a una pipeline di elaborazione degli ordini.

## <a name="architecture"></a>Architettura

![Architettura di esempio per una pipeline scalabile per l'elaborazione degli ordini][architecture]

Questa architettura indica i componenti chiave di una pipeline per l'elaborazione degli ordini. Il flusso dei dati nello scenario avviene come segue:

1. I messaggi di evento giungono nel sistema tramite applicazioni rivolte ai clienti (in modo sincrono tramite HTTP) e diversi sistemi back-end (in modo asincrono tramite Apache Kafka). Questi messaggi vengono passati a una pipeline di elaborazione dei comandi.
2. Ogni messaggio di evento viene inserito e mappato a un comando di un set definito tramite un microservizio di elaborazione di comandi. L'elaboratore di comando recupera qualsiasi stato corrente relativo all'esecuzione del comando da un database di snapshot del flusso di eventi. Il comando viene quindi eseguito e l'output emesso come nuovo evento.
3. Per ogni evento emesso come output di un comando viene eseguito il commit in un database di flusso di eventi tramite Cosmos DB.
4. Per ogni inserimento o aggiornamento di database con commit nel database di flusso di eventi, viene generato un evento dal feed di modifiche di Cosmos DB. I sistemi a valle possono effettuare la sottoscrizione a qualsiasi argomento di evento pertinente per il sistema.
5. Tutti gli eventi del feed di modifiche di Cosmos DB vengono inviati anche a un microservizio di flusso di eventi snapshot che calcola eventuali cambiamenti di stato causati da eventi. Viene quindi eseguito il commit del nuovo stato nel database di snapshot del flusso di eventi archiviato in Cosmos DB. Il database di snapshot offre un'origine dati distribuita globalmente e a bassa latenza per lo stato corrente di tutti gli elementi dati. Il database del flusso di eventi fornisce una registrazione completa di tutti i messaggi di eventi che sono passati attraverso l'architettura, consentendo scenari solidi di test, risoluzione dei problemi e ripristino di emergenza.

### <a name="components"></a>Componenti

* [Cosmos DB](/azure/cosmos-db/introduction) è il database multimodello di Microsoft, distribuito a livello globale, che consente di ridimensionare in modo flessibile e indipendente la velocità effettiva e le risorse di archiviazione delle soluzioni, in un numero qualsiasi di aree geografiche. Offre garanzie di produttività, latenza, disponibilità e coerenza con contratti di servizio completi. Questo scenario usa Cosmos DB per l'archiviazione di flussi di eventi e l'archiviazione di snapshot e sfrutta le funzioni di [feed di modifiche di Cosmos DB][docs-cosmos-db-change-feed] per garantire la coerenza dei dati e il recupero da errori.
* [Apache Kafka in HDInsight](/azure/hdinsight/kafka/apache-kafka-introduction) è un'implementazione di servizi gestiti di Apache Kafka, una piattaforma di streaming distribuita open-source per la creazione di applicazioni e pipeline di dati in streaming in tempo reale. Kafka offre anche una funzionalità di broker di messaggi simile a una coda di messaggi, in cui è possibile pubblicare e sottoscrivere flussi dei dati denominati. Questo scenario usa Kafka per l'elaborazione di eventi in ingresso e downstream nella pipeline di elaborazione degli ordini. 

## <a name="considerations"></a>Considerazioni

Per l'inserimento di messaggi in tempo reale, l'archiviazione dei dati, l'elaborazione dei flussi, l'archiviazione dei dati analitici, l'analisi e la creazione di report sono disponibili molte opzioni tecnologiche. Per una panoramica di queste opzioni, delle relative funzionalità e dei principali criteri di selezione, vedere l'articolo su [architetture per Big Data ed elaborazione in tempo reale](/azure/architecture/data-guide/technology-choices/real-time-ingestion) in [Guida all'architettura dei dati di Azure](/azure/architecture/data-guide).

I microservizi sono diventati uno stile di architettura diffuso per la creazione di applicazioni cloud che offrono resilienza, scalabilità elevata, possibilità di distribuzione indipendente e capacità di evolversi rapidamente. I microservizi richiedono un approccio diverso alla progettazione e alla creazione di applicazioni. Strumenti come Docker, Kubernetes, Azure Service Fabric e Nomad consentono lo sviluppo di architetture basate su microservizi. Per informazioni sulla creazione e l'esecuzione di un'architettura basata su microservizi, vedere [Progettazione di microservizi in Azure](/azure/architecture/microservices in Centro architetture di Azure.

### <a name="availability"></a>Disponibilità

L'approccio di origine eventi di questo scenario consente di associare e distribuire i componenti di sistema in modo flessibile e indipendente l'uno dall'altro. Cosmos DB offre [disponibilità elevata][docs-cosmos-db-regional-failover] e consente all'organizzazione di gestire i compromessi associati a coerenza, disponibilità e prestazioni, il tutto con [garanzie corrispondenti][docs-cosmos-db-guarantees]. Anche Apache Kafka in HDInsight è progettato per la [disponibilità elevata][docs-kafka-high-availability].

Monitoraggio di Azure offre interfacce utente unificate per il monitoraggio di diversi servizi di Azure. Per altre informazioni, vedere l'articolo relativo al [monitoraggio in Microsoft Azure](/azure/monitoring-and-diagnostics/monitoring-overview). Sia Hub eventi che Analisi di flusso sono integrati in Monitoraggio di Azure. 

Per altre considerazioni sulla disponibilità, vedere l'[elenco di controllo della disponibilità][availability].

### <a name="scalability"></a>Scalabilità

Kafka in HDInsight consente la [configurazione dell'archiviazione e della scalabilità](/azure/hdinsight/kafka/apache-kafka-scalability) per i cluster Kafka. Cosmos DB offre prestazioni rapide e prevedibili e può essere[ridimensionato agevolmente](/azure/cosmos-db/partition-data) man mano che l'applicazione cresce.
L'architettura di origine eventi basata su microservizi di questo scenario semplifica anche il ridimensionamento del sistema e l'espansione delle sue funzionalità.

Per altre considerazioni sulla scalabilità, vedere l'[elenco di controllo della scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Sicurezza

Il [modello di sicurezza di Cosmos DB](/azure/cosmos-db/secure-access-to-data) autentica gli utenti e fornisce accesso ai dati e alle risorse. Per altre informazioni, vedere [Sicurezza database di Azure Cosmos DB](/azure/cosmos-db/database-security).

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

L'architettura di origine eventi e le tecnologie associate in questo scenario di esempio rendono lo scenario stesso altamente resiliente in caso di errori. Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].

## <a name="pricing"></a>Prezzi

Per esaminare il costo di esecuzione dello scenario, nel calcolatore dei costi sono preconfigurati tutti i servizi. Per verificare la variazione dei prezzi per un determinato scenario, modificare le variabili appropriate in base al volume di dati previsto. In questo scenario, il prezzo di esempio include solo Cosmos DB e un cluster Kafka per l'elaborazione degli eventi generati dal feed di modifiche di Cosmos DB. Gli elaboratori di eventi e i microservizi per i sistemi di origine e altri sistemi a valle non sono inclusi e il loro costo dipende in larga misura dalla quantità e dalle dimensioni di tali servizi, nonché dalle tecnologie scelte per la loro implementazione.

La valuta di Azure Cosmos DB è l'unità richiesta (UR). Con le unità richiesta, non è necessario riservare capacità di lettura/scrittura né effettuare il provisioning di CPU, memoria e operazioni di I/O al secondo. Azure Cosmos DB supporta varie API che dispongono di operazioni diverse, che vanno dalla semplice lettura e scrittura alle query per grafi più complesse. Poiché non tutte le richieste sono uguali, viene loro assegnata una quantità normalizzata di unità richiesta in base alla quantità di calcolo necessaria per servire la richiesta. Il numero di unità richiesta necessarie per la soluzione dipende dalle dimensioni degli elementi dati e dal numero di operazioni di lettura e scrittura del database al secondo. Per altre informazioni, vedere [Unità richiesta in Azure Cosmos DB](/azure/cosmos-db/request-units). Questi prezzi stimati si basano sull'esecuzione di Cosmos DB in due aree di Azure.

Sono stati definiti tre profili di costo di esempio in base alla quantità di attività prevista.

* [Small][small-pricing]: questo esempio di prezzi corrisponde a 5 unità richiesta riservate con un archivio dati di 1 TB in Cosmos DB e un piccolo cluster Kafka (D3 v2).
* [Medium][medium-pricing]: questo esempio di prezzi corrisponde a 50 unità richiesta riservate con un archivio dati di 10 TB in Cosmos DB e un cluster Kafka di medie dimensioni (D4 v2).
* [Large][large-pricing]: questo esempio di prezzi corrisponde a 500 unità richiesta riservate con un archivio dati di 30 TB in Cosmos DB e un cluster Kafka di grandi dimensioni (D5 v2).

## <a name="related-resources"></a>Risorse correlate

Questo scenario di esempio si basa su una versione più estesa di questa architettura, creata da [Jet.com](https://jet.com) per la propria pipeline di elaborazione degli ordini end-to-end. Per altre informazioni, vedere il [profilo tecnico del cliente jet.com][source-document] e la [presentazione di jet.com a Build 2018][source-presentation].

Altre risorse correlate includono:
* _[Designing Data-Intensive Applications](https://dataintensive.net)_ di Martin Kleppmann (O'Reilly Media, 2017).
* _[Domain Modeling Made Functional: Tackle Software Complexity with Domain-Driven Design and F#](https://pragprog.com/book/swdddf/domain-modeling-made-functional)_ di Scott Wlaschin (Pragmatic Programmers LLC, 2018).
* Alti [casi d'uso di Cosmos DB][docs-cosmos-db-use-cases]
* [Architettura di elaborazione in tempo reale](/azure/architecture/data-guide/big-data/real-time-processing) nella [Guida all'architettura dei dati di Azure](/azure/architecture/data-guide).

<!-- links -->
[architecture]: ./media/architecture-ecommerce-order-processing.png
[product-category]: https://azure.microsoft.com/product-categories/databases/
[source-document]: https://customers.microsoft.com/story/jet-com-powers-innovative-e-commerce-engine-on-azure-in-less-than-12-months
[source-presentation]: https://channel9.msdn.com/events/Build/2018/BRK3602
[small-pricing]: https://azure.com/e/3d43949ffbb945a88cc0a126dc3a0e6e
[medium-pricing]: https://azure.com/e/1f1e7bf2a6ad4f7799581211f4369b9b
[large-pricing]: https://azure.com/e/75207172ece94cf6b5fb354a2252b333
[docs-cosmos-db-change-feed]: /azure/cosmos-db/change-feed
[docs-cosmos-db-regional-failover]: /azure/cosmos-db/regional-failover
[docs-cosmos-db-guarantees]: /azure/cosmos-db/distribute-data-globally#AvailabilityGuarantees
[docs-cosmos-db-use-cases]: /azure/cosmos-db/use-cases
[docs-kafka-high-availability]: /azure/hdinsight/kafka/apache-kafka-high-availability
[docs-event-hubs]: /azure/event-hubs/event-hubs-what-is-event-hubs
[docs-stream-analytics]: /azure/stream-analytics/stream-analytics-introduction
[availability]: /azure/architecture/checklist/availability
[scalability]: /azure/architecture/checklist/scalability
[resiliency]: /azure/architecture/patterns/category/resiliency/
[security]: /azure/security/
