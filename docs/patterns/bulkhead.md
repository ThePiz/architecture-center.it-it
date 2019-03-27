---
title: Modello A scomparti
titleSuffix: Cloud Design Patterns
description: Isolare gli elementi di un'applicazione in pool in modo che un eventuale problema in uno dei componenti non blocchi il funzionamento degli altri componenti.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 4ad221ae5e9bd51c6d304ba33b884f71c5081b16
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244492"
---
# <a name="bulkhead-pattern"></a><span data-ttu-id="483d1-104">Modello A scomparti</span><span class="sxs-lookup"><span data-stu-id="483d1-104">Bulkhead pattern</span></span>

<span data-ttu-id="483d1-105">Isolare gli elementi di un'applicazione in pool in modo che un eventuale problema in uno dei componenti non blocchi il funzionamento degli altri componenti.</span><span class="sxs-lookup"><span data-stu-id="483d1-105">Isolate elements of an application into pools so that if one fails, the others will continue to function.</span></span>

<span data-ttu-id="483d1-106">Questo modello è denominato *A scomparti* perché ricorda le aree divise da porte tagliafuoco nello scafo di una nave.</span><span class="sxs-lookup"><span data-stu-id="483d1-106">This pattern is named *Bulkhead* because it resembles the sectioned partitions of a ship's hull.</span></span> <span data-ttu-id="483d1-107">Se lo scafo subisce un danno, si riempie d'acqua solo la sezione danneggiata, impedendo quindi che l'intera nave affondi.</span><span class="sxs-lookup"><span data-stu-id="483d1-107">If the hull of a ship is compromised, only the damaged section fills with water, which prevents the ship from sinking.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="483d1-108">Contesto e problema</span><span class="sxs-lookup"><span data-stu-id="483d1-108">Context and problem</span></span>

<span data-ttu-id="483d1-109">Un'applicazione basata su cloud può includere più servizi, e ogni servizio può essere usato da uno o più consumer.</span><span class="sxs-lookup"><span data-stu-id="483d1-109">A cloud-based application may include multiple services, with each service having one or more consumers.</span></span> <span data-ttu-id="483d1-110">Un carico eccessivo o un errore in un servizio interesserà tutti i consumer del servizio.</span><span class="sxs-lookup"><span data-stu-id="483d1-110">Excessive load or failure in a service will impact all consumers of the service.</span></span>

<span data-ttu-id="483d1-111">Inoltre, un consumer può inviare richieste a più servizi contemporaneamente, impiegando delle risorse per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="483d1-111">Moreover, a consumer may send requests to multiple services simultaneously, using resources for each request.</span></span> <span data-ttu-id="483d1-112">Quando il consumer invia una richiesta a un servizio che non è configurato correttamente o non risponde, le risorse usate dalla richiesta del client potrebbero non essere liberate in modo tempestivo.</span><span class="sxs-lookup"><span data-stu-id="483d1-112">When the consumer sends a request to a service that is misconfigured or not responding, the resources used by the client's request may not be freed in a timely manner.</span></span> <span data-ttu-id="483d1-113">Se le richieste inviate al servizio continuano, le risorse possono esaurirsi.</span><span class="sxs-lookup"><span data-stu-id="483d1-113">As requests to the service continue, those resources may be exhausted.</span></span> <span data-ttu-id="483d1-114">Può ad esempio esaurirsi il pool di connessione del client,</span><span class="sxs-lookup"><span data-stu-id="483d1-114">For example, the client's connection pool may be exhausted.</span></span> <span data-ttu-id="483d1-115">e questo influisce sulle richieste ad altri servizi da parte del consumer.</span><span class="sxs-lookup"><span data-stu-id="483d1-115">At that point, requests by the consumer to other services are affected.</span></span> <span data-ttu-id="483d1-116">Alla fine, il consumer non sarà più in grado di inviare richieste neanche agli altri servizi, non solo al servizio che per primo non ha risposto.</span><span class="sxs-lookup"><span data-stu-id="483d1-116">Eventually the consumer can no longer send requests to other services, not just the original unresponsive service.</span></span>

<span data-ttu-id="483d1-117">Lo stesso problema di esaurimento delle risorse può interessare servizi con più consumer.</span><span class="sxs-lookup"><span data-stu-id="483d1-117">The same issue of resource exhaustion affects services with multiple consumers.</span></span> <span data-ttu-id="483d1-118">Un numero elevato di richieste provenienti da un client può esaurire le risorse disponibili del servizio.</span><span class="sxs-lookup"><span data-stu-id="483d1-118">A large number of requests originating from one client may exhaust available resources in the service.</span></span> <span data-ttu-id="483d1-119">Altri consumer non saranno più in grado di usare il servizio, innescando errori a catena.</span><span class="sxs-lookup"><span data-stu-id="483d1-119">Other consumers are no longer able to consume the service, causing a cascading failure effect.</span></span>

## <a name="solution"></a><span data-ttu-id="483d1-120">Soluzione</span><span class="sxs-lookup"><span data-stu-id="483d1-120">Solution</span></span>

<span data-ttu-id="483d1-121">Suddividere le istanze del servizio in diversi gruppi, in base ai requisiti di carico e disponibilità dei consumer.</span><span class="sxs-lookup"><span data-stu-id="483d1-121">Partition service instances into different groups, based on consumer load and availability requirements.</span></span> <span data-ttu-id="483d1-122">Questo schema progettuale agevola l'isolamento degli errori e consente di mantenere le funzionalità del servizio per alcuni consumer anche in caso di errore.</span><span class="sxs-lookup"><span data-stu-id="483d1-122">This design helps to isolate failures, and allows you to sustain service functionality for some consumers, even during a failure.</span></span>

<span data-ttu-id="483d1-123">Un consumer può anche partizionare le risorse, per assicurare che le risorse usate per chiamare un servizio non incidano su quelle usate per chiamare un altro servizio.</span><span class="sxs-lookup"><span data-stu-id="483d1-123">A consumer can also partition resources, to ensure that resources used to call one service don't affect the resources used to call another service.</span></span> <span data-ttu-id="483d1-124">Ad esempio, a un consumer che chiama più servizi è possibile assegnare un pool di connessioni per ogni servizio.</span><span class="sxs-lookup"><span data-stu-id="483d1-124">For example, a consumer that calls multiple services may be assigned a connection pool for each service.</span></span> <span data-ttu-id="483d1-125">L'eventuale errore registrato in un servizio incide solo sul pool di connessioni assegnato a quel servizio, e il consumer può continuare a usare altri servizi.</span><span class="sxs-lookup"><span data-stu-id="483d1-125">If a service begins to fail, it only affects the connection pool assigned for that service, allowing the consumer to continue using the other services.</span></span>

<span data-ttu-id="483d1-126">Questo schema mostra i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="483d1-126">The benefits of this pattern include:</span></span>

- <span data-ttu-id="483d1-127">Consente di isolare consumer e servizi evitando errori a catena.</span><span class="sxs-lookup"><span data-stu-id="483d1-127">Isolates consumers and services from cascading failures.</span></span> <span data-ttu-id="483d1-128">Un problema che interessa un consumer o un servizio può restare isolato nel proprio scomparto, evitando che il problema si ripercuota sull'intera soluzione.</span><span class="sxs-lookup"><span data-stu-id="483d1-128">An issue affecting a consumer or service can be isolated within its own bulkhead, preventing the entire solution from failing.</span></span>
- <span data-ttu-id="483d1-129">Consente di mantenere alcune funzionalità in caso di errore di un servizio.</span><span class="sxs-lookup"><span data-stu-id="483d1-129">Allows you to preserve some functionality in the event of a service failure.</span></span> <span data-ttu-id="483d1-130">Altri servizi e funzionalità dell'applicazione continueranno a funzionare.</span><span class="sxs-lookup"><span data-stu-id="483d1-130">Other services and features of the application will continue to work.</span></span>
- <span data-ttu-id="483d1-131">Consente di distribuire servizi che offrono una diversa qualità del servizio per applicazioni più esigenti.</span><span class="sxs-lookup"><span data-stu-id="483d1-131">Allows you to deploy services that offer a different quality of service for consuming applications.</span></span> <span data-ttu-id="483d1-132">È possibile configurare un pool di consumer ad alta priorità che usa servizi ad alta priorità.</span><span class="sxs-lookup"><span data-stu-id="483d1-132">A high-priority consumer pool can be configured to use high-priority services.</span></span>

<span data-ttu-id="483d1-133">Il diagramma seguente mostra scomparti strutturati in pool di connessioni che chiamano singoli servizi.</span><span class="sxs-lookup"><span data-stu-id="483d1-133">The following diagram shows bulkheads structured around connection pools that call individual services.</span></span> <span data-ttu-id="483d1-134">Se nel Servizio A si verifica un errore o un altro problema, il pool di connessioni è isolato, e l'errore o il problema interesserà soli i carichi di lavoro che usano il pool di thread assegnato al Servizio A.</span><span class="sxs-lookup"><span data-stu-id="483d1-134">If Service A fails or causes some other issue, the connection pool is isolated, so only workloads using the thread pool assigned to Service A are affected.</span></span> <span data-ttu-id="483d1-135">I carichi di lavoro che usano i Servizi B e C non sono interessati e possono continuare a lavorare senza interruzioni.</span><span class="sxs-lookup"><span data-stu-id="483d1-135">Workloads that use Service B and C are not affected and can continue working without interruption.</span></span>

