---
title: Modelli di disponibilità
titleSuffix: Cloud Design Patterns
description: La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione. Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema. Viene in genere misurata come percentuale del tempo di attività. Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.custom: seodec18
ms.openlocfilehash: d256ddd39ca3e8a452162b7b75d67f26f7bb22c2
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54009815"
---
# <a name="availability-patterns"></a><span data-ttu-id="e70d7-107">Modelli di disponibilità</span><span class="sxs-lookup"><span data-stu-id="e70d7-107">Availability patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="e70d7-108">La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e70d7-108">Availability defines the proportion of time that the system is functional and working.</span></span> <span data-ttu-id="e70d7-109">Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema.</span><span class="sxs-lookup"><span data-stu-id="e70d7-109">It will be affected by system errors, infrastructure problems, malicious attacks, and system load.</span></span> <span data-ttu-id="e70d7-110">Viene in genere misurata come percentuale del tempo di attività.</span><span class="sxs-lookup"><span data-stu-id="e70d7-110">It is usually measured as a percentage of uptime.</span></span> <span data-ttu-id="e70d7-111">Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità.</span><span class="sxs-lookup"><span data-stu-id="e70d7-111">Cloud applications typically provide users with a service level agreement (SLA), which means that applications must be designed and implemented in a way that maximizes availability.</span></span>

|                            <span data-ttu-id="e70d7-112">Modello</span><span class="sxs-lookup"><span data-stu-id="e70d7-112">Pattern</span></span>                             |                                                           <span data-ttu-id="e70d7-113">Summary</span><span class="sxs-lookup"><span data-stu-id="e70d7-113">Summary</span></span>                                                            |
|----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| [<span data-ttu-id="e70d7-114">Monitoraggio endpoint di integrità</span><span class="sxs-lookup"><span data-stu-id="e70d7-114">Health Endpoint Monitoring</span></span>](../health-endpoint-monitoring.md) | <span data-ttu-id="e70d7-115">Implementare controlli funzionali all'interno di un'applicazione a cui gli strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari.</span><span class="sxs-lookup"><span data-stu-id="e70d7-115">Implement functional checks in an application that external tools can access through exposed endpoints at regular intervals.</span></span> |
|  [<span data-ttu-id="e70d7-116">Livellamento del carico basato sulle code</span><span class="sxs-lookup"><span data-stu-id="e70d7-116">Queue-Based Load Leveling</span></span>](../queue-based-load-leveling.md)  | <span data-ttu-id="e70d7-117">Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti.</span><span class="sxs-lookup"><span data-stu-id="e70d7-117">Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads.</span></span>  |
|                 [<span data-ttu-id="e70d7-118">Limitazione</span><span class="sxs-lookup"><span data-stu-id="e70d7-118">Throttling</span></span>](../throttling.md)                 |   <span data-ttu-id="e70d7-119">Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio.</span><span class="sxs-lookup"><span data-stu-id="e70d7-119">Control the consumption of resources used by an instance of an application, an individual tenant, or an entire service.</span></span>    |
