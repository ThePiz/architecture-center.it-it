---
title: Scelta di una tecnologia per il trasferimento dei dati
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: d5fbdc3a49ab16be2626b772ffd1af782963a2f0
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902681"
---
# <a name="transferring-data-to-and-from-azure"></a>Trasferimento dei dati da e verso Azure

Sono disponibili diverse opzioni per il trasferimento dei dati da e verso Azure, in base alle esigenze specifiche:

## <a name="physical-transfer"></a>Trasferimento fisico

L'uso di hardware fisico per il trasferimento dei dati in Azure è una buona opzione nelle situazioni seguenti:

- La rete è lenta o poco affidabile.
- Un aumento della larghezza di banda di rete è troppo dispendioso.
- I criteri di sicurezza o dell'organizzazione non consentono connessioni in uscita quando si gestiscono dati sensibili. 

Se il tempo necessario per trasferire i dati è di interesse prioritario, può essere opportuno eseguire un test per verificare se il trasferimento tramite rete è effettivamente più lento del trasporto fisico.

Per il trasporto fisico dei dati in Azure sono disponibili due opzioni:
- **Importazione/Esportazione di Azure**. Il [servizio Importazione/Esportazione di Azure](/azure/storage/common/storage-import-export-service) consente di trasferire in modo sicuro grandi quantità di dati in Archiviazione BLOB di Azure o File di Azure tramite la spedizione di dischi SATA HDD o SDD a un data center di Azure. È anche possibile usare questo servizio per trasferire i dati da Archiviazione di Azure a unità disco rigido e farsi spedire le unità presso la propria sede per caricarle fisicamente.

