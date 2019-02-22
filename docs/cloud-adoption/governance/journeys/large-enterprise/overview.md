---
title: 'CAF: Percorso di governance per aziende di grandi dimensioni'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Percorso di governance per aziende di grandi dimensioni
author: BrianBlanchard
ms.openlocfilehash: 689b600524c3be6c505ade8c5aaa447d772c6e35
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901335"
---
# <a name="caf-large-enterprise-governance-journey"></a><span data-ttu-id="c984b-103">CAF: Percorso di governance per aziende di grandi dimensioni</span><span class="sxs-lookup"><span data-stu-id="c984b-103">CAF: Large enterprise governance journey</span></span>

## <a name="best-practice-overview"></a><span data-ttu-id="c984b-104">Panoramica delle procedure consigliate</span><span class="sxs-lookup"><span data-stu-id="c984b-104">Best practice overview</span></span>

<span data-ttu-id="c984b-105">Questo percorso di governance segue le esperienze di una società fittizia attraverso le diverse fasi di evoluzione della governance.</span><span class="sxs-lookup"><span data-stu-id="c984b-105">This governance journey follows the experiences of a fictional company through various stages of governance maturity.</span></span> <span data-ttu-id="c984b-106">Il percorso è basato su percorsi di clienti reali.</span><span class="sxs-lookup"><span data-stu-id="c984b-106">It is based on real customer journeys.</span></span> <span data-ttu-id="c984b-107">Le procedure consigliate si basano sui vincoli e sulle esigenze della società fittizia.</span><span class="sxs-lookup"><span data-stu-id="c984b-107">The suggested best practices are based on the constraints and needs of the fictional company.</span></span>

<span data-ttu-id="c984b-108">Come punto di partenza rapido, questa panoramica definisce un prodotto minimo funzionante (MVP) per la governance basato su procedure consigliate.</span><span class="sxs-lookup"><span data-stu-id="c984b-108">As a quick starting point, this overview defines a minimum viable product (MVP) for governance based on best practices.</span></span> <span data-ttu-id="c984b-109">Vengono inoltre forniti collegamenti ad alcune forme di evoluzione della governance che offrono altre procedure consigliate in base all'emergere di nuovi rischi tecnici o aziendali.</span><span class="sxs-lookup"><span data-stu-id="c984b-109">It also provides links to some governance evolutions that add further best practices as new business or technical risks emerge.</span></span>

> [!WARNING]
> <span data-ttu-id="c984b-110">Questo MVP è solo un punto di partenza, fondato su un insieme di ipotesi.</span><span class="sxs-lookup"><span data-stu-id="c984b-110">This MVP is a baseline starting point, based on a set of assumptions.</span></span> <span data-ttu-id="c984b-111">Anche questo insieme minimo di procedure consigliate è basato su criteri aziendali determinati da rischi commerciali e tolleranze di rischio speciali.</span><span class="sxs-lookup"><span data-stu-id="c984b-111">Even this minimal set of best practices is based on corporate policies driven by unique business risks and risk tolerances.</span></span> <span data-ttu-id="c984b-112">Per determinare se queste ipotesi si applicano alla propria situazione, vedere lo [scenario più approfondito](./narrative.md) che segue questo articolo.</span><span class="sxs-lookup"><span data-stu-id="c984b-112">To see if these assumptions apply to you, read the [longer narrative](./narrative.md) that follows this article.</span></span>

### <a name="governance-best-practice"></a><span data-ttu-id="c984b-113">Procedura consigliata per la governance</span><span class="sxs-lookup"><span data-stu-id="c984b-113">Governance best practice</span></span>

<span data-ttu-id="c984b-114">Questa procedura consigliata funge da base di cui può servirsi un'organizzazione per aggiungere in modo rapido e coerente misure di tutela per la governance tra più sottoscrizioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="c984b-114">This best practice serves as a foundation that an organization can use to quickly and consistently add governance guardrails across multiple Azure subscriptions.</span></span>

### <a name="resource-organization"></a><span data-ttu-id="c984b-115">Organizzazione delle risorse</span><span class="sxs-lookup"><span data-stu-id="c984b-115">Resource organization</span></span>

<span data-ttu-id="c984b-116">Il diagramma seguente mostra la gerarchia dell'MVP per la governance per organizzare le risorse.</span><span class="sxs-lookup"><span data-stu-id="c984b-116">The following diagram shows the governance MVP hierarchy for organizing resources.</span></span>

![Diagramma di organizzazione delle risorse](../../../_images/governance/resource-organization.png)

