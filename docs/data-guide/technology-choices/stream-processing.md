---
title: Scelta di una tecnologia per l'elaborazione di flussi
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 51129bd09974151ef66a1d660ee3cfa8f79eeb0c
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111513"
---
# <a name="choosing-a-stream-processing-technology-in-azure"></a>Scelta di una tecnologia per l'elaborazione di flussi in Azure

Questo articolo mette a confronto le tecnologie disponibili per l'elaborazione di flussi in tempo reale in Azure.

L'elaborazione di flussi in tempo reale utilizza i messaggi provenienti da una coda o da un risorsa di archiviazione basata su file, li elabora e inoltra il risultato a un'altra coda di messaggi, a un archivio di file o a un database. L'elaborazione può includere operazioni di query, filtraggio e aggregazione sui messaggi. I motori per l'elaborazione di flussi devono essere in grado di utilizzare flussi di dati infiniti e generare risultati con una latenza minima. Per altre informazioni, vedere [Elaborazione in tempo reale](../big-data/real-time-processing.md).

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-when-choosing-a-technology-for-real-time-processing"></a>Opzioni disponibili per la scelta di una tecnologia per l'elaborazione in tempo reale

<!-- markdownlint-enable MD026 -->

In Azure tutti gli archivi dati elencati di seguito soddisfano i requisiti di base per il supporto dell'elaborazione in tempo reale:

- [Azure Stream Analytics](/azure/stream-analytics/)
- [HDInsight con Spark Streaming](/azure/hdinsight/spark/apache-spark-streaming-overview)
- [Apache Spark in Azure Databricks](/azure/azure-databricks/)
- [HDInsight con Storm](/azure/hdinsight/storm/apache-storm-overview)
- [Funzioni di Azure](/azure/azure-functions/functions-overview)
- [Processi Web del servizio app di Azure](/azure/app-service/web-sites-create-web-jobs)

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per gli scenari di elaborazione in tempo reale, rispondere prima di tutto a queste domande per scegliere il servizio adatto alle proprie esigenze:

- Si preferisce adottare un approccio dichiarativo o imperativo per modificare la logica di elaborazione dei flussi?

- È necessario il supporto predefinito per l'elaborazione temporale o windowing?

- I dati arrivano in più formati oltre ad Avro, JSON o CSV? In caso affermativo, prendere in considerazione le opzioni che supportano qualsiasi formato tramite codice personalizzato.

- È necessario aumentare la capacità di elaborazione oltre 1 GB al secondo? In caso affermativo, prendere in considerazione le opzioni che supportano la scalabilità in base alla dimensione del cluster.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="general-capabilities"></a>Funzionalità generali

| | Analisi di flusso di Azure | HDInsight con Spark Streaming | Apache Spark in Azure Databricks | HDInsight con Storm | Funzioni di Azure | Processi Web del servizio app di Azure |
| --- | --- | --- | --- | --- | --- | --- |
| Programmabilità | Linguaggio di query di analisi di flusso, JavaScript | Scala, Python, Java | Scala, Python, Java, R | Java, C# | C#, F#, Node.js | C#, Node.js, PHP, Java, Python |
| Paradigma di programmazione | Dichiarativo | Dichiarativo e imperativo | Dichiarativo e imperativo | Imperativo | Imperativo | Imperativo |
| Modello di prezzi | [Unità di streaming](https://azure.microsoft.com/pricing/details/stream-analytics/) | Per ora del cluster | [Unità di Databricks](https://azure.microsoft.com/pricing/details/databricks/) | Per ora del cluster | Per esecuzione di funzione e utilizzo di risorse | Per ora del piano di servizio app |  

### <a name="integration-capabilities"></a>Funzionalità di integrazione

| | Analisi di flusso di Azure | HDInsight con Spark Streaming | Apache Spark in Azure Databricks | HDInsight con Storm | Funzioni di Azure | Processi Web del servizio app di Azure |
| --- | --- | --- | --- | --- | --- | --- |
| Input | Hub eventi di Azure, hub IoT di Azure, archiviazione BLOB di Azure  | Hub eventi, hub IoT, Kafka, HDFS, BLOB del servizio di archiviazione, Azure Data Lake Store  | Hub eventi, hub IoT, Kafka, HDFS, BLOB del servizio di archiviazione, Azure Data Lake Store  | Hub eventi, hub IoT, BLOB del servizio di archiviazione, Azure Data Lake Store  | [Binding supportati](/azure/azure-functions/functions-triggers-bindings#supported-bindings) | Bus di servizio, Code di archiviazione, BLOB del servizio di archiviazione, Hub eventi, WebHooks, Cosmos DB, File |
| Sink |  Azure Data Lake Store, database SQL di Azure SQL, BLOB di archiviazione, Hub eventi, Power BI, archiviazione tabelle, code del bus di servizio, argomenti del bus di servizio, Cosmos DB, Funzioni di Azure  | HDFS, Kafka, BLOB del servizio di archiviazione, Azure Data Lake Store, Cosmos DB | HDFS, Kafka, BLOB del servizio di archiviazione, Azure Data Lake Store, Cosmos DB | Hub eventi, bus di servizio, Kafka | [Binding supportati](/azure/azure-functions/functions-triggers-bindings#supported-bindings) | Bus di servizio, Code di archiviazione, BLOB del servizio di archiviazione, Hub eventi, WebHooks, Cosmos DB, File |

### <a name="processing-capabilities"></a>Funzionalità di elaborazione

| | Analisi di flusso di Azure | HDInsight con Spark Streaming | Apache Spark in Azure Databricks | HDInsight con Storm | Funzioni di Azure | Processi Web del servizio app di Azure |
| --- | --- | --- | --- | --- | --- | --- |
| Supporto predefinito per elaborazione temporale/windowing | Yes | Sì | Sì | Sì | No  | No  |
| Formati dei dati di input | Avro, JSON o CSV, con codifica UTF-8 | Qualsiasi formato con codice personalizzato | Qualsiasi formato con codice personalizzato | Qualsiasi formato con codice personalizzato | Qualsiasi formato con codice personalizzato | Qualsiasi formato con codice personalizzato |
| Scalabilità | [Partizioni per query](/azure/stream-analytics/stream-analytics-parallelization) | Limitata in base alle dimensioni del cluster | Limitata in base alla configurazione di scalabilità del cluster Databricks | Limitata in base alle dimensioni del cluster | Elaborazione parallela di un massimo di 200 istanze di app per le funzioni | Limitata dalla capacità del piano di servizio app |
| Supporto per la gestione di eventi di arrivo in ritardo e con ordine non corretto | Sì | Sì | Sì | Sì | No  | No  |

Vedere anche la pagina relativa alla

- [Scelta di una tecnologia di inserimento di messaggi in tempo reale](./real-time-ingestion.md)
- [Elaborazione in tempo reale](../big-data/real-time-processing.md)
