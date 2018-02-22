---
title: Implementazione una topologia di rete hub-spoke in Azure
description: Come implementare una topologia di rete hub-spoke in Azure.
author: telmosampaio
ms.date: 02/14/2018
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: c03ecd4ba5ddbe50cfb17e56d75c18102b751cfb
ms.sourcegitcommit: 475064f0a3c2fac23e1286ba159aaded287eec86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2018
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a><span data-ttu-id="9003b-103">Implementare una topologia di rete hub-spoke in Azure</span><span class="sxs-lookup"><span data-stu-id="9003b-103">Implement a hub-spoke network topology in Azure</span></span>

<span data-ttu-id="9003b-104">Questa architettura di riferimento illustra come implementare una topologia hub-spoke in Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-104">This reference architecture shows how to implement a hub-spoke topology in Azure.</span></span> <span data-ttu-id="9003b-105">L'*hub* è una rete virtuale in Azure che funge da punto di connettività centrale alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="9003b-105">The *hub* is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="9003b-106">Gli *spoke* sono le reti virtuali che eseguono il peering con l'hub e possono essere usate per isolare i carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="9003b-106">The *spokes* are VNets that peer with the hub, and can be used to isolate workloads.</span></span> <span data-ttu-id="9003b-107">Flussi di traffico tra il data center locale e l'hub attraverso una connessione di gateway VPN o ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="9003b-107">Traffic flows between the on-premises datacenter and the hub through an ExpressRoute or VPN gateway connection.</span></span>  <span data-ttu-id="9003b-108">[**Distribuire questa soluzione**](#deploy-the-solution).</span><span class="sxs-lookup"><span data-stu-id="9003b-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="9003b-109">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="9003b-109">![[0]][0]</span></span>

<span data-ttu-id="9003b-110">*Scaricare un [file di Visio][visio-download] di questa architettura*</span><span class="sxs-lookup"><span data-stu-id="9003b-110">*Download a [Visio file][visio-download] of this architecture*</span></span>


<span data-ttu-id="9003b-111">I vantaggi di questa topologia includono:</span><span class="sxs-lookup"><span data-stu-id="9003b-111">The benefits of this toplogy include:</span></span>

* <span data-ttu-id="9003b-112">**Risparmio sui costi**, centralizzando in un'unica posizione i servizi che possono essere condivisi da più carichi di lavoro, ad esempio appliance virtuali di rete e server DNS.</span><span class="sxs-lookup"><span data-stu-id="9003b-112">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
* <span data-ttu-id="9003b-113">**Superamento dei limiti delle sottoscrizioni** eseguendo il peering delle reti virtuali da sottoscrizioni diverse all'hub centrale.</span><span class="sxs-lookup"><span data-stu-id="9003b-113">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
* <span data-ttu-id="9003b-114">**Separazione dei compiti** tra IT centrale (SecOPs, InfraOps) e carichi di lavoro (DevOps).</span><span class="sxs-lookup"><span data-stu-id="9003b-114">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="9003b-115">Tra gli usi tipici di questa architettura vi sono:</span><span class="sxs-lookup"><span data-stu-id="9003b-115">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="9003b-116">Carichi di lavoro distribuiti in ambienti diversi, ad esempio sviluppo, test e produzione, che richiedono servizi condivisi, ad esempio DNS, IDS, NTP o Active Directory Domain Services.</span><span class="sxs-lookup"><span data-stu-id="9003b-116">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="9003b-117">I servizi condivisi vengono inseriti nella rete virtuale dell'hub, mentre ogni ambiente viene distribuito in uno spoke per mantenere l'isolamento.</span><span class="sxs-lookup"><span data-stu-id="9003b-117">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
* <span data-ttu-id="9003b-118">Carichi di lavoro che non richiedono connettività uno con l'altro, ma richiedono l'accesso ai servizi condivisi.</span><span class="sxs-lookup"><span data-stu-id="9003b-118">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
* <span data-ttu-id="9003b-119">Aziende che richiedono il controllo centrale sugli aspetti di sicurezza, ad esempio un firewall nell'hub come rete perimetrale, e gestione separata per i carichi di lavoro in ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-119">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="9003b-120">Architecture</span><span class="sxs-lookup"><span data-stu-id="9003b-120">Architecture</span></span>

<span data-ttu-id="9003b-121">L'architettura è costituita dai componenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="9003b-121">The architecture consists of the following components.</span></span>

