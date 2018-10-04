---
title: Rendering di video 3D in Azure
description: Esecuzione di carichi di lavoro HPC nativi in Azure con il servizio Azure Batch
author: adamboeglin
ms.date: 07/13/2018
ms.openlocfilehash: 67dc8496656a75eab8f5d0ce45d00f8b1f4ea03f
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47428058"
---
# <a name="3d-video-rendering-on-azure"></a><span data-ttu-id="da23c-103">Rendering di video 3D in Azure</span><span class="sxs-lookup"><span data-stu-id="da23c-103">3D video rendering on Azure</span></span>

<span data-ttu-id="da23c-104">Il rendering 3D è un processo di lunga durata il cui completamento richiede un'elevata quantità di tempo CPU.</span><span class="sxs-lookup"><span data-stu-id="da23c-104">3D rendering is a time consuming process that requires a significant amount of CPU time to complete.</span></span>  <span data-ttu-id="da23c-105">In un singolo computer, il processo di generazione di un file video da asset statici può richiedere ore o addirittura giorni, a seconda della lunghezza e della complessità del video che si intende produrre.</span><span class="sxs-lookup"><span data-stu-id="da23c-105">On a single machine, the process of generating a video file from static assets can take hours or even days depending on the length and complexity of the video you are producing.</span></span>  <span data-ttu-id="da23c-106">Per eseguire queste attività, molte aziende acquistano costosi computer desktop di fascia alta oppure investono in farm di rendering di grandi dimensioni a cui possono inviare i processi.</span><span class="sxs-lookup"><span data-stu-id="da23c-106">Many companies will purchase either expensive high end desktop computers to perform these tasks, or invest in large render farms that they can submit jobs to.</span></span>  <span data-ttu-id="da23c-107">Sfruttando Azure Batch, tuttavia, la stessa potenza è disponibile quando necessario e si arresta in caso contrario, senza investimenti di capitale.</span><span class="sxs-lookup"><span data-stu-id="da23c-107">However, by taking advantage of Azure Batch, that power is available to you when you need it and shuts itself down when you don't, all without any capital investment.</span></span>

<span data-ttu-id="da23c-108">Batch offre un'esperienza di gestione e di pianificazione dei processi coerente indipendentemente dal fatto che si selezionino nodi di calcolo Windows Server o Linux.</span><span class="sxs-lookup"><span data-stu-id="da23c-108">Batch gives you a consistent management experience and job scheduling, whether you select Windows Server or Linux compute nodes.</span></span> <span data-ttu-id="da23c-109">Con Batch, è possibile usare le applicazioni Windows o Linux esistenti, come AutoDesk Maya e Blender, per eseguire processi di rendering su larga scala in Azure.</span><span class="sxs-lookup"><span data-stu-id="da23c-109">With Batch, you can use your existing Windows or Linux applications, including AutoDesk Maya and Blender, to run large-scale render jobs in Azure.</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="da23c-110">Casi d'uso correlati</span><span class="sxs-lookup"><span data-stu-id="da23c-110">Related use cases</span></span>

<span data-ttu-id="da23c-111">Prendere in considerazione questo scenario per questi casi d'uso simili:</span><span class="sxs-lookup"><span data-stu-id="da23c-111">Consider this scenario for these similar use cases:</span></span>

* <span data-ttu-id="da23c-112">Modellazione 3D</span><span class="sxs-lookup"><span data-stu-id="da23c-112">3D Modeling</span></span>
* <span data-ttu-id="da23c-113">Rendering VFX (Visual FX)</span><span class="sxs-lookup"><span data-stu-id="da23c-113">Visual FX (VFX) Rendering</span></span>
* <span data-ttu-id="da23c-114">Transcodifica di video</span><span class="sxs-lookup"><span data-stu-id="da23c-114">Video transcoding</span></span>
* <span data-ttu-id="da23c-115">Elaborazione, correzione dei colori e ridimensionamento di immagini</span><span class="sxs-lookup"><span data-stu-id="da23c-115">Image processing, color correction, & resizing</span></span>

## <a name="architecture"></a><span data-ttu-id="da23c-116">Architettura</span><span class="sxs-lookup"><span data-stu-id="da23c-116">Architecture</span></span>

