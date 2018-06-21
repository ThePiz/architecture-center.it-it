---
title: Modello A scomparti
description: Isolare gli elementi di un'applicazione in pool in modo che un eventuale problema in uno dei componenti non blocchi il funzionamento degli altri componenti
author: dragon119
ms.date: 06/23/2017
ms.openlocfilehash: 9917870e1dcbed87aaa41e051f1622ad4950456a
ms.sourcegitcommit: f665226cec96ec818ca06ac6c2d83edb23c9f29c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31012706"
---
# <a name="bulkhead-pattern"></a><span data-ttu-id="7bbd9-103">Modello A scomparti</span><span class="sxs-lookup"><span data-stu-id="7bbd9-103">Bulkhead pattern</span></span>

<span data-ttu-id="7bbd9-104">Isolare gli elementi di un'applicazione in pool in modo che un eventuale problema in uno dei componenti non blocchi il funzionamento degli altri componenti.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-104">Isolate elements of an application into pools so that if one fails, the others will continue to function.</span></span>

<span data-ttu-id="7bbd9-105">Questo modello è denominato *A scomparti* perché ricorda le aree divise da porte tagliafuoco nello scafo di una nave.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-105">This pattern is named *Bulkhead* because it resembles the sectioned partitions of a ship's hull.</span></span> <span data-ttu-id="7bbd9-106">Se lo scafo subisce un danno, si riempie d'acqua solo la sezione danneggiata, impedendo quindi che l'intera nave affondi.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-106">If the hull of a ship is compromised, only the damaged section fills with water, which prevents the ship from sinking.</span></span> 

## <a name="context-and-problem"></a><span data-ttu-id="7bbd9-107">Contesto e problema</span><span class="sxs-lookup"><span data-stu-id="7bbd9-107">Context and problem</span></span>

<span data-ttu-id="7bbd9-108">Un'applicazione basata su cloud può includere più servizi, e ogni servizio può essere usato da uno o più consumer.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-108">A cloud-based application may include multiple services, with each service having one or more consumers.</span></span> <span data-ttu-id="7bbd9-109">Un carico eccessivo o un errore in un servizio interesserà tutti i consumer del servizio.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-109">Excessive load or failure in a service will impact all consumers of the service.</span></span>

<span data-ttu-id="7bbd9-110">Inoltre, un consumer può inviare richieste a più servizi contemporaneamente, impiegando delle risorse per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-110">Moreover, a consumer may send requests to multiple services simultaneously, using resources for each request.</span></span> <span data-ttu-id="7bbd9-111">Quando il consumer invia una richiesta a un servizio che non è configurato correttamente o non risponde, le risorse usate dalla richiesta del client potrebbero non essere liberate in modo tempestivo.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-111">When the consumer sends a request to a service that is misconfigured or not responding, the resources used by the client's request may not be freed in a timely manner.</span></span> <span data-ttu-id="7bbd9-112">Se le richieste inviate al servizio continuano, le risorse possono esaurirsi.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-112">As requests to the service continue, those resources may be exhausted.</span></span> <span data-ttu-id="7bbd9-113">Può ad esempio esaurirsi il pool di connessione del client,</span><span class="sxs-lookup"><span data-stu-id="7bbd9-113">For example, the client's connection pool may be exhausted.</span></span> <span data-ttu-id="7bbd9-114">con un conseguente impatto sulle richieste di altri servizi da parte del consumer.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-114">At that point, requests by the consumer to other services are impacted.</span></span> <span data-ttu-id="7bbd9-115">Alla fine, il consumer non sarà più in grado di inviare richieste neanche agli altri servizi, non solo al servizio che per primo non ha risposto.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-115">Eventually the consumer can no longer send requests to other services, not just the original unresponsive service.</span></span>

