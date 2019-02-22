---
title: 'CAF: Criteri relativi alle baseline di sicurezza native del cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Criteri relativi alle baseline di sicurezza native del cloud
author: BrianBlanchard
ms.openlocfilehash: fc161009f6ce7aa2b775fe6b3b53ff1e1d62a848
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901303"
---
# <a name="cloud-native-security-baseline-policy"></a>Criteri relativi alle baseline di sicurezza native del cloud

[Baseline di sicurezza](overview.md) è una delle [cinque discipline della governance del cloud](../governance-disciplines.md). Questa disciplina è incentrata sugli argomenti di sicurezza generale, inclusi la protezione di rete, le risorse digitali, i dati e così via. Come illustrato nella [Guida alla revisione di criteri](../policy-compliance/what-is-a-cloud-policy-review.md), il CAF include tre livelli di **criteri di esempio**: nativo del cloud, Enterprise e conforme ai principi di progettazione del cloud per ciascuna delle cinque discipline. Questo articolo illustra i criteri di esempio nativi del cloud relativi alla disciplina Baseline di sicurezza.

> [!NOTE]
> Microsoft non intende dettare criteri aziendali o IT. Questo articolo è pensato per agevolare la preparazione di una revisione dei criteri aziendali. Si presuppone che questo criterio di esempio venga esteso, convalidato e testato in base ai criteri aziendali prima di provare a usarlo. Si sconsiglia qualsiasi uso di questo criterio di esempio nello stato in cui si trova.

## <a name="policy-alignment"></a>Allineamento dei criteri

Questo criterio di esempio sintetizza uno scenario nativo del cloud, ciò significa che gli strumenti e le piattaforme forniti da Azure possono bastare per ridurre i rischi aziendali relativi a una distribuzione. In questo scenario, si presume che una semplice configurazione dell'impostazione predefinita dei servizi di Azure offra una protezione sufficiente delle risorse.

## <a name="cloud-security-and-compliance"></a>Conformità e sicurezza del cloud

Azure integra la sicurezza in ogni suo aspetto, offrendo vantaggi esclusivi che derivano da una intelligence globale per la sicurezza, controlli relativi ai clienti e un'infrastruttura dotata di protezione avanzata. Questa combinazione avanzata contribuisce alla protezione delle applicazioni e dei dati e al supporto del lavoro richiesto per la conformità e offre una sicurezza a costi contenuti per organizzazioni di qualsiasi dimensione. Questo approccio consente di creare una solida posizione di partenza per qualsiasi criterio di sicurezza, ma non nega la necessità di pratiche di sicurezza ugualmente elevate relative ai servizi di protezione in uso.

### <a name="built-in-security-controls"></a>Criteri di sicurezza integrati

È difficile mantenere un'infrastruttura di protezione solida quando i controlli relativi alla sicurezza non sono intuitivi e devono essere configurati separatamente. Azure offre criteri di sicurezza integrati per un'ampia gamma di servizi che consentono di proteggere rapidamente i dati e i carichi di lavoro, oltre a gestire i rischi in ambienti ibridi. Anche le soluzioni partner integrate agevolano la transizione delle protezioni esistenti alla piattaforma cloud.

### <a name="cloud-native-identity-policies"></a>Criteri di identità nativa del cloud

L'identità è percepita come il nuovo livello limite per la sicurezza, sostituendo in questo ruolo la prospettiva tradizionale incentrata sulla rete. I perimetri di rete sono diventati sempre più permeabili e la difesa perimetrale non è più in grado di garantire la stessa efficacia offerta prima dell'evoluzione delle politiche aziendali Bring your own device (BYOD) e delle applicazioni cloud. La gestione delle identità di Azure e il controllo degli accessi garantiscono un accesso facile e sicuro a tutte le applicazioni.

Un criterio di esempio nativo del cloud relativo all'identità tra i cloud e le directory locali potrebbe comprendere i requisiti seguenti:

* Accesso autorizzato alle risorse tramite il controllo degli accessi in base ai ruoli (RBAC), l'autenticazione a più fattori (MFA) e l'accesso single sign-on (SSO)
* Compromissione sospetta dell'attuazione rapida delle identità utente
* Just-in-time (JIT), l'accesso concesso in base alle singole attività per limitare l'esposizione delle credenziali di amministratore aventi privilegi eccessivi
* Accesso ai criteri in più ambienti tramite Azure Active Directory e identità utente estese

Sebbene sia importante capire le [Baseline sull'identità](../identity-baseline/overview.md) nel contesto delle Baseline sulla sicurezza, è bene sottolineare che le [cinque discipline della governance del cloud](../overview.md) considerano le [Baseline sull'identità](../identity-baseline/overview.md) come una disciplina a sé, che non fa parte della Baseline sulla sicurezza.

### <a name="network-access-policies"></a>Criteri di accesso alla rete

Il controllo della rete comprende la configurazione, la gestione e la protezione degli elementi della rete, come la rete virtuale, il bilanciamento del carico, il DNS e i gateway. Questi controlli sono un mezzo di comunicazione e interoperabilità dei servizi. Azure dispone di una infrastruttura di rete solida e protetta per supportare i requisiti di connettività di applicazioni e servizi. La connettività di rete è possibile tra le risorse residenti in Azure, le risorse locali e quelle ospitate in Azure, nonché da e verso Internet e Azure.

I criteri nativi del cloud per i controlli di rete possono comprendere requisiti simili a quanto segue:

