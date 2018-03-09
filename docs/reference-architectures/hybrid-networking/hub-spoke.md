---
title: Implementazione una topologia di rete hub-spoke in Azure
description: Come implementare una topologia di rete hub-spoke in Azure.
author: telmosampaio
ms.date: 02/23/2018
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: 1a2855f0d4a903fc4d7a022aef20ea73fe763e2c
ms.sourcegitcommit: 2123c25b1a0b5501ff1887f98030787191cf6994
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a><span data-ttu-id="bb582-103">Implementare una topologia di rete hub-spoke in Azure</span><span class="sxs-lookup"><span data-stu-id="bb582-103">Implement a hub-spoke network topology in Azure</span></span>

<span data-ttu-id="bb582-104">Questa architettura di riferimento illustra come implementare una topologia hub-spoke in Azure.</span><span class="sxs-lookup"><span data-stu-id="bb582-104">This reference architecture shows how to implement a hub-spoke topology in Azure.</span></span> <span data-ttu-id="bb582-105">L'*hub* è una rete virtuale in Azure che funge da punto di connettività centrale alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="bb582-105">The *hub* is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="bb582-106">Gli *spoke* sono le reti virtuali che eseguono il peering con l'hub e possono essere usate per isolare i carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="bb582-106">The *spokes* are VNets that peer with the hub, and can be used to isolate workloads.</span></span> <span data-ttu-id="bb582-107">Flussi di traffico tra il data center locale e l'hub attraverso una connessione di gateway VPN o ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="bb582-107">Traffic flows between the on-premises datacenter and the hub through an ExpressRoute or VPN gateway connection.</span></span>  <span data-ttu-id="bb582-108">[**Distribuire questa soluzione**](#deploy-the-solution).</span><span class="sxs-lookup"><span data-stu-id="bb582-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="bb582-109">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="bb582-109">![[0]][0]</span></span>

<span data-ttu-id="bb582-110">*Scaricare un [file di Visio][visio-download] di questa architettura*</span><span class="sxs-lookup"><span data-stu-id="bb582-110">*Download a [Visio file][visio-download] of this architecture*</span></span>


<span data-ttu-id="bb582-111">I vantaggi di questa topologia includono:</span><span class="sxs-lookup"><span data-stu-id="bb582-111">The benefits of this toplogy include:</span></span>

* <span data-ttu-id="bb582-112">**Risparmio sui costi**, centralizzando in un'unica posizione i servizi che possono essere condivisi da più carichi di lavoro, ad esempio appliance virtuali di rete e server DNS.</span><span class="sxs-lookup"><span data-stu-id="bb582-112">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
* <span data-ttu-id="bb582-113">**Superamento dei limiti delle sottoscrizioni** eseguendo il peering delle reti virtuali da sottoscrizioni diverse all'hub centrale.</span><span class="sxs-lookup"><span data-stu-id="bb582-113">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
* <span data-ttu-id="bb582-114">**Separazione dei compiti** tra IT centrale (SecOPs, InfraOps) e carichi di lavoro (DevOps).</span><span class="sxs-lookup"><span data-stu-id="bb582-114">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="bb582-115">Tra gli usi tipici di questa architettura vi sono:</span><span class="sxs-lookup"><span data-stu-id="bb582-115">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="bb582-116">Carichi di lavoro distribuiti in ambienti diversi, ad esempio sviluppo, test e produzione, che richiedono servizi condivisi, ad esempio DNS, IDS, NTP o Active Directory Domain Services.</span><span class="sxs-lookup"><span data-stu-id="bb582-116">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="bb582-117">I servizi condivisi vengono inseriti nella rete virtuale dell'hub, mentre ogni ambiente viene distribuito in uno spoke per mantenere l'isolamento.</span><span class="sxs-lookup"><span data-stu-id="bb582-117">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
* <span data-ttu-id="bb582-118">Carichi di lavoro che non richiedono connettività uno con l'altro, ma richiedono l'accesso ai servizi condivisi.</span><span class="sxs-lookup"><span data-stu-id="bb582-118">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
* <span data-ttu-id="bb582-119">Aziende che richiedono il controllo centrale sugli aspetti di sicurezza, ad esempio un firewall nell'hub come rete perimetrale, e gestione separata per i carichi di lavoro in ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="bb582-119">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="bb582-120">Architecture</span><span class="sxs-lookup"><span data-stu-id="bb582-120">Architecture</span></span>

<span data-ttu-id="bb582-121">L'architettura è costituita dai componenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="bb582-121">The architecture consists of the following components.</span></span>

