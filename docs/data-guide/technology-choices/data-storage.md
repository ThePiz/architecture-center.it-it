---
title: Scelta di una tecnologia per l'archiviazione di dati
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: d8f831e758ddc8604758392644a68b56dc51cf57
ms.sourcegitcommit: 475064f0a3c2fac23e1286ba159aaded287eec86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2018
---
# <a name="choosing-a-big-data-storage-technology-in-azure"></a>Scelta di una tecnologia per l'archiviazione di Big Data in Azure

Questo argomento mette a confronto le opzioni di archiviazione disponibili per le soluzioni per Big Data &mdash; in particolare l'inserimento di dati in blocco e l'elaborazione batch, e le tecnologie per gli [archivi dati analitici](./analytical-data-stores.md) o l'[inserimento di streaming in tempo reale](./real-time-ingestion.md).

## <a name="what-are-your-options-when-choosing-data-storage-in-azure"></a>Opzioni disponibili per la scelta di una tecnologia per l'archiviazione di dati in Azure

Sono disponibili diverse opzioni per l'inserimento di dati in Azure, in base alle esigenze specifiche:

**Archiviazione file**

- [BLOB del servizio di archiviazione di Azure](/azure/storage/blobs/storage-blobs-introduction)
- [Archivio Data Lake di Azure](/azure/data-lake-store/)

**Database NoSQL**

