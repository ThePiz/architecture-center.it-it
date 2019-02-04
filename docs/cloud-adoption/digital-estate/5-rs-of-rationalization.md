---
title: Le 5 "R" della razionalizzazione
titleSuffix: Enterprise Cloud Adoption
description: Vengono descritte le opzioni disponibili durante la razionalizzazione di un digital estate
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 06058967e6ffcd9e3554a46c67144f72fb19078f
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908580"
---
# <a name="enterprise-cloud-adoption-the-5-rs-of-rationalization"></a><span data-ttu-id="4f251-103">Adozione del cloud nell'organizzazione: Le 5 "R" della razionalizzazione</span><span class="sxs-lookup"><span data-stu-id="4f251-103">Enterprise Cloud Adoption: The 5 Rs of rationalization</span></span>

<span data-ttu-id="4f251-104">La razionalizzazione del cloud è il processo di valutazione degli asset per determinare l'approccio migliore per la migrazione o la modernizzazione di ogni asset nel cloud.</span><span class="sxs-lookup"><span data-stu-id="4f251-104">Cloud Rationalization is the process of evaluating assets to determine the best approach to migrating or modernizing each asset in the cloud.</span></span> <span data-ttu-id="4f251-105">Per altre informazioni sul processo di razionalizzazione, consultare [Che cos'è un digital estate?](overview.md)</span><span class="sxs-lookup"><span data-stu-id="4f251-105">For more information about the process of rationalization, see [What is a digital estate?](overview.md)</span></span>

<span data-ttu-id="4f251-106">Le 5 "R" della razionalizzazione elencate di seguito descrivono le opzioni più comuni per la razionalizzazione.</span><span class="sxs-lookup"><span data-stu-id="4f251-106">The "5 Rs of rationalization" listed here describe the most common options for rationalization.</span></span>

## <a name="rehost"></a><span data-ttu-id="4f251-107">Rehosting</span><span class="sxs-lookup"><span data-stu-id="4f251-107">Rehost</span></span>

<span data-ttu-id="4f251-108">Nota anche come "lift and shift", un'attività di rehost consente di spostare l'asset dello stato corrente nel provider di servizi cloud scelto, con modifiche minime all'architettura complessiva.</span><span class="sxs-lookup"><span data-stu-id="4f251-108">Also known as "lift and shift," a rehost effort moves the current state asset to the chosen cloud provider, with minimal change to overall architecture.</span></span>

<span data-ttu-id="4f251-109">I fattori comuni possono includere:</span><span class="sxs-lookup"><span data-stu-id="4f251-109">Common drivers could include:</span></span>

* <span data-ttu-id="4f251-110">Riduzione delle spese in conto capitale</span><span class="sxs-lookup"><span data-stu-id="4f251-110">Reduce CapEx</span></span>
* <span data-ttu-id="4f251-111">Spazio libero nel data center</span><span class="sxs-lookup"><span data-stu-id="4f251-111">Free up datacenter space</span></span>
* <span data-ttu-id="4f251-112">Rapido ritorno sugli investimenti nel cloud</span><span class="sxs-lookup"><span data-stu-id="4f251-112">Quick cloud ROI</span></span>

<span data-ttu-id="4f251-113">Fattori di analisi quantitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-113">Quantitative analysis factors:</span></span>

* <span data-ttu-id="4f251-114">Dimensioni della macchina virtuale (CPU, memoria, archiviazione)</span><span class="sxs-lookup"><span data-stu-id="4f251-114">VM size (CPU, memory, storage)</span></span>
* <span data-ttu-id="4f251-115">Dipendenze (traffico di rete)</span><span class="sxs-lookup"><span data-stu-id="4f251-115">Dependencies (network traffic)</span></span>
* <span data-ttu-id="4f251-116">Compatibilità degli asset</span><span class="sxs-lookup"><span data-stu-id="4f251-116">Asset compatibility</span></span>

<span data-ttu-id="4f251-117">Fattori di analisi qualitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-117">Qualitative analysis factors:</span></span>

