---
title: Indicazioni sulla rete per la distribuzione di contenuti
description: Indicazioni sulla rete per la distribuzione di contenuti (CDN) per fornire contenuto a larghezza di banda elevata ospitato in Azure.
author: dragon119
ms.date: 02/02/2018
pnp.series.title: Best Practices
ms.openlocfilehash: 73da41edec246b672564dd4a52b317eacf8ad649
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="best-practices-for-using-content-delivery-networks-cdns"></a>Procedure consigliate per l'uso di reti per la distribuzione di contenuti (CDN)

Una rete per la distribuzione di contenuti (CDN) è una rete di server distribuita in grado di fornire contenuti Web agli utenti in modo efficiente. Per ridurre la latenza, le reti CDN archiviano il contenuto memorizzato nella cache in server perimetrali prossimi agli utenti finali. 

In genere, le reti CDN vengono usate per distribuire contenuti statici, ad esempio immagini, fogli di stile, documenti, script lato client e pagine HTML. I principali vantaggi offerti dall'uso di una rete CDN sono una latenza più bassa e la distribuzione più rapida di contenuto agli utenti, indipendentemente dalla loro posizione geografica in relazione al data center in cui è ospitata l'applicazione. Le reti CDN possono anche contribuire a ridurre il carico su un'applicazione Web poiché l'applicazione non ha le richieste di servizio per il contenuto ospitato nella rete CDN.
 
![Diagramma della rete CDN](./images/cdn/CDN.png)

[Rete di distribuzione dei contenuti di Azure](/azure/cdn/cdn-overview) è una soluzione CDN globale per la distribuzione di contenuti a larghezza di banda elevata ospitati in Azure o in altre posizioni. Con Rete CDN di Azure è possibile memorizzare nella cache gli oggetti disponibili pubblicamente, caricati dall'archivio BLOB di Azure, da un'applicazione Web, da una macchina virtuale e da qualsiasi server Web pubblico. 

Questo argomento descrive alcune procedure consigliate e offre considerazioni di carattere generale sull'uso di una rete CDN. Per altre informazioni sull'uso di Rete CDN di Azure, vedere la [documentazione relativa alla rete di distribuzione dei contenuti (CDN)](/azure/cdn/).

## <a name="how-and-why-a-cdn-is-used"></a>Come e perché si usa la rete CDN

Ecco alcuni usi tipici della rete CDN:  

* Distribuzione di risorse statiche per applicazioni client, spesso da un sito Web. Può trattarsi di immagini, fogli di stile, documenti, file, script sul lato client, pagine HTML, frammenti HTML o qualsiasi altro contenuto che il server non debba modificare per ogni richiesta. L'applicazione può creare gli elementi al runtime e renderli disponibili per la rete CDN (ad esempio, creando un elenco di titoli di notizie correnti), ma esegue queste operazioni per ogni richiesta.
* Distribuzione di contenuto statico e condiviso pubblico a dispositivi quali telefoni cellulari e tablet PC. L'applicazione stessa è un servizio Web che offre un'API ai client in esecuzione sui vari dispositivi. La rete CDN può anche recapitare set di dati statici, tramite il servizio Web, che i client useranno, ad esempio, per generare la propria interfaccia utente. Ad esempio, la rete CDN può essere usata per distribuire documenti JSON o XML.
* Distribuzione di interi siti Web costituiti da solo contenuto pubblico statico ai client, senza richiedere risorse di calcolo dedicate.
* File video in streaming al client on demand. I video traggono vantaggio dalla latenza bassa e dalla connettività affidabile disponibili nei centri dati di tutto il mondo che offrono connessioni di rete CDN. Servizi multimediali di Microsoft Azure si integra con la rete di distribuzione dei contenuti di Azure per inviare i contenuti direttamente alla rete di distribuzione dei contenuti al fine di eseguire una nuova distribuzione. Per altre informazioni, vedere [Streaming endpoints overview](/azure/media-services/media-services-streaming-endpoints-overview) (Panoramica degli endpoint di streaming).
* Miglioramento generale dell'esperienza per gli utenti, specialmente quelli che si trovano lontano dal data center che ospita l'applicazione, che altrimenti risentirebbero di una latenza più elevata. Gran parte della dimensione totale del contenuto in un'applicazione Web è spesso statica e l’utilizzo della rete CDN consente di mantenere le prestazioni e l’esperienza complessiva dell’utente eliminando la necessità di distribuire l'applicazione in più centri dati. Per un elenco delle posizioni dei nodi di Rete CDN di Azure, vedere [Località POP della rete CDN di Azure](/azure/cdn/cdn-pop-locations/).
* Supporto di soluzioni IoT (Internet delle cose). L'enorme numero di dispositivi e appliance in una soluzione IoT può facilmente sovraccaricare un'applicazione se deve distribuire aggiornamenti firmware direttamente in ogni dispositivo.
* Gestione dei picchi e della crescita della domanda senza richiedere il ridimensionamento dell'applicazione, evitando il conseguente aumento dei costi di esecuzione. Ad esempio, quando viene rilasciato un aggiornamento del sistema operativo per un dispositivo hardware, come un modello specifico di router, o per un dispositivo di consumo come una smart TV, si verifica un grande picco nella domanda a causa del download eseguito da milioni di utenti e dispositivi in un breve periodo.

