---
title: Eseguire una macchina virtuale (VM) Windows in Azure
description: Come eseguire una macchina virtuale Windows in Azure, con particolare attenzione a scalabilità, resilienza, gestibilità e sicurezza.
author: telmosampaio
ms.date: 04/03/2018
ms.openlocfilehash: 748bcfcf599c7fa0e82691632d14e0f78fd78582
ms.sourcegitcommit: a5e549c15a948f6fb5cec786dbddc8578af3be66
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2018
ms.locfileid: "33673603"
---
# <a name="run-a-windows-vm-on-azure"></a>Eseguire una macchina virtuale (VM) Windows in Azure

Questo articolo descrive un set di procedure consolidate per l'esecuzione di una macchina virtuale Windows in Azure. Include suggerimenti per il provisioning della macchina virtuale insieme ai componenti di rete e archiviazione. [**Distribuire questa soluzione.**](#deploy-the-solution)

![[0]][0]

## <a name="components"></a>Componenti

Il provisioning di una macchina virtuale di Azure richiede componenti aggiuntivi oltre alla VM stessa, incluse risorse di rete e di archiviazione.

* **Gruppo di risorse.** Un [gruppo di risorse][resource-manager-overview] è un contenitore logico di risorse correlate di Azure. È generalmente necessario raggruppare le risorse in base alla loro durata e alle persone che le gestiranno. 

* **VM**. È possibile eseguire il provisioning di una macchina virtuale da un elenco di immagini pubblicate, da un'immagine gestita personalizzata o da un file del disco rigido virtuale (VHD) caricato nell'archivio BLOB di Azure.

* **Managed Disks**. [Azure Managed Disks][managed-disks] semplifica la gestione dei dischi, gestendo automaticamente le risorse di archiviazione. Il disco del sistema operativo è un disco rigido virtuale archiviato in [Archiviazione di Azure][azure-storage], dove viene conservato anche quando il computer host è inattivo. Si consiglia anche di creare uno o più [dischi dati][data-disk], ovvero dischi rigidi virtuali usati per i dati dell'applicazione.

* **Disco temporaneo.** La VM viene creata con un disco temporaneo, ovvero l'unità `D:` in Windows. Questo disco viene archiviato in un'unità fisica nel computer host. *Non* viene salvato nell'Archiviazione di Azure ed è possibile che venga eliminato durante i riavvii e altri eventi del ciclo di vita della macchina virtuale. Usare questo disco solo per dati temporanei, ad esempio file di paging o di scambio.

* **Rete virtuale (VNet).** Ogni macchina virtuale di Azure viene distribuita in una rete virtuale che può essere suddivisa in più subnet.

* **Interfaccia di rete (NIC)**. La scheda di interfaccia di rete consente alla VM di comunicare con la rete virtuale.  

* **Indirizzo IP pubblico.** Un indirizzo IP pubblico è necessario per comunicare con la VM &mdash;, ad esempio tramite Desktop remoto.  

* **DNS di Azure**. [DNS di Azure][azure-dns] è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.

* **Gruppo di sicurezza di rete**. I [gruppi di sicurezza di rete][nsg] vengono usati per consentire o negare il traffico di rete verso le macchine virtuali. I gruppi di sicurezza di rete possono essere associati a subnet o singole istanze di macchina virtuale. 

* **Diagnostica.** La registrazione diagnostica è essenziale per la gestione e la risoluzione dei problemi della macchina virtuale.

## <a name="vm-recommendations"></a>Indicazioni per le VM

Azure offre macchine virtuali di diverse dimensioni. Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure][virtual-machine-sizes]. Se si sposta un carico di lavoro esistente in Azure, per iniziare scegliere le dimensioni della VM più simili a quelle dei server locali. Misurare quindi le prestazioni del carico di lavoro effettivo in relazione alla CPU, alla memoria e alle operazioni di input/output al secondo (IOPS) del disco e regolare le dimensioni in base alle necessità. Se sono necessarie più schede di interfaccia di rete per la macchina virtuale, tenere presente che per ogni [dimensione di macchina virtuale][vm-size-tables] è definito un numero massimo di schede.

È in genere consigliabile scegliere l'area di Azure più vicina agli utenti interni o ai clienti. È tuttavia possibile che non tutte le dimensioni di macchina virtuale siano disponibili in tutte le aree. Per altre informazioni, vedere i [servizi disponibili in base all'area][services-by-region]. Per visualizzare un elenco delle dimensioni di macchina virtuale disponibili in un'area specifica, eseguire il comando seguente dall'interfaccia della riga di comando di Azure:

```
az vm list-sizes --location <location>
```

