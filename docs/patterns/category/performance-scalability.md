---
title: Modelli di prestazioni e scalabilità
titleSuffix: Cloud Design Patterns
description: Le prestazioni sono un'indicazione della velocità di risposta con cui un sistema esegue qualsiasi azione all'interno di un determinato intervallo di tempo, mentre la scalabilità è la capacità di un sistema di gestire gli aumenti del carico senza influire sulle prestazioni o di aumentare facilmente le risorse disponibili. In genere le applicazioni cloud hanno carichi di lavoro e picchi di attività variabili. La previsione di questi aspetti, soprattutto in uno scenario multi-tenant, è quasi impossibile. Le applicazioni, invece, devono essere in grado di aumentare le risorse entro i limiti per soddisfare i picchi di domanda e di ridurle quando la domanda diminuisce. La scalabilità non riguarda solo le istanze di calcolo, ma anche altri elementi, come l'archiviazione dei dati, l'infrastruttura di messaggistica e altro.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: a57194f70218a8294507bd389ea9526660e3041b
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54480726"
---
# <a name="performance-and-scalability-patterns"></a>Modelli di prestazioni e scalabilità

[!INCLUDE [header](../../_includes/header.md)]

Le prestazioni sono un'indicazione della velocità di risposta con cui un sistema esegue qualsiasi azione all'interno di un determinato intervallo di tempo, mentre la scalabilità è la capacità di un sistema di gestire gli aumenti del carico senza influire sulle prestazioni o di aumentare facilmente le risorse disponibili. In genere le applicazioni cloud hanno carichi di lavoro e picchi di attività variabili. La previsione di questi aspetti, soprattutto in uno scenario multi-tenant, è quasi impossibile. Le applicazioni, invece, devono essere in grado di aumentare le risorse entro i limiti per soddisfare i picchi di domanda e di ridurle quando la domanda diminuisce. La scalabilità non riguarda solo le istanze di calcolo, ma anche altri elementi, come l'archiviazione dei dati, l'infrastruttura di messaggistica e altro.

|                           Modello                            |                                                                        Summary                                                                         |
|--------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
|               [Cache-aside](../cache-aside.md)               |                                                   Caricare i dati su richiesta in una cache da un archivio dati                                                   |
|                      [CQRS](../cqrs.md)                      |                           Consente di segregare le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati attraverso l'utilizzo di interfacce separate.                           |
|            [Origine eventi](../event-sourcing.md)            |                     Usare un archivio di solo accodamento per registrare la serie completa di eventi che descrivono le azioni eseguite sui dati di un dominio.                      |
|               [Tabella degli indici](../index-table.md)               |                                Creare indici sui campi negli archivi dati spesso referenziati dalle query.                                |
|         [Vista materializzata](../materialized-view.md)         |       Generare viste prepopolate sui dati in uno o più archivi dati quando i dati non sono formattati in modo ideale per le operazioni di query necessarie.        |
|            [Coda di priorità](../priority-queue.md)            | Assegnare una priorità alle richieste inviate ai servizi in modo che le richieste con una priorità più alta vengano ricevute ed elaborate più rapidamente rispetto a quelle con priorità più bassa. |
| [Livellamento del carico basato sulle code](../queue-based-load-leveling.md) |              Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti.               |
|                  [Partizionamento orizzontale](../sharding.md)                  |                                           Dividere un archivio dati in un set di partizioni orizzontali.                                           |
|    [Hosting di contenuto statico](../static-content-hosting.md)    |                          Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client.                          |
|                [Limitazione](../throttling.md)                |                Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio.                 |
