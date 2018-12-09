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
# <a name="stream-processing-with-azure-databricks"></a><span data-ttu-id="fad66-103">Elaborazione di flussi con Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="fad66-103">Stream processing with Azure Databricks</span></span>

<span data-ttu-id="fad66-104">Questa architettura di riferimento illustra una pipeline di [elaborazione di flussi](/azure/architecture/data-guide/big-data/real-time-processing) end-to-end.</span><span class="sxs-lookup"><span data-stu-id="fad66-104">This reference architecture shows an end-to-end [stream processing](/azure/architecture/data-guide/big-data/real-time-processing) pipeline.</span></span> <span data-ttu-id="fad66-105">Questo tipo di pipeline include quattro fasi: inserimento, processo, archiviazione, e analisi e creazione di report.</span><span class="sxs-lookup"><span data-stu-id="fad66-105">This type of pipeline has four stages: ingest, process, store, and analysis and reporting.</span></span> <span data-ttu-id="fad66-106">Per questa architettura di riferimento, la pipeline inserisce i dati da due origini, esegue un join in record correlati da ogni flusso, arricchisce il risultato e calcola una media in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="fad66-106">For this reference architecture, the pipeline ingests data from two sources, performs a join on related records from each stream, enriches the result, and calculates an average in real time.</span></span> <span data-ttu-id="fad66-107">I risultati vengono archiviati per analisi aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="fad66-107">The results are stored for further analysis.</span></span> [<span data-ttu-id="fad66-108">**Distribuire questa soluzione**.</span><span class="sxs-lookup"><span data-stu-id="fad66-108">**Deploy this solution**.</span></span>](#deploy-the-solution)

![](./images/stream-processing-databricks.png)

<span data-ttu-id="fad66-109">**Scenario**: una società di taxi raccoglie dati su ogni corsa.</span><span class="sxs-lookup"><span data-stu-id="fad66-109">**Scenario**: A taxi company collects data about each taxi trip.</span></span> <span data-ttu-id="fad66-110">Per questo scenario si presuppone che siano presenti due dispositivi diversi che inviano dati.</span><span class="sxs-lookup"><span data-stu-id="fad66-110">For this scenario, we assume there are two separate devices sending data.</span></span> <span data-ttu-id="fad66-111">Il taxi ha un tassametro che invia informazioni su ogni corsa &mdash; la durata, la distanza e le ubicazioni di salita e di discesa del cliente.</span><span class="sxs-lookup"><span data-stu-id="fad66-111">The taxi has a meter that sends information about each ride &mdash; the duration, distance, and pickup and dropoff locations.</span></span> <span data-ttu-id="fad66-112">Un dispositivo separato accetta i pagamenti dai clienti e invia dati sui prezzi delle corse.</span><span class="sxs-lookup"><span data-stu-id="fad66-112">A separate device accepts payments from customers and sends data about fares.</span></span> <span data-ttu-id="fad66-113">Per individuare le tendenze dell'utenza, la società di taxi vuole calcolare la mancia media per miglia guidate, in tempo reale, per ogni quartiere.</span><span class="sxs-lookup"><span data-stu-id="fad66-113">To spot ridership trends, the taxi company wants to calculate the average tip per mile driven, in real time, for each neighborhood.</span></span>

## <a name="architecture"></a><span data-ttu-id="fad66-114">Architettura</span><span class="sxs-lookup"><span data-stu-id="fad66-114">Architecture</span></span>

<span data-ttu-id="fad66-115">L'architettura è costituita dai componenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="fad66-115">The architecture consists of the following components.</span></span>

<span data-ttu-id="fad66-116">**Origini dati**.</span><span class="sxs-lookup"><span data-stu-id="fad66-116">**Data sources**.</span></span> <span data-ttu-id="fad66-117">In questa architettura sono presenti due origini dati che generano flussi di dati in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="fad66-117">In this architecture, there are two data sources that generate data streams in real time.</span></span> <span data-ttu-id="fad66-118">Il primo flusso contiene le informazioni sulla corsa e il secondo contiene le informazioni sui costi delle corse.</span><span class="sxs-lookup"><span data-stu-id="fad66-118">The first stream contains ride information, and the second contains fare information.</span></span> <span data-ttu-id="fad66-119">L'architettura di riferimento include un generatore di dati simulato che legge dati da un set di file statici ed esegue il push dei dati in Hub eventi.</span><span class="sxs-lookup"><span data-stu-id="fad66-119">The reference architecture includes a simulated data generator that reads from a set of static files and pushes the data to Event Hubs.</span></span> <span data-ttu-id="fad66-120">Le origini dati in un'applicazione reale corrisponderebbero a dispositivi installati nei taxi.</span><span class="sxs-lookup"><span data-stu-id="fad66-120">The data sources in a real application would be devices installed in the taxi cabs.</span></span>

<span data-ttu-id="fad66-121">**Hub eventi di Azure**.</span><span class="sxs-lookup"><span data-stu-id="fad66-121">**Azure Event Hubs**.</span></span> <span data-ttu-id="fad66-122">[Hub eventi](/azure/event-hubs/) è un servizio di inserimento di eventi.</span><span class="sxs-lookup"><span data-stu-id="fad66-122">[Event Hubs](/azure/event-hubs/) is an event ingestion service.</span></span> <span data-ttu-id="fad66-123">Questa architettura usa due istanze di Hub eventi, una per ogni origine dati.</span><span class="sxs-lookup"><span data-stu-id="fad66-123">This architecture uses two event hub instances, one for each data source.</span></span> <span data-ttu-id="fad66-124">Ogni origine dati invia un flusso di dati all'istanza associata di Hub eventi.</span><span class="sxs-lookup"><span data-stu-id="fad66-124">Each data source sends a stream of data to the associated event hub.</span></span>

<span data-ttu-id="fad66-125">**Azure Databricks**.</span><span class="sxs-lookup"><span data-stu-id="fad66-125">**Azure Databricks**.</span></span> <span data-ttu-id="fad66-126">[Databricks](/azure/azure-databricks/) è una piattaforma di analisi basata su Apache Spark ottimizzata per la piattaforma dei servizi cloud di Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="fad66-126">[Databricks](/azure/azure-databricks/) is an Apache Spark-based analytics platform optimized for the Microsoft Azure cloud services platform.</span></span> <span data-ttu-id="fad66-127">Databricks viene usata per la correlazione dei dati su corse e tariffe dei taxi, nonché per migliorare i dati correlati con i dati sul quartiere archiviati nel file System di Databricks.</span><span class="sxs-lookup"><span data-stu-id="fad66-127">Databricks is used to correlate of the taxi ride and fare data, and also to enrich the correlated data with neighborhood data stored in the Databricks file system.</span></span>

<span data-ttu-id="fad66-128">**Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="fad66-128">**Cosmos DB**.</span></span> <span data-ttu-id="fad66-129">L'output dal processo di Azure Databricks è una serie di record, che vengono scritti in [Cosmos DB](/azure/cosmos-db/) con l'API Cassandra.</span><span class="sxs-lookup"><span data-stu-id="fad66-129">The output from Azure Databricks job is a series of records, which are written to [Cosmos DB](/azure/cosmos-db/) using the Cassandra API.</span></span> <span data-ttu-id="fad66-130">Viene usata l'API Cassandra perché supporta la modellazione di dati delle serie temporali.</span><span class="sxs-lookup"><span data-stu-id="fad66-130">The Cassandra API is used because it supports time series data modeling.</span></span>

<span data-ttu-id="fad66-131">**Azure Log Analytics**.</span><span class="sxs-lookup"><span data-stu-id="fad66-131">**Azure Log Analytics**.</span></span> <span data-ttu-id="fad66-132">I dati del registro applicazioni raccolti da [Monitoraggio di Azure](/azure/monitoring-and-diagnostics/) vengono archiviati in un'[area di lavoro di Log Analytics](/azure/log-analytics).</span><span class="sxs-lookup"><span data-stu-id="fad66-132">Application log data collected by [Azure Monitor](/azure/monitoring-and-diagnostics/) is stored in a [Log Analytics workspace](/azure/log-analytics).</span></span> <span data-ttu-id="fad66-133">Le query di Log Analytics permettono di analizzare e visualizzare le metriche e ispezionare i messaggi di log allo scopo di identificare i problemi all'interno dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fad66-133">Log Analytics queries can be used to analyze and visualize metrics and inspect log messages to identify issues within the application.</span></span>

## <a name="data-ingestion"></a><span data-ttu-id="fad66-134">Inserimento di dati</span><span class="sxs-lookup"><span data-stu-id="fad66-134">Data ingestion</span></span>

<span data-ttu-id="fad66-135">Per simulare un'origine dati, questa architettura di riferimento usa il set di dati [New York City Taxi Data](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797) <sup>[[1]](#note1)</sup>.</span><span class="sxs-lookup"><span data-stu-id="fad66-135">To simulate a data source, this reference architecture uses the [New York City Taxi Data](https://uofi.app.box.com/v/NYCtaxidata/folder/2332218797) dataset<sup>[[1]](#note1)</sup>.</span></span> <span data-ttu-id="fad66-136">Questo set di dati contiene dati relativi alle corse dei taxi a New York City in un periodo di quattro anni (2010 &ndash; 2013).</span><span class="sxs-lookup"><span data-stu-id="fad66-136">This dataset contains data about taxi trips in New York City over a four-year period (2010 &ndash; 2013).</span></span> <span data-ttu-id="fad66-137">Contiene due tipi di record: i dati relativi alle corse e i dati relativi ai costi delle corse.</span><span class="sxs-lookup"><span data-stu-id="fad66-137">It contains two types of record: Ride data and fare data.</span></span> <span data-ttu-id="fad66-138">I dati relativi alle corsa includono la durata della corsa, la distanza percorsa e le ubicazioni di salita e discesa del cliente.</span><span class="sxs-lookup"><span data-stu-id="fad66-138">Ride data includes trip duration, trip distance, and pickup and dropoff location.</span></span> <span data-ttu-id="fad66-139">I dati relativi ai costi della corsa includono gli importi relativi a costo di base, imposte e mancia.</span><span class="sxs-lookup"><span data-stu-id="fad66-139">Fare data includes fare, tax, and tip amounts.</span></span> <span data-ttu-id="fad66-140">I campi comuni in entrambi i tipi di record includono il numero di taxi, il numero di licenza e l'ID del fornitore.</span><span class="sxs-lookup"><span data-stu-id="fad66-140">Common fields in both record types include medallion number, hack license, and vendor ID.</span></span> <span data-ttu-id="fad66-141">Questi tre campi identificano in modo univoco un taxi e un tassista.</span><span class="sxs-lookup"><span data-stu-id="fad66-141">Together these three fields uniquely identify a taxi plus a driver.</span></span> <span data-ttu-id="fad66-142">I dati vengono archiviati in formato CSV.</span><span class="sxs-lookup"><span data-stu-id="fad66-142">The data is stored in CSV format.</span></span> 

<span data-ttu-id="fad66-143">Il generatore di dati è un'applicazione .NET Core che legge i record e li invia a Hub eventi di Azure.</span><span class="sxs-lookup"><span data-stu-id="fad66-143">The data generator is a .NET Core application that reads the records and sends them to Azure Event Hubs.</span></span> <span data-ttu-id="fad66-144">Il generatore invia i dati relativi alle corse in formato JSON e i dati relativi ai costi in formato CSV.</span><span class="sxs-lookup"><span data-stu-id="fad66-144">The generator sends ride data in JSON format and fare data in CSV format.</span></span> 

<span data-ttu-id="fad66-145">Hub eventi usa [partizioni](/azure/event-hubs/event-hubs-features#partitions) per segmentare i dati.</span><span class="sxs-lookup"><span data-stu-id="fad66-145">Event Hubs uses [partitions](/azure/event-hubs/event-hubs-features#partitions) to segment the data.</span></span> <span data-ttu-id="fad66-146">Le partizioni consentono a un consumer di leggere ogni partizione in parallelo.</span><span class="sxs-lookup"><span data-stu-id="fad66-146">Partitions allow a consumer to read each partition in parallel.</span></span> <span data-ttu-id="fad66-147">Quando si inviano dati a Hub eventi, è possibile specificare in modo esplicito la chiave di partizione.</span><span class="sxs-lookup"><span data-stu-id="fad66-147">When you send data to Event Hubs, you can specify the partition key explicitly.</span></span> <span data-ttu-id="fad66-148">In caso contrario, i record vengono assegnati alle partizioni in modalità round-robin.</span><span class="sxs-lookup"><span data-stu-id="fad66-148">Otherwise, records are assigned to partitions in round-robin fashion.</span></span> 

<span data-ttu-id="fad66-149">In questo scenario i dati relativi alle corse e i dati relativi ai costi devono avere lo stesso ID di partizione per un taxi specifico.</span><span class="sxs-lookup"><span data-stu-id="fad66-149">In this scenario, ride data and fare data should end up with the same partition ID for a given taxi cab.</span></span> <span data-ttu-id="fad66-150">Ciò consente a Databricks di applicare un certo livello di parallelismo durante la correlazione dei due flussi.</span><span class="sxs-lookup"><span data-stu-id="fad66-150">This enables Databricks to apply a degree of parallelism when it correlates the two streams.</span></span> <span data-ttu-id="fad66-151">Un record nella partizione *n* dei dati relativi alle corse corrisponderà a un record nella partizione *n* dei dati relativi ai costi.</span><span class="sxs-lookup"><span data-stu-id="fad66-151">A record in partition *n* of the ride data will match a record in partition *n* of the fare data.</span></span>

![](./images/stream-processing-databricks-eh.png)

<span data-ttu-id="fad66-152">Nel generatore di dati il modello di dati comune per entrambi i tipi di record ha una proprietà `PartitionKey` che corrisponde alla concatenazione di `Medallion`, `HackLicense` e `VendorId`.</span><span class="sxs-lookup"><span data-stu-id="fad66-152">In the data generator, the common data model for both record types has a `PartitionKey` property that is the concatenation of `Medallion`, `HackLicense`, and `VendorId`.</span></span>

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

<span data-ttu-id="fad66-153">Questa proprietà viene usata per fornire una chiave di partizione esplicita durante l'invio a Hub eventi:</span><span class="sxs-lookup"><span data-stu-id="fad66-153">This property is used to provide an explicit partition key when sending to Event Hubs:</span></span>

```csharp
using (var client = pool.GetObject())
{
    return client.Value.SendAsync(new EventData(Encoding.UTF8.GetBytes(
        t.GetData(dataFormat))), t.PartitionKey);
}
```

### <a name="event-hubs"></a><span data-ttu-id="fad66-154">Hub eventi</span><span class="sxs-lookup"><span data-stu-id="fad66-154">Event Hubs</span></span>

<span data-ttu-id="fad66-155">La capacità di elaborazione di Hub eventi viene misurata in [unità elaborate](/azure/event-hubs/event-hubs-features#throughput-units).</span><span class="sxs-lookup"><span data-stu-id="fad66-155">The throughput capacity of Event Hubs is measured in [throughput units](/azure/event-hubs/event-hubs-features#throughput-units).</span></span> <span data-ttu-id="fad66-156">È possibile ridimensionare automaticamente un hub eventi abilitando l'[aumento automatico](/azure/event-hubs/event-hubs-auto-inflate), che ridimensiona automaticamente le unità elaborate in base al traffico, fino a un limite massimo configurato.</span><span class="sxs-lookup"><span data-stu-id="fad66-156">You can autoscale an event hub by enabling [auto-inflate](/azure/event-hubs/event-hubs-auto-inflate), which automatically scales the throughput units based on traffic, up to a configured maximum.</span></span> 

## <a name="stream-processing"></a><span data-ttu-id="fad66-157">Elaborazione del flusso</span><span class="sxs-lookup"><span data-stu-id="fad66-157">Stream processing</span></span>

<span data-ttu-id="fad66-158">In Azure Databricks, viene eseguita l'elaborazione dei dati da un processo.</span><span class="sxs-lookup"><span data-stu-id="fad66-158">In Azure Databricks, data processing is performed by a job.</span></span> <span data-ttu-id="fad66-159">Il processo viene assegnato a e viene eseguito in un cluster.</span><span class="sxs-lookup"><span data-stu-id="fad66-159">The job is assigned to and runs on a cluster.</span></span> <span data-ttu-id="fad66-160">Il processo può essere codice personalizzato scritto in Java o un [notebook](https://docs.databricks.com/user-guide/notebooks/index.html) Spark.</span><span class="sxs-lookup"><span data-stu-id="fad66-160">The job can either be custom code written in Java, or a Spark [notebook](https://docs.databricks.com/user-guide/notebooks/index.html).</span></span>

<span data-ttu-id="fad66-161">In questa architettura di riferimento, il processo è un file di archivio Java con classi scritte in Java e Scala.</span><span class="sxs-lookup"><span data-stu-id="fad66-161">In this reference architecture, the job is a Java archive with classes written in both Java and Scala.</span></span> <span data-ttu-id="fad66-162">Quando si specifica il file di archivio Java per un processo di Databricks, la classe viene specificata per l'esecuzione da parte del cluster Databricks.</span><span class="sxs-lookup"><span data-stu-id="fad66-162">When specifying the Java archive for a Databricks job, the class is specified for execution by the Databricks cluster.</span></span> <span data-ttu-id="fad66-163">In questo caso, il **principale** metodo della classe **com.microsoft.pnp.TaxiCabReader** contiene la logica di elaborazione dati.</span><span class="sxs-lookup"><span data-stu-id="fad66-163">Here, the **main** method of the **com.microsoft.pnp.TaxiCabReader** class contains the data processing logic.</span></span> 

### <a name="reading-the-stream-from-the-two-event-hub-instances"></a><span data-ttu-id="fad66-164">Lettura del flusso dalle due istanze dell'hub eventi</span><span class="sxs-lookup"><span data-stu-id="fad66-164">Reading the stream from the two event hub instances</span></span>

<span data-ttu-id="fad66-165">La logica di elaborazione dei dati usa [lo streaming strutturato Spark](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html) per leggere dalle due istanze dell'hub eventi di Azure:</span><span class="sxs-lookup"><span data-stu-id="fad66-165">The data processing logic uses [Spark structured streaming](https://spark.apache.org/docs/2.1.2/structured-streaming-programming-guide.html) to read from the two Azure event hub instances:</span></span>

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

### <a name="enriching-the-data-with-the-neighborhood-information"></a><span data-ttu-id="fad66-166">Arricchimento dei dati con le informazioni sul quartiere</span><span class="sxs-lookup"><span data-stu-id="fad66-166">Enriching the data with the neighborhood information</span></span>

<span data-ttu-id="fad66-167">I dati sulla corsa includono le coordinate di latitudine e longitudine dei punti di partenza e arrivo.</span><span class="sxs-lookup"><span data-stu-id="fad66-167">The ride data includes the latitude and longitude coordinates of the pick up and drop off locations.</span></span> <span data-ttu-id="fad66-168">Benché siano utili, queste coordinate non sono facilmente analizzabili.</span><span class="sxs-lookup"><span data-stu-id="fad66-168">While these coordinates are useful, they are not easily consumed for analysis.</span></span> <span data-ttu-id="fad66-169">Di conseguenza, questi dati vengono arricchiti con i dati sul quartiere, letti da un [file di forma](https://en.wikipedia.org/wiki/Shapefile).</span><span class="sxs-lookup"><span data-stu-id="fad66-169">Therefore, this data is enriched with neighborhood data that is read from a [shapefile](https://en.wikipedia.org/wiki/Shapefile).</span></span> 

<span data-ttu-id="fad66-170">Il formato di file di forma è binario e non facilmente analizzato, ma la libreria [GeoTools](http://geotools.org/) fornisce strumenti per i dati geospaziali che usano il formato di file di forma.</span><span class="sxs-lookup"><span data-stu-id="fad66-170">The shapefile format is binary and not easily parsed, but the [GeoTools](http://geotools.org/) library provides tools for geospatial data that use the shapefile format.</span></span> <span data-ttu-id="fad66-171">Questa libreria viene usata nella classe **com.microsoft.pnp.GeoFinder** per determinare il nome del quartiere in base alle coordinate di partenza e arrivo.</span><span class="sxs-lookup"><span data-stu-id="fad66-171">This library is used in the **com.microsoft.pnp.GeoFinder** class to determine the neighborhood name based on the pick up and drop off coordinates.</span></span> 

```scala
val neighborhoodFinder = (lon: Double, lat: Double) => {
      NeighborhoodFinder.getNeighborhood(lon, lat).get()
    }
```

### <a name="joining-the-ride-and-fare-data"></a><span data-ttu-id="fad66-172">Unione di dati su corse e tariffe</span><span class="sxs-lookup"><span data-stu-id="fad66-172">Joining the ride and fare data</span></span>

<span data-ttu-id="fad66-173">Prima di tutto i dati su corse e tariffe vengono trasformati:</span><span class="sxs-lookup"><span data-stu-id="fad66-173">First the ride and fare data is transformed:</span></span>

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

<span data-ttu-id="fad66-174">Quindi, i dati sulla corsa vengono aggiunti ai dati sulle tariffe:</span><span class="sxs-lookup"><span data-stu-id="fad66-174">And then the ride data is joined with the fare data:</span></span>

```scala
val mergedTaxiTrip = rides.join(fares, Seq("medallion", "hackLicense", "vendorId", "pickupTime"))
```

### <a name="processing-the-data-and-inserting-into-cosmos-db"></a><span data-ttu-id="fad66-175">Elaborazione dati e inserimento in Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="fad66-175">Processing the data and inserting into Cosmos DB</span></span>

<span data-ttu-id="fad66-176">L'importo tariffario medio per ogni quartiere viene calcolato per un determinato intervallo di tempo:</span><span class="sxs-lookup"><span data-stu-id="fad66-176">The average fare amount for each neighborhood is calculated for a given time interval:</span></span>

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

<span data-ttu-id="fad66-177">Che viene quindi inserito in Cosmos DB:</span><span class="sxs-lookup"><span data-stu-id="fad66-177">Which is then inserted into Cosmos DB:</span></span>

```scala
maxAvgFarePerNeighborhood
      .writeStream
      .queryName("maxAvgFarePerNeighborhood_cassandra_insert")
      .outputMode(OutputMode.Append())
      .foreach(new CassandraSinkForeach(connector))
      .start()
      .awaitTermination()
```

## <a name="security-considerations"></a><span data-ttu-id="fad66-178">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="fad66-178">Security considerations</span></span>

<span data-ttu-id="fad66-179">L'accesso all'area di lavoro del Database di Azure viene controllato dalla [console di amministrazione](https://docs.databricks.com/administration-guide/admin-settings/index.html).</span><span class="sxs-lookup"><span data-stu-id="fad66-179">Access to the Azure Database workspace is controlled using the [administrator console](https://docs.databricks.com/administration-guide/admin-settings/index.html).</span></span> <span data-ttu-id="fad66-180">La console di amministrazione include funzionalità per aggiungere utenti, gestire le autorizzazioni utente e impostare il single sign-on.</span><span class="sxs-lookup"><span data-stu-id="fad66-180">The administrator console includes functionality to add users, manage user permissions, and set up single sign-on.</span></span> <span data-ttu-id="fad66-181">La console permette anche di impostare il controllo di accesso ad aree di lavoro, cluster, processi e tabelle.</span><span class="sxs-lookup"><span data-stu-id="fad66-181">Access control for workspaces, clusters, jobs, and tables can also be set through the administrator console.</span></span>

### <a name="managing-secrets"></a><span data-ttu-id="fad66-182">Gestione dei segreti</span><span class="sxs-lookup"><span data-stu-id="fad66-182">Managing secrets</span></span>

<span data-ttu-id="fad66-183">Azure Databricks include un [archivio segreto](https://docs.azuredatabricks.net/user-guide/secrets/index.html) che viene usato per archiviare i segreti, tra cui le stringhe di connessione, le chiavi di accesso, i nomi utente e le password.</span><span class="sxs-lookup"><span data-stu-id="fad66-183">Azure Databricks includes a [secret store](https://docs.azuredatabricks.net/user-guide/secrets/index.html) that is used to store secrets, including connection strings, access keys, user names, and passwords.</span></span> <span data-ttu-id="fad66-184">I segreti all'interno dell'archivio segreto di Azure Databricks vengono partizionati per **ambiti**:</span><span class="sxs-lookup"><span data-stu-id="fad66-184">Secrets within the Azure Databricks secret store are partitioned by **scopes**:</span></span>

```bash
databricks secrets create-scope --scope "azure-databricks-job"
```

<span data-ttu-id="fad66-185">I segreti vengono aggiunti a livello ambito:</span><span class="sxs-lookup"><span data-stu-id="fad66-185">Secrets are added at the scope level:</span></span>

```bash
databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
```

> [!NOTE]
> <span data-ttu-id="fad66-186">È possibile usare un ambito di cui è stato eseguito il backup in Azure Key Vault invece dell'ambito nativo di Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="fad66-186">An Azure Key Vault-backed scope can be used instead of the native Azure Databricks scope.</span></span> <span data-ttu-id="fad66-187">Per altre informazioni, vedere [Azure Key Vault-backed scopes](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes) ( Ambiti di cui è stato eseguito il backup in Azure Key Vault).</span><span class="sxs-lookup"><span data-stu-id="fad66-187">To learn more, see [Azure Key Vault-backed scopes](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes).</span></span>

<span data-ttu-id="fad66-188">Nel codice, i segreti sono accessibili grazie alle [utilità dei segreti](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities) di Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="fad66-188">In code, secrets are accessed via the Azure Databricks [secrets utilities](https://docs.databricks.com/user-guide/dev-tools/dbutils.html#secrets-utilities).</span></span>


## <a name="monitoring-considerations"></a><span data-ttu-id="fad66-189">Considerazioni sul monitoraggio</span><span class="sxs-lookup"><span data-stu-id="fad66-189">Monitoring considerations</span></span>

<span data-ttu-id="fad66-190">Azure Databricks si basa su Apache Spark, e entrambi usano [log4j](https://logging.apache.org/log4j/2.x/) come libreria standard per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="fad66-190">Azure Databricks is based on Apache Spark, and both use [log4j](https://logging.apache.org/log4j/2.x/) as the standard library for logging.</span></span> <span data-ttu-id="fad66-191">Oltre alla registrazione predefinita fornita da Apache Spark, questa architettura di riferimento invia log e metriche a [Azure Log Analytics](/azure/log-analytics/).</span><span class="sxs-lookup"><span data-stu-id="fad66-191">In addition to the default logging provided by Apache Spark, this reference architecture sends logs and metrics to [Azure Log Analytics](/azure/log-analytics/).</span></span>

<span data-ttu-id="fad66-192">La classe **com.microsoft.pnp.TaxiCabReader** configura il sistema di registrazione di Apache Spark in modo da inviare i registri a Azure Log Analytics usando i valori contenuti nel file **log4j.properties**.</span><span class="sxs-lookup"><span data-stu-id="fad66-192">The **com.microsoft.pnp.TaxiCabReader** class configures the Apache Spark logging system to send its logs to Azure Log Analytics using the values in the **log4j.properties** file.</span></span> <span data-ttu-id="fad66-193">Mentre i messaggi del logger di Apache Spark sono stringhe, Azure Log Analytics richiede che i messaggi di log siano formattati come JSON.</span><span class="sxs-lookup"><span data-stu-id="fad66-193">While the Apache Spark logger messages are strings, Azure Log Analytics requires log messages to be formatted as JSON.</span></span> <span data-ttu-id="fad66-194">La classe **com.microsoft.pnp.log4j.LogAnalyticsAppender** trasforma questi messaggi in JSON:</span><span class="sxs-lookup"><span data-stu-id="fad66-194">The **com.microsoft.pnp.log4j.LogAnalyticsAppender** class transforms these messages to JSON:</span></span>

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

<span data-ttu-id="fad66-195">Dal momento che la classe **com.microsoft.pnp.TaxiCabReader** elabora i messaggi relativi a corse e tariffe, è possibile che il formato di uno dei due non sia valido.</span><span class="sxs-lookup"><span data-stu-id="fad66-195">As the **com.microsoft.pnp.TaxiCabReader** class processes ride and fare messages, it's possible that either one may be malformed and therefore not valid.</span></span> <span data-ttu-id="fad66-196">In un ambiente di produzione, è importante analizzare questi messaggi in formato non valido per identificare un problema con le origini dati in modo da risolverlo rapidamente per evitare la perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="fad66-196">In a production environment, it's important to analyze these malformed messages to identify a problem with the data sources so it can be fixed quickly to prevent data loss.</span></span> <span data-ttu-id="fad66-197">La classe **com.microsoft.pnp.TaxiCabReader** registra un accumulatore Apache Spark che tiene traccia del numero di record su tariffe e corse in formato non valido:</span><span class="sxs-lookup"><span data-stu-id="fad66-197">The **com.microsoft.pnp.TaxiCabReader** class registers an Apache Spark Accumulator that keeps track of the number of malformed fare and ride records:</span></span>

```scala
    @transient val appMetrics = new AppMetrics(spark.sparkContext)
    appMetrics.registerGauge("metrics.malformedrides", AppAccumulators.getRideInstance(spark.sparkContext))
    appMetrics.registerGauge("metrics.malformedfares", AppAccumulators.getFareInstance(spark.sparkContext))
    SparkEnv.get.metricsSystem.registerSource(appMetrics)
```

<span data-ttu-id="fad66-198">Apache Spark usa la libreria Dropwizard per inviare metriche e alcuni dei campi metrici nativi di Dropwizard non sono compatibili con Azure Log Analytics.</span><span class="sxs-lookup"><span data-stu-id="fad66-198">Apache Spark uses the Dropwizard library to send metrics, and some of the native Dropwizard metrics fields are incompatible with Azure Log Analytics.</span></span> <span data-ttu-id="fad66-199">Di conseguenza, questa architettura di riferimento include un sink e un reporter di Dropwizard personalizzati.</span><span class="sxs-lookup"><span data-stu-id="fad66-199">Therefore, this reference architecture includes a custom Dropwizard sink and reporter.</span></span> <span data-ttu-id="fad66-200">Formatta le metriche nel formato previsto da Azure Log Analytics.</span><span class="sxs-lookup"><span data-stu-id="fad66-200">It formats the metrics in the format expected by Azure Log Analytics.</span></span> <span data-ttu-id="fad66-201">Quando Apache Spark riporta le metriche, vengono inviate anche le metriche personalizzate per i dati di corse e tariffe in formato non valido.</span><span class="sxs-lookup"><span data-stu-id="fad66-201">When Apache Spark reports metrics, the custom metrics for the malformed ride and fare data are also sent.</span></span>

<span data-ttu-id="fad66-202">L'ultima metrica da registrare per l'area di lavoro di Azure Log Analytics è lo stato di avanzamento cumulativo del processo Spark Structured Streaming.</span><span class="sxs-lookup"><span data-stu-id="fad66-202">The last metric to be logged to the Azure Log Analytics workspace is the cumulative progress of the Spark Structured Streaming job progress.</span></span> <span data-ttu-id="fad66-203">Questa operazione viene eseguita usando un listener StreamingQuery personalizzato implementato nella classe **com.microsoft.pnp.StreamingMetricsListener**.</span><span class="sxs-lookup"><span data-stu-id="fad66-203">This is done using a custom StreamingQuery listener implemented in the **com.microsoft.pnp.StreamingMetricsListener** class.</span></span> <span data-ttu-id="fad66-204">Questa classe viene registrata nella sessione di Apache Spark quando viene eseguito il processo:</span><span class="sxs-lookup"><span data-stu-id="fad66-204">This class is registered to the Apache Spark Session when the job runs:</span></span>

```scala
spark.streams.addListener(new StreamingMetricsListener())
```

<span data-ttu-id="fad66-205">I metodi nella classe StreamingMetricsListener vengono chiamati dal runtime di Apache Spark ogni volta che si verifica un evento di streaming strutturato, inviando messaggi di log e metriche all'area di lavoro di Azure Log Analytics.</span><span class="sxs-lookup"><span data-stu-id="fad66-205">The methods in the StreamingMetricsListener are called by the Apache Spark runtime whenever a structured steaming event occurs, sending log messages and metrics to the Azure Log Analytics workspace.</span></span> <span data-ttu-id="fad66-206">È possibile usare le query seguenti nell'area di lavoro per monitorare l'applicazione:</span><span class="sxs-lookup"><span data-stu-id="fad66-206">You can use the following queries in your workspace to monitor the application:</span></span>

### <a name="latency-and-throughput-for-streaming-queries"></a><span data-ttu-id="fad66-207">Latenza e velocità effettiva per le query di streaming</span><span class="sxs-lookup"><span data-stu-id="fad66-207">Latency and throughput for streaming queries</span></span> 

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| project  mdc_inputRowsPerSecond_d, mdc_durationms_triggerExecution_d  
| render timechart
``` 
### <a name="exceptions-logged-during-stream-query-execution"></a><span data-ttu-id="fad66-208">Eccezioni registrate durante l'esecuzione di query di flusso</span><span class="sxs-lookup"><span data-stu-id="fad66-208">Exceptions logged during stream query execution</span></span>

```shell
taxijob_CL
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| where Level contains "Error" 
```

### <a name="accumulation-of-malformed-fare-and-ride-data"></a><span data-ttu-id="fad66-209">Accumulo di dati su tariffe e corse in formato non valido</span><span class="sxs-lookup"><span data-stu-id="fad66-209">Accumulation of malformed fare and ride data</span></span>

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

### <a name="job-execution-to-trace-resiliency"></a><span data-ttu-id="fad66-210">Esecuzione del processo per tenere traccia della resilienza</span><span class="sxs-lookup"><span data-stu-id="fad66-210">Job execution to trace resiliency</span></span>

```shell
SparkMetric_CL 
| where TimeGenerated > startofday(datetime(<date>)) and TimeGenerated < endofday(datetime(<date>))
| render timechart 
| where name_s contains "driver.DAGScheduler.job.allJobs" 
```

## <a name="deploy-the-solution"></a><span data-ttu-id="fad66-211">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="fad66-211">Deploy the solution</span></span>

<span data-ttu-id="fad66-212">Una distribuzione di questa architettura di riferimento è disponibile in [GitHub](https://github.com/mspnp/azure-databricks-streaming-analytics).</span><span class="sxs-lookup"><span data-stu-id="fad66-212">A deployment for this reference architecture is available on [GitHub](https://github.com/mspnp/azure-databricks-streaming-analytics).</span></span> 

### <a name="prerequisites"></a><span data-ttu-id="fad66-213">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="fad66-213">Prerequisites</span></span>

1. <span data-ttu-id="fad66-214">Clonare, creare una copia tramite fork o scaricare il repository GitHub per l'[elaborazione di flussi con Azure Databricks](https://github.com/mspnp/azure-databricks-streaming-analytics).</span><span class="sxs-lookup"><span data-stu-id="fad66-214">Clone, fork, or download the [stream processing with Azure Databricks](https://github.com/mspnp/azure-databricks-streaming-analytics) GitHub repository.</span></span>

2. <span data-ttu-id="fad66-215">Installare [Docker](https://www.docker.com/) per eseguire il generatore di dati.</span><span class="sxs-lookup"><span data-stu-id="fad66-215">Install [Docker](https://www.docker.com/) to run the data generator.</span></span>

3. <span data-ttu-id="fad66-216">Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).</span><span class="sxs-lookup"><span data-stu-id="fad66-216">Install [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).</span></span>

4. <span data-ttu-id="fad66-217">Installare l'[interfaccia della riga di comando di Databricks](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html).</span><span class="sxs-lookup"><span data-stu-id="fad66-217">Install [Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html).</span></span>

5. <span data-ttu-id="fad66-218">Al prompt dei comandi, di Bash o di PowerShell accedere all'account Azure come illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="fad66-218">From a command prompt, bash prompt, or PowerShell prompt, sign into your Azure account as follows:</span></span>
    ```shell
    az login
    ```
6. <span data-ttu-id="fad66-219">Installare un ambiente IDE Java, con le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="fad66-219">Install a Java IDE, with the following resources:</span></span>
    - <span data-ttu-id="fad66-220">JDK 1.8</span><span class="sxs-lookup"><span data-stu-id="fad66-220">JDK 1.8</span></span>
    - <span data-ttu-id="fad66-221">Scala SDK 2.11</span><span class="sxs-lookup"><span data-stu-id="fad66-221">Scala SDK 2.11</span></span>
    - <span data-ttu-id="fad66-222">Maven 3.5.4</span><span class="sxs-lookup"><span data-stu-id="fad66-222">Maven 3.5.4</span></span>

### <a name="download-the-new-york-city-taxi-and-neighborhood-data-files"></a><span data-ttu-id="fad66-223">Scaricare i file di dati dei taxi e quartieri di New York City</span><span class="sxs-lookup"><span data-stu-id="fad66-223">Download the New York City taxi and neighborhood data files</span></span>

1. <span data-ttu-id="fad66-224">Creare una directory denominata `DataFile` nella radice del repository Github clonato nel file system locale.</span><span class="sxs-lookup"><span data-stu-id="fad66-224">Create a directory named `DataFile` in the root of the cloned Github repository in your local file system.</span></span>

2. <span data-ttu-id="fad66-225">Aprire un Web browser e passare a https://uofi.app.box.com/v/NYCtaxidata/folder/2332219935.</span><span class="sxs-lookup"><span data-stu-id="fad66-225">Open a web browser and navigate to https://uofi.app.box.com/v/NYCtaxidata/folder/2332219935.</span></span>

3. <span data-ttu-id="fad66-226">Fare clic sul pulsante **Download** in questa pagina per scaricare un file ZIP di tutti i dati relativi ai taxi per tale anno.</span><span class="sxs-lookup"><span data-stu-id="fad66-226">Click the **Download** button on this page to download a zip file of all the taxi data for that year.</span></span>

4. <span data-ttu-id="fad66-227">Estrarre il file ZIP nella directory `DataFile`.</span><span class="sxs-lookup"><span data-stu-id="fad66-227">Extract the zip file to the `DataFile` directory.</span></span>

    > [!NOTE]
    > <span data-ttu-id="fad66-228">Questo file ZIP contiene altri file ZIP.</span><span class="sxs-lookup"><span data-stu-id="fad66-228">This zip file contains other zip files.</span></span> <span data-ttu-id="fad66-229">Non estrarre i file ZIP figlio.</span><span class="sxs-lookup"><span data-stu-id="fad66-229">Don't extract the child zip files.</span></span>

    <span data-ttu-id="fad66-230">La struttura della directory deve avere un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="fad66-230">The directory structure must look like the following:</span></span>

    ```shell
    /DataFile
        /FOIL2013
            trip_data_1.zip
            trip_data_2.zip
            trip_data_3.zip
            ...
    ```

5. <span data-ttu-id="fad66-231">Aprire un Web browser e passare a https://www.zillow.com/howto/api/neighborhood-boundaries.htm.</span><span class="sxs-lookup"><span data-stu-id="fad66-231">Open a web browser and navigate to https://www.zillow.com/howto/api/neighborhood-boundaries.htm.</span></span> 

6. <span data-ttu-id="fad66-232">Fare clic su **New York Neighborhood Boundaries** per scaricare il file.</span><span class="sxs-lookup"><span data-stu-id="fad66-232">Click on **New York Neighborhood Boundaries** to download the file.</span></span>

7. <span data-ttu-id="fad66-233">Copiare il file **ZillowNeighborhoods-NY.zip** dalla directory dei **download** del browser nella directory `DataFile`.</span><span class="sxs-lookup"><span data-stu-id="fad66-233">Copy the **ZillowNeighborhoods-NY.zip** file from your browser's **downloads** directory to the `DataFile` directory.</span></span>

### <a name="deploy-the-azure-resources"></a><span data-ttu-id="fad66-234">Distribuire le risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="fad66-234">Deploy the Azure resources</span></span>

1. <span data-ttu-id="fad66-235">Da una shell o dal prompt dei comandi di Windows eseguire il comando seguente e attenersi alla richiesta di accesso:</span><span class="sxs-lookup"><span data-stu-id="fad66-235">From a shell or Windows Command Prompt, run the following command and follow the sign-in prompt:</span></span>

    ```bash
    az login
    ```

2. <span data-ttu-id="fad66-236">Passare alla cartella denominata `azure` nel repository GitHub:</span><span class="sxs-lookup"><span data-stu-id="fad66-236">Navigate to the folder named `azure` in the GitHub repository:</span></span>

    ```bash
    cd azure
    ```

3. <span data-ttu-id="fad66-237">Eseguire questi comandi per distribuire le risorse di Azure:</span><span class="sxs-lookup"><span data-stu-id="fad66-237">Run the following commands to deploy the Azure resources:</span></span>

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

4. <span data-ttu-id="fad66-238">L'output della distribuzione viene scritto nella console al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="fad66-238">The output of the deployment is written to the console once complete.</span></span> <span data-ttu-id="fad66-239">Cercare l'output per il JSON seguente:</span><span class="sxs-lookup"><span data-stu-id="fad66-239">Search the output for the following JSON:</span></span>

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
<span data-ttu-id="fad66-240">Questi valori sono i segreti che verranno aggiunti ai segreti di Databricks nelle sezioni successive.</span><span class="sxs-lookup"><span data-stu-id="fad66-240">These values are the secrets that will be added to Databricks secrets in upcoming sections.</span></span> <span data-ttu-id="fad66-241">Tenerli al sicuro fino a quando saranno aggiunti in tali sezioni.</span><span class="sxs-lookup"><span data-stu-id="fad66-241">Keep them secure until you add them in those sections.</span></span>

### <a name="add-a-cassandra-table-to-the-cosmos-db-account"></a><span data-ttu-id="fad66-242">Aggiungere una tabella Cassandra all'account Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="fad66-242">Add a Cassandra table to the Cosmos DB Account</span></span>

1. <span data-ttu-id="fad66-243">Nel portale di Azure passare al gruppo di risorse creato nella sezione **Distribuire le risorse di Azure** precedente.</span><span class="sxs-lookup"><span data-stu-id="fad66-243">In the Azure portal, navigate to the resource group created in the **deploy the Azure resources** section above.</span></span> <span data-ttu-id="fad66-244">Fare clic su **Account Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="fad66-244">Click on **Azure Cosmos DB Account**.</span></span> <span data-ttu-id="fad66-245">Creare una tabella con l'API Cassandra.</span><span class="sxs-lookup"><span data-stu-id="fad66-245">Create a table with the Cassandra API.</span></span>

2. <span data-ttu-id="fad66-246">Nel pannello **Panoramica** fare clic su **Aggiungi tabella**.</span><span class="sxs-lookup"><span data-stu-id="fad66-246">In the **overview** blade, click **add table**.</span></span>

3. <span data-ttu-id="fad66-247">Quando il pannello **Aggiungi tabella** viene visualizzato, immettere `newyorktaxi` nella casella di testo **Keyspace name**.</span><span class="sxs-lookup"><span data-stu-id="fad66-247">When the **add table** blade opens, enter `newyorktaxi` in the **Keyspace name** text box.</span></span> 

4. <span data-ttu-id="fad66-248">Nella sezione **enter CQL command to create the table** immettere `neighborhoodstats` nella casella di testo accanto a `newyorktaxi`.</span><span class="sxs-lookup"><span data-stu-id="fad66-248">In the **enter CQL command to create the table** section, enter `neighborhoodstats` in the text box beside `newyorktaxi`.</span></span>

5. <span data-ttu-id="fad66-249">Nella casella di testo seguente, immettere:</span><span class="sxs-lookup"><span data-stu-id="fad66-249">In the text box below, enter the following::</span></span>
```shell
(neighborhood text, window_end timestamp, number_of_rides bigint,total_fare_amount double, primary key(neighborhood, window_end))
```
6. <span data-ttu-id="fad66-250">Nella casella di testo **Throughput (1,000 - 1,000,000 RU/s)** immettere il valore `4000`.</span><span class="sxs-lookup"><span data-stu-id="fad66-250">In the **Throughput (1,000 - 1,000,000 RU/s)** text box enter the value `4000`.</span></span>

7. <span data-ttu-id="fad66-251">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="fad66-251">Click **OK**.</span></span>

### <a name="add-the-databricks-secrets-using-the-databricks-cli"></a><span data-ttu-id="fad66-252">Aggiungere i segreti di Databricks usando l'interfaccia della riga di comando di Databricks</span><span class="sxs-lookup"><span data-stu-id="fad66-252">Add the Databricks secrets using the Databricks CLI</span></span>

<span data-ttu-id="fad66-253">Prima di tutto immettere i segreti per EventHub:</span><span class="sxs-lookup"><span data-stu-id="fad66-253">First, enter the secrets for EventHub:</span></span>

1. <span data-ttu-id="fad66-254">Usando l'**interfaccia della riga di comando di Azure Databricks** installata al passaggio 2 dei prerequisiti, creare l'ambito del segreto di Azure Databricks:</span><span class="sxs-lookup"><span data-stu-id="fad66-254">Using the **Azure Databricks CLI** installed in step 2 of the prerequisites, create the Azure Databricks secret scope:</span></span>
    ```shell
    databricks secrets create-scope --scope "azure-databricks-job"
    ```
2. <span data-ttu-id="fad66-255">Aggiungere il segreto per l'EventHub della corsa in taxi:</span><span class="sxs-lookup"><span data-stu-id="fad66-255">Add the secret for the taxi ride EventHub:</span></span>
    ```shell
    databricks secrets put --scope "azure-databricks-job" --key "taxi-ride"
    ```
    <span data-ttu-id="fad66-256">Una volta eseguito, questo comando apre l'editor vi.</span><span class="sxs-lookup"><span data-stu-id="fad66-256">Once executed, this command opens the vi editor.</span></span> <span data-ttu-id="fad66-257">Immettere il valore **taxi-ride-eh** dalla sezione output **eventHubs** al passaggio 4 della sezione *Distribuire le risorse di Azure*.</span><span class="sxs-lookup"><span data-stu-id="fad66-257">Enter the **taxi-ride-eh** value from the **eventHubs** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="fad66-258">Salvare e chiudere vi.</span><span class="sxs-lookup"><span data-stu-id="fad66-258">Save and exit vi.</span></span>

3. <span data-ttu-id="fad66-259">Aggiungere il segreto per l'EventHub della tariffa del taxi:</span><span class="sxs-lookup"><span data-stu-id="fad66-259">Add the secret for the taxi fare EventHub:</span></span>
    ```shell
    databricks secrets put --scope "azure-databricks-job" --key "taxi-fare"
    ```
    <span data-ttu-id="fad66-260">Una volta eseguito, questo comando apre l'editor vi.</span><span class="sxs-lookup"><span data-stu-id="fad66-260">Once executed, this command opens the vi editor.</span></span> <span data-ttu-id="fad66-261">Immettere il valore **taxi-fare-eh** dalla sezione output **eventHubs** al passaggio 4 della sezione *Distribuire le risorse di Azure*.</span><span class="sxs-lookup"><span data-stu-id="fad66-261">Enter the **taxi-fare-eh** value from the **eventHubs** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="fad66-262">Salvare e chiudere vi.</span><span class="sxs-lookup"><span data-stu-id="fad66-262">Save and exit vi.</span></span>

<span data-ttu-id="fad66-263">Quindi, immettere i segreti per Cosmos DB:</span><span class="sxs-lookup"><span data-stu-id="fad66-263">Next, enter the secrets for Cosmos DB:</span></span>

1. <span data-ttu-id="fad66-264">Aprire il portale di Azure e passare al gruppo di risorse specificato al passaggio 3 della sezione **Distribuire le risorse di Azure**.</span><span class="sxs-lookup"><span data-stu-id="fad66-264">Open the Azure portal, and navigate to the resource group specified in step 3 of the **deploy the Azure resources** section.</span></span> <span data-ttu-id="fad66-265">Fare clic sull'account Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="fad66-265">Click on the Azure Cosmos DB Account.</span></span>

2. <span data-ttu-id="fad66-266">Usando l'**interfaccia della riga di comando di Azure Databricks**, aggiungere il segreto per il nome utente di Cosmos DB:</span><span class="sxs-lookup"><span data-stu-id="fad66-266">Using the **Azure Databricks CLI**, add the secret for the Cosmos DB user name:</span></span>
    ```shell
    databricks secrets put --scope azure-databricks-job --key "cassandra-username"
    ```
<span data-ttu-id="fad66-267">Una volta eseguito, questo comando apre l'editor vi.</span><span class="sxs-lookup"><span data-stu-id="fad66-267">Once executed, this command opens the vi editor.</span></span> <span data-ttu-id="fad66-268">Immettere il valore **username** dalla sezione output **CosmosDb** al passaggio 4 della sezione *Distribuire le risorse di Azure*.</span><span class="sxs-lookup"><span data-stu-id="fad66-268">Enter the **username** value from the **CosmosDb** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="fad66-269">Salvare e chiudere vi.</span><span class="sxs-lookup"><span data-stu-id="fad66-269">Save and exit vi.</span></span>

3. <span data-ttu-id="fad66-270">Successivamente, aggiungere il segreto della password di Cosmos DB:</span><span class="sxs-lookup"><span data-stu-id="fad66-270">Next, add the secret for the Cosmos DB password:</span></span>
    ```shell
    databricks secrets put --scope azure-databricks-job --key "cassandra-password"
    ```

<span data-ttu-id="fad66-271">Una volta eseguito, questo comando apre l'editor vi.</span><span class="sxs-lookup"><span data-stu-id="fad66-271">Once executed, this command opens the vi editor.</span></span> <span data-ttu-id="fad66-272">Immettere il valore **secret** dalla sezione output **CosmosDb** al passaggio 4 della sezione *Distribuire le risorse di Azure*.</span><span class="sxs-lookup"><span data-stu-id="fad66-272">Enter the **secret** value from the **CosmosDb** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="fad66-273">Salvare e chiudere vi.</span><span class="sxs-lookup"><span data-stu-id="fad66-273">Save and exit vi.</span></span>

> [!NOTE]
> <span data-ttu-id="fad66-274">Se si usa un [ambito segreto di cui è stato eseguito il backup in Azure Key Vault](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes), l'ambito deve essere denominato **azure-databricks-job** e i segreti devono avere gli stessi nomi esatti di quelli precedenti.</span><span class="sxs-lookup"><span data-stu-id="fad66-274">If using an [Azure Key Vault-backed secret scope](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#azure-key-vault-backed-scopes), the scope must be named **azure-databricks-job** and the secrets must have the exact same names as those above.</span></span>

### <a name="add-the-zillow-neighborhoods-data-file-to-the-databricks-file-system"></a><span data-ttu-id="fad66-275">Aggiungere il file di dati Zillow Neighborhoods al file system di Databricks</span><span class="sxs-lookup"><span data-stu-id="fad66-275">Add the Zillow Neighborhoods data file to the Databricks file system</span></span>

1. <span data-ttu-id="fad66-276">Creare una directory nel file system di Databricks:</span><span class="sxs-lookup"><span data-stu-id="fad66-276">Create a directory in the Databricks file system:</span></span>
    ```bash
    dbfs mkdirs dbfs:/azure-databricks-jobs
    ```

2. <span data-ttu-id="fad66-277">Passare alla directory `DataFile` e immettere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fad66-277">Navigate to the `DataFile` directory and enter the following:</span></span>
    ```bash
    dbfs cp ZillowNeighborhoods-NY.zip dbfs:/azure-databricks-jobs
    ```

### <a name="add-the-azure-log-analytics-workspace-id-and-primary-key-to-configuration-files"></a><span data-ttu-id="fad66-278">Aggiungere l'ID e la chiave primaria dell'area di lavoro di Log Analytics ai file di configurazione</span><span class="sxs-lookup"><span data-stu-id="fad66-278">Add the Azure Log Analytics workspace ID and primary key to configuration files</span></span>

<span data-ttu-id="fad66-279">Per questa sezione, sono richiesti l'ID e la chiave primaria dell'area di lavoro di Log Analytics.</span><span class="sxs-lookup"><span data-stu-id="fad66-279">For this section, you require the Log Analytics workspace ID and primary key.</span></span> <span data-ttu-id="fad66-280">L'ID dell'area di lavoro è il valore **workspaceId** della sezione di output **logAnalytics** al passaggio 4 della sezione *Distribuire le risorse di Azure*.</span><span class="sxs-lookup"><span data-stu-id="fad66-280">The workspace ID is the **workspaceId** value from the **logAnalytics** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="fad66-281">La chiave primaria è il **segreto** della sezione di output.</span><span class="sxs-lookup"><span data-stu-id="fad66-281">The primary key is the **secret** from the output section.</span></span> 

1. <span data-ttu-id="fad66-282">Per configurare la registrazione di log4j, aprire `\azure\AzureDataBricksJob\src\main\resources\com\microsoft\pnp\azuredatabricksjob\log4j.properties`.</span><span class="sxs-lookup"><span data-stu-id="fad66-282">To configure log4j logging, open `\azure\AzureDataBricksJob\src\main\resources\com\microsoft\pnp\azuredatabricksjob\log4j.properties`.</span></span> <span data-ttu-id="fad66-283">Modificare i due valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="fad66-283">Edit the following two values:</span></span>
    ```shell
    log4j.appender.A1.workspaceId=<Log Analytics workspace ID>
    log4j.appender.A1.secret=<Log Analytics primary key>
    ```

2. <span data-ttu-id="fad66-284">Per configurare la registrazione personalizzata, aprire `\azure\azure-databricks-monitoring\scripts\metrics.properties`.</span><span class="sxs-lookup"><span data-stu-id="fad66-284">To configure custom logging, open `\azure\azure-databricks-monitoring\scripts\metrics.properties`.</span></span> <span data-ttu-id="fad66-285">Modificare i due valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="fad66-285">Edit the following two values:</span></span>
    ```shell
    *.sink.loganalytics.workspaceId=<Log Analytics workspace ID>
    *.sink.loganalytics.secret=<Log Analytics primary key>
    ```

### <a name="build-the-jar-files-for-the-databricks-job-and-databricks-monitoring"></a><span data-ttu-id="fad66-286">Compilare i file JAR per il processo e il monitoraggio Databricks</span><span class="sxs-lookup"><span data-stu-id="fad66-286">Build the .jar files for the Databricks job and Databricks monitoring</span></span>

1. <span data-ttu-id="fad66-287">Usare l'ambiente IDE Java per importare il file di progetto Maven denominato **pom.xml** situato nella radice della directory radice.</span><span class="sxs-lookup"><span data-stu-id="fad66-287">Use your Java IDE to import the Maven project file named **pom.xml** located in the root directory.</span></span> 

2. <span data-ttu-id="fad66-288">Eseguire una compilazione pulita.</span><span class="sxs-lookup"><span data-stu-id="fad66-288">Perform a clean build.</span></span> <span data-ttu-id="fad66-289">L'output di questa compilazione sono file denominati **azure-databricks-job-1.0-SNAPSHOT.jar** e **azure-databricks-monitoring-0.9.jar**.</span><span class="sxs-lookup"><span data-stu-id="fad66-289">The output of this build is files named **azure-databricks-job-1.0-SNAPSHOT.jar** and **azure-databricks-monitoring-0.9.jar**.</span></span> 

### <a name="configure-custom-logging-for-the-databricks-job"></a><span data-ttu-id="fad66-290">Configurare la registrazione personalizzata per il processo di Databricks</span><span class="sxs-lookup"><span data-stu-id="fad66-290">Configure custom logging for the Databricks job</span></span>

1. <span data-ttu-id="fad66-291">Copiare il file **azure-databricks-monitoring-0.9.jar** nel file system di Databricks immettendo il comando seguente nell'**interfaccia della riga di comando di Databricks**:</span><span class="sxs-lookup"><span data-stu-id="fad66-291">Copy the **azure-databricks-monitoring-0.9.jar** file to the Databricks file system by entering the following command in the **Databricks CLI**:</span></span>
    ```shell
    databricks fs cp --overwrite azure-databricks-monitoring-0.9.jar dbfs:/azure-databricks-job/azure-databricks-monitoring-0.9.jar
    ```

2. <span data-ttu-id="fad66-292">Copiare le proprietà di registrazione personalizzate da `\azure\azure-databricks-monitoring\scripts\metrics.properties` nel file system di Databricks immettendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fad66-292">Copy the custom logging properties from `\azure\azure-databricks-monitoring\scripts\metrics.properties` to the Databricks file system by entering the following command:</span></span>
    ```shell
    databricks fs cp --overwrite metrics.properties dbfs:/azure-databricks-job/metrics.properties
    ```

3. <span data-ttu-id="fad66-293">Selezionare adesso un nome per il cluster di Databricks.</span><span class="sxs-lookup"><span data-stu-id="fad66-293">While you haven't yet decided on a name for your Databricks cluster, select one now.</span></span> <span data-ttu-id="fad66-294">Immetterlo nel percorso del file system di Databricks relativo al cluster.</span><span class="sxs-lookup"><span data-stu-id="fad66-294">You'll enter the name below in the Databricks file system path for your cluster.</span></span> <span data-ttu-id="fad66-295">Copiare lo script di inizializzazione da `\azure\azure-databricks-monitoring\scripts\spark.metrics` nel file system di Databricks immettendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="fad66-295">Copy the initialization script from `\azure\azure-databricks-monitoring\scripts\spark.metrics` to the Databricks file system by entering the following command:</span></span>
    ```
    databricks fs cp --overwrite spark-metrics.sh dbfs:/databricks/init/<cluster-name>/spark-metrics.sh
    ```

### <a name="create-a-databricks-cluster"></a><span data-ttu-id="fad66-296">Creare un cluster di Databricks</span><span class="sxs-lookup"><span data-stu-id="fad66-296">Create a Databricks cluster</span></span>

1. <span data-ttu-id="fad66-297">Nell'area di lavoro di Databricks, fare clic su "Clusters", quindi fare clic su "create cluster".</span><span class="sxs-lookup"><span data-stu-id="fad66-297">In the Databricks workspace, click "Clusters", then click "create cluster".</span></span> <span data-ttu-id="fad66-298">Immettere il nome del cluster creato al passaggio 3 della sezione **Configurare la registrazione personalizzata per il processo di Databricks** precedente.</span><span class="sxs-lookup"><span data-stu-id="fad66-298">Enter the cluster name you created in step 3 of the **configure custom logging for the Databricks job** section above.</span></span> 

2. <span data-ttu-id="fad66-299">Selezionare una modalità cluster **standard**.</span><span class="sxs-lookup"><span data-stu-id="fad66-299">Select a **standard** cluster mode.</span></span>

3. <span data-ttu-id="fad66-300">Impostare la **versione del runtime di Databricks** su **4.3 (include Apache Spark 2.3.1, Scala 2.11)**</span><span class="sxs-lookup"><span data-stu-id="fad66-300">Set **Databricks runtime version** to **4.3 (includes Apache Spark 2.3.1, Scala 2.11)**</span></span>

4. <span data-ttu-id="fad66-301">Impostare la **versione di Python** su **2**.</span><span class="sxs-lookup"><span data-stu-id="fad66-301">Set **Python version** to **2**.</span></span>

5. <span data-ttu-id="fad66-302">Impostare il **tipo driver** su **Same as worker**.</span><span class="sxs-lookup"><span data-stu-id="fad66-302">Set **Driver Type** to **Same as worker**</span></span>

6. <span data-ttu-id="fad66-303">Impostare il **tipo di ruolo di lavoro** su **Standard_DS3_v2**.</span><span class="sxs-lookup"><span data-stu-id="fad66-303">Set **Worker Type** to **Standard_DS3_v2**.</span></span>

7. <span data-ttu-id="fad66-304">Impostare **Min Workers** su **2**.</span><span class="sxs-lookup"><span data-stu-id="fad66-304">Set **Min Workers** to **2**.</span></span>

8. <span data-ttu-id="fad66-305">Deselezionare **Enable autoscaling** (Abilita la scalabilità automatica).</span><span class="sxs-lookup"><span data-stu-id="fad66-305">Deselect **Enable autoscaling**.</span></span> 

9. <span data-ttu-id="fad66-306">Sotto la finestra di dialogo **Auto Termination** fare clic su **Init Scripts**.</span><span class="sxs-lookup"><span data-stu-id="fad66-306">Below the **Auto Termination** dialog box, click on **Init Scripts**.</span></span> 

10. <span data-ttu-id="fad66-307">Immettere **dbfs:/databricks/init/<nome-cluster>/spark-metrics.sh**, sostituendo il nome del cluster creato al passaggio 1 per <nome-cluster>.</span><span class="sxs-lookup"><span data-stu-id="fad66-307">Enter **dbfs:/databricks/init/<cluster-name>/spark-metrics.sh**, substituting the cluster name created in step 1 for <cluster-name>.</span></span>

11. <span data-ttu-id="fad66-308">Fare clic su **Add** .</span><span class="sxs-lookup"><span data-stu-id="fad66-308">Click the **Add** button.</span></span>

12. <span data-ttu-id="fad66-309">Fare clic sul pulsante **Create Cluster**.</span><span class="sxs-lookup"><span data-stu-id="fad66-309">Click the **Create Cluster** button.</span></span>

### <a name="create-a-databricks-job"></a><span data-ttu-id="fad66-310">Creare un processo di Databricks</span><span class="sxs-lookup"><span data-stu-id="fad66-310">Create a Databricks job</span></span>

1. <span data-ttu-id="fad66-311">Nell'area di lavoro di Databricks, fare clic su "Jobs", quindi fare clic su "create job".</span><span class="sxs-lookup"><span data-stu-id="fad66-311">In the Databricks workspace, click "Jobs", "create job".</span></span>

2. <span data-ttu-id="fad66-312">Immettere un nome per il processo.</span><span class="sxs-lookup"><span data-stu-id="fad66-312">Enter a job name.</span></span>

3. <span data-ttu-id="fad66-313">Fare clic su "set jar" per aprire la finestra di dialogo "Upload JAR to Run".</span><span class="sxs-lookup"><span data-stu-id="fad66-313">Click "set jar", this opens the "Upload JAR to Run" dialog box.</span></span>

4. <span data-ttu-id="fad66-314">Trascinare il file **azure-databricks-job-1.0-SNAPSHOT.jar** creato nella sezione **Compilare i file JAR per il processo e il monitoraggio Databricks** nella casella **Drop JAR here to upload**.</span><span class="sxs-lookup"><span data-stu-id="fad66-314">Drag the **azure-databricks-job-1.0-SNAPSHOT.jar** file you created in the **build the .jar for the Databricks job** section to the **Drop JAR here to upload** box.</span></span>

5. <span data-ttu-id="fad66-315">Immettere **com.microsoft.pnp.TaxiCabReader** nel campo **Main Class**.</span><span class="sxs-lookup"><span data-stu-id="fad66-315">Enter **com.microsoft.pnp.TaxiCabReader** in the **Main Class** field.</span></span>

6. <span data-ttu-id="fad66-316">Nel campo arguments, immettere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="fad66-316">In the arguments field, enter the following:</span></span>
    ```shell
    -n jar:file:/dbfs/azure-databricks-jobs/ZillowNeighborhoods-NY.zip!/ZillowNeighborhoods-NY.shp --taxi-ride-consumer-group taxi-ride-eh-cg --taxi-fare-consumer-group taxi-fare-eh-cg --window-interval "1 minute" --cassandra-host <Cosmos DB Cassandra host name from above> 
    ``` 

7. <span data-ttu-id="fad66-317">Per installare le librerie dipendenti, seguire questi passaggi:</span><span class="sxs-lookup"><span data-stu-id="fad66-317">Install the dependent libraries by following these steps:</span></span>
    
    1. <span data-ttu-id="fad66-318">Nell'interfaccia utente di Databricks, fare clic sul pulsante **home**.</span><span class="sxs-lookup"><span data-stu-id="fad66-318">In the Databricks user interface, click on the **home** button.</span></span>
    
    2. <span data-ttu-id="fad66-319">Nell'elenco a discesa **Users** fare clic sul nome del proprio account utente per accedere alle impostazioni dell'area di lavoro dell'account.</span><span class="sxs-lookup"><span data-stu-id="fad66-319">In the **Users** drop-down, click on your user account name to open your account workspace settings.</span></span>
    
    3. <span data-ttu-id="fad66-320">Fare clic sulla freccia giù accanto al nome dell'account, fare clic su **create**e quindi su **Library** per aprire la finestra di dialogo **New Library**.</span><span class="sxs-lookup"><span data-stu-id="fad66-320">Click on the drop-down arrow beside your account name, click on **create**, and click on **Library** to open the **New Library** dialog.</span></span>
    
    4. <span data-ttu-id="fad66-321">Nel controllo a discesa **Source** selezionare **Maven Coordinate**.</span><span class="sxs-lookup"><span data-stu-id="fad66-321">In the **Source** drop-down control, select **Maven Coordinate**.</span></span>
    
    5. <span data-ttu-id="fad66-322">Sotto l'intestazione **Install Maven Artifacts** immettere `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.5` nella casella di testo **Coordinate**.</span><span class="sxs-lookup"><span data-stu-id="fad66-322">Under the **Install Maven Artifacts** heading, enter `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.5` in the **Coordinate** text box.</span></span> 
    
    6. <span data-ttu-id="fad66-323">Fare clic su **Create Library** per aprire la finestra **Artifacts**.</span><span class="sxs-lookup"><span data-stu-id="fad66-323">Click on **Create Library** to open the **Artifacts** window.</span></span>
    
    7. <span data-ttu-id="fad66-324">Sotto **Status on running clusters** selezionare la casella di controllo **Attach automatically to all clusters**.</span><span class="sxs-lookup"><span data-stu-id="fad66-324">Under **Status on running clusters** check the **Attach automatically to all clusters** checkbox.</span></span>
    
    8. <span data-ttu-id="fad66-325">Ripetere i passaggi da 1 a 7 per la coordinata Maven `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`.</span><span class="sxs-lookup"><span data-stu-id="fad66-325">Repeat steps 1 - 7 for the `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` Maven coordinate.</span></span>
    
    9. <span data-ttu-id="fad66-326">Ripetere i passaggi da 1 a 6 per la coordinata Maven `org.geotools:gt-shapefile:19.2`.</span><span class="sxs-lookup"><span data-stu-id="fad66-326">Repeat steps 1 - 6 for the `org.geotools:gt-shapefile:19.2` Maven coordinate.</span></span>
    
    10. <span data-ttu-id="fad66-327">Fare clic su **Advanced Options**.</span><span class="sxs-lookup"><span data-stu-id="fad66-327">Click on **Advanced Options**.</span></span>
    
    11. <span data-ttu-id="fad66-328">Immettere `http://download.osgeo.org/webdav/geotools/` nella casella di testo **Repository**.</span><span class="sxs-lookup"><span data-stu-id="fad66-328">Enter `http://download.osgeo.org/webdav/geotools/` in the **Repository** text box.</span></span> 
    
    12. <span data-ttu-id="fad66-329">Fare clic su **Create Library** per aprire la finestra **Artifacts**.</span><span class="sxs-lookup"><span data-stu-id="fad66-329">Click **Create Library** to open the **Artifacts** window.</span></span> 
    
    13. <span data-ttu-id="fad66-330">Sotto **Status on running clusters** selezionare la casella di controllo **Attach automatically to all clusters**.</span><span class="sxs-lookup"><span data-stu-id="fad66-330">Under **Status on running clusters** check the **Attach automatically to all clusters** checkbox.</span></span>

8. <span data-ttu-id="fad66-331">Aggiungere le librerie dipendenti aggiunte al passaggio 7 per il processo creato alla fine del passaggio 6:</span><span class="sxs-lookup"><span data-stu-id="fad66-331">Add the dependent libraries added in step 7 to the job created at the end of step 6:</span></span>
    1. <span data-ttu-id="fad66-332">Nell'area di lavoro di Azure Databricks, fare clic su **Jobs**.</span><span class="sxs-lookup"><span data-stu-id="fad66-332">In the Azure Databricks workspace, click on **Jobs**.</span></span>

    2. <span data-ttu-id="fad66-333">Fare clic sul nome del processo creato al passaggio 2 della sezione **Creare un processo di Databricks**.</span><span class="sxs-lookup"><span data-stu-id="fad66-333">Click on the job name created in step 2 of the **create a Databricks job** section.</span></span> 
    
    3. <span data-ttu-id="fad66-334">Accanto alla sezione **Dependent Libraries** fare clic su **Add** per aprire la finestra di dialogo **Add Dependent Library**.</span><span class="sxs-lookup"><span data-stu-id="fad66-334">Beside the **Dependent Libraries** section, click on **Add** to open the **Add Dependent Library** dialog.</span></span> 
    
    4. <span data-ttu-id="fad66-335">Sotto **Library From** selezionare **Workspace**.</span><span class="sxs-lookup"><span data-stu-id="fad66-335">Under **Library From** select **Workspace**.</span></span>
    
    5. <span data-ttu-id="fad66-336">Fare clic su **users**, quindi sul proprio nome utente e infine su `azure-eventhubs-spark_2.11:2.3.5`.</span><span class="sxs-lookup"><span data-stu-id="fad66-336">Click on **users**, then your username, then click on `azure-eventhubs-spark_2.11:2.3.5`.</span></span> 
    
    6. <span data-ttu-id="fad66-337">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="fad66-337">Click **OK**.</span></span>
    
    7. <span data-ttu-id="fad66-338">Ripetere i passaggi da 1 a 6 per `spark-cassandra-connector_2.11:2.3.1` e `gt-shapefile:19.2`.</span><span class="sxs-lookup"><span data-stu-id="fad66-338">Repeat steps 1 - 6 for `spark-cassandra-connector_2.11:2.3.1` and `gt-shapefile:19.2`.</span></span>

9. <span data-ttu-id="fad66-339">Accanto a **Cluster:**, fare clic su **Edit**.</span><span class="sxs-lookup"><span data-stu-id="fad66-339">Beside **Cluster:**, click on **Edit**.</span></span> <span data-ttu-id="fad66-340">Verrà visualizzata la finestra di dialogo **Configure Cluster**.</span><span class="sxs-lookup"><span data-stu-id="fad66-340">This opens the **Configure Cluster** dialog.</span></span> <span data-ttu-id="fad66-341">Nell'elenco a discesa **Cluster Type** selezionare **Existing Cluster**.</span><span class="sxs-lookup"><span data-stu-id="fad66-341">In the **Cluster Type** drop-down, select **Existing Cluster**.</span></span> <span data-ttu-id="fad66-342">Nell'elenco a discesa **Select Cluster** selezionare il cluster creato nella sezione **Creare un cluster di Databricks**.</span><span class="sxs-lookup"><span data-stu-id="fad66-342">In the **Select Cluster** drop-down, select the cluster created the **create a Databricks cluster** section.</span></span> <span data-ttu-id="fad66-343">Fare clic su **confirm**.</span><span class="sxs-lookup"><span data-stu-id="fad66-343">Click **confirm**.</span></span>

10. <span data-ttu-id="fad66-344">Fare clic su **run now**.</span><span class="sxs-lookup"><span data-stu-id="fad66-344">Click **run now**.</span></span>

### <a name="run-the-data-generator"></a><span data-ttu-id="fad66-345">Eseguire il generatore di dati</span><span class="sxs-lookup"><span data-stu-id="fad66-345">Run the data generator</span></span>

1. <span data-ttu-id="fad66-346">Passare alla directory denominata `onprem` nel repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="fad66-346">Navigate to the directory named `onprem` in the GitHub repository.</span></span>

2. <span data-ttu-id="fad66-347">Aggiornare i valori nel file **main.env** come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="fad66-347">Update the values in the file **main.env** as follows:</span></span>

    ```shell
    RIDE_EVENT_HUB=[Connection string for the taxi-ride event hub]
    FARE_EVENT_HUB=[Connection string for the taxi-fare event hub]
    RIDE_DATA_FILE_PATH=/DataFile/FOIL2013
    MINUTES_TO_LEAD=0
    PUSH_RIDE_DATA_FIRST=false
    ```
    <span data-ttu-id="fad66-348">La stringa di connessione per l'hub eventi taxi-ride è il valore **taxi-ride-eh** dalla sezione output **eventHubs** al passaggio 4 della sezione *Distribuire le risorse di Azure*.</span><span class="sxs-lookup"><span data-stu-id="fad66-348">The connection string for the taxi-ride event hub is the **taxi-ride-eh** value from the **eventHubs** output section in step 4 of the *deploy the Azure resources* section.</span></span> <span data-ttu-id="fad66-349">La stringa di connessione per l'hub eventi taxi-fare è il valore **taxi-fare-eh** dalla sezione output **eventHubs** al passaggio 4 della sezione *Distribuire le risorse di Azure*.</span><span class="sxs-lookup"><span data-stu-id="fad66-349">The connection string for the taxi-fare event hub the **taxi-fare-eh** value from the **eventHubs** output section in step 4 of the *deploy the Azure resources* section.</span></span>

3. <span data-ttu-id="fad66-350">Eseguire questo comando per creare l'immagine Docker.</span><span class="sxs-lookup"><span data-stu-id="fad66-350">Run the following command to build the Docker image.</span></span>

    ```bash
    docker build --no-cache -t dataloader .
    ```

4. <span data-ttu-id="fad66-351">Tornare alla directory padre.</span><span class="sxs-lookup"><span data-stu-id="fad66-351">Navigate back to the parent directory.</span></span>

    ```bash
    cd ..
    ```

5. <span data-ttu-id="fad66-352">Eseguire questo comando per eseguire l'immagine Docker.</span><span class="sxs-lookup"><span data-stu-id="fad66-352">Run the following command to run the Docker image.</span></span>

    ```bash
    docker run -v `pwd`/DataFile:/DataFile --env-file=onprem/main.env dataloader:latest
    ```

<span data-ttu-id="fad66-353">L'output sarà simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="fad66-353">The output should look like the following:</span></span>

```
Created 10000 records for TaxiFare
Created 10000 records for TaxiRide
Created 20000 records for TaxiFare
Created 20000 records for TaxiRide
Created 30000 records for TaxiFare
...
```

<span data-ttu-id="fad66-354">Per verificare che il processo di Databricks venga eseguito in modo corretto, aprire il portale di Azure e passare al database di Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="fad66-354">To verify the Databricks job is running correctly, open the Azure portal and navigate to the Cosmos DB database.</span></span> <span data-ttu-id="fad66-355">Aprire il pannello **Esplora dati** ed esaminare i dati nella tabella **taxi records**.</span><span class="sxs-lookup"><span data-stu-id="fad66-355">Open the **Data Explorer** blade and examine the data in the **taxi records** table.</span></span> 

<span data-ttu-id="fad66-356">[1] <span id="note1">Donovan, Brian; Work, Dan (2016): New York City Taxi Trip Data (2010-2013).</span><span class="sxs-lookup"><span data-stu-id="fad66-356">[1] <span id="note1">Donovan, Brian; Work, Dan (2016): New York City Taxi Trip Data (2010-2013).</span></span> <span data-ttu-id="fad66-357">University of Illinois at Urbana-Champaign.</span><span class="sxs-lookup"><span data-stu-id="fad66-357">University of Illinois at Urbana-Champaign.</span></span> <span data-ttu-id="fad66-358">https://doi.org/10.13012/J8PN93H8</span><span class="sxs-lookup"><span data-stu-id="fad66-358">https://doi.org/10.13012/J8PN93H8</span></span>
