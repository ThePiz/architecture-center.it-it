---
title: Indicazioni specifiche del servizio per la ripetizione di tentativi
description: Indicazioni specifiche del servizio per impostare il meccanismo di ripetizione dei tentativi.
author: dragon119
ms.date: 07/13/2016
pnp.series.title: Best Practices
ms.openlocfilehash: 790c933458717f2cb4cde0741b1d22f6ae89cc39
ms.sourcegitcommit: 8ec48a0e2c080c9e2e0abbfdbc463622b28de2f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "43016062"
---
# <a name="retry-guidance-for-specific-services"></a>Materiale sussidiario su come eseguire nuovi tentativi per servizi specifici

La maggior parte dei servizi di Azure e degli SDK client include un meccanismo di ripetizione dei tentativi. Ogni servizio, tuttavia, presenta caratteristiche e requisiti diversi e, quindi, ogni meccanismo di ripetizione dei tentativi è ottimizzato per il servizio specifico in cui è incorporato. Questo articolo riepiloga le caratteristiche dei meccanismi di ripetizione dei tentativi relativi alla maggior parte dei servizi di Azure e include utili informazioni per usare, adattare o estendere il meccanismo di ripetizione dei tentativi per ogni servizio.

Per indicazioni generali sulla gestione degli errori temporanei e sulla ripetizione dei tentativi di eseguire connessioni e operazioni su servizi e risorse, vedere [Indicazioni generali per la ripetizione di tentativi](./transient-faults.md).

La tabella seguente riepiloga le caratteristiche dei meccanismi di ripetizione dei tentativi associati ai servizi di Azure descritti in questo articolo.

