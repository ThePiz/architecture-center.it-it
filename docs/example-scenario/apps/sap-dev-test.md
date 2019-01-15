---
title: Ambienti di sviluppo/test per i carichi di lavoro SAP
titleSuffix: Azure Example Scenarios
description: Creare un ambiente di sviluppo/test per i carichi di lavoro SAP.
author: AndrewDibbins
ms.date: 07/11/2018
ms.custom: fasttrack
ms.openlocfilehash: 9f9e8ec971373e4309703800c200ba2c62fe9a66
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111020"
---
# <a name="devtest-environments-for-sap-workloads-on-azure"></a><span data-ttu-id="c3dfd-103">Ambienti di sviluppo/test per i carichi di lavoro SAP in Azure</span><span class="sxs-lookup"><span data-stu-id="c3dfd-103">Dev/test environments for SAP workloads on Azure</span></span>

<span data-ttu-id="c3dfd-104">Questo esempio mostra come stabilire un ambiente di sviluppo/test per SAP NetWeaver in un ambiente Windows o Linux in Azure.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-104">This example shows how to establish a dev/test environment for SAP NetWeaver in a Windows or Linux environment on Azure.</span></span> <span data-ttu-id="c3dfd-105">Il database usato è AnyDB, che nella terminologia SAP indica qualsiasi DBMS supportato diverso da SAP HANA.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-105">The database used is AnyDB, the SAP term for any supported DBMS (that isn't SAP HANA).</span></span> <span data-ttu-id="c3dfd-106">Dato che è progettata per ambienti non di produzione, l'architettura viene distribuita con una sola macchina virtuale (VM) ed è possibile modificarne le dimensioni in base alle esigenze dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-106">Because this architecture is designed for non-production environments, it's deployed with just a single virtual machine (VM) and it's size can be changed to accommodate your organization's needs.</span></span>

<span data-ttu-id="c3dfd-107">Per casi d'uso di produzione, esaminare le architetture di riferimento SAP disponibili di seguito:</span><span class="sxs-lookup"><span data-stu-id="c3dfd-107">For production use cases review the SAP reference architectures available below:</span></span>

- <span data-ttu-id="c3dfd-108">[SAP NetWeaver per AnyDB][sap-netweaver]</span><span class="sxs-lookup"><span data-stu-id="c3dfd-108">[SAP NetWeaver for AnyDB][sap-netweaver]</span></span>
- <span data-ttu-id="c3dfd-109">[SAP S/4HANA][sap-hana]</span><span class="sxs-lookup"><span data-stu-id="c3dfd-109">[SAP S/4HANA][sap-hana]</span></span>
- <span data-ttu-id="c3dfd-110">[SAP in istanze Large di Azure][sap-large]</span><span class="sxs-lookup"><span data-stu-id="c3dfd-110">[SAP on Azure large instances][sap-large]</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="c3dfd-111">Casi d'uso pertinenti</span><span class="sxs-lookup"><span data-stu-id="c3dfd-111">Relevant use cases</span></span>

<span data-ttu-id="c3dfd-112">Gli altri casi d'uso pertinenti includono:</span><span class="sxs-lookup"><span data-stu-id="c3dfd-112">Other relevant use cases include:</span></span>

- <span data-ttu-id="c3dfd-113">Carichi di lavoro SAP non di produzione e non critici (sandbox, sviluppo, test, controllo di qualità)</span><span class="sxs-lookup"><span data-stu-id="c3dfd-113">Non-critical SAP non-productive workloads (sandbox, development, test, quality assurance)</span></span>
- <span data-ttu-id="c3dfd-114">Carichi di lavoro SAP Business non critici</span><span class="sxs-lookup"><span data-stu-id="c3dfd-114">Non-critical SAP business workloads</span></span>

## <a name="architecture"></a><span data-ttu-id="c3dfd-115">Architettura</span><span class="sxs-lookup"><span data-stu-id="c3dfd-115">Architecture</span></span>

![Diagramma dell'architettura per gli ambienti di sviluppo/test per i carichi di lavoro SAP](./media/architecture-sap-dev-test.png)

<span data-ttu-id="c3dfd-117">Questo scenario illustra il provisioning di un singolo database di sistema SAP e del server applicazioni SAP in una singola macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-117">This scenario demonstrates provisioning a single SAP system database and SAP application server on a single virtual machine.</span></span> <span data-ttu-id="c3dfd-118">Il flusso dei dati nello scenario avviene come segue:</span><span class="sxs-lookup"><span data-stu-id="c3dfd-118">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="c3dfd-119">I clienti usano l'interfaccia utente SAP o altri strumenti client (Excel, un Web browser o un'altra applicazione Web) per accedere al sistema SAP basato su Azure.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-119">Customers use the SAP user interface or other client tools (Excel, a web browser, or other web application) to access the Azure-based SAP system.</span></span>
2. <span data-ttu-id="c3dfd-120">Per la connettività viene usata una connessione ExpressRoute stabilita,</span><span class="sxs-lookup"><span data-stu-id="c3dfd-120">Connectivity is provided through the use of an established ExpressRoute.</span></span> <span data-ttu-id="c3dfd-121">che termina nel gateway ExpressRoute in Azure.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-121">The ExpressRoute connection is terminated in Azure at the ExpressRoute gateway.</span></span> <span data-ttu-id="c3dfd-122">Il traffico di rete viene indirizzato attraverso il gateway ExpressRoute alla subnet del gateway, da questa alla subnet spoke del livello applicazione (vedere la [topologia di rete hub-spoke][hub-spoke]) e quindi tramite un gateway di sicurezza di rete alla macchina virtuale dell'applicazione SAP.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-122">Network traffic routes through the ExpressRoute gateway to the gateway subnet, and from the gateway subnet to the application-tier spoke subnet (see the [hub-spoke network topology][hub-spoke]) and via a Network Security Gateway to the SAP application virtual machine.</span></span>
3. <span data-ttu-id="c3dfd-123">I server di gestione delle identità offrono servizi di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-123">The identity management servers provide authentication services.</span></span>
4. <span data-ttu-id="c3dfd-124">Il jumpbox offre funzionalità di gestione locale.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-124">The jump box provides local management capabilities.</span></span>

### <a name="components"></a><span data-ttu-id="c3dfd-125">Componenti</span><span class="sxs-lookup"><span data-stu-id="c3dfd-125">Components</span></span>

- <span data-ttu-id="c3dfd-126">Le [reti virtuali](/azure/virtual-network/virtual-networks-overview) costituiscono la base delle comunicazioni di rete in Azure.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-126">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) are the basis of network communication within Azure.</span></span>
- <span data-ttu-id="c3dfd-127">Le [macchine virtuali](/azure/virtual-machines/windows/overview) di Azure offrono un'infrastruttura virtualizzata sicura, a scalabilità elevata e su richiesta con Windows o Linux Server.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-127">[Virtual Machine](/azure/virtual-machines/windows/overview) Azure Virtual Machines provides on-demand, high-scale, secure, virtualized infrastructure using Windows or Linux Server.</span></span>
- <span data-ttu-id="c3dfd-128">[ExpressRoute](/azure/expressroute/expressroute-introduction) consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-128">[ExpressRoute](/azure/expressroute/expressroute-introduction) lets you extend your on-premises networks into the Microsoft cloud over a private connection facilitated by a connectivity provider.</span></span>
- <span data-ttu-id="c3dfd-129">Un [gruppo di sicurezza di rete](/azure/virtual-network/security-overview) consente di limitare il traffico di rete verso le risorse in una rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-129">[Network Security Group](/azure/virtual-network/security-overview) lets you limit network traffic to resources in a virtual network.</span></span> <span data-ttu-id="c3dfd-130">Un gruppo di sicurezza di rete contiene un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in ingresso o in uscita in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-130">A network security group contains a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span>
- <span data-ttu-id="c3dfd-131">I [gruppi di risorse](/azure/azure-resource-manager/resource-group-overview#resource-groups) fungono da contenitori logici per le risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-131">[Resource Groups](/azure/azure-resource-manager/resource-group-overview#resource-groups) act as logical containers for Azure resources.</span></span>

## <a name="considerations"></a><span data-ttu-id="c3dfd-132">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="c3dfd-132">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="c3dfd-133">Disponibilità</span><span class="sxs-lookup"><span data-stu-id="c3dfd-133">Availability</span></span>

<span data-ttu-id="c3dfd-134">Microsoft offre un contratto di servizio per le singole istanze di VM.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-134">Microsoft offers a service level agreement (SLA) for single VM instances.</span></span> <span data-ttu-id="c3dfd-135">Per altre informazioni sul contratto di servizio di Microsoft Azure per le macchine virtuali, vedere [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines).</span><span class="sxs-lookup"><span data-stu-id="c3dfd-135">For more information on Microsoft Azure Service Level Agreement for Virtual Machines [SLA For Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span></span>

### <a name="scalability"></a><span data-ttu-id="c3dfd-136">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="c3dfd-136">Scalability</span></span>

<span data-ttu-id="c3dfd-137">Per indicazioni generali sulla progettazione di soluzioni scalabili, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-137">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="c3dfd-138">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="c3dfd-138">Security</span></span>

<span data-ttu-id="c3dfd-139">Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].</span><span class="sxs-lookup"><span data-stu-id="c3dfd-139">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="c3dfd-140">Resilienza</span><span class="sxs-lookup"><span data-stu-id="c3dfd-140">Resiliency</span></span>

<span data-ttu-id="c3dfd-141">Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].</span><span class="sxs-lookup"><span data-stu-id="c3dfd-141">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="c3dfd-142">Prezzi</span><span class="sxs-lookup"><span data-stu-id="c3dfd-142">Pricing</span></span>

<span data-ttu-id="c3dfd-143">Per semplificare il calcolo del costo di esecuzione dello scenario, tutti i servizi sono stati preconfigurati negli esempi del calcolatore dei costi riportati di seguito.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-143">To help you explore the cost of running this scenario, all of the services are pre-configured in the cost calculator examples below.</span></span> <span data-ttu-id="c3dfd-144">Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base</span><span class="sxs-lookup"><span data-stu-id="c3dfd-144">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="c3dfd-145">Sono stati definiti quattro profili di costo di esempio in base alla quantità di traffico prevista:</span><span class="sxs-lookup"><span data-stu-id="c3dfd-145">We have provided four sample cost profiles based on amount of traffic you expect to receive:</span></span>

|<span data-ttu-id="c3dfd-146">Dimensione</span><span class="sxs-lookup"><span data-stu-id="c3dfd-146">Size</span></span>|<span data-ttu-id="c3dfd-147">SAP</span><span class="sxs-lookup"><span data-stu-id="c3dfd-147">SAPs</span></span>|<span data-ttu-id="c3dfd-148">Tipo macchina virtuale</span><span class="sxs-lookup"><span data-stu-id="c3dfd-148">VM Type</span></span>|<span data-ttu-id="c3dfd-149">Archiviazione</span><span class="sxs-lookup"><span data-stu-id="c3dfd-149">Storage</span></span>|<span data-ttu-id="c3dfd-150">Calcolatore prezzi di Azure</span><span class="sxs-lookup"><span data-stu-id="c3dfd-150">Azure Pricing Calculator</span></span>|
|----|----|-------|-------|---------------|
|<span data-ttu-id="c3dfd-151">Piccolo</span><span class="sxs-lookup"><span data-stu-id="c3dfd-151">Small</span></span>|<span data-ttu-id="c3dfd-152">8000</span><span class="sxs-lookup"><span data-stu-id="c3dfd-152">8000</span></span>|<span data-ttu-id="c3dfd-153">D8s_v3</span><span class="sxs-lookup"><span data-stu-id="c3dfd-153">D8s_v3</span></span>|<span data-ttu-id="c3dfd-154">2xP20, 1xP10</span><span class="sxs-lookup"><span data-stu-id="c3dfd-154">2xP20, 1xP10</span></span>|[<span data-ttu-id="c3dfd-155">Small</span><span class="sxs-lookup"><span data-stu-id="c3dfd-155">Small</span></span>](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)|
|<span data-ttu-id="c3dfd-156">Media</span><span class="sxs-lookup"><span data-stu-id="c3dfd-156">Medium</span></span>|<span data-ttu-id="c3dfd-157">16000</span><span class="sxs-lookup"><span data-stu-id="c3dfd-157">16000</span></span>|<span data-ttu-id="c3dfd-158">D16s_v3</span><span class="sxs-lookup"><span data-stu-id="c3dfd-158">D16s_v3</span></span>|<span data-ttu-id="c3dfd-159">3xP20, 1xP10</span><span class="sxs-lookup"><span data-stu-id="c3dfd-159">3xP20, 1xP10</span></span>|[<span data-ttu-id="c3dfd-160">Medium</span><span class="sxs-lookup"><span data-stu-id="c3dfd-160">Medium</span></span>](https://azure.com/e/465bd07047d148baab032b2f461550cd)|
<span data-ttu-id="c3dfd-161">Grande</span><span class="sxs-lookup"><span data-stu-id="c3dfd-161">Large</span></span>|<span data-ttu-id="c3dfd-162">32000</span><span class="sxs-lookup"><span data-stu-id="c3dfd-162">32000</span></span>|<span data-ttu-id="c3dfd-163">E32s_v3</span><span class="sxs-lookup"><span data-stu-id="c3dfd-163">E32s_v3</span></span>|<span data-ttu-id="c3dfd-164">3xP20, 1xP10</span><span class="sxs-lookup"><span data-stu-id="c3dfd-164">3xP20, 1xP10</span></span>|[<span data-ttu-id="c3dfd-165">Large</span><span class="sxs-lookup"><span data-stu-id="c3dfd-165">Large</span></span>](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)|
<span data-ttu-id="c3dfd-166">Molto grande</span><span class="sxs-lookup"><span data-stu-id="c3dfd-166">Extra Large</span></span>|<span data-ttu-id="c3dfd-167">64000</span><span class="sxs-lookup"><span data-stu-id="c3dfd-167">64000</span></span>|<span data-ttu-id="c3dfd-168">M64s</span><span class="sxs-lookup"><span data-stu-id="c3dfd-168">M64s</span></span>|<span data-ttu-id="c3dfd-169">4xP20, 1xP10</span><span class="sxs-lookup"><span data-stu-id="c3dfd-169">4xP20, 1xP10</span></span>|[<span data-ttu-id="c3dfd-170">Extra Large</span><span class="sxs-lookup"><span data-stu-id="c3dfd-170">Extra Large</span></span>](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)|

> [!NOTE]
> <span data-ttu-id="c3dfd-171">Queste indicazioni dei prezzi costituiscono una guida che indica esclusivamente le macchine virtuali e i costi di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-171">This pricing is a guide that only indicates the VMs and storage costs.</span></span> <span data-ttu-id="c3dfd-172">Sono esclusi i costi relativi a rete, archivio di backup e dati in ingresso/uscita.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-172">It excludes networking, backup storage, and data ingress/egress charges.</span></span>

- <span data-ttu-id="c3dfd-173">[Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1): un sistema di piccole dimensioni è costituito da una macchina virtuale di tipo D8s_v3 con 8 vCPU, 32 GB di RAM e 200 GB di archiviazione temporanea, nonché due dischi di archiviazione Premium da 512 GB e uno da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-173">[Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1): A small system consists of VM type D8s_v3 with 8x vCPUs, 32 GB RAM and 200 GB temp storage, additionally two 512 GB and one 128 GB premium storage disks.</span></span>
- <span data-ttu-id="c3dfd-174">[Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd): un sistema di medie dimensioni è costituito da una macchina virtuale di tipo D16s_v3 con 16 vCPU, 64 GB di RAM e 400 GB di archiviazione temporanea, nonché tre dischi di archiviazione Premium da 512 GB e uno da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-174">[Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd): A medium system consists of VM type D16s_v3 with 16x vCPUs, 64 GB RAM and 400 GB temp storage, additionally three 512 GB and one 128 GB premium storage disks.</span></span>
- <span data-ttu-id="c3dfd-175">[Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931): un sistema di grandi dimensioni è costituito da una macchina virtuale di tipo E32s_v3 con 32 vCPU, 256 GB di RAM e 512 GB di archiviazione temporanea, nonché tre dischi di archiviazione Premium da 512 GB e uno da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-175">[Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931): A large system consists of VM type E32s_v3 with 32x vCPUs, 256 GB RAM and 512 GB temp storage, additionally three 512GB and one 128GB premium storage disks.</span></span>
- <span data-ttu-id="c3dfd-176">[Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef): un sistema di dimensioni molto grandi è costituito da una macchina virtuale di tipo M64s con 64 vCPU, 1024 GB di RAM e 2000 GB di archiviazione temporanea, nonché quattro dischi di archiviazione Premium da 512 GB e uno da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-176">[Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef): An extra large system consists of a VM type M64s with 64x vCPUs, 1024 GB RAM and 2000 GB temp storage, additionally four 512 GB and one 128 GB premium storage disks.</span></span>

## <a name="deployment"></a><span data-ttu-id="c3dfd-177">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="c3dfd-177">Deployment</span></span>

<span data-ttu-id="c3dfd-178">Fare clic qui per distribuire l'infrastruttura sottostante per questo scenario.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-178">Click here to deploy the underlying infrastructure for this scenario.</span></span>

<!-- markdownlint-disable MD033 -->

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-2tier%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

<!-- markdownlint-enable MD033 -->

> [!NOTE]
> <span data-ttu-id="c3dfd-179">SAP e Oracle non vengono installati durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-179">SAP and Oracle are not installed during this deployment.</span></span> <span data-ttu-id="c3dfd-180">Questi componenti dovranno essere distribuiti separatamente.</span><span class="sxs-lookup"><span data-stu-id="c3dfd-180">You will need to deploy these components separately.</span></span>

<!-- links -->
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[sap-netweaver]: /azure/architecture/reference-architectures/sap/sap-netweaver
[sap-hana]: /azure/architecture/reference-architectures/sap/sap-s4hana
[sap-large]: /azure/architecture/reference-architectures/sap/hana-large-instances
[hub-spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
