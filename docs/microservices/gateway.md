---
title: Gateway API
description: Gateway API nei microservizi
author: MikeWasson
ms.date: 12/08/2017
ms.openlocfilehash: 6483d416363e24f4084d6b856847a740bf4054d9
ms.sourcegitcommit: a8453c4bc7c870fa1a12bb3c02e3b310db87530c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2017
ms.locfileid: "27549179"
---
# <a name="designing-microservices-api-gateways"></a>Progettazione di microservizi: gateway API

In un'architettura di microservizi, un client potrebbe interagire con più servizi front-end. In questo caso, come determina gli endpoint da chiamare? Che cosa accade in caso di introduzione di nuovi servizi o di refactoring dei servizi esistenti? Come vengono gestiti dai servizi la terminazione SSL, l'autenticazione e altri aspetti? Un *gateway API* consente di risolvere queste problematiche. 

![](./images/gateway.png)

## <a name="what-is-an-api-gateway"></a>Informazioni sui gateway API

Un gateway API si trova tra i client e i servizi e funge da proxy inverso, indirizzando le richieste dai client ai servizi. Può anche eseguire varie attività trasversali come l'autenticazione, la terminazione SSL e la limitazione della frequenza. Se non si distribuisce un gateway, i client devono inviare le richieste direttamente ai servizi front-end. L'esposizione diretta dei servizi ai client, tuttavia, presenta alcuni potenziali problemi:

- Può determinare codice client complesso. Il client deve tenere traccia di più endpoint e gestire gli errori in modo resiliente. 
- Crea un accoppiamento tra client e back-end. Il client deve conoscere la scomposizione dei singoli servizi. Questo rende più difficile gestire il client nonché effettuare il refactoring dei servizi.
- Per una singola operazione possono essere necessarie chiamate a più servizi. Questo può determinare più round trip in rete tra il client e il server e causare così una latenza significativa. 
- Ogni servizio pubblico deve gestire aspetti come autenticazione, SSL e limitazione della frequenza dei client. 
- I servizi devono esporre un protocollo adatto ai client come HTTP o WebSocket. Questo limita la scelta dei [protocolli di comunicazione](./interservice-communication.md). 
- I servizi con endpoint pubblici sono una potenziale superficie di attacco e necessitano di protezione avanzata.

Un gateway consente di risolvere questi problemi separando i client dai servizi. I gateway possono eseguire molte diverse funzioni e non tutte potrebbero essere necessarie. Le funzioni possono essere raggruppate negli schemi progettuali seguenti:

[Routing del gateway](../patterns/gateway-routing.md). Usare il gateway come proxy inverso per indirizzare le richieste a uno o più servizi back-end, con routing di livello 7. Il gateway offre un singolo endpoint per i client e consente di separare i client dai servizi. 

[Aggregazione tramite il gateway](../patterns/gateway-aggregation.md). Usare il gateway per aggregare più richieste singole in un'unica richiesta. Questo schema si applica quando per una singola operazione sono necessarie chiamate a più servizi back-end. Il client invia una richiesta al gateway, che recapita le richieste ai vari servizi back-end e quindi aggrega i risultati e li invia al client. Questo consente di ridurre la quantità di comunicazioni tra client e back-end. 

[Offload al gateway](../patterns/gateway-offloading.md). Usare il gateway per eseguire l'offload delle funzionalità dai singoli servizi al gateway, soprattutto per aspetti trasversali. Può essere utile per consolidare tali funzioni in un'unica posizione anziché affidarne l'implementazione a ogni servizio, in particolare per le funzionalità la cui implementazione richiede competenze specializzate, come l'autenticazione e l'autorizzazione. 

Di seguito sono riportati alcuni esempi di funzionalità di cui è possibile eseguire l'offload a un gateway:

- Terminazione SSL
- Autenticazione
- Inserimento nell'elenco di IP consentiti
- Limitazione della frequenza dei client (limitazione delle richieste)
- Registrazione e monitoraggio
- Memorizzazione delle risposte nella cache
- Web application firewall
- Compressione GZIP
- Gestione di contenuto statico

## <a name="choosing-a-gateway-technology"></a>Scelta di una tecnologia gateway

Di seguito sono riportate alcune opzioni per l'implementazione di un gateway API nell'applicazione.

- **Server proxy inverso**. Nginx e HAProxy sono server proxy inversi che supportano funzionalità come bilanciamento del carico, SSL e routing di livello 7. Entrambi sono prodotti open source gratuiti, con edizioni a pagamento che offrono funzionalità aggiuntive e opzioni di supporto. Sia Nginx che HAProxy sono prodotti avanzati con ampi set di funzionalità e prestazioni elevate. Possono essere estesi con moduli di terze parti o scrivendo script personalizzati in Lua. Nginx supporta anche un modulo di scripting basato su JavaScript denominato NginScript.

- **Controller di ingresso per rete mesh di servizi**. Se si usa una rete mesh di servizi come Linkerd o Istio, considerare le funzionalità offerte dal controller di ingresso per tale rete mesh. Il controller di ingresso Istio, ad esempio, supporta il routing di livello 7, i reindirizzamenti HTTP, la ripetizione dei tentativi e altre funzionalità. 

- [Gateway applicazione di Azure](/azure/application-gateway/). Il gateway applicazione è un servizio di bilanciamento del carico gestito che può eseguire il routing di livello 7 e la terminazione SSL. Offre anche un Web application firewall (WAF).

- [Gestione API di Azure](/azure/api-management/). Gestione API è una soluzione chiavi in mano per la pubblicazione di API per clienti interni ed esterni. Offre funzionalità utili per la gestione di un'API pubblica, tra cui la limitazione della frequenza, l'inserimento nell'elenco di IP consentiti e l'autenticazione con Azure Active Directory o altri provider di identità. Gestione API non esegue alcun bilanciamento del carico e dovrà quindi essere usato insieme a un servizio di bilanciamento del carico come il gateway applicazione o un proxy inverso.

Nella scelta di una tecnologia gateway, considerare quanto segue:

**Funzionalità**. Tutte le opzioni elencate sopra supportano il routing di livello 7, ma il supporto per altre funzionalità può variare. È possibile distribuire più gateway in base alle funzionalità necessarie. 

**Distribuzione**. Il gateway applicazione e Gestione API di Azure sono servizi gestiti. Nginx e HAProxy vengono in genere eseguiti in contenitori all'interno del cluster, ma possono essere anche distribuiti in VM dedicate all'esterno. Questo consente di isolare il gateway dal resto del carico di lavoro, ma determina un maggiore sovraccarico di gestione.

**Gestione**. Quando vengono aggiornati i servizi o ne vengono aggiunti di nuovi, potrebbe essere necessario aggiornare le regole di routing del gateway. Considerare come verrà gestito questo processo. Considerazioni simili si applicano alla gestione dei certificati SSL, degli elenchi di IP consentiti e di altri aspetti della configurazione.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

### <a name="deploying-nginx-or-haproxy-to-kubernetes"></a>Distribuzione di Nginx o HAProxy in Kubernetes

È possibile distribuire Nginx o HAProxy in Kubernetes come [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) o [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) che specifica l'immagine del contenitore di Nginx o HAProxy. Usare un oggetto ConfigMap per archiviare il file di configurazione per il proxy e montare ConfigMap come volume. Creare un servizio di tipo LoadBalancer per esporre il gateway tramite un'istanza di Azure Load Balancer. 

<!-- - Configure a readiness probe that serves a static file from the gateway (rather than routing to another service). -->

