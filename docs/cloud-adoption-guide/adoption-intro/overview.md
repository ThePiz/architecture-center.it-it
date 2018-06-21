---
title: 'Adozione di Azure: Adozione di base'
description: Descrive il livello di base di conoscenza richiesto per l'adozione di Azure da parte di un'organizzazione
author: petertay
ms.openlocfilehash: 3f522d1662849d651423d8022ad152c64692b823
ms.sourcegitcommit: b3d74d8a89b2224fc796ce0e89cea447af43a0d4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35290476"
---
# <a name="adopting-azure-foundational"></a>Adozione di Azure: Adozione di base

L'adozione di Azure è la prima fase di maturità organizzativa per un'azienda. Al termine di questa fase, gli utenti dell'organizzazione sono in grado di distribuire semplici carichi di lavoro in Azure.

L'elenco seguente include le attività per il completamento della fase di adozione di base. L'elenco è progressivo, pertanto è necessario completare le attività nell'ordine indicato. Se un'attività è stata completata, passare all'attività successiva nell'elenco. 

1. Informazioni sugli elementi interni di Azure:
    - **Spiegazione:** [Funzionamento di Azure](azure-explainer.md)
    - **Spiegazione:** [Informazioni sulla governance delle risorse cloud](governance-explainer.md)
2. Informazioni sull'identità digitale aziendale in Azure:
    - **Spiegazione:** [Informazioni su un tenant di Azure Active Directory](tenant-explainer.md)
    - **Procedura:** [Ottenere un tenant di Azure Active Directory](/azure/active-directory/develop/active-directory-howto-tenant?toc=/azure/architecture/cloud-adoption-guide/toc.json)
    - **Indicazioni:** [Indicazioni per la progettazione di un tenant di Azure AD](tenant.md)
    - **Procedura:** [Aggiungere nuovi utenti ad Azure Active Directory](/azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json)    
3. Informazioni sulle sottoscrizioni in Azure:
    - **Spiegazione:** [Informazioni sulla sottoscrizione di Azure](subscription-explainer.md)
    - **Indicazioni:** [Progettazione della sottoscrizione di Azure](subscription.md)
4. Informazioni sulla gestione delle risorse in Azure: 
    - **Spiegazione:** [Informazioni su Azure Resource Manager](resource-manager-explainer.md)
    - **Spiegazione:** [Informazioni sul gruppo di risorse di Azure](resource-group-explainer.md)
    - **Spiegazione:** [Informazioni sull'accesso alle risorse in Azure](/azure/active-directory/active-directory-understanding-resource-access?toc=/azure/architecture/cloud-adoption-guide/toc.json)
    - **Procedura:** [Creare un gruppo di risorse di Azure usando il portale di Azure](/azure/azure-resource-manager/resource-group-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)
    - **Indicazioni:** [Indicazioni per la progettazione di un gruppo di risorse di Azure](resource-group.md)
    - **Indicazioni:** [Convenzioni di denominazione per le risorse di Azure](/azure/architecture/best-practices/naming-conventions?toc=/azure/architecture/cloud-adoption-guide/toc.json)
5. Distribuire un'architettura di Azure di base:
    - Per informazioni sui diversi tipi di opzioni di calcolo di Azure, ad esempio infrastruttura distribuita come servizio (IaaS, Infrastructure as a Service) e piattaforma distribuita come servizio (PaaS, Platform as a Service), vedere la [panoramica delle opzioni di calcolo di Azure](/azure/architecture/guide/technology-choices/compute-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json).
    - Dopo aver compreso i diversi tipi di opzioni di calcolo di Azure, selezionare un'applicazione Web (PaaS) o una macchina virtuale (IaaS) come prima risorsa in Azure:
    - PaaS - Introduzione alla piattaforma come servizio:
        - **Procedura:** [Distribuire un'applicazione Web di base in Azure](/azure/app-service/app-service-web-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json)
        - **Indicazioni:** Procedure comprovate per la distribuzione di una [applicazione Web di base](/azure/architecture/reference-architectures/app-service-web-app/basic-web-app?toc=/azure/architecture/cloud-adoption-guide/toc.json) in Azure
    - IaaS - Introduzione alle reti virtuali:
        - **Spiegazione:** [Rete virtuale di Azure](/azure/virtual-network/virtual-networks-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json)
        - **Procedura:** [Distribuire una rete virtuale in Azure usando il portale](/azure/virtual-network/virtual-networks-create-vnet-arm-pportal?toc=/azure/architecture/cloud-adoption-guide/toc.json)
    - IaaS - Distribuire il carico di lavoro di una singola macchina virtuale (VM) (Windows e Linux):
        - **Procedura:** [Distribuire una VM di Windows in Azure tramite il portale](/azure/virtual-machines/windows/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)
        - **Indicazioni:** [Procedure comprovate per l'esecuzione di una VM di Windows in Azure](/azure/architecture/reference-architectures/virtual-machines-windows/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json)
        - **Procedura:** [Distribuire una VM di Linux in Azure tramite il portale](/azure/virtual-machines/linux/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)
        - **Indicazioni:** [Procedure comprovate per l'esecuzione di una VM di Linux in Azure](/azure/architecture/reference-architectures/virtual-machines-linux/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json)
