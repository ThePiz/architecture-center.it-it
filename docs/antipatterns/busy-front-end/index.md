---
title: Antipattern Front End occupato
titleSuffix: Performance antipatterns for cloud apps
description: Le attività asincrone in un numero elevato di thread in background può privare delle risorse necessarie altre attività in primo piano.
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
---

# <a name="busy-front-end-antipattern"></a>Antipattern Front End occupato

L'esecuzione di attività asincrone in un numero elevato di thread in background può rendere non disponibili le risorse per altre attività simultanee in primo piano generando tempi di risposta non accettabili.

## <a name="problem-description"></a>Descrizione del problema

Le attività a elevato utilizzo di risorse possono allungare i tempi di risposta per le richieste utente e generare una latenza elevata. Un modo per migliorare i tempi di risposta è eseguire l'offload di un'attività a elevato utilizzo di risorse in un thread separato. Questo approccio consente all'applicazione di fornire risposte tempestive durante l'elaborazione in background. Tuttavia anche le attività eseguite in un thread in background usano risorse. Un numero elevato di attività può impegnare in modo eccessivo i thread che gestiscono le richieste.

> [!NOTE]
> Il termine generico *risorsa* può dare riferimento, ad esempio, all'utilizzo di CPU e memoria o alle operazioni di I/O del disco e della rete.

In genere questo problema si verifica con le applicazioni sviluppate come frammento di codice monolitico, in cui tutta la logica di business viene combinata in un singolo livello condiviso con il livello di presentazione.

L'esempio con ASP.NET seguente illustra il problema. L'esempio completo è disponibile [qui][code-sample].

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

- Il metodo `Post` del controller `WorkInFrontEnd` implementa un'operazione HTTP POST. Questa operazione simula un'attività a esecuzione prolungata con utilizzo intensivo della CPU. L'attività viene eseguita in un thread separato nel tentativo di consentire il rapido completamento dell'operazione POST.

- Il metodo `Get` del controller `UserProfile` implementa un'operazione HTTP GET. Questo metodo implica un utilizzo della CPU notevolmente inferiore.

Il problema principale è il fabbisogno di risorse del metodo `Post`. Anche se l'attività viene inserita in un thread in background, è possibile che vengano usate notevoli risorse della CPU. Queste risorse vengono condivise con altre operazioni eseguite da altri utenti simultanei. Se un numero discreto di utenti invia questa richiesta nello stesso momento, è probabile che le prestazioni complessive si riducano, rallentando tutte le operazioni. Gli utenti che usano il metodo `Get` potrebbero, ad esempio, riscontrare una latenza elevata.

## <a name="how-to-fix-the-problem"></a>Come risolvere il problema

Trasferire i processi con uso intensivo di risorse in un back-end separato.

Con questo approccio, il front-end inserisce le attività a elevato utilizzo di risorse in una coda di messaggi. Il back-end preleva le attività per l'elaborazione asincrona. La coda funge anche da livellatore di carico, memorizzando nel buffer le richieste per il back-end. Se la coda diventa troppo lunga, è possibile configurare la scalabilità automatica in modo da scalare orizzontalmente il back-end.

Di seguito è riportata una versione modificata del codice precedente. In questa versione il metodo `Post` inserisce un messaggio in una coda del bus di servizio.

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

Il back-end estrae i messaggi dalla coda del bus di servizio ed esegue l'elaborazione.

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

## <a name="considerations"></a>Considerazioni

- Questo approccio aggiunge un ulteriore livello di complessità all'applicazione. È necessario gestire in modo sicuro l'accodamento e la rimozione dalla coda per evitare la perdita di richieste in caso di errore.
- L'applicazione stabilisce una dipendenza da un servizio aggiuntivo per la coda di messaggi.
- L'ambiente di elaborazione deve essere sufficientemente scalabile per gestire il carico di lavoro previsto e soddisfare gli obiettivi di velocità effettiva necessari.
- Sebbene questo approccio migliori la velocità di risposta complessiva, il completamento delle attività trasferite nel back-end può richiedere più tempo.

## <a name="how-to-detect-the-problem"></a>Come rilevare il problema

Uno dei sintomi di un front-end occupato è la latenza elevata durante l'esecuzione delle attività a elevato utilizzo di risorse. Gli utenti finali riferiscono in genere tempi di risposta prolungati o errori causati dal timeout dei servizi. È possibile che vengano restituiti anche gli errori HTTP 500 (Server interno) o HTTP 503 (Servizio non disponibile). Esaminare i log di eventi per il server Web che possono contenere informazioni più dettagliate sulle cause e le circostanze degli errori.

Per provare a identificare questo problema è possibile eseguire la procedura seguente:

