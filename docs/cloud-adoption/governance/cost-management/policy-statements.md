---
title: 'CAF: Definizioni dei criteri di esempio per la gestione dei costi'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 1/4/2019
description: Definizioni dei criteri di esempio per la gestione dei costi
author: BrianBlanchard
ms.openlocfilehash: 1c8b94ae5285fa26cdf9760892beaced2487af8b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902059"
---
# <a name="cost-management-sample-policy-statements"></a>Definizioni dei criteri di esempio per la gestione dei costi

Le singole definizioni dei criteri del cloud sono linee guida per affrontare i rischi specifici identificati durante il processo di valutazione dei rischi. Queste definizioni devono fornire un riassunto conciso dei rischi e dei piani per gestirli. Ogni definizione di istruzione deve includere i tipi di informazioni seguenti:

- Rischio aziendale: un riepilogo del rischio a cui sono applicabili questi criteri.
- Definizione dei criteri - Una spiegazione chiara e riepilogativa dei requisiti dei criteri.
- Opzioni di progettazione - Suggerimenti pratici, specifiche o altre linee guida che i team IT e gli sviluppatori possono usare per l'implementazione dei criteri.

Gli esempi seguenti di definizioni dei criteri riguardano alcuni rischi aziendali comuni relativi ai costi e vengono forniti a scopo di riferimento per creare effettive definizioni di criteri in base alle esigenze della propria organizzazione. Questi esempi non devono essere considerati vincolanti. Esistono potenzialmente diverse opzioni per definire criteri che consentano di affrontare qualsiasi tipo di rischio. Lavorare a stretto contatto con i team responsabili delle attività aziendali e dell'IT per identificare le migliori soluzioni di criteri per i rischi correlati ai costi specifici della propria organizzazione.  

## <a name="future-proofing"></a>Investimento solido per il futuro

**Rischio aziendale**: i criteri attuali non giustificano un investimento in una disciplina Gestione costi da parte del team di governance. Tuttavia, si prevede un investimento di questo tipo in futuro.

**Definizione dei criteri**: è consigliabile associare tutti gli asset distribuiti nel cloud a un'unità di fatturazione, applicazione o carico di lavoro, e adottare specifici standard di denominazione. In questo modo si ha la sicurezza di ottenere risultati efficaci dall'impegno che si dedicherà in futuro alla gestione dei costi.

**Opzioni di progettazione**: per informazioni su come realizzare una base solida per il futuro, vedere le discussioni relative alla creazione di un prodotto minimo funzionante (MVP) per la governance nei [percorsi di utilità pratica per la progettazione](../journeys/overview.md) inclusi nella guida CAF.

## <a name="budget-overruns"></a>Superamento del budget

**Rischio aziendale**: la distribuzione in modalità self-service crea un rischio di superamento dei costi.

**Definizione dei criteri**: qualsiasi distribuzione cloud deve essere allocata a un'unità di fatturazione con budget approvato e un meccanismo per i limiti di budget.

**Opzioni di progettazione**: in Azure è possibile controllare il budget con [Gestione costi di Azure](/azure/cost-management/manage-budgets).

## <a name="underutilization"></a>Sottoutilizzo

**Rischio aziendale**: la società ha pagato in anticipo i servizi cloud o ha scelto un piano annuale per un importo specifico. Esiste il rischio che la capacità relativa all'importo concordato non venga sfruttata, con conseguenti perdite sull'investimento.

**Definizione dei criteri**: ogni unità di fatturazione con un budget allocato per il cloud si incontrerà ogni anno per definire i budget, ogni trimestre per apportare eventuali rettifiche e ogni mese per verificare la spesa effettiva rispetto a quella pianificata. Discutere delle deviazioni superiori al 20% con il responsabile dell'unità di fatturazione una volta al mese. Ai fini della tracciabilità, assegnare tutti gli asset a un'unità di fatturazione.

**Opzioni di progettazione**:

