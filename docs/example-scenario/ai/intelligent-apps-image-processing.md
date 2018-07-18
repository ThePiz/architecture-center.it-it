---
title: 'App intelligenti: elaborazione di immagini in Azure'
description: Soluzione collaudata per integrare l'elaborazione di immagini nelle applicazioni Azure.
author: david-stanford
ms.date: 07/05/2018
ms.openlocfilehash: c5bfb9a929ddddda4336e1cbc8665a0b4d3bbe2c
ms.sourcegitcommit: 5d99b195388b7cabba383c49a81390ac48f86e8a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37891334"
---
# <a name="insurance-claim-image-classification-on-azure"></a><span data-ttu-id="27332-103">Classificazione delle immagini per richieste di indennizzo assicurativo in Azure</span><span class="sxs-lookup"><span data-stu-id="27332-103">Insurance claim image classification on Azure</span></span>

<span data-ttu-id="27332-104">Questo scenario di esempio è applicabile ad aziende che devono elaborare immagini.</span><span class="sxs-lookup"><span data-stu-id="27332-104">This example scenario is applicable for businesses that need to process images.</span></span>

<span data-ttu-id="27332-105">Le potenziali applicazioni includono la classificazione di immagini per un sito Web di abbigliamento, l'analisi di testo e immagini per richieste di indennizzo assicurativo e la comprensione di dati di telemetria provenienti da screenshot di giochi.</span><span class="sxs-lookup"><span data-stu-id="27332-105">Potential applications include classifying images for a fashion website, analyzing text and images for insurance claims, or understanding telemetry data from game screenshots.</span></span> <span data-ttu-id="27332-106">Generalmente le aziende devono sviluppare competenze nei modelli di apprendimento automatico, eseguire il training dei modelli e infine eseguire un processo personalizzato sulle immagini per ricavarne dati.</span><span class="sxs-lookup"><span data-stu-id="27332-106">Traditionally, companies would need to develop expertise in machine learning models, train the models, and finally run the images through their custom process to get the data out of the images.</span></span>

<span data-ttu-id="27332-107">Con servizi di Azure come l'API Visione artificiale e Funzioni di Azure, le aziende possono eliminare l'esigenza di gestire singoli server riducendo al tempo stesso i costi e sfruttando le competenze già sviluppate da Microsoft in relazione all'elaborazione di immagini con Servizi cognitivi.</span><span class="sxs-lookup"><span data-stu-id="27332-107">By using Azure services such as the Computer Vision API and Azure Functions, companies can eliminate the need to manage individual servers, while reducing costs and leveraging the expertise that Microsoft has already developed around processing images with Cognitive services.</span></span> <span data-ttu-id="27332-108">Questo scenario riguarda specificamente l'elaborazione di immagini.</span><span class="sxs-lookup"><span data-stu-id="27332-108">This scenario specifically addresses an image processing scenario.</span></span> <span data-ttu-id="27332-109">Per altre esigenze di intelligenza artificiale, prendere in considerazione l'intera famiglia di prodotti [Servizi cognitivi][cognitive-docs].</span><span class="sxs-lookup"><span data-stu-id="27332-109">If you have different AI needs, consider the full suite of [Cognitive Services][cognitive-docs].</span></span>

## <a name="potential-use-cases"></a><span data-ttu-id="27332-110">Potenziali casi d'uso</span><span class="sxs-lookup"><span data-stu-id="27332-110">Potential use cases</span></span>

<span data-ttu-id="27332-111">Prendere in considerazione questa soluzione per i casi d'uso seguenti:</span><span class="sxs-lookup"><span data-stu-id="27332-111">Consider this solution for the following use cases:</span></span>

