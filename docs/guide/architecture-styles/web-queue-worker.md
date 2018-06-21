---
title: Stile di architettura Web/coda/ruolo di lavoro
description: Descrive i vantaggi, le problematiche e le procedure consigliate per l'architettura Web/coda/ruolo di lavoro in Azure
author: MikeWasson
ms.openlocfilehash: 545472e71ffcd43717ad24af0dc9218a221ca910
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
ms.locfileid: "24539802"
---
# <a name="web-queue-worker-architecture-style"></a><span data-ttu-id="52428-103">Stile di architettura Web/coda/ruolo di lavoro</span><span class="sxs-lookup"><span data-stu-id="52428-103">Web-Queue-Worker architecture style</span></span>

<span data-ttu-id="52428-104">I componenti di base di questa architettura sono un **front-end Web** che gestisce le richieste client e un **ruolo di lavoro** che esegue attività a elevato utilizzo di risorse, flussi di lavoro a esecuzione prolungata o processi batch.</span><span class="sxs-lookup"><span data-stu-id="52428-104">The core components of this architecture are a **web front end** that serves client requests, and a **worker** that performs resource-intensive tasks, long-running workflows, or batch jobs.</span></span>  <span data-ttu-id="52428-105">Il front-end Web comunica con il ruolo di lavoro tramite una **coda di messaggi**.</span><span class="sxs-lookup"><span data-stu-id="52428-105">The web front end communicates with the worker through a **message queue**.</span></span>  

![](./images/web-queue-worker-logical.svg)

<span data-ttu-id="52428-106">Altri componenti comunemente integrati in questa architettura sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="52428-106">Other components that are commonly incorporated into this architecture include:</span></span>

- <span data-ttu-id="52428-107">Uno o più database.</span><span class="sxs-lookup"><span data-stu-id="52428-107">One or more databases.</span></span> 
- <span data-ttu-id="52428-108">Una cache per archiviare i valori dal database per letture rapide.</span><span class="sxs-lookup"><span data-stu-id="52428-108">A cache to store values from the database for quick reads.</span></span>
- <span data-ttu-id="52428-109">Rete CDN per rendere disponibile il contenuto statico</span><span class="sxs-lookup"><span data-stu-id="52428-109">CDN to serve static content</span></span>
- <span data-ttu-id="52428-110">Servizi remoti, tra cui i servizi di posta elettronica o SMS.</span><span class="sxs-lookup"><span data-stu-id="52428-110">Remote services, such as email or SMS service.</span></span> <span data-ttu-id="52428-111">Questi vengono spesso forniti da terze parti.</span><span class="sxs-lookup"><span data-stu-id="52428-111">Often these are provided by third parties.</span></span>
- <span data-ttu-id="52428-112">Provider di identità per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="52428-112">Identity provider for authentication.</span></span>

<span data-ttu-id="52428-113">Il front-end Web e il ruolo di lavoro sono entrambi senza stato.</span><span class="sxs-lookup"><span data-stu-id="52428-113">The web and worker are both stateless.</span></span> <span data-ttu-id="52428-114">Lo stato della sessione può essere archiviato in una cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="52428-114">Session state can be stored in a distributed cache.</span></span> <span data-ttu-id="52428-115">Qualsiasi operazione a esecuzione prolungata viene eseguita in modo asincrono dal ruolo di lavoro.</span><span class="sxs-lookup"><span data-stu-id="52428-115">Any long-running work is done asynchronously by the worker.</span></span> <span data-ttu-id="52428-116">Il ruolo di lavoro può essere attivato da messaggi nella coda oppure può essere eseguito in base a una pianificazione per l'elaborazione batch.</span><span class="sxs-lookup"><span data-stu-id="52428-116">The worker can be triggered by messages on the queue, or run on a schedule for batch processing.</span></span> <span data-ttu-id="52428-117">Il ruolo di lavoro è un componente facoltativo.</span><span class="sxs-lookup"><span data-stu-id="52428-117">The worker is an optional component.</span></span> <span data-ttu-id="52428-118">In assenza di operazioni a esecuzione prolungata, il ruolo di lavoro può essere omesso.</span><span class="sxs-lookup"><span data-stu-id="52428-118">If there are no long-running operations, the worker can be omitted.</span></span>  

<span data-ttu-id="52428-119">Il front-end può essere costituito da un'API Web.</span><span class="sxs-lookup"><span data-stu-id="52428-119">The front end might consist of a web API.</span></span> <span data-ttu-id="52428-120">Sul lato client l'API Web può essere utilizzata da un'applicazione a singola pagina che effettua chiamate AJAX oppure da un'applicazione client nativa.</span><span class="sxs-lookup"><span data-stu-id="52428-120">On the client side, the web API can be consumed by a single-page application that makes AJAX calls, or by a native client application.</span></span>

