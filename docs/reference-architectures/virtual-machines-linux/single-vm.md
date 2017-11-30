---
title: Eseguire una macchina virtuale Linux in Azure
description: "Informazioni su come eseguire una VM in Azure, con particolare attenzione a scalabilità, resilienza, gestibilità e sicurezza."
author: telmosampaio
ms.date: 09/06/2017
pnp.series.title: Linux VM workloads
pnp.series.next: multi-vm
pnp.series.prev: ./index
ms.openlocfilehash: f38c53db5df1681a1abc926df9f0b7f929ceb76b
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="run-a-linux-vm-on-azure"></a>Eseguire una macchina virtuale Linux in Azure

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di una macchina virtuale Linux in Azure. Include suggerimenti per il provisioning della macchina virtuale insieme ai componenti di rete e archiviazione. Questa architettura può essere usata per eseguire un'istanza singola ed è la base per architetture più complesse come le applicazioni a più livelli. [**Distribuire questa soluzione**.](#deploy-the-solution)

![[0]][0]

*Scaricare un [file di Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architettura

Il provisioning di una VM in Azure coinvolge un altri componenti mobili oltre alla VM in sé. È necessario prendere in considerazione elementi di calcolo, di rete e archiviazione.

* **Gruppo di risorse.** Un [*gruppo di risorse*][resource-manager-overview] è un contenitore in cui risiedono le risorse correlate. In genere si creano gruppi di risorse per diverse risorse in una soluzione in base alla loro durata e alle persone che le gestiranno. Per il carico di lavoro di una singola macchina virtuale è possibile creare un unico gruppo di risorse per tutte le risorse.
* **VM**. Azure supporta l'esecuzione di molte distribuzioni Linux diffuse, tra cui CentOS, Debian, Red Hat Enterprise, Ubuntu e FreeBSD. Per altre informazioni, vedere [Azure e Linux][azure-linux]. È possibile effettuare il provisioning di una macchina virtuale da un elenco di immagini pubblicate oppure da un file di disco rigido virtuale (VHD) caricato nell'archivio BLOB di Azure.
* **Disco del sistema operativo.** Il disco del sistema operativo è un VHD archiviato in [Archiviazione di Azure][azure-storage]. Viene quindi salvato in modo permanente anche se il computer host si arresta. Il disco del sistema operativo è `/dev/sda1`.
* **Disco temporaneo.** La VM viene creata con un disco temporaneo. Questo disco viene archiviato in un'unità fisica nel computer host. *Non* viene salvato nell'Archiviazione di Azure ed è possibile che venga eliminato durante i riavvii e altri eventi del ciclo di vita della macchina virtuale. Usare questo disco solo per dati temporanei, ad esempio file di paging o di scambio. Il disco temporaneo è `/dev/sdb1` ed è montato in `/mnt/resource` o `/mnt`.
* **Dischi dati.** Un [disco dati][data-disk] è un VHD persistente usato per i dati dell'applicazione. I dischi dati vengono archiviati nell'Archiviazione di Azure, come il disco del sistema operativo.
* **Rete virtuale (VNet) e subnet.** Ogni VM in Azure viene distribuita in una rete virtuale (VNet), che viene a sua volta suddivisa in subnet.
* **Indirizzo IP pubblico.** È necessario un indirizzo IP pubblico per comunicare con la macchina virtuale, ad esempio tramite SSH.
* **Interfaccia di rete (NIC)**. La scheda di interfaccia di rete consente alla VM di comunicare con la rete virtuale.
* **Gruppo di sicurezza di rete (NSG)**. Il [gruppo di sicurezza di rete][nsg] viene usato per consentire/negare il traffico di rete verso la subnet. Un NSG può essere associato a una singola scheda di interfaccia di rete o a una subnet. Se lo si associa a una subnet, le regole dell’NSG si applicano a tutte le VM nella subnet.
* **Diagnostica.** La registrazione diagnostica è essenziale per la gestione e la risoluzione dei problemi della macchina virtuale.

## <a name="recommendations"></a>Raccomandazioni

Questa architettura mostra le indicazioni di base per l'esecuzione di una macchina virtuale Linux in Azure. Non è tuttavia consigliabile usare una singola macchina virtuale per carichi di lavoro di importanza strategica, perché in questo modo viene creato un singolo punto di guasto. Per una disponibilità più elevata, distribuire più VM in un [set di disponibilità][availability-set]. Per altre informazioni, vedere [Esecuzione di più VM in Azure][multi-vm]. 

### <a name="vm-recommendations"></a>Indicazioni per le VM

Azure offre diverse dimensioni per le macchine virtuali, ma è consigliabile scegliere le serie DS e GS perché supportano [Archiviazione Premium][premium-storage]. Selezionare una di queste dimensioni del computer, a meno che non si abbia un carico di lavoro specializzato, ad esempio di high-performance computing. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali in Azure][virtual-machine-sizes].

