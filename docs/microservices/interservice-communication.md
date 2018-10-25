---
title: Comunicazione tra i servizi nei microservizi
description: Comunicazione tra i servizi nei microservizi
author: MikeWasson
ms.date: 10/23/2018
ms.openlocfilehash: 19a54ffc362a1fc88c3255c9346bd697a319b143
ms.sourcegitcommit: fdcacbfdc77370532a4dde776c5d9b82227dff2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49962960"
---
# <a name="designing-microservices-interservice-communication"></a>Progettazione di microservizi: comunicazione tra i servizi

La comunicazione tra i microservizi deve essere efficiente e solida, ma raggiungere questo obiettivo può essere difficile, visto l'elevato numero di piccoli servizi che interagiscono per completare una singola transazione. In questo capitolo vengono esaminati i compromessi tra messaggistica asincrona e API sincrone, oltre ad alcune delle problematiche poste dalla progettazione della comunicazione resiliente tra i servizi e il ruolo che può ricoprire una rete mesh di servizi.

![](./images/interservice-communication.png)

## <a name="challenges"></a>Problematiche 

Ecco alcune delle principali problematiche derivanti dalla comunicazione da servizio a servizio. Le reti mesh di servizi, descritte più avanti in questo capitolo, sono progettate per gestire molte di tali problematiche.

**Resilienza.** Possono esistere decine o addirittura centinaia di istanze di un determinato microservizio. Un'istanza può avere esito negativo per diversi motivi. Può verificarsi un errore a livello di nodo, ad esempio un errore hardware o un riavvio della VM. Un'istanza potrebbe arrestarsi in modo anomalo o essere sovraccaricata da richieste e non riuscire a elaborare le nuove richieste. Uno di questi eventi può causare l'esito negativo di una chiamata di rete. Due schemi progettuali consentono di rendere più resilienti le chiamate di rete da servizio a servizio:

- **[Nuovo tentativo](../patterns/retry.md)**. Una chiamata di rete potrebbe non riuscire a causa di un errore temporaneo che si risolve da sé. Invece di non riuscire del tutto, l'operazione in genere deve essere ripetuta dal chiamante un certo numero di volte o finché non trascorre un periodo di timeout configurato. Se tuttavia un'operazione non è idempotente, i nuovi tentativi possono causare effetti collaterali indesiderati. La chiamata originale potrebbe riuscire, ma il chiamante non riceve mai una risposta. Se il chiamante riprova, l'operazione potrebbe essere richiamata due volte. In genere non è sicuro provare a eseguire di nuovo i metodi POST o PATCH, perché non è garantito che siano idempotenti.

- **[Interruttore](../patterns/circuit-breaker.md)**. Un numero eccessivo di richieste non riuscite può causare un collo di bottiglia perché le richieste in sospeso si accumulano nella coda. Queste richieste bloccate potrebbero tenere in sospeso risorse di sistema critiche, come la memoria, i thread, le connessioni di database e così via, che possono causare errori a catena. Il modello a interruttore può impedire a un servizio di provare a eseguire ripetutamente un'operazione che probabilmente continuerà a restituire un errore. 

**Bilanciamento del carico**. Quando il servizio "A" chiama il servizio "B", la richiesta deve raggiungere un'istanza in esecuzione del servizio "B". In Kubernetes il tipo di risorsa `Service` fornisce un indirizzo IP stabile per un gruppo di pod. Il traffico di rete verso l'indirizzo IP del servizio viene inoltrato a un pod per mezzo di regole di iptables. Per impostazione predefinita, viene scelto un pod casuale. Una rete mesh di servizi (vedere sotto) può fornire algoritmi di bilanciamento del carico più intelligenti in base alla latenza osservata o ad altre metriche.

**Analisi distribuita**. Una singola transazione può estendersi a più servizi, rendendo così difficile monitorare le prestazioni complessive e l'integrità del sistema. Anche se ogni servizio genera log e metriche, se non vengono in qualche modo collegati, hanno un uso limitato. Il capitolo [Registrazione e monitoraggio](./logging-monitoring.md) illustra in maggior dettaglio l'analisi distribuita, che qui viene citata in quanto problematica.

**Controllo delle versioni dei servizi**. Quando un team distribuisce una nuova versione di un servizio, deve evitare di interrompere gli altri servizi o i client esterni che ne dipendono. Potrebbe anche essere necessario eseguire più versioni affiancate di un servizio e instradare le richieste a una determinata versione. Per altre informazioni su questo problema, vedere [Controllo delle versioni delle API](./api-design.md#api-versioning).

**Crittografia TLS e autenticazione TLS reciproca**. Per motivi di sicurezza, potrebbe essere necessario crittografare il traffico tra i servizi con TLS e usare l'autenticazione TLS reciproca per autenticare i chiamanti.

## <a name="synchronous-versus-asynchronous-messaging"></a>Messaggistica sincrona e asincrona

I microservizi possono usare due modelli di messaggistica di base per comunicare con gli altri microservizi. 

1. Comunicazione sincrona. In questo modello un servizio chiama un'API esposta da un altro servizio, usando un protocollo come HTTP o gRPC. Questa opzione è un modello di messaggistica sincrona perché il chiamante attende una risposta dal ricevitore. 

2. Passaggio di messaggi asincroni. In questo modello un servizio invia un messaggio senza attendere una risposta e uno o più servizi elaborano il messaggio in modo asincrono.

È importante distinguere tra I/O asincrono e un protocollo asincrono. L'I/O asincrono implica che il thread chiamante non viene bloccato durante il completamento dell'I/O. È importante per le prestazioni, ma dal punto di vista dell'architettura è un dettaglio dell'implementazione. Un protocollo asincrono implica che il mittente non attende una risposta. HTTP è un protocollo sincrono, anche se un client HTTP può usare l'I/O asincrono quando invia una richiesta. 

Sono possibili compromessi per ogni modello. Il paradigma richiesta/risposta è ben noto, quindi progettare un'API può risultare più naturale che progettare un sistema di messaggistica. La messaggistica asincrona presenta tuttavia alcuni vantaggi che possono essere molto utili in un'architettura di microservizi:

- **Accoppiamento ridotto**. Il mittente del messaggio non ha bisogno di informazioni sul consumer. 

- **Più sottoscrittori**. Usando un modello di pubblicazione/sottoscrizione, più consumer possono effettuare la sottoscrizione per ricevere gli eventi. Vedere [Stile di architettura guidato dagli eventi](/azure/architecture/guide/architecture-styles/event-driven).

- **Isolamento degli errori**. Se il consumer ha esito negativo, il mittente può ugualmente inviare i messaggi. I messaggi verranno selezionati quando il consumer eseguirà il recupero. Questa possibilità è particolarmente utile in un'architettura di microservizi, perché ogni servizio ha il proprio ciclo di vita. Un servizio potrebbe non essere disponibile o essere sostituito con una versione più recente in qualsiasi momento. La messaggistica asincrona può gestire i tempi di inattività intermittenti. Per le API sincrone è invece necessario che il servizio sia disponibile o l'operazione non riuscirà. 
 
- **Tempi di risposta**. Un servizio upstream può rispondere più rapidamente se non attende servizi downstream. Ciò può rivelarsi particolarmente utile in un'architettura di microservizi. Se è presente una catena di dipendenze dei servizi (il servizio A chiama B, che chiama C e così via), l'attesa di chiamate sincrone può aggiungere periodi di latenza inaccettabili.

- **Livellamento del carico**. Una coda può fungere da buffer per livellare il carico di lavoro, in modo che i ricevitori possano elaborare i messaggi alla propria velocità. 

- **Flussi di lavoro**. Le code possono essere usate per gestire un flusso di lavoro, inserendo un checkpoint nel messaggio dopo ogni passaggio del flusso di lavoro.

L'uso efficiente della messaggistica asincrona comporta tuttavia anche alcune problematiche.

- **Accoppiamento con l'infrastruttura di messaggistica**. L'uso di una determinata infrastruttura di messaggistica può causare un accoppiamento rigido con tale infrastruttura. Sarà difficile passare a un'altra infrastruttura di messaggistica in un secondo momento.

- **Latenza**. La latenza end-to-end per un'operazione può diventare elevata se le code di messaggi si riempiono.  

- **Costi**. A velocità effettive elevate, il costo economico dell'infrastruttura di messaggistica potrebbe essere considerevole.

- **Complessità**. La gestione della messaggistica asincrona non è un'attività semplice. È ad esempio necessario gestire i messaggi duplicati, deduplicandoli o rendendo le operazioni idempotenti. È anche difficile implementare la semantica richiesta-risposta usando la messaggistica asincrona. Per inviare una risposta, è necessaria un'altra coda, oltre a un modo per correlare i messaggi di richiesta e di risposta.

- **Velocità effettiva**. Se i messaggi richiedono la *semantica di accodamento*, la coda può diventare un collo di bottiglia nel sistema. Ogni messaggio richiede almeno un'operazione di accodamento e un'operazione di rimozione dalla coda. La semantica di accodamento inoltre richiede in genere qualche tipo di blocco all'interno dell'infrastruttura di messaggistica. Se la coda è un servizio gestito, potrebbe esserci latenza aggiuntiva, perché la coda è esterna alla rete virtuale del cluster. È possibile attenuare questi problemi con l'invio in batch dei messaggi, che però aumenta la complessità del codice. Se i messaggi non richiedono la semantica di accodamento, è possibile usare un *flusso* di eventi invece di una coda. Per altre informazioni, vedere [Stile di architettura guidato dagli eventi](../guide/architecture-styles/event-driven.md).  

## <a name="drone-delivery-choosing-the-messaging-patterns"></a>Drone Delivery: scelta dei modelli di messaggistica

Tenendo presenti queste considerazioni, il team di sviluppo ha effettuato le scelte di progettazione seguenti per l'applicazione Drone Delivery

- Il servizio di inserimento espone un'API REST pubblica che le applicazioni client usano per pianificare, aggiornare o annullare le consegne.

- Il servizio di inserimento usa Hub eventi per inviare messaggi asincroni al servizio utilità di pianificazione. I messaggi asincroni sono necessari per implementare il livellamento del carico richiesto per l'inserimento. Per informazioni dettagliate sull'interazione tra i servizi di inserimento e di utilità di pianificazione, vedere [Inserimento e flusso di lavoro][ingestion-workflow].

- I servizi account, consegna, pacchetto, drone e trasporto di terze parti espongono tutti API REST interne. Il servizio utilità di pianificazione chiama queste API per eseguire una richiesta di un utente. Uno dei motivi per usare le API sincrone è la necessità dell'utilità di pianificazione di ottenere una risposta da ogni servizio downstream. Un errore in uno dei servizi downstream comporta l'esito negativo dell'intera operazione. Un potenziale problema è tuttavia la quantità di latenza che viene introdotta chiamando i servizi back-end. 

- Se in un servizio downstream si verifica un errore non temporaneo, l'intera transazione deve essere contrassegnata come non riuscita. Per gestire un caso come questo, il servizio utilità di pianificazione invia un messaggio asincrono al supervisore, in modo che il supervisore possa pianificare transazioni di compensazione, come descritto nel capitolo [Inserimento e flusso di lavoro][ingestion-workflow].   

- Il servizio di consegna espone un'API pubblica che i client possono usare per ottenere lo stato di una consegna. Nel capitolo [Gateway API](./gateway.md) viene illustrato come un gateway API può nascondere i servizi sottostanti al client, in modo che il client non debba conoscere le API esposte dai diversi servizi. 

- Mentre un drone è in volo, il servizio drone invia gli eventi contenenti la posizione e lo stato correnti del drone. Il servizio di consegna è in ascolto di questi eventi per tenere traccia dello stato di una consegna.

- Quando lo stato di una consegna cambia, il servizio di consegna invia un evento relativo allo stato della consegna, ad esempio `DeliveryCreated` o `DeliveryCompleted`. Qualsiasi servizio può sottoscrivere questi eventi. Nella progettazione corrente il servizio di consegna è il solo sottoscrittore, ma potrebbero esserci altri sottoscrittori in seguito. Gli eventi, ad esempio, potrebbero essere inviati a un servizio di analisi in tempo reale e poiché l'utilità di pianificazione non deve attendere una risposta, l'aggiunta di altri sottoscrittori non ha effetto sul percorso del flusso di lavoro principale.

![](./images/drone-communication.png)

Si noti che gli eventi relativi allo stato della consegna sono derivati dagli eventi relativi alla posizione del drone. Quando ad esempio un drone raggiunge la posizione di una consegna e recapita un pacchetto, il servizio di consegna lo converte in un evento DeliveryCompleted. Questo è un esempio di come si possa pensare in termini di modelli di dominio. Come illustrato prima, la gestione dei droni rientra in un contesto delimitato distinto. Gli eventi relativi ai droni indicano la posizione fisica di un drone. Gli eventi relativi alle consegne invece rappresentano le modifiche dello stato di una consegna, che è un'entità di business diversa.

## <a name="using-a-service-mesh"></a>Uso di una rete mesh di servizi

Una *rete mesh di servizi* è un livello di software che gestisce la comunicazione da servizio a servizio. Le reti mesh di servizi sono progettate per fare fronte a molte delle problematiche elencate nella sezione precedente e per trasferire la responsabilità di queste problematiche dai microservizi a un livello condiviso. La rete mesh di servizi funge da proxy che intercetta la comunicazione di rete tra i microservizi nel cluster. 

> [!NOTE]
> La rete mesh di servizi è un esempio del [modello ad ambasciata](../patterns/ambassador.md), un servizio helper che invia le richiesta di rete per conto dell'applicazione. 

Le opzioni principali per una rete mesh di servizi in Kubernetes sono attualmente [linkerd](https://linkerd.io/) e [Istio](https://istio.io/). Entrambe queste tecnologie sono in rapida evoluzione. Alcune funzionalità comuni a linkerd e Istio tuttavia includono: 

- Bilanciamento del carico a livello di sessione, in base alle latenze osservate o al numero di richieste arretrate, che può migliorare le prestazioni oltre quelle del bilanciamento del carico di livello 4 offerte da Kubernetes. 

- Routing di livello 7 basato sul percorso URL, sull'intestazione host, sulla versione API o su altre regole a livello di applicazione.

- Nuovo tentativo per le richieste non riuscite. Una rete mesh di servizi conosce i codici di errore HTTP e può automaticamente riprovare a eseguire le richieste non riuscite. È possibile configurare il numero massimo di tentativi, oltre a un periodo di timeout per associare la latenza massima. 

- Interruzione del circuito. Se un'istanza continua a non riuscire a eseguire le richieste, la rete mesh di servizi la contrassegnerà temporaneamente come non disponibile. Dopo un periodo di backoff, proverà di nuovo l'istanza. È possibile configurare l'interruttore in base a diversi criteri, ad esempio il numero di errori consecutivi.  

- La rete mesh di servizi acquisisce le metriche sulle chiamate tra i servizi, ad esempio il volume della richiesta, la latenza, le percentuali di errore e riuscita e le dimensioni della risposta. La rete mesh di servizi abilita anche l'analisi distribuita aggiungendo le informazioni di correlazione per ogni hop in una richiesta.

- Autenticazione TLS reciproca per le chiamate da servizio a servizio.

Se è necessaria una rete mesh di servizi, tenere in considerazione che il valore aggiunto a un sistema distribuito è indubbiamente interessante. Se non si ha una rete mesh di servizi, sarà necessario valutare ognuna delle problematiche esposte all'inizio del capitolo. È possibile risolvere problemi come quelli relativi ai nuovi tentativi, agli interruttori di circuito e alle analisi distribuite anche senza una rete mesh di servizi, ma con una rete mesh di servizi queste problematiche passano dai singoli servizi a un livello dedicato. D'altra parte, le reti mesh di servizi sono una tecnologia relativamente nuova ancora da perfezionare. La distribuzione di una rete mesh di servizi aumenta la complessità dell'installazione e della configurazione del cluster. Le prestazioni potrebbero risentirne, perché le richieste ora vengono instradate tramite il proxy della rete mesh di servizi e perché in ogni nodo del cluster ora sono in esecuzione servizi aggiuntivi. È consigliabile eseguire test di carico e delle prestazioni completi prima di distribuire una rete mesh di servizi nell'ambiente di produzione.

> [!div class="nextstepaction"]
> [Progettazione API](./api-design.md)

<!-- links -->

[ingestion-workflow]: ./ingestion-workflow.md
