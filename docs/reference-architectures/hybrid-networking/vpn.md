---
title: Connettere una rete locale ad Azure tramite VPN
description: Come implementare un'architettura di rete sicura da sito a sito, che si estende su una rete virtuale di Azure e su una rete locale connessa tramite VPN.
author: RohitSharma-pnp
ms.date: 11/28/2016
pnp.series.title: Connect an on-premises network to Azure
pnp.series.next: expressroute
pnp.series.prev: ./index
cardTitle: VPN
ms.openlocfilehash: d0966791642ee174d020c960c9fc6e72d8768539
ms.sourcegitcommit: b38ba378c9d6110da2dfd50b4233fadd94604bb0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167439"
---
# <a name="connect-an-on-premises-network-to-azure-using-a-vpn-gateway"></a><span data-ttu-id="9fdc9-103">Connettere una rete locale ad Azure tramite un gateway VPN</span><span class="sxs-lookup"><span data-stu-id="9fdc9-103">Connect an on-premises network to Azure using a VPN gateway</span></span>

<span data-ttu-id="9fdc9-104">Questa architettura di riferimento mostra come estendere una rete locale ad Azure tramite una rete privata virtuale (VPN) da sito a sito.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-104">This reference architecture shows how to extend an on-premises network to Azure, using a site-to-site virtual private network (VPN).</span></span> <span data-ttu-id="9fdc9-105">Il traffico passa tra la rete locale e la rete virtuale di Azure tramite un tunnel per VPN IPSec.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-105">Traffic flows between the on-premises network and an Azure Virtual Network (VNet) through an IPSec VPN tunnel.</span></span> [<span data-ttu-id="9fdc9-106">**Distribuire questa soluzione**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-106">**Deploy this solution**.</span></span>](#deploy-the-solution)

<span data-ttu-id="9fdc9-107">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="9fdc9-107">![[0]][0]</span></span>

<span data-ttu-id="9fdc9-108">*Scaricare un [file Visio][visio-download] di questa architettura.*</span><span class="sxs-lookup"><span data-stu-id="9fdc9-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="9fdc9-109">Architettura</span><span class="sxs-lookup"><span data-stu-id="9fdc9-109">Architecture</span></span> 

<span data-ttu-id="9fdc9-110">L'architettura è costituita dai componenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-110">The architecture consists of the following components.</span></span>

* <span data-ttu-id="9fdc9-111">**Rete locale**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-111">**On-premises network**.</span></span> <span data-ttu-id="9fdc9-112">Una rete LAN privata in esecuzione all'interno di un'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-112">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="9fdc9-113">**Appliance VPN**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-113">**VPN appliance**.</span></span> <span data-ttu-id="9fdc9-114">Un dispositivo o un servizio che offre connettività esterna alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-114">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="9fdc9-115">L'appliance VPN può essere un dispositivo hardware o una soluzione software, ad esempio il servizio Routing e Accesso remoto (RRAS) in Windows Server 2012.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-115">The VPN appliance may be a hardware device, or it can be a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="9fdc9-116">Per un elenco di appliance VPN supportate e per informazioni su come configurarle per la connessione a un gateway VPN di Azure, vedere le istruzioni per il dispositivo selezionato nell'articolo [Informazioni sui dispositivi VPN per connessioni del Gateway VPN da sito a sito][vpn-appliance].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-116">For a list of supported VPN appliances and information on configuring them to connect to an Azure VPN gateway, see the instructions for the selected device in the article [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="9fdc9-117">**Rete virtuale**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-117">**Virtual network (VNet)**.</span></span> <span data-ttu-id="9fdc9-118">L'applicazione cloud e i componenti del gateway VPN di Azure si trovano nella stessa [rete virtuale][azure-virtual-network].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-118">The cloud application and the components for the Azure VPN gateway reside in the same [VNet][azure-virtual-network].</span></span>

* <span data-ttu-id="9fdc9-119">**Gateway VPN di Azure**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-119">**Azure VPN gateway**.</span></span> <span data-ttu-id="9fdc9-120">Il servizio [gateway VPN][azure-vpn-gateway] consente di connettere la rete virtuale alla rete locale tramite un'appliance VPN.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-120">The [VPN gateway][azure-vpn-gateway] service enables you to connect the VNet to the on-premises network through a VPN appliance.</span></span> <span data-ttu-id="9fdc9-121">Per maggiori informazioni, consultare [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-121">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span> <span data-ttu-id="9fdc9-122">Il gateway VPN include gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-122">The VPN gateway includes the following elements:</span></span>
  
  * <span data-ttu-id="9fdc9-123">**Gateway di rete virtuale**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-123">**Virtual network gateway**.</span></span> <span data-ttu-id="9fdc9-124">Risorsa che fornisce un'appliance VPN virtuale per la rete virtuale</span><span class="sxs-lookup"><span data-stu-id="9fdc9-124">A resource that provides a virtual VPN appliance for the VNet.</span></span> <span data-ttu-id="9fdc9-125">ed è responsabile del routing del traffico dalla rete locale alla rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-125">It is responsible for routing traffic from the on-premises network to the VNet.</span></span>
  * <span data-ttu-id="9fdc9-126">**Gateway di rete locale**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-126">**Local network gateway**.</span></span> <span data-ttu-id="9fdc9-127">Astrazione dell'appliance VPN locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-127">An abstraction of the on-premises VPN appliance.</span></span> <span data-ttu-id="9fdc9-128">Il routing del traffico di rete dall'applicazione cloud alla rete locale viene eseguito tramite il gateway.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-128">Network traffic from the cloud application to the on-premises network is routed through this gateway.</span></span>
  * <span data-ttu-id="9fdc9-129">**Connessione**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-129">**Connection**.</span></span> <span data-ttu-id="9fdc9-130">La connessione ha proprietà che specificano il tipo di connessione (IPSec) e la chiave condivisa con l'appliance VPN locale per crittografare il traffico.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-130">The connection has properties that specify the connection type (IPSec) and the key shared with the on-premises VPN appliance to encrypt traffic.</span></span>
  * <span data-ttu-id="9fdc9-131">**Subnet del gateway**:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-131">**Gateway subnet**.</span></span> <span data-ttu-id="9fdc9-132">Il gateway di rete virtuale viene mantenuto nella propria subnet, che è soggetta a vari requisiti, descritti nella sezione Raccomandazioni di seguito.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-132">The virtual network gateway is held in its own subnet, which is subject to various requirements, described in the Recommendations section below.</span></span>

* <span data-ttu-id="9fdc9-133">**Applicazione cloud**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-133">**Cloud application**.</span></span> <span data-ttu-id="9fdc9-134">Applicazione contenuta in Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-134">The application hosted in Azure.</span></span> <span data-ttu-id="9fdc9-135">Può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-135">It might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="9fdc9-136">Per altre informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] ed [Esecuzione di carichi di lavoro della macchina virtuale di Linux][linux-vm-ra].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-136">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

* <span data-ttu-id="9fdc9-137">**Servizio di bilanciamento del carico interno**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-137">**Internal load balancer**.</span></span> <span data-ttu-id="9fdc9-138">Il routing del traffico di rete dal gateway VPN viene eseguito per l'applicazione cloud tramite un servizio di bilanciamento del carico interno.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-138">Network traffic from the VPN gateway is routed to the cloud application through an internal load balancer.</span></span> <span data-ttu-id="9fdc9-139">Il servizio di bilanciamento del carico si trova nella subnet front-end dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-139">The load balancer is located in the front-end subnet of the application.</span></span>

## <a name="recommendations"></a><span data-ttu-id="9fdc9-140">Consigli</span><span class="sxs-lookup"><span data-stu-id="9fdc9-140">Recommendations</span></span>

<span data-ttu-id="9fdc9-141">Le raccomandazioni seguenti sono valide per la maggior parte degli scenari.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-141">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="9fdc9-142">Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-142">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="vnet-and-gateway-subnet"></a><span data-ttu-id="9fdc9-143">Rete virtuale e subnet gateway</span><span class="sxs-lookup"><span data-stu-id="9fdc9-143">VNet and gateway subnet</span></span>

<span data-ttu-id="9fdc9-144">Creare una rete virtuale di Azure con uno spazio indirizzi sufficientemente grande per tutte le risorse necessarie.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-144">Create an Azure VNet with an address space large enough for all of your required resources.</span></span> <span data-ttu-id="9fdc9-145">Verificare che lo spazio indirizzi di rete virtuale disponga di spazio sufficiente per la crescita se le macchine virtuali aggiuntive sono necessarie in futuro.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-145">Ensure that the VNet address space has sufficient room for growth if additional VMs are likely to be needed in the future.</span></span> <span data-ttu-id="9fdc9-146">Lo spazio indirizzi della rete virtuale non deve sovrapporsi con la rete locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-146">The address space of the VNet must not overlap with the on-premises network.</span></span> <span data-ttu-id="9fdc9-147">Il diagramma precedente usa ad esempio lo spazio indirizzi 10.20.0.0/16 per la rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-147">For example, the diagram above uses the address space 10.20.0.0/16 for the VNet.</span></span>

<span data-ttu-id="9fdc9-148">Creare una subnet denominata *GatewaySubnet*, con un intervallo di indirizzi /27.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-148">Create a subnet named *GatewaySubnet*, with an address range of /27.</span></span> <span data-ttu-id="9fdc9-149">Questa subnet è necessaria per il gateway di rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-149">This subnet is required by the virtual network gateway.</span></span> <span data-ttu-id="9fdc9-150">Allocare 32 indirizzi a questa subnet contribuirà in futuro a evitare di raggiungere i limiti di dimensioni del gateway.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-150">Allocating 32 addresses to this subnet will help to prevent reaching gateway size limitations in the future.</span></span> <span data-ttu-id="9fdc9-151">Evitare inoltre di inserire questa subnet all'interno dello spazio indirizzi.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-151">Also, avoid placing this subnet in the middle of the address space.</span></span> <span data-ttu-id="9fdc9-152">Una procedura consigliata consiste nell'impostare lo spazio indirizzi per la subnet del gateway all'estremità superiore dello spazio indirizzi della rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-152">A good practice is to set the address space for the gateway subnet at the upper end of the VNet address space.</span></span> <span data-ttu-id="9fdc9-153">L'esempio illustrato nel diagramma usa 10.20.255.224/27.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-153">The example shown in the diagram uses 10.20.255.224/27.</span></span>  <span data-ttu-id="9fdc9-154">Di seguito è riportata una procedura rapida per calcolare il [CIDR]:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-154">Here is a quick procedure to calculate the [CIDR]:</span></span>

1. <span data-ttu-id="9fdc9-155">Impostare i bit variabili nello spazio indirizzi della rete virtuale su 1, fino ai bit usati dalla subnet del gateway, quindi impostare i bit rimanenti su 0.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-155">Set the variable bits in the address space of the VNet to 1, up to the bits being used by the gateway subnet, then set the remaining bits to 0.</span></span>
2. <span data-ttu-id="9fdc9-156">Convertire i bit risultanti in decimali ed esprimerli sotto forma di uno spazio indirizzi con la lunghezza del prefisso impostata sulle dimensioni della subnet del gateway.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-156">Convert the resulting bits to decimal and express it as an address space with the prefix length set to the size of the gateway subnet.</span></span>

<span data-ttu-id="9fdc9-157">Per una rete virtuale con un intervallo di indirizzi IP di 10.20.0.0/16, applicando il passaggio 1 precedente si ottiene 10.20.0b11111111.0b11100000.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-157">For example, for a VNet with an IP address range of 10.20.0.0/16, applying step #1 above becomes 10.20.0b11111111.0b11100000.</span></span>  <span data-ttu-id="9fdc9-158">Se si converte tale risultato in numero decimale e lo si esprime come uno spazio indirizzi, si ottiene 10.20.255.224/27.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-158">Converting that to decimal and expressing it as an address space yields 10.20.255.224/27.</span></span> 

> [!WARNING]
> <span data-ttu-id="9fdc9-159">Non distribuire le macchine virtuali nella subnet del gateway.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-159">Do not deploy any VMs to the gateway subnet.</span></span> <span data-ttu-id="9fdc9-160">Non assegnare inoltre un gruppo di sicurezza di rete a questa subnet, in quanto verrebbe causata l'interruzione del funzionamento del gateway.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-160">Also, do not assign an NSG to this subnet, as it will cause the gateway to stop functioning.</span></span>
> 
> 

### <a name="virtual-network-gateway"></a><span data-ttu-id="9fdc9-161">Gateway di rete virtuale</span><span class="sxs-lookup"><span data-stu-id="9fdc9-161">Virtual network gateway</span></span>

<span data-ttu-id="9fdc9-162">Allocare un indirizzo IP pubblico per il gateway di rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-162">Allocate a public IP address for the virtual network gateway.</span></span>

<span data-ttu-id="9fdc9-163">Creare il gateway di rete virtuale nella subnet del gateway e assegnare a esso il nuovo indirizzo IP pubblico allocato.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-163">Create the virtual network gateway in the gateway subnet and assign it the newly allocated public IP address.</span></span> <span data-ttu-id="9fdc9-164">Usare il tipo di gateway che soddisfa maggiormente i propri requisiti e che sia abilitato dall'appliance VPN in uso:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-164">Use the gateway type that most closely matches your requirements and that is enabled by your VPN appliance:</span></span>

- <span data-ttu-id="9fdc9-165">Creare un [gateway basato su criteri][policy-based-routing] se è necessario controllare da vicino il modo in cui viene eseguito il routing delle richieste in base ai criteri, ad esempio i prefissi di indirizzo.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-165">Create a [policy-based gateway][policy-based-routing] if you need to closely control how requests are routed based on policy criteria such as address prefixes.</span></span> <span data-ttu-id="9fdc9-166">I gateway basati su criteri usano il routing statico e funzionano solo con le connessioni da sito a sito.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-166">Policy-based gateways use static routing, and only work with site-to-site connections.</span></span>

- <span data-ttu-id="9fdc9-167">Creare un [gateway basato su route][route-based-routing] se ci si connette alla rete locale mediante RRAS, sono supportate le connessioni multisito o tra più aree o sono implementate le connessioni da rete virtuale a rete virtuale (incluse le route che attraversano più reti virtuali).</span><span class="sxs-lookup"><span data-stu-id="9fdc9-167">Create a [route-based gateway][route-based-routing] if you connect to the on-premises network using RRAS, support multi-site or cross-region connections, or implement VNet-to-VNet connections (including routes that traverse multiple VNets).</span></span> <span data-ttu-id="9fdc9-168">I gateway basati su route usano il routing dinamico per indirizzare il traffico tra le reti.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-168">Route-based gateways use dynamic routing to direct traffic between networks.</span></span> <span data-ttu-id="9fdc9-169">Sono caratterizzati da una maggiore tolleranza degli errori nel percorso di rete rispetto alle route statiche poiché sono in grado di provare route alternative.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-169">They can tolerate failures in the network path better than static routes because they can try alternative routes.</span></span> <span data-ttu-id="9fdc9-170">I gateway basati su route possono anche ridurre il sovraccarico di gestione, poiché potrebbe non essere necessario aggiornare le route manualmente quando cambiano gli indirizzi di rete.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-170">Route-based gateways can also reduce the management overhead because routes might not need to be updated manually when network addresses change.</span></span>

<span data-ttu-id="9fdc9-171">Per un elenco di appliance VPN supportate, vedere [Informazioni sui dispositivi VPN per connessioni del Gateway VPN da sito a sito][vpn-appliances].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-171">For a list of supported VPN appliances, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliances].</span></span>

> [!NOTE]
> <span data-ttu-id="9fdc9-172">Una volta creato il gateway, non è possibile passare da un tipo di gateway a un altro senza eliminare e ricreare il gateway.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-172">After the gateway has been created, you cannot change between gateway types without deleting and re-creating the gateway.</span></span>
> 
> 

<span data-ttu-id="9fdc9-173">Selezionare lo SKU del gateway VPN di Azure che soddisfa maggiormente i requisiti di velocità effettiva.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-173">Select the Azure VPN gateway SKU that most closely matches your throughput requirements.</span></span> <span data-ttu-id="9fdc9-174">Per altre informazioni, vedere [SKU del gateway][azure-gateway-skus]</span><span class="sxs-lookup"><span data-stu-id="9fdc9-174">For more informayion, see [Gateway SKUs][azure-gateway-skus]</span></span>

> [!NOTE]
> <span data-ttu-id="9fdc9-175">Lo SKU di base non è compatibile con Azure ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-175">The Basic SKU is not compatible with Azure ExpressRoute.</span></span> <span data-ttu-id="9fdc9-176">È possibile [modificare lo SKU][changing-SKUs] dopo che il gateway è stato creato.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-176">You can [change the SKU][changing-SKUs] after the gateway has been created.</span></span>
> 
> 

<span data-ttu-id="9fdc9-177">L'importo addebitato è basato sulla quantità di tempo durante il quale il gateway viene sottoposto a provisioning e risulta disponibile.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-177">You are charged based on the amount of time that the gateway is provisioned and available.</span></span> <span data-ttu-id="9fdc9-178">Vedere [Prezzi di Gateway VPN][azure-gateway-charges].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-178">See [VPN Gateway Pricing][azure-gateway-charges].</span></span>

<span data-ttu-id="9fdc9-179">Creare regole di routing per la subnet del gateway che indirizzano il traffico dell'applicazione in ingresso dal gateway al servizio di bilanciamento del carico interno, anziché consentire alle richieste di passare direttamente alle macchine virtuali dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-179">Create routing rules for the gateway subnet that direct incoming application traffic from the gateway to the internal load balancer, rather than allowing requests to pass directly to the application VMs.</span></span>

### <a name="on-premises-network-connection"></a><span data-ttu-id="9fdc9-180">Connessione di rete locale</span><span class="sxs-lookup"><span data-stu-id="9fdc9-180">On-premises network connection</span></span>

<span data-ttu-id="9fdc9-181">Creare un gateway di rete locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-181">Create a local network gateway.</span></span> <span data-ttu-id="9fdc9-182">Specificare l'indirizzo IP pubblico dell'appliance VPN locale e lo spazio indirizzi della rete locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-182">Specify the public IP address of the on-premises VPN appliance, and the address space of the on-premises network.</span></span> <span data-ttu-id="9fdc9-183">Si noti che l'appliance VPN locale deve avere un indirizzo IP pubblico a cui è possibile accedere tramite il gateway di rete locale in Gateway VPN di Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-183">Note that the on-premises VPN appliance must have a public IP address that can be accessed by the local network gateway in Azure VPN Gateway.</span></span> <span data-ttu-id="9fdc9-184">Il dispositivo VPN non può trovarsi dietro un dispositivo NAT (Network Address Translation).</span><span class="sxs-lookup"><span data-stu-id="9fdc9-184">The VPN device cannot be located behind a network address translation (NAT) device.</span></span>

<span data-ttu-id="9fdc9-185">Creare una connessione da sito a sito per il gateway di rete virtuale e il gateway di rete locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-185">Create a site-to-site connection for the virtual network gateway and the local network gateway.</span></span> <span data-ttu-id="9fdc9-186">Selezionare il tipo di connessione da sito a sito (IPSec) e specificare la chiave condivisa.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-186">Select the site-to-site (IPSec) connection type, and specify the shared key.</span></span> <span data-ttu-id="9fdc9-187">La crittografia da sito a sito con il gateway VPN di Azure è basata sul protocollo IPSec, usando le chiavi precondivise per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-187">Site-to-site encryption with the Azure VPN gateway is based on the IPSec protocol, using preshared keys for authentication.</span></span> <span data-ttu-id="9fdc9-188">Quando si crea il gateway VPN di Azure, si specifica la chiave.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-188">You specify the key when you create the Azure VPN gateway.</span></span> <span data-ttu-id="9fdc9-189">È necessario configurare l'appliance VPN eseguita in locale con la stessa chiave.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-189">You must configure the VPN appliance running on-premises with the same key.</span></span> <span data-ttu-id="9fdc9-190">Altri meccanismi di autenticazione non sono attualmente supportati.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-190">Other authentication mechanisms are not currently supported.</span></span>

<span data-ttu-id="9fdc9-191">Verificare che l'infrastruttura di routing locale sia configurata per inoltrare al dispositivo VPN le richieste destinate agli indirizzi nella rete virtuale di Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-191">Ensure that the on-premises routing infrastructure is configured to forward requests intended for addresses in the Azure VNet to the VPN device.</span></span>

<span data-ttu-id="9fdc9-192">Aprire le porte necessarie all'applicazione cloud nella rete locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-192">Open any ports required by the cloud application in the on-premises network.</span></span>

<span data-ttu-id="9fdc9-193">Eseguire il test della connessione per verificare che:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-193">Test the connection to verify that:</span></span>

* <span data-ttu-id="9fdc9-194">L'appliance VPN locale esegua in modo corretto il routing del traffico all'applicazione cloud tramite il gateway VPN di Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-194">The on-premises VPN appliance correctly routes traffic to the cloud application through the Azure VPN gateway.</span></span>
* <span data-ttu-id="9fdc9-195">La rete virtuale esegua in modo corretto il routing del traffico alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-195">The VNet correctly routes traffic back to the on-premises network.</span></span>
* <span data-ttu-id="9fdc9-196">Il traffico non consentito in entrambe le direzioni sia bloccato in modo corretto.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-196">Prohibited traffic in both directions is blocked correctly.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="9fdc9-197">Considerazioni sulla scalabilità</span><span class="sxs-lookup"><span data-stu-id="9fdc9-197">Scalability considerations</span></span>

<span data-ttu-id="9fdc9-198">È possibile ottenere la scalabilità verticale limitata passando dallo SKU del Gateway VPN standard o di base allo SKU VPN a prestazioni elevate.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-198">You can achieve limited vertical scalability by moving from the Basic or Standard VPN Gateway SKUs to the High Performance VPN SKU.</span></span>

<span data-ttu-id="9fdc9-199">Per le reti virtuali che prevedono un volume elevato di traffico VPN, è possibile considerare di distribuire diversi carichi di lavoro in reti virtuali separate di dimensioni più piccole e di configurare un gateway VPN per ognuno di essi.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-199">For VNets that expect a large volume of VPN traffic, consider distributing the different workloads into separate smaller VNets and configuring a VPN gateway for each of them.</span></span>

<span data-ttu-id="9fdc9-200">È possibile eseguire il partizionamento orizzontale o verticale della rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-200">You can partition the VNet either horizontally or vertically.</span></span> <span data-ttu-id="9fdc9-201">Per eseguire il partizionamento orizzontale, spostare alcune istanze della macchina virtuale di ciascun livello nelle subnet della nuova rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-201">To partition horizontally, move some VM instances from each tier into subnets of the new VNet.</span></span> <span data-ttu-id="9fdc9-202">Ogni rete virtuale avrà a questo punto la stessa struttura e la stessa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-202">The result is that each VNet has the same structure and functionality.</span></span> <span data-ttu-id="9fdc9-203">Per eseguire il partizionamento verticale, riprogettare ogni livello in modo da dividere la funzionalità in diverse aree logiche, ad esempio la gestione degli ordini, la fatturazione, la gestione degli account cliente e così via.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-203">To partition vertically, redesign each tier to divide the functionality into different logical areas (such as handling orders, invoicing, customer account management, and so on).</span></span> <span data-ttu-id="9fdc9-204">Ogni area funzionale può quindi essere inserita nella propria rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-204">Each functional area can then be placed in its own VNet.</span></span>

<span data-ttu-id="9fdc9-205">La replica di un controller di dominio Active Directory locale nella rete virtuale e l'implementazione di DNS nella rete virtuale possono aiutare a limitare il flusso di traffico amministrativo e correlato alla sicurezza locale verso il cloud.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-205">Replicating an on-premises Active Directory domain controller in the VNet, and implementing DNS in the VNet, can help to reduce some of the security-related and administrative traffic flowing from on-premises to the cloud.</span></span> <span data-ttu-id="9fdc9-206">Per altre informazioni, vedere [Estensione di Active Directory Domain Services in Azure][adds-extend-domain].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-206">For more information, see [Extending Active Directory Domain Services (AD DS) to Azure][adds-extend-domain].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="9fdc9-207">Considerazioni sulla disponibilità</span><span class="sxs-lookup"><span data-stu-id="9fdc9-207">Availability considerations</span></span>

<span data-ttu-id="9fdc9-208">Se è necessario assicurarsi che la rete locale rimanga disponibile per il gateway VPN di Azure, implementare un cluster di failover per il gateway VPN locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-208">If you need to ensure that the on-premises network remains available to the Azure VPN gateway, implement a failover cluster for the on-premises VPN gateway.</span></span>

<span data-ttu-id="9fdc9-209">Se l'organizzazione dispone di più siti locali, creare [connessioni mulsito][vpn-gateway-multi-site] per una o più reti virtuali di Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-209">If your organization has multiple on-premises sites, create [multi-site connections][vpn-gateway-multi-site] to one or more Azure VNets.</span></span> <span data-ttu-id="9fdc9-210">Questo approccio richiede il routing dinamico (basato su route), assicurarsi quindi che il gateway VPN locale supporti questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-210">This approach requires dynamic (route-based) routing, so make sure that the on-premises VPN gateway supports this feature.</span></span>

<span data-ttu-id="9fdc9-211">Per dettagli sui contratti di servizio, vedere [Contratto di Servizio per Gateway VPN][sla-for-vpn-gateway].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-211">For details about service level agreements, see [SLA for VPN Gateway][sla-for-vpn-gateway].</span></span> 

## <a name="manageability-considerations"></a><span data-ttu-id="9fdc9-212">Considerazioni sulla gestibilità</span><span class="sxs-lookup"><span data-stu-id="9fdc9-212">Manageability considerations</span></span>

<span data-ttu-id="9fdc9-213">Monitorare le informazioni di diagnostica da appliance VPN locali.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-213">Monitor diagnostic information from on-premises VPN appliances.</span></span> <span data-ttu-id="9fdc9-214">Questo processo dipende dalle funzionalità fornite dall'appliance VPN.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-214">This process depends on the features provided by the VPN appliance.</span></span> <span data-ttu-id="9fdc9-215">Se si usa ad esempio il servizio Routing e Accesso remoto in Windows Server 2012, servirsi della [registrazione RRAS][rras-logging].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-215">For example, if you are using the Routing and Remote Access Service on Windows Server 2012, [RRAS logging][rras-logging].</span></span>

<span data-ttu-id="9fdc9-216">Usare la [diagnostica del gateway VPN di Azure][gateway-diagnostic-logs] per acquisire informazioni sui problemi di connettività.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-216">Use [Azure VPN gateway diagnostics][gateway-diagnostic-logs] to capture information about connectivity issues.</span></span> <span data-ttu-id="9fdc9-217">È possibile usare questi log per tenere traccia delle informazioni, ad esempio l'origine e le destinazioni delle richieste di connessione, il protocollo usato e la modalità con cui è stata stabilita la connessione (o il motivo per cui il tentativo non è riuscito).</span><span class="sxs-lookup"><span data-stu-id="9fdc9-217">These logs can be used to track information such as the source and destinations of connection requests, which protocol was used, and how the connection was established (or why the attempt failed).</span></span>

<span data-ttu-id="9fdc9-218">Monitorare i log operativi del gateway VPN di Azure usando i log di controllo disponibili nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-218">Monitor the operational logs of the Azure VPN gateway using the audit logs available in the Azure portal.</span></span> <span data-ttu-id="9fdc9-219">Sono disponibili log distinti per il gateway di rete locale, il gateway di rete di Azure e la connessione.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-219">Separate logs are available for the local network gateway, the Azure network gateway, and the connection.</span></span> <span data-ttu-id="9fdc9-220">Queste informazioni possono essere usate per tenere traccia di tutte le modifiche apportate al gateway e possono essere utili se, per qualche motivo, si verificano problemi su un gateway precedentemente funzionante.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-220">This information can be used to track any changes made to the gateway, and can be useful if a previously functioning gateway stops working for some reason.</span></span>

<span data-ttu-id="9fdc9-221">![[2]][2]</span><span class="sxs-lookup"><span data-stu-id="9fdc9-221">![[2]][2]</span></span>

<span data-ttu-id="9fdc9-222">Monitorare la connettività e tenere traccia degli eventi di errore di connettività.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-222">Monitor connectivity, and track connectivity failure events.</span></span> <span data-ttu-id="9fdc9-223">È possibile usare un pacchetto di monitoraggio, ad esempio [Nagios][nagios], per acquisire queste informazioni e creare appositi report.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-223">You can use a monitoring package such as [Nagios][nagios] to capture and report this information.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="9fdc9-224">Considerazioni relative alla sicurezza</span><span class="sxs-lookup"><span data-stu-id="9fdc9-224">Security considerations</span></span>

<span data-ttu-id="9fdc9-225">Generare una chiave condivisa diversa per ogni gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-225">Generate a different shared key for each VPN gateway.</span></span> <span data-ttu-id="9fdc9-226">Usare una chiave condivisa complessa per resistere agli attacchi di forza bruta.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-226">Use a strong shared key to help resist brute-force attacks.</span></span>

> [!NOTE]
> <span data-ttu-id="9fdc9-227">Non è attualmente possibile usare Azure Key Vault per precondividere le chiavi per il gateway VPN di Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-227">Currently, you cannot use Azure Key Vault to preshare keys for the Azure VPN gateway.</span></span>
> 
> 

<span data-ttu-id="9fdc9-228">Verificare che il dispositivo VPN locale usi un metodo di crittografia [compatibile con il gateway VPN di Azure][vpn-appliance-ipsec].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-228">Ensure that the on-premises VPN appliance uses an encryption method that is [compatible with the Azure VPN gateway][vpn-appliance-ipsec].</span></span> <span data-ttu-id="9fdc9-229">Per il routing basato su criteri, il gateway VPN di Azure supporta gli algoritmi di crittografia AES256, AES128 e 3DES.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-229">For policy-based routing, the Azure VPN gateway supports the AES256, AES128, and 3DES encryption algorithms.</span></span> <span data-ttu-id="9fdc9-230">I gateway basati su route supportano AES256 e 3DES.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-230">Route-based gateways support AES256 and 3DES.</span></span>

<span data-ttu-id="9fdc9-231">Se l'appliance VPN locale si trova su una rete perimetrale con un firewall tra la rete perimetrale e Internet, potrebbe essere necessario configurare [regole del firewall aggiuntive][additional-firewall-rules] per consentire la connessione VPN da sito a sito.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-231">If your on-premises VPN appliance is on a perimeter network (DMZ) that has a firewall between the perimeter network and the Internet, you might have to configure [additional firewall rules][additional-firewall-rules] to allow the site-to-site VPN connection.</span></span>

<span data-ttu-id="9fdc9-232">Se l'applicazione nella rete virtuale invia dati a Internet, è possibile considerare l'[implementazione del tunneling forzato][forced-tunneling] per eseguire il routing di tutto il traffico associato a Internet attraverso la rete locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-232">If the application in the VNet sends data to the Internet, consider [implementing forced tunneling][forced-tunneling] to route all Internet-bound traffic through the on-premises network.</span></span> <span data-ttu-id="9fdc9-233">Questo approccio consente di controllare le richieste in uscita eseguite tramite l'applicazione dall'infrastruttura locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-233">This approach enables you to audit outgoing requests made by the application from the on-premises infrastructure.</span></span>

> [!NOTE]
> <span data-ttu-id="9fdc9-234">Il tunneling forzato può influire sulla connettività ai servizi di Azure, ad esempio Servizio di archiviazione, e sulla gestione delle licenze di Windows.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-234">Forced tunneling can impact connectivity to Azure services (the Storage Service, for example) and the Windows license manager.</span></span>
> 
> 


## <a name="troubleshooting"></a><span data-ttu-id="9fdc9-235">risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="9fdc9-235">Troubleshooting</span></span> 

<span data-ttu-id="9fdc9-236">Per informazioni generali sulla risoluzione dei problemi relativi a errori comuni correlati alla VPN, vedere [Troubleshooting common VPN related errors (Risoluzione dei problemi relativi a errori comuni correlati alla VPN)][troubleshooting-vpn-errors].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-236">For general information on troubleshooting common VPN-related errors, see [Troubleshooting common VPN related errors][troubleshooting-vpn-errors].</span></span>

<span data-ttu-id="9fdc9-237">Le raccomandazioni seguenti sono utili per determinare se l'appliance VPN locale funziona correttamente.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-237">The following recommendations are useful for determining if your on-premises VPN appliance is functioning correctly.</span></span>

- <span data-ttu-id="9fdc9-238">**Controllare la presenza di problemi o errori nei file di log generati dall'appliance VPN.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-238">**Check any log files generated by the VPN appliance for errors or failures.**</span></span>

    <span data-ttu-id="9fdc9-239">Ciò consentirà di determinare se l'appliance VPN funziona correttamente.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-239">This will help you determine if the VPN appliance is functioning correctly.</span></span> <span data-ttu-id="9fdc9-240">La posizione di tali informazioni varia in base all'appliance.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-240">The location of this information will vary according to your appliance.</span></span> <span data-ttu-id="9fdc9-241">Se ad esempio si usa RRAS in Windows Server 2012, è possibile usare il comando PowerShell seguente per visualizzare informazioni sugli eventi di errore per il servizio RRAS:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-241">For example, if you are using RRAS on Windows Server 2012, you can use the following PowerShell command to display error event information for the RRAS service:</span></span>

    ```PowerShell
    Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
    ```

    <span data-ttu-id="9fdc9-242">La proprietà *Message* di ciascuna voce offre una descrizione dell'errore.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-242">The *Message* property of each entry provides a description of the error.</span></span> <span data-ttu-id="9fdc9-243">Di seguito sono riportati alcuni esempi comuni:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-243">Some common examples are:</span></span>

        - Inability to connect, possibly due to an incorrect IP address specified for the Azure VPN gateway in the RRAS VPN network interface configuration.

        ```
        EventID            : 20111
        MachineName        : on-prem-vm
        Data               : {41, 3, 0, 0}
        Index              : 14231
        Category           : (0)
        CategoryNumber     : 0
        EntryType          : Error
        Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A demand dial connection to the remote
                             interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                             successfully because of the  following error: The network connection between your computer and
                             the VPN server could not be established because the remote server is not responding. This could
                             be because one of the network devices (for example, firewalls, NAT, routers, and so on) between your computer
                             and the remote server is not configured to allow VPN connections. Please contact your
                             Administrator or your service provider to determine which device may be causing the problem.
        Source             : RemoteAccess
        ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                             your computer and the VPN server could not be established because the remote server is not
                             responding. This could be because one of the network devices (for example, firewalls, NAT, routers, and so on)
                             between your computer and the remote server is not configured to allow VPN connections. Please
                             contact your Administrator or your service provider to determine which device may be causing the
                             problem.}
        InstanceId         : 20111
        TimeGenerated      : 3/18/2016 1:26:02 PM
        TimeWritten        : 3/18/2016 1:26:02 PM
        UserName           :
        Site               :
        Container          :
        ```

        - The wrong shared key being specified in the RRAS VPN network interface configuration.

        ```
        EventID            : 20111
        MachineName        : on-prem-vm
        Data               : {233, 53, 0, 0}
        Index              : 14245
        Category           : (0)
        CategoryNumber     : 0
        EntryType          : Error
        Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A demand dial connection to the remote
                             interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                             successfully because of the  following error: Internet key exchange (IKE) authentication credentials are unacceptable.

        Source             : RemoteAccess
        ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                             unacceptable.
                             }
        InstanceId         : 20111
        TimeGenerated      : 3/18/2016 1:34:22 PM
        TimeWritten        : 3/18/2016 1:34:22 PM
        UserName           :
        Site               :
        Container          :
        ```

    <span data-ttu-id="9fdc9-244">È anche possibile usare il comando PowerShell seguente per ottenere informazioni del log eventi sui tentativi di connessione tramite il servizio RRAS:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-244">You can also obtain event log information about attempts to connect through the RRAS service using the following PowerShell command:</span></span> 

    ```
    Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
    ```

    <span data-ttu-id="9fdc9-245">In caso di un errore di connessione, questo log conterrà errori simili al seguente:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-245">In the event of a failure to connect, this log will contain errors that look similar to the following:</span></span>

    ```
    EventID            : 20227
    MachineName        : on-prem-vm
    Data               : {}
    Index              : 4203
    Category           : (0)
    CategoryNumber     : 0
    EntryType          : Error
    Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                         AzureGateway that has failed. The error code returned on failure is 809.
    Source             : RasClient
    ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
    InstanceId         : 20227
    TimeGenerated      : 3/18/2016 1:29:21 PM
    TimeWritten        : 3/18/2016 1:29:21 PM
    UserName           :
    Site               :
    Container          :
    ```

- <span data-ttu-id="9fdc9-246">**Verificare la connettività e il routing attraverso il gateway VPN.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-246">**Verify connectivity and routing across the VPN gateway.**</span></span>

    <span data-ttu-id="9fdc9-247">L'appliance VPN potrebbe non essere in grado di eseguire correttamente il routing del traffico attraverso Gateway VPN di Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-247">The VPN appliance may not be correctly routing traffic through the Azure VPN Gateway.</span></span> <span data-ttu-id="9fdc9-248">Usare uno strumento come [PsPing][psping] per verificare la connettività e il routing attraverso il gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-248">Use a tool such as [PsPing][psping] to verify connectivity and routing across the VPN gateway.</span></span> <span data-ttu-id="9fdc9-249">Per eseguire il test della connettività da un computer locale a un server Web situato nella rete virtuale, usare ad esempio questo comando (sostituendo `<<web-server-address>>` con l'indirizzo del server Web):</span><span class="sxs-lookup"><span data-stu-id="9fdc9-249">For example, to test connectivity from an on-premises machine to a web server located on the VNet, run the following command (replacing `<<web-server-address>>` with the address of the web server):</span></span>

    ```
    PsPing -t <<web-server-address>>:80
    ```

    <span data-ttu-id="9fdc9-250">Se il computer locale può eseguire il routing del traffico verso il server Web, è possibile che venga visualizzato un output simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-250">If the on-premises machine can route traffic to the web server, you should see output similar to the following:</span></span>

    ```
    D:\PSTools>psping -t 10.20.0.5:80

    PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2014 Mark Russinovich
    Sysinternals - www.sysinternals.com

    TCP connect to 10.20.0.5:80:
    Infinite iterations (warmup 1) connecting test:
    Connecting to 10.20.0.5:80 (warmup): 6.21ms
    Connecting to 10.20.0.5:80: 3.79ms
    Connecting to 10.20.0.5:80: 3.44ms
    Connecting to 10.20.0.5:80: 4.81ms

      Sent = 3, Received = 3, Lost = 0 (0% loss),
      Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
    ```

    <span data-ttu-id="9fdc9-251">Se il computer locale non può comunicare con la destinazione specificata, si visualizzeranno messaggi simili al seguente:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-251">If the on-premises machine cannot communicate with the specified destination, you will see messages like this:</span></span>

    ```
    D:\PSTools>psping -t 10.20.1.6:80

    PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2014 Mark Russinovich
    Sysinternals - www.sysinternals.com

    TCP connect to 10.20.1.6:80:
    Infinite iterations (warmup 1) connecting test:
    Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
    Connecting to 10.20.1.6:80:
      Sent = 3, Received = 0, Lost = 3 (100% loss),
      Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
    ```

- <span data-ttu-id="9fdc9-252">**Verificare che il firewall locale consenta il passaggio del traffico VPN e che le porte appropriate siano aperte.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-252">**Verify that the on-premises firewall allows VPN traffic to pass and that the correct ports are opened.**</span></span>

- <span data-ttu-id="9fdc9-253">**Verificare che l'appliance VPN locale usi un metodo di crittografia [compatibile con il gateway VPN di Azure][vpn-appliance].**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-253">**Verify that the on-premises VPN appliance uses an encryption method that is [compatible with the Azure VPN gateway][vpn-appliance].**</span></span> <span data-ttu-id="9fdc9-254">Per il routing basato su criteri, il gateway VPN di Azure supporta gli algoritmi di crittografia AES256, AES128 e 3DES.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-254">For policy-based routing, the Azure VPN gateway supports the AES256, AES128, and 3DES encryption algorithms.</span></span> <span data-ttu-id="9fdc9-255">I gateway basati su route supportano AES256 e 3DES.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-255">Route-based gateways support AES256 and 3DES.</span></span>

<span data-ttu-id="9fdc9-256">Le raccomandazioni seguenti sono utili per determinare se si è verificato un problema con il gateway VPN di Azure:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-256">The following recommendations are useful for determining if there is a problem with the Azure VPN gateway:</span></span>

- <span data-ttu-id="9fdc9-257">**Esaminare i [log di diagnostica del gateway VPN di Azure][gateway-diagnostic-logs] al fine di individuare potenziali problemi.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-257">**Examine [Azure VPN gateway diagnostic logs][gateway-diagnostic-logs] for potential issues.**</span></span>

- <span data-ttu-id="9fdc9-258">**Verificare che il gateway VPN di Azure e l'appliance VPN locale siano configurati con la stessa chiave di autenticazione condivisa.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-258">**Verify that the Azure VPN gateway and on-premises VPN appliance are configured with the same shared authentication key.**</span></span>

    <span data-ttu-id="9fdc9-259">È possibile visualizzare la chiave condivisa archiviata dal gateway VPN di Azure usando questo comando dell'interfaccia della riga di comando di Azure:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-259">You can view the shared key stored by the Azure VPN gateway using the following Azure CLI command:</span></span>

    ```
    azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
    ```

    <span data-ttu-id="9fdc9-260">Usare il comando appropriato per l'appliance VPN locale per visualizzare la chiave condivisa configurata per l'appliance.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-260">Use the command appropriate for your on-premises VPN appliance to show the shared key configured for that appliance.</span></span>

    <span data-ttu-id="9fdc9-261">Verificare che la subnet *GatewaySubnet* che contiene il gateway VPN di Azure non sia associata a un gruppo di sicurezza di rete (NSG).</span><span class="sxs-lookup"><span data-stu-id="9fdc9-261">Verify that the *GatewaySubnet* subnet holding the Azure VPN gateway is not associated with an NSG.</span></span>

    <span data-ttu-id="9fdc9-262">È possibile visualizzare i dettagli della subnet usando questo comando dell'interfaccia della riga di comando di Azure:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-262">You can view the subnet details using the following Azure CLI command:</span></span>

    ```
    azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
    ```

    <span data-ttu-id="9fdc9-263">Verificare che non sia presente alcun campo dati denominato *ID gruppo di sicurezza di rete*. L'esempio seguente mostra i risultati di un'istanza di *GatewaySubnet* a cui è assegnato un gruppo di sicurezza di rete (*VPN-Gateway-Group*).</span><span class="sxs-lookup"><span data-stu-id="9fdc9-263">Ensure there is no data field named *Network Security Group id*. The following example shows the results for an instance of the *GatewaySubnet* that has an assigned NSG (*VPN-Gateway-Group*).</span></span> <span data-ttu-id="9fdc9-264">Ciò può impedire il corretto funzionamento del gateway qualora siano definite eventuali regole per questo gruppo di sicurezza di rete.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-264">This can prevent the gateway from working correctly if there are any rules defined for this NSG.</span></span>

    ```
    C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
        info:    Executing command network vnet subnet show
        + Looking up virtual network "profx-vnet"
        + Looking up the subnet "GatewaySubnet"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
        data:    Name                            : GatewaySubnet
        data:    Provisioning state              : Succeeded
        data:    Address prefix                  : 10.20.3.0/27
        data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
        info:    network vnet subnet show command OK
    ```

- <span data-ttu-id="9fdc9-265">**Verificare che le macchine virtuali nella rete virtuale di Azure siano configurate per consentire il traffico in arrivo dall'esterno della rete virtuale.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-265">**Verify that the virtual machines in the Azure VNet are configured to permit traffic coming in from outside the VNet.**</span></span>

    <span data-ttu-id="9fdc9-266">Controllare eventuali regole del gruppo di sicurezza di rete associate alle subnet contenenti queste macchine virtuali.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-266">Check any NSG rules associated with subnets containing these virtual machines.</span></span> <span data-ttu-id="9fdc9-267">È possibile visualizzare tutte le regole del gruppo di sicurezza di rete usando questo comando dell'interfaccia della riga di comando di Azure:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-267">You can view all NSG rules using the following Azure CLI command:</span></span>

    ```
    azure network nsg show -g <<resource-group>> -n <<nsg-name>>
    ```

- <span data-ttu-id="9fdc9-268">**Verificare che il gateway VPN di Azure sia connesso.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-268">**Verify that the Azure VPN gateway is connected.**</span></span>

    <span data-ttu-id="9fdc9-269">È possibile usare il comando di Azure PowerShell seguente per controllare lo stato corrente della connessione VPN di Azure.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-269">You can use the following Azure PowerShell command to check the current status of the Azure VPN connection.</span></span> <span data-ttu-id="9fdc9-270">Il parametro `<<connection-name>>` è il nome della connessione VPN di Azure che collega il gateway di rete virtuale e il gateway locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-270">The `<<connection-name>>` parameter is the name of the Azure VPN connection that links the virtual network gateway and the local gateway.</span></span>

    ```
    Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
    ```

    <span data-ttu-id="9fdc9-271">I frammenti di codice seguenti mostrano l'output generato se il gateway è connesso (nel primo esempio) e disconnesso (nel secondo esempio):</span><span class="sxs-lookup"><span data-stu-id="9fdc9-271">The following snippets highlight the output generated if the gateway is connected (the first example), and disconnected (the second example):</span></span>

    ```
    PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     :
    LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
    Peer                       :
    ConnectionType             : IPsec
    RoutingWeight              : 0
    SharedKey                  : ####################################
    ConnectionStatus           : Connected
    EgressBytesTransferred     : 55254803
    IngressBytesTransferred    : 32227221
    ProvisioningState          : Succeeded
    ...
    ```

    ```
    PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     :
    LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
    Peer                       :
    ConnectionType             : IPsec
    RoutingWeight              : 0
    SharedKey                  : ####################################
    ConnectionStatus           : NotConnected
    EgressBytesTransferred     : 0
    IngressBytesTransferred    : 0
    ProvisioningState          : Succeeded
    ...
    ```

<span data-ttu-id="9fdc9-272">Le raccomandazioni seguenti sono utili per determinare se per la macchina virtuale host è presente un problema di configurazione, di utilizzo della larghezza di banda di rete o di prestazioni dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-272">The following recommendations are useful for determining if there is an issue with Host VM configuration, network bandwidth utilization, or application performance:</span></span>

- <span data-ttu-id="9fdc9-273">**Verificare che il firewall nel sistema operativo guest in esecuzione su macchine virtuali di Azure nella subnet sia configurato correttamente per consentire il traffico proveniente dagli intervalli di IP locali.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-273">**Verify that the firewall in the guest operating system running on the Azure VMs in the subnet is configured correctly to allow permitted traffic from the on-premises IP ranges.**</span></span>

- <span data-ttu-id="9fdc9-274">**Verificare che il volume di traffico non sia prossimo al limite della larghezza di banda disponibile per il gateway VPN di Azure.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-274">**Verify that the volume of traffic is not close to the limit of the bandwidth available to the Azure VPN gateway.**</span></span>

    <span data-ttu-id="9fdc9-275">Il modo per verificare tali aspetti varia in base all'appliance VPN eseguita in locale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-275">How to verify this depends on the VPN appliance running on-premises.</span></span> <span data-ttu-id="9fdc9-276">Se ad esempio si usa RRAS in Windows Server 2012, è possibile servirsi di Performance Monitor per tenere traccia del volume dei dati ricevuti e trasmessi tramite una connessione VPN.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-276">For example, if you are using RRAS on Windows Server 2012, you can use Performance Monitor to track the volume of data being received and transmitted over the VPN connection.</span></span> <span data-ttu-id="9fdc9-277">Usando l'oggetto *Totale RAS*, selezionare i contatori *Byte ricevuti/sec* e *Byte trasmessi/sec*:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-277">Using the *RAS Total* object, select the *Bytes Received/Sec* and *Bytes Transmitted/Sec* counters:</span></span>

    <span data-ttu-id="9fdc9-278">![[3]][3]</span><span class="sxs-lookup"><span data-stu-id="9fdc9-278">![[3]][3]</span></span>

    <span data-ttu-id="9fdc9-279">È necessario confrontare i risultati con la larghezza di banda disponibile per il gateway VPN (100 Mbps per gli SKU standard e di base e 200 Mbps per lo SKU a prestazioni elevate):</span><span class="sxs-lookup"><span data-stu-id="9fdc9-279">You should compare the results with the bandwidth available to the VPN gateway (100 Mbps for the Basic and Standard SKUs, and 200 Mbps for the High Performance SKU):</span></span>

    <span data-ttu-id="9fdc9-280">![[4]][4]</span><span class="sxs-lookup"><span data-stu-id="9fdc9-280">![[4]][4]</span></span>

- <span data-ttu-id="9fdc9-281">**Verificare di aver distribuito le dimensioni e il numero corretto di macchine virtuali per il carico dell'applicazione.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-281">**Verify that you have deployed the right number and size of VMs for your application load.**</span></span>

    <span data-ttu-id="9fdc9-282">Determinare la presenza nella rete virtuale di Azure di eventuali macchine virtuali eseguite lentamente.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-282">Determine if any of the virtual machines in the Azure VNet are running slowly.</span></span> <span data-ttu-id="9fdc9-283">In tal caso, è possibile che vi sia un sovraccarico sulle macchine virtuali, che il numero di macchine virtuali sia insufficiente per gestire il carico o che i servizi di bilanciamento del carico non siano configurati correttamente.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-283">If so, they may be overloaded, there may be too few to handle the load, or the load-balancers may not be configured correctly.</span></span> <span data-ttu-id="9fdc9-284">[Acquisire e analizzare le informazioni di diagnostica][azure-vm-diagnostics] per individuare la causa di tale situazione.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-284">To determine this, [capture and analyze diagnostic information][azure-vm-diagnostics].</span></span> <span data-ttu-id="9fdc9-285">È possibile esaminare i risultati tramite il portale di Azure, ma sono disponibili anche molti strumenti di terze parti in grado di fornire informazioni dettagliate sui dati sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-285">You can examine the results using the Azure portal, but many third-party tools are also available that can provide detailed insights into the performance data.</span></span>

- <span data-ttu-id="9fdc9-286">**Verificare che l'applicazione usi le risorse cloud in modo efficiente.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-286">**Verify that the application is making efficient use of cloud resources.**</span></span>

    <span data-ttu-id="9fdc9-287">Instrumentare il codice applicazione in esecuzione in ogni macchina virtuale per determinare se le applicazioni usano le risorse in modo ottimale.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-287">Instrument application code running on each VM to determine whether applications are making the best use of resources.</span></span> <span data-ttu-id="9fdc9-288">È possibile usare strumenti come [Application Insights][application-insights].</span><span class="sxs-lookup"><span data-stu-id="9fdc9-288">You can use tools such as [Application Insights][application-insights].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="9fdc9-289">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="9fdc9-289">Deploy the solution</span></span>


<span data-ttu-id="9fdc9-290">**Prerequisiti.**</span><span class="sxs-lookup"><span data-stu-id="9fdc9-290">**Prequisites.**</span></span> <span data-ttu-id="9fdc9-291">È necessario disporre di un'infrastruttura locale esistente già configurata con un'appliance di rete adatta.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-291">You must have an existing on-premises infrastructure already configured with a suitable network appliance.</span></span>

<span data-ttu-id="9fdc9-292">Per distribuire la soluzione, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-292">To deploy the solution, perform the following steps.</span></span>

1. <span data-ttu-id="9fdc9-293">Fare clic sul pulsante seguente:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-293">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fvpn%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="9fdc9-294">Attendere che il collegamento si apra nel portale di Azure e quindi eseguire questi passaggi:</span><span class="sxs-lookup"><span data-stu-id="9fdc9-294">Wait for the link to open in the Azure portal, then follow these steps:</span></span> 
   * <span data-ttu-id="9fdc9-295">Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-hybrid-vpn-rg` nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-295">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-hybrid-vpn-rg` in the text box.</span></span>
   * <span data-ttu-id="9fdc9-296">Selezionare l'area dalla casella di riepilogo a discesa **Località**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-296">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="9fdc9-297">Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).</span><span class="sxs-lookup"><span data-stu-id="9fdc9-297">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="9fdc9-298">Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-298">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="9fdc9-299">Fare clic sul pulsante **Acquista**.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-299">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="9fdc9-300">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="9fdc9-300">Wait for the deployment to complete.</span></span>



<!-- links -->

[adds-extend-domain]: ../identity/adds-extend-domain.md
[expressroute]: ../hybrid-networking/expressroute.md
[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md

[naming conventions]: /azure/guidance/guidance-naming-conventions

[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[arm-templates]: /azure/resource-group-authoring-templates
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-portal]: /azure/azure-portal/resource-group-portal
[azure-powershell]: /azure/powershell-azure-resource-manager
[azure-virtual-network]: /azure/virtual-network/virtual-networks-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-gateway-skus]: /azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: /azure/vpn-gateway/vpn-gateway-multi-site
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: /azure/virtual-network/virtual-networks-nsg
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_2/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: /azure/application-insights/app-insights-overview-usage
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[vpn-appliances]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[vpn-appliance-ipsec]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec-parameters
[virtualNetworkGateway-parameters]: https://github.com/mspnp/hybrid-networking/vpn/parameters/virtualNetworkGateway.parameters.json
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[0]: ./images/vpn.png "Rete ibrida che si estende in locale e nelle infrastrutture di Azure"
[2]: ../_images/guidance-hybrid-network-vpn/audit-logs.png "Log di controllo nel portale di Azure"
[3]: ../_images/guidance-hybrid-network-vpn/RRAS-perf-counters.png "Contatori delle prestazioni per il monitoraggio del traffico di rete VPN"
[4]: ../_images/guidance-hybrid-network-vpn/RRAS-perf-graph.png "Esempio di grafico delle prestazioni della rete VPN"
