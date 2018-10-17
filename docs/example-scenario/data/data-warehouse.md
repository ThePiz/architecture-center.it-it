---
title: Data warehousing e analitica per vendite e marketing
description: Consolidare i dati da più origini e ottimizzare le analisi dei dati.
author: alexbuckgit
ms.date: 09/15/2018
ms.openlocfilehash: f9ca9785b65f18098a91aedc1f3157f49456a6e1
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818650"
---
# <a name="data-warehousing-and-analytics-for-sales-and-marketing"></a>Data warehousing e analitica per vendite e marketing

Questo scenario di esempio illustra una pipeline di dati che integra grandi quantità di dati da più origini in una piattaforma di analisi unificata in Azure. Questo scenario specifico è basato su una soluzione di vendite e marketing, ma gli schemi progettuali sono pertinenti a diversi settori che richiedono l'analisi avanzata di grandi set di dati, ad esempio e-commerce, vendite al dettaglio e settore sanitario.

Questo esempio illustra una società di vendite e marketing che crea programmi di incentivazione. Questi programmi premiano clienti, fornitori, venditori e dipendenti. I dati sono fondamentali per questi programmi e la società intende migliorare le informazioni dettagliate ottenute tramite l'analisi dei dati in Azure.

La società vuole adottare un approccio moderno ai dati di analisi, in modo che le decisioni vengano prese usando i dati corretti al momento giusto. Gli obiettivi dell'azienda includono:
* Combinazione di diversi tipi di origini dati in una piattaforma di livello cloud.
* Trasformazione dei dati di origine in una tassonomia e una struttura comuni, per rendere i dati coerenti e facilmente confrontabili.
* Caricamento dei dati basato su un approccio altamente parallelizzato in grado di supportare migliaia di programmi di incentivazione, senza i costi elevati legati alla distribuzione e alla gestione dell'infrastruttura locale.
* Riduzione significativa del tempo necessario per raccogliere e trasformare i dati, per potersi concentrare sull'analisi dei dati.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

È anche possibile usare questo approccio per eseguire le operazioni seguenti:

* Definire un data warehouse come singola origine di dati reali.
* Integrare le origini dati relazionali con altri set di dati non strutturati.
* Usare la modellazione semantica e strumenti di visualizzazione avanzati per semplificare l'analisi dei dati.

## <a name="architecture"></a>Architettura

![Architettura per uno scenario di data warehousing e analisi dei dati in Azure][architecture]

Il flusso dei dati attraverso la soluzione avviene come segue:

1. Per ogni origine dati, tutti gli aggiornamenti vengono esportati periodicamente in un'area di staging nell'archivio BLOB di Azure.
2. Data Factory carica i dati in modo incrementale dall'archivio BLOB alle tabelle di staging in SQL Data Warehouse. I dati vengono puliti e trasformati durante questo processo. Polybase può parallelizzare il processo per grandi set di dati.
3. Dopo aver caricato un nuovo batch di dati nel data warehouse, viene aggiornato un modello tabulare di Analysis Services creato in precedenza. Questo modello semantico semplifica l'analisi dei dati e delle relazioni aziendali.
4. I business analyst usano Microsoft Power BI per analizzare i dati del data warehouse tramite il modello semantico di Analysis Services.

### <a name="components"></a>Componenti

La società ha origini dati in diverse piattaforme:
* SQL Server in locale
* Oracle in locale
* database SQL di Azure
* Archiviazione tabelle di Azure
* Cosmos DB

