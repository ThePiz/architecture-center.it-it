---
title: Esecuzione di simulazioni di fluidodinamica computazionale (CFD) in Azure
description: Eseguire simulazioni di fluidodinamica computazionale (CFD) in Azure.
author: mikewarr
ms.date: 09/20/2018
ms.openlocfilehash: f32e055838d6c62584130f61a0d92b06cc46ec63
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610635"
---
# <a name="running-computational-fluid-dynamics-cfd-simulations-on-azure"></a><span data-ttu-id="32d82-103">Esecuzione di simulazioni di fluidodinamica computazionale (CFD) in Azure</span><span class="sxs-lookup"><span data-stu-id="32d82-103">Running computational fluid dynamics (CFD) simulations on Azure</span></span>

<span data-ttu-id="32d82-104">Le simulazioni di fluidodinamica computazionale (CFD) richiedono un notevole tempo di calcolo e hardware specializzato.</span><span class="sxs-lookup"><span data-stu-id="32d82-104">Computational Fluid Dynamics (CFD) simulations require significant compute time along with specialized hardware.</span></span> <span data-ttu-id="32d82-105">Man mano che aumenta l'utilizzo dei cluster, crescono i tempi di simulazione e l'uso complessivo della griglia, causando problemi in termini di capacità di riserva e tempi di attesa.</span><span class="sxs-lookup"><span data-stu-id="32d82-105">As cluster usage increases, simulation times and overall grid use grow, leading to issues with spare capacity and long queue times.</span></span> <span data-ttu-id="32d82-106">L'aggiunta di hardware fisico può risultare costosa e non allineata alle fluttuazioni nell'utilizzo affrontate in un'azienda.</span><span class="sxs-lookup"><span data-stu-id="32d82-106">Adding physical hardware can be expensive, and may not align to the usage peaks and valleys that a business goes through.</span></span> <span data-ttu-id="32d82-107">Grazie all'uso di Azure, molti di questi problemi possono essere risolti senza investimenti di capitale.</span><span class="sxs-lookup"><span data-stu-id="32d82-107">By taking advantage of Azure, many of these challenges can be overcome with no capital expenditure.</span></span>

<span data-ttu-id="32d82-108">Azure fornisce l'hardware necessario per eseguire i processi CFD in macchine virtuali CPU e GPU.</span><span class="sxs-lookup"><span data-stu-id="32d82-108">Azure provides the hardware you need to run your CFD jobs on both GPU and CPU virtual machines.</span></span> <span data-ttu-id="32d82-109">Le dimensioni di VM abilitate per l'accesso diretto a memoria remota (RDMA) usano la rete InfiniBand FDR, che fornisce comunicazione MPI (Message Passing Interface) a bassa latenza.</span><span class="sxs-lookup"><span data-stu-id="32d82-109">RDMA (Remote Direct Memory Access) enabled VM sizes have FDR InfiniBand-based networking which allows for low latency MPI (Message Passing Interface) communication.</span></span> <span data-ttu-id="32d82-110">In combinazione con Avere vFXT, che fornisce un file system in cluster su scala aziendale, consente ai clienti di garantire la massima velocità effettiva per le operazioni di lettura in Azure.</span><span class="sxs-lookup"><span data-stu-id="32d82-110">Combined with the Avere vFXT, which provides an enterprise-scale clustered file system, customers can ensure maximum throughput for read operations in Azure.</span></span>

<span data-ttu-id="32d82-111">Per semplificare la creazione, la gestione e l'ottimizzazione di cluster HPC, è possibile usare Azure CycleCloud per effettuare il provisioning dei cluster e orchestrare i dati in scenari ibridi e cloud.</span><span class="sxs-lookup"><span data-stu-id="32d82-111">To simplify the creation, management, and optimization of HPC clusters, Azure CycleCloud can be used to provision clusters and orchestrate data in both hybrid and cloud scenarios.</span></span> <span data-ttu-id="32d82-112">Monitorando i processi in sospeso, CycleCloud avvierà automaticamente il calcolo su richiesta, in cui si paga solo per le risorse usate, connesso all'utilità di pianificazione del carico di lavoro scelta.</span><span class="sxs-lookup"><span data-stu-id="32d82-112">By monitoring the pending jobs, CycleCloud will automatically launch on-demand compute, where you only pay for what you use, connected to the workload scheduler of your choice.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="32d82-113">Casi d'uso pertinenti</span><span class="sxs-lookup"><span data-stu-id="32d82-113">Relevant use cases</span></span>

