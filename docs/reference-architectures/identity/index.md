---
title: Scegliere una soluzione per l'integrazione di Active Directory locale con Azure.
description: Confronta le architetture di riferimento per l'integrazione di Active Directory locale con Azure.
ms.date: 07/02/2018
ms.openlocfilehash: 7e89998c59bccf4d37cebca5ddd4ea7ecba58cd5
ms.sourcegitcommit: 776b8c1efc662d42273a33de3b82ec69e3cd80c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38987530"
---
# <a name="choose-a-solution-for-integrating-on-premises-active-directory-with-azure"></a><span data-ttu-id="1e4c3-103">Scegliere una soluzione per l'integrazione di Active Directory locale con Azure</span><span class="sxs-lookup"><span data-stu-id="1e4c3-103">Choose a solution for integrating on-premises Active Directory with Azure</span></span>

<span data-ttu-id="1e4c3-104">Questo articolo confronta le opzioni per l'integrazione dell'ambiente di Active Directory locale con una rete di Azure.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-104">This article compares options for integrating your on-premises Active Directory (AD) environment with an Azure network.</span></span> <span data-ttu-id="1e4c3-105">Per ogni opzione è disponibile un'architettura di riferimento più dettagliata.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-105">For each option, a more detailed reference architecture is available.</span></span>

<span data-ttu-id="1e4c3-106">Molte organizzazioni usano Active Directory Domain Services per autenticare le identità associate a utenti, computer, applicazioni o ad altre risorse incluse in un limite di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-106">Many organizations use Active Directory Domain Services (AD DS) to authenticate identities associated with users, computers, applications, or other resources that are included in a security boundary.</span></span> <span data-ttu-id="1e4c3-107">I servizi di gestione delle identità e delle directory vengono in genere ospitati in locale, ma se l'applicazione è ospitata in parte in locale e in parte in Azure, potrebbe esserci latenza nell'invio delle richieste di autenticazione da Azure a locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-107">Directory and identity services are typically hosted on-premises, but if your application is hosted partly on-premises and partly in Azure, there may be latency sending authentication requests from Azure back to on-premises.</span></span> <span data-ttu-id="1e4c3-108">L'implementazione dei servizi di gestione delle identità e delle directory in Azure può ridurre la latenza.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-108">Implementing directory and identity services in Azure can reduce this latency.</span></span>

<span data-ttu-id="1e4c3-109">Azure offre due soluzioni per l'implementazione dei servizi di gestione delle identità e delle directory in Azure:</span><span class="sxs-lookup"><span data-stu-id="1e4c3-109">Azure provides two solutions for implementing directory and identity services in Azure:</span></span> 

* <span data-ttu-id="1e4c3-110">Usare [Azure AD][azure-active-directory] per creare un dominio di Active Directory nel cloud e connetterlo al dominio di Active Directory locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-110">Use [Azure AD][azure-active-directory] to create an Active Directory domain in the cloud and connect it to your on-premises Active Directory domain.</span></span> <span data-ttu-id="1e4c3-111">[Azure AD Connect][azure-ad-connect] integra le directory locali con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-111">[Azure AD Connect][azure-ad-connect] integrates your on-premises directories with Azure AD.</span></span>

* <span data-ttu-id="1e4c3-112">Estendere l'infrastruttura locale esistente di Active Directory ad Azure, distribuendo una macchina virtuale in Azure che esegue Active Directory Domain Services come controller di dominio.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-112">Extend your existing on-premises Active Directory infrastructure to Azure, by deploying a VM in Azure that runs AD DS as a domain controller.</span></span> <span data-ttu-id="1e4c3-113">Questa architettura è più comune quando la rete locale e la rete virtuale di Azure sono connesse tramite una connessione VPN o ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-113">This architecture is more common when the on-premises network and the Azure virtual network (VNet) are connected by a VPN or ExpressRoute connection.</span></span> <span data-ttu-id="1e4c3-114">Sono possibili diverse varianti di questa architettura:</span><span class="sxs-lookup"><span data-stu-id="1e4c3-114">Several variations of this architecture are possible:</span></span> 

    - <span data-ttu-id="1e4c3-115">Creare un dominio in Azure e aggiungerlo alla foresta di Active Directory locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-115">Create a domain in Azure and join it to your on-premises AD forest.</span></span>
    - <span data-ttu-id="1e4c3-116">Creare una foresta separata in Azure considerata attendibile dai domini nella foresta locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-116">Create a separate forest in Azure that is trusted by domains in your on-premises forest.</span></span>
    - <span data-ttu-id="1e4c3-117">Replicare una distribuzione di Active Directory Federation Services in Azure.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-117">Replicate an Active Directory Federation Services (AD FS) deployment to Azure.</span></span> 