I dati vengono caricati da queste origini dati diverse usando numerosi componenti di Azure:
* L'[archivio BLOB](/azure/storage/blobs/storage-blobs-introduction) viene usato per gestire temporaneamente i dati di origine prima che vengano caricati in SQL Data Warehouse.
* [Data Factory](/azure/data-factory) orchestra la trasformazione dei dati di gestione temporanea in una struttura comune in SQL Data Warehouse. Data Factory [usa Polybase quando si caricano i dati in SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse#use-polybase-to-load-data-into-azure-sql-data-warehouse) per ottimizzare la velocità effettiva. 
* [SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) è un sistema distribuito per l'archiviazione e l'analisi di set di dati di dimensioni elevate. L'uso dell'elaborazione parallela elevata (MPP, Massively Parallel Processing) lo rende appropriato per l'esecuzione di analisi ad alte prestazioni. SQL Data Warehouse può usare [PolyBase](/sql/relational-databases/polybase/polybase-guide) per caricare rapidamente dati dall'archivio BLOB.
* [Analysis Services](/azure/analysis-services) offre un modello semantico per i dati. Può anche aumentare le prestazioni del sistema durante l'analisi dei dati. 
* [Power BI](/power-bi) è una suite di strumenti di analisi business che consente di analizzare i dati e condividere informazioni dettagliate. Power BI può eseguire una query su un modello semantico archiviato in Analysis Services oppure può eseguire direttamente una query in SQL Data Warehouse.
* [Azure Active Directory](/azure/active-directory) (Azure AD) autentica gli utenti che si connettono al server di Analysis Services tramite Power BI. Data Factory può usare anche Azure AD per eseguire l'autenticazione in SQL Data Warehouse tramite un'entità servizio o un'[identità gestita per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview).

### <a name="alternatives"></a>Alternative

* La pipeline di esempio include diversi tipi di origini dati. Questa architettura può gestire un'ampia gamma di origini dati relazionali e non relazionali.
* Data Factory orchestra i flussi di lavoro per la pipeline di dati. Per caricare i dati solo una volta o su richiesta, è possibile usare strumenti come la copia bulk di SQL Server (bcp) e AzCopy per copiare i dati nell'archivio BLOB. È quindi possibile caricare i dati direttamente in SQL Data Warehouse usando Polybase.
* Se sono presenti set di dati di dimensioni molto grandi, valutare la possibilità di usare [Data Lake Storage](/azure/storage/data-lake-storage/introduction), che offre spazio di archiviazione senza limiti per i dati di analisi.
* Per l'elaborazione di Big Data, è anche possibile usare un'appliance di [SQL Server Parallel Data Warehouse](/sql/analytics-platform-system) locale. Tuttavia, i costi operativi sono in genere molto più bassi con una soluzione gestita basata sul cloud come SQL Data Warehouse. 
* SQL Data Warehouse non è una scelta ottimale per carichi di lavoro OLTP o set di dati più piccoli di 250 GB. In questi casi è consigliabile usare il database SQL di Azure o SQL Server.
* Per confrontare le alternative, vedere:
    * [Scelta di una tecnologia di orchestrazione di una pipeline di dati in Azure](/azure/architecture/data-guide/technology-choices/pipeline-orchestration-data-movement)
    * [Scelta di una tecnologia per l'elaborazione batch in Azure](/azure/architecture/data-guide/technology-choices/batch-processing)
    * [Scelta di un archivio dati analitici in Azure](/azure/architecture/data-guide/technology-choices/analytical-data-stores)
    * [Scelta di una tecnologia per l'analisi dei dati in Azure](/azure/architecture/data-guide/technology-choices/analysis-visualizations-reporting)

## <a name="considerations"></a>Considerazioni

Le tecnologie di questa architettura sono state scelte perché soddisfano i requisiti aziendali di scalabilità e disponibilità, oltre a consentire di controllare i costi.

* L'[architettura di elaborazione parallela elevata](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture) (MPP, Massively Parallel Processing) di SQL Data Warehouse assicura scalabilità e prestazioni elevate.
* SQL Data Warehouse offre [contratti di servizio garantiti](https://azure.microsoft.com/support/legal/sla/sql-data-warehouse) e [procedure consigliate per la disponibilità elevata](/azure/sql-data-warehouse/sql-data-warehouse-best-practices).
* Quando l'attività di analisi è scarsa, l'azienda può ridimensionare [SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview) su richiesta, riducendo o persino sospendendo il calcolo per ridurre i costi.
* È possibile [aumentare il numero di istanze](/azure/analysis-services/analysis-services-scale-out) di Azure Analysis Services per ridurre i tempi di risposta quando i carichi di lavoro delle query sono elevati. È anche possibile separare l'elaborazione dal pool di query, in modo che le prestazioni delle query dei client non vengano rallentate dalle operazioni di elaborazione. 
* Azure Analysis Services offre anche [contratti di servizio garantiti](https://azure.microsoft.com/support/legal/sla/analysis-services) e [procedure consigliate per la disponibilità elevata](/azure/analysis-services/analysis-services-bcdr).
* Il [modello di protezione di SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-manage-security) assicura la sicurezza della connessione, [l'autenticazione e l'autorizzazione](/azure/sql-data-warehouse/sql-data-warehouse-authentication) tramite Azure AD o l'autenticazione di SQL Server, oltre alla crittografia. [Azure Analysis Services](/azure/analysis-services/analysis-services-manage-users) usa Azure AD per la gestione delle identità e l'autenticazione degli utenti. 

## <a name="pricing"></a>Prezzi

Esaminare un [esempio del costo di uno scenario di data warehousing][calculator] tramite il calcolatore prezzi di Azure. Modificare i valori per verificare l'effetto delle specifiche esigenze sui costi.

* [SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2) consente di ridimensionare in modo indipendente i livelli di calcolo e archiviazione. Le risorse di calcolo vengono addebitate su base oraria ed è possibile ridimensionare o sospendere queste risorse su richiesta. Le risorse di archiviazione vengono addebitate per terabyte, pertanto i costi aumenteranno man mano che si inseriscono altri dati.
* I costi di [Data Factory](https://azure.microsoft.com/pricing/details/data-factory) dipendono dal numero di operazioni di lettura/scrittura, di operazioni di monitoraggio e di attività di orchestrazione eseguite in un carico di lavoro. I costi di Data Factory aumenteranno per ogni flusso dei dati aggiuntivo e in base alla quantità di dati elaborati da ogni flusso.
* [Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services) è disponibile nei livelli Developer, Basic e Standard. Le istanze vengono addebitate in base alle unità di elaborazione di query (QPU) e alla memoria disponibile. Per contenere i costi, ridurre al minimo il numero di query eseguite, la quantità di dati elaborata e la frequenza di esecuzione.
* [Power BI](https://powerbi.microsoft.com/pricing) offre diverse opzioni di prodotto per i differenti requisiti. [Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded) fornisce un'opzione basata su Azure per incorporare le funzionalità di Power BI all'interno delle applicazioni. Un'istanza di Power BI Embedded è inclusa nell'esempio di costi riportato in precedenza.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare l'[architettura di riferimento di Azure per la business intelligence aziendale automatizzata](/azure/architecture/reference-architectures/data/enterprise-bi-adf), che include le istruzioni per distribuire un'istanza di questa architettura in Azure.
* Leggere il [caso di successo del cliente Maritz Motivation Solutions][source-document]. Tale caso descrive un approccio simile per la gestione dei dati dei clienti.
* Nella [Guida all'architettura dei dati di Azure](/azure/architecture/data-guide) sono disponibili indicazioni sull'architettura per pipeline di dati, data warehousing, Online Analytical Processing (OLAP) e Big Data.

<!-- links -->
[source-document]: https://customers.microsoft.com/story/maritz
[calculator]: https://azure.com/e/b798fb70c53e4dd19fdeacea4db78276
[architecture]: ./media/architecture-data-warehouse.png
