---
title: Chatbot di conversazione per prenotazioni di hotel in Azure
description: Scenario collaudato per creare un chatbot di conversazione per applicazioni commerciali con il servizio Azure Bot, Servizi cognitivi e LUIS, un database SQL di Azure e Application Insights.
author: iainfoulds
ms.date: 07/05/2018
ms.openlocfilehash: b664faf20d806824c2581346aaa592b0d74207da
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060864"
---
# <a name="conversational-chatbot-for-hotel-reservations-on-azure"></a><span data-ttu-id="08512-103">Chatbot di conversazione per prenotazioni di hotel in Azure</span><span class="sxs-lookup"><span data-stu-id="08512-103">Conversational chatbot for hotel reservations on Azure</span></span>

<span data-ttu-id="08512-104">Questo scenario di esempio è applicabile ad aziende che devono integrare un chatbot di conversazione nelle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="08512-104">This example scenario is applicable to businesses that need integrate a conversational chatbot into applications.</span></span> <span data-ttu-id="08512-105">In questo scenario, un chatbot C# viene usato per una catena di hotel che consente ai clienti di controllare la disponibilità e prenotare l'alloggio tramite un'applicazione Web o per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="08512-105">In this scenario, a C# chatbot is used for a hotel chain that allows customers to check availability and book accommodation through a web or mobile application.</span></span>

<span data-ttu-id="08512-106">Gli scenari di esempio includono la possibilità per i clienti di visualizzare la disponibilità in hotel e prenotare le camere, esaminare il menu da asporto di un ristorante e ordinare cibo oppure cercare fotografie e ordinarne le stampe.</span><span class="sxs-lookup"><span data-stu-id="08512-106">Example scenarios include providing a way for customers to view hotel availability and book rooms, review a restaurant take-out menu and place a food order, or search for and order prints of photographs.</span></span> <span data-ttu-id="08512-107">Generalmente le aziende devono assumere e formare rappresentanti del servizio clienti per soddisfare queste richieste, mentre i clienti devono attendere che ne sia disponibile uno per ottenere assistenza.</span><span class="sxs-lookup"><span data-stu-id="08512-107">Traditionally, businesses would need to hire and train customer service agents to respond to these customer requests, and customers would have to wait until a representative is available to provide assistance.</span></span>

<span data-ttu-id="08512-108">Con servizi di Azure come il servizio Bot e Language Understanding o Speech API, le aziende possono assistere i clienti ed elaborare gli ordini o le prenotazioni con bot scalabili e automatizzati.</span><span class="sxs-lookup"><span data-stu-id="08512-108">By using Azure services such as the Bot Service and Language Understanding or Speech API services, companies can assist customers and process orders or reservations with automated, scalable bots.</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="08512-109">Casi d'uso correlati</span><span class="sxs-lookup"><span data-stu-id="08512-109">Related use cases</span></span>

<span data-ttu-id="08512-110">Prendere in considerazione questo scenario per i casi d'uso seguenti:</span><span class="sxs-lookup"><span data-stu-id="08512-110">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="08512-111">Visualizzare il menu da asporto di un ristorante e ordinare cibo</span><span class="sxs-lookup"><span data-stu-id="08512-111">View restaurant take-out menu and order food</span></span>
* <span data-ttu-id="08512-112">Controllare la disponibilità in hotel e prenotare una camera</span><span class="sxs-lookup"><span data-stu-id="08512-112">Check hotel availability and reserve a room</span></span>
* <span data-ttu-id="08512-113">Cercare le foto disponibili e ordinare stampe</span><span class="sxs-lookup"><span data-stu-id="08512-113">Search available photos and order prints</span></span>

## <a name="architecture"></a><span data-ttu-id="08512-114">Architettura</span><span class="sxs-lookup"><span data-stu-id="08512-114">Architecture</span></span>

![Panoramica dell'architettura dei componenti di Azure coinvolti in un chatbot di conversazione][architecture]

<span data-ttu-id="08512-116">Questo scenario include un bot di conversazione che svolge le funzioni di un receptionist di un hotel.</span><span class="sxs-lookup"><span data-stu-id="08512-116">This scenario covers a conversational bot that functions as a concierge for a hotel.</span></span> <span data-ttu-id="08512-117">Il flusso dei dati nello scenario avviene come segue:</span><span class="sxs-lookup"><span data-stu-id="08512-117">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="08512-118">Il cliente accede al chatbot con un'app Web o per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="08512-118">The customer accesses the chatbot with a mobile or web app.</span></span>
2. <span data-ttu-id="08512-119">L'utente viene autenticato con Azure Active Directory B2C (Business to Consumer).</span><span class="sxs-lookup"><span data-stu-id="08512-119">Using Azure Active Directory B2C (Business 2 Customer), the user is authenticated.</span></span>
3. <span data-ttu-id="08512-120">Interagendo con il servizio Bot, l'utente richiede informazioni sulla disponibilità in hotel.</span><span class="sxs-lookup"><span data-stu-id="08512-120">Interacting with the Bot Service, the user requests information about hotel availability.</span></span>
4. <span data-ttu-id="08512-121">Servizi cognitivi elabora la richiesta in linguaggio naturale per comprendere la comunicazione del cliente.</span><span class="sxs-lookup"><span data-stu-id="08512-121">Cognitive Services processes the natural language request to understand the customer communication.</span></span>
5. <span data-ttu-id="08512-122">Quando l'utente è soddisfatto del risultato, il bot aggiunge o aggiorna la prenotazione del cliente in un database SQL.</span><span class="sxs-lookup"><span data-stu-id="08512-122">After the user is happy with the results, the bot adds or updates the customer’s reservation in a SQL Database.</span></span>
6. <span data-ttu-id="08512-123">Application Insights raccoglie dati di telemetria in fase di esecuzione per offrire al team DevOps informazioni sulle prestazioni e l'utilizzo del bot.</span><span class="sxs-lookup"><span data-stu-id="08512-123">Application Insights gathers runtime telemetry throughout the process to help the DevOps team with bot performance and usage.</span></span>

### <a name="components"></a><span data-ttu-id="08512-124">Componenti</span><span class="sxs-lookup"><span data-stu-id="08512-124">Components</span></span>

* <span data-ttu-id="08512-125">[Azure Active Directory ][aad-docs] è il servizio directory e di gestione delle identità multi-tenant basato sul cloud di Microsoft.</span><span class="sxs-lookup"><span data-stu-id="08512-125">[Azure Active Directory][aad-docs] is Microsoft’s multi-tenant cloud-based directory and identity management service.</span></span> <span data-ttu-id="08512-126">Azure AD supporta un connettore B2C che consente di identificare gli utenti con ID esterni come Google, Facebook o un account Microsoft.</span><span class="sxs-lookup"><span data-stu-id="08512-126">Azure AD supports a B2C connector allowing you to identify individuals using external IDs such as Google, Facebook, or a Microsoft Account.</span></span>
* <span data-ttu-id="08512-127">Il [servizio app][appservice-docs] consente di creare e ospitare applicazioni Web nel linguaggio di programmazione preferito senza gestire l'infrastruttura.</span><span class="sxs-lookup"><span data-stu-id="08512-127">[App Service][appservice-docs] enables you to build and host web applications in the programming language of your choice without managing infrastructure.</span></span>
* <span data-ttu-id="08512-128">Il [servizio Bot][botservice-docs] offre gli strumenti per creare, testare, distribuire e gestire bot intelligenti.</span><span class="sxs-lookup"><span data-stu-id="08512-128">[Bot Service][botservice-docs] provides tools to build, test, deploy, and manage intelligent bots.</span></span>
* <span data-ttu-id="08512-129">[Servizi cognitivi][cognitive-docs] consente di usare algoritmi intelligenti per vedere, ascoltare, parlare, comprendere e interpretare le esigenze degli utenti tramite i metodi di comunicazione naturali.</span><span class="sxs-lookup"><span data-stu-id="08512-129">[Cognitive Services][cognitive-docs] lets you use intelligent algorithms to see, hear, speak, understand and interpret your user needs through natural methods of communication.</span></span>
* <span data-ttu-id="08512-130">Il [database SQL][sqldatabase-docs] è un servizio di database cloud relazionale completamente gestito compatibile con il motore di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="08512-130">[SQL Database][sqldatabase-docs] is a fully managed relational cloud database service that provides SQL Server engine compatibility.</span></span>
* <span data-ttu-id="08512-131">[Application Insights][appinsights-docs] è un servizio di gestione delle prestazioni applicative (APM, Application Performance Management) estendibile che consente di monitorare le prestazioni di applicazioni come il chatbot.</span><span class="sxs-lookup"><span data-stu-id="08512-131">[Application Insights][appinsights-docs] is an extensible Application Performance Management (APM) service that lets you monitor the performance of applications, such as your chatbot.</span></span>

### <a name="alternatives"></a><span data-ttu-id="08512-132">Alternative</span><span class="sxs-lookup"><span data-stu-id="08512-132">Alternatives</span></span>

* <span data-ttu-id="08512-133">[Microsoft Speech API][speech-api] consente di modificare le modalità di interazione dei clienti con il bot.</span><span class="sxs-lookup"><span data-stu-id="08512-133">[Microsoft Speech API][speech-api] can be used to change how customers interface with your bot.</span></span>
* <span data-ttu-id="08512-134">[QnA Maker][qna-maker] consente di aggiungere rapidamente informazioni al bot da contenuto semistrutturato come un documento di domande frequenti.</span><span class="sxs-lookup"><span data-stu-id="08512-134">[QnA Maker][qna-maker] can be used as to quickly add knowledge to your bot from semi-structured content like an FAQ.</span></span>
* <span data-ttu-id="08512-135">[Traduzione testuale][translator] è un servizio che può essere preso in considerazione per aggiungere facilmente il supporto multilingue al bot.</span><span class="sxs-lookup"><span data-stu-id="08512-135">[Translator Text][translator] is a service that you might consider to easily add multi-lingual support to your bot.</span></span>

## <a name="considerations"></a><span data-ttu-id="08512-136">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="08512-136">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="08512-137">Disponibilità</span><span class="sxs-lookup"><span data-stu-id="08512-137">Availability</span></span>

<span data-ttu-id="08512-138">Questo scenario usa il database SQL di Azure per archiviare le prenotazioni dei clienti.</span><span class="sxs-lookup"><span data-stu-id="08512-138">This scenario uses Azure SQL Database for storing customer reservations.</span></span> <span data-ttu-id="08512-139">Il servizio database SQL include database con ridondanza della zona, gruppi di failover e replica geografica.</span><span class="sxs-lookup"><span data-stu-id="08512-139">SQL Database includes zone redundant databases, failover groups, and geo-replication.</span></span> <span data-ttu-id="08512-140">Per altre informazioni, vedere la sezione relativa alle [funzionalità per la disponibilità del database SQL di Azure][sqlavailability-docs].</span><span class="sxs-lookup"><span data-stu-id="08512-140">For more information, see [Azure SQL Database availability capabilities][sqlavailability-docs].</span></span>

<span data-ttu-id="08512-141">Per altri argomenti relativi alla disponibilità, vedere l'[elenco di controllo per la disponibilità][availability] in Centro architetture Azure.</span><span class="sxs-lookup"><span data-stu-id="08512-141">For other availability topics, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="08512-142">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="08512-142">Scalability</span></span>

<span data-ttu-id="08512-143">Questo scenario usa il servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="08512-143">This scenario uses Azure App Service.</span></span> <span data-ttu-id="08512-144">Con il servizio app, è possibile ridimensionare automaticamente il numero di istanze in cui viene eseguito il bot.</span><span class="sxs-lookup"><span data-stu-id="08512-144">With App Service, you can automatically scale the number of instances that run your bot.</span></span> <span data-ttu-id="08512-145">Questa funzionalità consente di far fronte alla domanda dei clienti per l'applicazione Web e il chatbot.</span><span class="sxs-lookup"><span data-stu-id="08512-145">This functionality lets you keep up with customer demand for your web application and chatbot.</span></span> <span data-ttu-id="08512-146">Per altre informazioni sul ridimensionamento automatico, vedere le [procedure consigliate per la scalabilità automatica][autoscaling] in Centro architetture.</span><span class="sxs-lookup"><span data-stu-id="08512-146">For more information on autoscale, see [Autoscaling best practices][autoscaling] in the architecture center.</span></span>

<span data-ttu-id="08512-147">Per altri argomenti relativi alla scalabilità, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.</span><span class="sxs-lookup"><span data-stu-id="08512-147">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="08512-148">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="08512-148">Security</span></span>

<span data-ttu-id="08512-149">Per l'autenticazione degli utenti, questo scenario usa Azure Active Directory B2C (Business to Consumer).</span><span class="sxs-lookup"><span data-stu-id="08512-149">This scenario uses Azure Active Directory B2C (Business 2 Consumer) to authenticate users.</span></span> <span data-ttu-id="08512-150">Con AAD B2C, il chatbot non archivia credenziali o informazioni riservate degli account dei clienti.</span><span class="sxs-lookup"><span data-stu-id="08512-150">With AAD B2C, your chatbot doesn't store any sensitive customer account information or credentials.</span></span> <span data-ttu-id="08512-151">Per altre informazioni, vedere la [panoramica di Azure Active Directory B2C][aadb2c-docs].</span><span class="sxs-lookup"><span data-stu-id="08512-151">For more information, see [Azure Active Directory B2C overview][aadb2c-docs].</span></span>

<span data-ttu-id="08512-152">Alle informazioni archiviate nel database SQL di Azure viene applicata la crittografia dei dati inattivi con Transparent Data Encryption (TDE).</span><span class="sxs-lookup"><span data-stu-id="08512-152">Information stored in Azure SQL Database is encrypted at rest with transparent data encryption (TDE).</span></span> <span data-ttu-id="08512-153">Il database SQL offre anche Always Encrypted, che crittografa i dati durante l'esecuzione di query e l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="08512-153">SQL Database also offers Always Encrypted which encrypts data during querying and processing.</span></span> <span data-ttu-id="08512-154">Per altre informazioni sulla sicurezza del database SQL, vedere la sezione relativa a [sicurezza e conformità del database SQL di Azure][sqlsecurity-docs].</span><span class="sxs-lookup"><span data-stu-id="08512-154">For more information on SQL Database security, see [Azure SQL Database security and compliance][sqlsecurity-docs].</span></span>

<span data-ttu-id="08512-155">Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].</span><span class="sxs-lookup"><span data-stu-id="08512-155">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="08512-156">Resilienza</span><span class="sxs-lookup"><span data-stu-id="08512-156">Resiliency</span></span>

<span data-ttu-id="08512-157">Questo scenario usa il database SQL di Azure per archiviare le prenotazioni dei clienti.</span><span class="sxs-lookup"><span data-stu-id="08512-157">This scenario uses Azure SQL Database for storing customer reservations.</span></span> <span data-ttu-id="08512-158">Il servizio database SQL include database con ridondanza della zona, gruppi di failover, replica geografica e backup automatici.</span><span class="sxs-lookup"><span data-stu-id="08512-158">SQL Database includes zone redundant databases, failover groups, geo-replication, and automatic backups.</span></span> <span data-ttu-id="08512-159">Queste funzionalità consentono all'applicazione di rimanere in esecuzione in caso di interruzioni o eventi di manutenzione.</span><span class="sxs-lookup"><span data-stu-id="08512-159">These features allow your application to continue running in the event of a maintenance event or outage.</span></span> <span data-ttu-id="08512-160">Per altre informazioni, vedere la sezione relativa alle [funzionalità per la disponibilità del database SQL di Azure][sqlavailability-docs].</span><span class="sxs-lookup"><span data-stu-id="08512-160">For more information, see [Azure SQL Database availability capabilities][sqlavailability-docs].</span></span>

<span data-ttu-id="08512-161">Per monitorare l'integrità dell'applicazione, questo scenario usa Application Insights.</span><span class="sxs-lookup"><span data-stu-id="08512-161">To monitor the health of your application, this scenario uses Application Insights.</span></span> <span data-ttu-id="08512-162">Con Application Insights, è possibile generare avvisi e rispondere a problemi di prestazioni che influirebbero sull'esperienza dei clienti e sulla disponibilità del chatbot.</span><span class="sxs-lookup"><span data-stu-id="08512-162">With Application Insights, you can generate alerts and respond to performance issues that would impact the customer experience and availability of the chatbot.</span></span> <span data-ttu-id="08512-163">Per altre informazioni, vedere [Informazioni su Application Insights][appinsights-docs].</span><span class="sxs-lookup"><span data-stu-id="08512-163">For more information, see [What is Application Insights?][appinsights-docs]</span></span>

