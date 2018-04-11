---
title: Coda con priorità
description: Assegnare una priorità alle richieste inviate ai servizi in modo che le richieste con una priorità più alta vengano ricevute ed elaborate più rapidamente rispetto a quelle con priorità più bassa.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- messaging
- performance-scalability
ms.openlocfilehash: ecfbb38304bb95587e9ca15523ad9594898d9b32
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="priority-queue-pattern"></a>Modello di coda con priorità

[!INCLUDE [header](../_includes/header.md)]

Assegnare una priorità alle richieste inviate ai servizi in modo che le richieste con una priorità più alta vengano ricevute ed elaborate più rapidamente rispetto a quelle con priorità più bassa. Questo modello è utile nelle applicazioni che offrono diverse garanzie del livello di servizio ai singoli client.

## <a name="context-and-problem"></a>Contesto e problema

Le applicazioni possono delegare attività specifiche ad altri servizi, ad esempio per l'elaborazione in background o l'integrazione con altre applicazioni o altri servizi. Nel cloud, una coda di messaggi viene in genere usata per delegare attività all'elaborazione in background. In molti casi l'ordine con cui le richieste vengono ricevute da un servizio non è importante. In alcuni casi è invece necessario assegnare una priorità a richieste specifiche. Queste richieste devono essere elaborate prima delle richieste con priorità più bassa inviate precedentemente dall'applicazione.

## <a name="solution"></a>Soluzione

Una coda è in genere una struttura First-In, First-Out (FIFO) e gli utenti solitamente ricevono i messaggi nello stesso ordine con cui sono stati inseriti nella coda. Tuttavia alcune code di messaggi supportano l'invio dei messaggi in base a una priorità. L'applicazione che inserisce un messaggio può assegnare una priorità e i messaggi nella coda vengono riordinati automaticamente in modo che quelli con priorità più alta vengano ricevuti prima di quelli con priorità più bassa. La figura illustra una coda di messaggi con priorità.

