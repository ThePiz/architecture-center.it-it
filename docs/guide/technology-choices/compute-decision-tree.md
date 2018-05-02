---
title: Albero delle decisioni per i servizi di calcolo di Azure
description: Diagramma di flusso per la selezione di un servizio di calcolo
author: MikeWasson
ms.date: 04/21/2018
ms.openlocfilehash: f5703f4906ca2ea6f825b383710eb4bd335f5043
ms.sourcegitcommit: d702b4d27e96e7a5a248dc4f2f0e25cf6e82c134
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="decision-tree-for-azure-compute-services"></a>Albero delle decisioni per i servizi di calcolo di Azure

Azure offre una serie di modi per ospitare il codice dell'applicazione. Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione. Il diagramma di flusso seguente consente di scegliere un servizio di calcolo per l'applicazione.
 
![](../images/compute-decision-tree.svg)

Il diagramma di flusso illustra un set di criteri decisionali chiave per ottenere delle indicazioni. Ogni applicazione presenta requisiti specifici, per cui è necessario considerare le indicazioni come punto di partenza. È quindi opportuno eseguire un'analisi più dettagliata, esaminando aspetti quali, ad esempio:
 
- Set di funzionalità
- [Limiti del servizio](/azure/azure-subscription-service-limits)
- [Costii](https://azure.microsoft.com/pricing/)
- [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/)
- [Disponibilità internazionale](https://azure.microsoft.com/global-infrastructure/services/)
- Ecosistema e competenze del team dello sviluppatore
- [Tabelle di confronto tra i servizi di calcolo](./compute-comparison.md)

Se l'applicazione è costituita da più carichi di lavoro, valutare ogni carico di lavoro separatamente. Una soluzione completa può includere due o più servizi di calcolo.

