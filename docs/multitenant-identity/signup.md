---
title: Iscrizione e onboarding del tenant in applicazioni multi-tenant
description: Come eseguire l'onboarding dei tenant in un'applicazione multi-tenant.
author: MikeWasson
ms.date: 07/21/2017
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: claims
pnp.series.next: app-roles
ms.openlocfilehash: eb4e65b20ec3339b633b65d2adad768e98d1bdbb
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640601"
---
# <a name="tenant-sign-up-and-onboarding"></a><span data-ttu-id="09b3f-103">Iscrizione e onboarding del tenant</span><span class="sxs-lookup"><span data-stu-id="09b3f-103">Tenant sign-up and onboarding</span></span>

<span data-ttu-id="09b3f-104">[![GitHub](../_images/github.png) Codice di esempio][sample application]</span><span class="sxs-lookup"><span data-stu-id="09b3f-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="09b3f-105">Questo articolo descrive come implementare un *processo* di iscrizione in un'applicazione multi-tenant, che consente a un cliente di iscrivere la propria organizzazione all'applicazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-105">This article describes how to implement a *sign-up* process in a multi-tenant application, which allows a customer to sign up their organization for your application.</span></span>
<span data-ttu-id="09b3f-106">È possibile implementare un processo di iscrizione per diversi motivi:</span><span class="sxs-lookup"><span data-stu-id="09b3f-106">There are several reasons to implement a sign-up process:</span></span>

* <span data-ttu-id="09b3f-107">Consentire a un amministratore di AD di concedere all'intera organizzazione di un cliente di usare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-107">Allow an AD admin to consent for the customer's entire organization to use the application.</span></span>
* <span data-ttu-id="09b3f-108">Raccogliere informazioni sul cliente, ad esempio relative al pagamento con carta di credito.</span><span class="sxs-lookup"><span data-stu-id="09b3f-108">Collect credit card payment or other customer information.</span></span>
* <span data-ttu-id="09b3f-109">Eseguire installazioni occasionali per ogni tenant richieste dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-109">Perform any one-time per-tenant setup needed by your application.</span></span>

## <a name="admin-consent-and-azure-ad-permissions"></a><span data-ttu-id="09b3f-110">Consenso dell'amministratore e autorizzazioni di Azure AD</span><span class="sxs-lookup"><span data-stu-id="09b3f-110">Admin consent and Azure AD permissions</span></span>

<span data-ttu-id="09b3f-111">Per l'autenticazione con Azure AD, un'applicazione deve accedere alla directory dell'utente.</span><span class="sxs-lookup"><span data-stu-id="09b3f-111">In order to authenticate with Azure AD, an application needs access to the user's directory.</span></span> <span data-ttu-id="09b3f-112">Come requisito minimo, l'applicazione necessita dell'autorizzazione per leggere il profilo dell'utente.</span><span class="sxs-lookup"><span data-stu-id="09b3f-112">At a minimum, the application needs permission to read the user's profile.</span></span> <span data-ttu-id="09b3f-113">Al primo accesso dell'utente, Azure AD illustra una pagina di consenso che elenca le autorizzazioni richieste.</span><span class="sxs-lookup"><span data-stu-id="09b3f-113">The first time that a user signs in, Azure AD shows a consent page that lists the permissions being requested.</span></span> <span data-ttu-id="09b3f-114">Facendo clic su **Accetta**, l'utente concede l'autorizzazione all'applicazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-114">By clicking **Accept**, the user grants permission to the application.</span></span>

<span data-ttu-id="09b3f-115">Per impostazione predefinita, viene fornito il consenso per ogni utente.</span><span class="sxs-lookup"><span data-stu-id="09b3f-115">By default, consent is granted on a per-user basis.</span></span> <span data-ttu-id="09b3f-116">Ogni utente che accede vede la pagina di consenso.</span><span class="sxs-lookup"><span data-stu-id="09b3f-116">Every user who signs in sees the consent page.</span></span> <span data-ttu-id="09b3f-117">Tuttavia, Azure AD supporta anche il *consenso dell'amministratore*, che consente a un amministratore di AD di fornire il consenso per un'intera organizzazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-117">However, Azure AD also supports  *admin consent*, which allows an AD administrator to consent for an entire organization.</span></span>

