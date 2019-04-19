---
title: 'CAF: Piccole e medie imprese - Presentazione iniziale dello scenario per la strategia di governance'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Questa presentazione descrive un caso d'uso per un percorso di governance per piccole e medie imprese.
author: BrianBlanchard
ms.openlocfilehash: 6173b01f310169017761110d6641acfa51d45df8
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902211"
---
# <a name="small-to-medium-enterprise-the-narrative-behind-the-governance-strategy"></a><span data-ttu-id="da789-103">Piccole e medie imprese: presentazione dello scenario per la strategia di governance</span><span class="sxs-lookup"><span data-stu-id="da789-103">Small-to-medium enterprise: The narrative behind the governance strategy</span></span>

<span data-ttu-id="da789-104">La presentazione seguente descrive il caso d'uso per il [percorso di governance per le piccole e medie imprese](./overview.md).</span><span class="sxs-lookup"><span data-stu-id="da789-104">The following narrative describes the use case for the [small-to-medium enterprise governance journey](./overview.md).</span></span> <span data-ttu-id="da789-105">Prima di implementare il percorso, è importante conoscere i presupposti e i ragionamenti presentati in questo documento.</span><span class="sxs-lookup"><span data-stu-id="da789-105">Before implementing the journey, it’s important to understand the assumptions and reasoning that are reflected in this narrative.</span></span> <span data-ttu-id="da789-106">Sarà quindi possibile allineare più facilmente la strategia di governance al percorso specifico per la propria organizzazione.</span><span class="sxs-lookup"><span data-stu-id="da789-106">Then you can better align the governance strategy to your own organization’s journey.</span></span>

## <a name="back-story"></a><span data-ttu-id="da789-107">Scenario</span><span class="sxs-lookup"><span data-stu-id="da789-107">Back story</span></span>

<span data-ttu-id="da789-108">Il consiglio di amministrazione ha avviato l'anno con piani per dare nuova energia alle attività aziendali in diversi modi,</span><span class="sxs-lookup"><span data-stu-id="da789-108">The board of directors started the year with plans to energize the business in several ways.</span></span> <span data-ttu-id="da789-109">invitando la dirigenza a migliorare le esperienze dei clienti per conquistare nuove quote di mercato.</span><span class="sxs-lookup"><span data-stu-id="da789-109">They are pushing leadership to improve customer experiences to gain market share.</span></span> <span data-ttu-id="da789-110">Il consiglio spinge anche verso la realizzazione di nuovi prodotti e servizi per garantire all'azienda una posizione leader riconosciuta nel settore.</span><span class="sxs-lookup"><span data-stu-id="da789-110">They are also pushing for new products and services that will position the company as a thought leader in the industry.</span></span> <span data-ttu-id="da789-111">Sono state anche avviate iniziative parallele per ridurre gli sprechi e tagliare i costi non necessari.</span><span class="sxs-lookup"><span data-stu-id="da789-111">They also initiated a parallel effort to reduce waste and cut unnecessary costs.</span></span> <span data-ttu-id="da789-112">Anche se possono intimorire, le azioni del consiglio di amministrazione e della dirigenza dimostrano che gli investimenti sono incentrati al massimo sulla crescita futura.</span><span class="sxs-lookup"><span data-stu-id="da789-112">Though intimidating, the actions of the board and leadership show that this effort is focusing as much capital as possible on future growth.</span></span>

<span data-ttu-id="da789-113">In passato, il CIO (Chief Information Officer) dell'azienda è stato escluso da queste conversazioni strategiche.</span><span class="sxs-lookup"><span data-stu-id="da789-113">In the past, the company’s CIO has been excluded from these strategic conversations.</span></span> <span data-ttu-id="da789-114">Tuttavia, dato che la visione futura è intrinsecamente collegata allo sviluppo tecnico, il reparto IT partecipa alla discussione per contribuire alla definizione di questi piani di notevole portata.</span><span class="sxs-lookup"><span data-stu-id="da789-114">However, because the future vision is intrinsically linked to technical growth, IT has a seat at the table to help guide these big plans.</span></span> <span data-ttu-id="da789-115">Ci si aspetta ora che il reparto IT contribuisca in modi nuovi.</span><span class="sxs-lookup"><span data-stu-id="da789-115">IT is now expected to deliver in new ways.</span></span> <span data-ttu-id="da789-116">Il team non è in effetti preparato per questi cambiamenti ed è probabile che debba affrontare le difficoltà della curva di apprendimento.</span><span class="sxs-lookup"><span data-stu-id="da789-116">The team isn’t really prepared for these changes and is likely to struggle with the learning curve.</span></span>

