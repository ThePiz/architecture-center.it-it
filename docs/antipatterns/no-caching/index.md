---
title: Nessun antipattern della memorizzazione nella cache
titleSuffix: Performance antipatterns for cloud apps
description: Il recupero ripetuto degli stessi dati può ridurre le prestazioni e la scalabilità.
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 5c3062c0a17de708ada83ba81dcb111e6b1f8440
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58343850"
---
# <a name="no-caching-antipattern"></a><span data-ttu-id="47a5f-103">Nessun antipattern della memorizzazione nella cache</span><span class="sxs-lookup"><span data-stu-id="47a5f-103">No Caching antipattern</span></span>

<span data-ttu-id="47a5f-104">In un'applicazione cloud che gestisce molte richieste simultanee, il recupero ripetuto degli stessi dati può ridurre le prestazioni e la scalabilità.</span><span class="sxs-lookup"><span data-stu-id="47a5f-104">In a cloud application that handles many concurrent requests, repeatedly fetching the same data can reduce performance and scalability.</span></span>

## <a name="problem-description"></a><span data-ttu-id="47a5f-105">Descrizione del problema</span><span class="sxs-lookup"><span data-stu-id="47a5f-105">Problem description</span></span>

<span data-ttu-id="47a5f-106">Quando non vengono memorizzati i dati nella cache, possono verificarsi molti comportamenti indesiderati, tra cui:</span><span class="sxs-lookup"><span data-stu-id="47a5f-106">When data is not cached, it can cause a number of undesirable behaviors, including:</span></span>

- <span data-ttu-id="47a5f-107">Il recupero ripetuto delle stesse informazioni da una risorsa che è dispendiosa per l'accesso, in termini di sovraccarico di I/O o latenza.</span><span class="sxs-lookup"><span data-stu-id="47a5f-107">Repeatedly fetching the same information from a resource that is expensive to access, in terms of I/O overhead or latency.</span></span>
- <span data-ttu-id="47a5f-108">La costruzione ripetuta degli stessi oggetti o strutture di dati per più richieste.</span><span class="sxs-lookup"><span data-stu-id="47a5f-108">Repeatedly constructing the same objects or data structures for multiple requests.</span></span>
- <span data-ttu-id="47a5f-109">Chiamare in modo eccessivo un servizio remoto che dispone di una quota di servizio e limita i client dopo un determinato limite.</span><span class="sxs-lookup"><span data-stu-id="47a5f-109">Making excessive calls to a remote service that has a service quota and throttles clients past a certain limit.</span></span>

<span data-ttu-id="47a5f-110">A sua volta, questi problemi possono portare a tempi di risposta inadeguati, a una maggiore contesa nell'archivio dati e a una scarsa scalabilità.</span><span class="sxs-lookup"><span data-stu-id="47a5f-110">In turn, these problems can lead to poor response times, increased contention in the data store, and poor scalability.</span></span>

<span data-ttu-id="47a5f-111">L'esempio seguente usa Entity Framework per connettersi a un database.</span><span class="sxs-lookup"><span data-stu-id="47a5f-111">The following example uses Entity Framework to connect to a database.</span></span> <span data-ttu-id="47a5f-112">Ogni richiesta del client comporta una chiamata al database, anche se più richieste stiano recuperando esattamente gli stessi dati.</span><span class="sxs-lookup"><span data-stu-id="47a5f-112">Every client request results in a call to the database, even if multiple requests are fetching exactly the same data.</span></span> <span data-ttu-id="47a5f-113">Il costo delle richieste ripetute, in termini di sovraccarico di I/O e di addebiti di accesso ai dati, può accumularsi rapidamente.</span><span class="sxs-lookup"><span data-stu-id="47a5f-113">The cost of repeated requests, in terms of I/O overhead and data access charges, can accumulate quickly.</span></span>

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

<span data-ttu-id="47a5f-114">L'esempio completo è disponibile [qui][sample-app].</span><span class="sxs-lookup"><span data-stu-id="47a5f-114">You can find the complete sample [here][sample-app].</span></span>

<span data-ttu-id="47a5f-115">In genere questo antipattern si verifica perché:</span><span class="sxs-lookup"><span data-stu-id="47a5f-115">This antipattern typically occurs because:</span></span>

