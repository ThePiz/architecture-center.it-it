---
title: Configurare Azure Databricks per l'invio delle metriche a Monitoraggio di Azure
description: Una libreria scala per abilitare il monitoraggio delle metriche e la registrazione dei dati in Azure Log Analitica
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: f2fc1fd19da661b74ddf032dd1d5153ce575345c
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59639887"
---
<!-- markdownlint-disable MD040 -->

# <a name="configure-azure-databricks-to-send-metrics-to-azure-monitor"></a><span data-ttu-id="ca894-103">Configurare Azure Databricks per l'invio delle metriche a Monitoraggio di Azure</span><span class="sxs-lookup"><span data-stu-id="ca894-103">Configure Azure Databricks to send metrics to Azure Monitor</span></span>

<span data-ttu-id="ca894-104">Questo articolo illustra come configurare un cluster Azure Databricks per inviare le metriche a un [dell'area di lavoro di Log Analitica](/azure/azure-monitor/platform/manage-access).</span><span class="sxs-lookup"><span data-stu-id="ca894-104">This article shows how to configure an Azure Databricks cluster to send metrics to a [Log Analytics workspace](/azure/azure-monitor/platform/manage-access).</span></span> <span data-ttu-id="ca894-105">Usa il [libreria di monitoraggio di Azure Databricks](https://github.com/mspnp/spark-monitoring), disponibile su GitHub.</span><span class="sxs-lookup"><span data-stu-id="ca894-105">It uses the [Azure Databricks Monitoring Library](https://github.com/mspnp/spark-monitoring), which is available on GitHub.</span></span> <span data-ttu-id="ca894-106">Conoscenza di Java, Scala e Maven sono consigliate come prerequisiti.</span><span class="sxs-lookup"><span data-stu-id="ca894-106">Understanding of Java, Scala, and Maven are recommended as prerequisites.</span></span>

## <a name="about-the-azure-databricks-monitoring-library"></a><span data-ttu-id="ca894-107">Sulla libreria di monitoraggio di Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="ca894-107">About the Azure Databricks Monitoring Library</span></span>

<span data-ttu-id="ca894-108">Il [repository GitHub](https://github.com/mspnp/spark-monitoring) per la libreria di monitoraggio di Azure Databricks ha seguente struttura di directory:</span><span class="sxs-lookup"><span data-stu-id="ca894-108">The [GitHub repo](https://github.com/mspnp/spark-monitoring) for the Azure Databricks Monitoring Library has following directory structure:</span></span>

```
/src  
    /spark-jobs  
    /spark-listeners-loganalytics  
    /spark-listeners  
    /pom.xml  
```

<span data-ttu-id="ca894-109">Il **processi di spark** directory è un'applicazione Spark di esempio che illustra come implementare un contatore delle metriche dell'applicazione Spark.</span><span class="sxs-lookup"><span data-stu-id="ca894-109">The **spark-jobs** directory is a sample Spark application demonstrating how to implement a Spark application metric counter.</span></span>

<span data-ttu-id="ca894-110">Il **spark-listener** directory include funzionalità che consente a Azure Databricks inviare gli eventi di Apache Spark a livello di servizio a un'area di lavoro di Analitica di Log di Azure.</span><span class="sxs-lookup"><span data-stu-id="ca894-110">The **spark-listeners** directory includes functionality that enables Azure Databricks to send Apache Spark events at the service level to an Azure Log Analytics workspace.</span></span> <span data-ttu-id="ca894-111">Azure Databricks è un servizio basato su Apache Spark, che include un set di API strutturate per l'elaborazione dati mediante SQL, Dataframe e set di dati di batch.</span><span class="sxs-lookup"><span data-stu-id="ca894-111">Azure Databricks is a service based on Apache Spark, which includes a set of structured APIs for batch processing data using Datasets, DataFrames, and SQL.</span></span> <span data-ttu-id="ca894-112">Con Apache Spark 2.0, è stato aggiunto il supporto per [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html), un flusso di dati, l'elaborazione API basata su API per l'elaborazione batch di Spark.</span><span class="sxs-lookup"><span data-stu-id="ca894-112">With Apache Spark 2.0, support was added for [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html), a data stream processing API built on Spark's batch processing APIs.</span></span>

<span data-ttu-id="ca894-113">Il **spark-listener di log Analytics** directory include un sink per i listener Spark, un sink per DropWizard e un client per un'area di lavoro di Azure Log Analitica.</span><span class="sxs-lookup"><span data-stu-id="ca894-113">The **spark-listeners-loganalytics** directory includes a sink for Spark listeners, a sink for DropWizard, and a client for an Azure Log Analytics Workspace.</span></span> <span data-ttu-id="ca894-114">Questa directory include anche un Appender log4j per i log applicazioni di Apache Spark.</span><span class="sxs-lookup"><span data-stu-id="ca894-114">This directory also includes a log4j Appender for your Apache Spark application logs.</span></span>

<span data-ttu-id="ca894-115">Il **spark-listener di log Analytics** e **spark-listener** directory contengono il codice per la creazione di due file JAR distribuiti nel cluster Databricks.</span><span class="sxs-lookup"><span data-stu-id="ca894-115">The **spark-listeners-loganalytics** and **spark-listeners** directories contain the code for building the two JAR files that are deployed to the Databricks cluster.</span></span> <span data-ttu-id="ca894-116">Il **spark-listener** directory include un **script** directory che contiene uno script di inizializzazione nodo del cluster per copiare il file JAR di file da una directory di gestione temporanea nel file system di Azure Databricks per nodi di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ca894-116">The **spark-listeners** directory includes a **scripts** directory that contains a cluster node initialization script to copy the JAR files from a staging directory in the Azure Databricks file system to execution nodes.</span></span>

<span data-ttu-id="ca894-117">Il **POM. XML** è il file di compilazione Maven principale per l'intero progetto.</span><span class="sxs-lookup"><span data-stu-id="ca894-117">The **pom.xml** file is the main Maven build file for the entire project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ca894-118">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="ca894-118">Prerequisites</span></span>

<span data-ttu-id="ca894-119">Per iniziare, distribuire le risorse di Azure seguenti:</span><span class="sxs-lookup"><span data-stu-id="ca894-119">To get started, deploy the following Azure resources:</span></span>

- <span data-ttu-id="ca894-120">Un'area di lavoro Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="ca894-120">An Azure Databricks workspace.</span></span> <span data-ttu-id="ca894-121">Vedere [Avvio rapido: Eseguire un processo Spark in Azure Databricks tramite il portale di Azure](/azure/azure-databricks/quickstart-create-databricks-workspace-portal).</span><span class="sxs-lookup"><span data-stu-id="ca894-121">See [Quickstart: Run a Spark job on Azure Databricks using the Azure portal](/azure/azure-databricks/quickstart-create-databricks-workspace-portal).</span></span>
- <span data-ttu-id="ca894-122">Un'area di lavoro Log Analytics.</span><span class="sxs-lookup"><span data-stu-id="ca894-122">A Log Analytics workspace.</span></span> <span data-ttu-id="ca894-123">Visualizzare [creare un'area di lavoro di Log Analitica nel portale di Azure](/azure/azure-monitor/learn/quick-create-workspace).</span><span class="sxs-lookup"><span data-stu-id="ca894-123">See [Create a Log Analytics workspace in the Azure portal](/azure/azure-monitor/learn/quick-create-workspace).</span></span>

<span data-ttu-id="ca894-124">Successivamente, installare il [interfaccia della riga di comando di Azure Databricks](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#install-the-cli).</span><span class="sxs-lookup"><span data-stu-id="ca894-124">Next, install the [Azure Databricks CLI](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#install-the-cli).</span></span> <span data-ttu-id="ca894-125">Per utilizzare l'interfaccia della riga di comando è necessario un token di accesso personale di Azure Databricks.</span><span class="sxs-lookup"><span data-stu-id="ca894-125">An Azure Databricks personal access token is required to use the CLI.</span></span> <span data-ttu-id="ca894-126">Per istruzioni, vedere [generare un token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).</span><span class="sxs-lookup"><span data-stu-id="ca894-126">For instructions, see [Generate a token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).</span></span>

<span data-ttu-id="ca894-127">Trovare l'ID area di lavoro di Log Analitica e la chiave.</span><span class="sxs-lookup"><span data-stu-id="ca894-127">Find your Log Analytics workspace ID and key.</span></span> <span data-ttu-id="ca894-128">Per istruzioni, vedere [ottenere ID area di lavoro e la chiave](/azure/azure-monitor/platform/agent-windows#obtain-workspace-id-and-key).</span><span class="sxs-lookup"><span data-stu-id="ca894-128">For instructions, see [Obtain workspace ID and key](/azure/azure-monitor/platform/agent-windows#obtain-workspace-id-and-key).</span></span> <span data-ttu-id="ca894-129">Questi valori è necessario quando si configura il cluster Databricks.</span><span class="sxs-lookup"><span data-stu-id="ca894-129">You will need these values when you configure the Databricks cluster.</span></span>

<span data-ttu-id="ca894-130">Per compilare la libreria di monitoraggio, è necessario un ambiente IDE Java con le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="ca894-130">To build the monitoring library, you will need a Java IDE with the following resources:</span></span>

- [<span data-ttu-id="ca894-131">Java Development Kit (JDK) version 1.8</span><span class="sxs-lookup"><span data-stu-id="ca894-131">Java Development Kit (JDK) version 1.8</span></span>](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
- [<span data-ttu-id="ca894-132">Linguaggio scala 2.11 SDK</span><span class="sxs-lookup"><span data-stu-id="ca894-132">Scala language SDK 2.11</span></span>](https://www.scala-lang.org/download/)
- [<span data-ttu-id="ca894-133">Apache Maven 3.5.4</span><span class="sxs-lookup"><span data-stu-id="ca894-133">Apache Maven 3.5.4</span></span>](http://maven.apache.org/download.cgi)

## <a name="build-the-azure-databricks-monitoring-library"></a><span data-ttu-id="ca894-134">Compilare una libreria di monitoraggio di Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="ca894-134">Build the Azure Databricks Monitoring Library</span></span>

<span data-ttu-id="ca894-135">Per compilare la libreria di monitoraggio di Azure Databricks, procedere come segue:</span><span class="sxs-lookup"><span data-stu-id="ca894-135">To build the Azure Databricks Monitoring Library, perform the following steps:</span></span>

1. <span data-ttu-id="ca894-136">Clonare, creare il fork o scaricare il [repository GitHub](https://github.com/mspnp/spark-monitoring).</span><span class="sxs-lookup"><span data-stu-id="ca894-136">Clone, fork, or download the [GitHub repository](https://github.com/mspnp/spark-monitoring).</span></span>

1. <span data-ttu-id="ca894-137">Importare il file di modello oggetto progetto di Maven, _POM. XML_, all'interno di **/src** cartella nel progetto.</span><span class="sxs-lookup"><span data-stu-id="ca894-137">Import the Maven project object model file, _pom.xml_, located in the **/src** folder into your project.</span></span> <span data-ttu-id="ca894-138">Verranno importate tre progetti:</span><span class="sxs-lookup"><span data-stu-id="ca894-138">This will import three projects:</span></span>

    - <span data-ttu-id="ca894-139">processi Spark</span><span class="sxs-lookup"><span data-stu-id="ca894-139">spark-jobs</span></span>
    - <span data-ttu-id="ca894-140">spark-listeners</span><span class="sxs-lookup"><span data-stu-id="ca894-140">spark-listeners</span></span>
    - <span data-ttu-id="ca894-141">spark-listeners-loganalytics</span><span class="sxs-lookup"><span data-stu-id="ca894-141">spark-listeners-loganalytics</span></span>

1. <span data-ttu-id="ca894-142">Eseguire Maven **pacchetto** fase di compilazione nell'IDE di Java per creare i file con estensione JAR per ognuno di questi progetti:</span><span class="sxs-lookup"><span data-stu-id="ca894-142">Execute the Maven **package** build phase in your Java IDE to build the JAR files for each of these projects:</span></span>

    |<span data-ttu-id="ca894-143">Project</span><span class="sxs-lookup"><span data-stu-id="ca894-143">Project</span></span>| <span data-ttu-id="ca894-144">File JAR</span><span class="sxs-lookup"><span data-stu-id="ca894-144">JAR file</span></span>|
    |-------|---------|
    |<span data-ttu-id="ca894-145">processi Spark</span><span class="sxs-lookup"><span data-stu-id="ca894-145">spark-jobs</span></span>|<span data-ttu-id="ca894-146">spark-jobs-1.0-SNAPSHOT.jar</span><span class="sxs-lookup"><span data-stu-id="ca894-146">spark-jobs-1.0-SNAPSHOT.jar</span></span>|
    |<span data-ttu-id="ca894-147">spark-listeners</span><span class="sxs-lookup"><span data-stu-id="ca894-147">spark-listeners</span></span>|<span data-ttu-id="ca894-148">spark-listeners-1.0-SNAPSHOT.jar</span><span class="sxs-lookup"><span data-stu-id="ca894-148">spark-listeners-1.0-SNAPSHOT.jar</span></span>|
    |<span data-ttu-id="ca894-149">spark-listeners-loganalytics</span><span class="sxs-lookup"><span data-stu-id="ca894-149">spark-listeners-loganalytics</span></span>|<span data-ttu-id="ca894-150">spark-listeners-loganalytics-1.0-SNAPSHOT.jar</span><span class="sxs-lookup"><span data-stu-id="ca894-150">spark-listeners-loganalytics-1.0-SNAPSHOT.jar</span></span>|

1. <span data-ttu-id="ca894-151">Usare il comando di Azure Databricks per creare una directory denominata **dbfs: databricks/monitoraggio-gestionetemporanea/**:</span><span class="sxs-lookup"><span data-stu-id="ca894-151">Use the Azure Databricks CLI to create a directory named **dbfs:/databricks/monitoring-staging**:</span></span>  

    ```bash
    dbfs mkdirs dbfs:/databricks/monitoring-staging
    ```

1. <span data-ttu-id="ca894-152">Usare il comando di Azure Databricks per copiare **/src/spark-listeners/scripts/listeners.sh** in precedenza nella directory:</span><span class="sxs-lookup"><span data-stu-id="ca894-152">Use the Azure Databricks CLI to copy **/src/spark-listeners/scripts/listeners.sh** to the directory previously:</span></span>

    ```bash
    dbfs cp ./src/spark-listeners/scripts/listeners.sh dbfs:/databricks/monitoring-staging/listeners.sh
    ```

1. <span data-ttu-id="ca894-153">Usare il comando di Azure Databricks per copiare **/src/spark-listeners/scripts/metrics.properties** alla directory creata in precedenza:</span><span class="sxs-lookup"><span data-stu-id="ca894-153">Use the Azure Databricks CLI to copy **/src/spark-listeners/scripts/metrics.properties** to the directory created previously:</span></span>

    ```bash
    dbfs cp <local path to metrics.properties> dbfs:/databricks/monitoring-staging/metrics.properties
    ```

1. <span data-ttu-id="ca894-154">Usare il comando di Azure Databricks per copiare **spark-listener-1.0-snapshot. jar** e **spark-listener di lettura log Analytics-1.0-snapshot. jar** alla directory creata in precedenza:</span><span class="sxs-lookup"><span data-stu-id="ca894-154">Use the Azure Databricks CLI to copy **spark-listeners-1.0-SNAPSHOT.jar** and **spark-listeners-loganalytics-1.0-SNAPSHOT.jar** to the directory created previously:</span></span>

    ```bash
    dbfs cp ./src/spark-listeners/target/spark-listeners-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-1.0-SNAPSHOT.jar
    dbfs cp ./src/spark-listeners-loganalytics/target/spark-listeners-loganalytics-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-loganalytics-1.0-SNAPSHOT.jar
    ```

## <a name="create-and-configure-an-azure-databricks-cluster"></a><span data-ttu-id="ca894-155">Creare e configurare un cluster Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="ca894-155">Create and configure an Azure Databricks cluster</span></span>

<span data-ttu-id="ca894-156">Per creare e configurare un cluster Azure Databricks, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="ca894-156">To create and configure an Azure Databricks cluster, follow these steps:</span></span>

1. <span data-ttu-id="ca894-157">Passare all'area di lavoro Azure Databricks nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="ca894-157">Navigate to your Azure Databricks workspace in the Azure portal.</span></span>
1. <span data-ttu-id="ca894-158">Nella home page, fare clic su **nuovo Cluster**.</span><span class="sxs-lookup"><span data-stu-id="ca894-158">On the home page, click **New Cluster**.</span></span>
1. <span data-ttu-id="ca894-159">Immettere un nome per il cluster nel **nome Cluster** casella di testo.</span><span class="sxs-lookup"><span data-stu-id="ca894-159">Enter a name for your cluster in the **Cluster Name** text box.</span></span>
1. <span data-ttu-id="ca894-160">Nel **versione di Runtime di Databricks** elenco a discesa, seleziona **4.3** o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="ca894-160">In the **Databricks Runtime Version** dropdown, select **4.3** or greater.</span></span>
1. <span data-ttu-id="ca894-161">Sotto **opzioni avanzate**, fare clic sulla **Spark** scheda. Immettere le seguenti coppie nome-valore nel **configurazione di Spark** casella di testo:</span><span class="sxs-lookup"><span data-stu-id="ca894-161">Under **Advanced Options**, click on the **Spark** tab. Enter the following name-value pairs in the **Spark Config** text box:</span></span>

    ```
    spark.extraListeners com.databricks.backend.daemon.driver.DBCEventLoggingListener,org.apache.spark.listeners.UnifiedSparkListener
    spark.unifiedListener.sink org.apache.spark.listeners.sink.loganalytics.LogAnalyticsListenerSink
    spark.unifiedListener.logBlockUpdates false
    ```

1. <span data-ttu-id="ca894-162">Mentre comunque sotto il **Spark** , immettere i valori seguenti nel **variabili di ambiente** casella di testo:</span><span class="sxs-lookup"><span data-stu-id="ca894-162">While still under the **Spark** tab, enter the following values in the **Environment Variables** text box:</span></span>

    ```
    LOG_ANALYTICS_WORKSPACE_ID=[your Azure Log Analytics workspace ID]
    LOG_ANALYTICS_WORKSPACE_KEY=[your Azure Log Analytics workspace key]
    ```

    ![Screenshot dell'interfaccia utente di Databricks](./_images/create-cluster1.png)

1. <span data-ttu-id="ca894-164">Mentre comunque sotto il **opzioni avanzate** sezione, fare clic sul **script Init** scheda.</span><span class="sxs-lookup"><span data-stu-id="ca894-164">While still under the **Advanced Options** section, click on the **Init Scripts** tab.</span></span>
1. <span data-ttu-id="ca894-165">Nel **destinazione** elenco a discesa, seleziona **DBFS**.</span><span class="sxs-lookup"><span data-stu-id="ca894-165">In the **Destination** dropdown, select **DBFS**.</span></span>
1. <span data-ttu-id="ca894-166">Sotto **percorso di Script di inizializzazione**, immettere `dbfs:/databricks/monitoring-staging/listeners.sh`.</span><span class="sxs-lookup"><span data-stu-id="ca894-166">Under **Init Script Path**, enter `dbfs:/databricks/monitoring-staging/listeners.sh`.</span></span>
1. <span data-ttu-id="ca894-167">Fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="ca894-167">Click **Add**.</span></span>

    ![Screenshot dell'interfaccia utente di Databricks](./_images/create-cluster2.png)

1. <span data-ttu-id="ca894-169">Fare clic su **crea Cluster** per creare il cluster.</span><span class="sxs-lookup"><span data-stu-id="ca894-169">Click **Create Cluster** to create the cluster.</span></span>

## <a name="view-metrics"></a><span data-ttu-id="ca894-170">Visualizzare le metriche</span><span class="sxs-lookup"><span data-stu-id="ca894-170">View metrics</span></span>

<span data-ttu-id="ca894-171">Dopo aver completato questi passaggi, il cluster Databricks flussi alcuni dati delle metriche relative al cluster a monitoraggio di Azure.</span><span class="sxs-lookup"><span data-stu-id="ca894-171">After you complete these steps, your Databricks cluster streams some metric data about the cluster itself to Azure Monitor.</span></span> <span data-ttu-id="ca894-172">I dati del log sono disponibili nell'area di lavoro di Analitica di Log di Azure sotto il "Active | I log personalizzati | Schema SparkMetric_CL".</span><span class="sxs-lookup"><span data-stu-id="ca894-172">This log data is available in your Azure Log Analytics workspace under the "Active | Custom Logs | SparkMetric_CL" schema.</span></span> <span data-ttu-id="ca894-173">Per altre informazioni sui tipi di metriche che vengono registrati, vedere [le metriche principali](https://metrics.dropwizard.io/4.0.0/manual/core.html) nella documentazione di Dropwizard.</span><span class="sxs-lookup"><span data-stu-id="ca894-173">For more information about the types of metrics that are logged, see [Metrics Core](https://metrics.dropwizard.io/4.0.0/manual/core.html) in the Dropwizard documentation.</span></span> <span data-ttu-id="ca894-174">Il tipo di metrica, ad esempio contatore, contatore o dell'istogramma, viene scritto il campo di tipo.</span><span class="sxs-lookup"><span data-stu-id="ca894-174">The metric type, such as gauge, counter, or histogram, is written to the Type field.</span></span>

<span data-ttu-id="ca894-175">Inoltre, la libreria trasmette eventi a livello di Apache Spark e Spark Structured Streaming le metriche da processi di monitoraggio di Azure.</span><span class="sxs-lookup"><span data-stu-id="ca894-175">In addition, the library streams Apache Spark level events and Spark Structured Streaming metrics from your jobs to Azure Monitor.</span></span> <span data-ttu-id="ca894-176">Non è necessario apportare modifiche al codice dell'applicazione per queste metriche ed eventi.</span><span class="sxs-lookup"><span data-stu-id="ca894-176">You don't need to make any changes to your application code for these events and metrics.</span></span> <span data-ttu-id="ca894-177">I dati dei log di Spark Structured Streaming è disponibile con la "Active | I log personalizzati | Schema SparkListenerEvent_CL".</span><span class="sxs-lookup"><span data-stu-id="ca894-177">Spark Structured Streaming log data is available under the "Active | Custom Logs | SparkListenerEvent_CL" schema.</span></span>

![Schermata di un'area di lavoro di Log Analitica](./_images/workspace.png)

<span data-ttu-id="ca894-179">Per altre informazioni sulla visualizzazione dei log, vedere [visualizzazione e analisi dei dati di log in Monitoraggio di Azure](/azure/azure-monitor/log-query/portals).</span><span class="sxs-lookup"><span data-stu-id="ca894-179">For more information about viewing logs, see [Viewing and analyzing log data in Azure Monitor](/azure/azure-monitor/log-query/portals).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ca894-180">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="ca894-180">Next steps</span></span>

<span data-ttu-id="ca894-181">Questo articolo descrive come configurare il cluster per inviare le metriche a monitoraggio di Azure.</span><span class="sxs-lookup"><span data-stu-id="ca894-181">This article described how to configure your cluster to send metrics to Azure Monitor.</span></span> <span data-ttu-id="ca894-182">L'articolo successivo illustra come usare la libreria di monitoraggio di Azure Databricks per inviare i log e metriche dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ca894-182">The next article shows how to use the Azure Databricks Monitoring Library to send application metrics and logs.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ca894-183">Inviare i log dell'applicazione al monitoraggio di Azure</span><span class="sxs-lookup"><span data-stu-id="ca894-183">Send application logs to Azure Monitor</span></span>](./application-logs.md)
