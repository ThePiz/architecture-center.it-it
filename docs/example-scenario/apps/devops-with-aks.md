---
title: DevOps con Jenkins e il servizio Kubernetes di Azure
description: Soluzione collaudata per creare una pipeline DevOps per un'app Web Node.js con Jenkins, Registro contenitori di Azure, il servizio Kubernetes di Azure, Cosmos DB e Grafana.
author: iainfoulds
ms.date: 07/05/2018
ms.openlocfilehash: 9dbd1cb335f1c03c54ea342466594048e907d040
ms.sourcegitcommit: 5d99b195388b7cabba383c49a81390ac48f86e8a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37891337"
---
# <a name="deploy-a-container-based-devops-pipeline-for-modern-application-development-with-jenkins-and-azure-kubernetes-service"></a>Distribuire una pipeline DevOps basata su contenitori per lo sviluppo di applicazioni moderne con Jenkins e il servizio Kubernetes di Azure

Questo scenario di esempio è applicabile ad aziende che vogliono modernizzare lo sviluppo di applicazioni usando contenitori e flussi di lavoro DevOps. In questa soluzione, un'app Web Node.js viene compilata e distribuita tramite Jenkins in un'istanza di Registro contenitori di Azure e nel servizio Kubernetes di Azure. Per un livello database distribuito a livello globale, viene usato Azure Cosmos DB. Per monitorare le prestazioni dell'applicazione e risolvere i relativi problemi, Monitoraggio di Azure si integra con un dashboard e un'istanza di Grafana.

Gli scenari di applicazione di esempio includono l'implementazione di un ambiente di sviluppo automatizzato, la convalida di nuovi commit di codice e il push di nuove distribuzioni in ambienti di staging o di produzione. Generalmente le aziende devono creare e compilare manualmente le applicazioni e gli aggiornamenti e gestire una base di codice monolitica di grande dimensioni. Con un approccio moderno allo sviluppo di applicazioni con integrazione continua e distribuzione continua, è possibile compilare, testare e distribuire servizi più rapidamente. Questo approccio moderno consente di rilasciare applicazioni e aggiornamenti ai clienti più velocemente e di rispondere a esigenze aziendali mutevoli con maggiore flessibilità.

Con servizi di Azure come il servizio Kubernetes di Azure, Registro contenitori e Cosmos DB, le aziende possono usare le tecniche e gli strumenti di sviluppo di applicazioni più innovativi per semplificare il processo di implementazione della disponibilità elevata.

## <a name="potential-use-cases"></a>Potenziali casi d'uso

Prendere in considerazione questa soluzione per i casi d'uso seguenti:

* Modernizzazione delle procedure di sviluppo di applicazioni con un approccio basato su contenitori e microservizi.
* Velocizzazione dei cicli di vita di sviluppo e di distribuzione delle applicazioni.
* Automazione delle distribuzioni in ambienti di testing o di accettazione per la convalida.

## <a name="architecture"></a>Architettura

