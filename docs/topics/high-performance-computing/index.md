---
title: HPC (High Performance Computing) in Azure
description: Guida alla creazione di carichi di lavoro HPC in esecuzione in Azure
author: adamboeglin
ms.date: 2/4/2019
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD026 -->

# <a name="high-performance-computing-hpc-on-azure"></a><span data-ttu-id="15637-103">HPC (High Performance Computing) in Azure</span><span class="sxs-lookup"><span data-stu-id="15637-103">High Performance Computing (HPC) on Azure</span></span>

## <a name="introduction-to-hpc"></a><span data-ttu-id="15637-104">Introduzione all'HPC</span><span class="sxs-lookup"><span data-stu-id="15637-104">Introduction to HPC</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.youtube.com/embed/rKURT32faJk]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="15637-105">L'High Performance Computing (HPC), anche detto "Big Compute", prevede l'uso di un numero elevato di computer basati su CPU o GPU per risolvere attività matematiche complesse.</span><span class="sxs-lookup"><span data-stu-id="15637-105">High Performance Computing (HPC), also called "Big Compute", uses a large number of CPU or GPU-based computers to solve complex mathematical tasks.</span></span>

<span data-ttu-id="15637-106">In molti settori ci si avvale dell'HPC per risolvere i problemi più difficili,</span><span class="sxs-lookup"><span data-stu-id="15637-106">Many industries use HPC to solve some of their most difficult problems.</span></span>  <span data-ttu-id="15637-107">con carichi di lavoro come:</span><span class="sxs-lookup"><span data-stu-id="15637-107">These include workloads such as:</span></span>

- <span data-ttu-id="15637-108">Genomica</span><span class="sxs-lookup"><span data-stu-id="15637-108">Genomics</span></span>
- <span data-ttu-id="15637-109">Simulazioni di petrolio e gas</span><span class="sxs-lookup"><span data-stu-id="15637-109">Oil and gas simulations</span></span>
- <span data-ttu-id="15637-110">Finanza</span><span class="sxs-lookup"><span data-stu-id="15637-110">Finance</span></span>
- <span data-ttu-id="15637-111">Progettazione di semiconduttori</span><span class="sxs-lookup"><span data-stu-id="15637-111">Semiconductor design</span></span>
- <span data-ttu-id="15637-112">Ingegneria</span><span class="sxs-lookup"><span data-stu-id="15637-112">Engineering</span></span>
- <span data-ttu-id="15637-113">Modellazione meteo</span><span class="sxs-lookup"><span data-stu-id="15637-113">Weather modeling</span></span>

### <a name="how-is-hpc-different-on-the-cloud"></a><span data-ttu-id="15637-114">Quali sono le differenze dell'HPC sul cloud?</span><span class="sxs-lookup"><span data-stu-id="15637-114">How is HPC different on the cloud?</span></span>

<span data-ttu-id="15637-115">Una delle principali differenze tra i sistemi HPC locali e quelli nel cloud è la possibilità di aggiungere e rimuovere dinamicamente le risorse in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="15637-115">One of the primary differences between an on-premise HPC system and one in the cloud is the ability for resources to dynamically be added and removed as they're needed.</span></span>  <span data-ttu-id="15637-116">La scalabilità dinamica rimuove il collo di bottiglia della capacità di calcolo e consente invece ai clienti di dimensionare correttamente l'infrastruttura per i loro requisiti.</span><span class="sxs-lookup"><span data-stu-id="15637-116">Dynamic scaling removes compute capacity as a bottleneck and instead allow customers to right size their infrastructure for the requirements of their jobs.</span></span>

<span data-ttu-id="15637-117">Gli articoli seguenti contengono altri dettagli sulla scalabilità dinamica.</span><span class="sxs-lookup"><span data-stu-id="15637-117">The following articles provide more detail about this dynamic scaling capability.</span></span>