## <a name="challenges"></a>Problematiche

Esistono varie problematiche da considerare quando si pianifica l'uso di una rete CDN.  

* **Distribuzione**. Decidere l'origine da cui la rete CDN deve recuperare il contenuto e stabilire se è necessario distribuire tale contenuto in più sistemi di archiviazione. Prendere in considerazione il processo per la distribuzione del contenuto statico e delle risorse. Ad esempio, potrebbe essere necessario implementare un passaggio separato per caricare il contenuto nell'archivio BLOB di Azure.
* **Controllo delle versioni e controllo della cache**. Stabilire come aggiornare il contenuto statico e distribuire le nuove versioni. Comprendere il modo in cui la rete CDN esegue la memorizzazione nella cache e determina la durata (TTL). Per informazioni su Rete CDN di Azure, vedere [Funzionamento della memorizzazione nella cache](/azure/cdn/cdn-how-caching-works).
* **Test**. Può essere difficile eseguire il test locale delle impostazioni della rete CDN durante lo sviluppo e la verifica di un'applicazione in locale o in un ambiente di staging.
* **Ottimizzazione motore di ricerca**. Il contenuto, come immagini e documenti, viene fornito da un dominio diverso quando si usa la rete CDN e questo approccio può avere un impatto su SEO per tale contenuto.
* **Sicurezza del contenuto**. Non tutte le reti CDN offrono forme di controllo degli accessi per il contento. Alcuni servizi CDN, tra cui Rete CDN di Azure, supportano l'autenticazione basata su token per proteggere il contenuto della rete CDN. Per altre informazioni, vedere [Protezione di asset della rete per la distribuzione di contenuti (CDN) di Azure con l'autenticazione basata su token](/azure/cdn/cdn-token-auth).
* **Sicurezza del client**. I client possono connettersi da un ambiente che non consente l'accesso alle risorse nella rete CDN. Potrebbe trattarsi di un ambiente con limitazioni di sicurezza che limita l'accesso solo a una serie di origini note oppure di un ambiente che impedisce il caricamento delle risorse da una posizione diversa dall'origine della pagina. Per gestire questi casi è necessaria un'implementazione del fallback.
* **Resilienza**. La rete CDN è un singolo punto di guasto potenziale per un'applicazione. 

Scenari in cui la rete CDN può essere meno utile sono:  

* Se il contenuto ha una bassa percentuale di riscontri, è possibile che l'accesso avvenga solo poche volte durante la sua validità, in base all'impostazione della durata (TTL). 
* Se i dati sono privati, ad esempio per aziende di grandi dimensioni o ecosistemi di supply chain.

## <a name="general-guidelines-and-good-practices"></a>Linee guida generali e procedure consigliate

L'uso di una rete CDN è un ottimo modo per ridurre al minimo il carico sull'applicazione e ottimizzare disponibilità e prestazioni. Valutare l'adozione di questa strategia per tutto il contenuto appropriato e le risorse usate dall'applicazione. Quando si progetta la strategia per l'uso di una rete CDN, tenere presenti i punti nelle sezioni seguenti.

### <a name="deployment"></a>Distribuzione
È possibile che debba essere effettuato il provisioning e sia necessario distribuire il contenuto indipendentemente dall'applicazione se non viene incluso nel processo o nel pacchetto di distribuzione dell'applicazione. Si consideri come tale eventualità influirà sull’approccio del controllo delle versioni utilizzato per gestire i componenti dell'applicazione e il contenuto stativo della risorsa.

Valutare se può essere opportuno usare tecniche di creazione di bundle e minimizzazione per ridurre i tempi di caricamento dei client. La creazione di bundle consente di combinare più file in un unico file. La minimizzazione consente invece di rimuovere i caratteri non necessari da script e file CSS senza modificare la funzionalità.

Se è necessario distribuire il contenuto in un'altra posizione, è necessario un passaggio aggiuntivo nel processo di distribuzione. Se l'applicazione aggiorna il contenuto per la rete CDN, ad esempio a intervalli regolari o in risposta a un evento, è necessario archiviare il contenuto aggiornato in tutti i percorsi aggiuntivi, nonché l'endpoint per la rete CDN.

Stabilire come devono essere gestite le operazioni di sviluppo e test locali quando si prevede che contenuti statici siano serviti da una rete CDN. Ad esempio, è possibile distribuire preliminarmente il contenuto nella rete CDN durante l'esecuzione dello script di compilazione. In alternativa, è possibile usare direttive di compilazione o flag per controllare la modalità di caricamento delle risorse da parte dell'applicazione. Ad esempio, in modalità debug l'applicazione può caricare le risorse statiche da una cartella locale e in modalità versione può usare la rete CDN.

Valutare se è opportuno usare opzioni per la compressione dei file, ad esempio gzip (GNU zip). La compressione può essere eseguita nel server di origine dall'applicazione Web ospitante oppure direttamente nei server perimetrali dalla rete CDN. Per altre informazioni, vedere [Migliorare le prestazioni con la compressione dei file nella rete CDN di Azure](/azure/cdn/cdn-improve-performance).


### <a name="routing-and-versioning"></a>Routing e controllo delle versioni
Potrebbe essere necessario usare istanze diverse della rete CDN in momenti diversi. Ad esempio, quando si distribuisce una nuova versione dell'applicazione, è consigliabile usare una nuova rete CDN e quella precedente, mantenendo il contenuto in un formato meno recente, per le versioni precedenti. Se si usa l'archivio BLOB di Azure come origine di contenuto, è possibile creare un account di archiviazione separato o un contenitore separato e puntare l'endpoint della rete CDN ad esso. 

Non usare la stringa di query per indicare le diverse versioni dell'applicazione nei collegamenti alle risorse sulla rete CDN, perché quando si recupera il contenuto dall'archivio BLOB di Azure la stringa di query fa parte del nome della risorsa, ovvero il nome del BLOB. Questo approccio può anche influire sul modo in cui il client memorizza le risorse nella cache.

La distribuzione di nuove versioni di contenuto statico quando si aggiorna un'applicazione può essere problematica se le risorse precedenti vengono memorizzate nella cache nella rete CDN. Per altre informazioni, vedere più avanti la sezione sul controllo cache.

È consigliabile limitare l'accesso al contenuto della rete CDN in base al paese. La rete CDN di Azure consente di filtrare le richieste in base al paese di origine e limitare il contenuto recapitato. Per altre informazioni, vedere [Limitare l'accesso al contenuto in base al paese](/azure/cdn/cdn-restrict-access-by-country/).

### <a name="cache-control"></a>Controllo cache
Considerare come gestire la memorizzazione nella cache all'interno del sistema. Ad esempio, in Rete CDN di Azure è possibile impostare le regole di memorizzazione nella cache globale e successivamente la memorizzazione nella cache personalizzata per endpoint di origine specifici. È anche possibile controllare come viene eseguita la memorizzazione nella cache in una rete CDN inviando le intestazioni delle direttive della cache all'origine. 

Per altre informazioni, vedere [Funzionamento della memorizzazione nella cache](/azure/cdn/cdn-how-caching-works).

Per evitare la presenza di oggetti nella rete CDN, è possibile eliminarli dall'origine, rimuovere o eliminare l'endpoint della rete CDN o, nel caso dell'archiviazione BLOB, rendere privato il contenitore o il BLOB. Gli elementi, tuttavia, non vengono rimossi prima del termine della durata (TTL). È anche possibile ripulire un endpoint della rete CDN manualmente.

### <a name="security"></a>Sicurezza

La rete CDN può distribuire il contenuto tramite HTTPS (SSL) usando il certificato messo a disposizione dalla rete CDN, ma anche tramite HTTP. Per evitare avvisi del browser sul contenuto misto potrebbe essere necessario usare HTTPS per richiedere il contenuto statico visualizzato nelle pagine caricate tramite HTTPS.

Se si distribuiscono gli asset statici, ad esempio file di caratteri tramite la rete CDN, potrebbero verificarsi problemi relativi ai criteri di origine uguale se si usa una chiamata *XMLHttpRequest* per richiedere le risorse da un dominio diverso. Molti browser impediscono la condivisione di risorse tra origini (CORS), a meno che il server Web non è configurato per impostare le intestazioni di risposta appropriate. È possibile configurare la rete CDN per il supporto CORS tramite uno dei metodi seguenti:

* Configurare la rete CDN in modo che vengano aggiunte intestazioni CORS alle risposte. Per altre informazioni, vedere [Uso della rete CDN di Azure con CORS](/azure/cdn/cdn-cors). 
* Se l'origine è l'archiviazione BLOB di Azure, aggiungere le regole CORS all'endpoint di archiviazione. Per altre informazioni, vedere [Supporto della condivisione delle risorse tra le origini (CORS) per i servizi di archiviazione Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).
* Configurare l'applicazione in modo da impostare le intestazioni CORS. Ad esempio, vedere [Abilitare le richieste tra le origini (CORS)](/aspnet/core/security/cors) nella documentazione di ASP.NET Core.

### <a name="cdn-fallback"></a>Fallback della rete CDN
Considerare come l'applicazione gestirà un errore o l'indisponibilità temporanea della rete CDN. Le applicazioni client potrebbero riuscire a usare copie delle risorse memorizzate nella cache locale del client durante le richieste precedenti oppure è possibile includere codice che rilevi gli errori e richieda invece risorse dall'origine, ovvero la cartella dell'applicazione o il contenitore BLOB di Azure che contiene le risorse, se la rete CDN non è disponibile.

L'esempio seguente illustra un meccanismo di fallback
