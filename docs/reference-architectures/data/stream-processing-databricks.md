---
title: Elaborazione di flussi con Azure Databricks
description: Creare una pipeline di elaborazione di flussi end-to-end in Azure usando Azure Databricks
author: petertaylor9999
ms.date: 11/30/2018
ms.openlocfilehash: 0640e900c212d2b75cc9cdd5bec3a4f7c050490d
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902834"
---
# <a name="stream-processing-with-azure-databricks"></a>Elaborazione di flussi con Azure Databricks

Questa architettura di riferimento illustra una pipeline di [elaborazione di flussi](/azure/architecture/data-guide/big-data/real-time-processing) end-to-end. Questo tipo di pipeline include quattro fasi: inserimento, processo, archiviazione, e analisi e creazione di report. Per questa architettura di riferimento, la pipeline inserisce i dati da due origini, esegue un join in record correlati da ogni flusso, arricchisce il risultato e calcola una media in tempo reale. I risultati vengono archiviati per analisi aggiuntive. [**Distribuire questa soluzione**.](#deploy-the-solution)

![](./images/stream-processing-databricks.png)

**Scenario**: una società di taxi raccoglie dati su ogni corsa. Per questo scenario si presuppone che siano presenti due dispositivi diversi che inviano dati. Il taxi ha un tassametro che invia informazioni su ogni corsa &mdash; la durata, la distanza e le ubicazioni di salita e di discesa del cliente. Un dispositivo separato accetta i pagamenti dai clienti e invia dati sui prezzi delle corse. Per individuare le tendenze dell'utenza, la società di taxi vuole calcolare la mancia media per miglia guidate, in tempo reale, per ogni quartiere.

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

**Origini dati**. In questa architettura sono presenti due origini dati che generano flussi di dati in tempo reale. Il primo flusso contiene le informazioni sulla corsa e il secondo contiene le informazioni sui costi delle corse. L'architettura di riferimento include un generatore di dati simulato che legge dati da un set di file statici ed esegue il push dei dati in Hub eventi. Le origini dati in un'applicazione reale corrisponderebbero a dispositivi installati nei taxi.

**Hub eventi di Azure**. [Hub eventi](/azure/event-hubs/) è un servizio di inserimento di eventi. Questa architettura usa due istanze di Hub eventi, una per ogni origine dati. Ogni origine dati invia un flusso di dati all'istanza associata di Hub eventi.

**Azure Databricks**. [Databricks](/azure/azure-databricks/) è una piattaforma di analisi basata su Apache Spark ottimizzata per la piattaforma dei servizi cloud di Microsoft Azure. Databricks viene usata per la correlazione dei dati su corse e tariffe dei taxi, nonché per migliorare i dati correlati con i dati sul quartiere archiviati nel file System di Databricks.

**Cosmos DB**. L'output dal processo di Azure Databricks è una serie di record, che vengono scritti in [Cosmos DB](/azure/cosmos-db/) con l'API Cassandra. Viene usata l'API Cassandra perché supporta la modellazione di dati delle serie temporali.

**Azure Log Analytics**. I dati del registro applicazioni raccolti da [Monitoraggio di Azure](/azure/monitoring-and-diagnostics/) vengono archiviati in un'[area di lavoro di Log Analytics](/azure/log-analytics). Le query di Log Analytics permettono di analizzare e visualizzare le metriche e ispezionare i messaggi di log allo scopo di identificare i problemi all'interno dell'applicazione.

## <a name="data-ingestion"></a>Inserimento di dati

Per simulare un'origine dati, questa architettura di riferimento usa il set di dati [New York City Taxi Data](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797) <sup>[[1]](#note1)</sup>. Questo set di dati contiene dati relativi alle corse dei taxi a New York City in un periodo di quattro anni (2010 &ndash; 2013). Contiene due tipi di record: i dati relativi alle corse e i dati relativi ai costi delle corse. I dati relativi alle corsa includono la durata della corsa, la distanza percorsa e le ubicazioni di salita e discesa del cliente. I dati relativi ai costi della corsa includono gli importi relativi a costo di base, imposte e mancia. I campi comuni in entrambi i tipi di record includono il numero di taxi, il numero di licenza e l'ID del fornitore. Questi tre campi identificano in modo univoco un taxi e un tassista. I dati vengono archiviati in formato CSV. 

Il generatore di dati è un'applicazione .NET Core che legge i record e li invia a Hub eventi di Azure. Il generatore invia i dati relativi alle corse in formato JSON e i dati relativi ai costi in formato CSV. 

Hub eventi usa [partizioni](/azure/event-hubs/event-hubs-features#partitions) per segmentare i dati. Le partizioni consentono a un consumer di leggere ogni partizione in parallelo. Quando si inviano dati a Hub eventi, è possibile specificare in modo esplicito la chiave di partizione. In caso contrario, i record vengono assegnati alle partizioni in modalità round-robin. 

In questo scenario i dati relativi alle corse e i dati relativi ai costi devono avere lo stesso ID di partizione per un taxi specifico. Ciò consente a Databricks di applicare un certo livello di parallelismo durante la correlazione dei due flussi. Un record nella partizione *n* dei dati relativi alle corse corrisponderà a un record nella partizione *n* dei dati relativi ai costi.

![](./images/stream-processing-databricks-eh.png)

Nel generatore di dati il modello di dati comune per entrambi i tipi di record ha una proprietà `PartitionKey` che corrisponde alla concatenazione di `Medallion`, `HackLicense` e `VendorId`.

```csharp
public abstract class TaxiData
{
    public TaxiData()
    {
    }

    [JsonProperty]
    public long Medallion { get; set; }

    [JsonProperty]
    public long HackLicense { get; set; }

    [JsonProperty]
    public string VendorId { get; set; }

    [JsonProperty]
    public DateTimeOffset PickupTime { get; set; }

    [JsonIgnore]
    public string PartitionKey
    {
        get => $"{Medallion}_{HackLicense}_{VendorId}";
    }
```

Questa proprietà viene usata per fornire una chiave di partizione esplicita durante l'invio a Hub eventi:

```csharp
using (var client = pool.GetObject())
{
    return client.Value.SendAsync(new EventData(Encoding.UTF8.GetBytes(
        t.GetData(dataFormat))), t.PartitionKey);
}
```

### <a name="event-hubs"></a>Hub eventi

La capacità di elaborazione di Hub eventi viene misurata in [unità elaborate](/azure/event-hubs/event-hubs-features#throughput-units). È possibile ridimensionare automaticamente un hub eventi abilitando l'[aumento automatico](/azure/event-hubs/event-hubs-auto-inflate), che ridimensiona automaticamente le unità elaborate in base al traffico, fino a un limite massimo configurato. 

## <a name="stream-processing"></a>Elaborazione del flusso

In Azure Databricks, viene eseguita l'elaborazione dei dati da un processo. Il processo viene assegnato a e viene eseguito in un cluster. Il processo può essere codice personalizzato scritto in Java o un [notebook](https://docs.databricks.com/user-guide/notebooks/index.html) Spark.

In questa architettura di riferimento, il processo è un file di archivio Java con classi scritte in Java e Scala. Quando si specifica il file di archivio Java per un processo di Databricks, la classe viene specificata per l'esecuzione da parte del cluster Databricks. In questo caso, il **principale** metodo della classe **com.microsoft.pnp.TaxiCabReader** contiene la logica di elaborazione dati. 

### <a name="reading-the-stream-from-the-two-event-hub-instances"></a>Lettura del flusso dalle due istanze dell'hub eventi

La logica di elaborazione dei dati usa [lo streaming strutturato Spark](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html) per leggere dalle due istanze dell'hub eventi di Azure:

```scala
val rideEventHubOptions = EventHubsConf(rideEventHubConnectionString)
      .setConsumerGroup(conf.taxiRideConsumerGroup())
      .setStartingPosition(EventPosition.fromStartOfStream)
    val rideEvents = spark.readStream
      .format("eventhubs")
      .options(rideEventHubOptions.toMap)
      .load

    val fareEventHubOptions = EventHubsConf(fareEventHubConnectionString)
      .setConsumerGroup(conf.taxiFareConsumerGroup())
      .setStartingPosition(EventPosition.fromStartOfStream)
    val fareEvents = spark.readStream
      .format("eventhubs")
      .options(fareEventHubOptions.toMap)
      .load
```

### <a name="enriching-the-data-with-the-neighborhood-information"></a>Arricchimento dei dati con le informazioni sul quartiere

I dati sulla corsa includono le coordinate di latitudine e longitudine dei punti di partenza e arrivo. Benché siano utili, queste coordinate non sono facilmente analizzabili. Di conseguenza, questi dati vengono arricchiti con i dati sul quartiere, letti da un [file di forma](https://en.wikipedia.org/wiki/Shapefile). 

Il formato di file di forma è binario e non facilmente analizzato, ma la libreria [GeoTools](http://geotools.org/) fornisce strumenti per i dati geospaziali che usano il formato di file di forma. Questa libreria viene usata nella classe **com.microsoft.pnp.GeoFinder** per determinare il nome del quartiere in base alle coordinate di partenza e arrivo. 

```scala
val neighborhoodFinder = (lon: Double, lat: Double) => {
      NeighborhoodFinder.getNeighborhood(lon, lat).get()
    }
```

### <a name="joining-the-ride-and-fare-data"></a>Unione di dati su corse e tariffe

Prima di tutto i dati su corse e tariffe vengono trasformati:

```scala
    val rides = transformedRides
      .filter(r => {
        if (r.isNullAt(r.fieldIndex("errorMessage"))) {
          true
        }
        else {
          malformedRides.add(1)
          false
        }
      })
      .select(
        $"ride.*",
        to_neighborhood($"ride.pickupLon", $"ride.pickupLat")
          .as("pickupNeighborhood"),
        to_neighborhood($"ride.dropoffLon", $"ride.dropoffLat")
          .as("dropoffNeighborhood")
      )
      .withWatermark("pickupTime", conf.taxiRideWatermarkInterval())

    val fares = transformedFares
      .filter(r => {
        if (r.isNullAt(r.fieldIndex("errorMessage"))) {
          true
        }
        else {
          malformedFares.add(1)
          false
        }
      })
      .select(
        $"fare.*",
        $"pickupTime"
      )
      .withWatermark("pickupTime", conf.taxiFareWatermarkInterval())
```

Quindi, i dati sulla corsa vengono aggiunti ai dati sulle tariffe:

```scala
val mergedTaxiTrip = rides.join(fares, Seq("medallion", "hackLicense", "vendorId", "pickupTime"))
```

### <a name="processing-the-data-and-inserting-into-cosmos-db"></a>Elaborazione dati e inserimento in Cosmos DB

L'importo tariffario medio per ogni quartiere viene calcolato per un determinato intervallo di tempo:

```scala
val maxAvgFarePerNeighborhood = mergedTaxiTrip.selectExpr("medallion", "hackLicense", "vendorId", "pickupTime", "rateCode", "storeAndForwardFlag", "dropoffTime", "passengerCount", "tripTimeInSeconds", "tripDistanceInMiles", "pickupLon", "pickupLat", "dropoffLon", "dropoffLat", "paymentType", "fareAmount", "surcharge", "mtaTax", "tipAmount", "tollsAmount", "totalAmount", "pickupNeighborhood", "dropoffNeighborhood")
      .groupBy(window($"pickupTime", conf.windowInterval()), $"pickupNeighborhood")
      .agg(
        count("*").as("rideCount"),
        sum($"fareAmount").as("totalFareAmount"),
        sum($"tipAmount").as("totalTipAmount")
      )
      .select($"window.start", $"window.end", $"pickupNeighborhood", $"rideCount", $"totalFareAmount", $"totalTipAmount")
```

Che viene quindi inserito in Cosmos DB:

```scala
maxAvgFarePerNeighborhood
      .writeStream
      .queryName("maxAvgFarePerNeighborhood_cassandra_insert")
      .outputMode(OutputMode.Append())
      .foreach(new CassandraSinkForeach(connector))
      .start()
      .awaitTermination()
```

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

L'accesso all'area di lavoro del Database di Azure viene controllato dalla [console di amministrazione](https://docs.databricks.com/administration-guide/admin-settings/index.html). La console di amministrazione include funzionalità per aggiungere utenti, gestire le autorizzazioni utente e impostare il single sign-on. La console permette anche di impostare il controllo di accesso ad aree di lavoro, cluster, processi e tabelle.

### <a name="managing-secrets"></a>Gestione dei segreti

Azure Databricks include un [archivio segreto](https://docs.azuredatabricks.net/user-guide/secrets/index.html) che viene usato per archiviare i segreti, tra cui le stringhe di connessione, le chiavi di accesso, i nomi utente e le password. I segreti all'interno dell'archivio segreto di Azure Databricks vengono partizionati per **ambiti**:

```bash
databricks secrets create-scope --scope "azure-databricks-job"
```

I segreti vengono aggiunti a livello ambito:

```bash
databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
```

> [!NOTE]
> È possibile usare un ambito di cui è stato eseguito il backup in Azure Key Vault invece dell'ambito nativo di Azure Databricks. Per altre informazioni, vedere [Azure Key Vault-backed scopes](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes) ( Ambiti di cui è stato eseguito il backup in Azure Key Vault).

Nel codice, i segreti sono accessibili grazie alle [utilità dei segreti](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities) di Azure Databricks.


## <a name="monitoring-considerations"></a>Considerazioni sul monitoraggio

Azure Databricks si basa su Apache Spark, e entrambi usano [log4j](https://logging.apache.org/log4j/2.x/) come libreria standard per la registrazione. Oltre alla registrazione predefinita fornita da Apache Spark, questa architettura di riferimento invia log e metriche a [Azure Log Analytics](/azure/log-analytics/).

La classe **com.microsoft.pnp.TaxiCabReader** configura il sistema di registrazione di Apache Spark in modo da inviare i registri a Azure Log Analytics usando i valori contenuti nel file **log4j.properties**. Mentre i messaggi del logger di Apache Spark sono stringhe, Azure Log Analytics richiede che i messaggi di log siano formattati come JSON. La classe **com.microsoft.pnp.log4j.LogAnalyticsAppender** trasforma questi messaggi in JSON:

```scala

    @Override
    protected void append(LoggingEvent loggingEvent) {
        if (this.layout == null) {
            this.setLayout(new JSONLayout());
        }

        String json = this.getLayout().format(loggingEvent);
        try {
            this.client.send(json, this.logType);
        } catch(IOException ioe) {
            LogLog.warn("Error sending LoggingEvent to Log Analytics", ioe);
        }
    }

```

Dal momento che la classe **com.microsoft.pnp.TaxiCabReader** elabora i messaggi relativi a corse e tariffe, è possibile che il formato di uno dei due non sia valido. In un ambiente di produzione, è importante analizzare questi messaggi in formato non valido per identificare un problema con le origini dati in modo da risolverlo rapidamente per evitare la perdita di dati. La classe **com.microsoft.pnp.TaxiCabReader** registra un accumulatore Apache Spark che tiene traccia del numero di record su tariffe e corse in formato non valido:

```scala
    @transient val appMetrics = new AppMetrics(spark.sparkContext)
    appMetrics.registerGauge("metrics.malformedrides", AppAccumulators.getRideInstance(spark.sparkContext))
    appMetrics.registerGauge("metrics.malformedfares", AppAccumulators.getFareInstance(spark.sparkContext))
    SparkEnv.get.metricsSystem.registerSource(appMetrics)
```

Apache Spark usa la libreria Dropwizard per inviare metriche e alcuni dei campi metrici nativi di Dropwizard non sono compatibili con Azure Log Analytics. Di conseguenza, questa architettura di riferimento include un sink e un reporter di Dropwizard personalizzati. Formatta le metriche nel formato previsto da Azure Log Analytics. Quando Apache Spark riporta le metriche, vengono inviate anche le metriche personalizzate per i dati di corse e tariffe in formato non valido.

L'ultima metrica da registrare per l'area di lavoro di Azure Log Analytics è lo stato di avanzamento cumulativo del processo Spark Structured Streaming. Questa operazione viene eseguita usando un listener StreamingQuery personalizzato implementato nella classe **com.microsoft.pnp.StreamingMetricsListener**. Questa classe viene registrata nella sessione di Apache Spark quando viene eseguito il processo:

```scala
spark.streams.addListener(new StreamingMetricsListener())
```

I metodi nella classe StreamingMetricsListener vengono chiamati dal runtime di Apache Spark ogni volta che si verifica un evento di streaming strutturato, inviando messaggi di log e metriche all'area di lavoro di Azure Log Analytics. È possibile usare le query seguenti nell'area di lavoro per monitorare l'applicazione:

### <a name="latency-and-throughput-for-streaming-queries"></a>Latenza e velocità effettiva per le query di streaming 

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| project  mdc_inputRowsPerSecond_d, mdc_durationms_triggerExecution_d  
| render timechart
``` 
### <a name="exceptions-logged-during-stream-query-execution"></a>Eccezioni registrate durante l'esecuzione di query di flusso

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| where Level contains "Error" 
```

### <a name="accumulation-of-malformed-fare-and-ride-data"></a>Accumulo di dati su tariffe e corse in formato non valido

```shell
SparkMetric_CL 
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart 
| where name_s contains "metrics.malformedrides"

SparkMetric_CL 
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart 
| where name_s contains "metrics.malformedfares" 
```

### <a name="job-execution-to-trace-resiliency"></a>Esecuzione del processo per tenere traccia della resilienza

```shell
SparkMetric_CL 
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart 
| where name_s contains "driver.DAGScheduler.job.allJobs" 
```

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura di riferimento è disponibile in [GitHub](https://github.com/mspnp/azure-databricks-streaming-analytics). 

### <a name="prerequisites"></a>Prerequisiti

1. Clonare, creare una copia tramite fork o scaricare il repository GitHub per l'[elaborazione di flussi con Azure Databricks](https://github.com/mspnp/azure-databricks-streaming-analytics).

2. Installare [Docker](https://www.docker.com/) per eseguire il generatore di dati.

3. Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).

4. Installare l'[interfaccia della riga di comando di Databricks](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html).

5. Al prompt dei comandi, di Bash o di PowerShell accedere all'account Azure come illustrato di seguito:
    ```shell
    az login
    ```
6. Installare un ambiente IDE Java, con le risorse seguenti:
    - JDK 1.8
    - Scala SDK 2.11
    - Maven 3.5.4

### <a name="download-the-new-york-city-taxi-and-neighborhood-data-files"></a>Scaricare i file di dati dei taxi e quartieri di New York City

1. Creare una directory denominata `DataFile` nella radice del repository Github clonato nel file system locale.

2. Aprire un Web browser e passare a https://uofi.app.box.com/v/NYCtaxidata/folder/2332219935.

3. Fare clic sul pulsante **Download** in questa pagina per scaricare un file ZIP di tutti i dati relativi ai taxi per tale anno.

4. Estrarre il file ZIP nella directory `DataFile`.

    > [!NOTE]
    > Questo file ZIP contiene altri file ZIP. Non estrarre i file ZIP figlio.

    La struttura della directory deve avere un aspetto simile al seguente:

    ```shell
    /DataFile
        /FOIL2013
            trip_data_1.zip
            trip_data_2.zip
            trip_data_3.zip
            ...
    ```

5. Aprire un Web browser e passare a https://www.zillow.com/howto/api/neighborhood-boundaries.htm. 

6. Fare clic su **New York Neighborhood Boundaries** per scaricare il file.

7. Copiare il file **ZillowNeighborhoods-NY.zip** dalla directory dei **download** del browser nella directory `DataFile`.

### <a name="deploy-the-azure-resources"></a>Distribuire le risorse di Azure

1. Da una shell o dal prompt dei comandi di Windows eseguire il comando seguente e attenersi alla richiesta di accesso:

    ```bash
    az login
    ```

2. Passare alla cartella denominata `azure` nel repository GitHub:

    ```bash
    cd azure
    ```

3. Eseguire questi comandi per distribuire le risorse di Azure:

    ```bash
    export resourceGroup='[Resource group name]'
    export resourceLocation='[Region]'
    export eventHubNamespace='[Event Hubs namespace name]'
    export databricksWorkspaceName='[Azure Databricks workspace name]'
    export cosmosDatabaseAccount='[Cosmos DB database name]'
    export logAnalyticsWorkspaceName='[Log Analytics workspace name]'
    export logAnalyticsWorkspaceRegion='[Log Analytics region]'

    # Create a resource group
    az group create --name $resourceGroup --location $resourceLocation

    # Deploy resources
    az group deployment create --resource-group $resourceGroup \
        --template-file deployresources.json --parameters \
        eventHubNamespace=$eventHubNamespace \
        databricksWorkspaceName=$databricksWorkspaceName \
        cosmosDatabaseAccount=$cosmosDatabaseAccount \
        logAnalyticsWorkspaceName=$logAnalyticsWorkspaceName \
        logAnalyticsWorkspaceRegion=$logAnalyticsWorkspaceRegion
    ```

4. L'output della distribuzione viene scritto nella console al termine dell'operazione. Cercare l'output per il JSON seguente:

```JSON
"outputs": {
        "cosmosDb": {
          "type": "Object",
          "value": {
            "hostName": <value>,
            "secret": <value>,
            "username": <value>
          }
        },
        "eventHubs": {
          "type": "Object",
          "value": {
            "taxi-fare-eh": <value>,
            "taxi-ride-eh": <value>
          }
        },
        "logAnalytics": {
          "type": "Object",
          "value": {
            "secret": <value>,
            "workspaceId": <value>
          }
        }
},
```
Questi valori sono i segreti che verranno aggiunti ai segreti di Databricks nelle sezioni successive. Tenerli al sicuro fino a quando saranno aggiunti in tali sezioni.

### <a name="add-a-cassandra-table-to-the-cosmos-db-account"></a>Aggiungere una tabella Cassandra all'account Cosmos DB

1. Nel portale di Azure passare al gruppo di risorse creato nella sezione **Distribuire le risorse di Azure** precedente. Fare clic su **Account Azure Cosmos DB**. Creare una tabella con l'API Cassandra.

2. Nel pannello **Panoramica** fare clic su **Aggiungi tabella**.

3. Quando il pannello **Aggiungi tabella** viene visualizzato, immettere `newyorktaxi` nella casella di testo **Keyspace name**. 

4. Nella sezione **enter CQL command to create the table** immettere `neighborhoodstats` nella casella di testo accanto a `newyorktaxi`.

5. Nella casella di testo seguente, immettere:
```shell
(neighborhood text, window_end timestamp, number_of_rides bigint,total_fare_amount double, primary key(neighborhood, window_end))
```
6. Nella casella di testo **Throughput (1,000 - 1,000,000 RU/s)** immettere il valore `4000`.

7. Fare clic su **OK**.

### <a name="add-the-databricks-secrets-using-the-databricks-cli"></a>Aggiungere i segreti di Databricks usando l'interfaccia della riga di comando di Databricks

Prima di tutto immettere i segreti per EventHub:

1. Usando l'**interfaccia della riga di comando di Azure Databricks** installata al passaggio 2 dei prerequisiti, creare l'ambito del segreto di Azure Databricks:
    ```shell
    databricks secrets create-scope --scope "azure-databricks-job"
    ```
2. Aggiungere il segreto per l'EventHub della corsa in taxi:
    ```shell
    databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
    ```
    Una volta eseguito, questo comando apre l'editor vi. Immettere il valore **taxi-ride-eh** dalla sezione output **eventHubs** al passaggio 4 della sezione *Distribuire le risorse di Azure*. Salvare e chiudere vi.

3. Aggiungere il segreto per l'EventHub della tariffa del taxi:
    ```shell
    databricks secrets put --scope "azure-databricks-job" --key "taxi-fare"
    ```
    Una volta eseguito, questo comando apre l'editor vi. Immettere il valore **taxi-fare-eh** dalla sezione output **eventHubs** al passaggio 4 della sezione *Distribuire le risorse di Azure*. Salvare e chiudere vi.

Quindi, immettere i segreti per Cosmos DB:

1. Aprire il portale di Azure e passare al gruppo di risorse specificato al passaggio 3 della sezione **Distribuire le risorse di Azure**. Fare clic sull'account Azure Cosmos DB.

2. Usando l'**interfaccia della riga di comando di Azure Databricks**, aggiungere il segreto per il nome utente di Cosmos DB:
    ```shell
    databricks secrets put --scope azure-databricks-job --key "cassandra-username"
    ```
Una volta eseguito, questo comando apre l'editor vi. Immettere il valore **username** dalla sezione output **CosmosDb** al passaggio 4 della sezione *Distribuire le risorse di Azure*. Salvare e chiudere vi.

3. Successivamente, aggiungere il segreto della password di Cosmos DB:
    ```shell
    databricks secrets put --scope azure-databricks-job --key "cassandra-password"
    ```

Una volta eseguito, questo comando apre l'editor vi. Immettere il valore **secret** dalla sezione output **CosmosDb** al passaggio 4 della sezione *Distribuire le risorse di Azure*. Salvare e chiudere vi.

> [!NOTE]
> Se si usa un [ambito segreto di cui è stato eseguito il backup in Azure Key Vault](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes), l'ambito deve essere denominato **azure-databricks-job** e i segreti devono avere gli stessi nomi esatti di quelli precedenti.

### <a name="add-the-zillow-neighborhoods-data-file-to-the-databricks-file-system"></a>Aggiungere il file di dati Zillow Neighborhoods al file system di Databricks

1. Creare una directory nel file system di Databricks:
    ```bash
    dbfs mkdirs dbfs:/azure-databricks-jobs
    ```

2. Passare alla directory `DataFile` e immettere il comando seguente:
    ```bash
    dbfs cp ZillowNeighborhoods-NY.zip dbfs:/azure-databricks-jobs
    ```

### <a name="add-the-azure-log-analytics-workspace-id-and-primary-key-to-configuration-files"></a>Aggiungere l'ID e la chiave primaria dell'area di lavoro di Log Analytics ai file di configurazione

Per questa sezione, sono richiesti l'ID e la chiave primaria dell'area di lavoro di Log Analytics. L'ID dell'area di lavoro è il valore **workspaceId** della sezione di output **logAnalytics** al passaggio 4 della sezione *Distribuire le risorse di Azure*. La chiave primaria è il **segreto** della sezione di output. 

1. Per configurare la registrazione di log4j, aprire `\azure\AzureDataBricksJob\src\main\resources\com\microsoft\pnp\azuredatabricksjob\log4j.properties`. Modificare i due valori seguenti:
    ```shell
    log4j.appender.A1.workspaceId=<Log Analytics workspace ID>
    log4j.appender.A1.secret=<Log Analytics primary key>
    ```

2. Per configurare la registrazione personalizzata, aprire `\azure\azure-databricks-monitoring\scripts\metrics.properties`. Modificare i due valori seguenti:
    ```shell
    *.sink.loganalytics.workspaceId=<Log Analytics workspace ID>
    *.sink.loganalytics.secret=<Log Analytics primary key>
    ```

### <a name="build-the-jar-files-for-the-databricks-job-and-databricks-monitoring"></a>Compilare i file JAR per il processo e il monitoraggio Databricks

1. Usare l'ambiente IDE Java per importare il file di progetto Maven denominato **pom.xml** situato nella radice della directory radice. 

2. Eseguire una compilazione pulita. L'output di questa compilazione sono file denominati **azure-databricks-job-1.0-SNAPSHOT.jar** e **azure-databricks-monitoring-0.9.jar**. 

### <a name="configure-custom-logging-for-the-databricks-job"></a>Configurare la registrazione personalizzata per il processo di Databricks

1. Copiare il file **azure-databricks-monitoring-0.9.jar** nel file system di Databricks immettendo il comando seguente nell'**interfaccia della riga di comando di Databricks**:
    ```shell
    databricks fs cp --overwrite azure-databricks-monitoring-0.9.jar dbfs:/azure-databricks-job/azure-databricks-monitoring-0.9.jar
    ```

2. Copiare le proprietà di registrazione personalizzate da `\azure\azure-databricks-monitoring\scripts\metrics.properties` nel file system di Databricks immettendo il comando seguente:
    ```shell
    databricks fs cp --overwrite metrics.properties dbfs:/azure-databricks-job/metrics.properties
    ```

3. Selezionare adesso un nome per il cluster di Databricks. Immetterlo nel percorso del file system di Databricks relativo al cluster. Copiare lo script di inizializzazione da `\azure\azure-databricks-monitoring\scripts\spark.metrics` nel file system di Databricks immettendo il comando seguente:
    ```
    databricks fs cp --overwrite spark-metrics.sh dbfs:/databricks/init/<cluster-name>/spark-metrics.sh
    ```

### <a name="create-a-databricks-cluster"></a>Creare un cluster di Databricks

1. Nell'area di lavoro di Databricks, fare clic su "Clusters", quindi fare clic su "create cluster". Immettere il nome del cluster creato al passaggio 3 della sezione **Configurare la registrazione personalizzata per il processo di Databricks** precedente. 

2. Selezionare una modalità cluster **standard**.

3. Impostare la **versione del runtime di Databricks** su **4.3 (include Apache Spark 2.3.1, Scala 2.11)**

4. Impostare la **versione di Python** su **2**.

5. Impostare il **tipo driver** su **Same as worker**.

6. Impostare il **tipo di ruolo di lavoro** su **Standard_DS3_v2**.

7. Impostare **Min Workers** su **2**.

8. Deselezionare **Enable autoscaling** (Abilita la scalabilità automatica). 

9. Sotto la finestra di dialogo **Auto Termination** fare clic su **Init Scripts**. 

10. Immettere **dbfs:/databricks/init/<nome-cluster>/spark-metrics.sh**, sostituendo il nome del cluster creato al passaggio 1 per <nome-cluster>.

11. Fare clic su **Add** .

12. Fare clic sul pulsante **Create Cluster**.

### <a name="create-a-databricks-job"></a>Creare un processo di Databricks

1. Nell'area di lavoro di Databricks, fare clic su "Jobs", quindi fare clic su "create job".

2. Immettere un nome per il processo.

3. Fare clic su "set jar" per aprire la finestra di dialogo "Upload JAR to Run".

4. Trascinare il file **azure-databricks-job-1.0-SNAPSHOT.jar** creato nella sezione **Compilare i file JAR per il processo e il monitoraggio Databricks** nella casella **Drop JAR here to upload**.

5. Immettere **com.microsoft.pnp.TaxiCabReader** nel campo **Main Class**.

6. Nel campo arguments, immettere quanto segue:
    ```shell
    -n jar:file:/dbfs/azure-databricks-jobs/ZillowNeighborhoods-NY.zip!/ZillowNeighborhoods-NY.shp --taxi-ride-consumer-group taxi-ride-eh-cg --taxi-fare-consumer-group taxi-fare-eh-cg --window-interval "1 minute" --cassandra-host <Cosmos DB Cassandra host name from above> 
    ``` 

7. Per installare le librerie dipendenti, seguire questi passaggi:
    
    1. Nell'interfaccia utente di Databricks, fare clic sul pulsante **home**.
    
    2. Nell'elenco a discesa **Users** fare clic sul nome del proprio account utente per accedere alle impostazioni dell'area di lavoro dell'account.
    
    3. Fare clic sulla freccia giù accanto al nome dell'account, fare clic su **create**e quindi su **Library** per aprire la finestra di dialogo **New Library**.
    
    4. Nel controllo a discesa **Source** selezionare **Maven Coordinate**.
    
    5. Sotto l'intestazione **Install Maven Artifacts** immettere `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.5` nella casella di testo **Coordinate**. 
    
    6. Fare clic su **Create Library** per aprire la finestra **Artifacts**.
    
    7. Sotto **Status on running clusters** selezionare la casella di controllo **Attach automatically to all clusters**.
    
    8. Ripetere i passaggi da 1 a 7 per la coordinata Maven `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`.
    
    9. Ripetere i passaggi da 1 a 6 per la coordinata Maven `org.geotools:gt-shapefile:19.2`.
    
    10. Fare clic su **Advanced Options**.
    
    11. Immettere `http://download.osgeo.org/webdav/geotools/` nella casella di testo **Repository**. 
    
    12. Fare clic su **Create Library** per aprire la finestra **Artifacts**. 
    
    13. Sotto **Status on running clusters** selezionare la casella di controllo **Attach automatically to all clusters**.

8. Aggiungere le librerie dipendenti aggiunte al passaggio 7 per il processo creato alla fine del passaggio 6:
    1. Nell'area di lavoro di Azure Databricks, fare clic su **Jobs**.

    2. Fare clic sul nome del processo creato al passaggio 2 della sezione **Creare un processo di Databricks**. 
    
    3. Accanto alla sezione **Dependent Libraries** fare clic su **Add** per aprire la finestra di dialogo **Add Dependent Library**. 
    
    4. Sotto **Library From** selezionare **Workspace**.
    
    5. Fare clic su **users**, quindi sul proprio nome utente e infine su `azure-eventhubs-spark_2.11:2.3.5`. 
    
    6. Fare clic su **OK**.
    
    7. Ripetere i passaggi da 1 a 6 per `spark-cassandra-connector_2.11:2.3.1` e `gt-shapefile:19.2`.

9. Accanto a **Cluster:**, fare clic su **Edit**. Verrà visualizzata la finestra di dialogo **Configure Cluster**. Nell'elenco a discesa **Cluster Type** selezionare **Existing Cluster**. Nell'elenco a discesa **Select Cluster** selezionare il cluster creato nella sezione **Creare un cluster di Databricks**. Fare clic su **confirm**.

10. Fare clic su **run now**.

### <a name="run-the-data-generator"></a>Eseguire il generatore di dati

1. Passare alla directory denominata `onprem` nel repository GitHub.

2. Aggiornare i valori nel file **main.env** come indicato di seguito:

    ```shell
    RIDE_EVENT_HUB=[Connection string for the taxi-ride event hub]
    FARE_EVENT_HUB=[Connection string for the taxi-fare event hub]
    RIDE_DATA_FILE_PATH=/DataFile/FOIL2013
    MINUTES_TO_LEAD=0
    PUSH_RIDE_DATA_FIRST=false
    ```
    La stringa di connessione per l'hub eventi taxi-ride è il valore **taxi-ride-eh** dalla sezione output **eventHubs** al passaggio 4 della sezione *Distribuire le risorse di Azure*. La stringa di connessione per l'hub eventi taxi-fare è il valore **taxi-fare-eh** dalla sezione output **eventHubs** al passaggio 4 della sezione *Distribuire le risorse di Azure*.

3. Eseguire questo comando per creare l'immagine Docker.

    ```bash
    docker build --no-cache -t dataloader .
    ```

4. Tornare alla directory padre.

    ```bash
    cd ..
    ```

5. Eseguire questo comando per eseguire l'immagine Docker.

    ```bash
    docker run -v `pwd`/DataFile:/DataFile --env-file=onprem/main.env dataloader:latest
    ```

L'output sarà simile al seguente:

```
Created 10000 records for TaxiFare
Created 10000 records for TaxiRide
Created 20000 records for TaxiFare
Created 20000 records for TaxiRide
Created 30000 records for TaxiFare
...
```

Per verificare che il processo di Databricks venga eseguito in modo corretto, aprire il portale di Azure e passare al database di Cosmos DB. Aprire il pannello **Esplora dati** ed esaminare i dati nella tabella **taxi records**. 

[1] <span id="note1">Donovan, Brian; Work, Dan (2016): New York City Taxi Trip Data (2010-2013). University of Illinois at Urbana-Champaign. https://doi.org/10.13012/J8PN93H8
