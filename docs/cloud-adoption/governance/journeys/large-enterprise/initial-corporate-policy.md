---
title: 'CAF: Azienda di grandi dimensioni - Criteri aziendali iniziali per la strategia di governance'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: Azienda di grandi dimensioni - Criteri aziendali iniziali per la strategia di governance.
author: BrianBlanchard
ms.openlocfilehash: d3bc31f95229a82cbc2f6ac6e684a87107783c07
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902011"
---
# <a name="large-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a><span data-ttu-id="e04af-103">Aziende di grandi dimensioni: Criteri aziendali iniziali per la strategia di governance</span><span class="sxs-lookup"><span data-stu-id="e04af-103">Large enterprise: Initial corporate policy behind the governance strategy</span></span>

<span data-ttu-id="e04af-104">I criteri aziendali seguenti definiscono la posizione iniziale di governance, ovvero il punto di partenza per questo percorso.</span><span class="sxs-lookup"><span data-stu-id="e04af-104">The following corporate policy defines the initial governance position, which is the starting point for this journey.</span></span> <span data-ttu-id="e04af-105">L'articolo definisce i rischi in fase iniziale, le istruzioni iniziali sui criteri, i processi anticipati per applicare le istruzioni dei criteri.</span><span class="sxs-lookup"><span data-stu-id="e04af-105">This article defines early-stage risks, initial policy statements, and early processes to enforce policy statements.</span></span>

> [!NOTE]
><span data-ttu-id="e04af-106">I criteri aziendali non costituiscono un documento tecnico, ma guidano molte decisioni tecniche.</span><span class="sxs-lookup"><span data-stu-id="e04af-106">The corporate policy is not a technical document, but it drives many technical decisions.</span></span> <span data-ttu-id="e04af-107">La governance MVP descritta nella [panoramica](./overview.md) deriva in definitiva da questo criterio.</span><span class="sxs-lookup"><span data-stu-id="e04af-107">The governance MVP described in the [overview](./overview.md) ultimately derives from this policy.</span></span> <span data-ttu-id="e04af-108">Prima di implementare una governance MVP, l'organizzazione dovrebbe sviluppare un criterio aziendale in base ai propri obiettivi e rischi aziendali.</span><span class="sxs-lookup"><span data-stu-id="e04af-108">Before implementing a governance MVP, your organization should develop a corporate policy based on your own objectives and business risks.</span></span>

## <a name="cloud-governance-team"></a><span data-ttu-id="e04af-109">Team di Governance cloud</span><span class="sxs-lookup"><span data-stu-id="e04af-109">Cloud Governance team</span></span>

<span data-ttu-id="e04af-110">Il CIO ha recentemente tenuto una riunione con il team IT di governance per comprendere la cronologia delle informazioni personali e i criteri di importanza cruciale, oltre a esaminare l'impatto della modifica di tali criteri.</span><span class="sxs-lookup"><span data-stu-id="e04af-110">The CIO recently held a meeting with the IT Governance team to understand the history of the PII and mission-critical policies and review the impact of changing those policies.</span></span> <span data-ttu-id="e04af-111">Ha anche illustrato il potenziale complessivo del cloud per il settore IT e l'azienda.</span><span class="sxs-lookup"><span data-stu-id="e04af-111">She also discussed the overall potential of the cloud for IT and the company.</span></span>

