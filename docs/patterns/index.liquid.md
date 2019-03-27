---
title: Modelli di progettazione cloud
titleSuffix: Azure Architecture Center
description: Schemi progettuali del cloud per Microsoft Azure
keywords: Azure
author: dragon119
ms.date: 12/10/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 4229531366f1b0c3257384694cf4358da9e63177
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241482"
---
# <a name="cloud-design-patterns"></a><span data-ttu-id="9d448-104">Modelli di progettazione cloud</span><span class="sxs-lookup"><span data-stu-id="9d448-104">Cloud Design Patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="9d448-105">Questi schemi progettuali sono utili per la compilazione di applicazioni affidabili, scalabili e sicure nel cloud.</span><span class="sxs-lookup"><span data-stu-id="9d448-105">These design patterns are useful for building reliable, scalable, secure applications in the cloud.</span></span>

<span data-ttu-id="9d448-106">Ogni schema descrive il problema che risolve, le considerazioni per l'applicazione dello schema e un esempio basato su Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="9d448-106">Each pattern describes the problem that the pattern addresses, considerations for applying the pattern, and an example based on Microsoft Azure.</span></span> <span data-ttu-id="9d448-107">La maggior parte degli schemi include esempi o frammenti di codice che illustrano come implementare lo schema in Azure.</span><span class="sxs-lookup"><span data-stu-id="9d448-107">Most of the patterns include code samples or snippets that show how to implement the pattern on Azure.</span></span> <span data-ttu-id="9d448-108">Tuttavia, la maggior parte degli schemi è pertinente a qualsiasi sistema distribuito, sia in hosting su Azure sia su altre piattaforme cloud.</span><span class="sxs-lookup"><span data-stu-id="9d448-108">However, most of the patterns are relevant to any distributed system, whether hosted on Azure or on other cloud platforms.</span></span>

## <a name="problem-areas-in-the-cloud"></a><span data-ttu-id="9d448-109">Aree problematiche nel cloud</span><span class="sxs-lookup"><span data-stu-id="9d448-109">Problem areas in the cloud</span></span>

<!-- markdownlint-disable MD033 -->

<ul id="categories" class="panel">
<span data-ttu-id="9d448-110">{%- for category in categories %}</span><span class="sxs-lookup"><span data-stu-id="9d448-110">{%- for category in categories %}</span></span>
    <li>
    <span data-ttu-id="9d448-111">{% include 'pattern-category-card' %}</span><span class="sxs-lookup"><span data-stu-id="9d448-111">{% include 'pattern-category-card' %}</span></span>
    </li>
<span data-ttu-id="9d448-112">{%- endfor %}</span><span class="sxs-lookup"><span data-stu-id="9d448-112">{%- endfor %}</span></span>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="catalog-of-patterns"></a><span data-ttu-id="9d448-113">Catalogo dei modelli</span><span class="sxs-lookup"><span data-stu-id="9d448-113">Catalog of patterns</span></span>

| <span data-ttu-id="9d448-114">Modello</span><span class="sxs-lookup"><span data-stu-id="9d448-114">Pattern</span></span> | <span data-ttu-id="9d448-115">Summary</span><span class="sxs-lookup"><span data-stu-id="9d448-115">Summary</span></span> |
|---------|---------|
|         |         |

<span data-ttu-id="9d448-116">{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}</span><span class="sxs-lookup"><span data-stu-id="9d448-116">{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}</span></span>