<span data-ttu-id="32d82-114">Gli altri settori pertinenti per le applicazioni CFD includono:</span><span class="sxs-lookup"><span data-stu-id="32d82-114">Other relevant industries for CFD applications include:</span></span>

* <span data-ttu-id="32d82-115">Aeronautica</span><span class="sxs-lookup"><span data-stu-id="32d82-115">Aeronautics</span></span>
* <span data-ttu-id="32d82-116">Automobilistico</span><span class="sxs-lookup"><span data-stu-id="32d82-116">Automotive</span></span>
* <span data-ttu-id="32d82-117">Sistemi HVAC di edifici</span><span class="sxs-lookup"><span data-stu-id="32d82-117">Building HVAC</span></span>
* <span data-ttu-id="32d82-118">Petrolio e gas</span><span class="sxs-lookup"><span data-stu-id="32d82-118">Oil and gas</span></span>
* <span data-ttu-id="32d82-119">Scienze biologiche</span><span class="sxs-lookup"><span data-stu-id="32d82-119">Life sciences</span></span>

## <a name="architecture"></a><span data-ttu-id="32d82-120">Architettura</span><span class="sxs-lookup"><span data-stu-id="32d82-120">Architecture</span></span>

![Diagramma dell'architettura][architecture]

<span data-ttu-id="32d82-122">Questo diagramma mostra una panoramica generale di una progettazione ibrida tipica che fornisce il monitoraggio dei processi dei nodi su richiesta in Azure:</span><span class="sxs-lookup"><span data-stu-id="32d82-122">This diagram shows a high-level overview of a typical hybrid design providing job monitoring of the on-demand nodes in Azure:</span></span>

1. <span data-ttu-id="32d82-123">Connettersi al server Azure CycleCloud per configurare il cluster.</span><span class="sxs-lookup"><span data-stu-id="32d82-123">Connect to the Azure CycleCloud server to configure the cluster.</span></span>
2. <span data-ttu-id="32d82-124">Configurare e creare il nodo head del cluster, usando macchine virtuali abilitate per RDMA per la comunicazione MPI.</span><span class="sxs-lookup"><span data-stu-id="32d82-124">Configure and create the cluster head node, using RDMA enabled machines for MPI.</span></span>
3. <span data-ttu-id="32d82-125">Aggiungere e configurare il nodo head locale.</span><span class="sxs-lookup"><span data-stu-id="32d82-125">Add and configure the on-premises head node.</span></span>
4. <span data-ttu-id="32d82-126">Se le risorse sono insufficienti, Azure CycleCloud aumenterà (o ridurrà) le risorse di calcolo in Azure.</span><span class="sxs-lookup"><span data-stu-id="32d82-126">If there are insufficient resources, Azure CycleCloud will scale up (or down) compute resources in Azure.</span></span> <span data-ttu-id="32d82-127">È possibile definire un limite predeterminato per evitare la sovrassegnazione.</span><span class="sxs-lookup"><span data-stu-id="32d82-127">A predetermined limit can be defined to prevent over allocation.</span></span>
5. <span data-ttu-id="32d82-128">Attività allocate ai nodi di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="32d82-128">Tasks allocated to the execute nodes.</span></span>
6. <span data-ttu-id="32d82-129">Dati memorizzati nella cache di Azure dal server NFS locale.</span><span class="sxs-lookup"><span data-stu-id="32d82-129">Data cached in Azure from on-premises NFS server.</span></span>
7. <span data-ttu-id="32d82-130">Dati letti dalla cache Avere vFXT per Azure.</span><span class="sxs-lookup"><span data-stu-id="32d82-130">Data read in from the Avere vFXT for Azure cache.</span></span>
8. <span data-ttu-id="32d82-131">Informazioni di attività e processi inoltrate al server Azure CycleCloud.</span><span class="sxs-lookup"><span data-stu-id="32d82-131">Job and task information relayed to the Azure CycleCloud server.</span></span>

### <a name="components"></a><span data-ttu-id="32d82-132">Componenti</span><span class="sxs-lookup"><span data-stu-id="32d82-132">Components</span></span>

* <span data-ttu-id="32d82-133">[Azure CycleCloud][cyclecloud], uno strumento per la creazione, la gestione, l'uso e l'ottimizzazione di cluster HPC e Big Compute in Azure.</span><span class="sxs-lookup"><span data-stu-id="32d82-133">[Azure CycleCloud][cyclecloud] a tool for creating, managing, operating, and optimizing HPC and Big Compute clusters in Azure.</span></span>
* <span data-ttu-id="32d82-134">[Avere vFXT per Azure][avere] viene usato per fornire un file system in cluster su scala aziendale creato per il cloud.</span><span class="sxs-lookup"><span data-stu-id="32d82-134">[Avere vFXT on Azure][avere] is used to provide an enterprise-scale clustered file system built for the cloud.</span></span>
* <span data-ttu-id="32d82-135">Le [macchine virtuali di Azure][vms] vengono usate per creare un set statico di istanze di calcolo.</span><span class="sxs-lookup"><span data-stu-id="32d82-135">[Azure Virtual Machines (VMs)][vms] are used to create a static set of compute instances.</span></span>
* <span data-ttu-id="32d82-136">I [set di scalabilità di macchine virtuali][vmss] forniscono un gruppo di macchine virtuali identiche di cui è possibile aumentare o ridurre le prestazioni tramite Azure CycleCloud.</span><span class="sxs-lookup"><span data-stu-id="32d82-136">[Virtual Machine Scale Sets (virtual machine scale set)][vmss] provide a group of identical VMs capable of being scaled up or down by Azure CycleCloud.</span></span>
* <span data-ttu-id="32d82-137">Gli [account di archiviazione di Azure](/azure/storage/common/storage-introduction) vengono usati per la sincronizzazione e la conservazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="32d82-137">[Azure Storage accounts](/azure/storage/common/storage-introduction) are used for synchronization and data retention.</span></span>
* <span data-ttu-id="32d82-138">Le [reti virtuali](/azure/virtual-network/virtual-networks-overview) consentono a diversi tipi di risorse di Azure, ad esempio macchine virtuali di Azure, di comunicare in modo sicuro tra loro, con Internet e con le reti locali.</span><span class="sxs-lookup"><span data-stu-id="32d82-138">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) enable many types of Azure resources, such as Azure Virtual Machines (VMs), to securely communicate with each other, the internet, and on-premises networks.</span></span>

