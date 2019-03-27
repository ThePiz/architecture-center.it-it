---
title: Usare Key Vault per proteggere i segreti dell'applicazione
description: Come usare il servizio Key Vault per archiviare i segreti dell'applicazione.
author: MikeWasson
ms.date: 07/21/2017
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: client-assertion
ms.openlocfilehash: 6aa8d33da0b2fd41fdc037bac28bca9f7ff09907
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249416"
---
# <a name="use-azure-key-vault-to-protect-application-secrets"></a>Usare Azure Key Vault per proteggere i segreti dell'applicazione

[![GitHub](../_images/github.png) Codice di esempio][sample application]

Alcune impostazioni dell'applicazione sono sensibili e devono essere protette, ad esempio:

* Stringhe di connessione del database
* Password
* Chiavi crittografiche

Per una sicurezza ottimale, è consigliabile non archiviare mai questi segreti nel controllo del codice sorgente. In caso contrario, potranno essere persi facilmente, anche se il repository del codice sorgente è privato. Il problema non è costituito soltanto dalla necessità di non diffondere i segreti al pubblico generale. Nei progetti di grandi dimensioni è possibile che si voglia limitare l'accesso ai segreti di produzione solo ad alcuni sviluppatori e operatori. Le impostazioni per gli ambienti di test o di sviluppo sono diverse.

Un'opzione più sicura consiste nell'archiviare questi segreti in [Azure Key Vault][KeyVault]. L'insieme di credenziali delle chiavi è un servizio ospitato su cloud per la gestione delle chiavi crittografiche e di altri segreti. Questo articolo illustra come usare l'insieme di credenziali delle chiavi per archiviare le impostazioni di configurazione per l'app.

Nell'applicazione [Tailspin Surveys][Surveys] le impostazioni seguenti sono segrete:

* Stringa di connessione del database.
* Stringa di connessione Redis.
* Segreto client per l'applicazione Web.

L'applicazione Surveys carica le impostazioni di configurazione dalle posizioni seguenti:

* File appsettings.json
* [Archivio dei segreti utente][user-secrets] (solo per l'ambiente di sviluppo, per il test)
* Ambiente di hosting (impostazioni delle app nelle app Web di Azure)
* Key Vault (se abilitato)

Ogni impostazione esegue l'override della precedente, quindi eventuali impostazioni archiviate nell'insieme di credenziali delle chiavi hanno precedenza sulle altre.

> [!NOTE]
> Per impostazione predefinita, il provider di configurazione dell'insieme di credenziali delle chiavi è disabilitato. Non è necessario per l'esecuzione locale dell'applicazione. Occorre abilitarlo in un ambiente di produzione.

All'avvio l'applicazione legge le impostazioni da ogni provider di configurazione registrato e le usa per popolare un oggetto opzioni fortemente tipizzato. Per altre informazioni, vedere [Uso di opzioni e oggetti di configurazione][options].

## <a name="setting-up-key-vault-in-the-surveys-app"></a>Configurazione dell'insieme di credenziali delle chiavi nell'app Surveys

Prerequisiti:

* Installare i [cmdlet di Azure Resource Manager][azure-rm-cmdlets].
* Configurare l'applicazione Surveys come descritto in [Eseguire l'applicazione Surveys][readme].

Procedure generali:

1. Configurare un utente amministratore nel tenant.
2. Configurare un certificato client.
3. Creare un insieme di credenziali delle chiavi.
4. Aggiungere le impostazioni di configurazione all'insieme di credenziali delle chiavi.
5. Rimuovere il commento dal codice che abilita l'insieme di credenziali delle chiavi.
6. Aggiornare i segreti utente dell'applicazione.

### <a name="set-up-an-admin-user"></a>Configurare un utente amministratore

> [!NOTE]
> Per creare un insieme di credenziali delle chiavi, è necessario usare un account che può gestire la sottoscrizione di Azure. Eventuali applicazioni autorizzate a leggere dall'insieme di credenziali delle chiavi, inoltre, devono essere registrate nello stesso tenant in cui si trova l'account.

In questo passaggio si verifica che sia possibile creare un insieme di credenziali delle chiavi quando si accede come utente dal tenant in cui è registrata l'app Surveys.

Creare un utente amministratore nel tenant di Azure AD in cui è registrata l'applicazione Surveys.

1. Accedere al [portale di Azure][azure-portal].
2. Selezionare il tenant di Azure AD in cui è registrata l'applicazione.
3. Fare clic su **Altri servizi** > **Sicurezza e identità** > **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
4. Nella parte superiore del portale fare clic su **Nuovo utente**.
5. Completare i campi e assegnare all'utente il ruolo della directory **Amministratore globale**.
6. Fare clic su **Create**(Crea).

![Utente amministratore globale](./images/running-the-app/global-admin-user.png)

Impostare ora questo utente come proprietario della sottoscrizione.

1. Nel menu Hub selezionare **Sottoscrizioni**.

    ![Screenshot del menu Hub nel portale di Azure](./images/running-the-app/subscriptions.png)

2. Selezionare la sottoscrizione cui deve accedere l'amministratore.
3. Nel pannello della sottoscrizione selezionare **Controllo di accesso (IAM)**.
4. Fare clic su **Aggiungi**.
5. In **Ruolo** selezionare **Proprietario**.
6. Digitare l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario.
7. Selezionare l'utente e fare clic su **Salva**.

### <a name="set-up-a-client-certificate"></a>Configurare un certificato client

1. Eseguire lo script di PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] nel modo seguente:

    ```powershell
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    Per il parametro `Subject` immettere qualsiasi nome, ad esempio "surveysapp". Lo script genera un certificato autofirmato e lo archivia nell'archivio certificati "Utente corrente/Personale". L'output dallo script è un frammento JSON. Copiare questo valore.

2. Nel [portale di Azure][azure-portal] passare alla directory in cui è registrata l'applicazione Surveys selezionando l'account nell'angolo in alto a destra del portale.

3. Passare ad **Azure Active Directory** > **Registrazioni per l'app** > Surveys

4. Fare clic su **Manifesto** e quindi su **Modifica**.

5. Incollare l'output dello script nella proprietà `keyCredentials` . Dovrebbe essere simile a quello riportato di seguito:

    ```json
    "keyCredentials": [
        {
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "keyId": "29d4f7db-0539-455e-b708-....",
        "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
        "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
        }
    ],
    ```

6. Fare clic su **Save**.

7. Ripetere i passaggi da 3 a 6 per aggiungere al manifesto dell'applicazione lo stesso frammento JSON dell'API Web (Surveys.WebAPI).

8. Dalla finestra di PowerShell eseguire il comando seguente per ottenere l'identificazione personale del certificato.

    ```powershell
    certutil -store -user my [subject]
    ```

    Per `[subject]`, usare il valore specificato per Subject nello script di PowerShell. L'identificazione personale è elencata in "Cert Hash(sha1)". Copiare questo valore. L'identificazione personale verrà usata in seguito.

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

1. Eseguire lo script di PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] nel modo seguente:

    ```powershell
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    Quando vengono richieste le credenziali, accedere come l'utente di Azure AD creato in precedenza. Lo script crea un nuovo gruppo di risorse e un nuovo insieme di credenziali delle chiavi in tale gruppo di risorse.

2. Eseguire di nuovo Setup-KeyVault.ps1 come illustrato di seguito:

    ```powershell
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<Surveys app id>>", "<<Surveys.WebAPI app ID>>")
    ```

    Impostare i valori di parametro seguenti:

       * key vault name: nome assegnato all'insieme di credenziali delle chiavi nel passaggio precedente.
       * Surveys app ID = ID applicazione per l'applicazione Web Surveys.
       * Surveys.WebApi app ID = ID applicazione per l'applicazione Surveys.WebAPI.

    Esempio:

    ```powershell
     .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    Lo script autorizza l'app Web e l'API Web a recuperare segreti dall'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Introduzione ad Azure Key Vault](/azure/key-vault/key-vault-get-started/).

### <a name="add-configuration-settings-to-your-key-vault"></a>Aggiungere le impostazioni di configurazione all'insieme di credenziali delle chiavi

1. Eseguire Setup-KeyVault.ps1 come illustrato di seguito:

    ```powershell
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName Redis--Configuration -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true"
    ```
    dove

   * key vault name: nome assegnato all'insieme di credenziali delle chiavi nel passaggio precedente.
   * Redis DNS name: nome DNS dell'istanza della cache Redis.
   * Redis access key: chiave di accesso per l'istanza della cache Redis.

2. A questo punto è consigliabile verificare se i segreti sono stati archiviati correttamente nell'insieme di credenziali delle chiavi. Eseguire il seguente comando PowerShell:

    ```powershell
    Get-AzureKeyVaultSecret <<key vault name>> Redis--Configuration | Select-Object *
    ```

3. Eseguire di nuovo Setup-KeyVault.ps1 per aggiungere la stringa di connessione del database:

    ```powershell
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName Data--SurveysConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    dove `<<DB connection string>>` è il valore della stringa di connessione del database.

    Per il test con il database locale, copiare la stringa di connessione dal file Tailspin.Surveys.Web/appsettings.json. In questo caso, assicurarsi di modificare la doppia barra rovesciata ("\\\\") in barra rovesciata singola. La doppia barra rovesciata è un carattere di escape nel file JSON.

    Esempio:

    ```powershell
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName Data--SurveysConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true"
    ```

