---
title: Modello Limitazione
titleSuffix: Cloud Design Patterns
description: Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: f9f33421f3e1030e2477379970082f5c45690390
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486859"
---
# <a name="throttling-pattern"></a>Modello Limitazione

[!INCLUDE [header](../_includes/header.md)]

Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio. Questa scelta può consentire al sistema di continuare a funzionare e soddisfare i contratti di servizio, anche quando un aumento della domanda genera un carico particolarmente intenso sulle risorse.

## <a name="context-and-problem"></a>Contesto e problema

Il carico su un'applicazione cloud varia in genere nel tempo in base al numero di utenti attivi o ai tipi di attività in esecuzione. Ad esempio, è probabile che più utenti siano attivi durante l'orario di lavoro oppure al sistema potrebbe essere richiesto di eseguire un'analisi impegnativa a livello di calcolo alla fine di ogni mese. È possibile anche che si verifichino picchi improvvisi e imprevisti nell'attività. Se i requisiti di elaborazione del sistema superano la capacità delle risorse disponibili, è possibile che le prestazioni non siano soddisfacenti e persino che si verifichi un errore del sistema. Se il sistema deve soddisfare un livello di servizio concordato, questo errore potrebbe non essere accettabile.

Sono disponibili numerose strategie per gestire carichi variabili nel cloud, a seconda degli obiettivi aziendali per l'applicazione. Una strategia consiste nell'usare la scalabilità automatica in modo che corrisponda alle risorse di cui è stato eseguito il provisioning all'utente in un momento specifico. Questa strategia è in grado di soddisfare in modo coerente la richiesta dell'utente, ottimizzando al contempo i costi in esecuzione. Tuttavia, mentre la scalabilità automatica può attivare il provisioning di risorse aggiuntive, questo provisioning non è immediato. Se la domanda cresce rapidamente, è possibile che le risorse in un intervallo di tempo non siano sufficienti.

## <a name="solution"></a>Soluzione

