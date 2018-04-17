---
title: Implementazione una topologia di rete hub-spoke in Azure
description: Come implementare una topologia di rete hub-spoke in Azure.
author: telmosampaio
ms.date: 02/23/2018
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: 243ad026c7c9703d9659cbef6815131fcdaa8a11
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="implement-a-hub-spoke-network-topology-in-azure"></a>Implementare una topologia di rete hub-spoke in Azure

Questa architettura di riferimento illustra come implementare una topologia hub-spoke in Azure. L'*hub* è una rete virtuale in Azure che funge da punto di connettività centrale alla rete locale. Gli *spoke* sono le reti virtuali che eseguono il peering con l'hub e possono essere usate per isolare i carichi di lavoro. Flussi di traffico tra il data center locale e l'hub attraverso una connessione di gateway VPN o ExpressRoute.  [**Distribuire questa soluzione**](#deploy-the-solution).

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

## <a name="architecture"></a>Architecture

L'architettura è costituita dai componenti seguenti.

* **Rete locale**. Una rete LAN privata in esecuzione all'interno di un'organizzazione.

* **Dispositivo VPN**. Un dispositivo o un servizio che offre connettività esterna alla rete locale. Il dispositivo VPN può essere un dispositivo hardware o una soluzione software, ad esempio il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012. Per un elenco delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN da sito a sito][vpn-appliance].

* **Gateway di rete virtuale VPN o gateway ExpressRoute**. Il gateway di rete virtuale consente di connettere la rete virtuale al dispositivo VPN o al circuito ExpressRoute usato per la connettività con la propria rete locale. Per altre informazioni, vedere [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].

> [!NOTE]
> Gli script di distribuzione per questa architettura di riferimento usano un gateway VPN per la connettività e una rete virtuale in Azure per simulare la rete locale.

* **Rete virtuale dell'hub**. Rete virtuale di Azure usata come hub nella topologia hub-spoke. L'hub è il punto di connettività centrale alla rete locale e offre una posizione in cui ospitare servizi che possono essere utilizzati da carichi di lavoro diversi ospitati nelle reti virtuali spoke.

* **Subnet del gateway**. i gateway di rete virtuale vengono mantenuti nella stessa subnet.

* **Reti virtuali spoke**. Una o più reti virtuali di Azure usate come spoke nella topologia hub-spoke. Gli spoke possono essere usati per isolare i carichi di lavoro nelle reti virtuali corrispondenti, gestite separatamente rispetto agli altri spoke. Ogni carico di lavoro può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure. Per ulteriori informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] e [Esecuzione di carichi di lavoro della macchina virtuale Linux][linux-vm-ra].

* **Peering reti virtuali**. È possibile connettere due reti virtuali nella stessa area di Azure tramite una [connessione peering][vnet-peering]. Le connessioni peering sono connessioni non transitive a bassa latenza tra reti virtuali. Dopo il peering, le reti virtuali si scambiano traffico tramite il backbone di Azure, senza bisogno di un router. In una topologia hub-spoke di rete si usa il peering reti virtuali per connettere l'hub a ogni spoke.

> [!NOTE]
> Questo articolo illustra solo le distribuzioni [Resource Manager](/azure/azure-resource-manager/resource-group-overview), ma è anche possibile connettere una rete virtuale classica a una rete virtuale di Resource Manager nella stessa sottoscrizione. In questo modo, gli spoke possono ospitare distribuzioni classiche e trarre comunque vantaggio dai servizi condivisi nell'hub.

## <a name="recommendations"></a>Raccomandazioni

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.

### <a name="resource-groups"></a>Gruppi di risorse

È possibile implementare la rete virtuale dell'hub e ogni rete virtuale spoke in gruppi di risorse diversi e persino in sottoscrizioni diverse, a condizione che appartengano allo stesso tenant di Azure Active Directory (Azure AD) nella stessa area di Azure. Questo consente di decentralizzare la gestione di ogni carico di lavoro e allo stesso tempo condividere i servizi gestiti nella rete virtuale dell'hub.

### <a name="vnet-and-gatewaysubnet"></a>Reti virtuali e GatewaySubnet

Creare una subnet denominata *GatewaySubnet*, con un intervallo di indirizzi di /27. Questa subnet è necessaria per il gateway di rete virtuale. Allocare 32 indirizzi a questa subnet contribuirà in futuro a evitare di raggiungere i limiti di dimensioni del gateway.

Per altre informazioni sulla configurazione del gateway, vedere le architetture di riferimento seguenti, a seconda del tipo di connessione:

- [Rete ibrida tramite ExpressRoute][guidance-expressroute]
- [Rete ibrida tramite un gateway VPN][guidance-vpn]