* <span data-ttu-id="4f251-118">Tolleranza alle modifiche</span><span class="sxs-lookup"><span data-stu-id="4f251-118">Tolerance for change</span></span>
* <span data-ttu-id="4f251-119">Priorità aziendali</span><span class="sxs-lookup"><span data-stu-id="4f251-119">Business priorities</span></span>
* <span data-ttu-id="4f251-120">Eventi aziendali critici</span><span class="sxs-lookup"><span data-stu-id="4f251-120">Critical business events</span></span>
* <span data-ttu-id="4f251-121">Dipendenze dei processi</span><span class="sxs-lookup"><span data-stu-id="4f251-121">Process dependencies</span></span>

## <a name="refactor"></a><span data-ttu-id="4f251-122">Refactoring</span><span class="sxs-lookup"><span data-stu-id="4f251-122">Refactor</span></span>

<span data-ttu-id="4f251-123">Le opzioni di piattaforma distribuita come un servizio (PaaS) possono ridurre i costi operativi associati a molte applicazioni.</span><span class="sxs-lookup"><span data-stu-id="4f251-123">Platform as a Service (PaaS) options can reduce operational costs associated with many applications.</span></span> <span data-ttu-id="4f251-124">Può essere opportuno effettuare un leggero refactoring di un'applicazione per adattare un modello basato su PaaS.</span><span class="sxs-lookup"><span data-stu-id="4f251-124">It can be prudent to slightly refactor an application to fit a PaaS based model.</span></span>

<span data-ttu-id="4f251-125">Questo fa riferimento anche al refactoring del codice nel processo di sviluppo dell'applicazione per consentire a quest'ultima di mantenere nuove opportunità commerciali.</span><span class="sxs-lookup"><span data-stu-id="4f251-125">Refactor also refers to the application development process of refactoring code to allow an application to deliver on new business opportunities.</span></span>

<span data-ttu-id="4f251-126">I fattori comuni possono includere:</span><span class="sxs-lookup"><span data-stu-id="4f251-126">Common drivers could include:</span></span>

* <span data-ttu-id="4f251-127">Aggiornamenti più veloci e più brevi</span><span class="sxs-lookup"><span data-stu-id="4f251-127">Faster, shorter, updates</span></span>
* <span data-ttu-id="4f251-128">Portabilità del codice</span><span class="sxs-lookup"><span data-stu-id="4f251-128">Code portability</span></span>
* <span data-ttu-id="4f251-129">Maggiore efficienza del cloud (risorse, velocità, costo)</span><span class="sxs-lookup"><span data-stu-id="4f251-129">Greater cloud efficiency (resources, speed, cost)</span></span>

<span data-ttu-id="4f251-130">Fattori di analisi quantitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-130">Quantitative analysis factors:</span></span>

* <span data-ttu-id="4f251-131">Dimensioni degli asset dell'applicazione (CPU, memoria, archiviazione)</span><span class="sxs-lookup"><span data-stu-id="4f251-131">Application asset size (CPU, memory, storage)</span></span>
* <span data-ttu-id="4f251-132">Dipendenze (traffico di rete)</span><span class="sxs-lookup"><span data-stu-id="4f251-132">Dependencies (network traffic)</span></span>
* <span data-ttu-id="4f251-133">Traffico utente (visualizzazioni pagina, tempo su una pagina, tempo di caricamento)</span><span class="sxs-lookup"><span data-stu-id="4f251-133">User traffic (page views, time on page, load time)</span></span>
* <span data-ttu-id="4f251-134">Piattaforma di sviluppo (linguaggi, piattaforme di dati, servizi di livello intermedio)</span><span class="sxs-lookup"><span data-stu-id="4f251-134">Development platform (languages, data platform, middle tier services)</span></span>

<span data-ttu-id="4f251-135">Fattori di analisi qualitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-135">Qualitative analysis factors:</span></span>

* <span data-ttu-id="4f251-136">Investimenti aziendali continui</span><span class="sxs-lookup"><span data-stu-id="4f251-136">Continued business investments</span></span>
* <span data-ttu-id="4f251-137">Aumento delle opzioni/sequenze temporali</span><span class="sxs-lookup"><span data-stu-id="4f251-137">Bursting options/timelines</span></span>
* <span data-ttu-id="4f251-138">Dipendenze dei processi aziendali</span><span class="sxs-lookup"><span data-stu-id="4f251-138">Business process dependencies</span></span>