- [Azure Cosmos DB](/azure/cosmos-db/)
- [HBase in HDInsight](http://hbase.apache.org/)

## <a name="azure-storage-blobs"></a>BLOB del servizio di archiviazione di Azure

Archiviazione di Azure è un servizio di archiviazione gestito altamente disponibile, sicuro, affidabile, scalabile e ridondante. Microsoft si occupa della manutenzione e gestisce i problemi critici per conto dell'utente. Archiviazione di Azure è la soluzione di archiviazione più diffusa offerta da Azure grazie alla possibilità di integrazione di un numero elevato di servizi e strumenti.

In Archiviazione di Azure sono disponibili vari servizi per archiviare i dati. L'opzione più flessibile per l'archiviazione di BLOB da più origini dati è [Archiviazione BLOB](/azure/storage/blobs/storage-blobs-introduction). I BLOB sono essenzialmente file in cui vengono archiviati dati di qualsiasi tipo, ad esempio immagini, documenti, file HTML, dischi rigidi virtuali, Big Data come log, backup di database. I BLOB vengono archiviati nei contenitori, che sono simili alle cartelle. Un contenitore consente di raggruppare un set di BLOB. Un account di archiviazione può contenere un numero illimitato di contenitori, ciascuno dei quali può archiviare un numero illimitato di BLOB.

Archiviazione di Azure è una scelta ottimale per le soluzioni per l'analisi e i Big Data, grazie alla flessibilità, alla disponibilità elevata e ai costi contenuti. Offre diversi livelli di archiviazione, ad accesso frequente, ad accesso sporadico e archivio, per diversi casi d'uso. Per altre informazioni, vedere [Archivio BLOB di Azure: livelli di archiviazione ad accesso frequente, ad accesso sporadico e archivio](/azure/storage/blobs/storage-blob-storage-tiers).

Archiviazione BLOB di Azure è accessibile da Hadoop (disponibile tramite HDInsight). HDInsight può usare un contenitore BLOB in Archiviazione di Azure come file system predefinito per il cluster. Grazie a un'interfaccia HDFS (Hadoop Distributed File System) fornita da un driver WASB, tutti i componenti disponibili in HDInsight possono agire direttamente sui dati strutturati o non strutturati archiviati come BLOB. Archiviazione BLOB di Azure è accessibile anche tramite Azure SQL Data Warehouse con la funzionalità PolyBase.

Archiviazione di Azure rappresenta un'ottima scelta anche per altre funzionalità, in particolare:

- [Più strategie di concorrenza](/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
- [Opzioni per il ripristino di emergenza e la disponibilità elevata](/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
- [Crittografia di dati inattivi](/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
- [Controllo degli accessi in base al ruolo (RBAC)](/azure/storage/common/storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#management-plane-security) per controllare l'accesso tramite utenti e gruppi di Azure Active Directory.

## <a name="azure-data-lake-store"></a>Archivio Azure Data Lake

[Azure Data Lake Store](/azure/data-lake-store/) è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Data Lake consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione [sicura](/azure/data-lake-store/data-lake-store-overview#DataLakeStoreSecurity) per le analisi esplorative e operative.

Data Lake Store non pone limiti alle dimensioni dell'account e dei file o alla quantità di dati che possono essere archiviati in un data lake. I dati vengono archiviati in modo affidabile creandone più copie e non esiste alcun limite alla durata del tempo in cui i dati possono rimanere archiviati nel data lake. Oltre a creare più copie dei file per evitare eventuali errori imprevisti, Data Lake distribuisce le varie parti di un file su più server di archiviazione singoli. Ciò migliora la velocità effettiva di lettura durante la lettura in parallelo del file per l'esecuzione dell’analisi dei dati.

Data Lake Store è accessibile da Hadoop (disponibile tramite HDInsight) usando le API REST compatibili con WebHDFS. È possibile valutare l'opportunità di usare questa soluzione in alternativa ad Archiviazione di Azure quando le dimensioni dei file, singoli o combinati, superano il limite consentito da Archiviazione di Azure. Tuttavia, quando si usa Data Lake Store come risorsa di archiviazione primaria per un cluster HDInsight, è necessario seguire le [linee guida per l'ottimizzazione delle prestazioni](/azure/data-lake-store/data-lake-store-performance-tuning-guidance#optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight), in particolare per [Spark](/azure/data-lake-store/data-lake-store-performance-tuning-spark), [ Hive](/azure/data-lake-store/data-lake-store-performance-tuning-hive), [MapReduce](/azure/data-lake-store/data-lake-store-performance-tuning-mapreduce) e [Storm](/azure/data-lake-store/data-lake-store-performance-tuning-storm). Per Data Lake Store è inoltre necessario verificare la [disponibilità in base all'area](https://azure.microsoft.com/regions/#services), perché in molte aree non è disponibile come Archiviazione di Azure e deve trovarsi nella stessa area del cluster HDInsight.

Insieme ad Azure Data Lake Analytics, Data Lake Store è stato progettato per consentire l'analisi sui dati archiviati ed è ottimizzato per offrire prestazioni elevate in scenari di analisi dei dati. Data Lake Store è accessibile anche tramite Azure SQL Data Warehouse con la funzionalità PolyBase.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

[Azure Cosmos DB](/azure/cosmos-db/) è il database multimodello di Microsoft distribuito a livello globale. Cosmos DB garantisce latenze di pochi millisecondi al 99° percentile ovunque nel mondo, offre più modelli di coerenza ben definiti per ottimizzare le prestazioni e garantisce la disponibilità elevata con funzionalità di multihosting.

Azure Cosmos DB è completamente indipendente dallo schema. Indicizza automaticamente tutti i dati senza che sia necessario gestire manualmente indici e schemi. È anche un database multimodello e supporta in modalità nativa modelli di dati basati su documenti, coppie chiave-valore, grafi e famiglie di colonne. 

Funzionalità di Azure Cosmos DB:

- [Replica geografica](/azure/cosmos-db/distribute-data-globally)
- [Scalabilità elastica della velocità effettiva e dell'archiviazione](/azure/cosmos-db/partition-data) in tutto il mondo
- [Cinque livelli di coerenza ben definiti](/azure/cosmos-db/consistency-levels)

## <a name="hbase-on-hdinsight"></a>HBase in HDInsight

[Apache HBase](http://hbase.apache.org/) è un database NoSQL open source basato su Hadoop e modellato su Google BigTable. HBase fornisce accesso casuale e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati in un database privo di schema organizzato in base a famiglie di colonne.

I dati sono archiviati nelle righe di una tabella e i dati di ogni riga sono raggruppati in base al tipo di colonna. HBase è un database privo di schema poiché non è necessario definire le colonne o il tipo di dati archiviati nelle colonne prima dell'uso. Il codice open source offre scalabilità lineare, in modo da gestire petabyte di dati in migliaia di nodi. Può contare su ridondanza dei dati, elaborazione batch e altre funzionalità offerte dalle applicazioni distribuite nell'ecosistema di Hadoop.

L'[implementazione di HDInsight](/azure/hdinsight/hbase/apache-hbase-overview) usa l'architettura con scalabilità orizzontale di HBase per automatizzare il partizionamento orizzontale delle tabelle, la coerenza assoluta delle operazioni di lettura e scrittura e il failover automatico. Le prestazioni sono ottimizzate dalla cache in memoria per le operazioni di lettura e da flussi a velocità effettiva elevata per quelle di scrittura. Nella maggior parte dei casi è opportuno [creare il cluster HBase all'interno di una rete virtuale](/azure/hdinsight/hbase/apache-hbase-provision-vnet) per consentire ad altri cluster e applicazioni HDInsight di accedere direttamente alle tabelle.

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- È necessaria una soluzione di archiviazione gestita, ad alta velocità, basata sul cloud per qualsiasi tipo di dati di testo o binari? In caso affermativo, scegliere una delle opzioni di archiviazione di file.

- È necessaria una soluzione di archiviazione di file ottimizzata per carichi di lavoro di analisi paralleli, alta velocità effettiva e numero elevato di operazioni di I/O al secondo? In caso affermativo, scegliere un'opzione ottimizzata per le prestazioni richieste dai carichi di lavoro di analisi.

- È necessario archiviare dati non strutturati o semistrutturati in un database privo di schema? In caso affermativo, scegliere una delle opzioni non relazionali. Mettere a confronto le opzioni per i modelli di indicizzazione e database. A seconda del tipo di dati da archiviare, i modelli di database primario possono offrire la massima capacità.

- È possibile usare il servizio nella propria area? Controllare la disponibilità di ogni servizio di Azure a livello di area. Vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="file-storage-capabilities"></a>Funzionalità per l'archiviazione di file

|  | Archivio Azure Data Lake | Contenitori di Archiviazione BLOB di Azure |
| --- | --- | --- |
| Scopo | Archiviazione ottimizzata per carichi di lavoro di analisi dei Big Data |Archivio di oggetti generico per un'ampia gamma di scenari di archiviazione |
| Casi d'uso | Dati batch, analisi di flusso e di apprendimento automatico come file di log, dati IoT, dati clickstream e set di dati di grandi dimensioni | Qualsiasi tipo di dati di testo o binari, come back-end di applicazioni, dati di backup, archiviazione di supporti per streaming e dati di utilizzo generico |
| Structure | File system gerarchico | Archivio di oggetti con spazio dei nomi flat |
| Authentication | Basata sulle [identità di Azure Active Directory](/azure/active-directory/active-directory-authentication-scenarios) | Basata su segreti condivisi, [chiavi di accesso dell'account](/azure/storage/common/storage-create-storage-account#manage-your-storage-account) e [chiavi di firma di accesso condiviso](/azure/storage/common/storage-dotnet-shared-access-signature-part-1), e [controllo degli accessi in base al ruolo (RBAC)](/azure/security/security-storage-overview) |
| Protocollo di autenticazione | OAuth 2.0. le chiamate devono contenere un token JSON Web (JWT) valido rilasciato da Azure Active Directory | HMAC (Hash-based Message Authentication Code): Le chiamate devono contenere un hash SHA-256 con codifica Base64 su una parte della richiesta HTTP. |
| Authorization | Elenchi di controllo di accesso (ACL) POSIX: gli ACL basati su identità di Azure Active Directory possono essere impostati a livello di file e cartelle | Per l'autorizzazione a livello di account, usare [chiavi di accesso dell'account](/azure/storage/common/storage-create-storage-account#manage-your-storage-account) e per l'autorizzazione relativa ad account, contenitori o BLOB, usare [chiavi di firma di accesso condiviso](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) |
| Controllo | Disponibile.  |Disponibile |
| Crittografia di dati inattivi | Trasparente, lato server | Trasparente, lato server; crittografia lato client |
| SDK per sviluppatori | .NET, Java, Python, Node.js | .Net, Java, Python, Node.js, C++, Ruby |
| Prestazioni per carichi di lavoro di analisi | Prestazioni ottimizzate per carichi di lavoro di analisi paralleli, alta velocità effettiva e numero elevato di operazioni di I/O al secondo | Non è ottimizzato per carichi di lavoro di analisi. |
| Limiti di dimensioni | Nessun limite di dimensioni per l'account, i file o il numero di file | Limiti specifici documentati [qui](/azure/azure-subscription-service-limits#storage-limits) |
| Ridondanza geografica | Archiviazione con ridondanza locale (più copie di dati in un'area di Azure). | Archiviazione con ridondanza locale, archiviazione con ridondanza geografica, archiviazione con ridondanza geografica e accesso in lettura. Per altre informazioni, fare clic [qui](/azure/storage/common/storage-redundancy) . |

### <a name="nosql-database-capabilities"></a>Funzionalità di database NoSQL

| | Azure Cosmos DB | HBase in HDInsight |
| --- | --- | --- |
| Modello di database primario | Archivio a documenti, a grafo, a chiave-valore, a colonne esteso | Archivio a colonne esteso |
| Indici secondari | Sì | No  |
| Supporto per il linguaggio SQL | Sì | Sì (con il driver JDBC [Phoenix](http://phoenix.apache.org/)) |
| Consistency | Assoluta, decadimento ristretto, sessione, coerenza del prefisso, finale | Assoluta |
| Integrazione nativa di Funzioni di Azure | [Sì](/azure/cosmos-db/serverless-computing-database) | No  |
| Distribuzione globale automatica | [Sì](/azure/cosmos-db/distribute-data-globally) | Non [è possibile configurare una replica di cluster HBase](/azure/hdinsight/hbase/apache-hbase-replication) in aree geografiche con coerenza finale |
| Modello di prezzi | Unità richiesta (RU) scalabili in modo elastico addebitate al secondo in base alle esigenze, archiviazione scalabile in modo elastico | Prezzi al minuto per il cluster HDInsight (scalabilità orizzontale dei nodi), archiviazione |
