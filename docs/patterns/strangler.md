---
title: Modello Sostituzione
description: Migrare in maniera incrementale un sistema legacy, sostituendo gradualmente parti specifiche di funzionalità con nuove applicazioni e servizi.
author: dragon119
ms.date: 06/23/2017
ms.openlocfilehash: 0bf0b76a69f947419da83edd894a04dbea02371b
ms.sourcegitcommit: 2ae794de13c45cf24ad60d4f4dbb193c25944eff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50001882"
---
# <a name="strangler-pattern"></a><span data-ttu-id="215f6-103">Modello Sostituzione</span><span class="sxs-lookup"><span data-stu-id="215f6-103">Strangler pattern</span></span>

<span data-ttu-id="215f6-104">Migrare in maniera incrementale un sistema legacy, sostituendo gradualmente parti specifiche di funzionalità con nuove applicazioni e servizi.</span><span class="sxs-lookup"><span data-stu-id="215f6-104">Incrementally migrate a legacy system by gradually replacing specific pieces of functionality with new applications and services.</span></span> <span data-ttu-id="215f6-105">Mano a mano che le funzionalità del sistema precedente vengono sostituite, il nuovo sistema sostituisce tutte le funzionalità del sistema precedente fino a quando non è possibile effettuarne la completa dismissione.</span><span class="sxs-lookup"><span data-stu-id="215f6-105">As features from the legacy system are replaced, the new system eventually replaces all of the old system's features, strangling the old system and allowing you to decommission it.</span></span> 

## <a name="context-and-problem"></a><span data-ttu-id="215f6-106">Contesto e problema</span><span class="sxs-lookup"><span data-stu-id="215f6-106">Context and problem</span></span>

<span data-ttu-id="215f6-107">Con l'invecchiamento dei sistemi, gli strumenti di sviluppo, le tecnologie di hosting e perfino le architetture con cui sono stati compilati possono diventare obsoleti.</span><span class="sxs-lookup"><span data-stu-id="215f6-107">As systems age, the development tools, hosting technology, and even system architectures they were built on can become increasingly obsolete.</span></span> <span data-ttu-id="215f6-108">Con l'aggiunta di nuove caratteristiche e funzionalità, la complessità delle applicazioni può aumentare notevolmente, rendendo i sistemi più difficili da gestire o ostacolando l'aggiunta di nuove funzionalità.</span><span class="sxs-lookup"><span data-stu-id="215f6-108">As new features and functionality are added, the complexity of these applications can increase dramatically, making them harder to maintain or add new features to.</span></span>

<span data-ttu-id="215f6-109">La sostituzione totale di un sistema complesso può rappresentare un impegno notevole.</span><span class="sxs-lookup"><span data-stu-id="215f6-109">Completely replacing a complex system can be a huge undertaking.</span></span> <span data-ttu-id="215f6-110">Spesso è necessario migrare con gradualità verso il nuovo sistema, conservando quello precedente per gestire le funzionalità di cui non è stata ancora eseguita la migrazione.</span><span class="sxs-lookup"><span data-stu-id="215f6-110">Often, you will need a gradual migration to a new system, while keeping the old system to handle features that haven't been migrated yet.</span></span> <span data-ttu-id="215f6-111">Tuttavia, l'esecuzione di due versioni distinte di un'applicazione implica che i client sappiano dove si trovano le singole funzionalità.</span><span class="sxs-lookup"><span data-stu-id="215f6-111">However, running two separate versions of an application means that clients have to know where particular features are located.</span></span> <span data-ttu-id="215f6-112">Ogni volta che viene eseguita la migrazione di una funzione o di un servizio, i client devono essere aggiornati affinché puntino alla nuova posizione.</span><span class="sxs-lookup"><span data-stu-id="215f6-112">Every time a feature or service is migrated, clients need to be updated to point to the new location.</span></span>

## <a name="solution"></a><span data-ttu-id="215f6-113">Soluzione</span><span class="sxs-lookup"><span data-stu-id="215f6-113">Solution</span></span>

<span data-ttu-id="215f6-114">Sostituire gradualmente parti specifiche di funzionalità con nuove applicazioni e servizi.</span><span class="sxs-lookup"><span data-stu-id="215f6-114">Incrementally replace specific pieces of functionality with new applications and services.</span></span> <span data-ttu-id="215f6-115">Creare un'interfaccia che intercetta le richieste inviate al sistema back-end legacy.</span><span class="sxs-lookup"><span data-stu-id="215f6-115">Create a façade that intercepts requests going to the backend legacy system.</span></span> <span data-ttu-id="215f6-116">L'interfaccia indirizza tali richieste all'applicazione legacy o ai nuovi servizi.</span><span class="sxs-lookup"><span data-stu-id="215f6-116">The façade routes these requests either to the legacy application or the new services.</span></span> <span data-ttu-id="215f6-117">Le funzionalità esistenti possono essere migrate nel nuovo sistema gradualmente, e i consumer possono continuare a usare la stessa interfaccia senza percepire l'avvenuta migrazione.</span><span class="sxs-lookup"><span data-stu-id="215f6-117">Existing features can be migrated to the new system gradually, and consumers can continue using the same interface, unaware that any migration has taken place.</span></span>

![](./_images/strangler.png)  

