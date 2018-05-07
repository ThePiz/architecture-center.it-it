---
title: Albero delle decisioni per i servizi di calcolo di Azure
description: Diagramma di flusso per la selezione di un servizio di calcolo
author: MikeWasson
ms.date: 04/21/2018
ms.openlocfilehash: e601dcb653ed1809ea3f9bbda8db8b40efb460a5
ms.sourcegitcommit: 3846a0ab2b2b2552202a3c9c21af0097a145ffc6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2018
---
# <a name="decision-tree-for-azure-compute-services"></a>Albero delle decisioni per i servizi di calcolo di Azure

Azure offre una serie di modi per ospitare il codice dell'applicazione. Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione. Il diagramma di flusso seguente consente di scegliere un servizio di calcolo per l'applicazione. Il diagramma di flusso illustra un set di criteri decisionali chiave per ottenere delle indicazioni. 

**Considerare questo diagramma di flusso come un punto di partenza.** Dato che ogni applicazione presenta requisiti specifici, usare le indicazioni come punto di partenza e quindi eseguire una valutazione più dettagliata, esaminando aspetti come i seguenti:
 
- Set di funzionalità
- [Limiti del servizio](/azure/azure-subscription-service-limits)
- [Costi](https://azure.microsoft.com/pricing/)
- [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/)
- [Disponibilità internazionale](https://azure.microsoft.com/global-infrastructure/services/)
- Ecosistema e competenze del team dello sviluppatore
- [Tabelle di confronto tra i servizi di calcolo](./compute-comparison.md)

Se l'applicazione è costituita da più carichi di lavoro, valutare ogni carico di lavoro separatamente. Una soluzione completa può includere due o più servizi di calcolo.

![](../images/compute-decision-tree.svg)