<span data-ttu-id="c984b-118">Tutte le applicazioni devono essere distribuite nell'area appropriata della gerarchia di gruppi di gestione, sottoscrizioni e gruppi di risorse.</span><span class="sxs-lookup"><span data-stu-id="c984b-118">Every application should be deployed in the proper area of the management group, subscription, and resource group hierarchy.</span></span> <span data-ttu-id="c984b-119">Durante la pianificazione della distribuzione, il team di governance del cloud creerà i nodi necessari nella gerarchia per i team responsabili dell'adozione del cloud.</span><span class="sxs-lookup"><span data-stu-id="c984b-119">During deployment planning, the Cloud Governance team will create the necessary nodes in the hierarchy to empower the cloud adoption teams.</span></span>

1. <span data-ttu-id="c984b-120">Un gruppo di gestione per ogni business unit con una gerarchia dettagliata che rifletta geografia, e tipo di ambiente (produzione, non di produzione).</span><span class="sxs-lookup"><span data-stu-id="c984b-120">A management group for each business unit with a detailed hierarchy that reflects geography then environment type (Production, Non-Production).</span></span>
2. <span data-ttu-id="c984b-121">Una sottoscrizione per ogni combinazione univoca di business unit, geografia, ambiente e "Categorizzazione delle applicazioni".</span><span class="sxs-lookup"><span data-stu-id="c984b-121">A subscription for each unique combination of business unit, geography, environment, and "Application Categorization."</span></span>
3. <span data-ttu-id="c984b-122">Un gruppo di risorse separato per ogni applicazione.</span><span class="sxs-lookup"><span data-stu-id="c984b-122">A separate resource group for each application.</span></span>
4. <span data-ttu-id="c984b-123">È necessario applicare una nomenclatura coerente a ogni livello di questa gerarchia di gruppi.</span><span class="sxs-lookup"><span data-stu-id="c984b-123">Consistent nomenclature should be applied at each level of this grouping hierarchy.</span></span>

![Diagramma di organizzazione delle risorse per le grandi imprese](../../../_images/governance/large-enterprise-resource-organization.png)

<span data-ttu-id="c984b-125">Questi modelli lasciano un certo spazio per la crescita senza complicare inutilmente la gerarchia.</span><span class="sxs-lookup"><span data-stu-id="c984b-125">These patterns provide room for growth without complicating the hierarchy unnecessarily.</span></span>

[!INCLUDE [governance-of-resources](../../../../../includes/cloud-adoption/governance/governance-of-resources.md)]

## <a name="governance-evolutions"></a><span data-ttu-id="c984b-126">Evoluzioni della governance</span><span class="sxs-lookup"><span data-stu-id="c984b-126">Governance evolutions</span></span>

<span data-ttu-id="c984b-127">Una volta distribuito l'MVP, è possibile integrare livelli aggiuntivi di governance nell'ambiente.</span><span class="sxs-lookup"><span data-stu-id="c984b-127">Once this MVP has been deployed, additional layers of governance can be quickly incorporated into the environment.</span></span> <span data-ttu-id="c984b-128">Ecco alcuni modi in cui trasformare l'MVP per soddisfare esigenze aziendali specifiche:</span><span class="sxs-lookup"><span data-stu-id="c984b-128">Here are some ways to evolve the MVP to meet specific business needs:</span></span>

