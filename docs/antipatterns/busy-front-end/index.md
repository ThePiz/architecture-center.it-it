---
title: Antipattern Front End occupato
description: "Le attività asincrone in un numero elevato di thread in background può privare delle risorse necessarie altre attività in primo piano."
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: cedb80ddac5ceb1eb901455df3165993fd28a138
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="busy-front-end-antipattern"></a><span data-ttu-id="5cd97-103">Antipattern Front End occupato</span><span class="sxs-lookup"><span data-stu-id="5cd97-103">Busy Front End antipattern</span></span>

<span data-ttu-id="5cd97-104">L'esecuzione di attività asincrone in un numero elevato di thread in background può rendere non disponibili le risorse per altre attività simultanee in primo piano generando tempi di risposta non accettabili.</span><span class="sxs-lookup"><span data-stu-id="5cd97-104">Performing asynchronous work on a large number of background threads can starve other concurrent foreground tasks of resources, decreasing response times to unacceptable levels.</span></span>

## <a name="problem-description"></a><span data-ttu-id="5cd97-105">Descrizione del problema</span><span class="sxs-lookup"><span data-stu-id="5cd97-105">Problem description</span></span>

<span data-ttu-id="5cd97-106">Le attività a elevato utilizzo di risorse possono allungare i tempi di risposta per le richieste utente e generare una latenza elevata.</span><span class="sxs-lookup"><span data-stu-id="5cd97-106">Resource-intensive tasks can increase the response times for user requests and cause high latency.</span></span> <span data-ttu-id="5cd97-107">Un modo per migliorare i tempi di risposta è eseguire l'offload di un'attività a elevato utilizzo di risorse in un thread separato.</span><span class="sxs-lookup"><span data-stu-id="5cd97-107">One way to improve response times is to offload a resource-intensive task to a separate thread.</span></span> <span data-ttu-id="5cd97-108">Questo approccio consente all'applicazione di fornire risposte tempestive durante l'elaborazione in background.</span><span class="sxs-lookup"><span data-stu-id="5cd97-108">This approach lets the application stay responsive while processing happens in the background.</span></span> <span data-ttu-id="5cd97-109">Tuttavia anche le attività eseguite in un thread in background usano risorse.</span><span class="sxs-lookup"><span data-stu-id="5cd97-109">However, tasks that run on a background thread still consume resources.</span></span> <span data-ttu-id="5cd97-110">Un numero elevato di attività può impegnare in modo eccessivo i thread che gestiscono le richieste.</span><span class="sxs-lookup"><span data-stu-id="5cd97-110">If there are too many of them, they can starve the threads that are handling requests.</span></span>

> [!NOTE]
> <span data-ttu-id="5cd97-111">Il termine generico *risorsa* può dare riferimento, ad esempio, all'utilizzo di CPU e memoria o alle operazioni di I/O del disco e della rete.</span><span class="sxs-lookup"><span data-stu-id="5cd97-111">The term *resource* can encompass many things, such as CPU utilization, memory occupancy, and network or disk I/O.</span></span>

<span data-ttu-id="5cd97-112">In genere questo problema si verifica con le applicazioni sviluppate come frammento di codice monolitico, in cui tutta la logica di business viene combinata in un singolo livello condiviso con il livello di presentazione.</span><span class="sxs-lookup"><span data-stu-id="5cd97-112">This problem typically occurs when an application is developed as monolithic piece of code, with all of the business logic combined into a single tier shared with the presentation layer.</span></span>

<span data-ttu-id="5cd97-113">L'esempio con ASP.NET seguente illustra il problema.</span><span class="sxs-lookup"><span data-stu-id="5cd97-113">Here’s an example using ASP.NET that demonstrates the problem.</span></span> <span data-ttu-id="5cd97-114">L'esempio completo è disponibile [qui][code-sample].</span><span class="sxs-lookup"><span data-stu-id="5cd97-114">You can find the complete sample [here][code-sample].</span></span>

```csharp
public class WorkInFrontEndController : ApiController
{
    [HttpPost]
    [Route("api/workinfrontend")]
    public HttpResponseMessage Post()
    {
        new Thread(() =>
        {
            //Simulate processing
            Thread.SpinWait(Int32.MaxValue / 100);
        }).Start();

        return Request.CreateResponse(HttpStatusCode.Accepted);
    }
}

public class UserProfileController : ApiController
{
    [HttpGet]
    [Route("api/userprofile/{id}")]
    public UserProfile Get(int id)
    {
        //Simulate processing
        return new UserProfile() { FirstName = "Alton", LastName = "Hudgens" };
    }
}
```