![Panoramica dell'architettura dei componenti coinvolti in una soluzione HPC nativa cloud con Azure Batch][architecture]

<span data-ttu-id="da23c-118">Questo scenario di esempio include il flusso di lavoro associato all'uso di Azure Batch. Il flusso dei dati avviene come segue:</span><span class="sxs-lookup"><span data-stu-id="da23c-118">This sample scenario covers the workflow when using Azure Batch, the data flows as follows:</span></span>

1. <span data-ttu-id="da23c-119">Caricare i file di input e le applicazioni per elaborarli nell'account di archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="da23c-119">Upload input files and the applications to process those files to your Azure Storage account</span></span>
2. <span data-ttu-id="da23c-120">Creare un pool Batch di nodi di calcolo nell'account Batch, un processo per eseguire il carico di lavoro nel pool e le attività nel processo</span><span class="sxs-lookup"><span data-stu-id="da23c-120">Create a Batch pool of compute nodes in your Batch account, a job to run the workload on the pool, and tasks in the job.</span></span>
3. <span data-ttu-id="da23c-121">Scaricare i file di input e le applicazioni in Batch</span><span class="sxs-lookup"><span data-stu-id="da23c-121">Download input files and the applications to Batch</span></span>
4. <span data-ttu-id="da23c-122">Monitorare l'esecuzione delle attività</span><span class="sxs-lookup"><span data-stu-id="da23c-122">Monitor task execution</span></span>
5. <span data-ttu-id="da23c-123">Caricare l'output delle attività</span><span class="sxs-lookup"><span data-stu-id="da23c-123">Upload task output</span></span>
6. <span data-ttu-id="da23c-124">Scaricare i file di output</span><span class="sxs-lookup"><span data-stu-id="da23c-124">Download output files</span></span>

<span data-ttu-id="da23c-125">Per semplificare questo processo, è anche possibile usare i [plug-in di Batch per Maya e 3ds Max][batch-plugins]</span><span class="sxs-lookup"><span data-stu-id="da23c-125">To simplify this process, you could also use the [Batch Plugins for Maya & 3ds Max][batch-plugins]</span></span>

### <a name="components"></a><span data-ttu-id="da23c-126">Componenti</span><span class="sxs-lookup"><span data-stu-id="da23c-126">Components</span></span>

<span data-ttu-id="da23c-127">Azure Batch si basa sulle tecnologie Azure seguenti:</span><span class="sxs-lookup"><span data-stu-id="da23c-127">Azure Batch builds upon the following Azure technologies:</span></span>

* <span data-ttu-id="da23c-128">I [gruppi di risorse][resource-groups] sono un contenitore logico per le risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="da23c-128">[Resource Groups][resource-groups] is a logical container for Azure resources.</span></span>
* <span data-ttu-id="da23c-129">Le [reti virtuali][vnet] vengono usate sia per il nodo head che per le risorse di calcolo.</span><span class="sxs-lookup"><span data-stu-id="da23c-129">[Virtual Networks][vnet] are used to for both the Head Node and Compute resources</span></span>
* <span data-ttu-id="da23c-130">Gli account di [archiviazione][storage] vengono usati per la sincronizzazione e la conservazione dei dati</span><span class="sxs-lookup"><span data-stu-id="da23c-130">[Storage][storage] accounts are used for the synchronization and data retention</span></span>
* <span data-ttu-id="da23c-131">I [set di scalabilità di macchine virtuali][vmss] vengono usati da CycleCloud per le risorse di calcolo</span><span class="sxs-lookup"><span data-stu-id="da23c-131">[Virtual Machine Scale Sets][vmss] are used by CycleCloud for compute resources</span></span>

## <a name="considerations"></a><span data-ttu-id="da23c-132">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="da23c-132">Considerations</span></span>

### <a name="machine-sizes-available-for-azure-batch"></a><span data-ttu-id="da23c-133">Dimensioni di macchine virtuali disponibili per Azure Batch</span><span class="sxs-lookup"><span data-stu-id="da23c-133">Machine Sizes available for Azure Batch</span></span>

<span data-ttu-id="da23c-134">Nonostante la maggior parte dei clienti di soluzioni di rendering scelga risorse con un'elevata potenza di CPU, la scelta delle macchine virtuali per altri carichi di lavoro che usano set di scalabilità di macchine virtuali potrebbe essere effettuata in modo diverso e dipendere dai fattori seguenti:</span><span class="sxs-lookup"><span data-stu-id="da23c-134">While most rendering customers will choose resources with high CPU power, other workloads using virtual machine scale sets may choose VMs differently and will depend on a number of factors:</span></span>

* <span data-ttu-id="da23c-135">L'esecuzione dell'applicazione è associata alla memoria?</span><span class="sxs-lookup"><span data-stu-id="da23c-135">Is the application being run memory bound?</span></span>
* <span data-ttu-id="da23c-136">L'applicazione deve usare GPU?</span><span class="sxs-lookup"><span data-stu-id="da23c-136">Does the application need to use GPUs?</span></span> 
* <span data-ttu-id="da23c-137">I tipi di processi presentano elevati livelli di parallelismo o richiedono connettività InfiniBand per processi strettamente associati?</span><span class="sxs-lookup"><span data-stu-id="da23c-137">Are the job types embarrassingly parallel or require infiniband connectivity for tightly coupled jobs?</span></span>
* <span data-ttu-id="da23c-138">È necessaria un'elevata velocità di I/O nelle risorse di archiviazione nei nodi di calcolo</span><span class="sxs-lookup"><span data-stu-id="da23c-138">Require fast I/O to storage on the compute Nodes</span></span>

<span data-ttu-id="da23c-139">Azure offre numerose dimensioni di macchine virtuali che possono soddisfare ognuno dei requisiti delle applicazioni indicati sopra. Alcune sono specifiche per HPC, ma anche le dimensioni più ridotte possono essere usate per ottenere un'efficace implementazione a griglia:</span><span class="sxs-lookup"><span data-stu-id="da23c-139">Azure has a wide range of VM sizes that can address each and every one of the above application requirements, some are specific to HPC, but even the smallest sizes can be utilized to provide an effective grid implementation:</span></span>

* <span data-ttu-id="da23c-140">[Dimensioni di macchine virtuali HPC][compute-hpc]: dato che il rendering è associato alla CPU, è consigliabile usare macchine virtuali di Azure serie H.</span><span class="sxs-lookup"><span data-stu-id="da23c-140">[HPC VM sizes][compute-hpc] Due to the CPU bound nature of rendering, Microsoft typically suggests the Azure H-Series VMs.</span></span>  <span data-ttu-id="da23c-141">Queste macchine virtuali sono specificamente realizzate per esigenze di calcolo di fascia alta, sono disponibili in dimensioni da 8 a 16 vCPU core e sono dotate di memoria DDR4, archiviazione temporanea in unità SSD e tecnologia Haswell E5 Intel.</span><span class="sxs-lookup"><span data-stu-id="da23c-141">This type of VM is built specifically for high end computational needs, they have 8 and 16 core vCPU sizes available, and features DDR4 memory, SSD temporary storage, and Haswell E5 Intel technology.</span></span>
* <span data-ttu-id="da23c-142">[Dimensioni di VM GPU][compute-gpu]: le dimensioni di VM ottimizzate per la GPU sono macchine virtuali specializzate disponibili con una o più GPU NVIDIA.</span><span class="sxs-lookup"><span data-stu-id="da23c-142">[GPU VM sizes][compute-gpu] GPU optimized VM sizes are specialized virtual machines available with single or multiple NVIDIA GPUs.</span></span> <span data-ttu-id="da23c-143">Queste dimensioni sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica.</span><span class="sxs-lookup"><span data-stu-id="da23c-143">These sizes are designed for compute-intensive, graphics-intensive, and visualization workloads.</span></span>
* <span data-ttu-id="da23c-144">Le dimensioni NC, NCv2, NCv3 e ND sono ottimizzate per applicazioni e algoritmi a elevato utilizzo di calcolo e reti, come applicazioni e simulazioni basate su OpenCL e CUDA, intelligenza artificiale e Deep Learning.</span><span class="sxs-lookup"><span data-stu-id="da23c-144">NC, NCv2, NCv3, and ND sizes are optimized for compute-intensive and network-intensive applications and algorithms, including CUDA and OpenCL-based applications and simulations, AI, and Deep Learning.</span></span> <span data-ttu-id="da23c-145">Le dimensioni NV sono ottimizzate e progettate per scenari di visualizzazione remota, streaming, giochi, codifica e VDI che utilizzano framework come OpenGL e DirectX.</span><span class="sxs-lookup"><span data-stu-id="da23c-145">NV sizes are optimized and designed for remote visualization, streaming, gaming, encoding, and VDI scenarios utilizing frameworks such as OpenGL and DirectX.</span></span>
* <span data-ttu-id="da23c-146">[Dimensioni di macchine virtuali ottimizzate per la memoria][compute-memory]: quando è necessaria una maggiore quantità di memoria, queste dimensioni di macchine virtuali offrono un rapporto memoria-CPU superiore.</span><span class="sxs-lookup"><span data-stu-id="da23c-146">[Memory optimized VM sizes][compute-memory] When more memory is required, the memory optimized VM sizes offer a higher memory-to-CPU ratio.</span></span>
* <span data-ttu-id="da23c-147">[Dimensioni di macchine virtuali per utilizzo generico][compute-general]: sono disponibili anche dimensioni di macchine virtuali per utilizzo generico che offrono un rapporto CPU-memoria equilibrato.</span><span class="sxs-lookup"><span data-stu-id="da23c-147">[General purposes VM sizes][compute-general] General-purpose VM sizes are also available and provide balanced CPU-to-memory ratio.</span></span>

### <a name="alternatives"></a><span data-ttu-id="da23c-148">Alternative</span><span class="sxs-lookup"><span data-stu-id="da23c-148">Alternatives</span></span>

<span data-ttu-id="da23c-149">Se si necessita di maggiore controllo sull'ambiente di rendering in Azure o di un'implementazione ibrida, l'elaborazione con CycleCloud consente di orchestrare una griglia IaaS nel cloud</span><span class="sxs-lookup"><span data-stu-id="da23c-149">If you require more control over your rendering environment in Azure or need a hybrid implementation, then CycleCloud computing can help orchestrate an IaaS grid in the cloud.</span></span> <span data-ttu-id="da23c-150">e, usando le stesse tecnologie Azure sottostanti di Azure Batch, rende efficiente il processo di creazione e gestione di una griglia IaaS.</span><span class="sxs-lookup"><span data-stu-id="da23c-150">Using the same underlying Azure technologies as Azure Batch, it makes building and maintaining an IaaS grid an efficient process.</span></span> <span data-ttu-id="da23c-151">Per altre informazioni sui principi di progettazione, usare il collegamento seguente:</span><span class="sxs-lookup"><span data-stu-id="da23c-151">To find out more and learn about the design principles use the following link:</span></span>

<span data-ttu-id="da23c-152">Per una panoramica completa di tutte le soluzioni HPC disponibili in Azure, vedere l'articolo [Soluzioni HPC, batch e Big Compute con macchine virtuali di Azure][hpc-alt-solutions]</span><span class="sxs-lookup"><span data-stu-id="da23c-152">For a complete overview of all the HPC solutions that are available to you in Azure, see the article [HPC, Batch, and Big Compute solutions using Azure VMs][hpc-alt-solutions]</span></span>

### <a name="availability"></a><span data-ttu-id="da23c-153">Disponibilità</span><span class="sxs-lookup"><span data-stu-id="da23c-153">Availability</span></span>

<span data-ttu-id="da23c-154">Funzionalità di monitoraggio dei componenti di Azure Batch sono disponibili tramite diversi servizi, strumenti e API.</span><span class="sxs-lookup"><span data-stu-id="da23c-154">Monitoring of the Azure Batch components is available through a range of services, tools, and APIs.</span></span> <span data-ttu-id="da23c-155">Il monitoraggio è illustrato in maggiore dettaglio nell'articolo [Monitorare le soluzioni Batch][batch-monitor].</span><span class="sxs-lookup"><span data-stu-id="da23c-155">Monitoring is discussed further in the [Monitor Batch solutions][batch-monitor] article.</span></span>

### <a name="scalability"></a><span data-ttu-id="da23c-156">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="da23c-156">Scalability</span></span>

<span data-ttu-id="da23c-157">I pool in un account Azure Batch possono essere ridimensionati manualmente oppure automaticamente con una formula basata sulle metriche di Azure Batch.</span><span class="sxs-lookup"><span data-stu-id="da23c-157">Pools within an Azure Batch account can either scale through manual intervention or, by using a formula based on Azure Batch metrics, be scaled automatically.</span></span> <span data-ttu-id="da23c-158">Per altre informazioni in proposito, vedere l'articolo [Creare una formula di scalabilità automatica per la scalabilità dei nodi di calcolo in un pool Batch][batch-scaling].</span><span class="sxs-lookup"><span data-stu-id="da23c-158">For more information on scalability, see the article [Create an automatic scaling formula for scaling nodes in a Batch pool][batch-scaling].</span></span>

### <a name="security"></a><span data-ttu-id="da23c-159">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="da23c-159">Security</span></span>

<span data-ttu-id="da23c-160">Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].</span><span class="sxs-lookup"><span data-stu-id="da23c-160">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="da23c-161">Resilienza</span><span class="sxs-lookup"><span data-stu-id="da23c-161">Resiliency</span></span>

