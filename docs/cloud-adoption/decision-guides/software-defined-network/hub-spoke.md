---
title: 'CAF: Reti definite dal software - Native per il cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sui servizi di rete virtuale cloud nativi
author: rotycenh
ms.openlocfilehash: e0ad6803f2ddc982ea0c42c59fdf2486e1710433
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241125"
---
# <a name="software-defined-networks-hub-and-spoke"></a><span data-ttu-id="92437-103">Reti definite dal software: hub-spoke</span><span class="sxs-lookup"><span data-stu-id="92437-103">Software Defined Networks: Hub and Spoke</span></span>

<span data-ttu-id="92437-104">Il modello di rete hub-spoke organizza l'infrastruttura di rete cloud basata su Azure in più reti virtuali connesse.</span><span class="sxs-lookup"><span data-stu-id="92437-104">The hub and spoke networking model organizes your Azure-based cloud network infrastructure into multiple connected virtual networks.</span></span> <span data-ttu-id="92437-105">Questo modello consente di gestire in modo più efficiente i comuni requisiti di comunicazione o sicurezza e le potenziali limitazioni delle sottoscrizioni.</span><span class="sxs-lookup"><span data-stu-id="92437-105">This model allows you to more efficiently manage common communication or security requirements and deal with potential subscription limitations.</span></span>

<span data-ttu-id="92437-106">Nel modello hub-spoke l'*hub* è una rete virtuale che funge da posizione centrale per la gestione della connettività esterna e l'hosting dei servizi usati da più carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="92437-106">In the hub and spoke model, the *hub* is a virtual network that acts as a central location for managing external connectivity and hosting services used by multiple workloads.</span></span> <span data-ttu-id="92437-107">Gli *spoke* sono reti virtuali che ospitano i carichi di lavoro e si connettono all'hub centrale tramite il [peering di rete virtuale](/virtual-network/virtual-network-peering-overview).</span><span class="sxs-lookup"><span data-stu-id="92437-107">The *spokes* are virtual networks that host workloads and connect to the central hub through [virtual network peering](/virtual-network/virtual-network-peering-overview).</span></span>

<span data-ttu-id="92437-108">Tutto il traffico in ingresso o in uscita dalle reti spoke dei carichi di lavoro viene instradato tramite la rete hub dove può essere indirizzato, controllato o altrimenti gestito dalle regole o dai processi IT gestiti in modo centralizzato.</span><span class="sxs-lookup"><span data-stu-id="92437-108">All traffic passing in or out of the workload spoke networks is routed through the hub network where it can be routed, inspected, or otherwise managed by centrally managed IT rules or processes.</span></span>

<span data-ttu-id="92437-109">Questo modello contribuisce a risolvere i problemi seguenti:</span><span class="sxs-lookup"><span data-stu-id="92437-109">This model aims to address the following issues:</span></span>

- <span data-ttu-id="92437-110">Risparmio sui costi ed efficienza della gestione.</span><span class="sxs-lookup"><span data-stu-id="92437-110">Cost savings and management efficiency.</span></span> <span data-ttu-id="92437-111">Centralizzando in un'unica posizione i servizi che possono essere condivisi da più carichi di lavoro, ad esempio appliance virtuali di rete e server DNS, l'IT può ridurre al minimo le risorse ridondanti e il lavoro richiesto dalla gestione in più carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="92437-111">Centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location allows IT to minimize redundant resources and management effort across multiple workloads.</span></span>
- <span data-ttu-id="92437-112">Superamento dei limiti delle sottoscrizioni.</span><span class="sxs-lookup"><span data-stu-id="92437-112">Overcoming subscriptions limits.</span></span> <span data-ttu-id="92437-113">Per i carichi di lavoro di grandi dimensioni basati sul cloud, potrebbe essere necessario usare più risorse di quelle consentite in una singola sottoscrizione di Azure. Vedere [Limiti delle sottoscrizioni](/azure/azure-subscription-service-limits).</span><span class="sxs-lookup"><span data-stu-id="92437-113">Large cloud-based workloads may require the use of more resources than are allowed within a single Azure subscription (see [subscription limits](/azure/azure-subscription-service-limits)).</span></span> <span data-ttu-id="92437-114">Il peering delle reti virtuali dei carichi di lavoro da sottoscrizioni diverse a un hub centrale consente di superare tali limiti.</span><span class="sxs-lookup"><span data-stu-id="92437-114">Peering workload virtual networks from different subscriptions to a central hub can overcome these limits.</span></span>
- <span data-ttu-id="92437-115">Separazione dei compiti.</span><span class="sxs-lookup"><span data-stu-id="92437-115">Separation of concerns.</span></span> <span data-ttu-id="92437-116">Possibilità di distribuire singoli carichi di lavoro tra i team IT centrali e i team responsabili dei carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="92437-116">The ability to deploy individual workloads between central IT teams and workloads teams.</span></span>

<span data-ttu-id="92437-117">Il diagramma seguente illustra un'architettura hub-spoke di esempio che include la connettività ibrida gestita in modo centralizzato.</span><span class="sxs-lookup"><span data-stu-id="92437-117">The following diagram shows an example hub and spoke architecture including centrally managed hybrid connectivity.</span></span>

![Architettura di rete hub-spoke](../../../reference-architectures/hybrid-networking/images/hub-spoke.png)

