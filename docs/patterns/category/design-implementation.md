---
title: Modelli di progettazione e implementazione
titleSuffix: Cloud Design Patterns
description: Una progettazione efficace comprende fattori come la coerenza e la logica nella progettazione e nella distribuzione dei componenti, la manutenibilità per semplificare l'amministrazione e lo sviluppo e la riusabilità per consentire l'uso di componenti e sottosistemi in altri scenari e applicazioni. Le decisioni prese in fase di progettazione e implementazione hanno un notevole impatto sulla qualità e sul costo totale di proprietà delle applicazioni e dei servizi ospitati nel cloud.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: ff609679bed4ee4aa88daf95ff1cab4ab2d90d48
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54482549"
---
# <a name="design-and-implementation-patterns"></a>Modelli di progettazione e implementazione

Una progettazione efficace comprende fattori come la coerenza e la logica nella progettazione e nella distribuzione dei componenti, la manutenibilità per semplificare l'amministrazione e lo sviluppo e la riusabilità per consentire l'uso di componenti e sottosistemi in altri scenari e applicazioni. Le decisioni prese in fase di progettazione e implementazione hanno un notevole impatto sulla qualità e sul costo totale di proprietà delle applicazioni e dei servizi ospitati nel cloud.

|                                Modello                                 |                                                                                                      Summary                                                                                                       |
|------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                     [Ambasciata](../ambassador.md)                     |                                                         Creare servizi helper che inviano richieste di rete per conto di un servizio consumer o di un'applicazione.                                                          |
|          [Livello antidanneggiamento](../anti-corruption-layer.md)          |                                                               Implementare un livello adapter o di interfaccia tra un'applicazione moderna e un sistema legacy.                                                                |
|         [Back-end per front-end](../backends-for-frontends.md)         |                                                          Creare servizi back-end separati che vengono utilizzati da interfacce o applicazioni front-end specifiche.                                                          |
|                           [CQRS](../cqrs.md)                           |                                                         Consente di segregare le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati attraverso l'utilizzo di interfacce separate.                                                         |
| [Consolidamento delle risorse di calcolo](../compute-resource-consolidation.md) |                                                                     Consolidare più attività o operazioni in un'unica unità di calcolo                                                                      |
|   [Archivio di configurazione esterno](../external-configuration-store.md)   |                                                        È possibile estrarre le informazioni di configurazione dal pacchetto di distribuzione dell'applicazione e spostarle in una posizione centralizzata.                                                         |
|            [Aggregazione gateway](../gateway-aggregation.md)            |                                                                   Usare un gateway per aggregare più richieste singole in un'unica richiesta.                                                                   |
|             [Offload del gateway](../gateway-offloading.md)             |                                                                      Eseguire l'offload delle funzionalità dei servizi condivise o specializzate in un proxy gateway.                                                                       |
|                [Routing del gateway](../gateway-routing.md)                |                                                                            Eseguire il routing delle richieste a più servizi usando un singolo endpoint.                                                                            |
|                [Designazione leader](../leader-election.md)                | Coordinare le azioni eseguite da una raccolta di istanze di attività di collaborazione in un'applicazione distribuita designando un'istanza come leader, con la responsabilità di gestire le altre istanze. |
|              [Pipe e filtri](../pipes-and-filters.md)              |                                                     Scomporre un'attività che esegue un'elaborazione complessa in una serie di elementi distinti riutilizzabili.                                                      |
|                        [Sidecar](../sidecar.md)                        |                                                  Distribuire i componenti di un'applicazione in un processo o in un contenitore separato per fornire isolamento e incapsulamento.                                                  |
|         [Hosting di contenuto statico](../static-content-hosting.md)         |                                                        Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client.                                                        |
|                      [Sostituzione](../strangler.md)                      |                                         Migrare in maniera incrementale un sistema legacy, sostituendo gradualmente parti specifiche di funzionalità con nuove applicazioni e servizi.                                          |
