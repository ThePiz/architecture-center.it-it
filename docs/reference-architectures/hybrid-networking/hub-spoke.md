---
title: Implementare una topologia di rete hub-spoke in Azure
titleSuffix: Azure Reference Architectures
description: Implementare una topologia di rete hub-spoke in Azure.
author: telmosampaio
ms.date: 10/08/2018
ms.custom: seodec18
ms.openlocfilehash: fe56630b621f02fe71b864642b75688ba1965862
ms.sourcegitcommit: 8d951fd7e9534054b160be48a1881ae0857561ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53329433"
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a><span data-ttu-id="d6592-103">Implementare una topologia di rete hub-spoke in Azure</span><span class="sxs-lookup"><span data-stu-id="d6592-103">Implement a hub-spoke network topology in Azure</span></span>

<span data-ttu-id="d6592-104">Questa architettura di riferimento illustra come implementare una topologia hub-spoke in Azure.</span><span class="sxs-lookup"><span data-stu-id="d6592-104">This reference architecture shows how to implement a hub-spoke topology in Azure.</span></span> <span data-ttu-id="d6592-105">L'*hub* è una rete virtuale in Azure che funge da punto di connettività centrale alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="d6592-105">The *hub* is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="d6592-106">Gli *spoke* sono le reti virtuali che eseguono il peering con l'hub e possono essere usate per isolare i carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="d6592-106">The *spokes* are VNets that peer with the hub, and can be used to isolate workloads.</span></span> <span data-ttu-id="d6592-107">Flussi di traffico tra il data center locale e l'hub attraverso una connessione di gateway VPN o ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="d6592-107">Traffic flows between the on-premises datacenter and the hub through an ExpressRoute or VPN gateway connection.</span></span> <span data-ttu-id="d6592-108">[**Distribuire questa soluzione**](#deploy-the-solution).</span><span class="sxs-lookup"><span data-stu-id="d6592-108">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="d6592-109">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="d6592-109">![[0]][0]</span></span>

<span data-ttu-id="d6592-110">*Scaricare un [file di Visio][visio-download] di questa architettura*</span><span class="sxs-lookup"><span data-stu-id="d6592-110">*Download a [Visio file][visio-download] of this architecture*</span></span>

<span data-ttu-id="d6592-111">I vantaggi di questa topologia includono:</span><span class="sxs-lookup"><span data-stu-id="d6592-111">The benefits of this toplogy include:</span></span>

- <span data-ttu-id="d6592-112">**Risparmio sui costi**, centralizzando in un'unica posizione i servizi che possono essere condivisi da più carichi di lavoro, ad esempio appliance virtuali di rete e server DNS.</span><span class="sxs-lookup"><span data-stu-id="d6592-112">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
- <span data-ttu-id="d6592-113">**Superamento dei limiti delle sottoscrizioni** eseguendo il peering delle reti virtuali da sottoscrizioni diverse all'hub centrale.</span><span class="sxs-lookup"><span data-stu-id="d6592-113">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
- <span data-ttu-id="d6592-114">**Separazione dei compiti** tra IT centrale (SecOPs, InfraOps) e carichi di lavoro (DevOps).</span><span class="sxs-lookup"><span data-stu-id="d6592-114">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="d6592-115">Tra gli usi tipici di questa architettura vi sono:</span><span class="sxs-lookup"><span data-stu-id="d6592-115">Typical uses for this architecture include:</span></span>

- <span data-ttu-id="d6592-116">Carichi di lavoro distribuiti in ambienti diversi, ad esempio sviluppo, test e produzione, che richiedono servizi condivisi, ad esempio DNS, IDS, NTP o Active Directory Domain Services.</span><span class="sxs-lookup"><span data-stu-id="d6592-116">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="d6592-117">I servizi condivisi vengono inseriti nella rete virtuale dell'hub, mentre ogni ambiente viene distribuito in uno spoke per mantenere l'isolamento.</span><span class="sxs-lookup"><span data-stu-id="d6592-117">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
- <span data-ttu-id="d6592-118">Carichi di lavoro che non richiedono connettività uno con l'altro, ma richiedono l'accesso ai servizi condivisi.</span><span class="sxs-lookup"><span data-stu-id="d6592-118">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
- <span data-ttu-id="d6592-119">Aziende che richiedono il controllo centrale sugli aspetti di sicurezza, ad esempio un firewall nell'hub come rete perimetrale, e gestione separata per i carichi di lavoro in ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-119">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="d6592-120">Architettura</span><span class="sxs-lookup"><span data-stu-id="d6592-120">Architecture</span></span>

<span data-ttu-id="d6592-121">L'architettura è costituita dai componenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="d6592-121">The architecture consists of the following components.</span></span>

