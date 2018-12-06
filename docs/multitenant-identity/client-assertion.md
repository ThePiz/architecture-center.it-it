---
title: Usare l'asserzione client per ottenere token di accesso da Azure AD
description: Come usare le asserzioni client per ottenere token di accesso da Azure AD.
author: MikeWasson
ms.date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: adfs
pnp.series.next: key-vault
ms.openlocfilehash: 58eed82c982fe1c6cba0f04b237d92d117a26fd4
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902270"
---
# <a name="use-client-assertion-to-get-access-tokens-from-azure-ad"></a>Usare l'asserzione client per ottenere token di accesso da Azure AD

[![GitHub](../_images/github.png) Codice di esempio][sample application]

## <a name="background"></a>Background
Quando si usa il flusso del codice di autorizzazione o il flusso ibrido in OpenID Connect, il client scambia un codice di autorizzazione con un token di accesso. Durante questo passaggio, il client deve autenticarsi al server.

![Segreto client](./images/client-secret.png)

Una modalità per l'autenticazione del client è l'uso del segreto client. Ecco come è configurata l'applicazione [Tailspin Surveys][Surveys] per impostazione predefinita.

Ecco una richiesta di esempio dal client al provider di identità per la richiesta di un token di accesso. Si noti il parametro `client_secret` .

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

Il segreto è una stringa, è quindi necessario assicurarsi di non perdere il valore. La procedura consigliata consiste nel mantenere il segreto client fuori dal controllo del codice sorgente. Quando si esegue la distribuzione in Azure, archiviare il segreto in un'[impostazione dell'app][configure-web-app].

Chiunque abbia accesso alla sottoscrizione di Azure, tuttavia, può visualizzare le impostazioni dell'app. È inoltre sempre possibile verificare i segreti nel controllo del codice sorgente, ad esempio negli script di distribuzione, condividerli tramite posta elettronica e così via.

Per una maggiore sicurezza, è possibile usare l' [asserzione client] anziché un segreto client. Con l'asserzione client, il client usa un certificato X.509 per dimostrare che la richiesta del token proviene dal client. Il certificato client è installato nel server Web. In genere, risulta più semplice limitare l'accesso al certificato, che assicurarsi che nessuno riveli inavvertitamente un segreto client. Per altre informazioni sulla configurazione di certificati in un'app Web, vedere [Using Certificates in Azure Websites Applications][using-certs-in-websites] (Uso di certificati in applicazioni di siti Web di Azure)

Ecco una richiesta di token mediante un'asserzione client:

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

Si noti che non viene più usato il parametro `client_secret` . Il parametro `client_assertion` , invece, contiene un token JWT firmato mediante il certificato client. Il parametro `client_assertion_type` specifica il tipo di asserzione, in questo caso un token JWT. Il server convalida il token JWT. Se il token JWT non è valido, la richiesta del token restituisce un errore.

> [!NOTE]
> I certificati X.509 non sono l'unica forma di asserzione client, ma sono quelli trattati in questo articolo perché supportati in Azure AD.
> 
> 

In fase di esecuzione, l'applicazione Web legge il certificato dall'archivio certificati. Il certificato deve essere installato nello stesso computer dell'app Web.

L'applicazione Surveys include una classe helper che crea un oggetto [ClientAssertionCertificate](/dotnet/api/microsoft.identitymodel.clients.activedirectory.clientassertioncertificate) che può essere passato al metodo [AuthenticationContext.AcquireTokenSilentAsync](/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokensilentasync) per acquisire un token da Azure AD.

```csharp
public class CertificateCredentialService : ICredentialService
{
    private Lazy<Task<AdalCredential>> _credential;

    public CertificateCredentialService(IOptions<ConfigurationOptions> options)
    {
        var aadOptions = options.Value?.AzureAd;
        _credential = new Lazy<Task<AdalCredential>>(() =>
        {
            X509Certificate2 cert = CertificateUtility.FindCertificateByThumbprint(
                aadOptions.Asymmetric.StoreName,
                aadOptions.Asymmetric.StoreLocation,
                aadOptions.Asymmetric.CertificateThumbprint,
                aadOptions.Asymmetric.ValidationRequired);
            string password = null;
            var certBytes = CertificateUtility.ExportCertificateWithPrivateKey(cert, out password);
            return Task.FromResult(new AdalCredential(new ClientAssertionCertificate(aadOptions.ClientId, new X509Certificate2(certBytes, password))));
        });
    }

    public async Task<AdalCredential> GetCredentialsAsync()
    {
        return await _credential.Value;
    }
}
```

Per informazioni sulla configurazione dell'asserzione client nell'applicazione Surveys, vedere [Usare Azure Key Vault per proteggere i segreti dell'applicazione][key vault].

[**Avanti**][key vault]

<!-- Links -->
[configure-web-app]: /azure/app-service-web/web-sites-configure/
[azure-management-portal]: https://portal.azure.com
[asserzione client]: https://tools.ietf.org/html/rfc7521
[key vault]: key-vault.md
[Setup-KeyVault]: https://github.com/mspnp/multitenant-saas-guidance/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/

[sample application]: https://github.com/mspnp/multitenant-saas-guidance
