---
title: Scenari ed esempi per la governance delle sottoscrizioni | Microsoft Docs
description: Vengono forniti esempi di come implementare la governance delle sottoscrizioni di Azure per scenari comuni.
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: e8fbeeb8-d7a1-48af-804f-6fe1a6024bcb
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 4a4b25e6a6e54fd18c8ae21a88cab39143c3bf48
ms.sourcegitcommit: 4ec010846b9b5545c843a32e08293f906e512302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
---
# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Esempi di implementazione di scaffold enterprise di Azure
In questo articolo vengono forniti esempi di come un'azienda può implementare gli elementi consigliati per uno [scaffold enterprise di Azure](subscription-governance.md). Usa una società fittizia denominata Contoso per illustrare le procedure consigliate per scenari comuni.

## <a name="background"></a>Background
Contoso è una società globale che fornisce ai propri clienti soluzioni di supply chain. La gamma delle soluzioni offerte va da un modello SaaS a un pacchetto applicativo distribuito in locale.  La società sviluppa software in tutto il mondo e dispone di importanti centri per lo sviluppo in India, Stati Uniti e Canada.

La parte ISV della società è suddivisa in diverse business unit indipendenti che gestiscono i prodotti di un importante settore. Ogni business unit ha i suoi sviluppatori, responsabili di prodotto e architetti.

La business unit Enterprise Technology Services (ETS) fornisce funzionalità IT centralizzata e gestisce più data center in cui le business unit ospitano le loro applicazioni. Oltre a gestire i data center, l'organizzazione ETS fornisce e gestisce una collaborazione centralizzata (ad esempio e-mail e siti Web) e i servizi di rete/telefonia. Gestisce inoltre i carichi di lavoro rivolti ai clienti per business unit più piccole che non dispongono di personale operativo.

In questo articolo vengono usati gli utenti tipo seguenti:

* Dave è l'amministratore di Azure ETS.
* Alice è Director of Development di Contoso della business unit della supply chain.

Contoso deve creare un'app line-of-business e un'app rivolta ai clienti. Ha deciso di eseguire le app in Azure. Dave legge l'articolo relativo alla [governance normativa delle sottoscrizioni](subscription-governance.md) e ora è pronto a mettere in atto le indicazioni.

## <a name="scenario-1-line-of-business-application"></a>Scenario 1: applicazione line-of-business
Contoso sta creando un sistema per la gestione dei codici sorgente (BitBucket) che possa essere usato dagli sviluppatori in tutto il mondo.  L'applicazione usa l'infrastruttura distribuita come servizio (IaaS) per l'hosting ed è costituita da server Web e server di database. Gli sviluppatori accedono ai server nei loro ambienti di sviluppo, tuttavia non hanno bisogno di accedervi in Azure. Contoso ETS vuole consentire ai proprietari delle applicazioni e ai team di gestire l'applicazione. L'applicazione è disponibile solo mentre si trova nella rete aziendale di Contoso. Dave deve configurare la sottoscrizione per l'applicazione. In futuro, la sottoscrizione ospiterà anche altri software destinati agli sviluppatori.  

