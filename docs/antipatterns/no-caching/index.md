---
title: Nessun antipattern della memorizzazione nella cache
description: "Il recupero ripetuto degli stessi dati può ridurre le prestazioni e la scalabilità."
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: eb6b69c13b8954ae2efb1da96dec05bc1c13d18b
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="no-caching-antipattern"></a>Nessun antipattern della memorizzazione nella cache

In un'applicazione cloud che gestisce molte richieste simultanee, il recupero ripetuto degli stessi dati può ridurre le prestazioni e la scalabilità. 

## <a name="problem-description"></a>Descrizione del problema

Quando non vengono memorizzati i dati nella cache, possono verificarsi molti comportamenti indesiderati, tra cui:

- Il recupero ripetuto delle stesse informazioni da una risorsa che è dispendiosa per l'accesso, in termini di sovraccarico di I/O o latenza.
- La costruzione ripetuta degli stessi oggetti o strutture di dati per più richieste.
- Chiamare in modo eccessivo un servizio remoto che dispone di una quota di servizio e limita i client dopo un determinato limite.

A sua volta, questi problemi possono portare a tempi di risposta inadeguati, a una maggiore contesa nell'archivio dati e a una scarsa scalabilità.

L'esempio seguente usa Entity Framework per connettersi a un database. Ogni richiesta del client comporta una chiamata al database, anche se più richieste stiano recuperando esattamente gli stessi dati. Il costo delle richieste ripetute, in termini di sovraccarico di I/O e di addebiti di accesso ai dati, può accumularsi rapidamente.

```csharp
public class PersonRepository : IPersonRepository
{
    public async Task<Person> GetAsync(int id)
    {
        using (var context = new AdventureWorksContext())
        {
            return await context.People
                .Where(p => p.Id == id)
                .FirstOrDefaultAsync()
                .ConfigureAwait(false);
        }
    }
}
```

L'esempio completo è disponibile [qui][sample-app].

In genere questo antipattern si verifica perché:

- Il mancato utilizzo di una cache è più semplice da implementare e funziona correttamente con carichi bassi. La memorizzazione nella cache rende il codice più complesso. 
- I vantaggi e gli svantaggi dell'uso di una cache non sono ben compresi.
- La preoccupazione riguarda il sovraccarico di gestione dell'accuratezza e dell'aggiornamento dei dati memorizzati nella cache.
- È stata eseguita la migrazione di un'applicazione da un sistema locale, in cui la latenza di rete non rappresentava un problema, e il sistema ha lavorato su un hardware impegnativo ad alte prestazioni, pertanto la memorizzazione nella cache non è stata considerata nella progettazione originale.
- Gli sviluppatori non sono a conoscenza del fatto che la memorizzazione nella cache rappresenta una possibilità in uno scenario specifico. Ad esempio, gli sviluppatori possono non ritenere opportuno l'uso di ETag durante l'implementazione di un'API web.

## <a name="how-to-fix-the-problem"></a>Come risolvere il problema

La strategia di memorizzazione nella cache più diffusa è quella *su richiesta* o *cache-aside*.

- Nel percorso di lettura, l'applicazione tenta di leggere i dati dalla cache. Se i dati non sono nella cache, l'applicazione li recupera dall'origine dati e li aggiunge alla cache.
- Nel percorso di scrittura, l'applicazione scrive la modifica direttamente all'origine dati e rimuove il valore precedente dalla cache. Verrà recuperato e aggiunto alla cache la volta successiva in cui è richiesto.

Questo approccio è adatto per i dati vengono modificati frequentemente. Di seguito è riportato l'esempio precedente aggiornato per usare il modello [Cache-Aside][cache-aside].  

```csharp
public class CachedPersonRepository : IPersonRepository
{
    private readonly PersonRepository _innerRepository;

    public CachedPersonRepository(PersonRepository innerRepository)
    {
        _innerRepository = innerRepository;
    }

    public async Task<Person> GetAsync(int id)
    {
        return await CacheService.GetAsync<Person>("p:" + id, () => _innerRepository.GetAsync(id)).ConfigureAwait(false);
    }
}

public class CacheService
{
    private static ConnectionMultiplexer _connection;

    public static async Task<T> GetAsync<T>(string key, Func<Task<T>> loadCache, double expirationTimeInMinutes)
    {
        IDatabase cache = Connection.GetDatabase();
        T value = await GetAsync<T>(cache, key).ConfigureAwait(false);
        if (value == null)
        {
            // Value was not found in the cache. Call the lambda to get the value from the database.
            value = await loadCache().ConfigureAwait(false);
            if (value != null)
            {
                // Add the value to the cache.
                await SetAsync(cache, key, value, expirationTimeInMinutes).ConfigureAwait(false);
            }
        }
        return value;
    }
}
```

Si noti che il metodo `GetAsync` ora chiama la classe `CacheService`, invece di chiamare direttamente il database. La classe `CacheService` tenta innanzitutto di ottenere l'elemento dalla Cache Redis di Azure. Se il valore non viene trovato nella Cache Redis, `CacheService` richiama una funzione lambda che gli è stata passata dal chiamante. La funzione lambda ha il compito di recuperare i dati dal database. Questa implementazione separa il repository dalla particolare soluzione di memorizzazione nella cache e separa `CacheService` dal database. 

## <a name="considerations"></a>Considerazioni

- Se la cache non è disponibile, probabilmente a causa di un errore temporaneo, non restituisce un errore al client. In alternativa recuperare i dati dall'origine dati di partenza. Tuttavia, tenere conto del fatto che mentre viene recuperata la cache, l'archivio dati originale potrebbe essere sovraccaricato da richieste, con conseguenti timeout ed errori di connessione (dopo tutto, questo è uno dei motivi per usare una cache in primo luogo). Usare una tecnica come ad esempio il [Modello di interruttore][circuit-breaker] per evitare di sovraccaricare l'origine dati.

- Le applicazioni che memorizzano nella cache dati non statici devono essere progettate per supportare la coerenza finale.

- Per le API web, è possibile supportare la cache lato client includendo un'intestazione Cache-Control nei messaggi di richiesta e risposta, e usando i valori eTag per identificare le versioni degli oggetti. Per altre informazioni, vedere [implementazione delle API][api-implementation].

- Non è necessario memorizzare nella cache intere entità. Se la maggior parte di un'entità è statico ma solo una piccola parte cambia frequentemente, memorizzare nella cache gli elementi statici e recuperare gli elementi dinamici dall'origine dati. Questo approccio aiuta a ridurre il volume di I/O eseguito sull'origine dati.

- In alcuni casi, se i dati volatili sono di breve durata, può essere utile memorizzarli nella cache. Si consideri ad esempio un dispositivo che invia continuamente gli aggiornamenti di stato. Potrebbe essere opportuno memorizzare nella cache queste informazioni non appena arrivano e non scriverle affatto in un archivio permanente.  

- Per impedire l'obsolescenza dei dati, molte soluzioni di memorizzazione nella cache supportano periodi di scadenza configurabili, in modo che i dati vengono automaticamente rimossi dalla cache dopo un intervallo specificato. Potrebbe essere necessario regolare l'ora di scadenza per il proprio scenario. I dati che sono molto statici possono rimanere nella cache per periodi di tempo più lunghi rispetto ai dati volatili che possono diventare rapidamente obsoleti.

- Se la soluzione di memorizzazione nella cache non fornisce una scadenza incorporata, potrebbe essere necessario implementare un processo in background che occasionalmente organizza la cache per impedire l'aumento illimitato delle dimensioni. 

- Oltre alla memorizzazione nella cache dei dati da un'origine dati esterna, è possibile usare la memorizzazione nella cache per salvare i risultati di calcoli complessi. Prima di eseguire questa operazione, tuttavia, instrumentare l'applicazione per determinare se l'applicazione è realmente associata alla CPU.