## <a name="rearchitect"></a><span data-ttu-id="4f251-139">Riprogettazione</span><span class="sxs-lookup"><span data-stu-id="4f251-139">Rearchitect</span></span>

<span data-ttu-id="4f251-140">Alcune applicazioni obsolete non sono compatibili con i provider di servizi cloud a causa delle decisioni di progettazione prese quando sono state compilate.</span><span class="sxs-lookup"><span data-stu-id="4f251-140">Some aging applications aren't compatible with cloud providers because of the architectural decisions made when the application was built.</span></span> <span data-ttu-id="4f251-141">In questi casi, l'applicazione potrebbe dover essere riprogettata prima della trasformazione.</span><span class="sxs-lookup"><span data-stu-id="4f251-141">In these cases, the application may need to be rearchitected prior to transformation.</span></span>

<span data-ttu-id="4f251-142">In altri casi, le applicazioni compatibili con il cloud, ma senza vantaggi cloud-native, possono essere efficienti in termini di costi e operatività se la soluzione viene riprogettata per essere un'applicazione nativa del cloud.</span><span class="sxs-lookup"><span data-stu-id="4f251-142">In other cases, applications that are cloud compatible, but not cloud native benefits, may produce cost efficiencies and operational efficiencies by rearchitecting the solution to be a cloud native application.</span></span>

<span data-ttu-id="4f251-143">I fattori comuni possono includere:</span><span class="sxs-lookup"><span data-stu-id="4f251-143">Common drivers could include:</span></span>

* <span data-ttu-id="4f251-144">Scalabilità e flessibilità dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="4f251-144">Application scale and agility</span></span>
* <span data-ttu-id="4f251-145">Adozione più semplice di nuove funzionalità del cloud</span><span class="sxs-lookup"><span data-stu-id="4f251-145">Easier adoption of new cloud capabilities</span></span>
* <span data-ttu-id="4f251-146">Miscela di stack di tecnologie</span><span class="sxs-lookup"><span data-stu-id="4f251-146">Mix of technology stacks</span></span>

<span data-ttu-id="4f251-147">Fattori di analisi quantitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-147">Quantitative analysis factors:</span></span>

* <span data-ttu-id="4f251-148">Dimensioni degli asset dell'applicazione (CPU, memoria, archiviazione)</span><span class="sxs-lookup"><span data-stu-id="4f251-148">Application asset size (CPU, memory, storage)</span></span>
* <span data-ttu-id="4f251-149">Dipendenze (traffico di rete)</span><span class="sxs-lookup"><span data-stu-id="4f251-149">Dependencies (network traffic)</span></span>
* <span data-ttu-id="4f251-150">Traffico utente (visualizzazioni pagina, tempo su una pagina, tempo di caricamento)</span><span class="sxs-lookup"><span data-stu-id="4f251-150">User traffic (page views, time on page, load time)</span></span>
* <span data-ttu-id="4f251-151">Piattaforma di sviluppo (linguaggi, piattaforme di dati, servizi di livello intermedio)</span><span class="sxs-lookup"><span data-stu-id="4f251-151">Development platform (languages, data platform, middle tier services)</span></span>

<span data-ttu-id="4f251-152">Fattori di analisi qualitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-152">Qualitative analysis factors:</span></span>

* <span data-ttu-id="4f251-153">Investimenti aziendali in crescita</span><span class="sxs-lookup"><span data-stu-id="4f251-153">Growing business investments</span></span>
* <span data-ttu-id="4f251-154">Costi operativi</span><span class="sxs-lookup"><span data-stu-id="4f251-154">Operational costs</span></span>
* <span data-ttu-id="4f251-155">Potenziali cicli di feedback e investimenti per DevOps</span><span class="sxs-lookup"><span data-stu-id="4f251-155">Potential feedback loops and DevOps investments</span></span>

## <a name="rebuild"></a><span data-ttu-id="4f251-156">Ricompilazione</span><span class="sxs-lookup"><span data-stu-id="4f251-156">Rebuild</span></span>