* <span data-ttu-id="bb582-122">**Rete locale**.</span><span class="sxs-lookup"><span data-stu-id="bb582-122">**On-premises network**.</span></span> <span data-ttu-id="bb582-123">Una rete LAN privata in esecuzione all'interno di un'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="bb582-123">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="bb582-124">**Dispositivo VPN**.</span><span class="sxs-lookup"><span data-stu-id="bb582-124">**VPN device**.</span></span> <span data-ttu-id="bb582-125">Un dispositivo o un servizio che offre connettività esterna alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="bb582-125">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="bb582-126">Il dispositivo VPN può essere un dispositivo hardware o una soluzione software, ad esempio il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012.</span><span class="sxs-lookup"><span data-stu-id="bb582-126">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="bb582-127">Per un elenco delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN da sito a sito][vpn-appliance].</span><span class="sxs-lookup"><span data-stu-id="bb582-127">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="bb582-128">**Gateway di rete virtuale VPN o gateway ExpressRoute**.</span><span class="sxs-lookup"><span data-stu-id="bb582-128">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="bb582-129">Il gateway di rete virtuale consente di connettere la rete virtuale al dispositivo VPN o al circuito ExpressRoute usato per la connettività con la propria rete locale.</span><span class="sxs-lookup"><span data-stu-id="bb582-129">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="bb582-130">Per altre informazioni, vedere [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].</span><span class="sxs-lookup"><span data-stu-id="bb582-130">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="bb582-131">Gli script di distribuzione per questa architettura di riferimento usano un gateway VPN per la connettività e una rete virtuale in Azure per simulare la rete locale.</span><span class="sxs-lookup"><span data-stu-id="bb582-131">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

* <span data-ttu-id="bb582-132">**Rete virtuale dell'hub**.</span><span class="sxs-lookup"><span data-stu-id="bb582-132">**Hub VNet**.</span></span> <span data-ttu-id="bb582-133">Rete virtuale di Azure usata come hub nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="bb582-133">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="bb582-134">L'hub è il punto di connettività centrale alla rete locale e offre una posizione in cui ospitare servizi che possono essere utilizzati da carichi di lavoro diversi ospitati nelle reti virtuali spoke.</span><span class="sxs-lookup"><span data-stu-id="bb582-134">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

* <span data-ttu-id="bb582-135">**Subnet del gateway**.</span><span class="sxs-lookup"><span data-stu-id="bb582-135">**Gateway subnet**.</span></span> <span data-ttu-id="bb582-136">i gateway di rete virtuale vengono mantenuti nella stessa subnet.</span><span class="sxs-lookup"><span data-stu-id="bb582-136">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="bb582-137">**Reti virtuali spoke**.</span><span class="sxs-lookup"><span data-stu-id="bb582-137">**Spoke VNets**.</span></span> <span data-ttu-id="bb582-138">Una o più reti virtuali di Azure usate come spoke nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="bb582-138">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="bb582-139">Gli spoke possono essere usati per isolare i carichi di lavoro nelle reti virtuali corrispondenti, gestite separatamente rispetto agli altri spoke.</span><span class="sxs-lookup"><span data-stu-id="bb582-139">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="bb582-140">Ogni carico di lavoro può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure.</span><span class="sxs-lookup"><span data-stu-id="bb582-140">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="bb582-141">Per ulteriori informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] e [Esecuzione di carichi di lavoro della macchina virtuale Linux][linux-vm-ra].</span><span class="sxs-lookup"><span data-stu-id="bb582-141">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="bb582-142">**Peering reti virtuali**.</span><span class="sxs-lookup"><span data-stu-id="bb582-142">**VNet peering**.</span></span> <span data-ttu-id="bb582-143">È possibile connettere due reti virtuali nella stessa area di Azure tramite una [connessione peering][vnet-peering].</span><span class="sxs-lookup"><span data-stu-id="bb582-143">Two VNets in the same Azure region can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="bb582-144">Le connessioni peering sono connessioni non transitive a bassa latenza tra reti virtuali.</span><span class="sxs-lookup"><span data-stu-id="bb582-144">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="bb582-145">Dopo il peering, le reti virtuali si scambiano traffico tramite il backbone di Azure, senza bisogno di un router.</span><span class="sxs-lookup"><span data-stu-id="bb582-145">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="bb582-146">In una topologia hub-spoke di rete si usa il peering reti virtuali per connettere l'hub a ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="bb582-146">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span>

> [!NOTE]
> <span data-ttu-id="bb582-147">Questo articolo illustra solo le distribuzioni [Resource Manager](/azure/azure-resource-manager/resource-group-overview), ma è anche possibile connettere una rete virtuale classica a una rete virtuale di Resource Manager nella stessa sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="bb582-147">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="bb582-148">In questo modo, gli spoke possono ospitare distribuzioni classiche e trarre comunque vantaggio dai servizi condivisi nell'hub.</span><span class="sxs-lookup"><span data-stu-id="bb582-148">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="bb582-149">Raccomandazioni</span><span class="sxs-lookup"><span data-stu-id="bb582-149">Recommendations</span></span>

<span data-ttu-id="bb582-150">Le raccomandazioni seguenti sono valide per la maggior parte degli scenari.</span><span class="sxs-lookup"><span data-stu-id="bb582-150">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="bb582-151">Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.</span><span class="sxs-lookup"><span data-stu-id="bb582-151">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="bb582-152">Gruppi di risorse</span><span class="sxs-lookup"><span data-stu-id="bb582-152">Resource groups</span></span>

