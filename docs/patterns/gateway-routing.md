---
title: Modello di routing gateway
titleSuffix: Cloud Design Patterns
description: Eseguire il routing delle richieste a più servizi, usando un singolo endpoint.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: e5c93c98a562e790d547d08fdf312c973cfceed8
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487229"
---
# <a name="gateway-routing-pattern"></a><span data-ttu-id="3631e-104">Modello di routing gateway</span><span class="sxs-lookup"><span data-stu-id="3631e-104">Gateway Routing pattern</span></span>

<span data-ttu-id="3631e-105">Eseguire il routing delle richieste a più servizi, usando un singolo endpoint.</span><span class="sxs-lookup"><span data-stu-id="3631e-105">Route requests to multiple services using a single endpoint.</span></span> <span data-ttu-id="3631e-106">Questo modello è utile quando si vuole esporre più servizi in un singolo endpoint ed eseguire il routing al servizio appropriato in base alla richiesta.</span><span class="sxs-lookup"><span data-stu-id="3631e-106">This pattern is useful when you wish to expose multiple services on a single endpoint and route to the appropriate service based on the request.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="3631e-107">Contesto e problema</span><span class="sxs-lookup"><span data-stu-id="3631e-107">Context and problem</span></span>

<span data-ttu-id="3631e-108">Quando un client deve utilizzare più servizi, uno scenario in cui viene impostato un endpoint separato per ogni servizio e il client gestisce ogni endpoint può essere complesso.</span><span class="sxs-lookup"><span data-stu-id="3631e-108">When a client needs to consume multiple services, setting up a separate endpoint for each service and having the client manage each endpoint can be challenging.</span></span> <span data-ttu-id="3631e-109">Un'applicazione di e-commerce può ad esempio fornire servizi, come la ricerca, le recensioni, il carrello, il completamento delle transazioni e la cronologia degli ordini.</span><span class="sxs-lookup"><span data-stu-id="3631e-109">For example, an e-commerce application might provide services such as search, reviews, cart, checkout, and order history.</span></span> <span data-ttu-id="3631e-110">Ogni servizio ha un'API diversa con cui il client deve interagire e il client deve conoscere ogni endpoint per connettersi ai servizi.</span><span class="sxs-lookup"><span data-stu-id="3631e-110">Each service has a different API that the client must interact with, and the client must know about each endpoint in order to connect to the services.</span></span> <span data-ttu-id="3631e-111">Se un'API viene modificata, è necessario aggiornare anche il client.</span><span class="sxs-lookup"><span data-stu-id="3631e-111">If an API changes, the client must be updated as well.</span></span> <span data-ttu-id="3631e-112">Se si effettua il refactoring di un servizio in due o più servizi separati, è necessario modificare il codice sia nel servizio che nel client.</span><span class="sxs-lookup"><span data-stu-id="3631e-112">If you refactor a service into two or more separate services, the code must change in both the service and the client.</span></span>

## <a name="solution"></a><span data-ttu-id="3631e-113">Soluzione</span><span class="sxs-lookup"><span data-stu-id="3631e-113">Solution</span></span>

<span data-ttu-id="3631e-114">Inserire un gateway davanti a un set di applicazioni, servizi o distribuzioni.</span><span class="sxs-lookup"><span data-stu-id="3631e-114">Place a gateway in front of a set of applications, services, or deployments.</span></span> <span data-ttu-id="3631e-115">Usare il routing delle applicazioni di livello 7 per instradare la richiesta alle istanze appropriate.</span><span class="sxs-lookup"><span data-stu-id="3631e-115">Use application Layer 7 routing to route the request to the appropriate instances.</span></span>

<span data-ttu-id="3631e-116">Con questo modello, l'applicazione client deve conoscere e comunicare solo con un singolo endpoint.</span><span class="sxs-lookup"><span data-stu-id="3631e-116">With this pattern, the client application only needs to know about and communicate with a single endpoint.</span></span> <span data-ttu-id="3631e-117">Se un servizio viene consolidato o scomposto, il client non deve necessariamente essere aggiornato.</span><span class="sxs-lookup"><span data-stu-id="3631e-117">If a service is consolidated or decomposed, the client does not necessarily require updating.</span></span> <span data-ttu-id="3631e-118">Può continuare a inviare le richieste al gateway e solo il routing cambia.</span><span class="sxs-lookup"><span data-stu-id="3631e-118">It can continue making requests to the gateway, and only the routing changes.</span></span>

