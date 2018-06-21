---
title: Stile dell'architettura Big Compute
description: Descrive i vantaggi, le sfide e le procedure consigliate per le architetture Big Compute in Azure
author: MikeWasson
ms.openlocfilehash: b16be4133143d7d73062eeb280b44779c390f387
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
ms.locfileid: "24539786"
---
# <a name="big-compute-architecture-style"></a><span data-ttu-id="481cc-103">Stile dell'architettura Big Compute</span><span class="sxs-lookup"><span data-stu-id="481cc-103">Big compute architecture style</span></span>

<span data-ttu-id="481cc-104">Il termine *Big Compute* descrive i carichi di lavoro su larga scala che richiedono un numero elevato di core, spesso la numerazione in centinaia o migliaia.</span><span class="sxs-lookup"><span data-stu-id="481cc-104">The term *big compute* describes large-scale workloads that require a large number of cores, often numbering in the hundreds or thousands.</span></span> <span data-ttu-id="481cc-105">Gli scenari includono il rendering delle immagini, la dinamica dei fluidi, la modellazione dei rischi finanziari, la ricerca del petrolio, la creazione di farmaci e l'analisi delle sollecitazioni ingegneristiche, tra gli altri.</span><span class="sxs-lookup"><span data-stu-id="481cc-105">Scenarios include image rendering, fluid dynamics, financial risk modeling, oil exploration, drug design, and engineering stress analysis, among others.</span></span>

![](./images/big-compute-logical.png)

<span data-ttu-id="481cc-106">Ecco alcune caratteristiche tipiche delle applicazioni Big Compute:</span><span class="sxs-lookup"><span data-stu-id="481cc-106">Here are some typical characteristics of big compute applications:</span></span>

- <span data-ttu-id="481cc-107">È possibile dividere il lavoro in attività separate, che possono essere eseguite contemporaneamente su più core.</span><span class="sxs-lookup"><span data-stu-id="481cc-107">The work can be split into discrete tasks, which can be run across many cores simultaneously.</span></span>
- <span data-ttu-id="481cc-108">Ogni attività è finita.</span><span class="sxs-lookup"><span data-stu-id="481cc-108">Each task is finite.</span></span> <span data-ttu-id="481cc-109">Accetta l'input, esegue alcune operazioni di elaborazione e produce un output.</span><span class="sxs-lookup"><span data-stu-id="481cc-109">It takes some input, does some processing, and produces output.</span></span> <span data-ttu-id="481cc-110">Tutta l'applicazione viene eseguita per un periodo di tempo limitato, che può andare da minuti a giorni.</span><span class="sxs-lookup"><span data-stu-id="481cc-110">The entire application runs for a finite amount of time (minutes to days).</span></span> <span data-ttu-id="481cc-111">Un modello comune consiste nell'offrire un numero elevato di core in un burst e nell'interrompere l'esecuzione, quando l'applicazione termina.</span><span class="sxs-lookup"><span data-stu-id="481cc-111">A common pattern is to provision a large number of cores in a burst, and then spin down to zero once the application completes.</span></span> 
- <span data-ttu-id="481cc-112">Non è necessario che l'applicazione sia attiva 24 ore al giorno, 7 giorni alla settimana.</span><span class="sxs-lookup"><span data-stu-id="481cc-112">The application does not need to stay up 24/7.</span></span> <span data-ttu-id="481cc-113">Tuttavia, il sistema deve gestire gli errori del nodo o gli arresti anomali dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="481cc-113">However, the system must handle node failures or application crashes.</span></span>
- <span data-ttu-id="481cc-114">Per alcune applicazioni, le attività sono indipendenti e possono essere eseguite in parallelo.</span><span class="sxs-lookup"><span data-stu-id="481cc-114">For some applications, tasks are independent and can run in parallel.</span></span> <span data-ttu-id="481cc-115">In altri casi, le attività sono strettamente collegate, vale a dire che devono interagire o scambiarsi i risultati intermedi.</span><span class="sxs-lookup"><span data-stu-id="481cc-115">In other cases, tasks are tightly coupled, meaning they must interact or exchange intermediate results.</span></span> <span data-ttu-id="481cc-116">In questo caso considerare l'uso di tecnologie di rete ad alta velocità, come InfiniBand e l'accesso diretto a memoria remota.</span><span class="sxs-lookup"><span data-stu-id="481cc-116">In that case, consider using high-speed networking technologies such as InfiniBand and remote direct memory access (RDMA).</span></span> 
- <span data-ttu-id="481cc-117">A seconda del carico di lavoro, è possibile usare macchine virtuali con dimensioni per elevati livelli di calcolo, ad esempio H16r, H16mr e A9.</span><span class="sxs-lookup"><span data-stu-id="481cc-117">Depending on your workload, you might use compute-intensive VM sizes (H16r, H16mr, and A9).</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="481cc-118">Quando usare questa architettura</span><span class="sxs-lookup"><span data-stu-id="481cc-118">When to use this architecture</span></span>

