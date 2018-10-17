---
title: Esecuzione di carichi di lavoro di produzione SAP con un database Oracle in Azure
description: Eseguire una distribuzione di produzione SAP in Azure con un database Oracle.
author: DharmeshBhagat
ms.date: 9/12/2018
ms.openlocfilehash: 4c0e054a9024cd50581acd5b472a09d6e98d2bed
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818571"
---
# <a name="running-sap-production-workloads-using-an-oracle-database-on-azure"></a><span data-ttu-id="33698-103">Esecuzione di carichi di lavoro di produzione SAP con un database Oracle in Azure</span><span class="sxs-lookup"><span data-stu-id="33698-103">Running SAP production workloads using an Oracle Database on Azure</span></span>

<span data-ttu-id="33698-104">I sistemi SAP vengono usati per eseguire applicazioni aziendali cruciali.</span><span class="sxs-lookup"><span data-stu-id="33698-104">SAP systems are used to run mission-critical business applications.</span></span> <span data-ttu-id="33698-105">Eventuali interruzioni compromettono i processi chiave e possono causare un aumento dei costi o una perdita di profitti.</span><span class="sxs-lookup"><span data-stu-id="33698-105">Any outage disrupts key processes and can cause increased expenses or lost revenue.</span></span> <span data-ttu-id="33698-106">Per non incorrere in una simile situazione, è necessaria un'infrastruttura SAP in grado di garantire disponibilità elevata e resilienza in caso di errori.</span><span class="sxs-lookup"><span data-stu-id="33698-106">Avoiding these outcomes requires an SAP infrastructure that is highly available and resilient when failures occur.</span></span>

<span data-ttu-id="33698-107">La creazione di un ambiente SAP a disponibilità elevata richiede l'eliminazione di singoli punti di guasto nell'architettura di sistema e nei processi.</span><span class="sxs-lookup"><span data-stu-id="33698-107">Building a highly available SAP environment requires eliminating single points of failures in your system architecture and processes.</span></span> <span data-ttu-id="33698-108">I singoli punti di guasto possono essere causati da errori del sito, errori nei componenti di sistema o anche errori umani.</span><span class="sxs-lookup"><span data-stu-id="33698-108">Single points of failure can be caused by site failures, errors in system components, or even human error.</span></span>

<span data-ttu-id="33698-109">Questo scenario di esempio illustra una distribuzione SAP in macchine virtuali Windows o Linux in Azure, insieme a un database Oracle a disponibilità elevata.</span><span class="sxs-lookup"><span data-stu-id="33698-109">This example scenario demonstrates an SAP deployment on Windows or Linux virtual machines (VMs) on Azure, along with a High Availability (HA) Oracle database.</span></span> <span data-ttu-id="33698-110">Per la distribuzione SAP, è possibile usare macchine virtuali di dimensioni diverse in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="33698-110">For your SAP deployment, you can use VMs of different sizes based on your requirements.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="33698-111">Casi d'uso pertinenti</span><span class="sxs-lookup"><span data-stu-id="33698-111">Relevant use cases</span></span>

<span data-ttu-id="33698-112">Esaminare questo esempio per gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="33698-112">Consider this example for the following scenarios:</span></span>

* <span data-ttu-id="33698-113">Carichi di lavoro cruciali in esecuzione in SAP.</span><span class="sxs-lookup"><span data-stu-id="33698-113">Mission-critical workloads running on SAP.</span></span>
* <span data-ttu-id="33698-114">Carichi di lavoro SAP non critici.</span><span class="sxs-lookup"><span data-stu-id="33698-114">Non-critical SAP workloads.</span></span>
* <span data-ttu-id="33698-115">Ambienti di testing per SAP che simulano un ambiente a disponibilità elevata.</span><span class="sxs-lookup"><span data-stu-id="33698-115">Test environments for SAP that simulate a high-availability environment.</span></span>

