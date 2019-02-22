---
title: "CAF: gestione dell'accesso alle risorse in Azure"
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: "Spiegazione dei costrutti di gestione dell'accesso alle risorse in Azure: Azure Resource Manager, sottoscrizioni, gruppi di risorse e risorse"
author: petertaylor9999
ms.openlocfilehash: b98cdc94d6d3a37c1e65da1d4de35d5d9520d6eb
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902155"
---
# <a name="resource-access-management-in-azure"></a><span data-ttu-id="d2393-103">gestione dell'accesso alle risorse in Azure</span><span class="sxs-lookup"><span data-stu-id="d2393-103">Resource access management in Azure</span></span>

<span data-ttu-id="d2393-104">[Governance del cloud](../overview.md) illustra le cinque discipline della governance del cloud, che include la gestione delle risorse.</span><span class="sxs-lookup"><span data-stu-id="d2393-104">[Cloud Governance](../overview.md) outlines the five disciplines of Cloud Governance, which includes Resource Management.</span></span>  <span data-ttu-id="d2393-105">[Informazioni sulla governance dell'accesso alle risorse](overview.md) illustra in modo più dettagliato in che modo la gestione dell'accesso alle risorse si integra con la disciplina di gestione delle risorse.</span><span class="sxs-lookup"><span data-stu-id="d2393-105">[What is resource access governance](overview.md) furthers explains how resource access management fits into the resource management discipline.</span></span> <span data-ttu-id="d2393-106">Prima di esaminare come si progetta un modello di governance, è importante comprendere i controlli di gestione dell'accesso alle risorse in Azure.</span><span class="sxs-lookup"><span data-stu-id="d2393-106">Before you move on to learn how to design a governance model, it's important to understand the resource access management controls in Azure.</span></span> <span data-ttu-id="d2393-107">La configurazione di questi controlli di gestione dell'accesso alle risorse costituisce la base del modello di governance.</span><span class="sxs-lookup"><span data-stu-id="d2393-107">The configuration of these resource access management controls forms the basis of your governance model.</span></span>

<span data-ttu-id="d2393-108">Verrà ora esaminata la distribuzione delle risorse in Azure.</span><span class="sxs-lookup"><span data-stu-id="d2393-108">Begin by taking a closer look at how resources are deployed in Azure.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-is-an-azure-resource"></a><span data-ttu-id="d2393-109">Informazioni sulla risorsa in Azure</span><span class="sxs-lookup"><span data-stu-id="d2393-109">What is an Azure resource?</span></span>

<span data-ttu-id="d2393-110">In Azure il termine **risorsa** si riferisce a un'entità gestita da Azure.</span><span class="sxs-lookup"><span data-stu-id="d2393-110">In Azure, the term **resource** refers to an entity managed by Azure.</span></span> <span data-ttu-id="d2393-111">Ad esempio, le macchine virtuali, le reti virtuali e gli account di archiviazione sono tutte risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="d2393-111">For example, virtual machines, virtual networks, and storage accounts are all referred to as Azure resources.</span></span>

<span data-ttu-id="d2393-112">![Diagramma di una risorsa](../../_images/governance-1-9.png)
*Figura 1. Una risorsa.*</span><span class="sxs-lookup"><span data-stu-id="d2393-112">![Diagram of a resource](../../_images/governance-1-9.png)
*Figure 1. A resource.*</span></span>

## <a name="what-is-an-azure-resource-group"></a><span data-ttu-id="d2393-113">Informazioni sul gruppo di risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="d2393-113">What is an Azure resource group?</span></span>

<span data-ttu-id="d2393-114">Ogni risorsa in Azure deve appartenere a un [gruppo di risorse](/azure/azure-resource-manager/resource-group-overview#resource-groups).</span><span class="sxs-lookup"><span data-stu-id="d2393-114">Each resource in Azure must belong to a [resource group](/azure/azure-resource-manager/resource-group-overview#resource-groups).</span></span> <span data-ttu-id="d2393-115">Un gruppo di risorse è semplicemente un costrutto logico che raggruppa più risorse in modo che possano essere gestite come singola entità.</span><span class="sxs-lookup"><span data-stu-id="d2393-115">A resource group is simply a logical construct that groups multiple resources together so they can be managed as a single entity.</span></span> <span data-ttu-id="d2393-116">Ad esempio, le risorse che condividono un ciclo di vita simile, come le risorse per un'[applicazione a più livelli](/azure/architecture/guide/architecture-styles/n-tier), possono essere create o eliminate come gruppo.</span><span class="sxs-lookup"><span data-stu-id="d2393-116">For example, resources that share a similar lifecycle, such as the resources for an [n-tier application](/azure/architecture/guide/architecture-styles/n-tier) may be created or deleted as a group.</span></span>

<span data-ttu-id="d2393-117">![Diagramma di un gruppo di risorse contenente una risorsa](../../_images/governance-1-10.png)
*Figura 2. Un gruppo di risorse contiene una risorsa.*</span><span class="sxs-lookup"><span data-stu-id="d2393-117">![Diagram of a resource group containing a resource](../../_images/governance-1-10.png)
*Figure 2. A resource group contains a resource.*</span></span>

<span data-ttu-id="d2393-118">I gruppi di risorse e le risorse in essi contenute sono associati a una **sottoscrizione** di Azure.</span><span class="sxs-lookup"><span data-stu-id="d2393-118">Resource groups and the resources they contain are associated with an Azure **subscription**.</span></span>

## <a name="what-is-an-azure-subscription"></a><span data-ttu-id="d2393-119">Informazioni sulla sottoscrizione di Azure</span><span class="sxs-lookup"><span data-stu-id="d2393-119">What is an Azure subscription?</span></span>

<span data-ttu-id="d2393-120">Una sottoscrizione di Azure è simile a un gruppo di risorse, in quanto si tratta di un costrutto logico che raggruppa i gruppi di risorse e le loro risorse.</span><span class="sxs-lookup"><span data-stu-id="d2393-120">An Azure subscription is similar to a resource group in that it's a logical construct that groups together resource groups and their resources.</span></span> <span data-ttu-id="d2393-121">Una sottoscrizione di Azure è tuttavia anche associata ai controlli usati da Azure Resource Manager.</span><span class="sxs-lookup"><span data-stu-id="d2393-121">However, an Azure subscription is also associated with the controls used by Azure Resource Manager.</span></span> <span data-ttu-id="d2393-122">Che cosa significa?</span><span class="sxs-lookup"><span data-stu-id="d2393-122">What does this mean?</span></span> <span data-ttu-id="d2393-123">Esaminare Azure Resource Manager per comprendere la sua relazione con una sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="d2393-123">Take a closer look at Azure Resource Manager to learn about the relationship between it and an Azure subscription.</span></span>

<span data-ttu-id="d2393-124">![Diagramma di una sottoscrizione di Azure](../../_images/governance-1-11.png)
*Figura 3. Una sottoscrizione di Azure.*</span><span class="sxs-lookup"><span data-stu-id="d2393-124">![Diagram of an Azure subscription](../../_images/governance-1-11.png)
*Figure 3. An Azure subscription.*</span></span>

## <a name="what-is-azure-resource-manager"></a><span data-ttu-id="d2393-125">Informazioni su Azure Resource Manager</span><span class="sxs-lookup"><span data-stu-id="d2393-125">What is Azure Resource Manager?</span></span>

<span data-ttu-id="d2393-126">In [Funzionamento di Azure](../../getting-started/what-is-azure.md) si è appreso che Azure include un "front-end" con molti servizi che orchestrano tutte le funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="d2393-126">In [how does Azure work?](../../getting-started/what-is-azure.md) you learned that Azure includes a "front end" with many services that orchestrate all the functions of Azure.</span></span> <span data-ttu-id="d2393-127">Uno di questi servizi è [Azure Resource Manager](/azure/azure-resource-manager/), che ospita l'API RESTful usata dai client per gestire le risorse.</span><span class="sxs-lookup"><span data-stu-id="d2393-127">One of these services is [Azure Resource Manager](/azure/azure-resource-manager/), and this service hosts the RESTful API used by clients to manage resources.</span></span>

<span data-ttu-id="d2393-128">![Diagramma di Azure Resource Manager](../../_images/governance-1-12.png)
*Figura 4. Azure Resource Manager.*</span><span class="sxs-lookup"><span data-stu-id="d2393-128">![Diagram of Azure Resource Manager](../../_images/governance-1-12.png)
*Figure 4. Azure Resource Manager.*</span></span>

<span data-ttu-id="d2393-129">La figura seguente mostra tre client: [PowerShell](/powershell/azure/overview), il [portale di Azure](https://portal.azure.com) e l'[interfaccia della riga di comando di Azure](/cli/azure):</span><span class="sxs-lookup"><span data-stu-id="d2393-129">The following figure shows three clients: [PowerShell](/powershell/azure/overview), the [Azure portal](https://portal.azure.com), and the [Azure command-line interface (CLI)](/cli/azure):</span></span>

<span data-ttu-id="d2393-130">![Diagramma di client di Azure che si connettono all'API di Azure Resource Manager](../../_images/governance-1-13.png)
*Figura 5. I client di Azure si connettono all'API RESTful di Azure Resource Manager.*</span><span class="sxs-lookup"><span data-stu-id="d2393-130">![Diagram of Azure clients connecting to the Azure Resource Manager API](../../_images/governance-1-13.png)
*Figure 5. Azure clients connect to the Azure Resource Manager RESTful API.*</span></span>

<span data-ttu-id="d2393-131">Anche se questi client si connettono ad Azure Resource Manager usando l'API RESTful, Azure Resource Manager non include funzionalità per gestire direttamente le risorse.</span><span class="sxs-lookup"><span data-stu-id="d2393-131">While these clients connect to Azure Resource Manager using the RESTful API, Azure Resource Manager does not include functionality to manage resources directly.</span></span> <span data-ttu-id="d2393-132">La maggior parte dei tipi di risorse in Azure ha invece un proprio [**provider di risorse**](/azure/azure-resource-manager/resource-group-overview#terminology).</span><span class="sxs-lookup"><span data-stu-id="d2393-132">Rather, most resource types in Azure have their own [**resource provider**](/azure/azure-resource-manager/resource-group-overview#terminology).</span></span>

<span data-ttu-id="d2393-133">![Provider di risorse di Azure](../../_images/governance-1-14.png)
*Figura 6. Provider di risorse di Azure.*</span><span class="sxs-lookup"><span data-stu-id="d2393-133">![Azure resource providers](../../_images/governance-1-14.png)
*Figure 6. Azure resource providers.*</span></span>

<span data-ttu-id="d2393-134">Quando un client invia una richiesta per gestire una risorsa specifica, Azure Resource Manager si connette al provider di risorse per quel tipo di risorsa per completare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="d2393-134">When a client makes a request to manage a specific resource, Azure Resource Manager connects to the resource provider for that resource type to complete the request.</span></span> <span data-ttu-id="d2393-135">Se, ad esempio, un client invia una richiesta per gestire una risorsa macchina virtuale, Azure Resource Manager si connette al provider di risorse **Microsoft.Compute**.</span><span class="sxs-lookup"><span data-stu-id="d2393-135">For example, if a client makes a request to manage a virtual machine resource, Azure Resource Manager connects to the **Microsoft.Compute** resource provider.</span></span>

<span data-ttu-id="d2393-136">![Azure Resource Manager che si connette al provider di risorse Microsoft.Compute](../../_images/governance-1-15.png)
*Figura 7. Azure Resource Manager si connette al provider di risorse **Microsoft.Compute** per gestire la risorsa specificata nella richiesta del client.*</span><span class="sxs-lookup"><span data-stu-id="d2393-136">![Azure Resource Manager connecting to the Microsoft.Compute resource provider](../../_images/governance-1-15.png)
*Figure 7. Azure Resource Manager connects to the **Microsoft.Compute** resource provider to manage the resource specified in the client request.*</span></span>

<span data-ttu-id="d2393-137">Azure Resource Manager richiede al client di specificare un identificatore sia per la sottoscrizione che per il gruppo di risorse per gestire la risorsa macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="d2393-137">Azure Resource Manager requires the client to specify an identifier for both the subscription and the resource group in order to manage the virtual machine resource.</span></span>

<span data-ttu-id="d2393-138">Ora che si è appreso come funziona Resource Manager, tornare alla trattazione del modo in cui una sottoscrizione di Azure è associata ai controlli usati da Azure Resource Manager.</span><span class="sxs-lookup"><span data-stu-id="d2393-138">Now that you have an understanding of how Azure Resource Manager works, return to the discussion of how an Azure subscription is associated with the controls used by Azure Resource Manager.</span></span> <span data-ttu-id="d2393-139">Prima che qualsiasi richiesta di gestione delle risorse possa essere eseguita da Azure Resource Manager, vengono effettuati alcuni controlli.</span><span class="sxs-lookup"><span data-stu-id="d2393-139">Before any resource management request can be executed by Azure Resource Manager, a set of controls are checked.</span></span>

<span data-ttu-id="d2393-140">Il primo controllo è che una richiesta venga eseguita da un utente convalidato e che Azure Resource Manager abbia una relazione di trust con [Azure Active Directory](/azure/active-directory/) (Azure AD) perché possa rendere disponibile la funzionalità di identità utente.</span><span class="sxs-lookup"><span data-stu-id="d2393-140">The first control is that a request must be made by a validated user, and Azure Resource Manager has a trusted relationship with [Azure Active Directory (Azure AD)](/azure/active-directory/) to provide user identity functionality.</span></span>

<span data-ttu-id="d2393-141">![Azure Active Directory](../../_images/governance-1-16.png)
*Figura 8. Azure Active Directory.*</span><span class="sxs-lookup"><span data-stu-id="d2393-141">![Azure Active Directory](../../_images/governance-1-16.png)
*Figure 8. Azure Active Directory.*</span></span>

<span data-ttu-id="d2393-142">Gli utenti di Azure AD sono segmentati in **tenant**.</span><span class="sxs-lookup"><span data-stu-id="d2393-142">In Azure AD, users are segmented into **tenants**.</span></span> <span data-ttu-id="d2393-143">Un tenant è un costrutto logico che rappresenta un'istanza sicura e dedicata di Azure AD, generalmente associata a un'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="d2393-143">A tenant is a logical construct that represents a secure, dedicated instance of Azure AD typically associated with an organization.</span></span> <span data-ttu-id="d2393-144">Ogni sottoscrizione è associata a un tenant di Azure AD.</span><span class="sxs-lookup"><span data-stu-id="d2393-144">Each subscription is associated with an Azure AD tenant.</span></span>

<span data-ttu-id="d2393-145">![Un tenant di Azure AD associato a una sottoscrizione](../../_images/governance-1-17.png)
*Figura 9. Un tenant di Azure AD è associato a una sottoscrizione.*</span><span class="sxs-lookup"><span data-stu-id="d2393-145">![An Azure AD tenant associated with a subscription](../../_images/governance-1-17.png)
*Figure 9. An Azure AD tenant associated with a subscription.*</span></span>

<span data-ttu-id="d2393-146">Per ogni richiesta del client di gestire una risorsa in una specifica sottoscrizione è necessario che l'utente abbia un account nel tenant di Azure AD associato.</span><span class="sxs-lookup"><span data-stu-id="d2393-146">Each client request to manage a resource in a particular subscription requires that the user has an account in the associated Azure AD tenant.</span></span>

<span data-ttu-id="d2393-147">Il controllo successivo consiste nel verificare che l'utente abbia autorizzazioni sufficienti per effettuare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="d2393-147">The next control is a check that the user has sufficient permission to make the request.</span></span> <span data-ttu-id="d2393-148">Le autorizzazioni vengono assegnate agli utenti tramite il [controllo degli accessi in base al ruolo](/azure/role-based-access-control/).</span><span class="sxs-lookup"><span data-stu-id="d2393-148">Permissions are assigned to users using [role-based access control (RBAC)](/azure/role-based-access-control/).</span></span>

<span data-ttu-id="d2393-149">![Utenti assegnati a ruoli di controllo degli accessi in base al ruolo](../../_images/governance-1-18.png)
*Figura 10. A ogni utente del tenant vengono assegnati uno o più ruoli di controllo degli accessi in base al ruolo.*</span><span class="sxs-lookup"><span data-stu-id="d2393-149">![Users assigned to RBAC roles](../../_images/governance-1-18.png)
*Figure 10. Each user in the tenant is assigned one or more RBAC roles.*</span></span>

<span data-ttu-id="d2393-150">Un ruolo di controllo degli accessi in base al ruolo specifica un insieme di autorizzazioni che un utente può avere per una risorsa specifica.</span><span class="sxs-lookup"><span data-stu-id="d2393-150">An RBAC role specifies a set of permissions a user may take on a specific resource.</span></span> <span data-ttu-id="d2393-151">Quando il ruolo viene assegnato all'utente, tali autorizzazioni vengono applicate.</span><span class="sxs-lookup"><span data-stu-id="d2393-151">When the role is assigned to the user, those permissions are applied.</span></span> <span data-ttu-id="d2393-152">Ad esempio, il ruolo di [**proprietario** predefinito](/azure/role-based-access-control/built-in-roles#owner) consente all'utente di eseguire qualsiasi azione su una risorsa.</span><span class="sxs-lookup"><span data-stu-id="d2393-152">For example, the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner) allows a user to perform any action on a resource.</span></span>

<span data-ttu-id="d2393-153">Il controllo successivo verifica che la richiesta sia consentita nelle impostazioni specificate per i [criteri delle risorse di Azure](/azure/governance/policy/).</span><span class="sxs-lookup"><span data-stu-id="d2393-153">The next control is a check that the request is allowed under the settings specified for [Azure resource policy](/azure/governance/policy/).</span></span> <span data-ttu-id="d2393-154">I criteri delle risorse di Azure specificano le operazioni consentite per una determinata risorsa.</span><span class="sxs-lookup"><span data-stu-id="d2393-154">Azure resource policies specify the operations allowed for a specific resource.</span></span> <span data-ttu-id="d2393-155">Ad esempio, un criterio delle risorse di Azure può specificare che agli utenti sia consentito distribuire solo un tipo specifico di macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="d2393-155">For example, an Azure resource policy can specify that users are only allowed to deploy a specific type of virtual machine.</span></span>

<span data-ttu-id="d2393-156">![Criteri delle risorse di Azure](../../_images/governance-1-19.png)
*Figura 11. Criteri delle risorse di Azure.*</span><span class="sxs-lookup"><span data-stu-id="d2393-156">![Azure resource policy](../../_images/governance-1-19.png)
*Figure 11. Azure resource policy.*</span></span>

<span data-ttu-id="d2393-157">Il controllo successivo consiste nel verificare che la richiesta non superi un [limite della sottoscrizione di Azure](/azure/azure-subscription-service-limits).</span><span class="sxs-lookup"><span data-stu-id="d2393-157">The next control is a check that the request does not exceed an [Azure subscription limit](/azure/azure-subscription-service-limits).</span></span> <span data-ttu-id="d2393-158">Ad esempio, ogni sottoscrizione ha un limite di 980 gruppi di risorse.</span><span class="sxs-lookup"><span data-stu-id="d2393-158">For example, each subscription has a limit of 980 resource groups per subscription.</span></span> <span data-ttu-id="d2393-159">Se si riceve una richiesta di distribuzione di un gruppo di risorse aggiuntivo dopo aver raggiunto il limite, questa richiesta verrà rifiutata.</span><span class="sxs-lookup"><span data-stu-id="d2393-159">If a request is received to deploy an additional resource group once the limit has been reached, it is denied.</span></span>

<span data-ttu-id="d2393-160">![Limiti delle risorse di Azure](../../_images/governance-1-20.png)
*Figura 12. Limiti delle risorse di Azure.*</span><span class="sxs-lookup"><span data-stu-id="d2393-160">![Azure resource limits](../../_images/governance-1-20.png)
*Figure 12. Azure resource limits.*</span></span>

<span data-ttu-id="d2393-161">Il controllo finale verifica che la richiesta rientri nell'impegno finanziario associato alla sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="d2393-161">The final control is a check that the request is within the financial commitment associated with the subscription.</span></span> <span data-ttu-id="d2393-162">Se, ad esempio, la richiesta riguarda la distribuzione di una macchina virtuale, Azure Resource Manager verifica che la sottoscrizione contenga informazioni di pagamento sufficienti.</span><span class="sxs-lookup"><span data-stu-id="d2393-162">For example, if the request is to deploy a virtual machine, Azure Resource Manager verifies that the subscription has sufficient payment information.</span></span>

<span data-ttu-id="d2393-163">![A una sottoscrizione è associato un impegno finanziario](../../_images/governance-1-21.png)
*Figura 13. A una sottoscrizione è associato un impegno finanziario.*</span><span class="sxs-lookup"><span data-stu-id="d2393-163">![A financial commitment associated with a subscription](../../_images/governance-1-21.png)
*Figure 13. A financial commitment is associated with a subscription.*</span></span>

## <a name="summary"></a><span data-ttu-id="d2393-164">Summary</span><span class="sxs-lookup"><span data-stu-id="d2393-164">Summary</span></span>

<span data-ttu-id="d2393-165">In questo articolo si è appreso come viene gestito l'accesso alle risorse in Azure con Azure Resource Manager.</span><span class="sxs-lookup"><span data-stu-id="d2393-165">In this article, you learned about how resource access is managed in Azure using Azure Resource Manager.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d2393-166">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="d2393-166">Next steps</span></span>

<span data-ttu-id="d2393-167">Dopo aver compreso come gestire l'accesso alle risorse in Azure, è necessario apprendere come progettare un modello di governance [per un carico di lavoro semplice](governance-simple-workload.md) o per [più team](governance-multiple-teams.md) usando questi servizi.</span><span class="sxs-lookup"><span data-stu-id="d2393-167">Now that you understand how to manage resource access in Azure, move on to learn how to design a governance model [for a simple workload](governance-simple-workload.md) or for [multiple teams](governance-multiple-teams.md) using these services.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d2393-168">Panoramica della governance</span><span class="sxs-lookup"><span data-stu-id="d2393-168">An overview of governance</span></span>](../overview.md)
