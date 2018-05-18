---
title: Implementazione una topologia di rete hub-spoke in Azure
description: Come implementare una topologia di rete hub-spoke in Azure.
author: telmosampaio
ms.date: 04/09/2018
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: f04af90f328a0434d44ca7ea90309f3209a3b69d
ms.sourcegitcommit: f665226cec96ec818ca06ac6c2d83edb23c9f29c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a><span data-ttu-id="b2f81-103">Implementare una topologia di rete hub-spoke in Azure</span><span class="sxs-lookup"><span data-stu-id="b2f81-103">Implement a hub-spoke network topology in Azure</span></span>

<span data-ttu-id="b2f81-104">Questa architettura di riferimento illustra come implementare una topologia hub-spoke in Azure.</span><span class="sxs-lookup"><span data-stu-id="b2f81-104">This reference architecture shows how to implement a hub-spoke topology in Azure.</span></span> <span data-ttu-id="b2f81-105">L'*hub* è una rete virtuale in Azure che funge da punto di connettività centrale alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-105">The *hub* is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="b2f81-106">Gli *spoke* sono le reti virtuali che eseguono il peering con l'hub e possono essere usate per isolare i carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="b2f81-106">The *spokes* are VNets that peer with the hub, and can be used to isolate workloads.</span></span> <span data-ttu-id="b2f81-107">Flussi di traffico tra il data center locale e l'hub attraverso una connessione di gateway VPN o ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="b2f81-107">Traffic flows between the on-premises datacenter and the hub through an ExpressRoute or VPN gateway connection.</span></span>  <span data-ttu-id="b2f81-108">[**Distribuire questa soluzione**](#deploy-the-solution).</span><span class="sxs-lookup"><span data-stu-id="b2f81-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="b2f81-109">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="b2f81-109">![[0]][0]</span></span>

<span data-ttu-id="b2f81-110">*Scaricare un [file di Visio][visio-download] di questa architettura*</span><span class="sxs-lookup"><span data-stu-id="b2f81-110">*Download a [Visio file][visio-download] of this architecture*</span></span>


<span data-ttu-id="b2f81-111">I vantaggi di questa topologia includono:</span><span class="sxs-lookup"><span data-stu-id="b2f81-111">The benefits of this toplogy include:</span></span>

* <span data-ttu-id="b2f81-112">**Risparmio sui costi**, centralizzando in un'unica posizione i servizi che possono essere condivisi da più carichi di lavoro, ad esempio appliance virtuali di rete e server DNS.</span><span class="sxs-lookup"><span data-stu-id="b2f81-112">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
* <span data-ttu-id="b2f81-113">**Superamento dei limiti delle sottoscrizioni** eseguendo il peering delle reti virtuali da sottoscrizioni diverse all'hub centrale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-113">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
* <span data-ttu-id="b2f81-114">**Separazione dei compiti** tra IT centrale (SecOPs, InfraOps) e carichi di lavoro (DevOps).</span><span class="sxs-lookup"><span data-stu-id="b2f81-114">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="b2f81-115">Tra gli usi tipici di questa architettura vi sono:</span><span class="sxs-lookup"><span data-stu-id="b2f81-115">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="b2f81-116">Carichi di lavoro distribuiti in ambienti diversi, ad esempio sviluppo, test e produzione, che richiedono servizi condivisi, ad esempio DNS, IDS, NTP o Active Directory Domain Services.</span><span class="sxs-lookup"><span data-stu-id="b2f81-116">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="b2f81-117">I servizi condivisi vengono inseriti nella rete virtuale dell'hub, mentre ogni ambiente viene distribuito in uno spoke per mantenere l'isolamento.</span><span class="sxs-lookup"><span data-stu-id="b2f81-117">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
* <span data-ttu-id="b2f81-118">Carichi di lavoro che non richiedono connettività uno con l'altro, ma richiedono l'accesso ai servizi condivisi.</span><span class="sxs-lookup"><span data-stu-id="b2f81-118">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
* <span data-ttu-id="b2f81-119">Aziende che richiedono il controllo centrale sugli aspetti di sicurezza, ad esempio un firewall nell'hub come rete perimetrale, e gestione separata per i carichi di lavoro in ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-119">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="b2f81-120">Architecture</span><span class="sxs-lookup"><span data-stu-id="b2f81-120">Architecture</span></span>

<span data-ttu-id="b2f81-121">L'architettura è costituita dai componenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="b2f81-121">The architecture consists of the following components.</span></span>

