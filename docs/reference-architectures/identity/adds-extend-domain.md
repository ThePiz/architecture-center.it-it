---
title: Estendere il dominio Active Directory locale ad Azure
titleSuffix: Azure Reference Architectures
description: Distribuire Active Directory Domain Services (AD DS) in una rete virtuale di Azure.
author: telmosampaio
ms.date: 05/02/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, identity
ms.openlocfilehash: c617a0ceba900fc9cd78eff21aadf5c94f6b143b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640346"
---
# <a name="extend-your-on-premises-active-directory-domain-to-azure"></a>Estendere il dominio Active Directory locale ad Azure

Questa architettura Mostra come estendere un dominio di Active Directory locale ad Azure per offrire servizi di autenticazione distribuiti. [**Distribuire questa soluzione**](#deploy-the-solution).

![Architettura di rete ibrida protetta con Active Directory Domain Services](./images/adds-extend-domain.png)

*Scaricare un [file Visio][visio-download] di questa architettura.*

Se l'applicazione è ospitata in parte in locale e in parte in Azure, potrebbe essere più efficiente per la replica di Active Directory Domain Services (AD DS) in Azure. Ciò può ridurre la latenza dovuta all'invio delle richieste di autenticazione dal cloud al dominio di Active Directory in esecuzione in locale.

Questa architettura viene comunemente usata quando la rete locale e la rete virtuale di Azure sono connesse tramite una connessione VPN o ExpressRoute. L'architettura supporta anche la replica bidirezionale. Ciò vuol dire che è possibile apportare modifiche sia in locale che nel cloud ed entrambe le origini verranno mantenute coerenti. Gli usi tipici di questa architettura includono applicazioni ibride in cui la funzionalità viene distribuita tra origini locali e Azure, oltre che applicazioni e servizi che eseguono l'autenticazione tramite Active Directory.

Per altre considerazioni, vedere l'articolo su come [scegliere una soluzione per l'integrazione di Active Directory locale con Azure][considerations].

## <a name="architecture"></a>Architettura

Questa architettura estende l'architettura illustrata in [Rete perimetrale tra Azure e Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Include i componenti seguenti.

- **Rete locale**. La rete locale include i server Active Directory locali che possono gestire l'autenticazione e l'autorizzazione per i componenti situati in locale.
- **Server Active Directory**. Sono i controller di dominio che implementano i servizi directory (Active Directory Domain Services) in esecuzione come macchine virtuali nel cloud. Questi server possono fornire l'autenticazione dei componenti in esecuzione nella rete virtuale di Azure.
- **Subnet di Active Directory**. I server di Active Directory Domain Services sono ospitati in una subnet separata. Le regole del gruppo di sicurezza di rete (NSG) proteggono i server di Active Directory Domain Services e fungono da firewall per il traffico da origini non previste.
- **Gateway di Azure e sincronizzazione di Active Directory**. Il gateway di Azure fornisce una connessione tra la rete virtuale di Azure e la rete locale. Può trattarsi di una [connessione VPN][azure-vpn-gateway] o [Azure ExpressRoute][azure-expressroute]. Tutte le richieste di sincronizzazione tra i server Active Directory nel cloud e locali passano attraverso il gateway. Le route definite dall'utente gestiscono il routing per il traffico locale che passa in Azure. Il traffico da e verso i server Active Directory non passa attraverso le appliance virtuali di rete (NVA) usate in questo scenario.

Per altre informazioni sulla configurazione delle route definite dall'utente e delle appliance virtuali di rete, vedere [Implementazione di un'architettura di rete ibrida sicura in Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Consigli

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda.

### <a name="vm-recommendations"></a>Indicazioni per le VM

Determinare i requisiti di [dimensioni delle macchine virtuali][vm-windows-sizes] in base al volume di richieste di autenticazione previsto. Usare le specifiche dei computer che ospitano Active Directory Domain Services in locale come punto di partenza e associarle alle dimensioni delle macchine virtuali di Azure. Dopo la distribuzione, monitorare l'utilizzo e aumentare o ridurre le prestazioni in base al carico effettivo sulle macchine virtuali. Per altre informazioni sul dimensionamento dei controller di dominio Active Directory Domain Services, vedere [Capacity Planning for Active Directory Domain Services][capacity-planning-for-adds] (Pianificazione della capacità per Active Directory Domain Services).

Creare un disco dati virtuale separato per archiviare il database, i log e SYSVOL per Active Directory. Non archiviare questi elementi nello stesso disco del sistema operativo. Si noti che, per impostazione predefinita, le unità disco dati collegate a una macchina virtuale usano la funzionalità cache write-through. Tuttavia, questa forma di memorizzazione nella cache può essere in conflitto con i requisiti di Active Directory Domain Services. Per questo motivo, impostare l'opzione *Preferenze cache dell'host* del disco dati su *Nessuna*.

Distribuire almeno due macchine virtuali che eseguono Active Directory Domain Services come controller di dominio e aggiungerle a un [set di disponibilità][availability-set].