<span data-ttu-id="da23c-162">Attualmente non sono presenti funzionalità di failover in Azure Batch, ma è consigliabile usare questa procedura per garantire la disponibilità in caso di interruzione non pianificata:</span><span class="sxs-lookup"><span data-stu-id="da23c-162">While there is currently no failover capability in Azure Batch, we recommend using the following steps to ensure availability if there is an unplanned outage:</span></span>

* <span data-ttu-id="da23c-163">Creare un account Azure Batch in un percorso di Azure alternativo con un account di archiviazione alternativo</span><span class="sxs-lookup"><span data-stu-id="da23c-163">Create an Azure Batch account in an alternate Azure location with an alternate Storage Account</span></span>
* <span data-ttu-id="da23c-164">Creare gli stessi pool di nodi con lo stesso nome, con 0 nodi allocati</span><span class="sxs-lookup"><span data-stu-id="da23c-164">Create the same node pools with the same name, with zero nodes allocated</span></span>
* <span data-ttu-id="da23c-165">Assicurarsi che le applicazioni vengano create e aggiornate nell'account di archiviazione alternativo</span><span class="sxs-lookup"><span data-stu-id="da23c-165">Ensure Applications are created and updated to the alternate Storage Account</span></span>
* <span data-ttu-id="da23c-166">Caricare i file di input e inviare i processi all'account Azure Batch alternativo</span><span class="sxs-lookup"><span data-stu-id="da23c-166">Upload input files and submit jobs to the alternate Azure Batch account</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="da23c-167">Distribuire lo scenario</span><span class="sxs-lookup"><span data-stu-id="da23c-167">Deploy this scenario</span></span>

