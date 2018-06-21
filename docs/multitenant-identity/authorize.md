---
title: Autorizzazioni nelle applicazioni multi-tenant
description: Come eseguire le autorizzazioni in un'applicazione multi-tenant
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: app-roles
pnp.series.next: web-api
ms.openlocfilehash: 03c4d5fa10c75437a7b066534619ba9a123c350c
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30849672"
---
# <a name="role-based-and-resource-based-authorization"></a><span data-ttu-id="7ba03-103">Autorizzazione basata sui ruoli e sulle risorse</span><span class="sxs-lookup"><span data-stu-id="7ba03-103">Role-based and resource-based authorization</span></span>

<span data-ttu-id="7ba03-104">[![GitHub](../_images/github.png) Codice di esempio][sample application]</span><span class="sxs-lookup"><span data-stu-id="7ba03-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="7ba03-105">L' [implementazione di riferimento] è un'applicazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba03-105">Our [reference implementation] is an ASP.NET Core application.</span></span> <span data-ttu-id="7ba03-106">In questo articolo vengono esaminati due approcci generali alle autorizzazioni usando le API di autorizzazione fornite in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba03-106">In this article we'll look at two general approaches to authorization, using the authorization APIs provided in ASP.NET Core.</span></span>

* <span data-ttu-id="7ba03-107">**Role-based authorization**.</span><span class="sxs-lookup"><span data-stu-id="7ba03-107">**Role-based authorization**.</span></span> <span data-ttu-id="7ba03-108">Autorizzazione di un'azione in base ai ruoli assegnati a un utente.</span><span class="sxs-lookup"><span data-stu-id="7ba03-108">Authorizing an action based on the roles assigned to a user.</span></span> <span data-ttu-id="7ba03-109">Per alcune azioni, ad esempio, è richiesto un ruolo di amministratore.</span><span class="sxs-lookup"><span data-stu-id="7ba03-109">For example, some actions require an administrator role.</span></span>
* <span data-ttu-id="7ba03-110">**Autorizzazione basata sulle risorse**.</span><span class="sxs-lookup"><span data-stu-id="7ba03-110">**Resource-based authorization**.</span></span> <span data-ttu-id="7ba03-111">Autorizzazione di un'azione in base a una determinata risorsa.</span><span class="sxs-lookup"><span data-stu-id="7ba03-111">Authorizing an action based on a particular resource.</span></span> <span data-ttu-id="7ba03-112">Ogni risorsa è, ad esempio, assegnata a un proprietario</span><span class="sxs-lookup"><span data-stu-id="7ba03-112">For example, every resource has an owner.</span></span> <span data-ttu-id="7ba03-113">e solo il proprietario può eliminare la risorsa.</span><span class="sxs-lookup"><span data-stu-id="7ba03-113">The owner can delete the resource; other users cannot.</span></span>

<span data-ttu-id="7ba03-114">In genere, un'app può impiegare una combinazione di entrambi gli approcci.</span><span class="sxs-lookup"><span data-stu-id="7ba03-114">A typical app will employ a mix of both.</span></span> <span data-ttu-id="7ba03-115">Per eliminare una risorsa, ad esempio, l'utente deve essere proprietario *o* amministratore della risorsa.</span><span class="sxs-lookup"><span data-stu-id="7ba03-115">For example, to delete a resource, the user must be the resource owner *or* an admin.</span></span>

## <a name="role-based-authorization"></a><span data-ttu-id="7ba03-116">Autorizzazione basata sui ruoli</span><span class="sxs-lookup"><span data-stu-id="7ba03-116">Role-Based Authorization</span></span>
<span data-ttu-id="7ba03-117">L'applicazione [Tailspin Surveys][Tailspin] definisce i ruoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="7ba03-117">The [Tailspin Surveys][Tailspin] application defines the following roles:</span></span>

