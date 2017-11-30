---
title: Modelli di messaggistica
description: "La tipologia distribuita delle applicazioni cloud richiede un'infrastruttura di messaggistica in grado di connettere componenti e servizi, idealmente con un approccio \"a regime di controllo libero\" per ottimizzare la scalabilità. La messaggistica asincrona è ampiamente usata e, pur offrendo numerosi vantaggi, comporta anche sfide come l'ordinamento dei messaggi, la gestione dei messaggi non elaborabili, l'idempotenza e altro ancora."
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: 6151f7f76fc7b3a953988122db75bdc25b49811f
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="messaging-patterns"></a><span data-ttu-id="9e7a8-105">Modelli di messaggistica</span><span class="sxs-lookup"><span data-stu-id="9e7a8-105">Messaging patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="9e7a8-106">La tipologia distribuita delle applicazioni cloud richiede un'infrastruttura di messaggistica in grado di connettere componenti e servizi, idealmente con un approccio "a regime di controllo libero" per ottimizzare la scalabilità.</span><span class="sxs-lookup"><span data-stu-id="9e7a8-106">The distributed nature of cloud applications requires a messaging infrastructure that connects the components and services, ideally in a loosely coupled manner in order to maximize scalability.</span></span> <span data-ttu-id="9e7a8-107">La messaggistica asincrona è ampiamente usata e, pur offrendo numerosi vantaggi, comporta anche sfide come l'ordinamento dei messaggi, la gestione dei messaggi non elaborabili, l'idempotenza e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="9e7a8-107">Asynchronous messaging is widely used, and provides many benefits, but also brings challenges such as the ordering of messages, poison message management, idempotency, and more.</span></span>

| <span data-ttu-id="9e7a8-108">Modello</span><span class="sxs-lookup"><span data-stu-id="9e7a8-108">Pattern</span></span> | <span data-ttu-id="9e7a8-109">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="9e7a8-109">Summary</span></span> |
| ------- | ------- |
| [<span data-ttu-id="9e7a8-110">Consumer concorrenti</span><span class="sxs-lookup"><span data-stu-id="9e7a8-110">Competing Consumers</span></span>](../competing-consumers.md) | <span data-ttu-id="9e7a8-111">Consentire a più consumer concorrenti di elaborare i messaggi ricevuti sullo stesso canale di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="9e7a8-111">Enable multiple concurrent consumers to process messages received on the same messaging channel.</span></span> |
| [<span data-ttu-id="9e7a8-112">Pipe e filtri</span><span class="sxs-lookup"><span data-stu-id="9e7a8-112">Pipes and Filters</span></span>](../pipes-and-filters.md) | <span data-ttu-id="9e7a8-113">Scomporre un'attività che esegue un'elaborazione complessa in una serie di elementi distinti riutilizzabili.</span><span class="sxs-lookup"><span data-stu-id="9e7a8-113">Break down a task that performs complex processing into a series of separate elements that can be reused.</span></span> |
| [<span data-ttu-id="9e7a8-114">Coda di priorità</span><span class="sxs-lookup"><span data-stu-id="9e7a8-114">Priority Queue</span></span>](../priority-queue.md) | <span data-ttu-id="9e7a8-115">Assegnare una priorità alle richieste inviate ai servizi in modo che le richieste con una priorità più alta vengano ricevute ed elaborate più rapidamente rispetto a quelle con priorità più bassa.</span><span class="sxs-lookup"><span data-stu-id="9e7a8-115">Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those with a lower priority.</span></span> |
| [<span data-ttu-id="9e7a8-116">Livellamento del carico basato sulle code</span><span class="sxs-lookup"><span data-stu-id="9e7a8-116">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md) | <span data-ttu-id="9e7a8-117">Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi elevati intermittenti.</span><span class="sxs-lookup"><span data-stu-id="9e7a8-117">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span> |
| [<span data-ttu-id="9e7a8-118">Supervisione agente di pianificazione</span><span class="sxs-lookup"><span data-stu-id="9e7a8-118">Scheduler Agent Supervisor</span></span>](../scheduler-agent-supervisor.md) | <span data-ttu-id="9e7a8-119">Coordinare un set di azioni in un set distribuito di servizi e altre risorse remote.</span><span class="sxs-lookup"><span data-stu-id="9e7a8-119">Coordinate a set of actions across a distributed set of services and other remote resources.</span></span> |