---
title: 'CAF: Governance in CAF di Microsoft per Azure'
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Governance in CAF di Microsoft per Azure
author: BrianBlanchard
ms.openlocfilehash: ce407de0daa590e767382346692c80e0a113bb3c
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068838"
---
# <a name="governance-in-the-microsoft-caf-for-azure"></a>Governance in CAF di Microsoft per Azure

Il cloud crea nuovi paradigmi riguardanti le tecnologie che supportano l'azienda. Questi nuovi paradigmi comportano anche cambiamenti nel modo in cui le tecnologie vengono adottate, gestite e regolate. Quando interi data center possono essere eliminati e ricreati con una sola riga di codice eseguito da un processo automatico, è necessario rivedere gli approcci tradizionali. Questo è vero anche in riferimento alla governance.

Per le organizzazioni in cui sono già implementati criteri per il controllo degli ambienti locali, è necessario che vengano integrati con la governance del cloud. Il livello di integrazione dei criteri aziendali tra origini locali e cloud può tuttavia variare in base alla maturità della governance del cloud e al patrimonio digitale nel cloud. Parallelamente all'evoluzione delle risorse del cloud, si avrà anche un'evoluzione dei criteri e dei processi di governance del cloud.

Le linee guida fornite in questa sezione di Cloud Adoption Framework sono progettate per due scopi:

* Illustrare ai clienti percorsi operativi che rappresentano alcune delle loro esperienze comuni. Ognuno di questi percorsi include un'analisi dei rischi aziendali, criteri aziendali per la mitigazione dei rischi e linee guida di progettazione per implementare soluzioni tecniche. Le linee guida di progettazione sono ovviamente specifiche per Azure. Tutte le altre linee guida fornite in questi percorsi possono essere applicate come parte di un approccio indipendente dal cloud o multi-cloud.
* Aiutare i lettori a creare soluzioni di governance personalizzate in grado di soddisfare numerose esigenze aziendali, tra cui la governance di più cloud pubblici, tramite linee guida dettagliate sullo sviluppo di criteri, processi e strumenti aziendali.

Questo contenuto è destinato al team di governance del cloud, ma è utile anche per i Cloud Architect che devono sviluppare una base solida di governance del cloud.

## <a name="audience"></a>Audience

Il contenuto di Cloud Adoption Framework interessa i reparti aziendali impegnati nelle attività di carattere commerciale, tecnologico e culturale. Questa sezione di Cloud Adoption Framework interagisce in maniera significativa con il personale responsabile della sicurezza IT, della governance IT, delle attività amministrative, della gestione della rete e delle identità, con i responsabili line-of-business e con i team di adozione del cloud. Esistono varie co-dipendenze in queste figure professionali che richiederanno un approccio collaborativo da parte dei Cloud Architect nell'uso di queste linee guida. La collaborazione con questi team può essere un'attività una tantum, ma in alcuni casi può richiedere interazioni ricorrenti con queste altre figure professionali.

Il Cloud Architect assume il ruolo di leader e mediatore per semplificare l'interazione tra le varie persone coinvolte. I contenuti di questa raccolta di guide sono stati progettati per consentire ai Cloud Architect di comunicare informazioni corrette alle persone appropriate e giungere così alle decisioni opportune. La trasformazione aziendale introdotta dal cloud dipende dalla capacità del Cloud Architect di aiutare il personale tecnico e commerciale a prendere decisioni appropriate.

**Specializzazione del Cloud Architect in questa sezione:** ogni sezione di Cloud Adoption Framework rappresenta una diversa variante o specializzazione del ruolo Cloud Architect. In questa sezione della finestra di CAF è progettata per gli architetti di cloud con una passione per la riduzione o alla riduzione dei rischi tecnici. Molti provider di servizi cloud si riferiscono a questi esperti con l'appellativo di Cloud Custodian. In questa guida, si preferisce chiamarli Cloud Guardian o, collettivamente, team di governance del cloud. In ogni percorso operativo per i clienti, gli articoli illustrano in che modo la composizione e il ruolo del team di governance del cloud può cambiare nel tempo.

## <a name="using-this-guide"></a>Uso della guida

Per chi vuole seguire questa guida dall'inizio alla fine, il contenuto presentato consentirà di sviluppare una solida strategia di governance del cloud in parallelo all'implementazione del cloud. Le linee guida accompagneranno il lettore alla scoperta della teoria e dell'implementazione di tale strategia.

Per un corso di arresto anomalo del sistema in teoria e all'accesso rapido all'implementazione di Azure, iniziare con il [panoramica dei percorsi di utilità pratica governance](./journeys/overview.md). Con queste linee guida, il lettore può iniziare dalle attività fondamentali e sviluppare le proprie esigenze di governance in parallelo con le attività di adozione del cloud.

## <a name="next-steps"></a>Passaggi successivi

Esaminare i percorsi di governance di utilità pratica.

> [!div class="nextstepaction"]
> [Actionable Governance Journeys (Percorsi di governance di utilità pratica)](./journeys/overview.md)