* <span data-ttu-id="9003b-122">**Rete locale**.</span><span class="sxs-lookup"><span data-stu-id="9003b-122">**On-premises network**.</span></span> <span data-ttu-id="9003b-123">Una rete LAN privata in esecuzione all'interno di un'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="9003b-123">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="9003b-124">**Dispositivo VPN**.</span><span class="sxs-lookup"><span data-stu-id="9003b-124">**VPN device**.</span></span> <span data-ttu-id="9003b-125">Un dispositivo o un servizio che offre connettività esterna alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="9003b-125">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="9003b-126">Il dispositivo VPN può essere un dispositivo hardware o una soluzione software, ad esempio il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012.</span><span class="sxs-lookup"><span data-stu-id="9003b-126">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="9003b-127">Per un elenco delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN da sito a sito][vpn-appliance].</span><span class="sxs-lookup"><span data-stu-id="9003b-127">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="9003b-128">**Gateway di rete virtuale VPN o gateway ExpressRoute**.</span><span class="sxs-lookup"><span data-stu-id="9003b-128">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="9003b-129">Il gateway di rete virtuale consente di connettere la rete virtuale al dispositivo VPN o al circuito ExpressRoute usato per la connettività con la propria rete locale.</span><span class="sxs-lookup"><span data-stu-id="9003b-129">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="9003b-130">Per altre informazioni, vedere [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].</span><span class="sxs-lookup"><span data-stu-id="9003b-130">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="9003b-131">Gli script di distribuzione per questa architettura di riferimento usano un gateway VPN per la connettività e una rete virtuale in Azure per simulare la rete locale.</span><span class="sxs-lookup"><span data-stu-id="9003b-131">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

* <span data-ttu-id="9003b-132">**Rete virtuale dell'hub**.</span><span class="sxs-lookup"><span data-stu-id="9003b-132">**Hub VNet**.</span></span> <span data-ttu-id="9003b-133">Rete virtuale di Azure usata come hub nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-133">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="9003b-134">L'hub è il punto di connettività centrale alla rete locale e offre una posizione in cui ospitare servizi che possono essere utilizzati da carichi di lavoro diversi ospitati nelle reti virtuali spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-134">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

* <span data-ttu-id="9003b-135">**Subnet del gateway**.</span><span class="sxs-lookup"><span data-stu-id="9003b-135">**Gateway subnet**.</span></span> <span data-ttu-id="9003b-136">I gateway di rete virtuale vengono mantenuti nella stessa subnet.</span><span class="sxs-lookup"><span data-stu-id="9003b-136">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="9003b-137">**Subnet dei servizi condivisi**.</span><span class="sxs-lookup"><span data-stu-id="9003b-137">**Shared services subnet**.</span></span> <span data-ttu-id="9003b-138">Una subnet nella rete virtuale dell'hub usata per ospitare servizi che possono essere condivisi tra tutti gli spoke, ad esempio DNS o Active Directory Domain Services.</span><span class="sxs-lookup"><span data-stu-id="9003b-138">A subnet in the hub VNet used to host services that can be shared among all spokes, such as DNS or AD DS.</span></span>

* <span data-ttu-id="9003b-139">**Reti virtuali spoke**.</span><span class="sxs-lookup"><span data-stu-id="9003b-139">**Spoke VNets**.</span></span> <span data-ttu-id="9003b-140">Una o più reti virtuali di Azure usate come spoke nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-140">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="9003b-141">Gli spoke possono essere usati per isolare i carichi di lavoro nelle reti virtuali corrispondenti, gestite separatamente rispetto agli altri spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-141">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="9003b-142">Ogni carico di lavoro può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-142">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="9003b-143">Per ulteriori informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] e [Esecuzione di carichi di lavoro della macchina virtuale Linux][linux-vm-ra].</span><span class="sxs-lookup"><span data-stu-id="9003b-143">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="9003b-144">**Peering reti virtuali**.</span><span class="sxs-lookup"><span data-stu-id="9003b-144">**VNet peering**.</span></span> <span data-ttu-id="9003b-145">È possibile connettere due reti virtuali nella stessa area di Azure tramite una [connessione peering][vnet-peering].</span><span class="sxs-lookup"><span data-stu-id="9003b-145">Two VNets in the same Azure region can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="9003b-146">Le connessioni peering sono connessioni non transitive a bassa latenza tra reti virtuali.</span><span class="sxs-lookup"><span data-stu-id="9003b-146">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="9003b-147">Dopo il peering, le reti virtuali si scambiano traffico tramite il backbone di Azure, senza bisogno di un router.</span><span class="sxs-lookup"><span data-stu-id="9003b-147">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="9003b-148">In una topologia hub-spoke di rete si usa il peering reti virtuali per connettere l'hub a ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-148">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span>

> [!NOTE]
> <span data-ttu-id="9003b-149">Questo articolo illustra solo le distribuzioni [Resource Manager](/azure/azure-resource-manager/resource-group-overview), ma è anche possibile connettere una rete virtuale classica a una rete virtuale di Resource Manager nella stessa sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="9003b-149">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="9003b-150">In questo modo, gli spoke possono ospitare distribuzioni classiche e trarre comunque vantaggio dai servizi condivisi nell'hub.</span><span class="sxs-lookup"><span data-stu-id="9003b-150">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>


