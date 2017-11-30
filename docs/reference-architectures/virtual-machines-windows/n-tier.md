---
title: "Eseguire macchine virtuali Windows per un'architettura a più livelli"
description: "Come implementare un'architettura a più livelli in Azure, prestando particolare attenzione ai livelli di disponibilità, sicurezza, scalabilità e gestibilità."
author: MikeWasson
ms.date: 11/22/2016
pnp.series.title: Windows VM workloads
pnp.series.next: multi-region-application
pnp.series.prev: multi-vm
ms.openlocfilehash: f3c375f8fccc633d9525a8afbd11c13037265f4a
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="run-windows-vms-for-an-n-tier-application"></a>Eseguire macchine virtuali Windows per un'applicazione a più livelli

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di macchine virtuali Windows per un'applicazione a più livelli. [**Distribuire questa soluzione**.](#deploy-the-solution) 

![[0]][0]

*Scaricare un [file di Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architettura 

È possibile implementare un'architettura a più livelli in diversi modi. Il diagramma mostra una tipica applicazione Web a 3 livelli. Questa architettura è basata sull'architettura descritta in [Eseguire macchine virtuali con carico bilanciato per la scalabilità e la disponibilità][multi-vm]. I livelli Web e business usano macchine virtuali con carico bilanciato.

* **Set di disponibilità.** Creare un [set di disponibilità][azure-availability-sets] per ogni livello ed eseguire il provisioning di almeno due macchine virtuali in ogni livello. In questo modo le macchine virtuali sono idonee per un [contratto di servizio][vm-sla] di livello superiore.
* **Subnet.** Creare una subnet separata per ogni livello. Specificare l'intervallo di indirizzi e la subnet mask usando la notazione [CIDR]. 
* **Servizi di bilanciamento del carico.** Usare un [servizio di bilanciamento del carico con connessione Internet][load-balancer-external] per distribuire il traffico Internet in entrata al livello Web e un [servizio di bilanciamento del carico interno][load-balancer-internal] per distribuire il traffico di rete dal livello Web al livello business.
* **Jumpbox.** Detto anche [bastion host]. È una macchina virtuale sicura in rete che viene usata dagli amministratori per connettersi alle altre macchine virtuali. Il jumpbox ha un gruppo di sicurezza di rete (NSG) che consente il traffico remoto solo da Indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. L'NSG dovrebbe consentire il traffico RDP (Remote Desktop Protocol).
* **Monitoraggio.** Il software di monitoraggio, come [Nagios], [Zabbix] o [Icinga], può fornire informazioni dettagliate sul tempo di risposta, il tempo di attività delle macchine virtuali e l'integrità generale del sistema. Installare il software di monitoraggio in una macchina virtuale collocata in una subnet di gestione separata.
* **Gruppi di sicurezza di rete.** Usare i [gruppi di sicurezza di rete][nsg] (NSG) per limitare il traffico di rete nella rete virtuale. Ad esempio, nell'architettura a 3 livelli illustrata qui il livello database non accetta traffico dal front-end Web, solo dal livello business e dalla subnet di gestione.
* **Gruppo di disponibilità AlwaysOn di SQL Server.** Assicura disponibilità elevata al livello dati, abilitando la replica e il failover.
* **Server di Active Directory Domain Services.** Nelle versioni precedenti a Windows Server 2016 i gruppi di disponibilità AlwaysOn di SQL Server devono far parte di un dominio, in quanto questi gruppi dipendono dalla tecnologia WSFC (Windows Server Failover Cluster). A partire da Windows Server 2016 è possibile creare un cluster di failover senza Active Directory, nel qual caso i server di Active Directory Domain Services non sono necessari per questa architettura. Per altre informazioni, vedere [Novità di Clustering di failover in Windows Server 2016][wsfc-whats-new].

## <a name="recommendations"></a>Raccomandazioni

I requisiti della propria organizzazione potrebbero essere diversi da quelli dell'architettura descritta in questo articolo. Usare queste indicazioni come punto di partenza. 

### <a name="vnet--subnets"></a>Rete virtuale/subnet

Quando si crea la rete virtuale, determinare quanti indirizzi IP saranno necessari alle risorse in ogni subnet. Specificare una subnet mask e un intervallo di indirizzi della rete virtuale abbastanza ampio da contenere gli indirizzi IP necessari, usando la notazione [CIDR]. Usare uno spazio indirizzi che rientri nei [blocchi di indirizzi IP privati][private-ip-space] standard, ossia 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Scegliere un intervallo di indirizzi che non si sovrapponga alla rete locale, in caso occorra in seguito configurare un gateway tra la rete virtuale e la rete locale. Una volta creata la rete virtuale, non è più possibile cambiare l'intervallo di indirizzi.

Progettare le subnet tenendo presenti i requisiti di funzionamento e sicurezza. Tutte le macchine virtuali nello stesso livello o nello stesso ruolo dovrebbero far parte della stessa subnet, che può essere un limite di sicurezza. Per altre informazioni sulla progettazione di reti virtuali e subnet, vedere [Pianificare e progettare reti virtuali di Azure][plan-network].

Per ogni subnet occorre specificare lo spazio indirizzi usando la notazione CIDR. Ad esempio, "10.0.0.0/24" crea un intervallo di 256 indirizzi IP. Le macchine virtuali ne possono usare 251. Gli altri cinque sono riservati. Assicurarsi che gli intervalli di indirizzi non si sovrappongano nelle varie subnet. Vedere [Domande frequenti sulla rete virtuale di Azure][vnet faq].

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Usare le regole NSG per limitare il traffico fra livelli. Ad esempio, nell'architettura a 3 livelli illustrata sopra il livello Web non comunica direttamente con il livello database. Per applicare questo comportamento, il livello database deve bloccare il traffico in entrata dalla subnet del livello Web.  

1. Creare un gruppo di sicurezza di rete (NSG) e associarlo alla subnet del livello database.
2. Aggiungere una regola che rifiuti tutto il traffico in entrata dalla rete virtuale (usare il tag `VIRTUAL_NETWORK` nella regola). 
3. Aggiungere una regola con una priorità più alta che consenta il traffico in entrata dalla subnet del livello business. Questa regola esegue l'override di quella precedente e consente al livello business di comunicare con il livello database.
4. Aggiungere una regola che consenta il traffico in entrata dalla subnet del livello database. Questa regola permette la comunicazione tra le macchine virtuali nel livello database, condizione necessaria per la replica e il failover dei database.
5. Aggiungere una regola che consenta il traffico RDP dalla subnet del jumpbox. Questa regola consente agli amministratori di connettersi al livello database dal jumpbox.
   
   > [!NOTE]
   > Un NSG ha regole predefinite che consentono qualsiasi tipo di traffico in entrata dalla rete virtuale. Queste regole non possono essere eliminate, ma è possibile eseguirne l'override creando regole con priorità più alta.
   > 
   > 

### <a name="load-balancers"></a>Servizi di bilanciamento del carico

Il servizio di bilanciamento del carico esterno distribuisce il traffico Internet al livello Web. Creare un indirizzo IP pubblico per questo servizio. Vedere [Creazione del servizio di bilanciamento del carico Internet attraverso il portale di Azure][lb-external-create].

Il servizio di bilanciamento del carico interno distribuisce il traffico di rete dal livello Web al livello business. Per assegnare a questo servizio un indirizzo IP privato, creare una configurazione IP front-end e associarla alla subnet per il livello business. Vedere [Creare un servizio di bilanciamento del carico interno nel portale di Azure][lb-internal-create].

### <a name="sql-server-always-on-availability-groups"></a>Gruppi di disponibilità AlwaysOn di SQL Server

È consigliabile usare i [gruppi di disponibilità AlwaysOn][sql-alwayson] per ottenere la disponibilità elevata di SQL Server. Nelle versioni precedenti a Windows Server 2016 i gruppi di disponibilità AlwaysOn richiedono un controller di dominio e tutti i nodi del gruppo di disponibilità devono far parte dello stesso dominio Active Directory.

Altri livelli si connettono al database tramite un [listener del gruppo di disponibilità][sql-alwayson-listeners]. Il listener consente a un client SQL di connettersi senza conoscere il nome dell'istanza fisica di SQL Server. Le macchine virtuali che accedono al database devono far parte del dominio. Il client (in questo caso un altro livello) usa il DNS per risolvere il nome della rete virtuale del listener in indirizzi IP.

Configurare il gruppo di disponibilità AlwaysOn di SQL Server nel modo seguente:

1. Creare un cluster WSFC (Windows Server Failover Clustering), un gruppo di disponibilità AlwaysOn di SQL Server e una replica primaria. Per altre informazioni, vedere [Introduzione ai gruppi di disponibilità AlwaysOn (SQL Server)][sql-alwayson-getting-started]. 
2. Creare un servizio di bilanciamento del carico interno con un indirizzo privato statico.
3. Creare un listener del gruppo di disponibilità ed eseguire il mapping del nome DNS del listener all'indirizzo IP di un servizio di bilanciamento del carico interno. 
4. Creare una regola di bilanciamento del carico per la porta di ascolto di SQL Server (porta TCP 1433 per impostazione predefinita). La regola di bilanciamento del carico deve abilitare l'*indirizzo IP mobile*, detto anche Direct Server Return, per fare in modo che la macchina virtuale risponda direttamente al client, permettendo una connessione diretta alla replica primaria.
  
  > [!NOTE]
  > Quando l'indirizzo IP mobile è abilitato, il numero di porta front-end deve corrispondere al numero di porta back-end nella regola di bilanciamento del carico.
  > 
  > 

Quando un client SQL cerca di connettersi, il servizio di bilanciamento del carico indirizza la richiesta di connessione alla replica primaria. In caso di failover a un'altra replica, il servizio di bilanciamento del carico indirizza automaticamente le richieste successive a una nuova replica primaria. Per altre informazioni, vedere [Configure an ILB listener for SQL Server Always On Availability Groups][sql-alwayson-ilb] (Configurare un listener ILB per i gruppi di disponibilità AlwaysOn di SQL Server).

Durante un failover, le connessioni client esistenti vengono chiuse. Dopo il completamento del failover, le nuove connessioni verranno indirizzate alla nuova replica primaria.

Se l'applicazione esegue molte più letture che scritture, è possibile eseguire l'offload di alcune delle query di sola lettura a una replica secondaria. Vedere [Utilizzo di un listener per connettersi a una replica secondaria di sola lettura (routing di sola lettura)][sql-alwayson-read-only-routing].

Testare la distribuzione [forzando un failover manuale][sql-alwayson-force-failover] del gruppo di disponibilità.

### <a name="jumpbox"></a>Jumpbox

Poiché il jumpbox avrà requisiti di prestazioni minimi, selezionare dimensioni macchina virtuale ridotte per il jumpbox, ad esempio Standard A1. 

Creare un [indirizzo IP pubblico] per il jumpbox. Collocare il jumpbox nella stessa rete virtuale delle altre macchine virtuali, ma in una subnet di gestione separata.

Non consentire l'accesso RDP dalla rete Internet pubblica alle macchine virtuali che eseguono il carico di lavoro dell'applicazione. L'accesso RDP a queste macchine virtuali deve sempre passare attraverso il jumpbox. Un amministratore accede al jumpbox, quindi accede alle altre macchine virtuali dal jumpbox. Il jumpbox consente il traffico RDP da Internet, ma solo da indirizzi IP conosciuti e attendibili.

Per proteggere il jumpbox, creare un gruppo di sicurezza di rete (NSG) e applicarlo alla subnet del jumpbox. Aggiungere una regola NSG che consenta le connessioni RDP solo da un set di indirizzi IP pubblici attendibili. L'NSG può essere collegato alla subnet o alla scheda di interfaccia di rete del jumpbox. In questo caso è consigliabile collegarlo alla scheda di interfaccia di rete, in modo che il traffico RDP sia consentito solo al jumpbox, anche se si aggiungono altre macchine virtuali alla stessa subnet.

Configurare i gruppi di sicurezza di rete per le altre subnet per consentire il traffico RDP dalla subnet di gestione.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Al livello database, la presenza di più macchine virtuali non si traduce automaticamente in un database a disponibilità elevata. Per un database relazionale è in genere necessario usare la replica e il failover per ottenere la disponibilità elevata. Per SQL Server è consigliabile usare i [gruppi di disponibilità AlwaysOn][sql-alwayson]. 

Se occorre una disponibilità più elevata di quella fornita dal [contratto di servizio di Azure per le macchine virtuali][vm-sla], eseguire la replica dell'applicazione in due aree e usare Gestione traffico di Azure per il failover. Per altre informazioni, vedere [Eseguire macchine virtuali Windows in più aree per una disponibilità elevata][multi-dc].   

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Crittografare i dati sensibili inattivi e usare[Azure Key Vault][azure-key-vault] per gestire le chiavi di crittografia del database. Key Vault consente di archiviare le chiavi di crittografia in moduli di protezione hardware. Per altre informazioni, vedere [Configurare l'integrazione di Azure Key Vault per SQL Server in VM di Azure][sql-keyvault]. È anche consigliabile archiviare i segreti dell'applicazione, come le stringhe di connessione di database, in Key Vault.

Valutare l'aggiunta di un'appliance virtuale di rete per creare una rete perimetrale tra la rete Internet e la rete virtuale di Azure. Un'appliance virtuale di rete esegue attività correlate alla rete, ad esempio impostazione di un firewall, ispezione di pacchetti, controllo e routing personalizzato. Per altre informazioni, vedere [Implementazione di una rete perimetrale tra Azure e Internet][dmz].

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

I servizi di bilanciamento del carico distribuiscono il traffico ai livelli Web e business. Per ottenere la scalabilità orizzontale, aggiungere nuove istanze di macchine virtuali. La scalabilità dei livelli Web e business può essere ottenuta in modo indipendente, in base al carico. Per ridurre le possibili complicazioni causate dalla necessità di mantenere l'affinità dei client, le macchine virtuali nel livello Web devono essere senza stato. Anche le macchine virtuali che ospitano la logica di business devono essere senza stato.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Per semplificare la gestione dell'intero sistema, usare strumenti di amministrazione centralizzata come [Automazione di Azure][azure-administration], [Microsoft Operations Management Suite][operations-management-suite], [Chef][chef] o [Puppet][puppet]. Questi strumenti consentono di unire le informazioni di diagnostica e integrità acquisite da più macchine virtuali in modo da avere un quadro d'insieme generale del sistema.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura è disponibile in [GitHub][github-folder]. L'architettura viene distribuita in tre fasi. Per distribuire l'architettura, seguire questi passaggi: 

1. Fare clic sul pulsante seguente per iniziare la prima fase della distribuzione:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fvirtual-machines%2Fn-tier-windows%2FvirtualNetwork.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. Dopo avere aperto il collegamento nel portale di Azure, immettere i valori seguenti: 
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-ntier-sql-network-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
3. Nelle notifiche del portale di Azure verificare se è presente un messaggio che informa che la prima fase della distribuzione è completata.
4. Fare clic sul pulsante seguente per iniziare la seconda fase della distribuzione:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fvirtual-machines%2Fn-tier-windows%2Fworkload.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
5. Dopo avere aperto il collegamento nel portale di Azure, immettere i valori seguenti: 
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-ntier-sql-workload-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
6. Nelle notifiche del portale di Azure verificare se è presente un messaggio che informa che la seconda fase della distribuzione è completata.
7. Fare clic sul pulsante seguente per iniziare la terza fase della distribuzione:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fvirtual-machines%2Fn-tier-windows%2Fsecurity.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
8. Dopo avere aperto il collegamento nel portale di Azure, immettere i valori seguenti: 
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Usa esistente** e immettere `ra-ntier-sql-network-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
9. Nelle notifiche del portale di Azure verificare se è presente un messaggio che informa che la terza fase della distribuzione è completata.
10. I file dei parametri includono un nome utente e una password amministratore hardcoded. È consigliabile cambiarli immediatamente in tutte le macchine virtuali. Fare clic su ogni macchina virtuale nel portale di Azure, quindi fare clic su **Reimposta password** nel pannello **Supporto e risoluzione dei problemi**. Selezionare **Reimposta password** nella casella di riepilogo a discesa **Modalità**, quindi selezionare un nuovo **nome utente** e una nuova **password**. Fare clic sul pulsante **Aggiorna** per salvare il nuovo nome utente e la nuova password. 


<!-- links -->
[dmz]: ../dmz/secure-vnet-dmz.md
[multi-dc]: multi-region-application.md
[multi-vm]: multi-vm.md
[n-tier]: n-tier.md

[azure-administration]: /azure/automation/automation-intro
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-windows-manage-availability#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-key-vault]: https://azure.microsoft.com/services/key-vault
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/n-tier-windows
[lb-external-create]: /azure/load-balancer/load-balancer-get-started-internet-portal
[lb-internal-create]: /azure/load-balancer/load-balancer-get-started-ilb-arm-portal
[load-balancer-external]: /azure/load-balancer/load-balancer-internet-overview
[load-balancer-internal]: /azure/load-balancer/load-balancer-internal-overview
[nsg]: /azure/virtual-network/virtual-networks-nsg
[operations-management-suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[plan-network]: /azure/virtual-network/virtual-network-vnet-plan-design-arm
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[indirizzo IP pubblico]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[sql-alwayson]: https://msdn.microsoft.com/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/library/gg509118.aspx
[sql-alwayson-ilb]: /azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener
[sql-alwayson-listeners]: https://msdn.microsoft.com/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: /azure/virtual-machines/virtual-machines-windows-ps-sql-keyvault
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[vnet faq]: /azure/virtual-network/virtual-networks-faq
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[0]: ./images/n-tier-diagram.png "Architettura a più livelli con Microsoft Azure"