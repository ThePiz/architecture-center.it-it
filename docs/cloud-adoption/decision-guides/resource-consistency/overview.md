---
title: 'CAF: Guida alle decisioni relative alla coerenza delle risorse'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sulla coerenza delle risorse nell'ambito della pianificazione di una migrazione di Azure.
author: rotycenh
ms.openlocfilehash: 8170bfd09218a451e086a57e0631b7e567eb2b82
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901541"
---
# <a name="caf-resource-consistency-decision-guide"></a>CAF: Guida alle decisioni relative alla coerenza delle risorse

La [progettazione della sottoscrizione](../subscriptions/overview.md) di Azure definisce la modalità di organizzazione delle risorse cloud in relazione alla struttura complessiva dell'azienda. Inoltre, l'integrazione degli standard di gestione IT esistenti e dei criteri dell'organizzazione dipende dal modo in cui si distribuiscono e organizzano le risorse cloud all'interno di una sottoscrizione.

Gli strumenti disponibili per implementare le progettazioni di distribuzione, raggruppamento e gestione delle risorse dipendono dalla piattaforma cloud. In generale, ogni soluzione include le funzionalità seguenti:

- Un meccanismo di raggruppamento logico sotto il livello della sottoscrizione o dell'account.
- La possibilità di distribuire le risorse a livello di codice con le API.
- Modelli per la creazione di distribuzioni standardizzate.
- La possibilità di distribuire regole per i criteri ai livelli di sottoscrizione, account e raggruppamento delle risorse.

![Grafico delle opzioni relative alla coerenza delle risorse, dalla meno complessa alla più complessa, allineato con i collegamenti sotto](../../_images/discovery-guides/discovery-guide-resource-consistency.png)