<span data-ttu-id="bb582-153">È possibile implementare la rete virtuale dell'hub e ogni rete virtuale spoke in gruppi di risorse diversi e persino in sottoscrizioni diverse, a condizione che appartengano allo stesso tenant di Azure Active Directory (Azure AD) nella stessa area di Azure.</span><span class="sxs-lookup"><span data-stu-id="bb582-153">The hub VNet, and each spoke VNet, can be implemented in different resource groups, and even different subscriptions, as long as they belong to the same Azure Active Directory (Azure AD) tenant in the same Azure region.</span></span> <span data-ttu-id="bb582-154">Questo consente di decentralizzare la gestione di ogni carico di lavoro e allo stesso tempo condividere i servizi gestiti nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="bb582-154">This allows for a decentralized management of each workload, while sharing services maintained in the hub VNet.</span></span>

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="bb582-155">Reti virtuali e GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="bb582-155">VNet and GatewaySubnet</span></span>

<span data-ttu-id="bb582-156">Creare una subnet denominata *GatewaySubnet*, con un intervallo di indirizzi di /27.</span><span class="sxs-lookup"><span data-stu-id="bb582-156">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="bb582-157">Questa subnet è necessaria per il gateway di rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="bb582-157">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="bb582-158">Allocare 32 indirizzi a questa subnet contribuirà in futuro a evitare di raggiungere i limiti di dimensioni del gateway.</span><span class="sxs-lookup"><span data-stu-id="bb582-158">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span>

<span data-ttu-id="bb582-159">Per altre informazioni sulla configurazione del gateway, vedere le architetture di riferimento seguenti, a seconda del tipo di connessione:</span><span class="sxs-lookup"><span data-stu-id="bb582-159">For more information about setting up the gateway, see the following reference architectures, depending on your connection type:</span></span>

- <span data-ttu-id="bb582-160">[Rete ibrida tramite ExpressRoute][guidance-expressroute]</span><span class="sxs-lookup"><span data-stu-id="bb582-160">[Hybrid network using ExpressRoute][guidance-expressroute]</span></span>
- <span data-ttu-id="bb582-161">[Rete ibrida tramite un gateway VPN][guidance-vpn]</span><span class="sxs-lookup"><span data-stu-id="bb582-161">[Hybrid network using a VPN gateway][guidance-vpn]</span></span>

<span data-ttu-id="bb582-162">Per una disponibilità più elevata, è possibile usare ExpressRoute più una rete VPN per il failover.</span><span class="sxs-lookup"><span data-stu-id="bb582-162">For higher availability, you can use ExpressRoute plus a VPN for failover.</span></span> <span data-ttu-id="bb582-163">Vedere [Connettere una rete locale ad Azure tramite ExpressRoute con failover VPN][hybrid-ha].</span><span class="sxs-lookup"><span data-stu-id="bb582-163">See [Connect an on-premises network to Azure using ExpressRoute with VPN failover][hybrid-ha].</span></span>

<span data-ttu-id="bb582-164">Una topologia hub-spoke può essere usata anche senza gateway, se non è necessaria la connettività con la rete locale.</span><span class="sxs-lookup"><span data-stu-id="bb582-164">A hub-spoke topology can also be used without a gateway, if you don't need connectivity with your on-premises network.</span></span> 

### <a name="vnet-peering"></a><span data-ttu-id="bb582-165">Peering reti virtuali</span><span class="sxs-lookup"><span data-stu-id="bb582-165">VNet peering</span></span>

<span data-ttu-id="bb582-166">Il peering reti virtuali è una relazione non transitiva tra due reti virtuali.</span><span class="sxs-lookup"><span data-stu-id="bb582-166">VNet peering is a non-transitive relationship between two VNets.</span></span> <span data-ttu-id="bb582-167">Se è necessaria la connettività tra gli spoke, considerare l'aggiunta di una connessione peering separata tra di essi.</span><span class="sxs-lookup"><span data-stu-id="bb582-167">If you require spokes to connect to each other, consider adding a separate peering connection between those spokes.</span></span>

<span data-ttu-id="bb582-168">Tuttavia, se si dispone di più spoke che devono connettersi tra loro, le possibili connessioni peering si esauriranno molto rapidamente, a causa del [limite di peering reti virtuali per ogni rete virtuale][vnet-peering-limit].</span><span class="sxs-lookup"><span data-stu-id="bb582-168">However, if you have several spokes that need to connect with each other, you will run out of possible peering connections very quickly due to the [limitation on number of VNets peerings per VNet][vnet-peering-limit].</span></span> <span data-ttu-id="bb582-169">In questo scenario, prendere in considerazione l'uso di route definite dall'utente per forzare l'invio del traffico destinato a uno spoke a un'appliance virtuale di rete che funga da router nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="bb582-169">In this scenario, consider using user defined routes (UDRs) to force traffic destined to a spoke to be sent to an NVA acting as a router at the hub VNet.</span></span> <span data-ttu-id="bb582-170">Questo consentirà agli spoke di connettersi tra loro.</span><span class="sxs-lookup"><span data-stu-id="bb582-170">This will allow the spokes to connect to each other.</span></span>