## <a name="recommendations"></a><span data-ttu-id="9003b-151">Raccomandazioni</span><span class="sxs-lookup"><span data-stu-id="9003b-151">Recommendations</span></span>

<span data-ttu-id="9003b-152">Le raccomandazioni seguenti sono valide per la maggior parte degli scenari.</span><span class="sxs-lookup"><span data-stu-id="9003b-152">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="9003b-153">Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.</span><span class="sxs-lookup"><span data-stu-id="9003b-153">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="9003b-154">Gruppi di risorse</span><span class="sxs-lookup"><span data-stu-id="9003b-154">Resource groups</span></span>

<span data-ttu-id="9003b-155">È possibile implementare la rete virtuale dell'hub e ogni rete virtuale spoke in gruppi di risorse diversi e persino in sottoscrizioni diverse, a condizione che appartengano allo stesso tenant di Azure Active Directory (Azure AD) nella stessa area di Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-155">The hub VNet, and each spoke VNet, can be implemented in different resource groups, and even different subscriptions, as long as they belong to the same Azure Active Directory (Azure AD) tenant in the same Azure region.</span></span> <span data-ttu-id="9003b-156">Questo consente di decentralizzare la gestione di ogni carico di lavoro e allo stesso tempo condividere i servizi gestiti nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="9003b-156">This allows for a decentralized management of each workload, while sharing services maintained in the hub VNet.</span></span>

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="9003b-157">Reti virtuali e GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="9003b-157">VNet and GatewaySubnet</span></span>

<span data-ttu-id="9003b-158">Creare una subnet denominata *GatewaySubnet*, con un intervallo di indirizzi di /27.</span><span class="sxs-lookup"><span data-stu-id="9003b-158">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="9003b-159">Questa subnet è necessaria per il gateway di rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="9003b-159">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="9003b-160">Allocare 32 indirizzi a questa subnet contribuirà in futuro a evitare di raggiungere i limiti di dimensioni del gateway.</span><span class="sxs-lookup"><span data-stu-id="9003b-160">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span>

<span data-ttu-id="9003b-161">Per altre informazioni sulla configurazione del gateway, vedere le architetture di riferimento seguenti, a seconda del tipo di connessione:</span><span class="sxs-lookup"><span data-stu-id="9003b-161">For more information about setting up the gateway, see the following reference architectures, depending on your connection type:</span></span>

- <span data-ttu-id="9003b-162">[Rete ibrida tramite ExpressRoute][guidance-expressroute]</span><span class="sxs-lookup"><span data-stu-id="9003b-162">[Hybrid network using ExpressRoute][guidance-expressroute]</span></span>
- <span data-ttu-id="9003b-163">[Rete ibrida tramite un gateway VPN][guidance-vpn]</span><span class="sxs-lookup"><span data-stu-id="9003b-163">[Hybrid network using a VPN gateway][guidance-vpn]</span></span>

<span data-ttu-id="9003b-164">Per una disponibilità più elevata, è possibile usare ExpressRoute più una rete VPN per il failover.</span><span class="sxs-lookup"><span data-stu-id="9003b-164">For higher availability, you can use ExpressRoute plus a VPN for failover.</span></span> <span data-ttu-id="9003b-165">Vedere [Connettere una rete locale ad Azure tramite ExpressRoute con failover VPN][hybrid-ha].</span><span class="sxs-lookup"><span data-stu-id="9003b-165">See [Connect an on-premises network to Azure using ExpressRoute with VPN failover][hybrid-ha].</span></span>

<span data-ttu-id="9003b-166">Una topologia hub-spoke può essere usata anche senza gateway, se non è necessaria la connettività con la rete locale.</span><span class="sxs-lookup"><span data-stu-id="9003b-166">A hub-spoke topology can also be used without a gateway, if you don't need connectivity with your on-premises network.</span></span> 

### <a name="vnet-peering"></a><span data-ttu-id="9003b-167">Peering reti virtuali</span><span class="sxs-lookup"><span data-stu-id="9003b-167">VNet peering</span></span>

<span data-ttu-id="9003b-168">Il peering reti virtuali è una relazione non transitiva tra due reti virtuali.</span><span class="sxs-lookup"><span data-stu-id="9003b-168">VNet peering is a non-transitive relationship between two VNets.</span></span> <span data-ttu-id="9003b-169">Se è necessaria la connettività tra gli spoke, considerare l'aggiunta di una connessione peering separata tra di essi.</span><span class="sxs-lookup"><span data-stu-id="9003b-169">If you require spokes to connect to each other, consider adding a separate peering connection between those spokes.</span></span>

