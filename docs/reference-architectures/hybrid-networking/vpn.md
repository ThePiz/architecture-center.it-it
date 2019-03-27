---
title: Connettere una rete locale ad Azure tramite VPN
titleSuffix: Azure Reference Architectures
description: Implementare un'architettura di rete sicura da sito a sito, che si estende su una rete virtuale di Azure e su una rete locale connessa tramite VPN.
author: RohitSharma-pnp
ms.date: 10/22/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: networking
ms.openlocfilehash: 515cd3f5d23957e0e153c9d25198b3cb98b92a5d
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245672"
---
# <a name="connect-an-on-premises-network-to-azure-using-a-vpn-gateway"></a>Connettere una rete locale ad Azure tramite un gateway VPN

Questa architettura di riferimento mostra come estendere una rete locale ad Azure tramite una rete privata virtuale (VPN) da sito a sito. Il traffico passa tra la rete locale e la rete virtuale di Azure tramite un tunnel per VPN IPSec. [**Distribuire questa soluzione**](#deploy-the-solution).

![Rete ibrida che si estende in locale e nelle infrastrutture di Azure](./images/vpn.png)

*Scaricare un [file Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

- **Rete locale**. Una rete LAN privata in esecuzione all'interno di un'organizzazione.

- **Appliance VPN**. Un dispositivo o un servizio che offre connettività esterna alla rete locale. L'appliance VPN può essere un dispositivo hardware o una soluzione software, ad esempio il servizio Routing e Accesso remoto (RRAS) in Windows Server 2012. Per un elenco di appliance VPN supportate e per informazioni su come configurarle per la connessione a un gateway VPN di Azure, vedere le istruzioni per il dispositivo selezionato nell'articolo [Informazioni sui dispositivi VPN per connessioni del Gateway VPN da sito a sito][vpn-appliance].

- **Rete virtuale**. L'applicazione cloud e i componenti del gateway VPN di Azure si trovano nella stessa [rete virtuale][azure-virtual-network].

- **Gateway VPN di Azure**. Il servizio [gateway VPN][azure-vpn-gateway] consente di connettere la rete virtuale alla rete locale tramite un'appliance VPN. Per maggiori informazioni, consultare [Connettere una rete locale a una rete virtuale di Microsoft Azure][connect-to-an-Azure-vnet]. Il gateway VPN include gli elementi seguenti:

  - **Gateway di rete virtuale**. Risorsa che fornisce un'appliance VPN virtuale per la rete virtuale ed è responsabile del routing del traffico dalla rete locale alla rete virtuale.
  - **Gateway di rete locale**. Astrazione dell'appliance VPN locale. Il routing del traffico di rete dall'applicazione cloud alla rete locale viene eseguito tramite il gateway.
  - **Connessione**. La connessione ha proprietà che specificano il tipo di connessione (IPSec) e la chiave condivisa con l'appliance VPN locale per crittografare il traffico.
  - **Subnet del gateway**: Il gateway di rete virtuale viene mantenuto nella propria subnet, che è soggetta a vari requisiti, descritti nella sezione Raccomandazioni di seguito.

- **Applicazione cloud**. Applicazione contenuta in Azure. Può includere più livelli, con più subnet connesse tramite i servizi di bilanciamento del carico di Azure. Per altre informazioni sull'infrastruttura dell'applicazione, vedere [Esecuzione di carichi di lavoro della macchina virtuale Windows][windows-vm-ra] ed [Esecuzione di carichi di lavoro della macchina virtuale di Linux][linux-vm-ra].

- **Servizio di bilanciamento del carico interno**. Il routing del traffico di rete dal gateway VPN viene eseguito per l'applicazione cloud tramite un servizio di bilanciamento del carico interno. Il servizio di bilanciamento del carico si trova nella subnet front-end dell'applicazione.

## <a name="recommendations"></a>Consigli

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.

### <a name="vnet-and-gateway-subnet"></a>Rete virtuale e subnet gateway

Creare una rete virtuale di Azure con uno spazio indirizzi sufficientemente grande per tutte le risorse necessarie. Verificare che lo spazio indirizzi di rete virtuale disponga di spazio sufficiente per la crescita se le macchine virtuali aggiuntive sono necessarie in futuro. Lo spazio indirizzi della rete virtuale non deve sovrapporsi con la rete locale. Il diagramma precedente usa ad esempio lo spazio indirizzi 10.20.0.0/16 per la rete virtuale.

Creare una subnet denominata *GatewaySubnet*, con un intervallo di indirizzi /27. Questa subnet è necessaria per il gateway di rete virtuale. Allocare 32 indirizzi a questa subnet contribuirà in futuro a evitare di raggiungere i limiti di dimensioni del gateway. Evitare inoltre di inserire questa subnet all'interno dello spazio indirizzi. Una procedura consigliata consiste nell'impostare lo spazio indirizzi per la subnet del gateway all'estremità superiore dello spazio indirizzi della rete virtuale. L'esempio illustrato nel diagramma usa 10.20.255.224/27.  Di seguito è riportata una procedura rapida per calcolare il [CIDR]:

1. Impostare i bit variabili nello spazio indirizzi della rete virtuale su 1, fino ai bit usati dalla subnet del gateway, quindi impostare i bit rimanenti su 0.
2. Convertire i bit risultanti in decimali ed esprimerli sotto forma di uno spazio indirizzi con la lunghezza del prefisso impostata sulle dimensioni della subnet del gateway.

Per una rete virtuale con un intervallo di indirizzi IP di 10.20.0.0/16, applicando il passaggio 1 precedente si ottiene 10.20.0b11111111.0b11100000.  Se si converte tale risultato in numero decimale e lo si esprime come uno spazio indirizzi, si ottiene 10.20.255.224/27.

> [!WARNING]
> Non distribuire le macchine virtuali nella subnet del gateway. Non assegnare inoltre un gruppo di sicurezza di rete a questa subnet, in quanto verrebbe causata l'interruzione del funzionamento del gateway.
>

### <a name="virtual-network-gateway"></a>Gateway di rete virtuale

Allocare un indirizzo IP pubblico per il gateway di rete virtuale.

Creare il gateway di rete virtuale nella subnet del gateway e assegnare a esso il nuovo indirizzo IP pubblico allocato. Usare il tipo di gateway che soddisfa maggiormente i propri requisiti e che sia abilitato dall'appliance VPN in uso:

- Creare un [gateway basato su criteri][policy-based-routing] se è necessario controllare da vicino il modo in cui viene eseguito il routing delle richieste in base ai criteri, ad esempio i prefissi di indirizzo. I gateway basati su criteri usano il routing statico e funzionano solo con le connessioni da sito a sito.

- Creare un [gateway basato su route][route-based-routing] se ci si connette alla rete locale mediante RRAS, sono supportate le connessioni multisito o tra più aree o sono implementate le connessioni da rete virtuale a rete virtuale (incluse le route che attraversano più reti virtuali). I gateway basati su route usano il routing dinamico per indirizzare il traffico tra le reti. Sono caratterizzati da una maggiore tolleranza degli errori nel percorso di rete rispetto alle route statiche poiché sono in grado di provare route alternative. I gateway basati su route possono anche ridurre il sovraccarico di gestione, poiché potrebbe non essere necessario aggiornare le route manualmente quando cambiano gli indirizzi di rete.

Per un elenco di appliance VPN supportate, vedere [Informazioni sui dispositivi VPN per connessioni del Gateway VPN da sito a sito][vpn-appliances].

> [!NOTE]
> Una volta creato il gateway, non è possibile passare da un tipo di gateway a un altro senza eliminare e ricreare il gateway.
>

Selezionare lo SKU del gateway VPN di Azure che soddisfa maggiormente i requisiti di velocità effettiva. Per altre informazioni, vedere [SKU del gateway][azure-gateway-skus]

> [!NOTE]
> Lo SKU di base non è compatibile con Azure ExpressRoute. È possibile [modificare lo SKU][changing-SKUs] dopo che il gateway è stato creato.
>

L'importo addebitato è basato sulla quantità di tempo durante il quale il gateway viene sottoposto a provisioning e risulta disponibile. Vedere [Prezzi di Gateway VPN][azure-gateway-charges].

Creare regole di routing per la subnet del gateway che indirizzano il traffico dell'applicazione in ingresso dal gateway al servizio di bilanciamento del carico interno, anziché consentire alle richieste di passare direttamente alle macchine virtuali dell'applicazione.

### <a name="on-premises-network-connection"></a>Connessione di rete locale

Creare un gateway di rete locale. Specificare l'indirizzo IP pubblico dell'appliance VPN locale e lo spazio indirizzi della rete locale. Si noti che l'appliance VPN locale deve avere un indirizzo IP pubblico a cui è possibile accedere tramite il gateway di rete locale in Gateway VPN di Azure. Il dispositivo VPN non può trovarsi dietro un dispositivo NAT (Network Address Translation).

Creare una connessione da sito a sito per il gateway di rete virtuale e il gateway di rete locale. Selezionare il tipo di connessione da sito a sito (IPSec) e specificare la chiave condivisa. La crittografia da sito a sito con il gateway VPN di Azure è basata sul protocollo IPSec, usando le chiavi precondivise per l'autenticazione. Quando si crea il gateway VPN di Azure, si specifica la chiave. È necessario configurare l'appliance VPN eseguita in locale con la stessa chiave. Altri meccanismi di autenticazione non sono attualmente supportati.

Verificare che l'infrastruttura di routing locale sia configurata per inoltrare al dispositivo VPN le richieste destinate agli indirizzi nella rete virtuale di Azure.

Aprire le porte necessarie all'applicazione cloud nella rete locale.

Eseguire il test della connessione per verificare che:

- L'appliance VPN locale esegua in modo corretto il routing del traffico all'applicazione cloud tramite il gateway VPN di Azure.
- La rete virtuale esegua in modo corretto il routing del traffico alla rete locale.
- Il traffico non consentito in entrambe le direzioni sia bloccato in modo corretto.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

È possibile ottenere la scalabilità verticale limitata passando dallo SKU del Gateway VPN standard o di base allo SKU VPN a prestazioni elevate.

Per le reti virtuali che prevedono un volume elevato di traffico VPN, è possibile considerare di distribuire diversi carichi di lavoro in reti virtuali separate di dimensioni più piccole e di configurare un gateway VPN per ognuno di essi.

È possibile eseguire il partizionamento orizzontale o verticale della rete virtuale. Per eseguire il partizionamento orizzontale, spostare alcune istanze della macchina virtuale di ciascun livello nelle subnet della nuova rete virtuale. Ogni rete virtuale avrà a questo punto la stessa struttura e la stessa funzionalità. Per eseguire il partizionamento verticale, riprogettare ogni livello in modo da dividere la funzionalità in diverse aree logiche, ad esempio la gestione degli ordini, la fatturazione, la gestione degli account cliente e così via. Ogni area funzionale può quindi essere inserita nella propria rete virtuale.

La replica di un controller di dominio Active Directory locale nella rete virtuale e l'implementazione di DNS nella rete virtuale possono aiutare a limitare il flusso di traffico amministrativo e correlato alla sicurezza locale verso il cloud. Per altre informazioni, vedere [Estensione di Active Directory Domain Services in Azure][adds-extend-domain].

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Se è necessario assicurarsi che la rete locale rimanga disponibile per il gateway VPN di Azure, implementare un cluster di failover per il gateway VPN locale.

Se l'organizzazione dispone di più siti locali, creare [connessioni mulsito][vpn-gateway-multi-site] per una o più reti virtuali di Azure. Questo approccio richiede il routing dinamico (basato su route), assicurarsi quindi che il gateway VPN locale supporti questa funzionalità.

Per dettagli sui contratti di servizio, vedere [Contratto di Servizio per Gateway VPN][sla-for-vpn-gateway].

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Monitorare le informazioni di diagnostica da appliance VPN locali. Questo processo dipende dalle funzionalità fornite dall'appliance VPN. Se si usa ad esempio il servizio Routing e Accesso remoto in Windows Server 2012, servirsi della [registrazione RRAS][rras-logging].

Usare la [diagnostica del gateway VPN di Azure][gateway-diagnostic-logs] per acquisire informazioni sui problemi di connettività. È possibile usare questi log per tenere traccia delle informazioni, ad esempio l'origine e le destinazioni delle richieste di connessione, il protocollo usato e la modalità con cui è stata stabilita la connessione (o il motivo per cui il tentativo non è riuscito).

Monitorare i log operativi del gateway VPN di Azure usando i log di controllo disponibili nel portale di Azure. Sono disponibili log distinti per il gateway di rete locale, il gateway di rete di Azure e la connessione. Queste informazioni possono essere usate per tenere traccia di tutte le modifiche apportate al gateway e possono essere utili se, per qualche motivo, si verificano problemi su un gateway precedentemente funzionante.

![Log di controllo nel portale di Azure](../_images/guidance-hybrid-network-vpn/audit-logs.png)

Monitorare la connettività e tenere traccia degli eventi di errore di connettività. È possibile usare un pacchetto di monitoraggio, ad esempio [Nagios][nagios], per acquisire queste informazioni e creare appositi report.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Generare una chiave condivisa diversa per ogni gateway VPN. Usare una chiave condivisa complessa per resistere agli attacchi di forza bruta.

> [!NOTE]
> Non è attualmente possibile usare Azure Key Vault per precondividere le chiavi per il gateway VPN di Azure.
>

Verificare che il dispositivo VPN locale usi un metodo di crittografia [compatibile con il gateway VPN di Azure][vpn-appliance-ipsec]. Per il routing basato su criteri, il gateway VPN di Azure supporta gli algoritmi di crittografia AES256, AES128 e 3DES. I gateway basati su route supportano AES256 e 3DES.

Se l'appliance VPN locale si trova su una rete perimetrale con un firewall tra la rete perimetrale e Internet, potrebbe essere necessario configurare [regole del firewall aggiuntive][additional-firewall-rules] per consentire la connessione VPN da sito a sito.

Se l'applicazione nella rete virtuale invia dati a Internet, è possibile considerare l'[implementazione del tunneling forzato][forced-tunneling] per eseguire il routing di tutto il traffico associato a Internet attraverso la rete locale. Questo approccio consente di controllare le richieste in uscita eseguite tramite l'applicazione dall'infrastruttura locale.

> [!NOTE]
> Il tunneling forzato può influire sulla connettività ai servizi di Azure, ad esempio Servizio di archiviazione, e sulla gestione delle licenze di Windows.
>

## <a name="deploy-the-solution"></a>Distribuire la soluzione

**Prerequisiti**. È necessario disporre di un'infrastruttura locale esistente già configurata con un'appliance di rete adatta.

Per distribuire la soluzione, seguire questa procedura.

<!-- markdownlint-disable MD033 -->

1. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fvpn%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Attendere che il collegamento si apra nel portale di Azure e quindi eseguire questi passaggi:
   - Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-hybrid-vpn-rg` nella casella di testo.
   - Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   - Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   - Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   - Fare clic sul pulsante **Acquista**.
3. Attendere il completamento della distribuzione.

<!-- markdownlint-enable MD033 -->

Per risolvere i problemi di connessione, consultare [Risolvere i problemi relativi a una connessione VPN ibrida](./troubleshoot-vpn.md).

<!-- links -->

[adds-extend-domain]: ../identity/adds-extend-domain.md
[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md

[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-virtual-network]: /azure/virtual-network/virtual-networks-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-gateway-skus]: /azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: /azure/vpn-gateway/vpn-gateway-multi-site
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: https://blogs.technet.microsoft.com/keithmayer/2016/10/12/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[forced-tunneling]: /azure/vpn-gateway/vpn-gateway-about-forced-tunneling
[vpn-appliances]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[vpn-appliance-ipsec]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec-parameters
[azure-cli]: /cli/azure/install-azure-cli
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
