---
title: Inserimento e analisi di massa di feed di notizie in Azure
description: Creare una pipeline per l'inserimento e l'analisi di testo, immagini, sentiment e altri dati dei feed di notizie RSS usando solo i servizi Azure, tra cui Azure Cosmos DB e Servizi cognitivi di Azure.
author: njray
ms.date: 2/1/2019
ms.custom: azcat-ai, AI
social_image_url: /azure/architecture/example-scenario/ai/media/mass-ingestion-newsfeeds-architecture.png
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.openlocfilehash: e1bc636103753d474b545a6e3e9118b2ef302b34
ms.sourcegitcommit: ea97ac004c38c6b456794c1a8eef29f8d2b77d50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58489269"
---
# <a name="mass-ingestion-and-analysis-of-news-feeds-on-azure"></a><span data-ttu-id="73556-103">Inserimento e analisi di massa di feed di notizie in Azure</span><span class="sxs-lookup"><span data-stu-id="73556-103">Mass ingestion and analysis of news feeds on Azure</span></span>

<span data-ttu-id="73556-104">Questo scenario di esempio descrive una pipeline per l'inserimento di massa e quasi in tempo reale analisi di documenti usando i feed di notizie RSS pubblici.</span><span class="sxs-lookup"><span data-stu-id="73556-104">This example scenario describes a pipeline for mass ingestion and near real-time analysis of documents using public RSS news feeds.</span></span>  <span data-ttu-id="73556-105">Usa servizi cognitivi di Azure per offrire informazioni dettagliate utili tra cui rilevamento dei sentiment, riconoscimento facciale e traduzione testuale.</span><span class="sxs-lookup"><span data-stu-id="73556-105">It uses Azure Cognitive Services to offer useful insights including text translation, facial recognition, and sentiment detection.</span></span>

<span data-ttu-id="73556-106">Questo scenario è disponibili esempi per [inglese][english], [russo][russian], e [tedesco] [ german] notizie su feed, ma è possibile estenderlo facilmente da altri feed RSS.</span><span class="sxs-lookup"><span data-stu-id="73556-106">This scenario contains examples for [English][english], [Russian][russian], and [German][german] news feeds, but you can easily extend it to other RSS feeds.</span></span> <span data-ttu-id="73556-107">Per semplificare lo sviluppo, la raccolta dei dati, elaborazione e analisi sono basati interamente su servizi di Azure.</span><span class="sxs-lookup"><span data-stu-id="73556-107">For ease of deployment, the data collection, processing, and analysis are based entirely on Azure services.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="73556-108">Casi d'uso pertinenti</span><span class="sxs-lookup"><span data-stu-id="73556-108">Relevant use cases</span></span>

<span data-ttu-id="73556-109">Sebbene questo scenario si basa sull'elaborazione del feed RSS, è rilevante per qualsiasi documento, un sito Web o un articolo in cui è necessario:</span><span class="sxs-lookup"><span data-stu-id="73556-109">While this scenario is based on processing of RSS feeds, it's relevant to any document, website, or article where you would need to:</span></span>

* <span data-ttu-id="73556-110">Tradurre un testo per il linguaggio preferito.</span><span class="sxs-lookup"><span data-stu-id="73556-110">Translate any text to the language of choice.</span></span>
* <span data-ttu-id="73556-111">Trovare frasi chiave, entità e sentiment utente nel contenuto digitale.</span><span class="sxs-lookup"><span data-stu-id="73556-111">Find key phrases, entities, and user sentiment in digital content.</span></span>
* <span data-ttu-id="73556-112">Rilevare gli oggetti, il testo e punti di riferimento in immagini associate a un articolo digitale.</span><span class="sxs-lookup"><span data-stu-id="73556-112">Detect objects, text, and landmarks in images associated with a digital article.</span></span>
* <span data-ttu-id="73556-113">Rilevare le persone da loro genere ed età di qualsiasi immagine associato al contenuto digitale.</span><span class="sxs-lookup"><span data-stu-id="73556-113">Detect people by their gender and age in any image associated with digital content.</span></span>

