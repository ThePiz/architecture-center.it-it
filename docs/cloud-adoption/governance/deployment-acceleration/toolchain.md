---
title: 'CAF: Strumenti di Accelerazione della distribuzione'
description: Strumenti di Accelerazione della distribuzione
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
author: BrianBlanchard
ms.openlocfilehash: cd00f2fa132f5f177ccc12f61be8b5342b71197d
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58247422"
---
# <a name="deployment-acceleration-tools-in-azure"></a>Strumenti di Accelerazione della distribuzione

[Accelerazione della distribuzione](overview.md) è una delle [cinque discipline della governance del cloud](../governance-disciplines.md). Questa disciplina è incentrata sulle modalità di definizione dei criteri per definire la configurazione o la distribuzione di risorse. All'interno delle cinque discipline della governance del cloud, la governance di configurazione include distribuzione, allineamento di configurazione e strategie a disponibilità elevata e ripristino di emergenza. Ciò è possibile tramite le attività manuali o le attività DevOps completamente automatizzate. In entrambi i casi, i criteri rimangono per la maggior parte gli stessi.

È molto probabile che responsabili, sorveglianti e progettisti del cloud con un interesse per la governance, investano molto tempo nella disciplina di Accelerazione della distribuzione, che consente di codificare criteri e requisiti tra le attività di adozione di più cloud. Gli strumenti disponibili in questa toolchain sono importanti per il team di governance cloud e devono costituire una priorità alta nel percorso di apprendimento da parte del team.

Di seguito è riportato un elenco di strumenti di Azure che possono consentire di sviluppare i criteri e i processi che supportano la disciplina di governance.

|  | Criteri di Azure | Gruppi di gestione di Azure | Modelli di Azure Resource Manager | Azure Blueprint | Diagramma delle risorse di Azure | Gestione costi di Azure |
|---------|---------|---------|---------|---------|---------|---------|
|Implementare i criteri aziendali     |Sì |No   |No   |No  | No  |No  |
|Applicare i criteri tra le sottoscrizioni     |Obbligatoria |Sì  |No   |No  | No  |No  |
|Distribuire le risorse definite     |No  |No   |Sì  |No  | No  |No  |
|Creare ambienti completamente conformi      |Obbligatoria |Obbligatoria  |Obbligatoria  |Sì | No  |No  |
|Criteri di controllo      |Sì |No   |No   |No  | No  |No  |
|Eseguire query sulle risorse di Azure      |No  |No   |No   |No  |Sì |No  |
|Report sui costi delle risorse      |No  |No   |No   |No  |No  |Sì |

Gli strumenti aggiuntivi seguenti potrebbero essere necessari per realizzare obiettivi specifici di Accelerazione della distribuzione. Spesso questi strumenti vengono usati al di fuori del team di governance, ma sono comunque considerati un aspetto della disciplina di Accelerazione della distribuzione.

|  |Portale di Azure  |Modelli di Azure Resource Manager  |Criteri di Azure  | Azure DevOps | Backup di Azure | Azure Site Recovery |
|---------|---------|---------|---------|---------|---------|---------|
|Distribuzione manuale (asset singolo)     | Sì | Sì  | No   | Non efficiente | No  | Sì |
|Distribuzione manuale (ambiente completo)     | Non efficiente | Sì | No   | Non efficiente | No  | Sì |
|Distribuzione automatizzata (ambiente completo)     | No   | Sì  | No   | Sì  | No  | Sì |
|Aggiornare configurazione di un asset singolo     | Sì | Sì | Non efficiente | Non efficiente | No  | Sì: durante la replica |
|Aggiornare la configurazione di un ambiente completo     | Non efficiente | Sì | Sì | Sì  | No  | Sì: durante la replica |
|Gestire una deviazione di configurazione     | Non efficiente | Non efficiente | Sì  | Sì  | No  | Sì: durante la replica |
|Creare una pipeline automatizzata per distribuire il codice e configurare gli asset (DevOps)     | No  | No  | No  | Sì | No  | No  |
|Ripristinare i dati durante un'interruzione o una violazione del contratto di servizio     | No  | No  | No  | Sì | Sì | Sì |
|Ripristinare le applicazioni durante un'interruzione o una violazione del contratto di servizio     | No  | No  | No  | Sì | No  | Sì |

A parte gli strumenti Azure nativi indicati in precedenza, è comune per i clienti usare gli strumenti di terze parti per semplificare le distribuzioni di Accelerazione della distribuzione e DevOps.