* <span data-ttu-id="b2f81-122">**Rete locale**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-122">**On-premises network**.</span></span> <span data-ttu-id="b2f81-123">Una rete LAN privata in esecuzione all'interno di un'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="b2f81-123">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="b2f81-124">**Dispositivo VPN**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-124">**VPN device**.</span></span> <span data-ttu-id="b2f81-125">Un dispositivo o un servizio che offre connettività esterna alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-125">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="b2f81-126">Il dispositivo VPN può essere un dispositivo hardware o una soluzione software, ad esempio il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012.</span><span class="sxs-lookup"><span data-stu-id="b2f81-126">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="b2f81-127">Per un elenco delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN da sito a sito][vpn-appliance].</span><span class="sxs-lookup"><span data-stu-id="b2f81-127">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="b2f81-128">**Gateway di rete virtuale VPN o gateway ExpressRoute**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-128">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="b2f81-129">Il gateway di rete virtuale consente di connettere la rete virtuale al dispositivo VPN o al circuito ExpressRoute usato per la connettività con la propria rete locale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-129">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="b2f81-130">Per altre informazioni, vedere [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].</span><span class="sxs-lookup"><span data-stu-id="b2f81-130">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="b2f81-131">Gli script di distribuzione per questa architettura di riferimento usano un gateway VPN per la connettività e una rete virtuale in Azure per simulare la rete locale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-131">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

* <span data-ttu-id="b2f81-132">**Rete virtuale dell'hub**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-132">**Hub VNet**.</span></span> <span data-ttu-id="b2f81-133">Rete virtuale di Azure usata come hub nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-133">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="b2f81-134">L'hub è il punto di connettività centrale alla rete locale e offre una posizione in cui ospitare servizi che possono essere utilizzati da carichi di lavoro diversi ospitati nelle reti virtuali spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-134">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

* <span data-ttu-id="b2f81-135">**Subnet del gateway**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-135">**Gateway subnet**.</span></span> <span data-ttu-id="b2f81-136">i gateway di rete virtuale vengono mantenuti nella stessa subnet.</span><span class="sxs-lookup"><span data-stu-id="b2f81-136">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="b2f81-137">**Reti virtuali spoke**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-137">**Spoke VNets**.</span></span> <span data-ttu-id="b2f81-138">Una o più reti virtuali di Azure usate come spoke nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-138">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="b2f81-139">Gli spoke possono essere usati per isolare i carichi di lavoro nelle reti virtuali corrispondenti, gestite separatamente rispetto agli altri spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-139">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="b2f81-140">Ogni carico di lavoro può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure.</span><span class="sxs-lookup"><span data-stu-id="b2f81-140">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="b2f81-141">Per ulteriori informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] e [Esecuzione di carichi di lavoro della macchina virtuale Linux][linux-vm-ra].</span><span class="sxs-lookup"><span data-stu-id="b2f81-141">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="b2f81-142">**Peering reti virtuali**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-142">**VNet peering**.</span></span> <span data-ttu-id="b2f81-143">È possibile connettere due reti virtuali nella stessa area di Azure tramite una [connessione peering][vnet-peering].</span><span class="sxs-lookup"><span data-stu-id="b2f81-143">Two VNets in the same Azure region can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="b2f81-144">Le connessioni peering sono connessioni non transitive a bassa latenza tra reti virtuali.</span><span class="sxs-lookup"><span data-stu-id="b2f81-144">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="b2f81-145">Dopo il peering, le reti virtuali si scambiano traffico tramite il backbone di Azure, senza bisogno di un router.</span><span class="sxs-lookup"><span data-stu-id="b2f81-145">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="b2f81-146">In una topologia hub-spoke di rete si usa il peering reti virtuali per connettere l'hub a ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-146">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span>

> [!NOTE]
> <span data-ttu-id="b2f81-147">Questo articolo illustra solo le distribuzioni [Resource Manager](/azure/azure-resource-manager/resource-group-overview), ma è anche possibile connettere una rete virtuale classica a una rete virtuale di Resource Manager nella stessa sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="b2f81-147">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="b2f81-148">In questo modo, gli spoke possono ospitare distribuzioni classiche e trarre comunque vantaggio dai servizi condivisi nell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2f81-148">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b2f81-149">Raccomandazioni</span><span class="sxs-lookup"><span data-stu-id="b2f81-149">Recommendations</span></span>