<span data-ttu-id="7bbd9-116">Lo stesso problema di esaurimento delle risorse può interessare servizi con più consumer.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-116">The same issue of resource exhaustion affects services with multiple consumers.</span></span> <span data-ttu-id="7bbd9-117">Un numero elevato di richieste provenienti da un client può esaurire le risorse disponibili del servizio.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-117">A large number of requests originating from one client may exhaust available resources in the service.</span></span> <span data-ttu-id="7bbd9-118">Altri consumer non saranno più in grado di usare il servizio, innescando errori a catena.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-118">Other consumers are no longer able to consume the service, causing a cascading failure effect.</span></span>

## <a name="solution"></a><span data-ttu-id="7bbd9-119">Soluzione</span><span class="sxs-lookup"><span data-stu-id="7bbd9-119">Solution</span></span>

<span data-ttu-id="7bbd9-120">Suddividere le istanze del servizio in diversi gruppi, in base ai requisiti di carico e disponibilità dei consumer.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-120">Partition service instances into different groups, based on consumer load and availability requirements.</span></span> <span data-ttu-id="7bbd9-121">Questo schema progettuale agevola l'isolamento degli errori e consente di mantenere le funzionalità del servizio per alcuni consumer anche in caso di errore.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-121">This design helps to isolate failures, and allows you to sustain service functionality for some consumers, even during a failure.</span></span>

<span data-ttu-id="7bbd9-122">Un consumer può anche partizionare le risorse, per assicurare che le risorse usate per chiamare un servizio non incidano su quelle usate per chiamare un altro servizio.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-122">A consumer can also partition resources, to ensure that resources used to call one service don't affect the resources used to call another service.</span></span> <span data-ttu-id="7bbd9-123">Ad esempio, a un consumer che chiama più servizi è possibile assegnare un pool di connessioni per ogni servizio.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-123">For example, a consumer that calls multiple services may be assigned a connection pool for each service.</span></span> <span data-ttu-id="7bbd9-124">L'eventuale errore registrato in un servizio incide solo sul pool di connessioni assegnato a quel servizio, e il consumer può continuare a usare altri servizi.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-124">If a service begins to fail, it only affects the connection pool assigned for that service, allowing the consumer to continue using the other services.</span></span>

<span data-ttu-id="7bbd9-125">Questo schema mostra i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7bbd9-125">The benefits of this pattern include:</span></span>

- <span data-ttu-id="7bbd9-126">Consente di isolare consumer e servizi evitando errori a catena.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-126">Isolates consumers and services from cascading failures.</span></span> <span data-ttu-id="7bbd9-127">Un problema che interessa un consumer o un servizio può restare isolato nel proprio scomparto, evitando che il problema si ripercuota sull'intera soluzione.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-127">An issue affecting a consumer or service can be isolated within its own bulkhead, preventing the entire solution from failing.</span></span>
- <span data-ttu-id="7bbd9-128">Consente di mantenere alcune funzionalità in caso di errore di un servizio.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-128">Allows you to preserve some functionality in the event of a service failure.</span></span> <span data-ttu-id="7bbd9-129">Altri servizi e funzionalità dell'applicazione continueranno a funzionare.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-129">Other services and features of the application will continue to work.</span></span>
- <span data-ttu-id="7bbd9-130">Consente di distribuire servizi che offrono una diversa qualità del servizio per applicazioni più esigenti.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-130">Allows you to deploy services that offer a different quality of service for consuming applications.</span></span> <span data-ttu-id="7bbd9-131">È possibile configurare un pool di consumer ad alta priorità che usa servizi ad alta priorità.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-131">A high-priority consumer pool can be configured to use high-priority services.</span></span> 

<span data-ttu-id="7bbd9-132">Il diagramma seguente mostra scomparti strutturati in pool di connessioni che chiamano singoli servizi.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-132">The following diagram shows bulkheads structured around connection pools that call individual services.</span></span> <span data-ttu-id="7bbd9-133">Se nel Servizio A si verifica un errore o un altro problema, il pool di connessioni è isolato, e l'errore o il problema interesserà soli i carichi di lavoro che usano il pool di thread assegnato al Servizio A.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-133">If Service A fails or causes some other issue, the connection pool is isolated, so only workloads using the thread pool assigned to Service A are affected.</span></span> <span data-ttu-id="7bbd9-134">I carichi di lavoro che usano i Servizi B e C non sono interessati e possono continuare a lavorare senza interruzioni.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-134">Workloads that use Service B and C are not affected and can continue working without interruption.</span></span>