<span data-ttu-id="bb582-171">È anche possibile configurare gli spoke in modo da usare il gateway della rete virtuale dell'hub per comunicare con reti remote.</span><span class="sxs-lookup"><span data-stu-id="bb582-171">You can also configure spokes to use the hub VNet gateway to communicate with remote networks.</span></span> <span data-ttu-id="bb582-172">Per consentire il flusso del traffico gateway dallo spoke all'hub e connettersi a reti remote, è necessario:</span><span class="sxs-lookup"><span data-stu-id="bb582-172">To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:</span></span>

  - <span data-ttu-id="bb582-173">Configurare la connessione peering reti virtuali nell'hub in modo da **consentire il transito gateway**.</span><span class="sxs-lookup"><span data-stu-id="bb582-173">Configure the VNet peering connection in the hub to **allow gateway transit**.</span></span>
  - <span data-ttu-id="bb582-174">Configurare la connessione peering reti virtuali in ogni spoke in modo da **usare gateway remoti**.</span><span class="sxs-lookup"><span data-stu-id="bb582-174">Configure the VNet peering connection in each spoke to **use remote gateways**.</span></span>
  - <span data-ttu-id="bb582-175">Configurare tutte le connessioni peering reti virtuali in modo da **consentire il traffico inoltrato**.</span><span class="sxs-lookup"><span data-stu-id="bb582-175">Configure all VNet peering connections to **allow forwarded traffic**.</span></span>

## <a name="considerations"></a><span data-ttu-id="bb582-176">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="bb582-176">Considerations</span></span>

### <a name="spoke-connectivity"></a><span data-ttu-id="bb582-177">Connettività spoke</span><span class="sxs-lookup"><span data-stu-id="bb582-177">Spoke connectivity</span></span>

<span data-ttu-id="bb582-178">Se è necessaria la connettività tra spoke, è consigliabile implementare un'appliance virtuale di rete per il routing nell'hub e usare route definite dall'utente nello spoke per inoltrare il traffico all'hub.</span><span class="sxs-lookup"><span data-stu-id="bb582-178">If you require connectivity between spokes, consider implementing an NVA for routing in the hub, and using UDRs in the spoke to forward traffic to the hub.</span></span>

<span data-ttu-id="bb582-179">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="bb582-179">![[2]][2]</span></span>

<span data-ttu-id="bb582-180">In questo scenario occorre configurare le connessioni peering in modo da **consentire il traffico inoltrato**.</span><span class="sxs-lookup"><span data-stu-id="bb582-180">In this scenario, you must configure the peering connections to **allow forwarded traffic**.</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="bb582-181">Superamento dei limiti del peering reti virtuali</span><span class="sxs-lookup"><span data-stu-id="bb582-181">Overcoming VNet peering limits</span></span>

<span data-ttu-id="bb582-182">Assicurarsi di considerare il [limite sul numero di peering reti virtuali per ogni rete virtuale][vnet-peering-limit] in Azure.</span><span class="sxs-lookup"><span data-stu-id="bb582-182">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="bb582-183">Se si stabilisce che saranno necessari più spoke di quanto consentito dal limite, valutare la creazione di una topologia hub-spoke-hub-spoke, in cui il primo livello di spoke funge anche da hub.</span><span class="sxs-lookup"><span data-stu-id="bb582-183">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="bb582-184">Il diagramma seguente illustra questo approccio.</span><span class="sxs-lookup"><span data-stu-id="bb582-184">The following diagram shows this approach.</span></span>

<span data-ttu-id="bb582-185">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="bb582-185">![[3]][3]</span></span>

<span data-ttu-id="bb582-186">Considerare inoltre i servizi condivisi nell'hub, per assicurarsi che quest'ultimo sia scalabile per un numero superiore di spoke.</span><span class="sxs-lookup"><span data-stu-id="bb582-186">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="bb582-187">Ad esempio, se l'hub fornisce servizi firewall, valutare i limiti di larghezza di banda della soluzione firewall quando si aggiungono più spoke.</span><span class="sxs-lookup"><span data-stu-id="bb582-187">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="bb582-188">Può essere utile spostare alcuni dei servizi condivisi in un secondo livello di hub.</span><span class="sxs-lookup"><span data-stu-id="bb582-188">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="bb582-189">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="bb582-189">Deploy the solution</span></span>

<span data-ttu-id="bb582-190">Una distribuzione di questa architettura è disponibile in [GitHub][ref-arch-repo].</span><span class="sxs-lookup"><span data-stu-id="bb582-190">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="bb582-191">Usa VM Ubuntu in ogni rete virtuale per testare la connettività.</span><span class="sxs-lookup"><span data-stu-id="bb582-191">It uses Ubuntu VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="bb582-192">Nella subnet **shared-services** della **rete virtuale dell'hub** non sono effettivamente ospitati servizi.</span><span class="sxs-lookup"><span data-stu-id="bb582-192">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="bb582-193">prerequisiti</span><span class="sxs-lookup"><span data-stu-id="bb582-193">Prerequisites</span></span>

