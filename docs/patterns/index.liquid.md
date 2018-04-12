---
title: Modelli di progettazione cloud
description: Schemi progettuali del cloud per Microsoft Azure
keywords: Azure
ms.openlocfilehash: 4747c896fc6fc5866be782d76c5290d6b49ad451
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="cloud-design-patterns"></a><span data-ttu-id="b2da4-104">Modelli di progettazione cloud</span><span class="sxs-lookup"><span data-stu-id="b2da4-104">Cloud Design Patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="b2da4-105">Questi schemi progettuali sono utili per la compilazione di applicazioni affidabili, scalabili e sicure nel cloud.</span><span class="sxs-lookup"><span data-stu-id="b2da4-105">These design patterns are useful for building reliable, scalable, secure applications in the cloud.</span></span>

<span data-ttu-id="b2da4-106">Ogni schema descrive il problema che risolve, le considerazioni per l'applicazione dello schema e un esempio basato su Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="b2da4-106">Each pattern describes the problem that the pattern addresses, considerations for applying the pattern, and an example based on Microsoft Azure.</span></span> <span data-ttu-id="b2da4-107">La maggior parte degli schemi include esempi o frammenti di codice che illustrano come implementare lo schema in Azure.</span><span class="sxs-lookup"><span data-stu-id="b2da4-107">Most of the patterns include code samples or snippets that show how to implement the pattern on Azure.</span></span> <span data-ttu-id="b2da4-108">Tuttavia, la maggior parte degli schemi è pertinente a qualsiasi sistema distribuito, sia in hosting su Azure sia su altre piattaforme cloud.</span><span class="sxs-lookup"><span data-stu-id="b2da4-108">However, most of the patterns are relevant to any distributed system, whether hosted on Azure or on other cloud platforms.</span></span>

## <a name="problem-areas-in-the-cloud"></a><span data-ttu-id="b2da4-109">Aree problematiche nel cloud</span><span class="sxs-lookup"><span data-stu-id="b2da4-109">Problem areas in the cloud</span></span>

<ul id="categories" class="panel">
<span data-ttu-id="b2da4-110">{%- for category in categories %}</span><span class="sxs-lookup"><span data-stu-id="b2da4-110">{%- for category in categories %}</span></span>
    <li>
    <span data-ttu-id="b2da4-111">{% include 'pattern-category-card' %}</span><span class="sxs-lookup"><span data-stu-id="b2da4-111">{% include 'pattern-category-card' %}</span></span>
    </li>
<span data-ttu-id="b2da4-112">{%- endfor %}</span><span class="sxs-lookup"><span data-stu-id="b2da4-112">{%- endfor %}</span></span>
</ul>

## <a name="catalog-of-patterns"></a><span data-ttu-id="b2da4-113">Catalogo dei modelli</span><span class="sxs-lookup"><span data-stu-id="b2da4-113">Catalog of patterns</span></span>

| <span data-ttu-id="b2da4-114">Modello</span><span class="sxs-lookup"><span data-stu-id="b2da4-114">Pattern</span></span> | <span data-ttu-id="b2da4-115">Summary</span><span class="sxs-lookup"><span data-stu-id="b2da4-115">Summary</span></span> |
|---------|---------|
|         |         |

<span data-ttu-id="b2da4-116">{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}</span><span class="sxs-lookup"><span data-stu-id="b2da4-116">{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}</span></span>
