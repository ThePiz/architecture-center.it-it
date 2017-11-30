---
title: Hosting di contenuto statico
description: Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- data-management
- design-implementation
- performance-scalability
ms.openlocfilehash: deb15001bea2598d56a2793be78bbc3e7473bdf3
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="static-content-hosting-pattern"></a><span data-ttu-id="dbceb-104">Modello di hosting del contenuto statico</span><span class="sxs-lookup"><span data-stu-id="dbceb-104">Static Content Hosting pattern</span></span>

[!INCLUDE [header](../_includes/header.md)]

<span data-ttu-id="dbceb-105">Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client.</span><span class="sxs-lookup"><span data-stu-id="dbceb-105">Deploy static content to a cloud-based storage service that can deliver them directly to the client.</span></span> <span data-ttu-id="dbceb-106">Questo può ridurre la necessità di istanze di calcolo potenzialmente dispendiose.</span><span class="sxs-lookup"><span data-stu-id="dbceb-106">This can reduce the need for potentially expensive compute instances.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="dbceb-107">Contesto e problema</span><span class="sxs-lookup"><span data-stu-id="dbceb-107">Context and problem</span></span>

<span data-ttu-id="dbceb-108">Le applicazioni Web includono in genere alcuni elementi del contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="dbceb-108">Web applications typically include some elements of static content.</span></span> <span data-ttu-id="dbceb-109">Il contenuto statico potrebbe includere pagine HTML e altre risorse, ad esempio immagini e documenti disponibili per il client, come parte di una pagina HTML, ad esempio immagini in linea con il testo, fogli di stile e file JavaScript lato client, o come download separati, quali documenti PDF.</span><span class="sxs-lookup"><span data-stu-id="dbceb-109">This static content might include HTML pages and other resources such as images and documents that are available to the client, either as part of an HTML page (such as inline images, style sheets, and client-side JavaScript files) or as separate downloads (such as PDF documents).</span></span>

<span data-ttu-id="dbceb-110">Sebbene i server Web vengano regolati per ottimizzare le richieste tramite l'esecuzione efficiente del codice di una pagina dinamica e la memorizzazione nella cache di output, devono ancora gestire le richieste per scaricare il contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="dbceb-110">Although web servers are well tuned to optimize requests through efficient dynamic page code execution and output caching, they still have to handle requests to download static content.</span></span> <span data-ttu-id="dbceb-111">Usa cicli di elaborazione che potrebbero spesso essere usati in modo migliore.</span><span class="sxs-lookup"><span data-stu-id="dbceb-111">This consumes processing cycles that could often be put to better use.</span></span>

## <a name="solution"></a><span data-ttu-id="dbceb-112">Soluzione</span><span class="sxs-lookup"><span data-stu-id="dbceb-112">Solution</span></span>

<span data-ttu-id="dbceb-113">Nella maggior parte degli ambienti di hosting cloud è possibile ridurre al minimo la necessità di istanze di calcolo, ad esempio usando un'istanza più piccola o meno istanze, individuando alcune risorse e pagine statiche dell'applicazione in un servizio di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="dbceb-113">In most cloud hosting environments it's possible to minimize the need for compute instances (for example, use a smaller instance or fewer instances), by locating some of an application’s resources and static pages in a storage service.</span></span> <span data-ttu-id="dbceb-114">Il costo dell'archiviazione ospitata su cloud è in genere molto inferiore rispetto a quello per le istanze di calcolo.</span><span class="sxs-lookup"><span data-stu-id="dbceb-114">The cost for cloud-hosted storage is typically much less than for compute instances.</span></span>

<span data-ttu-id="dbceb-115">Quando si ospitano alcune parti di un'applicazione in un servizio di archiviazione, le principali considerazioni sono correlate alla distribuzione dell'applicazione e alla protezione delle risorse che non devono essere disponibili agli utenti anonimi.</span><span class="sxs-lookup"><span data-stu-id="dbceb-115">When hosting some parts of an application in a storage service, the main considerations are related to deployment of the application and to securing resources that aren't intended to be available to anonymous users.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="dbceb-116">Considerazioni e problemi</span><span class="sxs-lookup"><span data-stu-id="dbceb-116">Issues and considerations</span></span>

