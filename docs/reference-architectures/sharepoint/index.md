---
title: Eseguire una farm di SharePoint Server 2016 a disponibilità elevata in Azure.
description: Procedure consolidate per la configurazione di una farm di SharePoint Server 2016 a disponibilità elevata in Azure.
author: njray
ms.date: 07/14/2018
ms.openlocfilehash: 6141c73dc8e71f5546395c12421c470e2d16cf78
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47428993"
---
# <a name="run-a-high-availability-sharepoint-server-2016-farm-in-azure"></a>Eseguire una farm di SharePoint Server 2016 a disponibilità elevata in Azure.

Questa architettura di riferimento mostra una serie di procedure comprovate per la configurazione di una farm di SharePoint Server 2016 a disponibilità elevata in Azure, usando la topologia MinRole e i gruppi di disponibilità di SQL Server Always On. La farm di SharePoint viene distribuita in una rete virtuale protetta senza presenza su Internet o endpoint con connessione Internet. [**Distribuire questa soluzione**.](#deploy-the-solution) 

![](./images/sharepoint-ha.png)

*Scaricare un [file Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architettura

Questa architettura si basa su quella illustrata in [Eseguire macchine virtuali Windows per un'applicazione a più livelli][windows-n-tier]. Distribuisce una farm di SharePoint Server 2016 a disponibilità elevata all'interno di una rete virtuale Azure. Questa architettura è adatta a un ambiente di test o di produzione, a un'infrastruttura ibrida SharePoint con Office 365 o come base per uno scenario di ripristino di emergenza.

Questa architettura è costituita dai componenti seguenti:

- **Gruppi di risorse**. Un [gruppo di risorse][resource-group] è un contenitore di risorse correlate di Azure. Un gruppo di risorse viene usato per i server SharePoint e un altro gruppo di risorse viene usato per i componenti dell'infrastruttura indipendenti dalle macchine virtuali, ad esempio la rete virtuale e i bilanciamenti del carico.

- **Rete virtuale**. Le macchine virtuali vengono distribuite in una rete virtuale con uno spazio degli indirizzi Intranet univoco. La rete virtuale viene ulteriormente suddivisa in subnet. 

- **Macchine virtuali (VM)**. Le macchine virtuali vengono distribuite nella rete virtuale e a tutte le macchine virtuali vengono assegnati indirizzi IP statici privati. Gli indirizzi IP statici sono consigliati per le macchine virtuali che eseguono SQL Server e SharePoint Server 2016, per evitare problemi relativi alla memorizzazione nella cache degli indirizzi IP e alle modifiche di indirizzi dopo un riavvio.

- **Set di disponibilità**. Inserire le macchine virtuali per ogni ruolo SharePoint in [set di disponibilità][availability-set] separati ed eseguire il provisioning di almeno due macchine virtuali (VM) per ogni ruolo. In questo modo le macchine virtuali sono idonee per un contratto di servizio di livello superiore. 

- **Servizio di bilanciamento del carico interno**. Il [bilanciamento del carico][load-balancer] distribuisce il traffico di richieste di SharePoint dalla rete locale ai server Web front-end della farm di SharePoint. 

- **Gruppi di sicurezza di rete (NSG)**. Per ogni subnet contenente macchine virtuali viene creato un [gruppo di sicurezza di rete][nsg]. Usare i gruppi di sicurezza di rete per limitare il traffico di rete all'interno della rete virtuale al fine di isolare le subnet. 

- **Gateway**. Il gateway fornisce una connessione tra la rete virtuale di Azure e la rete locale. La connessione può usare ExpressRoute o la VPN da sito a sito. Per altre informazioni, vedere [Connect an on-premises network to Azure][hybrid-ra] (Connettere una rete locale ad Azure).

- **Controller di dominio di Windows Server Active Directory (AD)**. Questa architettura di riferimento distribuisce i controller di dominio AD di Windows Server. Questi controller di dominio vengono eseguiti nella rete virtuale di Azure e hanno una relazione di trust con la foresta di Windows Server AD locale. Le richieste Web dei client per le risorse della farm di SharePoint vengono autenticate nella rete virtuale, anziché inviare questo traffico di autenticazione attraverso la connessione del gateway alla rete locale. In DNS, i record Intranet A o CNAME vengono creati in modo che gli utenti della Intranet possano risolvere il nome della farm di SharePoint all'indirizzo IP privato del bilanciamento del carico interno.

  SharePoint Server 2016 supporta anche l'uso di [Azure Active Directory Domain Services](/azure/active-directory-domain-services/). Azure AD Domain Services fornisce servizi di dominio gestiti, per non dover distribuire e gestire controller di dominio in Azure.

- **Gruppo di disponibilità Always On di SQL Server**. Per la disponibilità elevata del database SQL Server è consigliabile usare i [gruppi di disponibilità Always On di SQL Server][sql-always-on]. Per SQL Server vengono usate due macchine virtuali. Una contiene la replica di database primaria e l'altra contiene la replica secondaria. 

- **Macchina virtuale con maggioranza dei nodi**. Questa macchina virtuale consente al cluster di failover di stabilire il quorum. Per altre informazioni, vedere [Informazioni sulle configurazioni quorum in un cluster di failover][sql-quorum].

- **Server SharePoint**. I server SharePoint eseguono i ruoli Web front-end, memorizzazione nella cache, applicazione e ricerca. 

- **Jumpbox**. detto anche [bastion host][bastion-host]. È una macchina virtuale sicura in rete che viene usata dagli amministratori per connettersi alle altre macchine virtuali. Il jumpbox ha un gruppo di sicurezza di rete che consente il traffico remoto solo da indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. L'NSG dovrebbe consentire il traffico RDP (Remote Desktop Protocol).

## <a name="recommendations"></a>Consigli

I requisiti della propria organizzazione potrebbero essere diversi da quelli dell'architettura descritta in questo articolo. Usare queste indicazioni come punto di partenza.

### <a name="resource-group-recommendations"></a>Indicazioni sui gruppi di risorse

È consigliabile separare i gruppi di risorse in base al ruolo del server e disporre di un gruppo di risorse separato per i componenti di infrastruttura che sono risorse globali. In questa architettura le risorse di SharePoint formano un gruppo, mentre le risorse di Microsoft Server SQL e di altre utilità formano un altro gruppo.

### <a name="virtual-network-and-subnet-recommendations"></a>Indicazioni sulla rete virtuale e le subnet

Usare una subnet per ogni ruolo SharePoint, oltre a una subnet per il gateway e una per il jumpbox. 

La subnet per il gateway deve essere denominata *GatewaySubnet*. Assegnare lo spazio di indirizzi della subnet per il gateway usando l'ultima parte dello spazio di indirizzi della rete virtuale. Per altre informazioni, vedere [Connect an on-premises network to Azure using a VPN gateway][hybrid-vpn-ra] (Connettere una rete locale ad Azure usando un gateway VPN).

### <a name="vm-recommendations"></a>Indicazioni per le VM

Questa architettura richiede un minimo di 44 core:

- 8 server SharePoint su Standard_DS3_v2 (ognuno con 4 core) = 32 core
- 2 controller di dominio Active Directory su Standard_DS1_v2 (ognuno con 1 core) = 2 core
- 2 VM SQL Server su Standard_DS3_v2 = 8 core
- 1 maggioranza di nodi su Standard_DS1_v2 = 1 core
- 1 server di gestione su Standard_DS1_v2 = 1 core

Assicurarsi che la sottoscrizione di Azure disponga di una quota di core sufficiente per la distribuzione; in caso contrario la distribuzione avrà esito negativo. Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi][quotas]. 

Per tutti i ruoli di SharePoint tranne l'indicizzatore di ricerca, è consigliabile usare la dimensione di macchina virtuale [Standard_DS3_v2][vm-sizes-general]. La dimensione dell'indicizzatore di ricerca deve essere almeno [Standard_DS13_v2] [ vm-sizes-memory]. Per i test, i file dei parametri per questa architettura di riferimento indicano la dimensione DS3_v2 più piccola per il ruolo di indicizzatore di ricerca. Per una distribuzione di produzione, aggiornare i file dei parametri per l'uso della dimensione DS13 o superiore. Per altre informazioni, vedere i [requisiti hardware e software per SharePoint Server 2016][sharepoint-reqs]. 

Per le VM SQL Server è consigliabile un minimo di 4 core e 8 GB di RAM. I file dei parametri per questa architettura di riferimento indicano la dimensione DS3_v2. Per una distribuzione di produzione, potrebbe essere necessario specificare una dimensione di VM maggiore. Per altre informazioni, vedere [Pianificazione e configurazione dell'archiviazione e della capacità di SQL Server (SharePoint Server)](/sharepoint/administration/storage-and-sql-server-capacity-planning-and-configuration#estimate-memory-requirements). 
 
### <a name="nsg-recommendations"></a>Consigli per i gruppi di sicurezza di rete

È consigliabile disporre di un gruppo di sicurezza di rete per ogni subnet che include macchine virtuali per consentire l'isolamento delle subnet. Per configurare l'isolamento delle subnet, aggiungere regole dei gruppi di sicurezza di rete che definiscono il traffico in ingresso o in uscita consentito o negato per ogni subnet. Per altre informazioni, vedere [Filtrare il traffico di rete con gruppi di sicurezza di rete][virtual-networks-nsg]. 

Non assegnare un gruppo di sicurezza di rete alla subnet del gateway per evitare che il gateway smetta di funzionare. 

### <a name="storage-recommendations"></a>Indicazioni sull'archiviazione

La configurazione dell'archiviazione delle macchine virtuali nella farm deve corrispondere alle procedure consigliate appropriate usate per le distribuzioni locali. È necessario un disco separato per i log nei server SharePoint. I server SharePoint con i ruoli di indice di ricerca richiedono ulteriore spazio su disco per l'archiviazione dell'indice di ricerca. Per SQL Server la procedura standard consiste nel separare i dati e i log. Aggiungere altri dischi per l'archiviazione del backup di database e usare un disco separato per [tempdb][tempdb].

Per una migliore affidabilità, è consigliabile usare [Azure Managed Disks][managed-disks]. I dischi gestiti garantiscono che i dischi per le macchine virtuali all'interno di un set di disponibilità siano isolati per evitare singoli punti di guasto. 

> [!NOTE]
> Attualmente il modello di Resource Manager per questa architettura di riferimento non usa dischi gestiti. È previsto l'aggiornamento del modello per consentire l'utilizzo di dischi gestiti.

Usare i dischi gestiti Premium per tutte le macchine virtuali di SQL Server e SharePoint. È possibile usare i dischi gestiti Standard per il server con maggioranza dei nodi, i controller di dominio e il server di gestione. 

### <a name="sharepoint-server-recommendations"></a>Indicazioni sui server SharePoint

Prima di configurare la farm di SharePoint, accertarsi che sia disponibile un account di servizio di Windows Server Active Directory per ogni servizio. Per questa architettura, sono necessari i seguenti account a livello di dominio per isolare il privilegio per ogni ruolo:

- Account del servizio SQL Server
- Account utente per la configurazione
- Account della server farm
- Account del servizio di ricerca
- Account di accesso al contenuto
- Account per il pool di app Web
- Account per il pool di app di servizio
- Account utente con privilegi avanzati per la cache
- Account lettore con privilegi avanzati per la cache

Per soddisfare il requisito di supporto per la velocità effettiva del disco di almeno 200 MB al secondo, assicurarsi di pianificare l'architettura di ricerca. Vedere [Pianificare l'architettura di ricerca a livello aziendale in SharePoint Server 2013][sharepoint-search]. Seguire anche le istruzioni in [Best practices for crawling in SharePoint Server 2016][sharepoint-crawling] (Procedure consigliate per la ricerca per indicizzazione in SharePoint Server 2016).

Inoltre è possibile archiviare i dati dei componenti di ricerca in una partizione o in un volume di archiviazione separato con prestazioni elevate. Per ridurre il carico e migliorare la velocità effettiva, configurare gli account utente per la cache di oggetti che sono necessari in questa architettura. Dividere i file del sistema operativo Windows Server, i file di programma di SharePoint Server 2016 e i log di diagnostica in tre partizioni o volumi di archiviazione separati con prestazioni normali. 

Per altre informazioni su queste indicazioni, vedere [Account amministrativi e di servizio per la distribuzione iniziale in SharePoint Server 2016][sharepoint-accounts].


### <a name="hybrid-workloads"></a>Carichi di lavoro ibridi

Questa architettura di riferimento distribuisce una farm di SharePoint Server 2016 che può essere usata come [ambiente ibrido SharePoint][sharepoint-hybrid]&mdash;, ovvero estendendo SharePoint Server 2016 a Office 365 SharePoint Online. Se si dispone di Office Online Server, vedere [Office Web Apps and Office Online Server supportability in Azure][office-web-apps] (Supporto di Office Web Apps e Office Online Server in Azure).

Le applicazioni di servizio predefinite in questa distribuzione sono progettate per supportare carichi di lavoro ibridi. Tutti i carichi di lavoro di SharePoint Server 2016 e Office 365 possono essere distribuiti nella farm senza apportare modifiche all'infrastruttura di SharePoint con un'unica eccezione: l'applicazione del servizio di ricerca ibrida su cloud non deve essere distribuita nei server in cui è presente in hosting una topologia di ricerca esistente. È quindi necessario aggiungere una o più macchine virtuali basate sui ruoli di ricerca alla farm per supportare questo scenario ibrido.

### <a name="sql-server-always-on-availability-groups"></a>Gruppi di disponibilità Always On di SQL Server

Questa architettura usa le macchine virtuali SQL Server perché SharePoint Server 2016 non è compatibile con il database SQL di Azure. Per supportare la disponibilità elevata in SQL Server, è consigliabile usare i gruppi di disponibilità Always On, che specificano un set di database che effettuano insieme il failover, assicurando disponibilità elevata e possibilità di ripristino. In questa architettura di riferimento i database vengono creati durante la distribuzione, ma è necessario abilitare manualmente i gruppi di disponibilità Always On e aggiungere i database di SharePoint a un gruppo di disponibilità. Per altre informazioni, vedere [Creare il gruppo di disponibilità e aggiungere i database SharePoint][create-availability-group].

È consigliabile anche aggiungere un indirizzo IP di listener al cluster, ovvero l'indirizzo IP privato del bilanciamento del carico interno per le macchine virtuali SQL Server.

Per le dimensioni della macchina virtuale consigliate e per altre raccomandazioni sulle prestazioni per SQL Server in esecuzione in Azure, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure][sql-performance]. Inoltre seguire le indicazioni in [Procedure consigliate per SQL Server in una farm di SharePoint Server 2016][sql-sharepoint-best-practices].

È consigliabile che il server con maggioranza dei nodi risieda in un computer separato dai partner di replica. Il server consente al server partner di replica secondaria in una sessione in modalità a protezione elevata di stabilire se avviare un failover automatico. A differenza dei due partner, il server con maggioranza dei nodi non serve il database ma supporta il failover automatico. 

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Per aumentare i server esistenti, è sufficiente modificare le dimensioni della macchina virtuale. 

Con la funzionalità [MinRoles][minroles] in SharePoint Server 2016, è possibile scalare orizzontalmente i server in base al ruolo del server e rimuovere i server da un ruolo. Quando si aggiungono server a un ruolo, è possibile specificare uno dei ruoli singoli o uno dei ruoli combinati. Tuttavia, se si aggiungono server al ruolo di ricerca, è necessario anche riconfigurare la topologia di ricerca tramite PowerShell. È anche possibile convertire i ruoli usando MinRoles. Per altre informazioni, vedere [Gestione di una server farm MinRole in SharePoint Server 2016][sharepoint-minrole].

Si noti che SharePoint Server 2016 non supporta l'uso di set di scalabilità di macchine virtuali per la scalabilità automatica.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Questa architettura di riferimento supporta la disponibilità elevata all'interno di un'area di Azure perché ogni ruolo dispone almeno di due macchine virtuali distribuite in un set di disponibilità.

Per evitare un errore a livello di area, creare una farm separata per il ripristino di emergenza in un'area diversa di Azure. Gli obiettivi del tempo di ripristino (RTO) e gli obiettivi del punto di ripristino (RPO) determineranno i requisiti della configurazione. Per informazioni dettagliate, vedere [Choose a disaster recovery strategy for SharePoint 2016][sharepoint-dr] (Scegliere una strategia di ripristino di emergenza per SharePoint 2016). L'area secondaria deve essere un'*area abbinata* all'area primaria. Nel caso di un'interruzione su vasta scala, viene definita la priorità di ripristino di un'area per ogni coppia. Per altre informazioni, vedere [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure][paired-regions].

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Per usare e gestire i server, le server farm e i siti, seguire le procedure consigliate per le operazioni SharePoint. Per altre informazioni, vedere [Operazioni per SharePoint Server 2016][sharepoint-ops].

Le attività da prendere in considerazione per la gestione di SQL Server in un ambiente SharePoint possono differire da quelle considerate in genere per un'applicazione di database. Una procedura consigliata consiste nel backup settimanale completo di tutti i database SQL con backup incrementali ogni notte. Eseguire un backup dei log delle transazioni ogni 15 minuti. Un'altra procedura consiste nell'implementare le attività di manutenzione di SQL Server nei database disabilitando al contempo quelle SharePoint predefinite. Per altre informazioni, vedere [Storage and SQL Server capacity planning and configuration][sql-server-capacity-planning] (Pianificazione e configurazione dell'archiviazione e della capacità di SQL Server). 

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Gli account di servizio a livello di dominio usati per eseguire SharePoint Server 2016 richiedono i controller di dominio di Windows Server Active Directory per i processi di autenticazione e di aggiunta al dominio. A questo scopo non è possibile usare Azure Active Directory Domain Services. Per estendere l'infrastruttura di identità di Windows Server Active Directory già in uso in Intranet, questa architettura usa due controller di dominio di replica di Windows Server Active Directory di una foresta Windows Server Active Directory locale esistente.

