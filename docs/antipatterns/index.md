---
title: Antipattern di prestazioni per le applicazioni cloud
titleSuffix: Azure Architecture Center
description: Procedure comuni che possono causare problemi di scalabilità.
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 212930368942728fc0be0c9b2af1a90293906b39
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58344836"
---
# <a name="performance-antipatterns-for-cloud-applications"></a>Antipattern di prestazioni per le applicazioni cloud

Un *antipattern di prestazioni* è una pratica comune che può causare problemi di scalabilità quando un'applicazione è sotto pressione.

Ecco uno scenario comune: un'applicazione si comporta correttamente durante i test delle prestazioni. Viene rilasciata nell'ambiente di produzione e inizia a gestire carichi di lavoro reali. A questo punto le sue prestazioni iniziano a diventare insufficienti &mdash;: rifiuta le richieste utente, si blocca o genera eccezioni. Il team di sviluppo deve rispondere a domande:

- Perché questo comportamento non si è manifestato durante il test?
- Come si può risolvere?

La risposta alla prima domanda è semplice. È molto difficile in un ambiente di test simulare utenti reali, i loro modelli di comportamento e i volumi di lavoro che potrebbero eseguire. L'unico modo completamente sicuro per comprendere il comportamento di un sistema sotto carico è osservare l'ambiente in produzione. Per chiarire, non si suggerisce di omettere i test delle prestazioni. I test delle prestazioni sono fondamentali per ottenere le metriche di prestazioni di base. Ma è necessario essere pronti osservare e correggere i problemi di prestazioni quando si verificano nel sistema in tempo reale.

La risposta alla seconda domanda, come risolvere il problema, è meno semplice. Ci sono molti fattori che possono contribuire e a volte il problema si manifesta solo in determinate circostanze. La strumentazione e la creazione di logo sono fondamentali per individuare la causa principale, ma è necessario anche sapere cosa cercare.

Insieme ai clienti di Microsoft Azure sono stati identificati alcuni dei problemi di prestazioni più comuni che i clienti riscontrano nell'ambiente di produzione. Per ogni antipattern sono indicati il motivo per cui l'antipattern si verifica in genere, i sintomi dell'antipattern e le tecniche per la risoluzione del problema. Viene fornito anche il codice di esempio che illustra sia l'antipattern sia la soluzione suggerita.

Alcuni di questi antipatterns potrebbero sembrare ovvi quando si leggono le descrizioni, ma si verificano più spesso di quanto si pensi. In alcuni casi un'applicazione eredita una progettazione che funzionava in locale, ma non è facilmente scalabile nel cloud. Oppure un'applicazione potrebbe iniziare con una progettazione molto pulita, ma con l'aggiunta di nuove funzionalità, potrebbe subentrare uno o più di questi antipattern. Indipendentemente dalla causa specifica, questa guida consentirà di identificare e correggere questi antipattern.

Ecco l'elenco degli antipattern che sono stati identificati:

| Antipattern | DESCRIZIONE |
|-------------|-------------|
| [Database occupato][BusyDatabase] | Offload di troppa elaborazione in un archivio dati. |
| [Front-end occupato][BusyFrontEnd] | Spostamento attività a intenso uso di risorse nei thread di background. |
| [I/O "frammentato"][ChattyIO] | Invio continuativo di molte richieste di rete di piccole dimensioni. |
| [Recupero estraneo][ExtraneousFetching] | Recupero di più dati del necessario, che determina un I/O inutile. |
| [Creazione di istanze non corretta][ImproperInstantiation] | Creazione e distruzione di oggetti che sono progettati per essere condivisi e riutilizzati. |
| [Persistenza monolitica][MonolithicPersistence] | Usare lo stesso archivio dati per dati con modelli di utilizzo molto diversi. |
| [Nessuna memorizzazione nella cache][NoCaching] | Impossibilità di memorizzare i dati nella cache. |
| [I/O sincrono][SynchronousIO] | Bloccare il thread chiamante durante il completamento dell'I/O. |

[BusyDatabase]: ./busy-database/index.md
[BusyFrontEnd]: ./busy-front-end/index.md
[ChattyIO]: ./chatty-io/index.md
[ExtraneousFetching]: ./extraneous-fetching/index.md
[ImproperInstantiation]: ./improper-instantiation/index.md
[MonolithicPersistence]: ./monolithic-persistence/index.md
[NoCaching]: ./no-caching/index.md
[SynchronousIO]: ./synchronous-io/index.md
