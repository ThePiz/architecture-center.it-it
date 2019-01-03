---
title: Progettare una pipeline CI/CD con Azure DevOps
titleSuffix: Azure Example Scenarios
description: Compilare e rilasciare un'app .NET per App Web di Azure usando Azure DevOps.
author: christianreddington
ms.date: 12/06/2018
ms.custom:
- fasttrack
- seodec18
ms.openlocfilehash: ae2dddd7567c6b69f936b3b9c9339313389e3bf6
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643799"
---
# <a name="design-a-cicd-pipeline-using-azure-devops"></a><span data-ttu-id="d77da-103">Progettare una pipeline CI/CD con Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="d77da-103">Design a CI/CD pipeline using Azure DevOps</span></span>

<span data-ttu-id="d77da-104">Questo scenario fornisce materiale sussidiario di architettura e progettazione per la compilazione di una pipeline di integrazione continua (CI) e distribuzione continua (CD).</span><span class="sxs-lookup"><span data-stu-id="d77da-104">This scenario provides architecture and design guidance for building a continuous integration (CI) and continuous deployment (CD) pipeline.</span></span> <span data-ttu-id="d77da-105">In questo esempio, la pipeline CI/CD consente di distribuire un'applicazione web .NET a due livelli nel servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="d77da-105">In this example, the CI/CD pipeline deploys a two-tier .NET web application to the Azure App Service.</span></span>

<span data-ttu-id="d77da-106">La migrazione in processi CI/CD moderni offre numerosi vantaggi per le compilazioni, le distribuzioni, i test e il monitoraggio delle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="d77da-106">Migrating to modern CI/CD processes provides many benefits for application builds, deployments, testing, and monitoring.</span></span> <span data-ttu-id="d77da-107">Usando Azure DevOps con altri servizi come il Servizio app, le organizzazioni possono concentrarsi sullo sviluppo delle proprie app invece che sulla gestione dell'infrastruttura di supporto.</span><span class="sxs-lookup"><span data-stu-id="d77da-107">By utilizing Azure DevOps along with other services such as App Service, organizations can focus on the development of their apps rather than the management of the supporting infrastructure.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="d77da-108">Casi d'uso pertinenti</span><span class="sxs-lookup"><span data-stu-id="d77da-108">Relevant use cases</span></span>

<span data-ttu-id="d77da-109">Prendere in considerazione i processi di CI/CD e Azure DevOps per:</span><span class="sxs-lookup"><span data-stu-id="d77da-109">Consider Azure DevOps and CI/CD processes for:</span></span>

- <span data-ttu-id="d77da-110">Accelerazione dello sviluppo di applicazioni e dei cicli di vita dello sviluppo</span><span class="sxs-lookup"><span data-stu-id="d77da-110">Accelerating application development and development life cycles</span></span>
- <span data-ttu-id="d77da-111">Integrazione di qualità e coerenza in un processo di compilazione e rilascio automatizzato</span><span class="sxs-lookup"><span data-stu-id="d77da-111">Building quality and consistency into an automated build and release process</span></span>
- <span data-ttu-id="d77da-112">Aumento della stabilità e del tempo di attività dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="d77da-112">Increasing application stability and uptime</span></span>

## <a name="architecture"></a><span data-ttu-id="d77da-113">Architettura</span><span class="sxs-lookup"><span data-stu-id="d77da-113">Architecture</span></span>

![Diagramma dell'architettura dei componenti di Azure coinvolti in uno scenario DevOps con Azure DevOps e Servizio app di Azure][architecture]

<span data-ttu-id="d77da-115">Il flusso dei dati nello scenario avviene come segue:</span><span class="sxs-lookup"><span data-stu-id="d77da-115">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="d77da-116">Uno sviluppatore modifica il codice sorgente dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d77da-116">A developer changes application source code.</span></span>
2. <span data-ttu-id="d77da-117">Si esegue il commit del codice dell'applicazione con il file web.config nel repository del codice sorgente in Azure Repos.</span><span class="sxs-lookup"><span data-stu-id="d77da-117">Application code including the web.config file is committed to the source code repository in Azure Repos.</span></span>
3. <span data-ttu-id="d77da-118">L'integrazione continua attiva la compilazione dell'applicazione e gli unit test tramite Azure Test Plans.</span><span class="sxs-lookup"><span data-stu-id="d77da-118">Continuous integration triggers application build and unit tests using Azure Test Plans.</span></span>
4. <span data-ttu-id="d77da-119">La distribuzione continua in Azure Pipelines attiva una distribuzione automatizzata degli artefatti dell'applicazione con *valori di configurazione specifici dell'ambiente*.</span><span class="sxs-lookup"><span data-stu-id="d77da-119">Continuous deployment within Azure Pipelines triggers an automated deployment of application artifacts *with environment-specific configuration values*.</span></span>
5. <span data-ttu-id="d77da-120">Gli artefatti vengono distribuiti nel Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="d77da-120">The artifacts are deployed to Azure App Service.</span></span>
6. <span data-ttu-id="d77da-121">Azure Application Insights raccoglie e analizza i dati relativi a integrità, prestazioni e utilizzo.</span><span class="sxs-lookup"><span data-stu-id="d77da-121">Azure Application Insights collects and analyzes health, performance, and usage data.</span></span>
7. <span data-ttu-id="d77da-122">Gli sviluppatori monitorano e gestiscono informazioni relative a integrità, prestazioni e utilizzo.</span><span class="sxs-lookup"><span data-stu-id="d77da-122">Developers monitor and mange health, performance, and usage information.</span></span>
8. <span data-ttu-id="d77da-123">Le informazioni di backlog sono usate per classificare le nuove funzionalità e le correzioni di bug in ordine di priorità mediante Azure Boards.</span><span class="sxs-lookup"><span data-stu-id="d77da-123">Backlog information is used to prioritize new features and bug fixes using Azure Boards.</span></span>

### <a name="components"></a><span data-ttu-id="d77da-124">Componenti</span><span class="sxs-lookup"><span data-stu-id="d77da-124">Components</span></span>

- <span data-ttu-id="d77da-125">[Azure DevOps][vsts] è un servizio per gestire il ciclo di vita dello sviluppo end-to-end, dalla pianificazione e dalla gestione dei progetti alla gestione del codice per proseguire con la compilazione e il rilascio.</span><span class="sxs-lookup"><span data-stu-id="d77da-125">[Azure DevOps][vsts] is a service for managing your development life cycle end-to-end &mdash; from planning and project management, to code management, and continuing to build and release.</span></span>

- <span data-ttu-id="d77da-126">[App Web di Azure][web-apps] è un servizio PaaS per l'hosting di applicazioni Web, API REST e back-end per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="d77da-126">[Azure Web Apps][web-apps] is a PaaS service for hosting web applications, REST APIs, and mobile back ends.</span></span> <span data-ttu-id="d77da-127">Questo articolo è incentrato su .NET, ma sono supportate diverse altre opzioni di piattaforma di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d77da-127">While this article focuses on .NET, there are several additional development platform options supported.</span></span>

- <span data-ttu-id="d77da-128">[Application Insights][application-insights] è un servizio proprietario estendibile di gestione delle prestazioni applicative (APM, Application Performance Management) per sviluppatori Web in più piattaforme.</span><span class="sxs-lookup"><span data-stu-id="d77da-128">[Application Insights][application-insights] is a first-party, extensible Application Performance Management (APM) service for web developers on multiple platforms.</span></span>

### <a name="alternatives"></a><span data-ttu-id="d77da-129">Alternative</span><span class="sxs-lookup"><span data-stu-id="d77da-129">Alternatives</span></span>

<span data-ttu-id="d77da-130">Questo articolo è incentrato su Azure DevOps, ma è possibile anche usare [Azure DevOps Server][azure-devops-server] (precedentemente noto come Microsoft Team Foundation Server) come strumento sostitutivo locale.</span><span class="sxs-lookup"><span data-stu-id="d77da-130">While this article focuses on Azure DevOps, [Azure DevOps Server][azure-devops-server] (previously known as Team Foundation Server) could be used as an on-premises substitute.</span></span> <span data-ttu-id="d77da-131">In alternativa, è anche possibile usare un set di tecnologie per una pipeline di sviluppo open source con [Jenkins][jenkins-on-azure].</span><span class="sxs-lookup"><span data-stu-id="d77da-131">Alternatively, you could also use a set of technologies for an open-source development pipeline using [Jenkins][jenkins-on-azure].</span></span>

<span data-ttu-id="d77da-132">Per un'infrastruttura distribuita come codice, nel progetto Azure DevOps sono stati usati [modelli di Resource Manager][arm-templates], ma si possono prendere in considerazione altre tecnologie di gestione come [Terraform][terraform] o [Chef][chef].</span><span class="sxs-lookup"><span data-stu-id="d77da-132">From an infrastructure-as-code perspective, [Resource Manager templates][arm-templates] were used as part of the Azure DevOps project, but you could consider other management technologies such as [Terraform][terraform] or [Chef][chef].</span></span> <span data-ttu-id="d77da-133">Se si preferisce una distribuzione basata su IaaS (infrastruttura distribuita come servizio) ed è necessaria la gestione della configurazione, si può prendere in considerazione [Automation State Configuration per Azure][desired-state-configuration], [Ansible][ansible] o [Chef][chef].</span><span class="sxs-lookup"><span data-stu-id="d77da-133">If you prefer an infrastructure-as-a-service (IaaS)-based deployment and require configuration management, you could consider either [Azure Automation State Configuration][desired-state-configuration], [Ansible][ansible], or [Chef][chef].</span></span>

<span data-ttu-id="d77da-134">È possibile prendere in considerazione queste alternative all'hosting in App Web di Azure:</span><span class="sxs-lookup"><span data-stu-id="d77da-134">You could consider these alternatives to hosting in Azure Web Apps:</span></span>

- <span data-ttu-id="d77da-135">[Macchine virtuali di Azure][compare-vm-hosting] gestisce i carichi di lavoro che richiedono un livello elevato di controllo o dipendono da servizi e componenti del sistema operativo non consentiti con App Web (ad esempio, COM o GAC di Windows).</span><span class="sxs-lookup"><span data-stu-id="d77da-135">[Azure Virtual Machines][compare-vm-hosting] handles workloads that require a high degree of control, or depend on OS components and services that are not possible with Web Apps (for example, the Windows GAC, or COM).</span></span>

- <span data-ttu-id="d77da-136">[Service Fabric][service-fabric] è un'opzione valida se l'architettura dei carichi di lavoro è incentrata su componenti distribuiti per cui sono utili la distribuzione e l'esecuzione in un cluster con un livello elevato di controllo.</span><span class="sxs-lookup"><span data-stu-id="d77da-136">[Service Fabric][service-fabric] is a good option if the workload architecture is focused around distributed components that benefit from being deployed and run across a cluster with a high degree of control.</span></span> <span data-ttu-id="d77da-137">È possibile usare Service Fabric anche per l'hosting di contenitori.</span><span class="sxs-lookup"><span data-stu-id="d77da-137">Service Fabric can also be used to host containers.</span></span>

- <span data-ttu-id="d77da-138">[Funzioni di Azure][azure-functions] offre un approccio serverless efficace se l'architettura dei carichi di lavoro è incentrata su componenti distribuiti specifici che richiedono dipendenze minime, nei casi in cui l'esecuzione dei singoli componenti è prevista solo su richiesta (non in modo continuo) e non è necessaria l'orchestrazione dei componenti.</span><span class="sxs-lookup"><span data-stu-id="d77da-138">[Azure Functions][azure-functions] provides an effective serverless approach if the workload architecture is centered around fine grained distributed components, requiring minimal dependencies, where individual components are only required to run on demand (not continuously) and orchestration of components is not required.</span></span>

<span data-ttu-id="d77da-139">Questo [albero delle decisioni per i servizi di calcolo di Azure](/azure/architecture/guide/technology-choices/compute-decision-tree) può essere utile quando si sceglie il percorso appropriato per una migrazione.</span><span class="sxs-lookup"><span data-stu-id="d77da-139">This [decision tree for Azure compute services](/azure/architecture/guide/technology-choices/compute-decision-tree) may help when choosing the right path to take for a migration.</span></span>

## <a name="management-and-security-considerations"></a><span data-ttu-id="d77da-140">Considerazioni relative alla gestione e alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="d77da-140">Management and Security Considerations</span></span>

- <span data-ttu-id="d77da-141">Valutare la possibilità di sfruttare una delle [attività di tokenizzazione][vsts-tokenization] disponibili nel marketplace di VSTS.</span><span class="sxs-lookup"><span data-stu-id="d77da-141">Consider leveraging one of the [tokenization tasks][vsts-tokenization] available in the VSTS marketplace.</span></span>

- <span data-ttu-id="d77da-142">Con le attività di [Azure Key Vault][download-keyvault-secrets] è possibile scaricare segreti da un'istanza di Azure Key Vault alla propria versione.</span><span class="sxs-lookup"><span data-stu-id="d77da-142">[Azure Key Vault][download-keyvault-secrets] tasks can download secrets from an Azure Key Vault into your release.</span></span> <span data-ttu-id="d77da-143">Sarà quindi possibile usare tali segreti come variabili nella definizione di versione, evitando di archiviarli nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="d77da-143">You can then use those secrets as variables in your release definition, which avoids storing them in source control.</span></span>

- <span data-ttu-id="d77da-144">Usare [variabili di versione][vsts-release-variables] nelle definizioni di versione per introdurre modifiche alla configurazione per gli ambienti.</span><span class="sxs-lookup"><span data-stu-id="d77da-144">Use [release variables][vsts-release-variables] in your release definitions to drive configuration changes of your environments.</span></span> <span data-ttu-id="d77da-145">Le variabili di versione possono avere come abito un'intera versione o un determinato ambiente.</span><span class="sxs-lookup"><span data-stu-id="d77da-145">Release variables can be scoped to an entire release or a given environment.</span></span> <span data-ttu-id="d77da-146">Quando si usano le variabili per le informazioni dei segreti, assicurarsi di selezionare l'icona a forma di lucchetto.</span><span class="sxs-lookup"><span data-stu-id="d77da-146">When using variables for secret information, ensure that you select the padlock icon.</span></span>

- <span data-ttu-id="d77da-147">Sarebbe opportuno usare [attività di controllo di distribuzione][vsts-deployment-gates] nella pipeline di versione.</span><span class="sxs-lookup"><span data-stu-id="d77da-147">[Deployment gates][vsts-deployment-gates] should be used in your release pipeline.</span></span> <span data-ttu-id="d77da-148">In questo modo è possibile sfruttare i dati di monitoraggio in associazione a sistemi esterni, ad esempio sistemi di gestione degli eventi imprevisti o altri sistemi personalizzati, per determinare se una versione deve essere alzata di livello.</span><span class="sxs-lookup"><span data-stu-id="d77da-148">This lets you leverage monitoring data in association with external systems (for example, incident management or additional bespoke systems) to determine whether a release should be promoted.</span></span>

- <span data-ttu-id="d77da-149">Quando è necessario l'intervento manuale in una pipeline di versione, usare la funzionalità delle [approvazioni][vsts-approvals].</span><span class="sxs-lookup"><span data-stu-id="d77da-149">Where manual intervention in a release pipeline is required, use the [approvals][vsts-approvals] functionality.</span></span>

- <span data-ttu-id="d77da-150">Valutare la possibilità di usare [Application Insights][application-insights] e altri strumenti di monitoraggio il prima possibile nella pipeline di versione.</span><span class="sxs-lookup"><span data-stu-id="d77da-150">Consider using [Application Insights][application-insights] and additional monitoring tools as early as possible in your release pipeline.</span></span> <span data-ttu-id="d77da-151">Molte organizzazioni iniziano il monitoraggio solo nel loro ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="d77da-151">Many organizations only begin monitoring in their production environment.</span></span> <span data-ttu-id="d77da-152">Monitorando gli altri ambienti, è possibile identificare i bug in fasi precedenti del processo di sviluppo ed evitare problemi nel proprio ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="d77da-152">By monitoring your other environments, you can identify bugs earlier in the development process and avoid issues in your production environment.</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="d77da-153">Distribuire lo scenario</span><span class="sxs-lookup"><span data-stu-id="d77da-153">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="d77da-154">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="d77da-154">Prerequisites</span></span>

- <span data-ttu-id="d77da-155">È necessario un account Azure esistente.</span><span class="sxs-lookup"><span data-stu-id="d77da-155">You must have an existing Azure account.</span></span> <span data-ttu-id="d77da-156">Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="d77da-156">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

- <span data-ttu-id="d77da-157">È necessario iscriversi per ottenere un'organizzazione di Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="d77da-157">You must sign up for an Azure DevOps organization.</span></span> <span data-ttu-id="d77da-158">Per ulteriori informazioni, consultare [Avvio rapido: Creare l'organizzazione][vsts-account-create].</span><span class="sxs-lookup"><span data-stu-id="d77da-158">For more information, see [Quickstart: Create your organization][vsts-account-create].</span></span>

### <a name="walk-through"></a><span data-ttu-id="d77da-159">Procedura dettagliata</span><span class="sxs-lookup"><span data-stu-id="d77da-159">Walk-through</span></span>

<span data-ttu-id="d77da-160">Il [progetto di Azure DevOps](/azure/devops-project/azure-devops-project-github) distribuirà un piano di servizio app, il servizio app e una risorsa di Application Insights e configurerà il progetto di Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="d77da-160">The [Azure DevOps project](/azure/devops-project/azure-devops-project-github) will deploy an App Service Plan, App Service, and an App Insights resource for you, as well as configure the Azure DevOps project for you.</span></span>

<span data-ttu-id="d77da-161">Una volta implementato il progetto di Azure DevOps e completata la compilazione, esaminare le modifiche al codice, gli elementi di lavoro e i risultati dei test associati.</span><span class="sxs-lookup"><span data-stu-id="d77da-161">Once you've deployed the Azure DevOps project and the build is completed, review the associated code changes, work items, and test results.</span></span> <span data-ttu-id="d77da-162">Si rileverà che non vengono visualizzati risultati dei test, perché il codice non contiene test da eseguire.</span><span class="sxs-lookup"><span data-stu-id="d77da-162">You will notice that no test results are displayed, because the code does not contain any tests to run.</span></span>

