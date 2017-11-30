---
title: "Modelli di disponibilità"
description: "La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione. Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema. Viene in genere misurata come percentuale del tempo di attività. Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità."
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: f7eb6b0df388b2f1dab83e64ab540cc22f368e19
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="availability-patterns"></a><span data-ttu-id="0f397-107">Modelli di disponibilità</span><span class="sxs-lookup"><span data-stu-id="0f397-107">Availability patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="0f397-108">La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="0f397-108">Availability defines the proportion of time that the system is functional and working.</span></span> <span data-ttu-id="0f397-109">Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema.</span><span class="sxs-lookup"><span data-stu-id="0f397-109">It will be affected by system errors, infrastructure problems, malicious attacks, and system load.</span></span> <span data-ttu-id="0f397-110">Viene in genere misurata come percentuale del tempo di attività.</span><span class="sxs-lookup"><span data-stu-id="0f397-110">It is usually measured as a percentage of uptime.</span></span> <span data-ttu-id="0f397-111">Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità.</span><span class="sxs-lookup"><span data-stu-id="0f397-111">Cloud applications typically provide users with a service level agreement (SLA), which means that applications must be designed and implemented in a way that maximizes availability.</span></span>

| <span data-ttu-id="0f397-112">Modello</span><span class="sxs-lookup"><span data-stu-id="0f397-112">Pattern</span></span> | <span data-ttu-id="0f397-113">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="0f397-113">Summary</span></span> |
| ------- | ------- |
| [<span data-ttu-id="0f397-114">Monitoraggio dell'integrità tramite endpoint</span><span class="sxs-lookup"><span data-stu-id="0f397-114">Health Endpoint Monitoring</span></span>](../health-endpoint-monitoring.md) | <span data-ttu-id="0f397-115">Implementare controlli funzionali all'interno di un'applicazione a cui gli strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="0f397-115">Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals.</span></span> |
| [<span data-ttu-id="0f397-116">Livellamento del carico basato sulle code</span><span class="sxs-lookup"><span data-stu-id="0f397-116">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md) | <span data-ttu-id="0f397-117">Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti.</span><span class="sxs-lookup"><span data-stu-id="0f397-117">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span> |
| [<span data-ttu-id="0f397-118">Limitazione</span><span class="sxs-lookup"><span data-stu-id="0f397-118">Throttling</span></span>](../throttling.md) | <span data-ttu-id="0f397-119">Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio.</span><span class="sxs-lookup"><span data-stu-id="0f397-119">Control the consumption of resources used by an instance of an application, an individual tenant, or an entire service.</span></span> |