* <span data-ttu-id="7ba03-118">Amministratore.</span><span class="sxs-lookup"><span data-stu-id="7ba03-118">Administrator.</span></span> <span data-ttu-id="7ba03-119">Può eseguire tutte le operazioni CRUD in qualsiasi sondaggio che appartiene al tenant.</span><span class="sxs-lookup"><span data-stu-id="7ba03-119">Can perform all CRUD operations on any survey that belongs to that tenant.</span></span>
* <span data-ttu-id="7ba03-120">Autore.</span><span class="sxs-lookup"><span data-stu-id="7ba03-120">Creator.</span></span> <span data-ttu-id="7ba03-121">Può creare nuovi sondaggi.</span><span class="sxs-lookup"><span data-stu-id="7ba03-121">Can create new surveys</span></span>
* <span data-ttu-id="7ba03-122">Lettore.</span><span class="sxs-lookup"><span data-stu-id="7ba03-122">Reader.</span></span> <span data-ttu-id="7ba03-123">Può leggere tutti i sondaggi che appartengono al tenant.</span><span class="sxs-lookup"><span data-stu-id="7ba03-123">Can read any surveys that belong to that tenant</span></span>

<span data-ttu-id="7ba03-124">I ruoli si applicano agli *utenti* dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7ba03-124">Roles apply to *users* of the application.</span></span> <span data-ttu-id="7ba03-125">Nell'applicazione Surveys, un utente può essere amministratore, autore o lettore.</span><span class="sxs-lookup"><span data-stu-id="7ba03-125">In the Surveys application, a user is either an administrator, creator, or reader.</span></span>

<span data-ttu-id="7ba03-126">Per una discussione su come definire e gestire i ruoli, vedere l'articolo relativo ai [ruoli dell'applicazione].</span><span class="sxs-lookup"><span data-stu-id="7ba03-126">For a discussion of how to define and manage roles, see [Application roles].</span></span>

<span data-ttu-id="7ba03-127">Il codice di autorizzazione risulterà invariato indipendentemente dalla modalità di gestione dei ruoli.</span><span class="sxs-lookup"><span data-stu-id="7ba03-127">Regardless of how you manage the roles, your authorization code will look similar.</span></span> <span data-ttu-id="7ba03-128">ASP.NET Core 1.0 include un'astrazione denominata [Criteri di autorizzazione][policies].</span><span class="sxs-lookup"><span data-stu-id="7ba03-128">ASP.NET Core has an abstraction called [authorization policies][policies].</span></span> <span data-ttu-id="7ba03-129">Questa funzionalità consente di definire i criteri di autorizzazione nel codice e quindi applicare i criteri alle azioni del controller.</span><span class="sxs-lookup"><span data-stu-id="7ba03-129">With this feature, you define authorization policies in code, and then apply those policies to controller actions.</span></span> <span data-ttu-id="7ba03-130">I criteri vengono separati dal controller.</span><span class="sxs-lookup"><span data-stu-id="7ba03-130">The policy is decoupled from the controller.</span></span>

