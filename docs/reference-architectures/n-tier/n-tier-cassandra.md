---
title: Applicazione a più livelli con Apache Cassandra
description: Come eseguire macchine virtuali Linux per un'architettura a più livelli in Microsoft Azure.
author: MikeWasson
ms.date: 05/03/2018
ms.openlocfilehash: 7ee14088a2fae3cfc5c1119daf717236c75ecc6a
ms.sourcegitcommit: 58d93e7ac9a6d44d5668a187a6827d7cd4f5a34d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37142234"
---
# <a name="n-tier-application-with-apache-cassandra"></a>Applicazione a più livelli con Apache Cassandra

Questa architettura di riferimento illustra come distribuire macchine virtuali e una rete virtuale configurata per un'applicazione a più livelli tramite Apache Cassandra in Linux per il livello dati. [**Distribuire questa soluzione**.](#deploy-the-solution) 

![[0]][0]

*Scaricare un [file Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architecture 

L'architettura include i componenti seguenti:

* **Gruppo di risorse.** I [gruppi di risorse][resource-manager-overview] vengono usati per raggruppare le risorse in modo che possano essere gestite in base alla durata, al proprietario o ad altri criteri.

* **Rete virtuale e subnet.** Ogni macchina virtuale di Azure viene distribuita in una rete virtuale che può essere suddivisa in più subnet. Creare una subnet separata per ogni livello. 

* **Gruppi di sicurezza di rete.** Usare i [gruppi di sicurezza di rete][nsg] (NSG) per limitare il traffico di rete nella rete virtuale. Ad esempio, nell'architettura a 3 livelli illustrata qui il livello database non accetta traffico dal front-end Web, solo dal livello business e dalla subnet di gestione.

* **Macchine virtuali**. Per indicazioni su come configurare le macchine virtuali, vedere [Eseguire una VM Windows in Azure](./windows-vm.md) ed [Eseguire una VM Linux in Azure](./linux-vm.md).

* **Set di disponibilità.** Creare un [set di disponibilità][azure-availability-sets] per ogni livello ed eseguire il provisioning di almeno due macchine virtuali in ogni livello. In questo modo le macchine virtuali sono idonee per un [contratto di servizio][vm-sla] di livello superiore. 

* **Set di scalabilità di macchine virtuali** (non illustrato). Un [set di scalabilità di macchine virtuali][vmss] rappresenta un'alternativa all'uso di un set di disponibilità. Un set di scalabilità rende più facile aumentare il numero di istanze delle VM in un livello, manualmente o automaticamente in base a regole predefinite.

* **Servizi di bilanciamento del carico di Azure.** I [servizi di bilanciamento del carico][load-balancer] distribuiscono le richieste Internet in ingresso alle istanze delle macchine virtuali. Usare un [servizio di bilanciamento del carico pubblico][load-balancer-external] per distribuire il traffico Internet in ingresso al livello Web e un [servizio di bilanciamento del carico interno][load-balancer-internal] per distribuire il traffico di rete dal livello Web al livello business.

* **Indirizzo IP pubblico**. È necessario un indirizzo IP pubblico per consentire al servizio di bilanciamento del carico pubblico di ricevere il traffico Internet.

* **Jumpbox.** Detto anche [bastion host]. È una macchina virtuale sicura in rete che viene usata dagli amministratori per connettersi alle altre macchine virtuali. Il jumpbox ha un gruppo di sicurezza di rete (NSG) che consente il traffico remoto solo da Indirizzi IP pubblici inclusi in un elenco di indirizzi attendibili. Il gruppo di sicurezza di rete deve consentire il traffico SSH.

* **Database Apache Cassandra.** Assicura disponibilità elevata al livello dati, abilitando la replica e il failover.

* **DNS di Azure**. [DNS di Azure][azure-dns] è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.

## <a name="recommendations"></a>Raccomandazioni

I requisiti della propria organizzazione potrebbero essere diversi da quelli dell'architettura descritta in questo articolo. Usare queste indicazioni come punto di partenza. 

### <a name="vnet--subnets"></a>Rete virtuale/subnet

Quando si crea la rete virtuale, determinare quanti indirizzi IP saranno necessari alle risorse in ogni subnet. Specificare una subnet mask e un intervallo di indirizzi della rete virtuale abbastanza ampio da contenere gli indirizzi IP necessari, usando la notazione [CIDR]. Usare uno spazio indirizzi che rientri nei [blocchi di indirizzi IP privati][private-ip-space] standard, ossia 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Scegliere un intervallo di indirizzi che non si sovrapponga alla rete locale, in caso occorra in seguito configurare un gateway tra la rete virtuale e la rete locale. Una volta creata la rete virtuale, non è più possibile cambiare l'intervallo di indirizzi.

Progettare le subnet tenendo presenti i requisiti di funzionamento e sicurezza. Tutte le macchine virtuali nello stesso livello o nello stesso ruolo dovrebbero far parte della stessa subnet, che può essere un limite di sicurezza. Per altre informazioni sulla progettazione di reti virtuali e subnet, vedere [Pianificare e progettare reti virtuali di Azure][plan-network].

### <a name="load-balancers"></a>Servizi di bilanciamento del carico

Non esporre le macchine virtuali direttamente su Internet, ma assegnare un indirizzo IP privato a ogni macchina virtuale. I client si connettono usando l'indirizzo IP del servizio di bilanciamento del carico pubblico.

Definire regole di bilanciamento del carico per indirizzare il traffico di rete alle macchine virtuali. Ad esempio, per consentire il traffico HTTP, creare una regola che esegua il mapping della porta 80 dalla configurazione front-end alla porta 80 sul pool di indirizzi back-end. Quando un client invia una richiesta HTTP alla porta 80, il servizio di bilanciamento del carico consente di selezionare un indirizzo IP back-end usando un [algoritmo di hash][load-balancer-hashing] che include l'indirizzo IP di origine. In tal modo, le richieste client vengono distribuite tra tutte le macchine virtuali.

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Usare le regole NSG per limitare il traffico fra livelli. Ad esempio, nell'architettura a 3 livelli illustrata sopra il livello Web non comunica direttamente con il livello database. Per applicare questo comportamento, il livello database deve bloccare il traffico in entrata dalla subnet del livello Web.  

1. Rifiutare tutto il traffico in ingresso dalla rete virtuale. (usare il tag `VIRTUAL_NETWORK` nella regola). 
2. Consentire il traffico in ingresso dalla subnet del livello business.  
3. Consentire il traffico in ingresso dalla subnet del livello database. Questa regola consente la comunicazione tra le macchine virtuali del database, condizione necessaria per la replica e il failover del database.
4. Consentire il traffico SSH (porta 22) dalla subnet del jumpbox. Questa regola consente agli amministratori di connettersi al livello database dal jumpbox.

Creare le regole 2 &ndash; 4 con priorità più alta rispetto alla prima regola, in modo da eseguirne l'override.

### <a name="cassandra"></a>Cassandra

In ambiente di produzione è consigliabile usare [DataStax Enterprise][datastax], ma questi suggerimenti sono validi per qualsiasi edizione di Cassandra. Per altre informazioni sull'esecuzione di DataStax in Azure, vedere [DataStax Enterprise Deployment Guide for Azure][cassandra-in-azure] (Guida alla distribuzione di DataStax Enterprise per Azure). 

Inserire le macchine virtuali per un cluster Cassandra in un set di disponibilità in modo che le repliche di Cassandra vengano distribuite in più domini di errore e domini di aggiornamento. Per altre informazioni sui domini di errore e sui domini di aggiornamento, vedere [Gestire la disponibilità delle macchine virtuali Linux][azure-availability-sets]. 

Configurare tre domini di errore (il massimo) e 18 domini di aggiornamento per ogni set di disponibilità. In questo modo si fornisce il numero massimo di domini di aggiornamento che possono essere distribuiti uniformemente tra i domini di errore.   

Configurare i nodi in modo che riconoscano il rack. Eseguire il mapping dei domini di errore ai rack nel file `cassandra-rackdc.properties`.

Non è necessario un servizio di bilanciamento del carico davanti al cluster. Il client si connette direttamente a un nodo nel cluster.

Per la disponibilità elevata, distribuire Cassandra in più aree di Azure. All'interno di ogni area, i nodi sono configurati in modo che riconoscano il rack con domini di errore e di aggiornamento, in modo da garantire la resilienza all'interno dell'area.


### <a name="jumpbox"></a>Jumpbox

Non consentire l'accesso SSH dalla rete Internet pubblica alle macchine virtuali che eseguono il carico di lavoro dell'applicazione. L'accesso SSH a queste macchine virtuali deve passare attraverso il jumpbox. Un amministratore accede al jumpbox, quindi accede alle altre macchine virtuali dal jumpbox. Il jumpbox consente il traffico SSH da Internet, ma solo da indirizzi IP conosciuti e attendibili.

Il jumpbox ha requisiti di prestazioni minimi, quindi selezionare una macchina virtuale di piccole dimensioni. Creare un [indirizzo IP pubblico] per il jumpbox. Collocare il jumpbox nella stessa rete virtuale delle altre macchine virtuali, ma in una subnet di gestione separata.

Per proteggere il jumpbox aggiungere una regola del gruppo di sicurezza di rete che consenta le connessioni SSH solo da un set di indirizzi IP pubblici attendibili. Configurare i gruppi di sicurezza di rete per le altre subnet per consentire il traffico SSH dalla subnet di gestione.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

I [set di scalabilità di macchine virtuali][vmss] consentono di distribuire e gestire un set di macchine virtuali identiche. I set di scalabilità supportano il ridimensionamento automatico in base alle metriche delle prestazioni. Di pari passo con l'aumento del carico sulle macchine virtuali, vengono aggiunte automaticamente altre macchine virtuali al servizio di bilanciamento del carico. Prendere in considerazione i set di scalabilità se è necessario aumentare rapidamente le istanze delle macchine virtuali o se si necessita del ridimensionamento automatico.

Esistono due modi per configurare le macchine virtuali distribuite in un set di scalabilità:

- Usare le estensioni per configurare la macchina virtuale dopo il provisioning. Con questo approccio, le nuove istanze delle macchine virtuali possono richiedere più tempo per l'avvio di una macchina virtuale senza estensioni.

- Distribuire un [disco gestito](/azure/storage/storage-managed-disks-overview) con un'immagine del disco personalizzata. Questa opzione può risultare più veloce da distribuire. Tuttavia, richiede che l'immagine sia mantenuta aggiornata.

Per altre considerazioni, vedere [Considerazioni sulla progettazione per i set di scalabilità][vmss-design].

> [!TIP]
> Quando si usa una soluzione di ridimensionamento automatico, occorre testarla in anticipo con carichi di lavoro a livello di produzione.

Ogni sottoscrizione di Azure ha limiti predefiniti, tra cui il numero massimo di macchine virtuali per area. È possibile aumentare il limite presentando una richiesta di supporto. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi][subscription-limits].

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Se non si usano i set di scalabilità di macchine virtuali, inserire le macchine virtuali dello stesso livello in un set di disponibilità. Creare almeno due macchine virtuali nel set di disponibilità in modo da supportare il [contratto di servizio relativo alla disponibilità per le macchine virtuali di Azure][vm-sla]. Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali][availability-set]. 

