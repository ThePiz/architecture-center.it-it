---
title: Consigli cinematografici in Azure
description: Usare Machine Learning per automatizzare i consigli sui film, sui prodotti e di altro tipo, usando Machine Learning e Azure Data Science Virtual Machine (DSVM) per eseguire il training di un modello in Azure.
author: njray
ms.date: 1/9/2019
ms.custom: azcat-ai
social_image_url: /azure/architecture/example-scenario/ai/media/architecture-movie-recommender.png
ms.openlocfilehash: 9e68f38cb61d7a3255b76a662c58907704914052
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908257"
---
# <a name="movie-recommendations-on-azure"></a><span data-ttu-id="b2987-103">Consigli cinematografici in Azure</span><span class="sxs-lookup"><span data-stu-id="b2987-103">Movie recommendations on Azure</span></span>

<span data-ttu-id="b2987-104">Questo scenario di esempio mostra come usare tecniche di Machine Learning in un'organizzazione per automatizzare i consigli sui prodotti per i clienti.</span><span class="sxs-lookup"><span data-stu-id="b2987-104">This example scenario shows how a business can use machine learning to automate product recommendations for their customers.</span></span> <span data-ttu-id="b2987-105">Viene usata una soluzione Azure Data Science Virtual Machine (DSVM) per eseguire il training di un modello in Azure che consiglia i film agli utenti in base alle valutazioni che hanno ricevuto.</span><span class="sxs-lookup"><span data-stu-id="b2987-105">An Azure Data Science Virtual Machine (DSVM) is used to train a model on Azure that recommends movies to users based on ratings that have been given to movies.</span></span>

<span data-ttu-id="b2987-106">I consigli possono rivelarsi utili in vari settori, come la vendita al dettaglio, le notizie e i media.</span><span class="sxs-lookup"><span data-stu-id="b2987-106">Recommendations can be useful in various industries from retail to news to media.</span></span> <span data-ttu-id="b2987-107">Le possibili applicazioni includono la distribuzione di consigli sui prodotti negli store virtuali, su notizie o post o oppure su brani musicali.</span><span class="sxs-lookup"><span data-stu-id="b2987-107">Potential applications include providing  product recommendations in a virtual store, providing news or post recommendations, or providing music recommendations.</span></span> <span data-ttu-id="b2987-108">Solitamente le organizzazioni devono assumere e formare assistenti per creare consigli personalizzati per i clienti.</span><span class="sxs-lookup"><span data-stu-id="b2987-108">Traditionally, businesses had to hire and train assistants to make personalized recommendations to customers.</span></span> <span data-ttu-id="b2987-109">Oggi possiamo invece offrire consigli personalizzati su vasta scala utilizzando Azure per eseguire il training di modelli e interpretare le preferenze dei clienti.</span><span class="sxs-lookup"><span data-stu-id="b2987-109">Today, we can  provide customized recommendations at scale by utilizing Azure to train models to understand customer preferences.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="b2987-110">Casi d'uso pertinenti</span><span class="sxs-lookup"><span data-stu-id="b2987-110">Relevant use cases</span></span>

<span data-ttu-id="b2987-111">Prendere in considerazione questo scenario per i casi d'uso seguenti:</span><span class="sxs-lookup"><span data-stu-id="b2987-111">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="b2987-112">Consigli sui film in un sito Web.</span><span class="sxs-lookup"><span data-stu-id="b2987-112">Movie recommendations on a website.</span></span>
* <span data-ttu-id="b2987-113">Consigli su prodotti di consumo in un'app per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="b2987-113">Consumer product recommendations in a mobile app.</span></span>
* <span data-ttu-id="b2987-114">Consigli sulle notizie nei media di streaming.</span><span class="sxs-lookup"><span data-stu-id="b2987-114">News recommendations on streaming media.</span></span>

## <a name="architecture"></a><span data-ttu-id="b2987-115">Architettura</span><span class="sxs-lookup"><span data-stu-id="b2987-115">Architecture</span></span>

![Architettura di un modello di Machine Learning per il training di raccomandazioni per film][architecture]

<span data-ttu-id="b2987-117">Questo scenario riguarda il training e la valutazione del modello di Machine Learning basato sull'algoritmo Spark [Alternating Least Squares][als] (ALS) usato con un set di dati di valutazioni dei film.</span><span class="sxs-lookup"><span data-stu-id="b2987-117">This scenario covers the training and evaluating of the machine learning model using the Spark [alternating least squares][als] (ALS) algorithm on a dataset of movie ratings.</span></span> <span data-ttu-id="b2987-118">I passaggi dello scenario sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="b2987-118">The steps for this scenario are as following:</span></span>

1. <span data-ttu-id="b2987-119">Il sito Web o il servizio di app front-end raccoglie dati cronologici delle interazioni tra utenti e film, rappresentati in una tabella di tuple relative a utenti, elementi e valutazioni numeriche.</span><span class="sxs-lookup"><span data-stu-id="b2987-119">The front-end website or app service collects historical data of user-movie interactions, which are represented in a table of user, item, and numerical rating tuples.</span></span>

2. <span data-ttu-id="b2987-120">I dati cronologici raccolti vengono memorizzati in un'archiviazione BLOB.</span><span class="sxs-lookup"><span data-stu-id="b2987-120">The collected historical data is stored in a blob storage.</span></span>

3. <span data-ttu-id="b2987-121">Per sperimentare con un modello di generazione di consigli Spark ALS o per trasferirlo in produzione si usa spesso un ambiente DSVM.</span><span class="sxs-lookup"><span data-stu-id="b2987-121">A DSVM is often used to experiment with or productize a Spark ALS recommender model.</span></span> <span data-ttu-id="b2987-122">Il training del modello ASL viene eseguito con un set di dati di training, generato dal set di dati complessivo applicando la strategia appropriata di divisione,</span><span class="sxs-lookup"><span data-stu-id="b2987-122">The ALS model is trained using a training dataset, which is produced from the overall dataset by applying the appropriate data splitting strategy.</span></span> <span data-ttu-id="b2987-123">che può essere effettuata in modo casuale, cronologico o stratificato, a seconda dei requisiti dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="b2987-123">For example, the dataset can be split into sets randomly, chronologically, or stratified, depending on the business requirement.</span></span> <span data-ttu-id="b2987-124">Analogamente ad altre attività di Machine Learning, il modello per la generazione di consigli viene convalidato con metriche di valutazione, ad esempio precision\@*k*, recall\@*k*, [MAP][map], [nDCG\@k][ndcg].</span><span class="sxs-lookup"><span data-stu-id="b2987-124">Similar to other machine learning tasks, a recommender is validated by using evaluation metrics (for example, precision\@*k*, recall\@*k*, [MAP][map], [nDCG\@k][ndcg]).</span></span>

4. <span data-ttu-id="b2987-125">Il servizio Azure Machine Learning viene usato per il coordinamento e la sperimentazione, ad esempio l'ottimizzazione degli iperparametri e la gestione dei modelli.</span><span class="sxs-lookup"><span data-stu-id="b2987-125">Azure Machine Learning service is used for coordinating the experimentation, such as hyperparameter sweeping and model management.</span></span>

5. <span data-ttu-id="b2987-126">Un modello sottoposto a training viene preservato in Azure Cosmos DB ed è quindi possibile applicarlo per consigliare i primi *k* film per uno specifico utente.</span><span class="sxs-lookup"><span data-stu-id="b2987-126">A trained model is preserved on Azure Cosmos DB, which can then be applied for recommending the top *k* movies for a given user.</span></span>

6. <span data-ttu-id="b2987-127">Il modello viene poi distribuito in un sito Web o un servizio di app tramite istanze di Azure Container o il servizio Azure Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="b2987-127">The model is then deployed onto a web or app service by using Azure Container Instances or Azure Kubernetes Service.</span></span>

