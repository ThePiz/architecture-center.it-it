---
title: Scelta di una tecnologia di orchestrazione di una pipeline di dati
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 76a101b76497ae2b2aacff973175bb0fe4703d9e
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54482443"
---
# <a name="choosing-a-data-pipeline-orchestration-technology-in-azure"></a>Scelta di una tecnologia di orchestrazione di una pipeline di dati in Azure

La maggior parte delle soluzioni di Big Data sono costituite da operazioni ripetute di elaborazione dei dati, incapsulate in flussi di lavoro. Un agente di orchestrazione di pipeline è uno strumento che consente di automatizzare tali flussi di lavoro. Un agente di orchestrazione può pianificare i processi, eseguire i flussi di lavoro e coordinare le dipendenze tra attività.

## <a name="what-are-your-options-for-data-pipeline-orchestration"></a>Opzioni disponibili per l'orchestrazione di una pipeline di dati

In Azure, i servizi e gli strumenti seguenti soddisfano i requisiti di base per l'orchestrazione di una pipeline, il flusso di controllo e lo spostamento di dati:

- [Data factory di Azure](/azure/data-factory/)
- [Oozie in HDInsight](/azure/hdinsight/hdinsight-use-oozie-linux-mac)
- [SQL Server Integration Services (SSIS)](/sql/integration-services/sql-server-integration-services)

Questi servizi e strumenti possono essere usati in modo indipendente l'uno dall'altro oppure in combinazione per creare una soluzione ibrida. Ad esempio, Integration Runtime (IR) in Azure Data Factory versione 2 può eseguire in modo nativo i pacchetti SSIS in un ambiente di calcolo di Azure gestito. Anche se sono presenti alcuni aspetti comuni tra le funzionalità di questi servizi, esistono alcune differenze principali.

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- Sono necessarie funzionalità di Big Data per lo spostamento e la trasformazione dei dati? In genere, ciò comporta la necessità di volumi elevati di dati. In caso affermativo, limitare la scelta alle opzioni più adatte ai Big Data.

- È necessario un servizio gestito che può operare su larga scala? In caso affermativo, selezionare uno dei servizi basati su cloud che non sono limitati dalla potenza di elaborazione locale.

- Alcune origini dati si trovano in locale? In caso affermativo, cercare opzioni che possono funzionare con origini dati o destinazioni sia cloud che locali.

- L'origine dati è archiviata nell'archiviazione BLOB in un file system HDFS? In questo caso, scegliere un'opzione che supporti le query Hive.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="general-capabilities"></a>Funzionalità generali

| | Data factory di Azure | SQL Server Integration Services (SSIS) | Oozie in HDInsight
| --- | --- | --- | --- |
| Gestito | Yes | No  | Yes |
| Basato su cloud | Yes | No (locale) | Yes |
| Prerequisito | Sottoscrizione di Azure | SQL Server  | Sottoscrizione di Azure, cluster HDInsight |
| Strumenti di gestione | Portale di Azure, PowerShell, interfaccia della riga di comando, .NET SDK | SSMS, PowerShell | Shell Bash, API REST di Oozie, interfaccia utente Web di Oozie |
| Prezzi | Pagamento in base all'utilizzo | Assegnazione di licenze/pagamento in base alle funzionalità | Nessun costo aggiuntivo per l'esecuzione del cluster HDInsight |

### <a name="pipeline-capabilities"></a>Funzionalità della pipeline

| | Data factory di Azure | SQL Server Integration Services (SSIS) | Oozie in HDInsight
| --- | --- | --- | --- |
| Copiare i dati | Yes | Sì | Yes |
| Trasformazioni personalizzate | Yes | Yes | Sì (processi MapReduce, Pig e Hive) |
| Assegnazione dei punteggi di Azure Machine Learning | Yes | Sì (con script) | No  |
| HDInsight su richiesta | Yes | No  | No  |
| Azure Batch | Yes | No  | No  |
| Pig, Hive, MapReduce | Yes | No  | Yes |
| Spark | Yes | No  | No  |
| Esecuzione del pacchetto SSIS | Yes | Sì | No  |
| Flusso di controllo | Yes | Sì | Yes |
| Accedere ai dati locali | Yes | Sì | No  |

### <a name="scalability-capabilities"></a>Funzionalità di scalabilità

| | Data factory di Azure | SQL Server Integration Services (SSIS) | Oozie in HDInsight
| --- | --- | --- | --- |
| Aumentare le prestazioni | Yes | No  | No  |
| Scalabilità orizzontale | Yes | No  | Sì (mediante aggiunta di nodi di lavoro al cluster) |
| Ottimizzazione per Big Data | Yes | No  | Yes |