- <span data-ttu-id="5cd97-115">Il metodo `Post` del controller `WorkInFrontEnd` implementa un'operazione HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="5cd97-115">The `Post` method in the `WorkInFrontEnd` controller implements an HTTP POST operation.</span></span> <span data-ttu-id="5cd97-116">Questa operazione simula un'attività a esecuzione prolungata con utilizzo intensivo della CPU.</span><span class="sxs-lookup"><span data-stu-id="5cd97-116">This operation simulates a long-running, CPU-intensive task.</span></span> <span data-ttu-id="5cd97-117">L'attività viene eseguita in un thread separato nel tentativo di consentire il rapido completamento dell'operazione POST.</span><span class="sxs-lookup"><span data-stu-id="5cd97-117">The work is performed on a separate thread, in an attempt to enable the POST operation to complete quickly.</span></span>

- <span data-ttu-id="5cd97-118">Il metodo `Get` del controller `UserProfile` implementa un'operazione HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="5cd97-118">The `Get` method in the `UserProfile` controller implements an HTTP GET operation.</span></span> <span data-ttu-id="5cd97-119">Questo metodo implica un utilizzo della CPU notevolmente inferiore.</span><span class="sxs-lookup"><span data-stu-id="5cd97-119">This method is much less CPU intensive.</span></span>

<span data-ttu-id="5cd97-120">Il problema principale è il fabbisogno di risorse del metodo `Post`.</span><span class="sxs-lookup"><span data-stu-id="5cd97-120">The primary concern is the resource requirements of the `Post` method.</span></span> <span data-ttu-id="5cd97-121">Anche se l'attività viene inserita in un thread in background, è possibile che vengano usate notevoli risorse della CPU.</span><span class="sxs-lookup"><span data-stu-id="5cd97-121">Although it puts the work onto a background thread, the work can still consume considerable CPU resources.</span></span> <span data-ttu-id="5cd97-122">Queste risorse vengono condivise con altre operazioni eseguite da altri utenti simultanei.</span><span class="sxs-lookup"><span data-stu-id="5cd97-122">These resources are shared with other operations being performed by other concurrent users.</span></span> <span data-ttu-id="5cd97-123">Se un numero discreto di utenti invia questa richiesta nello stesso momento, è probabile che le prestazioni complessive si riducano, rallentando tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="5cd97-123">If a moderate number of users send this request at the same time, overall performance is likely to suffer, slowing down all operations.</span></span> <span data-ttu-id="5cd97-124">Gli utenti che usano il metodo `Get` potrebbero, ad esempio, riscontrare una latenza elevata.</span><span class="sxs-lookup"><span data-stu-id="5cd97-124">Users might experience significant latency in the `Get` method, for example.</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="5cd97-125">Come risolvere il problema</span><span class="sxs-lookup"><span data-stu-id="5cd97-125">How to fix the problem</span></span>

<span data-ttu-id="5cd97-126">Trasferire i processi con uso intensivo di risorse in un back-end separato.</span><span class="sxs-lookup"><span data-stu-id="5cd97-126">Move processes that consume significant resources to a separate back end.</span></span> 

<span data-ttu-id="5cd97-127">Con questo approccio, il front-end inserisce le attività a elevato utilizzo di risorse in una coda di messaggi.</span><span class="sxs-lookup"><span data-stu-id="5cd97-127">With this approach, the front end puts resource-intensive tasks onto a message queue.</span></span> <span data-ttu-id="5cd97-128">Il back-end preleva le attività per l'elaborazione asincrona.</span><span class="sxs-lookup"><span data-stu-id="5cd97-128">The back end picks up the tasks for asynchronous processing.</span></span> <span data-ttu-id="5cd97-129">La coda funge anche da livellatore di carico, memorizzando nel buffer le richieste per il back-end.</span><span class="sxs-lookup"><span data-stu-id="5cd97-129">The queue also acts as a load leveler, buffering requests for the back end.</span></span> <span data-ttu-id="5cd97-130">Se la coda diventa troppo lunga, è possibile configurare la scalabilità automatica in modo da scalare orizzontalmente il back-end.</span><span class="sxs-lookup"><span data-stu-id="5cd97-130">If the queue length becomes too long, you can configure autoscaling to scale out the back end.</span></span>

<span data-ttu-id="5cd97-131">Di seguito è riportata una versione modificata del codice precedente.</span><span class="sxs-lookup"><span data-stu-id="5cd97-131">Here is a revised version of the previous code.</span></span> <span data-ttu-id="5cd97-132">In questa versione il metodo `Post` inserisce un messaggio in una coda del bus di servizio.</span><span class="sxs-lookup"><span data-stu-id="5cd97-132">In this version, the `Post` method puts a message on a Service Bus queue.</span></span> 