## <a name="architecture"></a><span data-ttu-id="73556-114">Architettura</span><span class="sxs-lookup"><span data-stu-id="73556-114">Architecture</span></span>

![][architecture]

<span data-ttu-id="73556-115">Il flusso dei dati attraverso la soluzione avviene come segue:</span><span class="sxs-lookup"><span data-stu-id="73556-115">The data flows through the solution as follows:</span></span>

1. <span data-ttu-id="73556-116">Un feed di notizie RSS agisce come il generatore che ottiene dati da un documento o un articolo.</span><span class="sxs-lookup"><span data-stu-id="73556-116">An RSS news feed acts as the generator that obtains data from a document or article.</span></span> <span data-ttu-id="73556-117">Ad esempio, con un articolo, dati includono in genere un titolo, un riepilogo del corpo originale dell'elemento di notizie e talvolta di immagini.</span><span class="sxs-lookup"><span data-stu-id="73556-117">For example, with an article, data typically includes a title, a summary of the original body of the news item, and sometimes images.</span></span>

2. <span data-ttu-id="73556-118">Un generatore o l'inserimento di un processo inserisce l'articolo e tutte le immagini associate in un Azure Cosmos DB [Collection][collection].</span><span class="sxs-lookup"><span data-stu-id="73556-118">A generator or ingestion process inserts the article and any associated images into an Azure Cosmos DB [Collection][collection].</span></span>

3. <span data-ttu-id="73556-119">Una notifica attiva una funzione di inserimento in funzioni di Azure che archivia il testo dell'articolo in COSMOS DB e le immagini di articolo (se presente) in archiviazione Blob di Azure.</span><span class="sxs-lookup"><span data-stu-id="73556-119">A notification triggers an ingest function in Azure Functions that stores the article text in CosmosDB and the article images (if any) in Azure Blob Storage.</span></span>  <span data-ttu-id="73556-120">L'articolo viene quindi passato alla coda successiva.</span><span class="sxs-lookup"><span data-stu-id="73556-120">The article is then passed to the next queue.</span></span>

4. <span data-ttu-id="73556-121">Una funzione translate viene attivata dall'evento della coda.</span><span class="sxs-lookup"><span data-stu-id="73556-121">A translate function is triggered by the queue event.</span></span> <span data-ttu-id="73556-122">Usa il [tradurre API traduzione testuale] [ translate-text] di servizi cognitivi di Azure per rilevare la lingua, tradurre se necessario e raccogliere i sentimenti, frasi chiave e le entità dal corpo e il titolo.</span><span class="sxs-lookup"><span data-stu-id="73556-122">It uses the [Translate Text API][translate-text] of Azure Cognitive Services to detect the language, translate if necessary, and collect the sentiment, key phrases, and entities from the body and the title.</span></span> <span data-ttu-id="73556-123">Passa quindi l'articolo alla coda successiva.</span><span class="sxs-lookup"><span data-stu-id="73556-123">Then it passes the article to the next queue.</span></span>

5. <span data-ttu-id="73556-124">Una funzione di rilevamento viene attivata dall'articolo in coda.</span><span class="sxs-lookup"><span data-stu-id="73556-124">A detect function is triggered from the queued article.</span></span> <span data-ttu-id="73556-125">Usa il [visione artificiale] [ vision] servizio per rilevare gli oggetti, riferimenti e parole scritte nell'immagine associato, quindi passa l'articolo alla coda successiva.</span><span class="sxs-lookup"><span data-stu-id="73556-125">It uses the [Computer Vision][vision] service to detect objects, landmarks, and written words in the associated image, then passes the article to the next queue.</span></span>

