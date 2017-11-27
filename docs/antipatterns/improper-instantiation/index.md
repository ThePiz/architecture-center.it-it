---
title: Antipattern di creazione di istanze non corretta
description: Evitare di creare continuamente nuove istanze di un oggetto che deve essere creato una sola volta e poi condiviso.
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: 3387eca45119b105b68cd60ab842d033df84727c
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="improper-instantiation-antipattern"></a>Antipattern di creazione di istanze non corretta

Continuando a creare nuove istanze di un oggetto che deve essere creato una sola volta e poi condiviso si rischia di compromettere le perstazioni. 

## <a name="problem-description"></a>Descrizione del problema

Molte librerie forniscono astrazioni di risorse esterne. Internamente, queste classi gestiscono in genere le proprie connessioni alla risorsa, agendo come broker che i client possono usare per accedere alla risorsa. Di seguito sono riportati alcuni esempi di classi broker pertinenti per le applicazioni Azure:

- `System.Net.Http.HttpClient`. Comunica con un servizio Web tramite HTTP.
- `Microsoft.ServiceBus.Messaging.QueueClient`. Invia e riceve messaggi da una coda del bus di servizio. 
- `Microsoft.Azure.Documents.Client.DocumentClient`. Si connette a un'istanza di database Cosmos
- `StackExchange.Redis.ConnectionMultiplexer`. Si connette a Redis, incluso Cache Redis di Azure.

Queste classi sono destinate a essere istanziate una sola volta e riutilizzate per tutta la durata di un'applicazione. Invece molti non sanno che queste classi devono essere acquisite solo quando è necessario e rilasciate rapidamente (quelle elencate di seguito sono librerie .NET, ma il modello non è unico di .NET).

L'esempio ASP.NET seguente crea un'istanza di `HttpClient` per comunicare con un servizio remoto. L'esempio completo è disponibile [qui][sample-app].

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

In un'applicazione Web questa tecnica non è scalabile. Viene creato un nuovo oggetto `HttpClient` per ogni richiesta dell'utente. In caso di carico pesante, il server Web può esaurire il numero di socket disponibile, causando errori `SocketException`.

Questo problema non è limitato alla classe `HttpClient`. Altre classi che eseguono il wrapping di risorse o sono costose da creare potrebbero causare problemi simili. L'esempio seguente crea un'istanza della classe `ExpensiveToCreateService`. In questo caso il problema non è necessariamente l'esaurimento dei socket, ma semplicemente il tempo necessario per la creazione di ogni istanza. La creazione ed eliminazione continua di istanze di questa classe può incidere negativamente sulla scalabilità del sistema.

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

## <a name="how-to-fix-the-problem"></a>Come risolvere il problema

Se la classe che esegue il wrapping della risorsa esterna è condivisibile e thread-safe, creare un'istanza singleton condivisa o un pool di istanze della classe riutilizzabili.

Nell'esempio riportato di seguito viene utilizzato un'istanza `HttpClient` statico, condividendo in questo modo la connessione tra tutte le richieste.

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

## <a name="considerations"></a>Considerazioni

- L'elemento principale di questo antipattern è la ripetuta creazione ed eliminazione di istanze di un oggetto *condivisibile*. Se una classe non è condivisibile (non è thread-safe), questo antipattern non è applicabile.

- Il tipo di risorsa condivisa può indicare se si deve usare un singleton o creare un pool. La classe `HttpClient` è progettata per essere condivisa anziché messa in pool. Altri oggetti potrebbero supportare il pool, consentendo al sistema di distribuire il carico di lavoro tra più istanze.

- Gli oggetti che si condividono tra più richieste *devono* essere thread-safe. La classe `HttpClient` è progettata per essere utilizzata in questo modo, ma altre classi potrebbero non supportare richieste simultanee: controllare la documentazione disponibile.

- Alcuni tipi di risorse sono scarse e non devono essere trattenute. Le connessioni di database sono un esempio. Trattenendo una connessione di database aperta che non è necessaria può impedire l'accesso al database da parte di altri utenti simultanei.

- In .NET Framework vengono creati più oggetti che consentono di stabilire connessioni a risorse esterne utilizzando metodi factory statici di altre classi che gestiscono queste connessioni. Questi oggetti factory devono essere salvati e riutilizzati, anziché eliminati e ricreati. Ad esempio, nel bus di servizio di Azure, l'oggetto `QueueClient` viene creato tramite un oggetto `MessagingFactory`. Internamente, `MessagingFactory` gestisce le connessioni. Per altre informazioni, vedere [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica del bus di servizio][service-bus-messaging].

## <a name="how-to-detect-the-problem"></a>Come rilevare il problema

Sintomi di questo problema includono la riduzione della velocità effettiva o l'aumento della frequenza degli errori, insieme a uno o più dei seguenti effetti: 

- Aumento delle eccezioni che indicano l'esaurimento delle risorse, ad esempio i socket, le connessioni al database, gli handle di file e così via. 
- Aumento dell'uso di memoria e del garbage collection.
- Aumento dell'attività di rete, disco o database.

Per provare a identificare questo problema è possibile eseguire la procedura seguente:

