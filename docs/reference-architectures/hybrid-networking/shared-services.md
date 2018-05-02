---
title: Implementazione di una topologia di rete hub-spoke con servizi condivisi in Azure
description: Come implementare una topologia di rete hub-spoke con servizi condivisi in Azure.
author: telmosampaio
ms.date: 02/25/2018
pnp.series.title: Implement a hub-spoke network topology with shared services in Azure
pnp.series.prev: hub-spoke
ms.openlocfilehash: 83367a3be2f7a1e33c2ef7018d42f70aae99104d
ms.sourcegitcommit: f665226cec96ec818ca06ac6c2d83edb23c9f29c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="implement-a-hub-spoke-network-topology-with-shared-services-in-azure"></a><span data-ttu-id="9e4be-103">Implementare una topologia di rete hub-spoke con servizi condivisi in Azure</span><span class="sxs-lookup"><span data-stu-id="9e4be-103">Implement a hub-spoke network topology with shared services in Azure</span></span>

<span data-ttu-id="9e4be-104">Questa architettura di riferimento, basata sull'architettura di riferimento [hub-spoke][guidance-hub-spoke], include nell'hub servizi condivisi che potranno essere utilizzati da tutti gli spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-104">This reference architecture builds on the [hub-spoke][guidance-hub-spoke] reference architecture to include shared services in the hub that can be consumed by all spokes.</span></span> <span data-ttu-id="9e4be-105">Come primo passo per la migrazione di un data center al cloud e la creazione di un [data center virtuale], i primi servizi da condividere sono quelli di gestione delle identità e di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="9e4be-105">As a first step toward migrating a datacenter to the cloud, and building a [virtual datacenter], the first services you need to share are identity and security.</span></span> <span data-ttu-id="9e4be-106">Questa architettura di riferimento illustra come estendere i servizi di Active Directory dal data center locale ad Azure e come aggiungere un'appliance virtuale di rete che possa fungere da firewall in una topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-106">This reference architecture shows you how to extend your Active Directory services from your on-premises datacenter to Azure, and how to add a network virtual appliance (NVA) that can act as a firewall, in a hub-spoke topology.</span></span>  <span data-ttu-id="9e4be-107">[**Distribuire questa soluzione**](#deploy-the-solution).</span><span class="sxs-lookup"><span data-stu-id="9e4be-107">[**Deploy this solution**](#deploy-the-solution).</span></span>

<span data-ttu-id="9e4be-108">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="9e4be-108">![[0]][0]</span></span>

<span data-ttu-id="9e4be-109">*Scaricare un [file di Visio][visio-download] di questa architettura*</span><span class="sxs-lookup"><span data-stu-id="9e4be-109">*Download a [Visio file][visio-download] of this architecture*</span></span>

<span data-ttu-id="9e4be-110">I vantaggi di questa topologia includono:</span><span class="sxs-lookup"><span data-stu-id="9e4be-110">The benefits of this topology include:</span></span>

* <span data-ttu-id="9e4be-111">**Risparmio sui costi**, centralizzando in un'unica posizione i servizi che possono essere condivisi da più carichi di lavoro, ad esempio appliance virtuali di rete e server DNS.</span><span class="sxs-lookup"><span data-stu-id="9e4be-111">**Cost savings** by centralizing services that can be shared by multiple workloads, such as network virtual appliances (NVAs) and DNS servers, in a single location.</span></span>
* <span data-ttu-id="9e4be-112">**Superamento dei limiti delle sottoscrizioni** eseguendo il peering delle reti virtuali da sottoscrizioni diverse all'hub centrale.</span><span class="sxs-lookup"><span data-stu-id="9e4be-112">**Overcome subscriptions limits** by peering VNets from different subscriptions to the central hub.</span></span>
* <span data-ttu-id="9e4be-113">**Separazione dei compiti** tra IT centrale (SecOPs, InfraOps) e carichi di lavoro (DevOps).</span><span class="sxs-lookup"><span data-stu-id="9e4be-113">**Separation of concerns** between central IT (SecOps, InfraOps) and workloads (DevOps).</span></span>

<span data-ttu-id="9e4be-114">Tra gli usi tipici di questa architettura vi sono:</span><span class="sxs-lookup"><span data-stu-id="9e4be-114">Typical uses for this architecture include:</span></span>

* <span data-ttu-id="9e4be-115">Carichi di lavoro distribuiti in ambienti diversi, ad esempio sviluppo, test e produzione, che richiedono servizi condivisi, ad esempio DNS, IDS, NTP o Active Directory Domain Services.</span><span class="sxs-lookup"><span data-stu-id="9e4be-115">Workloads deployed in different environments, such as development, testing, and production, that require shared services such as DNS, IDS, NTP, or AD DS.</span></span> <span data-ttu-id="9e4be-116">I servizi condivisi vengono inseriti nella rete virtuale dell'hub, mentre ogni ambiente viene distribuito in uno spoke per mantenere l'isolamento.</span><span class="sxs-lookup"><span data-stu-id="9e4be-116">Shared services are placed in the hub VNet, while each environment is deployed to a spoke to maintain isolation.</span></span>
* <span data-ttu-id="9e4be-117">Carichi di lavoro che non richiedono connettività uno con l'altro, ma richiedono l'accesso ai servizi condivisi.</span><span class="sxs-lookup"><span data-stu-id="9e4be-117">Workloads that do not require connectivity to each other, but require access to shared services.</span></span>
* <span data-ttu-id="9e4be-118">Aziende che richiedono il controllo centrale sugli aspetti di sicurezza, ad esempio un firewall nell'hub come rete perimetrale, e gestione separata per i carichi di lavoro in ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-118">Enterprises that require central control over security aspects, such as a firewall in the hub as a DMZ, and segregated management for the workloads in each spoke.</span></span>

## <a name="architecture"></a><span data-ttu-id="9e4be-119">Architecture</span><span class="sxs-lookup"><span data-stu-id="9e4be-119">Architecture</span></span>

<span data-ttu-id="9e4be-120">L'architettura è costituita dai componenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="9e4be-120">The architecture consists of the following components.</span></span>

* <span data-ttu-id="9e4be-121">**Rete locale**.</span><span class="sxs-lookup"><span data-stu-id="9e4be-121">**On-premises network**.</span></span> <span data-ttu-id="9e4be-122">Una rete LAN privata in esecuzione all'interno di un'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="9e4be-122">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="9e4be-123">**Dispositivo VPN**.</span><span class="sxs-lookup"><span data-stu-id="9e4be-123">**VPN device**.</span></span> <span data-ttu-id="9e4be-124">Un dispositivo o un servizio che offre connettività esterna alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="9e4be-124">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="9e4be-125">Il dispositivo VPN può essere un dispositivo hardware o una soluzione software, ad esempio il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012.</span><span class="sxs-lookup"><span data-stu-id="9e4be-125">The VPN device may be a hardware device, or a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="9e4be-126">Per un elenco delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN da sito a sito][vpn-appliance].</span><span class="sxs-lookup"><span data-stu-id="9e4be-126">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="9e4be-127">**Gateway di rete virtuale VPN o gateway ExpressRoute**.</span><span class="sxs-lookup"><span data-stu-id="9e4be-127">**VPN virtual network gateway or ExpressRoute gateway**.</span></span> <span data-ttu-id="9e4be-128">Il gateway di rete virtuale consente di connettere la rete virtuale al dispositivo VPN o al circuito ExpressRoute usato per la connettività con la propria rete locale.</span><span class="sxs-lookup"><span data-stu-id="9e4be-128">The virtual network gateway enables the VNet to connect to the VPN device, or ExpressRoute circuit, used for connectivity with your on-premises network.</span></span> <span data-ttu-id="9e4be-129">Per altre informazioni, vedere [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].</span><span class="sxs-lookup"><span data-stu-id="9e4be-129">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

