---
title: Interruttore
description: "Gestire gli errori la cui correzione potrebbe richiedere una quantità variabile di tempo in fase di connessione a una risorsa o a un servizio remoto."
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories: resiliency
ms.openlocfilehash: ce110d0bbda600575d328895f2feca5aa253479d
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="circuit-breaker-pattern"></a>Modello a interruttore

Gestire gli errori il cui ripristino potrebbe richiedere una quantità variabile di tempo in fase di connessione a una risorsa o a un servizio remoto. Questo può migliorare la stabilità e la resilienza di un'applicazione.

## <a name="context-and-problem"></a>Contesto e problema

In un ambiente distribuito le chiamate alle risorse remote e ai servizi possono non riuscire a causa di errori temporanei, ad esempio connessioni di rete lente, timeout oppure indisponibilità o overcommit delle risorse. Questi errori in genere si risolvono autonomamente dopo un breve periodo di tempo. Un'applicazione cloud affidabile deve essere preparata per gestirli tramite una strategia, ad esempio un [modello di ripetizione dei tentativi][retry-pattern].

Tuttavia, possono anche verificarsi situazioni in cui gli errori sono causati da eventi imprevisti, per cui la risoluzione potrebbe richiedere molto più tempo. Questi errori possono variare, in base alla gravità, dalla perdita parziale della connettività alla totale interruzione di un servizio. In queste situazioni potrebbe essere inutile ripetere continuamente un'operazione che ha scarse possibilità di successo, mentre invece è importante che l'applicazione accetti rapidamente l'esito negativo dell'operazione e gestisca il problema in modo appropriato.

Se un servizio è molto occupato, è inoltre possibile che un errore in una parte del sistema generi errori a cascata. Un'operazione che richiama un servizio potrebbe, ad esempio, essere configurata per l'implementazione di un timeout e restituire quindi un messaggio di errore se il servizio non risponde entro l'intervallo specificato. Questa strategia potrebbe, tuttavia, causare il blocco di un elevato numero di richieste simultanee alla stessa operazione fino alla scadenza del periodo di timeout. Le richieste bloccate potrebbero tenere in sospeso risorse di sistema critiche quali la memoria, i thread, le connessioni database e così via. Di conseguenza, queste risorse potrebbero esaurirsi, causando errori di altre parti del sistema, probabilmente non correlate, che devono usare le stesse risorse. In questi casi, sarebbe preferibile che l'operazione restituisse immediatamente un esito negativo e tentasse di richiamare il servizio solo se è probabile che questo venga eseguito correttamente. L'impostazione di un timeout più breve potrebbe risolvere il problema, ma il timeout non deve essere così breve da determinare, nella maggior parte dei casi, l'esito negativo dell'operazione, anche quando la richiesta al servizio avrebbe esito positivo.

## <a name="solution"></a>Soluzione