<span data-ttu-id="b2987-128">Per una guida approfondita sulla creazione e la scalabilità di un servizio di generazione di consigli, vedere [Creare un'API per raccomandazioni in tempo reale in Azure][ref-arch].</span><span class="sxs-lookup"><span data-stu-id="b2987-128">For an in-depth guide to building and scaling a recommender service, see [Build a real-time recommendation API on Azure][ref-arch].</span></span>

### <a name="components"></a><span data-ttu-id="b2987-129">Componenti</span><span class="sxs-lookup"><span data-stu-id="b2987-129">Components</span></span>

* <span data-ttu-id="b2987-130">[Data Science Virtual Machine][dsvm] (DSVM), una macchina virtuale di Azure con framework per Deep Learning e strumenti per Machine Learning e data science.</span><span class="sxs-lookup"><span data-stu-id="b2987-130">[Data Science Virtual Machine][dsvm] (DSVM) is an Azure virtual machine with deep learning frameworks and tools for machine learning and data science.</span></span> <span data-ttu-id="b2987-131">DSVM include un ambiente Spark autonomo che è possibile usare per eseguire ALS.</span><span class="sxs-lookup"><span data-stu-id="b2987-131">The DSVM has a standalone Spark environment that can be used to run ALS.</span></span>

* <span data-ttu-id="b2987-132">[Archiviazione BLOB di Azure][blob], che memorizza il set di dati per i consigli sui film.</span><span class="sxs-lookup"><span data-stu-id="b2987-132">[Azure Blob storage][blob] stores the dataset for movie recommendations.</span></span>

* <span data-ttu-id="b2987-133">[Servizio Azure Machine Learning][mls], per accelerare le attività di creazione, gestione e distribuzione dei modelli di Machine Learning.</span><span class="sxs-lookup"><span data-stu-id="b2987-133">[Azure Machine Learning service][mls] is used to accelerate the building, managing, and deploying of machine learning models.</span></span>

* <span data-ttu-id="b2987-134">[Azure Cosmos DB][cosmosdb], un servizio di database multimodello e distribuito a livello globale.</span><span class="sxs-lookup"><span data-stu-id="b2987-134">[Azure Cosmos DB][cosmosdb] enables globally distributed and multi-model database storage.</span></span>

* <span data-ttu-id="b2987-135">[Istanze di Azure Container][aci], per distribuire i modelli sottoposti a training nei servizi Web o di app, facoltativamente usando il [servizio Azure Kubernetes][aks].</span><span class="sxs-lookup"><span data-stu-id="b2987-135">[Azure Container Instances][aci] is used to deploy the trained models to web or app services, optionally using [Azure Kubernetes Service][aks].</span></span>

### <a name="alternatives"></a><span data-ttu-id="b2987-136">Alternative</span><span class="sxs-lookup"><span data-stu-id="b2987-136">Alternatives</span></span>

<span data-ttu-id="b2987-137">[Azure Databricks][databricks] è un cluster Spark gestito in cui vengono eseguiti il training e la valutazione dei modelli.</span><span class="sxs-lookup"><span data-stu-id="b2987-137">[Azure Databricks][databricks] is a managed Spark cluster where model training and evaluating is performed.</span></span> <span data-ttu-id="b2987-138">Un ambiente Spark gestito può essere configurato in pochi minuti e, grazie alla [scalabilità automatica][autoscale] in verticale e in orizzontale, consente di ridurre le risorse e i costi associati al ridimensionamento manuale dei cluster.</span><span class="sxs-lookup"><span data-stu-id="b2987-138">You can set up a managed Spark environment in minutes, and [autoscale][autoscale] up and down to help reduce the resources and costs associated with scaling clusters manually.</span></span> <span data-ttu-id="b2987-139">Un'altra opzione per risparmiare risorse consiste nel configurare il termine automatico dei [cluster][clusters] inattivi.</span><span class="sxs-lookup"><span data-stu-id="b2987-139">Another resource-saving option is to configure inactive [clusters][clusters] to terminate automatically.</span></span>

## <a name="considerations"></a><span data-ttu-id="b2987-140">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="b2987-140">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="b2987-141">Disponibilità</span><span class="sxs-lookup"><span data-stu-id="b2987-141">Availability</span></span>

<span data-ttu-id="b2987-142">Le app destinate a Machine Learning sono suddivise in due componenti: risorse per il training e risorse per la fornitura del servizio.</span><span class="sxs-lookup"><span data-stu-id="b2987-142">Machine-learning-built apps are split into two resource components: resources for training, and resources for serving.</span></span> <span data-ttu-id="b2987-143">Le risorse necessarie per il training non richiedono in genere disponibilità elevata, perché non sono interessate direttamente dalle richieste di produzione in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="b2987-143">Resources required for training generally do not need  high availability, as live production requests do not directly hit these resources.</span></span> <span data-ttu-id="b2987-144">Le risorse necessarie per la fornitura del servizio devono invece avere disponibilità elevata per rispondere alle richieste dei clienti.</span><span class="sxs-lookup"><span data-stu-id="b2987-144">Resources required for serving need to have high availability to serve customer requests.</span></span>

<span data-ttu-id="b2987-145">Per il training, la soluzione DSVM è disponibile in [più aree geografiche][regions] del mondo e soddisfa il [contratto di servizio][sla] (SLA) per le macchine virtuali.</span><span class="sxs-lookup"><span data-stu-id="b2987-145">For training, the DSVM is available in [multiple regions][regions] around the globe and meets the [service level agreement][sla] (SLA) for virtual machines.</span></span> <span data-ttu-id="b2987-146">Per la fornitura del servizio, il servizio Azure Kubernetes offre un'infrastruttura a [disponibilità elevata][ha].</span><span class="sxs-lookup"><span data-stu-id="b2987-146">For serving, Azure Kubernetes Service provides a [highly available][ha] infrastructure.</span></span> <span data-ttu-id="b2987-147">Anche i nodi dell'agente rispettano lo [SLA][sla-aks] per le macchine virtuali.</span><span class="sxs-lookup"><span data-stu-id="b2987-147">Agent nodes also follow the [SLA][sla-aks] for virtual machines.</span></span>

### <a name="scalability"></a><span data-ttu-id="b2987-148">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="b2987-148">Scalability</span></span>

<span data-ttu-id="b2987-149">In caso di dati di grandi dimensioni, la soluzione DSVM può essere scalabile per ridurre i tempi di training.</span><span class="sxs-lookup"><span data-stu-id="b2987-149">If you have a large data size, you can scale your DSVM to shorten training time.</span></span> <span data-ttu-id="b2987-150">È infatti possibile applicare scalabilità verticale o orizzontale a una VM [cambiandone le dimensioni][vm-size].</span><span class="sxs-lookup"><span data-stu-id="b2987-150">You can scale a VM up or down by changing the [VM size][vm-size].</span></span> <span data-ttu-id="b2987-151">Scegliere una memoria di dimensioni sufficienti per inserirvi il set di dati e un numero di vCPU più elevato per ridurre la quantità di tempo necessaria per il training.</span><span class="sxs-lookup"><span data-stu-id="b2987-151">Choose a memory size large enough to fit your dataset in-memory and a higher vCPU count in order to decrease the amount of time that training takes.</span></span>

### <a name="security"></a><span data-ttu-id="b2987-152">Security</span><span class="sxs-lookup"><span data-stu-id="b2987-152">Security</span></span>

<span data-ttu-id="b2987-153">Questo scenario può usare Azure Active Directory per autenticare gli utenti per l'[accesso a DSVM][dsvm-id], che contiene codice, modelli e dati in memoria.</span><span class="sxs-lookup"><span data-stu-id="b2987-153">This scenario can use Azure Active Directory to authenticate users for [access to the DSVM][dsvm-id], which contains your code, models, and (in-memory) data.</span></span> <span data-ttu-id="b2987-154">I dati vengono memorizzati in Archiviazione di Azure prima di essere caricati in una soluzione DSVM, dove vengono crittografati automaticamente con la [crittografia del servizio di archiviazione][storage-security].</span><span class="sxs-lookup"><span data-stu-id="b2987-154">Data is stored in Azure Storage prior to being loaded on a DSVM, where it is automatically encrypted using [Storage Service Encryption][storage-security].</span></span> <span data-ttu-id="b2987-155">Le autorizzazioni possono essere gestite tramite autenticazione di Azure Active Directory o con il controllo degli accessi in base al ruolo.</span><span class="sxs-lookup"><span data-stu-id="b2987-155">Permissions can be managed via Azure Active Directory authentication or role-based access control.</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="b2987-156">Distribuire lo scenario</span><span class="sxs-lookup"><span data-stu-id="b2987-156">Deploy this scenario</span></span>

<span data-ttu-id="b2987-157">**Prerequisiti**: È necessario un account Azure esistente.</span><span class="sxs-lookup"><span data-stu-id="b2987-157">**Prerequisites**: You must have an existing Azure account.</span></span> <span data-ttu-id="b2987-158">Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free] prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="b2987-158">If you don't have an Azure subscription, create a [free account][free] before you begin.</span></span>