### <a name="alternatives"></a><span data-ttu-id="32d82-139">Alternative</span><span class="sxs-lookup"><span data-stu-id="32d82-139">Alternatives</span></span>

<span data-ttu-id="32d82-140">I clienti possono anche usare Azure CycleCloud per creare una griglia interamente in Azure.</span><span class="sxs-lookup"><span data-stu-id="32d82-140">Customers can also use Azure CycleCloud to create a grid entirely in Azure.</span></span> <span data-ttu-id="32d82-141">In questa configurazione, il server Azure CycleCloud viene eseguito all'interno della sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="32d82-141">In this setup, the Azure CycleCloud server is run within your Azure subscription.</span></span>

<span data-ttu-id="32d82-142">Per un approccio moderno alle applicazioni, in cui non è necessaria la gestione di un'utilità di pianificazione del carico di lavoro, [Azure Batch][batch] può essere di aiuto.</span><span class="sxs-lookup"><span data-stu-id="32d82-142">For a modern application approach where management of a workload scheduler is not needed, [Azure Batch][batch] can help.</span></span> <span data-ttu-id="32d82-143">Azure Batch può eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud.</span><span class="sxs-lookup"><span data-stu-id="32d82-143">Azure Batch can run large-scale parallel and high-performance computing (HPC) applications efficiently in the cloud.</span></span> <span data-ttu-id="32d82-144">Azure Batch consente di definire le risorse di calcolo di Azure per eseguire le applicazioni in parallelo o in scala, senza necessità di configurare o gestire manualmente l'infrastruttura.</span><span class="sxs-lookup"><span data-stu-id="32d82-144">Azure Batch allows you to define the Azure compute resources to execute your applications in parallel or at scale without manually configuring or managing infrastructure.</span></span> <span data-ttu-id="32d82-145">Azure Batch pianifica le attività a elevato utilizzo di calcolo e aggiunge o rimuove dinamicamente le risorse in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="32d82-145">Azure Batch schedules compute-intensive tasks and dynamically adds and removes compute resources based on your requirements.</span></span>

