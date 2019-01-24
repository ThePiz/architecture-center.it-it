---
title: Modelli di disponibilità
titleSuffix: Cloud Design Patterns
description: La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione. Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema. Viene in genere misurata come percentuale del tempo di attività. Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 897bc3c87beb23770220ef0fc3c5c4394d192f2a
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486798"
---
# <a name="availability-patterns"></a><span data-ttu-id="71058-107">Modelli di disponibilità</span><span class="sxs-lookup"><span data-stu-id="71058-107">Availability patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="71058-108">La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="71058-108">Availability defines the proportion of time that the system is functional and working.</span></span> <span data-ttu-id="71058-109">Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema.</span><span class="sxs-lookup"><span data-stu-id="71058-109">It will be affected by system errors, infrastructure problems, malicious attacks, and system load.</span></span> <span data-ttu-id="71058-110">Viene in genere misurata come percentuale del tempo di attività.</span><span class="sxs-lookup"><span data-stu-id="71058-110">It is usually measured as a percentage of uptime.</span></span> <span data-ttu-id="71058-111">Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità.</span><span class="sxs-lookup"><span data-stu-id="71058-111">Cloud applications typically provide users with a service level agreement (SLA), which means that applications must be designed and implemented in a way that maximizes availability.</span></span>

|                            <span data-ttu-id="71058-112">Modello</span><span class="sxs-lookup"><span data-stu-id="71058-112">Pattern</span></span>                             |                                                           <span data-ttu-id="71058-113">Summary</span><span class="sxs-lookup"><span data-stu-id="71058-113">Summary</span></span>                                                            |
|----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| [<span data-ttu-id="71058-114">Monitoraggio endpoint di integrità</span><span class="sxs-lookup"><span data-stu-id="71058-114">Health Endpoint Monitoring</span></span>](../health-endpoint-monitoring.md) | <span data-ttu-id="71058-115">Implementare controlli funzionali all'interno di un'applicazione a cui gli strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="71058-115">Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals.</span></span> |
|  [<span data-ttu-id="71058-116">Livellamento del carico basato sulle code</span><span class="sxs-lookup"><span data-stu-id="71058-116">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md)  | <span data-ttu-id="71058-117">Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti.</span><span class="sxs-lookup"><span data-stu-id="71058-117">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span>  |
|                 [<span data-ttu-id="71058-118">Limitazione</span><span class="sxs-lookup"><span data-stu-id="71058-118">Throttling</span></span>](../throttling.md)                 |   <span data-ttu-id="71058-119">Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio.</span><span class="sxs-lookup"><span data-stu-id="71058-119">Control the consumption of resources used by an instance of an application, an individual tenant, or an entire service.</span></span>    |