![Primo diagramma del modello A scomparti](./_images/bulkhead-1.png)

<span data-ttu-id="483d1-137">Il diagramma seguente mostra più client che chiamano un singolo servizio.</span><span class="sxs-lookup"><span data-stu-id="483d1-137">The next diagram shows multiple clients calling a single service.</span></span> <span data-ttu-id="483d1-138">Ad ogni client viene assegnata un'istanza del servizio distinta.</span><span class="sxs-lookup"><span data-stu-id="483d1-138">Each client is assigned a separate service instance.</span></span> <span data-ttu-id="483d1-139">Il Client 1 ha inviato un numero eccessivo di richieste, con conseguente sovraccarico sulla relativa istanza.</span><span class="sxs-lookup"><span data-stu-id="483d1-139">Client 1 has made too many requests and overwhelmed its instance.</span></span> <span data-ttu-id="483d1-140">Poiché ogni istanza del servizio è isolata dalle altre, gli altri client possono continuare a effettuare chiamate.</span><span class="sxs-lookup"><span data-stu-id="483d1-140">Because each service instance is isolated from the others, the other clients can continue making calls.</span></span>

![Primo diagramma del modello A scomparti](./_images/bulkhead-2.png)

## <a name="issues-and-considerations"></a><span data-ttu-id="483d1-142">Considerazioni e problemi</span><span class="sxs-lookup"><span data-stu-id="483d1-142">Issues and considerations</span></span>

- <span data-ttu-id="483d1-143">Definire le partizioni in funzione dei requisiti aziendali e tecnici dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="483d1-143">Define partitions around the business and technical requirements of the application.</span></span>
- <span data-ttu-id="483d1-144">Quando si suddividono in scomparti i servizi o i consumer, è consigliabile considerare il livello di isolamento offerto dalla tecnologia in uso, nonché il sovraccarico in termini di costo, prestazioni e gestibilità.</span><span class="sxs-lookup"><span data-stu-id="483d1-144">When partitioning services or consumers into bulkheads, consider the level of isolation offered by the technology as well as the overhead in terms of cost, performance and manageability.</span></span>
- <span data-ttu-id="483d1-145">Considerare la combinazione del modello A scomparti con i modelli Nuovo tentativo, Interruttore e Limitazione per offrire una gestione degli errori più complessa.</span><span class="sxs-lookup"><span data-stu-id="483d1-145">Consider combining bulkheads with retry, circuit breaker, and throttling patterns to provide more sophisticated fault handling.</span></span>
- <span data-ttu-id="483d1-146">Quando si suddividono i consumer in scomparti, valutare l'uso di processi, pool di thread e semafori.</span><span class="sxs-lookup"><span data-stu-id="483d1-146">When partitioning consumers into bulkheads, consider using processes, thread pools, and semaphores.</span></span> <span data-ttu-id="483d1-147">Progetti come [Netflix Hystrix] [ hystrix] e [Polly] [ polly] offrono un framework per la creazione di scomparti per consumer.</span><span class="sxs-lookup"><span data-stu-id="483d1-147">Projects like [Netflix Hystrix][hystrix] and [Polly][polly] offer a framework for creating consumer bulkheads.</span></span>
- <span data-ttu-id="483d1-148">Quando si suddividono i servizi in scomparti, valutarne la distribuzione in macchine virtuali, contenitori o processi distinti.</span><span class="sxs-lookup"><span data-stu-id="483d1-148">When partitioning services into bulkheads, consider deploying them into separate virtual machines, containers, or processes.</span></span> <span data-ttu-id="483d1-149">I contenitori offrono un buon bilanciamento dell'isolamento delle risorse con un sovraccarico non significativo.</span><span class="sxs-lookup"><span data-stu-id="483d1-149">Containers offer a good balance of resource isolation with fairly low overhead.</span></span>
- <span data-ttu-id="483d1-150">I servizi che comunicano tramite messaggi asincroni possono essere isolati usando diversi set di code.</span><span class="sxs-lookup"><span data-stu-id="483d1-150">Services that communicate using asynchronous messages can be isolated through different sets of queues.</span></span> <span data-ttu-id="483d1-151">Ogni coda può disporre di un set dedicato di istanze che elabora i messaggi nella coda o di un singolo gruppo di istanze che usa un algoritmo per rimuovere la coda ed elaborare gli invii.</span><span class="sxs-lookup"><span data-stu-id="483d1-151">Each queue can have a dedicated set of instances processing messages on the queue, or a single group of instances using an algorithm to dequeue and dispatch processing.</span></span>
- <span data-ttu-id="483d1-152">Determinare il livello di granularità degli scomparti.</span><span class="sxs-lookup"><span data-stu-id="483d1-152">Determine the level of granularity for the bulkheads.</span></span> <span data-ttu-id="483d1-153">Se, ad esempio, si distribuiscono i tenant tra partizioni, è possibile posizionare ogni tenant in una partizione distinta o posizionare diversi tenant in una sola partizione.</span><span class="sxs-lookup"><span data-stu-id="483d1-153">For example, if you want to distribute tenants across partitions, you could place each tenant into a separate partition, or put several tenants into one partition.</span></span>
- <span data-ttu-id="483d1-154">Monitorare le prestazioni e il contratto di servizio di ogni partizione.</span><span class="sxs-lookup"><span data-stu-id="483d1-154">Monitor each partition’s performance and SLA.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="483d1-155">Quando usare questo modello</span><span class="sxs-lookup"><span data-stu-id="483d1-155">When to use this pattern</span></span>

<span data-ttu-id="483d1-156">Usare questo modello per:</span><span class="sxs-lookup"><span data-stu-id="483d1-156">Use this pattern to:</span></span>

- <span data-ttu-id="483d1-157">Isolare le risorse necessarie per l'utilizzo di un set di servizi back-end, soprattutto se l'applicazione può offrire un certo livello di funzionalità anche quando uno dei servizi non risponde.</span><span class="sxs-lookup"><span data-stu-id="483d1-157">Isolate resources used to consume a set of backend services, especially if the application can provide some level of functionality even when one of the services is not responding.</span></span>
- <span data-ttu-id="483d1-158">Isolare i consumer critici da quelli standard.</span><span class="sxs-lookup"><span data-stu-id="483d1-158">Isolate critical consumers from standard consumers.</span></span>
- <span data-ttu-id="483d1-159">Proteggere l'applicazione dagli errori a catena.</span><span class="sxs-lookup"><span data-stu-id="483d1-159">Protect the application from cascading failures.</span></span>

<span data-ttu-id="483d1-160">Questo modello potrebbe non essere adatto nelle situazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="483d1-160">This pattern may not be suitable when:</span></span>

- <span data-ttu-id="483d1-161">Un uso meno efficiente delle risorse non è accettabile nel progetto.</span><span class="sxs-lookup"><span data-stu-id="483d1-161">Less efficient use of resources may not be acceptable in the project.</span></span>
- <span data-ttu-id="483d1-162">La complessità aggiuntiva non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="483d1-162">The added complexity is not necessary</span></span>

## <a name="example"></a><span data-ttu-id="483d1-163">Esempio</span><span class="sxs-lookup"><span data-stu-id="483d1-163">Example</span></span>

<span data-ttu-id="483d1-164">Il file di configurazione Kubernetes seguente crea un contenitore isolato per l'esecuzione di un singolo servizio, con limiti e risorse di CPU e memoria propri.</span><span class="sxs-lookup"><span data-stu-id="483d1-164">The following Kubernetes configuration file creates an isolated container to run a single service, with its own CPU and memory resources and limits.</span></span>

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

## <a name="related-guidance"></a><span data-ttu-id="483d1-165">Informazioni correlate</span><span class="sxs-lookup"><span data-stu-id="483d1-165">Related guidance</span></span>

- [<span data-ttu-id="483d1-166">Progettazione di applicazioni resilienti per Azure</span><span class="sxs-lookup"><span data-stu-id="483d1-166">Designing resilient applications for Azure</span></span>](../resiliency/index.md)
- [<span data-ttu-id="483d1-167">Modello Interruttore</span><span class="sxs-lookup"><span data-stu-id="483d1-167">Circuit Breaker pattern</span></span>](./circuit-breaker.md)
- [<span data-ttu-id="483d1-168">Modello Nuovo tentativo</span><span class="sxs-lookup"><span data-stu-id="483d1-168">Retry pattern</span></span>](./retry.md)
- [<span data-ttu-id="483d1-169">Modello Limitazione</span><span class="sxs-lookup"><span data-stu-id="483d1-169">Throttling pattern</span></span>](./throttling.md)

<!-- links -->

[hystrix]: https://github.com/Netflix/Hystrix
[polly]: https://github.com/App-vNext/Polly
