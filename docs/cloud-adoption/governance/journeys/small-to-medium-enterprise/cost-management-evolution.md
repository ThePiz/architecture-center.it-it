---
title: 'CAF: Piccole e medie imprese - Evoluzione di Gestione costi  '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: Spiegazione di piccole e medie imprese - Evoluzione di Gestione costi
author: BrianBlanchard
ms.openlocfilehash: ca070bfca3efbf9548faa8cf28a1adffefd4a33a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901287"
---
# <a name="small-to-medium-enterprise-cost-management-evolution"></a><span data-ttu-id="cc86f-103">Piccole e medie imprese: Evoluzione di Gestione costi</span><span class="sxs-lookup"><span data-stu-id="cc86f-103">Small-to-medium enterprise: Cost Management evolution</span></span>

<span data-ttu-id="cc86f-104">L'articolo approfondisce la presentazione dello scenario aggiungendo controlli per l'MVP della governance.</span><span class="sxs-lookup"><span data-stu-id="cc86f-104">This article evolves the narrative by adding cost controls to the governance MVP.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="cc86f-105">Evoluzione dello scenario</span><span class="sxs-lookup"><span data-stu-id="cc86f-105">Evolution of the narrative</span></span>

<span data-ttu-id="cc86f-106">L'adozione del cloud ha superato l'indicatore di tolleranza dei costi definito nell'MVP della governance.</span><span class="sxs-lookup"><span data-stu-id="cc86f-106">Adoption has grown beyond the cost tolerance indicator defined in the governance MVP.</span></span> <span data-ttu-id="cc86f-107">Ciò è positivo, dato che corrisponde alle migrazioni dal data center "DR".</span><span class="sxs-lookup"><span data-stu-id="cc86f-107">This is a good thing, as it corresponds with migrations from the "DR" datacenter.</span></span> <span data-ttu-id="cc86f-108">L'aumento della spesa ora giustifica un investimento di tempo da parte del team di Governance cloud.</span><span class="sxs-lookup"><span data-stu-id="cc86f-108">The increase in spending now justifies an investment of time from the Cloud Governance team.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="cc86f-109">Evoluzione dello stato attuale</span><span class="sxs-lookup"><span data-stu-id="cc86f-109">Evolution of the current state</span></span>

<span data-ttu-id="cc86f-110">Nella fase precedente di questo scenario, IT ha ritirato il 100% dei data center DR.</span><span class="sxs-lookup"><span data-stu-id="cc86f-110">In the previous phase of this narrative, IT had retired 100% of the DR datacenter.</span></span> <span data-ttu-id="cc86f-111">Lo sviluppo dell'applicazione e i team di business intelligence (BI) sono pronti per il traffico di produzione.</span><span class="sxs-lookup"><span data-stu-id="cc86f-111">The application development and BI teams were ready for production traffic.</span></span>

