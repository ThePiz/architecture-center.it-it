---
title: Business intelligence aziendale automatizzata (BI)
titleSuffix: Azure Reference Architectures
description: Automatizzare un'estrazione, caricamento e trasformazione (ELT - Extract, Load and Transform) di un flusso di lavoro in Azure usando Azure Data Factory con SQL Data Warehouse.
author: MikeWasson
ms.date: 11/06/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: d52d2a323727760463c0b5694b9116e0ed469c93
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243852"
---
# <a name="automated-enterprise-bi-with-sql-data-warehouse-and-azure-data-factory"></a>Business intelligence aziendale automatizzata con SQL Data Warehouse e Azure Data Factory

Questa architettura di riferimento mostra come eseguire il caricamento incrementale in una pipeline [ELT](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) (estrazione-caricamento-trasformazione). Usa Azure Data Factory per automatizzare la pipeline ELT. La pipeline sposta in modo incrementale i dati OLTP più recenti da un database di SQL Server locale in SQL Data Warehouse. I dati transazionali vengono trasformati in un modello tabulare per l'analisi.

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2Gnz2]

<!-- markdownlint-enable MD034 -->

Un'implementazione di riferimento per questa architettura è disponibile in [GitHub][github].

![Diagramma dell'architettura per business intelligence aziendale automatizzata con SQL Data Warehouse e Azure Data Factory](./images/enterprise-bi-sqldw-adf.png)

Questa architettura si basa su quella illustrata in [Business intelligence aziendale con SQL Data Warehouse](./enterprise-bi-sqldw.md), ma aggiunge alcune funzionalità importanti per gli scenari di data warehousing aziendale.

- Automazione della pipeline con Data Factory.
- Caricamento incrementale.
- Integrazione di più origini dati.
- Caricamento di dati binari quali dati geospaziali e immagini.

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

### <a name="data-sources"></a>Origini dati

**SQL Server locale**. I dati di origine si trovano in un database di SQL Server locale. Per simulare l'ambiente locale, gli script di distribuzione per questa architettura eseguono il provisioning di una macchina virtuale in Azure con SQL Server installato. Come database di origine viene usato il [database OLTP di esempio Wide World Importers][wwi].

**Dati esterni**. Uno scenario comune per i data warehouse consiste nell'integrazione di più origini dati. Questa architettura di riferimento carica un set di dati esterno che contiene la popolazione delle città per ogni anno e lo integra con i dati dal database OLTP. È possibile usare questi dati per informazioni dettagliate, come ad esempio: capire se l'incremento delle vendite in ogni area corrisponde o è superiore alla crescita della popolazione.

### <a name="ingestion-and-data-storage"></a>Inserimento e archiviazione dei dati

**Archiviazione BLOB**. L'archiviazione BLOB viene usata come area di staging per i dati di origine prima del caricamento in SQL Data Warehouse.

**Azure SQL Data Warehouse**. [SQL Data Warehouse](/azure/sql-data-warehouse/) è un sistema distribuito progettato per eseguire analisi su dati di grandi dimensioni. Supporta l'elaborazione parallela su larga scala (MPP), che può essere usata per l'esecuzione di analisi ad alte prestazioni.

**Azure Data Factory**. [Data Factory][adf] è un servizio gestito che orchestra e automatizza lo spostamento e la trasformazione dei dati. In questa architettura coordina le diverse fasi del processo ELT.

### <a name="analysis-and-reporting"></a>Analisi e creazione di report

**Azure Analysis Services**. [Analysis Services](/azure/analysis-services/) è un servizio completamente gestito che offre funzionalità di creazione di modelli di dati. Il modello semantico viene caricato in Analysis Services.

**Power BI**. Power BI è una suite di strumenti di analisi aziendale che consente di analizzare dati e condividere informazioni dettagliate. In questa architettura viene eseguita una query del modello semantico archiviato in Analysis Services.

### <a name="authentication"></a>Authentication

**Azure Active Directory** (Azure AD) autentica gli utenti che si connettono al server di Analysis Services tramite Power BI.

Data Factory può usare anche Azure AD per eseguire l'autenticazione in SQL Data Warehouse, tramite un'entità servizio o un'identità del servizio gestita. Per semplificare, la distribuzione di esempio usa l'autenticazione di SQL Server.

## <a name="data-pipeline"></a>Data Pipeline

In [Azure Data Factory][adf], una pipeline è un raggruppamento logico di attività usato per coordinare un'attività, in questo caso il caricamento e la trasformazione di dati in SQL Data Warehouse.

Questa architettura di riferimento definisce una pipeline principale che esegue una sequenza di pipeline figlio. Ogni pipeline figlio carica i dati in una o più tabelle del data warehouse.

![Screenshot della pipeline in Azure Data Factory](./images/adf-pipeline.png)

## <a name="incremental-loading"></a>Caricamento incrementale

Quando si esegue un processo ETL o ELT automatizzato, risulta più efficiente caricare solo i dati modificati dopo l'esecuzione precedente. Questo approccio viene definito *caricamento incrementale*, a differenza di un caricamento completo che carica tutti i dati. Per eseguire un caricamento incrementale, è necessario potere identificare i dati modificati. L'approccio più comune consiste nell'usare un valore di tipo *limite massimo*, ovvero nel tenere traccia del valore più recente di una colonna nella tabella di origine, ad esempio una colonna di tipo data/ora o una colonna con numero intero univoco.

A partire da SQL Server 2016, è possibile usare [tabelle temporali](/sql/relational-databases/tables/temporal-tables). Si tratta di tabelle con controllo delle versioni di sistema che mantengono una cronologia completa delle modifiche dei dati. Il motore di database registra automaticamente la cronologia di ogni modifica in una tabella di cronologia separata. È possibile eseguire query sui dati cronologici aggiungendo una clausola FOR SYSTEM_TIME a una query. Il motore di database esegue internamente query sulla tabella di cronologia, ma questo processo è trasparente per l'applicazione.

> [!NOTE]
> Per le versioni precedenti di SQL Server è possibile usare [Change Data Capture](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) (CDC). Questo approccio risulta meno efficiente rispetto alle tabelle temporali, perché è necessario eseguire query su una tabella di modifiche separata e le modifiche vengono registrate tramite un numero di sequenza di log, invece che un timestamp.
>

Le tabelle temporali sono utili per i dati relativi alle dimensioni, che possono cambiare nel tempo. Le tabelle dei fatti rappresentano una transazione non modificabile, ad esempio una vendita, e in questo caso la conservazione della cronologia delle versioni di sistema risulta superflua. Le transazioni includono invece in genere una colonna che rappresenta la data della transazione, che può essere usata come valore limite. Ad esempio, nel database OLTP "Wide World Importers" le tabelle Sales.Invoices e Sales.InvoiceLines includono un campo `LastEditedWhen` il cui valore predefinito è `sysdatetime()`.

Ecco il flusso generale per la pipeline ELT:

1. Per ogni tabella del database di origine, tenere traccia del valore temporale limite relativo all'esecuzione dell'ultimo processo ELT. Archiviare tali informazioni nel data warehouse. Durante l'installazione iniziale tutti i valori temporali vengono impostati su '1-1-1900'.

2. Durante il passaggio di esportazione dei dati, il valore temporale limite viene passato come parametro a un set di stored procedure nel database di origine. Queste stored procedure eseguono query relative a eventuali record modificati o creati dopo il valore temporale limite. Per la tabella dei fatti Sales viene usata la colonna `LastEditedWhen`. Per i dati relativi alle dimensioni vengono usate tabelle temporali con controllo delle versioni di sistema.

3. Al termine della migrazione dei dati, aggiornare la tabella in cui sono archiviati i valori temporali limite.

È utile registrare anche una *derivazione* per ogni esecuzione di ELT. Per un record specifico, la derivazione associa tale record all'esecuzione di ELT che ha prodotto i dati. Per ogni esecuzione di ETL, viene creato un nuovo record di derivazione per ogni tabella, che illustra l'ora di inizio e di fine del caricamento. Le chiavi di derivazione per ogni record vengono archiviate nelle tabelle delle dimensioni e nelle tabelle dei fatti.

![Screenshot della tabella delle dimensioni delle città](./images/city-dimension-table.png)

Dopo il caricamento di un nuovo batch di dati nel data warehouse, aggiornare il modello tabulare di Analysis Services. Vedere [Aggiornamento asincrono con l'API REST](/azure/analysis-services/analysis-services-async-refresh).

## <a name="data-cleansing"></a>Pulizia dei dati

È consigliabile includere la pulizia dei dati nel processo ELT. In questa architettura di riferimento un'origine di dati non validi è la tabella relativa alla popolazione delle città, in cui alcune città hanno una popolazione pari a zero, probabilmente perché non sono disponibili dati. Durante l'elaborazione, la pipeline ELT rimuove tali città dalla tabella relativa alla popolazione delle città. Eseguire la pulizia dei dati nelle tabelle di staging, invece che nelle tabelle esterne.

Ecco la stored procedure che rimuove le città con popolazione pari a zero dalla tabella relativa alla popolazione delle città. Il file di origine è disponibile [qui](https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/citypopulation/%5BIntegration%5D.%5BMigrateExternalCityPopulationData%5D.sql).

```sql
DELETE FROM [Integration].[CityPopulation_Staging]
WHERE RowNumber in (SELECT DISTINCT RowNumber
FROM [Integration].[CityPopulation_Staging]
WHERE POPULATION = 0
GROUP BY RowNumber
HAVING COUNT(RowNumber) = 4)
```

## <a name="external-data-sources"></a>DROP EXTERNAL DATA SOURCE

I data warehouse eseguono spesso il consolidamento dei dati da più origini. Questa architettura di riferimento carica un'origine dati esterna che include dati demografici. Questo set di dati è disponibile nell'archiviazione BLOB di Azure come parte dell'esempio [WorldWideImportersDW](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/wide-world-importers/sample-scripts/polybase).

Azure Data Factory può copiare direttamente dall'archiviazione BLOB usando il [connettore di archiviazione BLOB](/azure/data-factory/connector-azure-blob-storage). Il connettore richiede tuttavia una stringa di connessione o una firma di accesso condiviso, quindi non può essere usato per copiare un BLOB con accesso in lettura pubblico. In alternativa, è possibile usare PolyBase per creare una tabella esterna rispetto all'archiviazione BLOB e quindi copiare le tabelle esterne in SQL Data Warehouse.

## <a name="handling-large-binary-data"></a>Gestione di dati binari di grandi dimensioni

Nel database di origine la tabella Cities include una colonna Location che contiene un tipo di dati spaziali [geography](/sql/t-sql/spatial-geography/spatial-types-geography). SQL Data Warehouse non supporta il tipo **geography** in modalità nativa, quindi questo campo viene convertito in un tipo **varbinary** durante il caricamento. Vedere [Alternative per i tipi di dati non supportati](/azure/sql-data-warehouse/sql-data-warehouse-tables-data-types#unsupported-data-types).

PolyBase supporta tuttavia dimensioni massime di colonna pari a `varbinary(8000)`, quindi è possibile che alcuni dati vengano troncati. Una soluzione alternativa per questo problema consiste nel suddividere i dati in blocchi durante l'esportazione e quindi riassemblare i blocchi, come illustrato di seguito:

1. Creare una tabella di staging temporanea per la colonna Location.

2. Per ogni città, suddividere i dati relativi alla località in blocchi da 8000 byte, in modo da ottenere 1 &ndash; N righe per ogni città.

3. Per riassemblare i blocchi, usare l'operatore [PIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot) di T-SQL per convertire le righe in colonne e quindi concatenare i valori delle colonne per ogni città.

Ogni città verrà tuttavia suddivisa in un numero diverso di righe, in base alle dimensioni dei dati geografici. Per consentire il funzionamento dell'operatore PIVOT, è necessario che ogni città abbia lo stesso numero di righe. Per il funzionamento di questo approccio, la query T-SQL, disponibile [qui][MergeLocation], esegue alcune operazioni per riempire le righe con valori vuoti, in modo che ogni città abbia lo stesso numero di colonne dopo l'applicazione dell'operatore PIVOT. La query risultante risulta molto più veloce dell'esecuzione di cicli di una riga alla volta.

Lo stesso approccio viene usato per i dati di immagine.

## <a name="slowly-changing-dimensions"></a>Dimensioni a modifica lenta

I dati relativi alle dimensioni sono relativamente statici, ma possono cambiare. È ad esempio possibile che un prodotto venga assegnato a una categoria di prodotto diversa. Sono disponibili diversi approcci per la gestione delle dimensioni a modifica lenta. Una tecnica comune, definita [Tipo 2](https://wikipedia.org/wiki/Slowly_changing_dimension#Type_2:_add_new_row), consiste nell'aggiungere un nuovo record ogni volta che viene modificata una dimensione.

Per implementare l'approccio Tipo 2, è necessario che le tabelle delle dimensioni includano colonne aggiuntive che specificano l'intervallo di date valide per un record specifico. Le chiavi primarie dal database di origine, inoltre, verranno duplicate, quindi la tabella delle dimensioni deve includere una chiave primaria artificiale.

L'immagine seguente mostra la tabella Dimension.City. La colonna `WWI City ID` è la chiave primaria dal database di origine. La colonna `City Key` è una chiave artificiale generata durante la pipeline ETL. Si noti anche che la tabella include le colonne `Valid From` e `Valid To` che definiscono l'intervallo di validità di ogni riga. Per i valori correnti `Valid To` è uguale a '9999-12-31'.

![Screenshot della tabella delle dimensioni delle città](./images/city-dimension-table.png)

Il vantaggio di questo approccio consiste nella conservazione dei dati cronologici, che possono essere utili per l'analisi. Implica tuttavia che saranno presenti più righe per la stessa entità. Ecco ad esempio i record corrispondenti a `WWI City ID` = 28561:

![Secondo screenshot della tabella delle dimensioni delle città](./images/city-dimension-table-2.png)

Per ogni fatto Sales si vuole associare tale fatto a una singola riga nella tabella delle dimensioni City, corrispondente alla data della fattura. Come parte del processo ETL, creare una colonna aggiuntiva che 

La query T-SQL seguente crea una tabella temporanea che associa ogni fattura al valore City Key corretto dalla tabella delle dimensioni City.

```sql
CREATE TABLE CityHolder
WITH (HEAP , DISTRIBUTION = HASH([WWI Invoice ID]))
AS
SELECT DISTINCT s1.[WWI Invoice ID] AS [WWI Invoice ID],
                c.[City Key] AS [City Key]
    FROM [Integration].[Sale_Staging] s1
    CROSS APPLY (
                SELECT TOP 1 [City Key]
                    FROM [Dimension].[City]
                WHERE [WWI City ID] = s1.[WWI City ID]
                    AND s1.[Last Modified When] > [Valid From]
                    AND s1.[Last Modified When] <= [Valid To]
                ORDER BY [Valid From], [City Key] DESC
                ) c

```

Questa tabella viene usata per popolare una colonna nella tabella dei fatti Sales:

```sql
UPDATE [Integration].[Sale_Staging]
SET [Integration].[Sale_Staging].[WWI Customer ID] =  CustomerHolder.[WWI Customer ID]
```

Questa colonna consente a una query di Power BI di trovare il record City corretto per una fattura di vendita specifica.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Per una sicurezza migliore, è possibile usare gli [Endpoint del servizio Rete virtuale](/azure/virtual-network/virtual-network-service-endpoints-overview) per proteggere le risorse dei servizi di Azure in modo che siano limitate solo alla rete virtuale specifica. In questo modo viene rimosso completamente l'accesso Internet pubblico a tali risorse, consentendo il traffico solo dalla rete virtuale specifica.

Con questo approccio è possibile creare una rete virtuale di Azure e quindi creare endpoint di servizio privati per i servizi di Azure. Questi servizi vengono quindi limitati al traffico proveniente da tale rete virtuale. È anche possibile raggiungerli dalla rete locale tramite un gateway.

Tenere presente le limitazioni seguenti:

- Al momento della creazione di questa architettura di riferimento, gli endpoint di servizio di rete virtuale sono supportati per Archiviazione di Azure e Azure SQL Data Warehouse, ma non per Azure Analysis Services. Per verificare lo stato più recente, vedere [qui](https://azure.microsoft.com/updates/?product=virtual-network).

- Se gli endpoint di servizio sono abilitati per Archiviazione di Azure, PolyBase non può copiare i dati da Archiviazione in SQL Data Warehouse. È disponibile una mitigazione per questo problema. Per altre informazioni, vedere [Impatto dell'uso degli endpoint di servizio di rete virtuale con Archiviazione di Azure](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fvirtual-network%2ftoc.json#impact-of-using-vnet-service-endpoints-with-azure-storage).

- Per spostare i dati da locale in Archiviazione di Azure, sarà necessario aggiungere all'elenco elementi consentiti gli indirizzi IP pubblici dall'istanza locale o da ExpressRoute. Per informazioni dettagliate, vedere [Associazione di servizi di Azure a reti virtuali](/azure/virtual-network/virtual-network-service-endpoints-overview#securing-azure-services-to-virtual-networks).

- Per consentire ad Analysis Services di leggere dati da SQL Data Warehouse, distribuire una macchina virtuale Windows nella rete virtuale che contiene l'endpoint di servizio di SQL Data Warehouse. Installare il [Gateway dati locale di Azure](/azure/analysis-services/analysis-services-gateway) in questa VM. Connettere quindi Azure Analysis Services al gateway dati.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Per distribuire ed eseguire l'implementazione di riferimento, seguire la procedura illustrata nel file [README in GitHub][github]. Ecco cosa viene distribuito:

- Una macchina virtuale di Windows per simulare un server di database locale. Include SQL Server 2017 e strumenti correlati, assieme a Power BI Desktop.
- Un account di archiviazione di Azure che fornisce l'archiviazione BLOB per conservare i dati esportati dal database di SQL Server.
- Un'istanza di Azure SQL Data Warehouse.
- Un'istanza di Azure Analysis Services.
- Azure Data Factory e la pipeline di Data Factory per il processo ELT.

## <a name="related-resources"></a>Risorse correlate

Può essere utile esaminare gli [scenari di esempio di Azure](/azure/architecture/example-scenario) seguenti, che illustrano soluzioni specifiche usando alcune delle stesse tecnologie:

- [Data warehousing e analisi per vendite e marketing](/azure/architecture/example-scenario/data/data-warehouse)
- [Processi ETL ibridi con distribuzioni SSIS locali esistenti e Azure Data Factory](/azure/architecture/example-scenario/data/hybrid-etl-with-adf)

<!-- links -->

[adf]: /azure/data-factory
[github]: https://github.com/mspnp/azure-data-factory-sqldw-elt-pipeline
[MergeLocation]: https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/city/%5BIntegration%5D.%5BMergeLocation%5D.sql
[wwi]: /sql/sample/world-wide-importers/wide-world-importers-oltp-database
