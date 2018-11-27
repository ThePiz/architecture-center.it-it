---
title: Scegliere una soluzione per la connessione di una rete locale ad Azure.
description: Confrontare le architetture di riferimento per connettersi a una rete locale in Azure.
author: telmosampaio
ms.date: 07/02/2018
ms.openlocfilehash: a9e2a212d65530e714635bbfae3a57766e77c3a6
ms.sourcegitcommit: 19a517a2fb70768b3edb9a7c3c37197baa61d9b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52295488"
---
# <a name="connect-an-on-premises-network-to-azure"></a><span data-ttu-id="ac059-103">Connettere una rete locale ad Azure</span><span class="sxs-lookup"><span data-stu-id="ac059-103">Connect an on-premises network to Azure</span></span>

<span data-ttu-id="ac059-104">In questo articolo vengono confrontate le opzioni per la connessione di una rete locale a una rete virtuale di Azure (VNet).</span><span class="sxs-lookup"><span data-stu-id="ac059-104">This article compares options for connecting an on-premises network to an Azure Virtual Network (VNet).</span></span> <span data-ttu-id="ac059-105">Per ogni opzione è disponibile un'architettura di riferimento più dettagliata.</span><span class="sxs-lookup"><span data-stu-id="ac059-105">For each option, a more detailed reference architecture is available.</span></span>

## <a name="vpn-connection"></a><span data-ttu-id="ac059-106">Connessione VPN</span><span class="sxs-lookup"><span data-stu-id="ac059-106">VPN connection</span></span>

<span data-ttu-id="ac059-107">Un [gateway VPN](/azure/vpn-gateway/vpn-gateway-about-vpngateways) è un tipo di gateway di rete virtuale che invia traffico crittografato tra una rete virtuale di Azure e una posizione locale.</span><span class="sxs-lookup"><span data-stu-id="ac059-107">A [VPN gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways) is a type of virtual network gateway that sends encrypted traffic between an Azure virtual network and an on-premises location.</span></span> <span data-ttu-id="ac059-108">Il traffico crittografato passa sulla rete Internet pubblica.</span><span class="sxs-lookup"><span data-stu-id="ac059-108">The encrypted traffic goes over the public Internet.</span></span>

<span data-ttu-id="ac059-109">Questa architettura è adatta ad applicazioni ibride in cui è probabile che il traffico tra l'hardware locale e il cloud sia leggero o se si vuole scambiare la latenza leggermente estesa con la flessibilità e la potenza di elaborazione del cloud.</span><span class="sxs-lookup"><span data-stu-id="ac059-109">This architecture is suitable for hybrid applications where the traffic between on-premises hardware and the cloud is likely to be light, or you are willing to trade slightly extended latency for the flexibility and processing power of the cloud.</span></span>

<span data-ttu-id="ac059-110">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="ac059-110">**Benefits**</span></span>

- <span data-ttu-id="ac059-111">Semplice da configurare.</span><span class="sxs-lookup"><span data-stu-id="ac059-111">Simple to configure.</span></span>

<span data-ttu-id="ac059-112">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="ac059-112">**Challenges**</span></span>

- <span data-ttu-id="ac059-113">Richiede un dispositivo VPN locale.</span><span class="sxs-lookup"><span data-stu-id="ac059-113">Requires an on-premises VPN device.</span></span>
- <span data-ttu-id="ac059-114">Nonostante Microsoft garantisca il 99,9% di disponibilità per ogni gateway VPN, il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) copre esclusivamente il gateway VPN e non la connessione di rete per il gateway.</span><span class="sxs-lookup"><span data-stu-id="ac059-114">Although Microsoft guarantees 99.9% availability for each VPN Gateway, this [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) only covers the VPN gateway, and not your network connection to the gateway.</span></span>
- <span data-ttu-id="ac059-115">Una connessione VPN tramite Gateway VPN di Azure supporta attualmente una larghezza di banda massima di 1,25 Gbps.</span><span class="sxs-lookup"><span data-stu-id="ac059-115">A VPN connection over Azure VPN Gateway currently supports a maximum of 1.25 Gbps bandwidth.</span></span> <span data-ttu-id="ac059-116">Potrebbe essere necessario partizionare la rete virtuale di Azure tra più connessioni VPN se si prevede di superare la velocità effettiva.</span><span class="sxs-lookup"><span data-stu-id="ac059-116">You may need to partition your Azure virtual network across multiple VPN connections if you expect to exceed this throughput.</span></span>