<span data-ttu-id="cc86f-112">Da allora, sono avvenuti alcuni cambiamenti che influiranno sulla governance:</span><span class="sxs-lookup"><span data-stu-id="cc86f-112">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="cc86f-113">il team di migrazione ha iniziato la migrazione delle macchine virtuali all'esterno del data center di produzione.</span><span class="sxs-lookup"><span data-stu-id="cc86f-113">The migration team has begun migrating VMs out of the production datacenter.</span></span>
- <span data-ttu-id="cc86f-114">I team di sviluppo dell'applicazione eseguono attivamente il push di applicazioni di produzione al cloud, tramite le pipeline CI/CD.</span><span class="sxs-lookup"><span data-stu-id="cc86f-114">The application development teams is actively pushing production applications to the cloud through CI/CD pipelines.</span></span> <span data-ttu-id="cc86f-115">Tali applicazioni si possono ridimensionare in modo reattivo in base alle esigenze dell'utente.</span><span class="sxs-lookup"><span data-stu-id="cc86f-115">Those applications can reactively scale with user demands.</span></span>
- <span data-ttu-id="cc86f-116">Il team di business intelligence all'interno di IT ha recapitato un numero di strumenti di analisi predittiva nel cloud.</span><span class="sxs-lookup"><span data-stu-id="cc86f-116">The business intelligence team within IT has delivered a number of predictive analytics tools in the cloud.</span></span> <span data-ttu-id="cc86f-117">i volumi di dati aggregati nel cloud sono in continua crescita.</span><span class="sxs-lookup"><span data-stu-id="cc86f-117">the volumes of data aggregated in the cloud continues to grow.</span></span>
- <span data-ttu-id="cc86f-118">Tale crescita supporta i risultati aziendali di esecuzione del commit.</span><span class="sxs-lookup"><span data-stu-id="cc86f-118">All of this growth supports committed business outcomes.</span></span> <span data-ttu-id="cc86f-119">Tuttavia, i costi hanno iniziato ad aumentare velocemente.</span><span class="sxs-lookup"><span data-stu-id="cc86f-119">However, costs have begun to mushroom.</span></span> <span data-ttu-id="cc86f-120">I budget previsti stanno crescendo più rapidamente di quanto ci si aspettava.</span><span class="sxs-lookup"><span data-stu-id="cc86f-120">Projected budgets are growing faster than expected.</span></span> <span data-ttu-id="cc86f-121">Il Direttore finanziario (CFO) deve migliorare gli approcci per i costi di gestione.</span><span class="sxs-lookup"><span data-stu-id="cc86f-121">The CFO needs improved approaches to managing costs.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="cc86f-122">Evoluzione dello stato futuro</span><span class="sxs-lookup"><span data-stu-id="cc86f-122">Evolution of the future state</span></span>

<span data-ttu-id="cc86f-123">Il monitoraggio dei costi e la creazione dei report deve essere aggiunta alla soluzione cloud.</span><span class="sxs-lookup"><span data-stu-id="cc86f-123">Cost monitoring and reporting is to be added to the cloud solution.</span></span> <span data-ttu-id="cc86f-124">IT svolge ancora la funzione di fornitore di servizi di accesso a terze parti.</span><span class="sxs-lookup"><span data-stu-id="cc86f-124">IT is still serving as a cost clearing house.</span></span> <span data-ttu-id="cc86f-125">Ciò significa che il pagamento per i servizi cloud, continua a provenire dall'approvvigionamento IT.</span><span class="sxs-lookup"><span data-stu-id="cc86f-125">This means that payment for cloud services continues to come from IT procurement.</span></span> <span data-ttu-id="cc86f-126">Tuttavia, i report dovrebbero correlare direttamente le spese operative alle funzioni che comportano l'addebito di costi del cloud.</span><span class="sxs-lookup"><span data-stu-id="cc86f-126">However, reporting should tie direct operational expenses to the functions that are consuming the cloud costs.</span></span> <span data-ttu-id="cc86f-127">Questo modello è definito come modello "Mostra indietro" nella contabilità del cloud.</span><span class="sxs-lookup"><span data-stu-id="cc86f-127">This model is referred to as "Show Back" model to cloud accounting.</span></span>

<span data-ttu-id="cc86f-128">I cambiamenti che riguardano lo stato attuale e quello futuro espongono a nuovi rischi che richiedono nuove definizioni di criteri.</span><span class="sxs-lookup"><span data-stu-id="cc86f-128">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="cc86f-129">Evoluzione dei rischi tangibili</span><span class="sxs-lookup"><span data-stu-id="cc86f-129">Evolution of tangible risks</span></span>