<span data-ttu-id="bb582-194">Prima di poter distribuire l'architettura di riferimento nella propria sottoscrizione, è necessario eseguire i passaggi seguenti.</span><span class="sxs-lookup"><span data-stu-id="bb582-194">Before you can deploy the reference architecture to your own subscription, you must perform the following steps.</span></span>

1. <span data-ttu-id="bb582-195">Clonare, creare una copia tramite fork o scaricare il file ZIP per il repository GitHub delle [architetture di riferimento AzureCAT][ref-arch-repo].</span><span class="sxs-lookup"><span data-stu-id="bb582-195">Clone, fork, or download the zip file for the [AzureCAT reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="bb582-196">Verificare che nel computer sia installata l'interfaccia della riga di comando di Azure 2.0.</span><span class="sxs-lookup"><span data-stu-id="bb582-196">Make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="bb582-197">Per istruzioni sull'installazione dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-2].</span><span class="sxs-lookup"><span data-stu-id="bb582-197">For CLI installation instructions, see [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="bb582-198">Installare il pacchetto npm dei [blocchi predefiniti di Azure][azbb].</span><span class="sxs-lookup"><span data-stu-id="bb582-198">Install the [Azure buulding blocks][azbb] npm package.</span></span>

4. <span data-ttu-id="bb582-199">Al prompt dei comandi, di Bash o di PowerShell accedere all'account Azure con questo comando e seguire le istruzioni.</span><span class="sxs-lookup"><span data-stu-id="bb582-199">From a command prompt, bash prompt, or PowerShell prompt, login to your Azure account by using the command below, and follow the prompts.</span></span>

  ```bash
  az login
  ```

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a><span data-ttu-id="bb582-200">Distribuire il data center locale simulato con azbb</span><span class="sxs-lookup"><span data-stu-id="bb582-200">Deploy the simulated on-premises datacenter using azbb</span></span>

<span data-ttu-id="bb582-201">Per distribuire il data center locale simulato come rete virtuale di Azure, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="bb582-201">To deploy the simulated on-premises datacenter as an Azure VNet, follow these steps:</span></span>

1. <span data-ttu-id="bb582-202">Passare alla cartella `hybrid-networking\hub-spoke\` per il repository scaricato nel passaggio dei prerequisiti precedente.</span><span class="sxs-lookup"><span data-stu-id="bb582-202">Navigate to the `hybrid-networking\hub-spoke\` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="bb582-203">Aprire il file `onprem.json` e immettere un nome utente e una password tra le virgolette nelle righe 36 e 37, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="bb582-203">Open the `onprem.json` file and enter a username and password between the quotes in line 36 and 37, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. <span data-ttu-id="bb582-204">Nella riga 38 digitare `Windows` o `Linux` per `osType` per installare Windows Server 2016 Datacenter o Ubuntu 16.04 come sistema operativo per il jumpbox.</span><span class="sxs-lookup"><span data-stu-id="bb582-204">On line 38, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

4. <span data-ttu-id="bb582-205">Eseguire `azbb` per distribuire l'ambiente locale simulato, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-205">Run `azbb` to deploy the simulated onprem environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g onprem-vnet-rg - l <location> -p onoprem.json --deploy
  ```
  > [!NOTE]
  > <span data-ttu-id="bb582-206">Se si decide di usare un nome del gruppo di risorse diverso da `onprem-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="bb582-206">If you decide to use a different resource group name (other than `onprem-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="bb582-207">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="bb582-207">Wait for the deployment to finish.</span></span> <span data-ttu-id="bb582-208">Questa distribuzione crea una rete virtuale, una macchina virtuale e un gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="bb582-208">This deployment creates a virtual network, a virtual machine, and a VPN gateway.</span></span> <span data-ttu-id="bb582-209">La creazione del gateway VPN può richiedere più di 40 minuti.</span><span class="sxs-lookup"><span data-stu-id="bb582-209">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="azure-hub-vnet"></a><span data-ttu-id="bb582-210">Rete virtuale dell'hub di Azure</span><span class="sxs-lookup"><span data-stu-id="bb582-210">Azure hub VNet</span></span>

<span data-ttu-id="bb582-211">Per distribuire la rete virtuale dell'hub e connettersi alla rete virtuale locale simulata creata in precedenza, eseguire la procedura seguente.</span><span class="sxs-lookup"><span data-stu-id="bb582-211">To deploy the hub VNet, and connect to the simulated on-premises VNet created above, perform the following steps.</span></span>

1. <span data-ttu-id="bb582-212">Aprire il file `hub-vnet.json` e immettere un nome utente e una password tra le virgolette nelle righe 39 e 40, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-212">Open the `hub-vnet.json` file and enter a username and password between the quotes in line 39 and 40, as shown below.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

2. <span data-ttu-id="bb582-213">Nella riga 41 digitare `Windows` o `Linux` per `osType` per installare Windows Server 2016 Datacenter o Ubuntu 16.04 come sistema operativo per il jumpbox.</span><span class="sxs-lookup"><span data-stu-id="bb582-213">On line 41, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

3. <span data-ttu-id="bb582-214">Immettere una chiave condivisa tra le virgolette nella riga 72, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="bb582-214">Enter a shared key between the quotes in line 72, as shown below, then save the file.</span></span>

  ```bash
  "sharedKey": "",
  ```

4. <span data-ttu-id="bb582-215">Eseguire `azbb` per distribuire l'ambiente locale simulato, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-215">Run `azbb` to deploy the simulated onprem environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet.json --deploy
  ```
  > [!NOTE]
  > <span data-ttu-id="bb582-216">Se si decide di usare un nome del gruppo di risorse diverso da `hub-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="bb582-216">If you decide to use a different resource group name (other than `hub-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="bb582-217">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="bb582-217">Wait for the deployment to finish.</span></span> <span data-ttu-id="bb582-218">Questa distribuzione crea una rete virtuale, una macchina virtuale, un gateway VPN e una connessione al gateway creato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="bb582-218">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway created in the previous section.</span></span> <span data-ttu-id="bb582-219">La creazione del gateway VPN può richiedere più di 40 minuti.</span><span class="sxs-lookup"><span data-stu-id="bb582-219">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="optional-test-connectivity-from-onprem-to-hub"></a><span data-ttu-id="bb582-220">(Facoltativo) Testare la connettività dall'ambiente locale all'hub</span><span class="sxs-lookup"><span data-stu-id="bb582-220">(Optional) Test connectivity from onprem to hub</span></span>

<span data-ttu-id="bb582-221">Per testare la connettività dall'ambiente locale simulato alla rete virtuale hub con VM Windows, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="bb582-221">To test conectivity from the simulated on-premises environment to the hub VNet using Windows VMs, perform the following steps.</span></span>

1. <span data-ttu-id="bb582-222">Dal portale di Azure passare al gruppo di risorse `onprem-jb-rg` e quindi fare clic sulla risorsa macchina virtuale `jb-vm1`.</span><span class="sxs-lookup"><span data-stu-id="bb582-222">From the Azure portal, navigate to the `onprem-jb-rg` resource group, then click on the `jb-vm1` virtual machine resource.</span></span>

2.  <span data-ttu-id="bb582-223">Nell'angolo superiore sinistro del pannello della VM nel portale fare clic su `Connect` e seguire le istruzioni per connettersi alla VM con Desktop remoto.</span><span class="sxs-lookup"><span data-stu-id="bb582-223">On the top left hand corner of your VM blade in the portal, click `Connect`, and follow the prompts to use remote desktop to connect to the VM.</span></span> <span data-ttu-id="bb582-224">Assicurarsi di usare il nome utente e la password specificati nelle righe 36 e 37 del file `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="bb582-224">Make sure to use the username and password you specified in lines 36 and 37 in the `onprem.json` file.</span></span>

3. <span data-ttu-id="bb582-225">Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi alla VM jumpbox dell'hub, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-225">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the hub jumpbox VM as shown below.</span></span>

  ```powershell
  Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
  ```
  > [!NOTE]
  > <span data-ttu-id="bb582-226">Per impostazione predefinita, le VM Windows Server non consentono risposte ICMP in Azure.</span><span class="sxs-lookup"><span data-stu-id="bb582-226">By default, Windows Server VMs do not allow ICMP responses in Azure.</span></span> <span data-ttu-id="bb582-227">Se si vuole usare `ping` per testare la connettività, è necessario abilitare il traffico ICMP in Windows Firewall con sicurezza avanzata per ogni VM.</span><span class="sxs-lookup"><span data-stu-id="bb582-227">If you want to use `ping` to test connectivity, you need to enable ICMP traffic in the Windows Advanced Firewall for each VM.</span></span>

<span data-ttu-id="bb582-228">Per testare la connettività dall'ambiente locale simulato alla rete virtuale hub con VM Linux, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="bb582-228">To test conectivity from the simulated on-premises environment to the hub VNet using Linux VMs, perform the following steps:</span></span>

1. <span data-ttu-id="bb582-229">Dal portale di Azure passare al gruppo di risorse `onprem-jb-rg` e quindi fare clic sulla risorsa macchina virtuale `jb-vm1`.</span><span class="sxs-lookup"><span data-stu-id="bb582-229">From the Azure portal, navigate to the `onprem-jb-rg` resource group, then click on the `jb-vm1` virtual machine resource.</span></span>

2. <span data-ttu-id="bb582-230">Nell'angolo superiore sinistro del pannello della VM nel portale fare clic su `Connect` e quindi copiare il comando `ssh` visualizzato nel portale.</span><span class="sxs-lookup"><span data-stu-id="bb582-230">On the top left hand corner of your VM blade in the portal, click `Connect`, and then copy the `ssh` command shown on the portal.</span></span> 

3. <span data-ttu-id="bb582-231">Al prompt Linux eseguire `ssh` per connettersi al jumpbox dell'ambiente locale simulato con le informazioni copiate nel passaggio 2 precedente, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-231">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment jumpbox witht the information you copied in step 2 above, as shown below.</span></span>

  ```bash
  ssh <your_user>@<public_ip_address>
  ```

4. <span data-ttu-id="bb582-232">Usare la password specificata nella riga 37 del file `onprem.json` per connettersi alla VM.</span><span class="sxs-lookup"><span data-stu-id="bb582-232">Use the password you specified in line 37 in the `onprem.json` file to the connect to the VM.</span></span>

5. <span data-ttu-id="bb582-233">Usare il comando `ping` per testare la connettività al jumpbox dell'hub, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-233">Use the `ping` command to test connectivity to the hub jumpbox, as shown below.</span></span>

  ```bash
  ping 10.0.0.68
  ```

### <a name="azure-spoke-vnets"></a><span data-ttu-id="bb582-234">Reti virtuali spoke di Azure</span><span class="sxs-lookup"><span data-stu-id="bb582-234">Azure spoke VNets</span></span>

<span data-ttu-id="bb582-235">Per distribuire le reti virtuali spoke, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="bb582-235">To deploy the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="bb582-236">Aprire il file `spoke1.json` e immettere un nome utente e una password tra le virgolette nelle righe 47 e 48, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="bb582-236">Open the `spoke1.json` file and enter a username and password between the quotes in lines 47 and 48, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

2. <span data-ttu-id="bb582-237">Nella riga 49 digitare `Windows` o `Linux` per `osType` per installare Windows Server 2016 Datacenter o Ubuntu 16.04 come sistema operativo per il jumpbox.</span><span class="sxs-lookup"><span data-stu-id="bb582-237">On line 49, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

3. <span data-ttu-id="bb582-238">Eseguire `azbb` per distribuire l'ambiente della prima rete virtuale spoke, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-238">Run `azbb` to deploy the first spoke VNet environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g spoke1-vnet-rg - l <location> -p spoke1.json --deploy
  ```
  
  > [!NOTE]
  > <span data-ttu-id="bb582-239">Se si decide di usare un nome del gruppo di risorse diverso da `spoke1-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="bb582-239">If you decide to use a different resource group name (other than `spoke1-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

3. <span data-ttu-id="bb582-240">Ripetere il passaggio 1 precedente per il file `spoke2.json`.</span><span class="sxs-lookup"><span data-stu-id="bb582-240">Repeat step 1 above for file `spoke2.json`.</span></span>

4. <span data-ttu-id="bb582-241">Eseguire `azbb` per distribuire l'ambiente della seconda rete virtuale spoke, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-241">Run `azbb` to deploy the second spoke VNet environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g spoke2-vnet-rg - l <location> -p spoke2.json --deploy
  ```
  > [!NOTE]
  > <span data-ttu-id="bb582-242">Se si decide di usare un nome del gruppo di risorse diverso da `spoke2-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="bb582-242">If you decide to use a different resource group name (other than `spoke2-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a><span data-ttu-id="bb582-243">Peering reti virtuali dell'hub di Azure alle reti virtuali spoke</span><span class="sxs-lookup"><span data-stu-id="bb582-243">Azure hub VNet peering to spoke VNets</span></span>

<span data-ttu-id="bb582-244">Per creare una connessione peering dalla rete virtuale hub alle reti virtuali spoke, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="bb582-244">To create a peering connection from the hub VNet to the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="bb582-245">Aprire il file `hub-vnet-peering.json` e verificare che il nome del gruppo di risorse e il nome della rete virtuale per ogni peering reti virtuali, a partire dalla riga 29, siano corretti.</span><span class="sxs-lookup"><span data-stu-id="bb582-245">Open the `hub-vnet-peering.json` file and verify that the resource group name, and virtual network name for each of the virtual network peerings starting in line 29 are correct.</span></span>

2. <span data-ttu-id="bb582-246">Eseguire `azbb` per distribuire l'ambiente della prima rete virtuale spoke, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-246">Run `azbb` to deploy the first spoke VNet environment as shown below.</span></span>

  ```bash
  azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet-peering.json --deploy
  ```

  > [!NOTE]
  > <span data-ttu-id="bb582-247">Se si decide di usare un nome del gruppo di risorse diverso da `hub-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="bb582-247">If you decide to use a different resource group name (other than `hub-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

### <a name="test-connectivity"></a><span data-ttu-id="bb582-248">Testare la connettività</span><span class="sxs-lookup"><span data-stu-id="bb582-248">Test connectivity</span></span>

<span data-ttu-id="bb582-249">Per testare la connettività dall'ambiente locale simulato alle reti virtuali spoke con VM Windows, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="bb582-249">To test conectivity from the simulated on-premises environment to the spoke VNets using Windows VMs, perform the following steps.</span></span>

1. <span data-ttu-id="bb582-250">Dal portale di Azure passare al gruppo di risorse `onprem-jb-rg` e quindi fare clic sulla risorsa macchina virtuale `jb-vm1`.</span><span class="sxs-lookup"><span data-stu-id="bb582-250">From the Azure portal, navigate to the `onprem-jb-rg` resource group, then click on the `jb-vm1` virtual machine resource.</span></span>

2.  <span data-ttu-id="bb582-251">Nell'angolo superiore sinistro del pannello della VM nel portale fare clic su `Connect` e seguire le istruzioni per connettersi alla VM con Desktop remoto.</span><span class="sxs-lookup"><span data-stu-id="bb582-251">On the top left hand corner of your VM blade in the portal, click `Connect`, and follow the prompts to use remote desktop to connect to the VM.</span></span> <span data-ttu-id="bb582-252">Assicurarsi di usare il nome utente e la password specificati nelle righe 36 e 37 del file `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="bb582-252">Make sure to use the username and password you specified in lines 36 and 37 in the `onprem.json` file.</span></span>

3. <span data-ttu-id="bb582-253">Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi alla VM jumpbox dell'hub, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-253">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the hub jumpbox VM as shown below.</span></span>

  ```powershell
  Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
  Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
  ```

<span data-ttu-id="bb582-254">Per testare la connettività dall'ambiente locale simulato alle reti virtuali spoke con VM Linux, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="bb582-254">To test conectivity from the simulated on-premises environment to the spoke VNets using Linux VMs, perform the following steps:</span></span>

1. <span data-ttu-id="bb582-255">Dal portale di Azure passare al gruppo di risorse `onprem-jb-rg` e quindi fare clic sulla risorsa macchina virtuale `jb-vm1`.</span><span class="sxs-lookup"><span data-stu-id="bb582-255">From the Azure portal, navigate to the `onprem-jb-rg` resource group, then click on the `jb-vm1` virtual machine resource.</span></span>

2. <span data-ttu-id="bb582-256">Nell'angolo superiore sinistro del pannello della VM nel portale fare clic su `Connect` e quindi copiare il comando `ssh` visualizzato nel portale.</span><span class="sxs-lookup"><span data-stu-id="bb582-256">On the top left hand corner of your VM blade in the portal, click `Connect`, and then copy the `ssh` command shown on the portal.</span></span> 

3. <span data-ttu-id="bb582-257">Al prompt Linux eseguire `ssh` per connettersi al jumpbox dell'ambiente locale simulato con le informazioni copiate nel passaggio 2 precedente, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-257">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment jumpbox witht the information you copied in step 2 above, as shown below.</span></span>

  ```bash
  ssh <your_user>@<public_ip_address>
  ```

5. <span data-ttu-id="bb582-258">Usare la password specificata nella riga 37 del file `onprem.json` per connettersi alla VM.</span><span class="sxs-lookup"><span data-stu-id="bb582-258">Use the password you specified in line 37 in the `onprem.json` file to the connect to the VM.</span></span>

6. <span data-ttu-id="bb582-259">Usare il comando `ping` per testare la connettività alle VM jumpbox in ogni spoke, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bb582-259">Use the `ping` command to test connectivity to the jumpbox VMs in each spoke, as shown below.</span></span>

  ```bash
  ping 10.1.0.68
  ping 10.2.0.68
  ```

### <a name="add-connectivity-between-spokes"></a><span data-ttu-id="bb582-260">Aggiungere la connettività tra spoke</span><span class="sxs-lookup"><span data-stu-id="bb582-260">Add connectivity between spokes</span></span>

<span data-ttu-id="bb582-261">Se si vuole consentire la connessione tra gli spoke, è necessario usare un'appliance virtuale di rete come router nella rete virtuale hub e forzare il traffico dagli spoke verso il router in caso di tentativo di connessione a un altro spoke.</span><span class="sxs-lookup"><span data-stu-id="bb582-261">If you want to allow spokes to connect to each other, you need to use a newtwork virtual appliance (NVA) as a router in the hub virtual netowrk, and force traffic from spokes to the router when trying to connect to another spoke.</span></span> <span data-ttu-id="bb582-262">Per distribuire un'appliance virtuale di rete di esempio di base come una singola VM e le route definite dall'utente necessarie per consentire la connessione di due reti virtuali spoke, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="bb582-262">To deploy a basic sample NVA as a single VM, and the necessary uder defined routes to allow the two spoke VNets to connect, perform the following steps:</span></span>

1. <span data-ttu-id="bb582-263">Aprire il file `hub-nva.json` e immettere un nome utente e una password tra le virgolette nelle righe 13 e 14, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="bb582-263">Open the `hub-nva.json` file and enter a username and password between the quotes in lines 13 and 14, as shown below, then save the file.</span></span>

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```
2. <span data-ttu-id="bb582-264">Eseguire `azbb` per distribuire la VM dell'appliance virtuale di rete e le route definite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="bb582-264">Run `azbb` to deploy the NVA VM and user defined routes.</span></span>

  ```bash
  azbb -s <subscription_id> -g hub-nva-rg - l <location> -p hub-nva.json --deploy
  ```
  > [!NOTE]
  > <span data-ttu-id="bb582-265">Se si decide di usare un nome del gruppo di risorse diverso da `hub-nva-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="bb582-265">If you decide to use a different resource group name (other than `hub-nva-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

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

[visio-download]: https://archcenter.azureedge.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/hub-spoke.png "Topologia hub-spoke in Azure"
[1]: ./images/hub-spoke-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo usando un'appliance virtuale di rete"
[3]: ./images/hub-spokehub-spoke.svg "Topologia hub-spoke-hub-spoke in Azure"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
