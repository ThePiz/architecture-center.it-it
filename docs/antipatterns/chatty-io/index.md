---
title: Antipattern I/O "frammentato"
description: Un numero elevato di richieste di I/O può influire negativamente sulle prestazioni e la velocità di risposta.
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: 17193198918cc742b2e3f30e77dfc5c3f2726ebf
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47428568"
---
# <a name="chatty-io-antipattern"></a><span data-ttu-id="24190-103">Antipattern I/O "frammentato"</span><span class="sxs-lookup"><span data-stu-id="24190-103">Chatty I/O antipattern</span></span>

<span data-ttu-id="24190-104">L'effetto cumulativo di un numero elevato di richieste di I/O può avere un impatto significativo sulle prestazioni e la velocità di risposta.</span><span class="sxs-lookup"><span data-stu-id="24190-104">The cumulative effect of a large number of I/O requests can have a significant impact on performance and responsiveness.</span></span>

## <a name="problem-description"></a><span data-ttu-id="24190-105">Descrizione del problema</span><span class="sxs-lookup"><span data-stu-id="24190-105">Problem description</span></span>

<span data-ttu-id="24190-106">Chiamate di rete e altre operazioni di I/O sono intrinsecamente lente rispetto all'attività di calcolo.</span><span class="sxs-lookup"><span data-stu-id="24190-106">Network calls and other I/O operations are inherently slow compared to compute tasks.</span></span> <span data-ttu-id="24190-107">Ogni richiesta di I/O è in genere presenta un overhead significativo e l'effetto cumulativo di numerose operazioni di I/O può rallentare il sistema.</span><span class="sxs-lookup"><span data-stu-id="24190-107">Each I/O request typically has significant overhead, and the cumulative effect of numerous I/O operations can slow down the system.</span></span> <span data-ttu-id="24190-108">Di seguito sono riportate alcune cause comuni di I/O "frammentato".</span><span class="sxs-lookup"><span data-stu-id="24190-108">Here are some common causes of chatty I/O.</span></span>

### <a name="reading-and-writing-individual-records-to-a-database-as-distinct-requests"></a><span data-ttu-id="24190-109">Lettura e scrittura di singoli record in un database come richieste distinte</span><span class="sxs-lookup"><span data-stu-id="24190-109">Reading and writing individual records to a database as distinct requests</span></span>

<span data-ttu-id="24190-110">L'esempio seguente legge da un database di prodotti.</span><span class="sxs-lookup"><span data-stu-id="24190-110">The following example reads from a database of products.</span></span> <span data-ttu-id="24190-111">Sono presenti tre tabelle, `Product`, `ProductSubcategory` e `ProductPriceListHistory`.</span><span class="sxs-lookup"><span data-stu-id="24190-111">There are three tables, `Product`, `ProductSubcategory`, and `ProductPriceListHistory`.</span></span> <span data-ttu-id="24190-112">Il codice recupera tutti i prodotti in una sottocategoria, insieme alle informazioni sui prezzi, eseguendo una serie di query:</span><span class="sxs-lookup"><span data-stu-id="24190-112">The code retrieves all of the products in a subcategory, along with the pricing information, by executing a series of queries:</span></span>  

1. <span data-ttu-id="24190-113">Eseguire una query per la sottocategoria sulla tabella `ProductSubcategory`.</span><span class="sxs-lookup"><span data-stu-id="24190-113">Query the subcategory from the `ProductSubcategory` table.</span></span>
2. <span data-ttu-id="24190-114">Trovare tutti i prodotti in tale sottocategoria eseguendo una query sulla tabella `Product`.</span><span class="sxs-lookup"><span data-stu-id="24190-114">Find all products in that subcategory by querying the `Product` table.</span></span>
3. <span data-ttu-id="24190-115">Per ogni prodotto eseguire una query sui dati dei prezzi della tabella `ProductPriceListHistory`.</span><span class="sxs-lookup"><span data-stu-id="24190-115">For each product, query the pricing data from the `ProductPriceListHistory` table.</span></span>

<span data-ttu-id="24190-116">L'applicazione utilizza [Entity Framework][ef] per eseguire query sul database.</span><span class="sxs-lookup"><span data-stu-id="24190-116">The application uses [Entity Framework][ef] to query the database.</span></span> <span data-ttu-id="24190-117">L'esempio completo è disponibile [qui][code-sample].</span><span class="sxs-lookup"><span data-stu-id="24190-117">You can find the complete sample [here][code-sample].</span></span> 

```csharp
public async Task<IHttpActionResult> GetProductsInSubCategoryAsync(int subcategoryId)
{
    using (var context = GetContext())
    {
        // Get product subcategory.
        var productSubcategory = await context.ProductSubcategories
                .Where(psc => psc.ProductSubcategoryId == subcategoryId)
                .FirstOrDefaultAsync();

        // Find products in that category.
        productSubcategory.Product = await context.Products
            .Where(p => subcategoryId == p.ProductSubcategoryId)
            .ToListAsync();

        // Find price history for each product.
        foreach (var prod in productSubcategory.Product)
        {
            int productId = prod.ProductId;
            var productListPriceHistory = await context.ProductListPriceHistory
                .Where(pl => pl.ProductId == productId)
                .ToListAsync();
            prod.ProductListPriceHistory = productListPriceHistory;
        }
        return Ok(productSubcategory);
    }
}
```

<span data-ttu-id="24190-118">Questo esempio illustra il problema in modo esplicito, ma talvolta un O/RM può mascherare il problema, se recupera in modo implicito i record figlio uno alla volta.</span><span class="sxs-lookup"><span data-stu-id="24190-118">This example shows the problem explicitly, but sometimes an O/RM can mask the problem, if it implicitly fetches child records one at a time.</span></span> <span data-ttu-id="24190-119">Questo è noto come "problema N+1".</span><span class="sxs-lookup"><span data-stu-id="24190-119">This is known as the "N+1 problem".</span></span> 

### <a name="implementing-a-single-logical-operation-as-a-series-of-http-requests"></a><span data-ttu-id="24190-120">Implementazione di una singola operazione logica come una serie di richieste HTTP</span><span class="sxs-lookup"><span data-stu-id="24190-120">Implementing a single logical operation as a series of HTTP requests</span></span>

<span data-ttu-id="24190-121">Ciò accade spesso quando gli sviluppatori tentano di seguire un paradigma a oggetti e trattano gli oggetti remoti come se fossero oggetti locali in memoria.</span><span class="sxs-lookup"><span data-stu-id="24190-121">This often happens when developers try to follow an object-oriented paradigm, and treat remote objects as if they were local objects in memory.</span></span> <span data-ttu-id="24190-122">Questo può comportare troppi round trip di rete.</span><span class="sxs-lookup"><span data-stu-id="24190-122">This can result in too many network round trips.</span></span> <span data-ttu-id="24190-123">Ad esempio, l'API Web seguente espone le singole proprietà degli oggetti `User` tramite i singoli metodi HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="24190-123">For example, the following web API exposes the individual properties of `User` objects through individual HTTP GET methods.</span></span> 

```csharp
public class UserController : ApiController
{
    [HttpGet]
    [Route("users/{id:int}/username")]
    public HttpResponseMessage GetUserName(int id)
    {
        ...
    }

    [HttpGet]
    [Route("users/{id:int}/gender")]
    public HttpResponseMessage GetGender(int id)
    {
        ...
    }

    [HttpGet]
    [Route("users/{id:int}/dateofbirth")]
    public HttpResponseMessage GetDateOfBirth(int id)
    {
        ...
    }
}
```

<span data-ttu-id="24190-124">Anche se non c'è niente di tecnicamente errato in questo approccio, la maggior parte dei client probabilmente dovrà ottenere molte proprietà per ogni `User`, di conseguenza il codice client sarà simile al seguente.</span><span class="sxs-lookup"><span data-stu-id="24190-124">While there's nothing technically wrong with this approach, most clients will probably need to get several properties for each `User`, resulting in client code like the following.</span></span> 

```csharp
HttpResponseMessage response = await client.GetAsync("users/1/username");
response.EnsureSuccessStatusCode();
var userName = await response.Content.ReadAsStringAsync();

response = await client.GetAsync("users/1/gender");
response.EnsureSuccessStatusCode();
var gender = await response.Content.ReadAsStringAsync();

response = await client.GetAsync("users/1/dateofbirth");
response.EnsureSuccessStatusCode();
var dob = await response.Content.ReadAsStringAsync();
```

### <a name="reading-and-writing-to-a-file-on-disk"></a><span data-ttu-id="24190-125">Lettura e scrittura in un file su disco</span><span class="sxs-lookup"><span data-stu-id="24190-125">Reading and writing to a file on disk</span></span>

<span data-ttu-id="24190-126">L'I/O su file comporta l'apertura di un file e lo spostamento nel punto appropriato prima di leggere o scrivere i dati.</span><span class="sxs-lookup"><span data-stu-id="24190-126">File I/O involves opening a file and moving to the appropriate point before reading or writing data.</span></span> <span data-ttu-id="24190-127">Una volta completata l'operazione, il file può essere chiuso per risparmiare risorse del sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="24190-127">When the operation is complete, the file might be closed to save operating system resources.</span></span> <span data-ttu-id="24190-128">Un'applicazione che legge e scrive continuamente piccole quantità di informazioni in un file genererà un considerevole sovraccarico di I/O.</span><span class="sxs-lookup"><span data-stu-id="24190-128">An application that continually reads and writes small amounts of information to a file will generate significant I/O overhead.</span></span> <span data-ttu-id="24190-129">Anche le richieste di scrittura di piccole dimensioni possono provocare la frammentazione dei file, rallentando ulteriormente le successive operazioni di I/O.</span><span class="sxs-lookup"><span data-stu-id="24190-129">Small write requests can also lead to file fragmentation, slowing subsequent I/O operations still further.</span></span> 