<span data-ttu-id="cc86f-130">**Controllo del budget**: esiste un rischio intrinseco che le capacità self-service comportino costi eccessivi e imprevisti sulla nuova piattaforma.</span><span class="sxs-lookup"><span data-stu-id="cc86f-130">**Budget control**: There is an inherent risk that self-service capabilities will result in excessive and unexpected costs on the new platform.</span></span> <span data-ttu-id="cc86f-131">I processi di governance per i costi di monitoraggio e i costi di migrazione in corso devono poter garantire un costante allineamento con il budget pianificato.</span><span class="sxs-lookup"><span data-stu-id="cc86f-131">Governance processes for monitoring costs and mitigating ongoing cost risks must be in place to ensure continued alignment with the planned budget.</span></span>

<span data-ttu-id="cc86f-132">Questo rischio aziendale può comportare alcuni rischi tecnici:</span><span class="sxs-lookup"><span data-stu-id="cc86f-132">This business risk can be expanded into a few technical risks:</span></span>

- <span data-ttu-id="cc86f-133">i costi effettivi potrebbero superare il piano.</span><span class="sxs-lookup"><span data-stu-id="cc86f-133">Actual costs might exceed the plan.</span></span>
- <span data-ttu-id="cc86f-134">Le condizioni aziendali possono cambiare.</span><span class="sxs-lookup"><span data-stu-id="cc86f-134">Business conditions change.</span></span> <span data-ttu-id="cc86f-135">In tal caso, è possibile che una funzione aziendale debba usare più servizi cloud del previsto, portando ad anomalie di spesa.</span><span class="sxs-lookup"><span data-stu-id="cc86f-135">When they do, there will be cases when a business function needs to consume more cloud services than expected, leading to spending anomalies.</span></span> <span data-ttu-id="cc86f-136">Sussiste il rischio che questa spesa aggiuntiva venga considerata in eccedenza, a differenza di una rettifica necessaria per il piano.</span><span class="sxs-lookup"><span data-stu-id="cc86f-136">There is a risk that this extra spending will be considered overages, as opposed to a necessary adjustment to the plan.</span></span>
- <span data-ttu-id="cc86f-137">I sistemi potrebbero avere un provisioning eccessivo, che causerebbe la spesa in eccesso.</span><span class="sxs-lookup"><span data-stu-id="cc86f-137">Systems could be overprovisioned, resulting in excess spending.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="cc86f-138">Evoluzione delle istruzioni dei criteri</span><span class="sxs-lookup"><span data-stu-id="cc86f-138">Evolution of the policy statements</span></span>

