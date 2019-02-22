---
title: 'CAF: Azienda di grandi dimensioni - Criteri aziendali iniziali per la strategia di governance'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: Azienda di grandi dimensioni - Criteri aziendali iniziali per la strategia di governance.
author: BrianBlanchard
ms.openlocfilehash: d3bc31f95229a82cbc2f6ac6e684a87107783c07
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902011"
---
# <a name="large-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a>Aziende di grandi dimensioni: Criteri aziendali iniziali per la strategia di governance

I criteri aziendali seguenti definiscono la posizione iniziale di governance, ovvero il punto di partenza per questo percorso. L'articolo definisce i rischi in fase iniziale, le istruzioni iniziali sui criteri, i processi anticipati per applicare le istruzioni dei criteri.

> [!NOTE]
>I criteri aziendali non costituiscono un documento tecnico, ma guidano molte decisioni tecniche. La governance MVP descritta nella [panoramica](./overview.md) deriva in definitiva da questo criterio. Prima di implementare una governance MVP, l'organizzazione dovrebbe sviluppare un criterio aziendale in base ai propri obiettivi e rischi aziendali.

## <a name="cloud-governance-team"></a>Team di Governance cloud

Il CIO ha recentemente tenuto una riunione con il team IT di governance per comprendere la cronologia delle informazioni personali e i criteri di importanza cruciale, oltre a esaminare l'impatto della modifica di tali criteri. Ha anche illustrato il potenziale complessivo del cloud per il settore IT e l'azienda.

Dopo la riunione, due membri del team IT di governance hanno richiesto l'autorizzazione per ricercare e supportare gli sforzi di pianificazione del cloud. Il direttore di governance IT ha supportato questo concetto, riconoscendo l'esigenza di governance e la possibilità per limitare lo shadow IT. Il team di Governance cloud è nato con questa idea. Nei mesi successivi, si erediterà la pulizia di molti errori commessi durante l'esplorazione nel cloud da una prospettiva di governance. Ciò consentirà di guadagnarsi il moniker dei custodi cloud. In evoluzioni successive, questo percorso illustrerà in che modo i relativi ruoli cambiano nel tempo.

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a>Indicatori sulla tolleranza

L'attuale tolleranza ai rischi è elevata e il desiderio di investire nella governance cloud è basso. Di conseguenza, gli indicatori di tolleranza agiscono come un sistema di avviso preventivo per attivare l'investimento di tempo ed energia. Se si osservano gli indicatori seguenti, sarebbe opportuno evolvere la strategia di governance.

- Gestione costi: la scala di distribuzione supera le 1.000 risorse nel cloud oppure la spesa mensile supera i USD 10.000 al mese.
- Baseline di identità: inclusione di applicazioni con requisiti di autenticazione legacy o a più fattori di terze parti (MFA).
- Baseline di sicurezza: inclusione dei dati protetti nei piani di adozione definiti per il cloud.
- Coerenza delle risorse: inclusione di tutte le applicazioni di importanza cruciale nei piani di adozione definiti per il cloud.

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a>Passaggi successivi

Questo criterio aziendale prepara il team di Governance cloud a implementare la governance MVP, che verrà usata come base per l'adozione. Il passaggio successivo consiste nell'implementazione di tale MVP.

> [!div class="nextstepaction"]
> [Best practice explained (Spiegazione delle procedure consigliate)](./best-practice-explained.md)