```csharp
public class WorkInBackgroundController : ApiController
{
    private static readonly QueueClient QueueClient;
    private static readonly string QueueName;
    private static readonly ServiceBusQueueHandler ServiceBusQueueHandler;

    public WorkInBackgroundController()
    {
        var serviceBusConnectionString = ...;
        QueueName = ...;
        ServiceBusQueueHandler = new ServiceBusQueueHandler(serviceBusConnectionString);
        QueueClient = ServiceBusQueueHandler.GetQueueClientAsync(QueueName).Result;
    }

    [HttpPost]
    [Route("api/workinbackground")]
    public async Task<long> Post()
    {
        return await ServiceBusQueuehandler.AddWorkLoadToQueueAsync(QueueClient, QueueName, 0);
    }
}
```

<span data-ttu-id="5cd97-133">Il back-end estrae i messaggi dalla coda del bus di servizio ed esegue l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="5cd97-133">The back end pulls messages from the Service Bus queue and does the processing.</span></span>

```csharp
public async Task RunAsync(CancellationToken cancellationToken)
{
    this._queueClient.OnMessageAsync(
        // This lambda is invoked for each message received.
        async (receivedMessage) =>
        {
            try
            {
                // Simulate processing of message
                Thread.SpinWait(Int32.Maxvalue / 1000);

                await receivedMessage.CompleteAsync();
            }
            catch
            {
                receivedMessage.Abandon();
            }
        });
}
```

## <a name="considerations"></a><span data-ttu-id="5cd97-134">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="5cd97-134">Considerations</span></span>

- <span data-ttu-id="5cd97-135">Questo approccio aggiunge un ulteriore livello di complessità all'applicazione.</span><span class="sxs-lookup"><span data-stu-id="5cd97-135">This approach adds some additional complexity to the application.</span></span> <span data-ttu-id="5cd97-136">È necessario gestire in modo sicuro l'accodamento e la rimozione dalla coda per evitare la perdita di richieste in caso di errore.</span><span class="sxs-lookup"><span data-stu-id="5cd97-136">You must handle queuing and dequeuing safely to avoid losing requests in the event of a failure.</span></span>
- <span data-ttu-id="5cd97-137">L'applicazione stabilisce una dipendenza da un servizio aggiuntivo per la coda di messaggi.</span><span class="sxs-lookup"><span data-stu-id="5cd97-137">The application takes a dependency on an additional service for the message queue.</span></span>
- <span data-ttu-id="5cd97-138">L'ambiente di elaborazione deve essere sufficientemente scalabile per gestire il carico di lavoro previsto e soddisfare gli obiettivi di velocità effettiva necessari.</span><span class="sxs-lookup"><span data-stu-id="5cd97-138">The processing environment must be sufficiently scalable to handle the expected workload and meet the required throughput targets.</span></span>
- <span data-ttu-id="5cd97-139">Sebbene questo approccio migliori la velocità di risposta complessiva, il completamento delle attività trasferite nel back-end può richiedere più tempo.</span><span class="sxs-lookup"><span data-stu-id="5cd97-139">While this approach should improve overall responsiveness, the tasks that are moved to the back end may take longer to complete.</span></span> 

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="5cd97-140">Come rilevare il problema</span><span class="sxs-lookup"><span data-stu-id="5cd97-140">How to detect the problem</span></span>

<span data-ttu-id="5cd97-141">Uno dei sintomi di un front-end occupato è la latenza elevata durante l'esecuzione delle attività a elevato utilizzo di risorse.</span><span class="sxs-lookup"><span data-stu-id="5cd97-141">Symptoms of a busy front end include high latency when resource-intensive tasks are being performed.</span></span> <span data-ttu-id="5cd97-142">Gli utenti finali riferiscono in genere tempi di risposta prolungati o errori causati dal timeout dei servizi. È possibile che vengano restituiti anche gli errori HTTP 500 (Server interno) o HTTP 503 (Servizio non disponibile).</span><span class="sxs-lookup"><span data-stu-id="5cd97-142">End users are likely to report extended response times or failures caused by services timing out. These failures could also return HTTP 500 (Internal Server) errors or HTTP 503 (Service Unavailable) errors.</span></span> <span data-ttu-id="5cd97-143">Esaminare i log di eventi per il server Web che possono contenere informazioni più dettagliate sulle cause e le circostanze degli errori.</span><span class="sxs-lookup"><span data-stu-id="5cd97-143">Examine the event logs for the web server, which are likely to contain more detailed information about the causes and circumstances of the errors.</span></span>