Se si sposta un carico di lavoro esistente in Azure, per iniziare scegliere le dimensioni della VM più simili a quelle dei server locali. Misurare quindi le prestazioni del carico di lavoro effettivo in relazione agli aspetti di CPU, memoria e operazioni di input/output (IOPS) del disco e regolare le dimensioni secondo necessità. Se sono necessarie più schede di interfaccia di rete per la VM, si noti che il numero massimo di schede di interfaccia di rete dipende dalle [dimensioni della VM][vm-size-tables].

Quando si effettua il provisioning della VM e di altre risorse, è necessario specificare una località. È in genere consigliabile scegliere la località più vicina agli utenti interni o ai clienti. È tuttavia possibile che non tutte le dimensioni della VM siano disponibili in tutte le aree. Per informazioni dettagliate, vedere i [servizi disponibili in base all'area][services-by-region]. Per elencare le dimensioni delle macchine virtuali disponibili in un'area specifica, eseguire questo comando dell'interfaccia della riga di comando di Azure:

```
az vm list-sizes --location <location>
```

Per informazioni sulla scelta di un'immagine di VM pubblicata, vedere [Selezionare immagini di VM Linux con l'interfaccia della riga di comando di Azure][select-vm-image].

Abilitare il monitoraggio e la diagnostica, tra cui le metriche di base sull'integrità, i log relativi all'infrastruttura di diagnostica e la [diagnostica di avvio][boot-diagnostics]. La diagnostica di avvio permette di diagnosticare gli errori di avvio quando la VM passa a uno stato non avviabile. Per altre informazioni, vedere [Abilitare il monitoraggio e la diagnostica][enable-monitoring].  

### <a name="disk-and-storage-recommendations"></a>Indicazioni per il disco e l'archiviazione

Per ottimizzare le prestazioni I/O del disco, si consiglia di usare [Archiviazione Premium][premium-storage], che archivia i dati in unità SSD (Solid State Drive). I costi dipendono dalle dimensioni del disco sottoposto a provisioning. Anche IOPS e velocità effettiva, ovvero la velocità di trasferimento dati, dipendono dalle dimensioni del disco. Quando si effettua il provisioning di un disco è quindi consigliabile tenere in considerazione tutti e tre i fattori, ovvero capacità, IOPS e velocità effettiva. 

È consigliabile anche usare [dischi gestiti](/azure/storage/storage-managed-disks-overview). I dischi gestiti non richiedono un account di archiviazione. Basta specificare le dimensioni e il tipo di disco per distribuirlo in modalità a disponibilità elevata.

Se non si usano dischi gestiti, creare account di archiviazione di Azure separati per ogni macchina virtuale che dovrà contenere i dischi rigidi virtuali, in modo da evitare di raggiungere i limiti di operazioni di I/O al secondo per gli account di archiviazione.

Aggiungere uno o più dischi dati. Quando si crea un VHD, il disco non è formattato. Accedere alla VM per formattare il disco. Nella shell di Linux, i dischi dati vengono visualizzati come `/dev/sdc`, `/dev/sdd` e così via. È possibile eseguire `lsblk` per elencare i dispositivi a blocchi, ad esempio i dischi. Per usare un disco dati, creare una partizione e un file system, quindi montare il disco. ad esempio:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Se non si usano [dischi gestiti](/azure/storage/storage-managed-disks-overview) e sono presenti molti dischi dati, occorre prestare attenzione ai limiti totali di I/O dell'account di archiviazione. Per altre informazioni, vedere [Limiti relativi ai dischi della macchina virtuale][vm-disk-limits].