## <a name="when-to-use-this-architecture"></a><span data-ttu-id="52428-121">Quando usare questa architettura</span><span class="sxs-lookup"><span data-stu-id="52428-121">When to use this architecture</span></span>

<span data-ttu-id="52428-122">L'architettura Web/coda/ruolo di lavoro viene in genere implementata usando servizi di calcolo gestiti, Servizio app di Azure o Servizi cloud di Azure.</span><span class="sxs-lookup"><span data-stu-id="52428-122">The Web-Queue-Worker architecture is typically implemented using managed compute services, either Azure App Service or Azure Cloud Services.</span></span> 

<span data-ttu-id="52428-123">Prendere in considerazione questo stile di architettura per:</span><span class="sxs-lookup"><span data-stu-id="52428-123">Consider this architecture style for:</span></span>

- <span data-ttu-id="52428-124">Applicazioni con un dominio relativamente semplice.</span><span class="sxs-lookup"><span data-stu-id="52428-124">Applications with a relatively simple domain.</span></span>
- <span data-ttu-id="52428-125">Applicazioni con alcuni flussi di lavoro o operazioni batch a esecuzione prolungata.</span><span class="sxs-lookup"><span data-stu-id="52428-125">Applications with some long-running workflows or batch operations.</span></span>
- <span data-ttu-id="52428-126">Quando si vuole usare servizi gestiti invece di un'infrastruttura distribuita come servizio (IaaS).</span><span class="sxs-lookup"><span data-stu-id="52428-126">When you want to use managed services, rather than infrastructure as a service (IaaS).</span></span>

## <a name="benefits"></a><span data-ttu-id="52428-127">Vantaggi</span><span class="sxs-lookup"><span data-stu-id="52428-127">Benefits</span></span>

- <span data-ttu-id="52428-128">Architettura relativamente semplice e di facile comprensione.</span><span class="sxs-lookup"><span data-stu-id="52428-128">Relatively simple architecture that is easy to understand.</span></span>
- <span data-ttu-id="52428-129">Semplicità di distribuzione e gestione.</span><span class="sxs-lookup"><span data-stu-id="52428-129">Easy to deploy and manage.</span></span>
- <span data-ttu-id="52428-130">Netta separazione delle attività.</span><span class="sxs-lookup"><span data-stu-id="52428-130">Clear separation of concerns.</span></span>
- <span data-ttu-id="52428-131">Il front-end viene separato dal ruolo di lavoro tramite messaggistica asincrona.</span><span class="sxs-lookup"><span data-stu-id="52428-131">The front end is decoupled from the worker using asynchronous messaging.</span></span>
- <span data-ttu-id="52428-132">Il front-end e il ruolo di lavoro possono essere ridimensionati in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="52428-132">The front end and the worker can be scaled independently.</span></span>

## <a name="challenges"></a><span data-ttu-id="52428-133">Problematiche</span><span class="sxs-lookup"><span data-stu-id="52428-133">Challenges</span></span>

- <span data-ttu-id="52428-134">Senza un'attenta progettazione, il front-end e il ruolo di lavoro possono diventare componenti monolitici di grandi dimensioni, difficili da gestire e aggiornare.</span><span class="sxs-lookup"><span data-stu-id="52428-134">Without careful design, the front end and the worker can become large, monolithic components that are difficult to maintain and update.</span></span>
- <span data-ttu-id="52428-135">Possono essere presenti dipendenze nascoste, se il front-end e il ruolo di lavoro condividono schemi di dati o moduli di codice.</span><span class="sxs-lookup"><span data-stu-id="52428-135">There may be hidden dependencies, if the front end and worker share data schemas or code modules.</span></span> 

## <a name="best-practices"></a><span data-ttu-id="52428-136">Procedure consigliate</span><span class="sxs-lookup"><span data-stu-id="52428-136">Best practices</span></span>

