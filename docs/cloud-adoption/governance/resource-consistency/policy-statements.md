---
title: 'CAF: Definizioni dei criteri di esempio per la coerenza delle risorse'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Definizioni dei criteri di esempio per la coerenza delle risorse
author: BrianBlanchard
ms.openlocfilehash: 9217ae73b0edf5b40bedac1cdc961b7a34da87d1
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901471"
---
# <a name="resource-consistency-sample-policy-statements"></a>Definizioni dei criteri di esempio per la coerenza delle risorse

Le singole definizioni dei criteri del cloud sono linee guida per affrontare i rischi specifici identificati durante il processo di valutazione dei rischi. Queste definizioni devono fornire un riassunto conciso dei rischi e dei piani per gestirli. Ogni definizione di istruzione deve includere i tipi di informazioni seguenti:

- Rischio tecnico - Riepilogo del rischio a cui sono applicabili questi criteri.
- Definizione dei criteri - Una spiegazione chiara e riepilogativa dei requisiti dei criteri.
- Opzioni di progettazione - Suggerimenti pratici, specifiche o altre linee guida che i team IT e gli sviluppatori possono usare per l'implementazione dei criteri.

Gli esempi seguenti di definizioni dei criteri riguardano alcuni rischi aziendali comuni relativi alla coerenza delle risorse e vengono forniti come esempi a cui fare riferimento durante la creazione delle definizioni dei criteri per rispondere alle esigenze specifiche dell'organizzazione. Si noti che questi esempi non devono essere considerati prescrittivi e che esistono potenzialmente diverse opzioni relative ai criteri per affrontare qualsiasi tipo di rischio. Lavorare a stretto contatto con i team responsabili delle attività aziendali e IT per identificare le migliori soluzioni di criteri per il proprio set di rischi specifico.

## <a name="tagging"></a>Assegnazione di tag

**Rischio tecnico**: senza i tag dei metadati appropriati associati alle risorse distribuite, il reparto responsabile delle operazioni IT non può definire le priorità per il supporto o l'ottimizzazione delle risorse in base al contratto di servizio richiesto, all'importanza per le operazioni aziendali o ai costi operativi. Ciò può comportare l'errata allocazione delle risorse IT e possibili ritardi nella risoluzione degli eventi imprevisti.

**Definizione dei criteri**: verranno implementati i criteri seguenti:

- Agli asset distribuiti devono essere assegnati tag con i valori seguenti: costi, criticità, contratto di servizio e ambiente.
- Gli strumenti di governance devono convalidare l'assegnazione di tag correlati a costo, criticità, contratto di servizio, applicazione e ambiente. Tutti i valori devono essere allineati ai valori predefiniti gestiti dal team di governance.

**Opzioni di progettazione potenziali**: in Azure i [tag di metadati standard nome-valore](/azure/azure-resource-manager/resource-group-using-tags) sono supportati per la maggior parte dei tipi di risorse. Si usa [Criteri di Azure](/azure/governance/policy/overview) per applicare tag specifici nell'ambito della creazione delle risorse.

## <a name="ungoverned-subscriptions"></a>Sottoscrizioni non gestite

**Rischio tecnico**: la creazione arbitraria di sottoscrizioni e gruppi di gestione può portare a sezioni isolate delle risorse cloud non correttamente soggette ai criteri di governance stabiliti.

**Definizione dei criteri**: la creazione di nuovi gruppi di gestione o sottoscrizioni per le applicazioni cruciali o i dati protetti richiede una revisione da parte del team di governance del cloud. Le modifiche approvate verranno integrate in un'assegnazione di progetto appropriata.

**Opzioni di progettazione potenziali**: consentire l'accesso amministrativo ai [gruppi di gestione di Azure](/azure/governance/management-groups/) dell'organizzazione solo ai membri approvati del team di governance che controlleranno la creazione di sottoscrizioni e il processo di controllo degli accessi.

## <a name="manage-updates-to-virtual-machines"></a>Gestire gli aggiornamenti delle macchine virtuali

**Rischio tecnico**: le macchine virtuali che non sono aggiornate con gli aggiornamenti e le patch software più recenti sono vulnerabili a problemi di sicurezza o di prestazioni, che possono portare a interruzioni del servizio.

