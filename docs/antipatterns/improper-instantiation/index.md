---
title: Antipattern di creazione di istanze non corretta
description: Evitare di creare continuamente nuove istanze di un oggetto che deve essere creato una sola volta e poi condiviso.
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: 4b217f7fc644901eb5c3e77319d151caed30eef1
ms.sourcegitcommit: cf207fd10110f301f1e05f91eeb9f8dfca129164
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="improper-instantiation-antipattern"></a><span data-ttu-id="aabee-103">Antipattern di creazione di istanze non corretta</span><span class="sxs-lookup"><span data-stu-id="aabee-103">Improper Instantiation antipattern</span></span>

<span data-ttu-id="aabee-104">Continuando a creare nuove istanze di un oggetto che deve essere creato una sola volta e poi condiviso si rischia di compromettere le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="aabee-104">It can hurt performance to continually create new instances of an object that is meant to be created once and then shared.</span></span> 

## <a name="problem-description"></a><span data-ttu-id="aabee-105">Descrizione del problema</span><span class="sxs-lookup"><span data-stu-id="aabee-105">Problem description</span></span>

<span data-ttu-id="aabee-106">Molte librerie forniscono astrazioni di risorse esterne.</span><span class="sxs-lookup"><span data-stu-id="aabee-106">Many libraries provide abstractions of external resources.</span></span> <span data-ttu-id="aabee-107">Internamente, queste classi gestiscono in genere le proprie connessioni alla risorsa, agendo come broker che i client possono usare per accedere alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="aabee-107">Internally, these classes typically manage their own connections to the resource, acting as brokers that clients can use to access the resource.</span></span> <span data-ttu-id="aabee-108">Di seguito sono riportati alcuni esempi di classi broker pertinenti per le applicazioni Azure:</span><span class="sxs-lookup"><span data-stu-id="aabee-108">Here are some examples of broker classes that are relevant to Azure applications:</span></span>

