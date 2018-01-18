---
title: Modello di livello anti-danneggiamento
description: Implementare un'interfaccia o un livello adapter tra un'applicazione moderna e un sistema legacy.
author: dragon119
ms.date: 06/23/2017
ms.openlocfilehash: e41f080abbef772596ee7f8b10ad72bb03a3b829
ms.sourcegitcommit: c93f1b210b3deff17cc969fb66133bc6399cfd10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="anti-corruption-layer-pattern"></a>Modello di livello anti-danneggiamento

Implementare un livello adapter o di interfaccia tra un'applicazione moderna e un sistema legacy da cui dipende. Questo livello converte le richieste tra l'applicazione moderna e il sistema legacy. Usare questo modello per assicurarsi che la progettazione di un'applicazione non sia limitata a causa di dipendenze da sistemi legacy. Questo modello è stato descritto per la prima volta da Eric Evans in *Domain-Driven Design*.

## <a name="context-and-problem"></a>Contesto e problema

La maggior parte delle applicazioni si basa su altri sistemi per alcuni dati o funzionalità. Quando, ad esempio, si esegue la migrazione di un'applicazione legacy a un sistema moderno, potrebbero ancora essere necessarie risorse legacy esistenti. Le nuove funzionalità devono quindi poter chiamare il sistema legacy. Ciò vale soprattutto per le migrazioni graduali, in cui le diverse funzionalità di un'applicazione di grandi dimensioni vengono spostate in un sistema moderno nel corso del tempo.

Spesso questi sistemi legacy sono soggetti a problemi di qualità causati, ad esempio, da schemi di dati complessa o da API obsolete. Le funzionalità e le tecnologie usate nei sistemi legacy possono essere molto diverse rispetto ai sistemi più moderni. Per interagire con il sistema legacy, è possibile che la nuova applicazione debba supportare infrastruttura, protocolli, modelli di dati e API obsolete o altre funzionalità che diversamente non sarebbero incluse in un'applicazione moderna.

Per garantire l'accesso tra il nuovo sistema e quello legacy, è possibile che il nuovo sistema debba adeguarsi ad almeno alcune API o ad altra semantica del sistema legacy. Quando queste funzionalità legacy presentano problemi di qualità, supportarle significa danneggiare quella che, diversamente, sarebbe un'applicazione moderna dal design pulito. 

## <a name="solution"></a>Soluzione

Isolare i sistemi legacy e moderni, inserendo tra loro un livello anti-danneggiamento. Questo livello converte le comunicazioni tra i due sistemi, consentendo al sistema legacy di rimanere invariato ed evitando di compromettere il design e l'approccio tecnologico dell'applicazione moderna.

![](./_images/anti-corruption-layer.png) 

Per le comunicazioni tra l'applicazione moderna e il livello anti-danneggiamento vengono sempre usati l'architettura e il modello di dati dell'applicazione. Le chiamate dal livello anti-danneggiamento al sistema legacy sono conformi al modello di dati o ai metodi di tale sistema. Il livello anti-danneggiamento contiene tutta la logica necessaria per la conversione tra i due sistemi. È possibile implementare il livello come componente all'interno dell'applicazione o come servizio indipendente.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

- Il livello anti-danneggiamento potrebbe aggiungere latenza alle chiamate effettuate tra i due sistemi.
- Il livello anti-danneggiamento aggiunge un altro servizio che deve essere gestito e mantenuto.
- Valutare la scalabilità del livello anti-danneggiamento.
- Valutare se sono necessari più livelli anti-danneggiamento. Potrebbe essere necessario scomporre la funzionalità in più servizi usando linguaggi o tecnologie diverse oppure potrebbero esserci altri motivi per partizionare il livello di anti-danneggiamento.
- Valutare il modo in cui il livello anti-danneggiamento verrà gestito in relazione ad altre applicazioni o altri servizi usati. Chiedersi in che modo verrà integrato nei processi esistenti di monitoraggio, rilascio e configurazione.
- Assicurarsi che la coerenza dei dati e delle transazioni venga mantenuta e possa essere monitorata.
- Valutare se il livello anti-danneggiamento deve gestire tutte le comunicazioni tra sistemi legacy e moderni o solo un subset di funzionalità. 
- Valutare se il livello anti-danneggiamento deve essere permanente o se verrà ritirato una volta conclusa la migrazione di tutte le funzionalità legacy.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello quando:

- È stata pianificata una migrazione in più fasi, ma è necessario mantenere l'integrazione tra il sistema legacy e quello nuovo.
- Il nuovo sistema e quello legacy usano semantiche diverse, ma devono comunque comunicare.

Questo modello potrebbe non essere appropriato se le differenze di semantica tra il sistema legacy e quello nuovo sono significative. 

## <a name="related-guidance"></a>Informazioni correlate

- [Modello di sostituzione][strangler]

[strangler]: ./strangler.md
