---
title: Modello di offload gateway
titleSuffix: Cloud Design Patterns
description: Eseguire l'offload delle funzionalità dei servizi condivise o specializzate in un proxy gateway.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 28dbd1798060d1bdd01b1e6bee03337a2239a9ef
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487662"
---
# <a name="gateway-offloading-pattern"></a><span data-ttu-id="18f03-104">Modello di offload gateway</span><span class="sxs-lookup"><span data-stu-id="18f03-104">Gateway Offloading pattern</span></span>

<span data-ttu-id="18f03-105">Eseguire l'offload delle funzionalità dei servizi condivise o specializzate in un proxy gateway.</span><span class="sxs-lookup"><span data-stu-id="18f03-105">Offload shared or specialized service functionality to a gateway proxy.</span></span> <span data-ttu-id="18f03-106">Questo modello consente di semplificare lo sviluppo di applicazioni grazie allo spostamento di funzionalità di servizi condivisi, quali l'uso di certificati SSL, da altre parti dell'applicazione nel gateway.</span><span class="sxs-lookup"><span data-stu-id="18f03-106">This pattern can simplify application development by moving shared service functionality, such as the use of SSL certificates, from other parts of the application into the gateway.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="18f03-107">Contesto e problema</span><span class="sxs-lookup"><span data-stu-id="18f03-107">Context and problem</span></span>

<span data-ttu-id="18f03-108">Alcune funzionalità usate comunemente in più servizi richiedono l'esecuzione di attività di configurazione, gestione e manutenzione.</span><span class="sxs-lookup"><span data-stu-id="18f03-108">Some features are commonly used across multiple services, and these features require configuration, management, and maintenance.</span></span> <span data-ttu-id="18f03-109">Un servizio condiviso o specializzato che viene distribuito con ogni distribuzione dell'applicazione implica un incremento del carico amministrativo, nonché delle probabilità di errori di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="18f03-109">A shared or specialized service that is distributed with every application deployment increases the administrative overhead and increases the likelihood of deployment error.</span></span> <span data-ttu-id="18f03-110">Tutti gli aggiornamenti di una funzionalità condivisa devono essere distribuiti in tutti i servizi che condividono tale funzionalità.</span><span class="sxs-lookup"><span data-stu-id="18f03-110">Any updates to a shared feature must be deployed across all services that share that feature.</span></span>

<span data-ttu-id="18f03-111">Per gestire correttamente problemi di sicurezza (convalida dei token, crittografia, gestione dei certificati SSL) e altre attività complesse, i membri del team devono essere altamente specializzati.</span><span class="sxs-lookup"><span data-stu-id="18f03-111">Properly handling security issues (token validation, encryption, SSL certificate management) and other complex tasks can require team members to have highly specialized skills.</span></span> <span data-ttu-id="18f03-112">Ad esempio, un certificato richiesto da un'applicazione deve essere configurato e distribuito in tutte le istanze dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="18f03-112">For example, a certificate needed by an application must be configured and deployed on all application instances.</span></span> <span data-ttu-id="18f03-113">Con ogni nuova distribuzione il certificato deve essere gestito per assicurarsi che non scada.</span><span class="sxs-lookup"><span data-stu-id="18f03-113">With each new deployment, the certificate must be managed to ensure that it does not expire.</span></span> <span data-ttu-id="18f03-114">In prossimità della scadenza, tutti i certificati comuni devono essere aggiornati, testati e verificati in ogni distribuzione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="18f03-114">Any common certificate that is due to expire must be updated, tested, and verified on every application deployment.</span></span>

<span data-ttu-id="18f03-115">L'implementazione e la gestione di altri servizi comuni, tra cui l'autenticazione, l'autorizzazione, la registrazione, il monitoraggio e la [limitazione delle richieste](./throttling.md), possono risultare complicate in un numero elevato di distribuzioni.</span><span class="sxs-lookup"><span data-stu-id="18f03-115">Other common services such as authentication, authorization, logging, monitoring, or [throttling](./throttling.md) can be difficult to implement and manage across a large number of deployments.</span></span> <span data-ttu-id="18f03-116">Può quindi essere opportuno consolidare questo tipo di funzionalità per ridurre il sovraccarico e la probabilità di errori.</span><span class="sxs-lookup"><span data-stu-id="18f03-116">It may be better to consolidate this type of functionality, in order to reduce overhead and the chance of errors.</span></span>

## <a name="solution"></a><span data-ttu-id="18f03-117">Soluzione</span><span class="sxs-lookup"><span data-stu-id="18f03-117">Solution</span></span>

<span data-ttu-id="18f03-118">Eseguire l'offload in un gateway API di alcune funzionalità, in particolare di quelle con problematiche trasversali, come la gestione dei certificati, l'autenticazione, la terminazione SSL, il monitoraggio, la conversione dei protocolli e la limitazione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="18f03-118">Offload some features into an API gateway, particularly cross-cutting concerns such as certificate management, authentication, SSL termination, monitoring, protocol translation, or throttling.</span></span>

<span data-ttu-id="18f03-119">Il diagramma seguente illustra un gateway API che termina le connessioni SSL in ingresso.</span><span class="sxs-lookup"><span data-stu-id="18f03-119">The following diagram shows an API gateway that terminates inbound SSL connections.</span></span> <span data-ttu-id="18f03-120">Il gateway richiede i dati per conto del richiedente originale di qualsiasi server HTTP a monte del gateway API.</span><span class="sxs-lookup"><span data-stu-id="18f03-120">It requests data on behalf of the original requestor from any HTTP server upstream of the API gateway.</span></span>

 ![Diagramma del modello di offload gateway](./_images/gateway-offload.png)

<span data-ttu-id="18f03-122">I vantaggi di questo schema includono:</span><span class="sxs-lookup"><span data-stu-id="18f03-122">Benefits of this pattern include:</span></span>

- <span data-ttu-id="18f03-123">Semplificazione dello sviluppo di servizi, perché non è più necessario distribuire e gestire risorse di supporto, ad esempio i certificati server Web e la configurazione dei siti Web sicuri.</span><span class="sxs-lookup"><span data-stu-id="18f03-123">Simplify the development of services by removing the need to distribute and maintain supporting resources, such as web server certificates and configuration for secure websites.</span></span> <span data-ttu-id="18f03-124">La semplificazione della configurazione facilita la gestione e la scalabilità, semplificando a sua volta gli aggiornamenti del servizio.</span><span class="sxs-lookup"><span data-stu-id="18f03-124">Simpler configuration results in easier management and scalability and makes service upgrades simpler.</span></span>

- <span data-ttu-id="18f03-125">Possibilità per i team dedicati di implementare funzionalità che richiedono competenze specifiche, come la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="18f03-125">Allow dedicated teams to implement features that require specialized expertise, such as security.</span></span> <span data-ttu-id="18f03-126">In questo modo il core team può concentrarsi sulle funzionalità dell'applicazione, lasciando queste problematiche trasversali agli esperti del settore.</span><span class="sxs-lookup"><span data-stu-id="18f03-126">This allows your core team to focus on the application functionality, leaving these specialized but cross-cutting concerns to the relevant experts.</span></span>

- <span data-ttu-id="18f03-127">Implementazione di un certo livello di coerenza per la registrazione e il monitoraggio di richieste e risposte.</span><span class="sxs-lookup"><span data-stu-id="18f03-127">Provide some consistency for request and response logging and monitoring.</span></span> <span data-ttu-id="18f03-128">Anche se un servizio non è instrumentato correttamente, è possibile configurare il gateway in modo da garantire un livello minimo di monitoraggio e registrazione.</span><span class="sxs-lookup"><span data-stu-id="18f03-128">Even if a service is not correctly instrumented, the gateway can be configured to ensure a minimum level of monitoring and logging.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="18f03-129">Considerazioni e problemi</span><span class="sxs-lookup"><span data-stu-id="18f03-129">Issues and considerations</span></span>

- <span data-ttu-id="18f03-130">Assicurarsi che il gateway API sia resiliente agli errori e a disponibilità elevata.</span><span class="sxs-lookup"><span data-stu-id="18f03-130">Ensure the API gateway is highly available and resilient to failure.</span></span> <span data-ttu-id="18f03-131">Per evitare singoli punti di guasto, eseguire più istanze del gateway API.</span><span class="sxs-lookup"><span data-stu-id="18f03-131">Avoid single points of failure by running multiple instances of your API gateway.</span></span>
- <span data-ttu-id="18f03-132">Assicurarsi che il gateway che sia progettato per soddisfare i requisiti di capacità e scalabilità dell'applicazione e degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="18f03-132">Ensure the gateway is designed for the capacity and scaling requirements of your application and endpoints.</span></span> <span data-ttu-id="18f03-133">Assicurarsi che il gateway non diventi un collo di bottiglia per l'applicazione e che sia sufficientemente scalabile.</span><span class="sxs-lookup"><span data-stu-id="18f03-133">Make sure the gateway does not become a bottleneck for the application and is sufficiently scalable.</span></span>
- <span data-ttu-id="18f03-134">Eseguire l'offload solo delle funzionalità usate in tutta l'applicazione, ad esempio il trasferimento di dati o la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="18f03-134">Only offload features that are used by the entire application, such as security or data transfer.</span></span>
- <span data-ttu-id="18f03-135">Non eseguire mai l'offload della logica di business al gateway API.</span><span class="sxs-lookup"><span data-stu-id="18f03-135">Business logic should never be offloaded to the API gateway.</span></span>
- <span data-ttu-id="18f03-136">Se è necessario tenere traccia delle transazioni, provare a generare ID di correlazione ai fini della registrazione.</span><span class="sxs-lookup"><span data-stu-id="18f03-136">If you need to track transactions, consider generating correlation IDs for logging purposes.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="18f03-137">Quando usare questo modello</span><span class="sxs-lookup"><span data-stu-id="18f03-137">When to use this pattern</span></span>

