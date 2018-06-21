---
title: Analisi della modalità di errore
description: Linee guida per l'esecuzione dell'analisi della modalità di errore per soluzioni cloud basate su Azure.
author: MikeWasson
ms.date: 03/24/2017
ms.custom: resiliency
pnp.series.title: Design for Resiliency
ms.openlocfilehash: 95068bf8b1f5b559255e27819aaddb454d3427bc
ms.sourcegitcommit: d08f6ee27e1e8a623aeee32d298e616bc9bb87ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33810500"
---
# <a name="failure-mode-analysis"></a>Analisi della modalità di errore
[!INCLUDE [header](../_includes/header.md)]

L'analisi della modalità di errore (FMA) è un processo che consente di ottenere la resilienza in un sistema, identificando possibili punti di errore in esso presenti. La FMA deve essere eseguita durante le fasi di progettazione e architettura, in modo che sia possibile integrare il ripristino dagli errori nel sistema dall'inizio.

Ecco il processo generale per eseguire una FMA:

1. Identificare tutti i componenti nel sistema. Includere le dipendenze esterne, ad esempio i provider di identità, i servizi di terze parti e così via.   
2. Per ogni componente, identificare potenziali errori che potrebbero verificarsi. Un singolo componente può avere più di una modalità di errore. Ad esempio gli errori di lettura e gli errori di scrittura devono essere valutati separatamente, in quanto l'impatto e le misure di attenuazione sono diversi.
3. Classificare ogni modalità di errore in base al relativo rischio complessivo. Tenere presente questi fattori:  

   * Che cos'è la probabilità di errore. È abbastanza comune? Estremamente rara? Non è necessario ragionare su numeri esatti, in quanto lo scopo è stabilire una classificazione della priorità.
   * Qual è l'impatto sull'applicazione in termini di disponibilità, perdita dei dati, costo monetario e interruzione dell'operatività?
4. Per ogni modalità di errore, determinare il modo in cui l'applicazione risponde ed esegue il ripristino. Prendere in considerazione eventuali compromessi relativi ai costi e alla complessità dell'applicazione.   

Come punto di partenza per il processo di analisi della modalità di errore, questo articolo include un catalogo di potenziali modalità di errore e delle relative attenuazioni. Il catalogo è organizzato in base alla tecnologia o al servizio di Azure, oltre a una categoria generale per la progettazione a livello di applicazione. Il catalogo non è esaustivo, ma tiene conto di molti dei servizi di base di Azure.

## <a name="app-service"></a>Servizio app
### <a name="app-service-app-shuts-down"></a>L'app Servizio app di Azure si arresta.
**Rilevamento**. Possibili cause:

* Arresto previsto

  * Un operatore arresta l'applicazione, ad esempio tramite il portale di Azure.
  * L'app è stata scaricata perché era inattiva (solo se l'impostazione `Always On` è disabilitata).
* Arresto imprevisto

  * L'app si arresta in modo anomalo.
  * Un'istanza VM del Servizio app di Azure non è più disponibile.

La registrazione Application_End è l'unico modo per individuare gli arresti di dominio dell'app (arresto anomalo del processo software).

**Ripristino**

* Se l'arresto era previsto, usare l'evento di arresto dell'applicazione per spegnere normalmente il sistema. Ad esempio, in ASP.NET usare il metodo `Application_End`.
* Se l'applicazione è stata scaricata durante l'inattività, viene riavviata automaticamente alla richiesta successiva. Tuttavia, si incorrerà nei costi dell'"avvio a freddo".
* Per evitare che l'applicazione venga scaricata durante l'inattività, abilitare l'impostazione `Always On` nell'app Web. Vedere [Configurazione delle app Web in Servizio app di Azure][app-service-configure].
* Per impedire che un operatore arresti l'app, impostare un blocco di risorsa con livello `ReadOnly`. Vedere [Bloccare le risorse per impedire modifiche impreviste][rm-locks].
* In caso di arresto anomalo dell'app o se un'istanza VM del Servizio app di Azure non è più disponibile, il Servizio app riavvia automaticamente l'app.

**Diagnostica**. Log dell'applicazione e del server Web. Vedere [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure][app-service-logging].

### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>Un utente specifico esegue ripetutamente richieste non valide o sovraccarica il sistema.
**Rilevamento** Autenticare gli utenti e includere l'ID utente nei registri dell'applicazione.

**Ripristino**

* Usare la [Documentazione di Gestione API] [ api-management] per limitare le richieste da parte dell'utente. Vedere [Limitazione avanzata delle richieste con Gestione API di Azure][api-management-throttling]
* Bloccare l'utente.

**Diagnostica**. Registrare tutte le richieste di autenticazione.