- Potrebbe essere utile preparare la cache all'avvio dell'applicazione. Popolare la cache con i dati che sono probabilmente da usare.

- Includere sempre strumentazione che rileva i riscontri nella cache e i mancati riscontri nella cache. Usare queste informazioni per ottimizzare i criteri di memorizzazione nella cache, come ad esempio quali dati memorizzare nella cache e per quanto tempo contenere i dati nella cache prima della scadenza.

- Se la mancanza di memorizzazione nella cache è un collo di bottiglia, aggiungere la memorizzazione nella cache può aumentare il volume di richieste a tal punto che il front-end web diventa sovraccarico. I client possono iniziare a ricevere gli errori HTTP 503 (Servizio non disponibile). Si tratta di un'indicazione del fatto che è necessario scalare orizzontalmente il front-end.

## <a name="how-to-detect-the-problem"></a>Come rilevare il problema

È possibile eseguire la procedura seguente per identificare se la mancanza di memorizzazione nella cache sta provocando problemi alle prestazioni:

1. Verificare la progettazione dell'applicazione. Eseguire un inventario di tutti gli archivi dati usati dall'applicazione. Per ciascuno, determinare se l'applicazione sta usando una cache. Se possibile, determinare la frequenza con cui i dati vengono modificati. Dei buoni candidati iniziali per la memorizzazione nella cache includono i dati che cambiano lentamente e i dati di riferimento statici che sono letti frequentemente. 

2. Instrumentare l'applicazione e monitorare il sistema in tempo reale per individuare la frequenza con cui l'applicazione recupera i dati o calcola le informazioni.

3. Profilare l'applicazione in un ambiente di test per acquisire le metriche di basso livello relative al sovraccarico associato a operazioni di accesso ai dati o altri calcoli eseguiti di frequente.

4. Eseguire test di carico in un ambiente di test per identificare come il sistema risponde con un carico di lavoro normale e un carico pesante. Il test di carico deve simulare il modello di accesso ai dati osservato nell'ambiente di produzione usando carichi realistici. 

5. Esaminare le statistiche di accesso ai dati per sottolineare l'archivio dati ed eseguire la revisione della frequenza con cui le stesse richieste di dati vengono ripetute. 


## <a name="example-diagnosis"></a>Diagnosi di esempio

Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.

### <a name="instrument-the-application-and-monitor-the-live-system"></a>Instrumentare l'applicazione e monitorare il sistema in tempo reale

Instrumentare l'applicazione e monitorarla per ottenere informazioni sulle specifiche richieste che gli utenti fanno mentre l'applicazione è in fase di produzione. 

L'immagine seguente mostra il monitoraggio dei dati acquisiti da [New Relic][NewRelic] durante un test di carico. In questo caso, l'unica operazione HTTP GET eseguita è `Person/GetAsync`. Ma in un ambiente di produzione in tempo reale, conoscere la frequenza relativa in cui viene eseguita ogni richiesta può fornire informazioni dettagliate su quali risorse devono essere memorizzate nella cache.