### <a name="naming-standards--resource-groups"></a>Standard di denominazione e gruppi di risorse
Dave crea una sottoscrizione per supportare gli strumenti di sviluppo più diffusi nelle business unit. Dave deve creare nomi significativi per la sottoscrizione e i gruppi di risorse (per l'applicazione e le reti). Crea le sottoscrizioni e i gruppi di risorse seguenti:

| Elemento | NOME | DESCRIZIONE |
| --- | --- | --- |
| Sottoscrizione |Produzione di strumenti per gli sviluppatori Contoso ETS |Supporta gli strumenti di sviluppo più diffusi |
| Gruppo di risorse |bitbucket-prod-rg |Contiene il server Web dell'applicazione e il server di database |
| Gruppo di risorse |corenetworks-prod-rg |Contiene le reti virtuali e la connessione gateway da sito a sito |

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
Dopo aver creato la sottoscrizione, Dave desidera assicurarsi che le risorse siano accessibili dal team appropriato e dai proprietari delle applicazioni. Dave riconosce che ogni team ha requisiti diversi. Usa i gruppi che sono stati sincronizzati da Active Directory locale di Contoso ad Azure Active Directory e fornisce il corretto livello di accesso ai team.

Dave assegna i ruoli seguenti per la sottoscrizione:

| Ruolo | Assegnato a | DESCRIZIONE |
| --- | --- | --- |
| [Proprietario](/azure/role-based-access-control/built-in-roles#owner) |ID gestito da Active Directory di Contoso |Questo ID è controllato con accesso Just in Time (JIT) tramite lo strumento di gestione delle identità di Contoso e assicura il totale controllo degli accessi dei proprietari delle sottoscrizioni |
| [Ruolo con autorizzazioni di lettura per la sicurezza](/azure/role-based-access-control/built-in-roles#security-reader) |Reparto di gestione dei rischi e della sicurezza |Questo ruolo consente agli utenti di esaminare il Centro sicurezza di Azure e lo stato delle risorse |
| [Collaboratore di rete](/azure/role-based-access-control/built-in-roles#network-contributor) |Team di rete |Questo ruolo consente team di rete di Contoso gestire la VPN da sito a sito e le reti virtuali |
| *Ruolo personalizzato* |Proprietario dell'applicazione |Dave crea un ruolo che concede la possibilità di modificare le risorse all'interno del gruppo di risorse. Per altre informazioni, vedere [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](/azure/role-based-access-control/custom-roles) |

### <a name="policies"></a>Criteri
Dave dispone dei requisiti seguenti per gestire le risorse nella sottoscrizione:

* Poiché gli strumenti di sviluppo aiutano gli sviluppatori di tutto il mondo, non desidera impedire agli utenti di creare risorse in qualsiasi area. Tuttavia, ha bisogno di sapere dove vengono create le risorse.
* È preoccupato per i costi. Di conseguenza, desidera impedire ai proprietari delle applicazioni di creare macchine virtuali inutilmente costose.  
* Poiché questa applicazione è destinata agli sviluppatori di diverse business unit. desidera aggiungere a ogni risorsa un tag con il proprietario dell'applicazione e la business unit. L'uso di questi tag aiuta ETS nella fatturazione ai team appropriati.

Crea i [criteri di Azure ](/azure/azure-policy/azure-policy-introduction) seguenti:

| Campo | Effetto | DESCRIZIONE |
| --- | --- | --- |
| location |audit |Controlla la creazione delle risorse in qualsiasi area |
| type |deny |Nega la creazione di macchine virtuali serie G |
| tags |deny |Richiede il tag del proprietario dell'applicazione |
| tags |deny |Richiede il tag del centro di costo |
| tags |append |Aggiunge il nome del tag **BusinessUnit** e il valore del tag **ETS** a tutte le risorse |

### <a name="resource-tags"></a>Tag delle risorse
Dave è consapevole che è necessario disporre di informazioni specifiche sulla fattura per identificare il centro di costo per l'implementazione di BitBucket. Inoltre, Dave desidera conoscere tutte le risorse ETS che possiede.

Aggiunge i [tag](/azure/azure-resource-manager/resource-group-using-tags) seguenti ai gruppi di risorse e alle risorse.

| Nome del tag | Valore del tag |
| --- | --- |
| ApplicationOwner |Il nome della persona che gestisce l'applicazione |
| CostCenter |Il centro di costo del gruppo che paga per il consumo di Azure |
| BusinessUnit |**ETS** (la business unit associata alla sottoscrizione) |

### <a name="core-network"></a>Rete core
Il team di gestione dei rischi e della sicurezza delle informazioni di Contoso ETS esamina il piano proposto da Dave per spostare l'applicazione in Azure. Vuole assicurarsi che l'applicazione non sia esposta su Internet.  Dave dispone anche di app per gli sviluppatori che in futuro verranno spostate in Azure. Queste app richiedono interfacce pubbliche.  Per soddisfare questi requisiti, fornisce reti virtuali interne ed esterne e un gruppo di sicurezza di rete per limitare l'accesso.

Crea le risorse seguenti:

| Tipo di risorsa | NOME | DESCRIZIONE |
| --- | --- | --- |
| Rete virtuale |rete virtuale interna |Viene usata con l'applicazione BitBucket ed è connessa tramite ExpressRoute alla rete aziendale di Contoso.  Una subnet (`bitbucket`) fornisce all'applicazione uno spazio di indirizzi IP specifico |
| Rete virtuale |rete virtuale esterna |È disponibile per le applicazioni future che richiedono endpoint pubblici |
| Gruppo di sicurezza di rete |bitbucket-nsg |Garantisce che la superficie di attacco di questo carico di lavoro sia ridotta al minimo consentendo le connessioni solo sulla porta 443 per la subnet in cui risiede l'applicazione (`bitbucket`) |

### <a name="resource-locks"></a>Blocchi risorse
Dave riconosce che la connettività dalla rete aziendale di Contoso alla rete virtuale interna deve essere protetta da qualsiasi script imprevisto o eliminazione accidentale.

Crea il [blocco risorsa](/azure/azure-resource-manager/resource-group-lock-resources) seguente:

| Tipo di blocco | Risorsa | DESCRIZIONE |
| --- | --- | --- |
| **CanNotDelete** |rete virtuale interna |Impedisce agli utenti di eliminare la rete virtuale o le subnet, ma non impedisce l'aggiunta di nuove subnet |

### <a name="azure-automation"></a>Automazione di Azure
Dave non ha nulla da automatizzare per questa applicazione. Anche se ha creato un account di Automazione di Azure, inizialmente non lo userà.

### <a name="azure-security-center"></a>Centro sicurezza di Azure
La gestione dei servizi IT di Contoso deve identificare e gestire rapidamente i rischi. Inoltre è necessario capire quali problemi potrebbero esistere.  

Per soddisfare questi requisiti, Dave abilita il [Centro sicurezza di Azure](/azure/security-center/security-center-intro) e fornisce l'accesso al ruolo con autorizzazioni di lettura per la sicurezza.

## <a name="scenario-2-customer-facing-app"></a>Scenario 2: app destinata ai clienti
I responsabili aziendali della business unit della supply chain hanno identificato diverse opportunità per aumentare il coinvolgimento dei clienti Contoso attraverso l'uso di una carta fedeltà. Il team di Alice deve creare l'applicazione e decide che Azure aumenta la capacità di soddisfare le esigenze aziendali. Alice collabora con Dave di ETS per configurare due sottoscrizioni per sviluppare e far funzionare l'applicazione.

### <a name="azure-subscriptions"></a>Sottoscrizioni Azure
Dave accede al portale aziendale di Azure e vede che il reparto della supply chain esiste già.  Tuttavia, poiché questo progetto è il primo progetto di sviluppo per il team della supply chain in Azure, Dave riconosce la necessità di un nuovo account per il team di sviluppo di Alice.  Crea l'account "R & D" per il team e assegna l'accesso ad Alice. Alice accede tramite il portale di Azure e crea due sottoscrizioni: una per i server di sviluppo e l'altra per i server di produzione.  Segue gli standard di denominazione stabiliti in precedenza durante la creazione di sottoscrizioni seguenti:

| Uso della sottoscrizione | NOME |
| --- | --- |
| Sviluppo. |Contoso SupplyChain ResearchDevelopment LoyaltyCard Development |
| Produzione |Contoso SupplyChain Operations LoyaltyCard Production |

### <a name="policies"></a>Criteri
Dave e Alice discutono dell'applicazione e concludono che è destinata solo ai clienti nell'area dell'America del Nord.  Alice e il suo team pianificano di usare l'ambiente del servizio dell'applicazione di Azure e SQL di Azure per creare l'applicazione. Potrebbero aver bisogno di creare macchine virtuali durante lo sviluppo.  Alice vuole assicurarsi che gli sviluppatori dispongano delle risorse che necessarie per esplorare ed esaminare i problemi senza doversi rivolgere a ETS.

Per la **sottoscrizione di sviluppo**, creano i criteri seguenti:

| Campo | Effetto | DESCRIZIONE |
| --- | --- | --- |
| location |audit |Controlla la creazione delle risorse in qualsiasi area |

Non stabiliscono un limite per il tipo di SKU che un utente può creare in fase di sviluppo e non richiedono tag per i gruppi di risorse o le risorse.

Per la **sottoscrizione di produzione**, creano i criteri seguenti:

| Campo | Effetto | DESCRIZIONE |
| --- | --- | --- |
| location |deny |Impedisce la creazione di risorse all'esterno dei data center degli Stati Uniti |
| tags |deny |Richiede il tag del proprietario dell'applicazione |
| tags |deny |Richiede il tag del reparto |
| tags |append |Aggiunge un tag a ogni gruppo di risorse per indicare l'ambiente di produzione |

Non stabiliscono un limite per il tipo di SKU che un utente può creare nell'ambiente di produzione.

### <a name="resource-tags"></a>Tag delle risorse
Dave capisce che è necessario disporre di informazioni specifiche per identificare i gruppi aziendali corretti per la fatturazione e la proprietà. Definisce i tag delle risorse per i gruppi di risorse e le risorse.

| Nome del tag | Valore del tag |
| --- | --- |
| ApplicationOwner |Il nome della persona che gestisce l'applicazione |
| department |Il centro di costo del gruppo che paga per il consumo di Azure |
| EnvironmentType |**Produzione** (anche se la sottoscrizione include **produzione** nel nome, l'inclusione di questo tag consente di identificarla facilmente quando si esaminano le risorse nel portale o nella fattura) |

### <a name="core-networks"></a>Reti core
Il team di gestione dei rischi e della sicurezza delle informazioni di Contoso ETS esamina il piano proposto da Dave per spostare l'applicazione in Azure. Desidera assicurarsi che l'applicazione Carta fedeltà sia correttamente isolata e protetta in una rete perimetrale.  Per soddisfare questo requisito, Dave e Alice creano una rete virtuale esterna e un gruppo di sicurezza di rete per isolare l'applicazione Carta fedeltà dalla rete aziendale Contoso.  

Per la **sottoscrizione di sviluppo**, creano:

| Tipo di risorsa | NOME | DESCRIZIONE |
| --- | --- | --- |
| Rete virtuale |rete virtuale interna |È destinata all'ambiente di sviluppo della carta fedeltà di Contoso ed è connessa tramite ExpressRoute alla rete aziendale di Contoso |

Per la **sottoscrizione di produzione**, creano:

| Tipo di risorsa | NOME | DESCRIZIONE |
| --- | --- | --- |
| Rete virtuale |rete virtuale esterna |Ospita l'applicazione Carta fedeltà e non è connessa direttamente a ExpressRoute di Contoso. Il codice viene trasmesso tramite il sistema di codice sorgente direttamente ai servizi PaaS |
| Gruppo di sicurezza di rete |loyaltycard-nsg |Garantisce che la superficie di attacco di questo carico di lavoro sia ridotta al minimo consentendo la comunicazione in entrata solo su TCP 443.  Contoso sta indagando anche attraverso un firewall applicazione Web per protezione aggiuntiva |

### <a name="resource-locks"></a>Blocchi risorse
Dave e Alice si consultano e decidono di aggiungere blocchi di risorse in alcune delle risorse chiave nell'ambiente per impedire l'eliminazione accidentale durante un push del codice fuori controllo.

Creano il blocco seguente:

| Tipo di blocco | Risorsa | DESCRIZIONE |
| --- | --- | --- |
| **CanNotDelete** |rete virtuale esterna |Per impedire agli utenti di eliminare la rete virtuale o le subnet. Il blocco non impedisce l'aggiunta di nuove subnet |

### <a name="azure-automation"></a>Automazione di Azure
Alice e il suo team di sviluppo hanno runbook estesi per gestire l'ambiente per l'applicazione. I runbook consentono l'aggiunta o l'eliminazione di nodi per l'applicazione e altre attività DevOps.

Per usare questi runbook, abilitano [Automazione](/azure/automation/automation-intro).

### <a name="azure-security-center"></a>Centro sicurezza di Azure
La gestione dei servizi IT di Contoso deve identificare e gestire rapidamente i rischi. Inoltre è necessario capire quali problemi potrebbero esistere.  

Per soddisfare questi requisiti, Dave abilita il Centro sicurezza di Azure. Verifica che il Centro di sicurezza di Azure esegua il monitoraggio delle risorse e fornisce l'accesso ai team di sicurezza e DevOps.

## <a name="next-steps"></a>Passaggi successivi
* Per altri suggerimenti, vedere [Best practices for creating Azure Resource Manager templates](/azure/azure-resource-manager/resource-manager-template-best-practices) (Procedure consigliate per la creazione di modelli di Azure Resource Manager).
