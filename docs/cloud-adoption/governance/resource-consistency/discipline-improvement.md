---
title: 'CAF: Miglioramento della disciplina Coerenza delle risorse'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Miglioramento della disciplina Coerenza delle risorse
author: BrianBlanchard
ms.openlocfilehash: bc81b894d46266c52291c53dba5532ab2ab6b860
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241572"
---
# <a name="resource-consistency-discipline-improvement"></a>Miglioramento della disciplina Coerenza delle risorse

La disciplina Coerenza delle risorse riguarda le modalità di definizione dei criteri di gestione operativa di un ambiente, applicazione o carico di lavoro. Tra le cinque discipline della governance del cloud, la coerenza delle risorse include il monitoraggio dell'applicazione, del carico di lavoro e delle prestazioni degli asset. Comprende inoltre le attività necessarie per soddisfare le esigenze di scalabilità e correggere e prevenire dinamicamente le violazioni dei contratti di servizio tramite una procedura di correzione automatizzata.

Questo articolo delinea alcune attività potenziali che l'azienda può svolgere al fine di sviluppare ed evolvere al meglio la disciplina Coerenza delle risorse. Queste attività possono essere suddivise nelle fasi di pianificazione, costruzione, adozione e funzionamento dell'implementazione di una soluzione cloud, che vengono poi ripetute per consentire lo sviluppo di un [approccio incrementale alla governance del cloud](../journeys/overview.md#an-incremental-approach-to-cloud-governance).

![Quattro fasi di adozione](../../_images/adoption-phases.png)

*Figura 1. Fasi di adozione dell'approccio incrementale alla governance del cloud.*

È impossibile tener conto per qualsiasi documento dei requisiti di tutte le aziende. Di conseguenza, questo articolo delinea le attività di esempio minime e potenziali suggerite per ogni fase del processo di maturazione della governance. L'obiettivo iniziale di queste attività è quello di permettere di costruire un [MVP dei criteri](../journeys/overview.md#an-incremental-approach-to-cloud-governance) e stabilire un framework per l'evoluzione incrementale dei criteri di Azure. Il team di governance del cloud dovrà decidere quanto investire in queste attività per migliorare le capacità di governance della coerenza delle risorse.

> [!CAUTION]
> Nessuna delle attività minime o potenziali descritte in questo articolo è allineata a specifici criteri aziendali o requisiti di conformità di terzi. Queste linee guida sono state progettate per facilitare le conversazioni che porteranno all'allineamento di entrambi i requisiti con un modello di governance del cloud.

## <a name="planning-and-readiness"></a>Pianificazione e preparazione

Questa fase di maturità della governance colma il divario tra i risultati aziendali e le strategie attuabili. Durante questo processo, il team di leadership definisce metriche specifiche, associa tali metriche al patrimonio digitale e inizia a pianificare l'impegno complessivo richiesto per la migrazione.

**Attività minime suggerite:**

* Valutare le opzioni della [toolchain di Coerenza delle risorse](toolchain.md).
* Comprendere i requisiti di licenza per la strategia cloud.
* Sviluppare una bozza di linee guida sull'architettura e distribuirla ai principali stakeholder.
* Acquisire familiarità con la gestione delle risorse usata per distribuire, gestire e monitorare tutte le risorse per la soluzione come gruppo.
* Formare e coinvolgere le persone e i team interessati allo sviluppo delle linee guida sull'architettura.
* Aggiungere attività di distribuzione delle risorse con priorità al backlog di migrazione.

**Attività potenziali:**

* Lavorare con gli stakeholder aziendali e/o il team di strategia cloud per comprendere l'approccio di contabilità cloud e le procedure di contabilità desiderati all'interno della business unit e dell'organizzazione nel suo complesso.
* Definire i requisiti di [applicazione dei criteri e di monitoraggio](compliance-processes.md).
* Esaminare il valore di business e il costo dovuto alle interruzioni per definire i criteri di correzione e i requisiti del contratto di servizio.
* Determinare se verrà distribuito una strategia di governance per le risorse con [carico di lavoro semplice](./governance-simple-workload.md) oppure [multi-team](./governance-multiple-teams.md).
* Determinare i requisiti di scalabilità per carichi di lavoro pianificati.

## <a name="build-and-pre-deployment"></a>Compilazione e pre-distribuzione

Sono necessari una serie di prerequisiti tecnici e non per eseguire la migrazione di un ambiente con esito positivo. Questo processo è incentrato sulle decisioni, sulla conformità e sull'infrastruttura di base che consente una migrazione.

**Attività minime suggerite:**

* Implementare la [toolchain della Coerenza delle risorse](toolchain.md) in una fase di pre-distribuzione.
* Aggiornare il documento relativo alle linee guida sull'architettura e distribuirlo ai principali stakeholder.
* Implementare le attività relative alla distribuzione delle risorse nel backlog di migrazione con priorità.
* Sviluppare materiali didattici e documentazione, comunicazioni di sensibilizzazione, incentivi e altri programmi per guidare l'adozione da parte degli utenti.

**Attività potenziali:**

* Scegliere una [strategia di progettazione della sottoscrizione](../../decision-guides/subscriptions/overview.md), scegliere i criteri per la sottoscrizione che meglio soddisfano le esigenze dell'organizzazione e del carico di lavoro.
* Usare la strategia [della coerenza delle risorse](../../decision-guides/resource-consistency/overview.md) per applicare le linee guida per l'architettura nel tempo.
* Implementare la [denominazione delle risorse e l'assegnazione di tag standard](../../decision-guides/resource-tagging/overview.md) perché le risorse corrispondano ai requisiti organizzativi e contabili.
* Per creare governance proattive temporizzate usare i modelli e l'automazione della distribuzione per applicare le configurazioni comuni e una struttura di raggruppamento coerente durante la distribuzione di risorse e gruppi di risorse.
* Stabilire almeno un modello di autorizzazione con privilegi minimi in cui gli utenti non hanno autorizzazioni predefinite.
* Determinare quali utenti dell'organizzazione sono proprietari di ogni carico di lavoro e di ogni account e chi dovrà accedere per gestire o modificare queste risorse. Definire i ruoli e le responsabilità del cloud che corrispondono a queste esigenze e usano questi ruoli come base per il controllo di accesso.
* Definire le dipendenze tra le risorse.
* Implementare il ridimensionamento automatico delle risorse per osservare i requisiti definiti in fase di pianificazione.
* Ottenere le prestazioni di accesso per misurare la qualità dei servizi ricevuti.
* È consigliabile distribuire [criteri](/azure/governance/policy/overview) per gestire l'applicazione del contratto di servizio usando le impostazioni di configurazione e regole per la creazione di risorse.

## <a name="adopt-and-migrate"></a>Adottare ed eseguire la migrazione

La migrazione è un processo incrementale incentrato sullo spostamento, il testing e l'adozione di applicazioni o carichi di lavoro in un patrimonio digitale esistente.

**Attività minime suggerite:**

* Eseguire la migrazione della [toolchain della Coerenza delle risorse](toolchain.md) dall'ambiente di pre-distribuzione a quello di produzione.
* Aggiornare il documento relativo alle linee guida sull'architettura e distribuirlo ai principali stakeholder.
* Sviluppare materiali didattici e documentazione, comunicazioni di sensibilizzazione, incentivi e altri programmi per guidare l'adozione da parte degli utenti.
* Eseguire la migrazione di qualsiasi script o strumento di correzione automatizzato esistente per supportare i requisiti del contratto di servizio stabiliti.

**Attività potenziali:**

* Completare ed eseguire il test dei dati di monitoraggio e di creazione di report con la scelta di una soluzione locale, gateway cloud o ibrida.
* Determinare se le modifiche devono essere apportate al contratto di servizio o ai criteri di gestione delle risorse.
* Migliorare le attività operative implementando funzionalità di query al fine di trovare la risorsa nel cloud estate in modo efficiente.
* Allineare le risorse alle mutevoli esigenze aziendali e ai requisiti di governance.
* Assicurarsi che macchine virtuali, reti virtuali e account di archiviazione rispettino le esigenze di accesso alle risorse effettive per ogni versione e adattarle se necessario.
* Verificare che il ridimensionamento automatico delle risorse soddisfi i requisiti di accesso.
* Verificare l'accesso utente a risorse, gruppi di risorse e sottoscrizioni di Azure e adattarne i controlli di accesso in base alle esigenze.
* Monitorare le modifiche ai piani di accesso alle risorse e verificare con gli stakeholder se sono necessarie altre approvazioni.
* Aggiornare le modifiche al documento delle linee guida sull'architettura in modo da riflettere i costi effettivi.
* Determinare se l'organizzazione richiede un allineamento al conto Profitti e perdite più chiaro da un punto di vista finanziario per la business unit di riferimento.
* Per le organizzazioni globali, implementare i requisiti di sovranità o conformità al contratto di servizio.
* Per l'aggregazione al cloud, distribuire una soluzione gateway a un provider di servizi cloud.
* Per gli strumenti che non consentono opzioni ibride o gateway, accoppiare perfettamente il monitoraggio con uno strumento di monitoraggio operativo.

## <a name="operate-and-post-implementation"></a>Operazione e post-implementazione

Al termine della trasformazione, la governance e le operazioni devono continuare a funzionare per il ciclo di vita naturale di un'applicazione o di un carico di lavoro. Questa fase di maturità della governance si basa principalmente sulle attività che vengono svolte in genere dopo che la soluzione è stata implementata e il ciclo di trasformazione ha iniziato a stabilizzarsi.

**Attività minime suggerite:**

* Personalizzare la [toolchain di Coerenza delle risorse](toolchain.md) in base ai mutevoli aggiornamenti delle esigenze di Gestione costi della tua organizzazione.
* Valutare l'opportunità di automatizzare le notifiche e i report in modo da riflettere l'utilizzo effettivo.
* Perfezionare le linee guida sull'architettura per fornire indicazioni precise sui processi di adozione futuri.
* Dedicarsi periodicamente alla formazione dei team interessati per assicurarsi che vengano costantemente rispettate le linee guida sull'architettura.

**Attività potenziali:**

* Modificare i piani su base trimestrale in base alle modifiche alle risorse effettive.
* Applicare e garantirne la continua applicazione automatica dei requisiti di governance delle distribuzioni future.
* Valutare le risorse che sono state sfruttate in modo non soddisfacente e determinare se vale la pena mantenerle.
* Rilevare eventuali disallineamenti e anomalie tra l'uso pianificato e quello effettivo.
* Aiutare i team di adozione del cloud e il team per la strategia cloud a comprendere e risolvere queste anomalie.
* Determinare se sono necessarie modifiche alla Coerenza delle risorse a scopo di fatturazione e di contratti di servizio.
* Valutare gli strumenti di registrazione e monitoraggio per determinare se la soluzione locale, gateway cloud o ibrida necessiti di modifiche.
* Per le unità aziendali e i gruppi distribuiti geograficamente, determinare se per l'organizzazione è consigliabile usare le funzionalità di gestione cloud aggiuntive (ad esempio [Gruppi di gestione di Azure](/azure/governance/management-groups/)) per applicare in modo ottimale criteri centralizzati e soddisfare i requisiti del contratto di servizio.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso il concetto di governance delle risorse cloud, si approfondirà [come viene gestito l'accesso alle risorse](azure-resource-access.md) in Azure, quindi come progettare un modello di governance per un [carico di lavori semplice ](governance-simple-workload.md) o per [più team](governance-multiple-teams.md).

> [!div class="nextstepaction"]
> [Informazioni sull'accesso alle risorse in Azure](azure-resource-access.md)
> [Informazioni sui contratti di servizio per Azure](https://azure.microsoft.com/support/legal/sla/)
> [Informazioni registrazione, creazione di report e monitoraggio](../../decision-guides/log-and-report/overview.md)
