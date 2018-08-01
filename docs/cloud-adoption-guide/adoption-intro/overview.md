---
title: 'Adozione di Azure: Adozione di base'
description: Descrive il livello di conoscenza di base richiesto per l'adozione di Azure da parte di un'organizzazione
author: petertay
ms.openlocfilehash: b5a0a4a2c4ed1d06c97774b0eca643a89a5a2110
ms.sourcegitcommit: c704d5d51c8f9bbab26465941ddcf267040a8459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39229168"
---
# <a name="adopting-microsoft-azure-foundational"></a>Adozione di Microsoft Azure: fase iniziale

Per un'organizzazione che non ha mai usato le tecnologie cloud, può essere difficile decidere da dove iniziare il percorso di adozione. L'obiettivo della fase di adozione iniziale è fornire un punto di partenza. Dopo aver completato questa fase, le persone all'interno dell'organizzazione avranno tutte le conoscenze e le competenze necessarie per distribuire le risorse di calcolo per un carico di lavoro semplice in Azure. 

> [!NOTE]
> Questa guida non tratta lo sviluppo di applicazioni. Per altre informazioni sullo sviluppo di applicazioni in Azure, vedere [Guida all'architettura delle applicazioni in Azure](/azure/architecture/guide/).

I destinatari di questa fase della guida sono gli utenti tipo seguenti all'interno dell'organizzazione:

- *Settore finanziario*: proprietario dell'impegno finanziario per Azure, responsabile dello sviluppo di criteri e procedure per tenere traccia dei costi per il consumo delle risorse, inclusi gli aspetti di fatturazione e chargeback.
- *IT centrale*: responsabile della governance delle risorse cloud dell'organizzazione, inclusi gli aspetti di gestione e accesso e di integrità e monitoraggio dei carichi di lavoro.
- *Proprietari dei carichi di lavoro*: tutti i ruoli di sviluppo coinvolti nella distribuzione dei carichi di lavoro in Azure, inclusi sviluppatori, tester e responsabili di compilazione.

## <a name="section-1-azure-basics"></a>Sezione 1: Nozioni fondamentali di Azure

Questa sezione introduttiva è destinata alle persone addette alla divisione *finanziaria* e all'*IT centrale* dell'organizzazione. Questa sezione è incentrata sull'acquisizione di una comprensione di base del [funzionamento di Azure](azure-explainer.md), in vista dell'apprendimento del [concetto di governance cloud](governance-explainer.md). Questo contenuto può essere utile anche per i *proprietari dei carichi di lavoro* dell'organizzazione, in quanto illustra la gestione dell'accesso alle risorse.

## <a name="section-2-governance-design-guide"></a>Sezione 2: Guida alla progettazione della governance

Dopo aver appreso il funzionamento di Azure e le basi della goverance cloud, il primo passo nell'adozione di Azure è comprendere la [gestione dell'accesso alle risorse](azure-resource-access.md) in Azure. Questo articolo descrive i servizi di Azure per inviare richieste di accesso alle risorse e i controlli usati per convalidare tali richieste.

Il passo successivo è comprendere come [progettare un modello di governance](governance-how-to.md) per un singolo team. Questo articolo descrive come configurare i servizi di gestione dell'accesso alle risorse e i controlli appresi in precedenza.

## <a name="section-3-implementing-a-basic-resource-access-management-model"></a>Sezione 3: Implementazione di un modello di base per la gestione dell'accesso alle risorse

Nel passaggio finale del percorso di adozione si apprende come implementare il modello di governance progettato in precedenza. 