Passare a: [Raggruppamento di base](#basic-grouping) | [Coerenza di distribuzione](#deployment-consistency) | [Coerenza dei criteri](#policy-consistency) | [Coerenza gerarchica](#hierarchical-consistency) | [Coerenza automatizzata](#automated-consistency)

Le decisioni relative alla distribuzione e al raggruppamento delle risorse sono guidate principalmente da questi fattori: le dimensioni del digital estate dopo la migrazione, la complessità dell'azienda o dell'ambiente che non si adatta perfettamente agli approcci di progettazione della sottoscrizione esistenti o la necessità di applicare la governance nel tempo dopo la distribuzione delle risorse. Una progettazione di raggruppamento delle risorse più avanzata richiede uno sforzo maggiore per assicurare un raggruppamento accurato, con un conseguente aumento di tempo dedicato alla gestione del cambiamento e al rilevamento.

## <a name="basic-grouping"></a>Raggruppamento di base

In Azure i [gruppi di risorse](/azure/azure-resource-manager/resource-group-overview#resource-groups) sono un meccanismo principale di organizzazione delle risorse che consente di raggruppare le risorse in modo logico all'interno di una sottoscrizione.

I gruppi di risorse fungono da contenitori per le risorse con un ciclo di vita comune o vincoli di gestione condivisi, ad esempio criteri o requisiti di controllo degli accessi in base al ruolo. I gruppi di risorse non possono essere nidificati e le risorse possono appartenere a un solo gruppo di risorse. Alcune azioni possono essere applicate a tutte le risorse di un gruppo di risorse. Ad esempio, l'eliminazione di un gruppo di risorse comporta la rimozione di tutte le risorse all'interno di quel gruppo. La creazione di gruppi di risorse prevede degli schemi comuni, generalmente divisi in due categorie:

- Carichi di lavoro IT tradizionali: sono più comunemente raggruppati in base agli elementi all'interno di uno stesso ciclo di vita, ad esempio un'applicazione. Il raggruppamento in base all'applicazione consente la gestione di singole applicazioni.
- Carichi di lavoro IT Agile: sono incentrati sulle applicazioni cloud orientate ai clienti esterni. Questi gruppi di risorse spesso riflettono i livelli funzionali di distribuzione (ad esempio livello Web o livello app) e gestione.

## <a name="deployment-consistency"></a>Coerenza di distribuzione

La maggior parte delle piattaforme cloud offre un sistema, basato sul meccanismo di raggruppamento delle risorse di base, che consente di usare modelli per distribuire le risorse nell'ambiente cloud. È possibile usare i modelli per creare convenzioni di denominazione e organizzazione coerenti per distribuire i carichi di lavoro, applicando i vari aspetti della progettazione della distribuzione e gestione delle risorse.

I [modelli di Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) consentono di distribuire ripetutamente le risorse in uno stato coerente usando una configurazione e una struttura dei gruppi di risorse predeterminate. Con questi modelli è possibile definire facilmente un set di standard come base delle distribuzioni.

Ad esempio, si può usare un modello standard per distribuire un carico di lavoro di server Web che contiene due macchine virtuali come server Web combinati con un servizio di bilanciamento del carico per gestire il traffico tra i server. È quindi possibile riutilizzare questo modello per creare distribuzioni strutturalmente identiche ogni volta che è necessario un nuovo carico di lavoro di server Web, cambiando solo il nome e l'indirizzo IP della distribuzione.

È anche possibile distribuire questi modelli a livello di codice e integrarli con i sistemi CI/CD.

## <a name="policy-consistency"></a>Coerenza dei criteri

Per assicurare che i criteri di governance siano applicati al momento della creazione delle risorse, parte della progettazione del raggruppamento delle risorse prevede l'uso di una configurazione comune per distribuire le risorse.

Combinando gruppi di risorse e modelli di Resource Manager standardizzati, è possibile applicare standard relativi alle impostazioni necessarie in una distribuzione e alle regole di [Criteri di Azure](/azure/governance/policy/overview) da applicare a ogni gruppo di risorse o risorsa.

Supponiamo ad esempio che esista un requisito in base al quale tutte le macchine virtuali distribuite all'interno della sottoscrizione devono connettersi a una subnet comune gestita dal team IT centrale. È possibile creare un modello standard per la distribuzione di macchine virtuali del carico di lavoro che crea un gruppo di risorse separato per il carico di lavoro e vi distribuisce le macchine virtuali necessarie. Questo gruppo di risorse deve avere una regola dei criteri che consente di aggiungere alla subnet condivisa solo le interfacce di rete all'interno del gruppo di risorse.

Per una discussione più approfondita in merito all'applicazione delle decisioni relative ai criteri in una distribuzione cloud, vedere [Imposizione dei criteri](../policy-enforcement/overview.md).

## <a name="hierarchical-consistency"></a>Coerenza gerarchica

Man mano che aumentano le dimensioni dell'ambiente cloud, potrebbe presentarsi la necessità di supportare requisiti di governance più complessi di quelli che possono essere supportati mediante la gerarchia azienda/reparto/account/sottoscrizione del contratto Enterprise di Azure. I gruppi di risorse consentono di supportare livelli di gerarchia aggiuntivi all'interno dell'organizzazione, applicando regole di Criteri di Azure e controlli di accesso a livello di gruppo di risorse.

I [gruppi di gestione di Azure](../subscriptions/overview.md#management-groups) possono supportare strutture organizzative più complesse sovrapponendo una gerarchia alternativa alla struttura del contratto Enterprise. Questo consente alle sottoscrizioni, e alle risorse che contengono, di supportare meccanismi di controllo di accesso e applicazione di criteri organizzati in modo da soddisfare i requisiti organizzativi dell'azienda.

## <a name="automated-consistency"></a>Coerenza automatizzata

Per le distribuzioni cloud di grandi dimensioni, la governance globale diventa tanto più importante quanto più complessa. È fondamentale applicare automaticamente i requisiti di governance durante la distribuzione delle risorse, oltre a soddisfare i requisiti aggiornati per le distribuzioni esistenti.

[Azure Blueprints](/azure/governance/blueprints/overview) consente alle organizzazioni di supportare la governance globale di ambienti cloud estesi in Azure. Blueprints va oltre le funzionalità fornite da modelli di Azure Resource Manager standard, permettendo di creare orchestrazioni di distribuzione complete in grado di distribuire le risorse e applicare regole dei criteri. Blueprints supporta il controllo delle versioni, la possibilità di applicare aggiornamenti a tutte le sottoscrizioni in cui è stato usato il piano del corso e la possibilità di bloccare le sottoscrizioni distribuite per evitare la creazione e la modifica non autorizzate di risorse.

Questi pacchetti di distribuzione consentono ai team IT e di sviluppo di distribuire rapidamente nuovi carichi di lavoro e risorse di rete conformi alle variazioni dei requisiti relativi ai criteri organizzativi. Blueprints può anche essere integrato in pipeline CI/CD per applicare standard di governance modificati alle distribuzioni quando vengono aggiornate.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come vengono usate la denominazione delle risorse e l'applicazione di tag per organizzare ulteriormente e gestire le risorse cloud.

> [!div class="nextstepaction"]
> [Denominazione delle risorse e applicazione di tag](../resource-tagging/overview.md)