6. <span data-ttu-id="73556-126">Viene attivata una funzione di volti viene attivato dall'articolo in coda.</span><span class="sxs-lookup"><span data-stu-id="73556-126">A face function is triggered is triggered from the queued article.</span></span> <span data-ttu-id="73556-127">Usa il [Azure l'API viso] [ face] servizio per rilevare i volti per sesso ed età nell'immagine associato, quindi passa l'articolo alla coda successiva.</span><span class="sxs-lookup"><span data-stu-id="73556-127">It uses the [Azure Face API][face] service to detect faces for gender and age in the associated image, then passes the article to the next queue.</span></span>

7. <span data-ttu-id="73556-128">Dopo aver complete tutte le funzioni, la funzione di cui inviare la notifica viene attivata.</span><span class="sxs-lookup"><span data-stu-id="73556-128">When all functions are complete, the notify function is triggered.</span></span> <span data-ttu-id="73556-129">Carica il record elaborati per l'articolo e li esegue l'analisi dei risultati desiderati.</span><span class="sxs-lookup"><span data-stu-id="73556-129">It loads the processed records for the article and scans them for any results you want.</span></span> <span data-ttu-id="73556-130">Se viene trovato, viene contrassegnato il contenuto e viene inviata una notifica al sistema di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="73556-130">If found, the content is flagged and a notification is sent to the system of your choice.</span></span>

<span data-ttu-id="73556-131">A ogni passaggio di elaborazione, la funzione scrive i risultati in Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="73556-131">At each processing step, the function writes the results to Azure Cosmos DB.</span></span> <span data-ttu-id="73556-132">In definitiva, i dati possono essere usati come desiderato.</span><span class="sxs-lookup"><span data-stu-id="73556-132">Ultimately, the data can be used as desired.</span></span> <span data-ttu-id="73556-133">Ad esempio, è possibile usarlo per migliorare i processi di business, individuare i nuovi clienti o identificare i problemi di soddisfazione dei clienti.</span><span class="sxs-lookup"><span data-stu-id="73556-133">For example, you can use it to enhance business processes, locate new customers, or identify customer satisfaction issues.</span></span>

### <a name="components"></a><span data-ttu-id="73556-134">Componenti</span><span class="sxs-lookup"><span data-stu-id="73556-134">Components</span></span>

<span data-ttu-id="73556-135">In questo esempio viene usato il seguente elenco di componenti di Azure.</span><span class="sxs-lookup"><span data-stu-id="73556-135">The following list of Azure components is used in this example.</span></span>

* <span data-ttu-id="73556-136">[Archiviazione di Azure] [ storage] viene utilizzato per i file video associati a un articolo e l'immagine non elaborata.</span><span class="sxs-lookup"><span data-stu-id="73556-136">[Azure Storage][storage] is used to hold raw image and video files associated with an article.</span></span> <span data-ttu-id="73556-137">Un account di archiviazione secondario viene creato con il servizio App di Azure e viene usato per ospitare il codice della funzione di Azure e log.</span><span class="sxs-lookup"><span data-stu-id="73556-137">A secondary storage account is created with Azure App Service and is used to host the Azure Function code and logs.</span></span>

* <span data-ttu-id="73556-138">[Azure Cosmos DB] [ cosmos-db] l'articolo contiene testo, immagini e video, informazioni di rilevamento.</span><span class="sxs-lookup"><span data-stu-id="73556-138">[Azure Cosmos DB][cosmos-db] holds article text, image, and video tracking information.</span></span> <span data-ttu-id="73556-139">I risultati delle operazioni di servizi cognitivi vengono anche archiviati qui.</span><span class="sxs-lookup"><span data-stu-id="73556-139">The results of the Cognitive Services steps are also stored here.</span></span>

