---
title: Usare i dashboard per visualizzare le metriche di Azure Databricks
description: Come distribuire un dashboard di Grafana per monitorare le prestazioni in Azure Databricks
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: a84203a9188848e6363a80ac455332e8f6a73cda
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640312"
---
# <a name="use-dashboards-to-visualize-azure-databricks-metrics"></a>Usare i dashboard per visualizzare le metriche di Azure Databricks

Questo articolo illustra come configurare un dashboard di Grafana per monitorare i processi di Azure Databricks per i problemi di prestazioni.

[Azure Databricks](/azure/azure-databricks/) è un fast, potente e collaborativa [Apache Spark](https://spark.apache.org/)– basato su servizio analitica che rende più semplice sviluppare e distribuire soluzioni di intelligenza artificiale (AI) e analitica dei big data rapidamente. Il monitoraggio è un componente fondamentale del funzionamento dei carichi di lavoro Azure Databricks nell'ambiente di produzione. Il primo passaggio è per raccogliere le metriche in un'area di lavoro per l'analisi. In Azure è la soluzione migliore per la gestione dei dati di log [monitoraggio di Azure](/azure/azure-monitor/). Azure Databricks in modo nativo non supportano l'invio dei dati di log per il monitoraggio di Azure, ma un [libreria per questa funzionalità](https://github.com/mspnp/spark-monitoring) è disponibile in [Github](https://github.com).

Questa libreria consente la registrazione delle metriche del servizio Azure Databricks, nonché la struttura di Apache Spark streaming le metriche di eventi di query. Dopo aver correttamente distribuito la raccolta a un cluster Azure Databricks, è possibile distribuire un set di ulteriormente [Grafana](https://granfana.com) dashboard che è possibile distribuire come parte dell'ambiente di produzione.

![Screenshot del dashboard](./_images/dashboard-screenshot.png)

## <a name="prerequisites"></a>Prerequisiti

Clona il [repository Github](https://github.com/mspnp/spark-monitoring) e [seguire le istruzioni di distribuzione](./configure-cluster.md) per compilare e configurare la registrazione di monitoraggio di Azure per la libreria di Azure Databricks inviare i log all'area di lavoro di Analitica di Log di Azure.

## <a name="deploy-the-azure-log-analytics-workspace"></a>Distribuire l'area di lavoro di Analitica di Log di Azure

Per distribuire l'area di lavoro di Analitica di Log di Azure, seguire questa procedura:

1. Passare al `/perftools/deployment/loganalytics` directory.
1. Distribuire il **logAnalyticsDeploy.json** modello Azure Resource Manager. Per altre informazioni sulla distribuzione dei modelli di Resource Manager, vedere [distribuire le risorse con i modelli di Resource Manager e Azure CLI][rm-cli]. Il modello include i parametri seguenti:

    * **location**: L'area in cui vengono distribuiti le area di lavoro di Log Analitica e i dashboard.
    * **serviceTier**: Piano tariffario dell'area di lavoro Rhe. Visualizzare [Ecco] [ sku] per un elenco di valori validi.
    * **dataRetention** (facoltativo): Il numero di giorni di dati di log viene mantenuto nell'area di lavoro di Log Analitica. Il valore predefinito è 30 giorni. Se il piano tariffario è `Free`, la conservazione dei dati deve essere di 7 giorni.
    * **workspaceName** (optional): Un nome per l'area di lavoro. Se non specificato, il modello genera un nome.

    ```bash
    az group deployment create --resource-group <resource-group-name> --template-file logAnalyticsDeploy.json --parameters location='East US' serviceTier='Standalone'
    ```

Questo modello consente di creare l'area di lavoro e crea anche un set di query predefinite che vengono utilizzati dal dashboard.

## <a name="deploy-grafana-in-a-virtual-machine"></a>Distribuire Grafana in una macchina virtuale

Grafana è un progetto open source che è possibile distribuire per visualizzare le metriche di serie ora archiviate nell'area di lavoro di Analitica di Log di Azure usando il plug-in di Grafana per monitoraggio di Azure. Grafana esegue in una macchina virtuale (VM) e richiede un account di archiviazione, rete virtuale e altre risorse. Per distribuire una macchina virtuale con il bitnami certified Grafana immagine e le risorse associate, seguire questa procedura:

1. Usare il comando di Azure per accettare le condizioni di immagini di Azure Marketplace per Grafana.

    ```bash
    az vm image accept-terms --publisher bitnami --offer grafana --plan default
    ```

1. Passare al `/spark-monitoring/perftools/deployment/grafana` directory nella copia locale del repository GitHub.
1. Distribuire il **grafanaDeploy.json** modello di Resource Manager come indicato di seguito:

    ```bash
    export DATA_SOURCE="https://raw.githubusercontent.com/mspnp/spark-monitoring/master/perftools/deployment/grafana/AzureDataSource.sh"
    az group deployment create \
        --resource-group <resource-group-name> \
        --template-file grafanaDeploy.json \
        --parameters adminPass='<vm password>' dataSource=$DATA_SOURCE
    ```

Una volta completata la distribuzione, l'immagine bitnami di Grafana è installato nella macchina virtuale.

## <a name="update-the-grafana-password"></a>Aggiornare la password di Grafana

Come parte del processo di installazione, lo script di installazione di Grafana restituisce una password provvisoria per il **admin** utente. È necessario specificare tale password temporanea per l'accesso. Per ottenere la password temporanea, seguire questa procedura:  

1. Accedere al portale di Azure.  
1. Selezionare il gruppo di risorse in cui sono state distribuite le risorse.
1. Selezionare la macchina virtuale in cui è stato installato Grafana. Se si usa il nome del parametro predefinito nel modello di distribuzione, il nome VM è preceduto **sparkmonitoring-vm-grafana**.
1. Nel **supporto + risoluzione dei problemi** fare clic su **diagnostica di avvio** per aprire la pagina di diagnostica di avvio.
1. Fare clic su **log seriale** nella pagina di diagnostica di avvio.
1. Cercare la stringa seguente: "Impostazione password di applicazioni Bitnami su".
1. Copiare la password in una posizione sicura.

Successivamente, modificare la password di amministratore di Grafana seguendo questa procedura:

1. Nel portale di Azure, selezionare la macchina virtuale e fare clic su **Panoramica**.
1. Copiare l'indirizzo IP pubblico.
1. Aprire un web browser e passare all'URL seguente: `http://<IP address>:3000`.
1. Nella schermata di accesso di Grafana, immettere **admin** per il nome utente e la password di Grafana dai passaggi precedenti.
1. Una volta effettuato l'accesso, selezionare **configurazione** (l'icona a forma di ingranaggio).
1. Selezionare **l'amministratore del Server**.
1. Nel **gli utenti** scheda, seleziona la **admin** account di accesso.
1. Aggiornare la password.

## <a name="create-an-azure-monitor-data-source"></a>Creare un'origine dati di monitoraggio di Azure

1. Creare un'entità servizio che consente di Grafana gestire l'accesso all'area di lavoro di Log Analitica. Per altre informazioni, vedere [creare entità servizio di Azure con Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

    ```bash
    az ad sp create-for-rbac --name http://<service principal name> --role "Log Analytics Reader"
    ```

1. Prendere nota dei valori per l'appId, password e tenant nell'output di questo comando:

    ```json
    {
        "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "displayName": "azure-cli-2019-03-27-00-33-39",
        "name": "http://<service principal name>",
        "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Accedere a Grafana come descritto in precedenza. Selezionare **Configuration** (l'icona a forma di ingranaggio) e quindi **Zdroje dat**.
1. Nel **Zdroje dat** scheda, fare clic su **Aggiungi origine dati**.
1. Selezionare **monitoraggio di Azure** come tipo di origine dati.
1. Nel **le impostazioni** , quindi immettere un nome per l'origine dati nel **nome** nella casella di testo.
1. Nel **dettagli dell'API Azure Monitor** sezione, immettere le informazioni seguenti:

    * ID sottoscrizione: ID sottoscrizione di Azure.
    * Id tenant: L'ID tenant in precedenza.
    * Id client: Il valore di "appId" illustrato in precedenza.
    * Segreto client: Il valore di "password" in precedenza.

1. Nel **i dettagli dell'API Azure Log Analitica** sezione, verificare le **stessi dettagli come API di monitoraggio di Azure** casella di controllo.
1. Fare clic su **Salva e verifica**. Se l'origine dati Analitica di Log è configurato correttamente, viene visualizzato un messaggio di conferma.

## <a name="create-the-dashboard"></a>Creare il dashboard

Creare i dashboard di Grafana seguendo questa procedura:

1. Passare al `/perftools/dashboards/grafana` directory nella copia locale del repository GitHub.
1. Eseguire lo script seguente:

    ```bash
    export WORKSPACE=<your Azure Log Analytics workspace ID>
    export LOGTYPE=SparkListenerEvent_CL

    sh DashGen.sh
    ```

    L'output dello script è un file denominato **SparkMonitoringDash.json**.

1. Tornare al dashboard di Grafana e selezionare **Create** (icona del segno più).
1. Selezionare **Importa**.
1. Fare clic su **caricare il File con estensione JSON**.
1. Selezionare il **SparkMonitoringDash.json** file creato nel passaggio 2.
1. Nel **le opzioni** nella sezione **ALA**, selezionare l'origine dati di monitoraggio di Azure creata in precedenza.
1. Fare clic su **Importa**.

## <a name="visualizations-in-the-dashboards"></a>Visualizzazioni nei dashboard

I dashboard di Grafana sia l'Analitica di Log di Azure includono un set di visualizzazioni di serie temporali. Ogni grafico viene tracciato di serie temporali dei dati delle metriche correlati a un Apache Spark [processo](https://spark.apache.org/docs/latest/job-scheduling.html), fasi del processo e attività che costituiscono ogni fase.

Le visualizzazioni sono come segue:

### <a name="job-latency"></a>Latenza del processo

Questa visualizzazione mostra la latenza di esecuzione per un processo, ovvero una visualizzazione generico sulle prestazioni complessive di un processo. Visualizza la durata di esecuzione processo dall'inizio al completamento. Si noti che ora di inizio del processo non sono quello utilizzato per l'ora di invio del processo. La latenza è rappresentata come percentili (del 10%, 30%, 50%, 90%) esecuzione dei processi indicizzato da ID cluster e ID dell'applicazione.

### <a name="stage-latency"></a>Latenza di fase

La visualizzazione mostra la latenza di ogni fase per ogni cluster, per ogni applicazione e per ogni fase singola. Questa visualizzazione è utile per identificare una specifica fase che viene eseguita lentamente.

### <a name="task-latency"></a>Latenza di attività

Questa visualizzazione mostra la latenza di esecuzione attività. La latenza è rappresentata come un percentile dell'esecuzione di attività per ogni cluster, nome fase e dell'applicazione.

### <a name="sum-task-execution-per-host"></a>Esecuzione dell'attività Sum per ogni host

Questa visualizzazione mostra la somma della latenza di esecuzione di attività per ogni host in esecuzione in un cluster. Visualizzando la latenza di esecuzione attività per ogni host identifica gli host che hanno una quantità eccessiva latenza maggiore di attività complessivo rispetto degli altri host. Questo può significare che le attività in modo inefficiente o non sono distribuite agli host.

### <a name="task-metrics"></a>Metrica delle attività

Questa visualizzazione mostra un set di metriche di esecuzione per l'esecuzione dell'attività specificata. Queste metriche includono le dimensioni e durata di una sequenza casuale dei dati, la durata delle operazioni di serializzazione e deserializzazione e ad altri utenti. Per il set completo di metriche, visualizzare la query di Log Analitica per il pannello. Questa visualizzazione è utile per comprendere le operazioni che costituiscono un'attività e l'utilizzo delle risorse di identificazione di ogni operazione. I picchi nel grafico rappresentano operazioni costose che dovrebbero essere esaminate.

### <a name="cluster-throughput"></a>Velocità effettiva di cluster

Questa visualizzazione è una vista di alto livello degli elementi di lavoro indicizzato dal cluster e delle applicazioni per rappresentare la quantità di lavoro eseguito per ogni cluster e dell'applicazione. Mostra il numero di processi, attività e fasi completate al cluster, l'applicazione e fase in incrementi di 1 minuto. 

### <a name="streaming-throughputlatency"></a>Velocità effettiva/latenza streaming

Questa visualizzazione è correlata alle metriche di cui è associate a una query di streaming strutturata. I grafici visualizzano il numero di righe di input al secondo e il numero di righe elaborate al secondo. La metrica di streaming è rappresentata anche per ogni applicazione. Queste metriche vengono inviate quando viene generato l'evento OnQueryProgress quando viene elaborata la query di streaming strutturata e rappresenta la visualizzazione streaming latenza come la quantità di tempo, espresso in millisecondi, impiegato per eseguire un batch di query.

### <a name="resource-consumption-per-executor"></a>Utilizzo delle risorse per ogni executor

Di seguito è riportato un set di visualizzazioni per la presentazione del dashboard il particolare tipo di risorsa e come vengono utilizzati per ogni executor in ogni cluster. Queste visualizzazioni consentono di identificare gli outlier in termini di consumo di risorse per ogni executor. Ad esempio, se l'allocazione di lavoro per un esecutore specifico è sfasato e consumo di risorse verrà elevato in relazione agli altri executor in esecuzione nel cluster. Può essere identificato da picchi di utilizzo delle risorse per un executor.

### <a name="executor-compute-time-metrics"></a>Metriche di tempo di calcolo dell'executor

Di seguito è riportato un set di visualizzazioni per il dashboard che mostra il rapporto tra executor serializzare tempo, deserializzare tempo, il tempo di CPU e l'ora di macchina virtuale Java per il tempo di calcolo complessivo dell'executor. Ciò dimostra visivamente quanto ognuna di queste quattro metriche sono aggiunta come contributo al sistema complessivo elaborazione.

### <a name="shuffle-metrics"></a>Le metriche di riproduzione casuale

Il set finale di visualizzazioni Mostra i dati di riproduzione casuale le metriche associate a una query di streaming strutturata tra tutti gli executor. Ad esempio shuffle byte letti e shuffle byte scritti, shuffle memoria e utilizzo dei dischi nelle query in cui viene usato il file system.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Risolvere i problemi di colli di bottiglia delle prestazioni](./performance-troubleshooting.md)

<!-- links -->

[rm-cli]: /azure/azure-resource-manager/resource-group-template-deploy-cli
[sku]: /azure/templates/Microsoft.OperationalInsights/2015-11-01-preview/workspaces#sku-object