Inoltre è sempre consigliabile pianificare la protezione avanzata della sicurezza. Altri indicazioni includono:

- Aggiungere regole ai gruppi di sicurezza di rete per isolare i ruoli e le subnet.
- Non assegnare indirizzi IP pubblici alle macchine virtuali.
- Per il rilevamento delle intrusioni e l'analisi dei payload, considerare l'uso di un dispositivo di rete virtuale davanti ai server Web front-end anziché un servizio di bilanciamento del carico interno di Azure.
- In alternativa, usare i criteri IPsec per la crittografia del traffico di testo non crittografato tra server. Se si esegue anche l'isolamento delle subnet, è possibile aggiornare le regole dei gruppi di sicurezza di rete per consentire il traffico IPsec.
- Installare gli agenti anti-malware per le macchine virtuali.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura di riferimento è disponibile in [GitHub][github]. Il completamento dell'intera distribuzione può richiedere alcune ore.

La distribuzione crea i seguenti gruppi di risorse nella sottoscrizione:

- ra-onprem-sp2016-rg
- ra-sp2016-network-rg

I file parametro di modello fanno riferimento a questi nomi; pertanto, se questi vengono modificati, aggiornare i file parametro in modo che corrispondano. 

I file dei parametri comprendono una password hardcoded in diverse posizioni. Modificare questi valori prima di eseguire la distribuzione.