<span data-ttu-id="1e4c3-118">Le sezioni successive descrivono tutte queste opzioni in modo più dettagliato.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-118">The next sections describe each of these options in more detail.</span></span>

## <a name="integrate-your-on-premises-domains-with-azure-ad"></a><span data-ttu-id="1e4c3-119">Integrare i domini locali con Azure AD</span><span class="sxs-lookup"><span data-stu-id="1e4c3-119">Integrate your on-premises domains with Azure AD</span></span>

<span data-ttu-id="1e4c3-120">Usare Azure Active Directory per creare un dominio in Azure e collegarlo a un dominio locale di Active Directory.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-120">Use Azure Active Directory (Azure AD) to create a domain in Azure and link it to an on-premises AD domain.</span></span> 

<span data-ttu-id="1e4c3-121">La directory di Azure AD non è un'estensione di una directory locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-121">The Azure AD directory is not an extension of an on-premises directory.</span></span> <span data-ttu-id="1e4c3-122">Si tratta piuttosto di una copia che contiene gli stessi oggetti e le stesse identità.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-122">Rather, it's a copy that contains the same objects and identities.</span></span> <span data-ttu-id="1e4c3-123">Le modifiche apportate a questi elementi locali vengono copiate in Azure AD, tuttavia le modifiche apportate in Azure AD non vengono replicate nel dominio locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-123">Changes made to these items on-premises are copied to Azure AD, but changes made in Azure AD are not replicated back to the on-premises domain.</span></span>

<span data-ttu-id="1e4c3-124">È anche possibile usare Azure AD senza usare una directory locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-124">You can also use Azure AD without using an on-premises directory.</span></span> <span data-ttu-id="1e4c3-125">In questo caso, Azure AD funge da fonte principale di tutte le informazioni di identità, invece di contenere i dati replicati da una directory locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-125">In this case, Azure AD acts as the primary source of all identity information, rather than containing data replicated from an on-premises directory.</span></span>

<span data-ttu-id="1e4c3-126">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-126">**Benefits**</span></span>

* <span data-ttu-id="1e4c3-127">Non è necessario gestire un'infrastruttura di Active Directory nel cloud.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-127">You don't need to maintain an AD infrastructure in the cloud.</span></span> <span data-ttu-id="1e4c3-128">Azure AD è completamente gestito da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-128">Azure AD is entirely managed and maintained by Microsoft.</span></span>
* <span data-ttu-id="1e4c3-129">Azure AD offre le stesse informazioni di identità disponibili in locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-129">Azure AD provides the same identity information that is available on-premises.</span></span>
* <span data-ttu-id="1e4c3-130">L'autenticazione può essere eseguita in Azure, riducendo la necessità di applicazioni esterne e utenti per contattare il dominio locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-130">Authentication can happen in Azure, reducing the need for external applications and users to contact the on-premises domain.</span></span>

<span data-ttu-id="1e4c3-131">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-131">**Challenges**</span></span>

* <span data-ttu-id="1e4c3-132">I servizi di gestione delle identità sono limitati a utenti e gruppi.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-132">Identity services are limited to users and groups.</span></span> <span data-ttu-id="1e4c3-133">Non è possibile eseguire l'autenticazione del servizio e degli account del computer.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-133">There is no ability to authenticate service and computer accounts.</span></span>
* <span data-ttu-id="1e4c3-134">È necessario configurare la connettività con il dominio locale per mantenere la sincronizzazione della directory di Azure AD.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-134">You must configure connectivity with your on-premises domain to keep the Azure AD directory synchronized.</span></span> 
* <span data-ttu-id="1e4c3-135">Per abilitare l'autenticazione tramite Azure AD è possibile che sia necessario riscrivere le applicazioni.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-135">Applications may need to be rewritten to enable authentication through Azure AD.</span></span>

<span data-ttu-id="1e4c3-136">**Architettura di riferimento**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-136">**Reference architecture**</span></span>

- <span data-ttu-id="1e4c3-137">[Integrare i domini Active Directory locali con Azure Active Directory][aad]</span><span class="sxs-lookup"><span data-stu-id="1e4c3-137">[Integrate on-premises Active Directory domains with Azure Active Directory][aad]</span></span>

## <a name="ad-ds-in-azure-joined-to-an-on-premises-forest"></a><span data-ttu-id="1e4c3-138">Active Directory Domain Services in Azure unito a una foresta locale</span><span class="sxs-lookup"><span data-stu-id="1e4c3-138">AD DS in Azure joined to an on-premises forest</span></span>

<span data-ttu-id="1e4c3-139">Distribuire i server di Active Directory Domain Services in Azure.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-139">Deploy AD Domain Services (AD DS) servers to Azure.</span></span> <span data-ttu-id="1e4c3-140">Creare un dominio in Azure e aggiungerlo alla foresta di Active Directory locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-140">Create a domain in Azure and join it to your on-premises AD forest.</span></span> 

<span data-ttu-id="1e4c3-141">Prendere in considerazione questa opzione se si desidera usare le funzionalità di Active Directory Domain Services non implementate al momento da Azure AD.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-141">Consider this option if you need to use AD DS features that are not currently implemented by Azure AD.</span></span> 

<span data-ttu-id="1e4c3-142">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-142">**Benefits**</span></span>

* <span data-ttu-id="1e4c3-143">Offre accesso alle stesse informazioni di identità disponibili in locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-143">Provides access to the same identity information that is available on-premises.</span></span>
* <span data-ttu-id="1e4c3-144">È possibile eseguire l'autenticazione di utenti, servizi e account del computer locale e in Azure.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-144">You can authenticate user, service, and computer accounts on-premises and in Azure.</span></span>
* <span data-ttu-id="1e4c3-145">Non è necessario gestire una foresta di Active Directory separata.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-145">You don't need to manage a separate AD forest.</span></span> <span data-ttu-id="1e4c3-146">Il dominio in Azure può appartenere alla foresta locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-146">The domain in Azure can belong to the on-premises forest.</span></span>
* <span data-ttu-id="1e4c3-147">È possibile applicare i criteri del gruppo definiti dagli oggetti Criteri di gruppo locali nel dominio in Azure.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-147">You can apply group policy defined by on-premises Group Policy Objects to the domain in Azure.</span></span>

<span data-ttu-id="1e4c3-148">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-148">**Challenges**</span></span>

* <span data-ttu-id="1e4c3-149">È necessario distribuire e gestire i propri server e il dominio di Active Directory Domain Services nel cloud.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-149">You must deploy and manage your own AD DS servers and domain in the cloud.</span></span>
* <span data-ttu-id="1e4c3-150">Potrebbe verificarsi una certa latenza di sincronizzazione tra i server del dominio nel cloud e i server in esecuzione in locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-150">There may be some synchronization latency between the domain servers in the cloud and the servers running on-premises.</span></span>

<span data-ttu-id="1e4c3-151">**Architettura di riferimento**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-151">**Reference architecture**</span></span>

- <span data-ttu-id="1e4c3-152">[Estendere Active Directory Domain Services in Azure][ad-ds]</span><span class="sxs-lookup"><span data-stu-id="1e4c3-152">[Extend Active Directory Domain Services (AD DS) to Azure][ad-ds]</span></span>

## <a name="ad-ds-in-azure-with-a-separate-forest"></a><span data-ttu-id="1e4c3-153">Active Directory Domain Services in Azure con una foresta separata</span><span class="sxs-lookup"><span data-stu-id="1e4c3-153">AD DS in Azure with a separate forest</span></span>

<span data-ttu-id="1e4c3-154">Distribuire i server di Active Directory Domain Services in Azure, ma creare un'altra [foresta][ad-forest-defn] di Active Directory separata dalla foresta locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-154">Deploy AD Domain Services (AD DS) servers to Azure, but create a separate Active Directory [forest][ad-forest-defn] that is separate from the on-premises forest.</span></span> <span data-ttu-id="1e4c3-155">Questa foresta è considerata attendibile dai domini nella foresta locale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-155">This forest is trusted by domains in your on-premises forest.</span></span>

<span data-ttu-id="1e4c3-156">Gli usi tipici di questa architettura includono mantenere la separazione della sicurezza per le identità e gli oggetti contenuti nel cloud ed eseguire la migrazione di singoli domini da un ambiente locale al cloud.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-156">Typical uses for this architecture include maintaining security separation for objects and identities held in the cloud, and migrating individual domains from on-premises to the cloud.</span></span>

<span data-ttu-id="1e4c3-157">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-157">**Benefits**</span></span>

* <span data-ttu-id="1e4c3-158">È possibile implementare le identità locali e separare solo le identità di Azure.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-158">You can implement on-premises identities and separate Azure-only identities.</span></span>
* <span data-ttu-id="1e4c3-159">Non è necessario eseguire la replica dalla foresta locale di Active Directory in Azure.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-159">You don't need to replicate from the on-premises AD forest to Azure.</span></span>

<span data-ttu-id="1e4c3-160">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-160">**Challenges**</span></span>