![New Relic che mostra le richieste del server per l'applicazione CachingDemo][NewRelic-server-requests]

Se è necessaria un'analisi più approfondita, è possibile usare un profiler per acquisire i dati delle prestazioni di basso livello in un ambiente di test (non il sistema di produzione). Esaminare le metriche, come ad esempio la frequenza delle richieste, l'uso della memoria, e l'uso della CPU. Queste metriche potrebbero mostrare un numero elevato di richieste a un archivio dati o a un servizio o l'elaborazione ripetuta che esegue lo stesso calcolo. 

### <a name="load-test-the-application"></a>Testare il carico dell'applicazione

Il grafico seguente mostra i risultati del test di carico dell'applicazione di esempio. Il test di carico simula un caricamento del passaggio di un massimo di 800 utenti che eseguono una serie tipica di operazioni. 

![Risultati del test di carico delle prestazioni per lo scenario non memorizzato nella cache][Performance-Load-Test-Results-Uncached]

Il numero di test con esito positivo eseguiti ogni secondo raggiunge una soglia e di conseguenza le richieste aggiuntive subiscono un rallentamento. Il tempo medio del test viene incrementato costantemente con il carico di lavoro. Il tempo di risposta raggiunge il picco quando il carico dell'utente raggiunge il picco.

### <a name="examine-data-access-statistics"></a>Esaminare le statistiche dell'accesso ai dati

Le statistiche dell'accesso ai dati e altre informazioni fornite da un archivio dati può fornire informazioni utili, ad esempio quali query vengono ripetute più frequentemente. Ad esempio, in Microsoft SQL Server, la gestione `sys.dm_exec_query_stats` dispone di informazioni statistiche per le query eseguite di recente. Il testo per ogni query è disponibile nella visualizzazione `sys.dm_exec-query_plan`. È possibile usare uno strumento come SQL Server Management Studio per eseguire la seguente query SQL e per determinare la frequenza con cui vengono eseguite le query.

```SQL
SELECT UseCounts, Text, Query_Plan
FROM sys.dm_exec_cached_plans
CROSS APPLY sys.dm_exec_sql_text(plan_handle)
CROSS APPLY sys.dm_exec_query_plan(plan_handle)
```

La colonna `UseCount` nei risultati indica la frequenza con cui viene eseguita ogni query. L'immagine seguente mostra che la terza query è stata eseguita più di 250.000 volte, molto di più di qualsiasi altra query.

![Risultati dell'esecuzione delle query sulle DMV nel Server di gestione di SQL Server][Dynamic-Management-Views]

Di seguito è riportata la query SQL che sta causando così tante richieste di database: 

```SQL
(@p__linq__0 int)SELECT TOP (2)
[Extent1].[BusinessEntityId] AS [BusinessEntityId],
[Extent1].[FirstName] AS [FirstName],
[Extent1].[LastName] AS [LastName]
FROM [Person].[Person] AS [Extent1]
WHERE [Extent1].[BusinessEntityId] = @p__linq__0
```

Di seguito è riportata la query che Entity Framework genera nel metodo `GetByIdAsync` illustrato in precedenza.

### <a name="implement-the-solution-and-verify-the-result"></a>Implementare la soluzione e verificare il risultato

In seguito si incorpora una cache, si ripetono i test di carico e si confrontano i risultati con i test di carico precedenti senza una cache. Di seguito sono riportati i risultati del test di carico dopo l'aggiunta di una cache all'applicazione di esempio.

![Risultati del test di carico delle prestazioni per lo scenario memorizzato nella cache][Performance-Load-Test-Results-Cached]

Il volume di test con esito positivo si stabilizza, ma a un carico maggiore dell'utente. La frequenza delle richieste su questo carico è notevolmente superiore rispetto alla precedente. Il tempo medio per il test aumenta ancora con il carico, ma il tempo massimo di risposta è di 0,05 ms, confrontato con 1 ms precedente a &mdash; un miglioramento 20&times;. 

## <a name="related-resources"></a>Risorse correlate

- [Procedure consigliate dell'implementazione API][api-implementation]
- [Modello Cache-Aside][cache-aside-pattern]
- [Caching best practices][caching-guidance] (Procedure consigliate per la memorizzazione nella cache)
- [Modello di interruttore][circuit-breaker]

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/NoCaching
[cache-aside-pattern]: /azure/architecture/patterns/cache-aside
[caching-guidance]: ../../best-practices/caching.md
[circuit-breaker]: ../../patterns/circuit-breaker.md
[api-implementation]: ../../best-practices/api-implementation.md#considerations-for-optimizing-client-side-data-access
[NewRelic]: http://newrelic.com/azure
[NewRelic-server-requests]: _images/New-Relic.jpg
[Performance-Load-Test-Results-Uncached]:_images/InitialLoadTestResults.jpg
[Dynamic-Management-Views]: _images/SQLServerManagementStudio.jpg
[Performance-Load-Test-Results-Cached]: _images/CachedLoadTestResults.jpg