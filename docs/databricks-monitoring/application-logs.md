---
title: Inviare i log dell'applicazione di Azure Databricks a Monitoraggio di Azure
description: Come inviare i log personalizzati e metriche da Azure Databricks per monitoraggio di Azure
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: ea67122d7871663e8aaf42b7af0043492f63b6b1
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59639186"
---
# <a name="send-azure-databricks-application-logs-to-azure-monitor"></a>Inviare i log dell'applicazione di Azure Databricks a Monitoraggio di Azure

Questo articolo illustra come inviare i log dell'applicazione e le metriche da Azure Databricks per un [dell'area di lavoro di Log Analitica](/azure/azure-monitor/platform/manage-access). Usa il [libreria di monitoraggio di Azure Databricks](https://github.com/mspnp/spark-monitoring), disponibile su GitHub.

## <a name="prerequisites"></a>Prerequisiti

Configurare il cluster Azure Databricks per usare la libreria di monitoraggio, come descritto in [configurare Azure Databricks per inviare le metriche a monitoraggio di Azure][config-cluster].

> [!NOTE]
> La libreria di monitoraggio trasmette eventi a livello di Apache Spark e Spark Structured Streaming le metriche da processi di monitoraggio di Azure. Non è necessario apportare modifiche al codice dell'applicazione per queste metriche ed eventi.

## <a name="send-application-metrics-using-dropwizard"></a>Inviare le metriche dell'applicazione usando Dropwizard

Spark Usa un sistema di metriche configurabile basato sulla libreria Dropwizard metriche. Per altre informazioni, vedere [metriche](https://spark.apache.org/docs/latest/monitoring.html#metrics) nella documentazione di Spark.

Per inviare le metriche dell'applicazione dal codice dell'applicazione di Azure Databricks per monitoraggio di Azure, seguire questa procedura:

1. Compilare il **spark-listener di lettura log Analytics-1.0-snapshot. jar** file con estensione JAR come descritto in [compilare la libreria di monitoraggio di Azure Databricks][build-lib].

1. Creare Dropwizard [misuratori o contatori](https://metrics.dropwizard.io/4.0.0/manual/core.html) nel codice dell'applicazione. È possibile usare il `UserMetricsSystem` classe definita nella libreria di monitoraggio. L'esempio seguente crea un contatore denominato `counter1`.

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

    La libreria di monitoraggio include un [applicazione di esempio] [ sample-app] che illustra come usare il `UserMetricsSystem` classe.

## <a name="send-application-logs-using-log4j"></a>Inviare i log dell'applicazione tramite Log4j

Per inviare i log dell'applicazione di Azure Databricks Analitica di Log di Azure usando il [appender Log4j](https://logging.apache.org/log4j/2.x/manual/appenders.html) nella raccolta, seguire questa procedura:

1. Compilare il **spark-listener di lettura log Analytics-1.0-snapshot. jar** file con estensione JAR come descritto in [compilare la libreria di monitoraggio di Azure Databricks][build-lib].

1. Creare un **log4j.properties** [file di configurazione](https://logging.apache.org/log4j/2.x/manual/configuration.html) per l'applicazione. Includere le seguenti proprietà di configurazione. Sostituire il nome del pacchetto dell'applicazione e log di livello dove indicato:

    ```YAML
    log4j.appender.A1=com.microsoft.pnp.logging.loganalytics.LogAnalyticsAppender
    log4j.appender.A1.layout=com.microsoft.pnp.logging.JSONLayout
    log4j.appender.A1.layout.LocationInfo=false
    log4j.additivity.<your application package name>=false
    log4j.logger.<your application package name>=<log level>, A1
    ```

    È possibile trovare un file di configurazione di esempio [Ecco][log4j.properties].

1. Nel codice dell'applicazione, includere il **spark-listener di log Analytics** del progetto e importare `com.microsoft.pnp.logging.Log4jconfiguration` al codice dell'applicazione.

    ```Scala
    import com.microsoft.pnp.logging.Log4jConfiguration
    ```

1. Configurare Log4j usando il **log4j.properties** file creato nel passaggio 3:

    ```Scala
    getClass.getResourceAsStream("<path to file in your JAR file>/log4j.properties")) {
          stream => {
            Log4jConfiguration.configure(stream)
          }
    }
    ```

1. Aggiungere i messaggi di log di Apache Spark al livello appropriato nel codice in base alle esigenze. Ad esempio, usare il `logDebug` metodo per inviare un messaggio di log di debug. Per altre informazioni, vedere [Logging] [ spark-logging] nella documentazione di Spark.

    ```Scala
    logTrace("Trace message")
    logDebug("Debug message")
    logInfo("Info message")
    logWarning("Warning message")
    logError("Error message")
    ```

## <a name="run-the-sample-application"></a>Eseguire l'applicazione di esempio

La libreria di monitoraggio include un [applicazione di esempio] [ sample-app] che illustra come inviare sia metriche dell'applicazione e i log dell'applicazione di monitoraggio di Azure. Per eseguire l'esempio:

1. Compilare il **spark-jobs** del progetto nella libreria di monitoraggio, come descritto in [compilare la libreria di monitoraggio di Azure Databricks][build-lib].

1. Passare all'area di lavoro di Databricks e creare un nuovo processo, come descritto [qui](https://docs.azuredatabricks.net/user-guide/jobs.html#create-a-job).

1. Nella pagina di dettagli del processo, selezionare **impostare JAR**.

1. Caricare il file JAR da `/src/spark-jobs/target/spark-jobs-1.0-SNAPSHOT.jar`.

1. Per la **classe principale**, immettere `com.microsoft.pnp.samplejob.StreamingQueryListenerSampleJob`.

1. Selezionare un cluster che è già configurato per usare la libreria di monitoraggio. Visualizzare [configurare Azure Databricks per inviare le metriche a monitoraggio di Azure][config-cluster].

Quando si esegue il processo, è possibile visualizzare i registri applicazioni e le metriche nell'area di lavoro di Log Analitica.

I log dell'applicazione vengono visualizzati sotto SparkLoggingEvent_CL:

```Kusto
SparkLoggingEvent_CL | where logger_name_s contains "com.microsoft.pnp"
```

Le metriche dell'applicazione vengono visualizzate sotto SparkMetric_CL:

```Kusto
SparkMetric_CL | where name_s contains "rowcounter" | limit 50
```

## <a name="next-steps"></a>Passaggi successivi

Distribuire il dashboard che accompagna questa libreria di codice per risolvere i problemi di prestazioni nei tuoi carichi di lavoro di produzione Azure Databricks di monitoraggio delle prestazioni.

> [!div class="nextstepaction"]
> [Usare i dashboard per visualizzare le metriche di Azure Databricks](./dashboards.md)

<!-- links -->

[build-lib]: ./configure-cluster.md##build-the-azure-databricks-monitoring-library
[config-cluster]: ./configure-cluster.md
[log4j.properties]: https://github.com/mspnp/spark-monitoring/blob/master/src/spark-jobs/src/main/resources/com/microsoft/pnp/samplejob/log4j.properties
[sample-app]: https://github.com/mspnp/spark-monitoring/tree/master/src/spark-jobs
[spark-logging]: https://spark.apache.org/docs/2.3.0/api/java/org/apache/spark/internal/Logging.html