- <span data-ttu-id="47a5f-116">Il mancato utilizzo di una cache è più semplice da implementare e funziona correttamente con carichi bassi.</span><span class="sxs-lookup"><span data-stu-id="47a5f-116">Not using a cache is simpler to implement, and it works fine under low loads.</span></span> <span data-ttu-id="47a5f-117">La memorizzazione nella cache rende il codice più complesso.</span><span class="sxs-lookup"><span data-stu-id="47a5f-117">Caching makes the code more complicated.</span></span>
- <span data-ttu-id="47a5f-118">I vantaggi e gli svantaggi dell'uso di una cache non sono ben compresi.</span><span class="sxs-lookup"><span data-stu-id="47a5f-118">The benefits and drawbacks of using a cache are not clearly understood.</span></span>
- <span data-ttu-id="47a5f-119">La preoccupazione riguarda il sovraccarico di gestione dell'accuratezza e dell'aggiornamento dei dati memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="47a5f-119">There is concern about the overhead of maintaining the accuracy and freshness of cached data.</span></span>
- <span data-ttu-id="47a5f-120">È stata eseguita la migrazione di un'applicazione da un sistema locale, in cui la latenza di rete non rappresentava un problema, e il sistema ha lavorato su un hardware impegnativo ad alte prestazioni, pertanto la memorizzazione nella cache non è stata considerata nella progettazione originale.</span><span class="sxs-lookup"><span data-stu-id="47a5f-120">An application was migrated from an on-premises system, where network latency was not an issue, and the system ran on expensive high-performance hardware, so caching wasn't considered in the original design.</span></span>
- <span data-ttu-id="47a5f-121">Gli sviluppatori non sono a conoscenza del fatto che la memorizzazione nella cache rappresenta una possibilità in uno scenario specifico.</span><span class="sxs-lookup"><span data-stu-id="47a5f-121">Developers aren't aware that caching is a possibility in a given scenario.</span></span> <span data-ttu-id="47a5f-122">Ad esempio, gli sviluppatori possono non ritenere opportuno l'uso di ETag durante l'implementazione di un'API web.</span><span class="sxs-lookup"><span data-stu-id="47a5f-122">For example, developers may not think of using ETags when implementing a web API.</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="47a5f-123">Come risolvere il problema</span><span class="sxs-lookup"><span data-stu-id="47a5f-123">How to fix the problem</span></span>

<span data-ttu-id="47a5f-124">La strategia di memorizzazione nella cache più diffusa è quella *su richiesta* o *cache-aside*.</span><span class="sxs-lookup"><span data-stu-id="47a5f-124">The most popular caching strategy is the *on-demand* or *cache-aside* strategy.</span></span>

- <span data-ttu-id="47a5f-125">Nel percorso di lettura, l'applicazione tenta di leggere i dati dalla cache.</span><span class="sxs-lookup"><span data-stu-id="47a5f-125">On read, the application tries to read the data from the cache.</span></span> <span data-ttu-id="47a5f-126">Se i dati non sono nella cache, l'applicazione li recupera dall'origine dati e li aggiunge alla cache.</span><span class="sxs-lookup"><span data-stu-id="47a5f-126">If the data isn't in the cache, the application retrieves it from the data source and adds it to the cache.</span></span>
- <span data-ttu-id="47a5f-127">Nel percorso di scrittura, l'applicazione scrive la modifica direttamente all'origine dati e rimuove il valore precedente dalla cache.</span><span class="sxs-lookup"><span data-stu-id="47a5f-127">On write, the application writes the change directly to the data source and removes the old value from the cache.</span></span> <span data-ttu-id="47a5f-128">Verrà recuperato e aggiunto alla cache la volta successiva in cui è richiesto.</span><span class="sxs-lookup"><span data-stu-id="47a5f-128">It will be retrieved and added to the cache the next time it is required.</span></span>

<span data-ttu-id="47a5f-129">Questo approccio è adatto per i dati vengono modificati frequentemente.</span><span class="sxs-lookup"><span data-stu-id="47a5f-129">This approach is suitable for data that changes frequently.</span></span> <span data-ttu-id="47a5f-130">Di seguito è riportato l'esempio precedente aggiornato per usare il modello [cache-aside][cache-aside-pattern].</span><span class="sxs-lookup"><span data-stu-id="47a5f-130">Here is the previous example updated to use the [Cache-Aside][cache-aside-pattern] pattern.</span></span>

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