### <a name="networking-recommendations"></a>Raccomandazioni di rete

Configurare l'interfaccia di rete della macchina virtuale (NIC) per ogni server di Active Directory Domain Services con un indirizzo IP privato statico per il supporto completo del servizio DNS (Domain Name Service). Per altre informazioni, vedere [Come impostare un indirizzo IP statico privato nel portale di Azure][set-a-static-ip-address].

> [!NOTE]
> Non configurare la scheda di interfaccia di rete per Active Directory Domain Services con un indirizzo IP pubblico. Vedere le [considerazioni relative alla sicurezza][security-considerations] per altri dettagli.
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

Una distribuzione di questa architettura è disponibile in [GitHub][github]. Si noti che l'intera distribuzione può richiedere fino a due ore, incluso la creazione del gateway VPN e l'esecuzione degli script che consentono di configurare Active Directory Domain Services.

### <a name="prerequisites"></a>Prerequisiti

1. Clonare, creare una copia tramite fork o scaricare il file ZIP per il [repository GitHub](https://github.com/mspnp/identity-reference-architectures).

2. Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Installare il pacchetto npm dei [blocchi predefiniti di Azure](https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks).

   ```bash
   npm install -g @mspnp/azure-building-blocks
   ```

4. Al prompt dei comandi, di Bash o di PowerShell accedere all'account Azure come illustrato di seguito:

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a>Distribuire il data center locale simulato

1. Passare alla cartella `identity/adds-extend-domain` del repository GitHub.

2. Aprire il file `onprem.json` . Cercare istanze di `adminPassword` e `Password` e aggiungere i valori per le password.

3. Eseguire il comando seguente e attendere il completamento della distribuzione:

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p onprem.json --deploy
    ```

### <a name="deploy-the-azure-vnet"></a>Distribuire la rete virtuale di Azure

1. Aprire il file `azure.json` .  Cercare istanze di `adminPassword` e `Password` e aggiungere i valori per le password.

2. Nello stesso file cercare le istanze di `sharedKey` e immettere le chiavi condivise per la connessione VPN.

    ```json
    "sharedKey": "",
    ```

3. Eseguire il comando seguente e attendere il completamento della distribuzione.

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p azure.json --deploy
    ```

   Eseguire la distribuzione nello stesso gruppo di risorse della rete virtuale locale.

### <a name="test-connectivity-with-the-azure-vnet"></a>Testare la connettività con la rete virtuale di Azure

Dopo aver completato la distribuzione, è possibile testare la connettività dall'ambiente locale simulato nella rete virtuale di Azure.

1. Usare il portale di Azure per passare al gruppo di risorse creato.

2. Trovare la macchina virtuale denominata `ra-onpremise-mgmt-vm1`.

3. Fare clic su `Connect` per aprire una sessione di desktop remoto per la macchina virtuale. Il nome utente è `contoso\testuser` e la password è quella specificata nel file parametro `onprem.json`.

4. All'interno della sessione di desktop remoto, aprire un'altra sessione di desktop remoto per 10.0.4.4, ovvero l'indirizzo IP della macchina virtuale denominata `adds-vm1`. Il nome utente è `contoso\testuser` e la password è quella specificata nel file parametro `azure.json`.

5. Dalla sessione di desktop remoto per `adds-vm1`, passare a **Server Manager** e fare clic su **Aggiungi altri server da gestire**.

6. Nella scheda **Active Directory**, fare clic su **Find now** (Trova ora). Verrà visualizzato un elenco di Active Directory, Active Directory Domain Services e macchine virtuali Web.

   ![Screenshot della finestra di dialogo Aggiungi server](./images/add-servers-dialog.png)

## <a name="next-steps"></a>Passaggi successivi

- Procedure consigliate per [creare una foresta di risorse di Active Directory Domain Services][adds-resource-forest] in Azure.
- Procedure consigliate per [creare un'infrastruttura Active Directory Federation Services][adfs] in Azure.

<!-- links -->

[adds-resource-forest]: adds-forest.md
[adfs]: adfs.md
[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md

[adds-data-disks]: https://msdn.microsoft.com/library/mt674703.aspx
[ad-ds-operations-masters]: https://technet.microsoft.com/library/cc779716(v=ws.10).aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azure-expressroute]: /azure/expressroute/expressroute-introduction
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[capacity-planning-for-adds]: https://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[considerations]: ./considerations.md
[GitHub]: https://github.com/mspnp/identity-reference-architectures/tree/master/adds-extend-domain
[microsoft_systems_center]: https://www.microsoft.com/download/details.aspx?id=50013
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[security-considerations]: #security-considerations
[set-a-static-ip-address]: /azure/virtual-network/virtual-networks-static-private-ip-arm-pportal
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx
[vm-windows-sizes]: /azure/virtual-machines/virtual-machines-windows-sizes

[0]: ./images/adds-extend-domain.png "Architettura di rete ibrida protetta con Active Directory Domain Services"