* <span data-ttu-id="73556-140">[Funzioni di Azure] [ functions] esegue il codice della funzione usato per rispondere ai messaggi in coda e trasformare il contenuto in ingresso.</span><span class="sxs-lookup"><span data-stu-id="73556-140">[Azure Functions][functions] executes the function code used to respond to queue messages and transform the incoming content.</span></span> <span data-ttu-id="73556-141">[Servizio App di Azure] [ aas] ospita il codice della funzione ed elabora i record in modo seriale.</span><span class="sxs-lookup"><span data-stu-id="73556-141">[Azure App Service][aas] hosts the function code and processes the records serially.</span></span> <span data-ttu-id="73556-142">Questo scenario include cinque funzioni: Inserire, trasformare, oggetto, viso, di rilevare e inviare una notifica.</span><span class="sxs-lookup"><span data-stu-id="73556-142">This scenario includes five functions: Ingest, Transform, Detect Object, Face, and Notify.</span></span>

* <span data-ttu-id="73556-143">[Il Bus di servizio di Azure] [ service-bus] ospita le code del Bus di servizio di Azure usate dalle funzioni.</span><span class="sxs-lookup"><span data-stu-id="73556-143">[Azure Service Bus][service-bus] hosts the Azure Service Bus queues used by the functions.</span></span>

* <span data-ttu-id="73556-144">[Servizi cognitivi di Azure] [ acs] offre l'intelligenza artificiale per la pipeline in base alle implementazioni del [visione artificiale] [ vision] servizio [viso API][face], e [Traduci testo] [ translate-text] servizio di traduzione.</span><span class="sxs-lookup"><span data-stu-id="73556-144">[Azure Cognitive Services][acs] provides the AI for the pipeline based on implementations of the [Computer Vision][vision] service, [Face API][face], and [Translate Text][translate-text] machine translation service.</span></span>

* <span data-ttu-id="73556-145">[Azure Application Insights] [ aai] fornisce analitica che consentono di diagnosticare i problemi e comprendere la funzionalità dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="73556-145">[Azure Application Insights][aai] provides analytics to help you diagnose issues and to understand functionality of your application.</span></span>

### <a name="alternatives"></a><span data-ttu-id="73556-146">Alternative</span><span class="sxs-lookup"><span data-stu-id="73556-146">Alternatives</span></span>

* <span data-ttu-id="73556-147">Invece di usare un modello basato su notifica della coda e funzioni di Azure, usare un altro modello per questo flusso di dati.</span><span class="sxs-lookup"><span data-stu-id="73556-147">Instead of using a pattern based on queue notification and Azure Functions, use another pattern for this data flow.</span></span> <span data-ttu-id="73556-148">Ad esempio, [gli argomenti del Bus di servizio di Azure] [ topics] può essere utilizzato per le varie parti dell'articolo in parallelo anziché l'elaborazione seriale eseguito in questo esempio i processi.</span><span class="sxs-lookup"><span data-stu-id="73556-148">For example, [Azure Service Bus Topics][topics] can be used to processes the various parts of the article in parallel as opposed to the serial processing done in this example.</span></span> <span data-ttu-id="73556-149">Per altre informazioni, confrontare [code e argomenti][queues-topics].</span><span class="sxs-lookup"><span data-stu-id="73556-149">For more information, compare [queues and topics][queues-topics].</span></span>

