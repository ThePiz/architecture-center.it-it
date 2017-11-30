---
title: Memorizzazione nella cache dei token di accesso in un'applicazione multi-tenant
description: Memorizzazione nella cache dei token di accesso usati per richiamare un'API Web back-end
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: web-api
pnp.series.next: adfs
ms.openlocfilehash: ed0def244f3229bbd3fdd0976574d13a345f9aee
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="cache-access-tokens"></a><span data-ttu-id="83de6-103">Memorizzare nella cache i token di accesso</span><span class="sxs-lookup"><span data-stu-id="83de6-103">Cache access tokens</span></span>

<span data-ttu-id="83de6-104">[![Codice di esempio](../_images/github.png) GitHub][sample application]</span><span class="sxs-lookup"><span data-stu-id="83de6-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="83de6-105">Ottenere un token di accesso OAuth è un'attività relativamente complessa, in quanto è necessaria una richiesta HTTP all'endpoint del token.</span><span class="sxs-lookup"><span data-stu-id="83de6-105">It's relatively expensive to get an OAuth access token, because it requires an HTTP request to the token endpoint.</span></span> <span data-ttu-id="83de6-106">È consigliabile quindi memorizzare nella cache i token, laddove possibile.</span><span class="sxs-lookup"><span data-stu-id="83de6-106">Therefore, it's good to cache tokens whenever possible.</span></span> <span data-ttu-id="83de6-107">La [Azure AD Authentication Library][ADAL] (ADAL) (Raccolta di autenticazione di Azure AD) salva automaticamente nella cache i token ottenuti da Azure AD, compresi i token di aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="83de6-107">The [Azure AD Authentication Library][ADAL] (ADAL)  automatically caches tokens obtained from Azure AD, including refresh tokens.</span></span>

<span data-ttu-id="83de6-108">ADAL fornisce un'implementazione predefinita della cache del token.</span><span class="sxs-lookup"><span data-stu-id="83de6-108">ADAL provides a default token cache implementation.</span></span> <span data-ttu-id="83de6-109">Tuttavia, questa cache dei token è progettata per le app client native e *non* è adatta alle app Web:</span><span class="sxs-lookup"><span data-stu-id="83de6-109">However, this token cache is intended for native client apps, and is *not* suitable for web apps:</span></span>

* <span data-ttu-id="83de6-110">Si tratta di un'istanza statica e non thread-safe.</span><span class="sxs-lookup"><span data-stu-id="83de6-110">It is a static instance, and not thread safe.</span></span>
* <span data-ttu-id="83de6-111">Non supporta la scalabilità a un numero elevato di utenti, in quanto i token di tutti gli utenti vengono collocati nello stesso dizionario.</span><span class="sxs-lookup"><span data-stu-id="83de6-111">It doesn't scale to large numbers of users, because tokens from all users go into the same dictionary.</span></span>
* <span data-ttu-id="83de6-112">Non può essere condivisa tra i server Web in una farm.</span><span class="sxs-lookup"><span data-stu-id="83de6-112">It can't be shared across web servers in a farm.</span></span>

<span data-ttu-id="83de6-113">In alternativa, è necessario implementare una cache di token personalizzata che deriva dalla classe `TokenCache` di ADAL, ma è adatta a un ambiente server e fornisce il livello di isolamento tra i token desiderato per utenti diversi.</span><span class="sxs-lookup"><span data-stu-id="83de6-113">Instead, you should implement a custom token cache that derives from the ADAL `TokenCache` class but is suitable for a server environment and provides the desirable level of isolation between tokens for different users.</span></span>

<span data-ttu-id="83de6-114">La classe `TokenCache` archivia un dizionario di token, indicizzati da autorità di certificazione, risorse, ID client e utente.</span><span class="sxs-lookup"><span data-stu-id="83de6-114">The `TokenCache` class stores a dictionary of tokens, indexed by issuer, resource, client ID, and user.</span></span> <span data-ttu-id="83de6-115">Una cache di token personalizzata deve scrivere questo dizionario in un archivio di backup, ad esempio una cache Redis.</span><span class="sxs-lookup"><span data-stu-id="83de6-115">A custom token cache should write this dictionary to a backing store, such as a Redis cache.</span></span>

