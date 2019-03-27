---
title: Modello di livello anti-danneggiamento
titleSuffix: Cloud Design Patterns
description: Implementare un'interfaccia o un livello adapter tra un'applicazione moderna e un sistema legacy.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: bec9fb1a2bd2ad8eab68d6fbf07bfa197e57cbf5
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248936"
---
# <a name="anti-corruption-layer-pattern"></a>Modello di livello anti-danneggiamento

Implementare un'interfaccia o un livello adapter tra sottosistemi diversi che non condividono la stessa semantica. Questo livello traduce le richieste inviate da un sottosistema all'altro. Usare questo modello per assicurarsi che la progettazione di un'applicazione non sia limitata a causa di dipendenze da sottosistemi esterni. Questo modello è stato descritto per la prima volta da Eric Evans in *Domain-Driven Design*.

## <a name="context-and-problem"></a>Contesto e problema

La maggior parte delle applicazioni si basa su altri sistemi per alcuni dati o funzionalità. Quando, ad esempio, si esegue la migrazione di un'applicazione legacy a un sistema moderno, potrebbero ancora essere necessarie risorse legacy esistenti. Le nuove funzionalità devono quindi poter chiamare il sistema legacy. Ciò vale soprattutto per le migrazioni graduali, in cui le diverse funzionalità di un'applicazione di grandi dimensioni vengono spostate in un sistema moderno nel corso del tempo.

Spesso questi sistemi legacy sono soggetti a problemi di qualità causati, ad esempio, da schemi di dati complessa o da API obsolete. Le funzionalità e le tecnologie usate nei sistemi legacy possono essere molto diverse rispetto ai sistemi più moderni. Per interagire con il sistema legacy, è possibile che la nuova applicazione debba supportare infrastruttura, protocolli, modelli di dati e API obsolete o altre funzionalità che diversamente non sarebbero incluse in un'applicazione moderna.

Per garantire l'accesso tra il nuovo sistema e quello legacy, è possibile che il nuovo sistema debba adeguarsi ad almeno alcune API o ad altra semantica del sistema legacy. Quando queste funzionalità legacy presentano problemi di qualità, supportarle significa danneggiare quella che, diversamente, sarebbe un'applicazione moderna dal design pulito.

Problemi analoghi possono sorgere con qualsiasi sistema esterno non gestito dal team di sviluppo, non solo con sistemi legacy.

## <a name="solution"></a>Soluzione

Isolare i diversi sottosistemi inserendo tra loro un livello anti-danneggiamento. Questo livello converte le comunicazioni tra i due sistemi, consentendo a un sistema di rimanere invariato ed evitando di compromettere il design e l'approccio tecnologico dell'altro.

![Diagramma del modello di livello anti-danneggiamento](./_images/anti-corruption-layer.png)

Il diagramma precedente mostra un'applicazione con due livelli intermedi. Il sottosistema A chiama il sottosistema B tramite un livello antidanneggiamento. Per le comunicazioni tra il sottosistema A e il livello antidanneggiamento vengono sempre usati l'architettura e il modello di dati del sottosistema A. Le chiamate dal livello antidanneggiamento al sottosistema B sono conformi al modello di dati o ai metodi di tale sottosistema. Il livello anti-danneggiamento contiene tutta la logica necessaria per la conversione tra i due sistemi. È possibile implementare il livello come componente all'interno dell'applicazione o come servizio indipendente.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

- Il livello anti-danneggiamento potrebbe aggiungere latenza alle chiamate effettuate tra i due sistemi.
- Il livello anti-danneggiamento aggiunge un altro servizio che deve essere gestito e mantenuto.
- Valutare la scalabilità del livello anti-danneggiamento.
- Valutare se sono necessari più livelli anti-danneggiamento. Potrebbe essere necessario scomporre la funzionalità in più servizi usando linguaggi o tecnologie diverse oppure potrebbero esserci altri motivi per partizionare il livello di anti-danneggiamento.
- Valutare il modo in cui il livello anti-danneggiamento verrà gestito in relazione ad altre applicazioni o altri servizi usati. Chiedersi in che modo verrà integrato nei processi esistenti di monitoraggio, rilascio e configurazione.
- Assicurarsi che la coerenza dei dati e delle transazioni venga mantenuta e possa essere monitorata.
- Valutare se il livello antidanneggiamento debba gestire tutte le comunicazioni tra sottosistemi diversi o solo un subset di funzionalità.
- Valutare se il livello antidanneggiamento debba far parte di una strategia di migrazione delle applicazioni, se sarà permanente o se verrà ritirato una volta conclusa la migrazione di tutte le funzionalità legacy.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello quando:

- È stata pianificata una migrazione in più fasi, ma è necessario mantenere l'integrazione tra il sistema legacy e quello nuovo.
- Due o più sottosistemi hanno semantiche diverse, ma devono comunque comunicare.

Questo modello potrebbe non essere appropriato se le differenze di semantica tra il sistema legacy e quello nuovo sono significative.

## <a name="related-guidance"></a>Informazioni correlate

- [Modello di sostituzione](./strangler.md)
