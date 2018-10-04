---
title: Uso delle identità basate sulle attestazioni nelle applicazioni multi-tenant
description: Come usare le attestazioni per l'autorizzazione e la convalida dell'autorità di certificazione
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: authenticate
pnp.series.next: signup
ms.openlocfilehash: 46c43c9bfa4514f206b5e7eabd9223ad4c61628b
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47429373"
---
# <a name="work-with-claims-based-identities"></a>Usare le identità basate sulle attestazioni

[![GitHub](../_images/github.png) Codice di esempio][sample application]

## <a name="claims-in-azure-ad"></a>Attestazioni in Azure AD
Quando un utente esegue l'accesso, Azure AD invia un token ID che contiene un set di attestazioni relative all'utente. Un'attestazione è semplicemente un'informazione espressa come coppia chiave/valore. Ad esempio, `email`=`bob@contoso.com`.  Le attestazioni hanno un'autorità di certificazione &mdash; in questo caso Azure AD &mdash; che è l'entità che autentica l'utente e crea le attestazioni. Le attestazioni vengono considerate attendibili perché si considera attendibile l'autorità di certificazione. (Al contrario, se non si considera attendibile l'autorità di certificazione, le attestazioni non vengono considerate attendibili.)

In generale:

1. L'utente esegue l'autenticazione.
2. Il provider di identità invia un set di attestazioni.
3. L'app normalizza o aumenta le attestazioni (opzione facoltativa).
4. L'app usa le attestazioni per prendere decisioni in merito all'autorizzazione.

In OpenID Connect, il set di attestazioni che si ottiene è controllato dal [parametro di ambito] della richiesta di autenticazione. Tuttavia, Azure AD rilascia un set limitato di attestazioni tramite OpenID Connect. Vedere [Token e tipi di attestazioni supportati]. Se si vogliono altre informazioni sull'utente, è necessario usare l'API Graph di Azure AD.

Ecco alcune delle attestazioni di AAD di cui in genere si occupa un'app:

| Tipo di attestazione nel token ID | DESCRIZIONE |
| --- | --- |
| aud |Utente per cui è stato emesso il token. Si tratta dell'ID client dell'applicazione. In genere, non è necessario occuparsi di questa attestazione in quanto il middleware ne esegue automaticamente la convalida. Esempio:  `"91464657-d17a-4327-91f3-2ed99386406f"` |
| groups |Elenco di gruppi AAD di cui l'utente è membro. Esempio: `["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]` |
| iss |[Autorità di certificazione] del token OIDC. Esempio: `https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/` |
| name |Nome visualizzato dell'utente. Esempio: `"Alice A."` |
| oid |Identificatore oggetto per l'utente in AAD. Questo valore è l'identificatore non modificabile e non riutilizzabile dell'utente. Usare questo valore e non l'indirizzo di posta elettronica come identificatore univoco per gli utenti. Gli indirizzi di posta elettronica possono cambiare. Se si usa l'API Graph di Azure AD nell'app, l'ID oggetto è il valore usato per richiedere informazioni sul profilo. Esempio: `"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"` |
| roles |Elenco di ruoli app per l'utente.    Esempio: `["SurveyCreator"]` |
| tid |ID tenant. Questo valore è un identificatore univoco per il tenant in Azure AD. Esempio: `"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"` |
| unique_name |Nome visualizzato leggibile dell'utente. Esempio: `"alice@contoso.com"` |
| upn |Nome dell'entità utente. Esempio: `"alice@contoso.com"` |

Questa tabella elenca i tipi di attestazione, come vengono visualizzati nel token ID. In ASP.NET Core, il middleware OpenID Connect converte alcuni dei tipi di attestazione quando popola la raccolta di attestazioni per l'entità utente:

* oid > `http://schemas.microsoft.com/identity/claims/objectidentifier`
* tid > `http://schemas.microsoft.com/identity/claims/tenantid`
* unique_name > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
* upn > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## <a name="claims-transformations"></a>Trasformazioni delle attestazioni
Durante il flusso di autenticazione, può essere necessario modificare le attestazioni ottenute dal provider di identità. In ASP.NET Core è possibile eseguire la trasformazione delle attestazioni all'interno dell'evento **AuthenticationValidated** dal middleware OpenID Connect. Vedere la sezione relativa agli [eventi di autenticazione].

Qualsiasi attestazione aggiunta durante l'evento **AuthenticationValidated** viene memorizzata nel cookie di autenticazione della sessione. Le attestazioni non vengono reindirizzate ad Azure AD.

Ecco alcuni esempi di trasformazioni delle attestazioni:

* **Normalizzazione di attestazioni**o coerenza delle attestazioni tra gli utenti. Si tratta di una trasformazione particolarmente importante se si ottengono attestazioni da più provider di identità che possono usare diversi tipi di attestazione per informazioni analoghe.
  Ad esempio, Azure AD invia un'attestazione "upn" che contiene messaggi di posta elettronica dell'utente. Altri provider di identità possono inviare un'attestazione "email". Il codice seguente converte l'attestazione "upn" in un'attestazione "email":
  
  ```csharp
  var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
  if (!string.IsNullOrWhiteSpace(email))
  {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
  }
  ```
* Aggiungere **valori di attestazione predefiniti** per le attestazioni non presenti &mdash;, ad esempio l'assegnazione di un utente a un ruolo predefinito. In alcuni casi ciò consente di semplificare la logica di autorizzazione.
* Aggiunta di **tipi di attestazione personalizzati** con informazioni specifiche dell'applicazione sull'utente. Ad esempio, è possibile archiviare alcune informazioni sull'utente in un database. È possibile aggiungere un'attestazione personalizzata con queste informazioni al ticket di autenticazione. L'attestazione viene archiviata in un cookie, quindi è sufficiente scaricarla dal database una sola volta per sessione di accesso. Può essere inoltre opportuno evitare di creare cookie di dimensioni troppo grandi, è quindi necessario considerare il compromesso tra la dimensione di un cookie e le ricerche nel database.   

Dopo aver completato il flusso di autenticazione, le attestazioni sono disponibili in `HttpContext.User`. A questo punto è necessario considerarle come una raccolta di sola lettura &mdash;, ad esempio usarle per prendere decisioni di autorizzazione.

## <a name="issuer-validation"></a>Convalida dell'autorità di certificazione
In OpenID Connect, l'attestazione dell'autorità di certificazione ("iss") identifica il provider di identità che ha emesso il token ID. Parte del flusso di autenticazione OIDC consiste nel verificare che l'attestazione dell'autorità di certificazione corrisponda all'autorità di certificazione effettiva. Il middleware OIDC gestisce tale flusso automaticamente.

In Azure AD, il valore dell'autorità di certificazione è univoco per ogni tenant di AD (`https://sts.windows.net/<tenantID>`). Un'applicazione deve quindi eseguire un ulteriore controllo per assicurarsi che l'autorità di certificazione rappresenti un tenant che consente di accedere all'app.

Per un'applicazione single-tenant, è sufficiente verificare che l'autorità di certificazione sia il proprio tenant. Il middleware OIDC esegue tale verifica automaticamente per impostazione predefinita. In un'app multi-tenant, è necessario che siano ammesse più autorità di certificazione, corrispondenti a tenant diversi. Ecco un approccio generale da usare:

* Nelle opzioni del middleware OIDC impostare **ValidateIssuer** su false. In questo modo, il controllo automatico viene disattivato.
* Quando un tenant esegue l'iscrizione, archiviare il tenant e l'autorità di certificazione nel database dell'utente.
* Ogni volta che un utente accede, cercare l'autorità di certificazione nel database. Se l'autorità di certificazione non viene rilevata, significa che non è stata eseguita l'iscrizione. È possibile reindirizzare gli utenti a una pagina di iscrizione.
* È inoltre possibile disattivare determinati tenant, ad esempio quelli relativi ai clienti che non hanno pagato l'abbonamento.

Per informazioni più dettagliate, vedere [Iscrizione e onboarding del tenant in un'applicazione multi-tenant][signup].

## <a name="using-claims-for-authorization"></a>Uso delle attestazioni per le autorizzazioni
Con le attestazioni, l'identità di un utente non è più un'entità monolitica. Ad esempio, un utente potrebbe avere indirizzo di posta elettronica, numero di telefono, data di nascita, sesso e così via. È possibile che il provider di identità dell'utente archivi tutte queste informazioni. Quando si autentica l'utente, tuttavia, in genere si ottiene un subset di queste informazioni sotto forma di attestazioni. In questo modello, l'identità dell'utente è semplicemente un'aggregazione di attestazioni. Quando si prendono decisioni di autorizzazione relative a un utente, si cercano particolari set di attestazioni. In altre parole, la domanda "L'utente X può eseguire l'azione Y?" diventa "L'utente X dispone dell'attestazione Z?".

Ecco alcuni modelli di base per la verifica delle attestazioni.

* Per verificare che l'utente abbia un'attestazione specifica con un valore specifico:
  
   ```csharp
   if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
   ```
   Questo codice verifica se l'utente ha un'attestazione di ruolo con il valore "Admin". Gestisce correttamente il caso in cui l'utente non ha alcuna attestazione di ruolo o ha più attestazioni di ruolo.
  
   La classe **ClaimType** definisce le costanti per i tipi di attestazione di uso comune. Tuttavia, è possibile usare qualsiasi valore stringa per il tipo di attestazione.
* Per ottenere un singolo valore in relazione a un tipo di attestazione, quando si prevede che possa essere presente al massimo un valore:
  
  ```csharp
  string email = User.FindFirst(ClaimTypes.Email)?.Value;
  ```
* Per ottenere tutti i valori di un tipo di attestazione:
  
  ```csharp
  IEnumerable<Claim> groups = User.FindAll("groups");
  ```

Per altre informazioni, vedere [Autorizzazione basata sui ruoli e sulle risorse nelle applicazioni multi-tenant][authorization].

[**Avanti**][signup]


<!-- Links -->

[parametro di ambito]: https://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Token e tipi di attestazioni supportati]: /azure/active-directory/active-directory-token-and-claims/
[Autorità di certificazione]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Eventi di autenticazione]: authenticate.md#authentication-events
[signup]: signup.md
[Claims-Based Authorization]: /aspnet/core/security/authorization/claims
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
[authorization]: authorize.md
