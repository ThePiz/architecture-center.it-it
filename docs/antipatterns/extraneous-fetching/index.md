---
title: Antipattern estraneo in fase di recupero
description: Il recupero di più dati rispetto a quelli necessari per un'operazione commerciale può comportare un sovraccarico non necessario di I/O e ridurre i tempi di risposta.
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: 7a72bfd3e4b2e206f3266a046fac2083224ecb4f
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30846604"
---
# <a name="extraneous-fetching-antipattern"></a><span data-ttu-id="1327f-103">Antipattern di recupero estraneo</span><span class="sxs-lookup"><span data-stu-id="1327f-103">Extraneous Fetching antipattern</span></span>

<span data-ttu-id="1327f-104">Il recupero di più dati rispetto a quelli necessari per un'operazione commerciale può comportare un sovraccarico non necessario di I/O e ridurre i tempi di risposta.</span><span class="sxs-lookup"><span data-stu-id="1327f-104">Retrieving more data than needed for a business operation can result in unnecessary I/O overhead and reduce responsiveness.</span></span> 

## <a name="problem-description"></a><span data-ttu-id="1327f-105">Descrizione del problema</span><span class="sxs-lookup"><span data-stu-id="1327f-105">Problem description</span></span>

<span data-ttu-id="1327f-106">Questo antipattern può verificarsi se l'applicazione prova a ridurre al minimo le richieste I/O recuperando tutti i dati che *potrebbero* essere necessari.</span><span class="sxs-lookup"><span data-stu-id="1327f-106">This antipattern can occur if the application tries to minimize I/O requests by retrieving all of the data that it *might* need.</span></span> <span data-ttu-id="1327f-107">Spesso si tratta di un risultato della sovracompensazione per l'antipattern [I/O "frammentato"][chatty-io].</span><span class="sxs-lookup"><span data-stu-id="1327f-107">This is often a result of overcompensating for the [Chatty I/O][chatty-io] antipattern.</span></span> <span data-ttu-id="1327f-108">Ad esempio, un'applicazione può recuperare i dettagli per ogni prodotto in un database.</span><span class="sxs-lookup"><span data-stu-id="1327f-108">For example, an application might fetch the details for every product in a database.</span></span> <span data-ttu-id="1327f-109">Ma l'utente potrebbe aver bisogno solamente di un subset dei dettagli (alcuni potrebbero non essere rilevanti per i clienti) e probabilmente non aver bisogno di vedere *tutti* i prodotti in una sola volta.</span><span class="sxs-lookup"><span data-stu-id="1327f-109">But the user may need just a subset of the details (some may not be relevant to customers), and probably doesn't need to see *all* of the products at once.</span></span> <span data-ttu-id="1327f-110">Anche se l'utente sfoglia l'intero catalogo, potrebbe essere più utile impaginare i risultati &mdash; mostrandone 20 alla volta, ad esempio.</span><span class="sxs-lookup"><span data-stu-id="1327f-110">Even if the user is browsing the entire catalog, it would make sense to paginate the results &mdash; showing 20 at a time, for example.</span></span>

<span data-ttu-id="1327f-111">Un'altra origine di questo problema è quella di seguire delle programmazioni o delle procedure consigliate di progettazione non ottimali.</span><span class="sxs-lookup"><span data-stu-id="1327f-111">Another source of this problem is following poor programming or design practices.</span></span> <span data-ttu-id="1327f-112">Ad esempio, il codice seguente usa Entity Framework per recuperare i dettagli completi per ogni prodotto.</span><span class="sxs-lookup"><span data-stu-id="1327f-112">For example, the following code uses Entity Framework to fetch the complete details for every product.</span></span> <span data-ttu-id="1327f-113">Poi filtra i risultati per restituire solo un subset dei campi, ignorando il resto.</span><span class="sxs-lookup"><span data-stu-id="1327f-113">Then it filters the results to return only a subset of the fields, discarding the rest.</span></span> <span data-ttu-id="1327f-114">L'esempio completo è disponibile [qui][sample-app].</span><span class="sxs-lookup"><span data-stu-id="1327f-114">You can find the complete sample [here][sample-app].</span></span>

```csharp
public async Task<IHttpActionResult> GetAllFieldsAsync()
{
    using (var context = new AdventureWorksContext())
    {
        // Execute the query. This happens at the database.
        var products = await context.Products.ToListAsync();

        // Project fields from the query results. This happens in application memory.
        var result = products.Select(p => new ProductInfo { Id = p.ProductId, Name = p.Name });
        return Ok(result);
    }
}
```

<span data-ttu-id="1327f-115">Nell'esempio successivo l'applicazione recupera i dati per eseguire un'aggregazione che poteva essere invece eseguita dal database.</span><span class="sxs-lookup"><span data-stu-id="1327f-115">In the next example, the application retrieves data to perform an aggregation that could be done by the database instead.</span></span> <span data-ttu-id="1327f-116">L'applicazione calcola le vendite totali raccogliendo ogni record per tutti gli ordini venduti e poi calcolando la somma su tali record.</span><span class="sxs-lookup"><span data-stu-id="1327f-116">The application calculates total sales by getting every record for all orders sold, and then computing the sum over those records.</span></span> <span data-ttu-id="1327f-117">L'esempio completo è disponibile [qui][sample-app].</span><span class="sxs-lookup"><span data-stu-id="1327f-117">You can find the complete sample [here][sample-app].</span></span>

