---
title: 'Spiegazione: Informazioni sulla sottoscrizione di Azure'
description: Spiega le sottoscrizioni, gli account e le offerte di Azure
author: alexbuckgit
ms.openlocfilehash: 1650d90d6f78b46b7fe4128d2dab6a80bd6cca78
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="explainer-what-is-an-azure-subscription"></a>Spiegazione: Informazioni sulla sottoscrizione di Azure

Nell'articolo [Spiegazione: Informazioni su un tenant di Azure Active Directory](tenant-explainer.md) si è appreso che l'identità digitale dell'organizzazione viene archiviata in un tenant di Azure Active Directory. Si è inoltre appreso che Azure si affida ad Azure Active Directory per autenticare le richieste utente allo scopo di creare, leggere, aggiornare o eliminare una risorsa. 

Fondamentalmente, si comprende il motivo per cui è necessario limitare l'accesso a queste operazioni su una risorsa, ovvero per impedire che utenti non autenticati e non autorizzati accedano alla risorsa. Queste operazioni sulle risorse, tuttavia, hanno anche altre proprietà che un'organizzazione vorrebbe controllare, come il numero di risorse che un utente o un gruppo di utenti può creare, oltre al costo di esecuzione delle risorse. 

Azure implementa questo controllo, che prende il nome di **sottoscrizione**. In una sottoscrizione vengono raggruppati gli utenti e le risorse create dagli utenti. Ognuna di queste risorse contribuisce a un [limite complessivo][subscription-service-limits] su una determinata risorsa.

Le organizzazioni possono usare le sottoscrizioni per gestire i costi e la creazione di risorse in base a utenti, team e progetti o adottando molte altre strategie. Queste strategie verranno descritte negli articoli relativi alle fasi di adozione intermedia e avanzata. 

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver appreso le nozioni di base relative alle sottoscrizioni di Azure, è ora necessario acquisire informazioni sulla [creazione di una sottoscrizione](subscription.md) prima di creare le prime risorse di Azure.

<!-- Links -->
[azure-get-started]: https://azure.microsoft.com/get-started/
[azure-offers]: https://azure.microsoft.com/support/legal/offer-details/
[azure-free-trial]: https://azure.microsoft.com/offers/ms-azr-0044p/
[azure-change-subscription-offer]: /azure/billing/billing-how-to-switch-azure-offer
[microsoft-account]: https://account.microsoft.com/account
[subscription-service-limits]: /azure/azure-subscription-service-limits
[docs-organizational-account]: https://docs.microsoft.com/azure/active-directory/sign-up-organization