* <span data-ttu-id="73556-150">Uso [App per la logica di Azure] [ logic-app] per implementare il codice della funzione e implementare, ad esempio il blocco a livello di record [Redlock] [ redlock] (necessario per paralleli l'elaborazione fino al Azure Cosmos DB supporta [parziale di documenti sono stati aggiornati][partial]).</span><span class="sxs-lookup"><span data-stu-id="73556-150">Use [Azure Logic App][logic-app] to implement the function code and implement record-level locking such as [Redlock][redlock] (needed for parallel processing until Azure Cosmos DB supports [partial document updates][partial]).</span></span> <span data-ttu-id="73556-151">Per altre informazioni, [confrontare funzioni e App per la logica][compare].</span><span class="sxs-lookup"><span data-stu-id="73556-151">For more information, [compare Functions and Logic Apps][compare].</span></span>

* <span data-ttu-id="73556-152">Implementare questa architettura usando i componenti di intelligenza artificiale personalizzati anziché limitarsi a servizi di Azure.</span><span class="sxs-lookup"><span data-stu-id="73556-152">Implement this architecture using customized AI components rather than existing Azure services.</span></span> <span data-ttu-id="73556-153">Ad esempio, estendere la pipeline usando un modello personalizzato che consente di rilevare determinate persone in un'immagine invece il numero di persone generico, sesso e i dati raccolti in questo esempio l'età.</span><span class="sxs-lookup"><span data-stu-id="73556-153">For example, extend the pipeline using a customized model that detects certain people in an image as opposed to the generic people count, gender, and age data collected in this example.</span></span> <span data-ttu-id="73556-154">Per usare apprendimento automatico personalizzati o modelli di intelligenza artificiale con questa architettura, compilare i modelli come endpoint RESTful in modo che possono essere chiamati da funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="73556-154">To use customized machine learning or AI models with this architecture, build the models as RESTful endpoints so they can be called from Azure Functions.</span></span>

* <span data-ttu-id="73556-155">Utilizzare un diverso meccanismo di input anziché feed RSS.</span><span class="sxs-lookup"><span data-stu-id="73556-155">Use a different input mechanism instead of RSS feeds.</span></span> <span data-ttu-id="73556-156">Usare più generatori o i processi di inserimento di feed di Azure Cosmos DB e archiviazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="73556-156">Use multiple generators or ingestion processes to feed Azure Cosmos DB and Azure Storage.</span></span>

## <a name="considerations"></a><span data-ttu-id="73556-157">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="73556-157">Considerations</span></span>

<span data-ttu-id="73556-158">Per semplicità, questo scenario di esempio Usa solo alcune API disponibili e i servizi da servizi cognitivi di Azure.</span><span class="sxs-lookup"><span data-stu-id="73556-158">For simplicity, this example scenario uses only a few of the available APIs and services from Azure Cognitive Services.</span></span> <span data-ttu-id="73556-159">Ad esempio, testo nelle immagini può essere analizzato con il [API traduzione testuale Analitica][text-analytics].</span><span class="sxs-lookup"><span data-stu-id="73556-159">For example, text in images can be analyzed using the [Text Analytics API][text-analytics].</span></span> <span data-ttu-id="73556-160">Lingua di destinazione in questo scenario si presuppone che sia in lingua inglese, ma è possibile modificare l'input a uno [lingua supportata] [ language] di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="73556-160">The target language in this scenario is assumed to be English, but you can change the input to any [supported language][language] of your choice.</span></span>

### <a name="scalability"></a><span data-ttu-id="73556-161">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="73556-161">Scalability</span></span>

<span data-ttu-id="73556-162">Funzioni di Azure la scalabilità dipende il [piano di hosting] [ plan] è utilizzare.</span><span class="sxs-lookup"><span data-stu-id="73556-162">Azure Functions scaling depends on the [hosting plan][plan] you use.</span></span> <span data-ttu-id="73556-163">Questa soluzione presuppone una [piano a consumo][plan-c], in cui calcolo power viene allocato automaticamente per le funzioni quando necessario.</span><span class="sxs-lookup"><span data-stu-id="73556-163">This solution assumes a [Consumption plan][plan-c], in which compute power is automatically allocated to the functions when required.</span></span> <span data-ttu-id="73556-164">Si paga solo quando le funzioni sono in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="73556-164">You pay only when your functions are running.</span></span> <span data-ttu-id="73556-165">Un'altra opzione consiste nell'usare un [servizio App di Azure] [ plan-aas] piano, che consente di applicare la scalabilità tra livelli per allocare una quantità di risorse diversa.</span><span class="sxs-lookup"><span data-stu-id="73556-165">Another option is to use an [Azure App Service][plan-aas] plan, which allows you to scale between tiers to allocate a different amount of resources.</span></span>

<span data-ttu-id="73556-166">Con Azure Cosmos DB, la chiave consiste nel distribuire il carico di lavoro più o meno in modo uniforme tra un numero sufficientemente elevato di [chiavi di partizione][keys].</span><span class="sxs-lookup"><span data-stu-id="73556-166">With Azure Cosmos DB, the key is to distribute your workload roughly evenly among a sufficiently large number of [partition keys][keys].</span></span> <span data-ttu-id="73556-167">Non esiste un limite alla quantità totale di dati che può archiviare un contenitore o alla quantità totale di [velocità effettiva] [ throughput] in grado di supportare un contenitore.</span><span class="sxs-lookup"><span data-stu-id="73556-167">There's no limit to the total amount of data that a container can store or to the total amount of [throughput][throughput] that a container can support.</span></span>

### <a name="management-and-logging"></a><span data-ttu-id="73556-168">Gestione e registrazione</span><span class="sxs-lookup"><span data-stu-id="73556-168">Management and logging</span></span>

<span data-ttu-id="73556-169">Questa soluzione Usa [Application Insights] [ aai] per raccogliere informazioni su prestazioni e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="73556-169">This solution uses [Application Insights][aai] to collect performance and logging information.</span></span> <span data-ttu-id="73556-170">Con la distribuzione nello stesso gruppo di risorse come gli altri servizi necessari per questa distribuzione viene creata un'istanza di Application Insights.</span><span class="sxs-lookup"><span data-stu-id="73556-170">An instance of Application Insights is created with the deployment in the same resource group as the other services needed for this deployment.</span></span>

<span data-ttu-id="73556-171">Per visualizzare i log generati dalla soluzione:</span><span class="sxs-lookup"><span data-stu-id="73556-171">To view the logs generated by the solution:</span></span>

1. <span data-ttu-id="73556-172">Passare a [portale di Azure] [ portal] e passare al gruppo di risorse creato per la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="73556-172">Go to [Azure portal][portal] and navigate to the resource group created for the deployment.</span></span>

2. <span data-ttu-id="73556-173">Scegliere il **Application Insights** istanza.</span><span class="sxs-lookup"><span data-stu-id="73556-173">Click the **Application Insights** instance.</span></span>

3. <span data-ttu-id="73556-174">Dal **Application Insights** sezione, passare alla **indagare\\ricerca** e cercare i dati.</span><span class="sxs-lookup"><span data-stu-id="73556-174">From the **Application Insights** section, navigate to **Investigate\\Search** and search the data.</span></span>

### <a name="security"></a><span data-ttu-id="73556-175">Security</span><span class="sxs-lookup"><span data-stu-id="73556-175">Security</span></span>

<span data-ttu-id="73556-176">Azure Cosmos DB Usa una connessione protetta e una firma di accesso condiviso tramite C\# SDK forniti da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="73556-176">Azure Cosmos DB uses a secured connection and shared access signature through the C\# SDK provided by Microsoft.</span></span> <span data-ttu-id="73556-177">Sono disponibili altre aree area accessibile pubblicamente.</span><span class="sxs-lookup"><span data-stu-id="73556-177">There are no other externally facing surface areas.</span></span> <span data-ttu-id="73556-178">Altre informazioni sulla sicurezza [procedure consigliate] [ db-practices] per Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="73556-178">Learn more about security [best practices][db-practices] for Azure Cosmos DB.</span></span>

## <a name="pricing"></a><span data-ttu-id="73556-179">Prezzi</span><span class="sxs-lookup"><span data-stu-id="73556-179">Pricing</span></span>

<span data-ttu-id="73556-180">Il costo stimato giornaliero per mantenere questa distribuzione disponibile è di circa \$20 degli Stati Uniti senza dati passano attraverso il sistema.</span><span class="sxs-lookup"><span data-stu-id="73556-180">The estimated daily cost to keep this deployment available is approximately \$20 U.S. with no data moving through the system.</span></span>

<span data-ttu-id="73556-181">Azure Cosmos DB è potente, ma comporta il maggior [costo] [ db-cost] in questa distribuzione.</span><span class="sxs-lookup"><span data-stu-id="73556-181">Azure Cosmos DB is powerful but incurs the greatest [cost][db-cost] in this deployment.</span></span> <span data-ttu-id="73556-182">È possibile usare un'altra soluzione di archiviazione per il refactoring del codice di funzioni di Azure fornito.</span><span class="sxs-lookup"><span data-stu-id="73556-182">You can use another storage solution by refactoring the Azure Functions code provided.</span></span>

<span data-ttu-id="73556-183">Prezzi per funzioni di Azure varia a seconda del [piano] [ function-plan] è in esecuzione in.</span><span class="sxs-lookup"><span data-stu-id="73556-183">Pricing for Azure Functions varies depending on the [plan][function-plan] it runs in.</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="73556-184">Distribuire lo scenario</span><span class="sxs-lookup"><span data-stu-id="73556-184">Deploy the scenario</span></span>

> [!NOTE]
> <span data-ttu-id="73556-185">È necessario un account Azure esistente.</span><span class="sxs-lookup"><span data-stu-id="73556-185">You must have an existing Azure account.</span></span> <span data-ttu-id="73556-186">Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free] prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="73556-186">If you don't have an Azure subscription, create a [free account][free] before you begin.</span></span>

