---
title: 'Indicazioni: Struttura di un tenant di Azure AD'
description: Indicazioni per la progettazione di un tenant di Azure nell'ambito di una strategia di adozione del cloud di base
author: telmosampaio
ms.openlocfilehash: 9ac52e9fd44bd8b9c777625002d5960f4f269be2
ms.sourcegitcommit: 29fbcb1eec44802d2c01b6d3bcf7d7bd0bae65fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
ms.locfileid: "29563456"
---
# <a name="guidance-azure-ad-tenant-design"></a>Indicazioni: Struttura di un tenant di Azure AD

Un tenant di Azure AD fornisce servizi di gestione delle identità digitali e spazi dei nomi usati per una o più [sottoscrizioni di Azure](subscription-explainer.md). Se si seguono i passaggi della strategia di adozione di base, si è già appreso [come ottenere un tenant di Azure AD][how-to-get-aad-tenant]. 

## <a name="design-considerations"></a>Considerazioni sulla progettazione

- Durante la fase di adozione di base è possibile iniziare con un singolo tenant di Azure AD. Se la propria organizzazione ha già una sottoscrizione di Office 365 o di Azure, si avrà già a disposizione un tenant di Azure AD da usare. Se invece nessuna di queste due sottoscrizioni è disponibile, è possibile leggere altre informazioni su [come ottenere un tenant di Azure AD][how-to-get-aad-tenant]. 
- Nelle fasi di adozione intermedia e avanzata si apprenderà come eseguire la sincronizzazione o la federazione delle directory locali con Azure AD. In questo modo sarà possibile usare l'identità digitale locale in Azure AD. Durante la fase di base, tuttavia, si aggiungeranno nuovi utenti che hanno solo identità relative al singolo tenant di Azure AD e si dovranno gestire tali identità. Sarà ad esempio necessario caricare nuovi utenti di Azure AD, scaricare gli utenti di Azure AD che non vogliono più accedere alle risorse di Azure e apportare altre modifiche alle autorizzazioni degli utenti.

## <a name="next-steps"></a>Passaggi successivi

* Ora che è disponibile un tenant di Azure AD, si apprenderà [come aggiungere un utente][azure-ad-add-user]. Dopo l'aggiunta di uno o più nuovi utenti al tenant di Azure AD, il passaggio successivo sarà quello di apprendere i concetti relativi alle [sottoscrizioni di Azure](subscription-explainer.md).

<!-- Links -->

[azure-ad-add-user]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[docs-manage-azure-ad]: /azure/active-directory/active-directory-administer?toc=/azure/architecture/cloud-adoption-guide/toc.json
[docs-tenant]: /azure/active-directory/develop/active-directory-howto-tenant?toc=/azure/architecture/cloud-adoption-guide/toc.json
[docs-associate-subscription]: /azure/active-directory/active-directory-how-subscriptions-associated-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[how-to-get-aad-tenant]: /azure/active-directory/develop/active-directory-howto-tenant?toc=/azure/architecture/cloud-adoption-guide/toc.json
