---
title: Implementazione di una topologia di rete hub-spoke con servizi condivisi in Azure
description: Come implementare una topologia di rete hub-spoke con servizi condivisi in Azure.
author: telmosampaio
ms.date: 06/19/2018
pnp.series.title: Implement a hub-spoke network topology with shared services in Azure
pnp.series.prev: hub-spoke
ms.openlocfilehash: 0238c5d6f28bacbc32268d4586b30395de36384b
ms.sourcegitcommit: 62945777e519d650159f0f963a2489b6bb6ce094
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48876869"
---
# <a name="implement-a-hub-spoke-network-topology-with-shared-services-in-azure"></a>Implementare una topologia di rete hub-spoke con servizi condivisi in Azure

Questa architettura di riferimento, basata sull'architettura di riferimento [hub-spoke][guidance-hub-spoke], include nell'hub servizi condivisi che potranno essere utilizzati da tutti gli spoke. Come primo passo per la migrazione di un data center al cloud e la creazione di un [data center virtuale], i primi servizi da condividere sono quelli di gestione delle identità e di sicurezza. Questa architettura di riferimento illustra come estendere i servizi di Active Directory dal data center locale ad Azure e come aggiungere un'appliance virtuale di rete che possa fungere da firewall in una topologia hub-spoke.  [**Distribuire questa soluzione**](#deploy-the-solution).

![[0]][0]

*Scaricare un [file di Visio][visio-download] di questa architettura*

I vantaggi di questa topologia includono:

* **Risparmio sui costi**, centralizzando in un'unica posizione i servizi che possono essere condivisi da più carichi di lavoro, ad esempio appliance virtuali di rete e server DNS.
* **Superamento dei limiti delle sottoscrizioni** eseguendo il peering delle reti virtuali da sottoscrizioni diverse all'hub centrale.
* **Separazione dei compiti** tra IT centrale (SecOPs, InfraOps) e carichi di lavoro (DevOps).

Tra gli usi tipici di questa architettura vi sono:

* Carichi di lavoro distribuiti in ambienti diversi, ad esempio sviluppo, test e produzione, che richiedono servizi condivisi, ad esempio DNS, IDS, NTP o Active Directory Domain Services. I servizi condivisi vengono inseriti nella rete virtuale dell'hub, mentre ogni ambiente viene distribuito in uno spoke per mantenere l'isolamento.
* Carichi di lavoro che non richiedono connettività uno con l'altro, ma richiedono l'accesso ai servizi condivisi.
* Aziende che richiedono il controllo centrale sugli aspetti di sicurezza, ad esempio un firewall nell'hub come rete perimetrale, e gestione separata per i carichi di lavoro in ogni spoke.

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

* **Rete locale**. Una rete LAN privata in esecuzione all'interno di un'organizzazione.

* **Dispositivo VPN**. Un dispositivo o un servizio che offre connettività esterna alla rete locale. Il dispositivo VPN può essere un dispositivo hardware o una soluzione software, ad esempio il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012. Per un elenco delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN da sito a sito][vpn-appliance].

* **Gateway di rete virtuale VPN o gateway ExpressRoute**. Il gateway di rete virtuale consente di connettere la rete virtuale al dispositivo VPN o al circuito ExpressRoute usato per la connettività con la propria rete locale. Per altre informazioni, vedere [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].

> [!NOTE]
> Gli script di distribuzione per questa architettura di riferimento usano un gateway VPN per la connettività e una rete virtuale in Azure per simulare la rete locale.

* **Rete virtuale dell'hub**. Rete virtuale di Azure usata come hub nella topologia hub-spoke. L'hub è il punto di connettività centrale alla rete locale e offre una posizione in cui ospitare servizi che possono essere utilizzati da carichi di lavoro diversi ospitati nelle reti virtuali spoke.

* **Subnet del gateway**. I gateway di rete virtuale vengono mantenuti nella stessa subnet.

* **Subnet dei servizi condivisi**. Una subnet nella rete virtuale dell'hub usata per ospitare servizi che possono essere condivisi tra tutti gli spoke, ad esempio DNS o Active Directory Domain Services.

* **Subnet della rete perimetrale**. Subnet nella rete virtuale hub usata per ospitare appliance virtuali di rete che possono fungere da appliance di sicurezza, ad esempio da firewall.

* **Reti virtuali spoke**. Una o più reti virtuali di Azure usate come spoke nella topologia hub-spoke. Gli spoke possono essere usati per isolare i carichi di lavoro nelle reti virtuali corrispondenti, gestite separatamente rispetto agli altri spoke. Ogni carico di lavoro può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure. Per ulteriori informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] e [Esecuzione di carichi di lavoro della macchina virtuale Linux][linux-vm-ra].

