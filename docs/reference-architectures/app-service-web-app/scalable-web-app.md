---
title: Applicazione Web scalabile
description: Miglioramento della scalabilità in un'applicazione Web eseguita in Microsoft Azure.
author: MikeWasson
ms.date: 10/25/2018
ms.openlocfilehash: dd1fa30b6ac8d978ad0151c8753951d848d60664
ms.sourcegitcommit: 877777094b554559dc9cb1f0d9214d6d38197439
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527610"
---
# <a name="improve-scalability-in-an-azure-web-application"></a>Migliorare la scalabilità in un'applicazione Web di Azure

Questa architettura di riferimento è basata su procedure consolidate volte al miglioramento della scalabilità e delle prestazioni di un'applicazione Web Servizio app di Azure.

![[0]][0]

*Scaricare un [file Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architettura

Questa architettura si basa su quella illustrata in [Applicazione Web di base][basic-web-app]. Include i componenti seguenti:

* **Gruppo di risorse**. Un [gruppo di risorse][resource-group] è un contenitore logico per le risorse di Azure.
* **[App Web][app-service-web-app]**. Una tipica applicazione moderna potrebbe includere sia un sito Web che una o più API Web RESTful. Un'API Web potrebbe essere utilizzata da client browser tramite AJAX, da applicazioni client native o da applicazioni lato server. Per considerazioni sulla progettazione di un'API, vedere [Linee guida per la progettazione di un'API][api-guidance].
* **App per le funzioni**. Usare le [app per le funzioni][functions] per eseguire attività in background. Le funzioni vengono richiamate da un trigger, ad esempio un evento del timer o l'inserimento di un messaggio in una coda. Per attività con stato ed esecuzione prolungata, usare [Durable Functions][durable-functions].
* **Coda**. Nell'architettura illustrata qui l'applicazione accoda le attività in background inserendo un messaggio in una coda dell'[archivio code di Azure][queue-storage]. Il messaggio attiva un'app per le funzioni. In alternativa, è possibile usare le code del bus di servizio. Per un confronto, vedere [Analogie e differenze tra le code di Azure e le code del bus di servizio][queues-compared].
* **Cache**. Archiviare i dati semistatici nella [cache Redis di Azure][azure-redis].  
* <strong>Rete CDN</strong>. Usare la [rete per la distribuzione di contenuti di Azure][azure-cdn] (rete CDN) per memorizzare nella cache il contenuto disponibile pubblicamente, in modo da ridurre la latenza e accelerare la distribuzione del contenuto.
* **Archiviazione dei dati**. Usare un [database SQL di Azure][sql-db] per i dati relazionali. Per i dati non relazionali, prendere in considerazione [Cosmos DB][cosmosdb].
* **Ricerca di Azure**. Usare il servizio [Ricerca di Azure][azure-search] per aggiungere funzionalità di ricerca come i suggerimenti per la ricerca, la ricerca fuzzy e la ricerca specifica della lingua. Il servizio Ricerca di Azure viene in genere usato in combinazione con un altro archivio dati, soprattutto se l'archivio dati primario richiede la coerenza assoluta. In questo approccio, archiviare i dati autorevoli nell'altro archivio dati e l'indice di ricerca in Ricerca di Azure. Ricerca di Azure può essere usato anche per creare un unico indice di ricerca da più archivi dati.  
* **DNS di Azure**. [DNS di Azure][azure-dns] è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.
* **Gateway applicazione**. Il [gateway applicazione](/azure/application-gateway/) è un servizio di bilanciamento del carico di livello 7. In questa architettura, instrada le richieste HTTP al front-end Web. Il gateway applicazione fornisce anche un [Web application firewall](/azure/application-gateway/waf-overview) (WAF) che protegge l'applicazione da exploit e vulnerabilità comuni. 

## <a name="recommendations"></a>Consigli

I requisiti della propria organizzazione potrebbero essere diversi da quelli dell'architettura descritta in questo articolo. Seguire le indicazioni in questa sezione come punto di partenza.

### <a name="app-service-apps"></a>App del servizio app
È consigliabile creare l'applicazione Web e l'API Web come app del servizio app separate. In questo modo potranno essere eseguite in piani di servizio app separati ed essere scalate in maniera indipendente. Se inizialmente questo livello di scalabilità non è necessario, è possibile distribuire le app nello stesso piano e successivamente spostarle in piani distinti, se necessario.

> [!NOTE]
> I piani Basic, Standard e Premium non vengono fatturati in base all'app ma in base alle istanze di macchine virtuali nel piano. Vedere [Prezzi di Servizio app ][app-service-pricing].
> 
> 

### <a name="cache"></a>Cache
È possibile migliorare le prestazioni e la scalabilità usando la [cache Redis di Azure][azure-redis] per memorizzare nella cache alcuni dati. È consigliabile usare la cache Redis per:

* Dati di transazione semistatici.
* Stato della sessione.
* Output HTML. Può essere utile nelle applicazioni che eseguono il rendering di output HTML complesso.

Per informazioni più dettagliate sulla progettazione di una strategia di memorizzazione nella cache, vedere [Informazioni aggiuntive sulla memorizzazione nella cache][caching-guidance].

### <a name="cdn"></a>RETE CDN
Usare la [rete per la distribuzione di contenuti di Azure][azure-cdn] per memorizzare nella cache il contenuto statico. Il vantaggio principale di una rete CDN è quello di ridurre la latenza per gli utenti, in quanto il contenuto viene memorizzato nella cache di un server perimetrale geograficamente vicino all'utente. La rete CDN può anche ridurre il carico sull'applicazione, poiché il traffico non viene gestito dall'applicazione.

Se l'app è costituita prevalentemente da pagine statiche, valutare l'uso della [rete CDN per memorizzare nella cache l'intera app][cdn-app-service]. In alternativa, inserire il contenuto statico, ad esempio immagini, CSS e file HTML, nel servizio [Archiviazione di Azure e usare la rete CDN per memorizzare nella cache questi file][cdn-storage-account].

> [!NOTE]
> La rete CDN di Azure non supporta i contenuti che richiedono l'autenticazione.
> 
> 

Per informazioni più dettagliate, vedere [Indicazioni sulla rete per la distribuzione di contenuti (CDN)][cdn-guidance].

### <a name="storage"></a>Archiviazione
Le applicazioni moderne spesso elaborano grandi quantità di dati. Per assicurare la scalabilità per il cloud, è importante scegliere il tipo di archiviazione corretto. Di seguito sono elencati alcuni suggerimenti di base. 

| Elemento da archiviare | Esempio | Tipo di archiviazione consigliato |
| --- | --- | --- |
| File |Immagini, documenti, PDF |Archiviazione BLOB di Azure |
| Coppie chiave/valore |Dati del profilo utente cercati in base all'ID utente |Archiviazione tabelle di Azure |
| Brevi messaggi aventi lo scopo di attivare un'ulteriore elaborazione |Richieste di ordini |Coda di archiviazione, coda del bus di servizio o argomento del bus di servizio di Azure |
| Dati non relazionali con uno schema flessibile che richiedono l'esecuzione di query di base |Catalogo prodotti |Database di documenti, come Azure Cosmos DB, MongoDB o Apache CouchDB |
| Dati relazionali che richiedono il supporto di query più avanzate, uno schema rigido e/o coerenza assoluta |Inventario prodotti |Database SQL di Azure |

 Vedere [Choose the right data store][datastore] (Scegliere il giusto archivio dati).

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Uno dei vantaggi principali del servizio app di Azure è la possibilità di scalare l'applicazione in base al carico. Ecco alcune considerazioni da tenere presenti quando si pianifica la scalabilità dell'applicazione.

### <a name="app-service-app"></a>app del servizio app
Se la propria soluzione include diverse app del servizio app, è consigliabile distribuirle in piani di servizio app distinti. Questo approccio consente di scalare le app in modo indipendente, poiché vengono eseguite in istanze separate. 

Analogamente, valutare la possibilità di inserire un'app per le funzioni in un piano distinto affinché le attività in background non vengano eseguite nelle stesse istanze che gestiscono le richieste HTTP. Se le attività in background vengono eseguite in modo intermittente, prendere in considerazione un [piano a consumo][functions-consumption-plan], che prevede la fatturazione in base al numero di esecuzioni anziché su base oraria. 

### <a name="sql-database"></a>Database SQL
Aumentare la scalabilità di un database SQL eseguendo il *partizionamento orizzontale* del database. Il database viene così partizionato in senso orizzontale. Il partizionamento orizzontale consente di scalare orizzontalmente il database mediante [strumenti di database elastico][sql-elastic]. I possibili vantaggi del partizionamento orizzontale includono:

- Maggiore velocità effettiva delle transazioni.
- Esecuzione più rapida delle query su un subset dei dati.

### <a name="azure-search"></a>Ricerca di Azure
Il servizio Ricerca di Azure evita di dover eseguire ricerche di dati complesse dall'archivio dati primario e può essere scalato per la gestione del carico. Vedere [Ridimensionare i livelli di risorse per i carichi di lavoro di indicizzazione e query in Ricerca di Azure][azure-search-scaling].

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza
Questa sezione contiene alcune considerazioni sulla sicurezza specifiche dei servizi di Azure descritti in questo articolo. Non è un elenco completo delle procedure di sicurezza consigliate. Per alcune considerazioni aggiuntive sulla sicurezza, vedere [Garantire la sicurezza di un'app in Servizio app di Azure][app-service-security].

### <a name="cross-origin-resource-sharing-cors"></a>Condivisione risorse tra le origini (CORS)
Se si crea un sito Web e un'API Web come app separate, per consentire al sito Web di effettuare chiamate AJAX sul lato client è necessario abilitare CORS.

> [!NOTE]
> La sicurezza del browser impedisce a una pagina Web di creare richieste AJAX per un altro dominio. Questa restrizione è nota come criteri di corrispondenza dell'origine e impedisce a un sito dannoso di leggere dati sensibili da un altro sito. CORS è uno standard W3C che consente a un server di ridurre i criteri di corrispondenza dell'origine, oltre che di accettare alcune richieste multiorigine e rifiutarne altre.
> 
> 

Il supporto per CORS è incorporato in Servizi app, quindi non è necessario scrivere alcun codice applicazione. Vedere [Utilizzare un'app per le API da JavaScript tramite CORS][cors]. Aggiungere il sito Web all'elenco delle origini consentite per l'API.

### <a name="sql-database-encryption"></a>Crittografia del database SQL
Usare [Transparent Data Encryption][sql-encryption] se occorre crittografare i dati inattivi nel database. Questa funzionalità esegue in tempo reale la crittografia e la decrittografia di un intero database, inclusi i backup e i file di log delle transazioni, senza dover apportare modifiche all'applicazione. La crittografia aggiunge un certo livello di latenza, quindi è consigliabile separare i dati che devono essere protetti nel relativo database e abilitare la crittografia solo per quel database.  
  

<!-- links -->

[api-guidance]: ../../best-practices/api-design.md
[app-service-security]: /azure/app-service-web/web-sites-security
[app-service-web-app]: /azure/app-service-web/app-service-web-overview
[app-service-api-app]: /azure/app-service-api/app-service-api-apps-why-best-platform
[app-service-pricing]: https://azure.microsoft.com/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/services/cdn/
[azure-dns]: /azure/dns/dns-overview
[azure-redis]: https://azure.microsoft.com/services/cache/
[azure-search]: https://azure.microsoft.com/documentation/services/search/
[azure-search-scaling]: /azure/search/search-capacity-planning
[basic-web-app]: basic-web-app.md
[basic-web-app-scalability]: basic-web-app.md#scalability-considerations
[caching-guidance]: ../../best-practices/caching.md
[cdn-app-service]: /azure/app-service-web/cdn-websites-with-cdn
[cdn-storage-account]: /azure/cdn/cdn-create-a-storage-account-with-cdn
[cdn-guidance]: ../../best-practices/cdn.md
[cors]: /azure/app-service-api/app-service-api-cors-consume-javascript
[cosmosdb]: /azure/cosmos-db/
[datastore]: ../..//guide/technology-choices/data-store-overview.md
[durable-functions]: /azure/azure-functions/durable-functions-overview
[functions]: /azure/azure-functions/functions-overview
[functions-consumption-plan]: /azure/azure-functions/functions-scale#consumption-plan
[queue-storage]: /azure/storage/storage-dotnet-how-to-use-queues
[queues-compared]: /azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted
[resource-group]: /azure/azure-resource-manager/resource-group-overview#resource-groups
[sql-db]: https://azure.microsoft.com/documentation/services/sql-database/
[sql-elastic]: /azure/sql-database/sql-database-elastic-scale-introduction
[sql-encryption]: https://msdn.microsoft.com/library/dn948096.aspx
[tm]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/app-service-reference-architectures.vsdx
[web-app-multi-region]: ./multi-region.md
[0]: ./images/scalable-web-app.png "Applicazione Web in Azure con scalabilità migliorata"