<span data-ttu-id="24190-130">Il seguente esempio usa un `FileStream` per scrivere un oggetto `Customer` in un file.</span><span class="sxs-lookup"><span data-stu-id="24190-130">The following example uses a `FileStream` to write a `Customer` object to a file.</span></span> <span data-ttu-id="24190-131">Creando il `FileStream`, il file viene aperto, ed eliminandolo, il file viene chiuso</span><span class="sxs-lookup"><span data-stu-id="24190-131">Creating the `FileStream` opens the file, and disposing it closes the file.</span></span> <span data-ttu-id="24190-132">(l'istruzione `using` elimina automaticamente l'oggetto `FileStream`). Se l'applicazione chiama questo metodo ripetutamente man mano che vengono aggiunti nuovi clienti, può accumularsi rapidamente molto sovraccarico di I/O.</span><span class="sxs-lookup"><span data-stu-id="24190-132">(The `using` statement automatically disposes the `FileStream` object.) If the application calls this method repeatedly as new customers are added, the I/O overhead can accumulate quickly.</span></span>

```csharp
private async Task SaveCustomerToFileAsync(Customer cust)
{
    using (Stream fileStream = new FileStream(CustomersFileName, FileMode.Append))
    {
        BinaryFormatter formatter = new BinaryFormatter();
        byte [] data = null;
        using (MemoryStream memStream = new MemoryStream())
        {
            formatter.Serialize(memStream, cust);
            data = memStream.ToArray();
        }
        await fileStream.WriteAsync(data, 0, data.Length);
    }
}
```

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="24190-133">Come risolvere il problema</span><span class="sxs-lookup"><span data-stu-id="24190-133">How to fix the problem</span></span>

<span data-ttu-id="24190-134">Ridurre il numero di richieste di I/O assemblando i dati in un numero inferiore di richieste di dimensioni maggiore.</span><span class="sxs-lookup"><span data-stu-id="24190-134">Reduce the number of I/O requests by packaging the data into larger, fewer requests.</span></span>

<span data-ttu-id="24190-135">Recuperare i dati da un database in una singola query, anziché in più query di dimensioni inferiori.</span><span class="sxs-lookup"><span data-stu-id="24190-135">Fetch data from a database as a single query, instead of several smaller queries.</span></span> <span data-ttu-id="24190-136">Di seguito è riportata una versione modificata del codice che consente di recuperare informazioni sui prodotti.</span><span class="sxs-lookup"><span data-stu-id="24190-136">Here's a revised version of the code that retrieves product information.</span></span>

```csharp
public async Task<IHttpActionResult> GetProductCategoryDetailsAsync(int subCategoryId)
{
    using (var context = GetContext())
    {
        var subCategory = await context.ProductSubcategories
                .Where(psc => psc.ProductSubcategoryId == subCategoryId)
                .Include("Product.ProductListPriceHistory")
                .FirstOrDefaultAsync();

        if (subCategory == null)
            return NotFound();

        return Ok(subCategory);
    }
}
```

<span data-ttu-id="24190-137">Seguire i principi di progettazione REST per le API Web.</span><span class="sxs-lookup"><span data-stu-id="24190-137">Follow REST design principles for web APIs.</span></span> <span data-ttu-id="24190-138">Di seguito è riportata una versione modificata dell'API Web dell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="24190-138">Here's a revised version of the web API from the earlier example.</span></span> <span data-ttu-id="24190-139">Anziché metodi GET separati per ogni proprietà, c'è un singolo metodo GET che restituisce il `User`.</span><span class="sxs-lookup"><span data-stu-id="24190-139">Instead of separate GET methods for each property, there is a single GET method that returns the `User`.</span></span> <span data-ttu-id="24190-140">Di conseguenza, il corpo di risposta per ogni richiesta ha dimensioni maggiori, ma è probabile che ogni client esegua un numero inferiore di chiamate API.</span><span class="sxs-lookup"><span data-stu-id="24190-140">This results in a larger response body per request, but each client is likely to make fewer API calls.</span></span>

```csharp
public class UserController : ApiController
{
    [HttpGet]
    [Route("users/{id:int}")]
    public HttpResponseMessage GetUser(int id)
    {
        ...
    }
}

// Client code
HttpResponseMessage response = await client.GetAsync("users/1");
response.EnsureSuccessStatusCode();
var user = await response.Content.ReadAsStringAsync();
```

<span data-ttu-id="24190-141">Per l'I/O su file, considerare di memorizzare nel buffer i dati in memoria e quindi scrivere i dati memorizzati nel buffer in un file in una singola operazione.</span><span class="sxs-lookup"><span data-stu-id="24190-141">For file I/O, consider buffering data in memory and then writing the buffered data to a file as a single operation.</span></span> <span data-ttu-id="24190-142">Questo approccio riduce il sovraccarico dato dall'aprire e chiudere il file ripetutamente e consente di ridurre la frammentazione dei file su disco.</span><span class="sxs-lookup"><span data-stu-id="24190-142">This approach reduces the overhead from repeatedly opening and closing the file, and helps to reduce fragmentation of the file on disk.</span></span>