- <span data-ttu-id="d6592-122">**Rete locale**.</span><span class="sxs-lookup"><span data-stu-id="d6592-122">**On-premises network**.</span></span> <span data-ttu-id="d6592-123">Una rete LAN privata in esecuzione all'interno di un'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="d6592-123">A private local-area network running within an organization.</span></span>

- <span data-ttu-id="d6592-124">**Dispositivo VPN**.</span><span class="sxs-lookup"><span data-stu-id="d6592-124">**VPN device**.</span></span> <span data-ttu-id="d6592-125">Un dispositivo o un servizio che offre connettività esterna alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="d6592-125">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="d6592-126">Il dispositivo VPN può essere un dispositivo hardware o una soluzione software, ad esempio il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012.</span><span class="sxs-lookup"><span data-stu-id="d6592-126">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="d6592-127">Per un elenco delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN da sito a sito][vpn-appliance].</span><span class="sxs-lookup"><span data-stu-id="d6592-127">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

- <span data-ttu-id="d6592-128">**Gateway di rete virtuale VPN o gateway ExpressRoute**.</span><span class="sxs-lookup"><span data-stu-id="d6592-128">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="d6592-129">Il gateway di rete virtuale consente di connettere la rete virtuale al dispositivo VPN o al circuito ExpressRoute usato per la connettività con la propria rete locale.</span><span class="sxs-lookup"><span data-stu-id="d6592-129">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="d6592-130">Per altre informazioni, vedere [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].</span><span class="sxs-lookup"><span data-stu-id="d6592-130">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="d6592-131">Gli script di distribuzione per questa architettura di riferimento usano un gateway VPN per la connettività e una rete virtuale in Azure per simulare la rete locale.</span><span class="sxs-lookup"><span data-stu-id="d6592-131">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

- <span data-ttu-id="d6592-132">**Rete virtuale dell'hub**.</span><span class="sxs-lookup"><span data-stu-id="d6592-132">**Hub VNet**.</span></span> <span data-ttu-id="d6592-133">Rete virtuale di Azure usata come hub nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-133">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="d6592-134">L'hub è il punto di connettività centrale alla rete locale e offre una posizione in cui ospitare servizi che possono essere utilizzati da carichi di lavoro diversi ospitati nelle reti virtuali spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-134">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

- <span data-ttu-id="d6592-135">**Subnet del gateway**.</span><span class="sxs-lookup"><span data-stu-id="d6592-135">**Gateway subnet**.</span></span> <span data-ttu-id="d6592-136">i gateway di rete virtuale vengono mantenuti nella stessa subnet.</span><span class="sxs-lookup"><span data-stu-id="d6592-136">The virtual network gateways are held in the same subnet.</span></span>

- <span data-ttu-id="d6592-137">**Reti virtuali spoke**.</span><span class="sxs-lookup"><span data-stu-id="d6592-137">**Spoke VNets**.</span></span> <span data-ttu-id="d6592-138">Una o più reti virtuali di Azure usate come spoke nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-138">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="d6592-139">Gli spoke possono essere usati per isolare i carichi di lavoro nelle reti virtuali corrispondenti, gestite separatamente rispetto agli altri spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-139">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="d6592-140">Ogni carico di lavoro può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure.</span><span class="sxs-lookup"><span data-stu-id="d6592-140">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="d6592-141">Per ulteriori informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] e [Esecuzione di carichi di lavoro della macchina virtuale Linux][linux-vm-ra].</span><span class="sxs-lookup"><span data-stu-id="d6592-141">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

- <span data-ttu-id="d6592-142">**Peering reti virtuali**.</span><span class="sxs-lookup"><span data-stu-id="d6592-142">**VNet peering**.</span></span> <span data-ttu-id="d6592-143">È possibile connettere due reti virtuali tramite una [connessione peering][vnet-peering].</span><span class="sxs-lookup"><span data-stu-id="d6592-143">Two VNets can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="d6592-144">Le connessioni peering sono connessioni non transitive a bassa latenza tra reti virtuali.</span><span class="sxs-lookup"><span data-stu-id="d6592-144">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="d6592-145">Dopo il peering, le reti virtuali si scambiano traffico tramite il backbone di Azure, senza bisogno di un router.</span><span class="sxs-lookup"><span data-stu-id="d6592-145">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="d6592-146">In una topologia hub-spoke di rete si usa il peering reti virtuali per connettere l'hub a ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-146">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span> <span data-ttu-id="d6592-147">È possibile eseguire il peering di reti virtuali nella stessa area o in aree differenti.</span><span class="sxs-lookup"><span data-stu-id="d6592-147">You can peer virtual networks in the same region, or different regions.</span></span> <span data-ttu-id="d6592-148">Per altre informazioni, vedere [Requisiti e vincoli][vnet-peering-requirements].</span><span class="sxs-lookup"><span data-stu-id="d6592-148">For more information, see [Requirements and constraints][vnet-peering-requirements].</span></span>

