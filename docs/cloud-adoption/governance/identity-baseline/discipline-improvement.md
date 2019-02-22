---
title: 'CAF: Miglioramento della disciplina Baseline di identità'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Miglioramento della disciplina Baseline di identità
author: BrianBlanchard
ms.openlocfilehash: c96a638af549782fec22b2068c9b4943df4b943a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901319"
---
# <a name="identity-baseline-discipline-improvement"></a>Miglioramento della disciplina Baseline di identità

La disciplina Baseline di identità è incentrata sulle modalità di definizione dei criteri che garantiscono la coerenza e la continuità delle identità utente, indipendentemente dal provider di servizi cloud che ospita l'applicazione o il flusso di lavoro. All'interno delle cinque discipline di Governance Cloud, la Baseline di identità include le decisioni relative a [Hybrid Identity Strategy (Strategia di identità ibrida)](../../decision-guides/identity/overview.md), valutazione ed estensione dei repository di identità, implementazione di Single Sign-On (same sign-on), controllo e monitoraggio per usi non autorizzati o attori malintenzionati. In alcuni casi, potrebbe inoltre comportare decisioni per modernizzare, consolidare o integrare più provider di identità.

Questo articolo delinea alcune attività potenziali che l'azienda può svolgere al fine di sviluppare ed evolvere al meglio la disciplina Baseline di identità. Queste attività possono essere suddivise nelle fasi di pianificazione, costruzione, adozione e funzionamento dell'implementazione di una soluzione cloud, che vengono poi ripetute per consentire lo sviluppo di un [incremental approach to cloud governance (approccio incrementale alla governance del cloud)](../journeys/overview.md#an-incremental-approach-to-cloud-governance).

![Quattro fasi di adozione](../../_images/adoption-phases.png)

*Figura 1. Fasi di adozione dell'approccio incrementale alla governance del cloud.*

È impossibile tener conto per qualsiasi documento dei requisiti di tutte le aziende. Di conseguenza, questo articolo delinea le attività di esempio minime e potenziali suggerite per ogni fase del processo di maturazione della governance. L'obiettivo iniziale di queste attività è quello di permettere di costruire un [MVP dei criteri](../journeys/overview.md#an-incremental-approach-to-cloud-governance) e stabilire un framework per l'evoluzione incrementale dei criteri di Azure. Il team di Governance cloud dovrà decidere quanto investire in queste attività per migliorare le capacità di governance della Baseline di identità.

> [!CAUTION]
> Nessuna delle attività minime o potenziali descritte in questo articolo sono allineate a specifici criteri aziendali o requisiti di conformità di terze parti. Questo materiale sussidiario è progettato per facilitare le conversazioni che porteranno all'allineamento di entrambi i requisiti con un modello di governance del cloud.

## <a name="planning-and-readiness"></a>Pianificazione e preparazione

Questa fase di maturità della governance colma il divario tra i risultati aziendali e le strategie attuabili. Durante questo processo, il team di leadership definisce metriche specifiche, esegue il mapping di tali metriche sul patrimonio digitale e inizia a pianificare il lavoro richiesto di migrazione complessiva.

**Attività minime suggerite:**

* Valutare la [Identity toolchain (Toolchain di identità)](toolchain.md) e implementare una strategia ibrida adatta alla propria organizzazione.
* Sviluppare una bozza di documento relativo alle linee guida sull'architettura e distribuirla ai principali stakeholder.
* Formare e coinvolgere le persone e i team interessati allo sviluppo delle linee guida sull'architettura.

**Attività potenziali:**

* Definire i ruoli e le assegnazioni che regolano l'identità e la gestione dell'accesso nel cloud.
* Definire i gruppi in locale ed associarli ai ruoli corrispondenti basati sul cloud.
* Provider di identità di inventario (incluse le identità basate su database usate da applicazioni personalizzate).
* Prendere in considerazione il consolidamento o l'integrazione di provider di identità in cui è presente la duplicazione, al fine di semplificare la soluzione di identità complessiva.
* Valutare la compatibilità ibrida di provider di identità esistenti.
* Per i provider di identità che non sono ibrido compatibili, valutare le opzioni di sostituzione o di consolidamento.

## <a name="build-and-pre-deployment"></a>Compilazione e pre-distribuzione

È necessaria una serie di prerequisiti tecnici e non, per eseguire correttamente la migrazione di un ambiente. Questo processo è incentrato sulle decisioni, sulla conformità e sull'infrastruttura di base che consente una migrazione.

**Attività minime suggerite:**

* Prendere in considerazione un test pilota prima di implementare la [Identity toolchain (Toolchain di identità)](toolchain.md), assicurandosi che semplifichi l'esperienza utente il più possibile.
* I feedback si applicano direttamente dai test pilota nella pre-distribuzione. Ripetere fino a quando i risultati sono accettabili.
* Aggiornare il documento sulle linee guida dell'architettura per includere piani di distribuzione e adozione da parte degli utenti e per distribuirlo ai principali stakeholder.
* Prendere in considerazione la definizione di un programma early adopter e distribuire un numero limitato di utenti.
* Continuare a formare le persone e i team più interessati alle linee guida dell'architettura.

**Attività potenziali:**

* Valutare l'architettura logica e fisica e determinare una [Hybrid Identity Strategy (Strategia di identità ibrida)](../../decision-guides/identity/overview.md).
* Eseguire l'associazione dei criteri di gestione di accesso alle identità, ad esempio le assegnazioni di ID di accesso, quindi scegliere il metodo di autenticazione appropriato per Azure AD.
  * Se federati, abilitare restrizioni dei tenant per gli account amministrativi.
* Integrare le directory locali e il cloud.
* È consigliabile usare i modelli di accesso seguenti:
  * modello [Accesso con privilegi minimi](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)
  * modello di accesso [Baseline di identità privilegiata](/azure/active-directory/privileged-identity-management/pim-configure)
* Completare tutti i dettagli relativi alla pre-integrazione e rivedere [Procedure consigliate di identità](/azure/security/azure-security-identity-management-best-practices).
  * Abilitare identità singola, Single Sign-On (SSO) o l'accesso SSO facile
  * Configurare l'autenticazione a più fattori (MFA) per gli amministratori
  * Se necessario, consolidare o integrare i provider di identità
  * Implementare gli strumenti necessari per centralizzare la gestione delle identità
  * Abilitare l'accesso just-in-time (JIT) e gli avvisi di modifica ruolo
  * Condurre un'analisi dei rischi delle attività di amministrazione principali per l'assegnazione a ruoli predefiniti
  * Prendere in considerazione l'implementazione aggiornata di metodi di autenticazione più avanzati per tutti gli utenti
  * Abilitare Privileged Identity Management (PIM) della baseline per JIT (tramite l'attivazione a tempo limitato) per altri ruoli amministrativi
  * Separare gli account utente dagli account amministratore globale (per assicurarsi che gli amministratori non aprano inavvertitamente le e-mail o eseguano programmi associati con i propri account amministratore globale)

## <a name="adopt-and-migrate"></a>Adottare ed eseguire la migrazione

La migrazione è un processo incrementale incentrato sul movimento, il testing e l'adozione di applicazioni o carichi di lavoro in un patrimonio digitale esistente.

**Attività minime suggerite:**

* Eseguire la migrazione della [Identity toolchain (Toolchain di identità)](toolchain.md) dallo sviluppo alla produzione.
* Aggiornare il documento relativo alle linee guida sull'architettura e distribuirlo ai principali stakeholder.
* Sviluppare materiali didattici e documentazione, comunicazioni di sensibilizzazione, incentivi e altri programmi per guidare l'adozione da parte degli utenti.

**Attività potenziali:**

* Verificare che le procedure consigliate definite durante le fasi di compilazione/pre-distribuzione vengano eseguite correttamente.
* Convalidare e/o ridefinire la [Hybrid Identity Strategy (Strategia di identità ibrida)](../../decision-guides/identity/overview.md).
* Assicurarsi che ogni applicazione o carico di lavoro continui l'allineamento alla strategia di identità prima del rilascio.
* Verificare che Single Sign-On (SSO) e l'accesso SSO facile funzionino come previsto dalle applicazioni.
* Se possibile, ridurre o eliminare il numero di archivi identità alternativi.
* Esaminare la necessità per ogni archivio identità nell'app o nel database. Le identità che si trovano all'esterno di un provider di identità appropriato (proprio o di terze parti) possono rappresentare rischi per l'applicazione e gli utenti.
* Abilitare l'accesso condizionale per le [Applicazioni federate locali](/azure/active-directory/active-directory-device-registration-on-premises-setup).
* Distribuire identità tra le aree globali in più hub con sincronizzazione tra le aree.
* Stabilire la federazione di controllo centrale degli accessi in base al ruolo (RBAC).

## <a name="operate-and-post-implementation"></a>Operazione e post-implementazione

Al termine della trasformazione, la governance e le operazioni devono continuare a funzionare per il ciclo di vita naturale di un'applicazione o del carico di lavoro. Questa fase di maturità della governance si concentra sulle attività che comunemente vengono dopo che la soluzione sia stata implementata e che il ciclo di trasformazione inizi a stabilizzarsi.

**Attività minime suggerite:**

* Personalizzare la [Identity Baseline toolchain (Tolchain della baseline di identità)](toolchain.md), in base alle modifiche delle mutevoli esigenze di identità della propria organizzazione.
* Automatizzare le notifiche e i report in modo che possano avvisare su potenziali minacce.
* Monitorare e creare report sullo stato di uso del sistema e di adozione da parte dell'utente.
* Creare report sulle metriche di post-distribuzione e distribuirle agli stakeholder.
* Perfezionare le linee guida sull'architettura per guidare i processi di adozione futura.
* Comunicare e dedicarsi periodicamente alla formazione continua dei team interessati per garantire il rispetto costante delle linee guida sull'architettura.

**Attività potenziali:**

* Eseguire controlli periodici dei criteri di identità e delle prassi di conformità.
* Analizzare regolarmente gli attori malintenzionati e le violazioni dei dati, in particolare quelli correlati a furti di identità, ad esempio potenziali acquisizioni di account amministratore.
* Configurare uno strumento di monitoraggio e creazione di report.
* Prendere in considerazione minuziosamente l'integrazione con sistemi di prevenzione delle frodi e sicurezza.
* Esaminare regolarmente i diritti di accesso per utenti con privilegi o ruoli elevati.
  * Identificare tutti gli utenti idonei per attivare i privilegi di amministratore.
* Esaminare l'onboarding, l'offboarding e i processi di aggiornamento della credenziali.
* Verificare i livelli crescenti di automazione e la comunicazione tra i moduli di gestione delle identità e degli accessi (IAM).
* Prendere in considerazione di implementare un approccio di sviluppo delle operazioni di sicurezza (DevSecOps).
* Eseguire un'analisi di impatto per misurare i risultati su costi, sicurezza e adozione utente.
* Presentare periodicamente un report di impatto che mostri le modifiche delle metriche create dal sistema e stimare l'impatto aziendale della [Hybrid Identity Strategy (Strategia di identità ibrida)](../../decision-guides/identity/overview.md).
* Stabilire un monitoraggio integrato consigliato dal [Centro sicurezza di Azure](/azure/security-center/security-center-intro).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compreso il concetto di governance dell'identità del cloud, esaminare la [Identity Baseline toolchain (Toolchain della baseline di identità)](toolchain.md) per identificare gli strumenti e le funzionalità di Azure di cui si avrà bisogno per sviluppare la disciplina di governance della baseline di identità sulla piattaforma Azure.

> [!div class="nextstepaction"]
> [Identity Baseline toolchain for Azure (Toolchain della baseline di identità per Azure)](toolchain.md)