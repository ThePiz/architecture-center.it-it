---
title: Antipattern di persistenza monolitica
description: L'inserimento di tutti i dati dell'applicazione in un unico archivio dati può influire negativamente sulle prestazioni.
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: 7f04b9f0805c281068b6b2edaf040683773e6f6e
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
ms.locfileid: "24538682"
---
# <a name="monolithic-persistence-antipattern"></a><span data-ttu-id="2f206-103">Antipattern di persistenza monolitica</span><span class="sxs-lookup"><span data-stu-id="2f206-103">Monolithic Persistence antipattern</span></span>

<span data-ttu-id="2f206-104">L'inserimento di tutti i dati di un'applicazione in un unico archivio dati può influire negativamente sulle prestazioni o perché causa una contesa delle risorse o perché l'archivio dati non è adatto per alcuni dei dati.</span><span class="sxs-lookup"><span data-stu-id="2f206-104">Putting all of an application's data into a single data store can hurt performance, either because it leads to resource contention, or because the data store is not a good fit for some of the data.</span></span>

## <a name="problem-description"></a><span data-ttu-id="2f206-105">Descrizione del problema</span><span class="sxs-lookup"><span data-stu-id="2f206-105">Problem description</span></span>

<span data-ttu-id="2f206-106">In passato le applicazioni usavano spesso un unico archivio dati, indipendentemente dai diversi tipi di dati che l'applicazione doveva archiviare.</span><span class="sxs-lookup"><span data-stu-id="2f206-106">Historically, applications have often used a single data store, regardless of the different types of data that the application might need to store.</span></span> <span data-ttu-id="2f206-107">In genere di faceva questo per semplificare la progettazione dell'applicazione, oppure per conformità con il set di competenze del team di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="2f206-107">Usually this was done to simplify the application design, or else to match the existing skill set of the development team.</span></span> 

<span data-ttu-id="2f206-108">I moderni sistemi basati su cloud hanno spesso requisiti ulteriori funzionali e non funzionali e devono archiviare molti tipi di dati eterogenei, come documenti, immagini, dati memorizzati nella cache, messaggi in coda, log di applicazioni e dati di telemetria.</span><span class="sxs-lookup"><span data-stu-id="2f206-108">Modern cloud-based systems often have additional functional and nonfunctional requirements, and need to store many heterogenous types of data, such as documents, images, cached data, queued messages, application logs, and telemetry.</span></span> <span data-ttu-id="2f206-109">Seguendo l'approccio tradizionale e inserendo tutte queste informazioni nello stesso archivio dati, si può influire negativamente sulle prestazioni, per due motivi:</span><span class="sxs-lookup"><span data-stu-id="2f206-109">Following the traditional approach and putting all of this information into the same data store can hurt performance, for two main reasons:</span></span>

- <span data-ttu-id="2f206-110">Archiviare e recuperare grandi quantità di dati non correlati nello stesso archivio dati può causare contesa, che a sua volta rallenta i tempi di risposta e causa errori di connessione.</span><span class="sxs-lookup"><span data-stu-id="2f206-110">Storing and retrieving large amounts of unrelated data in the same data store can cause contention, which in turn leads to slow response times and connection failures.</span></span>
- <span data-ttu-id="2f206-111">Qualsiasi archivio dati si scelga, potrebbe non essere la scelta migliore per tutti i diversi tipi di dati o non potrebbe non essere ottimizzato per le operazioni eseguite dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="2f206-111">Whichever data store is chosen, it might not be the best fit for all of the different types of data, or it might not be optimized for the operations that the application performs.</span></span> 

<span data-ttu-id="2f206-112">L'esempio seguente illustra un controller API Web ASP.NET che aggiunge un nuovo record a un database e registra anche il risultato in un log.</span><span class="sxs-lookup"><span data-stu-id="2f206-112">The following example shows an ASP.NET Web API controller that adds a new record to a database and also records the result to a log.</span></span> <span data-ttu-id="2f206-113">Il log viene mantenuto attivo nello stesso database dei dati aziendali.</span><span class="sxs-lookup"><span data-stu-id="2f206-113">The log is held in the same database as the business data.</span></span> <span data-ttu-id="2f206-114">L'esempio completo è disponibile [qui][sample-app].</span><span class="sxs-lookup"><span data-stu-id="2f206-114">You can find the complete sample [here][sample-app].</span></span>

