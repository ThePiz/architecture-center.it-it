---
title: 'Migrazione dei mainframe: Falsi miti e fatti'
description: Informazioni su come eseguire la migrazione di applicazioni da ambienti mainframe ad Azure, un'infrastruttura scalabile, collaudata e a disponibilità elevata per i sistemi attualmente in esecuzione su mainframe.
author: njray
ms.date: 12/27/2018
ms.openlocfilehash: bcad01ec044d2d802b055e328a9496aae7b33311
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901472"
---
# <a name="mainframe-myths-and-facts"></a>Falsi miti e fatti sui mainframe

I mainframe hanno sempre rivestito una posizione di rilievo nella storia dell'informatica e restano una valida soluzione per carichi di lavoro estremamente specifici. La maggior parte degli utenti considera il mainframe una piattaforma collaudata con procedure operative consolidate che lo rendono un ambiente solido e affidabile. Il software viene eseguito in base all'utilizzo e misurato in milioni di istruzioni al secondo (MIP). Sono inoltre disponibili report di utilizzo dettagliato per operazioni di chargeback.

L'affidabilità, la disponibilità e la potenza di elaborazione dei mainframe hanno tuttavia adottato proporzioni quasi mitiche. Per valutare i carichi di lavoro mainframe più adatti per Azure, è necessario prima distinguere i falsi miti dalla realtà.

## <a name="myth-mainframes-never-go-down-and-have-a-minimum-of-five-9s-of-availability"></a>Falso mito: i mainframe non hanno mai periodi di inattività e garantiscono una disponibilità pari al 99,999%

L'hardware e i sistemi operativi dei mainframe vengono considerati sempre stabili e affidabili. In realtà, tuttavia, è necessario pianificare periodi di inattività per interventi di manutenzione e operazioni di riavvio (denominate IPL: Initial Program Load). Quando vengono effettuate queste attività, una soluzione mainframe si attesta su una disponibilità di circa il 99-99,9%, pari a quella dei server Intel di fascia alta.

I mainframe, inoltre, rimangono vulnerabili alle situazioni di emergenza come qualsiasi altro server e richiedono un gruppo di continuità (UPS) per gestire questi tipi di eventi.

## <a name="myth-mainframes-have-limitless-scalability"></a>Falso mito: i mainframe hanno una scalabilità illimitata

La scalabilità di un mainframe dipende dalla capacità del relativo software di sistema, come il Customer Information Control System (CICS), e dalla capacità delle nuove istanze di motori e risorse di archiviazione del mainframe. Alcune grandi aziende che usano sistemi mainframe hanno personalizzato il software CICS per aumentarne le prestazioni, superando la capacità dei più grandi mainframe attualmente disponibili.

## <a name="myth-intel-based-servers-are-not-as-powerful-as-mainframes"></a>Falso mito: i server basati su Intel non sono potenti come i mainframe

I nuovi sistemi basati su Intel ad alta densità di core hanno una capacità di elaborazione equivalente a quella dei mainframe.

## <a name="myth-the-cloud-cannot-accommodate-mission-critical-applications-for-large-companies-such-as-financial-institutions"></a>Falso mito: il cloud non può supportare applicazioni mission-critical per aziende di grandi dimensioni come gli istituti finanziari

Esistono dei casi isolati in cui le soluzioni cloud non sono sufficienti, ma questo si verifica quando gli algoritmi di applicazione non possono essere distribuiti. Questi rari esempi costituiscono l'eccezione, non la regola.

## <a name="summary"></a>Riepilogo

Azure offre una piattaforma alternativa ai sistemi mainframe, in grado di offrire caratteristiche e funzionalità equivalenti a costi notevolmente inferiori. Il costo totale di proprietà offerto dal modello di costi del cloud, basato su sottoscrizione e sull'utilizzo effettivo, è notevolmente inferiore a quello dei computer mainframe.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Transizione dai mainframe ad Azure](migration-strategies.md)