<span data-ttu-id="dbceb-117">Prima di decidere come implementare questo modello, considerare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="dbceb-117">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="dbceb-118">Il servizio di archiviazione ospitato deve esporre un endpoint HTTP a cui gli utenti possono accedere per scaricare le risorse statiche.</span><span class="sxs-lookup"><span data-stu-id="dbceb-118">The hosted storage service must expose an HTTP endpoint that users can access to download the static resources.</span></span> <span data-ttu-id="dbceb-119">Anche alcuni servizi di archiviazione supportano HTTPS, pertanto è possibile ospitare le risorse nei servizi di archiviazione che richiedono l'SSL.</span><span class="sxs-lookup"><span data-stu-id="dbceb-119">Some storage services also support HTTPS, so it's possible to host resources in storage services that require SSL.</span></span>

- <span data-ttu-id="dbceb-120">Per prestazioni e disponibilità ottimali, usare una rete per la distribuzione di contenuti per memorizzare nella cache i contenuti del contenitore di archiviazione nei diversi datacenter di tutto il mondo.</span><span class="sxs-lookup"><span data-stu-id="dbceb-120">For maximum performance and availability, consider using a content delivery network (CDN) to cache the contents of the storage container in multiple datacenters around the world.</span></span> <span data-ttu-id="dbceb-121">Tuttavia, probabilmente sarà necessario pagare per l'uso della rete CDN.</span><span class="sxs-lookup"><span data-stu-id="dbceb-121">However, you'll likely have to pay for using the CDN.</span></span>

- <span data-ttu-id="dbceb-122">Gli account di archiviazione sono spesso sottoposti a replica geografica per impostazione predefinita per offrire resilienza in caso di eventi che potrebbero interessare un datacenter.</span><span class="sxs-lookup"><span data-stu-id="dbceb-122">Storage accounts are often geo-replicated by default to provide resiliency against events that might affect a datacenter.</span></span> <span data-ttu-id="dbceb-123">Ciò significa che l'indirizzo IP potrebbe cambiare, ma l'URL resta invariato.</span><span class="sxs-lookup"><span data-stu-id="dbceb-123">This means that the IP address might change, but the URL will remain the same.</span></span>

- <span data-ttu-id="dbceb-124">Quando alcuni contenuti si trovano in un account di archiviazione e altri in un'istanza di calcolo ospitata diventa più difficile distribuire un'applicazione e aggiornarla.</span><span class="sxs-lookup"><span data-stu-id="dbceb-124">When some content is located in a storage account and other content is in a hosted compute instance it becomes more challenging to deploy an application and to update it.</span></span> <span data-ttu-id="dbceb-125">Potrebbe essere necessario eseguire distribuzioni separate e controllare la versione dell'applicazione e del contenuto per gestirla in modo più semplice&mdash;soprattutto quando il contenuto statico include file script o componenti dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="dbceb-125">You might have to perform separate deployments, and version the application and content to manage it more easily&mdash;especially when the static content includes script files or UI components.</span></span> <span data-ttu-id="dbceb-126">Tuttavia, se è necessario aggiornare solo le risorse statiche, basta caricarle nell'account di archiviazione senza dover ridistribuire il pacchetto dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="dbceb-126">However, if only static resources have to be updated, they can simply be uploaded to the storage account without needing to redeploy the application package.</span></span>

- <span data-ttu-id="dbceb-127">I servizi di archiviazione potrebbero non supportare l'uso di nomi di dominio personalizzati.</span><span class="sxs-lookup"><span data-stu-id="dbceb-127">Storage services might not support the use of custom domain names.</span></span> <span data-ttu-id="dbceb-128">In questo caso è necessario specificare l'URL completo delle risorse nei collegamenti, in quanto si troveranno in un dominio diverso dal contenuto generato in modo dinamico che contiene i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="dbceb-128">In this case it's necessary to specify the full URL of the resources in links because they'll be in a different domain from the dynamically-generated content containing the links.</span></span>

- <span data-ttu-id="dbceb-129">I contenitori di archiviazione devono essere configurati per l'accesso in lettura pubblico, un'operazione fondamentale per garantire che non siano configurati per l'accesso pubblico in scrittura e impedire agli utenti di caricare il contenuto.</span><span class="sxs-lookup"><span data-stu-id="dbceb-129">The storage containers must be configured for public read access, but it's vital to ensure that they aren't configured for public write access to prevent users being able to upload content.</span></span> <span data-ttu-id="dbceb-130">È consigliabile usare un passepartout o un token per controllare l'accesso alle risorse che non devono essere disponibili in modo anonimo&mdash;vedere [Valet Key pattern](valet-key.md) (Modello passepartout) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="dbceb-130">Consider using a valet key or token to control access to resources that shouldn't be available anonymously&mdash;see the [Valet Key pattern](valet-key.md) for more information.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="dbceb-131">Quando usare questo modello</span><span class="sxs-lookup"><span data-stu-id="dbceb-131">When to use this pattern</span></span>

