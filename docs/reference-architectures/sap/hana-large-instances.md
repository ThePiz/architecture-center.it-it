---
title: Eseguire SAP HANA in istanze Large di Azure
description: Procedure consolidate per l'esecuzione di SAP HANA in un ambiente a disponibilità elevata in istanze Large di Azure.
author: lbrader
ms.date: 05/16/2018
ms.openlocfilehash: 7605fa8a0012aaef3f7323c6f88614b640152e3b
ms.sourcegitcommit: bb348bd3a8a4e27ef61e8eee74b54b07b65dbf98
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2018
ms.locfileid: "34423059"
---
# <a name="run-sap-hana-on-azure-large-instances"></a>Eseguire SAP HANA in istanze Large di Azure

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di SAP HANA in Azure (istanze Large) con disponibilità elevata e ripristino di emergenza. Questa offerta, definita istanze Large di HANA, viene distribuita in server fisici nelle aree di Azure. 

![0][0]

> [!NOTE]
> La distribuzione di questa architettura di riferimento richiede una licenza adeguata dei prodotti SAP e altre tecnologie non Microsoft.

## <a name="architecture"></a>Architecture

L'architettura è costituita dai componenti dell'infrastruttura seguenti.

- **Rete virtuale**. Il servizio [Rete virtuale di Azure][vnet] collega in modo sicuro le risorse di Azure tra di loro ed è suddiviso in [subnet][subnet] distinte per ogni livello. I livelli dell'applicazione SAP sono distribuiti nelle macchine virtuali (VM) di Azure per la connessione al livello di database HANA che risiede in istanze Large.

- **Macchine virtuali**. Le macchine virtuali sono usate nel livello dell'applicazione SAP e nel livello dei servizi condivisi. Quest'ultimo include un jumpbox usato dagli amministratori per impostare le istanze Large di HANA e per fornire l'accesso ad altre macchine virtuali. 

- **Istanza Large di HANA**. SAP HANA è eseguito in un [server fisico][physical] certificato per rispettare gli standard SAP HANA Tailored Datacenter Integration (TDI). Questa architettura usa due istanze Large di HANA: un'unità di calcolo primaria e una secondaria. La disponibilità elevata a livello di dati è fornita dalla replica di sistema HANA (HSR).

- **Coppia a disponibilità elevata**. Un gruppo di blade server di istanze Large di HANA è gestito insieme per offrire ridondanza delle applicazioni e affidabilità. 

- **MSEE (Microsoft Enterprise Edge)**. MSEE è un punto di connessione da un provider di connettività o dal perimetro della rete attraverso un circuito ExpressRoute. 

- **Schede di interfaccia di rete (NIC)**. Per abilitare la comunicazione, il server delle istanze Large di HANA offre quattro schede di rete virtuali per impostazione predefinita. Questa architettura richiede una scheda di rete per la comunicazione client, una seconda scheda di rete per la connettività da nodo a nodo richiesta da HSR, una terza scheda di rete per l'archiviazione delle istanze Large di HANA e una quarta scheda di rete per iSCSI, usato nel clustering a disponibilità elevata.
    
- **Archiviazione di NFS (Network File System)**. Il server [NFS][nfs] supporta la condivisione di file di rete che fornisce la persistenza dei dati protetta per l'istanza Large di HANA.

- **ExpressRoute.** [ExpressRoute][expressroute] è il servizio di rete Azure consigliato per la creazione di connessioni private, che non passano attraverso la rete Internet pubblica, tra una rete locale e le reti virtuali di Azure. Le macchine virtuali di Azure si connettono alle istanze Large di HANA usando un'altra connessione ExpressRoute. La connessione ExpressRoute tra la rete virtuale di Azure e le istanze Large di HANA è configurata come parte dell'offerta di Microsoft.

- **Gateway**. Il gateway di ExpressRoute è usato per la connessione della rete virtuale di Azure usata per il livello di applicazione SAP alla rete dell'istanza Large di HANA. Usare la SKU [Prestazioni elevate o Prestazioni extra][sku].

- **Ripristino di emergenza**. Su richiesta, la replica di archiviazione è supportata e verrà configurata dal sito primario al [sito di ripristino di emergenza][DR-site] che si trova in un'altra area.  
 
## <a name="recommendations"></a>Raccomandazioni
I requisiti possono variare: usare queste indicazioni solo come punto di partenza.

