---
title: 'Cloud Adoption Framework: Informazioni sulla classificazione dei dati'
description: Che cos'è la classificazione dei dati?
author: BrianBlanchard
ms.date: 2/11/2019
ms.openlocfilehash: 07268e7242d92ac2581bf28b378a3c43d166620c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901631"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-data-classification"></a><span data-ttu-id="f5fda-103">Che cos'è la classificazione dei dati?</span><span class="sxs-lookup"><span data-stu-id="f5fda-103">What is data classification?</span></span>

<span data-ttu-id="f5fda-104">Questo è un articolo introduttivo sull'argomento generale relativo alla classificazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="f5fda-104">This is an introductory article on the general topic of Data Classification.</span></span> <span data-ttu-id="f5fda-105">La classificazione dei dati è un punto di partenza molto comune nell'ambito della governance.</span><span class="sxs-lookup"><span data-stu-id="f5fda-105">Data classification is a very common starting point for all governance.</span></span>

## <a name="business-risks-and-governance"></a><span data-ttu-id="f5fda-106">Governance e rischi aziendali</span><span class="sxs-lookup"><span data-stu-id="f5fda-106">Business risks and governance</span></span>

<span data-ttu-id="f5fda-107">Nella maggior parte delle organizzazioni, i motivi principali per cui investire nella governance possono essere illustrati in sintesi da tre rischi aziendali:</span><span class="sxs-lookup"><span data-stu-id="f5fda-107">In most organizations, the primary reasons for investing in governance can be reduced to three business risks:</span></span>

* <span data-ttu-id="f5fda-108">Rischio associato alla violazione dei dati</span><span class="sxs-lookup"><span data-stu-id="f5fda-108">Liability associated with data breaches</span></span>
* <span data-ttu-id="f5fda-109">Interruzione dell'attività aziendale</span><span class="sxs-lookup"><span data-stu-id="f5fda-109">Interruption to the business from outages</span></span>
* <span data-ttu-id="f5fda-110">Spese non pianificate o impreviste</span><span class="sxs-lookup"><span data-stu-id="f5fda-110">Unplanned or unexpected spending</span></span>

<span data-ttu-id="f5fda-111">Esistono molte varianti di questi rischi aziendali,</span><span class="sxs-lookup"><span data-stu-id="f5fda-111">There are many variants of these three business risks.</span></span> <span data-ttu-id="f5fda-112">ma i tre indicati tendono a essere i più comuni.</span><span class="sxs-lookup"><span data-stu-id="f5fda-112">However, the tend to be the most common.</span></span>

## <a name="understand-then-mitigate"></a><span data-ttu-id="f5fda-113">Comprendere e mitigare i rischi</span><span class="sxs-lookup"><span data-stu-id="f5fda-113">Understand then mitigate</span></span>

<span data-ttu-id="f5fda-114">Prima di poter mitigare eventuali rischi, è necessario riconoscerli.</span><span class="sxs-lookup"><span data-stu-id="f5fda-114">Before any risk can be mitigated, it must be understood.</span></span> <span data-ttu-id="f5fda-115">Nel caso di rischio di violazione dei dati, l'identificazione è basata principalmente sulla classificazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="f5fda-115">In the case of data breach liability, that understanding starts with data classification.</span></span> <span data-ttu-id="f5fda-116">La classificazione dei dati è il processo di associazione di una caratteristica di metadati a ogni asset di un patrimonio digitale, che identifica il tipo di dati associati a tale asset.</span><span class="sxs-lookup"><span data-stu-id="f5fda-116">Data classification is the process of associating a meta data characteristic to every asset in a digital estate, which identifies the type of data associated with that asset.</span></span>

<span data-ttu-id="f5fda-117">Microsoft suggerisce che qualsiasi asset identificato come candidato potenziale per la migrazione o la distribuzione nel cloud debba disporre di metadati documentati per registrare la classificazione dei dati, il livello di criticità aziendale e la responsabilità di fatturazione.</span><span class="sxs-lookup"><span data-stu-id="f5fda-117">Microsoft suggests that any asset which has been identified as a potential candidate for migration or deployment to the cloud should have documented meta data to record the data classification, business criticality, and billing responsibility.</span></span> <span data-ttu-id="f5fda-118">Questi tre punti di classificazione risultano molto utili per comprendere e mitigare i rischi.</span><span class="sxs-lookup"><span data-stu-id="f5fda-118">These three points of classification can go a long way to understanding and mitigating risks.</span></span>