<span data-ttu-id="92437-119">L'architettura hub-spoke viene spesso usata insieme all'architettura di rete ibrida, fornendo una connessione gestita a livello centrale all'ambiente locale condiviso tra più carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="92437-119">The hub and spoke architecture is often used alongside the hybrid networking architecture, providing a centrally managed connection to your on-premises environment shared between multiple workloads.</span></span> <span data-ttu-id="92437-120">In questo scenario tutto il traffico esistente tra i carichi di lavoro e l'ambiente locale passa attraverso l'hub dove può essere gestito e protetto.</span><span class="sxs-lookup"><span data-stu-id="92437-120">In this scenario, all traffic traveling between the workloads and on-premises passes through the hub where it can be managed and secured.</span></span>

## <a name="hub-and-spoke-assumptions"></a><span data-ttu-id="92437-121">Presupposti dell'architettura hub-spoke</span><span class="sxs-lookup"><span data-stu-id="92437-121">Hub and spoke assumptions</span></span>

<span data-ttu-id="92437-122">L'implementazione di un'architettura di rete virtuale hub-spoke presuppone quanto segue:</span><span class="sxs-lookup"><span data-stu-id="92437-122">Implementing a hub and spoke virtual networking architecture assumes the following:</span></span>

- <span data-ttu-id="92437-123">Le distribuzioni cloud coinvolgono carichi di lavoro ospitati in ambienti di lavoro distinti, ad esempio sviluppo, test e produzione, tutti basati su un set di servizi comuni, ad esempio DNS o i servizi directory.</span><span class="sxs-lookup"><span data-stu-id="92437-123">Your cloud deployments will involve workloads hosted in separate working environments, such as development, test, and production, that all rely on a set of common services such as DNS or directory services.</span></span>
- <span data-ttu-id="92437-124">Non è necessario che i carichi di lavoro comunichino tra loro, ma hanno comunicazioni esterne comuni e requisiti di servizi condivisi.</span><span class="sxs-lookup"><span data-stu-id="92437-124">Your workloads do not need to communicate with each other but have common external communications and shared services requirements.</span></span>
- <span data-ttu-id="92437-125">I carichi di lavoro richiedono più risorse di quelle disponibili in una singola sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="92437-125">Your workloads require more resources than are available within a single Azure subscription.</span></span>
- <span data-ttu-id="92437-126">È necessario fornire ai team responsabili dei carichi di lavoro i diritti di gestione delegata sulle risorse mantenendo al contempo il controllo della sicurezza centrale sulla connettività esterna.</span><span class="sxs-lookup"><span data-stu-id="92437-126">You need to provide workload teams with delegated management rights over their own resources while maintaining central security control over external connectivity.</span></span>

## <a name="global-hub-and-spoke"></a><span data-ttu-id="92437-127">Hub-spoke globale</span><span class="sxs-lookup"><span data-stu-id="92437-127">Global hub and spoke</span></span>

<span data-ttu-id="92437-128">Le architetture hub-spoke vengono comunemente implementate con le reti virtuali distribuite nella stessa area di Azure per ridurre al minimo la latenza tra le reti.</span><span class="sxs-lookup"><span data-stu-id="92437-128">Hub and spoke architectures are commonly implemented with virtual networks deployed to the same Azure Region to minimize latency between networks.</span></span> <span data-ttu-id="92437-129">Per le organizzazioni di grandi dimensioni con copertura globale potrebbe tuttavia essere necessario distribuire i carichi di lavoro tra più aree per soddisfare i requisiti di disponibilità, ripristino di emergenza o normativi.</span><span class="sxs-lookup"><span data-stu-id="92437-129">However, large organizations with global reach may need to deploy workloads across multiple regions for availability, disaster recovery, or regulatory requirements.</span></span> <span data-ttu-id="92437-130">Usando il [peering di rete virtuale globale](/azure/virtual-network/virtual-network-peering-overview), il modello hub-spoke può estendere la gestione centralizzata e i servizi condivisi tra le aree per supportare la distribuzione dei carichi di lavoro in tutto il mondo.</span><span class="sxs-lookup"><span data-stu-id="92437-130">Through the use of Azure [global virtual network peering](/azure/virtual-network/virtual-network-peering-overview), the hub and spoke model can extend centralized management and shared services across regions to support workloads distributed across the world.</span></span>

## <a name="learn-more"></a><span data-ttu-id="92437-131">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="92437-131">Learn more</span></span>

<span data-ttu-id="92437-132">Per esempi di implementazione delle reti hub-spoke in Azure, vedere gli esempi seguenti nel sito Architetture di riferimento di Azure:</span><span class="sxs-lookup"><span data-stu-id="92437-132">For examples of how to implement hub and spoke networks on Azure, see the following examples on the Azure Reference Architectures site:</span></span>

- [<span data-ttu-id="92437-133">Implementare una topologia di rete hub-spoke in Azure</span><span class="sxs-lookup"><span data-stu-id="92437-133">Implement a hub-spoke network topology in Azure</span></span>](../../../reference-architectures/hybrid-networking/hub-spoke.md)
- [<span data-ttu-id="92437-134">Implementare una topologia di rete hub-spoke con servizi condivisi in Azure</span><span class="sxs-lookup"><span data-stu-id="92437-134">Implement a hub-spoke network topology with shared services in Azure</span></span>](../../../reference-architectures/hybrid-networking/shared-services.md)