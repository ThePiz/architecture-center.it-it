---
title: Definizione dei requisiti per applicazioni Azure resilienti
description: Panoramica della raccolta dei requisiti di disponibilità e resilienza per le applicazioni Azure
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: 2af2ce4200c285abfda1395862d21efc3c17e577
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646629"
---
# <a name="developing-requirements-for-resilient-azure-applications"></a>Sviluppo di requisiti per applicazioni Azure resilienti

Creazione *resilienza* (ripristino da errori) e *disponibilità* (in esecuzione in uno stato integro senza tempi di inattività significativi) nelle tue App inizia con la raccolta di requisiti. Ad esempio, quanto tempo di inattività è accettabile? Quanto costa potenziali tempi di inattività sul tuo business? Quali sono i requisiti di disponibilità del tuo cliente? Quanto investire per rendere l'applicazione a disponibilità elevata? Che cos'è il rischio e il costo?

## <a name="identify-distinct-workloads"></a>Identificare i carichi di lavoro distinti

Soluzioni cloud in genere costituiti da più applicazioni *carichi di lavoro*. Un carico di lavoro è una funzionalità distinte o attività che è logicamente separata da altre attività in termini di requisiti di archiviazione per la logica e i dati aziendali. Un'applicazione di e-commerce, ad esempio, potrebbe essere carichi di lavoro seguenti:

- Esplorare e cercare un catalogo di prodotti.
- Creare e tenere traccia degli ordini.
- Visualizzare le raccomandazioni.

Ogni carico di lavoro ha requisiti diversi per la disponibilità, scalabilità, coerenza dei dati e il ripristino di emergenza. Prendere le decisioni aziendali mediante il bilanciamento tra costi e rischi per ogni carico di lavoro.

Anche scomporre i carichi di lavoro da obiettivo del livello di servizio. Se un servizio è composto da carichi di lavoro critici e meno critici, gestirli in modo diverso e specificare le funzionalità del servizio e il numero di istanze necessarie per soddisfare i requisiti di disponibilità.

## <a name="plan-for-usage-patterns"></a>Piano per i modelli di utilizzo

Identificare le differenze nei requisiti durante i periodi critici e non critici. esistono determinati periodi critici in cui il sistema deve essere disponibile? Ad esempio, un'applicazione di redditi non può non riuscire durante un'entro la scadenza e un servizio di streaming di video non deve rimanere durante un evento live. In queste situazioni, valutare il costo contro il rischio.

- Per garantire tempi di attività e soddisfare i contratti di servizio (SLA) in periodi critici, pianificare la ridondanza tra aree diverse nel caso in cui uno ha esito negativo, anche se ha un costo maggiore.
- Viceversa, durante i periodi non critico, eseguire l'applicazione in un'unica area per ridurre al minimo i costi.
- In alcuni casi, è possibile ridurre le spese aggiuntive usando tecniche senza server moderne con fatturazione in base al consumo.

## <a name="establish-availability-and-recovery-metrics"></a>Stabilire le metriche di disponibilità e ripristino

Creare i numeri di riferimento per i due set di metriche nel quadro del processo di requisiti. Il primo set aiuta a determinare la posizione in cui aggiungere ridondanza a servizi cloud e che i contratti di servizio per offrire ai clienti. Il secondo set aiuta a che pianificare il ripristino di emergenza.

### <a name="availability-metrics"></a>Metriche di disponibilità

Utilizzare queste misure per pianificare la ridondanza e determinare i contratti di servizio clienti.

- **Tempo medio di recupero (MTTR)** è il tempo medio impiegato per ripristinare un componente dopo un errore.
- **Tempo medio tra errori (MTBF)** è come lungo un componente ragionevolmente possibile prevedere per ultimi tra interruzioni.

### <a name="recovery-metrics"></a>Metriche di ripristino

Tali valori derivano da condurre una valutazione dei rischi e assicurarsi di che comprendere il costo e rischio di tempi di inattività e perdita di dati. Questi sono i requisiti non funzionali di un sistema e dipende dai requisiti aziendali.