<span data-ttu-id="73556-187">È disponibile in tutto il codice per questo scenario il [GitHub] [ github] repository.</span><span class="sxs-lookup"><span data-stu-id="73556-187">All the code for this scenario is available in the [GitHub][github] repository.</span></span> <span data-ttu-id="73556-188">Questo repository contiene il codice sorgente utilizzato per compilare l'applicazione di generazione che la pipeline per questa demo di feed.</span><span class="sxs-lookup"><span data-stu-id="73556-188">This repository contains the source code used to build the generator application that feeds the pipeline for this demo.</span></span>

[architecture]: ./media/mass-ingestion-newsfeeds-architecture.png
[aai]: /azure/azure-monitor/app/app-insights-overview
[aas]: https://azure.microsoft.com/try/app-service/
[acs]: https://azure.microsoft.com/services/cognitive-services/directory/
[collection]: /rest/api/cosmos-db/collections
[compare]: /azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs#compare-azure-functions-and-azure-logic-apps
[cosmos-db]: /azure/cosmos-db/introduction
[db-cost]: https://azure.microsoft.com/pricing/details/cosmos-db/
[db-practices]: /azure/cosmos-db/database-security
[db-collection]: /azure/cosmos-db/databases-containers-items
[english]: https://www.nasa.gov/rss/dyn/breaking_news.rss
[face]: /azure/cognitive-services/face/overview
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[functions]: /azure/azure-functions/functions-overview
[function-plan]: /azure/azure-functions/functions-scale
[german]: http://www.bamf.de/SiteGlobals/Functions/RSS/DE/Feed/RSSNewsfeed_Meldungen
[github]: https://github.com/Azure/cognitive-services
[keys]: /azure/cosmos-db/partition-data
[language]: /azure/cognitive-services/translator/reference/v3-0-languages
[logic-app]: /azure/logic-apps/logic-apps-overview
[queues-topics]: /azure/service-bus-messaging/service-bus-queues-topics-subscriptions
[partial]: https://feedback.azure.com/forums/263030-azure-cosmos-db/suggestions/6693091-be-able-to-do-partial-updates-on-document
[plan]: /azure/azure-functions/functions-scale
[plan-aas]: /azure/azure-functions/functions-scale#app-service-plan
[plan-c]: /azure/azure-functions/functions-scale#consumption-plan
[portal]: http://portal.azure.com
[redlock]: https://redis.io/topics/distlock
[russian]: http://government.ru/all/rss/
[service-bus]: /azure/service-bus-messaging/
[storage]: /azure/storage/common/storage-account-overview 
[throughput]: /azure/cosmos-db/scaling-throughput
[topics]: /azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions
[text-analytics]: /azure/cognitive-services/text-analytics/
[translate-text]: /azure/cognitive-services/translator/translator-info-overview
[vision]: /azure/cognitive-services/computer-vision/home
