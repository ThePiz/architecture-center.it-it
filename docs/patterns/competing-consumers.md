---
title: Modello di consumer concorrenti
titleSuffix: Cloud Design Patterns
description: Consentire a più consumer concorrenti di elaborare i messaggi ricevuti sullo stesso canale di messaggistica.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: ea3f48971a78f59ad6575b055278aab449fa26a1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244472"
---
# <a name="competing-consumers-pattern"></a>Modello di consumer concorrenti

[!INCLUDE [header](../_includes/header.md)]

Consentire a più consumer concorrenti di elaborare i messaggi ricevuti sullo stesso canale di messaggistica. Questo modello consente a un sistema di elaborare più messaggi contemporaneamente per ottimizzare la velocità effettiva, per migliorare la scalabilità e la disponibilità e per bilanciare il carico di lavoro.

## <a name="context-and-problem"></a>Contesto e problema

Un'applicazione in esecuzione nel cloud deve gestire un numero elevato di richieste. Una tecnica comune prevede che, anziché elaborare ogni richiesta in modo sincrono, l'applicazione le passi tramite un sistema di messaggistica a un altro servizio (un servizio consumer) che le gestisce in modo asincrono. Questa strategia consente di assicurarsi che la logica di business nell'applicazione non venga bloccata durante l'elaborazione delle richieste.

Il numero di richieste può variare in modo significativo nel tempo per diversi motivi. Un aumento improvviso delle attività utente o l'arrivo di richieste aggregate provenienti da più tenant può causare un carico di lavoro imprevisto. Negli orari di punta, un sistema potrebbe dover elaborare centinaia di richieste al secondo, mentre in altri momenti il numero potrebbe essere molto basso. Inoltre, la natura del lavoro eseguito per gestire le richieste può essere estremamente variabile. Usando una singola istanza del servizio consumer, è possibile che l'istanza sia inondata da un numero eccessivo di richieste o che il sistema di messaggistica venga sovraccaricato da un forte afflusso di messaggi provenienti dall'applicazione. Per gestire questo carico di lavoro variabile, il sistema può eseguire più istanze del servizio consumer. Tuttavia, i consumer devono essere coordinati per garantire che ogni messaggio venga recapitato solo a un singolo consumer. Inoltre, occorre bilanciare il carico di lavoro tra i consumer per evitare che un'istanza diventi un collo di bottiglia.

## <a name="solution"></a>Soluzione

Usare una coda di messaggi per implementare il canale di comunicazione tra l'applicazione e le istanze del servizio consumer. L'applicazione invia le richieste alla coda sotto forma di messaggi e le istanze del servizio consumer ricevono i messaggi dalla coda e li elaborano. Questo approccio consente allo stesso pool di istanze del servizio consumer di gestire i messaggi provenienti da qualsiasi istanza dell'applicazione. La figura illustra l'uso di una coda di messaggi per distribuire il lavoro alle istanze di un servizio.

![Uso di una coda di messaggi per la distribuzione del lavoro alle istanze di un servizio](./_images/competing-consumers-diagram.png)

Questa soluzione offre i vantaggi seguenti:

- Offre un sistema con bilanciamento del carico che può gestire notevoli variazioni nel volume di richieste inviate da istanze dell'applicazione. La coda funge da buffer tra le istanze dell'applicazione e le istanze del servizio consumer. Questo contribuisce a ridurre al minimo l'impatto sulla disponibilità e sui tempi di risposta per le istanze sia dell'applicazione che del servizio, come descritto in [Modello di livellamento del carico basato sulle code](./queue-based-load-leveling.md). La gestione di un messaggio che richiede operazioni di elaborazione a esecuzione prolungata non impedisce la gestione contemporanea di altri messaggi da parte di altre istanze del servizio consumer.