* **Peering reti virtuali**. È possibile connettere due reti virtuali nella stessa area di Azure tramite una [connessione peering][vnet-peering]. Le connessioni peering sono connessioni non transitive a bassa latenza tra reti virtuali. Dopo il peering, le reti virtuali si scambiano traffico tramite il backbone di Azure, senza bisogno di un router. In una topologia hub-spoke di rete si usa il peering reti virtuali per connettere l'hub a ogni spoke.

> [!NOTE]
> Questo articolo illustra solo le distribuzioni [Resource Manager](/azure/azure-resource-manager/resource-group-overview), ma è anche possibile connettere una rete virtuale classica a una rete virtuale di Resource Manager nella stessa sottoscrizione. In questo modo, gli spoke possono ospitare distribuzioni classiche e trarre comunque vantaggio dai servizi condivisi nell'hub.

## <a name="recommendations"></a>Consigli

Tutte le raccomandazioni per l'architettura di riferimento [hub-spoke][guidance-hub-spoke] si applicano anche all'architettura di riferimento con servizi condivisi. 

Per la maggior parte degli scenari con servizi condivisi sono valide anche le raccomandazioni seguenti. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.

### <a name="identity"></a>Identità

La maggior parte delle organizzazioni aziendali ha un ambiente Active Directory Domain Services nel proprio data center locale. Per facilitare la gestione degli asset spostati dalla rete locale ad Azure che dipendono da Active Directory Domain Services, è consigliabile ospitare i controller di dominio di Active Directory Domain Services in Azure.

Se si usano oggetti Criteri di gruppo che si preferisce controllare separatamente per Azure e l'ambiente locale, usare un diverso sito AD per ogni area di Azure. Inserire i controller di dominio in una rete virtuale centrale (hub) accessibile dai carichi di lavoro dipendenti.

### <a name="security"></a>Sicurezza

Quando si spostano carichi di lavoro dall'ambiente locale ad Azure, alcuni di questi dovranno essere ospitati in VM. Per motivi di conformità, potrebbe essere necessario applicare restrizioni sul traffico correlato a tali carichi di lavoro. 

È possibile usare appliance virtuali di rete in Azure per ospitare diversi tipi di servizi di sicurezza e gestione delle prestazioni. Se si ha attualmente familiarità con un determinato set di appliance in locale, è consigliabile usare le stesse appliance virtualizzate in Azure, se possibile.

> [!NOTE]
> Gli script di distribuzione per questa architettura di riferimento usano una macchina virtuale Ubuntu con inoltro IP abilitato per simulare un'appliance virtuale di rete.

## <a name="considerations"></a>Considerazioni

### <a name="overcoming-vnet-peering-limits"></a>Superamento dei limiti del peering reti virtuali

Assicurarsi di considerare il [limite sul numero di peering reti virtuali per ogni rete virtuale][vnet-peering-limit] in Azure. Se si stabilisce che saranno necessari più spoke di quanto consentito dal limite, valutare la creazione di una topologia hub-spoke-hub-spoke, in cui il primo livello di spoke funge anche da hub. Il diagramma seguente illustra questo approccio.

![[3]][3]

Considerare inoltre i servizi condivisi nell'hub, per assicurarsi che quest'ultimo sia scalabile per un numero superiore di spoke. Ad esempio, se l'hub fornisce servizi firewall, valutare i limiti di larghezza di banda della soluzione firewall quando si aggiungono più spoke. Può essere utile spostare alcuni dei servizi condivisi in un secondo livello di hub.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura è disponibile in [GitHub][ref-arch-repo]. La distribuzione crea i seguenti gruppi di risorse nella sottoscrizione:

- hub-adds-rg
- hub-nva-rg
- hub-vnet-rg
- onprem-vnet-rg
- spoke1-vnet-rg
- spoke2-vent-rg

I file parametro di modello fanno riferimento a questi nomi; pertanto, se questi vengono modificati, aggiornare i file parametro in modo che corrispondano.

### <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a>Distribuire il data center locale simulato con azbb

Questo passaggio distribuisce il data center locale simulato come rete virtuale di Azure.

1. Passare alla cartella `hybrid-networking\shared-services-stack\` del repository GitHub.

2. Aprire il file `onprem.json` . 

3. Cercare tutte le istanze di `UserName`, `adminUserName`, `Password` e `adminPassword`. Immettere i valori per nome utente e password nei parametri e salvare il file. 

4. Eseguire il comando seguente:

   ```bash
   azbb -s <subscription_id> -g onprem-vnet-rg -l <location> -p onprem.json --deploy
   ```
5. Attendere il completamento della distribuzione. Questa distribuzione crea una rete virtuale, una macchina virtuale che esegue Windows e un gateway VPN. La creazione del gateway VPN può richiedere più di 40 minuti.

### <a name="deploy-the-hub-vnet"></a>Distribuire la rete virtuale dell'hub

Questo passaggio distribuisce la rete virtuale dell'hub e la connette alla rete virtuale locale simulata.

1. Aprire il file `hub-vnet.json` . 

2. Cercare `adminPassword` e immettere un nome utente e una password nei parametri. 

3. Cercare tutte le istanze di `sharedKey` e immettere un valore per una chiave condivisa. Salvare il file.

   ```bash
   "sharedKey": "abc123",
   ```

4. Eseguire il comando seguente:

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet.json --deploy
   ```

5. Attendere il completamento della distribuzione. Questa distribuzione crea una rete virtuale, una macchina virtuale, un gateway VPN e una connessione al gateway creato nella sezione precedente. Il completamento del gateway VPN può richiedere più di 40 minuti.

### <a name="deploy-ad-ds-in-azure"></a>Distribuire AD DS in Azure

Questo passaggio distribuisce i controller di dominio di AD DS in Azure.

1. Aprire il file `hub-adds.json` .

2. Cercare tutte le istanze di `Password` e `adminPassword`. Immettere i valori per nome utente e password nei parametri e salvare il file. 

3. Eseguire il comando seguente:

   ```bash
   azbb -s <subscription_id> -g hub-adds-rg -l <location> -p hub-adds.json --deploy
   ```
  
Questo passaggio della distribuzione può richiedere alcuni minuti perché aggiunge le due macchine virtuali al dominio ospitato nel data center locale simulato e installa AD DS nelle due VM.

### <a name="deploy-the-spoke-vnets"></a>Distribuire le reti virtuali spoke

Questo passaggio distribuisce le reti virtuali spoke.

1. Aprire il file `spoke1.json` .

2. Cercare `adminPassword` e immettere un nome utente e una password nei parametri. 

3. Eseguire il comando seguente:

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg -l <location> -p spoke1.json --deploy
   ```
  
4. Ripetere i passaggi 1 e 2 per il file `spoke2.json`.

5. Eseguire il comando seguente:

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg -l <location> -p spoke2.json --deploy
   ```

### <a name="peer-the-hub-vnet-to-the-spoke-vnets"></a>Eseguire il peering della rete virtuale dell'hub e delle reti virtuali spoke

Per creare una connessione peering dalla rete virtuale dell'hub alle reti virtuali spoke, eseguire questo comando:

```bash
azbb -s <subscription_id> -g hub-vnet-rg -l <location> -p hub-vnet-peering.json --deploy
```

### <a name="deploy-the-nva"></a>Distribuire un'appliance virtuale di rete

Questo passaggio distribuisce un'appliance virtuale di rete nella subnet `dmz`.

1. Aprire il file `hub-nva.json` .

2. Cercare `adminPassword` e immettere un nome utente e una password nei parametri. 

3. Eseguire il comando seguente:

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg -l <location> -p hub-nva.json --deploy
   ```

### <a name="test-connectivity"></a>Testare la connettività 

Testare la connettività dall'ambiente locale simulato alla rete virtuale hub dell'hub.

1. Usare il portale di Azure per trovare la macchina virtuale denominata `jb-vm1` nel gruppo di risorse `onprem-jb-rg`.

2. Fare clic su `Connect` per aprire una sessione di desktop remoto per la macchina virtuale. Usare la password specificata nel file parametro `onprem.json`.

3. Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi alla macchina virtuale jumpbox nella rete virtuale dell'hub.

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```
L'output dovrebbe essere simile al seguente:

```powershell
ComputerName     : 10.0.0.68
RemoteAddress    : 10.0.0.68
RemotePort       : 3389
InterfaceAlias   : Ethernet 2
SourceAddress    : 192.168.1.000
TcpTestSucceeded : True
```

> [!NOTE]
> Per impostazione predefinita, le VM Windows Server non consentono risposte ICMP in Azure. Se si vuole usare `ping` per testare la connettività, è necessario abilitare il traffico ICMP in Windows Firewall con sicurezza avanzata per ogni VM.

Ripetere gli stessi passaggi per testare la connettività alle reti virtuali spoke:

```powershell
Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
```


<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[guidance-hub-spoke]: ./hub-spoke.md
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[data center virtuale]: https://aka.ms/vdc
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/shared-services.png "Topologia con servizi condivisi in Azure"
[3]: ./images/hub-spokehub-spoke.svg "Topologia hub-spoke-hub-spoke in Azure"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