<span data-ttu-id="5cd97-144">Per provare a identificare questo problema è possibile eseguire la procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="5cd97-144">You can perform the following steps to help identify this problem:</span></span>

1. <span data-ttu-id="5cd97-145">Eseguire il monitoraggio del processo per il sistema di produzione per identificare i punti associati a tempi di risposta più lunghi.</span><span class="sxs-lookup"><span data-stu-id="5cd97-145">Perform process monitoring of the production system, to identify points when response times slow down.</span></span>
2. <span data-ttu-id="5cd97-146">Esaminare i dati di telemetria acquisiti in questi punti per determinare la combinazione di operazioni eseguite e di risorse usate.</span><span class="sxs-lookup"><span data-stu-id="5cd97-146">Examine the telemetry data captured at these points to determine the mix of operations being performed and the resources being used.</span></span> 
3. <span data-ttu-id="5cd97-147">Individuare eventuali correlazioni tra i tempi di risposta insoddisfacenti e i volumi e le combinazioni di operazioni eseguite in questi intervalli.</span><span class="sxs-lookup"><span data-stu-id="5cd97-147">Find any correlations between poor response times and the volumes and combinations of operations that were happening at those times.</span></span>
4. <span data-ttu-id="5cd97-148">Eseguire il test di carico di ogni operazione sospetta per identificare quali operazioni stanno usando le risorse rendendole non disponibili ad altre operazioni.</span><span class="sxs-lookup"><span data-stu-id="5cd97-148">Load test each suspected operation to identify which operations are consuming resources and starving other operations.</span></span> 
5. <span data-ttu-id="5cd97-149">Esaminare il codice sorgente di queste operazioni per determinare la possibile causa dell'eccessivo consumo eccessivo di risorse.</span><span class="sxs-lookup"><span data-stu-id="5cd97-149">Review the source code for those operations to determine why they might cause excessive resource consumption.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="5cd97-150">Diagnosi di esempio</span><span class="sxs-lookup"><span data-stu-id="5cd97-150">Example diagnosis</span></span> 

<span data-ttu-id="5cd97-151">Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="5cd97-151">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="identify-points-of-slowdown"></a><span data-ttu-id="5cd97-152">Identificare i punti di rallentamento</span><span class="sxs-lookup"><span data-stu-id="5cd97-152">Identify points of slowdown</span></span>

<span data-ttu-id="5cd97-153">Instrumentare ciascun metodo per tenere traccia della durata e delle risorse usate da ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="5cd97-153">Instrument each method to track the duration and resources consumed by each request.</span></span> <span data-ttu-id="5cd97-154">Monitorare quindi l'applicazione in produzione.</span><span class="sxs-lookup"><span data-stu-id="5cd97-154">Then monitor the application in production.</span></span> <span data-ttu-id="5cd97-155">In questo modo si ottiene una visione generale del modo in cui le richieste si contendono le risorse.</span><span class="sxs-lookup"><span data-stu-id="5cd97-155">This can provide an overall view of how requests compete with each other.</span></span> <span data-ttu-id="5cd97-156">Durante i periodi di stress, è probabile che le richieste a esecuzione lenta con elevato utilizzo di risorse impattino sulle altre operazioni; analizzare questo comportamento monitorando il sistema e rilevando il calo di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="5cd97-156">During periods of stress, slow-running resource-hungry requests will likely impact other operations, and this behavior can be observed by monitoring the system and noting the drop off in performance.</span></span>

<span data-ttu-id="5cd97-157">L'immagine seguente mostra un dashboard di monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="5cd97-157">The following image shows a monitoring dashboard.</span></span> <span data-ttu-id="5cd97-158">Per i test è stato usato [AppDynamics]. Inizialmente il carico del sistema è ridotto.</span><span class="sxs-lookup"><span data-stu-id="5cd97-158">(We used [AppDynamics] for our tests.) Initially, the system has light load.</span></span> <span data-ttu-id="5cd97-159">Gli utenti iniziano quindi a richiedere il metodo GET `UserProfile`.</span><span class="sxs-lookup"><span data-stu-id="5cd97-159">Then users start requesting the `UserProfile` GET method.</span></span> <span data-ttu-id="5cd97-160">Le prestazioni sono abbastanza soddisfacenti fino a quando altri utenti iniziano ad inviare richieste al metodo POST `WorkInFrontEnd`.</span><span class="sxs-lookup"><span data-stu-id="5cd97-160">The performance is reasonably good until other users start issuing requests to the `WorkInFrontEnd` POST method.</span></span> <span data-ttu-id="5cd97-161">A questo punto i tempi di risposta si allungano notevolmente (prima freccia).</span><span class="sxs-lookup"><span data-stu-id="5cd97-161">At that point, response times increase dramatically (first arrow).</span></span> <span data-ttu-id="5cd97-162">I tempi di risposta migliorano solo dopo la diminuzione del volume di richieste per il controller `WorkInFrontEnd` (seconda freccia).</span><span class="sxs-lookup"><span data-stu-id="5cd97-162">Response times only improve after the volume of requests to the `WorkInFrontEnd` controller diminishes (second arrow).</span></span>

