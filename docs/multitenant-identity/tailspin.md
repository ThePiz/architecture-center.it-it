---
title: Informazioni sull'applicazione Tailspin Surveys
description: Panoramica dell'applicazione Tailspin Surveys
author: MikeWasson
ms.date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: index
pnp.series.next: authenticate
ms.openlocfilehash: a1c357bd1b5306d1255c66aaea96d86be55e7b77
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902069"
---
# <a name="the-tailspin-scenario"></a>Lo scenario Tailspin

[![GitHub](../_images/github.png) Codice di esempio][sample application]

Tailspin è una società fittizia che sta sviluppando un'applicazione SaaS denominata Surveys. Questa applicazione consente alle organizzazioni di creare e pubblicare sondaggi online.

* Le organizzazioni possono iscriversi per l'applicazione.
* Dopo aver iscritto l'organizzazione, gli utenti possono accedere all'applicazione con le proprie credenziali aziendali.
* Gli utenti possono creare, modificare e pubblicare i  sondaggi.

> [!NOTE]
> Per iniziare a usare l'applicazione, vedere [Eseguire l'applicazione Surveys].
> 
> 

## <a name="users-can-create-edit-and-view-surveys"></a>Gli utenti possono creare, modificare e visualizzare sondaggi
Un utente autenticato può visualizzare tutti i sondaggi che ha creato o per cui ha diritti di collaboratore, nonché creare nuovi sondaggi. Si noti che l'utente è connesso con la propria identità aziendale, `bob@contoso.com`.

![App Surveys](./images/surveys-screenshot.png)

Questa schermata mostra la pagina Edit Survey:

![Modifica del sondaggio](./images/edit-survey.png)

Gli utenti possono anche visualizzare i sondaggi creati da altri utenti all'interno dello stesso tenant.

![Sondaggi del tenant](./images/tenant-surveys.png)

## <a name="survey-owners-can-invite-contributors"></a>I proprietari dei sondaggi possono invitare i collaboratori
Quando crea un sondaggio, l'utente può invitare altre persone a collaborare nel sondaggio. I collaboratori possono modificare il sondaggio, ma non possono eliminarlo o pubblicarlo.  

![Aggiungere un collaboratore](./images/add-contributor.png)

L'utente può aggiungere collaboratori di altri tenant consentendo in questo modo la condivisione di risorse tra tenant. In questo screenshot Bob (`bob@contoso.com`) sta aggiungendo Alice (`alice@fabrikam.com`) come collaboratore a un sondaggio che ha creato lui stesso.

Quando Alice esegue l'accesso, il sondaggio sarà visualizzato in "Surveys I can contribute to".

![Collaboratore del sondaggio](./images/contributor.png)

Si noti che Alice accede al proprio tenant e non come guest al tenant Contoso. Alice ha autorizzazioni di collaboratore solo per questo sondaggio e non può visualizzare altri sondaggi dal tenant Contoso.

## <a name="architecture"></a>Architettura
L'applicazione Surveys è costituita da un'API Web di front-end e un'API Web di back-end. Entrambe le API vengono implementate usando [ASP.NET Core].

L'applicazione Web usa Azure Active Directory (Azure AD) per l'autenticazione degli utenti. L'applicazione Web chiama inoltre Azure AD per ottenere i token di accesso OAuth 2 per l'API Web. I token di accesso sono memorizzati nella cache in Cache Redis di Azure. La cache consente a più istanze di condividere la stessa cache dei token (ad esempio, in una server farm).

![Architettura](./images/architecture.png)

[**Avanti**][authentication]

<!-- Links -->

[authentication]: authenticate.md

[Eseguire l'applicazione Surveys]: ./run-the-app.md
[ASP.NET Core]: /aspnet/core
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
