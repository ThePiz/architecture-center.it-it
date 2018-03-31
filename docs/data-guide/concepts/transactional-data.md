---
title: Dati transazionali
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: b7fdbb403d2a438ebc59e40ef58ed8067489dddc
ms.sourcegitcommit: 943e671a8d522cef5ddc8c6e04848134b03c2de4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="transactional-data"></a>Dati transazionali

I dati transazionali sono informazioni che tengono traccia delle interazioni correlate alle attività di un'organizzazione. Tali interazioni sono in genere transazioni aziendali, ad esempio pagamenti ricevuti dai clienti, pagamenti effettuati ai fornitori, movimentazione di prodotti dell'inventario, elaborazione di ordini o fornitura di servizi. Gli eventi transazionali, che rappresentano le transazioni stesse, in genere contengono una dimensione temporale, alcuni valori numerici e riferimenti ad altri dati. 

In genere, le transazioni devono essere *atomiche* e *coerenti*. L'atomicità indica che un'intera transazione ha sempre esito positivo o negativo come unità di lavoro e non viene mai lasciata in uno stato parziale. Se una transazione non può essere completata, il sistema di database deve eseguire il rollback di eventuali passaggi già eseguiti come parte di tale transazione. In un sistema di gestione di database relazionali (RDBMS) tradizionale, il rollback viene eseguito automaticamente se una transazione non può essere completata. La coerenza indica che le transazioni lasciano sempre i dati in uno stato valido. Queste sono descrizioni molto informali dei concetti di atomicità e coerenza. Sono tuttavia disponibili definizioni più formali di queste proprietà, ad esempio [ACID](https://en.wikipedia.org/wiki/ACID).

I database transazionali possono supportare la coerenza assoluta per le transazioni che usano diverse strategie di blocco, ad esempio il blocco pessimistico, per garantire che tutti i dati siano fortemente coerenti nel contesto aziendale per tutti gli utenti e i processi. 

L'architettura di distribuzione più comune che usa i dati transazionali è il livello di archivio dati in un'architettura a 3 livelli. Un'architettura a 3 livelli in genere è costituita da livello di presentazione, livello di logica di business e livello di archivio dati. Un'architettura di distribuzione correlata è l'architettura [a più livelli](/azure/architecture/guide/architecture-styles/n-tier), che può avere più livelli intermedi che gestiscono la logica di business.

![Esempio di un'applicazione a 3 livelli](./images/three-tier-application.png)

## <a name="typical-traits-of-transactional-data"></a>Caratteristiche tipiche dei dati transazionali

I dati transazionali hanno, in genere, le caratteristiche seguenti:

| Requisito | DESCRIZIONE |
| --- | --- |
| Normalizzazione | Normalizzazione elevata |
| SCHEMA | Schema durante la scrittura, fortemente applicato|
| Consistency | Coerenza assoluta, garanzia ACID |
| Integrità | Integrità elevata |
| Uso delle transazioni | Sì |
| Strategia di blocco | Ottimistica o pessimistica|
| Aggiornabile | Sì |
| Accodamento | Sì |
| Carico di lavoro | Operazioni di scrittura intense e lettura moderate |
| Indicizzazione | Indici primari e secondari |
| Dimensioni dato | Da piccole a medie |
| Modello | Relazionale |
| Forma dei dati | Tabulare |
| Flessibilità query | Flessibilità elevata |
| Scalabilità | Da piccole (MB) a grandi (alcuni TB) dimensioni | 

## <a name="see-also"></a>Vedere anche

[OLTP (Online Transaction Processing)](../scenarios/online-transaction-processing.md)
