---
title: "Gestione delle identità per le applicazioni multi-tenant"
description: "Procedure consigliate per l'autenticazione, l'autorizzazione e la gestione delle identità in applicazioni multi-tenant."
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.next: tailspin
ms.openlocfilehash: c363ac01e798b522fa95f39586e28fe3af5fae4a
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="manage-identity-in-multitenant-applications"></a>Gestire le identità nelle applicazioni multi-tenant

Questa serie di articoli descrive le procedure consigliate per il multi-tenancy, quando si usa Azure AD per l'autenticazione e la gestione delle identità.

[![Codice di esempio](../_images/github.png) GitHub][sample application]

Quando si crea un'applicazione multi-tenant, una delle attività principali è la gestione delle identità degli utenti, ognuno dei quali appartiene ora a un tenant. Ad esempio:

* Gli utenti accedono con le credenziali dell'organizzazione.
* Gli utenti devono avere accesso ai dati dell'organizzazione ma non ai dati appartenenti ad altri tenant.
* Un'organizzazione può effettuare l'iscrizione per l'applicazione e quindi assegnare i ruoli applicazione ai propri membri.

Azure Active Directory (Azure AD) include alcune funzionalità molto utili in tutti questi scenari.

A supporto di questa serie di articoli, abbiamo creato una completa [implementazione end-to-end][sample application] di un'applicazione multi-tenant. Gli articoli riflettono quanto appreso nel processo di compilazione dell'applicazione. Per iniziare con l'applicazione, vedere [Eseguire l'applicazione Surveys][running-the-app].

## <a name="introduction"></a>Introduzione

Si supponga di scrivere un'applicazione SaaS aziendale che deve essere ospitata nel cloud. L'applicazione avrà alcuni utenti:

![Utenti](./images/users.png)

Ma tali utenti appartengono a organizzazioni:

![Utenti dell'organizzazione](./images/org-users.png)

Esempio: Tailspin vende le sottoscrizioni per la propria applicazione SaaS. Contoso e Fabrikam si iscrivono all'app. Quando Alice (`alice@contoso`) accede, l'applicazione deve sapere che questo utente fa parte di Contoso.

* Alice *deve* avere accesso ai dati di Contoso.
* Alice *non deve* avere accesso ai dati di Fabrikam.

Questo materiale sussidiario illustra come gestire le entità utente in un'applicazione multi-tenant usando [Azure Active Directory][AzureAD] (Azure AD) per gestire l'accesso e l'autenticazione.

## <a name="what-is-multitenancy"></a>Che cosa si intende per multi-tenancy?
Un *tenant* è un gruppo di utenti. In un'applicazione SaaS, il tenant è un sottoscrittore o un cliente dell'applicazione. Per *multi-tenancy* si intende un'architettura in cui più tenant condividono la stessa istanza fisica dell'app. Anche se i tenant condividono le risorse fisiche, ad esempio VM o risorse di archiviazione, ogni tenant ottiene la propria istanza logica dell'app.

In genere, i dati dell'applicazione vengono condivisi tra gli utenti all'interno di un tenant, ma non con altri tenant.

![Multi-tenant](./images/multitenant.png)

Si confronti questa architettura con un'architettura a tenant singolo, in cui ogni tenant ha un'istanza fisica dedicata. In un'architettura a tenant singolo, si aggiungono tenant aumentando le istanze dell'app.

![Tenant singolo](./images/single-tenant.png)

### <a name="multitenancy-and-horizontal-scaling"></a>Multi-tenancy e scalabilità orizzontale
Per ottenere la scalabilità nel cloud, in genere si aggiungono istanze fisiche. Questo è noto come *scalabilità orizzontale*. Si consideri un'app Web. Per gestire più traffico, è possibile aggiungere altre VM del server e inserirle in un servizio di bilanciamento del carico. Ogni VM esegue un'istanza fisica separata dell'app Web.

![Bilanciamento del carico di un sito Web](./images/load-balancing.png)

Le richieste possono essere indirizzate a qualsiasi istanza. Nell'insieme, il sistema funziona come una singola istanza logica. È possibile interrompere una VM o aggiungerne una nuova, senza che ciò abbia alcun impatto sugli utenti. In questa architettura, ogni istanza fisica è di tipo multi-tenant ed è possibile eseguire la scalabilità mediante l'aggiunta di più istanze. Se un'istanza viene disattivata, non ha alcun impatto sui tenant.

## <a name="identity-in-a-multitenant-app"></a>Identità in un'app multi-tenant
In un'app multi-tenant è necessario considerare gli utenti nel contesto dei tenant.

**Autenticazione**

* Gli utenti accedono all'app con le credenziali dell'organizzazione. Non devono creare nuovi profili utente per l'app.
* Gli utenti all'interno della stessa organizzazione fanno parte dello stesso tenant.
* Quando un utente esegue l'accesso, l'applicazione conosce a quale tenant appartiene l'utente.

**Autorizzazione**

* Quando si autorizzano azioni dell'utente, ad esempio la visualizzazione di una risorsa, l'app deve tenere in considerazione il tenant dell'utente.
* Agli utenti è possibile assegnare ruoli all'interno dell'applicazione, ad esempio "Amministratore" o "Utente Standard". Le assegnazioni di ruolo devono essere gestite dal cliente, non dal provider SaaS.

**Esempio.** Alice, un dipendente di Contoso, passa all'applicazione nel proprio browser e fa clic sul pulsante di accesso. Viene reindirizzata a una schermata di accesso in cui immette le proprie credenziali aziendali (nome utente e password). A questo punto, viene registrata nell'app come `alice@contoso.com`. L'applicazione individua in Alice un utente amministratore per questa applicazione. Grazie a tale ruolo, Alice può visualizzare un elenco di tutte le risorse appartenenti a Contoso. Tuttavia, non può visualizzare le risorse di Fabrikam, poiché ha il ruolo di amministratore solo nel proprio tenant.

In queste informazioni aggiuntive si esamina l'uso di Azure AD per la gestione delle identità.

* Si presuppone che il cliente archivia i profili utente in Azure AD (tra cui i tenant di Office 365 e Dynamics CRM).
* I clienti con Active Directory (AD) locale possono usare [Azure AD Connect][ADConnect] per sincronizzare i relativi AD locali con Azure AD.

Se un cliente con account AD locale non può usare Azure AD Connect (a causa di criteri IT aziendali o per altri motivi), il provider SaaS può attuare la federazione con il cliente di AD mediante Active Directory Federation Services (AD FS). Questa opzione è descritta nell'argomento relativo alla [federazione con un'istanza AD FS del cliente].

Queste informazioni aggiuntive non prendono in considerazione altri aspetti della multi-tenancy, ad esempio il partizionamento dei dati, la configurazione per tenant e così via.

[**Avanti**][tailpin]



<!-- Links -->
[ADConnect]: /azure/active-directory/active-directory-aadconnect
[AzureAD]: /azure/active-directory

[federazione con un'istanza AD FS del cliente]: adfs.md
[tailpin]: tailspin.md

[running-the-app]: ./run-the-app.md
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