<span data-ttu-id="08512-164">Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].</span><span class="sxs-lookup"><span data-stu-id="08512-164">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="08512-165">Distribuire lo scenario</span><span class="sxs-lookup"><span data-stu-id="08512-165">Deploy the scenario</span></span>

<span data-ttu-id="08512-166">Questo scenario è suddiviso in tre componenti per consentire l'esplorazione delle aree di maggiore interesse:</span><span class="sxs-lookup"><span data-stu-id="08512-166">This scenario is divided into three components for you to explore areas that you are most focused on:</span></span>

* <span data-ttu-id="08512-167">[Componenti dell'infrastruttura](#deploy-infrastructure-components).</span><span class="sxs-lookup"><span data-stu-id="08512-167">[Infrastructure components](#deploy-infrastructure-components).</span></span> <span data-ttu-id="08512-168">Usare un modello di Azure Resource Manager per distribuire i componenti dell'infrastruttura di base di un servizio app, dell'app Web, di Application Insights, dell'account di archiviazione, di SQL Server e del database SQL.</span><span class="sxs-lookup"><span data-stu-id="08512-168">Use an Azure Resource Manger template to deploy the core infrastructure components of an App Service, Web App, Application Insights, Storage account, and SQL Server and database.</span></span>
* <span data-ttu-id="08512-169">[Chatbot per l'app Web](#deploy-web-app-chatbot).</span><span class="sxs-lookup"><span data-stu-id="08512-169">[Web App Chatbot](#deploy-web-app-chatbot).</span></span> <span data-ttu-id="08512-170">Usare l'interfaccia della riga di comando di Azure per distribuire un bot con il servizio Bot e l'app LUIS (Language Understanding Intelligent Service).</span><span class="sxs-lookup"><span data-stu-id="08512-170">Use the Azure CLI to deploy a bot with the Bot Service and Language Understanding and Intelligent Services (LUIS) app.</span></span>
* <span data-ttu-id="08512-171">[Applicazione del chatbot C# di esempio](#deploy-chatbot-c-application-code).</span><span class="sxs-lookup"><span data-stu-id="08512-171">[Sample C# chatbot application](#deploy-chatbot-c-application-code).</span></span> <span data-ttu-id="08512-172">Usare Visual Studio per esaminare il codice dell'applicazione C# di esempio per prenotazioni di hotel ed eseguire la distribuzione in un bot in Azure.</span><span class="sxs-lookup"><span data-stu-id="08512-172">Use Visual Studio to review the sample hotel reservation C# application code and deploy to a bot in Azure.</span></span>

<span data-ttu-id="08512-173">**Prerequisiti.**</span><span class="sxs-lookup"><span data-stu-id="08512-173">**Prerequisites.**</span></span> <span data-ttu-id="08512-174">È necessario un account Azure esistente.</span><span class="sxs-lookup"><span data-stu-id="08512-174">You must have an existing Azure account.</span></span> <span data-ttu-id="08512-175">Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="08512-175">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

### <a name="deploy-infrastructure-components"></a><span data-ttu-id="08512-176">Distribuire i componenti dell'infrastruttura</span><span class="sxs-lookup"><span data-stu-id="08512-176">Deploy infrastructure components</span></span>

<span data-ttu-id="08512-177">Per distribuire i componenti dell'infrastruttura con un modello di Azure Resource Manager, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="08512-177">To deploy the infrastructure components with an Azure Resource Manager template, perform the following steps.</span></span>

1. <span data-ttu-id="08512-178">Fare clic sul pulsante **Distribuisci in Azure**:</span><span class="sxs-lookup"><span data-stu-id="08512-178">Click the **Deploy to Azure** button:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fcommerce-chatbot.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="08512-179">Attendere l'apertura della distribuzione del modello nel portale di Azure e quindi completare la procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="08512-179">Wait for the template deployment to open in the Azure portal, then complete the following steps:</span></span>
   * <span data-ttu-id="08512-180">Scegliere **Crea nuovo** per creare un nuovo gruppo di risorse e quindi specificare un nome, ad esempio *myCommerceChatBotInfrastructure*, nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="08512-180">Choose to **Create new** resource group, then provide a name such as *myCommerceChatBotInfrastructure* in the text box.</span></span>
   * <span data-ttu-id="08512-181">Selezionare un'area nella casella a discesa **Località**.</span><span class="sxs-lookup"><span data-stu-id="08512-181">Select a region from the **Location** drop-down box.</span></span>
   * <span data-ttu-id="08512-182">Specificare un nome utente e una password sicura per l'account amministratore di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="08512-182">Provide a username and secure password for the SQL Server administrator account.</span></span>
   * <span data-ttu-id="08512-183">Esaminare le condizioni e quindi selezionare **Accetto le condizioni riportate sopra**.</span><span class="sxs-lookup"><span data-stu-id="08512-183">Review the terms and conditions, then check **I agree to the terms and conditions stated above**.</span></span>
   * <span data-ttu-id="08512-184">Selezionare il pulsante **Acquista**.</span><span class="sxs-lookup"><span data-stu-id="08512-184">Select the **Purchase** button.</span></span>

<span data-ttu-id="08512-185">Il completamento della distribuzione richiede alcuni minuti.</span><span class="sxs-lookup"><span data-stu-id="08512-185">It takes a few minutes for the deployment to complete.</span></span>

### <a name="deploy-web-app-chatbot"></a><span data-ttu-id="08512-186">Distribuire il chatbot per l'app Web</span><span class="sxs-lookup"><span data-stu-id="08512-186">Deploy Web App chatbot</span></span>

<span data-ttu-id="08512-187">Per creare il chatbot, usare l'interfaccia della riga di comando di Azure.</span><span class="sxs-lookup"><span data-stu-id="08512-187">To create the chatbot, use the Azure CLI.</span></span> <span data-ttu-id="08512-188">L'esempio seguente installa l'estensione dell'interfaccia della riga di comando per il servizio Bot, crea un gruppo di risorse e quindi distribuisce un bot che usa Application Insights.</span><span class="sxs-lookup"><span data-stu-id="08512-188">The following example installs the CLI extension for Bot Service, creates a resource group, then deploys a bot that uses Application Insights.</span></span> <span data-ttu-id="08512-189">Quando richiesto, eseguire l'autenticazione dell'account Microsoft e consentire al bot di registrarsi nel servizio Bot e nell'app LUIS (Language Understanding Intelligent Service).</span><span class="sxs-lookup"><span data-stu-id="08512-189">When prompted, authenticate your Microsoft account and allow the bot to register itself with the Bot Service and Language Understanding and Intelligent Services (LUIS) app.</span></span>

```azurecli-interactive
# Install the Azure CLI extension for the Bot Service
az extension add --name botservice --yes

# Create a resource group
az group create --name myCommerceChatbot --location eastus

# Create a Web App Chatbot that uses Application Insights
az bot create \
    --resource-group myCommerceChatbot \
    --name commerceChatbot \
    --location eastus \
    --kind webapp \
    --sku S1 \
    --insights eastus
```

### <a name="deploy-chatbot-c-application-code"></a><span data-ttu-id="08512-190">Distribuire il codice dell'applicazione C# del chatbot</span><span class="sxs-lookup"><span data-stu-id="08512-190">Deploy chatbot C# application code</span></span>

<span data-ttu-id="08512-191">In GitHub è disponibile un'applicazione C# di esempio:</span><span class="sxs-lookup"><span data-stu-id="08512-191">A sample C# application is available on GitHub:</span></span> 

* [<span data-ttu-id="08512-192">Esempio C# Commerce Bot</span><span class="sxs-lookup"><span data-stu-id="08512-192">Commerce Bot C# sample</span></span>](https://github.com/Microsoft/AzureBotServices-scenarios/tree/master/CSharp/Commerce/src)

<span data-ttu-id="08512-193">L'applicazione di esempio include i componenti di autenticazione di Azure Active Directory e l'integrazione con il componente LUIS (Language Understanding Intelligent Service) di Servizi cognitivi.</span><span class="sxs-lookup"><span data-stu-id="08512-193">The sample application includes the Azure Active Directory authentication components and integration with the Language Understanding and Intelligent Services (LUIS) component of Cognitive Services.</span></span> <span data-ttu-id="08512-194">L'applicazione richiede Visual Studio per la creazione e la distribuzione dello scenario.</span><span class="sxs-lookup"><span data-stu-id="08512-194">The application requires Visual Studio to build and deploy the scenario.</span></span> <span data-ttu-id="08512-195">Altre informazioni sulla configurazione di AAD B2C e dell'app LUIS sono disponibili nella documentazione del repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="08512-195">Additional information on configuring AAD B2C and the LUIS app can be found in the GitHub repo documentation.</span></span>

## <a name="pricing"></a><span data-ttu-id="08512-196">Prezzi</span><span class="sxs-lookup"><span data-stu-id="08512-196">Pricing</span></span>

<span data-ttu-id="08512-197">Per esaminare il costo di esecuzione dello scenario, nel calcolatore dei costi sono preconfigurati tutti i servizi.</span><span class="sxs-lookup"><span data-stu-id="08512-197">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="08512-198">Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base</span><span class="sxs-lookup"><span data-stu-id="08512-198">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="08512-199">Sono stati definiti tre profili di costo di esempio in base alla quantità di messaggi che si prevede venga elaborata dal chatbot.</span><span class="sxs-lookup"><span data-stu-id="08512-199">We have provided three sample cost profiles based on the amount of messages you expect your chatbot to process:</span></span>

* <span data-ttu-id="08512-200">[Small][small-pricing]: correlato all'elaborazione di meno di 10.000 messaggi al mese.</span><span class="sxs-lookup"><span data-stu-id="08512-200">[Small][small-pricing]: this correlates to processing < 10,000 messages per month.</span></span>
* <span data-ttu-id="08512-201">[Medium][medium-pricing]: correlato all'elaborazione di meno di 500.000 messaggi al mese.</span><span class="sxs-lookup"><span data-stu-id="08512-201">[Medium][medium-pricing]: this correlates to processing < 500,000 messages per month.</span></span>
* <span data-ttu-id="08512-202">[Large][large-pricing]: correlato all'elaborazione di meno di 10 milioni di messaggi al mese.</span><span class="sxs-lookup"><span data-stu-id="08512-202">[Large][large-pricing]: this correlates to processing < 10 million messages per month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="08512-203">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="08512-203">Related Resources</span></span>

<span data-ttu-id="08512-204">Per un set di esercitazioni guidate sull'uso del servizio Azure Bot, vedere il [nodo delle esercitazioni][botservice-docs] nella relativa documentazione.</span><span class="sxs-lookup"><span data-stu-id="08512-204">For a set of guided tutorials on leveraging the Azure Bot Service, see the [tutorial node][botservice-docs] of the documentation.</span></span>

<!-- links -->
[aadb2c-docs]: /azure/active-directory-b2c/active-directory-b2c-overview
[aad-docs]: /azure/active-directory/
[appinsights-docs]: /azure/application-insights/app-insights-overview
[appservice-docs]: /azure/app-service/
[architecture]: ./media/commerce-chatbot/architecture-commerce-chatbot.png
[autoscaling]: ../../best-practices/auto-scaling.md
[availability]: ../../checklist/availability.md
[botservice-docs]: /azure/bot-service/
[cognitive-docs]: /azure/cognitive-services/
[resiliency]: ../../resiliency/index.md
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[scalability]: ../../checklist/scalability.md
[sqlavailability-docs]: /azure/sql-database/sql-database-technical-overview#availability-capabilities
[sqldatabase-docs]: /azure/sql-database/
[sqlsecurity-docs]: /azure/sql-database/sql-database-technical-overview#advanced-security-and-compliance
[qna-maker]: /azure/cognitive-services/QnAMaker/Overview/overview
[speech-api]: /azure/cognitive-services/speech/home
[translator]: /azure/cognitive-services/translator/translator-info-overview

[small-pricing]: https://azure.com/e/dce05b6184904c50b38e1a8654f726b6
[medium-pricing]: https://azure.com/e/304d17106afc480dbc414f9726078a03
[large-pricing]: https://azure.com/e/8319dd5e5e3d4f118f9029e32a80e887