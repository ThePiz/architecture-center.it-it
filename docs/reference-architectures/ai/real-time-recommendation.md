---
title: Creare un'API per raccomandazioni in tempo reale in Azure
description: Usare l'apprendimento automatico per automatizzare le raccomandazioni usando Azure Databricks e Data Science Virtual Machine (DSVM) di Azure per il training di un modello in Azure.
author: njray
ms.date: 12/12/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 8fa15c82275f18af78d1f42ec1750ab9b28119fb
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487115"
---
# <a name="build-a-real-time-recommendation-api-on-azure"></a><span data-ttu-id="de703-103">Creare un'API per raccomandazioni in tempo reale in Azure</span><span class="sxs-lookup"><span data-stu-id="de703-103">Build a real-time recommendation API on Azure</span></span>

<span data-ttu-id="de703-104">Questa architettura di riferimento illustra come eseguire il training di un modello per raccomandazioni con Azure Databricks e distribuirlo come un'API usando Azure Cosmos DB, Azure Machine Learning e il servizio Azure Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="de703-104">This reference architecture shows how to train a recommendation model using Azure Databricks and deploy it as an API by using Azure Cosmos DB, Azure Machine Learning, and Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="de703-105">Questa architettura può essere generalizzata per la maggior parte degli scenari di motore per la generazione di raccomandazioni, incluse raccomandazioni per prodotti, film e notizie.</span><span class="sxs-lookup"><span data-stu-id="de703-105">This architecture can be generalized for most recommendation engine scenarios, including recommendations for products, movies, and news.</span></span>

<span data-ttu-id="de703-106">Un'implementazione di riferimento per questa architettura è disponibile in [GitHub](https://github.com/Microsoft/Recommenders/blob/master/notebooks/04_operationalize/als_movie_o16n.ipynb).</span><span class="sxs-lookup"><span data-stu-id="de703-106">A reference implementation for this architecture is available on [GitHub](https://github.com/Microsoft/Recommenders/blob/master/notebooks/04_operationalize/als_movie_o16n.ipynb).</span></span>

![Architettura di un modello di Machine Learning per il training di raccomandazioni per film](./_images/recommenders-architecture.png)

<span data-ttu-id="de703-108">**Scenario**: un'organizzazione che opera nel settore multimediale vuole fornire raccomandazioni per video o film ai propri utenti.</span><span class="sxs-lookup"><span data-stu-id="de703-108">**Scenario**: A media organization wants to provide movie or video recommendations to its users.</span></span> <span data-ttu-id="de703-109">Fornendo raccomandazioni personalizzate, l'organizzazione soddisfa diversi obiettivi aziendali, tra cui percentuali di clic maggiori. migliore engagement nel sito e una maggiore soddisfazione degli utenti.</span><span class="sxs-lookup"><span data-stu-id="de703-109">By providing personalized recommendations, the organization meets several business goals, including increased click-through rates, increased engagement on site, and higher user satisfaction.</span></span>

<span data-ttu-id="de703-110">Questa architettura di riferimento è progettata per il training e la distribuzione di un'API per un servizio di raccomandazione in grado raccomandare i 10 film più interessanti per un determinato utente.</span><span class="sxs-lookup"><span data-stu-id="de703-110">This reference architecture is for training and deploying a real-time recommender service API that can provide the top 10 movie recommendations for a given user.</span></span>

<span data-ttu-id="de703-111">Il flusso di dati per questo modello di raccomandazione è il seguente:</span><span class="sxs-lookup"><span data-stu-id="de703-111">The data flow for this recommendation model is as follows:</span></span>

1. <span data-ttu-id="de703-112">Tenere traccia del comportamento degli utenti.</span><span class="sxs-lookup"><span data-stu-id="de703-112">Track user behaviors.</span></span> <span data-ttu-id="de703-113">Ad esempio, un servizio back-end potrebbe registrare quando un utente classifica un film o fa clic su un prodotto o un articolo di notizie.</span><span class="sxs-lookup"><span data-stu-id="de703-113">For example, a backend service might log when a user rates a movie or clicks a product or news article.</span></span>

2. <span data-ttu-id="de703-114">Caricare i dati in Azure Databricks da un'[origine dati][data-source] disponibile.</span><span class="sxs-lookup"><span data-stu-id="de703-114">Load the data into Azure Databricks from an available [data source][data-source].</span></span>

3. <span data-ttu-id="de703-115">Preparare i dati e dividerli in set di training e di testing per il training del modello.</span><span class="sxs-lookup"><span data-stu-id="de703-115">Prepare the data and split it into training and testing sets to train the model.</span></span> <span data-ttu-id="de703-116">([Questa guida][guide] descrive le opzioni per la suddivisione dei dati.)</span><span class="sxs-lookup"><span data-stu-id="de703-116">([This guide][guide] describes options for splitting data.)</span></span>

4. <span data-ttu-id="de703-117">Adattare il modello di [filtraggio collaborativo Spark][als] ai dati.</span><span class="sxs-lookup"><span data-stu-id="de703-117">Fit the [Spark Collaborative Filtering][als] model to the data.</span></span>

5. <span data-ttu-id="de703-118">Valutare la qualità del modello con le metriche di classificazione e ranking.</span><span class="sxs-lookup"><span data-stu-id="de703-118">Evaluate the quality of the model using rating and ranking metrics.</span></span> <span data-ttu-id="de703-119">([Questa guida][eval-guide] include informazioni dettagliate sulle metriche per cui è possibile valutare il sistema di raccomandazione.)</span><span class="sxs-lookup"><span data-stu-id="de703-119">([This guide][eval-guide] provides details about the metrics you can evaluate your recommender on.)</span></span>

6. <span data-ttu-id="de703-120">Precalcolare le prime 10 raccomandazioni per ogni utente e archiviarle come cache in Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="de703-120">Precompute the top 10 recommendations per user and store as a cache in Azure Cosmos DB.</span></span>

7. <span data-ttu-id="de703-121">Distribuire un servizio API nel servizio Azure Kubernetes usando le API di Azure Machine Learning per inserire le API in contenitori e distribuirle.</span><span class="sxs-lookup"><span data-stu-id="de703-121">Deploy an API service to AKS using the Azure Machine Learning APIs to containerize and deploy the API.</span></span>

8. <span data-ttu-id="de703-122">Quando il servizio back-end riceve una richiesta da un utente, chiamare l'API per le raccomandazioni ospitata nel servizio Azure Kubernetes per ottenere le prime 10 raccomandazioni e visualizzarle all'utente.</span><span class="sxs-lookup"><span data-stu-id="de703-122">When the backend service gets a request from a user, call the recommendations API hosted in AKS to get the top 10 recommendations and display them to the user.</span></span>

## <a name="architecture"></a><span data-ttu-id="de703-123">Architettura</span><span class="sxs-lookup"><span data-stu-id="de703-123">Architecture</span></span>

<span data-ttu-id="de703-124">L'architettura è costituita dai componenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="de703-124">This architecture consists of the following components:</span></span>

<span data-ttu-id="de703-125">[Azure Databricks][databricks].</span><span class="sxs-lookup"><span data-stu-id="de703-125">[Azure Databricks][databricks].</span></span> <span data-ttu-id="de703-126">Databricks è un ambiente di sviluppo usato per preparare i dati di input ed eseguire il training del modello del sistema di raccomandazione in un cluster Spark.</span><span class="sxs-lookup"><span data-stu-id="de703-126">Databricks is a development environment used to prepare input data and train the recommender model on a Spark cluster.</span></span> <span data-ttu-id="de703-127">Azure Databricks offre anche un'area di lavoro interattiva per eseguire e collaborare su notebook per qualsiasi attività di elaborazione dei dati o di apprendimento automatico.</span><span class="sxs-lookup"><span data-stu-id="de703-127">Azure Databricks also provides an interactive workspace to run and collaborate on notebooks for any data processing or machine learning tasks.</span></span>

<span data-ttu-id="de703-128">[Servizio Azure Kubernetes][aks].</span><span class="sxs-lookup"><span data-stu-id="de703-128">[Azure Kubernetes Service][aks] (AKS).</span></span> <span data-ttu-id="de703-129">Il servizio Azure Kubernetes viene usato per distribuire e rendere operativa l'API del servizio del modello di Machine Learning in un cluster Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="de703-129">AKS is used to deploy and operationalize a machine learning model service API on a Kubernetes cluster.</span></span> <span data-ttu-id="de703-130">Il servizio Azure Kubernetes ospita il modello nel contenitore, offrendo la scalabilità richiesta per soddisfare i requisiti di velocità effettiva, di gestione delle identità e degli accessi, di registrazione e di monitoraggio dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="de703-130">AKS hosts the containerized model, providing scalability that meets your throughput requirements, identity and access management, and logging and health monitoring.</span></span>

<span data-ttu-id="de703-131">[Azure Cosmos DB][cosmosdb].</span><span class="sxs-lookup"><span data-stu-id="de703-131">[Azure Cosmos DB][cosmosdb].</span></span> <span data-ttu-id="de703-132">Cosmos DB è un servizio di database distribuito a livello globale usato per archiviare i primi 10 film consigliati per ogni utente.</span><span class="sxs-lookup"><span data-stu-id="de703-132">Cosmos DB is a globally distributed database service used to store the top 10 recommended movies for each user.</span></span> <span data-ttu-id="de703-133">Azure Cosmos DB è ideale per questo scenario, perché offre bassa latenza (10 ms al 99° percentile) per la lettura dei primi elementi consigliati per un determinato utente.</span><span class="sxs-lookup"><span data-stu-id="de703-133">Azure Cosmos DB is well-suited for this scenario, because it provides low latency (10 ms at 99th percentile) to read the top recommended items for a given user.</span></span>

<span data-ttu-id="de703-134">[Servizio Azure Machine Learning][mls].</span><span class="sxs-lookup"><span data-stu-id="de703-134">[Azure Machine Learning Service][mls].</span></span> <span data-ttu-id="de703-135">Questo servizio consente di tenere traccia e gestire i modelli di Machine Learning, quindi di creare un pacchetto per questi modelli e distribuirli in un ambiente del servizio Azure Kubernetes scalabile.</span><span class="sxs-lookup"><span data-stu-id="de703-135">This service is used to track and manage machine learning models, and then package and deploy these models to a scalable AKS environment.</span></span>

<span data-ttu-id="de703-136">[Microsoft Recommenders][github].</span><span class="sxs-lookup"><span data-stu-id="de703-136">[Microsoft Recommenders][github].</span></span> <span data-ttu-id="de703-137">Questo repository open source contiene codice di utilità ed esempi per consentire agli utenti di iniziare facilmente a creare, valutare e rendere operativo un sistema di raccomandazione.</span><span class="sxs-lookup"><span data-stu-id="de703-137">This open-source repository contains utility code and samples to help users get started in building, evaluating, and operationalizing a recommender system.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="de703-138">Considerazioni sulle prestazioni</span><span class="sxs-lookup"><span data-stu-id="de703-138">Performance considerations</span></span>

<span data-ttu-id="de703-139">Le prestazioni sono un aspetto fondamentale per le raccomandazioni in tempo reale, in quanto le raccomandazioni in genere rientrano nel percorso critico della richiesta effettuata da un utente nel sito.</span><span class="sxs-lookup"><span data-stu-id="de703-139">Performance is a primary consideration for real-time recommendations, because recommendations usually fall in the critical path of the request a user makes on your site.</span></span>

<span data-ttu-id="de703-140">La combinazione di servizio Azure Kubernetes e Azure Cosmos DB consente a questa architettura di offrire un buon punto di partenza per fornire raccomandazioni per un carico di lavoro di medie dimensioni con un overhead minimo.</span><span class="sxs-lookup"><span data-stu-id="de703-140">The combination of AKS and Azure Cosmos DB enables this architecture to provide a good starting point to provide recommendations for a medium-sized workload with minimal overhead.</span></span> <span data-ttu-id="de703-141">In un test di carico con 200 utenti simultanei, questa architettura offre raccomandazioni con una latenza mediana di circa 60 ms e una velocità effettiva di esecuzione di 180 richieste al secondo.</span><span class="sxs-lookup"><span data-stu-id="de703-141">Under a load test with 200 concurrent users, this architecture provides recommendations at a median latency of about 60 ms and performs at a throughput of 180 requests per second.</span></span> <span data-ttu-id="de703-142">Il testo di carico è stato eseguito sulla configurazione di distribuzione predefinita (cluster del servizio Azure Kubernetes 3x D3 v2 con 12 vCPU, 42 GB di memoria e 11.000 [unità richiesta (UR) al secondo][ru] sottoposto a provisioning per Azure Cosmos DB).</span><span class="sxs-lookup"><span data-stu-id="de703-142">The load test was run against the default deployment configuration (a 3x D3 v2 AKS cluster with 12 vCPUs, 42 GB of memory, and 11,000 [Request Units (RUs) per second][ru] provisioned for Azure Cosmos DB).</span></span>

![Grafico delle prestazioni](./_images/recommenders-performance.png)

![Grafico della velocità effettiva](./_images/recommenders-throughput.png)

<span data-ttu-id="de703-145">Azure Cosmos DB è consigliato per la distribuzione globale e l'utilità chiavi in mano, in grado di soddisfare qualsiasi requisito per il database delle app.</span><span class="sxs-lookup"><span data-stu-id="de703-145">Azure Cosmos DB is recommended for its turnkey global distribution and usefulness in meeting any database requirements your app has.</span></span> <span data-ttu-id="de703-146">Per una [latenza leggermente più veloce][latency], valutare la possibilità di usare [Cache Redis di Azure][redis] invece di Azure Cosmos DB per gestire le ricerche.</span><span class="sxs-lookup"><span data-stu-id="de703-146">For slightly [faster latency][latency], consider using [Azure Redis Cache][redis] instead of Azure Cosmos DB to serve lookups.</span></span> <span data-ttu-id="de703-147">Cache Redis può migliorare le prestazioni dei sistemi che dipendono fortemente dai dati in archivi back-end.</span><span class="sxs-lookup"><span data-stu-id="de703-147">Redis Cache can improve performance of systems that rely highly on data in back-end stores.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="de703-148">Considerazioni sulla scalabilità</span><span class="sxs-lookup"><span data-stu-id="de703-148">Scalability considerations</span></span>

<span data-ttu-id="de703-149">Se non si prevede di usare Spark o in presenza di un carico di lavoro più piccolo per cui non è necessaria la distribuzione, valutare la possibilità di usare [Data Science Virtual Machine][dsvm] (DSVM) invece di Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="de703-149">If you don't plan to use Spark, or you have a smaller workload where you don't need distribution, consider using [Data Science Virtual Machine][dsvm] (DSVM) instead of Azure Databricks.</span></span> <span data-ttu-id="de703-150">DSVM è una macchina virtuale di Azure con framework per Deep Learning e strumenti per apprendimento automatico e data science.</span><span class="sxs-lookup"><span data-stu-id="de703-150">DSVM is an Azure virtual machine with deep learning frameworks and tools for machine learning and data science.</span></span> <span data-ttu-id="de703-151">Come con Azure Databricks, qualsiasi modello creato in una DSVM può essere reso operativo come servizio nel servizio Azure Kubernetes tramite Azure Machine Learning.</span><span class="sxs-lookup"><span data-stu-id="de703-151">As with Azure Databricks, any model you create in a DSVM can be operationalized as a service on AKS via Azure Machine Learning.</span></span>

<span data-ttu-id="de703-152">Durante il training, effettuare il provisioning di un cluster Spark più grande di dimensioni fisse in Azure Databricks o configurare la [scalabilità automatica][autoscaling].</span><span class="sxs-lookup"><span data-stu-id="de703-152">During training, provision a larger fixed-size Spark cluster in Azure Databricks or configure [autoscaling][autoscaling].</span></span> <span data-ttu-id="de703-153">Quando è abilitata la scalabilità automatica, Databricks esegue il monitoraggio del carico nel cluster e aumenta o riduce le risorse quando necessario.</span><span class="sxs-lookup"><span data-stu-id="de703-153">When autoscaling is enabled, Databricks monitors the load on your cluster and scales up and downs when required.</span></span> <span data-ttu-id="de703-154">Effettuare il provisioning o predisporre un cluster più grande in presenza di dati di grandi dimensioni e se si vuole ridurre la quantità di tempo necessaria per le attività di preparazione dei dati o di modellazione.</span><span class="sxs-lookup"><span data-stu-id="de703-154">Provision or scale out a larger cluster if you have a large data size and you want to reduce the amount of time it takes for data preparation or modeling tasks.</span></span>

<span data-ttu-id="de703-155">Ridimensionare il cluster del servizio Azure Kubernetes in modo da soddisfare i requisiti di prestazioni e velocità effettiva.</span><span class="sxs-lookup"><span data-stu-id="de703-155">Scale the AKS cluster to meet your performance and throughput requirements.</span></span> <span data-ttu-id="de703-156">Prestare attenzione a selezionare il numero di [pod][scale] ottimale per sfruttare appieno il cluster e configurare il numero di [nodi][nodes] del cluster adatto a soddisfare la domanda del servizio.</span><span class="sxs-lookup"><span data-stu-id="de703-156">Take care to scale up the number of [pods][scale] to fully utilize the cluster, and to scale the [nodes][nodes] of the cluster to meet the demand of your service.</span></span> <span data-ttu-id="de703-157">Per altre informazioni su come ridimensionare il cluster in modo da soddisfare i requisiti di prestazioni e velocità effettiva del servizio del sistema di raccomandazione, vedere [Scaling Azure Container Service Clusters][blog] (Ridimensionare i cluster del servizio Azure Container).</span><span class="sxs-lookup"><span data-stu-id="de703-157">For more information on how to scale your cluster to meet the performance and throughput requirements of your recommender service, see [Scaling Azure Container Service Clusters][blog].</span></span>

<span data-ttu-id="de703-158">Per gestire le prestazioni di Azure Cosmos DB, stimare il numero di letture necessarie al secondo ed effettuare il provisioning del numero di [unità richiesta al secondo][ru] (velocità effettiva) necessario.</span><span class="sxs-lookup"><span data-stu-id="de703-158">To manage Azure Cosmos DB performance, estimate the number of reads required per second, and provision the number of [RUs per second][ru] (throughput) needed.</span></span> <span data-ttu-id="de703-159">Usare le procedure consigliate per [partizionamento e scalabilità orizzontale][partition-data].</span><span class="sxs-lookup"><span data-stu-id="de703-159">Use best practices for [partitioning and horizontal scaling][partition-data].</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="de703-160">Considerazioni sul costo</span><span class="sxs-lookup"><span data-stu-id="de703-160">Cost considerations</span></span>

<span data-ttu-id="de703-161">I principali elementi che generano costi in questo scenario sono:</span><span class="sxs-lookup"><span data-stu-id="de703-161">The main drivers of cost in this scenario are:</span></span>

- <span data-ttu-id="de703-162">Le dimensioni del cluster di Azure Databricks richieste per il training.</span><span class="sxs-lookup"><span data-stu-id="de703-162">The Azure Databricks cluster size required for training.</span></span>
- <span data-ttu-id="de703-163">Le dimensioni del cluster del servizio Azure Kubernetes richieste per soddisfare i requisiti di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="de703-163">The AKS cluster size required to meet your performance requirements.</span></span>
- <span data-ttu-id="de703-164">Le UR di Azure Cosmos DB di cui viene effettuato il provisioning in modo da soddisfare i requisiti di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="de703-164">Azure Cosmos DB RUs provisioned to meet your performance requirements.</span></span>

<span data-ttu-id="de703-165">Gestire i costi di Azure Databricks con una frequenza minore di ripetizione del training e disattivando il cluster Spark quando non è in uso.</span><span class="sxs-lookup"><span data-stu-id="de703-165">Manage the Azure Databricks costs by retraining less frequently and turning off the Spark cluster when not in use.</span></span> <span data-ttu-id="de703-166">I costi del servizio Azure Kubernetes e di Azure Cosmos DB sono legati alla velocità effettiva e alle prestazioni richieste per il sito e aumenteranno e diminuiranno a seconda del volume di traffico del sito.</span><span class="sxs-lookup"><span data-stu-id="de703-166">The AKS and Azure Cosmos DB costs are tied to the throughput and performance required by your site and will scale up and down depending on the volume of traffic to your site.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="de703-167">Distribuire la soluzione</span><span class="sxs-lookup"><span data-stu-id="de703-167">Deploy the solution</span></span>

<span data-ttu-id="de703-168">Per distribuire questa architettura, creare prima di tutto un ambiente di Azure Databricks per preparare i dati ed eseguire il training di un modello del sistema di raccomandazione:</span><span class="sxs-lookup"><span data-stu-id="de703-168">To deploy this architecture, first create an Azure Databricks environment to prepare data and train a recommender model:</span></span>

1. <span data-ttu-id="de703-169">Creare un'[area di lavoro di Azure Databricks][workspace].</span><span class="sxs-lookup"><span data-stu-id="de703-169">Create an [Azure Databricks workspace][workspace].</span></span>

2. <span data-ttu-id="de703-170">Creare un nuovo cluster in Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="de703-170">Create a new cluster in Azure Databricks.</span></span> <span data-ttu-id="de703-171">È necessaria la configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="de703-171">The following configuration is required:</span></span>

    - <span data-ttu-id="de703-172">Modalità cluster: Standard</span><span class="sxs-lookup"><span data-stu-id="de703-172">Cluster mode: Standard</span></span>
    - <span data-ttu-id="de703-173">Versione del runtime di Databricks: 4.1 (include Apache Spark 2.3.0, Scala 2.11)</span><span class="sxs-lookup"><span data-stu-id="de703-173">Databricks Runtime Version: 4.1 (includes Apache Spark 2.3.0, Scala 2.11)</span></span>
    - <span data-ttu-id="de703-174">Versione di Python: 3</span><span class="sxs-lookup"><span data-stu-id="de703-174">Python Version: 3</span></span>
    - <span data-ttu-id="de703-175">Tipo driver: Standard\_DS3\_v2</span><span class="sxs-lookup"><span data-stu-id="de703-175">Driver Type: Standard\_DS3\_v2</span></span>
    - <span data-ttu-id="de703-176">Tipo di ruolo di lavoro: Standard\_DS3\_v2 (min e max in base alle esigenze)</span><span class="sxs-lookup"><span data-stu-id="de703-176">Worker Type: Standard\_DS3\_v2 (min and max as required)</span></span>
    - <span data-ttu-id="de703-177">Interruzione automatica: (in base alle esigenze)</span><span class="sxs-lookup"><span data-stu-id="de703-177">Auto Termination: (as required)</span></span>
    - <span data-ttu-id="de703-178">Configurazione di Spark: (in base alle esigenze)</span><span class="sxs-lookup"><span data-stu-id="de703-178">Spark Config: (as required)</span></span>
    - <span data-ttu-id="de703-179">Variabili di ambiente: (in base alle esigenze)</span><span class="sxs-lookup"><span data-stu-id="de703-179">Environment Variables: (as required)</span></span>

3. <span data-ttu-id="de703-180">Clonare il repository [Microsoft Recommenders][github] nel computer locale.</span><span class="sxs-lookup"><span data-stu-id="de703-180">Clone the [Microsoft Recommenders][github] repository on your local computer.</span></span>

4. <span data-ttu-id="de703-181">Comprimere il contenuto all'interno della cartella Recommenders:</span><span class="sxs-lookup"><span data-stu-id="de703-181">Zip the content inside the Recommenders folder:</span></span>

    ```console
    cd Recommenders
    zip -r Recommenders.zip
    ```

5. <span data-ttu-id="de703-182">Associare la libreria Recommenders al cluster come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="de703-182">Attach the Recommenders library to your cluster as follows:</span></span>

    1. <span data-ttu-id="de703-183">Nel menu successivo, usare l'opzione per importare una libreria ("To import a library, such as a jar or egg, click here" - Per importare una libreria, come un file jag o egg, fare clic qui) e premere **click here** (fare clic qui).</span><span class="sxs-lookup"><span data-stu-id="de703-183">In the next menu, use the option to import a library ("To import a library, such as a jar or egg, click here") and press **click here**.</span></span>

    2. <span data-ttu-id="de703-184">Nel primo menu a discesa selezionare l'opzione **Upload Python egg or PyPI** (Carica egg o PyPI Python).</span><span class="sxs-lookup"><span data-stu-id="de703-184">At the first drop-down menu, select the **Upload Python egg or PyPI** option.</span></span>

    3. <span data-ttu-id="de703-185">Selezionare **Drop library egg here to upload** (Trascinare qui il file egg della libreria per caricarlo) e selezionare il file Recommenders.zip appena creato.</span><span class="sxs-lookup"><span data-stu-id="de703-185">Select **Drop library egg here to upload** and select the Recommenders.zip file you just created.</span></span>

    4. <span data-ttu-id="de703-186">Selezionare **Create library** (Crea libreria) per caricare il file ZIP e renderlo disponibile nell'area di lavoro.</span><span class="sxs-lookup"><span data-stu-id="de703-186">Select **Create library** to upload the .zip file and make it available in your workspace.</span></span>

    5. <span data-ttu-id="de703-187">Nel menu successivo associare la libreria al cluster.</span><span class="sxs-lookup"><span data-stu-id="de703-187">In the next menu, attach the library to your cluster.</span></span>

6. <span data-ttu-id="de703-188">Nell'area di lavoro importare l'[esempio ALS Movie Operationalization][als-example].</span><span class="sxs-lookup"><span data-stu-id="de703-188">In your workspace, import the [ALS Movie Operationalization example][als-example].</span></span>

7. <span data-ttu-id="de703-189">Eseguire il notebook ALS Movie Operationalization per creare le risorse necessarie per creare un'API per le raccomandazioni che fornisce le raccomandazioni per i primi 10 film per un determinato utente.</span><span class="sxs-lookup"><span data-stu-id="de703-189">Run the ALS Movie Operationalization notebook to create the resources required to create a recommendation API that provides the top-10 movie recommendations for a given user.</span></span>

<!-- links -->
[aci]: /azure/container-instances/container-instances-overview
[aad]: /azure/active-directory-b2c/active-directory-b2c-overview
[aks]: /azure/aks/intro-kubernetes
[als]: https://spark.apache.org/docs/latest/ml-collaborative-filtering.html
[als-example]: https://github.com/Microsoft/Recommenders/blob/master/notebooks/04_operationalize/als_movie_o16n.ipynb
[autoscaling]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html
[autoscale]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html#autoscaling
[availability]: /azure/architecture/checklist/availability
[blob]: /azure/storage/blobs/storage-blobs-introduction
[blog]: https://blogs.technet.microsoft.com/machinelearning/2018/03/20/scaling-azure-container-service-cluster/
[clusters]: https://docs.azuredatabricks.net/user-guide/clusters/configure.html
[cosmosdb]: /azure/cosmos-db/introduction
[data-source]: https://docs.azuredatabricks.net/spark/latest/data-sources/index.html
[databricks]: /azure/azure-databricks/what-is-azure-databricks
[dsvm]: /azure/machine-learning/data-science-virtual-machine/overview
[dsvm-ubuntu]: /azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro
[eval-guide]: https://github.com/Microsoft/Recommenders/blob/master/notebooks/03_evaluate/evaluation.ipynb
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[github]: https://github.com/Microsoft/Recommenders
[guide]: https://github.com/Microsoft/Recommenders/blob/master/notebooks/01_prepare_data/data_split.ipynb
[latency]: https://github.com/jessebenson/azure-performance
[mls]: /azure/machine-learning/service/
[n-tier]: /azure/architecture/reference-architectures/n-tier/n-tier-cassandra
[ndcg]: https://en.wikipedia.org/wiki/Discounted_cumulative_gain
[nodes]: /azure/aks/scale-cluster
[notebook]: https://github.com/Microsoft/Recommenders/notebooks/00_quick_start/als_pyspark_movielens.ipynb
[partition-data]: /azure/cosmos-db/partition-data
[redis]: /azure/redis-cache/cache-overview
[regions]: https://azure.microsoft.com/en-us/global-infrastructure/services/?products=virtual-machines&regions=all
[resiliency]: /azure/architecture/resiliency/
[ru]: /azure/cosmos-db/request-units
[sec-docs]: /azure/security/
[setup]: https://github.com/Microsoft/Recommenders/blob/master/SETUP.md%60
[scale]: /azure/aks/tutorial-kubernetes-scale
[sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/
[vm-size]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
[workspace]: https://docs.azuredatabricks.net/getting-started/index.html
