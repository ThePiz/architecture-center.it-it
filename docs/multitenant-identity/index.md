---
title: Gestione delle identità per le applicazioni multi-tenant
description: Procedure consigliate per l'autenticazione, l'autorizzazione e la gestione delle identità in applicazioni multi-tenant.
author: MikeWasson
ms.date: 07/21/2017
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: f8875612ad6b1a71fdb6f7a768078ae599eb70b5
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54480548"
---
# <a name="manage-identity-in-multitenant-applications"></a><span data-ttu-id="6715d-103">Gestire le identità in applicazioni multi-tenant</span><span class="sxs-lookup"><span data-stu-id="6715d-103">Manage identity in multitenant applications</span></span>

<span data-ttu-id="6715d-104">Questa serie di articoli descrive le procedure consigliate per il multi-tenancy, quando si usa Azure AD per l'autenticazione e la gestione delle identità.</span><span class="sxs-lookup"><span data-stu-id="6715d-104">This series of articles describes best practices for multitenancy, when using Azure AD for authentication and identity management.</span></span>

<span data-ttu-id="6715d-105">[![GitHub](../_images/github.png) Codice di esempio][sample-application]</span><span class="sxs-lookup"><span data-stu-id="6715d-105">[![GitHub](../_images/github.png) Sample code][sample-application]</span></span>

<span data-ttu-id="6715d-106">Quando si crea un'applicazione multi-tenant, una delle attività principali è la gestione delle identità degli utenti, ognuno dei quali appartiene ora a un tenant.</span><span class="sxs-lookup"><span data-stu-id="6715d-106">When you're building a multitenant application, one of the first challenges is managing user identities, because now every user belongs to a tenant.</span></span> <span data-ttu-id="6715d-107">Ad esempio: </span><span class="sxs-lookup"><span data-stu-id="6715d-107">For example:</span></span>

- <span data-ttu-id="6715d-108">Gli utenti accedono con le credenziali dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="6715d-108">Users sign in with their organizational credentials.</span></span>
- <span data-ttu-id="6715d-109">Gli utenti devono avere accesso ai dati dell'organizzazione ma non ai dati appartenenti ad altri tenant.</span><span class="sxs-lookup"><span data-stu-id="6715d-109">Users should have access to their organization's data, but not data that belongs to other tenants.</span></span>
- <span data-ttu-id="6715d-110">Un'organizzazione può effettuare l'iscrizione per l'applicazione e quindi assegnare i ruoli applicazione ai propri membri.</span><span class="sxs-lookup"><span data-stu-id="6715d-110">An organization can sign up for the application, and then assign application roles to its members.</span></span>

<span data-ttu-id="6715d-111">Azure Active Directory (Azure AD) include alcune funzionalità molto utili in tutti questi scenari.</span><span class="sxs-lookup"><span data-stu-id="6715d-111">Azure Active Directory (Azure AD) has some great features that support all of these scenarios.</span></span>

