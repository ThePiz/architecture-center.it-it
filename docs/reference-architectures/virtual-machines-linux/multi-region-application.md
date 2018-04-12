---
title: Eseguire macchine virtuali Linux in più aree di Azure per una disponibilità elevata
description: Come distribuire le macchine virtuali in più aree in Azure per la disponibilità elevata e la resilienza.
author: MikeWasson
ms.date: 11/22/2016
pnp.series.title: Linux VM workloads
pnp.series.prev: n-tier
ms.openlocfilehash: 07ccf44f28203e6d5001475b47adce01437e9600
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="run-linux-vms-in-multiple-regions-for-high-availability"></a>Eseguire macchine virtuali Linux in più aree per una disponibilità elevata

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di un'applicazione a più livelli in più aree di Azure, per ottenere disponibilità e un'infrastruttura di ripristino di emergenza affidabile. 

![[0]][0]

*Scaricare un [file Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architecture 

Questa architettura si basa su quella illustrata in [Eseguire macchine virtuali Linux per un'applicazione a più livelli](n-tier.md). 

* **Aree primarie e secondarie**. Usare due aree per ottenere una maggiore disponibilità: una è l'area primaria, l'altra è per il failover.
* **DNS di Azure**. [DNS di Azure][azure-dns] è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.
* **Gestione traffico di Azure**. [Gestione traffico][traffic-manager] indirizza le richieste in ingresso a una delle aree. Durante il normale funzionamento, le richieste vengono indirizzate all'area primaria. Se tale area non è più disponibile, Gestione traffico effettua il failover all'area secondaria. Per altre informazioni, vedere la sezione [Configurazione di Gestione traffico](#traffic-manager-configuration).
* **Gruppi di risorse**. Creare [gruppi di risorse][resource groups] distinti per l'area primaria, l'area secondaria e Gestione traffico. In questo modo si ottiene la flessibilità necessaria per gestire ogni area come un'unica raccolta di risorse. Ad esempio, è possibile ridistribuire un'area, senza rendere non disponibile l'altra. [Collegare i gruppi di risorse][resource-group-links], in modo che sia possibile eseguire una query per elencare tutte le risorse per l'applicazione.
* **Reti virtuali**. Creare una rete virtuale distinta per ogni area. Assicurarsi che gli spazi degli indirizzi non si sovrappongano.
* **Apache Cassandra**. Distribuire Cassandra nei data center tra le aree di Azure per una disponibilità elevata. All'interno di ogni area, i nodi sono configurati in modo che riconoscano il rack con domini di errore e di aggiornamento, in modo da garantire la resilienza all'interno dell'area.

## <a name="recommendations"></a>Raccomandazioni

Un'architettura di tipo multi-area può fornire una maggiore disponibilità rispetto alla distribuzione in un'unica area. Se un'interruzione a livello di area interessa l'area primaria, è possibile usare [Gestione traffico][traffic-manager] per effettuare il failover all'area secondaria. Questa architettura è utile anche in caso di errore di un singolo sottosistema dell'applicazione.

Sono disponibili diversi approcci generali per ottenere una disponibilità elevata tra le diverse aree geografiche:   

* Attivo/passivo con hot standby. Il traffico viene indirizzato a un'area, mentre l'altra attende in modalità hot standby. Hot standby significa che le macchine virtuali nell'area secondaria sono allocate e in esecuzione in qualsiasi momento.
* Attivo/passivo con cold standby. Il traffico viene indirizzato a un'area, mentre l'altra attende in modalità cold standby. Cold standby significa che le macchine virtuali nell'area secondaria non vengono allocate finché non diventa necessario per il failover. Questo approccio presenta costi inferiori in termini di esecuzione, ma in genere richiederà più tempo per portare online le risorse in caso di errore.
* Attivo/attivo. Entrambe le aree sono attive e viene effettuato un bilanciamento del carico tra le richieste. Se un'area non è più disponibile, viene esclusa dalla rotazione. 

Questa architettura è incentrata sulla modalità attivo/passivo con hot standby, usando Gestione traffico per il failover. Si noti che è possibile distribuire un numero ridotto di macchine virtuali per la modalità hot standby e quindi aumentare il numero di istanze in base alle esigenze.


### <a name="regional-pairing"></a>Coppie di aree

Ogni area di Azure è abbinata a un'altra area con la stessa collocazione geografica. In generale, è consigliabile scegliere aree della stessa coppia di aree (ad esempio, Stati Uniti orientali 2 e Stati Uniti centrali). I vantaggi di questa operazione includono i seguenti:

* In caso di interruzione su vasta scala, viene data priorità al ripristino di almeno un'area di ogni coppia.
* Gli aggiornamenti di sistema di Azure pianificati vengono implementati in sequenza tra le aree abbinate, per ridurre al minimo l'eventuale tempo di inattività.
* Le coppie si trovano nella stessa area geografica, per soddisfare i requisiti di residenza dei dati.

Assicurarsi tuttavia che entrambe le aree supportino tutti i servizi di Azure necessari per l'applicazione (vedere i [servizi disponibili in base all'area][services-by-region]). Per altre informazioni sulle coppie di aree, vedere [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure][regional-pairs].

### <a name="traffic-manager-configuration"></a>Configurazione di Gestione traffico

Quando si configura Gestione traffico, tenere presente quanto segue:

* **Routing**. Gestione traffico supporta diversi [algoritmi di routing][tm-routing]. Per lo scenario descritto in questo articolo, usare il routing *Priorità* (precedentemente denominato routing *Failover*). Con questa impostazione, Gestione traffico invia tutte le richieste all'area primaria, a meno che l'area primaria non diventi irraggiungibile. A questo punto, viene automaticamente effettuato il failover all'area secondaria. Vedere [Configurare il metodo di routing failover][tm-configure-failover].
* **Probe di integrità**. Gestione traffico usa un [probe][tm-monitoring] HTTP (o HTTPS) per monitorare la disponibilità di ogni area. Il probe verifica una risposta HTTP 200 per un percorso URL specificato. Come procedura consigliata, creare un endpoint che segnali l'integrità complessiva dell'applicazione e usare questo endpoint per il probe di integrità. In caso contrario, il probe potrebbe segnalare un endpoint integro quando le parti più importanti dell'applicazione in realtà hanno esito negativo. Per altre informazioni, vedere [Health Endpoint Monitoring Pattern][health-endpoint-monitoring-pattern] (Modello di monitoraggio degli endpoint di integrità).

Quando Gestione traffico effettua il failover, per un periodo di tempo i client non riescono a raggiungere l'applicazione. La durata di questo periodo è influenzata dai fattori seguenti:

* Il probe di integrità deve rilevare che l'area primaria è diventata irraggiungibile.
* I server DNS devono aggiornare i record DNS memorizzati nella cache per l'indirizzo IP, che dipende dalla durata (TTL) DNS. Il valore TTL predefinito è di 300 secondi (5 minuti), ma è possibile configurare questo valore durante la creazione del profilo di Gestione traffico.

Per dettagli, vedere [Informazioni sul monitoraggio di Gestione traffico][tm-monitoring].

Se Gestione traffico effettua il failover, è consigliabile eseguire un failback manuale invece di implementare un failback automatico. In caso contrario, si potrebbe creare una situazione in cui l'applicazione passa alternativamente da un'area all'altra. Verificare che tutti i sottosistemi dell'applicazione siano integri prima del failback.

Si noti che Gestione traffico effettua automaticamente il failback per impostazione predefinita. Per evitare questa situazione, ridurre manualmente la priorità dell'area primaria dopo un evento di failover. Ad esempio, si supponga che l'area primaria sia di priorità 1 e la secondaria di priorità 2. Dopo un failover, impostare l'area primaria sul livello di priorità 3 per evitare il failback automatico. Quando si è pronti per il cambio, aggiornare la priorità impostandola su 1.

Il comando seguente dell'[interfaccia della riga di comando di Azure][install-azure-cli] aggiorna la priorità:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

Un altro approccio consiste nel disabilitare temporaneamente l'endpoint finché non si è pronti per eseguire il failback:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```    

A seconda della causa di un failover, potrebbe essere necessario ridistribuire le risorse all'interno di un'area. Prima di eseguire nuovamente il failback, eseguire un test della conformità operativa, che avrà lo scopo di verificare gli elementi seguenti:

* Le macchine virtuali sono configurate correttamente (tutto il software richiesto è installato, IIS è in esecuzione e così via).
* I sottosistemi dell'applicazione sono integri.
* Test funzionale (ad esempio, il livello del database è raggiungibile dal livello Web).

### <a name="cassandra-deployment-across-multiple-regions"></a>Distribuzione di Cassandra tra più aree

I data center Cassandra sono un gruppo di nodi dati correlati che vengono configurati collettivamente all'interno di un cluster per la replica e la separazione del carico di lavoro.

È consigliabile usare [DataStax Enterprise][datastax] per l'uso in produzione. Per altre informazioni sull'esecuzione di DataStax in Azure, vedere [DataStax Enterprise Deployment Guide for Azure][cassandra-in-azure] (Guida alla distribuzione di DataStax Enterprise per Azure). Le raccomandazioni generali seguenti si applicano a qualsiasi edizione di Cassandra: 

* Assegnare un indirizzo IP pubblico a ogni nodo. Ciò consente ai cluster di comunicare tra le aree usando l'infrastruttura backbone di Azure, garantendo una velocità effettiva elevata a costo ridotto.
* Proteggere i nodi usando le configurazioni appropriate per il firewall e il gruppo di sicurezza di rete, consentendo il traffico solo da e verso gli host noti, inclusi i client e altri nodi del cluster. Si noti che Cassandra usa porte diverse per la comunicazione, OpsCenter, Spark e così via. Per l'uso delle porte in Cassandra, vedere [Configuring firewall port access][cassandra-ports] (Configurazione dell'accesso alle porte del firewall).
* Usare la crittografia SSL per tutte le comunicazioni [da client a nodo][ssl-client-node] e [da nodo a nodo][ssl-node-node].
* All'interno di un'area, seguire le indicazioni riportate nelle [raccomandazioni per Cassandra](n-tier.md#cassandra).

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Con un'app complessa a più livelli, potrebbe non essere necessario replicare l'intera applicazione nell'area secondaria. In alternativa, è possibile replicare solo un sottosistema critico indispensabile per supportare la continuità aziendale.

Gestione traffico è un possibile punto di guasto nel sistema. In caso di interruzione del servizio Gestione traffico, i client non riescono ad accedere all'applicazione durante il tempo di inattività. Rivedere il [contratto di servizio di Gestione traffico][tm-sla] e determinare se l'uso di Gestione traffico da solo soddisfa i requisiti aziendali per la disponibilità elevata. In caso contrario, considerare di aggiungere un'altra soluzione di gestione del traffico come failback. In caso di errore del servizio Gestione traffico di Azure, modificare i record CNAME in DNS in modo da puntare all'altro servizio di gestione del traffico (questo passaggio deve essere eseguito manualmente e l'applicazione non sarà disponibile finché non vengono propagate le modifiche al DNS).

Per il cluster Cassandra, gli scenari di failover da prendere in considerazione dipendono dai livelli di coerenza usati dall'applicazione, nonché dal numero di repliche usate. Per i livelli di coerenza e l'uso in Cassandra, vedere [Configuring data consistency][cassandra-consistency] (Configurazione della coerenza dei dati) e [Cassandra: How many nodes are talked to with Quorum?][cassandra-consistency-usage] (Cassandra: con quanti nodi si stabilisce una comunicazione con Quorum). La disponibilità dei dati in Cassandra è determinata dal livello di coerenza usato dall'applicazione e dal meccanismo di replica. Per la replica in Cassandra, vedere [Data Replication in NoSQL Databases Explained][cassandra-replication] (Spiegazione della replica dei dati nei database NoSQL).

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Quando si aggiorna la distribuzione, aggiornare un'area alla volta per ridurre la probabilità di un errore globale da una configurazione errata o un errore nell'applicazione.

Testare la resilienza del sistema agli errori. Di seguito sono riportati alcuni scenari di errore comuni da testare:

* Arrestare le istanze di macchina virtuale.
* Esercitare un uso elevato delle risorse come CPU e memoria.
* Disconnettere/ritardare la rete.
* Arrestare in modo anomalo i processi.
* Far scadere i certificati.
* Simulare errori hardware.
* Arrestare il servizio DNS nei controller di dominio.

Misurare i tempi di ripristino e verificare che soddisfino i requisiti aziendali. Testare anche le combinazioni di modalità di errore.


<!-- Links -->
[hybrid-vpn]: ../hybrid-networking/vpn.md
[azure-dns]: /azure/dns/dns-overview
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-ports]: https://docs.datastax.com/en/datastax_enterprise/5.0/datastax_enterprise/sec/configFirewallPorts.html
[datastax]: https://www.datastax.com/products/datastax-enterprise
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[install-azure-cli]: /azure/xplat-cli-install
[regional-pairs]: /azure/best-practices-availability-paired-regions
[resource groups]: /azure/azure-resource-manager/resource-group-overview
[resource-group-links]: /azure/resource-group-link-resources
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssl-client-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLClientToNode_t.html
[ssl-node-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLNodeToNode_t.html
[tablediff]: https://msdn.microsoft.com/library/ms162843.aspx
[tm-configure-failover]: /azure/traffic-manager/traffic-manager-configure-failover-routing-method
[tm-monitoring]: /azure/traffic-manager/traffic-manager-monitoring
[tm-routing]: /azure/traffic-manager/traffic-manager-routing-methods
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/services/traffic-manager/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[wsfc]: https://msdn.microsoft.com/library/hh270278.aspx
[0]: ./images/multi-region-application-diagram.png "Architettura di rete a disponibilità elevata per applicazioni Azure a più livelli"
