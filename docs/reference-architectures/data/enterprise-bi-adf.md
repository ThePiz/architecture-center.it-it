---
title: Business intelligence aziendale automatizzata con SQL Data Warehouse e Azure Data Factory
description: Automatizzare un flusso di lavoro ELT in Azure con Azure Data Factory
author: MikeWasson
ms.date: 07/01/2018
ms.openlocfilehash: ffd75ba8c57a9afbc6abad61f21f738c644c9bc8
ms.sourcegitcommit: 58d93e7ac9a6d44d5668a187a6827d7cd4f5a34d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37142282"
---
# <a name="automated-enterprise-bi-with-sql-data-warehouse-and-azure-data-factory"></a>Business intelligence aziendale automatizzata con SQL Data Warehouse e Azure Data Factory

Questa architettura di riferimento mostra come eseguire il caricamento incrementale in una pipeline [ELT](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) (estrazione-caricamento-trasformazione). Usa Azure Data Factory per automatizzare la pipeline ELT. La pipeline sposta in modo incrementale i dati OLTP più recenti da un database di SQL Server locale in SQL Data Warehouse. I dati transazionali vengono trasformati in un modello tabulare per l'analisi. [**Distribuire questa soluzione**.](#deploy-the-solution)

![](./images/enterprise-bi-sqldw-adf.png)

Questa architettura si basa su quella illustrata in [Business intelligence aziendale con SQL Data Warehouse](./enterprise-bi-sqldw.md), ma aggiunge alcune funzionalità importanti per gli scenari di data warehousing aziendale.

-   Automazione della pipeline con Data Factory.
-   Caricamento incrementale.
-   Integrazione di più origini dati.
-   Caricamento di dati binari quali dati geospaziali e immagini.

## <a name="architecture"></a>Architecture

L'architettura è costituita dai componenti seguenti.

### <a name="data-sources"></a>Origini dati

**SQL Server locale**. I dati di origine si trovano in un database di SQL Server locale. Per simulare l'ambiente locale, gli script di distribuzione per questa architettura eseguono il provisioning di una macchina virtuale in Azure con SQL Server installato. Il [database OLTP di esempio "Wide World Importers"][wwi] viene usato come database di origine.

**Dati esterni**. Uno scenario comune per i data warehouse consiste nell'integrazione di più origini dati. Questa architettura di riferimento carica un set di dati esterno che contiene la popolazione delle città per ogni anno e lo integra con i dati dal database OLTP. È possibile usare questi dati per ottenere informazioni dettagliate utili per capire ad esempio se l'incremento delle vendite in ogni area corrisponde o è superiore alla crescita della popolazione.

### <a name="ingestion-and-data-storage"></a>Inserimento e archiviazione dei dati

**Archiviazione BLOB**. L'archiviazione BLOB viene usata come area di staging per i dati di origine prima del caricamento in SQL Data Warehouse.

**Azure SQL Data Warehouse**. [SQL Data Warehouse](/azure/sql-data-warehouse/) è un sistema distribuito progettato per eseguire analisi su dati di grandi dimensioni. Supporta l'elaborazione parallela su larga scala (MPP), che può essere usata per l'esecuzione di analisi ad alte prestazioni. 

**Azure Data Factory**. [Data Factory][adf] è un servizio gestito che esegue l'orchestrazione e l'automazione dello spostamento dati e della trasformazione dei dati. In questa architettura coordina le diverse fasi del processo ELT.

### <a name="analysis-and-reporting"></a>Analisi e creazione di report

**Azure Analysis Services**. [Analysis Services](/azure/analysis-services/) è un servizio completamente gestito che offre funzionalità di creazione di modelli di dati. Il modello semantico viene caricato in Analysis Services.

**Power BI**. Power BI è una suite di strumenti di analisi aziendale che consente di analizzare dati e condividere informazioni dettagliate. In questa architettura viene eseguita una query del modello semantico archiviato in Analysis Services.

### <a name="authentication"></a>Authentication

**Azure Active Directory** (Azure AD) autentica gli utenti che si connettono al server di Analysis Services tramite Power BI.

Data Factory può usare anche Azure AD per eseguire l'autenticazione in SQL Data Warehouse, tramite un'entità servizio o un'identità del servizio gestita. Per semplificare, la distribuzione di esempio usa l'autenticazione di SQL Server.

## <a name="data-pipeline"></a>Data Pipeline

In [Azure Data Factory][adf] una pipeline è un raggruppamento logico di attività usato per coordinare un'attività &mdash; in questo caso il caricamento e la trasformazione di dati in SQL Data Warehouse. 

Questa architettura di riferimento definisce una pipeline principale che esegue una sequenza di pipeline figlio. Ogni pipeline figlio carica i dati in una o più tabelle del data warehouse.

![](./images/adf-pipeline.png)

## <a name="incremental-loading"></a>Caricamento incrementale

Quando si esegue un processo ETL o ELT automatizzato, risulta più efficiente caricare solo i dati modificati dopo l'esecuzione precedente. Questo approccio viene definito *caricamento incrementale*, a differenza di un caricamento completo che carica tutti i dati. Per eseguire un caricamento incrementale, è necessario potere identificare i dati modificati. L'approccio più comune consiste nell'usare un valore di tipo *limite massimo*, ovvero nel tenere traccia del valore più recente di una colonna nella tabella di origine, ad esempio una colonna di tipo data/ora o una colonna con numero intero univoco. 

A partire da SQL Server 2016, è possibile usare [tabelle temporali](/sql/relational-databases/tables/temporal-tables). Si tratta di tabelle con controllo delle versioni di sistema che mantengono una cronologia completa delle modifiche dei dati. Il motore di database registra automaticamente la cronologia di ogni modifica in una tabella di cronologia separata. È possibile eseguire query sui dati cronologici aggiungendo una clausola FOR SYSTEM_TIME a una query. Il motore di database esegue internamente query sulla tabella di cronologia, ma questo processo è trasparente per l'applicazione. 

> [!NOTE]
> Per le versioni precedenti di SQL Server è possibile usare [Change Data Capture](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) (CDC). Questo approccio risulta meno efficiente rispetto alle tabelle temporali, perché è necessario eseguire query su una tabella di modifiche separata e le modifiche vengono registrate tramite un numero di sequenza di log, invece che un timestamp. 

Le tabelle temporali sono utili per i dati relativi alle dimensioni, che possono cambiare nel tempo. Le tabelle dei fatti rappresentano una transazione non modificabile, ad esempio una vendita, e in questo caso la conservazione della cronologia delle versioni di sistema risulta superflua. Le transazioni includono invece in genere una colonna che rappresenta la data della transazione, che può essere usata come valore limite. Ad esempio, nel database OLTP "Wide World Importers" le tabelle Sales.Invoices e Sales.InvoiceLines includono un campo `LastEditedWhen` il cui valore predefinito è `sysdatetime()`. 

Ecco il flusso generale per la pipeline ELT:

1. Per ogni tabella del database di origine, tenere traccia del valore temporale limite relativo all'esecuzione dell'ultimo processo ELT. Archiviare tali informazioni nel data warehouse. Durante l'installazione iniziale tutti i valori temporali vengono impostati su '1-1-1900'.

2. Durante il passaggio di esportazione dei dati, il valore temporale limite viene passato come parametro a un set di stored procedure nel database di origine. Queste stored procedure eseguono query relative a eventuali record modificati o creati dopo il valore temporale limite. Per la tabella dei fatti Sales viene usata la colonna `LastEditedWhen`. Per i dati relativi alle dimensioni vengono usate tabelle temporali con controllo delle versioni di sistema.

3. Al termine della migrazione dei dati, aggiornare la tabella in cui sono archiviati i valori temporali limite.

È utile registrare anche una *derivazione* per ogni esecuzione di ELT. Per un record specifico, la derivazione associa tale record all'esecuzione di ELT che ha prodotto i dati. Per ogni esecuzione di ETL, viene creato un nuovo record di derivazione per ogni tabella, che illustra l'ora di inizio e di fine del caricamento. Le chiavi di derivazione per ogni record vengono archiviate nelle tabelle delle dimensioni e nelle tabelle dei fatti.

![](./images/city-dimension-table.png)

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

Ogni città verrà tuttavia suddivisa in un numero diverso di righe, in base alle dimensioni dei dati geografici. Per consentire il funzionamento dell'operatore PIVOT, è necessario che ogni città abbia lo stesso numero di righe. Per il funzionamento corretto di questo approccio, la query T-SQL, disponibile [qui][MergeLocation], esegue alcune operazioni per riempire le righe con valori vuoti, in modo che ogni città abbia lo stesso numero di colonne dopo l'applicazione dell'operatore PIVOT. La query risultante risulta molto più veloce dell'esecuzione di cicli di una riga alla volta.

Lo stesso approccio viene usato per i dati di immagine.

## <a name="slowly-changing-dimensions"></a>Dimensioni a modifica lenta

I dati relativi alle dimensioni sono relativamente statici, ma possono cambiare. È ad esempio possibile che un prodotto venga assegnato a una categoria di prodotto diversa. Sono disponibili diversi approcci per la gestione delle dimensioni a modifica lenta. Una tecnica comune, definita [Tipo 2](https://wikipedia.org/wiki/Slowly_changing_dimension#Type_2:_add_new_row), consiste nell'aggiungere un nuovo record ogni volta che viene modificata una dimensione. 

Per implementare l'approccio Tipo 2, è necessario che le tabelle delle dimensioni includano colonne aggiuntive che specificano l'intervallo di date valide per un record specifico. Le chiavi primarie dal database di origine, inoltre, verranno duplicate, quindi la tabella delle dimensioni deve includere una chiave primaria artificiale.

L'immagine seguente mostra la tabella Dimension.City. La colonna `WWI City ID` è la chiave primaria dal database di origine. La colonna `City Key` è una chiave artificiale generata durante la pipeline ETL. Si noti anche che la tabella include le colonne `Valid From` e `Valid To` che definiscono l'intervallo di validità di ogni riga. Per i valori correnti `Valid To` è uguale a '9999-12-31'.

![](./images/city-dimension-table.png)

Il vantaggio di questo approccio consiste nella conservazione dei dati cronologici, che possono essere utili per l'analisi. Implica tuttavia che saranno presenti più righe per la stessa entità. Ecco ad esempio i record corrispondenti a `WWI City ID` = 28561:

![](./images/city-dimension-table-2.png)

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

Una distribuzione di questa architettura di riferimento è disponibile in [GitHub][ref-arch-repo-folder]. Ecco cosa viene distribuito:

  * Una macchina virtuale di Windows per simulare un server di database locale. Include SQL Server 2017 e strumenti correlati, assieme a Power BI Desktop.
  * Un account di archiviazione di Azure che fornisce l'archiviazione BLOB per conservare i dati esportati dal database di SQL Server.
  * Un'istanza di Azure SQL Data Warehouse.
  * Un'istanza di Azure Analysis Services.
  * Azure Data Factory e la pipeline di Data Factory per il processo ELT.

### <a name="prerequisites"></a>prerequisiti

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="variables"></a>variables

I passaggi seguenti includono alcune variabili definite dall'utente. Potrebbe essere necessario sostituire tali variabili con valori specifici.

- `<data_factory_name>`. Nome dell'istanza di Data Factory.
- `<analysis_server_name>`. Nome del server di Analysis Services.
- `<active_directory_upn>`. Nome dell'entità utente di Azure Active Directory. Ad esempio, `user@contoso.com`.
- `<data_warehouse_server_name>`. Nome del server di SQL Data Warehouse.
- `<data_warehouse_password>`. Password dell'amministratore di SQL Data Warehouse.
- `<resource_group_name>`. Nome del gruppo di risorse.
- `<region>`. Area di Azure in cui verranno distribuite le risorse.
- `<storage_account_name>`. Nome dell'account di archiviazione. Deve seguire le [regole di denominazione](../../best-practices/naming-conventions.md#naming-rules-and-restrictions) per gli account di archiviazione.
- `<sql-db-password>`. Password di accesso a SQL Server.

### <a name="deploy-azure-data-factory"></a>Distribuire Azure Data Factory

1. Passare alla cartella `data\enterprise_bi_sqldw_advanced\azure\templates` del [repository GitHub][ref-arch-repo].

2. Eseguire il comando dell'interfaccia della riga di comando di Azure per creare un gruppo di risorse.  

    ```bash
    az group create --name <resource_group_name> --location <region>  
    ```

    Specificare un'area che supporta SQL Data Warehouse, Azure Analysis Services e Data Factory v2. Vedere [Prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/).

3. Eseguire il comando seguente

    ```
    az group deployment create --resource-group <resource_group_name> \
        --template-file adf-create-deploy.json \
        --parameters factoryName=<data_factory_name> location=<location>
    ```

Usare quindi il portale di Azure per ottenere la chiave di autenticazione per il [runtime di integrazione](/azure/data-factory/concepts-integration-runtime) di Azure Data Factory, come indicato di seguito:

1. Nel [portale di Azure](https://portal.azure.com/) passare all'istanza di Data Factory.

2. Nel pannello Data Factory fare clic su **Crea e monitora**. Il portale di Azure Data Factory verrà aperto in un'altra finestra del browser.

    ![](./images/adf-blade.png)

3. Nel portale di Azure Data Factory selezionare l'icona a forma di matita ("Crea"). 

4. Fare clic su **Connessioni**, e quindi selezionare **Integration Runtimes** (Runtime di integrazione).

5. In **sourceIntegrationRuntime** fare clic sull'icona a forma di matita ("Modifica").

    > [!NOTE]
    > Lo stato del portale sarà "non disponibile". Questo comportamento è previsto fino alla distribuzione del server locale.

6. Trovare il valore **Key1** e copiare il valore della chiave di autenticazione.

La chiave di autenticazione sarà necessaria per il passaggio successivo.

### <a name="deploy-the-simulated-on-premises-server"></a>Distribuire il server locale simulato

Questo passaggio distribuisce una macchina virtuale come server locale simulato, che include SQL Server 2017 e strumenti correlati. Carica anche il [database OLTP "Wide World Importers"][wwi] in SQL Server.

1. Passare alla cartella `data\enterprise_bi_sqldw_advanced\onprem\templates` del repository.

2. Nel file `onprem.parameters.json` cercare `adminPassword`. Questa è la password per accedere alla macchina virtuale di SQL Server. Sostituire il valore con un'altra password.

3. Nello stesso file cercare `SqlUserCredentials`. Questa proprietà specifica le credenziali dell'account SQL Server. Sostituire la password con un valore diverso.

4. Nello stesso file incollare la chiave di autenticazione di Integration Runtime nel parametro `IntegrationRuntimeGatewayKey`, come indicato di seguito:

    ```json
    "protectedSettings": {
        "configurationArguments": {
            "SqlUserCredentials": {
                "userName": ".\\adminUser",
                "password": "<sql-db-password>"
            },
            "IntegrationRuntimeGatewayKey": "<authentication key>"
        }
    ```

5. Eseguire il comando indicato di seguito.

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p onprem.parameters.json --deploy
    ```

Il completamento di questo passaggio potrebbe richiedere tra 20 e 30 minuti. Include l'esecuzione di uno script di [DSC](/powershell/dsc/overview) per l'installazione degli strumenti e il ripristino del database. 

### <a name="deploy-azure-resources"></a>Distribuire le risorse di Azure

Questo passaggio effettua il provisioning di SQL Data Warehouse, Azure Analysis Services e Data Factory.

1. Passare alla cartella `data\enterprise_bi_sqldw_advanced\azure\templates` del [repository GitHub][ref-arch-repo].

2. Eseguire il comando dell'interfaccia della riga di comando di Azure seguente: Sostituire i valori dei parametri visualizzati tra parentesi acute.

    ```bash
    az group deployment create --resource-group <resource_group_name> \
     --template-file azure-resources-deploy.json \
     --parameters "dwServerName"="<data_warehouse_server_name>" \
     "dwAdminLogin"="adminuser" "dwAdminPassword"="<data_warehouse_password>" \ 
     "storageAccountName"="<storage_account_name>" \
     "analysisServerName"="<analysis_server_name>" \
     "analysisServerAdmin"="<user@contoso.com>"
    ```

    - Il parametro `storageAccountName` deve seguire le [regole di denominazione](../../best-practices/naming-conventions.md#naming-rules-and-restrictions) per gli account di archiviazione. 
    - Per il parametro `analysisServerAdmin`, usare il nome dell'entità utente (UPN) di Azure Active Directory.

3. Eseguire il comando seguente dell'interfaccia della riga di comando di Azure per ottenere la chiave di accesso per l'account di archiviazione. La chiave verrà usata nel passaggio successivo.

    ```bash
    az storage account keys list -n <storage_account_name> -g <resource_group_name> --query [0].value
    ```

4. Eseguire il comando dell'interfaccia della riga di comando di Azure seguente: Sostituire i valori dei parametri visualizzati tra parentesi acute. 

    ```bash
    az group deployment create --resource-group <resource_group_name> \
    --template-file adf-pipeline-deploy.json \
    --parameters "factoryName"="<data_factory_name>" \
    "sinkDWConnectionString"="Server=tcp:<data_warehouse_server_name>.database.windows.net,1433;Initial Catalog=wwi;Persist Security Info=False;User ID=adminuser;Password=<data_warehouse_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" \
    "blobConnectionString"="DefaultEndpointsProtocol=https;AccountName=<storage_account_name>;AccountKey=<storage_account_key>;EndpointSuffix=core.windows.net" \
    "sourceDBConnectionString"="Server=sql1;Database=WideWorldImporters;User Id=adminuser;Password=<sql-db-password>;Trusted_Connection=True;"
    ```

    Le stringhe di connessione includono sottostringhe visualizzate tra parentesi acute, che devono essere sostituite. Per `<storage_account_key>` usare la chiave ottenuta nel passaggio precedente. Per `<sql-db-password>` usare la password dell'account SQL Server specificata in precedenza nel file `onprem.parameters.json`.

### <a name="run-the-data-warehouse-scripts"></a>Eseguire gli script di data warehouse

1. Nel [portale di Azure](https://portal.azure.com/) trovare la VM locale, denominata `sql-vm1`. Il nome utente e la password per la VM sono specificati nel file `onprem.parameters.json`.

2. Fare clic su **Connetti** e usare Desktop remoto per connettersi alla VM.

3. Dalla sessione di Desktop remoto aprire un prompt dei comandi e passare alla cartella seguente nella VM:

    ```
    cd C:\SampleDataFiles\reference-architectures\data\enterprise_bi_sqldw_advanced\azure\sqldw_scripts
    ```

4. Eseguire il comando seguente:

    ```
    deploy_database.cmd -S <data_warehouse_server_name>.database.windows.net -d wwi -U adminuser -P <data_warehouse_password> -N -I
    ```

    Per `<data_warehouse_server_name>` e `<data_warehouse_password>` usare il nome e la password del server del data warehouse indicati in precedenza.

Per verificare questo passaggio, è possibile usare SQL Server Management Studio (SSMS) per connettersi al database di SQL Data Warehouse. Dovrebbero essere visualizzati gli schemi della tabella del database.

### <a name="run-the-data-factory-pipeline"></a>Eseguire la pipeline di Data Factory

1. Dalla stessa sessione di Desktop remoto aprire una finestra di PowerShell.

2. Eseguire il comando di PowerShell seguente. Scegliere **Sì** quando richiesto.

    ```powershell
    Install-Module -Name AzureRM -AllowClobber
    ```

3. Eseguire il comando di PowerShell seguente. Immettere le credenziali di Azure quando richiesto.

    ```powershell
    Connect-AzureRmAccount 
    ```

4. Eseguire i comandi di PowerShell seguenti. Sostituire i valori visualizzati tra parentesi acute.

    ```powershell
    Set-AzureRmContext -SubscriptionId <subscription id>

    Invoke-AzureRmDataFactoryV2Pipeline -DataFactory <data-factory-name> -PipelineName "MasterPipeline" -ResourceGroupName <resource_group_name>

5. In the Azure Portal, navigate to the Data Factory instance that was created earlier.

6. In the Data Factory blade, click **Author & Monitor**. This opens the Azure Data Factory portal in another browser window.

    ![](./images/adf-blade.png)

7. In the Azure Data Factory portal, click the **Monitor** icon. 

8. Verify that the pipeline completes successfully. It can take a few minutes.

    ![](./images/adf-pipeline-progress.png)


## Build the Analysis Services model

In this step, you will create a tabular model that imports data from the data warehouse. Then you will deploy the model to Azure Analysis Services.

**Create a new tabular project**

1. From your Remote Desktop session, launch SQL Server Data Tools 2015.

2. Select **File** > **New** > **Project**.

3. In the **New Project** dialog, under **Templates**, select  **Business Intelligence** > **Analysis Services** > **Analysis Services Tabular Project**. 

4. Name the project and click **OK**.

5. In the **Tabular model designer** dialog, select **Integrated workspace**  and set **Compatibility level** to `SQL Server 2017 / Azure Analysis Services (1400)`. 

6. Click **OK**.


**Import data**

1. In the **Tabular Model Explorer** window, right-click the project and select **Import from Data Source**.

2. Select **Azure SQL Data Warehouse** and click **Connect**.

3. For **Server**, enter the fully qualified name of your Azure SQL Data Warehouse server. You can get this value from the Azure Portal. For **Database**, enter `wwi`. Click **OK**.

4. In the next dialog, choose **Database** authentication and enter your Azure SQL Data Warehouse user name and password, and click **OK**.

5. In the **Navigator** dialog, select the checkboxes for the **Fact.\*** and **Dimension.\*** tables.

    ![](./images/analysis-services-import-2.png)

6. Click **Load**. When processing is complete, click **Close**. You should now see a tabular view of the data.

**Create measures**

1. In the model designer, select the **Fact Sale** table.

2. Click a cell in the the measure grid. By default, the measure grid is displayed below the table. 

    ![](./images/tabular-model-measures.png)

3. In the formula bar, enter the following and press ENTER:

    ```
    Total Sales:=SUM('Fact Sale'[Total Including Tax])
    ```

4. Repeat these steps to create the following measures:

    ```
    Number of Years:=(MAX('Fact CityPopulation'[YearNumber])-MIN('Fact CityPopulation'[YearNumber]))+1
    
    Beginning Population:=CALCULATE(SUM('Fact CityPopulation'[Population]),FILTER('Fact CityPopulation','Fact CityPopulation'[YearNumber]=MIN('Fact CityPopulation'[YearNumber])))
    
    Ending Population:=CALCULATE(SUM('Fact CityPopulation'[Population]),FILTER('Fact CityPopulation','Fact CityPopulation'[YearNumber]=MAX('Fact CityPopulation'[YearNumber])))
    
    CAGR:=IFERROR((([Ending Population]/[Beginning Population])^(1/[Number of Years]))-1,0)
    ```

    ![](./images/analysis-services-measures.png)

For more information about creating measures in SQL Server Data Tools, see [Measures](/sql/analysis-services/tabular-models/measures-ssas-tabular).

**Create relationships**

1. In the **Tabular Model Explorer** window, right-click the project and select **Model View** > **Diagram View**.

2. Drag the **[Fact Sale].[City Key]** field to the **[Dimension City].[City Key]** field to create a relationship.  

3. Drag the **[Face CityPopulation].[City Key]** field to the **[Dimension City].[City Key]** field.  

    ![](./images/analysis-services-relations-2.png)

**Deploy the model**

1. From the **File** menu, choose **Save All**.

2. In **Solution Explorer**, right-click the project and select **Properties**. 

3. Under **Server**, enter the URL of your Azure Analysis Services instance. You can get this value from the Azure Portal. In the portal, select the Analysis Services resource, click the Overview pane, and look for the **Server Name** property. It will be similar to `asazure://westus.asazure.windows.net/contoso`. Click **OK**.

    ![](./images/analysis-services-properties.png)

4. In **Solution Explorer**, right-click the project and select **Deploy**. Sign into Azure if prompted. When processing is complete, click **Close**.

5. In the Azure portal, view the details for your Azure Analysis Services instance. Verify that your model appears in the list of models.

    ![](./images/analysis-services-models.png)

## Analyze the data in Power BI Desktop

In this step, you will use Power BI to create a report from the data in Analysis Services.

1. From your Remote Desktop session, launch Power BI Desktop.

2. In the Welcome Scren, click **Get Data**.

3. Select **Azure** > **Azure Analysis Services database**. Click **Connect**

    ![](./images/power-bi-get-data.png)

4. Enter the URL of your Analysis Services instance, then click **OK**. Sign into Azure if prompted.

5. In the **Navigator** dialog, expand the tabular project, select the model, and click **OK**.

2. In the **Visualizations** pane, select the **Table** icon. In the Report view, resize the visualization to make it larger.

6. In the **Fields** pane, expand **Dimension City**.

7. From **Dimension City**, drag **City** and **State Province** to the **Values** well.

9. In the **Fields** pane, expand **Fact Sale**.

10. From **Fact Sale**, drag **CAGR**, **Ending Population**,  and **Total Sales** to the **Value** well.

11. Under **Visual Level Filters**, select **Ending Population**. Set the filter to "is greater than 100000" and click **Apply filter**.

12. Under **Visual Level Filters**, select **Total Sales**. Set the filter to "is 0" and click **Apply filter**.

![](./images/power-bi-report-2.png)

The table now shows cities with population greater than 100,000 and zero sales. CAGR  stands for Compounded Annual Growth Rate and measures the rate of population growth per city. You could use this value to find cities with high growth rates, for example. However, note that the values for CAGR in the model aren't accurate, because they are derived from sample data.

To learn more about Power BI Desktop, see [Getting started with Power BI Desktop](/power-bi/desktop-getting-started).


[adf]: //azure/data-factory
[azure-cli-2]: //azure/install-azure-cli
[azbb-repo]: https://github.com/mspnp/template-building-blocks
[azbb-wiki]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[MergeLocation]: https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/city/%5BIntegration%5D.%5BMergeLocation%5D.sql
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[ref-arch-repo-folder]: https://github.com/mspnp/reference-architectures/tree/master/data/enterprise_bi_sqldw_advanced
[wwi]: //sql/sample/world-wide-importers/wide-world-importers-oltp-database
