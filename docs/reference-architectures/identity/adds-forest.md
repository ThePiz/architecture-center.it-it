---
title: Creare una foresta di risorse di Active Directory Domain Services in Azure
description: >-
  Come creare un dominio trusted di Active Directory in Azure.

  linee guida, gateway vpn, expressroute, bilanciamento del carico, rete virtuale, active directory
author: telmosampaio
ms.date: 05/02/2018
pnp.series.title: Identity management
pnp.series.prev: adds-extend-domain
pnp.series.next: adfs
cardTitle: Create an AD DS forest in Azure
ms.openlocfilehash: 047ecea41ba30ce4cccf17b8c4964a37ae60150f
ms.sourcegitcommit: 0de300b6570e9990e5c25efc060946cb9d079954
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="create-an-active-directory-domain-services-ad-ds-resource-forest-in-azure"></a>Creare una foresta di risorse di Active Directory Domain Services in Azure

Questa architettura di riferimento illustra come creare un dominio di Active Directory separato in Azure considerato attendibile dai domini nella foresta di Active Directory locale. [**Distribuire questa soluzione**.](#deploy-the-solution)

[![0]][0] 

*Scaricare un [file Visio][visio-download] di questa architettura.*

Active Directory Domain Services archivia le informazioni di identità in una struttura gerarchica. Il nodo principale nella struttura gerarchica è noto come foresta. Una foresta contiene domini e i domini contengono altri tipi di oggetti. Questa architettura di riferimento crea una foresta di Active Directory Domain Services in Azure con una relazione di trust unidirezionale in uscita con un dominio locale. La foresta in Azure contiene un dominio che non esiste in locale. A causa della relazione di trust, gli accessi effettuati nei domini locali possono essere considerati attendibili per accedere alle risorse nel dominio di Azure separato. 

Gli usi tipici di questa architettura includono mantenere la separazione della sicurezza per le identità e gli oggetti contenuti nel cloud ed eseguire la migrazione di singoli domini da un ambiente locale al cloud. 

Per altre considerazioni, vedere l'articolo su come [scegliere una soluzione per l'integrazione di Active Directory locale con Azure][considerations]. 

## <a name="architecture"></a>Architecture

L'architettura include i componenti indicati di seguito.

