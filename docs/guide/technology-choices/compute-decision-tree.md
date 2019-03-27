---
title: Albero delle decisioni per i servizi di calcolo di Azure
titleSuffix: Azure Application Architecture Guide
description: Diagramma di flusso per la selezione di un servizio di calcolo.
author: MikeWasson
ms.date: 11/03/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: e3df1cbdd049e8c40597a85eca29899d8d0d1bc3
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245592"
---
# <a name="decision-tree-for-azure-compute-services"></a><span data-ttu-id="15350-103">Albero delle decisioni per i servizi di calcolo di Azure</span><span class="sxs-lookup"><span data-stu-id="15350-103">Decision tree for Azure compute services</span></span>

<span data-ttu-id="15350-104">Azure offre una serie di modi per ospitare il codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="15350-104">Azure offers a number of ways to host your application code.</span></span> <span data-ttu-id="15350-105">Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="15350-105">The term *compute* refers to the hosting model for the computing resources that your application runs on.</span></span> <span data-ttu-id="15350-106">Il diagramma di flusso seguente consente di scegliere un servizio di calcolo per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="15350-106">The following flowchart will help you to choose a compute service for your application.</span></span> <span data-ttu-id="15350-107">Il diagramma di flusso illustra un set di criteri decisionali chiave per ottenere delle indicazioni.</span><span class="sxs-lookup"><span data-stu-id="15350-107">The flowchart guides you through a set of key decision criteria to reach a recommendation.</span></span>

<span data-ttu-id="15350-108">**Considerare questo diagramma di flusso come un punto di partenza.**</span><span class="sxs-lookup"><span data-stu-id="15350-108">**Treat this flowchart as a starting point.**</span></span> <span data-ttu-id="15350-109">Dato che ogni applicazione presenta requisiti specifici, usare le indicazioni come punto di partenza</span><span class="sxs-lookup"><span data-stu-id="15350-109">Every application has unique requirements, so use the recommendation as a starting point.</span></span> <span data-ttu-id="15350-110">e quindi eseguire una valutazione più dettagliata, esaminando aspetti come i seguenti:</span><span class="sxs-lookup"><span data-stu-id="15350-110">Then perform a more detailed evaluation, looking at aspects such as:</span></span>

- <span data-ttu-id="15350-111">Set di funzionalità</span><span class="sxs-lookup"><span data-stu-id="15350-111">Feature set</span></span>
- [<span data-ttu-id="15350-112">Limiti del servizio</span><span class="sxs-lookup"><span data-stu-id="15350-112">Service limits</span></span>](/azure/azure-subscription-service-limits)
- [<span data-ttu-id="15350-113">Costii</span><span class="sxs-lookup"><span data-stu-id="15350-113">Cost</span></span>](https://azure.microsoft.com/pricing/)
- [<span data-ttu-id="15350-114">Contratto di servizio</span><span class="sxs-lookup"><span data-stu-id="15350-114">SLA</span></span>](https://azure.microsoft.com/support/legal/sla/)
- [<span data-ttu-id="15350-115">Disponibilità internazionale</span><span class="sxs-lookup"><span data-stu-id="15350-115">Regional availability</span></span>](https://azure.microsoft.com/global-infrastructure/services/)
- <span data-ttu-id="15350-116">Ecosistema e competenze del team dello sviluppatore</span><span class="sxs-lookup"><span data-stu-id="15350-116">Developer ecosystem and team skills</span></span>
- [<span data-ttu-id="15350-117">Tabelle di confronto tra i servizi di calcolo</span><span class="sxs-lookup"><span data-stu-id="15350-117">Compute comparison tables</span></span>](./compute-comparison.md)

<span data-ttu-id="15350-118">Se l'applicazione è costituita da più carichi di lavoro, valutare ogni carico di lavoro separatamente.</span><span class="sxs-lookup"><span data-stu-id="15350-118">If your application consists of multiple workloads, evaluate each workload separately.</span></span> <span data-ttu-id="15350-119">Una soluzione completa può includere due o più servizi di calcolo.</span><span class="sxs-lookup"><span data-stu-id="15350-119">A complete solution may incorporate two or more compute services.</span></span>

<span data-ttu-id="15350-120">Per altre informazioni sulle opzioni per l'hosting di contenitori in Azure, vedere [Azure Container](https://azure.microsoft.com/overview/containers/).</span><span class="sxs-lookup"><span data-stu-id="15350-120">For more information about your options for hosting containers in Azure, see [Azure Containers](https://azure.microsoft.com/overview/containers/).</span></span>

## <a name="flowchart"></a><span data-ttu-id="15350-121">Diagramma di flusso</span><span class="sxs-lookup"><span data-stu-id="15350-121">Flowchart</span></span>

![Albero delle decisioni per i servizi di calcolo di Azure](../images/compute-decision-tree.svg)

## <a name="definitions"></a><span data-ttu-id="15350-123">Definizioni</span><span class="sxs-lookup"><span data-stu-id="15350-123">Definitions</span></span>

- <span data-ttu-id="15350-124">La **modalità lift-and-shift** è una strategia per la migrazione di un carico di lavoro sul cloud senza necessità di riprogettare l'applicazione o apportare modifiche al codice.</span><span class="sxs-lookup"><span data-stu-id="15350-124">**Lift and shift** is a strategy for migrating a workload to the cloud without redesigning the application or making code changes.</span></span> <span data-ttu-id="15350-125">Viene definita anche *rehosting*.</span><span class="sxs-lookup"><span data-stu-id="15350-125">Also called *rehosting*.</span></span> <span data-ttu-id="15350-126">Per altre informazioni, vedere il [Centro migrazione di Azure](https://azure.microsoft.com/migration/).</span><span class="sxs-lookup"><span data-stu-id="15350-126">For more information, see [Azure migration center](https://azure.microsoft.com/migration/).</span></span>

- <span data-ttu-id="15350-127">L'**ottimizzazione per il cloud** è una strategia per la migrazione al cloud tramite il refactoring di un'applicazione per sfruttare i vantaggi delle funzionalità native del cloud.</span><span class="sxs-lookup"><span data-stu-id="15350-127">**Cloud optimized** is a strategy for migrating to the cloud by refactoring an application to take advantage of cloud-native features and capabilities.</span></span>

## <a name="next-steps"></a><span data-ttu-id="15350-128">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="15350-128">Next steps</span></span>

<span data-ttu-id="15350-129">Per altri criteri da prendere in considerazione, vedere [Criteri per la scelta di un servizio di calcolo di Azure](./compute-comparison.md).</span><span class="sxs-lookup"><span data-stu-id="15350-129">For additional criteria to consider, see [Criteria for choosing an Azure compute service](./compute-comparison.md).</span></span>
