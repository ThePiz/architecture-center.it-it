---
title: Inviare i log dell'applicazione di Azure Databricks per monitoraggio di Azure
description: Come inviare i log personalizzati e metriche da Azure Databricks per monitoraggio di Azure
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: 49c631687fb3e3bbd807ffbbb49d9c5f6526bfb4
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503402"
---
# <a name="send-azure-databricks-application-logs-to-azure-monitor"></a><span data-ttu-id="081d9-103">Inviare i log dell'applicazione di Azure Databricks per monitoraggio di Azure</span><span class="sxs-lookup"><span data-stu-id="081d9-103">Send Azure Databricks application logs to Azure Monitor</span></span>

<span data-ttu-id="081d9-104">Questo articolo illustra come inviare i log dell'applicazione e le metriche da Azure Databricks per un [dell'area di lavoro di Log Analitica](/azure/azure-monitor/platform/manage-access).</span><span class="sxs-lookup"><span data-stu-id="081d9-104">This article shows how to send application logs and metrics from Azure Databricks to a [Log Analytics workspace](/azure/azure-monitor/platform/manage-access).</span></span> <span data-ttu-id="081d9-105">Usa il [libreria di monitoraggio di Azure Databricks](https://github.com/mspnp/spark-monitoring), disponibile su GitHub.</span><span class="sxs-lookup"><span data-stu-id="081d9-105">It uses the [Azure Databricks Monitoring Library](https://github.com/mspnp/spark-monitoring), which is available on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="081d9-106">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="081d9-106">Prerequisites</span></span>

<span data-ttu-id="081d9-107">Configurare il cluster Azure Databricks per usare la libreria di monitoraggio, come descritto in [configurare Azure Databricks per inviare le metriche a monitoraggio di Azure][config-cluster].</span><span class="sxs-lookup"><span data-stu-id="081d9-107">Configure your Azure Databricks cluster to use the monitoring library, as described in [Configure Azure Databricks to send metrics to Azure Monitor][config-cluster].</span></span>

> [!NOTE]
> <span data-ttu-id="081d9-108">La libreria di monitoraggio trasmette eventi a livello di Apache Spark e Spark Structured Streaming le metriche da processi di monitoraggio di Azure.</span><span class="sxs-lookup"><span data-stu-id="081d9-108">The monitoring library streams Apache Spark level events and Spark Structured Streaming metrics from your jobs to Azure Monitor.</span></span> <span data-ttu-id="081d9-109">Non è necessario apportare modifiche al codice dell'applicazione per queste metriche ed eventi.</span><span class="sxs-lookup"><span data-stu-id="081d9-109">You don't need to make any changes to your application code for these events and metrics.</span></span>

## <a name="send-application-metrics-using-dropwizard"></a><span data-ttu-id="081d9-110">Inviare le metriche dell'applicazione usando Dropwizard</span><span class="sxs-lookup"><span data-stu-id="081d9-110">Send application metrics using Dropwizard</span></span>

<span data-ttu-id="081d9-111">Spark Usa un sistema di metriche configurabile basato sulla libreria Dropwizard metriche.</span><span class="sxs-lookup"><span data-stu-id="081d9-111">Spark uses a configurable metrics system based on the Dropwizard Metrics Library.</span></span> <span data-ttu-id="081d9-112">Per altre informazioni, vedere [metriche](https://spark.apache.org/docs/latest/monitoring.html#metrics) nella documentazione di Spark.</span><span class="sxs-lookup"><span data-stu-id="081d9-112">For more information, see [Metrics](https://spark.apache.org/docs/latest/monitoring.html#metrics) in the Spark documentation.</span></span>

<span data-ttu-id="081d9-113">Per inviare le metriche dell'applicazione dal codice dell'applicazione di Azure Databricks per monitoraggio di Azure, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="081d9-113">To send application metrics from Azure Databricks application code to Azure Monitor, follow these steps:</span></span>

1. <span data-ttu-id="081d9-114">Compilare il **spark-listener di lettura log Analytics-1.0-snapshot. jar** file con estensione JAR come descritto in [compilare la libreria di monitoraggio di Azure Databricks][build-lib].</span><span class="sxs-lookup"><span data-stu-id="081d9-114">Build the **spark-listeners-loganalytics-1.0-SNAPSHOT.jar** JAR file as described in [Build the Azure Databricks Monitoring Library][build-lib].</span></span>

1. <span data-ttu-id="081d9-115">Creare Dropwizard [misuratori o contatori](https://metrics.dropwizard.io/4.0.0/manual/core.html) nel codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="081d9-115">Create Dropwizard [gauges or counters](https://metrics.dropwizard.io/4.0.0/manual/core.html) in your application code.</span></span> <span data-ttu-id="081d9-116">È possibile usare il `UserMetricsSystem` classe definita nella libreria di monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="081d9-116">You can use the `UserMetricsSystem` class defined in the monitoring library.</span></span> <span data-ttu-id="081d9-117">L'esempio seguente crea un contatore denominato `counter1`.</span><span class="sxs-lookup"><span data-stu-id="081d9-117">The following example creates a counter named `counter1`.</span></span>

    ```Scala
    import org.apache.spark.metrics.UserMetricsSystems
    import org.apache.spark.sql.SparkSession

    object StreamingQueryListenerSampleJob  {

      private final val METRICS_NAMESPACE = "samplejob"
      private final val COUNTER_NAME = "counter1"

      def main(args: Array[String]): Unit = {

        val spark = SparkSession
          .builder
          .getOrCreate

        val driverMetricsSystem = UserMetricsSystems
            .getMetricSystem(METRICS_NAMESPACE, builder => {
              builder.registerCounter(COUNTER_NAME)
            })

        driverMetricsSystem.counter(COUNTER_NAME).inc(5)
      }
    }
    ```

    <span data-ttu-id="081d9-118">La libreria di monitoraggio include un [applicazione di esempio] [ sample-app] che illustra come usare il `UserMetricsSystem` classe.</span><span class="sxs-lookup"><span data-stu-id="081d9-118">The monitoring library includes a [sample application][sample-app] that demonstrates how to use the `UserMetricsSystem` class.</span></span>

## <a name="send-application-logs-using-log4j"></a><span data-ttu-id="081d9-119">Inviare i log dell'applicazione tramite Log4j</span><span class="sxs-lookup"><span data-stu-id="081d9-119">Send application logs using Log4j</span></span>

<span data-ttu-id="081d9-120">Per inviare i log dell'applicazione di Azure Databricks Analitica di Log di Azure usando il [appender Log4j](https://logging.apache.org/log4j/2.x/manual/appenders.html) nella raccolta, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="081d9-120">To send your Azure Databricks application logs to Azure Log Analytics using the [Log4j appender](https://logging.apache.org/log4j/2.x/manual/appenders.html) in the library, follow these steps:</span></span>

1. <span data-ttu-id="081d9-121">Compilare il **spark-listener di lettura log Analytics-1.0-snapshot. jar** file con estensione JAR come descritto in [compilare la libreria di monitoraggio di Azure Databricks][build-lib].</span><span class="sxs-lookup"><span data-stu-id="081d9-121">Build the **spark-listeners-loganalytics-1.0-SNAPSHOT.jar** JAR file as described in [Build the Azure Databricks Monitoring Library][build-lib].</span></span>

1. <span data-ttu-id="081d9-122">Creare un **log4j.properties** [file di configurazione](https://logging.apache.org/log4j/2.x/manual/configuration.html) per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="081d9-122">Create a **log4j.properties** [configuration file](https://logging.apache.org/log4j/2.x/manual/configuration.html) for your application.</span></span> <span data-ttu-id="081d9-123">Includere le seguenti proprietà di configurazione.</span><span class="sxs-lookup"><span data-stu-id="081d9-123">Include the following configuration properties.</span></span> <span data-ttu-id="081d9-124">Sostituire il nome del pacchetto dell'applicazione e log di livello dove indicato:</span><span class="sxs-lookup"><span data-stu-id="081d9-124">Substitute your application package name and log level where indicated:</span></span>

    ```YAML
    log4j.appender.A1=com.microsoft.pnp.logging.loganalytics.LogAnalyticsAppender
    log4j.appender.A1.layout=com.microsoft.pnp.logging.JSONLayout
    log4j.appender.A1.layout.LocationInfo=false
    log4j.additivity.<your application package name>=false
    log4j.logger.<your application package name>=<log level>, A1
    ```

    <span data-ttu-id="081d9-125">È possibile trovare un file di configurazione di esempio [Ecco][log4j.properties].</span><span class="sxs-lookup"><span data-stu-id="081d9-125">You can find a sample configuration file [here][log4j.properties].</span></span>

1. <span data-ttu-id="081d9-126">Nel codice dell'applicazione, includere il **spark-listener di log Analytics** del progetto e importare `com.microsoft.pnp.logging.Log4jconfiguration` al codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="081d9-126">In your application code, include the **spark-listeners-loganalytics** project, and import `com.microsoft.pnp.logging.Log4jconfiguration` to your application code.</span></span>

    ```Scala
    import com.microsoft.pnp.logging.Log4jConfiguration
    ```

1. <span data-ttu-id="081d9-127">Configurare Log4j usando il **log4j.properties** file creato nel passaggio 3:</span><span class="sxs-lookup"><span data-stu-id="081d9-127">Configure Log4j using the **log4j.properties** file you created in step 3:</span></span>

    ```Scala
    getClass.getResourceAsStream("<path to file in your JAR file>/log4j.properties")) {
          stream => {
            Log4jConfiguration.configure(stream)
          }
    }
    ```

1. <span data-ttu-id="081d9-128">Aggiungere i messaggi di log di Apache Spark al livello appropriato nel codice in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="081d9-128">Add Apache Spark log messages at the appropriate level in your code as required.</span></span> <span data-ttu-id="081d9-129">Ad esempio, usare il `logDebug` metodo per inviare un meesage log di debug.</span><span class="sxs-lookup"><span data-stu-id="081d9-129">For example, use the `logDebug` method to send a debug log meesage.</span></span> <span data-ttu-id="081d9-130">Per altre informazioni, vedere [Logging] [ spark-logging] nella documentazione di Spark.</span><span class="sxs-lookup"><span data-stu-id="081d9-130">For more information, see [Logging][spark-logging] in the Spark documentation.</span></span>

    ```Scala
    logTrace("Trace message")
    logDebug("Debug message")
    logInfo("Info message")
    logWarning("Warning message")
    logError("Error message")
    ```

## <a name="run-the-sample-application"></a><span data-ttu-id="081d9-131">Eseguire l'applicazione di esempio</span><span class="sxs-lookup"><span data-stu-id="081d9-131">Run the sample application</span></span>

<span data-ttu-id="081d9-132">La libreria di monitoraggio include un [applicazione di esempio] [ sample-app] che illustra come inviare sia metriche dell'applicazione e i log dell'applicazione di monitoraggio di Azure.</span><span class="sxs-lookup"><span data-stu-id="081d9-132">The monitoring library includes a [sample application][sample-app] that demonstrates how to send both application metrics and application logs to Azure Monitor.</span></span> <span data-ttu-id="081d9-133">Per eseguire l'esempio:</span><span class="sxs-lookup"><span data-stu-id="081d9-133">To run the sample:</span></span>

1. <span data-ttu-id="081d9-134">Compilare il **spark-jobs** del progetto nella libreria di monitoraggio, come descritto in [compilare la libreria di monitoraggio di Azure Databricks][build-lib].</span><span class="sxs-lookup"><span data-stu-id="081d9-134">Build the **spark-jobs** project in the monitoring library, as described in [Build the Azure Databricks Monitoring Library][build-lib].</span></span>

1. <span data-ttu-id="081d9-135">Passare all'area di lavoro di Databricks e creare un nuovo processo, come descritto [qui](https://docs.azuredatabricks.net/user-guide/jobs.html#create-a-job).</span><span class="sxs-lookup"><span data-stu-id="081d9-135">Navigate to your Databricks workspace and create a new job, as described [here](https://docs.azuredatabricks.net/user-guide/jobs.html#create-a-job).</span></span>

1. <span data-ttu-id="081d9-136">Nella pagina di dettagli del processo, selezionare **impostare JAR**.</span><span class="sxs-lookup"><span data-stu-id="081d9-136">In the job detail page, select **Set JAR**.</span></span>

1. <span data-ttu-id="081d9-137">Caricare il file JAR da `/src/spark-jobs/target/spark-jobs-1.0-SNAPSHOT.jar`.</span><span class="sxs-lookup"><span data-stu-id="081d9-137">Upload the JAR file from `/src/spark-jobs/target/spark-jobs-1.0-SNAPSHOT.jar`.</span></span>

1. <span data-ttu-id="081d9-138">Per la **classe principale**, immettere `com.microsoft.pnp.samplejob.StreamingQueryListenerSampleJob`.</span><span class="sxs-lookup"><span data-stu-id="081d9-138">For **Main class**, enter `com.microsoft.pnp.samplejob.StreamingQueryListenerSampleJob`.</span></span>

1. <span data-ttu-id="081d9-139">Selezionare un cluster che è già configurato per usare la libreria di monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="081d9-139">Select a cluster that is already configured to use the monitoring library.</span></span> <span data-ttu-id="081d9-140">Visualizzare [configurare Azure Databricks per inviare le metriche a monitoraggio di Azure][config-cluster].</span><span class="sxs-lookup"><span data-stu-id="081d9-140">See [Configure Azure Databricks to send metrics to Azure Monitor][config-cluster].</span></span>

<span data-ttu-id="081d9-141">Quando si esegue il processo, è possibile visualizzare i registri applicazioni e le metriche nell'area di lavoro di Log Analitica.</span><span class="sxs-lookup"><span data-stu-id="081d9-141">When the job runs, you can view the application logs and metrics in your Log Analytics workspace.</span></span>

<span data-ttu-id="081d9-142">I log dell'applicazione vengono visualizzati sotto SparkLoggingEvent_CL:</span><span class="sxs-lookup"><span data-stu-id="081d9-142">Application logs appear under SparkLoggingEvent_CL:</span></span>

```Kusto
SparkLoggingEvent_CL | where logger_name_s contains "com.microsoft.pnp"
```

<span data-ttu-id="081d9-143">Le metriche dell'applicazione vengono visualizzate sotto SparkMetric_CL:</span><span class="sxs-lookup"><span data-stu-id="081d9-143">Application metrics appear under SparkMetric_CL:</span></span>

```Kusto
SparkMetric_CL | where name_s contains "rowcounter" | limit 50
```

## <a name="next-steps"></a><span data-ttu-id="081d9-144">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="081d9-144">Next steps</span></span>

<span data-ttu-id="081d9-145">Distribuire il dashboard che accompagna questa libreria di codice per risolvere i problemi di prestazioni nei tuoi carichi di lavoro di produzione Azure Databricks di monitoraggio delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="081d9-145">Deploy the performance monitoring dashboard that accompanies this code library to troubleshoot performance issues in your production Azure Databricks workloads.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="081d9-146">Utilizzare Dashboard per visualizzare le metriche di Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="081d9-146">Use dashboards to visualize Azure Databricks metrics</span></span>](./dashboards.md)

<!-- links -->

[build-lib]: ./configure-cluster.md##build-the-azure-databricks-monitoring-library
[config-cluster]: ./configure-cluster.md
[log4j.properties]: https://github.com/mspnp/spark-monitoring/blob/master/src/spark-jobs/src/main/resources/com/microsoft/pnp/samplejob/log4j.properties
[sample-app]: https://github.com/mspnp/spark-monitoring/tree/master/src/spark-jobs
[spark-logging]: https://spark.apache.org/docs/2.3.0/api/java/org/apache/spark/internal/Logging.html