Per una disponibilità più elevata, è possibile usare ExpressRoute più una rete VPN per il failover. Vedere [Connettere una rete locale ad Azure tramite ExpressRoute con failover VPN][hybrid-ha].

Una topologia hub-spoke può essere usata anche senza gateway, se non è necessaria la connettività con la rete locale. 

### <a name="vnet-peering"></a>Peering reti virtuali

Il peering reti virtuali è una relazione non transitiva tra due reti virtuali. Se è necessaria la connettività tra gli spoke, considerare l'aggiunta di una connessione peering separata tra di essi.

Tuttavia, se si dispone di più spoke che devono connettersi tra loro, le possibili connessioni peering si esauriranno molto rapidamente, a causa del [limite di peering reti virtuali per ogni rete virtuale][vnet-peering-limit]. In questo scenario, prendere in considerazione l'uso di route definite dall'utente per forzare l'invio del traffico destinato a uno spoke a un'appliance virtuale di rete che funga da router nella rete virtuale dell'hub. Questo consentirà agli spoke di connettersi tra loro.

È anche possibile configurare gli spoke in modo da usare il gateway della rete virtuale dell'hub per comunicare con reti remote. Per consentire il flusso del traffico gateway dallo spoke all'hub e connettersi a reti remote, è necessario:

  - Configurare la connessione peering reti virtuali nell'hub in modo da **consentire il transito gateway**.
  - Configurare la connessione peering reti virtuali in ogni spoke in modo da **usare gateway remoti**.
  - Configurare tutte le connessioni peering reti virtuali in modo da **consentire il traffico inoltrato**.

## <a name="considerations"></a>Considerazioni

### <a name="spoke-connectivity"></a>Connettività spoke

Se è necessaria la connettività tra spoke, è consigliabile implementare un'appliance virtuale di rete per il routing nell'hub e usare route definite dall'utente nello spoke per inoltrare il traffico all'hub.

![[2]][2]

In questo scenario occorre configurare le connessioni peering in modo da **consentire il traffico inoltrato**.

### <a name="overcoming-vnet-peering-limits"></a>Superamento dei limiti del peering reti virtuali

Assicurarsi di considerare il [limite sul numero di peering reti virtuali per ogni rete virtuale][vnet-peering-limit] in Azure. Se si stabilisce che saranno necessari più spoke di quanto consentito dal limite, valutare la creazione di una topologia hub-spoke-hub-spoke, in cui il primo livello di spoke funge anche da hub. Il diagramma seguente illustra questo approccio.

![[3]][3]

Considerare inoltre i servizi condivisi nell'hub, per assicurarsi che quest'ultimo sia scalabile per un numero superiore di spoke. Ad esempio, se l'hub fornisce servizi firewall, valutare i limiti di larghezza di banda della soluzione firewall quando si aggiungono più spoke. Può essere utile spostare alcuni dei servizi condivisi in un secondo livello di hub.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura è disponibile in [GitHub][ref-arch-repo]. Usa VM Ubuntu in ogni rete virtuale per testare la connettività. Nella subnet **shared-services** della **rete virtuale dell'hub** non sono effettivamente ospitati servizi.

### <a name="prerequisites"></a>prerequisiti

Prima di poter distribuire l'architettura di riferimento nella propria sottoscrizione, è necessario eseguire i passaggi seguenti.

1. Clonare, creare una copia tramite fork o scaricare il file ZIP per il repository GitHub delle [architetture di riferimento][ref-arch-repo].

2. Verificare che nel computer sia installata l'interfaccia della riga di comando di Azure 2.0. Per istruzioni sull'installazione dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-2].

3. Installare il pacchetto npm dei [blocchi predefiniti di Azure][azbb].

4. Al prompt dei comandi, di Bash o di PowerShell accedere all'account Azure con questo comando e seguire le istruzioni.

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter-using-azbb"></a>Distribuire il data center locale simulato con azbb

Per distribuire il data center locale simulato come rete virtuale di Azure, seguire questa procedura:

1. Passare alla cartella `hybrid-networking\hub-spoke\` per il repository scaricato nel passaggio dei prerequisiti precedente.

2. Aprire il file `onprem.json` e immettere un nome utente e una password tra le virgolette nelle righe 36 e 37, come illustrato di seguito, quindi salvare il file.

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

3. Nella riga 38 digitare `Windows` o `Linux` per `osType` per installare Windows Server 2016 Datacenter o Ubuntu 16.04 come sistema operativo per il jumpbox.

4. Eseguire `azbb` per distribuire l'ambiente locale simulato, come illustrato di seguito.

   ```bash
   azbb -s <subscription_id> -g onprem-vnet-rg - l <location> -p onoprem.json --deploy
   ```
   > [!NOTE]
   > Se si decide di usare un nome del gruppo di risorse diverso da `onprem-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

