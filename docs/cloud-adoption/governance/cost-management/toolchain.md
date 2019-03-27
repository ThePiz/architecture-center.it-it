---
title: 'CAF: Strumenti per la gestione dei costi in Azure'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Strumenti per la gestione dei costi in Azure
author: BrianBlanchard
ms.openlocfilehash: 58dfa604863f704fd9b9fbb8d0693447cecdaf84
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246602"
---
# <a name="cost-management-tools-in-azure"></a>Strumenti per la gestione dei costi in Azure

La [Gestione dei costi](overview.md) è una delle [cinque discipline della governance del cloud](../governance-disciplines.md). Questa disciplina è incentrata sui modi per stabilire piani di spesa per il cloud, allocare, monitorare e applicare i budget per il cloud, rilevare costose anomalie e modificare il piano di governance del cloud quando la spesa effettiva non è allineata.

Di seguito è riportato un elenco di strumenti nativi di Azure che possono aiutare a sviluppare i criteri e i processi che supportano la disciplina di governance.

|  | [Portale di Azure](https://azure.microsoft.com/features/azure-portal/)  | [Gestione costi di Azure](/azure/cost-management/overview-cost-mgt)  | [Pacchetto di contenuto per Azure con contratto Enterprise](/power-bi/service-connect-to-azure-enterprise)  | [Criteri di Azure](/azure/governance/policy/overview) |
|---------|---------|---------|---------|---------|
|È necessario un contratto Enterprise?     | No          | Sì (non obbligatorio con [Cloudyn](/azure/cost-management/overview))         | Sì         | No          |
|Controllo del budget     | No          | Sì         | No          | Sì         |
|Monitoraggio della spesa su singola risorsa    | Sì         | Sì         | Sì         | No          |
|Monitoraggio della spesa su più risorse    | No          | Sì        | Sì         | No          |
|Controllo della spesa su singola risorsa     | Sì, dimensionamento manuale         | Sì         | No          | Sì         |
|Imposizione della spesa su più risorse    | No          | Sì         | No          | Sì         |
|Monitoraggio e rilevamento delle tendenze     | Sì, limitato         | Sì        | Sì         | No          |
|Rilevamento delle anomalie di spesa     | No          | Sì        | Sì         | No         |
|Socializzazione deviazioni     | No         | Sì        | Sì        | No         |