> [!NOTE]
> <span data-ttu-id="d6592-149">Questo articolo illustra solo le distribuzioni [Resource Manager](/azure/azure-resource-manager/resource-group-overview), ma è anche possibile connettere una rete virtuale classica a una rete virtuale di Resource Manager nella stessa sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="d6592-149">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="d6592-150">In questo modo, gli spoke possono ospitare distribuzioni classiche e trarre comunque vantaggio dai servizi condivisi nell'hub.</span><span class="sxs-lookup"><span data-stu-id="d6592-150">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="d6592-151">Consigli</span><span class="sxs-lookup"><span data-stu-id="d6592-151">Recommendations</span></span>

<span data-ttu-id="d6592-152">Le raccomandazioni seguenti sono valide per la maggior parte degli scenari.</span><span class="sxs-lookup"><span data-stu-id="d6592-152">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="d6592-153">Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.</span><span class="sxs-lookup"><span data-stu-id="d6592-153">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="d6592-154">Gruppi di risorse</span><span class="sxs-lookup"><span data-stu-id="d6592-154">Resource groups</span></span>

<span data-ttu-id="d6592-155">È possibile implementare la rete virtuale dell'hub e ogni rete virtuale spoke in gruppi di risorse diversi e persino in sottoscrizioni diverse.</span><span class="sxs-lookup"><span data-stu-id="d6592-155">The hub VNet, and each spoke VNet, can be implemented in different resource groups, and even different subscriptions.</span></span> <span data-ttu-id="d6592-156">Quando il peering delle reti virtuali viene eseguito in sottoscrizioni diverse, entrambe le sottoscrizioni possono essere associate allo stesso tenant di Azure Active Directory o a uno diverso.</span><span class="sxs-lookup"><span data-stu-id="d6592-156">When you peer virtual networks in different subscriptions, both subscriptions can be associated to the same or different Azure Active Directory tenant.</span></span> <span data-ttu-id="d6592-157">Questo consente di decentralizzare la gestione di ogni carico di lavoro e allo stesso tempo condividere i servizi gestiti nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="d6592-157">This allows for a decentralized management of each workload, while sharing services maintained in the hub VNet.</span></span>

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="d6592-158">Reti virtuali e GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="d6592-158">VNet and GatewaySubnet</span></span>

<span data-ttu-id="d6592-159">Creare una subnet denominata *GatewaySubnet*, con un intervallo di indirizzi di /27.</span><span class="sxs-lookup"><span data-stu-id="d6592-159">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="d6592-160">Questa subnet è necessaria per il gateway di rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="d6592-160">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="d6592-161">Allocare 32 indirizzi a questa subnet contribuirà in futuro a evitare di raggiungere i limiti di dimensioni del gateway.</span><span class="sxs-lookup"><span data-stu-id="d6592-161">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span>

<span data-ttu-id="d6592-162">Per altre informazioni sulla configurazione del gateway, vedere le architetture di riferimento seguenti, a seconda del tipo di connessione:</span><span class="sxs-lookup"><span data-stu-id="d6592-162">For more information about setting up the gateway, see the following reference architectures, depending on your connection type:</span></span>

- <span data-ttu-id="d6592-163">[Rete ibrida tramite ExpressRoute][guidance-expressroute]</span><span class="sxs-lookup"><span data-stu-id="d6592-163">[Hybrid network using ExpressRoute][guidance-expressroute]</span></span>
- <span data-ttu-id="d6592-164">[Rete ibrida tramite un gateway VPN][guidance-vpn]</span><span class="sxs-lookup"><span data-stu-id="d6592-164">[Hybrid network using a VPN gateway][guidance-vpn]</span></span>

<span data-ttu-id="d6592-165">Per una disponibilità più elevata, è possibile usare ExpressRoute più una rete VPN per il failover.</span><span class="sxs-lookup"><span data-stu-id="d6592-165">For higher availability, you can use ExpressRoute plus a VPN for failover.</span></span> <span data-ttu-id="d6592-166">Vedere [Connettere una rete locale ad Azure tramite ExpressRoute con failover VPN][hybrid-ha].</span><span class="sxs-lookup"><span data-stu-id="d6592-166">See [Connect an on-premises network to Azure using ExpressRoute with VPN failover][hybrid-ha].</span></span>

<span data-ttu-id="d6592-167">Una topologia hub-spoke può essere usata anche senza gateway, se non è necessaria la connettività con la rete locale.</span><span class="sxs-lookup"><span data-stu-id="d6592-167">A hub-spoke topology can also be used without a gateway, if you don't need connectivity with your on-premises network.</span></span>