Il servizio di bilanciamento del carico usa i [probe di integrità][health-probes] per monitorare la disponibilità delle istanze delle macchine virtuali. Se un probe non riesce a raggiungere un'istanza entro il periodo di timeout, il bilanciamento del carico interrompe l'invio di traffico a quella macchina virtuale. Tuttavia, il servizio di bilanciamento del carico continuerà a eseguire la verifica e, se la macchina virtuale diventa nuovamente disponibile, il servizio di bilanciamento del carico riprenderà a inviare il traffico a quella macchina virtuale.

Ecco alcune raccomandazioni per i probe di integrità del servizio di bilanciamento del carico:

* I probe possono testare protocolli HTTP o TCP. Se le macchine virtuali eseguono un server HTTP, creare un probe HTTP. In caso contrario, creare un probe TCP.
* Per un probe HTTP, specificare il percorso di un endpoint HTTP. Il probe controlla una risposta HTTP 200 da questo percorso. Può trattarsi del percorso radice ("/") o di un endpoint di monitoraggio dell'integrità che implementa una logica personalizzata per controllare l'integrità dell'applicazione. L'endpoint deve consentire richieste HTTP anonime.
* Il probe viene inviato da un [indirizzo IP noto][health-probe-ip], 168.63.129.16. Verificare di non bloccare il traffico da o verso questo indirizzo IP in eventuali criteri del firewall o regole del gruppo di sicurezza di rete.
* Usare i [log dei probe di integrità][health-probe-log] per visualizzare lo stato dei probe di integrità. Abilitare la registrazione nel portale di Azure per ogni servizio di bilanciamento del carico. I log vengono scritti in Archiviazione BLOB di Azure. Il log mostra il numero di macchine virtuali sul back-end che non ricevono traffico di rete a causa di risposte del probe non riuscite.

Per il cluster Cassandra, gli scenari di failover da prendere in considerazione dipendono dai livelli di coerenza usati dall'applicazione, nonché dal numero di repliche usate. Per i livelli di coerenza e l'uso in Cassandra, vedere [Configuring data consistency][cassandra-consistency] (Configurazione della coerenza dei dati) e [Cassandra: How many nodes are talked to with Quorum?][cassandra-consistency-usage] (Cassandra: con quanti nodi si stabilisce una comunicazione con Quorum). La disponibilità dei dati in Cassandra è determinata dal livello di coerenza usato dall'applicazione e dal meccanismo di replica. Per la replica in Cassandra, vedere [Data Replication in NoSQL Databases Explained][cassandra-replication] (Spiegazione della replica dei dati nei database NoSQL).

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Le reti virtuali sono un limite di isolamento del traffico in Azure. Le macchine virtuali in una rete virtuale possono comunicare direttamente con quelle in una rete virtuale diversa. Le macchine virtuali all'interno della stessa rete virtuale possono comunicare, a meno che non si creino [gruppi di sicurezza di rete][nsg] per limitare il traffico. Per altre informazioni, vedere [Servizi cloud Microsoft e sicurezza di rete][network-security].

Per il traffico Internet in ingresso, le regole di bilanciamento del carico definiscono il tipo di traffico che può raggiungere il back-end. Tuttavia, le regole di bilanciamento del carico non supportano gli elenchi di indirizzi IP attendibili, pertanto se si vogliono aggiungere determinati indirizzi IP pubblici a un elenco di indirizzi attendibili, aggiungere un gruppo di sicurezza di rete alla subnet.

