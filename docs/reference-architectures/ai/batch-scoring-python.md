---
title: Assegnazione del punteggio in batch per i modelli Python in Azure
description: Creare una soluzione scalabile per l'assegnazione del punteggio in batch per i modelli in base a una pianificazione in parallelo tramite Azure Batch per intelligenza artificiale.
author: njray
ms.date: 12/13/2018
ms.custom: azcat-ai
ms.openlocfilehash: 4c43a3dadab11cb8dcf163cf63618795299283ad
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111052"
---
# <a name="batch-scoring-of-python-models-on-azure"></a><span data-ttu-id="e80af-103">Assegnazione del punteggio in batch per i modelli Python in Azure</span><span class="sxs-lookup"><span data-stu-id="e80af-103">Batch scoring of Python models on Azure</span></span>

<span data-ttu-id="e80af-104">Questa architettura di riferimento illustra come creare una soluzione scalabile per l'assegnazione del punteggio in batch a molti modelli in base a una pianificazione in parallelo usando Azure Batch per intelligenza artificiale.</span><span class="sxs-lookup"><span data-stu-id="e80af-104">This reference architecture shows how to build a scalable solution for batch scoring many models on a schedule in parallel using Azure Batch AI.</span></span> <span data-ttu-id="e80af-105">La soluzione può essere usata come modello e supporta la generalizzazione per problemi diversi.</span><span class="sxs-lookup"><span data-stu-id="e80af-105">The solution can be used as a template and can generalize to different problems.</span></span>

<span data-ttu-id="e80af-106">Un'implementazione di riferimento per questa architettura è disponibile in  [GitHub][github].</span><span class="sxs-lookup"><span data-stu-id="e80af-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![Assegnazione del punteggio in batch per i modelli Python in Azure](./_images/batch-scoring-python.png)

<span data-ttu-id="e80af-108">**Scenario**: Questa soluzione consente di monitorare il funzionamento di un numero elevato di dispositivi in uno scenario IoT in cui ogni dispositivo invia le letture dei sensori in modo continuo.</span><span class="sxs-lookup"><span data-stu-id="e80af-108">**Scenario**: This solution monitors the operation of a large number of devices in an IoT setting where each device sends sensor readings continuously.</span></span> <span data-ttu-id="e80af-109">Si presuppone che ogni dispositivo abbia modelli di rilevamento delle anomalie sottoposti precedentemente a training per prevedere se una serie di misurazioni, aggregate per un intervallo di tempo predefinito, corrispondono o meno a un'anomalia.</span><span class="sxs-lookup"><span data-stu-id="e80af-109">Each device is assumed to have pre-trained anomaly detection models that need to be used to predict whether a series of measurements, that are aggregated over a predefined time interval, correspond to an anomaly or not.</span></span> <span data-ttu-id="e80af-110">In scenari reali, potrebbe trattarsi di un flusso di letture di sensori che devono essere filtrate e aggregate prima di essere usate per operazioni di training o per l'assegnazione del punteggio in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="e80af-110">In real-world scenarios, this could be a stream of sensor readings that need to be filtered and aggregated before being used in training or real-time scoring.</span></span> <span data-ttu-id="e80af-111">Per semplicità, la soluzione usa lo stesso file di dati durante l'esecuzione dei processi di assegnazione del punteggio.</span><span class="sxs-lookup"><span data-stu-id="e80af-111">For simplicity, the solution uses the same data file when executing scoring jobs.</span></span>

## <a name="architecture"></a><span data-ttu-id="e80af-112">Architettura</span><span class="sxs-lookup"><span data-stu-id="e80af-112">Architecture</span></span>

<span data-ttu-id="e80af-113">L'architettura è costituita dai componenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="e80af-113">This architecture consists of the following components:</span></span>

