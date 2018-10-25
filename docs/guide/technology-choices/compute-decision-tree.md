---
title: Albero delle decisioni per i servizi di calcolo di Azure
description: Diagramma di flusso per la selezione di un servizio di calcolo
author: MikeWasson
ms.date: 10/23/2018
ms.openlocfilehash: 35002b4840b80bcc35b5baf36ec8e414ed8f20be
ms.sourcegitcommit: 2ae794de13c45cf24ad60d4f4dbb193c25944eff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50001899"
---
# <a name="decision-tree-for-azure-compute-services"></a><span data-ttu-id="6dd8d-103">Albero delle decisioni per i servizi di calcolo di Azure</span><span class="sxs-lookup"><span data-stu-id="6dd8d-103">Decision tree for Azure compute services</span></span>

<span data-ttu-id="6dd8d-104">Azure offre una serie di modi per ospitare il codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-104">Azure offers a number of ways to host your application code.</span></span> <span data-ttu-id="6dd8d-105">Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-105">The term *compute* refers to the hosting model for the computing resources that your application runs on.</span></span> <span data-ttu-id="6dd8d-106">Il diagramma di flusso seguente consente di scegliere un servizio di calcolo per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-106">The following flowchart will help you to choose a compute service for your application.</span></span> <span data-ttu-id="6dd8d-107">Il diagramma di flusso illustra un set di criteri decisionali chiave per ottenere delle indicazioni.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-107">The flowchart guides you through a set of key decision criteria to reach a recommendation.</span></span> 

<span data-ttu-id="6dd8d-108">**Considerare questo diagramma di flusso come un punto di partenza.**</span><span class="sxs-lookup"><span data-stu-id="6dd8d-108">**Treat this flowchart as a starting point.**</span></span> <span data-ttu-id="6dd8d-109">Dato che ogni applicazione presenta requisiti specifici, usare le indicazioni come punto di partenza</span><span class="sxs-lookup"><span data-stu-id="6dd8d-109">Every application has unique requirements, so use the recommendation as a starting point.</span></span> <span data-ttu-id="6dd8d-110">e quindi eseguire una valutazione più dettagliata, esaminando aspetti come i seguenti:</span><span class="sxs-lookup"><span data-stu-id="6dd8d-110">Then perform a more detailed evaluation, looking at aspects such as:</span></span>
 
- <span data-ttu-id="6dd8d-111">Set di funzionalità</span><span class="sxs-lookup"><span data-stu-id="6dd8d-111">Feature set</span></span>
- [<span data-ttu-id="6dd8d-112">Limiti del servizio</span><span class="sxs-lookup"><span data-stu-id="6dd8d-112">Service limits</span></span>](/azure/azure-subscription-service-limits)
- [<span data-ttu-id="6dd8d-113">Costii</span><span class="sxs-lookup"><span data-stu-id="6dd8d-113">Cost</span></span>](https://azure.microsoft.com/pricing/)
- [<span data-ttu-id="6dd8d-114">Contratto di servizio</span><span class="sxs-lookup"><span data-stu-id="6dd8d-114">SLA</span></span>](https://azure.microsoft.com/support/legal/sla/)
- [<span data-ttu-id="6dd8d-115">Disponibilità internazionale</span><span class="sxs-lookup"><span data-stu-id="6dd8d-115">Regional availability</span></span>](https://azure.microsoft.com/global-infrastructure/services/)
- <span data-ttu-id="6dd8d-116">Ecosistema e competenze del team dello sviluppatore</span><span class="sxs-lookup"><span data-stu-id="6dd8d-116">Developer ecosystem and team skills</span></span>
- [<span data-ttu-id="6dd8d-117">Tabelle di confronto tra i servizi di calcolo</span><span class="sxs-lookup"><span data-stu-id="6dd8d-117">Compute comparison tables</span></span>](./compute-comparison.md)

<span data-ttu-id="6dd8d-118">Se l'applicazione è costituita da più carichi di lavoro, valutare ogni carico di lavoro separatamente.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-118">If your application consists of multiple workloads, evaluate each workload separately.</span></span> <span data-ttu-id="6dd8d-119">Una soluzione completa può includere due o più servizi di calcolo.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-119">A complete solution may incorporate two or more compute services.</span></span>

<span data-ttu-id="6dd8d-120">Per altre informazioni sulle opzioni per l'hosting di contenitori in Azure, vedere https://azure.microsoft.com/overview/containers/.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-120">For more information about your options for hosting containers in Azure, see https://azure.microsoft.com/overview/containers/.</span></span>

## <a name="flowchart"></a><span data-ttu-id="6dd8d-121">Diagramma di flusso</span><span class="sxs-lookup"><span data-stu-id="6dd8d-121">Flowchart</span></span>

![](../images/compute-decision-tree.svg)

## <a name="definitions"></a><span data-ttu-id="6dd8d-122">Definizioni</span><span class="sxs-lookup"><span data-stu-id="6dd8d-122">Definitions</span></span>

- <span data-ttu-id="6dd8d-123">La **modalità lift-and-shift** è una strategia per la migrazione di un carico di lavoro sul cloud senza necessità di riprogettare l'applicazione o apportare modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-123">**Lift and shift** is a strategy for migrating a workload to the cloud without redesigning the application or making code changes.</span></span> <span data-ttu-id="6dd8d-124">Viene definita anche *rehosting*.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-124">Also called *rehosting*.</span></span> <span data-ttu-id="6dd8d-125">Per altre informazioni, vedere il [Centro migrazione di Azure](https://azure.microsoft.com/migration/).</span><span class="sxs-lookup"><span data-stu-id="6dd8d-125">For more information, see [Azure migration center](https://azure.microsoft.com/migration/).</span></span>

- <span data-ttu-id="6dd8d-126">L'**ottimizzazione per il cloud** è una strategia per la migrazione al cloud tramite il refactoring di un'applicazione per sfruttare i vantaggi delle funzionalità native del cloud.</span><span class="sxs-lookup"><span data-stu-id="6dd8d-126">**Cloud optimized** is a strategy for migrating to the cloud by refactoring an application to take advantage of cloud-native features and capabilities.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6dd8d-127">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="6dd8d-127">Next steps</span></span>

<span data-ttu-id="6dd8d-128">Per altri criteri da prendere in considerazione, vedere [Criteri per la scelta di un servizio di calcolo di Azure](./compute-comparison.md).</span><span class="sxs-lookup"><span data-stu-id="6dd8d-128">For additional criteria to consider, see [Criteria for choosing an Azure compute service](./compute-comparison.md).</span></span>
