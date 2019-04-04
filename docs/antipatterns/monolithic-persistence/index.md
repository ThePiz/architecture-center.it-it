---
title: Antipattern di persistenza monolitica
titleSuffix: Performance antipatterns for cloud apps
description: L'inserimento di tutti i dati dell'applicazione in un unico archivio dati può influire negativamente sulle prestazioni.
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: e3d6fbb789e422af22ce54b0defd6bb73099f15a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58346332"
---
# <a name="monolithic-persistence-antipattern"></a>Antipattern di persistenza monolitica

L'inserimento di tutti i dati di un'applicazione in un unico archivio dati può influire negativamente sulle prestazioni o perché causa una contesa delle risorse o perché l'archivio dati non è adatto per alcuni dei dati.

## <a name="problem-description"></a>Descrizione del problema

In passato le applicazioni usavano spesso un unico archivio dati, indipendentemente dai diversi tipi di dati che l'applicazione doveva archiviare. In genere di faceva questo per semplificare la progettazione dell'applicazione, oppure per conformità con il set di competenze del team di sviluppo.

I moderni sistemi basati su cloud hanno spesso requisiti ulteriori funzionali e non funzionali e devono archiviare molti tipi di dati eterogenei, come documenti, immagini, dati memorizzati nella cache, messaggi in coda, log di applicazioni e dati di telemetria. Seguendo l'approccio tradizionale e inserendo tutte queste informazioni nello stesso archivio dati, si può influire negativamente sulle prestazioni, per due motivi:

- Archiviare e recuperare grandi quantità di dati non correlati nello stesso archivio dati può causare contesa, che a sua volta rallenta i tempi di risposta e causa errori di connessione.
- Qualsiasi archivio dati si scelga, potrebbe non essere la scelta migliore per tutti i diversi tipi di dati o non potrebbe non essere ottimizzato per le operazioni eseguite dall'applicazione.

L'esempio seguente illustra un controller API Web ASP.NET che aggiunge un nuovo record a un database e registra anche il risultato in un log. Il log viene mantenuto attivo nello stesso database dei dati aziendali. L'esempio completo è disponibile [qui][sample-app].

```csharp
public class MonoController : ApiController
{
    private static readonly string ProductionDb = ...;

    public async Task<IHttpActionResult> PostAsync([FromBody]string value)
    {
        await DataAccess.InsertPurchaseOrderHeaderAsync(ProductionDb);
        await DataAccess.LogAsync(ProductionDb, LogTableName);
        return Ok();
    }
}
```

La frequenza con cui vengono generati i record di log probabilmente influirà sulle prestazioni delle operazioni di business. E se un altro componente, ad esempio un monitor di processi applicativi, legge ed elabora regolarmente i dati di log, anche questo può influire sulle operazioni di business.

## <a name="how-to-fix-the-problem"></a>Come risolvere il problema

Separare i dati in base al relativo utilizzo. Per ogni set di dati, selezionare un archivio dati che corrisponde alla modalità di utilizzo del set di dati. Nell'esempio precedente l'applicazione deve creare i log in un archivio separato dal database che contiene i dati aziendali:

```csharp
public class PolyController : ApiController
{
    private static readonly string ProductionDb = ...;
    private static readonly string LogDb = ...;

    public async Task<IHttpActionResult> PostAsync([FromBody]string value)
    {
        await DataAccess.InsertPurchaseOrderHeaderAsync(ProductionDb);
        // Log to a different data store.
        await DataAccess.LogAsync(LogDb, LogTableName);
        return Ok();
    }
}
```

## <a name="considerations"></a>Considerazioni

- Separare i dati in base al modo in cui sono utilizzati e al modo in cui vi si accede. Ad esempio, non archiviare informazioni di log e dati aziendali nello stesso archivio dati. Questi tipi di dati hanno requisiti e modelli di accesso molto diversi. I record di log sono intrinsecamente sequenziali, mentre i dati aziendali è più probabile che richiedano l'accesso casuale e spesso sono relazionali.