<span data-ttu-id="e80af-114">[Hub eventi di Azure][event-hubs].</span><span class="sxs-lookup"><span data-stu-id="e80af-114">[Azure Event Hubs][event-hubs].</span></span> <span data-ttu-id="e80af-115">Questo servizio di inserimento dei messaggi può inserire milioni di messaggi di eventi al secondo.</span><span class="sxs-lookup"><span data-stu-id="e80af-115">This message ingestion service can ingest millions of event messages per second.</span></span> <span data-ttu-id="e80af-116">In questa architettura, i sensori inviano un flusso di dati all'hub eventi.</span><span class="sxs-lookup"><span data-stu-id="e80af-116">In this architecture, sensors send a stream of data to the event hub.</span></span>

<span data-ttu-id="e80af-117">[Analisi di flusso di Azure][stream-analytics].</span><span class="sxs-lookup"><span data-stu-id="e80af-117">[Azure Stream Analytics][stream-analytics].</span></span> <span data-ttu-id="e80af-118">Un motore di elaborazione di eventi.</span><span class="sxs-lookup"><span data-stu-id="e80af-118">An event-processing engine.</span></span> <span data-ttu-id="e80af-119">Un processo di Analisi di flusso legge i flussi di dati dall'hub eventi ed esegue l'elaborazione dei flussi.</span><span class="sxs-lookup"><span data-stu-id="e80af-119">A Stream Analytics job reads the data streams from the event hub and performs stream processing.</span></span>

<span data-ttu-id="e80af-120">[Azure Batch per intelligenza artificiale][batch-ai].</span><span class="sxs-lookup"><span data-stu-id="e80af-120">[Azure Batch AI][batch-ai].</span></span> <span data-ttu-id="e80af-121">Questo motore di calcolo distribuito viene usato per eseguire training e test dei modelli di Machine Learning e di intelligenza artificiale su larga scala.</span><span class="sxs-lookup"><span data-stu-id="e80af-121">This distributed computing engine is used to train and test machine learning and AI models at scale in Azure.</span></span> <span data-ttu-id="e80af-122">Batch per intelligenza artificiale consente di creare macchine virtuali su richiesta con un'opzione di scalabilità automatica, in cui ogni nodo del cluster di Batch per intelligenza artificiale esegue un processo di assegnazione del punteggio per un sensore specifico.</span><span class="sxs-lookup"><span data-stu-id="e80af-122">Batch AI creates virtual machines on demand with an automatic scaling option, where each node in the Batch AI cluster runs a scoring job for a specific sensor.</span></span> <span data-ttu-id="e80af-123">Lo  [script][python-script] Python viene eseguito in contenitori Docker che vengono creati in ogni nodo del cluster, in cui legge i dati del sensore pertinenti, genera stime e le archivia nell'archivio Blob.</span><span class="sxs-lookup"><span data-stu-id="e80af-123">The scoring Python [script][python-script] runs in Docker containers that are created on each node of the cluster, where it reads the relevant sensor data, generates predictions and stores them in Blob storage.</span></span>

<span data-ttu-id="e80af-124">[Archiviazione BLOB di Azure][storage].</span><span class="sxs-lookup"><span data-stu-id="e80af-124">[Azure Blob Storage][storage].</span></span> <span data-ttu-id="e80af-125">I contenitori BLOB vengono usati per archiviare i modelli già sottoposti a training, i dati e le stime di output.</span><span class="sxs-lookup"><span data-stu-id="e80af-125">Blob containers are used to store the pretrained models, the data, and the output predictions.</span></span> <span data-ttu-id="e80af-126">I modelli vengono caricati nell'archivio BLOB nel notebook [create\_resources.ipynb][create-resources].</span><span class="sxs-lookup"><span data-stu-id="e80af-126">The models are uploaded to Blob storage in the [create\_resources.ipynb][create-resources] notebook.</span></span> <span data-ttu-id="e80af-127">I modelli [one-class SVM][one-class-svm] vengono sottoposti a training su dati che rappresentano i valori di sensori diversi per diversi dispositivi.</span><span class="sxs-lookup"><span data-stu-id="e80af-127">These [one-class SVM][one-class-svm] models are trained on data that represents values of different sensors for different devices.</span></span> <span data-ttu-id="e80af-128">Questa soluzione presuppone che i valori dei dati vengano aggregati per un intervallo di tempo fisso.</span><span class="sxs-lookup"><span data-stu-id="e80af-128">This solution assumes that the data values are aggregated over a fixed interval of time.</span></span>

