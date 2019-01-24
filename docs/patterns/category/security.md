---
title: Modelli di protezione
titleSuffix: Cloud Design Patterns
description: La sicurezza è la capacità di un sistema di impedire azioni dannose o accidentali al di fuori dell'utilizzo designato e di impedire la divulgazione o la perdita di informazioni. Le applicazioni cloud sono esposte in Internet fuori dai limiti locali attendibili, spesso sono aperte al pubblico e possono essere usate da utenti non attendibili. Le applicazioni devono essere progettate e distribuite in modo da proteggerle da attacchi dannosi, limitare l'accesso solo agli utenti approvati e proteggere i dati sensibili.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: c18255dccdbd8659705884a4141f5ecd099d9ece
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54482086"
---
# <a name="security-patterns"></a>Modelli di protezione

[!INCLUDE [header](../../_includes/header.md)]

La sicurezza è la capacità di un sistema di impedire azioni dannose o accidentali al di fuori dell'utilizzo designato e di impedire la divulgazione o la perdita di informazioni. Le applicazioni cloud sono esposte in Internet fuori dai limiti locali attendibili, spesso sono aperte al pubblico e possono essere usate da utenti non attendibili. Le applicazioni devono essere progettate e distribuite in modo da proteggerle da attacchi dannosi, limitare l'accesso solo agli utenti approvati e proteggere i dati sensibili.

|                    Modello                     |                                                                                                         Summary                                                                                                         |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Identità federativa](../federated-identity.md) |                                                                                È possibile delegare l'autenticazione a un provider di identità esterno.                                                                                |
|         [Gatekeeper](../gatekeeper.md)         | Proteggere le applicazioni e i servizi usando un'istanza host dedicata che funga da broker tra i client e l'applicazione o il servizio, convalidi e purifichi le richieste e passi le richieste e i dati tra di essi. |
|          [Passepartout](../valet-key.md)          |                                                        Usare un token o una chiave che fornisca ai client l'accesso diretto limitato a una specifica risorsa o a un servizio.                                                        |
