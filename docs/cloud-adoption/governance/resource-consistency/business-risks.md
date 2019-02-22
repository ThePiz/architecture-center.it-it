---
title: 'CAF: Motivazioni e rischi aziendali correlati alla coerenza delle risorse'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Motivazioni e rischi aziendali correlati alla coerenza delle risorse
author: alexbuckgit
ms.openlocfilehash: 19e0d761e4afa3473099bde2edc960c8b9eadb79
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901667"
---
# <a name="resource-consistency-motivations-and-business-risks"></a>Motivazioni e rischi aziendali correlati alla coerenza delle risorse

Questo articolo illustra i motivi per cui i clienti in genere adottano una disciplina di coerenza delle risorse all'interno di una strategia di governance del cloud. L'articolo offre anche alcuni esempi di potenziali rischi aziendali che possono determinare le definizioni dei criteri.

<!-- markdownlint-disable MD026 -->

## <a name="is-resource-consistency-relevant"></a>La coerenza delle risorse è pertinente?

Quando si tratta di distribuire risorse e carichi di lavoro, il cloud offre un'agilità e una flessibilità maggiori rispetto ai tradizionali data center locali. Questi potenziali vantaggi del cloud, tuttavia, vengono compensati dai potenziali svantaggi di gestione che possono compromettere seriamente il successo di un processo di adozione del cloud. Quali asset sono stati distribuiti? Quali sono i team proprietari degli asset? Si dispone di risorse sufficienti per supportare un carico di lavoro? Come si può sapere se i carichi di lavoro sono integri?

La coerenza delle risorse è fondamentale per garantire che le risorse vengano distribuite, aggiornate e configurate in modo coerente e ripetibile e che le interruzioni di servizio vengano ridotte al minimo e risolte nel minor tempo possibile.

La disciplina di coerenza delle risorse si occupa di identificare e attenuare i rischi aziendali correlati gli aspetti operativi della distribuzione del cloud. La coerenza delle risorse include il monitoraggio delle applicazioni, dei carichi di lavoro e delle prestazioni degli asset. Comprende inoltre le attività necessarie per soddisfare le esigenze di scalabilità e correggere e prevenire dinamicamente le violazioni dei contratti di servizio tramite una procedura di correzione automatizzata.

Per supportare le esigenze di coerenza delle risorse, le distribuzioni di test iniziali richiedono solo standard generici di denominazione e assegnazione dei tag. Con l'evoluzione del processo di adozione del cloud e la distribuzione di asset mission-critical più complicati, aumenta rapidamente anche la necessità di investire nella disciplina di coerenza delle risorse.

## <a name="business-risk"></a>Rischio aziendale

La disciplina di coerenza delle risorse tenta di affrontare i principali rischi operativi delle aziende. Collaborare con l'azienda e il personale IT per identificare questi rischi e monitorarne la rilevanza durante la pianificazione e l'implementazione delle distribuzioni cloud.

I rischi variano da un'organizzazione all'altra. I punti seguenti, tuttavia, rappresentano rischi comuni che è possibile usare come punto di partenza per le discussioni all'interno del team di governance del cloud:

- **Costi operativi inutili**. Risorse obsolete o inutilizzate o risorse con un provisioning eccessivo durante i periodi di bassa richiesta aggiungono costi operativi non necessari.
- **Risorse con provisioning insufficiente**. Le risorse sovraccaricate da una domanda superiore rispetto a quella prevista possono provocare un'interruzione delle attività aziendali.
- **Inefficienze di gestione**. In caso di mancanza di una denominazione coerente e di metadati per l'applicazione dei tag, il personale IT può riscontrare difficoltà a trovare le risorse per le attività di gestione o a identificare le informazioni di proprietà e accounting correlate agli asset. Si creano quindi inefficienze di gestione che possono aumentare i costi e rallentare i tempi di risposta IT a un'interruzione del servizio o altri problemi operativi.
- **Interruzione delle attività aziendali**. Le interruzioni di servizio che danno origine a una violazione dei contratti di servizio dell'organizzazione possono comportare per l'azienda una perdita di profitto o altre conseguenze finanziarie.

## <a name="next-steps"></a>Passaggi successivi

Usare il [modello di gestione del cloud](./template.md) per documentare i rischi aziendali che è probabile che vengano introdotti dal piano di adozione del cloud attuale.

Dopo aver stabilito in modo realistico i rischi aziendali, il passaggio successivo consiste nel documentare le metriche e gli indicatori principali della tolleranza ai rischi dell'azienda allo scopo di monitorare tale tolleranza.

> [!div class="nextstepaction"]
> [Indicatori, metriche e tolleranza ai rischi](./metrics-tolerance.md)