* **Rete locale**. La rete locale contiene la propria foresta di Active Directory con i relativi domini.
* **Server Active Directory**. Sono i controller di dominio che implementano i servizi di dominio in esecuzione come macchine virtuali nel cloud. Questi server ospitano una foresta che contiene uno o più domini, separati da quelli locali.
* **Relazione di trust unidirezionale**. L'esempio nel diagramma mostra una relazione di trust unidirezionale dal dominio in Azure al dominio locale. Questa relazione consente agli utenti locali di accedere alle risorse nel dominio in Azure, ma non viceversa. È possibile creare un trust bidirezionale se gli utenti cloud richiedono l'accesso anche alle risorse locali.
* **Subnet di Active Directory**. I server di Active Directory Domain Services sono ospitati in una subnet separata. Le regole del gruppo di sicurezza di rete (NSG) proteggono i server di Active Directory Domain Services e fungono da firewall per il traffico da origini non previste.
* **Gateway di Azure**. Il gateway di Azure fornisce una connessione tra la rete virtuale di Azure e la rete locale. Può trattarsi di una [connessione VPN][azure-vpn-gateway] o [Azure ExpressRoute][azure-expressroute]. Per altre informazioni, vedere [Implementazione di un'architettura di rete ibrida sicura in Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Raccomandazioni

Per consigli specifici sull'implementazione di Active Directory in Azure, vedere gli articoli seguenti:

- [Estensione di Active Directory Domain Services in Azure][adds-extend-domain]. 
- [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure][ad-azure-guidelines].

### <a name="trust"></a>Trust

I domini locali sono contenuti in una foresta diversa rispetto ai domini nel cloud. Per abilitare l'autenticazione degli utenti locali nel cloud, i domini in Azure devono considerare attendibile il dominio di accesso nella foresta locale. Analogamente, se il cloud offre un dominio di accesso per gli utenti esterni, può essere necessario che la foresta locale consideri attendibile il dominio cloud.

È possibile stabilire relazioni di trust a livello di foresta, [creando trust tra foreste][creating-forest-trusts], o a livello di dominio, [creando trust esterni][creating-external-trusts]. Un trust a livello di foresta crea una relazione tra tutti i domini in due foreste. Un trust a livello di dominio esterno crea solo una relazione tra i due domini specificati. È consigliabile creare trust a livello di dominio esterno solo tra domini in foreste diverse.

I trust possono essere unidirezionali o bidirezionali:

* Un trust unidirezionale consente agli utenti in un dominio o foresta (noti come dominio o foresta *in ingresso*) per accedere alle risorse contenute in un altro dominio o foresta (il dominio o foresta *in uscita*).
* Un trust bidirezionale consente agli utenti nel dominio o nella foresta di accedere alle risorse contenute nell'altro dominio o foresta.

La tabella seguente presenta un riepilogo delle configurazioni di trust per alcuni semplici scenari:

| Scenario | Trust locale | Trust cloud |
| --- | --- | --- |
| Gli utenti locali richiedono l'accesso alle risorse nel cloud, ma non viceversa |Unidirezionale in ingresso |Unidirezionale in uscita |
| Gli utenti nel cloud richiedono l'accesso alle risorse nel cloud, ma non viceversa |Unidirezionale in uscita |Unidirezionale in ingresso |
| Gli utenti locali e nel cloud richiedono entrambi l'accesso alle risorse contenute nel cloud e locali |Bidirezionale, in ingresso e in uscita |Bidirezionale, in ingresso e in uscita |

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Active Directory offre scalabilità automatica per i controller di dominio che fanno parte dello stesso dominio. Le richieste vengono distribuite tra tutti i controller all'interno di un dominio. È possibile aggiungere un altro controller di dominio, che viene sincronizzato automaticamente con il dominio. Non configurare un dispositivo di bilanciamento del carico separato per indirizzare il traffico verso i controller all'interno del dominio. Verificare che tutti i controller di dominio dispongano di risorse di memoria e archiviazione sufficienti per gestire il database del dominio. Impostare dimensioni uguali per tutte le macchine virtuali del controller di dominio.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Eseguire il provisioning di almeno due controller di dominio per ogni dominio. Questo consente la replica automatica tra server. Creare un set di disponibilità per le macchine virtuali che fungono da server Active Directory per la gestione di ogni dominio. In questo set di disponibilità, inserire almeno due server.

Inoltre, è opportuno designare uno o più server in ogni dominio come [master operazioni in standby][standby-operations-masters], in caso di errori di connettività con un server che riveste il ruolo FSMO (Flexible Single Master Operation).

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Per considerazioni sulla gestione e il monitoraggio, vedere [Estensione di Active Directory Domain Services in Azure][adds-extend-domain]. 
 
Per altre informazioni, vedere [Monitoring Active Directory][monitoring_ad] (Monitoraggio di Active Directory). È possibile installare strumenti come [Microsoft Systems Center][microsoft_systems_center] in un server di monitoraggio della subnet di gestione per agevolare l'esecuzione di queste attività.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

I trust a livello di foresta sono transitivi. Se si stabilisce un trust a livello di foresta tra una foresta locale e una foresta nel cloud, la relazione di trust viene estesa ai nuovi domini creati in entrambe le foreste. Se si usano i domini per implementare la separazione a scopo di sicurezza, è consigliabile creare trust solo a livello di dominio. I trust a livello di dominio sono non transitivi.

Per considerazioni sulla sicurezza specifiche di Active Directory, vedere la sezione corrispondente in [Estensione di Active Directory Domain Services in Azure][adds-extend-domain].

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura è disponibile in [GitHub][github]. Si noti che l'intera distribuzione può richiedere fino a due ore, incluso la creazione del gateway VPN e l'esecuzione degli script che consentono di configurare Active Directory Domain Services.

### <a name="prerequisites"></a>prerequisiti

1. Clonare, creare una copia tramite fork o scaricare il file ZIP per il repository GitHub delle [architetture di riferimento][github].

2. Installare l'[Interfaccia della riga di comando di Azure 2.0][azure-cli-2].

3. Installare il pacchetto npm dei [blocchi predefiniti di Azure][azbb].

4. Al prompt dei comandi, di Bash o di PowerShell accedere all'account Azure con il comando seguente.

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a>Distribuire il data center locale simulato

1. Passare alla cartella `identity/adds-forest` del repository GitHub.

2. Aprire il file `onprem.json` . Cercare istanze di `adminPassword` e `Password` e aggiungere i valori per le password.

3. Eseguire il comando seguente e attendere il completamento della distribuzione:

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p onprem.json --deploy
    ```

### <a name="deploy-the-azure-vnet"></a>Distribuire la rete virtuale di Azure

1. Aprire il file `azure.json` . Cercare istanze di `adminPassword` e `Password` e aggiungere i valori per le password.

2. Nello stesso file cercare le istanze di `sharedKey` e immettere le chiavi condivise per la connessione VPN. 

    ```bash
    "sharedKey": "",
    ```

3. Eseguire il comando seguente e attendere il completamento della distribuzione.

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p onoprem.json --deploy
    ```

   Eseguire la distribuzione nello stesso gruppo di risorse della rete virtuale locale.


### <a name="test-the-ad-trust-relation"></a>Testare la relazione di trust di Active Directory

1. Usare il portale di Azure per passare al gruppo di risorse creato.

2. Usare il portale di Azure per trovare la macchina virtuale di Azure denominata `ra-adt-mgmt-vm1`.

2. Fare clic su `Connect` per aprire una sessione di desktop remoto per la macchina virtuale. Il nome utente è `contoso\testuser` e la password è quella specificata nel file parametro `onprem.json`.

3. All'interno della sessione Desktop remoto, aprire un'altra sessione Desktop remoto per 192.168.0.4, ovvero l'indirizzo IP della macchina virtuale denominata `ra-adtrust-onpremise-ad-vm1`. Il nome utente è `contoso\testuser` e la password è quella specificata nel file parametro `azure.json`.

4. Dall'interno della sessione Desktop remoto per `ra-adtrust-onpremise-ad-vm1`, passare a **Server Manager** e fare clic su **Strumenti** > **Domini e trust di Active Directory**. 

5. Nel riquadro sinistro fare clic su contoso.com e selezionare **Proprietà**.

6. Scegliere la scheda **Trust**. Sarà visibile treyresearch.net elencato come trust in ingresso.

![](./images/ad-forest-trust.png)


## <a name="next-steps"></a>Passaggi successivi

* Procedure consigliate per [estendere il dominio di Active Directory Domain Services in Azure][adds-extend-domain]
* Procedure consigliate per [creare un'infrastruttura Active Directory Federation Services][adfs] in Azure.

<!-- links -->
[adds-extend-domain]: adds-extend-domain.md
[adfs]: adfs.md
[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks

[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md

[running-VMs-for-an-N-tier-architecture-on-Azure]: ../virtual-machines-windows/n-tier.md

[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[considerations]: ./considerations.md
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[github]: https://github.com/mspnp/reference-architectures/tree/master/identity/adds-forest
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/identity/adds-forest/extensions/incoming-trust.ps1
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/identity/adds-forest/Deploy-ReferenceArchitecture.ps1
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/identity/adds-forest/extensions/outgoing-trust.ps1
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx
[0]: ./images/adds-forest.png "Architettura di rete ibrida sicura con domini di Active Directory separati"