<span data-ttu-id="4f251-157">In alcuni scenari, il delta da risolvere per trasferire un'applicazione può essere troppo grande per giustificare un ulteriore investimento.</span><span class="sxs-lookup"><span data-stu-id="4f251-157">In some scenarios, the delta that must be overcome to carry forward an application can be too large to justify further investment.</span></span> <span data-ttu-id="4f251-158">Questo vale soprattutto per le applicazioni usate per soddisfare le esigenze dell'azienda, ma non più supportate o non più allineate con la modalità attuale di esecuzione dei processi.</span><span class="sxs-lookup"><span data-stu-id="4f251-158">This is especially true for applications that used to meet the needs of the business, but are now unsupported or misaligned with how the business processes are executed today.</span></span> <span data-ttu-id="4f251-159">In questo caso, viene creata una nuova base di codice in linea con l'approccio [cloud nativo](https://azure.microsoft.com/overview/cloudnative/).</span><span class="sxs-lookup"><span data-stu-id="4f251-159">In this case, a new code base is created to align with a [cloud native](https://azure.microsoft.com/overview/cloudnative/) approach.</span></span>

<span data-ttu-id="4f251-160">I fattori comuni possono includere:</span><span class="sxs-lookup"><span data-stu-id="4f251-160">Common drivers could include:</span></span>

* <span data-ttu-id="4f251-161">Accelerazione dell'innovazione</span><span class="sxs-lookup"><span data-stu-id="4f251-161">Accelerate innovation</span></span>
* <span data-ttu-id="4f251-162">Compilazione delle app più veloce</span><span class="sxs-lookup"><span data-stu-id="4f251-162">Build apps faster</span></span>
* <span data-ttu-id="4f251-163">Riduzione dei costi operativi</span><span class="sxs-lookup"><span data-stu-id="4f251-163">Reduce operational cost</span></span>

<span data-ttu-id="4f251-164">Fattori di analisi quantitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-164">Quantitative analysis factors:</span></span>

* <span data-ttu-id="4f251-165">Dimensioni degli asset dell'applicazione (CPU, memoria, archiviazione)</span><span class="sxs-lookup"><span data-stu-id="4f251-165">Application asset size (CPU, memory, storage)</span></span>
* <span data-ttu-id="4f251-166">Dipendenze (traffico di rete)</span><span class="sxs-lookup"><span data-stu-id="4f251-166">Dependencies (network traffic)</span></span>
* <span data-ttu-id="4f251-167">Traffico utente (visualizzazioni pagina, tempo su una pagina, tempo di caricamento)</span><span class="sxs-lookup"><span data-stu-id="4f251-167">User traffic (page views, time on page, load time)</span></span>
* <span data-ttu-id="4f251-168">Piattaforma di sviluppo (linguaggi, piattaforme di dati, servizi di livello intermedio)</span><span class="sxs-lookup"><span data-stu-id="4f251-168">Development platform (languages, data platform, middle tier services)</span></span>

<span data-ttu-id="4f251-169">Fattori di analisi qualitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-169">Qualitative analysis Factors:</span></span>

* <span data-ttu-id="4f251-170">Calo di soddisfazione dell'utente finale</span><span class="sxs-lookup"><span data-stu-id="4f251-170">Declining end user satisfaction</span></span>
* <span data-ttu-id="4f251-171">Processi aziendali limitati per funzionalità</span><span class="sxs-lookup"><span data-stu-id="4f251-171">Business processes limited by functionality</span></span>
* <span data-ttu-id="4f251-172">Potenziali guadagni in termini di costi, esperienza o ricavi</span><span class="sxs-lookup"><span data-stu-id="4f251-172">Potential cost, experience, or revenue gains</span></span>

## <a name="replace"></a><span data-ttu-id="4f251-173">Replace</span><span class="sxs-lookup"><span data-stu-id="4f251-173">Replace</span></span>