- **Obiettivo tempo di ripristino (RTO)** è il tempo massimo accettabile un'applicazione non è disponibile dopo un evento imprevisto.
- **Obiettivo del punto di ripristino (RPO)** è la durata massima di perdita di dati che è accettabile durante un'emergenza.

Se il valore MTTR *qualsiasi* componente fondamentale in una configurazione a disponibilità elevata supera l'obiettivo RTO di sistema, un errore di sistema può causare un'interruzione delle attività inaccettabile. Vale a dire, è possibile ripristinare il sistema entro l'obiettivo RTO definito.

## <a name="determine-workload-availability-targets"></a>Determinare obiettivi di disponibilità del carico di lavoro

Definire la propria destinazione i contratti di servizio per ogni carico di lavoro nella soluzione in modo che è possibile determinare se l'architettura soddisfi i requisiti aziendali.

### <a name="consider-cost-and-complexity"></a>Prendere in considerazione la complessità e costi

Tutti gli elementi parità, una disponibilità più elevata è migliore. Tuttavia, quando si cercano altre nove, aumentare i costi e complessità. Un tempo di attività pari al 99,99% si traduce in circa cinque minuti di inattività totale al mese. Vale la pena la maggiore complessità e costi per raggiungere cinque nove? La risposta dipende dai requisiti aziendali.

Di seguito sono riportate alcune altre considerazioni che emergono quando si definisce un contratto di servizio:

- Per ottenere una disponibilità del 99,99 (pari al 99,99%), è possibile basarsi sull'intervento manuale per correggere gli errori. L'applicazione, infatti, deve essere in grado di eseguire automaticamente la diagnosi e la riparazione.
- Oltre quattro nove, è difficile rilevare le interruzioni in modo sufficientemente rapido per soddisfare il contratto di servizio.
- Si pensi all'intervallo di tempo rispetto al quale viene misurato il contratto di servizio: minore è la finestra, più ristretto sarà il livello di tolleranza. Non ha senso definire il contratto di servizio in termini di tempo di attività oraria o giornaliera.
- Prendere in considerazione le misurazioni MTBF e MTTR. Maggiore sarà il contratto di servizio, meno spesso il servizio può scendere e in termini economici necessario ripristinare il servizio.
- Ottieni contratto dai tuoi clienti gli obiettivi di disponibilità di tutti i dati dell'applicazione e la documentazione. In caso contrario, la progettazione potrebbe non soddisfare le aspettative dei clienti.

### <a name="identify-dependencies"></a>Identificare le dipendenze

Eseguire gli esercizi di mapping delle dipendenze per identificare le dipendenze interne ed esterne. Gli esempi includono le dipendenze relative alla sicurezza o l'identità, quali Active Directory o i servizi di terze parti, ad esempio un provider di pagamenti o servizio di messaggistica di posta elettronica.

Prestare particolare attenzione alle dipendenze esterne che potrebbe essere un singolo punto di guasto o causare colli di bottiglia. Se un carico di lavoro richiede tempi di attività pari al 99,99%, ma dipende da un servizio con un contratto di servizio del 99,9%, tale servizio non può essere un singolo punto di guasto nel sistema. Un rimedio è avere un percorso di fallback nel caso in cui il servizio ha esito negativo. In alternativa, intraprenda altre misure per il ripristino da un errore nel servizio.

La tabella seguente illustra il potenziale tempo totale di inattività per vari livelli di contratto di servizio.

| **Contratto di servizio** | **Tempo di inattività settimanale** | **Tempo di inattività al mese** | **Tempo di inattività annuale** |
|---------|-----------------------|------------------------|-----------------------|
| 99%     | 1,68 ore            | 7,2 ore              | 3,65 giorni             |
| 99,9%   | 10,1 minuti          | 43,2 minuti           | 8,76 ore            |
| 99,95%  | 5 minuti             | 21,6 minuti           | 4,38 ore            |
| 99,99%  | 1,01 minuti          | 4,32 minuti           | 52,56 minuti         |
| 99,999% | 6 secondi             | 25,9 secondi           | 5,26 minuti          |

