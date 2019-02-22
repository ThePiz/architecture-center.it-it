---
title: 'CAF: Allineare le guide alla progettazione con i criteri.'
description: Come allineare le guide alla progettazione con i criteri?
author: BrianBlanchard
ms.date: 01/04/2019
ms.openlocfilehash: 77a35597585e5f58967ea79d3c7b0fa17b6ab80e
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901296"
---
<!---
I've established policies. How to help developers adopt these policies?
Draft an architecture design guide.

[Aspirational statement] If you're using Azure, you can use one of ours as a starting point. The choose one of the following 6 as a starting point and mold it to fit your policies.
--->

<!-- markdownlint-disable MD026 -->

# <a name="how-do-you-align-design-guides-with-policy"></a><span data-ttu-id="53d50-103">Come allineare le guide alla progettazione con i criteri?</span><span class="sxs-lookup"><span data-stu-id="53d50-103">How do you align design guides with policy?</span></span>

<span data-ttu-id="53d50-104">Dopo aver [definito i criteri del cloud](define-policy.md) in base ai [rischi identificati](understanding-business-risk.md), è necessario generare una guida operativa che si allinei con questi criteri a cui il personale IT e gli sviluppatori devono fare riferimento.</span><span class="sxs-lookup"><span data-stu-id="53d50-104">After you've [defined cloud policies](define-policy.md) based on your [identified risks](understanding-business-risk.md), you'll need to generate actionable guidance that aligns with these policies for your IT staff and developers to refer to.</span></span> <span data-ttu-id="53d50-105">Elaborare una guida alla progettazione della governance del cloud consente di specificare scelte strutturali, tecnologiche e di processo specifiche sulla base delle istruzioni dei criteri generati per ognuna delle cinque discipline di governance.</span><span class="sxs-lookup"><span data-stu-id="53d50-105">Drafting a cloud governance design guide allows you to specify specific structural, technological, and process choices based on the policy statements you generated for each of the five governance disciplines.</span></span>

<span data-ttu-id="53d50-106">Una guida alla progettazione della governance del cloud deve stabilire le scelte di architettura e gli schemi progettuali per ognuna delle componenti fondamentali dell'infrastruttura delle distribuzioni del cloud che meglio soddisfano i requisiti dei criteri.</span><span class="sxs-lookup"><span data-stu-id="53d50-106">A cloud governance design guide should establish the architecture choices and design patterns for each of the core infrastructure components of cloud deployments that best meet your policy requirements.</span></span> <span data-ttu-id="53d50-107">Assieme a questi è necessario fornire una spiegazione di alto livello della tecnologia, degli strumenti e dei processi che supportano ognuna di queste decisioni di progettazione.</span><span class="sxs-lookup"><span data-stu-id="53d50-107">Alongside these you should provide a high-level explanation of the technology, tools, and processes that will support each of these design decisions.</span></span>

<span data-ttu-id="53d50-108">Sebbene l'analisi dei rischi e le istruzioni di criteri possano, in una certa misura, essere agnostici per la piattaforma cloud, la guida alla progettazione deve fornire dettagli di implementazione specifici per la piattaforma IT e di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="53d50-108">Although your risk analysis and policy statements may, to some degree, be cloud platform agnostic, your design guide should provide platform-specific implementation details that your IT and dev.</span></span> <span data-ttu-id="53d50-109">Concentrarsi sull'architettura, le funzioni e le caratteristiche della piattaforma scelta quando si prendono decisioni di progettazione e si fornisce materiale sussidiario.</span><span class="sxs-lookup"><span data-stu-id="53d50-109">Focus on the architecture, tools, and features of your chosen platform when making design decision and providing guidance.</span></span>

<span data-ttu-id="53d50-110">Anche se le guide alla progettazione del cloud dovrebbero tenere conto di alcuni dettagli tecnici associati a ogni componente dell'infrastruttura, non sono intese come documenti tecnici o specifiche estese.</span><span class="sxs-lookup"><span data-stu-id="53d50-110">While cloud design guides should take into account some of the technical details associated with each infrastructure component, they are not meant to be extensive technical documents or specifications.</span></span> <span data-ttu-id="53d50-111">Assicurarsi che le guide indichino tutte le istruzioni di criteri e dichiarino chiaramente le decisioni di progettazione in un formato di facile riconoscimento e riferimento per il personale.</span><span class="sxs-lookup"><span data-stu-id="53d50-111">Make sure your guides address all of your policy statements and clearly state design decisions in a format easy for staff to understand and reference.</span></span>

<!-- markdownlint-enable MD033 -->

## <a name="using-the-actionable-governance-journeys"></a><span data-ttu-id="53d50-112">Usare i percorsi di governance di utilità pratica</span><span class="sxs-lookup"><span data-stu-id="53d50-112">Using the actionable governance journeys</span></span>

<span data-ttu-id="53d50-113">Se si prevede di usare la piattaforma Azure per l'adozione del cloud, il CAF fornisce [percorsi di governance ](../journeys/overview.md) che illustrano l'approccio incrementale del modello di governance CAF.</span><span class="sxs-lookup"><span data-stu-id="53d50-113">If you're planning to use the Azure platform for your cloud adoption, the CAF provides [governance journeys](../journeys/overview.md) illustrating the incremental approach of the CAF governance model.</span></span> <span data-ttu-id="53d50-114">Questi percorsi narrativi coprono una serie di scenari di adozione comuni, compresi i rischi aziendali, i requisiti di tolleranza e le istruzioni dei criteri che hanno portato alla creazione di un prodotto minimo funzionante (MVP) di governance.</span><span class="sxs-lookup"><span data-stu-id="53d50-114">These narrative journeys cover a range of common adoption scenarios, including the business risks, tolerance requirements, and policy statements that went into creating a governance minimum viable product (MVP).</span></span> <span data-ttu-id="53d50-115">Questi percorsi rappresentano una sintesi delle reali esperienze del cliente del processo di adozione del cloud in Azure.</span><span class="sxs-lookup"><span data-stu-id="53d50-115">These journeys represent a synthesis of real-world customer experience of the cloud adoption process in Azure.</span></span>

<span data-ttu-id="53d50-116">Sebbene ogni adozione del cloud abbia obiettivi, priorità e sfide uniche, questi esempi dovrebbero fornire un modello ottimale per la conversione dei criteri in materiale sussidiario.</span><span class="sxs-lookup"><span data-stu-id="53d50-116">While every cloud adoption has unique goals, priorities, and challenges, these samples should provide a good template for converting your policy into guidance.</span></span> <span data-ttu-id="53d50-117">Selezionare lo scenario più vicino alla propria situazione come punto iniziale in modo da soddisfare le proprie esigenze specifiche in ambito di criteri.</span><span class="sxs-lookup"><span data-stu-id="53d50-117">Pick the closest scenario to your situation as a starting point, and mold it to fit your specific policy needs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="53d50-118">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="53d50-118">Next steps</span></span>

<span data-ttu-id="53d50-119">Servirsi della guida alla progettazione stabilire i processi di adesione ai criteri per garantirne la conformità.</span><span class="sxs-lookup"><span data-stu-id="53d50-119">With design guidance in place, establish policy adherence processes to ensure policy compliance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="53d50-120">Processi di adesione ai criteri</span><span class="sxs-lookup"><span data-stu-id="53d50-120">Policy adherence processes</span></span>](processes.md)