> [!NOTE]
> <span data-ttu-id="9e4be-130">Gli script di distribuzione per questa architettura di riferimento usano un gateway VPN per la connettività e una rete virtuale in Azure per simulare la rete locale.</span><span class="sxs-lookup"><span data-stu-id="9e4be-130">The deployment scripts for this reference architecture use a VPN gateway for connectivity, and a VNet in Azure to simulate your on-premises network.</span></span>

* <span data-ttu-id="9e4be-131">**Rete virtuale dell'hub**.</span><span class="sxs-lookup"><span data-stu-id="9e4be-131">**Hub VNet**.</span></span> <span data-ttu-id="9e4be-132">Rete virtuale di Azure usata come hub nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-132">Azure VNet used as the hub in the hub-spoke topology.</span></span> <span data-ttu-id="9e4be-133">L'hub è il punto di connettività centrale alla rete locale e offre una posizione in cui ospitare servizi che possono essere utilizzati da carichi di lavoro diversi ospitati nelle reti virtuali spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-133">The hub is the central point of connectivity to your on-premises network, and a place to host services that can be consumed by the different workloads hosted in the spoke VNets.</span></span>

* <span data-ttu-id="9e4be-134">**Subnet del gateway**.</span><span class="sxs-lookup"><span data-stu-id="9e4be-134">**Gateway subnet**.</span></span> <span data-ttu-id="9e4be-135">I gateway di rete virtuale vengono mantenuti nella stessa subnet.</span><span class="sxs-lookup"><span data-stu-id="9e4be-135">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="9e4be-136">**Subnet dei servizi condivisi**.</span><span class="sxs-lookup"><span data-stu-id="9e4be-136">**Shared services subnet**.</span></span> <span data-ttu-id="9e4be-137">Una subnet nella rete virtuale dell'hub usata per ospitare servizi che possono essere condivisi tra tutti gli spoke, ad esempio DNS o Active Directory Domain Services.</span><span class="sxs-lookup"><span data-stu-id="9e4be-137">A subnet in the hub VNet used to host services that can be shared among all spokes, such as DNS or AD DS.</span></span>