Valutare l'aggiunta di un'appliance virtuale di rete per creare una rete perimetrale tra la rete Internet e la rete virtuale di Azure. Un'appliance virtuale di rete esegue attività correlate alla rete, ad esempio impostazione di un firewall, ispezione di pacchetti, controllo e routing personalizzato. Per altre informazioni, vedere [Implementazione di una rete perimetrale tra Azure e Internet][dmz].

Crittografare i dati sensibili inattivi e usare[Azure Key Vault][azure-key-vault] per gestire le chiavi di crittografia del database. Key Vault consente di archiviare le chiavi di crittografia in moduli di protezione hardware. È anche consigliabile archiviare in Key Vault i segreti dell'applicazione, ad esempio le stringhe di connessione di database.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura di riferimento è disponibile in [GitHub][github-folder]. 

### <a name="prerequisites"></a>prerequisiti

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-solution-using-azbb"></a>Distribuire la soluzione mediante azbb

Per distribuire le macchine virtuali Linux per un'architettura di riferimento per applicazioni a più livelli, seguire questi passaggi:

1. Passare alla cartella `virtual-machines\n-tier-linux` per il repository clonato nel passaggio 1 dei prerequisiti nella sezione precedente.

2. Il file di parametri specifica un nome utente amministratore predefinito e una password per ogni macchina virtuale nella distribuzione. È necessario modificare questi elementi prima di distribuire l'architettura di riferimento. Aprire il file `n-tier-linux.json` e sostituire ogni campo **adminUsername** e **adminPassword** con le nuove impostazioni.   Salvare il file.

3. Distribuire l'architettura di riferimento usando lo strumento da riga di comando **azbb**, come mostrato di seguito.

   ```bash
   azbb -s <your subscription_id> -g <your resource_group_name> -l <azure region> -p n-tier-linux.json --deploy
   ```

Per altre informazioni sulla distribuzione di questa architettura di riferimento di esempio usando blocchi predefiniti di Azure, visitare il [repository GitHub][git].

<!-- links -->
[dmz]: ../dmz/secure-vnet-dmz.md
[multi-vm]: ./multi-vm.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-administration]: /azure/automation/automation-intro
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[azure-cli-2]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-dns]: /azure/dns/dns-overview
[azure-key-vault]: https://azure.microsoft.com/services/key-vault

[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2

[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[git]: https://github.com/mspnp/template-building-blocks
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
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[vnet faq]: /azure/virtual-network/virtual-networks-faq
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./images/n-tier-cassandra.png "Architettura a più livelli con Microsoft Azure"

[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview 
[vmss]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[load-balancer]: /azure/load-balancer/load-balancer-get-started-internet-arm-cli
[load-balancer-hashing]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[vmss-design]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview
[subscription-limits]: /azure/azure-subscription-service-limits
[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[health-probes]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[health-probe-log]: /azure/load-balancer/load-balancer-monitor-log
[health-probe-ip]: /azure/virtual-network/virtual-networks-nsg#special-rules
[network-security]: /azure/best-practices-network-security