- Prendere in considerazione il modello di accesso ai dati per ogni tipo di dati. Ad esempio, archiviare i report e i documenti formattati in un database di documenti, come [DB Cosmos][CosmosDB], ma usare [Cache Redis di Azure][Azure-cache] per i dati temporanei di cache.

- Se si segue questo materiale sussidiario ma si raggiungono comunque i limiti del database, potrebbe essere necessario ridimensionare il database. Considerare anche la scalabilità orizzontale e il partizionamento del carico tra i server di database. Tuttavia, il partizionamento può richiedere la riprogettazione dell'applicazione. Per altre informazioni, vedere le informazioni sul [partizionamento dei dati][DataPartitioningGuidance].

## <a name="how-to-detect-the-problem"></a>Come rilevare il problema

Il sistema probabilmente rallenterà molto e alla fine si bloccherà quando esaurirà le risorse, ad esempio le connessioni al database.

È possibile eseguire la procedura seguente per identificare la causa.

1. Instrumentare il sistema per registrare le statistiche di prestazioni chiave. Acquisire informazioni sui tempi per ogni operazione, nonché i punti in cui l'applicazione legge e scrive i dati.
2. Se possibile, monitorare il sistema in esecuzione per alcuni giorni in un ambiente di produzione per ottenere una visualizzazione reale dell'utilizzo del sistema. Se questo non è possibile, eseguire test di carico tramite script con un volume realistico di utenti virtuali che eseguono una tipica serie di operazioni.
3. Usare i dati di telemetria per identificare i periodi di riduzione delle prestazioni.
4. Identificare gli archivi di dati che erano accessibili durante tali periodi.
5. Identificare le risorse di archiviazione dati che potrebbero avere conflitti.

## <a name="example-diagnosis"></a>Diagnosi di esempio

Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.

### <a name="instrument-and-monitor-the-system"></a>Instrumentare e monitorare il sistema

Il grafico seguente mostra i risultati del test di carico dell'applicazione di esempio descritto sopra. Il test ha utilizzato un carico per passaggio fino a 1000 utenti simultanei.

![Risultati di prestazioni del test sul carico per il controller basato su SQL][MonolithicScenarioLoadTest]

Mentre il carico aumenta fino a 700 utenti, la velocità effettiva fa lo stesso. Ma a questo punto, la velocità effettiva si stabilizza, ma il sistema sembra eseguirsi alla capacità massima. La risposta media aumenta gradualmente con il carico utente, il che mostra che il sistema non riesca a far fronte alla domanda.

### <a name="identify-periods-of-poor-performance"></a>Identificare i periodi di riduzione delle prestazioni

Monitorando il sistema di produzione è possibile notare determinati schemi. Ad esempio, i tempi di risposta potrebbero peggiorare notevolmente alla stessa ora ogni giorno. Questo potrebbe essere causato da un carico di lavoro periodico o da un processo batch pianificato o semplicemente dal fatto che in determinati orari ci sono più utenti connessi al sistema. È consigliabile concentrarsi sui dati di telemetria per questi eventi.

Cercare correlazioni tra i tempi di risposta maggiori e l'aumento dell'attività del database o dell'I/O alle risorse condivise. Se sono presenti correlazioni, significa che il database potrebbe rappresentare un collo di bottiglia.

### <a name="identify-which-data-stores-are-accessed-during-those-periods"></a>Identificare gli archivi dati che sono accessibili durante tali periodi

Il grafico successivo illustra l'uso delle unità di trasmissione dati (DTU) durante il test di carico. (una DTU è una misura della capacità disponibile ed è una combinazione di utilizzo della CPU, allocazione della memoria e velocità di I/O) L'utilizzo delle DTU ha raggiunto rapidamente il 100%. Questo è approssimativamente il punto in cui la velocità effettiva ha avuto un picco nel grafico precedente. L'utilizzo del database è rimasto molto elevato fino alla fine del test. C'è un lieve calo verso la fine che potrebbe essere causato dalla limitazione, dalla concorrenza per le connessioni al database o da altri fattori.

