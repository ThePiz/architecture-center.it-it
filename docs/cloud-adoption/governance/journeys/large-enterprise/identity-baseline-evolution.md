---
title: 'CAF: Grandi imprese - Evoluzione di Baseline di identità'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Grandi imprese - Evoluzione di Baseline di identità
author: BrianBlanchard
ms.openlocfilehash: efda14819bfbc70632dc9bb8b4c6c5aca96004c0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901572"
---
# <a name="large-enterprise-identity-baseline-evolution"></a>Grandi imprese: Evoluzione di Baseline di identità

Questo articolo approfondisce la presentazione dello scenario aggiungendo controlli per la baseline di identità all'MVP per la governance.

## <a name="evolution-of-the-narrative"></a>Evoluzione dello scenario

La motivazione aziendale per la migrazione al cloud dei due data center è stata approvata dal CFO. Durante lo studio di fattibilità tecnica sono stati individuati diversi ostacoli:

- I dati protetti e le applicazioni cruciali rappresentano 25% dei carichi di lavoro nei due data center. Nessuno dei due può essere eliminato finché gli attuali criteri di governance relativi alle informazioni personali e alle applicazioni cruciali non saranno stati modernizzati.
- Il 7% degli asset in quei data center non è compatibile con il cloud. Verranno spostati in un data center alternativo prima della risoluzione del contratto del data center.
- Il 15% degli asset nel data center (750 macchine virtuali) ha una dipendenza da sistemi di autenticazione legacy o di autenticazione a più fattori di terze parti.
- La connessione VPN tra Azure e i data center esistenti non offre velocità di trasmissione dei dati o latenza sufficiente per eseguire la migrazione dell'intero volume degli asset entro i due anni previsti per il ritiro del data center.

I primi due ostacoli vengono mitigati in parallelo. Questo articolo illustra la risoluzione del terzo e del quarto ostacolo.

### <a name="evolution-of-the-cloud-governance-team"></a>Evoluzione del team di governance del cloud

Il team di governance del cloud è in espansione. Data la necessità di ulteriore supporto in merito alla gestione delle identità, un amministratore di sistemi del team addetto alla baseline di identità ora partecipa a una riunione settimanale per mantenere i membri del team al corrente dei cambiamenti.

### <a name="evolution-of-the-current-state"></a>Evoluzione dello stato attuale

Il team IT ha ricevuto l'approvazione a procedere con i piani del CIO e del CFO per il ritiro di due data center. Tuttavia, la preoccupazione dell'IT è che il 750 (il 15%) degli asset in tali data center dovrà essere spostato in una posizione diversa dal cloud.

### <a name="evolution-of-the-future-state"></a>Evoluzione dello stato futuro

I nuovi piani per lo stato futuro richiedono una soluzione più solida per la baseline di identità, per eseguire la migrazione delle 750 macchine virtuali con requisiti di autenticazione legacy. Oltre a questi due data center, si prevede che percentuali simili di asset in altri data center saranno interessati da questa problematica.
Ora lo stato futuro richiede anche una connessione dal provider di servizi cloud alla soluzione MPLS/linea dedicata della società.

I cambiamenti che riguardano lo stato attuale e quello futuro creano nuovi rischi che richiederanno nuove definizioni di criteri.

## <a name="evolution-of-tangible-risks"></a>Evoluzione dei rischi tangibili

**Interruzione delle attività aziendali durante la migrazione**. La migrazione al cloud crea un rischio controllato e limitato nel tempo, che può essere gestito. Lo spostamento dell'hardware obsolescente in un'altra parte del mondo comporta rischi molto maggiori. È necessaria una strategia di mitigazione dei rischi per evitare interruzioni delle operazioni aziendali.

**Dipendenze da servizi di gestione delle identità esistenti**. Le dipendenze da servizi di autenticazione e gestione delle identità esistenti possono ritardare o impedire la migrazione di alcuni carichi di lavoro al cloud. La mancata restituzione dei due data center nei tempi previsti comporterà spese nell'ordine dei milioni di dollari in canoni di lease.

Questo rischio aziendale può tradursi in alcuni rischi tecnici:

- L'autenticazione legacy potrebbe non essere disponibile nel cloud, limitando la distribuzione di alcune applicazioni.
- La soluzione di autenticazione a più fattori di terze parti potrebbe non essere disponibile nel cloud, limitando la distribuzione di alcune applicazioni.
- L'adozione di nuovi strumenti o lo spostamento potrebbero creare interruzioni e aggiungere costi.
- La velocità e la stabilità della rete VPN potrebbero impedire la migrazione.
- Il traffico in ingresso nel cloud potrebbe provocare problemi di sicurezza in altre parti della rete globale.

## <a name="evolution-of-the-policy-statements"></a>Evoluzione delle definizioni dei criteri

Le modifiche ai criteri indicate di seguito contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione.

1. Il provider di servizi cloud scelto deve offrire un mezzo per l'autenticazione tramite i metodi legacy.
2. Il provider di servizi cloud scelto deve offrire un mezzo per l'autenticazione tramite la soluzione di autenticazione a più fattori di terze parti corrente.
3. È necessario stabilire una connessione privata ad alta velocità tra il provider di servizi cloud e il provider di servizi di telecomunicazione della società, connettendo il provider di servizi cloud alla rete globale di data center.
4. Finché non vengono stabiliti requisiti di sicurezza sufficienti, nessun tipo di traffico pubblico in ingresso può accedere agli asset aziendali ospitati nel cloud. Tutte le porte vengono bloccate per qualsiasi origine all'esterno della WAN globale.

## <a name="evolution-of-the-best-practices"></a>Evoluzione delle procedure consigliate

La progettazione dell'MVP per la governance si evolve in modo da includere nuovi criteri di Azure e un'implementazione di Active Directory in una macchina virtuale. Insieme, queste due modifiche di progettazione soddisfano le nuove definizioni dei criteri aziendali.

Ecco le nuove procedure consigliate:

1. Progetto per la rete perimetrale: il lato locale della rete perimetrale deve essere configurato in modo da consentire la comunicazione tra la soluzione e i server Active Directory locali. La procedura consigliata richiede una rete perimetrale per abilitare Active Directory Domain Services attraverso i limiti di rete.
2. Modelli di Azure Resource Manager:
    1. Definire un gruppo di sicurezza di rete per bloccare il traffico esterno e aggiungere il traffico interno all'elenco elementi consentiti.
    1. Distribuire due macchine virtuali Active Directory in una coppia con bilanciamento del carico in base a un'immagine finale. Al primo avvio, l'immagine esegue uno script di PowerShell per eseguire l'aggiunta al dominio e la registrazione con i servizi di dominio. Per altre informazioni, vedere [Estendere Active Directory Domain Services in Azure](../../../../reference-architectures/identity/adds-extend-domain.md).
3. Criteri di Azure: Applicare il gruppo di sicurezza a tutte le risorse.
4. Progetto di Azure
    1. Creare un progetto denominato `active-directory-virtual-machines`.
    1. Aggiungere al progetto tutti i criteri e i modelli di Active Directory.
    1. Pubblicare il progetto in qualsiasi gruppo di gestione applicabile.
    1. Applicare il progetto a tutte le sottoscrizioni che richiedono l'autenticazione legacy o MFA di terze parti.
    1. L'istanza di Active Directory in esecuzione in Azure può ora essere usata come estensione della soluzione Active Directory locale, in modo che possa integrarsi con lo strumento MFA esistente e fornire l'autenticazione basata su attestazioni tramite la funzionalità Active Directory esistente.

## <a name="conclusion"></a>Conclusioni

L'aggiunta di queste modifiche all'MVP per la governance contribuisce a mitigare molti dei rischi descritti in questo articolo, consentendo a ogni team di adozione cloud di superare rapidamente l'ostacolo.

## <a name="next-steps"></a>Passaggi successivi

In un'epoca in cui l'adozione del cloud continua a evolversi e a offrire valore aziendale, anche i rischi e le esigenze di governance sono destinati ad evolversi. Di seguito sono illustrate alcune possibili evoluzioni. Per la società fittizia presentata in questo percorso, il prossimo fattore scatenante sarà l'inclusione dei dati protetti nel piano di adozione del cloud. Questa modifica richiederà controlli di sicurezza aggiuntivi.

> [!div class="nextstepaction"]
> [Evoluzione di Baseline di sicurezza](./security-baseline-evolution.md)
