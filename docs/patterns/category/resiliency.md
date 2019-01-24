---
title: Modelli di resilienza
titleSuffix: Cloud Design Patterns
description: La resilienza è la capacità di un sistema di gestire correttamente gli errori e il ripristino dagli errori. La natura dell'hosting nel cloud, dove le applicazioni sono spesso multi-tenant, usano servizi di piattaforma condivisi, si contendono le risorse e la larghezza di banda, comunicano tramite Internet e vengono eseguite su hardware apposito, implica una maggiore probabilità che si verifichino errori temporanei o permanenti. Per mantenere la resilienza è necessario poter rilevare gli errori e correggerli in modo rapido ed efficiente.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 5863cf9491434e2ddd683178591aff09c40e7315
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481206"
---
# <a name="resiliency-patterns"></a>Modelli di resilienza

La resilienza è la capacità di un sistema di gestire correttamente gli errori e il ripristino dagli errori. La natura dell'hosting nel cloud, dove le applicazioni sono spesso multi-tenant, usano servizi di piattaforma condivisi, si contendono le risorse e la larghezza di banda, comunicano tramite Internet e vengono eseguite su hardware apposito, implica una maggiore probabilità che si verifichino errori temporanei o permanenti. Per mantenere la resilienza è necessario poter rilevare gli errori e correggerli in modo rapido ed efficiente.

|                            Modello                             |                                                                                                      Summary                                                                                                       |
|----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                   [A scomparti](../bulkhead.md)                   |                                                     Isolare gli elementi di un'applicazione in pool in modo che un eventuale problema in uno dei componenti non blocchi il funzionamento degli altri componenti.                                                      |
|            [Interruttore](../circuit-breaker.md)            |                                                  Gestire gli errori la cui correzione potrebbe richiedere una quantità variabile di tempo in fase di connessione a una risorsa o a un servizio remoto.                                                   |
|   [Transazione di compensazione](../compensating-transaction.md)   |                                                      Annullare il lavoro eseguito da una serie di passaggi che insieme definiscono un'operazione coerente.                                                       |
| [Monitoraggio endpoint di integrità](../health-endpoint-monitoring.md) |                                            Implementare controlli funzionali all'interno di un'applicazione a cui gli strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari.                                            |
|            [Designazione leader](../leader-election.md)            | Coordinare le azioni eseguite da una raccolta di istanze di attività di collaborazione in un'applicazione distribuita designando un'istanza come leader, con la responsabilità di gestire le altre istanze. |
|  [Livellamento del carico basato sulle code](../queue-based-load-leveling.md)  |                                            Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti.                                             |
|                      [Nuovo tentativo](../retry.md)                      |             È possibile abilitare un'applicazione per gestire gli errori temporanei previsti durante il tentativo di connessione a un servizio o a una risorsa di rete ritentando in modo trasparente un'operazione non riuscita in precedenza.             |
| [Supervisione agente di pianificazione](../scheduler-agent-supervisor.md) |                                                            Coordinare un set di azioni in un set distribuito di servizi e altre risorse remote.                                                            |