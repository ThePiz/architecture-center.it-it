---
title: SAP per carichi di lavoro di sviluppo/test
description: Scenario SAP per un ambiente di sviluppo/test
author: AndrewDibbins
ms.date: 7/11/18
ms.openlocfilehash: d0f266e40969cf4782e69041889a686387499722
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389180"
---
# <a name="sap-for-devtest-workloads"></a><span data-ttu-id="3cb74-103">SAP per carichi di lavoro di sviluppo/test</span><span class="sxs-lookup"><span data-stu-id="3cb74-103">SAP for dev/test workloads</span></span>

<span data-ttu-id="3cb74-104">Questo esempio offre indicazioni su come eseguire un'implementazione di sviluppo/test di SAP NetWeaver in un ambiente Windows o Linux in Azure.</span><span class="sxs-lookup"><span data-stu-id="3cb74-104">This example provides guidance for how to run a dev/test implementation of SAP NetWeaver in a Windows or Linux environment on Azure.</span></span> <span data-ttu-id="3cb74-105">Il database usato è AnyDB, che nella terminologia SAP indica qualsiasi DBMS supportato diverso da SAP HANA.</span><span class="sxs-lookup"><span data-stu-id="3cb74-105">The database used is AnyDB, the SAP term for any supported DBMS (that isn't SAP HANA).</span></span> <span data-ttu-id="3cb74-106">Dato che è progettata per ambienti non di produzione, l'architettura viene distribuita con una sola macchina virtuale (VM) ed è possibile modificarne le dimensioni in base alle esigenze dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="3cb74-106">Because this architecture is designed for non-production environments, it's deployed with just a single virtual machine (VM) and it's size can be changed to accommodate your organization's needs.</span></span>

<span data-ttu-id="3cb74-107">Per casi d'uso di produzione, esaminare le architetture di riferimento SAP disponibili di seguito:</span><span class="sxs-lookup"><span data-stu-id="3cb74-107">For production use cases review the SAP reference architectures available below:</span></span>

* <span data-ttu-id="3cb74-108">[SAP NetWeaver per AnyDB][sap-netweaver]</span><span class="sxs-lookup"><span data-stu-id="3cb74-108">[SAP netweaver for AnyDB][sap-netweaver]</span></span>
* <span data-ttu-id="3cb74-109">[SAP S/4Hana][sap-hana]</span><span class="sxs-lookup"><span data-stu-id="3cb74-109">[SAP S/4Hana][sap-hana]</span></span>
* <span data-ttu-id="3cb74-110">[SAP in istanze Large di Azure][sap-large]</span><span class="sxs-lookup"><span data-stu-id="3cb74-110">[SAP on Azure large instances][sap-large]</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="3cb74-111">Casi d'uso correlati</span><span class="sxs-lookup"><span data-stu-id="3cb74-111">Related use cases</span></span>

<span data-ttu-id="3cb74-112">Prendere in considerazione questo scenario per i casi d'uso seguenti:</span><span class="sxs-lookup"><span data-stu-id="3cb74-112">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="3cb74-113">Carichi di lavoro SAP non di produzione e non critici (sandbox, sviluppo, test, controllo di qualità)</span><span class="sxs-lookup"><span data-stu-id="3cb74-113">Non-critical SAP non-productive workloads (sandbox, development, test, quality assurance)</span></span>
* <span data-ttu-id="3cb74-114">Carichi di lavoro SAP Business One non critici</span><span class="sxs-lookup"><span data-stu-id="3cb74-114">Non-critical SAP business one workloads</span></span>

## <a name="architecture"></a><span data-ttu-id="3cb74-115">Architettura</span><span class="sxs-lookup"><span data-stu-id="3cb74-115">Architecture</span></span>

![Diagramma](media/sap-2tier/SAP-Infra-2Tier_finalversion.png)