Una strategia alternativa alla scalabilità automatica consiste nel consentire alle applicazioni di usare le risorse solo fino a un limite e di limitarle al raggiungimento di questo limite. Il sistema deve monitorare la modalità di utilizzo delle risorse in modo che, quando l'utilizzo supera la soglia, sia possibile limitare le richieste provenienti da uno o più utenti. In questo modo il sistema continuerà a funzionare e soddisferà gli eventuali contratti di servizio applicabili. Per altre informazioni sul monitoraggio dell'utilizzo delle risorse, vedere [Indicazioni sulla strumentazione e la telemetria](https://msdn.microsoft.com/library/dn589775.aspx).

Il sistema potrebbe implementare diverse strategie di limitazione, tra cui:

- Rifiutare le richieste da un singolo utente che ha già effettuato l'accesso alle API di sistema più di n volte al secondo in un determinato periodo di tempo. Per tale scopo è necessario che il sistema calcoli l'utilizzo delle risorse per ogni tenant o utente che esegue un'applicazione. Per altre informazioni, vedere le [indicazioni sulla misurazione del servizio](https://msdn.microsoft.com/library/dn589796.aspx).

- Disabilitare o degradare la funzionalità di servizi non essenziali selezionati in modo che i servizi essenziali possano essere eseguiti senza problemi con risorse sufficienti. Ad esempio, se l'applicazione sta trasmettendo l'output video, potrebbe passare a una risoluzione inferiore.

- Usare il livellamento del carico per regolare il volume di attività; questo approccio è descritto in maggior dettaglio nello [schema di livellamento del carico basato sulle code](./queue-based-load-leveling.md). In un ambiente multi-tenant questo approccio ridurrà le prestazioni per ogni tenant. Se il sistema deve supportare un insieme di tenant con contratti di servizio diversi, le operazioni i tenant di alto valore possono essere eseguite immediatamente. Le richieste di altri tenant possono essere tratenute e gestite quando il backlog è diminuito. Per facilitare l'implementazione di questo approccio potrebbe essere usato il modello di coda con priorità.

- Rinviare operazioni eseguite per conto di tenant o di applicazioni con priorità più bassa. Queste operazioni possono essere sospese o limitate, con un'eccezione generata per informare il tenant che il sistema è occupato e che l'operazione dovrebbe essere ritentata successivamente.

La figura mostra un grafo ad area per l'utilizzo delle risorse (una combinazione di memoria, CPU, larghezza di banda e di altri fattori) rispetto al tempo per cui le applicazioni usano le tre funzioni. Una funzione è un'area di funzionalità, ad esempio un componente che esegue una serie specifica di attività, una parte del codice che esegue un calcolo complesso o un elemento che fornisce un servizio, ad esempio una cache in memoria. Queste funzioni sono etichettate come A, B e C.

![Figura 1: grafo che mostra l'utilizzo delle risorse rispetto al tempo di esecuzione delle applicazioni per conto di tre utenti](./_images/throttling-resource-utilization.png)

> L'area immediatamente sotto la linea per una funzione indica le risorse usate dalle applicazioni quando richiamano questa funzione. Ad esempio, l'area sotto la riga per la Funzione A mostra le risorse usate dalle applicazioni che usano la Funzione A e l'area tra le righe per la Funzione A e la Funzione B indica le risorse usate dalle applicazioni che richiamano la Funzione B. L'aggregazione delle aree per ogni funzione mostra l'utilizzo totale delle risorse del sistema.

La figura precedente illustra gli effetti delle operazioni di rinvio. Appena prima del tempo T1, le risorse totali assegnate a tutte le applicazioni che usano queste funzioni raggiungono una soglia, il limite di utilizzo di risorse. A questo punto, le applicazioni rischiano di esaurire le risorse disponibili. In questo sistema, la Funzione B è meno critica della Funzione A o della Funzione C e quindi è stata temporaneamente disattivata rilasciando le risorse che stava usando. Tra i tempi T1 e T2, le applicazioni che usano la Funzione A e la Funzione C continuano a funzionare normalmente. Alla fine, l'utilizzo delle risorse di queste due funzioni diminuisce al punto in cui, al tempo T2, è disponibile una capacità sufficiente per abilitare nuovamente la Funzione B.

Gli approcci di scalabilità automatica e limitazione possono anche essere combinati per mantenere reattive le applicazioni e in linea con i contratti di servizio. Se si prevede che la domanda rimarrà elevata, la limitazione fornisce una soluzione temporanea mentre il sistema esegue la scalabilità orizzontale. A questo punto è possibile ripristinare la funzionalità completa del sistema.

La figura seguente mostra un grafo ad area sull'utilizzo generale delle risorse da parte di tutte le applicazioni eseguite in un sistema rispetto al tempo e illustra come è possibile combinare la limitazione delle richieste con la scalabilità automatica.

![Figura 2: grafo che mostra gli effetti ottenuti combinando la limitazione delle richieste con la scalabilità automatica](./_images/throttling-autoscaling.png)

All'ora T1 viene raggiunta la soglia che specifica il limite flessibile di utilizzo di risorse. A questo punto il sistema può iniziare a scalare orizzontalmente. Tuttavia, se le nuove risorse non diventano disponibili abbastanza rapidamente, le risorse esistenti potrebbero esaurirsi e si potrebbe verificare un errore del sistema. Per evitare questo problema, il sistema viene limitato temporaneamente, come descritto in precedenza. Dopo che la scalabilità automatica è stata completata e che risorse aggiuntive sono diventate disponibili, è possibile ridurre la limitazione.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo schema, è opportuno considerare quanto segue:

- Limitare le richieste di un'applicazione e scegliere la strategia sono decisioni che influiscono sull'intera progettazione di un sistema. Non è facile aggiungere la limitazione delle richieste dopo l'implementazione di un sistema ed è quindi opportuno valutare questa scelta all'inizio del processo di progettazione.

- La limitazione delle richieste deve essere eseguita rapidamente. Il sistema deve essere in grado di rilevare un aumento di attività e reagire di conseguenza. Il sistema deve anche essere in grado di ripristinare lo stato originale rapidamente dopo che il carico è diminuito. A tale scopo è necessario che vengano costantemente acquisiti e monitorati i dati sulle prestazioni appropriati.

- Se un servizio deve negare temporaneamente una richiesta dell'utente, deve restituire un codice di errore specifico in modo che l'applicazione client sia consapevole che il motivo del rifiuto ad eseguire un'operazione è dovuto alla limitazione delle richieste. L'applicazione client può attendere prima di ritentare la richiesta.

- La limitazione delle richieste può essere usata come misura temporanea mentre il sistema scala automaticamente. In alcuni casi è preferibile limitare semplicemente le richieste, anziché eseguire la scalabilità, se un picco di attività è improvviso e non si prevede una lunga durata perché la scalabilità aumenta notevolmente i costi di gestione.

- Se la limitazione delle richieste viene usata come misura temporanea mentre il sistema scala automaticamente e se le richieste di risorse aumentano molto rapidamente, il sistema potrebbe non essere in grado di continuare a funzionare normalmente, anche se funziona in modalità limitata. Se questa condizione non è accettabile, è consigliabile, prendere in considerazione la possibilità di mantenere maggiori riserve di capacità e di configurare una scalabilità automatica più aggressiva.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Usare questo schema:

- Per garantire che un sistema continui a soddisfare i contratti di servizio.

- Per evitare a un singolo tenant di monopolizzare le risorse fornite da un'applicazione.

- Per gestire i picchi di attività.

- Per ottimizzare i costi di un sistema limitando i livelli massimi di risorse necessari per mantenerlo funzionante.

## <a name="example"></a>Esempio

La figura finale illustra come la limitazione delle richieste può essere implementata in un sistema multi-tenant. Gli utenti di ciascuna organizzazione tenant accedono a un'applicazione ospitata nel cloud dove compilano e inviano sondaggi. L'applicazione contiene la strumentazione che monitora la velocità con cui questi utenti inviano richieste all'applicazione.

Per impedire agli utenti di un tenant di compromettere i tempi di risposta e la disponibilità dell'applicazione per tutti gli altri utenti, il numero di richieste al secondo che gli utenti di qualsiasi tenant possono inviare viene limitato. L'applicazione blocca le richieste che superano questo limite.

![Figura 3: implementazione della limitazione delle richieste in un'applicazione multi-tenant](./_images/throttling-multi-tenant.png)

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:

- [Indicazioni sulla strumentazione e la telemetria](https://msdn.microsoft.com/library/dn589775.aspx). La limitazione dipende dalla raccolta di informazioni sulla frequenza di utilizzo di un servizio. Descrive come generare e acquisire informazioni di monitoraggio personalizzate.
- [Indicazioni sulla misurazione del servizio](https://msdn.microsoft.com/library/dn589796.aspx). Descrive come controllare l'utilizzo dei servizi per comprenderne le modalità di utilizzo. Queste informazioni possono essere utili per determinare come limitare un servizio.
- [Scalabilità automatica](https://msdn.microsoft.com/library/dn589774.aspx). La limitazione delle richieste può essere usata come misura provvisoria mentre un sistema esegue la scalabilità automatica o per non rendere necessaria questa operazione. Contiene informazioni sulle strategie di scalabilità automatica.
- [Schema di livellamento del carico basato sulle code](./queue-based-load-leveling.md). Il livellamento del carico basato sulle code è un meccanismo di utilizzo comune per implementare la limitazione delle richieste. Una coda può fungere da buffer che aiuta a bilanciare la velocità con cui le richieste inviate da un'applicazione vengono recapitate a un servizio.
- [Schema di coda di priorità](./priority-queue.md). Un sistema può usare l'accodamento prioritario nell'ambito della strategia di limitazione per garantire le prestazioni per le applicazioni strategiche o di livello superiore, riducendo al contempo le prestazioni delle applicazioni meno importanti.
