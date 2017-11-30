---
title: Implementazione una topologia di rete hub-spoke in Azure
description: Come implementare una topologia di rete hub-spoke in Azure.
author: telmosampaio
ms.date: 05/05/2017
pnp.series.title: Implement a hub-spoke network topology in Azure
pnp.series.prev: expressroute
ms.openlocfilehash: e6f07a7962dd5728226b023700268340590d97a3
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
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

## <a name="architecture"></a>Architettura

Questa architettura è costituita dai componenti seguenti.

* **Rete locale**. Una rete LAN privata in esecuzione all'interno di un'organizzazione.

* **Dispositivo VPN**. Un dispositivo o un servizio che offre connettività esterna alla rete locale. Il dispositivo VPN può essere un dispositivo hardware o una soluzione software, ad esempio il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012. Per un elenco delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN per le connessioni di gateway VPN da sito a sito][vpn-appliance].

* **Gateway di rete virtuale VPN o gateway ExpressRoute**. Il gateway di rete virtuale consente di connettere la rete virtuale al dispositivo VPN o al circuito ExpressRoute usato per la connettività con la propria rete locale. Per altre informazioni, vedere [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].

> [!NOTE]
> Gli script di distribuzione per questa architettura di riferimento usano un gateway VPN per la connettività e una rete virtuale in Azure per simulare la rete locale.

* **Rete virtuale dell'hub**. Rete virtuale di Azure usata come hub nella topologia hub-spoke. L'hub è il punto di connettività centrale alla rete locale e offre una posizione in cui ospitare servizi che possono essere utilizzati da carichi di lavoro diversi ospitati nelle reti virtuali spoke.

* **Subnet del gateway**. I gateway di rete virtuale vengono mantenuti nella stessa subnet.

* **Subnet dei servizi condivisi**. Una subnet nella rete virtuale dell'hub usata per ospitare servizi che possono essere condivisi tra tutti gli spoke, ad esempio DNS o Active Directory Domain Services.

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

### <a name="prerequisites"></a>Prerequisiti

Prima di poter distribuire l'architettura di riferimento nella propria sottoscrizione, è necessario eseguire i passaggi seguenti.

1. Clonare, creare una copia tramite fork o scaricare il file ZIP per il repository GitHub delle [architetture di riferimento AzureCAT][ref-arch-repo].

2. Se si preferisce usare l'interfaccia della riga di comando di Azure, verificare che nel computer sia installata l'interfaccia della riga di comando di Azure 2.0. Per installare l'interfaccia della riga di comando, seguire le istruzioni in [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-2].

3. Se si preferisce usare PowerShell, verificare che nel computer sia installato il modulo PowerShell per Azure più recente. Per installare l'ultimo modulo Azure PowerShell, seguire le istruzioni in [Installare PowerShell per Azure][azure-powershell].

4. Da un prompt dei comandi, di Bash o di PowerShell accedere al proprio account di Azure usando uno dei comandi riportati di seguito e seguire le istruzioni.

  ```bash
  az login
  ```

  ```powershell
  Login-AzureRmAccount
  ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a>Distribuire il data center locale simulato

Per distribuire il data center locale simulato come rete virtuale di Azure, eseguire la procedura seguente.

1. Passare alla cartella `hybrid-networking\hub-spoke\onprem` per il repository scaricato nel passaggio dei prerequisiti precedente.

2. Aprire il file `onprem.vm.parameters.json` e immettere un nome utente e una password tra le virgolette nelle righe 11 e 12, come illustrato di seguito, quindi salvare il file.

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. Eseguire il comando di Bash o PowerShell seguente per distribuire l'ambiente locale simulato come una virtuale in Azure. Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.

  ```bash
  sh ./onprem.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-onprem-rg \
    --location westus
  ```

  ```powershell
  ./onprem.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-onprem-rg `
    -Location westus
  ```
  > [!NOTE]
  > Se si decide di usare un nome del gruppo di risorse diverso da `ra-onprem-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

4. Attendere il completamento della distribuzione. Questa distribuzione crea una rete virtuale, una macchina virtuale che esegue Ubuntu e un gateway VPN. La creazione del gateway VPN può richiedere più di 40 minuti.

### <a name="azure-hub-vnet"></a>Rete virtuale dell'hub di Azure

Per distribuire la rete virtuale dell'hub e connettersi alla rete virtuale locale simulata creata in precedenza, eseguire la procedura seguente.

1. Passare alla cartella `hybrid-networking\hub-spoke\hub` per il repository scaricato nel passaggio dei prerequisiti precedente.

2. Aprire il file `hub.vm.parameters.json` e immettere un nome utente e una password tra le virgolette nelle righe 11 e 12, come illustrato di seguito, quindi salvare il file.

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. Aprire il file `hub.gateway.parameters.json` e immettere una chiave condivisa tra le virgolette nella riga 23, come illustrato di seguito, quindi salvare il file. Prendere nota di questo valore, che sarà necessario più avanti nella distribuzione.

  ```bash
  "sharedKey": "",
  ```

4. Eseguire il comando di Bash o PowerShell seguente per distribuire l'ambiente locale simulato come una virtuale in Azure. Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.

  ```bash
  sh ./hub.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus
  ```

  ```powershell
  ./hub.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus
  ```
  > [!NOTE]
  > Se si decide di usare un nome del gruppo di risorse diverso da `ra-hub-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

