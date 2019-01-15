---
title: Sicurezza delle soluzioni dati
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 47e3be2afd14d980b98ac9659f7f1e5a4df3403f
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54110876"
---
# <a name="securing-data-solutions"></a>Sicurezza delle soluzioni dati

In molti casi, rendere i dati accessibili nel cloud, in particolare nella fase di passaggio dall'utilizzo esclusivo di archivi dati locali, può causare problemi relativi all'aumento dell'accessibilità ai dati e alla necessità di individuare nuovi modi per garantirne la sicurezza.

## <a name="challenges"></a>Problematiche

- Centralizzazione del monitoraggio e dell'analisi degli eventi di sicurezza archiviati in più log.
- Implementazione di soluzioni di crittografia e gestione delle autorizzazioni in applicazioni e servizi.
- Verifica che la gestione centralizzata delle identità sia operativa in tutte le soluzioni in uso, sia in locale che nel cloud.

## <a name="data-protection"></a>Protezione dati

Per la protezione delle informazioni, è innanzitutto fondamentale identificare gli elementi da proteggere. Sviluppare linee guida chiare, semplici ed efficaci per identificare, proteggere e monitorare gli asset di dati più importanti, ovunque si trovino. Definire il livello di protezione più elevato per le risorse di maggiore impatto su redditività e mission aziendali, ossia le risorse di valore. Eseguire analisi dettagliate del ciclo di vita delle risorse di valore e delle dipendenze di sicurezza e stabilire condizioni e controlli di sicurezza appropriati. Analogamente, identificare e classificare risorse sensibili e definire le tecnologie e i processi a cui applicare automaticamente i controlli di sicurezza.

Dopo aver identificato i dati da proteggere, è necessario individuare soluzioni per la protezione dei dati *inattivi* e *in transito*.

- **Dati inattivi**: dati presenti in modo statico su supporti fisici, sia dischi magnetici che dischi ottici, in locale o nel cloud.
- **Dati in transito**: dati trasferiti tra componenti, posizioni o programmi, ad esempio attraverso la rete, tramite un bus di servizio (da locale a cloud e viceversa) o durante un processo di input/output.

Per altre informazioni sulla protezione dei dati inattivi o in transito, vedere [Procedure consigliate per la sicurezza dei dati e la crittografia in Azure](/azure/security/azure-security-data-encryption-best-practices).

## <a name="access-control"></a>Controllo dell’accesso

Per la protezione dei dati nel cloud, è importante adottare una combinazione di soluzioni di gestione delle identità e controllo di accesso. Data la varietà e il tipo di servizi cloud, nonché la diffusione del [cloud ibrido](../scenarios/hybrid-on-premises-and-cloud.md), esistono diverse procedure fondamentali da seguire nell'ambito del controllo di accesso e delle identità:

- Centralizzare la gestione delle identità.
- Abilitare l'autenticazione Single Sign-On (SSO).
- Distribuire la gestione delle password.
- Applicare l'autenticazione a più fattori (MFA) per gli utenti.
- Usare il controllo degli accessi in base al ruolo.
- Configurare criteri di accesso condizionale per il miglioramento della concezione classica di identità utente con proprietà aggiuntive relative a posizione dell'utente, tipo di dispositivo, livello di patch e così via.
- Controllare i percorsi di creazione delle risorse con Resource Manager.
- Monitorare attivamente le attività sospette

Per altre informazioni, vedere [Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure](/azure/security/azure-security-identity-management-best-practices).

## <a name="auditing"></a>Controllo

Oltre al monitoraggio delle identità e degli accessi descritto in precedenza, le applicazioni e i servizi usati nel cloud dovrebbero generare eventi correlati alla sicurezza da poter monitorare. La sfida principale nel monitorare tali eventi consiste nella gestione dei log, al fine di evitare potenziali problemi o risolvere quelli già esistenti. Le applicazioni basate sul cloud contengono, in genere, molte parti mobili, la maggior parte delle quali genera una certa quantità di dati di registrazione e telemetria. Può essere opportuno adottare soluzioni di analisi e monitoraggio centralizzate, in modo da gestire e utilizzare più efficacemente grandi quantità di informazioni.

Per altre informazioni, vedere [Registrazione e controllo di Azure](/azure/security/azure-log-audit).

## <a name="securing-data-solutions-in-azure"></a>Sicurezza delle soluzioni dati in Azure

### <a name="encryption"></a>Crittografia

**Macchine virtuali**. Usare [Crittografia dischi di Azure](/azure/security/azure-security-disk-encryption) per crittografare i dischi collegati in macchine virtuali Windows o Linux. Questa soluzione è integrata in [Azure Key Vault](/azure/key-vault/) per consentire il controllo e la gestione dei segreti e delle chiavi di crittografia dei dischi.