<span data-ttu-id="3631e-119">Un gateway consente anche di astrarre i servizi back-end dai client, permettendo di mantenere semplici le chiamate client e consentendo nel contempo le modifiche ai servizi back-end dietro il gateway.</span><span class="sxs-lookup"><span data-stu-id="3631e-119">A gateway also lets you abstract backend services from the clients, allowing you to keep client calls simple while enabling changes in the backend services behind the gateway.</span></span> <span data-ttu-id="3631e-120">Le chiamate client possono essere instradate al servizio, o ai servizi, che devono gestire il comportamento previsto del client, in modo che sia possibile aggiungere, dividere e riorganizzare i servizi dietro il gateway senza modificare il client.</span><span class="sxs-lookup"><span data-stu-id="3631e-120">Client calls can be routed to whatever service or services need to handle the expected client behavior, allowing you to add, split, and reorganize services behind the gateway without changing the client.</span></span>

![Diagramma del modello di routing gateway](./_images/gateway-routing.png)

<span data-ttu-id="3631e-122">Questo modello è utile anche per la distribuzione, perché consente di gestire la modalità di implementazione degli aggiornamenti per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="3631e-122">This pattern can also help with deployment, by allowing you to manage how updates are rolled out to users.</span></span> <span data-ttu-id="3631e-123">Quando viene distribuita una nuova versione del servizio, la distribuzione può avvenire in parallelo con la versione esistente.</span><span class="sxs-lookup"><span data-stu-id="3631e-123">When a new version of your service is deployed, it can be deployed in parallel with the existing version.</span></span> <span data-ttu-id="3631e-124">Il routing consente di controllare la versione del servizio presentata ai client, offrendo la flessibilità di scegliere tra diverse strategie di rilascio, ovvero implementazioni incrementali, parallele o complete degli aggiornamenti.</span><span class="sxs-lookup"><span data-stu-id="3631e-124">Routing lets you control what version of the service is presented to the clients, giving you the flexibility to use various release strategies, whether incremental, parallel, or complete rollouts of updates.</span></span> <span data-ttu-id="3631e-125">È possibile correggere rapidamente eventuali problemi rilevati dopo la distribuzione del nuovo servizio modificando la configurazione nel gateway, senza influire sui client.</span><span class="sxs-lookup"><span data-stu-id="3631e-125">Any issues discovered after the new service is deployed can be quickly reverted by making a configuration change at the gateway, without affecting clients.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="3631e-126">Considerazioni e problemi</span><span class="sxs-lookup"><span data-stu-id="3631e-126">Issues and considerations</span></span>

- <span data-ttu-id="3631e-127">Il servizio gateway può introdurre un punto di errore singolo.</span><span class="sxs-lookup"><span data-stu-id="3631e-127">The gateway service may introduce a single point of failure.</span></span> <span data-ttu-id="3631e-128">Assicurarsi che sia progettato correttamente per soddisfare i requisiti di disponibilità.</span><span class="sxs-lookup"><span data-stu-id="3631e-128">Ensure it is properly designed to meet your availability requirements.</span></span> <span data-ttu-id="3631e-129">Durante l'implementazione, considerare le capacità di resilienza e tolleranza di errore.</span><span class="sxs-lookup"><span data-stu-id="3631e-129">Consider resiliency and fault tolerance capabilities when implementing.</span></span>
- <span data-ttu-id="3631e-130">Il servizio gateway può introdurre un collo di bottiglia.</span><span class="sxs-lookup"><span data-stu-id="3631e-130">The gateway service may introduce a bottleneck.</span></span> <span data-ttu-id="3631e-131">Verificare che il gateway offra prestazioni adeguate per gestire il carico e consenta facile scalabilità in base alle proprie aspettative di crescita.</span><span class="sxs-lookup"><span data-stu-id="3631e-131">Ensure the gateway has adequate performance to handle load and can easily scale in line with your growth expectations.</span></span>
- <span data-ttu-id="3631e-132">Eseguire test di carico sul gateway per assicurarsi di non introdurre errori a catena per i servizi.</span><span class="sxs-lookup"><span data-stu-id="3631e-132">Perform load testing against the gateway to ensure you don't introduce cascading failures for services.</span></span>
- <span data-ttu-id="3631e-133">Il routing gateway è di livello 7.</span><span class="sxs-lookup"><span data-stu-id="3631e-133">Gateway routing is level 7.</span></span> <span data-ttu-id="3631e-134">Può essere basato su IP, porta, intestazione o URL.</span><span class="sxs-lookup"><span data-stu-id="3631e-134">It can be based on IP, port, header, or URL.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="3631e-135">Quando usare questo modello</span><span class="sxs-lookup"><span data-stu-id="3631e-135">When to use this pattern</span></span>

