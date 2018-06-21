---
title: Data warehousing e data mart
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 9b90d77ce1a81cd4a7532f5d4230ada8b4991d13
ms.sourcegitcommit: 85334ab0ccb072dac80de78aa82bcfa0f0044d3f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35252806"
---
# <a name="data-warehousing-and-data-marts"></a>Data warehousing e data mart

Un data warehouse è un repository relazionale centrale con funzione organizzativa contenente i dati integrati da una o più origini diverse in molte o in tutte le aree di interesse. I data warehouse archiviano i dati correnti e quelli cronologici e vengono usati per scopi di report e analisi dei dati in diversi modi.

![Data warehousing in Azure](../images/data-warehousing.png)

I dati da trasferire in un data warehouse vengono estratti con cadenza periodica da varie origini che contengono informazioni aziendali importanti. Quando vengono trasferiti, i dati possono essere formattati, puliti, convalidati, riassunti e riorganizzati. In alternativa, possono essere archiviati nel livello di dettaglio più basso, con visualizzazioni aggregate fornite nel warehouse per scopi di report. In entrambi i casi, il data warehouse diventa uno spazio di archiviazione permanente per i dati usati per la creazione di report, l'analisi e l'adozione di importanti decisioni aziendali con l'ausilio di strumenti di business intelligence (BI).

## <a name="data-marts-and-operational-data-stores"></a>Data mart e archivi dati operativi

La gestione dei dati su larga scala è complessa ed è sempre più raro trovare un unico data warehouse che rappresenta i dati dell'intera azienda. Al contrario, le organizzazioni tendono a creare data warehouse più piccoli e più mirati, denominati *data mart*, che espongono i dati desiderati per scopi di analisi. Grazie a un processo di orchestrazione, i data mart vengono popolati con i dati mantenuti in un archivio dati operativo. L'archivio dati operativi agisce da intermediario tra il sistema transazionale di origine e il data mart. I dati gestiti dall'archivio dati operativi sono una versione pulita dei dati presenti nel sistema transazionale di origine. Si tratta in genere di un subset dei dati cronologici manutenuti dal data warehouse o dal data mart. 

## <a name="when-to-use-this-solution"></a>Quando usare questa soluzione

Scegliere un data warehouse quando è necessario convertire le enormi quantità di dati dei sistemi operativi in un formato facilmente comprensibile, aggiornato e preciso. I data warehouse non devono necessariamente riflettere la stessa struttura dati concisa che può essere usata nei database operativi/OLTP. È possibile usare nomi di colonna significativi per gli analisti e gli utenti aziendali, ristrutturare lo schema per semplificare le relazioni tra i dati e consolidare più tabelle in una. Questi passaggi forniscono agli utenti le indicazioni necessarie per creare report ad hoc o per creare report e analizzare i dati nei sistemi di business intelligence, senza il supporto di un amministratore del database (DBA) o uno sviluppatore di dati.

Può essere opportuno usare un data warehouse quando è necessario tenere separati i dati cronologici dai sistemi transazionali di origine per non compromettere le prestazioni. I data warehouse facilitano l'accesso ai dati cronologici da varie posizioni, offrendo un'area centralizzata con formati, chiavi, modelli di dati e metodi di accesso comuni.

I data warehouse sono ottimizzati per l'accesso in lettura e quindi consentono una generazione più rapida dei report rispetto all'esecuzione dei report a fronte del sistema transazionale di origine. Inoltre, i data warehouse offrono i vantaggi seguenti:

* Tutti i dati cronologici estratti da più origini possono essere archiviati e visualizzati da un data warehouse come SSOT (Single Source of Truth).
* È possibile migliorare la qualità dei dati eseguendo una pulizia durante l'importazione nel data warehouse, fornendo dati più precisi e assegnando codici e descrizioni coerenti.
* Gli strumenti di report e i sistemi transazionali di origine non sono in concorrenza per i cicli di elaborazione delle query. Un data warehouse consente al sistema transazionale di concentrarsi prevalentemente sulla gestione delle operazioni di scrittura, mentre il data warehouse soddisfa la maggior parte delle richieste di lettura.
* Un data warehouse consente di consolidare i dati di applicazioni software diverse.
* Gli strumenti di data mining consentono di trovare modelli nascosti usando metodologie automatiche sui dati archiviati nel warehouse.
* I data warehouse consentono di fornire l'accesso sicuro agli utenti autorizzati, limitando al tempo stesso l'accesso agli altri utenti. Non è necessario consentire l'accesso degli utenti aziendali ai dati di origine. In tal modo, si elimina un potenziale vettore di attacco contro uno o più sistemi transazionali di produzione.
* I data warehouse facilitano la creazione di soluzioni di business intelligence basate sui dati, ad esempio i [cubi OLAP](online-analytical-processing.md).