* <span data-ttu-id="9e4be-138">**Subnet della rete perimetrale**.</span><span class="sxs-lookup"><span data-stu-id="9e4be-138">**DMZ subnet**.</span></span> <span data-ttu-id="9e4be-139">Subnet nella rete virtuale hub usata per ospitare appliance virtuali di rete che possono fungere da appliance di sicurezza, ad esempio da firewall.</span><span class="sxs-lookup"><span data-stu-id="9e4be-139">A subnet in the hub VNet used to host NVAs that can act as security appliances, such as firewalls.</span></span>

* <span data-ttu-id="9e4be-140">**Reti virtuali spoke**.</span><span class="sxs-lookup"><span data-stu-id="9e4be-140">**Spoke VNets**.</span></span> <span data-ttu-id="9e4be-141">Una o più reti virtuali di Azure usate come spoke nella topologia hub-spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-141">One or more Azure VNets that are used as spokes in the hub-spoke topology.</span></span> <span data-ttu-id="9e4be-142">Gli spoke possono essere usati per isolare i carichi di lavoro nelle reti virtuali corrispondenti, gestite separatamente rispetto agli altri spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-142">Spokes can be used to isolate workloads in their own VNets, managed separately from other spokes.</span></span> <span data-ttu-id="9e4be-143">Ogni carico di lavoro può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure.</span><span class="sxs-lookup"><span data-stu-id="9e4be-143">Each workload might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="9e4be-144">Per ulteriori informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] e [Esecuzione di carichi di lavoro della macchina virtuale Linux][linux-vm-ra].</span><span class="sxs-lookup"><span data-stu-id="9e4be-144">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="9e4be-145">**Peering reti virtuali**.</span><span class="sxs-lookup"><span data-stu-id="9e4be-145">**VNet peering**.</span></span> <span data-ttu-id="9e4be-146">È possibile connettere due reti virtuali nella stessa area di Azure tramite una [connessione peering][vnet-peering].</span><span class="sxs-lookup"><span data-stu-id="9e4be-146">Two VNets in the same Azure region can be connected using a [peering connection][vnet-peering].</span></span> <span data-ttu-id="9e4be-147">Le connessioni peering sono connessioni non transitive a bassa latenza tra reti virtuali.</span><span class="sxs-lookup"><span data-stu-id="9e4be-147">Peering connections are non-transitive, low latency connections between VNets.</span></span> <span data-ttu-id="9e4be-148">Dopo il peering, le reti virtuali si scambiano traffico tramite il backbone di Azure, senza bisogno di un router.</span><span class="sxs-lookup"><span data-stu-id="9e4be-148">Once peered, the VNets exchange traffic by using the Azure backbone, without the need for a router.</span></span> <span data-ttu-id="9e4be-149">In una topologia hub-spoke di rete si usa il peering reti virtuali per connettere l'hub a ogni spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-149">In a hub-spoke network topology, you use VNet peering to connect the hub to each spoke.</span></span>

> [!NOTE]
> <span data-ttu-id="9e4be-150">Questo articolo illustra solo le distribuzioni [Resource Manager](/azure/azure-resource-manager/resource-group-overview), ma è anche possibile connettere una rete virtuale classica a una rete virtuale di Resource Manager nella stessa sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="9e4be-150">This article only covers [Resource Manager](/azure/azure-resource-manager/resource-group-overview) deployments, but you can also connect a classic VNet to a Resource Manager VNet in the same subscription.</span></span> <span data-ttu-id="9e4be-151">In questo modo, gli spoke possono ospitare distribuzioni classiche e trarre comunque vantaggio dai servizi condivisi nell'hub.</span><span class="sxs-lookup"><span data-stu-id="9e4be-151">That way, your spokes can host classic deployments and still benefit from services shared in the hub.</span></span>