1. Eseguire il monitoraggio del processo per il sistema di produzione per identificare i punti associati a tempi di risposta più lunghi.
2. Esaminare i dati di telemetria acquisiti in questi punti per determinare la combinazione di operazioni eseguite e di risorse usate.
3. Individuare eventuali correlazioni tra i tempi di risposta insoddisfacenti e i volumi e le combinazioni di operazioni eseguite in questi intervalli.
4. Eseguire il test di carico di ogni operazione sospetta per identificare quali operazioni stanno usando le risorse rendendole non disponibili ad altre operazioni.
5. Esaminare il codice sorgente di queste operazioni per determinare la possibile causa dell'eccessivo consumo eccessivo di risorse.

## <a name="example-diagnosis"></a>Diagnosi di esempio

Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.

### <a name="identify-points-of-slowdown"></a>Identificare i punti di rallentamento

Instrumentare ciascun metodo per tenere traccia della durata e delle risorse usate da ogni richiesta. Monitorare quindi l'applicazione in produzione. In questo modo si ottiene una visione generale del modo in cui le richieste si contendono le risorse. Durante i periodi di stress, è probabile che le richieste a esecuzione lenta con elevato utilizzo di risorse influiscano sulle altre operazioni; analizzare questo comportamento monitorando il sistema e rilevando il calo di prestazioni.

L'immagine seguente mostra un dashboard di monitoraggio. Per i test è stato usato [AppDynamics]. Inizialmente il carico del sistema è ridotto. Gli utenti iniziano quindi a richiedere il metodo GET `UserProfile`. Le prestazioni sono abbastanza soddisfacenti fino a quando altri utenti iniziano ad inviare richieste al metodo POST `WorkInFrontEnd`. A questo punto i tempi di risposta si allungano notevolmente (prima freccia). I tempi di risposta migliorano solo dopo la diminuzione del volume di richieste per il controller `WorkInFrontEnd` (seconda freccia).

![Riquadro delle transazioni business di AppDynamics che mostra gli effetti dei tempi di risposta di tutte le richieste quando viene usato il controller WorkInFrontEnd][AppDynamics-Transactions-Front-End-Requests]

### <a name="examine-telemetry-data-and-find-correlations"></a>Esaminare i dati di telemetria e trovare le correlazioni

L'immagine successiva illustra alcune metriche raccolte per monitorare l'utilizzo delle risorse durante lo stesso intervallo. Inizialmente pochi utenti accedono al sistema. Quando il numero di utenti che si connette al sistema aumenta, l'utilizzo della CPU diventa molto elevato (100%). Si noti anche che la velocità di I/O della rete aumenta inizialmente quando aumenta l'utilizzo della CPU. Quando tuttavia si verifica un picco di utilizzo della CPU, la velocità di I/O della rete in realtà rallenta. Ciò avviene perché il sistema può gestire solo un numero relativamente ridotto di richieste quando la CPU raggiunge la capacità massima. A mano a mano che gli utenti si disconnettono, il carico della CPU diminuisce.

![Metriche di AppDynamics che mostrano l'utilizzo della CPU e della rete][AppDynamics-Metrics-Front-End-Requests]

A questo punto sembra che il metodo `Post` nel controller `WorkInFrontEnd` sia il candidato ideale per un esame più attento. Per confermare l'ipotesi, sono necessarie ulteriori attività in un ambiente controllato.

### <a name="perform-load-testing"></a>Eseguire il test di carico

Il passaggio successivo è eseguire i test in un ambiente controllato. Ad esempio, eseguire una serie di test di carico includendo e quindi omettendo a turno ogni richiesta per verificare gli effetti.

Il grafo seguente mostra i risultati dei test di carico eseguiti in una distribuzione identica del servizio cloud usata nei test precedenti. Nel test è stato usato un carico costante di 500 utenti che eseguono l'operazione `Get` nel controller `UserProfile`, oltre a un carico per passaggio di utenti che eseguono l'operazione `Post` nel controller `WorkInFrontEnd`.

![Risultati del test di carico iniziale per il controller WorkInFrontEnd][Initial-Load-Test-Results-Front-End]

Inizialmente il carico per passaggio è 0 e quindi solo gli utenti attivi stanno eseguendo le richieste `UserProfile`. Il sistema è in grado di rispondere a circa 500 richieste al secondo. Dopo 60 secondi un carico di 100 utenti aggiuntivi inizia a inviare richieste POST al controller `WorkInFrontEnd`. Quasi immediatamente, il carico di lavoro inviato al controller `UserProfile` diminuisce fino a circa 150 richieste al secondo. Ciò è dovuto alla modalità di funzionamento dello strumento di esecuzione dei test di carico. Attende una risposta prima di inviare la richiesta successiva; maggiore è il tempo impiegato per ricevere una risposta, minore sarà la frequenza di richieste.

Quando il numero di utenti che invia richieste POST al controller `WorkInFrontEnd` aumenta, la velocità di risposta del controller `UserProfile` diminuisce. Si noti che il volume di richieste gestito dal controller `WorkInFrontEnd` rimane relativamente costante. La saturazione del sistema diventa evidente quando la velocità globale di entrambe le richieste tende verso un limite fisso ma basso.

### <a name="review-the-source-code"></a>Esaminare il codice sorgente

Il passaggio finale consiste nell'esaminare il codice sorgente. Il team di sviluppo è consapevole che il metodo `Post` può richiedere una notevole quantità di tempo e, per questo motivo, è stato usato un thread separato nell'implementazione originale. Questa scelta ha risolto il problema immediato perché il metodo `Post` non si è bloccato attendendo il completamento di un'attività a esecuzione prolungata.

Tuttavia, le attività eseguite da questo metodo usano ancora risorse di CPU, memoria e di altro tipo. L'abilitazione di questo processo per l'esecuzione asincrona potrebbe effettivamente compromettere le prestazioni, poiché gli utenti possono attivare contemporaneamente un numero elevato di operazioni di questo tipo in modo incontrollato. È previsto un limite al numero di thread che un server può eseguire. Superando questo limite è probabile che venga generata un'eccezione durante il tentativo di avviare un nuovo thread.

> [!NOTE]
> Questo non significa che è consigliabile evitare operazioni asincrone. L'esecuzione di un'operazione await asincrona in una chiamata di rete è una procedura consigliata. Vedere l'antipattern [I/O sincrono][sync-io]. In questo caso il problema è che un'operazione con utilizzo elevato di CPU è stata generata in un altro thread.

### <a name="implement-the-solution-and-verify-the-result"></a>Implementare la soluzione e verificare il risultato

L'immagine seguente mostra il monitoraggio delle prestazioni dopo l'implementazione della soluzione. Il carico è simile a quello illustrato in precedenza, ma i tempi di risposta per il controller `UserProfile` sono ora molto più veloci. Il volume di richieste è aumentato nella stessa durata da 2.759 a 23.565.

![Riquadro delle transazioni business di AppDynamics che mostra gli effetti dei tempi di risposta di tutte le richieste quando viene usato il controller WorkInBackground][AppDynamics-Transactions-Background-Requests]

Si noti che il controller `WorkInBackground` ha inoltre gestito un volume di richieste molto più ampio. Tuttavia non è possibile eseguire un confronto diretto in questo caso, poiché il lavoro in esecuzione in questo controller è molto diverso dal codice originale. La nuova versione accoda semplicemente una richiesta, anziché eseguire un calcolo che richiede molto tempo. Il punto principale è che questo metodo non trascina più l'intero sistema sotto carico.

Anche l'utilizzo della CPU e della rete indica prestazioni migliori. L'utilizzo della CPU non ha mai raggiunto il 100% e il volume delle richieste di rete gestite è notevolmente maggiore del precedente e non si attenua finché il carico di lavoro non diminuisce.

![Metriche di AppDynamics che mostrano l'utilizzo della CPU e della rete per il controller WorkInBackground][AppDynamics-Metrics-Background-Requests]

Il grafo seguente mostra i risultati di un test di carico. Il volume complessivo di richieste elaborate risulta notevolmente migliore rispetto ai test precedenti.

![Risultati dei test di carico per il controller BackgroundImageProcessing][Load-Test-Results-Background]

## <a name="related-guidance"></a>Informazioni correlate

- [Autoscaling best practices][autoscaling] (Procedure consigliate per la scalabilità automatica).
- [Background jobs best practices][background-jobs] (Procedure consigliate per i processi in background)
- [Schema di livellamento del carico basato sulle code][load-leveling]
- [Stile di architettura Web/coda/ruolo di lavoro][web-queue-worker]

[AppDyanamics]: https://www.appdynamics.com/
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[background-jobs]: /azure/architecture/best-practices/background-jobs
[code-sample]: https://github.com/mspnp/performance-optimization/tree/master/BusyFrontEnd
[fullDemonstrationOfSolution]: https://github.com/mspnp/performance-optimization/tree/master/BusyFrontEnd
[load-leveling]: /azure/architecture/patterns/queue-based-load-leveling
[sync-io]: ../synchronous-io/index.md
[web-queue-worker]: /azure/architecture/guide/architecture-styles/web-queue-worker

[WebJobs]: https://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
[ComputePartitioning]: https://msdn.microsoft.com/library/dn589773.aspx
[ServiceBusQueues]: https://msdn.microsoft.com/library/azure/hh367516.aspx
[AppDynamics-Transactions-Front-End-Requests]: ./_images/AppDynamicsPerformanceStats.jpg
[AppDynamics-Metrics-Front-End-Requests]: ./_images/AppDynamicsFrontEndMetrics.jpg
[Initial-Load-Test-Results-Front-End]: ./_images/InitialLoadTestResultsFrontEnd.jpg
[AppDynamics-Transactions-Background-Requests]: ./_images/AppDynamicsBackgroundPerformanceStats.jpg
[AppDynamics-Metrics-Background-Requests]: ./_images/AppDynamicsBackgroundMetrics.jpg
[Load-Test-Results-Background]: ./_images/LoadTestResultsBackground.jpg