## <a name="understand-service-level-agreements"></a>Comprendere i contratti di servizio

In Azure, il [contratto di servizio](https://azure.microsoft.com/en-us/support/legal/sla/) impegni di Microsoft per il tempo di attività e connettività. Se il contratto di servizio per un determinato servizio è pari al 99,9%, è necessario prevedere che il servizio sia disponibile il 99,9% del tempo. Servizi differenti presentano diversi contratti di servizio.

Il contratto di servizio di Azure include anche solo indicazioni per ottenere un credito di servizio se il contratto di servizio non viene soddisfatta, insieme a definizioni specifiche di *disponibilità* per ogni servizio. Questo aspetto del contratto di servizio funge da criterio di imposizione.

### <a name="composite-slas"></a>Contratti di servizio compositi

*I contratti di servizio compositi* interessare più servizi che supportano un'applicazione, ognuno con diversi livelli di disponibilità. Si consideri ad esempio un'app web del servizio App che scrive nel Database SQL di Azure. Al momento della redazione del presente documento, tali servizi Azure dispongono dei contratti di servizio seguenti:

- App web del servizio App = 99,95%
- Database SQL = 99,99%

A quanto corrisponde il tempo di inattività massimo che ci si aspetta da questa applicazione? Se il servizio presenta errori, di conseguenza l'intera applicazione presenterà errori. La probabilità di ogni interruzione del servizio è indipendente, pertanto il contratto di servizio composito per l'applicazione è pari al 99,95% × pari al 99,99% = 99,94%. È inferiore ai singoli contratti di servizio, che non è sorprendente perché un'applicazione che si basa su più servizi dispone di più punti di errore potenziali.

È possibile migliorare il contratto di servizio composito creando percorsi di fallback indipendenti. Ad esempio, se il Database SQL è disponibile, inserire le transazioni in una coda per l'elaborazione successiva.

![Contratto di servizio composito](_images/composite-sla.png)

Con questa struttura, l'applicazione è ancora disponibile anche se non si può connettere al database. Tuttavia, se la coda e il database si interrompono allo stesso tempo, l'applicazione si interromperà. La percentuale di tempo per un errore simultaneo prevista è 0,0001 × 0,001 e pertanto il contratto di servizio composito per questo percorso combinato è:

- Database *o* coda = 1,0 − (0,0001 × 0,001) = 99,99999%

Il contratto di servizio composito totale è:

- App Web *e* (database *oppure* coda) = 99,95% × 99,99999% = \~pari al 99,95%

Sussistono compromessi di questo approccio. La logica dell'applicazione è più complessa e si sta pagando per la coda è necessario considerare problemi di coerenza dei dati.

### <a name="slas-for-multiregion-deployments"></a>Contratti di servizio per le distribuzioni multiarea

*I contratti di servizio per le distribuzioni multiarea* comportano una tecnica a disponibilità elevata per distribuire l'applicazione in più aree e usare Gestione traffico di Azure per eseguire il failover se l'applicazione non riesce in un'unica area.

Il contratto di servizio composito per una distribuzione multiarea viene calcolata come segue:

- *N* è il contratto di servizio composito per l'applicazione distribuita in un'unica area.
- *R* è il numero di aree in cui l'applicazione viene distribuita.

È la probabilità prevista che l'applicazione non riesce in tutte le aree allo stesso tempo ((1 − N) \^ R). Ad esempio, se il contratto di servizio singola area è pari al 99,95%:

- Il contratto di servizio combinato per due aree = (1 − (1 − 0.9995) \^ 2) = % 99.999975
- Il contratto di servizio combinato per quattro aree = (1 − (1 − 0.9995) \^ 4) = % 99.999999

Il [contratto di servizio per Traffic Manager](https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/) è inoltre un fattore. Il failover non è istantaneo nelle configurazioni di tipo attivo / passivo, che possono comportare tempi di inattività durante un failover. Visualizzare [monitoraggio degli endpoint di gestione traffico e failover](/azure/traffic-manager/traffic-manager-monitoring).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Architetto per la resilienza e disponibilità](./architect.md)
