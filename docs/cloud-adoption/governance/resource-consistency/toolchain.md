---
title: 'CAF: Strumenti di coerenza delle risorse in Azure'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Strumenti di coerenza delle risorse in Azure
author: BrianBlanchard
ms.openlocfilehash: 68503289f60fbb3682264ff39546ca7b7700cef5
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241582"
---
# <a name="resource-consistency-tools-in-azure"></a>Strumenti di coerenza delle risorse in Azure

La [Coerenza delle risorse](overview.md) è una delle [cinque discipline della governance del cloud](../governance-disciplines.md). Questa disciplina riguarda le modalità di definizione dei criteri di gestione operativa di un ambiente, applicazione o carico di lavoro. Tra le cinque discipline della governance del cloud, la coerenza delle risorse include il monitoraggio dell'applicazione, del carico di lavoro e delle prestazioni degli asset. Riguarda inoltre le attività necessarie per soddisfare le esigenze di scalabilità, correggere e prevenire rapidamente le violazioni ai contratti di servizio tramite una procedura di correzione automatizzata.

Di seguito è riportato un elenco di strumenti di Azure che possono aiutare a sviluppare i criteri e i processi che supportano la disciplina di governance.

|    | [Portale di Azure](https://azure.microsoft.com/features/azure-portal/)  | [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)  | [Azure Blueprint](/azure/governance/blueprints/overview) | [Automazione di Azure](/azure/automation/automation-intro) | [Azure AD](/azure/active-directory/fundamentals/active-directory-whatis) |
|---------|---------|---------|---------|---------|---------|
| Distribuire le risorse                             | Sì | Sì | Sì | Sì | No   |
| Gestire risorse                             | Sì | Sì | Sì | Sì | No   |
| Implementare le risorse tramite modelli             | No   | Sì | No   | Sì | No   |
| Distribuire l'ambiente orchestrato          | No   | No   | Sì | No   | No   |
| Definire gruppi di risorse                       | Sì | Sì | Sì | No   | No   |
| Gestire i proprietari dell'account e i carichi di lavoro           | Sì | Sì | Sì | No   | No   |
| Gestire le risorse di accesso condizionale       | Sì | Sì | Sì | No   | No   |
| Configurare gli utenti per il controllo degli accessi in base al ruolo                         | Sì | No   | No   | No   | Sì |
| Assegnare autorizzazioni e ruoli per le risorse | Sì | Sì | Sì | No   | Sì |
| Definire le dipendenze tra le risorse        | No   | Sì | Sì | No   | No   |
| Applicare il controllo di accesso                         | Sì | Sì | Sì | No   | Sì |
| Valutare la disponibilità e la scalabilità          | No   | No   | No   | Sì | No   |
| Applicare tag alle risorse                      | Sì | Sì | Sì | No   | No   |
| Assegnare regole di Criteri in Azure                    | Sì | Sì | Sì | No   | No   |
| Pianificare le risorse per il ripristino di emergenza         | Sì | Sì | Sì | No   | No   |
| Applicare la correzione automatizzata                  | No   | No   | No   | Sì | No   |
| Gestire la fatturazione                               | Sì | No   | No   | No   | No   |

Oltre a questi strumenti di funzionalità e coerenza delle risorse, è necessario monitorare le risorse implementate per le prestazioni e i problemi di integrità. [Monitoraggio di Azure](/azure/azure-monitor/overview) è l'impostazione predefinita per il monitoraggio e il reporting relativi alle soluzioni in Azure. Monitoraggio di Azure fornisce una serie di funzionalità singole che è possibile usare per monitorare le risorse del cloud. L'elenco seguente illustra le funzionalità che consentono di garantire i requisiti di monitoraggio comuni.

|                                                    | [Portale di Azure](https://azure.microsoft.com/features/azure-portal/) | [Application Insights](/azure/application-insights/app-insights-overview) | [Log Analytics](/azure/azure-monitor/log-query/log-query-overview) | [API REST di Monitoraggio di Azure](/rest/api/monitor/) |
|----------------------------------------------------|--------------|----------------------|---------------|------------------------|
| Dati di telemetria di macchine virtuali log                 | No            | No                    | Sì           | No                      |
| Dati di telemetria di reti virtuali log              | No            | No                    | Sì           | No                      |
| Dati di telemetria di servizi PaaS di log                   | No            | No                    | Sì           | No                      |
| Dati di telemetria per applicazioni log                     | No            | Sì                  | No             | No                      |
| Configurare report e avvisi                       | Sì          | No                    | No             | Sì                    |
| Pianificare report a intervalli regolari o analisi personalizzate        | No            | No                    | No             | No                      |
| Visualizzare e analizzare i dati di prestazioni e log     | Sì          | No                    | No             | No                      |
| Integrare una soluzione di monitoraggio locale o di terze parti     | No            | No                    | No             | Sì                    |

Quando si pianifica la distribuzione, si dovrà considerare dove sono archiviati i dati di registrazione e come si desidera integrare gli strumenti e i processi esistenti nei [servizi di monitoraggio e reporting](../../decision-guides/log-and-report/overview.md) integrati basati su cloud.

> [!NOTE]
> Le organizzazioni usano anche strumenti Azure DevOps di terze parti per monitorare i carichi di lavoro e le risorse. Per altre informazioni, vedere [Integrazioni con gli strumenti per DevOps](https://azure.microsoft.com/products/devops-tool-integrations/).

# <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare, assegnare e gestire le [definizioni dei criteri](/azure/governance/policy/) in Azure.
