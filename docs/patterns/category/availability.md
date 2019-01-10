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
# <a name="availability-patterns"></a>Modelli di disponibilità

[!INCLUDE [header](../../_includes/header.md)]

La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione. Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema. Viene in genere misurata come percentuale del tempo di attività. Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità.

|                            Modello                             |                                                           Summary                                                            |
|----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| [Monitoraggio endpoint di integrità](../health-endpoint-monitoring.md) | Implementare controlli funzionali all'interno di un'applicazione a cui gli strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari. |
|  [Livellamento del carico basato sulle code](../queue-based-load-leveling.md)  | Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti.  |
|                 [Limitazione](../throttling.md)                 |   Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio.    |
