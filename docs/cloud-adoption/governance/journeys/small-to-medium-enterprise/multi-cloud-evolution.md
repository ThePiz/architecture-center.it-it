---
title: 'CAF: Piccole e medie imprese - Evoluzione multi-cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Spiegazione per piccole e medie imprese - Evoluzione multi-cloud
author: BrianBlanchard
ms.openlocfilehash: a5b09c92acc4e165590b5a35827b88b0ca099bed
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902052"
---
# <a name="small-to-medium-enterprise-multi-cloud-evolution"></a>Piccole e medie imprese: Evoluzione di Multi-cloud

Questo articolo approfondisce la presentazione dello scenario aggiungendo controlli per l'adozione multi-cloud.

## <a name="evolution-of-the-narrative"></a>Evoluzione dello scenario

Microsoft è consapevole che i clienti stanno adottando più cloud per scopi specifici. Il cliente fittizio in questo percorso non fa eccezione. Parallelamente al percorso di adozione di Azure, il successo aziendale ha portato all'acquisizione di un'altra azienda, di piccole dimensioni ma complementare. La nuova azienda gestisce tutte le operazioni IT con un provider di servizi cloud diverso.

Questo articolo descrive i cambiamenti introdotti dall'integrazione della nuova organizzazione. Per gli scopi di questo scenario, si presuppone che questa società abbia completato tutte le evoluzioni della governance descritte in questo percorso del cliente.

### <a name="evolution-of-the-current-state"></a>Evoluzione dello stato attuale

Nella fase precedente di questo scenario, la società aveva iniziato a eseguire attivamente il push di applicazioni di produzione nel cloud tramite le pipeline CI/CD.

Da allora, sono avvenuti alcuni cambiamenti che influiranno sulla governance:

- Il controllo delle identità avviene tramite un'istanza locale di Active Directory. La gestione delle identità ibride è facilitata tramite la replica in Azure Active Directory.
- Le operazioni IT o le operazioni nel cloud sono in gran parte gestite da Monitoraggio di Azure e automazioni correlate.
- Il ripristino di emergenza e la continuità aziendale sono controllati tramite istanze di Azure Key Vault.
- Per monitorare le violazioni della sicurezza e gli attacchi viene usato Centro sicurezza di Azure.
- Centro sicurezza di Azure e Monitoraggio di Azure vengono usati entrambi per monitorare la governance del cloud.
- Per automatizzare la conformità ai criteri vengono usati Azure Blueprints, Criteri di Azure e i gruppi di gestione.

### <a name="evolution-of-the-future-state"></a>Evoluzione dello stato futuro

L'obiettivo è integrare l'azienda acquisita nelle procedure operative esistenti ove possibile.

## <a name="evolution-of-tangible-risks"></a>Evoluzione dei rischi tangibili

**Costo di acquisizione dell'azienda**: è previsto che l'acquisizione della nuova azienda generi profitto in circa cinque anni. Considerato il ritorno lento dell'investimento, il consiglio vuole mantenere sotto controlli i costi di acquisizione quanto più possibile. C'è il rischio di un conflitto tra controllo dei costi e integrazione tecnica.

Questo rischio aziendale può tradursi in alcuni rischi tecnici:

- La migrazione cloud potrebbe produrre costi di acquisizione aggiuntivi
- La governance del nuovo ambiente potrebbe essere inappropriata, causando violazioni dei criteri.

## <a name="evolution-of-the-policy-statements"></a>Evoluzione delle definizioni dei criteri

Le modifiche ai criteri indicate di seguito contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione.

1. Tutti gli asset in un cloud secondario devono essere monitorati tramite gli strumenti esistenti per la gestione operativa e il monitoraggio della sicurezza
2. Tutte le unità organizzative devono essere integrate nel provider di identità esistente
3. Il provider di identità primario deve gestire l'autenticazione per gli asset nel cloud secondario

## <a name="evolution-of-the-best-practices"></a>Evoluzione delle procedure consigliate

Questa sezione dell'articolo approfondisce la progettazione dell'MVP per la governance in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure. Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove definizioni dei criteri aziendali.

1. Connettere le reti. Questo passaggio viene eseguito dal team di rete e dal team di sicurezza IT e supportato dal team di governance del cloud. L'aggiunta di una connessione dal provider MPLS/linea dedicata al nuovo cloud consentirà di integrare le reti. L'aggiunta di tabelle di routing e di configurazioni del firewall consentirà di controllare l'accesso e il traffico tra gli ambienti.
2. Consolidare i provider di identità. A seconda dei carichi di lavoro ospitati nel cloud secondario, esistono diverse opzioni per il consolidamento dei provider di identità. Di seguito sono riportati alcuni esempi:
    1. Per le applicazioni che eseguono l'autenticazione tramite OAuth 2, gli utenti in Active Directory nel cloud secondario possono essere semplicemente replicati nel tenant di Azure AD esistente. Questo assicura che tutti gli utenti possano essere autenticati nel tenant.
    2. Da altra parte, la federazione consente alle unità organizzative di confluire in Active Directory locale, quindi nell'istanza di Azure AD.
3. Aggiungere asset ad Azure Site Recovery.
    1. Azure Site Recovery è stato progettato sin dall'inizio come strumento ibrido/multi-cloud.
    2. Potrebbe essere possibile proteggere le macchine virtuali nel cloud secondario con gli stessi processi di Azure Site Recovery usati per proteggere gli asset locali.
4. Aggiungere asset a Gestione costi di Azure
    1. Gestione costi di Azure è stato concepito sin dall'inizio come strumento ibrido/multi-cloud.
    2. Le macchine virtuali nel cloud secondario possono essere compatibili con Gestione costi di Azure per alcuni provider di servizi cloud. Si potrebbe incorrere in costi aggiuntivi.
5. Aggiungere asset a Monitoraggio di Azure.
    1. Monitoraggio di Azure è stato progettato sin dall'inizio come strumento cloud ibrido.
    2. Le macchine virtuali nel cloud secondario potrebbero essere compatibili con gli agenti di Monitoraggio di Azure, permettendo così di includerle in Monitoraggio di Azure per il monitoraggio operativo.
6. Strumenti di applicazione della governance:
    1. L'applicazione della governance è specifica del cloud,
    2. mentre non lo sono i criteri aziendali stabiliti nel percorso di governance. Anche se l'implementazione può variare da cloud a cloud, i criteri possono essere applicati al provider secondario.

Con la maggiore adozione di soluzioni multi-cloud, l'evoluzione della progettazione sopra descritta continuerà a maturare.

## <a name="conclusion"></a>Conclusioni

Questa serie di articoli ha illustrato l'evoluzione delle procedure consigliate di governance in base alle esperienze di questa società fittizia. Iniziando gradualmente, ma sulle giuste basi, la società è stata in grado di muoversi rapidamente, applicando comunque la giusta quantità di governance al momento giusto. L'MVP di per sé non ha protetto il cliente. È invece servito a creare le basi per mitigare il rischio e aggiungere protezioni. Successivamente, sono stati applicati livelli di governance per mitigare i rischi tangibili. Il percorso presentato in questo articolo non sarà perfettamente allineato con le esperienze di tutti i lettori. È infatti da intendere come modello per la governance incrementale. È consigliabile adattare queste procedure consigliate in base ai propri specifici vincoli e requisiti di governance.