### <a name="uncomment-the-code-that-enables-key-vault"></a>Rimuovere i commenti dal codice che abilita l'insieme di credenziali delle chiavi

1. Aprire la soluzione Tailspin.Surveys.
2. In Tailspin.Surveys.Web/Startup.cs individuare il blocco di codice seguente e rimuovere i commenti.

    ```csharp
    //var config = builder.Build();
    //builder.AddAzureKeyVault(
    //    $"https://{config["KeyVault:Name"]}.vault.azure.net/",
    //    config["AzureAd:ClientId"],
    //    config["AzureAd:ClientSecret"]);
    ```
3. In Tailspin.Surveys.Web/Startup.cs individuare il codice per la registrazione di `ICredentialService`. Rimuovere il commento dalla riga che usa `CertificateCredentialService` e impostare come commento la riga che usa `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    Questa modifica consente all'app Web di usare l'[asserzione client][client-assertion] per ottenere i token di accesso OAuth. Con l'asserzione client, non è necessario un segreto client OAuth. In alternativa, è possibile archiviare il segreto client nell'insieme di credenziali delle chiavi. Tuttavia, poiché l'insieme di credenziali delle chiavi e l'asserzione client usano entrambi un certificato client, se si abilita l'insieme di credenziali delle chiavi, è consigliabile abilitare anche l'asserzione client.

### <a name="update-the-user-secrets"></a>Aggiornare i segreti utente

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Tailspin.Surveys.Web e scegliere **Gestisci segreti utente**. Nel file secrets.json eliminare il codice JSON esistente e incollare il codice seguente:

```json
{
  "AzureAd": {
    "ClientId": "[Surveys web app client ID]",
    "ClientSecret": "[Surveys web app client secret]",
    "PostLogoutRedirectUri": "https://localhost:44300/",
    "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Sostituire le voci racchiuse tra [parentesi quadre] con i valori corretti.

* `AzureAd:ClientId`: ID client dell'app Surveys.
* `AzureAd:ClientSecret`: chiave generata durante la registrazione dell'applicazione Surveys in Azure AD.
* `AzureAd:WebApiResourceId`: URI ID app specificato durante la creazione dell'applicazione Surveys.WebAPI in Azure AD.
* `Asymmetric:CertificateThumbprint`: identificazione personale del certificato ottenuta in precedenza, durante la creazione del certificato client.
* `KeyVault:Name`: nome dell'insieme di credenziali delle chiavi.

> [!NOTE]
> `Asymmetric:ValidationRequired` è false perché il certificato creato in precedenza non è stato firmato dall'autorità di certificazione radice. Nell'ambiente di produzione usare un certificato firmato da un'autorità di certificazione radice e impostare `ValidationRequired` su true.

Salvare il file secrets.json aggiornato.

In Esplora soluzioni fare quindi clic con il pulsante destro del mouse sul progetto Tailspin.Surveys.WebApi e scegliere **Gestisci segreti utente**. Eliminare il codice JSON esistente e incollare il codice seguente:

```json
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Sostituire le voci tra [parentesi quadre] e salvare il file secrets.json.

> [!NOTE]
> Per l'API Web assicurarsi di usare l'ID client per l'applicazione Surveys.WebAPI, non l'applicazione Surveys.

[**Avanti**][adfs]

<!-- links -->

[adfs]: ./adfs.md
[authorize-app]: /azure/key-vault/key-vault-get-started//#authorize
[azure-portal]: https://portal.azure.com
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: client-assertion.md
[configuration]: /aspnet/core/fundamentals/configuration
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[key-tags]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: /aspnet/core/fundamentals/configuration#using-options-and-configuration-objects
[readme]: ./run-the-app.md
[Setup-KeyVault]: https://github.com/mspnp/multitenant-saas-guidance/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: tailspin.md
[user-secrets]: /aspnet/core/security/app-secrets
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
