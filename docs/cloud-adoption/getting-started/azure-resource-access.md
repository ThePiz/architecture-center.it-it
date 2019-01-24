---
title: "Adozione del cloud nell'organizzazione: gestione dell'accesso alle risorse in Azure"
description: "Spiegazione dei costrutti di gestione dell'accesso alle risorse in Azure: Azure Resource Manager, sottoscrizioni, gruppi di risorse e risorse"
author: petertaylor9999
ms.date: 09/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 9f1d28cfeb062f44b2c58cd52c58f163d6de5b9d
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54483072"
---
# <a name="enterprise-cloud-adoption-resource-access-management-in-azure"></a><span data-ttu-id="33341-103">Adozione del cloud nell'organizzazione: gestione dell'accesso alle risorse in Azure</span><span class="sxs-lookup"><span data-stu-id="33341-103">Enterprise Cloud Adoption: Resource access management in Azure</span></span>

<span data-ttu-id="33341-104">In [Informazioni sulla governance delle risorse](what-is-governance.md) si è appreso che per governance si intende il processo continuativo di gestione, monitoraggio e controllo dell'uso delle risorse di Azure per soddisfare gli obiettivi e i requisiti dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="33341-104">In [what is resource governance?](what-is-governance.md), you learned that governance refers to the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the goals and requirements of your organization.</span></span> <span data-ttu-id="33341-105">Prima di esaminare come si progetta un modello di governance, è importante comprendere i controlli di gestione dell'accesso alle risorse in Azure.</span><span class="sxs-lookup"><span data-stu-id="33341-105">Before you move on to learn how to design a governance model, it's important to understand the resource access management controls in Azure.</span></span> <span data-ttu-id="33341-106">La configurazione di questi controlli di gestione dell'accesso alle risorse costituisce la base del modello di governance.</span><span class="sxs-lookup"><span data-stu-id="33341-106">The configuration of these resource access management controls forms the basis of your governance model.</span></span>

<span data-ttu-id="33341-107">Verrà ora esaminata la distribuzione delle risorse in Azure.</span><span class="sxs-lookup"><span data-stu-id="33341-107">Let's begin by taking a closer look at how resources are deployed in Azure.</span></span> 

## <a name="what-is-an-azure-resource"></a><span data-ttu-id="33341-108">Informazioni sulla risorsa in Azure</span><span class="sxs-lookup"><span data-stu-id="33341-108">What is an Azure resource?</span></span>

<span data-ttu-id="33341-109">In Azure il termine **risorsa** si riferisce a un'entità gestita da Azure.</span><span class="sxs-lookup"><span data-stu-id="33341-109">In Azure, the term **resource** refers to an entity managed by Azure.</span></span> <span data-ttu-id="33341-110">Ad esempio, le macchine virtuali, le reti virtuali e gli account di archiviazione sono tutte risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="33341-110">For example, virtual machines, virtual networks, and storage accounts are all referred to as Azure resources.</span></span>

![](../_images/governance-1-9.png)   
<span data-ttu-id="33341-111">*Figura 1. Una risorsa.*</span><span class="sxs-lookup"><span data-stu-id="33341-111">*Figure 1. A resource.*</span></span>

## <a name="what-is-an-azure-resource-group"></a><span data-ttu-id="33341-112">Informazioni sul gruppo di risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="33341-112">What is an Azure resource group?</span></span>