<span data-ttu-id="e80af-129">[App per la logica di Azure][logic-apps].</span><span class="sxs-lookup"><span data-stu-id="e80af-129">[Azure Logic Apps][logic-apps].</span></span> <span data-ttu-id="e80af-130">Questa soluzione crea un'app per la logica che esegue processi di Batch per intelligenza artificiale ogni ora.</span><span class="sxs-lookup"><span data-stu-id="e80af-130">This solution creates a Logic App that runs hourly Batch AI jobs.</span></span> <span data-ttu-id="e80af-131">App per la logica offre un modo semplice per creare il flusso di lavoro di runtime e la pianificazione per la soluzione.</span><span class="sxs-lookup"><span data-stu-id="e80af-131">Logic Apps provides an easy way to create the runtime workflow and scheduling for the solution.</span></span> <span data-ttu-id="e80af-132">I processi di Batch per intelligenza artificiale vengono inviati tramite uno [script][script] Python, anch'esso eseguito in un contenitore Docker.</span><span class="sxs-lookup"><span data-stu-id="e80af-132">The Batch AI jobs are submitted using a Python [script][script] that also runs in a Docker container.</span></span>

<span data-ttu-id="e80af-133">[Registro Azure Container][acr].</span><span class="sxs-lookup"><span data-stu-id="e80af-133">[Azure Container Registry][acr].</span></span> <span data-ttu-id="e80af-134">Le immagini Docker vengono usate sia in Batch per intelligenza artificiale che in App per la logica e vengono create nel notebook [create\_resources.ipynb][create-resources], quindi ne viene eseguito il push in Registro Container.</span><span class="sxs-lookup"><span data-stu-id="e80af-134">Docker images are used in both Batch AI and Logic Apps and are created in the [create\_resources.ipynb][create-resources] notebook, then pushed to Container Registry.</span></span> <span data-ttu-id="e80af-135">Si ottiene così un modo pratico per ospitare le immagini e creare istanze dei contenitori tramite altri servizi di Azure, ovvero App per la logica e Batch per intelligenza artificiale in questa soluzione.</span><span class="sxs-lookup"><span data-stu-id="e80af-135">This provides a convenient way to host images and instantiate containers through other Azure services—Logic Apps and Batch AI in this solution.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="e80af-136">Considerazioni sulle prestazioni</span><span class="sxs-lookup"><span data-stu-id="e80af-136">Performance considerations</span></span>

<span data-ttu-id="e80af-137">Per i modelli Python standard, le CPU sono generalmente considerate sufficienti per gestire il carico di lavoro.</span><span class="sxs-lookup"><span data-stu-id="e80af-137">For standard Python models, it's generally accepted that CPUs are sufficient to handle the workload.</span></span> <span data-ttu-id="e80af-138">Questa architettura usa CPU.</span><span class="sxs-lookup"><span data-stu-id="e80af-138">This architecture uses CPUs.</span></span> <span data-ttu-id="e80af-139">Per [carichi di lavoro di Deep Learning][deep], tuttavia, le GPU in genere garantiscono prestazioni nettamente superiori rispetto alle CPU, per cui di norma è necessario un cluster consistente di CPU per ottenere prestazioni analoghe.</span><span class="sxs-lookup"><span data-stu-id="e80af-139">However, for [deep learning workloads][deep], GPUs generally outperform CPUs by a considerable amount—a sizeable cluster of CPUs is usually needed to get comparable performance.</span></span>