### <a name="vnet-peering"></a><span data-ttu-id="d6592-168">Peering reti virtuali</span><span class="sxs-lookup"><span data-stu-id="d6592-168">VNet peering</span></span>

<span data-ttu-id="d6592-169">Il peering reti virtuali è una relazione non transitiva tra due reti virtuali.</span><span class="sxs-lookup"><span data-stu-id="d6592-169">VNet peering is a non-transitive relationship between two VNets.</span></span> <span data-ttu-id="d6592-170">Se è necessaria la connettività tra gli spoke, considerare l'aggiunta di una connessione peering separata tra di essi.</span><span class="sxs-lookup"><span data-stu-id="d6592-170">If you require spokes to connect to each other, consider adding a separate peering connection between those spokes.</span></span>

<span data-ttu-id="d6592-171">Tuttavia, se si dispone di più spoke che devono connettersi tra loro, le possibili connessioni peering si esauriranno molto rapidamente, a causa del [limite di peering reti virtuali per ogni rete virtuale][vnet-peering-limit].</span><span class="sxs-lookup"><span data-stu-id="d6592-171">However, if you have several spokes that need to connect with each other, you will run out of possible peering connections very quickly due to the [limitation on number of VNets peerings per VNet][vnet-peering-limit].</span></span> <span data-ttu-id="d6592-172">In questo scenario, prendere in considerazione l'uso di route definite dall'utente per forzare l'invio del traffico destinato a uno spoke a un'appliance virtuale di rete che funga da router nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="d6592-172">In this scenario, consider using user defined routes (UDRs) to force traffic destined to a spoke to be sent to an NVA acting as a router at the hub VNet.</span></span> <span data-ttu-id="d6592-173">Questo consentirà agli spoke di connettersi tra loro.</span><span class="sxs-lookup"><span data-stu-id="d6592-173">This will allow the spokes to connect to each other.</span></span>

<span data-ttu-id="d6592-174">È anche possibile configurare gli spoke in modo da usare il gateway della rete virtuale dell'hub per comunicare con reti remote.</span><span class="sxs-lookup"><span data-stu-id="d6592-174">You can also configure spokes to use the hub VNet gateway to communicate with remote networks.</span></span> <span data-ttu-id="d6592-175">Per consentire il flusso del traffico gateway dallo spoke all'hub e connettersi a reti remote, è necessario:</span><span class="sxs-lookup"><span data-stu-id="d6592-175">To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:</span></span>

- <span data-ttu-id="d6592-176">Configurare la connessione peering reti virtuali nell'hub in modo da **consentire il transito gateway**.</span><span class="sxs-lookup"><span data-stu-id="d6592-176">Configure the VNet peering connection in the hub to **allow gateway transit**.</span></span>
- <span data-ttu-id="d6592-177">Configurare la connessione peering reti virtuali in ogni spoke in modo da **usare gateway remoti**.</span><span class="sxs-lookup"><span data-stu-id="d6592-177">Configure the VNet peering connection in each spoke to **use remote gateways**.</span></span>
- <span data-ttu-id="d6592-178">Configurare tutte le connessioni peering reti virtuali in modo da **consentire il traffico inoltrato**.</span><span class="sxs-lookup"><span data-stu-id="d6592-178">Configure all VNet peering connections to **allow forwarded traffic**.</span></span>

## <a name="considerations"></a><span data-ttu-id="d6592-179">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="d6592-179">Considerations</span></span>

### <a name="spoke-connectivity"></a><span data-ttu-id="d6592-180">Connettività spoke</span><span class="sxs-lookup"><span data-stu-id="d6592-180">Spoke connectivity</span></span>

<span data-ttu-id="d6592-181">Se è necessaria la connettività tra spoke, è consigliabile implementare un'appliance virtuale di rete per il routing nell'hub e usare route definite dall'utente nello spoke per inoltrare il traffico all'hub.</span><span class="sxs-lookup"><span data-stu-id="d6592-181">If you require connectivity between spokes, consider implementing an NVA for routing in the hub, and using UDRs in the spoke to forward traffic to the hub.</span></span>

<span data-ttu-id="d6592-182">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="d6592-182">![[2]][2]</span></span>

<span data-ttu-id="d6592-183">In questo scenario occorre configurare le connessioni peering in modo da **consentire il traffico inoltrato**.</span><span class="sxs-lookup"><span data-stu-id="d6592-183">In this scenario, you must configure the peering connections to **allow forwarded traffic**.</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="d6592-184">Superamento dei limiti del peering reti virtuali</span><span class="sxs-lookup"><span data-stu-id="d6592-184">Overcoming VNet peering limits</span></span>

