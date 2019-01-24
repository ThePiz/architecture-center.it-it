---
title: Modelli di messaggistica
titleSuffix: Cloud Design Patterns
description: La tipologia distribuita delle applicazioni cloud richiede un'infrastruttura di messaggistica in grado di connettere componenti e servizi, idealmente con un approccio "a regime di controllo libero" per ottimizzare la scalabilità. La messaggistica asincrona è ampiamente usata e, pur offrendo numerosi vantaggi, comporta anche sfide come l'ordinamento dei messaggi, la gestione dei messaggi non elaborabili, l'idempotenza e altro ancora.
keywords: schema progettuale
author: dragon119
ms.date: 12/07/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 2f8a34afc5e6c427bf5635b7a3be316719211112
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485163"
---
# <a name="messaging-patterns"></a><span data-ttu-id="24006-105">Modelli di messaggistica</span><span class="sxs-lookup"><span data-stu-id="24006-105">Messaging patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="24006-106">La tipologia distribuita delle applicazioni cloud richiede un'infrastruttura di messaggistica in grado di connettere componenti e servizi, idealmente con un approccio "a regime di controllo libero" per ottimizzare la scalabilità.</span><span class="sxs-lookup"><span data-stu-id="24006-106">The distributed nature of cloud applications requires a messaging infrastructure that connects the components and services, ideally in a loosely coupled manner in order to maximize scalability.</span></span> <span data-ttu-id="24006-107">La messaggistica asincrona è ampiamente usata e, pur offrendo numerosi vantaggi, comporta anche sfide come l'ordinamento dei messaggi, la gestione dei messaggi non elaborabili, l'idempotenza e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="24006-107">Asynchronous messaging is widely used, and provides many benefits, but also brings challenges such as the ordering of messages, poison message management, idempotency, and more.</span></span>

| <span data-ttu-id="24006-108">Modello</span><span class="sxs-lookup"><span data-stu-id="24006-108">Pattern</span></span> | <span data-ttu-id="24006-109">Summary</span><span class="sxs-lookup"><span data-stu-id="24006-109">Summary</span></span> |
| ------- | ------- |
| [<span data-ttu-id="24006-110">Consumer concorrenti</span><span class="sxs-lookup"><span data-stu-id="24006-110">Competing Consumers</span></span>](../competing-consumers.md) | <span data-ttu-id="24006-111">Consentire a più consumer concorrenti di elaborare i messaggi ricevuti sullo stesso canale di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="24006-111">Enable multiple concurrent consumers to process messages received on the same messaging channel.</span></span> |
| [<span data-ttu-id="24006-112">Pipe e filtri</span><span class="sxs-lookup"><span data-stu-id="24006-112">Pipes and Filters</span></span>](../pipes-and-filters.md) | <span data-ttu-id="24006-113">Scomporre un'attività che esegue un'elaborazione complessa in una serie di elementi distinti riutilizzabili.</span><span class="sxs-lookup"><span data-stu-id="24006-113">Break down a task that performs complex processing into a series of separate elements that can be reused.</span></span> |
| [<span data-ttu-id="24006-114">Coda di priorità</span><span class="sxs-lookup"><span data-stu-id="24006-114">Priority Queue</span></span>](../priority-queue.md) | <span data-ttu-id="24006-115">Assegnare una priorità alle richieste inviate ai servizi in modo che le richieste con una priorità più alta vengano ricevute ed elaborate più rapidamente rispetto a quelle con priorità più bassa.</span><span class="sxs-lookup"><span data-stu-id="24006-115">Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those with a lower priority.</span></span> |
| [<span data-ttu-id="24006-116">Server di pubblicazione - Sottoscrittore</span><span class="sxs-lookup"><span data-stu-id="24006-116">Publisher-Subscriber</span></span>](../publisher-subscriber.md) | <span data-ttu-id="24006-117">Abilitare un'applicazione all'annuncio di eventi a più consumer interessati in modalità asincrona, senza accoppiamento di mittenti e destinatari.</span><span class="sxs-lookup"><span data-stu-id="24006-117">Enable an application to announce events to multiple interested consumers aynchronously, without coupling the senders to the receivers.</span></span> |
| [<span data-ttu-id="24006-118">Livellamento del carico basato sulle code</span><span class="sxs-lookup"><span data-stu-id="24006-118">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md) | <span data-ttu-id="24006-119">Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi elevati intermittenti.</span><span class="sxs-lookup"><span data-stu-id="24006-119">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span> |
| [<span data-ttu-id="24006-120">Supervisione agente di pianificazione</span><span class="sxs-lookup"><span data-stu-id="24006-120">Scheduler Agent Supervisor</span></span>](../scheduler-agent-supervisor.md) | <span data-ttu-id="24006-121">Coordinare un set di azioni in un set distribuito di servizi e altre risorse remote.</span><span class="sxs-lookup"><span data-stu-id="24006-121">Coordinate a set of actions across a distributed set of services and other remote resources.</span></span> |
