---
title: Albero delle decisioni per i servizi di calcolo di Azure
description: Diagramma di flusso per la selezione di un servizio di calcolo
author: MikeWasson
ms.date: 04/21/2018
ms.openlocfilehash: 3dcfbd156d4fced863a56bcc8bb74483aa665f9f
ms.sourcegitcommit: 7ced70ebc11aa0df0dc0104092d3cc6ad5c28bd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
---
# <a name="decision-tree-for-azure-compute-services"></a><span data-ttu-id="fd371-103">Albero delle decisioni per i servizi di calcolo di Azure</span><span class="sxs-lookup"><span data-stu-id="fd371-103">Decision tree for Azure compute services</span></span>

<span data-ttu-id="fd371-104">Azure offre una serie di modi per ospitare il codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fd371-104">Azure offers a number of ways to host your application code.</span></span> <span data-ttu-id="fd371-105">Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fd371-105">The term *compute* refers to the hosting model for the computing resources that your application runs on.</span></span> <span data-ttu-id="fd371-106">Il diagramma di flusso seguente consente di scegliere un servizio di calcolo per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fd371-106">The following flowchart will help you to choose a compute service for your application.</span></span> <span data-ttu-id="fd371-107">Il diagramma di flusso illustra un set di criteri decisionali chiave per ottenere delle indicazioni.</span><span class="sxs-lookup"><span data-stu-id="fd371-107">The flowchart guides you through a set of key decision criteria to reach a recommendation.</span></span> 

<span data-ttu-id="fd371-108">**Considerare questo diagramma di flusso come un punto di partenza.**</span><span class="sxs-lookup"><span data-stu-id="fd371-108">**Treat this flowchart as a starting point.**</span></span> <span data-ttu-id="fd371-109">Dato che ogni applicazione presenta requisiti specifici, usare le indicazioni come punto di partenza</span><span class="sxs-lookup"><span data-stu-id="fd371-109">Every application has unique requirements, so use the recommendation as a starting point.</span></span> <span data-ttu-id="fd371-110">e quindi eseguire una valutazione più dettagliata, esaminando aspetti come i seguenti:</span><span class="sxs-lookup"><span data-stu-id="fd371-110">Then perform a more detailed evaluation, looking at aspects such as:</span></span>
 
- <span data-ttu-id="fd371-111">Set di funzionalità</span><span class="sxs-lookup"><span data-stu-id="fd371-111">Feature set</span></span>
- [<span data-ttu-id="fd371-112">Limiti del servizio</span><span class="sxs-lookup"><span data-stu-id="fd371-112">Service limits</span></span>](/azure/azure-subscription-service-limits)
- [<span data-ttu-id="fd371-113">Costii</span><span class="sxs-lookup"><span data-stu-id="fd371-113">Cost</span></span>](https://azure.microsoft.com/pricing/)
- [<span data-ttu-id="fd371-114">Contratto di servizio</span><span class="sxs-lookup"><span data-stu-id="fd371-114">SLA</span></span>](https://azure.microsoft.com/support/legal/sla/)
- [<span data-ttu-id="fd371-115">Disponibilità internazionale</span><span class="sxs-lookup"><span data-stu-id="fd371-115">Regional availability</span></span>](https://azure.microsoft.com/global-infrastructure/services/)
- <span data-ttu-id="fd371-116">Ecosistema e competenze del team dello sviluppatore</span><span class="sxs-lookup"><span data-stu-id="fd371-116">Developer ecosystem and team skills</span></span>
- [<span data-ttu-id="fd371-117">Tabelle di confronto tra i servizi di calcolo</span><span class="sxs-lookup"><span data-stu-id="fd371-117">Compute comparison tables</span></span>](./compute-comparison.md)

<span data-ttu-id="fd371-118">Se l'applicazione è costituita da più carichi di lavoro, valutare ogni carico di lavoro separatamente.</span><span class="sxs-lookup"><span data-stu-id="fd371-118">If your application consists of multiple workloads, evaluate each workload separately.</span></span> <span data-ttu-id="fd371-119">Una soluzione completa può includere due o più servizi di calcolo.</span><span class="sxs-lookup"><span data-stu-id="fd371-119">A complete solution may incorporate two or more compute services.</span></span>

![](../images/compute-decision-tree.svg)