5. Attendere il completamento della distribuzione. Questa distribuzione crea una rete virtuale, una macchina virtuale e un gateway VPN. La creazione del gateway VPN può richiedere più di 40 minuti.

### <a name="azure-hub-vnet"></a>Rete virtuale dell'hub di Azure

Per distribuire la rete virtuale dell'hub e connettersi alla rete virtuale locale simulata creata in precedenza, eseguire la procedura seguente.

1. Aprire il file `hub-vnet.json` e immettere un nome utente e una password tra le virgolette nelle righe 39 e 40, come illustrato di seguito.

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. Nella riga 41 digitare `Windows` o `Linux` per `osType` per installare Windows Server 2016 Datacenter o Ubuntu 16.04 come sistema operativo per il jumpbox.

3. Immettere una chiave condivisa tra le virgolette nella riga 72, come illustrato di seguito, quindi salvare il file.

   ```bash
   "sharedKey": "",
   ```

4. Eseguire `azbb` per distribuire l'ambiente locale simulato, come illustrato di seguito.

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet.json --deploy
   ```
   > [!NOTE]
   > Se si decide di usare un nome del gruppo di risorse diverso da `hub-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

5. Attendere il completamento della distribuzione. Questa distribuzione crea una rete virtuale, una macchina virtuale, un gateway VPN e una connessione al gateway creato nella sezione precedente. La creazione del gateway VPN può richiedere più di 40 minuti.

### <a name="optional-test-connectivity-from-onprem-to-hub"></a>(Facoltativo) Testare la connettività dall'ambiente locale all'hub

Per testare la connettività dall'ambiente locale simulato alla rete virtuale hub con VM Windows, seguire questa procedura.

1. Dal portale di Azure passare al gruppo di risorse `onprem-jb-rg` e quindi fare clic sulla risorsa macchina virtuale `jb-vm1`.

2. Nell'angolo superiore sinistro del pannello della VM nel portale fare clic su `Connect` e seguire le istruzioni per connettersi alla VM con Desktop remoto. Assicurarsi di usare il nome utente e la password specificati nelle righe 36 e 37 del file `onprem.json`.

3. Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi alla VM jumpbox dell'hub, come illustrato di seguito.

   ```powershell
   Test-NetConnection 10.0.0.68 -CommonTCPPort RDP
   ```
   > [!NOTE]
   > Per impostazione predefinita, le VM Windows Server non consentono risposte ICMP in Azure. Se si vuole usare `ping` per testare la connettività, è necessario abilitare il traffico ICMP in Windows Firewall con sicurezza avanzata per ogni VM.

Per testare la connettività dall'ambiente locale simulato alla rete virtuale hub con VM Linux, seguire questa procedura:

1. Dal portale di Azure passare al gruppo di risorse `onprem-jb-rg` e quindi fare clic sulla risorsa macchina virtuale `jb-vm1`.

2. Nell'angolo superiore sinistro del pannello della VM nel portale fare clic su `Connect` e quindi copiare il comando `ssh` visualizzato nel portale. 

3. Al prompt Linux eseguire `ssh` per connettersi al jumpbox dell'ambiente locale simulato con le informazioni copiate nel passaggio 2 precedente, come illustrato di seguito.

   ```bash
   ssh <your_user>@<public_ip_address>
   ```

4. Usare la password specificata nella riga 37 del file `onprem.json` per connettersi alla VM.

5. Usare il comando `ping` per testare la connettività al jumpbox dell'hub, come illustrato di seguito.

   ```bash
   ping 10.0.0.68
   ```

### <a name="azure-spoke-vnets"></a>Reti virtuali spoke di Azure

Per distribuire le reti virtuali spoke, seguire questa procedura.

1. Aprire il file `spoke1.json` e immettere un nome utente e una password tra le virgolette nelle righe 47 e 48, come illustrato di seguito, quindi salvare il file.

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```

2. Nella riga 49 digitare `Windows` o `Linux` per `osType` per installare Windows Server 2016 Datacenter o Ubuntu 16.04 come sistema operativo per il jumpbox.

3. Eseguire `azbb` per distribuire l'ambiente della prima rete virtuale spoke, come illustrato di seguito.

   ```bash
   azbb -s <subscription_id> -g spoke1-vnet-rg - l <location> -p spoke1.json --deploy
   ```
  
   > [!NOTE]
   > Se si decide di usare un nome del gruppo di risorse diverso da `spoke1-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

4. Ripetere il passaggio 1 precedente per il file `spoke2.json`.

