---
title: 'CAF: Strumenti per la baseline di sicurezza in Azure'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Spiegazione degli strumenti in grado di facilitare il miglioramento della baseline di sicurezza in Azure
author: BrianBlanchard
ms.openlocfilehash: b316626c8ad717514f7f592abefa0f33a92afdca
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901591"
---
# <a name="security-baseline-tools-in-azure"></a>Strumenti per la baseline di sicurezza in Azure

La [baseline di sicurezza](overview.md) è una delle [cinque discipline della governance del cloud](../governance-disciplines.md). Questa disciplina è incentrata sulle modalità di definizione dei criteri per proteggere rete, risorse e soprattutto dati presenti in una soluzione del provider di servizi cloud. All'interno delle cinque discipline della governance del cloud, la baseline di sicurezza include la classificazione del digital estate e dei dati, oltre alla documentazione relativa a rischi, tolleranza aziendale e strategie di mitigazione associate alla sicurezza dei dati, delle risorse e della rete. Dal punto di vista tecnico, include anche il coinvolgimento nelle decisioni relative a [crittografia](../../decision-guides/encryption/overview.md), [requisiti di rete](../../decision-guides/software-defined-network/overview.md), [strategie di identità ibride](../../decision-guides/identity/overview.md) e strumenti di [automazione dell'applicazione](../../decision-guides/policy-enforcement/overview.md) di criteri di sicurezza usati in tutti i [gruppi di risorse](../../decision-guides/resource-consistency/overview.md).

Di seguito è riportato un elenco di strumenti di Azure che possono aiutare a sviluppare i criteri e i processi che supportano la baseline di sicurezza.

|                                                            | [Portale di Azure](https://azure.microsoft.com/features/azure-portal/) / [Resource Manager](/azure/azure-resource-manager/resource-group-overview)  | [Insieme di credenziali chiave Azure](/azure/key-vault)  | [Azure AD](/azure/active-directory/fundamentals/active-directory-whatis) | [Criteri di Azure](/azure/governance/policy/overview) | [Centro sicurezza di Azure](/azure/security-center/security-center-intro) | [Monitoraggio di Azure](/azure/azure-monitor/overview) |
|------------------------------------------------------------|---------------------------------|-----------------|----------|--------------|-----------------------|---------------|
| Applicazione di controlli di accesso alle risorse e alla creazione di risorse   | Sì                             | No               | Sì      | No            | No                     | No             |
| Protezione delle reti virtuali                                    | Sì                             | No               | No        | Sì          | No                     | No             |
| Crittografia delle unità virtuali                                     | No                               | Sì             | No        | No            | No                     | No             |
| Crittografia delle risorse di archiviazione e dei database PaaS                         | No                               | Sì             | No        | No            | No                     | No             |
| Gestione dei servizi di gestione delle identità ibrida                            | No                               | No               | Sì      | No            | No                     | No             |
| Limitazione dei tipi di risorsa consentiti                         | No                               | No               | No        | Sì          | No                     | No             |
| Applicazione di restrizioni a livello di area geografica                          | No                               | No               | No        | Sì          | No                     | No             |
| Monitoraggio dell'integrità della sicurezza di reti e risorse          | No                               | No               | No        | No            | Sì                   | Sì           |
| Rilevamento di attività dannose                                  | No                               | No               | No        | No            | Sì                   | Sì           |
| Rilevamento preventivo delle vulnerabilità                        | No                               | No               | No        | No            | Sì                   | No             |
| Configurazione del backup e del ripristino di emergenza                     | Sì                             | No               | No        | No            | No                     | No             |

Per un elenco completo di strumenti e servizi per la sicurezza di Azure, vedere [Servizi e tecnologie per la sicurezza disponibili in Azure](/azure/security/azure-security-services-technologies).

È anche estremamente comune per i clienti usare strumenti di terze parti per facilitare le attività di baseline di sicurezza. Per altre informazioni, vedere l'articolo [Integrare soluzioni di sicurezza nel Centro sicurezza di Azure](/azure/security-center/security-center-partner-integration).

Oltre a strumenti di sicurezza, [Microsoft Trust Center](https://www.microsoft.com/trustcenter/guidance/risk-assessment) contiene ampio materiale sussidiario, report e documentazione correlata con cui è possibile eseguire valutazioni dei rischi nell'ambito del processo di pianificazione della migrazione.