<span data-ttu-id="d6592-185">Assicurarsi di considerare il [limite sul numero di peering reti virtuali per ogni rete virtuale][vnet-peering-limit] in Azure.</span><span class="sxs-lookup"><span data-stu-id="d6592-185">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="d6592-186">Se si stabilisce che saranno necessari più spoke di quanto consentito dal limite, valutare la creazione di una topologia hub-spoke-hub-spoke, in cui il primo livello di spoke funge anche da hub.</span><span class="sxs-lookup"><span data-stu-id="d6592-186">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="d6592-187">Il diagramma seguente illustra questo approccio.</span><span class="sxs-lookup"><span data-stu-id="d6592-187">The following diagram shows this approach.</span></span>

<span data-ttu-id="d6592-188">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="d6592-188">![[3]][3]</span></span>

<span data-ttu-id="d6592-189">Considerare inoltre i servizi condivisi nell'hub, per assicurarsi che quest'ultimo sia scalabile per un numero superiore di spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-189">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="d6592-190">Ad esempio, se l'hub fornisce servizi firewall, valutare i limiti di larghezza di banda della soluzione firewall quando si aggiungono più spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-190">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="d6592-191">Può essere utile spostare alcuni dei servizi condivisi in un secondo livello di hub.</span><span class="sxs-lookup"><span data-stu-id="d6592-191">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="d6592-192">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="d6592-192">Deploy the solution</span></span>

<span data-ttu-id="d6592-193">Una distribuzione di questa architettura è disponibile in [GitHub][ref-arch-repo].</span><span class="sxs-lookup"><span data-stu-id="d6592-193">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="d6592-194">Usa macchine virtuali in ogni rete virtuale per testare la connettività.</span><span class="sxs-lookup"><span data-stu-id="d6592-194">It uses VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="d6592-195">Nella subnet **shared-services** della **rete virtuale dell'hub** non sono effettivamente ospitati servizi.</span><span class="sxs-lookup"><span data-stu-id="d6592-195">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

<span data-ttu-id="d6592-196">La distribuzione crea i seguenti gruppi di risorse nella sottoscrizione:</span><span class="sxs-lookup"><span data-stu-id="d6592-196">The deployment creates the following resource groups in your subscription:</span></span>

- <span data-ttu-id="d6592-197">hub-nva-rg</span><span class="sxs-lookup"><span data-stu-id="d6592-197">hub-nva-rg</span></span>
- <span data-ttu-id="d6592-198">hub-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="d6592-198">hub-vnet-rg</span></span>
- <span data-ttu-id="d6592-199">onprem-jb-rg</span><span class="sxs-lookup"><span data-stu-id="d6592-199">onprem-jb-rg</span></span>
- <span data-ttu-id="d6592-200">onprem-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="d6592-200">onprem-vnet-rg</span></span>
- <span data-ttu-id="d6592-201">spoke1-vnet-rg</span><span class="sxs-lookup"><span data-stu-id="d6592-201">spoke1-vnet-rg</span></span>
- <span data-ttu-id="d6592-202">spoke2-vent-rg</span><span class="sxs-lookup"><span data-stu-id="d6592-202">spoke2-vent-rg</span></span>

<span data-ttu-id="d6592-203">I file parametro di modello fanno riferimento a questi nomi; pertanto, se questi vengono modificati, aggiornare i file parametro in modo che corrispondano.</span><span class="sxs-lookup"><span data-stu-id="d6592-203">The template parameter files refer to these names, so if you change them, update the parameter files to match.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="d6592-204">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="d6592-204">Prerequisites</span></span>

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-simulated-on-premises-datacenter"></a><span data-ttu-id="d6592-205">Distribuire il data center locale simulato</span><span class="sxs-lookup"><span data-stu-id="d6592-205">Deploy the simulated on-premises datacenter</span></span>

<span data-ttu-id="d6592-206">Per distribuire il data center locale simulato come rete virtuale di Azure, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="d6592-206">To deploy the simulated on-premises datacenter as an Azure VNet, follow these steps:</span></span>

1. <span data-ttu-id="d6592-207">Passare alla cartella `hybrid-networking/hub-spoke` del repository di architetture di riferimento.</span><span class="sxs-lookup"><span data-stu-id="d6592-207">Navigate to the `hybrid-networking/hub-spoke` folder of the reference architectures repository.</span></span>