## <a name="architecture"></a><span data-ttu-id="33698-116">Architettura</span><span class="sxs-lookup"><span data-stu-id="33698-116">Architecture</span></span>

![Panoramica dell'architettura di un ambiente SAP di produzione in Azure][architecture]

<span data-ttu-id="33698-118">Questo esempio include una configurazione a disponibilità elevata per un database Oracle, SAP Central Services e più server applicazioni SAP in esecuzione in macchine virtuali diverse.</span><span class="sxs-lookup"><span data-stu-id="33698-118">This example includes a high availability configuration for an Oracle database, SAP central services, and multiple SAP application servers running on different virtual machines.</span></span> <span data-ttu-id="33698-119">La rete di Azure usa una [topologia hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) per motivi di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="33698-119">The Azure network uses a [hub-and-spoke topology](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) for security purposes.</span></span> <span data-ttu-id="33698-120">Il flusso dei dati attraverso la soluzione avviene come segue:</span><span class="sxs-lookup"><span data-stu-id="33698-120">The data flows through the solution as follows:</span></span>

1. <span data-ttu-id="33698-121">Gli utenti accedono al sistema SAP tramite l'interfaccia utente SAP, un Web browser o altri strumenti client come Microsoft Excel.</span><span class="sxs-lookup"><span data-stu-id="33698-121">Users access the SAP system via the SAP user interface, a web browser, or other client tools like Microsoft Excel.</span></span> <span data-ttu-id="33698-122">Una connessione ExpressRoute fornisce l'accesso dalla rete locale dell'organizzazione alle risorse in esecuzione in Azure.</span><span class="sxs-lookup"><span data-stu-id="33698-122">An ExpressRoute connection provides access from the organization's on-premises network to resources running in Azure.</span></span>
2. <span data-ttu-id="33698-123">ExpressRoute termina nel gateway di rete virtuale di ExpressRoute in Azure.</span><span class="sxs-lookup"><span data-stu-id="33698-123">The ExpressRoute terminates in Azure at the ExpressRoute virtual network (VNet) gateway.</span></span> <span data-ttu-id="33698-124">Il traffico di rete viene indirizzato a una subnet del gateway tramite il gateway ExpressRoute creato nella rete virtuale dell'hub.</span><span class="sxs-lookup"><span data-stu-id="33698-124">Network traffic is routed to a gateway subnet through the ExpressRoute gateway created in the hub VNet.</span></span>
3. <span data-ttu-id="33698-125">Viene eseguito il peering della rete virtuale dell'hub a una rete virtuale spoke.</span><span class="sxs-lookup"><span data-stu-id="33698-125">The hub VNet is peered to a spoke VNet.</span></span> <span data-ttu-id="33698-126">La subnet del livello applicazione ospita le macchine virtuali che eseguono SAP in un set di disponibilità.</span><span class="sxs-lookup"><span data-stu-id="33698-126">The application tier subnet hosts the virtual machines running SAP in an availability set.</span></span>
4. <span data-ttu-id="33698-127">I server di gestione delle identità offrono servizi di autenticazione per la soluzione.</span><span class="sxs-lookup"><span data-stu-id="33698-127">The identity management servers provide authentication services for the solution.</span></span>
5. <span data-ttu-id="33698-128">Il jumpbox viene usato dagli amministratori di sistema per gestire in modo sicuro le risorse distribuite in Azure.</span><span class="sxs-lookup"><span data-stu-id="33698-128">The jump box is used by system administrators to securely manage resources deployed in Azure.</span></span>

### <a name="components"></a><span data-ttu-id="33698-129">Componenti</span><span class="sxs-lookup"><span data-stu-id="33698-129">Components</span></span>

* <span data-ttu-id="33698-130">Le [reti virtuali](/azure/virtual-network/virtual-networks-overview) vengono usate in questo scenario per creare una topologia hub-spoke virtuale in Azure.</span><span class="sxs-lookup"><span data-stu-id="33698-130">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) are used in this scenario to create a virtual hub-and-spoke topology in Azure.</span></span>
* <span data-ttu-id="33698-131">Le [macchine virtuali](/azure/virtual-machines/windows/overview) offrono le risorse di calcolo per ogni livello della soluzione.</span><span class="sxs-lookup"><span data-stu-id="33698-131">[Virtual Machines](/azure/virtual-machines/windows/overview) provide the compute resources for each tier of the solution.</span></span> <span data-ttu-id="33698-132">Ogni cluster di macchine virtuali è configurato come un [set di disponibilità](/azure/virtual-machines/windows/regions-and-availability#availability-sets).</span><span class="sxs-lookup"><span data-stu-id="33698-132">Each cluster of virtual machines is configured as an [availability set](/azure/virtual-machines/windows/regions-and-availability#availability-sets).</span></span>
* <span data-ttu-id="33698-133">[ExpressRoute](/azure/expressroute/expressroute-introduction) estende la rete locale nel cloud Microsoft tramite una connessione privata stabilita da un provider di connettività.</span><span class="sxs-lookup"><span data-stu-id="33698-133">[ExpressRoute](/azure/expressroute/expressroute-introduction) extends your on-premises network into the Microsoft cloud through a private connection established by a connectivity provider.</span></span>
* <span data-ttu-id="33698-134">I [gruppi di sicurezza di rete (NSG)](/azure/virtual-network/security-overview) limitano l'accesso alla rete alle risorse in una rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="33698-134">[Network Security Groups (NSG)](/azure/virtual-network/security-overview) limit network access to the resources in a virtual network.</span></span> <span data-ttu-id="33698-135">Un NSG contiene un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo.</span><span class="sxs-lookup"><span data-stu-id="33698-135">An NSG contains a list of security rules that allow or deny network traffic based on source or destination IP address, port, and protocol.</span></span> 
* <span data-ttu-id="33698-136">I [gruppi di risorse](/azure/azure-resource-manager/resource-group-overview#resource-groups) fungono da contenitori logici per le risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="33698-136">[Resource Groups](/azure/azure-resource-manager/resource-group-overview#resource-groups) act as logical containers for Azure resources.</span></span>

### <a name="alternatives"></a><span data-ttu-id="33698-137">Alternative</span><span class="sxs-lookup"><span data-stu-id="33698-137">Alternatives</span></span>

<span data-ttu-id="33698-138">SAP offre opzioni flessibili per diverse combinazioni di sistema operativo, sistema di gestione di database e tipi di macchine virtuali in un ambiente Azure.</span><span class="sxs-lookup"><span data-stu-id="33698-138">SAP provides flexible options for different combinations of operating system, database management system, and VM types in an Azure environment.</span></span> <span data-ttu-id="33698-139">Per altre informazioni, vedere la [nota SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533) "SAP Applications on Azure: Supported Products and Azure VM Types" (Applicazioni SAP in Azure: prodotti e tipi di macchine virtuali di Azure supportati).</span><span class="sxs-lookup"><span data-stu-id="33698-139">For more information, see [SAP note 1928533](https://launchpad.support.sap.com/#/notes/1928533), "SAP Applications on Azure: Supported Products and Azure VM Types".</span></span>

## <a name="considerations"></a><span data-ttu-id="33698-140">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="33698-140">Considerations</span></span>

<span data-ttu-id="33698-141">È stata definita una serie di procedure consigliate per la creazione di ambienti SAP a disponibilità elevata in Azure.</span><span class="sxs-lookup"><span data-stu-id="33698-141">Recommended practices are defined for building highly available SAP environments in Azure.</span></span> <span data-ttu-id="33698-142">Per altre informazioni, vedere [Architettura e scenari di disponibilità elevata per SAP NetWeaver](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios).</span><span class="sxs-lookup"><span data-stu-id="33698-142">For more information, see [High-availability architecture and scenarios for SAP NetWeaver](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios).</span></span>
<span data-ttu-id="33698-143">Per altre informazioni, vedere [Disponibilità elevata di applicazioni SAP in macchine virtuali di Azure](/azure/virtual-machines/workloads/sap/high-availability-guide).</span><span class="sxs-lookup"><span data-stu-id="33698-143">For more information, see [High availability of SAP applications on Azure VMs](/azure/virtual-machines/workloads/sap/high-availability-guide).</span></span>
* <span data-ttu-id="33698-144">Anche per i database Oracle sono disponibili procedure consigliate per Azure.</span><span class="sxs-lookup"><span data-stu-id="33698-144">Oracle databases also have recommended practices for Azure.</span></span> <span data-ttu-id="33698-145">Per altre informazioni, vedere [Progettazione e implementazione di un database Oracle in Azure](/azure/virtual-machines/workloads/oracle/oracle-design).</span><span class="sxs-lookup"><span data-stu-id="33698-145">For more information, see [Designing and implementing an Oracle database in Azure](/azure/virtual-machines/workloads/oracle/oracle-design).</span></span> 
* <span data-ttu-id="33698-146">Oracle Data Guard viene usato per eliminare i singoli punti di guasto per i database Oracle cruciali.</span><span class="sxs-lookup"><span data-stu-id="33698-146">Oracle Data Guard is used to eliminate single points of failure for mission critical Oracle databases.</span></span> <span data-ttu-id="33698-147">Per altre informazioni, vedere [Implementazione di Oracle Data Guard su una macchina virtuale Linux in Azure](/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard).</span><span class="sxs-lookup"><span data-stu-id="33698-147">For more information, see [Implementing Oracle Data Guard on a Linux virtual machine in Azure](/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard).</span></span>

<span data-ttu-id="33698-148">Microsoft Azure offre servizi di infrastruttura che possono essere usati per distribuire prodotti SAP con un database Oracle.</span><span class="sxs-lookup"><span data-stu-id="33698-148">Microsoft Azure offers infrastructure services that can be used to deploy SAP products with an Oracle database.</span></span> <span data-ttu-id="33698-149">Per altre informazioni, vedere [Distribuzione di un sistema DBMS di Oracle in Azure per un carico di lavoro SAP](/azure/virtual-machines/workloads/sap/dbms_guide_oracle).</span><span class="sxs-lookup"><span data-stu-id="33698-149">For more information, see [Deploying an Oracle DBMS on Azure for an SAP workload](/azure/virtual-machines/workloads/sap/dbms_guide_oracle).</span></span>

## <a name="pricing"></a><span data-ttu-id="33698-150">Prezzi</span><span class="sxs-lookup"><span data-stu-id="33698-150">Pricing</span></span>

<span data-ttu-id="33698-151">Per semplificare il calcolo del costo di esecuzione dello scenario, tutti i servizi sono stati preconfigurati negli esempi del calcolatore dei costi riportati di seguito.</span><span class="sxs-lookup"><span data-stu-id="33698-151">To help you explore the cost of running this scenario, all of the services are pre-configured in the cost calculator examples below.</span></span> <span data-ttu-id="33698-152">Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base</span><span class="sxs-lookup"><span data-stu-id="33698-152">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="33698-153">Sono stati definiti quattro profili di costo di esempio in base alla quantità di traffico prevista:</span><span class="sxs-lookup"><span data-stu-id="33698-153">We have provided four sample cost profiles based on amount of traffic you expect to receive:</span></span>

|<span data-ttu-id="33698-154">Dimensione</span><span class="sxs-lookup"><span data-stu-id="33698-154">Size</span></span>|<span data-ttu-id="33698-155">SAP</span><span class="sxs-lookup"><span data-stu-id="33698-155">SAPs</span></span>|<span data-ttu-id="33698-156">Tipo di macchina virtuale del database</span><span class="sxs-lookup"><span data-stu-id="33698-156">DB VM Type</span></span>|<span data-ttu-id="33698-157">Archiviazione database</span><span class="sxs-lookup"><span data-stu-id="33698-157">DB Storage</span></span>|<span data-ttu-id="33698-158">Macchina virtuale (A)SCS</span><span class="sxs-lookup"><span data-stu-id="33698-158">(A)SCS VM</span></span>|<span data-ttu-id="33698-159">Archiviazione (A)SCS</span><span class="sxs-lookup"><span data-stu-id="33698-159">(A)SCS Storage</span></span>|<span data-ttu-id="33698-160">Tipo di macchina virtuale dell'app</span><span class="sxs-lookup"><span data-stu-id="33698-160">App VM Type</span></span>|<span data-ttu-id="33698-161">Archiviazione app</span><span class="sxs-lookup"><span data-stu-id="33698-161">App Storage</span></span>|<span data-ttu-id="33698-162">Calcolatore prezzi di Azure</span><span class="sxs-lookup"><span data-stu-id="33698-162">Azure Pricing Calculator</span></span>|
|----|----|-------|-------|-----|---|---|--------|---------------|
|<span data-ttu-id="33698-163">Piccolo</span><span class="sxs-lookup"><span data-stu-id="33698-163">Small</span></span>|<span data-ttu-id="33698-164">30000</span><span class="sxs-lookup"><span data-stu-id="33698-164">30000</span></span>|<span data-ttu-id="33698-165">DS13_v2</span><span class="sxs-lookup"><span data-stu-id="33698-165">DS13_v2</span></span>|<span data-ttu-id="33698-166">4xP20, 1xP20</span><span class="sxs-lookup"><span data-stu-id="33698-166">4xP20, 1xP20</span></span>|<span data-ttu-id="33698-167">DS11_v2</span><span class="sxs-lookup"><span data-stu-id="33698-167">DS11_v2</span></span>|<span data-ttu-id="33698-168">1x P10</span><span class="sxs-lookup"><span data-stu-id="33698-168">1x P10</span></span>|<span data-ttu-id="33698-169">DS13_v2</span><span class="sxs-lookup"><span data-stu-id="33698-169">DS13_v2</span></span>|<span data-ttu-id="33698-170">1x P10</span><span class="sxs-lookup"><span data-stu-id="33698-170">1x P10</span></span>|[<span data-ttu-id="33698-171">Small</span><span class="sxs-lookup"><span data-stu-id="33698-171">Small</span></span>](https://azure.com/e/45880ba0bfdf47d497851a7cf2650c7c)|
|<span data-ttu-id="33698-172">Media</span><span class="sxs-lookup"><span data-stu-id="33698-172">Medium</span></span>|<span data-ttu-id="33698-173">70000</span><span class="sxs-lookup"><span data-stu-id="33698-173">70000</span></span>|<span data-ttu-id="33698-174">DS14_v2</span><span class="sxs-lookup"><span data-stu-id="33698-174">DS14_v2</span></span>|<span data-ttu-id="33698-175">6xP20, 1xP20</span><span class="sxs-lookup"><span data-stu-id="33698-175">6xP20, 1xP20</span></span>|<span data-ttu-id="33698-176">DS11_v2</span><span class="sxs-lookup"><span data-stu-id="33698-176">DS11_v2</span></span>|<span data-ttu-id="33698-177">1x P10</span><span class="sxs-lookup"><span data-stu-id="33698-177">1x P10</span></span>|<span data-ttu-id="33698-178">4x DS13_v2</span><span class="sxs-lookup"><span data-stu-id="33698-178">4x DS13_v2</span></span>|<span data-ttu-id="33698-179">1x P10</span><span class="sxs-lookup"><span data-stu-id="33698-179">1x P10</span></span>|[<span data-ttu-id="33698-180">Medium</span><span class="sxs-lookup"><span data-stu-id="33698-180">Medium</span></span>](https://azure.com/e/9a523f79591347ca9a48c3aaa1406f8a)|
<span data-ttu-id="33698-181">Grande</span><span class="sxs-lookup"><span data-stu-id="33698-181">Large</span></span>|<span data-ttu-id="33698-182">180000</span><span class="sxs-lookup"><span data-stu-id="33698-182">180000</span></span>|<span data-ttu-id="33698-183">E32s_v3</span><span class="sxs-lookup"><span data-stu-id="33698-183">E32s_v3</span></span>|<span data-ttu-id="33698-184">5xP30, 1xP20</span><span class="sxs-lookup"><span data-stu-id="33698-184">5xP30, 1xP20</span></span>|<span data-ttu-id="33698-185">DS11_v2</span><span class="sxs-lookup"><span data-stu-id="33698-185">DS11_v2</span></span>|<span data-ttu-id="33698-186">1x P10</span><span class="sxs-lookup"><span data-stu-id="33698-186">1x P10</span></span>|<span data-ttu-id="33698-187">6x DS14_v2</span><span class="sxs-lookup"><span data-stu-id="33698-187">6x DS14_v2</span></span>|<span data-ttu-id="33698-188">1x P10</span><span class="sxs-lookup"><span data-stu-id="33698-188">1x P10</span></span>|[<span data-ttu-id="33698-189">Large</span><span class="sxs-lookup"><span data-stu-id="33698-189">Large</span></span>](https://azure.com/e/f70fccf571e948c4b37d4fecc07cbf42)|
<span data-ttu-id="33698-190">Molto grande</span><span class="sxs-lookup"><span data-stu-id="33698-190">Extra Large</span></span>|<span data-ttu-id="33698-191">250000</span><span class="sxs-lookup"><span data-stu-id="33698-191">250000</span></span>|<span data-ttu-id="33698-192">M64s</span><span class="sxs-lookup"><span data-stu-id="33698-192">M64s</span></span>|<span data-ttu-id="33698-193">6xP30, 1xP30</span><span class="sxs-lookup"><span data-stu-id="33698-193">6xP30, 1xP30</span></span>|<span data-ttu-id="33698-194">DS11_v2</span><span class="sxs-lookup"><span data-stu-id="33698-194">DS11_v2</span></span>|<span data-ttu-id="33698-195">1x P10</span><span class="sxs-lookup"><span data-stu-id="33698-195">1x P10</span></span>|<span data-ttu-id="33698-196">10x DS14_v2</span><span class="sxs-lookup"><span data-stu-id="33698-196">10x DS14_v2</span></span>|<span data-ttu-id="33698-197">1x P10</span><span class="sxs-lookup"><span data-stu-id="33698-197">1x P10</span></span>|[<span data-ttu-id="33698-198">Extra Large</span><span class="sxs-lookup"><span data-stu-id="33698-198">Extra Large</span></span>](https://azure.com/e/58c636922cf94faf9650f583ff35e97b)|

> [!NOTE]
> <span data-ttu-id="33698-199">Queste indicazioni dei prezzi costituiscono una guida e indicano esclusivamente le macchine virtuali e i costi di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="33698-199">This pricing is a guide and only indicates the VMs and storage costs.</span></span> <span data-ttu-id="33698-200">Sono esclusi i costi relativi a rete, archivio di backup e dati in ingresso/uscita.</span><span class="sxs-lookup"><span data-stu-id="33698-200">It excludes networking, backup storage, and data ingress/egress charges.</span></span>

* <span data-ttu-id="33698-201">[Small](https://azure.com/e/45880ba0bfdf47d497851a7cf2650c7c): un sistema di piccole dimensioni è costituito da una macchina virtuale di tipo DS13_v2 per il server di database con 8 vCPU, 56 GB di RAM e 112 GB di archiviazione temporanea, nonché cinque dischi di archiviazione Premium da 512 GB.</span><span class="sxs-lookup"><span data-stu-id="33698-201">[Small](https://azure.com/e/45880ba0bfdf47d497851a7cf2650c7c): A small system consists of VM type DS13_v2 for the database server with 8x vCPUs, 56-GB RAM, and 112-GB temp storage, additionally five 512-GB premium storage disks.</span></span> <span data-ttu-id="33698-202">Un server di istanza di SAP Central che usa una macchina virtuale di tipo DS11_v2 con 2 vCPU, 14 GB di RAM e 28 GB di archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="33698-202">An SAP Central Instance server using a DS11_v2 VM types with 2x vCPUs 14-GB RAM and 28-GB temp storage.</span></span> <span data-ttu-id="33698-203">Una singola macchina virtuale di tipo DS13_v2 per il server applicazioni SAP con 8 vCPU, 56 GB di RAM e 400 GB di archiviazione temporanea, nonché un disco di archiviazione Premium da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="33698-203">A single VM type DS13_v2 for the SAP application server with 8x vCPUs, 56-GB RAM, and 400-GB temp storage, additionally one 128-GB premium storage disk.</span></span>

* <span data-ttu-id="33698-204">[Medium](https://azure.com/e/9a523f79591347ca9a48c3aaa1406f8a): un sistema di medie dimensioni è costituito da una macchina virtuale di tipo DS14_v2 per il server di database con 16 vCPU, 112 GB di RAM e 800 GB di archiviazione temporanea, nonché sette dischi di archiviazione Premium da 512 GB.</span><span class="sxs-lookup"><span data-stu-id="33698-204">[Medium](https://azure.com/e/9a523f79591347ca9a48c3aaa1406f8a): A medium system consists of VM type DS14_v2 for the database server with 16x vCPUs, 112 GB RAM, and 800-GB temp storage, additionally seven 512-GB premium storage disks.</span></span> <span data-ttu-id="33698-205">Un server di istanza di SAP Central che usa una macchina virtuale di tipo DS11_v2 con 2 vCPU, 14 GB di RAM e 28 GB di archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="33698-205">An SAP Central Instance server using a DS11_v2 VM types with 2x vCPUs 14-GB RAM and 28-GB temp storage.</span></span> <span data-ttu-id="33698-206">Quattro macchine virtuali di tipo DS13_v2 per il server applicazioni SAP con 8 vCPU, 56 GB di RAM e 400 GB di archiviazione temporanea, nonché un disco di archiviazione Premium da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="33698-206">Four VM type DS13_v2 for the SAP application server with 8x vCPUs, 56-GB RAM, and 400-GB temp storage, additionally one 128-GB premium storage disk.</span></span>

* <span data-ttu-id="33698-207">[Large](https://azure.com/e/f70fccf571e948c4b37d4fecc07cbf42): un sistema di grandi dimensioni è costituito da una macchina virtuale di tipo E32s_v3 per il server di database con 32 vCPU, 256 GB di RAM e 800 GB di archiviazione temporanea, nonché tre dischi di archiviazione Premium da 512 GB e uno da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="33698-207">[Large](https://azure.com/e/f70fccf571e948c4b37d4fecc07cbf42): A large system consists of VM type E32s_v3 for the database server with 32x vCPUs, 256-GB RAM and 800-GB temp storage, additionally three 512 GB and one 128-GB premium storage disks.</span></span> <span data-ttu-id="33698-208">Un server di istanza di SAP Central che usa una macchina virtuale di tipo DS11_v2 con 2 vCPU, 14 GB di RAM e 28 GB di archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="33698-208">An SAP Central Instance server using a DS11_v2 VM types with 2x vCPUs 14-GB RAM and 28-GB temp storage.</span></span> <span data-ttu-id="33698-209">Sei macchine virtuali di tipo DS14_v2 per il server applicazioni SAP con 16 vCPU, 112 GB di RAM e 224 GB di archiviazione temporanea, nonché sei dischi di archiviazione Premium da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="33698-209">Six VM type DS14_v2 for the SAP application servers with 16x vCPUs, 112 GB RAM, and 224 GB temp storage, additionally six 128-GB premium storage disk.</span></span>

* <span data-ttu-id="33698-210">[Extra Large](https://azure.com/e/58c636922cf94faf9650f583ff35e97b): un sistema di dimensioni molto grandi è costituito da una macchina virtuale di tipo M64s per il server di database con 64 vCPU, 1024 GB di RAM e 2000 GB di archiviazione temporanea, nonché sette dischi di archiviazione Premium da 1024 GB.</span><span class="sxs-lookup"><span data-stu-id="33698-210">[Extra Large](https://azure.com/e/58c636922cf94faf9650f583ff35e97b): An extra large system consists of the M64s VM type for the database server with 64x vCPUs, 1024 GB RAM, and 2000 GB temp storage, additionally seven 1024-GB premium storage disks.</span></span> <span data-ttu-id="33698-211">Un server di istanza di SAP Central che usa una macchina virtuale di tipo DS11_v2 con 2 vCPU, 14 GB di RAM e 28 GB di archiviazione temporanea.</span><span class="sxs-lookup"><span data-stu-id="33698-211">An SAP Central Instance server using a DS11_v2 VM types with 2x vCPUs 14-GB RAM and 28-GB temp storage.</span></span> <span data-ttu-id="33698-212">Dieci macchine virtuali di tipo DS14_v2 per il server applicazioni SAP con 16 vCPU, 112 GB di RAM e 224 GB di archiviazione temporanea, nonché dieci dischi di archiviazione Premium da 128 GB.</span><span class="sxs-lookup"><span data-stu-id="33698-212">10 VM type DS14_v2 for the SAP application servers with 16x vCPUs, 112 GB RAM, and 224 GB temp storage, additionally ten 128-GB premium storage disk.</span></span>

## <a name="deployment"></a><span data-ttu-id="33698-213">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="33698-213">Deployment</span></span>

<span data-ttu-id="33698-214">Usare il collegamento seguente per distribuire l'infrastruttura sottostante per questo scenario.</span><span class="sxs-lookup"><span data-stu-id="33698-214">Use the following link to deploy the underlying infrastructure for this scenario.</span></span>

<a
href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-3tier-distributed-ora%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

> [!NOTE]
> <span data-ttu-id="33698-215">SAP e Oracle non vengono installati durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="33698-215">SAP and Oracle are not installed during this deployment.</span></span> <span data-ttu-id="33698-216">Questi componenti dovranno essere distribuiti separatamente.</span><span class="sxs-lookup"><span data-stu-id="33698-216">You will need to deploy these components separately.</span></span>

## <a name="related-resources"></a><span data-ttu-id="33698-217">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="33698-217">Related resources</span></span>

<span data-ttu-id="33698-218">Per altre informazioni sull'esecuzione di carichi di lavoro di produzione SAP in Azure, vedere le architetture di riferimento seguenti:</span><span class="sxs-lookup"><span data-stu-id="33698-218">For other information about running SAP production workloads in Azure, review the following reference architectures:</span></span>
* [<span data-ttu-id="33698-219">SAP NetWeaver per AnyDB</span><span class="sxs-lookup"><span data-stu-id="33698-219">SAP NetWeaver for AnyDB</span></span>](/azure/architecture/reference-architectures/sap/sap-netweaver) 
* [<span data-ttu-id="33698-220">SAP S/4HANA</span><span class="sxs-lookup"><span data-stu-id="33698-220">SAP S/4HANA</span></span>](/azure/architecture/reference-architectures/sap/sap-s4hana)
* [<span data-ttu-id="33698-221">SAP HANA in istanze Large</span><span class="sxs-lookup"><span data-stu-id="33698-221">SAP HANA large instances</span></span>](/azure/architecture/reference-architectures/sap/hana-large-instances)

<!-- links -->
[architecture]: media/architecture-sap-production.png