5. Attendere il completamento della distribuzione. Questa distribuzione crea una rete virtuale, una macchina virtuale che esegue Ubuntu, un gateway VPN e una connessione al gateway creato nella sezione precedente. La creazione del gateway VPN può richiedere più di 40 minuti.

### <a name="connection-from-on-premises-to-the-hub"></a>Connessione da locale all'hub

Per connettersi dal data center locale simulato alla rete virtuale dell'hub, eseguire la procedura seguente.

1. Passare alla cartella `hybrid-networking\hub-spoke\onprem` per il repository scaricato nel passaggio dei prerequisiti precedente.

2. Aprire il file `onprem.connection.parameters.json` e immettere una chiave condivisa tra le virgolette nella riga 9, come illustrato di seguito, quindi salvare il file. Il valore di questa chiave condivisa deve essere lo stesso usato nel gateway locale distribuito in precedenza.

  ```bash
  "sharedKey": "",
  ```

3. Eseguire il comando di Bash o PowerShell seguente per distribuire l'ambiente locale simulato come una virtuale in Azure. Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.

  ```bash
  sh ./onprem.connection.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-onprem-rg \
    --location westus
  ```

  ```powershell
  ./onprem.connection.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-onprem-rg `
    -Location westus
  ```
  > [!NOTE]
  > Se si decide di usare un nome del gruppo di risorse diverso da `ra-onprem-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

4. Attendere il completamento della distribuzione. Questa distribuzione crea una connessione tra la rete virtuale usata per simulare un data center locale e la rete virtuale dell'hub.

### <a name="azure-spoke-vnets"></a>Reti virtuali spoke di Azure

Per distribuire le reti virtuali spoke e connettersi alla rete virtuale dell'hub creata in precedenza, eseguire la procedura seguente.

1. Passare alla cartella `hybrid-networking\hub-spoke\spokes` per il repository scaricato nel passaggio di prerequisiti precedente.

2. Aprire il file `spoke1.web.parameters.json` e immettere un nome utente e una password tra le virgolette nelle righe 53 e 54, come illustrato di seguito, quindi salvare il file.

  ```bash
  "adminUsername": "XXX",
  "adminPassword": "YYY",
  ```

3. Ripetere il passaggio precedente per il file `spoke2.web.parameters.json`.

4. Eseguire il comando di Bash o PowerShell seguente per distribuire il primo spoke e connetterlo all'hub. Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.

  ```bash
  sh ./spoke.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke1-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./spoke.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke1-rg `
    -Location westus `
    -Spoke 1
  ```
  > [!NOTE]
  > Se si decide di usare un nome del gruppo di risorse diverso da `ra-spoke1-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

5. Attendere il completamento della distribuzione. Questa distribuzione crea una rete virtuale, un sistema di bilanciamento del carico con tre macchine virtuali che eseguono Ubuntu e Apache e una connessione peering reti virtuali alla rete virtuale dell'hub creata nella sezione precedente. La distribuzione potrebbe richiedere più di 20 minuti.

6. Eseguire il comando di Bash o PowerShell seguente per distribuire il primo spoke e connetterlo all'hub. Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.

  ```bash
  sh ./spoke.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke2-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./spoke.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke2-rg `
    -Location westus `
    -Spoke 2
  ```
  > [!NOTE]
  > Se si decide di usare un nome del gruppo di risorse diverso da `ra-spoke2-rg`, assicurarsi di cercare tutti i file di parametri che usano tale nome e modificarli in modo da usare il nome del gruppo di risorse scelto.

5. Attendere il completamento della distribuzione. Questa distribuzione crea una rete virtuale, un sistema di bilanciamento del carico con tre macchine virtuali che eseguono Ubuntu e Apache e una connessione peering reti virtuali alla rete virtuale dell'hub creata nella sezione precedente. La distribuzione potrebbe richiedere più di 20 minuti.

### <a name="azure-hub-vnet-peering-to-spoke-vnets"></a>Peering reti virtuali dell'hub di Azure alle reti virtuali spoke

Per distribuire le connessioni peering reti virtuali per la rete virtuale dell'hub, eseguire la procedura seguente.

1. Passare alla cartella `hybrid-networking\hub-spoke\hub` per il repository scaricato nel passaggio di prerequisiti precedente.

2. Eseguire il comando di Bash o PowerShell seguente per distribuire la connessione peering al primo spoke. Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.

  ```bash
  sh ./hub.peering.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./hub.peering.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus `
    -Spoke 1
  ```

