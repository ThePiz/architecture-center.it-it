---
title: 'CAF: Percorso di governance per piccole e medie imprese'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Percorso di governance per piccole e medie imprese
author: BrianBlanchard
ms.openlocfilehash: a3e078845038a12977e7be5affbf22708411069f
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245162"
---
# <a name="small-to-medium-enterprise-governance-journey"></a>Percorso di governance per piccole e medie imprese

## <a name="best-practice-overview"></a>Panoramica delle procedure consigliate

Questo percorso di governance segue le esperienze di una società fittizia attraverso le diverse fasi di evoluzione della governance. Il percorso è basato su percorsi di clienti reali. Le procedure consigliate si basano sui vincoli e sulle esigenze della società fittizia.

Come punto di partenza rapido, questa panoramica definisce un prodotto minimo funzionante (MVP) per la governance basato su procedure consigliate. Vengono inoltre forniti collegamenti ad alcune forme di evoluzione della governance che offrono altre procedure consigliate in base all'emergere di nuovi rischi tecnici o aziendali.

> [!WARNING]
> Questo MVP è solo un punto di partenza, fondato su un insieme di ipotesi. Anche questo insieme minimo di procedure consigliate è basato su criteri aziendali determinati da rischi commerciali e tolleranze di rischio speciali. Per determinare se queste ipotesi si applicano alla propria situazione, vedere lo [scenario più approfondito](./narrative.md) che segue questo articolo.

## <a name="governance-best-practice"></a>Procedura consigliata per la governance

Questa procedura consigliata funge da base di cui può servirsi un'organizzazione per aggiungere in modo rapido e coerente misure di tutela per la governance tra più sottoscrizioni di Azure.

### <a name="resource-organization"></a>Organizzazione delle risorse

Il diagramma seguente mostra la gerarchia dell'MVP per la governance per organizzare le risorse.

![Diagramma di organizzazione delle risorse](../../../_images/governance/resource-organization.png)

Tutte le applicazioni devono essere distribuite nell'area appropriata della gerarchia di gruppi di gestione, sottoscrizioni e gruppi di risorse. Durante la pianificazione della distribuzione, il team di governance del cloud creerà i nodi necessari nella gerarchia per i team responsabili dell'adozione del cloud.  

1. Un gruppo di gestione per ogni tipo di ambiente (ad esempio produzione, sviluppo e test).
2. Una sottoscrizione per ogni "categorizzazione delle applicazioni".
3. Un gruppo di risorse separato per ogni applicazione.
4. È necessario applicare una nomenclatura coerente a ogni livello di questa gerarchia di gruppi.

Ecco un esempio di questo modello in uso:

![Esempio di organizzazione delle risorse per una società midmarket](../../../_images/governance/mid-market-resource-organization.png)

Questi modelli lasciano un certo spazio per la crescita senza complicare inutilmente la gerarchia.

[!INCLUDE [governance-of-resources](../../../../../includes/cloud-adoption/governance/governance-of-resources.md)]

## <a name="governance-evolutions"></a>Evoluzioni della governance

Una volta distribuito l'MVP, è possibile integrare livelli aggiuntivi di governance nell'ambiente. Ecco alcuni modi in cui trasformare l'MVP per soddisfare esigenze aziendali specifiche:

- [Baseline di sicurezza dei dati protetti](./security-baseline-evolution.md)
- [Configurazioni delle risorse per applicazioni cruciali](./resource-consistency-evolution.md)
- [Controlli per Gestione dei costi](./cost-management-evolution.md)
- [Controlli per l'evoluzione multi-cloud](./multi-cloud-evolution.md)

<!-- markdownlint-disable MD026 -->

## <a name="what-does-this-best-practice-do"></a>Qual è lo scopo di questa procedura consigliata?

Nell'MVP vengono definiti strumenti e procedure della disciplina di [accelerazione della distribuzione](../../deployment-acceleration/overview.md) per applicare rapidamente criteri aziendali. In particolare, l'MVP usa Azure Blueprints, Criteri di Azure e gruppi di gestione di Azure per applicare alcuni criteri aziendali di base, secondo quanto definito nello scenario per questa società fittizia. Questi criteri aziendali vengono applicati tramite modelli di Resource Manager e criteri di Azure per stabilire una baseline minima per identità e sicurezza.

![Esempio di MVP per una governance incrementale](../../../_images/governance/governance-mvp.png)

## <a name="evolving-the-best-practice"></a>Evoluzione della procedura consigliata

Con il passare del tempo, questo MVP per la governance verrà usato per adeguare le procedure necessarie. Con il progredire dell'adozione, aumentano i rischi aziendali. Diverse discipline all'interno del modello di governance del framework per l'adozione del cloud evolveranno per mitigare questi rischi. Gli articoli successivi di questa serie illustrano l'evoluzione di criteri aziendali che interessano la società fittizia. Queste evoluzioni avvengono in tre discipline:

- Gestione dei costi, con il ridimensionarsi dell'adozione.
- Baseline di sicurezza, man mano che vengono distribuiti dati protetti.
- Coerenza delle risorse, man mano che il team responsabile delle operazioni IT inizia a supportare carichi di lavoro cruciali.

![Esempio di MVP per una governance incrementale](../../../_images/governance/governance-evolution.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con l'MVP per la governance e un'idea delle evoluzioni della governance da seguire, consultare lo scenario associato per altre informazioni sul contesto.

> [!div class="nextstepaction"]
> [Consultare lo scenario associato](./narrative.md)