- In Azure il controllo della spesa effettiva rispetto a quella pianificata può essere gestito tramite [Gestione costi di Azure](/azure/cost-management/quick-acm-cost-analysis).
- Sono disponibili diverse opzioni per raggruppare le risorse in base all'unità di fatturazione. In Azure è consigliabile scegliere un [modello di coerenza delle risorse](../../decision-guides/resource-consistency/overview.md) in collaborazione con il team di governance e applicarlo a tutti gli asset.

## <a name="overprovisioned-assets"></a>Provisioning eccessivo di asset

**Rischio aziendale**: nei data center locali tradizionali si ha l'abitudine di distribuire asset con più capacità del necessario in previsione di una crescita futura. Le risorse cloud possono essere ridimensionate più rapidamente rispetto alle apparecchiature tradizionali. Inoltre, i prezzi degli asset del cloud variano in base alla capacità tecnica. Esiste il rischio che la prassi tradizionale adottata nei data center locali abbia l'effetto di aumentare artificialmente la spesa relativa al cloud.

**Definizione dei criteri**: qualsiasi asset distribuito nel cloud deve essere registrato in un programma che può monitorarne l'utilizzo e segnalare l'eventuale capacità in eccesso del 50% rispetto all'utilizzo. Gli asset distribuiti nel cloud devono essere raggruppati o contrassegnati in base a una logica per consentire ai membri del team di governance di coinvolgere il proprietario del carico di lavoro nell'ottimizzazione degli asset con provisioning eccessivo.

**Opzioni di progettazione**:

- In Azure è possibile ottenere consigli per l'ottimizzazione tramite [Azure Advisor](/azure/advisor/advisor-cost-recommendations).
- Sono disponibili diverse opzioni per raggruppare le risorse in base all'unità di fatturazione. In Azure è consigliabile scegliere un [modello di coerenza delle risorse](../../decision-guides/resource-consistency/overview.md) in collaborazione con il team di governance e applicarlo a tutti gli asset.

## <a name="overoptimization"></a>Ottimizzazione eccessiva

**Rischio aziendale**: la gestione efficace dei costi presenta in realtà nuovi rischi. L'ottimizzazione della spesa è inversamente proporzionale alle prestazioni del sistema. Tagliando i costi si corre il rischio di ridurre eccessivamente la spesa e di conseguenza peggiorare la qualità delle esperienze degli utenti.

**Definizione dei criteri**: è necessario raggruppare e contrassegnare gli asset che hanno un impatto diretto sulle esperienze dei clienti. Prima di ottimizzare tali asset, il team di governance del cloud deve eseguire un'analisi delle tendenze di utilizzo su un periodo di almeno 90 giorni. Documentare eventuali picchi collegati a eventi o alla variazione stagionale presi in considerazione durante la fase di ottimizzazione degli asset.

**Opzioni di progettazione**:

- In Azure le [funzionalità di Monitoraggio di Azure](/azure/azure-monitor/insights/vminsights-performance) consentono di ottenere informazioni dettagliate per l'analisi dell'utilizzo del sistema.
- Sono disponibili diverse opzioni per raggruppare e contrassegnare le risorse in base ai ruoli. In Azure è consigliabile scegliere un [modello di coerenza delle risorse](../../decision-guides/resource-consistency/overview.md) in collaborazione con il team di governance e applicarlo a tutti gli asset.

## <a name="next-steps"></a>Passaggi successivi

Usare gli esempi citati in questo articolo come punto di partenza per elaborare criteri applicabili a rischi aziendali specifici, in linea con i piani di adozione del cloud.

Per iniziare a sviluppare definizioni dei criteri personalizzate correlate alla gestione dei costi, scaricare il [modello di gestione dei costi](template.md).

Per accelerare l'adozione di questa disciplina, scegliere il [percorso di governance di utilità pratica](../journeys/overview.md) più adatto al proprio ambiente. Modificare quindi la progettazione per incorporare le decisioni relative a criteri aziendali specifici.

> [!div class="nextstepaction"]
> [Percorsi di governance di utilità pratica](../journeys/overview.md)