<span data-ttu-id="b2f81-150">Le raccomandazioni seguenti sono valide per la maggior parte degli scenari.</span><span class="sxs-lookup"><span data-stu-id="b2f81-150">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="b2f81-151">Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.</span><span class="sxs-lookup"><span data-stu-id="b2f81-151">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="b2f81-152">Gruppi di risorse</span><span class="sxs-lookup"><span data-stu-id="b2f81-152">Resource groups</span></span>

<span data-ttu-id="b2f81-153">È possibile implementare la rete virtuale dell'hub e ogni rete virtuale spoke in gruppi di risorse diversi e persino in sottoscrizioni diverse, a condizione che appartengano allo stesso tenant di Azure Active Directory (Azure AD) nella stessa area di Azure.</span><span class="sxs-lookup"><span data-stu-id="b2f81-153">The hub VNet, and each spoke VNet, can be implemented in different resource groups, and even different subscriptions, as long as they belong to the same Azure Active Directory (Azure AD) tenant in the same Azure region.</span></span> <span data-ttu-id="b2f81-154">Questo consente di decentralizzare la gestione di ogni carico di lavoro e allo stesso tempo condividere i servizi gestiti nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2f81-154">This allows for a decentralized management of each workload, while sharing services maintained in the hub VNet.</span></span>

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="b2f81-155">Reti virtuali e GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="b2f81-155">VNet and GatewaySubnet</span></span>

<span data-ttu-id="b2f81-156">Creare una subnet denominata *GatewaySubnet*, con un intervallo di indirizzi di /27.</span><span class="sxs-lookup"><span data-stu-id="b2f81-156">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="b2f81-157">Questa subnet è necessaria per il gateway di rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-157">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="b2f81-158">Allocare 32 indirizzi a questa subnet contribuirà in futuro a evitare di raggiungere i limiti di dimensioni del gateway.</span><span class="sxs-lookup"><span data-stu-id="b2f81-158">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span>

<span data-ttu-id="b2f81-159">Per altre informazioni sulla configurazione del gateway, vedere le architetture di riferimento seguenti, a seconda del tipo di connessione:</span><span class="sxs-lookup"><span data-stu-id="b2f81-159">For more information about setting up the gateway, see the following reference architectures, depending on your connection type:</span></span>

- <span data-ttu-id="b2f81-160">[Rete ibrida tramite ExpressRoute][guidance-expressroute]</span><span class="sxs-lookup"><span data-stu-id="b2f81-160">[Hybrid network using ExpressRoute][guidance-expressroute]</span></span>
- <span data-ttu-id="b2f81-161">[Rete ibrida tramite un gateway VPN][guidance-vpn]</span><span class="sxs-lookup"><span data-stu-id="b2f81-161">[Hybrid network using a VPN gateway][guidance-vpn]</span></span>

<span data-ttu-id="b2f81-162">Per una disponibilità più elevata, è possibile usare ExpressRoute più una rete VPN per il failover.</span><span class="sxs-lookup"><span data-stu-id="b2f81-162">For higher availability, you can use ExpressRoute plus a VPN for failover.</span></span> <span data-ttu-id="b2f81-163">Vedere [Connettere una rete locale ad Azure tramite ExpressRoute con failover VPN][hybrid-ha].</span><span class="sxs-lookup"><span data-stu-id="b2f81-163">See [Connect an on-premises network to Azure using ExpressRoute with VPN failover][hybrid-ha].</span></span>

<span data-ttu-id="b2f81-164">Una topologia hub-spoke può essere usata anche senza gateway, se non è necessaria la connettività con la rete locale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-164">A hub-spoke topology can also be used without a gateway, if you don't need connectivity with your on-premises network.</span></span> 

### <a name="vnet-peering"></a><span data-ttu-id="b2f81-165">Peering reti virtuali</span><span class="sxs-lookup"><span data-stu-id="b2f81-165">VNet peering</span></span>

<span data-ttu-id="b2f81-166">Il peering reti virtuali è una relazione non transitiva tra due reti virtuali.</span><span class="sxs-lookup"><span data-stu-id="b2f81-166">VNet peering is a non-transitive relationship between two VNets.</span></span> <span data-ttu-id="b2f81-167">Se è necessaria la connettività tra gli spoke, considerare l'aggiunta di una connessione peering separata tra di essi.</span><span class="sxs-lookup"><span data-stu-id="b2f81-167">If you require spokes to connect to each other, consider adding a separate peering connection between those spokes.</span></span>

<span data-ttu-id="b2f81-168">Tuttavia, se si dispone di più spoke che devono connettersi tra loro, le possibili connessioni peering si esauriranno molto rapidamente, a causa del [limite di peering reti virtuali per ogni rete virtuale][vnet-peering-limit].</span><span class="sxs-lookup"><span data-stu-id="b2f81-168">However, if you have several spokes that need to connect with each other, you will run out of possible peering connections very quickly due to the [limitation on number of VNets peerings per VNet][vnet-peering-limit].</span></span> <span data-ttu-id="b2f81-169">In questo scenario, prendere in considerazione l'uso di route definite dall'utente per forzare l'invio del traffico destinato a uno spoke a un'appliance virtuale di rete che funga da router nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2f81-169">In this scenario, consider using user defined routes (UDRs) to force traffic destined to a spoke to be sent to an NVA acting as a router at the hub VNet.</span></span> <span data-ttu-id="b2f81-170">Questo consentirà agli spoke di connettersi tra loro.</span><span class="sxs-lookup"><span data-stu-id="b2f81-170">This will allow the spokes to connect to each other.</span></span>

<span data-ttu-id="b2f81-171">È anche possibile configurare gli spoke in modo da usare il gateway della rete virtuale dell'hub per comunicare con reti remote.</span><span class="sxs-lookup"><span data-stu-id="b2f81-171">You can also configure spokes to use the hub VNet gateway to communicate with remote networks.</span></span> <span data-ttu-id="b2f81-172">Per consentire il flusso del traffico gateway dallo spoke all'hub e connettersi a reti remote, è necessario:</span><span class="sxs-lookup"><span data-stu-id="b2f81-172">To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:</span></span>

  - <span data-ttu-id="b2f81-173">Configurare la connessione peering reti virtuali nell'hub in modo da **consentire il transito gateway**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-173">Configure the VNet peering connection in the hub to **allow gateway transit**.</span></span>
  - <span data-ttu-id="b2f81-174">Configurare la connessione peering reti virtuali in ogni spoke in modo da **usare gateway remoti**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-174">Configure the VNet peering connection in each spoke to **use remote gateways**.</span></span>
  - <span data-ttu-id="b2f81-175">Configurare tutte le connessioni peering reti virtuali in modo da **consentire il traffico inoltrato**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-175">Configure all VNet peering connections to **allow forwarded traffic**.</span></span>

## <a name="considerations"></a><span data-ttu-id="b2f81-176">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="b2f81-176">Considerations</span></span>

### <a name="spoke-connectivity"></a><span data-ttu-id="b2f81-177">Connettività spoke</span><span class="sxs-lookup"><span data-stu-id="b2f81-177">Spoke connectivity</span></span>

<span data-ttu-id="b2f81-178">Se è necessaria la connettività tra spoke, è consigliabile implementare un'appliance virtuale di rete per il routing nell'hub e usare route definite dall'utente nello spoke per inoltrare il traffico all'hub.</span><span class="sxs-lookup"><span data-stu-id="b2f81-178">If you require connectivity between spokes, consider implementing an NVA for routing in the hub, and using UDRs in the spoke to forward traffic to the hub.</span></span>

<span data-ttu-id="b2f81-179">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="b2f81-179">![[2]][2]</span></span>

<span data-ttu-id="b2f81-180">In questo scenario occorre configurare le connessioni peering in modo da **consentire il traffico inoltrato**.</span><span class="sxs-lookup"><span data-stu-id="b2f81-180">In this scenario, you must configure the peering connections to **allow forwarded traffic**.</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="b2f81-181">Superamento dei limiti del peering reti virtuali</span><span class="sxs-lookup"><span data-stu-id="b2f81-181">Overcoming VNet peering limits</span></span>

<span data-ttu-id="b2f81-182">Assicurarsi di considerare il [limite sul numero di peering reti virtuali per ogni rete virtuale][vnet-peering-limit] in Azure.</span><span class="sxs-lookup"><span data-stu-id="b2f81-182">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="b2f81-183">Se si stabilisce che saranno necessari più spoke di quanto consentito dal limite, valutare la creazione di una topologia hub-spoke-hub-spoke, in cui il primo livello di spoke funge anche da hub.</span><span class="sxs-lookup"><span data-stu-id="b2f81-183">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="b2f81-184">Il diagramma seguente illustra questo approccio.</span><span class="sxs-lookup"><span data-stu-id="b2f81-184">The following diagram shows this approach.</span></span>