- <span data-ttu-id="aabee-109">`System.Net.Http.HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="aabee-109">`System.Net.Http.HttpClient`.</span></span> <span data-ttu-id="aabee-110">Comunica con un servizio Web tramite HTTP.</span><span class="sxs-lookup"><span data-stu-id="aabee-110">Communicates with a web service using HTTP.</span></span>
- <span data-ttu-id="aabee-111">`Microsoft.ServiceBus.Messaging.QueueClient`.</span><span class="sxs-lookup"><span data-stu-id="aabee-111">`Microsoft.ServiceBus.Messaging.QueueClient`.</span></span> <span data-ttu-id="aabee-112">Invia e riceve messaggi da una coda del bus di servizio.</span><span class="sxs-lookup"><span data-stu-id="aabee-112">Posts and receives messages to a Service Bus queue.</span></span> 
- <span data-ttu-id="aabee-113">`Microsoft.Azure.Documents.Client.DocumentClient`.</span><span class="sxs-lookup"><span data-stu-id="aabee-113">`Microsoft.Azure.Documents.Client.DocumentClient`.</span></span> <span data-ttu-id="aabee-114">Si connette a un'istanza di database Cosmos</span><span class="sxs-lookup"><span data-stu-id="aabee-114">Connects to a Cosmos DB instance</span></span>
- <span data-ttu-id="aabee-115">`StackExchange.Redis.ConnectionMultiplexer`.</span><span class="sxs-lookup"><span data-stu-id="aabee-115">`StackExchange.Redis.ConnectionMultiplexer`.</span></span> <span data-ttu-id="aabee-116">Si connette a Redis, incluso Cache Redis di Azure.</span><span class="sxs-lookup"><span data-stu-id="aabee-116">Connects to Redis, including Azure Redis Cache.</span></span>

<span data-ttu-id="aabee-117">Per queste classi è prevista la creazione di una sola istanza, che viene riutilizzata per tutta la durata di un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="aabee-117">These classes are intended to be instantiated once and reused throughout the lifetime of an application.</span></span> <span data-ttu-id="aabee-118">È un errore comune ritenere che queste classi debbano essere acquisite solo quando è necessario e rilasciate rapidamente.</span><span class="sxs-lookup"><span data-stu-id="aabee-118">However, it's a common misunderstanding that these classes should be acquired only as necessary and released quickly.</span></span> <span data-ttu-id="aabee-119">(Le librerie elencate di seguito sono librerie .NET, ma il modello non è univoco per .NET).</span><span class="sxs-lookup"><span data-stu-id="aabee-119">(The ones listed here happen to be .NET libraries, but the pattern is not unique to .NET.)</span></span>

<span data-ttu-id="aabee-120">L'esempio ASP.NET seguente crea un'istanza di `HttpClient` per comunicare con un servizio remoto.</span><span class="sxs-lookup"><span data-stu-id="aabee-120">The following ASP.NET example creates an instance of `HttpClient` to communicate with a remote service.</span></span> <span data-ttu-id="aabee-121">L'esempio completo è disponibile [qui][sample-app].</span><span class="sxs-lookup"><span data-stu-id="aabee-121">You can find the complete sample [here][sample-app].</span></span>

```csharp
public class NewHttpClientInstancePerRequestController : ApiController
{
    // This method creates a new instance of HttpClient and disposes it for every call to GetProductAsync.
    public async Task<Product> GetProductAsync(string id)
    {
        using (var httpClient = new HttpClient())
        {
            var hostName = HttpContext.Current.Request.Url.Host;
            var result = await httpClient.GetStringAsync(string.Format("http://{0}:8080/api/...", hostName));
            return new Product { Name = result };
        }
    }
}
```

<span data-ttu-id="aabee-122">In un'applicazione Web questa tecnica non è scalabile.</span><span class="sxs-lookup"><span data-stu-id="aabee-122">In a web application, this technique is not scalable.</span></span> <span data-ttu-id="aabee-123">Viene creato un nuovo oggetto `HttpClient` per ogni richiesta dell'utente.</span><span class="sxs-lookup"><span data-stu-id="aabee-123">A new `HttpClient` object is created for each user request.</span></span> <span data-ttu-id="aabee-124">In caso di carico pesante, il server Web può esaurire il numero di socket disponibile, causando errori `SocketException`.</span><span class="sxs-lookup"><span data-stu-id="aabee-124">Under heavy load, the web server may exhaust the number of available sockets, resulting in `SocketException` errors.</span></span>

<span data-ttu-id="aabee-125">Questo problema non è limitato alla classe `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="aabee-125">This problem is not restricted to the `HttpClient` class.</span></span> <span data-ttu-id="aabee-126">Altre classi che eseguono il wrapping di risorse o sono costose da creare potrebbero causare problemi simili.</span><span class="sxs-lookup"><span data-stu-id="aabee-126">Other classes that wrap resources or are expensive to create might cause similar issues.</span></span> <span data-ttu-id="aabee-127">L'esempio seguente crea un'istanza della classe `ExpensiveToCreateService`.</span><span class="sxs-lookup"><span data-stu-id="aabee-127">The following example creates an instances of the `ExpensiveToCreateService` class.</span></span> <span data-ttu-id="aabee-128">In questo caso il problema non è necessariamente l'esaurimento dei socket, ma semplicemente il tempo necessario per la creazione di ogni istanza.</span><span class="sxs-lookup"><span data-stu-id="aabee-128">Here the issue is not necessarily socket exhaustion, but simply how long it takes to create each instance.</span></span> <span data-ttu-id="aabee-129">La creazione e l'eliminazione continua di istanze di questa classe possono incidere negativamente sulla scalabilità  del sistema.</span><span class="sxs-lookup"><span data-stu-id="aabee-129">Continually creating and destroying instances of this class might adversely affect the scalability of the system.</span></span>

```csharp
public class NewServiceInstancePerRequestController : ApiController
{
    public async Task<Product> GetProductAsync(string id)
    {
        var expensiveToCreateService = new ExpensiveToCreateService();
        return await expensiveToCreateService.GetProductByIdAsync(id);
    }
}

public class ExpensiveToCreateService
{
    public ExpensiveToCreateService()
    {
        // Simulate delay due to setup and configuration of ExpensiveToCreateService
        Thread.SpinWait(Int32.MaxValue / 100);
    }
    ...
}
```

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="aabee-130">Come risolvere il problema</span><span class="sxs-lookup"><span data-stu-id="aabee-130">How to fix the problem</span></span>

