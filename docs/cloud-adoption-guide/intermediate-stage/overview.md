---
title: 'Adozione di Azure: livello intermedio'
description: Viene descritto il livello intermedio di conoscenze richiesto per l'adozione di Azure da parte di un'organizzazione
author: petertay
ms.openlocfilehash: 227d9558647ed8076b2832d95e192f2f0c43b9db
ms.sourcegitcommit: 26b04f138a860979aea5d253ba7fecffc654841e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36206362"
---
# <a name="azure-cloud-adoption-guide-intermediate-overview"></a>Guida all'adozione del cloud di Azure: panoramica del livello intermedio

Nella fase di adozione di base sono stati presentati i concetti di base della governance delle risorse di Azure. La fase con livello di base è stata progettata per aiutare a intraprendere il percorso di adozione di Azure, con informazioni dettagliate su come distribuire un carico di lavoro semplice con un singolo team di piccole dimensioni. In realtà, la maggior parte delle organizzazioni di grandi dimensioni ha numerosi team che lavorano a molti diversi carichi di lavoro contemporaneamente. Come si può immaginare, un modello di governance semplice non è sufficiente per gestire scenari di sviluppo e aziendali più complessi.

La fase intermedia dell'adozione di Azure è incentrata sulla progettazione di un modello di governance per più team che lavorano a più nuovi carichi di lavoro di sviluppo di Azure.  

I destinatari di questa fase della guida sono gli utenti tipo seguenti all'interno dell'organizzazione:
- *Settore finanziario*: proprietario dell'impegno finanziario per Azure, responsabile dello sviluppo di criteri e procedure per tenere traccia dei costi per il consumo delle risorse, inclusi gli aspetti di fatturazione e chargeback.
- *Reparto IT centrale*: responsabile che controlla le risorse cloud dell'organizzazione, inclusi gli aspetti di gestione e accesso, e che si occupa di monitoraggio e integrità dei carichi di lavoro.
- *Proprietario dell'infrastruttura condivisa*: ruoli tecnici responsabili della connettività di rete da ambiente locale a cloud.
- *Operazioni di sicurezza*: responsabile dell'implementazione dei criteri di sicurezza necessari per estendere i confini di sicurezza locali in modo da includere Azure. Può essere anche il proprietario dell'infrastruttura di sicurezza in Azure per l'archiviazione dei segreti.
- *Proprietario del carico di lavoro*: responsabile della pubblicazione di un carico di lavoro in Azure. A seconda della struttura dei team di sviluppo dell'organizzazione, potrebbe trattarsi di un responsabile dello sviluppo, un responsabile della gestione dei programmi o un responsabile di compilazione. Parte del processo di pubblicazione può includere la distribuzione delle risorse in Azure.
  - *Collaboratore del carico di lavoro*: responsabile della collaborazione alla pubblicazione di un carico di lavoro in Azure. Può richiedere l'accesso in lettura alle risorse di Azure per il monitoraggio o l'ottimizzazione delle prestazioni. Non necessita dell'autorizzazione per creare, aggiornare o eliminare le risorse.

## <a name="section-1-azure-concepts-for-multiple-workloads-and-multiple-teams"></a>Sezione 1: Concetti di Azure per più carichi di lavoro e più team

Nella fase di adozione di base sono state apprese alcune nozioni di base sui meccanismi interni di Azure e su come vengono create, lette, aggiornate ed eliminate le risorse. Sono state inoltre fornite informazioni sull'identità e sul fatto che Azure consideri attendibile solo Azure Active Directory (AD) per autenticare e autorizzare gli utenti che richiedono l'accesso alle risorse.

Si è anche appreso come configurare gli strumenti di governance di Azure per gestire l'uso delle risorse di Azure all'interno dell'organizzazione. Nella fase di base è stato analizzato come gestire l'accesso di un singolo team alle risorse necessarie per distribuire un carico di lavoro semplice. In realtà, spesso un'organizzazione è costituita da più team che lavorano a più carichi di lavoro contemporaneamente. 

Prima di iniziare, verrà esaminato il significato effettivo del termine **carico di lavoro**. Questo termine viene in genere inteso come un'unità arbitraria di funzionalità, ad esempio un'applicazione o un servizio. È possibile pensare a un carico di lavoro in termini di elementi di codice distribuiti in un server, nonché di altri servizi necessari, ad esempio un database. Questa definizione è utile per un'applicazione o un servizio locale, ma nel cloud è necessario estenderla. 

Nel cloud un carico di lavoro non include solo tutti gli elementi, ma comprende anche le risorse cloud. Le risorse cloud vengono incluse come parte della definizione in base a un concetto noto come **infrastruttura come codice**. Come illustrato nell'articolo sul funzionamento di Azure, le risorse in Azure vengono distribuite da un servizio agente di orchestrazione. Il servizio agente di orchestrazione espone questa funzionalità tramite un'API Web e tale API Web può essere chiamata usando diversi strumenti, ad esempio Powershell, l'interfaccia della riga di comando di Azure e il portale di Azure. Ciò significa che è possibile specificare le risorse in un file leggibile dal computer che può essere archiviato insieme agli elementi di codice associati all'applicazione.