<span data-ttu-id="6715d-112">A supporto di questa serie di articoli, abbiamo creato una completa [implementazione end-to-end][sample-application] di un'applicazione multi-tenant.</span><span class="sxs-lookup"><span data-stu-id="6715d-112">To accompany this series of articles, we created a complete [end-to-end implementation][sample-application] of a multitenant application.</span></span> <span data-ttu-id="6715d-113">Gli articoli riflettono quanto appreso nel processo di compilazione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6715d-113">The articles reflect what we learned in the process of building the application.</span></span> <span data-ttu-id="6715d-114">Per iniziare con l'applicazione, vedere [Eseguire l'applicazione Surveys][running-the-app].</span><span class="sxs-lookup"><span data-stu-id="6715d-114">To get started with the application, see [Run the Surveys application][running-the-app].</span></span>

## <a name="introduction"></a><span data-ttu-id="6715d-115">Introduzione</span><span class="sxs-lookup"><span data-stu-id="6715d-115">Introduction</span></span>

<span data-ttu-id="6715d-116">Si supponga di scrivere un'applicazione SaaS aziendale che deve essere ospitata nel cloud.</span><span class="sxs-lookup"><span data-stu-id="6715d-116">Let's say you're writing an enterprise SaaS application to be hosted in the cloud.</span></span> <span data-ttu-id="6715d-117">L'applicazione avrà alcuni utenti:</span><span class="sxs-lookup"><span data-stu-id="6715d-117">Of course, the application will have users:</span></span>

![Utenti](./images/users.png)

<span data-ttu-id="6715d-119">Ma tali utenti appartengono a organizzazioni:</span><span class="sxs-lookup"><span data-stu-id="6715d-119">But those users belong to organizations:</span></span>

![Utenti dell'organizzazione](./images/org-users.png)

<span data-ttu-id="6715d-121">Esempio: Tailspin vende le sottoscrizioni per la propria applicazione SaaS.</span><span class="sxs-lookup"><span data-stu-id="6715d-121">Example: Tailspin sells subscriptions to its SaaS application.</span></span> <span data-ttu-id="6715d-122">Contoso e Fabrikam si iscrivono all'app.</span><span class="sxs-lookup"><span data-stu-id="6715d-122">Contoso and Fabrikam sign up for the app.</span></span> <span data-ttu-id="6715d-123">Quando Alice (`alice@contoso`) accede, l'applicazione deve sapere che questo utente fa parte di Contoso.</span><span class="sxs-lookup"><span data-stu-id="6715d-123">When Alice (`alice@contoso`) signs in, the application should know that Alice is part of Contoso.</span></span>

- <span data-ttu-id="6715d-124">Alice *deve* avere accesso ai dati di Contoso.</span><span class="sxs-lookup"><span data-stu-id="6715d-124">Alice *should* have access to Contoso data.</span></span>
- <span data-ttu-id="6715d-125">Alice *non deve* avere accesso ai dati di Fabrikam.</span><span class="sxs-lookup"><span data-stu-id="6715d-125">Alice *should not* have access to Fabrikam data.</span></span>

<span data-ttu-id="6715d-126">Queste linee guida illustrano come gestire le identità degli utenti in un'applicazione multi-tenant usando [Azure Active Directory (Azure AD)](/azure/active-directory) per gestire l'accesso e l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6715d-126">This guidance will show you how to manage user identities in a multitenant application, using [Azure Active Directory (Azure AD)](/azure/active-directory) to handle sign-in and authentication.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-is-multitenancy"></a><span data-ttu-id="6715d-127">Che cosa si intende per multi-tenancy?</span><span class="sxs-lookup"><span data-stu-id="6715d-127">What is multitenancy?</span></span>

<!-- markdownlint-enable MD026 -->

<span data-ttu-id="6715d-128">Un *tenant* è un gruppo di utenti.</span><span class="sxs-lookup"><span data-stu-id="6715d-128">A *tenant* is a group of users.</span></span> <span data-ttu-id="6715d-129">In un'applicazione SaaS, il tenant è un sottoscrittore o un cliente dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6715d-129">In a SaaS application, the tenant is a subscriber or customer of the application.</span></span> <span data-ttu-id="6715d-130">*multi-tenancy* si intende un'architettura in cui più tenant condividono la stessa istanza fisica dell'app.</span><span class="sxs-lookup"><span data-stu-id="6715d-130">*Multitenancy* is an architecture where multiple tenants share the same physical instance of the app.</span></span> <span data-ttu-id="6715d-131">Anche se i tenant condividono le risorse fisiche, ad esempio VM o risorse di archiviazione, ogni tenant ottiene la propria istanza logica dell'app.</span><span class="sxs-lookup"><span data-stu-id="6715d-131">Although tenants share physical resources (such as VMs or storage), each tenant gets its own logical instance of the app.</span></span>

<span data-ttu-id="6715d-132">In genere, i dati dell'applicazione vengono condivisi tra gli utenti all'interno di un tenant, ma non con altri tenant.</span><span class="sxs-lookup"><span data-stu-id="6715d-132">Typically, application data is shared among the users within a tenant, but not with other tenants.</span></span>

![Multi-tenant](./images/multitenant.png)

<span data-ttu-id="6715d-134">Si confronti questa architettura con un'architettura a tenant singolo, in cui ogni tenant ha un'istanza fisica dedicata.</span><span class="sxs-lookup"><span data-stu-id="6715d-134">Compare this architecture with a single-tenant architecture, where each tenant has a dedicated physical instance.</span></span> <span data-ttu-id="6715d-135">In un'architettura a tenant singolo, si aggiungono tenant aumentando le istanze dell'app.</span><span class="sxs-lookup"><span data-stu-id="6715d-135">In a single-tenant architecture, you add tenants by spinning up new instances of the app.</span></span>

![Tenant singolo](./images/single-tenant.png)

### <a name="multitenancy-and-horizontal-scaling"></a><span data-ttu-id="6715d-137">Multi-tenancy e scalabilità orizzontale</span><span class="sxs-lookup"><span data-stu-id="6715d-137">Multitenancy and horizontal scaling</span></span>

<span data-ttu-id="6715d-138">Per ottenere la scalabilità nel cloud, in genere si aggiungono istanze fisiche.</span><span class="sxs-lookup"><span data-stu-id="6715d-138">To achieve scale in the cloud, it’s common to add more physical instances.</span></span> <span data-ttu-id="6715d-139">Questo è noto come *scalabilità orizzontale*. Si consideri un'app Web.</span><span class="sxs-lookup"><span data-stu-id="6715d-139">This is known as *horizontal scaling* or *scaling out*. Consider a web app.</span></span> <span data-ttu-id="6715d-140">Per gestire più traffico, è possibile aggiungere altre VM del server e inserirle in un servizio di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="6715d-140">To handle more traffic, you can add more server VMs and put them behind a load balancer.</span></span> <span data-ttu-id="6715d-141">Ogni VM esegue un'istanza fisica separata dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="6715d-141">Each VM runs a separate physical instance of the web app.</span></span>

![Bilanciamento del carico di un sito Web](./images/load-balancing.png)

<span data-ttu-id="6715d-143">Le richieste possono essere indirizzate a qualsiasi istanza.</span><span class="sxs-lookup"><span data-stu-id="6715d-143">Any request can be routed to any instance.</span></span> <span data-ttu-id="6715d-144">Nell'insieme, il sistema funziona come una singola istanza logica.</span><span class="sxs-lookup"><span data-stu-id="6715d-144">Together, the system functions as a single logical instance.</span></span> <span data-ttu-id="6715d-145">È possibile interrompere una VM o aggiungerne una nuova, senza che ciò abbia alcun impatto sugli utenti.</span><span class="sxs-lookup"><span data-stu-id="6715d-145">You can tear down a VM or spin up a new VM, without affecting users.</span></span> <span data-ttu-id="6715d-146">In questa architettura, ogni istanza fisica è di tipo multi-tenant ed è possibile eseguire la scalabilità mediante l'aggiunta di più istanze.</span><span class="sxs-lookup"><span data-stu-id="6715d-146">In this architecture, each physical instance is multi-tenant, and you scale by adding more instances.</span></span> <span data-ttu-id="6715d-147">Se un'istanza viene disattivata, non ha alcun impatto sui tenant.</span><span class="sxs-lookup"><span data-stu-id="6715d-147">If one instance goes down, it should not affect any tenant.</span></span>

## <a name="identity-in-a-multitenant-app"></a><span data-ttu-id="6715d-148">Identità in un'app multi-tenant</span><span class="sxs-lookup"><span data-stu-id="6715d-148">Identity in a multitenant app</span></span>

<span data-ttu-id="6715d-149">In un'app multi-tenant è necessario considerare gli utenti nel contesto dei tenant.</span><span class="sxs-lookup"><span data-stu-id="6715d-149">In a multitenant app, you must consider users in the context of tenants.</span></span>

### <a name="authentication"></a><span data-ttu-id="6715d-150">Authentication</span><span class="sxs-lookup"><span data-stu-id="6715d-150">Authentication</span></span>

- <span data-ttu-id="6715d-151">Gli utenti accedono all'app con le credenziali dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="6715d-151">Users sign into the app with their organization credentials.</span></span> <span data-ttu-id="6715d-152">Non devono creare nuovi profili utente per l'app.</span><span class="sxs-lookup"><span data-stu-id="6715d-152">They don't have to create new user profiles for the app.</span></span>
- <span data-ttu-id="6715d-153">Gli utenti all'interno della stessa organizzazione fanno parte dello stesso tenant.</span><span class="sxs-lookup"><span data-stu-id="6715d-153">Users within the same organization are part of the same tenant.</span></span>
- <span data-ttu-id="6715d-154">Quando un utente esegue l'accesso, l'applicazione conosce a quale tenant appartiene l'utente.</span><span class="sxs-lookup"><span data-stu-id="6715d-154">When a user signs in, the application knows which tenant the user belongs to.</span></span>

### <a name="authorization"></a><span data-ttu-id="6715d-155">Authorization</span><span class="sxs-lookup"><span data-stu-id="6715d-155">Authorization</span></span>

- <span data-ttu-id="6715d-156">Quando si autorizzano azioni dell'utente, ad esempio la visualizzazione di una risorsa, l'app deve tenere in considerazione il tenant dell'utente.</span><span class="sxs-lookup"><span data-stu-id="6715d-156">When authorizing a user's actions (say, viewing a resource), the app must take into account the user's tenant.</span></span>
- <span data-ttu-id="6715d-157">Agli utenti è possibile assegnare ruoli all'interno dell'applicazione, ad esempio "Amministratore" o "Utente Standard".</span><span class="sxs-lookup"><span data-stu-id="6715d-157">Users might be assigned roles within the application, such as "Admin" or "Standard User".</span></span> <span data-ttu-id="6715d-158">Le assegnazioni di ruolo devono essere gestite dal cliente, non dal provider SaaS.</span><span class="sxs-lookup"><span data-stu-id="6715d-158">Role assignments should be managed by the customer, not by the SaaS provider.</span></span>

<span data-ttu-id="6715d-159">**Esempio.**</span><span class="sxs-lookup"><span data-stu-id="6715d-159">**Example.**</span></span> <span data-ttu-id="6715d-160">Alice, un dipendente di Contoso, passa all'applicazione nel proprio browser e fa clic sul pulsante di accesso.</span><span class="sxs-lookup"><span data-stu-id="6715d-160">Alice, an employee at Contoso, navigates to the application in her browser and clicks the “Log in” button.</span></span> <span data-ttu-id="6715d-161">Viene reindirizzata a una schermata di accesso in cui immette le proprie credenziali aziendali (nome utente e password).</span><span class="sxs-lookup"><span data-stu-id="6715d-161">She is redirected to a login screen where she enters her corporate credentials (username and password).</span></span> <span data-ttu-id="6715d-162">A questo punto, viene registrata nell'app come `alice@contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="6715d-162">At this point, she is logged into the app as `alice@contoso.com`.</span></span> <span data-ttu-id="6715d-163">L'applicazione individua in Alice un utente amministratore per questa applicazione.</span><span class="sxs-lookup"><span data-stu-id="6715d-163">The application also knows that Alice is an admin user for this application.</span></span> <span data-ttu-id="6715d-164">Grazie a tale ruolo, Alice può visualizzare un elenco di tutte le risorse appartenenti a Contoso.</span><span class="sxs-lookup"><span data-stu-id="6715d-164">Because she is an admin, she can see a list of all the resources that belong to Contoso.</span></span> <span data-ttu-id="6715d-165">Tuttavia, non può visualizzare le risorse di Fabrikam, poiché ha il ruolo di amministratore solo nel proprio tenant.</span><span class="sxs-lookup"><span data-stu-id="6715d-165">However, she cannot view Fabrikam's resources, because she is an admin only within her tenant.</span></span>

<span data-ttu-id="6715d-166">In queste informazioni aggiuntive si esamina l'uso di Azure AD per la gestione delle identità.</span><span class="sxs-lookup"><span data-stu-id="6715d-166">In this guidance, we'll look specifically at using Azure AD for identity management.</span></span>

- <span data-ttu-id="6715d-167">Si presuppone che il cliente archivia i profili utente in Azure AD (tra cui i tenant di Office 365 e Dynamics CRM).</span><span class="sxs-lookup"><span data-stu-id="6715d-167">We assume the customer stores their user profiles in Azure AD (including Office365 and Dynamics CRM tenants)</span></span>
- <span data-ttu-id="6715d-168">I clienti con Active Directory (AD) locale possono usare [Azure AD Connect](/azure/active-directory/hybrid/whatis-hybrid-identity) per sincronizzare l'istanza locale di Active Directory con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="6715d-168">Customers with on-premise Active Directory (AD) can use [Azure AD Connect](/azure/active-directory/hybrid/whatis-hybrid-identity) to sync their on-premise AD with Azure AD.</span></span>

<span data-ttu-id="6715d-169">Se un cliente con account AD locale non può usare Azure AD Connect (a causa di criteri IT aziendali o per altri motivi), il provider SaaS può attuare la federazione con il cliente di AD mediante Active Directory Federation Services (AD FS).</span><span class="sxs-lookup"><span data-stu-id="6715d-169">If a customer with on-premise AD cannot use Azure AD Connect (due to corporate IT policy or other reasons), the SaaS provider can federate with the customer's AD through Active Directory Federation Services (AD FS).</span></span> <span data-ttu-id="6715d-170">Questa opzione è descritta nell'argomento relativo alla [federazione con un'istanza AD FS del cliente](adfs.md).</span><span class="sxs-lookup"><span data-stu-id="6715d-170">This option is described in [Federating with a customer's AD FS](adfs.md).</span></span>

<span data-ttu-id="6715d-171">Queste informazioni aggiuntive non prendono in considerazione altri aspetti della multi-tenancy, ad esempio il partizionamento dei dati, la configurazione per tenant e così via.</span><span class="sxs-lookup"><span data-stu-id="6715d-171">This guidance does not consider other aspects of multitenancy such as data partitioning, per-tenant configuration, and so forth.</span></span>

[<span data-ttu-id="6715d-172">**Avanti**</span><span class="sxs-lookup"><span data-stu-id="6715d-172">**Next**</span></span>](./tailspin.md)

<!-- links -->

[sample-application]: https://github.com/mspnp/multitenant-saas-guidance
[running-the-app]: ./run-the-app.md