## <a name="recommendations"></a><span data-ttu-id="9e4be-152">Raccomandazioni</span><span class="sxs-lookup"><span data-stu-id="9e4be-152">Recommendations</span></span>

<span data-ttu-id="9e4be-153">Tutte le raccomandazioni per l'architettura di riferimento [hub-spoke][guidance-hub-spoke] si applicano anche all'architettura di riferimento con servizi condivisi.</span><span class="sxs-lookup"><span data-stu-id="9e4be-153">All the recommendations for the [hub-spoke][guidance-hub-spoke] reference architecture also apply to the shared services reference architecture.</span></span> 

<span data-ttu-id="9e4be-154">Per la maggior parte degli scenari con servizi condivisi sono valide anche le raccomandazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="9e4be-154">ALso, the following recommendations apply for most scenarios under shared services.</span></span> <span data-ttu-id="9e4be-155">Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.</span><span class="sxs-lookup"><span data-stu-id="9e4be-155">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="identity"></a><span data-ttu-id="9e4be-156">Identità</span><span class="sxs-lookup"><span data-stu-id="9e4be-156">Identity</span></span>

<span data-ttu-id="9e4be-157">La maggior parte delle organizzazioni aziendali ha un ambiente Active Directory Domain Services nel proprio data center locale.</span><span class="sxs-lookup"><span data-stu-id="9e4be-157">Most enterprise organizations have an Active Directory Directory Services (ADDS) environment in their on-premises datacenter.</span></span> <span data-ttu-id="9e4be-158">Per facilitare la gestione degli asset spostati dalla rete locale ad Azure che dipendono da Active Directory Domain Services, è consigliabile ospitare i controller di dominio di Active Directory Domain Services in Azure.</span><span class="sxs-lookup"><span data-stu-id="9e4be-158">To facilitate management of assets moved to Azure from your on-premises network that depend on ADDS, it is recommended to host ADDS domain controllers in Azure.</span></span>

<span data-ttu-id="9e4be-159">Se si usano oggetti Criteri di gruppo che si preferisce controllare separatamente per Azure e l'ambiente locale, usare un diverso sito AD per ogni area di Azure.</span><span class="sxs-lookup"><span data-stu-id="9e4be-159">If you make use of Group Policy Objects, that you want to control separately for Azure and your on-premises environment, use a different AD site for each Azure region.</span></span> <span data-ttu-id="9e4be-160">Inserire i controller di dominio in una rete virtuale centrale (hub) accessibile dai carichi di lavoro dipendenti.</span><span class="sxs-lookup"><span data-stu-id="9e4be-160">Place your domain controllers in a central VNet (hub) that dependent workloads can access.</span></span>

### <a name="security"></a><span data-ttu-id="9e4be-161">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="9e4be-161">Security</span></span>

<span data-ttu-id="9e4be-162">Quando si spostano carichi di lavoro dall'ambiente locale ad Azure, alcuni di questi dovranno essere ospitati in VM.</span><span class="sxs-lookup"><span data-stu-id="9e4be-162">As you move workloads from your on-premises environment to Azure, some of these workloads will require to be hosted in VMs.</span></span> <span data-ttu-id="9e4be-163">Per motivi di conformità, potrebbe essere necessario applicare restrizioni sul traffico correlato a tali carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="9e4be-163">For compliance reasons, you may need to enforce restrictions on traffic traversing those workloads.</span></span> 

<span data-ttu-id="9e4be-164">È possibile usare appliance virtuali di rete in Azure per ospitare diversi tipi di servizi di sicurezza e gestione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="9e4be-164">You can use network virtual appliances (NVAs) in Azure to host different types of security and performance services.</span></span> <span data-ttu-id="9e4be-165">Se si ha attualmente familiarità con un determinato set di appliance in locale, è consigliabile usare le stesse appliance virtualizzate in Azure, se possibile.</span><span class="sxs-lookup"><span data-stu-id="9e4be-165">If you are familiar with a given set of appliances on-premises today, it is recommended to use the same virtualized appliances in Azure, where applicable.</span></span>