- <span data-ttu-id="481cc-119">Le operazioni con elevati livelli di calcolo, ad esempio simulazione e l'uso dei numeri.</span><span class="sxs-lookup"><span data-stu-id="481cc-119">Computationally intensive operations such as simulation and number crunching.</span></span>
- <span data-ttu-id="481cc-120">Simulazioni con elevati livelli di calcolo che devono essere divise tra le CPU in più computer (da 10 a 1.000 volte).</span><span class="sxs-lookup"><span data-stu-id="481cc-120">Simulations that are computationally intensive and must be split across CPUs in multiple computers (10-1000s).</span></span>
- <span data-ttu-id="481cc-121">Simulazioni che richiedono una quantità eccessiva di memoria per un singolo computer e devono essere suddivise tra più computer.</span><span class="sxs-lookup"><span data-stu-id="481cc-121">Simulations that require too much memory for one computer, and must be split across multiple computers.</span></span>
- <span data-ttu-id="481cc-122">Calcoli a esecuzione prolungata che richiedono troppo tempo per essere completate su un solo computer.</span><span class="sxs-lookup"><span data-stu-id="481cc-122">Long-running computations that would take too long to complete on a single computer.</span></span>
- <span data-ttu-id="481cc-123">Calcoli più piccoli che devono essere eseguiti 100 o 1.000 volte, ad esempio le simulazioni di Monte Carlo.</span><span class="sxs-lookup"><span data-stu-id="481cc-123">Smaller computations that must be run 100s or 1000s of times, such as Monte Carlo simulations.</span></span>

## <a name="benefits"></a><span data-ttu-id="481cc-124">Vantaggi</span><span class="sxs-lookup"><span data-stu-id="481cc-124">Benefits</span></span>

- <span data-ttu-id="481cc-125">Prestazioni elevate con l'elaborazione "[a elevati livelli di parallelismo][embarrassingly-parallel]".</span><span class="sxs-lookup"><span data-stu-id="481cc-125">High performance with "[embarrassingly parallel][embarrassingly-parallel]" processing.</span></span>
- <span data-ttu-id="481cc-126">Può sfruttare centinaia o migliaia di core di computer per risolvere i problemi di grandi dimensioni più velocemente.</span><span class="sxs-lookup"><span data-stu-id="481cc-126">Can harness hundreds or thousands of computer cores to solve large problems faster.</span></span>
- <span data-ttu-id="481cc-127">Accesso all'hardware specializzato con prestazioni elevate, con reti dedicate InfiniBand ad alta velocità.</span><span class="sxs-lookup"><span data-stu-id="481cc-127">Access to specialized high-performance hardware, with dedicated high-speed InfiniBand networks.</span></span>
- <span data-ttu-id="481cc-128">È possibile eseguire il provisioning di macchine virtuali in base alle operazioni da eseguire e quindi chiuderle.</span><span class="sxs-lookup"><span data-stu-id="481cc-128">You can provision VMs as needed to do work, and then tear them down.</span></span> 

## <a name="challenges"></a><span data-ttu-id="481cc-129">Problematiche</span><span class="sxs-lookup"><span data-stu-id="481cc-129">Challenges</span></span>

- <span data-ttu-id="481cc-130">Gestione dell'infrastruttura della macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="481cc-130">Managing the VM infrastructure.</span></span>
- <span data-ttu-id="481cc-131">Gestione del volume nell'uso dei numeri.</span><span class="sxs-lookup"><span data-stu-id="481cc-131">Managing the volume of number crunching.</span></span> 
- <span data-ttu-id="481cc-132">Provisioning di migliaia di core in modo tempestivo.</span><span class="sxs-lookup"><span data-stu-id="481cc-132">Provisioning thousands of cores in a timely manner.</span></span>
- <span data-ttu-id="481cc-133">Per le attività strettamente collegate, l'aggiunta di più core può avere risultati riduttivi.</span><span class="sxs-lookup"><span data-stu-id="481cc-133">For tightly coupled tasks, adding more cores can have diminishing returns.</span></span> <span data-ttu-id="481cc-134">Potrebbe essere necessario riuscire a trovare il numero ottimale di core.</span><span class="sxs-lookup"><span data-stu-id="481cc-134">You may need to experiment to find the optimum number of cores.</span></span>

## <a name="big-compute-using-azure-batch"></a><span data-ttu-id="481cc-135">Big Compute con Azure Batch</span><span class="sxs-lookup"><span data-stu-id="481cc-135">Big compute using Azure Batch</span></span>

<span data-ttu-id="481cc-136">[Azure Batch][batch] è un servizio gestito per l'esecuzione di applicazioni high-performance computing su larga scala.</span><span class="sxs-lookup"><span data-stu-id="481cc-136">[Azure Batch][batch] is a managed service for running large-scale high-performance computing (HPC) applications.</span></span>

<span data-ttu-id="481cc-137">Usando Azure Batch configurare un pool di macchine virtuali e caricare le applicazioni e i file di dati.</span><span class="sxs-lookup"><span data-stu-id="481cc-137">Using Azure Batch, you configure a VM pool, and upload the applications and data files.</span></span> <span data-ttu-id="481cc-138">Quindi il servizio Batch esegue il provisioning delle macchine virtuali, assegna le attività alle macchine virtuali, esegue le attività e consente di monitorare lo stato di avanzamento.</span><span class="sxs-lookup"><span data-stu-id="481cc-138">Then the Batch service provisions the VMs, assign tasks to the VMs, runs the tasks, and monitors the progress.</span></span> <span data-ttu-id="481cc-139">Batch può automaticamente ridurre le macchine virtuali in risposta al carico di lavoro.</span><span class="sxs-lookup"><span data-stu-id="481cc-139">Batch can automatically scale out the VMs in response to the workload.</span></span> <span data-ttu-id="481cc-140">Batch offre anche la pianificazione dei processi.</span><span class="sxs-lookup"><span data-stu-id="481cc-140">Batch also provides job scheduling.</span></span>

![](./images/big-compute-batch.png) 

## <a name="big-compute-running-on-virtual-machines"></a><span data-ttu-id="481cc-141">Esecuzione di Big Compute su Macchine virtuali</span><span class="sxs-lookup"><span data-stu-id="481cc-141">Big compute running on Virtual Machines</span></span>

<span data-ttu-id="481cc-142">È possibile usare il [pacchetto Microsoft HPC][hpc-pack] per amministrare un cluster di macchine virtuali, pianificare e monitorare i processi HPC.</span><span class="sxs-lookup"><span data-stu-id="481cc-142">You can use [Microsoft HPC Pack][hpc-pack] to administer a cluster of VMs, and schedule and monitor HPC jobs.</span></span> <span data-ttu-id="481cc-143">Con questo approccio, è necessario eseguire il provisioning e gestire le macchine virtuali e l'infrastruttura di rete.</span><span class="sxs-lookup"><span data-stu-id="481cc-143">With this approach, you must provision and manage the VMs and network infrastructure.</span></span> <span data-ttu-id="481cc-144">Prendere in considerazione questo approccio se si hanno carichi di lavoro HPC esistenti e si desidera spostarne alcuni o tutti in Azure.</span><span class="sxs-lookup"><span data-stu-id="481cc-144">Consider this approach if you have existing HPC workloads and want to move some or all it to Azure.</span></span> <span data-ttu-id="481cc-145">Spostare l'intero cluster HPC in Azure, o mantenere il cluster HPC locale ma usare Azure per la capacità di burst.</span><span class="sxs-lookup"><span data-stu-id="481cc-145">You can move the entire HPC cluster to Azure, or keep your HPC cluster on-premises but use Azure for burst capacity.</span></span> <span data-ttu-id="481cc-146">Per altre informazioni vedere [Batch e soluzioni HPC per carichi di lavoro di calcolo su larga scala][batch-hpc-solutions].</span><span class="sxs-lookup"><span data-stu-id="481cc-146">For more information, see [Batch and HPC solutions for large-scale computing workloads][batch-hpc-solutions].</span></span>

### <a name="hpc-pack-deployed-to-azure"></a><span data-ttu-id="481cc-147">Pacchetto HPC distribuito in Azure</span><span class="sxs-lookup"><span data-stu-id="481cc-147">HPC Pack deployed to Azure</span></span>

<span data-ttu-id="481cc-148">In questo scenario viene creato il cluster HPC interamente in Azure.</span><span class="sxs-lookup"><span data-stu-id="481cc-148">In this scenario, the HPC cluster is created entirely within Azure.</span></span>

![](./images/big-compute-iaas.png) 
 
<span data-ttu-id="481cc-149">Il nodo head offre i servizi di gestione e programmazione dei processi nel cluster.</span><span class="sxs-lookup"><span data-stu-id="481cc-149">The head node provides management and job scheduling services to the cluster.</span></span> <span data-ttu-id="481cc-150">Per le attività strettamente collegate, usare una rete RDMA che offre una larghezza di banda molto elevata e comunicazioni a bassa latenza tra le macchine virtuali.</span><span class="sxs-lookup"><span data-stu-id="481cc-150">For tightly coupled tasks, use an RDMA network that provides very high bandwidth, low latency communication between VMs.</span></span> <span data-ttu-id="481cc-151">Per altre informazioni vedere [Distribuire un cluster HPC Pack 2016 in Azure][deploy-hpc-azure].</span><span class="sxs-lookup"><span data-stu-id="481cc-151">For more information see [Deploy an HPC Pack 2016 cluster in Azure][deploy-hpc-azure].</span></span>

### <a name="burst-an-hpc-cluster-to-azure"></a><span data-ttu-id="481cc-152">Potenziamento di un cluster HPC in Azure</span><span class="sxs-lookup"><span data-stu-id="481cc-152">Burst an HPC cluster to Azure</span></span>

<span data-ttu-id="481cc-153">In questo scenario un'organizzazione esegue il pacchetto HPC in locale e usa le macchine virtuali di Azure per la capacità di burst.</span><span class="sxs-lookup"><span data-stu-id="481cc-153">In this scenario, an organization is running HPC Pack on-premises, and uses Azure VMs for burst capacity.</span></span> <span data-ttu-id="481cc-154">Il nodo head del cluster è in locale.</span><span class="sxs-lookup"><span data-stu-id="481cc-154">The cluster head node is on-premises.</span></span> <span data-ttu-id="481cc-155">ExpressRoute o Gateway VPN connettono la rete locale alla rete virtuale di Azure.</span><span class="sxs-lookup"><span data-stu-id="481cc-155">ExpressRoute or VPN Gateway connects the on-premises network to the Azure VNet.</span></span>

![](./images/big-compute-hybrid.png) 


[batch]: /azure/batch/
[batch-hpc-solutions]: /azure/batch/batch-hpc-solutions
[deploy-hpc-azure]: /azure/virtual-machines/windows/hpcpack-2016-cluster
[embarrassingly-parallel]: https://en.wikipedia.org/wiki/Embarrassingly_parallel
[hpc-pack]: https://technet.microsoft.com/library/cc514029

 