![Panoramica dell'architettura dei componenti di Azure coinvolti in una soluzione DevOps con Jenkins, Registro contenitori di Azure e il servizio Kubernetes di Azure][architecture]

Questa soluzione include una pipeline DevOps per un'applicazione Web Node.js e un database back-end. Il flusso dei dati attraverso la soluzione avviene come segue:

1. Uno sviluppatore apporta modifiche al codice sorgente dell'applicazione Web Node.js.
2. Viene eseguito il commit della modifica al codice in un repository di controllo del codice sorgente, come GitHub.
3. Per avviare il processo di integrazione continua, un webhook GitHub attiva la compilazione di un progetto Jenkins.
4. Il processo di compilazione Jenkins usa un agente di compilazione dinamico nel servizio Kubernetes di Azure per eseguire un processo di compilazione di contenitori.
5. Viene creata un'immagine del contenitore dal codice nel controllo del codice sorgente e ne viene quindi eseguito il push in un'istanza di Registro contenitori di Azure.
6. Tramite funzionalità di distribuzione continua, Jenkins distribuisce questa immagine del contenitore aggiornata nel cluster Kubernetes.
7. L'applicazione Web Node.js usa Azure Cosmos DB come back-end. Sia Cosmos DB che il servizio Kubernetes di Azure segnalano le metriche a Monitoraggio di Azure.
8. Un'istanza di Grafana offre dashboard visivi delle prestazioni dell'applicazione in base ai dati provenienti da Monitoraggio di Azure.

### <a name="components"></a>Componenti

* [Jenkins][jenkins] è un server di automazione open source che può integrarsi con i servizi di Azure per supportare l'integrazione continua e la distribuzione continua. In questa soluzione, Jenkins orchestra la creazione di nuove immagini del contenitore in base ai commit nel controllo del codice sorgente, esegue il push di tali immagini in Registro contenitori di Azure e quindi aggiorna le istanze dell'applicazione nel servizio Kubernetes di Azure.
* [Macchine virtuali Linux di Azure][azurevm-docs] vengono usate per eseguire le istanze di Jenkins e Grafana.
* [Registro contenitori di Azure][azureacr-docs] archivia e gestisce le immagini del contenitore usate dal cluster del servizio Kubernetes di Azure. Le immagini vengono archiviate in modo sicuro e possono essere replicate dalla piattaforma Azure in altre aree per ridurre i tempi di distribuzione.
* Il [servizio Kubernetes di Azure][azureaks-docs] è una piattaforma Kubernetes gestita che consente di distribuire e gestire applicazioni in contenitori senza competenze nell'orchestrazione di contenitori. Come servizio Kubernetes ospitato, Azure gestisce attività critiche quali il monitoraggio dell'integrità e la manutenzione per l'utente.
* [Azure Cosmos DB][azurecosmosdb-docs] è un database multimodello distribuito a livello globale che consente di scegliere tra vari modelli di database e di coerenza in base alle esigenze. Con Cosmos DB è possibile eseguire la replica dei dati a livello globale e non è necessario distribuire e configurare componenti di replica o gestione cluster.
* [Monitoraggio di Azure][azuremonitor-docs] consente di tenere traccia delle prestazioni, gestire la sicurezza e identificare le tendenze. Le metriche ottenute da Monitoraggio possono essere usate da altri strumenti e risorse, come Grafana.
* [Grafana][grafana] è una soluzione open source per l'esecuzione di query, la visualizzazione, la generazione di avvisi e la comprensione delle metriche. Un plug-in di origine dati per Monitoraggio di Azure consente a Grafana di creare dashboard visivi per monitorare le prestazioni delle applicazioni che vengono eseguite nel servizio Kubernetes di Azure e usano Cosmos DB.

### <a name="alternatives"></a>Alternative

* [Visual Studio Team Services][vsts] e Team Foundation Server consentono di implementare una pipeline di integrazione, test e distribuzione continui per qualsiasi app.
* [Kubernetes][kubernetes] può essere eseguito direttamente in VM di Azure anziché tramite un servizio gestito, se si vuole ottenere maggiore controllo sul cluster.
* [Service Fabric][service-fabric] è un altro agente di orchestrazione di contenitori che può sostituire il servizio Kubernetes di Azure.

## <a name="considerations"></a>Considerazioni

### <a name="availability"></a>Disponibilità

Per monitorare le prestazioni dell'applicazione e segnalare i problemi, questa soluzione combina Monitoraggio di Azure e Grafana per offrire dashboard visivi. Questi strumenti consentono il monitoraggio e la risoluzione dei problemi di prestazioni che potrebbero richiedere aggiornamenti del codice, che possono quindi essere distribuiti tutti con la pipeline di integrazione continua/distribuzione continua.

Nell'ambito del cluster del servizio Kubernetes di Azure, un servizio di bilanciamento del carico distribuisce il traffico dell'applicazione a uno o più contenitori (pod) in cui viene eseguita. Questo approccio per l'esecuzione di applicazioni in contenitori in Kubernetes offre un'infrastruttura a disponibilità elevata per i clienti.

Per altri argomenti relativi alla scalabilità, vedere l'[elenco di controllo per la disponibilità][availability] in Centro architetture.

### <a name="scalability"></a>Scalabilità

Il servizio Kubernetes di Azure consente di ridimensionare il numero di nodi del cluster in base alle esigenze delle applicazioni. Con la crescita dell'applicazione, è possibile aumentare il numero di nodi Kubernetes che eseguono il servizio.

I dati dell'applicazione vengono archiviati in Azure Cosmos DB, un database multimodello con distribuzione e scalabilità a livello globale. Con Cosmos DB non è necessario ridimensionare l'infrastruttura come con i componenti di database tradizionali ed è possibile scegliere di eseguire la replica di Cosmos DB a livello globale per soddisfare le esigenze dei clienti.

Per altri argomenti relativi alla scalabilità, vedere l'[elenco di controllo per la scalabilità][scalability] disponibile in Centro architetture.

### <a name="security"></a>Sicurezza

Per ridurre al minimo la superficie di attacco, questa soluzione non espone l'istanza di VM Jenkins tramite HTTP. Per tutte le attività di gestione che richiedono l'interazione con Jenkins, si crea una connessione remota sicura usando un tunnel SSH dal computer locale. Per le istanze di VM Jenkins e Grafana è consentita solo l'autenticazione con chiave pubblica SSH. Gli accessi basati su password sono disabilitati. Per altre informazioni, vedere [Eseguire un server Jenkins in Azure](../../reference-architectures/jenkins/index.md).

Per la separazione delle credenziali e delle autorizzazioni, questa soluzione usa un'entità servizio dedicata di Azure Active Directory (AD). Le credenziali per questa entità servizio vengono archiviate come oggetto credenziali sicuro in Jenkins in modo che non siano direttamente esposte e visibili negli script o nella pipeline di compilazione.

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

Questa soluzione usa il servizio Kubernetes di Azure per l'applicazione. In Kubernetes sono integrati componenti di resilienza che monitorano e riavviano i contenitori (pod) in caso di problemi. Con l'esecuzione di più nodi Kubernetes, l'applicazione può tollerare l'indisponibilità di un pod o un nodo.

Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].

