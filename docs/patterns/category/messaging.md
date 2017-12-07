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
# <a name="messaging-patterns"></a>Modelli di messaggistica

[!INCLUDE [header](../../_includes/header.md)]

La tipologia distribuita delle applicazioni cloud richiede un'infrastruttura di messaggistica in grado di connettere componenti e servizi, idealmente con un approccio "a regime di controllo libero" per ottimizzare la scalabilità. La messaggistica asincrona è ampiamente usata e, pur offrendo numerosi vantaggi, comporta anche sfide come l'ordinamento dei messaggi, la gestione dei messaggi non elaborabili, l'idempotenza e altro ancora.

| Modello | Riepilogo |
| ------- | ------- |
| [Consumer concorrenti](../competing-consumers.md) | Consentire a più consumer concorrenti di elaborare i messaggi ricevuti sullo stesso canale di messaggistica. |
| [Pipe e filtri](../pipes-and-filters.md) | Scomporre un'attività che esegue un'elaborazione complessa in una serie di elementi distinti riutilizzabili. |
| [Coda di priorità](../priority-queue.md) | Assegnare una priorità alle richieste inviate ai servizi in modo che le richieste con una priorità più alta vengano ricevute ed elaborate più rapidamente rispetto a quelle con priorità più bassa. |
| [Livellamento del carico basato sulle code](../queue-based-load-leveling.md) | Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi elevati intermittenti. |
| [Supervisione agente di pianificazione](../scheduler-agent-supervisor.md) | Coordinare un set di azioni in un set distribuito di servizi e altre risorse remote. |