* <span data-ttu-id="1e4c3-161">L'autenticazione in Azure per le identità locali richiede hop di rete aggiuntivi per i server di Active Directory locali.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-161">Authentication within Azure for on-premises identities requires extra network hops to the on-premises AD servers.</span></span>
* <span data-ttu-id="1e4c3-162">È necessario distribuire i propri server e la foresta di Active Directory Domain Services nel cloud e stabilire le relazioni di trust appropriate tra le foreste.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-162">You must deploy your own AD DS servers and forest in the cloud, and establish the appropriate trust relationships between forests.</span></span>

<span data-ttu-id="1e4c3-163">**Architettura di riferimento**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-163">**Reference architecture**</span></span>

- <span data-ttu-id="1e4c3-164">[Creare una foresta di risorse di Active Directory Domain Services in Azure][ad-ds-forest]</span><span class="sxs-lookup"><span data-stu-id="1e4c3-164">[Create an Active Directory Domain Services (AD DS) resource forest in Azure][ad-ds-forest]</span></span>

## <a name="extend-ad-fs-to-azure"></a><span data-ttu-id="1e4c3-165">Estendere AD FS in Azure</span><span class="sxs-lookup"><span data-stu-id="1e4c3-165">Extend AD FS to Azure</span></span>

<span data-ttu-id="1e4c3-166">Replicare una distribuzione di Active Directory Federation Services in Azure, per eseguire l'autenticazione federata e l'autorizzazione per i componenti in esecuzione in Azure.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-166">Replicate an Active Directory Federation Services (AD FS) deployment to Azure, to perform federated authentication and authorization for components running in Azure.</span></span> 

<span data-ttu-id="1e4c3-167">Usi tipici di questa architettura:</span><span class="sxs-lookup"><span data-stu-id="1e4c3-167">Typical uses for this architecture:</span></span>

* <span data-ttu-id="1e4c3-168">Le organizzazioni partner possono autenticare e autorizzare gli utenti.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-168">Authenticate and authorize users from partner organizations.</span></span>
* <span data-ttu-id="1e4c3-169">Gli utenti possono eseguire l'autenticazione da Web browser in esecuzione all'esterno del firewall aziendale.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-169">Allow users to authenticate from web browsers running outside of the organizational firewall.</span></span>
* <span data-ttu-id="1e4c3-170">Gli utenti possono connettersi da dispositivi esterni autorizzati, ad esempio dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-170">Allow users to connect from authorized external devices such as mobile devices.</span></span> 

<span data-ttu-id="1e4c3-171">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-171">**Benefits**</span></span>

* <span data-ttu-id="1e4c3-172">È possibile sfruttare le applicazioni in grado di riconoscere attestazioni.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-172">You can leverage claims-aware applications.</span></span>
* <span data-ttu-id="1e4c3-173">Offre la possibilità di considerare attendibili i partner esterni per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-173">Provides the ability to trust external partners for authentication.</span></span>
* <span data-ttu-id="1e4c3-174">Compatibilità con grandi set di protocolli di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-174">Compatibility with large set of authentication protocols.</span></span>

<span data-ttu-id="1e4c3-175">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-175">**Challenges**</span></span>

* <span data-ttu-id="1e4c3-176">È necessario distribuire i server di Active Directory Domain Services, Active Directory Federation Services e del proxy applicazione Web di Active Directory Federation Services in Azure.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-176">You must deploy your own AD DS, AD FS, and AD FS Web Application Proxy servers in Azure.</span></span>
* <span data-ttu-id="1e4c3-177">Questa architettura può essere complessa da configurare.</span><span class="sxs-lookup"><span data-stu-id="1e4c3-177">This architecture can be complex to configure.</span></span>

<span data-ttu-id="1e4c3-178">**Architettura di riferimento**</span><span class="sxs-lookup"><span data-stu-id="1e4c3-178">**Reference architecture**</span></span>

- <span data-ttu-id="1e4c3-179">[Estendere Active Directory Federation Services in Azure][adfs]</span><span class="sxs-lookup"><span data-stu-id="1e4c3-179">[Extend Active Directory Federation Services (AD FS) to Azure][adfs]</span></span>

<!-- links -->

[aad]: ./azure-ad.md
[ad-ds]: ./adds-extend-domain.md
[ad-ds-forest]: ./adds-forest.md
[ad-forest-defn]: /windows/desktop/AD/forests
[adfs]: ./adfs.md

[azure-active-directory]: /azure/active-directory-domain-services/active-directory-ds-overview
[azure-ad-connect]: /azure/active-directory/active-directory-aadconnect