```csharp
public async Task<IHttpActionResult> AggregateOnClientAsync()
{
    using (var context = new AdventureWorksContext())
    {
        // Fetch all order totals from the database.
        var orderAmounts = await context.SalesOrderHeaders.Select(soh => soh.TotalDue).ToListAsync();

        // Sum the order totals in memory.
        var total = orderAmounts.Sum();
        return Ok(total);
    }
}
```

<span data-ttu-id="1327f-118">L'esempio successivo mostra un problema complesso causato dal modo in cui Entity Framework usa LINQ to Entities.</span><span class="sxs-lookup"><span data-stu-id="1327f-118">The next example shows a subtle problem caused by the way Entity Framework uses LINQ to Entities.</span></span> 

```csharp
var query = from p in context.Products.AsEnumerable()
            where p.SellStartDate < DateTime.Now.AddDays(-7) // AddDays cannot be mapped by LINQ to Entities
            select ...;

List<Product> products = query.ToList();
```

<span data-ttu-id="1327f-119">L'applicazione cerca di trovare i prodotti con un `SellStartDate` in più rispetto alla settimana precedente.</span><span class="sxs-lookup"><span data-stu-id="1327f-119">The application is trying to find products with a `SellStartDate` more than a week old.</span></span> <span data-ttu-id="1327f-120">Nella maggior parte dei casi, LINQ to Entities converte una clausola `where` in un'istruzione SQL che viene eseguita dal database.</span><span class="sxs-lookup"><span data-stu-id="1327f-120">In most cases, LINQ to Entities would translate a `where` clause to a SQL statement that is executed by the database.</span></span> <span data-ttu-id="1327f-121">In questo caso, tuttavia, LINQ to Entities non può eseguire il mapping del metodo `AddDays` in SQL.</span><span class="sxs-lookup"><span data-stu-id="1327f-121">In this case, however, LINQ to Entities cannot map the `AddDays` method to SQL.</span></span> <span data-ttu-id="1327f-122">Al contrario, vengono restituite tutte le righe dalla tabella `Product` e i risultati vengono filtrati in memoria.</span><span class="sxs-lookup"><span data-stu-id="1327f-122">Instead, every row from the `Product` table is returned, and the results are filtered in memory.</span></span> 

<span data-ttu-id="1327f-123">La chiamata a `AsEnumerable` è un suggerimento che indica un problema.</span><span class="sxs-lookup"><span data-stu-id="1327f-123">The call to `AsEnumerable` is a hint that there is a problem.</span></span> <span data-ttu-id="1327f-124">Questo metodo converte i risultati in un'interfaccia `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="1327f-124">This method converts the results to an `IEnumerable` interface.</span></span> <span data-ttu-id="1327f-125">Sebbene `IEnumerable` supporti il filtro, il filtro viene applicato sul lato *client*, non sul database.</span><span class="sxs-lookup"><span data-stu-id="1327f-125">Although `IEnumerable` supports filtering, the filtering is done on the *client* side, not the database.</span></span> <span data-ttu-id="1327f-126">Per impostazione predefinita, LINQ to Entities usa `IQueryable`, che passa la responsabilità per il filtro all'origine dati.</span><span class="sxs-lookup"><span data-stu-id="1327f-126">By default, LINQ to Entities uses `IQueryable`, which passes the responsibility for filtering to the data source.</span></span> 

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="1327f-127">Come risolvere il problema</span><span class="sxs-lookup"><span data-stu-id="1327f-127">How to fix the problem</span></span>

<span data-ttu-id="1327f-128">Evitare il recupero di grandi volumi di dati che potrebbero presto risultare obsoleti o potrebbero essere scartati e recuperare solo i dati necessari per l'esecuzione dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="1327f-128">Avoid fetching large volumes of data that may quickly become outdated or might be discarded, and only fetch the data needed for the operation being performed.</span></span> 

<span data-ttu-id="1327f-129">Invece di ottenere ogni colonna da una tabella e filtrarla successivamente, selezionare le colonne necessarie dal database.</span><span class="sxs-lookup"><span data-stu-id="1327f-129">Instead of getting every column from a table and then filtering them, select the columns that you need from the database.</span></span>

```csharp
public async Task<IHttpActionResult> GetRequiredFieldsAsync()
{
    using (var context = new AdventureWorksContext())
    {
        // Project fields as part of the query itself
        var result = await context.Products
            .Select(p => new ProductInfo {Id = p.ProductId, Name = p.Name})
            .ToListAsync();
        return Ok(result);
    }
}
```

<span data-ttu-id="1327f-130">Analogamente, eseguire l'aggregazione nel database e non nella memoria dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1327f-130">Similarly, perform aggregation in the database and not in application memory.</span></span>

```csharp
public async Task<IHttpActionResult> AggregateOnDatabaseAsync()
{
    using (var context = new AdventureWorksContext())
    {
        // Sum the order totals as part of the database query.
        var total = await context.SalesOrderHeaders.SumAsync(soh => soh.TotalDue);
        return Ok(total);
    }
}
```