Quando si aggiunge un disco dati, al disco viene assegnato un ID numero di unità logica (LUN). È anche possibile specificare l'ID LUN &mdash; ad esempio se si intende sostituire un disco e si vuole mantenere lo stesso ID LUN o se si ha un'applicazione che cerca un ID LUN specifico. Tuttavia, tenere presente che gli ID LUN devono essere univoci per ogni disco.

È possibile modificare l'utilità di pianificazione I/O per ottimizzare le prestazioni nelle unità SSD, dato che i dischi delle VM negli account di archiviazione Premium sono SSD. Normalmente si consiglia di usare l'utilità di pianificazione NOOP per le unità SSD, ma è necessario usare uno strumento come [iostat] per monitorare le prestazioni di I/O del disco relative al proprio carico di lavoro.

Per prestazioni ottimali, creare un account di archiviazione separato per i log di diagnostica. Un account di archiviazione con ridondanza locale standard è sufficiente per i log di diagnostica.

### <a name="network-recommendations"></a>Indicazioni per la rete

L'indirizzo IP pubblico può essere dinamico o statico. Per impostazione predefinita, è dinamico.

* Riservare un [indirizzo IP statico][static-ip] se è necessario un indirizzo IP fisso che non subirà modifiche &mdash; ad esempio se è necessario creare un record A nel DNS o se è necessario aggiungere l'indirizzo IP a un elenco di indirizzi attendibili.
* È inoltre possibile creare un nome di dominio completo (FQDN) per l'indirizzo IP. È quindi possibile registrare un [record CNAME][cname-record] nel DNS che punta al nome FQDN. Per altre informazioni, vedere [Creare un nome di dominio completo nel portale di Azure][fqdn].

Tutti i gruppi di sicurezza di rete contengono un set di [regole predefinite][nsg-default-rules], inclusa una regola che blocca tutto il traffico Internet in ingresso. Le regole predefinite non possono essere eliminate, ma altre regole possono eseguirne l'override. Per consentire il traffico Internet, creare regole per indirizzare il traffico in ingresso a determinate porte &mdash; ad esempio la porta 80 per il traffico HTTP.

Per abilitare SSH, aggiungere una regola al gruppo di sicurezza di rete per consentire il traffico in arrivo sulla porta TCP 22.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

È possibile aumentare o ridurre le prestazioni di una VM [cambiando le dimensioni della macchina virtuale][vm-resize]. Per ottenere la scalabilità orizzontale, inserire due o più macchine virtuali nell'ambito di un servizio di bilanciamento del carico. Per informazioni dettagliate, vedere [Running multiple VMs on Azure for scalability and availability][multi-vm] (Esecuzione di più VM in Azure per la scalabilità e la disponibilità).

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Per una disponibilità più elevata, distribuire più macchine virtuali in un set di disponibilità. Sarà così disponibile anche un [contratto di servizio][vm-sla] (SLA) di livello più elevato.

È possibile che la VM sia interessata da attività di [manutenzione pianificata][planned-maintenance] o [manutenzione non pianificata][manage-vm-availability]. È possibile usare i [log di riavvio della VM][reboot-logs] per determinare se un riavvio della VM è stato provocato da attività di manutenzione pianificata.

I dischi rigidi virtuali vengono archiviati nell'[archivio di Azure][azure-storage] e l'archivio di Azure viene replicato per assicurare durabilità e disponibilità.

Per proteggersi dalla perdita accidentale di dati durante le operazioni normali, ad esempio a causa di un errore dell'utente, è consigliabile implementare anche i backup temporizzati usando [snapshot di BLOB][blob-snapshot] o un altro strumento.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

**Gruppi di risorse.** Posizionare in uno stesso [gruppo di risorse][resource-manager-overview] le risorse strettamente associate che condividono lo stesso ciclo di vita. I gruppi di risorse consentono di distribuire e monitorare le risorse in gruppo, distribuendo i costi per ogni gruppo di risorse. È inoltre possibile eliminare un intero set di risorse, operazione molto utile nelle distribuzioni di test. Assegnare alle risorse nomi significativi. In tal modo, è più semplice individuare una specifica risorsa e comprenderne il ruolo. Vedere [Recommended Naming Conventions for Azure Resources][naming conventions] (Convenzioni di denominazione consigliate per le risorse di Azure).

