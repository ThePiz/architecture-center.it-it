---
title: Eseguire la migrazione di un'applicazione di Servizi cloud di Azure in Azure Service Fabric
description: Come eseguire la migrazione di un'applicazione da Servizi cloud di Azure ad Azure Service Fabric.
author: MikeWasson
ms.date: 04/11/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: a1fc28737b194fe69e2ae094bd996d97363eb29c
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641111"
---
# <a name="migrate-an-azure-cloud-services-application-to-azure-service-fabric"></a>Eseguire la migrazione di un'applicazione di Servizi cloud di Azure in Azure Service Fabric 

[![GitHub](../_images/github.png) Codice di esempio][sample-code]

Questo articolo descrive come eseguire la migrazione di un'applicazione da Servizi cloud di Azure ad Azure Service Fabric. Vengono analizzati gli aspetti correlati all'architettura e le procedure consigliate. 

Per questo progetto, si inizia con un'applicazione di Servizi cloud denominata Surveys, che viene trasferita in Service Fabric. L'obiettivo è quello di eseguire la migrazione dell'applicazione con il minor numero possibile di modifiche. In un articolo successivo, l'applicazione viene ottimizzata per Service Fabric adottando un'architettura di microservizi.

Prima di leggere questo articolo, è utile comprendere le nozioni di base relative a Service Fabric e alle architetture di microservizi in generale. Vedere gli articoli seguenti:

- [Panoramica di Azure Service Fabric][sf-overview]
- [Perché usare un approccio ai microservizi per la compilazione di applicazioni][sf-why-microservices]

## <a name="about-the-surveys-application"></a>Informazioni sull'applicazione Surveys

Nel 2012, il gruppo che si occupa di modelli e procedure ha creato un'applicazione denominata Surveys, per un libro intitolato [Developing Multi-tenant Applications for the Cloud][tailspin-book]. Il libro descrive una società fittizia denominata Tailspin che progetta e implementa l'applicazione Surveys.

Surveys è un'applicazione multi-tenant che consente ai clienti di creare sondaggi. Dopo che un cliente si registra per usare l'applicazione, i membri della sua organizzazione possono creare e pubblicare sondaggi e raccogliere i risultati per l'analisi. L'applicazione include un sito Web pubblico in cui gli utenti possono partecipare a un sondaggio. Altre informazioni sullo scenario Tailspin originale sono disponibili [qui][tailspin-scenario].

Tailspin vuole ora spostare l'applicazione Surveys in un'architettura di microservizi, usando Service Fabric in esecuzione in Azure. Poiché l'applicazione è già stata distribuita come applicazione di Servizi cloud, Tailspin adotta un approccio in più fasi:

1. Trasferimento dei servizi cloud in Service Fabric, riducendo al minimo le modifiche all'applicazione.
2. Ottimizzazione dell'applicazione per Service Fabric, passando a un'architettura di microservizi.

Questo articolo descrive la prima fase. La seconda fase viene descritta in un articolo successivo. In un progetto reale, è probabile che le due fasi si sovrappongano. Durante il trasferimento a Service Fabric, è possibile iniziare anche a riprogettare l'applicazione in microservizi. Successivamente, è possibile ottimizzare ulteriormente l'architettura, ad esempio dividendo i servizi con granularità grossolana in servizi più piccoli.  

Il codice dell'applicazione è disponibile in [GitHub][sample-code]. Questo repository contiene sia l'applicazione di Servizi cloud che la versione di Service Fabric. 

> Il servizio cloud è una versione aggiornata dell'applicazione originale tratta dal libro *Developing Multi-tenant Applications*.

## <a name="why-microservices"></a>Perché usare i microservizi?

Un'analisi approfondita dei microservizi esula dall'ambito di questo articolo, ma di seguito sono illustrati alcuni dei vantaggi che Tailspin spera di ottenere passando a un'architettura di microservizi:

