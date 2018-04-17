---
title: Linee guida per la progettazione di un’API
description: Linee guida su come creare un'API Web progettata correttamente.
author: dragon119
ms.date: 01/12/2018
pnp.series.title: Best Practices
ms.openlocfilehash: a8c4a81835ebd3ebdba2fd2cec624a9a9d5646f5
ms.sourcegitcommit: ea7108f71dab09175ff69322874d1bcba800a37a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="api-design"></a>Progettazione API

La maggior parte delle applicazioni Web moderne espone API che possono essere usate dai client per interagire con l'applicazione. Un'API Web progettata in modo adeguato deve supportare:

* **Indipendenza dalla piattaforma**. Qualsiasi client deve poter chiamare l'API indipendentemente da come è implementata internamente. A questo scopo è necessario usare protocolli standard e un meccanismo che consenta al client e al servizio Web di concordare il formato dei dati da scambiare.

* **Evoluzione del servizio**. L'API Web deve avere la possibilità di evolversi e aggiungere funzionalità indipendentemente dalle applicazioni client. Con l'evoluzione dell'API, le applicazioni client dovranno continuare a funzionare senza modifiche. Tutte le funzionalità dovranno essere individuabili per poter essere utilizzate appieno dalle applicazioni client.

Queste linee guida descrivono gli aspetti da considerare durante la progettazione di un'API Web.

## <a name="introduction-to-rest"></a>Introduzione a REST

Nel 2000, Roy Fielding ha proposto REST (Representational State Transfer) come approccio di architettura per la progettazione di servizi Web. REST è uno stile architetturale per la creazione di sistemi distribuiti basati su ipermedia. REST è indipendente da qualsiasi protocollo sottostante e non è necessariamente collegato a HTTP. Le implementazioni REST più comuni, tuttavia, usano HTTP come protocollo applicativo e questa guida è incentrata sulla progettazione di API REST per HTTP.

Uno dei vantaggi principali di REST su HTTP è costituito dal fatto che usa standard aperti e non vincola l'implementazione dell'API o le applicazioni client a un'implementazione specifica. Per un servizio Web REST scritto in ASP.NET, ad esempio, le applicazioni client possono usare qualsiasi linguaggio o set di strumenti che può generare richieste HTTP e analizzare risposte HTTP.

Di seguito sono riportati alcuni fondamentali principi di progettazione delle API RESTful che usano HTTP:

- Le API REST sono progettate in base a *risorse*, che possono essere costituite da qualsiasi tipologia di oggetto, servizio o dati accessibile dal client. 

- Una risorsa ha un *identificatore*, costituito da un URI che identifica in modo univoco la risorsa. L'URI per uno specifico ordine cliente può essere ad esempio: 
 
    ```http
    http://adventure-works.com/orders/1
    ```
 
- I client interagiscono con un servizio scambiando *rappresentazioni* delle risorse. Molte API Web usano JSON come formato di scambio. Una richiesta GET all'URI riportato sopra, ad esempio, potrebbe restituire questo corpo della risposta:

    ```json
    {"orderId":1,"orderValue":99.90,"productId":1,"quantity":1}
    ```

- Le API REST usano un'interfaccia uniforme che consente di separare le implementazioni del client e del servizio. Per le API REST basate su HTTP, l'interfaccia uniforme include l'uso di verbi HTTP standard per l'esecuzione di operazioni sulle risorse. Le operazioni più comuni sono GET, POST, PUT, PATCH e DELETE. 

- Le API REST usano un modello di richiesta senza stato. Le richieste HTTP devono essere indipendenti e possono essere eseguite in qualsiasi ordine, di conseguenza non è possibile mantenere informazioni temporanee sullo stato tra le richieste. L'unico punto in cui sono memorizzate le informazioni sono le risorse stesse e ogni richiesta deve essere un'operazione atomica. Questo vincolo garantisce ai servizi Web un'elevata scalabilità, perché non è necessario mantenere alcuna affinità tra i client e server specifici. Qualsiasi server può gestire qualsiasi richiesta proveniente da qualsiasi client. La scalabilità può tuttavia essere limitata da altri fattori. Molti servizi Web, ad esempio, eseguono la scrittura in un archivio dati back-end di cui potrebbe essere difficile aumentare il numero di istanze. Le strategie per aumentare il numero di istanze di un archivio dati sono descritte nell'articolo [Partizionamento dei dati](./data-partitioning.md).

- Le API REST si basano su collegamenti ipermediali contenuti nella rappresentazione. Di seguito viene ad esempio illustrata una rappresentazione JSON di un ordine che contiene collegamenti per recuperare o aggiornare il cliente associato all'ordine. 
 
    ```json
    {
        "orderID":3,
        "productID":2,
        "quantity":4,
        "orderValue":16.60,
        "links": [
            {"rel":"product","href":"http://adventure-works.com/customers/3", "action":"GET" },
            {"rel":"product","href":"http://adventure-works.com/customers/3", "action":"PUT" } 
        ]
    } 
    ```


Nel 2008, Leonard Richardson ha proposto per le API Web il [modello di maturità](https://martinfowler.com/articles/richardsonMaturityModel.html) seguente.

- Livello 0: definizione di un URI e operazioni interamente costituite da richieste POST a tale URI.
- Livello 1: creazione di URI separati per le singole risorse.
- Livello 2: uso di metodi HTTP per definire le operazioni sulle risorse.
- Livello 3: uso di ipermedia (principio HATEOAS descritto di seguito).

Il livello 3 corrisponde a un'API realmente RESTful in base alla definizione di Fielding. In pratica, molte API Web pubblicate rientrano all'incirca nel livello 2.  

## <a name="organize-the-api-around-resources"></a>Organizzare l'API in base alle risorse

Concentrarsi sulle entità di business esposte dall'API Web. In un sistema di e-commerce, ad esempio, le entità primarie potrebbero essere i clienti e gli ordini. Un ordine può essere creato inviando una richiesta HTTP POST contenente le informazioni dell'ordine. La risposta HTTP indica se l'ordine è stato inserito correttamente o meno. Quando possibile, gli URI delle risorse devono essere basati su sostantivi (ovvero sulla risorsa) e non su verbi (ossia sulle operazioni sulla risorsa). 

```HTTP
http://adventure-works.com/orders // Good

http://adventure-works.com/create-order // Avoid
```

Non è necessario che una risorsa sia basata su un singolo elemento fisico di dati. Una risorsa ordine, ad esempio, potrebbe essere implementata internamente in diverse tabelle di un database relazionale, ma essere presentata al client come una singola entità. Evitare di creare API che rispecchiano semplicemente la struttura interna di un database. Lo scopo di REST è modellare entità e le operazioni che possono essere eseguite da un'applicazione su tali entità. L'implementazione interna non dovrebbe essere esposta a un client.

Le entità sono spesso raggruppate in raccolte (ad esempio, ordini e clienti). Una raccolta è una risorsa separata dall'elemento al suo interno e deve avere un proprio URI. L'URI seguente, ad esempio, potrebbe rappresentare la raccolta degli ordini: 

```HTTP
http://adventure-works.com/orders
```

Inviando una richiesta HTTP GET all'URI della raccolta viene recuperato un elenco degli elementi al suo interno. Ogni elemento della raccolta ha anche un proprio URI univoco. Una richiesta HTTP GET all'URI di un elemento restituisce i dettagli di tale elemento. 

Adottare una convenzione di denominazione coerente negli URI. In generale, per gli URI che fanno riferimento alle raccolte è utile usare sostantivi plurali. È consigliabile organizzare gli URI per le raccolte e gli elementi in una gerarchia. Se `/customers` è il percorso della raccolta dei clienti, ad esempio, `/customers/5` sarà il percorso del cliente il cui ID è 5. Questo approccio consente di mantenere l'API Web intuitiva. Molti framework API Web, inoltre, possono indirizzare le richieste in base a percorsi URI con parametri ed è quindi possibile definire una route per il percorso `/customers/{id}`.

Considerare anche le relazioni tra i diversi tipi di risorse e come si potrebbero esporre tali associazioni. Ad esempio, `/customers/5/orders` potrebbe rappresentare tutti gli ordini per il cliente 5. Si potrebbe procedere anche nella direzione opposta e rappresentare l'associazione da un ordine a un cliente con un URI come `/orders/99/customer`. Se questo modello viene esteso eccessivamente, tuttavia, la relativa implementazione può diventare complessa. È preferibile fornire collegamenti esplorabili alle risorse associate nel corpo del messaggio di risposta HTTP. Questo meccanismo viene descritto in modo più dettagliato nella sezione [Usare HATEOAS per consentire lo spostamento alle risorse correlate](#using-the-hateoas-approach-to-enable-navigation-to-related-resources) più avanti.

Nei sistemi più complessi si può essere tentati di fornire URI che consentono a un client di spostarsi tra diversi livelli di relazioni, ad esempio `/customers/1/orders/99/products`. Tuttavia questo livello di complessità può essere difficile da mantenere e non è flessibile se le relazioni tra le risorse cambiano in futuro. Provare invece a mantenere gli URI relativamente semplici. Quando un'applicazione contiene un riferimento a una risorsa, dovrebbe essere possibile usare questo riferimento per trovare gli elementi correlati a tale risorsa. La query precedente può essere sostituita con l'URI `/customers/1/orders` per trovare tutti gli ordini per il cliente 1 e quindi `/orders/99/products` per trovare i prodotti in tale ordine.

> [!TIP]
> Evitare di richiedere URI di risorse più complessi di *raccolta/elemento/raccolta*.

Un altro fattore è che tutte le richieste Web impongono un carico sul server Web. Maggiore è il numero di richieste, maggiore è il carico. Provare quindi a evitare API Web "frammentate" che espongono un numero elevato di risorse di piccole dimensioni. Un'API di questo tipo può richiedere che un'applicazione client invii più richieste per trovare tutti i dati necessari. Potrebbe essere invece opportuno denormalizzare i dati e combinare le informazioni correlate in risorse di maggiori dimensioni recuperabili con una singola richiesta. Questo approccio deve tuttavia essere bilanciato rispetto al sovraccarico associato al recupero di dati non necessari al client. Il recupero di oggetti di grandi dimensioni può aumentare la latenza di una richiesta e comportare costi aggiuntivi in termini di larghezza di banda. Per altre informazioni su questi anti-modelli delle prestazioni, vedere gli articoli relativi a [I/O frammentato](../antipatterns/chatty-io/index.md) e [recupero estraneo](../antipatterns/extraneous-fetching/index.md).

Evitare di introdurre dipendenze tra l'API Web e le origini dati sottostanti. Se i dati sono archiviati in un database relazionale, ad esempio, non è necessario che l'API Web esponga ogni tabella come raccolta di risorse. Questo potrebbe probabilmente rivelarsi un errore di progettazione. Considerare invece l'API Web come un'astrazione del database. Se necessario, introdurre un livello di mapping tra il database e l'API Web. In questo modo, le applicazioni client vengono isolate dalle modifiche allo schema di database sottostante.

Infine, potrebbe non essere possibile eseguire il mapping di ogni operazione implementata da un'API Web a una risorsa specifica. È possibile gestire tali scenari *non corrispondenti a una risorsa* tramite richieste HTTP che richiamano una funzione e restituiscono i risultati come messaggio di risposta HTTP. Un'API Web che implementa semplici operazioni aritmetiche come addizione e sottrazione, ad esempio, potrebbe fornire URI che espongono tali operazioni come pseudo-risorse e usare la stringa di query per specificare i parametri necessari. Una richiesta GET all'URI */add?operand1=99&operand2=1* restituirà ad esempio un messaggio di risposta il cui corpo contiene il valore 100. Utilizzare tuttavia questi formati di URI solo in casi limitati.

## <a name="define-operations-in-terms-of-http-methods"></a>Definire le operazioni in termini di metodi HTTP

Il protocollo HTTP definisce una serie di metodi che assegnano un significato semantico a una richiesta. I metodi HTTP usati dalla maggior parte delle API Web RESTful sono:

* **GET** recupera una rappresentazione della risorsa nell'URI specificato. Il corpo del messaggio di risposta contiene i dettagli della risorsa richiesta.
* **POST** crea una nuova risorsa nell'URI specificato. Il corpo del messaggio di richiesta fornisce i dettagli della nuova risorsa. Si noti che POST può anche essere usato per avviare operazioni che in realtà non creano risorse.
* **PUT** crea o sostituisce la risorsa nell'URI specificato. Il corpo del messaggio di richiesta specifica la risorsa da creare o aggiornare.
* **PATCH** esegue un aggiornamento parziale di una risorsa. Il corpo della richiesta specifica il set di modifiche da applicare alla risorsa.
* **DELETE** rimuove la risorsa nell'URI specificato.

L'effetto di una specifica richiesta varia a seconda che la risorsa sia una raccolta o un singolo elemento. La tabella seguente riepiloga le convenzioni comuni adottate dalla maggior parte delle implementazioni RESTful usando l’esempio dell’e-commerce. Si noti che potrebbe non essere possibile implementare tutte queste richieste. L’implementazione dipende dallo scenario specifico.

| **Risorsa** | **POST** | **GET** | **PUT** | **DELETE** |
| --- | --- | --- | --- | --- |
| /customers. |Creare un nuovo cliente |Recuperare tutti i clienti |Eseguire l'aggiornamento in blocco dei clienti |Rimuovere tutti i clienti |
| /customers/1 |Tipi di errore |Recuperare i dettagli per il cliente 1 |Aggiornare i dettagli del cliente 1, se esistente |Rimuovere il cliente 1 |
| /customers/1/orders |Creare un nuovo ordine per il cliente 1 |Recuperare tutti gli ordini per il cliente 1 |Eseguire l'aggiornamento in blocco degli ordini per il cliente 1 |Rimuovere tutti gli ordini per il cliente 1 |

Le differenze tra POST, PUT e PATCH possono risultare poco chiare.

- Una richiesta POST crea una risorsa. Il server assegna un URI per la nuova risorsa e restituisce tale URI al client. Nel modello REST, le richieste POST vengono spesso applicate alle raccolte. La nuova risorsa viene aggiunta alla raccolta. Una richiesta POST può essere usata anche per inviare dati a una risorsa esistente per l'elaborazione, senza che venga creata una nuova risorsa.

- Una richiesta PUT crea una risorsa *o* aggiorna una risorsa esistente. Il client specifica l'URI per la risorsa. Il corpo della richiesta contiene una rappresentazione completa della risorsa. Se esiste già una risorsa con tale URI, viene sostituita. In caso contrario viene creata una nuova risorsa, se questa operazione è supportata dal server. Le richieste PUT vengono applicate con maggiore frequenza ai singoli elementi, come un cliente specifico, anziché alle raccolte. Un server potrebbe supportare gli aggiornamenti ma non la creazione tramite PUT. Il supporto o meno della creazione tramite PUT dipende dalla possibilità per il client di assegnare un URI significativo a una risorsa non ancora esistente. Se il client non può, usare POST per creare le risorse e PUT o PATCH per l'aggiornamento.

- Una richiesta PATCH esegue un *aggiornamento parziale* di una risorsa esistente. Il client specifica l'URI per la risorsa. Il corpo della richiesta specifica un set di *modifiche* da applicare alla risorsa. Può essere più efficiente rispetto all'uso di PUT, perché il client invia solo le modifiche, non l'intera rappresentazione della risorsa. Tecnicamente, PATCH può anche creare una nuova risorsa, specificando un set di aggiornamenti per una risorsa "Null", se questa operazione è supportata dal server. 

Le richieste PUT devono essere idempotenti. Se un client invia la stessa richiesta PUT più volte, i risultati dovranno essere sempre uguali, ossia la stessa risorsa verrà modificata con gli stessi valori. Non è invece garantito che le richieste POST e PATCH siano idempotenti.

## <a name="conform-to-http-semantics"></a>Mantenere la conformità alla semantica HTTP

Questa sezione descrive alcune considerazioni tipiche per la progettazione di un'API conforme alla specifica HTTP. Non illustra tuttavia ogni possibile dettaglio o scenario. In caso di dubbi, consultare le specifiche HTTP.

### <a name="media-types"></a>Tipi di supporto

Come indicato in precedenza, i client e i server si scambiano rappresentazioni delle risorse. In una richiesta POST, ad esempio, il corpo della richiesta contiene una rappresentazione della risorsa da creare. In una richiesta GET, il corpo della risposta contiene una rappresentazione della risorsa recuperata.

Nel protocollo HTTP, i formati vengono specificati usando *tipi di supporto*, denominati anche tipi MIME. Per i dati non binari, la maggior parte delle API Web supporta JSON (ovvero il tipo di supporto application/json) ed eventualmente XML (ossia il tipo di supporto application/xml). 

L'intestazione Content-Type in una richiesta o una risposta specifica il formato della rappresentazione. Di seguito è riportato un esempio di richiesta POST contenente dati JSON:

```HTTP
POST http://adventure-works.com/orders HTTP/1.1
Content-Type: application/json; charset=utf-8
Content-Length: 57

{"Id":1,"Name":"Gizmo","Category":"Widgets","Price":1.99}
```

Se il server non supporta il tipo di supporto, deve restituire il codice di stato HTTP 415 (Tipo di supporto non supportato).

Una richiesta client può includere un'intestazione Accept contenente un elenco dei tipi di supporto accettati dal client nel messaggio di risposta proveniente dal server. Ad esempio: 

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
Accept: application/json
```

Se il server non può fornire un tipo di supporto corrispondente a quelli elencati, deve restituire il codice di stato HTTP 406 (Pagina non valida). 

### <a name="get-methods"></a>Metodi GET

Un metodo GET riuscito restituisce in genere il codice di stato HTTP 200 (OK). Se la risorsa non è stata trovata, il metodo deve restituire 404 (Non trovato).

### <a name="post-methods"></a>Metodi POST

Se un metodo POST crea una nuova risorsa, restituisce il codice di stato HTTP 201 (Creato). L'URI della nuova risorsa è incluso nell'intestazione Location della risposta. Il corpo della risposta contiene una rappresentazione della risorsa.

Se esegue alcune attività di elaborazione ma non crea una nuova risorsa, il metodo può restituire il codice di stato HTTP 200 e includere il risultato dell'operazione nel corpo della risposta. In alternativa, se non sono presenti risultati, il metodo può restituire il codice di stato HTTP 204 (Nessun contenuto) senza corpo della risposta.

Se il client inserisce dati non validi nella richiesta, il server deve restituire il codice di stato HTTP 400 (Richiesta non valida). Il corpo della risposta può contenere informazioni aggiuntive sull'errore o un collegamento a un URI che offre maggiori dettagli.

### <a name="put-methods"></a>Metodi PUT

Se un metodo PUT crea una nuova risorsa, restituisce il codice di stato HTTP 201 (Creato), così come un metodo POST. Se il metodo aggiorna una risorsa esistente, restituisce 200 (OK) o 204 (Nessun contenuto). In alcuni casi potrebbe non essere possibile aggiornare una risorsa esistente. In tale circostanza, valutare la possibilità di restituire il codice di stato HTTP 409 (Conflitto). 

Prendere in considerazione l’implementazione di operazioni HTTP PUT in blocco in grado di eseguire aggiornamenti in batch di più risorse in una raccolta. La richiesta PUT deve specificare l'URI della raccolta e il corpo della richiesta deve specificare i dettagli delle risorse da modificare. Questo approccio consente di ridurre la frammentarietà e migliorare le prestazioni.

### <a name="patch-methods"></a>Metodi PATCH

Con una richiesta PATCH, il client invia un set di aggiornamenti a una risorsa esistente, sotto forma di *documento di patch*. Il server elabora il documento di patch per eseguire l'aggiornamento. Il documento di patch non descrive l'intera risorsa, ma solo un set di modifiche da applicare. La specifica per il metodo PATCH ([RFC 5789](https://tools.ietf.org/html/rfc5789)) non definisce un particolare formato per i documenti di patch. Il formato deve essere dedotto dal tipo di supporto nella richiesta.

Il formato di dati più comune per le API Web è probabilmente JSON. Esistono due formati principali di patch basati su JSON, denominati *patch JSON* e *patch JSON di tipo merge*.

La patch JSON di tipo merge è un po' più semplice. Il documento di patch presenta la stessa struttura della risorsa JSON originale, ma include solo il subset di campi da modificare o aggiungere. È anche possibile eliminare un campo specificando `null` come valore del campo nel documento di patch. La patch di tipo merge non è quindi adatta se la risorsa originale può contenere valori Null espliciti.

Si supponga ad esempio che la risorsa originale abbia la rappresentazione JSON seguente:

```json
{ 
    "name":"gizmo",
    "category":"widgets",
    "color":"blue",
    "price":10
}
```

Di seguito è riportata una possibile patch JSON di tipo merge per tale risorsa:

```json
{ 
    "price":12,
    "color":null,
    "size":"small"
}
```

Questa patch indica al server di aggiornare "price", eliminare "color" e aggiungere "size". I campi "name" e "category" non vengono modificati. Per informazioni dettagliate precise sulla patch JSON di tipo merge, vedere la specifica [RFC 7396](https://tools.ietf.org/html/rfc7396). Il tipo di supporto per la patch JSON di tipo merge è "application/merge-patch+json".

La patch di tipo merge non è adatta se la risorsa originale può contenere valori Null espliciti, a causa del significato speciale di `null` nel documento di patch. Il documento di patch, inoltre, non specifica l'ordine con cui il server dovrà applicare gli aggiornamenti. Tale ordine può essere importante o meno a seconda dei dati e del dominio. La patch JSON, definita in [RFC 6902](https://tools.ietf.org/html/rfc6902), è più flessibile e specifica le modifiche come sequenza di operazioni da applicare. Le operazioni includono aggiunta, rimozione, sostituzione, copia e test (per convalidare i valori). Il tipo di supporto per la patch JSON è "application/json-patch+json".

Di seguito sono riportate alcune condizioni di errore tipiche che si possono verificare durante l'elaborazione di una richiesta PATCH, con il codice di stato HTTP appropriato.

| Condizione di errore | Stato codice HTTP |
|-----------|------------|
| Il formato del documento di patch non è supportato. | 415 (Tipo di supporto non supportato) |
| Il formato del documento di patch non è valido. | 400 (Richiesta non valida) |
| Il documento di patch è valido, ma le modifiche non possono essere applicate alla risorsa nello stato corrente. | 409 (Conflitto)

### <a name="delete-methods"></a>Metodi DELETE

Se l'operazione di eliminazione ha esito positivo, il server Web risponderà con il codice di stato HTTP 204, che indica che il processo è stato gestito correttamente, ma il corpo della risposta non conterrà altre informazioni. Se la risorsa non esiste, il server Web può restituire il codice di stato HTTP 404 (Non trovato).

### <a name="asynchronous-operations"></a>Operazioni asincrone

Un'operazione POST, PUT, PATCH o DELETE comporta talvolta attività di elaborazione il cui completamento richiede tempo. Attendere il completamento prima di inviare una risposta al client può causare una latenza inaccettabile. In tal caso, valutare la possibilità di rendere l'operazione asincrona. Restituire il codice di stato HTTP 202 (Accettato) per indicare che la richiesta è stata accettata per l'elaborazione ma non è stata completata. 

È consigliabile esporre un endpoint che restituisce lo stato di una richiesta asincrona, in modo che il client possa monitorare lo stato eseguendo il polling sull'endpoint di stato. Includere l'URI dell'endpoint di stato nell'intestazione Location della risposta 202. Ad esempio: 

```http
HTTP/1.1 202 Accepted
Location: /api/status/12345
```

Se il client invia una richiesta GET a questo endpoint, la risposta dovrà contenere lo stato corrente della richiesta. Facoltativamente, potrà includere anche il tempo di completamento stimato o un collegamento per annullare l'operazione. 

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "status":"In progress",
    "link": { "rel":"cancel", "method":"delete", "href":"/api/status/12345"
}
```

Se l'operazione asincrona crea una nuova risorsa, l'endpoint di stato deve restituire il codice di stato 303 (Vedi altro) al termine dell'operazione. Nella risposta 303 includere un'intestazione Location contenente l'URI della nuova risorsa:

```http
HTTP/1.1 303 See Other
Location: /api/orders/12345
```

Per altre informazioni, vedere [Asynchronous operations in REST](https://www.adayinthelifeof.nl/2011/06/02/asynchronous-operations-in-rest/) (Operazioni asincrone in REST).

## <a name="filter-and-paginate-data"></a>Filtrare e impaginare i dati

Esponendo una raccolta di risorse tramite un singolo URI, le applicazioni potrebbero recuperare grandi quantità di dati quando è necessario solo un subset delle informazioni. Si supponga ad esempio che un'applicazione client debba trovare tutti gli ordini con un costo superiore a un valore specificato. Potrebbe recuperare tutti gli ordini dall'URI */orders* e quindi filtrare tali ordini sul lato client. Questo processo è chiaramente molto inefficiente, perché comporta uno spreco di larghezza di banda di rete e di potenza di elaborazione nel server che ospita l'API Web.

L'API può invece consentire di passare un filtro nella stringa di query dell'URI, ad esempio */orders?minCost=n*. L'API Web sarà quindi responsabile dell'analisi e della gestione del parametro `minCost` nella stringa di query e della restituzione dei risultati filtrati sul lato server. 

Le richieste GET su risorse raccolta potrebbero restituire un numero elevato di elementi. È consigliabile progettare un'API Web in modo da limitare la quantità di dati restituita da ogni singola richiesta. Valutare la possibilità di supportare stringhe di query che specificano il numero massimo di elementi da recuperare e un offset iniziale nella raccolta. Ad esempio: 

```
/orders?limit=25&offset=50
```

Valutare anche la possibilità di imporre un limite massimo al numero di elementi restituiti, per prevenire attacchi Denial of Service. Per agevolare le applicazioni client, le richieste GET che restituiscono i dati impaginati devono includere anche un tipo di metadati che indichino il numero totale delle risorse disponibili nella raccolta. È anche possibile prendere in considerazione altre strategie di paging intelligente. Per altre informazioni, vedere [API Design Notes: Smart Paging](http://bizcoder.com/api-design-notes-smart-paging) (Note sulla progettazione di API: paging intelligente).

È possibile usare una strategia simile per ordinare i dati mentre vengono recuperati, specificando un parametro sort che accetta il nome di un campo come valore, ad esempio */orders?sort=ProductID*. Questo approccio, tuttavia, può avere un effetto negativo sulla memorizzazione nella cache, perché i parametri della stringa di query formano parte dell'identificatore di risorsa usato da molte implementazioni di cache come chiave per i dati memorizzati nella cache.

È possibile estendere questo approccio per limitare i campi restituiti per ogni elemento, se ognuno contiene una grande quantità di dati. Si può usare, ad esempio, un parametro di stringa di query che accetta un elenco di campi delimitato da virgole, come */orders?fields=ProductID,Quantity*. 

Assegnare valori predefiniti significativi a tutti i parametri facoltativi nelle stringhe di query. Ad esempio, impostare il parametro `limit` su 10 e il parametro `offset` su 0 se si implementa la paginazione, impostare il parametro Sort sulla chiave della risorsa se si implementa l’ordinamento e impostare il parametro `fields` su tutti i campi della risorsa se si supportano le proiezioni.

## <a name="support-partial-responses-for-large-binary-resources"></a>Supportare risposte parziali per risorse binarie di grandi dimensioni

Una risorsa può contenere campi binari di grandi dimensioni, come file o immagini. Per superare i problemi causati da connessioni intermittenti e inaffidabili e migliorare i tempi di risposta, valutare la possibilità di consentire il recupero di tali risorse in blocchi. A tale scopo, l'API Web deve supportare l'intestazione Accept-Ranges per le richieste GET relative a risorse di grandi dimensioni. Questa intestazione indica che l'operazione GET supporta richieste parziali. L'applicazione client può inviare richieste GET che restituiscono un subset di una risorsa, specificato come un intervallo di byte. 

Valutare anche la possibilità di implementare richieste HTTP HEAD per queste risorse. Una richiesta HEAD è simile a una richiesta GET tranne per il fatto che restituisce solo le intestazioni HTTP che descrivono la risorsa, con un corpo del messaggio vuoto. Un'applicazione client può inviare una richiesta HEAD per determinare se recuperare una risorsa tramite richieste GET parziali. Ad esempio: 

```HTTP
HEAD http://adventure-works.com/products/10?fields=productImage HTTP/1.1
```

Di seguito è riportato un messaggio di risposta di esempio: 

```HTTP
HTTP/1.1 200 OK

Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 4580
```

L'intestazione Content-Length contiene le dimensioni totali della risorsa e l'intestazione Accept-Ranges indica che l'operazione GET corrispondente supporta risultati parziali. L'applicazione client può usare queste informazioni per recuperare l'immagine in blocchi più piccoli. La prima richiesta recupera i primi 2500 byte mediante l'intestazione Range:

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=0-2499
```

Il codice di stato HTTP 206 restituito nel messaggio di risposta indica che si tratta di una risposta parziale. L'intestazione Content-Length specifica il numero effettivo di byte restituiti nel corpo del messaggio (non le dimensioni della risorsa) e l'intestazione Content-Range indica di quale parte della risorsa si tratta (byte 0-2499 di 4580):

```HTTP
HTTP/1.1 206 Partial Content

Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2500
Content-Range: bytes 0-2499/4580

[...]
```

Una richiesta successiva dall'applicazione client può recuperare la parte restante della risorsa.

## <a name="use-hateoas-to-enable-navigation-to-related-resources"></a>Usare HATEOAS per consentire lo spostamento alle risorse correlate

Una delle principali motivazioni alla base dell’approccio REST è che dovrebbe essere possibile spostarsi nell'intero set di risorse senza conoscere a priori lo schema URI. Ogni richiesta HTTP GET deve restituire le informazioni necessarie a trovare le risorse correlate direttamente all'oggetto richiesto tramite collegamenti ipertestuali inclusi nella risposta e deve anche contenere informazioni che descrivono le operazioni disponibili in ciascuna di queste risorse. Tale principio è noto come HATEOAS o Hypertext as the Engine of Application State. Il sistema è in realtà una macchina a stati finiti e la risposta a ogni richiesta contiene le informazioni necessarie per passare da uno stato all’altro. Non dovrebbero essere necessarie altre informazioni.

> [!NOTE]
> Attualmente non esistono standard o specifiche che definiscono come modellare il principio HATEOAS. Gli esempi mostrati in questa sezione illustrano una possibile soluzione.
>
>

Per gestire la relazione tra un ordine e un cliente, ad esempio, la rappresentazione di un ordine potrebbe includere collegamenti che identificano le operazioni disponibili per il cliente dell'ordine. Di seguito è riportata una possibile rappresentazione: 

```json
{
  "orderID":3,
  "productID":2,
  "quantity":4,
  "orderValue":16.60,
  "links":[
    {
      "rel":"customer",
      "href":"http://adventure-works.com/customers/3", 
      "action":"GET",
      "types":["text/xml","application/json"] 
    },
    {
      "rel":"customer",
      "href":"http://adventure-works.com/customers/3", 
      "action":"PUT",
      "types":["application/x-www-form-urlencoded"]
    },
    {
      "rel":"customer",
      "href":"http://adventure-works.com/customers/3",
      "action":"DELETE",
      "types":[]
    },
    {
      "rel":"self",
      "href":"http://adventure-works.com/orders/3", 
      "action":"GET",
      "types":["text/xml","application/json"]
    },
    {
      "rel":"self",
      "href":"http://adventure-works.com/orders/3", 
      "action":"PUT",
      "types":["application/x-www-form-urlencoded"]
    },
    {
      "rel":"self",
      "href":"http://adventure-works.com/orders/3", 
      "action":"DELETE",
      "types":[]
    }]
}
```

In questo esempio, la matrice `links` contiene un set di collegamenti, ognuno dei quali rappresenta un'operazione su un'entità correlata. I dati per ogni collegamento includono la relazione ("customer"), l'URI (`http://adventure-works.com/customers/3`), il metodo HTTP e i tipi MIME supportati. Queste sono tutte le informazioni necessarie a un'applicazione client per poter richiamare l'operazione. 

La matrice `links` include anche informazioni autoreferenziali sulla risorsa che è stata recuperata. Queste informazioni presentano la relazione *self*.

Il set di collegamenti restituito può variare a seconda dello stato della risorsa. È per questo motivo che l'ipertesto viene definito il "motore dello stato dell'applicazione" (Hypertext as the Engine of Application State).

## <a name="versioning-a-restful-web-api"></a>Controllo delle versioni di un'API Web RESTful

È altamente improbabile che un'API Web rimanga statica. Man mano che i requisiti aziendali cambiano, è possibile che vengano aggiunte nuove raccolte di risorse, che le relazioni tra le risorse cambino e che la struttura dei dati nelle risorse venga modificata. Sebbene l'aggiornamento di un'API Web per gestire requisiti nuovi o diversi sia un processo relativamente semplice, è necessario considerare gli effetti delle modifiche sulle applicazioni client che utilizzano l'API Web. Il problema è che nonostante lo sviluppatore che progetta e implementa un'API Web abbia il controllo completo su tale API, non ha lo stesso livello di controllo sulle applicazioni client che possono essere compilate da organizzazioni di terze parti che operano in modalità remota. L’imperativo è consentire alle applicazioni client esistenti di continuare a funzionare senza modifiche e allo stesso tempo permettere alle nuove applicazioni client di avvalersi di nuove funzionalità e risorse.

Il controllo delle versioni consente a un’API Web di indicare le funzionalità e le risorse esposte e a un’applicazione client di inviare richieste dirette a una versione specifica di una funzionalità o di una risorsa. Le sezioni seguenti descrivono diversi approcci differenti, ognuno dei quali presenta vantaggi e compromessi.

### <a name="no-versioning"></a>Nessun controllo delle versioni
Si tratta dell'approccio più semplice e può essere accettabile per alcune API interne. Grandi cambiamenti potrebbero essere rappresentati come nuove risorse o nuovi collegamenti.  L’aggiunta di contenuto alle risorse esistenti potrebbe non presentare una modifica sostanziale in quanto le applicazioni client che non prevedono di visualizzare che questo contenuto lo ignoreranno semplicemente.

Una richiesta all'URI *http://adventure-works.com/customers/3*, ad esempio, restituirà i dettagli di un singolo cliente contenente i campi `id`, `name` e `address` previsti dall'applicazione client:

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

> [!NOTE]
> Per semplicità, le risposte di esempio illustrate in questa sezione non includono collegamenti HATEOAS.
>
>

Se il campo `DateCreated` viene aggiunto allo schema della risorsa customer, la risposta sarà simile alla seguente:

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":"1 Microsoft Way Redmond WA 98053"}
```

Le applicazioni client esistenti potrebbero continuare a funzionare correttamente se riescono a ignorare i campi non riconosciuti, mentre le nuove applicazioni client possono essere progettate per gestire questo nuovo campo. Tuttavia, modifiche più radicali allo schema di risorse (ad esempio, la rimozione o la ridenominazione dei campi) o cambiamenti nelle relazioni tra le risorse possono costituire modifiche sostanziali che impediscono alle applicazioni client esistenti di funzionare correttamente. In tali situazioni è necessario considerare uno degli approcci seguenti.

### <a name="uri-versioning"></a>Controllo delle versioni tramite l’URI
Ogni volta che si modifica l'API Web o che si cambia lo schema di risorse, viene aggiunto un numero di versione all'URI per ciascuna risorsa. Gli URI già esistenti continueranno a funzionare come prima, restituendo risorse conformi al relativo schema originale.

Estendendo l'esempio precedente, se il campo `address` viene ristrutturato in campi secondari contenenti le singole parti che costituiscono l'indirizzo (ad esempio `streetAddress`, `city`, `state` e `zipCode`), questa versione della risorsa potrebbe essere esposta tramite un URI contenente un numero di versione, ad esempio http://adventure-works.com/v2/customers/3:

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

Questo meccanismo di controllo delle versioni è molto semplice, ma dipende dal server che esegue il routing della richiesta all'endpoint appropriato. Tuttavia, può diventare scomodo man mano che l’API Web matura attraverso diverse iterazioni e il server deve supportare un numero elevato di versioni diverse. Inoltre, dal punto di vista di un purista, in tutti i casi le applicazioni client recuperano gli stessi dati (cliente 3), quindi l'URI non deve essere effettivamente diverso a seconda della versione. Questo schema complica anche l'implementazione di HATEOAS perché tutti i collegamenti dovranno includere il numero di versione nei relativi URI.

### <a name="query-string-versioning"></a>Controllo delle versioni tramite la stringa di query
Anziché fornire più URI, è possibile specificare la versione della risorsa usando un parametro all'interno della stringa di query aggiunta alla richiesta HTTP, ad esempio *http://adventure-works.com/customers/3?version=2*. Il parametro della versione deve essere impostato su un valore predefinito significativo, ad esempio 1 se viene omesso dalle applicazioni client meno recenti.

Questo approccio ha il vantaggio semantico che la stessa risorsa viene sempre recuperata dallo stesso URI, ma dipende dal codice che gestisce la richiesta analizzare la stringa di query e inviare la risposta HTTP appropriata. Questo approccio presenta anche le stesse complicazioni per l'implementazione di HATEOAS del meccanismo di controllo delle versioni tramite URI.

> [!NOTE]
> Alcuni Web browser e proxy Web meno recenti non memorizzano nella cache le risposte per le richieste che includono una stringa di query nell'URI. Ciò può avere un impatto negativo sulle prestazioni per le applicazioni Web che utilizzano un'API Web e che vengono eseguite dall'interno di questo tipo di Web browser.
>
>

### <a name="header-versioning"></a>Controllo delle versioni tramite l’intestazione
Anziché aggiungere il numero di versione come parametro di stringa di query, è possibile implementare un'intestazione personalizzata che indica la versione della risorsa. Questo approccio richiede l’aggiunta, da parte dell'applicazione client, dell'intestazione appropriata a tutte le richieste, nonostante il codice che gestisce la richiesta del client possa usare un valore predefinito (versione 1) se l'intestazione della versione viene omessa. Nell'esempio seguente viene usata un'intestazione personalizzata denominata *Custom-Header*. Il valore di questa intestazione indica la versione dell'API Web.

Versione 1:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
Custom-Header: api-version=1
```

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

Versione 2:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
Custom-Header: api-version=2
```

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

Si noti che, come con i due approcci precedenti, l’implementazione di HATEOAS richiede l’inclusione dell'intestazione personalizzata appropriata in tutti i collegamenti.

### <a name="media-type-versioning"></a>Controllo delle versioni tramite il tipo di supporto
Quando un'applicazione client invia una richiesta HTTP GET a un server Web, è necessario stabilire il formato del contenuto che è possibile gestire tramite un'intestazione Accept, come descritto in precedenza in queste linee guida. L'intestazione *Accept* ha spesso lo scopo di consentire all'applicazione client di specificare se il corpo della risposta deve essere in formato XML, JSON o in un altro formato comune analizzabile dal client. Tuttavia, è possibile definire tipi di supporti personalizzati che includono informazioni che consentono all'applicazione client di indicare la versione di una risorsa prevista.163 Nell'esempio seguente viene illustrata una richiesta che specifica un'intestazione *Accept* con il valore *application/vnd.adventure-works.v1+json*. L'elemento *vnd.adventure works.v1* indica al server Web che deve restituire la versione 1 della risorsa, mentre l'elemento *json* specifica che il corpo della risposta deve essere in formato JSON:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
Accept: application/vnd.adventure-works.v1+json
```

Il codice che gestisce la richiesta è responsabile dell'elaborazione dell'intestazione *Accept* rispettandola per quanto possibile. L'applicazione client può specificare più formati nell'intestazione *Accept* e in questo caso il server Web può scegliere il formato più appropriato per il corpo della risposta. Il server Web conferma il formato dei dati nel corpo della risposta mediante l'intestazione Content-Type:

```HTTP
HTTP/1.1 200 OK
Content-Type: application/vnd.adventure-works.v1+json; charset=utf-8

{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

Se l'intestazione Accept non specifica alcun tipo di supporto noto, il server Web potrebbe generare un messaggio di risposta HTTP 406 (Pagina non valida) o restituire un messaggio con un tipo di supporto predefinito.

Questo approccio è senza dubbio il meccanismo di controllo delle versioni più puro e si presta naturalmente a HATEOAS, che può includere il tipo MIME dei dati correlati nei collegamenti alle risorse.

> [!NOTE]
> Quando si seleziona una strategia di controllo delle versioni, è consigliabile tenere in considerazione anche le implicazioni sulle prestazioni, soprattutto la memorizzazione nella cache sul server Web. Il controllo delle versioni tramite URI e gli schemi di controllo delle versioni tramite la stringa di query sono adatti alla cache perché la stessa combinazione URI/stringa di query fa riferimento ogni volta agli stessi dati.
>
> I meccanismi di controllo delle versioni tramite intestazione e tipo di supporto in genere richiedono logica aggiuntiva per esaminare i valori nell’intestazione personalizzata o nell'intestazione Accept. In un ambiente su larga scala, la presenza di molti client che usano versioni diverse di un'API Web può comportare una notevole quantità di dati duplicati in una cache lato server. Questo problema può diventare evidente se un'applicazione client comunica con un server Web tramite un proxy che implementa la memorizzazione nella cache e che inoltra una richiesta al server Web solo se attualmente non mantiene una copia dei dati richiesti nella propria cache.
>
>

## <a name="open-api-initiative"></a>Iniziativa OpenAPI
L'[Iniziativa OpenAPI](https://www.openapis.org/) è stata creata da un consorzio di settore per standardizzare le descrizioni dell'API REST tra i fornitori. Come parte di questa iniziativa, la specifica di Swagger 2.0 è stata rinominata Specifica OpenAPI ed è stata inserita nell'Iniziativa OpenAPI.

Si consiglia di adottare OpenAPI per le API Web. Alcune informazioni da considerare:

- La specifica OpenAPI include un set di dettagliate linee guida su come dovrebbe essere progettata un'API REST. Questo presenta dei vantaggi per quanto riguarda l'interoperabilità, ma richiede una maggiore attenzione quando si progetta l'API in conformità con la specifica.
- OpenAPI promuove un approccio con priorità al contratto, anziché un approccio con priorità all'implementazione. Priorità al contratto vuol dire che l'utente progetta prima di tutto il contratto dell'API, ovvero l'interfaccia, e poi scrive il codice che implementa il contratto. 
- Strumenti come Swagger possono generare librerie o documentazione client dai contratti API. Ad esempio, vedere [Pagine della guida dell'API Web ASP.NET con Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger).

## <a name="more-information"></a>Altre informazioni
* [Microsoft REST API Guidelines](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md) (Linee guida per API REST Microsoft): suggerimenti dettagliati per la progettazione di API REST pubbliche.
* [Guida di riferimento dettagliata su REST](http://restcookbook.com/): introduzione alla creazione di API RESTful.
* [Elenco di controllo per le API Web](https://mathieu.fenniak.net/the-api-checklist/): utile elenco degli elementi da considerare durante la progettazione e l'implementazione di un'API Web.
* [Iniziativa OpenAPI](https://www.openapis.org/): documentazione e dettagli di implementazione per OpenAPI.
