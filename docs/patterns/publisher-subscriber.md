---
title: Modello di pubblicazione/sottoscrizione
description: Abilitare un'applicazione all'annuncio di eventi a più consumer interessati in modalità asincrona.
keywords: schema progettuale
author: alexbuckgit
ms.date: 12/07/2018
ms.openlocfilehash: f47792dd60e65cac1ff06928d9ea100ed452933a
ms.sourcegitcommit: a0a9981e7586bed8d876a54e055dea1e392118f8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53233939"
---
# <a name="publisher-subscriber-pattern"></a>Modello di pubblicazione/sottoscrizione

Abilitare un'applicazione all'annuncio di eventi a più consumer interessati in modalità asincrona, senza accoppiamento di mittenti e destinatari.

**Detto anche**: messaggistica pub/sub

## <a name="context-and-problem"></a>Contesto e problema

Nelle applicazioni basate su cloud e distribuite, i componenti del sistema spesso richiedono di fornire informazioni ad altri componenti appena si verificano gli eventi.

La messaggistica asincrona è un metodo efficace per separare i mittenti dai consumer ed evitare il blocco del mittente in attesa di una risposta. Tuttavia, l'uso di una coda di messaggi dedicata per ogni consumer non si adatta in modo efficace a molti consumer. Inoltre, alcuni dei consumer potrebbero essere interessati solo a un subset delle informazioni. Come può il mittente annunciare gli eventi a tutti i consumer interessati senza conoscere le loro identità?

## <a name="solution"></a>Soluzione

Introdurre un sottosistema di messaggistica asincrona che include quanto segue:

- Un canale di input di messaggistica usato dal mittente. Il mittente, usando un formato di messaggio conosciuto, crea dei pacchetti di eventi nei messaggi e invia questi messaggi tramite il canale di input. In questo modello, il mittente viene chiamato anche *server di pubblicazione*.

  > [!NOTE]
  > Un *messaggio* è un pacchetto di dati. Un *evento* è un messaggio che informa altri componenti di una modifica o un'azione che ha avuto luogo.

- Un canale di output di messaggistica per ogni consumer. I consumer sono detti *sottoscrittori*.

- Un meccanismo per la copia di ogni messaggio dal canale di input ai canali di output per tutti i sottoscrittori interessati al messaggio. Questa operazione viene in genere gestita da un intermediario, ad esempio un bus di eventi o broker di messaggi.

Il diagramma seguente mostra i componenti logici di questo modello:

![Modello di pubblicazione/sottoscrizione tramite un broker di messaggi](./_images/publish-subscribe.png)
 
La messaggistica pub/sub offre i seguenti vantaggi:

- Separa i sottosistemi che devono ancora comunicare. I sottosistemi possono essere gestiti in modo indipendente, mentre i messaggi possono essere gestiti correttamente anche se uno o più destinatari sono offline.

- Aumenta la scalabilità e migliora la velocità di risposta del mittente. Il mittente può inviare rapidamente un singolo messaggio al canale di input, quindi tornare alle responsabilità di elaborazione principali. L'infrastruttura di messaggistica è responsabile nel garantire che i messaggi vengano recapitati ai sottoscrittori interessati.

- Migliora l'affidabilità. La messaggistica asincrona consente alle applicazioni di continuare a eseguire carichi di lavoro elevati in modo uniforme, nonché a gestire in modo più efficace gli errori intermittenti.

- Permette l'elaborazione pianificata o posticipata. I sottoscrittori possono attendere fino a orari di meno traffico prima di prelevare i messaggi, oppure questi ultimi possono essere instradati o elaborati in base a una pianificazione specifica.

- Rende più semplice l'integrazione tra sistemi che usano piattaforme, linguaggi di programmazione o protocolli di comunicazione diversi, nonché tra sistemi locali e applicazioni in esecuzione nel cloud.

- Semplifica i flussi di lavoro asincroni in un'organizzazione.

- Migliora la testabilità. I canali possono essere monitorati e i messaggi controllati o registrati come parte di una strategia generale di test di integrazione.

- Fornisce la separazione degli interessi per le applicazioni. Ciascuna applicazione può concentrarsi sulle sue principali capacità, mentre l'infrastruttura di messaggistica gestisce tutte le operazioni necessarie per indirizzare i messaggi a più consumer in modo affidabile. 

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo modello, considerare quanto segue:

- **Tecnologie esistenti.** È altamente consigliato l'uso dei prodotti e servizi di messaggistica disponibili che supportano il modello di pubblicazione/sottoscrizione, anziché compilarne uno per conto proprio. In Azure, è consigliabile usare il [bus di servizio](/azure/service-bus-messaging/) oppure la [griglia di eventi](/azure/event-grid/). Altre tecnologie che possono essere usate per la messaggistica pub/sub includono Apache Kafka, RabbitMQ e Redis.

- **Gestione delle sottoscrizioni.** L'infrastruttura di messaggistica deve fornire meccanismi utili agli utenti per effettuare o annullare la sottoscrizione ai canali disponibili.

- **Sicurezza.** La connessione a un canale di messaggi deve essere limitata da criteri di sicurezza per impedire l'intercettazione da parte di utenti o applicazioni non autorizzate.

- **Subset di messaggi.** In genere, i sottoscrittori sono interessati solo a subset dei messaggi distribuiti da un server di pubblicazione. I servizi di messaggistica spesso consentono ridurre il set di messaggi ricevuti tramite limitazioni per:

  - **Argomenti.** Ogni argomento ha un canale di output dedicato e ogni consumer può effettuare la sottoscrizione a tutti gli argomenti pertinenti.
  - **Filtri dei contenuti.** I messaggi vengono ispezionati e distribuiti in base al contenuto di ogni messaggio. Ogni sottoscrittore può specificare il contenuto di suo interesse.