### <a name="create-policies"></a><span data-ttu-id="7ba03-131">Creare criteri</span><span class="sxs-lookup"><span data-stu-id="7ba03-131">Create policies</span></span>
<span data-ttu-id="7ba03-132">Per definire i criteri, creare innanzitutto una classe che implementa `IAuthorizationRequirement`.</span><span class="sxs-lookup"><span data-stu-id="7ba03-132">To define a policy, first create a class that implements `IAuthorizationRequirement`.</span></span> <span data-ttu-id="7ba03-133">La procedura più semplice consiste nella derivazione da `AuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="7ba03-133">It's easiest to derive from `AuthorizationHandler`.</span></span> <span data-ttu-id="7ba03-134">Nel metodo `Handle` esaminare le attestazioni pertinenti.</span><span class="sxs-lookup"><span data-stu-id="7ba03-134">In the `Handle` method, examine the relevant claim(s).</span></span>

<span data-ttu-id="7ba03-135">Ecco un esempio dell'applicazione Tailspin Surveys:</span><span class="sxs-lookup"><span data-stu-id="7ba03-135">Here is an example from the Tailspin Surveys application:</span></span>

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) || 
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="7ba03-136">Questa classe definisce i requisiti che consentono a un utente di creare un nuovo sondaggio.</span><span class="sxs-lookup"><span data-stu-id="7ba03-136">This class defines the requirement for a user to create a new survey.</span></span> <span data-ttu-id="7ba03-137">L'utente deve avere il ruolo SurveyAdmin o SurveyCreator.</span><span class="sxs-lookup"><span data-stu-id="7ba03-137">The user must be in the SurveyAdmin or SurveyCreator role.</span></span>

<span data-ttu-id="7ba03-138">Nella classe di avvio, definire i criteri denominati che includono uno o più requisiti.</span><span class="sxs-lookup"><span data-stu-id="7ba03-138">In your startup class, define a named policy that includes one or more requirements.</span></span> <span data-ttu-id="7ba03-139">Se sono presenti più requisiti, l'utente deve soddisfare *ogni* requisito da autorizzare.</span><span class="sxs-lookup"><span data-stu-id="7ba03-139">If there are multiple requirements, the user must meet *every* requirement to be authorized.</span></span> <span data-ttu-id="7ba03-140">Il codice seguente definisce due criteri:</span><span class="sxs-lookup"><span data-stu-id="7ba03-140">The following code defines two policies:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.RequireAuthenticatedUser(); // Adds DenyAnonymousAuthorizationRequirement 
            // By adding the CookieAuthenticationDefaults.AuthenticationScheme, if an authenticated
            // user is not in the appropriate role, they will be redirected to a "forbidden" page.
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.RequireAuthenticatedUser();  
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

<span data-ttu-id="7ba03-141">Questo codice consente inoltre di impostare lo schema di autenticazione, che indica ad ASP.NET il middleware di autenticazione da eseguire se l'autorizzazione non riesce.</span><span class="sxs-lookup"><span data-stu-id="7ba03-141">This code also sets the authentication scheme, which tells ASP.NET which authentication middleware should run if authorization fails.</span></span> <span data-ttu-id="7ba03-142">In questo caso, viene specificato il middleware di autenticazione dei cookie, in quanto tale middleware può reindirizzare gli utenti a una pagina di accesso negato.</span><span class="sxs-lookup"><span data-stu-id="7ba03-142">In this case, we specify the cookie authentication middleware, because the cookie authentication middleware can redirect the user to a "Forbidden" page.</span></span> <span data-ttu-id="7ba03-143">Il percorso della pagina di accesso negato è impostato nell'opzione `AccessDeniedPath` del middleware dei cookie. Vedere [Configurazione del middleware di autenticazione].</span><span class="sxs-lookup"><span data-stu-id="7ba03-143">The location of the Forbidden page is set in the `AccessDeniedPath` option for the cookie middleware; see [Configuring the authentication middleware].</span></span>

### <a name="authorize-controller-actions"></a><span data-ttu-id="7ba03-144">Autorizzare le azioni del controller</span><span class="sxs-lookup"><span data-stu-id="7ba03-144">Authorize controller actions</span></span>
<span data-ttu-id="7ba03-145">Infine, per autorizzare un'azione in un controller MVC, impostare i criteri nell'attributo `Authorize` :</span><span class="sxs-lookup"><span data-stu-id="7ba03-145">Finally, to authorize an action in an MVC controller, set the policy in the `Authorize` attribute:</span></span>

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
public IActionResult Create()
{
    var survey = new SurveyDTO();
    return View(survey);
}
```