> [!NOTE]
> <span data-ttu-id="9e4be-166">Gli script di distribuzione per questa architettura di riferimento usano una macchina virtuale Ubuntu con inoltro IP abilitato per simulare un'appliance virtuale di rete.</span><span class="sxs-lookup"><span data-stu-id="9e4be-166">The deployment scripts for this reference architecture use an Ubuntu VM with IP forwarding enabled to mimic a network virtual appliance.</span></span>

## <a name="considerations"></a><span data-ttu-id="9e4be-167">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="9e4be-167">Considerations</span></span>

### <a name="overcoming-vnet-peering-limits"></a><span data-ttu-id="9e4be-168">Superamento dei limiti del peering reti virtuali</span><span class="sxs-lookup"><span data-stu-id="9e4be-168">Overcoming VNet peering limits</span></span>

<span data-ttu-id="9e4be-169">Assicurarsi di considerare il [limite sul numero di peering reti virtuali per ogni rete virtuale][vnet-peering-limit] in Azure.</span><span class="sxs-lookup"><span data-stu-id="9e4be-169">Make sure you consider the [limitation on number of VNets peerings per VNet][vnet-peering-limit] in Azure.</span></span> <span data-ttu-id="9e4be-170">Se si stabilisce che saranno necessari più spoke di quanto consentito dal limite, valutare la creazione di una topologia hub-spoke-hub-spoke, in cui il primo livello di spoke funge anche da hub.</span><span class="sxs-lookup"><span data-stu-id="9e4be-170">If you decide you need more spokes than the limit will allow, consider creating a hub-spoke-hub-spoke topology, where the first level of spokes also act as hubs.</span></span> <span data-ttu-id="9e4be-171">Il diagramma seguente illustra questo approccio.</span><span class="sxs-lookup"><span data-stu-id="9e4be-171">The following diagram shows this approach.</span></span>

<span data-ttu-id="9e4be-172">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="9e4be-172">![[3]][3]</span></span>

<span data-ttu-id="9e4be-173">Considerare inoltre i servizi condivisi nell'hub, per assicurarsi che quest'ultimo sia scalabile per un numero superiore di spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-173">Also consider what services are shared in the hub, to ensure the hub scales for a larger number of spokes.</span></span> <span data-ttu-id="9e4be-174">Ad esempio, se l'hub fornisce servizi firewall, valutare i limiti di larghezza di banda della soluzione firewall quando si aggiungono più spoke.</span><span class="sxs-lookup"><span data-stu-id="9e4be-174">For instance, if your hub provides firewall services, consider the bandwidth limits of your firewall solution when adding multiple spokes.</span></span> <span data-ttu-id="9e4be-175">Può essere utile spostare alcuni dei servizi condivisi in un secondo livello di hub.</span><span class="sxs-lookup"><span data-stu-id="9e4be-175">You might want to move some of these shared services to a second level of hubs.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="9e4be-176">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="9e4be-176">Deploy the solution</span></span>

<span data-ttu-id="9e4be-177">Una distribuzione di questa architettura è disponibile in [GitHub][ref-arch-repo].</span><span class="sxs-lookup"><span data-stu-id="9e4be-177">A deployment for this architecture is available on [GitHub][ref-arch-repo].</span></span> <span data-ttu-id="9e4be-178">Usa VM Ubuntu in ogni rete virtuale per testare la connettività.</span><span class="sxs-lookup"><span data-stu-id="9e4be-178">It uses Ubuntu VMs in each VNet to test connectivity.</span></span> <span data-ttu-id="9e4be-179">Nella subnet **shared-services** della **rete virtuale dell'hub** non sono effettivamente ospitati servizi.</span><span class="sxs-lookup"><span data-stu-id="9e4be-179">There are no actual services hosted in the **shared-services** subnet in the **hub VNet**.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="9e4be-180">prerequisiti</span><span class="sxs-lookup"><span data-stu-id="9e4be-180">Prerequisites</span></span>

<span data-ttu-id="9e4be-181">Prima di poter distribuire l'architettura di riferimento nella propria sottoscrizione, è necessario eseguire i passaggi seguenti.</span><span class="sxs-lookup"><span data-stu-id="9e4be-181">Before you can deploy the reference architecture to your own subscription, you must perform the following steps.</span></span>

1. <span data-ttu-id="9e4be-182">Clonare, creare una copia tramite fork o scaricare il file ZIP per il repository GitHub delle [architetture di riferimento][ref-arch-repo].</span><span class="sxs-lookup"><span data-stu-id="9e4be-182">Clone, fork, or download the zip file for the [reference architectures][ref-arch-repo] GitHub repository.</span></span>

