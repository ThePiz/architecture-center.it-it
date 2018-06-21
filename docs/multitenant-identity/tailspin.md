---
title: Informazioni sull'applicazione Tailspin Surveys
description: Panoramica dell'applicazione Tailspin Surveys
author: MikeWasson
ms:date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: index
pnp.series.next: authenticate
ms.openlocfilehash: 028f7940d2e3cd7e8e629554f8af290ec5fdd184
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
ms.locfileid: "24540058"
---
# <a name="the-tailspin-scenario"></a><span data-ttu-id="eee39-103">Scenario Tailspin</span><span class="sxs-lookup"><span data-stu-id="eee39-103">The Tailspin scenario</span></span>

<span data-ttu-id="eee39-104">[![Codice di esempio](../_images/github.png) GitHub][sample application]</span><span class="sxs-lookup"><span data-stu-id="eee39-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="eee39-105">Tailspin è una società fittizia che sta sviluppando un'applicazione SaaS denominata Surveys.</span><span class="sxs-lookup"><span data-stu-id="eee39-105">Tailspin is a fictitious company that is developing a SaaS application named Surveys.</span></span> <span data-ttu-id="eee39-106">Questa applicazione consente alle organizzazioni di creare e pubblicare sondaggi online.</span><span class="sxs-lookup"><span data-stu-id="eee39-106">This application enables organizations to create and publish online surveys.</span></span>

* <span data-ttu-id="eee39-107">Le organizzazioni possono iscriversi per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="eee39-107">An organization can sign up for the application.</span></span>
* <span data-ttu-id="eee39-108">Dopo aver iscritto l'organizzazione, gli utenti possono accedere all'applicazione con le proprie credenziali aziendali.</span><span class="sxs-lookup"><span data-stu-id="eee39-108">After the organization is signed up, users can sign into the application with their organizational credentials.</span></span>
* <span data-ttu-id="eee39-109">Gli utenti possono creare, modificare e pubblicare i  sondaggi.</span><span class="sxs-lookup"><span data-stu-id="eee39-109">Users can create, edit, and publish surveys.</span></span>

> [!NOTE]
> <span data-ttu-id="eee39-110">Per iniziare a usare l'applicazione, vedere [Eseguire l'applicazione Surveys].</span><span class="sxs-lookup"><span data-stu-id="eee39-110">To get started with the application, see [Run the Surveys application].</span></span>
> 
> 

## <a name="users-can-create-edit-and-view-surveys"></a><span data-ttu-id="eee39-111">Gli utenti possono creare, modificare e visualizzare sondaggi</span><span class="sxs-lookup"><span data-stu-id="eee39-111">Users can create, edit, and view surveys</span></span>
<span data-ttu-id="eee39-112">Un utente autenticato può visualizzare tutti i sondaggi che ha creato o per cui ha diritti di collaboratore, nonché creare nuovi sondaggi.</span><span class="sxs-lookup"><span data-stu-id="eee39-112">An authenticated user can view all the surveys that he or she has created or has contributor rights to, and create new surveys.</span></span> <span data-ttu-id="eee39-113">Si noti che l'utente è connesso con la propria identità aziendale, `bob@contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="eee39-113">Notice that the user is signed in with his organizational identity, `bob@contoso.com`.</span></span>

![App Surveys](./images/surveys-screenshot.png)

<span data-ttu-id="eee39-115">Questa schermata mostra la pagina Edit Survey:</span><span class="sxs-lookup"><span data-stu-id="eee39-115">This screenshot shows the Edit Survey page:</span></span>

![Modifica del sondaggio](./images/edit-survey.png)

<span data-ttu-id="eee39-117">Gli utenti possono anche visualizzare i sondaggi creati da altri utenti all'interno dello stesso tenant.</span><span class="sxs-lookup"><span data-stu-id="eee39-117">Users can also view any surveys created by other users within the same tenant.</span></span>

![Sondaggi del tenant](./images/tenant-surveys.png)

## <a name="survey-owners-can-invite-contributors"></a><span data-ttu-id="eee39-119">I proprietari dei sondaggi possono invitare i collaboratori</span><span class="sxs-lookup"><span data-stu-id="eee39-119">Survey owners can invite contributors</span></span>
<span data-ttu-id="eee39-120">Quando crea un sondaggio, l'utente può invitare altre persone a collaborare nel sondaggio.</span><span class="sxs-lookup"><span data-stu-id="eee39-120">When a user creates a survey, he or she can invite other people to be contributors on the survey.</span></span> <span data-ttu-id="eee39-121">I collaboratori possono modificare il sondaggio, ma non possono eliminarlo o pubblicarlo.</span><span class="sxs-lookup"><span data-stu-id="eee39-121">Contributors can edit the survey, but cannot delete or publish it.</span></span>  

![Aggiungere un collaboratore](./images/add-contributor.png)

<span data-ttu-id="eee39-123">L'utente può aggiungere collaboratori di altri tenant consentendo in questo modo la condivisione di risorse tra tenant.</span><span class="sxs-lookup"><span data-stu-id="eee39-123">A user can add contributors from other tenants, which enables cross-tenant sharing of resources.</span></span> <span data-ttu-id="eee39-124">In questo screenshot Bob (`bob@contoso.com`) sta aggiungendo Alice (`alice@fabrikam.com`) come collaboratore a un sondaggio che ha creato lui stesso.</span><span class="sxs-lookup"><span data-stu-id="eee39-124">In this screenshot, Bob (`bob@contoso.com`) is adding Alice (`alice@fabrikam.com`) as a contributor to a survey that Bob created.</span></span>

<span data-ttu-id="eee39-125">Quando Alice esegue l'accesso, il sondaggio sarà visualizzato in "Surveys I can contribute to".</span><span class="sxs-lookup"><span data-stu-id="eee39-125">When Alice logs in, she sees the survey listed under "Surveys I can contribute to".</span></span>

![Collaboratore del sondaggio](./images/contributor.png)

<span data-ttu-id="eee39-127">Si noti che Alice accede al proprio tenant e non come guest al tenant Contoso.</span><span class="sxs-lookup"><span data-stu-id="eee39-127">Note that Alice signs into her own tenant, not as a guest of the Contoso tenant.</span></span> <span data-ttu-id="eee39-128">Alice ha autorizzazioni di collaboratore solo per questo sondaggio e non può visualizzare altri sondaggi dal tenant Contoso.</span><span class="sxs-lookup"><span data-stu-id="eee39-128">Alice has contributor permissions only for that survey &mdash; she cannot view other surveys from the Contoso tenant.</span></span>

## <a name="architecture"></a><span data-ttu-id="eee39-129">Architettura</span><span class="sxs-lookup"><span data-stu-id="eee39-129">Architecture</span></span>
<span data-ttu-id="eee39-130">L'applicazione Surveys è costituita da un'API Web di front-end e un'API Web di back-end.</span><span class="sxs-lookup"><span data-stu-id="eee39-130">The Surveys application consists of a web front end and a web API backend.</span></span> <span data-ttu-id="eee39-131">Entrambe le API vengono implementate usando [ASP.NET Core].</span><span class="sxs-lookup"><span data-stu-id="eee39-131">Both are implemented using [ASP.NET Core].</span></span>

<span data-ttu-id="eee39-132">L'applicazione Web usa Azure Active Directory (Azure AD) per l'autenticazione degli utenti.</span><span class="sxs-lookup"><span data-stu-id="eee39-132">The web application uses Azure Active Directory (Azure AD) to authenticate users.</span></span> <span data-ttu-id="eee39-133">L'applicazione Web chiama inoltre Azure AD per ottenere i token di accesso OAuth 2 per l'API Web.</span><span class="sxs-lookup"><span data-stu-id="eee39-133">The web application also calls Azure AD to get OAuth 2 access tokens for the Web API.</span></span> <span data-ttu-id="eee39-134">I token di accesso sono memorizzati nella cache in Cache Redis di Azure.</span><span class="sxs-lookup"><span data-stu-id="eee39-134">Access tokens are cached in Azure Redis Cache.</span></span> <span data-ttu-id="eee39-135">La cache consente a più istanze di condividere la stessa cache dei token (ad esempio, in una server farm).</span><span class="sxs-lookup"><span data-stu-id="eee39-135">The cache enables multiple instances to share the same token cache (e.g., in a server farm).</span></span>

![Architettura](./images/architecture.png)

<span data-ttu-id="eee39-137">[**Avanti**][authentication]</span><span class="sxs-lookup"><span data-stu-id="eee39-137">[**Next**][authentication]</span></span>

<!-- Links -->

[authentication]: authenticate.md

[Eseguire l'applicazione Surveys]: ./run-the-app.md
[Run the Surveys application]: ./run-the-app.md
[ASP.NET Core]: /aspnet/core
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
