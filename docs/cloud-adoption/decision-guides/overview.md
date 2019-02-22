---
title: "CAF: Guide all'architettura decisionale"
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sulle guide all'architettura decisionale nel Framework di adozione del Cloud.
author: rotycenh
ms.openlocfilehash: b43047b5fc5b636bc84b9f28ec24846730e63672
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901956"
---
# <a name="architectural-decision-guides"></a><span data-ttu-id="db2b1-103">Guide all'architettura decisionale</span><span class="sxs-lookup"><span data-stu-id="db2b1-103">Architectural decision guides</span></span>

<span data-ttu-id="db2b1-104">Le guide all'architettura decisionale nel Framework di adozione del Cloud descrivono i modelli e gli schemi che aiutano quando si crea una guida alla progettazione della governance del cloud.</span><span class="sxs-lookup"><span data-stu-id="db2b1-104">The architectural decision guides in the Cloud Adoption Framework describe patterns and models that help when creating cloud governance design guidance.</span></span> <span data-ttu-id="db2b1-105">Ogni guida decisionale è incentrata su un componente fondamentale dell'infrastruttura delle distribuzioni cloud ed elenca i potenziali schemi o modelli progettati per supportare scenari specifici di distribuzione del cloud.</span><span class="sxs-lookup"><span data-stu-id="db2b1-105">Each decision guide focuses on one core infrastructure component of cloud deployments and lists potential patterns or models intended to support specific cloud deployment scenarios.</span></span>

<span data-ttu-id="db2b1-106">Quando si inizia a definire la governance cloud per l'organizzazione, i percorsi di governance attuabili forniscono una roadmap di base.</span><span class="sxs-lookup"><span data-stu-id="db2b1-106">When you begin to establish cloud governance for your organization,  actionable governance journeys provide a baseline roadmap.</span></span> <span data-ttu-id="db2b1-107">Tuttavia, questi percorsi fanno ipotesi sui requisiti e le priorità che potrebbero non riflettere quelle della propria organizzazione.</span><span class="sxs-lookup"><span data-stu-id="db2b1-107">However, these journeys make assumptions about requirements and priorities that may not reflect those of your organization.</span></span>
<span data-ttu-id="db2b1-108">Tali guide decisionali integrano i percorsi di esempio di governance, fornendo schemi e modelli alternativi che aiutano ad allineare con le proprie esigenze le scelte di progettazione architetturale prese nella guida di progettazione di esempio.</span><span class="sxs-lookup"><span data-stu-id="db2b1-108">These decision guides supplement the sample governance journeys by providing alternative patterns and models that help you align the architectural design choices made in the example design guidance with your own requirements.</span></span>

## <a name="design-guidance-categories"></a><span data-ttu-id="db2b1-109">Categorie di indicazioni per la progettazione</span><span class="sxs-lookup"><span data-stu-id="db2b1-109">Design guidance categories</span></span>

<span data-ttu-id="db2b1-110">Ognuna delle categorie seguenti rappresenta una tecnologia di base di tutte le distribuzioni cloud.</span><span class="sxs-lookup"><span data-stu-id="db2b1-110">Each of the following categories represents a foundational technology of all cloud deployments.</span></span> <span data-ttu-id="db2b1-111">Per ogni scelta nei percorsi di progettazione di esempio che non corrisponde ai propri requisiti, esaminare le opzioni nella sezione pertinente di seguito per scegliere uno schema o un modello più adatto alle proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="db2b1-111">For each choice in the example design journeys that doesn’t match your requirements, examine the options in the relevant section below to choose a pattern or model better suited to your needs.</span></span>

<span data-ttu-id="db2b1-112">[Sottoscrizioni](./subscriptions/overview.md): pianificare la struttura della sottoscrizione e la struttura dell'account della distribuzione cloud in modo che corrisponda alle proprietà, alla fatturazione e alle capacità di gestione dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="db2b1-112">[Subscriptions](./subscriptions/overview.md): Plan your cloud deployment's subscription design and account structure to match your organization's ownership, billing, and management capabilities.</span></span>

<span data-ttu-id="db2b1-113">[Identità](./identity/overview.md): integrare servizi di identità basati sul cloud con le risorse di identità esistenti per supportare il controllo di accesso all'interno dell'ambiente IT.</span><span class="sxs-lookup"><span data-stu-id="db2b1-113">[Identity](./identity/overview.md): Integrate cloud-based identity services with your existing identity resources to support access control within your IT environment.</span></span>

