---
title: Antipattern I/O sincrono
titleSuffix: Performance antipatterns for cloud apps
description: Bloccare il thread chiamante durante il completamento dell'I/O può ridurre le prestazioni e influire sulla scalabilità verticale.
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 1b53806b2939a7c44a8b48c9146d5e86c84d9e2e
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486166"
---
# <a name="synchronous-io-antipattern"></a><span data-ttu-id="eafa4-103">Antipattern I/O sincrono</span><span class="sxs-lookup"><span data-stu-id="eafa4-103">Synchronous I/O antipattern</span></span>

<span data-ttu-id="eafa4-104">Bloccare il thread chiamante durante il completamento dell'I/O può ridurre le prestazioni e influire sulla scalabilità verticale.</span><span class="sxs-lookup"><span data-stu-id="eafa4-104">Blocking the calling thread while I/O completes can reduce performance and affect vertical scalability.</span></span>

## <a name="problem-description"></a><span data-ttu-id="eafa4-105">Descrizione del problema</span><span class="sxs-lookup"><span data-stu-id="eafa4-105">Problem description</span></span>

<span data-ttu-id="eafa4-106">Un'operazione I/O sincrona blocca il thread chiamante durante il completamento dell'I/O.</span><span class="sxs-lookup"><span data-stu-id="eafa4-106">A synchronous I/O operation blocks the calling thread while the I/O completes.</span></span> <span data-ttu-id="eafa4-107">Il thread chiamante entra in uno stato di attesa e non è in grado di eseguire operazioni utili durante questo intervallo: un inutile consumo di risorse di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="eafa4-107">The calling thread enters a wait state and is unable to perform useful work during this interval, wasting processing resources.</span></span>

<span data-ttu-id="eafa4-108">Alcuni esempi comuni sono:</span><span class="sxs-lookup"><span data-stu-id="eafa4-108">Common examples of I/O include:</span></span>

- <span data-ttu-id="eafa4-109">Recupero o archiviazione persistente di dati in un database o qualsiasi tipo di archivio permanente.</span><span class="sxs-lookup"><span data-stu-id="eafa4-109">Retrieving or persisting data to a database or any type of persistent storage.</span></span>
- <span data-ttu-id="eafa4-110">Invio di una richiesta a un servizio Web.</span><span class="sxs-lookup"><span data-stu-id="eafa4-110">Sending a request to a web service.</span></span>
- <span data-ttu-id="eafa4-111">Inserimento di un messaggio o recupero di un messaggio da una coda.</span><span class="sxs-lookup"><span data-stu-id="eafa4-111">Posting a message or retrieving a message from a queue.</span></span>
- <span data-ttu-id="eafa4-112">Scrittura o lettura in un file locale.</span><span class="sxs-lookup"><span data-stu-id="eafa4-112">Writing to or reading from a local file.</span></span>

<span data-ttu-id="eafa4-113">In genere questo antipattern si verifica perché:</span><span class="sxs-lookup"><span data-stu-id="eafa4-113">This antipattern typically occurs because:</span></span>

- <span data-ttu-id="eafa4-114">Sembra essere il modo più intuitivo per eseguire un'operazione.</span><span class="sxs-lookup"><span data-stu-id="eafa4-114">It appears to be the most intuitive way to perform an operation.</span></span>
- <span data-ttu-id="eafa4-115">L'applicazione richiede una risposta a una richiesta.</span><span class="sxs-lookup"><span data-stu-id="eafa4-115">The application requires a response from a request.</span></span>
- <span data-ttu-id="eafa4-116">L'applicazione usa una libreria che fornisce solo metodi sincroni per l'I/O.</span><span class="sxs-lookup"><span data-stu-id="eafa4-116">The application uses a library that only provides synchronous methods for I/O.</span></span>
- <span data-ttu-id="eafa4-117">Una libreria esterna esegue operazioni di I/O sincrone internamente.</span><span class="sxs-lookup"><span data-stu-id="eafa4-117">An external library performs synchronous I/O operations internally.</span></span> <span data-ttu-id="eafa4-118">Una singola chiamata di I/O sincrona può bloccare un'intera sequenza di chiamate.</span><span class="sxs-lookup"><span data-stu-id="eafa4-118">A single synchronous I/O call can block an entire call chain.</span></span>

<span data-ttu-id="eafa4-119">Il codice seguente carica un file nell'archiviazione BLOB di Azure.</span><span class="sxs-lookup"><span data-stu-id="eafa4-119">The following code uploads a file to Azure blob storage.</span></span> <span data-ttu-id="eafa4-120">Esistono due posizioni in cui il codice blocca l'attesa per l'I/O sincrono: il metodo `CreateIfNotExists` e il metodo `UploadFromStream`.</span><span class="sxs-lookup"><span data-stu-id="eafa4-120">There are two places where the code blocks waiting for synchronous I/O, the `CreateIfNotExists` method and the `UploadFromStream` method.</span></span>

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference("uploadedfiles");

container.CreateIfNotExists();
var blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = File.OpenRead(HostingEnvironment.MapPath("~/FileToUpload.txt")))
{
    blockBlob.UploadFromStream(fileStream);
}
```

<span data-ttu-id="eafa4-121">Di seguito è riportato un esempio di attesa di una risposta da un servizio esterno.</span><span class="sxs-lookup"><span data-stu-id="eafa4-121">Here's an example of waiting for a response from an external service.</span></span> <span data-ttu-id="eafa4-122">Il metodo `GetUserProfile` chiama un servizio remoto che restituisce un `UserProfile`.</span><span class="sxs-lookup"><span data-stu-id="eafa4-122">The `GetUserProfile` method calls a remote service that returns a `UserProfile`.</span></span>

```csharp
public interface IUserProfileService
{
    UserProfile GetUserProfile();
}

public class SyncController : ApiController
{
    private readonly IUserProfileService _userProfileService;

    public SyncController()
    {
        _userProfileService = new FakeUserProfileService();
    }