<span data-ttu-id="b2f81-185">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="b2f81-185">![[3]][3]</span></span>

<span data-ttu-id="b2f81-186">Considerare inoltre i servizi condivisi nell'hub, per assicurarsi che quest'ultimo sia scalabile per un numero superiore di spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-186">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="b2f81-187">Ad esempio, se l'hub fornisce servizi firewall, valutare i limiti di larghezza di banda della soluzione firewall quando si aggiungono più spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-187">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="b2f81-188">Può essere utile spostare alcuni dei servizi condivisi in un secondo livello di hub.</span><span class="sxs-lookup"><span data-stu-id="b2f81-188">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="b2f81-189">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="b2f81-189">Deploy the solution</span></span>

<span data-ttu-id="b2f81-190">Una distribuzione di questa architettura è disponibile in [GitHub][ref-arch-repo].</span><span class="sxs-lookup"><span data-stu-id="b2f81-190">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="b2f81-191">Usa macchine virtuali in ogni rete virtuale per testare la connettività.</span><span class="sxs-lookup"><span data-stu-id="b2f81-191">It uses VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="b2f81-192">Nella subnet **shared-services** della **rete virtuale dell'hub** non sono effettivamente ospitati servizi.</span><span class="sxs-lookup"><span data-stu-id="b2f81-192">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

<span data-ttu-id="b2f81-193">La distribuzione crea i seguenti gruppi di risorse nella sottoscrizione:</span><span class="sxs-lookup"><span data-stu-id="b2f81-193">The deployment creates the following resource groups in your subscription:</span></span>

- <span data-ttu-id="b2f81-194">hub-nva-rg</span><span class="sxs-lookup"><span data-stu-id="b2f81-194">hub-nva-rg</span></span>
- <span data-ttu-id="b2f81-195">hub-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="b2f81-195">hub-vnet-rg</span></span>
- <span data-ttu-id="b2f81-196">onprem-jb-rg</span><span class="sxs-lookup"><span data-stu-id="b2f81-196">onprem-jb-rg</span></span>
- <span data-ttu-id="b2f81-197">onprem-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="b2f81-197">onprem-vnet-rg</span></span>
- <span data-ttu-id="b2f81-198">spoke1-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="b2f81-198">spoke1-vnet-rg</span></span>
- <span data-ttu-id="b2f81-199">spoke2-vent-rg</span><span class="sxs-lookup"><span data-stu-id="b2f81-199">spoke2-vent-rg</span></span>

<span data-ttu-id="b2f81-200">I file parametro di modello fanno riferimento a questi nomi; pertanto, se questi vengono modificati, aggiornare i file parametro in modo che corrispondano.</span><span class="sxs-lookup"><span data-stu-id="b2f81-200">The template parameter files refer to these names, so if you change them, update the parameter files to match.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="b2f81-201">prerequisiti</span><span class="sxs-lookup"><span data-stu-id="b2f81-201">Prerequisites</span></span>

