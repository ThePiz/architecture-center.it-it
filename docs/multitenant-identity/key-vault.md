---
title: Usare Key Vault per proteggere i segreti dell'applicazione
description: Come usare il servizio Key Vault per archiviare i segreti dell'applicazione
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: client-assertion
ms.openlocfilehash: d49129a38d0413f6006095f03b817885e1ce6c92
ms.sourcegitcommit: f665226cec96ec818ca06ac6c2d83edb23c9f29c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31012519"
---
# <a name="use-azure-key-vault-to-protect-application-secrets"></a><span data-ttu-id="a35e7-103">Usare Azure Key Vault per proteggere i segreti dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="a35e7-103">Use Azure Key Vault to protect application secrets</span></span>

<span data-ttu-id="a35e7-104">[![GitHub](../_images/github.png) Codice di esempio][sample application]</span><span class="sxs-lookup"><span data-stu-id="a35e7-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="a35e7-105">Alcune impostazioni dell'applicazione sono sensibili e devono essere protette, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a35e7-105">It's common to have application settings that are sensitive and must be protected, such as:</span></span>

* <span data-ttu-id="a35e7-106">Stringhe di connessione del database</span><span class="sxs-lookup"><span data-stu-id="a35e7-106">Database connection strings</span></span>
* <span data-ttu-id="a35e7-107">Password</span><span class="sxs-lookup"><span data-stu-id="a35e7-107">Passwords</span></span>
* <span data-ttu-id="a35e7-108">Chiavi crittografiche</span><span class="sxs-lookup"><span data-stu-id="a35e7-108">Cryptographic keys</span></span>

<span data-ttu-id="a35e7-109">Per una sicurezza ottimale, è consigliabile non archiviare mai questi segreti nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="a35e7-109">As a security best practice, you should never store these secrets in source control.</span></span> <span data-ttu-id="a35e7-110">In caso contrario, potranno essere persi facilmente, anche se il repository del codice sorgente è privato.</span><span class="sxs-lookup"><span data-stu-id="a35e7-110">It's too easy for them to leak &mdash; even if your source code repository is private.</span></span> <span data-ttu-id="a35e7-111">Il problema non è costituito soltanto dalla necessità di non diffondere i segreti al pubblico generale.</span><span class="sxs-lookup"><span data-stu-id="a35e7-111">And it's not just about keeping secrets from the general public.</span></span> <span data-ttu-id="a35e7-112">Nei progetti di grandi dimensioni è possibile che si voglia limitare l'accesso ai segreti di produzione solo ad alcuni sviluppatori e operatori.</span><span class="sxs-lookup"><span data-stu-id="a35e7-112">On larger projects, you might want to restrict which developers and operators can access the production secrets.</span></span> <span data-ttu-id="a35e7-113">Le impostazioni per gli ambienti di test o di sviluppo sono diverse.</span><span class="sxs-lookup"><span data-stu-id="a35e7-113">(Settings for test or development environments are different.)</span></span>

<span data-ttu-id="a35e7-114">Un'opzione più sicura consiste nell'archiviare questi segreti in [Azure Key Vault][KeyVault].</span><span class="sxs-lookup"><span data-stu-id="a35e7-114">A more secure option is to store these secrets in [Azure Key Vault][KeyVault].</span></span> <span data-ttu-id="a35e7-115">L'insieme di credenziali delle chiavi è un servizio ospitato su cloud per la gestione delle chiavi crittografiche e di altri segreti.</span><span class="sxs-lookup"><span data-stu-id="a35e7-115">Key Vault is a cloud-hosted service for managing cryptographic keys and other secrets.</span></span> <span data-ttu-id="a35e7-116">Questo articolo illustra come usare l'insieme di credenziali delle chiavi per archiviare le impostazioni di configurazione per l'app.</span><span class="sxs-lookup"><span data-stu-id="a35e7-116">This article shows how to use Key Vault to store configuration settings for your app.</span></span>

<span data-ttu-id="a35e7-117">Nell'applicazione [Tailspin Surveys][Surveys] le impostazioni seguenti sono segrete:</span><span class="sxs-lookup"><span data-stu-id="a35e7-117">In the [Tailspin Surveys][Surveys] application, the following settings are secret:</span></span>

* <span data-ttu-id="a35e7-118">Stringa di connessione del database.</span><span class="sxs-lookup"><span data-stu-id="a35e7-118">The database connection string.</span></span>
* <span data-ttu-id="a35e7-119">Stringa di connessione Redis.</span><span class="sxs-lookup"><span data-stu-id="a35e7-119">The Redis connection string.</span></span>
* <span data-ttu-id="a35e7-120">Segreto client per l'applicazione Web.</span><span class="sxs-lookup"><span data-stu-id="a35e7-120">The client secret for the web application.</span></span>

<span data-ttu-id="a35e7-121">L'applicazione Surveys carica le impostazioni di configurazione dalle posizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a35e7-121">The Surveys application loads configuration settings from the following places:</span></span>

* <span data-ttu-id="a35e7-122">File appsettings.json</span><span class="sxs-lookup"><span data-stu-id="a35e7-122">The appsettings.json file</span></span>
* <span data-ttu-id="a35e7-123">[Archivio dei segreti utente][user-secrets] (solo per l'ambiente di sviluppo, per il test)</span><span class="sxs-lookup"><span data-stu-id="a35e7-123">The [user secrets store][user-secrets] (development environment only; for testing)</span></span>
* <span data-ttu-id="a35e7-124">Ambiente di hosting (impostazioni delle app nelle app Web di Azure)</span><span class="sxs-lookup"><span data-stu-id="a35e7-124">The hosting environment (app settings in Azure web apps)</span></span>
* <span data-ttu-id="a35e7-125">Key Vault (se abilitato)</span><span class="sxs-lookup"><span data-stu-id="a35e7-125">Key Vault (when enabled)</span></span>

<span data-ttu-id="a35e7-126">Ogni impostazione esegue l'override della precedente, quindi eventuali impostazioni archiviate nell'insieme di credenziali delle chiavi hanno precedenza sulle altre.</span><span class="sxs-lookup"><span data-stu-id="a35e7-126">Each of these overrides the previous one, so any settings stored in Key Vault take precedence.</span></span>

> [!NOTE]
> <span data-ttu-id="a35e7-127">Per impostazione predefinita, il provider di configurazione dell'insieme di credenziali delle chiavi è disabilitato.</span><span class="sxs-lookup"><span data-stu-id="a35e7-127">By default, the Key Vault configuration provider is disabled.</span></span> <span data-ttu-id="a35e7-128">Non è necessario per l'esecuzione locale dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="a35e7-128">It's not needed for running the application locally.</span></span> <span data-ttu-id="a35e7-129">Occorre abilitarlo in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="a35e7-129">You would enable it in a production deployment.</span></span>

<span data-ttu-id="a35e7-130">All'avvio l'applicazione legge le impostazioni da ogni provider di configurazione registrato e le usa per popolare un oggetto opzioni fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="a35e7-130">At startup, the application reads settings from every registered configuration provider, and uses them to populate a strongly typed options object.</span></span> <span data-ttu-id="a35e7-131">Per altre informazioni, vedere [Uso di opzioni e oggetti di configurazione][options].</span><span class="sxs-lookup"><span data-stu-id="a35e7-131">For more information, see [Using Options and configuration objects][options].</span></span>

## <a name="setting-up-key-vault-in-the-surveys-app"></a><span data-ttu-id="a35e7-132">Configurazione dell'insieme di credenziali delle chiavi nell'app Surveys</span><span class="sxs-lookup"><span data-stu-id="a35e7-132">Setting up Key Vault in the Surveys app</span></span>
<span data-ttu-id="a35e7-133">Prerequisiti:</span><span class="sxs-lookup"><span data-stu-id="a35e7-133">Prerequisites:</span></span>

* <span data-ttu-id="a35e7-134">Installare i [cmdlet di Azure Resource Manager][azure-rm-cmdlets].</span><span class="sxs-lookup"><span data-stu-id="a35e7-134">Install the [Azure Resource Manager Cmdlets][azure-rm-cmdlets].</span></span>
* <span data-ttu-id="a35e7-135">Configurare l'applicazione Surveys come descritto in [Eseguire l'applicazione Surveys][readme].</span><span class="sxs-lookup"><span data-stu-id="a35e7-135">Configure the Surveys application as described in [Run the Surveys application][readme].</span></span>

<span data-ttu-id="a35e7-136">Procedure generali:</span><span class="sxs-lookup"><span data-stu-id="a35e7-136">High-level steps:</span></span>

1. <span data-ttu-id="a35e7-137">Configurare un utente amministratore nel tenant.</span><span class="sxs-lookup"><span data-stu-id="a35e7-137">Set up an admin user in the tenant.</span></span>
2. <span data-ttu-id="a35e7-138">Configurare un certificato client.</span><span class="sxs-lookup"><span data-stu-id="a35e7-138">Set up a client certificate.</span></span>
3. <span data-ttu-id="a35e7-139">Creare un insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="a35e7-139">Create a key vault.</span></span>
4. <span data-ttu-id="a35e7-140">Aggiungere le impostazioni di configurazione all'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="a35e7-140">Add configuration settings to your key vault.</span></span>
5. <span data-ttu-id="a35e7-141">Rimuovere il commento dal codice che abilita l'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="a35e7-141">Uncomment the code that enables key vault.</span></span>
6. <span data-ttu-id="a35e7-142">Aggiornare i segreti utente dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="a35e7-142">Update the application's user secrets.</span></span>

### <a name="set-up-an-admin-user"></a><span data-ttu-id="a35e7-143">Configurare un utente amministratore</span><span class="sxs-lookup"><span data-stu-id="a35e7-143">Set up an admin user</span></span>
> [!NOTE]
> <span data-ttu-id="a35e7-144">Per creare un insieme di credenziali delle chiavi, è necessario usare un account che può gestire la sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="a35e7-144">To create a key vault, you must use an account which can manage your Azure subscription.</span></span> <span data-ttu-id="a35e7-145">Eventuali applicazioni autorizzate a leggere dall'insieme di credenziali delle chiavi, inoltre, devono essere registrate nello stesso tenant in cui si trova l'account.</span><span class="sxs-lookup"><span data-stu-id="a35e7-145">Also, any application that you authorize to read from the key vault must be registered in the same tenant as that account.</span></span>
> 
> 

<span data-ttu-id="a35e7-146">In questo passaggio si verifica che sia possibile creare un insieme di credenziali delle chiavi quando si accede come utente dal tenant in cui è registrata l'app Surveys.</span><span class="sxs-lookup"><span data-stu-id="a35e7-146">In this step, you will make sure that you can create a key vault while signed in as a user from the tenant where the Surveys app is registered.</span></span>

<span data-ttu-id="a35e7-147">Creare un utente amministratore nel tenant di Azure AD in cui è registrata l'applicazione Surveys.</span><span class="sxs-lookup"><span data-stu-id="a35e7-147">Create an administrator user within the Azure AD tenant where the Surveys application is registered.</span></span>

1. <span data-ttu-id="a35e7-148">Accedere al [portale di Azure][azure-portal].</span><span class="sxs-lookup"><span data-stu-id="a35e7-148">Log into the [Azure portal][azure-portal].</span></span>
2. <span data-ttu-id="a35e7-149">Selezionare il tenant di Azure AD in cui è registrata l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="a35e7-149">Select the Azure AD tenant where your application is registered.</span></span>
3. <span data-ttu-id="a35e7-150">Fare clic su **Altri servizi** > **Sicurezza e identità** > **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-150">Click **More service** > **SECURITY + IDENTITY** > **Azure Active Directory** > **User and groups** > **All users**.</span></span>
4. <span data-ttu-id="a35e7-151">Nella parte superiore del portale fare clic su **Nuovo utente**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-151">At the top of the portal, click **New user**.</span></span>
5. <span data-ttu-id="a35e7-152">Completare i campi e assegnare all'utente il ruolo della directory **Amministratore globale**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-152">Fill in the fields and assign the user to the **Global administrator** directory role.</span></span>
6. <span data-ttu-id="a35e7-153">Fare clic su **Crea**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-153">Click **Create**.</span></span>

![Utente amministratore globale](./images/running-the-app/global-admin-user.png)

<span data-ttu-id="a35e7-155">Impostare ora questo utente come proprietario della sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="a35e7-155">Now assign this user as the subscription owner.</span></span>

1. <span data-ttu-id="a35e7-156">Nel menu Hub selezionare **Sottoscrizioni**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-156">On the Hub menu, select **Subscriptions**.</span></span>

    ![](./images/running-the-app/subscriptions.png)

2. <span data-ttu-id="a35e7-157">Selezionare la sottoscrizione cui deve accedere l'amministratore.</span><span class="sxs-lookup"><span data-stu-id="a35e7-157">Select the subscription that you want the administrator to access.</span></span>
3. <span data-ttu-id="a35e7-158">Nel pannello della sottoscrizione selezionare **Controllo di accesso (IAM)**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-158">In the subscription blade, select **Access control (IAM)**.</span></span>
4. <span data-ttu-id="a35e7-159">Fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-159">Click **Add**.</span></span>
4. <span data-ttu-id="a35e7-160">In **Ruolo** selezionare **Proprietario**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-160">Under **Role**, select **Owner**.</span></span>
5. <span data-ttu-id="a35e7-161">Digitare l'indirizzo e-mail dell'utente che si vuole aggiungere come proprietario.</span><span class="sxs-lookup"><span data-stu-id="a35e7-161">Type the email address of the user you want to add as owner.</span></span>
6. <span data-ttu-id="a35e7-162">Selezionare l'utente e fare clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-162">Select the user and click **Save**.</span></span>

### <a name="set-up-a-client-certificate"></a><span data-ttu-id="a35e7-163">Configurare un certificato client</span><span class="sxs-lookup"><span data-stu-id="a35e7-163">Set up a client certificate</span></span>
1. <span data-ttu-id="a35e7-164">Eseguire lo script di PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="a35e7-164">Run the PowerShell script [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] as follows:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    <span data-ttu-id="a35e7-165">Per il parametro `Subject` immettere qualsiasi nome, ad esempio "surveysapp".</span><span class="sxs-lookup"><span data-stu-id="a35e7-165">For the `Subject` parameter, enter any name, such as "surveysapp".</span></span> <span data-ttu-id="a35e7-166">Lo script genera un certificato autofirmato e lo archivia nell'archivio certificati "Utente corrente/Personale".</span><span class="sxs-lookup"><span data-stu-id="a35e7-166">The script generates a self-signed certificate and stores it in the "Current User/Personal" certificate store.</span></span> <span data-ttu-id="a35e7-167">L'output dallo script è un frammento JSON.</span><span class="sxs-lookup"><span data-stu-id="a35e7-167">The output from the script is a JSON fragment.</span></span> <span data-ttu-id="a35e7-168">Copiare questo valore.</span><span class="sxs-lookup"><span data-stu-id="a35e7-168">Copy this value.</span></span>

2. <span data-ttu-id="a35e7-169">Nel [portale di Azure][azure-portal] passare alla directory in cui è registrata l'applicazione Surveys selezionando l'account nell'angolo in alto a destra del portale.</span><span class="sxs-lookup"><span data-stu-id="a35e7-169">In the [Azure portal][azure-portal], switch to the directory where the Surveys application is registered, by selecting your account in the top right corner of the portal.</span></span>

3. <span data-ttu-id="a35e7-170">Passare ad **Azure Active Directory** > **Registrazioni per l'app** > Surveys</span><span class="sxs-lookup"><span data-stu-id="a35e7-170">Select **Azure Active Directory** > **App Registrations** > Surveys</span></span>

4.  <span data-ttu-id="a35e7-171">Fare clic su **Manifesto** e quindi su **Modifica**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-171">Click **Manifest** and then **Edit**.</span></span>

5.  <span data-ttu-id="a35e7-172">Incollare l'output dello script nella proprietà `keyCredentials` .</span><span class="sxs-lookup"><span data-stu-id="a35e7-172">Paste the output from the script into the `keyCredentials` property.</span></span> <span data-ttu-id="a35e7-173">Dovrebbe essere simile a quello riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a35e7-173">It should look similar to the following:</span></span>
        
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

6. <span data-ttu-id="a35e7-174">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-174">Click **Save**.</span></span>  

7. <span data-ttu-id="a35e7-175">Ripetere i passaggi da 3 a 6 per aggiungere al manifesto dell'applicazione lo stesso frammento JSON dell'API Web (Surveys.WebAPI).</span><span class="sxs-lookup"><span data-stu-id="a35e7-175">Repeat steps 3-6 to add the same JSON fragment to the application manifest of the web API (Surveys.WebAPI).</span></span>

8. <span data-ttu-id="a35e7-176">Dalla finestra di PowerShell eseguire il comando seguente per ottenere l'identificazione personale del certificato.</span><span class="sxs-lookup"><span data-stu-id="a35e7-176">From the PowerShell window, run the following command to get the thumbprint of the certificate.</span></span>
   
    ```
    certutil -store -user my [subject]
    ```
    
    <span data-ttu-id="a35e7-177">Per `[subject]`, usare il valore specificato per Subject nello script di PowerShell.</span><span class="sxs-lookup"><span data-stu-id="a35e7-177">For `[subject]`, use the value that you specified for Subject in the PowerShell script.</span></span> <span data-ttu-id="a35e7-178">L'identificazione personale è elencata in "Cert Hash(sha1)".</span><span class="sxs-lookup"><span data-stu-id="a35e7-178">The thumbprint is listed under "Cert Hash(sha1)".</span></span> <span data-ttu-id="a35e7-179">Copiare questo valore.</span><span class="sxs-lookup"><span data-stu-id="a35e7-179">Copy this value.</span></span> <span data-ttu-id="a35e7-180">L'identificazione personale verrà usata in seguito.</span><span class="sxs-lookup"><span data-stu-id="a35e7-180">You will use the thumbprint later.</span></span>

### <a name="create-a-key-vault"></a><span data-ttu-id="a35e7-181">Creare un insieme di credenziali delle chiavi</span><span class="sxs-lookup"><span data-stu-id="a35e7-181">Create a key vault</span></span>
1. <span data-ttu-id="a35e7-182">Eseguire lo script di PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="a35e7-182">Run the PowerShell script [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault] as follows:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```
   
    <span data-ttu-id="a35e7-183">Quando vengono richieste le credenziali, accedere come l'utente di Azure AD creato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="a35e7-183">When prompted for credentials, sign in as the Azure AD user that you created earlier.</span></span> <span data-ttu-id="a35e7-184">Lo script crea un nuovo gruppo di risorse e un nuovo insieme di credenziali delle chiavi in tale gruppo di risorse.</span><span class="sxs-lookup"><span data-stu-id="a35e7-184">The script creates a new resource group, and a new key vault within that resource group.</span></span> 
   
2. <span data-ttu-id="a35e7-185">Eseguire SetupKeyVault.ps nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="a35e7-185">Run SetupKeyVault.ps again as follows:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<Surveys app id>>", "<<Surveys.WebAPI app ID>>")
    ```
   
    <span data-ttu-id="a35e7-186">Impostare i valori di parametro seguenti:</span><span class="sxs-lookup"><span data-stu-id="a35e7-186">Set the following parameter values:</span></span>
   
       * <span data-ttu-id="a35e7-187">key vault name: nome assegnato all'insieme di credenziali delle chiavi nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="a35e7-187">key vault name = The name that you gave the key vault in the previous step.</span></span>
       * <span data-ttu-id="a35e7-188">Surveys app ID = ID applicazione per l'applicazione Web Surveys.</span><span class="sxs-lookup"><span data-stu-id="a35e7-188">Surveys app ID = The application ID for the Surveys web application.</span></span>
       * <span data-ttu-id="a35e7-189">Surveys.WebApi app ID = ID applicazione per l'applicazione Surveys.WebAPI.</span><span class="sxs-lookup"><span data-stu-id="a35e7-189">Surveys.WebApi app ID = The application ID for the Surveys.WebAPI application.</span></span>
         
    <span data-ttu-id="a35e7-190">Esempio:</span><span class="sxs-lookup"><span data-stu-id="a35e7-190">Example:</span></span>
     
    ```
     .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```
    
    <span data-ttu-id="a35e7-191">Lo script autorizza l'app Web e l'API Web a recuperare segreti dall'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="a35e7-191">This script authorizes the web app and web API to retrieve secrets from your key vault.</span></span> <span data-ttu-id="a35e7-192">Per altre informazioni, vedere [Introduzione ad Azure Key Vault](/azure/key-vault/key-vault-get-started/).</span><span class="sxs-lookup"><span data-stu-id="a35e7-192">See [Get started with Azure Key Vault](/azure/key-vault/key-vault-get-started/) for more information.</span></span>