2. <span data-ttu-id="d6592-208">Aprire il file `onprem.json` .</span><span class="sxs-lookup"><span data-stu-id="d6592-208">Open the `onprem.json` file.</span></span> <span data-ttu-id="d6592-209">Sostituire i valori per `adminUsername` e `adminPassword`.</span><span class="sxs-lookup"><span data-stu-id="d6592-209">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```json
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

3. <span data-ttu-id="d6592-210">(Facoltativo) Per una distribuzione Linux, impostare `osType` su `Linux`.</span><span class="sxs-lookup"><span data-stu-id="d6592-210">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

4. <span data-ttu-id="d6592-211">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d6592-211">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g onprem-vnet-rg -l <location> -p onprem.json --deploy
    ```

5. <span data-ttu-id="d6592-212">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="d6592-212">Wait for the deployment to finish.</span></span> <span data-ttu-id="d6592-213">Questa distribuzione crea una rete virtuale, una macchina virtuale e un gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="d6592-213">This deployment creates a virtual network, a virtual machine, and a VPN gateway.</span></span> <span data-ttu-id="d6592-214">Occorreranno circa 40 minuti per creare il gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="d6592-214">It can take about 40 minutes to create the VPN gateway.</span></span>

### <a name="deploy-the-hub-vnet"></a><span data-ttu-id="d6592-215">Distribuire la rete virtuale dell'hub</span><span class="sxs-lookup"><span data-stu-id="d6592-215">Deploy the hub VNet</span></span>

<span data-ttu-id="d6592-216">Per distribuire la rete virtuale dell'hub, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="d6592-216">To deploy the hub VNet, perform the following steps.</span></span>

1. <span data-ttu-id="d6592-217">Aprire il file `hub-vnet.json` .</span><span class="sxs-lookup"><span data-stu-id="d6592-217">Open the `hub-vnet.json` file.</span></span> <span data-ttu-id="d6592-218">Sostituire i valori per `adminUsername` e `adminPassword`.</span><span class="sxs-lookup"><span data-stu-id="d6592-218">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```json
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="d6592-219">(Facoltativo) Per una distribuzione Linux, impostare `osType` su `Linux`.</span><span class="sxs-lookup"><span data-stu-id="d6592-219">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

3. <span data-ttu-id="d6592-220">Trovare entrambe le istanze di `sharedKey` e immettere una chiave condivisa per la connessione VPN.</span><span class="sxs-lookup"><span data-stu-id="d6592-220">Find both instances of `sharedKey` and enter a shared key for the VPN connection.</span></span> <span data-ttu-id="d6592-221">I valori devono corrispondere.</span><span class="sxs-lookup"><span data-stu-id="d6592-221">The values must match.</span></span>

    ```json
    "sharedKey": "",
    ```

4. <span data-ttu-id="d6592-222">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d6592-222">Run the following command:</span></span>

    ```bash
    azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet.json --deploy
    ```

5. <span data-ttu-id="d6592-223">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="d6592-223">Wait for the deployment to finish.</span></span> <span data-ttu-id="d6592-224">Questa distribuzione crea una rete virtuale, una macchina virtuale, un gateway VPN e una connessione al gateway.</span><span class="sxs-lookup"><span data-stu-id="d6592-224">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway.</span></span>  <span data-ttu-id="d6592-225">Occorreranno circa 40 minuti per creare il gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="d6592-225">It can take about 40 minutes to create the VPN gateway.</span></span>

### <a name="test-connectivity-with-the-hub"></a><span data-ttu-id="d6592-226">Testare la connettività con l'hub</span><span class="sxs-lookup"><span data-stu-id="d6592-226">Test connectivity with the hub</span></span>

<span data-ttu-id="d6592-227">Testare la connettività dall'ambiente locale simulato alla rete virtuale hub dell'hub.</span><span class="sxs-lookup"><span data-stu-id="d6592-227">Test conectivity from the simulated on-premises environment to the hub VNet.</span></span>

<span data-ttu-id="d6592-228">**Distribuzione in Windows**</span><span class="sxs-lookup"><span data-stu-id="d6592-228">**Windows deployment**</span></span>

1. <span data-ttu-id="d6592-229">Usare il portale di Azure per trovare la macchina virtuale denominata `jb-vm1` nel gruppo di risorse `onprem-jb-rg`.</span><span class="sxs-lookup"><span data-stu-id="d6592-229">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="d6592-230">Fare clic su `Connect` per aprire una sessione di desktop remoto per la macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="d6592-230">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="d6592-231">Usare la password specificata nel file parametro `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="d6592-231">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="d6592-232">Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi alla macchina virtuale jumpbox nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="d6592-232">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VM in the hub VNet.</span></span>

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```

<span data-ttu-id="d6592-233">L'output dovrebbe essere simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="d6592-233">The output should look similar to the following:</span></span>

```powershell
ComputerName     : 10.0.0.68
RemoteAddress    : 10.0.0.68
RemotePort       : 3389
InterfaceAlias   : Ethernet 2
SourceAddress    : 192.168.1.000
TcpTestSucceeded : True
```

> [!NOTE]
> <span data-ttu-id="d6592-234">Per impostazione predefinita, le VM Windows Server non consentono risposte ICMP in Azure.</span><span class="sxs-lookup"><span data-stu-id="d6592-234">By default, Windows Server VMs do not allow ICMP responses in Azure.</span></span> <span data-ttu-id="d6592-235">Se si vuole usare `ping` per testare la connettività, è necessario abilitare il traffico ICMP in Windows Firewall con sicurezza avanzata per ogni VM.</span><span class="sxs-lookup"><span data-stu-id="d6592-235">If you want to use `ping` to test connectivity, you need to enable ICMP traffic in the Windows Advanced Firewall for each VM.</span></span>

<span data-ttu-id="d6592-236">**Distribuzione in Linux**</span><span class="sxs-lookup"><span data-stu-id="d6592-236">**Linux deployment**</span></span>

1. <span data-ttu-id="d6592-237">Usare il portale di Azure per trovare la macchina virtuale denominata `jb-vm1` nel gruppo di risorse `onprem-jb-rg`.</span><span class="sxs-lookup"><span data-stu-id="d6592-237">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="d6592-238">Fare clic su `Connect` e copiare il comando `ssh` visualizzato nel portale.</span><span class="sxs-lookup"><span data-stu-id="d6592-238">Click `Connect` and copy the `ssh` command shown in the portal.</span></span> 

3. <span data-ttu-id="d6592-239">Da un prompt di Linux, eseguire `ssh` per connettersi all'ambiente locale simulato.</span><span class="sxs-lookup"><span data-stu-id="d6592-239">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment.</span></span> <span data-ttu-id="d6592-240">Usare la password specificata nel file parametro `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="d6592-240">Use the password that you specified in the `onprem.json` parameter file.</span></span>