<span data-ttu-id="215f6-118">Questo modello contribuisce a ridurre i rischi implicati dalla migrazione e a diluire l'attività di sviluppo nel tempo.</span><span class="sxs-lookup"><span data-stu-id="215f6-118">This pattern helps to minimize risk from the migration, and spread the development effort over time.</span></span> <span data-ttu-id="215f6-119">Grazie all'interfaccia che indirizza gli utenti in modo sicuro all'applicazione corretta, è possibile aggiungere le funzionalità al nuovo sistema al ritmo preferito, assicurando al contempo il funzionamento continuo dell'applicazione legacy.</span><span class="sxs-lookup"><span data-stu-id="215f6-119">With the façade safely routing users to the correct application, you can add functionality to the new system at whatever pace you like, while ensuring the legacy application continues to function.</span></span> <span data-ttu-id="215f6-120">Nel tempo, le funzionalità vengono migrate nel nuovo sistema e il sistema legacy viene sostituito fino a non essere più necessario.</span><span class="sxs-lookup"><span data-stu-id="215f6-120">Over time, as features are migrated to the new system, the legacy system is eventually "strangled" and is no longer necessary.</span></span> <span data-ttu-id="215f6-121">Una volta completato questo processo, sarà possibile ritirare il sistema legacy in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="215f6-121">Once this process is complete, the legacy system can safely be retired.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="215f6-122">Considerazioni e problemi</span><span class="sxs-lookup"><span data-stu-id="215f6-122">Issues and considerations</span></span>

- <span data-ttu-id="215f6-123">Considerare la modalità di gestione di archivi dati e servizi che possono essere potenzialmente usati tanto dai sistemi legacy quanto da quelli nuovi.</span><span class="sxs-lookup"><span data-stu-id="215f6-123">Consider how to handle services and data stores that are potentially used by both new and legacy systems.</span></span> <span data-ttu-id="215f6-124">Verificare che entrambi possano accedere a queste risorse simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="215f6-124">Make sure both can access these resources side-by-side.</span></span>
- <span data-ttu-id="215f6-125">Strutturare i nuovi servizi e le applicazioni in modo che possono essere facilmente intercettati e sostituiti durante le future migrazioni.</span><span class="sxs-lookup"><span data-stu-id="215f6-125">Structure new applications and services in a way that they can easily be intercepted and replaced in future strangler migrations.</span></span>
- <span data-ttu-id="215f6-126">Quando la migrazione sarà infine completata, l'interfaccia sostitutiva verrà eliminata o adattata ai client legacy.</span><span class="sxs-lookup"><span data-stu-id="215f6-126">At some point, when the migration is complete, the strangler façade will either go away or evolve into an adaptor for legacy clients.</span></span>
- <span data-ttu-id="215f6-127">Verificare che l'interfaccia mantenga il passo con la migrazione.</span><span class="sxs-lookup"><span data-stu-id="215f6-127">Make sure the façade keeps up with the migration.</span></span>
- <span data-ttu-id="215f6-128">Verificare che l'interfaccia non diventi un singolo punto di guasto o un collo di bottiglia delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="215f6-128">Make sure the façade doesn't become a single point of failure or a performance bottleneck.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="215f6-129">Quando usare questo modello</span><span class="sxs-lookup"><span data-stu-id="215f6-129">When to use this pattern</span></span>

<span data-ttu-id="215f6-130">Usare questo modello quando si migra gradualmente un'applicazione back-end in una nuova architettura.</span><span class="sxs-lookup"><span data-stu-id="215f6-130">Use this pattern when gradually migrating a back-end application to a new architecture.</span></span>

<span data-ttu-id="215f6-131">Questo modello potrebbe non essere adatto nelle situazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="215f6-131">This pattern may not be suitable:</span></span>

- <span data-ttu-id="215f6-132">Quando non è possibile intercettare le richieste inviate al sistema back-end.</span><span class="sxs-lookup"><span data-stu-id="215f6-132">When requests to the back-end system cannot be intercepted.</span></span>
- <span data-ttu-id="215f6-133">Per i sistemi di piccole dimensioni in cui la complessità della sostituzione su larga scala è bassa.</span><span class="sxs-lookup"><span data-stu-id="215f6-133">For smaller systems where the complexity of wholesale replacement is low.</span></span>

## <a name="related-guidance"></a><span data-ttu-id="215f6-134">Informazioni correlate</span><span class="sxs-lookup"><span data-stu-id="215f6-134">Related guidance</span></span>

- <span data-ttu-id="215f6-135">Post di blog di Martin Fowler su [StranglerApplication](https://www.martinfowler.com/bliki/StranglerApplication.html)</span><span class="sxs-lookup"><span data-stu-id="215f6-135">Martin Fowler's blog post on [StranglerApplication](https://www.martinfowler.com/bliki/StranglerApplication.html)</span></span>
- [<span data-ttu-id="215f6-136">Modello Livello anti-danneggiamento</span><span class="sxs-lookup"><span data-stu-id="215f6-136">Anti-Corruption Layer pattern</span></span>](./anti-corruption-layer.md)
- [<span data-ttu-id="215f6-137">Modello Routing gateway</span><span class="sxs-lookup"><span data-stu-id="215f6-137">Gateway Routing pattern</span></span>](./gateway-routing.md)


 