2. <span data-ttu-id="9e4be-183">Verificare che nel computer sia installata l'interfaccia della riga di comando di Azure 2.0.</span><span class="sxs-lookup"><span data-stu-id="9e4be-183">Make sure you have the Azure CLI 2.0 installed on your computer.</span></span> <span data-ttu-id="9e4be-184">Per istruzioni sull'installazione dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-2].</span><span class="sxs-lookup"><span data-stu-id="9e4be-184">For CLI installation instructions, see [Install Azure CLI 2.0][azure-cli-2].</span></span>

3. <span data-ttu-id="9e4be-185">Installare il pacchetto npm dei [blocchi predefiniti di Azure][azbb].</span><span class="sxs-lookup"><span data-stu-id="9e4be-185">Install the [Azure building blocks][azbb] npm package.</span></span>

4. <span data-ttu-id="9e4be-186">Al prompt dei comandi, di Bash o di PowerShell accedere all'account Azure con questo comando e seguire le istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9e4be-186">From a command prompt, bash prompt, or PowerShell prompt, login to your Azure account by using the command below, and follow the prompts.</span></span>

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a><span data-ttu-id="9e4be-187">Distribuire il data center locale simulato con azbb</span><span class="sxs-lookup"><span data-stu-id="9e4be-187">Deploy the simulated on-premises datacenter using azbb</span></span>

<span data-ttu-id="9e4be-188">Per distribuire il data center locale simulato come rete virtuale di Azure, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="9e4be-188">To deploy the simulated on-premises datacenter as an Azure VNet, follow these steps:</span></span>

1. <span data-ttu-id="9e4be-189">Passare alla cartella `hybrid-networking\shared-services-stack\` per il repository scaricato nel passaggio dei prerequisiti precedente.</span><span class="sxs-lookup"><span data-stu-id="9e4be-189">Navigate to the `hybrid-networking\shared-services-stack\` folder for the repository you downloaded in the pre-requisites step above.</span></span>

2. <span data-ttu-id="9e4be-190">Aprire il file `onprem.json` e immettere un nome utente e una password tra le virgolette nelle righe 45 e 46, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9e4be-190">Open the `onprem.json` file and enter a username and password between the quotes in line 45 and 46, as shown below, then save the file.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

3. <span data-ttu-id="9e4be-191">Eseguire `azbb` per distribuire l'ambiente locale simulato, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="9e4be-191">Run `azbb` to deploy the simulated onprem environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g onprem-vnet-rg - l <location> -p onoprem.json --deploy
   ```
   > [!NOTE]
   > <span data-ttu-id="9e4be-192">Se si decide di usare un nome del gruppo di risorse diverso da `onprem-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9e4be-192">If you decide to use a different resource group name (other than `onprem-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

4. <span data-ttu-id="9e4be-193">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9e4be-193">Wait for the deployment to finish.</span></span> <span data-ttu-id="9e4be-194">Questa distribuzione crea una rete virtuale, una macchina virtuale che esegue Windows e un gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="9e4be-194">This deployment creates a virtual network, a virtual machine running Windows, and a VPN gateway.</span></span> <span data-ttu-id="9e4be-195">La creazione del gateway VPN può richiedere più di 40 minuti.</span><span class="sxs-lookup"><span data-stu-id="9e4be-195">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="azure-hub-vnet"></a><span data-ttu-id="9e4be-196">Rete virtuale dell'hub di Azure</span><span class="sxs-lookup"><span data-stu-id="9e4be-196">Azure hub VNet</span></span>

<span data-ttu-id="9e4be-197">Per distribuire la rete virtuale dell'hub e connettersi alla rete virtuale locale simulata creata in precedenza, eseguire la procedura seguente.</span><span class="sxs-lookup"><span data-stu-id="9e4be-197">To deploy the hub VNet, and connect to the simulated on-premises VNet created above, perform the following steps.</span></span>

1. <span data-ttu-id="9e4be-198">Aprire il file `hub-vnet.json` e immettere un nome utente e una password tra le virgolette nelle righe 50 e 51, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="9e4be-198">Open the `hub-vnet.json` file and enter a username and password between the quotes in line 50 and 51, as shown below.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. <span data-ttu-id="9e4be-199">Nella riga 52 digitare `Windows` o `Linux` per `osType` per installare Windows Server 2016 Datacenter o Ubuntu 16.04 come sistema operativo per il jumpbox.</span><span class="sxs-lookup"><span data-stu-id="9e4be-199">On line 52, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