4. <span data-ttu-id="d6592-241">Usare il comando `ping` per testare la connettività alla macchina virtuale jumpbox nella rete virtuale dell'hub:</span><span class="sxs-lookup"><span data-stu-id="d6592-241">Use the `ping` command to test connectivity to the jumpbox VM in the hub VNet:</span></span>

   ```shell
   ping 10.0.0.68
   ```

### <a name="deploy-the-spoke-vnets"></a><span data-ttu-id="d6592-242">Distribuire le reti virtuali spoke</span><span class="sxs-lookup"><span data-stu-id="d6592-242">Deploy the spoke VNets</span></span>

<span data-ttu-id="d6592-243">Per distribuire le reti virtuali spoke, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="d6592-243">To deploy the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="d6592-244">Aprire il file `spoke1.json` .</span><span class="sxs-lookup"><span data-stu-id="d6592-244">Open the `spoke1.json` file.</span></span> <span data-ttu-id="d6592-245">Sostituire i valori per `adminUsername` e `adminPassword`.</span><span class="sxs-lookup"><span data-stu-id="d6592-245">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```json
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="d6592-246">(Facoltativo) Per una distribuzione Linux, impostare `osType` su `Linux`.</span><span class="sxs-lookup"><span data-stu-id="d6592-246">(Optional) For a Linux deployment, set `osType` to `Linux`.</span></span>

3. <span data-ttu-id="d6592-247">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d6592-247">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg -l <location> -p spoke1.json --deploy
   ```

4. <span data-ttu-id="d6592-248">Ripetere i passaggi 1 e 2 per il file `spoke2.json`.</span><span class="sxs-lookup"><span data-stu-id="d6592-248">Repeat steps 1-2 for the `spoke2.json` file.</span></span>

5. <span data-ttu-id="d6592-249">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d6592-249">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg -l <location> -p spoke2.json --deploy
   ```

6. <span data-ttu-id="d6592-250">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d6592-250">Run the following command:</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet-peering.json --deploy
   ```

### <a name="test-connectivity"></a><span data-ttu-id="d6592-251">Testare la connettività</span><span class="sxs-lookup"><span data-stu-id="d6592-251">Test connectivity</span></span>

<span data-ttu-id="d6592-252">Testare la connettività dall'ambiente locale simulato alle reti virtuali spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-252">Test conectivity from the simulated on-premises environment to the spoke VNets.</span></span>

<span data-ttu-id="d6592-253">**Distribuzione in Windows**</span><span class="sxs-lookup"><span data-stu-id="d6592-253">**Windows deployment**</span></span>

