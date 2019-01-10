---
title: Modelli di progettazione cloud
titleSuffix: Azure Architecture Center
description: Schemi progettuali del cloud per Microsoft Azure
keywords: Azure
author: dragon119
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 873d4cf02690a2cc3ffe4f35b044dedf70700fb5
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54011024"
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
