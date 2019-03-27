---
title: 'CAF: Allineare le guide alla progettazione con i criteri.'
description: Come allineare le guide alla progettazione con i criteri?
author: BrianBlanchard
ms.date: 01/04/2019
ms.openlocfilehash: 77a35597585e5f58967ea79d3c7b0fa17b6ab80e
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241592"
---
<!---
I've established policies. How to help developers adopt these policies?
Draft an architecture design guide.

[Aspirational statement] If you're using Azure, you can use one of ours as a starting point. The choose one of the following 6 as a starting point and mold it to fit your policies.
--->

<!-- markdownlint-disable MD026 -->

# <a name="how-do-you-align-design-guides-with-policy"></a>Come allineare le guide alla progettazione con i criteri?

Dopo aver [definito i criteri del cloud](define-policy.md) in base ai [rischi identificati](understanding-business-risk.md), è necessario generare una guida operativa che si allinei con questi criteri a cui il personale IT e gli sviluppatori devono fare riferimento. Elaborare una guida alla progettazione della governance del cloud consente di specificare scelte strutturali, tecnologiche e di processo specifiche sulla base delle istruzioni dei criteri generati per ognuna delle cinque discipline di governance.

Una guida alla progettazione della governance del cloud deve stabilire le scelte di architettura e gli schemi progettuali per ognuna delle componenti fondamentali dell'infrastruttura delle distribuzioni del cloud che meglio soddisfano i requisiti dei criteri. Assieme a questi è necessario fornire una spiegazione di alto livello della tecnologia, degli strumenti e dei processi che supportano ognuna di queste decisioni di progettazione.

Sebbene l'analisi dei rischi e le istruzioni di criteri possano, in una certa misura, essere agnostici per la piattaforma cloud, la guida alla progettazione deve fornire dettagli di implementazione specifici per la piattaforma IT e di sviluppo. Concentrarsi sull'architettura, le funzioni e le caratteristiche della piattaforma scelta quando si prendono decisioni di progettazione e si fornisce materiale sussidiario.

Anche se le guide alla progettazione del cloud dovrebbero tenere conto di alcuni dettagli tecnici associati a ogni componente dell'infrastruttura, non sono intese come documenti tecnici o specifiche estese. Assicurarsi che le guide indichino tutte le istruzioni di criteri e dichiarino chiaramente le decisioni di progettazione in un formato di facile riconoscimento e riferimento per il personale.

<!-- markdownlint-enable MD033 -->

## <a name="using-the-actionable-governance-journeys"></a>Usare i percorsi di governance di utilità pratica

Se si prevede di usare la piattaforma Azure per l'adozione del cloud, il CAF fornisce [percorsi di governance ](../journeys/overview.md) che illustrano l'approccio incrementale del modello di governance CAF. Questi percorsi narrativi coprono una serie di scenari di adozione comuni, compresi i rischi aziendali, i requisiti di tolleranza e le istruzioni dei criteri che hanno portato alla creazione di un prodotto minimo funzionante (MVP) di governance. Questi percorsi rappresentano una sintesi delle reali esperienze del cliente del processo di adozione del cloud in Azure.

Sebbene ogni adozione del cloud abbia obiettivi, priorità e sfide uniche, questi esempi dovrebbero fornire un modello ottimale per la conversione dei criteri in materiale sussidiario. Selezionare lo scenario più vicino alla propria situazione come punto iniziale in modo da soddisfare le proprie esigenze specifiche in ambito di criteri.

## <a name="next-steps"></a>Passaggi successivi

Servirsi della guida alla progettazione stabilire i processi di adesione ai criteri per garantirne la conformità.

> [!div class="nextstepaction"]
> [Processi di adesione ai criteri](processes.md)