### <a name="parallelizing-across-vms-vs-cores"></a><span data-ttu-id="e80af-140">Esecuzione in parallelo tra macchine virtuali e core a confronto</span><span class="sxs-lookup"><span data-stu-id="e80af-140">Parallelizing across VMs vs cores</span></span>

<span data-ttu-id="e80af-141">Durante l'esecuzione dei processi di assegnazione del punteggio a molti modelli in modalità batch, i processi devono essere eseguiti in parallelo tra le macchine virtuali.</span><span class="sxs-lookup"><span data-stu-id="e80af-141">When running scoring processes of many models in batch mode, the jobs need to be parallelized across VMs.</span></span> <span data-ttu-id="e80af-142">Sono possibili due approcci:</span><span class="sxs-lookup"><span data-stu-id="e80af-142">Two approaches are possible:</span></span>

* <span data-ttu-id="e80af-143">Creare un cluster più grande con macchine virtuali a basso costo.</span><span class="sxs-lookup"><span data-stu-id="e80af-143">Create a larger cluster using low-cost VMs.</span></span>

* <span data-ttu-id="e80af-144">Creare un cluster più piccolo con macchine virtuali a prestazioni elevate con più core disponibili in ognuna.</span><span class="sxs-lookup"><span data-stu-id="e80af-144">Create a smaller cluster using high performing VMs with more cores available on each.</span></span>

<span data-ttu-id="e80af-145">In generale, l'assegnazione del punteggio a modelli di Python standard non è così impegnativa come l'assegnazione del punteggio a modelli di Deep Learning e un cluster piccolo dovrebbe deve essere in grado di gestire in modo efficiente un numero elevato di modelli in coda.</span><span class="sxs-lookup"><span data-stu-id="e80af-145">In general, scoring of standard Python models is not as demanding as scoring of deep learning models, and a small cluster should be able to handle a large number of queued models efficiently.</span></span> <span data-ttu-id="e80af-146">È possibile aumentare il numero di nodi del cluster man mano che aumentano le dimensioni dei set di dati.</span><span class="sxs-lookup"><span data-stu-id="e80af-146">You can increase the number of cluster nodes as the dataset sizes increase.</span></span>

<span data-ttu-id="e80af-147">Per motivi di praticità in questo scenario, una sola attività di assegnazione del punteggio viene inviata all'interno di un singolo processo di Batch per intelligenza artificiale.</span><span class="sxs-lookup"><span data-stu-id="e80af-147">For convenience in this scenario, one scoring task is submitted within a single Batch AI job.</span></span> <span data-ttu-id="e80af-148">Tuttavia, può essere più efficiente assegnare il punteggio a più blocchi di dati all'interno dello stesso processo di Batch per intelligenza artificiale.</span><span class="sxs-lookup"><span data-stu-id="e80af-148">However, it can be more efficient to score multiple data chunks within the same Batch AI job.</span></span> <span data-ttu-id="e80af-149">In questi casi, scrivere codice personalizzato per leggere in più set di dati ed eseguire lo script di assegnazione del punteggio per tali set durante l'esecuzione di un singolo processo di Batch per intelligenza artificiale.</span><span class="sxs-lookup"><span data-stu-id="e80af-149">In those cases, write custom code to read in multiple datasets and execute the scoring script for those during a single Batch AI job execution.</span></span>

### <a name="file-servers"></a><span data-ttu-id="e80af-150">File server</span><span class="sxs-lookup"><span data-stu-id="e80af-150">File servers</span></span>

