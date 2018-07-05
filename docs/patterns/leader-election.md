---
title: Designazione leader
description: Coordinare le azioni eseguite da una raccolta di istanze di attività di collaborazione in un'applicazione distribuita designando un'istanza come leader, con la responsabilità di gestire le altre istanze.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- design-implementation
- resiliency
ms.openlocfilehash: 8c8efa0846550557bb53ea81f85ac0e303a77b19
ms.sourcegitcommit: f19314f18cd794ebe380fa722ca92066b8735b56
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37348270"
---
# <a name="leader-election-pattern"></a>Modello Designazione leader

[!INCLUDE [header](../_includes/header.md)]

Coordinare le azioni eseguite da una raccolta di istanze di collaborazione in un'applicazione distribuita designando un'istanza come leader, con la responsabilità di gestire le altre, consente di garantire che le istanze non siano in conflitto tra loro, non provochino una contesa per le risorse condivise o non interferiscano inavvertitamente con il lavoro di altre istanze.

## <a name="context-and-problem"></a>Contesto e problema

Un'applicazione cloud tipica include molte attività che agiscono in modo coordinato. Queste attività potrebbero essere tutte istanze che eseguono lo stesso codice e che richiedono l'accesso alle stesse risorse o potrebbero collaborare in parallelo per eseguire le singole parti di un calcolo complesso.

Le istanze delle attività potrebbero essere eseguite separatamente per la maggior parte del tempo, ma potrebbe anche essere necessario coordinare le azioni di ogni istanza per garantire che non entrino in conflitto, non provochino una contesa per le risorse condivise o non interferiscano inavvertitamente con il lavoro di altre istanze delle attività.

Ad esempio: 

- In un sistema basato su cloud che implementa la scalabilità orizzontale, più istanze della stessa attività potrebbero essere in esecuzione nello stesso momento con ogni istanza che serve un utente diverso. Se queste istanze scrivono in una risorsa condivisa, è necessario coordinarne le azioni per impedire che le diverse istanze sovrascrivano le modifiche apportate dalle altre.
- Se le attività eseguono singoli elementi di un calcolo complesso in parallelo, i risultati devono essere aggregati quando sono tutte completate.

Le istanze delle attività sono tutte pari, pertanto non c'è un leader naturale che possa agire da coordinatore o da aggregatore.

## <a name="solution"></a>Soluzione

Una singola istanza dell'attività deve essere designata per fungere da leader e questa istanza deve coordinare le azioni delle altre istanze delle attività subordinate. Se tutte le istanze delle attività eseguono lo stesso codice, ognuna di esse sarà in grado di agire come leader. Pertanto, il processo di designazione deve essere gestito attentamente per evitare che due o più istanze assumano il ruolo di leader nello stesso momento.

Il sistema deve fornire un meccanismo efficiente per la scelta del leader. Questo metodo deve essere in grado di gestire eventi come interruzioni di rete o errori dei processi. In molte soluzioni le istanze delle attività subordinate monitorano il leader usando un metodo heartbeat o tramite polling. Se il leader designato termina in modo imprevisto o un errore di rete lo rende non disponibile per le istanze delle attività subordinate, è necessario poter designare un nuovo leader.

Sono disponibili diverse strategie per designare un leader tra un set di attività in un ambiente distribuito, tra cui:
- Selezione dell'istanza dell'attività con l'ID di processo o di istanza con la classificazione più bassa.
- Competizione per acquisire un mutex condiviso e distribuito. La prima istanza dell'attività che acquisisce il mutex è il leader. Tuttavia, il sistema deve garantire che, se il leader termina o viene disconnesso dal resto del sistema, il mutex viene rilasciato per consentire a un'altra istanza dell'attività di diventare leader.
- Implementazione di uno degli algoritmi di designazione leader comuni, ad esempio l'[algoritmo Bully](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/BullyExample.html) o l'[algoritmo Ring](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/RingElectExample.html). Questi algoritmi presuppongono che ciascun candidato alla designazione abbia un ID univoco e sia in grado di comunicare con altri candidati in modo affidabile.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo modello, considerare quanto segue:
- Il processo di designazione di un leader deve essere resiliente agli errori temporanei e permanenti.
- Deve essere possibile rilevare quando il leader ha riscontrato un errore o è diventato non disponibile, ad esempio a causa di un problema di comunicazione. La rapidità con cui è necessario il rilevamento dipende dal sistema. Alcuni sistemi potrebbero essere in grado di funzionare in assenza di un leader per un breve periodo, durante il quale un errore temporaneo potrebbe essere corretto. In altri casi potrebbe essere necessario rilevare immediatamente l'errore del leader e attivare una nuova designazione.
- In un sistema che implementa il ridimensionamento orizzontale il leader potrebbe essere terminato se il sistema riduce il numero di alcune delle risorse di calcolo o le arresta.
- L'uso di un mutex distribuito condiviso introduce una dipendenza dal servizio esterno che fornisce il mutex. Il servizio costituisce un singolo punto di guasto. Se per qualsiasi motivo non risulta disponibile, il sistema non sarà in grado di designare un leader.
- L'uso di un singolo processo dedicato come leader è un approccio molto semplice. Tuttavia, se il processo non riesce potrebbe verificarsi un notevole ritardo durante il riavvio. La latenza risultante può influire sulle prestazioni e sui tempi di risposta di altri processi se sono in attesa del leader per il coordinamento di un'operazione.
- L'implementazione manuale di uno degli algoritmi di designazione leader assicura la massima flessibilità per l'ottimizzazione del codice.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello quando le attività in un'applicazione distribuita, ad esempio una soluzione ospitata nel cloud, necessitano di un attento coordinamento e non è presente alcun leader naturale.

>  Evitare di fare del leader un collo di bottiglia nel sistema. L'obiettivo del leader consiste nel coordinare il lavoro delle attività subordinate; non deve necessariamente partecipare al lavoro, anche se deve essere in grado di farlo se l'attività non viene designata come leader.

Questo modello potrebbe non essere utile se:
- È presente un leader naturale o un processo dedicato che può sempre fungere da leader. Ad esempio, potrebbe essere possibile implementare un processo singleton che coordina le istanze delle attività. Se questo processo non riesce o risulta non integro, il sistema può arrestarlo e riavviarlo.
- Il coordinamento tra le attività può essere ottenuto usando un metodo più semplice. Ad esempio, se più istanze delle attività necessitano semplicemente dell'accesso coordinato a una risorsa condivisa, una soluzione migliore consiste nell'usare il blocco ottimistico o pessimistico per controllare l'accesso.
- Una soluzione di terze parti è più appropriata. Ad esempio, il servizio Microsoft Azure HDInsight (basato su Apache Hadoop) usa i servizi forniti da Apache Zookeeper per coordinare la mappa e ridurre le attività di raccolta e riepilogo dei dati.

## <a name="example"></a>Esempio

Il progetto DistributedMutex nella soluzione LeaderElection (un esempio che illustra questo modello è disponibile in [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/leader-election)) mostra come usare un lease in un BLOB del servizio di archiviazione di Azure per fornire un meccanismo per l'implementazione di un mutex distribuito condiviso. Questo mutex può essere usato per designare un leader in un gruppo di istanze del ruolo in un servizio cloud di Azure. La prima istanza del ruolo ad acquisire il lease viene designata come leader e rimane tale finché non rilascia il lease o non è in grado di rinnovarlo. Altre istanze del ruolo possono continuare a monitorare il lease del BLOB, nel caso in cui il leader non sia più disponibile.