<span data-ttu-id="db2b1-114">[Imposizione dei criteri](./policy-enforcement/overview.md): definire e applicare le regole dei criteri dell'organizzazione per le risorse e i carichi di lavoro che si distribuisce nel cloud.</span><span class="sxs-lookup"><span data-stu-id="db2b1-114">[Policy Enforcement](./policy-enforcement/overview.md): Define and enforce organizational policy rules for resources and workloads that you deploy to the cloud.</span></span>

<span data-ttu-id="db2b1-115">[Coerenza delle risorse](./resource-consistency/overview.md): assicurarsi che la distribuzione e l'organizzazione delle risorse basate su cloud siano allineate per far rispettare i requisiti di gestione delle risorse e dei criteri.</span><span class="sxs-lookup"><span data-stu-id="db2b1-115">[Resource Consistency](./resource-consistency/overview.md): Ensure that deployment and organization of your cloud-based resources align to enforce resource management and policy requirements.</span></span>

<span data-ttu-id="db2b1-116">[Tag delle risorse](./resource-tagging/overview.md): organizzare le risorse basate su cloud per supportare i modelli di fatturazione, gli approcci di accounting cloud, la gestione, il controllo di accesso e l'efficienza operativa.</span><span class="sxs-lookup"><span data-stu-id="db2b1-116">[Resource Tagging](./resource-tagging/overview.md): Organize your cloud-based resources to support billing models, cloud accounting approaches, management, access control, and operational efficiency.</span></span> <span data-ttu-id="db2b1-117">Il tag delle risorse richiede uno schema di denominazione e metadati coerente e ben organizzato.</span><span class="sxs-lookup"><span data-stu-id="db2b1-117">Resource tagging requires a consistent and well-organized naming and metadata scheme.</span></span>

<span data-ttu-id="db2b1-118">[Reti definite dal software](./software-defined-network/overview.md): distribuire i carichi di lavoro sicuri nel cloud usando una distribuzione rapida e la modifica della capacità di rete virtualizzata.</span><span class="sxs-lookup"><span data-stu-id="db2b1-118">[Software Defined Networks](./software-defined-network/overview.md): Deploy secure workloads to the cloud using rapid deployment and modification of virtualized networking capabilities.</span></span> <span data-ttu-id="db2b1-119">Software defined networks (SDNs) può supportare flussi di lavoro agili, isolare le risorse e integrare i sistemi basati su cloud con l'infrastruttura IT esistente.</span><span class="sxs-lookup"><span data-stu-id="db2b1-119">Software-defined networks (SDNs) can support agile workflows, isolate resources, and integrate cloud-based systems with your existing IT infrastructure.</span></span>

<span data-ttu-id="db2b1-120">[Crittografia](./encryption/overview.md): proteggere i dati sensibili usando la crittografia, un aspetto importante della protezione all'interno di una distribuzione cloud.</span><span class="sxs-lookup"><span data-stu-id="db2b1-120">[Encryption](./encryption/overview.md): Secure your sensitive data using encryption, an important aspect of security within a cloud deployment.</span></span>

<span data-ttu-id="db2b1-121">[Log e report](./log-and-report/overview.md): monitorare i dati di log generati dalle risorse basate sul cloud.</span><span class="sxs-lookup"><span data-stu-id="db2b1-121">[Logs and Reporting](./log-and-report/overview.md): Monitor log data generated by cloud-based resources.</span></span> <span data-ttu-id="db2b1-122">L'analisi dei dati fornisce informazioni dettagliate correlate all'integrità delle operazioni, la manutenzione e lo stato di imposizione dei criteri dei carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="db2b1-122">Analyzing data provides health-related insights into the operations, maintenance, and policy enforcement status of workloads.</span></span>

## <a name="next-steps"></a><span data-ttu-id="db2b1-123">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="db2b1-123">Next steps</span></span>

<span data-ttu-id="db2b1-124">Informazioni su come le sottoscrizioni e gli account servono da base di una distribuzione cloud.</span><span class="sxs-lookup"><span data-stu-id="db2b1-124">Learn how subscriptions and accounts serve as the base of a cloud deployment.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="db2b1-125">Progettazione delle sottoscrizioni</span><span class="sxs-lookup"><span data-stu-id="db2b1-125">Subscriptions design</span></span>](subscriptions/overview.md)
