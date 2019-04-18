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
# <a name="use-dashboards-to-visualize-azure-databricks-metrics"></a><span data-ttu-id="25d86-103">Usare i dashboard per visualizzare le metriche di Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="25d86-103">Use dashboards to visualize Azure Databricks metrics</span></span>

<span data-ttu-id="25d86-104">Questo articolo illustra come configurare un dashboard di Grafana per monitorare i processi di Azure Databricks per i problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="25d86-104">This article shows how to set up a Grafana dashboard to monitor Azure Databricks jobs for performance issues.</span></span>

<span data-ttu-id="25d86-105">[Azure Databricks](/azure/azure-databricks/) è un fast, potente e collaborativa [Apache Spark](https://spark.apache.org/)– basato su servizio analitica che rende più semplice sviluppare e distribuire soluzioni di intelligenza artificiale (AI) e analitica dei big data rapidamente.</span><span class="sxs-lookup"><span data-stu-id="25d86-105">[Azure Databricks](/azure/azure-databricks/) is a fast, powerful, and collaborative [Apache Spark](https://spark.apache.org/)–based analytics service that makes it easy to rapidly develop and deploy big data analytics and artificial intelligence (AI) solutions.</span></span> <span data-ttu-id="25d86-106">Il monitoraggio è un componente fondamentale del funzionamento dei carichi di lavoro Azure Databricks nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="25d86-106">Monitoring is a critical component of operating Azure Databricks workloads in production.</span></span> <span data-ttu-id="25d86-107">Il primo passaggio è per raccogliere le metriche in un'area di lavoro per l'analisi.</span><span class="sxs-lookup"><span data-stu-id="25d86-107">The first step is to gather metrics into a workspace for analysis.</span></span> <span data-ttu-id="25d86-108">In Azure è la soluzione migliore per la gestione dei dati di log [monitoraggio di Azure](/azure/azure-monitor/).</span><span class="sxs-lookup"><span data-stu-id="25d86-108">In Azure, the best solution for managing log data is [Azure Monitor](/azure/azure-monitor/).</span></span> <span data-ttu-id="25d86-109">Azure Databricks in modo nativo non supportano l'invio dei dati di log per il monitoraggio di Azure, ma un [libreria per questa funzionalità](https://github.com/mspnp/spark-monitoring) è disponibile in [Github](https://github.com).</span><span class="sxs-lookup"><span data-stu-id="25d86-109">Azure Databricks does not natively support sending log data to Azure monitor, but a [library for this functionality](https://github.com/mspnp/spark-monitoring) is available in [Github](https://github.com).</span></span>

<span data-ttu-id="25d86-110">Questa libreria consente la registrazione delle metriche del servizio Azure Databricks, nonché la struttura di Apache Spark streaming le metriche di eventi di query.</span><span class="sxs-lookup"><span data-stu-id="25d86-110">This library enables logging of Azure Databricks service metrics as well as Apache Spark structure streaming query event metrics.</span></span> <span data-ttu-id="25d86-111">Dopo aver correttamente distribuito la raccolta a un cluster Azure Databricks, è possibile distribuire un set di ulteriormente [Grafana](https://granfana.com) dashboard che è possibile distribuire come parte dell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="25d86-111">Once you've successfully deployed this library to an Azure Databricks cluster, you can further deploy a set of [Grafana](https://granfana.com) dashboards that you can deploy as part of your production environment.</span></span>

![Screenshot del dashboard](./_images/dashboard-screenshot.png)

## <a name="prerequisites"></a><span data-ttu-id="25d86-113">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="25d86-113">Prerequisites</span></span>

<span data-ttu-id="25d86-114">Clona il [repository Github](https://github.com/mspnp/spark-monitoring) e [seguire le istruzioni di distribuzione](./configure-cluster.md) per compilare e configurare la registrazione di monitoraggio di Azure per la libreria di Azure Databricks inviare i log all'area di lavoro di Analitica di Log di Azure.</span><span class="sxs-lookup"><span data-stu-id="25d86-114">Clone the [Github repository](https://github.com/mspnp/spark-monitoring) and [follow the deployment instructions](./configure-cluster.md) to build and configure the Azure Monitor logging for Azure Databricks library to send logs to your Azure Log Analytics workspace.</span></span>

## <a name="deploy-the-azure-log-analytics-workspace"></a><span data-ttu-id="25d86-115">Distribuire l'area di lavoro di Analitica di Log di Azure</span><span class="sxs-lookup"><span data-stu-id="25d86-115">Deploy the Azure Log Analytics workspace</span></span>

<span data-ttu-id="25d86-116">Per distribuire l'area di lavoro di Analitica di Log di Azure, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="25d86-116">To deploy the Azure Log Analytics workspace, follow these steps:</span></span>

1. <span data-ttu-id="25d86-117">Passare al `/perftools/deployment/loganalytics` directory.</span><span class="sxs-lookup"><span data-stu-id="25d86-117">Navigate to the `/perftools/deployment/loganalytics` directory.</span></span>
1. <span data-ttu-id="25d86-118">Distribuire il **logAnalyticsDeploy.json** modello Azure Resource Manager.</span><span class="sxs-lookup"><span data-stu-id="25d86-118">Deploy the **logAnalyticsDeploy.json** Azure Resource Manager template.</span></span> <span data-ttu-id="25d86-119">Per altre informazioni sulla distribuzione dei modelli di Resource Manager, vedere [distribuire le risorse con i modelli di Resource Manager e Azure CLI][rm-cli].</span><span class="sxs-lookup"><span data-stu-id="25d86-119">For more information about deploying Resource Manager templates, see [Deploy resources with Resource Manager templates and Azure CLI][rm-cli].</span></span> <span data-ttu-id="25d86-120">Il modello include i parametri seguenti:</span><span class="sxs-lookup"><span data-stu-id="25d86-120">The template has the following parameters:</span></span>

    * <span data-ttu-id="25d86-121">**location**: L'area in cui vengono distribuiti le area di lavoro di Log Analitica e i dashboard.</span><span class="sxs-lookup"><span data-stu-id="25d86-121">**location**: The region where the Log Analytics workspace and dashboards are deployed.</span></span>
    * <span data-ttu-id="25d86-122">**serviceTier**: Piano tariffario dell'area di lavoro Rhe.</span><span class="sxs-lookup"><span data-stu-id="25d86-122">**serviceTier**: Rhe workspace pricing tier.</span></span> <span data-ttu-id="25d86-123">Visualizzare [Ecco] [ sku] per un elenco di valori validi.</span><span class="sxs-lookup"><span data-stu-id="25d86-123">See [here][sku] for a list of valid values.</span></span>
    * <span data-ttu-id="25d86-124">**dataRetention** (facoltativo): Il numero di giorni di dati di log viene mantenuto nell'area di lavoro di Log Analitica.</span><span class="sxs-lookup"><span data-stu-id="25d86-124">**dataRetention** (optional): The number of days log data is retained in the Log Analytics workspace.</span></span> <span data-ttu-id="25d86-125">Il valore predefinito è 30 giorni.</span><span class="sxs-lookup"><span data-stu-id="25d86-125">The default value is 30 days.</span></span> <span data-ttu-id="25d86-126">Se il piano tariffario è `Free`, la conservazione dei dati deve essere di 7 giorni.</span><span class="sxs-lookup"><span data-stu-id="25d86-126">If the pricing tier is `Free`, the data retention must be 7 days.</span></span>
    * <span data-ttu-id="25d86-127">**workspaceName** (optional): Un nome per l'area di lavoro.</span><span class="sxs-lookup"><span data-stu-id="25d86-127">**workspaceName** (optional): A name for the workspace.</span></span> <span data-ttu-id="25d86-128">Se non specificato, il modello genera un nome.</span><span class="sxs-lookup"><span data-stu-id="25d86-128">If not specified, the template generates a name.</span></span>

    ```bash
    az group deployment create --resource-group <resource-group-name> --template-file logAnalyticsDeploy.json --parameters location='East US' serviceTier='Standalone'
    ```

<span data-ttu-id="25d86-129">Questo modello consente di creare l'area di lavoro e crea anche un set di query predefinite che vengono utilizzati dal dashboard.</span><span class="sxs-lookup"><span data-stu-id="25d86-129">This template creates the workspace and also creates a set of predefined queries that are used by dashboard.</span></span>

## <a name="deploy-grafana-in-a-virtual-machine"></a><span data-ttu-id="25d86-130">Distribuire Grafana in una macchina virtuale</span><span class="sxs-lookup"><span data-stu-id="25d86-130">Deploy Grafana in a virtual machine</span></span>

<span data-ttu-id="25d86-131">Grafana è un progetto open source che è possibile distribuire per visualizzare le metriche di serie ora archiviate nell'area di lavoro di Analitica di Log di Azure usando il plug-in di Grafana per monitoraggio di Azure.</span><span class="sxs-lookup"><span data-stu-id="25d86-131">Grafana is an open source project you can deploy to visualize the time series metrics stored in your Azure Log Analytics workspace using the Grafana plugin for Azure Monitor.</span></span> <span data-ttu-id="25d86-132">Grafana esegue in una macchina virtuale (VM) e richiede un account di archiviazione, rete virtuale e altre risorse.</span><span class="sxs-lookup"><span data-stu-id="25d86-132">Grafana executes on a virtual machine (VM) and requires a storage account, virtual network, and other resources.</span></span> <span data-ttu-id="25d86-133">Per distribuire una macchina virtuale con il bitnami certified Grafana immagine e le risorse associate, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="25d86-133">To deploy a virtual machine with the bitnami certified Grafana image and associated resources, follow these steps:</span></span>

1. <span data-ttu-id="25d86-134">Usare il comando di Azure per accettare le condizioni di immagini di Azure Marketplace per Grafana.</span><span class="sxs-lookup"><span data-stu-id="25d86-134">Use the Azure CLI to accept the Azure Marketplace image terms for Grafana.</span></span>

    ```bash
    az vm image accept-terms --publisher bitnami --offer grafana --plan default
    ```

1. <span data-ttu-id="25d86-135">Passare al `/spark-monitoring/perftools/deployment/grafana` directory nella copia locale del repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="25d86-135">Navigate to the `/spark-monitoring/perftools/deployment/grafana` directory in your local copy of the GitHub repo.</span></span>
1. <span data-ttu-id="25d86-136">Distribuire il **grafanaDeploy.json** modello di Resource Manager come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="25d86-136">Deploy the **grafanaDeploy.json** Resource Manager template as follows:</span></span>

    ```bash
    export DATA_SOURCE="https://raw.githubusercontent.com/mspnp/spark-monitoring/master/perftools/deployment/grafana/AzureDataSource.sh"
    az group deployment create \
        --resource-group <resource-group-name> \
        --template-file grafanaDeploy.json \
        --parameters adminPass='<vm password>' dataSource=$DATA_SOURCE
    ```

<span data-ttu-id="25d86-137">Una volta completata la distribuzione, l'immagine bitnami di Grafana è installato nella macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="25d86-137">Once the deployment is complete, the bitnami image of Grafana is installed on the virtual machine.</span></span>

## <a name="update-the-grafana-password"></a><span data-ttu-id="25d86-138">Aggiornare la password di Grafana</span><span class="sxs-lookup"><span data-stu-id="25d86-138">Update the Grafana password</span></span>

<span data-ttu-id="25d86-139">Come parte del processo di installazione, lo script di installazione di Grafana restituisce una password provvisoria per il **admin** utente.</span><span class="sxs-lookup"><span data-stu-id="25d86-139">As part of the setup process, the Grafana installation script outputs a temporary password for the **admin** user.</span></span> <span data-ttu-id="25d86-140">È necessario specificare tale password temporanea per l'accesso.</span><span class="sxs-lookup"><span data-stu-id="25d86-140">You need this temporary password to sign in.</span></span> <span data-ttu-id="25d86-141">Per ottenere la password temporanea, seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="25d86-141">To obtain the temporary password, follow these steps:</span></span>  

1. <span data-ttu-id="25d86-142">Accedere al portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="25d86-142">Log in to the Azure portal.</span></span>  
1. <span data-ttu-id="25d86-143">Selezionare il gruppo di risorse in cui sono state distribuite le risorse.</span><span class="sxs-lookup"><span data-stu-id="25d86-143">Select the resource group where the resources were deployed.</span></span>
1. <span data-ttu-id="25d86-144">Selezionare la macchina virtuale in cui è stato installato Grafana.</span><span class="sxs-lookup"><span data-stu-id="25d86-144">Select the VM where Grafana was installed.</span></span> <span data-ttu-id="25d86-145">Se si usa il nome del parametro predefinito nel modello di distribuzione, il nome VM è preceduto **sparkmonitoring-vm-grafana**.</span><span class="sxs-lookup"><span data-stu-id="25d86-145">If you used the default parameter name in the deployment template, the VM name is prefaced with **sparkmonitoring-vm-grafana**.</span></span>
1. <span data-ttu-id="25d86-146">Nel **supporto + risoluzione dei problemi** fare clic su **diagnostica di avvio** per aprire la pagina di diagnostica di avvio.</span><span class="sxs-lookup"><span data-stu-id="25d86-146">In the **Support + troubleshooting** section, click **Boot diagnostics** to open the boot diagnostics page.</span></span>
1. <span data-ttu-id="25d86-147">Fare clic su **log seriale** nella pagina di diagnostica di avvio.</span><span class="sxs-lookup"><span data-stu-id="25d86-147">Click **Serial log** on the boot diagnostics page.</span></span>
1. <span data-ttu-id="25d86-148">Cercare la stringa seguente: "Impostazione password di applicazioni Bitnami su".</span><span class="sxs-lookup"><span data-stu-id="25d86-148">Search for the following string: "Setting Bitnami application password to".</span></span>
1. <span data-ttu-id="25d86-149">Copiare la password in una posizione sicura.</span><span class="sxs-lookup"><span data-stu-id="25d86-149">Copy the password to a safe location.</span></span>

<span data-ttu-id="25d86-150">Successivamente, modificare la password di amministratore di Grafana seguendo questa procedura:</span><span class="sxs-lookup"><span data-stu-id="25d86-150">Next, change the Grafana administrator password by following these steps:</span></span>

1. <span data-ttu-id="25d86-151">Nel portale di Azure, selezionare la macchina virtuale e fare clic su **Panoramica**.</span><span class="sxs-lookup"><span data-stu-id="25d86-151">In the Azure portal, select the VM and click **Overview**.</span></span>
1. <span data-ttu-id="25d86-152">Copiare l'indirizzo IP pubblico.</span><span class="sxs-lookup"><span data-stu-id="25d86-152">Copy the public IP address.</span></span>
1. <span data-ttu-id="25d86-153">Aprire un web browser e passare all'URL seguente: `http://<IP address>:3000`.</span><span class="sxs-lookup"><span data-stu-id="25d86-153">Open a web browser and navigate to the following URL: `http://<IP address>:3000`.</span></span>
1. <span data-ttu-id="25d86-154">Nella schermata di accesso di Grafana, immettere **admin** per il nome utente e la password di Grafana dai passaggi precedenti.</span><span class="sxs-lookup"><span data-stu-id="25d86-154">At the Grafana log in screen, enter **admin** for the user name, and use the Grafana password from the previous steps.</span></span>
1. <span data-ttu-id="25d86-155">Una volta effettuato l'accesso, selezionare **configurazione** (l'icona a forma di ingranaggio).</span><span class="sxs-lookup"><span data-stu-id="25d86-155">Once logged in, select **Configuration** (the gear icon).</span></span>
1. <span data-ttu-id="25d86-156">Selezionare **l'amministratore del Server**.</span><span class="sxs-lookup"><span data-stu-id="25d86-156">Select **Server Admin**.</span></span>
1. <span data-ttu-id="25d86-157">Nel **gli utenti** scheda, seleziona la **admin** account di accesso.</span><span class="sxs-lookup"><span data-stu-id="25d86-157">On the **Users** tab, select the **admin** login.</span></span>
1. <span data-ttu-id="25d86-158">Aggiornare la password.</span><span class="sxs-lookup"><span data-stu-id="25d86-158">Update the password.</span></span>

## <a name="create-an-azure-monitor-data-source"></a><span data-ttu-id="25d86-159">Creare un'origine dati di monitoraggio di Azure</span><span class="sxs-lookup"><span data-stu-id="25d86-159">Create an Azure Monitor data source</span></span>

1. <span data-ttu-id="25d86-160">Creare un'entità servizio che consente di Grafana gestire l'accesso all'area di lavoro di Log Analitica.</span><span class="sxs-lookup"><span data-stu-id="25d86-160">Create a service principal that allows Grafana to manage access to your Log Analytics workspace.</span></span> <span data-ttu-id="25d86-161">Per altre informazioni, vedere [creare entità servizio di Azure con Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)</span><span class="sxs-lookup"><span data-stu-id="25d86-161">For more information, see [Create an Azure service principal with Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)</span></span>

    ```bash
    az ad sp create-for-rbac --name http://<service principal name> --role "Log Analytics Reader"
    ```

1. <span data-ttu-id="25d86-162">Prendere nota dei valori per l'appId, password e tenant nell'output di questo comando:</span><span class="sxs-lookup"><span data-stu-id="25d86-162">Note the values for appId, password, and tenant in the output from this command:</span></span>

    ```json
    {
        "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "displayName": "azure-cli-2019-03-27-00-33-39",
        "name": "http://<service principal name>",
        "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. <span data-ttu-id="25d86-163">Accedere a Grafana come descritto in precedenza.</span><span class="sxs-lookup"><span data-stu-id="25d86-163">Log into Grafana as described earlier.</span></span> <span data-ttu-id="25d86-164">Selezionare **Configuration** (l'icona a forma di ingranaggio) e quindi **Zdroje dat**.</span><span class="sxs-lookup"><span data-stu-id="25d86-164">Select **Configuration** (the gear icon) and then **Data Sources**.</span></span>
1. <span data-ttu-id="25d86-165">Nel **Zdroje dat** scheda, fare clic su **Aggiungi origine dati**.</span><span class="sxs-lookup"><span data-stu-id="25d86-165">In the **Data Sources** tab, click **Add data source**.</span></span>
1. <span data-ttu-id="25d86-166">Selezionare **monitoraggio di Azure** come tipo di origine dati.</span><span class="sxs-lookup"><span data-stu-id="25d86-166">Select **Azure Monitor** as the data source type.</span></span>
1. <span data-ttu-id="25d86-167">Nel **le impostazioni** , quindi immettere un nome per l'origine dati nel **nome** nella casella di testo.</span><span class="sxs-lookup"><span data-stu-id="25d86-167">In the **Settings** section, enter a name for the data source in the **Name** textbox.</span></span>
1. <span data-ttu-id="25d86-168">Nel **dettagli dell'API Azure Monitor** sezione, immettere le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="25d86-168">In the **Azure Monitor API Details** section, enter the following information:</span></span>

    * <span data-ttu-id="25d86-169">ID sottoscrizione: ID sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="25d86-169">Subscription Id: Your Azure subscription ID.</span></span>
    * <span data-ttu-id="25d86-170">Id tenant: L'ID tenant in precedenza.</span><span class="sxs-lookup"><span data-stu-id="25d86-170">Tenant Id: The tenant ID from earlier.</span></span>
    * <span data-ttu-id="25d86-171">Id client: Il valore di "appId" illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="25d86-171">Client Id: The value of "appId" from earlier.</span></span>
    * <span data-ttu-id="25d86-172">Segreto client: Il valore di "password" in precedenza.</span><span class="sxs-lookup"><span data-stu-id="25d86-172">Client Secret: The value of "password" from earlier.</span></span>

1. <span data-ttu-id="25d86-173">Nel **i dettagli dell'API Azure Log Analitica** sezione, verificare le **stessi dettagli come API di monitoraggio di Azure** casella di controllo.</span><span class="sxs-lookup"><span data-stu-id="25d86-173">In the **Azure Log Analytics API Details** section, check the **Same Details as Azure Monitor API** checkbox.</span></span>
1. <span data-ttu-id="25d86-174">Fare clic su **Salva e verifica**.</span><span class="sxs-lookup"><span data-stu-id="25d86-174">Click **Save & Test**.</span></span> <span data-ttu-id="25d86-175">Se l'origine dati Analitica di Log è configurato correttamente, viene visualizzato un messaggio di conferma.</span><span class="sxs-lookup"><span data-stu-id="25d86-175">If the Log Analytics data source is correctly configured, a success message is displayed.</span></span>

## <a name="create-the-dashboard"></a><span data-ttu-id="25d86-176">Creare il dashboard</span><span class="sxs-lookup"><span data-stu-id="25d86-176">Create the dashboard</span></span>

<span data-ttu-id="25d86-177">Creare i dashboard di Grafana seguendo questa procedura:</span><span class="sxs-lookup"><span data-stu-id="25d86-177">Create the dashboards in Grafana by following these steps:</span></span>

1. <span data-ttu-id="25d86-178">Passare al `/perftools/dashboards/grafana` directory nella copia locale del repository GitHub.</span><span class="sxs-lookup"><span data-stu-id="25d86-178">Navigate to the `/perftools/dashboards/grafana` directory in your local copy of the GitHub repo.</span></span>
1. <span data-ttu-id="25d86-179">Eseguire lo script seguente:</span><span class="sxs-lookup"><span data-stu-id="25d86-179">Run the following script:</span></span>

    ```bash
    export WORKSPACE=<your Azure Log Analytics workspace ID>
    export LOGTYPE=SparkListenerEvent_CL

    sh DashGen.sh
    ```

    <span data-ttu-id="25d86-180">L'output dello script è un file denominato **SparkMonitoringDash.json**.</span><span class="sxs-lookup"><span data-stu-id="25d86-180">The output from the script is a file named **SparkMonitoringDash.json**.</span></span>

1. <span data-ttu-id="25d86-181">Tornare al dashboard di Grafana e selezionare **Create** (icona del segno più).</span><span class="sxs-lookup"><span data-stu-id="25d86-181">Return to the Grafana dashboard and select **Create** (the plus icon).</span></span>
1. <span data-ttu-id="25d86-182">Selezionare **Importa**.</span><span class="sxs-lookup"><span data-stu-id="25d86-182">Select **Import**.</span></span>
1. <span data-ttu-id="25d86-183">Fare clic su **caricare il File con estensione JSON**.</span><span class="sxs-lookup"><span data-stu-id="25d86-183">Click **Upload .json File**.</span></span>
1. <span data-ttu-id="25d86-184">Selezionare il **SparkMonitoringDash.json** file creato nel passaggio 2.</span><span class="sxs-lookup"><span data-stu-id="25d86-184">Select the **SparkMonitoringDash.json** file created in step 2.</span></span>
1. <span data-ttu-id="25d86-185">Nel **le opzioni** nella sezione **ALA**, selezionare l'origine dati di monitoraggio di Azure creata in precedenza.</span><span class="sxs-lookup"><span data-stu-id="25d86-185">In the **Options** section, under **ALA**, select the Azure Monitor data source created earlier.</span></span>
1. <span data-ttu-id="25d86-186">Fare clic su **Importa**.</span><span class="sxs-lookup"><span data-stu-id="25d86-186">Click **Import**.</span></span>

## <a name="visualizations-in-the-dashboards"></a><span data-ttu-id="25d86-187">Visualizzazioni nei dashboard</span><span class="sxs-lookup"><span data-stu-id="25d86-187">Visualizations in the dashboards</span></span>

<span data-ttu-id="25d86-188">I dashboard di Grafana sia l'Analitica di Log di Azure includono un set di visualizzazioni di serie temporali.</span><span class="sxs-lookup"><span data-stu-id="25d86-188">Both the Azure Log Analytics and Grafana dashboards include a set of time-series visualizations.</span></span> <span data-ttu-id="25d86-189">Ogni grafico viene tracciato di serie temporali dei dati delle metriche correlati a un Apache Spark [processo](https://spark.apache.org/docs/latest/job-scheduling.html), fasi del processo e attività che costituiscono ogni fase.</span><span class="sxs-lookup"><span data-stu-id="25d86-189">Each graph is time-series plot of metric data related to an Apache Spark [job](https://spark.apache.org/docs/latest/job-scheduling.html), stages of the job, and tasks that make up each stage.</span></span>

<span data-ttu-id="25d86-190">Le visualizzazioni sono come segue:</span><span class="sxs-lookup"><span data-stu-id="25d86-190">The visualizations are as follows:</span></span>

### <a name="job-latency"></a><span data-ttu-id="25d86-191">Latenza del processo</span><span class="sxs-lookup"><span data-stu-id="25d86-191">Job latency</span></span>

<span data-ttu-id="25d86-192">Questa visualizzazione mostra la latenza di esecuzione per un processo, ovvero una visualizzazione generico sulle prestazioni complessive di un processo.</span><span class="sxs-lookup"><span data-stu-id="25d86-192">This visualization shows execution latency for a job, which is a coarse view on the overall performance of a job.</span></span> <span data-ttu-id="25d86-193">Visualizza la durata di esecuzione processo dall'inizio al completamento.</span><span class="sxs-lookup"><span data-stu-id="25d86-193">Displays the job execution duration from start to completion.</span></span> <span data-ttu-id="25d86-194">Si noti che ora di inizio del processo non sono quello utilizzato per l'ora di invio del processo.</span><span class="sxs-lookup"><span data-stu-id="25d86-194">Note that the job start time is not the same as the job submission time.</span></span> <span data-ttu-id="25d86-195">La latenza è rappresentata come percentili (del 10%, 30%, 50%, 90%) esecuzione dei processi indicizzato da ID cluster e ID dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="25d86-195">Latency is represented as percentiles (10%, 30%, 50%, 90%) of job execution indexed by cluster ID and application ID.</span></span>

### <a name="stage-latency"></a><span data-ttu-id="25d86-196">Latenza di fase</span><span class="sxs-lookup"><span data-stu-id="25d86-196">Stage latency</span></span>

<span data-ttu-id="25d86-197">La visualizzazione mostra la latenza di ogni fase per ogni cluster, per ogni applicazione e per ogni fase singola.</span><span class="sxs-lookup"><span data-stu-id="25d86-197">The visualization shows the latency of each stage per cluster, per application, and per individual stage.</span></span> <span data-ttu-id="25d86-198">Questa visualizzazione è utile per identificare una specifica fase che viene eseguita lentamente.</span><span class="sxs-lookup"><span data-stu-id="25d86-198">This visualization is useful for identifying a particular stage that is running slowly.</span></span>

### <a name="task-latency"></a><span data-ttu-id="25d86-199">Latenza di attività</span><span class="sxs-lookup"><span data-stu-id="25d86-199">Task latency</span></span>

<span data-ttu-id="25d86-200">Questa visualizzazione mostra la latenza di esecuzione attività.</span><span class="sxs-lookup"><span data-stu-id="25d86-200">This visualization shows task execution latency.</span></span> <span data-ttu-id="25d86-201">La latenza è rappresentata come un percentile dell'esecuzione di attività per ogni cluster, nome fase e dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="25d86-201">Latency is represented as a percentile of task execution per cluster, stage name, and application.</span></span>

### <a name="sum-task-execution-per-host"></a><span data-ttu-id="25d86-202">Esecuzione dell'attività Sum per ogni host</span><span class="sxs-lookup"><span data-stu-id="25d86-202">Sum Task Execution per host</span></span>

<span data-ttu-id="25d86-203">Questa visualizzazione mostra la somma della latenza di esecuzione di attività per ogni host in esecuzione in un cluster.</span><span class="sxs-lookup"><span data-stu-id="25d86-203">This visualization shows the sum of task execution latency per host running on a cluster.</span></span> <span data-ttu-id="25d86-204">Visualizzando la latenza di esecuzione attività per ogni host identifica gli host che hanno una quantità eccessiva latenza maggiore di attività complessivo rispetto degli altri host.</span><span class="sxs-lookup"><span data-stu-id="25d86-204">Viewing task execution latency per host identifies hosts that have much higher overall task latency than other hosts.</span></span> <span data-ttu-id="25d86-205">Questo può significare che le attività in modo inefficiente o non sono distribuite agli host.</span><span class="sxs-lookup"><span data-stu-id="25d86-205">This may mean that tasks have been inefficiently or unevenly distributed to hosts.</span></span>

### <a name="task-metrics"></a><span data-ttu-id="25d86-206">Metrica delle attività</span><span class="sxs-lookup"><span data-stu-id="25d86-206">Task metrics</span></span>

<span data-ttu-id="25d86-207">Questa visualizzazione mostra un set di metriche di esecuzione per l'esecuzione dell'attività specificata.</span><span class="sxs-lookup"><span data-stu-id="25d86-207">This visualization shows a set of the execution metrics for a given task's execution.</span></span> <span data-ttu-id="25d86-208">Queste metriche includono le dimensioni e durata di una sequenza casuale dei dati, la durata delle operazioni di serializzazione e deserializzazione e ad altri utenti.</span><span class="sxs-lookup"><span data-stu-id="25d86-208">These metrics include the size and duration of a data shuffle, duration of serialization and deserialization operations, and others.</span></span> <span data-ttu-id="25d86-209">Per il set completo di metriche, visualizzare la query di Log Analitica per il pannello.</span><span class="sxs-lookup"><span data-stu-id="25d86-209">For the full set of metrics, view the Log Analytics query for the panel.</span></span> <span data-ttu-id="25d86-210">Questa visualizzazione è utile per comprendere le operazioni che costituiscono un'attività e l'utilizzo delle risorse di identificazione di ogni operazione.</span><span class="sxs-lookup"><span data-stu-id="25d86-210">This visualization is useful for understanding the operations that make up a task and identifying resource consumption of each operation.</span></span> <span data-ttu-id="25d86-211">I picchi nel grafico rappresentano operazioni costose che dovrebbero essere esaminate.</span><span class="sxs-lookup"><span data-stu-id="25d86-211">Spikes in the graph represent costly operations that should be investigated.</span></span>

### <a name="cluster-throughput"></a><span data-ttu-id="25d86-212">Velocità effettiva di cluster</span><span class="sxs-lookup"><span data-stu-id="25d86-212">Cluster throughput</span></span>

<span data-ttu-id="25d86-213">Questa visualizzazione è una vista di alto livello degli elementi di lavoro indicizzato dal cluster e delle applicazioni per rappresentare la quantità di lavoro eseguito per ogni cluster e dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="25d86-213">This visualization is a high level view of work items indexed by cluster and application to represent the amount of work done per cluster and application.</span></span> <span data-ttu-id="25d86-214">Mostra il numero di processi, attività e fasi completate al cluster, l'applicazione e fase in incrementi di 1 minuto.</span><span class="sxs-lookup"><span data-stu-id="25d86-214">It shows the number of jobs, tasks, and stages completed per cluster, application, and stage in one minute increments.</span></span> 

### <a name="streaming-throughputlatency"></a><span data-ttu-id="25d86-215">Velocità effettiva/latenza streaming</span><span class="sxs-lookup"><span data-stu-id="25d86-215">Streaming Throughput/Latency</span></span>

<span data-ttu-id="25d86-216">Questa visualizzazione è correlata alle metriche di cui è associate a una query di streaming strutturata.</span><span class="sxs-lookup"><span data-stu-id="25d86-216">This visualization is related to the metrics associated with a structured streaming query.</span></span> <span data-ttu-id="25d86-217">I grafici visualizzano il numero di righe di input al secondo e il numero di righe elaborate al secondo.</span><span class="sxs-lookup"><span data-stu-id="25d86-217">The graphs shows the number of input rows per second and the number of rows processed per second.</span></span> <span data-ttu-id="25d86-218">La metrica di streaming è rappresentata anche per ogni applicazione.</span><span class="sxs-lookup"><span data-stu-id="25d86-218">The streaming metrics are also represented per application.</span></span> <span data-ttu-id="25d86-219">Queste metriche vengono inviate quando viene generato l'evento OnQueryProgress quando viene elaborata la query di streaming strutturata e rappresenta la visualizzazione streaming latenza come la quantità di tempo, espresso in millisecondi, impiegato per eseguire un batch di query.</span><span class="sxs-lookup"><span data-stu-id="25d86-219">These metrics are sent when the OnQueryProgress event is generated as the structured streaming query is processed and the visualization represents streaming latency as the amount of time, in milliseconds, taken to execute a query batch.</span></span>

### <a name="resource-consumption-per-executor"></a><span data-ttu-id="25d86-220">Utilizzo delle risorse per ogni executor</span><span class="sxs-lookup"><span data-stu-id="25d86-220">Resource consumption per executor</span></span>

<span data-ttu-id="25d86-221">Di seguito è riportato un set di visualizzazioni per la presentazione del dashboard il particolare tipo di risorsa e come vengono utilizzati per ogni executor in ogni cluster.</span><span class="sxs-lookup"><span data-stu-id="25d86-221">Next is a set of visualizations for the dashboard show the particular type of resource and how it is consumed per executor on each cluster.</span></span> <span data-ttu-id="25d86-222">Queste visualizzazioni consentono di identificare gli outlier in termini di consumo di risorse per ogni executor.</span><span class="sxs-lookup"><span data-stu-id="25d86-222">These visualizations help identify outliers in resource consumption per executor.</span></span> <span data-ttu-id="25d86-223">Ad esempio, se l'allocazione di lavoro per un esecutore specifico è sfasato e consumo di risorse verrà elevato in relazione agli altri executor in esecuzione nel cluster.</span><span class="sxs-lookup"><span data-stu-id="25d86-223">For example, if the work allocation for a particular executor is skewed, resource consumption will be elevated in relation to other executors running on the cluster.</span></span> <span data-ttu-id="25d86-224">Può essere identificato da picchi di utilizzo delle risorse per un executor.</span><span class="sxs-lookup"><span data-stu-id="25d86-224">This can be identified by spikes in the resource consumption for an executor.</span></span>

### <a name="executor-compute-time-metrics"></a><span data-ttu-id="25d86-225">Metriche di tempo di calcolo dell'executor</span><span class="sxs-lookup"><span data-stu-id="25d86-225">Executor compute time metrics</span></span>

<span data-ttu-id="25d86-226">Di seguito è riportato un set di visualizzazioni per il dashboard che mostra il rapporto tra executor serializzare tempo, deserializzare tempo, il tempo di CPU e l'ora di macchina virtuale Java per il tempo di calcolo complessivo dell'executor.</span><span class="sxs-lookup"><span data-stu-id="25d86-226">Next is a set of visualizations for the dashboard that show the ratio of executor serialize time, deserialize time, CPU time, and Java virtual machine time to overall executor compute time.</span></span> <span data-ttu-id="25d86-227">Ciò dimostra visivamente quanto ognuna di queste quattro metriche sono aggiunta come contributo al sistema complessivo elaborazione.</span><span class="sxs-lookup"><span data-stu-id="25d86-227">This demonstrates visually how much each of these four metrics are contributing to overall executor processing.</span></span>

### <a name="shuffle-metrics"></a><span data-ttu-id="25d86-228">Le metriche di riproduzione casuale</span><span class="sxs-lookup"><span data-stu-id="25d86-228">Shuffle metrics</span></span>

<span data-ttu-id="25d86-229">Il set finale di visualizzazioni Mostra i dati di riproduzione casuale le metriche associate a una query di streaming strutturata tra tutti gli executor.</span><span class="sxs-lookup"><span data-stu-id="25d86-229">The final set of visualizations show the data shuffle metrics associated with a structured streaming query across all executors.</span></span> <span data-ttu-id="25d86-230">Ad esempio shuffle byte letti e shuffle byte scritti, shuffle memoria e utilizzo dei dischi nelle query in cui viene usato il file system.</span><span class="sxs-lookup"><span data-stu-id="25d86-230">These include shuffle bytes read, shuffle bytes written, shuffle memory, and disk usage in queries where the file system is used.</span></span>

## <a name="next-steps"></a><span data-ttu-id="25d86-231">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="25d86-231">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="25d86-232">Risolvere i problemi di colli di bottiglia delle prestazioni</span><span class="sxs-lookup"><span data-stu-id="25d86-232">Troubleshoot performance bottlenecks</span></span>](./performance-troubleshooting.md)

<!-- links -->

[rm-cli]: /azure/azure-resource-manager/resource-group-template-deploy-cli
[sku]: /azure/templates/Microsoft.OperationalInsights/2015-11-01-preview/workspaces#sku-object