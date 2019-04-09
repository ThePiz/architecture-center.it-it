---
title: 'CAF: Informazioni sulla governance delle risorse cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: Informazioni sulla governance delle risorse cloud in Azure
author: petertaylor9999
ms.openlocfilehash: 0989a5aad8a6290cce07fd71771c690bbd615e0d
ms.sourcegitcommit: 0a8a60d782facc294f7f78ec0e9033e3ee16bf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068855"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-cloud-resource-governance"></a>Informazioni sulla governance delle risorse cloud

Nelle [come funziona Azure?](what-is-azure.md), si è appreso che Azure è una raccolta di server e hardware che eseguono software e hardware virtualizzato per conto degli utenti di rete. Azure consente lo sviluppo di applicazioni e ai reparti IT di offrire una flessibilità rendendo più semplice creare, leggere, aggiornare ed eliminare le risorse in base alle esigenze dell'organizzazione.

Tuttavia, durante l'accesso illimitato alle risorse possa rendere gli sviluppatori molto agile, può inoltre causare costi imprevisti. Un team di sviluppo, ad esempio, potrebbe avere l'approvazione per distribuire un set di risorse per i test, ma potrebbe dimenticarsi di eliminare tali risorse al termine della fase di test. Queste risorse continueranno ad accumularsi dei costi, anche se non sono più necessarie o approvato.

La soluzione è governance delle risorse di accesso. **Governance** è il processo in corso di gestione, monitoraggio e la registrazione dell'utilizzo delle risorse di Azure per soddisfare i requisiti dell'organizzazione.

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

Questi requisiti sono univoci per ogni organizzazione, pertanto un approccio unico per la governance non è utile. Al contrario, spetta a ogni organizzazione per progettare il modello di governance utilizzando due strumenti di governance principale di Azure: **accesso basato sui ruoli controllo degli accessi** e **criteri delle risorse**.

RBAC definisce i ruoli e i ruoli definiscono le funzionalità di ogni utente assegnato quel ruolo. Ad esempio, il **owner** ruolo consente tutte le funzionalità (creare, leggere, aggiornare ed eliminare) per una risorsa, mentre il **lettore** ruolo consente solo la capacità di lettura. I ruoli possono essere definiti con un ampio ambito applicabile a molti tipi di risorsa o un ristretto ambito che si applica a poche.

I criteri delle risorse definiscono le regole per la creazione delle risorse. Un criterio di risorsa, ad esempio, possibile limitare lo SKU di una macchina virtuale per una determinata dimensione preapprovata. Un altro criterio di risorse potrebbe imporre l'applicazione di un tag per un centro di costo assegnato quando la richiesta viene effettuata per creare la risorsa.

Durante la configurazione di questi strumenti, è importante bilanciare la governance e l'agilità aziendale. Le più restrittive criteri governance, i meno agile agli sviluppatori e professionisti IT saranno. Un criterio restrittivo governance può richiedere più passaggi manuali, ad esempio uno sviluppatore deve compilare un modulo o inviare un messaggio di posta elettronica a un membro del team di governance per creare manualmente una risorsa. Il team di governance ha la capacità limitata e potrebbe diventare un collo di bottiglia, causando i team di sviluppo indotto attendere le relative risorse essere create o non necessarie risorse accumulo dei costi prima che vengano eliminati.

## <a name="next-steps"></a>Passaggi successivi

Ora che abbiamo appreso il concetto di governance delle risorse cloud, altre informazioni su come viene gestito l'accesso alle risorse in Azure.

> [!div class="nextstepaction"]
> [Informazioni sull'accesso alle risorse in Azure](azure-resource-access.md)