<span data-ttu-id="18f03-138">Usare questo modello quando:</span><span class="sxs-lookup"><span data-stu-id="18f03-138">Use this pattern when:</span></span>

- <span data-ttu-id="18f03-139">La distribuzione di un'applicazione implica problematiche condivise, come i certificati SSL o la crittografia.</span><span class="sxs-lookup"><span data-stu-id="18f03-139">An application deployment has a shared concern such as SSL certificates or encryption.</span></span>
- <span data-ttu-id="18f03-140">Una funzionalità è in comune tra più distribuzioni di applicazioni che potrebbero avere requisiti diversi in termini di risorse, ad esempio risorse di memoria, capacità di archiviazione o connessioni di rete.</span><span class="sxs-lookup"><span data-stu-id="18f03-140">A feature that is common across application deployments that may have different resource requirements, such as memory resources, storage capacity or network connections.</span></span>
- <span data-ttu-id="18f03-141">Si vuole demandare a un team specializzato la responsabilità di problemi quali la sicurezza di rete, la limitazione delle risorse o altre problematiche relative ai limiti di rete.</span><span class="sxs-lookup"><span data-stu-id="18f03-141">You wish to move the responsibility for issues such as network security, throttling, or other network boundary concerns to a more specialized team.</span></span>

<span data-ttu-id="18f03-142">Questo modello potrebbe non essere adatto se introduce l'accoppiamento tra i servizi.</span><span class="sxs-lookup"><span data-stu-id="18f03-142">This pattern may not be suitable if it introduces coupling across services.</span></span>

## <a name="example"></a><span data-ttu-id="18f03-143">Esempio</span><span class="sxs-lookup"><span data-stu-id="18f03-143">Example</span></span>

<span data-ttu-id="18f03-144">Usando Nginx come appliance di offload SSL, la configurazione seguente consente di terminare una connessione SSL in ingresso e di distribuire la connessione a uno dei tre server HTTP a monte.</span><span class="sxs-lookup"><span data-stu-id="18f03-144">Using Nginx as the SSL offload appliance, the following configuration terminates an inbound SSL connection and distributes the connection to one of three upstream HTTP servers.</span></span>

```console
upstream iis {
        server  10.3.0.10    max_fails=3    fail_timeout=15s;
        server  10.3.0.20    max_fails=3    fail_timeout=15s;
        server  10.3.0.30    max_fails=3    fail_timeout=15s;
}

server {
        listen 443;
        ssl on;
        ssl_certificate /etc/nginx/ssl/domain.cer;
        ssl_certificate_key /etc/nginx/ssl/domain.key;

        location / {
                set $targ iis;
                proxy_pass http://$targ;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header Host $host;
        }
}
```

## <a name="related-guidance"></a><span data-ttu-id="18f03-145">Informazioni correlate</span><span class="sxs-lookup"><span data-stu-id="18f03-145">Related guidance</span></span>

- [<span data-ttu-id="18f03-146">Modello back-end per front-end</span><span class="sxs-lookup"><span data-stu-id="18f03-146">Backends for Frontends pattern</span></span>](./backends-for-frontends.md)
- [<span data-ttu-id="18f03-147">Modello di aggregazione gateway</span><span class="sxs-lookup"><span data-stu-id="18f03-147">Gateway Aggregation pattern</span></span>](./gateway-aggregation.md)
- [<span data-ttu-id="18f03-148">Modello di routing gateway</span><span class="sxs-lookup"><span data-stu-id="18f03-148">Gateway Routing pattern</span></span>](./gateway-routing.md)
