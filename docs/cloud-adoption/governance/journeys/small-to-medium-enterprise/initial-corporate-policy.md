---
title: 'CAF: Piccole e medie imprese - Criteri aziendali iniziali per la strategia di governance'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: Piccole e medie imprese - Criteri aziendali iniziali per la strategia di governance
author: BrianBlanchard
ms.openlocfilehash: 4f49d0aae2c6ab5a5c8feba669cbbb904af2773b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901360"
---
# <a name="small-to-medium-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a>Piccole e medie imprese: Criteri aziendali iniziali per la strategia di governance

I criteri aziendali seguenti definiscono la posizione iniziale di governance, ovvero il punto iniziale per questo percorso. L'articolo definisce i rischi in fase iniziale, le istruzioni iniziali sui criteri e i processi anticipati per applicare le istruzioni dei criteri.

> [!NOTE]
>I criteri aziendali non costituiscono un documento tecnico, ma guidano molte decisioni tecniche. La governance MVP descritta nella [panoramica](./overview.md) deriva in definitiva da questo criterio. Prima di implementare una governance MVP, l'organizzazione dovrebbe sviluppare un criterio aziendale in base ai propri obiettivi e rischi aziendali.

## <a name="cloud-governance-team"></a>Team di Governance cloud

In questo esempio pratico, il team di Governance cloud comprende due amministratori di sistema che hanno riconosciuto la necessità di governance. Nei prossimi mesi, erediteranno il processo di pulizia della governance della presenza cloud della società, guadagnandosi il titolo di Custodi del cloud. Nelle successive evoluzioni, questo titolo verrà probabilmente modificato.

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a>Indicatori sulla tolleranza

L'attuale tolleranza ai rischi è elevata e il desiderio di investire nella governance cloud è basso. Di conseguenza, gli indicatori di tolleranza agiscono come un sistema di avviso preventivo per attivare più investimenti di tempo ed energia. Se e quando si osservano gli indicatori seguenti, sarebbe necessario evolvere la strategia di governance.

- Gestione costi: La scala di distribuzione supera le 100 risorse nel cloud oppure la spesa mensile supera i 1.000 USD al mese.
- Baseline di sicurezza: Inclusione dei dati protetti nei piani di adozione definiti per il cloud.
- Coerenza delle risorse: Inclusione di tutte le applicazioni di importanza cruciale nei piani di adozione definiti per il cloud.

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a>Passaggi successivi

Questo criterio aziendale prepara il team di Governance cloud a implementare la governance MVP, che verrà usata come base per l'adozione. Il passaggio successivo consiste nell'implementazione di tale MVP.

> [!div class="nextstepaction"]
> [Spiegazione delle procedure consigliate](./best-practice-explained.md)