1. Eseguire il monitoraggio del processo per il sistema di produzione per identificare i punti associati a tempi di risposta più lunghi o arresti anomali del sistema causati da mancanza di risorse.
2. Esaminare i dati di telemetria acquisiti in questi punti per determinare quali operazioni potrebbero creare ed eliminare oggetti che consumano risorse.
3. Eseguire test di carico su ogni operazione sospetta, in un ambiente di test controllato anziché nel sistema di produzione.
4. Esaminare il codice sorgente ed esaminare come vengono gestiti gli oggetti broker.

Esaminare le tracce dello stack per le operazioni a esecuzione prolungata o che generano eccezioni quando il sistema è in condizioni di carico. Queste informazioni sono utili per identificare in che modo queste operazioni utilizzano le risorse. Le eccezioni possono aiutare a determinare se gli errori sono causati dall'esaurimento di risorse condivise. 

## <a name="example-diagnosis"></a>Diagnosi di esempio

Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.

### <a name="identify-points-of-slow-down-or-failure"></a>Individuare i punti di rallentamento o errore

La figura seguente mostra i risultati generati utilizzando l'[APM New Relic][new-relic], che mostra le operazioni che hanno tempi di risposta lunghi. In questo caso, vale la pena di analizzare ulteriormente il metodo `GetProductAsync` nel controller `NewHttpClientInstancePerRequest`. Si noti che il tasso di errore aumenta quando queste operazioni sono in esecuzione. 

![Il dashboard di monitoraggio di New Relic che mostra l'applicazione di esempio che crea una nuova istanza di un oggetto HttpClient per ogni richiesta][dashboard-new-HTTPClient-instance]

### <a name="examine-telemetry-data-and-find-correlations"></a>Esaminare i dati di telemetria e trovare le correlazioni

L'immagine successiva mostra i dati acquisiti utilizzando la profilatura dei thread, nello stesso periodo corrispondente all'immagine precedente. Il sistema impiega molto tempo per l'apertura delle connessioni socket e anche più tempo per chiuderle e gestire le eccezioni di socket.

![Il thread profiler di New Relic che mostra l'applicazione di esempio che crea una nuova istanza di un oggetto HttpClient per ogni richiesta][thread-profiler-new-HTTPClient-instance]

### <a name="performing-load-testing"></a>Eseguire i test di carico

Per simulare le operazioni tipiche che gli utenti potrebbero eseguire, utilizzare i test di carico. Ciò consente di identificare quali parti di un sistema subiscono un esaurimento delle risorse con carichi diversi. Eseguire questi test in un ambiente controllato anziché nel sistema di produzione. Il grafico seguente mostra la velocità effettiva delle richieste gestite dal controller `NewHttpClientInstancePerRequest` mentre il carico utente aumenta a 100 utenti simultanei.

![Velocità effettiva dell'applicazione di esempio che crea una nuova istanza di un oggetto HttpClient per ogni richiesta][throughput-new-HTTPClient-instance]

Inizialmente il volume di richieste gestite al secondo aumenta man mano che aumenta il carico di lavoro. Quando gli utenti sono circa 30, invece, il volume di richieste riuscite raggiunge un limite e il sistema inizia a generare eccezioni. Da quel punto in avanti, il volume delle eccezioni aumenta gradualmente con il carico utente. 

Il test di carico ha segnalato questi errori come errori HTTP 500 (server interno). L'esame dei dati di telemetria ha dimostrato che questi errori sono stati causati dall'esaurimento delle risorse di socket da parte del sistema, con la creazione di un numero sempre crescente di oggetti `HttpClient`.

Il grafico successivo mostra un test simile per un controller che crea l'oggetto personalizzato `ExpensiveToCreateService`.

![Velocità effettiva dell'applicazione di esempio che crea una nuova istanza di ExpensiveToCreateService per ogni richiesta][throughput-new-ExpensiveToCreateService-instance]

Questa volta il controller non genera alcuna eccezione, ma la velocità effettiva raggiunge ancora una soglia, mentre il tempo di risposta medio aumenta con fattore 20 (il grafico usa la scala logaritmica per la velocità effettiva e il tempo di risposta). I dati di telemetria hanno dimostrato che la creazione di nuove istanze del `ExpensiveToCreateService` è la causa principale del problema.

### <a name="implement-the-solution-and-verify-the-result"></a>Implementare la soluzione e verificare il risultato

Dopo aver cambiato il metodo `GetProductAsync` in modo che venga condivisa una singola istanza `HttpClient`, un secondo test di carico ha dimostrato prestazioni migliorate. Non sono stati segnalati errori e il sistema è stato in grado di gestire un incremento del carico fino a 500 richieste al secondo. Il tempo medio di risposta si è ridotto a metà rispetto al test precedente.

![Velocità effettiva dell'applicazione di esempio che riutilizza la stessa istanza di un oggetto HttpClient per ogni richiesta][throughput-single-HTTPClient-instance]

Per il confronto, l'immagine seguente mostra i dati di telemetria di monitoraggio dello stack. Questa volta il sistema passa la maggior parte del tempo a eseguire il lavoro effettivo, anziché ad aprire e chiudere socket.

![Il thread profiler di New Relic che mostra l'applicazione di esempio che crea una singola istanza di un oggetto HttpClient per tutte le richieste][thread-profiler-single-HTTPClient-instance]

Il grafico successivo mostra un test di carico simile utilizzando un'istanza condivisa dell'oggetto `ExpensiveToCreateService`. Anche questa volta il volume di richieste gestite aumenta in linea con il carico utente, mentre il tempo di risposta medio rimane basso. 

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