* <span data-ttu-id="27332-112">Classificare le immagini in un sito Web di abbigliamento.</span><span class="sxs-lookup"><span data-stu-id="27332-112">Classify images on a fashion website.</span></span>
* <span data-ttu-id="27332-113">Classificare le immagini per richieste di indennizzo assicurativo.</span><span class="sxs-lookup"><span data-stu-id="27332-113">Classify images for insurance claims</span></span>
* <span data-ttu-id="27332-114">Classificare i dati di telemetria provenienti da screenshot di giochi.</span><span class="sxs-lookup"><span data-stu-id="27332-114">Classify telemetry data from screenshots of games.</span></span>

## <a name="architecture"></a><span data-ttu-id="27332-115">Architettura</span><span class="sxs-lookup"><span data-stu-id="27332-115">Architecture</span></span>

![Architettura di app intelligenti: visione artificiale][architecture-computer-vision]

<span data-ttu-id="27332-117">Questa soluzione include i componenti back-end di un'applicazione Web o per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="27332-117">This solution covers the back-end components of a web or mobile application.</span></span> <span data-ttu-id="27332-118">Il flusso dei dati attraverso la soluzione avviene come segue:</span><span class="sxs-lookup"><span data-stu-id="27332-118">Data flows through the solution as follows:</span></span>

1. <span data-ttu-id="27332-119">Funzioni di Azure funge da livello API.</span><span class="sxs-lookup"><span data-stu-id="27332-119">Azure Functions acts as the API layer.</span></span> <span data-ttu-id="27332-120">Le API consentono all'applicazione di caricare immagini e recuperare dati da Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="27332-120">These APIs enable the application to upload images and retrieve data from Cosmos DB.</span></span>

2. <span data-ttu-id="27332-121">Un'immagine caricata tramite una chiamata API viene archiviata nell'archivio BLOB.</span><span class="sxs-lookup"><span data-stu-id="27332-121">When an image is uploaded via an API call, it's stored in Blob storage.</span></span>

3. <span data-ttu-id="27332-122">L'aggiunta di nuovi file all'archivio BLOB attiva una notifica di Griglia di eventi che verrà inviata a una funzione di Azure.</span><span class="sxs-lookup"><span data-stu-id="27332-122">Adding new files to Blob storage triggers an EventGrid notification to be sent to an Azure Function.</span></span>

4. <span data-ttu-id="27332-123">Funzioni di Azure invia un collegamento al file appena caricato all'API Visione artificiale per l'analisi.</span><span class="sxs-lookup"><span data-stu-id="27332-123">Azure Functions sends a link to the newly uploaded file to the Computer Vision API to analyze.</span></span>

5. <span data-ttu-id="27332-124">I dati restituiti dall'API Visione artificiale vengono immessi da Funzioni di Azure in Cosmos DB in modo da rendere persistenti i risultati dell'analisi insieme ai metadati dell'immagine.</span><span class="sxs-lookup"><span data-stu-id="27332-124">Once the data has been returned from the Computer Vision API, Azure Functions makes an entry in Cosmos DB to persist the results of the analysis alongside the image metadata.</span></span>

### <a name="components"></a><span data-ttu-id="27332-125">Componenti</span><span class="sxs-lookup"><span data-stu-id="27332-125">Components</span></span>

* <span data-ttu-id="27332-126">L'[API Visione artificiale][computer-vision-docs], appartenente alla famiglia di prodotti Servizi cognitivi, viene usata per recuperare informazioni su ogni immagine.</span><span class="sxs-lookup"><span data-stu-id="27332-126">[Computer Vision API][computer-vision-docs] is part of the Cognitive Services suite and is used to retrieve information about each image.</span></span>

* <span data-ttu-id="27332-127">[Funzioni di Azure][functions-docs] fornisce l'API back-end per l'applicazione Web, nonché l'elaborazione di eventi per le immagini caricate.</span><span class="sxs-lookup"><span data-stu-id="27332-127">[Azure Functions][functions-docs] provides the backend API for the web application, as well as the event processing for uploaded images.</span></span>