<span data-ttu-id="cc86f-139">Le modifiche ai criteri seguenti contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="cc86f-139">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="cc86f-140">Tutti i costi del cloud dovrebbero essere monitorati rispetto al piano dal team di governance del cloud con frequenza settimanale.</span><span class="sxs-lookup"><span data-stu-id="cc86f-140">All cloud costs should be monitored against plan on a weekly basis by the governance team.</span></span> <span data-ttu-id="cc86f-141">I report sulle deviazioni tra i costi del cloud e il piano stabilito devono essere condivisi con i responsabili IT e l'amministrazione con frequenza mensile.</span><span class="sxs-lookup"><span data-stu-id="cc86f-141">Reporting on deviations between cloud costs and plan is to be shared with IT leadership and finance monthly.</span></span> <span data-ttu-id="cc86f-142">Tutti i costi del cloud e gli aggiornamenti del piano devono essere controllati ogni mese con i responsabili IT e l'amministrazione.</span><span class="sxs-lookup"><span data-stu-id="cc86f-142">All cloud costs and plan updates should be reviewed with IT leadership and finance monthly.</span></span>
2. <span data-ttu-id="cc86f-143">Tutti i costi devono essere allocati in una funzione aziendale ai fini della rendicontazione.</span><span class="sxs-lookup"><span data-stu-id="cc86f-143">All costs must be allocated to a business function for accountability purposes.</span></span>
3. <span data-ttu-id="cc86f-144">Gli asset del cloud devono essere monitorati costantemente per eventuali opportunità di ottimizzazione.</span><span class="sxs-lookup"><span data-stu-id="cc86f-144">Cloud assets should be continually monitored for optimization opportunities.</span></span>
4. <span data-ttu-id="cc86f-145">Gli strumenti di Governance cloud devono limitare le opzioni delle dimensioni degli asset a un elenco approvato di configurazioni.</span><span class="sxs-lookup"><span data-stu-id="cc86f-145">Cloud Governance tooling must limit Asset sizing options to an approved list of configurations.</span></span> <span data-ttu-id="cc86f-146">Gli strumenti devono assicurare che tutti gli asset siano individuabili e tracciati dalla soluzione di monitoraggio dei costi.</span><span class="sxs-lookup"><span data-stu-id="cc86f-146">The tooling must ensure that all assets are discoverable and tracked by the cost monitoring solution.</span></span>
5. <span data-ttu-id="cc86f-147">Durante la pianificazione della distribuzione, tutte le risorse richieste del cloud associate all'hosting dei flussi di lavoro di produzione dovrebbero essere documentate.</span><span class="sxs-lookup"><span data-stu-id="cc86f-147">During deployment planning, any required cloud resources associated with the hosting of production workloads should be documented.</span></span> <span data-ttu-id="cc86f-148">Questa documentazione aiuterà a ridefinire i budget e a preparare altre soluzioni di automazione per impedire l'uso delle opzioni più costose.</span><span class="sxs-lookup"><span data-stu-id="cc86f-148">This documentation will help refine budgets and prepare additional automation to prevent the use of more expensive options.</span></span> <span data-ttu-id="cc86f-149">Durante questo processo si dovrebbero prendere in considerazione diversi strumenti di sconto offerti dal provider di servizi cloud, come istanze riservate o riduzioni dei costi di licenza.</span><span class="sxs-lookup"><span data-stu-id="cc86f-149">During this process consideration should be given to different discounting tools offered by the cloud provider, such as reserved instances or license cost reductions.</span></span>
6. <span data-ttu-id="cc86f-150">A tutti i proprietari di applicazioni è richiesta la partecipazione con training eseguito su procedure consigliate, al fine di ottimizzare i carichi di lavoro per controllare al meglio i costi del cloud.</span><span class="sxs-lookup"><span data-stu-id="cc86f-150">All application owners are required to attend trained on practices for optimizing workloads to better control cloud costs.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="cc86f-151">Evoluzione delle procedure consigliate</span><span class="sxs-lookup"><span data-stu-id="cc86f-151">Evolution of the best practices</span></span>

<span data-ttu-id="cc86f-152">Questa sezione dell'articolo sviluppa l'MVP della governance, in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure.</span><span class="sxs-lookup"><span data-stu-id="cc86f-152">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="cc86f-153">Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove istruzioni dei criteri aziendali.</span><span class="sxs-lookup"><span data-stu-id="cc86f-153">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="cc86f-154">Implementare Gestione costi di Azure</span><span class="sxs-lookup"><span data-stu-id="cc86f-154">Implement Azure Cost Management</span></span>
    1. <span data-ttu-id="cc86f-155">Stabilire l'ambito corretto dell'accesso in linea con il criterio di sottoscrizione e la disciplina di Coerenza delle risorse.</span><span class="sxs-lookup"><span data-stu-id="cc86f-155">Establish the right scope of access to align with the subscription pattern and the Resource Consistency discipline.</span></span> <span data-ttu-id="cc86f-156">Supponendo che sia allineato all'MVP della governance definito negli articoli precedenti, ciò richiede l'accesso **Ambito dell'account di registrazione** per il team di Governance cloud incaricato della generazione di report di alto livello.</span><span class="sxs-lookup"><span data-stu-id="cc86f-156">Assuming alignment with the governance MVP defined in prior articles, this requires **Enrollment Account Scope** access for the Cloud Governance team executing on high-level reporting.</span></span> <span data-ttu-id="cc86f-157">Altri team al di fuori della governance possono richiedere l'accesso **Ambito del gruppo di risorse**.</span><span class="sxs-lookup"><span data-stu-id="cc86f-157">Additional teams outside of governance may require **Resource Group Scope** access.</span></span>
    2. <span data-ttu-id="cc86f-158">Stabilire un budget in Gestione costi di Azure.</span><span class="sxs-lookup"><span data-stu-id="cc86f-158">Establish a budget in Azure Cost Management.</span></span>
    3. <span data-ttu-id="cc86f-159">Esaminare e implementare gli elementi consigliati.</span><span class="sxs-lookup"><span data-stu-id="cc86f-159">Review and act on initial recommendations.</span></span> <span data-ttu-id="cc86f-160">Disporre di un processo ricorrente per supportare la creazione di report.</span><span class="sxs-lookup"><span data-stu-id="cc86f-160">Have a recurring process to support reporting.</span></span>
    4. <span data-ttu-id="cc86f-161">Configurare ed eseguire Creazione di report di Gestione costi di Azure, sia nella fase iniziale sia con frequenza periodica.</span><span class="sxs-lookup"><span data-stu-id="cc86f-161">Configure and execute Azure Cost Management Reporting, both initial and recurring.</span></span>