![Riquadro delle transazioni business di AppDynamics che mostra gli effetti dei tempi di risposta di tutte le richieste quando viene usato il controller WorkInFrontEnd][AppDynamics-Transactions-Front-End-Requests]

### <a name="examine-telemetry-data-and-find-correlations"></a><span data-ttu-id="5cd97-164">Esaminare i dati di telemetria e trovare le correlazioni</span><span class="sxs-lookup"><span data-stu-id="5cd97-164">Examine telemetry data and find correlations</span></span>

<span data-ttu-id="5cd97-165">L'immagine successiva illustra alcune metriche raccolte per monitorare l'utilizzo delle risorse durante lo stesso intervallo.</span><span class="sxs-lookup"><span data-stu-id="5cd97-165">The next image shows some of the metrics gathered to monitor resource utilization during the same interval.</span></span> <span data-ttu-id="5cd97-166">Inizialmente pochi utenti accedono al sistema.</span><span class="sxs-lookup"><span data-stu-id="5cd97-166">At first, few users are accessing the system.</span></span> <span data-ttu-id="5cd97-167">Quando il numero di utenti che si connette al sistema aumenta, l'utilizzo della CPU diventa molto elevato (100%).</span><span class="sxs-lookup"><span data-stu-id="5cd97-167">As more users connect, CPU utilization becomes very high (100%).</span></span> <span data-ttu-id="5cd97-168">Si noti anche che la velocità di I/O della rete aumenta inizialmente quando aumenta l'utilizzo della CPU.</span><span class="sxs-lookup"><span data-stu-id="5cd97-168">Also notice that the network I/O rate initially goes up as CPU usage rises.</span></span> <span data-ttu-id="5cd97-169">Quando tuttavia si verifica un picco di utilizzo della CPU, la velocità di I/O della rete in realtà rallenta.</span><span class="sxs-lookup"><span data-stu-id="5cd97-169">But once CPU usage peaks, network I/O actually goes down.</span></span> <span data-ttu-id="5cd97-170">Ciò avviene perché il sistema può gestire solo un numero relativamente ridotto di richieste quando la CPU raggiunge la capacità massima.</span><span class="sxs-lookup"><span data-stu-id="5cd97-170">That's because the system can only handle a relatively small number of requests once the CPU is at capacity.</span></span> <span data-ttu-id="5cd97-171">A mano a mano che gli utenti si disconnettono, il carico della CPU diminuisce.</span><span class="sxs-lookup"><span data-stu-id="5cd97-171">As users disconnect, the CPU load tails off.</span></span>