### <a name="add-configuration-settings-to-your-key-vault"></a><span data-ttu-id="a35e7-193">Aggiungere le impostazioni di configurazione all'insieme di credenziali delle chiavi</span><span class="sxs-lookup"><span data-stu-id="a35e7-193">Add configuration settings to your key vault</span></span>
1. <span data-ttu-id="a35e7-194">Eseguire SetupKeyVault.ps come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a35e7-194">Run SetupKeyVault.ps as follows::</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName Redis--Configuration -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" 
    ```
    <span data-ttu-id="a35e7-195">dove</span><span class="sxs-lookup"><span data-stu-id="a35e7-195">where</span></span>
   
   * <span data-ttu-id="a35e7-196">key vault name: nome assegnato all'insieme di credenziali delle chiavi nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="a35e7-196">key vault name = The name that you gave the key vault in the previous step.</span></span>
   * <span data-ttu-id="a35e7-197">Redis DNS name: nome DNS dell'istanza della cache Redis.</span><span class="sxs-lookup"><span data-stu-id="a35e7-197">Redis DNS name = The DNS name of your Redis cache instance.</span></span>
   * <span data-ttu-id="a35e7-198">Redis access key: chiave di accesso per l'istanza della cache Redis.</span><span class="sxs-lookup"><span data-stu-id="a35e7-198">Redis access key = The access key for your Redis cache instance.</span></span>
     
2. <span data-ttu-id="a35e7-199">A questo punto è consigliabile verificare se i segreti sono stati archiviati correttamente nell'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="a35e7-199">At this point, it's a good idea to test whether you successfully stored the secrets to key vault.</span></span> <span data-ttu-id="a35e7-200">Eseguire il seguente comando PowerShell:</span><span class="sxs-lookup"><span data-stu-id="a35e7-200">Run the following PowerShell command:</span></span>
   
    ```
    Get-AzureKeyVaultSecret <<key vault name>> Redis--Configuration | Select-Object *
    ```

3. <span data-ttu-id="a35e7-201">Eseguire di nuovo SetupKeyVault.ps per aggiungere la stringa di connessione del database:</span><span class="sxs-lookup"><span data-stu-id="a35e7-201">Run SetupKeyVault.ps again to add the database connection string:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName Data--SurveysConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```
   
    <span data-ttu-id="a35e7-202">dove `<<DB connection string>>` è il valore della stringa di connessione del database.</span><span class="sxs-lookup"><span data-stu-id="a35e7-202">where `<<DB connection string>>` is the value of the database connection string.</span></span>
   
    <span data-ttu-id="a35e7-203">Per il test con il database locale, copiare la stringa di connessione dal file Tailspin.Surveys.Web/appsettings.json.</span><span class="sxs-lookup"><span data-stu-id="a35e7-203">For testing with the local database, copy the connection string from the Tailspin.Surveys.Web/appsettings.json file.</span></span> <span data-ttu-id="a35e7-204">In questo caso, assicurarsi di modificare la doppia barra rovesciata ("\\\\") in barra rovesciata singola.</span><span class="sxs-lookup"><span data-stu-id="a35e7-204">If you do that, make sure to change the double backslash ('\\\\') into a single backslash.</span></span> <span data-ttu-id="a35e7-205">La doppia barra rovesciata è un carattere di escape nel file JSON.</span><span class="sxs-lookup"><span data-stu-id="a35e7-205">The double backslash is an escape character in the JSON file.</span></span>
   
    <span data-ttu-id="a35e7-206">Esempio:</span><span class="sxs-lookup"><span data-stu-id="a35e7-206">Example:</span></span>
   
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName Data--SurveysConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" 
    ```

### <a name="uncomment-the-code-that-enables-key-vault"></a><span data-ttu-id="a35e7-207">Rimuovere i commenti dal codice che abilita l'insieme di credenziali delle chiavi</span><span class="sxs-lookup"><span data-stu-id="a35e7-207">Uncomment the code that enables Key Vault</span></span>
1. <span data-ttu-id="a35e7-208">Aprire la soluzione Tailspin.Surveys.</span><span class="sxs-lookup"><span data-stu-id="a35e7-208">Open the Tailspin.Surveys solution.</span></span>
2. <span data-ttu-id="a35e7-209">In Tailspin.Surveys.Web/Startup.cs individuare il blocco di codice seguente e rimuovere i commenti.</span><span class="sxs-lookup"><span data-stu-id="a35e7-209">In Tailspin.Surveys.Web/Startup.cs, locate the following code block and uncomment it.</span></span>
   
    ```csharp
    //var config = builder.Build();
    //builder.AddAzureKeyVault(
    //    $"https://{config["KeyVault:Name"]}.vault.azure.net/",
    //    config["AzureAd:ClientId"],
    //    config["AzureAd:ClientSecret"]);
    ```
3. <span data-ttu-id="a35e7-210">In Tailspin.Surveys.Web/Startup.cs individuare il codice per la registrazione di `ICredentialService`.</span><span class="sxs-lookup"><span data-stu-id="a35e7-210">In Tailspin.Surveys.Web/Startup.cs, locate the code that registers the `ICredentialService`.</span></span> <span data-ttu-id="a35e7-211">Rimuovere il commento dalla riga che usa `CertificateCredentialService` e impostare come commento la riga che usa `ClientCredentialService`:</span><span class="sxs-lookup"><span data-stu-id="a35e7-211">Uncomment the line that uses `CertificateCredentialService`, and comment out the line that uses `ClientCredentialService`:</span></span>
   
    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```
   
    <span data-ttu-id="a35e7-212">Questa modifica consente all'app Web di usare l'[asserzione client][client-assertion] per ottenere i token di accesso OAuth.</span><span class="sxs-lookup"><span data-stu-id="a35e7-212">This change enables the web app to use [Client assertion][client-assertion] to get OAuth access tokens.</span></span> <span data-ttu-id="a35e7-213">Con l'asserzione client, non è necessario un segreto client OAuth.</span><span class="sxs-lookup"><span data-stu-id="a35e7-213">With client assertion, you don't need an OAuth client secret.</span></span> <span data-ttu-id="a35e7-214">In alternativa, è possibile archiviare il segreto client nell'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="a35e7-214">Alternatively, you could store the client secret in key vault.</span></span> <span data-ttu-id="a35e7-215">Tuttavia, poiché l'insieme di credenziali delle chiavi e l'asserzione client usano entrambi un certificato client, se si abilita l'insieme di credenziali delle chiavi, è consigliabile abilitare anche l'asserzione client.</span><span class="sxs-lookup"><span data-stu-id="a35e7-215">However, key vault and client assertion both use a client certificate, so if you enable key vault, it's a good practice to enable client assertion as well.</span></span>

