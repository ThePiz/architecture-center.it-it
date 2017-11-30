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
# <a name="availability-patterns"></a>Modelli di disponibilità

[!INCLUDE [header](../../_includes/header.md)]

La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione. Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi e carico del sistema. Viene in genere misurata come percentuale del tempo di attività. Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate e implementate in modo da ottimizzare la disponibilità.

| Modello | Riepilogo |
| ------- | ------- |
| [Monitoraggio dell'integrità tramite endpoint](../health-endpoint-monitoring.md) | Implementare controlli funzionali all'interno di un'applicazione a cui gli strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari. |
| [Livellamento del carico basato sulle code](../queue-based-load-leveling.md) | Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti. |
| [Limitazione](../throttling.md) | Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio. |