![Metriche di AppDynamics che mostrano l'utilizzo della CPU e della rete][AppDynamics-Metrics-Front-End-Requests]

<span data-ttu-id="5cd97-173">A questo punto sembra che il metodo `Post` nel controller `WorkInFrontEnd` sia il candidato ideale per un esame più attento.</span><span class="sxs-lookup"><span data-stu-id="5cd97-173">At this point, it appears the `Post` method in the `WorkInFrontEnd` controller is a prime candidate for closer examination.</span></span> <span data-ttu-id="5cd97-174">Per confermare l'ipotesi, sono necessarie ulteriori attività in un ambiente controllato.</span><span class="sxs-lookup"><span data-stu-id="5cd97-174">Further work in a controlled environment is needed to confirm the hypothesis.</span></span>

### <a name="perform-load-testing"></a><span data-ttu-id="5cd97-175">Eseguire il test di carico</span><span class="sxs-lookup"><span data-stu-id="5cd97-175">Perform load testing</span></span> 

<span data-ttu-id="5cd97-176">Il passaggio successivo è eseguire i test in un ambiente controllato.</span><span class="sxs-lookup"><span data-stu-id="5cd97-176">The next step is to perform tests in a controlled environment.</span></span> <span data-ttu-id="5cd97-177">Ad esempio, eseguire una serie di test di carico includendo e quindi omettendo a turno ogni richiesta per verificare gli effetti.</span><span class="sxs-lookup"><span data-stu-id="5cd97-177">For example, run a series of load tests that include and then omit each request in turn to see the effects.</span></span>

<span data-ttu-id="5cd97-178">Il grafo seguente mostra i risultati dei test di carico eseguiti in una distribuzione identica del servizio cloud usata nei test precedenti.</span><span class="sxs-lookup"><span data-stu-id="5cd97-178">The graph below shows the results of a load test performed against an identical deployment of the cloud service used in the previous tests.</span></span> <span data-ttu-id="5cd97-179">Nel test è stato usato un carico costante di 500 utenti che eseguono l'operazione `Get` nel controller `UserProfile`, oltre a un carico per passaggio di utenti che eseguono l'operazione `Post` nel controller `WorkInFrontEnd`.</span><span class="sxs-lookup"><span data-stu-id="5cd97-179">The test used a constant load of 500 users performing the `Get` operation in the `UserProfile` controller, along with a step load of users performing the `Post` operation in the `WorkInFrontEnd` controller.</span></span> 

![Risultati del test di carico iniziale per il controller WorkInFrontEnd][Initial-Load-Test-Results-Front-End]

<span data-ttu-id="5cd97-181">Inizialmente il carico per passaggio è 0 e quindi solo gli utenti attivi stanno eseguendo le richieste `UserProfile`.</span><span class="sxs-lookup"><span data-stu-id="5cd97-181">Initially, the step load is 0, so the only active users are performing the `UserProfile` requests.</span></span> <span data-ttu-id="5cd97-182">Il sistema è in grado di rispondere a circa 500 richieste al secondo.</span><span class="sxs-lookup"><span data-stu-id="5cd97-182">The system is able to respond to approximately 500 requests per second.</span></span> <span data-ttu-id="5cd97-183">Dopo 60 secondi un carico di 100 utenti aggiuntivi inizia a inviare richieste POST al controller `WorkInFrontEnd`.</span><span class="sxs-lookup"><span data-stu-id="5cd97-183">After 60 seconds, a load of 100 additional users starts sending POST requests to the `WorkInFrontEnd` controller.</span></span> <span data-ttu-id="5cd97-184">Quasi immediatamente, il carico di lavoro inviato al controller `UserProfile` diminuisce fino a circa 150 richieste al secondo.</span><span class="sxs-lookup"><span data-stu-id="5cd97-184">Almost immediately, the workload sent to the `UserProfile` controller drops to about 150 requests per second.</span></span> <span data-ttu-id="5cd97-185">Ciò è dovuto alla modalità di funzionamento dello strumento di esecuzione dei test di carico.</span><span class="sxs-lookup"><span data-stu-id="5cd97-185">This is due to the way the load-test runner functions.</span></span> <span data-ttu-id="5cd97-186">Attende una risposta prima di inviare la richiesta successiva; maggiore è il tempo impiegato per ricevere una risposta, minore sarà la frequenza di richieste.</span><span class="sxs-lookup"><span data-stu-id="5cd97-186">It waits for a response before sending the next request, so the longer it takes to receive a response, the lower the request rate.</span></span>

<span data-ttu-id="5cd97-187">Quando il numero di utenti che invia richieste POST al controller `WorkInFrontEnd` aumenta, la velocità di risposta del controller `UserProfile` diminuisce.</span><span class="sxs-lookup"><span data-stu-id="5cd97-187">As more users send POST requests to the `WorkInFrontEnd` controller, the response rate of the `UserProfile` controller continues to drop.</span></span> <span data-ttu-id="5cd97-188">Si noti che il volume di richieste gestito dal controller `WorkInFrontEnd` rimane relativamente costante.</span><span class="sxs-lookup"><span data-stu-id="5cd97-188">But note that the volume of requests handled by the `WorkInFrontEnd`controller remains relatively constant.</span></span> <span data-ttu-id="5cd97-189">La saturazione del sistema diventa evidente quando la velocità globale di entrambe le richieste tende verso un limite fisso ma basso.</span><span class="sxs-lookup"><span data-stu-id="5cd97-189">The saturation of the system becomes apparent as the overall rate of both requests tends towards a steady but low limit.</span></span>


### <a name="review-the-source-code"></a><span data-ttu-id="5cd97-190">Esaminare il codice sorgente</span><span class="sxs-lookup"><span data-stu-id="5cd97-190">Review the source code</span></span>

<span data-ttu-id="5cd97-191">Il passaggio finale consiste nell'esaminare il codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="5cd97-191">The final step is to look at the source code.</span></span> <span data-ttu-id="5cd97-192">Il team di sviluppo è consapevole che il metodo `Post` può richiedere una notevole quantità di tempo e, per questo motivo, è stato usato un thread separato nell'implementazione originale.</span><span class="sxs-lookup"><span data-stu-id="5cd97-192">The development team was aware that the `Post` method could take a considerable amount of time, which is why the original implementation used a separate thread.</span></span> <span data-ttu-id="5cd97-193">Questa scelta ha risolto il problema immediato perché il metodo `Post` non si è bloccato attendendo il completamento di un'attività a esecuzione prolungata.</span><span class="sxs-lookup"><span data-stu-id="5cd97-193">That solved the immediate problem, because the `Post` method did not block waiting for a long-running task to complete.</span></span>

<span data-ttu-id="5cd97-194">Tuttavia, le attività eseguite da questo metodo usano ancora risorse di CPU, memoria e di altro tipo.</span><span class="sxs-lookup"><span data-stu-id="5cd97-194">However, the work performed by this method still consumes CPU, memory, and other resources.</span></span> <span data-ttu-id="5cd97-195">L'abilitazione di questo processo per l'esecuzione asincrona potrebbe effettivamente compromettere le prestazioni, poiché gli utenti possono attivare contemporaneamente un numero elevato di operazioni di questo tipo in modo incontrollato.</span><span class="sxs-lookup"><span data-stu-id="5cd97-195">Enabling this process to run asynchronously might actually damage performance, as users can trigger a large number of these operations simultaneously, in an uncontrolled manner.</span></span> <span data-ttu-id="5cd97-196">È previsto un limite al numero di thread che un server può eseguire.</span><span class="sxs-lookup"><span data-stu-id="5cd97-196">There is a limit to the number of threads that a server can run.</span></span> <span data-ttu-id="5cd97-197">Superando questo limite è probabile che venga generata un'eccezione durante il tentativo di avviare un nuovo thread.</span><span class="sxs-lookup"><span data-stu-id="5cd97-197">Past this limit, the application is likely to get an exception when it tries to start a new thread.</span></span>

> [!NOTE]
> <span data-ttu-id="5cd97-198">Questo non significa che è consigliabile evitare operazioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="5cd97-198">This doesn't mean you should avoid asynchronous operations.</span></span> <span data-ttu-id="5cd97-199">L'esecuzione di un'operazione await asincrona in una chiamata di rete è una procedura consigliata.</span><span class="sxs-lookup"><span data-stu-id="5cd97-199">Performing an asynchronous await on a network call is a recommended practice.</span></span> <span data-ttu-id="5cd97-200">Vedere l'antipattern [I/O sincrono][sync-io]. In questo caso il problema è che un'operazione con utilizzo elevato di CPU è stata generata in un altro thread.</span><span class="sxs-lookup"><span data-stu-id="5cd97-200">(See the [Synchronous I/O][sync-io] antipattern.) The problem here is that CPU-intensive work was spawned on another thread.</span></span> 

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="5cd97-201">Implementare la soluzione e verificare il risultato</span><span class="sxs-lookup"><span data-stu-id="5cd97-201">Implement the solution and verify the result</span></span>

<span data-ttu-id="5cd97-202">L'immagine seguente mostra il monitoraggio delle prestazioni dopo l'implementazione della soluzione.</span><span class="sxs-lookup"><span data-stu-id="5cd97-202">The following image shows performance monitoring after the solution was implemented.</span></span> <span data-ttu-id="5cd97-203">Il carico è simile a quello illustrato in precedenza, ma i tempi di risposta per il controller `UserProfile` sono ora molto più veloci.</span><span class="sxs-lookup"><span data-stu-id="5cd97-203">The load was similar to that shown earlier, but the response times for the `UserProfile` controller are now much faster.</span></span> <span data-ttu-id="5cd97-204">Il volume di richieste è aumentato nella stessa durata da 2.759 a 23.565.</span><span class="sxs-lookup"><span data-stu-id="5cd97-204">The volume of requests increased over the same duration, from 2,759 to 23,565.</span></span> 

![Riquadro delle transazioni business di AppDynamics che mostra gli effetti dei tempi di risposta di tutte le richieste quando viene usato il controller WorkInBackground][AppDynamics-Transactions-Background-Requests]

<span data-ttu-id="5cd97-206">Si noti che il controller `WorkInBackground` ha inoltre gestito un volume di richieste molto più ampio.</span><span class="sxs-lookup"><span data-stu-id="5cd97-206">Note that the `WorkInBackground` controller also handled a much larger volume of requests.</span></span> <span data-ttu-id="5cd97-207">Tuttavia non è possibile eseguire un confronto diretto in questo caso, poiché il lavoro in esecuzione in questo controller è molto diverso dal codice originale.</span><span class="sxs-lookup"><span data-stu-id="5cd97-207">However, you can't make a direct comparison in this case, because the work being performed in this controller is very different from the original code.</span></span> <span data-ttu-id="5cd97-208">La nuova versione accoda semplicemente una richiesta, anziché eseguire un calcolo che richiede molto tempo.</span><span class="sxs-lookup"><span data-stu-id="5cd97-208">The new version simply queues a request, rather than performing a time consuming calculation.</span></span> <span data-ttu-id="5cd97-209">Il punto principale è che questo metodo non trascina più l'intero sistema sotto carico.</span><span class="sxs-lookup"><span data-stu-id="5cd97-209">The main point is that this method no longer drags down the entire system under load.</span></span>

<span data-ttu-id="5cd97-210">Anche l'utilizzo della CPU e della rete indica prestazioni migliori.</span><span class="sxs-lookup"><span data-stu-id="5cd97-210">CPU and network utilization also show the improved performance.</span></span> <span data-ttu-id="5cd97-211">L'utilizzo della CPU non ha mai raggiunto il 100% e il volume delle richieste di rete gestite è notevolmente maggiore del precedente e non si attenua finché il carico di lavoro non diminuisce.</span><span class="sxs-lookup"><span data-stu-id="5cd97-211">The CPU utilization never reached 100%, and the volume of handled network requests was far greater than earlier, and did not tail off until the workload dropped.</span></span>

![Metriche di AppDynamics che mostrano l'utilizzo della CPU e della rete per il controller WorkInBackground][AppDynamics-Metrics-Background-Requests]

<span data-ttu-id="5cd97-213">Il grafo seguente mostra i risultati di un test di carico.</span><span class="sxs-lookup"><span data-stu-id="5cd97-213">The following graph shows the results of a load test.</span></span> <span data-ttu-id="5cd97-214">Il volume complessivo di richieste elaborate risulta notevolmente migliore rispetto ai test precedenti.</span><span class="sxs-lookup"><span data-stu-id="5cd97-214">The overall volume of requests serviced is greatly improved compared to the the earlier tests.</span></span>

![Risultati dei test di carico per il controller BackgroundImageProcessing][Load-Test-Results-Background]

## <a name="related-guidance"></a><span data-ttu-id="5cd97-216">Informazioni correlate</span><span class="sxs-lookup"><span data-stu-id="5cd97-216">Related guidance</span></span>

- <span data-ttu-id="5cd97-217">[Autoscaling best practices][autoscaling] (Procedure consigliate per la scalabilità automatica).</span><span class="sxs-lookup"><span data-stu-id="5cd97-217">[Autoscaling best practices][autoscaling]</span></span>
- <span data-ttu-id="5cd97-218">[Background jobs best practices][background-jobs] (Procedure consigliate per i processi in background)</span><span class="sxs-lookup"><span data-stu-id="5cd97-218">[Background jobs best practices][background-jobs]</span></span>
- <span data-ttu-id="5cd97-219">[Schema di livellamento del carico basato sulle code][load-leveling]</span><span class="sxs-lookup"><span data-stu-id="5cd97-219">[Queue-Based Load Leveling pattern][load-leveling]</span></span>
- <span data-ttu-id="5cd97-220">[Stile di architettura Web/coda/ruolo di lavoro][web-queue-worker]</span><span class="sxs-lookup"><span data-stu-id="5cd97-220">[Web Queue Worker architecture style][web-queue-worker]</span></span>

[AppDyanamics]: https://www.appdynamics.com/
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[background-jobs]: /azure/architecture/best-practices/background-jobs
[code-sample]: https://github.com/mspnp/performance-optimization/tree/master/BusyFrontEnd
[fullDemonstrationOfSolution]: https://github.com/mspnp/performance-optimization/tree/master/BusyFrontEnd
[load-leveling]: /azure/architecture/patterns/queue-based-load-leveling
[sync-io]: ../synchronous-io/index.md
[web-queue-worker]: /azure/architecture/guide/architecture-styles/web-queue-worker

[WebJobs]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
[ComputePartitioning]: https://msdn.microsoft.com/library/dn589773.aspx
[ServiceBusQueues]: https://msdn.microsoft.com/library/azure/hh367516.aspx
[AppDynamics-Transactions-Front-End-Requests]: ./_images/AppDynamicsPerformanceStats.jpg
[AppDynamics-Metrics-Front-End-Requests]: ./_images/AppDynamicsFrontEndMetrics.jpg
[Initial-Load-Test-Results-Front-End]: ./_images/InitialLoadTestResultsFrontEnd.jpg
[AppDynamics-Transactions-Background-Requests]: ./_images/AppDynamicsBackgroundPerformanceStats.jpg
[AppDynamics-Metrics-Background-Requests]: ./_images/AppDynamicsBackgroundMetrics.jpg
[Load-Test-Results-Background]: ./_images/LoadTestResultsBackground.jpg


