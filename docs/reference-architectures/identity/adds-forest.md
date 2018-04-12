---
title: Creare una foresta di risorse di Active Directory Domain Services in Azure
description: >-
  Come creare un dominio trusted di Active Directory in Azure.

  linee guida, gateway vpn, expressroute, bilanciamento del carico, rete virtuale, active directory
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Identity management
pnp.series.prev: adds-extend-domain
pnp.series.next: adfs
cardTitle: Create an AD DS forest in Azure
ms.openlocfilehash: e32a6420821e70c84e77d2c39614f0c45efbb7e2
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
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

Una soluzione per la distribuzione di questa architettura di riferimento è disponibile in [GitHub][github]. Per eseguire lo script di PowerShell che distribuisce la soluzione è necessaria l'ultima versione dell'interfaccia della riga di comando di Azure. Per distribuire l'architettura di riferimento, eseguire la procedura seguente:

1. Scaricare o clonare la cartella della soluzione da [GitHub][github] al computer locale.

2. Aprire l'interfaccia della riga di comando di Azure e passare alla cartella della soluzione locale.

3. Eseguire il comando seguente:
   
    ```Powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```
   
    Sostituire `<subscription id>` con l'ID della sottoscrizione di Azure.
   
    Per `<location>`, specificare un'area di Azure, come `eastus` o `westus`.
   
    Il parametro `<mode>` controlla la granularità della distribuzione e può essere uno dei valori seguenti:
   
   * `Onpremise`: distribuisce l'ambiente locale simulato.
   * `Infrastructure`: distribuisce l'infrastruttura di rete virtuale e il jumpbox in Azure.
   * `CreateVpn`: distribuisce il gateway di rete virtuale di Azure e lo connette alla rete locale simulata.
   * `AzureADDS`: distribuisce le macchine virtuali che agiscono come server Active Directory Domain Services, distribuisce Active Directory in queste macchine virtuali e distribuisce il dominio in Azure.
   * `WebTier`: distribuisce le macchine virtuali e il software di bilanciamento del carico di livello Web.
   * `Prepare`: distribuisce tutte le distribuzioni precedenti. **Questa è l'opzione consigliata se non si ha una rete locale esistente, ma si vuole distribuire l'architettura di riferimento completa descritta in precedenza a scopo di testing o valutazione.** 
   * `Workload`: distribuisce le macchine virtuali e il software di bilanciamento del carico di livello Web e business. Si noti che queste macchine virtuali non sono incluse nella distribuzione `Prepare`.

4. Attendere il completamento della distribuzione. Se si sta distribuendo la distribuzione `Prepare`, saranno necessarie diverse ore.
     
5. Se si usa la configurazione locale simulata, configurare la relazione di trust in ingresso:
   
   1. Connettersi al jumpbox (<em>ra-adtrust-mgmt-vm1</em> nel gruppo di risorse <em>ra-adtrust-security-rg</em>). Accedere come <em>testuser</em> con la password <em>AweS0me@PW</em>.
   2. Nel jumpbox aprire una sessione RDP nella prima macchina virtuale nel dominio <em>contoso.com</em> (dominio locale). L'indirizzo IP di questa macchina virtuale è 192.168.0.4. Il nome utente è <em>contoso\testuser</em> con password <em>AweS0me@PW</em>.
   3. Scaricare lo script [incoming-trust.ps1][incoming-trust] ed eseguirlo per creare il trust in ingresso dal dominio *treyresearch.com*.

6. Se si usa la propria infrastruttura locale:
   
   1. Scaricare lo script [incoming-trust.ps1][incoming-trust].
   2. Modificare lo script e sostituire il valore della variabile `$TrustedDomainName` con il nome del proprio dominio.
   3. Eseguire lo script.

7. Dal jumpbox connettersi alla prima macchina virtuale nel dominio <em>treyresearch.com</em> (il dominio nel cloud). L'indirizzo IP di questa macchina virtuale è 10.0.4.4. Il nome utente è <em>treyresearch\testuser</em> con password <em>AweS0me@PW</em>.

8. Scaricare lo script [outgoing-trust.ps1][outgoing-trust] ed eseguirlo per creare il trust in ingresso dal dominio *treyresearch.com*. Se si usano i propri computer locali, prima di tutto modificare lo script. Impostare la variabile `$TrustedDomainName` sul nome del dominio locale e specificare gli indirizzi IP dei server di Active Directory Domain Services per questo dominio nella variabile `$TrustedDomainDnsIpAddresses`.

9. Attendere qualche minuto per il completamento dei passaggi precedenti, quindi connettersi a una macchina virtuale locale ed eseguire la procedura descritta nell'articolo [Verificare un trust][verify-a-trust] per determinare se la relazione di trust tra i domini *contoso.com* e *treyresearch.com* è configurata correttamente.

## <a name="next-steps"></a>Passaggi successivi

* Procedure consigliate per [estendere il dominio di Active Directory Domain Services in Azure][adds-extend-domain]
* Procedure consigliate per [creare un'infrastruttura Active Directory Federation Services][adfs] in Azure.

<!-- links -->
[adds-extend-domain]: adds-extend-domain.md
[adfs]: adfs.md

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