<span data-ttu-id="b2987-159">Tutto il codice di questo scenario è disponibile nel [repository Microsoft Recommenders][github].</span><span class="sxs-lookup"><span data-stu-id="b2987-159">All the code for this scenario is available in the [Microsoft Recommenders repository][github].</span></span>

<span data-ttu-id="b2987-160">Seguire questi passaggi per eseguire il [notebook di avvio rapido ALS][notebook]:</span><span class="sxs-lookup"><span data-stu-id="b2987-160">Follow these steps to run the [ALS quickstart notebook][notebook]:</span></span>

1. <span data-ttu-id="b2987-161">[Creare una soluzione DSVM][dsvm-ubuntu] nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="b2987-161">[Create a DSVM][dsvm-ubuntu] from the Azure portal.</span></span>

2. <span data-ttu-id="b2987-162">Clonare il repository nella cartella Notebook:</span><span class="sxs-lookup"><span data-stu-id="b2987-162">Clone the repo in the Notebooks folder:</span></span>

    ```shell
    cd notebooks
    git clone https://github.com/Microsoft/Recommenders
    ```

3. <span data-ttu-id="b2987-163">Installare le dipendenze Conda seguendo i passaggi descritti nel file [SETUP.md][setup].</span><span class="sxs-lookup"><span data-stu-id="b2987-163">Install the conda dependencies following the steps described in the [SETUP.md][setup] file.</span></span>

4. <span data-ttu-id="b2987-164">Nel browser accedere alla VM jupyterlab e passare a `notebooks/00_quick_start/als_pyspark_movielens.ipynb`.</span><span class="sxs-lookup"><span data-stu-id="b2987-164">In a browser, go to your jupyterlab VM and navigate to `notebooks/00_quick_start/als_pyspark_movielens.ipynb`.</span></span>

5. <span data-ttu-id="b2987-165">Eseguire il notebook.</span><span class="sxs-lookup"><span data-stu-id="b2987-165">Execute the notebook.</span></span>

## <a name="related-resources"></a><span data-ttu-id="b2987-166">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="b2987-166">Related resources</span></span>

<span data-ttu-id="b2987-167">Per una guida approfondita sulla creazione e la scalabilità di un servizio di generazione di consigli, vedere [Creare un'API per raccomandazioni in tempo reale in Azure][ref-arch].</span><span class="sxs-lookup"><span data-stu-id="b2987-167">For an in-depth guide to building and scaling a recommender service, see [Build a real-time recommendation API on Azure][ref-arch].</span></span> <span data-ttu-id="b2987-168">Per trovare esercitazioni ed esempi dei sistemi consigliati, visitare il [repository Microsoft Recommenders][github].</span><span class="sxs-lookup"><span data-stu-id="b2987-168">For tutorials and examples of recommendation systems, see [Microsoft Recommenders repository][github].</span></span>

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