* <span data-ttu-id="27332-128">[Griglia di eventi][eventgrid-docs] attiva un evento quando una nuova immagine viene caricata nell'archivio BLOB.</span><span class="sxs-lookup"><span data-stu-id="27332-128">[Event Grid][eventgrid-docs] triggers an event when a new image is uploaded to blob storage.</span></span> <span data-ttu-id="27332-129">L'immagine viene quindi elaborata con Funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="27332-129">The image is then processed with Azure functions.</span></span>

* <span data-ttu-id="27332-130">Nell'[archivio BLOB][storage-docs] vengono archiviati tutti i file di immagine caricati nell'applicazione Web, nonché i file statici da essa utilizzati.</span><span class="sxs-lookup"><span data-stu-id="27332-130">[Blob Storage][storage-docs] stores all of the image files that are uploaded into the web application, as well any static files that the web application consumes.</span></span>

* <span data-ttu-id="27332-131">In [Cosmos DB][cosmos-docs] vengono archiviati i metadati relativi a ogni immagine caricata, inclusi i risultati dell'elaborazione dell'API Visione artificiale.</span><span class="sxs-lookup"><span data-stu-id="27332-131">[Cosmos DB][cosmos-docs] stores metadata about each image that is uploaded, including the results of the processing from Computer Vision API.</span></span>

## <a name="alternatives"></a><span data-ttu-id="27332-132">Alternative</span><span class="sxs-lookup"><span data-stu-id="27332-132">Alternatives</span></span>

* <span data-ttu-id="27332-133">[Servizio visione artificiale personalizzato][custom-vision-docs].</span><span class="sxs-lookup"><span data-stu-id="27332-133">[Custom Vision Service][custom-vision-docs].</span></span> <span data-ttu-id="27332-134">L'API Visione artificiale restituisce un set di [categorie basate sulla tassonomia][cv-categories].</span><span class="sxs-lookup"><span data-stu-id="27332-134">The Computer Vision API returns a set of [taxonomy-based categories][cv-categories].</span></span> <span data-ttu-id="27332-135">Se è necessario elaborare informazioni non restituite dall'API Visione artificiale, prendere in considerazione il Servizio visione artificiale personalizzato, che consente di creare classificatori di immagini personalizzati.</span><span class="sxs-lookup"><span data-stu-id="27332-135">If you need to process information that isn't returned by the Computer Vision API, consider the Custom Vision Service, which lets you build custom image classifiers.</span></span>

* <span data-ttu-id="27332-136">[Ricerca di Azure][azure-search-docs].</span><span class="sxs-lookup"><span data-stu-id="27332-136">[Azure Search][azure-search-docs].</span></span> <span data-ttu-id="27332-137">Se il caso d'uso include l'esecuzione di query sui metadati per trovare le immagini che soddisfano criteri specifici, valutare la possibilità di usare Ricerca di Azure.</span><span class="sxs-lookup"><span data-stu-id="27332-137">If your use case involves querying the metadata to find images that meet specific criteria, consider using Azure Search.</span></span> <span data-ttu-id="27332-138">La funzionalità di [ricerca cognitiva][cognitive-search], attualmente in anteprima, integra perfettamente questo flusso di lavoro.</span><span class="sxs-lookup"><span data-stu-id="27332-138">Currently in preview, [Cognitive search][cognitive-search] seamlessly integrates this workflow.</span></span>

## <a name="considerations"></a><span data-ttu-id="27332-139">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="27332-139">Considerations</span></span>

### <a name="scalability"></a><span data-ttu-id="27332-140">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="27332-140">Scalability</span></span>

<span data-ttu-id="27332-141">Per la maggior parte, tutti i componenti di questa soluzione sono servizi gestiti con scalabilità automatica.</span><span class="sxs-lookup"><span data-stu-id="27332-141">For the most part all of the components of this solution are managed services that will automatically scale.</span></span> <span data-ttu-id="27332-142">Esistono alcune eccezioni significative. Funzioni di Azure ha un limite massimo di 200 istanze.</span><span class="sxs-lookup"><span data-stu-id="27332-142">A couple notable exceptions: Azure Functions has a limit of a maximum of 200 instances.</span></span> <span data-ttu-id="27332-143">Se è necessaria una scalabilità superiore, prendere in considerazione più aree o piani app.</span><span class="sxs-lookup"><span data-stu-id="27332-143">If you need to scale beyond, consider multiple regions or app plans.</span></span>