5. Eseguire `azbb` per distribuire l'ambiente della seconda rete virtuale spoke, come illustrato di seguito.

   ```bash
   azbb -s <subscription_id> -g spoke2-vnet-rg - l <location> -p spoke2.json --deploy
   ```
   > [!NOTE]
   > Se si decide di usare un nome del gruppo di risorse diverso da `spoke2-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a>Peering reti virtuali dell'hub di Azure alle reti virtuali spoke

Per creare una connessione peering dalla rete virtuale hub alle reti virtuali spoke, seguire questa procedura.

1. Aprire il file `hub-vnet-peering.json` e verificare che il nome del gruppo di risorse e il nome della rete virtuale per ogni peering reti virtuali, a partire dalla riga 29, siano corretti.

2. Eseguire `azbb` per distribuire l'ambiente della prima rete virtuale spoke, come illustrato di seguito.

   ```bash
   azbb -s <subscription_id> -g hub-vnet-rg - l <location> -p hub-vnet-peering.json --deploy
   ```

   > [!NOTE]
   > Se si decide di usare un nome del gruppo di risorse diverso da `hub-vnet-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

### <a name="test-connectivity"></a>Testare la connettività

Per testare la connettività dall'ambiente locale simulato alle reti virtuali spoke con VM Windows, seguire questa procedura.

1. Dal portale di Azure passare al gruppo di risorse `onprem-jb-rg` e quindi fare clic sulla risorsa macchina virtuale `jb-vm1`.

2. Nell'angolo superiore sinistro del pannello della VM nel portale fare clic su `Connect` e seguire le istruzioni per connettersi alla VM con Desktop remoto. Assicurarsi di usare il nome utente e la password specificati nelle righe 36 e 37 del file `onprem.json`.

3. Aprire una console PowerShell nella VM e usare il cmdlet `Test-NetConnection` per verificare la possibilità di connettersi alla VM jumpbox dell'hub, come illustrato di seguito.

   ```powershell
   Test-NetConnection 10.1.0.68 -CommonTCPPort RDP
   Test-NetConnection 10.2.0.68 -CommonTCPPort RDP
   ```

Per testare la connettività dall'ambiente locale simulato alle reti virtuali spoke con VM Linux, seguire questa procedura:

1. Dal portale di Azure passare al gruppo di risorse `onprem-jb-rg` e quindi fare clic sulla risorsa macchina virtuale `jb-vm1`.

2. Nell'angolo superiore sinistro del pannello della VM nel portale fare clic su `Connect` e quindi copiare il comando `ssh` visualizzato nel portale. 

3. Al prompt Linux eseguire `ssh` per connettersi al jumpbox dell'ambiente locale simulato con le informazioni copiate nel passaggio 2 precedente, come illustrato di seguito.

   ```bash
   ssh <your_user>@<public_ip_address>
   ```

4. Usare la password specificata nella riga 37 del file `onprem.json` per connettersi alla VM.

5. Usare il comando `ping` per testare la connettività alle VM jumpbox in ogni spoke, come illustrato di seguito.

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

### <a name="add-connectivity-between-spokes"></a>Aggiungere la connettività tra spoke

Se si vuole consentire la connessione tra gli spoke, è necessario usare un'appliance virtuale di rete come router nella rete virtuale hub e forzare il traffico dagli spoke verso il router in caso di tentativo di connessione a un altro spoke. Per distribuire un'appliance virtuale di rete di esempio di base come una singola VM e le route definite dall'utente necessarie per consentire la connessione di due reti virtuali spoke, seguire questa procedura:

1. Aprire il file `hub-nva.json` e immettere un nome utente e una password tra le virgolette nelle righe 13 e 14, come illustrato di seguito, quindi salvare il file.

   ```bash
   "adminUsername": "XXX",
   "adminPassword": "YYY",
   ```
2. Eseguire `azbb` per distribuire la VM dell'appliance virtuale di rete e le route definite dall'utente.

   ```bash
   azbb -s <subscription_id> -g hub-nva-rg - l <location> -p hub-nva.json --deploy
   ```
   > [!NOTE]
   > Se si decide di usare un nome del gruppo di risorse diverso da `hub-nva-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[best-practices-security]: /azure/best-practices-network-securit
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[linux-vm-ra]: ../virtual-machines-linux/index.md
[hybrid-ha]: ./expressroute-vpn-failover.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vnet-peering]: /azure/virtual-network/virtual-network-peering-overview
[vnet-peering-limit]: /azure/azure-subscription-service-limits#networking-limits
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[windows-vm-ra]: ../virtual-machines-windows/index.md

[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/hub-spoke.png "Topologia hub-spoke in Azure"
[1]: ./images/hub-spoke-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo usando un'appliance virtuale di rete"
[3]: ./images/hub-spokehub-spoke.svg "Topologia hub-spoke-hub-spoke in Azure"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