<span data-ttu-id="dbceb-132">Questo modello è utile per:</span><span class="sxs-lookup"><span data-stu-id="dbceb-132">This pattern is useful for:</span></span>

- <span data-ttu-id="dbceb-133">Ridurre al minimo il costo di hosting di applicazioni e siti Web che contengono risorse statiche.</span><span class="sxs-lookup"><span data-stu-id="dbceb-133">Minimizing the hosting cost for websites and applications that contain some static resources.</span></span>

- <span data-ttu-id="dbceb-134">Ridurre al minimo il costo di hosting per i siti Web costituiti solo da contenuto statico e risorse.</span><span class="sxs-lookup"><span data-stu-id="dbceb-134">Minimizing the hosting cost for websites that consist of only static content and resources.</span></span> <span data-ttu-id="dbceb-135">In base alle funzionalità del sistema di archiviazione del provider hosting, potrebbe essere possibile ospitare un sito Web statico completo in un account di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="dbceb-135">Depending on the capabilities of the hosting provider’s storage system, it might be possible to entirely host a fully static website in a storage account.</span></span>

- <span data-ttu-id="dbceb-136">Esposizione di risorse e contenuto statico per applicazioni in esecuzione in ambienti di hosting diversi o in server locali.</span><span class="sxs-lookup"><span data-stu-id="dbceb-136">Exposing static resources and content for applications running in other hosting environments or on-premises servers.</span></span>

- <span data-ttu-id="dbceb-137">Individuare il contenuto in più aree geografiche tramite una rete di distribuzione dei contenuti che memorizza nella cache il contenuto dell'account di archiviazione in più datacenter in tutto il mondo.</span><span class="sxs-lookup"><span data-stu-id="dbceb-137">Locating content in more than one geographical area using a content delivery network that caches the contents of the storage account in multiple datacenters around the world.</span></span>

- <span data-ttu-id="dbceb-138">Monitorare i costi e l'uso della larghezza di banda.</span><span class="sxs-lookup"><span data-stu-id="dbceb-138">Monitoring costs and bandwidth usage.</span></span> <span data-ttu-id="dbceb-139">L'uso di un account di archiviazione separato per una parte o per l'intero contenuto statico consente una più semplice separazione dei costi da quelli di hosting e di runtime.</span><span class="sxs-lookup"><span data-stu-id="dbceb-139">Using a separate storage account for some or all of the static content allows the costs to be more easily separated from hosting and runtime costs.</span></span>

<span data-ttu-id="dbceb-140">Questo modello può non essere utile nelle situazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="dbceb-140">This pattern might not be useful in the following situations:</span></span>

- <span data-ttu-id="dbceb-141">L'applicazione deve eseguire alcune operazioni di elaborazione sul contenuto statico prima di distribuirlo al client.</span><span class="sxs-lookup"><span data-stu-id="dbceb-141">The application needs to perform some processing on the static content before delivering it to the client.</span></span> <span data-ttu-id="dbceb-142">Ad esempio, potrebbe essere necessario aggiungere un timestamp a un documento.</span><span class="sxs-lookup"><span data-stu-id="dbceb-142">For example, it might be necessary to add a timestamp to a document.</span></span>

- <span data-ttu-id="dbceb-143">Il volume del contenuto statico è molto ridotto.</span><span class="sxs-lookup"><span data-stu-id="dbceb-143">The volume of static content is very small.</span></span> <span data-ttu-id="dbceb-144">Il sovraccarico per il recupero del contenuto da un archivio separato può annullare il vantaggio del costo della separazione dalla risorsa di calcolo.</span><span class="sxs-lookup"><span data-stu-id="dbceb-144">The overhead of retrieving this content from separate storage can outweigh the cost benefit of separating it out from the compute resource.</span></span>

## <a name="example"></a><span data-ttu-id="dbceb-145">Esempio</span><span class="sxs-lookup"><span data-stu-id="dbceb-145">Example</span></span>

