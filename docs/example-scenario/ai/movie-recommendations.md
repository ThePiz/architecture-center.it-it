---
title: Consigli cinematografici in Azure
description: Usare Machine Learning per automatizzare i consigli sui film, sui prodotti e di altro tipo, usando Machine Learning e Azure Data Science Virtual Machine (DSVM) per eseguire il training di un modello in Azure.
author: njray
ms.date: 01/09/2019
ms.custom: azcat-ai, AI
social_image_url: /azure/architecture/example-scenario/ai/media/architecture-movie-recommender.png
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.openlocfilehash: be7959c1201e3a2aaf92c192d73a0ca47a990934
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249646"
---
# <a name="movie-recommendations-on-azure"></a>Consigli cinematografici in Azure

Questo scenario di esempio mostra come usare tecniche di Machine Learning in un'organizzazione per automatizzare i consigli sui prodotti per i clienti. Viene usata una soluzione Azure Data Science Virtual Machine (DSVM) per eseguire il training di un modello in Azure che consiglia i film agli utenti in base alle valutazioni che hanno ricevuto.

I consigli possono rivelarsi utili in vari settori, come la vendita al dettaglio, le notizie e i media. Le possibili applicazioni includono la distribuzione di consigli sui prodotti negli store virtuali, su notizie o post o oppure su brani musicali. Solitamente le organizzazioni devono assumere e formare assistenti per creare consigli personalizzati per i clienti. Oggi possiamo invece offrire consigli personalizzati su vasta scala utilizzando Azure per eseguire il training di modelli e interpretare le preferenze dei clienti.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Prendere in considerazione questo scenario per i casi d'uso seguenti:

* Consigli sui film in un sito Web.
* Consigli su prodotti di consumo in un'app per dispositivi mobili.
* Consigli sulle notizie nei media di streaming.

## <a name="architecture"></a>Architettura

![Architettura di un modello di Machine Learning per il training di raccomandazioni per film][architecture]

Questo scenario riguarda il training e la valutazione del modello di Machine Learning basato sull'algoritmo Spark [Alternating Least Squares][als] (ALS) usato con un set di dati di valutazioni dei film. I passaggi dello scenario sono i seguenti:

1. Il sito Web o il servizio di app front-end raccoglie dati cronologici delle interazioni tra utenti e film, rappresentati in una tabella di tuple relative a utenti, elementi e valutazioni numeriche.

2. I dati cronologici raccolti vengono memorizzati in un'archiviazione BLOB.

3. Per sperimentare con un modello di generazione di consigli Spark ALS o per trasferirlo in produzione si usa spesso un ambiente DSVM. Il training del modello ASL viene eseguito con un set di dati di training, generato dal set di dati complessivo applicando la strategia appropriata di divisione, che può essere effettuata in modo casuale, cronologico o stratificato, a seconda dei requisiti dell'organizzazione. Analogamente ad altre attività di Machine Learning, il modello per la generazione di consigli viene convalidato con metriche di valutazione, ad esempio precision\@*k*, recall\@*k*, [MAP][map], [nDCG\@k][ndcg].

4. Il servizio Azure Machine Learning viene usato per il coordinamento e la sperimentazione, ad esempio l'ottimizzazione degli iperparametri e la gestione dei modelli.

5. Un modello sottoposto a training viene preservato in Azure Cosmos DB ed è quindi possibile applicarlo per consigliare i primi *k* film per uno specifico utente.

6. Il modello viene poi distribuito in un sito Web o un servizio di app tramite istanze di Azure Container o il servizio Azure Kubernetes.