| **Servizio** | **Funzionalità per la ripetizione di tentativi** | **Configurazione dei criteri** | **Ambito** | **Funzionalità di telemetria** |
| --- | --- | --- | --- | --- |
| **[Azure Active Directory](#azure-active-directory)** |Nativo nella libreria ADAL |Incorporato nella libreria ADAL |Interno |Nessuna |
| **[Cosmos DB](#cosmos-db)** |Native nel servizio |Non configurabili |Globale |TraceSource |
| **Data Lake Store** |Native nel client |Non configurabili |Singole operazioni |Nessuna |
| **[Hub eventi](#event-hubs)** |Native nel client |Programmatica |Client |Nessuna |
| **[Hub IoT](#iot-hub)** |Native nell'SDK client |Programmatica |Client |Nessuna |
| **[Cache Redis](#azure-redis-cache)** |Native nel client |Programmatica |Client |TextWriter |
| **[Ricerca](#azure-search)** |Native nel client |Programmatica |Client |ETW o personalizzato |
| **[Bus di servizio](#service-bus)** |Native nel client |Programmatica |Gestore dello spazio dei nomi, factory di messaggistica e client |ETW |
| **[Service Fabric](#service-fabric)** |Native nel client |Programmatica |Client |Nessuna | 
| **[Database SQL con ADO.NET](#sql-database-using-adonet)** |[Polly](#transient-fault-handling-with-polly) |Dichiarativa e a livello di codice |Singole istruzioni o blocchi di codice |Personalizzate |
| **[Database SQL con Entity Framework](#sql-database-using-entity-framework-6)** |Native nel client |Programmatica |Globale per AppDomain |Nessuna |
| **[Database SQL con Entity Framework Core](#sql-database-using-entity-framework-core)** |Native nel client |Programmatica |Globale per AppDomain |Nessuna |
| **[Archiviazione](#azure-storage)** |Native nel client |Programmatica |Client e singole operazioni |TraceSource |

> [!NOTE]
> Per la maggior parte dei meccanismi di ripetizione dei tentativi predefiniti di Azure, non è attualmente possibile applicare criteri di ripetizione differenti per diversi tipi di errori o eccezioni. È necessario configurare un criterio che fornisca prestazioni e disponibilità medie ottimali. I criteri possono essere successivamente ottimizzati analizzando i file di log per determinare i tipi di errori temporanei che si sono verificati. 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory è una soluzione cloud completa per la gestione delle identità e dell'accesso che combina servizi directory di importanza strategica, governance avanzata delle identità e gestione della sicurezza e dell'accesso alle applicazioni. Azure AD offre inoltre agli sviluppatori una piattaforma di gestione delle identità per consentire il controllo dell'accesso alle applicazioni in base a regole e criteri centralizzati.

> [!NOTE]
> Per informazioni sulla ripetizione dei tentativi negli endpoint dell'identità del servizio gestita, vedere [Come usare un'identità del servizio gestita di una macchina virtuale di Azure per l'acquisizione di token](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
Nella libreria di autenticazione di Active Directory è previsto un meccanismo di ripetizione dei tentativi incorporato per Azure Active Directory. Per evitare blocchi imprevisti, è consigliabile che le librerie di terze parti e il codice dell'applicazione **non** ripetano i tentativi di connessione non riusciti, ma consentano ad ADAL di gestire i tentativi. 

### <a name="retry-usage-guidance"></a>Linee guida sull'uso dei criteri di ripetizione dei tentativi
Quando si usa Azure Active Directory, tenere presente le linee guida seguenti:

* Quando possibile, usare la libreria ADAL e il supporto incorporato per le ripetzioni dei tentativi.
* Se si usa l'API REST per Azure Active Directory, ripetere l'operazione se il codice di risultato è 429 (Troppe richieste) oppure un errore compreso nell'intervallo 5xx. Non ripetere nuovi tentativi per altri tipi di errore.
* Negli scenari in batch di Azure Active Directory è opportuno usare criteri di backoff esponenziale.

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.

| **Contesto** | **Destinazione di esempio E2E<br />latenza massima** | **Strategia di ripetizione dei tentativi** | **Impostazioni** | **Valori** | **Funzionamento** |
| --- | --- | --- | --- | --- | --- |
| Interattivo, interfaccia utente<br />o in primo piano |2 secondi |FixedInterval |Numero tentativi<br />Intervallo tentativi<br />Primo tentativo rapido |3<br />500 ms<br />true |Tentativo di 1 - intervallo di 0 sec<br />Tentativo 2 - intervallo di 500 ms<br />Tentativo 3 - intervallo di 500 ms |
| Background<br />o batch |60 secondi |ExponentialBackoff |Numero tentativi<br />Interruzione temporanea minima<br />Interruzione temporanea massima<br />Interruzione temporanea delta<br />Primo tentativo rapido |5<br />0 secondi<br />60 secondi<br />2 secondi<br />false |Tentativo di 1 - intervallo di 0 sec<br />Tentativo 2 - intervallo di ~2 sec<br />Tentativo 3 - intervallo di ~6 sec<br />Tentativo 4 - intervallo di ~14 sec<br />Tentativo 5 - intervallo di 30 sec |

### <a name="more-information"></a>Altre informazioni
* [Librerie di autenticazione di Azure Active Directory][adal]

## <a name="cosmos-db"></a>Cosmos DB

Cosmos DB è un database multi-modello completamente gestito che supporta i dati JSON senza schema. Offre prestazioni affidabili e configurabili, consente l'elaborazione transazionale JavaScript nativa e, grazie alla scalabilità elastica, è ottimizzato per il cloud.

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
La classe `DocumentClient` esegue nuovi tentativi in automatico. Per impostare il numero di tentativi e il tempo di attesa massimo, configurare [ConnectionPolicy.RetryOptions]. Le eccezioni generate dal client escludono il criterio di ripetizione oppure non sono errori temporanei.

Se Cosmos DB limita il client, viene restituito un errore HTTP 429. Verificare il codice di stato in `DocumentClientException`.

### <a name="policy-configuration"></a>Configurazione dei criteri
La tabella seguente mostra le impostazioni predefinite per la classe `RetryOptions`.

| Impostazione | Valore predefinito | DESCRIZIONE |
| --- | --- | --- |
| MaxRetryAttemptsOnThrottledRequests |9 |Il numero massimo di tentativi se la richiesta ha esito negativo a causa della limitazione applicata da Cosmos DB alla velocità del client. |
| MaxRetryWaitTimeInSeconds |30 |Il tempo massimo per i tentativi in secondi. |

### <a name="example"></a>Esempio
```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); ;
var options = client.ConnectionPolicy.RetryOptions;
options.MaxRetryAttemptsOnThrottledRequests = 5;
options.MaxRetryWaitTimeInSeconds = 15;
```

### <a name="telemetry"></a>Telemetria
I tentativi vengono registrati come messaggi di traccia non strutturati tramite un oggetto **TraceSource**in .NET. È necessario configurare un oggetto **TraceListener** per acquisire gli eventi e scriverli in un log di destinazione appropriato.

Ad esempio, se si aggiunge il seguente codice al file App.config in uso, verranno generate tracce in un file di testo nello stesso percorso dell'eseguibile:

```xml
<configuration>
  <system.diagnostics>
    <switches>
      <add name="SourceSwitch" value="Verbose"/>
    </switches>
    <sources>
      <source name="DocDBTrace" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" >
        <listeners>
          <add name="MyTextListener" type="System.Diagnostics.TextWriterTraceListener" traceOutputOptions="DateTime,ProcessId,ThreadId" initializeData="CosmosDBTrace.txt"></add>
        </listeners>
      </source>
    </sources>
  </system.diagnostics>
</configuration>
```

## <a name="event-hubs"></a>Hub eventi

Hub eventi di Azure è un servizio di inserimento di dati di telemetria su vastissima scala che raccoglie, trasforma e archivia milioni di eventi.

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
Il comportamento di ripetizione dei tentativi nella libreria client dell'Hub eventi di Azure è controllato dalla proprietà `RetryPolicy` nella classe `EventHubClient`. I criteri predefiniti eseguono nuovi tentativi con backoff esponenziale quando Hub eventi di Azure restituisce `EventHubsException` o `OperationCanceledException` temporanei.

### <a name="example"></a>Esempio
```csharp
EventHubClient client = EventHubClient.CreateFromConnectionString("[event_hub_connection_string]");
client.RetryPolicy = RetryPolicy.Default;
```

### <a name="more-information"></a>Altre informazioni
[Libreria client di .NET standard per gli Hub eventi di Azure](https://github.com/Azure/azure-event-hubs-dotnet)

## <a name="iot-hub"></a>Hub IoT

Hub IoT di Azure è un servizio per la connessione, il monitoraggio e la gestione di dispositivi per lo sviluppo di applicazioni Internet delle cose (IoT).

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi

Azure IoT SDK per dispositivi è in grado di rilevare errori nella rete, nel protocollo o nell'applicazione. In base al tipo di errore, l'SDK verifica se è necessario eseguire un nuovo tentativo. Se l'errore è *reversibile*, l'SDK avvia un nuovo tentativo usando i criteri di ripetizione configurati.

I criteri di ripetizione predefiniti prevedono il *backoff esponenziale con instabilità casuale*, ma possono essere configurati.

### <a name="policy-configuration"></a>Configurazione dei criteri

La configurazione dei criteri varia a seconda del linguaggio. Per maggiori dettagli, vedere la [configurazione dei criteri di ripetizione per l'hub IoT](/azure/iot-hub/iot-hub-reliability-features-in-sdks#retry-policy-apis).

### <a name="more-information"></a>Altre informazioni

* [Criteri di ripetizione per l'hub IoT](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
* [Risoluzione dei problemi di disconnessione dei dispositivi hub IoT](/azure/iot-hub/iot-hub-troubleshoot-connectivity)

## <a name="azure-redis-cache"></a>Cache Redis di Azure
Cache Redis di Azure è un servizio cache a bassa latenza e di rapido accesso ai dati basato sulla nota Cache Redis open source. È un servizio protetto, gestito da Microsoft e accessibile da qualsiasi applicazione in Azure.

Le indicazioni fornite in questa sezione presuppongono che si usi il client StackExchange.Redis per accedere alla cache. Nel [sito Web di Redis](http://redis.io/clients)è disponibile un elenco di altri client idonei, a cui possono essere associati vari meccanismi di ripetizione dei tentativi.

Il client StackExchange.Redis usa il multiplexing tramite un'unica connessione. È consigliabile quindi creare un'istanza del client all'avvio dell'applicazione e usarla per tutte le operazioni eseguite sulla cache. In questo modo, la connessione alla cache viene eseguita una sola volta e tutte le indicazioni fornite in questa sezione fanno riferimento ai criteri di ripetizione dei tentativi definiti per la connessione iniziale e non per ogni operazione che accede alla cache.

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
Il client StackExchange.Redis usa una classe di gestione della connessione configurata tramite un set di opzioni, che include:

- **ConnectRetry**. Il numero di volte in cui una connessione non riuscita per la cache verrà ritentata.
- **ReconnectRetryPolicy**. La strategia di ripetizione dei tentativi da usare.
- **ConnectTimeout**. Il tempo di attesa massimo in millisecondi.

### <a name="policy-configuration"></a>Configurazione dei criteri
I criteri di ripetizione dei tentativi vengono configurati a livello di codice impostando le opzioni per il client prima di connettersi alla cache. Questa operazione può essere eseguita creando un'istanza della classe **ConfigurationOptions**, popolandone le proprietà e passandola al metodo **Connect**.

Le classi incorporate supportano i ritardi lineari, ovvero il tempo d'intervallo costante, e i backoff esponenziali con intervalli di ripetizione dei tentativi casuali. È anche possibile creare un criterio di ripetizione dei tentativi personalizzato implementando l'interfaccia **IReconnectRetryPolicy**.

L'esempio seguente consente di configurare una strategia di ripetizione dei tentativi con backoff esponenziale.

```csharp
var deltaBackOffInMilliseconds = TimeSpan.FromSeconds(5).Milliseconds;
var maxDeltaBackOffInMilliseconds = TimeSpan.FromSeconds(20).Milliseconds;
var options = new ConfigurationOptions
{
    EndPoints = {"localhost"},
    ConnectRetry = 3,
    ReconnectRetryPolicy = new ExponentialRetry(deltaBackOffInMilliseconds, maxDeltaBackOffInMilliseconds),
    ConnectTimeout = 2000
};
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

In alternativa, è possibile specificare le opzioni sotto forma di stringa, per poi passarla al metodo **Connect** . Si noti che la proprietà **ReconnectRetryPolicy** non può essere impostata in questo modo, ma solo tramite codice.

```csharp
var options = "localhost,connectRetry=3,connectTimeout=2000";
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

È anche possibile specificare le opzioni direttamente durante la connessione alla cache.

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

Per altre informazioni, vedere [Configurazione di Stack Exchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration) nella documentazione di StackExchange.Redis.

La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.

| **Contesto** | **Impostazione** | **Valore predefinito**<br />(v 1.2.2) | **Significato** |
| --- | --- | --- | --- |
| Opzioni di configurazione |ConnectRetry<br /><br />ConnectTimeout<br /><br />SyncTimeout<br /><br />ReconnectRetryPolicy |3<br /><br />Massimo 5000 ms più SyncTimeout<br />1000<br /><br />LinearRetry 5.000 ms |Numero di nuovi tentativi di connessione durante l'operazione di connessione iniziale.<br />Timeout (ms) per operazioni di connessione. Non un intervallo tra i tentativi.<br />Tempo (ms) concesso per consentire operazioni sincrone.<br /><br />Ripetizione dei tentativi ogni 5.000 ms.|

> [!NOTE]
> Per le operazioni sincrone, è possibile aggiungere `SyncTimeout` alla latenza end-to-end, ma impostare un valore troppo basso può causare timeout eccessivi. Vedere [Come risolvere i problemi di Cache Redis di Azure][redis-cache-troubleshoot]. In generale, evitare di usare operazioni sincrone, usare invece operazioni asincrone. Per altre informazioni, vedere [Pipeline e multiplexer](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).
>
>

### <a name="retry-usage-guidance"></a>Linee guida sull'uso dei criteri di ripetizione dei tentativi
Quando si usa Cache Redis di Azure, tenere presente le linee guida seguenti:

* Il client StackExchange Redis gestisce autonomamente i propri tentativi, ma solo se viene stabilita una connessione alla cache al primo avvio dell'applicazione. È possibile configurare il timeout di connessione, il numero di ripetizione di tentativi e l'intervallo di tempo tra i tentativi per stabilire la connessione, ma i criteri di ripetizione non si applicano alle operazioni eseguite sulla cache.
* Anziché ricorrere a un numero elevato di tentativi, valutare la possibilità di eseguire il fallback accedendo alla sorgente originale dei dati.

### <a name="telemetry"></a>Telemetria
È possibile raccogliere informazioni sulle connessioni, ma su non altre operazioni, usando un **TextWriter**.

```csharp
var writer = new StringWriter();
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Seguito è riportato un esempio di output generato.

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

### <a name="examples"></a>Esempi
L'esempio di codice seguente consente di configurare un intervallo costante, ovvero lineare, tra le ripetizioni di tentativi durante l'inizializzazione del client StackExchange.Redis. Questo esempio illustra come impostare la configurazione usando un'istanza **ConfigurationOptions**.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
    class CacheRedisCodeSamples
    {
        public async static Task Samples()
        {
            var writer = new StringWriter();
            {
                try
                {
                    var retryTimeInMilliseconds = TimeSpan.FromSeconds(4).Milliseconds; // delay between retries

                    // Using object-based configuration.
                    var options = new ConfigurationOptions
                                        {
                                            EndPoints = { "localhost" },
                                            ConnectRetry = 3,
                                            ReconnectRetryPolicy = new LinearRetry(retryTimeInMilliseconds)
                                        };
                    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

                    // Store a reference to the multiplexer for use in the application.
                }
                catch
                {
                    Console.WriteLine(writer.ToString());
                    throw;
                }
            }
        }
    }
}
```

L'esempio successivo imposta la configurazione specificando le opzioni sotto forma di stringa. Il timeout di connessione è l'intervallo di tempo massimo di attesa per la connessione alla cache, non l'intervallo tra i tentativi. Si noti che la proprietà **ReconnectRetryPolicy** può essere impostata solo dal codice.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
    class CacheRedisCodeSamples
    {
        public async static Task Samples()
        {
            var writer = new StringWriter();
            {
                try
                {
                    // Using string-based configuration.
                    var options = "localhost,connectRetry=3,connectTimeout=2000";
                    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

                    // Store a reference to the multiplexer for use in the application.
                }
                catch
                {
                    Console.WriteLine(writer.ToString());
                    throw;
                }
            }
        }
    }
}
```

Per altri esempi, vedere la sezione relativa alla [configurazione](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) sul sito Web di progetto.

### <a name="more-information"></a>Altre informazioni
* [Sito Web di Redis](http://redis.io/)

## <a name="azure-search"></a>Ricerca di Azure
Lo strumento Ricerca di Azure può essere usato per aggiungere sofisticate e avanzate funzionalità di ricerca in un sito Web o un'applicazione, ottimizzare i risultati di ricerca in modo semplice e rapido e creare avanzati modelli di classificazione.

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
Il comportamento di ripetizione dei tentativi nell'SDK di Ricerca di Azure è controllato dal metodo `SetRetryPolicy` nelle classi [SearchServiceClient] e [SearchIndexClient]. I criteri predefiniti eseguono nuovi tentativi con backoff esponenziale quando Ricerca di Azure restituisce una risposta 5xx o 408 (Timeout della richiesta).

### <a name="telemetry"></a>Telemetria
Tracciare con ETW o registrando un provider di traccia personalizzato. Per altre informazioni, consultare la [documentazione di AutoRest][autorest].

## <a name="service-bus"></a>Bus di servizio
Il bus di servizio è una piattaforma di messaggistica basata sul cloud che offre una funzione di scambio dei messaggi di tipo "loosely coupled" con alti livelli di scalabilità e resilienza per i componenti di un'applicazione (ospitata nel cloud o in locale).

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
Il bus di servizio implementa la ripetizione dei tentativi usando implementazioni della classe di base [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) . Tutti i client del bus di servizio espongono una proprietà **RetryPolicy** che può essere impostata in una delle implementazioni della classe di base **RetryPolicy**. Le implementazioni predefinite sono:

* La [classe RetryExponential](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx). Espone proprietà che controllano l'intervallo di backoff, il numero di tentativi e la proprietà **TerminationTimeBuffer** usata per limitare il tempo complessivo per il completamento dell'operazione.
* La [classe NoRetry](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx). Viene usata quando non è necessario eseguire nuovi tentativi al livello dell'API del bus di servizio, ad esempio quando i nuovi tentativi vengono gestiti da un altro processo nell'ambito di un'operazione in batch o composta da più passaggi.

Le azioni del bus di servizio possono restituire una serie di eccezioni, elencate in [Eccezioni di messaggistica del bus di servizio](/azure/service-bus-messaging/service-bus-messaging-exceptions). L'elenco fornisce informazioni su quali delle eccezioni indicano che è opportuno ripetere l'operazione. **ServerBusyException** , ad esempio, indica che il client deve attendere un determinato intervallo di tempo, quindi ripetere l'operazione. Un'occorrenza di **ServerBusyException** induce inoltre il bus di servizio a passare a una modalità diversa, in cui viene aggiunto un ulteriore intervallo di 10 secondi all'intervallo tra i tentativi elaborato. Questa modalità viene reimpostata dopo un breve intervallo di tempo.

Le eccezioni restituite dal bus di servizio espongono la proprietà **IsTransient** che indica se il client deve ripetere l'operazione. I criteri **RetryExponential** incorporati si basano sulla proprietà **IsTransient** della classe **MessagingException**, che costituisce la classe di base per tutte le eccezioni del bus di servizio. Se si creano implementazioni personalizzate della classe di base **RetryPolicy**, è possibile usare una combinazione del tipo di eccezione e della proprietà **IsTransient** per ottenere un controllo più preciso sulle azioni di ripetizione dei tentativi. Se si identifica un'eccezione di tipo **QuotaExceededException** , ad esempio, è possibile intraprendere le azioni necessarie per esaurire la coda prima di riprovare a inviare un messaggio.

### <a name="policy-configuration"></a>Configurazione dei criteri
I criteri di ripetizione dei tentativi vengono impostati a livello di codice e possono essere impostati come criteri predefiniti per **NamespaceManager** e **MessagingFactory** oppure impostati singolarmente per ogni client di messaggistica. Per impostare criteri di ripetizione dei tentativi predefiniti per una sessione di messaggistica, impostare la proprietà **RetryPolicy** di **NamespaceManager**.

```csharp
namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                                maxBackoff: TimeSpan.FromSeconds(30),
                                                                maxRetryCount: 3);
```

Per impostare criteri di ripetizione dei tentativi predefiniti per tutti i client da una factory di messaggistica, impostare la proprietà **RetryPolicy** di **MessagingFactory**.

```csharp
messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                    maxBackoff: TimeSpan.FromSeconds(30),
                                                    maxRetryCount: 3);
```

Per impostare criteri di ripetizione dei tentativi per un client di messaggistica o per ignorare i criteri predefiniti, impostarne la proprietà **RetryPolicy** usando un'istanza della classe di criteri necessaria:

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                            maxBackoff: TimeSpan.FromSeconds(30),
                                            maxRetryCount: 3);
```

I criteri di ripetizione dei tentativi non possono essere impostati a livello di singola operazione. Questo vale per tutte le operazioni relative al client di messaggistica.
La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.

| Impostazione | Valore predefinito | Significato |
|---------|---------------|---------|
| Criterio | Esponenziali | Backoff esponenziale. |
| MinimalBackoff | 0 | Intervallo minimo di backoff. Viene aggiunto all'intervallo per i tentativi calcolato a partire da deltaBackoff. |
| MaximumBackoff | 30 secondi | Intervallo massimo di backoff. Si usa MaximumBackoff se l'intervallo tra i tentativi calcolato è maggiore di MaxBackoff. |
| DeltaBackoff | 3 secondi | Intervallo di backoff tra i tentativi. Per i successivi tentativi verranno utilizzati i multipli di questi timespan. |
| TimeBuffer | 5 secondi | Il buffer temporale associato al tentativo. I tentativi verranno abbandonati se il tempo rimanente è minore del valore di TimeBuffer. |
| MaxRetryCount | 10 | Numero massimo di tentativi. |
| ServerBusyBaseSleepTime | 10 secondi | Se l'ultima eccezione riscontrata è **ServerBusyException**, questo valore verrà aggiunto all'intervallo di ripetizione dei tentativi calcolato. Questo valore non può essere modificato. |

### <a name="retry-usage-guidance"></a>Linee guida sull'uso dei criteri di ripetizione dei tentativi
Quando si usa il bus di servizio, tenere presente le linee guida seguenti:

* Se si sceglie l'implementazione **RetryExponential** incorporata, non implementare un'operazione di fallback poiché i criteri reagiscono alle eccezioni di tipo "server occupato" e viene automaticamente attivata una modalità di ripetizione dei tentativi appropriata al nuovo scenario.
* Il bus di servizio supporta una funzionalità relativa agli spazi dei nomi associati che implementa il failover automatico su una coda di backup di uno spazio dei nomi diverso in caso di esito negativo della coda nello spazio dei nomi primario. I messaggi ricevuti dalla coda secondaria possono essere nuovamente inviati alla coda primaria nel momento in cui viene ripristinata. Questa funzionalità consente di risolvere eventuali problemi temporanei. Per altre informazioni, vedere [Modelli di messaggistica asincrona e disponibilità elevata](http://msdn.microsoft.com/library/azure/dn292562.aspx).

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.

| Context | Latenza massima di esempio | Criteri di ripetizione | Impostazioni | Funzionamento |
|---------|---------|---------|---------|---------|
| Interattivo, interfaccia utente o in primo piano | 2 secondi*  | Esponenziali | MinimumBackoff = 0 <br/> MaximumBackoff = 30 sec <br/> DeltaBackoff = 300 msec <br/> TimeBuffer = 300 msec <br/> MaxRetryCount = 2 | Tentativo 1: intervallo di 0 sec <br/> Tentativo 2: intervallo di ~300 msec <br/> Tentativo 3: intervallo di ~900 msec |
| Background o batch | 30 secondi | Esponenziali | MinimumBackoff = 1 <br/> MaximumBackoff = 30 sec <br/> DeltaBackoff = 1,75 sec <br/> TimeBuffer = 5 sec <br/> MaxRetryCount = 3 | Tentativo 1: intervallo di ~1 sec <br/> Tentativo 2: intervallo di ~3 sec <br/> Tentativo 3: intervallo di ~6 msec <br/> Tentativo 4: intervallo di ~13 msec |

\* Non è incluso l'ulteriore intervallo aggiunto se viene restituita una risposta di server occupato.

### <a name="telemetry"></a>Telemetria
Il bus di servizio registra i tentativi come eventi ETW tramite un oggetto **EventSource**. È necessario associare un **EventListener** all'origine eventi per acquisire gli eventi e visualizzarli nel visualizzatore delle prestazioni o scriverli in un log di destinazione appropriato. Gli eventi di ripetizione dei tentativi sono caratterizzati dal formato seguente:

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### <a name="examples"></a>Esempi
L'esempio di codice seguente illustra come impostare i criteri di ripetizione dei tentativi per:

* Un gestore dello spazio dei nomi. I criteri si applicano a tutte le operazioni eseguite sul gestore e non possono essere sostituiti a favore di singole operazioni.
* Una factory di messaggistica. I criteri si applicano a tutti i client creati dalla factory e non possono essere sostituiti durante la creazione di client individuali.
* Un singolo client di messaggistica. Dopo aver creato un client, è possibile impostarne i criteri di ripetizione dei tentativi. I criteri di applicano a tutte le operazioni eseguite sul client.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
    class ServiceBusCodeSamples
    {
        private const string connectionString =
            @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
                SharedAccessKeyName=RootManageSharedAccessKey;
                SharedAccessKey=C99..........Mk=";

        public async static Task Samples()
        {
            const string QueueName = "TestQueue";

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

            var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

            // The namespace manager will have a default exponential policy with 10 retry attempts
            // and a 3 second delay delta.
            // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
            // with an extra 10 sec added when receiving a ServiceBusyException.

            {
                // Set different values for the retry policy, used for all operations on the namespace manager.
                namespaceManager.Settings.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(0),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);

                // Policies cannot be specified on a per-operation basis.
                if (!await namespaceManager.QueueExistsAsync(QueueName))
                {
                    await namespaceManager.CreateQueueAsync(QueueName);
                }
            }

            var messagingFactory = MessagingFactory.Create(
                namespaceManager.Address, namespaceManager.Settings.TokenProvider);
            // The messaging factory will have a default exponential policy with 10 retry attempts
            // and a 3 second delay delta.
            // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
            // with an extra 10 sec added when receiving a ServiceBusyException.

            {
                // Set different values for the retry policy, used for clients created from it.
                messagingFactory.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(1),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);


                // Policies cannot be specified on a per-operation basis.
                var session = await messagingFactory.AcceptMessageSessionAsync();
            }

            {
                var client = messagingFactory.CreateQueueClient(QueueName);
                // The client inherits the policy from the factory that created it.


                // Set different values for the retry policy on the client.
                client.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(0.1),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);

                // Policies cannot be specified on a per-operation basis.
                var session = await client.AcceptMessageSessionAsync();
            }
        }
    }
}
```

### <a name="more-information"></a>Altre informazioni
* [Modelli di messaggistica asincrona e disponibilità elevata.](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## <a name="service-fabric"></a>Service Fabric

La distribuzione di servizi affidabili in un cluster di Service Fabric protegge dalla maggior parte degli errori temporanei potenziali descritti in questo articolo. Tuttavia, alcuni errori temporanei sono comunque possibili. Ad esempio, il servizio di denominazione potrebbe essere all'interno di una modifica di routing quando rileva una richiesta, provocando la generazione di un'eccezione. Se la stessa richiesta arriva 100 millisecondi dopo, verrà probabilmente completata.

Service Fabric gestisce internamente questo tipo di errori temporanei. È possibile configurare alcune impostazioni tramite la classe `OperationRetrySettings` durante la configurazione dei servizi.  Il codice seguente mostra un esempio. Nella maggior parte dei casi ciò non dovrebbe essere necessario e le impostazioni predefinite sono adeguate.

```csharp
FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
{
    OperationTimeout = TimeSpan.FromSeconds(30)
};

var retrySettings = new OperationRetrySettings(TimeSpan.FromSeconds(15), TimeSpan.FromSeconds(1), 5);

var clientFactory = new FabricTransportServiceRemotingClientFactory(transportSettings);

var serviceProxyFactory = new ServiceProxyFactory((c) => clientFactory, retrySettings);

var client = serviceProxyFactory.CreateServiceProxy<ISomeService>(
    new Uri("fabric:/SomeApp/SomeStatefulReliableService"),
    new ServicePartitionKey(0));
```

### <a name="more-information"></a>Altre informazioni

* [Remote Exception Handling](https://github.com/Microsoft/azure-docs/blob/master/articles/service-fabric/service-fabric-reliable-services-communication-remoting.md#remoting-exception-handling) (Gestione delle eccezioni da remoto)

## <a name="sql-database-using-adonet"></a>Database SQL con ADO.NET
Il database SQL, in questo caso, è un database SQL ospitato, di varie dimensioni, disponibile come servizio standard (condiviso) o premium (non condiviso).

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
Il database SQL non offre il supporto incorporato per la ripetizione dei tentativi quando si accede ad esso con ADO.NET. Per determinare il motivo per cui una richiesta ha avuto esito negativo, è possibile usare i codici restituiti dalle richieste. Per altre informazioni sulla limitazione delle richieste del database SQL, vedere [Limiti delle risorse del database SQL di Azure](/azure/sql-database/sql-database-resource-limits). Per un elenco dei codici di errore pertinenti, vedere [Codici di errore SQL per le applicazioni client del database SQL](/azure/sql-database/sql-database-develop-error-messages).

È possibile usare la libreria Polly per implementare la ripetizione di tentativi per il database SQL. Vedere [Gestione degli errori temporanei con Polly](#transient-fault-handling-with-polly).

### <a name="retry-usage-guidance"></a>Linee guida sull'uso dei criteri di ripetizione dei tentativi
Quando si accede al database SQL con ADO.NET, tenere presente le linee guida seguenti:

* Scegliere il tipo di servizio appropriato (condiviso o premium). Un'istanza condivisa può subire limitazioni o ritardi di connessione maggiori rispetto alla media perché può essere usata da altri tenant del server condiviso. Se sono necessarie prestazioni più prevedibili o affidabili operazioni di bassa latenza, valutare la possibilità di scegliere il tipo servizio premium.
* Assicurarsi di eseguire i nuovi tentativi al livello o nell'ambito appropriato per evitare operazioni non idempotenti che possono causare incoerenze nei dati. In teoria, tutte le operazioni dovrebbero essere idempotenti in modo da poter essere ripetute senza causare incoerenze. Se questo non avviene, i nuovi tentativi devono essere eseguiti a un livello o in un ambito che consenta l'annullamento di tutte le modifiche correlate in caso di esito negativo di un'operazione, ad esempio un ambito transazionale. Per altre informazioni, vedere l'articolo sul [livello di accesso ai dati di Cloud Service Fundamentals e la gestione degli errori temporanei](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).
* Non è consigliabile una strategia basata su intervalli fissi per il database SQL di Azure, se non negli scenari interattivi in cui vengono eseguiti solo alcuni tentativi a intervalli molto brevi. In alternativa, valutare la possibilità di usare una strategia di backoff esponenziale per la maggior parte degli scenari.
* Quando si definiscono le connessioni, scegliere un valore appropriato per i timeout di connessione e dei comandi. Un timeout troppo breve può comportare errori di connessione prematuri quando il database è occupato, mentre un timeout troppo lungo può impedire che la logica di ripetizione dei tentativi funzioni correttamente, poiché l'intervallo di attesa prima che venga rilevata una connessione non riuscita è troppo lungo. Il valore di timeout è un componente della latenza end-to-end; per ogni nuovo tentativo, viene aggiunto all'intervallo tra i tentativi specificato nei criteri di ripetizione dei tentativi.
* Chiudere la connessione dopo un certo numero di nuovi tentativi, anche se si usa una logica di ripetizione dei tentativi con backoff esponenziale, e ripetere l'operazione su una nuova connessione. Se si ripete più volte la stessa operazione su un'unica connessione, infatti, è possibile che si verifichino problemi di connessione. Per altre informazioni su questa tecnica, vedere l'articolo sul [livello di accesso ai dati di Cloud Service Fundamentals e la gestione degli errori temporanei](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).
* Se si usa il pool di connessioni (impostazione predefinita), è probabile che venga scelta la stessa connessione, anche dopo aver chiuso e riaperto una connessione. Se si verifica questo problema, è possibile risolverlo chiamando il metodo **ClearPool** della classe **SqlConnection** per contrassegnare la connessione come non riutilizzabile. Questo metodo, tuttavia, deve essere adottato solo dopo vari tentativi di connessione non riusciti e solo quando è presente la classe di errori temporanei, come i timeout SQL (codice di errore -2), specifica delle connessioni difettose.
* Se il codice di accesso ai dati usa transazioni avviate come istanze **TransactionScope** , la logica di ripetizione dei tentativi deve riaprire la connessione e avviare un nuovo ambito della transazione. Per questo motivo, il blocco di codice non irreversibile deve interessare l'intero ambito della transazione.

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.

| **Contesto** | **Destinazione di esempio E2E<br />latenza massima** | **Strategia di ripetizione dei tentativi** | **Impostazioni** | **Valori** | **Funzionamento** |
| --- | --- | --- | --- | --- | --- |
| Interattivo, interfaccia utente<br />o in primo piano |2 secondi |FixedInterval |Numero tentativi<br />Intervallo tentativi<br />Primo tentativo rapido |3<br />500 ms<br />true |Tentativo di 1 - intervallo di 0 sec<br />Tentativo 2 - intervallo di 500 ms<br />Tentativo 3 - intervallo di 500 ms |
| Background<br />o batch |30 secondi |ExponentialBackoff |Numero tentativi<br />Interruzione temporanea minima<br />Interruzione temporanea massima<br />Interruzione temporanea delta<br />Primo tentativo rapido |5<br />0 secondi<br />60 secondi<br />2 secondi<br />false |Tentativo di 1 - intervallo di 0 sec<br />Tentativo 2 - intervallo di ~2 sec<br />Tentativo 3 - intervallo di ~6 sec<br />Tentativo 4 - intervallo di ~14 sec<br />Tentativo 5 - intervallo di 30 sec |

> [!NOTE]
> Gli obiettivi di latenza end-to-end presuppongono il timeout predefinito per le connessioni al servizio. Se si specifica un timeout di connessione più lungo, la latenza end-to-end verrà estesa di questo intervallo di tempo aggiuntivo per ogni nuovo tentativo.
>
>

### <a name="examples"></a>Esempi
Questa sezione illustra come usare Polly per accedere al database SQL di Azure usando un set di criteri di ripetizione dei tentativi configurato nella classe `Policy`.

Il codice seguente illustra un metodo di estensione nella classe `SqlCommand` che richiama `ExecuteAsync` con backoff esponenziale.

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command)
{
    GuardConnectionIsNotNull(command);

    var policy = Policy.Handle<Exception>().WaitAndRetryAsync(
        retryCount: 3, // Retry 3 times
        sleepDurationProvider: attempt => TimeSpan.FromMilliseconds(200 * Math.Pow(2, attempt - 1)), // Exponential backoff based on an initial 200ms delay.
        onRetry: (exception, attempt) => 
        {
            // Capture some info for logging/telemetry.  
            logger.LogWarn($"ExecuteReaderWithRetryAsync: Retry {attempt} due to {exception}.");
        });

    // Retry the following call according to the policy.
    await policy.ExecuteAsync<SqlDataReader>(async token =>
    {
        // This code is executed within the Policy 

        if (conn.State != System.Data.ConnectionState.Open) await conn.OpenAsync(token);
        return await command.ExecuteReaderAsync(System.Data.CommandBehavior.Default, token);

    }, cancellationToken);
}
```

Questo metodo di estensione asincrona può essere usato come indicato di seguito.

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync())
{
    // Do something with the values
}
```

### <a name="more-information"></a>Altre informazioni
* [Livello di accesso ai dati di Cloud Service Fundamentals e gestione degli errori temporanei.](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

Per indicazioni generali su come ottenere il massimo dal database SQL, vedere la [guida all'elasticità e alle prestazioni del database SQL di Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx).

## <a name="sql-database-using-entity-framework-6"></a>Database SQL con Entity Framework 6
Il database SQL, in questo caso, è un database SQL ospitato, di varie dimensioni, disponibile come servizio standard (condiviso) o premium (non condiviso). Entity Framework, invece, è un mapper relazionale a oggetti che consente agli sviluppatori .NET di usare dati relazionali mediante oggetti specifici di dominio. Elimina la necessità di gran parte del codice di accesso ai dati che in genere gli sviluppatori devono scrivere.

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
Quando si accede al database SQL con Entity Framework 6.0 o versione superiore, il supporto per la ripetizione di tentativi viene fornito attraverso un meccanismo denominato [resilienza delle connessioni / logica di ripetizione dei tentativi](http://msdn.microsoft.com/data/dn456835.aspx). Di seguiti sono illustrate le caratteristiche principali del meccanismo di ripetizione dei tentativi:

* L'astrazione primaria è l'interfaccia **IDbExecutionStrategy** . Questa interfaccia:
  * Definisce i metodi **Execute*** sincroni e asincroni.
  * Definisce le classi che possono essere usate direttamente o configurate in un contesto di database come strategia predefinita, quindi mappate a un nome di provider o a un nome di provider e un nome di server. Se configurate in un contesto, i nuovi tentativi vengono eseguiti a livello di singole operazioni di database, che possono essere molteplici per una determinata operazione di contesto.
  * Definisce quando tentare di eseguire nuovamente una connessione non riuscita e in che modo.
* Include diverse implementazioni incorporate dell'interfaccia **IDbExecutionStrategy** :
  * Predefinita - Nessun nuovo tentativo.
  * Predefinita per il database SQL (automatica) - Nessun nuovo tentativo, ma controlla le eccezioni e ne esegue il wrapping con il suggerimento di usare la strategia relativa al database SQL.
  * Predefinita per il database SQL - Esponenziale (ereditata dalla classe di base), combinata con la logica di rilevamento del database SQL.
* Implementa una strategia di backoff esponenziale che include la sequenza casuale.
* Le classi di ripetizione dei tentativi incorporate sono classi con stato di tipo non thread-safe. Possono comunque essere riusate dopo il completamento dell'operazione corrente.
* Se viene superato il numero di tentativi specificato, i risultati vengono inclusi in una nuova eccezione e l'eccezione corrente non viene visualizzata.

### <a name="policy-configuration"></a>Configurazione dei criteri
Quando si accede al database SQL con Entity Framework 6.0, o versioni successive, è già disponibile il supporto per la ripetizione di tentativi. I criteri di ripetizione dei tentativi sono configurati a livello di codice. La configurazione non può essere modificata a livello di singola operazione.

Quando si configura una strategia di contesto come impostazione predefinita, è necessario specificare anche una funzione che consente di creare una nuova strategia su richiesta. Il codice seguente mostra come creare una classe di configurazione di nuovi tentativi che estende la classe di base **DbConfiguration** .

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

È quindi possibile impostarla come strategia di ripetizione dei tentativi predefinita per tutte le operazioni usando il metodo **SetConfiguration** dell'istanza **DbConfiguration** all'avvio dell'applicazione. Per impostazione predefinita, Entity Framework rileverà e userà automaticamente questa classe di configurazione.

```csharp
DbConfiguration.SetConfiguration(new BloggingContextConfiguration());
```

È possibile specificare la classe di configurazione di nuovi tentativi per un contesto specifico annotando la classe del contesto con un attributo **DbConfigurationType** . Tuttavia, se è presente una sola classe di configurazione, Entity Framework la userà anche senza dover annotare il contesto.

```csharp
[DbConfigurationType(typeof(BloggingContextConfiguration))]
public class BloggingContext : DbContext
```

Se per determinate operazioni è necessario usare strategie di ripetizione dei tentativi diverse o si disabilita la ripetizione dei tentativi, è possibile creare una classe di configurazione che consente di sospendere o cambiare strategie impostando un flag in **CallContext**. Questo flag può essere usato dalla classe di configurazione per cambiare strategie o per disabilitare la strategia specificata e usare invece una strategia predefinita. Per altre informazioni, vedere la sezione sulla [sospensione della strategia di esecuzione](http://msdn.microsoft.com/dn307226#transactions_workarounds) nella pagina relativa alle limitazioni quando si ripetono strategie di esecuzione (Entity Framework 6 o versione successiva).

Un'altra tecnica per usare strategie di ripetizione specifiche per singole operazioni consiste nel creare un'istanza della classe di strategia necessaria, fornire le impostazioni desiderate attraverso dei parametri e quindi richiamare il metodo **ExecuteAsync**.

```csharp
var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
var blogs = await executionStrategy.ExecuteAsync(
    async () =>
    {
        using (var db = new BloggingContext("Blogs"))
        {
            // Acquire some values asynchronously and return them
        }
    },
    new CancellationToken()
);
```

Il modo più semplice per usare una classe **DbConfiguration** consiste nell'individuarla nello stesso assembly della classe **DbContext**. Questo metodo, tuttavia, non è appropriato quando lo stesso contesto è richiesto in scenari diversi, ad esempio in varie strategie di ripetizione dei tentativi, interattiva o in background. Se i diversi contesti vengono eseguiti in AppDomain distinti, è possibile usare il supporto incorporato per specificare le classi di configurazione nel file di configurazione o impostarle in modo esplicito tramite codice. Se invece i diversi contesti devono essere eseguiti nello stesso AppDomain, sarà necessaria una soluzione personalizzata.

Per altre informazioni, vedere l'articolo sulla [configurazione basata su codice (Entity Framework 6 o versione successiva)](http://msdn.microsoft.com/data/jj680699.aspx).

La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati quando si usa Entity Framework 6.

| Impostazione | Valore predefinito | Significato |
|---------|---------------|---------|
| Criterio | Esponenziali | Backoff esponenziale. |
| MaxRetryCount | 5 | Numero massimo di tentativi. |
| MaxDelay | 30 secondi | Ritardo massimo tra i tentativi. Questa valore non influisce sulla modalità di calcolo della serie di ritardi. Definisce solo un limite superiore. |
| DefaultCoefficient | 1 secondo | Coefficiente per il calcolo del backoff esponenziale. Questo valore non può essere modificato. |
| DefaultRandomFactor | 1.1 | Moltiplicatore usato per aggiungere un ritardo casuale per ogni voce. Questo valore non può essere modificato. |
| DefaultExponentialBase | 2 | Moltiplicatore usato per calcolare il ritardo successivo. Questo valore non può essere modificato. |

### <a name="retry-usage-guidance"></a>Linee guida sull'uso dei criteri di ripetizione dei tentativi
Quando si accede al database SQL con Entity Framework 6, tenere presente le linee guida seguenti:

* Scegliere il tipo di servizio appropriato (condiviso o premium). Un'istanza condivisa può subire limitazioni o ritardi di connessione maggiori rispetto alla media perché può essere usata da altri tenant del server condiviso. Se sono necessarie prestazioni prevedibili o affidabili operazioni di bassa latenza, valutare la possibilità di scegliere il tipo servizio premium.
* Per il database SQL di Azure non è consigliabile usare una strategia a intervalli fissi, bensì una strategia di backoff esponenziale, poiché il servizio può essere sovraccarico e intervalli più lunghi concedono più tempo per il ripristino del servizio.
* Quando si definiscono le connessioni, scegliere un valore appropriato per i timeout di connessione e dei comandi. I timeout devono essere definiti in base alla progettazione della logica di business e tramite una serie di test. Potrebbe essere necessario modificare questi valori nel tempo man mano che i volumi dei dati o dei processi di business cambiano. Un timeout troppo breve può comportare errori di connessione prematuri quando il database è occupato, mentre un timeout troppo lungo può impedire che la logica di ripetizione dei tentativi funzioni correttamente, poiché l'intervallo di attesa prima che venga rilevata una connessione non riuscita è troppo lungo. Sebbene il valore di timeout sia un componente della latenza end-to-end, non è possibile determinare facilmente il numero di comandi che verranno eseguiti durante il salvataggio del contesto. È possibile modificare il timeout predefinito impostando la proprietà **CommandTimeout** dell'istanza **DbContext**.
* Entity Framework supporta le configurazioni di ripetizione dei tentativi definite nei file di configurazione. Per ottenere la massima flessibilità in Azure, tuttavia, è opportuno valutare la possibilità di creare la configurazione a livello di codice all'interno dell'applicazione. I parametri specifici dei criteri di ripetizione dei tentativi, ad esempio il numero di tentativi e l'intervallo tra di essi, possono essere archiviati nel file di configurazione del servizio e usati in fase di esecuzione per creare i criteri appropriati. In questo modo è possibile modificare le impostazioni senza dover riavviare l'applicazione.

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Non è possibile specificare l'intervallo tra i tentativi (si tratta di un valore fisso generato come sequenza esponenziale); è possibile specificare solo i valori massimi, come illustrato di seguito, a meno che non sia stata creata una strategia di ripetizione dei tentativi personalizzata. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.

| **Contesto** | **Destinazione di esempio E2E<br />latenza massima** | **Criteri di ripetizione** | **Impostazioni** | **Valori** | **Funzionamento** |
| --- | --- | --- | --- | --- | --- |
| Interattivo, interfaccia utente<br />o in primo piano |2 secondi |Esponenziali |MaxRetryCount<br />MaxDelay |3<br />750 ms |Tentativo di 1 - intervallo di 0 sec<br />Tentativo 2 - intervallo di 750 ms<br />Tentativo 3 - intervallo di 750 ms |
| Background<br /> o batch |30 secondi |Esponenziali |MaxRetryCount<br />MaxDelay |5<br />12 secondi |Tentativo di 1 - intervallo di 0 sec<br />Tentativo 2 - intervallo di ~1 sec<br />Tentativo 3 - intervallo di ~3 sec<br />Tentativo 4 - intervallo di ~7 sec<br />Tentativo 5 - intervallo di 12 sec |

> [!NOTE]
> Gli obiettivi di latenza end-to-end presuppongono il timeout predefinito per le connessioni al servizio. Se si specifica un timeout di connessione più lungo, la latenza end-to-end verrà estesa di questo intervallo di tempo aggiuntivo per ogni nuovo tentativo.
>
>

### <a name="examples"></a>Esempi
L'esempio di codice seguente definisce una soluzione di accesso ai dati semplice che usa Entity Framework. Imposta una strategia di ripetizione dei tentativi specifica tramite la definizione di un'istanza di una classe denominata **BlogConfiguration** che estende **DbConfiguration**.

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
    public class BlogConfiguration : DbConfiguration
    {
        public BlogConfiguration()
        {
            // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
            // These values could be loaded from configuration rather than being hard-coded.
            this.SetExecutionStrategy(
                    "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
        }
    }

    // Specify the configuration type if more than one has been defined.
    // [DbConfigurationType(typeof(BlogConfiguration))]
    public class BloggingContext : DbContext
    {
        // Definition of content goes here.
    }

    class EF6CodeSamples
    {
        public async static Task Samples()
        {
            // Execution strategy configured by DbConfiguration subclass, discovered automatically or
            // or explicitly indicated through configuration or with an attribute. Default is no retries.
            using (var db = new BloggingContext("Blogs"))
            {
                // Add, edit, delete blog items here, then:
                await db.SaveChangesAsync();
            }
        }
    }
}
```

Altri esempi sull'uso del meccanismo di ripetizione dei tentavi in Entity Framework sono disponibili in [resilienza delle connessioni / logica di ripetizione dei tentativi](http://msdn.microsoft.com/data/dn456835.aspx).

### <a name="more-information"></a>Altre informazioni
* [Guida relativa all'elasticità e alle prestazioni del database SQL di Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## <a name="sql-database-using-entity-framework-core"></a>Database SQL con Entity Framework Core
[Entity Framework Core](/ef/core/) è un mapper relazionale a oggetti che consente agli sviluppatori .NET Core di usare dati mediante oggetti specifici di dominio. Elimina la necessità di gran parte del codice di accesso ai dati che in genere gli sviluppatori devono scrivere. Questa versione di Entity Framework è stata scritta da zero e non eredita automaticamente tutte le funzionalità di EF6.x.

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
Quando si accede al database SQL con Entity Framework Core, il supporto per la ripetizione di tentativi viene offerto attraverso un meccanismo denominato [Resilienza connessione](/ef/core/miscellaneous/connection-resiliency). Resilienza connessione è stato introdotto in EF Core 1.1.0.

La principale astrazione è l'interfaccia `IExecutionStrategy`. La strategia di esecuzione per SQL Server, che include SQL Azure, riconosce i tipi di eccezione per cui è possibile ripetere i tentativi e dispone di impostazioni predefinite ragionevoli per un numero massimo ripetizione dei tentativi, intervalli tra di essi e così via.

### <a name="examples"></a>Esempi

Il codice seguente abilita le ripetizioni automatiche di tentativi quando si configura l'oggetto DbContext, che rappresenta una sessione con il database. 

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(
            @"Server=(localdb)\mssqllocaldb;Database=EFMiscellanous.ConnectionResiliency;Trusted_Connection=True;",
            options => options.EnableRetryOnFailure());
}
```

Il codice seguente illustra come eseguire una transazione con la ripetizione di tentativi automatica, usando una strategia di esecuzione. La transazione viene definita in un delegato. Se si verifica un errore temporaneo, la strategia di esecuzione chiamerà nuovamente il delegato.

```csharp
using (var db = new BloggingContext())
{
    var strategy = db.Database.CreateExecutionStrategy();

    strategy.Execute(() =>
    {
        using (var transaction = db.Database.BeginTransaction())
        {
            db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
            db.SaveChanges();

            db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/visualstudio" });
            db.SaveChanges();

            transaction.Commit();
        }
    });
}
```

## <a name="azure-storage"></a>Archiviazione di Azure
I servizi di archiviazione di Azure includono funzioni di archiviazione di BLOB e tabelle, file e code di archiviazione.

### <a name="retry-mechanism"></a>Meccanismo di ripetizione dei tentativi
I nuovi tentativi si compiono a livello di singola operazione REST e sono parte integrante dell'implementazione dell'API client. L'SDK di archiviazione dei client usa classi che implementano l'interfaccia [IExtendedRetryPolicy Interface](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).

Esistono diverse implementazioni di questa interfaccia. I client di archiviazione possono scegliere tra vari tipi di criteri, progettati per l'accesso a tabelle, BLOB o code. Ogni implementazione usa una strategia di ripetizione dei tentativi diversa, che essenzialmente definisce l'intervallo di tempo tra i tentativi e altri dettagli.

Le classi incorporate forniscono il supporto per intervalli lineari (tempo d'intervallo costante) ed esponenziali con sequenza casuale. Esiste anche un criterio di tipo "nessun tentativo" se la gestione dei tentativi viene eseguita da un altro processo a un livello superiore. Se, tuttavia, sono presenti requisiti specifici non previsti dalle classi incorporate, è possibile implementare classi di ripetizione dei tentativi personalizzate.

Se si usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è possibile, ad esempio, alternare i tentativi tra il percorso del servizio di archiviazione primario e quello del servizio secondario e il risultato della richiesta è un errore non irreversibile. Per altre informazioni, vedere [Opzioni di ridondanza dell'archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx) .

### <a name="policy-configuration"></a>Configurazione dei criteri
I criteri di ripetizione dei tentativi sono configurati a livello di codice. La procedura tipica consiste nel creare e popolare un'istanza **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions** o **QueueRequestOptions**.

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. 
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

L'istanza delle opzioni di richiesta può essere quindi impostata sul client in modo che tutte le operazioni eseguite sul client usino le opzioni di richiesta specificate.

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

È comunque possibile ignorare le opzioni di richiesta del client passando ai metodi dell'operazione un'istanza popolata della classe delle opzioni di richiesta sotto forma di parametro.

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

Si userà in questo caso un'istanza **OperationContext** per specificare il codice da eseguire quando si ripete un tentativo o quando, invece, viene completata un'operazione. Questo codice è in grado di raccogliere informazioni sull'operazione, da usare nei log e nella telemetria.

```csharp
// Set up notifications for an operation
var context = new OperationContext();
context.ClientRequestID = "some request id";
context.Retrying += (sender, args) =>
{
    /* Collect retry information */
};
context.RequestCompleted += (sender, args) =>
{
    /* Collect operation completion information */
};
var stats = await client.GetServiceStatsAsync(null, context);
```

Oltre a indicare se un errore può essere risolto eseguendo nuovi tentativi, i criteri estesi di ripetizione dei tentativi restituiscono un oggetto **RetryContext** , che indica il numero di tentativi, i risultati dell'ultima richiesta e se il tentativo successivo verrà eseguito nel percorso primario o secondario (vedere la tabella seguente per maggiori dettagli). Le proprietà dell'oggetto **RetryContext** possono essere inoltre usate per decidere se e quando eseguire un nuovo tentativo. Per altre informazioni, vedere [IExtendedRetryPolicy.Evaluate Method](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).

Le tabelle seguenti mostrano le impostazioni predefinite per i criteri di ripetizione dei tentativi predefiniti.

**Opzioni richiesta**

| **Impostazione** | **Valore predefinito** | **Significato** |
| --- | --- | --- |
| MaximumExecutionTime | Nessuna | Tempo di esecuzione massimo per la richiesta, inclusi tutti i potenziali tentativi. Se non è specificato, il tempo che una richiesta è autorizzata a impiegare è illimitato. In altre parole, la richiesta potrebbe bloccarsi. |
| ServerTimeout | Nessuna | Intervallo di timeout del server per la richiesta (il valore viene arrotondato a secondi). Se non specificato, verrà usato il valore predefinito per tutte le richieste al server. In genere, la scelta migliore è omettere questa impostazione in modo che venga usato il valore predefinito del server. | 
| LocationMode | Nessuna | Se l'account di archiviazione viene creato con l'opzione di replica "archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)", è possibile usare la modalità percorso per indicare in corrispondenza di quale percorso deve essere ricevuta la richiesta. Ad esempio, se è specificata l'opzione **PrimaryThenSecondary** , le richieste vengono sempre inviate prima al percorso primario. Se una richiesta ha esito negativo, viene quindi inviata al percorso secondario. |
| RetryPolicy | ExponentialPolicy | Per informazioni dettagliate su ogni opzione, vedere le sezioni seguenti. |

**Criteri esponenziali** 

| **Impostazione** | **Valore predefinito** | **Significato** |
| --- | --- | --- |
| maxAttempt | 3 | Numero di tentativi. |
| deltaBackoff | 4 secondi | Intervallo di backoff tra i tentativi. Per i tentativi successivi verranno usati multipli di questo intervallo di tempo, combinati con un elemento casuale. |
| MinBackoff | 3 secondi | I valori vengono aggiunti a tutti gli intervalli tra i tentativi, calcolati a partire da deltaBackoff. Questo valore non può essere modificato.
| MaxBackoff | 120 secondi | MaxBackoff viene usato se l'intervallo tra i tentativi è maggiore di MaxBackoff. Questo valore non può essere modificato. |

**Criteri lineari**

| **Impostazione** | **Valore predefinito** | **Significato** |
| --- | --- | --- |
| maxAttempt | 3 | Numero di tentativi. |
| deltaBackoff | 30 secondi | Intervallo di backoff tra i tentativi. |

### <a name="retry-usage-guidance"></a>Linee guida sull'uso dei criteri di ripetizione dei tentativi
Quando si accede ai servizi di archiviazione di Azure tramite l'API del client di archiviazione, tenere presenti le linee guida seguenti:

* Usare i criteri di ripetizione dei tentativi dello spazio dei nomi Microsoft.WindowsAzure.Storage.RetryPolicies se appropriati per le proprie esigenze. Nella maggior parte dei casi, questi criteri saranno sufficienti.
* Usare invece i criteri **ExponentialRetry** nelle operazioni in batch, nelle attività in background e negli scenari non interattivi. In questi scenari, è generalmente possibile concedere più tempo per il ripristino del servizio, aumentando così le probabilità che l'operazione abbia esito positivo.
* Valutare la possibilità di specificare la proprietà **MaximumExecutionTime** del parametro **RequestOptions** per limitare il tempo di esecuzione complessivo, ma tenere conto del tipo e delle dimensioni dell'operazione quando si sceglie un valore di timeout.
* Se è necessario implementare un meccanismo di ripetizione dei tentativi personalizzato, evitare di creare wrapper per le classi dei client di archiviazione. Usare invece questa funzionalità per estendere i criteri esistenti tramite l'interfaccia **IExtendedRetryPolicy** .
* Se si usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è possibile adottare la modalità **LocationMode** per specificare che i nuovi tentativi accederanno alla copia secondaria di sola lettura dell'archivio, nel caso in cui l'accesso alla versione principale abbia esito negativo. Quando si usa questa opzione, tuttavia, è necessario verificare che l'applicazione possa gestire correttamente anche dati non aggiornati, nel caso in cui la replica dall'archivio primario non sia ancora stata completata.

È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti. Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.  

| **Contesto** | **Destinazione di esempio E2E<br />latenza massima** | **Criteri di ripetizione** | **Impostazioni** | **Valori** | **Funzionamento** |
| --- | --- | --- | --- | --- | --- |
| Interattivo, interfaccia utente<br />o in primo piano |2 secondi |Lineari |maxAttempt<br />deltaBackoff |3<br />500 ms |Tentativo di 1 - intervallo di 500 ms<br />Tentativo 2 - intervallo di 500 ms<br />Tentativo 3 - intervallo di 500 ms |
| Background<br />o batch |30 secondi |Esponenziali |maxAttempt<br />deltaBackoff |5<br />4 secondi |Tentativo di 1 - intervallo di ~3 sec<br />Tentativo 2 - intervallo di ~7 sec<br />Tentativo 3 - intervallo di ~15 sec |

### <a name="telemetry"></a>Telemetria
Il numero di tentativi viene registrato in un'origine **TraceSource**. È necessario configurare un oggetto **TraceListener** per acquisire gli eventi e scriverli in un log di destinazione appropriato. È possibile usare **TextWriterTraceListener** o **XmlWriterTraceListener** per scrivere i dati in un file di log, **EventLogTraceListener** per scriverli nel Registro eventi di Windows o **EventProviderTraceListener** per scrivere i dati di traccia nel sottosistema ETW. È inoltre possibile configurare lo svuotamento automatico del buffer e il livello di dettaglio degli eventi che verranno registrati nel log (specificando, ad esempio, i valori Error, Warning, Informational e Verbose). Per altre informazioni, vedere [Registrazione lato client con la libreria client di archiviazione .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx).

Le operazioni possono ricevere un'istanza **OperationContext**, che espone un evento **Retrying** di cui è possibile usufruire per associare una logica personalizzata per i dati telemetrici. Per altre informazioni, vedere [Evento OperationContext.Retrying](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).

### <a name="examples"></a>Esempi
L'esempio di codice seguente mostra come creare due istanze **TableRequestOptions** con due diverse impostazioni di ripetizione dei tentativi: una per le richieste interattive e una per le richieste in background. L'esempio imposta quindi questi due criteri di ripetizione dei tentativi in modo che vengano applicati a tutte le richieste. Imposta inoltre la strategia interattiva su una richiesta specifica in modo che ignori le impostazioni predefinite applicate al client.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case. 
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

### <a name="more-information"></a>Altre informazioni
* [Suggerimenti per i criteri di ripetizione dei tentativi nella libreria client dell'archiviazione di Azure](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
* [Libreria client di archiviazione 2.0 - Implementazione dei criteri di ripetizione dei tentativi](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## <a name="general-rest-and-retry-guidelines"></a>Linee guida generali su REST e sulla ripetizione di tentativi
Quando si accede a servizi di Azure o di terze parti, tenere presente quanto segue:

* Usare un approccio sistematico per gestire la ripetizione di tentativi, ad esempio sotto forma di codice riusabile, in modo da poter applicare una metodologia coerente tra tutti i client e le soluzioni.
* Valutare la possibilità di usare un framework di ripetizione dei tentativi come [Polly][polly] per gestire la ripetizione di tentativi se nel client o nel servizio di destinazione non è incorporato un meccanismo di ripetizione dei tentativi. In questo modo, sarà possibile implementare un comportamento coerente per la gestione dei nuovi tentativi e usufruire di un'adeguata strategia di ripetizione dei tentativi predefinita per il servizio di destinazione. Potrebbe essere necessario, tuttavia, creare un codice di ripetizione dei tentativi personalizzato per i servizi che presentano un comportamento non standard, ovvero non basato sulle eccezioni per stabilire gli errori temporanei, oppure se si desidera usare un metodo **Retry-Response** per gestire il comportamento di ripetizione dei tentativi.
* La logica di rilevamento degli errori temporanei dipenderà dall'API client effettiva usata per richiamare le chiamate REST. Alcuni client, ad esempio la nuova classe **HttpClient** , non genereranno eccezioni per le richieste completate con un codice di stato HTTP di errore. 
* Il codice di stato HTTP restituito dal servizio consente di stabilire se l'errore è temporaneo. È possibile che sia necessario esaminare le eccezioni generate da un client o dal framework di ripetizione dei tentativi per accedere al codice di stato o determinare il tipo di eccezione equivalente. I seguenti codici HTTP indicano, in genere, che è opportuno eseguire un nuovo tentativo:
  * 408 - Timeout richiesta
  * 429 - Numero eccessivo di richieste
  * 500 - Errore interno del server
  * 502 - Gateway non valido
  * 503 - Servizio non disponibile
  * 504 - Timeout gateway
* Se si usa una logica di ripetizione dei tentativi basata sulle eccezioni, i valori seguenti indicano un errore temporaneo in cui non è possibile stabilire una connessione:
  * WebExceptionStatus.ConnectionClosed
  * WebExceptionStatus.ConnectFailure
  * WebExceptionStatus.Timeout
  * WebExceptionStatus.RequestCanceled
* In caso di stato non disponibile di un servizio, l'intervallo di tempo appropriato prima di un nuovo tentativo può essere riportato nell'intestazione della risposta **Retry-After** o in un'altra intestazione personalizzata del servizio. Alcuni servizi possono anche inviare informazioni aggiuntive come intestazioni personalizzate o incorporate nel contenuto della risposta. 
* Non eseguire nuovi tentativi in caso di codici di stato che rappresentano errori client (errori nell'intervallo 4xx), ad eccezione di 408 - Timeout richiesta.
* Verificare accuratamente le strategie e i meccanismi di ripetizione dei tentativi in condizioni diverse, ad esempio in vari stati di rete e carichi di sistema.

### <a name="retry-strategies"></a>Strategie di ripetizione dei tentativi
Di seguito sono riportati i tipi intervallo più comuni nelle strategie di ripetizione dei tentativi:

* **Esponenziale**. Criteri di ripetizione dei tentativi che eseguono un numero prestabilito di nuovi tentativi e usano un approccio di backoff esponenziale casuale per determinare l'intervallo di tempo tra i tentativi. Ad esempio: 

    ```csharp
    var random = new Random();

    var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
                random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
                (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
    var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
                    this.maxBackoff.TotalMilliseconds);
    retryInterval = TimeSpan.FromMilliseconds(interval);
    ```

* **Incrementale**. Strategia di ripetizione dei tentativi con un numero prestabilito di nuovi tentativi e un intervallo di tempo incrementale tra i tentativi. Ad esempio: 

    ```csharp
    retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
                    (this.increment.TotalMilliseconds * currentRetryCount));
    ```

* **LinearRetry**. Criteri di ripetizione dei tentativi che eseguono un numero prestabilito di nuovi tentativi e usano un intervallo di tempo fisso tra i tentativi. Ad esempio: 

    ```csharp
    retryInterval = this.deltaBackoff;
    ```

### <a name="transient-fault-handling-with-polly"></a>Gestione degli errori temporanei con Polly
[Polly][polly] è una libreria per gestire a livello di codice la ripetizione dei tentativi e le strategie dell'[interruttore][circuit-breaker]. Il progetto Polly è membro di [.NET Foundation][dotnet-foundation]. Per i servizi in cui il client non supporta in modo nativo la ripetizione dei tentativi, Polly è una valida alternativa ed evita la necessità di scrivere il codice personalizzato per la ripetizione dei tentativi, la cui corretta implementazione può risultare difficile. Polly offre anche un modo per tracciare gli errori quando questi si verificano, in modo che sia possibile registrare i tentativi.


<!-- links -->

[adal]: /azure/active-directory/develop/active-directory-authentication-libraries
[autorest]: https://github.com/Azure/autorest/tree/master/docs
[circuit-breaker]: ../patterns/circuit-breaker.md
[ConnectionPolicy.RetryOptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.retryoptions.aspx
[dotnet-foundation]: https://dotnetfoundation.org/
[polly]: http://www.thepollyproject.org
[redis-cache-troubleshoot]: /azure/redis-cache/cache-how-to-troubleshoot
[SearchIndexClient]: https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx
[SearchServiceClient]: https://msdn.microsoft.com/library/microsoft.azure.search.searchserviceclient.aspx


### <a name="more-information"></a>Altre informazioni
* [Resilienza connessione](/ef/core/miscellaneous/connection-resiliency)
* [Punto dati - EF Core 1.1](https://msdn.microsoft.com/magazine/mt745093.aspx)


