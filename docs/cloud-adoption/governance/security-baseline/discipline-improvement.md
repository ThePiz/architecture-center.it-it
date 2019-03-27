---
title: 'CAF: Miglioramento della disciplina Baseline di sicurezza'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Miglioramento della disciplina Baseline di sicurezza
author: BrianBlanchard
ms.openlocfilehash: 28a971f56c9f8ada1d184bdc1cb3dbb9a17c3507
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246452"
---
# <a name="security-baseline-discipline-improvement"></a>Miglioramento della disciplina Baseline di sicurezza

La disciplina Baseline di sicurezza è incentrata sulle modalità di definizione dei criteri per proteggere la rete, le risorse e soprattutto i dati presenti in una soluzione del provider di servizi cloud. All'interno delle cinque discipline della governance del cloud, Baseline di sicurezza include la classificazione del patrimonio digitale e dei dati. Include inoltre la documentazione relativa a rischi, tolleranza aziendale e strategie di attenuazione associate alla sicurezza dei dati, risorse e rete. Dal punto di vista tecnico, ciò include anche il coinvolgimento nelle decisioni relative a [crittografia](../../decision-guides/encryption/overview.md), [requisiti di rete](../../decision-guides/software-defined-network/overview.md), [strategie di identità ibride](../../decision-guides/identity/overview.md) e [processi](compliance-processes.md) usati per lo sviluppo di criteri cloud in Baseline di sicurezza.

Questo articolo delinea alcuni compiti potenziali che l'azienda può svolgere per sviluppare e maturare al meglio la disciplina Baseline di sicurezza. Queste attività possono essere suddivise nelle fasi di pianificazione, costruzione, adozione e funzionamento dell'implementazione di una soluzione cloud, che vengono poi ripetute per consentire lo sviluppo di un [approccio incrementale alla governance del cloud](../journeys/overview.md#an-incremental-approach-to-cloud-governance).

![Quattro fasi di adozione](../../_images/adoption-phases.png)

*Figura 1. Fasi di adozione dell'approccio incrementale alla governance del cloud.*

È impossibile tener conto per qualsiasi documento dei requisiti di tutte le aziende. Di conseguenza, questo articolo delinea le attività di esempio minime e potenziali suggerite per ogni fase del processo di maturazione della governance. L'obiettivo iniziale di queste attività è quello di permettere di costruire un [MVP dei criteri](../journeys/overview.md#an-incremental-approach-to-cloud-governance) e stabilire un framework per l'evoluzione incrementale dei criteri. Il team di governance del cloud dovrà decidere quanto investire in queste attività per migliorare le capacità di governance della Baseline di sicurezza.

> [!CAUTION]
> Nessuna delle attività minime o potenziali descritte in questo articolo è allineata a specifici criteri aziendali o requisiti di conformità di terze parti. Queste linee guida sono state progettate per facilitare le conversazioni che porteranno all'allineamento di entrambi i requisiti con un modello di governance del cloud.

## <a name="planning-and-readiness"></a>Pianificazione e preparazione

Questa fase di maturità della governance colma il divario tra i risultati aziendali e le strategie attuabili. Durante questo processo, il team di leadership definisce metriche specifiche, associa tali metriche al patrimonio digitale e inizia a pianificare l'impegno complessivo richiesto per la migrazione.

**Attività minime suggerite:**

- Valutare le opzioni della [toolchain della Baseline di sicurezza](toolchain.md).
- Sviluppare una bozza di documento relativo alle linee guida sull'architettura e distribuirlo ai principali stakeholder.
- Educare e coinvolgere le persone e i team interessati dallo sviluppo delle linee guida sull'architettura.
- Aggiungere attività di sicurezza con priorità al backlog di migrazione.

**Attività potenziali:**

- Definire uno schema di classificazione dei dati.
- Condurre un processo di pianificazione del patrimonio digitale per creare un inventario delle attuali risorse IT che gestiscono i processi aziendali e le operazioni di supporto.
- Condurre una [verifica dei criteri](../../governance/policy-compliance/what-is-a-cloud-policy-review.md) per iniziare il processo di modernizzazione dei criteri di sicurezza IT aziendale esistenti e per definire i criteri MVP per affrontare i rischi noti.
- Verificare le linee guida sulla sicurezza della piattaforma cloud. Per Azure sono disponibili in [Microsoft Service Trust Platform](https://www.microsoft.com/trustcenter/stp/default.aspx).
- Determinare se i criteri della Baseline di sicurezza includono un [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/).
- Valutare rete, dati e rischi correlati aziendali alle risorse, in base alle prossime tre versioni e valutare la tolleranza dell'organizzazione per tali rischi.
- Esaminare il report di Microsoft sulle [principali tendenze per la sicurezza informatica](https://www.microsoft.com/security/operations/security-intelligence-report) per ottenere una panoramica dello scenario di sicurezza corrente.
- Si consiglia di sviluppare un ruolo di [Security DevOps](https://www.microsoft.com/en-us/securityengineering/devsecops) (sicurezza DevOps) all'interno dell'organizzazione.

<!-- "en-us" location is required for the URL above. -->

## <a name="build-and-pre-deployment"></a>Compilazione e pre-distribuzione

Sono necessari una serie di prerequisiti tecnici e non per eseguire la migrazione di un ambiente con esito positivo. Questo processo è incentrato sulle decisioni, sulla conformità e sull'infrastruttura di base che consente una migrazione.

**Attività minime suggerite:**

- Implementare la [toolchain della Baseline di sicurezza](toolchain.md) distribuendo in una fase di pre-distribuzione.
- Aggiornare il documento relativo alle linee guida sull'architettura e distribuirlo ai principali stakeholder.
- Implementare le attività relative alla sicurezza nel backlog di migrazione con priorità.
- Sviluppare materiali didattici e documentazione, comunicazioni di sensibilizzazione, incentivi e altri programmi per guidare l'adozione da parte degli utenti.

**Attività potenziali:**

- Determinare la strategia di[crittografia](../../decision-guides/encryption/overview.md) dell'organizzazione per i dati ospitati nel cloud.
- Valutare la strategia [identità](../../decision-guides/identity/overview.md) di distribuzione nel cloud. Determinare come la soluzione di identità basata sul cloud potrà coesistere o venire integrata con i provider di identità locali.
- Determinare i criteri sui limiti di rete per la progettazione delle [Software Defined Networking (SDN)](../../decision-guides/software-defined-network/overview.md) (reti primarie definite dal software) per garantire funzionalità di rete virtualizzata sicure.
- Valutare i criteri di [accesso con privilegi minimi](/azure/active-directory/users-groups-roles/roles-delegate-by-task) dell'organizzazione e usare i ruoli basati sull'attività per fornire l'accesso a risorse specifiche.
- Applicare meccanismi di sicurezza e monitoraggio a tutti i servizi cloud e macchine virtuali.
- Automatizzare i [criteri di sicurezza](../../decision-guides/policy-enforcement/overview.md) laddove possibile.
- Rivedere i criteri di Baseline di sicurezza e determinare se è necessario modificare i piani in base alle indicazioni delle procedure consigliate, ad esempio quelle indicate nella [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/).

## <a name="adopt-and-migrate"></a>Adottare ed eseguire la migrazione

La migrazione è un processo incrementale incentrato sullo spostamento, il testing e l'adozione di applicazioni o carichi di lavoro in un patrimonio digitale esistente.

**Attività minime suggerite:**

- Eseguire la migrazione del [toolchain della Baseline di sicurezza](toolchain.md) dall'ambiente di pre-distribuzione all'ambiente di produzione.
- Aggiornare il documento relativo alle linee guida sull'architettura e distribuirlo ai principali stakeholder.
- Sviluppare materiali didattici e documentazione, comunicazioni di sensibilizzazione, incentivi e altri programmi per guidare l'adozione da parte degli utenti

**Attività potenziali:**

- Rivedere le informazioni più recenti relative a Baseline di sicurezza e alle minacce per identificare eventuali nuovi rischi aziendali.
- Misurare la tolleranza della propria organizzazione per gestire nuovi rischi per la sicurezza che potrebbero verificarsi.
- Identificare le deviazioni dai criteri e applicare le correzioni.
- Rettificare la sicurezza e l'automazione del controllo di accesso per garantire la massima conformità ai criteri.  
- Verificare che le procedure consigliate definite durante la compilazione o le fasi di pre-distribuzione vengano eseguite correttamente.
- Verificare i criteri di accesso con privilegi minimi e modificare i controlli di accesso per ottimizzare la sicurezza.
- Testare la toolchain della Baseline di sicurezza contro i carichi di lavoro per identificare e risolvere eventuali vulnerabilità.

## <a name="operate-and-post-implementation"></a>Operazioni e post-implementazione

Al termine della trasformazione, la governance e le operazioni devono continuare a funzionare per il ciclo di vita naturale di un'applicazione o di un carico di lavoro. Questa fase di maturità della governance si basa principalmente sulle attività che vengono svolte in genere dopo che la soluzione è stata implementata e il ciclo di trasformazione ha iniziato a stabilizzarsi.

**Attività minime suggerite:**

- Convalidare e/o perfezionare la [toolchain della Baseline di sicurezza](toolchain.md).
- Personalizzare notifiche e report per inviare un avviso relativo a potenziali problemi di sicurezza.
- Perfezionare le linee guida sull'architettura per guidare i processi di adozione futura.
- Comunicare e informare periodicamente i team interessati per verificare la conformità costante alle indicazioni sull'architettura.

**Attività potenziali:**

- Individuare i motivi e il comportamento per i carichi di lavoro e configurare il monitoraggio e gli strumenti di report per identificare e ricevere una notifica su qualsiasi attività anomala,accesso o uso delle risorse.
- Aggiornare continuamente i criteri di report e monitoraggio per rilevare le vulnerabilità più recenti, gli attacchi e le minacce.
- Predisporre procedure per arrestare velocemente gli accessi non autorizzati e disabilitare le risorse che potrebbero essere state compromesse da un utente malintenzionato.
- Revisionare regolarmente le più recenti procedure consigliate per la sicurezza e applicare gli elementi consigliati ai criteri di sicurezza, all'automazione e alle funzionalità di monitoraggio, laddove possibile.

## <a name="next-steps"></a>Passaggi successivi

Una volta appreso il concetto di governance della sicurezza nel cloud, per altre informazioni vedere l'articolo relativo alle [procedure consigliate fornite da Microsoft](azure-security-guidance.md) per Azure (in inglese).

> [!div class="nextstepaction"]
> [Learn about security guidance for Azure](azure-security-guidance.md)
> (Informazioni sulle indicazioni per la sicurezza in Azure)[Introduzione alla sicurezza in Azure](/azure/security/azure-security)
> [Learn about logging, reporting, and monitoring](../../decision-guides/log-and-report/overview.md) (Informazioni sulla registrazione, il reporting e il monitoraggio)