### <a name="scalability-and-security"></a><span data-ttu-id="32d82-146">Scalabilità e sicurezza</span><span class="sxs-lookup"><span data-stu-id="32d82-146">Scalability, and Security</span></span>

<span data-ttu-id="32d82-147">Il ridimensionamento dei nodi di esecuzione in Azure CycleCloud può essere eseguito manualmente o tramite la scalabilità automatica.</span><span class="sxs-lookup"><span data-stu-id="32d82-147">Scaling the execute nodes on Azure CycleCloud can be accomplished either manually or using autoscaling.</span></span> <span data-ttu-id="32d82-148">Per altre informazioni, vedere l'articolo sulla [scalabilità automatica in CycleCloud][cycle-scale].</span><span class="sxs-lookup"><span data-stu-id="32d82-148">For more information, see [CycleCloud Autoscaling][cycle-scale].</span></span>

<span data-ttu-id="32d82-149">Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].</span><span class="sxs-lookup"><span data-stu-id="32d82-149">For general guidance on designing secure solutions, see the [Azure security documentation][security].</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="32d82-150">Distribuire lo scenario</span><span class="sxs-lookup"><span data-stu-id="32d82-150">Deploy this scenario</span></span>

<span data-ttu-id="32d82-151">Prima della distribuzione in Azure occorre soddisfare alcuni prerequisiti.</span><span class="sxs-lookup"><span data-stu-id="32d82-151">Before deploying in Azure, some pre-requisites are required.</span></span> <span data-ttu-id="32d82-152">Seguire questi passaggi prima di distribuire il modello di Resource Manager:</span><span class="sxs-lookup"><span data-stu-id="32d82-152">Follow these steps before deploying the Resource Manager template:</span></span>
1. <span data-ttu-id="32d82-153">Creare un'[entità servizio][cycle-svcprin] per recuperare appId, displayName, nome, password e tenant.</span><span class="sxs-lookup"><span data-stu-id="32d82-153">Create a [service principal][cycle-svcprin] for retrieving the appId, displayName, name, password, and tenant.</span></span>
2. <span data-ttu-id="32d82-154">Generare una [coppia di chiavi SSH][cycle-ssh] per accedere in modo sicuro al server CycleCloud.</span><span class="sxs-lookup"><span data-stu-id="32d82-154">Generate an [SSH key pair][cycle-ssh] to sign in securely to the CycleCloud server.</span></span>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FCycleCloudCommunity%2Fcyclecloud_arm%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

3. <span data-ttu-id="32d82-155">[Accedere al server CycleCloud][cycle-login] per configurare e creare un nuovo cluster.</span><span class="sxs-lookup"><span data-stu-id="32d82-155">[Log into the CycleCloud server][cycle-login] to configure and create a new cluster.</span></span>
4. <span data-ttu-id="32d82-156">[Creare un cluster][cycle-create].</span><span class="sxs-lookup"><span data-stu-id="32d82-156">[Create a cluster][cycle-create].</span></span>

<span data-ttu-id="32d82-157">La cache Avere è una soluzione opzionale che può aumentare notevolmente la velocità effettiva di lettura per i dati del processo dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="32d82-157">The Avere Cache is an optional solution that can drastically increase read throughput for the application job data.</span></span> <span data-ttu-id="32d82-158">Avere vFXT per Azure risolve il problema di eseguire queste applicazioni aziendali HPC nel cloud e allo stesso tempo usare i dati archiviati in locale o nell'archiviazione BLOB di Azure.</span><span class="sxs-lookup"><span data-stu-id="32d82-158">Avere vFXT for Azure solves the problem of running these enterprise HPC applications in the cloud while leveraging data stored on-premises or in Azure Blob storage.</span></span>

