---
title: Transazione di compensazione
description: Annullare il lavoro eseguito da una serie di passaggi che insieme definiscono un'operazione coerente.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
pnp.pattern.categories:
- resiliency
ms.openlocfilehash: a822de990d6ce933024207073b110e98f8da40bf
ms.sourcegitcommit: 8ab30776e0c4cdc16ca0dcc881960e3108ad3e94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
ms.locfileid: "26359402"
---
# <a name="compensating-transaction-pattern"></a>Modello di transazioni di compensazione

[!INCLUDE [header](../_includes/header.md)]

Annullare il lavoro eseguito da una serie di passaggi che insieme definiscono un'operazione coerente in caso di esito negativo di uno o più passaggi. Le operazioni che seguono il modello di coerenza finale sono presenti in genere nelle applicazioni ospitate nel cloud che implementano flussi di lavoro e processi aziendali complessi.

## <a name="context-and-problem"></a>Contesto e problema

Le applicazioni in esecuzione nel cloud spesso modificano i dati. Questi dati potrebbero essere distribuiti in varie origini dati contenute in aree geografiche diverse. Per evitare conflitti e migliorare le prestazioni in un ambiente distribuito, un'applicazione non deve provare a offrire coerenza transazionale assoluta. Deve invece implementare la coerenza finale. In questo modello, un'operazione aziendale tipica è costituita da una serie di passaggi distinti. Mentre questi passaggi sono in esecuzione, la visualizzazione complessiva dello stato del sistema potrebbe essere incoerente, ma quando l'operazione è stata completata e tutti i passaggi sono stati eseguiti il sistema deve diventare di nuovo coerente.

> L'articolo [Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx) (Nozioni di base sulla coerenza dei dati) contiene informazioni sui motivi per cui le transazioni distribuite non garantiscono una corretta scalabilità e i principi del modello di coerenza finale.

Una delle parti più difficili del modello di coerenza finale è come gestire un passaggio non riuscito. In questo caso potrebbe essere necessario annullare tutto il lavoro completato tramite i passaggi precedenti nell'operazione. I dati non possono tuttavia essere semplicemente annullati perché potrebbero essere stati modificati da altre istanze simultanee dell'applicazione. Anche nei casi in cui i dati non siano stati modificati da un'istanza simultanea, l'annullamento di un passaggio non può essere una semplice questione di ripristino dello stato originale. Potrebbe essere necessario applicare diverse regole specifiche per l'azienda (vedere il sito Web di viaggi descritto nella sezione Esempio).

Se un'operazione che implementa la coerenza finale si estende su diversi archivi dati eterogenei, l'annullamento dei passaggi dell'operazione richiederà a sua volta la visita a ogni archivio dati. Il lavoro eseguito in ogni archivio dati deve essere annullato in modo affidabile per evitare che il sistema rimanga non coerente.

Non tutti i dati interessati da un'operazione che implementa la coerenza finale potrebbero essere conservati in un database. In un ambiente di architettura orientata ai servizi (SOA) un'operazione potrebbe chiamare un'azione in un servizio e provocare una modifica nello stato mantenuto da quel servizio. Per annullare l'operazione, è necessario annullare anche questa modifica di stato. Potrebbe pertanto essere necessario chiamare nuovamente il servizio ed eseguire un'altra azione che inverta gli effetti del primo.

## <a name="solution"></a>Soluzione

La soluzione consiste nell'implementare una transazione di compensazione. I passaggi di una transazione di compensazione devono annullare gli effetti dei passaggi nell'operazione originale. Una transazione di compensazione potrebbe non essere in grado di sostituire semplicemente lo stato corrente con lo stato di sistema all'inizio dell'operazione poiché questo approccio potrebbe sovrascrivere le modifiche apportate da altre istanze simultanee di un'applicazione. Deve invece essere un processo intelligente che prenda in considerazione tutte le azioni eseguite dalle istanze simultanee. Questo processo in genere è applicazione specifica, determinata dalla natura del lavoro eseguito dall'operazione originale.

Un approccio comune prevede l'uso di un flusso di lavoro per implementare un'operazione coerente che richiede la compensazione. Via via che l'operazione originale procede, il sistema registra informazioni su ogni passaggio e su come può essere annullato il lavoro da esso eseguito. Se l'operazione non riesce in qualsiasi punto, il flusso di lavoro torna indietro nei passaggi completati ed esegue il lavoro che inverte ogni passaggio. Si noti che una transazione di compensazione potrebbe non dover annullare il lavoro seguendo l'esatto ordine inverso dell'operazione originale e alcuni dei passaggi dell'annullamento potrebbero essere eseguiti in parallelo.

> Questo approccio è simile alla strategia delle Saghe descritta nel [blog di Clemens Vasters](http://vasters.com/clemensv/2012/09/01/Sagas.aspx).

Una transazione di compensazione è anche un'operazione coerente e potrebbe avere esito negativo. Il sistema deve essere in grado di riprendere la transazione di compensazione al momento dell'errore e continuare. Potrebbe essere necessario ripetere un passaggio non riuscito, pertanto i passaggi in una transazione di compensazione devono essere definiti come comandi idempotenti. Per altre informazioni, vedere [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) (Modelli di idempotenza) sul blog di Jonathan Oliver.

In alcuni casi potrebbe non essere possibile riprendere la transazione da un passaggio non riuscito se non tramite un intervento manuale. In questi casi il sistema deve generare un avviso e fornire quante più informazioni possibili sul motivo dell'errore.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo modello, considerare quanto segue:

Potrebbe non essere facile determinare quando un passaggio di un'operazione che implementa la coerenza finale non è riuscito. Un passaggio potrebbe non riuscire immediatamente, ma bloccarsi. Potrebbe essere necessario implementare un tipo di meccanismo di timeout.

- La logica di compensazione non è facilmente generalizzata. Una transazione di compensazione è specifica dell'applicazione. Si basa sul fatto che l'applicazione possieda informazioni sufficienti per poter annullare gli effetti di ogni passaggio di un'operazione non riuscita.

È necessario definire i passaggi in una transazione di compensazione come comandi idempotenti. In questo modo è possibile ripetere i passaggi se la transazione di compensazione ha esito negativo.

L'infrastruttura che gestisce i passaggi nell'operazione originale e la transazione di compensazione devono essere resilienti. Non deve perdere le informazioni necessarie per compensare un passaggio non riuscito ed essere in grado di monitorare in modo affidabile lo stato di avanzamento della logica di compensazione.

Una transazione di compensazione non riporta necessariamente i dati nel sistema allo stato in cui si trovavano all'inizio dell'operazione originale. Consente invece di compensare il lavoro eseguito dai passaggi completati correttamente prima che l'operazione non riuscisse.

L'ordine dei passaggi nella transazione di compensazione non deve essere necessariamente l'esatto opposto dei passaggi dell'operazione originale. Ad esempio, un archivio dati potrebbe essere più sensibile alle incoerenze rispetto a un altro e pertanto i passaggi nella transazione di compensazione che annullano le modifiche a questo archivio devono essere eseguiti per primi.

Inserire un blocco basato sul timeout a breve termine su ogni risorsa necessaria per completare un'operazione e ottenere in anticipo queste risorse per aumentare la probabilità che l'attività complessiva abbia esito positivo. L'operazione deve essere eseguita solo dopo aver acquisito tutte le risorse. Tutte le azioni devono essere completate prima della scadenza del blocco.

È consigliabile usare la logica di ripetizione dei tentativi, che è più tollerante, per ridurre al minimo gli errori che attivano una transazione di compensazione. Se un passaggio in un'operazione che implementa la coerenza finale non riesce, provare a gestire l'errore come un'eccezione temporanea e ripetere il passaggio. Arrestare l'operazione e avviare una transazione di compensazione solo se un passaggio non riesce ripetutamente o in modo permanente.

> Molti dei problemi posti dall'implementazione di una transazione di compensazione sono gli stessi relativi all'implementazione di coerenza finale. Per altre informazioni, vedere la sezione relativa alle considerazioni sull'implementazione della coerenza finale nell'articolo [Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx) (Nozioni di base sulla coerenza dei dati).

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello solo per le operazioni che devono essere annullate in caso di errore. Se possibile, progettare soluzioni per evitare la complessità della richiesta di transazioni di compensazione.

## <a name="example"></a>Esempio

Un sito Web di viaggi consente ai clienti di prenotare itinerari. Un singolo itinerario potrebbe comprendere una serie di voli e hotel. Un cliente che viaggia da Seattle a Londra e quindi a Parigi potrebbe eseguire la procedura seguente durante la creazione di un itinerario:

1. Prenotare un posto sul volo F1 da Seattle a Londra.
2. Prenotare un posto sul volo F2 da Londra a Parigi.
3. Prenotare un posto sul volo F3 da Parigi a Seattle.
4. Prenotare una camera nell'hotel H1 a Londra.
5. Prenotare una camera nell'hotel H2 a Parigi.

Questi passaggi costituiscono un'operazione coerente, anche se ogni passaggio è un'operazione separata. Di conseguenza, oltre a eseguire questi passaggi, il sistema deve anche registrare le operazioni di contatore necessarie per annullare ogni passaggio nel caso in cui il cliente decidesse di annullare l'itinerario. I passaggi necessari per eseguire le operazioni di contatore possono quindi essere eseguite come una transazione di compensazione.

Si noti che le operazioni nella transazione di compensazione potrebbero non essere l'esatto opposto dei passaggi originali e la logica in ogni passaggio nella transazione di compensazione deve prendere in considerazione tutte le eventuali regole specifiche dell'azienda. Ad esempio, l'annullamento della prenotazione di un volo potrebbe non dare al cliente il diritto al rimborso completo dell'importo pagato. Nella figura viene illustrata la generazione di una transazione di compensazione per annullare una transazione a esecuzione prolungata per la prenotazione di un itinerario di viaggio.

![Nella figura viene illustrata la generazione di una transazione di compensazione per annullare una transazione a esecuzione prolungata per la prenotazione di un itinerario di viaggio](./_images/compensating-transaction-diagram.png)


> Potrebbe essere possibile eseguire in parallelo i passaggi di una transazione di compensazione, a seconda di come è stata progettata la logica di compensazione per ogni passaggio.

In molte soluzioni aziendali, l'esito negativo di un unico passaggio non sempre richiede il roll-back del sistema tramite una transazione di compensazione. Ad esempio, se &mdash; dopo che aver prenotato i voli F1, F2 e F3 nello scenario del sito Web di viaggio &mdash; il cliente non è in grado di prenotare una stanza nell'hotel H1, è preferibile offrire al cliente una stanza in un altro albergo della stessa città anziché annullare i voli. Il cliente può comunque decidere di annullare la prenotazione (in tal caso, viene eseguita la transazione di compensazione e vengono annullate le prenotazioni effettuate sui voli F1, F2 e F3), ma questa decisione deve essere effettuata dal cliente anziché dal sistema.

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

- [Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx) (Nozioni di base sulla coerenza dei dati). Il modello di transazioni di compensazione viene spesso usato per annullare le operazioni che implementano il modello di coerenza finale. Queste nozioni di base offrono informazioni su vantaggi e compromessi della coerenza finale.

- [Scheduler-Agent-Supervisor Pattern](scheduler-agent-supervisor.md) (Modello di supervisore agente di pianificazione). Descrive come implementare sistemi resilienti che eseguono operazioni aziendali che usano risorse e servizi distribuiti. Potrebbe talvolta essere necessario annullare il lavoro eseguito da un'operazione usando una transazione di compensazione.

- [Modello di ripetizione dei tentativi](./retry.md). L'esecuzione delle transazioni di compensazione può essere costosa. Per ridurne al minimo l'uso è possibile implementare criteri efficaci per la ripetizione delle operazioni non riuscite seguendo il modello di ripetizione dei tentativi.