<span data-ttu-id="47a5f-131">Si noti che il metodo `GetAsync` ora chiama la classe `CacheService`, invece di chiamare direttamente il database.</span><span class="sxs-lookup"><span data-stu-id="47a5f-131">Notice that the `GetAsync` method now calls the `CacheService` class, rather than calling the database directly.</span></span> <span data-ttu-id="47a5f-132">La classe `CacheService` tenta innanzitutto di ottenere l'elemento dalla Cache Redis di Azure.</span><span class="sxs-lookup"><span data-stu-id="47a5f-132">The `CacheService` class first tries to get the item from Azure Redis Cache.</span></span> <span data-ttu-id="47a5f-133">Se il valore non viene trovato nella Cache Redis, `CacheService` richiama una funzione lambda che gli è stata passata dal chiamante.</span><span class="sxs-lookup"><span data-stu-id="47a5f-133">If the value isn't found in Redis Cache, the `CacheService` invokes a lambda function that was passed to it by the caller.</span></span> <span data-ttu-id="47a5f-134">La funzione lambda ha il compito di recuperare i dati dal database.</span><span class="sxs-lookup"><span data-stu-id="47a5f-134">The lambda function is responsible for fetching the data from the database.</span></span> <span data-ttu-id="47a5f-135">Questa implementazione separa il repository dalla particolare soluzione di memorizzazione nella cache e separa `CacheService` dal database.</span><span class="sxs-lookup"><span data-stu-id="47a5f-135">This implementation decouples the repository from the particular caching solution, and decouples the `CacheService` from the database.</span></span>

## <a name="considerations"></a><span data-ttu-id="47a5f-136">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="47a5f-136">Considerations</span></span>

- <span data-ttu-id="47a5f-137">Se la cache non è disponibile, probabilmente a causa di un errore temporaneo, non restituisce un errore al client.</span><span class="sxs-lookup"><span data-stu-id="47a5f-137">If the cache is unavailable, perhaps because of a transient failure, don't return an error to the client.</span></span> <span data-ttu-id="47a5f-138">In alternativa recuperare i dati dall'origine dati di partenza.</span><span class="sxs-lookup"><span data-stu-id="47a5f-138">Instead, fetch the data from the original data source.</span></span> <span data-ttu-id="47a5f-139">Tuttavia, tenere conto del fatto che mentre viene recuperata la cache, l'archivio dati originale potrebbe essere sovraccaricato da richieste, con conseguenti timeout ed errori di connessione</span><span class="sxs-lookup"><span data-stu-id="47a5f-139">However, be aware that while the cache is being recovered, the original data store could be swamped with requests, resulting in timeouts and failed connections.</span></span> <span data-ttu-id="47a5f-140">(dopo tutto, questo è uno dei motivi per usare una cache in primo luogo). Usare una tecnica come ad esempio il [Modello di interruttore][circuit-breaker] per evitare di sovraccaricare l'origine dati.</span><span class="sxs-lookup"><span data-stu-id="47a5f-140">(After all, this is one of the motivations for using a cache in the first place.) Use a technique such as the [Circuit Breaker pattern][circuit-breaker] to avoid overwhelming the data source.</span></span>

- <span data-ttu-id="47a5f-141">Le applicazioni che memorizzano nella cache dati non statici devono essere progettate per supportare la coerenza finale.</span><span class="sxs-lookup"><span data-stu-id="47a5f-141">Applications that cache nonstatic data should be designed to support eventual consistency.</span></span>

- <span data-ttu-id="47a5f-142">Per le API web, è possibile supportare la cache lato client includendo un'intestazione Cache-Control nei messaggi di richiesta e risposta, e usando i valori eTag per identificare le versioni degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="47a5f-142">For web APIs, you can support client-side caching by including a Cache-Control header in request and response messages, and using ETags to identify versions of objects.</span></span> <span data-ttu-id="47a5f-143">Per altre informazioni, vedere [implementazione delle API][api-implementation].</span><span class="sxs-lookup"><span data-stu-id="47a5f-143">For more information, see [API implementation][api-implementation].</span></span>

