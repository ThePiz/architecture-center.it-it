---
title: Modello di hosting del contenuto statico
titleSuffix: Cloud Design Patterns
description: Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client.
keywords: schema progettuale
author: dragon119
ms.date: 01/04/2019
ms.custom: seodec18
ms.openlocfilehash: cf4f65e935a01e4d84b3cc82b5779edb729bd80e
ms.sourcegitcommit: 036cd03c39f941567e0de4bae87f4e2aa8c84cf8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2019
ms.locfileid: "54058183"
---
# <a name="static-content-hosting-pattern"></a><span data-ttu-id="f2a90-104">Modello di hosting del contenuto statico</span><span class="sxs-lookup"><span data-stu-id="f2a90-104">Static Content Hosting pattern</span></span>

<span data-ttu-id="f2a90-105">Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client.</span><span class="sxs-lookup"><span data-stu-id="f2a90-105">Deploy static content to a cloud-based storage service that can deliver them directly to the client.</span></span> <span data-ttu-id="f2a90-106">Questo può ridurre la necessità di istanze di calcolo potenzialmente dispendiose.</span><span class="sxs-lookup"><span data-stu-id="f2a90-106">This can reduce the need for potentially expensive compute instances.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="f2a90-107">Contesto e problema</span><span class="sxs-lookup"><span data-stu-id="f2a90-107">Context and problem</span></span>

<span data-ttu-id="f2a90-108">Le applicazioni Web includono in genere alcuni elementi del contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="f2a90-108">Web applications typically include some elements of static content.</span></span> <span data-ttu-id="f2a90-109">Il contenuto statico potrebbe includere pagine HTML e altre risorse, ad esempio immagini e documenti disponibili per il client, come parte di una pagina HTML, ad esempio immagini in linea con il testo, fogli di stile e file JavaScript lato client, o come download separati, quali documenti PDF.</span><span class="sxs-lookup"><span data-stu-id="f2a90-109">This static content might include HTML pages and other resources such as images and documents that are available to the client, either as part of an HTML page (such as inline images, style sheets, and client-side JavaScript files) or as separate downloads (such as PDF documents).</span></span>

<span data-ttu-id="f2a90-110">Nonostante siano ottimizzati per il rendering dinamico e la memorizzazione dell'output nella cache, i server Web devono comunque gestire le richieste per il download del contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="f2a90-110">Although web servers are optimized for dynamic rendering and output caching, they still have to handle requests to download static content.</span></span> <span data-ttu-id="f2a90-111">Usa cicli di elaborazione che potrebbero spesso essere usati in modo migliore.</span><span class="sxs-lookup"><span data-stu-id="f2a90-111">This consumes processing cycles that could often be put to better use.</span></span>

## <a name="solution"></a><span data-ttu-id="f2a90-112">Soluzione</span><span class="sxs-lookup"><span data-stu-id="f2a90-112">Solution</span></span>

<span data-ttu-id="f2a90-113">Nella maggior parte degli ambienti di hosting cloud, è possibile includere alcune risorse e pagine statiche di un'applicazione in un servizio di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="f2a90-113">In most cloud hosting environments, you can put some of an application's resources and static pages in a storage service.</span></span> <span data-ttu-id="f2a90-114">Il servizio di archiviazione potrà gestire le richieste relative a tali risorse riducendo il carico sulle risorse di calcolo che gestiscono le altre richieste Web.</span><span class="sxs-lookup"><span data-stu-id="f2a90-114">The storage service can serve requests for these resources, reducing load on the compute resources that handle other web requests.</span></span> <span data-ttu-id="f2a90-115">Il costo dell'archiviazione ospitata su cloud è in genere molto inferiore rispetto a quello per le istanze di calcolo.</span><span class="sxs-lookup"><span data-stu-id="f2a90-115">The cost for cloud-hosted storage is typically much less than for compute instances.</span></span>

<span data-ttu-id="f2a90-116">Quando si ospitano alcune parti di un'applicazione in un servizio di archiviazione, le principali considerazioni sono correlate alla distribuzione dell'applicazione e alla protezione delle risorse che non devono essere disponibili agli utenti anonimi.</span><span class="sxs-lookup"><span data-stu-id="f2a90-116">When hosting some parts of an application in a storage service, the main considerations are related to deployment of the application and to securing resources that aren't intended to be available to anonymous users.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="f2a90-117">Considerazioni e problemi</span><span class="sxs-lookup"><span data-stu-id="f2a90-117">Issues and considerations</span></span>