<span data-ttu-id="7ba03-146">Nelle versioni precedenti di ASP.NET, è necessario impostare la proprietà **Roles** sull'attributo:</span><span class="sxs-lookup"><span data-stu-id="7ba03-146">In earlier versions of ASP.NET, you would set the **Roles** property on the attribute:</span></span>

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]
```

<span data-ttu-id="7ba03-147">Questa opzione è ancora supportata in ASP.NET Core, ma presenta alcuni svantaggi rispetto ai criteri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="7ba03-147">This is still supported in ASP.NET Core, but it has some drawbacks compared with authorization policies:</span></span>

* <span data-ttu-id="7ba03-148">Presuppone un particolare tipo di attestazione.</span><span class="sxs-lookup"><span data-stu-id="7ba03-148">It assumes a particular claim type.</span></span> <span data-ttu-id="7ba03-149">I criteri possono controllare la presenza di qualsiasi tipo di attestazione.</span><span class="sxs-lookup"><span data-stu-id="7ba03-149">Policies can check for any claim type.</span></span> <span data-ttu-id="7ba03-150">I ruoli sono semplicemente un tipo di attestazione.</span><span class="sxs-lookup"><span data-stu-id="7ba03-150">Roles are just a type of claim.</span></span>
* <span data-ttu-id="7ba03-151">Il nome del ruolo è hardcoded nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="7ba03-151">The role name is hard-coded into the attribute.</span></span> <span data-ttu-id="7ba03-152">Con i criteri, la logica di autorizzazione si trova in un'unica posizione, rendendo più semplice le operazioni di aggiornamento o caricamento dalle impostazioni di configurazione.</span><span class="sxs-lookup"><span data-stu-id="7ba03-152">With policies, the authorization logic is all in one place, making it easier to update or even load from configuration settings.</span></span>
* <span data-ttu-id="7ba03-153">I criteri consentono di abilitare decisioni di autorizzazione più complesse, ad esempio età >= 21, che non possono essere espresse dalla semplice appartenenza al ruolo.</span><span class="sxs-lookup"><span data-stu-id="7ba03-153">Policies enable more complex authorization decisions (e.g., age >= 21) that can't be expressed by simple role membership.</span></span>

## <a name="resource-based-authorization"></a><span data-ttu-id="7ba03-154">Autorizzazione basata sulle risorse</span><span class="sxs-lookup"><span data-stu-id="7ba03-154">Resource based authorization</span></span>
<span data-ttu-id="7ba03-155">L'*autorizzazione basata sulle risorse* viene eseguita ogni volta che l'autorizzazione dipende da una risorsa specifica che verrà influenzata da un'operazione.</span><span class="sxs-lookup"><span data-stu-id="7ba03-155">*Resource based authorization* occurs whenever the authorization depends on a specific resource that will be affected by an operation.</span></span> <span data-ttu-id="7ba03-156">Nell'applicazione Tailspin Surveys ogni sondaggio ha un proprietario e collaboratori zero-a-molti.</span><span class="sxs-lookup"><span data-stu-id="7ba03-156">In the Tailspin Surveys application, every survey has an owner and zero-to-many contributors.</span></span>

* <span data-ttu-id="7ba03-157">Il proprietario può leggere, aggiornare, eliminare, pubblicare e annullare la pubblicazione del sondaggio.</span><span class="sxs-lookup"><span data-stu-id="7ba03-157">The owner can read, update, delete, publish, and unpublish the survey.</span></span>
* <span data-ttu-id="7ba03-158">Il proprietario può assegnare collaboratori al sondaggio.</span><span class="sxs-lookup"><span data-stu-id="7ba03-158">The owner can assign contributors to the survey.</span></span>
* <span data-ttu-id="7ba03-159">I collaboratori possono leggere e aggiornare il sondaggio.</span><span class="sxs-lookup"><span data-stu-id="7ba03-159">Contributors can read and update the survey.</span></span>

<span data-ttu-id="7ba03-160">Si noti che "proprietario" e "collaboratore" non sono ruoli applicazione e vengono archiviati per ogni sondaggio nel database dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7ba03-160">Note that "owner" and "contributor" are not application roles; they are stored per survey, in the application database.</span></span> <span data-ttu-id="7ba03-161">Per verificare se un utente può eliminare un sondaggio, ad esempio, l'app verifica se l'utente è il proprietario di tale sondaggio.</span><span class="sxs-lookup"><span data-stu-id="7ba03-161">To check whether a user can delete a survey, for example, the app checks whether the user is the owner for that survey.</span></span>

<span data-ttu-id="7ba03-162">Per implementare l'autorizzazione basata sulle risorse, in ASP.NET Core usare la derivazione da **AuthorizationHandler** ed eseguire l'override del metodo **Handle**.</span><span class="sxs-lookup"><span data-stu-id="7ba03-162">In ASP.NET Core, implement resource-based authorization by deriving from **AuthorizationHandler** and overriding the **Handle** method.</span></span>

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
    protected override void HandleRequirementAsync(AuthorizationHandlerContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

<span data-ttu-id="7ba03-163">Si noti che questa classe è fortemente tipizzata per gli oggetti Survey.</span><span class="sxs-lookup"><span data-stu-id="7ba03-163">Notice that this class is strongly typed for Survey objects.</span></span>  <span data-ttu-id="7ba03-164">Registrare la classe per l'inserimento delle dipendenze all'avvio:</span><span class="sxs-lookup"><span data-stu-id="7ba03-164">Register the class for DI on startup:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

<span data-ttu-id="7ba03-165">Per eseguire i controlli di autorizzazione, usare l'interfaccia **IAuthorizationService** che è possibile inserire nei controller.</span><span class="sxs-lookup"><span data-stu-id="7ba03-165">To perform authorization checks, use the **IAuthorizationService** interface, which you can inject into your controllers.</span></span> <span data-ttu-id="7ba03-166">Il codice seguente controlla se un utente può leggere un sondaggio:</span><span class="sxs-lookup"><span data-stu-id="7ba03-166">The following code checks whether a user can read a survey:</span></span>

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return StatusCode(403);
}
```