<span data-ttu-id="9003b-170">Tuttavia, se si dispone di più spoke che devono connettersi tra loro, le possibili connessioni peering si esauriranno molto rapidamente, a causa del [limite di peering reti virtuali per ogni rete virtuale][vnet-peering-limit].</span><span class="sxs-lookup"><span data-stu-id="9003b-170">However, if you have several spokes that need to connect with each other, you will run out of possible peering connections very quickly due to the [limitation on number of VNets peerings per VNet][vnet-peering-limit].</span></span> <span data-ttu-id="9003b-171">In questo scenario, prendere in considerazione l'uso di route definite dall'utente per forzare l'invio del traffico destinato a uno spoke a un'appliance virtuale di rete che funga da router nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="9003b-171">In this scenario, consider using user defined routes (UDRs) to force traffic destined to a spoke to be sent to an NVA acting as a router at the hub VNet.</span></span> <span data-ttu-id="9003b-172">Questo consentirà agli spoke di connettersi tra loro.</span><span class="sxs-lookup"><span data-stu-id="9003b-172">This will allow the spokes to connect to each other.</span></span>

<span data-ttu-id="9003b-173">È anche possibile configurare gli spoke in modo da usare il gateway della rete virtuale dell'hub per comunicare con reti remote.</span><span class="sxs-lookup"><span data-stu-id="9003b-173">You can also configure spokes to use the hub VNet gateway to communicate with remote networks.</span></span> <span data-ttu-id="9003b-174">Per consentire il flusso del traffico gateway dallo spoke all'hub e connettersi a reti remote, è necessario:</span><span class="sxs-lookup"><span data-stu-id="9003b-174">To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:</span></span>

  - <span data-ttu-id="9003b-175">Configurare la connessione peering reti virtuali nell'hub in modo da **consentire il transito gateway**.</span><span class="sxs-lookup"><span data-stu-id="9003b-175">Configure the VNet peering connection in the hub to **allow gateway transit**.</span></span>
  - <span data-ttu-id="9003b-176">Configurare la connessione peering reti virtuali in ogni spoke in modo da **usare gateway remoti**.</span><span class="sxs-lookup"><span data-stu-id="9003b-176">Configure the VNet peering connection in each spoke to **use remote gateways**.</span></span>
  - <span data-ttu-id="9003b-177">Configurare tutte le connessioni peering reti virtuali in modo da **consentire il traffico inoltrato**.</span><span class="sxs-lookup"><span data-stu-id="9003b-177">Configure all VNet peering connections to **allow forwarded traffic**.</span></span>

## <a name="considerations"></a><span data-ttu-id="9003b-178">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="9003b-178">Considerations</span></span>

### <a name="spoke-connectivity"></a><span data-ttu-id="9003b-179">Connettività spoke</span><span class="sxs-lookup"><span data-stu-id="9003b-179">Spoke connectivity</span></span>

<span data-ttu-id="9003b-180">Se è necessaria la connettività tra spoke, è consigliabile implementare un'appliance virtuale di rete per il routing nell'hub e usare route definite dall'utente nello spoke per inoltrare il traffico all'hub.</span><span class="sxs-lookup"><span data-stu-id="9003b-180">If you require connectivity between spokes, consider implementing an NVA for routing in the hub, and using UDRs in the spoke to forward traffic to the hub.</span></span>

<span data-ttu-id="9003b-181">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="9003b-181">![[2]][2]</span></span>

<span data-ttu-id="9003b-182">In questo scenario occorre configurare le connessioni peering in modo da **consentire il traffico inoltrato**.</span><span class="sxs-lookup"><span data-stu-id="9003b-182">In this scenario, you must configure the peering connections to **allow forwarded traffic**.</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="9003b-183">Superamento dei limiti del peering reti virtuali</span><span class="sxs-lookup"><span data-stu-id="9003b-183">Overcoming VNet peering limits</span></span>

<span data-ttu-id="9003b-184">Assicurarsi di considerare il [limite sul numero di peering reti virtuali per ogni rete virtuale][vnet-peering-limit] in Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-184">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="9003b-185">Se si stabilisce che saranno necessari più spoke di quanto consentito dal limite, valutare la creazione di una topologia hub-spoke-hub-spoke, in cui il primo livello di spoke funge anche da hub.</span><span class="sxs-lookup"><span data-stu-id="9003b-185">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="9003b-186">Il diagramma seguente illustra questo approccio.</span><span class="sxs-lookup"><span data-stu-id="9003b-186">The following diagram shows this approach.</span></span>

<span data-ttu-id="9003b-187">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="9003b-187">![[3]][3]</span></span>

<span data-ttu-id="9003b-188">Considerare inoltre i servizi condivisi nell'hub, per assicurarsi che quest'ultimo sia scalabile per un numero superiore di spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-188">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="9003b-189">Ad esempio, se l'hub fornisce servizi firewall, valutare i limiti di larghezza di banda della soluzione firewall quando si aggiungono più spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-189">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="9003b-190">Può essere utile spostare alcuni dei servizi condivisi in un secondo livello di hub.</span><span class="sxs-lookup"><span data-stu-id="9003b-190">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="9003b-191">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="9003b-191">Deploy the solution</span></span>