### <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-solution"></a>Distribuire la soluzione 

1. Eseguire il comando seguente per distribuire una rete locale simulata.

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p onprem.json --deploy
    ```

2. Eseguire il comando seguente per distribuire la rete virtuale di Azure e il gateway VPN.

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p connections.json --deploy
    ```

3. Eseguire il comando seguente per distribuire il jumpbox, i controller di dominio di AD e le VM di SQL Server.

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure1.json --deploy
    ```

4. Eseguire il comando seguente per creare il cluster di failover e il gruppo di disponibilità. 

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure2-cluster.json --deploy

5. Run the following command to deploy the remaining VMs.

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure3.json --deploy
    ```

A questo punto, assicurarsi che sia possibile stabilire una connessione TCP dal front-end Web al servizio di bilanciamento del carico per il gruppo di disponibilità AlwaysOn di SQL Server. A questo scopo, seguire questa procedura:

1. Usare il portale di Azure per trovare la macchina virtuale denominata `ra-sp-jb-vm1` nel gruppo di risorse `ra-sp2016-network-rg`. Questa è la VM del jumpbox.

2. Fare clic su `Connect` per aprire una sessione di desktop remoto per la macchina virtuale. Usare la password specificata nel file parametro `azure1.json`.

3. Dalla sessione di Desktop remoto accedere a 10.0.5.4. Si tratta dell'indirizzo IP della macchina virtuale denominata `ra-sp-app-vm1`.

4. Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi al servizio di bilanciamento del carico.

    ```powershell
    Test-NetConnection 10.0.3.100 -Port 1433
    ```

L'output dovrebbe essere simile al seguente:

```powershell
ComputerName     : 10.0.3.100
RemoteAddress    : 10.0.3.100
RemotePort       : 1433
InterfaceAlias   : Ethernet 3
SourceAddress    : 10.0.0.132
TcpTestSucceeded : True
```

In caso di errore, usare il portale di Azure per riavviare la VM denominata `ra-sp-sql-vm2`. Dopo il riavvio della macchina virtuale, eseguire di nuovo il comando `Test-NetConnection`. Potrebbe essere necessario attendere circa un minuto dopo il riavvio della VM prima che venga stabilita la connessione. 

Completare quindi la distribuzione come indicato di seguito.

1. Eseguire il comando seguente per distribuire il nodo primario della farm di SharePoint.

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure4-sharepoint-server.json --deploy
    ```

2. Eseguire il comando seguente per distribuire la cache, le funzionalità di ricerca e il Web di SharePoint.

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure5-sharepoint-farm.json --deploy
    ```

3. Eseguire il comando seguente per creare le regole del gruppo di sicurezza di rete.

    ```bash
    azbb -s <subscription_id> -g ra-onprem-sp2016-rg -l <location> -p azure6-security.json --deploy
    ```