![Il monitor del database nel portale di Azure classico che mostra l'utilizzo delle risorse del database][MonolithicDatabaseUtilization]

### <a name="examine-the-telemetry-for-the-data-stores"></a>Esaminare la telemetria per gli archivi dati

Instrumentare gli archivi dati per acquisire i dettagli di basso livello dell'attività. Nell'applicazione di esempio, le statistiche di accesso ai dati hanno mostrato un volume elevato di operazioni di inserimento eseguite sia sulla tabella `PurchaseOrderHeader` che sulla tabella `MonoLog`.

![Le statistiche di accesso ai dati per l'applicazione di esempio][MonolithicDataAccessStats]

### <a name="identify-resource-contention"></a>Identificare la contesa delle risorse

A questo punto è possibile esaminare il codice sorgente, con particolare attenzione ai punti in cui l'applicazione accede a risorse contese. Cercare situazioni come:

- Dati logicamente separati che vengono scritti allo stesso archivio. I dati come log, report e i messaggi in coda non devono essere mantenuti nello stesso database delle informazioni aziendali.
- Una mancata corrispondenza tra il tipo di archivio dati e il tipo di dati, ad esempio BLOB di grandi dimensioni o documenti XML in un database relazionale.
- Dati con modelli di utilizzo notevolmente diversi che condividono lo stesso archivio, ad esempio dati a elevata intensità di scrittura e bassa intensità di lettura archiviati insieme a dati a bassa intensità di scrittura e alta intensità di lettura.

### <a name="implement-the-solution-and-verify-the-result"></a>Implementare la soluzione e verificare il risultato

L'applicazione è stata modificata in modo che scriva i log in un archivio dati separato. Ecco i risultati del test di carico:

![Risultati di prestazioni del test di carico con l'uso del controller Polyglot][PolyglotScenarioLoadTest]

Il modello di velocità effettiva è simile al grafico precedente, ma il punto in cui le prestazioni raggiungono il picco è superiore di circa 500 richieste al secondo. Il tempo di risposta medio è minimo da un punto di vista marginale. Tuttavia queste statistiche non mostrano tutto. La telemetria per il database aziendale mostra che l'utilizzo di DTU raggiunge il picco intorno al 75% anziché al 100%.

![Il monitor del database nel portale di Azure classico che mostra l'utilizzo delle risorse del database nello scenario Polyglot][PolyglotDatabaseUtilization]

Analogamente, l'utilizzo massimo di DTU del database di log arriva solo a circa il 70%. I database non sono più il fattore limitante delle prestazioni del sistema.

![Il monitor del database nel portale di Azure classico che mostra l'utilizzo delle risorse del database di log nello scenario Polyglot][LogDatabaseUtilization]

## <a name="related-resources"></a>Risorse correlate

- [Choose the right data store][data-store-overview] (Scegliere il giusto archivio dati)
- [Criteri per la scelta di un archivio dati][data-store-comparison]
- [Data Access for Highly-Scalable Solutions: Using SQL, NoSQL, and Polyglot Persistence][Data-Access-Guide] (Accesso ai dati per le soluzioni altamente scalabili mediante SQL, NoSQL e persistenza Polyglot)
- [Partizionamento dei dati][DataPartitioningGuidance]

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/MonolithicPersistence
[CosmosDB]: https://azure.microsoft.com/services/cosmos-db/
[Azure-cache]: /azure/redis-cache/
[Data-Access-Guide]: https://msdn.microsoft.com/library/dn271399.aspx
[DataPartitioningGuidance]: ../../best-practices/data-partitioning.md
[data-store-overview]: ../../guide/technology-choices/data-store-overview.md
[data-store-comparison]: ../../guide/technology-choices/data-store-comparison.md

[MonolithicScenarioLoadTest]: _images/MonolithicScenarioLoadTest.jpg
[MonolithicDatabaseUtilization]: _images/MonolithicDatabaseUtilization.jpg
[MonolithicDataAccessStats]: _images/MonolithicDataAccessStats.jpg
[PolyglotScenarioLoadTest]: _images/PolyglotScenarioLoadTest.jpg
[PolyglotDatabaseUtilization]: _images/PolyglotDatabaseUtilization.jpg
[LogDatabaseUtilization]: _images/LogDatabaseUtilization.jpg
