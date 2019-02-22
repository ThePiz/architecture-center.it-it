---
title: 'CAF: Monitorare e applicare la conformità ai criteri'
description: Come è possibile garantire la conformità con i criteri stabiliti?
author: BrianBlanchard
ms.date: 1/4/2019
ms.openlocfilehash: 9066f33c8baa183476a9632e82d6eb960d03752c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901327"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-processes-can-help-ensure-policy-adherence"></a>Quali processi possono aiutare a garantire la conformità ai criteri?

<!---
I've defined policies, I've provided an architecture guide. Now how do I monitor adherence to policy? If there is a violation, how do I enforce the policy?
--->

Dopo la definizione delle istruzioni dei criteri cloud e l'elaborazione di una guida di progettazione, è necessario creare una strategia per garantire che la distribuzione cloud sia conforme ai requisiti dei criteri. Questa strategia deve includere verifica continua e processi di comunicazione del team di governance cloud, deve definire i principi per cui le violazioni dei criteri richiedono un'azione e i requisiti per i sistemi di monitoraggio e conformità automatizzati atti a rilevare le violazioni e ad attivare le relative azioni correttive.

Vedere le sezioni sui criteri aziendali di [Actionable Governance Journeys](../journeys/overview.md) (Percorsi di governance di utilità pratica) per alcuni esempi sul modo in cui il processo di conformità ai criteri rientra in un piano di governance cloud.

## <a name="prioritize-policy-adherence-processes"></a>Classificare i processi di conformità ai criteri

Quanto investimento nello sviluppo dei processi è necessario per supportare gli obiettivi dei criteri? A seconda delle dimensioni e del livello di maturità della distribuzione cloud, l'impegno necessario per stabilire i processi che supportano la conformità, e i costi associati per farlo, possono variare notevolmente.

Per distribuzioni di piccole dimensioni costituite da risorse di sviluppo e test, i requisiti dei criteri possono essere semplici e richiedere poche risorse dedicate. D'altra parte, una distribuzione cloud matura e cruciale con esigenze di sicurezza e prestazioni di alta priorità può richiedere un team di personale, processi interni completi e strumenti di monitoraggio personalizzati per supportare gli obiettivi dei criteri.

Come primo passaggio nella definizione della strategia di conformità ai criteri, bisogna valutare il modo in cui i processi descritti di seguito possono supportare i requisiti dei criteri. Determinare l'impegno che vale la pena investire in questi processi e quindi usare queste informazioni per stabilire budget realistici e piani del personale che soddisfino queste esigenze.

## <a name="establish-cloud-governance-team-processes"></a>Stabilire i processi del team di governance cloud

Prima di definire i trigger per la correzione di conformità ai criteri, è necessario stabilire i processi generali usati dal team e il modo in cui le informazioni vengono condivise e inoltrate tra il personale IT e il team di governance cloud.

### <a name="assign-cloud-governance-team-members"></a>Assegnare i membri del team di governance cloud

Chi offre indicazioni continue sulla conformità ai criteri e gestisce i problemi relativi ai criteri che emergono quando si distribuiscono e si attivano le risorse cloud? Le dimensioni e la composizione del team di governance cloud variano in base alla complessità dei requisiti dei criteri e alle priorità di budget e personale associate alla conformità ai criteri.

Scegliere i membri del team che hanno esperienza nelle aree comprese nelle istruzioni dei criteri definite. Per le distribuzioni di prova iniziale, questo può essere limitato a pochi amministratori di sistema responsabili della definizione delle nozioni di base della governance. Quando le distribuzioni si sviluppano e i criteri diventano più complessi e più integrati con i requisiti dei più ampi criteri aziendali, è necessario modificare il team di governance cloud in modo tale da supportare i requisiti di criteri sempre più complicati.

Quando i processi di governance maturano, è necessario verificare l'appartenenza del team indicazioni cloud regolarmente per garantire la possibilità di indirizzare correttamente i requisiti dei criteri più recenti. Identificare i membri del personale IT con esperienza pertinente o interesse in aree specifiche di governance e includerli nei team in modo permanente o ad-hoc secondo necessità.

### <a name="reviews-and-policy-iteration"></a>Revisioni e iterazione dei criteri