```csharp
public class MonoController : ApiController
{
    private static readonly string ProductionDb = ...;

    public async Task<IHttpActionResult> PostAsync([FromBody]string value)
    {
        await DataAccess.InsertPurchaseOrderHeaderAsync(ProductionDb);
        await DataAccess.LogAsync(ProductionDb, LogTableName);
        return Ok();
    }
}
```

<span data-ttu-id="2f206-115">La frequenza con cui vengono generati i record di log probabilmente influirà sulle prestazioni delle operazioni di business.</span><span class="sxs-lookup"><span data-stu-id="2f206-115">The rate at which log records are generated will probably affect the performance of the business operations.</span></span> <span data-ttu-id="2f206-116">E se un altro componente, ad esempio un monitor di processi applicativi, legge ed elabora regolarmente i dati di log, anche questo può influire sulle operazioni di business.</span><span class="sxs-lookup"><span data-stu-id="2f206-116">And if another component, such as an application process monitor, regularly reads and processes the log data, that can also affect the business operations.</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="2f206-117">Come risolvere il problema</span><span class="sxs-lookup"><span data-stu-id="2f206-117">How to fix the problem</span></span>

<span data-ttu-id="2f206-118">Separare i dati in base al relativo utilizzo.</span><span class="sxs-lookup"><span data-stu-id="2f206-118">Separate data according to its use.</span></span> <span data-ttu-id="2f206-119">Per ogni set di dati, selezionare un archivio dati che corrisponde alla modalità di utilizzo del set di dati.</span><span class="sxs-lookup"><span data-stu-id="2f206-119">For each data set, select a data store that best matches how that data set will be used.</span></span> <span data-ttu-id="2f206-120">Nell'esempio precedente l'applicazione deve creare i log in un archivio separato dal database che contiene i dati aziendali:</span><span class="sxs-lookup"><span data-stu-id="2f206-120">In the previous example, the application should be logging to a separate store from the database that holds business data:</span></span> 

```csharp
public class PolyController : ApiController
{
    private static readonly string ProductionDb = ...;
    private static readonly string LogDb = ...;

    public async Task<IHttpActionResult> PostAsync([FromBody]string value)
    {
        await DataAccess.InsertPurchaseOrderHeaderAsync(ProductionDb);
        // Log to a different data store.
        await DataAccess.LogAsync(LogDb, LogTableName);
        return Ok();
    }
}
```

## <a name="considerations"></a><span data-ttu-id="2f206-121">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="2f206-121">Considerations</span></span>

- <span data-ttu-id="2f206-122">Separare i dati in base al modo in cui sono utilizzati e al modo in cui vi si accede.</span><span class="sxs-lookup"><span data-stu-id="2f206-122">Separate data by the way it is used and how it is accessed.</span></span> <span data-ttu-id="2f206-123">Ad esempio, non archiviare informazioni di log e dati aziendali nello stesso archivio dati.</span><span class="sxs-lookup"><span data-stu-id="2f206-123">For example, don't store log information and business data in the same data store.</span></span> <span data-ttu-id="2f206-124">Questi tipi di dati hanno requisiti e modelli di accesso molto diversi.</span><span class="sxs-lookup"><span data-stu-id="2f206-124">These types of data have significantly different requirements and patterns of access.</span></span> <span data-ttu-id="2f206-125">I record di log sono intrinsecamente sequenziali, mentre i dati aziendali è più probabile che richiedano l'accesso casuale e spesso sono relazionali.</span><span class="sxs-lookup"><span data-stu-id="2f206-125">Log records are inherently sequential, while business data is more likely to require random access, and is often relational.</span></span>

- <span data-ttu-id="2f206-126">Prendere in considerazione il modello di accesso ai dati per ogni tipo di dati.</span><span class="sxs-lookup"><span data-stu-id="2f206-126">Consider the data access pattern for each type of data.</span></span> <span data-ttu-id="2f206-127">Ad esempio, archiviare i report e i documenti formattati in un database di documenti, come [DB Cosmos][CosmosDB], ma usare [Cache Redis di Azure][Azure-cache] per i dati temporanei di cache.</span><span class="sxs-lookup"><span data-stu-id="2f206-127">For example, store formatted reports and documents in a document database such as [Cosmos DB][CosmosDB], but use [Azure Redis Cache][Azure-cache] to cache temporary data.</span></span>