### <a name="a-bad-update-was-deployed"></a>È stato distribuito un aggiornamento non valido.
**Rilevamento** Monitorare l'integrità dell'applicazione tramite il portale di Azure (vedere [Monitoraggio delle prestazioni dell'applicazione Web di Azure][app-insights-web-apps]) o implementare il [modello di monitoraggio degli endpoint per l'integrità] [health-endpoint-monitoring-pattern].

**Ripristino**. Usare più [slot di distribuzione] [ app-service-slots] ed eseguire il rollback all'ultima distribuzione valida. Per altre informazioni, vedere [Applicazione Web di base][ra-web-apps-basic].

## <a name="azure-active-directory"></a>Azure Active Directory
### <a name="openid-connect-oidc-authentication-fails"></a>L'autenticazione di OpenID Connect (OIDC) non riesce.
**Rilevamento** Le modalità di errore possibili includono:

1. Azure Active Directory non è disponibile o non è raggiungibile a causa di un problema di rete. Il reindirizzamento all'endpoint di autenticazione non riesce e il middleware OIDC genera un'eccezione.
2. Il tenant di Azure Active Directory non esiste. Il reindirizzamento all'endpoint di autenticazione restituisce un codice di errore HTTP e il middleware OIDC genera un'eccezione.
3. L'utente non riesce a eseguire l'autenticazione. Non è necessaria alcuna strategia di rilevamento; Azure AD gestisce gli errori di accesso.

**Ripristino**

1. Individuare le eccezioni non gestite dal middleware.
2. Gestire gli eventi `AuthenticationFailed`.
3. Reindirizzare l'utente a una pagina di errore.
4. Tentativi utente.

## <a name="azure-search"></a>Ricerca di Azure
### <a name="writing-data-to-azure-search-fails"></a>La scrittura dei dati in Ricerca di Azure non riesce.
**Rilevamento** Individuare gli errori `Microsoft.Rest.Azure.CloudException`.

**Ripristino**

L'[SDK Search .NET] [ search-sdk] esegue automaticamente nuovi tentativi dopo gli errori temporanei. Tutte le eccezioni generate dall'SDK client devono essere considerate come errori non temporanei.

Il criterio di ripetizione dei tentativi predefinito usa il backoff esponenziale. Per usare un criterio di ripetizione dei tentativi diverso, chiamare `SetRetryPolicy` sulla classe `SearchIndexClient` o `SearchServiceClient`. Per altre informazioni, vedere [Tentativi automatici][auto-rest-client-retry].

**Diagnostica**. Usare [Analisi del traffico di ricerca][search-analytics].

### <a name="reading-data-from-azure-search-fails"></a>La lettura dei dati da Ricerca di Azure non riesce.
**Rilevamento** Individuare gli errori `Microsoft.Rest.Azure.CloudException`.

**Ripristino**

L'[SDK Search .NET][search-sdk] esegue automaticamente nuovi tentativi dopo gli errori temporanei. Tutte le eccezioni generate dall'SDK client devono essere considerate come errori non temporanei.

Il criterio di ripetizione dei tentativi predefinito usa il backoff esponenziale. Per usare un criterio di ripetizione dei tentativi diverso, chiamare `SetRetryPolicy` sulla classe `SearchIndexClient` o `SearchServiceClient`. Per altre informazioni, vedere [Tentativi automatici][auto-rest-client-retry].

**Diagnostica**. Usare [Analisi del traffico di ricerca][search-analytics].

## <a name="cassandra"></a>Cassandra
### <a name="reading-or-writing-to-a-node-fails"></a>La lettura o la scrittura in un nodo non riesce.
**Rilevamento** Individuare l'eccezione. Per i client .NET, in genere è `System.Web.HttpException`. Altri client potrebbero avere altri tipi di eccezione.  Per altre informazioni, vedere la pagina relativa alla [gestione degli errori Cassandra eseguita correttamente](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right).

**Ripristino**

* Ogni [client Cassandra](https://wiki.apache.org/cassandra/ClientOptions) presenta capacità e criteri specifici di ripetizione dei tentativi. Per altre informazioni, vedere la pagina relativa alla [gestione degli errori Cassandra eseguita correttamente][cassandra-error-handling].
* Usare una distribuzione con riconoscimento del rack e i nodi di dati distribuiti nei domini di errore.
* Distribuire in più aree con coerenza del quorum locale. Se si verifica un errore non temporaneo, eseguire il failover in un'altra area.

**Diagnostica**. Log applicazioni

## <a name="cloud-service"></a>Servizio cloud
### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>I ruoli di lavoro o Web vengono arrestati in modo imprevisto.
**Rilevamento** Viene generato l'evento [RoleEnvironment.Stopping][RoleEnvironment.Stopping].

<strong>Ripristino</strong>. Eseguire l'override del metodo [RoleEntryPoint.OnStop][RoleEntryPoint.OnStop] per eseguire normalmente la pulizia. Per altre informazioni, vedere [Come gestire correttamente gli eventi OnStop d Azure][onstop-events] (blog).

## <a name="cosmos-db"></a>Cosmos DB 
### <a name="reading-data-fails"></a>La lettura dei dati non riesce.
**Rilevamento** Individuare `System.Net.Http.HttpRequestException` o `Microsoft.Azure.Documents.DocumentClientException`.

**Ripristino**

* L'SDK esegue automaticamente nuovi tentativi. Per impostare il numero di tentativi e il tempo di attesa massimo, configurare `ConnectionPolicy.RetryOptions`. Le eccezioni generate dal client escludono il criterio di ripetizione oppure non sono errori temporanei.
* Se Cosmos DB limita il client, viene restituito un errore HTTP 429. Verificare il codice di stato in `DocumentClientException`. Se l'errore 429 si verifica in modo coerente, è consigliabile aumentare il valore di throughput della raccolta.
    * Se si usa l'API di MongoDB, il servizio restituisce il codice di errore 16500 in caso di limitazione.
* Replicare il database Cosmos DB tra due o più aree. Tutte le repliche sono leggibili. Specificare il parametro `PreferredLocations` mediante gli SDK client. Si tratta di un elenco ordinato di aree di Azure. Tutte le letture verranno inviate alla prima area disponibile nell'elenco. Se la richiesta non riesce, il client proverà con altre aree nell'elenco seguendo l'ordine. Per altre informazioni, vedere [Procedura di configurazione della distribuzione globale in Azure Cosmos DB mediante l'API SQL][cosmosdb-multi-region].

**Diagnostica**. Registrare tutti gli errori sul lato client.

### <a name="writing-data-fails"></a>La scrittura dei dati non riesce.
**Rilevamento** Individuare `System.Net.Http.HttpRequestException` o `Microsoft.Azure.Documents.DocumentClientException`.

**Ripristino**

* L'SDK esegue automaticamente nuovi tentativi. Per impostare il numero di tentativi e il tempo di attesa massimo, configurare `ConnectionPolicy.RetryOptions`. Le eccezioni generate dal client escludono il criterio di ripetizione oppure non sono errori temporanei.
* Se Cosmos DB limita il client, viene restituito un errore HTTP 429. Verificare il codice di stato in `DocumentClientException`. Se l'errore 429 si verifica in modo coerente, è consigliabile aumentare il valore di throughput della raccolta.
* Replicare il database Cosmos DB tra due o più aree. Se l'area primaria non riesce, verrà alzata di livello per la scrittura un'area diversa. È inoltre possibile attivare manualmente un failover. L'SDK esegue l'individuazione e il routing automatici, pertanto dopo un failover il codice di applicazione continua a funzionare. Durante il periodo di failover (in genere minuti), le operazioni di scrittura avranno una latenza maggiore, in quanto l'SDK deve individuare la nuova area di scrittura.
  Per altre informazioni, vedere [Procedura di configurazione della distribuzione globale in Azure Cosmos DB mediante l'API SQL][cosmosdb-multi-region].
* Come fallback, rendere persistente il documento in una coda di backup ed elaborare la coda in un secondo momento.

**Diagnostica**. Registrare tutti gli errori sul lato client.

## <a name="elasticsearch"></a>Elasticsearch
### <a name="reading-data-from-elasticsearch-fails"></a>La lettura dei dati da Elasticsearch non riesce.
**Rilevamento** Individuare l'eccezione appropriata per il [client Elasticsearch] [ elasticsearch-client] specifico in uso.

**Ripristino**

* Usare un meccanismo di ripetizione dei tentativi. Ogni client presenta criteri specifici di ripetizione dei tentativi.
* Distribuire più nodi Elasticsearch e usare la replica per la disponibilità elevata.

Per altre informazioni, vedere [Esecuzione di Elasticsearch su Azure][elasticsearch-azure].

**Diagnostica**. È possibile usare gli strumenti di monitoraggio per Elasticsearch o registrare tutti gli errori sul lato client con il payload. Vedere la sezione "Monitoraggio" in [Esecuzione di Elasticsearch su Azure][elasticsearch-azure].

### <a name="writing-data-to-elasticsearch-fails"></a>La scrittura dei dati in Elasticsearch non riesce.
**Rilevamento** Individuare l'eccezione appropriata per il [client Elasticsearch] [ elasticsearch-client] specifico in uso.  

**Ripristino**

* Usare un meccanismo di ripetizione dei tentativi. Ogni client presenta criteri specifici di ripetizione dei tentativi.
* Se l'applicazione è in grado di tollerare un livello di coerenza ridotto, è consigliabile scrivere con l'impostazione `write_consistency` per `quorum`.

Per altre informazioni, vedere [Esecuzione di Elasticsearch su Azure][elasticsearch-azure].

**Diagnostica**. È possibile usare gli strumenti di monitoraggio per Elasticsearch o registrare tutti gli errori sul lato client con il payload. Vedere la sezione "Monitoraggio" in [Esecuzione di Elasticsearch su Azure][elasticsearch-azure].

## <a name="queue-storage"></a>Archiviazione code
### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>La scrittura di un messaggio in Archiviazione code di Azure non riesce sistematicamente.
**Rilevamento** Dopo *N* tentativi l'operazione di scrittura continua a non riuscire.

**Ripristino**

* Archiviare i dati in una cache locale e inoltrare le operazioni di scrittura per l'archiviazione in un secondo momento, quando il servizio diventa disponibile.
* Creare una coda secondaria e scrivere in tale coda se quella primaria non è disponibile.

**Diagnostica**. Usare le [metriche di archiviazione][storage-metrics].

### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>L'applicazione non è in grado di elaborare un determinato messaggio dalla coda.
**Rilevamento** (specifico dell'applicazione). Ad esempio il messaggio contiene dati non validi o la logica di business non riesce per qualche motivo.

**Ripristino**

Spostare il messaggio in una coda separata. Eseguire un processo separato per esaminare i messaggi in quella coda.

Usare le code di messaggistica del Bus di servizio di Microsoft Azure, che fornisce una funzionalità [coda di messaggi non recapitabili] [ sb-dead-letter-queue] a questo scopo.

> [!NOTE]
> Se si usano le code di archiviazione con Processi Web, WebJobs SDK fornisce la funzionalità incorporata di gestione di messaggi non elaborabili. Vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK][sb-poison-message].

**Diagnostica**. Usare la registrazione delle applicazioni.

## <a name="redis-cache"></a>Cache Redis
### <a name="reading-from-the-cache-fails"></a>La lettura dalla cache non riesce.
**Rilevamento** Individuare `StackExchange.Redis.RedisConnectionException`.

**Ripristino**

1. Riprovare in caso di errori temporanei. Cache Redis di Azure supporta la ripetizione integrata dei tentativi. Vedere [Linee guida per la ripetizione di tentativi di Cache Redis di Azure][redis-retry].
2. Considerare gli errori non temporanei come un mancato riscontro nella cache e usare l'origine dati originale.

**Diagnostica**. Usare [Come monitorare Cache Redis di Azure][redis-monitor].

### <a name="writing-to-the-cache-fails"></a>La scrittura nella cache non riesce.
**Rilevamento** Individuare `StackExchange.Redis.RedisConnectionException`.

**Ripristino**

1. Riprovare in caso di errori temporanei. Cache Redis di Azure supporta la ripetizione integrata dei tentativi. Vedere [Linee guida per la ripetizione di tentativi di Cache Redis di Azure][redis-retry].
2. Se l'errore è non temporaneo, ignorarlo e consentire alle altre transazioni di scrivere nella cache in un secondo momento.

**Diagnostica**. Usare [Come monitorare Cache Redis di Azure][redis-monitor].

## <a name="sql-database"></a>Database SQL
### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>Non è possibile connettersi al database nell'area primaria.
**Rilevamento** La connessione non riesce.

**Ripristino**

Prerequisito: Il database deve essere configurato per la replica geografica attiva. Vedere [Panoramica: gruppi di failover e replica geografica attiva][sql-db-replication].

* Per le query, leggere da una replica secondaria.
* Per inserimenti e aggiornamenti, eseguire manualmente il failover in una replica secondaria. Vedere [Configurare la replica geografica attiva per il database SQL di Azure nel portale di Azure e avviare il failover][sql-db-failover].

La replica usa una stringa di connessione diversa, pertanto sarà necessario aggiornare la stringa di connessione nella propria applicazione.

### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>Il client esaurisce le connessioni nel pool di connessioni.
**Rilevamento** Individuare gli errori `System.InvalidOperationException`.

**Ripristino**

* Ripetere l'operazione.
* Come piano di mitigazione, isolare il pool di connessioni per ciascun caso d'uso, in modo che un solo caso d'uso non possa dominare tutte le connessioni.
* Aumentare il pool di connessioni massimo.

**Diagnostica**. Log applicazioni.

### <a name="database-connection-limit-is-reached"></a>È stato raggiunto il limite di connessioni di database.
**Rilevamento** Il database SQL di Azure limita il numero di accessi, sessioni e ruoli di lavoro simultanei. I limiti dipendono dal livello di servizio. Per altre informazioni, vedere [Limiti delle risorse del database SQL di Azure][sql-db-limits].

Per rilevare questi errori, individuare `System.Data.SqlClient.SqlException` e controllare il valore di `SqlException.Number` per il codice di errore SQL. Per un elenco di codici di errore rilevanti, vedere [Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database][sql-db-errors].

**Ripristino**. Questi errori sono considerati temporanei, pertanto il problema si può risolvere con altri tentativi. Se si riscontrano questi errori in modo sistematico, valutare se non sia opportuno un ridimensionamento del database.

**Diagnostica**. - La query [sys.event_log][sys.event_log] restituisce connessioni di database riuscite, non riuscite e deadlock.

* Creare una [regola di avviso][azure-alerts] per le connessioni non riuscite.
* Abilitare il [controllo del database SQL di Microsoft Azure][sql-db-audit] e verificare la presenza di accessi non riusciti.

## <a name="service-bus-messaging"></a>Messaggistica del bus di servizio
### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>La lettura di un messaggio da una coda del bus di servizio non riesce.
**Rilevamento** Individuare le eccezioni dal client SDK. La classe base per le eccezioni del bus di servizio è [MessagingException][sb-messagingexception-class]. Se l'errore è temporaneo, a proprietà `IsTransient` è true.

Per altre informazioni, vedere [Eccezioni di messaggistica del bus di servizio][sb-messaging-exceptions].

**Ripristino**

1. Riprovare in caso di errori temporanei. Vedere [Bus di servizio - Linee guida per la ripetizione di tentativi][sb-retry].
2. I messaggi che non si riesce a recapitare a nessun ricevitore vengono inseriti nella *coda di messaggi non recapitabili*. Usare questa cosa per vedere i messaggi che non sono stati ricevuti. Non è prevista alcuna pulizia automatica della coda di messaggi non recapitabili. I messaggi rimangono nella coda fino a quando non vengono recuperati in modo esplicito. Vedere [Panoramica delle code dei messaggi non recapitabili del bus di servizio][sb-dead-letter-queue].

### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>La scrittura di un messaggio in una coda del bus di servizio non riesce.
**Rilevamento** Individuare le eccezioni dal client SDK. La classe base per le eccezioni del bus di servizio è [MessagingException][sb-messagingexception-class]. Se l'errore è temporaneo, a proprietà `IsTransient` è true.

Per altre informazioni, vedere [Eccezioni di messaggistica del bus di servizio][sb-messaging-exceptions].

**Ripristino**

1. Il client del bus di servizio esegue automaticamente nuovi tentativi dopo gli errori temporanei. Per impostazione predefinita, usa il backoff esponenziale. Superato il numero massimo di tentativi o trascorso il periodo di timeout massimo, il client genera un'eccezione. Per altre informazioni, vedere [Bus di servizio - Linee guida per la ripetizione di tentativi][sb-retry].
2. Se viene superata la quota della coda, il client genera [QuotaExceededException][QuotaExceededException]. Per informazioni dettagliate, vedere il messaggio di eccezione. Eliminare alcuni messaggi dalla coda prima di fare un nuovo tentativo e provare a usare il modello Interruttore per evitare che vengano fatti tentativi continuativi superando la quota. Assicurasi inoltre che la proprietà [BrokeredMessage.TimeToLive] non sia impostata su un valore troppo elevato.
3. All'interno di un'area è possibile migliorare la resilienza tramite [code o argomenti partizionati][sb-partition]. Una coda o un argomento non partizionato viene assegnato a un archivio di messaggistica. Se l'archivio di messaggistica in questione non è disponibile, tutte le operazioni eseguite sulla coda o sull'argomento avranno esito negativo. Una coda o un argomento partizionati sono suddivisi in più archivi di messaggistica.
4. Per aumentare la resilienza, creare due spazi dei nomi del bus di servizio in aree diverse e replicare i messaggi. È possibile usare sia la replica attiva sia quella passiva.

   * Replica attiva: il client invia tutti i messaggi a entrambe le code. Il ricevitore è in ascolto su entrambe le code. Contrassegnare i messaggi con un identificatore univoco, in modo che il client possa eliminare i messaggi duplicati.
   * Replica passiva: il client invia tutti i messaggi a una sola coda. Se si verifica un errore, il client esegue il fallback alla coda di altri. Il ricevitore è in ascolto su entrambe le code. Questo approccio riduce il numero di messaggi duplicati inviati. Tuttavia, il ricevitore deve comunque gestire i messaggi duplicati.

     Per altre informazioni, vedere l'[esempio di replica geografica][sb-georeplication-sample] e [Procedure consigliate per isolare le applicazioni del bus di servizio da interruzioni ed emergenze del servizio](/azure/service-bus-messaging/service-bus-outages-disasters/).

### <a name="duplicate-message"></a>Messaggio duplicato.
**Rilevamento** Esaminare le proprietà `MessageId` e `DeliveryCount` del messaggio.

**Ripristino**

* Se possibile, concepire le operazioni di elaborazione dei messaggi in modo che siano idempotenti. In caso contrario, archiviare gli ID dei messaggio che sono già stati elaborati e verificare l'ID prima di elaborare un messaggio.
* Abilitare il rilevamento dei duplicati creando la coda con `RequiresDuplicateDetection` impostato su true. Con questa impostazione, il bus di servizio elimina automaticamente qualsiasi messaggio inviato con lo stesso `MessageId` di un messaggio precedente.  Tenere presente quanto segue:

  * Questa impostazione impedisce di inserire nella coda messaggi duplicati. Non impedisce tuttavia a un ricevitore di elaborare più volte lo stesso messaggio.
  * Il rilevamento di duplicati prevede una finestra temporale. Se un duplicato viene inviato al di là di questa finestra, non verrà rilevato.

**Diagnostica**. Registrare messaggi duplicati.

### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>L'applicazione non è in grado di elaborare un determinato messaggio dalla coda.
**Rilevamento** (specifico dell'applicazione). Ad esempio il messaggio contiene dati non validi o la logica di business non riesce per qualche motivo.

**Ripristino**

Esistono due modalità di errore da considerare.

* Il ricevitore rileva l'errore. In questo caso spostare il messaggio nella coda di messaggi non recapitabili. Successivamente eseguire un processo separato per esaminare i messaggi in quella coda.
* Si verifica un errore del ricevitore durante l'elaborazione del messaggio &mdash;, ad esempio, a causa di un'eccezione non gestita. Per gestire questo caso, usare la modalità `PeekLock`. In questa modalità, se il blocco scade, il messaggio diventa disponibile per altri ricevitori. Se il messaggio supera il numero massimo di recapiti o la durata, il messaggio viene spostato automaticamente nella coda di messaggi non recapitabili.

Per altre informazioni, vedere [Panoramica delle code dei messaggi non recapitabili del bus di servizio][sb-dead-letter-queue].

**Diagnostica**. Ogni volta che l'applicazione sposta un messaggio alla coda di messaggi non recapitabili, scrivere un evento nel registro applicazione.

## <a name="service-fabric"></a>Service Fabric
### <a name="a-request-to-a-service-fails"></a>Una richiesta a un servizio non riesce.
**Rilevamento** Il servizio restituisce un errore.

**Ripristino**

* Individuare di nuovo un proxy (`ServiceProxy` o `ActorProxy`) e chiamare nuovamente il metodo di servizio/attore.
* **Servizio con stato**. Eseguire il wrapping delle operazioni sulle raccolte affidabili in una transazione. Se si verifica un errore, verrà eseguito il rollback della transazione. Se viene eseguito il pull da un coda di una richiesta, questa verrà elaborata nuovamente.
* **Servizio senza stato**. Se il servizio mantiene i dati in un archivio esterno, è necessario che tutte le operazioni siano idempotenti.

**Diagnostica**. Registro applicazioni

### <a name="service-fabric-node-is-shut-down"></a>Il nodo di Service Fabric è stato arrestato.
**Rilevamento** Un token di annullamento viene passato al metodo `RunAsync` del servizio. Service Fabric annulla l'attività prima dell'arresto del nodo.

**Ripristino**. Usare il token di annullamento per rilevare l'arresto. Quando Service Fabric richiede l'annullamento, completare tutte le operazioni e chiudere  `RunAsync` più rapidamente possibile.

**Diagnostica**. Log applicazioni

## <a name="storage"></a>Archiviazione
### <a name="writing-data-to-azure-storage-fails"></a>La scrittura dei dati in Archiviazione di Azure non riesce
**Rilevamento** Il client riceve errori durante la scrittura.

**Ripristino**

1. Ritentare l'operazione per il ripristino da errori temporanei. Il [criterio di ripetizione dei tentativi][Storage.RetryPolicies] nel client SDK viene gestito automaticamente.
2. Implementare il modello Interruttore per evitare di oltrepassare le limitazioni di archiviazione.
3. Se N tentativi non sono riusciti, eseguire un normale fallback. Ad esempio: 

   * Archiviare i dati in una cache locale e inoltrare le operazioni di scrittura per l'archiviazione in un secondo momento, quando il servizio diventa disponibile.
   * Se l'azione di scrittura viene eseguita in un ambito transazionale, è possibile compensare la transazione.

**Diagnostica**. Usare le [metriche di archiviazione][storage-metrics].

### <a name="reading-data-from-azure-storage-fails"></a>La lettura dei dati da Archiviazione di Azure non riesce.
**Rilevamento** Il client riceve errori durante la lettura.

**Ripristino**

1. Ritentare l'operazione per il ripristino da errori temporanei. Il [criterio di ripetizione dei tentativi][Storage.RetryPolicies] nel client SDK viene gestito automaticamente.
2. Per l'archiviazione RA-GRS, se la lettura dall'endpoint primario non riesce, provare a leggere dall'endpoint secondario. Il client SDK può gestire questa operazione automaticamente. Vedere [Replica di Archiviazione di Azure][storage-replication].
3. Se *N* tentativi hanno esito negativo, eseguire un'azione di fallback per ridurre le prestazioni in modo graduale. Ad esempio, se non è possibile recuperare un'immagine del prodotto non può essere recuperata dall'archivio, mostrare un'immagine segnaposto generica.

**Diagnostica**. Usare le [metriche di archiviazione][storage-metrics].

## <a name="virtual-machine"></a>Macchina virtuale
### <a name="connection-to-a-backend-vm-fails"></a>La connessione a una macchina virtuale di back-end non riesce.
**Rilevamento** Errori delle connessioni di rete.

**Ripristino**

* Distribuire almeno du macchine virtuali di back-end in un set di disponibilità dietro un bilanciamento del carico.
* Quando l'errore di connessione è temporaneo, talvolta i protocollo TCP riesce a ritentare l'invio del messaggio.
* Implementare un criterio di ripetizione dei tentativi nell'applicazione.
* Per gli errori persistenti o non temporanei, implementare il modello [Interruttore][circuit-breaker].
* Se la macchina virtuale chiamante supera il limite di rete in uscita, la coda in uscita si riempirà. Se la coda in uscita è completa in modo coerente, prendere in considerazione la scalabilità orizzontale.

**Diagnostica**. Registrare gli eventi in base ai limiti del servizio.

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>Un'istanza VM non è più disponibile o non è più integra.
**Rilevamento** Configurare il [probe di integrità][lb-probe] di Load Balancer che segnala se l'istanza di macchina virtuale è integra. Il probe deve verificare se le funzioni critiche rispondono correttamente.

**Ripristino**. Per ogni livello di applicazione, inserire più istanze di macchina virtuale nello stesso set di disponibilità e posizionare un bilanciamento del carico di fronte alle macchine virtuali. Se il probe di integrità non riesce, Load Balancer interrompe l'invio di nuove connessioni all'istanza non integra.

**Diagnostica**. - Usare l'[analisi dei log][lb-monitor] di Load Balancer.

* Configurare il sistema di monitoraggio per monitorare tutti gli endpoint di monitoraggio dello stato.

### <a name="operator-accidentally-shuts-down-a-vm"></a>L'operatore arrestato accidentalmente una macchina virtuale.
**Rilevamento** N/D

**Ripristino**. Impostare un blocco di risorsa con livello `ReadOnly`. Vedere [Bloccare le risorse per impedire modifiche impreviste][rm-locks].

**Diagnostica**. Usare i [Log attività di Azure][azure-activity-logs].

## <a name="webjobs"></a>WebJobs
### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>L'esecuzione del processo continuo viene interrotta quando l'host SCM è inattivo.
**Rilevamento** Passare un token di annullamento alla funzione WebJob. Per altre informazioni, vedere l'articolo sull' [arresto normale dei processi][web-jobs-shutdown].

**Ripristino**. Abilitare l'impostazione `Always On` nell'app Web. Per altre informazioni, vedere [Eseguire attività in background con Processi Web in Servizio app di Azure][web-jobs].

## <a name="application-design"></a>Progettazione di applicazioni
### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>L'applicazione non riesce a gestire un picco nelle richieste in arrivo.
**Rilevamento** Dipende dall'applicazione. Sintomi tipici:

* Il sito Web inizia a restituire codici di errore HTTP 5xx.
* I servizi dipendenti, ad esempio di database o archiviazione, iniziano a limitare le richieste. Cercare gli errori HTTP, ad esempio HTTP 429 (troppe richieste), a seconda del servizio.
* La lunghezza della coda aumenta.

**Ripristino**

* Scalare in orizzontale per gestire il carico maggiore.
* Ridurre gli errori per evitare che errori a cascata interferiscano con l'intera applicazione. Le strategie di mitigazione includono:

  * Implementare il [Modello Limitazione][throttling-pattern] per evitare di sovraccaricare i sistemi back-end.
  * Usare lo [schema di livellamento del carico basato sulle code][queue-based-load-leveling] per memorizzare nel buffer le richieste ed elaborarle a un ritmo adeguato.
  * Assegnare la priorità per alcuni client. Ad esempio, se l'applicazione prevede livelli gratuiti e a pagamento, limitare i clienti del livello gratuito, ma non quelli a pagamento. Vedere [Modello di coda con priorità][priority-queue-pattern].

**Diagnostica**. Usare [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure][app-service-logging]. Usare un servizio come [Azure Log Analytics][azure-log-analytics], [Application Insights][app-insights] o [New Relic][new-relic] per interpretare i log di diagnostica.

### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>Una delle operazioni in una transazione distribuita o un flusso di lavoro non riesce.
**Rilevamento** Dopo *N* tentativi, il problema persiste.

**Ripristino**

* Come piano di mitigazione, implementare il modello [Supervisore agente di pianificazione] [ scheduler-agent-supervisor] per gestire l'intero flusso di lavoro.
* Non riprovare ai timeout. La percentuale di successo per questo errore è bassa.
* Mettere il lavoro in coda per riprovare più tardi.

**Diagnostica**. Accedere a tutte le operazioni (riuscite e non riuscite), incluse le azioni di compensazione. Usare gli ID di correlazione, in modo che sia possibile tenere traccia di tutte le operazioni all'interno della stessa transazione.

### <a name="a-call-to-a-remote-service-fails"></a>Una chiamata a un servizio remoto non riesce.
**Rilevamento** Codice di errore HTTP.

**Ripristino**

1. Riprovare in caso di errori temporanei.
2. Se la chiamata non riesce dopo *N* tentativi, eseguire un'azione di fallback (specifica dell'applicazione).
3. Implementare il [modello di Interruttore] [ circuit-breaker] per evitare che si verifichino errori a catena.

**Diagnostica**. Accedere a tutti gli errori di chiamata remota.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul processo di FMA, vedere [Resilienza in base alla progettazione per i servizi cloud] [ resilience-by-design-pdf] (download PDF).

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: /azure/api-management/api-management-sample-flexible-throttling/
[app-insights]: /azure/application-insights/app-insights-overview/
[app-insights-web-apps]: /azure/application-insights/app-insights-azure-web-apps/
[app-service-configure]: /azure/app-service-web/web-sites-configure/
[app-service-logging]: /azure/app-service-web/web-sites-enable-diagnostic-log/
[app-service-slots]: /azure/app-service-web/web-sites-staged-publishing/
[auto-rest-client-retry]: https://github.com/Azure/autorest/tree/master/docs
[azure-activity-logs]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs/
[azure-alerts]: /azure/monitoring-and-diagnostics/insights-alerts-portal/
[azure-log-analytics]: /azure/log-analytics/log-analytics-overview/
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cosmosdb-multi-region]: /azure/cosmos-db/tutorial-global-distribution-sql-api
[elasticsearch-azure]: ../elasticsearch/index.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: /azure/load-balancer/load-balancer-monitor-log/
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview/#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: ../reference-architectures/app-service-web-app/basic-web-app.md
[redis-monitor]: /azure/redis-cache/cache-how-to-monitor/
[redis-retry]: ../best-practices/retry-service-specific.md#azure-redis-cache
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: /azure/azure-resource-manager/resource-group-lock-resources/
[sb-dead-letter-queue]: /azure/service-bus-messaging/service-bus-dead-letter-queues/
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: /azure/service-bus-messaging/service-bus-messaging-exceptions/
[sb-outages]: /azure/service-bus-messaging/service-bus-outages-disasters/#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: /azure/service-bus-messaging/service-bus-partitioning/
[sb-poison-message]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#poison
[sb-retry]: ../best-practices/retry-service-specific.md#service-bus
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: /azure/search/search-traffic-analytics/
[sql-db-audit]: /azure/sql-database/sql-database-auditing-get-started/
[sql-db-errors]: /azure/sql-database/sql-database-develop-error-messages/#resource-governance-errors
[sql-db-failover]: /azure/sql-database/sql-database-geo-replication-failover-portal/
[sql-db-limits]: /azure/sql-database/sql-database-resource-limits/
[sql-db-replication]: /azure/sql-database/sql-database-geo-replication-overview/
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: /azure/storage/storage-redundancy/
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: /azure/app-service-web/web-sites-create-web-jobs/
[web-jobs-shutdown]: /azure/app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful
