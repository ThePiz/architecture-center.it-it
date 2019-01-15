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
# <a name="static-content-hosting-pattern"></a>Modello di hosting del contenuto statico

Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client. Questo può ridurre la necessità di istanze di calcolo potenzialmente dispendiose.

## <a name="context-and-problem"></a>Contesto e problema

Le applicazioni Web includono in genere alcuni elementi del contenuto statico. Il contenuto statico potrebbe includere pagine HTML e altre risorse, ad esempio immagini e documenti disponibili per il client, come parte di una pagina HTML, ad esempio immagini in linea con il testo, fogli di stile e file JavaScript lato client, o come download separati, quali documenti PDF.

Nonostante siano ottimizzati per il rendering dinamico e la memorizzazione dell'output nella cache, i server Web devono comunque gestire le richieste per il download del contenuto statico. Usa cicli di elaborazione che potrebbero spesso essere usati in modo migliore.

## <a name="solution"></a>Soluzione

Nella maggior parte degli ambienti di hosting cloud, è possibile includere alcune risorse e pagine statiche di un'applicazione in un servizio di archiviazione. Il servizio di archiviazione potrà gestire le richieste relative a tali risorse riducendo il carico sulle risorse di calcolo che gestiscono le altre richieste Web. Il costo dell'archiviazione ospitata su cloud è in genere molto inferiore rispetto a quello per le istanze di calcolo.

Quando si ospitano alcune parti di un'applicazione in un servizio di archiviazione, le principali considerazioni sono correlate alla distribuzione dell'applicazione e alla protezione delle risorse che non devono essere disponibili agli utenti anonimi.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo modello, considerare quanto segue:

- Il servizio di archiviazione ospitato deve esporre un endpoint HTTP a cui gli utenti possono accedere per scaricare le risorse statiche. Anche alcuni servizi di archiviazione supportano HTTPS, pertanto è possibile ospitare le risorse nei servizi di archiviazione che richiedono l'SSL.

- Per prestazioni e disponibilità ottimali, usare una rete per la distribuzione di contenuti per memorizzare nella cache i contenuti del contenitore di archiviazione nei diversi datacenter di tutto il mondo. Tuttavia, probabilmente sarà necessario pagare per l'uso della rete CDN.

- Gli account di archiviazione sono spesso sottoposti a replica geografica per impostazione predefinita per offrire resilienza in caso di eventi che potrebbero interessare un datacenter. Ciò significa che l'indirizzo IP potrebbe cambiare, ma l'URL resta invariato.

- Quando alcuni contenuti si trovano in un account di archiviazione e altri in un'istanza di calcolo ospitata, la distribuzione e l'aggiornamento dell'applicazione diventano più complessi. Potrebbe essere necessario eseguire distribuzioni separate e controllare la versione dell'applicazione e del contenuto per gestirla in modo più semplice&mdash;soprattutto quando il contenuto statico include file script o componenti dell'interfaccia utente. Tuttavia, se è necessario aggiornare solo le risorse statiche, basta caricarle nell'account di archiviazione senza dover ridistribuire il pacchetto dell'applicazione.

- I servizi di archiviazione potrebbero non supportare l'uso di nomi di dominio personalizzati. In questo caso è necessario specificare l'URL completo delle risorse nei collegamenti, in quanto si troveranno in un dominio diverso dal contenuto generato in modo dinamico che contiene i collegamenti.

- I contenitori di archiviazione devono essere configurati per l'accesso in lettura pubblico, un'operazione fondamentale per garantire che non siano configurati per l'accesso pubblico in scrittura e impedire agli utenti di caricare il contenuto.

- Valutare la possibilità di usare un passepartout o un token per controllare l'accesso alle risorse che non devono essere accessibili in modo anonimo. Per altre informazioni, vedere [Modello di passepartout](./valet-key.md).

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Questo modello è utile per:

- Ridurre al minimo il costo di hosting di applicazioni e siti Web che contengono risorse statiche.

