---
title: Scelta di una tecnologia per l'elaborazione batch
description: ''
author: zoinerTejada
ms.date: 11/03/2018
ms.openlocfilehash: 0c6392fb0a921e95f2704696fb2447ac5ec6f4c0
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111326"
---
# <a name="choosing-a-batch-processing-technology-in-azure"></a>Scelta di una tecnologia per l'elaborazione batch in Azure

Le soluzioni per Big Data usano spesso i processi batch con esecuzione prolungata per filtrare, aggregare e preparare in altro modo i dati per l'analisi. In genere questi processi prevedono la lettura dei file di origine dalla risorsa di archiviazione scalabile, ad esempio HDFS, Azure Data Lake Store e Archiviazione di Azure, l'elaborazione dei file e la scrittura dell'output in nuovi file della risorsa di archiviazione scalabile.

Il requisito chiave per questi motori di elaborazione batch è la capacità di aumentare i calcoli per gestire un volume di dati elevato. Diversamente dall'elaborazione in tempo reale, tuttavia, per l'elaborazione batch si prevedono latenze (il tempo che intercorre tra l'inserimento dei dati e il calcolo del risultato) che vengono misurate in minuti all'ora.

## <a name="technology-choices-for-batch-processing"></a>Opzioni tecnologiche per l'elaborazione batch

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

[SQL Data Warehouse](/azure/sql-data-warehouse/) è un sistema distribuito progettato per eseguire analisi su dati di grandi dimensioni. Supporta l'elaborazione parallela su larga scala (MPP), che può essere usata per l'esecuzione di analisi ad alte prestazioni. Prendere in considerazione SQL Data Warehouse quando si hanno grandi quantità di dati (più di 1 TB) ed è in esecuzione un carico di lavoro analitico che può trarre vantaggio dal parallelismo.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.

[Data Lake Analytics](/azure/data-lake-analytics/data-lake-analytics-overview) è un servizio per processi di analisi su richiesta. È ottimizzato per l'elaborazione distribuita di set di dati di dimensioni molto estese archiviati in Azure Data Lake Store.

- Linguaggi: [U-SQL](/azure/data-lake-analytics/data-lake-analytics-u-sql-get-started) (con estensioni Python, R e C#).
- Integrazione con Azure Data Lake Store, BLOB del servizio di archiviazione di Azure, database SQL di Azure e SQL Data Warehouse.
- Modello di determinazione prezzi per processo.

### <a name="hdinsight"></a>HDInsight

HDInsight è un servizio Hadoop gestito. Usarlo per distribuire e gestire cluster Hadoop in Azure. Per l'elaborazione batch, è possibile usare [Spark](/azure/hdinsight/spark/apache-spark-overview), [Hive](/azure/hdinsight/hadoop/hdinsight-use-hive), [Hive LLAP](/azure/hdinsight/interactive-query/apache-interactive-query-get-started) e [MapReduce](/azure/hdinsight/hadoop/hdinsight-use-mapreduce).

- Linguaggi: R, Python, Java, Scala e SQL
- Autenticazione Kerberos con Active Directory, controllo di accesso basato su Apache Ranger
- Controllo completo del cluster Hadoop

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/) è una piattaforma di analisi basata su Apache Spark. Questa opzione può essere considerata una sorta di "Spark distribuito come servizio". È il modo più semplice per usare Spark nella piattaforma Azure.

- Linguaggi: R, Python, Java, Scala e Spark SQL
- Tempi di avvio del cluster più rapidi e terminazione e ridimensionamento automatici.
- Gestione automatica del cluster Spark.
- Integrazione predefinita con l'archivio BLOB di Azure, Azure Data Lake Storage (ADLS), Azure SQL Data Warehouse (SQL DW) e altri servizi. Vedere [Data Sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) (Origini dati).
- Autenticazione utente con Azure Active Directory.
- [Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) basati sul Web per la collaborazione e l'esplorazione dei dati.
- Supporto di [cluster abilitati per GPU](https://docs.azuredatabricks.net/user-guide/clusters/gpu.html).

### <a name="azure-distributed-data-engineering-toolkit"></a>Azure Distributed Data Engineering Toolkit

[Distributed Data Engineering Toolkit](https://github.com/azure/aztk) (AZTK) è uno strumento per il provisioning in Azure di cluster Spark in Docker su richiesta.

AZTK non è un servizio di Azure. È piuttosto uno strumento lato client con un'interfaccia della riga di comando e Python SDK basata su Azure Batch. Questa opzione offre il massimo controllo sull'infrastruttura durante la distribuzione di un cluster Spark.

- Immagine Docker personalizzata.
- Uso di VM con priorità bassa per uno sconto dell'80%.
- Cluster in modalità mista che usano sia VM con priorità bassa che VM dedicate.
- Supporto predefinito per la connessione dell'archivio BLOB di Azure e di Azure Data Lake.

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- Si vuole usare un servizio gestito anziché gestire direttamente i propri server?

- Si desidera creare la logica di elaborazione batch in modo dichiarativo o imperativo?

- L'elaborazione batch verrà eseguita in burst? In caso affermativo, prendere in considerazione le opzioni che consentono la terminazione automatica del cluster o quelle con un modello di determinazione prezzi per processo batch.

- È necessario eseguire query sugli archivi dati relazionali durante l'elaborazione batch, ad esempio per cercare dati di riferimento? In caso affermativo, considerare le opzioni che consentono di eseguire query sugli archivi relazionali esterni.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="general-capabilities"></a>Funzionalità generali

<!-- markdownlint-disable MD033 -->

| | Azure Data Lake Analytics. | Azure SQL Data Warehouse | HDInsight | Azure Databricks |
| --- | --- | --- | --- | --- | --- |
| Servizio gestito | Yes | Yes | Sì <sup>1</sup> | Yes |
| Archivio dati relazionale | Yes | Sì | No  | No  |
| Modello di prezzi | Per processo batch | Per ora di cluster | Per ora di cluster | Unità Databricks<sup>2</sup> + ora di cluster |

[1] Con configurazione e scalabilità manuali.

[2] Un'unità Databricks è un'unità di capacità di elaborazione all'ora.

### <a name="capabilities"></a>Capabilities

| | Azure Data Lake Analytics. | SQL Data Warehouse | HDInsight con Spark | HDInsight con Hive | HDInsight con Hive LLAP | Azure Databricks |
| --- | --- | --- | --- | --- | --- | --- |
| Scalabilità automatica | No  | No  | No  | No  | No  | Yes |
| Granularità della scalabilità orizzontale  | Per processo | Per cluster | Per cluster | Per cluster | Per cluster | Per cluster |
| Memorizzazione nella cache dei dati in memoria | No  | Yes | Sì | No  | Yes | Yes |
| Query da archivi relazionali esterni | Yes | No  | Yes | No  | No  | Yes |
| Authentication  | Azure AD | SQL/Azure AD | No  | Azure AD<sup>1</sup> | Azure AD<sup>1</sup> | Azure AD |
| Controllo  | Yes | Sì | No  | Sì <sup>1</sup> | Sì <sup>1</sup> | Yes |
| Sicurezza a livello di riga | No  | No  | No  | Sì <sup>1</sup> | Sì <sup>1</sup> | No  |
| Supporto dei firewall | Yes | Sì | Yes | Sì <sup>2</sup> | Sì <sup>2</sup> | No  |
| Maschera dati dinamica | No  | No  | No  | Sì <sup>1</sup> | Sì <sup>1</sup> | No  |

<!-- markdownlint-enable MD033 -->

[1] Richiede l'uso di un [cluster HDInsight aggiunto al dominio](/azure/hdinsight/domain-joined/apache-domain-joined-introduction).

[2] In caso di [uso all'interno di una rete virtuale di Azure](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network).
