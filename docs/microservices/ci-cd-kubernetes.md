---
title: Creazione di una pipeline CI/CD per microservizi in Kubernetes
description: Descrive una pipeline di integrazione continua/recapito Continuo di esempio per la distribuzione di microservizi di Azure Kubernetes Service (AKS).
author: MikeWasson
ms.date: 04/11/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: e7da8a1b4111fb7856b919b40d033833a4e475e0
ms.sourcegitcommit: d58e6b2b891c9c99e951c59f15fce71addcb96b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59533195"
---
<!-- markdownlint-disable MD040 -->

# <a name="building-a-cicd-pipeline-for-microservices-on-kubernetes"></a><span data-ttu-id="6ef21-103">Creazione di una pipeline CI/CD per microservizi in Kubernetes</span><span class="sxs-lookup"><span data-stu-id="6ef21-103">Building a CI/CD pipeline for microservices on Kubernetes</span></span>

<span data-ttu-id="6ef21-104">Può essere difficile creare un processo di integrazione continua/distribuzione affidabile per un'architettura di microservizi.</span><span class="sxs-lookup"><span data-stu-id="6ef21-104">It can be challenging to create a reliable CI/CD process for a microservices architecture.</span></span> <span data-ttu-id="6ef21-105">I singoli team deve essere in grado di rilasciare i servizi in modo rapido e affidabile, senza interrompere altri team o destabilizzare l'applicazione nel suo complesso.</span><span class="sxs-lookup"><span data-stu-id="6ef21-105">Individual teams must be able to release services quickly and reliably, without disrupting other teams or destabilizing the application as a whole.</span></span>

<span data-ttu-id="6ef21-106">Questo articolo illustra una pipeline di integrazione continua/recapito Continuo di esempio per la distribuzione di microservizi di Azure Kubernetes Service (AKS).</span><span class="sxs-lookup"><span data-stu-id="6ef21-106">This article describes an example CI/CD pipeline for deploying microservices to Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="6ef21-107">Ogni team e progetto è diversa, in modo che non accettano questo articolo come un set di regole categoriche.</span><span class="sxs-lookup"><span data-stu-id="6ef21-107">Every team and project is different, so don't take this article as a set of hard-and-fast rules.</span></span> <span data-ttu-id="6ef21-108">In alternativa, è fatta per essere un punto di partenza per la progettazione di un proprio processo di integrazione continua/recapito Continuo.</span><span class="sxs-lookup"><span data-stu-id="6ef21-108">Instead, it's meant to be a starting point for designing your own CI/CD process.</span></span>

<span data-ttu-id="6ef21-109">La pipeline di esempio descritta di seguito è stata creata per un'implementazione di riferimento di microservizi chiamata l'applicazione di recapito tramite Drone, che sono disponibili nel [GitHub][ri].</span><span class="sxs-lookup"><span data-stu-id="6ef21-109">The example pipeline described here was created for a microservices reference implementation called the Drone Delivery application, which you can find on [GitHub][ri].</span></span> <span data-ttu-id="6ef21-110">Viene descritto lo scenario di applicazione [qui](./design/index.md#reference-implementation).</span><span class="sxs-lookup"><span data-stu-id="6ef21-110">The application scenario is described [here](./design/index.md#reference-implementation).</span></span>

<span data-ttu-id="6ef21-111">Gli obiettivi della pipeline possono essere riepilogati come segue:</span><span class="sxs-lookup"><span data-stu-id="6ef21-111">The goals of the pipeline can be summarized as follows:</span></span>

- <span data-ttu-id="6ef21-112">I team possono creare e distribuire i propri servizi in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="6ef21-112">Teams can build and deploy their services independently.</span></span>
- <span data-ttu-id="6ef21-113">Modifiche al codice che superano il processo di integrazione continua vengono distribuiti automaticamente in un ambiente di simil-produzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-113">Code changes that pass the CI process are automatically deployed to a production-like environment.</span></span>
- <span data-ttu-id="6ef21-114">In ogni fase della pipeline vengono applicati i controlli di qualità.</span><span class="sxs-lookup"><span data-stu-id="6ef21-114">Quality gates are enforced at each stage of the pipeline.</span></span>
- <span data-ttu-id="6ef21-115">Una nuova versione di un servizio può essere distribuita side alla versione precedente.</span><span class="sxs-lookup"><span data-stu-id="6ef21-115">A new version of a service can be deployed side by side with the previous version.</span></span>

<span data-ttu-id="6ef21-116">Per altre informazioni, vedere [integrazione continua/recapito Continuo per le architetture di microservizi](./ci-cd.md).</span><span class="sxs-lookup"><span data-stu-id="6ef21-116">For more background, see [CI/CD for microservices architectures](./ci-cd.md).</span></span>

## <a name="assumptions"></a><span data-ttu-id="6ef21-117">Presupposti</span><span class="sxs-lookup"><span data-stu-id="6ef21-117">Assumptions</span></span>

<span data-ttu-id="6ef21-118">Ai fini di questo esempio, ecco alcune supposizioni sul team di sviluppo e il codice di base:</span><span class="sxs-lookup"><span data-stu-id="6ef21-118">For purposes of this example, here are some assumptions about the development team and the code base:</span></span>

- <span data-ttu-id="6ef21-119">Il repository di codice è un monorepo, con cartelle organizzati per microservizi.</span><span class="sxs-lookup"><span data-stu-id="6ef21-119">The code repository is a monorepo, with folders organized by microservice.</span></span>
- <span data-ttu-id="6ef21-120">La strategia di creazione dei rami del team è basata sullo [sviluppo basato su trunk](https://trunkbaseddevelopment.com/).</span><span class="sxs-lookup"><span data-stu-id="6ef21-120">The team's branching strategy is based on [trunk-based development](https://trunkbaseddevelopment.com/).</span></span>
- <span data-ttu-id="6ef21-121">Il team utilizza [i rami di rilascio](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases) per gestire i rilasci.</span><span class="sxs-lookup"><span data-stu-id="6ef21-121">The team uses [release branches](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases) to manage releases.</span></span> <span data-ttu-id="6ef21-122">Vengono create versioni separate per ogni microservizio.</span><span class="sxs-lookup"><span data-stu-id="6ef21-122">Separate releases are created for each microservice.</span></span>
- <span data-ttu-id="6ef21-123">Usa il processo di integrazione continua/recapito Continuo [pipeline di Azure](/azure/devops/pipelines/?view=azure-devops) per compilare, testare e distribuire i microservizi in servizio contenitore di AZURE.</span><span class="sxs-lookup"><span data-stu-id="6ef21-123">The CI/CD process uses [Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) to build, test, and deploy the microservices to AKS.</span></span>
- <span data-ttu-id="6ef21-124">Vengono archiviate le immagini del contenitore per ogni microservizio [registro contenitori di Azure](/azure/container-registry/).</span><span class="sxs-lookup"><span data-stu-id="6ef21-124">The container images for each microservice are stored in [Azure Container Registry](/azure/container-registry/).</span></span>
- <span data-ttu-id="6ef21-125">Il team Usa i grafici Helm per creare il pacchetto ogni microservizio.</span><span class="sxs-lookup"><span data-stu-id="6ef21-125">The team uses Helm charts to package each microservice.</span></span>

<span data-ttu-id="6ef21-126">Questi presupposti unità molte informazioni dettagliate specifiche della pipeline di integrazione continua/recapito Continuo.</span><span class="sxs-lookup"><span data-stu-id="6ef21-126">These assumptions drive many of the specific details of the CI/CD pipeline.</span></span> <span data-ttu-id="6ef21-127">Tuttavia, l'approccio di base descritta di seguito essere adattato per altri processi, strumenti e servizi, come Jenkins o Hub Docker.</span><span class="sxs-lookup"><span data-stu-id="6ef21-127">However, the basic approach described here be adapted for other processes, tools, and services, such as Jenkins or Docker Hub.</span></span>

## <a name="validation-builds"></a><span data-ttu-id="6ef21-128">Compilazioni di convalida</span><span class="sxs-lookup"><span data-stu-id="6ef21-128">Validation builds</span></span>

<span data-ttu-id="6ef21-129">Si supponga che uno sviluppatore sta lavorando a un microservizio denominato servizio di recapito.</span><span class="sxs-lookup"><span data-stu-id="6ef21-129">Suppose that a developer is working on a microservice called the Delivery Service.</span></span> <span data-ttu-id="6ef21-130">Durante lo sviluppo di una nuova funzionalità, lo sviluppatore archivia il codice in un ramo di funzionalità.</span><span class="sxs-lookup"><span data-stu-id="6ef21-130">While developing a new feature, the developer checks code into a feature branch.</span></span> <span data-ttu-id="6ef21-131">Per convenzione, i rami di funzionalità sono denominati `feature/*`.</span><span class="sxs-lookup"><span data-stu-id="6ef21-131">By convention, feature branches are named `feature/*`.</span></span>

![Flusso di lavoro CI/CD](./images/aks-cicd-1.png)

<span data-ttu-id="6ef21-133">Il file di definizione di compilazione include un trigger che filtra in base al nome del ramo e il percorso di origine:</span><span class="sxs-lookup"><span data-stu-id="6ef21-133">The build definition file includes a trigger that filters by the branch name and the source path:</span></span>

```yaml
trigger:
  batch: true
  branches:
    include:
    - master
    - feature/*
    - topic/*

    exclude:
    - feature/experimental/*
    - topic/experimental/*

  paths:
     include:
     - /src/shipping/delivery/
```

<span data-ttu-id="6ef21-134">&#11162;Vedere le [file di origine](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml).</span><span class="sxs-lookup"><span data-stu-id="6ef21-134">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml).</span></span>

<span data-ttu-id="6ef21-135">Con questo approccio, ogni team può avere una propria pipeline di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-135">Using this approach, each team can have its own build pipeline.</span></span> <span data-ttu-id="6ef21-136">Solo il codice archiviato nel `/src/shipping/delivery` cartella attiva una compilazione al servizio di recapito.</span><span class="sxs-lookup"><span data-stu-id="6ef21-136">Only code that is checked into the `/src/shipping/delivery` folder triggers a build of the Delivery Service.</span></span> <span data-ttu-id="6ef21-137">Il push dei commit in un ramo che corrisponde al filtro attiva una compilazione CI.</span><span class="sxs-lookup"><span data-stu-id="6ef21-137">Pushing commits to a branch that matches the filter triggers a CI build.</span></span> <span data-ttu-id="6ef21-138">A questo punto nel flusso di lavoro, la compilazione CI esegue alcune verifiche minime del codice:</span><span class="sxs-lookup"><span data-stu-id="6ef21-138">At this point in the workflow, the CI build runs some minimal code verification:</span></span>

1. <span data-ttu-id="6ef21-139">Compilare il codice.</span><span class="sxs-lookup"><span data-stu-id="6ef21-139">Build the code.</span></span>
1. <span data-ttu-id="6ef21-140">Eseguire unit test.</span><span class="sxs-lookup"><span data-stu-id="6ef21-140">Run unit tests.</span></span>

<span data-ttu-id="6ef21-141">L'obiettivo è mantenere brevi e tempi di compilazione in modo che lo sviluppatore può ottenere feedback rapido.</span><span class="sxs-lookup"><span data-stu-id="6ef21-141">The goal is to keep build times short, so the developer can get quick feedback.</span></span> <span data-ttu-id="6ef21-142">La funzionalità sarà pronta per l'unione nel master, lo sviluppatore apre una richiesta pull.</span><span class="sxs-lookup"><span data-stu-id="6ef21-142">Once the feature is ready to merge into master, the developer opens a PR.</span></span> <span data-ttu-id="6ef21-143">In questo modo viene attivata un'altra compilazione CI che esegue alcuni controlli aggiuntivi:</span><span class="sxs-lookup"><span data-stu-id="6ef21-143">This triggers another CI build that performs some additional checks:</span></span>

1. <span data-ttu-id="6ef21-144">Compilare il codice.</span><span class="sxs-lookup"><span data-stu-id="6ef21-144">Build the code.</span></span>
1. <span data-ttu-id="6ef21-145">Eseguire unit test.</span><span class="sxs-lookup"><span data-stu-id="6ef21-145">Run unit tests.</span></span>
1. <span data-ttu-id="6ef21-146">Compilare l'immagine di contenitore di runtime.</span><span class="sxs-lookup"><span data-stu-id="6ef21-146">Build the runtime container image.</span></span>
1. <span data-ttu-id="6ef21-147">Esegui analisi delle vulnerabilità sull'immagine.</span><span class="sxs-lookup"><span data-stu-id="6ef21-147">Run vulnerability scans on the image.</span></span>

![Flusso di lavoro CI/CD](./images/aks-cicd-2.png)

> [!NOTE]
> <span data-ttu-id="6ef21-149">Nel repository di DevOps di Azure, è possibile definire [criteri](/azure/devops/repos/git/branch-policies) per proteggere i rami.</span><span class="sxs-lookup"><span data-stu-id="6ef21-149">In Azure DevOps Repos, you can define [policies](/azure/devops/repos/git/branch-policies) to protect branches.</span></span> <span data-ttu-id="6ef21-150">Ad esempio, i criteri potrebbero richiedere una compilazione CI riuscita oltre all'approvazione da un responsabile per poter eseguire il merge nel master.</span><span class="sxs-lookup"><span data-stu-id="6ef21-150">For example, the policy could require a successful CI build plus a sign-off from an approver in order to merge into master.</span></span>

## <a name="full-cicd-build"></a><span data-ttu-id="6ef21-151">Compilazione di CI/CD completa</span><span class="sxs-lookup"><span data-stu-id="6ef21-151">Full CI/CD build</span></span>

<span data-ttu-id="6ef21-152">A un certo punto, il team è pronto per distribuire una nuova versione del servizio di recapito.</span><span class="sxs-lookup"><span data-stu-id="6ef21-152">At some point, the team is ready to deploy a new version of the Delivery service.</span></span> <span data-ttu-id="6ef21-153">Il responsabile del rilascio viene creato un ramo dal master con questo modello di denominazione: `release/<microservice name>/<semver>`.</span><span class="sxs-lookup"><span data-stu-id="6ef21-153">The release manager creates a branch from master with this naming pattern: `release/<microservice name>/<semver>`.</span></span> <span data-ttu-id="6ef21-154">Ad esempio: `release/delivery/v1.0.2`.</span><span class="sxs-lookup"><span data-stu-id="6ef21-154">For example, `release/delivery/v1.0.2`.</span></span>

![Flusso di lavoro CI/CD](./images/aks-cicd-3.png)

<span data-ttu-id="6ef21-156">La creazione di questo ramo attiva una compilazione CI completa che consente di eseguire tutti i passaggi precedenti più:</span><span class="sxs-lookup"><span data-stu-id="6ef21-156">Creation of this branch triggers a full CI build that runs all of the previous steps plus:</span></span>

1. <span data-ttu-id="6ef21-157">Eseguire il push dell'immagine del contenitore per registro contenitori di Azure.</span><span class="sxs-lookup"><span data-stu-id="6ef21-157">Push the container image to Azure Container Registry.</span></span> <span data-ttu-id="6ef21-158">L'immagine viene contrassegnata con il numero di versione ottenuto dal nome del ramo.</span><span class="sxs-lookup"><span data-stu-id="6ef21-158">The image is tagged with the version number taken from the branch name.</span></span>
2. <span data-ttu-id="6ef21-159">Eseguire `helm package` per creare un pacchetto il grafico Helm per il servizio.</span><span class="sxs-lookup"><span data-stu-id="6ef21-159">Run `helm package` to package the Helm chart for the service.</span></span> <span data-ttu-id="6ef21-160">Il grafico anch'esso contrassegnato con un numero di versione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-160">The chart is also tagged with a version number.</span></span>
3. <span data-ttu-id="6ef21-161">Eseguire il push del pacchetto di Helm in Registro contenitori.</span><span class="sxs-lookup"><span data-stu-id="6ef21-161">Push the Helm package to Container Registry.</span></span>

<span data-ttu-id="6ef21-162">Supponendo che la compilazione ha esito positivo, attiva un processo di distribuzione (CD) tramite una pipeline di Azure [pipeline di rilascio](/azure/devops/pipelines/release/what-is-release-management).</span><span class="sxs-lookup"><span data-stu-id="6ef21-162">Assuming this build succeeds, it triggers a deployment (CD) process using an Azure Pipelines [release pipeline](/azure/devops/pipelines/release/what-is-release-management).</span></span> <span data-ttu-id="6ef21-163">Questa pipeline include i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6ef21-163">This pipeline has the following steps:</span></span>

1. <span data-ttu-id="6ef21-164">Distribuire il grafico Helm in un ambiente di controllo di qualità.</span><span class="sxs-lookup"><span data-stu-id="6ef21-164">Deploy the Helm chart to a QA environment.</span></span>
1. <span data-ttu-id="6ef21-165">Un responsabile dà l'approvazione prima che il pacchetto venga spostato nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-165">An approver signs off before the package moves to production.</span></span> <span data-ttu-id="6ef21-166">Vedere [Release deployment control using approvals](/azure/devops/pipelines/release/approvals/approvals) (Controllo della distribuzione della versione tramite approvazioni).</span><span class="sxs-lookup"><span data-stu-id="6ef21-166">See [Release deployment control using approvals](/azure/devops/pipelines/release/approvals/approvals).</span></span>
1. <span data-ttu-id="6ef21-167">Riapplicare tag immagine Docker per lo spazio dei nomi di ambiente di produzione in Registro contenitori di Azure.</span><span class="sxs-lookup"><span data-stu-id="6ef21-167">Retag the Docker image for the production namespace in Azure Container Registry.</span></span> <span data-ttu-id="6ef21-168">Ad esempio, se il tag corrente è `myrepo.azurecr.io/delivery:v1.0.2`, il tag di produzione è `myrepo.azurecr.io/prod/delivery:v1.0.2`.</span><span class="sxs-lookup"><span data-stu-id="6ef21-168">For example, if the current tag is `myrepo.azurecr.io/delivery:v1.0.2`, the production tag is `myrepo.azurecr.io/prod/delivery:v1.0.2`.</span></span>
1. <span data-ttu-id="6ef21-169">Distribuire il grafico Helm nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-169">Deploy the Helm chart to the production environment.</span></span>

<span data-ttu-id="6ef21-170">Anche in un monorepo, queste attività possono essere limitate a singoli microservizi, in modo che i team possono distribuire con velocità elevata.</span><span class="sxs-lookup"><span data-stu-id="6ef21-170">Even in a monorepo, these tasks can be scoped to individual microservices, so that teams can deploy with high velocity.</span></span> <span data-ttu-id="6ef21-171">Il processo presenta alcuni passaggi manuali: Approvazione delle richieste pull, creazione di rami di rilascio e approvazione delle distribuzioni nel cluster di produzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-171">The process has some manual steps: Approving PRs, creating release branches, and approving deployments into the production cluster.</span></span> <span data-ttu-id="6ef21-172">Questi passaggi sono manuali dai criteri &mdash; può essere automatizzate se si preferisce l'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-172">These steps are manual by policy &mdash; they could be automated if the organization prefers.</span></span>

## <a name="isolation-of-environments"></a><span data-ttu-id="6ef21-173">Isolamento degli ambienti</span><span class="sxs-lookup"><span data-stu-id="6ef21-173">Isolation of environments</span></span>

<span data-ttu-id="6ef21-174">Si avranno più ambienti in cui si distribuiscono servizi, inclusi gli ambienti: per lo sviluppo, per lo smoke test, per il test di integrazione, per il test di carico e infine per la produzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-174">You will have multiple environments where you deploy services, including environments for development, smoke testing, integration testing, load testing, and finally production.</span></span> <span data-ttu-id="6ef21-175">Per questi ambienti è necessario un certo livello di isolamento.</span><span class="sxs-lookup"><span data-stu-id="6ef21-175">These environments need some level of isolation.</span></span> <span data-ttu-id="6ef21-176">In Kubernetes, è possibile scegliere tra isolamento fisico e isolamento logico.</span><span class="sxs-lookup"><span data-stu-id="6ef21-176">In Kubernetes, you have a choice between physical isolation and logical isolation.</span></span> <span data-ttu-id="6ef21-177">Per isolamento fisico si intende la distribuzione atta a separare i cluster.</span><span class="sxs-lookup"><span data-stu-id="6ef21-177">Physical isolation means deploying to separate clusters.</span></span> <span data-ttu-id="6ef21-178">L'isolamento logico usa spazi dei nomi e criteri, come descritto in precedenza.</span><span class="sxs-lookup"><span data-stu-id="6ef21-178">Logical isolation makes use of namespaces and policies, as described earlier.</span></span>

<span data-ttu-id="6ef21-179">Si consiglia di creare un cluster di produzione dedicato insieme a un cluster separato per gli ambienti di sviluppo/prova.</span><span class="sxs-lookup"><span data-stu-id="6ef21-179">Our recommendation is to create a dedicated production cluster along with a separate cluster for your dev/test environments.</span></span> <span data-ttu-id="6ef21-180">Usare l'isolamento logico per separare gli ambienti all'interno del cluster di sviluppo/prova.</span><span class="sxs-lookup"><span data-stu-id="6ef21-180">Use logical isolation to separate environments within the dev/test cluster.</span></span> <span data-ttu-id="6ef21-181">I servizi distribuiti nel cluster di sviluppo/prova non avranno accesso agli archivi dati che contengono dati aziendali.</span><span class="sxs-lookup"><span data-stu-id="6ef21-181">Services deployed to the dev/test cluster should never have access to data stores that hold business data.</span></span>

## <a name="build-process"></a><span data-ttu-id="6ef21-182">Processo di compilazione</span><span class="sxs-lookup"><span data-stu-id="6ef21-182">Build process</span></span>

<span data-ttu-id="6ef21-183">Quando possibile, il processo di compilazione del pacchetto in un contenitore Docker.</span><span class="sxs-lookup"><span data-stu-id="6ef21-183">When possible, package your build process into a Docker container.</span></span> <span data-ttu-id="6ef21-184">Che consente di compilare gli artefatti del codice usando Docker, senza la necessità di configurare l'ambiente di compilazione in ogni computer di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-184">That allows you to build your code artifacts using Docker, without needing to configure the build environment on each build machine.</span></span> <span data-ttu-id="6ef21-185">Un processo di compilazione in contenitori semplifica per scalare orizzontalmente la pipeline di integrazione continua tramite l'aggiunta di nuovi agenti di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-185">A containerized build process makes it easy to scale out the CI pipeline by adding new build agents.</span></span> <span data-ttu-id="6ef21-186">Inoltre, quando lo sviluppatore del team può compilare il codice semplicemente eseguendo il contenitore di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-186">Also, any developer on the team can build the code simply by running the build container.</span></span>

<span data-ttu-id="6ef21-187">Con le compilazioni in più fasi in Docker, è possibile definire l'ambiente di compilazione e l'immagine di runtime in un solo Dockerfile.</span><span class="sxs-lookup"><span data-stu-id="6ef21-187">By using multi-stage builds in Docker, you can define the build environment and the runtime image in a single Dockerfile.</span></span> <span data-ttu-id="6ef21-188">Ad esempio, ecco un Dockerfile che compila un'applicazione ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6ef21-188">For example, here's a Dockerfile that builds an ASP.NET Core application:</span></span>

```
FROM microsoft/dotnet:2.2-runtime AS base
WORKDIR /app

FROM microsoft/dotnet:2.2-sdk AS build
WORKDIR /src/Fabrikam.Workflow.Service

COPY Fabrikam.Workflow.Service/Fabrikam.Workflow.Service.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.csproj

COPY Fabrikam.Workflow.Service/. .
RUN dotnet build Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM build AS publish
RUN dotnet publish Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "Fabrikam.Workflow.Service.dll"]
```

<span data-ttu-id="6ef21-189">&#11162;Vedere le [file di origine](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile).</span><span class="sxs-lookup"><span data-stu-id="6ef21-189">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile).</span></span>

<span data-ttu-id="6ef21-190">Questo Dockerfile definisce diverse fasi di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-190">This Dockerfile defines several build stages.</span></span> <span data-ttu-id="6ef21-191">Si noti che la fase denominata `base` Usa il runtime di ASP.NET Core, durante la fase denominata `build` Usa la versione completa di ASP.NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="6ef21-191">Notice that the stage named `base` uses the ASP.NET Core runtime, while the stage named `build` uses the full ASP.NET Core SDK.</span></span> <span data-ttu-id="6ef21-192">Il `build` fase viene usata per compilare il progetto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ef21-192">The `build` stage is used to build the ASP.NET Core project.</span></span> <span data-ttu-id="6ef21-193">Ma viene compilato il contenitore di runtime finale da `base`, che contiene solo il runtime e sono notevolmente inferiori l'immagine SDK completo.</span><span class="sxs-lookup"><span data-stu-id="6ef21-193">But the final runtime container is built from `base`, which contains just the runtime and is significantly smaller than the full SDK image.</span></span>

### <a name="building-a-test-runner"></a><span data-ttu-id="6ef21-194">Creazione di un test runner</span><span class="sxs-lookup"><span data-stu-id="6ef21-194">Building a test runner</span></span>

<span data-ttu-id="6ef21-195">Un'altra buona norma consiste nell'eseguire unit test nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="6ef21-195">Another good practice is to run unit tests in the container.</span></span> <span data-ttu-id="6ef21-196">Ad esempio, qui è parte di un file Docker che consente di creare un test runner:</span><span class="sxs-lookup"><span data-stu-id="6ef21-196">For example, here is part of a Docker file that builds a test runner:</span></span>

```
FROM build AS testrunner
WORKDIR /src/tests

COPY Fabrikam.Workflow.Service.Tests/*.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.Tests.csproj

COPY Fabrikam.Workflow.Service.Tests/. .
ENTRYPOINT ["dotnet", "test", "--logger:trx"]
```

<span data-ttu-id="6ef21-197">Uno sviluppatore può utilizzare questo file di Docker per eseguire i test in locale:</span><span class="sxs-lookup"><span data-stu-id="6ef21-197">A developer can use this Docker file to run the tests locally:</span></span>

```bash
docker build . -t delivery-test:1 --target=testrunner
docker run -p 8080:8080 delivery-test:1
```

<span data-ttu-id="6ef21-198">La pipeline di integrazione continua deve inoltre eseguire i test come parte del passaggio di verifica compilazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-198">The CI pipeline should also run the tests as part of the build verification step.</span></span>

<span data-ttu-id="6ef21-199">Si noti che questo file Usa Docker `ENTRYPOINT` comando per eseguire i test, non il Docker `RUN` comando.</span><span class="sxs-lookup"><span data-stu-id="6ef21-199">Note that this file uses the Docker `ENTRYPOINT` command to run the tests, not the Docker `RUN` command.</span></span>

- <span data-ttu-id="6ef21-200">Se si usa il `RUN` comando, i test eseguiti ogni volta che si compila l'immagine.</span><span class="sxs-lookup"><span data-stu-id="6ef21-200">If you use the `RUN` command, the tests run every time you build the image.</span></span> <span data-ttu-id="6ef21-201">Usando `ENTRYPOINT`, i test sono acconsenti esplicitamente.</span><span class="sxs-lookup"><span data-stu-id="6ef21-201">By using `ENTRYPOINT`, the tests are opt-in.</span></span> <span data-ttu-id="6ef21-202">Vengono eseguiti solo quando la destinazione è in modo esplicito il `testrunner` fase.</span><span class="sxs-lookup"><span data-stu-id="6ef21-202">They run only when you explicitly target the `testrunner` stage.</span></span>
- <span data-ttu-id="6ef21-203">Un test non superato non causi Docker `build` comando esito negativo.</span><span class="sxs-lookup"><span data-stu-id="6ef21-203">A failing test doesn't cause the Docker `build` command to fail.</span></span> <span data-ttu-id="6ef21-204">In questo modo è possibile distinguere contenitore errori da errori del test di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-204">That way you can distinguish container build failures from test failures.</span></span>
- <span data-ttu-id="6ef21-205">I risultati dei test può essere salvati in un volume montato.</span><span class="sxs-lookup"><span data-stu-id="6ef21-205">Test results can be saved to a mounted volume.</span></span>

### <a name="container-best-practices"></a><span data-ttu-id="6ef21-206">Le procedure consigliate di contenitore</span><span class="sxs-lookup"><span data-stu-id="6ef21-206">Container best practices</span></span>

<span data-ttu-id="6ef21-207">Ecco alcune altre procedure consigliate da prendere in considerazione per i contenitori:</span><span class="sxs-lookup"><span data-stu-id="6ef21-207">Here are some other best practices to consider for containers:</span></span>

- <span data-ttu-id="6ef21-208">Definire convenzioni a livello di organizzazione per i tag dei contenitori, il controllo delle versioni e la denominazione delle risorse distribuite nel cluster (pod, servizi e così via).</span><span class="sxs-lookup"><span data-stu-id="6ef21-208">Define organization-wide conventions for container tags, versioning, and naming conventions for resources deployed to the cluster (pods, services, and so on).</span></span> <span data-ttu-id="6ef21-209">Questo può facilitare la diagnosi dei problemi di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-209">That can make it easier to diagnose deployment issues.</span></span>

- <span data-ttu-id="6ef21-210">Durante il ciclo di sviluppo e di test, il processo CI/CD compilerà molte immagini di contenitori.</span><span class="sxs-lookup"><span data-stu-id="6ef21-210">During the development and test cycle, the CI/CD process will build many container images.</span></span> <span data-ttu-id="6ef21-211">Solo alcune di queste immagini sono candidati per il rilascio e quindi solo alcuni di tali release candidate verranno alzate nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-211">Only some of those images are candidates for release, and then only some of those release candidates will get promoted to production.</span></span> <span data-ttu-id="6ef21-212">Disporre di una strategia di controllo delle versioni ben, in modo da sapere quali immagini sono attualmente distribuite in produzione e possono eseguire il rollback a una versione precedente se necessario.</span><span class="sxs-lookup"><span data-stu-id="6ef21-212">Have a clear versioning strategy, so that you know which images are currently deployed to production, and can roll back to a previous version if necessary.</span></span>

- <span data-ttu-id="6ef21-213">Distribuire sempre i tag di versione specifico contenitore, non `latest`.</span><span class="sxs-lookup"><span data-stu-id="6ef21-213">Always deploy specific container version tags, not `latest`.</span></span>

- <span data-ttu-id="6ef21-214">Uso [spazi dei nomi](/azure/container-registry/container-registry-best-practices#repository-namespaces) in Registro contenitori di Azure per isolare le immagini approvate per la produzione di immagini che sono ancora stati individuati.</span><span class="sxs-lookup"><span data-stu-id="6ef21-214">Use [namespaces](/azure/container-registry/container-registry-best-practices#repository-namespaces) in Azure Container Registry to isolate images that are approved for production from images that are still being tested.</span></span> <span data-ttu-id="6ef21-215">Non spostare un'immagine nello spazio dei nomi di ambiente di produzione finché non si è pronti a distribuirla nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-215">Don't move an image into the production namespace until you're ready to deploy it into production.</span></span> <span data-ttu-id="6ef21-216">Combinando questa procedura con il versionamento semantico delle immagini di contenitori è possibile ridurre le probabilità di distribuire accidentalmente una versione il cui rilascio non è stato approvato.</span><span class="sxs-lookup"><span data-stu-id="6ef21-216">If you combine this practice with semantic versioning of container images, it can reduce the chance of accidentally deploying a version that wasn't approved for release.</span></span>

- <span data-ttu-id="6ef21-217">Attenersi al principio del privilegio minimo, contenitori in esecuzione come utente senza privilegi.</span><span class="sxs-lookup"><span data-stu-id="6ef21-217">Follow the principle of least privilege by running containers as a nonprivileged user.</span></span> <span data-ttu-id="6ef21-218">In Kubernetes, è possibile creare criteri di sicurezza pod che impedisce l'esecuzione come contenitori *radice*.</span><span class="sxs-lookup"><span data-stu-id="6ef21-218">In Kubernetes, you can create a pod security policy that prevents containers from running as *root*.</span></span> <span data-ttu-id="6ef21-219">Visualizzare [impediscono che i POD in esecuzione con privilegi Root](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/).</span><span class="sxs-lookup"><span data-stu-id="6ef21-219">See [Prevent Pods From Running With Root Privileges](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/).</span></span>

## <a name="helm-charts"></a><span data-ttu-id="6ef21-220">Grafici Helm</span><span class="sxs-lookup"><span data-stu-id="6ef21-220">Helm charts</span></span>

<span data-ttu-id="6ef21-221">È consigliabile usare Helm per gestire la compilazione e distribuzione di servizi.</span><span class="sxs-lookup"><span data-stu-id="6ef21-221">Consider using Helm to manage building and deploying services.</span></span> <span data-ttu-id="6ef21-222">Ecco alcune delle funzionalità che consentono di con integrazione continua/recapito Continuo di Helm:</span><span class="sxs-lookup"><span data-stu-id="6ef21-222">Here are some of the features of Helm that help with CI/CD:</span></span>

- <span data-ttu-id="6ef21-223">Un singolo microservizio è spesso definito da più oggetti Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="6ef21-223">Often a single microservice is defined by multiple Kubernetes objects.</span></span> <span data-ttu-id="6ef21-224">Helm consente a questi oggetti essere compresse in un singolo grafico Helm.</span><span class="sxs-lookup"><span data-stu-id="6ef21-224">Helm allows these objects to be packaged into a single Helm chart.</span></span>
- <span data-ttu-id="6ef21-225">Un grafico può essere distribuito con un singolo comando di Helm, anziché una serie di comandi di kubectl.</span><span class="sxs-lookup"><span data-stu-id="6ef21-225">A chart can be deployed with a single Helm command, rather than a series of kubectl commands.</span></span>
- <span data-ttu-id="6ef21-226">I grafici sono con controllo delle versioni in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="6ef21-226">Charts are explicitly versioned.</span></span> <span data-ttu-id="6ef21-227">Usare Helm per rilasciare una versione, visualizzare le versioni ed eseguire il rollback a una versione precedente.</span><span class="sxs-lookup"><span data-stu-id="6ef21-227">Use Helm to release a version, view releases, and roll back to a previous version.</span></span> <span data-ttu-id="6ef21-228">Rilevamento aggiornamenti e revisioni, con controllo delle versioni semantico, nonché la possibilità di eseguire il rollback a una versione precedente.</span><span class="sxs-lookup"><span data-stu-id="6ef21-228">Tracking updates and revisions, using semantic versioning, along with the ability to roll back to a previous version.</span></span>
- <span data-ttu-id="6ef21-229">I grafici Helm utilizzano modelli per evitare la duplicazione delle informazioni, ad esempio etichette e i selettori, più file.</span><span class="sxs-lookup"><span data-stu-id="6ef21-229">Helm charts use templates to avoid duplicating information, such as labels and selectors, across many files.</span></span>
- <span data-ttu-id="6ef21-230">Helm è possibile gestire le dipendenze tra i grafici.</span><span class="sxs-lookup"><span data-stu-id="6ef21-230">Helm can manage dependencies between charts.</span></span>
- <span data-ttu-id="6ef21-231">I grafici possono essere archiviati in un repository Helm, ad esempio il registro contenitori di Azure e integrati nella pipeline di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-231">Charts can be stored in a Helm repository, such as Azure Container Registry, and integrated into the build pipeline.</span></span>

<span data-ttu-id="6ef21-232">Per altre informazioni sull'uso del Registro Container come repository Helm, vedere [Usare il Registro Azure Container come repository Helm per i grafici di applicazione](/azure/container-registry/container-registry-helm-repos).</span><span class="sxs-lookup"><span data-stu-id="6ef21-232">For more information about using Container Registry as a Helm repository, see [Use Azure Container Registry as a Helm repository for your application charts](/azure/container-registry/container-registry-helm-repos).</span></span>

<span data-ttu-id="6ef21-233">Un singolo microservizio può comprendere più file di configurazione di Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="6ef21-233">A single microservice may involve multiple Kubernetes configuration files.</span></span> <span data-ttu-id="6ef21-234">Aggiornamento di un servizio può significare tocca tutti questi file per aggiornare i selettori, etichette e i tag di immagine.</span><span class="sxs-lookup"><span data-stu-id="6ef21-234">Updating a service can mean touching all of these files to update selectors, labels, and image tags.</span></span> <span data-ttu-id="6ef21-235">Helm considera queste informazioni come un singolo pacchetto chiamato grafico e consente di aggiornare facilmente i file YAML usando le variabili.</span><span class="sxs-lookup"><span data-stu-id="6ef21-235">Helm treats these as a single package called a chart and allows you to easily update the YAML files by using variables.</span></span> <span data-ttu-id="6ef21-236">Helm Usa un linguaggio del modello (basato su modelli di consumo) per consentire la scrittura dei file di configurazione YAML con parametri.</span><span class="sxs-lookup"><span data-stu-id="6ef21-236">Helm uses a template language (based on Go templates) to let you write parameterized YAML configuration files.</span></span>

<span data-ttu-id="6ef21-237">Ad esempio, qui è parte di un file YAML che definisce una distribuzione:</span><span class="sxs-lookup"><span data-stu-id="6ef21-237">For example, here's part of a YAML file that defines a deployment:</span></span>

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "package.fullname" . | replace "." "" }}
  labels:
    app.kubernetes.io/name: {{ include "package.name" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}

...

  spec:
      containers:
      - name: &package-container_name fabrikam-package
        image: {{ .Values.dockerregistry }}/{{ .Values.image.repository }}:{{ .Values.image.tag }}
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        env:
        - name: LOG_LEVEL
          value: {{ .Values.log.level }}
```

<span data-ttu-id="6ef21-238">&#11162;Vedere le [file di origine](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml).</span><span class="sxs-lookup"><span data-stu-id="6ef21-238">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml).</span></span>

<span data-ttu-id="6ef21-239">È possibile vedere che il nome della distribuzione, le etichette e contenitore spec tutti i parametri del modello Usa, che vengono forniti in fase di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-239">You can see that the deployment name, labels, and container spec all use template parameters, which are provided at deployment time.</span></span> <span data-ttu-id="6ef21-240">Ad esempio, dalla riga di comando:</span><span class="sxs-lookup"><span data-stu-id="6ef21-240">For example, from the command line:</span></span>

```bash
helm install $HELM_CHARTS/package/ \
     --set image.tag=0.1.0 \
     --set image.repository=package \
     --set dockerregistry=$ACR_SERVER \
     --namespace backend \
     --name package-v0.1.0
```

<span data-ttu-id="6ef21-241">Anche se la pipeline di integrazione continua/recapito Continuo è stato possibile installare un grafico direttamente in Kubernetes, è consigliabile creare un archivio di grafico (file con estensione TGZ) e il push del grafico in un repository Helm, ad esempio registro contenitori di Azure.</span><span class="sxs-lookup"><span data-stu-id="6ef21-241">Although your CI/CD pipeline could install a chart directly to Kubernetes, we recommend creating a chart archive (.tgz file) and pushing the chart to a Helm repository such as Azure Container Registry.</span></span> <span data-ttu-id="6ef21-242">Per altre informazioni, vedere [le app basate su Docker pacchetto nei grafici di Helm in Azure pipeline](/azure/devops/pipelines/languages/helm?view=azure-devops).</span><span class="sxs-lookup"><span data-stu-id="6ef21-242">For more information, see [Package Docker-based apps in Helm charts in Azure Pipelines](/azure/devops/pipelines/languages/helm?view=azure-devops).</span></span>

<span data-ttu-id="6ef21-243">Prendere in considerazione la distribuzione di Helm in un proprio spazio dei nomi e tramite il controllo di accesso basato sui ruoli (RBAC) per limitare quali spazi dei nomi è possibile distribuire.</span><span class="sxs-lookup"><span data-stu-id="6ef21-243">Consider deploying Helm to its own namespace and using role-based access control (RBAC) to restrict which namespaces it can deploy to.</span></span> <span data-ttu-id="6ef21-244">Per altre informazioni, vedere [Role-based Access Control](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control) nella documentazione di Helm.</span><span class="sxs-lookup"><span data-stu-id="6ef21-244">For more information, see [Role-based Access Control](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control) in the Helm documentation.</span></span>

### <a name="revisions"></a><span data-ttu-id="6ef21-245">Revisioni</span><span class="sxs-lookup"><span data-stu-id="6ef21-245">Revisions</span></span>

<span data-ttu-id="6ef21-246">I grafici Helm hanno sempre un numero di versione, è necessario usare [versionamento semantico](https://semver.org/).</span><span class="sxs-lookup"><span data-stu-id="6ef21-246">Helm charts always have a version number, which must use [semantic versioning](https://semver.org/).</span></span> <span data-ttu-id="6ef21-247">Un grafico può avere anche un `appVersion`.</span><span class="sxs-lookup"><span data-stu-id="6ef21-247">A chart can also have an `appVersion`.</span></span> <span data-ttu-id="6ef21-248">Questo campo è facoltativo e non deve necessariamente essere correlata a quella del grafico.</span><span class="sxs-lookup"><span data-stu-id="6ef21-248">This field is optional, and doesn't have to be related to the chart version.</span></span> <span data-ttu-id="6ef21-249">Alcuni team potrebbe voler versioni dell'applicazione separatamente dagli aggiornamenti per i grafici.</span><span class="sxs-lookup"><span data-stu-id="6ef21-249">Some teams might want to application versions separately from updates to the charts.</span></span> <span data-ttu-id="6ef21-250">Ma un approccio più semplice consiste nell'usare un numero di versione, pertanto non c'è una relazione 1:1 tra la versione del grafico e versione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-250">But a simpler approach is to use one version number, so there's a 1:1 relation between chart version and application version.</span></span> <span data-ttu-id="6ef21-251">In questo modo, è possibile archiviare un grafico per ogni versione e distribuire con facilità la versione desiderata:</span><span class="sxs-lookup"><span data-stu-id="6ef21-251">That way, you can store one chart per release and easily deploy the desired release:</span></span>

```bash
helm install <package-chart-name> --version <desiredVersion>
```

<span data-ttu-id="6ef21-252">Un'altra buona norma consiste nel fornire un'annotazione a causa di modifiche nel modello di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="6ef21-252">Another good practice is to provide a change-cause annotation in the deployment template:</span></span>

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "delivery.fullname" . | replace "." "" }}
  labels:
     ...
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}
```

<span data-ttu-id="6ef21-253">Ciò consente di visualizzare il campo causa di modifiche per ogni revisione, utilizzando il `kubectl rollout history` comando.</span><span class="sxs-lookup"><span data-stu-id="6ef21-253">This lets you view the change-cause field for each revision, using the `kubectl rollout history` command.</span></span> <span data-ttu-id="6ef21-254">Nell'esempio precedente, la causa di modifiche viene fornita come parametro un grafico Helm.</span><span class="sxs-lookup"><span data-stu-id="6ef21-254">In the previous example, the change-cause is provided as a Helm chart parameter.</span></span>

```bash
$ kubectl rollout history deployments/delivery-v010 -n backend
deployment.extensions/delivery-v010
REVISION  CHANGE-CAUSE
1         Initial deployment
```

<span data-ttu-id="6ef21-255">È anche possibile usare il `helm list` comando per visualizzare la cronologia delle revisioni:</span><span class="sxs-lookup"><span data-stu-id="6ef21-255">You can also use the `helm list` command to view the revision history:</span></span>

```bash
~$ helm list
NAME            REVISION    UPDATED                     STATUS        CHART            APP VERSION     NAMESPACE
delivery-v0.1.0 1           Sun Apr  7 00:25:30 2019    DEPLOYED      delivery-v0.1.0  v0.1.0          backend
```

> [!TIP]
> <span data-ttu-id="6ef21-256">Usare il `--history-max` flag durante l'inizializzazione di Helm.</span><span class="sxs-lookup"><span data-stu-id="6ef21-256">Use the `--history-max` flag when initializing Helm.</span></span> <span data-ttu-id="6ef21-257">Questa impostazione limita il numero delle revisioni Tiller vengono salvati nella cronologia.</span><span class="sxs-lookup"><span data-stu-id="6ef21-257">This setting limits the number of revisions that Tiller saves in its history.</span></span> <span data-ttu-id="6ef21-258">Tiller archivia la cronologia delle revisioni in configmaps.</span><span class="sxs-lookup"><span data-stu-id="6ef21-258">Tiller stores revision history in configmaps.</span></span> <span data-ttu-id="6ef21-259">Se si sta rilasciando spesso gli aggiornamenti, il configmaps può raggiungere dimensioni molto grandi a meno che non si limita la dimensione della cronologia.</span><span class="sxs-lookup"><span data-stu-id="6ef21-259">If you're releasing updates frequently, the configmaps can grow very large unless you limit the history size.</span></span>

## <a name="azure-devops-pipeline"></a><span data-ttu-id="6ef21-260">Azure DevOps Pipeline</span><span class="sxs-lookup"><span data-stu-id="6ef21-260">Azure DevOps Pipeline</span></span>

<span data-ttu-id="6ef21-261">Nelle pipeline di Azure, le pipeline sono suddivise *compilare le pipeline* e *pipeline di versione*.</span><span class="sxs-lookup"><span data-stu-id="6ef21-261">In Azure Pipelines, pipelines are divided into *build pipelines* and *release pipelines*.</span></span> <span data-ttu-id="6ef21-262">La pipeline di compilazione viene eseguito il processo di integrazione continua e crea gli artefatti di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-262">The build pipeline runs the CI process and creates build artifacts.</span></span> <span data-ttu-id="6ef21-263">Per un'architettura di microservizi in Kubernetes, questi elementi sono le immagini del contenitore e i grafici Helm che definiscono ogni microservizio.</span><span class="sxs-lookup"><span data-stu-id="6ef21-263">For a microservices architecture on Kubernetes, these artifacts are the container images and Helm charts that define each microservice.</span></span> <span data-ttu-id="6ef21-264">La pipeline di rilascio viene eseguito il processo CD che consente di distribuire un microservizio in un cluster.</span><span class="sxs-lookup"><span data-stu-id="6ef21-264">The release pipeline runs that CD process that deploys a microservice into a cluster.</span></span>

<span data-ttu-id="6ef21-265">Una pipeline di compilazione basato sul flusso di integrazione continua descritto in precedenza in questo articolo, potrebbe essere sono le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="6ef21-265">Based on the CI flow described earlier in this article, a build pipeline might consist of the following tasks:</span></span>

1. <span data-ttu-id="6ef21-266">Creare il contenitore di test runner.</span><span class="sxs-lookup"><span data-stu-id="6ef21-266">Build the test runner container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        arguments: '--pull --target testrunner'
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        imageName: '$(imageName)-test'
    ```

1. <span data-ttu-id="6ef21-267">Eseguire i test, richiamando docker, eseguire il contenitore di test runner.</span><span class="sxs-lookup"><span data-stu-id="6ef21-267">Run the tests, by invoking docker run against the test runner container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'run'
        containerName: testrunner
        volumes: '$(System.DefaultWorkingDirectory)/TestResults:/app/tests/TestResults'
        imageName: '$(imageName)-test'
        runInBackground: false
    ```

1. <span data-ttu-id="6ef21-268">Pubblicare i risultati del test.</span><span class="sxs-lookup"><span data-stu-id="6ef21-268">Publish the test results.</span></span> <span data-ttu-id="6ef21-269">Visualizzare [integrare compilazione e attività di test](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks).</span><span class="sxs-lookup"><span data-stu-id="6ef21-269">See [Integrate build and test tasks](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks).</span></span>

    ```yaml
    - task: PublishTestResults@2
      inputs:
        testResultsFormat: 'VSTest'
        testResultsFiles: 'TestResults/*.trx'
        searchFolder: '$(System.DefaultWorkingDirectory)'
        publishRunAttachments: true
    ```

1. <span data-ttu-id="6ef21-270">Creare il contenitore di runtime.</span><span class="sxs-lookup"><span data-stu-id="6ef21-270">Build the runtime container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        includeLatestTag: false
        imageName: '$(imageName)'
    ```

1. <span data-ttu-id="6ef21-271">Eseguire il push al contenitore per registro contenitori di Azure (o altro registro contenitori).</span><span class="sxs-lookup"><span data-stu-id="6ef21-271">Push to the container to Azure Container Registry (or other container registry).</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'Push an image'
        imageName: '$(imageName)'
        includeSourceTags: false
    ```

1. <span data-ttu-id="6ef21-272">Creare un pacchetto il grafico Helm.</span><span class="sxs-lookup"><span data-stu-id="6ef21-272">Package the Helm chart.</span></span>

    ```yaml
    - task: HelmDeploy@0
      inputs:
        command: package
        chartPath: $(chartPath)
        chartVersion: $(Build.SourceBranchName)
        arguments: '--app-version $(Build.SourceBranchName)'
    ```

1. <span data-ttu-id="6ef21-273">Eseguire il push del pacchetto di Helm in Registro contenitori di Azure (o altri repository Helm).</span><span class="sxs-lookup"><span data-stu-id="6ef21-273">Push the Helm package to Azure Container Registry (or other Helm repository).</span></span>

    ```yaml
    task: AzureCLI@1
      inputs:
        azureSubscription: $(AzureSubscription)
        scriptLocation: inlineScript
        inlineScript: |
        az acr helm push $(System.ArtifactsDirectory)/$(repositoryName)-$(Build.SourceBranchName).tgz --name $(AzureContainerRegistry);
    ```

<span data-ttu-id="6ef21-274">&#11162;Vedere le [file di origine](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml).</span><span class="sxs-lookup"><span data-stu-id="6ef21-274">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml).</span></span>

<span data-ttu-id="6ef21-275">L'output della pipeline di integrazione continua è un'immagine del contenitore di produzione e un grafico Helm aggiornato per il microservizio.</span><span class="sxs-lookup"><span data-stu-id="6ef21-275">The output from the CI pipeline is a production-ready container image and an updated Helm chart for the microservice.</span></span> <span data-ttu-id="6ef21-276">A questo punto, la pipeline di rilascio può intervenire.</span><span class="sxs-lookup"><span data-stu-id="6ef21-276">At this point, the release pipeline can take over.</span></span> <span data-ttu-id="6ef21-277">Esegue i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6ef21-277">It performs the following steps:</span></span>

- <span data-ttu-id="6ef21-278">Configurare ambienti di sviluppo/controllo qualità/staging.</span><span class="sxs-lookup"><span data-stu-id="6ef21-278">Deploy to dev/QA/staging environments.</span></span>
- <span data-ttu-id="6ef21-279">Attendere che un responsabile approvazione di approvare o rifiutare la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-279">Wait for an approver to approve or reject the deployment.</span></span>
- <span data-ttu-id="6ef21-280">Riapplicare l'immagine del contenitore per il rilascio di tag</span><span class="sxs-lookup"><span data-stu-id="6ef21-280">Retag the container image for release</span></span>
- <span data-ttu-id="6ef21-281">Eseguire il push al tag di versione nel registro contenitori.</span><span class="sxs-lookup"><span data-stu-id="6ef21-281">Push the release tag to the container registry.</span></span>
- <span data-ttu-id="6ef21-282">Aggiornare il grafico Helm nel cluster di produzione.</span><span class="sxs-lookup"><span data-stu-id="6ef21-282">Upgrade the Helm chart in the production cluster.</span></span>

<span data-ttu-id="6ef21-283">Per altre informazioni sulla creazione di una pipeline di rilascio, vedere [pipeline di rilascio, le versioni in versione bozza e opzioni per la versione](/azure/devops/pipelines/release/?view=azure-devops).</span><span class="sxs-lookup"><span data-stu-id="6ef21-283">For more information about creating a release pipeline, see [Release pipelines, draft releases, and release options](/azure/devops/pipelines/release/?view=azure-devops).</span></span>

<span data-ttu-id="6ef21-284">Il diagramma seguente illustra il processo CI/CD end-to-end descritto in questo articolo:</span><span class="sxs-lookup"><span data-stu-id="6ef21-284">The following diagram shows the end-to-end CI/CD process described in this article:</span></span>

![Pipeline di recapito Continuo/distribuzione continua](./images/aks-cicd-flow.png)

## <a name="next-steps"></a><span data-ttu-id="6ef21-286">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="6ef21-286">Next steps</span></span>

<span data-ttu-id="6ef21-287">Questo articolo si basava su un'implementazione di riferimento che sono disponibili nel [GitHub][ri].</span><span class="sxs-lookup"><span data-stu-id="6ef21-287">This article was based on a reference implementation that you can find on [GitHub][ri].</span></span>

[ri]: https://github.com/mspnp/microservices-reference-implementation