<span data-ttu-id="e04af-112">Dopo la riunione, due membri del team IT di governance hanno richiesto l'autorizzazione per ricercare e supportare gli sforzi di pianificazione del cloud.</span><span class="sxs-lookup"><span data-stu-id="e04af-112">After the meeting, two members of the IT Governance team requested permission to research and support the cloud planning efforts.</span></span> <span data-ttu-id="e04af-113">Il direttore di governance IT ha supportato questo concetto, riconoscendo l'esigenza di governance e la possibilità per limitare lo shadow IT.</span><span class="sxs-lookup"><span data-stu-id="e04af-113">Recognizing the need for governance and an opportunity to limit shadow IT, the Director of IT Governance supported this idea.</span></span> <span data-ttu-id="e04af-114">Il team di Governance cloud è nato con questa idea.</span><span class="sxs-lookup"><span data-stu-id="e04af-114">With that, the Cloud Governance team was born.</span></span> <span data-ttu-id="e04af-115">Nei mesi successivi, si erediterà la pulizia di molti errori commessi durante l'esplorazione nel cloud da una prospettiva di governance.</span><span class="sxs-lookup"><span data-stu-id="e04af-115">Over the next several months, they will inherit the cleanup of many mistakes made during exploration in the cloud from a governance perspective.</span></span> <span data-ttu-id="e04af-116">Ciò consentirà di guadagnarsi il moniker dei custodi cloud.</span><span class="sxs-lookup"><span data-stu-id="e04af-116">This will earn them the moniker of Cloud Custodians.</span></span> <span data-ttu-id="e04af-117">In evoluzioni successive, questo percorso illustrerà in che modo i relativi ruoli cambiano nel tempo.</span><span class="sxs-lookup"><span data-stu-id="e04af-117">In later evolutions, this journey will show how their roles change over time.</span></span>

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a><span data-ttu-id="e04af-118">Indicatori sulla tolleranza</span><span class="sxs-lookup"><span data-stu-id="e04af-118">Tolerance indicators</span></span>

<span data-ttu-id="e04af-119">L'attuale tolleranza ai rischi è elevata e il desiderio di investire nella governance cloud è basso.</span><span class="sxs-lookup"><span data-stu-id="e04af-119">The current risk tolerance is high and the appetite for investing in cloud governance is low.</span></span> <span data-ttu-id="e04af-120">Di conseguenza, gli indicatori di tolleranza agiscono come un sistema di avviso preventivo per attivare l'investimento di tempo ed energia.</span><span class="sxs-lookup"><span data-stu-id="e04af-120">As such, the tolerance indicators act as an early warning system to trigger the investment of time and energy.</span></span> <span data-ttu-id="e04af-121">Se si osservano gli indicatori seguenti, sarebbe opportuno evolvere la strategia di governance.</span><span class="sxs-lookup"><span data-stu-id="e04af-121">If the following indicators are observed, it would be wise to evolve the governance strategy.</span></span>

- <span data-ttu-id="e04af-122">Gestione costi: la scala di distribuzione supera le 1.000 risorse nel cloud oppure la spesa mensile supera i USD 10.000 al mese.</span><span class="sxs-lookup"><span data-stu-id="e04af-122">Cost Management: Scale of deployment exceeds 1,000 assets to the cloud, or monthly spending exceeds $10,000 USD per month.</span></span>
- <span data-ttu-id="e04af-123">Baseline di identità: inclusione di applicazioni con requisiti di autenticazione legacy o a più fattori di terze parti (MFA).</span><span class="sxs-lookup"><span data-stu-id="e04af-123">Identity Baseline: Inclusion of applications with legacy or third-party multifactor authentication (MFA) requirements.</span></span>
- <span data-ttu-id="e04af-124">Baseline di sicurezza: inclusione dei dati protetti nei piani di adozione definiti per il cloud.</span><span class="sxs-lookup"><span data-stu-id="e04af-124">Security Baseline: Inclusion of protected data in defined cloud adoption plans.</span></span>
- <span data-ttu-id="e04af-125">Coerenza delle risorse: inclusione di tutte le applicazioni di importanza cruciale nei piani di adozione definiti per il cloud.</span><span class="sxs-lookup"><span data-stu-id="e04af-125">Resource Consistency: Inclusion of any mission-critical applications in defined cloud adoption plans.</span></span>

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a><span data-ttu-id="e04af-126">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="e04af-126">Next steps</span></span>

<span data-ttu-id="e04af-127">Questo criterio aziendale prepara il team di Governance cloud a implementare la governance MVP, che verrà usata come base per l'adozione.</span><span class="sxs-lookup"><span data-stu-id="e04af-127">This corporate policy prepares the Cloud Governance team to implement the governance MVP, which will be the foundation for adoption.</span></span> <span data-ttu-id="e04af-128">Il passaggio successivo consiste nell'implementazione di tale MVP.</span><span class="sxs-lookup"><span data-stu-id="e04af-128">The next step is to implement this MVP.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="e04af-129">Best practice explained (Spiegazione delle procedure consigliate)</span><span class="sxs-lookup"><span data-stu-id="e04af-129">Best practice explained</span></span>](./best-practice-explained.md)