Quando le risorse aggiuntive vengono distribuite, è necessario che il team di governance cloud garantisca che i nuovi carichi di lavoro o le risorse siano conformi ai requisiti dei criteri. Pianificare incontri con i team responsabili della distribuzione di nuove risorse per discutere l'allineamento alle guide di progettazione.

Con l'aumento della distribuzione complessiva, bisogna valutare regolarmente nuovi rischi potenziali e aggiornare le istruzioni dei criteri e le guide di progettazione se necessario. Pianificare cicli di revisione regolari per ognuna delle cinque discipline di governance per assicurarsi che i criteri siano aggiornati e soddisfatti.

### <a name="education"></a>Formazione

La conformità ai criteri richiede che il personale e gli sviluppatori IT comprendano i requisiti dei criteri che influiscono sulle proprie aree di responsabilità. Pianificare di riservare risorse per documentare le decisioni e i requisiti e formare tutti i team pertinenti sulle guide di progettazione che supportano i requisiti dei criteri.

Quando i criteri cambiano, è necessario aggiornare regolarmente la documentazione e i materiali di formazione e assicurarsi che gli sforzi di formazione comunichino requisiti e materiale sussidiario aggiornati al personale IT pertinente.  

### <a name="establish-escalation-paths"></a>Stabilire percorsi di escalation

Se una risorsa diventa non conforme, chi riceve la notifica? Se il personale IT rileva un problema di conformità dei criteri, chi dovrebbe contattare? Assicurarsi che il processo di escalation al team di governance cloud sia chiaramente definito. Verificare che questi canali di comunicazione siano mantenuti aggiornati in modo da riflettere modifiche del personale e dell'organizzazione.

## <a name="violation-triggers-and-actions"></a>Trigger di violazione e azioni

Dopo aver definito il team di governance cloud e i relativi processi, è necessario definire in modo esplicito che cosa si intende per violazioni di conformità che attivano azioni e quali devono essere tali azioni.

### <a name="define-triggers"></a>Definire i trigger

Per ognuna delle istruzioni dei criteri, esaminare i requisiti per determinare cosa costituisce una violazione dei criteri. Generare i trigger usando le informazioni già configurate come parte del processo di definizione dei criteri.

* Tolleranza ai rischi - Creare trigger di violazione basati sugli indicatori di metrica e dei rischi stabiliti nell'ambito dell'[analisi di tolleranza dei rischi](risk-tolerance.md).
* Requisiti dei criteri definiti - Le istruzioni dei criteri possono offrire contratto di servizio, continuità aziendale e ripristino di emergenza o requisiti di prestazione che è consigliabile usare come base per i trigger di conformità.

### <a name="define-actions"></a>Definire le azioni

Ogni trigger di violazione deve avere un'azione corrispondente. Le azioni attivate devono sempre inviare una notifica a un membro del personale IT o del team di governance cloud appropriato quando si verifica una violazione. Questa notifica può portare a una revisione manuale del problema di conformità oppure all'avvio di un processo di correzione prestabilito in base al tipo e alla gravità della violazione rilevata.

Alcuni esempi di trigger di violazione e azioni:

| Disciplina di governance cloud | Trigger di esempio | Azione di esempio |
|-----------------------------|----------------|---------------|
| Gestione costi | Spesa mensile per il cloud superiore di oltre il 20% rispetto al previsto. | Inviare una notifica al responsabile dell'unità di fatturazione per avviare una verifica sull'utilizzo delle risorse. |
| Baseline di sicurezza | Rilevamento attività di accesso utente sospetta. | Avvisare il team di sicurezza IT e disattivare l'account utente sospetto. |
| Coerenza delle risorse | Utilizzo della CPU per carico di lavoro superiore al 90%. | Informare il team operativo IT e ampliare le risorse aggiuntive per gestire il carico. |

## <a name="monitoring-and-compliance-automation"></a>Automazione di monitoraggio e conformità

Dopo aver definito i trigger di violazione di conformità e le azioni, è possibile iniziare a pianificare il modo migliore per usare strumenti di registrazione e reporting e altre funzionalità della piattaforma cloud per automatizzare la strategia di monitoraggio e di conformità dei criteri.

Consultare l'argomento CAF di [guida decisionale a registrazione e reporting](../../decision-guides/log-and-report/overview.md) per indicazioni su come scegliere il miglior modello di monitoraggio per la distribuzione.