Per iniziare, l'organizzazione richiede un account Azure. Se l'organizzazione ha già un [Contratto Enterprise Microsoft](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) che non include Azure, è possibile aggiungere Azure assumendo un impegno monetario iniziale. Per altre informazioni, vedere [Licenze di Azure per l'azienda](https://azure.microsoft.com/pricing/enterprise-agreement/). 

Quando viene creato l'account Azure, è necessario specificare una persona dell'organizzazione che sarà il **proprietario dell'account Azure**. Per impostazione predefinita, viene quindi creato un tenant Azure Active Directory (Azure AD). Il **proprietario dell'account** Azure deve creare l'[account utente](/azure/active-directory/add-users-azure-active-directory) per la persona dell'organizzazione che sarà il **proprietario del carico di lavoro**. 

Il **proprietario dell'account** Azure deve quindi [creare una sottoscrizione](https://docs.microsoft.com/partner-center/create-a-new-subscription) alla quale dovrà essere [associato il tenant Azure AD](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory).

Dopo aver creato la sottoscrizione e aver associato il tenant Azure AD, è possibile [aggiungere il **proprietario del carico di lavoro** alla sottoscrizione con il ruolo di **proprietario** predefinito](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal).

## <a name="section-4-deploy-a-basic-workload-architecture-to-azure"></a>Sezione 4: Distribuire un'architettura di carico di lavoro di base in Azure

I destinatari di questa sezione sono i *proprietari dei carichi di lavoro*. I *proprietari dei carichi di lavoro* definiscono i requisiti di calcolo e di rete per i propri carichi di lavoro, selezionano le risorse corrette per soddisfare tali requisiti e le distribuiscono in Azure. 

Per la fase di adozione iniziale, il proprietario del carico di lavoro può scegliere tra un'applicazione Web di base o una rete virtuale e una macchina virtuale. Per altre informazioni su questi diversi tipi di opzioni di calcolo in Azure, vedere la [panoramica delle opzioni di calcolo di Azure](/azure/architecture/guide/technology-choices/compute-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json).

Indipendentemente dall'opzione di calcolo selezionata, ognuna di queste distribuzioni richiede un **gruppo di risorse**. Il **proprietario del carico di lavoro** deve [creare il gruppo di risorse](/azure/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy). Nell'ambito della distribuzione, il **proprietario del carico di lavoro** specifica un nome per il gruppo di risorse. Questo nome verrà usato nelle sezioni seguenti.

### <a name="basic-web-application-paas"></a>Applicazione Web di base (PaaS)

Per un'applicazione Web di base, selezionare una delle guide introduttive di 5 minuti dalla [documentazione delle app Web](/azure/app-service?toc=/azure/architecture/cloud-adoption-guide/toc.json) e seguire le procedure. 

> [!NOTE]
> Alcune delle guide introduttive distribuiscono un gruppo di risorse per impostazione predefinita. In questo caso, il **proprietario del carico di lavoro** non deve creare esplicitamente un gruppo di risorse. In caso contrario, distribuire l'applicazione Web nel gruppo di risorse creato in precedenza.

Dopo aver distribuito questo carico di lavoro semplice, è possibile ottenere altre informazioni sulle procedure collaudate per la distribuzione di un'[un'applicazione Web di base](/azure/architecture/reference-architectures/app-service-web-app/basic-web-app?toc=/azure/architecture/cloud-adoption-guide/toc.json) in Azure.

### <a name="single-windows-or-linux-vm-iaas"></a>Singola VM Windows o Linux (IaaS)

Per un carico di lavoro semplice che viene eseguito su una macchina virtuale, il primo passo è distribuire una rete virtuale. Tutte le risorse IaaS in Azure, come le macchine virtuali, i servizi di bilanciamento del carico e i gateway, richiedono una rete virtuale. Per altre informazioni sulle [reti virtuali di Azure](/azure/virtual-network/virtual-networks-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json), seguire i passaggi per [distribuire una rete virtuale in Azure tramite il portale](/azure/virtual-network/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json). Quando si specificano le impostazioni per la rete virtuale nel portale di Azure, indicare il nome del gruppo di risorse creato in precedenza.

Il passo successivo è decidere se distribuire una singola VM Windows o Linux. Per una macchina virtuale Windows, seguire i passaggi per [distribuire una macchina virtuale Windows in Azure con il portale](/azure/virtual-machines/windows/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json). Anche in questo caso, quando si specificano le impostazioni per la macchina virtuale nel portale di Azure, indicare il nome del gruppo di risorse creato in precedenza.

Dopo aver seguito la procedura e distribuito la macchina virtuale, è possibile apprendere [procedure consolidate per l'esecuzione di una macchina virtuale Windows in Azure](/azure/architecture/reference-architectures/virtual-machines-windows/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json). Per una macchina virtuale Linux, seguire i passaggi per [distribuire una macchina virtuale Linux in Azure con il portale](/azure/virtual-machines/linux/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json). È anche possibile ottenere altre informazioni su [procedure consolidate per l'esecuzione di una macchina virtuale Linux in Azure](/azure/architecture/reference-architectures/virtual-machines-linux/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json).

## <a name="next-steps"></a>Passaggi successivi

La fase successiva per l'adozione del cloud è la [**fase intermedia**](../intermediate-stage/overview.md). Nella fase intermedia si apprende come estendere la rete locale con l'esecuzione di più carichi di lavoro e modelli di governance per più team.