<span data-ttu-id="27332-144">Cosmos DB non offre scalabilità automatica in termini di unità richiesta (UR) sottoposte a provisioning.</span><span class="sxs-lookup"><span data-stu-id="27332-144">Cosmos DB doesn’t auto-scale in terms of provisioned request units (RUs).</span></span>  <span data-ttu-id="27332-145">Per indicazioni su come stimare i propri requisiti, vedere l'articolo relativo alle [unità richiesta][request-units] nella documentazione.</span><span class="sxs-lookup"><span data-stu-id="27332-145">For guidance on estimating your requirements see [request units][request-units] in our documentation.</span></span> <span data-ttu-id="27332-146">Per sfruttare appieno la scalabilità in Cosmos DB, è consigliabile esaminare anche le [chiavi di partizione][partition-key].</span><span class="sxs-lookup"><span data-stu-id="27332-146">To fully take advantage of the scaling in Cosmos DB you should also take a look at [partition keys][partition-key].</span></span>

<span data-ttu-id="27332-147">I database NoSQL spesso privilegiano la disponibilità, la scalabilità e la partizione rispetto alla coerenza (nel senso del teorema CAP).</span><span class="sxs-lookup"><span data-stu-id="27332-147">NoSQL databases frequently trade consistency (in the sense of the CAP theorem) for availability, scalability and partition.</span></span>  <span data-ttu-id="27332-148">Nel caso dei modelli di dati chiave-valore usati in questo scenario, tuttavia,la coerenza delle transazione è raramente necessaria perché le operazioni sono per la maggior parte atomiche per definizione.</span><span class="sxs-lookup"><span data-stu-id="27332-148">However, in the case of key-value data models which is used in this scenario, transaction consistency is rarely needed as most operations are by definition atomic.</span></span> <span data-ttu-id="27332-149">Altre indicazioni per [scegliere l'archivio dati corretto](../../guide/technology-choices/data-store-overview.md) sono disponibili in Centro architetture.</span><span class="sxs-lookup"><span data-stu-id="27332-149">Additional guidance to [Choose the right data store](../../guide/technology-choices/data-store-overview.md) is available in the architecture center.</span></span>

<span data-ttu-id="27332-150">Per indicazioni generali sulla progettazione di soluzioni scalabili, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.</span><span class="sxs-lookup"><span data-stu-id="27332-150">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="27332-151">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="27332-151">Security</span></span>

<span data-ttu-id="27332-152">[Identità del servizio gestite][msi] vengono usate per offrire l'accesso ad altre risorse interne all'account e quindi assegnate a Funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="27332-152">[Managed service identities][msi] (MSI) are used to provide access to other resources internal to your account and then assigned to your Azure Functions.</span></span> <span data-ttu-id="27332-153">In tali identità consentire l'accesso solo alle risorse necessarie in modo da non esporre nient'altro alle funzioni e potenzialmente ai clienti.</span><span class="sxs-lookup"><span data-stu-id="27332-153">Only allow access to the requisite resources in those identities to ensure that nothing extra is exposed to your functions (and potentially to your customers).</span></span>  

<span data-ttu-id="27332-154">Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].</span><span class="sxs-lookup"><span data-stu-id="27332-154">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="27332-155">Resilienza</span><span class="sxs-lookup"><span data-stu-id="27332-155">Resiliency</span></span>

<span data-ttu-id="27332-156">Tutti i componenti della soluzione sono gestiti e quindi automaticamente resilienti a livello di area.</span><span class="sxs-lookup"><span data-stu-id="27332-156">All of the components in this solution are managed, so at a regional level they are all resilient automatically.</span></span> 