### <a name="update-the-user-secrets"></a><span data-ttu-id="a35e7-216">Aggiornare i segreti utente</span><span class="sxs-lookup"><span data-stu-id="a35e7-216">Update the user secrets</span></span>
<span data-ttu-id="a35e7-217">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Tailspin.Surveys.Web e scegliere **Gestisci segreti utente**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-217">In Solution Explorer, right-click the Tailspin.Surveys.Web project and select **Manage User Secrets**.</span></span> <span data-ttu-id="a35e7-218">Nel file secrets.json eliminare il codice JSON esistente e incollare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="a35e7-218">In the secrets.json file, delete the existing JSON and paste in the following:</span></span>

    ```
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

<span data-ttu-id="a35e7-219">Sostituire le voci racchiuse tra [parentesi quadre] con i valori corretti.</span><span class="sxs-lookup"><span data-stu-id="a35e7-219">Replace the entries in [square brackets] with the correct values.</span></span>

* <span data-ttu-id="a35e7-220">`AzureAd:ClientId`: ID client dell'app Surveys.</span><span class="sxs-lookup"><span data-stu-id="a35e7-220">`AzureAd:ClientId`: The client ID of the Surveys app.</span></span>
* <span data-ttu-id="a35e7-221">`AzureAd:ClientSecret`: chiave generata durante la registrazione dell'applicazione Surveys in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a35e7-221">`AzureAd:ClientSecret`: The key that you generated when you registered the Surveys application in Azure AD.</span></span>
* <span data-ttu-id="a35e7-222">`AzureAd:WebApiResourceId`: URI ID app specificato durante la creazione dell'applicazione Surveys.WebAPI in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a35e7-222">`AzureAd:WebApiResourceId`: The App ID URI that you specified when you created the Surveys.WebAPI application in Azure AD.</span></span>
* <span data-ttu-id="a35e7-223">`Asymmetric:CertificateThumbprint`: identificazione personale del certificato ottenuta in precedenza, durante la creazione del certificato client.</span><span class="sxs-lookup"><span data-stu-id="a35e7-223">`Asymmetric:CertificateThumbprint`: The certificate thumbprint that you got previously, when you created the client certificate.</span></span>
* <span data-ttu-id="a35e7-224">`KeyVault:Name`: nome dell'insieme di credenziali delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="a35e7-224">`KeyVault:Name`: The name of your key vault.</span></span>

> [!NOTE]
> <span data-ttu-id="a35e7-225">`Asymmetric:ValidationRequired` è false perché il certificato creato in precedenza non è stato firmato dall'autorità di certificazione radice.</span><span class="sxs-lookup"><span data-stu-id="a35e7-225">`Asymmetric:ValidationRequired` is false because the certificate that you created previously was not signed by a root certificate authority (CA).</span></span> <span data-ttu-id="a35e7-226">Nell'ambiente di produzione usare un certificato firmato da un'autorità di certificazione radice e impostare `ValidationRequired` su true.</span><span class="sxs-lookup"><span data-stu-id="a35e7-226">In production, use a certificate that is signed by a root CA and set `ValidationRequired` to true.</span></span>
> 
> 

<span data-ttu-id="a35e7-227">Salvare il file secrets.json aggiornato.</span><span class="sxs-lookup"><span data-stu-id="a35e7-227">Save the updated secrets.json file.</span></span>

<span data-ttu-id="a35e7-228">In Esplora soluzioni fare quindi clic con il pulsante destro del mouse sul progetto Tailspin.Surveys.WebApi e scegliere **Gestisci segreti utente**.</span><span class="sxs-lookup"><span data-stu-id="a35e7-228">Next, in Solution Explorer, right-click the Tailspin.Surveys.WebApi project and select **Manage User Secrets**.</span></span> <span data-ttu-id="a35e7-229">Eliminare il codice JSON esistente e incollare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="a35e7-229">Delete the existing JSON and paste in the following:</span></span>

```
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

<span data-ttu-id="a35e7-230">Sostituire le voci tra [parentesi quadre] e salvare il file secrets.json.</span><span class="sxs-lookup"><span data-stu-id="a35e7-230">Replace the entries in [square brackets] and save the secrets.json file.</span></span>

> [!NOTE]
> <span data-ttu-id="a35e7-231">Per l'API Web assicurarsi di usare l'ID client per l'applicazione Surveys.WebAPI, non l'applicazione Surveys.</span><span class="sxs-lookup"><span data-stu-id="a35e7-231">For the web API, make sure to use the client ID for the Surveys.WebAPI application, not the Surveys application.</span></span>
> 
> 

<span data-ttu-id="a35e7-232">[**Avanti**][adfs]</span><span class="sxs-lookup"><span data-stu-id="a35e7-232">[**Next**][adfs]</span></span>

<!-- Links -->
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
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