- **Aggiornamenti dell'applicazione**. I servizi possono essere distribuiti in modo indipendente, quindi è possibile adottare un approccio incrementale per l'aggiornamento di un'applicazione.
- **Resilienza e isolamento in caso di errore**. In caso di errore di un servizio, gli altri servizi rimangono in esecuzione.
- **Scalabilità**. I servizi possono essere ridimensionati in modo indipendente.
- **Flessibilità**. I servizi sono progettati per scenari aziendali, non per stack di tecnologie, quindi è più semplice eseguirne la migrazione a nuovi archivi dati, framework o tecnologie.
- **Sviluppo Agile**. I singoli servizi contengono meno codice rispetto a un'applicazione monolitica, quindi la codebase è più facile da comprendere, valutare e testare.
- **Team dedicati di piccole dimensioni**. Poiché l'applicazione viene suddivisa in molti servizi di piccole dimensioni, ogni servizio può essere compilato da un piccolo team dedicato.

## <a name="why-service-fabric"></a>Perché usare Service Fabric?
      
Service Fabric è una scelta ideale per un'architettura di microservizi, perché la maggior parte delle funzionalità necessarie in un sistema distribuito è integrata in Service Fabric, tra cui:

- **Gestione dei cluster**. Service Fabric gestisce automaticamente il failover dei nodi, il monitoraggio dell'integrità e altre funzioni di gestione dei cluster.
- **Scalabilità orizzontale**. Quando si aggiungono nodi a un cluster di Service Fabric, l'applicazione viene ridimensionata automaticamente, man mano che i servizi vengono distribuiti nei nuovi nodi.
- **Individuazione dei servizi**. Service Fabric fornisce un servizio di individuazione in grado di risolvere l'endpoint per un servizio denominato.
- **Servizi con e senza stato**. I servizi con stato usano [raccolte Reliable Collections][sf-reliable-collections], che possono prendere il posto di una cache o una coda e possono essere partizionate.
- **Processo Application Lifecycle Management**. I servizi possono essere aggiornati in modo indipendente e senza tempo di inattività dell'applicazione.
- **Orchestrazione dei servizi** in un cluster di computer.
- **Maggiore densità** per l'ottimizzazione del consumo di risorse. Un singolo nodo può ospitare più servizi.

Service Fabric è una soluzione usata da diversi servizi Microsoft, tra cui database SQL di Azure, Cosmos DB, Hub eventi di Azure e altri servizi, ed è quindi una piattaforma collaudata per la creazione di applicazioni cloud distribuite. 

## <a name="comparing-cloud-services-with-service-fabric"></a>Confronto tra Servizi cloud e Service Fabric

La tabella seguente riepiloga alcune importanti differenze tra le applicazioni di Servizi cloud e di Service Fabric. Per una discussione più approfondita, vedere [Informazioni sulle differenze tra Servizi cloud e Service Fabric che è opportuno conoscere prima di procedere alla migrazione di applicazioni][sf-compare-cloud-services].

|        | Servizi cloud | Service Fabric |
|--------|---------------|----------------|
| Composizione dell'applicazione | Ruoli| Servizi |
| Densità |Un'istanza del ruolo per macchina virtuale | Più servizi in un singolo nodo |
| Numero minimo di nodi | 2 per ruolo | 5 per cluster, per le distribuzioni di produzione |
| Gestione dello stato | Senza stato | Senza stato o con stato* |
| Hosting | Azure | Cloud o in locale |
| Hosting Web | IIS** | Self-hosting |
| Modello di distribuzione | [Modello di distribuzione classica][azure-deployment-models] | [Resource Manager][azure-deployment-models]  |
| Packaging | File di pacchetto del servizio cloud (con estensione cspkg) | Pacchetti di applicazioni e servizi |
| Aggiornamento dell'applicazione | Scambio di indirizzi VIP o aggiornamento in sequenza | Aggiornamento in sequenza |
| Scalabilità automatica | [Servizio integrato][cloud-service-autoscale] | Set di scalabilità di macchine virtuali per la scalabilità orizzontale |
| Debug | Emulatore locale | Cluster locale |

