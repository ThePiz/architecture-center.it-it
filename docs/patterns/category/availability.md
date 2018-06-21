---
title: Modelli di disponibilità
description: La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione. Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema. Viene in genere misurata come percentuale del tempo di attività. Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: eac1d192fe83f8501d11dce02d9bec16e939ba55
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30847278"
---
# <a name="availability-patterns"></a>Modelli di disponibilità

[!INCLUDE [header](../../_includes/header.md)]

La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione. Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema. Viene in genere misurata come percentuale del tempo di attività. Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità.


|                            Modello                             |                                                           Summary                                                            |
|----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| [Monitoraggio endpoint di integrità](../health-endpoint-monitoring.md) | Implementare controlli funzionali all'interno di un'applicazione a cui gli strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari. |
|  [Livellamento del carico basato sulle code](../queue-based-load-leveling.md)  | Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti.  |
|                 [Limitazione](../throttling.md)                 |   Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio.    |