## <a name="business-characteristics"></a><span data-ttu-id="da789-117">Caratteristiche dell'azienda</span><span class="sxs-lookup"><span data-stu-id="da789-117">Business characteristics</span></span>

<span data-ttu-id="da789-118">Il profilo di business della società è il seguente:</span><span class="sxs-lookup"><span data-stu-id="da789-118">The company has the following business profile:</span></span>

- <span data-ttu-id="da789-119">Tutte le attività di vendita e operative sono concentrate in un singolo paese, con una bassa percentuale di clienti globali.</span><span class="sxs-lookup"><span data-stu-id="da789-119">All sales and operations reside in a single country, with a low percentage of global customers.</span></span>
- <span data-ttu-id="da789-120">L'azienda opera come una singola business unit, con budget allineato alle funzioni, tra cui Vendite, Marketing, Operazioni e IT.</span><span class="sxs-lookup"><span data-stu-id="da789-120">The business operates as a single business unit, with budget aligned to functions, including Sales, Marketing, Operations, and IT.</span></span>
- <span data-ttu-id="da789-121">Per l'azienda, la maggior parte delle attività IT è considerata come un deflusso di capitali o un centro di costo.</span><span class="sxs-lookup"><span data-stu-id="da789-121">The business views most of IT as a capital drain or a cost center.</span></span>

## <a name="current-state"></a><span data-ttu-id="da789-122">Stato corrente</span><span class="sxs-lookup"><span data-stu-id="da789-122">Current state</span></span>

<span data-ttu-id="da789-123">Questo è lo stato corrente delle attività IT e cloud della società:</span><span class="sxs-lookup"><span data-stu-id="da789-123">Here is the current state of the company’s IT and cloud operations:</span></span>

- <span data-ttu-id="da789-124">Il reparto IT gestisce operativamente due ambienti di infrastruttura ospitati.</span><span class="sxs-lookup"><span data-stu-id="da789-124">IT operates two hosted infrastructure environments.</span></span> <span data-ttu-id="da789-125">Un ambiente contiene gli asset di produzione.</span><span class="sxs-lookup"><span data-stu-id="da789-125">One environment contains production assets.</span></span> <span data-ttu-id="da789-126">Il secondo ambiente contiene gli asset per il ripristino di emergenza e alcuni asset per sviluppo e test.</span><span class="sxs-lookup"><span data-stu-id="da789-126">The second environment contains disaster recovery and some dev/test assets.</span></span> <span data-ttu-id="da789-127">Questi ambienti sono ospitati da due diversi provider.</span><span class="sxs-lookup"><span data-stu-id="da789-127">These environments are hosted by two different providers.</span></span> <span data-ttu-id="da789-128">Il reparto IT fa riferimento a questi due data center rispettivamente come Prod e DR.</span><span class="sxs-lookup"><span data-stu-id="da789-128">IT refers to these two datacenters as Prod and DR respectively.</span></span>
- <span data-ttu-id="da789-129">L'accesso al cloud è avvenuto tramite la migrazione di tutti gli account di posta elettronica degli utenti finali a Office 365.</span><span class="sxs-lookup"><span data-stu-id="da789-129">IT entered the cloud by migrating all end-user email accounts to Office 365.</span></span> <span data-ttu-id="da789-130">Questa migrazione è stata completata sei mesi fa.</span><span class="sxs-lookup"><span data-stu-id="da789-130">This migration was completed six months ago.</span></span> <span data-ttu-id="da789-131">Pochi altri asset IT sono stati distribuiti nel cloud.</span><span class="sxs-lookup"><span data-stu-id="da789-131">Few other IT assets have been deployed to the cloud.</span></span>
- <span data-ttu-id="da789-132">I team di sviluppo delle applicazioni operano in modalità sviluppo/test per scoprire altre capacità native del cloud.</span><span class="sxs-lookup"><span data-stu-id="da789-132">The application development teams are working in a dev/test capacity to learn about cloud native capabilities.</span></span>
- <span data-ttu-id="da789-133">Il team di business intelligence (BI) sta sperimentando implementazioni di Big Data nel cloud e la gestione dei dati in nuove piattaforme.</span><span class="sxs-lookup"><span data-stu-id="da789-133">The business intelligence (BI) team is experimenting with big data in the cloud and curation of data on new platforms.</span></span>
- <span data-ttu-id="da789-134">La società ha definito criteri generali che stabiliscono che le informazioni personali dei clienti e i dati finanziari non possono essere ospitati nel cloud, con conseguenti limiti per le applicazioni cruciali nelle distribuzioni correnti.</span><span class="sxs-lookup"><span data-stu-id="da789-134">The company has a loosely defined policy stating that customer personally identifiable information (PII) and financial data cannot be hosted in the cloud, which limits mission-critical applications in the current deployments.</span></span>
- <span data-ttu-id="da789-135">Gli investimenti IT sono gestiti in gran parte come spese in conto capitale (CapEx)</span><span class="sxs-lookup"><span data-stu-id="da789-135">IT investments are controlled largely by capital expense (CapEx).</span></span> <span data-ttu-id="da789-136">e sono pianificati di anno in anno.</span><span class="sxs-lookup"><span data-stu-id="da789-136">Those investments are planned yearly.</span></span> <span data-ttu-id="da789-137">Nel corso degli ultimi anni, gli investimenti sono stati limitati a poco più dei requisiti di manutenzione di base.</span><span class="sxs-lookup"><span data-stu-id="da789-137">In the past several years, investments have included little more than basic maintenance requirements.</span></span>