## <a name="challenges"></a>Problematiche

La corretta configurazione di un data warehouse per soddisfare le esigenze aziendali presenta alcune complessità, tra cui:

* Dedicare il tempo necessario alla corretta modellazione dei concetti aziendali. Questo passaggio è importante perché i data warehouse sono basati su informazioni e il mapping dei concetti dà impulso al resto del progetto. Tale mapping include la standardizzazione di termini aziendali e formati comuni (ad esempio date e valute) e la ristrutturazione dello schema in modo che risulti significativo per gli utenti aziendali ma anche in grado di assicurare la precisione delle relazioni e delle aggregazioni di dati.
* Pianificazione e configurazione dell'orchestrazione dei dati. Prendere in considerazione come copiare i dati dal sistema transazionale di origine nel data warehouse e quando spostare i dati cronologici dagli archivi dati operativi nel warehouse.
* Mantenimento o miglioramento della qualità dei dati attraverso la pulizia dei dati in fase di importazione nel warehouse.

## <a name="data-warehousing-in-azure"></a>Data warehousing in Azure

In Azure possono essere presenti una o più origini di dati, derivanti da transazioni cliente o da varie applicazioni aziendali usate da diversi reparti. Questi dati vengono in genere archiviati in uno o più database [OLTP](online-transaction-processing.md). I dati possono essere persistenti in altri supporti di archiviazione, ad esempio condivisioni di rete, BLOB di archiviazione di Azure o un servizio Data Lake. I dati possono anche essere archiviati dal data warehouse stesso o in un database relazionale, ad esempio il database SQL di Azure. Lo scopo del livello dell'archivio dati analitici è quello di rispondere alle query inviate dagli strumenti di analisi e report a fronte del data warehouse o del data mart. In Azure questa capacità di archiviazione analitica può essere assicurata da Azure SQL Data Warehouse o da Azure HDInsight con Hive o Interactive Query. Inoltre, sarà necessario un determinato livello di orchestrazione per trasferire o copiare periodicamente i dati dalla risorsa di archiviazione dati nel data warehouse. Questa operazione può essere eseguita con Azure Data Factory o con Oozie in Azure HDInsight.

Sono disponibili diverse opzioni per l'implementazione di un data warehouse in Azure, in base alle esigenze specifiche. Gli elenchi seguenti sono suddivisi in due categorie: [multielaborazione simmetrica](https://en.wikipedia.org/wiki/Symmetric_multiprocessing) (SMP, Symmetric Multiprocessing) e [elaborazione parallela elevata](https://en.wikipedia.org/wiki/Massively_parallel) (MPP, Massively Parallel Processing). 

SMP:

- [Database SQL di Azure](/azure/sql-database/)
- [SQL Server in una macchina virtuale](/sql/sql-server/sql-server-technical-documentation)

MPP:

- [Data Warehouse di Azure](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
- [Apache Hive in HDInsight](/azure/hdinsight/hadoop/hdinsight-use-hive)
- [Interactive Query (Hive LLAP) in HDInsight](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)

In generale, i data warehouse basati su SMP sono più adatti per i set di dati di piccole e medie dimensioni (fino a 4-100 TB), mentre quelli basati su MPP vengono spesso usati per i Big Data. Il limite tra set di dati di piccole e medie dimensioni e Big Data dipende in parte dalla definizione dell'organizzazione e dall'infrastruttura di supporto. Per informazioni, vedere [Scelta di un archivio dati OLTP](online-transaction-processing.md#scalability-capabilities). 

Oltre alle dimensioni dei dati, il tipo di modello di carico di lavoro è probabilmente un fattore ancor più determinante. Ad esempio, le query complesse potrebbero essere troppo lente per una soluzione SMP e richiedere invece una soluzione MPP. Con i set di dati di piccole dimensioni è probabile che i sistemi basati su MPP impongano una riduzione delle prestazioni dovuta al modo in cui i processi vengono distribuiti e consolidati nei nodi. Se le dimensioni dei dati superano già 1 TB e si prevede che continuino a crescere, può essere opportuno scegliere una soluzione MPP. Tuttavia, se le dimensioni dei dati sono inferiori ma i carichi di lavoro superano le risorse disponibili della soluzione SMP, una soluzione MPP potrebbe essere l'opzione ottimale anche in questo caso.

I dati usati o archiviati dal data warehouse possono provenire da svariate origini dati, ad esempio da [Azure Data Lake Store](/azure/data-lake-store/). Per una sessione video di confronto tra i diversi punti di forza dei servizi MPP che possono usare Azure Data Lake, vedere [Azure Data Lake and Azure Data Warehouse: Applying Modern Practices to Your App](https://azure.microsoft.com/resources/videos/build-2016-azure-data-lake-and-azure-data-warehouse-applying-modern-practices-to-your-app/) (Azure Data Lake e Azure Data Warehouse: applicazione di procedure moderne all'app).

I sistemi SMP sono caratterizzati da una singola istanza di un sistema di gestione di database relazionali che condivide tutte le risorse (CPU/memoria/disco). È possibile aumentare le prestazioni di un sistema SMP. Per SQL Server in esecuzione in una macchina virtuale è possibile aumentare le dimensioni della macchina virtuale. Per il database SQL di Azure è possibile aumentare le prestazioni scegliendo un livello di servizio diverso. 

È possibile aumentare il numero di istanze dei sistemi MPP aggiungendo altri nodi di calcolo, con i propri sottosistemi I/O, CPU e memoria. L'aumento delle prestazioni di un server presenta limiti fisici, oltre i quali è preferibile aumentare il numero di istanze, a seconda del carico di lavoro. Tuttavia, le soluzioni MPP richiedono un set di competenze diverso a causa delle varianze nell'esecuzione di query, nella modellazione e nel partizionamento dei dati, nonché per altri motivi specifici dell'elaborazione parallela. 

Per decidere quale soluzione SMP usare, vedere [Informazioni dettagliate sul database SQL di Azure e su SQL Server in macchine virtuali di Azure](/azure/sql-database/sql-database-paas-vs-sql-server-iaas#a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms). 

Azure SQL Data Warehouse può essere usato anche per set di dati di piccole e medie dimensioni, con un carico di lavoro a elevato utilizzo di calcolo e di memoria. Sono disponibili altre informazioni sui modelli di SQL Data Warehouse e sugli scenari comuni:

- [SQL Data Warehouse Patterns and Anti-Patterns](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/) (Modelli e anti-pattern di SQL Data Warehouse)
- [SQL Data Warehouse Loading Patterns and Strategies](https://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/) (Strategie e modelli di caricamento di SQL Data Warehouse)
- [Post di blog Migrazione di dati in Azure SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/)
- [Common ISV Application Patterns Using Azure SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/) (Modelli di applicazione ISV comuni che usano Azure SQL Data Warehouse)

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- Si vuole usare un servizio gestito anziché gestire direttamente i propri server?

- Si usano set di dati di enormi dimensioni o query estremamente complesse a esecuzione prolungata? In caso affermativo, è consigliabile una soluzione MPP. 

- Per un set di dati di grandi dimensioni, l'origine dati è strutturata o non strutturata? Per i dati non strutturati può essere necessaria l'elaborazione in un ambiente per Big Data, ad esempio Spark in HDInsight, Azure Databricks, LLAP Hive in HDInsight o Azure Data Lake Analytics. Tutti questi ambienti possono servire da motori ELT (Extract, Load, Transform) ed ETL (Extract, Transform, Load) e restituire i dati elaborati come dati strutturati, facilitando le operazioni di caricamento in SQL Data Warehouse o in una delle altre opzioni. Per i dati strutturati, SQL Data Warehouse ha un livello di prestazioni denominato Ottimizzato per il calcolo, specifico per carichi di lavoro a elevato utilizzo di calcolo che richiedono prestazioni estremamente elevate.

- Si vogliono separare i dati cronologici dai dati operativi correnti? In caso affermativo, scegliere una delle opzioni in cui è necessaria l'[orchestrazione](../technology-choices/pipeline-orchestration-data-movement.md). Si tratta di warehouse autonomi ottimizzati per l'accesso intenso in lettura e più adatti come archivio dati cronologici separato.

- È necessario integrare dati da più origini, oltre all'archivio dati OLTP? In caso affermativo, prendere in considerazione le opzioni che facilitano l'integrazione di più origini dati. 

- Si hanno requisiti di multi-tenancy? In caso affermativo, SQL Data Warehouse non è la soluzione ottimale per questo requisito. Per altre informazioni, vedere [SQL Data Warehouse Patterns and Anti-Patterns](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/) (Modelli e anti-pattern di SQL Data Warehouse).

- Si preferisce un archivio dati relazionale? In caso affermativo, limitare le opzioni a quelle dotate di un archivio dati relazionale, ma notare anche che è possibile usare strumenti come PolyBase per eseguire query, se necessario, su archivi dati non relazionali. Se, tuttavia, si decide di usare PolyBase, eseguire i test delle prestazioni sui set di dati non strutturati del carico di lavoro.

- Si hanno requisiti per la creazione di report in tempo reale? Se sono necessari tempi di risposta rapidi alle query su volumi elevati di inserimenti singleton, limitare le opzioni a quelle in grado di supportare la creazione di report in tempo reale.

- È necessario supportare un numero elevato di connessioni e utenti simultanei? La capacità di supportare connessioni e utenti simultanei dipende da diversi fattori. 

    - Per il database SQL di Azure, vedere i [limiti delle risorse documentati](/azure/sql-database/sql-database-resource-limits) in base al livello di servizio. 
    
    - SQL Server consente un massimo di 32.767 connessioni utente. Se è in esecuzione in una macchina virtuale, le prestazioni variano in base alle dimensioni della macchina virtuale e ad altri fattori. 
    
    - SQL Data Warehouse presenta limiti per quanto riguarda query e connessioni simultanee. Per altre informazioni, vedere [Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency). Per superare il problema dei limiti in SQL Data Warehouse, è consigliabile usare servizi complementari, ad esempio [Azure Analysis Services](/azure/analysis-services/analysis-services-overview).

- Che tipo di carico di lavoro si esegue? In generale, le soluzioni warehouse basate su MPP sono ideali per i carichi di lavoro analitici in batch. Se i carichi di lavoro sono di tipo transazionale, con numerose operazioni di lettura/scrittura di piccole dimensioni o diverse operazioni riga per riga, è consigliabile usare una delle opzioni SMP. Fanno eccezione a questa indicazione generale l'uso dell'elaborazione di flussi in un cluster HDInsight, ad esempio Spark Streaming, e l'archiviazione dei dati in una tabella Hive.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="general-capabilities"></a>Funzionalità generali

| | database SQL di Azure | SQL Server (macchina virtuale) | SQL Data Warehouse | Apache Hive in HDInsight | Hive LLAP in HDInsight |
| --- | --- | --- | --- | --- | --- | -- |
| Servizio gestito | Sì | No  | Sì | Sì <sup>1</sup> | Sì <sup>1</sup> |
| Orchestrazione dei dati necessaria (contiene una copia dei dati/dato cronologici) | No  | No  | Sì | Sì | Sì |
| Facilità di integrazione di più origini dati | No  | No  | Sì | Sì | Sì |
| Sospensione del calcolo supportata | No  | No  | Sì | No <sup>2</sup> | No <sup>2</sup> |
| Archivio dati relazionale | Sì | Sì |  Sì | No  | No  |
| Creazione di report in tempo reale | Sì | Sì | No  | No  | Sì |
| Punti di ripristino di backup flessibili | Sì | Sì | No <sup>3</sup> | Sì <sup>4</sup> | Sì <sup>4</sup> |
| SMP/MPP | SMP | SMP | MPP | MPP | MPP |

[1] Configurazione e scalabilità manuali.

[2] I cluster HDInsight possono essere eliminati quando non sono necessari e successivamente possono essere ricreati. Collegare al cluster un archivio dati esterno in modo che i dati vengano conservati in caso di eliminazione del cluster. È possibile usare Azure Data Factory per automatizzare il ciclo di vita del cluster creando un cluster HDInsight su richiesta per l'elaborazione del carico di lavoro. Eliminare quindi il cluster al termine dell'elaborazione.

[3] Con SQL Data Warehouse è possibile ripristinare un database a qualsiasi punto di ripristino disponibile degli ultimi sette giorni. Gli snapshot vengono eseguiti ogni quattro-otto ore e sono disponibili per sette giorni. Quando uno snapshot supera i sette giorni di vita, scade e il relativo punto di ripristino non è più disponibile.

[4] È consigliabile usare un [metastore Hive esterno](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters#use-hiveoozie-metastore) di cui può essere eseguito il backup e il ripristino in base alle esigenze. Le opzioni di backup e ripristino standard applicabili ad Archiviazione BLOB o a Data Lake Store possono essere usate per il backup dei dati o per soluzioni di backup e ripristino HDInsight di terze parti, ad esempio [Imanis Data](https://azure.microsoft.com/blog/imanis-data-cloud-migration-backup-for-your-big-data-applications-on-azure-hdinsight/) può essere usato per una maggiore flessibilità e semplicità d'uso.

### <a name="scalability-capabilities"></a>Funzionalità di scalabilità

| | database SQL di Azure | SQL Server (macchina virtuale) |  SQL Data Warehouse | Apache Hive in HDInsight | Hive LLAP in HDInsight |
| --- | --- | --- | --- | --- | --- | -- |
| Server regionali ridondanti per disponibilità elevata  | Sì | Sì | Sì | No  | No  |
| Supporto per la scalabilità orizzontale delle query (query distribuite)  | No  | No  | Sì | Sì | Sì |
| Scalabilità dinamica | Sì | No  | Sì <sup>1</sup> | No  | No  |
| Supporto per la memorizzazione nella cache dei dati in memoria | Sì |  Sì | No  | Sì | Sì |

[1] SQL Data Warehouse consente di aumentare o ridurre le prestazioni modificando il numero di unità Data Warehouse (DWU). Vedere [Gestire la potenza di calcolo in Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview).

### <a name="security-capabilities"></a>Funzionalità di sicurezza

|                         |           database SQL di Azure            |  SQL Server in una macchina virtuale  | SQL Data Warehouse |   Apache Hive in HDInsight    |    Hive LLAP in HDInsight     |
|-------------------------|-----------------------------------------|-----------------------------------|--------------------|-------------------------------|-------------------------------|
|     Authentication      | SQL/Azure Active Directory (Azure AD) | SQL/Azure AD/Active Directory |   SQL/Azure AD   | locale/Azure AD <sup>1</sup> | locale/Azure AD <sup>1</sup> |
|      Authorization      |                   Sì                   |                Sì                |        Sì         |              Sì              |       Sì <sup>1</sup>        |
|        Controllo         |                   Sì                   |                Sì                |        Sì         |              Sì              |       Sì <sup>1</sup>        |
| Crittografia di dati inattivi |            Sì <sup>2</sup>             |         Sì <sup>2</sup>          |  Sì <sup>2</sup>  |       Sì <sup>2</sup>        |       Sì <sup>1</sup>        |
|   Sicurezza a livello di riga    |                   Sì                   |                Sì                |        Sì         |              No                |       Sì <sup>1</sup>        |
|   Supporto dei firewall    |                   Sì                   |                Sì                |        Sì         |              Sì              |       Sì <sup>3</sup>        |
|  Maschera dati dinamica   |                   Sì                   |                Sì                |        Sì         |              No                |       Sì <sup>1</sup>        |

[1] Richiede l'uso di un [cluster HDInsight aggiunto al dominio](/azure/hdinsight/domain-joined/apache-domain-joined-introduction).

[2] Richiede l'uso della crittografia TDE (Transparent Data Encryption) per crittografare e decrittografare i dati inattivi.

[3] Quando è [usato all'interno di una rete virtuale di Azure](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network).

Sono disponibili altre informazioni sulla sicurezza del data warehouse:

* [Protezione del Database SQL](/azure/sql-database/sql-database-security-overview#connection-security)
* [Proteggere un database in SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-manage-security)
* [Estendere Azure HDInsight usando Rete virtuale di Azure](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)
* [Introduzione alla sicurezza Hadoop con i cluster HDInsight aggiunti al dominio](/azure/hdinsight/domain-joined/apache-domain-joined-introduction)