<span data-ttu-id="3cb74-117">Questo scenario prevede il provisioning di un singolo database di sistema SAP e un server applicazioni SAP in un'unica macchina virtuale. Il flusso dei dati nello scenario avviene come segue:</span><span class="sxs-lookup"><span data-stu-id="3cb74-117">This scenario covers the provision of a single SAP system database and SAP application Server on a single virtual machine, the data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="3cb74-118">I clienti dal livello presentazione usano in locale l'interfaccia utente grafica SAP o altre interfacce utente (Internet Explorer, Excel o un'altra applicazione Web) per accedere al sistema SAP basato su Azure.</span><span class="sxs-lookup"><span data-stu-id="3cb74-118">Customers from the Presentation Tier use their SAP GUI, or other user interfaces (Internet Explorer, Excel, or other web application) on premise to access the Azure-based SAP system.</span></span>
2. <span data-ttu-id="3cb74-119">Per la connettività viene usata una connessione ExpressRoute stabilita,</span><span class="sxs-lookup"><span data-stu-id="3cb74-119">Connectivity is provided through the use of an established Express Route.</span></span> <span data-ttu-id="3cb74-120">che termina nel gateway ExpressRoute in Azure.</span><span class="sxs-lookup"><span data-stu-id="3cb74-120">The Express Route connection is terminated in Azure at the Express Route Gateway.</span></span> <span data-ttu-id="3cb74-121">Il traffico di rete viene indirizzato attraverso il gateway ExpressRoute alla subnet del gateway, da questa alla subnet spoke del livello applicazione (vedere il modello [hub-spoke][hub-spoke]) e quindi tramite un gateway di sicurezza di rete alla macchina virtuale dell'applicazione SAP.</span><span class="sxs-lookup"><span data-stu-id="3cb74-121">Network traffic routes through the Express Route gateway to the Gateway Subnet and from the gateway subnet to the Application Tier Spoke subnet (see the [hub-spoke][hub-spoke] pattern) and via a Network Security Gateway to the SAP application virtual machine.</span></span>
3. <span data-ttu-id="3cb74-122">I server di gestione delle identità offrono servizi di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3cb74-122">The identity management servers provide authentication services.</span></span>
4. <span data-ttu-id="3cb74-123">Il jumpbox offre funzionalità di gestione locale.</span><span class="sxs-lookup"><span data-stu-id="3cb74-123">The jump box provides local management capabilities.</span></span>

### <a name="components"></a><span data-ttu-id="3cb74-124">Componenti</span><span class="sxs-lookup"><span data-stu-id="3cb74-124">Components</span></span>

* <span data-ttu-id="3cb74-125">I [gruppi di risorse](/azure/azure-resource-manager/resource-group-overview#resource-groups) sono un contenitore logico per le risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="3cb74-125">[Resource Groups](/azure/azure-resource-manager/resource-group-overview#resource-groups) is a logical container for Azure resources.</span></span>
* <span data-ttu-id="3cb74-126">Le [reti virtuali](/azure/virtual-network/virtual-networks-overview) costituiscono la base delle comunicazioni di rete in Azure.</span><span class="sxs-lookup"><span data-stu-id="3cb74-126">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) is the basis of network communications within Azure</span></span>
* <span data-ttu-id="3cb74-127">Le [macchine virtuali](/azure/virtual-machines/windows/overview) di Azure offrono un'infrastruttura virtualizzata sicura, a scalabilità elevata e su richiesta con Windows o Linux Server.</span><span class="sxs-lookup"><span data-stu-id="3cb74-127">[Virtual Machine](/azure/virtual-machines/windows/overview) Azure Virtual Machines provides on-demand, high-scale, secure, virtualized infrastructure using Windows or Linux Server</span></span>
* <span data-ttu-id="3cb74-128">[ExpressRoute](/azure/expressroute/expressroute-introduction) consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività.</span><span class="sxs-lookup"><span data-stu-id="3cb74-128">[Express Route](/azure/expressroute/expressroute-introduction) lets you extend your on-premises networks into the Microsoft cloud over a private connection facilitated by a connectivity provider.</span></span>
* <span data-ttu-id="3cb74-129">Un [gruppo di sicurezza di rete](/azure/virtual-network/security-overview) consente di limitare il traffico di rete verso le risorse in una rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="3cb74-129">[Network Security Group](/azure/virtual-network/security-overview) lets you limit network traffic to resources in a virtual network.</span></span> <span data-ttu-id="3cb74-130">Un gruppo di sicurezza di rete contiene un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in ingresso o in uscita in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo.</span><span class="sxs-lookup"><span data-stu-id="3cb74-130">A network security group contains a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> 

## <a name="considerations"></a><span data-ttu-id="3cb74-131">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="3cb74-131">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="3cb74-132">Disponibilità</span><span class="sxs-lookup"><span data-stu-id="3cb74-132">Availability</span></span>

 <span data-ttu-id="3cb74-133">Microsoft offre un contratto di servizio per le singole istanze di VM.</span><span class="sxs-lookup"><span data-stu-id="3cb74-133">Microsoft offers a service level agreement (SLA) for single VM instances.</span></span> <span data-ttu-id="3cb74-134">Per altre informazioni sul contratto di servizio di Microsoft Azure per le macchine virtuali, vedere [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines).</span><span class="sxs-lookup"><span data-stu-id="3cb74-134">For more information on Microsoft Azure Service Level Agreement for Virtual Machines [SLA For Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span></span>

### <a name="scalability"></a><span data-ttu-id="3cb74-135">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="3cb74-135">Scalability</span></span>

<span data-ttu-id="3cb74-136">Per indicazioni generali sulla progettazione di soluzioni scalabili, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.</span><span class="sxs-lookup"><span data-stu-id="3cb74-136">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="3cb74-137">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="3cb74-137">Security</span></span>

<span data-ttu-id="3cb74-138">Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].</span><span class="sxs-lookup"><span data-stu-id="3cb74-138">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="3cb74-139">Resilienza</span><span class="sxs-lookup"><span data-stu-id="3cb74-139">Resiliency</span></span>

<span data-ttu-id="3cb74-140">Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].</span><span class="sxs-lookup"><span data-stu-id="3cb74-140">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="3cb74-141">Prezzi</span><span class="sxs-lookup"><span data-stu-id="3cb74-141">Pricing</span></span>

<span data-ttu-id="3cb74-142">Esaminare il costo di esecuzione dello scenario. Nel calcolatore dei costi sono preconfigurati tutti i servizi.</span><span class="sxs-lookup"><span data-stu-id="3cb74-142">Explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span>  <span data-ttu-id="3cb74-143">Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base al traffico previsto.</span><span class="sxs-lookup"><span data-stu-id="3cb74-143">To see how the pricing would change for your particular use case change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="3cb74-144">Sono stati definiti quattro profili di costo di esempio in base alla quantità di traffico prevista:</span><span class="sxs-lookup"><span data-stu-id="3cb74-144">We have provided four sample cost profiles based on amount of traffic you expect to get:</span></span>

|<span data-ttu-id="3cb74-145">Dimensione</span><span class="sxs-lookup"><span data-stu-id="3cb74-145">Size</span></span>|<span data-ttu-id="3cb74-146">SAP</span><span class="sxs-lookup"><span data-stu-id="3cb74-146">SAPs</span></span>|<span data-ttu-id="3cb74-147">Tipo macchina virtuale</span><span class="sxs-lookup"><span data-stu-id="3cb74-147">VM Type</span></span>|<span data-ttu-id="3cb74-148">Archiviazione</span><span class="sxs-lookup"><span data-stu-id="3cb74-148">Storage</span></span>|<span data-ttu-id="3cb74-149">Calcolatore prezzi di Azure</span><span class="sxs-lookup"><span data-stu-id="3cb74-149">Azure Pricing Calculator</span></span>|
|----|----|-------|-------|---------------|
|<span data-ttu-id="3cb74-150">Piccolo</span><span class="sxs-lookup"><span data-stu-id="3cb74-150">Small</span></span>|<span data-ttu-id="3cb74-151">8000</span><span class="sxs-lookup"><span data-stu-id="3cb74-151">8000</span></span>|<span data-ttu-id="3cb74-152">D8s_v3</span><span class="sxs-lookup"><span data-stu-id="3cb74-152">D8s_v3</span></span>|<span data-ttu-id="3cb74-153">2xP20, 1xP10</span><span class="sxs-lookup"><span data-stu-id="3cb74-153">2xP20, 1xP10</span></span>|[<span data-ttu-id="3cb74-154">Small</span><span class="sxs-lookup"><span data-stu-id="3cb74-154">Small</span></span>](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)|
|<span data-ttu-id="3cb74-155">Media</span><span class="sxs-lookup"><span data-stu-id="3cb74-155">Medium</span></span>|<span data-ttu-id="3cb74-156">16000</span><span class="sxs-lookup"><span data-stu-id="3cb74-156">16000</span></span>|<span data-ttu-id="3cb74-157">D16s_v3</span><span class="sxs-lookup"><span data-stu-id="3cb74-157">D16s_v3</span></span>|<span data-ttu-id="3cb74-158">3xP20, 1xP10</span><span class="sxs-lookup"><span data-stu-id="3cb74-158">3xP20, 1xP10</span></span>|[<span data-ttu-id="3cb74-159">Medium</span><span class="sxs-lookup"><span data-stu-id="3cb74-159">Medium</span></span>](https://azure.com/e/465bd07047d148baab032b2f461550cd)|
<span data-ttu-id="3cb74-160">Grande</span><span class="sxs-lookup"><span data-stu-id="3cb74-160">Large</span></span>|<span data-ttu-id="3cb74-161">32000</span><span class="sxs-lookup"><span data-stu-id="3cb74-161">32000</span></span>|<span data-ttu-id="3cb74-162">E32s_v3</span><span class="sxs-lookup"><span data-stu-id="3cb74-162">E32s_v3</span></span>|<span data-ttu-id="3cb74-163">3xP20, 1xP10</span><span class="sxs-lookup"><span data-stu-id="3cb74-163">3xP20, 1xP10</span></span>|[<span data-ttu-id="3cb74-164">Large</span><span class="sxs-lookup"><span data-stu-id="3cb74-164">Large</span></span>](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)|
<span data-ttu-id="3cb74-165">Molto grande</span><span class="sxs-lookup"><span data-stu-id="3cb74-165">Extra Large</span></span>|<span data-ttu-id="3cb74-166">64000</span><span class="sxs-lookup"><span data-stu-id="3cb74-166">64000</span></span>|<span data-ttu-id="3cb74-167">M64s</span><span class="sxs-lookup"><span data-stu-id="3cb74-167">M64s</span></span>|<span data-ttu-id="3cb74-168">4xP20, 1xP10</span><span class="sxs-lookup"><span data-stu-id="3cb74-168">4xP20, 1xP10</span></span>|[<span data-ttu-id="3cb74-169">Extra Large</span><span class="sxs-lookup"><span data-stu-id="3cb74-169">Extra Large</span></span>](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)|

<span data-ttu-id="3cb74-170">Nota: i prezzi offrono una guida e indicano solo le macchine virtuali e i costi di archiviazione (escludendo gli addebiti per rete, archivio di backup e dati in ingresso/uscita).</span><span class="sxs-lookup"><span data-stu-id="3cb74-170">Note: pricing is a guide and only indicates the VMs and storage costs (excludes, networking, backup storage, and data ingress/egress charges).</span></span>

* <span data-ttu-id="3cb74-171">[Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1): un sistema di piccole dimensioni è costituito da una macchina virtuale di tipo D8s_v3 con 8 vCPU, 32 GB di RAM e 200 GB di archiviazione temporanea, nonché due dischi di archiviazione Premium da 512 GB e uno da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="3cb74-171">[Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1): A small system consists of VM type D8s_v3 with 8x vCPUs, 32 GB RAM and 200 GB temp storage, additionally two 512 GB and one 128 GB premium storage disks.</span></span>
* <span data-ttu-id="3cb74-172">[Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd): un sistema di medie dimensioni è costituito da una macchina virtuale di tipo D16s_v3 con 16 vCPU, 64 GB di RAM e 400 GB di archiviazione temporanea, nonché tre dischi di archiviazione Premium da 512 GB e uno da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="3cb74-172">[Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd): A medium system consists of VM type D16s_v3 with 16x vCPUs, 64 GB RAM and 400 GB temp storage, additionally three 512 GB and one 128 GB premium storage disks.</span></span>
* <span data-ttu-id="3cb74-173">[Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931): un sistema di grandi dimensioni è costituito da una macchina virtuale di tipo E32s_v3 con 32 vCPU, 256 GB di RAM e 512 GB di archiviazione temporanea, nonché tre dischi di archiviazione Premium da 512 GB e uno da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="3cb74-173">[Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931): A large system consists of VM type E32s_v3 with 32x vCPUs, 256 GB RAM and 512 GB temp storage, additionally three 512GB and one 128GB premium storage disks.</span></span>
* <span data-ttu-id="3cb74-174">[Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef): un sistema di dimensioni molto grandi è costituito da una macchina virtuale di tipo M64s con 64 vCPU, 1024 GB di RAM e 2000 GB di archiviazione temporanea, nonché quattro dischi di archiviazione Premium da 512 GB e uno da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="3cb74-174">[Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef): An extra large system consists of a VM type M64s with 64x vCPUs, 1024 GB RAM and 2000 GB temp storage, additionally four 512 GB and one 128 GB premium storage disks.</span></span>

## <a name="deployment"></a><span data-ttu-id="3cb74-175">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="3cb74-175">Deployment</span></span>

<span data-ttu-id="3cb74-176">Per distribuire un'infrastruttura sottostante simile allo scenario descritto sopra, usare il pulsante di seguito</span><span class="sxs-lookup"><span data-stu-id="3cb74-176">To deploy the underlying infrastructure similar to the scenario above, use the deploy button</span></span>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-2tier%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

<span data-ttu-id="3cb74-177">\* SAP non sarà installato automaticamente. Installarlo manualmente dopo aver compilato l'infrastruttura.</span><span class="sxs-lookup"><span data-stu-id="3cb74-177">\* SAP won't be automatically installed, manually install it after the infrastructure has been built.</span></span>

<!-- links -->
[reference architecture]:  /azure/architecture/reference-architectures/sap
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[sap-netweaver]: /azure/architecture/reference-architectures/sap/sap-netweaver
[sap-hana]: /azure/architecture/reference-architectures/sap/sap-s4hana
[sap-large]: /azure/architecture/reference-architectures/sap/hana-large-instances
[hub-spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke