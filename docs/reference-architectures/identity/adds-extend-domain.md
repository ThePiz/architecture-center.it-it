---
title: Estensione di Active Directory Domain Services in Azure
description: >-
  Come implementare un'architettura di rete ibrida sicura con autorizzazione di Active Directory in Azure.

  linee guida, gateway vpn, expressroute, bilanciamento del carico, rete virtuale, active directory
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Identity management
pnp.series.prev: azure-ad
pnp.series.next: adds-forest
ms.openlocfilehash: 216c59a0a5912d0fe90011e49ad20eb017ada6be
ms.sourcegitcommit: 8ab30776e0c4cdc16ca0dcc881960e3108ad3e94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="extend-active-directory-domain-services-ad-ds-to-azure"></a>Estendere Active Directory Domain Services in Azure

Questa architettura di riferimento mostra come estendere l'ambiente Active Directory in Azure per fornire servizi di autenticazione distribuiti usando [Active Directory Domain Services][active-directory-domain-services].  [**Distribuire questa soluzione**.](#deploy-the-solution)

[![0]][0] 

*Scaricare un [file Visio][visio-download] di questa architettura.*

Active Directory Domain Services viene usato per autenticare utenti, computer, applicazioni o altre identità incluse in un dominio di sicurezza. Può essere ospitato in locale, ma se l'applicazione è ospitata in parte in locale e in parte in Azure, potrebbe essere più efficiente replicare questa funzionalità in Azure. Questo consente di ridurre la latenza dovuta all'invio di richieste di autenticazione e di autorizzazione locali dal cloud di nuovo all'istanza di Active Directory Domain Services in esecuzione in locale. 

Questa architettura viene comunemente usata quando la rete locale e la rete virtuale di Azure sono connesse tramite una connessione VPN o ExpressRoute. L'architettura supporta anche la replica bidirezionale. Ciò vuol dire che è possibile apportare modifiche sia in locale che nel cloud ed entrambe le origini verranno mantenute coerenti. Gli usi tipici di questa architettura includono applicazioni ibride in cui la funzionalità viene distribuita tra origini locali e Azure, oltre che applicazioni e servizi che eseguono l'autenticazione tramite Active Directory.

Per altre considerazioni, vedere l'articolo su come [scegliere una soluzione per l'integrazione di Active Directory locale con Azure][considerations]. 

## <a name="architecture"></a>Architecture 

Questa architettura estende l'architettura illustrata in [Rete perimetrale tra Azure e Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Include i componenti seguenti.

* **Rete locale**. La rete locale include i server Active Directory locali che possono gestire l'autenticazione e l'autorizzazione per i componenti situati in locale.
* **Server Active Directory**. Sono i controller di dominio che implementano i servizi directory (Active Directory Domain Services) in esecuzione come macchine virtuali nel cloud. Questi server possono fornire l'autenticazione dei componenti in esecuzione nella rete virtuale di Azure.
* **Subnet di Active Directory**. I server di Active Directory Domain Services sono ospitati in una subnet separata. Le regole del gruppo di sicurezza di rete (NSG) proteggono i server di Active Directory Domain Services e fungono da firewall per il traffico da origini non previste.
* **Gateway di Azure e sincronizzazione di Active Directory**. Il gateway di Azure fornisce una connessione tra la rete virtuale di Azure e la rete locale. Può trattarsi di una [connessione VPN][azure-vpn-gateway] o [Azure ExpressRoute][azure-expressroute]. Tutte le richieste di sincronizzazione tra i server Active Directory nel cloud e locali passano attraverso il gateway. Le route definite dall'utente gestiscono il routing per il traffico locale che passa in Azure. Il traffico da e verso i server Active Directory non passa attraverso le appliance virtuali di rete (NVA) usate in questo scenario.

Per altre informazioni sulla configurazione delle route definite dall'utente e delle appliance virtuali di rete, vedere [Implementazione di un'architettura di rete ibrida sicura in Azure][implementing-a-secure-hybrid-network-architecture]. 

## <a name="recommendations"></a>Raccomandazioni

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda. 

### <a name="vm-recommendations"></a>Indicazioni per le VM

Determinare i requisiti di [dimensioni delle macchine virtuali][vm-windows-sizes] in base al volume di richieste di autenticazione previsto. Usare le specifiche dei computer che ospitano Active Directory Domain Services in locale come punto di partenza e associarle alle dimensioni delle macchine virtuali di Azure. Dopo la distribuzione, monitorare l'utilizzo e aumentare o ridurre le prestazioni in base al carico effettivo sulle macchine virtuali. Per altre informazioni sul dimensionamento dei controller di dominio Active Directory Domain Services, vedere [Capacity Planning for Active Directory Domain Services][capacity-planning-for-adds] (Pianificazione della capacità per Active Directory Domain Services).

