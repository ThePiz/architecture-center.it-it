---
title: "Implementazione di un'architettura di rete ibrida a disponibilità elevata"
description: Come implementare un'architettura di rete sicura da sito a sito, che si estende su una rete virtuale di Azure e su una rete locale connessa tramite ExpressRoute con failover del gateway VPN.
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Connect an on-premises network to Azure
pnp.series.prev: expressroute
cardTitle: Improving availability
ms.openlocfilehash: 4c101f17e5e91085b61178f9efb2bc5acb61189c
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="connect-an-on-premises-network-to-azure-using-expressroute-with-vpn-failover"></a><span data-ttu-id="f9f09-103">Connettere una rete locale ad Azure tramite ExpressRoute con failover VPN</span><span class="sxs-lookup"><span data-stu-id="f9f09-103">Connect an on-premises network to Azure using ExpressRoute with VPN failover</span></span>

<span data-ttu-id="f9f09-104">Questa architettura di riferimento illustra come connettere una rete locale a una rete virtuale di Azure (VNet) usando ExpressRoute, tramite una rete privata virtuale da sito a sito (VPN), che funge da connessione di failover.</span><span class="sxs-lookup"><span data-stu-id="f9f09-104">This reference architecture shows how to connect an on-premises network to an Azure virtual network (VNet) using ExpressRoute, with a site-to-site virtual private network (VPN) as a failover connection.</span></span> <span data-ttu-id="f9f09-105">Flussi di traffico tra la rete locale e la rete virtuale di Azure tramite una connessione ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="f9f09-105">Traffic flows between the on-premises network and the Azure VNet through an ExpressRoute connection.</span></span> <span data-ttu-id="f9f09-106">Se si verifica una perdita di connettività del circuito ExpressRoute, il traffico viene indirizzato attraverso un tunnel per VPN IPSec.</span><span class="sxs-lookup"><span data-stu-id="f9f09-106">If there is a loss of connectivity in the ExpressRoute circuit, traffic is routed through an IPSec VPN tunnel.</span></span> [<span data-ttu-id="f9f09-107">**Distribuire questa soluzione**.</span><span class="sxs-lookup"><span data-stu-id="f9f09-107">**Deploy this solution**.</span></span>](#deploy-the-solution)

<span data-ttu-id="f9f09-108">Si noti che se il circuito ExpressRoute non è disponibile, la route VPN gestisce solo le connessioni di peering private.</span><span class="sxs-lookup"><span data-stu-id="f9f09-108">Note that if the ExpressRoute circuit is unavailable, the VPN route will only handle private peering connections.</span></span> <span data-ttu-id="f9f09-109">Le connessioni di peering pubblico e peering Microsoft passano attraverso Internet.</span><span class="sxs-lookup"><span data-stu-id="f9f09-109">Public peering and Microsoft peering connections will pass over the Internet.</span></span> 

<span data-ttu-id="f9f09-110">![[0]][0]</span><span class="sxs-lookup"><span data-stu-id="f9f09-110">![[0]][0]</span></span>

<span data-ttu-id="f9f09-111">*Scaricare un [file di Visio][visio-download] di questa architettura.*</span><span class="sxs-lookup"><span data-stu-id="f9f09-111">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="f9f09-112">Architettura</span><span class="sxs-lookup"><span data-stu-id="f9f09-112">Architecture</span></span> 

<span data-ttu-id="f9f09-113">L'architettura è costituita dai componenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="f9f09-113">The architecture consists of the following components.</span></span>

* <span data-ttu-id="f9f09-114">**Rete locale**:</span><span class="sxs-lookup"><span data-stu-id="f9f09-114">**On-premises network**.</span></span> <span data-ttu-id="f9f09-115">una rete LAN privata in esecuzione all'interno di un'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="f9f09-115">A private local-area network running within an organization.</span></span>

* <span data-ttu-id="f9f09-116">**Appliance VPN**:</span><span class="sxs-lookup"><span data-stu-id="f9f09-116">**VPN appliance**.</span></span> <span data-ttu-id="f9f09-117">un dispositivo o un servizio che offre connettività esterna alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="f9f09-117">A device or service that provides external connectivity to the on-premises network.</span></span> <span data-ttu-id="f9f09-118">L'appliance VPN può essere un dispositivo hardware o una soluzione software come il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012.</span><span class="sxs-lookup"><span data-stu-id="f9f09-118">The VPN appliance may be a hardware device, or it can be a software solution such as the Routing and Remote Access Service (RRAS) in Windows Server 2012.</span></span> <span data-ttu-id="f9f09-119">Per una lista delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN e sui parametri IPsec/IKE per connessioni del Gateway VPN da sito a sito][vpn-appliance].</span><span class="sxs-lookup"><span data-stu-id="f9f09-119">For a list of supported VPN appliances and information on configuring selected VPN appliances for connecting to Azure, see [About VPN devices for Site-to-Site VPN Gateway connections][vpn-appliance].</span></span>

* <span data-ttu-id="f9f09-120">**Circuito ExpressRoute**:</span><span class="sxs-lookup"><span data-stu-id="f9f09-120">**ExpressRoute circuit**.</span></span> <span data-ttu-id="f9f09-121">un circuito di livello 2 o di livello 3 fornito dal provider di connettività che unisce la rete locale ad Azure attraverso i router perimetrali.</span><span class="sxs-lookup"><span data-stu-id="f9f09-121">A layer 2 or layer 3 circuit supplied by the connectivity provider that joins the on-premises network with Azure through the edge routers.</span></span> <span data-ttu-id="f9f09-122">Il circuito usa l'infrastruttura hardware gestita dal provider di connettività.</span><span class="sxs-lookup"><span data-stu-id="f9f09-122">The circuit uses the hardware infrastructure managed by the connectivity provider.</span></span>

* <span data-ttu-id="f9f09-123">**Gateway di rete virtuale per ExpressRoute**:</span><span class="sxs-lookup"><span data-stu-id="f9f09-123">**ExpressRoute virtual network gateway**.</span></span> <span data-ttu-id="f9f09-124">il gateway di rete virtuale per ExpressRoute consente di connettere la rete virtuale al circuito ExpressRoute, usato per la connettività, con la rete locale.</span><span class="sxs-lookup"><span data-stu-id="f9f09-124">The ExpressRoute virtual network gateway enables the VNet to connect to the ExpressRoute circuit used for connectivity with your on-premises network.</span></span>

* <span data-ttu-id="f9f09-125">**Gateway di rete virtuale VPN**:</span><span class="sxs-lookup"><span data-stu-id="f9f09-125">**VPN virtual network gateway**.</span></span> <span data-ttu-id="f9f09-126">il gateway di rete virtuale VPN consente di connettere la rete virtuale all'appliance VPN nella rete locale.</span><span class="sxs-lookup"><span data-stu-id="f9f09-126">The VPN virtual network gateway enables the VNet to connect to the VPN appliance in the on-premises network.</span></span> <span data-ttu-id="f9f09-127">Il gateway di rete virtuale VPN è configurato per accettare le richieste provenienti dalla rete locale solo tramite l'appliance VPN.</span><span class="sxs-lookup"><span data-stu-id="f9f09-127">The VPN virtual network gateway is configured to accept requests from the on-premises network only through the VPN appliance.</span></span> <span data-ttu-id="f9f09-128">Per maggiori informazioni, consultare [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].</span><span class="sxs-lookup"><span data-stu-id="f9f09-128">For more information, see [Connect an on-premises network to a Microsoft Azure virtual network][connect-to-an-Azure-vnet].</span></span>

* <span data-ttu-id="f9f09-129">**Connessione VPN**:</span><span class="sxs-lookup"><span data-stu-id="f9f09-129">**VPN connection**.</span></span> <span data-ttu-id="f9f09-130">la connessione ha proprietà che specificano il tipo di connessione (IPSec) e la chiave condivisa con l'appliance VPN locale per crittografare il traffico.</span><span class="sxs-lookup"><span data-stu-id="f9f09-130">The connection has properties that specify the connection type (IPSec) and the key shared with the on-premises VPN appliance to encrypt traffic.</span></span>

* <span data-ttu-id="f9f09-131">**Rete virtuale di Azure (VNet)**:</span><span class="sxs-lookup"><span data-stu-id="f9f09-131">**Azure Virtual Network (VNet)**.</span></span> <span data-ttu-id="f9f09-132">ogni rete virtuale si trova in una singola area di Azure e può contenere più livelli applicazione.</span><span class="sxs-lookup"><span data-stu-id="f9f09-132">Each VNet resides in a single Azure region, and can host multiple application tiers.</span></span> <span data-ttu-id="f9f09-133">I livelli dell'applicazione possono essere segmentati usando subnet in ogni rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="f9f09-133">Application tiers can be segmented using subnets in each VNet.</span></span>

* <span data-ttu-id="f9f09-134">**Subnet del gateway**:</span><span class="sxs-lookup"><span data-stu-id="f9f09-134">**Gateway subnet**.</span></span> <span data-ttu-id="f9f09-135">i gateway di rete virtuale vengono mantenuti nella stessa subnet.</span><span class="sxs-lookup"><span data-stu-id="f9f09-135">The virtual network gateways are held in the same subnet.</span></span>

* <span data-ttu-id="f9f09-136">**Applicazione cloud**:</span><span class="sxs-lookup"><span data-stu-id="f9f09-136">**Cloud application**.</span></span> <span data-ttu-id="f9f09-137">l'applicazione contenuta in Azure.</span><span class="sxs-lookup"><span data-stu-id="f9f09-137">The application hosted in Azure.</span></span> <span data-ttu-id="f9f09-138">Può includere più livelli, con più subnet, connesse tramite i servizi di bilanciamento del carico di Azure.</span><span class="sxs-lookup"><span data-stu-id="f9f09-138">It might include multiple tiers, with multiple subnets connected through Azure load balancers.</span></span> <span data-ttu-id="f9f09-139">Per maggiori informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] e [Esecuzione di carichi di lavoro della macchina virtuale di Linux][linux-vm-ra].</span><span class="sxs-lookup"><span data-stu-id="f9f09-139">For more information about the application infrastructure, see [Running Windows VM workloads][windows-vm-ra] and [Running Linux VM workloads][linux-vm-ra].</span></span>

## <a name="recommendations"></a><span data-ttu-id="f9f09-140">Raccomandazioni</span><span class="sxs-lookup"><span data-stu-id="f9f09-140">Recommendations</span></span>

<span data-ttu-id="f9f09-141">Le raccomandazioni seguenti sono valide per la maggior parte degli scenari.</span><span class="sxs-lookup"><span data-stu-id="f9f09-141">The following recommendations apply for most scenarios.</span></span> <span data-ttu-id="f9f09-142">Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.</span><span class="sxs-lookup"><span data-stu-id="f9f09-142">Follow these recommendations unless you have a specific requirement that overrides them.</span></span>

### <a name="vnet-and-gatewaysubnet"></a><span data-ttu-id="f9f09-143">Rete virtuali e GatewaySubnet</span><span class="sxs-lookup"><span data-stu-id="f9f09-143">VNet and GatewaySubnet</span></span>

<span data-ttu-id="f9f09-144">Creare il gateway di rete virtuale per ExpressRoute e il gateway di rete virtuale VPN nella stessa rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="f9f09-144">Create the ExpressRoute virtual network gateway and the VPN virtual network gateway in the same VNet.</span></span> <span data-ttu-id="f9f09-145">Ciò significa che condividono la stessa subnet denominata *GatewaySubnet*.</span><span class="sxs-lookup"><span data-stu-id="f9f09-145">This means that they should share the same subnet named *GatewaySubnet*.</span></span>

<span data-ttu-id="f9f09-146">Se la rete virtuale include già una subnet denominata *GatewaySubnet*, verificare che abbia uno spazio di indirizzi /27 o maggiore.</span><span class="sxs-lookup"><span data-stu-id="f9f09-146">If the VNet already includes a subnet named *GatewaySubnet*, ensure that it has a /27 or larger address space.</span></span> <span data-ttu-id="f9f09-147">Se la subnet esistente è troppo piccola, usare il comando PowerShell seguente per rimuoverla:</span><span class="sxs-lookup"><span data-stu-id="f9f09-147">If the existing subnet is too small, use the following PowerShell command to remove the subnet:</span></span> 

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

<span data-ttu-id="f9f09-148">Se la rete virtuale non contiene una subnet denominata **GatewaySubnet**, crearne una nuova usando il comando Powershell seguente:</span><span class="sxs-lookup"><span data-stu-id="f9f09-148">If the VNet does not contain a subnet named **GatewaySubnet**, create a new one using the following Powershell command:</span></span>

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a><span data-ttu-id="f9f09-149">VPN e gateway ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="f9f09-149">VPN and ExpressRoute gateways</span></span>

<span data-ttu-id="f9f09-150">Verificare che l'organizzazione sia in linea con i [Prerequisiti di ExpressRoute][expressroute-prereq] per connettersi ad Azure.</span><span class="sxs-lookup"><span data-stu-id="f9f09-150">Verify that your organization meets the [ExpressRoute prerequisite requirements][expressroute-prereq] for connecting to Azure.</span></span>

<span data-ttu-id="f9f09-151">Se si dispone già di un gateway di rete virtuale VPN nella rete virtuale di Azure, usare il comando Powershell seguente per rimuoverlo:</span><span class="sxs-lookup"><span data-stu-id="f9f09-151">If you already have a VPN virtual network gateway in your Azure VNet, use the following  Powershell command to remove it:</span></span>

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

<span data-ttu-id="f9f09-152">Seguire le istruzioni in [Implementing a hybrid network architecture with Azure ExpressRoute][implementing-expressroute] (Implementazione di un'architettura di rete ibrida con Azure ExpressRoute) per stabilire la connessione ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="f9f09-152">Follow the instructions in [Implementing a hybrid network architecture with Azure ExpressRoute][implementing-expressroute] to establish your ExpressRoute connection.</span></span>

<span data-ttu-id="f9f09-153">Seguire le istruzioni in [Implementing a hybrid network architecture with Azure and On-premises VPN][implementing-vpn] (Implementazione di un'architettura di rete ibrida con Azure e con la rete VPN locale) per stabilire la connessione del gateway di rete virtuale VPN.</span><span class="sxs-lookup"><span data-stu-id="f9f09-153">Follow the instructions in [Implementing a hybrid network architecture with Azure and On-premises VPN][implementing-vpn] to establish your VPN virtual network gateway connection.</span></span>

<span data-ttu-id="f9f09-154">Dopo avere stabilito le connessioni del gateway di rete virtuale, verificare l'ambiente come segue:</span><span class="sxs-lookup"><span data-stu-id="f9f09-154">After you have established the virtual network gateway connections, test the environment as follows:</span></span>

1. <span data-ttu-id="f9f09-155">Verificare che sia possibile connettersi dalla rete locale a quella virtuale di Azure.</span><span class="sxs-lookup"><span data-stu-id="f9f09-155">Make sure you can connect from your on-premises network to your Azure VNet.</span></span>
2. <span data-ttu-id="f9f09-156">Contattare il provider per arrestare la connettività a ExpressRoute per la verifica.</span><span class="sxs-lookup"><span data-stu-id="f9f09-156">Contact your provider to stop ExpressRoute connectivity for testing.</span></span>
3. <span data-ttu-id="f9f09-157">Verificare che sia ancora possibile connettersi dalla rete locale alla rete virtuale di Azure tramite la connessione del gateway di rete virtuale VPN.</span><span class="sxs-lookup"><span data-stu-id="f9f09-157">Verify that you can still connect from your on-premises network to your Azure VNet using the VPN virtual network gateway connection.</span></span>
4. <span data-ttu-id="f9f09-158">Contattare il provider per ristabilire la connettività a ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="f9f09-158">Contact your provider to reestablish ExpressRoute connectivity.</span></span>

## <a name="considerations"></a><span data-ttu-id="f9f09-159">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="f9f09-159">Considerations</span></span>

<span data-ttu-id="f9f09-160">Per considerazioni su ExpressRoute, vedere il materiale sussidiario [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute] (Implementazione di un'architettura di rete ibrida con Azure ExpressRoute).</span><span class="sxs-lookup"><span data-stu-id="f9f09-160">For ExpressRoute considerations, see the [Implementing a Hybrid Network Architecture with Azure ExpressRoute][guidance-expressroute] guidance.</span></span>

<span data-ttu-id="f9f09-161">Per considerazioni su VPN da sito a sito, vedere il materiale sussidiario [Implementing a hybrid network architecture with Azure and On-premises VPN][guidance-vpn] (Implementazione di un'architettura di rete ibrida con Azure e con la rete VPN locale).</span><span class="sxs-lookup"><span data-stu-id="f9f09-161">For site-to-site VPN considerations, see the [Implementing a Hybrid Network Architecture with Azure and On-premises VPN][guidance-vpn] guidance.</span></span>

<span data-ttu-id="f9f09-162">Per considerazioni sulla sicurezza generale di Azure, vedere [Servizi cloud Microsoft e sicurezza di rete][best-practices-security].</span><span class="sxs-lookup"><span data-stu-id="f9f09-162">For general Azure security considerations, see [Microsoft cloud services and network security][best-practices-security].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="f9f09-163">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="f9f09-163">Deploy the solution</span></span>

<span data-ttu-id="f9f09-164">**Prerequisiti.**</span><span class="sxs-lookup"><span data-stu-id="f9f09-164">**Prequisites.**</span></span> <span data-ttu-id="f9f09-165">È necessario disporre di un'infrastruttura locale esistente già configurata con un'appliance di rete adatto.</span><span class="sxs-lookup"><span data-stu-id="f9f09-165">You must have an existing on-premises infrastructure already configured with a suitable network appliance.</span></span>

<span data-ttu-id="f9f09-166">Per distribuire la soluzione, seguire questa procedura.</span><span class="sxs-lookup"><span data-stu-id="f9f09-166">To deploy the solution, perform the following steps.</span></span>

1. <span data-ttu-id="f9f09-167">Fare clic sul pulsante seguente:</span><span class="sxs-lookup"><span data-stu-id="f9f09-167">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute-vpn-failover%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="f9f09-168">Attendere che il collegamento si apra nel Portale di Azure, successivamente eseguire i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f9f09-168">Wait for the link to open in the Azure portal, then follow these steps:</span></span>   
   * <span data-ttu-id="f9f09-169">Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-hybrid-vpn-er-rg` nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="f9f09-169">The **Resource group** name is already defined in the parameter file, so select **Create New** and enter `ra-hybrid-vpn-er-rg` in the text box.</span></span>
   * <span data-ttu-id="f9f09-170">Selezionare l'area dalla casella di riepilogo a discesa **Località**.</span><span class="sxs-lookup"><span data-stu-id="f9f09-170">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="f9f09-171">Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).</span><span class="sxs-lookup"><span data-stu-id="f9f09-171">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="f9f09-172">Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.</span><span class="sxs-lookup"><span data-stu-id="f9f09-172">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="f9f09-173">Fare clic sul pulsante **Acquista**.</span><span class="sxs-lookup"><span data-stu-id="f9f09-173">Click the **Purchase** button.</span></span>
3. <span data-ttu-id="f9f09-174">Attendere il completamento della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="f9f09-174">Wait for the deployment to complete.</span></span>
4. <span data-ttu-id="f9f09-175">Fare clic sul pulsante seguente:</span><span class="sxs-lookup"><span data-stu-id="f9f09-175">Click the button below:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute-vpn-failover%2Fazuredeploy-expressRouteCircuit.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
5. <span data-ttu-id="f9f09-176">Attendere che il collegamento si apra nel Portale di Azure, successivamente premere Invio ed eseguire i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f9f09-176">Wait for the link to open in the Azure portal, then enter then follow these steps:</span></span>
   * <span data-ttu-id="f9f09-177">Selezionare **Usa esistente** nel **Gruppo di risorse** e immettere `ra-hybrid-vpn-er-rg` nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="f9f09-177">Select **Use existing** in the **Resource group** section and enter `ra-hybrid-vpn-er-rg` in the text box.</span></span>
   * <span data-ttu-id="f9f09-178">Selezionare l'area dalla casella di riepilogo a discesa **Località**.</span><span class="sxs-lookup"><span data-stu-id="f9f09-178">Select the region from the **Location** drop down box.</span></span>
   * <span data-ttu-id="f9f09-179">Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).</span><span class="sxs-lookup"><span data-stu-id="f9f09-179">Do not edit the **Template Root Uri** or the **Parameter Root Uri** text boxes.</span></span>
   * <span data-ttu-id="f9f09-180">Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.</span><span class="sxs-lookup"><span data-stu-id="f9f09-180">Review the terms and conditions, then click the **I agree to the terms and conditions stated above** checkbox.</span></span>
   * <span data-ttu-id="f9f09-181">Fare clic sul pulsante **Acquista**.</span><span class="sxs-lookup"><span data-stu-id="f9f09-181">Click the **Purchase** button.</span></span>

<!-- links -->

[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md


[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[expressroute-prereq]: /azure/expressroute/expressroute-prerequisites
[implementing-expressroute]: ./expressroute.md
[implementing-vpn]: ./vpn.md
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[best-practices-security]: /azure/best-practices-network-security
[visio-download]: https://archcenter.azureedge.net/cdn/hybrid-network-architectures.vsdx
[0]: ./images/expressroute-vpn-failover.png "Struttura di un'architettura di rete ibrida a disponibilità elevata con ExpressRoute e gateway VPN"
