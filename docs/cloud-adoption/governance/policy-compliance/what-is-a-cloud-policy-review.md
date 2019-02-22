---
title: "CAF: Cos'è la revisione dei criteri per il cloud?"
description: Cos'è la revisione dei criteri per il cloud?
author: BrianBlanchard
ms.date: 2/8/2019
ms.openlocfilehash: b879f261e16ffc72180417e2e0f533e2eaa435ba
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901819"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-a-cloud-policy-review"></a>Cos'è la revisione dei criteri per il cloud?

Una **revisione dei criteri per il cloud** è il primo passo verso la [maturità della governance](../overview.md) nel cloud. L'obiettivo di questo processo è quello di modernizzare i criteri IT aziendali esistenti. Al termine, i criteri aggiornati offriranno un livello equivalente di mitigazione dei rischi per le risorse basate sul cloud. Questo articolo descrive il processo di revisione dei criteri per il cloud e la sua importanza.

## <a name="why-perform-a-cloud-policy-review"></a>Perché eseguire una revisione dei criteri per il cloud?

La maggior parte delle aziende gestisce l'IT tramite l'esecuzione di processi allineati ai criteri di governance. Nelle piccole imprese questi criteri possono essere aneddotici e basarsi su processi definiti in modo vago. Di pari passo con l'espansione delle aziende, i criteri e i processi tendono a essere documentati più chiaramente ed eseguiti in modo coerente.

Man mano che le organizzazioni maturano i propri criteri IT aziendali, le dipendenze dalle decisioni tecniche prese in precedenza tendono a diffondersi come criteri di governance. Ad esempio, è comune che i processi di ripristino di emergenza includano criteri che impongono il backup su nastro esterno. Questa decisione presuppone una dipendenza da un tipo di tecnologia (backup su nastro) che potrebbe non essere più la soluzione ideale.

Le trasformazioni per il cloud creano un punto di flesso naturale per riesaminare le decisioni relative ai criteri legacy prese in passato. Le funzionalità tecniche e i processi predefiniti cambiano notevolmente nel cloud, così come i rischi ereditati. Prendendo nuovamente in considerazione l'esempio precedente, il criterio di backup su nastro deriva dal rischio di un singolo punto di guasto legato al mantenimento dei dati in un'unica posizione e dalla necessità dell'azienda di ridurre al minimo il profilo di rischio mitigando questo rischio. In una distribuzione cloud sono disponibili diverse opzioni per la stessa mitigazione dei rischi, con obiettivi del tempi di ripristino (RTO, Recovery Time Objective) molto inferiori. Ad esempio:

- Una soluzione nativa del cloud potrebbe abilitare la replica geografica del database SQL di Azure
- Una soluzione ibrida potrebbe sfruttare Azure Site Recovery (ASR) per la replica di un carico di lavoro IaaS in più data center

Quando si esegue una trasformazione per il cloud, spesso i criteri determinano molti degli strumenti, dei servizi e dei processi disponibili per i team di adozione del cloud. Se questi criteri si basano su tecnologie legacy, possono intralciare gli sforzi del team mirati all'attuazione delle modifiche. Nel peggiore dei casi i criteri importanti vengono completamente ignorati dal team di migrazione per abilitare soluzioni alternative. Nessuno dei due è un risultato accettabile.

## <a name="the-cloud-policy-review-process"></a>Processo di revisione dei criteri per il cloud

Le revisioni dei criteri per il cloud si allineano ai criteri di sicurezza e governance IT esistenti con le [cinque discipline della governance del cloud](../overview.md): [Gestione costi](../cost-management/overview.md), [Baseline di sicurezza](../security-baseline/overview.md), [Baseline di identità](../identity-baseline/overview.md), [Coerenza delle risorse](../resource-consistency/overview.md) e [Accelerazione della distribuzione](../deployment-acceleration/overview.md).

Per ognuna di queste discipline, il processo di revisione segue questi passaggi:

1. Rivedere i criteri locali esistenti correlati alla disciplina specifica, prestando particolare attenzione a due punti dati chiave: dipendenze legacy e rischi aziendali identificati.
2. Valutare ogni rischio aziendale ponendo una semplice domanda: "Il rischio aziendale sussiste ancora in un modello di cloud?"
3. Se il rischio è ancora presente, riscrivere il criterio documentando la mitigazione necessaria, non la soluzione tecnica.
4. Rivedere i criteri aggiornati con i team di adozione del cloud per comprendere le potenziali soluzioni per la mitigazione richiesta.

## <a name="example-of-a-policy-review-for-a-legacy-policy"></a>Esempio di una revisione dei criteri per un criterio legacy

Per fornire un esempio del processo, è possibile usare nuovamente il criterio di backup su nastro presentato nella sezione precedente:

- Un criterio aziendale impone i backup su nastro esterni per tutti i sistemi di produzione. In questo criterio è possibile vedere due punti dati di interesse:
  - Dipendenza legacy da una soluzione di backup su nastro
  - Rischio aziendale presunto associato all'archiviazione dei backup nella stessa posizione fisica delle attrezzature di produzione.
- Il rischio sussiste ancora? Sì. Anche nel cloud una dipendenza da una singola funzionalità crea un rischio. Ci sono minori probabilità che interessi l'azienda rispetto alla soluzione in locale, ma tale rischio esiste comunque.
- Riscrivere il criterio. In caso di emergenza a livello di data center, deve esistere un modo per ripristinare i sistemi di produzione entro 24 ore dall'interruzione del servizio in un altro data center e in un'area geografica diversa.
- Procedere alla revisione con i team di adozione del cloud. A seconda della soluzione implementata, ci sono più modi per rispettare questo criterio di Coerenza delle risorse.

## <a name="tools-to-help-create-modern-policies"></a>Strumenti per semplificare la creazione di criteri moderni

Per accelerare la creazione di criteri moderni, è disponibile un set di criteri di esempio in ognuna delle cinque discipline della governance del cloud. Questi criteri di esempio verranno avviati con uno di questi tre presupposti di progettazione:

- **Nativa del cloud**: la soluzione da distribuire è nativa del cloud e può trarre vantaggio dalle soluzioni predefinite disponibili in Azure, con la configurazione minima.
- **Di livello enterprise**: la soluzione da distribuire è complessa e richiede un modello di distribuzione del cloud ibrido. Ciò richiede implementazioni più complesse di determinate discipline di governance.
- **Conforme ai principi di progettazione del cloud**: la soluzione da distribuire deve rispettare gli assi architetturali definiti nei principi di progettazione del cloud, richiedendo un livello molto più elevato di governance.  

Per ogni disciplina, deve essere creato un criterio di esempio in ognuno di questi livelli. Ogni esempio è stato pensato per suscitare discussioni e scambi di opinioni all'interno dell'ambiente aziendale. Si noti che questi esempi non sono intesi per l'uso in alternativa a criteri IT aziendali adeguatamente realizzati.