- [<span data-ttu-id="c984b-129">Baseline di sicurezza dei dati protetti</span><span class="sxs-lookup"><span data-stu-id="c984b-129">Security Baseline for protected data</span></span>](./security-baseline-evolution.md)
- [<span data-ttu-id="c984b-130">Configurazioni delle risorse per applicazioni cruciali</span><span class="sxs-lookup"><span data-stu-id="c984b-130">Resource configurations for mission-critical applications</span></span>](./resource-consistency-evolution.md)
- [<span data-ttu-id="c984b-131">Controlli per Gestione dei costi</span><span class="sxs-lookup"><span data-stu-id="c984b-131">Controls for Cost Management</span></span>](./cost-management-evolution.md)
- [<span data-ttu-id="c984b-132">Controlli per l'evoluzione multi-cloud</span><span class="sxs-lookup"><span data-stu-id="c984b-132">Controls for multi-cloud evolution</span></span>](./multi-cloud-evolution.md)

<!-- markdownlint-disable MD026 -->

## <a name="what-does-this-best-practice-do"></a><span data-ttu-id="c984b-133">Qual è lo scopo di questa procedura consigliata?</span><span class="sxs-lookup"><span data-stu-id="c984b-133">What does this best practice do?</span></span>

<span data-ttu-id="c984b-134">Nell'MVP vengono definiti strumenti e procedure della disciplina di [accelerazione della distribuzione](../../deployment-acceleration/overview.md) per applicare rapidamente criteri aziendali.</span><span class="sxs-lookup"><span data-stu-id="c984b-134">In the MVP, practices and tools from the [Deployment Acceleration](../../deployment-acceleration/overview.md) discipline are established to quickly apply corporate policy.</span></span> <span data-ttu-id="c984b-135">In particolare, l'MVP usa Azure Blueprints, Criteri di Azure e gruppi di gestione di Azure per applicare alcuni criteri aziendali di base, secondo quanto definito nello scenario per questa società fittizia.</span><span class="sxs-lookup"><span data-stu-id="c984b-135">In particular, the MVP uses Azure Blueprints, Azure Policy, and Azure management groups to apply a few basic corporate policies, as defined in the narrative for this fictional company.</span></span> <span data-ttu-id="c984b-136">Questi criteri aziendali vengono applicati tramite modelli di Resource Manager e criteri di Azure per stabilire una baseline minima per identità e sicurezza.</span><span class="sxs-lookup"><span data-stu-id="c984b-136">Those corporate policies are applied using Azure Resource Manager templates and Azure policies to establish a very small baseline for identity and security.</span></span>

![Esempio di MVP per una governance incrementale](../../../_images/governance/governance-mvp.png)

## <a name="evolving-the-best-practice"></a><span data-ttu-id="c984b-138">Evoluzione della procedura consigliata</span><span class="sxs-lookup"><span data-stu-id="c984b-138">Evolving the best practice</span></span>

<span data-ttu-id="c984b-139">Con il passare del tempo, questo MVP per la governance verrà usato per adeguare le procedure necessarie.</span><span class="sxs-lookup"><span data-stu-id="c984b-139">Over time, this governance MVP will be used to evolve the governance practices.</span></span> <span data-ttu-id="c984b-140">Con il progredire dell'adozione, aumentano i rischi aziendali.</span><span class="sxs-lookup"><span data-stu-id="c984b-140">As adoption advances, business risk grows.</span></span> <span data-ttu-id="c984b-141">Diverse discipline all'interno del modello di governance del framework per l'adozione del cloud evolveranno per mitigare questi rischi.</span><span class="sxs-lookup"><span data-stu-id="c984b-141">Various disciplines within the CAF governance model will evolve to mitigate those risks.</span></span> <span data-ttu-id="c984b-142">Gli articoli successivi di questa serie illustrano l'evoluzione di criteri aziendali che interessano la società fittizia.</span><span class="sxs-lookup"><span data-stu-id="c984b-142">Later articles in this series discuss the evolution of corporate policy affecting the fictional company.</span></span> <span data-ttu-id="c984b-143">Queste evoluzioni avvengono in tre discipline:</span><span class="sxs-lookup"><span data-stu-id="c984b-143">These evolutions happen across three disciplines:</span></span>

- <span data-ttu-id="c984b-144">Baseline di identità, con le dipendenze della migrazione che si evolvono nel testo descrittivo</span><span class="sxs-lookup"><span data-stu-id="c984b-144">Identity Baseline, as migration dependencies evolve in the narrative</span></span>
- <span data-ttu-id="c984b-145">Gestione dei costi, con il ridimensionarsi dell'adozione.</span><span class="sxs-lookup"><span data-stu-id="c984b-145">Cost Management, as adoption scales.</span></span>
- <span data-ttu-id="c984b-146">Baseline di sicurezza, man mano che vengono distribuiti dati protetti.</span><span class="sxs-lookup"><span data-stu-id="c984b-146">Security Baseline, as protected data is deployed.</span></span>
- <span data-ttu-id="c984b-147">Coerenza delle risorse, man mano che il team responsabile delle operazioni IT inizia a supportare carichi di lavoro cruciali.</span><span class="sxs-lookup"><span data-stu-id="c984b-147">Resource Consistency, as IT Operations begins supporting mission-critical workloads.</span></span>

![Esempio di MVP per una governance incrementale](../../../_images/governance/governance-evolution-large.png)

## <a name="next-steps"></a><span data-ttu-id="c984b-149">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="c984b-149">Next steps</span></span>

<span data-ttu-id="c984b-150">Ora che si ha familiarità con l'MVP per la governance e un'idea delle evoluzioni della governance da seguire, consultare lo scenario associato per altre informazioni sul contesto.</span><span class="sxs-lookup"><span data-stu-id="c984b-150">Now that you’re familiar with the governance MVP and have an idea of the governance evolutions to follow, read the supporting narrative for additional context.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c984b-151">Consultare lo scenario associato</span><span class="sxs-lookup"><span data-stu-id="c984b-151">Read the supporting narrative</span></span>](./narrative.md)
