---
title: Cache-aside
description: Caricare i dati su richiesta in una cache da un archivio dati
keywords: schema progettuale
author: dragon119
ms.date: 11/01/2018
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- data-management
- performance-scalability
ms.openlocfilehash: 4c93ed02ff28e79cedc26f83364592baba96821d
ms.sourcegitcommit: dbbf914757b03cdee7a274204f9579fa63d7eed2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50916374"
---
# <a name="cache-aside-pattern"></a><span data-ttu-id="73b91-104">Modello cache-aside</span><span class="sxs-lookup"><span data-stu-id="73b91-104">Cache-Aside pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="73b91-105">Con questo modello i dati vengono caricati su richiesta in una cache da un archivio dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-105">Load data on demand into a cache from a data store.</span></span> <span data-ttu-id="73b91-106">In questo modo è possibile migliorare le prestazioni, nonché garantire la coerenza tra i dati memorizzati nella cache e quelli presenti nell'archivio dati sottostante.</span><span class="sxs-lookup"><span data-stu-id="73b91-106">This can improve performance and also helps to maintain consistency between data held in the cache and data in the underlying data store.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="73b91-107">Contesto e problema</span><span class="sxs-lookup"><span data-stu-id="73b91-107">Context and problem</span></span>

<span data-ttu-id="73b91-108">Le applicazioni usano una cache per migliorare l'accesso ripetuto alle informazioni presenti in un archivio dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-108">Applications use a cache to improve repeated access to information held in a data store.</span></span> <span data-ttu-id="73b91-109">Non è però pensabile che i dati memorizzati nella cache siano sempre completamente coerenti con i dati presenti nell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-109">However, it's impractical to expect that cached data will always be completely consistent with the data in the data store.</span></span> <span data-ttu-id="73b91-110">Le applicazioni devono implementare una strategia in grado di garantire che i dati nella cache siano il più possibile aggiornati, ma anche di rilevare e gestire le situazioni che si verificano quando i dati nella cache diventano obsoleti.</span><span class="sxs-lookup"><span data-stu-id="73b91-110">Applications should implement a strategy that helps to ensure that the data in the cache is as up-to-date as possible, but can also detect and handle situations that arise when the data in the cache has become stale.</span></span>

## <a name="solution"></a><span data-ttu-id="73b91-111">Soluzione</span><span class="sxs-lookup"><span data-stu-id="73b91-111">Solution</span></span>

<span data-ttu-id="73b91-112">Molti sistemi di memorizzazione nella cache disponibili in commercio offrono operazioni di read-through e write-through/write-behind.</span><span class="sxs-lookup"><span data-stu-id="73b91-112">Many commercial caching systems provide read-through and write-through/write-behind operations.</span></span> <span data-ttu-id="73b91-113">In questi sistemi un'applicazione fa riferimento alla cache per recuperare i dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-113">In these systems, an application retrieves data by referencing the cache.</span></span> <span data-ttu-id="73b91-114">Se non sono presenti nella cache, i dati vengono recuperati dall'archivio dati e aggiunti alla cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-114">If the data isn't in the cache, it's retrieved from the data store and added to the cache.</span></span> <span data-ttu-id="73b91-115">Tutte le modifiche apportate ai dati memorizzati nella cache vengono scritte automaticamente anche nell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-115">Any modifications to data held in the cache are automatically written back to the data store as well.</span></span>

<span data-ttu-id="73b91-116">Per le cache che non offrono questa funzionalità, la conservazione dei dati viene gestita dalle applicazioni che usano la cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-116">For caches that don't provide this functionality, it's the responsibility of the applications that use the cache to maintain the data.</span></span>

<span data-ttu-id="73b91-117">Un'applicazione può emulare la funzionalità di memorizzazione nella cache di read-through implementando la strategia di cache-aside.</span><span class="sxs-lookup"><span data-stu-id="73b91-117">An application can emulate the functionality of read-through caching by implementing the cache-aside strategy.</span></span> <span data-ttu-id="73b91-118">Con questa strategia i dati vengono caricati nella cache su richiesta.</span><span class="sxs-lookup"><span data-stu-id="73b91-118">This strategy loads data into the cache on demand.</span></span> <span data-ttu-id="73b91-119">La figura illustra l'uso del modello cache-aside per l'archiviazione dei dati nella cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-119">The figure illustrates using the Cache-Aside pattern to store data in the cache.</span></span>