<span data-ttu-id="3631e-136">Usare questo modello quando:</span><span class="sxs-lookup"><span data-stu-id="3631e-136">Use this pattern when:</span></span>

- <span data-ttu-id="3631e-137">Un client deve utilizzare più servizi accessibili dietro un gateway.</span><span class="sxs-lookup"><span data-stu-id="3631e-137">A client needs to consume multiple services that can be accessed behind a gateway.</span></span>
- <span data-ttu-id="3631e-138">Si vuole semplificare le applicazioni client usando un singolo endpoint.</span><span class="sxs-lookup"><span data-stu-id="3631e-138">You wish to simplify client applications by using a single endpoint.</span></span>
- <span data-ttu-id="3631e-139">È necessario eseguire il routing delle richieste da endpoint indirizzabili esternamente a endpoint virtuali interni, ad esempio esponendo le porte in una macchina virtuale agli indirizzi IP virtuali del cluster.</span><span class="sxs-lookup"><span data-stu-id="3631e-139">You need to route requests from externally addressable endpoints to internal virtual endpoints, such as exposing ports on a VM to cluster virtual IP addresses.</span></span>

<span data-ttu-id="3631e-140">Questo modello potrebbe non essere appropriato quando si ha un'applicazione semplice che usa solo uno o due servizi.</span><span class="sxs-lookup"><span data-stu-id="3631e-140">This pattern may not be suitable when you have a simple application that uses only one or two services.</span></span>

## <a name="example"></a><span data-ttu-id="3631e-141">Esempio</span><span class="sxs-lookup"><span data-stu-id="3631e-141">Example</span></span>

<span data-ttu-id="3631e-142">Usando Nginx come router, di seguito viene fornito un semplice file di configurazione di esempio per un server che esegue il routing delle richieste per le applicazioni che si trovano in directory virtuali diverse nei vari computer nel back-end.</span><span class="sxs-lookup"><span data-stu-id="3631e-142">Using Nginx as the router, the following is a simple example configuration file for a server that routes requests for applications residing on different virtual directories to different machines at the back end.</span></span>

```console
server {
    listen 80;
    server_name domain.com;

    location /app1 {
        proxy_pass http://10.0.3.10:80;
    }

    location /app2 {
        proxy_pass http://10.0.3.20:80;
    }

    location /app3 {
        proxy_pass http://10.0.3.30:80;
    }
}
```

## <a name="related-guidance"></a><span data-ttu-id="3631e-143">Informazioni correlate</span><span class="sxs-lookup"><span data-stu-id="3631e-143">Related guidance</span></span>

- [<span data-ttu-id="3631e-144">Modello back-end per front-end</span><span class="sxs-lookup"><span data-stu-id="3631e-144">Backends for Frontends pattern</span></span>](./backends-for-frontends.md)
- [<span data-ttu-id="3631e-145">Modello di aggregazione gateway</span><span class="sxs-lookup"><span data-stu-id="3631e-145">Gateway Aggregation pattern</span></span>](./gateway-aggregation.md)
- [<span data-ttu-id="3631e-146">Modello di offload gateway</span><span class="sxs-lookup"><span data-stu-id="3631e-146">Gateway Offloading pattern</span></span>](./gateway-offloading.md)
