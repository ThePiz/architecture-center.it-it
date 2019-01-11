---
title: Architettura di riferimento di Azure IoT
description: Architettura consigliata per le applicazioni IoT in Azure che usano componenti PaaS (piattaforma distribuita come servizio)
titleSuffix: Azure Reference Architectures
author: MikeWasson
ms.date: 01/09/2019
ms.openlocfilehash: bde507527262a722219edba534275fb7ab499069
ms.sourcegitcommit: 7d9efe716e8c9e99f3fafa9d0213d48c23d9713d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54166023"
---
# <a name="azure-iot-reference-architecture"></a>Architettura di riferimento di Azure IoT

Questa architettura di riferimento mostra un'architettura consigliata per le applicazioni IoT in Azure che usano componenti PaaS (piattaforma distribuita come servizio).

![Diagramma dell'architettura](./_images/iot.png)

Le applicazioni IoT possono essere descritte come **cose** (dispositivi) che inviano dati che generano **informazioni dettagliate**. Queste informazioni dettagliate generano **azioni** che consentono di migliorare le attività aziendali o i processi. Ad esempio, un motore (la cosa) che invia dati relativi alla temperatura. Questi dati vengono usati per valutare se il motore funziona come previsto (informazione dettagliata). L'informazione dettagliata viene usata per classificare proattivamente in ordine di priorità la pianificazione della manutenzione per il motore (azione).

Questa architettura di riferimento usa componenti PaaS (piattaforma distribuita come servizio) di Azure. Altre opzioni per la creazione di soluzioni IoT in Azure includono:

- [Azure IoT Central](/azure/iot-central/). IoT Central è una soluzione SaaS (software come un servizio) completamente gestita. Estrapola le scelte tecniche per concentrarsi esclusivamente sulla soluzione. Questa semplicità comporta tuttavia un compromesso, in quanto la personalizzazione è ridotta rispetto a una soluzione PaaS.
- Utilizzo di componenti OSS, ad esempio lo stack SMACK (Spark, Mesos, Akka, Cassandra, Kafka), distribuiti in macchine virtuali di Azure. Questo approccio offre molto controllo ma risulta più complesso.

In generale, sono disponibili due modi per elaborare i dati di telemetria, il percorso ad accesso frequente e il percorso ad accesso sporadico. La differenza sta nei requisiti di latenza e accesso ai dati.

- Il **percorso ad accesso frequente** analizza i dati in modalità near real time, appena arrivano. Nel percorso ad accesso frequente, i dati di telemetria devono essere elaborati con una latenza molto bassa. Il percorso ad accesso frequente è in genere implementato tramite un motore di elaborazione dei flussi. L'output può attivare un avviso o essere scritto in un formato strutturato su cui è possibile eseguire query con strumenti di analisi.
- Il **percorso ad accesso sporadico** esegue un'elaborazione batch a intervalli più lunghi (ogni ora o ogni giorno). Il percorso ad accesso sporadico viene in genere usato su volumi elevati di dati, ma i risultati non devono essere generati tempestivamente come con il percorso ad accesso frequente. Nel percorso ad accesso sporadico i dati di telemetria non elaborati vengono acquisiti e quindi inclusi in un processo batch.

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti. Alcune applicazioni potrebbero non richiedere tutti i componenti elencati qui.

**Dispositivi IoT**. I dispositivi possono effettuare la registrazione sicura con il cloud e connettersi al cloud per inviare e ricevere dati. Alcuni dispositivi possono essere **dispositivi perimetrali** che eseguono operazioni di elaborazione dati nel dispositivo stesso o in un gateway sul campo. Per l'elaborazione dei dispositivi perimetrali, è consigliabile usare [Azure IoT Edge](/azure/iot-edge/).

**Gateway cloud**. Un gateway cloud offre un hub cloud che consente ai dispositivi di connettersi in modo sicuro al cloud e inviare dati. Fornisce inoltre funzionalità di gestione dei dispositivi, ad esempio il comando e il controllo dei dispositivi. Per il gateway cloud, è consigliabile usare l'[hub IoT](/azure/iot-hub/). L'hub IoT è un servizio cloud ospitato che inserisce eventi dai dispositivi e che agisce da broker di messaggi tra dispositivi e servizi back-end. L'hub IoT offre connettività sicura, inserimento di eventi, comunicazioni bidirezionali e gestione dei dispositivi.

**Provisioning di dispositivi**. Per la registrazione e la connessione di set di dispositivi di grandi dimensioni, è consigliabile usare il [servizio Device Provisioning in hub IoT](/azure/iot-dps/). Il servizio Device Provisioning in hub IoT consente di assegnare e registrare i dispositivi in endpoint hub IoT di Azure su vasta scala.

**Elaborazione dei flussi**. L'elaborazione dei flussi analizza flussi di record di dati di grandi dimensioni e valuta le regole per tali flussi. Per l'elaborazione dei flussi, è consigliabile usare [Analisi di flusso di Azure](/azure/stream-analytics/). Analisi di flusso di Azure può eseguire analisi complesse su larga scala, usando funzioni per le finestre temporali, aggregazioni di flusso e join di origini dati esterne. Un'altra opzione prevede l'uso di Apache Spark in [Azure Databricks](/azure/azure-databricks/).

L'apprendimento automatico consente di eseguire algoritmi predittivi su dati di telemetria cronologici per abilitare scenari come la manutenzione predittiva. Per l'apprendimento automatico, è consigliabile usare il [servizio Azure Machine Learning](/azure/machine-learning/service/).

L'**archiviazione ad accesso frequente** consente di conservare i dati dei dispositivi che devono essere immediatamente disponibili per la creazione di report e la visualizzazione. Per l'archiviazione ad accesso frequente, è consigliabile usare [Cosmos DB](/azure/cosmos-db/introduction). Cosmos DB è un database multimodello distribuito a livello globale.

L'**archiviazione ad accesso sporadico** viene usata per i dati conservati più a lungo termine e per l'elaborazione batch. Per l'archiviazione ad accesso sporadico, è consigliabile usare l'[Archiviazione BLOB di Azure](/azure/storage/blobs/storage-blobs-introduction). I dati possono essere archiviati nell'archiviazione BLOB a tempo indefinito e a costi ridotti, pur rimanendo facilmente accessibili per l'elaborazione batch.

La **trasformazione dei dati** modifica o aggrega il flusso dei dati di telemetria. Tra gli esempi è inclusa la trasformazione del protocollo, come la conversione dei dati binari nel formato JSON, o la combinazione di punti dati. Se i dati devono essere trasformati prima di raggiungere l'hub IoT, è consigliabile usare un [gateway di protocollo](/azure/iot-hub/iot-hub-protocol-gateway) (non illustrato). In caso contrario, i dati possono essere trasformati dopo aver raggiunto l'hub IoT. In tal caso, è consigliabile usare [Funzioni di Azure](/azure/azure-functions/). Il servizio Funzioni di Azure offre l'integrazione predefinita con l'hub IoT, Cosmos DB e l'archiviazione BLOB.

L'**integrazione dei processi aziendali** esegue azioni in base alle informazioni dettagliate acquisite dai dati dei dispositivi. Ad esempio, l'archiviazione di messaggi informativi, la generazione di avvisi, l'invio di messaggi di posta elettronica o SMS o l'integrazione con un sistema CRM. Per l'integrazione dei processi aziendali, è consigliabile usare le [App per la logica di Azure](/azure/logic-apps/logic-apps-overview).

La **gestione degli utenti** consente di limitare gli utenti o i gruppi che possono eseguire azioni sui dispositivi, ad esempio l'aggiornamento del firmware. Definisce anche le funzionalità disponibili per gli utenti nelle applicazioni. Per autenticare e autorizzare gli utenti è consigliabile usare [Azure Active Directory](/azure/active-directory/).

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

È consigliabile creare un'applicazione IoT come servizi discreti che possono essere ridimensionati in modo indipendente. Tenere presente i punti seguenti sulla scalabilità:

**Hub IoT**. Per l'hub IoT, tenere presente i fattori di scala seguenti:

- La [quota giornaliera](/azure/iot-hub/iot-hub-devguide-quotas-throttling) massima di messaggi nell'hub IoT.
- La quota di dispositivi connessi in un'istanza di Hub IoT.
- Velocità effettiva di inserimento: velocità con cui l'hub IoT può inserire i messaggi.
- Velocità effettiva di elaborazione: velocità con cui vengono elaborati i messaggi in arrivo.

Il provisioning di ogni hub IoT viene eseguito con un determinato numero di unità in un livello specifico. Il livello e il numero di unità determinano la quota giornaliera massima dei messaggi che i dispositivi possono inviare all'hub. Per altre informazioni, vedere Quote e limitazioni dell'hub IoT. È possibile aumentare le prestazioni di un hub senza interrompere le operazioni esistenti.

**Analisi di flusso**. I processi di Analisi di flusso vengono ridimensionati in modo ottimale se sono paralleli in corrispondenza di tutti i punti della pipeline di Analisi di flusso, dall'input, alla query, fino all'output. Un processo completamente parallelo consente ad Analisi di flusso di dividere il lavoro tra più nodi di calcolo. In caso contrario, Analisi di flusso deve combinare i dati del flusso in un'unica posizione. Per altre informazioni, vedere [Sfruttare i vantaggi della parallelizzazione delle query in Analisi di flusso di Azure](/azure/stream-analytics/stream-analytics-parallelization).

L'hub IoT partiziona automaticamente i messaggi dei dispositivi in base all'ID dispositivo. Tutti i messaggi da un dispositivo specifico arriveranno sempre nella stessa partizione, ma una singola partizione conterrà messaggi da più dispositivi. Di conseguenza, l'unità della parallelizzazione è costituita dall'ID partizione.

**Funzioni**. Durante la lettura dall'endpoint Hub eventi, è previsto un limite massimo di istanze di funzione per ogni partizione dell'hub eventi. La velocità di elaborazione massima è determinata dalla velocità con cui un'istanza di funzione può elaborare gli eventi da una singola partizione. La funzione deve elaborare i messaggi in batch.

**Cosmos DB**. Per aumentare il numero di istanze di una raccolta di Cosmos DB, creare la raccolta con una chiave di partizione e includere la chiave di partizione in ogni documento che viene scritto. Per altre informazioni, vedere le [procedure consigliate per la scelta di una chiave di partizione](/azure/cosmos-db/partition-data#best-practices-when-choosing-a-partition-key).

- Se si archivia e aggiorna un singolo documento per dispositivo, l'ID dispositivo costituisce una chiave di partizione ottimale. Le scritture vengono distribuite in modo uniforme tra le chiavi. Le dimensioni di ogni partizione sono rigorosamente vincolate, poiché è presente un singolo documento per ogni valore di chiave.
- Se si archivia un documento separato per ogni messaggio del dispositivo, usando l'ID dispositivo come chiave di partizione si raggiungerebbe rapidamente il limite di 10 GB per partizione. In questo caso, l'ID messaggio rappresenta una chiave di partizione migliore. Normalmente, si includerà comunque l'ID dispositivo nel documento ai fini dell'indicizzazione e dell'esecuzione di query.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

### <a name="trustworthy-and-secure-communication"></a>Comunicazioni affidabili e sicure

Tutte le informazioni ricevute dai dispositivi e inviate ai dispositivi devono essere affidabili. A meno che un dispositivo non supporti le funzionalità di crittografia seguenti, le comunicazioni devono essere limitate alle reti locali e tutte le comunicazioni all'interno della rete devono transitare attraverso un gateway sul campo:

- Crittografia dei dati con un algoritmo di crittografia a chiave simmetrica con sicurezza comprovata, analizzato pubblicamente e implementato su vasta scala.
- Firma digitale con un algoritmo di crittografia a chiave simmetrica con sicurezza comprovata, analizzato pubblicamente e implementato su vasta scala.
- Supporto per i protocolli TLS 1.2 per TCP o altri percorsi di comunicazione basati su flusso o DTLS 1.2 per percorsi di comunicazione basati su diagrammi. Il supporto della gestione dei certificati X.509 è facoltativo e può essere sostituito dalla modalità a chiave precondivisa per TLS più vantaggiosa a livello di calcolo e collegamento, che può essere implementata con il supporto per gli algoritmi AES e SHA-2.
- Archivio chiavi aggiornabile e chiavi per dispositivo. Ogni dispositivo deve disporre di chiavi o token univoci che lo identificano nel sistema. I dispositivi devono archiviare la chiave in modo sicuro nel dispositivo, ad esempio usando un archivio chiavi sicuro. Il dispositivo deve poter aggiornare le chiavi o i token periodicamente o tempestivamente in caso di situazioni di emergenza, come una violazione del sistema.
- Il firmware e il software dell'applicazione nel dispositivo devono consentire gli aggiornamenti per la riparazione delle vulnerabilità della sicurezza individuate.

Tuttavia, molti dispositivi sono eccessivamente limitati per supportare tali requisiti. In questo caso, è necessario usare un gateway sul campo. I dispositivi si connettono in modo sicuro al gateway sul campo tramite una rete locale e il gateway garantisce la comunicazione sicura con il cloud.

### <a name="physical-tamper-proofing"></a>Misure antimanomissione fisiche

La progettazione del dispositivo deve includere funzionalità di protezione dai tentativi di manomissione fisica per garantire l'integrità della sicurezza e l'affidabilità del sistema complessivo.

Ad esempio: 

- Scegliere microcontroller/microprocessori o hardware ausiliario che offre un servizio di archiviazione sicuro e l'uso di chiavi crittografiche, ad esempio l'integrazione di un modulo TPM (Trusted Platform Module).
- Caricatore di avvio sicuro e caricamento di software sicuro, ancorati nel modulo TPM.
- Usare i sensori per rilevare i tentativi di intrusione e i tentativi di manomissione dell'ambiente del dispositivo con avvisi e, potenzialmente, l'"autodistruzione digitale" del dispositivo.

Per altre considerazioni sulla sicurezza, vedere [Architettura della sicurezza di Internet delle cose (IoT)](/azure/iot-fundamentals/iot-security-architecture).

### <a name="monitoring-and-logging"></a>Monitoraggio e registrazione

I sistemi di registrazione e monitoraggio consentono di determinare se la soluzione funziona e per la risoluzione dei problemi. I sistemi di monitoraggio e registrazione permettono di rispondere alle domande operative seguenti:

- I dispositivi o i sistemi si trovano in una condizione di errore?
- I dispositivi o i sistemi sono configurati correttamente?
- I dispositivi o i sistemi generano dati accurati?
- I sistemi soddisfano le aspettative sia dell'azienda sia dei clienti finali?

Gli strumenti di registrazione e monitoraggio includono in genere i quattro componenti seguenti:

- Strumenti di visualizzazione delle prestazioni del sistema e della sequenza temporale per monitorare il sistema e risolvere i problemi di base.
- Inserimento di dati memorizzati nel buffer, per memorizzare nel buffer i dati di log.
- Archivio salvataggi permanenti per l'archiviazione dei dati di log.
- Funzionalità di ricerca e query, per visualizzare i dati di log ai fini della risoluzione dei problemi dettagliata.

I sistemi di monitoraggio forniscono informazioni dettagliate sull'integrità, la sicurezza, la stabilità e le prestazioni di una soluzione IoT. Questi sistemi possono anche offrire una visualizzazione più dettagliata, registrando le modifiche di configurazione dei dispositivi e fornendo dati di registrazione estratti che possono evidenziare potenziali vulnerabilità della sicurezza, ottimizzare il processo di gestione degli eventi imprevisti e consentire al proprietario del sistema di risolvere i problemi. Le soluzioni di monitoraggio complete includono la possibilità di eseguire query sulle informazioni per sottosistemi specifici o per l'aggregazione tra più sottosistemi.

Lo sviluppo del sistema di monitoraggio deve iniziare con la definizione del corretto funzionamento, della conformità alle normative e dei requisiti di controllo. Le metriche raccolte possono includere:

- Dispositivi fisici, dispositivi perimetrali e componenti dell'infrastruttura che segnalano modifiche di configurazione.
- Applicazioni che segnalano modifiche di configurazione, log di controllo della sicurezza, frequenza delle richieste, tempi di risposta, percentuali di errore e statistiche di Garbage Collection per i linguaggi gestiti.
- Database, archivi salvataggi permanenti e cache che segnalano prestazioni di query e scritture, modifiche dello schema, log di controllo della sicurezza, blocchi o deadlock, prestazioni di indicizzazione, utilizzo di CPU, memoria e dischi.
- Servizi gestiti (IaaS, PaaS, SaaS e FaaS) che segnalano le metriche sull'integrità e le modifiche di configurazione che influiscono sull'integrità e le prestazioni dei sistemi dipendenti.

Visualizzazione delle metriche di monitoraggio per avvisare gli operatori circa le instabilità del sistema e agevolare la risoluzione tempestiva degli eventi imprevisti.

### <a name="tracing-telemetry"></a>Traccia dei dati di telemetria

La traccia dei dati di telemetria consente a un operatore di seguire il percorso dei dati di telemetria dalla creazione fino al sistema. La traccia è importante ai fini del debug e della risoluzione dei problemi. Per le soluzioni IoT che usano l'hub IoT di Azure e gli [IoT Hub Device SDK](/azure/iot-hub/iot-hub-devguide-sdks), i datagrammi di traccia possono essere originati come messaggi da cloud a dispositivo e inclusi nel flusso di telemetria.

### <a name="logging"></a>Registrazione

I sistemi di registrazione sono essenziali per comprendere le azioni o le attività eseguite da una soluzione e gli errori che si sono verificati e possono aiutare nella correzione degli errori. È possibile analizzare i log per comprendere e correggere le condizioni di errore, ottimizzare le caratteristiche delle prestazioni e garantire la conformità con le regole e le normative vigenti.

Sebbene la registrazione in testo normale preveda un impatto ridotto sui costi di sviluppo iniziali, la lettura e l'analisi da computer risulta più complessa. È consigliabile usare la registrazione strutturata, in quanto le informazioni raccolte possono essere analizzate e lette sia dal computer che dall'uomo. La registrazione strutturata aggiunge inoltre il contesto della situazione e i metadati alle informazioni di log. Nella registrazione strutturata, le proprietà sono formattate come coppie chiave-valore oppure presentano uno schema fisso per migliorare le funzionalità di ricerca e di query.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni più dettagliate sull'architettura consigliata e sulle scelte di implementazione, vedere il documento [Microsoft Azure IoT Reference Architecture](http://aka.ms/iotrefarchitecture) (Architettura di riferimento di Microsoft Azure IoT) in formato PDF.

- Per la documentazione dettagliata relativa ai servizi disponibili in Azure IoT, vedere [Nozioni fondamentali su Azure IoT](/azure/iot-fundamentals/).
