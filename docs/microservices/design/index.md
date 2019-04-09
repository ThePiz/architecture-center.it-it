---
title: Implementazione di riferimento di microservizi per il servizio Azure Kubernetes
description: Questa implementazione di riferimento illustra le procedure consigliate per un'architettura di microservizi
author: MikeWasson
ms.date: 02/26/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: 17e275e5b5f45233f7467192402cb28fce35c57b
ms.sourcegitcommit: 0a8a60d782facc294f7f78ec0e9033e3ee16bf4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068906"
---
# <a name="designing-a-microservices-architecture"></a><span data-ttu-id="4e52d-103">Progettazione di un'architettura di microservizi</span><span class="sxs-lookup"><span data-stu-id="4e52d-103">Designing a microservices architecture</span></span>

<span data-ttu-id="4e52d-104">I microservizi sono diventati uno stile di architettura diffuso per la creazione di applicazioni cloud che offrono resilienza, scalabilità elevata, possibilità di distribuzione indipendente e capacità di evolversi rapidamente.</span><span class="sxs-lookup"><span data-stu-id="4e52d-104">Microservices have become a popular architectural style for building cloud applications that are resilient, highly scalable, independently deployable, and able to evolve quickly.</span></span> <span data-ttu-id="4e52d-105">I microservizi non sono solo una moda, ma rappresentano un nuovo concetto che richiede un approccio diverso alla progettazione e alla creazione di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="4e52d-105">To be more than just a buzzword, however, microservices require a different approach to designing and building applications.</span></span>

<span data-ttu-id="4e52d-106">In questo set di articoli viene analizzato come creare ed eseguire un'architettura di microservizi in Azure.</span><span class="sxs-lookup"><span data-stu-id="4e52d-106">In this set of articles, we explore how to build and run a microservices architecture on Azure.</span></span> <span data-ttu-id="4e52d-107">Gli argomenti includono:</span><span class="sxs-lookup"><span data-stu-id="4e52d-107">Topics include:</span></span>

- [<span data-ttu-id="4e52d-108">Comunicazione tra i servizi</span><span class="sxs-lookup"><span data-stu-id="4e52d-108">Interservice communication</span></span>](./interservice-communication.md)
- [<span data-ttu-id="4e52d-109">Progettazione API</span><span class="sxs-lookup"><span data-stu-id="4e52d-109">API design</span></span>](./api-design.md)
- [<span data-ttu-id="4e52d-110">Gateway API</span><span class="sxs-lookup"><span data-stu-id="4e52d-110">API gateways</span></span>](./gateway.md)
- [<span data-ttu-id="4e52d-111">Considerazioni sui dati</span><span class="sxs-lookup"><span data-stu-id="4e52d-111">Data considerations</span></span>](./data-considerations.md)
- [<span data-ttu-id="4e52d-112">Modelli di progettazione</span><span class="sxs-lookup"><span data-stu-id="4e52d-112">Design patterns</span></span>](./patterns.md)

## <a name="prerequisites"></a><span data-ttu-id="4e52d-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="4e52d-113">Prerequisites</span></span>

<span data-ttu-id="4e52d-114">Prima di leggere questi articoli, è consigliabile iniziare con quanto segue:</span><span class="sxs-lookup"><span data-stu-id="4e52d-114">Before reading these articles, you might start with the following:</span></span>

- <span data-ttu-id="4e52d-115">[Introduzione alle architetture di microservizi](../introduction.md).</span><span class="sxs-lookup"><span data-stu-id="4e52d-115">[Introduction to microservices architectures](../introduction.md).</span></span> <span data-ttu-id="4e52d-116">Informazioni su vantaggi e sfide dei microservizi e sui casi in cui usare questo stile di architettura.</span><span class="sxs-lookup"><span data-stu-id="4e52d-116">Understand the benefits and challenges of microservices, and when to use this style of architecture.</span></span>
- <span data-ttu-id="4e52d-117">[Uso dell'analisi del dominio per modellare i microservizi](../model/domain-analysis.md).</span><span class="sxs-lookup"><span data-stu-id="4e52d-117">[Using domain analysis to model microservices](../model/domain-analysis.md).</span></span> <span data-ttu-id="4e52d-118">Informazioni su un approccio basato su dominio per la modellazione di microservizi.</span><span class="sxs-lookup"><span data-stu-id="4e52d-118">Learn a domain-driven approach to modeling microservices.</span></span>

## <a name="reference-implementation"></a><span data-ttu-id="4e52d-119">Implementazione di riferimento</span><span class="sxs-lookup"><span data-stu-id="4e52d-119">Reference implementation</span></span>

<span data-ttu-id="4e52d-120">Per illustrare le procedure consigliate per un'architettura di microservizi, è stata creata un'implementazione di riferimento, ovvero l'applicazione di recapito tramite drone.</span><span class="sxs-lookup"><span data-stu-id="4e52d-120">To illustrate best practices for a microservices architecture, we created a reference implementation that we call the Drone Delivery application.</span></span> <span data-ttu-id="4e52d-121">Questa implementazione viene eseguita in Kubernetes usando il servizio Azure Kubernetes (AKS).</span><span class="sxs-lookup"><span data-stu-id="4e52d-121">This implementation runs on Kubernetes using Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="4e52d-122">L'implementazione di riferimento è disponibile su [GitHub][drone-ri].</span><span class="sxs-lookup"><span data-stu-id="4e52d-122">You can find the reference implementation on [GitHub][drone-ri].</span></span>

![Architettura dell'applicazione di recapito tramite drone](../images/drone-delivery.png)

## <a name="scenario"></a><span data-ttu-id="4e52d-124">Scenario</span><span class="sxs-lookup"><span data-stu-id="4e52d-124">Scenario</span></span>

<span data-ttu-id="4e52d-125">Fabrikam, Inc. sta avviando un servizio di recapito tramite drone.</span><span class="sxs-lookup"><span data-stu-id="4e52d-125">Fabrikam, Inc. is starting a drone delivery service.</span></span> <span data-ttu-id="4e52d-126">La società gestisce una flotta di droni.</span><span class="sxs-lookup"><span data-stu-id="4e52d-126">The company manages a fleet of drone aircraft.</span></span> <span data-ttu-id="4e52d-127">Le aziende possono registrarsi per usare il servizio e gli utenti possono richiedere un drone per prelevare merci da recapitare.</span><span class="sxs-lookup"><span data-stu-id="4e52d-127">Businesses register with the service, and users can request a drone to pick up goods for delivery.</span></span> <span data-ttu-id="4e52d-128">Quando un cliente pianifica un prelievo, un sistema back-end assegna un drone e invia all'utente una notifica con un tempo di recapito stimato.</span><span class="sxs-lookup"><span data-stu-id="4e52d-128">When a customer schedules a pickup, a backend system assigns a drone and notifies the user with an estimated delivery time.</span></span> <span data-ttu-id="4e52d-129">Durante il recapito, il cliente può tenere traccia della posizione del drone, con un tempo stimato di arrivo che viene aggiornato continuamente.</span><span class="sxs-lookup"><span data-stu-id="4e52d-129">While the delivery is in progress, the customer can track the location of the drone, with a continuously updated ETA.</span></span>

<span data-ttu-id="4e52d-130">Questo scenario prevede un dominio piuttosto complesso.</span><span class="sxs-lookup"><span data-stu-id="4e52d-130">This scenario involves a fairly complicated domain.</span></span> <span data-ttu-id="4e52d-131">Alcuni dei problemi aziendali da affrontare includono la pianificazione dei droni, il monitoraggio dei pacchetti, la gestione degli account utente e l'archiviazione e l'analisi dei dati cronologici.</span><span class="sxs-lookup"><span data-stu-id="4e52d-131">Some of the business concerns include scheduling drones, tracking packages, managing user accounts, and storing and analyzing historical data.</span></span> <span data-ttu-id="4e52d-132">Fabrikam vuole inoltre accelerare i tempi di immissione sul mercato e di iterazione, aggiungendo nuove caratteristiche e funzionalità.</span><span class="sxs-lookup"><span data-stu-id="4e52d-132">Moreover, Fabrikam wants to get to market quickly and then iterate quickly, adding new functionality and capabilities.</span></span> <span data-ttu-id="4e52d-133">L'applicazione deve operare a livello cloud, con un obiettivo del livello di servizio elevato.</span><span class="sxs-lookup"><span data-stu-id="4e52d-133">The application needs to operate at cloud scale, with a high service level objective (SLO).</span></span> <span data-ttu-id="4e52d-134">Fabrikam prevede inoltre che le diverse parti del sistema avranno requisiti molto diversi per l'archiviazione dei dati e le query.</span><span class="sxs-lookup"><span data-stu-id="4e52d-134">Fabrikam also expects that different parts of the system will have very different requirements for data storage and querying.</span></span> <span data-ttu-id="4e52d-135">Tutte queste considerazioni hanno portato Fabrikam a scegliere un'architettura di microservizi per l'applicazione di recapito tramite drone.</span><span class="sxs-lookup"><span data-stu-id="4e52d-135">All of these considerations lead Fabrikam to choose a microservices architecture for the Drone Delivery application.</span></span>

> [!NOTE]
> <span data-ttu-id="4e52d-136">Per un aiuto nella scelta tra un'architettura di microservizi e altri stili di architettura, vedere [Guida all'architettura delle applicazioni in Azure](../../guide/index.md).</span><span class="sxs-lookup"><span data-stu-id="4e52d-136">For help in choosing between a microservices architecture and other architectural styles, see the [Azure Application Architecture Guide](../../guide/index.md).</span></span>

<span data-ttu-id="4e52d-137">L'implementazione di riferimento usa Kubernetes con il [servizio Azure Kubernetes](/azure/aks/).</span><span class="sxs-lookup"><span data-stu-id="4e52d-137">Our reference implementation uses Kubernetes with [Azure Kubernetes Service](/azure/aks/) (AKS).</span></span> <span data-ttu-id="4e52d-138">Molte delle scelte e delle sfide generali relative all'architettura si applicano tuttavia a qualsiasi agente di orchestrazione di contenitori, tra cui [Azure Service Fabric](/azure/service-fabric/).</span><span class="sxs-lookup"><span data-stu-id="4e52d-138">However, many of the high-level architectural decisions and challenges will apply to any container orchestrator, including [Azure Service Fabric](/azure/service-fabric/).</span></span>

<!-- links -->

[drone-ri]: https://github.com/mspnp/microservices-reference-implementation/tree/v0.1.0-orig

## <a name="next-steps"></a><span data-ttu-id="4e52d-139">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="4e52d-139">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="4e52d-140">Scegliere un'opzione di calcolo</span><span class="sxs-lookup"><span data-stu-id="4e52d-140">Choose a compute option</span></span>](./compute-options.md)