<span data-ttu-id="ac059-117">**Architettura di riferimento**</span><span class="sxs-lookup"><span data-stu-id="ac059-117">**Reference architecture**</span></span>

- [<span data-ttu-id="ac059-118">Rete ibrida con gateway VPN</span><span class="sxs-lookup"><span data-stu-id="ac059-118">Hybrid network with VPN gateway</span></span>](./vpn.md)

## <a name="azure-expressroute-connection"></a><span data-ttu-id="ac059-119">Connessione ExpressRoute di Azure</span><span class="sxs-lookup"><span data-stu-id="ac059-119">Azure ExpressRoute connection</span></span>

<span data-ttu-id="ac059-120">Le [connessioni ExpressRoute](/azure/expressroute/) usano una connessione dedicata privata tramite un provider di connettività di terze parti.</span><span class="sxs-lookup"><span data-stu-id="ac059-120">[ExpressRoute](/azure/expressroute/) connections use a private, dedicated connection through a third-party connectivity provider.</span></span> <span data-ttu-id="ac059-121">La connessione privata estende la rete locale in Azure.</span><span class="sxs-lookup"><span data-stu-id="ac059-121">The private connection extends your on-premises network into Azure.</span></span> 

<span data-ttu-id="ac059-122">Questa architettura è adatta ad applicazioni ibride che eseguono carichi di lavoro critici su larga scala e che richiedono un livello di scalabilità elevato.</span><span class="sxs-lookup"><span data-stu-id="ac059-122">This architecture is suitable for hybrid applications running large-scale, mission-critical workloads that require a high degree of scalability.</span></span> 

<span data-ttu-id="ac059-123">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="ac059-123">**Benefits**</span></span>

- <span data-ttu-id="ac059-124">Disponibilità di una maggiore larghezza di banda, che può arrivare fino a 10 Gbps a seconda del provider di connettività.</span><span class="sxs-lookup"><span data-stu-id="ac059-124">Much higher bandwidth available; up to 10 Gbps depending on the connectivity provider.</span></span>
- <span data-ttu-id="ac059-125">Supporta la scalabilità dinamica della larghezza di banda per ridurre i costi durante i periodi di minore richiesta.</span><span class="sxs-lookup"><span data-stu-id="ac059-125">Supports dynamic scaling of bandwidth to help reduce costs during periods of lower demand.</span></span> <span data-ttu-id="ac059-126">Tuttavia, non tutti i provider di connettività dispongono di questa opzione.</span><span class="sxs-lookup"><span data-stu-id="ac059-126">However, not all connectivity providers have this option.</span></span>
- <span data-ttu-id="ac059-127">Può consentire all'organizzazione l'accesso diretto ai cloud nazionali, a seconda del provider di connettività.</span><span class="sxs-lookup"><span data-stu-id="ac059-127">May allow your organization direct access to national clouds, depending on the connectivity provider.</span></span>
- <span data-ttu-id="ac059-128">Offre il 99,9% di disponibilità del contratto di servizio attraverso l'intera stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="ac059-128">99.9% availability SLA across the entire connection.</span></span>

<span data-ttu-id="ac059-129">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="ac059-129">**Challenges**</span></span>

- <span data-ttu-id="ac059-130">La configurazione può risultare complessa.</span><span class="sxs-lookup"><span data-stu-id="ac059-130">Can be complex to set up.</span></span> <span data-ttu-id="ac059-131">La creazione di una connessione ExpressRoute richiede l'uso di un provider di connettività di terze parti.</span><span class="sxs-lookup"><span data-stu-id="ac059-131">Creating an ExpressRoute connection requires working with a third-party connectivity provider.</span></span> <span data-ttu-id="ac059-132">Il provider è responsabile del provisioning della connessione di rete.</span><span class="sxs-lookup"><span data-stu-id="ac059-132">The provider is responsible for provisioning the network connection.</span></span>
- <span data-ttu-id="ac059-133">Richiede un router locale a larghezza di banda elevata.</span><span class="sxs-lookup"><span data-stu-id="ac059-133">Requires high-bandwidth routers on-premises.</span></span>

<span data-ttu-id="ac059-134">**Architettura di riferimento**</span><span class="sxs-lookup"><span data-stu-id="ac059-134">**Reference architecture**</span></span>

- [<span data-ttu-id="ac059-135">Rete ibrida con ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="ac059-135">Hybrid network with ExpressRoute</span></span>](./expressroute.md)

## <a name="expressroute-with-vpn-failover"></a><span data-ttu-id="ac059-136">ExpressRoute con failover VPN</span><span class="sxs-lookup"><span data-stu-id="ac059-136">ExpressRoute with VPN failover</span></span>