<span data-ttu-id="d77da-163">Il progetto crea una pipeline di versione e un trigger di distribuzione continua, distribuendo l'applicazione nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d77da-163">The project creates a release pipeline and continuous deployment trigger, deploying our application into the Dev environment.</span></span> <span data-ttu-id="d77da-164">In un processo di distribuzione continua, i rilasci possono estendersi a più ambienti.</span><span class="sxs-lookup"><span data-stu-id="d77da-164">As part of a continuous deployment process, you may see releases that span multiple environments.</span></span> <span data-ttu-id="d77da-165">Un rilascio può riguardare l'infrastruttura (con tecniche come l'infrastruttura distribuita come codice) nonché distribuire i pacchetti dell'applicazione con tutte le attività di post-configurazione.</span><span class="sxs-lookup"><span data-stu-id="d77da-165">A release can span both infrastructure (using techniques such as infrastructure-as-code), and can also deploy the application packages required along with any post-configuration tasks.</span></span>

## <a name="pricing"></a><span data-ttu-id="d77da-166">Prezzi</span><span class="sxs-lookup"><span data-stu-id="d77da-166">Pricing</span></span>

<span data-ttu-id="d77da-167">I costi di Azure DevOps dipendono dal numero di utenti dell'organizzazione che devono accedere, nonché da fattori come il numero di processi di compilazione/rilascio simultanei necessari e il numero di utenti di test.</span><span class="sxs-lookup"><span data-stu-id="d77da-167">Azure DevOps costs depend on the number of users in your organization that require access, along with other factors like the number of concurrent build/releases required and number of test users.</span></span> <span data-ttu-id="d77da-168">Per altre informazioni, vedere [Prezzi per Azure DevOps][vsts-pricing-page].</span><span class="sxs-lookup"><span data-stu-id="d77da-168">For more information, see [Azure DevOps pricing][vsts-pricing-page].</span></span>

<span data-ttu-id="d77da-169">Questo [calcolatore dei prezzi][vsts-pricing-calculator] offre una stima per l'esecuzione di Azure DevOps con 20 utenti.</span><span class="sxs-lookup"><span data-stu-id="d77da-169">This [pricing calculator][vsts-pricing-calculator] provides an estimate for running Azure DevOps with 20 users.</span></span>

<span data-ttu-id="d77da-170">Azure DevOps viene fatturato su base mensile per ogni singolo utente.</span><span class="sxs-lookup"><span data-stu-id="d77da-170">Azure DevOps is billed on a per-user per-month basis.</span></span> <span data-ttu-id="d77da-171">Addebiti aggiuntivi possono essere previsti a seconda delle pipeline simultanee necessarie e di eventuali licenze utente Basic o utenti di test aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="d77da-171">There may be additional charges dependent upon concurrent pipelines needed, in addition to any additional test users or user basic licenses.</span></span>

## <a name="related-resources"></a><span data-ttu-id="d77da-172">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="d77da-172">Related resources</span></span>

<span data-ttu-id="d77da-173">Rivedere le risorse seguenti per altre informazioni relative ai processi di CI/CD e Azure DevOps:</span><span class="sxs-lookup"><span data-stu-id="d77da-173">Review the following resources to learn more about CI/CD and Azure DevOps:</span></span>

- <span data-ttu-id="d77da-174">[Informazioni su DevOps][devops-whatis]</span><span class="sxs-lookup"><span data-stu-id="d77da-174">[What is DevOps?][devops-whatis]</span></span>
- <span data-ttu-id="d77da-175">[DevOps at Microsoft - How we work with Azure DevOps][devops-microsoft] (DevOps in Microsoft - Come viene usato Azure DevOps)</span><span class="sxs-lookup"><span data-stu-id="d77da-175">[DevOps at Microsoft - How we work with Azure DevOps][devops-microsoft]</span></span>
- <span data-ttu-id="d77da-176">[Esercitazioni dettagliate: DevOps con Azure DevOps][devops-with-vsts]</span><span class="sxs-lookup"><span data-stu-id="d77da-176">[Step-by-step Tutorials: DevOps with Azure DevOps][devops-with-vsts]</span></span>
- <span data-ttu-id="d77da-177">[Elenco di controllo DevOps][devops-checklist]</span><span class="sxs-lookup"><span data-stu-id="d77da-177">[Devops Checklist][devops-checklist]</span></span>
- <span data-ttu-id="d77da-178">[Creare una pipeline di CI/CD per .NET con il progetto DevOps di Azure][devops-project-create]</span><span class="sxs-lookup"><span data-stu-id="d77da-178">[Create a CI/CD pipeline for .NET with the Azure DevOps project][devops-project-create]</span></span>

<!-- links -->

[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[architecture]: ./media/architecture-devops-dotnet-webapp.svg
[chef]: /azure/chef/
[design-patterns-availability]: /azure/architecture/patterns/category/availability
[design-patterns-resiliency]: /azure/architecture/patterns/category/resiliency
[design-patterns-scalability]: /azure/architecture/patterns/category/performance-scalability
[design-patterns-security]: /azure/architecture/patterns/category/security
[desired-state-configuration]: /azure/automation/automation-dsc-overview
[devops-microsoft]: /azure/devops/devops-at-microsoft/
[devops-with-vsts]: https://almvm.azurewebsites.net/labs/vsts/
[devops-checklist]: /azure/architecture/checklist/dev-ops
[application-insights]: https://azure.microsoft.com/services/application-insights/
[cloud-based-load-testing]: https://visualstudio.microsoft.com/team-services/cloud-load-testing/
[cloud-based-load-testing-on-premises]: /vsts/test/load-test/clt-with-private-machines?view=vsts
[jenkins-on-azure]: /azure/jenkins/
[devops-whatis]: /azure/devops/what-is-devops
[download-keyvault-secrets]: /vsts/pipelines/tasks/deploy/azure-key-vault?view=vsts
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency-app-service]: /azure/architecture/checklist/resiliency-per-service#app-service
[vsts]: /vsts/?view=vsts#pivot=services
[continuous-integration]: /azure/devops/what-is-continuous-integration
[continuous-delivery]: /azure/devops/what-is-continuous-delivery
[web-apps]: /azure/app-service/app-service-web-overview
[vsts-account-create]: /azure/devops/organizations/accounts/create-organization-msa-or-work-student?view=vsts
[vsts-approvals]: /vsts/pipelines/release/approvals/approvals?view=vsts
[devops-project]: https://portal.azure.com/?feature.customportal=false#create/Microsoft.AzureProject
[vsts-deployment-gates]: /vsts/pipelines/release/approvals/gates?view=vsts
[vsts-pricing-calculator]: https://azure.com/e/498aa024454445a8a352e75724f900b1
[vsts-pricing-page]: https://azure.microsoft.com/pricing/details/visual-studio-team-services/
[vsts-release-variables]: /vsts/pipelines/release/variables?view=vsts&tabs=batch
[vsts-tokenization]: https://marketplace.visualstudio.com/search?term=token&target=VSTS&category=All%20categories&sortBy=Relevance
[azure-key-vault]: /azure/key-vault/key-vault-overview
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[azure-devops-server]: https://visualstudio.microsoft.com/tfs/
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[service-fabric]: /azure/service-fabric/
[azure-functions]: /azure/azure-functions/
[azure-containers]: https://azure.microsoft.com/overview/containers/
[compare-vm-hosting]: /azure/app-service/choose-web-site-cloud-service-vm
[app-insights-cd-monitoring]: /azure/application-insights/app-insights-vsts-continuous-monitoring
[azure-region-pair-bcdr]: /azure/best-practices-availability-paired-regions
[devops-project-create]: /azure/devops-project/azure-devops-project-aspnet-core
[terraform]: /azure/terraform/