Per informazioni sulla scelta di un'immagine di macchina virtuale pubblicata, vedere [Come trovare immagini di macchine virtuali Windows][select-vm-image].

Abilitare il monitoraggio e la diagnostica, tra cui le metriche di base sull'integrità, i log relativi all'infrastruttura di diagnostica e la [diagnostica di avvio][boot-diagnostics]. La diagnostica di avvio permette di diagnosticare gli errori di avvio quando la VM passa a uno stato non avviabile. Per altre informazioni, vedere [Abilitare il monitoraggio e la diagnostica][enable-monitoring].  

## <a name="disk-and-storage-recommendations"></a>Indicazioni per il disco e l'archiviazione

Per ottimizzare le prestazioni I/O del disco, si consiglia di usare [Archiviazione Premium][premium-storage], che archivia i dati in unità SSD (Solid State Drive). I costi dipendono dalla capacità del disco sottoposto a provisioning. Anche IOPS e velocità effettiva, ovvero la velocità di trasferimento dati, dipendono dalle dimensioni del disco. Quando si effettua il provisioning di un disco è quindi consigliabile tenere in considerazione tutti e tre i fattori, ovvero capacità, IOPS e velocità effettiva. 

È anche consigliabile usare [Managed Disks][managed-disks]. I dischi gestiti non richiedono un account di archiviazione. È sufficiente specificare le dimensioni e il tipo di disco per distribuirlo come risorsa a disponibilità elevata.

Aggiungere uno o più dischi dati. Quando si crea un VHD, il disco non è formattato. Accedere alla VM per formattare il disco. Quando possibile, installare applicazioni su un disco dati, anziché sul disco del sistema operativo. Potrebbe essere che alcune applicazioni legacy installino componenti nell'unità C:; In tal caso, è possibile [ridimensionare il disco del sistema operativo] [ resize-os-disk] tramite PowerShell.

Creare un account di archiviazione per conservare i log di diagnostica. Un account di archiviazione con ridondanza locale standard è sufficiente per i log di diagnostica.

> [!NOTE]
> Se non si usa Managed Disks, creare account di archiviazione di Azure separati per ogni macchina virtuale che dovrà contenere i dischi rigidi virtuali, per evitare di raggiungere i [limiti di operazioni di I/O al secondo][vm-disk-limits] per gli account di archiviazione. Prestare attenzione ai limiti totali di I/O dell'account di archiviazione. Per altre informazioni, vedere [Limiti relativi ai dischi della macchina virtuale][vm-disk-limits].


## <a name="network-recommendations"></a>Indicazioni per la rete

L'indirizzo IP pubblico può essere dinamico o statico. Per impostazione predefinita, è dinamico.

* Riservare un [indirizzo IP statico][static-ip] se è necessario un indirizzo IP fisso che non subisca modifiche &mdash; ad esempio se è necessario creare un record "A" nel DNS o se è necessario aggiungere l'indirizzo IP a un elenco di indirizzi attendibili.
* È inoltre possibile creare un nome di dominio completo (FQDN) per l'indirizzo IP. È quindi possibile registrare un [record CNAME][cname-record] nel DNS che punta al nome FQDN. Per altre informazioni, vedere [Creare un nome di dominio completo nel portale di Azure][fqdn].

Tutti i gruppi di sicurezza di rete contengono un set di [regole predefinite][nsg-default-rules], inclusa una regola che blocca tutto il traffico Internet in ingresso. Le regole predefinite non possono essere eliminate, ma altre regole possono eseguirne l'override. Per consentire il traffico Internet, creare regole per indirizzare il traffico in ingresso a determinate porte &mdash; ad esempio la porta 80 per il traffico HTTP.

Per abilitare Desktop remoto, aggiungere una regola all'NSG per consentire il traffico in arrivo sulla porta TCP 3389.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

È possibile aumentare o ridurre le prestazioni di una VM [cambiando le dimensioni della macchina virtuale][vm-resize]. Per ottenere la scalabilità orizzontale, inserire due o più macchine virtuali nell'ambito di un servizio di bilanciamento del carico. Per altre informazioni, vedere l'[architettura di riferimento a più livelli](./n-tier-sql-server.md).

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Per una disponibilità più elevata, distribuire più macchine virtuali in un set di disponibilità. Sarà così disponibile anche un [contratto di servizio][vm-sla] di livello più elevato.

È possibile che la VM sia interessata da attività di [manutenzione pianificata][planned-maintenance] o [manutenzione non pianificata][manage-vm-availability]. È possibile usare i [log di riavvio della VM][reboot-logs] per determinare se un riavvio della VM è stato provocato da attività di manutenzione pianificata.