**Definizione dei criteri**: gli strumenti di governance devono imporre l'abilitazione degli aggiornamenti automatici per tutte le macchine virtuali distribuite. Le violazioni devono essere esaminate con i team di gestione operativa e risolte in base ai criteri relativi alle operazioni. Gli asset che non vengono aggiornati automaticamente devono essere inclusi in processi di proprietà del team responsabile delle operazioni IT.

**Opzioni di progettazione potenziali**: per le macchine virtuali ospitate in Azure, è possibile implementare una gestione coerente degli aggiornamenti usando la [Soluzione Gestione aggiornamenti in Azure](/azure/automation/automation-update-management).

## <a name="deployment-compliance"></a>Conformità della distribuzione

**Rischio tecnico**: gli script di distribuzione e gli strumenti di automazione non vagliati completamente dal team di governance del cloud possono causare distribuzioni di risorse che violano i criteri.

**Definizione dei criteri**: verranno implementati i criteri seguenti:

- Gli strumenti di distribuzione devono essere approvati dal team di governance del cloud per garantire la governance regolare degli asset distribuiti.
- Gli script di distribuzione devono essere conservati in un archivio centrale accessibile dal team di governance del cloud per la revisione e il controllo periodici.

**Opzioni di progettazione potenziali**: l'uso coerente di [Azure Blueprints](/azure/governance/blueprints/) per gestire le distribuzioni automatizzate consente distribuzioni coerenti delle risorse di Azure che rispettano gli standard e i criteri di governance dell'organizzazione.

## <a name="monitoring"></a>Monitoraggio

**Rischio tecnico**: l'implementazione non appropriata o la strumentazione incoerente del monitoraggio possono impedire il rilevamento di problemi di integrità dei carichi di lavoro o di altre violazioni della conformità dei criteri.

**Definizione dei criteri**: verranno implementati i criteri seguenti:

- Gli strumenti di governance devono convalidare che tutti gli asset correlati alle applicazioni cruciali o ai dati protetti siano inclusi nel monitoraggio per l'esaurimento e l'ottimizzazione delle risorse.
- Gli strumenti di governance devono convalidare che venga raccolto il livello appropriato di dati di registrazione per tutti i dati protetti o le applicazioni cruciali.

**Opzioni di progettazione potenziali**: [Monitoraggio di Azure](/azure/azure-monitor/overview) è il servizio di monitoraggio predefinito nella piattaforma Azure ed è possibile applicare un monitoraggio coerente tramite l'uso di [Azure Blueprints](/azure/governance/blueprints/) durante la distribuzione delle risorse.

## <a name="disaster-recovery"></a>Ripristino di emergenza

**Rischio tecnico**: errori delle risorse, eliminazioni o danneggiamenti possono causare interruzioni di applicazioni o servizi cruciali e la perdita di dati sensibili.

**Definizione dei criteri**: è necessario implementare soluzioni di backup e ripristino per tutte le applicazioni cruciali e tutti i dati protetti per ridurre al minimo l'impatto aziendale di interruzioni o errori di sistema.

**Opzioni di progettazione potenziali**: il servizio [Azure Site Recovery] offre funzionalità di backup, ripristino e replica progettate per ridurre al minimo la durata di un'interruzione in scenari di continuità aziendale e ripristino di emergenza (BCDR).

## <a name="next-steps"></a>Passaggi successivi

Usare gli esempi citati in questo articolo come punto di partenza per elaborare criteri applicabili a rischi aziendali specifici, in linea con i piani di adozione del cloud.

Per iniziare a sviluppare le definizioni dei criteri personalizzate correlate alla coerenza delle risorse, scaricare il [modello di coerenza delle risorse](template.md).

Per accelerare l'adozione di questa disciplina, scegliere il [percorso di governance di utilità pratica](../journeys/overview.md) più adatto al proprio ambiente. Modificare quindi la progettazione per incorporare le decisioni relative a criteri aziendali specifici.

> [!div class="nextstepaction"]
> [Percorsi di governance di utilità pratica](../journeys/overview.md)