- <span data-ttu-id="2f206-128">Se si segue questo materiale sussidiario ma si raggiungono comunque i limiti del database, potrebbe essere necessario ridimensionare il database.</span><span class="sxs-lookup"><span data-stu-id="2f206-128">If you follow this guidance but still reach the limits of the database, you may need to scale up the database.</span></span> <span data-ttu-id="2f206-129">Considerare anche la scalabilità orizzontale e il partizionamento del carico tra i server di database.</span><span class="sxs-lookup"><span data-stu-id="2f206-129">Also consider scaling horizontally and partitioning the load across database servers.</span></span> <span data-ttu-id="2f206-130">Tuttavia, il partizionamento può richiedere la riprogettazione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="2f206-130">However, partitioning may require redesigning the application.</span></span> <span data-ttu-id="2f206-131">Per altre informazioni, vedere le informazioni sul [partizionamento dei dati][DataPartitioningGuidance].</span><span class="sxs-lookup"><span data-stu-id="2f206-131">For more information, see [Data partitioning][DataPartitioningGuidance].</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="2f206-132">Come rilevare il problema</span><span class="sxs-lookup"><span data-stu-id="2f206-132">How to detect the problem</span></span>

<span data-ttu-id="2f206-133">Il sistema probabilmente rallenterà molto e alla fine si bloccherà quando esaurirà le risorse, ad esempio le connessioni al database.</span><span class="sxs-lookup"><span data-stu-id="2f206-133">The system will likely slow down dramatically and eventually fail, as the system runs out of resources such as database connections.</span></span>

<span data-ttu-id="2f206-134">È possibile eseguire la procedura seguente per identificare la causa.</span><span class="sxs-lookup"><span data-stu-id="2f206-134">You can perform the following steps to help identify the cause.</span></span>

1. <span data-ttu-id="2f206-135">Instrumentare il sistema per registrare le statistiche di prestazioni chiave.</span><span class="sxs-lookup"><span data-stu-id="2f206-135">Instrument the system to record the key performance statistics.</span></span> <span data-ttu-id="2f206-136">Acquisire informazioni sui tempi per ogni operazione, nonché i punti in cui l'applicazione legge e scrive i dati.</span><span class="sxs-lookup"><span data-stu-id="2f206-136">Capture timing information for each operation, as well as the points where the application reads and writes data.</span></span>
1. <span data-ttu-id="2f206-137">Se possibile, monitorare il sistema in esecuzione per alcuni giorni in un ambiente di produzione per ottenere una visualizzazione reale dell'utilizzo del sistema.</span><span class="sxs-lookup"><span data-stu-id="2f206-137">If possible, monitor the system running for a few days in a production environment to get a real-world view of how the system is used.</span></span> <span data-ttu-id="2f206-138">Se questo non è possibile, eseguire test di carico tramite script con un volume realistico di utenti virtuali che eseguono una tipica serie di operazioni.</span><span class="sxs-lookup"><span data-stu-id="2f206-138">If this is not possible, run scripted load tests with a realistic volume of virtual users performing a typical series of operations.</span></span>
2. <span data-ttu-id="2f206-139">Usare i dati di telemetria per identificare i periodi di riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="2f206-139">Use the telemetry data to identify periods of poor performance.</span></span>
3. <span data-ttu-id="2f206-140">Identificare gli archivi di dati che erano accessibili durante tali periodi.</span><span class="sxs-lookup"><span data-stu-id="2f206-140">Identify which data stores were accessed during those periods.</span></span>
4. <span data-ttu-id="2f206-141">Identificare le risorse di archiviazione dati che potrebbero avere conflitti.</span><span class="sxs-lookup"><span data-stu-id="2f206-141">Identify data storage resources that might be experiencing contention.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="2f206-142">Diagnosi di esempio</span><span class="sxs-lookup"><span data-stu-id="2f206-142">Example diagnosis</span></span>

<span data-ttu-id="2f206-143">Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="2f206-143">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="instrument-and-monitor-the-system"></a><span data-ttu-id="2f206-144">Instrumentare e monitorare il sistema</span><span class="sxs-lookup"><span data-stu-id="2f206-144">Instrument and monitor the system</span></span>

<span data-ttu-id="2f206-145">Il grafico seguente mostra i risultati del test di carico dell'applicazione di esempio descritto sopra.</span><span class="sxs-lookup"><span data-stu-id="2f206-145">The following graph shows the results of load testing the sample application described earlier.</span></span> <span data-ttu-id="2f206-146">Il test ha utilizzato un carico per passaggio fino a 1000 utenti simultanei.</span><span class="sxs-lookup"><span data-stu-id="2f206-146">The test used a step load of up to 1000 concurrent users.</span></span>