<span data-ttu-id="33341-113">Ogni risorsa in Azure deve appartenere a un [gruppo di risorse](/azure/azure-resource-manager/resource-group-overview#resource-groups).</span><span class="sxs-lookup"><span data-stu-id="33341-113">Each resource in Azure must belong to a [resource group](/azure/azure-resource-manager/resource-group-overview#resource-groups).</span></span> <span data-ttu-id="33341-114">Un gruppo di risorse è semplicemente un costrutto logico che raggruppa più risorse in modo che possano essere gestite come singola entità.</span><span class="sxs-lookup"><span data-stu-id="33341-114">A resource group is simply a logical construct that groups multiple resources together so they can be managed as a single entity.</span></span> <span data-ttu-id="33341-115">Ad esempio, le risorse che condividono un ciclo di vita simile, come le risorse per un'[applicazione a più livelli](/azure/architecture/guide/architecture-styles/n-tier), possono essere create o eliminate come gruppo.</span><span class="sxs-lookup"><span data-stu-id="33341-115">For example, resources that share a similar lifecycle, such as the resources for an [n-tier application](/azure/architecture/guide/architecture-styles/n-tier) may be created or deleted as a group.</span></span> 

![](../_images/governance-1-10.png)   
<span data-ttu-id="33341-116">*Figura 2. Un gruppo di risorse contiene una risorsa.*</span><span class="sxs-lookup"><span data-stu-id="33341-116">*Figure 2. A resource group contains a resource.*</span></span> 

<span data-ttu-id="33341-117">I gruppi di risorse e le risorse in essi contenute sono associati a una **sottoscrizione** di Azure.</span><span class="sxs-lookup"><span data-stu-id="33341-117">Resource groups and the resources they contain are associated with an Azure **subscription**.</span></span> 

## <a name="what-is-an-azure-subscription"></a><span data-ttu-id="33341-118">Informazioni sulla sottoscrizione di Azure</span><span class="sxs-lookup"><span data-stu-id="33341-118">What is an Azure subscription?</span></span>

<span data-ttu-id="33341-119">Una sottoscrizione di Azure è simile a un gruppo di risorse, in quanto si tratta di un costrutto logico che raggruppa i gruppi di risorse e le loro risorse.</span><span class="sxs-lookup"><span data-stu-id="33341-119">An Azure subscription is similar to a resource group in that it's a logical construct that groups together resource groups and their resources.</span></span> <span data-ttu-id="33341-120">Una sottoscrizione di Azure è tuttavia anche associata ai controlli usati da Azure Resource Manager.</span><span class="sxs-lookup"><span data-stu-id="33341-120">However, an Azure subscription is also associated with the controls used by Azure resource manager.</span></span> <span data-ttu-id="33341-121">Che cosa significa?</span><span class="sxs-lookup"><span data-stu-id="33341-121">What does this mean?</span></span> <span data-ttu-id="33341-122">Si esaminerà ora Azure Resource Manager per comprendere la sua relazione con una sottoscrizione di Azure.</span><span class="sxs-lookup"><span data-stu-id="33341-122">Let's take a closer look at Azure resource manager to learn about the relationship between it and an Azure subscription.</span></span>

![](../_images/governance-1-11.png)   
<span data-ttu-id="33341-123">*Figura 3. Una sottoscrizione di Azure.*</span><span class="sxs-lookup"><span data-stu-id="33341-123">*Figure 3. An Azure subscription.*</span></span>

## <a name="what-is-azure-resource-manager"></a><span data-ttu-id="33341-124">Informazioni su Azure Resource Manager</span><span class="sxs-lookup"><span data-stu-id="33341-124">What is Azure resource manager?</span></span>

<span data-ttu-id="33341-125">In [Funzionamento di Azure](what-is-azure.md) si è appreso che Azure include un "front-end" con molti servizi che orchestrano tutte le funzioni di Azure.</span><span class="sxs-lookup"><span data-stu-id="33341-125">In [how does Azure work?](what-is-azure.md) you learned that Azure includes a "front end" with many services that orchestrate all the functions of Azure.</span></span> <span data-ttu-id="33341-126">Uno di questi servizi è [Azure Resource Manager](/azure/azure-resource-manager/), che ospita l'API RESTful usata dai client per gestire le risorse.</span><span class="sxs-lookup"><span data-stu-id="33341-126">One of these services is [Azure resource manager](/azure/azure-resource-manager/), and this service hosts the RESTful API used by clients to manage resources.</span></span> 

![](../_images/governance-1-12.png)   
<span data-ttu-id="33341-127">*Figura 4. Azure Resource Manager.*</span><span class="sxs-lookup"><span data-stu-id="33341-127">*Figure 4. Azure resource manager.*</span></span>

<span data-ttu-id="33341-128">La figura seguente mostra tre client: [PowerShell](/powershell/azure/overview), il [portale di Azure](https://portal.azure.com) e l'[interfaccia della riga di comando di Azure](/cli/azure).</span><span class="sxs-lookup"><span data-stu-id="33341-128">The following figure shows three clients: [Powershell](/powershell/azure/overview), [the Azure portal](https://portal.azure.com), and the [Azure command line interface (CLI)](/cli/azure):</span></span>

![](../_images/governance-1-13.png)   
<span data-ttu-id="33341-129">*Figura 5. I client di Azure si connettono all'API RESTful di Azure Resource Manager.*</span><span class="sxs-lookup"><span data-stu-id="33341-129">*Figure 5. Azure clients connect to the Azure resource manager RESTful API.*</span></span>

<span data-ttu-id="33341-130">Anche se questi client si connettono ad Azure Resource Manager usando l'API RESTful, Azure Resource Manager non include funzionalità per gestire direttamente le risorse.</span><span class="sxs-lookup"><span data-stu-id="33341-130">While these clients connect to Azure resource manager using the RESTful API, Azure resource manager does not include functionality to manage resources directly.</span></span> <span data-ttu-id="33341-131">La maggior parte dei tipi di risorse in Azure ha invece un proprio [**provider di risorse**](/azure/azure-resource-manager/resource-group-overview#terminology).</span><span class="sxs-lookup"><span data-stu-id="33341-131">Rather, most resource types in Azure have their own [**resource provider**](/azure/azure-resource-manager/resource-group-overview#terminology).</span></span> 

![](../_images/governance-1-14.png)   
<span data-ttu-id="33341-132">*Figura 6. Provider di risorse di Azure.*</span><span class="sxs-lookup"><span data-stu-id="33341-132">*Figure 6. Azure resource providers.*</span></span>

<span data-ttu-id="33341-133">Quando un client invia una richiesta per gestire una risorsa specifica, Azure Resource Manager si connette al provider di risorse per quel tipo di risorsa per completare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="33341-133">When a client makes a request to manage a specific resource, Azure resource manager connects to the resource provider for that resource type to complete the request.</span></span> <span data-ttu-id="33341-134">Se ad esempio un client invia una richiesta per gestire una risorsa di macchina virtuale, Azure Resource Manager si connette al provider di risorse **Microsoft.compute**.</span><span class="sxs-lookup"><span data-stu-id="33341-134">For example, if a client makes a request to manage a virtual machine resource, Azure resource manager connects to the **Microsoft.compute** resource provider.</span></span> 

![](../_images/governance-1-15.png)   
<span data-ttu-id="33341-135">*Figura 7. Azure Resource Manager si connette al provider di risorse **Microsoft.compute** per gestire la risorsa specificata nella richiesta del client.*</span><span class="sxs-lookup"><span data-stu-id="33341-135">*Figure 7. Azure resource manager connects to the **Microsoft.compute** resource provider to manage the resource specified in the client request.*</span></span>

<span data-ttu-id="33341-136">Azure Resource Manager richiede al client di specificare un identificatore sia per la sottoscrizione che per il gruppo di risorse per gestire la risorsa di macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="33341-136">Azure resource manager requires the client to specify an identifier for both the subscription and the resource group in order to manage the virtual machine resource.</span></span> 

<span data-ttu-id="33341-137">Ora che si è appreso come funziona Azure Resource Manager, verrà illustrato il modo in cui la sottoscrizione di Azure è associata ai controlli usati da Azure Resource Manager.</span><span class="sxs-lookup"><span data-stu-id="33341-137">Now that you have an understanding of how Azure resource manager works, let's return to our discussion of how an Azure subscription is associated with the controls used by Azure resource manager.</span></span> <span data-ttu-id="33341-138">Prima che qualsiasi richiesta di gestione delle risorse possa essere eseguita da Azure Resource Manager, vengono verificati alcuni controlli.</span><span class="sxs-lookup"><span data-stu-id="33341-138">Before any resource management request can be executed by Azure resource manager, a set of controls are checked.</span></span> 

<span data-ttu-id="33341-139">Il primo controllo è che una richiesta deve essere eseguita da un utente convalidato e Azure Resource Manager ha una relazione di trust con [Azure Active Directory (Azure AD)](/azure/active-directory/) per fornire la funzionalità di identità utente.</span><span class="sxs-lookup"><span data-stu-id="33341-139">The first control is that a request must be made by a validated user, and Azure resource manager has a trusted relationship with [Azure Active Directory (Azure AD)](/azure/active-directory/) to provide user identity functionality.</span></span>

![](../_images/governance-1-16.png)   
<span data-ttu-id="33341-140">*Figura 8. Azure Active Directory.*</span><span class="sxs-lookup"><span data-stu-id="33341-140">*Figure 8. Azure Active Directory.*</span></span>

<span data-ttu-id="33341-141">Gli utenti di Azure AD sono segmentati in **tenant**.</span><span class="sxs-lookup"><span data-stu-id="33341-141">In Azure AD, users are segmented into **tenants**.</span></span> <span data-ttu-id="33341-142">Un tenant è un costrutto logico che rappresenta un'istanza sicura e dedicata di Azure AD, generalmente associata a un'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="33341-142">A tenant is a logical construct that represents a secure, dedicated instance of Azure AD typically associated with an organization.</span></span> <span data-ttu-id="33341-143">Ogni sottoscrizione è associata a un tenant di Azure AD.</span><span class="sxs-lookup"><span data-stu-id="33341-143">Each subscription is associated with an Azure AD tenant.</span></span>

![](../_images/governance-1-17.png)   
<span data-ttu-id="33341-144">*Figura 9. Un tenant di Azure AD è associato a una sottoscrizione.*</span><span class="sxs-lookup"><span data-stu-id="33341-144">*Figure 9. An Azure AD tenant associated with a subscription.*</span></span>

<span data-ttu-id="33341-145">Per ogni richiesta del client di gestire una risorsa in una specifica sottoscrizione è necessario che l'utente abbia un account nel tenant di Azure AD associato.</span><span class="sxs-lookup"><span data-stu-id="33341-145">Each client request to manage a resource in a particular subscription requires that the user has an account in the associated Azure AD tenant.</span></span> 

<span data-ttu-id="33341-146">Il controllo successivo consiste nel verificare che l'utente abbia autorizzazioni sufficienti per effettuare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="33341-146">The next control is a check that the user has sufficient permission to make the request.</span></span> <span data-ttu-id="33341-147">Le autorizzazioni vengono assegnate agli utenti tramite il [controllo degli accessi in base al ruolo](/azure/role-based-access-control/).</span><span class="sxs-lookup"><span data-stu-id="33341-147">Permissions are assigned to users using [role-based access control (RBAC)](/azure/role-based-access-control/).</span></span>

![](../_images/governance-1-18.png)   
<span data-ttu-id="33341-148">*Figura 10. A ogni utente del tenant vengono assegnati uno o più ruoli di controllo degli accessi in base al ruolo.*</span><span class="sxs-lookup"><span data-stu-id="33341-148">*Figure 10. Each user in the tenant is assigned one or more RBAC roles.*</span></span>

<span data-ttu-id="33341-149">Un ruolo di controllo degli accessi in base al ruolo specifica un insieme di autorizzazioni che un utente può avere per una risorsa specifica.</span><span class="sxs-lookup"><span data-stu-id="33341-149">An RBAC role specifies a set of permissions a user may take on a specific resource.</span></span> <span data-ttu-id="33341-150">Quando il ruolo viene assegnato all'utente, tali autorizzazioni vengono applicate.</span><span class="sxs-lookup"><span data-stu-id="33341-150">When the role is assigned to the user, those permissions are applied.</span></span> <span data-ttu-id="33341-151">Ad esempio, il ruolo di [**proprietario** predefinito](/azure/role-based-access-control/built-in-roles#owner) consente all'utente di eseguire qualsiasi azione su una risorsa.</span><span class="sxs-lookup"><span data-stu-id="33341-151">For example, the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner) allows a user to perform any action on a resource.</span></span>

<span data-ttu-id="33341-152">Il controllo successivo verifica che la richiesta sia consentita nelle impostazioni specificate per i [criteri delle risorse di Azure](/azure/governance/policy/).</span><span class="sxs-lookup"><span data-stu-id="33341-152">The next control is a check that the request is allowed under the settings specified for [Azure resource policy](/azure/governance/policy/).</span></span> <span data-ttu-id="33341-153">I criteri delle risorse di Azure specificano le operazioni consentite per una determinata risorsa.</span><span class="sxs-lookup"><span data-stu-id="33341-153">Azure resource policies specify the operations allowed for a specific resource.</span></span> <span data-ttu-id="33341-154">Ad esempio, un criterio delle risorse di Azure può specificare che agli utenti sia consentito distribuire solo un tipo specifico di macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="33341-154">For example, an Azure resource policy can specify that users are only allowed to deploy a specific type of virtual machine.</span></span>

![](../_images/governance-1-19.png)   
<span data-ttu-id="33341-155">*Figura 11. Criteri delle risorse di Azure.*</span><span class="sxs-lookup"><span data-stu-id="33341-155">*Figure 11. Azure resource policy.*</span></span>

<span data-ttu-id="33341-156">Il controllo successivo consiste nel verificare che la richiesta non superi un [limite della sottoscrizione di Azure](/azure/azure-subscription-service-limits).</span><span class="sxs-lookup"><span data-stu-id="33341-156">The next control is a check that the request does not exceed an [Azure subscription limit](/azure/azure-subscription-service-limits).</span></span> <span data-ttu-id="33341-157">Ad esempio, ogni sottoscrizione ha un limite di 980 gruppi di risorse.</span><span class="sxs-lookup"><span data-stu-id="33341-157">For example, each subscription has a limit of 980 resource groups per subscription.</span></span> <span data-ttu-id="33341-158">Se si riceve una richiesta di distribuzione di un gruppo di risorse aggiuntivo dopo aver raggiunto il limite, questa richiesta verrà rifiutata.</span><span class="sxs-lookup"><span data-stu-id="33341-158">If a request is received to deploy an additional resource group once the limit has been reached, it is denied.</span></span>

![](../_images/governance-1-20.png)   
<span data-ttu-id="33341-159">*Figura 12. Limiti delle risorse di Azure.*</span><span class="sxs-lookup"><span data-stu-id="33341-159">*Figure 12. Azure resource limits.*</span></span> 

<span data-ttu-id="33341-160">Il controllo finale verifica che la richiesta rientri nell'impegno finanziario associato alla sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="33341-160">The final control is a check that the request is within the financial commitment associated with the subscription.</span></span> <span data-ttu-id="33341-161">Ad esempio, se la richiesta riguarda la distribuzione di una macchina virtuale, Azure Resource Manager verifica che la sottoscrizione contenga sufficienti informazioni di pagamento.</span><span class="sxs-lookup"><span data-stu-id="33341-161">For example, if the request is to deploy a virtual machine, Azure resource manager verifies that the subscription has sufficient payment information.</span></span>

![](../_images/governance-1-21.png)   
<span data-ttu-id="33341-162">*Figura 13. A una sottoscrizione è associato un impegno finanziario.*</span><span class="sxs-lookup"><span data-stu-id="33341-162">*Figure 13. A financial commitment is associated with a subscription.*</span></span>

## <a name="summary"></a><span data-ttu-id="33341-163">Summary</span><span class="sxs-lookup"><span data-stu-id="33341-163">Summary</span></span>

<span data-ttu-id="33341-164">In questo articolo si è appreso come viene gestito l'accesso alle risorse in Azure con Azure Resource Manager.</span><span class="sxs-lookup"><span data-stu-id="33341-164">In this article, you learned about how resource access is managed in Azure using Azure resource manager.</span></span>

## <a name="next-steps"></a><span data-ttu-id="33341-165">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="33341-165">Next steps</span></span>

<span data-ttu-id="33341-166">Dopo aver compreso come viene gestito l'accesso alle risorse in Azure, si esaminerà ora come progettare un modello di governance [per un solo team](../governance/governance-single-team.md) o per [più team](../governance/governance-multiple-teams.md) usando questi servizi.</span><span class="sxs-lookup"><span data-stu-id="33341-166">Now that you understand how resource access is managed in Azure, move on to learn how to design a governance model [for a single team](../governance/governance-single-team.md) or [multiple teams](../governance/governance-multiple-teams.md) using these services.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="33341-167">Panoramica della governance</span><span class="sxs-lookup"><span data-stu-id="33341-167">An overview of governance</span></span>](../governance/overview.md)
