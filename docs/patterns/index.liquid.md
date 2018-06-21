---
title: Modelli di progettazione cloud
description: Schemi progettuali del cloud per Microsoft Azure
keywords: Azure
ms.openlocfilehash: 4747c896fc6fc5866be782d76c5290d6b49ad451
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30848258"
---
# <a name="cloud-design-patterns"></a>Modelli di progettazione cloud

[!INCLUDE [header](../../_includes/header.md)]

Questi schemi progettuali sono utili per la compilazione di applicazioni affidabili, scalabili e sicure nel cloud.

Ogni schema descrive il problema che risolve, le considerazioni per l'applicazione dello schema e un esempio basato su Microsoft Azure. La maggior parte degli schemi include esempi o frammenti di codice che illustrano come implementare lo schema in Azure. Tuttavia, la maggior parte degli schemi è pertinente a qualsiasi sistema distribuito, sia in hosting su Azure sia su altre piattaforme cloud.

## <a name="problem-areas-in-the-cloud"></a>Aree problematiche nel cloud

<ul id="categories" class="panel">
{%- for category in categories %}
    <li>
    {% include 'pattern-category-card' %}
    </li>
{%- endfor %}
</ul>

## <a name="catalog-of-patterns"></a>Catalogo dei modelli

| Modello | Summary |
|---------|---------|
|         |         |

{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}
