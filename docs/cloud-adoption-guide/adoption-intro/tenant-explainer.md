---
title: 'Spiegazione: Informazioni su un tenant di Azure Active Directory'
description: Illustra il funzionamento interno di Azure Active Directory per fornire l'identità come servizio (IDaaS) in Azure
author: petertay
ms.openlocfilehash: ce5a33b92047e1f360eee8fcbc7a726bcf8cd19f
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
ms.locfileid: "29062039"
---
# <a name="explainer-what-is-an-azure-active-directory-tenant"></a>Spiegazione: Informazioni su un tenant di Azure Active Directory

Nell'articolo [Spiegazione: Funzionamento di Azure](azure-explainer.md) si è appreso che Azure è costituito da un insieme di server e componenti hardware di rete che eseguono software e hardware virtualizzato per conto degli utenti. Si è inoltre appreso che alcuni di questi server eseguono un'applicazione di orchestrazione distribuita per gestire la creazione, la lettura, l'aggiornamento e l'eliminazione delle risorse di Azure.

Azure, tuttavia, come si può immaginare, non consente a qualsiasi utente di eseguire una di queste operazioni su una risorsa, ma limita l'accesso a queste operazioni tramite un servizio di gestione delle identità digitali attendibili denominato **Azure Active Directory** (Azure AD). In Azure AD sono archiviati il nome utente, le password, i dati del profilo e altre informazioni. Gli utenti di Azure AD sono segmentati in **tenant**. Un tenant è un costrutto logico che rappresenta un'istanza sicura e dedicata di Azure AD, generalmente associata a un'organizzazione.

Per creare un tenant, Azure richiede un **account con privilegi**. Quest'ultimo è associato a un account di Azure o a un contratto Enterprise Agreement, che sono entrambi costrutti di fatturazione e non sono archiviati in Azure AD, ma in un database di fatturazione con sicurezza elevata. 

Dopo che è stato creato il tenant, l'**ID tenant** corrispondente viene generato e salvato in un database di Azure AD interno altamente sicuro. Il proprietario di un account con privilegi può quindi accedere a un portale di Azure e aggiungere utenti al tenant di Azure AD appena creato. 

La maggior parte delle aziende dispone già di almeno un servizio di gestione delle identità, in genere Active Directory Domain Services (AD DS). Azure AD è in grado di eseguire la sincronizzazione o la federazione delle identità utente da AD DS. In questo modo, le aziende non devono gestire le identità separatamente nei due ambienti. Questo concetto verrà illustrato in modo più dettagliato negli articoli relativi alle fasi di adozione intermedia e avanzata per l'identità digitale.

## <a name="next-steps"></a>Passaggi successivi

* Ora che si sono acquisite le nozioni fondamentali sui tenant di Azure AD, il primo passaggio della fase di adozione di base è quello di apprendere [come ottenere un tenant di Azure Active Directory][how-to-get-aad-tenant] e quindi rivedere le [indicazioni sulla struttura dei tenant di Azure AD](tenant.md).

<!-- Links -->
[how-to-get-aad-tenant]: /azure/active-directory/develop/active-directory-howto-tenant?toc=/azure/architecture/cloud-adoption-guide/toc.json