---
title: Elaborazione di flussi con Azure Databricks
titleSuffix: Azure Reference Architectures
description: Creare una pipeline di elaborazione di flussi end-to-end in Azure usando Azure Databricks.
author: petertaylor9999
ms.date: 11/30/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 3d109cb830b7dfc8c3d4de0e654f9d8667acf101
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59740460"
---
# <a name="create-a-stream-processing-pipeline-with-azure-databricks"></a>Creare una pipeline di elaborazione di flussi con Azure Databricks

Questa architettura di riferimento illustra una pipeline di [elaborazione di flussi](/azure/architecture/data-guide/big-data/real-time-processing) end-to-end. Questo tipo di pipeline include quattro fasi: inserimento, processo, archiviazione, e analisi e creazione di report. Per questa architettura di riferimento, la pipeline inserisce i dati da due origini, esegue un join in record correlati da ogni flusso, arricchisce il risultato e calcola una media in tempo reale. I risultati vengono archiviati per analisi aggiuntive. [**Distribuire questa soluzione**](#deploy-the-solution).

![Architettura di riferimento per l'elaborazione di flussi con Azure Databricks](./images/stream-processing-databricks.png)

**Scenario**: una società di taxi raccoglie dati su ogni corsa. Per questo scenario si presuppone che siano presenti due dispositivi diversi che inviano dati. Il taxi ha un tassametro che invia informazioni su ogni corsa &mdash; la durata, la distanza e le ubicazioni di salita e di discesa del cliente. Un dispositivo separato accetta i pagamenti dai clienti e invia dati sui prezzi delle corse. Per individuare le tendenze dell'utenza, la società di taxi vuole calcolare la mancia media per miglia guidate, in tempo reale, per ogni quartiere.

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

**Origini dati**. In questa architettura sono presenti due origini dati che generano flussi di dati in tempo reale. Il primo flusso contiene le informazioni sulla corsa e il secondo contiene le informazioni sui costi delle corse. L'architettura di riferimento include un generatore di dati simulato che legge dati da un set di file statici ed esegue il push dei dati in Hub eventi. Le origini dati in un'applicazione reale corrisponderebbero a dispositivi installati nei taxi.

**Hub eventi di Azure**. [Hub eventi](/azure/event-hubs/) è un servizio di inserimento di eventi. Questa architettura usa due istanze di Hub eventi, una per ogni origine dati. Ogni origine dati invia un flusso di dati all'istanza associata di Hub eventi.

**Azure Databricks**. [Databricks](/azure/azure-databricks/) è una piattaforma di analisi basata su Apache Spark ottimizzata per la piattaforma dei servizi cloud di Microsoft Azure. Databricks viene usata per la correlazione dei dati su corse e tariffe dei taxi, nonché per migliorare i dati correlati con i dati sul quartiere archiviati nel file System di Databricks.

**Cosmos DB**. L'output dal processo di Azure Databricks è una serie di record, che vengono scritti in [Cosmos DB](/azure/cosmos-db/) con l'API Cassandra. Viene usata l'API Cassandra perché supporta la modellazione di dati delle serie temporali.

**Azure Log Analytics**. I dati del log applicazioni raccolti da [Monitoraggio di Azure](/azure/monitoring-and-diagnostics/) vengono archiviati in un'[area di lavoro Log Analytics](/azure/log-analytics). Le query di Log Analytics permettono di analizzare e visualizzare le metriche e ispezionare i messaggi di log allo scopo di identificare i problemi all'interno dell'applicazione.

## <a name="data-ingestion"></a>Inserimento di dati

<!-- markdownlint-disable MD033 -->

Per simulare un'origine dati, questa architettura di riferimento usa il set di dati [New York City Taxi Data](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797) <sup>[[1]](#note1)</sup>. Questo set di dati contiene dati relativi alle corse dei taxi a New York City in un periodo di quattro anni (2010 &ndash; 2013). I tipi di record contenuti sono due: Dati relativi alle corse e dati relativi alle tariffe. I dati relativi alle corsa includono la durata della corsa, la distanza percorsa e le ubicazioni di salita e discesa del cliente. I dati relativi ai costi della corsa includono gli importi relativi a costo di base, imposte e mancia. I campi comuni in entrambi i tipi di record includono il numero di taxi, il numero di licenza e l'ID del fornitore. Questi tre campi identificano in modo univoco un taxi e un tassista. I dati vengono archiviati in formato CSV.

> [1] <span id="note1">Donovan, Brian; Work, Dan (2016): New York City Taxi Trip Data (2010-2013). University of Illinois at Urbana-Champaign. <https://doi.org/10.13012/J8PN93H8>

<!-- markdownlint-enable MD033 -->

Il generatore di dati è un'applicazione .NET Core che legge i record e li invia a Hub eventi di Azure. Il generatore invia i dati relativi alle corse in formato JSON e i dati relativi ai costi in formato CSV.

Hub eventi usa [partizioni](/azure/event-hubs/event-hubs-features#partitions) per segmentare i dati. Le partizioni consentono a un consumer di leggere ogni partizione in parallelo. Quando si inviano dati a Hub eventi, è possibile specificare in modo esplicito la chiave di partizione. In caso contrario, i record vengono assegnati alle partizioni in modalità round-robin.

In questo scenario i dati relativi alle corse e i dati relativi ai costi devono avere lo stesso ID di partizione per un taxi specifico. Ciò consente a Databricks di applicare un certo livello di parallelismo durante la correlazione dei due flussi. Un record nella partizione *n* dei dati relativi alle corse corrisponderà a un record nella partizione *n* dei dati relativi ai costi.

![Diagramma di elaborazione dei flussi con Azure Databricks e l'hub eventi di Azure](./images/stream-processing-databricks-eh.png)

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

L'ultima metrica da registrare per l'area di lavoro Azure Log Analytics è lo stato di avanzamento cumulativo del processo Spark Structured Streaming. Questa operazione viene eseguita usando un listener StreamingQuery personalizzato implementato nella classe **com.microsoft.pnp.StreamingMetricsListener**. Questa classe viene registrata nella sessione di Apache Spark quando viene eseguito il processo:

```scala
spark.streams.addListener(new StreamingMetricsListener())
```

I metodi nella classe StreamingMetricsListener vengono chiamati dal runtime di Apache Spark ogni volta che si verifica un evento di streaming strutturato, inviando messaggi di log e metriche all'area di lavoro Azure Log Analytics. È possibile usare le query seguenti nell'area di lavoro per monitorare l'applicazione:

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

Per altre informazioni, vedere [monitoraggio di Azure Databricks](../../databricks-monitoring/index.md).

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Per distribuire ed eseguire l'implementazione di riferimento, seguire la procedura illustrata nel file [README in GitHub](https://github.com/mspnp/azure-databricks-streaming-analytics).
