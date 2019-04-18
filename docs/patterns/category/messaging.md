---
title: Modelli di messaggistica
titleSuffix: Cloud Design Patterns
description: La tipologia distribuita delle applicazioni cloud richiede un'infrastruttura di messaggistica in grado di connettere componenti e servizi, idealmente con un approccio "a regime di controllo libero" per ottimizzare la scalabilità. La messaggistica asincrona è ampiamente usata e, pur offrendo numerosi vantaggi, comporta anche sfide come l'ordinamento dei messaggi, la gestione dei messaggi non elaborabili, l'idempotenza e altro ancora.
keywords: schema progettuale
author: dragon119
ms.date: 03/01/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: f838288e10acf9af5776c93972028b6b878bd7b0
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640669"
---
# <a name="messaging-patterns"></a><span data-ttu-id="68042-105">Modelli di messaggistica</span><span class="sxs-lookup"><span data-stu-id="68042-105">Messaging patterns</span></span>

<span data-ttu-id="68042-106">La tipologia distribuita delle applicazioni cloud richiede un'infrastruttura di messaggistica in grado di connettere componenti e servizi, idealmente con un approccio "a regime di controllo libero" per ottimizzare la scalabilità.</span><span class="sxs-lookup"><span data-stu-id="68042-106">The distributed nature of cloud applications requires a messaging infrastructure that connects the components and services, ideally in a loosely coupled manner in order to maximize scalability.</span></span> <span data-ttu-id="68042-107">La messaggistica asincrona è ampiamente usata e, pur offrendo numerosi vantaggi, comporta anche sfide come l'ordinamento dei messaggi, la gestione dei messaggi non elaborabili, l'idempotenza e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="68042-107">Asynchronous messaging is widely used, and provides many benefits, but also brings challenges such as the ordering of messages, poison message management, idempotency, and more.</span></span>

| <span data-ttu-id="68042-108">Modello</span><span class="sxs-lookup"><span data-stu-id="68042-108">Pattern</span></span> | <span data-ttu-id="68042-109">Summary</span><span class="sxs-lookup"><span data-stu-id="68042-109">Summary</span></span> |
| ------- | ------- |
| [<span data-ttu-id="68042-110">Scontrino</span><span class="sxs-lookup"><span data-stu-id="68042-110">Claim Check</span></span>](../claim-check.md) | <span data-ttu-id="68042-111">Dividere un messaggio di grandi dimensioni in uno scontrino e un payload per evitare di sovraccaricare il bus dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="68042-111">Split a large message into a claim check and a payload to avoid overwhelming a message bus.</span></span> |
| [<span data-ttu-id="68042-112">Consumer concorrenti</span><span class="sxs-lookup"><span data-stu-id="68042-112">Competing Consumers</span></span>](../competing-consumers.md) | <span data-ttu-id="68042-113">Consentire a più consumer concorrenti di elaborare i messaggi ricevuti sullo stesso canale di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="68042-113">Enable multiple concurrent consumers to process messages received on the same messaging channel.</span></span> |
| [<span data-ttu-id="68042-114">Pipe e filtri</span><span class="sxs-lookup"><span data-stu-id="68042-114">Pipes and Filters</span></span>](../pipes-and-filters.md) | <span data-ttu-id="68042-115">Scomporre un'attività che esegue un'elaborazione complessa in una serie di elementi distinti riutilizzabili.</span><span class="sxs-lookup"><span data-stu-id="68042-115">Break down a task that performs complex processing into a series of separate elements that can be reused.</span></span> |
| [<span data-ttu-id="68042-116">Coda di priorità</span><span class="sxs-lookup"><span data-stu-id="68042-116">Priority Queue</span></span>](../priority-queue.md) | <span data-ttu-id="68042-117">Assegnare una priorità alle richieste inviate ai servizi in modo che le richieste con una priorità più alta vengano ricevute ed elaborate più rapidamente rispetto a quelle con priorità più bassa.</span><span class="sxs-lookup"><span data-stu-id="68042-117">Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those with a lower priority.</span></span> |
| [<span data-ttu-id="68042-118">Server di pubblicazione - Sottoscrittore</span><span class="sxs-lookup"><span data-stu-id="68042-118">Publisher-Subscriber</span></span>](../publisher-subscriber.md) | <span data-ttu-id="68042-119">Abilitare un'applicazione a cui annunciare gli eventi a più consumer interessate in modo asincrono, senza l'accoppiamento tra i mittenti ai destinatari.</span><span class="sxs-lookup"><span data-stu-id="68042-119">Enable an application to announce events to multiple interested consumers asynchronously, without coupling the senders to the receivers.</span></span> |
| [<span data-ttu-id="68042-120">Livellamento del carico basato sulle code</span><span class="sxs-lookup"><span data-stu-id="68042-120">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md) | <span data-ttu-id="68042-121">Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi elevati intermittenti.</span><span class="sxs-lookup"><span data-stu-id="68042-121">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span> |
| [<span data-ttu-id="68042-122">Supervisione agente di pianificazione</span><span class="sxs-lookup"><span data-stu-id="68042-122">Scheduler Agent Supervisor</span></span>](../scheduler-agent-supervisor.md) | <span data-ttu-id="68042-123">Coordinare un set di azioni in un set distribuito di servizi e altre risorse remote.</span><span class="sxs-lookup"><span data-stu-id="68042-123">Coordinate a set of actions across a distributed set of services and other remote resources.</span></span> |
