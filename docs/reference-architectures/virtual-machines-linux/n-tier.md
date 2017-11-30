---
title: "Eseguire macchine virtuali Linux per un'applicazione a più livelli in Azure"
description: "Come eseguire macchine virtuali Linux per un'architettura a più livelli in Microsoft Azure."
author: MikeWasson
ms.date: 11/22/2016
pnp.series.title: Linux VM workloads
pnp.series.next: multi-region-application
pnp.series.prev: multi-vm
ms.openlocfilehash: 673e090e306ffc421603371658c8273b10b980d4
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="run-linux-vms-for-an-n-tier-application"></a>Eseguire macchine virtuali Linux per un'applicazione a più livelli

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di macchine virtuali Linux per un'applicazione a più livelli. [**Distribuire questa soluzione**.](#deploy-the-solution)  

![[0]][0]

*Scaricare un [file di Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architettura

È possibile implementare un'architettura a più livelli in diversi modi. Il diagramma mostra una tipica applicazione Web a 3 livelli. Questa architettura è basata sull'architettura descritta in [Eseguire macchine virtuali con carico bilanciato per la scalabilità e la disponibilità][multi-vm]. I livelli Web e business usano macchine virtuali con carico bilanciato.

* **Set di disponibilità.** Creare un [set di disponibilità][azure-availability-sets] per ogni livello ed eseguire il provisioning di almeno due macchine virtuali in ogni livello.  In questo modo le macchine virtuali sono idonee per un [contratto di servizio][vm-sla] di livello superiore.
* **Subnet.** Creare una subnet separata per ogni livello. Specificare l'intervallo di indirizzi e la subnet mask usando la notazione [CIDR]. 
* **Servizi di bilanciamento del carico.** Usare un [servizio di bilanciamento del carico con connessione Internet][load-balancer-external] per distribuire il traffico Internet in entrata al livello Web e un [servizio di bilanciamento del carico interno][load-balancer-internal] per distribuire il traffico di rete dal livello Web al livello business.
* **Jumpbox.** Detto anche [bastion host]. È una macchina virtuale sicura in rete che viene usata dagli amministratori per connettersi alle altre macchine virtuali. Il jumpbox ha un gruppo di sicurezza di rete (NSG) che consente il traffico remoto solo da Indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Il gruppo di sicurezza di rete deve consentire il traffico SSH (Secure Shell).
* **Monitoraggio.** Il software di monitoraggio, come [Nagios], [Zabbix] o [Icinga], può fornire informazioni dettagliate sul tempo di risposta, il tempo di attività delle macchine virtuali e l'integrità generale del sistema. Installare il software di monitoraggio in una macchina virtuale collocata in una subnet di gestione separata.
* **Gruppi di sicurezza di rete.** Usare i [gruppi di sicurezza di rete][nsg] (NSG) per limitare il traffico di rete nella rete virtuale. Ad esempio, nell'architettura a 3 livelli illustrata qui il livello database non accetta traffico dal front-end Web, solo dal livello business e dalla subnet di gestione.
* **Database Apache Cassandra.** Assicura disponibilità elevata al livello dati, abilitando la replica e il failover.

## <a name="recommendations"></a>Raccomandazioni

I requisiti della propria organizzazione potrebbero essere diversi da quelli dell'architettura descritta in questo articolo. Usare queste indicazioni come punto di partenza. 

### <a name="vnet--subnets"></a>Rete virtuale/subnet

Quando si crea la rete virtuale, determinare quanti indirizzi IP saranno necessari alle risorse in ogni subnet. Specificare una subnet mask e un intervallo di indirizzi della rete virtuale abbastanza ampio da contenere gli indirizzi IP necessari usando la notazione [CIDR]. Usare uno spazio indirizzi che rientri nei [blocchi di indirizzi IP privati][private-ip-space] standard, ossia 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Scegliere un intervallo di indirizzi che non si sovrapponga alla rete locale, in caso occorra in seguito configurare un gateway tra la rete virtuale e la rete locale. Una volta creata la rete virtuale, non è più possibile cambiare l'intervallo di indirizzi.

Progettare le subnet tenendo presenti i requisiti di funzionamento e sicurezza. Tutte le macchine virtuali nello stesso livello o nello stesso ruolo dovrebbero far parte della stessa subnet, che può essere un limite di sicurezza. Per altre informazioni sulla progettazione di reti virtuali e subnet, vedere [Pianificare e progettare reti virtuali di Azure][plan-network].

Per ogni subnet occorre specificare lo spazio indirizzi usando la notazione CIDR. Ad esempio, "10.0.0.0/24" crea un intervallo di 256 indirizzi IP. Le macchine virtuali ne possono usare 251. Gli altri cinque sono riservati. Assicurarsi che gli intervalli di indirizzi non si sovrappongano nelle varie subnet. Vedere [Domande frequenti sulla rete virtuale di Azure][vnet faq].

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Usare le regole NSG per limitare il traffico fra livelli. Ad esempio, nell'architettura a 3 livelli illustrata sopra il livello Web non comunica direttamente con il livello database. Per applicare questo comportamento, il livello database deve bloccare il traffico in entrata dalla subnet del livello Web.  

1. Creare un gruppo di sicurezza di rete (NSG) e associarlo alla subnet del livello database.
2. Aggiungere una regola che rifiuti tutto il traffico in entrata dalla rete virtuale (usare il tag `VIRTUAL_NETWORK` nella regola). 
3. Aggiungere una regola con una priorità più alta che consenta il traffico in entrata dalla subnet del livello business. Questa regola esegue l'override di quella precedente e consente al livello business di comunicare con il livello database.
4. Aggiungere una regola che consenta il traffico in entrata dalla subnet del livello database. Questa regola permette la comunicazione tra le macchine virtuali nel livello database, condizione necessaria per la replica e il failover dei database.
5. Aggiungere una regola che consenta il traffico SSH dalla subnet del jumpbox. Questa regola consente agli amministratori di connettersi al livello database dal jumpbox.
   
   > [!NOTE]
   > Un NSG ha [regole predefinite][nsg-rules] che consentono qualsiasi tipo di traffico in entrata dalla rete virtuale. Queste regole non possono essere eliminate, ma è possibile eseguirne l'override creando regole di priorità più alta.
   > 
   > 

### <a name="load-balancers"></a>Servizi di bilanciamento del carico

Il servizio di bilanciamento del carico esterno distribuisce il traffico Internet al livello Web. Creare un indirizzo IP pubblico per questo servizio. Vedere [Creazione del servizio di bilanciamento del carico Internet attraverso il portale di Azure][lb-external-create].

Il servizio di bilanciamento del carico interno distribuisce il traffico di rete dal livello Web al livello business. Per assegnare a questo servizio un indirizzo IP privato, creare una configurazione IP front-end e associarla alla subnet per il livello business. Vedere [Creare un servizio di bilanciamento del carico interno nel portale di Azure][lb-internal-create].

### <a name="cassandra"></a>Cassandra

In ambiente di produzione è consigliabile usare [DataStax Enterprise][datastax], ma questi suggerimenti sono validi per qualsiasi edizione di Cassandra. Per altre informazioni sull'esecuzione di DataStax in Azure, vedere [DataStax Enterprise Deployment Guide for Azure][cassandra-in-azure] (Guida alla distribuzione di DataStax Enterprise per Azure). 

Inserire le macchine virtuali per un cluster Cassandra in un set di disponibilità in modo che le repliche di Cassandra vengano distribuite in più domini di errore e domini di aggiornamento. Per altre informazioni sui domini di errore e sui domini di aggiornamento, vedere [Gestire la disponibilità delle macchine virtuali Linux][azure-availability-sets]. 

Configurare tre domini di errore (il massimo) e 18 domini di aggiornamento per ogni set di disponibilità. In questo modo si fornisce il numero massimo di domini di aggiornamento che possono essere distribuiti uniformemente tra i domini di errore.   

Configurare i nodi in modo che riconoscano il rack. Eseguire il mapping dei domini di errore ai rack nel file `cassandra-rackdc.properties`.

Non è necessario un servizio di bilanciamento del carico davanti al cluster. Il client si connette direttamente a un nodo nel cluster.

### <a name="jumpbox"></a>Jumpbox

Poiché il jumpbox avrà requisiti di prestazioni minimi, selezionare dimensioni macchina virtuale ridotte per il jumpbox, ad esempio Standard A1. 

Creare un [indirizzo IP pubblico] per il jumpbox. Collocare il jumpbox nella stessa rete virtuale delle altre macchine virtuali, ma in una subnet di gestione separata.

Non consentire l'accesso SSH dalla rete Internet pubblica alle macchine virtuali che eseguono il carico di lavoro dell'applicazione. L'accesso SSH a queste macchine virtuali deve sempre passare attraverso il jumpbox. Un amministratore accede al jumpbox, quindi accede alle altre macchine virtuali dal jumpbox. Il jumpbox consente il traffico SSH da Internet, ma solo da indirizzi IP conosciuti e attendibili.

Per proteggere il jumpbox, creare un gruppo di sicurezza di rete (NSG) e applicarlo alla subnet del jumpbox. Aggiungere una regola NSG che consenta le connessioni SSH solo da un set di indirizzi IP pubblici attendibili. L'NSG può essere collegato alla subnet o alla scheda di interfaccia di rete del jumpbox. In questo caso è consigliabile collegarlo alla scheda di interfaccia di rete, in modo che il traffico SSH sia consentito solo al jumpbox, anche se si aggiungono altre macchine virtuali alla stessa subnet.

Configurare i gruppi di sicurezza di rete per le altre subnet per consentire il traffico SSH dalla subnet di gestione.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Inserire ogni livello o ruolo macchina virtuale in un set di disponibilità separato. 

Al livello database, la presenza di più macchine virtuali non si traduce automaticamente in un database a disponibilità elevata. Per un database relazionale è in genere necessario usare la replica e il failover per ottenere la disponibilità elevata.  

Se occorre una disponibilità più elevata di quella fornita dal [contratto di servizio di Azure per le macchine virtuali][vm-sla], eseguire la replica dell'applicazione in due aree e usare Gestione traffico di Azure per il failover. Per altre informazioni, vedere [Eseguire macchine virtuali Linux in più aree per una disponibilità elevata][multi-dc].  

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Valutare l'aggiunta di un'appliance virtuale di rete per creare una rete perimetrale tra la rete Internet pubblica e la rete virtuale di Azure. Un'appliance virtuale di rete esegue attività correlate alla rete, ad esempio impostazione di un firewall, ispezione di pacchetti, controllo e routing personalizzato. Per altre informazioni, vedere [Implementazione di una rete perimetrale tra Azure e Internet][dmz].

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

I servizi di bilanciamento del carico distribuiscono il traffico ai livelli Web e business. Per ottenere la scalabilità orizzontale, aggiungere nuove istanze di macchine virtuali. La scalabilità dei livelli Web e business può essere ottenuta in modo indipendente, in base al carico. Per ridurre le possibili complicazioni causate dalla necessità di mantenere l'affinità dei client, le macchine virtuali nel livello Web devono essere senza stato. Anche le macchine virtuali che ospitano la logica di business devono essere senza stato.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Per semplificare la gestione dell'intero sistema, usare strumenti di amministrazione centralizzata come [Automazione di Azure][azure-administration], [Microsoft Operations Management Suite][operations-management-suite], [Chef][chef] o [Puppet][puppet]. Questi strumenti consentono di unire le informazioni di diagnostica e integrità acquisite da più macchine virtuali in modo da avere un quadro d'insieme generale del sistema.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura è disponibile in [GitHub][github-folder]. L'architettura viene distribuita in tre fasi. Per distribuire l'architettura, seguire questi passaggi: 

1. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fvirtual-machines%2Fn-tier-linux%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. Dopo avere aperto il collegamento nel portale di Azure, immettere i valori seguenti: 
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-ntier-cassandra-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
3. Nelle notifiche del portale di Azure verificare se è presente un messaggio che informa che la distribuzione è completata.
4. I file dei parametri includono un nome utente e una password amministratore hardcoded. È consigliabile cambiarli immediatamente in tutte le macchine virtuali. Fare clic su ogni macchina virtuale nel portale di Azure, quindi fare clic su **Reimposta password** nel pannello **Supporto e risoluzione dei problemi**. Selezionare **Reimposta password** nella casella di riepilogo a discesa **Modalità**, quindi selezionare un nuovo **nome utente** e una nuova **password**. Fare clic sul pulsante **Aggiorna** per rendere permanenti il nuovo nome utente e la nuova password.

<!-- links -->
[multi-dc]: multi-region-application.md
[dmz]: ../dmz/secure-vnet-dmz.md
[multi-vm]: ./multi-vm.md
[naming conventions]: /azure/guidance/guidance-naming-conventions

[azure-administration]: /azure/automation/automation-intro
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-in-azure]: https://docs.datastax.com/en/datastax_enterprise/4.5/datastax_enterprise/install/installAzure.html
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/n-tier-linux
[lb-external-create]: /azure/load-balancer/load-balancer-get-started-internet-portal
[lb-internal-create]: /azure/load-balancer/load-balancer-get-started-ilb-arm-portal
[load-balancer-external]: /azure/load-balancer/load-balancer-internet-overview
[load-balancer-internal]: /azure/load-balancer/load-balancer-internal-overview
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-rules]: /azure/azure-resource-manager/best-practices-resource-manager-security#network-security-groups
[operations-management-suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[plan-network]: /azure/virtual-network/virtual-network-vnet-plan-design-arm
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[indirizzo IP pubblico]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[vnet faq]: /azure/virtual-network/virtual-networks-faq
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./images/n-tier-diagram.png "Architettura a più livelli con Microsoft Azure"

