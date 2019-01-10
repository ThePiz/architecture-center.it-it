---
title: Modelli di protezione
titleSuffix: Cloud Design Patterns
description: La sicurezza è la capacità di un sistema di impedire azioni dannose o accidentali al di fuori dell'utilizzo designato e di impedire la divulgazione o la perdita di informazioni. Le applicazioni cloud sono esposte in Internet fuori dai limiti locali attendibili, spesso sono aperte al pubblico e possono essere usate da utenti non attendibili. Le applicazioni devono essere progettate e distribuite in modo da proteggerle da attacchi dannosi, limitare l'accesso solo agli utenti approvati e proteggere i dati sensibili.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.custom: seodec18
ms.openlocfilehash: 259d4bd5178ca8ab85df0cd03e5268c5a7845a3a
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54009441"
---
# <a name="security-patterns"></a><span data-ttu-id="9bf9e-106">Modelli di protezione</span><span class="sxs-lookup"><span data-stu-id="9bf9e-106">Security patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="9bf9e-107">La sicurezza è la capacità di un sistema di impedire azioni dannose o accidentali al di fuori dell'utilizzo designato e di impedire la divulgazione o la perdita di informazioni.</span><span class="sxs-lookup"><span data-stu-id="9bf9e-107">Security is the capability of a system to prevent malicious or accidental actions outside of the designed usage, and to prevent disclosure or loss of information.</span></span> <span data-ttu-id="9bf9e-108">Le applicazioni cloud sono esposte in Internet fuori dai limiti locali attendibili, spesso sono aperte al pubblico e possono essere usate da utenti non attendibili.</span><span class="sxs-lookup"><span data-stu-id="9bf9e-108">Cloud applications are exposed on the Internet outside trusted on-premises boundaries, are often open to the public, and may serve untrusted users.</span></span> <span data-ttu-id="9bf9e-109">Le applicazioni devono essere progettate e distribuite in modo da proteggerle da attacchi dannosi, limitare l'accesso solo agli utenti approvati e proteggere i dati sensibili.</span><span class="sxs-lookup"><span data-stu-id="9bf9e-109">Applications must be designed and deployed in a way that protects them from malicious attacks, restricts access to only approved users, and protects sensitive data.</span></span>

|                    <span data-ttu-id="9bf9e-110">Modello</span><span class="sxs-lookup"><span data-stu-id="9bf9e-110">Pattern</span></span>                     |                                                                                                         <span data-ttu-id="9bf9e-111">Summary</span><span class="sxs-lookup"><span data-stu-id="9bf9e-111">Summary</span></span>                                                                                                         |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [<span data-ttu-id="9bf9e-112">Identità federativa</span><span class="sxs-lookup"><span data-stu-id="9bf9e-112">Federated Identity</span></span>](../federated-identity.md) |                                                                                <span data-ttu-id="9bf9e-113">È possibile delegare l'autenticazione a un provider di identità esterno.</span><span class="sxs-lookup"><span data-stu-id="9bf9e-113">Delegate authentication to an external identity provider.</span></span>                                                                                |
|         [<span data-ttu-id="9bf9e-114">Gatekeeper</span><span class="sxs-lookup"><span data-stu-id="9bf9e-114">Gatekeeper</span></span>](../gatekeeper.md)         | <span data-ttu-id="9bf9e-115">Proteggere le applicazioni e i servizi usando un'istanza host dedicata che funga da broker tra i client e l'applicazione o il servizio, convalidi e purifichi le richieste e passi le richieste e i dati tra di essi.</span><span class="sxs-lookup"><span data-stu-id="9bf9e-115">Protect applications and services by using a dedicated host instance that acts as a broker between clients and the application or service, validates and sanitizes requests, and passes requests and data between them.</span></span> |
|          [<span data-ttu-id="9bf9e-116">Passepartout</span><span class="sxs-lookup"><span data-stu-id="9bf9e-116">Valet Key</span></span>](../valet-key.md)          |                                                        <span data-ttu-id="9bf9e-117">Usare un token o una chiave che fornisca ai client l'accesso diretto limitato a una specifica risorsa o a un servizio.</span><span class="sxs-lookup"><span data-stu-id="9bf9e-117">Use a token or key that provides clients with restricted direct access to a specific resource or service.</span></span>                                                        |
