---
title: 'CAF: Informazioni sulla governance delle risorse cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: Informazioni sulla governance delle risorse cloud in Azure
author: petertaylor9999
ms.openlocfilehash: 602ac81f2b2201c77746df971d282582ceee23f7
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503197"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-cloud-resource-governance"></a>Informazioni sulla governance delle risorse cloud

Nell'articolo [Funzionamento di Azure](what-is-azure.md) si è appreso che Azure è un insieme di server e componenti hardware di rete che eseguono software e hardware virtualizzati per conto degli utenti. Azure consente ai reparti che si occupano di IT e sviluppo dell'organizzazione di lavorare in modo flessibile, creando, leggendo, aggiornando ed eliminando facilmente le risorse in base alle esigenze.

Tuttavia, sebbene offrire agli sviluppatori un accesso illimitato alle risorse garantisca loro ampia flessibilità, ciò può anche comportare costi imprevisti. Un team di sviluppo, ad esempio, potrebbe avere l'approvazione per distribuire un set di risorse per i test, ma potrebbe dimenticarsi di eliminare tali risorse al termine della fase di test. Queste risorse continueranno ad accumulare costi anche se il loro uso non è più approvato o necessario.

La soluzione a questo problema è la **governance** dell'accesso alle risorse. Per governance si intende il processo continuativo di gestione, monitoraggio e controllo dell'uso delle risorse di Azure per soddisfare gli obiettivi e i requisiti dell'organizzazione.

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

Questi obiettivi e requisiti sono univoci per ogni organizzazione, quindi non è possibile definire un approccio alla governance appropriato per tutti. Azure implementa due strumenti di governance principali, il **controllo degli accessi in base al ruolo** e i **criteri delle risorse**, che ogni organizzazione può usare per progettare un modello di governance personalizzato.

Il controllo degli accessi in base al ruolo definisce i ruoli e i ruoli definiscono le funzionalità per un utente a cui viene assegnato il ruolo. Il ruolo **proprietario**, ad esempio, consente tutte le funzionalità (creazione, lettura, aggiornamento ed eliminazione) per una risorsa, mentre il ruolo **lettore** consente solo la capacità di lettura. I ruoli possono essere definiti con un ambito ampio che si applica a molti tipi di risorse oppure un ambito ristretto che si applica a pochi tipi.

I criteri delle risorse definiscono le regole per la creazione delle risorse. Un criterio delle risorse può ad esempio limitare lo SKU di una macchina virtuale a una dimensione specifica pre-approvata. Un altro criterio delle risorse può imporre l'aggiunta di un tag con un centro di costo quando viene effettuata la richiesta di creazione della risorsa.

Quando si configurano questi strumenti, è importante trovare il giusto equilibrio tra governance e flessibilità organizzativa. Più sono restrittivi i criteri di governance, meno flessibilità avranno gli sviluppatori e i professionisti IT. Questo avviene perché un criterio restrittivo governance può richiedere più passaggi manuali, ad esempio richiedere agli sviluppatori di compilare un modulo o inviare un messaggio di posta elettronica a una persona del team di governance per creare manualmente una risorsa. Il team di governance ha funzionalità finita e potrebbe essere inclusi nel backlog, causando i team di sviluppo produttivo in attesa di essere create e non necessarie risorse provenienti i costi durante l'attesa da eliminare le relative risorse.

## <a name="next-steps"></a>Passaggi successivi

Ora che abbiamo appreso il concetto di governance delle risorse cloud, altre informazioni su come viene gestito l'accesso alle risorse in Azure.

> [!div class="nextstepaction"]
> [Informazioni sull'accesso alle risorse in Azure](azure-resource-access.md)
