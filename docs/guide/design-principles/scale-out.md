---
title: Progettare per la scalabilità orizzontale
titleSuffix: Azure Application Architecture Guide
description: Le applicazioni cloud devono essere progettate per la scalabilità orizzontale.
author: MikeWasson
ms.date: 08/30/2018
ms.custom: seojan19
ms.openlocfilehash: 9e8a36146c711fda2b03e00dfeb3554caf1fd5f0
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113060"
---
# <a name="design-to-scale-out"></a>Progettare per la scalabilità orizzontale

## <a name="design-your-application-so-that-it-can-scale-horizontally"></a>Progettare l'applicazione in modo che consenta la scalabilità orizzontale

Un importante vantaggio del cloud è la scalabilità elastica, ovvero la possibilità di usare la capacità necessaria, aumentandola quando aumenta il carico e riducendola quando la capacità aggiuntiva non è necessaria. Progettare l'applicazione in modo che consenta la scalabilità orizzontale, aggiungendo o rimuovendo nuove istanze in base alle esigenze.

## <a name="recommendations"></a>Consigli

**Evitare la persistenza delle istanze**. Si parla di persistenza, o *affinità di sessione*, quando le richieste dello stesso client vengono sempre instradate allo stesso server. La persistenza limita la capacità di scalabilità orizzontale dell'applicazione. Il traffico di un utente con volumi elevati, ad esempio, non viene distribuito tra più istanze. Tra le cause della persistenza ci sono lo stato sessione in memoria e l'uso di chiavi specifiche del computer per la crittografia. Assicurarsi che qualsiasi istanza possa gestire qualsiasi richiesta.

**Identificare i colli di bottiglia**. La scalabilità orizzontale non è una soluzione magica per ogni problema di prestazioni. Se, ad esempio, il database back-end rappresenta un collo di bottiglia, l'aggiunta di altri server Web non sarà utile. Identificare e risolvere i colli di bottiglia nel sistema prima di generare più istanze. Le parti con stato del sistema sono le cause più probabili dei colli di bottiglia.

**Scomporre i carichi di lavoro in base ai requisiti di scalabilità.**  Le applicazioni spesso sono costituite da più carichi di lavoro, con requisiti di scalabilità diversi. Un'applicazione potrebbe ad esempio avere un sito pubblico e un sito di amministrazione separato. Nel sito pubblico potrebbero verificarsi picchi improvvisi di traffico, mentre il carico del sito di amministrazione è minore e più prevedibile.

**Ripartire il carico delle attività a elevato utilizzo di risorse.** Le attività che richiedono molte risorse della CPU o di I/O devono essere spostate nei [processi in background][background-jobs], quando possibile, per ridurre al minimo il carico sul front-end che gestisce le richieste degli utenti.

**Usare le funzionalità di scalabilità automatica predefinite**. Molti servizi di calcolo di Azure offrono il supporto predefinito per la scalabilità automatica. Se l'applicazione ha un carico di lavoro prevedibile e regolare, è possibile applicare la scalabilità orizzontale in base a una pianificazione. Ad esempio, applicare la scalabilità orizzontale durante l'orario di ufficio. In caso contrario, se il carico di lavoro non è prevedibile, usare le metriche delle prestazioni, ad esempio relative a lunghezza della coda di richieste o CPU, per attivare la scalabilità automatica. Per le procedure consigliate relative alla scalabilità automatica, vedere [Scalabilità automatica][autoscaling].

**Prendere in considerazione la scalabilità automatica aggressiva per i carichi di lavoro critici**. Per i carichi di lavoro critici, è necessario anticipare le richieste. In condizioni di carico elevato, è preferibile aggiungere rapidamente nuove istanze per gestire il traffico aggiuntivo e quindi ridurle gradualmente.

**Progettare per la riduzione delle istanze**.  Tenere presente che con la scalabilità elastica, l'applicazione avrà periodi di riduzione delle risorse, in cui le istanze vengono rimosse. L'applicazione deve gestire correttamente le istanze rimosse. Ecco alcuni modi per gestire la riduzione delle istanze:

- Restare in ascolto degli eventi di arresto (quando disponibili) ed eseguire la chiusura normale.
- I client/consumer di un servizio devono supportare la gestione degli errori temporanei e i nuovi tentativi.
- Per le attività a esecuzione prolungata, prendere in considerazione la possibilità di suddividere il lavoro, usando checkpoint o il modello [Pipe e filtri][pipes-filters-pattern].
- Inserire gli elementi di lavoro in una coda, in modo che un'altra istanza possa gestire il lavoro se un'istanza viene rimossa durante l'elaborazione.

<!-- links -->

[autoscaling]: ../../best-practices/auto-scaling.md
[background-jobs]: ../../best-practices/background-jobs.md
[pipes-filters-pattern]: ../../patterns/pipes-and-filters.md