![](./_images/bulkhead-1.png) 

<span data-ttu-id="7bbd9-135">Il diagramma seguente mostra più client che chiamano un singolo servizio.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-135">The next diagram shows multiple clients calling a single service.</span></span> <span data-ttu-id="7bbd9-136">Ad ogni client viene assegnata un'istanza del servizio distinta.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-136">Each client is assigned a separate service instance.</span></span> <span data-ttu-id="7bbd9-137">Il Client 1 ha inviato un numero eccessivo di richieste, con conseguente sovraccarico sulla relativa istanza.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-137">Client 1 has made too many requests and overwhelmed its instance.</span></span> <span data-ttu-id="7bbd9-138">Poiché ogni istanza del servizio è isolata dalle altre, gli altri client possono continuare a effettuare chiamate.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-138">Because each service instance is isolated from the others, the other clients can continue making calls.</span></span>

![](./_images/bulkhead-2.png)
     
## <a name="issues-and-considerations"></a><span data-ttu-id="7bbd9-139">Considerazioni e problemi</span><span class="sxs-lookup"><span data-stu-id="7bbd9-139">Issues and considerations</span></span>

- <span data-ttu-id="7bbd9-140">Definire le partizioni in funzione dei requisiti aziendali e tecnici dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-140">Define partitions around the business and technical requirements of the application.</span></span>
- <span data-ttu-id="7bbd9-141">Quando si suddividono in scomparti i servizi o i consumer, è consigliabile considerare il livello di isolamento offerto dalla tecnologia in uso, nonché il sovraccarico in termini di costo, prestazioni e gestibilità.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-141">When partitioning services or consumers into bulkheads, consider the level of isolation offered by the technology as well as the overhead in terms of cost, performance and manageability.</span></span>
- <span data-ttu-id="7bbd9-142">Considerare la combinazione del modello A scomparti con i modelli Nuovo tentativo, Interruttore e Limitazione per offrire una gestione degli errori più complessa.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-142">Consider combining bulkheads with retry, circuit breaker, and throttling patterns to provide more sophisticated fault handling.</span></span>
- <span data-ttu-id="7bbd9-143">Quando si suddividono i consumer in scomparti, valutare l'uso di processi, pool di thread e semafori.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-143">When partitioning consumers into bulkheads, consider using processes, thread pools, and semaphores.</span></span> <span data-ttu-id="7bbd9-144">Progetti come [Netflix Hystrix] [ hystrix] e [Polly] [ polly] offrono un framework per la creazione di scomparti per consumer.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-144">Projects like [Netflix Hystrix][hystrix] and [Polly][polly] offer a framework for creating consumer bulkheads.</span></span>
- <span data-ttu-id="7bbd9-145">Quando si suddividono i servizi in scomparti, valutarne la distribuzione in macchine virtuali, contenitori o processi distinti.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-145">When partitioning services into bulkheads, consider deploying them into separate virtual machines, containers, or processes.</span></span> <span data-ttu-id="7bbd9-146">I contenitori offrono un buon bilanciamento dell'isolamento delle risorse con un sovraccarico non significativo.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-146">Containers offer a good balance of resource isolation with fairly low overhead.</span></span>
- <span data-ttu-id="7bbd9-147">I servizi che comunicano tramite messaggi asincroni possono essere isolati usando diversi set di code.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-147">Services that communicate using asynchronous messages can be isolated through different sets of queues.</span></span> <span data-ttu-id="7bbd9-148">Ogni coda può disporre di un set dedicato di istanze che elabora i messaggi nella coda o di un singolo gruppo di istanze che usa un algoritmo per rimuovere la coda ed elaborare gli invii.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-148">Each queue can have a dedicated set of instances processing messages on the queue, or a single group of instances using an algorithm to dequeue and dispatch processing.</span></span>
- <span data-ttu-id="7bbd9-149">Determinare il livello di granularità degli scomparti.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-149">Determine the level of granularity for the bulkheads.</span></span> <span data-ttu-id="7bbd9-150">Se, ad esempio, si distribuiscono i tenant tra partizioni, è possibile posizionare ogni tenant in una partizione distinta o posizionare diversi tenant in una sola partizione.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-150">For example, if you want to distribute tenants across partitions, you could place each tenant into a separate partition, or put several tenants into one partition.</span></span>
- <span data-ttu-id="7bbd9-151">Monitorare le prestazioni e il contratto di servizio di ogni partizione.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-151">Monitor each partition’s performance and SLA.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="7bbd9-152">Quando usare questo modello</span><span class="sxs-lookup"><span data-stu-id="7bbd9-152">When to use this pattern</span></span>