<span data-ttu-id="dbceb-146">Il contenuto statico che si trova nell'archivio BLOB di Azure è accessibile direttamente da un Web browser.</span><span class="sxs-lookup"><span data-stu-id="dbceb-146">Static content located in Azure Blob storage can be accessed directly by a web browser.</span></span> <span data-ttu-id="dbceb-147">Azure offre un'interfaccia basata su HTTP per quanto riguarda l'archiviazione che può essere esposta pubblicamente ai client.</span><span class="sxs-lookup"><span data-stu-id="dbceb-147">Azure provides an HTTP-based interface over storage that can be publicly exposed to clients.</span></span> <span data-ttu-id="dbceb-148">Ad esempio, il contenuto in un contenitore di archivio BLOB di Azure verrà esposto tramite un URL nel formato seguente:</span><span class="sxs-lookup"><span data-stu-id="dbceb-148">For example, content in an Azure Blob storage container is exposed using a URL with the following form:</span></span>

`http://[ storage-account-name ].blob.core.windows.net/[ container-name ]/[ file-name ]`


<span data-ttu-id="dbceb-149">Quando si carica il contenuto è necessario creare uno o più contenitori BLOB per i file e i documenti.</span><span class="sxs-lookup"><span data-stu-id="dbceb-149">When uploading the content it's necessary to create one or more blob containers to hold the files and documents.</span></span> <span data-ttu-id="dbceb-150">Si noti che l'autorizzazione predefinita per un nuovo contenitore è Privata ed è necessario modificarla in Pubblica per consentire ai client di accedere al contenuto.</span><span class="sxs-lookup"><span data-stu-id="dbceb-150">Note that the default permission for a new container is Private, and you must change this to Public to allow clients to access the contents.</span></span> <span data-ttu-id="dbceb-151">Se è necessario proteggere il contenuto dall'accesso anonimo, è possibile implementare il [modello passepartout](valet-key.md) in modo che gli utenti debbano presentare un token valido per scaricare le risorse.</span><span class="sxs-lookup"><span data-stu-id="dbceb-151">If it's necessary to protect the content from anonymous access, you can implement the [Valet Key pattern](valet-key.md) so users must present a valid token to download the resources.</span></span>

