---
title: Implementazione di un'architettura di rete ibrida a disponibilità elevata
description: Come implementare un'architettura di rete sicura da sito a sito, che si estende su una rete virtuale di Azure e su una rete locale connessa tramite ExpressRoute con failover del gateway VPN.
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Connect an on-premises network to Azure
pnp.series.prev: expressroute
cardTitle: Improving availability
ms.openlocfilehash: 31ed1dbf59c4fa2b7fa86b9ceb2fed7b36e75c8c
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47428823"
---
# <a name="connect-an-on-premises-network-to-azure-using-expressroute-with-vpn-failover"></a>Connettere una rete locale ad Azure tramite ExpressRoute con failover VPN

Questa architettura di riferimento illustra come connettere una rete locale a una rete virtuale di Azure (VNet) usando ExpressRoute, tramite una rete privata virtuale da sito a sito (VPN), che funge da connessione di failover. Flussi di traffico tra la rete locale e la rete virtuale di Azure tramite una connessione ExpressRoute. Se si verifica una perdita di connettività del circuito ExpressRoute, il traffico viene indirizzato attraverso un tunnel per VPN IPSec. [**Distribuire questa soluzione**.](#deploy-the-solution)

Si noti che se il circuito ExpressRoute non è disponibile, la route VPN gestisce solo le connessioni di peering private. Le connessioni di peering pubblico e peering Microsoft passano attraverso Internet. 

![[0]][0]

*Scaricare un [file Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architettura 

L'architettura è costituita dai componenti seguenti.

* **Rete locale**. Una rete LAN privata in esecuzione all'interno di un'organizzazione.

* **Appliance VPN**. Un dispositivo o un servizio che offre connettività esterna alla rete locale. L'appliance VPN può essere un dispositivo hardware o una soluzione software come il Servizio Routing e Accesso remoto (RRAS) in Windows Server 2012. Per una lista delle appliance VPN supportate e per informazioni sulla configurazione di appliance VPN selezionate per connettersi ad Azure, vedere [Informazioni sui dispositivi VPN e sui parametri IPsec/IKE per connessioni del Gateway VPN da sito a sito][vpn-appliance].

* **Circuito ExpressRoute**: Un circuito di livello 2 o di livello 3 fornito dal provider di connettività che unisce la rete locale ad Azure attraverso i router perimetrali. Il circuito usa l'infrastruttura hardware gestita dal provider di connettività.

* **Gateway di rete virtuale per ExpressRoute**: il gateway di rete virtuale per ExpressRoute consente di connettere la rete virtuale al circuito ExpressRoute, usato per la connettività, con la rete locale.

* **Gateway di rete virtuale VPN**: il gateway di rete virtuale VPN consente di connettere la rete virtuale all'appliance VPN nella rete locale. Il gateway di rete virtuale VPN è configurato per accettare le richieste provenienti dalla rete locale solo tramite l'appliance VPN. Per maggiori informazioni, consultare [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet].

* **Connessione VPN**: la connessione ha proprietà che specificano il tipo di connessione (IPSec) e la chiave condivisa con l'appliance VPN locale per crittografare il traffico.

* **Rete virtuale di Azure (VNet)**: ogni rete virtuale si trova in una singola area di Azure e può contenere più livelli applicazione. I livelli dell'applicazione possono essere segmentati usando subnet in ogni rete virtuale.

* **Subnet del gateway**: i gateway di rete virtuale vengono mantenuti nella stessa subnet.

* **Applicazione cloud**: Applicazione contenuta in Azure. Può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure. Per altre informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] ed [Esecuzione di carichi di lavoro della macchina virtuale di Linux][linux-vm-ra].

## <a name="recommendations"></a>Consigli

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.

### <a name="vnet-and-gatewaysubnet"></a>Rete virtuali e GatewaySubnet

Creare il gateway di rete virtuale per ExpressRoute e il gateway di rete virtuale VPN nella stessa rete virtuale. Ciò significa che condividono la stessa subnet denominata *GatewaySubnet*.

Se la rete virtuale include già una subnet denominata *GatewaySubnet*, verificare che abbia uno spazio di indirizzi /27 o maggiore. Se la subnet esistente è troppo piccola, usare il comando PowerShell seguente per rimuoverla: 

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

Se la rete virtuale non contiene una subnet denominata **GatewaySubnet**, crearne una nuova usando il comando Powershell seguente:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a>VPN e gateway ExpressRoute

Verificare che l'organizzazione sia in linea con i [Prerequisiti di ExpressRoute][expressroute-prereq] per connettersi ad Azure.

Se si dispone già di un gateway di rete virtuale VPN nella rete virtuale di Azure, usare il comando Powershell seguente per rimuoverlo:

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

Seguire le istruzioni in [Implementing a hybrid network architecture with Azure ExpressRoute][implementing-expressroute] (Implementazione di un'architettura di rete ibrida con Azure ExpressRoute) per stabilire la connessione ExpressRoute.

Seguire le istruzioni in [Implementing a hybrid network architecture with Azure and On-premises VPN][implementing-vpn] (Implementazione di un'architettura di rete ibrida con Azure e con la rete VPN locale) per stabilire la connessione del gateway di rete virtuale VPN.

Dopo avere stabilito le connessioni del gateway di rete virtuale, verificare l'ambiente come segue:

1. Verificare che sia possibile connettersi dalla rete locale a quella virtuale di Azure.
2. Contattare il provider per arrestare la connettività a ExpressRoute per la verifica.
3. Verificare che sia ancora possibile connettersi dalla rete locale alla rete virtuale di Azure tramite la connessione del gateway di rete virtuale VPN.
4. Contattare il provider per ristabilire la connettività a ExpressRoute.

## <a name="considerations"></a>Considerazioni

Per considerazioni su ExpressRoute, vedere il materiale sussidiario [Implementing a hybrid network architecture with Azure ExpressRoute][guidance-expressroute] (Implementazione di un'architettura di rete ibrida con Azure ExpressRoute).

Per considerazioni su VPN da sito a sito, vedere il materiale sussidiario [Implementing a hybrid network architecture with Azure and On-premises VPN][guidance-vpn] (Implementazione di un'architettura di rete ibrida con Azure e con la rete VPN locale).

Per considerazioni sulla sicurezza generale di Azure, vedere [Servizi cloud Microsoft e sicurezza di rete][best-practices-security].

## <a name="deploy-the-solution"></a>Distribuire la soluzione

**Prerequisiti.** È necessario disporre di un'infrastruttura locale esistente già configurata con un'appliance di rete adatta.

Per distribuire la soluzione, seguire questa procedura.

1. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute-vpn-failover%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Attendere che il collegamento si apra nel portale di Azure e quindi eseguire questi passaggi:   
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-hybrid-vpn-er-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
3. Attendere il completamento della distribuzione.
4. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute-vpn-failover%2Fazuredeploy-expressRouteCircuit.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
5. Attendere che il collegamento si apra nel Portale di Azure, successivamente premere Invio ed eseguire i passaggi seguenti:
   * Selezionare **Usa esistente** nella sezione **Gruppo di risorse** e immettere `ra-hybrid-vpn-er-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.

<!-- links -->

[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md


[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[expressroute-prereq]: /azure/expressroute/expressroute-prerequisites
[implementing-expressroute]: ./expressroute.md
[implementing-vpn]: ./vpn.md
[guidance-expressroute]: ./expressroute.md
[guidance-vpn]: ./vpn.md
[best-practices-security]: /azure/best-practices-network-security
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[0]: ./images/expressroute-vpn-failover.png "Struttura di un'architettura di rete ibrida a disponibilità elevata con ExpressRoute e gateway VPN"
