---
title: Scelta di un archivio dati analitici
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 166361c73a3a9c812e07445f6b039e843e5e32f8
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902341"
---
# <a name="choosing-an-analytical-data-store-in-azure"></a>Scelta di un archivio dati analitici in Azure

In un'architettura per [Big Data](../big-data/index.md) è spesso necessario un archivio dati analitici in grado di servire dati elaborati in un formato strutturato su cui è possibile eseguire query con strumenti di analisi. Gli archivi dati analitici che supportano l'esecuzione di query per ottenere dati dal percorso ad accesso frequente e da quello ad accesso sporadico sono collettivamente denominati livello di gestione o archiviazione di gestione dati.

Al livello di gestione vengono trattati i dati elaborati ottenuti da entrambi i percorsi. Nell'[architettura lambda](../big-data/index.md#lambda-architecture) il livello di gestione è suddiviso in un livello di _elaborazione rapida_, che archivia i dati elaborati in modo incrementale, e in un livello di _elaborazione batch_, che contiene l'output elaborato in batch. Il livello di gestione richiede un solido supporto per le operazioni di lettura casuali a bassa latenza. L'archiviazione dei dati per il livello di elaborazione rapida deve supportare anche le operazioni di scrittura casuali perché il caricamento dei dati in batch nell'archivio causerebbe ritardi indesiderati. Al contrario, l'archiviazione dei dati per il livello di elaborazione batch non deve supportare le operazioni di scrittura casuali, ma solo quelle in batch.

Non esiste un'unica soluzione di gestione dei dati ottimale per tutte le attività di archiviazione dei dati. Le soluzioni più adatte variano in base alle attività da eseguire. La maggior parte delle app cloud e dei processi su Big Data nel mondo reale presenta un'ampia varietà di requisiti di archiviazione dei dati e spesso sfrutta una combinazione di soluzioni di archiviazione.

## <a name="what-are-your-options-when-choosing-an-analytical-data-store"></a>Opzioni disponibili per la scelta di un archivio dati analitici

Sono disponibili diverse opzioni per l'archiviazione di gestione dati in Azure, in base alle esigenze specifiche:

- [SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
- [Database SQL di Azure](/azure/sql-database/)
- [SQL Server in una macchina virtuale di Azure](/sql/sql-server/sql-server-technical-documentation)
- [HBase/Phoenix in HDInsight](/azure/hdinsight/hbase/apache-hbase-overview)
- [Hive LLAP in HDInsight](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
- [Azure Analysis Services](/azure/analysis-services/analysis-services-overview)
- [Azure Cosmos DB](/azure/cosmos-db/)

Queste opzioni offrono vari modelli di database ottimizzati per diversi tipi di attività:

- I database a [chiave-valore](https://msdn.microsoft.com/library/dn313285.aspx#sec7) contengono un singolo oggetto serializzato per ogni valore di chiave. Sono utili per archiviare grossi volumi di dati quando si vuole ottenere un solo elemento per un determinato valore di chiave e non si devono eseguire query in base ad altre proprietà dell'elemento.
- I database a [documenti](https://msdn.microsoft.com/library/dn313285.aspx#sec8) sono database chiave-valore in cui i valori sono costituiti da *documenti*. In questo contesto, per "documento" si intende una raccolta di valori e campi con nome. I dati del database sono in genere archiviati in un determinato formato, ad esempio XML, YAML, JSON o BSON, ma possono essere presenti anche in formato testo normale. I database a documenti possono eseguire query su campi non chiave e definire indici secondari per rendere più efficiente il processo di query. Sono quindi più adatti per le applicazioni che devono recuperare dati in base a criteri più complessi rispetto al valore della chiave del documento. Sono ad esempio utili per eseguire query sui campi relativi a ID prodotto, ID cliente o nome del cliente.
- I database a [colonne](https://msdn.microsoft.com/library/dn313285.aspx#sec9) sono archivi chiave-valore in cui i dati sono strutturati in raccolte di colonne correlate, dette famiglie di colonne. Ad esempio, un database relativo a un censimento può includere un gruppo di colonne per il nome delle persone (nome e cognome), un gruppo per l'indirizzo e un altro ancora per le informazioni sul profilo (data di nascita e sesso). Il database può archiviare ogni famiglia di colonne in una partizione separata, pur mantenendo tutti i dati di una persona correlati alla stessa chiave. In questo modo, un'applicazione può leggere una singola famiglia di colonne senza dover leggere tutti i dati relativi a un'entità.
- I database a [grafo](https://msdn.microsoft.com/library/dn313285.aspx#sec10) archiviano le informazioni come una raccolta di oggetti e relazioni. Un database a grafo può eseguire in modo efficiente query in grado di attraversare la rete degli oggetti e le relazioni tra di essi. Gli oggetti possono ad esempio essere costituiti da dipendenti all'interno di un database delle risorse umane e può essere necessario eseguire query rapide per cercare tutti i dipendenti che lavorano direttamente o indirettamente per un determinato responsabile.

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- È necessaria una soluzione di archiviazione di gestione che possa essere usata come percorso ad accesso frequente per i dati? In caso affermativo, limitare la scelta alle opzioni ottimizzate per un livello di elaborazione rapida.

- È necessario il supporto per l'elaborazione parallela elevata (MPP, Massively Parallel Processing), in cui le query vengono distribuite automaticamente tra più processi o nodi? In caso affermativo, scegliere un'opzione con il supporto per la scalabilità orizzontale delle query.

- Si preferisce usare un archivio dati relazionale? In caso affermativo, limitare la scelta alle opzioni con un modello di database relazionale. Si noti tuttavia che alcuni archivi non relazionali supportano la sintassi SQL per le query e che è possibile usare strumenti come PolyBase per eseguire query su archivi dati non relazionali.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="general-capabilities"></a>Funzionalità generali

| | Database SQL | SQL Data Warehouse | HBase/Phoenix in HDInsight | Hive LLAP in HDInsight | Azure Analysis Services | Cosmos DB |
| --- | --- | --- | --- | --- | --- | --- |
| Servizio gestito | Yes | Yes | Sì <sup>1</sup> | Sì <sup>1</sup> | Yes | Yes |
| Modello di database primario | Relazionale (formato a colonne quando si usano indici columnstore) | Tabelle relazionali con archiviazione a colonne | Archivio a colonne esteso | Hive/In memoria | Modelli semantici tabulari/MOLAP | Archivio a documenti, a grafo, a chiave-valore, a colonne esteso |
| Supporto per il linguaggio SQL | Yes | Yes | Sì (con driver JDBC [Phoenix](https://phoenix.apache.org/)) | Yes | No  | Yes |
| Ottimizzato per un livello di elaborazione rapida | Sì <sup>2</sup> | No  | Yes | Sì | No  | Yes |

[1] Con configurazione e scalabilità manuali.

[2] Con tabelle ottimizzate per la memoria e indici hash o non cluster.
 
### <a name="scalability-capabilities"></a>Funzionalità di scalabilità

|                                                  | Database SQL | SQL Data Warehouse | HBase/Phoenix in HDInsight | Hive LLAP in HDInsight | Azure Analysis Services | Cosmos DB |
|--------------------------------------------------|--------------|--------------------|----------------------------|------------------------|-------------------------|-----------|
| Server regionali ridondanti per disponibilità elevata |     Yes      |        Sì         |            Sì             |           No            |           No             |    Yes    |
|             Supporto per la scalabilità orizzontale delle query             |      No       |        Yes         |            Sì             |          Sì           |           Sì           |    Yes    |
|          Scalabilità dinamica (aumento delle prestazioni)          |     Yes      |        Sì         |             No              |           No            |           Yes           |    Yes    |
|        Supporto per la memorizzazione nella cache dei dati in memoria        |     Yes      |        Sì         |             No              |          Yes           |           Sì           |    No      |

### <a name="security-capabilities"></a>Funzionalità di sicurezza

| | Database SQL | SQL Data Warehouse | HBase/Phoenix in HDInsight | Hive LLAP in HDInsight | Azure Analysis Services | Cosmos DB |
| --- | --- | --- | --- | --- | --- | --- |
| Authentication  | SQL/Azure Active Directory (Azure AD) | SQL/Azure AD | locale/Azure AD <sup>1</sup> | locale/Azure AD <sup>1</sup> | Azure AD | Utenti database/Azure AD tramite controllo di accesso (IAM) |
| Crittografia di dati inattivi | Sì <sup>2</sup> | Sì <sup>2</sup> | Sì <sup>1</sup> | Sì <sup>1</sup> | Yes | Yes |
| Sicurezza a livello di riga | Yes | No  | Sì <sup>1</sup> | Sì <sup>1</sup> | Sì (tramite la sicurezza a livello di oggetto nel modello) | No  |
| Supporto dei firewall | Yes | Yes | Sì <sup>3</sup> | Sì <sup>3</sup> | Yes | Yes |
| Maschera dati dinamica | Yes | No  | Sì <sup>1</sup> | Sì * | No  | No  |

[1] Richiede l'uso di un [cluster HDInsight aggiunto al dominio](/azure/hdinsight/domain-joined/apache-domain-joined-introduction).

[2] Richiede l'uso della crittografia TDE (Transparent Data Encryption) per crittografare e decrittografare i dati inattivi.

[3] Quando è usato all'interno di una rete virtuale di Azure. Vedere [Estendere Azure HDInsight usando Rete virtuale di Azure](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network).
