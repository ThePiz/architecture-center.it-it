---
title: Considerazioni sui dati per i microservizi
description: Considerazioni sui dati per i microservizi
author: MikeWasson
ms.date: 12/08/2017
ms.openlocfilehash: 9bd7a8424309b5753b42cfb70559836288a3ce9d
ms.sourcegitcommit: c7f46b14ad7d55cf553b2d0b01045c9c25aefcdb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
ms.locfileid: "26587757"
---
# <a name="designing-microservices-data-considerations"></a>Progettazione di microservizi: considerazioni sui dati

Questo capitolo illustra le considerazioni sulla gestione dei dati in un'architettura di microservizi. Poiché ogni microservizio gestisce i propri dati, l'integrità e la coerenza dei dati sono problematiche critiche.

![](./images/data-considerations.png)

Un principio alla base dei microservizi è che ogni servizio gestisce i propri dati. Due servizi non devono condividere un archivio dati. Ogni servizio è invece responsabile del proprio archivio dati privato, a cui gli altri servizi non possono accedere direttamente.

Il motivo di questa regola è di evitare l'accoppiamento accidentale tra i servizi, come può accadere se i servizi condividono gli stessi schemi di dati sottostanti. Se viene apportata una modifica allo schema dei dati, la modifica deve essere coordinata tra ogni servizio basato su tale database. Isolando l'archivio dati di ogni servizio, è possibile limitare l'ambito della modifica e conservare la flessibilità offerta dalle distribuzioni effettivamente indipendenti. Un altro motivo è che ogni microservizio può avere i propri modelli di dati, query o modelli di lettura/scrittura. L'uso di un archivio dati condiviso limita la possibilità di ogni team di ottimizzare l'archiviazione dati per il proprio servizio specifico. 

![](../guide/architecture-styles/images/cqrs-microservices-wrong.png)

Questo approccio ha come naturale conseguenza la [programmazione poliglotta persistente](https://martinfowler.com/bliki/PolyglotPersistence.html), ovvero all'uso di più tecnologie di archiviazione dati in una singola applicazione. Un servizio potrebbe richiedere le funzionalità dello schema in lettura di un database di documenti. Per un altro potrebbe essere necessaria l'integrità referenziale fornita da un sistema di gestione di database relazionali. Ogni team può scegliere liberamente l'opzione migliore per il proprio servizio. Per altre informazioni sul principio generale della programmazione poliglotta persistente, vedere [Usare il migliore archivio dati per il processo](../guide/design-principles/use-the-best-data-store.md). 

> [!NOTE]
> La condivisione dello stesso server di database fisico è appropriata per i servizi. Il problema si verifica quando i servizi condividono lo stesso schema o leggono e scrivono nello stesso set di tabelle di database.


## <a name="challenges"></a>Problematiche

Alcune problematiche sorgono da questo approccio distribuito alle gestione dei dati. In primo luogo potrebbe verificarsi una ridondanza tra gli archivi dati, con lo stesso elemento di dati visualizzato in più punti. I dati, ad esempio, potrebbero venire memorizzati nell'ambito di una transazione, quindi memorizzati altrove per l'analisi, la creazione di report o l'archiviazione. I dati duplicati o partizionati possono causare problemi di integrità e coerenza dei dati. Quando le relazioni tra i dati si estendono su più servizi, non è possibile usare le tecniche tradizionali di gestione dati per applicare le relazioni.

La modellazione dei dati tradizionale usa la regola "un'operazione in una posizione". Ogni entità viene visualizzata un sola volta nello schema. Le altre entità possono contenere riferimenti a essa, ma non duplicarla. L'evidente vantaggio dell'approccio tradizionale è che gli aggiornamenti vengono eseguiti in una singola posizione, evitando così problemi di coerenza dei dati. In un'architettura di microservizi è necessario considerare come gli aggiornamenti vengono propagati nei servizi e come gestire la coerenza finale quando i dati vengono visualizzati in più posizioni senza coerenza assoluta. 

## <a name="approaches-to-managing-data"></a>Approcci alla gestione dei dati

Anche se non esiste un singolo approccio valido sempre, di seguito sono elencate alcune linee guida generali per la gestione dei dati in un'architettura di microservizi.

- Implementare la coerenza finale dove possibile. Conoscere le posizioni del sistema in cui è necessaria la coerenza assoluta o le transazioni ACID e le posizioni in cui la coerenza finale è accettabile.

- Quando sono necessarie garanzie di coerenza assoluta, un servizio può rappresentare l'origine di dati reali per una determinata entità, esposta tramite un'API. Gli altri servizi potrebbero contenere la propria copia dei dati o un subset dei dati, che presentano la coerenza finale con i dati master, ma non sono considerati l'origine di dati reali. Si prenda ad esempio un sistema di e-commerce con un servizio ordini cliente e un servizio elementi consigliati. Il servizio elementi consigliati potrebbe essere in ascolto degli eventi dal servizio ordini, ma se un cliente richiede un rimborso, è il servizio ordini e non il servizio elementi consigliati ad avere la cronologia completa delle transazioni.

- Per le transazioni, usare modelli come [Supervisione agente di pianificazione](../patterns/scheduler-agent-supervisor.md) e [Transazione di compensazione](../patterns/compensating-transaction.md) per mantenere i dati coerenti in più servizi.  Potrebbe essere necessario archiviare dati aggiuntivi che acquisiscono lo stato di un'unità di lavoro che si estende su più servizi, per evitare un errore parziale in più servizi, ad esempio, mantenere un elemento di lavoro in una coda durevole mentre è in corso una transazione in più passi. 

- Archiviare solo i dati necessari per un servizio. A un servizio potrebbe essere necessario solo un subset di informazioni su un'entità di dominio. Nel contesto delimitato per il recapito, ad esempio, è necessario conoscere il cliente associato a una determinata consegna, ma non l'indirizzo di fatturazione del cliente, gestito dal contesto delimitato per gli account. In questo caso, possono essere utili un'attenta valutazione del dominio e l'uso della progettazione basata su dominio. 

- Valutare se i servizi sono coerenti e a regime di controllo libero. Se due servizi si scambiano continuamente informazioni e le API risultano di conseguenza frammentate, potrebbe essere necessario ridisegnare i limiti tra i servizi, unendo i due servizi o effettuando il refactoring delle funzionalità.

- Usare uno [stile di architettura basato sugli eventi](../guide/architecture-styles/event-driven.md). In questo stile di architettura un servizio pubblica un evento quando vengono apportate modifiche alle entità o ai modelli pubblici. I servizi interessati possono sottoscrivere questi eventi. Un altro servizio, ad esempio, potrebbe usare gli eventi per creare una vista materializzata dei dati più adatta alle query. 

- Un servizio proprietario di eventi deve pubblicare uno schema che può essere usato per automatizzare la serializzazione e la deserializzazione degli eventi, per evitare un accoppiamento rigido tra server di pubblicazione e sottoscrittori. Si consideri lo schema JSON o un framework come [Microsoft Bond](https://github.com/Microsoft/bond), Protobuf o Avro.  
 
- Poiché in generale gli eventi possono diventare un collo di bottiglia nel sistema, prendere in considerazione la possibilità di usare l'aggregazione o l'invio in batch per ridurre il carico totale. 

## <a name="drone-delivery-choosing-the-data-stores"></a>Drone Delivery: scelta degli archivi dati 

Anche con solo pochi servizi, il contesto delimitato per il recapito illustra diversi punti discussi in questa sezione. 

Quando un utente pianifica una nuova consegna, la richiesta client include le informazioni sia sul recapito, ad esempio le località di ritiro e di consegna, che sul pacchetto, ad esempio le dimensioni e il peso. Queste informazioni definiscono un'unità di lavoro, che il servizio di inserimento invia a Hub eventi. È importante che l'unità di lavoro rimanga in una risorsa di archiviazione permanente mentre il servizio utilità di pianificazione esegue il flusso di lavoro, in modo che nessuna richiesta di recapito vada persa. Per altre informazioni sul flusso di lavoro, vedere [Inserimento e flusso di lavoro](./ingestion-workflow.md). 

I diversi servizi back-end gestiscono parti differenti delle informazioni nella richiesta e hanno anche profili di lettura e scrittura differenti. 

### <a name="delivery-service"></a>Servizio di recapito

Il servizio di recapito archivia le informazioni su ogni consegna attualmente pianificata o in corso. È in ascolto degli eventi provenienti dai droni e tiene traccia dello stato delle consegne in corso. Con gli aggiornamenti dello stato della consegna invia anche gli eventi di dominio.

È previsto che gli utenti controllino spesso lo stato di una consegna mentre sono in attesa del pacchetto. Il servizio di recapito richiede quindi un archivio dati che evidenzi la velocità effettiva (lettura e scrittura) nell'archiviazione a lungo termine. Il servizio di recapito non esegue nemmeno query o analisi complesse, ma si limita a recuperare lo stato più recente di una determinata consegna. Il team del servizio di recapito ha scelto Cache Redis di Azure per le prestazioni elevate delle operazioni di lettura e scrittura. Il ciclo di vita delle informazioni archiviate in Redis è relativamente breve. Dopo che una consegna viene portata a termine, il relativo record viene archiviato nel servizio cronologia di recapito.

### <a name="delivery-history-service"></a>Servizio cronologia di recapito

Il servizio cronologia di recapito è in ascolto degli eventi relativi allo stato di una consegna dal servizio di recapito. Memorizza questi dati in una risorsa di archiviazione a lungo termine. Per questi dati cronologici esistono due diversi casi d'uso con requisiti di archiviazione dati diversi. 

Il primo scenario consiste nell'aggregazione dei dati a scopo di analisi, per ottimizzare il business o migliorare la qualità del servizio. Si noti che il servizio cronologia di recapito non esegue l'analisi effettiva dei dati, ma è responsabile solo dell'inserimento e dell'archiviazione. Per questo scenario, la risorsa di archiviazione deve essere ottimizzata per l'analisi dei dati in un set di dati di grandi dimensioni, con un approccio basato sullo schema in lettura per gestire svariate origini dati. [Azure Data Lake Store](/azure/data-lake-store/) è l'ideale per questo scenario. Data Lake Store è un file system Apache Hadoop compatibile con HDFS (Hadoop Distributed File System) le cui prestazioni sono ottimizzate per gli scenari di analisi dei dati. 

Nell'altro scenario si consente agli utenti di cercare la cronologia di un recapito che è stato completato. Azure Data Lake non è particolarmente adatto a questo scenario. Per prestazioni ottimali, Microsoft consiglia di archiviare i dati relativi alle serie temporali in Data Lake in cartelle partizionate per data. Vedere [Ottimizzazione delle prestazioni di Azure Data Lake Store](/azure/data-lake-store/data-lake-store-performance-tuning-guidance). Tale struttura non è tuttavia l'ideale per la ricerca dei singoli record per ID. A meno di non conoscere anche il timestamp, una ricerca per ID richiede l'analisi dell'intera raccolta. Il servizio cronologia di recapito quindi archivia anche un subset di dati cronologici in Cosmos DB per ricerche più veloci. Non è necessario che i record rimangano in Cosmos DB a tempo indeterminato. I recapiti meno recenti possono essere archiviati, ad esempio dopo un mese, eseguendo un processo batch occasionale.

### <a name="package-service"></a>Servizio pacchetto

Il servizio pacchetto archivia le informazioni su tutti i pacchetti. I requisiti di archiviazione per il pacchetto sono: 

- Archiviazione a lungo termine.
- Possibilità di gestire un volume elevato di pacchetti, che richiede un velocità effettiva di scrittura elevata.
- Supporto di query semplici per ID pacchetto. Nessun join o requisito complesso per l'integrità referenziale.

Poiché i dati del pacchetto non sono relazionali, l'ideale è un database orientato ai documenti e Cosmos DB può raggiungere una velocità effettiva molto elevata usando raccolte partizionate. Poiché il team che usa il servizio pacchetto ha familiarità con lo stack MEAN (MongoDB, Express.js, AngularJS e Node.js), seleziona l'[API MongoDB](/azure/cosmos-db/mongodb-introduction) per Cosmos DB. In questo modo può sfruttare l'esperienza acquisita con MongoDB, mentre usufruisce dei vantaggi di Cosmos DB, che è un servizio gestito di Azure.

> [!div class="nextstepaction"]
> [Comunicazione tra i servizi](./interservice-communication.md)