<span data-ttu-id="83de6-116">Nell'applicazione Tailspin Surveys la classe `DistributedTokenCache` implementa la cache di token.</span><span class="sxs-lookup"><span data-stu-id="83de6-116">In the Tailspin Surveys application, the `DistributedTokenCache` class implements the token cache.</span></span> <span data-ttu-id="83de6-117">Questa implementazione usa l'astrazione [IDistributedCache][distributed-cache] da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83de6-117">This implementation uses the [IDistributedCache][distributed-cache] abstraction from ASP.NET Core.</span></span> <span data-ttu-id="83de6-118">In questo modo, qualsiasi implementazione di `IDistributedCache` può essere usata come archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="83de6-118">That way, any `IDistributedCache` implementation can be used as a backing store.</span></span>

* <span data-ttu-id="83de6-119">Per impostazione predefinita, l'app Surveys usa una cache Redis.</span><span class="sxs-lookup"><span data-stu-id="83de6-119">By default, the Surveys app uses a Redis cache.</span></span>
* <span data-ttu-id="83de6-120">Per un server Web d'istanza singola, è possibile usare la [cache in memoria][in-memory-cache] ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83de6-120">For a single-instance web server, you could use the ASP.NET Core [in-memory cache][in-memory-cache].</span></span> <span data-ttu-id="83de6-121">Questa opzione risulta valida anche per l'esecuzione locale dell'app durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="83de6-121">(This is also a good option for running the app locally during development.)</span></span>

<span data-ttu-id="83de6-122">`DistributedTokenCache` archivia i dati della cache come coppie chiave/valore nell'archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="83de6-122">`DistributedTokenCache` stores the cache data as key/value pairs in the backing store.</span></span> <span data-ttu-id="83de6-123">La chiave è costituita dall'ID utente e dall'ID client, pertanto l'archivio di backup contiene dati di cache separati per ogni combinazione univoca di utente/client.</span><span class="sxs-lookup"><span data-stu-id="83de6-123">The key is the user ID plus client ID, so the backing store holds separate cache data for each unique combination of user/client.</span></span>

![Cache del token](./images/token-cache.png)

<span data-ttu-id="83de6-125">L'archivio di backup viene partizionato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="83de6-125">The backing store is partitioned by user.</span></span> <span data-ttu-id="83de6-126">Per ogni richiesta HTTP, i token, per quel cliente, vengono letti dall'archivio di backup e caricati nel dizionario `TokenCache`.</span><span class="sxs-lookup"><span data-stu-id="83de6-126">For each HTTP request, the tokens for that user are read from the backing store and loaded into the `TokenCache` dictionary.</span></span> <span data-ttu-id="83de6-127">Se Redis è usato come archivio di backup, ogni istanza del server in un cluster di server legge/scrive nella stessa cache, e questo approccio scala a molti utenti.</span><span class="sxs-lookup"><span data-stu-id="83de6-127">If Redis is used as the backing store, every server instance in a server farm reads/writes to the same cache, and this approach scales to many users.</span></span>

## <a name="encrypting-cached-tokens"></a><span data-ttu-id="83de6-128">Crittografia dei token memorizzati nella cache</span><span class="sxs-lookup"><span data-stu-id="83de6-128">Encrypting cached tokens</span></span>
<span data-ttu-id="83de6-129">I token sono dati sensibili, perché garantiscono l'accesso alle risorse dell'utente.</span><span class="sxs-lookup"><span data-stu-id="83de6-129">Tokens are sensitive data, because they grant access to a user's resources.</span></span> <span data-ttu-id="83de6-130">(Per di più, a differenza di una password di un utente, non è possibile solo archiviare un hash del token.) Pertanto, è fondamentale proteggere i token in modo che non vengano compromessi.</span><span class="sxs-lookup"><span data-stu-id="83de6-130">(Moreover, unlike a user's password, you can't just store a hash of the token.) Therefore, it's critical to protect tokens from being compromised.</span></span> <span data-ttu-id="83de6-131">La cache di backup Redis è protetta da un codice di accesso e se qualcuno dovesse venire a conoscenza di tale codice, otterrebbe anche i token di accesso memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="83de6-131">The Redis-backed cache is protected by a password, but if someone obtains the password, they could get all of the cached access tokens.</span></span> <span data-ttu-id="83de6-132">Per questo motivo, la `DistributedTokenCache` crittografa tutto ciò che scrive nell'archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="83de6-132">For that reason, the `DistributedTokenCache` encrypts everything that it writes to the backing store.</span></span> <span data-ttu-id="83de6-133">La crittografia viene eseguita usando [Protezione dati][data-protection] in ASP.NET Core: le API di Consumer, configurazione, API di estensibilità e implementazione.</span><span class="sxs-lookup"><span data-stu-id="83de6-133">Encryption is done using the ASP.NET Core [data protection][data-protection] APIs.</span></span>

> [!NOTE]
> <span data-ttu-id="83de6-134">Se si distribuisce ai siti Web di Azure, viene eseguito il backup delle chiavi di crittografia nella risorsa di archiviazione di rete. Le chiavi vengono poi sincronizzate in tutti i computer (vedere [Key management and lifetime][key-management] (Gestione e durata delle chiavi)).</span><span class="sxs-lookup"><span data-stu-id="83de6-134">If you deploy to Azure Web Sites, the encryption keys are backed up to network storage and synchronized across all machines (see [Key management and lifetime][key-management]).</span></span> <span data-ttu-id="83de6-135">Per impostazione predefinita, le chiavi non sono crittografate durante l'esecuzione in siti Web di Azure, ma è possibile [abilitare la crittografia tramite un certificato X.509][x509-cert-encryption].</span><span class="sxs-lookup"><span data-stu-id="83de6-135">By default, keys are not encrypted when running in Azure Web Sites, but you can [enable encryption using an X.509 certificate][x509-cert-encryption].</span></span>
> 
> 

## <a name="distributedtokencache-implementation"></a><span data-ttu-id="83de6-136">Implementazione di DistributedTokenCache</span><span class="sxs-lookup"><span data-stu-id="83de6-136">DistributedTokenCache implementation</span></span>
<span data-ttu-id="83de6-137">La classe `DistributedTokenCache` deriva dalla classe [TokenCache][tokencache-class] di ADAL.</span><span class="sxs-lookup"><span data-stu-id="83de6-137">The `DistributedTokenCache` class derives from the ADAL [TokenCache][tokencache-class] class.</span></span>

<span data-ttu-id="83de6-138">Nel costruttore, la classe `DistributedTokenCache` crea una chiave per l'utente corrente e carica la cache dall'archivio di backup:</span><span class="sxs-lookup"><span data-stu-id="83de6-138">In the constructor, the `DistributedTokenCache` class creates a key for the current user and loads the cache from the backing store:</span></span>

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

<span data-ttu-id="83de6-139">La chiave viene creata concatenando l'ID utente e l'ID client,</span><span class="sxs-lookup"><span data-stu-id="83de6-139">The key is created by concatenating the user ID and client ID.</span></span> <span data-ttu-id="83de6-140">entrambi prelevati dalle attestazioni trovate in `ClaimsPrincipal` dell'utente:</span><span class="sxs-lookup"><span data-stu-id="83de6-140">Both of these are taken from claims found in the user's `ClaimsPrincipal`:</span></span>

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

<span data-ttu-id="83de6-141">Per caricare i dati della cache, leggere il BLOB serializzato dall'archivio di backup, e chiamare `TokenCache.Deserialize` per convertire il BLOB nei dati della cache.</span><span class="sxs-lookup"><span data-stu-id="83de6-141">To load the cache data, read the serialized blob from the backing store, and call `TokenCache.Deserialize` to convert the blob into cache data.</span></span>

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

<span data-ttu-id="83de6-142">Ogni volta che ADAL accede alla cache, genera un evento `AfterAccess` .</span><span class="sxs-lookup"><span data-stu-id="83de6-142">Whenever ADAL access the cache, it fires an `AfterAccess` event.</span></span> <span data-ttu-id="83de6-143">Se i dati della cache vengono modificati, la proprietà `HasStateChanged` è true.</span><span class="sxs-lookup"><span data-stu-id="83de6-143">If the cache data has changed, the `HasStateChanged` property is true.</span></span> <span data-ttu-id="83de6-144">In tal caso, aggiornare l'archivio di backup in modo che rifletta la modifica e quindi impostare `HasStateChanged` su false.</span><span class="sxs-lookup"><span data-stu-id="83de6-144">In that case, update the backing store to reflect the change, and then set `HasStateChanged` to false.</span></span>

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

<span data-ttu-id="83de6-145">TokenCache invia altri due eventi:</span><span class="sxs-lookup"><span data-stu-id="83de6-145">TokenCache sends two other events:</span></span>

* <span data-ttu-id="83de6-146">`BeforeWrite`.</span><span class="sxs-lookup"><span data-stu-id="83de6-146">`BeforeWrite`.</span></span> <span data-ttu-id="83de6-147">Chiamato immediatamente prima che ADAL scriva nella cache.</span><span class="sxs-lookup"><span data-stu-id="83de6-147">Called immediately before ADAL writes to the cache.</span></span> <span data-ttu-id="83de6-148">Ciò consente di implementare una strategia di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="83de6-148">You can use this to implement a concurrency strategy</span></span>
* <span data-ttu-id="83de6-149">`BeforeAccess`.</span><span class="sxs-lookup"><span data-stu-id="83de6-149">`BeforeAccess`.</span></span> <span data-ttu-id="83de6-150">Chiamato immediatamente prima che ADAL legga dalla cache.</span><span class="sxs-lookup"><span data-stu-id="83de6-150">Called immediately before ADAL reads from the cache.</span></span> <span data-ttu-id="83de6-151">In questo caso, è possibile ricaricare la cache per ottenere la versione più recente.</span><span class="sxs-lookup"><span data-stu-id="83de6-151">Here you can reload the cache to get the latest version.</span></span>

<span data-ttu-id="83de6-152">In questo esempio specifico, è stato ritenuto opportuno non gestire questi due eventi.</span><span class="sxs-lookup"><span data-stu-id="83de6-152">In our case, we decided not to handle these two events.</span></span>

* <span data-ttu-id="83de6-153">Per la concorrenza, prevale la scrittura più recente.</span><span class="sxs-lookup"><span data-stu-id="83de6-153">For concurrency, last write wins.</span></span> <span data-ttu-id="83de6-154">Questa opzione è accettabile, perché i token vengono archiviati in modo indipendente per ogni utente e per ogni client, pertanto si verificherà un conflitto solo se lo stesso utente ha due sessioni di accesso simultanee.</span><span class="sxs-lookup"><span data-stu-id="83de6-154">That's OK, because tokens are stored independently for each user + client, so a conflict would only happen if the same user had two concurrent login sessions.</span></span>
* <span data-ttu-id="83de6-155">Per la lettura, la cache viene caricata per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="83de6-155">For reading, we load the cache on every request.</span></span> <span data-ttu-id="83de6-156">Le richieste sono di breve durata.</span><span class="sxs-lookup"><span data-stu-id="83de6-156">Requests are short lived.</span></span> <span data-ttu-id="83de6-157">Se la cache viene modificata in un determinato momento, la richiesta successiva rileverà il nuovo valore.</span><span class="sxs-lookup"><span data-stu-id="83de6-157">If the cache gets modified in that time, the next request will pick up the new value.</span></span>

<span data-ttu-id="83de6-158">[**Avanti**][client-assertion]</span><span class="sxs-lookup"><span data-stu-id="83de6-158">[**Next**][client-assertion]</span></span>

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[client-assertion]: ./client-assertion.md
[data-protection]: /aspnet/core/security/data-protection/
[distributed-cache]: /aspnet/core/performance/caching/distributed
[key-management]: /aspnet/core/security/data-protection/configuration/default-settings
[in-memory-cache]: /aspnet/core/performance/caching/memory
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: /aspnet/core/security/data-protection/implementation/key-encryption-at-rest#x509-certificate
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
