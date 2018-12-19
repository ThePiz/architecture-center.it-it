---
title: Distribuire appliance virtuali di rete con disponibilità elevata
titleSuffix: Azure Reference Architectures
description: Distribuire le appliance virtuali di rete con disponibilità elevata.
author: telmosampaio
ms.date: 12/08/2018
ms.custom: seodec18
ms.openlocfilehash: d3f9017db1bbf9741b10db16eb5a3dbab78f1160
ms.sourcegitcommit: 7d21aec9d9de0004ac777c1d1e364f53aac2350d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2018
ms.locfileid: "53120753"
---
# <a name="deploy-highly-available-network-virtual-appliances"></a>Distribuire appliance virtuali di rete con disponibilità elevata

Questo articolo illustra come distribuire un set di appliance virtuali di rete per la disponibilità elevata in Azure. L'appliance virtuale di rete è in genere usata per controllare il flusso del traffico di rete da una rete perimetrale in altre reti o subnet. Per altre informazioni sull'implementazione di una rete perimetrale in Azure, vedere [Servizi cloud Microsoft e sicurezza di rete][cloud-security]. L'articolo include le architetture di esempio solo per i dati in ingresso, solo per i dati in uscita e per i dati in ingresso e in uscita.

**Prerequisiti:** questo articolo presuppone una conoscenza di base della rete di Azure, dei [servizi di bilanciamento del carico di Azure][lb-overview] e delle [route definite dall'utente][udr-overview] (UDR).

## <a name="architecture-diagrams"></a>Diagrammi di architettura

L'appliance virtuale di rete può essere distribuita in una rete perimetrale in diverse architetture. Ad esempio, la figura seguente illustra l'uso di una [singola appliance virtuale di rete][nva-scenario] per i dati in ingresso.

![[0]][0]

In questa architettura l'appliance virtuale di rete costituisce un limite di rete protetta tramite il controllo di tutto il traffico di rete in ingresso e in uscita e il passaggio del solo traffico che soddisfa le regole di sicurezza di rete. Tuttavia, il fatto che tutto il traffico di rete debba passare attraverso l'appliance virtuale di rete significa che questa è un singolo punto di guasto nella rete. In caso di errore dell'appliance virtuale di rete, non c'è un altro percorso per il traffico di rete e tutte le subnet di back-end non sono disponibili.

Per rendere un'appliance virtuale di rete altamente disponibile, distribuire più appliance virtuali di rete in un set di disponibilità.

Le architetture seguenti descrivono le risorse e la configurazione necessarie per le appliance virtuali di rete a disponibilità elevata:

| Soluzione | Vantaggi | Considerazioni |
| --- | --- | --- |
| [Dati in ingresso con appliance virtuali di rete di livello 7][ingress-with-layer-7] |Tutti i nodi dell'appliance virtuale di rete sono attivi |Richiede un'appliance virtuale di rete che può interrompere le connessioni e usare SNAT<br/> Richiede un set separato di appliance virtuali di rete per il traffico proveniente da Internet e da Azure <br/> Può essere usato solo per il traffico proveniente da punti esterni ad Azure |
| [Dati in uscita con appliance virtuali di rete di livello 7][egress-with-layer-7] |Tutti i nodi dell'appliance virtuale di rete sono attivi | Richiede un'appliance virtuale di rete in grado di terminare le connessioni e implementa SNAT
| [Dati in ingresso e in uscita con appliance virtuali di rete di livello 7][ingress-egress-with-layer-7] |Tutti i nodi sono attivi<br/>In grado di gestire il traffico generato in Azure |Richiede un'appliance virtuale di rete che può interrompere le connessioni e usare SNAT<br/>Richiede un set separato di appliance virtuali di rete per il traffico proveniente da Internet e da Azure |
| [Commutatore PIP-UDR][pip-udr-switch] |Un solo set di appliance virtuali di rete per tutto il traffico<br/>In grado di gestire tutto il traffico (nessun limite alle regole di porta) |Modello attivo/passivo<br/>Richiede un processo di failover |
| [PIP-UDR senza SNAT](#pip-udr-nvas-without-snat) | Un solo set di appliance virtuali di rete per tutto il traffico<br/>In grado di gestire tutto il traffico (nessun limite alle regole di porta)<br/>Non richiede la configurazione di SNAT per le richieste in ingresso |Modello attivo/passivo<br/>Richiede un processo di failover<br/>La logica di probe e il failover vengono eseguiti all'esterno della rete virtuale |

## <a name="ingress-with-layer-7-nvas"></a>Dati in ingresso con appliance virtuali di rete di livello 7

La figura seguente mostra un'architettura a disponibilità elevata che implementa una rete perimetrale con dati in ingresso dietro un servizio di bilanciamento del carico con connessione a Internet. Questa architettura è progettata per offrire la connettività ai carichi di lavoro di Azure per il traffico di livello 7, ad esempio HTTP o HTTPS:

![[1]][1]

Il vantaggio di questa architettura è che tutte le appliance virtuali di rete sono attive e se una di queste ha esito negativo il servizio di bilanciamento del carico indirizza il traffico di rete a un'altra appliance virtuale di rete. Entrambe le appliance virtuali di rete indirizzano il traffico al servizio di bilanciamento del carico interno quindi fino a quando un'appliance virtuale di rete è attiva, il traffico continua a fluire. Le appliance virtuali di rete devono interrompere il traffico SSL previsto per le macchine virtuali di livello Web. Queste appliance virtuali di rete non possono essere estese per gestire il traffico locale perché questo richiede un altro set dedicato di appliance virtuali di rete con le proprie route di rete.

> [!NOTE]
> Questa architettura viene usata nell'architettura di riferimento della [rete perimetrale tra Azure e il data center locale][dmz-on-prem] e quella della [rete perimetrale tra Azure e Internet][dmz-internet]. Ognuna di queste architetture di riferimento include una soluzione di distribuzione che è possibile usare. Per altre informazioni, vedere i collegamenti seguenti.

## <a name="egress-with-layer-7-nvas"></a>Dati in uscita con appliance virtuali di rete di livello 7

L'architettura precedente può essere estesa per offrire una rete perimetrale per i dati in uscita per le richieste provenienti dal carico di lavoro di Azure. L'architettura seguente è progettata per offrire un'elevata disponibilità delle appliance virtuali di rete nella rete perimetrale per il traffico di livello 7, ad esempio HTTP o HTTPS:

![[2]][2]

In questa architettura tutto il traffico proveniente da Azure viene indirizzato a un servizio di bilanciamento del carico interno. Il servizio di bilanciamento del carico distribuisce le richieste in uscita tra un set di appliance virtuali di rete. Le appliance virtuali di rete indirizzano il traffico a Internet usando i singoli indirizzi IP pubblici.

> [!NOTE]
> Questa architettura viene usata nell'architettura di riferimento della [rete perimetrale tra Azure e il data center locale][dmz-on-prem] e quella della [rete perimetrale tra Azure e Internet][dmz-internet]. Ognuna di queste architetture di riferimento include una soluzione di distribuzione che è possibile usare. Per altre informazioni, vedere i collegamenti seguenti.

## <a name="ingress-egress-with-layer-7-nvas"></a>Dati in ingresso e in uscita con appliance virtuali di rete di livello 7

Le due architetture precedenti prevedevano due reti perimetrali diverse per i dati in ingresso e i dati in uscita. L'architettura seguente illustra come creare una rete perimetrale che può essere usata sia per i dati in ingresso che per i dati in uscita per il traffico di livello 7, ad esempio HTTP o HTTPS:

![[4]][4]

In questa architettura le appliance virtuali di rete elaborano le richieste provenienti dal gateway dell'applicazione. Le appliance virtuali di rete elaborano anche le richieste in uscita dalle macchine virtuali del carico di lavoro nel pool back-end del servizio di bilanciamento del carico. Poiché il traffico in ingresso viene indirizzato con un gateway dell'applicazione e il traffico in uscita viene indirizzato con un servizio di bilanciamento del carico, le appliance virtuali di rete sono responsabili della gestione dell'affinità di sessione. Questo vuol dire che il gateway dell'applicazione conserva un mapping delle richieste in ingresso e in uscita in modo da poter inoltrare la risposta corretta al richiedente originale. Tuttavia, il servizio di bilanciamento del carico interno non dispone dell'accesso ai mapping del gateway dell'applicazione e usa la propria logica per inviare le risposte alle appliance virtuali di rete. È possibile che il servizio di bilanciamento del carico possa inviare una risposta a un appliance virtuale di rete che inizialmente non ha ricevuto la richiesta dal gateway dell'applicazione. In questo caso, le appliance virtuali di rete devono comunicare e trasferire la risposta tra di loro in modo che la corretta appliance virtuale di rete possa inoltrare la risposta al gateway dell'applicazione.

> [!NOTE]
> È anche possibile risolvere il problema di routing asimmetrico garantendo che le appliance virtuali di rete eseguano SNAt. In questo modo l'IP dell'origine iniziale del richiedente sostituisce uno degli indirizzi IP dell'appliance virtuale di rete usato nel flusso in ingresso. Questo garantisce la possibilità di usare più appliance virtuali di rete contemporaneamente, preservando la simmetria di route.

## <a name="pip-udr-switch-with-layer-4-nvas"></a>Commutatore PIP-UDR con appliance virtuali di rete di livello 4

L'architettura seguente illustra un'architettura con un'appliance virtuali di rete attiva e una passiva. Questa architettura gestisce sia i dati in ingresso che i dati in uscita per il traffico di livello 4:

![[3]][3]

> [!TIP]
> Una soluzione completa di questa architettura è disponibile in [GitHub][pnp-ha-nva].

Questa architettura è simile alla prima architettura descritta in questo articolo, che prevedeva un'unica appliance virtuale di rete che accettava e filtrava le richieste in ingresso di livello 4. Questa architettura aggiunge una seconda appliance virtuale di rete passiva per garantire un'elevata disponibilità. In caso di errore dell'appliance virtuale di rete attiva, quella passiva viene resa attiva e UDR e PIP vengono modificati per puntare alle schede di interfaccia di rete nell'appliance virtuale di rete attualmente attiva. Queste modifiche a UDR e PIP possono essere eseguite manualmente o mediante un processo automatico. Il processo automatico è in genere un daemon o un altro servizio di monitoraggio in esecuzione in Azure. Esegue una query su un probe di integrità nell'appliance virtuale di rete attiva ed esegue la commutazionre di UDR e PIP quando rileva un errore di appliance virtuale di rete.

La figura precedente illustra un cluster di esempio [ZooKeeper][zookeeper] che offre un daemon a disponibilità elevata. All'interno del cluster ZooKeeper, un quorum di nodi sceglie un coordinatore. In caso di errore del coordinatore, i nodi restanti consentono di scegliere un nuovo coordinatore. Per questa architettura, il nodo coordinatore esegue il daemon che esegue la query sull'endpoint di integrità nell'appliance virtuale di rete. Se l'appliance virtuale di rete non risponde al probe di integrità, il daemon attiva l'appliance virtuale di rete passiva. Il daemon quindi chiama l'API REST di Azure per rimuovere PIP dall'appliance virtuale di rete non riuscita e lo collega all'appliance virtuale di rete appena attivata. Il daemon quindi modifica l'UDR in modo che punti all'indirizzo IP interno dell'appliance virtuale di rete appena attivata.

Non includere i nodi ZooKeeper in una subnet accessibile solo tramite una route che include l'appliance virtuale di rete. In caso contrario i nodi ZooKeeper non sono accessibili se l'appliance virtuale di rete ha esito negativo. Nel caso in cui il daemon abbia esito negativo per un qualsiasi motivo, non sarà possibile accedere a uno dei nodi ZooKeeper per diagnosticare il problema.

Per visualizzare la soluzione completa incluso il codice di esempio, vedere i file nei [repository GitHub][pnp-ha-nva].

## <a name="pip-udr-nvas-without-snat"></a>PIP-UDR NVA senza SNAT

Questa architettura usa due macchine virtuali di Azure per ospitare il firewall di appliance virtuale di rete (NVA) in una configurazione attiva-passiva che supporta il failover automatizzato, ma non richiede Source Network Address Translation (SNAT).

![PIP-UDR NVA senza architettura SNAT](./images/nva-ha/pip-udr-without-snat.png)

> [!TIP]
> Una soluzione completa per questa architettura è disponibile in [GitHub][ha-nva-fo].

Questa soluzione è progettata per i clienti di Azure che non possono configurare SNAT per le richieste in ingresso sui firewall di appliance virtuale di rete. SNAT nasconde l'indirizzo IP del client di origine. Se è necessario registrare gli indirizzi IP di origine o usati all'interno di altri componenti di sicurezza su più livelli dietro l'appliance di rete virtuale, questa soluzione offre un approccio di base.

Il failover di voci della tabella di route definita dall'utente viene automatizzato con un indirizzo dell'hop successivo impostato sull'indirizzo IP di un'interfaccia nella macchina virtuale firewall di appliance virtuale di rete. La logica di failover automatizzato è ospitata in un'app per le funzioni creata tramite [Funzioni di Azure](/azure/azure-functions/). Il codice di failover viene eseguito come una funzione senza server all'interno di funzioni di Azure. La distribuzione è pratica, conveniente e facile da gestire e personalizzare. Inoltre, l'app per le funzioni è ospitata all'interno di funzioni di Azure, pertanto, non ha dipendenze nella rete virtuale. Se le modifiche alla rete virtuale compromettono i firewall di appliance virtuale di rete, l’esecuzione dell'app per le funzioni continua in modo indipendente. Il test è più preciso, in quanto avviene all'esterno della rete virtuale usando la stessa route richiesta dal client in ingresso.

Per verificare la disponibilità del firewall di appliance virtuale di rete, il codice dell'app per le funzioni esegue il probe in una delle due modalità riportate di seguito:

- Monitorando lo stato delle macchine virtuali di Azure che ospitano il firewall di appliance virtuale di rete.

- Verificando se c’è una porta aperta tramite il firewall al server Web back-end. Per questa opzione, l'appliance virtuale di rete deve esporre un socket tramite PIP per il codice di app per le funzioni da testare.

Scegliere il tipo di probe da usare quando si configura l'app per le funzioni. Per visualizzare la soluzione completa incluso il codice di esempio, vedere i file nei [repository GitHub][ha-nva-fo].

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [implementare una rete perimetrale tra Azure e il data center locale][dmz-on-prem] con le appliance virtuali di rete di livello 7.
- Informazioni su come [implementare una rete perimetrale tra Azure e Internet][dmz-internet] con le appliance virtuali di rete di livello 7.
- [Risoluzione dei problemi delle appliance virtuali di rete in Azure](/azure/virtual-network/virtual-network-troubleshoot-nva)

<!-- links -->

[cloud-security]: /azure/best-practices-network-security
[dmz-on-prem]: ./secure-vnet-hybrid.md
[dmz-internet]: ./secure-vnet-dmz.md
[egress-with-layer-7]: #egress-with-layer-7-nvas
[ingress-with-layer-7]: #ingress-with-layer-7-nvas
[ingress-egress-with-layer-7]: #ingress-egress-with-layer-7-nvas
[lb-overview]: /azure/load-balancer/load-balancer-overview/
[nva-scenario]: /azure/virtual-network/virtual-network-scenario-udr-gw-nva/
[pip-udr-switch]: #pip-udr-switch-with-layer-4-nvas
[udr-overview]: /azure/virtual-network/virtual-networks-udr-overview/
[zookeeper]: https://zookeeper.apache.org/
[pnp-ha-nva]: https://github.com/mspnp/ha-nva
[ha-nva-fo]: https://aka.ms/ha-nva-fo

<!-- images -->

[0]: ./images/nva-ha/single-nva.png "Architettura della singola appliance virtuale di rete"
[1]: ./images/nva-ha/l7-ingress.png "Dati in ingresso di livello 7"
[2]: ./images/nva-ha/l7-ingress-egress.png "Dati in uscita di livello 7"
[3]: ./images/nva-ha/active-passive.png "Cluster attivo-passivo"
[4]: ./images/nva-ha/l7-ingress-egress-ag.png