- **Sottoscrizioni con caratteri jolly.** È possibile consentire ai sottoscrittori di effettuare la sottoscrizione a più argomenti tramite caratteri jolly.

- **Comunicazioni bidirezionali.** I canali in un sistema di pubblicazione/sottoscrizione sono considerati come unidirezionali. Se uno specifico sottoscrittore deve inviare un riconoscimento o comunicare lo stato al server di pubblicazione, è consigliabile usare il [modello di richiesta/risposta](http://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html). Questo modello usa un canale per inviare il messaggio al sottoscrittore e un canale di risposta separato per comunicare nuovamente con il server di pubblicazione.

- **Ordinamento dei messaggi.** L'ordine in cui le istanze del consumer ricevono i messaggi non è garantito e non riflette necessariamente l'ordine di creazione dei messaggi. Progettare il sistema in modo da assicurarsi che l'elaborazione dei messaggi sia idempotente, in modo da eliminare qualsiasi dipendenza rispetto all'ordine di gestione dei messaggi.

- **Priorità dei messaggi.** Alcune soluzioni potrebbero richiedere l'elaborazione dei messaggi in un ordine specifico. Il [modello della coda di priorità](priority-queue.md) fornisce un meccanismo per garantire il recapito di messaggi specifici prima di altri.

- **Messaggi non elaborabili.** Un messaggio in formato non valido o un'attività che richiede l'accesso a risorse non disponibili può causare un errore in un'istanza del servizio. Il sistema deve impedire la restituzione di tali messaggi alla coda. Acquisire e archiviare i dettagli di questi messaggi in un'altra posizione, in modo da poterli analizzare se necessario.

- **Messaggi ripetuti.** Lo stesso messaggio potrebbe essere inviato più volte. Ad esempio, l'invio potrebbe non riuscire dopo la pubblicazione di un messaggio. Potrebbe quindi avviarsi una nuova istanza del mittente, ripetendo il messaggio. L'infrastruttura di messaggistica deve implementare il rilevamento e la rimozione dei messaggi duplicati, nota anche come deduplicazione, in base ai loro ID per garantire che siano recapitati solo una volta.

- **Scadenza dei messaggi.** Un messaggio potrebbe avere una durata limitata. Se non viene elaborato entro questo periodo di tempo, potrebbe non essere più rilevante e deve essere eliminato. Un mittente può specificare una scadenza come parte dei dati nel messaggio. Un ricevitore può esaminare queste informazioni prima di decidere se eseguire la logica di business associata al messaggio.

- **Pianificazione dei messaggi.** Un messaggio potrebbe essere temporaneamente bloccato e non deve essere elaborato fino a una data e ora specifiche. Il messaggio non deve essere disponibile per un ricevitore fino a quel momento.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo modello quando:

- Un'applicazione deve trasmettere informazioni a un numero significativo di consumer.

- Un'applicazione deve comunicare con uno o più servizi o applicazioni sviluppate in modo indipendente, i quali potrebbero usare piattaforme, linguaggi di programmazione e protocolli di comunicazione diversi.

- Un'applicazione può inviare informazioni ai consumer senza richiedere risposte in tempo reale da parte di questi ultimi.

- I sistemi integrati sono progettati per supportare un modello di coerenza finale per i loro dati.

- Un'applicazione deve comunicare informazioni a più consumer, i quali possono avere requisiti di disponibilità o pianificazioni dei tempi di attività diverse rispetto al mittente.

Questo modello potrebbe non essere utile quando:

- Un'applicazione ha solo pochi consumer che hanno bisogno di informazioni notevolmente diverse da parte dell'applicazione di produzione.

- Un'applicazione richiede un'interazione quasi in tempo reali con i consumer.

## <a name="example"></a>Esempio

Il diagramma seguente mostra un'architettura di integrazione aziendale che usa il bus di servizio per il coordinamento dei flussi di lavoro, mentre la griglia di eventi invia notifiche ai sottosistemi relative agli eventi che si verificano. Per altre informazioni, consultare [Integrazione aziendale in Azure con code ed eventi di messaggi](../reference-architectures/enterprise-integration/queues-events.md).

![Architettura di integrazione aziendale](../reference-architectures/enterprise-integration/_images/enterprise-integration-queues-events.png)

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

- [Scegliere tra i servizi di Azure che recapitano messaggi](/azure/event-grid/compare-messaging-services).

- Lo [stile di architettura guidato dagli eventi](../guide/architecture-styles/event-driven.md) è uno stile di architettura che usa la messaggistica pub/sub.

- [Introduzione alla messaggistica asincrona](https://msdn.microsoft.com/library/dn589781.aspx). Le code di messaggi sono un meccanismo di comunicazione asincrona. Se un servizio consumer deve inviare una risposta a un'applicazione, può essere necessario implementare una forma di messaggistica di risposta. L'articolo Introduzione alla messaggistica asincrona contiene informazioni sull'implementazione della messaggistica di richiesta/risposta tramite code di messaggi.

- [Modello osservatore](https://en.wikipedia.org/wiki/Observer_pattern). Il modello di pubblicazione/sottoscrizione si basa sul modello osservatore, separando i soggetti dagli osservatori tramite messaggistica asincrona.

- [Modello del broker di messaggi](https://en.wikipedia.org/wiki/Message_broker). Molti sottosistemi di messaggistica che supportano il modello di pubblicazione/sottoscrizione sono implementati tramite un broker messaggi.