- <span data-ttu-id="47a5f-144">Non è necessario memorizzare nella cache intere entità.</span><span class="sxs-lookup"><span data-stu-id="47a5f-144">You don't have to cache entire entities.</span></span> <span data-ttu-id="47a5f-145">Se la maggior parte di un'entità è statico ma solo una piccola parte cambia frequentemente, memorizzare nella cache gli elementi statici e recuperare gli elementi dinamici dall'origine dati.</span><span class="sxs-lookup"><span data-stu-id="47a5f-145">If most of an entity is static but only a small piece changes frequently, cache the static elements and retrieve the dynamic elements from the data source.</span></span> <span data-ttu-id="47a5f-146">Questo approccio aiuta a ridurre il volume di I/O eseguito sull'origine dati.</span><span class="sxs-lookup"><span data-stu-id="47a5f-146">This approach can help to reduce the volume of I/O being performed against the data source.</span></span>

- <span data-ttu-id="47a5f-147">In alcuni casi, se i dati volatili sono di breve durata, può essere utile memorizzarli nella cache.</span><span class="sxs-lookup"><span data-stu-id="47a5f-147">In some cases, if volatile data is short-lived, it can be useful to cache it.</span></span> <span data-ttu-id="47a5f-148">Si consideri ad esempio un dispositivo che invia continuamente gli aggiornamenti di stato.</span><span class="sxs-lookup"><span data-stu-id="47a5f-148">For example, consider a device that continually sends status updates.</span></span> <span data-ttu-id="47a5f-149">Potrebbe essere opportuno memorizzare nella cache queste informazioni non appena arrivano e non scriverle affatto in un archivio permanente.</span><span class="sxs-lookup"><span data-stu-id="47a5f-149">It might make sense to cache this information as it arrives, and not write it to a persistent store at all.</span></span>

- <span data-ttu-id="47a5f-150">Per impedire l'obsolescenza dei dati, molte soluzioni di memorizzazione nella cache supportano periodi di scadenza configurabili, in modo che i dati vengono automaticamente rimossi dalla cache dopo un intervallo specificato.</span><span class="sxs-lookup"><span data-stu-id="47a5f-150">To prevent data from becoming stale, many caching solutions support configurable expiration periods, so that data is automatically removed from the cache after a specified interval.</span></span> <span data-ttu-id="47a5f-151">Potrebbe essere necessario regolare l'ora di scadenza per il proprio scenario.</span><span class="sxs-lookup"><span data-stu-id="47a5f-151">You may need to tune the expiration time for your scenario.</span></span> <span data-ttu-id="47a5f-152">I dati che sono molto statici possono rimanere nella cache per periodi di tempo più lunghi rispetto ai dati volatili che possono diventare rapidamente obsoleti.</span><span class="sxs-lookup"><span data-stu-id="47a5f-152">Data that is highly static can stay in the cache for longer periods than volatile data that may become stale quickly.</span></span>

- <span data-ttu-id="47a5f-153">Se la soluzione di memorizzazione nella cache non fornisce una scadenza incorporata, potrebbe essere necessario implementare un processo in background che occasionalmente organizza la cache per impedire l'aumento illimitato delle dimensioni.</span><span class="sxs-lookup"><span data-stu-id="47a5f-153">If the caching solution doesn't provide built-in expiration, you may need to implement a background process that occasionally sweeps the cache, to prevent it from growing without limits.</span></span>

- <span data-ttu-id="47a5f-154">Oltre alla memorizzazione nella cache dei dati da un'origine dati esterna, è possibile usare la memorizzazione nella cache per salvare i risultati di calcoli complessi.</span><span class="sxs-lookup"><span data-stu-id="47a5f-154">Besides caching data from an external data source, you can use caching to save the results of complex computations.</span></span> <span data-ttu-id="47a5f-155">Prima di eseguire questa operazione, tuttavia, instrumentare l'applicazione per determinare se l'applicazione è realmente associata alla CPU.</span><span class="sxs-lookup"><span data-stu-id="47a5f-155">Before you do that, however, instrument the application to determine whether the application is really CPU bound.</span></span>

- <span data-ttu-id="47a5f-156">Potrebbe essere utile preparare la cache all'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="47a5f-156">It might be useful to prime the cache when the application starts.</span></span> <span data-ttu-id="47a5f-157">Popolare la cache con i dati che sono probabilmente da usare.</span><span class="sxs-lookup"><span data-stu-id="47a5f-157">Populate the cache with the data that is most likely to be used.</span></span>

- <span data-ttu-id="47a5f-158">Includere sempre strumentazione che rileva i riscontri nella cache e i mancati riscontri nella cache.</span><span class="sxs-lookup"><span data-stu-id="47a5f-158">Always include instrumentation that detects cache hits and cache misses.</span></span> <span data-ttu-id="47a5f-159">Usare queste informazioni per ottimizzare i criteri di memorizzazione nella cache, come ad esempio quali dati memorizzare nella cache e per quanto tempo contenere i dati nella cache prima della scadenza.</span><span class="sxs-lookup"><span data-stu-id="47a5f-159">Use this information to tune caching policies, such what data to cache, and how long to hold data in the cache before it expires.</span></span>

- <span data-ttu-id="47a5f-160">Se la mancanza di memorizzazione nella cache è un collo di bottiglia, aggiungere la memorizzazione nella cache può aumentare il volume di richieste a tal punto che il front-end web diventa sovraccarico.</span><span class="sxs-lookup"><span data-stu-id="47a5f-160">If the lack of caching is a bottleneck, then adding caching may increase the volume of requests so much that the web front end becomes overloaded.</span></span> <span data-ttu-id="47a5f-161">I client possono iniziare a ricevere gli errori HTTP 503 (Servizio non disponibile).</span><span class="sxs-lookup"><span data-stu-id="47a5f-161">Clients may start to receive HTTP 503 (Service Unavailable) errors.</span></span> <span data-ttu-id="47a5f-162">Si tratta di un'indicazione del fatto che è necessario scalare orizzontalmente il front-end.</span><span class="sxs-lookup"><span data-stu-id="47a5f-162">These are an indication that you should scale out the front end.</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="47a5f-163">Come rilevare il problema</span><span class="sxs-lookup"><span data-stu-id="47a5f-163">How to detect the problem</span></span>

<span data-ttu-id="47a5f-164">È possibile eseguire la procedura seguente per identificare se la mancanza di memorizzazione nella cache sta provocando problemi alle prestazioni:</span><span class="sxs-lookup"><span data-stu-id="47a5f-164">You can perform the following steps to help identify whether lack of caching is causing performance problems:</span></span>

1. <span data-ttu-id="47a5f-165">Verificare la progettazione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="47a5f-165">Review the application design.</span></span> <span data-ttu-id="47a5f-166">Eseguire un inventario di tutti gli archivi dati usati dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="47a5f-166">Take an inventory of all the data stores that the application uses.</span></span> <span data-ttu-id="47a5f-167">Per ciascuno, determinare se l'applicazione sta usando una cache.</span><span class="sxs-lookup"><span data-stu-id="47a5f-167">For each, determine whether the application is using a cache.</span></span> <span data-ttu-id="47a5f-168">Se possibile, determinare la frequenza con cui i dati vengono modificati.</span><span class="sxs-lookup"><span data-stu-id="47a5f-168">If possible, determine how frequently the data changes.</span></span> <span data-ttu-id="47a5f-169">Dei buoni candidati iniziali per la memorizzazione nella cache includono i dati che cambiano lentamente e i dati di riferimento statici che sono letti frequentemente.</span><span class="sxs-lookup"><span data-stu-id="47a5f-169">Good initial candidates for caching include data that changes slowly, and static reference data that is read frequently.</span></span>

2. <span data-ttu-id="47a5f-170">Instrumentare l'applicazione e monitorare il sistema in tempo reale per individuare la frequenza con cui l'applicazione recupera i dati o calcola le informazioni.</span><span class="sxs-lookup"><span data-stu-id="47a5f-170">Instrument the application and monitor the live system to find out how frequently the application retrieves data or calculates information.</span></span>

3. <span data-ttu-id="47a5f-171">Profilare l'applicazione in un ambiente di test per acquisire le metriche di basso livello relative al sovraccarico associato a operazioni di accesso ai dati o altri calcoli eseguiti di frequente.</span><span class="sxs-lookup"><span data-stu-id="47a5f-171">Profile the application in a test environment to capture low-level metrics about the overhead associated with data access operations or other frequently performed calculations.</span></span>

4. <span data-ttu-id="47a5f-172">Eseguire test di carico in un ambiente di test per identificare come il sistema risponde con un carico di lavoro normale e un carico pesante.</span><span class="sxs-lookup"><span data-stu-id="47a5f-172">Perform load testing in a test environment to identify how the system responds under a normal workload and under heavy load.</span></span> <span data-ttu-id="47a5f-173">Il test di carico deve simulare il modello di accesso ai dati osservato nell'ambiente di produzione usando carichi realistici.</span><span class="sxs-lookup"><span data-stu-id="47a5f-173">Load testing should simulate the pattern of data access observed in the production environment using realistic workloads.</span></span>

5. <span data-ttu-id="47a5f-174">Esaminare le statistiche di accesso ai dati per sottolineare l'archivio dati ed eseguire la revisione della frequenza con cui le stesse richieste di dati vengono ripetute.</span><span class="sxs-lookup"><span data-stu-id="47a5f-174">Examine the data access statistics for the underlying data stores and review how often the same data requests are repeated.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="47a5f-175">Diagnosi di esempio</span><span class="sxs-lookup"><span data-stu-id="47a5f-175">Example diagnosis</span></span>

<span data-ttu-id="47a5f-176">Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="47a5f-176">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="instrument-the-application-and-monitor-the-live-system"></a><span data-ttu-id="47a5f-177">Instrumentare l'applicazione e monitorare il sistema in tempo reale</span><span class="sxs-lookup"><span data-stu-id="47a5f-177">Instrument the application and monitor the live system</span></span>

<span data-ttu-id="47a5f-178">Instrumentare l'applicazione e monitorarla per ottenere informazioni sulle specifiche richieste che gli utenti fanno mentre l'applicazione è in fase di produzione.</span><span class="sxs-lookup"><span data-stu-id="47a5f-178">Instrument the application and monitor it to get information about the specific requests that users make while the application is in production.</span></span>

<span data-ttu-id="47a5f-179">L'immagine seguente mostra il monitoraggio dei dati acquisiti da [New Relic][NewRelic] durante un test di carico.</span><span class="sxs-lookup"><span data-stu-id="47a5f-179">The following image shows monitoring data captured by [New Relic][NewRelic] during a load test.</span></span> <span data-ttu-id="47a5f-180">In questo caso, l'unica operazione HTTP GET eseguita è `Person/GetAsync`.</span><span class="sxs-lookup"><span data-stu-id="47a5f-180">In this case, the only HTTP GET operation performed is `Person/GetAsync`.</span></span> <span data-ttu-id="47a5f-181">Ma in un ambiente di produzione in tempo reale, conoscere la frequenza relativa in cui viene eseguita ogni richiesta può fornire informazioni dettagliate su quali risorse devono essere memorizzate nella cache.</span><span class="sxs-lookup"><span data-stu-id="47a5f-181">But in a live production environment, knowing the relative frequency that each request is performed can give you insight into which resources should be cached.</span></span>

![New Relic che mostra le richieste del server per l'applicazione CachingDemo][NewRelic-server-requests]

<span data-ttu-id="47a5f-183">Se è necessaria un'analisi più approfondita, è possibile usare un profiler per acquisire i dati delle prestazioni di basso livello in un ambiente di test (non il sistema di produzione).</span><span class="sxs-lookup"><span data-stu-id="47a5f-183">If you need a deeper analysis, you can use a profiler to capture low-level performance data in a test environment (not the production system).</span></span> <span data-ttu-id="47a5f-184">Esaminare le metriche, come ad esempio la frequenza delle richieste, l'uso della memoria, e l'uso della CPU.</span><span class="sxs-lookup"><span data-stu-id="47a5f-184">Look at metrics such as I/O request rates, memory usage, and CPU utilization.</span></span> <span data-ttu-id="47a5f-185">Queste metriche potrebbero mostrare un numero elevato di richieste a un archivio dati o a un servizio o l'elaborazione ripetuta che esegue lo stesso calcolo.</span><span class="sxs-lookup"><span data-stu-id="47a5f-185">These metrics may show a large number of requests to a data store or service, or repeated processing that performs the same calculation.</span></span>

### <a name="load-test-the-application"></a><span data-ttu-id="47a5f-186">Testare il carico dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="47a5f-186">Load test the application</span></span>

<span data-ttu-id="47a5f-187">Il grafico seguente mostra i risultati del test di carico dell'applicazione di esempio.</span><span class="sxs-lookup"><span data-stu-id="47a5f-187">The following graph shows the results of load testing the sample application.</span></span> <span data-ttu-id="47a5f-188">Il test di carico simula un caricamento del passaggio di un massimo di 800 utenti che eseguono una serie tipica di operazioni.</span><span class="sxs-lookup"><span data-stu-id="47a5f-188">The load test simulates a step load of up to 800 users performing a typical series of operations.</span></span>

![Risultati del test di carico delle prestazioni per lo scenario non memorizzato nella cache][Performance-Load-Test-Results-Uncached]

<span data-ttu-id="47a5f-190">Il numero di test con esito positivo eseguiti ogni secondo raggiunge una soglia e di conseguenza le richieste aggiuntive subiscono un rallentamento.</span><span class="sxs-lookup"><span data-stu-id="47a5f-190">The number of successful tests performed each second reaches a plateau, and additional requests are slowed as a result.</span></span> <span data-ttu-id="47a5f-191">Il tempo medio del test viene incrementato costantemente con il carico di lavoro.</span><span class="sxs-lookup"><span data-stu-id="47a5f-191">The average test time steadily increases with the workload.</span></span> <span data-ttu-id="47a5f-192">Il tempo di risposta raggiunge il picco quando il carico dell'utente raggiunge il picco.</span><span class="sxs-lookup"><span data-stu-id="47a5f-192">The response time levels off once the user load peaks.</span></span>

### <a name="examine-data-access-statistics"></a><span data-ttu-id="47a5f-193">Esaminare le statistiche dell'accesso ai dati</span><span class="sxs-lookup"><span data-stu-id="47a5f-193">Examine data access statistics</span></span>

<span data-ttu-id="47a5f-194">Le statistiche dell'accesso ai dati e altre informazioni fornite da un archivio dati può fornire informazioni utili, ad esempio quali query vengono ripetute più frequentemente.</span><span class="sxs-lookup"><span data-stu-id="47a5f-194">Data access statistics and other information provided by a data store can give useful information, such as which queries are repeated most frequently.</span></span> <span data-ttu-id="47a5f-195">Ad esempio, in Microsoft SQL Server, la gestione `sys.dm_exec_query_stats` dispone di informazioni statistiche per le query eseguite di recente.</span><span class="sxs-lookup"><span data-stu-id="47a5f-195">For example, in Microsoft SQL Server, the `sys.dm_exec_query_stats` management view has statistical information for recently executed queries.</span></span> <span data-ttu-id="47a5f-196">Il testo per ogni query è disponibile nella visualizzazione `sys.dm_exec-query_plan`.</span><span class="sxs-lookup"><span data-stu-id="47a5f-196">The text for each query is available in the `sys.dm_exec-query_plan` view.</span></span> <span data-ttu-id="47a5f-197">È possibile usare uno strumento come SQL Server Management Studio per eseguire la seguente query SQL e per determinare la frequenza con cui vengono eseguite le query.</span><span class="sxs-lookup"><span data-stu-id="47a5f-197">You can use a tool such as SQL Server Management Studio to run the following SQL query and determine how frequently queries are performed.</span></span>

```SQL
SELECT UseCounts, Text, Query_Plan
FROM sys.dm_exec_cached_plans
CROSS APPLY sys.dm_exec_sql_text(plan_handle)
CROSS APPLY sys.dm_exec_query_plan(plan_handle)
```

<span data-ttu-id="47a5f-198">La colonna `UseCount` nei risultati indica la frequenza con cui viene eseguita ogni query.</span><span class="sxs-lookup"><span data-stu-id="47a5f-198">The `UseCount` column in the results indicates how frequently each query is run.</span></span> <span data-ttu-id="47a5f-199">L'immagine seguente mostra che la terza query è stata eseguita più di 250.000 volte, molto di più di qualsiasi altra query.</span><span class="sxs-lookup"><span data-stu-id="47a5f-199">The following image shows that the third query was run more than 250,000 times, significantly more than any other query.</span></span>

![Risultati dell'esecuzione delle query sulle DMV nel Server di gestione di SQL Server][Dynamic-Management-Views]

<span data-ttu-id="47a5f-201">Di seguito è riportata la query SQL che sta causando così tante richieste di database:</span><span class="sxs-lookup"><span data-stu-id="47a5f-201">Here is the SQL query that is causing so many database requests:</span></span>

```SQL
(@p__linq__0 int)SELECT TOP (2)
[Extent1].[BusinessEntityId] AS [BusinessEntityId],
[Extent1].[FirstName] AS [FirstName],
[Extent1].[LastName] AS [LastName]
FROM [Person].[Person] AS [Extent1]
WHERE [Extent1].[BusinessEntityId] = @p__linq__0
```

<span data-ttu-id="47a5f-202">Di seguito è riportata la query che Entity Framework genera nel metodo `GetByIdAsync` illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="47a5f-202">This is the query that Entity Framework generates in `GetByIdAsync` method shown earlier.</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="47a5f-203">Implementare la soluzione e verificare il risultato</span><span class="sxs-lookup"><span data-stu-id="47a5f-203">Implement the solution and verify the result</span></span>

<span data-ttu-id="47a5f-204">In seguito si incorpora una cache, si ripetono i test di carico e si confrontano i risultati con i test di carico precedenti senza una cache.</span><span class="sxs-lookup"><span data-stu-id="47a5f-204">After you incorporate a cache, repeat the load tests and compare the results to the earlier load tests without a cache.</span></span> <span data-ttu-id="47a5f-205">Di seguito sono riportati i risultati del test di carico dopo l'aggiunta di una cache all'applicazione di esempio.</span><span class="sxs-lookup"><span data-stu-id="47a5f-205">Here are the load test results after adding a cache to the sample application.</span></span>

![Risultati del test di carico delle prestazioni per lo scenario memorizzato nella cache][Performance-Load-Test-Results-Cached]

<span data-ttu-id="47a5f-207">Il volume di test con esito positivo si stabilizza, ma a un carico maggiore dell'utente.</span><span class="sxs-lookup"><span data-stu-id="47a5f-207">The volume of successful tests still reaches a plateau, but at a higher user load.</span></span> <span data-ttu-id="47a5f-208">La frequenza delle richieste su questo carico è notevolmente superiore rispetto alla precedente.</span><span class="sxs-lookup"><span data-stu-id="47a5f-208">The request rate at this load is significantly higher than earlier.</span></span> <span data-ttu-id="47a5f-209">Il tempo medio per il test aumenta ancora con il carico, ma il tempo massimo di risposta è di 0,05 ms, confrontato con 1 ms precedente a &mdash; un miglioramento 20&times;.</span><span class="sxs-lookup"><span data-stu-id="47a5f-209">Average test time still increases with load, but the maximum response time is 0.05 ms, compared with 1ms earlier &mdash; a 20&times; improvement.</span></span>

## <a name="related-resources"></a><span data-ttu-id="47a5f-210">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="47a5f-210">Related resources</span></span>

- <span data-ttu-id="47a5f-211">[Procedure consigliate dell'implementazione API][api-implementation]</span><span class="sxs-lookup"><span data-stu-id="47a5f-211">[API implementation best practices][api-implementation]</span></span>
- <span data-ttu-id="47a5f-212">[Modello cache-aside][cache-aside-pattern]</span><span class="sxs-lookup"><span data-stu-id="47a5f-212">[Cache-Aside pattern][cache-aside-pattern]</span></span>
- <span data-ttu-id="47a5f-213">[Caching best practices][caching-guidance] (Procedure consigliate per la memorizzazione nella cache)</span><span class="sxs-lookup"><span data-stu-id="47a5f-213">[Caching best practices][caching-guidance]</span></span>
- <span data-ttu-id="47a5f-214">[Modello di interruttore][circuit-breaker]</span><span class="sxs-lookup"><span data-stu-id="47a5f-214">[Circuit Breaker pattern][circuit-breaker]</span></span>

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/NoCaching
[cache-aside-pattern]: /azure/architecture/patterns/cache-aside
[caching-guidance]: ../../best-practices/caching.md
[circuit-breaker]: ../../patterns/circuit-breaker.md
[api-implementation]: ../../best-practices/api-implementation.md#optimizing-client-side-data-access
[NewRelic]: https://newrelic.com/partner/azure
[NewRelic-server-requests]: _images/New-Relic.jpg
[Performance-Load-Test-Results-Uncached]:_images/InitialLoadTestResults.jpg
[Dynamic-Management-Views]: _images/SQLServerManagementStudio.jpg
[Performance-Load-Test-Results-Cached]: _images/CachedLoadTestResults.jpg