<span data-ttu-id="1327f-131">Quando si usa Entity Framework, verificare che le query LINQ vengano risolte usando l'interfaccia `IQueryable` e non `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="1327f-131">When using Entity Framework, ensure that LINQ queries are resolved using the `IQueryable`interface and not `IEnumerable`.</span></span> <span data-ttu-id="1327f-132">Potrebbe essere necessario modificare la query per usare solo le funzioni che possono essere mappate nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="1327f-132">You may need to adjust the query to use only functions that can be mapped to the data source.</span></span> <span data-ttu-id="1327f-133">L'esempio precedente può essere sottoposto a refactoring per rimuovere il metodo `AddDays` dalla query, consentendo di applicare un filtro dal database.</span><span class="sxs-lookup"><span data-stu-id="1327f-133">The earlier example can be refactored to remove the `AddDays` method from the query, allowing filtering to be done by the database.</span></span>

```csharp
DateTime dateSince = DateTime.Now.AddDays(-7); // AddDays has been factored out. 
var query = from p in context.Products
            where p.SellStartDate < dateSince // This criterion can be passed to the database by LINQ to Entities
            select ...;

List<Product> products = query.ToList();
```

## <a name="considerations"></a><span data-ttu-id="1327f-134">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="1327f-134">Considerations</span></span>

- <span data-ttu-id="1327f-135">In alcuni casi è possibile migliorare le prestazioni tramite il partizionamento orizzontale dei dati.</span><span class="sxs-lookup"><span data-stu-id="1327f-135">In some cases, you can improve performance by partitioning data horizontally.</span></span> <span data-ttu-id="1327f-136">Se diverse operazioni hanno accesso a diversi attributi dei dati, il partizionamento orizzontale può ridurre le contese.</span><span class="sxs-lookup"><span data-stu-id="1327f-136">If different operations access different attributes of the data, horizontal partitioning may reduce contention.</span></span> <span data-ttu-id="1327f-137">Spesso, la maggior parte delle operazioni vengono eseguite su un piccolo subset dei dati, pertanto se si estende il carico le prestazioni possono migliorare.</span><span class="sxs-lookup"><span data-stu-id="1327f-137">Often, most operations are run against a small subset of the data, so spreading this load may improve performance.</span></span> <span data-ttu-id="1327f-138">Vedere [Partizionamento dei dati][data-partitioning].</span><span class="sxs-lookup"><span data-stu-id="1327f-138">See [Data partitioning][data-partitioning].</span></span>

- <span data-ttu-id="1327f-139">Per le operazioni che devono supportare query unbounded, implementare l'impaginazione e recuperare solo un numero limitato di entità alla volta.</span><span class="sxs-lookup"><span data-stu-id="1327f-139">For operations that have to support unbounded queries, implement pagination and only fetch a limited number of entities at a time.</span></span> <span data-ttu-id="1327f-140">Ad esempio, se un cliente sta sfogliando un catalogo dei prodotti, è possibile visualizzare una pagina di risultati alla volta.</span><span class="sxs-lookup"><span data-stu-id="1327f-140">For example, if a customer is browsing a product catalog, you can show one page of results at a time.</span></span>

- <span data-ttu-id="1327f-141">Dove possibile, sfruttare i vantaggi delle funzionalità compilate nell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="1327f-141">When possible, take advantage of features built into the data store.</span></span> <span data-ttu-id="1327f-142">Ad esempio, i database SQL in genere forniscono funzioni di aggregazione.</span><span class="sxs-lookup"><span data-stu-id="1327f-142">For example, SQL databases typically provide aggregate functions.</span></span> 

- <span data-ttu-id="1327f-143">Se si usa un archivio dati che non supporta una determinata funzione, ad esempio l'aggregazione, è possibile archiviare il risultato calcolato altrove, aggiornando il valore a seconda dei record che vengono aggiunti o aggiornati, pertanto non è necessario ricalcolare il valore ogni volta che viene richiesto.</span><span class="sxs-lookup"><span data-stu-id="1327f-143">If you're using a data store that doesn't support a particular function, such as aggregration, you could store the calculated result elsewhere, updating the value as records are added or updated, so the application doesn't have to recalculate the value each time it's needed.</span></span>

- <span data-ttu-id="1327f-144">Se si osserva che le richieste stanno recuperando un numero elevato di campi, esaminare il codice sorgente per determinare se tutti questi campi sono effettivamente necessari.</span><span class="sxs-lookup"><span data-stu-id="1327f-144">If you see that requests are retrieving a large number of fields, examine the source code to determine whether all of these fields are actually necessary.</span></span> <span data-ttu-id="1327f-145">A volte queste richieste sono il risultato di una query `SELECT *` progettata in modo non adeguato.</span><span class="sxs-lookup"><span data-stu-id="1327f-145">Sometimes these requests are the result of poorly designed `SELECT *` query.</span></span> 

- <span data-ttu-id="1327f-146">Analogamente, le richieste che recuperano un numero elevato di entità potrebbero indicare che l'applicazione non filtra i dati correttamente.</span><span class="sxs-lookup"><span data-stu-id="1327f-146">Similarly, requests that retrieve a large number of entities may be sign that the application is not filtering data correctly.</span></span> <span data-ttu-id="1327f-147">Verificare che tutte queste entità siano effettivamente necessarie.</span><span class="sxs-lookup"><span data-stu-id="1327f-147">Verify that all of these entities are actually needed.</span></span> <span data-ttu-id="1327f-148">Usare il filtro dal lato del database se possibile, ad esempio, usando le clausole `WHERE` in SQL.</span><span class="sxs-lookup"><span data-stu-id="1327f-148">Use database-side filtering if possible, for example, by using `WHERE` clauses in SQL.</span></span> 