**Archiviazione di Azure**. Usare [Crittografia del servizio di archiviazione di Azure](/azure/storage/common/storage-service-encryption) per crittografare automaticamente i dati inattivi in Archiviazione di Azure. Le attività di crittografia, decrittografia e gestione delle chiavi sono completamente trasparenti per gli utenti. È anche possibile proteggere i dati in transito usando la crittografia lato client con Azure Key Vault. Per altre informazioni, vedere [Crittografia lato client e Azure Key Vault per Archiviazione di Microsoft Azure](/azure/storage/common/storage-client-side-encryption).

**Database SQL** e **Azure SQL Data Warehouse**. Usare [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE) per eseguire la crittografia e la decrittografia in tempo reale dei database, dei backup associati e dei file di log delle transazioni, senza dover apportare modifiche all'applicazione. Il database SQL usa anche la funzionalità [Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) che consente di proteggere i dati sensibili inattivi sul server durante lo spostamento tra client e server e durante l'uso. È possibile usare Azure Key Vault per archiviare le chiavi di crittografia Always Encrypted.

### <a name="rights-management"></a>Rights Management

[Azure Rights Management](/information-protection/understand-explore/what-is-azure-rms) è un servizio basato su cloud che usa criteri di crittografia, identità e autorizzazione per proteggere i file e la posta elettronica. Viene usato su più dispositivi: telefoni, tablet e PC. È possibile proteggere le informazioni sia all'interno che all'esterno dell'organizzazione perché la protezione rimane associata ai dati, anche al di fuori dei confini fisici dell'organizzazione.

### <a name="access-control"></a>Controllo di accesso

Usare il [controllo degli accessi in base al ruolo](/azure/active-directory/role-based-access-control-what-is) per limitare l'accesso alle risorse di Azure basate sui ruoli utente. Se si usa Active Directory locale, è possibile [eseguire la sincronizzazione con Azure AD](/azure/active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements) per fornire agli utenti un'identità cloud basata sull'identità locale.

Usare l'[accesso condizionale in Azure Active Directory](/azure/active-directory/active-directory-conditional-access-azure-portal) per applicare controlli sull'accesso alle applicazioni nel proprio ambiente in base a specifiche condizioni. L'istruzione del criterio potrebbe ad esempio assumere la forma seguente: _Quando i terzisti provano ad accedere alle app cloud da reti non attendibili, bloccare l'accesso_.

[Azure AD Privileged Identity Management](/azure/active-directory/active-directory-privileged-identity-management-configure) consente di gestire, controllare e monitorare gli utenti e le attività che eseguono con i propri privilegi di amministratore. Si tratta di un passaggio importante per definire le entità che all'interno dell'organizzazione possono eseguire operazioni con privilegi in Azure AD, Azure, Office 365 o app SaaS, nonché monitorarne le attività.

### <a name="network"></a>Rete

Per proteggere i dati in transito, usare sempre i protocolli SSL/TLS durante lo scambio di dati in posizioni diverse. In alcuni casi, è necessario isolare l'intero canale di comunicazione tra l'infrastruttura locale e quella cloud usando una rete privata virtuale (VPN) o [ExpressRoute](/azure/expressroute/). Per altre informazioni, vedere [Estensione di soluzioni dati locali nel cloud](../scenarios/hybrid-on-premises-and-cloud.md).

Usare [gruppi di sicurezza di rete](/azure/virtual-network/virtual-networks-nsg) (NSG) per ridurre il numero di potenziali vettori di attacco. Un gruppo di sicurezza di rete contiene un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in ingresso o in uscita in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo.

Usare gli [endpoint di servizio di Rete virtuale](/azure/virtual-network/virtual-network-service-endpoints-overview) per proteggere le risorse di SQL di Azure o di Archiviazione di Azure, in modo che solo il traffico dalla propria rete virtuale possa accedere a tali risorse.

Le macchine virtuali all'interno di una rete virtuale di Azure (VNet) possono comunicare in modo sicuro con altre reti virtuali usando il [peering di rete virtuale](/azure/virtual-network/virtual-network-peering-overview). Il traffico di rete tra reti virtuali di cui è stato eseguito il peering è privato. Il traffico tra le reti virtuali viene mantenuto nella rete backbone Microsoft.

Per altre informazioni, vedere [Sicurezza di rete di Azure](/azure/security/azure-network-security).

### <a name="monitoring"></a>Monitoraggio

Il [Centro sicurezza di Azure](/azure/security-center/security-center-intro) raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni dei partner connesse, ad esempio soluzioni firewall, per rilevare le minacce reali e ridurre i falsi positivi.

[Log Analytics](/azure/log-analytics/log-analytics-overview) fornisce l'accesso centralizzato ai log e consente di analizzare i dati e creare avvisi personalizzati.

[Rilevamento delle minacce per il database SQL di Azure](/azure/sql-database/sql-database-threat-detection) rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. I responsabili della sicurezza o altri amministratori designati possono ricevere una notifica immediata sulle attività di database sospette non appena si verificano. Ogni notifica contiene dettagli sulle attività sospette e consigli su come eseguire ulteriori indagini e mitigare la minaccia.
