---
title: Modelli di protezione
description: La sicurezza è la capacità di un sistema di impedire azioni dannose o accidentali al di fuori dell'utilizzo designato e di impedire la divulgazione o la perdita di informazioni. Le applicazioni cloud sono esposte in Internet fuori dai limiti locali attendibili, spesso sono aperte al pubblico e possono essere usate da utenti non attendibili. Le applicazioni devono essere progettate e distribuite in modo da proteggerle da attacchi dannosi, limitare l'accesso solo agli utenti approvati e proteggere i dati sensibili.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: 8437b8dfef751226580437a1b5678ca0e0e71f18
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30846999"
---
# <a name="security-patterns"></a><span data-ttu-id="a3b07-106">Modelli di protezione</span><span class="sxs-lookup"><span data-stu-id="a3b07-106">Security patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="a3b07-107">La sicurezza è la capacità di un sistema di impedire azioni dannose o accidentali al di fuori dell'utilizzo designato e di impedire la divulgazione o la perdita di informazioni.</span><span class="sxs-lookup"><span data-stu-id="a3b07-107">Security is the capability of a system to prevent malicious or accidental actions outside of the designed usage, and to prevent disclosure or loss of information.</span></span> <span data-ttu-id="a3b07-108">Le applicazioni cloud sono esposte in Internet fuori dai limiti locali attendibili, spesso sono aperte al pubblico e possono essere usate da utenti non attendibili.</span><span class="sxs-lookup"><span data-stu-id="a3b07-108">Cloud applications are exposed on the Internet outside trusted on-premises boundaries, are often open to the public, and may serve untrusted users.</span></span> <span data-ttu-id="a3b07-109">Le applicazioni devono essere progettate e distribuite in modo da proteggerle da attacchi dannosi, limitare l'accesso solo agli utenti approvati e proteggere i dati sensibili.</span><span class="sxs-lookup"><span data-stu-id="a3b07-109">Applications must be designed and deployed in a way that protects them from malicious attacks, restricts access to only approved users, and protects sensitive data.</span></span>


|                    <span data-ttu-id="a3b07-110">Modello</span><span class="sxs-lookup"><span data-stu-id="a3b07-110">Pattern</span></span>                     |                                                                                                         <span data-ttu-id="a3b07-111">Summary</span><span class="sxs-lookup"><span data-stu-id="a3b07-111">Summary</span></span>                                                                                                         |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [<span data-ttu-id="a3b07-112">Identità federativa</span><span class="sxs-lookup"><span data-stu-id="a3b07-112">Federated Identity</span></span>](../federated-identity.md) |                                                                                <span data-ttu-id="a3b07-113">È possibile delegare l'autenticazione a un provider di identità esterno.</span><span class="sxs-lookup"><span data-stu-id="a3b07-113">Delegate authentication to an external identity provider.</span></span>                                                                                |
|         [<span data-ttu-id="a3b07-114">Gatekeeper</span><span class="sxs-lookup"><span data-stu-id="a3b07-114">Gatekeeper</span></span>](../gatekeeper.md)         | <span data-ttu-id="a3b07-115">Proteggere le applicazioni e i servizi usando un'istanza host dedicata che funga da broker tra i client e l'applicazione o il servizio, convalidi e purifichi le richieste e passi le richieste e i dati tra di essi.</span><span class="sxs-lookup"><span data-stu-id="a3b07-115">Protect applications and services by using a dedicated host instance that acts as a broker between clients and the application or service, validates and sanitizes requests, and passes requests and data between them.</span></span> |
|          [<span data-ttu-id="a3b07-116">Passepartout</span><span class="sxs-lookup"><span data-stu-id="a3b07-116">Valet Key</span></span>](../valet-key.md)          |                                                        <span data-ttu-id="a3b07-117">Usare un token o una chiave che fornisca ai client l'accesso diretto limitato a una specifica risorsa o a un servizio.</span><span class="sxs-lookup"><span data-stu-id="a3b07-117">Use a token or key that provides clients with restricted direct access to a specific resource or service.</span></span>                                                        |