**SSH**. Prima di creare una VM Linux, generare una coppia di chiavi RSA pubblica/privata a 2.048 bit. Quando si crea la VM, utilizzare il file di chiave pubblica. Per altre informazioni, vedere [Come usare SSH con Linux e Mac in Azure][ssh-linux].

**Arresto di una VM.** Azure distingue tra gli stati "Arrestato" e "Deallocato". L'addebito avviene quando lo stato della VM viene arrestato, ma non quando la VM viene deallocata. 

Anche il pulsante **Arresta** nel portale di Azure consente di deallocare la VM. Se l'arresto viene effettuato tramite il sistema operativo ad accesso eseguito, la VM viene arrestata ma *non* deallocata, quindi gli addebiti continueranno a essere effettuati. 

**Eliminazione di una VM.** Se si elimina una VM, i VHD non vengono eliminati. È quindi possibile eliminare in modo sicuro la macchina virtuale senza perdere dati. Verranno tuttavia applicati comunque addebiti per l'archiviazione. Per eliminare il VHD, eliminare il file dall'[archivio BLOB][blob-storage].

Per impedire l'eliminazione accidentale, usare un [blocco di risorsa][resource-lock] per bloccare l'intero gruppo di risorse o le singole risorse, ad esempio la VM.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Usare il [Centro sicurezza di Azure][security-center] per ottenere una visualizzazione centrale dello stato di sicurezza delle risorse di Azure. Il Centro sicurezza monitora potenziali problemi di sicurezza e offre un'immagine completa dello stato della sicurezza della distribuzione. Il Centro sicurezza è configurato per ogni sottoscrizione di Azure. Abilitare la raccolta dei dati di sicurezza come descritto nella [Guida introduttiva per il Centro sicurezza di Azure][security-center-get-started]. Quando la raccolta dei dati è abilitata, il Centro sicurezza analizza automaticamente tutte le macchine virtuali create nell'ambito della sottoscrizione.

**Gestione delle patch.** Se abilitato, Centro sicurezza PC controlla se mancano aggiornamenti critici e della sicurezza. 

**Antimalware.** Se abilitato, Centro sicurezza PC controlla se è installato il software antimalware. È inoltre possibile utilizzare Centro sicurezza PC per installare il software antimalware all'interno del portale di Azure.

**Operazioni.** Usare il [controllo degli accessi in base al ruolo][rbac] per controllare l'accesso alle risorse di Azure da distribuire. Il controllo degli accessi in base al ruolo consente di assegnare i ruoli di autorizzazione ai membri del proprio team DevOps. Ad esempio, il ruolo di lettura permette di visualizzare le risorse di Azure, ma non di crearle, gestirle o eliminarle. Alcuni ruoli sono specifici di determinati tipi di risorse di Azure. Ad esempio, il ruolo di Collaboratore Macchina virtuale consente di riavviare o deallocare una VM, reimpostare la password di amministratore, creare una nuova VM e così via. Altri [ruoli predefiniti di Controllo degli accessi in base al ruolo][rbac-roles] che potrebbero essere utili per questa architettura includono [Utente DevTest Labs][rbac-devtest] e [Collaboratore Rete][rbac-network]. Oltre a poter assegnare un utente a più ruoli, è possibile creare ruoli personalizzati per autorizzazioni ancora più dettagliate.

> [!NOTE]
> Il controllo degli accessi in base al ruolo non limita le azioni eseguibili da un utente registrato in una VM. Le autorizzazioni sono determinate dal tipo di account sul sistema operativo guest.   

Per verificare le azioni di provisioning e altri eventi della VM, usare i [log di controllo][audit-logs].

**Crittografia dei dati.** Se è necessario crittografare i dischi del sistema operativo e i dischi dati, usare [Crittografia dischi di Azure ][disk-encryption]. 

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura è disponibile in [GitHub][github-folder]. Ecco cosa viene distribuito:

  * Una rete virtuale con una sola subnet denominata **Web** e usata come host della macchina virtuale.
  * Un gruppo di sicurezza di rete con due regole in ingresso per consentire il traffico SSH e HTTP alla macchina virtuale.
  * Una macchina virtuale che esegue la versione più recente di Ubuntu 16.04.3 LTS.
  * Un'estensione script personalizzata di esempio che distribuisce Apache HTTP Server alla macchina virtuale Ubuntu e formatta i due dischi dati.