Il modello a interruttore, reso noto da Michael Nygard nel suo libro [Release It!](https://pragprog.com/book/mnee/release-it), può impedire che un'applicazione tenti più volte di eseguire un'operazione con scarsa probabilità di riuscita, in modo da poter continuare l'esecuzione senza attendere la risoluzione dell'errore o senza sprecare cicli della CPU, mentre si stabilisce che l'errore è di lunga durata. Grazie al modello a interruttore, inoltre, un'applicazione è in grado di rilevare se l'errore è stato risolto. In questo caso, l'applicazione potrà provare a richiamare l'operazione.

> Lo scopo del modello a interruttore è diverso da quello del modello di ripetizione dei tentativi. Il criterio di ripetizione consente a un'applicazione di ripetere un'operazione quando si prevede che avrà esito positivo. Il modello a interruttore impedisce che un'applicazione tenti un'operazione che probabilmente continuerà a restituire un errore. Un'applicazione può combinare questi due modelli, usando il modello di ripetizione dei tentativi per richiamare un'operazione tramite un interruttore. Tuttavia, la logica di riesecuzione deve essere sensibile alle eventuali eccezioni restituite dall'interruttore e abbandonare i tentativi di ripetizione se l'interruttore indica che un errore non è temporaneo.

Un interruttore funge da proxy per le operazioni che potrebbero non riuscire. Il proxy deve monitorare il numero di errori recenti che si sono verificati e usare queste informazioni per decidere se consentire il proseguimento dell'operazione o semplicemente restituire subito un'eccezione.

Il proxy può essere implementato come una macchina a stati, con gli stati seguenti che simulano le funzionalità di un interruttore elettrico:

- **Chiuso**: la richiesta inviata dall'applicazione viene indirizzata all'operazione. Il proxy mantiene un conteggio del numero di errori recenti e, se la chiamata all'operazione ha esito negativo, incrementa il conteggio. Se il numero di errori recenti supera una soglia specificata in un determinato intervallo di tempo, il proxy viene impostato sullo stato **Aperto**. A questo punto, il proxy avvia un timer di timeout e viene impostato sullo stato **Semiaperto** quando il timer scade.

    > Lo scopo del timer di timeout è dare al sistema il tempo per risolvere il problema che ha causato l'errore prima di consentire all'applicazione di tentare una nuova esecuzione dell'operazione.

- **Aperto**: la richiesta inviata dall'applicazione ha un esito negativo immediato che restituisce un'eccezione all'applicazione.

- **Semiaperto**: solo un numero limitato di richieste inviate dall'applicazione può passare e richiamare l'operazione. Se queste richieste hanno esito positivo, si presuppone che l'errore che si è verificato in precedenza sia stato risolto e l'interruttore passa allo stato **Chiuso** (il contatore degli errori viene reimpostato). Se una richiesta ha esito negativo, l'interruttore presuppone che l'errore sia ancora presente e pertanto ripristina lo stato **Aperto** e riavvia il timer di timeout per concedere al sistema un ulteriore intervallo di tempo in cui correggere l'errore.

    > Lo stato **Semiaperto** è utile per impedire che un servizio di recupero venga improvvisamente sommerso di richieste. Quando un servizio viene ripristinato, potrebbe essere in grado di supportare un volume di richieste limitato finché il ripristino non sarà stato completato; tuttavia, mentre questo processo è in corso, l'invio di una grande quantità di richieste potrebbe causare un nuovo timeout o una nuova interruzione del servizio.

![Stati dell'interruttore](./_images/circuit-breaker-diagram.png)

Nella figura il contatore degli errori usato dallo stato **Chiuso** è basato sul tempo e viene reimpostato automaticamente a intervalli periodici. Questo consente di impedire che l'interruttore attivi lo stato **Aperto** in caso di guasti occasionali. La soglia di errore che attiva lo stato **Aperto** dell'interruttore viene raggiunta solo quando si verifica un determinato numero di errori durante un intervallo specificato. Il contatore usato dallo stato **Semiaperto** registra il numero di tentativi di chiamata dell'operazione con esito positivo. L'interruttore ripristina lo stato **Chiuso** dopo che un determinato numero di chiamate consecutive all'operazione ha avuto esito positivo. In caso di esito negativo di una chiamata, l'interruttore attiva immediatamente lo stato **Aperto** e il contatore delle operazioni riuscite viene reimpostato alla successiva attivazione dello stato **Semiaperto**.

> La modalità di ripristino del sistema viene gestita dall'esterno, verosimilmente mediante il ripristino o il riavvio di un componente in errore o il ripristino di una connessione di rete.

Il modello a interruttore offre stabilità mentre il sistema esegue il recupero da un errore e riduce al minimo l'impatto sulle prestazioni. Può aiutare a gestire i tempi di risposta del sistema rifiutando rapidamente una richiesta per un'operazione con scarsa probabilità di riuscita, anziché attendere il timeout o addirittura l'arresto dell'operazione. Se l'interruttore genera un evento a ogni cambiamento di stato, queste informazioni possono essere usate per monitorare l'integrità della parte del sistema protetta dall'interruttore o per avvisare un amministratore quando un interruttore attiva lo stato **Aperto**.

Il modello è personalizzabile e può essere adattato in base al tipo di errore possibile. È ad esempio possibile applicare un timer di timeout crescente per un interruttore. È possibile impostare inizialmente l'interruttore sullo stato **Aperto** per pochi secondi e quindi, se l'errore non è stato risolto, aumentare il timeout di qualche minuto e così via. In alcuni casi, anziché impostare lo stato **Aperto** in modo da restituire un errore che genera un'eccezione, potrebbe essere utile restituire un valore predefinito significativo per l'applicazione.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo schema, è opportuno considerare quanto segue:

**Gestione delle eccezioni**. Un'applicazione che richiama un'operazione tramite un interruttore deve essere pronta a gestire le eccezioni generate se l'operazione non è disponibile. La modalità di gestione delle eccezioni cambierà a seconda dell'applicazione. Le funzionalità di un'applicazione potrebbero ad esempio peggiorare temporaneamente, l'applicazione potrebbe richiamare un'operazione alternativa per provare a eseguire la stessa attività o per ottenere gli stessi dati oppure segnalare l'eccezione all'utente e chiedergli di riprovare più tardi.

**Tipi di eccezioni**. Una richiesta potrebbe non riuscire per diversi motivi, alcuni dei quali potrebbero indicare un tipo di errore più grave rispetto ad altri. Una richiesta potrebbe, ad esempio, non riuscire a causa dell'arresto di un servizio remoto e richiedere diversi minuti per completare il ripristino o a causa di un timeout dovuto all'overload temporaneo di un servizio. Un interruttore potrebbe essere in grado di esaminare i tipi di eccezioni che si verificano e adattare la propria strategia alla natura di tali eccezioni. Per attivare lo stato **Aperto** dell'interruttore, ad esempio, potrebbe essere necessario un numero maggiore di eccezioni di timeout rispetto al numero di errori dovuti alla totale indisponibilità del servizio.

**Registrazione**. Un interruttore deve registrare tutte le richieste con esito negativo (e possibilmente anche quelle con esito positivo) per consentire a un amministratore di monitorare lo stato dell'operazione.

**Recuperabilità**. È necessario configurare l'interruttore in modo che corrisponda al probabile modello di recupero dell'operazione che protegge. Se, ad esempio, l'interruttore rimane a lungo nello stato **Aperto**, potrebbe generare eccezioni anche se il motivo dell'errore è stato risolto. Analogamente, un interruttore potrebbe fluttuare e ridurre i tempi di risposta delle applicazioni se passa dallo stato **Aperto** allo stato **Semiaperto** troppo rapidamente.

**Test delle operazioni non riuscite**. Nello stato **Aperto**, anziché usare un timer per stabilire quando passare allo stato **Semiaperto**, un interruttore può eseguire periodicamente un ping alla risorsa o al servizio remoto per determinare se è nuovamente disponibile. Questo ping potrebbe assumere la forma di un tentativo di richiamo di un'operazione precedentemente non riuscita oppure potrebbe usare un'operazione speciale, specificamente fornita dal servizio remoto per il test dell'integrità del servizio, come descritto dal [modello di monitoraggio degli endpoint di integrità](health-endpoint-monitoring.md).

**Sostituzione manuale**. In un sistema in cui il tempo per il recupero di un'operazione in errore è estremamente variabile, può essere utile offrire un'opzione di ripristino manuale che consenta a un amministratore di chiudere un interruttore e reimpostare il contatore degli errori. Analogamente, un amministratore può forzare l'attivazione dello stato **Aperto** di un interruttore e riavviare il timer di timeout se l'operazione protetta dall'interruttore è temporaneamente non disponibile.

**Concorrenza**. L'accesso all'interruttore stesso potrebbe essere eseguito da un numero elevato di istanze simultanee di un'applicazione. L'implementazione non deve bloccare le richieste simultanee o aggiungere un sovraccarico eccessivo a ogni chiamata a un'operazione.

**Differenziazione delle risorse**. Prestare attenzione quando si usa un singolo interruttore per un tipo di risorsa, se potrebbero essere presenti più provider indipendenti sottostanti. In un archivio dati che contiene più partizioni, ad esempio, una partizione potrebbe essere completamente accessibile mentre su un'altra si sta verificando un problema temporaneo. Se le risposte di errore in questi scenari vengono unite, un'applicazione potrebbe tentare di accedere ad alcune partizioni anche quando l'errore è molto probabile, mentre l'accesso ad altre partizioni potrebbe essere bloccato anche se è probabile che abbia esito positivo.

**Interruzione accelerata**. Talvolta una risposta di errore può contenere informazioni sufficienti per attivare immediatamente l'interruttore e lasciarlo attivato per una quantità minima di tempo. La risposta di errore ricevuta da una risorsa condivisa sottoposta a overload potrebbe, ad esempio, indicare che un nuovo tentativo immediato non è consigliabile e che è piuttosto preferibile provare a eseguire di nuovo l'applicazione dopo qualche minuto.

> [!NOTE]
> Un servizio può restituire l'errore HTTP 429 (numero eccessivo di richieste) se limita il client o l'errore HTTP 503 (servizio non disponibile) se il servizio non è attualmente disponibile. La risposta può includere informazioni aggiuntive, ad esempio la durata del ritardo prevista.

**Riproduzione delle richieste non riuscite**. Nello stato **Aperto**, anziché restituire subito un errore, un interruttore può anche registrare i dettagli di ogni richiesta in un giornale di registrazione e rimandare la riproduzione di queste richieste a quando la risorsa o il servizio remoto sarà disponibile.

**Timeout inappropriati in servizi esterni**. Un interruttore potrebbe non essere in grado di proteggere completamente le applicazioni dalle operazioni con esito negativo in servizi esterni configurati con un periodo di timeout di lunga durata. Se il timeout è troppo lungo, un thread che esegue un interruttore potrebbe essere bloccato per un periodo prolungato prima che l'interruttore indichi che l'operazione non è riuscita. In questo periodo di tempo, anche molte altre istanze di applicazioni potrebbero provare a richiamare il servizio tramite l'interruttore e bloccare un numero significativo di thread prima che abbiano tutti esito negativo.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo schema:

- Per impedire che un'applicazione tenti di richiamare un servizio remoto o accedere a una risorsa condivisa se è molto probabile che questa operazione abbia esito negativo.

Questo modello non è consigliato:

- Per gestire l'accesso a risorse private locali in un'applicazione, come una struttura di dati in memoria. In questo ambiente, l'uso di un interruttore aggiungerebbe un overhead per il sistema.
- Come soluzione alternativa per la gestione delle eccezioni nella logica di business delle applicazioni.

## <a name="example"></a>Esempio

In un'applicazione Web diverse pagine vengono popolate con dati recuperati da un servizio esterno. Se il sistema implementa una memorizzazione nella cache minima, la maggior parte dei riscontri per queste pagine causerà un round trip al servizio. Le connessioni dall'applicazione Web al servizio potrebbero essere configurate con un periodo di timeout (in genere 60 secondi), per cui se il servizio non risponde entro questo intervallo di tempo la logica in ogni pagina Web assumerà che il servizio non è disponibile e genererà un'eccezione.

Se, tuttavia, il servizio ha esito negativo e il sistema è molto occupato, gli utenti potrebbero essere costretti ad attendere fino a 60 secondi prima che si verifichi un'eccezione. Alla fine, risorse come memoria, connessioni e thread potrebbero esaurirsi e impedire ad altri utenti di connettersi al sistema, anche se non stanno accedendo a pagine che recuperano dati dal servizio.

Il ridimensionamento del sistema mediante l'aggiunta di ulteriori server Web e l'implementazione del bilanciamento del carico potrebbe ritardare quando le risorse si esauriscono, ma non risolverà il problema perché le richieste utente continueranno a non rispondere e tutti i server Web alla fine potrebbero ancora esaurire le risorse.

Il wrapping della logica che si connette al servizio e recupera i dati in un interruttore può essere utile per risolvere questo problema e gestire l'esito negativo del servizio in modo più efficace. Le richieste utente continueranno ad avere esito negativo, ma in tempi più rapidi, e le risorse non verranno bloccate.

La classe `CircuitBreaker` conserva le informazioni sugli stati di un interruttore in un oggetto che implementa l'interfaccia `ICircuitBreakerStateStore` mostrata nel codice che segue.

```csharp
interface ICircuitBreakerStateStore
{
  CircuitBreakerStateEnum State { get; }

  Exception LastException { get; }

  DateTime LastStateChangedDateUtc { get; }

  void Trip(Exception ex);

  void Reset();

  void HalfOpen();

  bool IsClosed { get; }
}
```

La proprietà `State` indica lo stato corrente dell'interruttore, che sarà **Aperto**, **Semiaperto** o **Chiuso** in base a quanto definito dall'enumerazione `CircuitBreakerStateEnum`. Il metodo `IsClosed` deve essere true se l'interruttore è chiuso, ma false se è aperto o semiaperto. Il metodo `Trip` imposta lo stato dell'interruttore su Aperto e registra l'eccezione che ha causato il cambiamento di stato, insieme alla data e all'ora in cui si è verificata l'eccezione. Le proprietà `LastException` e `LastStateChangedDateUtc` restituiscono queste informazioni. Il metodo `Reset` chiude l'interruttore, mentre il metodo `HalfOpen` lo imposta su Semiaperto.

La classe `InMemoryCircuitBreakerStateStore` nell'esempio contiene un'implementazione dell'interfaccia `ICircuitBreakerStateStore`. La classe `CircuitBreaker` crea un'istanza di questa classe per conservare lo stato dell'interruttore.

Il metodo `ExecuteAction` nella classe `CircuitBreaker` esegue il wrapping di un'operazione specificata come delegato `Action`. Se l'interruttore è chiuso, `ExecuteAction` richiama il delegato `Action`. Se l'operazione non riesce, un gestore di eccezioni chiama `TrackException`, che imposta lo stato dell'interruttore su Aperto. Il codice di esempio seguente illustra questo flusso.

```csharp
public class CircuitBreaker
{
  private readonly ICircuitBreakerStateStore stateStore =
    CircuitBreakerStateStoreFactory.GetCircuitBreakerStateStore();

  private readonly object halfOpenSyncObject = new object ();
  ...
  public bool IsClosed { get { return stateStore.IsClosed; } }

  public bool IsOpen { get { return !IsClosed; } }

  public void ExecuteAction(Action action)
  {
    ...
    if (IsOpen)
    {
      // The circuit breaker is Open.
      ... (see code sample below for details)
    }

    // The circuit breaker is Closed, execute the action.
    try
    {
      action();
    }
    catch (Exception ex)
    {
      // If an exception still occurs here, simply
      // retrip the breaker immediately.
      this.TrackException(ex);

      // Throw the exception so that the caller can tell
      // the type of exception that was thrown.
      throw;
    }
  }

  private void TrackException(Exception ex)
  {
    // For simplicity in this example, open the circuit breaker on the first exception.
    // In reality this would be more complex. A certain type of exception, such as one
    // that indicates a service is offline, might trip the circuit breaker immediately.
    // Alternatively it might count exceptions locally or across multiple instances and
    // use this value over time, or the exception/success ratio based on the exception
    // types, to open the circuit breaker.
    this.stateStore.Trip(ex);
  }
}
```

L'esempio seguente illustra il codice (omesso nell'esempio precedente) che viene eseguito se l'interruttore non è chiuso. Verifica innanzitutto se l'interruttore è stato aperto per un intervallo di tempo più lungo di quello specificato dal campo `OpenToHalfOpenWaitTime` locale nella classe `CircuitBreaker`. In questo caso, il metodo `ExecuteAction` imposta l'interruttore su Semiaperto, quindi tenta di eseguire l'operazione specificata dal delegato `Action`.

Se l'operazione ha esito positivo, l'interruttore viene reimpostato sullo stato Chiuso. Se l'operazione non riesce, viene riattivato lo stato Aperto e l'ora in cui si è verificata l'eccezione viene aggiornata, in modo che l'interruttore attenderà per un ulteriore intervallo di tempo prima di provare ad eseguire di nuovo l'operazione.

Se l'interruttore è stato aperto solo per un breve periodo di tempo, inferiore al valore `OpenToHalfOpenWaitTime`, il metodo `ExecuteAction` genera semplicemente un'eccezione `CircuitBreakerOpenException` e restituisce l'errore che ha causato la transizione dell'interruttore allo stato Aperto.

Usa inoltre un blocco per impedire che l'interruttore tenti di eseguire chiamate simultanee all'operazione mentre è Semiaperto. Un tentativo simultaneo di chiamata all'operazione verrà gestito come se l'interruttore fosse Aperto e avrà esito negativo con un'eccezione, come descritto più avanti.

```csharp
    ...
    if (IsOpen)
    {
      // The circuit breaker is Open. Check if the Open timeout has expired.
      // If it has, set the state to HalfOpen. Another approach might be to
      // check for the HalfOpen state that had be set by some other operation.
      if (stateStore.LastStateChangedDateUtc + OpenToHalfOpenWaitTime < DateTime.UtcNow)
      {
        // The Open timeout has expired. Allow one operation to execute. Note that, in
        // this example, the circuit breaker is set to HalfOpen after being
        // in the Open state for some period of time. An alternative would be to set
        // this using some other approach such as a timer, test method, manually, and
        // so on, and check the state here to determine how to handle execution
        // of the action.
        // Limit the number of threads to be executed when the breaker is HalfOpen.
        // An alternative would be to use a more complex approach to determine which
        // threads or how many are allowed to execute, or to execute a simple test
        // method instead.
        bool lockTaken = false;
        try
        {
          Monitor.TryEnter(halfOpenSyncObject, ref lockTaken)
          if (lockTaken)
          {
            // Set the circuit breaker state to HalfOpen.
            stateStore.HalfOpen();

            // Attempt the operation.
            action();

            // If this action succeeds, reset the state and allow other operations.
            // In reality, instead of immediately returning to the Closed state, a counter
            // here would record the number of successful operations and return the
            // circuit breaker to the Closed state only after a specified number succeed.
            this.stateStore.Reset();
            return;
          }
          catch (Exception ex)
          {
            // If there's still an exception, trip the breaker again immediately.
            this.stateStore.Trip(ex);

            // Throw the exception so that the caller knows which exception occurred.
            throw;
          }
          finally
          {
            if (lockTaken)
            {
              Monitor.Exit(halfOpenSyncObject);
            }
          }
        }
      }
      // The Open timeout hasn't yet expired. Throw a CircuitBreakerOpen exception to
      // inform the caller that the call was not actually attempted,
      // and return the most recent exception received.
      throw new CircuitBreakerOpenException(stateStore.LastException);
    }
    ...
```

Per usare un oggetto `CircuitBreaker` per proteggere un'operazione, un'applicazione crea un'istanza della classe `CircuitBreaker` e richiama il metodo `ExecuteAction`, specificando l'operazione da eseguire come parametro. L'applicazione deve essere pronta a intercettare l'eccezione `CircuitBreakerOpenException` se l'operazione ha esito negativo perché l'interruttore è aperto. Il codice seguente mostra un esempio:

```csharp
var breaker = new CircuitBreaker();

try
{
  breaker.ExecuteAction(() =>
  {
    // Operation protected by the circuit breaker.
    ...
  });
}
catch (CircuitBreakerOpenException ex)
{
  // Perform some different action when the breaker is open.
  // Last exception details are in the inner exception.
  ...
}
catch (Exception ex)
{
  ...
}
```

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Quando si implementa questo modello, possono essere utili anche i modelli seguenti:

- [Modello di ripetizione dei tentativi][retry-pattern]. Descrive in che modo un'applicazione può gestire gli errori temporanei previsti durante il tentativo di connessione a un servizio o a una risorsa di rete ritentando in modo trasparente un'operazione non riuscita in precedenza.

- [Criterio di monitoraggio endpoint di integrità](health-endpoint-monitoring.md). Un interruttore potrebbe essere in grado di verificare l'integrità di un servizio inviando una richiesta a un endpoint esposto dal servizio. Il servizio dovrebbe restituire informazioni che ne indicano lo stato.


[retry-pattern]: ./retry.md