- Ridurre al minimo il costo di hosting per i siti Web costituiti solo da contenuto statico e risorse. In base alle funzionalità del sistema di archiviazione del provider di hosting, potrebbe essere possibile ospitare completamente un sito Web interamente statico in un account di archiviazione.

- Esposizione di risorse e contenuto statico per applicazioni in esecuzione in ambienti di hosting diversi o in server locali.

- Individuare il contenuto in più aree geografiche tramite una rete di distribuzione dei contenuti che memorizza nella cache il contenuto dell'account di archiviazione in più datacenter in tutto il mondo.

- Monitorare i costi e l'uso della larghezza di banda. L'uso di un account di archiviazione separato per una parte o per l'intero contenuto statico consente una più semplice separazione dei costi da quelli di hosting e di runtime.

Questo modello può non essere utile nelle situazioni seguenti:

- L'applicazione deve eseguire alcune operazioni di elaborazione sul contenuto statico prima di distribuirlo al client. Ad esempio, potrebbe essere necessario aggiungere un timestamp a un documento.

- Il volume del contenuto statico è molto ridotto. Il sovraccarico per il recupero del contenuto da un archivio separato può annullare il vantaggio del costo della separazione dalla risorsa di calcolo.

## <a name="example"></a>Esempio

Archiviazione di Azure supporta la gestione di contenuto statico direttamente da un contenitore di archiviazione. I file vengono gestiti tramite richieste di accesso anonimo. Per impostazione predefinita, i file hanno un URL in un sottodominio di `core.windows.net`, ad esempio `https://contoso.z4.web.core.windows.net/image.png`. È possibile configurare un nome di dominio personalizzato e usare Rete CDN di Azure per accedere ai file tramite HTTPS. Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).

![Distribuzione delle parti statiche di un'applicazione direttamente da un servizio di archiviazione](./_images/static-content-hosting-pattern.png)

L'hosting di siti Web statici rende i file disponibili per l'accesso anonimo. Se è necessario controllare quali utenti possono accedere ai file, è possibile archiviare i file in un archivio BLOB di Azure e quindi generare [firme di accesso condiviso](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) per limitare l'accesso.

I collegamenti nelle pagine distribuite al client devono specificare l'URL completo della risorsa. Se la risorsa è protetta con un passepartout, ad esempio una firma di accesso condiviso, la firma deve essere inclusa nell'URL.

In [GitHub][sample-app] è disponibile un'applicazione di esempio che illustra l'uso di un archivio esterno per le risorse statiche. L'esempio usa file di configurazione che specificano l'account di archiviazione e il contenitore in cui si trova il contenuto statico.

```xml
<Setting name="StaticContent.StorageConnectionString"
         value="UseDevelopmentStorage=true" />
<Setting name="StaticContent.Container" value="static-content" />
```

La classe `Settings` nel file Settings.cs del progetto StaticContentHosting.Web contiene i metodi per estrarre questi valori e generare un valore di stringa contenente l'URL del contenitore dell'account di archiviazione cloud.

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

La classe `StaticContentUrlHtmlHelper` nel file StaticContentUrlHtmlHelper.cs espone un metodo denominato `StaticContentUrl` che genera un URL contenente il percorso all'account di archiviazione cloud, se l'URL passato inizia con il carattere del percorso radice ASP.NET (~).

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

Il file Index.cshtml nella cartella Visualizzazioni\Home contiene un elemento immagine che usa il metodo `StaticContentUrl` per creare l'URL relativo all'attributo `src`.

```html
<img src="@Html.StaticContentUrl("~/media/orderedList1.png")" alt="Test Image" />
```

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

- [Esempio di hosting di contenuto statico][sample-app]: applicazione di esempio che illustra questo modello.
- [Modello di passepartout](./valet-key.md): se non è previsto che le risorse di destinazione siano disponibili per utenti anonimi, usare questo modello per limitare l'accesso diretto.
- [Applicazione Web serverless in Azure](../reference-architectures/serverless/web-app.md): architettura di riferimento che usa l'hosting di siti Web statici con Funzioni di Azure per implementare un'app Web serverless.

[sample-app]: https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting
