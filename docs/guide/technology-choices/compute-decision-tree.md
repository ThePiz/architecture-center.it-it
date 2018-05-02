---
title: Albero delle decisioni per i servizi di calcolo di Azure
description: Diagramma di flusso per la selezione di un servizio di calcolo
author: MikeWasson
ms.date: 04/21/2018
ms.openlocfilehash: f5703f4906ca2ea6f825b383710eb4bd335f5043
ms.sourcegitcommit: d702b4d27e96e7a5a248dc4f2f0e25cf6e82c134
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="decision-tree-for-azure-compute-services"></a><span data-ttu-id="fc271-103">Albero delle decisioni per i servizi di calcolo di Azure</span><span class="sxs-lookup"><span data-stu-id="fc271-103">Decision tree for Azure compute services</span></span>

<span data-ttu-id="fc271-104">Azure offre una serie di modi per ospitare il codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fc271-104">Azure offers a number of ways to host your application code.</span></span> <span data-ttu-id="fc271-105">Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fc271-105">The term *compute* refers to the hosting model for the computing resources that your application runs on.</span></span> <span data-ttu-id="fc271-106">Il diagramma di flusso seguente consente di scegliere un servizio di calcolo per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fc271-106">The following flowchart will help you to choose a compute service for your application.</span></span>
 
![](../images/compute-decision-tree.svg)

<span data-ttu-id="fc271-107">Il diagramma di flusso illustra un set di criteri decisionali chiave per ottenere delle indicazioni.</span><span class="sxs-lookup"><span data-stu-id="fc271-107">The flowchart guides you through a set of key decision criteria to reach a recommendation.</span></span> <span data-ttu-id="fc271-108">Ogni applicazione presenta requisiti specifici, per cui è necessario considerare le indicazioni come punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="fc271-108">Every application has unique requirements, so you should treat the recommendation as a starting point.</span></span> <span data-ttu-id="fc271-109">È quindi opportuno eseguire un'analisi più dettagliata, esaminando aspetti quali, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="fc271-109">Then perform a more detailed analysis, looking at aspects such as:</span></span>
 
- <span data-ttu-id="fc271-110">Set di funzionalità</span><span class="sxs-lookup"><span data-stu-id="fc271-110">Feature set</span></span>
- [<span data-ttu-id="fc271-111">Limiti del servizio</span><span class="sxs-lookup"><span data-stu-id="fc271-111">Service limits</span></span>](/azure/azure-subscription-service-limits)
- [<span data-ttu-id="fc271-112">Costii</span><span class="sxs-lookup"><span data-stu-id="fc271-112">Cost</span></span>](https://azure.microsoft.com/pricing/)
- [<span data-ttu-id="fc271-113">Contratto di servizio</span><span class="sxs-lookup"><span data-stu-id="fc271-113">SLA</span></span>](https://azure.microsoft.com/support/legal/sla/)
- [<span data-ttu-id="fc271-114">Disponibilità internazionale</span><span class="sxs-lookup"><span data-stu-id="fc271-114">Regional availability</span></span>](https://azure.microsoft.com/global-infrastructure/services/)
- <span data-ttu-id="fc271-115">Ecosistema e competenze del team dello sviluppatore</span><span class="sxs-lookup"><span data-stu-id="fc271-115">Developer ecosystem and team skills</span></span>
- [<span data-ttu-id="fc271-116">Tabelle di confronto tra i servizi di calcolo</span><span class="sxs-lookup"><span data-stu-id="fc271-116">Compute comparison tables</span></span>](./compute-comparison.md)

<span data-ttu-id="fc271-117">Se l'applicazione è costituita da più carichi di lavoro, valutare ogni carico di lavoro separatamente.</span><span class="sxs-lookup"><span data-stu-id="fc271-117">If your application consists of multiple workloads, evaluate each workload separately.</span></span> <span data-ttu-id="fc271-118">Una soluzione completa può includere due o più servizi di calcolo.</span><span class="sxs-lookup"><span data-stu-id="fc271-118">A complete solution may incorporate two or more compute services.</span></span>