<span data-ttu-id="e80af-151">Quando si usa Azure Batch per intelligenza artificiale, è possibile scegliere più opzioni di archiviazione, a seconda della velocità effettiva necessaria per lo scenario.</span><span class="sxs-lookup"><span data-stu-id="e80af-151">When using Batch AI, you can choose multiple storage options depending on the throughput needed for your scenario.</span></span> <span data-ttu-id="e80af-152">Per carichi di lavoro con requisiti di bassa velocità effettiva, l'uso dell'archivio BLOB dovrebbe essere sufficiente.</span><span class="sxs-lookup"><span data-stu-id="e80af-152">For workloads with low throughput requirements, using blob storage should be enough.</span></span> <span data-ttu-id="e80af-153">In alternativa, Azure Batch per intelligenza artificiale supporta anche un [file server Batch per intelligenza artificiale][bai-file-server], un NFS a nodo singolo gestito che può essere montato automaticamente su nodi del cluster per offrire una posizione di archiviazione accessibile a livello centrale per i processi.</span><span class="sxs-lookup"><span data-stu-id="e80af-153">Alternatively, Batch AI also supports a [Batch AI File Server][bai-file-server], a managed, single-node NFS, which can be automatically mounted on cluster nodes to provide a centrally accessible storage location for jobs.</span></span> <span data-ttu-id="e80af-154">Nella maggior parte dei casi è necessario un solo file server in un'area di lavoro ed è possibile separare i dati per i processi di training in directory diverse.</span><span class="sxs-lookup"><span data-stu-id="e80af-154">For most cases, only one file server is needed in a workspace, and you can separate data for your training jobs into different directories.</span></span>

<span data-ttu-id="e80af-155">Se un NFS a nodo singolo non è appropriato per i carichi di lavoro, Azure Batch per intelligenza artificiale supporta altre opzioni di archiviazione, tra cui [File di Azure][azure-files] e soluzioni personalizzate, come ad esempio un file system Gluster o Lustre.</span><span class="sxs-lookup"><span data-stu-id="e80af-155">If a single-node NFS isn't appropriate for your workloads, Batch AI supports other storage options, including [Azure Files][azure-files] and custom solutions such as a Gluster or Lustre file system.</span></span>

## <a name="management-considerations"></a><span data-ttu-id="e80af-156">Considerazioni sulla gestione</span><span class="sxs-lookup"><span data-stu-id="e80af-156">Management considerations</span></span>

### <a name="monitoring-batch-ai-jobs"></a><span data-ttu-id="e80af-157">Monitoraggio dei processi Batch per intelligenza artificiale</span><span class="sxs-lookup"><span data-stu-id="e80af-157">Monitoring Batch AI jobs</span></span>

<span data-ttu-id="e80af-158">È importante monitorare lo stato dei processi in esecuzione, ma può essere complesso eseguire il monitoraggio in un cluster di nodi attivi.</span><span class="sxs-lookup"><span data-stu-id="e80af-158">It's important to monitor the progress of running jobs, but it can be a challenge to monitor across a cluster of active nodes.</span></span> <span data-ttu-id="e80af-159">Per ottenere un'idea dello stato complessivo del cluster, passare al pannello **Batch per intelligenza artificiale** del [portale di Azure][portal] per controllare lo stato dei nodi del cluster.</span><span class="sxs-lookup"><span data-stu-id="e80af-159">To get a sense of the overall state of the cluster, go to the **Batch AI** blade of the [Azure Portal][portal] to inspect the state of the nodes in the cluster.</span></span> <span data-ttu-id="e80af-160">Se un nodo è inattivo oppure un processo ha avuto esito negativo, i log degli errori vengono salvati nell'archivio BLOB e sono accessibili anche dal pannello **Processi** nel portale.</span><span class="sxs-lookup"><span data-stu-id="e80af-160">If a node is inactive or a job has failed, the error logs are saved to blob storage, and are also accessible in the **Jobs** blade of the portal.</span></span>