<span data-ttu-id="aabee-131">Se la classe che esegue il wrapping della risorsa esterna è condivisibile e thread-safe, creare un'istanza singleton condivisa o un pool di istanze della classe riutilizzabili.</span><span class="sxs-lookup"><span data-stu-id="aabee-131">If the class that wraps the external resource is shareable and thread-safe, create a shared singleton instance or a pool of reusable instances of the class.</span></span>

<span data-ttu-id="aabee-132">L'esempio seguente usa un'istanza statica di `HttpClient`, condividendo quindi la connessione tra tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="aabee-132">The following example uses a static `HttpClient` instance, thus sharing the connection across all requests.</span></span>

```csharp
public class SingleHttpClientInstanceController : ApiController
{
    private static readonly HttpClient HttpClient;

    static SingleHttpClientInstanceController()
    {
        HttpClient = new HttpClient();
    }

    // This method uses the shared instance of HttpClient for every call to GetProductAsync.
    public async Task<Product> GetProductAsync(string id)
    {
        var hostName = HttpContext.Current.Request.Url.Host;
        var result = await HttpClient.GetStringAsync(string.Format("http://{0}:8080/api/...", hostName));
        return new Product { Name = result };
    }
}
```

## <a name="considerations"></a><span data-ttu-id="aabee-133">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="aabee-133">Considerations</span></span>

- <span data-ttu-id="aabee-134">L'elemento principale di questo antipattern è la ripetuta creazione ed eliminazione di istanze di un oggetto *condivisibile*.</span><span class="sxs-lookup"><span data-stu-id="aabee-134">The key element of this antipattern is repeatedly creating and destroying instances of a *shareable* object.</span></span> <span data-ttu-id="aabee-135">Se una classe non è condivisibile (non è thread-safe), questo antipattern non è applicabile.</span><span class="sxs-lookup"><span data-stu-id="aabee-135">If a class is not shareable (not thread-safe), then this antipattern does not apply.</span></span>

- <span data-ttu-id="aabee-136">Il tipo di risorsa condivisa può indicare se si deve usare un singleton o creare un pool.</span><span class="sxs-lookup"><span data-stu-id="aabee-136">The type of shared resource might dictate whether you should use a singleton or create a pool.</span></span> <span data-ttu-id="aabee-137">La classe `HttpClient` è progettata per essere condivisa anziché inserita in un pool.</span><span class="sxs-lookup"><span data-stu-id="aabee-137">The `HttpClient` class is designed to be shared rather than pooled.</span></span> <span data-ttu-id="aabee-138">Altri oggetti potrebbero supportare il pool, consentendo al sistema di distribuire il carico di lavoro tra più istanze.</span><span class="sxs-lookup"><span data-stu-id="aabee-138">Other objects might support pooling, enabling the system to spread the workload across multiple instances.</span></span>

- <span data-ttu-id="aabee-139">Gli oggetti che si condividono tra più richieste *devono* essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="aabee-139">Objects that you share across multiple requests *must* be thread-safe.</span></span> <span data-ttu-id="aabee-140">La classe `HttpClient` è progettata per essere utilizzata in questo modo, ma altre classi potrebbero non supportare richieste simultanee. Controllare la documentazione disponibile.</span><span class="sxs-lookup"><span data-stu-id="aabee-140">The `HttpClient` class is designed to be used in this manner, but other classes might not support concurrent requests, so check the available documentation.</span></span>

- <span data-ttu-id="aabee-141">Alcuni tipi di risorse sono scarse e non devono essere trattenute.</span><span class="sxs-lookup"><span data-stu-id="aabee-141">Some resource types are scarce and should not be held onto.</span></span> <span data-ttu-id="aabee-142">Le connessioni di database sono un esempio.</span><span class="sxs-lookup"><span data-stu-id="aabee-142">Database connections are an example.</span></span> <span data-ttu-id="aabee-143">Tenendo aperta una connessione di database non necessaria si potrebbe impedire l'accesso contemporaneo al database da parte di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="aabee-143">Holding an open database connection that is not required may prevent other concurrent users from gaining access to the database.</span></span>

- <span data-ttu-id="aabee-144">In .NET Framework molti oggetti che stabiliscono connessioni a risorse esterne vengono creati usando metodi factory statici di altre classi che gestiscono tali connessioni.</span><span class="sxs-lookup"><span data-stu-id="aabee-144">In the .NET Framework, many objects that establish connections to external resources are created by using static factory methods of other classes that manage these connections.</span></span> <span data-ttu-id="aabee-145">Questi oggetti factory devono essere salvati e riutilizzati, anziché eliminati e ricreati.</span><span class="sxs-lookup"><span data-stu-id="aabee-145">These factories  objects are intended to be saved and reused, rather than disposed and recreated.</span></span> <span data-ttu-id="aabee-146">Ad esempio, nel bus di servizio di Azure, l'oggetto `QueueClient` viene creato tramite un oggetto `MessagingFactory`.</span><span class="sxs-lookup"><span data-stu-id="aabee-146">For example, in Azure Service Bus, the `QueueClient` object is created through a `MessagingFactory` object.</span></span> <span data-ttu-id="aabee-147">Internamente, `MessagingFactory` gestisce le connessioni.</span><span class="sxs-lookup"><span data-stu-id="aabee-147">Internally, the `MessagingFactory` manages connections.</span></span> <span data-ttu-id="aabee-148">Per altre informazioni, vedere [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica del bus di servizio][service-bus-messaging].</span><span class="sxs-lookup"><span data-stu-id="aabee-148">For more information, see [Best Practices for performance improvements using Service Bus Messaging][service-bus-messaging].</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="aabee-149">Come rilevare il problema</span><span class="sxs-lookup"><span data-stu-id="aabee-149">How to detect the problem</span></span>

<span data-ttu-id="aabee-150">Sintomi di questo problema includono la riduzione della velocità  effettiva o l'aumento della frequenza degli errori, insieme a uno o più degli effetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="aabee-150">Symptoms of this problem include a drop in throughput or an increased error rate, along with one or more of the following:</span></span> 

- <span data-ttu-id="aabee-151">Aumento delle eccezioni che indicano l'esaurimento delle risorse, ad esempio i socket, le connessioni al database, gli handle di file e così via.</span><span class="sxs-lookup"><span data-stu-id="aabee-151">An increase in exceptions that indicate exhaustion of resources such as sockets, database connections, file handles, and so on.</span></span> 
- <span data-ttu-id="aabee-152">Aumento dell'uso di memoria e del garbage collection.</span><span class="sxs-lookup"><span data-stu-id="aabee-152">Increased memory use and garbage collection.</span></span>
- <span data-ttu-id="aabee-153">Aumento dell'attività di rete, disco o database.</span><span class="sxs-lookup"><span data-stu-id="aabee-153">An increase in network, disk, or database activity.</span></span>

<span data-ttu-id="aabee-154">Per provare a identificare questo problema è possibile eseguire la procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="aabee-154">You can perform the following steps to help identify this problem:</span></span>

1. <span data-ttu-id="aabee-155">Eseguire il monitoraggio del processo per il sistema di produzione per identificare i punti associati a tempi di risposta più lunghi o arresti anomali del sistema causati da mancanza di risorse.</span><span class="sxs-lookup"><span data-stu-id="aabee-155">Performing process monitoring of the production system, to identify points when response times slow down or the system fails due to lack of resources.</span></span>
2. <span data-ttu-id="aabee-156">Esaminare i dati di telemetria acquisiti in questi punti per determinare quali operazioni potrebbero creare ed eliminare oggetti che consumano risorse.</span><span class="sxs-lookup"><span data-stu-id="aabee-156">Examine the telemetry data captured at these points to determine which operations might be creating and destroying resource-consuming objects.</span></span>
3. <span data-ttu-id="aabee-157">Eseguire test di carico su ogni operazione sospetta, in un ambiente di test controllato anziché nel sistema di produzione.</span><span class="sxs-lookup"><span data-stu-id="aabee-157">Load test each suspected operation, in a controlled test environment rather than the production system.</span></span>
4. <span data-ttu-id="aabee-158">Esaminare il codice sorgente ed esaminare come vengono gestiti gli oggetti broker.</span><span class="sxs-lookup"><span data-stu-id="aabee-158">Review the source code and examine the how broker objects are managed.</span></span>

<span data-ttu-id="aabee-159">Esaminare le tracce dello stack per le operazioni a esecuzione prolungata o che generano eccezioni quando il sistema è in condizioni di carico.</span><span class="sxs-lookup"><span data-stu-id="aabee-159">Look at stack traces for operations that are slow-running or that generate exceptions when the system is under load.</span></span> <span data-ttu-id="aabee-160">Queste informazioni sono utili per identificare in che modo queste operazioni utilizzano le risorse.</span><span class="sxs-lookup"><span data-stu-id="aabee-160">This information can help to identify how these operations are utilizing resources.</span></span> <span data-ttu-id="aabee-161">Le eccezioni possono aiutare a determinare se gli errori sono causati dall'esaurimento di risorse condivise.</span><span class="sxs-lookup"><span data-stu-id="aabee-161">Exceptions can help to determine whether errors are caused by shared resources being exhausted.</span></span> 

## <a name="example-diagnosis"></a><span data-ttu-id="aabee-162">Diagnosi di esempio</span><span class="sxs-lookup"><span data-stu-id="aabee-162">Example diagnosis</span></span>

<span data-ttu-id="aabee-163">Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="aabee-163">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="identify-points-of-slow-down-or-failure"></a><span data-ttu-id="aabee-164">Individuare i punti di rallentamento o errore</span><span class="sxs-lookup"><span data-stu-id="aabee-164">Identify points of slow down or failure</span></span>

<span data-ttu-id="aabee-165">La figura seguente mostra i risultati generati utilizzando [New Relic APM][new-relic], che mostra le operazioni che hanno tempi di risposta lunghi.</span><span class="sxs-lookup"><span data-stu-id="aabee-165">The following image shows results generated using [New Relic APM][new-relic], showing operations that have a poor response time.</span></span> <span data-ttu-id="aabee-166">In questo caso, vale la pena di analizzare ulteriormente il metodo `GetProductAsync` nel controller `NewHttpClientInstancePerRequest`.</span><span class="sxs-lookup"><span data-stu-id="aabee-166">In this case, the `GetProductAsync` method in the `NewHttpClientInstancePerRequest` controller are worth investigating further.</span></span> <span data-ttu-id="aabee-167">Si noti che il tasso di errore aumenta quando queste operazioni sono in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="aabee-167">Notice that the error rate also increases when these operations are running.</span></span> 

