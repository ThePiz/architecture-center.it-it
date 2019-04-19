---
title: 'CAF: Guida alle decisioni relative alla gestione delle identità'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sulla gestione delle identità come servizio di base nelle migrazioni di Azure.
author: rotycenh
ms.openlocfilehash: addc11928a0bc72917a28b468a04880720a1fdf4
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901641"
---
# <a name="identity-decision-guide"></a><span data-ttu-id="a7508-103">Guida alle decisioni relative alla gestione delle identità</span><span class="sxs-lookup"><span data-stu-id="a7508-103">Identity decision guide</span></span>

<span data-ttu-id="a7508-104">In qualsiasi ambiente, che sia locale, ibrido o cloud, l'IT deve avere il controllo su quali amministratori, utenti e gruppi hanno accesso alle risorse.</span><span class="sxs-lookup"><span data-stu-id="a7508-104">In any environment, whether on-premises, hybrid, or cloud-only, IT needs to control which administrators, users, and groups have access to resources.</span></span> <span data-ttu-id="a7508-105">I servizi di gestione delle identità e degli accessi (IAM) consentono di gestire il controllo di accesso nel cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-105">Identity and access management (IAM) services enable you to manage access control in the cloud.</span></span>

![Grafico delle opzioni relative alla gestione delle identità, dalla meno complessa alla più complessa, allineato con i collegamenti sotto](../../_images/discovery-guides/discovery-guide-identity.png)