```csharp
// Save a list of customer objects to a file
private async Task SaveCustomerListToFileAsync(List<Customer> customers)
{
    using (Stream fileStream = new FileStream(CustomersFileName, FileMode.Append))
    {
        BinaryFormatter formatter = new BinaryFormatter();
        foreach (var cust in customers)
        {
            byte[] data = null;
            using (MemoryStream memStream = new MemoryStream())
            {
                formatter.Serialize(memStream, cust);
                data = memStream.ToArray();
            }
            await fileStream.WriteAsync(data, 0, data.Length);
        }
    }
}

// In-memory buffer for customers.
List<Customer> customers = new List<Customers>();

// Create a new customer and add it to the buffer
var cust = new Customer(...);
customers.Add(cust);

// Add more customers to the list as they are created
...

// Save the contents of the list, writing all customers in a single operation
await SaveCustomerListToFileAsync(customers);
```

## <a name="considerations"></a><span data-ttu-id="24190-143">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="24190-143">Considerations</span></span>

- <span data-ttu-id="24190-144">I primi due esempi generano *meno* chiamate di I/O, ma ognuno recupera *più* informazioni.</span><span class="sxs-lookup"><span data-stu-id="24190-144">The first two examples make *fewer* I/O calls, but each one retrieves *more* information.</span></span> <span data-ttu-id="24190-145">È necessario considerare il compromesso tra questi due fattori.</span><span class="sxs-lookup"><span data-stu-id="24190-145">You must consider the tradeoff between these two factors.</span></span> <span data-ttu-id="24190-146">La risposta esatta varia in base agli schemi di utilizzo effettivi.</span><span class="sxs-lookup"><span data-stu-id="24190-146">The right answer will depend on the actual usage patterns.</span></span> <span data-ttu-id="24190-147">Ad esempio, nell'esempio dell'API Web, potrebbe risultare che i client richiedono spesso solo il nome utente.</span><span class="sxs-lookup"><span data-stu-id="24190-147">For example, in the web API example, it might turn out that clients often need just the user name.</span></span> <span data-ttu-id="24190-148">In tal caso, può rivelarsi utile esporlo come una chiamata API distinta.</span><span class="sxs-lookup"><span data-stu-id="24190-148">In that case, it might make sense to expose it as a separate API call.</span></span> <span data-ttu-id="24190-149">Per altre informazioni, vedere l'antipattern di [recupero estraneo][extraneous-fetching].</span><span class="sxs-lookup"><span data-stu-id="24190-149">For more information, see the [Extraneous Fetching][extraneous-fetching] antipattern.</span></span>

- <span data-ttu-id="24190-150">Durante la lettura dei dati, non eseguire richieste di I/O troppo grandi.</span><span class="sxs-lookup"><span data-stu-id="24190-150">When reading data, do not make your I/O requests too large.</span></span> <span data-ttu-id="24190-151">Un'applicazione deve recuperare solo le informazioni che è probabile che vengano utilizzate.</span><span class="sxs-lookup"><span data-stu-id="24190-151">An application should only retrieve the information that it is likely to use.</span></span> 

- <span data-ttu-id="24190-152">Talvolta è utile partizionare le informazioni per un oggetto in due blocchi, *dati con accesso frequente*, che sono responsabili della maggior parte delle richieste, e *dati ad accesso meno frequente*, utilizzati raramente.</span><span class="sxs-lookup"><span data-stu-id="24190-152">Sometimes it helps to partition the information for an object into two chunks, *frequently accessed data* that accounts for most requests, and *less frequently accessed data* that is used rarely.</span></span> <span data-ttu-id="24190-153">Spesso i dati utilizzati più di frequente sono una parte relativamente piccola dei dati totali per un oggetto, pertanto restituire solo questa parte permette di risparmiare un considerevole sovraccarico di I/O.</span><span class="sxs-lookup"><span data-stu-id="24190-153">Often the most frequently accessed data is a relatively small portion of the total data for an object, so returning just that portion can save significant I/O overhead.</span></span>

- <span data-ttu-id="24190-154">Durante la scrittura dei dati, evitare di bloccare risorse più a lungo del necessario, per ridurre le probabilità di contesa durante un'operazione di lunga durata.</span><span class="sxs-lookup"><span data-stu-id="24190-154">When writing data, avoid locking resources for longer than necessary, to reduce the chances of contention during a lengthy operation.</span></span> <span data-ttu-id="24190-155">Se un'operazione di scrittura si estende su più archivi dati, file o i servizi, adottare un approccio finale coerente.</span><span class="sxs-lookup"><span data-stu-id="24190-155">If a write operation spans multiple data stores, files, or services, then adopt an eventually consistent approach.</span></span> <span data-ttu-id="24190-156">Vedere il [materiale sussidiario sulla coerenza dei dati][data-consistency-guidance].</span><span class="sxs-lookup"><span data-stu-id="24190-156">See [Data Consistency guidance][data-consistency-guidance].</span></span>

- <span data-ttu-id="24190-157">Se si memorizzano i dati in memoria prima della loro scrittura, i dati sono vulnerabili in caso di arresto anomalo del processo.</span><span class="sxs-lookup"><span data-stu-id="24190-157">If you buffer data in memory before writing it, the data is vulnerable if the process crashes.</span></span> <span data-ttu-id="24190-158">Se la velocità dei dati è soggetta a picchi o è relativamente sparsa, potrebbe essere preferibile memorizzare nel buffer i dati utilizzando una coda durevole esterna, ad esempio [Hub eventi](https://azure.microsoft.com/services/event-hubs/).</span><span class="sxs-lookup"><span data-stu-id="24190-158">If the data rate typically has bursts or is relatively sparse, it may be safer to buffer the data in an external durable queue such as [Event Hubs](https://azure.microsoft.com/services/event-hubs/).</span></span>

- <span data-ttu-id="24190-159">Si più prendere in considerazione di memorizzare nella cache i dati recuperati da un servizio o da un database.</span><span class="sxs-lookup"><span data-stu-id="24190-159">Consider caching data that you retrieve from a service or a database.</span></span> <span data-ttu-id="24190-160">Ciò consente di ridurre il volume di I/O, evitando richieste ripetute per gli stessi dati.</span><span class="sxs-lookup"><span data-stu-id="24190-160">This can help to reduce the volume of I/O by avoiding repeated requests for the same data.</span></span> <span data-ttu-id="24190-161">Per altre informazioni, vedere [Caching best practices][caching-guidance] (Procedure consigliate per la memorizzazione nella cache).</span><span class="sxs-lookup"><span data-stu-id="24190-161">For more information, see [Caching best practices][caching-guidance].</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="24190-162">Come rilevare il problema</span><span class="sxs-lookup"><span data-stu-id="24190-162">How to detect the problem</span></span>

<span data-ttu-id="24190-163">I sintomi dell'I/O "frammentato" includono una latenza elevata e una bassa velocità effettiva.</span><span class="sxs-lookup"><span data-stu-id="24190-163">Symptoms of chatty I/O include high latency and low throughput.</span></span> <span data-ttu-id="24190-164">Gli utenti finali riferiscono in genere tempi di risposta prolungati o errori causati dal timeout dei servizi a causa della maggiore contesa per le risorse di I/O.</span><span class="sxs-lookup"><span data-stu-id="24190-164">End users are likely to report extended response times or failures caused by services timing out, due to increased contention for I/O resources.</span></span>

<span data-ttu-id="24190-165">È possibile eseguire la procedura seguente per identificare la causa di qualsiasi problema:</span><span class="sxs-lookup"><span data-stu-id="24190-165">You can perform the following steps to help identify the causes of any problems:</span></span>

1. <span data-ttu-id="24190-166">Eseguire il monitoraggio del processo per il sistema di produzione per identificare le operazioni con tempi di risposta più lunghi.</span><span class="sxs-lookup"><span data-stu-id="24190-166">Perform process monitoring of the production system to identify operations with poor response times.</span></span>
2. <span data-ttu-id="24190-167">Eseguire test di carico di ogni operazione identificata nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="24190-167">Perform load testing of each operation identified in the previous step.</span></span>
3. <span data-ttu-id="24190-168">Durante i test di carico, raccogliere dati di telemetria sulle richieste di accesso ai dati eseguite da ogni operazione.</span><span class="sxs-lookup"><span data-stu-id="24190-168">During the load tests, gather telemetry data about the data access requests made by each operation.</span></span>
4. <span data-ttu-id="24190-169">Raccogliere statistiche dettagliate per ogni richiesta inviata a un archivio dati.</span><span class="sxs-lookup"><span data-stu-id="24190-169">Gather detailed statistics for each request sent to a data store.</span></span>
5. <span data-ttu-id="24190-170">Profilare l'applicazione nell'ambiente di test per stabilire dove potrebbero verificarsi possibili colli di bottiglia di I/O.</span><span class="sxs-lookup"><span data-stu-id="24190-170">Profile the application in the test environment to establish where possible I/O bottlenecks might be occurring.</span></span> 

<span data-ttu-id="24190-171">Cercare uno qualsiasi dei seguenti sintomi:</span><span class="sxs-lookup"><span data-stu-id="24190-171">Look for any of these symptoms:</span></span>

- <span data-ttu-id="24190-172">Un numero elevato di richieste di I/O di piccole dimensioni eseguite verso lo stesso file.</span><span class="sxs-lookup"><span data-stu-id="24190-172">A large number of small I/O requests made to the same file.</span></span>
- <span data-ttu-id="24190-173">Un numero elevato di richieste di rete di piccole dimensioni eseguite da un'istanza di applicazione verso lo stesso servizio.</span><span class="sxs-lookup"><span data-stu-id="24190-173">A large number of small network requests made by an application instance to the same service.</span></span>
- <span data-ttu-id="24190-174">Un numero elevato di richieste di piccole dimensioni eseguite da un'istanza di applicazione verso lo stesso archivio dati.</span><span class="sxs-lookup"><span data-stu-id="24190-174">A large number of small requests made by an application instance to the same data store.</span></span>
- <span data-ttu-id="24190-175">Applicazioni e servizi che diventano associati all'I/O.</span><span class="sxs-lookup"><span data-stu-id="24190-175">Applications and services becoming I/O bound.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="24190-176">Diagnosi di esempio</span><span class="sxs-lookup"><span data-stu-id="24190-176">Example diagnosis</span></span>

<span data-ttu-id="24190-177">Nelle sezioni seguenti si applicano questi passaggi all'esempio illustrato in precedenza che esegue query in un database.</span><span class="sxs-lookup"><span data-stu-id="24190-177">The following sections apply these steps to the example shown earlier that queries a database.</span></span>

### <a name="load-test-the-application"></a><span data-ttu-id="24190-178">Testare il carico dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="24190-178">Load test the application</span></span>

<span data-ttu-id="24190-179">Questo grafico mostra i risultati del test di carico.</span><span class="sxs-lookup"><span data-stu-id="24190-179">This graph shows the results of load testing.</span></span> <span data-ttu-id="24190-180">Il tempo di risposta mediano viene misurato in decimi di secondo per richiesta.</span><span class="sxs-lookup"><span data-stu-id="24190-180">Median response time is measured in 10s of seconds per request.</span></span> <span data-ttu-id="24190-181">Il grafico mostra una latenza molto elevata.</span><span class="sxs-lookup"><span data-stu-id="24190-181">The graph shows very high latency.</span></span> <span data-ttu-id="24190-182">Con un carico di 1000 utenti, un utente potrebbe dover attendere circa un minuto per visualizzare i risultati di una query.</span><span class="sxs-lookup"><span data-stu-id="24190-182">With a load of 1000 users, a user might have to wait for nearly a minute to see the results of a query.</span></span> 

![Indicatori chiave risultanti dai test di carico gli per l'applicazione di esempio sull'I/O "frammentato"][key-indicators-chatty-io]

> [!NOTE]
> <span data-ttu-id="24190-184">L'applicazione è stata distribuita come un'app Web del Servizio App di Azure, utilizzando il database SQL di Azure.</span><span class="sxs-lookup"><span data-stu-id="24190-184">The application was deployed as an Azure App Service web app, using Azure SQL Database.</span></span> <span data-ttu-id="24190-185">Il test di carico utilizzava un carico di lavoro simulato a passaggi fino a un massimo di 1000 utenti simultanei.</span><span class="sxs-lookup"><span data-stu-id="24190-185">The load test used a simulated step workload of up to 1000 concurrent users.</span></span> <span data-ttu-id="24190-186">Il database è stato configurato con un pool di connessioni che supporta un massimo di 1000 connessioni simultanee, per ridurre le probabilità che una contesa per le connessioni possa influire sui risultati.</span><span class="sxs-lookup"><span data-stu-id="24190-186">The database was configured with a connection pool supporting up to 1000 concurrent connections, to reduce the chance that contention for connections would affect the results.</span></span> 

### <a name="monitor-the-application"></a><span data-ttu-id="24190-187">Monitorare l'applicazione</span><span class="sxs-lookup"><span data-stu-id="24190-187">Monitor the application</span></span>

<span data-ttu-id="24190-188">È possibile usare una pacchetto di monitoraggio delle prestazioni di applicazione (APM) per acquisire e analizzare le metriche chiave che potrebbero identificare I/O "frammentato".</span><span class="sxs-lookup"><span data-stu-id="24190-188">You can use an application performance monitoring (APM) package to capture and analyze the key metrics that might identify chatty I/O.</span></span> <span data-ttu-id="24190-189">Quali metriche sono importanti dipende dal carico di lavoro di I/O.</span><span class="sxs-lookup"><span data-stu-id="24190-189">Which metrics are important will depend on the I/O workload.</span></span> <span data-ttu-id="24190-190">Per questo esempio, le richieste di I/O interessanti erano le query di database.</span><span class="sxs-lookup"><span data-stu-id="24190-190">For this example, the interesting I/O requests were the database queries.</span></span> 

<span data-ttu-id="24190-191">La figura seguente mostra i risultati generati utilizzando l'[APM New Relic][new-relic].</span><span class="sxs-lookup"><span data-stu-id="24190-191">The following image shows results generated using [New Relic APM][new-relic].</span></span> <span data-ttu-id="24190-192">Il tempo di risposta medio del database ha avuto un picco a circa 5,6 secondi per ogni richiesta durante il carico di lavoro massimo.</span><span class="sxs-lookup"><span data-stu-id="24190-192">The average database response time peaked at approximately 5.6 seconds per request during the maximum workload.</span></span> <span data-ttu-id="24190-193">Il sistema è in grado di supportare una media di 410 richieste al minuto per tutto il test.</span><span class="sxs-lookup"><span data-stu-id="24190-193">The system was able to support an average of 410 requests per minute throughout the test.</span></span>

![Panoramica del traffico che raggiunge il database AdventureWorks2012][databasetraffic]

### <a name="gather-detailed-data-access-information"></a><span data-ttu-id="24190-195">Raccogliere informazioni di accesso ai dati dettagliate</span><span class="sxs-lookup"><span data-stu-id="24190-195">Gather detailed data access information</span></span>

<span data-ttu-id="24190-196">Alcuni approfondimenti nei dati di monitoraggio illustrano che l'applicazione esegue tre diverse istruzioni SQL SELECT.</span><span class="sxs-lookup"><span data-stu-id="24190-196">Digging deeper into the monitoring data shows the application executes three different SQL SELECT statements.</span></span> <span data-ttu-id="24190-197">Queste corrispondono alle richieste generate da Entity Framework per recuperare i dati dalle tabelle `ProductListPriceHistory`, `Product` e `ProductSubcategory`.</span><span class="sxs-lookup"><span data-stu-id="24190-197">These correspond to the requests generated by Entity Framework to fetch data from the `ProductListPriceHistory`, `Product`, and `ProductSubcategory` tables.</span></span>
<span data-ttu-id="24190-198">Inoltre, la query che recupera i dati dalla tabella `ProductListPriceHistory` è di gran lunga l'istruzione SELECT eseguita più di frequente, di un ordine di grandezza.</span><span class="sxs-lookup"><span data-stu-id="24190-198">Furthermore, the query that retrieves data from the `ProductListPriceHistory` table is by far the most frequently executed SELECT statement, by an order of magnitude.</span></span>

![Query eseguite dall'applicazione di esempio sottoposta a test][queries]

<span data-ttu-id="24190-200">Si scopre che il metodo `GetProductsInSubCategoryAsync`, illustrato in precedenza, esegue 45 query SELECT.</span><span class="sxs-lookup"><span data-stu-id="24190-200">It turns out that the `GetProductsInSubCategoryAsync` method, shown earlier, performs 45 SELECT queries.</span></span> <span data-ttu-id="24190-201">Ogni query induce l'applicazione ad aprire una nuova connessione SQL.</span><span class="sxs-lookup"><span data-stu-id="24190-201">Each query causes the application to open a new SQL connection.</span></span>

![Statistiche delle query per l'applicazione di esempio sottoposta a test][queries2]

> [!NOTE]
> <span data-ttu-id="24190-203">Questa immagine mostra le informazioni di traccia per l'istanza più lenta dell'operazione `GetProductsInSubCategoryAsync` nel test di carico.</span><span class="sxs-lookup"><span data-stu-id="24190-203">This image shows trace information for the slowest instance of the `GetProductsInSubCategoryAsync` operation in the load test.</span></span> <span data-ttu-id="24190-204">In un ambiente di produzione è utile esaminare le tracce delle istanze più lente, per verificare se esiste un modello che indica un problema.</span><span class="sxs-lookup"><span data-stu-id="24190-204">In a production environment, it's useful to examine traces of the slowest instances, to see if there is a pattern that suggests a problem.</span></span> <span data-ttu-id="24190-205">Se si osservano solo i valori medi, si potrebbero tralasciare i problemi che peggioreranno notevolmente in condizioni di carico.</span><span class="sxs-lookup"><span data-stu-id="24190-205">If you just look at the average values, you might overlook problems that will get dramatically worse under load.</span></span>

<span data-ttu-id="24190-206">La figura seguente mostra le istruzioni SQL effettive che sono state eseguite.</span><span class="sxs-lookup"><span data-stu-id="24190-206">The next image shows the actual SQL statements that were issued.</span></span> <span data-ttu-id="24190-207">La query che recupera le informazioni sui prezzi Viene eseguita per ogni singolo prodotto nella sottocategoria del prodotto.</span><span class="sxs-lookup"><span data-stu-id="24190-207">The query that fetches price information is run for each individual product in the product subcategory.</span></span> <span data-ttu-id="24190-208">Utilizzando un join si potrebbe ridurre notevolmente il numero di chiamate al database.</span><span class="sxs-lookup"><span data-stu-id="24190-208">Using a join would considerably reduce the number of database calls.</span></span>

![Dettagli delle query per l'applicazione di esempio sottoposta a test][queries3]

<span data-ttu-id="24190-210">Se si utilizza un O/RM, ad esempio Entity Framework, il monitoraggio delle query SQL può consentire di comprendere la modalità in cui O/RM converte le chiamate a livello di codice in istruzioni SQL e indicare le aree in cui l'accesso ai dati può essere ottimizzato.</span><span class="sxs-lookup"><span data-stu-id="24190-210">If you are using an O/RM, such as Entity Framework, tracing the SQL queries can provide insight into how the O/RM translates programmatic calls into SQL statements, and indicate areas where data access might be optimized.</span></span> 

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="24190-211">Implementare la soluzione e verificare il risultato</span><span class="sxs-lookup"><span data-stu-id="24190-211">Implement the solution and verify the result</span></span>

<span data-ttu-id="24190-212">Riscrivendo la chiamata a Entity Framework sono stati ottenuti i risultati seguenti.</span><span class="sxs-lookup"><span data-stu-id="24190-212">Rewriting the call to Entity Framework produced the following results.</span></span>

![Indicatori chiave risultanti dai test di carico gli per l'API chunky nell'applicazione di esempio sull'I/O "frammentato"][key-indicators-chunky-io]

<span data-ttu-id="24190-214">Questo test di carico è stato eseguito nella stessa distribuzione, utilizzando lo stesso profilo di carico.</span><span class="sxs-lookup"><span data-stu-id="24190-214">This load test was performed on the same deployment, using the same load profile.</span></span> <span data-ttu-id="24190-215">Questa volta grafico mostra una latenza molto inferiore.</span><span class="sxs-lookup"><span data-stu-id="24190-215">This time the graph shows much lower latency.</span></span> <span data-ttu-id="24190-216">Il tempo medio di richiesta con 1000 utenti è compreso tra 5 e 6 secondi, più basso di circa un minuto.</span><span class="sxs-lookup"><span data-stu-id="24190-216">The average request time at 1000 users is between 5 and 6 seconds, down from nearly a minute.</span></span>

<span data-ttu-id="24190-217">Questa volta il sistema ha supportato una media di 3.970 richieste al minuto, rispetto alle 410 per il test precedente.</span><span class="sxs-lookup"><span data-stu-id="24190-217">This time the system supported an average of 3,970 requests per minute, compared to 410 for the earlier test.</span></span>

![Panoramica delle transazioni per l'API a blocchi][databasetraffic2]

<span data-ttu-id="24190-219">Monitorando l'istruzione SQL si vede che tutti i dati vengono recuperati in una singola istruzione SELECT.</span><span class="sxs-lookup"><span data-stu-id="24190-219">Tracing the SQL statement shows that all the data is fetched in a single SELECT statement.</span></span> <span data-ttu-id="24190-220">Anche se questa query è notevolmente più complessa, viene eseguita una sola volta per ogni operazione.</span><span class="sxs-lookup"><span data-stu-id="24190-220">Although this query is considerably more complex, it is performed only once per operation.</span></span> <span data-ttu-id="24190-221">E mentre i join complessi possono diventare costosi, i sistemi di database relazionali sono ottimizzati per questo tipo di query.</span><span class="sxs-lookup"><span data-stu-id="24190-221">And while complex joins can become expensive, relational database systems are optimized for this type of query.</span></span>  

![Dettagli delle query per l'API a blocchi][queries4]

## <a name="related-resources"></a><span data-ttu-id="24190-223">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="24190-223">Related resources</span></span>

- <span data-ttu-id="24190-224">[API design best practices][api-design] (Procedure consigliate per la progettazione di API)</span><span class="sxs-lookup"><span data-stu-id="24190-224">[API Design best practices][api-design]</span></span>
- <span data-ttu-id="24190-225">[Caching best practices][caching-guidance] (Procedure consigliate per la memorizzazione nella cache)</span><span class="sxs-lookup"><span data-stu-id="24190-225">[Caching best practices][caching-guidance]</span></span>
- <span data-ttu-id="24190-226">[Nozioni di base sulla coerenza dei dati][data-consistency-guidance]</span><span class="sxs-lookup"><span data-stu-id="24190-226">[Data Consistency Primer][data-consistency-guidance]</span></span>
- <span data-ttu-id="24190-227">[Antipattern di recupero estraneo][extraneous-fetching]</span><span class="sxs-lookup"><span data-stu-id="24190-227">[Extraneous Fetching antipattern][extraneous-fetching]</span></span>
- <span data-ttu-id="24190-228">[Nessun antipattern della memorizzazione nella cache][no-cache]</span><span class="sxs-lookup"><span data-stu-id="24190-228">[No Caching antipattern][no-cache]</span></span>

[api-design]: ../../best-practices/api-design.md
[caching-guidance]: ../../best-practices/caching.md
[code-sample]:  https://github.com/mspnp/performance-optimization/tree/master/ChattyIO
[data-consistency-guidance]: https://msdn.microsoft.com/library/dn589800.aspx
[ef]: /ef/
[extraneous-fetching]: ../extraneous-fetching/index.md
[new-relic]: https://newrelic.com/application-monitoring
[no-cache]: ../no-caching/index.md

[key-indicators-chatty-io]: _images/ChattyIO.jpg
[key-indicators-chunky-io]: _images/ChunkyIO.jpg
[databasetraffic]: _images/DatabaseTraffic.jpg
[databasetraffic2]: _images/DatabaseTraffic2.jpg
[queries]: _images/DatabaseQueries.jpg
[queries2]: _images/DatabaseQueries2.jpg
[queries3]: _images/DatabaseQueries3.jpg
[queries4]: _images/DatabaseQueries4.jpg