![Figura 1 - Uso di un meccanismo di accodamento che supporta l'assegnazione di priorità ai messaggi](./_images/priority-queue-pattern.png)

> La maggior parte delle implementazioni delle code di messaggi supporta più consumer (secondo il [modello di consumer concorrenti](https://msdn.microsoft.com/library/dn568101.aspx)) e il numero di processi consumer può essere aumentato o diminuito in base alla richiesta.

Come soluzione alternativa nei sistemi che non supportano le code di messaggi basate sulla priorità è possibile gestire una coda separata per ogni priorità. L'applicazione è responsabile dell'inserimento dei messaggi nella coda appropriata. Ogni coda può avere un pool di consumer distinto. Le code con priorità maggiore possono avere un pool di consumer più ampio eseguito su hardware più veloce rispetto alle code con priorità inferiore. La figura seguente illustra l'uso di code di messaggi separate per ogni priorità.

![Figura 2 - Uso di code di messaggi separate per ogni priorità](./_images/priority-queue-separate.png)


Una variante di questa strategia consiste nell'usare un unico pool di consumer che gestisca prima i messaggi nelle code con priorità elevata e successivamente inizi a recuperare i messaggi dalle code con priorità inferiore. Esistono alcune differenze a livello semantico tra una soluzione che usa un solo pool di processi consumer (con una sola coda che supporta messaggi con priorità diverse o con più code ognuna delle quali gestisce messaggi con una sola priorità) e una soluzione che usa più code con un pool separato per ogni coda.

Nel caso del singolo pool, i messaggi con priorità più alta vengono sempre ricevuti ed elaborati prima dei messaggi con priorità più bassa. In teoria, i messaggi con una priorità molto bassa potrebbero essere continuamente sostituiti dai messaggi con priorità più alta e non essere mai elaborati. Nel caso di più pool, i messaggi con priorità più bassa verranno sempre elaborati, anche se non altrettanto rapidamente di quelli con priorità più alta (a seconda delle dimensioni relative dei pool e delle risorse che hanno a disposizione).

L'uso di un meccanismo di accodamento basato sulla priorità può offrire i vantaggi seguenti:

- Consente alle applicazioni di soddisfare i requisiti aziendali a livello di priorità di disponibilità o prestazioni, come l'offerta di livelli di servizio diversi a specifici gruppi di clienti.

- Può contribuire a ridurre al minimo i costi operativi. Nel caso della singola coda, è possibile ridurre il numero di consumer se necessario. I messaggi con priorità alta continueranno a essere elaborati per primi (anche se probabilmente più lentamente), mentre l'invio dei messaggi con priorità più bassa potrebbe essere ulteriormente ritardato. Se è stata implementata la soluzione con più code di messaggi con pool di consumer distinti per ogni coda, è possibile ridurre il pool di consumer per le code con priorità più bassa o persino sospendere l'elaborazione di alcune code con priorità molto bassa arrestando tutti i consumer in attesa di messaggi su tali code.

- L'approccio basato su più code di messaggi può contribuire a ottimizzare le prestazioni e la scalabilità dell'applicazione eseguendo il partizionamento dei messaggi in base ai requisiti di elaborazione. Ad esempio, alle attività di importanza vitale può essere assegnata la priorità massima in modo che vengano gestite dai ricevitori che vengono eseguiti immediatamente, mentre le attività in background meno importanti possono essere gestite dai ricevitori programmati per l'esecuzione in periodi con meno traffico.

## <a name="issues-and-considerations"></a>Problemi e considerazioni

Prima di decidere come implementare questo modello, considerare quanto segue:

Definire le priorità nel contesto della soluzione. Ad esempio, priorità alta potrebbe significare che i messaggi debbano essere elaborati entro dieci secondi. Identificare i requisiti per la gestione degli elementi con priorità alta e le altre risorse che dovrebbero essere allocate per soddisfare questi criteri.

Decidere se tutti gli elementi con priorità alta debbano essere elaborati prima di qualsiasi elemento con priorità più bassa. Se i messaggi vengono elaborati da un solo pool di consumer, occorre fornire un meccanismo che sia in grado di anticipare e sospendere un'attività che sta gestendo un messaggio con priorità bassa qualora diventi disponibile un messaggio con priorità più alta.

Nel caso di più code, quando si usa un solo pool di processi consumer in ascolto su tutte le code invece di un pool di consumer dedicati per ogni coda, il consumer deve applicare un algoritmo che assicuri che vengano sempre serviti i messaggi delle code con priorità più alta prima di quelli delle code con priorità più bassa.

Monitorare la velocità di elaborazione sulle code con priorità alta e bassa per verificare che i messaggi in queste code vengano elaborati con la frequenza prevista.

Se occorre garantire che i messaggi con priorità bassa verranno elaborati, è necessario implementare la soluzione con più code di messaggi e più pool di consumer. In alternativa, in una coda che supporta la classificazione dei messaggi in ordine di priorità, è possibile aumentare dinamicamente la priorità di un messaggio in coda man mano che trascorre il tempo. Questo approccio dipende però dalla coda di messaggi che fornisce questa funzionalità.

L'uso di una coda separata per ogni priorità di messaggio è più indicata per i sistemi con un numero limitato di priorità ben definite.

Le priorità dei messaggi possono essere determinate in modo logico dal sistema. Ad esempio, invece di assegnare esplicitamente una priorità alta o bassa ai messaggi, il sistema potrebbe designare le priorità in base ai clienti che pagano una tariffa e a quelli che non la pagano. A seconda del modello aziendale, il sistema può allocare più risorse per l'elaborazione dei messaggi dei clienti paganti rispetto a quelli non paganti.

Al controllo dei messaggi in una coda potrebbe essere associato un costo finanziario e di elaborazione. Alcuni sistemi di messaggistica commerciali addebitano infatti un piccolo importo per ogni messaggio inserito o recuperato e per ogni volta che vengono controllati i messaggi in una coda. Questo costo aumenta se occorre controllare più code.

È possibile modificare dinamicamente le dimensioni di un pool di consumer in base alla lunghezza della coda servita dal pool. Per altre informazioni, vedere [Indicazioni sulla scalabilità automatica](https://msdn.microsoft.com/library/dn589774.aspx).

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Questo modello è utile in scenari in cui:

- Il sistema deve gestire più attività con priorità diverse.

- Utenti o tenant diversi devono essere serviti con una priorità diversa.

## <a name="example"></a>Esempio

Microsoft Azure non fornisce un meccanismo di accodamento che supporti in maniera nativa la classificazione automatica dei messaggi in base alla priorità mediante l'ordinamento. Fornisce tuttavia argomenti e sottoscrizioni del bus di servizio di Azure che supportano un meccanismo di accodamento che fornisce il filtro dei messaggi, oltre a un'ampia varietà di funzionalità flessibili che lo rendono ideale per l'uso nella maggior parte delle implementazioni delle code con priorità.

Una soluzione di Azure può implementare un argomento del bus di servizio in cui un'applicazione può inserire messaggi, allo stesso modo di una coda. I messaggi possono contenere metadati in forma di proprietà personalizzate definite dall'applicazione. Le sottoscrizioni del bus di servizio possono essere associate all'argomento e queste sottoscrizioni possono filtrare i messaggi in base alle relative proprietà. Quando un'applicazione invia un messaggio a un argomento, il messaggio viene indirizzato alla sottoscrizione appropriata in cui possa essere letto da un consumer. I processi consumer possono recuperare messaggi da una sottoscrizione usando la stessa semantica di una coda di messaggi (una sottoscrizione è una coda logica). La figura seguente illustra l'implementazione di una coda con priorità con argomenti e sottoscrizioni del bus di servizio di Azure.

![Figura 3 - Implementazione di una coda con priorità con argomenti e sottoscrizioni del bus di servizio di Azure](./_images/priority-queue-service-bus.png)


Nella figura riportata sopra l'applicazione crea diversi messaggi e assegna una proprietà personalizzata chiamata `Priority` in ogni messaggio con un valore, `High` o `Low`. L'applicazione inserisce questi messaggi in un argomento. All'argomento sono associate due sottoscrizioni che filtrano i messaggi esaminando la proprietà `Priority`. Una sottoscrizione accetta i messaggi in cui la proprietà `Priority` è impostata su `High`, mentre l'altra accetta i messaggi in cui la proprietà `Priority` è impostata su `Low`. Un pool di consumer legge i messaggi di ogni sottoscrizione. La sottoscrizione con priorità alta ha un pool più ampio e i relativi consumer potrebbero essere eseguiti in computer più potenti con più risorse a disposizione rispetto ai consumer del pool con priorità bassa.

In questo esempio non c'è niente di speciale in merito alla designazione di messaggi con priorità alta e bassa. Si tratta di semplici etichette specificate come proprietà in ogni messaggio e usate per indirizzare i messaggi a una sottoscrizione specifica. Se sono necessarie ulteriori priorità, è relativamente facile creare altre sottoscrizioni e pool di processi consumer per gestire queste priorità.

La soluzione PriorityQueue disponibile in [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/priority-queue) contiene un'implementazione di questo approccio. Questa soluzione contiene due progetti di ruolo di lavoro denominati `PriorityQueue.High` e `PriorityQueue.Low`. Questi ruoli di lavoro ereditano dalla classe `PriorityWorkerRole` che contiene la funzionalità per la connessione a una sottoscrizione specificata nel metodo `OnStart`.

I ruoli di lavoro `PriorityQueue.High` e `PriorityQueue.Low` si connettono a sottoscrizioni diverse, definite dalle relative impostazioni di configurazione. Un amministratore può configurare diverse istanze di ogni ruolo da eseguire. In genere esisteranno più istanze del ruolo di lavoro `PriorityQueue.High` rispetto al ruolo di lavoro `PriorityQueue.Low`.

Il metodo `Run` nella classe `PriorityWorkerRole` predispone l'esecuzione del metodo virtuale `ProcessMessage` (definito anche nella classe `PriorityWorkerRole`) per ogni messaggio ricevuto dalla coda. Il codice seguente mostra i metodi `Run` e `ProcessMessage`. La classe `QueueManager`, definita nel progetto PriorityQueue.Shared, fornisce metodi helper per l'uso delle code del bus di servizio di Azure.

```csharp
public class PriorityWorkerRole : RoleEntryPoint
{
  private QueueManager queueManager;
  ...

  public override void Run()
  {
    // Start listening for messages on the subscription.
    var subscriptionName = CloudConfigurationManager.GetSetting("SubscriptionName");
    this.queueManager.ReceiveMessages(subscriptionName, this.ProcessMessage);
    ...;
  }
  ...

  protected virtual async Task ProcessMessage(BrokeredMessage message)
  {
    // Simulating processing.
    await Task.Delay(TimeSpan.FromSeconds(2));
  }
}
```
I ruoli di lavoro `PriorityQueue.High` e `PriorityQueue.Low` eseguono entrambi l'override della funzionalità predefinita del metodo `ProcessMessage`. Il codice seguente mostra il metodo `ProcessMessage` per il ruolo di lavoro `PriorityQueue.High`.

```csharp
protected override async Task ProcessMessage(BrokeredMessage message)
{
  // Simulate message processing for High priority messages.
  await base.ProcessMessage(message);
  Trace.TraceInformation("High priority message processed by " +
    RoleEnvironment.CurrentRoleInstance.Id + " MessageId: " + message.MessageId);
}
```

Quando un'applicazione inserisce messaggi nell'argomento associato alle sottoscrizioni usate dai ruoli di lavoro `PriorityQueue.High` e `PriorityQueue.Low`, specifica la priorità usando la proprietà personalizzata `Priority`, come illustrato nel codice di esempio seguente. Questo codice (implementato nella classe `WorkerRole` del progetto PriorityQueue.Sender) usa il metodo helper `SendBatchAsync` della classe `QueueManager` per inserire messaggi in un argomento in batch.

```csharp
// Send a low priority batch.
var lowMessages = new List<BrokeredMessage>();

for (int i = 0; i < 10; i++)
{
  var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
  message.Properties["Priority"] = Priority.Low;
  lowMessages.Add(message);
}

this.queueManager.SendBatchAsync(lowMessages).Wait();
...

// Send a high priority batch.
var highMessages = new List<BrokeredMessage>();

for (int i = 0; i < 10; i++)
{
  var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
  message.Properties["Priority"] = Priority.High;
  highMessages.Add(message);
}

this.queueManager.SendBatchAsync(highMessages).Wait();
```

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

- Un esempio che illustra questo modello è disponibile su [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/priority-queue).

- [Introduzione alla messaggistica asincrona](https://msdn.microsoft.com/library/dn589781.aspx). Un servizio consumer che elabora una richiesta potrebbe dover inviare una risposta all'istanza dell'applicazione che ha inviato la richiesta. Fornisce informazioni sulle strategie che è possibile usare per implementare la messaggistica richiesta-risposta.

- [Modello di consumer concorrenti](competing-consumers.md). Per aumentare la velocità effettiva delle code, è possibile impostare più consumer in ascolto sulla stessa coda ed elaborare le attività in parallelo. Questi consumer competeranno per i messaggi, ma solo uno dovrebbe essere in grado di elaborare ogni messaggio. Offre altre informazioni sui vantaggi e gli svantaggi dell'implementazione di questo approccio.

- [Modello di limitazione](throttling.md). È possibile implementare la limitazione delle richieste mediante le code. L'assegnazione di priorità ai messaggi può garantire che alle richieste provenienti da applicazioni di importanza cruciale o da applicazioni eseguite da clienti importanti sia data priorità sulle richieste provenienti da applicazioni meno importanti.

- [Indicazioni sulla scalabilità automatica](https://msdn.microsoft.com/library/dn589774.aspx). Potrebbe essere possibile aumentare o ridurre le dimensioni del pool di processi consumer che gestiscono una coda in base alla lunghezza della coda. Questa strategia può migliorare le prestazioni, specialmente per i pool che gestiscono messaggi con priorità alta.

- [Modelli di integrazione aziendale con il bus di servizio](http://abhishekrlal.com/2013/01/11/enterprise-integration-patterns-with-service-bus-part-2/) sul blog di Abhishek Lal.