Per proteggersi dalla perdita accidentale di dati durante le operazioni normali, ad esempio a causa di un errore dell'utente, è consigliabile implementare anche i backup temporizzati usando [snapshot di BLOB][blob-snapshot] o un altro strumento.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

**Gruppi di risorse.** Inserire nello stesso [gruppo di risorse][resource-manager-overview] le risorse strettamente associate che condividono lo stesso ciclo di vita. I gruppi di risorse consentono di distribuire e monitorare le risorse come gruppo, tenendo traccia dei costi per ogni gruppo di risorse. È inoltre possibile eliminare un intero set di risorse, operazione molto utile nelle distribuzioni di test. Assegnare nomi di risorsa significativi per semplificare l'individuazione di una risorsa specifica e comprenderne il ruolo. Per altre informazioni, vedere [Convenzioni di denominazione][naming-conventions].

**Arresto di una VM.** Azure distingue tra gli stati "Arrestato" e "Deallocato". L'addebito avviene quando lo stato della VM viene arrestato, ma non quando la VM viene deallocata. Anche il pulsante **Arresta** nel portale di Azure consente di deallocare la VM. Se l'arresto viene effettuato tramite il sistema operativo ad accesso eseguito, la VM viene arrestata ma **non** deallocata, quindi gli addebiti continueranno a essere effettuati.

**Eliminazione di una VM.** Se si elimina una VM, i VHD non vengono eliminati. È quindi possibile eliminare in modo sicuro la macchina virtuale senza perdere dati. Verranno tuttavia applicati comunque addebiti per l'archiviazione. Per eliminare il VHD, eliminare il file dall'[archivio BLOB][blob-storage]. Per impedire l'eliminazione accidentale, usare un [blocco di risorsa][resource-lock] per bloccare l'intero gruppo di risorse o le singole risorse, ad esempio una macchina virtuale.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Usare il [Centro sicurezza di Azure][security-center] per ottenere una visualizzazione centrale dello stato di sicurezza delle risorse di Azure. Il Centro sicurezza monitora potenziali problemi di sicurezza e offre un'immagine completa dello stato della sicurezza della distribuzione. Il Centro sicurezza è configurato per ogni sottoscrizione di Azure. Abilitare la raccolta dei dati di sicurezza come descritto nella [Guida introduttiva per il Centro sicurezza di Azure][security-center-get-started]. Quando la raccolta dei dati è abilitata, il Centro sicurezza analizza automaticamente tutte le macchine virtuali create nell'ambito della sottoscrizione.

**Gestione delle patch.** Se abilitato, Centro sicurezza controlla se mancano aggiornamenti critici e della sicurezza. Usare le [impostazioni di Criteri di gruppo][group-policy] nella VM per abilitare gli aggiornamenti automatici del sistema.

**Antimalware.** Se abilitato, Centro sicurezza PC controlla se è installato il software antimalware. È inoltre possibile utilizzare Centro sicurezza PC per installare il software antimalware all'interno del portale di Azure.

**Operazioni.** Usare il [controllo degli accessi in base al ruolo][rbac] per controllare l'accesso alle risorse di Azure da distribuire. Il controllo degli accessi in base al ruolo consente di assegnare i ruoli di autorizzazione ai membri del proprio team DevOps. Ad esempio, il ruolo di lettura permette di visualizzare le risorse di Azure, ma non di crearle, gestirle o eliminarle. Alcuni ruoli sono specifici di determinati tipi di risorse di Azure. Ad esempio, il ruolo di Collaboratore Macchina virtuale consente di riavviare o deallocare una VM, reimpostare la password di amministratore, creare una nuova VM e così via. Altri [ruoli predefiniti di Controllo degli accessi in base al ruolo][rbac-roles] che potrebbero essere utili per questa architettura includono [Utente DevTest Labs][rbac-devtest] e [Collaboratore Rete][rbac-network]. Oltre a poter assegnare un utente a più ruoli, è possibile creare ruoli personalizzati per autorizzazioni ancora più dettagliate.

> [!NOTE]
> Il controllo degli accessi in base al ruolo non limita le azioni eseguibili da un utente registrato in una VM. Le autorizzazioni sono determinate dal tipo di account sul sistema operativo guest.   

Per verificare le azioni di provisioning e altri eventi della VM, usare i [log di controllo][audit-logs].

**Crittografia dei dati.** Se è necessario crittografare i dischi del sistema operativo e i dischi dati, usare [Crittografia dischi di Azure ][disk-encryption]. 

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura è disponibile in [GitHub][github-folder]. Ecco cosa viene distribuito:

  * Una rete virtuale con una sola subnet denominata **Web** e usata come host della macchina virtuale.
  * Un gruppo di sicurezza di rete con due regole in ingresso per consentire il traffico RDP e HTTP alla macchina virtuale.
  * Una macchina virtuale che esegue la versione più recente di Windows Server 2016 Datacenter Edition.
  * Un'estensione script personalizzata di esempio che formatta i due dischi dati e uno script DSC di PowerShell che distribuisce Internet Information Services (IIS).