- <span data-ttu-id="52428-137">Esporre un'API ben progettata al client.</span><span class="sxs-lookup"><span data-stu-id="52428-137">Expose a well-designed API to the client.</span></span> <span data-ttu-id="52428-138">Vedere [API design best practices][api-design] (Procedure consigliate per la progettazione di API).</span><span class="sxs-lookup"><span data-stu-id="52428-138">See [API design best practices][api-design].</span></span>
- <span data-ttu-id="52428-139">Usare la scalabilità automatica per gestire le modifiche nel carico.</span><span class="sxs-lookup"><span data-stu-id="52428-139">Autoscale to handle changes in load.</span></span> <span data-ttu-id="52428-140">Vedere [Autoscaling best practices][autoscaling] (Procedure consigliate per la scalabilità automatica).</span><span class="sxs-lookup"><span data-stu-id="52428-140">See [Autoscaling best practices][autoscaling].</span></span>
- <span data-ttu-id="52428-141">Memorizzare nella cache dati semi-statici.</span><span class="sxs-lookup"><span data-stu-id="52428-141">Cache semi-static data.</span></span> <span data-ttu-id="52428-142">Vedere [Caching best practices][caching] (Procedure consigliate per la memorizzazione nella cache).</span><span class="sxs-lookup"><span data-stu-id="52428-142">See [Caching best practices][caching].</span></span>
- <span data-ttu-id="52428-143">Usare una rete CDN per ospitare il contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="52428-143">Use a CDN to host static content.</span></span> <span data-ttu-id="52428-144">Vedere [CDN best practices][cdn] (Procedure consigliate per la rete CDN).</span><span class="sxs-lookup"><span data-stu-id="52428-144">See [CDN best practices][cdn].</span></span>
- <span data-ttu-id="52428-145">Usare la programmazione poliglotta persistente nei casi appropriati.</span><span class="sxs-lookup"><span data-stu-id="52428-145">Use polyglot persistence when appropriate.</span></span> <span data-ttu-id="52428-146">Vedere [Usare il migliore archivio dati per il processo][polyglot].</span><span class="sxs-lookup"><span data-stu-id="52428-146">See [Use the best data store for the job][polyglot].</span></span>
- <span data-ttu-id="52428-147">Partizionare i dati per migliorare la scalabilità, ridurre i conflitti e ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="52428-147">Partition data to improve scalability, reduce contention, and optimize performance.</span></span> <span data-ttu-id="52428-148">Vedere [Procedure consigliate per il partizionamento dei dati][data-partition].</span><span class="sxs-lookup"><span data-stu-id="52428-148">See [Data partitioning best practices][data-partition].</span></span>


## <a name="web-queue-worker-on-azure-app-service"></a><span data-ttu-id="52428-149">Architettura Web/coda/ruolo di lavoro per Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="52428-149">Web-Queue-Worker on Azure App Service</span></span>

<span data-ttu-id="52428-150">Questa sezione descrive un'architettura Web/coda/ruolo di lavoro consigliata che usa Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="52428-150">This section describes a recommended Web-Queue-Worker architecture that uses Azure App Service.</span></span> 

![](./images/web-queue-worker-physical.png)

<span data-ttu-id="52428-151">Il front-end viene implementato come app Web di Servizio app di Azure, mentre il ruolo di lavoro viene implementato come processo Web.</span><span class="sxs-lookup"><span data-stu-id="52428-151">The front end is implemented as an Azure App Service web app, and the worker is implemented as a WebJob.</span></span> <span data-ttu-id="52428-152">L'app Web e il processo Web sono entrambi associati a un piano di servizio app che fornisce le istanze di macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="52428-152">The web app and the WebJob are both associated with an App Service plan that provides the VM instances.</span></span> 

<span data-ttu-id="52428-153">È possibile usare code del bus di servizio di Azure o di archiviazione di Azure per la coda di messaggi.</span><span class="sxs-lookup"><span data-stu-id="52428-153">You can use either Azure Service Bus or Azure Storage queues for the message queue.</span></span> <span data-ttu-id="52428-154">Il diagramma mostra una coda di archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="52428-154">(The diagram shows an Azure Storage queue.)</span></span>

<span data-ttu-id="52428-155">Cache Redis di Azure archivia lo stato della sessione e altri dati che richiedono accesso a bassa latenza.</span><span class="sxs-lookup"><span data-stu-id="52428-155">Azure Redis Cache stores session state and other data that needs low latency access.</span></span>

<span data-ttu-id="52428-156">La rete CDN di Azure viene usata per memorizzare nella cache contenuto statico come immagini, CSS o HTML.</span><span class="sxs-lookup"><span data-stu-id="52428-156">Azure CDN is used to cache static content such as images, CSS, or HTML.</span></span>

<span data-ttu-id="52428-157">Per l'archiviazione, scegliere le tecnologie più adatte in base alle esigenze dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="52428-157">For storage, choose the storage technologies that best fit the needs of the application.</span></span> <span data-ttu-id="52428-158">È possibile usare più tecnologie di archiviazione (programmazione poliglotta persistente).</span><span class="sxs-lookup"><span data-stu-id="52428-158">You might use multiple storage technologies (polyglot persistence).</span></span> <span data-ttu-id="52428-159">Per illustrare questo concetto, il diagramma mostra il database SQL di Azure e Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="52428-159">To illustrate this idea, the diagram shows Azure SQL Database and Azure Cosmos DB.</span></span>  