<span data-ttu-id="09b3f-118">Quando viene usato il flusso di consenso dell'amministratore, la pagina di consenso indica che l'amministratore di AD concede l'autorizzazione per conto dell'intero tenant:</span><span class="sxs-lookup"><span data-stu-id="09b3f-118">When the admin consent flow is used, the consent page states that the AD admin is granting permission on behalf of the entire tenant:</span></span>

![Richiesta di consenso dell'amministratore](./images/admin-consent.png)

<span data-ttu-id="09b3f-120">Dopo che l'amministratore seleziona **Accetta**, altri utenti all'interno dello stesso tenant possono accedere e Azure AD ignorerà la schermata di consenso.</span><span class="sxs-lookup"><span data-stu-id="09b3f-120">After the admin clicks **Accept**, other users within the same tenant can sign in, and Azure AD will skip the consent screen.</span></span>

<span data-ttu-id="09b3f-121">Solo un amministratore di AD può autorizzare questo tipo di consenso, in quanto concede l'autorizzazione per conto dell'intera organizzazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-121">Only an AD administrator can give admin consent, because it grants permission on behalf of the entire organization.</span></span> <span data-ttu-id="09b3f-122">Se un utente non amministratore tenta di eseguire l'autenticazione con il flusso di consenso dell'amministratore, Azure AD visualizza un errore:</span><span class="sxs-lookup"><span data-stu-id="09b3f-122">If a non-administrator tries to authenticate with the admin consent flow, Azure AD displays an error:</span></span>

![Errore di consenso](./images/consent-error.png)

<span data-ttu-id="09b3f-124">Se l'applicazione richiede autorizzazioni aggiuntive in un secondo momento, il cliente dovrà accedere nuovamente e acconsentire alle autorizzazioni aggiornate.</span><span class="sxs-lookup"><span data-stu-id="09b3f-124">If the application requires additional permissions at a later point, the customer will need to sign up again and consent to the updated permissions.</span></span>

## <a name="implementing-tenant-sign-up"></a><span data-ttu-id="09b3f-125">Implementazione dell'iscrizione del tenant</span><span class="sxs-lookup"><span data-stu-id="09b3f-125">Implementing tenant sign-up</span></span>

<span data-ttu-id="09b3f-126">Sono stati definiti diversi requisiti del processo di iscrizione per l'applicazione [Tailspin Surveys][Tailspin]:</span><span class="sxs-lookup"><span data-stu-id="09b3f-126">For the [Tailspin Surveys][Tailspin] application,  we defined several requirements for the sign-up process:</span></span>

* <span data-ttu-id="09b3f-127">Affinché gli utenti possano accedere, il tenant deve eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="09b3f-127">A tenant must sign up before users can sign in.</span></span>
* <span data-ttu-id="09b3f-128">L'iscrizione usa il flusso di consenso dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="09b3f-128">Sign-up uses the admin consent flow.</span></span>
* <span data-ttu-id="09b3f-129">L'iscrizione aggiunge il tenant dell'utente al database dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-129">Sign-up adds the user's tenant to the application database.</span></span>
* <span data-ttu-id="09b3f-130">Dopo che un tenant esegue l'iscrizione, l'applicazione visualizza una pagina per l'onboarding.</span><span class="sxs-lookup"><span data-stu-id="09b3f-130">After a tenant signs up, the application shows an onboarding page.</span></span>

<span data-ttu-id="09b3f-131">In questa sezione viene illustrata l'implementazione del processo di iscrizione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-131">In this section, we'll walk through our implementation of the sign-up process.</span></span>
<span data-ttu-id="09b3f-132">È importante comprendere che "iscrizione" e "accesso" sono concetti riferiti all'applicazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-132">It's important to understand that "sign up" versus "sign in" is an application concept.</span></span> <span data-ttu-id="09b3f-133">Durante il flusso di autenticazione, Azure AD non è in grado di capire se per l'utente è in corso la procedura di iscrizione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-133">During the authentication flow, Azure AD does not inherently know whether the user is in process of signing up.</span></span> <span data-ttu-id="09b3f-134">Spetta infatti all'applicazione tenere traccia del contesto.</span><span class="sxs-lookup"><span data-stu-id="09b3f-134">It's up to the application to keep track of the context.</span></span>

<span data-ttu-id="09b3f-135">Quando un utente anonimo visita l'applicazione Surveys, visualizza due pulsanti: uno per accedere e uno per la registrazione della società (iscrizione).</span><span class="sxs-lookup"><span data-stu-id="09b3f-135">When an anonymous user visits the Surveys application, the user is shown two buttons, one to sign in, and one to "enroll your company" (sign up).</span></span>

![Pagina di iscrizione dell'applicazione](./images/sign-up-page.png)

<span data-ttu-id="09b3f-137">Questi pulsanti richiamano le azioni nella classe `AccountController`.</span><span class="sxs-lookup"><span data-stu-id="09b3f-137">These buttons invoke actions in the `AccountController` class.</span></span>

<span data-ttu-id="09b3f-138">Il `SignIn` azione restituisce un **ChallengeResult**, in modo che il middleware OpenID Connect per il reindirizzamento all'endpoint di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-138">The `SignIn` action returns a **ChallengeResult**, which causes the OpenID Connect middleware to redirect to the authentication endpoint.</span></span> <span data-ttu-id="09b3f-139">Questa è la modalità predefinita per attivare l'autenticazione in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="09b3f-139">This is the default way to trigger authentication in ASP.NET Core.</span></span>

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

<span data-ttu-id="09b3f-140">Confrontarla a questo punto con l'azione `SignUp` :</span><span class="sxs-lookup"><span data-stu-id="09b3f-140">Now compare the `SignUp` action:</span></span>

```csharp
[AllowAnonymous]
public IActionResult SignUp()
{
    var state = new Dictionary<string, string> { { "signup", "true" }};
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties(state)
        {
            RedirectUri = Url.Action(nameof(SignUpCallback), "Account")
        });
}
```

<span data-ttu-id="09b3f-141">Come `SignIn`, l'azione `SignUp` restituisce anche un `ChallengeResult`.</span><span class="sxs-lookup"><span data-stu-id="09b3f-141">Like `SignIn`, the `SignUp` action also returns a `ChallengeResult`.</span></span> <span data-ttu-id="09b3f-142">Ma questa volta, viene aggiunta una parte di informazioni sullo stato di `AuthenticationProperties` nel `ChallengeResult`:</span><span class="sxs-lookup"><span data-stu-id="09b3f-142">But this time, we add a piece of state information to the `AuthenticationProperties` in the `ChallengeResult`:</span></span>

* <span data-ttu-id="09b3f-143">signup: flag booleano che indica che l'utente ha avviato il processo di iscrizione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-143">signup: A Boolean flag, indicating that the user has started the sign-up process.</span></span>

<span data-ttu-id="09b3f-144">Le informazioni sullo stato in `AuthenticationProperties` vengono aggiunte al parametro [state] di OpenID Connect che esegue un ciclo durante il flusso di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-144">The state information in `AuthenticationProperties` gets added to the OpenID Connect [state] parameter, which round trips during the authentication flow.</span></span>

![Parametro state](./images/state-parameter.png)

<span data-ttu-id="09b3f-146">Dopo che l'utente viene autenticato in Azure AD e viene reindirizzato all'applicazione, il ticket di autenticazione contiene informazioni sullo stato.</span><span class="sxs-lookup"><span data-stu-id="09b3f-146">After the user authenticates in Azure AD and gets redirected back to the application, the authentication ticket contains the state.</span></span> <span data-ttu-id="09b3f-147">Questo aspetto viene usato per assicurarsi che il valore "signup" venga mantenuto per l'intero flusso di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-147">We are using this fact to make sure the "signup" value persists across the entire authentication flow.</span></span>

## <a name="adding-the-admin-consent-prompt"></a><span data-ttu-id="09b3f-148">Aggiunta della richiesta di consenso dell'amministratore</span><span class="sxs-lookup"><span data-stu-id="09b3f-148">Adding the admin consent prompt</span></span>

<span data-ttu-id="09b3f-149">In Azure AD, il flusso di consenso dell'amministratore viene attivato mediante l'aggiunta di un parametro "prompt" alla stringa di query nella richiesta di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="09b3f-149">In Azure AD, the admin consent flow is triggered by adding a "prompt" parameter to the query string in the authentication request:</span></span>

<!-- markdownlint-disable MD040 -->

```
/authorize?prompt=admin_consent&...
```

<!-- markdownlint-enable MD040 -->

<span data-ttu-id="09b3f-150">L'applicazione Surveys aggiunge tale parametro durante l'evento `RedirectToAuthenticationEndpoint` .</span><span class="sxs-lookup"><span data-stu-id="09b3f-150">The Surveys application adds the prompt during the `RedirectToAuthenticationEndpoint` event.</span></span> <span data-ttu-id="09b3f-151">Questo evento viene chiamato subito prima che il middleware esegua il reindirizzamento all'endpoint di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-151">This event is called right before the middleware redirects to the authentication endpoint.</span></span>

```csharp
public override Task RedirectToAuthenticationEndpoint(RedirectContext context)
{
    if (context.IsSigningUp())
    {
        context.ProtocolMessage.Prompt = "admin_consent";
    }

    _logger.RedirectToIdentityProvider();
    return Task.FromResult(0);
}
```

<span data-ttu-id="09b3f-152">L'impostazione `ProtocolMessage.Prompt` indica al middleware di aggiungere il parametro "prompt" alla richiesta di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-152">Setting `ProtocolMessage.Prompt` tells the middleware to add the "prompt" parameter to the authentication request.</span></span>

<span data-ttu-id="09b3f-153">Si noti che la richiesta è necessaria solo durante l'iscrizione,</span><span class="sxs-lookup"><span data-stu-id="09b3f-153">Note that the prompt is only needed during sign-up.</span></span> <span data-ttu-id="09b3f-154">in un accesso normale non è inclusa.</span><span class="sxs-lookup"><span data-stu-id="09b3f-154">Regular sign-in should not include it.</span></span> <span data-ttu-id="09b3f-155">Per comprendere la differenza, viene verificato il valore di `signup` nello stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-155">To distinguish between them, we check for the `signup` value in the authentication state.</span></span> <span data-ttu-id="09b3f-156">Il metodo di estensione seguente consente di verificare questa condizione:</span><span class="sxs-lookup"><span data-stu-id="09b3f-156">The following extension method checks for this condition:</span></span>

```csharp
internal static bool IsSigningUp(this BaseControlContext context)
{
    Guard.ArgumentNotNull(context, nameof(context));

    string signupValue;
    // Check the HTTP context and convert to string
    if ((context.Ticket == null) ||
        (!context.Ticket.Properties.Items.TryGetValue("signup", out signupValue)))
    {
        return false;
    }

    // We have found the value, so see if it's valid
    bool isSigningUp;
    if (!bool.TryParse(signupValue, out isSigningUp))
    {
        // The value for signup is not a valid boolean, throw

        throw new InvalidOperationException($"'{signupValue}' is an invalid boolean value");
    }

    return isSigningUp;
}
```

## <a name="registering-a-tenant"></a><span data-ttu-id="09b3f-157">Registrazione di un tenant</span><span class="sxs-lookup"><span data-stu-id="09b3f-157">Registering a tenant</span></span>

<span data-ttu-id="09b3f-158">L'applicazione Surveys archivia alcune informazioni su ogni tenant e su ogni utente nel database dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-158">The Surveys application stores some information about each tenant and user in the application database.</span></span>

![Tabella Tenant](./images/tenant-table.png)

<span data-ttu-id="09b3f-160">Nella tabella Tenant, IssuerValue è il valore dell'attestazione dell'autorità di certificazione del tenant.</span><span class="sxs-lookup"><span data-stu-id="09b3f-160">In the Tenant table, IssuerValue is the value of the issuer claim for the tenant.</span></span> <span data-ttu-id="09b3f-161">Per Azure AD, si tratta di `https://sts.windows.net/<tentantID>` e fornisce un valore univoco per ogni tenant.</span><span class="sxs-lookup"><span data-stu-id="09b3f-161">For Azure AD, this is `https://sts.windows.net/<tentantID>` and gives a unique value per tenant.</span></span>

<span data-ttu-id="09b3f-162">Quando un nuovo tenant esegue l'iscrizione, l'applicazione Surveys scrive un record di tenant al database.</span><span class="sxs-lookup"><span data-stu-id="09b3f-162">When a new tenant signs up, the Surveys application writes a tenant record to the database.</span></span> <span data-ttu-id="09b3f-163">Ciò si verifica all'interno dell'evento `AuthenticationValidated` .</span><span class="sxs-lookup"><span data-stu-id="09b3f-163">This happens inside the `AuthenticationValidated` event.</span></span> <span data-ttu-id="09b3f-164">È opportuno non eseguire questa operazione prima di questo evento, in quanto non sarà possibile convalidare il token ID e di conseguenza i valori dell'attestazione non saranno attendibili.</span><span class="sxs-lookup"><span data-stu-id="09b3f-164">(Don't do it before this event, because the ID token won't be validated yet, so you can't trust the claim values.</span></span> <span data-ttu-id="09b3f-165">Vedere l'argomento relativo all' [autenticazione].</span><span class="sxs-lookup"><span data-stu-id="09b3f-165">See [Authentication].</span></span>

<span data-ttu-id="09b3f-166">Ecco il codice pertinente dell'applicazione Surveys:</span><span class="sxs-lookup"><span data-stu-id="09b3f-166">Here is the relevant code from the Surveys application:</span></span>

```csharp
public override async Task TokenValidated(TokenValidatedContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var userId = principal.GetObjectIdentifierValue();
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    _logger.AuthenticationValidated(userId, issuerValue);

    // Normalize the claims first.
    NormalizeClaims(principal);
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue)
        .ConfigureAwait(false);

    if (context.IsSigningUp())
    {
        if (tenant == null)
        {
            tenant = await SignUpTenantAsync(context, tenantManager)
                .ConfigureAwait(false);
        }

        // In this case, we need to go ahead and set up the user signing us up.
        await CreateOrUpdateUserAsync(context.Ticket, userManager, tenant)
            .ConfigureAwait(false);
    }
    else
    {
        if (tenant == null)
        {
            _logger.UnregisteredUserSignInAttempted(userId, issuerValue);
            throw new SecurityTokenValidationException($"Tenant {issuerValue} is not registered");
        }

        await CreateOrUpdateUserAsync(context.Ticket, userManager, tenant)
            .ConfigureAwait(false);
    }
}
```

<span data-ttu-id="09b3f-167">Il codice esegue le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="09b3f-167">This code does the following:</span></span>

1. <span data-ttu-id="09b3f-168">Verifica se il valore dell'autorità di certificazione del tenant è già nel database.</span><span class="sxs-lookup"><span data-stu-id="09b3f-168">Check if the tenant's issuer value is already in the database.</span></span> <span data-ttu-id="09b3f-169">Se il tenant non ha eseguito l'iscrizione, `FindByIssuerValueAsync` restituisce null.</span><span class="sxs-lookup"><span data-stu-id="09b3f-169">If the tenant has not signed up, `FindByIssuerValueAsync` returns null.</span></span>
2. <span data-ttu-id="09b3f-170">Se l'utente sta eseguendo l'iscrizione:</span><span class="sxs-lookup"><span data-stu-id="09b3f-170">If the user is signing up:</span></span>
   1. <span data-ttu-id="09b3f-171">Aggiunge il tenant al database (`SignUpTenantAsync`).</span><span class="sxs-lookup"><span data-stu-id="09b3f-171">Add the tenant to the database (`SignUpTenantAsync`).</span></span>
   2. <span data-ttu-id="09b3f-172">Aggiunge l'utente autenticato al database (`CreateOrUpdateUserAsync`).</span><span class="sxs-lookup"><span data-stu-id="09b3f-172">Add the authenticated user to the database (`CreateOrUpdateUserAsync`).</span></span>
3. <span data-ttu-id="09b3f-173">In caso contrario, completa il normale flusso di accesso:</span><span class="sxs-lookup"><span data-stu-id="09b3f-173">Otherwise complete the normal sign-in flow:</span></span>
   1. <span data-ttu-id="09b3f-174">Se l'autorità di certificazione del tenant non viene trovata nel database, significa che il tenant non è registrato e il cliente deve eseguire l'iscrizione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-174">If the tenant's issuer was not found in the database, it means the tenant is not registered, and the customer needs to sign up.</span></span> <span data-ttu-id="09b3f-175">In questo caso, viene generata un'eccezione che causa l'esito negativo dell'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="09b3f-175">In that case, throw an exception to cause the authentication to fail.</span></span>
   2. <span data-ttu-id="09b3f-176">In caso contrario, viene creato un record di database per questo utente, se non ne esiste già uno (`CreateOrUpdateUserAsync`).</span><span class="sxs-lookup"><span data-stu-id="09b3f-176">Otherwise, create a database record for this user, if there isn't one already (`CreateOrUpdateUserAsync`).</span></span>

<span data-ttu-id="09b3f-177">Ecco il metodo `SignUpTenantAsync` che permette di aggiungere il tenant al database.</span><span class="sxs-lookup"><span data-stu-id="09b3f-177">Here is the `SignUpTenantAsync` method that adds the tenant to the database.</span></span>

```csharp
private async Task<Tenant> SignUpTenantAsync(BaseControlContext context, TenantManager tenantManager)
{
    Guard.ArgumentNotNull(context, nameof(context));
    Guard.ArgumentNotNull(tenantManager, nameof(tenantManager));

    var principal = context.Ticket.Principal;
    var issuerValue = principal.GetIssuerValue();
    var tenant = new Tenant
    {
        IssuerValue = issuerValue,
        Created = DateTimeOffset.UtcNow
    };

    try
    {
        await tenantManager.CreateAsync(tenant)
            .ConfigureAwait(false);
    }
    catch(Exception ex)
    {
        _logger.SignUpTenantFailed(principal.GetObjectIdentifierValue(), issuerValue, ex);
        throw;
    }

    return tenant;
}
```

<span data-ttu-id="09b3f-178">Di seguito è riportato un riepilogo dell'intero flusso di iscrizione all'applicazione Surveys:</span><span class="sxs-lookup"><span data-stu-id="09b3f-178">Here is a summary of the entire sign-up flow in the Surveys application:</span></span>

1. <span data-ttu-id="09b3f-179">L'utente seleziona il pulsante **Iscriviti** .</span><span class="sxs-lookup"><span data-stu-id="09b3f-179">The user clicks the **Sign Up** button.</span></span>
2. <span data-ttu-id="09b3f-180">Il `AccountController.SignUp` azione restituisce un risultato della richiesta di verifica.</span><span class="sxs-lookup"><span data-stu-id="09b3f-180">The `AccountController.SignUp` action returns a challenge result.</span></span>  <span data-ttu-id="09b3f-181">Lo stato dell'autenticazione include il valore "signup".</span><span class="sxs-lookup"><span data-stu-id="09b3f-181">The authentication state includes "signup" value.</span></span>
3. <span data-ttu-id="09b3f-182">Nell'evento `RedirectToAuthenticationEndpoint` aggiungere la richiesta `admin_consent`.</span><span class="sxs-lookup"><span data-stu-id="09b3f-182">In the `RedirectToAuthenticationEndpoint` event, add the `admin_consent` prompt.</span></span>
4. <span data-ttu-id="09b3f-183">Il middleware OpenID Connect esegue il reindirizzamento ad Azure AD e l'utente viene autenticato.</span><span class="sxs-lookup"><span data-stu-id="09b3f-183">The OpenID Connect middleware redirects to Azure AD and the user authenticates.</span></span>
5. <span data-ttu-id="09b3f-184">Nell'evento `AuthenticationValidated` cercare lo stato "signup".</span><span class="sxs-lookup"><span data-stu-id="09b3f-184">In the `AuthenticationValidated` event, look for the "signup" state.</span></span>
6. <span data-ttu-id="09b3f-185">Aggiungere il tenant al database.</span><span class="sxs-lookup"><span data-stu-id="09b3f-185">Add the tenant to the database.</span></span>

<span data-ttu-id="09b3f-186">[**Avanti**][app roles]</span><span class="sxs-lookup"><span data-stu-id="09b3f-186">[**Next**][app roles]</span></span>

<!-- links -->

[app roles]: app-roles.md
[Tailspin]: tailspin.md

[state]: https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest
[autenticazione]: authenticate.md
[Authentication]: authenticate.md
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