<span data-ttu-id="27332-157">Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].</span><span class="sxs-lookup"><span data-stu-id="27332-157">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="27332-158">Prezzi</span><span class="sxs-lookup"><span data-stu-id="27332-158">Pricing</span></span>

<span data-ttu-id="27332-159">Per esaminare il costo di esecuzione della soluzione, nel calcolatore dei costi sono preconfigurati tutti i servizi.</span><span class="sxs-lookup"><span data-stu-id="27332-159">To explore the cost of running this solution, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="27332-160">Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base al traffico previsto.</span><span class="sxs-lookup"><span data-stu-id="27332-160">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="27332-161">Sono stati definiti tre profili di costo di esempio in base alla quantità di traffico, supponendo che tutte le immagini siano di 100 KB.</span><span class="sxs-lookup"><span data-stu-id="27332-161">We have provided three sample cost profiles based on amount of traffic (we assume all images are 100kb in size):</span></span>

* <span data-ttu-id="27332-162">[Small][pricing]: correlato all'elaborazione di meno di 5000 immagini al mese.</span><span class="sxs-lookup"><span data-stu-id="27332-162">[Small][pricing]: this correlates to processing &lt; 5000 images a month.</span></span>
* <span data-ttu-id="27332-163">[Medium][medium-pricing]: correlato all'elaborazione di 500.000 immagini al mese.</span><span class="sxs-lookup"><span data-stu-id="27332-163">[Medium][medium-pricing]: this correlates to processing 500,000 images a month.</span></span>
* <span data-ttu-id="27332-164">[Large][large-pricing]: correlato all'elaborazione di 50 milioni di immagini al mese.</span><span class="sxs-lookup"><span data-stu-id="27332-164">[Large][large-pricing]: this correlates to processing 50 million images a month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="27332-165">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="27332-165">Related Resources</span></span>

<span data-ttu-id="27332-166">Per un percorso di apprendimento guidato per questa soluzione, vedere [Build a serverless web app in Azure][serverless] (Creare un'app Web senza server in Azure).</span><span class="sxs-lookup"><span data-stu-id="27332-166">For a guided learning path of this solution, see [Build a serverless web app in Azure][serverless].</span></span>  

<span data-ttu-id="27332-167">Prima dell'inclusione in un ambiente di produzione, esaminare le [procedure consigliate][functions-best-practices] per Funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="27332-167">Before putting this in a production environment, review the Azure Functions [best practices][functions-best-practices].</span></span>

<!-- links -->
[pricing]: https://azure.com/e/f9b59d238b43423683db73f4a31dc380
[medium-pricing]: https://azure.com/e/7c7fc474db344b87aae93bc29ae27108
[large-pricing]: https://azure.com/e/cbadbca30f8640d6a061f8457a74ba7d
[functions-docs]: /azure/azure-functions/
[computer-vision-docs]: /azure/cognitive-services/computer-vision/home
[storage-docs]: /azure/storage/
[azure-search-docs]: /azure/search/
[cognitive-search]: /azure/search/cognitive-search-concept-intro
[architecture-computer-vision]: ./media/architecture-computer-vision.png
[serverless]: /azure/functions/tutorial-static-website-serverless-api-with-database
[cosmos-docs]: /azure/cosmos-db/
[eventgrid-docs]: /azure/event-grid/
[cognitive-docs]: /azure/#pivot=products&panel=ai
[custom-vision-docs]: /azure/cognitive-services/Custom-Vision-Service/home
[cv-categories]: /azure/cognitive-services/computer-vision/home#the-86-category-concept
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[functions-best-practices]: /azure/azure-functions/functions-best-practices
[msi]: /azure/app-service/app-service-managed-service-identity
[request-units]: /azure/cosmos-db/request-units
[partition-key]: /azure/cosmos-db/partition-data