2. Eseguire il comando di Bash o PowerShell seguente per distribuire la connessione peering al secondo spoke. Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.

  ```bash
  sh ./hub.peering.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-hub-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./hub.peering.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-hub-rg `
    -Location westus `
    -Spoke 2
  ```

### <a name="test-connectivity"></a>Testare la connettività

Per verificare che la distribuzione di una topologia hub-spoke connessa a un data center locale abbia avuto esito positivo, eseguire questi passaggi.

1. Dal [portale di Azure][portale] connettersi alla propria sottoscrizione e passare alla macchina virtuale `ra-onprem-vm1` nel gruppo di risorse `ra-onprem-rg`.

2. Nel pannello `Overview` prendere nota del valore `Public IP address` per la macchina virtuale.

3. Usare un client SSH per eseguire la connessione all'indirizzo IP annotato in precedenza, inserendo il nome utente e la password specificati durante la distribuzione.

4. Dal prompt dei comandi nella macchina virtuale a cui ci si è connessi, eseguire il comando seguente per testare la connettività tra la rete virtuale locale e la rete virtuale Spoke1.

  ```bash
  ping 10.1.1.37
  ```

### <a name="add-connectivity-between-spokes"></a>Aggiungere la connettività tra spoke

Se si vuole consentire la connessione tra gli spoke, è necessario distribuire a ogni spoke route definite dall'utente che inoltrino il traffico destinato agli altri spoke al gateway nella rete virtuale dell'hub. Eseguire la procedura seguente per verificare che attualmente non sia possibile connettersi da uno spoke a un altro, quindi distribuire le route definite dall'utente e ripetere il test della connettività.

1. Ripetere i precedenti passaggi da 1 a 4, se non si è più connessi alla VM Jumpbox.

2. Connettersi a uno dei server Web nello spoke 1.

  ```bash
  ssh 10.1.1.37
  ```

3. Testare la connettività tra spoke 1 e spoke 2. Dovrebbe avere esito negativo.

  ```bash
  ping 10.1.2.37
  ```

4. Tornare al prompt dei comandi del computer.

5. Passare alla cartella `hybrid-networking\hub-spoke\spokes` per il repository scaricato nel passaggio dei prerequisiti precedente.

6. Eseguire il comando di Bash o PowerShell seguente per distribuire una route definita dall'utente al primo spoke. Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.

  ```bash
  sh ./spoke.udr.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke1-rg \
    --location westus \
    --spoke 1
  ```

  ```powershell
  ./spoke.udr.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke1-rg `
    -Location westus `
    -Spoke 1
  ```

7. Eseguire il comando di Bash o PowerShell seguente per distribuire una route definita dall'utente al secondo spoke. Sostituire i valori con la propria sottoscrizione, il nome del gruppo di risorse e l'area di Azure.

  ```bash
  sh ./spoke.udr.deploy.sh --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --resourcegroup ra-spoke2-rg \
    --location westus \
    --spoke 2
  ```

  ```powershell
  ./spoke.udr.deploy.ps1 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx `
    -ResourceGroup ra-spoke2-rg `
    -Location westus `
    -Spoke 2
  ```

8. Tornare al terminale SSH.

9. Testare la connettività tra spoke 1 e spoke 2. Dovrebbe avere esito positivo.

  ```bash
  ping 10.1.2.37
  ```

<!-- links -->

[azure-cli-2]: /azure/install-azure-cli
[azure-powershell]: /powershell/azure/install-azure-ps?view=azuresmps-3.7.0
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

[visio-download]: https://archcenter.azureedge.net/cdn/hybrid-network-hub-spoke.vsdx
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[0]: ./images/hub-spoke.png "Topologia hub-spoke in Azure"
[1]: ./images/hub-spoke-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo"
[2]: ./images/hub-spoke-no-gateway-routing.svg "Topologia hub-spoke in Azure con routing transitivo usando un'appliance virtuale di rete"
[3]: ./images/hub-spokehub-spoke.svg "Topologia hub-spoke-hub-spoke in Azure"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
