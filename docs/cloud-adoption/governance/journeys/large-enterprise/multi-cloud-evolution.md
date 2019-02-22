---
title: 'CAF: Grandi imprese - Evoluzione multi-cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Grandi imprese - Evoluzione multi-cloud
author: BrianBlanchard
ms.openlocfilehash: 5ef29aa523c04ff93b2d4f983482f94654a4a039
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901811"
---
# <a name="large-enterprise-multi-cloud-evolution"></a>Grandi imprese: Evoluzione multi-cloud

## <a name="evolution-of-the-narrative"></a>Evoluzione dello scenario

Microsoft è consapevole che i clienti stanno adottando più cloud per scopi specifici. La società fittizia in questo percorso non fa eccezione. Parallelamente al percorso di adozione di Azure, il successo aziendale ha portato all'acquisizione di un'altra azienda, di piccole dimensioni ma complementare. La nuova azienda gestisce tutte le operazioni IT con un provider di servizi cloud diverso.

Questo articolo descrive i cambiamenti introdotti dall'integrazione della nuova organizzazione. Per gli scopi di questo scenario, si presuppone che questa società abbia completato tutte le evoluzioni della governance descritte in questo percorso del cliente.

### <a name="evolution-of-the-current-state"></a>Evoluzione dello stato attuale

Nella fase precedente di questo scenario, la società ha iniziato a implementare controlli e monitoraggio dei costi, dato che le spese per il cloud diventano un componente delle regolari spese operative dell'azienda.

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

- Esiste il rischio che la migrazione nel cloud generi costi aggiuntivi per l'acquisizione.
- Esiste anche il rischio che la governance del nuovo ambiente non sia appropriata oppure che si verifichino violazioni dei criteri.

## <a name="evolution-of-the-policy-statements"></a>Evoluzione delle definizioni dei criteri

Le modifiche ai criteri indicate di seguito contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione.

1. Tutti gli asset in un cloud secondario devono essere monitorati tramite gli strumenti esistenti per la gestione operativa e il monitoraggio della sicurezza.
2. Tutte le unità organizzative devono essere integrate nel provider di identità esistente.
3. Il provider di identità primario deve gestire l'autenticazione per gli asset nel cloud secondario.

## <a name="evolution-of-the-best-practices"></a>Evoluzione delle procedure consigliate

Questa sezione dell'articolo approfondisce la progettazione dell'MVP per la governance in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure. Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove definizioni dei criteri aziendali.

1. Connettere le reti - Operazione eseguita dal team responsabile di sicurezza IT e rete, supportata dalla governance
    1. L'aggiunta di una connessione dal provider MPLS/linea dedicata al nuovo cloud consentirà di integrare le reti. L'aggiunta di tabelle di routing e di configurazioni del firewall consentirà di controllare l'accesso e il traffico tra gli ambienti.
2. Consolidare i provider di identità. A seconda dei carichi di lavoro ospitati nel cloud secondario, esistono diverse opzioni per il consolidamento dei provider di identità. Di seguito sono riportati alcuni esempi:
    1. Per le applicazioni che eseguono l'autenticazione tramite OAuth 2, gli utenti in Active Directory nel cloud secondario possono essere semplicemente replicati nel tenant di Azure AD esistente.
    2. Nel caso opposto, la federazione tra i due provider di identità in locale consentirebbe la replica degli utenti dai nuovi domini di Active Directory in Azure.
3. Aggiungere asset ad Azure Site Recovery
    1. Azure Site Recovery è stato concepito sin dall'inizio come strumento ibrido/multi-cloud.
    2. Potrebbe essere possibile proteggere le macchine virtuali nel cloud secondario con gli stessi processi di Azure Site Recovery usati per proteggere gli asset locali.
4. Aggiungere asset a Gestione costi di Azure
    1. Gestione costi di Azure è stato concepito sin dall'inizio come strumento multi-cloud.
    2. Le macchine virtuali nel cloud secondario potrebbero essere compatibili con Gestione costi di Azure per alcuni provider di servizi cloud. Si potrebbe incorrere in costi aggiuntivi.
5. Aggiungere asset a Monitoraggio di Azure
    1. Monitoraggio di Azure è stato concepito sin dall'inizio come strumento cloud ibrido.
    2. Le macchine virtuali nel cloud secondario potrebbero essere compatibili con gli agenti di Monitoraggio di Azure, permettendo così di includerle in Monitoraggio di Azure per il monitoraggio operativo.
6. Strumenti di applicazione della governance
    1. L'applicazione della governance è specifica del cloud,
    2. mentre non lo sono i criteri aziendali stabiliti nel percorso di governance. Anche se l'implementazione può variare da cloud a cloud, le definizioni dei criteri possono essere applicate al provider secondario.

Con la maggiore adozione di soluzioni multi-cloud, l'evoluzione della progettazione sopra descritta continuerà a maturare.

## <a name="next-steps"></a>Passaggi successivi

Per molte grandi imprese, le discipline della governance del cloud possono rappresentare ostacoli all'adozione. L'articolo successivo propone alcune considerazioni conclusive sull'importanza di fare della governance uno sport di squadra, al fine di garantire un successo a lungo termine nel cloud.

> [!div class="nextstepaction"]
> [Più livelli di governance](./multiple-layers-of-governance.md)