<span data-ttu-id="e80af-161">Per operazioni di monitoraggio più avanzate, connettere i log ad [Application Insights][ai] o eseguire processi separati per il polling dello stato del cluster di Batch per intelligenza artificiale e dei relativi processi.</span><span class="sxs-lookup"><span data-stu-id="e80af-161">For richer monitoring, connect logs to [Application Insights][ai], or run separate processes to poll for the state of the Batch AI cluster and its jobs.</span></span>

### <a name="logging-in-batch-ai"></a><span data-ttu-id="e80af-162">Registrazione in Batch per intelligenza artificiale</span><span class="sxs-lookup"><span data-stu-id="e80af-162">Logging in Batch AI</span></span>

<span data-ttu-id="e80af-163">Batch per intelligenza artificiale registra tutti i flussi stdout/stderr nell'account di archiviazione di Azure associato.</span><span class="sxs-lookup"><span data-stu-id="e80af-163">Batch AI logs all stdout/stderr to the associated Azure storage account.</span></span> <span data-ttu-id="e80af-164">Per semplificare l'esplorazione dei file di log, usare uno strumento di esplorazione dell'archiviazione, ad esempio [Azure Storage Explorer][explorer].</span><span class="sxs-lookup"><span data-stu-id="e80af-164">For easy navigation of the log files, use a storage navigation tool such as [Azure Storage Explorer][explorer].</span></span>

<span data-ttu-id="e80af-165">Quando si distribuisce questa architettura di riferimento, è possibile configurare un sistema di registrazione più semplice.</span><span class="sxs-lookup"><span data-stu-id="e80af-165">When you deploy this reference architecture, you have the option to set up a simpler logging system.</span></span> <span data-ttu-id="e80af-166">Con questa opzione, tutti i log tra i diversi processi vengono salvati nella stessa directory nel contenitore BLOB come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="e80af-166">With this option, all the logs across the different jobs are saved to the same directory in your blob container as shown below.</span></span> <span data-ttu-id="e80af-167">Usare questi log per monitorare il tempo necessario per l'elaborazione di ogni processo e ogni immagine, in modo da ottenere un quadro più preciso di come ottimizzare il processo.</span><span class="sxs-lookup"><span data-stu-id="e80af-167">Use these logs to monitor how long it takes for each job and each image to process, so you have a better sense of how to optimize the process.</span></span>

![Esplora archivi Azure](./_images/batch-scoring-python-monitor.png)

## <a name="cost-considerations"></a><span data-ttu-id="e80af-169">Considerazioni sul costo</span><span class="sxs-lookup"><span data-stu-id="e80af-169">Cost considerations</span></span>

<span data-ttu-id="e80af-170">I componenti più onerosi usati in questa architettura di riferimento sono le risorse di calcolo.</span><span class="sxs-lookup"><span data-stu-id="e80af-170">The most expensive components used in this reference architecture are the compute resources.</span></span>

<span data-ttu-id="e80af-171">Le dimensioni del cluster di Batch per intelligenza artificiale possono aumentare o diminuire a seconda dei processi nella coda.</span><span class="sxs-lookup"><span data-stu-id="e80af-171">The Batch AI cluster size scales up and down depending on the jobs in the queue.</span></span> <span data-ttu-id="e80af-172">La [scalabilità automatica][automatic-scaling] con Batch per intelligenza artificiale può essere abilitata in due modi.</span><span class="sxs-lookup"><span data-stu-id="e80af-172">You can enable [automatic scaling][automatic-scaling] with Batch AI in one of two ways.</span></span> <span data-ttu-id="e80af-173">È possibile procedere a livello programmatico, tramite configurazione nel file env che fa parte della [procedura di distribuzione][github], oppure modificando la formula di ridimensionamento direttamente nel portale dopo la creazione del cluster.</span><span class="sxs-lookup"><span data-stu-id="e80af-173">You can do so programmatically, which can be configured in the .env file that is part of the [deployment steps][github], or you can change the scale formula directly in the portal after the cluster is created.</span></span>

