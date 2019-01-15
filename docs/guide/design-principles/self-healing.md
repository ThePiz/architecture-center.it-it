---
title: Progettazione per la correzione automatica
titleSuffix: Azure Application Architecture Guide
description: In caso di guasto, le applicazioni resilienti possono essere ripristinate senza alcun intervento manuale.
author: MikeWasson
ms.date: 08/30/2018
ms.custom: seojan19
ms.openlocfilehash: a7aaf50b4cb1d844637c89da8e977b694481f18d
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113517"
---
# <a name="design-for-self-healing"></a>Progettazione per la correzione automatica

## <a name="design-your-application-to-be-self-healing-when-failures-occur"></a>Progettare l'applicazione in modo che sia in grado di eseguire la correzione automatica dei propri errori

In un sistema distribuito possono verificarsi errori. Gli hardware possono danneggiarsi. La rete, poi, può avere errori temporanei. Raramente un intero servizio o area può subire delle interruzioni, tuttavia è necessario pianificare anche questo tipo di problemi.

A tal fine, è consigliabile progettare l'applicazione in modo che sia in grado di eseguire la correzione automatica dei propri errori. Ciò richiede un approccio a tre fasi:

- Rilevamento degli errori.
- Risposta graduale agli errori.
- Acquisizione di informazioni operative tramite la registrazione e il monitoraggio degli errori.

La modalità di risposta a un determinato tipo di errore può dipendere dai requisiti di disponibilità dell'applicazione. Quando è richiesta una disponibilità elevata, ad esempio, è consigliabile effettuare il failover automatico in un'area secondaria in caso di interruzione in un'area. Ciò tuttavia comporta un costo superiore rispetto alla distribuzione in una singola area.

È bene inoltre non valutare solo eventi importanti come le interruzioni a livello di area, in genere rare. È consigliabile invece concentrarsi sulla gestione degli errori locali e di breve durata, ad esempio gli errori di connettività di rete o le connessioni di database non riuscite.

## <a name="recommendations"></a>Consigli

**Ritentare le operazioni non riuscite**. Gli errori temporanei possono dipendere da una perdita momentanea della connettività di rete, da una connessione a un database eliminato o da un timeout che si verifica quando un servizio è occupato. Per gestire gli errori temporanei, nell'applicazione va prevista una logica di ripetizione dei tentativi. In molti servizi di Azure, l'SDK client implementa i tentativi automatici. Per altre informazioni, vedere [Gestione degli errori temporanei][transient-fault-handling] e [Modello di ripetizione dei tentativi][retry].

**Proteggere dagli errori i servizi remoti (Interruttore)**. Effettuare un tentativo dopo un errore temporaneo è un metodo valido, ma se il problema persiste può accadere che si ricevano segnalazioni di malfunzionamento del servizio da un numero eccessivo di chiamanti. Il sommarsi delle richieste può innescare errori a catena. Usare il [modello a interruttore][circuit-breaker] per fallire e rispondere immediatamente agli errori, senza chiamata remota, quando un'operazione avrà probabilmente esito negativo.

**Isolare le risorse critiche (A scomparti)**. Gli errori in un sottosistema possono propagarsi. Questa situazione può verificarsi se un errore fa sì che alcune risorse, come i thread o i socket, non siano liberate in modo tempestivo, causandone l'esaurimento. Per evitare questo problema, è possibile suddividere un sistema in gruppi isolati, in modo che un errore in una partizione non causi l'arresto dell'intero sistema.

**Adottare il livellamento del carico**. Nelle applicazioni possono verificarsi picchi improvvisi di traffico sovraccaricano i servizi nel back-end. Per evitare questo problema, usare lo [schema di livellamento del carico basato sulle code] [load-level] per inserire in coda gli elementi di lavoro per l'esecuzione asincrona. La coda si comporta come un buffer che contiene i picchi di carico.