- <span data-ttu-id="1327f-149">Eseguire il processo di offload nel database non è sempre la scelta migliore.</span><span class="sxs-lookup"><span data-stu-id="1327f-149">Offloading processing to the database is not always the best option.</span></span> <span data-ttu-id="1327f-150">Usare questa strategia solo quando il database è progettato o ottimizzato per eseguire questa operazione.</span><span class="sxs-lookup"><span data-stu-id="1327f-150">Only use this strategy when the database is designed or optimized to do so.</span></span> <span data-ttu-id="1327f-151">La maggior parte dei sistemi di database è altamente ottimizzata per determinate funzioni, ma non è progettata per fungere da motori di applicazione generici.</span><span class="sxs-lookup"><span data-stu-id="1327f-151">Most database systems are highly optimized for certain functions, but are not designed to act as general-purpose application engines.</span></span> <span data-ttu-id="1327f-152">Per altre informazioni, vedere [Antipattern del database occupato][BusyDatabase].</span><span class="sxs-lookup"><span data-stu-id="1327f-152">For more information, see the [Busy Database antipattern][BusyDatabase].</span></span>


## <a name="how-to-detect-the-problem"></a><span data-ttu-id="1327f-153">Come rilevare il problema</span><span class="sxs-lookup"><span data-stu-id="1327f-153">How to detect the problem</span></span>

<span data-ttu-id="1327f-154">I sintomi del recupero di estranei includono una latenza elevata e una bassa velocità effettiva.</span><span class="sxs-lookup"><span data-stu-id="1327f-154">Symptoms of extraneous fetching include high latency and low throughput.</span></span> <span data-ttu-id="1327f-155">Se i dati vengono recuperati da un archivio dati, è probabile anche un aumento della contesa.</span><span class="sxs-lookup"><span data-stu-id="1327f-155">If the data is retrieved from a data store, increased contention is also probable.</span></span> <span data-ttu-id="1327f-156">Gli utenti finali riferiscono in genere tempi di risposta prolungati o errori causati dal timeout dei servizi. Questi errori potrebbero restituire errori HTTP 500 (Server interno) o errori HTTP 503 (Servizio non disponibile).</span><span class="sxs-lookup"><span data-stu-id="1327f-156">End users are likely to report extended response times or failures caused by services timing out. These failures could return HTTP 500 (Internal Server) errors or HTTP 503 (Service Unavailable) errors.</span></span> <span data-ttu-id="1327f-157">Esaminare i log di eventi per il server Web che possono contenere informazioni più dettagliate sulle cause e le circostanze degli errori.</span><span class="sxs-lookup"><span data-stu-id="1327f-157">Examine the event logs for the web server, which are likely to contain more detailed information about the causes and circumstances of the errors.</span></span>

<span data-ttu-id="1327f-158">I sintomi di questo antipattern e alcuni dei dati di telemetria ottenuti potrebbero essere molto simili a quelli dell'[Antipattern di Persistenza monolitica][MonolithicPersistence].</span><span class="sxs-lookup"><span data-stu-id="1327f-158">The symptoms of this antipattern and some of the telemetry obtained might be very similar to those of the [Monolithic Persistence antipattern][MonolithicPersistence].</span></span> 

<span data-ttu-id="1327f-159">È possibile eseguire la procedura seguente per identificare la causa:</span><span class="sxs-lookup"><span data-stu-id="1327f-159">You can perform the following steps to help identify the cause:</span></span>

1. <span data-ttu-id="1327f-160">Identificare i carichi di lavoro o le transazioni lenti tramite l'esecuzione di test di carico, il monitoraggio del processo o altri metodi di acquisizione dei dati di strumentazione.</span><span class="sxs-lookup"><span data-stu-id="1327f-160">Identify slow workloads or transactions by performing load-testing, process monitoring, or other methods of capturing instrumentation data.</span></span>
2. <span data-ttu-id="1327f-161">Osservare gli eventuali modelli di comportamento mostrati dal sistema.</span><span class="sxs-lookup"><span data-stu-id="1327f-161">Observe any behavioral patterns exhibited by the system.</span></span> <span data-ttu-id="1327f-162">Esistono limiti particolari in termini di transazioni al secondo o di volume degli utenti?</span><span class="sxs-lookup"><span data-stu-id="1327f-162">Are there particular limits in terms of transactions per second or volume of users?</span></span>
3. <span data-ttu-id="1327f-163">Correlare le istanze dei carichi di lavoro lenti con i modelli di comportamento.</span><span class="sxs-lookup"><span data-stu-id="1327f-163">Correlate the instances of slow workloads with behavioral patterns.</span></span>
4. <span data-ttu-id="1327f-164">Identificare gli archivi dati in uso.</span><span class="sxs-lookup"><span data-stu-id="1327f-164">Identify the data stores being used.</span></span> <span data-ttu-id="1327f-165">Per ogni origine dati, eseguire la telemetria di livello inferiore per osservare il comportamento delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="1327f-165">For each data source, run lower level telemetry to observe the behavior of operations.</span></span>
6. <span data-ttu-id="1327f-166">Identificare le query a esecuzione lenta che fanno riferimento a queste origini dati.</span><span class="sxs-lookup"><span data-stu-id="1327f-166">Identify any slow-running queries that reference these data sources.</span></span>
7. <span data-ttu-id="1327f-167">Eseguire un'analisi delle risorse specifiche delle query a esecuzione lenta e verificare come vengono usati e consumati i dati.</span><span class="sxs-lookup"><span data-stu-id="1327f-167">Perform a resource-specific analysis of the slow-running queries and ascertain how the data is used and consumed.</span></span>

<span data-ttu-id="1327f-168">Cercare uno qualsiasi dei seguenti sintomi:</span><span class="sxs-lookup"><span data-stu-id="1327f-168">Look for any of these symptoms:</span></span>

- <span data-ttu-id="1327f-169">Richieste frequenti, elevate I/O presentate allo stesso archivio dati o risorsa.</span><span class="sxs-lookup"><span data-stu-id="1327f-169">Frequent, large I/O requests made to the same resource or data store.</span></span>
- <span data-ttu-id="1327f-170">Contesa in un archivio dati o di risorse condivise.</span><span class="sxs-lookup"><span data-stu-id="1327f-170">Contention in a shared resource or data store.</span></span>
- <span data-ttu-id="1327f-171">Un'operazione che riceve spesso grandi volumi di dati nella rete.</span><span class="sxs-lookup"><span data-stu-id="1327f-171">An operation that frequently receives large volumes of data over the network.</span></span>
- <span data-ttu-id="1327f-172">Applicazioni e servizi che impiegano un lasso di tempo consistente in attesa del completamento di I/O.</span><span class="sxs-lookup"><span data-stu-id="1327f-172">Applications and services spending significant time waiting for I/O to complete.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="1327f-173">Diagnosi di esempio</span><span class="sxs-lookup"><span data-stu-id="1327f-173">Example diagnosis</span></span>    

<span data-ttu-id="1327f-174">Nelle sezioni seguenti si applica questa procedura agli esempi precedenti.</span><span class="sxs-lookup"><span data-stu-id="1327f-174">The following sections apply these steps to the previous examples.</span></span>

### <a name="identify-slow-workloads"></a><span data-ttu-id="1327f-175">Identificare i carichi di lavoro lenti</span><span class="sxs-lookup"><span data-stu-id="1327f-175">Identify slow workloads</span></span>

<span data-ttu-id="1327f-176">Questo grafico mostra i risultati delle prestazioni da un test di carico che ha simulato fino a 400 utenti concorrenti che eseguono il metodo `GetAllFieldsAsync` illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="1327f-176">This graph shows performance results from a load test that simulated up to 400 concurrent users running the `GetAllFieldsAsync` method shown earlier.</span></span> <span data-ttu-id="1327f-177">La velocità effettiva diminuisce lentamente man mano che aumenta il carico.</span><span class="sxs-lookup"><span data-stu-id="1327f-177">Throughput diminishes slowly as the load increases.</span></span> <span data-ttu-id="1327f-178">Il tempo medio di risposta aumenta man mano che aumenta il carico di lavoro.</span><span class="sxs-lookup"><span data-stu-id="1327f-178">Average response time goes up as the workload increases.</span></span> 

![Risultati del test di carico per il metodo GetAllFieldsAsync][Load-Test-Results-Client-Side1]

<span data-ttu-id="1327f-180">Un test di carico per l'operazione `AggregateOnClientAsync` mostra un modello simile.</span><span class="sxs-lookup"><span data-stu-id="1327f-180">A load test for the `AggregateOnClientAsync` operation shows a similar pattern.</span></span> <span data-ttu-id="1327f-181">Il volume delle richieste è abbastanza stabile.</span><span class="sxs-lookup"><span data-stu-id="1327f-181">The volume of requests is reasonably stable.</span></span> <span data-ttu-id="1327f-182">Il tempo medio di risposta aumenta con il carico di lavoro, anche se più lentamente rispetto al grafico precedente.</span><span class="sxs-lookup"><span data-stu-id="1327f-182">The average response time increases with the workload, although more slowly than the previous graph.</span></span>

![I risultati del test di carico per il metodo AggregateOnClientAsync][Load-Test-Results-Client-Side2]

### <a name="correlate-slow-workloads-with-behavioral-patterns"></a><span data-ttu-id="1327f-184">Correlare i carichi di lavoro lenti con i modelli di comportamento</span><span class="sxs-lookup"><span data-stu-id="1327f-184">Correlate slow workloads with behavioral patterns</span></span>

<span data-ttu-id="1327f-185">Eventuali correlazioni tra i normali periodi di utilizzo elevato e il rallentamento delle prestazioni possono indicare aree problematiche.</span><span class="sxs-lookup"><span data-stu-id="1327f-185">Any correlation between regular periods of high usage and slowing performance can indicate areas of concern.</span></span> <span data-ttu-id="1327f-186">Esaminare attentamente il profilo delle prestazioni della funzionalità di cui si sospetta l'esecuzione lenta per determinare se corrisponde con il test di carico eseguito in precedenza.</span><span class="sxs-lookup"><span data-stu-id="1327f-186">Closely examine the performance profile of functionality that is suspected to be slow running, to determine whether it matches the load testing performed earlier.</span></span>

<span data-ttu-id="1327f-187">Eseguire il test di carico sulla stessa funzionalità usando carichi utente basati sul passaggio per trovare il punto in cui le prestazioni scendono in modo significativo o non riescono affatto.</span><span class="sxs-lookup"><span data-stu-id="1327f-187">Load test the same functionality using step-based user loads, to find the point where performance drops significantly or fails completely.</span></span> <span data-ttu-id="1327f-188">Se tale punto è compreso entro i limiti dell'uso reale previsto, esaminare in che modo è implementata la funzionalità.</span><span class="sxs-lookup"><span data-stu-id="1327f-188">If that point falls within the bounds of your expected real-world usage, examine how the functionality is implemented.</span></span>