<span data-ttu-id="e80af-174">Per le operazioni che non richiedono un intervento immediato, configurare la formula di scalabilità automatica in modo che lo stato predefinito (minimo) sia rappresentato da un cluster con un numero di nodi pari a zero.</span><span class="sxs-lookup"><span data-stu-id="e80af-174">For work that doesn't require immediate processing, configure the automatic scaling formula so the default state (minimum) is a cluster of zero nodes.</span></span> <span data-ttu-id="e80af-175">Con questa configurazione, il cluster inizia con un numero di nodi pari a zero, per poi aumentare quando rileva processi nella coda.</span><span class="sxs-lookup"><span data-stu-id="e80af-175">With this configuration, the cluster starts with zero nodes and only scales up when it detects jobs in the queue.</span></span> <span data-ttu-id="e80af-176">Se il processo di assegnazione punteggio batch si verifica poche volte al giorno, questa impostazione consente di ottenere risparmi significativi sui costi.</span><span class="sxs-lookup"><span data-stu-id="e80af-176">If the batch scoring process only happens a few times a day or less, this setting enables significant cost savings.</span></span>

<span data-ttu-id="e80af-177">La scalabilità automatica potrebbe non essere appropriata per i processi batch eseguiti a distanza troppo ravvicinata.</span><span class="sxs-lookup"><span data-stu-id="e80af-177">Automatic scaling may not be appropriate for batch jobs that happen too close to each other.</span></span> <span data-ttu-id="e80af-178">Anche il tempo necessario per avviare e interrompere un cluster comporta dei costi. Pertanto, se un carico di lavoro batch inizia solo pochi minuti dopo il termine del processo precedente, potrebbe essere più conveniente lasciare il cluster attivo tra i processi.</span><span class="sxs-lookup"><span data-stu-id="e80af-178">The time that it takes for a cluster to spin up and spin down also incur a cost, so if a batch workload begins only a few minutes after the previous job ends, it might be more cost effective to keep the cluster running between jobs.</span></span> <span data-ttu-id="e80af-179">Ciò dipende dalla frequenza pianificata per l'esecuzione dei processi di assegnazione del punteggio, ovvero elevata (ad esempio, ogni ora) o meno frequente (ad esempio, una volta al mese).</span><span class="sxs-lookup"><span data-stu-id="e80af-179">That depends on whether scoring processes are scheduled to run at a high frequency (every hour, for example), or less frequently (once a month, for example).</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="e80af-180">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="e80af-180">Deploy the solution</span></span>

<span data-ttu-id="e80af-181">L'implementazione di riferimento per questa architettura è disponibile in [GitHub][github].</span><span class="sxs-lookup"><span data-stu-id="e80af-181">The reference implementation of this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="e80af-182">Seguire la procedura di configurazione per creare una soluzione scalabile per l'assegnazione del punteggio a molti modelli in parallelo tramite Batch per intelligenza artificiale.</span><span class="sxs-lookup"><span data-stu-id="e80af-182">Follow the setup steps there to build a scalable solution for scoring many models in parallel using Batch AI.</span></span>

[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[automatic-scaling]: /azure/batch/batch-automatic-scaling
[azure-files]: /azure/storage/files/storage-files-introduction
[batch-ai]: /azure/batch-ai/
[bai-file-server]: /azure/batch-ai/resource-concepts#file-server
[create-resources]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/create_resources.ipynb
[deep]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[event-hubs]: /azure/event-hubs/event-hubs-geo-dr
[explorer]: https://azure.microsoft.com/en-us/features/storage-explorer/
[github]: https://github.com/Azure/BatchAIAnomalyDetection
[logic-apps]: /azure/logic-apps/logic-apps-overview
[one-class-svm]: http://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html
[portal]: https://portal.azure.com
[python-script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/batchai/predict.py
[script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/sched/submit_jobs.py
[storage]: /azure/storage/blobs/storage-blobs-overview
[stream-analytics]: /azure/stream-analytics/