* Le connessioni ibride a risorse locali, anche se tecnicamente possibile in Azure, potrebbero non essere consentite in un criterio nativo del cloud. Se dovesse essere necessario usare una connessione ibrida, si consiglia di fare riferimento a un esempio di criteri di protezione aziendale più solido.
* Gli utenti possono stabilire connessioni protette a e all'interno di Azure tramite reti virtuali e gruppi di sicurezza di rete.
* Firewall nativo di Azure per Windows consente di proteggere gli host dal traffico di rete dannoso, limitando l'accesso alla porta. Un esempio valido di questo criterio è la necessità di bloccare (o non abilitare) il traffico direttamente su una macchina virtuale tramite RDP - TCP/UDP porta 3389.
* I servizi, ad esempio Web Application Firewall e Protezione DDoS di Azure, proteggono le applicazioni e assicurano la disponibilità delle macchine virtuali in esecuzione in Azure. Queste funzionalità non devono essere disabilitate o usate impropriamente.

### <a name="data-protection"></a>Protezione dati

Uno degli aspetti fondamentali della protezione dei dati nel cloud consiste nel tenere conto dei possibili stati in cui possono trovarsi i dati e dei controlli disponibili per ogni stato. Per quanto concerne le procedure consigliate per la sicurezza dei dati e la crittografia in Azure, le raccomandazioni verteranno sugli stati dei dati seguenti:

* I controlli di crittografia dei dati sono integrati nei servizi dalle macchine virtuali all'archiviazione e nel database SQL.
* Dal momento che i dati passano da un cloud o da un cliente all'altro, è possibile proteggerli tramite protocolli di crittografia basati sugli standard aziendali.
* Azure Key Vault consente agli utenti di proteggere e controllare le chiavi di crittografia e altre chiavi segrete usate da app e servizi cloud.
* Azure Information Protection consente di classificare, etichettare e proteggere i dati sensibili presenti nelle app.

Anche se queste funzionalità sono integrate in Azure, quanto descritto in precedenza richiede una configurazione specifica e potrebbe comportare costi aggiuntivi. Si consiglia caldamente di allineare ogni funzionalità nativa del cloud con una [strategia di classificazione dei dati](../policy-compliance/what-is-data-classification.md).

### <a name="security-monitoring"></a>Monitoraggio della protezione

Il monitoraggio della sicurezza è una strategia attiva per il controllo delle risorse che permette di identificare i sistemi non conformi agli standard o alle procedure consigliate dell'organizzazione. Il Centro sicurezza di Azure offre Baseline di sicurezza e la protezione avanzata dalle minacce per carichi di lavoro cloud ibridi. Grazie al Centro sicurezza di Azure è possibile applicare criteri di sicurezza ai flussi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi, tra cui:

* Visualizzazione unificata della sicurezza in tutti i carichi di lavoro locali e sul cloud tramite il Centro sicurezza di Azure
* Valutazioni sulla sicurezza e monitoraggio continuo per garantire la conformità e correggere eventuali vulnerabilità
* Strumenti interattivi e intelligence per le minacce contestuali per semplificare l'investigazione
* Integrazione e registrazione estese alle informazioni sulla sicurezza esistenti

### <a name="extending-cloud-native-policies"></a>Estensione dei criteri nativi del cloud

L'uso del cloud potrebbe ridurre alcuni problemi legati alla protezione. Microsoft garantisce la sicurezza fisica dei data center di Azure e aiuta a proteggere la piattaforma cloud da minacce infrastrutturali, ad esempio un attacco DDoS. Dato che Microsoft dispone di migliaia di esperti che lavorano quotidianamente sulla sicurezza informatica, può fare affidamento su notevoli risorse per prevenire attacchi informatici. In effetti, anche se in passato le organizzazioni si preoccupavano della sicurezza nel cloud, oggi la maggior parte è cosciente del fatto che fornitori, come Microsoft, investono in personale e infrastrutture specializzate. Ecco perché il cloud è effettivamente più sicuro rispetto alla maggior parte dei data center locali.

Sebbene lo stesso investimento venga effettuato per le baseline di sicurezza native del cloud, si consiglia di estendere i criteri nativi del cloud per impostazione predefinita a ogni criterio di baseline di sicurezza. Di seguito sono riportati alcuni esempi dei criteri estesi da prendere in considerazione, anche in un ambiente nativo del cloud:

* **Proteggere le macchine virtuali**. La sicurezza deve essere una delle principali priorità di ogni organizzazione e per eseguirla in modo efficace sono necessarie diverse operazioni. Bisogna valutare lo stato della sicurezza, della protezione contro potenziali minacce, oltre a rilevare e rispondere rapidamente alle minacce che si verificano.
* **Proteggere i contenuti delle macchine virtuali**. Configurare backup automatici a intervalli regolari è fondamentale per proteggere le macchine dagli errori dell'utente. Tuttavia questo non basta: è anche necessario assicurarsi che i backup siano protetti dagli attacchi informatici e siano disponibili al momento opportuno.
* **Monitorare le macchine virtuali e le applicazioni**. Questo modello include numerose attività, tra cui ottenere informazioni sull'integrità delle macchine virtuali, comprendere in che modo interagiscono e definire i modi per monitorare le applicazioni in esecuzione su queste macchine virtuali. Tutte queste attività sono fondamentali per mantenere le applicazioni in esecuzione anche di notte.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato i criteri di esempio delle baseline di sicurezza per le soluzioni native del cloud, tornare alla [guida alla revisione di criteri](../policy-compliance/what-is-a-cloud-policy-review.md) per iniziare a creare i propri criteri all'adozione del cloud, basandosi sull'esempio proposto.

> [!div class="nextstepaction"]
> [Creare criteri tramite la Guida alla revisione dei criteri](../policy-compliance/what-is-a-cloud-policy-review.md)