Creare un disco dati virtuale separato per archiviare il database, i log e SYSVOL per Active Directory. Non archiviare questi elementi nello stesso disco del sistema operativo. Si noti che, per impostazione predefinita, le unità disco dati collegate a una macchina virtuale usano la funzionalità cache write-through. Tuttavia, questa forma di memorizzazione nella cache può essere in conflitto con i requisiti di Active Directory Domain Services. Per questo motivo, impostare l'opzione *Preferenze cache dell'host* del disco dati su *Nessuna*. Per altre informazioni, vedere [Posizione del database di Active Directory Domain Services di Windows Server e di SYSVOL][adds-data-disks].

Distribuire almeno due macchine virtuali che eseguono Active Directory Domain Services come controller di dominio e aggiungerle a un [set di disponibilità][availability-set].

### <a name="networking-recommendations"></a>Raccomandazioni di rete

Configurare l'interfaccia di rete della macchina virtuale (NIC) per ogni server di Active Directory Domain Services con un indirizzo IP privato statico per il supporto completo del servizio DNS (Domain Name Service). Per altre informazioni, vedere [Come impostare un indirizzo IP statico privato nel portale di Azure][set-a-static-ip-address].

> [!NOTE]
> Non configurare la scheda di interfaccia di rete per Active Directory Domain Services con un indirizzo IP pubblico. Vedere le [considerazioni relative alla sicurezza][security-considerations] per altri dettagli.
> 
> 

Il gruppo di sicurezza di rete della subnet di Active Directory richiede regole per consentire il traffico in ingresso da origini locali. Per informazioni dettagliate sulle porte usate da Active Directory Domain Services, vedere [Active Directory and Active Directory Domain Services Port Requirements][ad-ds-ports] (Requisiti delle porte per Active Directory e Active Directory Domain Services). Verificare inoltre che le tabelle di route definite dall'utente non indirizzino il traffico di Active Directory Domain Services attraverso le appliance virtuali di rete usate in questa architettura. 

### <a name="active-directory-site"></a>Sito Active Directory

In Active Directory Domain Services, un sito rappresenta un percorso fisico, una rete o una raccolta di dispositivi. I siti Active Directory Domain Services vengono usati per gestire la replica dei database di Active Directory Domain Services mediante il raggruppamento degli oggetti di Active Directory Domain Services vicini tra loro e connessi tramite una rete ad alta velocità. Active Directory Domain Services include logica per selezionare la strategia ottimale per la replica del database di Active Directory Domain Services tra siti.

È consigliabile creare un sito Active Directory Domain Services che includa le subnet definite per l'applicazione in Azure. Quindi, configurare un collegamento di sito tra i siti Active Directory Domain Services locali e Active Directory Domain Services eseguirà automaticamente la replica di database più efficiente possibile. Si noti che questa replica del database richiede poco altro oltre la configurazione iniziale.

### <a name="active-directory-operations-masters"></a>Master operazioni di Active Directory

È possibile assegnare il ruolo di master operazioni ai controller di dominio Active Directory Domain Services per supportare la coerenza tra le istanze di database di Active Directory Domain Services replicati. Esistono cinque ruoli di master operazioni: master schema, master per la denominazione dei domini, master RID, master emulatore controller di dominio primario e master infrastrutture. Per altre informazioni su questi ruoli, vedere [What are Operations Masters?][ad-ds-operations-masters] (Cosa sono i master operazioni?).

È consigliabile non assegnare ruoli di master operazioni ai controller di dominio distribuiti in Azure.

### <a name="monitoring"></a>Monitoraggio

