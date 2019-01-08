---
title: SAP S/4HANA per le macchine virtuali Linux in Azure
titleSuffix: Azure Reference Architectures
description: Procedure consolidate per l'esecuzione di SAP S/4HANA in un ambiente Linux su Azure con disponibilità elevata.
author: lbrader
ms.date: 05/11/2018
ms.custom: seodec18
ms.openlocfilehash: 9eb73ddaf5b1cb815f037f46c7e187f61d126876
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53644173"
---
# <a name="sap-s4hana-for-linux-virtual-machines-on-azure"></a>SAP S/4HANA per le macchine virtuali Linux in Azure

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di S/4HANA in un ambiente a disponibilità elevata che supporta il ripristino di emergenza in Azure. Questa architettura viene distribuita con dimensioni di macchina virtuale (VM) specifiche, che possono essere modificate in base alle esigenze dell'organizzazione.

![Architettura di riferimento per SAP S/4HANA per le macchine virtuali Linux in Azure](./images/sap-s4hana.png)

*Scaricare un [file Visio][visio-download] di questa architettura.*

> [!NOTE]
> La distribuzione di questa architettura di riferimento richiede una licenza adeguata dei prodotti SAP e altre tecnologie non Microsoft.

## <a name="architecture"></a>Architettura

Questa architettura di riferimento descrive un sistema di produzione di livello aziendale. Questa configurazione può essere ridotta a una singola macchina virtuale in base alle esigenze aziendali. Sono tuttavia necessari i componenti seguenti:

**Rete virtuale**. Il servizio [Rete virtuale di Azure](/azure/virtual-network/virtual-networks-overview) connette in modo sicuro tra loro le risorse di Azure. In questa architettura la rete virtuale si connette a un ambiente locale tramite un gateway distribuito nell'hub di una [topologia hub-spoke](../hybrid-networking/hub-spoke.md). Lo spoke è la rete virtuale usata per le applicazioni SAP.

**Subnet**. La rete virtuale è suddivisa in [subnet](/azure/virtual-network/virtual-network-manage-subnet) separate per ogni livello: gateway, applicazione, database e servizi condivisi.

**Macchine virtuali**. Questa architettura usa macchine virtuali che eseguono Linux per il livello applicazione e il livello database, raggruppate in questo modo:

- **Livello applicazione**. Include il pool di server front-end Fiori, il pool di Dispatcher Web SAP, il pool di server applicazioni e il cluster SAP Central Services. Per la disponibilità elevata di Central Services nelle macchine virtuali Linux di Azure, è necessario un servizio NFS (Network File System) a disponibilità elevata.
- **Cluster NFS**. Questa architettura usa un server [NFS](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) server in esecuzione in un cluster Linux per archiviare i dati condivisi tra sistemi SAP. Questo cluster centralizzato può essere condiviso tra più sistemi SAP. Per la disponibilità elevata del servizio NFS, viene usata l'estensione a disponibilità elevata appropriata per la distribuzione Linux selezionata.
- **SAP HANA**. Il livello database usa due o più macchine virtuali Linux in un cluster per ottenere la disponibilità elevata. La replica di sistema HANA (HSR) viene usata per replicare il contenuto tra i sistemi HANA primari e secondari. Il clustering Linux viene usato per rilevare gli errori di sistema e semplificare il failover automatico. È possibile usare un meccanismo di isolamento basato su cloud o archiviazione per assicurarsi che il sistema con errori venga isolato o arrestato per evitare la condizione split-brain del cluster.
- **Jumpbox**. Detto anche bastion host. È una macchina virtuale sicura in rete usata dagli amministratori per connettersi alle altre macchine virtuali. Può eseguire Windows o Linux. Usare Windows jumpbox per semplificare l'esplorazione Web quando si usano gli strumenti di gestione HANA Cockpit o HANA Studio.