### <a name="creating-an-azure-batch-account-and-pools-manually"></a><span data-ttu-id="da23c-168">Creazione manuale di un account e dei pool Azure Batch</span><span class="sxs-lookup"><span data-stu-id="da23c-168">Creating an Azure Batch account and pools manually</span></span>

<span data-ttu-id="da23c-169">Questo scenario di esempio è utile per apprendere il funzionamento di Azure Batch e presenta Azure Batch Labs come soluzione SaaS di esempio che è possibile sviluppare per i clienti:</span><span class="sxs-lookup"><span data-stu-id="da23c-169">This sample scenario helps in learning how Azure Batch works while showcasing Azure Batch Labs as an example SaaS solution that can be developed for your own customers:</span></span>

<span data-ttu-id="da23c-170">[Azure Batch Masterclass][batch-labs-masterclass]</span><span class="sxs-lookup"><span data-stu-id="da23c-170">[Azure Batch Masterclass][batch-labs-masterclass]</span></span>

### <a name="deploying-the-sample-scenario-using-an-azure-resource-manager-template"></a><span data-ttu-id="da23c-171">Distribuzione dello scenario di esempio con un modello di Azure Resource Manager</span><span class="sxs-lookup"><span data-stu-id="da23c-171">Deploying the sample scenario using an Azure Resource Manager template</span></span>