<span data-ttu-id="4f251-174">Le soluzioni, in genere, sono implementate usando la tecnologia e l'approccio migliore possibile al momento.</span><span class="sxs-lookup"><span data-stu-id="4f251-174">Solutions are generally implemented using the best technology and approach available at the time.</span></span> <span data-ttu-id="4f251-175">In alcuni casi, le applicazioni di software come servizio (SaaS) possono soddisfare tutti i requisiti di funzionalità richiesti dall'applicazione ospitata.</span><span class="sxs-lookup"><span data-stu-id="4f251-175">In some cases, Software as a Service (SaaS) applications can meet all of the functionality required of the hosted application.</span></span> <span data-ttu-id="4f251-176">In questi scenari, si può destinare un carico di lavoro a una futura sostituzione, rimuovendolo con efficacia dall'attività di trasformazione.</span><span class="sxs-lookup"><span data-stu-id="4f251-176">In these scenarios, a workload could be slated for future replacement, effectively removing it from the transformation effort.</span></span>

<span data-ttu-id="4f251-177">I fattori comuni possono includere:</span><span class="sxs-lookup"><span data-stu-id="4f251-177">Common drivers could include:</span></span>

* <span data-ttu-id="4f251-178">Standardizzazione secondo le procedure consigliate del settore</span><span class="sxs-lookup"><span data-stu-id="4f251-178">Standardize around industry-best practices</span></span>
* <span data-ttu-id="4f251-179">Accelerazione dell'adozione di approcci basati su processi aziendali</span><span class="sxs-lookup"><span data-stu-id="4f251-179">Accelerate adoption of business process driven approaches</span></span>
* <span data-ttu-id="4f251-180">Riallocazione di investimenti di sviluppo in applicazioni che creano una differenziazione o vantaggi competitivi</span><span class="sxs-lookup"><span data-stu-id="4f251-180">Reallocate development investments into applications that create competitive differentiation or advantages</span></span>

<span data-ttu-id="4f251-181">Fattori di analisi quantitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-181">Quantitative analysis factors:</span></span>

* <span data-ttu-id="4f251-182">Riduzione dei costi operativi generali</span><span class="sxs-lookup"><span data-stu-id="4f251-182">General operating cost reductions</span></span>
* <span data-ttu-id="4f251-183">Dimensioni della macchina virtuale (CPU, memoria, archiviazione)</span><span class="sxs-lookup"><span data-stu-id="4f251-183">VM size (CPU, memory, storage)</span></span>
* <span data-ttu-id="4f251-184">Dipendenze (traffico di rete)</span><span class="sxs-lookup"><span data-stu-id="4f251-184">Dependencies (network traffic)</span></span>
* <span data-ttu-id="4f251-185">Asset da ritirare</span><span class="sxs-lookup"><span data-stu-id="4f251-185">Assets to be retired</span></span>

<span data-ttu-id="4f251-186">Fattori di analisi qualitativa:</span><span class="sxs-lookup"><span data-stu-id="4f251-186">Qualitative analysis factors:</span></span>

* <span data-ttu-id="4f251-187">Analisi costi-benefici dell'architettura attuale in confronto alla soluzione SaaS</span><span class="sxs-lookup"><span data-stu-id="4f251-187">Cost benefit analysis of current architecture vs SaaS solution</span></span>
* <span data-ttu-id="4f251-188">Mapping dei processi aziendali</span><span class="sxs-lookup"><span data-stu-id="4f251-188">Business process maps</span></span>
* <span data-ttu-id="4f251-189">Schemi di dati</span><span class="sxs-lookup"><span data-stu-id="4f251-189">Data schemas</span></span>
* <span data-ttu-id="4f251-190">Processi personalizzati o automatizzati</span><span class="sxs-lookup"><span data-stu-id="4f251-190">Custom or automated processes</span></span>

## <a name="next-steps"></a><span data-ttu-id="4f251-191">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="4f251-191">Next steps</span></span>

<span data-ttu-id="4f251-192">Nel complesso, queste 5 "R" della razionalizzazione sono applicabili a un digital estate per decisioni in materia di razionalizzazione relative allo stato futuro di ciascuna applicazione.</span><span class="sxs-lookup"><span data-stu-id="4f251-192">Collectively, these 5 Rs of Rationalization can be applied to a digital estate to make rationalization decisions regarding the future state of each application.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="4f251-193">Informazioni sul digital estate</span><span class="sxs-lookup"><span data-stu-id="4f251-193">What is a digital estate?</span></span>](overview.md)