### <a name="validate-the-deployment"></a>Convalidare la distribuzione

1. Nel [portale di Azure][azure-portal] passare al gruppo di risorse `ra-onprem-sp2016-rg`.

2. Nell'elenco di risorse selezionare la risorsa di macchina virtuale denominata `ra-onpr-u-vm1`. 

3. Connettersi alla macchina virtuale, come descritto in [Connettersi alla macchina virtuale][connect-to-vm]. Il nome utente è `\onpremuser`.

5.  Quando viene stabilita la connessione remota alla macchina virtuale, aprire un browser nella macchina virtuale e passare a `http://portal.contoso.local`.

6.  Nella casella **Sicurezza di Windows** accedere al portale di SharePoint usando `contoso.local\testuser` per il nome utente.

Questo accesso crea un tunnel dal dominio Fabrikam.com usato dalla rete locale al dominio contoso.local usato dal portale SharePoint. Quando si apre il sito di SharePoint, verrà visualizzato il sito demo radice.

**_Collaboratori di questa architettura di riferimento_**&mdash; Joe Davies, Bob Fox, Neil Hodgkinson e Paul Stork

<!-- links -->

[availability-set]: /azure/virtual-machines/windows/manage-availability
[azure-portal]: https://portal.azure.com
[azure-ps]: /powershell/azure/overview
[azure-pricing]: https://azure.microsoft.com/pricing/calculator/
[bastion-host]: https://en.wikipedia.org/wiki/Bastion_host
[create-availability-group]: https://technet.microsoft.com/library/mt793548(v=office.16).aspx
[connect-to-vm]: /azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine
[github]: https://github.com/mspnp/reference-architectures
[hybrid-ra]: ../hybrid-networking/index.md
[hybrid-vpn-ra]: ../hybrid-networking/vpn.md
[load-balancer]: /azure/load-balancer/load-balancer-internal-overview
[managed-disks]: /azure/storage/storage-managed-disks-overview
[minroles]: https://technet.microsoft.com/library/mt346114(v=office.16).aspx
[nsg]: /azure/virtual-network/virtual-networks-nsg
[office-web-apps]: https://support.microsoft.com/help/3199955/office-web-apps-and-office-online-server-supportability-in-azure
[paired-regions]: /azure/best-practices-availability-paired-regions
[readme]: https://github.com/mspnp/reference-architectures/tree/master/sharepoint/sharepoint-2016
[resource-group]: /azure/azure-resource-manager/resource-group-overview
[quotas]: /azure/azure-subscription-service-limits
[sharepoint-accounts]: https://technet.microsoft.com/library/ee662513(v=office.16).aspx
[sharepoint-crawling]: https://technet.microsoft.com/library/dn535606(v=office.16).aspx
[sharepoint-dr]: https://technet.microsoft.com/library/ff628971(v=office.16).aspx
[sharepoint-hybrid]: https://aka.ms/sphybrid
[sharepoint-minrole]: https://technet.microsoft.com/library/mt743705(v=office.16).aspx
[sharepoint-ops]: https://technet.microsoft.com/library/cc262289(v=office.16).aspx
[sharepoint-reqs]: https://technet.microsoft.com/library/cc262485(v=office.16).aspx
[sharepoint-search]: https://technet.microsoft.com/library/dn342836.aspx
[sql-always-on]: /sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server
[sql-performance]: /azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance
[sql-server-capacity-planning]: https://technet.microsoft.com/library/cc298801(v=office.16).aspx
[sql-quorum]: https://technet.microsoft.com/library/cc731739(v=ws.11).aspx
[sql-sharepoint-best-practices]: https://technet.microsoft.com/library/hh292622(v=office.16).aspx
[tempdb]: /sql/relational-databases/databases/tempdb-database
[virtual-networks-nsg]: /azure/virtual-network/virtual-networks-nsg
[visio-download]: https://archcenter.blob.core.windows.net/cdn/Sharepoint-2016.vsdx
[vm-sizes-general]: /azure/virtual-machines/windows/sizes-general
[vm-sizes-memory]: /azure/virtual-machines/windows/sizes-memory
[windows-n-tier]: ../virtual-machines-windows/n-tier.md