Per una guida approfondita sulla creazione e la scalabilità di un servizio di generazione di consigli, vedere [Creare un'API per raccomandazioni in tempo reale in Azure][ref-arch].

### <a name="components"></a>Componenti

* [Data Science Virtual Machine][dsvm] (DSVM), una macchina virtuale di Azure con framework per Deep Learning e strumenti per Machine Learning e data science. DSVM include un ambiente Spark autonomo che è possibile usare per eseguire ALS.

* [Archiviazione BLOB di Azure][blob], che memorizza il set di dati per i consigli sui film.

* [Servizio Azure Machine Learning][mls], per accelerare le attività di creazione, gestione e distribuzione dei modelli di Machine Learning.

* [Azure Cosmos DB][cosmosdb], un servizio di database multimodello e distribuito a livello globale.

* [Istanze di Azure Container][aci], per distribuire i modelli sottoposti a training nei servizi Web o di app, facoltativamente usando il [servizio Azure Kubernetes][aks].

### <a name="alternatives"></a>Alternative

[Azure Databricks][databricks] è un cluster Spark gestito in cui vengono eseguiti il training e la valutazione dei modelli. Un ambiente Spark gestito può essere configurato in pochi minuti e, grazie alla [scalabilità automatica][autoscale] in verticale e in orizzontale, consente di ridurre le risorse e i costi associati al ridimensionamento manuale dei cluster. Un'altra opzione per risparmiare risorse consiste nel configurare il termine automatico dei [cluster][clusters] inattivi.

## <a name="considerations"></a>Considerazioni

### <a name="availability"></a>Disponibilità

Le app destinate a Machine Learning sono suddivise in due componenti: risorse per il training e risorse per la fornitura del servizio. Le risorse necessarie per il training non richiedono in genere disponibilità elevata, perché non sono interessate direttamente dalle richieste di produzione in tempo reale. Le risorse necessarie per la fornitura del servizio devono invece avere disponibilità elevata per rispondere alle richieste dei clienti.

Per il training, la soluzione DSVM è disponibile in [più aree geografiche][regions] del mondo e soddisfa il [contratto di servizio][sla] (SLA) per le macchine virtuali. Per la fornitura del servizio, il servizio Azure Kubernetes offre un'infrastruttura a [disponibilità elevata][ha]. Anche i nodi dell'agente rispettano lo [SLA][sla-aks] per le macchine virtuali.

### <a name="scalability"></a>Scalabilità

In caso di dati di grandi dimensioni, la soluzione DSVM può essere scalabile per ridurre i tempi di training. È infatti possibile applicare scalabilità verticale o orizzontale a una VM [cambiandone le dimensioni][vm-size]. Scegliere una memoria di dimensioni sufficienti per inserirvi il set di dati e un numero di vCPU più elevato per ridurre la quantità di tempo necessaria per il training.

### <a name="security"></a>Security

Questo scenario può usare Azure Active Directory per autenticare gli utenti per l'[accesso a DSVM][dsvm-id], che contiene codice, modelli e dati in memoria. I dati vengono memorizzati in Archiviazione di Azure prima di essere caricati in una soluzione DSVM, dove vengono crittografati automaticamente con la [crittografia del servizio di archiviazione][storage-security]. Le autorizzazioni possono essere gestite tramite autenticazione di Azure Active Directory o con il controllo degli accessi in base al ruolo.

## <a name="deploy-this-scenario"></a>Distribuire lo scenario

**Prerequisiti**: È necessario un account Azure esistente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free] prima di iniziare.

Tutto il codice di questo scenario è disponibile nel [repository Microsoft Recommenders][github].

Seguire questi passaggi per eseguire il [notebook di avvio rapido ALS][notebook]:

1. [Creare una soluzione DSVM][dsvm-ubuntu] nel portale di Azure.

2. Clonare il repository nella cartella Notebook:

    ```shell
    cd notebooks
    git clone https://github.com/Microsoft/Recommenders
    ```

3. Installare le dipendenze Conda seguendo i passaggi descritti nel file [SETUP.md][setup].

4. Nel browser accedere alla VM jupyterlab e passare a `notebooks/00_quick_start/als_pyspark_movielens.ipynb`.

5. Eseguire il notebook.

## <a name="related-resources"></a>Risorse correlate

Per una guida approfondita sulla creazione e la scalabilità di un servizio di generazione di consigli, vedere [Creare un'API per raccomandazioni in tempo reale in Azure][ref-arch]. Per trovare esercitazioni ed esempi dei sistemi consigliati, visitare il [repository Microsoft Recommenders][github].

[architecture]: ./media/architecture-movie-recommender.png
[aci]: /azure/container-instances/container-instances-overview
[aad]: /azure/active-directory-b2c/active-directory-b2c-overview
[aks]: /azure/aks/intro-kubernetes
[als]: https://spark.apache.org/docs/latest/ml-collaborative-filtering.html
[autoscale]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html#autoscaling
[blob]: /azure/storage/blobs/storage-blobs-introduction
[clusters]: https://docs.azuredatabricks.net/user-guide/clusters/configure.html
[cosmosdb]: /azure/cosmos-db/introduction
[databricks]: /azure/azure-databricks/what-is-azure-databricks
[dsvm]: /azure/machine-learning/data-science-virtual-machine/overview
[dsvm-id]: /azure/machine-learning/data-science-virtual-machine/dsvm-common-identity
[dsvm-ubuntu]: /azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[github]: https://github.com/Microsoft/Recommenders
[ha]: /azure/aks/container-service-quotas
[map]: https://en.wikipedia.org/wiki/Evaluation_measures_(information_retrieval)
[mls]: /azure/machine-learning/service/
[n-tier]: /azure/architecture/reference-architectures/n-tier/n-tier-cassandra
[ndcg]: https://en.wikipedia.org/wiki/Discounted_cumulative_gain
[notebook]: https://github.com/Microsoft/Recommenders/notebooks/00_quick_start/als_pyspark_movielens.ipynb
[ref-arch]: /azure/architecture/reference-architectures/ai/real-time-recommendation
[regions]: https://azure.microsoft.com/en-us/global-infrastructure/services/?products=virtual-machines&regions=all
[resiliency]: /azure/architecture/resiliency/
[sec-docs]: /azure/security/
[setup]: https://github.com/Microsoft/Recommenders/blob/master/SETUP.md%60
[sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/
[sla-aks]: https://azure.microsoft.com/en-us/support/legal/sla/kubernetes-service/v1_0/
[storage-security]: /azure/storage/common/storage-service-encryption
[vm-size]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
