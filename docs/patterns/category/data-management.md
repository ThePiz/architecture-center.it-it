---
title: Modelli di gestione dei dati
titleSuffix: Cloud Design Patterns
description: La gestione dei dati è l'elemento principale delle applicazioni cloud e influenza la maggior parte degli attributi di qualità. In genere i dati risiedono in percorsi diversi e sono dislocati tra più server per motivi di prestazioni, scalabilità o disponibilità e ciò può implicare una serie di sfide. Ad esempio, è necessario mantenere la coerenza dei dati e sincronizzare in genere i dati tra posizioni diverse.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 25571a431836656856ed3f299455dfdb94ae3477
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243052"
---
# <a name="data-management-patterns"></a>Modelli di gestione dei dati

[!INCLUDE [header](../../_includes/header.md)]

La gestione dei dati è l'elemento principale delle applicazioni cloud e influenza la maggior parte degli attributi di qualità. In genere i dati risiedono in percorsi diversi e sono dislocati tra più server per motivi di prestazioni, scalabilità o disponibilità e ciò può implicare una serie di sfide. Ad esempio, è necessario mantenere la coerenza dei dati e sincronizzare in genere i dati tra posizioni diverse.

|                        Modello                         |                                                                  Summary                                                                  |
|--------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
|            [Cache-aside](../cache-aside.md)            |                                            Caricare i dati su richiesta in una cache da un archivio dati                                             |
|                   [CQRS](../cqrs.md)                   |                    Consente di segregare le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati attraverso l'utilizzo di interfacce separate.                     |
|         [Origine eventi](../event-sourcing.md)         |               Usare un archivio di solo accodamento per registrare la serie completa di eventi che descrivono le azioni eseguite sui dati di un dominio.               |
|            [Tabella degli indici](../index-table.md)            |                         Creare indici sui campi negli archivi dati spesso referenziati dalle query.                          |
|      [Vista materializzata](../materialized-view.md)      | Generare viste prepopolate sui dati in uno o più archivi dati quando i dati non sono formattati in modo ideale per le operazioni di query necessarie. |
|               [Partizionamento orizzontale](../sharding.md)               |                                    Dividere un archivio dati in un set di partizioni orizzontali.                                     |
| [Hosting di contenuto statico](../static-content-hosting.md) |                   Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client.                    |
|              [Passepartout](../valet-key.md)              |                 Usare un token o una chiave che fornisca ai client l'accesso diretto limitato a una specifica risorsa o a un servizio.                 |