<span data-ttu-id="f2a90-118">Prima di decidere come implementare questo modello, considerare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="f2a90-118">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="f2a90-119">Il servizio di archiviazione ospitato deve esporre un endpoint HTTP a cui gli utenti possono accedere per scaricare le risorse statiche.</span><span class="sxs-lookup"><span data-stu-id="f2a90-119">The hosted storage service must expose an HTTP endpoint that users can access to download the static resources.</span></span> <span data-ttu-id="f2a90-120">Anche alcuni servizi di archiviazione supportano HTTPS, pertanto è possibile ospitare le risorse nei servizi di archiviazione che richiedono l'SSL.</span><span class="sxs-lookup"><span data-stu-id="f2a90-120">Some storage services also support HTTPS, so it's possible to host resources in storage services that require SSL.</span></span>

- <span data-ttu-id="f2a90-121">Per prestazioni e disponibilità ottimali, usare una rete per la distribuzione di contenuti per memorizzare nella cache i contenuti del contenitore di archiviazione nei diversi datacenter di tutto il mondo.</span><span class="sxs-lookup"><span data-stu-id="f2a90-121">For maximum performance and availability, consider using a content delivery network (CDN) to cache the contents of the storage container in multiple datacenters around the world.</span></span> <span data-ttu-id="f2a90-122">Tuttavia, probabilmente sarà necessario pagare per l'uso della rete CDN.</span><span class="sxs-lookup"><span data-stu-id="f2a90-122">However, you'll likely have to pay for using the CDN.</span></span>

- <span data-ttu-id="f2a90-123">Gli account di archiviazione sono spesso sottoposti a replica geografica per impostazione predefinita per offrire resilienza in caso di eventi che potrebbero interessare un datacenter.</span><span class="sxs-lookup"><span data-stu-id="f2a90-123">Storage accounts are often geo-replicated by default to provide resiliency against events that might affect a datacenter.</span></span> <span data-ttu-id="f2a90-124">Ciò significa che l'indirizzo IP potrebbe cambiare, ma l'URL resta invariato.</span><span class="sxs-lookup"><span data-stu-id="f2a90-124">This means that the IP address might change, but the URL will remain the same.</span></span>

- <span data-ttu-id="f2a90-125">Quando alcuni contenuti si trovano in un account di archiviazione e altri in un'istanza di calcolo ospitata, la distribuzione e l'aggiornamento dell'applicazione diventano più complessi.</span><span class="sxs-lookup"><span data-stu-id="f2a90-125">When some content is located in a storage account and other content is in a hosted compute instance, it becomes more challenging to deploy and update the application.</span></span> <span data-ttu-id="f2a90-126">Potrebbe essere necessario eseguire distribuzioni separate e controllare la versione dell'applicazione e del contenuto per gestirla in modo più semplice&mdash;soprattutto quando il contenuto statico include file script o componenti dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="f2a90-126">You might have to perform separate deployments, and version the application and content to manage it more easily&mdash;especially when the static content includes script files or UI components.</span></span> <span data-ttu-id="f2a90-127">Tuttavia, se è necessario aggiornare solo le risorse statiche, basta caricarle nell'account di archiviazione senza dover ridistribuire il pacchetto dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f2a90-127">However, if only static resources have to be updated, they can simply be uploaded to the storage account without needing to redeploy the application package.</span></span>

- <span data-ttu-id="f2a90-128">I servizi di archiviazione potrebbero non supportare l'uso di nomi di dominio personalizzati.</span><span class="sxs-lookup"><span data-stu-id="f2a90-128">Storage services might not support the use of custom domain names.</span></span> <span data-ttu-id="f2a90-129">In questo caso è necessario specificare l'URL completo delle risorse nei collegamenti, in quanto si troveranno in un dominio diverso dal contenuto generato in modo dinamico che contiene i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="f2a90-129">In this case it's necessary to specify the full URL of the resources in links because they'll be in a different domain from the dynamically-generated content containing the links.</span></span>