![Risultati di prestazioni del test sul carico per il controller basato su SQL][MonolithicScenarioLoadTest]

<span data-ttu-id="2f206-148">Mentre il carico aumenta fino a 700 utenti, la velocità effettiva fa lo stesso.</span><span class="sxs-lookup"><span data-stu-id="2f206-148">As the load increases to 700 users, so does the throughput.</span></span> <span data-ttu-id="2f206-149">Ma a questo punto, la velocità effettiva si stabilizza, ma il sistema sembra eseguirsi alla capacità massima.</span><span class="sxs-lookup"><span data-stu-id="2f206-149">But at that point, throughput levels off, and the system appears to be running at its maximum capacity.</span></span> <span data-ttu-id="2f206-150">La risposta media aumenta gradualmente con il carico utente, il che mostra che il sistema non riesca a far fronte alla domanda.</span><span class="sxs-lookup"><span data-stu-id="2f206-150">The average response gradually increases with user load, showing that the system can't keep up with demand.</span></span>

### <a name="identify-periods-of-poor-performance"></a><span data-ttu-id="2f206-151">Identificare i periodi di riduzione delle prestazioni</span><span class="sxs-lookup"><span data-stu-id="2f206-151">Identify periods of poor performance</span></span>

<span data-ttu-id="2f206-152">Monitorando il sistema di produzione è possibile notare determinati schemi.</span><span class="sxs-lookup"><span data-stu-id="2f206-152">If you are monitoring the production system, you might notice patterns.</span></span> <span data-ttu-id="2f206-153">Ad esempio, i tempi di risposta potrebbero peggiorare notevolmente alla stessa ora ogni giorno.</span><span class="sxs-lookup"><span data-stu-id="2f206-153">For example, response times might drop off significantly at the same time each day.</span></span> <span data-ttu-id="2f206-154">Questo potrebbe essere causato da un carico di lavoro periodico o da un processo batch pianificato o semplicemente dal fatto che in determinati orari ci sono più utenti connessi al sistema.</span><span class="sxs-lookup"><span data-stu-id="2f206-154">This could be caused by a regular workload or scheduled batch job, or just because the system has more users at certain times.</span></span> <span data-ttu-id="2f206-155">È consigliabile concentrarsi sui dati di telemetria per questi eventi.</span><span class="sxs-lookup"><span data-stu-id="2f206-155">You should focus on the telemetry data for these events.</span></span>

<span data-ttu-id="2f206-156">Cercare correlazioni tra i tempi di risposta maggiori e l'aumento dell'attività del database o dell'I/O alle risorse condivise.</span><span class="sxs-lookup"><span data-stu-id="2f206-156">Look for correlations between increased response times and increased database activity or I/O to shared resources.</span></span> <span data-ttu-id="2f206-157">Se sono presenti correlazioni, significa che il database potrebbe rappresentare un collo di bottiglia.</span><span class="sxs-lookup"><span data-stu-id="2f206-157">If there are correlations, it means the database might be a bottleneck.</span></span>

### <a name="identify-which-data-stores-are-accessed-during-those-periods"></a><span data-ttu-id="2f206-158">Identificare gli archivi dati che sono accessibili durante tali periodi</span><span class="sxs-lookup"><span data-stu-id="2f206-158">Identify which data stores are accessed during those periods</span></span>

<span data-ttu-id="2f206-159">Il grafico successivo illustra l'uso delle unità di trasmissione dati (DTU) durante il test di carico.</span><span class="sxs-lookup"><span data-stu-id="2f206-159">The next graph shows the utilization of database throughput units (DTU) during the load test.</span></span> <span data-ttu-id="2f206-160">(una DTU è una misura della capacità disponibile ed è una combinazione di utilizzo della CPU, allocazione della memoria e velocità di I/O) L'utilizzo delle DTU ha raggiunto rapidamente il 100%.</span><span class="sxs-lookup"><span data-stu-id="2f206-160">(A DTU is a measure of available capacity, and is a combination of CPU utilization, memory allocation, I/O rate.) Utilization of DTUs quickly reached 100%.</span></span> <span data-ttu-id="2f206-161">Questo è approssimativamente il punto in cui la velocità effettiva ha avuto un picco nel grafico precedente.</span><span class="sxs-lookup"><span data-stu-id="2f206-161">This is roughly the point where throughput peaked in the previous graph.</span></span> <span data-ttu-id="2f206-162">L'utilizzo del database è rimasto molto elevato fino alla fine del test.</span><span class="sxs-lookup"><span data-stu-id="2f206-162">Database utilization remained very high until the test finished.</span></span> <span data-ttu-id="2f206-163">C'è un lieve calo verso la fine che potrebbe essere causato dalla limitazione, dalla concorrenza per le connessioni al database o da altri fattori.</span><span class="sxs-lookup"><span data-stu-id="2f206-163">There is a slight drop toward the end, which could be caused by throttling, competition for database connections, or other factors.</span></span>

![Il monitor del database nel portale di Azure classico che mostra l'utilizzo delle risorse del database][MonolithicDatabaseUtilization]

### <a name="examine-the-telemetry-for-the-data-stores"></a><span data-ttu-id="2f206-165">Esaminare la telemetria per gli archivi dati</span><span class="sxs-lookup"><span data-stu-id="2f206-165">Examine the telemetry for the data stores</span></span>

<span data-ttu-id="2f206-166">Instrumentare gli archivi dati per acquisire i dettagli di basso livello dell'attività.</span><span class="sxs-lookup"><span data-stu-id="2f206-166">Instrument the data stores to capture the low-level details of the activity.</span></span> <span data-ttu-id="2f206-167">Nell'applicazione di esempio, le statistiche di accesso ai dati hanno mostrato un volume elevato di operazioni di inserimento eseguite sia sulla tabella `PurchaseOrderHeader` che sulla tabella `MonoLog`.</span><span class="sxs-lookup"><span data-stu-id="2f206-167">In the sample application, the data access statistics showed a high volume of insert operations performed against both the `PurchaseOrderHeader` table and the `MonoLog` table.</span></span> 

![Le statistiche di accesso ai dati per l'applicazione di esempio][MonolithicDataAccessStats]

### <a name="identify-resource-contention"></a><span data-ttu-id="2f206-169">Identificare la contesa delle risorse</span><span class="sxs-lookup"><span data-stu-id="2f206-169">Identify resource contention</span></span>

<span data-ttu-id="2f206-170">A questo punto è possibile esaminare il codice sorgente, con particolare attenzione ai punti in cui l'applicazione accede a risorse contese.</span><span class="sxs-lookup"><span data-stu-id="2f206-170">At this point, you can review the source code, focusing on the points where contended resources are accessed by the application.</span></span> <span data-ttu-id="2f206-171">Cercare situazioni come:</span><span class="sxs-lookup"><span data-stu-id="2f206-171">Look for situations such as:</span></span>

- <span data-ttu-id="2f206-172">Dati logicamente separati che vengono scritti allo stesso archivio.</span><span class="sxs-lookup"><span data-stu-id="2f206-172">Data that is logically separate being written to the same store.</span></span> <span data-ttu-id="2f206-173">I dati come log, report e i messaggi in coda non devono essere mantenuti nello stesso database delle informazioni aziendali.</span><span class="sxs-lookup"><span data-stu-id="2f206-173">Data such as logs, reports, and queued messages should not be held in the same database as business information.</span></span>
- <span data-ttu-id="2f206-174">Una mancata corrispondenza tra il tipo di archivio dati e il tipo di dati, ad esempio BLOB di grandi dimensioni o documenti XML in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="2f206-174">A mismatch between the choice of data store and the type of data, such as large blobs or XML documents in a relational database.</span></span>
- <span data-ttu-id="2f206-175">Dati con modelli di utilizzo notevolmente diversi che condividono lo stesso archivio, ad esempio dati a elevata intensità di scrittura e bassa intensità di lettura archiviati insieme a dati a bassa intensità di scrittura e alta intensità di lettura.</span><span class="sxs-lookup"><span data-stu-id="2f206-175">Data with significantly different usage patterns that share the same store, such as high-write/low-read data being stored with low-write/high-read data.</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="2f206-176">Implementare la soluzione e verificare il risultato</span><span class="sxs-lookup"><span data-stu-id="2f206-176">Implement the solution and verify the result</span></span>

<span data-ttu-id="2f206-177">L'applicazione è stata modificata in modo che scriva i log in un archivio dati separato.</span><span class="sxs-lookup"><span data-stu-id="2f206-177">The application was changed to write logs to a separate data store.</span></span> <span data-ttu-id="2f206-178">Ecco i risultati del test di carico:</span><span class="sxs-lookup"><span data-stu-id="2f206-178">Here are the load test results:</span></span>

![Risultati di prestazioni del test di carico con l'uso del controller Polyglot][PolyglotScenarioLoadTest]

<span data-ttu-id="2f206-180">Il modello di velocità effettiva è simile al grafico precedente, ma il punto in cui le prestazioni raggiungono il picco è superiore di circa 500 richieste al secondo.</span><span class="sxs-lookup"><span data-stu-id="2f206-180">The pattern of throughput is similar to the earlier graph, but the point at which performance peaks is approximately 500 requests per second higher.</span></span> <span data-ttu-id="2f206-181">Il tempo di risposta medio è minimo da un punto di vista marginale.</span><span class="sxs-lookup"><span data-stu-id="2f206-181">The average response time is marginally lower.</span></span> <span data-ttu-id="2f206-182">Tuttavia queste statistiche non mostrano tutto.</span><span class="sxs-lookup"><span data-stu-id="2f206-182">However, these statistics don't tell the full story.</span></span> <span data-ttu-id="2f206-183">La telemetria per il database aziendale mostra che l'utilizzo di DTU raggiunge il picco intorno al 75% anziché al 100%.</span><span class="sxs-lookup"><span data-stu-id="2f206-183">Telemetry for the business database shows that DTU utilization peaks at around 75%, rather than 100%.</span></span>

![Il monitor del database nel portale di Azure classico che mostra l'utilizzo delle risorse del database nello scenario Polyglot][PolyglotDatabaseUtilization]

<span data-ttu-id="2f206-185">Analogamente, l'utilizzo massimo di DTU del database di log arriva solo a circa il 70%.</span><span class="sxs-lookup"><span data-stu-id="2f206-185">Similarly, the maximum DTU utilization of the log database only reaches about 70%.</span></span> <span data-ttu-id="2f206-186">I database non sono più il fattore limitante delle prestazioni del sistema.</span><span class="sxs-lookup"><span data-stu-id="2f206-186">The databases are no longer the limiting factor in the performance of the system.</span></span>

![Il monitor del database nel portale di Azure classico che mostra l'utilizzo delle risorse del database di log nello scenario Polyglot][LogDatabaseUtilization]


## <a name="related-resources"></a><span data-ttu-id="2f206-188">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="2f206-188">Related resources</span></span>

- <span data-ttu-id="2f206-189">[Choose the right data store][data-store-overview] (Scegliere il giusto archivio dati)</span><span class="sxs-lookup"><span data-stu-id="2f206-189">[Choose the right data store][data-store-overview]</span></span>
- <span data-ttu-id="2f206-190">[Criteri per la scelta di un archivio dati][data-store-comparison]</span><span class="sxs-lookup"><span data-stu-id="2f206-190">[Criteria for choosing a data store][data-store-comparison]</span></span>
- <span data-ttu-id="2f206-191">[Data Access for Highly-Scalable Solutions: Using SQL, NoSQL, and Polyglot Persistence][Data-Access-Guide] (Accesso ai dati per le soluzioni altamente scalabili mediante SQL, NoSQL e persistenza Polyglot)</span><span class="sxs-lookup"><span data-stu-id="2f206-191">[Data Access for Highly-Scalable Solutions: Using SQL, NoSQL, and Polyglot Persistence][Data-Access-Guide]</span></span>
- <span data-ttu-id="2f206-192">[Partizionamento dei dati][DataPartitioningGuidance]</span><span class="sxs-lookup"><span data-stu-id="2f206-192">[Data partitioning][DataPartitioningGuidance]</span></span>

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/MonolithicPersistence
[CosmosDB]: http://azure.microsoft.com/services/cosmos-db/
[Azure-cache]: /azure/redis-cache/
[Data-Access-Guide]: https://msdn.microsoft.com/library/dn271399.aspx
[DataPartitioningGuidance]: ../../best-practices/data-partitioning.md
[data-store-overview]: ../../guide/technology-choices/data-store-overview.md
[data-store-comparison]: ../../guide/technology-choices/data-store-comparison.md

[MonolithicScenarioLoadTest]: _images/MonolithicScenarioLoadTest.jpg
[MonolithicDatabaseUtilization]: _images/MonolithicDatabaseUtilization.jpg
[MonolithicDataAccessStats]: _images/MonolithicDataAccessStats.jpg
[PolyglotScenarioLoadTest]: _images/PolyglotScenarioLoadTest.jpg
[PolyglotDatabaseUtilization]: _images/PolyglotDatabaseUtilization.jpg
[LogDatabaseUtilization]: _images/LogDatabaseUtilization.jpg