<span data-ttu-id="7bbd9-153">Usare questo modello per:</span><span class="sxs-lookup"><span data-stu-id="7bbd9-153">Use this pattern to:</span></span>

- <span data-ttu-id="7bbd9-154">Isolare le risorse necessarie per l'utilizzo di un set di servizi back-end, soprattutto se l'applicazione può offrire un certo livello di funzionalità anche quando uno dei servizi non risponde.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-154">Isolate resources used to consume a set of backend services, especially if the application can provide some level of functionality even when one of the services is not responding.</span></span>
- <span data-ttu-id="7bbd9-155">Isolare i consumer critici da quelli standard.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-155">Isolate critical consumers from standard consumers.</span></span>
- <span data-ttu-id="7bbd9-156">Proteggere l'applicazione dagli errori a catena.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-156">Protect the application from cascading failures.</span></span>

<span data-ttu-id="7bbd9-157">Questo modello potrebbe non essere adatto nelle situazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="7bbd9-157">This pattern may not be suitable when:</span></span>

- <span data-ttu-id="7bbd9-158">Un uso meno efficiente delle risorse non è accettabile nel progetto.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-158">Less efficient use of resources may not be acceptable in the project.</span></span>
- <span data-ttu-id="7bbd9-159">La complessità aggiuntiva non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-159">The added complexity is not necessary</span></span>

## <a name="example"></a><span data-ttu-id="7bbd9-160">Esempio</span><span class="sxs-lookup"><span data-stu-id="7bbd9-160">Example</span></span>

<span data-ttu-id="7bbd9-161">Il file di configurazione Kubernetes seguente crea un contenitore isolato per l'esecuzione di un singolo servizio, con limiti e risorse di CPU e memoria propri.</span><span class="sxs-lookup"><span data-stu-id="7bbd9-161">The following Kubernetes configuration file creates an isolated container to run a single service, with its own CPU and memory resources and limits.</span></span>

```yml
apiVersion: v1
kind: Pod
metadata:
  name: drone-management
spec:
  containers:
  - name: drone-management-container
    image: drone-service
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "1"
```

## <a name="related-guidance"></a><span data-ttu-id="7bbd9-162">Informazioni correlate</span><span class="sxs-lookup"><span data-stu-id="7bbd9-162">Related guidance</span></span>

- [<span data-ttu-id="7bbd9-163">Modello Interruttore</span><span class="sxs-lookup"><span data-stu-id="7bbd9-163">Circuit Breaker pattern</span></span>](./circuit-breaker.md)
- [<span data-ttu-id="7bbd9-164">Progettazione di applicazioni resilienti per Azure</span><span class="sxs-lookup"><span data-stu-id="7bbd9-164">Designing resilient applications for Azure</span></span>](../resiliency/index.md)
- [<span data-ttu-id="7bbd9-165">Modello Nuovo tentativo</span><span class="sxs-lookup"><span data-stu-id="7bbd9-165">Retry pattern</span></span>](./retry.md)
- [<span data-ttu-id="7bbd9-166">Modello Limitazione</span><span class="sxs-lookup"><span data-stu-id="7bbd9-166">Throttling pattern</span></span>](./throttling.md)


<!-- links -->

[hystrix]: https://github.com/Netflix/Hystrix
[polly]: https://github.com/App-vNext/Polly