<span data-ttu-id="9003b-192">Una distribuzione di questa architettura è disponibile in [GitHub][ref-arch-repo].</span><span class="sxs-lookup"><span data-stu-id="9003b-192">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="9003b-193">Usa VM Ubuntu in ogni rete virtuale per testare la connettività.</span><span class="sxs-lookup"><span data-stu-id="9003b-193">It uses Ubuntu VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="9003b-194">Nella subnet **shared-services** della **rete virtuale dell'hub** non sono effettivamente ospitati servizi.</span><span class="sxs-lookup"><span data-stu-id="9003b-194">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="9003b-195">prerequisiti</span><span class="sxs-lookup"><span data-stu-id="9003b-195">Prerequisites</span></span>

<span data-ttu-id="9003b-196">Prima di poter distribuire l'architettura di riferimento nella propria sottoscrizione, è necessario eseguire i passaggi seguenti.</span><span class="sxs-lookup"><span data-stu-id="9003b-196">Before you can deploy the reference architecture to your own subscription, you must perform the following steps.</span></span>

1. <span data-ttu-id="9003b-197">Clonare, creare una copia tramite fork o scaricare il file ZIP per il repository GitHub delle [architetture di riferimento AzureCAT][ref-arch-repo].</span><span class="sxs-lookup"><span data-stu-id="9003b-197">Clone, fork, or download the zip file for the [AzureCAT reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="9003b-198">Se si preferisce usare l'interfaccia della riga di comando di Azure, verificare che nel computer sia installata l'interfaccia della riga di comando di Azure 2.0.</span><span class="sxs-lookup"><span data-stu-id="9003b-198">If you prefer to use the Azure CLI, make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="9003b-199">Per installare l'interfaccia della riga di comando, seguire le istruzioni in [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-2].</span><span class="sxs-lookup"><span data-stu-id="9003b-199">To install the CLI, follow the instructions in [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="9003b-200">Se si preferisce usare PowerShell, verificare che nel computer sia installato il modulo PowerShell per Azure più recente.</span><span class="sxs-lookup"><span data-stu-id="9003b-200">If you prefer to use PowerShell, make sure you have the latest PowerShell module for Azure installed on you computer.</span></span> <span data-ttu-id="9003b-201">Per installare l'ultimo modulo Azure PowerShell, seguire le istruzioni in [Installare PowerShell per Azure][azure-powershell].</span><span class="sxs-lookup"><span data-stu-id="9003b-201">To install the latest Azure PowerShell module, follow the instructions in [Install PowerShell for Azure][azure-powershell].</span></span>

4. <span data-ttu-id="9003b-202">Da un prompt dei comandi, di Bash o di PowerShell accedere al proprio account di Azure usando uno dei comandi riportati di seguito e seguire le istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9003b-202">From a command prompt, bash prompt, or PowerShell prompt, login to your Azure account by using one of the commands below, and follow the prompts.</span></span>

  ```bash
  az login
  ```

  ```powershell
  Login-AzureRmAccount
  ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a><span data-ttu-id="9003b-203">Distribuire il data center locale simulato</span><span class="sxs-lookup"><span data-stu-id="9003b-203">Deploy the simulated on-premises datacenter</span></span>

<span data-ttu-id="9003b-204">Per distribuire il data center locale simulato come rete virtuale di Azure, eseguire la procedura seguente.</span><span class="sxs-lookup"><span data-stu-id="9003b-204">To deploy the simulated on-premises datacenter as an Azure VNet, perform the following steps.</span></span>

1. <span data-ttu-id="9003b-205">Passare alla cartella `hybrid-networking\hub-spoke\onprem` per il repository scaricato nel passaggio dei prerequisiti precedente.</span><span class="sxs-lookup"><span data-stu-id="9003b-205">Navigate to the `hybrid-networking\hub-spoke\onprem` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="9003b-206">Aprire il file `onprem.vm.parameters.json` e immettere un nome utente e una password tra le virgolette nelle righe 11 e 12, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9003b-206">Open the `onprem.vm.parameters.json` file and enter a username and password between the quotes in line 11 and 12, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. <span data-ttu-id="9003b-207">Eseguire il comando di Bash o PowerShell seguente per distribuire l'ambiente locale simulato come una virtuale in Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-207">Run the bash or PowerShell command below to deploy the simulated on-premises environment as a VNet in Azure.</span></span> <span data-ttu-id="9003b-208">Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-208">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./onprem.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-onprem-rg \
    --location westus
  ```

  ```powershell
  ./onprem.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-onprem-rg `
    -Location westus
  ```
  > [!NOTE]
  > <span data-ttu-id="9003b-209">Se si decide di usare un nome del gruppo di risorse diverso da `ra-onprem-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9003b-209">If you decide to use a different resource group name (other than `ra-onprem-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

4. <span data-ttu-id="9003b-210">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9003b-210">Wait for the deployment to finish.</span></span> <span data-ttu-id="9003b-211">Questa distribuzione crea una rete virtuale, una macchina virtuale che esegue Ubuntu e un gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="9003b-211">This deployment creates a virtual network, a virtual machine running Ubuntu, and a VPN gateway.</span></span> <span data-ttu-id="9003b-212">La creazione del gateway VPN può richiedere più di 40 minuti.</span><span class="sxs-lookup"><span data-stu-id="9003b-212">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="azure-hub-vnet"></a><span data-ttu-id="9003b-213">Rete virtuale dell'hub di Azure</span><span class="sxs-lookup"><span data-stu-id="9003b-213">Azure hub VNet</span></span>

<span data-ttu-id="9003b-214">Per distribuire la rete virtuale dell'hub e connettersi alla rete virtuale locale simulata creata in precedenza, eseguire la procedura seguente.</span><span class="sxs-lookup"><span data-stu-id="9003b-214">To deploy the hub VNet, and connect to the simulated on-premises VNet created above, perform the following steps.</span></span>

1. <span data-ttu-id="9003b-215">Passare alla cartella `hybrid-networking\hub-spoke\hub` per il repository scaricato nel passaggio dei prerequisiti precedente.</span><span class="sxs-lookup"><span data-stu-id="9003b-215">Navigate to the `hybrid-networking\hub-spoke\hub` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="9003b-216">Aprire il file `hub.vm.parameters.json` e immettere un nome utente e una password tra le virgolette nelle righe 11 e 12, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9003b-216">Open the `hub.vm.parameters.json` file and enter a username and password between the quotes in line 11 and 12, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. <span data-ttu-id="9003b-217">Aprire il file `hub.gateway.parameters.json` e immettere una chiave condivisa tra le virgolette nella riga 23, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9003b-217">Open the `hub.gateway.parameters.json` file and enter a shared key between the quotes in line 23, as shown below, then save the file.</span></span> <span data-ttu-id="9003b-218">Prendere nota di questo valore, che sarà necessario più avanti nella distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9003b-218">Keep a note of this value, you will need to use it later in the deployment.</span></span>

  ```bash
  "sharedKey": "",
  ```

4. <span data-ttu-id="9003b-219">Eseguire il comando di Bash o PowerShell seguente per distribuire l'ambiente locale simulato come una virtuale in Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-219">Run the bash or PowerShell command below to deploy the simulated on-premises environment as a VNet in Azure.</span></span> <span data-ttu-id="9003b-220">Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-220">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./hub.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus
  ```

  ```powershell
  ./hub.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus
  ```
  > [!NOTE]
  > <span data-ttu-id="9003b-221">Se si decide di usare un nome del gruppo di risorse diverso da `ra-hub-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9003b-221">If you decide to use a different resource group name (other than `ra-hub-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="9003b-222">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9003b-222">Wait for the deployment to finish.</span></span> <span data-ttu-id="9003b-223">Questa distribuzione crea una rete virtuale, una macchina virtuale che esegue Ubuntu, un gateway VPN e una connessione al gateway creato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="9003b-223">This deployment creates a virtual network, a virtual machine running Ubuntu, a VPN gateway, and a connection to the gateway created in the previous section.</span></span> <span data-ttu-id="9003b-224">La creazione del gateway VPN può richiedere più di 40 minuti.</span><span class="sxs-lookup"><span data-stu-id="9003b-224">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="connection-from-on-premises-to-the-hub"></a><span data-ttu-id="9003b-225">Connessione da locale all'hub</span><span class="sxs-lookup"><span data-stu-id="9003b-225">Connection from on-premises to the hub</span></span>

<span data-ttu-id="9003b-226">Per connettersi dal data center locale simulato alla rete virtuale dell'hub, eseguire la procedura seguente.</span><span class="sxs-lookup"><span data-stu-id="9003b-226">To connect from the simulated on-premises datacenter to the hub VNet, perform the following steps.</span></span>

1. <span data-ttu-id="9003b-227">Passare alla cartella `hybrid-networking\hub-spoke\onprem` per il repository scaricato nel passaggio dei prerequisiti precedente.</span><span class="sxs-lookup"><span data-stu-id="9003b-227">Navigate to the `hybrid-networking\hub-spoke\onprem` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="9003b-228">Aprire il file `onprem.connection.parameters.json` e immettere una chiave condivisa tra le virgolette nella riga 9, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9003b-228">Open the `onprem.connection.parameters.json` file and enter a shared key between the quotes in line 9, as shown below, then save the file.</span></span> <span data-ttu-id="9003b-229">Il valore di questa chiave condivisa deve essere lo stesso usato nel gateway locale distribuito in precedenza.</span><span class="sxs-lookup"><span data-stu-id="9003b-229">This shared key value must be the same used in the on-premises gateway you deployed previously.</span></span>

  ```bash
  "sharedKey": "",
  ```

3. <span data-ttu-id="9003b-230">Eseguire il comando di Bash o PowerShell seguente per distribuire l'ambiente locale simulato come una virtuale in Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-230">Run the bash or PowerShell command below to deploy the simulated on-premises environment as a VNet in Azure.</span></span> <span data-ttu-id="9003b-231">Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-231">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./onprem.connection.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-onprem-rg \
    --location westus
  ```

  ```powershell
  ./onprem.connection.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-onprem-rg `
    -Location westus
  ```
  > [!NOTE]
  > <span data-ttu-id="9003b-232">Se si decide di usare un nome del gruppo di risorse diverso da `ra-onprem-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9003b-232">If you decide to use a different resource group name (other than `ra-onprem-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

4. <span data-ttu-id="9003b-233">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9003b-233">Wait for the deployment to finish.</span></span> <span data-ttu-id="9003b-234">Questa distribuzione crea una connessione tra la rete virtuale usata per simulare un data center locale e la rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="9003b-234">This deployment creates a connection between the VNet used to simulate an on-premises datacenter, and the hub VNet.</span></span>

### <a name="azure-spoke-vnets"></a><span data-ttu-id="9003b-235">Reti virtuali spoke di Azure</span><span class="sxs-lookup"><span data-stu-id="9003b-235">Azure spoke VNets</span></span>

<span data-ttu-id="9003b-236">Per distribuire le reti virtuali spoke e connettersi alla rete virtuale dell'hub creata in precedenza, eseguire la procedura seguente.</span><span class="sxs-lookup"><span data-stu-id="9003b-236">To deploy the spoke VNets, and connect to the hub VNet created above, perform the following steps.</span></span>

1. <span data-ttu-id="9003b-237">Passare alla cartella `hybrid-networking\hub-spoke\spokes` per il repository scaricato nel passaggio di prerequisiti precedente.</span><span class="sxs-lookup"><span data-stu-id="9003b-237">Switch to the `hybrid-networking\hub-spoke\spokes` folder for the repository you download in the pre-requisites step above.</span></span>

2. <span data-ttu-id="9003b-238">Aprire il file `spoke1.web.parameters.json` e immettere un nome utente e una password tra le virgolette nelle righe 53 e 54, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9003b-238">Open the `spoke1.web.parameters.json` file and enter a username and password between the quotes in line 53 and 54, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. <span data-ttu-id="9003b-239">Ripetere il passaggio precedente per il file `spoke2.web.parameters.json`.</span><span class="sxs-lookup"><span data-stu-id="9003b-239">Repeat the previous step for file `spoke2.web.parameters.json`.</span></span>

4. <span data-ttu-id="9003b-240">Eseguire il comando di Bash o PowerShell seguente per distribuire il primo spoke e connetterlo all'hub.</span><span class="sxs-lookup"><span data-stu-id="9003b-240">Run the bash or PowerShell command below to deploy the first spoke and connect it to the hub.</span></span> <span data-ttu-id="9003b-241">Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-241">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./spoke.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke1-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./spoke.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke1-rg `
    -Location westus `
    -Spoke 1
  ```
  > [!NOTE]
  > <span data-ttu-id="9003b-242">Se si decide di usare un nome del gruppo di risorse diverso da `ra-spoke1-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9003b-242">If you decide to use a different resource group name (other than `ra-spoke1-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="9003b-243">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9003b-243">Wait for the deployment to finish.</span></span> <span data-ttu-id="9003b-244">Questa distribuzione crea una rete virtuale, un sistema di bilanciamento del carico con tre macchine virtuali che eseguono Ubuntu e Apache e una connessione peering reti virtuali alla rete virtuale dell'hub creata nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="9003b-244">This deployment creates a virtual network, a load balancer with three virtual machine running Ubuntu and Apache, and a VNet peering connection to the hub VNet created in the previous section.</span></span> <span data-ttu-id="9003b-245">La distribuzione potrebbe richiedere più di 20 minuti.</span><span class="sxs-lookup"><span data-stu-id="9003b-245">This deployment may take over 20 minutes.</span></span>

6. <span data-ttu-id="9003b-246">Eseguire il comando di Bash o PowerShell seguente per distribuire il primo spoke e connetterlo all'hub.</span><span class="sxs-lookup"><span data-stu-id="9003b-246">Run the bash or PowerShell command below to deploy the first spoke and connect it to the hub.</span></span> <span data-ttu-id="9003b-247">Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-247">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./spoke.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke2-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./spoke.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke2-rg `
    -Location westus `
    -Spoke 2
  ```
  > [!NOTE]
  > <span data-ttu-id="9003b-248">Se si decide di usare un nome del gruppo di risorse diverso da `ra-spoke2-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9003b-248">If you decide to use a different resource group name (other than `ra-spoke2-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="9003b-249">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9003b-249">Wait for the deployment to finish.</span></span> <span data-ttu-id="9003b-250">Questa distribuzione crea una rete virtuale, un sistema di bilanciamento del carico con tre macchine virtuali che eseguono Ubuntu e Apache e una connessione peering reti virtuali alla rete virtuale dell'hub creata nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="9003b-250">This deployment creates a virtual network, a load balancer with three virtual machine running Ubuntu and Apache, and a VNet peering connection to the hub VNet created in the previous section.</span></span> <span data-ttu-id="9003b-251">La distribuzione potrebbe richiedere più di 20 minuti.</span><span class="sxs-lookup"><span data-stu-id="9003b-251">This deployment may take over 20 minutes.</span></span>

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a><span data-ttu-id="9003b-252">Peering reti virtuali dell'hub di Azure alle reti virtuali spoke</span><span class="sxs-lookup"><span data-stu-id="9003b-252">Azure hub VNet peering to spoke VNets</span></span>

<span data-ttu-id="9003b-253">Per distribuire le connessioni peering reti virtuali per la rete virtuale dell'hub, eseguire la procedura seguente.</span><span class="sxs-lookup"><span data-stu-id="9003b-253">To deploy the VNet peering connections for the hub VNet, perform the following steps.</span></span>

1. <span data-ttu-id="9003b-254">Passare alla cartella `hybrid-networking\hub-spoke\hub` per il repository scaricato nel passaggio di prerequisiti precedente.</span><span class="sxs-lookup"><span data-stu-id="9003b-254">Switch to the `hybrid-networking\hub-spoke\hub` folder for the repository you download in the pre-requisites step above.</span></span>

2. <span data-ttu-id="9003b-255">Eseguire il comando di Bash o PowerShell seguente per distribuire la connessione peering al primo spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-255">Run the bash or PowerShell command below to deploy the peering connection to the first spoke.</span></span> <span data-ttu-id="9003b-256">Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-256">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./hub.peering.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./hub.peering.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus `
    -Spoke 1
  ```

2. <span data-ttu-id="9003b-257">Eseguire il comando di Bash o PowerShell seguente per distribuire la connessione peering al secondo spoke.</span><span class="sxs-lookup"><span data-stu-id="9003b-257">Run the bash or PowerShell command below to deploy the peering connection to the second spoke.</span></span> <span data-ttu-id="9003b-258">Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.</span><span class="sxs-lookup"><span data-stu-id="9003b-258">Substitute the values with your subscription, resource group name, and Azure region.</span></span>

  ```bash
  sh ./hub.peering.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./hub.peering.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus `
    -Spoke 2
  ```

### <a name="test-connectivity"></a><span data-ttu-id="9003b-259">Testare la connettività</span><span class="sxs-lookup"><span data-stu-id="9003b-259">Test connectivity</span></span>

<span data-ttu-id="9003b-260">Per verificare che la distribuzione di una topologia hub-spoke connessa a un data center locale abbia avuto esito positivo, eseguire questi passaggi.</span><span class="sxs-lookup"><span data-stu-id="9003b-260">To verify that the hub-spoke topology connected to an on-premises datacenter deployment worked, follow these steps.</span></span>

1. <span data-ttu-id="9003b-261">Dal [portale di Azure][portale] connettersi alla propria sottoscrizione e passare alla macchina virtuale `ra-onprem-vm1` nel gruppo di risorse `ra-onprem-rg`.</span><span class="sxs-lookup"><span data-stu-id="9003b-261">From the [Azure portal][portal], connect to your subscription, and navigate to the `ra-onprem-vm1` virtual machine in the `ra-onprem-rg` resource group.</span></span>

2. <span data-ttu-id="9003b-262">Nel pannello `Overview` prendere nota del valore `Public IP address` per la macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="9003b-262">In the `Overview` blade, note the `Public IP address` for the VM.</span></span>

3. <span data-ttu-id="9003b-263">Usare un client SSH per eseguire la connessione all'indirizzo IP annotato in precedenza, inserendo il nome utente e la password specificati durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9003b-263">Use an SSH client to connect to the IP address you noted above using the user name and password you specified during deployment.</span></span>

4. <span data-ttu-id="9003b-264">Dal prompt dei comandi nella macchina virtuale a cui ci si è connessi, eseguire il comando seguente per testare la connettività tra la rete virtuale locale e la rete virtuale Spoke1.</span><span class="sxs-lookup"><span data-stu-id="9003b-264">From the command prompt on the VM you connected to, run the command below to test connectivity from the on-premises VNet to the Spoke1 VNet.</span></span>

  ```bash
  ping 10.1.1.37
  ```

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azure-powershell]: /powershell/azure/install-azure-ps?view=azuresmps-3.7.0
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

[visio-download]: https://archcenter.azureedge.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/hub-spoke.png "Topologia hub-spoke in Azure"
[1]: ./images/hub-spoke-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo usando un'appliance virtuale di rete"
[3]: ./images/hub-spokehub-spoke.svg "Topologia hub-spoke-hub-spoke in Azure"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
