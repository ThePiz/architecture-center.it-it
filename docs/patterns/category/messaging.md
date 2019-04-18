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
# <a name="messaging-patterns"></a>Modelli di messaggistica

La tipologia distribuita delle applicazioni cloud richiede un'infrastruttura di messaggistica in grado di connettere componenti e servizi, idealmente con un approccio "a regime di controllo libero" per ottimizzare la scalabilità. La messaggistica asincrona è ampiamente usata e, pur offrendo numerosi vantaggi, comporta anche sfide come l'ordinamento dei messaggi, la gestione dei messaggi non elaborabili, l'idempotenza e altro ancora.

| Modello | Summary |
| ------- | ------- |
| [Scontrino](../claim-check.md) | Dividere un messaggio di grandi dimensioni in uno scontrino e un payload per evitare di sovraccaricare il bus dei messaggi. |
| [Consumer concorrenti](../competing-consumers.md) | Consentire a più consumer concorrenti di elaborare i messaggi ricevuti sullo stesso canale di messaggistica. |
| [Pipe e filtri](../pipes-and-filters.md) | Scomporre un'attività che esegue un'elaborazione complessa in una serie di elementi distinti riutilizzabili. |
| [Coda di priorità](../priority-queue.md) | Assegnare una priorità alle richieste inviate ai servizi in modo che le richieste con una priorità più alta vengano ricevute ed elaborate più rapidamente rispetto a quelle con priorità più bassa. |
| [Server di pubblicazione - Sottoscrittore](../publisher-subscriber.md) | Abilitare un'applicazione a cui annunciare gli eventi a più consumer interessate in modo asincrono, senza l'accoppiamento tra i mittenti ai destinatari. |
| [Livellamento del carico basato sulle code](../queue-based-load-leveling.md) | Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi elevati intermittenti. |
| [Supervisione agente di pianificazione](../scheduler-agent-supervisor.md) | Coordinare un set di azioni in un set distribuito di servizi e altre risorse remote. |