<span data-ttu-id="7ba03-167">Dal momento che viene passato un oggetto `Survey`, questa chiamata richiamerà `SurveyAuthorizationHandler`.</span><span class="sxs-lookup"><span data-stu-id="7ba03-167">Because we pass in a `Survey` object, this call will invoke the `SurveyAuthorizationHandler`.</span></span>

<span data-ttu-id="7ba03-168">Nel codice di autorizzazione, un buon approccio consiste nell'aggregazione di tutte le autorizzazioni dell'utente basate sui ruoli e sulle risorse e nella verifica del set di aggregazione in base all'operazione desiderata.</span><span class="sxs-lookup"><span data-stu-id="7ba03-168">In your authorization code, a good approach is to aggregate all of the user's role-based and resource-based permissions, then check the aggregate set against the desired operation.</span></span>
<span data-ttu-id="7ba03-169">Ecco un esempio relativo all'app Surveys.</span><span class="sxs-lookup"><span data-stu-id="7ba03-169">Here is an example from the Surveys app.</span></span> <span data-ttu-id="7ba03-170">L'applicazione definisce diversi tipi di autorizzazioni:</span><span class="sxs-lookup"><span data-stu-id="7ba03-170">The application defines several permission types:</span></span>

* <span data-ttu-id="7ba03-171">Admin</span><span class="sxs-lookup"><span data-stu-id="7ba03-171">Admin</span></span>
* <span data-ttu-id="7ba03-172">Collaboratore</span><span class="sxs-lookup"><span data-stu-id="7ba03-172">Contributor</span></span>
* <span data-ttu-id="7ba03-173">Autore</span><span class="sxs-lookup"><span data-stu-id="7ba03-173">Creator</span></span>
* <span data-ttu-id="7ba03-174">Proprietario</span><span class="sxs-lookup"><span data-stu-id="7ba03-174">Owner</span></span>
* <span data-ttu-id="7ba03-175">Reader</span><span class="sxs-lookup"><span data-stu-id="7ba03-175">Reader</span></span>

<span data-ttu-id="7ba03-176">Inoltre, l'applicazione definisce un set di operazioni possibili sui sondaggi:</span><span class="sxs-lookup"><span data-stu-id="7ba03-176">The application also defines a set of possible operations on surveys:</span></span>

* <span data-ttu-id="7ba03-177">Create</span><span class="sxs-lookup"><span data-stu-id="7ba03-177">Create</span></span>
* <span data-ttu-id="7ba03-178">Read</span><span class="sxs-lookup"><span data-stu-id="7ba03-178">Read</span></span>
* <span data-ttu-id="7ba03-179">Update</span><span class="sxs-lookup"><span data-stu-id="7ba03-179">Update</span></span>
* <span data-ttu-id="7ba03-180">Delete</span><span class="sxs-lookup"><span data-stu-id="7ba03-180">Delete</span></span>
* <span data-ttu-id="7ba03-181">Publish</span><span class="sxs-lookup"><span data-stu-id="7ba03-181">Publish</span></span>
* <span data-ttu-id="7ba03-182">Unpublish</span><span class="sxs-lookup"><span data-stu-id="7ba03-182">Unpublsh</span></span>