1. <span data-ttu-id="d6592-254">Usare il portale di Azure per trovare la macchina virtuale denominata `jb-vm1` nel gruppo di risorse `onprem-jb-rg`.</span><span class="sxs-lookup"><span data-stu-id="d6592-254">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="d6592-255">Fare clic su `Connect` per aprire una sessione di desktop remoto per la macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="d6592-255">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="d6592-256">Usare la password specificata nel file parametro `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="d6592-256">Use the password that you specified in the `onprem.json` parameter file.</span></span>

3. <span data-ttu-id="d6592-257">Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi alle macchine virtuali nelle reti virtuali spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-257">Open a PowerShell console in the VM, and use the `Test-NetConnection` cmdlet to verify that you can connect to the jumpbox VMs in the spoke VNets.</span></span>

   ```powershell
   Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
   Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
   ```

<span data-ttu-id="d6592-258">**Distribuzione in Linux**</span><span class="sxs-lookup"><span data-stu-id="d6592-258">**Linux deployment**</span></span>

<span data-ttu-id="d6592-259">Per testare la connettività dall'ambiente locale simulato alle reti virtuali spoke con VM Linux, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="d6592-259">To test conectivity from the simulated on-premises environment to the spoke VNets using Linux VMs, perform the following steps:</span></span>

1. <span data-ttu-id="d6592-260">Usare il portale di Azure per trovare la macchina virtuale denominata `jb-vm1` nel gruppo di risorse `onprem-jb-rg`.</span><span class="sxs-lookup"><span data-stu-id="d6592-260">Use the Azure portal to find the VM named `jb-vm1` in the `onprem-jb-rg` resource group.</span></span>

2. <span data-ttu-id="d6592-261">Fare clic su `Connect` e copiare il comando `ssh` visualizzato nel portale.</span><span class="sxs-lookup"><span data-stu-id="d6592-261">Click `Connect` and copy the `ssh` command shown in the portal.</span></span>

3. <span data-ttu-id="d6592-262">Da un prompt di Linux, eseguire `ssh` per connettersi all'ambiente locale simulato.</span><span class="sxs-lookup"><span data-stu-id="d6592-262">From a Linux prompt, run `ssh` to connect to the simulated on-premises environment.</span></span> <span data-ttu-id="d6592-263">Usare la password specificata nel file parametro `onprem.json`.</span><span class="sxs-lookup"><span data-stu-id="d6592-263">Use the password that you specified in the `onprem.json` parameter file.</span></span>

4. <span data-ttu-id="d6592-264">Usare il comando `ping` per testare la connettività alle macchine virtuali jumpbox in ogni spoke:</span><span class="sxs-lookup"><span data-stu-id="d6592-264">Use the `ping` command to test connectivity to the jumpbox VMs in each spoke:</span></span>

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

### <a name="add-connectivity-between-spokes"></a><span data-ttu-id="d6592-265">Aggiungere la connettività tra spoke</span><span class="sxs-lookup"><span data-stu-id="d6592-265">Add connectivity between spokes</span></span>

<span data-ttu-id="d6592-266">Questo passaggio è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="d6592-266">This step is optional.</span></span> <span data-ttu-id="d6592-267">Se si vuole consentire la connessione tra gli spoke, è necessario usare un'appliance virtuale di rete come router nella rete virtuale dell'hub e forzare il traffico dagli spoke verso il router in caso di tentativo di connessione a un altro spoke.</span><span class="sxs-lookup"><span data-stu-id="d6592-267">If you want to allow spokes to connect to each other, you must use a network virtual appliance (NVA) as a router in the hub VNet, and force traffic from spokes to the router when trying to connect to another spoke.</span></span> <span data-ttu-id="d6592-268">Per distribuire un'appliance virtuale di rete di esempio di base come una singola macchina virtuale assieme alle route definite dall'utente necessarie per consentire la connessione delle due reti virtuali spoke, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="d6592-268">To deploy a basic sample NVA as a single VM, along with user-defined routes (UDRs) to allow the two spoke VNets to connect, perform the following steps:</span></span>

1. <span data-ttu-id="d6592-269">Aprire il file `hub-nva.json` .</span><span class="sxs-lookup"><span data-stu-id="d6592-269">Open the `hub-nva.json` file.</span></span> <span data-ttu-id="d6592-270">Sostituire i valori per `adminUsername` e `adminPassword`.</span><span class="sxs-lookup"><span data-stu-id="d6592-270">Replace the values for `adminUsername` and `adminPassword`.</span></span>

    ```json
    "adminUsername": "<user name>",
    "adminPassword": "<password>",
    ```

2. <span data-ttu-id="d6592-271">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d6592-271">Run the following command:</span></span>

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
[vnet-peering-requirements]: /azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures

[0]: ./images/hub-spoke.png "Topologia hub-spoke in Azure"
[1]: ./images/hub-spoke-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo usando un'appliance virtuale di rete"
[3]: ./images/hub-spokehub-spoke.svg "Topologia hub-spoke-hub-spoke in Azure"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