![Il dashboard di monitoraggio di New Relic che mostra l'applicazione di esempio che crea una nuova istanza di un oggetto HttpClient per ogni richiesta][dashboard-new-HTTPClient-instance]

### <a name="examine-telemetry-data-and-find-correlations"></a><span data-ttu-id="aabee-169">Esaminare i dati di telemetria e trovare le correlazioni</span><span class="sxs-lookup"><span data-stu-id="aabee-169">Examine telemetry data and find correlations</span></span>

<span data-ttu-id="aabee-170">L'immagine successiva mostra i dati acquisiti usando la profilatura dei thread, nello stesso periodo corrispondente all'immagine precedente.</span><span class="sxs-lookup"><span data-stu-id="aabee-170">The next image shows data captured using thread profiling, over the same period corresponding as the previous image.</span></span> <span data-ttu-id="aabee-171">Il sistema impiega molto tempo per l'apertura delle connessioni socket e anche più tempo per chiuderle e gestire le eccezioni di socket.</span><span class="sxs-lookup"><span data-stu-id="aabee-171">The system spends a significant time opening socket connections, and even more time closing them and handling socket exceptions.</span></span>

![Il thread profiler di New Relic che mostra l'applicazione di esempio che crea una nuova istanza di un oggetto HttpClient per ogni richiesta][thread-profiler-new-HTTPClient-instance]

### <a name="performing-load-testing"></a><span data-ttu-id="aabee-173">Eseguire i test di carico</span><span class="sxs-lookup"><span data-stu-id="aabee-173">Performing load testing</span></span>

<span data-ttu-id="aabee-174">Per simulare le operazioni tipiche che gli utenti potrebbero eseguire, utilizzare i test di carico.</span><span class="sxs-lookup"><span data-stu-id="aabee-174">Use load testing to simulate the typical operations that users might perform.</span></span> <span data-ttu-id="aabee-175">Ciò consente di identificare quali parti di un sistema subiscono un esaurimento delle risorse con carichi diversi.</span><span class="sxs-lookup"><span data-stu-id="aabee-175">This can help to identify which parts of a system suffer from resource exhaustion under varying loads.</span></span> <span data-ttu-id="aabee-176">Eseguire questi test in un ambiente controllato anziché nel sistema di produzione.</span><span class="sxs-lookup"><span data-stu-id="aabee-176">Perform these tests in a controlled environment rather than the production system.</span></span> <span data-ttu-id="aabee-177">Il grafico seguente mostra la velocità effettiva delle richieste gestite dal controller `NewHttpClientInstancePerRequest` mentre il carico utente aumenta a 100 utenti simultanei.</span><span class="sxs-lookup"><span data-stu-id="aabee-177">The following graph shows the throughput of requests handled by the `NewHttpClientInstancePerRequest` controller as the user load increases to 100 concurrent users.</span></span>

![Velocità effettiva dell'applicazione di esempio che crea una nuova istanza di un oggetto HttpClient per ogni richiesta][throughput-new-HTTPClient-instance]

<span data-ttu-id="aabee-179">Inizialmente il volume di richieste gestite al secondo aumenta man mano che aumenta il carico di lavoro.</span><span class="sxs-lookup"><span data-stu-id="aabee-179">At first, the volume of requests handled per second increases as the workload increases.</span></span> <span data-ttu-id="aabee-180">Quando gli utenti sono circa 30, invece, il volume di richieste riuscite raggiunge un limite e il sistema inizia a generare eccezioni.</span><span class="sxs-lookup"><span data-stu-id="aabee-180">At about 30 users, however, the volume of successful requests reaches a limit, and the system starts to generate exceptions.</span></span> <span data-ttu-id="aabee-181">Da quel punto in avanti, il volume delle eccezioni aumenta gradualmente con il carico utente.</span><span class="sxs-lookup"><span data-stu-id="aabee-181">From then on, the volume of exceptions gradually increases with the user load.</span></span> 

<span data-ttu-id="aabee-182">Il test di carico ha segnalato questi errori come errori HTTP 500 (server interno).</span><span class="sxs-lookup"><span data-stu-id="aabee-182">The load test reported these failures as HTTP 500 (Internal Server) errors.</span></span> <span data-ttu-id="aabee-183">L'esame dei dati di telemetria ha dimostrato che questi errori sono stati causati dall'esaurimento delle risorse di socket da parte del sistema, con la creazione di un numero sempre crescente di oggetti `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="aabee-183">Reviewing the telemetry showed that these errors were caused by the system running out of socket resources, as more and more `HttpClient` objects were created.</span></span>

<span data-ttu-id="aabee-184">Il grafico successivo mostra un test simile per un controller che crea l'oggetto personalizzato `ExpensiveToCreateService`.</span><span class="sxs-lookup"><span data-stu-id="aabee-184">The next graph shows a similar test for a controller that creates the custom `ExpensiveToCreateService` object.</span></span>

![Velocità effettiva dell'applicazione di esempio che crea una nuova istanza di ExpensiveToCreateService per ogni richiesta][throughput-new-ExpensiveToCreateService-instance]

<span data-ttu-id="aabee-186">Questa volta il controller non genera alcuna eccezione, ma la velocità effettiva raggiunge ancora una soglia, mentre il tempo di risposta medio aumenta con fattore 20</span><span class="sxs-lookup"><span data-stu-id="aabee-186">This time, the controller does not generate any exceptions, but throughput still reaches a plateau, while the average response time increases by a factor of 20.</span></span> <span data-ttu-id="aabee-187">(il grafico usa la scala logaritmica per la velocità effettiva e il tempo di risposta). I dati di telemetria hanno dimostrato che la creazione di nuove istanze del `ExpensiveToCreateService` è la causa principale del problema.</span><span class="sxs-lookup"><span data-stu-id="aabee-187">(The graph uses a logarithmic scale for response time and throughput.) Telemetry showed that creating new instances of the `ExpensiveToCreateService` was the main cause of the problem.</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="aabee-188">Implementare la soluzione e verificare il risultato</span><span class="sxs-lookup"><span data-stu-id="aabee-188">Implement the solution and verify the result</span></span>

<span data-ttu-id="aabee-189">Dopo aver cambiato il metodo `GetProductAsync` in modo che venga condivisa una singola istanza `HttpClient`, un secondo test di carico ha dimostrato prestazioni migliorate.</span><span class="sxs-lookup"><span data-stu-id="aabee-189">After switching the `GetProductAsync` method to share a single `HttpClient` instance, a second load test showed improved performance.</span></span> <span data-ttu-id="aabee-190">Non sono stati segnalati errori e il sistema è stato in grado di gestire un incremento del carico fino a 500 richieste al secondo.</span><span class="sxs-lookup"><span data-stu-id="aabee-190">No errors were reported, and the system was able to handle an increasing load of up to 500 requests per second.</span></span> <span data-ttu-id="aabee-191">Il tempo medio di risposta si è ridotto a metà rispetto al test precedente.</span><span class="sxs-lookup"><span data-stu-id="aabee-191">The average response time was cut in half, compared with the previous test.</span></span>

![Velocità effettiva dell'applicazione di esempio che riutilizza la stessa istanza di un oggetto HttpClient per ogni richiesta][throughput-single-HTTPClient-instance]

<span data-ttu-id="aabee-193">Per il confronto, l'immagine seguente mostra i dati di telemetria di monitoraggio dello stack.</span><span class="sxs-lookup"><span data-stu-id="aabee-193">For comparison, the following image shows the stack trace telemetry.</span></span> <span data-ttu-id="aabee-194">Questa volta il sistema passa la maggior parte del tempo a eseguire il lavoro effettivo, anziché ad aprire e chiudere socket.</span><span class="sxs-lookup"><span data-stu-id="aabee-194">This time, the system spends most of its time performing real work, rather than opening and closing sockets.</span></span>

![Il thread profiler di New Relic che mostra l'applicazione di esempio che crea una singola istanza di un oggetto HttpClient per tutte le richieste][thread-profiler-single-HTTPClient-instance]

<span data-ttu-id="aabee-196">Il grafico successivo mostra un test di carico simile utilizzando un'istanza condivisa dell'oggetto `ExpensiveToCreateService`.</span><span class="sxs-lookup"><span data-stu-id="aabee-196">The next graph shows a similar load test using a shared instance of the `ExpensiveToCreateService` object.</span></span> <span data-ttu-id="aabee-197">Anche questa volta il volume di richieste gestite aumenta in linea con il carico utente, mentre il tempo di risposta medio rimane basso.</span><span class="sxs-lookup"><span data-stu-id="aabee-197">Again, the volume of handled requests increases in line with the user load, while the average response time remains low.</span></span> 

![Velocità effettiva dell'applicazione di esempio che riutilizza la stessa istanza di un oggetto HttpClient per ogni richiesta][throughput-single-ExpensiveToCreateService-instance]



[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/ImproperInstantiation
[service-bus-messaging]: /azure/service-bus-messaging/service-bus-performance-improvements
[new-relic]: https://newrelic.com/application-monitoring
[throughput-new-HTTPClient-instance]: _images/HttpClientInstancePerRequest.jpg
[dashboard-new-HTTPClient-instance]: _images/HttpClientInstancePerRequestWebTransactions.jpg
[thread-profiler-new-HTTPClient-instance]: _images/HttpClientInstancePerRequestThreadProfile.jpg
[throughput-new-ExpensiveToCreateService-instance]: _images/ServiceInstancePerRequest.jpg
[throughput-single-HTTPClient-instance]: _images/SingleHttpClientInstance.jpg
[throughput-single-ExpensiveToCreateService-instance]: _images/SingleServiceInstance.jpg
[thread-profiler-single-HTTPClient-instance]: _images/SingleHttpClientInstanceThreadProfile.jpg
