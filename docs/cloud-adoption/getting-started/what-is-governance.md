---
title: 'CAF: Informazioni sulla governance delle risorse cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: Informazioni sulla governance delle risorse cloud in Azure
author: petertaylor9999
ms.openlocfilehash: 5d022e83a1c97a5e5af8208ec00339575bb88be1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242122"
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

Quando si configurano questi strumenti, è importante trovare il giusto equilibrio tra governance e flessibilità organizzativa. Più sono restrittivi i criteri di governance, meno flessibilità avranno gli sviluppatori e i professionisti IT. Criteri di governance restrittivi potrebbero infatti richiedere un maggior numero di passaggi manuali, ad esempio la compilazione di un modulo o l'invio di un messaggio di posta elettronica da uno sviluppatore a un membro del team di governance per la creazione manuale di una risorsa. Il team di governance ha capacità limitate e potrebbe accumulare lavoro arretrato, con una conseguente diminuzione della produttività dei team di sviluppo che devono attendere la creazione delle risorse e con il rischio di accumulare costi per risorse non necessarie in attesa di essere eliminate.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso il concetto di governance delle risorse cloud, si apprenderà come viene gestito l'accesso alle risorse in Azure.

> [!div class="nextstepaction"]
> [Informazioni sull'accesso alle risorse in Azure](azure-resource-access.md)