> <span data-ttu-id="dbceb-152">[Blob Service Concepts](https://msdn.microsoft.com/library/azure/dd179376.aspx) (Concetti relativi al servizio BLOB) dispone di informazioni sull'archiviazione BLOB e i modi in cui è possibile accedervi e usarla.</span><span class="sxs-lookup"><span data-stu-id="dbceb-152">[Blob Service Concepts](https://msdn.microsoft.com/library/azure/dd179376.aspx) has information about blob storage, and the ways that you can access and use it.</span></span>

<span data-ttu-id="dbceb-153">I collegamenti di ogni pagina specificheranno l'URL della risorsa e il client potrà accedervi direttamente dal servizio di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="dbceb-153">The links in each page will specify the URL of the resource and the client will access it directly from the storage service.</span></span> <span data-ttu-id="dbceb-154">La figura illustra come recapitare le parti statiche di un'applicazione direttamente da un servizio di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="dbceb-154">The figure illustrates delivering static parts of an application directly from a storage service.</span></span>

![Figura 1 - Recapito di parti statiche di un'applicazione direttamente da un servizio di archiviazione](./_images/static-content-hosting-pattern.png)


<span data-ttu-id="dbceb-156">I collegamenti nelle pagine inviate al client devono specificare l'URL completo del contenitore BLOB e la risorsa.</span><span class="sxs-lookup"><span data-stu-id="dbceb-156">The links in the pages delivered to the client must specify the full URL of the blob container and resource.</span></span> <span data-ttu-id="dbceb-157">Ad esempio, una pagina che contiene un collegamento a un'immagine in un contenitore pubblico può contenere il seguente codice HTML.</span><span class="sxs-lookup"><span data-stu-id="dbceb-157">For example, a page that contains a link to an image in a public container might contain the following HTML.</span></span>

```html
<img src="http://mystorageaccount.blob.core.windows.net/myresources/image1.png"
     alt="My image" />
```

> <span data-ttu-id="dbceb-158">Se le risorse sono protette tramite un passepartout, ad esempio una firma di accesso condiviso di Azure, la firma deve essere inclusa negli URL dei collegamenti.</span><span class="sxs-lookup"><span data-stu-id="dbceb-158">If the resources are protected by using a valet key, such as an Azure shared access signature, this signature must be included in the URLs in the links.</span></span>

<span data-ttu-id="dbceb-159">In [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting) è disponibile una soluzione denominata StaticContentHosting che illustra l'uso dell'archiviazione esterna per le risorse statiche.</span><span class="sxs-lookup"><span data-stu-id="dbceb-159">A solution named StaticContentHosting that demonstrates using external storage for static resources is available from [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting).</span></span> <span data-ttu-id="dbceb-160">Il progetto StaticContentHosting.Cloud contiene i file di configurazione che specificano l'account di archiviazione e il contenitore che include il contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="dbceb-160">The StaticContentHosting.Cloud project contains configuration files that specify the storage account and container that holds the static content.</span></span>

```xml
<Setting name="StaticContent.StorageConnectionString"
         value="UseDevelopmentStorage=true" />
<Setting name="StaticContent.Container" value="static-content" />
```

<span data-ttu-id="dbceb-161">La classe `Settings` nel file Settings.cs del progetto StaticContentHosting.Web contiene i metodi per estrarre questi valori e generare un valore di stringa contenente l'URL del contenitore dell'account di archiviazione cloud.</span><span class="sxs-lookup"><span data-stu-id="dbceb-161">The `Settings` class in the file Settings.cs of the StaticContentHosting.Web project contains methods to extract these values and build a string value containing the cloud storage account container URL.</span></span>

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

<span data-ttu-id="dbceb-162">La classe `StaticContentUrlHtmlHelper` nel file StaticContentUrlHtmlHelper.cs espone un metodo denominato `StaticContentUrl` che genera un URL contenente il percorso all'account di archiviazione cloud, se l'URL passato inizia con il carattere del percorso radice ASP.NET (~).</span><span class="sxs-lookup"><span data-stu-id="dbceb-162">The `StaticContentUrlHtmlHelper` class in the file StaticContentUrlHtmlHelper.cs exposes a method named `StaticContentUrl` that generates a URL containing the path to the cloud storage account if the URL passed to it starts with the ASP.NET root path character (~).</span></span>

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

<span data-ttu-id="dbceb-163">Il file Index.cshtml nella cartella Visualizzazioni\Home contiene un elemento immagine che usa il metodo `StaticContentUrl` per creare l'URL relativo all'attributo `src`.</span><span class="sxs-lookup"><span data-stu-id="dbceb-163">The file Index.cshtml in the Views\Home folder contains an image element that uses the `StaticContentUrl` method to create the URL for its `src` attribute.</span></span>

```html
<img src="@Html.StaticContentUrl("~/media/orderedList1.png")" alt="Test Image" />
```

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="dbceb-164">Modelli correlati e informazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="dbceb-164">Related patterns and guidance</span></span>

- <span data-ttu-id="dbceb-165">Un esempio che illustra questo modello è disponibile su [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting).</span><span class="sxs-lookup"><span data-stu-id="dbceb-165">A sample that demonstrates this pattern is available on [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting).</span></span>
- <span data-ttu-id="dbceb-166">[Valet Key pattern](valet-key.md) (Modello di passepartout).</span><span class="sxs-lookup"><span data-stu-id="dbceb-166">[Valet Key pattern](valet-key.md).</span></span> <span data-ttu-id="dbceb-167">Se si suppone che le risorse di destinazione non debbano essere disponibili agli utenti anonimi è necessario implementare la sicurezza dell'archivio che contiene il contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="dbceb-167">If the target resources aren't supposed to be available to anonymous users it's necessary to implement security over the store that holds the static content.</span></span> <span data-ttu-id="dbceb-168">Descrive come usare un token o una chiave che offra ai client l'accesso diretto limitato a una risorsa o a un servizio specifico quali un servizio di archiviazione ospitato su cloud.</span><span class="sxs-lookup"><span data-stu-id="dbceb-168">Describes how to use a token or key that provides clients with restricted direct access to a specific resource or service such as a cloud-hosted storage service.</span></span>
- <span data-ttu-id="dbceb-169">[An efficient way of deploying a static web site on Azure](http://www.infosysblogs.com/microsoft/2010/06/an_efficient_way_of_deploying.html) (Un modo efficiente per distribuire un sito Web statico in Azure) sul blog di Infosys.</span><span class="sxs-lookup"><span data-stu-id="dbceb-169">[An efficient way of deploying a static web site on Azure](http://www.infosysblogs.com/microsoft/2010/06/an_efficient_way_of_deploying.html) on the Infosys blog.</span></span>
- <span data-ttu-id="dbceb-170">[Blob Service Concepts](https://msdn.microsoft.com/library/azure/dd179376.aspx) (Concetti relativi al servizio BLOB)</span><span class="sxs-lookup"><span data-stu-id="dbceb-170">[Blob Service Concepts](https://msdn.microsoft.com/library/azure/dd179376.aspx)</span></span>