- <span data-ttu-id="f2a90-130">I contenitori di archiviazione devono essere configurati per l'accesso in lettura pubblico, un'operazione fondamentale per garantire che non siano configurati per l'accesso pubblico in scrittura e impedire agli utenti di caricare il contenuto.</span><span class="sxs-lookup"><span data-stu-id="f2a90-130">The storage containers must be configured for public read access, but it's vital to ensure that they aren't configured for public write access to prevent users being able to upload content.</span></span>

- <span data-ttu-id="f2a90-131">Valutare la possibilità di usare un passepartout o un token per controllare l'accesso alle risorse che non devono essere accessibili in modo anonimo.</span><span class="sxs-lookup"><span data-stu-id="f2a90-131">Consider using a valet key or token to control access to resources that shouldn't be available anonymously.</span></span> <span data-ttu-id="f2a90-132">Per altre informazioni, vedere [Modello di passepartout](./valet-key.md).</span><span class="sxs-lookup"><span data-stu-id="f2a90-132">See the [Valet Key pattern](./valet-key.md) for more information.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="f2a90-133">Quando usare questo modello</span><span class="sxs-lookup"><span data-stu-id="f2a90-133">When to use this pattern</span></span>

<span data-ttu-id="f2a90-134">Questo modello è utile per:</span><span class="sxs-lookup"><span data-stu-id="f2a90-134">This pattern is useful for:</span></span>

- <span data-ttu-id="f2a90-135">Ridurre al minimo il costo di hosting di applicazioni e siti Web che contengono risorse statiche.</span><span class="sxs-lookup"><span data-stu-id="f2a90-135">Minimizing the hosting cost for websites and applications that contain some static resources.</span></span>

- <span data-ttu-id="f2a90-136">Ridurre al minimo il costo di hosting per i siti Web costituiti solo da contenuto statico e risorse.</span><span class="sxs-lookup"><span data-stu-id="f2a90-136">Minimizing the hosting cost for websites that consist of only static content and resources.</span></span> <span data-ttu-id="f2a90-137">In base alle funzionalità del sistema di archiviazione del provider di hosting, potrebbe essere possibile ospitare completamente un sito Web interamente statico in un account di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="f2a90-137">Depending on the capabilities of the hosting provider's storage system, it might be possible to entirely host a fully static website in a storage account.</span></span>

- <span data-ttu-id="f2a90-138">Esposizione di risorse e contenuto statico per applicazioni in esecuzione in ambienti di hosting diversi o in server locali.</span><span class="sxs-lookup"><span data-stu-id="f2a90-138">Exposing static resources and content for applications running in other hosting environments or on-premises servers.</span></span>

- <span data-ttu-id="f2a90-139">Individuare il contenuto in più aree geografiche tramite una rete di distribuzione dei contenuti che memorizza nella cache il contenuto dell'account di archiviazione in più datacenter in tutto il mondo.</span><span class="sxs-lookup"><span data-stu-id="f2a90-139">Locating content in more than one geographical area using a content delivery network that caches the contents of the storage account in multiple datacenters around the world.</span></span>

- <span data-ttu-id="f2a90-140">Monitorare i costi e l'uso della larghezza di banda.</span><span class="sxs-lookup"><span data-stu-id="f2a90-140">Monitoring costs and bandwidth usage.</span></span> <span data-ttu-id="f2a90-141">L'uso di un account di archiviazione separato per una parte o per l'intero contenuto statico consente una più semplice separazione dei costi da quelli di hosting e di runtime.</span><span class="sxs-lookup"><span data-stu-id="f2a90-141">Using a separate storage account for some or all of the static content allows the costs to be more easily separated from hosting and runtime costs.</span></span>

<span data-ttu-id="f2a90-142">Questo modello può non essere utile nelle situazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="f2a90-142">This pattern might not be useful in the following situations:</span></span>

- <span data-ttu-id="f2a90-143">L'applicazione deve eseguire alcune operazioni di elaborazione sul contenuto statico prima di distribuirlo al client.</span><span class="sxs-lookup"><span data-stu-id="f2a90-143">The application needs to perform some processing on the static content before delivering it to the client.</span></span> <span data-ttu-id="f2a90-144">Ad esempio, potrebbe essere necessario aggiungere un timestamp a un documento.</span><span class="sxs-lookup"><span data-stu-id="f2a90-144">For example, it might be necessary to add a timestamp to a document.</span></span>

- <span data-ttu-id="f2a90-145">Il volume del contenuto statico è molto ridotto.</span><span class="sxs-lookup"><span data-stu-id="f2a90-145">The volume of static content is very small.</span></span> <span data-ttu-id="f2a90-146">Il sovraccarico per il recupero del contenuto da un archivio separato può annullare il vantaggio del costo della separazione dalla risorsa di calcolo.</span><span class="sxs-lookup"><span data-stu-id="f2a90-146">The overhead of retrieving this content from separate storage can outweigh the cost benefit of separating it out from the compute resource.</span></span>

## <a name="example"></a><span data-ttu-id="f2a90-147">Esempio</span><span class="sxs-lookup"><span data-stu-id="f2a90-147">Example</span></span>

<span data-ttu-id="f2a90-148">Archiviazione di Azure supporta la gestione di contenuto statico direttamente da un contenitore di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="f2a90-148">Azure Storage supports serving static content directly from a storage container.</span></span> <span data-ttu-id="f2a90-149">I file vengono gestiti tramite richieste di accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="f2a90-149">Files are served through anonymous access requests.</span></span> <span data-ttu-id="f2a90-150">Per impostazione predefinita, i file hanno un URL in un sottodominio di `core.windows.net`, ad esempio `https://contoso.z4.web.core.windows.net/image.png`.</span><span class="sxs-lookup"><span data-stu-id="f2a90-150">By default, files have a URL in a subdomain of `core.windows.net`, such as `https://contoso.z4.web.core.windows.net/image.png`.</span></span> <span data-ttu-id="f2a90-151">È possibile configurare un nome di dominio personalizzato e usare Rete CDN di Azure per accedere ai file tramite HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f2a90-151">You can configure a custom domain name, and use Azure CDN to access the files over HTTPS.</span></span> <span data-ttu-id="f2a90-152">Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="f2a90-152">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