<span data-ttu-id="1327f-189">Un'operazione lenta non è necessariamente un problema, se non viene eseguita quando il sistema è in condizioni di stress, non ha una situazione problematica a livello di tempo e non influisce negativamente sulle prestazioni di altre operazioni importanti.</span><span class="sxs-lookup"><span data-stu-id="1327f-189">A slow operation is not necessarily a problem, if it is not being performed when the system is under stress, is not time critical, and does not negatively affect the performance of other important operations.</span></span> <span data-ttu-id="1327f-190">Ad esempio, la generazione di statistiche operative mensili potrebbe essere un'operazione con esecuzione prolungata, ma probabilmente può essere eseguita come processo batch e come processo con priorità bassa.</span><span class="sxs-lookup"><span data-stu-id="1327f-190">For example, generating monthly operational statistics might be a long-running operation, but it can probably be performed as a batch process and run as a low priority job.</span></span> <span data-ttu-id="1327f-191">D'altra parte, l'esecuzione di query del catalogo dei prodotti da parte dei clienti è un'operazione aziendale critica.</span><span class="sxs-lookup"><span data-stu-id="1327f-191">On the other hand, customers querying the product catalog is a critical business operation.</span></span> <span data-ttu-id="1327f-192">Concentrarsi sui dati di telemetria generati da queste operazioni critiche per osservare come variano le prestazioni durante i periodi di utilizzo elevato.</span><span class="sxs-lookup"><span data-stu-id="1327f-192">Focus on the telemetry generated by these critical operations to see how the performance varies during periods of high usage.</span></span>

### <a name="identify-data-sources-in-slow-workloads"></a><span data-ttu-id="1327f-193">Identificare le origini dati nei carichi di lavoro lenti</span><span class="sxs-lookup"><span data-stu-id="1327f-193">Identify data sources in slow workloads</span></span>

<span data-ttu-id="1327f-194">Se si ritiene che un servizio viene eseguito in modo inadeguato a causa del modo in cui avviene il recupero dei dati, esaminare come l'applicazione interagisce con il repository che usa.</span><span class="sxs-lookup"><span data-stu-id="1327f-194">If you suspect that a service is performing poorly because of the way it retrieves data, investigate how the application interacts with the repositories it uses.</span></span> <span data-ttu-id="1327f-195">Monitorare il sistema in tempo reale per vedere a quali origini si ha accesso durante i periodi di riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="1327f-195">Monitor the live system to see which sources are accessed during periods of poor performance.</span></span> 

<span data-ttu-id="1327f-196">Per ogni origine dati, instrumentare il sistema per acquisire gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1327f-196">For each data source, instrument the system to capture the following:</span></span>

- <span data-ttu-id="1327f-197">La frequenza con cui si ha accesso a ogni archivio dati.</span><span class="sxs-lookup"><span data-stu-id="1327f-197">The frequency that each data store is accessed.</span></span>
- <span data-ttu-id="1327f-198">Il volume dei dati che entrano ed escono dall'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="1327f-198">The volume of data entering and exiting the data store.</span></span>
- <span data-ttu-id="1327f-199">I tempi di queste operazioni, in particolare la latenza delle richieste.</span><span class="sxs-lookup"><span data-stu-id="1327f-199">The timing of these operations, especially the latency of requests.</span></span>
- <span data-ttu-id="1327f-200">La natura e la frequenza degli errori che si verificano durante l'accesso a ogni archivio dati con un carico tipico.</span><span class="sxs-lookup"><span data-stu-id="1327f-200">The nature and rate of any errors that occur while accessing each data store under typical load.</span></span>

<span data-ttu-id="1327f-201">Confrontare queste informazioni sul volume dei dati restituiti dall'applicazione al client.</span><span class="sxs-lookup"><span data-stu-id="1327f-201">Compare this information against the volume of data being returned by the application to the client.</span></span> <span data-ttu-id="1327f-202">Tenere traccia del rapporto del volume dei dati restituiti dall'archivio dati rispetto al volume dei dati restituiti al client.</span><span class="sxs-lookup"><span data-stu-id="1327f-202">Track the ratio of the volume of data returned by the data store against the volume of data returned to the client.</span></span> <span data-ttu-id="1327f-203">Se non vi è alcuna disparità di grandi dimensioni, indagare per determinare se l'applicazione recupera dati di cui non ha bisogno.</span><span class="sxs-lookup"><span data-stu-id="1327f-203">If there is any large disparity, investigate to determine whether the application is fetching data that it doesn't need.</span></span>

<span data-ttu-id="1327f-204">Potrebbe essere possibile acquisire questi dati osservando il sistema in tempo reale e tenendo traccia del ciclo di vita di ogni richiesta dell'utente oppure è possibile modellare una serie di carichi di lavoro sintetici ed eseguirli in relazione a un sistema di test.</span><span class="sxs-lookup"><span data-stu-id="1327f-204">You may be able to capture this data by observing the live system and tracing the lifecycle of each user request, or you can model a series of synthetic workloads and run them against a test system.</span></span>

<span data-ttu-id="1327f-205">I grafici seguenti mostrano la telemetria acquisita tramite [New Relic APM][new-relic] durante un test di carico del metodo `GetAllFieldsAsync`.</span><span class="sxs-lookup"><span data-stu-id="1327f-205">The following graphs show telemetry captured using [New Relic APM][new-relic] during a load test of the `GetAllFieldsAsync` method.</span></span> <span data-ttu-id="1327f-206">Si noti la differenza tra i volumi di dati ricevuti dal database e le risposte HTTP corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="1327f-206">Note the difference between the volumes of data received from the database and the corresponding HTTP responses.</span></span>