## <a name="deploy-the-solution"></a>Distribuire la soluzione

**Prerequisiti**

* È necessario un account Azure esistente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* È necessaria una coppia di chiavi con chiave pubblica SSH. Per la procedura per crearne una, vedere [Creare e usare una coppia di chiavi SSH per le macchine virtuali Linux][sshkeydocs].
* È necessaria un'entità servizio di Azure Active Directory (AD) per l'autenticazione del servizio e delle risorse. Se necessario, è possibile creare un'entità servizio con [az ad sp create-for-rbac][createsp].

    ```azurecli-interactive
    az ad sp create-for-rbac --name myDevOpsSolution
    ```

    Prendere nota dei valori di *appId* e *password* nell'output di questo comando, perché verranno passati al modello durante la distribuzione della soluzione.

Per distribuire la soluzione con un modello di Azure Resource Manager, seguire questa procedura.

1. Fare clic sul pulsante **Distribuisci in Azure**:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fdevops-with-aks%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Attendere l'apertura della distribuzione del modello nel portale di Azure e quindi completare la procedura seguente:
   * Scegliere **Crea nuovo** per creare un nuovo gruppo di risorse e quindi specificare un nome, ad esempio *myAKSDevOpsSolution*, nella casella di testo.
   * Selezionare un'area nella casella a discesa **Località**.
   * Immettere l'ID app e la password dell'entità servizio ottenuti con il comando `az ad sp create-for-rbac`.
   * Specificare un nome utente e una password sicura per l'istanza di Jenkins e la console di Grafana.
   * Specificare una chiave SSH per proteggere gli accessi alle VM Linux.
   * Esaminare le condizioni e quindi selezionare **Accetto le condizioni riportate sopra**.
   * Selezionare il pulsante **Acquista**.

Il completamento della distribuzione può richiedere 15-20 minuti.

## <a name="pricing"></a>Prezzi

Per esaminare il costo di esecuzione della soluzione, nel calcolatore dei costi sono preconfigurati tutti i servizi. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base al traffico previsto.

Sono stati definiti tre profili di costo di esempio in base al numero di immagini di contenitori da archiviare e ai nodi Kubernetes per l'esecuzione delle applicazioni.

* [Small][small-pricing]: correlato a 1000 compilazioni di contenitori al mese.
* [Medium][medium-pricing]: correlato a 100.000 compilazioni di contenitori al mese.
* [Large][large-pricing]: correlato a 1.000.000 compilazioni di contenitori al mese.

## <a name="related-resources"></a>Risorse correlate

In questa soluzione sono stati usati Registro contenitori di Azure e il servizio Kubernetes di Azure per archiviare ed eseguire applicazioni basate su contenitori. Per eseguire applicazioni basate su contenitori senza dover effettuare il provisioning di componenti di orchestrazione, è anche possibile usare Istanze di contenitore di Azure. Per altre informazioni, vedere la [panoramica di Istanze di contenitore di Azure][azureaci-docs].

<!-- links -->
[architecture]: ./media/devops-with-aks/architecture-devops-with-aks.png
[autoscaling]: ../../best-practices/auto-scaling.md
[availability]: ../../checklist/availability.md
[azureaci-docs]: /azure/container-instances/container-instances-overview
[azureacr-docs]: /azure/container-registry/container-registry-intro
[azurecosmosdb-docs]: /azure/cosmos-db/introduction
[azureaks-docs]: /azure/aks/intro-kubernetes
[azuremonitor-docs]: /azure/monitoring-and-diagnostics/monitoring-overview
[azurevm-docs]: /azure/virtual-machines/linux/overview
[createsp]: /cli/azure/ad/sp#az-ad-sp-create
[grafana]: https://grafana.com/
[jenkins]: https://jenkins.io/
[resiliency]: ../../resiliency/index.md
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[scalability]: ../../checklist/scalability.md
[sshkeydocs]: /azure/virtual-machines/linux/mac-create-ssh-keys
[vsts]: /vsts/?view=vsts
[kubernetes]: https://kubernetes.io/
[service-fabric]: /azure/service-fabric/

[small-pricing]: https://azure.com/e/841f0a75b1ea4802ba1ac8f7918a71e7
[medium-pricing]: https://azure.com/e/eea0e6d79b4e45618a96d33383ec77ba
[large-pricing]: https://azure.com/e/3faab662c54c473da55a1e93a27e0e64