In alternativa, creare un controller di ingresso. Un controller di ingresso è una risorsa Kubernetes che distribuisce un servizio di bilanciamento del carico o un server proxy inverso. Sono disponibili diverse implementazioni, che includono Nginx e HAProxy. Una risorsa separata denominata ingresso definisce le impostazioni per il controller di ingresso, come le regole di routing e i certificati TLS. In questo modo, non è necessario gestire file di configurazione complessi specifici di una determinata tecnologia server proxy. Al momento della stesura di questo articolo, i controller di ingresso sono ancora una funzionalità beta di Kubernetes che continuerà a evolversi.

Dato che il gateway è un potenziale collo di bottiglia o singolo punto di guasto nel sistema, distribuire sempre almeno due repliche per garantire disponibilità elevata. A seconda del carico, potrebbe essere necessario aumentare il numero di istanze delle repliche. 

Valutare anche la possibilità di eseguire il gateway in un set di nodi dedicato nel cluster. Questo approccio offre i vantaggi seguenti:

- Isolamento. Tutto il traffico in ingresso è indirizzato a un set fisso di nodi, che può essere isolato dai servizi back-end.

- Configurazione stabile. Se il gateway non è configurato correttamente, l'intera applicazione non sarà disponibile. 

- Prestazioni. Per motivi di prestazioni può essere opportuno usare una specifica configurazione di VM per il gateway.

<!-- - Load balancing. You can configure the external load balancer so that requests always go to a gateway node. That can save a network hop, which would otherwise happen whenever a request lands on a node that isn't running a gateway pod. This consideration applies mainly to large clusters, where the gateway runs on a relatively small fraction of the total nodes. In Azure Container Service (ACS), this approach currently requires [ACS Engine](https://github.com/Azure/acs-engine)) which allows you to create multiple agent pools. Then you can deploy the gateway as a DaemonSet to the front-end pool. -->

### <a name="azure-application-gateway"></a>Gateway applicazione di Azure

Per connettere il gateway applicazione a un cluster Kubernetes in Azure:

1. Creare una subnet vuota nella rete virtuale del cluster.
2. Distribuire il gateway applicazione.
3. Creare un servizio Kubernetes di tipo [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport). In questo modo, il servizio viene esposto in ogni nodo ed è quindi raggiungibile dall'esterno del cluster. Non viene creato un servizio di bilanciamento del carico.
5. Ottenere il numero di porta assegnato per il servizio.
6. Aggiungere una regola del gateway applicazione con le caratteristiche seguenti:
    - Il pool back-end contiene le VM dell'agente.
    - L'impostazione HTTP specifica il numero di porta del servizio.
    - Il listener del gateway è in ascolto sulle porte 80/443.
    
Impostare il numero di istanze su almeno 2 per garantire disponibilità elevata.

### <a name="azure-api-management"></a>Gestione API di Azure 

Per connettere Gestione API a un cluster Kubernetes in Azure:

1. Creare una subnet vuota nella rete virtuale del cluster.
2. Distribuire Gestione API in tale subnet.
3. Creare un servizio Kubernetes di tipo LoadBalancer. Usare l'annotazione di [servizio di bilanciamento del carico interno](https://kubernetes.io/docs/concepts/services-networking/service/#internal-load-balancer) per creare un servizio di bilanciamento del carico interno anziché Internet come da impostazione predefinita.
4. Trovare l'indirizzo IP privato del servizio di bilanciamento del carico interno usando kubectl o l'interfaccia della riga di comando di Azure.
5. Usare Gestione API per creare un'API che indirizzi all'IP privato del servizio di bilanciamento del carico.

Valutare la possibilità di combinare Gestione API con un proxy inverso, come Nginx, HAProxy o il gateway applicazione di Azure. Per informazioni sull'uso di Gestione API con il gateway applicazione, vedere [Integrare Gestione API in una rete virtuale interna con un gateway applicazione](/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway).

> [!div class="nextstepaction"]
> [Registrazione e monitoraggio](./logging-monitoring.md)