<span data-ttu-id="da23c-172">Il modello distribuirà quanto segue:</span><span class="sxs-lookup"><span data-stu-id="da23c-172">The template will deploy:</span></span>

* <span data-ttu-id="da23c-173">Un nuovo account Azure Batch</span><span class="sxs-lookup"><span data-stu-id="da23c-173">A new Azure Batch account</span></span>
* <span data-ttu-id="da23c-174">Un account di archiviazione</span><span class="sxs-lookup"><span data-stu-id="da23c-174">A storage account</span></span>
* <span data-ttu-id="da23c-175">Un pool di nodi associato all'account Batch</span><span class="sxs-lookup"><span data-stu-id="da23c-175">A node pool associated with the batch account</span></span>
* <span data-ttu-id="da23c-176">Il pool di nodi verrà configurato per l'uso di VM A2 v2 con immagini di Canonical Ubuntu</span><span class="sxs-lookup"><span data-stu-id="da23c-176">The node pool will be configured to use A2 v2 VMs with Canonical Ubuntu images</span></span>
* <span data-ttu-id="da23c-177">Il pool di nodi conterrà inizialmente 0 macchine virtuali e richiederà il ridimensionamento manuale per l'aggiunta di macchine virtuali</span><span class="sxs-lookup"><span data-stu-id="da23c-177">The node pool will contain zero VMs initially and will require you to manually scale to add VMs</span></span>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fhpc%2Fbatchcreatewithpools.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

<span data-ttu-id="da23c-178">[Altre informazioni sui modelli di Resource Manager][azure-arm-templates]</span><span class="sxs-lookup"><span data-stu-id="da23c-178">[Learn more about Resource Manager templates][azure-arm-templates]</span></span>

## <a name="pricing"></a><span data-ttu-id="da23c-179">Prezzi</span><span class="sxs-lookup"><span data-stu-id="da23c-179">Pricing</span></span>

<span data-ttu-id="da23c-180">Il costo correlato all'uso di Azure Batch dipenderà dalle dimensioni delle macchine virtuali usate per i pool e dalla durata dell'allocazione e dell'esecuzione. Alla creazione di un account Azure Batch non è associato alcun costo.</span><span class="sxs-lookup"><span data-stu-id="da23c-180">The cost of using Azure Batch will depend on the VM sizes that are used for the pools and how long these VMs are allocated and running, there is no cost associated with an Azure Batch account creation.</span></span> <span data-ttu-id="da23c-181">È necessario tenere in considerazione le risorse di archiviazione e i dati in uscita, perché comportano l'applicazione di costi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="da23c-181">Storage and data egress should be taken into account as these will apply additional costs.</span></span>

<span data-ttu-id="da23c-182">Di seguito sono riportati esempi dei costi che potrebbero essere addebitati per un processo completato in 8 ore con un diverso numero di server.</span><span class="sxs-lookup"><span data-stu-id="da23c-182">The following are examples of costs that could be incurred for a job that completes in 8 hours using a different number of servers:</span></span>

* <span data-ttu-id="da23c-183">100 macchine virtuali con CPU ad alte prestazioni: [stima dei costi][hpc-est-high]</span><span class="sxs-lookup"><span data-stu-id="da23c-183">100 High-Performance CPU VMs: [Cost Estimate][hpc-est-high]</span></span>

  <span data-ttu-id="da23c-184">100 x H16m (16 core, 225 GB di RAM, 512 GB di archiviazione Premium), 2 TB di archiviazione BLOB, 1 TB in uscita</span><span class="sxs-lookup"><span data-stu-id="da23c-184">100 x H16m (16 cores, 225 GB RAM, Premium Storage 512 GB), 2 TB Blob Storage, 1-TB egress</span></span>

* <span data-ttu-id="da23c-185">50 macchine virtuali con CPU ad alte prestazioni: [stima dei costi][hpc-est-med]</span><span class="sxs-lookup"><span data-stu-id="da23c-185">50 High-Performance CPU VMs: [Cost Estimate][hpc-est-med]</span></span>

  <span data-ttu-id="da23c-186">50 x H16m (16 core, 225 GB di RAM, 512 GB di archiviazione Premium), 2 TB di archiviazione BLOB, 1 TB in uscita</span><span class="sxs-lookup"><span data-stu-id="da23c-186">50 x H16m (16 cores, 225 GB RAM, Premium Storage 512 GB), 2 TB Blob Storage, 1-TB egress</span></span>

* <span data-ttu-id="da23c-187">10 macchine virtuali con CPU ad alte prestazioni: [stima dei costi][hpc-est-low]</span><span class="sxs-lookup"><span data-stu-id="da23c-187">10 High-Performance CPU VMs: [Cost Estimate][hpc-est-low]</span></span>
  
  <span data-ttu-id="da23c-188">10 x H16m (16 core, 225 GB di RAM, 512 GB di archiviazione Premium), 2 TB di archiviazione BLOB, 1 TB in uscita</span><span class="sxs-lookup"><span data-stu-id="da23c-188">10 x H16m (16 cores, 225 GB RAM, Premium Storage 512 GB), 2 TB Blob Storage, 1-TB egress</span></span>

### <a name="low-priority-vm-pricing"></a><span data-ttu-id="da23c-189">Prezzi delle macchine virtuali per priorità bassa</span><span class="sxs-lookup"><span data-stu-id="da23c-189">Low-Priority VM Pricing</span></span>