## <a name="microsofts-data-classification"></a><span data-ttu-id="f5fda-119">Classificazione dei dati Microsoft</span><span class="sxs-lookup"><span data-stu-id="f5fda-119">Microsoft's data classification</span></span>

<span data-ttu-id="f5fda-120">Di seguito è riportato un elenco delle classificazioni usate da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f5fda-120">The following is a list of classifications Microsoft uses.</span></span> <span data-ttu-id="f5fda-121">A seconda del settore di appartenenza o dei requisiti di sicurezza esistenti, è possibile che siano già definiti standard per la classificazione dei dati nell'ambito dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="f5fda-121">Depending on your industry or existing security requirements, data classifications standards may already exist within your organization.</span></span> <span data-ttu-id="f5fda-122">Se non esistono standard, è possibile usare questa classificazione di esempio, che consente di comprendere meglio il profilo di rischio e del patrimonio digitale.</span><span class="sxs-lookup"><span data-stu-id="f5fda-122">If no standard exists, we welcome you to use this sample classification, to help you better understand your digital estate and risk profile.</span></span>  

* <span data-ttu-id="f5fda-123">**Non aziendali:** dati personali non appartenenti a Microsoft</span><span class="sxs-lookup"><span data-stu-id="f5fda-123">**Non-Business:** Data from your personal life that does not belong to Microsoft</span></span>
* <span data-ttu-id="f5fda-124">**Pubblici**: dati aziendali approvati e disponibili liberamente per l'uso pubblico</span><span class="sxs-lookup"><span data-stu-id="f5fda-124">**Public:** Business data that is freely available and approved for public consumption</span></span>
* <span data-ttu-id="f5fda-125">**Generali:** dati aziendali non destinati a un gruppo di destinatari pubblico</span><span class="sxs-lookup"><span data-stu-id="f5fda-125">**General:** Business data that is not meant for a public audience</span></span>
* <span data-ttu-id="f5fda-126">**Riservati:** dati aziendali che possono causare danni a Microsoft se eccessivamente condivisi</span><span class="sxs-lookup"><span data-stu-id="f5fda-126">**Confidential:** Business data that could cause harm to Microsoft if over-shared</span></span>
* <span data-ttu-id="f5fda-127">**Strettamente riservati:** dati aziendali che possono causare danni ingenti a Microsoft se eccessivamente condivisi</span><span class="sxs-lookup"><span data-stu-id="f5fda-127">**Highly Confidential:** Business data that would cause extensive harm to Microsoft if over-shared</span></span>

## <a name="tagging-data-classification-in-azure"></a><span data-ttu-id="f5fda-128">Assegnazione di tag alla classificazione di dati in Azure</span><span class="sxs-lookup"><span data-stu-id="f5fda-128">Tagging data classification in Azure</span></span>

<span data-ttu-id="f5fda-129">Ogni provider di servizi cloud deve offrire un meccanismo per la registrazione dei metadati relativi a qualsiasi asset.</span><span class="sxs-lookup"><span data-stu-id="f5fda-129">Every cloud provider should offer a mechanism for recording metadata about any asset.</span></span> <span data-ttu-id="f5fda-130">I metadati sono fondamentali per la gestione degli asset nel cloud.</span><span class="sxs-lookup"><span data-stu-id="f5fda-130">Metadata is vital to managing assets in the cloud.</span></span> <span data-ttu-id="f5fda-131">Nel caso di Azure, i tag delle risorse costituiscono l'approccio consigliato per l'archiviazione dei metadati.</span><span class="sxs-lookup"><span data-stu-id="f5fda-131">In the case of Azure, resource tags are the suggested approach for metadata storage.</span></span> <span data-ttu-id="f5fda-132">Per altre informazioni sull'assegnazione di tag alle risorse, vedere l'articolo [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags).</span><span class="sxs-lookup"><span data-stu-id="f5fda-132">For additional information on resource tagging in Azure, see the article on [Using tags to organize your Azure resources](/azure/azure-resource-manager/resource-group-using-tags).</span></span>

## <a name="next-steps"></a><span data-ttu-id="f5fda-133">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="f5fda-133">Next steps</span></span>

<span data-ttu-id="f5fda-134">Applicare le classificazioni dei dati durante uno dei percorsi di governance di utilità pratica.</span><span class="sxs-lookup"><span data-stu-id="f5fda-134">Apply data classifications during one of the actionable governance journeys.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f5fda-135">Iniziare un percorso di governance di utilità pratica</span><span class="sxs-lookup"><span data-stu-id="f5fda-135">Begin an Actionable Governance Journeys</span></span>](../journeys/overview.md)
