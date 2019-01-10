---
title: Modello di consolidamento delle risorse di calcolo
titleSuffix: Cloud Design Patterns
description: Consolidare più attività o operazioni in un'unica unità di calcolo.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.custom: seodec18
ms.openlocfilehash: 0f787537fb97f52ad69df7f0784b7fca3c45d7d1
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111479"
---
# <a name="compute-resource-consolidation-pattern"></a>Modello di consolidamento delle risorse di calcolo

[!INCLUDE [header](../_includes/header.md)]

Consolidare più attività o operazioni in un'unica unità di calcolo. Ciò può aumentare l'uso delle risorse di calcolo e ridurre il sovraccarico di gestione e i costi associati all'esecuzione di elaborazioni di calcolo in applicazioni ospitate nel cloud.

## <a name="context-and-problem"></a>Contesto e problema

Un'applicazione cloud spesso implementa una serie di operazioni. In alcune soluzioni è opportuno seguire il principio di progettazione di separazione iniziale delle problematiche e dividere queste operazioni in unità di calcolo separate ospitate e distribuite singolarmente come ad esempio app Web del servizio app separate, macchine virtuali separate o ruoli del servizio Cloud separati. Tuttavia, sebbene questa strategia consente di semplificare la progettazione logica della soluzione, la distribuzione di un numero elevato di unità di calcolo come parte della stessa applicazione può aumentare i costi di host di runtime e rendere più complessa la gestione del sistema.

La figura mostra un esempio di struttura semplificata di una soluzione ospitata dal cloud implementata tramite più unità di calcolo. Ogni unità di calcolo viene eseguita nel proprio ambiente virtuale. Ogni funzione è stata implementata come attività separata, denominata attività da A a E, in esecuzione in una specifica unità di calcolo.

![Esecuzione di attività in un ambiente cloud tramite un set di unità di calcolo dedicate](./_images/compute-resource-consolidation-diagram.png)

Ogni unità di calcolo usa risorse addebitabili, anche quando è inattiva o poco usata. Pertanto, questa non è sempre la soluzione più conveniente.

In Azure, questo problema si applica ai ruoli in un Servizio Cloud, ai Servizi app e alle macchine virtuali. Questi elementi vengono eseguiti nel proprio ambiente virtuale. L'esecuzione di una raccolta di ruoli separati, di siti Web o di macchine virtuali progettati per eseguire una serie di operazioni ben definite, ma che devono comunicare e cooperare come parte di una singola soluzione, può rappresentare un uso inefficiente delle risorse.

## <a name="solution"></a>Soluzione

Per ridurre i costi, aumentare l'uso, migliorare la velocità di comunicazione e ridurre la gestione è possibile consolidare più attività o operazioni in una singola unità di calcolo.