<span data-ttu-id="7ba03-183">Il codice seguente crea un elenco di autorizzazioni per un utente e un sondaggio specifici.</span><span class="sxs-lookup"><span data-stu-id="7ba03-183">The following code creates a list of permissions for a particular user and survey.</span></span> <span data-ttu-id="7ba03-184">Si noti che questo codice esamina sia i ruoli dell'app dell'utente sia i campi proprietario o collaboratore del sondaggio.</span><span class="sxs-lookup"><span data-stu-id="7ba03-184">Notice that this code looks at both the user's app roles, and the owner/contributor fields in the survey.</span></span>

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, OperationAuthorizationRequirement requirement, Survey resource)
    {
        var permissions = new List<UserPermissionType>();
        int surveyTenantId = context.User.GetSurveyTenantIdValue();
        int userId = context.User.GetSurveyUserIdValue();
        string user = context.User.GetUserName();

        if (resource.TenantId == surveyTenantId)
        {
            // Admin can do anything, as long as the resource belongs to the admin's tenant.
            if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
            {
                context.Succeed(requirement);
                return Task.FromResult(0);
            }

            if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
            {
                permissions.Add(UserPermissionType.Creator);
            }
            else
            {
                permissions.Add(UserPermissionType.Reader);
            }

            if (resource.OwnerId == userId)
            {
                permissions.Add(UserPermissionType.Owner);
            }
        }
        if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
        {
            permissions.Add(UserPermissionType.Contributor);
        }

        if (ValidateUserPermissions[requirement](permissions))
        {
            context.Succeed(requirement);
        }
        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="7ba03-185">In un'applicazione multi-tenant è necessario assicurarsi che le autorizzazioni non vengano applicate inavvertitamente ai dati di un altro tenant.</span><span class="sxs-lookup"><span data-stu-id="7ba03-185">In a multi-tenant application, you must ensure that permissions don't "leak" to another tenant's data.</span></span> <span data-ttu-id="7ba03-186">Nell'app Surveys l'autorizzazione di collaboratore è consentita tra i vari tenant ed è quindi possibile assegnare come collaboratore un utente di un altro tenant.</span><span class="sxs-lookup"><span data-stu-id="7ba03-186">In the Surveys app, the Contributor permission is allowed across tenants &mdash; you can assign someone from another tenant as a contriubutor.</span></span> <span data-ttu-id="7ba03-187">Gli altri tipi di autorizzazione sono limitati a risorse che appartengono al tenant di tale utente.</span><span class="sxs-lookup"><span data-stu-id="7ba03-187">The other permission types are restricted to resources that belong to that user's tenant.</span></span> <span data-ttu-id="7ba03-188">Per applicare questo requisito, il codice verifica l'ID tenant prima di concedere l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="7ba03-188">To enforce this requirement, the code checks the tenant ID before granting the permission.</span></span> <span data-ttu-id="7ba03-189">Il campo `TenantId` viene assegnato durante la creazione del sondaggio.</span><span class="sxs-lookup"><span data-stu-id="7ba03-189">(The `TenantId` field as assigned when the survey is created.)</span></span>

<span data-ttu-id="7ba03-190">Il passaggio successivo consiste nella verifica dell'operazione (read, update, delete e così via) in base alle autorizzazioni.</span><span class="sxs-lookup"><span data-stu-id="7ba03-190">The next step is to check the operation (read, update, delete, etc) against the permissions.</span></span> <span data-ttu-id="7ba03-191">L'app Surveys implementa questa operazione usando una tabella di ricerca di funzioni:</span><span class="sxs-lookup"><span data-stu-id="7ba03-191">The Surveys app implements this step by using a lookup table of functions:</span></span>

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

<span data-ttu-id="7ba03-192">[**Avanti**][web-api]</span><span class="sxs-lookup"><span data-stu-id="7ba03-192">[**Next**][web-api]</span></span>

<!-- Links -->
[Tailspin]: tailspin.md

[ruoli dell'applicazione]: app-roles.md
[Application roles]: app-roles.md
[policies]: /aspnet/core/security/authorization/policies
[implementazione di riferimento]: tailspin.md
[reference implementation]: tailspin.md
[Configurazione del middleware di autenticazione]: authenticate.md#configure-the-auth-middleware
[Configuring the authentication middleware]: authenticate.md#configure-the-auth-middleware
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
[web-api]: web-api.md