**Effettuare il failover**. Se un'istanza non è raggiungibile, effettuare il failover su un'altra istanza. Per gli elementi senza stato, ad esempio un server Web, inserire più istanze dietro a un servizio di bilanciamento del carico o di gestione traffico. Per elementi che archiviano lo stato, ad esempio un database, usare le repliche e il failover. A seconda dell'archivio dati e della relativa modalità di replica, ciò può richiedere all'applicazione di gestire la coerenza.

**Compensare le transazioni non riuscite**. In generale è consigliabile evitare le transazioni distribuite, in quanto richiedono il coordinamento tra servizi e risorse. In alternativa, creare un'operazione costituita da singole transazioni più piccole. Se l'operazione si interrompe prima del termine, usare il modello [Transazioni di compensazione] [ compensating-transactions] per annullare qualsiasi passaggio già completato.

**Eseguire il checkpoint delle transazioni con esecuzione prolungata**. I checkpoint possono fornire resilienza qualora un'operazione con esecuzione prolungata non riesca. Quando l'operazione si riavvia (ad esempio, viene prelevata da un'altra macchina virtuale), può essere ripresa a partire dall'ultimo checkpoint.

**Ridurre le prestazioni in modo graduale**. In alcuni casi non è possibile risolvere un problema, ma è può risultare comunque utile offrire funzionalità ridotte. Si consideri un'applicazione in cui viene visualizzato un catalogo di libri. Nel caso in cui l'applicazione non possa recuperare l'immagine di anteprima di una copertina, si potrebbe visualizzare un'immagine segnaposto. Interi sottosistemi possono non essere critici per l'applicazione. Ad esempio, in un sito di e-commerce la visualizzazione dei consigli sui prodotti è probabilmente meno importante dell'elaborazione degli ordini.

**Limitare i client**. A volte un numero ridotto di utenti crea un carico eccessivo che può ridurre la disponibilità dell'applicazione per gli altri utenti. In questo caso, è utile limitare il client per un determinato periodo di tempo. Vedere [Modello Limitazione][throttle].

**Bloccare gli utenti malintenzionati**. Applicare una limitazione a un client non implica necessariamente che questi abbia agito con cattive intenzioni. Significa semplicemente che ha superato la propria quota del servizio. Tuttavia, se un client supera spesso la propria quota o mostra un comportamento errato, è possibile bloccarlo. Definire un processo fuori banda affinché l'utente possa richiedere lo sblocco.

**Usare il modello Designazione leader**. Quando è necessario coordinare un'attività, usare il modello [Designazione leader] [ leader-election] per selezionare un coordinatore. In questo modo, il coordinatore non rappresenta un singolo punto di guasto. In caso di errore, ne viene selezionato un altro. Se non si vuole implementare un algoritmo di designazione leader da zero, è possibile prendere in considerazione una soluzione preconfigurata, ad esempio Zookeeper.

**Eseguire i test di inserimento degli errori**. Accade spesso che venga testato il percorso di riuscita ma non il percorso di errore. Un sistema può essere eseguito nell'ambiente di produzione per lungo tempo prima che venga rilevato un percorso di errore. Usare i test di inserimento degli errori per testare la resilienza del sistema sia attivando errori reali sia simulandoli.

**Adottare l'ingegneria del caos**. L'ingegneria del caos estende il concetto di inserimento degli errori inserendo nelle istanze di produzione errori o condizioni anormali in modo casuale.

Per un approccio strutturato a come creare applicazioni che siano in grado di eseguire la correzione automatica dei propri errori, vedere [Progettazione di applicazioni resilienti per Azure][resiliency-overview].

<!-- links -->

[circuit-breaker]: ../../patterns/circuit-breaker.md
[compensating-transactions]: ../../patterns/compensating-transaction.md
[leader-election]: ../../patterns/leader-election.md
[load-level]: ../../patterns/queue-based-load-leveling.md
[resiliency-overview]: ../../resiliency/index.md
[retry]: ../../patterns/retry.md
[throttle]: ../../patterns/throttling.md
[transient-fault-handling]: ../../best-practices/transient-faults.md
