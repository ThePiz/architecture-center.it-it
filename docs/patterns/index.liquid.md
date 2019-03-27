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
# <a name="cloud-design-patterns"></a>Modelli di progettazione cloud

[!INCLUDE [header](../../_includes/header.md)]

Questi schemi progettuali sono utili per la compilazione di applicazioni affidabili, scalabili e sicure nel cloud.

Ogni schema descrive il problema che risolve, le considerazioni per l'applicazione dello schema e un esempio basato su Microsoft Azure. La maggior parte degli schemi include esempi o frammenti di codice che illustrano come implementare lo schema in Azure. Tuttavia, la maggior parte degli schemi è pertinente a qualsiasi sistema distribuito, sia in hosting su Azure sia su altre piattaforme cloud.

## <a name="problem-areas-in-the-cloud"></a>Aree problematiche nel cloud

<!-- markdownlint-disable MD033 -->

<ul id="categories" class="panel">
{%- for category in categories %}
    <li>
    {% include 'pattern-category-card' %}
    </li>
{%- endfor %}
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="catalog-of-patterns"></a>Catalogo dei modelli

| Modello | Summary |
|---------|---------|
|         |         |

{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}
