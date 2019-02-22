---
title: "CAF: Guide all'architettura decisionale"
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sulle guide all'architettura decisionale nel Framework di adozione del Cloud.
author: rotycenh
ms.openlocfilehash: b43047b5fc5b636bc84b9f28ec24846730e63672
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901956"
---
# <a name="architectural-decision-guides"></a>Guide all'architettura decisionale

Le guide all'architettura decisionale nel Framework di adozione del Cloud descrivono i modelli e gli schemi che aiutano quando si crea una guida alla progettazione della governance del cloud. Ogni guida decisionale è incentrata su un componente fondamentale dell'infrastruttura delle distribuzioni cloud ed elenca i potenziali schemi o modelli progettati per supportare scenari specifici di distribuzione del cloud.

Quando si inizia a definire la governance cloud per l'organizzazione, i percorsi di governance attuabili forniscono una roadmap di base. Tuttavia, questi percorsi fanno ipotesi sui requisiti e le priorità che potrebbero non riflettere quelle della propria organizzazione.
Tali guide decisionali integrano i percorsi di esempio di governance, fornendo schemi e modelli alternativi che aiutano ad allineare con le proprie esigenze le scelte di progettazione architetturale prese nella guida di progettazione di esempio.

## <a name="design-guidance-categories"></a>Categorie di indicazioni per la progettazione

Ognuna delle categorie seguenti rappresenta una tecnologia di base di tutte le distribuzioni cloud. Per ogni scelta nei percorsi di progettazione di esempio che non corrisponde ai propri requisiti, esaminare le opzioni nella sezione pertinente di seguito per scegliere uno schema o un modello più adatto alle proprie esigenze.

[Sottoscrizioni](./subscriptions/overview.md): pianificare la struttura della sottoscrizione e la struttura dell'account della distribuzione cloud in modo che corrisponda alle proprietà, alla fatturazione e alle capacità di gestione dell'organizzazione.

[Identità](./identity/overview.md): integrare servizi di identità basati sul cloud con le risorse di identità esistenti per supportare il controllo di accesso all'interno dell'ambiente IT.

[Imposizione dei criteri](./policy-enforcement/overview.md): definire e applicare le regole dei criteri dell'organizzazione per le risorse e i carichi di lavoro che si distribuisce nel cloud.

[Coerenza delle risorse](./resource-consistency/overview.md): assicurarsi che la distribuzione e l'organizzazione delle risorse basate su cloud siano allineate per far rispettare i requisiti di gestione delle risorse e dei criteri.

[Tag delle risorse](./resource-tagging/overview.md): organizzare le risorse basate su cloud per supportare i modelli di fatturazione, gli approcci di accounting cloud, la gestione, il controllo di accesso e l'efficienza operativa. Il tag delle risorse richiede uno schema di denominazione e metadati coerente e ben organizzato.

[Reti definite dal software](./software-defined-network/overview.md): distribuire i carichi di lavoro sicuri nel cloud usando una distribuzione rapida e la modifica della capacità di rete virtualizzata. Software defined networks (SDNs) può supportare flussi di lavoro agili, isolare le risorse e integrare i sistemi basati su cloud con l'infrastruttura IT esistente.

[Crittografia](./encryption/overview.md): proteggere i dati sensibili usando la crittografia, un aspetto importante della protezione all'interno di una distribuzione cloud.

[Log e report](./log-and-report/overview.md): monitorare i dati di log generati dalle risorse basate sul cloud. L'analisi dei dati fornisce informazioni dettagliate correlate all'integrità delle operazioni, la manutenzione e lo stato di imposizione dei criteri dei carichi di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come le sottoscrizioni e gli account servono da base di una distribuzione cloud.

> [!div class="nextstepaction"]
> [Progettazione delle sottoscrizioni](subscriptions/overview.md)
