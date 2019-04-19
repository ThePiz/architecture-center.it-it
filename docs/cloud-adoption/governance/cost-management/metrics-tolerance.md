---
title: 'CAF: Metriche sulla Gestione costi, indicatori e tolleranza ai rischi'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Spiegazione sulla Gestione costi in relazione alla governance cloud
author: BrianBlanchard
ms.openlocfilehash: 76e6b1b32dd862322f6cafd9aa63c6c4f79f4f5d
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241732"
---
# <a name="cost-management-metrics-indicators-and-risk-tolerance"></a><span data-ttu-id="d2c40-103">Metriche della Gestione costi, indicatori e tolleranza ai rischi</span><span class="sxs-lookup"><span data-stu-id="d2c40-103">Cost Management metrics, indicators, and risk tolerance</span></span>

<span data-ttu-id="d2c40-104">Questo articolo è concepito per aiutare l'utente a quantificare la tolleranza ai rischi aziendali, in relazione alla Gestione costi.</span><span class="sxs-lookup"><span data-stu-id="d2c40-104">This article is intended to help you quantify business risk tolerance as it relates to Cost Management.</span></span> <span data-ttu-id="d2c40-105">La definizione di metriche e indicatori consente di creare un business case per effettuare un investimento riguardo alla disciplina Gestione costi.</span><span class="sxs-lookup"><span data-stu-id="d2c40-105">Defining metrics and indicators helps you create a business case for making an investment in the maturity of the Cost Management discipline.</span></span>

## <a name="metrics"></a><span data-ttu-id="d2c40-106">Metriche</span><span class="sxs-lookup"><span data-stu-id="d2c40-106">Metrics</span></span>

<span data-ttu-id="d2c40-107">Il servizio Gestione costi generalmente è incentrato sulle metriche relative ai costi.</span><span class="sxs-lookup"><span data-stu-id="d2c40-107">Cost Management generally focuses on metrics related to costs.</span></span> <span data-ttu-id="d2c40-108">Come parte dell'analisi dei rischi, è necessario raccogliere dati relativi alle proprie spese correnti e pianificate sui carichi di lavoro basati sul cloud per determinare il livello di rischio da affrontare e quanto sia importante investire nella governance dei costi come strategia di adozione del cloud.</span><span class="sxs-lookup"><span data-stu-id="d2c40-108">As part of your risk analysis, you'll want to gather data related to your current and planned spending on cloud-based workloads to determine how much risk you face, and how important investment in cost governance is to your cloud adoption strategy.</span></span>

<span data-ttu-id="d2c40-109">Sono riportati alcuni esempi di metriche utili che è necessario raccogliere per valutare la tolleranza ai rischi all'interno della disciplina Baseline di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="d2c40-109">The following are examples of useful metrics that you should gather to help evaluate risk tolerance within the Security Baseline discipline:</span></span>

- <span data-ttu-id="d2c40-110">Spesa annuale: costo totale annuale dei servizi forniti da un provider di servizi cloud</span><span class="sxs-lookup"><span data-stu-id="d2c40-110">Annual spending: The total annual cost for services provided by a cloud provider</span></span>
- <span data-ttu-id="d2c40-111">Spesa mensile: costo totale mensile dei servizi forniti da un provider di servizi cloud</span><span class="sxs-lookup"><span data-stu-id="d2c40-111">Monthly spending: The total monthly cost for services provided by a cloud provider</span></span>
- <span data-ttu-id="d2c40-112">Percentuale prevista rispetto a quella effettiva: il rapporto è dato dal confronto tra la spesa prevista e quella effettiva (mensile o annuale)</span><span class="sxs-lookup"><span data-stu-id="d2c40-112">Forecasted versus actual ratio: The ratio comparing forecasted and actual spending (monthly or annual)</span></span>
- <span data-ttu-id="d2c40-113">Evoluzione della percentuale di adozione (MOM): percentuale dei delta nei costi del cloud su base mensile</span><span class="sxs-lookup"><span data-stu-id="d2c40-113">Pace of adoption (MOM) ratio: The percentage of the delta in cloud costs from month to month</span></span>
- <span data-ttu-id="d2c40-114">Costo accumulato: spese giornaliere accumulate totali, a partire dall'inizio del mese</span><span class="sxs-lookup"><span data-stu-id="d2c40-114">Accumulated cost: Total accrued daily spending, starting from the beginning of the month</span></span>
- <span data-ttu-id="d2c40-115">Tendenze di spesa: tendenza di spesa rispetto al budget</span><span class="sxs-lookup"><span data-stu-id="d2c40-115">Spending trends: Spending trend against the budget</span></span>

## <a name="risk-tolerance-indicators"></a><span data-ttu-id="d2c40-116">Indicatori sulla tolleranza ai rischi</span><span class="sxs-lookup"><span data-stu-id="d2c40-116">Risk tolerance indicators</span></span>

<span data-ttu-id="d2c40-117">Durante le distribuzioni iniziali, ad esempio Sviluppo/test o primi carichi di lavoro sperimentali, Gestione costi probabilmente comporta un rischio relativamente ridotto.</span><span class="sxs-lookup"><span data-stu-id="d2c40-117">During early deployments, such as Dev/Test or experimental first workloads, Cost Management is likely to be of relatively low risk.</span></span> <span data-ttu-id="d2c40-118">Dato che vengono distribuite più risorse, il rischio aumenta e la tolleranza delle aziende ai rischi probabilmente diminuisce.</span><span class="sxs-lookup"><span data-stu-id="d2c40-118">As more assets are deployed, the risk grows and the business' tolerance for risk is likely to decline.</span></span> <span data-ttu-id="d2c40-119">Inoltre, visto che altri team che adottano il cloud hanno la possibilità di configurare o distribuire le risorse nel cloud, il rischio aumenta e la tolleranza diminuisce.</span><span class="sxs-lookup"><span data-stu-id="d2c40-119">Additionally, as more cloud adoption teams are given the ability to configure or deploy assets to the cloud, the risk grows and tolerance decreases.</span></span> <span data-ttu-id="d2c40-120">Al contrario, l'aumento della disciplina della Gestione costi porterà gli utenti dalla fase di adozione del cloud alla distribuzione di nuove tecnologie più innovative.</span><span class="sxs-lookup"><span data-stu-id="d2c40-120">Conversely, growing a Cost Management discipline will take people from the cloud adoption phase to deploy more innovative new technologies.</span></span>

<span data-ttu-id="d2c40-121">Nelle prime fasi di adozione del cloud, si dovrà stabilire una baseline di tolleranza ai rischi insieme alla propria azienda.</span><span class="sxs-lookup"><span data-stu-id="d2c40-121">In the early stages of cloud adoption, you will work with your business to determine a risk tolerance baseline.</span></span> <span data-ttu-id="d2c40-122">Dopo aver creato una baseline, è necessario determinare i criteri che attiverebbero un investimento nella disciplina della Gestione costi.</span><span class="sxs-lookup"><span data-stu-id="d2c40-122">Once you have a baseline, you will need to determine the criteria that would trigger an investment in the Cost Management discipline.</span></span> <span data-ttu-id="d2c40-123">Questi criteri saranno probabilmente diversi per ogni organizzazione.</span><span class="sxs-lookup"><span data-stu-id="d2c40-123">These criteria will likely be different for every organization.</span></span>

<span data-ttu-id="d2c40-124">Dopo aver identificato [i rischi aziendali](./business-risks.md), si useranno con la propria azienda per identificare i benchmark che è possibile usare per identificare i trigger che potrebbero potenzialmente incrementare tali rischi.</span><span class="sxs-lookup"><span data-stu-id="d2c40-124">Once you have identified [business risks](./business-risks.md), you will work with your business to identify benchmarks that you can use to identify triggers that could potentially increase those risks.</span></span> <span data-ttu-id="d2c40-125">Sono indicati alcuni esempi del modo in cui le metriche, ad esempio quelle precedentemente indicate, possono essere confrontate con la tolleranza ai rischi della baseline per indicare la necessità dell'azienda di investire ancora di più in Gestione costi.</span><span class="sxs-lookup"><span data-stu-id="d2c40-125">The following are a few examples of how metrics, such as those mentioned above, can be compared against your risk baseline tolerance to indicate your business's need to further invest in Cost Management.</span></span>

