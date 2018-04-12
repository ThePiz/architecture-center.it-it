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
---
# <a name="role-based-and-resource-based-authorization"></a>Autorizzazione basata sui ruoli e sulle risorse

[![GitHub](../_images/github.png) Codice di esempio][sample application]

L' [implementazione di riferimento] è un'applicazione ASP.NET Core. In questo articolo vengono esaminati due approcci generali alle autorizzazioni usando le API di autorizzazione fornite in ASP.NET Core.

* **Role-based authorization**. Autorizzazione di un'azione in base ai ruoli assegnati a un utente. Per alcune azioni, ad esempio, è richiesto un ruolo di amministratore.
* **Autorizzazione basata sulle risorse**. Autorizzazione di un'azione in base a una determinata risorsa. Ogni risorsa è, ad esempio, assegnata a un proprietario e solo il proprietario può eliminare la risorsa.

In genere, un'app può impiegare una combinazione di entrambi gli approcci. Per eliminare una risorsa, ad esempio, l'utente deve essere proprietario *o* amministratore della risorsa.

## <a name="role-based-authorization"></a>Autorizzazione basata sui ruoli
L'applicazione [Tailspin Surveys][Tailspin] definisce i ruoli seguenti:

* Amministratore. Può eseguire tutte le operazioni CRUD in qualsiasi sondaggio che appartiene al tenant.
* Autore. Può creare nuovi sondaggi.
* Lettore. Può leggere tutti i sondaggi che appartengono al tenant.

I ruoli si applicano agli *utenti* dell'applicazione. Nell'applicazione Surveys, un utente può essere amministratore, autore o lettore.

Per una discussione su come definire e gestire i ruoli, vedere l'articolo relativo ai [ruoli dell'applicazione].

Il codice di autorizzazione risulterà invariato indipendentemente dalla modalità di gestione dei ruoli. ASP.NET Core 1.0 include un'astrazione denominata [Criteri di autorizzazione][policies]. Questa funzionalità consente di definire i criteri di autorizzazione nel codice e quindi applicare i criteri alle azioni del controller. I criteri vengono separati dal controller.

### <a name="create-policies"></a>Creare criteri
Per definire i criteri, creare innanzitutto una classe che implementa `IAuthorizationRequirement`. La procedura più semplice consiste nella derivazione da `AuthorizationHandler`. Nel metodo `Handle` esaminare le attestazioni pertinenti.

Ecco un esempio dell'applicazione Tailspin Surveys:

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

Questa classe definisce i requisiti che consentono a un utente di creare un nuovo sondaggio. L'utente deve avere il ruolo SurveyAdmin o SurveyCreator.

Nella classe di avvio, definire i criteri denominati che includono uno o più requisiti. Se sono presenti più requisiti, l'utente deve soddisfare *ogni* requisito da autorizzare. Il codice seguente definisce due criteri:

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

Questo codice consente inoltre di impostare lo schema di autenticazione, che indica ad ASP.NET il middleware di autenticazione da eseguire se l'autorizzazione non riesce. In questo caso, viene specificato il middleware di autenticazione dei cookie, in quanto tale middleware può reindirizzare gli utenti a una pagina di accesso negato. Il percorso della pagina di accesso negato è impostato nell'opzione `AccessDeniedPath` del middleware dei cookie. Vedere [Configurazione del middleware di autenticazione].

### <a name="authorize-controller-actions"></a>Autorizzare le azioni del controller
Infine, per autorizzare un'azione in un controller MVC, impostare i criteri nell'attributo `Authorize` :

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
public IActionResult Create()
{
    var survey = new SurveyDTO();
    return View(survey);
}
```

Nelle versioni precedenti di ASP.NET, è necessario impostare la proprietà **Roles** sull'attributo:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]
```

Questa opzione è ancora supportata in ASP.NET Core, ma presenta alcuni svantaggi rispetto ai criteri di autorizzazione:

* Presuppone un particolare tipo di attestazione. I criteri possono controllare la presenza di qualsiasi tipo di attestazione. I ruoli sono semplicemente un tipo di attestazione.
* Il nome del ruolo è hardcoded nell'attributo. Con i criteri, la logica di autorizzazione si trova in un'unica posizione, rendendo più semplice le operazioni di aggiornamento o caricamento dalle impostazioni di configurazione.
* I criteri consentono di abilitare decisioni di autorizzazione più complesse, ad esempio età >= 21, che non possono essere espresse dalla semplice appartenenza al ruolo.

## <a name="resource-based-authorization"></a>Autorizzazione basata sulle risorse
L'*autorizzazione basata sulle risorse* viene eseguita ogni volta che l'autorizzazione dipende da una risorsa specifica che verrà influenzata da un'operazione. Nell'applicazione Tailspin Surveys ogni sondaggio ha un proprietario e collaboratori zero-a-molti.

* Il proprietario può leggere, aggiornare, eliminare, pubblicare e annullare la pubblicazione del sondaggio.
* Il proprietario può assegnare collaboratori al sondaggio.
* I collaboratori possono leggere e aggiornare il sondaggio.

Si noti che "proprietario" e "collaboratore" non sono ruoli applicazione e vengono archiviati per ogni sondaggio nel database dell'applicazione. Per verificare se un utente può eliminare un sondaggio, ad esempio, l'app verifica se l'utente è il proprietario di tale sondaggio.

Per implementare l'autorizzazione basata sulle risorse, in ASP.NET Core usare la derivazione da **AuthorizationHandler** ed eseguire l'override del metodo **Handle**.

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
    protected override void HandleRequirementAsync(AuthorizationHandlerContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Si noti che questa classe è fortemente tipizzata per gli oggetti Survey.  Registrare la classe per l'inserimento delle dipendenze all'avvio:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Per eseguire i controlli di autorizzazione, usare l'interfaccia **IAuthorizationService** che è possibile inserire nei controller. Il codice seguente controlla se un utente può leggere un sondaggio:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return StatusCode(403);
}
```

Dal momento che viene passato un oggetto `Survey`, questa chiamata richiamerà `SurveyAuthorizationHandler`.

Nel codice di autorizzazione, un buon approccio consiste nell'aggregazione di tutte le autorizzazioni dell'utente basate sui ruoli e sulle risorse e nella verifica del set di aggregazione in base all'operazione desiderata.
Ecco un esempio relativo all'app Surveys. L'applicazione definisce diversi tipi di autorizzazioni:

* Admin
* Collaboratore
* Autore
* Proprietario
* Reader

Inoltre, l'applicazione definisce un set di operazioni possibili sui sondaggi:

* Create
* Read
* Update
* Delete
* Publish
* Unpublish

Il codice seguente crea un elenco di autorizzazioni per un utente e un sondaggio specifici. Si noti che questo codice esamina sia i ruoli dell'app dell'utente sia i campi proprietario o collaboratore del sondaggio.

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

In un'applicazione multi-tenant è necessario assicurarsi che le autorizzazioni non vengano applicate inavvertitamente ai dati di un altro tenant. Nell'app Surveys l'autorizzazione di collaboratore è consentita tra i vari tenant ed è quindi possibile assegnare come collaboratore un utente di un altro tenant. Gli altri tipi di autorizzazione sono limitati a risorse che appartengono al tenant di tale utente. Per applicare questo requisito, il codice verifica l'ID tenant prima di concedere l'autorizzazione. Il campo `TenantId` viene assegnato durante la creazione del sondaggio.

Il passaggio successivo consiste nella verifica dell'operazione (read, update, delete e così via) in base alle autorizzazioni. L'app Surveys implementa questa operazione usando una tabella di ricerca di funzioni:

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

[**Avanti**][web-api]

<!-- Links -->
[Tailspin]: tailspin.md

[ruoli dell'applicazione]: app-roles.md
[policies]: /aspnet/core/security/authorization/policies
[implementazione di riferimento]: tailspin.md
[Configurazione del middleware di autenticazione]: authenticate.md#configure-the-auth-middleware
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
[web-api]: web-api.md
