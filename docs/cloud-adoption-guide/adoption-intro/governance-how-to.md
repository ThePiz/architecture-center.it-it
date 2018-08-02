---
title: Guida alla progettazione della governance di Azure
description: Guida alla configurazione dei controlli della governance di Azure per consentire all'utente di distribuire un carico di lavoro semplice
author: petertay
ms.openlocfilehash: 78545400fc0b09262dce3d0e577442443468b2ed
ms.sourcegitcommit: c704d5d51c8f9bbab26465941ddcf267040a8459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39229576"
---
# <a name="azure-governance-design-guide"></a>Guida alla progettazione della governance di Azure

Il destinatario di questa guida alla progettazione è il responsabile dell'*IT centrale* dell'organizzazione. L'*IT centrale* è responsabile della progettazione e dell'implementazione dell'architettura di governance cloud dell'organizzazione. Come si è appreso in [Informazioni sulla governance delle risorse cloud](governance-explainer.md), per governance si intende il processo continuativo di gestione, monitoraggio e controllo dell'uso delle risorse di Azure per soddisfare gli obiettivi e i requisiti dell'organizzazione.

L'obiettivo di questa guida è apprendere il processo di progettazione dell'architettura di governance dell'organizzazione esaminando una serie di ipotetici obiettivi e requisiti di governance. Verrà quindi illustrato come configurare gli strumenti di governance di Azure per conseguire gli obiettivi e soddisfare i requisiti. 

Nella fase di adozione iniziale, l'obiettivo è distribuire un carico di lavoro semplice in Azure. Ne derivano i requisiti seguenti:
* Gestione delle identità per un singolo **proprietario del carico di lavoro** responsabile della distribuzione e della gestione del carico di lavoro semplice. Il proprietario del carico di lavoro necessita di autorizzazioni per creare, leggere, aggiornare ed eliminare risorse, nonché dell'autorizzazione a delegare questi diritti ad altri utenti nel sistema di gestione delle identità.
* Gestire tutte le risorse per il carico di lavoro semplice come un'unica unità di gestione.

## <a name="licensing-azure"></a>Licenze di Azure

Prima di iniziare a progettare il modello di governance, è importante capire come avviene la concessione in licenza di Azure, perché gli account amministrativi associati alla licenza di Azure hanno il massimo livello di accesso a tutte le risorse di Azure. Questi account amministrativi costituiscono la base del modello di governance.  