>  Un lease del BLOB è un blocco di scrittura esclusivo su un BLOB. Un singolo BLOB può essere oggetto di un solo lease in qualsiasi punto nel tempo. Un'istanza del ruolo può richiedere un lease per un BLOB specifico e il lease verrà concesso se nessun'altra istanza del ruolo contiene un lease per lo stesso BLOB. In caso contrario, la richiesta genererà un'eccezione.
> 
> Per evitare che un'istanza del ruolo con errori mantenga il lease per un periodo illimitato, specificare una durata per il lease. Alla scadenza, il lease diventerà disponibile. Tuttavia, mentre un'istanza del ruolo contiene il lease può richiedere che il lease venga rinnovato e il lease verrà concesso per un ulteriore periodo di tempo. L'istanza del ruolo può ripetere continuamente questo processo se vuole mantenere il lease.
> Per altre informazioni sul lease di un BLOB, vedere [Lease Blob (REST API)](https://msdn.microsoft.com/library/azure/ee691972.aspx) (Lease Blob (API REST)).

La classe `BlobDistributedMutex` nell'esempio di C# seguente contiene il metodo `RunTaskWhenMutexAquired`, che consente a un'istanza del ruolo di tentare di acquisire un lease per un BLOB specificato. I dettagli del BLOB (nome, contenitore e account di archiviazione) vengono passati al costruttore in un oggetto `BlobSettings` quando viene creato l'oggetto `BlobDistributedMutex` (questo oggetto è uno struct semplice incluso nel codice di esempio). Il costruttore accetta inoltre un `Task` che fa riferimento al codice che l'istanza del ruolo dovrebbe eseguire se acquisisce correttamente il lease per il BLOB e viene designato come leader. Si noti che il codice che gestisce i dettagli di basso livello dell'acquisizione del lease viene implementato in una classe helper distinta denominata `BlobLeaseManager`.

```csharp
public class BlobDistributedMutex
{
  ...
  private readonly BlobSettings blobSettings;
  private readonly Func<CancellationToken, Task> taskToRunWhenLeaseAcquired;
  ...

  public BlobDistributedMutex(BlobSettings blobSettings,
           Func<CancellationToken, Task> taskToRunWhenLeaseAquired)
  {
    this.blobSettings = blobSettings;
    this.taskToRunWhenLeaseAquired = taskToRunWhenLeaseAquired;
  }

  public async Task RunTaskWhenMutexAcquired(CancellationToken token)
  {
    var leaseManager = new BlobLeaseManager(blobSettings);
    await this.RunTaskWhenBlobLeaseAcquired(leaseManager, token);
  }
  ...
```

Il metodo `RunTaskWhenMutexAquired` nell'esempio di codice precedente richiama il metodo `RunTaskWhenBlobLeaseAcquired` illustrato nell'esempio di codice seguente per acquisire effettivamente il lease. Il metodo `RunTaskWhenBlobLeaseAcquired` viene eseguito in modo asincrono. Se il lease viene acquisito correttamente, l'istanza del ruolo è stata designata come leader. Lo scopo del delegato `taskToRunWhenLeaseAcquired` consiste nell'eseguire il lavoro che coordina le altre istanze del ruolo. Se il lease non viene acquisito, un'altra istanza del ruolo è stata designata come leader e l'istanza del ruolo corrente rimane subordinata. Si noti che il metodo `TryAcquireLeaseOrWait` è un metodo helper che usa l'oggetto `BlobLeaseManager` per acquisire il lease.

```csharp
  private async Task RunTaskWhenBlobLeaseAcquired(
    BlobLeaseManager leaseManager, CancellationToken token)
  {
    while (!token.IsCancellationRequested)
    {
      // Try to acquire the blob lease.
      // Otherwise wait for a short time before trying again.
      string leaseId = await this.TryAquireLeaseOrWait(leaseManager, token);

      if (!string.IsNullOrEmpty(leaseId))
      {
        // Create a new linked cancellation token source so that if either the
        // original token is canceled or the lease can't be renewed, the
        // leader task can be canceled.
        using (var leaseCts =
          CancellationTokenSource.CreateLinkedTokenSource(new[] { token }))
        {
          // Run the leader task.
          var leaderTask = this.taskToRunWhenLeaseAquired.Invoke(leaseCts.Token);
          ...
        }
      }
    }
    ...
  }
```

Anche l'attività avviata dal leader viene eseguita in modo asincrono. Durante l'esecuzione dell'attività, il metodo `RunTaskWhenBlobLeaseAquired` illustrato nell'esempio di codice seguente tenta periodicamente di rinnovare il lease. Ciò consente di garantire che l'istanza del ruolo rimanga il leader. Nella soluzione di esempio il ritardo tra le richieste di rinnovo è inferiore al tempo specificato per la durata del lease, in modo da impedire che un'altra istanza del ruolo venga designata come leader. Se per qualsiasi motivo il rinnovo non riesce, l'attività viene annullata.

Se il lease non viene rinnovato o l'attività viene annullata, ad esempio in seguito all'arresto dell'istanza del ruolo, il lease viene rilasciato. A questo punto, questa o un'altra istanza del ruolo può essere designata come leader. L'estratto di codice seguente illustra questa parte del processo.

```csharp
  private async Task RunTaskWhenBlobLeaseAcquired(
    BlobLeaseManager leaseManager, CancellationToken token)
  {
    while (...)
    {
      ...
      if (...)
      {
        ...
        using (var leaseCts = ...)
        {
          ...
          // Keep renewing the lease in regular intervals.
          // If the lease can't be renewed, then the task completes.
          var renewLeaseTask =
            this.KeepRenewingLease(leaseManager, leaseId, leaseCts.Token);

          // When any task completes (either the leader task itself or when it
          // couldn't renew the lease) then cancel the other task.
          await CancelAllWhenAnyCompletes(leaderTask, renewLeaseTask, leaseCts);
        }
      }
    }
  }
  ...
}
```

Il metodo `KeepRenewingLease` è un altro metodo helper che usa l'oggetto `BlobLeaseManager` per rinnovare il lease. Il metodo `CancelAllWhenAnyCompletes` annulla le attività specificate come i primi due parametri. Il diagramma seguente illustra l'uso della classe `BlobDistributedMutex` per la designazione di un leader e l'esecuzione di un'attività che coordina le operazioni.

![La figura 1 mostra le funzioni della classe BlobDistributedMutex](./_images/leader-election-diagram.png)


L'esempio di codice seguente illustra come usare la classe `BlobDistributedMutex` in un ruolo di lavoro. Questo codice acquisisce un lease per un BLOB denominato `MyLeaderCoordinatorTask` nel contenitore del lease nell'archivio di sviluppo e specifica che il codice definito nel metodo `MyLeaderCoordinatorTask` sia eseguito se l'istanza del ruolo viene designata come leader.

```csharp
var settings = new BlobSettings(CloudStorageAccount.DevelopmentStorageAccount,
  "leases", "MyLeaderCoordinatorTask");
var cts = new CancellationTokenSource();
var mutex = new BlobDistributedMutex(settings, MyLeaderCoordinatorTask);
mutex.RunTaskWhenMutexAcquired(this.cts.Token);
...

// Method that runs if the role instance is elected the leader
private static async Task MyLeaderCoordinatorTask(CancellationToken token)
{
  ...
}
```

Tenere presente i punti seguenti riguardo alla soluzione di esempio:
- Il BLOB è un singolo punto di guasto potenziale. Se il servizio BLOB non è più disponibile o è inaccessibile, il leader non sarà in grado di rinnovare il lease e nessun'altra istanza del ruolo potrà acquisire il lease. In questo caso, nessuna istanza del ruolo sarà in grado di fungere da leader. Tuttavia, il servizio BLOB è progettato per essere resiliente, pertanto un guasto completo del servizio BLOB è considerato estremamente improbabile.
- Se l'attività eseguita dal leader è in fase di stallo, il leader potrebbe continuare a rinnovare il lease, impedendo a qualsiasi altra istanza del ruolo di acquisire il lease e assumere il ruolo di leader per coordinare le attività. Nel mondo reale è necessario verificare l'integrità del leader a intervalli frequenti.
- Il processo di designazione è non deterministico. Non è possibile fare supposizioni su quale istanza del ruolo acquisirà il lease del BLOB e diventerà leader.
- Il BLOB usato come destinazione del lease del BLOB non dovrebbe essere usato per altri scopi. Se un'istanza del ruolo tenta di archiviare i dati in questo BLOB, questi dati non saranno accessibili a meno che l'istanza del ruolo non sia il leader e non contenga il lease del BLOB.

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Per l'implementazione di questo modello possono risultare utili le informazioni aggiuntive seguenti:
- Questo modello ha un'[applicazione di esempio](https://github.com/mspnp/cloud-design-patterns/tree/master/leader-election) scaricabile.
- [Scalabilità automatica](https://msdn.microsoft.com/library/dn589774.aspx). È possibile avviare e arrestare le istanze degli host delle attività di pari passo con la variazione del carico sull'applicazione. La scalabilità automatica consente di mantenere la velocità effettiva e le prestazioni durante i periodi di massima richiesta di elaborazione.
- [Indicazioni sul partizionamento del calcolo](https://msdn.microsoft.com/library/dn589773.aspx). Queste indicazioni descrivono come allocare attività agli host in un servizio cloud, in modo da ridurre al minimo i costi operativi mantenendo la scalabilità, le prestazioni, la disponibilità e la sicurezza del servizio.
- [Modello asincrono basato su attività](https://msdn.microsoft.com/library/hh873175.aspx).
- Esempio che illustra l'[algoritmo Bully](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/BullyExample.html).
- Esempio che illustra l'[algoritmo Ring](http://www.cs.colostate.edu/~cs551/CourseNotes/Synchronization/RingElectExample.html).
- [Apache Curator](http://curator.apache.org/), una libreria client per Apache ZooKeeper.
- Articolo [Lease Blob (REST API)](https://msdn.microsoft.com/library/azure/ee691972.aspx) (Lease Blob (API REST)) su MSDN.