- Migliora l'affidabilità. Se un producer comunica direttamente con un consumer invece di usare questo modello, ma non controlla il consumer, esiste una forte probabilità che messaggi vadano persi o non vengano elaborati se si verifica un errore del consumer. In questo modello, i messaggi non vengono inviati a una specifica istanza del servizio. Un'istanza del servizio non riuscita non bloccherà il producer e i messaggi possono essere elaborati da qualsiasi istanza funzionante del servizio.

- Non richiede un coordinamento complesso tra i consumer o tra il producer e le istanze del consumer. La coda di messaggi assicura che ogni messaggio sia recapitato almeno una volta.

- È scalabile. Il sistema può aumentare o diminuire in modo dinamico il numero di istanze del servizio consumer in base alla fluttuazione del volume dei messaggi.

- Se la coda di messaggi fornisce operazioni di lettura transazionali, può migliorare la resilienza. Se un'istanza del servizio consumer che legge ed elabora il messaggio come parte di un'operazione transazionale ha esito negativo, questo modello può garantire che il messaggio verrà restituito alla coda per essere prelevato e gestito da un'altra istanza del servizio consumer.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo modello, considerare quanto segue:

- **Ordinamento dei messaggi**. L'ordine in cui le istanze del servizio consumer ricevono i messaggi non è garantito e non riflette necessariamente l'ordine in cui i messaggi sono stati creati. Progettare il sistema in modo da assicurarsi che l'elaborazione dei messaggi sia idempotente, perché ciò è utile per eliminare qualsiasi dipendenza rispetto all'ordine in cui vengono gestiti i messaggi. Per altre informazioni, vedere [Idempotency Patterns](https://blog.jonathanoliver.com/idempotency-patterns/) (Modelli di idempotenza) sul blog di Jonathan Oliver.

    > Le code del bus di servizio di Microsoft Azure possono implementare l'ordinamento First In, First Out garantito dei messaggi mediante sessioni di messaggistica. Per altre informazioni, vedere [Modelli di messaggistica mediante sessioni](https://msdn.microsoft.com/magazine/jj863132.aspx).

- **Progettazione dei servizi per la resilienza**. Se il sistema è progettato per rilevare e riavviare le istanze di servizio non riuscite, può essere necessario implementare le operazioni di elaborazione eseguite dalle istanze del servizio come idempotenti, in modo da ridurre al minimo gli effetti se un singolo messaggio viene recuperato ed elaborato più di una volta.

- **Rilevamento dei messaggi non elaborabili**. Un messaggio in formato non valido o un'attività che richiede l'accesso a risorse non disponibili può causare un errore in un'istanza del servizio. Il sistema deve impedire che tali messaggi siano restituiti alla coda e invece acquisirne e archiviarne i dettagli altrove, in modo che possano essere analizzati se necessario.

- **Gestione dei risultati**. L'istanza del servizio che gestisce un messaggio è completamente separata dalla logica dell'applicazione che genera il messaggio e potrebbero non essere in grado di comunicare direttamente. Se l'istanza del servizio genera risultati che devono essere passati alla logica dell'applicazione, queste informazioni devono essere archiviate in un percorso accessibile da entrambe. Per evitare che logica dell'applicazione recuperi dati incompleti, il sistema deve segnalare il completamento dell'elaborazione.

     > Se si usa Azure, un processo di lavoro può passare i risultati alla logica dell'applicazione usando una coda dedicata di risposte al messaggio. La logica dell'applicazione deve essere in grado di correlare questi risultati con il messaggio originale. Questo scenario è descritto più dettagliatamente in [Introduzione alla messaggistica asincrona](https://msdn.microsoft.com/library/dn589781.aspx).

- **Scalabilità del sistema di messaggistica**. In una soluzione su larga scala, una singola coda di messaggi può essere sovraccaricata dal numero di messaggi e diventare un collo di bottiglia nel sistema. In questo caso, è possibile partizionare il sistema di messaggistica in modo da inviare i messaggi provenienti da specifici producer a una determinata coda oppure usare il bilanciamento del carico per distribuire i messaggi tra più code di messaggi.

- **Affidabilità del sistema di messaggistica**. Per garantire che dopo l'accodamento di un messaggio da parte dell'applicazione questo non vada perso, è necessario un sistema di messaggistica affidabile. Ciò è essenziale per assicurare che tutti i messaggi vengano recapitati almeno una volta.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello quando:

- Il carico di lavoro di un'applicazione è suddiviso in attività che è possibile eseguire in modo asincrono.
- Le attività sono indipendenti e possono essere eseguite in parallelo.
- Il volume di lavoro è estremamente variabile e per questo motivo è necessaria una soluzione scalabile.
- La soluzione deve offrire disponibilità elevata e deve essere resiliente se l'elaborazione di un'attività ha esito negativo.

Questo modello potrebbe non essere utile quando:

- Non è semplice separare il carico di lavoro dell'applicazione in attività distinte o esiste un livello elevato di dipendenza tra le attività.
- Le attività devono essere eseguite in modo sincrono e la logica dell'applicazione deve attendere il completamento di un'attività prima di continuare.
- Le attività devono essere eseguite in una sequenza specifica.

> Alcuni sistemi di messaggistica supportano sessioni che consentono a un producer di raggruppare i messaggi e assicurarsi che siano tutti gestiti dallo stesso consumer. Questo meccanismo può essere usato con i messaggi classificati in ordine di priorità, se supportati, per implementare una forma di ordinamento che prevede il recapito dei messaggi in sequenza da un producer a un singolo consumer.

## <a name="example"></a>Esempio

Azure offre code di archiviazione e code del bus di servizio che possono fungere da meccanismo per l'implementazione di questo modello. La logica dell'applicazione può inviare messaggi a una coda e i consumer implementati come attività in uno o più ruoli possono recuperare i messaggi dalla coda ed elaborarli. Per garantire la resilienza, le code del bus di servizio permettono a un consumer di usare la modalità `PeekLock` durante il recupero di un messaggio dalla coda. Questa modalità non rimuove effettivamente il messaggio, ma semplicemente lo nasconde agli altri consumer. Il consumer originale può eliminarlo dopo aver terminato l'elaborazione. In caso di errore del consumer, il blocco di visualizzazione raggiunge il timeout e il messaggio diventa nuovamente visibile, consentendo a un altro consumer di recuperarlo.

Per informazioni dettagliate sull'uso delle code del bus di servizio di Azure, vedere[Code, argomenti e sottoscrizioni del bus di servizio](https://msdn.microsoft.com/library/windowsazure/hh367516.aspx).

Per informazioni sull'uso delle code di archiviazione di Azure, vedere [Introduzione all'archiviazione code di Azure con .NET](/azure/storage/queues/storage-dotnet-how-to-use-queues).

L'esempio di codice dalla classe `QueueManager` della soluzione CompetingConsumers disponibile in [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/competing-consumers) illustra come creare una coda usando un'istanza di `QueueClient` nel gestore dell'evento `Start` in un ruolo Web o di lavoro.

```csharp
private string queueName = ...;
private string connectionString = ...;
...

public async Task Start()
{
  // Check if the queue already exists.
  var manager = NamespaceManager.CreateFromConnectionString(this.connectionString);
  if (!manager.QueueExists(this.queueName))
  {
    var queueDescription = new QueueDescription(this.queueName);

    // Set the maximum delivery count for messages in the queue. A message
    // is automatically dead-lettered after this number of deliveries. The
    // default value for dead letter count is 10.
    queueDescription.MaxDeliveryCount = 3;

    await manager.CreateQueueAsync(queueDescription);
  }
  ...

  // Create the queue client. By default the PeekLock method is used.
  this.client = QueueClient.CreateFromConnectionString(
    this.connectionString, this.queueName);
}
```

Il frammento di codice seguente mostra in che modo un'applicazione può creare e inviare un batch di messaggi alla coda.

```csharp
public async Task SendMessagesAsync()
{
  // Simulate sending a batch of messages to the queue.
  var messages = new List<BrokeredMessage>();

  for (int i = 0; i < 10; i++)
  {
    var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
    messages.Add(message);
  }
  await this.client.SendBatchAsync(messages);
}
```

Il codice seguente illustra come un'istanza del servizio consumer può ricevere messaggi dalla coda usando un approccio guidato dagli eventi. Il parametro `processMessageTask` per il metodo `ReceiveMessages` è un delegato che referenzia il codice da eseguire quando viene ricevuto un messaggio. Questo codice viene eseguito in modo asincrono.

```csharp
private ManualResetEvent pauseProcessingEvent;
...

public void ReceiveMessages(Func<BrokeredMessage, Task> processMessageTask)
{
  // Set up the options for the message pump.
  var options = new OnMessageOptions();

  // When AutoComplete is disabled it's necessary to manually
  // complete or abandon the messages and handle any errors.
  options.AutoComplete = false;
  options.MaxConcurrentCalls = 10;
  options.ExceptionReceived += this.OptionsOnExceptionReceived;

  // Use of the Service Bus OnMessage message pump.
  // The OnMessage method must be called once, otherwise an exception will occur.
  this.client.OnMessageAsync(
    async (msg) =>
    {
      // Will block the current thread if Stop is called.
      this.pauseProcessingEvent.WaitOne();

      // Execute processing task here.
      await processMessageTask(msg);
    },
    options);
}
...

private void OptionsOnExceptionReceived(object sender,
  ExceptionReceivedEventArgs exceptionReceivedEventArgs)
{
  ...
}
```

Si noti che le funzionalità di scalabilità automatica, ad esempio quelle disponibili in Azure, possono essere usate per avviare e arrestare le istanze del ruolo in base alle variazioni della lunghezza della coda. Per altre informazioni, vedere [Indicazioni sulla scalabilità automatica](https://msdn.microsoft.com/library/dn589774.aspx). Inoltre, non è necessario mantenere una corrispondenza uno a uno tra le istanze del ruolo e i processi di lavoro&mdash;una singola istanza del ruolo può implementare più processi di lavoro. Per altre informazioni, vedere [Modello di consolidamento delle risorse di calcolo](./compute-resource-consolidation.md).

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

- [Introduzione alla messaggistica asincrona](https://msdn.microsoft.com/library/dn589781.aspx). Le code di messaggi sono un meccanismo di comunicazione asincrona. Se un servizio consumer deve inviare una risposta a un'applicazione, può essere necessario implementare una forma di messaggistica di risposta. L'articolo Introduzione alla messaggistica asincrona contiene informazioni sull'implementazione della messaggistica di richiesta/risposta tramite code di messaggi.

- [Scalabilità automatica](https://msdn.microsoft.com/library/dn589774.aspx). Può essere possibile avviare e arrestare le istanze di un servizio consumer in base alla variazione della lunghezza della coda a cui le applicazioni inviano messaggi. La scalabilità automatica consente di mantenere la velocità effettiva durante i periodi di massima richiesta di elaborazione.

- [Compute Resource Consolidation pattern](./compute-resource-consolidation.md) (Modello di consolidamento delle risorse di calcolo). Può essere possibile consolidare più istanze di un servizio consumer in un singolo processo, per ridurre i costi e il sovraccarico di gestione. L'articolo Modello di consolidamento delle risorse di calcolo descrive i vantaggi e gli svantaggi di questo approccio.

- [Schema di livellamento del carico basato sulle code](./queue-based-load-leveling.md). Introdurre una coda di messaggi può aggiungere resilienza al sistema, consentendo alle istanze del servizio di gestire un'ampia gamma di volumi di richieste provenienti dalle istanze dell'applicazione. La coda di messaggi funge da buffer, livellando il carico. L'articolo Modello di livellamento del carico basato sulle code descrive in dettaglio i vantaggi e gli svantaggi di questo scenario.

- A questo modello è associata un'[applicazione di esempio](https://github.com/mspnp/cloud-design-patterns/tree/master/competing-consumers).
