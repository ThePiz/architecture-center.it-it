---
title: 'CAF: Piccole e medie imprese - Criteri aziendali iniziali per la strategia di governance'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: Piccole e medie imprese - Criteri aziendali iniziali per la strategia di governance
author: BrianBlanchard
ms.openlocfilehash: 4f49d0aae2c6ab5a5c8feba669cbbb904af2773b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901360"
---
# <a name="small-to-medium-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a><span data-ttu-id="4c6f0-103">Piccole e medie imprese: Criteri aziendali iniziali per la strategia di governance</span><span class="sxs-lookup"><span data-stu-id="4c6f0-103">Small-to-medium enterprise: Initial corporate policy behind the governance strategy</span></span>

<span data-ttu-id="4c6f0-104">I criteri aziendali seguenti definiscono la posizione iniziale di governance, ovvero il punto iniziale per questo percorso.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-104">The following corporate policy defines an initial governance position, which is the starting point for this journey.</span></span> <span data-ttu-id="4c6f0-105">L'articolo definisce i rischi in fase iniziale, le istruzioni iniziali sui criteri e i processi anticipati per applicare le istruzioni dei criteri.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-105">This article defines early-stage risks, initial policy statements, and early processes to enforce policy statements.</span></span>

> [!NOTE]
><span data-ttu-id="4c6f0-106">I criteri aziendali non costituiscono un documento tecnico, ma guidano molte decisioni tecniche.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-106">The corporate policy is not a technical document, but it drives many technical decisions.</span></span> <span data-ttu-id="4c6f0-107">La governance MVP descritta nella [panoramica](./overview.md) deriva in definitiva da questo criterio.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-107">The governance MVP described in the [overview](./overview.md) ultimately derives from this policy.</span></span> <span data-ttu-id="4c6f0-108">Prima di implementare una governance MVP, l'organizzazione dovrebbe sviluppare un criterio aziendale in base ai propri obiettivi e rischi aziendali.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-108">Before implementing a governance MVP, your organization should develop a corporate policy based on your own objectives and business risks.</span></span>

## <a name="cloud-governance-team"></a><span data-ttu-id="4c6f0-109">Team di Governance cloud</span><span class="sxs-lookup"><span data-stu-id="4c6f0-109">Cloud Governance team</span></span>

<span data-ttu-id="4c6f0-110">In questo esempio pratico, il team di Governance cloud comprende due amministratori di sistema che hanno riconosciuto la necessità di governance.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-110">In this narrative, the Cloud Governance team is comprised of two systems administrators who have recognized the need for governance.</span></span> <span data-ttu-id="4c6f0-111">Nei prossimi mesi, erediteranno il processo di pulizia della governance della presenza cloud della società, guadagnandosi il titolo di Custodi del cloud.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-111">Over the next several months, they will inherit the job of cleaning up the governance of the company’s cloud presence, earning them the title of Cloud Custodians.</span></span> <span data-ttu-id="4c6f0-112">Nelle successive evoluzioni, questo titolo verrà probabilmente modificato.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-112">In subsequent evolutions, this title will likely change.</span></span>

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a><span data-ttu-id="4c6f0-113">Indicatori sulla tolleranza</span><span class="sxs-lookup"><span data-stu-id="4c6f0-113">Tolerance indicators</span></span>

<span data-ttu-id="4c6f0-114">L'attuale tolleranza ai rischi è elevata e il desiderio di investire nella governance cloud è basso.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-114">The current tolerance for risk is high and the appetite for investing in cloud governance is low.</span></span> <span data-ttu-id="4c6f0-115">Di conseguenza, gli indicatori di tolleranza agiscono come un sistema di avviso preventivo per attivare più investimenti di tempo ed energia.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-115">As such, the tolerance indicators act as an early warning system to trigger more investment of time and energy.</span></span> <span data-ttu-id="4c6f0-116">Se e quando si osservano gli indicatori seguenti, sarebbe necessario evolvere la strategia di governance.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-116">If and when the following indicators are observed, you should evolve the governance strategy.</span></span>

- <span data-ttu-id="4c6f0-117">Gestione costi: La scala di distribuzione supera le 100 risorse nel cloud oppure la spesa mensile supera i 1.000 USD al mese.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-117">Cost Management: The scale of deployment exceeds 100 assets to the cloud, or monthly spending exceeds $1,000 USD per month.</span></span>
- <span data-ttu-id="4c6f0-118">Baseline di sicurezza: Inclusione dei dati protetti nei piani di adozione definiti per il cloud.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-118">Security Baseline: Inclusion of protected data in defined cloud adoption plans.</span></span>
- <span data-ttu-id="4c6f0-119">Coerenza delle risorse: Inclusione di tutte le applicazioni di importanza cruciale nei piani di adozione definiti per il cloud.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-119">Resource Consistency: Inclusion of any mission-critical applications in defined cloud adoption plans.</span></span>

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a><span data-ttu-id="4c6f0-120">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="4c6f0-120">Next steps</span></span>

<span data-ttu-id="4c6f0-121">Questo criterio aziendale prepara il team di Governance cloud a implementare la governance MVP, che verrà usata come base per l'adozione.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-121">This corporate policy prepares the Cloud Governance team to implement the governance MVP, which will be the foundation for adoption.</span></span> <span data-ttu-id="4c6f0-122">Il passaggio successivo consiste nell'implementazione di tale MVP.</span><span class="sxs-lookup"><span data-stu-id="4c6f0-122">The next step is to implement this MVP.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="4c6f0-123">Spiegazione delle procedure consigliate</span><span class="sxs-lookup"><span data-stu-id="4c6f0-123">Best practice explained</span></span>](./best-practice-explained.md)