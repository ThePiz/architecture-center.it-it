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

# <a name="configure-azure-databricks-to-send-metrics-to-azure-monitor"></a>Configurare Azure Databricks per l'invio delle metriche a Monitoraggio di Azure

Questo articolo illustra come configurare un cluster Azure Databricks per inviare le metriche a un [dell'area di lavoro di Log Analitica](/azure/azure-monitor/platform/manage-access). Usa il [libreria di monitoraggio di Azure Databricks](https://github.com/mspnp/spark-monitoring), disponibile su GitHub. Conoscenza di Java, Scala e Maven sono consigliate come prerequisiti.

## <a name="about-the-azure-databricks-monitoring-library"></a>Sulla libreria di monitoraggio di Azure Databricks

Il [repository GitHub](https://github.com/mspnp/spark-monitoring) per la libreria di monitoraggio di Azure Databricks ha seguente struttura di directory:

```
/src  
    /spark-jobs  
    /spark-listeners-loganalytics  
    /spark-listeners  
    /pom.xml  
```

Il **processi di spark** directory è un'applicazione Spark di esempio che illustra come implementare un contatore delle metriche dell'applicazione Spark.

Il **spark-listener** directory include funzionalità che consente a Azure Databricks inviare gli eventi di Apache Spark a livello di servizio a un'area di lavoro di Analitica di Log di Azure. Azure Databricks è un servizio basato su Apache Spark, che include un set di API strutturate per l'elaborazione dati mediante SQL, Dataframe e set di dati di batch. Con Apache Spark 2.0, è stato aggiunto il supporto per [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html), un flusso di dati, l'elaborazione API basata su API per l'elaborazione batch di Spark.

Il **spark-listener di log Analytics** directory include un sink per i listener Spark, un sink per DropWizard e un client per un'area di lavoro di Azure Log Analitica. Questa directory include anche un Appender log4j per i log applicazioni di Apache Spark.

Il **spark-listener di log Analytics** e **spark-listener** directory contengono il codice per la creazione di due file JAR distribuiti nel cluster Databricks. Il **spark-listener** directory include un **script** directory che contiene uno script di inizializzazione nodo del cluster per copiare il file JAR di file da una directory di gestione temporanea nel file system di Azure Databricks per nodi di esecuzione.

Il **POM. XML** è il file di compilazione Maven principale per l'intero progetto.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, distribuire le risorse di Azure seguenti:

- Un'area di lavoro Azure Databricks. Vedere [Avvio rapido: Eseguire un processo Spark in Azure Databricks tramite il portale di Azure](/azure/azure-databricks/quickstart-create-databricks-workspace-portal).
- Un'area di lavoro Log Analytics. Visualizzare [creare un'area di lavoro di Log Analitica nel portale di Azure](/azure/azure-monitor/learn/quick-create-workspace).

Successivamente, installare il [interfaccia della riga di comando di Azure Databricks](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#install-the-cli). Per utilizzare l'interfaccia della riga di comando è necessario un token di accesso personale di Azure Databricks. Per istruzioni, vedere [generare un token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

Trovare l'ID area di lavoro di Log Analitica e la chiave. Per istruzioni, vedere [ottenere ID area di lavoro e la chiave](/azure/azure-monitor/platform/agent-windows#obtain-workspace-id-and-key). Questi valori è necessario quando si configura il cluster Databricks.

Per compilare la libreria di monitoraggio, è necessario un ambiente IDE Java con le risorse seguenti:

- [Java Development Kit (JDK) version 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
- [Linguaggio scala 2.11 SDK](https://www.scala-lang.org/download/)
- [Apache Maven 3.5.4](http://maven.apache.org/download.cgi)

## <a name="build-the-azure-databricks-monitoring-library"></a>Compilare una libreria di monitoraggio di Azure Databricks

Per compilare la libreria di monitoraggio di Azure Databricks, procedere come segue:

1. Clonare, creare il fork o scaricare il [repository GitHub](https://github.com/mspnp/spark-monitoring).

1. Importare il file di modello oggetto progetto di Maven, _POM. XML_, all'interno di **/src** cartella nel progetto. Verranno importate tre progetti:

    - processi Spark
    - spark-listeners
    - spark-listeners-loganalytics

1. Eseguire Maven **pacchetto** fase di compilazione nell'IDE di Java per creare i file con estensione JAR per ognuno di questi progetti:

    |Project| File JAR|
    |-------|---------|
    |processi Spark|spark-jobs-1.0-SNAPSHOT.jar|
    |spark-listeners|spark-listeners-1.0-SNAPSHOT.jar|
    |spark-listeners-loganalytics|spark-listeners-loganalytics-1.0-SNAPSHOT.jar|

1. Usare il comando di Azure Databricks per creare una directory denominata **dbfs: databricks/monitoraggio-gestionetemporanea/**:  

    ```bash
    dbfs mkdirs dbfs:/databricks/monitoring-staging
    ```

1. Usare il comando di Azure Databricks per copiare **/src/spark-listeners/scripts/listeners.sh** in precedenza nella directory:

    ```bash
    dbfs cp ./src/spark-listeners/scripts/listeners.sh dbfs:/databricks/monitoring-staging/listeners.sh
    ```

1. Usare il comando di Azure Databricks per copiare **/src/spark-listeners/scripts/metrics.properties** alla directory creata in precedenza:

    ```bash
    dbfs cp <local path to metrics.properties> dbfs:/databricks/monitoring-staging/metrics.properties
    ```

1. Usare il comando di Azure Databricks per copiare **spark-listener-1.0-snapshot. jar** e **spark-listener di lettura log Analytics-1.0-snapshot. jar** alla directory creata in precedenza:

    ```bash
    dbfs cp ./src/spark-listeners/target/spark-listeners-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-1.0-SNAPSHOT.jar
    dbfs cp ./src/spark-listeners-loganalytics/target/spark-listeners-loganalytics-1.0-SNAPSHOT.jar dbfs:/databricks/monitoring-staging/spark-listeners-loganalytics-1.0-SNAPSHOT.jar
    ```

## <a name="create-and-configure-an-azure-databricks-cluster"></a>Creare e configurare un cluster Azure Databricks

Per creare e configurare un cluster Azure Databricks, seguire questa procedura:

1. Passare all'area di lavoro Azure Databricks nel portale di Azure.
1. Nella home page, fare clic su **nuovo Cluster**.
1. Immettere un nome per il cluster nel **nome Cluster** casella di testo.
1. Nel **versione di Runtime di Databricks** elenco a discesa, seleziona **4.3** o versione successiva.
1. Sotto **opzioni avanzate**, fare clic sulla **Spark** scheda. Immettere le seguenti coppie nome-valore nel **configurazione di Spark** casella di testo:

    ```
    spark.extraListeners com.databricks.backend.daemon.driver.DBCEventLoggingListener,org.apache.spark.listeners.UnifiedSparkListener
    spark.unifiedListener.sink org.apache.spark.listeners.sink.loganalytics.LogAnalyticsListenerSink
    spark.unifiedListener.logBlockUpdates false
    ```

1. Mentre comunque sotto il **Spark** , immettere i valori seguenti nel **variabili di ambiente** casella di testo:

    ```
    LOG_ANALYTICS_WORKSPACE_ID=[your Azure Log Analytics workspace ID]
    LOG_ANALYTICS_WORKSPACE_KEY=[your Azure Log Analytics workspace key]
    ```

    ![Screenshot dell'interfaccia utente di Databricks](./_images/create-cluster1.png)

1. Mentre comunque sotto il **opzioni avanzate** sezione, fare clic sul **script Init** scheda.
1. Nel **destinazione** elenco a discesa, seleziona **DBFS**.
1. Sotto **percorso di Script di inizializzazione**, immettere `dbfs:/databricks/monitoring-staging/listeners.sh`.
1. Fare clic su **Aggiungi**.

    ![Screenshot dell'interfaccia utente di Databricks](./_images/create-cluster2.png)

1. Fare clic su **crea Cluster** per creare il cluster.

## <a name="view-metrics"></a>Visualizzare le metriche

Dopo aver completato questi passaggi, il cluster Databricks flussi alcuni dati delle metriche relative al cluster a monitoraggio di Azure. I dati del log sono disponibili nell'area di lavoro di Analitica di Log di Azure sotto il "Active | I log personalizzati | Schema SparkMetric_CL". Per altre informazioni sui tipi di metriche che vengono registrati, vedere [le metriche principali](https://metrics.dropwizard.io/4.0.0/manual/core.html) nella documentazione di Dropwizard. Il tipo di metrica, ad esempio contatore, contatore o dell'istogramma, viene scritto il campo di tipo.

Inoltre, la libreria trasmette eventi a livello di Apache Spark e Spark Structured Streaming le metriche da processi di monitoraggio di Azure. Non è necessario apportare modifiche al codice dell'applicazione per queste metriche ed eventi. I dati dei log di Spark Structured Streaming è disponibile con la "Active | I log personalizzati | Schema SparkListenerEvent_CL".

![Schermata di un'area di lavoro di Log Analitica](./_images/workspace.png)

Per altre informazioni sulla visualizzazione dei log, vedere [visualizzazione e analisi dei dati di log in Monitoraggio di Azure](/azure/azure-monitor/log-query/portals).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive come configurare il cluster per inviare le metriche a monitoraggio di Azure. L'articolo successivo illustra come usare la libreria di monitoraggio di Azure Databricks per inviare i log e metriche dell'applicazione.

> [!div class="nextstepaction"]
> [Inviare i log dell'applicazione al monitoraggio di Azure](./application-logs.md)