    // This is a synchronous method that calls the synchronous GetUserProfile method.
    public UserProfile GetUserProfile()
    {
        return _userProfileService.GetUserProfile();
    }
}
```

<span data-ttu-id="eafa4-123">È possibile trovare il codice completo per entrambi gli esempi [qui][sample-app].</span><span class="sxs-lookup"><span data-stu-id="eafa4-123">You can find the complete code for both of these examples [here][sample-app].</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="eafa4-124">Come risolvere il problema</span><span class="sxs-lookup"><span data-stu-id="eafa4-124">How to fix the problem</span></span>

<span data-ttu-id="eafa4-125">Sostituire operazioni di I/O sincrone con operazioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="eafa4-125">Replace synchronous I/O operations with asynchronous operations.</span></span> <span data-ttu-id="eafa4-126">In questo modo il thread corrente si libera e può continuare a eseguire lavoro significativo anziché bloccarsi, inoltre può migliorare l'utilizzo delle risorse di calcolo.</span><span class="sxs-lookup"><span data-stu-id="eafa4-126">This frees the current thread to continue performing meaningful work rather than blocking, and helps improve the utilization of compute resources.</span></span> <span data-ttu-id="eafa4-127">Eseguire l'I/O in modo asincrono è particolarmente efficace per la gestione di un carico imprevisto nelle richieste dalle applicazioni client.</span><span class="sxs-lookup"><span data-stu-id="eafa4-127">Performing I/O asynchronously is particularly efficient for handling an unexpected surge in requests from client applications.</span></span>

<span data-ttu-id="eafa4-128">Molte librerie forniscono versioni sincrone e asincrone dei metodi.</span><span class="sxs-lookup"><span data-stu-id="eafa4-128">Many libraries provide both synchronous and asynchronous versions of methods.</span></span> <span data-ttu-id="eafa4-129">Se possibile, utilizzare le versioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="eafa4-129">Whenever possible, use the asynchronous versions.</span></span> <span data-ttu-id="eafa4-130">Di seguito è riportata la versione asincrona dell'esempio precedente che consente di caricare un file nell'archiviazione BLOB di Azure.</span><span class="sxs-lookup"><span data-stu-id="eafa4-130">Here is the asynchronous version of the previous example that uploads a file to Azure blob storage.</span></span>

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference("uploadedfiles");

await container.CreateIfNotExistsAsync();

var blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = File.OpenRead(HostingEnvironment.MapPath("~/FileToUpload.txt")))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

<span data-ttu-id="eafa4-131">L'operatore `await` restituisce il controllo all'ambiente chiamante mentre viene eseguita l'operazione asincrona.</span><span class="sxs-lookup"><span data-stu-id="eafa4-131">The `await` operator returns control to the calling environment while the asynchronous operation is performed.</span></span> <span data-ttu-id="eafa4-132">Il copdice che segue questa istruzione funziona come una continuazione che viene eseguita quando l'operazione asincrona è stata completata.</span><span class="sxs-lookup"><span data-stu-id="eafa4-132">The code after this statement acts as a continuation that runs when the asynchronous operation has completed.</span></span>

<span data-ttu-id="eafa4-133">Un servizio ben progettato deve fornire anche le operazioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="eafa4-133">A well designed service should also provide asynchronous operations.</span></span> <span data-ttu-id="eafa4-134">Di seguito è riportata una versione asincrona del servizio Web che restituisce profili utente.</span><span class="sxs-lookup"><span data-stu-id="eafa4-134">Here is an asynchronous version of the web service that returns user profiles.</span></span> <span data-ttu-id="eafa4-135">Il metodo `GetUserProfileAsync` dipende dalla presenza di una versione asincrona del servizio User Profile.</span><span class="sxs-lookup"><span data-stu-id="eafa4-135">The `GetUserProfileAsync` method depends on having an asynchronous version of the User Profile service.</span></span>

```csharp
public interface IUserProfileService
{
    Task<UserProfile> GetUserProfileAsync();
}

public class AsyncController : ApiController
{
    private readonly IUserProfileService _userProfileService;

    public AsyncController()
    {
        _userProfileService = new FakeUserProfileService();
    }