<span data-ttu-id="52428-160">Per altre informazioni, vedere [App Service web application reference architecture][scalable-web-app] (Architettura di riferimento per le applicazioni Web di Servizio app).</span><span class="sxs-lookup"><span data-stu-id="52428-160">For more details, see [App Service web application reference architecture][scalable-web-app].</span></span>

### <a name="additional-considerations"></a><span data-ttu-id="52428-161">Considerazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="52428-161">Additional considerations</span></span>

- <span data-ttu-id="52428-162">Non tutte le transazioni devono passare dalla coda e dal ruolo di lavoro per l'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="52428-162">Not every transaction has to go through the queue and worker to storage.</span></span> <span data-ttu-id="52428-163">Il front-end Web può eseguire semplici operazioni di lettura/scrittura direttamente.</span><span class="sxs-lookup"><span data-stu-id="52428-163">The web front end can perform simple read/write operations directly.</span></span> <span data-ttu-id="52428-164">I ruoli di lavoro sono progettati per attività a elevato utilizzo di risorse o flussi di lavoro a esecuzione prolungata.</span><span class="sxs-lookup"><span data-stu-id="52428-164">Workers are designed for resource-intensive tasks or long-running workflows.</span></span> <span data-ttu-id="52428-165">In alcuni casi, un ruolo di lavoro può essere totalmente superfluo.</span><span class="sxs-lookup"><span data-stu-id="52428-165">In some cases, you might not need a worker at all.</span></span>

- <span data-ttu-id="52428-166">Usare la funzionalità di scalabilità automatica predefinita di Servizio app per aumentare il numero di istanze di macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="52428-166">Use the built-in autoscale feature of App Service to scale out the number of VM instances.</span></span> <span data-ttu-id="52428-167">Se il carico sull'applicazione segue modelli prevedibili, usare la scalabilità automatica basata sulla pianificazione.</span><span class="sxs-lookup"><span data-stu-id="52428-167">If the load on the application follows predictable patterns, use schedule-based autoscale.</span></span> <span data-ttu-id="52428-168">Se il carico è imprevedibile, usare regole di scalabilità automatica basate sulle metriche.</span><span class="sxs-lookup"><span data-stu-id="52428-168">If the load is unpredictable, use metrics-based autoscaling rules.</span></span>      

- <span data-ttu-id="52428-169">Provare a includere l'app Web e il processo Web in piani di servizio app separati.</span><span class="sxs-lookup"><span data-stu-id="52428-169">Consider putting the web app and the WebJob into separate App Service plans.</span></span> <span data-ttu-id="52428-170">In questo modo, saranno ospitati in istanze di macchina virtuale separate e potranno essere ridimensionati in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="52428-170">That way, they are hosted on separate VM instances and can be scaled independently.</span></span> 

- <span data-ttu-id="52428-171">Usare piani di servizio app separati per gli ambienti di produzione e test.</span><span class="sxs-lookup"><span data-stu-id="52428-171">Use separate App Service plans for production and testing.</span></span> <span data-ttu-id="52428-172">In caso contrario, se si usa lo stesso piano per gli ambienti di produzione e test, i test verranno eseguiti nelle macchine virtuali di produzione.</span><span class="sxs-lookup"><span data-stu-id="52428-172">Otherwise, if you use the same plan for production and testing, it means your tests are running on your production VMs.</span></span>

- <span data-ttu-id="52428-173">Usare slot di distribuzione per gestire le distribuzioni.</span><span class="sxs-lookup"><span data-stu-id="52428-173">Use deployment slots to manage deployments.</span></span> <span data-ttu-id="52428-174">In questo modo, è possibile distribuire una versione aggiornata in uno slot di staging e quindi passare alla nuova versione.</span><span class="sxs-lookup"><span data-stu-id="52428-174">This lets you to deploy an updated version to a staging slot, then swap over to the new version.</span></span> <span data-ttu-id="52428-175">È anche possibile tornare alla versione precedente, in caso di problemi con l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="52428-175">It also lets you swap back to the previous version, if there was a problem with the update.</span></span>

<!-- links -->

[api-design]: ../../best-practices/api-design.md
[autoscaling]: ../../best-practices/auto-scaling.md
[caching]: ../../best-practices/caching.md
[cdn]: ../../best-practices/cdn.md
[data-partition]: ../../best-practices/data-partitioning.md
[polyglot]: ../design-principles/use-the-best-data-store.md
[scalable-web-app]: ../../reference-architectures/app-service-web-app/scalable-web-app.md