### <a name="hana-large-instances-compute"></a>Calcolo istanze Large di HANA
Le [istanze Large][physical] sono server fisici basati sull'architettura CPU Intel EX E7 e configurati in uno stamp di istanze Large, ovvero un set specifico di server o blade server. Un'unità di calcolo è uguale a un server o blade server e uno stamp è costituito da più server o blade server. All'interno di uno stamp di istanze Large, i server non sono condivisi e sono dedicati all'esecuzione della distribuzione di SAP HANA di un cliente.

Per le istanze Large di HANA sono disponibili vari SKU, che supportano fino a 20 TB di memoria per istanza singola (60 TB per più istanze) per S/4HANA o altri carichi di lavoro SAP HANA. Sono disponibili [due classi][classes] di server:

- Classe di tipo I: S72, S72m, S144, S144m, S192 e S192m

- Classe di tipo II: S384, S384m, S384xm, S576m, S768m e S960m

Ad esempio, lo SKU S72 viene fornito con 768 GB di RAM, 3 terabyte (TB) di archiviazione e 2 processori Intel Xeon (E7 8890 v3) con 36 core. Scegliere uno SKU che soddisfi i requisiti di dimensionamento determinati nelle fasi di architettura e progettazione. Assicurarsi sempre che il dimensionamento si applichi allo SKU corretto. Le funzionalità e i requisiti di distribuzione [variano in base al tipo][type] e la disponibilità [varia in base all'area][region]. È possibile inoltre passare da un SKU a uno SKU di dimensioni maggiori.

Microsoft aiuta a definire la configurazione delle istanze Large, ma è responsabilità del cliente verificare le impostazioni di configurazione del sistema operativo. Fare riferimento alle note SAP più recenti per la versione esatta di Linux in uso.

### <a name="storage"></a>Archiviazione
Il layout di archiviazione è implementato in base alla raccomandazione del TDI per SAP HANA. Le istanze Large di HANA sono fornite con una specifica configurazione di archiviazione per le specifiche TDI standard. È tuttavia possibile acquistare spazio di archiviazione aggiuntivo in incrementi di 1 TB. 

Per supportare i requisiti degli ambienti di importanza critica, tra cui il ripristino rapido, viene usata l'archiviazione NFS invece dell'archiviazione DAS. Il server di archiviazione NFS per le istanze Large di HANA è ospitato in un ambiente multi-tenant, dove i tenant sono separati e protetti tramite l'isolamento di calcolo, rete e archiviazione.

Per supportare la disponibilità elevata nel sito primario, usare layout di archiviazione diversi. Ad esempio, in una distribuzione multi-host con istanze aumentate, lo spazio di archiviazione è condiviso. Un'altra opzione di disponibilità elevata è la replica basata sull'applicazione, ad esempio HSR. Per il ripristino di emergenza, tuttavia, viene usata una replica di archiviazione basata su snapshot.

### <a name="networking"></a>Rete
Questa architettura usa reti sia virtuali che fisiche. La rete virtuale fa parte di IaaS di Azure e si connette a una rete fisica discreta di istanze Large di HANA attraverso circuiti [ExpressRoute][expressroute]. Un gateway tra più sedi locali connette i carichi di lavoro nella rete virtuale di Azure ai siti locali.

Le reti di istanze Large di HANA sono isolate tra di loro per motivi di sicurezza. Le istanze che si trovano in aree diverse non comunicano tra loro, tranne che per la replica di archiviazione dedicata. Tuttavia, per usare HSR, è necessaria la comunicazione tra aree diverse. Per abilitare HSR tra aree diverse, è possibile usare [tabelle di routing IP][ip] o proxy.

Tutte le reti virtuali di Azure che si connettono alle istanze Large di HANA in un'area possono essere [connesse][cross-connected] tramite ExpressRoute alle istanze Large di HANA in un'area secondaria.

ExpressRoute per istanze Large di HANA è incluso per impostazione predefinita durante il provisioning. Per l'installazione è necessario un layout di rete specifico, che includa i necessari intervalli di indirizzi CIDR e routing di dominio. Per i dettagli, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure][HLI-infrastructure].

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità
Per scegliere il piano adatto alle proprie esigenze, è possibile scegliere tra server di varie dimensioni disponibili per le istanze Large di HANA. I server sono classificati come [tipo I e tipo II][classes] e sono personalizzati per diversi carichi di lavoro. Scegliere una dimensione in grado di adattarsi alla crescita del carico di lavoro per i prossimi tre anni. Sono disponibili anche impegni di un anno.

Generalmente si usa una distribuzione multi-host con istanze aumentate per le distribuzioni BW/4HANA come strategia di partizionamento di database. Per aumentare le istanze, pianificare il posizionamento delle tabelle HANA prima dell'installazione. Dal punto di vista dell'infrastruttura, vi sono più host connessi a un volume di archiviazione condivisa, per consentire l'intervento rapido degli host in standby nel caso in cui uno dei nodi del ruolo di lavoro di calcolo nel sistema HANA smetta di funzionare.

S/4HANA e SAP Business Suite in HANA su un singolo blade server possono essere ridimensionati fino a 20 TB con una singola istanza di istanze Large di HANA.

Per gli scenari "vergini", è disponibile [SAP Quick Sizer][quick-sizer] per calcolare i requisiti di memoria dell'implementazione del software SAP in HANA. I requisiti di memoria per HANA aumentano in base al volume dei dati. Usare i dati di consumo di memoria corrente del sistema come base per stimare il consumo futuro e quindi individuare la dimensione delle istanze Large di HANA più adatta al proprio consumo.

Se si dispone già di distribuzioni SAP, SAP fornisce report utilizzabili per controllare i dati usati dai sistemi esistenti e calcolare i requisiti di memoria per un'istanza HANA. Per un esempio, vedere le note SAP seguenti: 

- SAP Note [1793345][sap-1793345] - Sizing for SAP Suite on HANA (Nota SAP 1793345: ridimensionamento della suite SAP in HANA)
- SAP Note [1872170][sap-1872170] - Suite on HANA and S/4 HANA sizing report (Nota SAP 1872170: report sul ridimensionamento della suite in HANA e S/4 HANA)
- SAP Note [2121330][sap-2121330] - FAQ: SAP BW on HANA sizing report (Nota SAP 2121330: domande frequenti: report sul ridimensionamento di SAP BW in HANA)
- SAP Note [1736976][sap-1736976] - Sizing report for BW on HANA (Nota SAP 1736976: report sul ridimensionamento per BW in HANA)
- SAP Note [2296290][sap-2296290] - New sizing report for BW on HANA (Nota SAP 2296290: nuovo report sul ridimensionamento per BW in HANA)

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

La ridondanza delle risorse è il tema generale nelle soluzioni di infrastruttura a disponibilità elevata. Per le organizzazioni che hanno contratti di servizio meno rigorosi, le macchine virtuali di Azure a istanza singola offrono un contratto di servizio per il tempo di attività. Per altre informazioni, vedere [Contratti di servizio di Azure](https://azure.microsoft.com/support/legal/sla/).

Per progettare e implementare una corretta strategia di [disponibilità elevata e ripristino di emergenza][hli-hadr], collaborare con SAP, con l'integratore di sistemi di fiducia o con Microsoft. Questa architettura segue il [
Contratto di servizio][sla] di Azure per HANA in Azure (istanze Large). Per valutare i propri requisiti di disponibilità, prendere in considerazione tutti i singoli punti di guasto, il livello desiderato di tempo di attività per i servizi e queste metriche comuni:

- Obiettivo tempo di ripristino (RTO) indica il tempo durante il quale il server di istanze Large di HANA non è disponibile.

- Obiettivo punto di ripristino (RPO) indica il periodo massimo tollerabile per il quale i dati dei clienti sono persi a causa di un guasto.

Per la disponibilità elevata, distribuire più di un'istanza in una coppia a disponibilità elevata e usare HSR in modalità sincrona per ridurre al minimo le perdite di dati e i tempi di inattività. In aggiunta a una configurazione a disponibilità elevata locale a due nodi, HSR supporta la replica multilivello, in cui un terzo nodo in un'area di Azure separata si registra nella replica secondaria della coppia HSR in cluster come destinazione di replica. In questo modo si forma un collegamento a margherita di repliche. Il failover al nodo di ripristino di emergenza è un processo manuale.

Quando si configura HSR per le istanze Large di HANA con failover automatico, è possibile richiedere al team di gestione dei servizi Microsoft di configurare un [dispositivo STONITH][stonith] per i server esistenti. 

## <a name="disaster-recovery-considerations"></a>Considerazioni sul ripristino di emergenza
Questa architettura supporta il [ripristino di emergenza][hli-dr] tra istanze Large di HANA in aree diverse di Azure. Vi sono due modi per supportare il ripristino di emergenza con istanze Large di HANA:

- Replica archiviazione. Il contenuto di archiviazione primario viene replicato costantemente nei sistemi di archiviazione di ripristino di emergenza remoti che sono disponibili nel server designato delle istanze Large di HANA per il ripristino di emergenza. Nella replica di archiviazione, il database HANA non viene caricato in memoria. Questa opzione di ripristino di emergenza è più semplice da un punto di vista amministrativo. Per determinare se si tratta di una strategia adeguata, confrontare il tempo di caricamento del database con il contratto di servizio sulla disponibilità. La replica di archiviazione consente inoltre di eseguire il recupero temporizzato. Se è configurato il ripristino di emergenza multifunzione (con ottimizzazione dei costi), è necessario acquistare ulteriore spazio di archiviazione delle stesse dimensioni nella posizione di ripristino di emergenza. Microsoft fornisce [script self-service di failover e snapshot di archiviazione][scripts] per il failover HANA come parte dell'offerta di istanze Large di HANA.

- HSR multilivello con una terza replica nell'area di ripristino di emergenza (dove viene caricato in memoria il database HANA). Questa opzione supporta un tempo di ripristino più rapido ma non supporta il recupero temporizzato. HSR richiede un sistema secondario. La replica di sistema HANA per il sito di ripristino di emergenza è gestita tramite proxy, ad esempio nginx, o tabelle IP. 

> [!NOTE]
> È possibile ottimizzare i costi di questa architettura di riferimento attraverso l'esecuzione in un ambiente a istanza singola. Questo [scenario di ottimizzazione dei costi](https://blogs.sap.com/2016/07/19/new-whitepaper-for-high-availability-for-sap-hana-cost-optimized-scenario/) è adatto a carichi di lavoro HANA non di produzione. 

## <a name="backup-considerations"></a>Considerazioni sul backup
In base alle esigenze aziendali, è possibile scegliere tra diverse opzioni disponibili per [il backup e il ripristino][hli-backup].

| Opzioni di backup                   | Vantaggi                                                                                                   | Svantaggi                                                       |
|---------------------------------|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| Backup HANA        | Funzionalità SAP nativa. Verifica predefinita della coerenza.                                                             | Tempi lunghi di backup e ripristino. Consumo di spazio di archiviazione. |
| Snapshot HANA      | Funzionalità SAP nativa. Backup e ripristino rapidi.                                                               |                                       |
| Snapshot di archiviazione   | Incluso nelle istanze Large di HANA. Ripristino di emergenza ottimizzato per le istanze Large di HANA. Supporto per il backup del volume di avvio. | 254 snapshot massimi per volume.                          |
| Backup dei log         | Necessario per il recupero temporizzato.                                                                   |                                                            |
| Altri strumenti di backup | Percorso di backup ridondante.                                                                             | Costi di licenza aggiuntivi.                                |

È inoltre disponibile un utile articolo in SapHanaTutorial.com, [Comparison between HANA Backup Options][sap-hana-tutorial] (Confronto tra le opzioni di backup HANA).

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità
Per monitorare le risorse delle istanze Large di HANA, come CPU, memoria, larghezza di banda della rete e spazio di archiviazione, sono disponibili strumenti nativi di Linux quali SAP HANA Studio, SAP HANA Cockpit, SAP Solution Manager e altri. Le istanze Large di HANA non dispongono di strumenti di monitoraggio integrati. Microsoft offre risorse di supporto per il [monitoraggio e la risoluzione dei problemi][hli-troubleshoot] in base ai requisiti dell'organizzazione e il team del supporto tecnico Microsoft può facilitare la risoluzione dei problemi tecnici. 

Se sono necessarie maggiori capacità di calcolo, è necessario ottenere uno SKU di dimensioni maggiori. 

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza
- Per impostazione predefinita, le istanze Large di HANA usano una crittografia di archiviazione basata su TDE (Transparent Data Encryption) per i dati inattivi.

- I dati in transito tra istanze Large di HANA e le macchine virtuali non vengono crittografati. Per crittografare i dati trasferiti, abilitare le funzioni di crittografia specifiche dell'applicazione. Vedere la nota SAP [2159014][sap-2159014] relativa alle domande frequenti sulla sicurezza SAP HANA.

- L'isolamento garantisce sicurezza tra i tenant nell'ambiente multi-tenant delle istanze Large di HANA. I tenant sono isolati mediante le rispettive VLAN.

- Una guida utile è disponibile in [Procedure consigliate per la sicurezza della rete di Azure][network-best-practices].

- Come con qualsiasi distribuzione, è consigliabile prevedere la [protezione avanzata del sistema operativo][os-hardening].

- Per motivi di sicurezza fisica, l'accesso ai data center di Azure è limitato al personale autorizzato. I clienti non possono accedere ai server fisici.

Per altre informazioni, vedere [SAP HANA Security - An Overview][sap-security] (Sicurezza SAP HANA - Panoramica). Per l'accesso è necessario un account SAP Service Marketplace.

## <a name="communities"></a>Community
Le community possono rispondere alle domande ed essere utili per configurare correttamente la distribuzione. Valutare gli aspetti seguenti:

* Blog [Running SAP Applications on the Microsoft Platform][running-sap-blog] (Esecuzione di applicazioni SAP nella piattaforma Microsoft)
* [Supporto della community di Azure][azure-forum]
* [Community SAP][sap-community]
* [SAP in Stack Overflow][stack-overflow]

[azure-forum]: https://azure.microsoft.com/support/forums/
[azure-large-instances]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[classes]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[cross-connected]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#network-considerations-for-disaster-recovery-with-hana-large-instances
[dr-site]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery
[expressroute]: /azure/architecture/reference-architectures/hybrid-networking/expressroute
[filter-network]: https://azure.microsoft.com/blog/multiple-vm-nics-and-network-virtual-appliances-in-azure/
[hli-dr]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#network-considerations-for-disaster-recovery-with-hana-large-instances
[hli-backup]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#backup-and-restore
[hli-hadr]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json
[hli-infrastructure]: /azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity
[hli-overview]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[hli-troubleshoot]: /azure/virtual-machines/workloads/sap/troubleshooting-monitoring
[ip]: https://blogs.msdn.microsoft.com/saponsqlserver/2018/02/10/setting-up-hana-system-replication-on-azure-hana-large-instances/
[network-best-practices]: /azure/security/azure-security-network-security-best-practices
[nfs]: /azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[os-hardening]: /azure/security/azure-security-iaas
[physical]: /azure/virtual-machines/workloads/sap/hana-overview-architecture
[planning]: /azure/vpn-gateway/vpn-gateway-plan-design
[protecting-sap]: https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/06/protecting-sap-systems-running-on-vmware-with-azure-site-recovery/
[ref-arch]: /azure/architecture/reference-architectures/
[running-SAP]: https://blogs.msdn.microsoft.com/saponsqlserver/2016/06/07/sap-on-sql-general-update-for-customers-partners-june-2016/
[region]: https://azure.microsoft.com/global-infrastructure/services/
[running-sap-blog]: https://blogs.msdn.microsoft.com/saponsqlserver/2017/05/04/sap-on-azure-general-update-for-customers-partners-april-2017/
[quick-sizer]: http://service.sap.com/quicksizing
[sap-1793345]: https://launchpad.support.sap.com/#/notes/1793345
[sap-1872170]: https://launchpad.support.sap.com/#/notes/1872170
[sap-2121330]: https://launchpad.support.sap.com/#/notes/2121330
[sap-2159014]: https://launchpad.support.sap.com/#/notes/2159014
[sap-1736976]: https://launchpad.support.sap.com/#/notes/1736976
[sap-2296290]: https://launchpad.support.sap.com/#/notes/2296290
[sap-community]: https://www.sap.com/community.html
[sap-hana-tutorial]: http://saphanatutorial.com/comparison-between-hana-backup-options/
[sap-security]: https://archive.sap.com/documents/docs/DOC-62943
[scripts]: /azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery
[sku]: /azure/expressroute/expressroute-about-virtual-network-gateways
[sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[stack-overflow]: http://stackoverflow.com/tags/sap/info
[stonith]: /azure/virtual-machines/workloads/sap/ha-setup-with-stonith
[subnet]: /azure/virtual-network/virtual-network-manage-subnet
[swd]: https://help.sap.com/doc/saphelp_nw70ehp2/7.02.16/en-us/48/8fe37933114e6fe10000000a421937/frameset.htm
[type]: /azure/virtual-machines/workloads/sap/hana-installation
[vnet]: /azure/virtual-network/virtual-networks-overview
[0]: ./images/sap-hana-large-instances.png "Architettura di SAP HANA mediante le istanze Large di Azure"