### <a name="prerequisites"></a>Prerequisiti

Prima di poter distribuire l'architettura di riferimento nella propria sottoscrizione, è necessario eseguire i passaggi seguenti.

1. Clonare, creare una copia tramite fork o scaricare il file ZIP per il repository GitHub delle [architetture di riferimento AzureCAT][ref-arch-repo].

2. Verificare che nel computer sia installata l'interfaccia della riga di comando di Azure 2.0. Per installare l'interfaccia della riga di comando, seguire le istruzioni in [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-2].

3. Installare il pacchetto npm dei [blocchi predefiniti di Azure][azbb].

4. Da un prompt dei comandi, di Bash o di PowerShell accedere al proprio account di Azure usando uno dei comandi riportati di seguito e seguire le istruzioni.

  ```bash
  az login
  ```

### <a name="deploy-the-solution-using-azbb"></a>Distribuire la soluzione mediante azbb

Per distribuire il carico di lavoro della singola macchina virtuale di esempio, eseguire questa procedura:

1. Passare alla cartella `virtual-machines\single-vm\parameters\linux` per il repository scaricato nel passaggio dei prerequisiti precedente.

2. Aprire il file `single-vm-v2.json` e immettere un nome utente e una chiave SSH tra virgolette, come illustrato di seguito, quindi salvare il file.

  ```bash
  "adminUsername": "",
  "adminsshPublicKey": "",
  ```

3. Eseguire `azbb` per distribuire la macchina virtuale di esempio, come illustrato di seguito.

  ```bash
  azbb -s <subscription_id> -g <resource_group_name> -l <location> -p single-vm-v2.json --deploy
  ```

Per altre informazioni sulla distribuzione di questa architettura di riferimento di esempio, visitare il [repository GitHub][git].

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [blocchi predefiniti di Azure][azbbv2].
- Distribuire [più macchine virtuali][multi-vm] in Azure.

<!-- links -->
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[multi-vm]: ../virtual-machines-linux/multi-vm.md
[naming conventions]: ../../best-practices/naming-conventions.md
[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks
[azbbv2]: https://github.com/mspnp/template-building-blocks
[azure-cli-2]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-linux]: /azure/virtual-machines/virtual-machines-linux-azure-overview
[azure-storage]: /azure/storage/storage-introduction
[blob-snapshot]: /azure/storage/storage-blob-snapshots
[blob-storage]: /azure/storage/storage-introduction
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: /azure/virtual-machines/virtual-machines-linux-about-disks-vhds
[disk-encryption]: /azure/security/azure-security-disk-encryption
[enable-monitoring]: /azure/monitoring-and-diagnostics/insights-how-to-use-diagnostics
[fqdn]: /azure/virtual-machines/virtual-machines-linux-portal-create-fqdn
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-default-rules]: /azure/virtual-network/virtual-networks-nsg#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: /azure/virtual-machines/virtual-machines-linux-planned-maintenance
[premium-storage]: /azure/storage/storage-premium-storage
[rbac]: /azure/active-directory/role-based-access-control-what-is
[rbac-roles]: /azure/active-directory/role-based-access-built-in-roles
[rbac-devtest]: /azure/active-directory/role-based-access-built-in-roles#devtest-labs-user
[rbac-network]: /azure/active-directory/role-based-access-built-in-roles#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[resource-lock]: /azure/resource-group-lock-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[security-center]: /azure/security-center/
[security-center-get-started]: /azure/security-center/security-center-get-started
[select-vm-image]: /azure/virtual-machines/virtual-machines-linux-cli-ps-findimage
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssh-linux]: /azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys
[static-ip]: /azure/virtual-network/virtual-networks-reserved-public-ip
[virtual-machine-sizes]: /azure/virtual-machines/virtual-machines-linux-sizes
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-resize]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
[vm-size-tables]: /azure/virtual-machines/virtual-machines-linux-sizes
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/virtual-machines/single-vm/README.md
[0]: ./images/single-vm-diagram.png "Singola architettura VM di Linux in Azure"

