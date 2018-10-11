---
title: Connettere una rete locale ad Azure tramite ExpressRoute
description: Come implementare un'architettura di rete sicura da sito a sito, che si estende su una rete virtuale di Azure e su una rete locale connessa tramite Azure ExpressRoute.
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Connect an on-premises network to Azure
pnp.series.next: expressroute-vpn-failover
pnp.series.prev: vpn
cardTitle: ExpressRoute
ms.openlocfilehash: 754542b37988e0cd2694ae84eb6b03d68c147243
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48819177"
---
# <a name="connect-an-on-premises-network-to-azure-using-expressroute"></a>Connettere una rete locale ad Azure tramite ExpressRoute

Questa architettura di riferimento illustra come connettere una rete locale a reti virtuali in Azure usando [Azure ExpressRoute][expressroute-introduction]. Le connessioni ExpressRoute usano una connessione privata dedicata tramite un provider di connettività di terze parti. La connessione privata estende la rete locale in Azure. [**Distribuire questa soluzione**.](#deploy-the-solution)

![[0]][0]

*Scaricare un [file Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

* **Rete aziendale locale**. Una rete LAN privata in esecuzione all'interno di un'organizzazione.

* **Circuito ExpressRoute**. Un circuito di livello 2 o di livello 3 fornito dal provider di connettività che unisce la rete locale ad Azure attraverso i router perimetrali. Il circuito usa l'infrastruttura hardware gestita dal provider di connettività.

* **Router perimetrali locali**. Router che connettono la rete locale al circuito gestito dal provider. A seconda di come viene eseguito il provisioning della connessione, è necessario fornire gli indirizzi IP pubblici usati dai router.
* **Router perimetrali Microsoft**. Due router in una configurazione a disponibilità elevata di tipo attivo-attivo. Questi router consentono a un provider di connettività di connettere i circuiti direttamente al data center. A seconda di come viene eseguito il provisioning della connessione, è necessario fornire gli indirizzi IP pubblici usati dai router.

* **Reti virtuali di Azure**. Ogni rete virtuale si trova in una singola area di Azure e può contenere più livelli applicazione. I livelli applicazione possono essere segmentati usando subnet in ogni rete virtuale.

* **Servizi pubblici di Azure**. Servizi di Azure che possono essere usati all'interno di un'applicazione ibrida. Questi servizi sono disponibili anche tramite Internet, ma l'accesso tramite un circuito ExpressRoute assicura bassa latenza e prestazioni più prevedibili, perché il traffico non passa attraverso Internet. Le connessioni vengono eseguite usando il [peering pubblico][expressroute-peering], con indirizzi di proprietà dell'organizzazione o forniti dal provider di connettività.

* **Servizi di Office 365**. Le applicazioni e i servizi di Office 365 disponibili pubblicamente forniti da Microsoft. Le connessioni vengono eseguite usando il [peering Microsoft][expressroute-peering], con indirizzi di proprietà dell'organizzazione o forniti dal provider di connettività. È inoltre possibile connettersi direttamente a Microsoft CRM Online tramite il peering Microsoft.

* **Provider di connettività** (non visualizzato). Società che forniscono una connessione usando la connettività di livello 2 o di livello 3 tra il data center dell'utente e un data center di Azure.

## <a name="recommendations"></a>Consigli

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.

### <a name="connectivity-providers"></a>Provider di connettività

Selezionare un provider di connettività ExpressRoute adatto alla propria località. Per ottenere un elenco di provider di connettività disponibili per la propria località, usare il comando di Azure PowerShell seguente:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

I provider di connettività di ExpressRoute connettono il data center dell'utente a Microsoft nei modi seguenti:

* **Percorso condiviso in una struttura con scambio cloud**. Nel caso di percorso condiviso in una struttura con scambio cloud, è possibile ordinare Cross Connection virtuali ad Azure tramite lo scambio Ethernet del provider di condivisione del percorso. I provider di condivisione del percorso possono fornire connessioni incrociate di livello 2 oppure gestite di livello 3 tra l'infrastruttura nella struttura di condivisione percorso e Azure.
* **Connessioni Ethernet da punto a punto**. È possibile connettere i data center o gli uffici locali ad Azure tramite collegamenti Ethernet punto a punto. I provider Ethernet punto a punto forniscono connessioni di livello 2 o connessioni gestite di livello 3 tra la sede dell'utente e Azure.
* **Reti Any-to-any (IPVPN)**. È possibile integrare una rete WAN con Azure. I provider IPVPN (in genere VPN MPLS) forniscono connettività any-to-any tra le succursali e i data center. Azure può essere interconnesso a una rete WAN in modo che abbia l'aspetto di qualsiasi altra succursale. I provider WAN offrono in genere connettività gestita di livello 3.

Per altre informazioni sui provider di connettività, vedere [Introduzione a ExpressRoute][expressroute-introduction].

### <a name="expressroute-circuit"></a>Circuito ExpressRoute

Assicurarsi che l'organizzazione sia in linea con i [Prerequisiti di ExpressRoute][expressroute-prereqs] per connettersi ad Azure.

Se non è già stato fatto, aggiungere una subnet denominata `GatewaySubnet` alla rete virtuale di Azure e creare un gateway di rete virtuale ExpressRoute tramite il servizio gateway VPN di Azure. Per altre informazioni su questo processo, vedere [Flussi di lavoro ExpressRoute per provisioning di un circuito e stati di circuito][ExpressRoute-provisioning].

Creare un circuito ExpressRoute nel modo seguente:

1. Eseguire il seguente comando PowerShell:
   
    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```
2. Inviare `ServiceKey` per il nuovo percorso al provider di servizi.

3. Attendere che il provider esegua il provisioning del circuito. Per verificare lo stato del provisioning di un circuito, eseguire il seguente comando PowerShell:
   
    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

    Il campo `Provisioning state` nella sezione `Service Provider` dell'output che passerà da `NotProvisioned` a `Provisioned` quando il circuito è pronto.

    > [!NOTE]
    > Se si usa una connessione di livello 3, il provider deve configurare e gestire il routing per l'utente. Fornire le informazioni necessarie per consentire al provider di implementare le route appropriate.
    > 
    > 

4. Se si usa una connessione di livello 2:

    1. Riservare due subnet /30 costituite da indirizzi IP pubblici validi per ogni tipo di peering che si vuole implementare. Queste subnet /30 verranno usate per fornire indirizzi IP per i router usati per il circuito. Se si sta implementano un peering privato, pubblico e Microsoft, sono necessarie 6 subnet /30 con indirizzi IP pubblici validi.     

    2. Configurare il routing per il circuito ExpressRoute. Eseguire i seguenti comandi PowerShell per ciascun tipo di peering che si intende configurare (privato, pubblico e Microsoft). Per altre informazioni, vedere [Creare e modificare il routing per un circuito ExpressRoute][configure-expressroute-routing].
   
        ```powershell
        Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
        ```

    3. Riservare un altro pool di indirizzi IP pubblici validi da usare per NAT (Network Address Translation) per il peering Microsoft e pubblico. È consigliabile disporre di un pool diverso per ogni tipo di peering. Specificare il pool al provider di connettività, per consentirgli di configurare gli annunci BGP (Border Gateway Protocol) per tali intervalli.

5. Eseguire i comandi PowerShell seguenti per collegare le reti virtuali al circuito ExpressRoute. Per altre informazioni, vedere [Collegare una rete virtuale a un circuito ExpressRoute][link-vnet-to-expressroute].

    ```powershell
    $circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    $gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
    New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
    ```

È possibile connettere più reti virtuali situate in diverse aree dello stesso circuito ExpressRoute, a condizione che tutte le reti virtuali e il circuito ExpressRoute si trovino nella stessa area geopolitica.

### <a name="troubleshooting"></a>risoluzione dei problemi 

Se un circuito ExpressRoute precedentemente funzionante non si connette, in assenza di eventuali modifiche di configurazione in locale o all'interno della rete virtuale privata, potrebbe essere necessario contattare il provider di connettività e collaborare con lui per risolvere il problema. Usare i seguenti comandi Powershell per verificare che sia stato eseguito il provisioning del circuito ExpressRoute:

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

L'output di questo comando mostra diverse proprietà per il circuito, tra cui `ProvisioningState`, `CircuitProvisioningState` e `ServiceProviderProvisioningState` come illustrato di seguito.

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

Se `ProvisioningState` non è impostato su `Succeeded` dopo aver tentato di creare un nuovo circuito, rimuovere il circuito usando il comando di seguito e tentare di ricrearlo.

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Se il provider ha già eseguito il provisioning del circuito e `ProvisioningState` è impostato su `Failed` o `CircuitProvisioningState` non `Enabled` contattare il provider per ulteriore assistenza.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

I circuiti ExpressRoute offrono un percorso di larghezza di banda elevata tra le reti. In genere, maggiore è la larghezza di banda maggiore sarà il costo. 

ExpressRoute offre ai clienti due [piani tariffari] [ expressroute-pricing], un piano a consumo e un piano dati senza limiti. Gli addebiti variano a seconda della larghezza di banda del circuito. La larghezza di banda disponibile probabilmente varierà da provider a provider. Usare cmdlet `Get-AzureRmExpressRouteServiceProvider` per visualizzare i provider disponibili nella propria area geografica e le larghezze di banda offerte.
 
Un singolo circuito ExpressRoute può supportare un determinato numero di peering e collegamenti di rete virtuale. Per altre informazioni, vedere [Limiti di ExpressRoute](/azure/azure-subscription-service-limits).

Per un addebito aggiuntivo, il componente aggiuntivo ExpressRoute Premium offre alcune funzionalità aggiuntive:

* Maggiori limiti delle route per peering pubblici e privati. 
* Maggior numero di collegamenti della rete virtuale per il circuito ExpressRoute. 
* Connettività globale per i servizi.

Per altri dettagli, vedere [Prezzi ExpressRoute][expressroute-pricing]. 

I circuiti ExpressRoute sono progettati per consentire di potenziare fino al doppio il limite di larghezza di banda acquistato delle reti temporanee, senza alcun costo aggiuntivo. Questo risultato viene ottenuto tramite collegamenti ridondanti. Tuttavia, non tutti i provider di connettività supportano questa funzione. Verificare che il provider di connettività consenta questa funzionalità prima di dipendere da essa.

Anche se alcuni provider consentono di modificare la larghezza di banda, assicurarsi di scegliere una larghezza di banda iniziale superiore alle proprie esigenze e che offra spazio per la crescita. Se è necessario aumentare la larghezza di banda in futuro, restano a disposizione due opzioni:

- Aumentare la larghezza di banda. È consigliabile evitare questa opzione quando possibile inoltre, non tutti i provider consentono di aumentare in modo dinamico la larghezza di banda. Ma se è necessario un aumento della larghezza di banda, verificare con il provider se supporta la modifica delle proprietà della larghezza di banda ExpressRoute tramite i comandi Powershell. In caso affermativo, eseguire i comandi riportati di seguito.

    ```powershell
    $ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    $ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

    È possibile aumentare la larghezza di banda senza perdita di connettività. Il downgrade della larghezza di banda comporterà interruzioni della connettività, perché è necessario eliminare il circuito e ricrearlo con la nuova configurazione.

- Modificare il piano tariffario e/o eseguire l'aggiornamento a Premium. A tale scopo, eseguire i comandi seguenti. La proprietà `Sku.Tier` può essere `Standard` o `Premium`; la proprietà `Sku.Name` può essere `MeteredData` o `UnlimitedData`.

    ```powershell
    $ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

    $ckt.Sku.Tier = "Premium"
    $ckt.Sku.Family = "MeteredData"
    $ckt.Sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

    > [!IMPORTANT]
    > Verificare che la proprietà `Sku.Name` corrisponda a `Sku.Tier` e `Sku.Family`. Se si modifica la famiglia e il livello ma non il nome, la connessione verrà disabilitata.
    > 
    > 

    È possibile aggiornare lo SKU senza interruzioni, ma non è possibile passare dal piano tariffario illimitato a quello a consumo. Quando si esegue il downgrade dello SKU, il consumo di larghezza di banda deve rimanere entro il limite predefinito dello SKU standard.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

ExpressRoute non supporta protocolli di ridondanza router, ad esempio HSRP (Standby Routing Protocol) e VRRP (Virtual Router Redundancy Protocol) per implementare la disponibilità elevata. Usa invece una coppia ridondante di sessioni BGP per il peering. Per facilitare le connessioni a disponibilità elevata per la rete, Azure esegue il provisioning di due porte ridondanti su due router (parte di Microsoft Edge) in una configurazione di tipo attivo-attivo.

Per impostazione predefinita, le sessioni BGP usano un valore di timeout inattività di 60 secondi. In caso di tre timeout della sessione (180 secondi in totale), il router viene contrassegnato come non disponibile e tutto il traffico viene reindirizzato al router rimanente. Questo timeout di 180 secondi potrebbe essere troppo lungo per le applicazioni critiche. In questo caso, è possibile modificare le impostazioni di timeout BGP sul router locale su un valore inferiore.

È possibile configurare la disponibilità elevata per la connessione di Azure in modi diversi, in base al tipo di provider in uso e al numero di circuiti ExpressRoute e di connessioni del gateway di rete virtuale che si desidera configurare. Di seguito sono riepilogate le opzioni di disponibilità:

* Se si usa una connessione di livello 2, distribuire router ridondanti nella rete locale in una configurazione di tipo attivo-attivo. Connettere il circuito primario a un router e il circuito secondario all'altro. In questo modo si otterrà una connessione a disponibilità elevata a entrambe le estremità della connessione. Questo è necessario se si richiede il contratto di servizio di ExpressRoute. Per altri dettagli, vedere [Contratto di servizio per Azure ExpressRoute][sla-for-expressroute].

    Il diagramma seguente illustra una configurazione con router ridondanti locali connessi ai circuiti primari e secondari. Ogni circuito gestisce il traffico per un peering pubblico e un peering privato (a ogni peering viene designata una coppia di spazi di indirizzi /30, come descritto nella sezione precedente).

    ![[1]][1]

* Se si usa una connessione di livello 3, verificare che fornisca sessioni BGP ridondanti che gestiscono la disponibilità per l'utente.

* Connettere la rete virtuale a più circuiti ExpressRoute, forniti da diversi provider di servizi. Questa strategia offre funzionalità di disponibilità elevata e di ripristino di emergenza aggiuntive.

* Configurare una VPN da sito a sito come percorso di failover per ExpressRoute. Per altre informazioni su questa opzione, vedere [Connettere una rete locale ad Azure tramite ExpressRoute con failover VPN][highly-available-network-architecture].
 Questa opzione si applica solo al peering privato. Per i servizi di Azure e Office 365, Internet è l'unico percorso di failover. 

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

È possibile usare [AzureCT (Azure Connectivity Toolkit)] [ azurect] per controllare la connettività tra il data center locale e Azure. 

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

È possibile configurare opzioni di sicurezza per la connessione di Azure in diversi modi, a seconda delle preoccupazioni relative alla sicurezza e alle esigenze di conformità. 

ExpressRoute funzione al livello 3. Le minacce a livello dell'applicazione possono essere impedite usando un dispositivo di sicurezza di rete per limitare il traffico alle risorse legittime. Inoltre, le connessioni ExpressRoute tramite il peering pubblico possono essere avviate solo da locale. In questo modo si impedisce a un servizio non autorizzato di accedere e compromettere i dati in locale da Internet.

Per ottimizzare la sicurezza, aggiungere dispositivi di sicurezza di rete tra la rete locale e i router perimetrali dei provider. Ciò è utile per limitare il carico del traffico non autorizzato dalla rete virtuale:

![[2]][2]

Per motivi di conformità o di controllo, potrebbe essere necessario impedire l'accesso diretto da componenti in esecuzione nella rete virtuale a Internet e implementare il [tunneling forzato][forced-tuneling]. In questo caso, il traffico Internet deve essere reindirizzato attraverso un proxy in esecuzione in locale in cui può essere controllato. Il proxy può essere configurato per bloccare i flussi di traffico in uscita non autorizzato e filtrare il traffico in ingresso potenzialmente dannoso.

![[3]][3]

Per ottimizzare la sicurezza, non abilitare un indirizzo IP pubblico per le macchine virtuali e usare gruppi di sicurezza di rete per assicurarsi che tali macchine virtuali non siano accessibili pubblicamente. È consigliabile rendere disponibili le macchine virtuali solo tramite l'indirizzo IP interno.  Questi indirizzi possono essere resi accessibili tramite la rete di ExpressRoute, consentendo al personale DevOps locale di eseguire la configurazione o la manutenzione.

Se è necessario esporre gli endpoint di gestione per le macchine virtuali a una rete esterna, usare gruppi di sicurezza di rete o elenchi di controllo di accesso per limitare la visibilità di queste porte a un elenco di indirizzi IP o reti consentite.

> [!NOTE]
> Per impostazione predefinita, le macchine virtuali di Azure distribuite tramite il portale di Azure includono un indirizzo IP pubblico che offre un account di accesso.  
> 
> 


## <a name="deploy-the-solution"></a>Distribuire la soluzione

**Prerequisiti.** È necessario disporre di un'infrastruttura locale esistente già configurata con un'appliance di rete adatta.

Per distribuire la soluzione, seguire questa procedura.

1. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Attendere che il collegamento si apra nel portale di Azure e quindi eseguire questi passaggi:
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-hybrid-er-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
3. Attendere il completamento della distribuzione.
4. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fexpressroute%2Fazuredeploy-expressRouteCircuit.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
5. Attendere che il collegamento si apra nel portale di Azure e quindi eseguire questi passaggi:
   * Selezionare **Usa esistente** nella sezione **Gruppo di risorse** e immettere `ra-hybrid-er-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
6. Attendere il completamento della distribuzione.


<!-- links -->
[forced-tuneling]: ../dmz/secure-vnet-hybrid.md
[highly-available-network-architecture]: ./expressroute-vpn-failover.md

[expressroute-technical-overview]: /azure/expressroute/expressroute-introduction
[expressroute-prereqs]: /azure/expressroute/expressroute-prerequisites
[configure-expressroute-routing]: /azure/expressroute/expressroute-howto-routing-arm
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute
[link-vnet-to-expressroute]: /azure/expressroute/expressroute-howto-linkvnet-arm
[ExpressRoute-provisioning]: /azure/expressroute/expressroute-workflows
[expressroute-introduction]: /azure/expressroute/expressroute-introduction
[expressroute-peering]: /azure/expressroute/expressroute-circuit-peerings
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: /azure/azure-subscription-service-limits#networking-limits
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/hybrid-networking/expressroute/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./images/expressroute.png "Architettura di rete ibrida tramite Azure ExpressRoute"
[1]: ../_images/guidance-hybrid-network-expressroute/figure2.png "Uso di router ridondanti con circuiti di ExpressRoute primari e secondari"
[2]: ../_images/guidance-hybrid-network-expressroute/figure3.png "Aggiunta di dispositivi di sicurezza alla rete locale"
[3]: ../_images/guidance-hybrid-network-expressroute/figure4.png "Uso del tunneling forzato per controllare il traffico associato a Internet"
[4]: ../_images/guidance-hybrid-network-expressroute/figure5.png "Individuazione della ServiceKey di un circuito ExpressRoute"  