2. <span data-ttu-id="cc86f-162">Aggiornare i Criteri di Azure</span><span class="sxs-lookup"><span data-stu-id="cc86f-162">Update Azure Policy</span></span>
    1. <span data-ttu-id="cc86f-163">Controllare l'assegnazione di tag, il gruppo di gestione, la sottoscrizione e i valori del gruppo di risorse per identificare ogni deviazione.</span><span class="sxs-lookup"><span data-stu-id="cc86f-163">Audit the tagging, management group, subscription, and resource group values to identify any deviation.</span></span>
    2. <span data-ttu-id="cc86f-164">Definire le opzioni relative alle dimensioni di SKU per limitare le distribuzioni agli SKU elencati nella documentazione relativa alla pianificazione della distribuzione.</span><span class="sxs-lookup"><span data-stu-id="cc86f-164">Establish SKU size options to limit deployments to SKUs listed in deployment planning documentation.</span></span>

## <a name="conclusion"></a><span data-ttu-id="cc86f-165">Conclusioni</span><span class="sxs-lookup"><span data-stu-id="cc86f-165">Conclusion</span></span>

<span data-ttu-id="cc86f-166">L'aggiunta di questi processi e di modifiche all'MVP della governance aiuta a mitigare molti dei rischi associati alla governance dei costi.</span><span class="sxs-lookup"><span data-stu-id="cc86f-166">Adding these processes and changes to the governance MVP helps mitigate many of the risks associated with cost governance.</span></span> <span data-ttu-id="cc86f-167">Nel loro insieme offrono le caratteristiche di visibilità, rendicontazione e ottimizzazione necessarie per controllare i costi.</span><span class="sxs-lookup"><span data-stu-id="cc86f-167">Together, they create the visibility, accountability, and optimization needed to control costs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cc86f-168">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="cc86f-168">Next steps</span></span>

<span data-ttu-id="cc86f-169">Dato che l'adozione del cloud continua a evolversi e a offrire valore aziendale supplementare, anche i rischi e le esigenze di governance del cloud sono destinate a evolversi.</span><span class="sxs-lookup"><span data-stu-id="cc86f-169">As cloud adoption continues to evolve and deliver additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="cc86f-170">Per l'azienda fittizia presentata in questo percorso, il passaggio successivo consiste nell'uso di questo investimento nella governance per gestire più cloud.</span><span class="sxs-lookup"><span data-stu-id="cc86f-170">For the fictional company in this journey, the next step is using this governance investment to manage multiple clouds.</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="cc86f-171">[Multi-cloud evolution](./multi-cloud-evolution.md) (Evoluzione di più cloud)</span><span class="sxs-lookup"><span data-stu-id="cc86f-171">[Multi-cloud evolution](./multi-cloud-evolution.md)</span></span>