    // This is an synchronous method that calls the Task based GetUserProfileAsync method.
    public Task<UserProfile> GetUserProfileAsync()
    {
        return _userProfileService.GetUserProfileAsync();
    }
}
```

<span data-ttu-id="eafa4-136">Per le librerie che non forniscono versioni asincrone delle operazioni, è possibile creare wrapper asincroni intorno a determinati metodi sincroni.</span><span class="sxs-lookup"><span data-stu-id="eafa4-136">For libraries that don't provide asynchronous versions of operations, it may be possible to create asynchronous wrappers around selected synchronous methods.</span></span> <span data-ttu-id="eafa4-137">Seguire questo approccio con cautela.</span><span class="sxs-lookup"><span data-stu-id="eafa4-137">Follow this approach with caution.</span></span> <span data-ttu-id="eafa4-138">Anche se potrebbe migliorare la velocità di risposta sul thread che richiama il wrapper asincrono, in realtà utilizza più risorse.</span><span class="sxs-lookup"><span data-stu-id="eafa4-138">While it may improve responsiveness on the thread that invokes the asynchronous wrapper, it actually consumes more resources.</span></span> <span data-ttu-id="eafa4-139">Potrebbe essere creato un thread aggiuntivo e sincronizzare il lavoro svolto da questo thread comporta un sovraccarico.</span><span class="sxs-lookup"><span data-stu-id="eafa4-139">An extra thread may be created, and there is overhead associated with synchronizing the work done by this thread.</span></span> <span data-ttu-id="eafa4-140">Alcuni svantaggi sono descritti in questo post di blog: [Should I expose asynchronous wrappers for synchronous methods?][async-wrappers] (È necessario esporre wrapper asincroni per i metodi sincroni)</span><span class="sxs-lookup"><span data-stu-id="eafa4-140">Some tradeoffs are discussed in this blog post: [Should I expose asynchronous wrappers for synchronous methods?][async-wrappers]</span></span>

<span data-ttu-id="eafa4-141">Di seguito è riportato un esempio di un wrapper asincrono per un metodo sincrono.</span><span class="sxs-lookup"><span data-stu-id="eafa4-141">Here is an example of an asynchronous wrapper around a synchronous method.</span></span>

```csharp
// Asynchronous wrapper around synchronous library method
private async Task<int> LibraryIOOperationAsync()
{
    return await Task.Run(() => LibraryIOOperation());
}
```

<span data-ttu-id="eafa4-142">Il codice chiamante può ora attendere sul wrapper:</span><span class="sxs-lookup"><span data-stu-id="eafa4-142">Now the calling code can await on the wrapper:</span></span>

```csharp
// Invoke the asynchronous wrapper using a task
await LibraryIOOperationAsync();
```

## <a name="considerations"></a><span data-ttu-id="eafa4-143">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="eafa4-143">Considerations</span></span>

- <span data-ttu-id="eafa4-144">Le operazioni di I/O per cui si prevede una durata molto breve e che hanno una bassa probabilità di provocare contesa potrebbero essere più efficienti come operazioni sincrone.</span><span class="sxs-lookup"><span data-stu-id="eafa4-144">I/O operations that are expected to be very short lived and are unlikely to cause contention might be more performant as synchronous operations.</span></span> <span data-ttu-id="eafa4-145">Un esempio potrebbe essere la lettura di piccoli file in un'unità SSD.</span><span class="sxs-lookup"><span data-stu-id="eafa4-145">An example might be reading small files on an SSD drive.</span></span> <span data-ttu-id="eafa4-146">L'overhead di invio di un'attività a un altro thread e la sincronizzazione con tale thread al completamento dell'attività, potrebbe superare i vantaggi dell'I/O asincrono.</span><span class="sxs-lookup"><span data-stu-id="eafa4-146">The overhead of dispatching a task to another thread, and synchronizing with that thread when the task completes, might outweigh the benefits of asynchronous I/O.</span></span> <span data-ttu-id="eafa4-147">Tuttavia questi casi sono relativamente rari e la maggior parte delle operazioni di I/O devono essere eseguite in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="eafa4-147">However, these cases are relatively rare, and most I/O operations should be done asynchronously.</span></span>

- <span data-ttu-id="eafa4-148">Il miglioramento delle prestazioni di I/O può causare colli di bottiglia in altre parti del sistema.</span><span class="sxs-lookup"><span data-stu-id="eafa4-148">Improving I/O performance may cause other parts of the system to become bottlenecks.</span></span> <span data-ttu-id="eafa4-149">Ad esempio, lo sblocco dei thread potrebbe comportare un volume maggiore di richieste simultanee a risorse condivise, che porta a sua volta all'esaurimento delle risorse o alla limitazione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="eafa4-149">For example, unblocking threads might result in a higher volume of concurrent requests to shared resources, leading in turn to resource starvation or throttling.</span></span> <span data-ttu-id="eafa4-150">Nel caso di un problema, potrebbe essere necessario scalare orizzontalmente il numero di server Web o di archivi dati delle partizionare per ridurre la contesa.</span><span class="sxs-lookup"><span data-stu-id="eafa4-150">If that becomes a problem, you might need to scale out the number of web servers or partition data stores to reduce contention.</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="eafa4-151">Come rilevare il problema</span><span class="sxs-lookup"><span data-stu-id="eafa4-151">How to detect the problem</span></span>

<span data-ttu-id="eafa4-152">Per gli utenti, l'applicazione potrebbe sembrare che non risponda o bloccarsi periodicamente.</span><span class="sxs-lookup"><span data-stu-id="eafa4-152">For users, the application may seem unresponsive or appear to hang periodically.</span></span> <span data-ttu-id="eafa4-153">L'applicazione potrebbe bloccarsi con eccezioni di timeout.</span><span class="sxs-lookup"><span data-stu-id="eafa4-153">The application might fail with timeout exceptions.</span></span> <span data-ttu-id="eafa4-154">È possibile che vengano restituiti anche errori HTTP 500 (Server interno).</span><span class="sxs-lookup"><span data-stu-id="eafa4-154">These failures could also return HTTP 500 (Internal Server) errors.</span></span> <span data-ttu-id="eafa4-155">Nel server, le richieste client in ingresso potrebbero essere bloccate fino a quando non diventa disponibile un thread, causando una lunghezza eccessiva della coda di richieste, che si manifesta sotto forma di errori HTTP 503 (servizio non disponibile).</span><span class="sxs-lookup"><span data-stu-id="eafa4-155">On the server, incoming client requests might be blocked until a thread becomes available, resulting in excessive request queue lengths, manifested as HTTP 503 (Service Unavailable) errors.</span></span>

<span data-ttu-id="eafa4-156">È possibile eseguire la procedura seguente per identificare il problema:</span><span class="sxs-lookup"><span data-stu-id="eafa4-156">You can perform the following steps to help identify the problem:</span></span>

1. <span data-ttu-id="eafa4-157">Monitorare il sistema di produzione e determinare se i thread di lavoro bloccati stanno vincolando la velocità effettiva.</span><span class="sxs-lookup"><span data-stu-id="eafa4-157">Monitor the production system and determine whether blocked worker threads are constraining throughput.</span></span>

2. <span data-ttu-id="eafa4-158">Se le richieste sono bloccate a causa di mancanza di thread, esaminare l'applicazione per determinare quali sono le operazioni che in quel momento potrebbero eseguire l'I/O in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="eafa4-158">If requests are being blocked due to lack of threads, review the application to determine which operations may be performing I/O synchronously.</span></span>

3. <span data-ttu-id="eafa4-159">Eseguire test di carico controllato di ogni operazione che sta eseguendo L'I/O sincrono, per sapere se queste operazioni influiscono sulle prestazioni del sistema.</span><span class="sxs-lookup"><span data-stu-id="eafa4-159">Perform controlled load testing of each operation that is performing synchronous I/O, to find out whether those operations are affecting system performance.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="eafa4-160">Diagnosi di esempio</span><span class="sxs-lookup"><span data-stu-id="eafa4-160">Example diagnosis</span></span>

<span data-ttu-id="eafa4-161">Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="eafa4-161">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="monitor-web-server-performance"></a><span data-ttu-id="eafa4-162">Monitorare le prestazioni del server Web</span><span class="sxs-lookup"><span data-stu-id="eafa4-162">Monitor web server performance</span></span>

<span data-ttu-id="eafa4-163">Per le applicazioni Web e i ruoli Web di Azure, è importante monitorare le prestazioni del server Web IIS.</span><span class="sxs-lookup"><span data-stu-id="eafa4-163">For Azure web applications and web roles, it's worth monitoring the performance of the IIS web server.</span></span> <span data-ttu-id="eafa4-164">In particolare occorre prestare attenzione alla lunghezza della coda di richieste per stabilire se le richieste vengono bloccate in attesa di thread disponibili durante i periodi di intensa attività.</span><span class="sxs-lookup"><span data-stu-id="eafa4-164">In particular, pay attention to the request queue length to establish whether requests are being blocked waiting for available threads during periods of high activity.</span></span> <span data-ttu-id="eafa4-165">È possibile raccogliere queste informazioni abilitando la diagnostica di Azure.</span><span class="sxs-lookup"><span data-stu-id="eafa4-165">You can gather this information by enabling Azure diagnostics.</span></span> <span data-ttu-id="eafa4-166">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="eafa4-166">For more information, see:</span></span>

- <span data-ttu-id="eafa4-167">[Eseguire il monitoraggio delle app nel servizio app di Azure][web-sites-monitor]</span><span class="sxs-lookup"><span data-stu-id="eafa4-167">[Monitor Apps in Azure App Service][web-sites-monitor]</span></span>
- <span data-ttu-id="eafa4-168">[Creare e usare contatori di prestazioni in un'applicazione Azure][performance-counters]</span><span class="sxs-lookup"><span data-stu-id="eafa4-168">[Create and use performance counters in an Azure application][performance-counters]</span></span>

<span data-ttu-id="eafa4-169">Instrumentare l'applicazione per vedere come le richieste vengono gestite dopo che sono state accettate.</span><span class="sxs-lookup"><span data-stu-id="eafa4-169">Instrument the application to see how requests are handled once they have been accepted.</span></span> <span data-ttu-id="eafa4-170">Tracciare il flusso di una richiesta può essere utili per identificare se sta eseguendo chiamate a esecuzione lenta e sta bloccando il thread corrente.</span><span class="sxs-lookup"><span data-stu-id="eafa4-170">Tracing the flow of a request can help to identify whether it is performing slow-running calls and blocking the current thread.</span></span> <span data-ttu-id="eafa4-171">Anche la profilatura di thread può evidenziare richieste che vengono bloccate.</span><span class="sxs-lookup"><span data-stu-id="eafa4-171">Thread profiling can also highlight requests that are being blocked.</span></span>

### <a name="load-test-the-application"></a><span data-ttu-id="eafa4-172">Testare il carico dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="eafa4-172">Load test the application</span></span>

<span data-ttu-id="eafa4-173">Il grafico seguente mostra le prestazioni del metodo `GetUserProfile` sincrono illustrato in precedenza, con vari carichi fino a 4000 utenti simultanei.</span><span class="sxs-lookup"><span data-stu-id="eafa4-173">The following graph shows the performance of the synchronous `GetUserProfile` method shown earlier, under varying loads of up to 4000 concurrent users.</span></span> <span data-ttu-id="eafa4-174">L'applicazione è un'applicazione ASP.NET eseguita in un ruolo Web servizio cloud di Azure.</span><span class="sxs-lookup"><span data-stu-id="eafa4-174">The application is an ASP.NET application running in an Azure Cloud Service web role.</span></span>

![Grafico delle prestazioni per l'applicazione di esempio che esegue operazioni di I/O sincrone][sync-performance]

<span data-ttu-id="eafa4-176">L'operazione sincrona prevede un'inattività di 2 secondi hardcoded, per simulare l'I/O sincrono, pertanto il tempo di risposta minimo è leggermente maggiore di 2 secondi.</span><span class="sxs-lookup"><span data-stu-id="eafa4-176">The synchronous operation is hard-coded to sleep for 2 seconds, to simulate synchronous I/O, so the minimum response time is slightly over 2 seconds.</span></span> <span data-ttu-id="eafa4-177">Quando il carico raggiunge circa 2500 utenti simultanei, il tempo di risposta medio diventa stabile, anche se il volume di richieste al secondo continua ad aumentare.</span><span class="sxs-lookup"><span data-stu-id="eafa4-177">When the load reaches approximately 2500 concurrent users, the average response time reaches a plateau, although the volume of requests per second continues to increase.</span></span> <span data-ttu-id="eafa4-178">Si noti che per queste due misure la scala è logaritmica.</span><span class="sxs-lookup"><span data-stu-id="eafa4-178">Note that the scale for these two measures is logarithmic.</span></span> <span data-ttu-id="eafa4-179">Il numero di richieste al secondo raddoppia tra il punto e la fine del test.</span><span class="sxs-lookup"><span data-stu-id="eafa4-179">The number of requests per second doubles between this point and the end of the test.</span></span>

<span data-ttu-id="eafa4-180">In isolamento, non sempre è chiaro da questo test se l'I/O sincrono sia un problema.</span><span class="sxs-lookup"><span data-stu-id="eafa4-180">In isolation, it's not necessarily clear from this test whether the synchronous I/O is a problem.</span></span> <span data-ttu-id="eafa4-181">In condizioni di carico pesante, l'applicazione può raggiungere un punto critico in cui il server Web non riesce più a elaborare le richieste in modo tempestivo, facendo in modo che le applicazioni client ricevano eccezioni di timeout.</span><span class="sxs-lookup"><span data-stu-id="eafa4-181">Under heavier load, the application may reach a tipping point where the web server can no longer process requests in a timely manner, causing client applications to receive time-out exceptions.</span></span>

<span data-ttu-id="eafa4-182">Le richieste in ingresso vengono accodate dal server Web IIS e passate a un thread in esecuzione nel pool di thread ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="eafa4-182">Incoming requests are queued by the IIS web server and handed to a thread running in the ASP.NET thread pool.</span></span> <span data-ttu-id="eafa4-183">Poiché ogni operazione esegue l'I/O in modo sincrono, il thread è bloccato fino al completamento dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="eafa4-183">Because each operation performs I/O synchronously, the thread is blocked until the operation completes.</span></span> <span data-ttu-id="eafa4-184">Man mano che aumenta il carico di lavoro, infine tutti i thread ASP.NET nel pool di thread sono allocati e bloccati.</span><span class="sxs-lookup"><span data-stu-id="eafa4-184">As the workload increases, eventually all of the ASP.NET threads in the thread pool are allocated and blocked.</span></span> <span data-ttu-id="eafa4-185">A questo punto le altre richieste in arrivo devono attendere nella coda che un thread diventi disponibile.</span><span class="sxs-lookup"><span data-stu-id="eafa4-185">At that point, any further incoming requests must wait in the queue for an available thread.</span></span> <span data-ttu-id="eafa4-186">Man mano che aumenta la lunghezza della coda, le richieste iniziano ad andare in timeout.</span><span class="sxs-lookup"><span data-stu-id="eafa4-186">As the queue length grows, requests start to time out.</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="eafa4-187">Implementare la soluzione e verificare il risultato</span><span class="sxs-lookup"><span data-stu-id="eafa4-187">Implement the solution and verify the result</span></span>

<span data-ttu-id="eafa4-188">Il grafico successivo mostra i risultati dei test ti carico della versione asincrona del codice.</span><span class="sxs-lookup"><span data-stu-id="eafa4-188">The next graph shows the results from load testing the asynchronous version of the code.</span></span>

![Grafico delle prestazioni per l'applicazione di esempio che esegue operazioni di I/O asincrone][async-performance]

<span data-ttu-id="eafa4-190">La velocità effettiva è molto superiore.</span><span class="sxs-lookup"><span data-stu-id="eafa4-190">Throughput is far higher.</span></span> <span data-ttu-id="eafa4-191">Nella stessa durata del test precedente, il sistema gestisce correttamente una velocità effettiva quasi decuplicata, misurata in richieste al secondo.</span><span class="sxs-lookup"><span data-stu-id="eafa4-191">Over the same duration as the previous test, the system successfully handles a nearly tenfold increase in throughput, as measured in requests per second.</span></span> <span data-ttu-id="eafa4-192">Inoltre, il tempo medio di risposta è relativamente costante e rimane circa 25 volte più piccolo rispetto al test precedente.</span><span class="sxs-lookup"><span data-stu-id="eafa4-192">Moreover, the average response time is relatively constant and remains approximately 25 times smaller than the previous test.</span></span>

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/SynchronousIO
[async-wrappers]: https://blogs.msdn.microsoft.com/pfxteam/2012/03/24/should-i-expose-asynchronous-wrappers-for-synchronous-methods/
[performance-counters]: /azure/cloud-services/cloud-services-dotnet-diagnostics-performance-counters
[web-sites-monitor]: /azure/app-service-web/web-sites-monitor

[sync-performance]: _images/SyncPerformance.jpg
[async-performance]: _images/AsyncPerformance.jpg