> [!NOTE]
> Se l'organizzazione ha già un [Contratto Enterprise Microsoft](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) che non include Azure, è possibile aggiungere Azure assumendo un impegno monetario iniziale. Per altre informazioni, vedere [Licenze di Azure per l'azienda](https://azure.microsoft.com/pricing/enterprise-agreement/). 

Quando Azure è stato aggiunto al Contratto Enterprise Microsoft, all'organizzazione è stato chiesto di creare un **account Azure**. Durante il processo di creazione dell'account, sono stati creati un **proprietario dell'account Azure** e un tenant di Azure Active Directory (Azure AD) con un account di **amministratore globale**. Un tenant di Azure AD è un costrutto logico che rappresenta un'istanza sicura e dedicata di Azure AD.

![Account Azure con proprietario dell'account Azure e amministratore globale di Azure AD](../_images/governance-3-0.png)
*Figura 1. Un account Azure con un proprietario dell'account e un amministratore globale di Azure AD.*

## <a name="identity-management"></a>Gestione delle identità

Azure considera attendibile solo [Azure AD](/azure/active-directory) per autenticare gli utenti e autorizzare l'accesso degli utenti alle risorse, quindi Azure AD è il sistema di gestione delle identità. L'amministratore globale di Azure AD ha il livello massimo di autorizzazioni e può eseguire tutte le azioni relative all'identità, incluse la creazione di utenti e l'assegnazione di autorizzazioni. 

Il requisito è la gestione delle identità per un singolo **proprietario del carico di lavoro** responsabile della distribuzione e della gestione del carico di lavoro semplice. Il proprietario del carico di lavoro necessita di autorizzazioni per creare, leggere, aggiornare ed eliminare risorse, nonché dell'autorizzazione a delegare questi diritti ad altri utenti nel sistema di gestione delle identità.

L'amministratore globale di Azure AD creerà l'account del **proprietario del carico di lavoro** per il **proprietario del carico di lavoro**:

![L'amministratore globale di Azure AD crea l'account del proprietario del carico di lavoro](../_images/governance-1-2.png)
*Figura 2. L'amministratore globale di Azure AD crea l'account utente del proprietario del carico di lavoro.*

Non è possibile assegnare autorizzazioni di accesso alle risorse fino a quando questo utente non viene aggiunto a una **sottoscrizione**, quindi questa operazione verrà eseguita nelle prossime due sezioni. 

## <a name="resource-management-scope"></a>Ambito di gestione delle risorse

Con l'aumento del numero di risorse distribuite dall'organizzazione, cresce anche la complessità della governance di tali risorse. Azure implementa una gerarchia di contenitori logica per consentire all'organizzazione di gestire le risorse in gruppi a vari livelli di granularità, noti anche come **ambito**. 

Il livello massimo dell'ambito di gestione delle risorse è il livello **sottoscrizione**. La sottoscrizione viene creata dal **proprietario dell'account** Azure, che stabilisce l'impegno finanziario ed è responsabile del pagamento di tutte le risorse di Azure associate alla sottoscrizione:

![Il proprietario di un account Azure crea una sottoscrizione](../_images/governance-1-3.png)
*Figura 3. Il proprietario dell'account Azure crea una sottoscrizione.*

Quando viene creata la sottoscrizione, il **proprietario dell'account Azure** associa alla sottoscrizione un tenant di Azure AD che viene usato per l'autenticazione e l'autorizzazione degli utenti:

![Il proprietario dell'account Azure associa il tenant di Azure AD alla sottoscrizione](../_images/governance-1-4.png)
*Figura 4. Il proprietario dell'account Azure associa il tenant di Azure AD alla sottoscrizione.*

Si sarà notato che attualmente non c'è alcun utente associato alla sottoscrizione; ciò significa che nessuno è autorizzato a gestire le risorse. In realtà, il **proprietario dell'account** è il proprietario della sottoscrizione ed è autorizzato a eseguire qualsiasi azione su una risorsa della sottoscrizione. In termini pratici, il **proprietario dell'account** sarà tuttavia molto probabilmente un responsabile della divisione finanziaria dell'organizzazione e non si occuperà delle operazioni di creazione, lettura, aggiornamento ed eliminazione delle risorse; tali attività saranno svolte dal **proprietario del carico di lavoro**. È quindi necessario aggiungere il **proprietario del carico di lavoro** alla sottoscrizione e assegnare le autorizzazioni.

Essendo l'unico utente attualmente autorizzato ad aggiungere il **proprietario del carico di lavoro** alla sottoscrizione, il **proprietario dell'account** aggiungerà il **proprietario del carico di lavoro** alla sottoscrizione:

![Il proprietario dell'account Azure aggiunge il **proprietario del carico di lavoro** alla sottoscrizione](../_images/governance-1-5.png)
*Figura 5. Il proprietario dell'account Azure aggiunge il proprietario del carico di lavoro alla sottoscrizione.*

Il **proprietario dell'account** Azure concede le autorizzazioni al **proprietario del carico di lavoro** assegnando un [ruolo di controllo degli accessi in base al ruolo](/azure/role-based-access-control/). Il ruolo di controllo degli accessi in base al ruolo specifica un insieme di autorizzazioni assegnate al **proprietario del carico di lavoro** per un singolo tipo di risorsa o un insieme di tipi di risorsa.

Si noti che in questo esempio il **proprietario dell'account** ha assegnato il ruolo di [proprietario **predefinito**](/azure/role-based-access-control/built-in-roles#owner): 

![Al **proprietario del carico di lavoro** è stato assegnato il ruolo di proprietario predefinito](../_images/governance-1-6.png)
*Figura 6. Al proprietario del carico di lavoro è stato assegnato il ruolo di proprietario predefinito.*

Il ruolo di **proprietario** predefinito concede tutte le autorizzazioni al **proprietario del carico di lavoro** nell'ambito della sottoscrizione. 

> [!IMPORTANT]
> Il **proprietario dell'account** Azure è responsabile dell'impegno finanziario associato alla sottoscrizione, ma il **proprietario del carico di lavoro** ha le stesse autorizzazioni. Il **proprietario dell'account** deve considerare il **proprietario del carico di lavoro** affidabile per la distribuzione delle risorse che rientrano nel budget della sottoscrizione.

Il livello successivo dell'ambito di gestione è il livello **risorsa di gruppo**. Un gruppo di risorse è un contenitore logico per le risorse. Le operazioni a livello di gruppo di risorse si applicano a tutte le risorse di un gruppo. È anche importante notare che le autorizzazioni per ogni utente vengono ereditate dal livello superiore successivo, a meno che non vengano esplicitamente modificate in quell'ambito. 

Per spiegare questo concetto, di seguito è illustrato cosa accade quando il **proprietario del carico di lavoro** crea un gruppo di risorse:

![Il **proprietario del carico di lavoro** crea un gruppo di risorse](../_images/governance-1-7.png)
*Figura 7. Il proprietario del carico di lavoro crea un gruppo di risorse ed eredita il ruolo di proprietario predefinito nell'ambito del gruppo di risorse.*

Il ruolo di **proprietario** predefinito concede tutte le autorizzazioni al **proprietario del carico di lavoro** nell'ambito del gruppo di risorse. Come accennato in precedenza, questo ruolo viene ereditato dal livello di sottoscrizione. Se a questo utente è assegnato un ruolo diverso in questo ambito, questo ruolo sarà applicabile solo a questo ambito.

Il livello più basso dell'ambito di gestione è il livello **risorsa**. Le operazioni a livello di risorsa si applicano solo alla risorsa stessa. Le autorizzazioni a livello di risorsa vengono ereditate dall'ambito del gruppo di risorse. Se ad esempio il **proprietario del carico di lavoro** distribuisce una [rete virtuale](/azure/virtual-network/virtual-networks-overview) nel gruppo di risorse:

![Il **proprietario del carico di lavoro** crea una risorsa](../_images/governance-1-8.png)
*Figura 8. Il proprietario del carico di lavoro crea una risorsa ed eredita il ruolo di proprietario predefinito nell'ambito della risorsa.*

Il **proprietario del carico di lavoro** eredita il ruolo di proprietario nell'ambito della risorsa, ovvero ha tutte le autorizzazioni per la rete virtuale. 

## <a name="summary"></a>Summary

In questo articolo si è appreso quanto segue:

* Azure considera attendibile solo Azure AD per la gestione delle identità.
* Una sottoscrizione ha l'ambito più elevato di gestione delle risorse e ogni sottoscrizione è associata a un tenant di Azure AD. Solo gli utenti del tenant di Azure AD associato possono accedere alle risorse della sottoscrizione.
* Esistono tre livelli di ambito di gestione delle risorse: sottoscrizione, gruppo di risorse e risorsa. Le autorizzazioni vengono assegnate a ogni ambito tramite i ruoli di controllo degli accessi in base al ruolo. I ruoli di controllo degli accessi in base al ruolo vengono ereditati dall'ambito superiore all'ambito inferiore.

## <a name="next-steps"></a>Passaggi successivi

Tornare alla [panoramica della fase di adozione iniziale](overview.md) per apprendere come implementare questo modello di goverance. Selezionare quindi un tipo di carico di lavoro e apprendere come distribuirlo.