<span data-ttu-id="ac059-137">Questa opzione risulta essere una combinazione delle due precedenti, vale a dire l'uso di ExpressRoute in condizioni normali, ma l'esecuzione del failover in una connessione VPN se vi è una perdita di connettività del circuito ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="ac059-137">This options combines the previous two, using ExpressRoute in normal conditions, but failing over to a VPN connection if there is a loss of connectivity in the ExpressRoute circuit.</span></span>

<span data-ttu-id="ac059-138">Questa architettura è adatta ad applicazioni ibride che necessitano di una larghezza di banda di ExpressRoute maggiore, oltre a una connettività di rete a disponibilità elevata.</span><span class="sxs-lookup"><span data-stu-id="ac059-138">This architecture is suitable for hybrid applications that need the higher bandwidth of ExpressRoute, and also require highly available network connectivity.</span></span> 

<span data-ttu-id="ac059-139">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="ac059-139">**Benefits**</span></span>

- <span data-ttu-id="ac059-140">Disponibilità elevata se il circuito ExpressRoute ha esito negativo, anche se la connessione fallback si trova in una rete a larghezza di banda inferiore.</span><span class="sxs-lookup"><span data-stu-id="ac059-140">High availability if the ExpressRoute circuit fails, although the fallback connection is on a lower bandwidth network.</span></span>

<span data-ttu-id="ac059-141">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="ac059-141">**Challenges**</span></span>

- <span data-ttu-id="ac059-142">Complesso da configurare.</span><span class="sxs-lookup"><span data-stu-id="ac059-142">Complex to configure.</span></span> <span data-ttu-id="ac059-143">È necessario configurare sia una connessione VPN sia un circuito ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="ac059-143">You need to set up both a VPN connection and an ExpressRoute circuit.</span></span>
- <span data-ttu-id="ac059-144">Richiede sia un hardware ridondante (dispositivi VPN) che una connessione Gateway VPN di Azure ridondante, entrambi soggetti a tariffa.</span><span class="sxs-lookup"><span data-stu-id="ac059-144">Requires redundant hardware (VPN appliances), and a redundant Azure VPN Gateway connection for which you pay charges.</span></span>

<span data-ttu-id="ac059-145">**Architettura di riferimento**</span><span class="sxs-lookup"><span data-stu-id="ac059-145">**Reference architecture**</span></span>

- [<span data-ttu-id="ac059-146">Rete ibrida con ExpressRoute e failover VPN</span><span class="sxs-lookup"><span data-stu-id="ac059-146">Hybrid network with ExpressRoute and VPN failover</span></span>](./expressroute-vpn-failover.md)


## <a name="hub-spoke-network-topology"></a><span data-ttu-id="ac059-147">Topologia di rete hub-spoke</span><span class="sxs-lookup"><span data-stu-id="ac059-147">Hub-spoke network topology</span></span>

<span data-ttu-id="ac059-148">Una topologia di rete hub-spoke è un modo per isolare i carichi di lavoro durante la condivisione di servizi quali identità e sicurezza.</span><span class="sxs-lookup"><span data-stu-id="ac059-148">A hub-spoke network topology is a way to isolate workloads while sharing services such as identity and security.</span></span> <span data-ttu-id="ac059-149">L'hub è una rete virtuale in Azure che funge da punto centrale di connettività alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="ac059-149">The hub is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="ac059-150">Gli spoke sono reti virtuali che eseguono il peering con l'hub.</span><span class="sxs-lookup"><span data-stu-id="ac059-150">The spokes are VNets that peer with the hub.</span></span> <span data-ttu-id="ac059-151">I servizi condivisi vengono distribuiti nell'hub, mentre i singoli carichi di lavoro vengono distribuiti come spoke.</span><span class="sxs-lookup"><span data-stu-id="ac059-151">Shared services are deployed in the hub, while individual workloads are deployed as spokes.</span></span>


<span data-ttu-id="ac059-152">**Architetture di riferimento**</span><span class="sxs-lookup"><span data-stu-id="ac059-152">**Reference architectures**</span></span>

- [<span data-ttu-id="ac059-153">Topologia hub-spoke</span><span class="sxs-lookup"><span data-stu-id="ac059-153">Hub-spoke topology</span></span>](./hub-spoke.md)
- [<span data-ttu-id="ac059-154">Hub-spoke con servizi condivisi</span><span class="sxs-lookup"><span data-stu-id="ac059-154">Hub-spoke with shared services</span></span>](./shared-services.md)