Monitorare le risorse delle macchine virtuali controller di dominio, nonché i servizi di Active Directory Domain Services e creare un piano per risolvere rapidamente eventuali problemi. Per altre informazioni, vedere [Monitoring Active Directory][monitoring_ad] (Monitoraggio di Active Directory). È anche possibile installare strumenti come [Microsoft Systems Center][microsoft_systems_center] nel server di monitoraggio (vedere il diagramma dell'architettura) per agevolare l'esecuzione di queste attività.  

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Active Directory Domain Services è progettato per la scalabilità. Non è necessario configurare un servizio di bilanciamento del carico o un controller del traffico per indirizzare le richieste ai controller di dominio Active Directory Domain Services. L'unica considerazione rispetto alla scalabilità riguarda la necessità di configurare le macchine virtuali che eseguono Active Directory Domain Services con le dimensioni corrette per i requisiti di carico della rete, monitorare il carico sulle macchine virtuali e aumentare o ridurre le prestazioni in base alle esigenze.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Distribuire le macchine virtuali che eseguono Active Directory Domain Services in un [set di disponibilità][availability-set]. Inoltre, valutare l'assegnazione del ruolo di [master operazioni in standby][standby-operations-masters] ad almeno un server ed eventualmente di più, a seconda dei requisiti. Un master operazioni in standby è una copia attiva del master operazioni che può essere usata al posto dei master operazioni principali durante il failover.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Eseguire backup regolari di Active Directory Domain Services. Non limitarsi a copiare i file VHD dei controller di dominio anziché eseguire backup regolari, perché il file di database di Active Directory Domain Services nel disco rigido virtuale potrebbe non essere in stato coerente quando viene copiato, rendendo impossibile riavviare il database.

Non arrestare una macchina virtuale del controller di dominio tramite il portale di Azure. Al contrario, arrestare e riavviare dal sistema operativo guest. L'arresto tramite il portale determina la deallocazione della VM e di conseguenza la reimpostazione di entrambi i valori `VM-GenerationID` e `invocationID` del repository Active Directory. Questo rimuove il pool di identificatori relativi (RID) di Active Directory Domain Services, contrassegna SYSVOL come non autorevole e può richiedere la riconfigurazione del controller di dominio.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

I server di Active Directory Domain Services offrono servizi di autenticazione e rappresentano un obiettivo ideale per eventuali attacchi. Per proteggere gli oggetti, impedire la connettività Internet diretta inserendo i server di Active Directory Domain Services in una subnet separata con un gruppo di sicurezza di rete che svolga la funzione di firewall. Chiudere tutte le porte nei server di Active Directory Domain Services ad eccezione di quelle necessarie per l'autenticazione, l'autorizzazione e la sincronizzazione dei server. Per altre informazioni, vedere [Active Directory and Active Directory Domain Services Port Requirements][ad-ds-ports] (Requisiti delle porte per Active Directory e Active Directory Domain Services).

Considerare la possibilità di implementare un perimetro di sicurezza aggiuntivo attorno ai server con una coppia di subnet e appliance virtuali di rete, come descritto nell'articolo su come [implementare un'architettura di rete ibrida sicura con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

Usare BitLocker o Crittografia dischi di Azure per crittografare il disco che ospita il database di Active Directory Domain Services.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una soluzione per la distribuzione di questa architettura di riferimento è disponibile in [GitHub][github]. Per eseguire lo script di PowerShell che distribuisce la soluzione, è necessaria l'ultima versione dell'[interfaccia della riga di comando di Azure][azure-powershell]. Per distribuire l'architettura di riferimento, eseguire la procedura seguente:

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
    * `AzureADDS`: distribuisce le macchine virtuali che agiscono come server di Active Directory Domain Services, distribuisce Active Directory in queste macchine virtuali e distribuisce il dominio in Azure.
    * `Workload`: distribuisce le reti perimetrali pubbliche e private e il livello del carico di lavoro.
    * `All`: distribuisce tutte le distribuzioni precedenti. **Questa è l'opzione consigliata se non si ha una rete locale esistente, ma si vuole distribuire l'architettura di riferimento completa descritta in precedenza a scopo di testing o valutazione.**

4. Attendere il completamento della distribuzione. Se si sta distribuendo la distribuzione `All`, saranno necessarie diverse ore.

## <a name="next-steps"></a>Passaggi successivi

* Procedure consigliate per [creare una foresta di risorse di Active Directory Domain Services][adds-resource-forest] in Azure.
* Procedure consigliate per [creare un'infrastruttura Active Directory Federation Services][adfs] in Azure.

<!-- links -->
[adds-resource-forest]: adds-forest.md
[adfs]: adfs.md

[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md

[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[ad-ds-operations-masters]: https://technet.microsoft.com/library/cc779716(v=ws.10).aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[considerations]: ./considerations.md
[GitHub]: https://github.com/mspnp/reference-architectures/tree/master/identity/adds-extend-domain
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[security-considerations]: #security-considerations
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[visio-download]: https://archcenter.azureedge.net/cdn/identity-architectures.vsdx
[vm-windows-sizes]: /azure/virtual-machines/virtual-machines-windows-sizes

[0]: ./images/adds-extend-domain.png "Architettura di rete ibrida sicura con Active Directory"
