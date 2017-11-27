---
title: Distribuire SAP NetWeaver e SAP HANA in Azure
description: "Procedure consolidate per l'esecuzione di SAP HANA in un ambiente a disponibilità elevata in Azure."
author: njray
ms.date: 06/29/2017
ms.openlocfilehash: 27a97103c0c6f305cb8e830d670c8d0ba7e22aa5
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-sap-netweaver-and-sap-hana-on-azure"></a>Distribuire SAP NetWeaver e SAP HANA in Azure

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di SAP HANA in un ambiente a disponibilità elevata in Azure. [**Distribuire questa soluzione**.](#deploy-the-solution)

![0][0]

*Scaricare un [file di Visio][visio-download] di questa architettura.*

> [!NOTE]
> La distribuzione di questa architettura di riferimento richiede una licenza adeguata dei prodotti SAP e altre tecnologie non Microsoft. Per informazioni sulla relazione tra Microsoft e SAP, vedere [SAP HANA in Azure][sap-hana-on-azure].

## <a name="architecture"></a>Architettura

Questa architettura è costituita dai componenti seguenti.

- **Rete virtuale**. Una rete virtuale è una rappresentazione di una rete isolata logicamente in Azure. Tutte le macchine virtuali in questa architettura di riferimento vengono distribuite nella stessa rete virtuale. La rete virtuale viene ulteriormente suddivisa in subnet. Creare una subnet separata per ogni livello, che comprenda applicazione (SAP NetWeaver), database (SAP HANA), gestione (il jumpbox) e Active Directory.

- **Macchine virtuali (VM)**. Le macchine virtuali per questa architettura vengono raggruppate in diversi livelli diversi.

    - **SAP NetWeaver**. Comprende SAP ASCS, SAP Dispatcher Web e i server applicazioni SAP. 
    
    - **SAP HANA**. Questa architettura di riferimento usa SAP HANA per il livello di database, in esecuzione su una singola istanza [GS5][vm-sizes-mem]. SAP HANA è certificato per i carichi di lavoro OLAP della produzione in GS5 o [SAP HANA in istanze grandi di Azure][azure-large-instances]. Questa architettura di riferimento è per le macchine virtuali di Azure nella serie-G e nella serie-M. Per informazioni su SAP HANA in istanze grandi di Azure, vedere [Panoramica di SAP HANA (istanze di grandi dimensioni) e l'architettura in Azure][azure-large-instances].
   
    - **Jumpbox**. Detto anche bastion host. È una macchina virtuale sicura in rete che viene usata dagli amministratori per connettersi alle altre macchine virtuali. 
     
    - **Controller di dominio di Windows Server Active Directory (AD).** I controller di dominio vengono usati per configurare il Cluster di Failover di Windows Server (vedere sotto).
 
- **Set di disponibilità**. Posizionare le macchine virtuali per il SAP Dispatcher Web, il server dell'applicazione SAP e i ruoli ACSC SAP in set di disponibilità separati ed eseguire il provisioning di almeno due macchine virtuali per ogni ruolo. In questo modo le macchine virtuali sono idonee per un contratto di servizio di livello superiore.
    
- **Schede di rete.** Le macchine virtuali che eseguono SAP NetWeaver e SAP HANA richiedono due interfacce di rete (NIC). Ogni scheda di interfaccia di rete viene assegnata a una subnet diversa, per separare i diversi tipi di traffico. Vedere le [Raccomandazioni](#recommendations) indicate di seguito per altre informazioni.

- **Windows Server Failover Clustering**. Le macchine virtuali che eseguono ACSC SAP sono configurate come cluster di failover per una disponibilità elevata. Per supportare il cluster di failover, SIOS DataKeeper Cluster Edition esegue la funzione di volume condiviso (CSV) del cluster tramite la replica di dischi indipendenti di proprietà del nodi del cluster. Per informazioni dettagliate, vedere [Esecuzione di applicazioni SAP nella piattaforma Microsoft][running-sap].
    
- **Servizi di bilanciamento del carico** Vengono usate due istanze di [Azure Load Balancer][azure-lb]. Il primo, mostrato a sinistra del diagramma, distribuisce il traffico alle macchine virtuali di SAP Web Dispatcher. Questa configurazione implementa l'opzione dispatcher web parallela descritta in [High Availability of the SAP Web Dispatcher (Disponibilità elevata del Dispatcher Web SAP)][sap-dispatcher-ha]. Il secondo bilanciamento del carico, visualizzato sulla destra, consente il failover nel cluster di failover di Windows Server, indirizzando le connessioni in ingresso verso il nodo attivo/integro.

- **Gateway VPN** Il Gateway VPN estende la rete locale alla rete virtuale di Azure. È anche possibile usare ExpressRoute, che si serve di una connessione privata dedicata che non passa attraverso la rete Internet pubblica. La soluzione di esempio non distribuisce il gateway. Per altre informazioni, vedere [Connect an on-premises network to Azure][hybrid-networking] (Connettere una rete locale ad Azure).

## <a name="recommendations"></a>Raccomandazioni

I requisiti della propria organizzazione potrebbero essere diversi da quelli dell'architettura descritta in questo articolo. Usare queste indicazioni come punto di partenza.

### <a name="load-balancers"></a>Servizi di bilanciamento del carico

[Dispatcher Web SAP][sap-dispatcher] gestisce il bilanciamento del carico del traffico HTTP(S) ai server dual-stack (ABAP e Java). SAP raccomanda i server con applicazione single-stack da anni, pertanto sono poche oggigiorno le applicazioni che vengono eseguite su un modello di distribuzione dual stack. Il servizio di bilanciamento del carico di Azure illustrato nel diagramma dell'architettura implementa il cluster a disponibilità elevata per il SAP Dispatcher Web.

Il bilanciamento del carico del traffico ai server delle applicazioni viene gestito in SAP. Per il traffico dai client SAPGUI in connessione con un server SAP tramite DIAG e le Chiamate di funzione remota (RFC), il server di messaggistica SCS bilancia il carico tramite la creazione del Server dell'app SAP [Gruppi di accesso][logon-groups]. 

SMLG è una transazione ABAP SAP usata per gestire la funzionalità di bilanciamento del carico con accesso dei Servizi Centrali SAP. Il pool back-end del gruppo di accesso ha più di un server di applicazione ABAP. I client che accedono ai servizi di cluster ASCS si connettono al servizio di bilanciamento del carico di Azure tramite un indirizzo IP di front-end. Il nome della rete virtuale del cluster ASCS dispone anche di un indirizzo IP. Facoltativamente, questo indirizzo può essere associato a un indirizzo IP aggiuntivo sul bilanciamento del carico di Azure, in modo che il cluster possa essere gestito in remoto.  

### <a name="nics"></a>Schede di interfaccia di rete

Le funzioni di gestione orizzontale SAP richiedono la separazione del traffico del server in diverse schede di interfaccia di rete. Ad esempio, i dati aziendali devono essere separati dal traffico amministrativo e dal traffico di backup. L'assegnazione di più schede di interfaccia di rete a subnet diverse consente la separazione dei dati. Per altre informazioni, vedere "Rete" in [Building High Availability for SAP NetWeaver and SAP HANA][sap-ha] (Compilazione della disponibilità elevata per SAP NetWeaver e SAP HANA) in formato .pdf.

Assegnare la scheda di interfaccia di rete dell'amministrazione alla subnet di gestione e assegnare la scheda di interfaccia di rete della comunicazione dati a una subnet distinta. Per i dettagli sulla configurazione, vedere [Create and manage a Windows virtual machine that has multiple NICs][multiple-vm-nics] (Creare e gestire una macchina virtuale di Windows che dispone di più schede di interfaccia di rete).

### <a name="azure-storage"></a>Archiviazione di Azure

Con tutte le macchine virtuali del server del database, è consigliabile usare Archiviazione Premium di Azure per una latenza di lettura/scrittura coerente. Per i server dell'applicazione SAP, tra cui (A) SCS le macchine virtuali, è possibile usare l'archiviazione di Azure Standard, poiché l'esecuzione dell'applicazione viene eseguita in memoria e usa i dischi per la registrazione solo.

Per una migliore affidabilità, è consigliabile usare [Azure Managed Disks][managed-disks]. I dischi gestiti garantiscono che i dischi per le macchine virtuali all'interno di un set di disponibilità siano isolati per evitare singoli punti di guasto.

> [!NOTE]
> Attualmente il modello di Resource Manager per questa architettura di riferimento non usa dischi gestiti. È previsto l'aggiornamento del modello per consentire l'utilizzo di dischi gestiti.

Per ottenere un numero elevato di IOPS e una velocità effettiva della larghezza di banda del disco, si applicano le comuni procedure di ottimizzazione delle prestazioni del volume di archiviazione al layout di archiviazione di Azure. Ad esempio, lo striping di più dischi insieme per creare un volume di disco più grande migliora le prestazioni di IO. L'abilitazione della cache di lettura sul contenuto di archiviazione che viene modificato raramente migliora la velocità di recupero di dati. Per informazioni dettagliate sui requisiti delle prestazioni, vedere [SAP note 1943937 - Hardware Configuration Check Tool][sap-1943937] (Nota SAP 1943937 - Strumento di controllo della configurazione dell'Hardware).

Per l'archivio dati di backup, è consigliabile usare il [livello di archiviazione ad accesso sporadico][cool-blob-storage] di archiviazione BLOB di Azure. Il livello di archiviazione ad accesso sporadico è un modo conveniente per archiviare i dati a cui si accede meno frequentemente e di lunga durata.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Al livello dell'applicazione SAP, Azure offre un'ampia gamma di dimensioni delle macchine virtuali per la scalabilità verticale. Per un elenco completo, vedere [SAP note 1928533 - SAP Applications on Azure: Supported Products and Azure VM Types][sap-1928533] (Nota SAP 1928533 - Applicazioni SAP in Azure: Prodotti supportati e Tipi di macchine virtuali di Azure). Scalare orizzontale tramite l'aggiunta di più macchine virtuali per il set di disponibilità.

Per SAP HANA in macchine virtuali di Azure con le applicazioni OLTP e OLAP SAP, le dimensioni della macchina virtuale certificata SAP sono di GS5 con una singola istanza della macchina virtuale. Per i carichi di lavoro più grandi, Microsoft offre anche [Istanze di grandi dimensioni di Azure][azure-large-instances] per SAP HANA in server fisici posizionati in un centro dati certificato di Microsoft Azure che fornisce fino a 4 TB di capacità di memoria per una singola istanza in questo momento. La configurazione a più nodi è anche possibile con una capacità di memoria totale di 32 TB.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

In questa installazione distribuita dell'applicazione SAP in un database centralizzato, l'installazione di base verrà replicata per ottenere la disponibilità elevata. Per ogni livello dell'architettura, la progettazione a disponibilità elevata varia:

- **Dispatcher Web.** Si ottiene le disponibilità elevata con istanze di SAP Dispatcher Web ridondanti con il traffico dell'applicazione SAP. Vedere [SAP Dispatcher Web][swd] nella documentazione di SAP.

- **ASCS.** Per la disponibilità elevata di ASCS nelle macchine virtuali di Windows Azure, il Clustering di failover di Windows Server viene usato con SIOS DataKeeper per implementare il volume condiviso del cluster. Per i dettagli di implementazione, vedere [Clustering SAP ASCS in Azure][clustering].

- **Server applicazioni.** Si ottiene la disponibilità elevata dal traffico del bilanciamento del carico in un pool di server di applicazioni.

- **Livello del database.** Questa architettura di riferimento distribuisce una singola istanza del database di SAP HANA. Per la disponibilità elevata, distribuire più di un'istanza e usare la replica di sistema HANA (HSR) per implementare il failover manuale. Per abilitare il failover automatico, è richiesta un'estensione a disponibilità elevata per la distribuzione di Linux specifica.

### <a name="disaster-recovery-considerations"></a>Considerazioni sul ripristino di emergenza

Ogni livello usa una strategia diversa per fornire una protezione con ripristino di emergenza.

- **Server applicazioni.** I server dell'applicazione SAP non contengono dati aziendali. In Azure, una semplice strategia di ripristino di emergenza consiste nel creare i server dell'applicazione SAP in un'altra area. Nel caso di eventuali modifiche alla configurazione o aggiornamenti del kernel sul server dell'applicazione principale, le stesse modifiche devono essere copiate nelle macchine virtuali nell'area di ripristino di emergenza. Ad esempio, i file eseguibili del kernel copiati nelle macchine virtuali del ripristino di emergenza.

- **Servizi centrali SAP.** Inoltre, questo componente dello stack dell'applicazione SAP non salva i dati aziendali. È possibile compilare una macchina virtuale nell'area di ripristino di emergenza per eseguire il ruolo SCS. L'unico contenuto dal nodo SCS primario per la sincronizzazione è il contenuto di condivisione **/sapmnt**. Inoltre, se le modifiche di configurazione o gli aggiornamenti del kernel hanno luogo nei server SCS primari, essi devono essere ripetuti nell'SCS del ripristino di emergenza. Per sincronizzare i due server, è sufficiente usare un normale processo pianificato di copia per copiare **/sapmnt** sul lato del ripristino di emergenza. Per altre informazioni su come compilare, copiare ed eseguire il processo di failover del test, scaricare [SAP NetWeaver: Building a Hyper-V and Microsoft Azure–based Disaster Recovery Solution][sap-netweaver-dr] (SAP NetWeaver: Compilazione di una soluzione di ripristino di emergenza basata su Hyper-V e Microsoft) e fare riferimento a "4.3. Livello di SPOF SAP (ASCS)."

- **Livello del database.** Usare soluzioni di replica supportate da HANA, ad esempio HSR o Replica di archiviazione. 

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

SAP HANA dispone di una funzionalità di backup che usa l'infrastruttura di Azure sottostante. Per eseguire il backup del database SAP HANA in esecuzione nelle macchine virtuali di Azure, sia lo snapshot di SAP HANA sia lo snapshot di archiviazione di Azure vengono usati per garantire la coerenza dei file di backup. Per informazioni dettagliate, vedere [Guida del backup di SAP HANA in macchine virtuali di Azure][hana-backup] e [Domande frequenti sul servizio di Backup di Azure][backup-faq].

Azure offre diverse funzioni per [il monitoraggio e la diagnostica][monitoring] dell'infrastruttura generale. Inoltre, il monitoraggio avanzato delle macchine virtuali di Azure (Linux o Windows) viene gestito da Azure Operations Management Suite (OMS).

Per fornire le risorse di monitoraggio e le prestazioni del servizio basate su SAP dell'infrastruttura di SAP, usare l'estensione Azure Enhanced Monitoring SAP. Questa estensione aumenta le statistiche di monitoraggio nell'applicazione SAP per il monitoraggio del sistema operativo e le funzioni del Pannello di controllo DBA. 

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

SAP dispone del proprio motore di gestione di utenti (UME) per controllare l'accesso basato sui ruoli e l'autorizzazione all'interno dell'applicazione SAP. Per informazioni dettagliate, vedere [SAP HANA Security - An Overview][sap-security] (Sicurezza SAP HANA - Panoramica) (si richiede un account di SAP Service Marketplace per l'accesso).

Per la protezione dell'infrastruttura, i dati di protezione vengono salvaguardati quando in transito e quando inattivi. La sezione "Considerazioni relative alla sicurezza" di [SAP NetWeaver on Azure Virtual Machines (VMs) – Planning and Implementation Guide ][netweaver-on-azure] (SAP NetWeaver in macchine virtuali di Azure (VM) – Guida alla pianificazione e all'implementazione) inizia indirizzando la protezione di rete. La guida specifica anche le porte di rete che devono essere aperte sui firewall per consentire la comunicazione dell'applicazione. 

Per la crittografia dei dischi della macchina virtuale di Azure, è possibile usare [Crittografia dischi di Azure][disk-encryption]. La Crittografia dischi di Azure usa la funzionalità BitLocker di Windows e la funzionalità DM-Crypt di Linux per fornire la crittografia del volume per i dischi dati e del sistema operativo. La soluzione funziona anche con Azure Key Vault per consentire di controllare e gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione dell'insieme di credenziali delle chiavi. I dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

Per la crittografia dei dati a riposo SAP HANA, è consigliabile usare la tecnologia di crittografia nativa SAP HANA.

> [!NOTE]
> Non usare la crittografia di dati inattivi HANA con la crittografia del disco di Azure nello stesso server.

È consigliabile usare [gruppi di sicurezza di rete][nsg] (NSG) per limitare il traffico tra le varie subnet nella rete virtuale.

## <a name="deploy-the-solution"></a>Distribuire la soluzione 

Gli script di distribuzione per questa architettura di riferimento è disponibile in [GitHub][github].


### <a name="prerequisites"></a>Prerequisiti

- È necessario avere accesso al Centro Download del Software SAP per completare l'installazione.
 
- Installare la versione più recente di [Azure PowerShell][azure-ps]. 

- Questa distribuzione richiede 51 Core. Prima di distribuire, verificare che la sottoscrizione abbia una quota sufficiente per core della macchina virtuale. In caso contrario, usare il portale di Azure per inviare una richiesta di supporto per più quote.
 
- Questa distribuzione usa una macchina virtuale di serie GS. Controllare la disponibilità della serie GS per ogni area [qui][region-availability].

- Per stimare il costo di questa distribuzione, vedere il [Calcolatore dei prezzi di Azure][azure-pricing]. 
 
Questa architettura di riferimento distribuisce le macchine virtuali seguenti:

| Nome risorsa | Dimensioni macchina virtuale | Scopo  |
|---------------|---------|----------|
| `ra-sapApps-scs-vm1` ... `ra-sapApps-scs-vmN` | DS11v2 | Servizi centrali SAP |
| `ra-sapApps-vm1` ... `ra-sapApps-vmN` | DS11v2 | Applicazione di SAP NetWeaver |
| `ra-sap-wdp-vm1` ... `ra-sap-wdp-vmN` | DS11v2 | Dispatcher Web SAP |
| `ra-sap-data-vm1` | GS5 | Istanza di database SAP HANA |
| `ra-sap-jumpbox-vm1` | DS1V2 | Jumpbox |

Viene distribuita un'istanza singola SAP HANA. Per le macchine virtuali dell'applicazione, il numero di istanze da distribuire è specificato nei parametri del modello.

### <a name="deploy-sap-infrastructure"></a>Distribuire l'infrastruttura SAP

È possibile distribuire questa architettura in modo incrementale o in una sola volta. La prima volta è consigliabile una distribuzione incrementale, in modo tale da poter vedere cosa accade in ogni passaggio di distribuzione. Specificare l'incremento usando uno dei seguenti parametri di *modalità*

| Mode           | Risultato                                                                                                            |
|----------------|-----------------------------------------------------|
| infrastructure | Distribuisce l'infrastruttura di rete in Azure.        |
| workload       | Distribuisce i server SAP nella rete.             |
| tutti            | Implementa tutte le distribuzioni precedenti.              |

Per eliminare la soluzione, eseguire la procedura seguente:

1. Scaricare o clonare il [repository GitHub][github] nel computer locale.

2. Aprire una finestra di PowerShell e passare alla cartella `/sap/sap-hana/`.

3. Eseguire il cmdlet di PowerShell seguente. Per `<subscription id>`, usare l'ID della sottoscrizione di Azure. Per `<location>`, specificare un'area di Azure, come `eastus` o `westus`. Per `<mode>`, specificare una delle modalità elencate in precedenza.

    ```powershell
     .\Deploy-ReferenceArchitecture -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group> <mode>
    ```

4.  Quando richiesto, accedere all'account di Azure. 

Gli script di distribuzione possono richiedere diverse ore per essere completati, a seconda della modalità selezionata.

> [!WARNING]
> I file dei parametri comprendono una password hardcoded (`AweS0me@PW`) in diverse posizioni. Modificare questi valori prima di eseguire la distribuzione.
 
### <a name="configure-sap-applications-and-database"></a>Configurare le applicazioni e i database SAP

Dopo aver distribuito l'infrastruttura SAP, installare e configurare le applicazioni SAP e il database HANA nelle macchine virtuali come indicato di seguito.

> [!NOTE]
> Per istruzioni sull'installazione di SAP, è necessario disporre di un nome utente del portale di supporto SAP e di una password per eseguire il download delle [SAP installation guides][sap-guide] (Guide all'installazione di SAP).

1. Accedere al jumpbox (`ra-sap-jumpbox-vm1`). Si userà il jumpbox per accedere alle altre macchine virtuali. 

2.  Per ogni macchina virtuale denominata `ra-sap-wdp-vm1` ... `ra-sap-wdp-vmN`, accedere alla macchina virtuale, installare e configurare l'istanza di SAP Dispatcher Web eseguendo la procedura descritta nella wiki [Web Dispatcher Installation][sap-dispatcher-install] (Installazione di Web Dispatcher).

3.  Accedere alla macchina virtuale denominata `ra-sap-data-vm1`. Installare e configurare l'istanza del Database SAP HANA con [SAP HANA Server Installation and Update Guide][hana-guide] (Guida all'installazione e all'aggiornamento del server SAP HANA).

4. Per ogni macchina virtuale denominata `ra-sapApps-scs-vm1` ... `ra-sapApps-scs-vmN`, accedere alla macchina virtuale, installare e configurare SAP Central Services (SCS) usando le [SAP installation guides][sap-guide] (Guide all'installazione di SAP).

5.  Per ogni macchina virtuale denominata `ra-sapApps-vm1` ... `ra-sapApps-vmN`, accedere alla macchina virtuale, installare e configurare l'applicazione SAP NetWeaver usando le [SAP installation guides][sap-guide] (Guide all'installazione di SAP).

**_Collaboratori di questa architettura di riferimento_** &mdash; Rick Rainey, Ross Sponholtz, Ben Trinh

[azure-large-instances]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[azure-lb]: /azure/load-balancer/load-balancer-overview
[azure-pricing]: https://azure.microsoft.com/pricing/calculator/
[azure-ps]: /powershell/azure/overview
[backup-faq]: /azure/backup/backup-azure-backup-faq
[clustering]: https://blogs.msdn.microsoft.com/saponsqlserver/2015/05/20/clustering-sap-ascs-instance-using-windows-server-failover-cluster-on-microsoft-azure-with-sios-datakeeper-and-azure-internal-load-balancer/
[cool-blob-storage]: /azure/storage/storage-blob-storage-tiers
[disk-encryption]: /azure/security/azure-security-disk-encryption
[github]: https://github.com/mspnp/reference-architectures/tree/master/sap/sap-hana
[hana-backup]: /azure/virtual-machines/workloads/sap/sap-hana-backup-guide
[hana-guide]: https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.01/en-US/7eb0167eb35e4e2885415205b8383584.html
[hybrid-networking]: ../hybrid-networking/index.md
[logon-groups]: https://wiki.scn.sap.com/wiki/display/SI/ABAP+Logon+Group+based+Load+Balancing
[managed-disks]: /azure/storage/storage-managed-disks-overview
[monitoring]: /azure/architecture/best-practices/monitoring
[multiple-vm-nics]: /azure/virtual-machines/windows/multiple-nics
[netweaver-on-azure]: /azure/virtual-machines/workloads/sap/planning-guide
[nsg]: /azure/virtual-network/virtual-networks-nsg
[region-availability]: https://azure.microsoft.com/regions/services/
[running-SAP]: https://blogs.msdn.microsoft.com/saponsqlserver/2016/06/07/sap-on-sql-general-update-for-customers-partners-june-2016/
[sap-1943937]: https://launchpad.support.sap.com/#/notes/1943937
[sap-1928533]: https://launchpad.support.sap.com/#/notes/1928533
[sap-dispatcher]: https://help.sap.com/doc/saphelp_nw73ehp1/7.31.19/en-US/48/8fe37933114e6fe10000000a421937/frameset.htm
[sap-dispatcher-ha]: https://help.sap.com/doc/saphelp_nw73ehp1/7.31.19/en-US/48/9a9a6b48c673e8e10000000a42189b/frameset.htm
[sap-dispatcher-install]: https://wiki.scn.sap.com/wiki/display/SI/Web+Dispatcher+Installation
[sap-guide]: https://service.sap.com/instguides
[sap-ha]: https://support.sap.com/content/dam/SAAP/SAP_Activate/AGS_70.pdf
[sap-hana-on-azure]: https://azure.microsoft.com/services/virtual-machines/sap-hana/
[sap-netweaver-dr]: http://download.microsoft.com/download/9/5/6/956FEDC3-702D-4EFB-A7D3-2DB7505566B6/SAP%20NetWeaver%20-%20Building%20an%20Azure%20based%20Disaster%20Recovery%20Solution%20V1_5%20.docx
[sap-security]: https://archive.sap.com/documents/docs/DOC-62943
[visio-download]: https://archcenter.azureedge.net/cdn/SAP-HANA-architecture.vsdx
[vm-sizes-mem]: /azure/virtual-machines/windows/sizes-memory
[swd]: https://help.sap.com/doc/saphelp_nw70ehp2/7.02.16/en-us/48/8fe37933114e6fe10000000a421937/frameset.htm
[0]: ./images/sap-hana.png "Architettura di SAP HANA con Microsoft Azure"