<span data-ttu-id="a7508-107">Passare a: [Determinare i requisiti di integrazione della soluzione di gestione delle identità](#determine-identity-integration-requirements) | [Infrastruttura nativa del cloud](#cloud-baseline) | [Sincronizzazione della directory](#directory-synchronization) | [Servizi di dominio ospitati nel cloud](#cloud-hosted-domain-services) | [Active Directory Federation Services](#active-directory-federation-services) | [Evoluzione dell'integrazione delle identità](#evolving-identity-integration) | [Altre informazioni](#learn-more)</span><span class="sxs-lookup"><span data-stu-id="a7508-107">Jump to: [Determine Identity Integration Requirements](#determine-identity-integration-requirements) | [Cloud native](#cloud-baseline) | [Directory Synchronization](#directory-synchronization) | [Cloud hosted domain services](#cloud-hosted-domain-services) | [Active Directory Federation Services](#active-directory-federation-services) | [Evolving identity integration](#evolving-identity-integration) | [Learn more](#learn-more)</span></span>

<span data-ttu-id="a7508-108">È possibile gestire l'identità in un ambiente cloud in diversi modi, che si differenziano per costo e complessità.</span><span class="sxs-lookup"><span data-stu-id="a7508-108">There are several ways to manage identity in a cloud environment, which vary in cost and complexity.</span></span> <span data-ttu-id="a7508-109">Un fattore chiave nella strutturazione dei servizi di gestione delle identità basati sul cloud è il livello di integrazione necessario con l'infrastruttura di gestione delle identità locale esistente.</span><span class="sxs-lookup"><span data-stu-id="a7508-109">A key factor in structuring your cloud-based identity services is the level of integration required with your existing on-premises identity infrastructure.</span></span>

<span data-ttu-id="a7508-110">Le soluzioni di gestione delle identità SaaS basate sul cloud offrono un livello base di controllo di accesso e gestione delle identità per le risorse cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-110">Cloud-based software as a service (SaaS) identity solutions provide a base level of access control and identity management for cloud resources.</span></span> <span data-ttu-id="a7508-111">Se però l'infrastruttura di Active Directory (AD) dell'organizzazione ha una struttura di foresta complessa o unità organizzative personalizzate, i carichi di lavoro basati sul cloud potrebbero richiedere la replica di directory nel cloud per ottenere un set coerente di identità, gruppi e ruoli tra l'ambiente locale e l'ambiente cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-111">However, if your organization's Active Directory (AD) infrastructure has a complex forest structure or customized organizational units (OUs), your cloud-based workloads may require directory replication to the cloud for a consistent set of identities, groups, and roles between your on-premises and cloud environments.</span></span> <span data-ttu-id="a7508-112">Se la replica di directory è necessaria per una soluzione globale, la complessità può aumentare notevolmente.</span><span class="sxs-lookup"><span data-stu-id="a7508-112">If directory replication is required for a global solution, complexity can increase significantly.</span></span> <span data-ttu-id="a7508-113">Inoltre, per supportare le applicazioni che dipendono da meccanismi di autenticazione legacy potrebbe essere necessario distribuire servizi di dominio nel cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-113">Additionally, support for applications dependent on legacy authentication mechanisms may require the deployment of domain services in the cloud.</span></span>

## <a name="determine-identity-integration-requirements"></a><span data-ttu-id="a7508-114">Determinare i requisiti di integrazione della soluzione di gestione delle identità</span><span class="sxs-lookup"><span data-stu-id="a7508-114">Determine identity integration requirements</span></span>

| <span data-ttu-id="a7508-115">Domanda</span><span class="sxs-lookup"><span data-stu-id="a7508-115">Question</span></span> | <span data-ttu-id="a7508-116">Infrastruttura nativa del cloud</span><span class="sxs-lookup"><span data-stu-id="a7508-116">Cloud baseline</span></span> | <span data-ttu-id="a7508-117">Sincronizzazione della directory</span><span class="sxs-lookup"><span data-stu-id="a7508-117">Directory synchronization</span></span> | <span data-ttu-id="a7508-118">Servizi di dominio ospitati nel cloud</span><span class="sxs-lookup"><span data-stu-id="a7508-118">Cloud-hosted Domain Services</span></span> | <span data-ttu-id="a7508-119">Active Directory Federation Services</span><span class="sxs-lookup"><span data-stu-id="a7508-119">AD Federation Services</span></span> |
|------|------|------|------|------|
| <span data-ttu-id="a7508-120">Attualmente manca un servizio directory locale?</span><span class="sxs-lookup"><span data-stu-id="a7508-120">Do you currently lack an on-premises directory service?</span></span> | <span data-ttu-id="a7508-121">Sì</span><span class="sxs-lookup"><span data-stu-id="a7508-121">Yes</span></span> | <span data-ttu-id="a7508-122">No </span><span class="sxs-lookup"><span data-stu-id="a7508-122">No</span></span> | <span data-ttu-id="a7508-123">No </span><span class="sxs-lookup"><span data-stu-id="a7508-123">No</span></span> | <span data-ttu-id="a7508-124">No </span><span class="sxs-lookup"><span data-stu-id="a7508-124">No</span></span> |
| <span data-ttu-id="a7508-125">I carichi di lavoro devono essere autenticati con servizi di gestione delle identità locali?</span><span class="sxs-lookup"><span data-stu-id="a7508-125">Do your workloads need to authenticate against on-premises identity services?</span></span> | <span data-ttu-id="a7508-126">No </span><span class="sxs-lookup"><span data-stu-id="a7508-126">No</span></span> | <span data-ttu-id="a7508-127">Sì</span><span class="sxs-lookup"><span data-stu-id="a7508-127">Yes</span></span> | <span data-ttu-id="a7508-128">No </span><span class="sxs-lookup"><span data-stu-id="a7508-128">No</span></span> | <span data-ttu-id="a7508-129">No </span><span class="sxs-lookup"><span data-stu-id="a7508-129">No</span></span> |
| <span data-ttu-id="a7508-130">I carichi di lavoro dipendono da meccanismi di autenticazione legacy, come Kerberos o NTLM?</span><span class="sxs-lookup"><span data-stu-id="a7508-130">Do your workloads depend on legacy authentication mechanisms, such as Kerberos or NTLM?</span></span> | <span data-ttu-id="a7508-131">No </span><span class="sxs-lookup"><span data-stu-id="a7508-131">No</span></span> | <span data-ttu-id="a7508-132">No </span><span class="sxs-lookup"><span data-stu-id="a7508-132">No</span></span> | <span data-ttu-id="a7508-133">Sì</span><span class="sxs-lookup"><span data-stu-id="a7508-133">Yes</span></span> | <span data-ttu-id="a7508-134">No </span><span class="sxs-lookup"><span data-stu-id="a7508-134">No</span></span> |
| <span data-ttu-id="a7508-135">L'integrazione tra i servizi di gestione delle identità cloud e locali è impossibile?</span><span class="sxs-lookup"><span data-stu-id="a7508-135">Is integration between cloud and on-premises identity services impossible?</span></span> | <span data-ttu-id="a7508-136">No </span><span class="sxs-lookup"><span data-stu-id="a7508-136">No</span></span> | <span data-ttu-id="a7508-137">No </span><span class="sxs-lookup"><span data-stu-id="a7508-137">No</span></span> | <span data-ttu-id="a7508-138">Sì</span><span class="sxs-lookup"><span data-stu-id="a7508-138">Yes</span></span> | <span data-ttu-id="a7508-139">No </span><span class="sxs-lookup"><span data-stu-id="a7508-139">No</span></span> |
| <span data-ttu-id="a7508-140">È necessario il Single Sign-On tra più provider di identità?</span><span class="sxs-lookup"><span data-stu-id="a7508-140">Do you require single sign-on across multiple identity providers?</span></span> | <span data-ttu-id="a7508-141">No </span><span class="sxs-lookup"><span data-stu-id="a7508-141">No</span></span> | <span data-ttu-id="a7508-142">No </span><span class="sxs-lookup"><span data-stu-id="a7508-142">No</span></span> | <span data-ttu-id="a7508-143">No </span><span class="sxs-lookup"><span data-stu-id="a7508-143">No</span></span> | <span data-ttu-id="a7508-144">Sì</span><span class="sxs-lookup"><span data-stu-id="a7508-144">Yes</span></span> |

<span data-ttu-id="a7508-145">Nell'ambito della pianificazione della migrazione ad Azure, è necessario determinare il modo migliore per integrare i servizi di gestione delle identità esistenti con i servizi cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-145">As part of planning your migration to Azure, you will need to determine how best to integrate your existing identity management and cloud identity services.</span></span> <span data-ttu-id="a7508-146">Di seguito sono descritti alcuni scenari di integrazione comuni.</span><span class="sxs-lookup"><span data-stu-id="a7508-146">The following are common integration scenarios.</span></span>

### <a name="cloud-baseline"></a><span data-ttu-id="a7508-147">Infrastruttura nativa del cloud</span><span class="sxs-lookup"><span data-stu-id="a7508-147">Cloud baseline</span></span>

<span data-ttu-id="a7508-148">Le piattaforme cloud pubbliche forniscono un sistema IAM nativo che concede a utenti e gruppi l'accesso alle funzionalità di gestione.</span><span class="sxs-lookup"><span data-stu-id="a7508-148">Public cloud platforms provide a native IAM system for granting users and groups access to management features.</span></span> <span data-ttu-id="a7508-149">Se l'organizzazione non dispone di una soluzione di gestione delle identità locale rilevante e si prevede di eseguire la migrazione dei carichi di lavoro in modo che siano compatibili con i meccanismi di autenticazione basati sul cloud, occorre creare l'infrastruttura di gestione delle identità usando un servizio di gestione delle identità nativo del cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-149">If your organization lacks a significant on-premises identity solution, and you plan on migrating workloads to be compatible with cloud-based authentication mechanisms, you should build your identity infrastructure using a cloud-native identity service.</span></span>

<span data-ttu-id="a7508-150">**Presupposti per un'infrastruttura nativa del cloud**.</span><span class="sxs-lookup"><span data-stu-id="a7508-150">**Cloud baseline assumptions**.</span></span> <span data-ttu-id="a7508-151">L'uso di un'infrastruttura di gestione delle identità completamente nativa del cloud presuppone le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a7508-151">Using a purely cloud-native identity infrastructure assumes the following:</span></span>

- <span data-ttu-id="a7508-152">Le risorse basate sul cloud non hanno dipendenze dai servizi directory locali o da server Active Directory oppure è possibile modificare i carichi di lavoro per rimuovere queste dipendenze.</span><span class="sxs-lookup"><span data-stu-id="a7508-152">Your cloud-based resources will not have dependencies on on-premises directory services or Active Directory servers, or workloads can be modified to remove those dependencies your.</span></span>
- <span data-ttu-id="a7508-153">I carichi di lavoro di applicazioni o servizi di cui eseguire la migrazione supportano meccanismi di autenticazione compatibili con i provider di identità cloud o possono essere facilmente modificati per supportarli.</span><span class="sxs-lookup"><span data-stu-id="a7508-153">The application or service workloads being migrated either support authentication mechanisms compatible with cloud identity providers or can be modified easily to support them.</span></span> <span data-ttu-id="a7508-154">I provider di identità nativi del cloud usano meccanismi di autenticazione basati su Internet come SAML, OAuth e OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="a7508-154">Cloud native identity providers rely on internet-ready authentication mechanisms such as SAML, OAuth, and OpenID Connect.</span></span> <span data-ttu-id="a7508-155">I carichi di lavoro esistenti che dipendono da metodi di autenticazione legacy che usano protocolli come Kerberos o NTLM potrebbero dover essere sottoposti a refactoring prima della migrazione al cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-155">Existing workloads that depend on legacy authentication methods using protocols such as Kerberos or NTLM may need to be refactored before migrating to the cloud.</span></span>

> [!TIP]
> <span data-ttu-id="a7508-156">La maggior parte dei servizi di gestione delle identità nativi del cloud non sostituisce completamente le directory locali tradizionali.</span><span class="sxs-lookup"><span data-stu-id="a7508-156">Most cloud-native identity services are not full replacements for traditional on-premises directories.</span></span> <span data-ttu-id="a7508-157">Le funzionalità di directory come la gestione dei computer o i criteri di gruppo potrebbero non essere disponibili se non si usano strumenti o servizi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="a7508-157">Directory features such as computer management or group policy may not be available without using additional tools or services.</span></span>

<span data-ttu-id="a7508-158">La migrazione completa dei servizi di gestione delle identità a un provider basato sul cloud elimina la necessità di mantenere l'infrastruttura di gestione delle identità locale, semplificando notevolmente la gestione IT.</span><span class="sxs-lookup"><span data-stu-id="a7508-158">Completely migrating your identity services to a cloud-based provider eliminates the need to maintain your own identity infrastructure, significantly simplifying your IT management.</span></span>

### <a name="directory-synchronization"></a><span data-ttu-id="a7508-159">Sincronizzazione della directory</span><span class="sxs-lookup"><span data-stu-id="a7508-159">Directory synchronization</span></span>

<span data-ttu-id="a7508-160">Per le organizzazioni che hanno già un'infrastruttura di gestione delle identità la sincronizzazione della directory è spesso la soluzione migliore per mantenere la gestione esistente di utenti e accessi e fornire al contempo le funzionalità IAM necessarie per gestire le risorse cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-160">For organizations with an existing identity infrastructure, directory synchronization is often the best solution for preserving existing user and access management while providing the required IAM capabilities for managing cloud resources.</span></span> <span data-ttu-id="a7508-161">Questo processo replica continuamente le informazioni della directory tra gli ambienti cloud e locale, consentendo il Single Sign-On (SSO) per gli utenti e un sistema di gestione di identità, ruoli e autorizzazioni coerente nell'intera organizzazione.</span><span class="sxs-lookup"><span data-stu-id="a7508-161">This process continuously replicates directory information between the cloud and on-premises environments, allowing single sign-on (SSO) for users and a consistent identity, role, and permission system across your entire organization.</span></span>

<span data-ttu-id="a7508-162">Nota: le organizzazioni che hanno adottato Office 365 potrebbero avere già implementato la [sincronizzazione della directory](/office365/enterprise/set-up-directory-synchronization) fra la propria infrastruttura Active Directory locale e Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a7508-162">Note: Organizations that have adopted Office 365 may have already implemented [directory synchronization](/office365/enterprise/set-up-directory-synchronization) between their on-premises Active Directory infrastructure and Azure Active Directory.</span></span>

<span data-ttu-id="a7508-163">**Presupposti relativi alla sincronizzazione della directory**.</span><span class="sxs-lookup"><span data-stu-id="a7508-163">**Directory synchronization assumptions**.</span></span> <span data-ttu-id="a7508-164">L'uso di una soluzione di gestione delle identità sincronizzata presuppone le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a7508-164">Using a synchronized identity solution assumes the following:</span></span>

- <span data-ttu-id="a7508-165">È necessario mantenere un set comune di account utente e di gruppi nell'infrastruttura cloud e in quella locale.</span><span class="sxs-lookup"><span data-stu-id="a7508-165">You need to maintain a common set of user accounts and groups across your cloud and on-premises IT infrastructure.</span></span>
- <span data-ttu-id="a7508-166">I servizi di gestione delle identità locali supportano la replica con il provider di identità cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-166">Your on-premises identity services support replication with your cloud identity provider.</span></span>
- <span data-ttu-id="a7508-167">Sono necessari meccanismi di Single Sign-On per gli utenti che accedono ai provider di identità cloud e locali.</span><span class="sxs-lookup"><span data-stu-id="a7508-167">You require SSO mechanisms for users accessing cloud and on-premises identity providers.</span></span>

> [!TIP]
> <span data-ttu-id="a7508-168">Qualsiasi carico di lavoro basato sul cloud che dipende da meccanismi di autenticazione legacy non supportati da servizi di gestione delle identità basati sul cloud come Azure AD avrà ancora bisogno di connettività ai servizi di dominio locali o ai server virtuali nell'ambiente cloud che forniscono questi servizi.</span><span class="sxs-lookup"><span data-stu-id="a7508-168">Any cloud-based workloads that depend on legacy authentication mechanisms that are not supported by cloud-based identity services like Azure AD will still require either connectivity to on-premises domain services or virtual servers in the cloud environment providing these services.</span></span> <span data-ttu-id="a7508-169">L'uso di servizi di gestione delle identità locali introduce anche dipendenze a livello di connettività tra la rete cloud e la rete locale.</span><span class="sxs-lookup"><span data-stu-id="a7508-169">Using on-premises identity services also introduces dependencies on connectivity between the cloud and on-premises networks.</span></span>

### <a name="cloud-hosted-domain-services"></a><span data-ttu-id="a7508-170">Servizi di dominio ospitati nel cloud</span><span class="sxs-lookup"><span data-stu-id="a7508-170">Cloud-hosted domain services</span></span>

<span data-ttu-id="a7508-171">In presenza di carichi di lavoro che dipendono da un meccanismo di autenticazione basata sulle attestazioni che usa protocolli legacy come Kerberos o NTLM, e che non possono essere sottoposti a refactoring per accettare protocolli di autenticazione moderni come SAML o OAuth e OpenID Connect, potrebbe essere necessario eseguire la migrazione di alcuni servizi di dominio al cloud nell'ambito della distribuzione cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-171">If you have workloads that depend on claims-based authentication using legacy protocols such as Kerberos or NTLM, and those workloads cannot be refactored to accept modern authentication protocols such as SAML or OAuth and OpenID Connect, you may need to migrate some of your domain services to the cloud as part of your cloud deployment.</span></span>

<span data-ttu-id="a7508-172">Questo tipo di distribuzione implica la distribuzione di macchine virtuali che eseguono Active Directory nelle reti virtuali aziendali basate sul cloud al fine di fornire servizi di dominio per le risorse nel cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-172">This type of deployment involves deploying virtual machines running Active Directory in your cloud-based virtual networks to provide domain services for resources in the cloud.</span></span> <span data-ttu-id="a7508-173">Le applicazioni e i servizi esistenti trasferiti nella rete cloud dovrebbero poter usare questi server di directory ospitati nel cloud con modifiche minime.</span><span class="sxs-lookup"><span data-stu-id="a7508-173">Any existing applications and services migrating to your cloud network should be able to use of these cloud-hosted directory servers with minor modifications.</span></span>

<span data-ttu-id="a7508-174">Probabilmente le directory e i servizi di dominio esistenti continueranno a essere usati nell'ambiente locale.</span><span class="sxs-lookup"><span data-stu-id="a7508-174">It's likely that your existing directories and domain services will continue to be used in your on-premises environment.</span></span> <span data-ttu-id="a7508-175">In questo scenario è consigliabile usare anche la sincronizzazione della directory per fornire un set comune di utenti e ruoli sia nell'ambiente cloud che in quello locale.</span><span class="sxs-lookup"><span data-stu-id="a7508-175">In this scenario, it's recommended that you also use directory synchronization to provide a common set of users and roles in both the cloud and on-premises environments.</span></span>

<span data-ttu-id="a7508-176">**Presupposti relativi ai servizi di dominio ospitati nel cloud**.</span><span class="sxs-lookup"><span data-stu-id="a7508-176">**Cloud hosted domain services assumptions**.</span></span> <span data-ttu-id="a7508-177">L'esecuzione di una migrazione di directory presuppone le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a7508-177">Performing a directory migration assumes the following:</span></span>

- <span data-ttu-id="a7508-178">I carichi di lavoro dipendono da un meccanismo di autenticazione basata sulle attestazioni che usa protocolli come Kerberos o NTLM.</span><span class="sxs-lookup"><span data-stu-id="a7508-178">Your workloads depend on claims-based authentication using protocols like Kerberos or NTLM.</span></span>
- <span data-ttu-id="a7508-179">Le macchine virtuali dei carichi di lavoro devono essere aggiunte a un dominio ai fini della gestione o dell'applicazione di criteri di gruppo di Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a7508-179">Your workload virtual machines need to be domain-joined for management or application of Active Directory group policy purposes.</span></span>

> [!TIP]
> <span data-ttu-id="a7508-180">Benché la migrazione della directory associata a servizi di dominio ospitati nel cloud assicuri un'ottima flessibilità per la migrazione dei carichi di lavoro esistenti, l'hosting di macchine virtuali nella rete virtuale cloud per fornire questi servizi aumenta la complessità delle attività di gestione IT.</span><span class="sxs-lookup"><span data-stu-id="a7508-180">While a directory migration coupled with cloud-hosted domain services provides great flexibility when migrating existing workloads, hosting virtual machines within your cloud virtual network to provide these services does increase the complexity of your IT management tasks.</span></span> <span data-ttu-id="a7508-181">Man mano che la propria esperienza di migrazione nel cloud matura, esaminare i requisiti di manutenzione a lungo termine per l'hosting di questi server.</span><span class="sxs-lookup"><span data-stu-id="a7508-181">As your cloud migration experience matures, examine the long-term maintenance requirements of hosting these servers.</span></span> <span data-ttu-id="a7508-182">Stabilire se il refactoring dei carichi di lavoro esistenti per la compatibilità con provider di identità cloud come Azure Active Directory può ridurre la necessità di questi server ospitati nel cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-182">Consider whether refactoring existing workloads for compatibility with cloud identity providers such as Azure Active Directory can reduce the need for these cloud-hosted servers.</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="a7508-183">Active Directory Federation Services</span><span class="sxs-lookup"><span data-stu-id="a7508-183">Active Directory Federation Services</span></span>

<span data-ttu-id="a7508-184">La federazione delle identità stabilisce relazioni di trust fra più sistemi di gestione delle identità per abilitare funzionalità comuni di autenticazione e autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="a7508-184">Identity federation establishes trust relationships across multiple identity management systems to allow common authentication and authorization capabilities.</span></span> <span data-ttu-id="a7508-185">È quindi possibile supportare funzionalità di Single Sign-On in più domini all'interno dell'organizzazione oppure sistemi di gestione delle identità gestiti da clienti o partner commerciali.</span><span class="sxs-lookup"><span data-stu-id="a7508-185">You can then support single sign-on capabilities across multiple domains within your organization or identity systems managed by your customers or business partners.</span></span>

<span data-ttu-id="a7508-186">Azure AD supporta la federazione di domini Active Directory locali tramite [Active Directory Federation Services](/azure/active-directory/hybrid/how-to-connect-fed-whatis) (AD FS).</span><span class="sxs-lookup"><span data-stu-id="a7508-186">Azure AD supports federation of on-premises Active Directory domains using [Active Directory Federation Services](/azure/active-directory/hybrid/how-to-connect-fed-whatis) (AD FS).</span></span> <span data-ttu-id="a7508-187">Vedere l'architettura di riferimento [Estendere Active Directory Federation Services in Azure](../../../reference-architectures/identity/adfs.md) per informazioni su come implementarla in Azure.</span><span class="sxs-lookup"><span data-stu-id="a7508-187">See the reference architecture [Extend AD FS to Azure](../../../reference-architectures/identity/adfs.md) to see how this can be implemented in Azure.</span></span>

## <a name="evolving-identity-integration"></a><span data-ttu-id="a7508-188">Evoluzione dell'integrazione delle identità</span><span class="sxs-lookup"><span data-stu-id="a7508-188">Evolving identity integration</span></span>

<span data-ttu-id="a7508-189">L'integrazione delle identità è un processo iterativo.</span><span class="sxs-lookup"><span data-stu-id="a7508-189">Identity integration is an iterative process.</span></span> <span data-ttu-id="a7508-190">È consigliabile iniziare con una soluzione nativa del cloud con un piccolo gruppo di utenti e di ruoli corrispondenti per una distribuzione iniziale.</span><span class="sxs-lookup"><span data-stu-id="a7508-190">You may want to start with a cloud native solution with a small set of users and corresponding roles for an initial deployment.</span></span> <span data-ttu-id="a7508-191">Man mano che l'esperienza di migrazione matura, si può considerare l'adozione di un modello federato o l'esecuzione di una migrazione della directory completa dei servizi di gestione delle identità locali nel cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-191">As your migration matures, consider adopting a federated model or performing a full directory migration of your on-premises identity services to the cloud.</span></span> <span data-ttu-id="a7508-192">Rivedere la propria strategia di gestione delle identità in ogni iterazione del processo di migrazione.</span><span class="sxs-lookup"><span data-stu-id="a7508-192">Revisit your identity strategy in every iteration of your migration process.</span></span>

## <a name="learn-more"></a><span data-ttu-id="a7508-193">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="a7508-193">Learn more</span></span>

<span data-ttu-id="a7508-194">Vedere i collegamenti seguenti per altre informazioni sui servizi di gestione delle identità nella piattaforma Azure.</span><span class="sxs-lookup"><span data-stu-id="a7508-194">See the following for more information about identity services on the Azure platform.</span></span>

- <span data-ttu-id="a7508-195">[Azure AD](https://azure.microsoft.com/services/active-directory).</span><span class="sxs-lookup"><span data-stu-id="a7508-195">[Azure AD](https://azure.microsoft.com/services/active-directory).</span></span> <span data-ttu-id="a7508-196">Azure AD fornisce servizi di gestione delle identità basati sul cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-196">Azure AD provides cloud-based identity services.</span></span> <span data-ttu-id="a7508-197">Consente di gestire l'accesso alle risorse di Azure e di controllare la gestione delle identità, la registrazione dei dispositivi, il provisioning degli utenti, il controllo di accesso alle applicazioni e la protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="a7508-197">It allows you to manage access to your Azure resources and control identity management, device registration, user provisioning, application access control, and data protection.</span></span>
- <span data-ttu-id="a7508-198">[Azure AD Connect](/azure/active-directory/hybrid/whatis-hybrid-identity).</span><span class="sxs-lookup"><span data-stu-id="a7508-198">[Azure AD Connect](/azure/active-directory/hybrid/whatis-hybrid-identity).</span></span> <span data-ttu-id="a7508-199">Lo strumento Azure AD Connect consente di connettere istanze di Azure AD alle soluzioni di gestione delle identità esistenti, permettendo la sincronizzazione della directory esistente nel cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-199">The Azure AD Connect tool allows you to connect Azure AD instances with your existing identity management solutions, allowing synchronization of your existing directory in the cloud.</span></span>
- <span data-ttu-id="a7508-200">[Controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview).</span><span class="sxs-lookup"><span data-stu-id="a7508-200">[Role-based access control](/azure/role-based-access-control/overview) (RBAC).</span></span> <span data-ttu-id="a7508-201">Azure AD offre funzionalità di controllo degli accessi in base al ruolo che consentono di gestire in modo efficiente e sicuro l'accesso alle risorse nel piano di gestione.</span><span class="sxs-lookup"><span data-stu-id="a7508-201">Azure AD provides RBAC to efficiently and securely manage access to resources in the management plane.</span></span> <span data-ttu-id="a7508-202">I processi e le responsabilità sono organizzati in ruoli e gli utenti sono assegnati a questi ruoli.</span><span class="sxs-lookup"><span data-stu-id="a7508-202">Jobs and responsibilities are organized into roles, and users are assigned to these roles.</span></span> <span data-ttu-id="a7508-203">Il controllo degli accessi in base al ruolo consente di controllare chi ha accesso a una risorsa e quali azioni possono essere eseguite dall'utente su quella risorsa.</span><span class="sxs-lookup"><span data-stu-id="a7508-203">RBAC allows you to control who has access to a resource along with which actions a user can perform on that resource.</span></span>
- <span data-ttu-id="a7508-204">[Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) (PIM).</span><span class="sxs-lookup"><span data-stu-id="a7508-204">[Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) (PIM).</span></span> <span data-ttu-id="a7508-205">PIM riduce il tempo di esposizione dei privilegi di accesso alle risorse e aumenta la visibilità sul loro utilizzo tramite report e avvisi.</span><span class="sxs-lookup"><span data-stu-id="a7508-205">PIM lowers the exposure time of resource access privileges and increases your visibility into their use through reports and alerts.</span></span> <span data-ttu-id="a7508-206">Consente agli utenti di assumere i loro privilegi soltanto "just in time" (JIT) oppure li assegna loro per una durata più breve, dopo la quale i privilegi vengono revocati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="a7508-206">It limits users to taking on their privileges "just in time" (JIT), or by assigning privileges for a shorter duration, after which privileges are revoked automatically.</span></span>
- <span data-ttu-id="a7508-207">[Integrare i domini Active Directory locali con Azure Active Directory](../../../reference-architectures/identity/azure-ad.md).</span><span class="sxs-lookup"><span data-stu-id="a7508-207">[Integrate on-premises Active Directory domains with Azure Active Directory](../../../reference-architectures/identity/azure-ad.md).</span></span> <span data-ttu-id="a7508-208">Questa architettura di riferimento fornisce un esempio di sincronizzazione della directory tra domini Active Directory locali e Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a7508-208">This reference architecture provides an example of directory synchronization between on-premises Active Directory domains and Azure AD.</span></span>
- <span data-ttu-id="a7508-209">[Estendere Active Directory Domain Services in Azure](../../../reference-architectures/identity/adds-extend-domain.md).</span><span class="sxs-lookup"><span data-stu-id="a7508-209">[Extend Active Directory Domain Services (AD DS) to Azure.](../../../reference-architectures/identity/adds-extend-domain.md)</span></span> <span data-ttu-id="a7508-210">Questa architettura di riferimento fornisce un esempio di distribuzione di server AD DS per estendere i servizi di dominio alle risorse basate sul cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-210">This reference architecture provides an example of deploying AD DS servers to extend domain services to cloud-based resources.</span></span>
- <span data-ttu-id="a7508-211">[Estendere Active Directory Federation Services in Azure](../../../reference-architectures/identity/adfs.md).</span><span class="sxs-lookup"><span data-stu-id="a7508-211">[Extend Active Directory Federation Services (AD FS) to Azure](../../../reference-architectures/identity/adfs.md).</span></span> <span data-ttu-id="a7508-212">Questa architettura di riferimento configura Active Directory Federation Services (AD FS) per l'esecuzione dell'autenticazione federata e dell'autorizzazione con la directory Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a7508-212">This reference architecture configures Active Directory Federation Services (AD FS) to perform federated authentication and authorization with your Azure AD directory.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a7508-213">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="a7508-213">Next steps</span></span>

<span data-ttu-id="a7508-214">Informazioni su come implementare l'applicazione dei criteri nel cloud.</span><span class="sxs-lookup"><span data-stu-id="a7508-214">Learn how to implement policy enforcement in the cloud.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="a7508-215">Applicazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="a7508-215">Policy enforcement</span></span>](../policy-enforcement/overview.md)