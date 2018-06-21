---
title: Scelta di una tecnologia per l'elaborazione batch
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 0117798af82f2caa6704dc86e88be57f09c381ea
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30848666"
---
# <a name="choosing-a-batch-processing-technology-in-azure"></a>Scelta di una tecnologia per l'elaborazione batch in Azure

Le soluzioni per Big Data usano spesso i processi batch con esecuzione prolungata per filtrare, aggregare e preparare in altro modo i dati per l'analisi. In genere questi processi prevedono la lettura dei file di origine dalla risorsa di archiviazione scalabile, ad esempio HDFS, Azure Data Lake Store e Archiviazione di Azure, l'elaborazione dei file e la scrittura dell'output in nuovi file della risorsa di archiviazione scalabile. 

Il requisito chiave per questi motori di elaborazione batch è la capacità di aumentare i calcoli per gestire un volume di dati elevato. Diversamente dall'elaborazione in tempo reale, tuttavia, per l'elaborazione batch si prevedono latenze (il tempo che intercorre tra l'inserimento dei dati e il calcolo del risultato) che vengono misurate in minuti all'ora.

## <a name="what-are-your-options-when-choosing-a-batch-processing-technology"></a>Opzioni disponibili per la scelta di una tecnologia per l'elaborazione batch

In Azure tutti gli archivi dati elencati di seguito soddisfano i requisiti di base per l'elaborazione batch:

- [Azure Data Lake Analytics.](/azure/data-lake-analytics/)
- [Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
- [HDInsight con Spark](/azure/hdinsight/spark/apache-spark-overview)
- [HDInsight con Hive](/azure/hdinsight/hadoop/hdinsight-use-hive)
- [HDInsight con Hive LLAP](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- Si vuole usare un servizio gestito anziché gestire direttamente i propri server?

- Si desidera creare la logica di elaborazione batch in modo dichiarativo o imperativo?

- L'elaborazione batch verrà eseguita in burst? In caso affermativo, prendere in considerazione le opzioni che consentono di sospendere il cluster o quelle con un modello di determinazione dei prezzi per processo batch.

- È necessario eseguire query sugli archivi dati relazionali durante l'elaborazione batch, ad esempio per cercare dati di riferimento? In caso affermativo, considerare le opzioni che consentono di eseguire query sugli archivi relazionali esterni.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità. 

### <a name="general-capabilities"></a>Funzionalità generali

| | Azure Data Lake Analytics. | Azure SQL Data Warehouse | HDInsight con Spark | HDInsight con Hive | HDInsight con Hive LLAP |
| --- | --- | --- | --- | --- | --- |
| Servizio gestito | Sì | Sì | Sì <sup>1</sup> | Sì <sup>1</sup> | Sì <sup>1</sup> |
| Sospensione del calcolo supportata | No  | Sì | No  | No  | No  |
| Archivio dati relazionale | Sì | Sì | No  | No  | No  |
| Programmabilità | U-SQL | T-SQL | Python, Scala, Java, R | HiveQL | HiveQL |
| Paradigma di programmazione | Dichiarativo e imperativo  | Dichiarativo | Dichiarativo e imperativo | Dichiarativo | Dichiarativo | 
| Modello di prezzi | Per processo batch | Per ora di cluster | Per ora di cluster | Per ora di cluster | Per ora di cluster |  

[1] Con configurazione e scalabilità manuali.

### <a name="integration-capabilities"></a>Funzionalità di integrazione

| | Azure Data Lake Analytics. | SQL Data Warehouse | HDInsight con Spark | HDInsight con Hive | HDInsight con Hive LLAP |
| --- | --- | --- | --- | --- | --- |
| Accesso da Azure Data Lake Store | Sì | Sì | Sì | Sì | Sì |
| Query da Archiviazione di Azure | Sì | Sì | Sì | Sì | Sì |
| Query da archivi relazionali esterni | Sì | No  | Sì | No  | No  |

### <a name="scalability-capabilities"></a>Funzionalità di scalabilità

| | Azure Data Lake Analytics. | SQL Data Warehouse | HDInsight con Spark | HDInsight con Hive | HDInsight con Hive LLAP |
| --- | --- | --- | --- | --- | --- |
| Granularità della scalabilità orizzontale  | Per processo | Per cluster | Per cluster | Per cluster | Per cluster |
| Scalabilità orizzontale rapida (meno di 1 minuto) | Sì | Sì | No  | No  | No  |
| Memorizzazione nella cache dei dati in memoria | No  | Sì | Sì | No  | Sì | 

### <a name="security-capabilities"></a>Funzionalità di sicurezza

| | Azure Data Lake Analytics. | SQL Data Warehouse | HDInsight con Spark | Apache Hive in HDInsight | Hive LLAP in HDInsight |
| --- | --- | --- | --- | --- | --- |
| Authentication  | Azure Active Directory (Azure AD) | SQL/Azure AD | No  | locale/Azure AD <sup>1</sup> | locale/Azure AD <sup>1</sup> |
| Authorization  | Sì | Sì| No  | Sì <sup>1</sup> | Sì <sup>1</sup> |
| Controllo  | Sì | Sì | No  | Sì <sup>1</sup> | Sì <sup>1</sup> |
| Crittografia di dati inattivi | Sì| Sì <sup>2</sup> | Sì | Sì | Sì |
| Sicurezza a livello di riga | No  | Sì | No  | Sì <sup>1</sup> | Sì <sup>1</sup> |
| Supporto dei firewall | Sì | Sì | Sì | Sì <sup>3</sup> | Sì <sup>3</sup> |
| Maschera dati dinamica | No  | No  | No  | Sì <sup>1</sup> | Sì <sup>1</sup> |

[1] Richiede l'uso di un [cluster HDInsight aggiunto al dominio](/azure/hdinsight/domain-joined/apache-domain-joined-introduction).

[2] Richiede l'uso della crittografia TDE (Transparent Data Encryption) per crittografare e decrittografare i dati inattivi.

[3] Quando è [usato all'interno di una rete virtuale di Azure](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network).