1. <span data-ttu-id="b2f81-202">Clonare, creare una copia tramite fork o scaricare il file ZIP per il repository GitHub delle [architetture di riferimento][ref-arch-repo].</span><span class="sxs-lookup"><span data-stu-id="b2f81-202">Clone, fork, or download the zip file for the [reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="b2f81-203">Installare l'[Interfaccia della riga di comando di Azure 2.0][azure-cli-2].</span><span class="sxs-lookup"><span data-stu-id="b2f81-203">Install [Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="b2f81-204">Installare il pacchetto npm dei [blocchi predefiniti di Azure][azbb].</span><span class="sxs-lookup"><span data-stu-id="b2f81-204">Install the [Azure building blocks][azbb] npm package.</span></span>

4. <span data-ttu-id="b2f81-205">Al prompt dei comandi, di Bash o di PowerShell accedere all'account Azure con il comando seguente.</span><span class="sxs-lookup"><span data-stu-id="b2f81-205">From a command prompt, bash prompt, or PowerShell prompt, log into your Azure account by using the command below.</span></span>

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a><span data-ttu-id="b2f81-206">Distribuire il data center locale simulato</span><span class="sxs-lookup"><span data-stu-id="b2f81-206">Deploy the simulated on-premises datacenter</span></span>

<span data-ttu-id="b2f81-207">Per distribuire il data center locale simulato come rete virtuale di Azure, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="b2f81-207">To deploy the simulated on-premises datacenter as an Azure VNet, follow these steps:</span></span>

1. <span data-ttu-id="b2f81-208">Passare alla cartella `hybrid-networking/hub-spoke` del repository di architetture di riferimento.</span><span class="sxs-lookup"><span data-stu-id="b2f81-208">Navigate to the `hybrid-networking/hub-spoke` folder of the reference architectures repository.</span></span>

2. <span data-ttu-id="b2f81-209">Aprire il file `onprem.json` .</span><span class="sxs-lookup"><span data-stu-id="b2f81-209">Open the `onprem.json` file.</span></span> <span data-ttu-id="b2f81-210">Sostituire i valori per `adminUsername` e `adminPassword`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-210">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```bash
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

3. <span data-ttu-id="b2f81-211">(Facoltativo) Per una distribuzione Linux, impostare `osType` su `Linux`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-211">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

4. <span data-ttu-id="b2f81-212">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="b2f81-212">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g onprem-vnet-rg -l <location> -p onprem.json --deploy
    ```

5. <span data-ttu-id="b2f81-213">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="b2f81-213">Wait for the deployment to finish.</span></span> <span data-ttu-id="b2f81-214">Questa distribuzione crea una rete virtuale, una macchina virtuale e un gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="b2f81-214">This deployment creates a virtual network, a virtual machine, and a VPN gateway.</span></span> <span data-ttu-id="b2f81-215">Occorreranno circa 40 minuti per creare il gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="b2f81-215">It can take about 40 minutes to create the VPN gateway.</span></span>

### <a name="deploy-the-hub-vnet"></a><span data-ttu-id="b2f81-216">Distribuire la rete virtuale dell'hub</span><span class="sxs-lookup"><span data-stu-id="b2f81-216">Deploy the hub VNet</span></span>

<span data-ttu-id="b2f81-217">Per distribuire la rete virtuale dell'hub, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="b2f81-217">To deploy the hub VNet, perform the following steps.</span></span>

1. <span data-ttu-id="b2f81-218">Aprire il file `hub-vnet.json` .</span><span class="sxs-lookup"><span data-stu-id="b2f81-218">Open the `hub-vnet.json` file.</span></span> <span data-ttu-id="b2f81-219">Sostituire i valori per `adminUsername` e `adminPassword`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-219">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```bash
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="b2f81-220">(Facoltativo) Per una distribuzione Linux, impostare `osType` su `Linux`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-220">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

3. <span data-ttu-id="b2f81-221">Per `sharedKey`, immettere una chiave condivisa per la connessione VPN.</span><span class="sxs-lookup"><span data-stu-id="b2f81-221">For `sharedKey`, enter a shared key for the VPN connection.</span></span> 

    ```bash
    "sharedKey": "",
    ```

4. <span data-ttu-id="b2f81-222">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="b2f81-222">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet.json --deploy
    ```

5. <span data-ttu-id="b2f81-223">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="b2f81-223">Wait for the deployment to finish.</span></span> <span data-ttu-id="b2f81-224">Questa distribuzione crea una rete virtuale, una macchina virtuale, un gateway VPN e una connessione al gateway.</span><span class="sxs-lookup"><span data-stu-id="b2f81-224">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway.</span></span>  <span data-ttu-id="b2f81-225">Occorreranno circa 40 minuti per creare il gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="b2f81-225">It can take about 40 minutes to create the VPN gateway.</span></span>

### <a name="test-connectivity-with-the-hub"></a><span data-ttu-id="b2f81-226">Testare la connettività con l'hub</span><span class="sxs-lookup"><span data-stu-id="b2f81-226">Test connectivity with the hub</span></span>

<span data-ttu-id="b2f81-227">Testare la connettività dall'ambiente locale simulato alla rete virtuale hub dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2f81-227">Test conectivity from the simulated on-premises environment to the hub VNet.</span></span>

<span data-ttu-id="b2f81-228">**Distribuzione in Windows**</span><span class="sxs-lookup"><span data-stu-id="b2f81-228">**Windows deployment**</span></span>

1. <span data-ttu-id="b2f81-229">Usare il portale di Azure per trovare la macchina virtuale denominata `jb-vm1` nel gruppo di risorse `onprem-jb-rg`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-229">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="b2f81-230">Fare clic su `Connect` per aprire una sessione di rimozione accesso per la macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-230">Click `Connect` to open a remove desktop session to the VM.</span></span> <span data-ttu-id="b2f81-231">Usare la password specificata nel file parametro `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-231">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="b2f81-232">Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi alla macchina virtuale jumpbox nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2f81-232">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VM in the hub VNet.</span></span>

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```
<span data-ttu-id="b2f81-233">L'output dovrebbe essere simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b2f81-233">The output should look similar to the following:</span></span>

```powershell
ComputerName     : 10.0.0.68
RemoteAddress    : 10.0.0.68
RemotePort       : 3389
InterfaceAlias   : Ethernet 2
SourceAddress    : 192.168.1.000
TcpTestSucceeded : True
```

> [!NOTE]
> <span data-ttu-id="b2f81-234">Per impostazione predefinita, le VM Windows Server non consentono risposte ICMP in Azure.</span><span class="sxs-lookup"><span data-stu-id="b2f81-234">By default, Windows Server VMs do not allow ICMP responses in Azure.</span></span> <span data-ttu-id="b2f81-235">Se si vuole usare `ping` per testare la connettività, è necessario abilitare il traffico ICMP in Windows Firewall con sicurezza avanzata per ogni VM.</span><span class="sxs-lookup"><span data-stu-id="b2f81-235">If you want to use `ping` to test connectivity, you need to enable ICMP traffic in the Windows Advanced Firewall for each VM.</span></span>

<span data-ttu-id="b2f81-236">**Distribuzione in Linux**</span><span class="sxs-lookup"><span data-stu-id="b2f81-236">**Linux deployment**</span></span>

1. <span data-ttu-id="b2f81-237">Usare il portale di Azure per trovare la macchina virtuale denominata `jb-vm1` nel gruppo di risorse `onprem-jb-rg`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-237">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="b2f81-238">Fare clic su `Connect` e copiare il comando `ssh` visualizzato nel portale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-238">Click `Connect` and copy the `ssh` command shown in the portal.</span></span> 

3. <span data-ttu-id="b2f81-239">Da un prompt di Linux, eseguire `ssh` per connettersi all'ambiente locale simulato.</span><span class="sxs-lookup"><span data-stu-id="b2f81-239">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment.</span></span> <span data-ttu-id="b2f81-240">Usare la password specificata nel file parametro `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-240">Use the password that you specified in the `onprem.json` parameter file.</span></span>

4. <span data-ttu-id="b2f81-241">Usare il comando `ping` per testare la connettività alla macchina virtuale jumpbox nella rete virtuale dell'hub:</span><span class="sxs-lookup"><span data-stu-id="b2f81-241">Use the `ping` command to test connectivity to the jumpbox VM in the hub VNet:</span></span>

   ```bash
   ping 10.0.0.68
   ```

### <a name="deploy-the-spoke-vnets"></a><span data-ttu-id="b2f81-242">Distribuire le reti virtuali spoke</span><span class="sxs-lookup"><span data-stu-id="b2f81-242">Deploy the spoke VNets</span></span>

<span data-ttu-id="b2f81-243">Per distribuire le reti virtuali spoke, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="b2f81-243">To deploy the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="b2f81-244">Aprire il file `spoke1.json` .</span><span class="sxs-lookup"><span data-stu-id="b2f81-244">Open the `spoke1.json` file.</span></span> <span data-ttu-id="b2f81-245">Sostituire i valori per `adminUsername` e `adminPassword`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-245">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```bash
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="b2f81-246">(Facoltativo) Per una distribuzione Linux, impostare `osType` su `Linux`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-246">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

3. <span data-ttu-id="b2f81-247">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="b2f81-247">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg -l <location> -p spoke1.json --deploy
   ```
  
4. <span data-ttu-id="b2f81-248">Ripetere i passaggi 1 e 2 per il file `spoke2.json`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-248">Repeat steps 1-2 for the `spoke2.json` file.</span></span>

5. <span data-ttu-id="b2f81-249">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="b2f81-249">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg -l <location> -p spoke2.json --deploy
   ```

6. <span data-ttu-id="b2f81-250">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="b2f81-250">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet-peering.json --deploy
   ```

### <a name="test-connectivity"></a><span data-ttu-id="b2f81-251">Testare la connettività</span><span class="sxs-lookup"><span data-stu-id="b2f81-251">Test connectivity</span></span>

<span data-ttu-id="b2f81-252">Testare la connettività dall'ambiente locale simulato alle reti virtuali spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-252">Test conectivity from the simulated on-premises environment to the spoke VNets.</span></span>

<span data-ttu-id="b2f81-253">**Distribuzione in Windows**</span><span class="sxs-lookup"><span data-stu-id="b2f81-253">**Windows deployment**</span></span>

1. <span data-ttu-id="b2f81-254">Usare il portale di Azure per trovare la macchina virtuale denominata `jb-vm1` nel gruppo di risorse `onprem-jb-rg`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-254">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="b2f81-255">Fare clic su `Connect` per aprire una sessione di rimozione accesso per la macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-255">Click `Connect` to open a remove desktop session to the VM.</span></span> <span data-ttu-id="b2f81-256">Usare la password specificata nel file parametro `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-256">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="b2f81-257">Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi alla macchina virtuale jumpbox nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2f81-257">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VM in the hub VNet.</span></span>

   ```powershell
   Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
   Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
   ```

<span data-ttu-id="b2f81-258">**Distribuzione in Linux**</span><span class="sxs-lookup"><span data-stu-id="b2f81-258">**Linux deployment**</span></span>

<span data-ttu-id="b2f81-259">Per testare la connettività dall'ambiente locale simulato alle reti virtuali spoke con VM Linux, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="b2f81-259">To test conectivity from the simulated on-premises environment to the spoke VNets using Linux VMs, perform the following steps:</span></span>

1. <span data-ttu-id="b2f81-260">Usare il portale di Azure per trovare la macchina virtuale denominata `jb-vm1` nel gruppo di risorse `onprem-jb-rg`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-260">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="b2f81-261">Fare clic su `Connect` e copiare il comando `ssh` visualizzato nel portale.</span><span class="sxs-lookup"><span data-stu-id="b2f81-261">Click `Connect` and copy the `ssh` command shown in the portal.</span></span> 

3. <span data-ttu-id="b2f81-262">Da un prompt di Linux, eseguire `ssh` per connettersi all'ambiente locale simulato.</span><span class="sxs-lookup"><span data-stu-id="b2f81-262">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment.</span></span> <span data-ttu-id="b2f81-263">Usare la password specificata nel file parametro `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-263">Use the password that you specified in the `onprem.json` parameter file.</span></span>

5. <span data-ttu-id="b2f81-264">Usare il comando `ping` per testare la connettività alle macchine virtuali jumpbox in ogni spoke:</span><span class="sxs-lookup"><span data-stu-id="b2f81-264">Use the `ping` command to test connectivity to the jumpbox VMs in each spoke:</span></span>

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

### <a name="add-connectivity-between-spokes"></a><span data-ttu-id="b2f81-265">Aggiungere la connettività tra spoke</span><span class="sxs-lookup"><span data-stu-id="b2f81-265">Add connectivity between spokes</span></span>

<span data-ttu-id="b2f81-266">Questo passaggio è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="b2f81-266">This step is optional.</span></span> <span data-ttu-id="b2f81-267">Se si vuole consentire la connessione tra gli spoke, è necessario usare un'appliance virtuale di rete come router nella rete virtuale dell'hub e forzare il traffico dagli spoke verso il router in caso di tentativo di connessione a un altro spoke.</span><span class="sxs-lookup"><span data-stu-id="b2f81-267">If you want to allow spokes to connect to each other, you must use a newtwork virtual appliance (NVA) as a router in the hub VNet, and force traffic from spokes to the router when trying to connect to another spoke.</span></span> <span data-ttu-id="b2f81-268">Per distribuire un'appliance virtuale di rete di esempio di base come una singola macchina virtuale assieme alle route definite dall'utente necessarie per consentire la connessione delle due reti virtuali spoke, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="b2f81-268">To deploy a basic sample NVA as a single VM, along with user-defined routes (UDRs) to allow the two spoke VNets to connect, perform the following steps:</span></span>

1. <span data-ttu-id="b2f81-269">Aprire il file `hub-nva.json` .</span><span class="sxs-lookup"><span data-stu-id="b2f81-269">Open the `hub-nva.json` file.</span></span> <span data-ttu-id="b2f81-270">Sostituire i valori per `adminUsername` e `adminPassword`.</span><span class="sxs-lookup"><span data-stu-id="b2f81-270">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```bash
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="b2f81-271">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="b2f81-271">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg -l <location> -p hub-nva.json --deploy
   ```

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/hub-spoke.png "Topologia hub-spoke in Azure"
[1]: ./images/hub-spoke-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo usando un'appliance virtuale di rete"
[3]: ./images/hub-spokehub-spoke.svg "Topologia hub-spoke-hub-spoke in Azure"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