- <span data-ttu-id="d2c40-126">Basato sull'impegno (il più comune): Un'azienda che quest'anno s'impegna a spendere $ X.000.000 per un fornitore di cloud.</span><span class="sxs-lookup"><span data-stu-id="d2c40-126">Commitment-driven (most common): A company that is committed to spending $X,000,000 this year on a cloud vendor.</span></span> <span data-ttu-id="d2c40-127">È necessaria una disciplina di Gestione costi per assicurarsi che l'azienda non superi i relativi obiettivi di spesa di più del 20% e che venga usato almeno il 90% di tale impegno.</span><span class="sxs-lookup"><span data-stu-id="d2c40-127">They need a Cost Management discipline to ensure that the business doesn't exceed its spending targets by more than 20%, and that they will use at least 90% of that commitment.</span></span>
- <span data-ttu-id="d2c40-128">Trigger di percentuale: Un'azienda con una spesa per il cloud che è stabile per i sistemi di produzione.</span><span class="sxs-lookup"><span data-stu-id="d2c40-128">Percentage trigger: A company with cloud spending that is stable for their production systems.</span></span> <span data-ttu-id="d2c40-129">In caso di modifiche di più del X%, una disciplina di Gestione costi sarà un saggio investimento.</span><span class="sxs-lookup"><span data-stu-id="d2c40-129">If that changes by more that X%, then a Cost Management discipline will be a wise investment.</span></span>
- <span data-ttu-id="d2c40-130">Trigger con provisioning eccessivo: Una società che ritiene che le soluzioni distribuite abbiano un provisioning eccessivo.</span><span class="sxs-lookup"><span data-stu-id="d2c40-130">Overprovisioned trigger: A company who believes their deployed solutions are overprovisioned.</span></span> <span data-ttu-id="d2c40-131">Gestione costi è un investimento prioritario fino a quando non è possibile illustrare l'allineamento corretto dell'uso di provisioning e risorse.</span><span class="sxs-lookup"><span data-stu-id="d2c40-131">Cost Management is a priority investment until they can demonstrate proper alignment of provisioning and asset utilization.</span></span>
- <span data-ttu-id="d2c40-132">Trigger della spesa mensile: Un'azienda che spende più di $ X.000 al mese, in cui tale importo viene considerato un costo considerevole.</span><span class="sxs-lookup"><span data-stu-id="d2c40-132">Monthly spending trigger: A company that spends over $x,000 per month is considered a sizable cost.</span></span> <span data-ttu-id="d2c40-133">Se la spesa supera tale valore in un determinato mese, sarà necessario investire in Gestione costi.</span><span class="sxs-lookup"><span data-stu-id="d2c40-133">If spending exceeds that amount in a given month, they will need to invest in Cost Management.</span></span>
- <span data-ttu-id="d2c40-134">Trigger della spesa annuale: Un'azienda con un budget IT destinato a ricerca e sviluppo che consente una spesa annuale di $ X.000 nella sperimentazione di cloud.</span><span class="sxs-lookup"><span data-stu-id="d2c40-134">Annual spending trigger: A company with an IT R&D budget that allows for spending $X,000 per year on cloud experimentation.</span></span> <span data-ttu-id="d2c40-135">Possono eseguire carichi di lavoro di produzione nel cloud, ma vengono comunque considerati soluzioni sperimentali se il budget non supera tale importo.</span><span class="sxs-lookup"><span data-stu-id="d2c40-135">They may run production workloads in the cloud, but they will still be considered experimental solutions if the budget doesn't exceed that amount.</span></span> <span data-ttu-id="d2c40-136">In caso di superamento, è necessario trattare il budget come un investimento di produzione e gestire la spesa minuziosamente.</span><span class="sxs-lookup"><span data-stu-id="d2c40-136">Once it goes over, they will need to treat the budget like a production investment and manage spending closely.</span></span>
- <span data-ttu-id="d2c40-137">Spesa operativa negativa (non comune): Un'azienda è molto contraria alle spesa operativa e deve controllare Gestione costi in vigore, prima della distribuzione di un flusso di lavoro Sviluppo/test.</span><span class="sxs-lookup"><span data-stu-id="d2c40-137">OpEx adverse (uncommon): As a company, they are very OpEx adverse and will need Cost Management controls in place before deploying a dev/test workload.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d2c40-138">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="d2c40-138">Next steps</span></span>

<span data-ttu-id="d2c40-139">Usare il [Cloud Management template (modello Gestione cloud)](./template.md), documentare le metriche e gli indicatori di tolleranza ai rischi che si allineano al piano di adozione del cloud attuale.</span><span class="sxs-lookup"><span data-stu-id="d2c40-139">Using the [Cloud Management template](./template.md), document metrics and tolerance indicators that align to the current cloud adoption plan.</span></span>

<span data-ttu-id="d2c40-140">Sulla base dei rischi e della tolleranza, stabilire un processo per controllare e comunicare la conformità ai criteri di Gestione costi.</span><span class="sxs-lookup"><span data-stu-id="d2c40-140">Building on risks and tolerance, establish a process for governing and communicating Cost Management policy adherence.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d2c40-141">Establish policy compliance processes (Stabilire i processi di conformità ai criteri)</span><span class="sxs-lookup"><span data-stu-id="d2c40-141">Establish policy compliance processes</span></span>](compliance-processes.md)