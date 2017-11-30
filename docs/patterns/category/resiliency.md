---
title: Modelli di resilienza
description: "La resilienza è la capacità di un sistema di gestire correttamente gli errori e il ripristino dagli errori. La natura dell'hosting nel cloud, dove le applicazioni sono spesso multi-tenant, usano servizi di piattaforma condivisi, si contendono le risorse e la larghezza di banda, comunicano tramite Internet e vengono eseguite su hardware apposito, implica una maggiore probabilità che si verifichino errori temporanei o permanenti. Per mantenere la resilienza è necessario poter rilevare gli errori e correggerli in modo rapido ed efficiente."
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: a3b9d72989e0de57c689bcec51e20653d0441d31
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="resiliency-patterns"></a>Modelli di resilienza

La resilienza è la capacità di un sistema di gestire correttamente gli errori e il ripristino dagli errori. La natura dell'hosting nel cloud, dove le applicazioni sono spesso multi-tenant, usano servizi di piattaforma condivisi, si contendono le risorse e la larghezza di banda, comunicano tramite Internet e vengono eseguite su hardware apposito, implica una maggiore probabilità che si verifichino errori temporanei o permanenti. Per mantenere la resilienza è necessario poter rilevare gli errori e correggerli in modo rapido ed efficiente.

| Modello | Riepilogo |
| ------- | ------- |
| [A scomparti](../bulkhead.md) | Isolare gli elementi di un'applicazione in pool in modo che un eventuale problema in uno dei componenti non blocchi il funzionamento degli altri componenti. |
| [Interruttore](../circuit-breaker.md) | Gestire gli errori la cui correzione potrebbe richiedere una quantità variabile di tempo in fase di connessione a una risorsa o a un servizio remoto. |
| [Transazione di compensazione](../compensating-transaction.md) | Annullare il lavoro eseguito da una serie di passaggi che insieme definiscono un'operazione coerente. |
| [Monitoraggio dell'integrità tramite endpoint](../health-endpoint-monitoring.md) | Implementare controlli funzionali all'interno di un'applicazione a cui gli strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari. |
| [Designazione leader](../leader-election.md) | Coordinare le azioni eseguite da una raccolta di istanze di attività di collaborazione in un'applicazione distribuita designando un'istanza come leader, con la responsabilità di gestire le altre istanze. |
| [Livellamento del carico basato sulle code](../queue-based-load-leveling.md) | Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti. |
| [Nuovo tentativo](../retry.md) | Abilitare un'applicazione per gestire gli errori temporanei previsti durante il tentativo di connessione a un servizio o a una risorsa di rete ritentando in modo trasparente un'operazione non riuscita in precedenza. |
| [Supervisione agente di pianificazione](../scheduler-agent-supervisor.md) | Coordinare un set di azioni in un set distribuito di servizi e altre risorse remote. |