<span data-ttu-id="32d82-159">Per le organizzazioni che stanno pianificando un'infrastruttura ibrida, con archiviazione locale e cloud computing, le applicazioni HPC possono eseguire il burst in Azure usando i dati archiviati in dispositivi NAS e attivare CPU virtuali in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="32d82-159">For organizations that are planning for a hybrid infrastructure with both on-premises storage and cloud computing, HPC applications can “burst” into Azure using data stored in NAS devices and spin up virtual CPUs as needed.</span></span> <span data-ttu-id="32d82-160">Il set di dati non viene mai spostato completamente nel cloud.</span><span class="sxs-lookup"><span data-stu-id="32d82-160">The data set is never moved completely into the cloud.</span></span> <span data-ttu-id="32d82-161">I byte richiesti vengono temporaneamente memorizzati nella cache usando un cluster Avere durante l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="32d82-161">The requested bytes are temporarily cached using an Avere cluster during processing.</span></span>

<span data-ttu-id="32d82-162">Per impostare e configurare un'installazione di Avere vFXT, seguire le indicazioni della [guida alla configurazione e installazione di Avere][avere].</span><span class="sxs-lookup"><span data-stu-id="32d82-162">To set up and configure an Avere vFXT installation, follow the [Avere Setup and Configuration guide][avere].</span></span>

## <a name="pricing"></a><span data-ttu-id="32d82-163">Prezzi</span><span class="sxs-lookup"><span data-stu-id="32d82-163">Pricing</span></span>

<span data-ttu-id="32d82-164">Il costo dell'esecuzione di un'implementazione HPC tramite un server CycleCloud varia in base a diversi fattori.</span><span class="sxs-lookup"><span data-stu-id="32d82-164">The cost of running an HPC implementation using CycleCloud server will vary depending on a number of factors.</span></span> <span data-ttu-id="32d82-165">Ad esempio, CycleCloud viene addebitato in base alla quantità di tempo di calcolo usato, con il server master e il server CycleCloud in genere costantemente allocati e in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="32d82-165">For example, CycleCloud is charged by the amount of compute time that is used, with the Master and CycleCloud server typically being constantly allocated and running.</span></span> <span data-ttu-id="32d82-166">Il costo dei nodi di esecuzione dipenderà dalle relative dimensioni e dal tempo per cui sono operativi.</span><span class="sxs-lookup"><span data-stu-id="32d82-166">The cost of running the Execute nodes will depend on how long these are up and running as well as what size is used.</span></span> <span data-ttu-id="32d82-167">Si applicano anche i normali costi di Azure per l'archiviazione e la rete.</span><span class="sxs-lookup"><span data-stu-id="32d82-167">The normal Azure charges for storage and networking also apply.</span></span>

<span data-ttu-id="32d82-168">Questo scenario illustra come è possibile eseguire applicazioni CFD in Azure. Le macchine virtuali richiederanno funzionalità RDMA, disponibili solo in macchine virtuali di dimensioni specifiche.</span><span class="sxs-lookup"><span data-stu-id="32d82-168">This scenario shows how CFD applications can be run in Azure, so the machines will require RDMA functionality, which is only available on specific VM sizes.</span></span> <span data-ttu-id="32d82-169">Di seguito sono riportati esempi dei costi che potrebbero essere addebitati per un set di scalabilità allocato in modo continuativo per otto ore al giorno per un mese, con dati in uscita per 1 TB.</span><span class="sxs-lookup"><span data-stu-id="32d82-169">The following are examples of costs that could be incurred for a scale set that is allocated continuously for eight hours per day for one month, with data egress of 1 TB.</span></span> <span data-ttu-id="32d82-170">Include anche i costi per il server Azure CycleCloud e l'installazione di Avere vFXT per Azure:</span><span class="sxs-lookup"><span data-stu-id="32d82-170">It also includes pricing for the Azure CycleCloud server and the Avere vFXT for Azure install:</span></span>