**Servizi di bilanciamento del carico**. Per ottenere disponibilità elevata, vengono usati sia servizi di bilanciamento del carico SAP sia [Azure Load Balancer](/azure/load-balancer/load-balancer-overview). Le istanze di Azure Load Balancer vengono usate per distribuire il carico alle macchine virtuali nella subnet del livello applicazione.

**Set di disponibilità**. Le macchine virtuali per tutti i pool e u cluster (Web Dispatcher, server applicazioni SAP, Central Services, NFS e HANA) sono raggruppate in [set di disponibilità](/azure/virtual-machines/windows/tutorial-availability-sets), e viene effettuato il provisioning di almeno due macchine virtuali per ogni ruolo. Questo approccio rende le macchine virtuali idonee per un [contratto di servizio](https://azure.microsoft.com/support/legal/sla/virtual-machines) di livello superiore.

**Schede di rete**. Le [schede di interfaccia di rete](/azure/virtual-network/virtual-network-network-interface) (NIC) permettono tutta la comunicazione delle macchine virtuali in una rete virtuale.

**Gruppi di sicurezza di rete**. Per limitare il traffico in ingresso, in uscita e tra subnet nella rete virtuale, vengono usati i [gruppi di sicurezza di rete](/azure/virtual-network/virtual-networks-nsg).

**Gateway**. Un gateway estende la rete locale alla rete virtuale di Azure. [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) è il servizio di Azure consigliato per creare connessioni private che non passano attraverso la rete Internet pubblica, ma è possibile usare anche una connessione [da sito a sito](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Archiviazione di Azure**. Per fornire l'archiviazione permanente di un disco rigido virtuale di una macchina virtuale, è necessario usare [Archiviazione di Azure](/azure/storage/).

## <a name="recommendations"></a>Consigli

Questa architettura descrive una distribuzione aziendale di piccole dimensioni a livello di produzione. La distribuzione varia in base ai requisiti aziendali. Usare queste indicazioni come punto di partenza.

### <a name="virtual-machines"></a>Macchine virtuali

Nei pool di server applicazioni e nei cluster regolare il numero di macchine virtuali in base ai requisiti specifici. La [guida alla pianificazione e all'implementazione di macchine virtuali di Azure](/azure/virtual-machines/workloads/sap/planning-guide) include informazioni dettagliate sull'esecuzione di SAP NetWeaver in macchine virtuali, ma le informazioni si applicano anche a SAP S/4HANA.

Per informazioni dettagliate sul supporto SAP per i tipi di macchine virtuali di Azure e le metriche di elaborazione (SAPS), vedere la [nota SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533).

### <a name="sap-web-dispatcher-pool"></a>Pool di componenti SAP Web Dispatcher

Il componente Web Dispatcher viene usato come servizio di bilanciamento del carico per il traffico SAP tra i server applicazioni SAP. Per ottenere la disponibilità elevata per il componente Web Dispatcher, viene usato Azure Load Balancer per implementare la configurazione di Web Dispatcher parallela in una configurazione round robin per la distribuzione del traffico HTTP(S) tra i componenti Web Dispatcher disponibili nel pool back-end di bilanciamento del carico.

### <a name="fiori-front-end-server"></a>Server front-end Fiori

Il server front-end Fiori usa un [gateway NetWeaver](https://help.sap.com/doc/saphelp_gateway20sp12/2.0/en-US/76/08828d832e4aa78748e9f82204a864/content.htm?no_cache=true). Per distribuzioni di piccole dimensioni può essere caricato nel server Fiori. Per distribuzioni di grandi dimensioni è possibile distribuire un server separato per il gateway NetWeaver prima del pool di server front-end Fiori.

### <a name="application-servers-pool"></a>Pool di server applicazioni

Per gestire i gruppi di accesso per i server applicazioni ABAP, viene usata la transazione SMLG. Questa usa la funzione di bilanciamento del carico nel server messaggi di Central Services per distribuire il carico di lavoro nel pool di server applicazioni SAP per le interfacce utente grafiche di SAP e il traffico RFC. La connessione del server applicazioni a Central Services con disponibilità elevata avviene tramite il nome di rete virtuale del cluster. In questo modo si evita la necessità di modificare il profilo del server applicazioni per la connettività di Central Services dopo un failover locale.

### <a name="sap-central-services-cluster"></a>Cluster SAP Central Services

Central Services può essere distribuito in una singola macchina virtuale quando la disponibilità elevata non è un requisito. Tuttavia, la singola macchina virtuale diventa un singolo punto di errore potenziale per l'ambiente SAP. Per una distribuzione di Central Services a disponibilità elevata, vengono usati un cluster NFS a disponibilità elevata e un cluster Central Services a disponibilità elevata.

### <a name="nfs-cluster"></a>Cluster NFS

DRBD (Distributed replicati blocco dispositivo) viene usato per la replica tra i nodi del cluster NFS.

### <a name="availability-sets"></a>Set di disponibilità

I set di disponibilità distribuiscono i server in un'infrastruttura fisica diversa e aggiornano i gruppi per migliorare la disponibilità del servizio. Inserire le macchine virtuali che eseguono lo stesso ruolo in un set di disponibilità per prevenire il tempo di inattività causato dalla manutenzione dell'infrastruttura di Azure e soddisfare i [contratti di servizio](https://azure.microsoft.com/support/legal/sla/virtual-machines). Sono consigliate due o più macchine virtuali per ogni set di disponibilità.

Tutte le macchine virtuali in un set devono eseguire lo stesso ruolo. Non inserire server di ruoli diversi nello stesso set di disponibilità. Ad esempio, non inserire un nodo ASCS nello stesso set di disponibilità del server applicazioni.

### <a name="nics"></a>Schede di interfaccia di rete

I tradizionali panorami applicativi SAP locali implementano più schede di interfaccia di rete (NIC) per ogni macchina per segregare il traffico amministrativo rispetto al traffico aziendale. In Azure la rete virtuale è una rete software-defined che invia tutto il traffico attraverso la stessa infrastruttura di rete. Di conseguenza, l'uso di più schede di interfaccia di rete non è necessario. Tuttavia, se l'organizzazione deve segregare il traffico, è possibile distribuire più schede di interfaccia di rete per ogni macchina virtuale, connettere ogni scheda di interfaccia di rete a una subnet diversa e quindi usare i gruppi di sicurezza di rete per applicare diversi criteri di controllo di accesso.

### <a name="subnets-and-nsgs"></a>Subnet e gruppi di sicurezza di rete

Questa architettura suddivide lo spazio degli indirizzi delle rete virtuale in subnet. Ogni subnet può essere associata a un gruppo di sicurezza di rete che definisce i criteri di accesso per la subnet. Posizionare i server applicazioni in una subnet separata in modo da proteggerli più facilmente gestendo i criteri di sicurezza della subnet anziché i singoli server.

Quando un gruppo di sicurezza di rete viene associato a una subnet, viene applicato a tutti i server all'interno della subnet. Per altre informazioni sull'uso di gruppi di sicurezza di rete per il controllo granulare dei server in una subnet, vedere [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://azure.microsoft.com/blog/multiple-vm-nics-and-network-virtual-appliances-in-azure/).

Vedere anche [Pianificazione e progettazione per il gateway VPN](/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="load-balancers"></a>Servizi di bilanciamento del carico

[SAP Web Dispatcher](https://help.sap.com/doc/saphelp_nw73ehp1/7.31.19/en-US/48/8fe37933114e6fe10000000a421937/frameset.htm) gestisce il bilanciamento del carico del traffico HTTP(S) comprese le applicazioni tipo Fiori in un pool di server applicazioni SAP.

Per il traffico proveniente dai client con interfaccia utente grafica SAP che si connettono a un server SAP tramite il protocollo DIAG o Remote Function Call (RFC), il server messaggi Central Services bilancia il carico tramite [gruppi di accesso](https://wiki.scn.sap.com/wiki/display/SI/ABAP+Logon+Group+based+Load+Balancing) del server applicazioni SAP e di conseguenza non è necessario bilanciamento del carico aggiuntivo.

### <a name="azure-storage"></a>Archiviazione di Azure

È consigliabile usare Archiviazione Premium di Azure per le macchine virtuali del server di database. Archiviazione Premium offre una latenza di lettura/scrittura coerente. Per informazioni dettagliate sull'uso di Archiviazione Premium per i dischi del sistema operativo e i dischi dati di una macchina virtuale a istanza singola, vedere [Contratto di servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Per tutti i sistemi SAP di produzione è consigliabile usare [Azure Managed Disks](/azure/storage/storage-managed-disks-overview) Premium. Managed Disks viene usato per gestire i file VHD per i dischi ai fini dell'affidabilità. Garantisce inoltre l'isolamento dei dischi per le macchine virtuali all'interno di un set di disponibilità, in modo da evitare singoli punti di errore.

Per i server applicazioni SAP, tra cui le macchine virtuali di Central Services, è possibile usare Archiviazione Standard di Azure per ridurre i costi, perché l'esecuzione delle applicazioni avviene in memoria e i dischi vengono usati solo per la registrazione. Tuttavia, attualmente Archiviazione Standard è certificato solo per l'archiviazione non gestita. Poiché i server applicazioni non ospitano dati, è possibile usare i dischi di Archiviazione Premium P4 e P6 di dimensioni minori per ridurre i costi.

Per l'archivio dati di backup, è consigliabile usare il [livello ad accesso sporadico e il livello di accesso archivio di Azure per l'archiviazione](/azure/storage/storage-blob-storage-tiers). Questi livelli di archiviazione sono convenienti per archiviare dati di durata elevata cui si accede meno di frequente.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

I server applicazioni SAP svolgono comunicazioni costanti con i server di database. Per le macchine virtuali del database HANA provare ad abilitare l'[acceleratore di scrittura](/azure/virtual-machines/linux/how-to-enable-write-accelerator) per migliorare la latenza di scrittura dei log. Per ottimizzare le comunicazioni tra server, usare la [rete accelerata](https://azure.microsoft.com/blog/linux-and-windows-networking-performance-enhancements-accelerated-networking/). Notare che questi acceleratori sono disponibili solo per determinate serie di macchine virtuali.

Per ottenere valori elevati per le operazioni di I/O al secondo e la velocità effettiva della larghezza di banda dei dischi, vengono usate le procedure comuni per l'[ottimizzazione delle prestazioni](/azure/virtual-machines/linux/premium-storage-performance) dei volumi di archiviazione per il layout di archiviazione di Azure. Ad esempio, la combinazione di più dischi per creare un volume di dischi con striping migliora le prestazioni di I/O. L'abilitazione della cache di lettura sul contenuto di archiviazione che viene modificato raramente migliora la velocità di recupero di dati. Per informazioni dettagliate sui requisiti delle prestazioni, vedere [SAP note 1943937 - Hardware Configuration Check Tool](https://launchpad.support.sap.com/#/notes/1943937) (Nota SAP 1943937 - Strumento di controllo della configurazione dell'hardware). Per l'accesso, è necessario un account di SAP Service Marketplace.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

A livello dell'applicazione SAP, Azure offre un'ampia gamma di dimensioni di macchina virtuale per la scalabilità verticale e orizzontale. Per altre informazioni, consultare la [Nota SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533) - Applicazioni SAP in Azure: prodotti supportati e tipi di macchine virtuali di Azure (per l’accesso è necessario un account di SAP Service Marketplace). Man mano che vengono certificati più tipi di macchine virtuali, è possibile aumentare o ridurre le prestazioni con la stessa distribuzione cloud.

A livello di database questa architettura esegue HANA in macchine virtuali. Se il carico di lavoro supera le dimensioni massime delle macchine virtuali, Microsoft offre inoltre [istanze Large di Azure](/azure/virtual-machines/workloads/sap/hana-overview-architecture) per SAP HANA. Questi server fisici vengono posizionati in un centro dati certificato di Microsoft Azure che fornisce attualmente fino a 20 TB di capacità di memoria per una singola istanza. La configurazione a più nodi è anche possibile con una capacità di memoria totale di 60 TB.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

La ridondanza delle risorse è il tema generale nelle soluzioni di infrastruttura a disponibilità elevata. Per le organizzazioni che hanno contratti di servizio meno rigorosi, le macchine virtuali di Azure a istanza singola offrono un contratto di servizio per il tempo di attività. Per altre informazioni, vedere [Contratti di servizio di Azure](https://azure.microsoft.com/support/legal/sla/).

In questa installazione distribuita dell'applicazione SAP l'installazione di base viene replicata per ottenere la disponibilità elevata. Per ogni livello dell'architettura, la progettazione a disponibilità elevata varia.

### <a name="application-tier"></a>Livello applicazione

- Web Dispatcher. La disponibilità elevata viene ottenuta con istanze ridondanti di Web Dispatcher. Vedere [SAP Web Dispatcher](https://help.sap.com/doc/saphelp_nw70ehp2/7.02.16/en-us/48/8fe37933114e6fe10000000a421937/frameset.htm) nella documentazione di SAP.
- Server Fiori. La disponibilità elevata viene ottenuta bilanciando il carico del traffico all'interno di un pool di server.
- Central Services. Per la disponibilità elevata di Central Services nelle macchine virtuali Linux di Azure, viene usata l'estensione a disponibilità elevata appropriata per la distribuzione Linux selezionata e il cluster NFS a disponibilità elevata ospita l'archiviazione DRBD.
- Server applicazioni. Si ottiene la disponibilità elevata dal traffico del bilanciamento del carico in un pool di server di applicazioni.

### <a name="database-tier"></a>Livello database

Questa architettura di riferimento illustra un sistema di database SAP HANA a disponibilità elevata costituito da due macchine virtuali di Azure. La funzionalità di replica del sistema nativo del livello database offre failover manuale o automatico tra i nodi replicati:

- Per il failover manuale distribuire più di un'istanza di HANA e usare la replica di sistema HANA (HSR).
- Per il failover automatico usare sia HSR sia l'estensione a disponibilità elevata (HAE) Linux per la distribuzione Linux. Linux HAE fornisce i servizi cluster alle risorse HANA rilevando gli eventi di errore e orchestrando il failover dei servizi in errore nel nodo integro.

Vedere [Configurazioni e certificazioni SAP in esecuzione su Microsoft Azure](/azure/virtual-machines/workloads/sap/sap-certifications).

### <a name="disaster-recovery-considerations"></a>Considerazioni sul ripristino di emergenza

Ogni livello usa una strategia diversa per fornire una protezione con ripristino di emergenza.

- **Livello server applicazioni**. I server applicazioni SAP non contengono dati aziendali. In Azure, una semplice strategia di ripristino di emergenza consiste nel creare server applicazioni SAP nell'area secondaria e quindi arrestarli. In caso di modifiche di configurazione o aggiornamenti del kernel nel server applicazioni primario, le stesse modifiche devono essere applicate alle macchine virtuali nell'area secondaria. Ad esempio, copiare gli eseguibili del kernel SAP nelle macchine virtuali di ripristino di emergenza. Per la replica automatica dei server applicazioni in un'area secondaria, la soluzione consigliata è [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Attualmente ASR non supporta ancora la replica dell'impostazione di configurazione della rete accelerata nelle macchine virtuali di Azure.

- **Central Services**. Anche questo componente dello stack di applicazioni SAP non salva in modo permanente i dati aziendali. È possibile compilare una macchina virtuale nell'area secondaria per eseguire il ruolo Central Services. L'unico contenuto dal nodo Central Services primario per la sincronizzazione è il contenuto della condivisione /sapmnt. Inoltre, se le modifiche di configurazione o gli aggiornamenti del kernel avvengono nei server Central Services primari, devono essere ripetuti nella macchina virtuale nell'area secondaria che esegue Central Services. Per sincronizzare i due server, è possibile usare Azure Site Recovery per replicare i nodi del cluster oppure è sufficiente usare un processo di copia pianificato regolarmente per copiare /sapmnt nell'area di ripristino di emergenza. Per informazioni dettagliate sulla build, sulla compilazione e sul processo di failover di test, scaricare [SAP NetWeaver: creazione di una soluzione di ripristino di emergenza basata su Hyper-V e Microsoft Azure](https://download.microsoft.com/download/9/5/6/956FEDC3-702D-4EFB-A7D3-2DB7505566B6/SAP%20NetWeaver%20-%20Building%20an%20Azure%20based%20Disaster%20Recovery%20Solution%20V1_5%20.docx) e fare riferimento a alla sezione 4.3 "Livello SAP SPOF (ASCS)." Questo documento si applica a NetWeaver in esecuzione su Windows, ma è possibile creare la configurazione equivalente per Linux. Per Central Services usare [Azure Site Recovery](/en-us/azure/site-recovery/site-recovery-overview) per replicare i nodi del cluster e l'archiviazione. Per Linux creare un geo-cluster a tre nodi con un'estensione a disponibilità elevata.

- **Livello database SAP**. Usare HSR per la replica supportata da HANA. In aggiunta a una configurazione a disponibilità elevata locale a due nodi, HSR supporta la replica multilivello, in cui un terzo nodo in un'area di Azure separata agisce come entità esterna, non appartenente al cluster, e si registra nella replica secondaria della coppia HSR in cluster come destinazione di replica. In questo modo si forma un collegamento a margherita di repliche. Il failover al nodo di ripristino di emergenza è un processo manuale.

Per usare Azure Site Recovery in modo da compilare automaticamente un sito di produzione completamente replicato del sito originale, è necessario eseguire [script di distribuzione](/azure/site-recovery/site-recovery-runbook-automation) personalizzati. Site Recovery distribuisce prima di tutto le macchine virtuali nei set di disponibilità e quindi esegue gli script per aggiungere risorse, come i servizi di bilanciamento del carico.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

SAP HANA ha una funzionalità di backup che usa l'infrastruttura di Azure sottostante. Per eseguire il backup del database SAP HANA in esecuzione nelle macchine virtuali di Azure, sia lo snapshot di SAP HANA sia lo snapshot di archiviazione di Azure vengono usati per garantire la coerenza dei file di backup. Per informazioni dettagliate, vedere [Guida del backup di SAP HANA in macchine virtuali di Azure](/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) e [Domande frequenti sul servizio Backup di Azure](/azure/backup/backup-azure-backup-faq). Solo le distribuzioni in un singolo contenitore HANA supportano snapshot di archiviazione di Azure.

### <a name="identity-management"></a>Gestione delle identità

Controllare l'accesso alle risorse usando un sistema di gestione delle identità centralizzato a tutti i livelli:

- Fornire l'accesso alle risorse di Azure tramite il [controllo degli accessi in base al ruolo](/azure/active-directory/role-based-access-control-what-is).
- Concedere l'accesso alle macchine virtuali di Azure tramite LDAP, Azure Active Directory, Kerberos o un altro sistema.
- Supportare l'accesso dall'interno delle app stesse tramite i servizi forniti da SAP oppure usare [OAuth 2.0 e Azure Active Directory](/azure/active-directory/develop/active-directory-protocols-oauth-code).

### <a name="monitoring"></a>Monitoraggio

Azure offre diverse funzioni per [monitoraggio e diagnostica](/azure/architecture/best-practices/monitoring) dell'infrastruttura complessiva. Inoltre, il monitoraggio avanzato delle macchine virtuali di Azure (Linux o Windows) viene gestito da Azure Operations Management Suite (OMS).

Per fornire il monitoraggio basato su SAP delle risorse e delle prestazioni del servizio dell'infrastruttura SAP, viene usata l'estensione [Azure Enhanced Monitoring SAP](/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca). Questa estensione aumenta le statistiche di monitoraggio nell'applicazione SAP per il monitoraggio del sistema operativo e le funzioni del Pannello di controllo DBA. L'estensione Enhanced Monitoring SAP è un prerequisito obbligatorio per l'esecuzione di SAP in Azure. Per informazioni dettagliate, vedere ["Nota SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498) – SAP in Linux con Azure: monitoraggio avanzato."

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

SAP dispone del proprio motore di gestione di utenti (UME) per controllare l'accesso basato sui ruoli e l'autorizzazione all'interno dell'applicazione SAP. Per informazioni dettagliate, vedere [SAP HANA Security - An Overview](https://archive.sap.com/documents/docs/DOC-62943) (Sicurezza SAP HANA - Panoramica). Per l'accesso è necessario un account SAP Service Marketplace.

Per ottenere sicurezza di rete aggiuntiva, valutare se implementare una [rete perimetrale](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid), che usa un'appliance di rete virtuale per creare un firewall davanti alla subnet per Web Dispatcher e i pool di server front-end Fiori.

Per la sicurezza dell'infrastruttura, vengono crittografati sia i dati in transito sia quelli inattivi. La sezione "Considerazioni relative alla sicurezza" di [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP NetWeaver](/azure/virtual-machines/workloads/sap/planning-guide) presenta alcune informazioni iniziali per soddisfare la sicurezza di rete e si applica a S/4HANA. La guida specifica anche le porte di rete che devono essere aperte sui firewall per consentire la comunicazione dell'applicazione.

Per crittografare i dischi delle macchine virtuali IaaS Linux, è possibile usare [Crittografia dischi di Azure](/azure/security/azure-security-disk-encryption). Questo servizio usa la funzionalità DM-Crypt di Linux per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione può essere usata anche con Azure Key Vault per semplificare il controllo e la gestione dei segreti e delle chiavi di crittografia dei dischi nella sottoscrizione dell'insieme di credenziali delle chiavi. I dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

Per la crittografia dei dati a riposo SAP HANA, è consigliabile usare la tecnologia di crittografia nativa SAP HANA.

> [!NOTE]
> Non usare la crittografia di dati inattivi HANA con Crittografia dischi di Azure nello stesso server. Per HANA usare solo la crittografia dei dati HANA.

## <a name="communities"></a>Community

Le community possono rispondere alle domande ed essere utili per configurare correttamente la distribuzione. Valutare gli aspetti seguenti:

- Blog [Running SAP Applications on the Microsoft Platform](https://blogs.msdn.microsoft.com/saponsqlserver/2017/05/04/sap-on-azure-general-update-for-customers-partners-april-2017/) (Esecuzione di applicazioni SAP nella piattaforma Microsoft)
- [Supporto della community di Azure](https://azure.microsoft.com/support/community/)
- [Community SAP](https://www.sap.com/community.html)
- [Stack Overflow](https://stackoverflow.com/tags/sap/)

## <a name="related-resources"></a>Risorse correlate

Può essere utile esaminare gli [scenari di esempio di Azure](/azure/architecture/example-scenario) seguenti, che illustrano soluzioni specifiche usando alcune delle stesse tecnologie:

- [Esecuzione di carichi di lavoro di produzione SAP con un database Oracle in Azure](/azure/architecture/example-scenario/apps/sap-production)
- [Ambienti di sviluppo/test per i carichi di lavoro SAP in Azure](/azure/architecture/example-scenario/apps/sap-dev-test)

<!-- links -->

[visio-download]: https://archcenter.blob.core.windows.net/cdn/sap-reference-architectures.vsdx