In questo modo, è possibile definire un carico di lavoro in termini di elementi di codice e di risorse cloud necessarie, in modo da **isolare** ulteriormente i carichi di lavoro. I carichi di lavoro possono essere isolati in base al modo in cui sono organizzate le risorse, in base alla topologia di rete o in base ad altri attributi. L'obiettivo dell'isolamento dei carichi di lavoro è quello di associare le risorse specifiche di un carico di lavoro a un team, così che il team possa gestire in modo indipendente tutti gli aspetti di tali risorse. Ciò consente a più team di condividere i servizi di gestione delle risorse in Azure, impedendo l'eliminazione o la modifica non intenzionale delle risorse di altri team.

Questo isolamento rende possibile anche un altro concetto, noto come [DevOps](https://azure.microsoft.com/solutions/devops/). DevOps include le procedure di sviluppo software che comprendono sia lo sviluppo di software che le operazioni IT, con l'aggiunta dell'uso del massimo livello possibile di automazione. Uno dei principi relativi a DevOps è noto come integrazione continua e recapito continuo (CI/CD). L'integrazione continua si riferisce ai processi di compilazione automatizzati eseguiti ogni volta che uno sviluppatore esegue il commit di una modifica del codice, mentre il recapito continuo si riferisce ai processi automatizzati di distribuzione del codice in diversi **ambienti**, ad esempio un **ambiente di sviluppo** per i test o un **ambiente di produzione** per la distribuzione finale.

## <a name="section-2-governance-design-for-multiple-teams-and-multiple-workloads"></a>Sezione 2: Progettazione della governance per più team e più carichi di lavoro

Nella [fase di base](/azure/architecture/cloud-adoption-guide/adoption-intro/overview) della guida all'adozione del cloud Azure è stato presentato il concetto di governance cloud. Si è appreso come progettare un semplice modello di governance per un singolo team che lavora a un unico carico di lavoro. 

Nella fase intermedia, la [guida alla progettazione della governance](governance-design-guide.md) espande i concetti di base aggiungendo più team, più carichi di lavoro e più ambienti. Una volta esaminati gli esempi nel documento, è possibile applicare i principi di progettazione per progettare e implementare il modello di governance della propria organizzazione.

## <a name="section-3-implementing-a-resource-management-model"></a>Sezione 3: Implementazione di un modello di gestione delle risorse

Il modello di governance cloud dell'organizzazione rappresenta l'intersezione tra strumenti di gestione dell'accesso alle risorse di Azure, utenti e regole di gestione dell'accesso definite. Nella guida alla progettazione della governance sono stati appresi diversi modelli per la gestione dell'accesso alle risorse di Azure. Ora verranno esaminati i passaggi necessari per implementare il modello di gestione delle risorse con una sottoscrizione per ognuno degli ambienti, di **infrastruttura condivisa**, **produzione** e **sviluppo**, illustrati nella guida alla progettazione. Sarà presente un **proprietario della sottoscrizione** per tutti e tre gli ambienti. Ogni carico di lavoro sarà isolato in un **gruppo di risorse** con un **proprietario del carico di lavoro** aggiunto con il ruolo di **collaboratore**.

> [!NOTE]
> Leggere [Informazioni sull'accesso alle risorse in Azure][understand-resource-access-in-azure] per altre informazioni sulla relazione tra account e sottoscrizioni di Azure. 

A tale scopo, seguire questa procedura:

1. Creare un [account di Azure](/azure/active-directory/sign-up-organization), se l'organizzazione non ne ha già uno. La persona che effettua l'iscrizione per l'account di Azure diventa l'amministratore account di Azure e i leader dell'organizzazione devono scegliere una persona che assuma questo ruolo. Questo utente sarà responsabile di:
    * Creazione delle sottoscrizioni.
    * Creazione e amministrazione dei tenant di [Azure Active Directory (AD)](/azure/active-directory/active-directory-whatis) in cui viene archiviata l'identità utente per le sottoscrizioni.    
2. Il team di leader dell'organizzazione decide quali utenti sono responsabili di:
    * Gestione dell'identità utente. Per impostazione predefinita, viene creato un [tenant di Azure AD](/azure/active-directory/develop/active-directory-howto-tenant) quando viene creato l'account di Azure dell'organizzazione e l'amministratore account viene aggiunto come [amministratore globale di Azure AD](/azure/active-directory/active-directory-assign-admin-roles-azure-portal#details-about-the-global-administrator-role). L'organizzazione può scegliere un altro utente per gestire l'identità utente [assegnando il ruolo di amministratore globale di Azure AD a tale utente](/azure/active-directory/active-directory-users-assign-role-azure-portal). 
    * Sottoscrizioni, che richiedono che tali utenti:
        * Gestiscano i costi associati all'utilizzo delle risorse nella sottoscrizione.
        * Implementino e gestiscano il modello di autorizzazioni minime per l'accesso alle risorse.
        * Tengano traccia dei limiti dei servizi.
    * Servizi di infrastruttura condivisi (se l'organizzazione decide di usare questo modello), che richiedono che l'utente sia responsabile di:
        * Connettività di rete da ambiente locale ad Azure. 
        * Proprietà della connettività di rete all'interno di Azure tramite il peering reti virtuali.
    * Proprietari dei carichi di lavoro. 
3. L'amministratore globale di Azure AD [crea i nuovi account utente](/azure/active-directory/add-users-azure-active-directory) per:
    * La persona che sarà il **proprietario della sottoscrizione** per ogni sottoscrizione associata a ogni ambiente. Si noti che questo è necessario solo se l'**amministratore del servizio** di sottoscrizione non ha il compito di gestire l'accesso alle risorse per ogni sottoscrizione/ambiente.
    * La persona che sarà l'**utente delle operazioni di rete**.
    * I **proprietari dei carichi di lavoro**.
4. L'amministratore account di Azure crea le tre sottoscrizioni seguenti usando il [portale degli account di Azure](https://account.azure.com):
    * Una sottoscrizione per l'ambiente di **infrastruttura condivisa**.
    * Una sottoscrizione per l'ambiente di **produzione**. 
    * Una sottoscrizione per l'ambiente di **sviluppo**. 
5. L'amministratore account di Azure [aggiunge il proprietario del servizio di sottoscrizione a ogni sottoscrizione](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-admin-for-a-subscription-in-azure-portal).
6. Creare un processo di approvazione per i **proprietari dei carichi di lavoro** per richiedere la creazione dei gruppi di risorse. Il processo di approvazione può essere implementato in molti modi, ad esempio tramite posta elettronica oppure usando uno strumento di gestione dei processi come i [flussi di lavoro di Sharepoint](https://support.office.com/article/introduction-to-sharepoint-workflow-07982276-54e8-4e17-8699-5056eff4d9e3). Il processo di approvazione può seguire questi passaggi:  
    * Il **proprietario del carico di lavoro** prepara una distinta base per le risorse di Azure necessarie nell'ambiente di **sviluppo**, di **produzione** o in entrambi e la invia al **proprietario della sottoscrizione**.
    * Il **proprietario della sottoscrizione** esamina la distinta base e convalida le risorse richieste per garantire che siano appropriate per l'uso previsto, ad esempio verificando che le [dimensioni delle macchine virtuali](/azure/virtual-machines/windows/sizes) richieste siano corrette.
    * Se la richiesta non viene approvata, viene inviata una notifica al **proprietario del carico di lavoro**. Se la richiesta viene approvata, il **proprietario della sottoscrizione** [crea il gruppo di risorse richiesto](/azure/azure-resource-manager/resource-group-portal#manage-resource-groups) seguendo le [convenzioni di denominazione](/azure/architecture/best-practices/naming-conventions) dell'organizzazione, [aggiunge il **proprietario del carico di lavoro**](/azure/role-based-access-control/role-assignments-portal#add-access) con il [**ruolo di collaboratore** ](/azure/role-based-access-control/built-in-roles#contributor)e invia una notifica al **proprietario del carico di lavoro** per comunicare che il gruppo di risorse è stato creato.
7. Creare un processo di approvazione per i proprietari del carico di lavoro per richiedere una connessione peering reti virtuali dal proprietario dell'infrastruttura condivisa. Come con il passaggio precedente, questo processo di approvazione può essere implementato tramite posta elettronica o tramite uno strumento di gestione dei processi.

Ora che il modello di governance è stato implementato, è possibile distribuire i servizi di infrastruttura condivisa.

## <a name="section-4-deploy-shared-infrastructure-services"></a>Sezione 4: Distribuzione dei servizi di infrastruttura condivisa

Ci sono diverse [architetture di riferimento di reti ibride](/azure/architecture/reference-architectures/hybrid-networking/) che l'organizzazione può usare per connettere la rete locale ad Azure. Ognuna di queste architetture di riferimento include una distribuzione che richiede un identificatore della sottoscrizione. Durante la distribuzione, specificare l'identificatore della sottoscrizione per la sottoscrizione associata all'ambiente di **infrastruttura condivisa**. Sarà necessario anche modificare i file di modello per specificare il gruppo di risorse gestito dall'utente delle **operazioni di rete** oppure è possibile usare i gruppi di risorse predefiniti nella distribuzione e aggiungervi l'utente delle **operazioni di rete**  con il ruolo di **collaboratore**.

<!-- links -->
[understand-resource-access-in-azure]: /azure/role-based-access-control/rbac-and-directory-admin-roles