* <span data-ttu-id="32d82-171">Area: Europa settentrionale</span><span class="sxs-lookup"><span data-stu-id="32d82-171">Region: North Europe</span></span>
* <span data-ttu-id="32d82-172">Server Azure CycleCloud: 1 x D3 Standard (4 x CPU, 14 GB di memoria, HDD Standard da 32 GB)</span><span class="sxs-lookup"><span data-stu-id="32d82-172">Azure CycleCloud Server: 1 x Standard D3 (4 x CPUs, 14 GB Memory, Standard HDD 32 GB)</span></span>
* <span data-ttu-id="32d82-173">Server master Azure CycleCloud: 1 x D12 v Standard (4 x CPU, 28 GB di memoria, HDD Standard da 32 GB)</span><span class="sxs-lookup"><span data-stu-id="32d82-173">Azure CycleCloud Master Server: 1 x Standard D12 v (4 x CPUs, 28 GB Memory, Standard HDD 32 GB)</span></span>
* <span data-ttu-id="32d82-174">Matrice di nodi di Azure CycleCloud: 10 x H16r Standard (16 x CPU, 112 GB di memoria)</span><span class="sxs-lookup"><span data-stu-id="32d82-174">Azure CycleCloud Node Array: 10 x Standard H16r (16 x CPUs, 112 GB Memory)</span></span>
* <span data-ttu-id="32d82-175">Cluster Avere vFXT in Azure: 3 x D16s v3 (sistema operativo 200 GB, disco dati SSD Premium da 1 TB)</span><span class="sxs-lookup"><span data-stu-id="32d82-175">Avere vFXT on Azure Cluster: 3 x D16s v3 (200 GB OS, Premium SSD 1-TB data disk)</span></span>
* <span data-ttu-id="32d82-176">Dati in uscita: 1 TB</span><span class="sxs-lookup"><span data-stu-id="32d82-176">Data Egress: 1 TB</span></span>

<span data-ttu-id="32d82-177">Vedere questa [stima dei prezzi][pricing] per l'hardware elencato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="32d82-177">Review this [price estimate][pricing] for the hardware listed above.</span></span>

## <a name="next-steps"></a><span data-ttu-id="32d82-178">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="32d82-178">Next Steps</span></span>

<span data-ttu-id="32d82-179">Dopo aver distribuito l'esempio, leggere altre informazioni su [Azure CycleCloud][cyclecloud].</span><span class="sxs-lookup"><span data-stu-id="32d82-179">Once you've deployed the sample, learn more about [Azure CycleCloud][cyclecloud].</span></span>

## <a name="related-resources"></a><span data-ttu-id="32d82-180">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="32d82-180">Related resources</span></span>

* <span data-ttu-id="32d82-181">[Istanze di macchine virtuali con supporto per RDMA][rdma]</span><span class="sxs-lookup"><span data-stu-id="32d82-181">[RDMA Capable Machine Instances][rdma]</span></span>
* <span data-ttu-id="32d82-182">[Personalizzazione di un'istanza di macchina virtuale con supporto per RDMA][rdma-custom]</span><span class="sxs-lookup"><span data-stu-id="32d82-182">[Customizing an RDMA Instance VM][rdma-custom]</span></span>

<!-- links -->
[architecture]: ./media/architecture-hpc-cfd.png
[calculator]: https://azure.com/e/
[availability]: /azure/architecture/checklist/availability
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[vmss]: /azure/virtual-machine-scale-sets/overview
[cyclecloud]: /azure/cyclecloud/
[rdma]: /azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances
[gpu]: /azure/virtual-machines/windows/sizes-gpu
[hpcsizes]: /azure/virtual-machines/windows/sizes-hpc
[vms]: /azure/virtual-machines/
[low-pri]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority
[batch]: /azure/batch/
[avere]: https://github.com/Azure/Avere/blob/master/README.md
[cycle-prereq]: /azure/cyclecloud/quickstart-install-cyclecloud#prerequisites
[cycle-svcprin]: /azure/cyclecloud/quickstart-install-cyclecloud#service-principal
[cycle-ssh]: /azure/cyclecloud/quickstart-install-cyclecloud#ssh-keypair
[cycle-login]: /azure/cyclecloud/quickstart-install-cyclecloud#log-into-the-cyclecloud-application-server
[cycle-create]: /azure/cyclecloud/quickstart-create-and-run-cluster
[rdma]: /azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances
[rdma-custom]: /azure/virtual-machines/linux/classic/rdma-cluster#customize-the-vm
[pricing]: https://azure.com/e/53030a04a2ab47a289156e2377a4247a
[cycle-scale]: /azure/cyclecloud/autoscale