![Uso del modello cache-aside per l'archiviazione dei dati nella cache](./_images/cache-aside-diagram.png)


<span data-ttu-id="73b91-121">Un'applicazione che aggiorna le informazioni può seguire la strategia di write-through in modo da apportare la modifica all'archivio dati e invalidare l'elemento corrispondente nella cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-121">If an application updates information, it can follow the write-through strategy by making the modification to the data store, and by invalidating the corresponding item in the cache.</span></span>

<span data-ttu-id="73b91-122">Quando l'elemento viene richiesto successivamente, con la strategia di cache-aside i dati aggiornati verranno recuperati dall'archivio dei dati e aggiunti nuovamente nella cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-122">When the item is next required, using the cache-aside strategy will cause the updated data to be retrieved from the data store and added back into the cache.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="73b91-123">Considerazioni e problemi</span><span class="sxs-lookup"><span data-stu-id="73b91-123">Issues and considerations</span></span>

<span data-ttu-id="73b91-124">Prima di decidere come implementare questo modello, considerare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="73b91-124">Consider the following points when deciding how to implement this pattern:</span></span> 

<span data-ttu-id="73b91-125">**Durata dei dati memorizzati nella cache**.</span><span class="sxs-lookup"><span data-stu-id="73b91-125">**Lifetime of cached data**.</span></span> <span data-ttu-id="73b91-126">Molte cache implementano un criterio di scadenza che invalida i dati e li rimuove dalla cache se non vengono usati per un periodo specificato.</span><span class="sxs-lookup"><span data-stu-id="73b91-126">Many caches implement an expiration policy that invalidates data and removes it from the cache if it's not accessed for a specified period.</span></span> <span data-ttu-id="73b91-127">Per rendere effettiva la strategia di cache-aside, assicurarsi che i criteri di scadenza corrispondano al criterio di accesso per le applicazioni che usano i dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-127">For cache-aside to be effective, ensure that the expiration policy matches the pattern of access for applications that use the data.</span></span> <span data-ttu-id="73b91-128">Non impostare un periodo di scadenza troppo breve per evitare che le applicazioni recuperino continuamente i dati dall'archivio dati e li aggiungano alla cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-128">Don't make the expiration period too short because this can cause applications to continually retrieve data from the data store and add it to the cache.</span></span> <span data-ttu-id="73b91-129">Analogamente, non impostare un periodo di scadenza troppo lungo per evitare che i dati memorizzati nella cache diventino obsoleti.</span><span class="sxs-lookup"><span data-stu-id="73b91-129">Similarly, don't make the expiration period so long that the cached data is likely to become stale.</span></span> <span data-ttu-id="73b91-130">Tenere presente che la memorizzazione nella cache è più efficace per i dati relativamente statici o per dati letti frequentemente.</span><span class="sxs-lookup"><span data-stu-id="73b91-130">Remember that caching is most effective for relatively static data, or data that is read frequently.</span></span>

<span data-ttu-id="73b91-131">**Rimozione dei dati**.</span><span class="sxs-lookup"><span data-stu-id="73b91-131">**Evicting data**.</span></span> <span data-ttu-id="73b91-132">Le dimensioni della maggior parte delle cache è limitata rispetto all'archivio dati da cui provengono i dati, di conseguenza, se necessario i dati verranno rimossi.</span><span class="sxs-lookup"><span data-stu-id="73b91-132">Most caches have a limited size compared to the data store where the data originates, and they'll evict data if necessary.</span></span> <span data-ttu-id="73b91-133">Per selezionare gli elementi da rimuovere, la maggior parte delle cache adotta il criterio di minor utilizzo in un determinato periodo, ma è possibile scegliere un criterio personalizzato.</span><span class="sxs-lookup"><span data-stu-id="73b91-133">Most caches adopt a least-recently-used policy for selecting items to evict, but this might be customizable.</span></span> <span data-ttu-id="73b91-134">Configurare la proprietà di scadenza globale e altre proprietà della cache, nonché la proprietà di scadenza di ogni elemento memorizzato nella cache, per garantire che la cache sia economicamente conveniente.</span><span class="sxs-lookup"><span data-stu-id="73b91-134">Configure the global expiration property and other properties of the cache, and the expiration property of each cached item, to ensure that the cache is cost effective.</span></span> <span data-ttu-id="73b91-135">Non è sempre opportuno applicare criteri di rimozione globale a ogni elemento della cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-135">It isn't always appropriate to apply a global eviction policy to every item in the cache.</span></span> <span data-ttu-id="73b91-136">Se, ad esempio, recuperare dall'archivio dati un elemento memorizzato nella cache è molto oneroso, può essere utile mantenere questo elemento nella cache a scapito di elementi usati più spesso, ma meno onerosi.</span><span class="sxs-lookup"><span data-stu-id="73b91-136">For example, if a cached item is very expensive to retrieve from the data store, it can be beneficial to keep this item in the cache at the expense of more frequently accessed but less costly items.</span></span>

<span data-ttu-id="73b91-137">**Inizializzazione della cache**.</span><span class="sxs-lookup"><span data-stu-id="73b91-137">**Priming the cache**.</span></span> <span data-ttu-id="73b91-138">In molte soluzioni la cache viene prepopolata con i dati che saranno probabilmente necessari durante il processo di avvio di un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="73b91-138">Many solutions prepopulate the cache with the data that an application is likely to need as part of the startup processing.</span></span> <span data-ttu-id="73b91-139">Il modello cache-aside può risultare comunque utile se alcuni di questi dati scadono o sono stati rimossi.</span><span class="sxs-lookup"><span data-stu-id="73b91-139">The Cache-Aside pattern can still be useful if some of this data expires or is evicted.</span></span>

<span data-ttu-id="73b91-140">**Coerenza**.</span><span class="sxs-lookup"><span data-stu-id="73b91-140">**Consistency**.</span></span> <span data-ttu-id="73b91-141">L'implementazione del modello cache-aside non garantisce la coerenza tra l'archivio dati e la cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-141">Implementing the Cache-Aside pattern doesn't guarantee consistency between the data store and the cache.</span></span> <span data-ttu-id="73b91-142">Un processo esterno può modificare in qualsiasi momento un elemento nell'archivio dati e questa modifica potrebbe non essere presente nella cache fino al successivo caricamento dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="73b91-142">An item in the data store can be changed at any time by an external process, and this change might not be reflected in the cache until the next time the item is loaded.</span></span> <span data-ttu-id="73b91-143">In un sistema che replica i dati tra archivi dati, questo problema può diventare grave se la sincronizzazione viene eseguita frequentemente.</span><span class="sxs-lookup"><span data-stu-id="73b91-143">In a system that replicates data across data stores, this problem can become serious if synchronization occurs frequently.</span></span>

<span data-ttu-id="73b91-144">**Memorizzazione nella cache locale (in memoria)**.</span><span class="sxs-lookup"><span data-stu-id="73b91-144">**Local (in-memory) caching**.</span></span> <span data-ttu-id="73b91-145">Una cache può essere locale per un'istanza dell'applicazione ed essere archiviata in memoria.</span><span class="sxs-lookup"><span data-stu-id="73b91-145">A cache could be local to an application instance and stored in-memory.</span></span> <span data-ttu-id="73b91-146">Il modello cache-aside può essere utile in questo ambiente se un'applicazione accede ripetutamente agli stessi dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-146">Cache-aside can be useful in this environment if an application repeatedly accesses the same data.</span></span> <span data-ttu-id="73b91-147">Una cache locale è però privata, di conseguenza istanze diverse dell'applicazione potrebbero contenere una copia degli stessi dati memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-147">However, a local cache is private and so different application instances could each have a copy of the same cached data.</span></span> <span data-ttu-id="73b91-148">Tali dati potrebbero diventare rapidamente incoerenti tra le cache, di conseguenza potrebbe essere necessario far scadere i dati contenuti in una cache privata e aggiornarli più frequentemente.</span><span class="sxs-lookup"><span data-stu-id="73b91-148">This data could quickly become inconsistent between caches, so it might be necessary to expire data held in a private cache and refresh it more frequently.</span></span> <span data-ttu-id="73b91-149">In questi scenari provare ad analizzare l'uso di un meccanismo di memorizzazione nella cache condivisa o distribuita.</span><span class="sxs-lookup"><span data-stu-id="73b91-149">In these scenarios, consider investigating the use of a shared or a distributed caching mechanism.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="73b91-150">Quando usare questo modello</span><span class="sxs-lookup"><span data-stu-id="73b91-150">When to use this pattern</span></span>

<span data-ttu-id="73b91-151">Usare questo modello quando:</span><span class="sxs-lookup"><span data-stu-id="73b91-151">Use this pattern when:</span></span>

- <span data-ttu-id="73b91-152">Una cache non offre operazioni native di read-through e write-through.</span><span class="sxs-lookup"><span data-stu-id="73b91-152">A cache doesn't provide native read-through and write-through operations.</span></span>
- <span data-ttu-id="73b91-153">La richiesta di risorse è imprevedibile.</span><span class="sxs-lookup"><span data-stu-id="73b91-153">Resource demand is unpredictable.</span></span> <span data-ttu-id="73b91-154">Questo modello consente alle applicazioni di caricare dati su richiesta.</span><span class="sxs-lookup"><span data-stu-id="73b91-154">This pattern enables applications to load data on demand.</span></span> <span data-ttu-id="73b91-155">Non è in grado di stabilire in anticipo i dati che verranno richiesti da un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="73b91-155">It makes no assumptions about which data an application will require in advance.</span></span>

<span data-ttu-id="73b91-156">Questo modello potrebbe non essere adatto:</span><span class="sxs-lookup"><span data-stu-id="73b91-156">This pattern might not be suitable:</span></span>

- <span data-ttu-id="73b91-157">Quando il set di dati memorizzati nella cache è statico.</span><span class="sxs-lookup"><span data-stu-id="73b91-157">When the cached data set is static.</span></span> <span data-ttu-id="73b91-158">Se lo spazio disponibile della cache è sufficiente per i dati, inizializzare la cache con i dati all'avvio e applicare un criterio che impedisca la scadenza dei dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-158">If the data will fit into the available cache space, prime the cache with the data on startup and apply a policy that prevents the data from expiring.</span></span>
- <span data-ttu-id="73b91-159">Per la memorizzazione nella cache delle informazioni sullo stato della sessione in un'applicazione Web ospitata in una Web farm.</span><span class="sxs-lookup"><span data-stu-id="73b91-159">For caching session state information in a web application hosted in a web farm.</span></span> <span data-ttu-id="73b91-160">In questo ambiente è consigliabile evitare di introdurre dipendenze basate sull'affinità client-server.</span><span class="sxs-lookup"><span data-stu-id="73b91-160">In this environment, you should avoid introducing dependencies based on client-server affinity.</span></span>

## <a name="example"></a><span data-ttu-id="73b91-161">Esempio</span><span class="sxs-lookup"><span data-stu-id="73b91-161">Example</span></span>

<span data-ttu-id="73b91-162">In Microsoft Azure è possibile usare Cache Redis di Azure per creare una cache distribuita condivisibile da più istanze di un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="73b91-162">In Microsoft Azure you can use Azure Redis Cache to create a distributed cache that can be shared by multiple instances of an application.</span></span> 

<span data-ttu-id="73b91-163">Gli esempi di codice seguenti usano il client [StackExchange.Redis], che è una libreria client Redis scritta per .NET.</span><span class="sxs-lookup"><span data-stu-id="73b91-163">This following code examples use the [StackExchange.Redis] client, which is a Redis client library written for .NET.</span></span> <span data-ttu-id="73b91-164">Per connettersi a un'istanza di Cache Redis di Azure, chiamare il metodo statico `ConnectionMultiplexer.Connect` e passare la stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="73b91-164">To connect to an Azure Redis Cache instance, call the static `ConnectionMultiplexer.Connect` method and pass in the connection string.</span></span> <span data-ttu-id="73b91-165">Il metodo restituisce un elemento `ConnectionMultiplexer` che rappresenta la connessione.</span><span class="sxs-lookup"><span data-stu-id="73b91-165">The method returns a `ConnectionMultiplexer` that represents the connection.</span></span> <span data-ttu-id="73b91-166">Un approccio per la condivisione di un'istanza di `ConnectionMultiplexer` nell'applicazione prevede una proprietà statica che restituisce un'istanza connessa, simile a quanto illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="73b91-166">One approach to sharing a `ConnectionMultiplexer` instance in your application is to have a static property that returns a connected instance, similar to the following example.</span></span> <span data-ttu-id="73b91-167">Questo approccio costituisce un modo thread-safe per inizializzare solo una singola istanza connessa.</span><span class="sxs-lookup"><span data-stu-id="73b91-167">This approach provides a thread-safe way to initialize only a single connected instance.</span></span>

```csharp
private static ConnectionMultiplexer Connection;

// Redis Connection string info
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
    return ConnectionMultiplexer.Connect(cacheConnection);
});

public static ConnectionMultiplexer Connection => lazyConnection.Value;
```

<span data-ttu-id="73b91-168">Il metodo `GetMyEntityAsync` nell'esempio di codice seguente illustra un'implementazione del modello cache-aside.</span><span class="sxs-lookup"><span data-stu-id="73b91-168">The `GetMyEntityAsync` method in the following code example shows an implementation of the Cache-Aside pattern.</span></span> <span data-ttu-id="73b91-169">Questo metodo recupera un oggetto dalla cache usando l'approccio read-through.</span><span class="sxs-lookup"><span data-stu-id="73b91-169">This method retrieves an object from the cache using the read-through approach.</span></span>

<span data-ttu-id="73b91-170">Per l'identificazione di un oggetto viene usato come chiave un ID di tipo Integer.</span><span class="sxs-lookup"><span data-stu-id="73b91-170">An object is identified by using an integer ID as the key.</span></span> <span data-ttu-id="73b91-171">Il metodo `GetMyEntityAsync` prova a recuperare un elemento dalla cache usando questa chiave.</span><span class="sxs-lookup"><span data-stu-id="73b91-171">The `GetMyEntityAsync` method tries to retrieve an item with this key from the cache.</span></span> <span data-ttu-id="73b91-172">Se trovato, l'eventuale elemento corrispondente viene restituito.</span><span class="sxs-lookup"><span data-stu-id="73b91-172">If a matching item is found, it's returned.</span></span> <span data-ttu-id="73b91-173">Se la cache non contiene alcun elemento corrispondente, il metodo `GetMyEntityAsync` recupera l'oggetto da un archivio dati, lo aggiunge alla cache e quindi lo restituisce.</span><span class="sxs-lookup"><span data-stu-id="73b91-173">If there's no match in the cache, the `GetMyEntityAsync` method retrieves the object from a data store, adds it to the cache, and then returns it.</span></span> <span data-ttu-id="73b91-174">L'esempio non include il codice che legge effettivamente i dati dall'archivio dati, perché tale codice dipende dall'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-174">The code that actually reads the data from the data store is not shown here, because it depends on the data store.</span></span> <span data-ttu-id="73b91-175">Tenere presente che l'elemento memorizzato nella cache è configurato per la scadenza per impedire che diventi obsoleto se viene aggiornato in un'altra posizione.</span><span class="sxs-lookup"><span data-stu-id="73b91-175">Note that the cached item is configured to expire to prevent it from becoming stale if it's updated elsewhere.</span></span>


```csharp
// Set five minute expiration as a default
private const double DefaultExpirationTimeInMinutes = 5.0;

public async Task<MyEntity> GetMyEntityAsync(int id)
{
  // Define a unique key for this method and its parameters.
  var key = $"MyEntity:{id}";
  var cache = Connection.GetDatabase();
  
  // Try to get the entity from the cache.
  var json = await cache.StringGetAsync(key).ConfigureAwait(false);
  var value = string.IsNullOrWhiteSpace(json) 
                ? default(MyEntity) 
                : JsonConvert.DeserializeObject<MyEntity>(json);
  
  if (value == null) // Cache miss
  {
    // If there's a cache miss, get the entity from the original store and cache it.
    // Code has been omitted because it's data store dependent.  
    value = ...;

    // Avoid caching a null value.
    if (value != null)
    {
      // Put the item in the cache with a custom expiration time that 
      // depends on how critical it is to have stale data.
      await cache.StringSetAsync(key, JsonConvert.SerializeObject(value)).ConfigureAwait(false);
      await cache.KeyExpireAsync(key, TimeSpan.FromMinutes(DefaultExpirationTimeInMinutes)).ConfigureAwait(false);
    }
  }

  return value;
}
```

>  <span data-ttu-id="73b91-176">Gli esempi usano Cache Redis per accedere all'archivio e recuperare informazioni dalla cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-176">The examples use Redis Cache to access the store and retrieve information from the cache.</span></span> <span data-ttu-id="73b91-177">Per altre informazioni, vedere [Come usare Cache Redis di Azure](https://docs.microsoft.com/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache) e [Come creare un'app Web con la cache Redis](https://docs.microsoft.com/azure/redis-cache/cache-web-app-howto).</span><span class="sxs-lookup"><span data-stu-id="73b91-177">For more information, see [Using Microsoft Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache) and [How to create a Web App with Redis Cache](https://docs.microsoft.com/azure/redis-cache/cache-web-app-howto)</span></span>

<span data-ttu-id="73b91-178">Il metodo `UpdateEntityAsync` illustrato di seguito spiega come invalidare un oggetto nella cache quando il valore viene modificato dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="73b91-178">The `UpdateEntityAsync` method shown below demonstrates how to invalidate an object in the cache when the value is changed by the application.</span></span> <span data-ttu-id="73b91-179">Il codice aggiorna l'archivio dati originale e quindi rimuove dalla cache l'elemento memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-179">The code updates the original data store and then removes the cached item from the cache.</span></span>

```csharp
public async Task UpdateEntityAsync(MyEntity entity)
{
    // Update the object in the original data store.
    await this.store.UpdateEntityAsync(entity).ConfigureAwait(false); 

    // Invalidate the current cache object.
    var cache = Connection.GetDatabase();
    var id = entity.Id;
    var key = $"MyEntity:{id}"; // The key for the cached object.
    await cache.KeyDeleteAsync(key).ConfigureAwait(false); // Delete this key from the cache.
}
```

> [!NOTE]
> <span data-ttu-id="73b91-180">L'ordine dei passaggi è importante.</span><span class="sxs-lookup"><span data-stu-id="73b91-180">The order of the steps is important.</span></span> <span data-ttu-id="73b91-181">Aggiornare l'archivio dati *prima* di rimuovere l'elemento dalla cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-181">Update the data store *before* removing the item from the cache.</span></span> <span data-ttu-id="73b91-182">Se si rimuove prima l'elemento memorizzato nella cache, per un breve arco di tempo un client potrebbe recuperare l'elemento prima che l'archivio dati venga aggiornato.</span><span class="sxs-lookup"><span data-stu-id="73b91-182">If you remove the cached item first, there is a small window of time when a client might fetch the item before the data store is updated.</span></span> <span data-ttu-id="73b91-183">Questo comporterà una perdita di dati nella cache (dal momento che l'elemento è stato rimosso dalla cache), di conseguenza l'elemento recuperato dall'archivio dati e aggiunto di nuovo nella cache sarà di una versione precedente</span><span class="sxs-lookup"><span data-stu-id="73b91-183">That will result in a cache miss (because the item was removed from the cache), causing the earlier version of the item to be fetched from the data store and added back into the cache.</span></span> <span data-ttu-id="73b91-184">e i dati memorizzati nella cache saranno obsoleti.</span><span class="sxs-lookup"><span data-stu-id="73b91-184">The result will be stale cache data.</span></span>


## <a name="related-guidance"></a><span data-ttu-id="73b91-185">Informazioni correlate</span><span class="sxs-lookup"><span data-stu-id="73b91-185">Related guidance</span></span> 

<span data-ttu-id="73b91-186">Per l'implementazione di questo modello possono risultare utili anche le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="73b91-186">The following information may be relevant when implementing this pattern:</span></span>

- <span data-ttu-id="73b91-187">[Informazioni aggiuntive sulla memorizzazione nella cache](https://docs.microsoft.com/azure/architecture/best-practices/caching).</span><span class="sxs-lookup"><span data-stu-id="73b91-187">[Caching Guidance](https://docs.microsoft.com/azure/architecture/best-practices/caching).</span></span> <span data-ttu-id="73b91-188">Include maggiori informazioni su come memorizzare nella cache i dati in una soluzione cloud ed esamina gli aspetti da considerare quando si implementa una cache.</span><span class="sxs-lookup"><span data-stu-id="73b91-188">Provides additional information on how you can cache data in a cloud solution, and the issues that you should consider when you implement a cache.</span></span>

- <span data-ttu-id="73b91-189">[Nozioni di base sulla coerenza dei dati](https://msdn.microsoft.com/library/dn589800.aspx).</span><span class="sxs-lookup"><span data-stu-id="73b91-189">[Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx).</span></span> <span data-ttu-id="73b91-190">Le applicazioni cloud usano in genere dati che vengono distribuiti in archivi dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-190">Cloud applications typically use data that's spread across data stores.</span></span> <span data-ttu-id="73b91-191">La gestione e il mantenimento della coerenza dei dati in questo ambiente rappresenta un aspetto critico del sistema, in particolare in relazione ai problemi di concorrenza e disponibilità che possono verificarsi.</span><span class="sxs-lookup"><span data-stu-id="73b91-191">Managing and maintaining data consistency in this environment is a critical aspect of the system, particularly the concurrency and availability issues that can arise.</span></span> <span data-ttu-id="73b91-192">Questo documento illustra i problemi relativi alla coerenza tra dati distribuiti e spiega in che modo un'applicazione può implementare controlli di coerenza per garantire la disponibilità dei dati.</span><span class="sxs-lookup"><span data-stu-id="73b91-192">This primer describes issues about consistency across distributed data, and summarizes how an application can implement eventual consistency to maintain the availability of data.</span></span>


[StackExchange.Redis]: https://github.com/StackExchange/StackExchange.Redis
