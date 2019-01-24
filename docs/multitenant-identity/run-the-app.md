---
title: Eseguire l'applicazione Surveys
description: Come eseguire l'applicazione di esempio Surveys in locale.
author: MikeWasson
ms.date: 07/21/2017
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: bdc2f4da6dd151cf395bee700e6a44e827c1e4f9
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481582"
---
# <a name="run-the-surveys-application"></a><span data-ttu-id="3a098-103">Eseguire l'applicazione Surveys</span><span class="sxs-lookup"><span data-stu-id="3a098-103">Run the Surveys application</span></span>

<span data-ttu-id="3a098-104">In questo articolo viene descritto come eseguire in locale l'applicazione [Tailspin Surveys](./tailspin.md) da Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3a098-104">This article describes how to run the [Tailspin Surveys](./tailspin.md) application locally, from Visual Studio.</span></span> <span data-ttu-id="3a098-105">In questi passaggi l'applicazione non verrà distribuita ad Azure,</span><span class="sxs-lookup"><span data-stu-id="3a098-105">In these steps, you won't deploy the application to Azure.</span></span> <span data-ttu-id="3a098-106">tuttavia sarà necessario creare alcune risorse di Azure &mdash; una directory Azure Active Directory (Azure AD) e una cache Redis.</span><span class="sxs-lookup"><span data-stu-id="3a098-106">However, you will need to create some Azure resources &mdash; an Azure Active Directory (Azure AD) directory and a Redis cache.</span></span>

<span data-ttu-id="3a098-107">Di seguito viene presentato un riepilogo dei passaggi:</span><span class="sxs-lookup"><span data-stu-id="3a098-107">Here is a summary of the steps:</span></span>

1. <span data-ttu-id="3a098-108">Creare una directory Azure AD (tenant) per la società fittizia Tailspin.</span><span class="sxs-lookup"><span data-stu-id="3a098-108">Create an Azure AD directory (tenant) for the fictitious Tailspin company.</span></span>
2. <span data-ttu-id="3a098-109">Registrare l'applicazione Surveys e l'API Web back-end in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="3a098-109">Register the Surveys application and the backend web API with Azure AD.</span></span>
3. <span data-ttu-id="3a098-110">Creare un'istanza di Cache Redis di Azure.</span><span class="sxs-lookup"><span data-stu-id="3a098-110">Create an Azure Redis Cache instance.</span></span>
4. <span data-ttu-id="3a098-111">Configurare le impostazioni dell'applicazione e creare un database locale.</span><span class="sxs-lookup"><span data-stu-id="3a098-111">Configure application settings and create a local database.</span></span>
5. <span data-ttu-id="3a098-112">Eseguire l'applicazione e registrare un nuovo tenant.</span><span class="sxs-lookup"><span data-stu-id="3a098-112">Run the application and sign up a new tenant.</span></span>
6. <span data-ttu-id="3a098-113">Aggiungere i ruoli applicazione agli utenti.</span><span class="sxs-lookup"><span data-stu-id="3a098-113">Add application roles to users.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a098-114">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3a098-114">Prerequisites</span></span>

- <span data-ttu-id="3a098-115">[Visual Studio 2017][VS2017] con il [carico di lavoro Sviluppo ASP.NET e Web](https://visualstudio.microsoft.com/vs/support/selecting-workloads-visual-studio-2017) installato</span><span class="sxs-lookup"><span data-stu-id="3a098-115">[Visual Studio 2017][VS2017] with the [ASP.NET and web development workload](https://visualstudio.microsoft.com/vs/support/selecting-workloads-visual-studio-2017) installed</span></span>
- <span data-ttu-id="3a098-116">Account di [Microsoft Azure](https://azure.microsoft.com)</span><span class="sxs-lookup"><span data-stu-id="3a098-116">[Microsoft Azure](https://azure.microsoft.com) account</span></span>

## <a name="create-the-tailspin-tenant"></a><span data-ttu-id="3a098-117">Creare il tenant Tailspin</span><span class="sxs-lookup"><span data-stu-id="3a098-117">Create the Tailspin tenant</span></span>

<span data-ttu-id="3a098-118">Tailspin è la società fittizia che ospita l'applicazione Surveys</span><span class="sxs-lookup"><span data-stu-id="3a098-118">Tailspin is the fictitious company that hosts the Surveys application.</span></span> <span data-ttu-id="3a098-119">e usa Azure AD per abilitare la registrazione di altri tenant con l'app.</span><span class="sxs-lookup"><span data-stu-id="3a098-119">Tailspin uses Azure AD to enable other tenants to register with the app.</span></span> <span data-ttu-id="3a098-120">Tali clienti quindi possono usare le proprie credenziali di Azure AD per accedere all'app.</span><span class="sxs-lookup"><span data-stu-id="3a098-120">Those customers can then use their Azure AD credentials to sign into the app.</span></span>

<span data-ttu-id="3a098-121">In questo passaggio si creerà una directory di Azure AD per Tailspin.</span><span class="sxs-lookup"><span data-stu-id="3a098-121">In this step, you'll create an Azure AD directory for Tailspin.</span></span>

1. <span data-ttu-id="3a098-122">Accedere al [portale di Azure][portal].</span><span class="sxs-lookup"><span data-stu-id="3a098-122">Sign into the [Azure portal][portal].</span></span>

2. <span data-ttu-id="3a098-123">Fare clic su **+ Crea una risorsa** > **Identità** > **Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="3a098-123">Click **+ Create a Resource** > **Identity** > **Azure Active Directory**.</span></span>

3. <span data-ttu-id="3a098-124">Immettere `Tailspin` per il nome dell'organizzazione e un nome di dominio.</span><span class="sxs-lookup"><span data-stu-id="3a098-124">Enter `Tailspin` for the organization name, and enter a domain name.</span></span> <span data-ttu-id="3a098-125">Il nome di dominio avrà la forma di `xxxx.onmicrosoft.com` e deve essere globalmente univoco.</span><span class="sxs-lookup"><span data-stu-id="3a098-125">The domain name will have the form `xxxx.onmicrosoft.com` and must be globally unique.</span></span>

    ![Finestra di dialogo per la creazione di una directory](./images/running-the-app/new-tenant.png)

4. <span data-ttu-id="3a098-127">Fare clic su **Create**(Crea).</span><span class="sxs-lookup"><span data-stu-id="3a098-127">Click **Create**.</span></span> <span data-ttu-id="3a098-128">La creazione della nuova directory può richiedere alcuni minuti.</span><span class="sxs-lookup"><span data-stu-id="3a098-128">It may take a few minutes to create the new directory.</span></span>

<span data-ttu-id="3a098-129">Per completare lo scenario end-to-end, sarà necessario usare una seconda directory Azure AD per rappresentare un cliente che procede alla registrazione per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="3a098-129">To complete the end-to-end scenario, you'll need a second Azure AD directory to represent a customer that signs up for the application.</span></span> <span data-ttu-id="3a098-130">È possibile usare la directory di Azure AD predefinita (non Tailspin) o creare una nuova directory per questo scopo.</span><span class="sxs-lookup"><span data-stu-id="3a098-130">You can use your default Azure AD directory (not Tailspin), or create a new directory for this purpose.</span></span> <span data-ttu-id="3a098-131">Negli esempi, viene usato Contoso come cliente fittizio.</span><span class="sxs-lookup"><span data-stu-id="3a098-131">In the examples, we use Contoso as the fictitious customer.</span></span>

## <a name="register-the-surveys-web-api"></a><span data-ttu-id="3a098-132">Registrare l'API Web Surveys</span><span class="sxs-lookup"><span data-stu-id="3a098-132">Register the Surveys web API</span></span>

1. <span data-ttu-id="3a098-133">Nel [portale di Azure][portal] passare alla nuova directory Tailspin selezionando l'account nell'angolo superiore destro.</span><span class="sxs-lookup"><span data-stu-id="3a098-133">In the [Azure portal][portal], switch to the new Tailspin directory by selecting your account in the top right corner of the portal.</span></span>

2. <span data-ttu-id="3a098-134">Scegliere **Azure Active Directory** nel riquadro di navigazione a sinistra.</span><span class="sxs-lookup"><span data-stu-id="3a098-134">In the left-hand navigation pane, choose **Azure Active Directory**.</span></span>

3. <span data-ttu-id="3a098-135">Fare clic su **Registrazioni per l'app** > **Registrazione nuova applicazione**.</span><span class="sxs-lookup"><span data-stu-id="3a098-135">Click **App registrations** > **New application registration**.</span></span>

4. <span data-ttu-id="3a098-136">Nel pannello **Crea** immettere le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3a098-136">In the **Create** blade, enter the following information:</span></span>

   - <span data-ttu-id="3a098-137">**Nome**: `Surveys.WebAPI`</span><span class="sxs-lookup"><span data-stu-id="3a098-137">**Name**: `Surveys.WebAPI`</span></span>

   - <span data-ttu-id="3a098-138">**Tipo di applicazione**: `Web app / API`</span><span class="sxs-lookup"><span data-stu-id="3a098-138">**Application type**: `Web app / API`</span></span>

   - <span data-ttu-id="3a098-139">**URL di accesso**: `https://localhost:44301/`</span><span class="sxs-lookup"><span data-stu-id="3a098-139">**Sign-on URL**: `https://localhost:44301/`</span></span>

   ![Screenshot per la registrazione dell'API Web](./images/running-the-app/register-web-api.png)

5. <span data-ttu-id="3a098-141">Fare clic su **Create**(Crea).</span><span class="sxs-lookup"><span data-stu-id="3a098-141">Click **Create**.</span></span>

6. <span data-ttu-id="3a098-142">Nel pannello **Registrazioni per l'app** selezionare la nuova applicazione **Surveys.WebAPI**.</span><span class="sxs-lookup"><span data-stu-id="3a098-142">In the **App registrations** blade, select the new **Surveys.WebAPI** application.</span></span>

7. <span data-ttu-id="3a098-143">Fare clic su **Impostazioni** > **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="3a098-143">Click **Settings** > **Properties**.</span></span>

8. <span data-ttu-id="3a098-144">Nella casella di modifica **URI ID app** immettere `https://<domain>/surveys.webapi`, dove `<domain>` è il nome di dominio della directory.</span><span class="sxs-lookup"><span data-stu-id="3a098-144">In the **App ID URI** edit box, enter `https://<domain>/surveys.webapi`, where `<domain>` is the domain name of the directory.</span></span> <span data-ttu-id="3a098-145">Ad esempio: `https://tailspin.onmicrosoft.com/surveys.webapi`</span><span class="sxs-lookup"><span data-stu-id="3a098-145">For example: `https://tailspin.onmicrosoft.com/surveys.webapi`</span></span>

    ![Impostazioni](./images/running-the-app/settings.png)

9. <span data-ttu-id="3a098-147">Impostare **Multi-tenant** su **SÌ**.</span><span class="sxs-lookup"><span data-stu-id="3a098-147">Set **Multi-tenanted** to **YES**.</span></span>

10. <span data-ttu-id="3a098-148">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="3a098-148">Click **Save**.</span></span>

## <a name="register-the-surveys-web-app"></a><span data-ttu-id="3a098-149">Registrare l'app Web Surveys</span><span class="sxs-lookup"><span data-stu-id="3a098-149">Register the Surveys web app</span></span>

1. <span data-ttu-id="3a098-150">Tornare al pannello **Registrazioni per l'app** e fare clic su **Registrazione nuova applicazione**.</span><span class="sxs-lookup"><span data-stu-id="3a098-150">Navigate back to the **App registrations** blade, and click **New application registration**.</span></span>

2. <span data-ttu-id="3a098-151">Nel pannello **Crea** immettere le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3a098-151">In the **Create** blade, enter the following information:</span></span>

    - <span data-ttu-id="3a098-152">**Nome**: `Surveys`</span><span class="sxs-lookup"><span data-stu-id="3a098-152">**Name**: `Surveys`</span></span>
    - <span data-ttu-id="3a098-153">**Tipo di applicazione**: `Web app / API`</span><span class="sxs-lookup"><span data-stu-id="3a098-153">**Application type**: `Web app / API`</span></span>
    - <span data-ttu-id="3a098-154">**URL di accesso**: `https://localhost:44300/`</span><span class="sxs-lookup"><span data-stu-id="3a098-154">**Sign-on URL**: `https://localhost:44300/`</span></span>

    <span data-ttu-id="3a098-155">Si noti che l'URL di accesso ha un numero di porta diverso dall'app `Surveys.WebAPI` nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="3a098-155">Notice that the sign-on URL has a different port number from the `Surveys.WebAPI` app in the previous step.</span></span>

3. <span data-ttu-id="3a098-156">Fare clic su **Create**(Crea).</span><span class="sxs-lookup"><span data-stu-id="3a098-156">Click **Create**.</span></span>

4. <span data-ttu-id="3a098-157">Nel pannello **Registrazioni per l'app** selezionare la nuova applicazione **Surveys**.</span><span class="sxs-lookup"><span data-stu-id="3a098-157">In the **App registrations** blade, select the new **Surveys** application.</span></span>

5. <span data-ttu-id="3a098-158">Copiare l'ID applicazione</span><span class="sxs-lookup"><span data-stu-id="3a098-158">Copy the application ID.</span></span> <span data-ttu-id="3a098-159">che sarà necessario più avanti.</span><span class="sxs-lookup"><span data-stu-id="3a098-159">You will need this later.</span></span>

    ![Screenshot della copia dell'ID applicazione](./images/running-the-app/application-id.png)

6. <span data-ttu-id="3a098-161">Fare clic su **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="3a098-161">Click **Properties**.</span></span>

7. <span data-ttu-id="3a098-162">Nella casella di modifica **URI ID app** immettere `https://<domain>/surveys`, dove `<domain>` è il nome di dominio della directory.</span><span class="sxs-lookup"><span data-stu-id="3a098-162">In the **App ID URI** edit box, enter `https://<domain>/surveys`, where `<domain>` is the domain name of the directory.</span></span>

    ![Impostazioni](./images/running-the-app/settings.png)

8. <span data-ttu-id="3a098-164">Impostare **Multi-tenant** su **SÌ**.</span><span class="sxs-lookup"><span data-stu-id="3a098-164">Set **Multi-tenanted** to **YES**.</span></span>

9. <span data-ttu-id="3a098-165">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="3a098-165">Click **Save**.</span></span>

10. <span data-ttu-id="3a098-166">Nel pannello **Impostazioni** fare clic su **URL di risposta**.</span><span class="sxs-lookup"><span data-stu-id="3a098-166">In the **Settings** blade, click **Reply URLs**.</span></span>

11. <span data-ttu-id="3a098-167">Aggiungere l'URL di risposta seguente : `https://localhost:44300/signin-oidc`.</span><span class="sxs-lookup"><span data-stu-id="3a098-167">Add the following reply URL: `https://localhost:44300/signin-oidc`.</span></span>

12. <span data-ttu-id="3a098-168">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="3a098-168">Click **Save**.</span></span>

13. <span data-ttu-id="3a098-169">In **ACCESSO ALL'API** fare clic su **Chiavi**.</span><span class="sxs-lookup"><span data-stu-id="3a098-169">Under **API ACCESS**, click **Keys**.</span></span>

14. <span data-ttu-id="3a098-170">Immettere una descrizione, ad esempio `client secret`.</span><span class="sxs-lookup"><span data-stu-id="3a098-170">Enter a description, such as `client secret`.</span></span>

15. <span data-ttu-id="3a098-171">Nell'elenco a discesa **Seleziona durata** selezionare **1 anno**.</span><span class="sxs-lookup"><span data-stu-id="3a098-171">In the **Select Duration** dropdown, select **1 year**.</span></span>

16. <span data-ttu-id="3a098-172">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="3a098-172">Click **Save**.</span></span> <span data-ttu-id="3a098-173">La chiave verrà generata al salvataggio.</span><span class="sxs-lookup"><span data-stu-id="3a098-173">The key will be generated when you save.</span></span>

17. <span data-ttu-id="3a098-174">Prima si uscire da questo pannello, copiare il valore della chiave:</span><span class="sxs-lookup"><span data-stu-id="3a098-174">Before you navigate away from this blade, copy the value of the key.</span></span>

    > [!NOTE]
    > <span data-ttu-id="3a098-175">dopo che si esce dal pannello, la chiave non verrà visualizzata nuovamente.</span><span class="sxs-lookup"><span data-stu-id="3a098-175">The key won't be visible again after you navigate away from the blade.</span></span>

18. <span data-ttu-id="3a098-176">In **ACCESSO ALL'API** fare clic su **Autorizzazioni necessarie**.</span><span class="sxs-lookup"><span data-stu-id="3a098-176">Under **API ACCESS**, click **Required permissions**.</span></span>

19. <span data-ttu-id="3a098-177">Fare clic su **Aggiungi** > **Selezionare un'API**.</span><span class="sxs-lookup"><span data-stu-id="3a098-177">Click **Add** > **Select an API**.</span></span>

20. <span data-ttu-id="3a098-178">Cercare `Surveys.WebAPI` cella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="3a098-178">In the search box, search for `Surveys.WebAPI`.</span></span>

    ![Autorizzazioni](./images/running-the-app/permissions.png)

21. <span data-ttu-id="3a098-180">Selezionare `Surveys.WebAPI` e fare clic su **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="3a098-180">Select `Surveys.WebAPI` and click **Select**.</span></span>

22. <span data-ttu-id="3a098-181">In **Autorizzazioni delegate** selezionare la casella di controllo **Access Surveys.WebAPI** (Accedi a Surveys.WebAPI).</span><span class="sxs-lookup"><span data-stu-id="3a098-181">Under **Delegated Permissions**, check **Access Surveys.WebAPI**.</span></span>

    ![Impostazione delle autorizzazioni delegate](./images/running-the-app/delegated-permissions.png)

23. <span data-ttu-id="3a098-183">Fare clic su **Seleziona** > **Fine**.</span><span class="sxs-lookup"><span data-stu-id="3a098-183">Click **Select** > **Done**.</span></span>

## <a name="update-the-application-manifests"></a><span data-ttu-id="3a098-184">Aggiornare i manifesti dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="3a098-184">Update the application manifests</span></span>

1. <span data-ttu-id="3a098-185">Tornare al pannello **Impostazioni** per l'app `Surveys.WebAPI`.</span><span class="sxs-lookup"><span data-stu-id="3a098-185">Navigate back to the **Settings** blade for the `Surveys.WebAPI` app.</span></span>

2. <span data-ttu-id="3a098-186">Fare clic su **Manifesto** > **Modifica**.</span><span class="sxs-lookup"><span data-stu-id="3a098-186">Click **Manifest** > **Edit**.</span></span>

    ![Screenshot della modifica del manifesto dell'applicazione](./images/running-the-app/manifest.png)

3. <span data-ttu-id="3a098-188">Aggiungere il JSON seguente all'elemento `appRoles`.</span><span class="sxs-lookup"><span data-stu-id="3a098-188">Add the following JSON to the `appRoles` element.</span></span> <span data-ttu-id="3a098-189">Generare nuovi GUID per le proprietà `id`.</span><span class="sxs-lookup"><span data-stu-id="3a098-189">Generate new GUIDs for the `id` properties.</span></span>

   ```json
   {
     "allowedMemberTypes": ["User"],
     "description": "Creators can create surveys",
     "displayName": "SurveyCreator",
     "id": "<Generate a new GUID. Example: 1b4f816e-5eaf-48b9-8613-7923830595ad>",
     "isEnabled": true,
     "value": "SurveyCreator"
   },
   {
     "allowedMemberTypes": ["User"],
     "description": "Administrators can manage the surveys in their tenant",
     "displayName": "SurveyAdmin",
     "id": "<Generate a new GUID>",  
     "isEnabled": true,
     "value": "SurveyAdmin"
   }
   ```

4. <span data-ttu-id="3a098-190">Nella proprietà `knownClientApplications` aggiungere l'ID applicazione per l'app Web Surveys, che è stato ottenuto in precedenza durante la registrazione dell'applicazione Surveys.</span><span class="sxs-lookup"><span data-stu-id="3a098-190">In the `knownClientApplications` property, add the application ID for the Surveys web application, which you got when you registered the Surveys application earlier.</span></span> <span data-ttu-id="3a098-191">Ad esempio: </span><span class="sxs-lookup"><span data-stu-id="3a098-191">For example:</span></span>

   ```json
   "knownClientApplications": ["be2cea23-aa0e-4e98-8b21-2963d494912e"],
   ```

   <span data-ttu-id="3a098-192">Questa impostazione aggiunge l'app Surveys all'elenco dei client autorizzati a chiamare l'API Web.</span><span class="sxs-lookup"><span data-stu-id="3a098-192">This setting adds the Surveys app to the list of clients authorized to call the web API.</span></span>

5. <span data-ttu-id="3a098-193">Fare clic su **Save**.</span><span class="sxs-lookup"><span data-stu-id="3a098-193">Click **Save**.</span></span>

<span data-ttu-id="3a098-194">Ora ripetere gli stessi passaggi per l'app Surveys, ad eccezione di non aggiungere una voce per `knownClientApplications`.</span><span class="sxs-lookup"><span data-stu-id="3a098-194">Now repeat the same steps for the Surveys app, except do not add an entry for `knownClientApplications`.</span></span> <span data-ttu-id="3a098-195">Usare le stesse definizioni dei ruoli, ma generare nuovi GUID per gli ID.</span><span class="sxs-lookup"><span data-stu-id="3a098-195">Use the same role definitions, but generate new GUIDs for the IDs.</span></span>

## <a name="create-a-new-redis-cache-instance"></a><span data-ttu-id="3a098-196">Creare una nuova istanza di cache Redis</span><span class="sxs-lookup"><span data-stu-id="3a098-196">Create a new Redis Cache instance</span></span>

<span data-ttu-id="3a098-197">L'applicazione Surveys usa Redis per memorizzare nella cache i token di accesso OAuth 2.</span><span class="sxs-lookup"><span data-stu-id="3a098-197">The Surveys application uses Redis to cache OAuth 2 access tokens.</span></span> <span data-ttu-id="3a098-198">Per creare la cache:</span><span class="sxs-lookup"><span data-stu-id="3a098-198">To create the cache:</span></span>

1. <span data-ttu-id="3a098-199">Passare al [portale di Azure](https://portal.azure.com) e fare clic su **+ Crea una risorsa** > **Database** > **Cache Redis**.</span><span class="sxs-lookup"><span data-stu-id="3a098-199">Go to [Azure Portal](https://portal.azure.com) and click **+ Create a Resource** > **Databases** > **Redis Cache**.</span></span>

2. <span data-ttu-id="3a098-200">Inserire le informazioni necessarie, tra cui nome DNS, gruppo di risorse, posizione e piano tariffario.</span><span class="sxs-lookup"><span data-stu-id="3a098-200">Fill in the required information, including DNS name, resource group, location, and pricing tier.</span></span> <span data-ttu-id="3a098-201">È possibile creare un nuovo gruppo di risorse o usarne uno esistente.</span><span class="sxs-lookup"><span data-stu-id="3a098-201">You can create a new resource group or use an existing resource group.</span></span>

3. <span data-ttu-id="3a098-202">Fare clic su **Create**(Crea).</span><span class="sxs-lookup"><span data-stu-id="3a098-202">Click **Create**.</span></span>

4. <span data-ttu-id="3a098-203">Dopo aver creato la cache Redis, passare alla risorsa nel portale.</span><span class="sxs-lookup"><span data-stu-id="3a098-203">After the Redis cache is created, navigate to the resource in the portal.</span></span>

5. <span data-ttu-id="3a098-204">Fare clic su **Chiavi di accesso** e copiare la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="3a098-204">Click **Access keys** and copy the primary key.</span></span>

<span data-ttu-id="3a098-205">Per altre informazioni sulla creazione di un'istanza di cache Redis, vedere [Come usare Cache Redis di Azure](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache).</span><span class="sxs-lookup"><span data-stu-id="3a098-205">For more information about creating a Redis cache, see [How to Use Azure Redis Cache](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache).</span></span>

## <a name="set-application-secrets"></a><span data-ttu-id="3a098-206">Impostare i segreti dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="3a098-206">Set application secrets</span></span>

1. <span data-ttu-id="3a098-207">Aprire la soluzione Tailspin.Surveys in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3a098-207">Open the Tailspin.Surveys solution in Visual Studio.</span></span>

2. <span data-ttu-id="3a098-208">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Tailspin.Surveys.Web e scegliere **Gestisci segreti utente**.</span><span class="sxs-lookup"><span data-stu-id="3a098-208">In Solution Explorer, right-click the Tailspin.Surveys.Web project and select **Manage User Secrets**.</span></span>

3. <span data-ttu-id="3a098-209">Nel file secrets.json, incollare il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3a098-209">In the secrets.json file, paste in the following:</span></span>

    ```json
    {
      "AzureAd": {
        "ClientId": "<Surveys application ID>",
        "ClientSecret": "<Surveys app client secret>",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "<Surveys.WebAPI app ID URI>"
      },
      "Redis": {
        "Configuration": "<Redis DNS name>.redis.cache.windows.net,password=<Redis primary key>,ssl=true"
      }
    }
    ```

    <span data-ttu-id="3a098-210">Sostituire gli elementi visualizzati tra parentesi acute, come illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="3a098-210">Replace the items shown in angle brackets, as follows:</span></span>

    - <span data-ttu-id="3a098-211">`AzureAd:ClientId`: ID applicazione dell'app Surveys.</span><span class="sxs-lookup"><span data-stu-id="3a098-211">`AzureAd:ClientId`: The application ID of the Surveys app.</span></span>
    - <span data-ttu-id="3a098-212">`AzureAd:ClientSecret`: chiave generata durante la registrazione dell'applicazione Surveys in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="3a098-212">`AzureAd:ClientSecret`: The key that you generated when you registered the Surveys application in Azure AD.</span></span>
    - <span data-ttu-id="3a098-213">`AzureAd:WebApiResourceId`: URI ID app specificato durante la creazione dell'applicazione Surveys.WebAPI in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="3a098-213">`AzureAd:WebApiResourceId`: The App ID URI that you specified when you created the Surveys.WebAPI application in Azure AD.</span></span> <span data-ttu-id="3a098-214">Dovrebbe avere questa forma: `https://<directory>.onmicrosoft.com/surveys.webapi`</span><span class="sxs-lookup"><span data-stu-id="3a098-214">It should have the form `https://<directory>.onmicrosoft.com/surveys.webapi`</span></span>
    - <span data-ttu-id="3a098-215">`Redis:Configuration`: compilare questa stringa con il nome DNS della cache Redis e la chiave di accesso primaria.</span><span class="sxs-lookup"><span data-stu-id="3a098-215">`Redis:Configuration`: Build this string from the DNS name of the Redis cache and the primary access key.</span></span> <span data-ttu-id="3a098-216">Ad esempio, "tailspin.redis.cache.windows.net,password=2h5tBxxx,ssl=true".</span><span class="sxs-lookup"><span data-stu-id="3a098-216">For example, "tailspin.redis.cache.windows.net,password=2h5tBxxx,ssl=true".</span></span>

4. <span data-ttu-id="3a098-217">Salvare il file secrets.json aggiornato.</span><span class="sxs-lookup"><span data-stu-id="3a098-217">Save the updated secrets.json file.</span></span>

5. <span data-ttu-id="3a098-218">Ripetere questi passaggi per il progetto Tailspin.Surveys.WebAPI ma incollare il codice seguente in secrets.json.</span><span class="sxs-lookup"><span data-stu-id="3a098-218">Repeat these steps for the Tailspin.Surveys.WebAPI project, but paste the following into secrets.json.</span></span> <span data-ttu-id="3a098-219">Sostituire gli elementi tra parentesi acute, come in precedenza.</span><span class="sxs-lookup"><span data-stu-id="3a098-219">Replace the items in angle brackets, as before.</span></span>

    ```json
    {
      "AzureAd": {
        "WebApiResourceId": "<Surveys.WebAPI app ID URI>"
      },
      "Redis": {
        "Configuration": "<Redis DNS name>.redis.cache.windows.net,password=<Redis primary key>,ssl=true"
      }
    }
    ```

## <a name="initialize-the-database"></a><span data-ttu-id="3a098-220">Inizializzare il database</span><span class="sxs-lookup"><span data-stu-id="3a098-220">Initialize the database</span></span>

<span data-ttu-id="3a098-221">In questo passaggio viene usato Entity Framework 7 per creare un database SQL locale con Local DB.</span><span class="sxs-lookup"><span data-stu-id="3a098-221">In this step, you will use Entity Framework 7 to create a local SQL database, using LocalDB.</span></span>

1. <span data-ttu-id="3a098-222">Aprire una finestra di comando</span><span class="sxs-lookup"><span data-stu-id="3a098-222">Open a command window</span></span>

2. <span data-ttu-id="3a098-223">Passare al progetto Tailspin.Surveys.Data.</span><span class="sxs-lookup"><span data-stu-id="3a098-223">Navigate to the Tailspin.Surveys.Data project.</span></span>

3. <span data-ttu-id="3a098-224">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="3a098-224">Run the following command:</span></span>

    ```bat
    dotnet ef database update --startup-project ..\Tailspin.Surveys.Web
    ```

## <a name="run-the-application"></a><span data-ttu-id="3a098-225">Eseguire l'applicazione</span><span class="sxs-lookup"><span data-stu-id="3a098-225">Run the application</span></span>

<span data-ttu-id="3a098-226">Per eseguire l'applicazione, avviare entrambi i progetti Tailspin.Surveys.Web e Tailspin.Surveys.WebAPI.</span><span class="sxs-lookup"><span data-stu-id="3a098-226">To run the application, start both the Tailspin.Surveys.Web and Tailspin.Surveys.WebAPI projects.</span></span>

<span data-ttu-id="3a098-227">È possibile impostare Visual Studio per eseguire automaticamente entrambi i progetti su F5, come segue:</span><span class="sxs-lookup"><span data-stu-id="3a098-227">You can set Visual Studio to run both projects automatically on F5, as follows:</span></span>

1. <span data-ttu-id="3a098-228">In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e fare clic su **Imposta progetti di avvio**.</span><span class="sxs-lookup"><span data-stu-id="3a098-228">In Solution Explorer, right-click the solution and click **Set Startup Projects**.</span></span>
2. <span data-ttu-id="3a098-229">Selezionare **Progetti di avvio multipli**.</span><span class="sxs-lookup"><span data-stu-id="3a098-229">Select **Multiple startup projects**.</span></span>
3. <span data-ttu-id="3a098-230">Impostare **Azione** = **Avvia** per i progetti Tailspin.Surveys.Web e Tailspin.Surveys.WebAPI.</span><span class="sxs-lookup"><span data-stu-id="3a098-230">Set **Action** = **Start** for the Tailspin.Surveys.Web and Tailspin.Surveys.WebAPI projects.</span></span>

## <a name="sign-up-a-new-tenant"></a><span data-ttu-id="3a098-231">Registrare un nuovo tenant</span><span class="sxs-lookup"><span data-stu-id="3a098-231">Sign up a new tenant</span></span>

<span data-ttu-id="3a098-232">All'avvio dell'applicazione, non viene eseguito alcun accesso e viene quindi visualizzata la home page:</span><span class="sxs-lookup"><span data-stu-id="3a098-232">When the application starts, you are not signed in, so you see the welcome page:</span></span>

![Home page](./images/running-the-app/screenshot1.png)

<span data-ttu-id="3a098-234">Per registrare un'organizzazione:</span><span class="sxs-lookup"><span data-stu-id="3a098-234">To sign up an organization:</span></span>

1. <span data-ttu-id="3a098-235">Fare clic su **Enroll your company in Tailspin** (Registra la società su Tailspin).</span><span class="sxs-lookup"><span data-stu-id="3a098-235">Click **Enroll your company in Tailspin**.</span></span>
2. <span data-ttu-id="3a098-236">Accedere alla directory di Azure AD che rappresenta l'organizzazione con l'app Surveys.</span><span class="sxs-lookup"><span data-stu-id="3a098-236">Sign in to the Azure AD directory that represents the organization using the Surveys app.</span></span> <span data-ttu-id="3a098-237">È necessario accedere come utente amministratore.</span><span class="sxs-lookup"><span data-stu-id="3a098-237">You must sign in as an admin user.</span></span>
3. <span data-ttu-id="3a098-238">Accettare la richiesta di consenso.</span><span class="sxs-lookup"><span data-stu-id="3a098-238">Accept the consent prompt.</span></span>

<span data-ttu-id="3a098-239">L'applicazione registra il tenant e l'utente viene quindi disconnesso. Questo avviene poiché, prima di usare l'applicazione, è necessario configurare i ruoli applicazione in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="3a098-239">The application registers the tenant, and then signs you out. The app signs you out because you need to set up the application roles in Azure AD, before using the application.</span></span>

![Dopo la registrazione](./images/running-the-app/screenshot2.png)

## <a name="assign-application-roles"></a><span data-ttu-id="3a098-241">Assegnare i ruoli applicazione</span><span class="sxs-lookup"><span data-stu-id="3a098-241">Assign application roles</span></span>

<span data-ttu-id="3a098-242">Quando un tenant esegue la registrazione, è necessario che un amministratore di Azure AD per il tenant assegni i ruoli applicazione agli utenti.</span><span class="sxs-lookup"><span data-stu-id="3a098-242">When a tenant signs up, an AD admin for the tenant must assign application roles to users.</span></span>

1. <span data-ttu-id="3a098-243">Nel [portale di Azure][portal] passare alla directory di Azure AD usata per eseguire la registrazione per l'app Surveys.</span><span class="sxs-lookup"><span data-stu-id="3a098-243">In the [Azure portal][portal], switch to the Azure AD directory that you used to sign up for the Surveys app.</span></span>

2. <span data-ttu-id="3a098-244">Scegliere **Azure Active Directory** nel riquadro di navigazione a sinistra.</span><span class="sxs-lookup"><span data-stu-id="3a098-244">In the left-hand navigation pane, choose **Azure Active Directory**.</span></span>

3. <span data-ttu-id="3a098-245">Fare clic su **Applicazioni aziendali** > **Tutte le applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="3a098-245">Click **Enterprise applications** > **All applications**.</span></span> <span data-ttu-id="3a098-246">Il portale elencherà `Survey` e `Survey.WebAPI`.</span><span class="sxs-lookup"><span data-stu-id="3a098-246">The portal will list `Survey` and `Survey.WebAPI`.</span></span> <span data-ttu-id="3a098-247">In caso contrario, assicurarsi di aver completato il processo di registrazione.</span><span class="sxs-lookup"><span data-stu-id="3a098-247">If not, make sure that you completed the sign up process.</span></span>

4. <span data-ttu-id="3a098-248">Fare clic sull'applicazione Surveys.</span><span class="sxs-lookup"><span data-stu-id="3a098-248">Click on the Surveys application.</span></span>

5. <span data-ttu-id="3a098-249">Fare clic su **Utenti e gruppi**.</span><span class="sxs-lookup"><span data-stu-id="3a098-249">Click **Users and Groups**.</span></span>

6. <span data-ttu-id="3a098-250">Fare clic su **Add User**.</span><span class="sxs-lookup"><span data-stu-id="3a098-250">Click **Add user**.</span></span>

7. <span data-ttu-id="3a098-251">Se si dispone di Azure AD Premium, fare clic su **Utenti e gruppi**.</span><span class="sxs-lookup"><span data-stu-id="3a098-251">If you have Azure AD Premium, click **Users and groups**.</span></span> <span data-ttu-id="3a098-252">In caso contrario, fare clic su **Utenti**</span><span class="sxs-lookup"><span data-stu-id="3a098-252">Otherwise, click **Users**.</span></span> <span data-ttu-id="3a098-253">(l'assegnazione di un ruolo a un gruppo richiede Azure AD Premium).</span><span class="sxs-lookup"><span data-stu-id="3a098-253">(Assigning a role to a group requires Azure AD Premium.)</span></span>

8. <span data-ttu-id="3a098-254">Selezionare uno o più utenti e fare clic su **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="3a098-254">Select one or more users and click **Select**.</span></span>

    ![Selezionare un utente o gruppo](./images/running-the-app/select-user-or-group.png)

9. <span data-ttu-id="3a098-256">Selezionare un ruolo e fare clic su **Seleziona**.</span><span class="sxs-lookup"><span data-stu-id="3a098-256">Select the role and click **Select**.</span></span>

    ![Selezionare un utente o gruppo](./images/running-the-app/select-role.png)

10. <span data-ttu-id="3a098-258">Fare clic su **Assegna**.</span><span class="sxs-lookup"><span data-stu-id="3a098-258">Click **Assign**.</span></span>

<span data-ttu-id="3a098-259">Ripetere gli stessi passaggi per assegnare i ruoli per l'applicazione Survey.WebAPI.</span><span class="sxs-lookup"><span data-stu-id="3a098-259">Repeat the same steps to assign roles for the Survey.WebAPI application.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3a098-260">Un utente deve sempre avere gli stessi ruoli in Survey e in Survey.WebAPI.</span><span class="sxs-lookup"><span data-stu-id="3a098-260">A user should always have the same roles in both Survey and Survey.WebAPI.</span></span> <span data-ttu-id="3a098-261">In caso contrario, l'utente disporrà di autorizzazioni non coerenti e ciò può causare errori 403 (Accesso negato) dall'API Web.</span><span class="sxs-lookup"><span data-stu-id="3a098-261">Otherwise, the user will have inconsistent permissions, which may lead to 403 (Forbidden) errors from the Web API.</span></span>

<span data-ttu-id="3a098-262">Tornare ora all'app e accedere nuovamente.</span><span class="sxs-lookup"><span data-stu-id="3a098-262">Now go back to the app and sign in again.</span></span> <span data-ttu-id="3a098-263">Fare clic su **My Surveys** (I miei sondaggi).</span><span class="sxs-lookup"><span data-stu-id="3a098-263">Click **My Surveys**.</span></span> <span data-ttu-id="3a098-264">Se l'utente è assegnato al ruolo SurveyAdmin o SurveyCreator, verrà visualizzato un pulsante **Create Survey** (Crea sondaggio), che indica che l'utente dispone delle autorizzazioni per creare un nuovo sondaggio.</span><span class="sxs-lookup"><span data-stu-id="3a098-264">If the user is assigned to the SurveyAdmin or SurveyCreator role, you will see a **Create Survey** button, indicating that the user has permissions to create a new survey.</span></span>

![My surveys (I miei sondaggi)](./images/running-the-app/screenshot3.png)

<!-- links -->

[portal]: https://portal.azure.com
[VS2017]: https://www.visualstudio.com/vs/
