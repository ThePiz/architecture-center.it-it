---
title: Stile di architettura per Big Data
description: Descrive i vantaggi, le problematiche e le procedure consigliate per le architetture per i Big Data in Azure
author: MikeWasson
ms.openlocfilehash: 4e8b58d5fa0f6a441d70e05ec7d6a0e668712563
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
ms.locfileid: "24540890"
---
# <a name="big-data-architecture-style"></a>Stile di architettura per Big Data

Un'architettura per Big Data è progettata per gestire l'inserimento, l'elaborazione e l'analisi di dati troppo grandi o complessi per i sistemi di database tradizionali.

![](./images/big-data-logical.svg)

 Le soluzioni per i Big Data implicano in genere uno o più dei seguenti tipi di carico di lavoro:

- L'elaborazione batch di origini di Big Data inattivi.
- L'elaborazione in tempo reale di Big Data in movimento.
- L'esplorazione interattiva di Big Data.
- L'analisi predittiva e il Machine Learning.

La maggior parte delle architetture per i Big Data include alcuni o tutti i seguenti componenti:

- **Origini dati**: tutte le soluzioni per i Big Data iniziano con una o più origini dati. Tra gli esempi sono inclusi:

    - Archivi dati di applicazioni, ad esempio database relazionali.
    - File statici generati dalle applicazioni, ad esempio file di log di server Web.
    - Origini dati in tempo reale, ad esempio dispositivi IoT.

- **Archiviazione dei dati**: i dati per le operazioni di elaborazione batch vengono in genere archiviati in un archivio di file distribuito che può contenere volumi elevati di file di grandi dimensioni in vari formati. Questo tipo di archivio viene spesso chiamato *data lake*. Alcune opzioni per l'implementazione di questo tipo di archiviazione sono Azure Data Lake Store o i contenitori BLOB in Archiviazione di Azure. 

- **Elaborazione batch**: i set di dati sono di grandi dimensioni e una soluzione per Big Data deve spesso elaborare i file di dati mediante processi batch con esecuzione prolungata per filtrare, aggregare e preparare in altro modo i dati per l'analisi. In genere questi processi prevedono la lettura dei file di origine, la relativa elaborazione e la scrittura dell'output in nuovi file. Le opzioni includono l'esecuzione di processi U-SQL in Azure Data Lake Analytics, l'utilizzo di Hive, Pig o di processi MapReduce personalizzati in un cluster HDInsight Hadoop o l'utilizzo di programmi Java, Scala o Python in un cluster HDInsight Spark.

- **Inserimento di messaggi in tempo reale**: se la soluzione include origini in tempo reale, l'architettura deve includere un modo per acquisire e archiviare i messaggi in tempo reale per l'elaborazione del flusso. Potrebbe trattarsi di un archivio dati semplice in cui i messaggi in ingresso vengono rilasciati in una cartella per l'elaborazione. Tuttavia, molte soluzioni richiedono che un archivio di inserimento dei messaggi funga da buffer per i messaggi e supporti l'elaborazione scale-out, il recapito affidabile e altri tipi di semantica di accodamento dei messaggi. Le opzioni includono Hub eventi di Azure, hub IoT di Azure e Kafka.

- **Elaborazione del flusso**: dopo avere acquisito i messaggi in tempo reale, la soluzione deve elaborarli filtrando, aggregando e preparando in altro modo i dati per l'analisi. I dati del flusso elaborati vengono quindi scritti in un sink di output. Analisi di flusso di Azure offre un servizio di elaborazione del flusso gestito basato su query SQL in esecuzione perenne che operano su flussi non associati. È possibile anche usare tecnologie di streaming open source di Apache, come Storm e Spark Streaming in un cluster HDInsight.

- **Archivio dati analitici**: numerose soluzioni per Big Data preparano i dati per l'analisi e quindi servono i dati elaborati in un formato strutturato su sui è possibile eseguire query con strumenti analitici. L'archivio dati analitici usato per rispondere a queste query può essere un data warehouse relazionale in stile Kimball, come nella maggior parte delle soluzioni di business intelligence (BI) tradizionali. In alternativa, i dati possono essere presentati tramite una tecnologia NoSQL a bassa latenza come HBase o un database Hive interattivo che fornisce un'astrazione di metadati sui file di dati nell'archivio dati distribuito. Azure SQL Data Warehouse fornisce un servizio gestito per il data warehousing su larga scala basato su cloud. HDInsight supporta Interactive Hive, HBase e Spark SQL, che possono anche essere usati per fornire dati per l'analisi.

- **Analisi e creazione di report**: l'obiettivo della maggior parte delle soluzioni per Big Data è fornire informazioni dettagliate sui dati tramite l'analisi e il reporting. Per consentire agli utenti di analizzare i dati, l'architettura può includere un livello di modellazione dei dati, ad esempio un cubo OLAP multidimensionale o un modello di dati tabulari in Azure Analysis Services. Potrebbe inoltre supportare la business intelligence in modalità self-service, usando le tecnologie di modellazione e visualizzazione in Microsoft Power BI o Microsoft Excel. L'analisi e il reporting possono anche assumere la forma di esplorazione interattiva dei dati da parte di data scientist o analisti di dati. Per questi scenari, molti servizi di Azure supportano notebook analitici come Jupyter, consentendo a questi utenti di sfruttare le proprie competenze esistenti con Python o R. Per l'esplorazione di dati su larga scala, è possibile usare Microsoft R Server, sia autonomo che con Spark.

- **Orchestrazione**: la maggior parte delle soluzioni per Big Data consiste in operazioni ripetute di elaborazione dei dati, incapsulate in flussi di lavoro, che trasformano i dati di origine, spostano i dati tra più origini e sink, caricano i dati elaborati in un archivio dati analitico o li inseriscono direttamente in un report o i un dashboard. Per automatizzare questi flussi di lavoro, è possibile usare una tecnologia di orchestrazione come Azure Data Factory o Apache Oozie e Sqoop.

Azure include molti servizi che possono essere usati in un'architettura per Big Data. Si dividono approssimativamente in due categorie:

- Servizi gestiti, tra cui Azure Data Lake Store, Azure Data Lake Analytics, Azure Data Warehouse, Analisi di flusso di Azure, Azure Event Hub, Hub IoT e Azure Data Factory.
- Tecnologie open source basate sulla piattaforma Apache Hadoop, tra cui Hadoop Distributed File System, HBase, Hive, Pig, Spark, Storm, Oozie, Sqoop e Kafka. Queste tecnologie sono disponibili in Azure nel servizio Azure HDInsight.

Queste opzioni non si escludono a vicenda e molte soluzioni combinano le tecnologie open source con i servizi Azure.

## <a name="when-to-use-this-architecture"></a>Quando usare questa architettura

Prendere in considerazione questo stile di architettura quando è necessario:

- Archiviare ed elaborare i dati in volumi troppo grandi per un database tradizionale.
- Trasformare i dati non strutturati per consentire l'analisi e il reporting.
- Acquisire, elaborare e analizzare i flussi di dati non associati in tempo reale o con una latenza bassa.
- Usare Microsoft Azure Machine Learning o Servizi cognitivi Microsoft.

## <a name="benefits"></a>Vantaggi

- **Scelte tecnologiche**. È possibile combinare i servizi gestiti Azure e le tecnologie Apache nei cluster HDInsight per sfruttare appieno le competenze o gli investimenti tecnologici esistenti.
- **Prestazioni tramite il parallelismo**. Le soluzioni per i Big Data sfruttano i vantaggi del parallelismo, consentendo soluzioni ad alte prestazioni che si adattano a grandi volumi di dati.
- **Scalabilità elastica**. Tutti i componenti dell'architettura per Big Data supportano il provisioning scale-out che consente di adattare la soluzione a carichi di lavoro piccoli o grandi e pagare solo per le risorse usate.
- **Interoperabilità con soluzioni esistenti**. I componenti dell'architettura per Big Data vengono usati anche per l'elaborazione IoT e le soluzioni di BI enterprise, consentendo di creare una soluzione integrata tra i carichi di lavoro di dati.

## <a name="challenges"></a>Problematiche

- **Complessità**. Le soluzioni per Big Data possono essere estremamente complesse, con numerosi componenti per gestire l'inserimento di dati da più origini dati. Può essere difficile compilare, testare e risolvere i problemi relativi ai processi con Big Data. Inoltre potrebbe esistere un numero elevato di impostazioni di configurazione tra più sistemi che devono essere usate per ottimizzare le prestazioni.
- **Competenze**. Molte tecnologie per Big Data sono altamente specializzate e usano framework e linguaggi che non sono tipici di architetture applicative più generali. D'altra parte, le tecnologie per i Big Data stanno evolvendo nuove API basate su altri linguaggi consolidati. Ad esempio, il linguaggio U-SQL in Azure Data Lake Analytics si basa su una combinazione di Transact-SQL e C#. Analogamente, le API basate su SQL sono disponibili per Hive, HBase e Spark.
- **Maturità tecnologica**. Molte tecnologie usate per i Big Data sono in continua evoluzione. Mentre le tecnologie Hadoop principali come Hive e Pig sono ormai stabili, le tecnologie emergenti come Spark introducono numerosi cambiamenti e miglioramenti in ogni nuova versione. I servizi gestiti come Azure Data Lake Analytics e Azure Data Factory sono relativamente recenti rispetto ad altri servizi Azure e probabilmente si evolveranno ancora nel tempo.
- **Sicurezza**. Le soluzioni per Big Data di solito si basano sull'archiviazione di tutti i dati statici in un data lake centralizzato. Garantire l'accesso sicuro a questi dati può essere difficile, specialmente quando i dati devono essere inseriti e usati da più applicazioni e piattaforme.

## <a name="best-practices"></a>Procedure consigliate

- **Sfruttare il parallelismo**. La maggior parte delle tecnologie di elaborazione per i Big Data distribuisce il carico di lavoro su più unità di elaborazione. È necessario che i file di dati statici vengano creati e archiviati in un formato divisibile. I file system distribuiti come HDFS possono ottimizzare le prestazioni in lettura e in scrittura e l'elaborazione effettiva viene eseguita da più nodi del cluster in parallelo, riducendo i tempi complessivi del processo.

- **Dati di partizione**. L'elaborazione batch di solito si verifica in base a una pianificazione ricorrente, ad esempio, settimanale o mensile. Partizionare i file di dati e le strutture di dati come tabelle, in base a periodi temporali corrispondenti alla pianificazione di elaborazione. Ciò semplifica l'inserimento dei dati e la pianificazione dei processi e quindi la risoluzione dei problemi. Inoltre, le tabelle delle partizioni usate nelle query Hive, U-SQL o SQL possono migliorare significativamente le prestazioni delle query.

- **Applicare la semantica dello schema in lettura**. L'utilizzo di un data lake consente di combinare l'archiviazione per i file in più formati, siano essi strutturati, semi-strutturati o non strutturati. Usare la semantica dello *schema in lettura* che applica uno schema sui dati in fase di elaborazione e non in fase di archiviazione. In questo modo aumenta la flessibilità della soluzione e si prevengono colli di bottiglia durante l'inserimento dei dati a causa della convalida dei dati e del controllo del tipo.

- **Elaborare i dati sul posto**. Le soluzioni tradizionali di business intelligence usano spesso un processo di estrazione, trasformazione e (ETL) per trasferire i dati in un data warehouse. Con volumi di dati di dimensioni maggiori e una gamma di formati più ampia, in genere le soluzioni per Big Data usano variazioni del processo ETL, ad esempio trasformazione, estrazione e caricamento (TEL). Con questo approccio i dati vengono elaborati all'interno dell'archivio dati distribuito, trasformandoli nella struttura richiesta, prima di spostare i dati trasformati in un archivio dati analitici.

- **Bilanciare il costo unitario e il costo per l'utilizzo**. Per i processi di elaborazione batch, è importante considerare due fattori: il costo unitario dei nodi di calcolo e il costo al minuto dell'utilizzo di tali nodi per completare il processo. Ad esempio, un processo batch può richiedere otto ore con quattro nodi cluster. Tuttavia, potrebbe risultare che il processo usi tutti e quattro i nodi solo durante le prime due ore e, successivamente, siano necessari solo due nodi. In tal caso, eseguire l'intero lavoro su due nodi aumenterebbe il tempo totale del lavoro, ma non lo raddoppierebbe e quindi il costo totale sarebbe inferiore. In alcuni scenari aziendali, un tempo di elaborazione più lungo può essere preferibile al costo più elevato dell'utilizzo di risorse cluster sottoutilizzate.

- **Separare le risorse del cluster**. Quando si distribuiscono cluster HDInsight, di solito si ottengono prestazioni migliori mediante il provisioning di risorse cluster separate per ogni tipo di carico di lavoro. Ad esempio, sebbene i cluster Spark includano Hive, se è necessario eseguire un'elaborazione estesa con Hive e Spark, è consigliabile considerare l'implementazione di cluster Spark e Hadoop dedicati separati. Analogamente, se si usa HBase e Storm per l'elaborazione di flussi a bassa latenza e Hive per l'elaborazione batch, considerare l'utilizzo di cluster separati per Storm, HBase e Hadoop.

- **Orchestrare l'inserimento di dati**. In alcuni casi, le applicazioni aziendali esistenti possono scrivere file di dati per l'elaborazione batch direttamente nei contenitori BLOB di archiviazione di Azure, dove possono essere usati da HDInsight o Azure Data Lake Analytics. Tuttavia sarà spesso necessario orchestrare l'inserimento di dati da origini dati locali o esterne nel data lake. Usare un flusso di lavoro o una pipeline di orchestrazione, ad esempio quelli supportati da Azure Data Factory oppure Oozie, per ottenere questo risultato in modo prevedibile e gestibile a livello centrale.

- **Eseguire lo scrubbing dei dati sensibili in anticipo**. Il flusso di lavoro per l'inserimento dei dati dovrebbe eseguire lo scrubbing dei dati sensibili all'inizio del processo, per evitare di archiviarli nel data lake.