### <a name="prerequisites"></a>prerequisiti

1. Clonare, creare una copia tramite fork o scaricare il file ZIP per il repository GitHub delle [architetture di riferimento][ref-arch-repo].

2. Verificare che nel computer sia installata l'interfaccia della riga di comando di Azure 2.0. Per istruzioni sull'installazione dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-2].

3. Installare il pacchetto npm dei [blocchi predefiniti di Azure][azbb].

4. Al prompt dei comandi, di Bash o di PowerShell accedere all'account Azure con il comando seguente.

  ```bash
  az login
  ```

### <a name="deploy-the-solution-using-azbb"></a>Distribuire la soluzione mediante azbb

Per distribuire questa architettura di riferimento, eseguire questa procedura:

1. Passare alla cartella `virtual-machines\single-vm\parameters\windows` per il repository scaricato nel passaggio dei prerequisiti precedente.

2. Aprire il file `single-vm-v2.json` e immettere un nome utente e una password tra virgolette, quindi salvare il file.

  ```bash
  "adminUsername": "",
  "adminPassword": "",
  ```

3. Eseguire `azbb` per distribuire la macchina virtuale di esempio, come illustrato di seguito.

  ```bash
  azbb -s <subscription_id> -g <resource_group_name> -l <location> -p single-vm-v2.json --deploy
  ```

Per verificare la distribuzione, eseguire questo comando dell'interfaccia della riga di comando di Azure per trovare l'indirizzo IP pubblico della macchina virtuale:

```bash
az vm show -n ra-single-windows-vm1 -g <resource-group-name> -d -o table
```

Se si passa a questo indirizzo in un Web browser, verrà visualizzata la home page di IIS predefinita.

Per informazioni sulla personalizzazione di questa distribuzione, visitare il [repository GitHub][git] di Microsoft.

<!-- links -->
[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: /azure/virtual-machines/virtual-machines-windows-create-availability-set
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azbbv2]: https://github.com/mspnp/template-building-blocks
[azure-cli-2]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-dns]: /azure/dns/dns-overview
[azure-storage]: /azure/storage/storage-introduction
[blob-snapshot]: /azure/storage/storage-blob-snapshots
[blob-storage]: /azure/storage/storage-introduction
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: /azure/virtual-machines/virtual-machines-windows-about-disks-vhds
[disk-encryption]: /azure/security/azure-security-disk-encryption
[enable-monitoring]: /azure/monitoring-and-diagnostics/insights-how-to-use-diagnostics
[fqdn]: /azure/virtual-machines/virtual-machines-windows-portal-create-fqdn
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[group-policy]: https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn595129(v=ws.11)
[log-collector]: https://azure.microsoft.com/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[managed-disks]: /azure/storage/storage-managed-disks-overview
[naming-conventions]: ../../best-practices/naming-conventions.md
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-default-rules]: /azure/virtual-network/virtual-networks-nsg#default-rules
[planned-maintenance]: /azure/virtual-machines/virtual-machines-windows-planned-maintenance
[premium-storage]: /azure/virtual-machines/windows/premium-storage
[premium-storage-supported]: /azure/virtual-machines/windows/premium-storage#supported-vms
[rbac]: /azure/active-directory/role-based-access-control-what-is
[rbac-roles]: /azure/active-directory/role-based-access-built-in-roles
[rbac-devtest]: /azure/active-directory/role-based-access-built-in-roles#devtest-labs-user
[rbac-network]: /azure/active-directory/role-based-access-built-in-roles#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[resize-os-disk]: /azure/virtual-machines/virtual-machines-windows-expand-os-disk
[resource-lock]: /azure/resource-group-lock-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[security-center]: /azure/security-center/security-center-intro
[security-center-get-started]: /azure/security-center/security-center-get-started
[select-vm-image]: /azure/virtual-machines/virtual-machines-windows-cli-ps-findimage
[services-by-region]: https://azure.microsoft.com/regions/#services
[static-ip]: /azure/virtual-network/virtual-networks-reserved-public-ip
[virtual-machine-sizes]: /azure/virtual-machines/virtual-machines-windows-sizes
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-resize]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
[vm-size-tables]: /azure/virtual-machines/virtual-machines-windows-sizes
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[0]: ./images/single-vm-diagram.png "Singola architettura VM di Windows in Azure"