3. <span data-ttu-id="9e4be-200">Immettere una chiave condivisa tra le virgolette nella riga 83, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9e4be-200">Enter a shared key between the quotes in line 83, as shown below, then save the file.</span></span>

   ```bash
   "sharedKey": "",
   ```

4. <span data-ttu-id="9e4be-201">Eseguire `azbb` per distribuire l'ambiente locale simulato, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="9e4be-201">Run `azbb` to deploy the simulated onprem environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet.json --deploy
   ```
   > [!NOTE]
   > <span data-ttu-id="9e4be-202">Se si decide di usare un nome del gruppo di risorse diverso da `hub-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9e4be-202">If you decide to use a different resource group name (other than `hub-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

5. <span data-ttu-id="9e4be-203">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9e4be-203">Wait for the deployment to finish.</span></span> <span data-ttu-id="9e4be-204">Questa distribuzione crea una rete virtuale, una macchina virtuale, un gateway VPN e una connessione al gateway creato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="9e4be-204">This deployment creates a virtual network, a virtual machine, a VPN gateway, and a connection to the gateway created in the previous section.</span></span> <span data-ttu-id="9e4be-205">La creazione del gateway VPN può richiedere più di 40 minuti.</span><span class="sxs-lookup"><span data-stu-id="9e4be-205">The VPN gateway creation can take more than 40 minutes to complete.</span></span>

### <a name="adds-in-azure"></a><span data-ttu-id="9e4be-206">Active Directory Domain Services in Azure</span><span class="sxs-lookup"><span data-stu-id="9e4be-206">ADDS in Azure</span></span>

<span data-ttu-id="9e4be-207">Per distribuire i controller di dominio di Active Directory Domain Services in Azure, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="9e4be-207">To deploy the ADDS domain controllers in Azure, perform the following steps.</span></span>

1. <span data-ttu-id="9e4be-208">Aprire il file `hub-adds.json` e immettere un nome utente e una password tra le virgolette nelle righe 14 e 15, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9e4be-208">Open the `hub-adds.json` file and enter a username and password between the quotes in lines 14 and 15, as shown below, then save the file.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. <span data-ttu-id="9e4be-209">Eseguire `azbb` per distribuire i controller di dominio di Active Directory Domain Services, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="9e4be-209">Run `azbb` to deploy the ADDS domain controllers as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-adds-rg - l <location> -p hub-adds.json --deploy
   ```
  
   > [!NOTE]
   > <span data-ttu-id="9e4be-210">Se si decide di usare un nome del gruppo di risorse diverso da `hub-adds-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9e4be-210">If you decide to use a different resource group name (other than `hub-adds-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

   > [!NOTE]
   > <span data-ttu-id="9e4be-211">Questa parte della distribuzione potrebbe richiedere alcuni minuti, perché prevede l'aggiunta delle due VM al dominio ospitato nel data center locale simulato e quindi l'installazione di Active Directory Domain Services in tali VM.</span><span class="sxs-lookup"><span data-stu-id="9e4be-211">This part of the deployment may take several minutes, since it requires joining the two VMs to the domain hosted int he simulated on-premises datacenter, then installing AD DS on them.</span></span>

### <a name="nva"></a><span data-ttu-id="9e4be-212">Appliance virtuale di rete</span><span class="sxs-lookup"><span data-stu-id="9e4be-212">NVA</span></span>

<span data-ttu-id="9e4be-213">Per distribuire un'appliance virtuale di rete nella subnet `dmz`, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="9e4be-213">To deploy an NVA in the `dmz` subnet, perform the following steps:</span></span>

1. <span data-ttu-id="9e4be-214">Aprire il file `hub-nva.json` e immettere un nome utente e una password tra le virgolette nelle righe 13 e 14, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9e4be-214">Open the `hub-nva.json` file and enter a username and password between the quotes in lines 13 and 14, as shown below, then save the file.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```
2. <span data-ttu-id="9e4be-215">Eseguire `azbb` per distribuire la VM dell'appliance virtuale di rete e le route definite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="9e4be-215">Run `azbb` to deploy the NVA VM and user defined routes.</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg - l <location> -p hub-nva.json --deploy
   ```
   > [!NOTE]
   > <span data-ttu-id="9e4be-216">Se si decide di usare un nome del gruppo di risorse diverso da `hub-nva-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9e4be-216">If you decide to use a different resource group name (other than `hub-nva-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