- [<span data-ttu-id="15637-118">Stile di architettura Big Compute</span><span class="sxs-lookup"><span data-stu-id="15637-118">Big Compute Architecture Style</span></span>](/azure/architecture/guide/architecture-styles/big-compute?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-119">Procedure consigliate per la scalabilità automatica</span><span class="sxs-lookup"><span data-stu-id="15637-119">Autoscaling best practices</span></span>](/azure/architecture/best-practices/auto-scaling?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="implementation-checklist"></a><span data-ttu-id="15637-120">Elenco di controllo per l'implementazione</span><span class="sxs-lookup"><span data-stu-id="15637-120">Implementation checklist</span></span>

<span data-ttu-id="15637-121">Se si intende implementare una soluzione HPC in Azure, assicurarsi di consultare gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="15637-121">As you're looking to implement your own HPC solution on Azure, ensure you're reviewed the following topics:</span></span>

<!-- markdownlint-disable MD032 -->

> [!div class="checklist"]
> - <span data-ttu-id="15637-122">Scegliere l'[architettura](#infrastructure) appropriata in base ai requisiti</span><span class="sxs-lookup"><span data-stu-id="15637-122">Choose the appropriate [architecture](#infrastructure) based on your requirements</span></span>
> - <span data-ttu-id="15637-123">Identificare le opzioni di [calcolo](#compute) appropriate per uno specifico carico di lavoro</span><span class="sxs-lookup"><span data-stu-id="15637-123">Know which [compute](#compute) options is right for your workload</span></span>
> - <span data-ttu-id="15637-124">Identificare la soluzione di [archiviazione](#storage) che soddisfi specifiche esigenze</span><span class="sxs-lookup"><span data-stu-id="15637-124">Identify the right [storage](#storage) solution that meets your needs</span></span>
> - <span data-ttu-id="15637-125">Decidere come [gestire](#management) tutte le risorse</span><span class="sxs-lookup"><span data-stu-id="15637-125">Decide how you're going to [manage](#management) all your resources</span></span>
> - <span data-ttu-id="15637-126">Ottimizzare l'[applicazione](#hpc-applications) per il cloud</span><span class="sxs-lookup"><span data-stu-id="15637-126">Optimize your [application](#hpc-applications) for the cloud</span></span>
> - <span data-ttu-id="15637-127">[Proteggere](#security) l'infrastruttura</span><span class="sxs-lookup"><span data-stu-id="15637-127">[Secure](#security) your Infrastructure</span></span>

<!-- markdownlint-enable MD032 -->

## <a name="infrastructure"></a><span data-ttu-id="15637-128">Infrastruttura</span><span class="sxs-lookup"><span data-stu-id="15637-128">Infrastructure</span></span>

<span data-ttu-id="15637-129">Per creare un sistema HPC, sono necessari diversi componenti dell'infrastruttura.</span><span class="sxs-lookup"><span data-stu-id="15637-129">There are a number of infrastructure components necessary to build an HPC system.</span></span>  <span data-ttu-id="15637-130">I componenti sottostanti sono le risorse di calcolo, archiviazione e rete, indipendentemente da come si sceglie di gestire i carichi di lavoro HPC.</span><span class="sxs-lookup"><span data-stu-id="15637-130">Compute, Storage, and Networking provide the underlying components, no matter how you choose to manage your HPC workloads.</span></span>

### <a name="example-hpc-architectures"></a><span data-ttu-id="15637-131">Esempi di architetture HPC</span><span class="sxs-lookup"><span data-stu-id="15637-131">Example HPC architectures</span></span>

<span data-ttu-id="15637-132">È possibile progettare e implementare l'architettura HPC in vari modi in Azure.</span><span class="sxs-lookup"><span data-stu-id="15637-132">There are a number of different ways to design and implement your HPC architecture on Azure.</span></span>  <span data-ttu-id="15637-133">Le applicazioni HPC possono offrire scalabilità fino a migliaia di core di calcolo, estendere i cluster locali o venire eseguite come soluzioni cloud native al 100%.</span><span class="sxs-lookup"><span data-stu-id="15637-133">HPC applications can scale to thousands of compute cores, extend on-premises clusters, or run as a 100% cloud native solution.</span></span>

<span data-ttu-id="15637-134">Gli scenari seguenti descrivono alcuni modi comuni in cui vengono sviluppate le soluzioni HPC.</span><span class="sxs-lookup"><span data-stu-id="15637-134">The following scenarios outline a few of the common ways HPC solutions are built.</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/apps/hpc-saas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/apps/media/architecture-hpc-saas.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="15637-135">Servizi CAE (Computer-Aided Engineering) in Azure</span><span class="sxs-lookup"><span data-stu-id="15637-135">Computer-aided engineering services on Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="15637-136">Fornire una piattaforma software come un servizio (SaaS) per CAE (Computer-Aided Engineering) in Azure.</span><span class="sxs-lookup"><span data-stu-id="15637-136">Provide a software-as-a-service (SaaS) platform for computer-aided engineering (CAE) on Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/infrastructure/hpc-cfd?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/infrastructure/media/architecture-hpc-cfd.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="15637-137">Simulazioni di fluidodinamica computazionale in Azure</span><span class="sxs-lookup"><span data-stu-id="15637-137">Computational fluid dynamics (CFD) simulations on Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="15637-138">Eseguire simulazioni di fluidodinamica computazionale (CFD) in Azure.</span><span class="sxs-lookup"><span data-stu-id="15637-138">Execute computational fluid dynamics (CFD) simulations on Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/infrastructure/video-rendering?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/infrastructure/media/architecture-video-rendering.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="15637-139">Rendering di video 3D in Azure</span><span class="sxs-lookup"><span data-stu-id="15637-139">3D video rendering on Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="15637-140">Eseguire carichi di lavoro HPC nativi in Azure con il servizio Azure Batch</span><span class="sxs-lookup"><span data-stu-id="15637-140">Run native HPC workloads in Azure using the Azure Batch service</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

### <a name="compute"></a><span data-ttu-id="15637-141">Calcolo</span><span class="sxs-lookup"><span data-stu-id="15637-141">Compute</span></span>

<span data-ttu-id="15637-142">Azure offre un'ampia gamma di dimensioni ottimizzate per i carichi di lavoro a uso intensivo di CPU e GPU.</span><span class="sxs-lookup"><span data-stu-id="15637-142">Azure offers a range of sizes that are optimized for both CPU & GPU intensive workloads.</span></span>

#### <a name="cpu-based-virtual-machines"></a><span data-ttu-id="15637-143">Macchine virtuali basate su CPU</span><span class="sxs-lookup"><span data-stu-id="15637-143">CPU-based virtual machines</span></span>

- [<span data-ttu-id="15637-144">Macchine virtuali di Linux</span><span class="sxs-lookup"><span data-stu-id="15637-144">Linux VMs</span></span>](/azure/virtual-machines/linux/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- <span data-ttu-id="15637-145">[Macchine virtuali di Windows](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)</span><span class="sxs-lookup"><span data-stu-id="15637-145">[Windows VM's](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) VMs</span></span>
  
#### <a name="gpu-enabled-virtual-machines"></a><span data-ttu-id="15637-146">Macchine virtuali abilitate per GPU</span><span class="sxs-lookup"><span data-stu-id="15637-146">GPU-enabled virtual machines</span></span>

<span data-ttu-id="15637-147">Le VM serie N sono dotate di GPU con tecnologia NVIDIA progettate per applicazioni a elevato utilizzo di calcolo o di grafica, inclusi apprendimento e visualizzazione basati sull'intelligenza artificiale (AI).</span><span class="sxs-lookup"><span data-stu-id="15637-147">N-series VMs feature NVIDIA GPUs designed for compute-intensive or graphics-intensive applications including artificial intelligence (AI) learning and visualization.</span></span>

- [<span data-ttu-id="15637-148">Macchine virtuali di Linux</span><span class="sxs-lookup"><span data-stu-id="15637-148">Linux VMs</span></span>](/azure/virtual-machines/linux/sizes-gpu?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-149">Macchine virtuali di Windows</span><span class="sxs-lookup"><span data-stu-id="15637-149">Windows VMs</span></span>](/azure/virtual-machines/windows/sizes-gpu?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

### <a name="storage"></a><span data-ttu-id="15637-150">Archiviazione</span><span class="sxs-lookup"><span data-stu-id="15637-150">Storage</span></span>

<span data-ttu-id="15637-151">I carichi di lavoro Batch e HPC su larga scala hanno richieste di archiviazione dati e di accesso che superano le capacità dei tradizionali file system cloud.</span><span class="sxs-lookup"><span data-stu-id="15637-151">Large-scale Batch and HPC workloads have demands for data storage and access that exceed the capabilities of traditional cloud file systems.</span></span>  <span data-ttu-id="15637-152">Per gestire le esigenze di velocità e capacità delle applicazioni HPC in Azure, sono disponibili numerose soluzioni</span><span class="sxs-lookup"><span data-stu-id="15637-152">There are a number of solutions to manage both the speed and capacity needs of HPC applications on Azure</span></span>

- <span data-ttu-id="15637-153">[Avere vFXT](https://azure.microsoft.com/services/storage/avere-vfxt/) per l'archiviazione dei dati più veloce e accessibile per l'HPC nei dispositivi perimetrali</span><span class="sxs-lookup"><span data-stu-id="15637-153">[Avere vFXT](https://azure.microsoft.com/services/storage/avere-vfxt/) for faster, more accessible data storage for high-performance computing at the edge</span></span>
- [<span data-ttu-id="15637-154">BeeGFS</span><span class="sxs-lookup"><span data-stu-id="15637-154">BeeGFS</span></span>](https://azure.microsoft.com/resources/implement-glusterfs-on-azure/)
- [<span data-ttu-id="15637-155">Macchine virtuali ottimizzate per l'archiviazione</span><span class="sxs-lookup"><span data-stu-id="15637-155">Storage Optimized Virtual Machines</span></span>](/azure/virtual-machines/windows/sizes-storage?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-156">Archiviazione BLOB, tabelle e code</span><span class="sxs-lookup"><span data-stu-id="15637-156">Blob, table, and queue storage</span></span>](/azure/storage/storage-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-157">Archiviazione file SMB di Azure</span><span class="sxs-lookup"><span data-stu-id="15637-157">Azure SMB File storage</span></span>](/azure/storage/files/storage-files-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-158">Intel Cloud Edition Lustre</span><span class="sxs-lookup"><span data-stu-id="15637-158">Intel Cloud Edition Lustre</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/intel.intel-cloud-edition-gs)

<span data-ttu-id="15637-159">Per altre informazioni di confronto tra Lustre, GlusterFS e BeeGFS in Azure, vedere l'[eBook sui file system paralleli in Azure](https://blogs.msdn.microsoft.com/azurecat/2018/06/11/azurecat-ebook-parallel-virtual-file-systems-on-microsoft-azure/)</span><span class="sxs-lookup"><span data-stu-id="15637-159">For more information comparing Lustre, GlusterFS, and BeeGFS on Azure, review the [Parallel Files Systems on Azure eBook](https://blogs.msdn.microsoft.com/azurecat/2018/06/11/azurecat-ebook-parallel-virtual-file-systems-on-microsoft-azure/)</span></span>

### <a name="networking"></a><span data-ttu-id="15637-160">Rete</span><span class="sxs-lookup"><span data-stu-id="15637-160">Networking</span></span>

<span data-ttu-id="15637-161">Le VM H16r, H16mr, A8, e A9 possono connettersi a una rete RDMA back-end a velocità effettiva elevata.</span><span class="sxs-lookup"><span data-stu-id="15637-161">H16r, H16mr, A8, and A9 VMs can connect to a high throughput back-end RDMA network.</span></span> <span data-ttu-id="15637-162">Questa rete può migliorare le prestazioni delle applicazioni parallele strettamente associate in esecuzione in Microsoft MPI o Intel MPI.</span><span class="sxs-lookup"><span data-stu-id="15637-162">This network can improve the performance of tightly coupled parallel applications running under Microsoft MPI or Intel MPI.</span></span>

- [<span data-ttu-id="15637-163">Istanze con supporto per RDMA</span><span class="sxs-lookup"><span data-stu-id="15637-163">RDMA Capable Instances</span></span>](/azure/virtual-machines/windows/sizes-hpc?context=/azure/architecture/topics/high-performance-computing/context/hpc-context#rdma-capable-instances)
- [<span data-ttu-id="15637-164">Rete virtuale</span><span class="sxs-lookup"><span data-stu-id="15637-164">Virtual Network</span></span>](/azure/virtual-network/virtual-networks-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-165">ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="15637-165">ExpressRoute</span></span>](/azure/expressroute/expressroute-introduction?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="management"></a><span data-ttu-id="15637-166">Gestione</span><span class="sxs-lookup"><span data-stu-id="15637-166">Management</span></span>

### <a name="do-it-yourself"></a><span data-ttu-id="15637-167">Procedure manuali</span><span class="sxs-lookup"><span data-stu-id="15637-167">Do-it-yourself</span></span>

<span data-ttu-id="15637-168">La creazione di un sistema HPC da zero in Azure offre una notevole flessibilità, ma può rivelarsi problematica in termini di manutenzione.</span><span class="sxs-lookup"><span data-stu-id="15637-168">Building an HPC system from scratch on Azure offers a significant amount of flexibility, but is often very maintenance intensive.</span></span>  

1. <span data-ttu-id="15637-169">Configurare l'ambiente cluster nelle macchine virtuali di Azure o nel [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context).</span><span class="sxs-lookup"><span data-stu-id="15637-169">Set up your own cluster environment in Azure virtual machines or [virtual machine scale sets](/azure/virtual-machine-scale-sets/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context).</span></span>
2. <span data-ttu-id="15637-170">Usare i modelli di Azure Resource Manager per distribuire [gestori di carichi di lavoro](#workload-managers), infrastruttura e [applicazioni](#hpc-applications).</span><span class="sxs-lookup"><span data-stu-id="15637-170">Use Azure Resource Manager templates to deploy leading [workload managers](#workload-managers), infrastructure, and [applications](#hpc-applications).</span></span>
3. <span data-ttu-id="15637-171">Scegliere [dimensioni di VM HPC e GPU](#hpc-and-gpu-sizes) che includono hardware specializzato e connessioni di rete per carichi di lavoro MPI o GPU.</span><span class="sxs-lookup"><span data-stu-id="15637-171">Choose [HPC and GPU VM sizes](#hpc-and-gpu-sizes) that include specialized hardware and network connections for MPI or GPU workloads.</span></span>
4. <span data-ttu-id="15637-172">Aggiungere [archiviazione ad alte prestazioni](#hpc-storage) per carichi di lavoro con elevato numero di operazioni di I/O.</span><span class="sxs-lookup"><span data-stu-id="15637-172">Add [high performance storage](#hpc-storage) for I/O-intensive workloads.</span></span>

### <a name="hybrid-and-cloud-bursting"></a><span data-ttu-id="15637-173">Burst ibrido e nel cloud</span><span class="sxs-lookup"><span data-stu-id="15637-173">Hybrid and cloud Bursting</span></span>

<span data-ttu-id="15637-174">Se si ha già un sistema HPC locale che si vuole connettere ad Azure, sono disponibili numerose risorse per iniziare.</span><span class="sxs-lookup"><span data-stu-id="15637-174">If you have an existing on-premise HPC system that you'd like to connect to Azure, there are a number of resources to help get you started.</span></span>

<span data-ttu-id="15637-175">Prima di tutto, vedere l'articolo sulle [opzioni per connettere una rete locale ad Azure](/azure/architecture/reference-architectures/hybrid-networking/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) nella documentazione.</span><span class="sxs-lookup"><span data-stu-id="15637-175">First, review the [Options for connecting an on-premises network to Azure](/azure/architecture/reference-architectures/hybrid-networking/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) article in the documentation.</span></span>  <span data-ttu-id="15637-176">Poi, è consigliabile consultare informazioni su queste opzioni di connettività:</span><span class="sxs-lookup"><span data-stu-id="15637-176">From there, you may want information on these connectivity options:</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/vpn?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/vpn.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="15637-177">Connettere una rete locale ad Azure tramite un gateway VPN</span><span class="sxs-lookup"><span data-stu-id="15637-177">Connect an on-premises network to Azure using a VPN gateway</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="15637-178">Questa architettura di riferimento mostra come estendere una rete locale ad Azure tramite una rete privata virtuale (VPN) da sito a sito.</span><span class="sxs-lookup"><span data-stu-id="15637-178">This reference architecture shows how to extend an on-premises network to Azure, using a site-to-site virtual private network (VPN).</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/expressroute?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/expressroute.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="15637-179">Connettere una rete locale ad Azure tramite ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="15637-179">Connect an on-premises network to Azure using ExpressRoute</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="15637-180">Le connessioni ExpressRoute usano una connessione privata dedicata tramite un provider di connettività di terze parti.</span><span class="sxs-lookup"><span data-stu-id="15637-180">ExpressRoute connections use a private, dedicated connection through a third-party connectivity provider.</span></span> <span data-ttu-id="15637-181">La connessione privata estende la rete locale in Azure.</span><span class="sxs-lookup"><span data-stu-id="15637-181">The private connection extends your on-premises network into Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/expressroute-vpn-failover?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/expressroute-vpn-failover.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="15637-182">Connettere una rete locale ad Azure tramite ExpressRoute con failover VPN</span><span class="sxs-lookup"><span data-stu-id="15637-182">Connect an on-premises network to Azure using ExpressRoute with VPN failover</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="15637-183">Implementare un'architettura di rete sicura da sito a sito e a disponibilità elevata, che si estende su una rete virtuale di Azure e su una rete locale connessa tramite ExpressRoute con failover del gateway VPN.</span><span class="sxs-lookup"><span data-stu-id="15637-183">Implement a highly available and secure site-to-site network architecture that spans an Azure virtual network and an on-premises network connected using ExpressRoute with VPN gateway failover.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

<span data-ttu-id="15637-184">Dopo aver stabilito la connettività di rete, è possibile iniziare a usare le risorse di calcolo del cloud su richiesta con le funzionalità burst dell'attuale [soluzione di gestione dei carichi di lavoro](#workload-manager).</span><span class="sxs-lookup"><span data-stu-id="15637-184">Once network connectivity is securely established, you can start using cloud compute resources on-demand with the bursting capabilities of your existing [workload manager](#workload-manager).</span></span>

### <a name="marketplace-solutions"></a><span data-ttu-id="15637-185">Soluzioni del Marketplace</span><span class="sxs-lookup"><span data-stu-id="15637-185">Marketplace solutions</span></span>

<span data-ttu-id="15637-186">In [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) sono disponibili numerose offerte di soluzioni di gestione dei carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="15637-186">There are a number of workload managers offered in the [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).</span></span>

- [<span data-ttu-id="15637-187">HPC basato su RogueWave CentOS</span><span class="sxs-lookup"><span data-stu-id="15637-187">RogueWave CentOS-based HPC</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
- [<span data-ttu-id="15637-188">SUSE Linux Enterprise Server per HPC</span><span class="sxs-lookup"><span data-stu-id="15637-188">SUSE Linux Enterprise Server for HPC</span></span>](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
- [<span data-ttu-id="15637-189">TIBCO Grid Server Engine</span><span class="sxs-lookup"><span data-stu-id="15637-189">TIBCO Grid Server Engine</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
- [<span data-ttu-id="15637-190">VM per l'analisi scientifica dei dati di Azure per Windows e Linux</span><span class="sxs-lookup"><span data-stu-id="15637-190">Azure Data Science VM for Windows and Linux</span></span>](/azure/machine-learning/data-science-virtual-machine/overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-191">D3View</span><span class="sxs-lookup"><span data-stu-id="15637-191">D3View</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
- [<span data-ttu-id="15637-192">UberCloud</span><span class="sxs-lookup"><span data-stu-id="15637-192">UberCloud</span></span>](https://azure.microsoft.com/search/marketplace/?q=ubercloud)

### <a name="azure-batch"></a><span data-ttu-id="15637-193">Azure Batch</span><span class="sxs-lookup"><span data-stu-id="15637-193">Azure Batch</span></span>

<span data-ttu-id="15637-194">[Azure Batch](/azure/batch/batch-technical-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) è un servizio di piattaforma per eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud.</span><span class="sxs-lookup"><span data-stu-id="15637-194">[Azure Batch](/azure/batch/batch-technical-overview?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) is a platform service for running large-scale parallel and high-performance computing (HPC) applications efficiently in the cloud.</span></span> <span data-ttu-id="15637-195">Azure Batch pianifica l'esecuzione del lavoro a elevato utilizzo di calcolo su un pool di macchine virtuali gestito e può ridimensionare automaticamente le risorse di calcolo in base alle esigenze dei processi.</span><span class="sxs-lookup"><span data-stu-id="15637-195">Azure Batch schedules compute-intensive work to run on a managed pool of virtual machines, and can automatically scale compute resources to meet the needs of your jobs.</span></span>

<span data-ttu-id="15637-196">I fornitori o sviluppatori di SaaS possono usare gli strumenti e gli SDK di Batch per integrare le applicazioni HPC o i carichi di lavoro dei contenitori con Azure, gestire temporaneamente i dati in Azure e creare pipeline per l'esecuzione di processi.</span><span class="sxs-lookup"><span data-stu-id="15637-196">SaaS providers or developers can use the Batch SDKs and tools to integrate HPC applications or container workloads with Azure, stage data to Azure, and build job execution pipelines.</span></span>

### <a name="azure-cyclecloud"></a><span data-ttu-id="15637-197">Azure CycleCloud</span><span class="sxs-lookup"><span data-stu-id="15637-197">Azure CycleCloud</span></span>

<span data-ttu-id="15637-198">[Azure CycleCloud](/azure/cyclecloud/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) offre il modo più semplice per gestire carichi di lavoro HPC con qualsiasi utilità di pianificazione (come Slurm, Grid Engine, HPC Pack, HTCondor, LSF, PBS Pro o Symphony) in Azure</span><span class="sxs-lookup"><span data-stu-id="15637-198">[Azure CycleCloud](/azure/cyclecloud/?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) Provides the simplest way to manage HPC workloads using any scheduler (like Slurm, Grid Engine, HPC Pack, HTCondor, LSF, PBS Pro, or Symphony), on Azure</span></span>

<span data-ttu-id="15637-199">CycleCloud consente di:</span><span class="sxs-lookup"><span data-stu-id="15637-199">CycleCloud allows you to:</span></span>

- <span data-ttu-id="15637-200">Distribuire cluster completi e altre risorse, tra cui un'utilità di pianificazione, VM di calcolo, risorse di archiviazione, risorse di rete e cache</span><span class="sxs-lookup"><span data-stu-id="15637-200">Deploy full clusters and other resources, including scheduler, compute VMs, storage, networking, and cache</span></span>
- <span data-ttu-id="15637-201">Orchestrare processi, dati e flussi di lavoro sul cloud</span><span class="sxs-lookup"><span data-stu-id="15637-201">Orchestrate job, data, and cloud workflows</span></span>
- <span data-ttu-id="15637-202">Offrire agli amministratori il controllo completo su quali utenti possono eseguire i processi, dove e a quale costo</span><span class="sxs-lookup"><span data-stu-id="15637-202">Give admins full control over which users can run jobs, as well as where and at what cost</span></span>
- <span data-ttu-id="15637-203">Personalizzare e ottimizzare i cluster tramite funzionalità avanzate per criteri e governance, tra cui i controlli per i costi, l'integrazione con Active Directory, il monitoraggio e la creazione di report</span><span class="sxs-lookup"><span data-stu-id="15637-203">Customize and optimize clusters through advanced policy and governance features, including cost controls, Active Directory integration, monitoring, and reporting</span></span>
- <span data-ttu-id="15637-204">Usare l'utilità di pianificazione di processi e le applicazioni correnti senza modifiche</span><span class="sxs-lookup"><span data-stu-id="15637-204">Use your current job scheduler and applications without modification</span></span>
- <span data-ttu-id="15637-205">Sfruttare i vantaggi della scalabilità automatica predefinita e di architetture di riferimento convalidate per una vasta gamma di carichi di lavoro HPC e di settori</span><span class="sxs-lookup"><span data-stu-id="15637-205">Take advantage of built-in autoscaling and battle-tested reference architectures for a wide range of HPC workloads and industries</span></span>

### <a name="workload-managers"></a><span data-ttu-id="15637-206">Soluzioni di gestione dei carichi di lavoro</span><span class="sxs-lookup"><span data-stu-id="15637-206">Workload managers</span></span>

<span data-ttu-id="15637-207">Di seguito sono riportati esempi di cluster e soluzioni di gestione dei carichi di lavoro eseguibili nell'infrastruttura di Azure.</span><span class="sxs-lookup"><span data-stu-id="15637-207">The following are examples of cluster and workload managers that can run in Azure infrastructure.</span></span> <span data-ttu-id="15637-208">Creare cluster autonomi nelle macchine virtuali di Azure oppure eseguire il potenziamento in macchine virtuali di Azure da un cluster locale.</span><span class="sxs-lookup"><span data-stu-id="15637-208">Create stand-alone clusters in Azure VMs or burst to Azure VMs from an on-premises cluster.</span></span>

- [<span data-ttu-id="15637-209">Alces Flight Compute</span><span class="sxs-lookup"><span data-stu-id="15637-209">Alces Flight Compute</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
- [<span data-ttu-id="15637-210">TIBCO DataSynapse GridServer</span><span class="sxs-lookup"><span data-stu-id="15637-210">TIBCO DataSynapse GridServer</span></span>](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/)
- [<span data-ttu-id="15637-211">Bright Cluster Manager</span><span class="sxs-lookup"><span data-stu-id="15637-211">Bright Cluster Manager</span></span>](http://www.brightcomputing.com/technology-partners/microsoft)
- [<span data-ttu-id="15637-212">IBM Spectrum Symphony e Symphony LSF</span><span class="sxs-lookup"><span data-stu-id="15637-212">IBM Spectrum Symphony and Symphony LSF</span></span>](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
- [<span data-ttu-id="15637-213">PBS Pro</span><span class="sxs-lookup"><span data-stu-id="15637-213">PBS Pro</span></span>](http://pbspro.org)
- [<span data-ttu-id="15637-214">Altair</span><span class="sxs-lookup"><span data-stu-id="15637-214">Altair</span></span>](http://www.altair.com/)
- [<span data-ttu-id="15637-215">Rescale</span><span class="sxs-lookup"><span data-stu-id="15637-215">Rescale</span></span>](https://www.rescale.com/azure/)
- [<span data-ttu-id="15637-216">Microsoft HPC Pack</span><span class="sxs-lookup"><span data-stu-id="15637-216">Microsoft HPC Pack</span></span>](https://technet.microsoft.com/library/mt744885.aspx)
  - [<span data-ttu-id="15637-217">HPC Pack per Windows</span><span class="sxs-lookup"><span data-stu-id="15637-217">HPC Pack for Windows</span></span>](/azure/virtual-machines/windows/hpcpack-cluster-options?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
  - [<span data-ttu-id="15637-218">HPC Pack per Linux</span><span class="sxs-lookup"><span data-stu-id="15637-218">HPC Pack for Linux</span></span>](/azure/virtual-machines/linux/hpcpack-cluster-options?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

#### <a name="containers"></a><span data-ttu-id="15637-219">Contenitori</span><span class="sxs-lookup"><span data-stu-id="15637-219">Containers</span></span>

<span data-ttu-id="15637-220">È anche possibile usare i contenitori per gestire alcuni carichi di lavoro HPC.</span><span class="sxs-lookup"><span data-stu-id="15637-220">Containers can also be used to manage some HPC workloads.</span></span>  <span data-ttu-id="15637-221">Servizi come Azure Kubernetes (AKS) semplificano la distribuzione di un cluster Kubernetes gestito in Azure.</span><span class="sxs-lookup"><span data-stu-id="15637-221">Services like the Azure Kubernetes Service (AKS) makes it simple to deploy a managed Kubernetes cluster in Azure.</span></span>

- [<span data-ttu-id="15637-222">Servizio Azure Kubernetes</span><span class="sxs-lookup"><span data-stu-id="15637-222">Azure Kubernetes Service (AKS)</span></span>](/azure/aks/intro-kubernetes?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-223">Registro Container</span><span class="sxs-lookup"><span data-stu-id="15637-223">Container Registry</span></span>](/azure/container-registry/container-registry-intro?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="cost-management"></a><span data-ttu-id="15637-224">Gestione dei costi</span><span class="sxs-lookup"><span data-stu-id="15637-224">Cost management</span></span>

<span data-ttu-id="15637-225">I costi dell'HPC in Azure possono essere gestiti in vari modi.</span><span class="sxs-lookup"><span data-stu-id="15637-225">Managing your HPC cost on Azure can be done through a few different ways.</span></span>  <span data-ttu-id="15637-226">Assicurarsi di aver esaminato le [opzioni di acquisto di Azure](https://azure.microsoft.com/pricing/purchase-options/) per trovare il metodo più appropriato per la propria organizzazione.</span><span class="sxs-lookup"><span data-stu-id="15637-226">Ensure you've reviewed the [Azure purchasing options](https://azure.microsoft.com/pricing/purchase-options/) to find the method that works best for your organization.</span></span>

<span data-ttu-id="15637-227">L'uso di [macchine virtuali con priorità bassa](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi.</span><span class="sxs-lookup"><span data-stu-id="15637-227">[Low priority VMs](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) allow you to take advantage of our unutilized capacity at a significant cost savings.</span></span>

## <a name="security"></a><span data-ttu-id="15637-228">Security</span><span class="sxs-lookup"><span data-stu-id="15637-228">Security</span></span>

<span data-ttu-id="15637-229">Per una panoramica delle procedure consigliate per la sicurezza, vedere la [documentazione sulla sicurezza di Azure](/azure/security/azure-security?context=/azure/architecture/topics/high-performance-computing/context/hpc-context).</span><span class="sxs-lookup"><span data-stu-id="15637-229">For an overview of security best practices on Azure, review the [Azure Security Documentation](/azure/security/azure-security?context=/azure/architecture/topics/high-performance-computing/context/hpc-context).</span></span>  

<span data-ttu-id="15637-230">Oltre alle configurazioni di rete disponibili nella sezione sul [burst nel cloud](#hybrid-and-cloud-bursting), è possibile implementare una configurazione hub-spoke per isolare le risorse di calcolo:</span><span class="sxs-lookup"><span data-stu-id="15637-230">In addition to the network configurations available in the [Cloud Bursting](#hybrid-and-cloud-bursting) section, you may want to implement a hub/spoke configuration to isolate your compute resources:</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/hub-spoke.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="15637-231">Implementare una topologia di rete hub-spoke in Azure</span><span class="sxs-lookup"><span data-stu-id="15637-231">Implement a hub-spoke network topology in Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="15637-232">L'hub è una rete virtuale in Azure che funge da punto centrale di connettività alla rete locale.</span><span class="sxs-lookup"><span data-stu-id="15637-232">The hub is a virtual network (VNet) in Azure that acts as a central point of connectivity to your on-premises network.</span></span> <span data-ttu-id="15637-233">Gli spoke sono le reti virtuali peer con l'hub, e possono essere usati per isolare i carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="15637-233">The spokes are VNets that peer with the hub, and can be used to isolate workloads.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/reference-architectures/hybrid-networking/shared-services?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="/azure/architecture/reference-architectures/hybrid-networking/images/shared-services.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="15637-234">Implementare una topologia di rete hub-spoke con servizi condivisi in Azure</span><span class="sxs-lookup"><span data-stu-id="15637-234">Implement a hub-spoke network topology with shared services in Azure</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="15637-235">Questa architettura di riferimento, basata sull'architettura di riferimento hub-spoke, include nell'hub servizi condivisi che potranno essere utilizzati da tutti gli spoke.</span><span class="sxs-lookup"><span data-stu-id="15637-235">This reference architecture builds on the hub-spoke reference architecture to include shared services in the hub that can be consumed by all spokes.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="hpc-applications"></a><span data-ttu-id="15637-236">Applicazioni HPC</span><span class="sxs-lookup"><span data-stu-id="15637-236">HPC applications</span></span>

<span data-ttu-id="15637-237">Eseguire applicazioni HPC personalizzate o commerciali in Azure.</span><span class="sxs-lookup"><span data-stu-id="15637-237">Run custom or commercial HPC applications in Azure.</span></span> <span data-ttu-id="15637-238">Per alcuni esempi in questa sezione è disponibile un benchmark per una scalabilità efficiente con altre VM o core di calcolo.</span><span class="sxs-lookup"><span data-stu-id="15637-238">Several examples in this section are benchmarked to scale efficiently with additional VMs or compute cores.</span></span> <span data-ttu-id="15637-239">Visitare [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) per le soluzioni pronte per la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="15637-239">Visit the [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) for ready-to-deploy solutions.</span></span>

> [!NOTE]
> <span data-ttu-id="15637-240">Verificare le licenze del fornitore di qualsiasi applicazione commerciale o altre restrizioni per l'esecuzione nel cloud.</span><span class="sxs-lookup"><span data-stu-id="15637-240">Check with the vendor of any commercial application for licensing or other restrictions for running in the cloud.</span></span> <span data-ttu-id="15637-241">Non tutti i fornitori offrono licenze con pagamento in base al consumo.</span><span class="sxs-lookup"><span data-stu-id="15637-241">Not all vendors offer pay-as-you-go licensing.</span></span> <span data-ttu-id="15637-242">Può essere necessario un server di gestione licenze nel cloud per la soluzione o connettersi a un server licenze locale.</span><span class="sxs-lookup"><span data-stu-id="15637-242">You might need a licensing server in the cloud for your solution, or connect to an on-premises license server.</span></span>

### <a name="engineering-applications"></a><span data-ttu-id="15637-243">Applicazioni tecniche</span><span class="sxs-lookup"><span data-stu-id="15637-243">Engineering applications</span></span>

- [<span data-ttu-id="15637-244">Altair RADIOSS</span><span class="sxs-lookup"><span data-stu-id="15637-244">Altair RADIOSS</span></span>](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
- [<span data-ttu-id="15637-245">ANSYS CFD</span><span class="sxs-lookup"><span data-stu-id="15637-245">ANSYS CFD</span></span>](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
- [<span data-ttu-id="15637-246">MATLAB Distributed Computing Server</span><span class="sxs-lookup"><span data-stu-id="15637-246">MATLAB Distributed Computing Server</span></span>](/azure/virtual-machines/windows/matlab-mdcs-cluster?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-247">StarCCM+</span><span class="sxs-lookup"><span data-stu-id="15637-247">StarCCM+</span></span>](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
- [<span data-ttu-id="15637-248">OpenFOAM</span><span class="sxs-lookup"><span data-stu-id="15637-248">OpenFOAM</span></span>](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

### <a name="graphics-and-rendering"></a><span data-ttu-id="15637-249">Grafica e rendering</span><span class="sxs-lookup"><span data-stu-id="15637-249">Graphics and rendering</span></span>

- <span data-ttu-id="15637-250">[Autodesk Maya, 3ds Max e Arnold](/azure/batch/batch-rendering-service?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) in Azure Batch</span><span class="sxs-lookup"><span data-stu-id="15637-250">[Autodesk Maya, 3ds Max, and Arnold](/azure/batch/batch-rendering-service?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) on Azure Batch</span></span>

### <a name="ai-and-deep-learning"></a><span data-ttu-id="15637-251">AI e apprendimento avanzato</span><span class="sxs-lookup"><span data-stu-id="15637-251">AI and deep learning</span></span>

- [<span data-ttu-id="15637-252">Microsoft Cognitive Toolkit</span><span class="sxs-lookup"><span data-stu-id="15637-252">Microsoft Cognitive Toolkit</span></span>](/cognitive-toolkit/cntk-on-azure)
- [<span data-ttu-id="15637-253">Macchina virtuale per l'apprendimento avanzato</span><span class="sxs-lookup"><span data-stu-id="15637-253">Deep Learning VM</span></span>](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
- [<span data-ttu-id="15637-254">Soluzioni di Batch Shipyard per l'apprendimento avanzato</span><span class="sxs-lookup"><span data-stu-id="15637-254">Batch Shipyard recipes for deep learning</span></span>](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

### <a name="mpi-providers"></a><span data-ttu-id="15637-255">Provider di MPI</span><span class="sxs-lookup"><span data-stu-id="15637-255">MPI Providers</span></span>

- [<span data-ttu-id="15637-256">Microsoft MPI</span><span class="sxs-lookup"><span data-stu-id="15637-256">Microsoft MPI</span></span>](/message-passing-interface/microsoft-mpi)

## <a name="remote-visualization"></a><span data-ttu-id="15637-257">Visualizzazione remota</span><span class="sxs-lookup"><span data-stu-id="15637-257">Remote visualization</span></span>

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-third has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="/azure/architecture/example-scenario/infrastructure/linux-vdi-citrix?context=/azure/architecture/topics/high-performance-computing/context/hpc-context">
            <article class="card has-outline-hover is-relative is-fullheight">
                    <figure class="image has-margin-right-none has-margin-left-none has-margin-top-none has-margin-bottom-none">
                        <img role="presentation" alt="" src="../../example-scenario/infrastructure/media/azure-citrix-sample-diagram.png">
                    </figure>
                <div class="card-content has-text-overflow-ellipsis">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary"><span data-ttu-id="15637-258">Desktop virtuali Linux con Citrix</span><span class="sxs-lookup"><span data-stu-id="15637-258">Linux virtual desktops with Citrix</span></span></h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p><span data-ttu-id="15637-259">Creare un ambiente VDI per i desktop Linux con Citrix in Azure.</span><span class="sxs-lookup"><span data-stu-id="15637-259">Build a VDI environment for Linux Desktops using Citrix on Azure.</span></span></p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="performance-benchmarks"></a><span data-ttu-id="15637-260">Benchmark delle prestazioni</span><span class="sxs-lookup"><span data-stu-id="15637-260">Performance Benchmarks</span></span>

- [<span data-ttu-id="15637-261">Benchmark delle risorse di calcolo</span><span class="sxs-lookup"><span data-stu-id="15637-261">Compute benchmarks</span></span>](/azure/virtual-machines/windows/compute-benchmark-scores?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)

## <a name="customer-stories"></a><span data-ttu-id="15637-262">Casi di successo dei clienti</span><span class="sxs-lookup"><span data-stu-id="15637-262">Customer stories</span></span>

<span data-ttu-id="15637-263">Numerosi clienti hanno usato con successo Azure per i loro carichi di lavoro HPC.</span><span class="sxs-lookup"><span data-stu-id="15637-263">There are a number of customers who have seen great success by using Azure for their HPC workloads.</span></span>  <span data-ttu-id="15637-264">Di seguito sono riportati alcuni case study dei clienti:</span><span class="sxs-lookup"><span data-stu-id="15637-264">You can find a few of these customer case studies below:</span></span>

- [<span data-ttu-id="15637-265">ANEO</span><span class="sxs-lookup"><span data-stu-id="15637-265">ANEO</span></span>](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu)
- [<span data-ttu-id="15637-266">AXA Global P&amp;C</span><span class="sxs-lookup"><span data-stu-id="15637-266">AXA Global P&C</span></span>](https://customers.microsoft.com/story/axa-global-p-and-c)
- [<span data-ttu-id="15637-267">Axioma</span><span class="sxs-lookup"><span data-stu-id="15637-267">Axioma</span></span>](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
- [<span data-ttu-id="15637-268">d3View</span><span class="sxs-lookup"><span data-stu-id="15637-268">d3View</span></span>](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
- [<span data-ttu-id="15637-269">EFS</span><span class="sxs-lookup"><span data-stu-id="15637-269">EFS</span></span>](https://customers.microsoft.com/story/efs-professionalservices-azure)
- [<span data-ttu-id="15637-270">Hymans Robertson</span><span class="sxs-lookup"><span data-stu-id="15637-270">Hymans Robertson</span></span>](https://customers.microsoft.com/story/hymans-robertson)
- [<span data-ttu-id="15637-271">MetLife</span><span class="sxs-lookup"><span data-stu-id="15637-271">MetLife</span></span>](https://enterprise.microsoft.com/customer-story/industries/insurance/metlife/)
- [<span data-ttu-id="15637-272">Microsoft Research</span><span class="sxs-lookup"><span data-stu-id="15637-272">Microsoft Research</span></span>](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
- [<span data-ttu-id="15637-273">Milliman</span><span class="sxs-lookup"><span data-stu-id="15637-273">Milliman</span></span>](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
- [<span data-ttu-id="15637-274">Mitsubishi UFJ Securities International</span><span class="sxs-lookup"><span data-stu-id="15637-274">Mitsubishi UFJ Securities International</span></span>](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
- [<span data-ttu-id="15637-275">NeuroInitiative</span><span class="sxs-lookup"><span data-stu-id="15637-275">NeuroInitiative</span></span>](https://customers.microsoft.com/story/neuroinitiative-health-provider-azure)
- [<span data-ttu-id="15637-276">Schlumberger</span><span class="sxs-lookup"><span data-stu-id="15637-276">Schlumberger</span></span>](https://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
- [<span data-ttu-id="15637-277">Towers Watson</span><span class="sxs-lookup"><span data-stu-id="15637-277">Towers Watson</span></span>](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)

## <a name="other-important-information"></a><span data-ttu-id="15637-278">Altre informazioni importanti</span><span class="sxs-lookup"><span data-stu-id="15637-278">Other important information</span></span>

- <span data-ttu-id="15637-279">Assicurarsi che la [quota di CPU virtuali](/azure/virtual-machines/linux/quotas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) sia stata aumentata prima di provare a eseguire carichi di lavoro su larga scala.</span><span class="sxs-lookup"><span data-stu-id="15637-279">Ensure your [vCPU quota](/azure/virtual-machines/linux/quotas?context=/azure/architecture/topics/high-performance-computing/context/hpc-context) has been increased before attempting to run large-scale workloads.</span></span>

## <a name="next-steps"></a><span data-ttu-id="15637-280">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="15637-280">Next steps</span></span>

<span data-ttu-id="15637-281">Per gli annunci più recenti, vedere:</span><span class="sxs-lookup"><span data-stu-id="15637-281">For the latest announcements, see:</span></span>

- [<span data-ttu-id="15637-282">Blog del team Microsoft HPC e Batch</span><span class="sxs-lookup"><span data-stu-id="15637-282">Microsoft HPC and Batch team blog</span></span>](http://blogs.technet.com/b/windowshpc/)
- <span data-ttu-id="15637-283">Visitare il [blog di Azure](https://azure.microsoft.com/blog/tag/hpc/).</span><span class="sxs-lookup"><span data-stu-id="15637-283">Visit the [Azure blog](https://azure.microsoft.com/blog/tag/hpc/).</span></span>

### <a name="microsoft-batch-examples"></a><span data-ttu-id="15637-284">Esempi di Microsoft Batch</span><span class="sxs-lookup"><span data-stu-id="15637-284">Microsoft Batch Examples</span></span>

<span data-ttu-id="15637-285">Queste esercitazioni forniscono informazioni dettagliate sull'esecuzione di applicazioni in Microsoft Batch</span><span class="sxs-lookup"><span data-stu-id="15637-285">These tutorials will provide you with details on running applications on Microsoft Batch</span></span>

- [<span data-ttu-id="15637-286">Introduzione allo sviluppo con Batch</span><span class="sxs-lookup"><span data-stu-id="15637-286">Get started developing with Batch</span></span>](/azure/batch/quick-run-dotnet?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-287">Usare gli esempi di codice per Azure Batch</span><span class="sxs-lookup"><span data-stu-id="15637-287">Use Azure Batch code samples</span></span>](https://github.com/Azure/azure-batch-samples)
- [<span data-ttu-id="15637-288">Usare le macchine virtuali con priorità bassa in Batch</span><span class="sxs-lookup"><span data-stu-id="15637-288">Use low-priority VMs with Batch</span></span>](/azure/batch/batch-low-pri-vms?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)
- [<span data-ttu-id="15637-289">Eseguire carichi di lavoro HPC inclusi in contenitori con Batch Shipyard</span><span class="sxs-lookup"><span data-stu-id="15637-289">Run containerized HPC workloads with Batch Shipyard</span></span>](https://github.com/Azure/batch-shipyard)
- [<span data-ttu-id="15637-290">Eseguire carichi di lavoro R paralleli in Batch</span><span class="sxs-lookup"><span data-stu-id="15637-290">Run parallel R workloads on Batch</span></span>](https://github.com/Azure/doAzureParallel)
- [<span data-ttu-id="15637-291">Eseguire processi Spark su richiesta in Batch</span><span class="sxs-lookup"><span data-stu-id="15637-291">Run on-demand Spark jobs on Batch</span></span>](https://github.com/Azure/aztk)
- [<span data-ttu-id="15637-292">Usare VM a elevato utilizzo di calcolo in pool di Batch</span><span class="sxs-lookup"><span data-stu-id="15637-292">Use compute-intensive VMs in Batch pools</span></span>](/azure/batch/batch-pool-compute-intensive-sizes?context=/azure/architecture/topics/high-performance-computing/context/hpc-context)