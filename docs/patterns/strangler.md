---
title: Modello Sostituzione
titleSuffix: Cloud Design Patterns
description: Migrare in maniera incrementale un sistema legacy, sostituendo gradualmente parti specifiche di funzionalità con nuove applicazioni e servizi.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: f139d368c98256c0190753930983a47df81a5134
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241452"
---
# <a name="strangler-pattern"></a>Modello Sostituzione

Migrare in maniera incrementale un sistema legacy, sostituendo gradualmente parti specifiche di funzionalità con nuove applicazioni e servizi. Mano a mano che le funzionalità del sistema precedente vengono sostituite, il nuovo sistema sostituisce tutte le funzionalità del sistema precedente fino a quando non è possibile effettuarne la completa dismissione.

## <a name="context-and-problem"></a>Contesto e problema

Con l'invecchiamento dei sistemi, gli strumenti di sviluppo, le tecnologie di hosting e perfino le architetture con cui sono stati compilati possono diventare obsoleti. Con l'aggiunta di nuove caratteristiche e funzionalità, la complessità delle applicazioni può aumentare notevolmente, rendendo i sistemi più difficili da gestire o ostacolando l'aggiunta di nuove funzionalità.

La sostituzione totale di un sistema complesso può rappresentare un impegno notevole. Spesso è necessario migrare con gradualità verso il nuovo sistema, conservando quello precedente per gestire le funzionalità di cui non è stata ancora eseguita la migrazione. Tuttavia, l'esecuzione di due versioni distinte di un'applicazione implica che i client sappiano dove si trovano le singole funzionalità. Ogni volta che viene eseguita la migrazione di una funzione o di un servizio, i client devono essere aggiornati affinché puntino alla nuova posizione.

## <a name="solution"></a>Soluzione

Sostituire gradualmente parti specifiche di funzionalità con nuove applicazioni e servizi. Creare un'interfaccia che intercetta le richieste inviate al sistema back-end legacy. L'interfaccia indirizza tali richieste all'applicazione legacy o ai nuovi servizi. Le funzionalità esistenti possono essere migrate nel nuovo sistema gradualmente, e i consumer possono continuare a usare la stessa interfaccia senza percepire l'avvenuta migrazione.

![Diagramma del modello Sostituzione](./_images/strangler.png)

Questo modello contribuisce a ridurre i rischi implicati dalla migrazione e a diluire l'attività di sviluppo nel tempo. Grazie all'interfaccia che indirizza gli utenti in modo sicuro all'applicazione corretta, è possibile aggiungere le funzionalità al nuovo sistema al ritmo preferito, assicurando al contempo il funzionamento continuo dell'applicazione legacy. Nel tempo, le funzionalità vengono migrate nel nuovo sistema e il sistema legacy viene sostituito fino a non essere più necessario. Una volta completato questo processo, sarà possibile ritirare il sistema legacy in modo sicuro.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

- Considerare la modalità di gestione di archivi dati e servizi che possono essere potenzialmente usati tanto dai sistemi legacy quanto da quelli nuovi. Verificare che entrambi possano accedere a queste risorse simultaneamente.
- Strutturare i nuovi servizi e le applicazioni in modo che possono essere facilmente intercettati e sostituiti durante le future migrazioni.
- Quando la migrazione sarà infine completata, l'interfaccia sostitutiva verrà eliminata o adattata ai client legacy.
- Verificare che l'interfaccia mantenga il passo con la migrazione.
- Verificare che l'interfaccia non diventi un singolo punto di guasto o un collo di bottiglia delle prestazioni.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello quando si migra gradualmente un'applicazione back-end in una nuova architettura.

Questo modello potrebbe non essere adatto nelle situazioni seguenti:

- Quando non è possibile intercettare le richieste inviate al sistema back-end.
- Per i sistemi di piccole dimensioni in cui la complessità della sostituzione su larga scala è bassa.

## <a name="related-guidance"></a>Informazioni correlate

- Post di blog di Martin Fowler su [StranglerApplication](https://www.martinfowler.com/bliki/StranglerApplication.html)