## <a name="future-state"></a><span data-ttu-id="da789-138">Stato futuro</span><span class="sxs-lookup"><span data-stu-id="da789-138">Future state</span></span>

<span data-ttu-id="da789-139">Sono previste le modifiche seguenti per gli anni successivi:</span><span class="sxs-lookup"><span data-stu-id="da789-139">The following changes are anticipated over the next several years:</span></span>

- <span data-ttu-id="da789-140">Il CIO sta rivedendo i criteri per la gestione dei dati personali e finanziari, in modo da tenere conto degli obiettivi futuri.</span><span class="sxs-lookup"><span data-stu-id="da789-140">The CIO is reviewing the policy on PII and financial data to allow for the future state goals.</span></span>
- <span data-ttu-id="da789-141">I team di sviluppo delle applicazioni e di business intelligence intendono rilasciare soluzioni basate sul cloud in produzione nel corso dei 24 mesi successivi, in linea con la visione aziendale per l'engagement dei clienti e i nuovi prodotti.</span><span class="sxs-lookup"><span data-stu-id="da789-141">The application development and BI teams want to release cloud-based solutions to production over the next 24 months based on the vision for customer engagement and new products.</span></span>
- <span data-ttu-id="da789-142">Nel corso dell'anno, il team IT terminerà il ritiro dei carichi di lavoro di ripristino di emergenza del data center DR con la migrazione di 2.000 macchine virtuali nel cloud.</span><span class="sxs-lookup"><span data-stu-id="da789-142">This year, the IT team will finish retiring the disaster recovery workloads of the DR datacenter by migrating 2,000 VMs to the cloud.</span></span> <span data-ttu-id="da789-143">È previsto che questa misura consenta risparmi sui costi stimati per 25 milioni di dollari USA nel corso dei prossimi cinque anni.</span><span class="sxs-lookup"><span data-stu-id="da789-143">This is expected to produce an estimated $25M USD cost savings over the next five years.</span></span>
    <span data-ttu-id="da789-144">![Costi locali rispetto ai costi di Azure in un grafico che dimostra un ritorno di 25 milioni di dollari USA nei prossimi cinque anni](../../../_images/governance/calculator-small-to-medium-enterprise.png)</span><span class="sxs-lookup"><span data-stu-id="da789-144">![On-premise costs versus Azure costs demonstrating a return of $25M USD over the next five years](../../../_images/governance/calculator-small-to-medium-enterprise.png)</span></span>
- <span data-ttu-id="da789-145">L'azienda intende modificare la modalità di gestione degli investimenti, riposizionando le spese in conto capitale come spese operative (OpEx) per il contesto IT.</span><span class="sxs-lookup"><span data-stu-id="da789-145">The company plans to change how it makes IT investments by repositioning the committed CapEx as an operational expense (OpEx) within IT.</span></span> <span data-ttu-id="da789-146">Questa modifica consentirà un maggiore controllo dei costi e permetterà al reparto IT di accelerare altri progetti pianificati.</span><span class="sxs-lookup"><span data-stu-id="da789-146">This change will provide greater cost control and enable IT to accelerate other planned efforts.</span></span>

## <a name="next-steps"></a><span data-ttu-id="da789-147">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="da789-147">Next steps</span></span>

<span data-ttu-id="da789-148">La società ha elaborato criteri aziendali per strutturare l'implementazione della governance.</span><span class="sxs-lookup"><span data-stu-id="da789-148">The company has developed a corporate policy to shape the governance implementation.</span></span> <span data-ttu-id="da789-149">Tali criteri aziendali guidano molte delle decisioni tecniche.</span><span class="sxs-lookup"><span data-stu-id="da789-149">The corporate policy drives many of the technical decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="da789-150">Esaminare i criteri aziendali iniziali</span><span class="sxs-lookup"><span data-stu-id="da789-150">Review the initial corporate policy</span></span>](./initial-corporate-policy.md)