![Dati di telemetria per il metodo 'GetAllFieldsAsync'][TelemetryAllFields]

<span data-ttu-id="1327f-208">Per ogni richiesta, il database ha restituito 80.503 byte, ma la risposta al client conteneva solo 19.855 byte, circa il 25% delle dimensioni della risposta del database.</span><span class="sxs-lookup"><span data-stu-id="1327f-208">For each request, the database returned 80,503 bytes, but the response to the client only contained 19,855 bytes, about 25% of the size of the database response.</span></span> <span data-ttu-id="1327f-209">Le dimensioni dei dati restituiti al client possono variare a seconda del formato.</span><span class="sxs-lookup"><span data-stu-id="1327f-209">The size of the data returned to the client can vary depending on the format.</span></span> <span data-ttu-id="1327f-210">Per questo test di carico, il client ha richiesto i dati JSON.</span><span class="sxs-lookup"><span data-stu-id="1327f-210">For this load test, the client requested JSON data.</span></span> <span data-ttu-id="1327f-211">L'esecuzione di test separati usando XML (non illustrato) ha avuto una dimensione della risposta di 35.655 byte, o del 44% delle dimensioni della risposta del database.</span><span class="sxs-lookup"><span data-stu-id="1327f-211">Separate testing using XML (not shown) had a response size of 35,655 bytes, or 44% of the size of the database response.</span></span>

<span data-ttu-id="1327f-212">Il test di carico per il metodo `AggregateOnClientAsync` mostra più risultati estremi.</span><span class="sxs-lookup"><span data-stu-id="1327f-212">The load test for the `AggregateOnClientAsync` method shows more extreme results.</span></span> <span data-ttu-id="1327f-213">In questo caso, ogni test ha eseguito una query che ha recuperato più di 280Kb dei dati dal database, ma la risposta JSON è stata semplicemente di 14 byte.</span><span class="sxs-lookup"><span data-stu-id="1327f-213">In this case, each test performed a query that retrieved over 280Kb of data from the database, but the JSON response was a mere 14 bytes.</span></span> <span data-ttu-id="1327f-214">L'ampia disparità è dovuta al fatto che il metodo calcola un risultato aggregato da un grande volume di dati.</span><span class="sxs-lookup"><span data-stu-id="1327f-214">The wide disparity is because the method calculates an aggregated result from a large volume of data.</span></span>

![Dati di telemetria per il metodo 'AggregateOnClientAsync'][TelemetryAggregateOnClient]

### <a name="identify-and-analyze-slow-queries"></a><span data-ttu-id="1327f-216">Identificare e analizzare le query lente</span><span class="sxs-lookup"><span data-stu-id="1327f-216">Identify and analyze slow queries</span></span>

<span data-ttu-id="1327f-217">Cercare le query del database che usano la maggior parte delle risorse e richiedono più tempo per l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1327f-217">Look for database queries that consume the most resources and take the most time to execute.</span></span> <span data-ttu-id="1327f-218">È possibile aggiungere altri strumenti per trovare le ore di inizio e di completamento per molte operazioni del database.</span><span class="sxs-lookup"><span data-stu-id="1327f-218">You can add instrumentation to find the start and completion times for many database operations.</span></span> <span data-ttu-id="1327f-219">Inoltre, molti archivi dati forniscono informazioni dettagliate su come vengono eseguite e ottimizzate le query.</span><span class="sxs-lookup"><span data-stu-id="1327f-219">Many data stores also provide in-depth information on how queries are performed and optimized.</span></span> <span data-ttu-id="1327f-220">Ad esempio, il riquadro delle Prestazioni delle query nel portale di gestione di Database SQL di Azure consente di selezionare una query e di visualizzare le informazioni dettagliate sulle prestazioni di runtime.</span><span class="sxs-lookup"><span data-stu-id="1327f-220">For example, the Query Performance pane in the Azure SQL Database management portal lets you select a query and view detailed runtime performance information.</span></span> <span data-ttu-id="1327f-221">Ecco la query generata per l'operazione `GetAllFieldsAsync`:</span><span class="sxs-lookup"><span data-stu-id="1327f-221">Here is the query generated by the `GetAllFieldsAsync` operation:</span></span>

![Il riquadro dei dettagli della query nel portale di gestione del Database SQL di Azure][QueryDetails]


## <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="1327f-223">Implementare la soluzione e verificare il risultato</span><span class="sxs-lookup"><span data-stu-id="1327f-223">Implement the solution and verify the result</span></span>

<span data-ttu-id="1327f-224">Dopo aver modificato il metodo `GetRequiredFieldsAsync` per usare un'istruzione SELECT sul lato database, il test di carico ha mostrato i risultati seguenti.</span><span class="sxs-lookup"><span data-stu-id="1327f-224">After changing the `GetRequiredFieldsAsync` method to use a SELECT statement on the database side, load testing showed the following results.</span></span>

![Risultati del test di carico per il metodo GetRequiredFieldsAsync][Load-Test-Results-Database-Side1]

<span data-ttu-id="1327f-226">Questo test di carico ha usato la stessa distribuzione e lo stesso carico di lavoro simulato di 400 utenti concorrenti usato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="1327f-226">This load test used the same deployment and the same simulated workload of 400 concurrent users as before.</span></span> <span data-ttu-id="1327f-227">Il grafico mostra una latenza di molto inferiore.</span><span class="sxs-lookup"><span data-stu-id="1327f-227">The graph shows much lower latency.</span></span> <span data-ttu-id="1327f-228">Il tempo di risposta aumenta con il carico a circa 1,3 secondi, rispetto ai 4 secondi nel caso precedente.</span><span class="sxs-lookup"><span data-stu-id="1327f-228">Response time rises with load to approximately 1.3 seconds, compared to 4 seconds in the previous case.</span></span> <span data-ttu-id="1327f-229">Anche la velocità effettiva è superiore, con 350 richieste per secondo rispetto alle 100 precedenti.</span><span class="sxs-lookup"><span data-stu-id="1327f-229">The throughput is also higher at 350 requests per second compared to 100 earlier.</span></span> <span data-ttu-id="1327f-230">Il volume dei dati recuperati dal database ora corrispondono alle dimensioni dei messaggi di risposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="1327f-230">The volume of data retrieved from the database now closely matches the size of the HTTP response messages.</span></span>

![Dati di telemetria per il metodo 'GetRequiredFieldsAsync'][TelemetryRequiredFields]

<span data-ttu-id="1327f-232">L'esecuzione del test di carico tramite il metodo `AggregateOnDatabaseAsync` genera i risultati seguenti:</span><span class="sxs-lookup"><span data-stu-id="1327f-232">Load testing using the `AggregateOnDatabaseAsync` method generates the following results:</span></span>

![I risultati del test di carico per il metodo AggregateOnDatabaseAsync][Load-Test-Results-Database-Side2]

<span data-ttu-id="1327f-234">Il tempo di risposta medio è ora minimo.</span><span class="sxs-lookup"><span data-stu-id="1327f-234">The average response time is now minimal.</span></span> <span data-ttu-id="1327f-235">Si tratta di un ordine di miglioramento della grandezza delle prestazioni, dovuto principalmente alla riduzione delle grandi dimensioni in I/O dal database.</span><span class="sxs-lookup"><span data-stu-id="1327f-235">This is an order of magnitude improvement in performance, caused primarily by the large reduction in I/O from the database.</span></span> 

<span data-ttu-id="1327f-236">Ecco i dati di telemetria corrispondenti per il metodo `AggregateOnDatabaseAsync`.</span><span class="sxs-lookup"><span data-stu-id="1327f-236">Here is the corresponding telemetry for the `AggregateOnDatabaseAsync` method.</span></span> <span data-ttu-id="1327f-237">La quantità di dati recuperati dal database è stata notevolmente ridotta, da più di 280Kb per ogni transazione a 53 byte.</span><span class="sxs-lookup"><span data-stu-id="1327f-237">The amount of data retrieved from the database was vastly reduced, from over 280Kb per transaction to 53 bytes.</span></span> <span data-ttu-id="1327f-238">Di conseguenza, il numero massimo sostenuto di richieste al minuto è stato aumentato da circa 2.000 a più di 25.000.</span><span class="sxs-lookup"><span data-stu-id="1327f-238">As a result, the maximum sustained number of requests per minute was raised from around 2,000 to over 25,000.</span></span>

![Dati di telemetria per il metodo 'AggregateOnDatabaseAsync'][TelemetryAggregateInDatabaseAsync]


## <a name="related-resources"></a><span data-ttu-id="1327f-240">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="1327f-240">Related resources</span></span>

- <span data-ttu-id="1327f-241">[Antipattern del database occupato][BusyDatabase]</span><span class="sxs-lookup"><span data-stu-id="1327f-241">[Busy Database antipattern][BusyDatabase]</span></span>
- <span data-ttu-id="1327f-242">[Antipattern I/O "Frammentato"][chatty-io]</span><span class="sxs-lookup"><span data-stu-id="1327f-242">[Chatty I/O antipattern][chatty-io]</span></span>
- <span data-ttu-id="1327f-243">[Procedure consigliate per il partizionamento dei dati][data-partitioning]</span><span class="sxs-lookup"><span data-stu-id="1327f-243">[Data partitioning best practices][data-partitioning]</span></span>


[BusyDatabase]: ../busy-database/index.md
[data-partitioning]: ../../best-practices/data-partitioning.md
[new-relic]: https://newrelic.com/application-monitoring

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/ExtraneousFetching

[chatty-io]: ../chatty-io/index.md
[MonolithicPersistence]: ../monolithic-persistence/index.md
[Load-Test-Results-Client-Side1]:_images/LoadTestResultsClientSide1.jpg
[Load-Test-Results-Client-Side2]:_images/LoadTestResultsClientSide2.jpg
[Load-Test-Results-Database-Side1]:_images/LoadTestResultsDatabaseSide1.jpg
[Load-Test-Results-Database-Side2]:_images/LoadTestResultsDatabaseSide2.jpg
[QueryPerformanceZoomed]: _images/QueryPerformanceZoomed.jpg
[QueryDetails]: _images/QueryDetails.jpg
[TelemetryAllFields]: _images/TelemetryAllFields.jpg
[TelemetryAggregateOnClient]: _images/TelemetryAggregateOnClient.jpg
[TelemetryRequiredFields]: _images/TelemetryRequiredFields.jpg
[TelemetryAggregateInDatabaseAsync]: _images/TelemetryAggregateInDatabase.jpg