- **Azure Data Box**. [Azure Data Box](https://azure.microsoft.com/services/storage/databox/) è un'appliance fornita da Microsoft che funziona in modo analogo al servizio Importazione/Esportazione di Azure. Microsoft spedisce un'appliance proprietaria, sicura e antimanomissione per il trasferimento dei dati e gestisce la logistica dall'inizio alla fine, consentendone il monitoraggio tramite il portale. Uno dei vantaggi del servizio Azure Data Box è dato dalla facilità d'uso. Non è necessario acquistare più unità disco rigido, prepararle e trasferire i file in ciascuna di esse. Azure Data Box è supportato da numerosi partner di Azure con un ruolo leader nel settore per sfruttare con più facilità il trasferimento offline dei dati nel cloud dai loro prodotti. 

## <a name="command-line-tools-and-apis"></a>Strumenti da riga di comando e API

Prendere in considerazione queste opzioni quando si vuole effettuare un trasferimento dei dati a livello di codice e di script.

- **Interfaccia della riga di comando di Azure**. L'[interfaccia della riga di comando di Azure](/azure/hdinsight/hdinsight-upload-data#commandline) è uno strumento multipiattaforma che consente di gestire i servizi di Azure e caricare i dati in Archiviazione di Azure. 

- **AzCopy**. Eseguire AzCopy da una riga di comando di [Windows](/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) o [Linux](/azure/storage/common/storage-use-azcopy-linux?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per copiare facilmente i dati da e verso risorse di archiviazione BLOB, File e Tabelle di Azure con prestazioni ottimali. AzCopy supporta la concorrenza e il parallelismo e consente di riprendere le operazioni di copia in caso di interruzione. È inoltre più veloce rispetto alla maggior parte delle altre opzioni. Per l'accesso a livello di codice, AzCopy è basato sul framework della [libreria per lo spostamento dei dati di Archiviazione di Microsoft Azure](/azure/storage/common/storage-use-data-movement-library). Viene fornito come libreria .NET Core. 

- **PowerShell**. Il [cmdlet `Start-AzureStorageBlobCopy` di PowerShell](/powershell/module/azure.storage/start-azurestorageblobcopy?view=azurermps-5.0.0) è un'opzione per gli amministratori di Windows abituati a usare PowerShell.  

- **AdlCopy**. [AdlCopy](/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) consente di copiare dati dai BLOB del servizio di archiviazione di Azure a Data Lake Store. Può inoltre essere usato per copiare dati tra due account Azure Data Lake Store, ma non da Data Lake Store ai BLOB del servizio di archiviazione.

- **Distcp**. Se è stato creato un cluster HDInsight con accesso a Data Lake Store, è possibile usare gli strumenti dell'ecosistema Hadoop, come [Distcp](/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp), per copiare dati da una risorsa di archiviazione del cluster HDInsight (WASB) in un account Data Lake Store e viceversa.

- **Sqoop**. [Sqoop](/azure/hdinsight/hadoop/hdinsight-use-sqoop) è un progetto di Apache e fa parte dell'ecosistema Hadoop. È preinstallato in tutti i cluster HDInsight. Consente il trasferimento dei dati tra un cluster HDInsight e database relazionali come SQL, Oracle, MySQL e così via. Sqoop è una raccolta di strumenti correlati, inclusi quelli per l'importazione e l'esportazione. Sqoop interagisce con i cluster HDInsight usando una risorsa di archiviazione collegata, come BLOB del servizio di archiviazione di Azure o Data Lake Store.

- **PolyBase**. [PolyBase](/sql/relational-databases/polybase/get-started-with-polybase) è una tecnologia che accede a dati esterni al database tramite il linguaggio T-SQL. In SQL Server 2016 consente di eseguire query su dati esterni in Hadoop oppure di importare o esportare dati da Archiviazione BLOB di Azure. In Azure SQL Data Warehouse è possibile importare o esportare dati da Archiviazione BLOB di Azure e da Azure Data Lake Store. PolyBase è attualmente il metodo più veloce per importare dati in SQL Data Warehouse.

- **Riga di comando di Hadoop**. Quando si hanno dati che si trovano sul nodo head di un cluster HDInsight, è possibile usare il comando `hadoop -copyFromLocal` per copiarli nella risorsa di archiviazione collegata del cluster, ad esempio BLOB del servizio di archiviazione di Azure o Azure Data Lake Store. Per usare il comando di Hadoop, è necessario prima di tutto connettersi al nodo head. Dopo aver stabilito la connessione, sarà possibile caricare un file nella risorsa di archiviazione.

## <a name="graphical-interface"></a>Interfaccia grafica

Prendere in considerazione le opzioni seguenti se si prevede di trasferire solo alcuni file o oggetti dati e non è necessario automatizzare il processo.

- **Azure Storage Explorer**. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) è uno strumento multipiattaforma che consente di gestire il contenuto degli account di archiviazione di Azure. Con questo strumento è possibile caricare, scaricare e gestire BLOB, file, code, tabelle ed entità di Azure Cosmos DB. Usarlo con l'archiviazione BLOB per gestire BLOB e cartelle e anche per caricare e scaricare BLOB tra il file system locale e l'archiviazione BLOB o tra gli account di archiviazione.

- **portale di Azure**. L'archiviazione BLOB e Data Lake Store offrono entrambi un'interfaccia basata sul Web per esplorare i file e caricare nuovi file uno alla volta. Si tratta di un'ottima scelta se non si vogliono installare strumenti o eseguire comandi per esplorare rapidamente i file o caricare contemporaneamente un certo numero di nuovi file.

## <a name="data-pipeline"></a>Data Pipeline

**Azure Data Factory**. [Azure Data Factory](/azure/data-factory/) è un servizio gestito particolarmente adatto per trasferire abitualmente file tra un certo numero di servizi di Azure, il sistema locale o una combinazione dei due. Con Azure Data Factory è possibile creare e pianificare flussi di lavoro basati sui dati, detti pipeline, che inseriscono dati provenienti da archivi diversi. Azure Data Factory può elaborare e trasformare i dati usando servizi di calcolo, ad esempio Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning. Creando flussi di lavoro basati sui dati è possibile [orchestrare](../technology-choices/pipeline-orchestration-data-movement.md) e automatizzare le attività di spostamento e trasformazione dei dati.

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per gli scenari di trasferimento dei dati, rispondere prima di tutto a queste domande per scegliere il sistema adatto alle proprie esigenze:

- È necessario trasferire grandi quantità di dati e il trasferimento attraverso un connessione Internet potrebbe richiedere troppo tempo oppure essere troppo dispendioso o poco affidabile? In caso affermativo, prendere in considerazione un'opzione di trasferimento fisico.

- Si preferisce creare script per le attività di trasferimento dei dati, in modo da poterli riutilizzare? In caso affermativo, scegliere una delle opzioni che prevedono l'uso di una riga di comando o Azure Data Factory.

- È necessario trasferire una grande quantità di dati attraverso una connessione di rete? In questo caso, scegliere un'opzione ottimizzata per i Big Data.

- È necessario trasferire dati da o verso un database relazionale? In caso affermativo, scegliere un'opzione con il supporto per uno o più database relazionali. Si noti che per alcune di queste opzioni è richiesto anche un cluster Hadoop.

- È necessario configurare un'orchestrazione automatizzata di una pipeline di dati o di un flusso di lavoro? In caso affermativo, prendere in considerazione Azure Data Factory.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="physical-transfer"></a>Trasferimento fisico

| | Servizio Importazione/Esportazione di Azure | Azure Data Box |
| --- | --- | --- |
| Fattore di forma | Dischi SATA HDD o SDD interni | Singola appliance hardware, sicura e antimanomissione |
| Logistica della spedizione gestita da Microsoft | No  | Yes |
| Integrazione con prodotti di partner | No  | Yes |
| Appliance personalizzata | No  | Yes |

### <a name="command-line-tools"></a>Strumenti da riga di comando

**Hadoop/HDInsight**

| | Distcp | Sqoop | Riga di comando di Hadoop |
| --- | --- | --- | --- |
| Ottimizzazione per Big Data | Yes | Sì |  Yes |
| Copia in database relazionale |  No  | Yes | No  |
| Copia da database relazionale |  No  | Yes | No  |
| Copia in archiviazione BLOB |  Yes | Sì | Yes |
| Copia da archiviazione BLOB | Yes |  Sì | No  |
| Copia in Data Lake Store | Yes | Sì | Yes |
| Copia da Data Lake Store | Yes | Sì | No  |

**Altri**

| | Interfaccia della riga di comando di Azure | AzCopy | PowerShell | AdlCopy | PolyBase |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Piattaforme compatibili | Linux, OS X, Windows | Linux, Windows | Windows | Linux, OS X, Windows | SQL Server, Azure SQL Data Warehouse | 
| Ottimizzazione per Big Data | No  | No  | No  | Sì <sup>1</sup> | Sì <sup>2</sup> |
| Copia in database relazionale | No  | No  | No  | No  | Yes | 
| Copia da database relazionale | No  | No  | No  | No  | Yes | 
| Copia in archiviazione BLOB | Yes | Sì | Sì | No  | Yes | 
| Copia da archiviazione BLOB | Yes | Sì | Sì | Sì | Yes |
| Copia in Data Lake Store | No  | No  | Yes | Sì |  Yes | 
| Copia da Data Lake Store | No  | No  | Yes | Sì | Yes | 


[1] AdlCopy è ottimizzato per il trasferimento di Big Data se usato con un account Data Lake Analytics.

[2] È possibile [migliorare le prestazioni](/sql/relational-databases/polybase/polybase-guide#performance) di PolyBase effettuando il push delle operazioni di calcolo in Hadoop e usando i [gruppi con scalabilità orizzontale di PolyBase](/sql/relational-databases/polybase/polybase-scale-out-groups) per consentire il trasferimento dei dati parallelo tra le istanze di SQL Server e i nodi di Hadoop.

### <a name="graphical-interface-and-azure-data-factory"></a>Interfaccia grafica e Azure Data Factory

| | Esplora archivi Azure | Portale di Azure * | Data factory di Azure |
| --- | --- | --- | --- |
| Ottimizzazione per Big Data | No  | No  | Yes | 
| Copia in database relazionale | No  | No  | Yes |
| Copia in database relazionale | No  | No  | Yes |
| Copia in archiviazione BLOB | Yes | No  | Yes |
| Copia da archiviazione BLOB | Yes | No  | Yes |
| Copia in Data Lake Store | No  | No  | Yes |
| Copia da Data Lake Store | No  | No  | Yes |
| Caricamento in archiviazione BLOB | Yes | Sì | Yes |
| Caricamento in Data Lake Store | Yes | Sì | Yes |
| Orchestrazione dei trasferimenti di dati | No  | No  | Yes |
| Trasformazioni dei dati personalizzate | No  | No  | Yes |
| Modello di prezzi | Gratuito | Gratuito | Pagamento in base all'utilizzo |

\* In questo contesto l'opzione relativa al portale di Azure si riferisce all'uso degli strumenti di esplorazione basati sul Web per l'archiviazione BLOB e Data Lake Store.

