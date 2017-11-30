---
title: Modelli di progettazione e implementazione
description: "Una progettazione efficace comprende fattori come la coerenza e la logica nella progettazione e nella distribuzione dei componenti, la manutenibilità per semplificare l'amministrazione e lo sviluppo e la riusabilità per consentire l'uso di componenti e sottosistemi in altri scenari e applicazioni. Le decisioni prese in fase di progettazione e implementazione hanno un notevole impatto sulla qualità e sul costo totale di proprietà delle applicazioni e dei servizi ospitati nel cloud."
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: 3d6e528b8c88c6fcc265d6425dcdae6fae5166fb
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="design-and-implementation-patterns"></a>Modelli di progettazione e implementazione

Una progettazione efficace comprende fattori come la coerenza e la logica nella progettazione e nella distribuzione dei componenti, la manutenibilità per semplificare l'amministrazione e lo sviluppo e la riusabilità per consentire l'uso di componenti e sottosistemi in altri scenari e applicazioni. Le decisioni prese in fase di progettazione e implementazione hanno un notevole impatto sulla qualità e sul costo totale di proprietà delle applicazioni e dei servizi ospitati nel cloud.

| Modello | Riepilogo |
| ------- | ------- |
| [Ambasciatore](../ambassador.md) | Creare servizi helper che inviano richieste di rete per conto di un'applicazione o un servizio consumer. |
| [Livello anti-danneggiamento](../anti-corruption-layer.md) | Implementare un livello adapter o di interfaccia tra un'applicazione moderna e un sistema legacy. |
| [Back-end per front-end](../backends-for-frontends.md) | Creare servizi back-end separati che vengono utilizzati da interfacce o applicazioni front-end specifiche. |
| [CQRS](../cqrs.md) | Segregare le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati attraverso l'utilizzo di interfacce separate. |
| [Consolidamento delle risorse di calcolo](../compute-resource-consolidation.md) | Consolidare più attività o operazioni in un'unica unità di calcolo |
| [Archivio di configurazione esterno](../external-configuration-store.md) | Estrarre le informazioni di configurazione dal pacchetto di distribuzione dell'applicazione e spostarle in una posizione centralizzata. |
| [Aggregazione gateway](../gateway-aggregation.md) | Usare un gateway per aggregare più richieste singole in un'unica richiesta. |
| [Offload gateway](../gateway-offloading.md) | Eseguire l'offload delle funzionalità dei servizi condivise o specializzate in un proxy gateway. |
| [Routing gateway](../gateway-routing.md) | Eseguire il routing delle richieste a più servizi usando un singolo endpoint. |
| [Designazione leader](../leader-election.md) | Coordinare le azioni eseguite da una raccolta di istanze di attività di collaborazione in un'applicazione distribuita designando un'istanza come leader, con la responsabilità di gestire le altre istanze. |
| [Pipe e filtri](../pipes-and-filters.md) | Scomporre un'attività che esegue un'elaborazione complessa in una serie di elementi distinti riutilizzabili. |
| [Sidecar](../sidecar.md) | Distribuire i componenti di un'applicazione in un processo o in un contenitore separato per fornire isolamento e incapsulamento. |
| [Hosting di contenuto statico](../static-content-hosting.md) | Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client. |
| [Sostituzione](../strangler.md) | Eseguire la migrazione incrementale di un sistema legacy sostituendo gradualmente funzionalità specifiche con nuovi servizi e applicazioni. |