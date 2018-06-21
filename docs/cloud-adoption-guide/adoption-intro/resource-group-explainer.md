---
title: 'Spiegazione: Informazioni sul gruppo di risorse di Azure'
description: Questo articolo illustra la funzione interna di Azure relativa al gruppo di risorse
author: petertay
ms.openlocfilehash: e7c7334bd88c28f57498486bd2bed3c349565222
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
ms.locfileid: "29062079"
---
# <a name="what-is-an-azure-resource-group"></a>Informazioni sul gruppo di risorse di Azure

Nell'articolo [Spiegazione: Informazioni su Azure Resource Manager](resource-manager-explainer.md) si è appreso che Azure Resource Manager richiede un **identificatore del gruppo di risorse** quando viene effettuata una chiamata per creare, leggere, aggiornare o eliminare una risorsa. Questo ID del gruppo di risorse fa riferimento a un **gruppo di risorse**. Un gruppo di risorse è semplicemente un identificatore che Azure Resource Manager applica alle risorse per raggrupparle. L'ID del gruppo di risorse consente ad Azure Resource Manager di eseguire operazioni su un gruppo di risorse che condividono lo stesso ID.

Ad esempio, un utente può eseguire una chiamata **delete** a un'API RESTful di Azure Resource Manager specificando l'ID del gruppo di risorse senza includere un ID di risorsa specifico. Azure Resource Manager esegue in un database interno di Azure una query relativa a tutte le risorse con l'ID del gruppo di risorse specificato e chiama l'API RESTful per eliminare le risorse.

Un gruppo di risorse non può includere risorse da sottoscrizioni diverse, in quanto è presente una relazione uno-a-molti tra l'ID tenant e l'ID sottoscrizione &mdash; più sottoscrizioni possono considerare attendibile lo stesso tenant per fornire l'autenticazione e l'autorizzazione, ma ogni sottoscrizione può considerare attendibile un solo tenant. È inoltre presente una relazione uno-a-molti tra l'ID sottoscrizione e l'ID del gruppo di risorse &mdash; più gruppi di risorse possono appartenere alla stessa sottoscrizione, ma ogni gruppo di risorse può appartenere a una sola sottoscrizione. Infine, è presente una relazione uno-a-molti tra l'ID del gruppo di risorse e l'ID della risorsa &mdash; un singolo gruppo di risorse può avere più risorse, ma ogni risorsa può appartenere solo a un singolo gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

* A questo punto, dopo aver appreso informazioni importanti sui gruppi di risorse di Azure, è opportuno acquisire una conoscenza di base [sulle restrizioni di accesso alle risorse](/azure/active-directory/active-directory-understanding-resource-access?toc=/azure/architecture/cloud-adoption-guide/toc.json). Anche se non fa parte della fase di adozione di base, è importante durante la fase di adozione intermedia. Sarà quindi possibile [creare il primo gruppo di risorse](/azure/azure-resource-manager/resource-group-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json) ed esaminare le [linee guida per la progettazione dei gruppi di risorse di Azure](resource-group.md).
