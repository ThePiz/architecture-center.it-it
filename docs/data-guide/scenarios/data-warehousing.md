---
title: Data warehousing e data mart
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: eec883c68cf94637c3061814d0841c73b58d7e52
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="data-warehousing-and-data-marts"></a>Data warehousing e data mart

Un data warehouse è un repository relazionale centrale con funzione organizzativa contenente i dati integrati da una o più origini diverse in molte o in tutte le aree di interesse. I data warehouse archiviano i dati correnti e quelli cronologici e vengono usati per scopi di report e analisi dei dati in diversi modi.

![Data warehousing in Azure](./images/data-warehousing.png)

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

Servizi correlati:

* [Database SQL di Azure](/azure/sql-database/)
* [SQL Server in una macchina virtuale](/sql/sql-server/sql-server-technical-documentation)
* [Azure Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)
* [Apache Hive in HDInsight](/azure/hdinsight/hadoop/hdinsight-use-hive)
* [Interactive Query (Hive LLAP) in HDInsight](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)


## <a name="technology-choices"></a>Scelte di tecnologia

- [Data warehouse](../technology-choices/data-warehouses.md)
- [Orchestrazione di pipeline](../technology-choices/pipeline-orchestration-data-movement.md)

