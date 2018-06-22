---
title: Albero delle decisioni per i servizi di calcolo di Azure
description: Diagramma di flusso per la selezione di un servizio di calcolo
author: MikeWasson
ms.date: 06/13/2018
ms.openlocfilehash: 60bb84d4bf210888d3d43498db043b6e452f6a80
ms.sourcegitcommit: 26b04f138a860979aea5d253ba7fecffc654841e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36206727"
---
# <a name="decision-tree-for-azure-compute-services"></a><span data-ttu-id="3423d-103">Albero delle decisioni per i servizi di calcolo di Azure</span><span class="sxs-lookup"><span data-stu-id="3423d-103">Decision tree for Azure compute services</span></span>

<span data-ttu-id="3423d-104">Azure offre una serie di modi per ospitare il codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="3423d-104">Azure offers a number of ways to host your application code.</span></span> <span data-ttu-id="3423d-105">Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="3423d-105">The term *compute* refers to the hosting model for the computing resources that your application runs on.</span></span> <span data-ttu-id="3423d-106">Il diagramma di flusso seguente consente di scegliere un servizio di calcolo per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="3423d-106">The following flowchart will help you to choose a compute service for your application.</span></span> <span data-ttu-id="3423d-107">Il diagramma di flusso illustra un set di criteri decisionali chiave per ottenere delle indicazioni.</span><span class="sxs-lookup"><span data-stu-id="3423d-107">The flowchart guides you through a set of key decision criteria to reach a recommendation.</span></span> 

<span data-ttu-id="3423d-108">**Considerare questo diagramma di flusso come un punto di partenza.**</span><span class="sxs-lookup"><span data-stu-id="3423d-108">**Treat this flowchart as a starting point.**</span></span> <span data-ttu-id="3423d-109">Dato che ogni applicazione presenta requisiti specifici, usare le indicazioni come punto di partenza</span><span class="sxs-lookup"><span data-stu-id="3423d-109">Every application has unique requirements, so use the recommendation as a starting point.</span></span> <span data-ttu-id="3423d-110">e quindi eseguire una valutazione più dettagliata, esaminando aspetti come i seguenti:</span><span class="sxs-lookup"><span data-stu-id="3423d-110">Then perform a more detailed evaluation, looking at aspects such as:</span></span>
 
- <span data-ttu-id="3423d-111">Set di funzionalità</span><span class="sxs-lookup"><span data-stu-id="3423d-111">Feature set</span></span>
- [<span data-ttu-id="3423d-112">Limiti del servizio</span><span class="sxs-lookup"><span data-stu-id="3423d-112">Service limits</span></span>](/azure/azure-subscription-service-limits)
- [<span data-ttu-id="3423d-113">Costii</span><span class="sxs-lookup"><span data-stu-id="3423d-113">Cost</span></span>](https://azure.microsoft.com/pricing/)
- [<span data-ttu-id="3423d-114">Contratto di servizio</span><span class="sxs-lookup"><span data-stu-id="3423d-114">SLA</span></span>](https://azure.microsoft.com/support/legal/sla/)
- [<span data-ttu-id="3423d-115">Disponibilità internazionale</span><span class="sxs-lookup"><span data-stu-id="3423d-115">Regional availability</span></span>](https://azure.microsoft.com/global-infrastructure/services/)
- <span data-ttu-id="3423d-116">Ecosistema e competenze del team dello sviluppatore</span><span class="sxs-lookup"><span data-stu-id="3423d-116">Developer ecosystem and team skills</span></span>
- [<span data-ttu-id="3423d-117">Tabelle di confronto tra i servizi di calcolo</span><span class="sxs-lookup"><span data-stu-id="3423d-117">Compute comparison tables</span></span>](./compute-comparison.md)

<span data-ttu-id="3423d-118">Se l'applicazione è costituita da più carichi di lavoro, valutare ogni carico di lavoro separatamente.</span><span class="sxs-lookup"><span data-stu-id="3423d-118">If your application consists of multiple workloads, evaluate each workload separately.</span></span> <span data-ttu-id="3423d-119">Una soluzione completa può includere due o più servizi di calcolo.</span><span class="sxs-lookup"><span data-stu-id="3423d-119">A complete solution may incorporate two or more compute services.</span></span>

## <a name="flowchart"></a><span data-ttu-id="3423d-120">Diagramma di flusso</span><span class="sxs-lookup"><span data-stu-id="3423d-120">Flowchart</span></span>

![](../images/compute-decision-tree.svg)

## <a name="definitions"></a><span data-ttu-id="3423d-121">Definizioni</span><span class="sxs-lookup"><span data-stu-id="3423d-121">Definitions</span></span>

- <span data-ttu-id="3423d-122">Il termine **greenfield** descrive un progetto software completamente nuovo e creato da zero.</span><span class="sxs-lookup"><span data-stu-id="3423d-122">**Greenfield** describes a software project that is completely new and built from scratch.</span></span> <span data-ttu-id="3423d-123">Non include codice legacy.</span><span class="sxs-lookup"><span data-stu-id="3423d-123">It does not include legacy code.</span></span> 

- <span data-ttu-id="3423d-124">Il termine **brownfield** indica un progetto software basato su un'applicazione esistente.</span><span class="sxs-lookup"><span data-stu-id="3423d-124">**Brownfield** describes a software project that builds on an existing application.</span></span> <span data-ttu-id="3423d-125">Può ereditare codice legacy o framework.</span><span class="sxs-lookup"><span data-stu-id="3423d-125">It may inherit legacy code or frameworks.</span></span>

- <span data-ttu-id="3423d-126">La **modalità lift-and-shift** è una strategia per la migrazione di un carico di lavoro sul cloud senza necessità di riprogettare l'applicazione o apportare modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="3423d-126">**Lift and shift** is a strategy for migrating a workload to the cloud without redesigning the application or making code changes.</span></span> <span data-ttu-id="3423d-127">Viene definita anche *rehosting*.</span><span class="sxs-lookup"><span data-stu-id="3423d-127">Also called *rehosting*.</span></span> <span data-ttu-id="3423d-128">Per altre informazioni, vedere il [Centro migrazione di Azure](https://azure.microsoft.com/migration/).</span><span class="sxs-lookup"><span data-stu-id="3423d-128">For more information, see [Azure migration center](https://azure.microsoft.com/migration/).</span></span>

- <span data-ttu-id="3423d-129">L'**ottimizzazione per il cloud** è una strategia per la migrazione al cloud tramite il refactoring di un'applicazione per sfruttare i vantaggi delle funzionalità native del cloud.</span><span class="sxs-lookup"><span data-stu-id="3423d-129">**Cloud optimized** is a strategy for migrating to the cloud by refactoring an application to take advantage of cloud-native features and capabilities.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3423d-130">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="3423d-130">Next steps</span></span>

<span data-ttu-id="3423d-131">Per altri criteri da prendere in considerazione, vedere [Criteri per la scelta di un servizio di calcolo di Azure](./compute-comparison.md).</span><span class="sxs-lookup"><span data-stu-id="3423d-131">For additional criteria to consider, see [Criteria for choosing an Azure compute service](./compute-comparison.md).</span></span>