![Distribuzione delle parti statiche di un'applicazione direttamente da un servizio di archiviazione](./_images/static-content-hosting-pattern.png)

<span data-ttu-id="f2a90-154">L'hosting di siti Web statici rende i file disponibili per l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="f2a90-154">Static website hosting makes the files available for anonymous access.</span></span> <span data-ttu-id="f2a90-155">Se è necessario controllare quali utenti possono accedere ai file, è possibile archiviare i file in un archivio BLOB di Azure e quindi generare [firme di accesso condiviso](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) per limitare l'accesso.</span><span class="sxs-lookup"><span data-stu-id="f2a90-155">If you need to control who can access the files, you can store files in Azure blob storage and then generate [shared access signatures](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) to limit access.</span></span>

<span data-ttu-id="f2a90-156">I collegamenti nelle pagine distribuite al client devono specificare l'URL completo della risorsa.</span><span class="sxs-lookup"><span data-stu-id="f2a90-156">The links in the pages delivered to the client must specify the full URL of the resource.</span></span> <span data-ttu-id="f2a90-157">Se la risorsa è protetta con un passepartout, ad esempio una firma di accesso condiviso, la firma deve essere inclusa nell'URL.</span><span class="sxs-lookup"><span data-stu-id="f2a90-157">If the resource is protected with a valet key, such as a shared access signature, this signature must be included in the URL.</span></span>

<span data-ttu-id="f2a90-158">In [GitHub][sample-app] è disponibile un'applicazione di esempio che illustra l'uso di un archivio esterno per le risorse statiche.</span><span class="sxs-lookup"><span data-stu-id="f2a90-158">A sample application that demonstrates using external storage for static resources is available on [GitHub][sample-app].</span></span> <span data-ttu-id="f2a90-159">L'esempio usa file di configurazione che specificano l'account di archiviazione e il contenitore in cui si trova il contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="f2a90-159">This sample uses configuration files to specify the storage account and container that holds the static content.</span></span>

```xml
<Setting name="StaticContent.StorageConnectionString"
         value="UseDevelopmentStorage=true" />
<Setting name="StaticContent.Container" value="static-content" />
```

<span data-ttu-id="f2a90-160">La classe `Settings` nel file Settings.cs del progetto StaticContentHosting.Web contiene i metodi per estrarre questi valori e generare un valore di stringa contenente l'URL del contenitore dell'account di archiviazione cloud.</span><span class="sxs-lookup"><span data-stu-id="f2a90-160">The `Settings` class in the file Settings.cs of the StaticContentHosting.Web project contains methods to extract these values and build a string value containing the cloud storage account container URL.</span></span>

```csharp
public class Settings
{
  public static string StaticContentStorageConnectionString {
    get
    {
      return RoleEnvironment.GetConfigurationSettingValue(
                              "StaticContent.StorageConnectionString");
    }
  }

  public static string StaticContentContainer
  {
    get
    {
      return RoleEnvironment.GetConfigurationSettingValue("StaticContent.Container");
    }
  }

  public static string StaticContentBaseUrl
  {
    get
    {
      var account = CloudStorageAccount.Parse(StaticContentStorageConnectionString);

      return string.Format("{0}/{1}", account.BlobEndpoint.ToString().TrimEnd('/'),
                                      StaticContentContainer.TrimStart('/'));
    }
  }
}
```

<span data-ttu-id="f2a90-161">La classe `StaticContentUrlHtmlHelper` nel file StaticContentUrlHtmlHelper.cs espone un metodo denominato `StaticContentUrl` che genera un URL contenente il percorso all'account di archiviazione cloud, se l'URL passato inizia con il carattere del percorso radice ASP.NET (~).</span><span class="sxs-lookup"><span data-stu-id="f2a90-161">The `StaticContentUrlHtmlHelper` class in the file StaticContentUrlHtmlHelper.cs exposes a method named `StaticContentUrl` that generates a URL containing the path to the cloud storage account if the URL passed to it starts with the ASP.NET root path character (~).</span></span>

```csharp
public static class StaticContentUrlHtmlHelper
{
  public static string StaticContentUrl(this HtmlHelper helper, string contentPath)
  {
    if (contentPath.StartsWith("~"))
    {
      contentPath = contentPath.Substring(1);
    }

    contentPath = string.Format("{0}/{1}", Settings.StaticContentBaseUrl.TrimEnd('/'),
                                contentPath.TrimStart('/'));

    var url = new UrlHelper(helper.ViewContext.RequestContext);

    return url.Content(contentPath);
  }
}
```

<span data-ttu-id="f2a90-162">Il file Index.cshtml nella cartella Visualizzazioni\Home contiene un elemento immagine che usa il metodo `StaticContentUrl` per creare l'URL relativo all'attributo `src`.</span><span class="sxs-lookup"><span data-stu-id="f2a90-162">The file Index.cshtml in the Views\Home folder contains an image element that uses the `StaticContentUrl` method to create the URL for its `src` attribute.</span></span>

```html
<img src="@Html.StaticContentUrl("~/media/orderedList1.png")" alt="Test Image" />
```

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="f2a90-163">Modelli correlati e informazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f2a90-163">Related patterns and guidance</span></span>

- <span data-ttu-id="f2a90-164">[Esempio di hosting di contenuto statico][sample-app]:</span><span class="sxs-lookup"><span data-stu-id="f2a90-164">[Static Content Hosting sample][sample-app].</span></span> <span data-ttu-id="f2a90-165">applicazione di esempio che illustra questo modello.</span><span class="sxs-lookup"><span data-stu-id="f2a90-165">A sample application that demonstrates this pattern.</span></span>
- <span data-ttu-id="f2a90-166">[Modello di passepartout](./valet-key.md):</span><span class="sxs-lookup"><span data-stu-id="f2a90-166">[Valet Key pattern](./valet-key.md).</span></span> <span data-ttu-id="f2a90-167">se non è previsto che le risorse di destinazione siano disponibili per utenti anonimi, usare questo modello per limitare l'accesso diretto.</span><span class="sxs-lookup"><span data-stu-id="f2a90-167">If the target resources aren't supposed to be available to anonymous users, use this pattern to restrict direct access.</span></span>
- <span data-ttu-id="f2a90-168">[Applicazione Web serverless in Azure](../reference-architectures/serverless/web-app.md):</span><span class="sxs-lookup"><span data-stu-id="f2a90-168">[Serverless web application on Azure](../reference-architectures/serverless/web-app.md).</span></span> <span data-ttu-id="f2a90-169">architettura di riferimento che usa l'hosting di siti Web statici con Funzioni di Azure per implementare un'app Web serverless.</span><span class="sxs-lookup"><span data-stu-id="f2a90-169">A reference architecture that uses static website hosting with Azure Functions to implement a serverless web app.</span></span>

[sample-app]: https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting
