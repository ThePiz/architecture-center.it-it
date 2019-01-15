---
title: Scelta di una tecnologia di apprendimento automatico
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: e4b8ecb64afbacc8a4e24e9c6274455db0451d62
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54112126"
---
# <a name="choosing-a-machine-learning-technology-in-azure"></a><span data-ttu-id="dadee-102">Scelta di una tecnologia di apprendimento automatico in Azure</span><span class="sxs-lookup"><span data-stu-id="dadee-102">Choosing a machine learning technology in Azure</span></span>

<span data-ttu-id="dadee-103">Il data science e l'apprendimento automatico costituiscono un carico di lavoro generalmente eseguito dai data scientist.</span><span class="sxs-lookup"><span data-stu-id="dadee-103">Data science and machine learning is a workload that is usually undertaken by data scientists.</span></span> <span data-ttu-id="dadee-104">Richiedono strumenti specializzati, molti dei quali appositamente progettati per il tipo di esplorazione interattiva dei dati e per le attività di modellazione che devono essere eseguite dai data scientist.</span><span class="sxs-lookup"><span data-stu-id="dadee-104">It requires specialist tools, many of which are designed specifically for the type of interactive data exploration and modeling tasks that a data scientist must perform.</span></span>

<span data-ttu-id="dadee-105">Le soluzioni di apprendimento automatico vengono compilate in modo iterativo e sono articolate in due fasi distinte:</span><span class="sxs-lookup"><span data-stu-id="dadee-105">Machine learning solutions are built iteratively, and have two distinct phases:</span></span>

- <span data-ttu-id="dadee-106">Preparazione e modellazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="dadee-106">Data preparation and modeling.</span></span>
- <span data-ttu-id="dadee-107">Distribuzione e utilizzo di servizi predittivi.</span><span class="sxs-lookup"><span data-stu-id="dadee-107">Deployment and consumption of predictive services.</span></span>

## <a name="tools-and-services-for-data-preparation-and-modeling"></a><span data-ttu-id="dadee-108">Strumenti e servizi per la preparazione e la modellazione dei dati</span><span class="sxs-lookup"><span data-stu-id="dadee-108">Tools and services for data preparation and modeling</span></span>

<span data-ttu-id="dadee-109">I data scientist preferiscono in genere eseguire operazioni sui dati usando codice personalizzato scritto in Python o R. Questo codice viene solitamente eseguito in modo interattivo e usato dai data scientist per eseguire query ed esplorare i dati, generando in questo modo visualizzazioni e statistiche utili a determinare le relazioni con essi.</span><span class="sxs-lookup"><span data-stu-id="dadee-109">Data scientists typically prefer to work with data using custom code written in Python or R. This code is generally run interactively, with the data scientists using it to query and explore the data, generating visualizations and statistics to help determine the relationships with it.</span></span> <span data-ttu-id="dadee-110">Esistono molti ambienti interattivi per R e Python che possono essere usati dai data scientist.</span><span class="sxs-lookup"><span data-stu-id="dadee-110">There are many interactive environments for R and Python that data scientists can use.</span></span> <span data-ttu-id="dadee-111">L'ambiente preferito è costituito dai **notebook di Jupyter**, che offrono una shell basata su browser con cui i data scientist possono creare file di *notebook* contenenti codice R o Phyton e testo di markdown.</span><span class="sxs-lookup"><span data-stu-id="dadee-111">A particular favorite is **Jupyter Notebooks** that provides a browser-based shell that enables data scientists to create *notebook* files that contain R or Python code and markdown text.</span></span> <span data-ttu-id="dadee-112">Questo ambiente consente quindi di collaborare mediante la condivisione di codice e risultati in un unico documento.</span><span class="sxs-lookup"><span data-stu-id="dadee-112">This is an effective way to collaborate by sharing and documenting code and results in a single document.</span></span>

<span data-ttu-id="dadee-113">Altri strumenti comunemente usati includono:</span><span class="sxs-lookup"><span data-stu-id="dadee-113">Other commonly used tools include:</span></span>

- <span data-ttu-id="dadee-114">**Spyder**: ambiente di sviluppo interattivo (IDE) per Python incluso con la distribuzione Anaconda Python.</span><span class="sxs-lookup"><span data-stu-id="dadee-114">**Spyder**: The interactive development environment (IDE) for Python provided with the Anaconda Python distribution.</span></span>
- <span data-ttu-id="dadee-115">**R Studio**: IDE per il linguaggio di programmazione R.</span><span class="sxs-lookup"><span data-stu-id="dadee-115">**R Studio**: An IDE for the R programming language.</span></span>
- <span data-ttu-id="dadee-116">**Visual Studio Code**: ambiente di creazione di codice multipiattaforma e leggero che supporta Python e i framework comunemente usati per lo sviluppo per apprendimento automatico e intelligenza artificiale.</span><span class="sxs-lookup"><span data-stu-id="dadee-116">**Visual Studio Code**: A lightweight, cross-platform coding environment that supports Python as well as commonly used frameworks for machine learning and AI development.</span></span>

<span data-ttu-id="dadee-117">Oltre a questi strumenti, i data scientist possono usare anche i servizi di Azure per semplificare la gestione del codice e dei modelli.</span><span class="sxs-lookup"><span data-stu-id="dadee-117">In addition to these tools, data scientists can leverage Azure services to simplify code and model management.</span></span>

### <a name="azure-notebooks"></a><span data-ttu-id="dadee-118">Notebook di Azure</span><span class="sxs-lookup"><span data-stu-id="dadee-118">Azure Notebooks</span></span>

<span data-ttu-id="dadee-119">Azure Notebooks è un servizio online basato su notebook di Jupyter che consente ai data scientist di creare, eseguire e condividere notebook di Jupyter in librerie basate sul cloud.</span><span class="sxs-lookup"><span data-stu-id="dadee-119">Azure Notebooks is an online Jupyter Notebooks service that enables data scientists to create, run, and share Jupyter Notebooks in cloud-based libraries.</span></span>

<span data-ttu-id="dadee-120">Vantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="dadee-120">Key benefits:</span></span>

- <span data-ttu-id="dadee-121">Il servizio è gratuito: non è necessaria una sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="dadee-121">Free service&mdash;no Azure subscription required.</span></span>
- <span data-ttu-id="dadee-122">Non è necessario installare in locale Jupyter né le distribuzioni R o Python di supporto: è sufficiente usare un browser.</span><span class="sxs-lookup"><span data-stu-id="dadee-122">No need to install Jupyter and the supporting R or Python distributions locally&mdash;just use a browser.</span></span>
- <span data-ttu-id="dadee-123">È possibile gestire le librerie online e accedervi da qualsiasi dispositivo.</span><span class="sxs-lookup"><span data-stu-id="dadee-123">Manage your own online libraries and access them from any device.</span></span>
- <span data-ttu-id="dadee-124">È possibile condividere i notebook con i collaboratori.</span><span class="sxs-lookup"><span data-stu-id="dadee-124">Share your notebooks with collaborators.</span></span>

<span data-ttu-id="dadee-125">Considerazioni:</span><span class="sxs-lookup"><span data-stu-id="dadee-125">Considerations:</span></span>

- <span data-ttu-id="dadee-126">Non è possibile accedere ai notebook in modalità offline.</span><span class="sxs-lookup"><span data-stu-id="dadee-126">You will be unable to access your notebooks when offline.</span></span>
- <span data-ttu-id="dadee-127">Le funzionalità di elaborazione limitate del servizio di notebook gratuito possono non essere sufficienti per eseguire il training di modelli complessi o di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="dadee-127">Limited processing capabilities of the free notebook service may not be enough to train large or complex models.</span></span>

### <a name="data-science-virtual-machine"></a><span data-ttu-id="dadee-128">Macchina virtuale di data science</span><span class="sxs-lookup"><span data-stu-id="dadee-128">Data science virtual machine</span></span>

<span data-ttu-id="dadee-129">La macchina virtuale di data science è un'immagine di macchina virtuale di Azure che include gli strumenti e i framework comunemente usati dai data scientist, tra cui R, Python, notebook di Jupyter, Visual Studio Code e librerie per modellazione dell'apprendimento automatico, come Microsoft Cognitive Toolkit.</span><span class="sxs-lookup"><span data-stu-id="dadee-129">The data science virtual machine is an Azure virtual machine image that includes the tools and frameworks commonly used by data scientists, including R, Python, Jupyter Notebooks, Visual Studio Code, and libraries for machine learning modeling such as the Microsoft Cognitive Toolkit.</span></span> <span data-ttu-id="dadee-130">Questi strumenti possono richiedere una procedura di installazione particolarmente lunga e complessa e contengono molte interdipendenze che spesso determinano problemi di gestione delle versioni.</span><span class="sxs-lookup"><span data-stu-id="dadee-130">These tools can be complex and time consuming to install, and contain many interdependencies that often lead to version management issues.</span></span> <span data-ttu-id="dadee-131">La disponibilità di un'immagine preinstallata può ridurre il tempo impiegato dai data scientist per risolvere i problemi relativi all'ambiente, consentendo loro di concentrarsi sulle attività di esplorazione e modellazione dei dati che devono eseguire.</span><span class="sxs-lookup"><span data-stu-id="dadee-131">Having a preinstalled image can reduce the time data scientists spend troubleshooting environment issues, allowing them to focus on the data exploration and modeling tasks they need to perform.</span></span>

<span data-ttu-id="dadee-132">Vantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="dadee-132">Key benefits:</span></span>

- <span data-ttu-id="dadee-133">Riduzione del tempo necessario per installare, gestire e risolvere i problemi relativi ai framework e agli strumenti di data science.</span><span class="sxs-lookup"><span data-stu-id="dadee-133">Reduced time to install, manage, and troubleshoot data science tools and frameworks.</span></span>
- <span data-ttu-id="dadee-134">Sono incluse le versioni più recenti di tutti i framework e gli strumenti comuni.</span><span class="sxs-lookup"><span data-stu-id="dadee-134">The latest versions of all commonly used tools and frameworks are included.</span></span>
- <span data-ttu-id="dadee-135">Le opzioni della macchina virtuale includono immagini altamente scalabili con funzionalità GPU per la modellazione di grandi quantità di dati.</span><span class="sxs-lookup"><span data-stu-id="dadee-135">Virtual machine options include highly scalable images with GPU capabilities for intensive data modeling.</span></span>

<span data-ttu-id="dadee-136">Considerazioni:</span><span class="sxs-lookup"><span data-stu-id="dadee-136">Considerations:</span></span>

- <span data-ttu-id="dadee-137">La macchina virtuale non è accessibile in modalità offline.</span><span class="sxs-lookup"><span data-stu-id="dadee-137">The virtual machine cannot be accessed when offline.</span></span>
- <span data-ttu-id="dadee-138">L'esecuzione di una macchina virtuale comporta costi di Azure ed è quindi necessario fare attenzione ad eseguirla solo quando è effettivamente necessario.</span><span class="sxs-lookup"><span data-stu-id="dadee-138">Running a virtual machine incurs Azure charges, so you must be careful to have it running only when required.</span></span>

### <a name="azure-machine-learning"></a><span data-ttu-id="dadee-139">Azure Machine Learning</span><span class="sxs-lookup"><span data-stu-id="dadee-139">Azure Machine Learning</span></span>

<span data-ttu-id="dadee-140">Azure Machine Learning è un servizio basato sul cloud per la gestione di modelli ed esperimenti di apprendimento automatico.</span><span class="sxs-lookup"><span data-stu-id="dadee-140">Azure Machine Learning is a cloud-based service for managing machine learning experiments and models.</span></span> <span data-ttu-id="dadee-141">Include un servizio di sperimentazione che tiene traccia degli script di training per la preparazione e la modellazione dei dati, oltre a mantenere la cronologia di tutte le esecuzioni per poter confrontare le prestazioni del modello nelle varie iterazioni.</span><span class="sxs-lookup"><span data-stu-id="dadee-141">It includes an experimentation service that tracks data preparation and modeling training scripts, maintaining a history of all executions so you can compare model performance across iterations.</span></span> <span data-ttu-id="dadee-142">I data scientist possono creare script nello strumento preferito, ad esempio Jupyter Notebook o Visual Studio Code, e quindi eseguire la distribuzione in diverse [risorse di calcolo](/azure/machine-learning/service/how-to-set-up-training-targets) in Azure.</span><span class="sxs-lookup"><span data-stu-id="dadee-142">Data scientists can create scripts in their tool of choice, such as Jupyter Notebooks or Visual Studio Code, and then deploy to a variety of different [compute resources](/azure/machine-learning/service/how-to-set-up-training-targets) in Azure.</span></span>

<span data-ttu-id="dadee-143">I modelli possono essere distribuiti come servizio Web in un contenitore Docker, Spark in Azure HDinsight, Microsoft Machine Learning Server o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="dadee-143">Models can be deployed as a web service to a Docker container, Spark on Azure HDinsight, Microsoft Machine Learning Server, or SQL Server.</span></span> <span data-ttu-id="dadee-144">Il servizio Gestione modelli di Machine Learning consente quindi di tenere traccia e gestire le distribuzioni di modelli nel cloud, in dispositivi perimetrali o a livello aziendale.</span><span class="sxs-lookup"><span data-stu-id="dadee-144">The Azure Machine Learning Model Management service then enables you to track and manage model deployments in the cloud, on edge devices, or across the enterprise.</span></span>

<span data-ttu-id="dadee-145">Vantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="dadee-145">Key benefits:</span></span>

- <span data-ttu-id="dadee-146">Gestione centrale degli script e della cronologia di esecuzione, semplificando il confronto tra più versioni dei modelli.</span><span class="sxs-lookup"><span data-stu-id="dadee-146">Central management of scripts and run history, making it easy to compare model versions.</span></span>
- <span data-ttu-id="dadee-147">Trasformazione interattiva dei dati tramite un editor visivo.</span><span class="sxs-lookup"><span data-stu-id="dadee-147">Interactive data transformation through a visual editor.</span></span>
- <span data-ttu-id="dadee-148">Semplicità di distribuzione e gestione dei modelli nel cloud o in dispositivi perimetrali.</span><span class="sxs-lookup"><span data-stu-id="dadee-148">Easy deployment and management of models to the cloud or edge devices.</span></span>

<span data-ttu-id="dadee-149">Considerazioni:</span><span class="sxs-lookup"><span data-stu-id="dadee-149">Considerations:</span></span>

- <span data-ttu-id="dadee-150">È necessaria una certa conoscenza del modello di gestione dei modelli.</span><span class="sxs-lookup"><span data-stu-id="dadee-150">Requires some familiarity with the model management model.</span></span>

### <a name="azure-batch-ai"></a><span data-ttu-id="dadee-151">Azure Batch per intelligenza artificiale</span><span class="sxs-lookup"><span data-stu-id="dadee-151">Azure Batch AI</span></span>

<span data-ttu-id="dadee-152">Azure Batch per intelligenza artificiale consente di eseguire esperimenti di apprendimento automatico in parallelo e di eseguire il training dei modelli su larga scala in un cluster di macchine virtuali con GPU.</span><span class="sxs-lookup"><span data-stu-id="dadee-152">Azure Batch AI enables you to run your machine learning experiments in parallel, and perform model training at scale across a cluster of virtual machines with GPUs.</span></span> <span data-ttu-id="dadee-153">Batch per intelligenza artificiale consente anche di aumentare le prestazioni dei processi di apprendimento avanzato in GPU in cluster tramite framework come Cognitive Toolkit, Caffe, Chainer, e TensorFlow.</span><span class="sxs-lookup"><span data-stu-id="dadee-153">Batch AI training enables you to scale out deep learning jobs across clustered GPUs, using frameworks such as Cognitive Toolkit, Caffe, Chainer, and TensorFlow.</span></span>

<span data-ttu-id="dadee-154">Gestione modelli di Azure Machine Learning consente di accettare modelli dal training di Batch per intelligenza artificiale per distribuirli, gestirli e monitorarli.</span><span class="sxs-lookup"><span data-stu-id="dadee-154">Azure Machine Learning Model Management can be used to take models from Batch AI training to deploy, manage, and monitor them.</span></span>

### <a name="azure-machine-learning-studio"></a><span data-ttu-id="dadee-155">Azure Machine Learning Studio</span><span class="sxs-lookup"><span data-stu-id="dadee-155">Azure Machine Learning Studio</span></span>

<span data-ttu-id="dadee-156">Azure Machine Learning Studio è un ambiente di sviluppo visivo basato sul cloud per la creazione di esperimenti di dati, il training di modelli di apprendimento automatico e la relativa pubblicazione come servizi Web in Azure.</span><span class="sxs-lookup"><span data-stu-id="dadee-156">Azure Machine Learning Studio is a cloud-based, visual development environment for creating data experiments, training machine learning models, and publishing them as web services in Azure.</span></span> <span data-ttu-id="dadee-157">L'interfaccia visiva con trascinamento della selezione consente ai data scientist e agli utenti esperti di creare rapidamente soluzioni di apprendimento automatico, supportando al tempo stesso la logica personalizzata di R e Python e un'ampia gamma di algoritmi statistici predefiniti e tecniche per attività di modellazione dell'apprendimento automatico, oltre al supporto incorporato per notebook di Jupyter.</span><span class="sxs-lookup"><span data-stu-id="dadee-157">Its visual drag-and-drop interface lets data scientists and power users create machine learning solutions quickly, while supporting custom R and Python logic, a wide range of established statistical algorithms and techniques for machine learning modeling tasks, and built-in support for Jupyter Notebooks.</span></span>

<span data-ttu-id="dadee-158">Vantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="dadee-158">Key benefits:</span></span>

- <span data-ttu-id="dadee-159">L'interfaccia visiva interattiva consente la modellazione dell'apprendimento automatico con una quantità minima di codice.</span><span class="sxs-lookup"><span data-stu-id="dadee-159">Interactive visual interface enables machine learning modeling with minimal code.</span></span>
- <span data-ttu-id="dadee-160">Sono integrati i notebook di Jupyter per l'esplorazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="dadee-160">Built-in Jupyter Notebooks for data exploration.</span></span>
- <span data-ttu-id="dadee-161">Distribuzione diretta di modelli sottoposti a training come servizi Web di Azure.</span><span class="sxs-lookup"><span data-stu-id="dadee-161">Direct deployment of trained models as Azure web services.</span></span>

<span data-ttu-id="dadee-162">Considerazioni:</span><span class="sxs-lookup"><span data-stu-id="dadee-162">Considerations:</span></span>

- <span data-ttu-id="dadee-163">Scalabilità limitata.</span><span class="sxs-lookup"><span data-stu-id="dadee-163">Limited scalability.</span></span> <span data-ttu-id="dadee-164">La dimensione massima di un set di dati di training è di 10 GB.</span><span class="sxs-lookup"><span data-stu-id="dadee-164">The maximum size of a training dataset is 10 GB.</span></span>
- <span data-ttu-id="dadee-165">Solo online.</span><span class="sxs-lookup"><span data-stu-id="dadee-165">Online only.</span></span> <span data-ttu-id="dadee-166">Nessun ambiente di sviluppo offline.</span><span class="sxs-lookup"><span data-stu-id="dadee-166">No offline development environment.</span></span>

## <a name="tools-and-services-for-deploying-machine-learning-models"></a><span data-ttu-id="dadee-167">Strumenti e servizi per la distribuzione di modelli di apprendimento automatico</span><span class="sxs-lookup"><span data-stu-id="dadee-167">Tools and services for deploying machine learning models</span></span>

<span data-ttu-id="dadee-168">Dopo che un data scientist ha creato un modello di apprendimento automatico, è necessario in genere distribuirlo e usarlo all'interno di applicazioni o in altri flussi di dati.</span><span class="sxs-lookup"><span data-stu-id="dadee-168">After a data scientist has created a machine learning model, you will typically need to deploy it and consume it from applications or in other data flows.</span></span> <span data-ttu-id="dadee-169">I modelli di apprendimento automatico hanno molte potenziali destinazioni di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="dadee-169">There are a number of potential deployment targets for machine learning models.</span></span>

### <a name="spark-on-azure-hdinsight"></a><span data-ttu-id="dadee-170">Spark in Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="dadee-170">Spark on Azure HDInsight</span></span>

<span data-ttu-id="dadee-171">Apache Spark include Spark MLlib, composto da un framework e da una libreria di modelli di apprendimento automatico.</span><span class="sxs-lookup"><span data-stu-id="dadee-171">Apache Spark includes Spark MLlib, a framework and library for machine learning models.</span></span> <span data-ttu-id="dadee-172">La libreria Microsoft Machine Learning for Spark (MMLSpark) offre anche il supporto per algoritmi di apprendimento automatico per modelli predittivi in Spark.</span><span class="sxs-lookup"><span data-stu-id="dadee-172">The Microsoft Machine Learning library for Spark (MMLSpark) also provides deep learning algorithm support for predictive models in Spark.</span></span>

<span data-ttu-id="dadee-173">Vantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="dadee-173">Key benefits:</span></span>

- <span data-ttu-id="dadee-174">Spark è una piattaforma distribuita che offre alta scalabilità per processi di apprendimento automatico con volumi elevati.</span><span class="sxs-lookup"><span data-stu-id="dadee-174">Spark is a distributed platform that offers high scalability for high-volume machine learning processes.</span></span>
- <span data-ttu-id="dadee-175">È possibile distribuire i modelli direttamente in Spark in HDinsight e gestirli con il servizio Gestione modelli di Azure Machine Learning.</span><span class="sxs-lookup"><span data-stu-id="dadee-175">You can deploy models directly to Spark in HDinsight and manage them using the Azure Machine Learning Model Management service.</span></span>

<span data-ttu-id="dadee-176">Considerazioni:</span><span class="sxs-lookup"><span data-stu-id="dadee-176">Considerations:</span></span>

- <span data-ttu-id="dadee-177">Spark viene eseguito in un cluster HDinsght che comporta addebiti per tutto il tempo in cui è in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="dadee-177">Spark runs in an HDinsght cluster that incurs charges the whole time it is running.</span></span> <span data-ttu-id="dadee-178">Se il servizio di apprendimento automatico viene usato solo in modo occasionale, può quindi determinare costi non necessari.</span><span class="sxs-lookup"><span data-stu-id="dadee-178">If the machine learning service will only be used occasionally, this may result in unnecessary costs.</span></span>

### <a name="azure-databricks"></a><span data-ttu-id="dadee-179">Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="dadee-179">Azure Databricks</span></span>

<span data-ttu-id="dadee-180">[Azure Databricks](/azure/azure-databricks/) è una piattaforma di analisi basata su Apache Spark.</span><span class="sxs-lookup"><span data-stu-id="dadee-180">[Azure Databricks](/azure/azure-databricks/) is an Apache Spark-based analytics platform.</span></span> <span data-ttu-id="dadee-181">Questa opzione può essere considerata una sorta di "Spark distribuito come servizio".</span><span class="sxs-lookup"><span data-stu-id="dadee-181">You can think of it as "Spark as a service."</span></span> <span data-ttu-id="dadee-182">È il modo più semplice per usare Spark nella piattaforma Azure.</span><span class="sxs-lookup"><span data-stu-id="dadee-182">It's the easiest way to use Spark on the Azure platform.</span></span> <span data-ttu-id="dadee-183">Per l'apprendimento automatico, è possibile usare [MLFlow](https://www.mlflow.org/), [Databricks Runtime ML](https://docs.azuredatabricks.net/user-guide/clusters/mlruntime.html), Apache Spark MLlib e altri strumenti.</span><span class="sxs-lookup"><span data-stu-id="dadee-183">For machine learning, you can use [MLFlow](https://www.mlflow.org/), [Databricks Runtime ML](https://docs.azuredatabricks.net/user-guide/clusters/mlruntime.html), Apache Spark MLlib, and others.</span></span> <span data-ttu-id="dadee-184">Per altre informazioni, vedere la documentazione sull'[apprendimento automatico in Azure Databricks](https://docs.azuredatabricks.net/spark/latest/mllib/index.html).</span><span class="sxs-lookup"><span data-stu-id="dadee-184">For more information, see [Azure Databricks: Machine Learning](https://docs.azuredatabricks.net/spark/latest/mllib/index.html).</span></span>

### <a name="web-service-in-a-container"></a><span data-ttu-id="dadee-185">Servizio Web in un contenitore</span><span class="sxs-lookup"><span data-stu-id="dadee-185">Web service in a container</span></span>

<span data-ttu-id="dadee-186">È possibile distribuire un modello di apprendimento automatico come servizio Web Python in un contenitore Docker.</span><span class="sxs-lookup"><span data-stu-id="dadee-186">You can deploy a machine learning model as a Python web service in a Docker container.</span></span> <span data-ttu-id="dadee-187">In particolare, è possibile distribuire il modello in Azure o in un dispositivo periferico, dove può essere usato in locale con i dati con cui opera.</span><span class="sxs-lookup"><span data-stu-id="dadee-187">You can deploy the model to Azure or to an edge device, where it can be used locally with the data on which it operates.</span></span>

<span data-ttu-id="dadee-188">Vantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="dadee-188">Key Benefits:</span></span>

- <span data-ttu-id="dadee-189">I contenitori offrono uno strumento semplice ed economicamente conveniente per creare pacchetti di servizi e distribuirli.</span><span class="sxs-lookup"><span data-stu-id="dadee-189">Containers are a lightweight and generally cost effective way to package and deploy services.</span></span>
- <span data-ttu-id="dadee-190">La possibilità di distribuire in un dispositivo periferico consente di spostare la logica predittiva più vicina ai dati.</span><span class="sxs-lookup"><span data-stu-id="dadee-190">The ability to deploy to an edge device enables you to move your predictive logic closer to the data.</span></span>

<span data-ttu-id="dadee-191">Considerazioni:</span><span class="sxs-lookup"><span data-stu-id="dadee-191">Considerations:</span></span>

- <span data-ttu-id="dadee-192">Questo modello di distribuzione è basato su contenitori Docker ed è quindi necessario avere familiarità con questa tecnologia prima di distribuire un servizio Web in questo modo.</span><span class="sxs-lookup"><span data-stu-id="dadee-192">This deployment model is based on Docker containers, so you should be familiar with this technology before deploying a web service this way.</span></span>

### <a name="microsoft-machine-learning-server"></a><span data-ttu-id="dadee-193">Microsoft Machine Learning Server</span><span class="sxs-lookup"><span data-stu-id="dadee-193">Microsoft Machine Learning Server</span></span>

<span data-ttu-id="dadee-194">Machine Learning Server (in precedenza Microsoft R Server) è una piattaforma scalabile per codice R e Python, appositamente progettata per scenari di apprendimento automatico.</span><span class="sxs-lookup"><span data-stu-id="dadee-194">Machine Learning Server (formerly Microsoft R Server) is a scalable platform for R and Python code, specifically designed for machine learning scenarios.</span></span>

<span data-ttu-id="dadee-195">Vantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="dadee-195">Key benefits:</span></span>

- <span data-ttu-id="dadee-196">Scalabilità elevata.</span><span class="sxs-lookup"><span data-stu-id="dadee-196">High scalability.</span></span>

<span data-ttu-id="dadee-197">Considerazioni:</span><span class="sxs-lookup"><span data-stu-id="dadee-197">Considerations:</span></span>

- <span data-ttu-id="dadee-198">È necessario distribuire e gestire Machine Learning Server a livello aziendale.</span><span class="sxs-lookup"><span data-stu-id="dadee-198">You need to deploy and manage Machine Learning Server in your enterprise.</span></span>

### <a name="microsoft-sql-server"></a><span data-ttu-id="dadee-199">Microsoft SQL Server</span><span class="sxs-lookup"><span data-stu-id="dadee-199">Microsoft SQL Server</span></span>

<span data-ttu-id="dadee-200">Microsoft SQL Server supporta R e Python in modo nativo, offrendo la possibilità di incapsulare i modelli di apprendimento automatico compilati in queste lingue come funzioni Transact-SQL in un database.</span><span class="sxs-lookup"><span data-stu-id="dadee-200">Microsoft SQL Server supports R and Python natively, enabling you to encapsulate machine learning models built in these languages as Transact-SQL functions in a database.</span></span>

<span data-ttu-id="dadee-201">Vantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="dadee-201">Key benefits:</span></span>

- <span data-ttu-id="dadee-202">Possibilità di incapsulare la logica predittiva in una funzione di database, con conseguente possibilità di includere facilmente anche la logica a livello dati.</span><span class="sxs-lookup"><span data-stu-id="dadee-202">Encapsulate predictive logic in a database function, making it easy to include in data-tier logic.</span></span>

<span data-ttu-id="dadee-203">Considerazioni:</span><span class="sxs-lookup"><span data-stu-id="dadee-203">Considerations:</span></span>

- <span data-ttu-id="dadee-204">Si presuppone la presenza di un database di SQL Server come livello dati per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="dadee-204">Assumes a SQL Server database as the data tier for your application.</span></span>

### <a name="azure-machine-learning-web-service"></a><span data-ttu-id="dadee-205">Servizio Web Azure Machine Learning</span><span class="sxs-lookup"><span data-stu-id="dadee-205">Azure Machine Learning web service</span></span>

<span data-ttu-id="dadee-206">Quando si crea un modello di apprendimento automatico tramite Azure Machine Learning Studio, è possibile distribuirlo come servizio Web,</span><span class="sxs-lookup"><span data-stu-id="dadee-206">When you create a machine learning model using Azure Machine Learning Studio, you can deploy it as a web service.</span></span> <span data-ttu-id="dadee-207">che può essere quindi usato tramite un'interfaccia REST da qualsiasi applicazione client in grado di comunicare tramite HTTP.</span><span class="sxs-lookup"><span data-stu-id="dadee-207">This can then be consumed through a REST interface from any client applications capable of communicating by HTTP.</span></span>

<span data-ttu-id="dadee-208">Vantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="dadee-208">Key benefits:</span></span>

- <span data-ttu-id="dadee-209">Semplicità di sviluppo e distribuzione.</span><span class="sxs-lookup"><span data-stu-id="dadee-209">Ease of development and deployment.</span></span>
- <span data-ttu-id="dadee-210">Portale di gestione dei servizi Web con metriche di monitoraggio di base.</span><span class="sxs-lookup"><span data-stu-id="dadee-210">Web service management portal with basic monitoring metrics.</span></span>
- <span data-ttu-id="dadee-211">Supporto incorporato per chiamare servizi Web di Azure Machine Learning da Azure Data Lake Analytics, Azure Data Factory e Analisi di flusso di Azure.</span><span class="sxs-lookup"><span data-stu-id="dadee-211">Built-in support for calling Azure Machine Learning web services from Azure Data Lake Analytics, Azure Data Factory, and Azure Stream Analytics.</span></span>

<span data-ttu-id="dadee-212">Considerazioni:</span><span class="sxs-lookup"><span data-stu-id="dadee-212">Considerations:</span></span>

- <span data-ttu-id="dadee-213">È disponibile solo per modelli compilati con Azure Machine Learning Studio.</span><span class="sxs-lookup"><span data-stu-id="dadee-213">Only available for models built using Azure Machine Learning Studio.</span></span>
- <span data-ttu-id="dadee-214">I modelli sottoposti a training con accesso solo basato sul Web non possono essere eseguiti in locale o in modalità offline.</span><span class="sxs-lookup"><span data-stu-id="dadee-214">Web-based access only, trained models cannot run on-premises or offline.</span></span>