<span data-ttu-id="da23c-190">Azure Batch supporta anche l'uso di macchine virtuali per priorità bassa\* nei pool di nodi e questo può offrire un significativo risparmio sui costi.</span><span class="sxs-lookup"><span data-stu-id="da23c-190">Azure Batch also supports the use of Low-Priority VMs\* in the node pools, which can potentially provide a substantial cost saving.</span></span> <span data-ttu-id="da23c-191">Per un confronto dei prezzi tra macchine virtuali standard e macchine virtuali per priorità bassa e per altre informazioni sulle macchine virtuali per priorità bassa, vedere [Prezzi di Batch][batch-pricing].</span><span class="sxs-lookup"><span data-stu-id="da23c-191">For a price comparison between standard VMs and Low-Priority VMs, and to find out more about Low-Priority VMs, see [Batch Pricing][batch-pricing].</span></span>

<span data-ttu-id="da23c-192">\* Si noti che le macchine virtuali per priorità bassa sono idonee all'esecuzione solo di determinati carichi di lavoro e applicazioni.</span><span class="sxs-lookup"><span data-stu-id="da23c-192">\* Please note that only certain applications and workloads will be suitable to run on Low-Priority VMs.</span></span>

## <a name="related-resources"></a><span data-ttu-id="da23c-193">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="da23c-193">Related Resources</span></span>

<span data-ttu-id="da23c-194">[Panoramica di Azure Batch][batch-overview]</span><span class="sxs-lookup"><span data-stu-id="da23c-194">[Azure Batch Overview][batch-overview]</span></span>

<span data-ttu-id="da23c-195">[Documentazione di Azure Batch][batch-doc]</span><span class="sxs-lookup"><span data-stu-id="da23c-195">[Azure Batch Documentation][batch-doc]</span></span>

<span data-ttu-id="da23c-196">[Uso di contenitori in Azure Batch][batch-containers]</span><span class="sxs-lookup"><span data-stu-id="da23c-196">[Using containers on Azure Batch][batch-containers]</span></span>

<!-- links -->
[architecture]: ./media/native-hpc-ref-arch.png
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[resiliency]: /azure/architecture/resiliency/
[scalability]: /azure/architecture/checklist/scalability
[vmss]: /azure/virtual-machine-scale-sets/overview
[vnet]: /azure/virtual-network/virtual-networks-overview
[storage]: https://azure.microsoft.com/services/storage/
[batch]: https://azure.microsoft.com/services/batch/
[batch-arch]: https://azure.microsoft.com/solutions/architecture/big-compute-with-azure-batch/
[compute-hpc]: /azure/virtual-machines/windows/sizes-hpc
[compute-gpu]: /azure/virtual-machines/windows/sizes-gpu
[compute-compute]: /azure/virtual-machines/windows/sizes-compute
[compute-memory]: /azure/virtual-machines/windows/sizes-memory
[compute-general]: /azure/virtual-machines/windows/sizes-general
[compute-storage]: /azure/virtual-machines/windows/sizes-storage
[compute-acu]: /azure/virtual-machines/windows/acu
[compute=benchmark]: /azure/virtual-machines/windows/compute-benchmark-scores
[hpc-est-high]: https://azure.com/e/9ac25baf44ef49c3a6b156935ee9544c
[hpc-est-med]: https://azure.com/e/0286f1d6f6784310af4dcda5aec8c893
[hpc-est-low]: https://azure.com/e/e39afab4e71949f9bbabed99b428ba4a
[batch-labs-masterclass]: https://github.com/azurebigcompute/BigComputeLabs/tree/master/Azure%20Batch%20Masterclass%20Labs
[batch-scaling]: /azure/batch/batch-automatic-scaling
[hpc-alt-solutions]: /azure/virtual-machines/linux/high-performance-computing?toc=%2fazure%2fbatch%2ftoc.json
[batch-monitor]: /azure/batch/monitoring-overview
[batch-pricing]: https://azure.microsoft.com/en-gb/pricing/details/batch/
[batch-doc]: /azure/batch/
[batch-overview]: https://azure.microsoft.com/services/batch/
[batch-containers]: https://github.com/Azure/batch-shipyard
[azure-arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[batch-plugins]: /azure/batch/batch-rendering-service#options-for-submitting-a-render-job