\*I servizi con stato usano [raccolte Reliable Collections][sf-reliable-collections] per archiviare lo stato tra repliche, in modo che tutte le letture siano locali per i nodi del cluster. Le operazioni di scrittura vengono replicate tra i nodi per una maggiore affidabilità. I servizi senza stato possono avere uno stato esterno, usando un database o un altro dispositivo di archiviazione esterna.

** I ruoli di lavoro supportano anche il self-hosting dell'API Web ASP.NET con OWIN.

## <a name="the-surveys-application-on-cloud-services"></a>Applicazione Surveys in Servizi cloud

Il diagramma seguente mostra l'architettura dell'applicazione Surveys in esecuzione in Servizi cloud. 

![](./images/tailspin01.png)

L'applicazione è costituita da due ruoli Web e un ruolo di lavoro.

- Il ruolo Web **Tailspin.Web** ospita un sito Web ASP.NET che i clienti di Tailspin usano per creare e gestire i sondaggi. I clienti possono anche usare il sito Web per registrarsi per usare l'applicazione e per gestire le sottoscrizioni. Infine, gli amministratori di Tailspin possono usarlo per visualizzare l'elenco dei tenant e gestire i relativi dati. 

- Il ruolo Web **Tailspin.Web.Survey.Public** ospita un sito Web ASP.NET dove gli utenti possono partecipare ai sondaggi pubblicati dai clienti di Tailspin. 

- Il ruolo di lavoro **Tailspin.Workers.Survey** esegue l'elaborazione in background. I ruoli Web inseriscono gli elementi di lavoro in una coda e il ruolo di lavoro elabora gli elementi. Vengono definite due attività in background: l'esportazione delle risposte al sondaggio nel database SQL di Azure e il calcolo delle statistiche per le risposte al sondaggio.

Oltre a Servizi cloud, l'applicazione Surveys usa altri servizi di Azure:

- **Archiviazione di Azure** per archiviare sondaggi, risposte ai sondaggi e informazioni relative ai tenant.

- **Cache Redis di Azure** per memorizzare nella cache alcuni dei dati archiviati in Archiviazione di Azure, per un accesso in lettura più veloce. 

- **Azure Active Directory** (Azure AD) per autenticare i clienti e gli amministratori di Tailspin.

- **Database SQL di Azure** per archiviare le risposte ai sondaggi per l'analisi. 

## <a name="moving-to-service-fabric"></a>Passaggio a Service Fabric

Come accennato, l'obiettivo di questa fase è la migrazione a Service Fabric con il numero minimo di modifiche necessarie. A tale scopo, sono stati creati servizi senza stato corrispondenti a ogni ruolo del servizio cloud nell'applicazione originale:

![](./images/tailspin02.png)

Questa architettura è intenzionalmente molto simile all'applicazione originale. Il diagramma nasconde tuttavia alcune importanti differenze. Nella parte restante di questo articolo verranno esaminate queste differenze. 

## <a name="converting-the-cloud-service-roles-to-services"></a>Conversione dei ruoli del servizio cloud in servizi

Come indicato, è stata eseguita la migrazione di ogni ruolo del servizio cloud a un servizio di Service Fabric. Poiché i ruoli del servizio cloud sono senza stato, per questa fase sono stati creati servizi senza stato in Service Fabric. 

Per la migrazione, sono stati eseguiti i passaggi descritti in [Guida alla conversione di ruoli di lavoro e Web in servizi senza stato di Service Fabric][sf-migration]. 

### <a name="creating-the-web-front-end-services"></a>Creazione dei servizi front-end Web

In Service Fabric un servizio viene eseguito all'interno di un processo creato dal runtime di Service Fabric. Per un front-end Web, ciò significa che il servizio non è in esecuzione in IIS. Il servizio deve invece ospitare un server Web. Questo approccio è definito *self-hosting*, perché il codice eseguito all'interno del processo funge da host del server Web. 

Il requisito di self-hosting implica che un servizio di Service Fabric non può usare ASP.NET MVC o Web Form ASP.NET, in quanto tali framework richiedono IIS e non supportano il self-hosting. Le opzioni di self-hosting includono:

- [ASP.NET Core][aspnet-core], per il cui self-hosting viene usato il server Web [Kestrel][kestrel]. 
- [API Web ASP.NET][aspnet-webapi], per il cui self-hosting viene usato [OWIN][owin].
- Framework di terze parti, ad esempio [Nancy](http://nancyfx.org/).

L'applicazione Surveys originale usa ASP.NET MVC. Poiché ASP.NET MVC non supporta il self-hosting in Service Fabric, sono state considerate le opzioni di migrazione seguenti:

- Trasferire i ruoli Web in ASP.NET Core, che supporta il self-hosting.
- Convertire il sito Web in un'applicazione a singola pagina, che chiama un'API Web implementata usando l'API Web ASP.NET. Ciò avrebbe richiesto una riprogettazione completa del front-end Web.
- Mantenere il codice ASP.NET MVC esistente e distribuire IIS in un contenitore di Windows Server in Service Fabric. Questo approccio non richiede modifiche al codice o richiede solo modifiche minime. 

La prima opzione, ovvero la migrazione ad ASP.NET Core, ha permesso di sfruttare le funzionalità più recenti in ASP.NET Core. Per la conversione sono stati seguiti i passaggi descritti in [Migrazione da ASP.NET MVC ad ASP.NET Core MVC][aspnet-migration]. 

> [!NOTE]
> Quando si usa ASP.NET Core con Kestrel, è consigliabile posizionare un proxy inverso davanti a Kestrel per gestire il traffico proveniente da Internet, per motivi di sicurezza. Per altre informazioni, vedere [Introduzione all'implementazione di server Web Kestrel in ASP.NET Core][kestrel]. La sezione [Distribuzione dell'applicazione](#deploying-the-application) descrive una distribuzione di Azure consigliata.

### <a name="http-listeners"></a>Listener HTTP

In Servizi cloud un ruolo Web o di lavoro espone un endpoint HTTP dichiarandolo nel [file di definizione del servizio][cloud-service-endpoints]. Un ruolo Web deve avere almeno un endpoint.

```xml
<!-- Cloud service endpoint -->
<Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
</Endpoints>
```

Analogamente, gli endpoint di Service Fabric vengono dichiarati in un manifesto del servizio: 

```xml
<!-- Service Fabric endpoint -->
<Endpoints>
    <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8002" />
</Endpoints>
```

A differenza di un ruolo del servizio cloud, tuttavia, i servizi di Service Fabric possono trovarsi nello stesso nodo. Per questo motivo, ogni servizio deve essere in ascolto su una porta distinta. Più avanti in questo articolo viene illustrato in che modo le richieste client sulla porta 80 o 443 vengono instradate alla porta corretta per il servizio.

Un servizio deve creare in modo esplicito i listener per ogni endpoint. Il motivo è che Service Fabric è indipendente dagli stack di comunicazione. Per altre informazioni, vedere [Compilare un front-end di servizio Web per l'applicazione tramite ASP.NET Core][sf-aspnet-core].

## <a name="packaging-and-configuration"></a>Creazione di pacchetti e configurazione

 Un servizio cloud contiene i file di configurazione e di pacchetto seguenti:

| File | DESCRIZIONE |
|------|-------------|
| Definizione del servizio (con estensione csdef) | Impostazioni usate da Azure per configurare il servizio cloud. Definisce i ruoli, gli endpoint, le attività di avvio e i nomi delle impostazioni di configurazione. |
| Configurazione del servizio (con estensione cscfg) | Impostazioni specifiche della distribuzione, inclusi il numero delle istanze del ruolo, i numeri di porta degli endpoint e i valori delle impostazioni di configurazione. 
| Pacchetto del servizio (con estensione cspkg) | Contiene il codice e le configurazioni dell'applicazione, oltre che il file di definizione del servizio.  |

C'è un file con estensione csdef per l'intera applicazione. Possono esserci più file con estensione cscfg per ambienti diversi, ad esempio locale, di test o di produzione. Quando il servizio è in esecuzione, è possibile aggiornare il file con estensione cscfg ma non quello con estensione csdef. Per altre informazioni, vedere [Cos'è il modello del servizio cloud e come è possibile crearne il pacchetto?][cloud-service-config]

Service Fabric prevede una divisione simile tra *definizione* del servizio e *impostazioni* del servizio, ma la struttura è più granulare. Per comprendere il modello di configurazione di Service Fabric, è utile per capire come viene creato un pacchetto di un'applicazione di Service Fabric. Ecco la struttura:

```
Application package
  - Service packages
    - Code package
    - Configuration package
    - Data package (optional)
```

Il pacchetto dell'applicazione è ciò che si distribuisce. Contiene uno o più pacchetti del servizio. Un pacchetto del servizio contiene i pacchetti di codice, configurazione e dati. Il pacchetto di codice contiene i file binari per i servizi e il pacchetto di configurazione contiene le impostazioni di configurazione. Questo modello consente di aggiornare i singoli servizi senza ridistribuire l'intera applicazione. Consente inoltre di aggiornare solo le impostazioni di configurazione senza ridistribuire il codice o riavviare il servizio.

Un'applicazione di Service Fabric contiene i file di configurazione seguenti:

| File | Località | DESCRIZIONE |
|------|----------|-------------|
| ApplicationManifest.xml | Pacchetto dell'applicazione | Definisce i servizi che costituiscono l'applicazione. |
| ServiceManifest.xml | Pacchetto del servizio| Descrive uno o più servizi. |
| Settings.xml | Pacchetto di configurazione | Contiene le impostazioni di configurazione per i servizi definiti nel pacchetto del servizio. |

Per altre informazioni, vedere [Modellare un'applicazione in Service Fabric][sf-application-model].

Per supportare diverse impostazioni di configurazione per più ambienti, usare l'approccio seguente, descritto in [Gestire i parametri dell'applicazione per più ambienti][sf-multiple-environments]:

1. Definire l'impostazione nel file Setting.xml per il servizio.
2. Nel manifesto dell'applicazione definire un override per l'impostazione.
3. Inserire le impostazioni specifiche dell'ambiente nel file di parametri dell'applicazione.

## <a name="deploying-the-application"></a>Distribuzione dell'applicazione

Mentre Servizi cloud di Azure è un servizio gestito, Service Fabric è un runtime. È possibile creare cluster di Service Fabric in molti ambienti, tra cui Azure o in locale. In questo articolo viene analizzata la distribuzione in Azure. 

Il diagramma seguente mostra una distribuzione consigliata:

![](./images/tailspin-cluster.png)

Il cluster di Service Fabric viene distribuito in un [set di scalabilità di macchine virtuali][vm-scale-sets]. I set di scalabilità sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire un set di macchine virtuali identiche. 

Come accennato, il server Web Kestrel richiede un proxy inverso per motivi di sicurezza. Il diagramma mostra il [gateway applicazione Azure][application-gateway], che è un servizio di Azure che offre diverse funzionalità di bilanciamento del carico di livello 7. Agisce come un servizio di proxy inverso, terminando la connessione di client e inoltrando richieste a endpoint di back-end. È possibile usare una soluzione di proxy inverso diversa, ad esempio nginx.  

### <a name="layer-7-routing"></a>Routing di livello 7

Nell'[applicazione Surveys originale](https://msdn.microsoft.com/library/hh534477.aspx#sec21) un ruolo Web è in ascolto sulla porta 80 e l'altro ruolo Web è in ascolto sulla porta 443. 

| Sito pubblico | Sito di gestione del sondaggio |
|-------------|------------------------|
| `http://tailspin.cloudapp.net` | `https://tailspin.cloudapp.net` |

Un'altra opzione prevede l'uso del routing di livello 7. In questo caso percorsi URL diversi vengono indirizzati a numeri di porta diversi nel back-end. Il sito pubblico può ad esempio usare i percorsi URL che iniziano con `/public/`. 

Le opzioni per il routing di livello 7 includono:

- Usare il gateway applicazione. 

- Usare un'appliance virtuale di rete, ad esempio nginx.

- Scrivere un gateway personalizzato come servizio senza stato.

Prendere in considerazione questo approccio se si hanno due o più servizi con endpoint HTTP pubblici, ma si vuole che vengano visualizzati come un sito con un singolo nome di dominio.

> Un approccio *non* consigliato è quello di consentire ai client esterni di inviare richieste tramite il [proxy inverso][sf-reverse-proxy] di Service Fabric. Sebbene sia possibile farlo, il proxy inverso è destinato alla comunicazione tra servizi. L'apertura ai client esterni espone *qualsiasi* servizio in esecuzione nel cluster che ha un endpoint HTTP.

### <a name="node-types-and-placement-constraints"></a>Tipi di nodo e vincoli di posizionamento

Nella distribuzione illustrata sopra tutti i servizi vengono eseguiti in tutti i nodi. È tuttavia anche possibile raggruppare i servizi, in modo che alcuni vengano eseguiti solo in nodi specifici all'interno del cluster. I motivi che possono portare ad adottare questo approccio includono:

- Esecuzione di alcuni servizi su tipi di macchine virtuali diversi. Alcuni servizi, ad esempio, potrebbero essere a elevato utilizzo di calcolo o richiedere unità GPU. Nel cluster di Service Fabric può esserci una combinazione di tipi di macchine virtuali.
- Isolamento dei servizi front-end dai servizi back-end per motivi di sicurezza. Tutti i servizi front-end verranno eseguiti in un set di nodi e i servizi back-end verranno eseguiti in nodi diversi nello stesso cluster.
- Requisiti di scalabilità diversi. Alcuni servizi possono richiedere più nodi di altri per l'esecuzione. Se, ad esempio, si definiscono nodi front-end e back-end, ogni set può essere ridimensionato in modo indipendente.

Il diagramma seguente mostra un cluster che separa i servizi front-end da quelli back-end:

![](././images/node-placement.png)

Per implementare questo approccio:

1. Quando si crea il cluster, definire due o più tipi di nodo. 
2. Per ogni servizio, usare i [vincoli di posizionamento][sf-placement-constraints] per assegnare il servizio a un tipo di nodo.

Quando si esegue la distribuzione in Azure, ogni tipo di nodo viene distribuito in un set di scalabilità di macchine virtuali distinto. Il cluster di Service Fabric include tutti i tipi di nodo. Per altre informazioni, vedere [Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali][sf-node-types].

> Se un cluster ha più tipi di nodo, un tipo viene designato come tipo di nodo *primario*. I servizi di runtime di Service Fabric, ad esempio il servizio di gestione cluster, vengono eseguiti nel tipo di nodo primario. Effettuare il provisioning di almeno 5 nodi per il tipo di nodo primario in un ambiente di produzione. L'altro tipo di nodo deve avere almeno 2 nodi.

## <a name="configuring-and-managing-the-cluster"></a>Configurazione e gestione del cluster

È necessario proteggere i cluster per evitare che utenti non autorizzati si connettano a essi. È consigliabile usare Azure AD per l'autenticazione dei client e i certificati X.509 per la sicurezza da nodo a nodo. Per altre informazioni, vedere [Scenari di sicurezza di un cluster di Service Fabric][sf-security].

Per configurare un endpoint HTTPS pubblico, vedere [Specificare le risorse in un manifesto del servizio][sf-manifest-resources].

È possibile scalare orizzontalmente l'applicazione aggiungendo macchine virtuali al cluster. I set di scalabilità di macchine virtuali supportano la scalabilità automatica tramite le regole di scalabilità automatica basate sui contatori delle prestazioni. Per altre informazioni vedere [Aumentare o ridurre le istanze del cluster di Service Fabric con le regole di scalabilità automatica][sf-auto-scale].

Quando il cluster è in esecuzione, è necessario raccogliere i log di tutti i nodi in una posizione centrale. Per altre informazioni, vedere [Raccogliere i log con Diagnostica di Azure][sf-logs].   

## <a name="conclusion"></a>Conclusioni

Il trasferimento dell'applicazione Surveys in Service Fabric è stato piuttosto semplice. Per riepilogare, sono state eseguite le operazioni seguenti:

- Conversione dei ruoli in servizi senza stato.
- Conversione dei front-end Web in ASP.NET Core.
- Modifica dei file di pacchetto e di configurazione in base al modello di Service Fabric.

La distribuzione è stata inoltre modificata da Servizi cloud a un cluster di Service Fabric in esecuzione in un set di scalabilità di macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

Al termine del trasferimento dell'applicazione Surveys, Tailspin vuole sfruttare i vantaggi delle funzionalità di Service Fabric, tra cui la distribuzione indipendente dei servizi e il controllo delle versioni. Per informazioni sul modo in cui Tailspin ha scomposto questi servizi in un'architettura più granulare per sfruttare i vantaggi di queste funzionalità di Service Fabric, vedere [Effettuare il refactoring di un'applicazione di Azure Service Fabric migrata da Servizi cloud di Azure][refactor-surveys]

<!-- links -->

[application-gateway]: /azure/application-gateway/
[aspnet-core]: /aspnet/core/
[aspnet-webapi]: https://www.asp.net/web-api
[aspnet-migration]: /aspnet/core/migration/mvc
[aspnet-hosting]: /aspnet/core/fundamentals/hosting
[aspnet-webapi]: https://www.asp.net/web-api
[azure-deployment-models]: /azure/azure-resource-manager/resource-manager-deployment-model
[cloud-service-autoscale]: /azure/cloud-services/cloud-services-how-to-scale-portal
[cloud-service-config]: /azure/cloud-services/cloud-services-model-and-package
[cloud-service-endpoints]: /azure/cloud-services/cloud-services-enable-communication-role-instances#worker-roles-vs-web-roles
[kestrel]: /aspnet/core/fundamentals/servers/kestrel
[lb-probes]: /azure/load-balancer/load-balancer-custom-probe-overview
[owin]: https://www.asp.net/aspnet/overview/owin-and-katana
[refactor-surveys]: refactor-migrated-app.md
[sample-code]: https://github.com/mspnp/cloud-services-to-service-fabric
[sf-application-model]: /azure/service-fabric/service-fabric-application-model
[sf-aspnet-core]: /azure/service-fabric/service-fabric-add-a-web-frontend
[sf-auto-scale]: /azure/service-fabric/service-fabric-cluster-scale-up-down
[sf-compare-cloud-services]: /azure/service-fabric/service-fabric-cloud-services-migration-differences
[sf-connect-and-communicate]: /azure/service-fabric/service-fabric-connect-and-communicate-with-services
[sf-containers]: /azure/service-fabric/service-fabric-containers-overview
[sf-logs]: /azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad
[sf-manifest-resources]: /azure/service-fabric/service-fabric-service-manifest-resources
[sf-migration]: /azure/service-fabric/service-fabric-cloud-services-migration-worker-role-stateless-service
[sf-multiple-environments]: /azure/service-fabric/service-fabric-manage-multiple-environment-app-configuration
[sf-node-types]: /azure/service-fabric/service-fabric-cluster-nodetypes
[sf-overview]: /azure/service-fabric/service-fabric-overview
[sf-placement-constraints]: /azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description
[sf-reliable-collections]: /azure/service-fabric/service-fabric-reliable-services-reliable-collections
[sf-reliable-services]: /azure/service-fabric/service-fabric-reliable-services-introduction
[sf-reverse-proxy]: /azure/service-fabric/service-fabric-reverseproxy
[sf-security]: /azure/service-fabric/service-fabric-cluster-security
[sf-why-microservices]: /azure/service-fabric/service-fabric-overview-microservices
[tailspin-book]: https://msdn.microsoft.com/library/ff966499.aspx
[tailspin-scenario]: https://msdn.microsoft.com/library/hh534482.aspx
[unity]: https://msdn.microsoft.com/library/ff647202.aspx
[vm-scale-sets]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