Le attività possono essere raggruppate secondo criteri basati sulle funzionalità fornite dall'ambiente e ai costi ad esse associati. Un approccio comune consiste nel cercare attività con un profilo simile per quanto riguarda i requisiti di elaborazione, durata e scalabilità. Raggruppando insieme queste funzioni è possibile scalarle come un'unità. L'elasticità fornita da molti ambienti cloud consente di avviare e arrestare istanze aggiuntive di un'unità di calcolo in base al carico di lavoro. Azure offre ad esempio la scalabilità automatica che è possibile applicare ai ruoli in un servizio Cloud, ai Servizi app e alle macchine virtuali. Per altre informazioni, vedere [Indicazioni sulla scalabilità automatica](https://msdn.microsoft.com/library/dn589774.aspx).

Come esempio di contatore per illustrare come usare la scalabilità per determinare quali operazioni non devono essere raggruppate, considerare le due attività seguenti:

- L'attività 1 esegue il poll per messaggi poco frequenti indipendenti dall'orario inviati a una coda.
- L'attività 2 consente di gestire picchi di volumi elevati di traffico di rete.

La seconda attività richiede l'elasticità che può implicare l'avvio e arresto di un numero elevato di istanze dell'unità di calcolo. Applicare la stessa scalabilità alla prima attività semplicemente darà origine a più attività in attesa di messaggi non frequenti nella stessa coda e rappresenta uno spreco di risorse.

In molti ambienti cloud è possibile specificare le risorse disponibili per un'unità di calcolo in termini di numero di core CPU, memoria, spazio su disco e così via. In genere, più sono le risorse specificate, maggiore è il costo. Per risparmiare, è importante ottimizzare il lavoro eseguito da un'unità di calcolo costosa e non lasciarla inattiva per un lungo periodo.

Se sono presenti attività che richiedono molta potenza della CPU in picchi brevi, provare a consolidare queste attività in un'unica unità di calcolo che fornisca la potenza necessaria. Tuttavia, è importante equilibrare la necessità di mantenere occupate le risorse costose anche in base al conflitto che si potrebbe verificare se vengono sovraccaricate. Attività con esecuzione prolungata ad elevato utilizzo di calcolo, ad esempio, non dovrebbero condividere la stessa unità di calcolo.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di implementare questo modello, considerare quanto segue:

**Scalabilità ed elasticità**. Molte soluzioni cloud implementano la scalabilità e l'elasticità al livello dell'unità di calcolo avviando e arrestando istanze di unità. Evitare di raggruppare attività che hanno requisiti di scalabilità in conflitto nella stessa unità di calcolo.

**Durata**. L'infrastruttura cloud ricicla periodicamente l'ambiente virtuale che ospita un'unità di calcolo. Quando sono presenti molte attività a esecuzione prolungata all'interno di un'unità di calcolo, potrebbe essere necessario configurare l'unità per impedire che venga riciclata fino al completamento queste attività. In alternativa, è possibile progettare le attività usando un approccio checkpoint che consente loro di arrestarsi normalmente e continuare dal punto di interruzione quando l'unità di calcolo viene riavviata.

**Versioni**. Se l'implementazione o la configurazione di un'attività viene modificata di frequente, potrebbe essere necessario arrestare l'unità di calcolo che ospita il codice aggiornato, riconfigurare e ridistribuire l'unità e quindi riavviarla. Questo processo richiederà inoltre che tutte le altre attività all'interno della stessa unità di calcolo siano arrestate, ridistribuite e riavviate.

**Sicurezza**. Le attività nella stessa unità di calcolo possono condividere lo stesso contesto di protezione ed essere in grado di accedere alle stesse risorse. Deve essere presente un elevato livello di attendibilità tra le attività e la sicurezza che un'attività non danneggia o influisce negativamente su un'altra. Inoltre, aumentando il numero di attività in esecuzione in un'unità di calcolo si aumenta la superficie di attacco dell'unità. Ciascuna attività è protetta nello stesso modo di una particolarmente vulnerabile.

**Tolleranza di errore**. Se un'attività in un'unità di calcolo non riesce o si comporta in modo anomalo, può influire sulle altre attività in esecuzione nella stessa unità. Se ad esempio un'attività non viene avviata correttamente, può causare un errore di tutta la logica di avvio dell'unità di calcolo e impedire l'esecuzione di altre attività nella stessa unità.

**Contesa**. Evitare di introdurre contese tra le attività che si contendono le risorse nella stessa unità di calcolo. In teoria, le attività che condividono la stessa unità di calcolo devono presentare caratteristiche di uso diverso delle risorse. Ad esempio, due attività a elevato utilizzo di calcolo probabilmente non dovrebbero trovarsi nella stessa unità di calcolo, così come due attività che usano grandi quantità di memoria. Tuttavia, la combinazione di un'attività ad elevato utilizzo di calcolo con un'attività che richiede una grande quantità di memoria è una combinazione possibile.

> [!NOTE]
> Prendere in considerazione il consolidamento delle risorse di calcolo solo per un sistema che sia stato in produzione per un periodo di tempo, in modo che gli operatori e gli sviluppatori possono monitorarlo e creare una _mappa termica_ che identifichi come ogni attività usa risorse diverse. Questa mappa consente di determinare quali attività sono candidati validi per la condivisione delle risorse di calcolo.

**Complessità**. La combinazione di più attività in una singola unità di calcolo aggiunge complessità al codice dell'unità, rendendolo più difficile da testare, eseguire il debug e gestire.

**Architettura logica stabile**. Progettare e implementare il codice in ogni attività in modo che non siano necessarie modifiche, anche se viene modificato l'ambiente fisico che in cui viene eseguita l'attività.

**Altre strategie**. Il consolidamento delle risorse di calcolo è solo un modo per ridurre i costi associati all'esecuzione di più attività contemporaneamente. Richiede un'attenta pianificazione e monitoraggio per verificare che continui a essere un approccio efficace. Altre strategie potrebbero essere più appropriate, a seconda della natura delle attività e a dove si trovano gli utenti che eseguono queste attività. La scomposizione funzionale del carico di lavoro, ad esempio, (come descritto in [Compute Partitioning Guidance](https://msdn.microsoft.com/library/dn589773.aspx) (Indicazioni per il partizionamento dei servizi di calcolo)) potrebbe essere un'opzione migliore.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello per attività che non sono convenienti se eseguite nelle rispettive unità di calcolo. Se un'attività è inattiva per molto tempo, l'esecuzione dell'attività in un'unità dedicata può essere costosa.

Questo modello potrebbe non essere adatto per le attività che eseguono operazioni a tolleranza di errore critica o attività che elaborano dati altamente sensibili o riservati e richiedono un proprio contesto di protezione. Queste attività devono essere eseguite nel proprio ambiente isolato, in un'unità di calcolo separata.

## <a name="example"></a>Esempio

Quando si compila un servizio cloud in Azure, è possibile consolidare l'elaborazione eseguita da più attività in un unico ruolo. In genere si tratta di un ruolo di lavoro che esegue attività di elaborazione asincrona o in background.

> In alcuni casi è possibile includere attività di elaborazione asincrona o in background nel ruolo Web. Questa tecnica consente di ridurre i costi e semplificare la distribuzione, anche se può ridurre la scalabilità e la velocità di risposta dell'interfaccia pubblica disponibile per il ruolo Web.

Il ruolo è responsabile di avviare e arrestare le attività. Quando il controller di infrastruttura di Azure carica un ruolo, viene generato l'evento `Start` per il ruolo. È possibile eseguire l'override del metodo `OnStart` della classe `WebRole` o `WorkerRole` per gestire questo evento, ad esempio per inizializzare i dati e altre risorse da cui dipendono le attività di questo metodo.

Quando il metodo `OnStart` viene completato, il ruolo può iniziare a rispondere alle richieste. È possibile trovare altre informazioni e istruzioni sull'uso dei metodi `OnStart` e `Run` in un ruolo nella sezione [Application Startup Processes](https://msdn.microsoft.com/library/ff803371.aspx#sec16) (Processi di avvio dell'applicazione) nel manuale dei modelli e procedure [Moving Applications to the Cloud](https://msdn.microsoft.com/library/ff728592.aspx) (Spostamento di applicazioni nel Cloud).

> Mantenere il codice nel metodo `OnStart` il più conciso possibile. Azure non impone alcun limite per il tempo impiegato per completare questo metodo, ma il ruolo non sarà in grado di rispondere alle richieste di rete inviate fino al completamento del metodo.

Quando il metodo `OnStart` ha terminato, il ruolo esegue il metodo `Run`. A questo punto il controller di infrastruttura può iniziare a inviare le richieste al ruolo.

Inserire il codice che crea effettivamente le attività nel metodo `Run`. Si noti che il metodo `Run` definisce la durata dell'istanza del ruolo. Quando questo metodo viene completato, il controller di infrastruttura arresterà il ruolo.

Quando un ruolo viene arrestato o riciclato, il controller di infrastruttura blocca la ricezione di altre richieste in ingresso dal servizio di bilanciamento del carico e genera l'evento `Stop`. È possibile acquisire questo evento eseguendo l'override del metodo `OnStop` del ruolo ed eseguendo qualsiasi operazione di riordino necessaria prima che il ruolo termini.

> Eventuali azioni eseguite nel metodo `OnStop` devono essere completate entro cinque minuti (o 30 secondi se si usa l'emulatore di Azure in un computer locale). In caso contrario il controller di infrastruttura di Azure presuppone che il ruolo sia bloccato e ne forza l'arresto.

Le attività sono avviate dal metodo `Run` che attende il completamento delle attività. Le attività implementano la logica di business del servizio cloud e possono rispondere a messaggi inviati al ruolo tramite il bilanciamento del carico di Azure. La figura illustra il ciclo di vita delle attività e delle risorse in un ruolo in un servizio cloud di Azure.

![Il ciclo di vita delle attività e delle risorse in un ruolo in un servizio cloud di Azure](./_images/compute-resource-consolidation-lifecycle.png)

Il file _WorkerRole.cs_ nel progetto _ComputeResourceConsolidation.Worker_ mostra un esempio di come è possibile implementare questo modello in un servizio cloud di Azure.

> Il progetto _ComputeResourceConsolidation.Worker_ fa parte della soluzione _ComputeResourceConsolidation_ disponibile per il download da [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/compute-resource-consolidation).

I metodi `MyWorkerTask1` e `MyWorkerTask2` illustrano come eseguire attività diverse all'interno dello stesso ruolo di lavoro. Il codice seguente mostra `MyWorkerTask1`. Si tratta di un'attività semplice che resta inattiva per 30 secondi e quindi restituisce un messaggio di traccia. Questo processo viene ripetuto fino a quando l'attività viene annullata. Il codice in `MyWorkerTask2` è simile.

```csharp
// A sample worker role task.
private static async Task MyWorkerTask1(CancellationToken ct)
{
  // Fixed interval to wake up and check for work and/or do work.
  var interval = TimeSpan.FromSeconds(30);

  try
  {
    while (!ct.IsCancellationRequested)
    {
      // Wake up and do some background processing if not canceled.
      // TASK PROCESSING CODE HERE
      Trace.TraceInformation("Doing Worker Task 1 Work");

      // Go back to sleep for a period of time unless asked to cancel.
      // Task.Delay will throw an OperationCanceledException when canceled.
      await Task.Delay(interval, ct);
    }
  }
  catch (OperationCanceledException)
  {
    // Expect this exception to be thrown in normal circumstances or check
    // the cancellation token. If the role instances are shutting down, a
    // cancellation request will be signaled.
    Trace.TraceInformation("Stopping service, cancellation requested");

    // Rethrow the exception.
    throw;
  }
}
```

> Il codice di esempio illustra un'implementazione comune di un processo in background. In un'applicazione reale è possibile seguire la stessa struttura, ad eccezione del fatto che è necessario inserire la propria logica di elaborazione nel corpo del ciclo che attende la richiesta di annullamento.

Dopo che il ruolo di lavoro ha inizializzato le risorse che usa, il metodo `Run` avvia le due attività contemporaneamente, come illustrato di seguito.

```csharp
/// <summary>
/// The cancellation token source use to cooperatively cancel running tasks
/// </summary>
private readonly CancellationTokenSource cts = new CancellationTokenSource();

/// <summary>
/// List of running tasks on the role instance
/// </summary>
private readonly List<Task> tasks = new List<Task>();

// RoleEntry Run() is called after OnStart().
// Returning from Run() will cause a role instance to recycle.
public override void Run()
{
  // Start worker tasks and add to the task list
  tasks.Add(MyWorkerTask1(cts.Token));
  tasks.Add(MyWorkerTask2(cts.Token));

  foreach (var worker in this.workerTasks)
  {
      this.tasks.Add(worker);
  }

  Trace.TraceInformation("Worker host tasks started");
  // The assumption is that all tasks should remain running and not return,
  // similar to role entry Run() behavior.
  try
  {
    Task.WaitAll(tasks.ToArray());
  }
  catch (AggregateException ex)
  {
    Trace.TraceError(ex.Message);

    // If any of the inner exceptions in the aggregate exception
    // are not cancellation exceptions then re-throw the exception.
    ex.Handle(innerEx => (innerEx is OperationCanceledException));
  }

  // If there wasn't a cancellation request, stop all tasks and return from Run()
  // An alternative to canceling and returning when a task exits would be to
  // restart the task.
  if (!cts.IsCancellationRequested)
  {
    Trace.TraceInformation("Task returned without cancellation request");
    Stop(TimeSpan.FromMinutes(5));
  }
}
...
```

In questo esempio, il metodo `Run` attende il completamento delle attività. Se un'attività viene annullata, il metodo `Run` presuppone che il ruolo viene arrestato e attende che le attività rimanenti vengano annullate prima di terminare (resta in attesa per un massimo di cinque minuti prima della chiusura). Se un'attività ha esito negativo a causa di un'eccezione prevista, il metodo `Run` annulla l'attività.

> È possibile implementare un monitoraggio e strategie di gestione delle eccezioni più completi nel metodo `Run` come ad esempio il riavvio di attività non riuscite o includere codice che abilita il ruolo ad arrestare e avviare le singole attività.

Il metodo `Stop` illustrato nel codice seguente viene chiamato quando il controller di infrastruttura arresta l'istanza del ruolo (viene richiamato dal metodo `OnStop`). Il codice interrompe ogni attività annullandola. Se qualsiasi attività richiede più di cinque minuti per il completamento, l'operazione di annullamento nel metodo `Stop` smette di attendere e il ruolo viene terminato.

```csharp
// Stop running tasks and wait for tasks to complete before returning
// unless the timeout expires.
private void Stop(TimeSpan timeout)
{
  Trace.TraceInformation("Stop called. Canceling tasks.");
  // Cancel running tasks.
  cts.Cancel();

  Trace.TraceInformation("Waiting for canceled tasks to finish and return");

  // Wait for all the tasks to complete before returning. Note that the
  // emulator currently allows 30 seconds and Azure allows five
  // minutes for processing to complete.
  try
  {
    Task.WaitAll(tasks.ToArray(), timeout);
  }
  catch (AggregateException ex)
  {
    Trace.TraceError(ex.Message);

    // If any of the inner exceptions in the aggregate exception
    // are not cancellation exceptions then rethrow the exception.
    ex.Handle(innerEx => (innerEx is OperationCanceledException));
  }
}
```

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

- [Scalabilità automatica](https://msdn.microsoft.com/library/dn589774.aspx). La scalabilità automatica consente di avviare e arrestare le istanze del servizio che ospita le risorse di calcolo, a seconda delle necessità di elaborazione previste.

- [Compute Partitioning Guidance](https://msdn.microsoft.com/library/dn589773.aspx) (Indicazioni per il partizionamento dei servizi di calcolo) Descrive come allocare i servizi e i componenti in un servizio cloud in modo da ridurre al minimo i costi operativi mantenendo la scalabilità, le prestazioni, la disponibilità e la sicurezza del servizio.

- Questo modello include un'[applicazione di esempio](https://github.com/mspnp/cloud-design-patterns/tree/master/compute-resource-consolidation) scaricabile.