### <a name="azure-spoke-vnets"></a><span data-ttu-id="9e4be-217">Reti virtuali spoke di Azure</span><span class="sxs-lookup"><span data-stu-id="9e4be-217">Azure spoke VNets</span></span>

<span data-ttu-id="9e4be-218">Per distribuire le reti virtuali spoke, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="9e4be-218">To deploy the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="9e4be-219">Aprire il file `spoke1.json` e immettere un nome utente e una password tra le virgolette nelle righe 52 e 53, come illustrato di seguito, quindi salvare il file.</span><span class="sxs-lookup"><span data-stu-id="9e4be-219">Open the `spoke1.json` file and enter a username and password between the quotes in lines 52 and 53, as shown below, then save the file.</span></span>

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. <span data-ttu-id="9e4be-220">Nella riga 54 digitare `Windows` o `Linux` per `osType` per installare Windows Server 2016 Datacenter o Ubuntu 16.04 come sistema operativo per il jumpbox.</span><span class="sxs-lookup"><span data-stu-id="9e4be-220">On line 54, for `osType`, type `Windows` or `Linux` to install either Windows Server 2016 Datacenter, or Ubuntu 16.04 as the operating system for the jumpbox.</span></span>

3. <span data-ttu-id="9e4be-221">Eseguire `azbb` per distribuire l'ambiente della prima rete virtuale spoke, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="9e4be-221">Run `azbb` to deploy the first spoke VNet environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg - l <location> -p spoke1.json --deploy
   ```
  
   > [!NOTE]
   > <span data-ttu-id="9e4be-222">Se si decide di usare un nome del gruppo di risorse diverso da `spoke1-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9e4be-222">If you decide to use a different resource group name (other than `spoke1-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

4. <span data-ttu-id="9e4be-223">Ripetere il passaggio 1 precedente per il file `spoke2.json`.</span><span class="sxs-lookup"><span data-stu-id="9e4be-223">Repeat step 1 above for file `spoke2.json`.</span></span>

5. <span data-ttu-id="9e4be-224">Eseguire `azbb` per distribuire l'ambiente della seconda rete virtuale spoke, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="9e4be-224">Run `azbb` to deploy the second spoke VNet environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg - l <location> -p spoke2.json --deploy
   ```
   > [!NOTE]
   > <span data-ttu-id="9e4be-225">Se si decide di usare un nome del gruppo di risorse diverso da `spoke2-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9e4be-225">If you decide to use a different resource group name (other than `spoke2-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a><span data-ttu-id="9e4be-226">Peering reti virtuali dell'hub di Azure alle reti virtuali spoke</span><span class="sxs-lookup"><span data-stu-id="9e4be-226">Azure hub VNet peering to spoke VNets</span></span>

<span data-ttu-id="9e4be-227">Per creare una connessione peering dalla rete virtuale hub alle reti virtuali spoke, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="9e4be-227">To create a peering connection from the hub VNet to the spoke VNets, perform the following steps.</span></span>

1. <span data-ttu-id="9e4be-228">Aprire il file `hub-vnet-peering.json` e verificare che il nome del gruppo di risorse e il nome della rete virtuale per ogni peering reti virtuali, a partire dalla riga 29, siano corretti.</span><span class="sxs-lookup"><span data-stu-id="9e4be-228">Open the `hub-vnet-peering.json` file and verify that the resource group name, and virtual network name for each of the virtual network peerings starting in line 29 are correct.</span></span>

2. <span data-ttu-id="9e4be-229">Eseguire `azbb` per distribuire l'ambiente della prima rete virtuale spoke, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="9e4be-229">Run `azbb` to deploy the first spoke VNet environment as shown below.</span></span>

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet-peering.json --deploy
   ```

   > [!NOTE]
   > <span data-ttu-id="9e4be-230">Se si decide di usare un nome del gruppo di risorse diverso da `hub-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.</span><span class="sxs-lookup"><span data-stu-id="9e4be-230">If you decide to use a different resource group name (other than `hub-vnet-rg`), make sure to search for all parameter files that use that name and edit them to use your own resource group name.</span></span>

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[guidance-hub-spoke]: ./hub-spoke.md
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[data center virtuale]: https://aka.ms/vdc
[virtual datacenter]: https://aka.ms/vdc
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/shared-services.png "Topologia con servizi condivisi in Azure"
[3]: ./images/hub-spokehub-spoke.svg "Topologia hub-spoke-hub-spoke in Azure"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
