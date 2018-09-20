---
title: Eseguire una macchina virtuale Linux in Azure
description: Informazioni su come eseguire una VM in Azure, con particolare attenzione a scalabilità, resilienza, gestibilità e sicurezza.
author: telmosampaio
ms.date: 04/03/2018
ms.openlocfilehash: b53db016a594bace880aaac4e16f0586fe3057b1
ms.sourcegitcommit: 25bf02e89ab4609ae1b2eb4867767678a9480402
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2018
ms.locfileid: "45584732"
---
# <a name="run-a-linux-vm-on-azure"></a>Eseguire una macchina virtuale Linux in Azure

Questo articolo descrive un set di procedure consolidate per l'esecuzione di una macchina virtuale Linux in Azure. Include suggerimenti per il provisioning della macchina virtuale insieme ai componenti di rete e archiviazione. [**Distribuire questa soluzione.**](#deploy-the-solution)

![[0]][0]

## <a name="components"></a>Componenti

Il provisioning di una macchina virtuale di Azure richiede componenti aggiuntivi oltre alla VM stessa, incluse risorse di rete e di archiviazione.

* **Gruppo di risorse.** Un [gruppo di risorse][resource-manager-overview] è un contenitore logico di risorse correlate di Azure. È generalmente necessario raggruppare le risorse in base alla loro durata e alle persone che le gestiranno. 

* **VM**. È possibile eseguire il provisioning di una macchina virtuale da un elenco di immagini pubblicate, da un'immagine gestita personalizzata o da un file del disco rigido virtuale (VHD) caricato nell'archivio BLOB di Azure. Azure supporta l'esecuzione di molte distribuzioni Linux diffuse, tra cui CentOS, Debian, Red Hat Enterprise, Ubuntu e FreeBSD. Per altre informazioni, vedere [Azure e Linux][azure-linux].

* **Managed Disks**. [Azure Managed Disks][managed-disks] semplifica la gestione dei dischi, gestendo automaticamente le risorse di archiviazione. Il disco del sistema operativo è un disco rigido virtuale archiviato in [Archiviazione di Azure][azure-storage], dove viene conservato anche quando il computer host è inattivo. Per le macchine virtuali Linux, il disco del sistema operativo è `/dev/sda1`. Si consiglia anche di creare uno o più [dischi dati][data-disk], ovvero dischi rigidi virtuali usati per i dati dell'applicazione. 

* **Disco temporaneo.** La VM viene creata con un disco temporaneo. Questo disco viene archiviato in un'unità fisica nel computer host. *Non* viene salvato nell'Archiviazione di Azure ed è possibile che venga eliminato durante i riavvii e altri eventi del ciclo di vita della macchina virtuale. Usare questo disco solo per dati temporanei, ad esempio file di paging o di scambio. Per le macchine virtuali Linux, il disco temporaneo è `/dev/sdb1` ed è montato in `/mnt/resource` o `/mnt`.

* **Rete virtuale (VNet).** Ogni macchina virtuale di Azure viene distribuita in una rete virtuale che può essere suddivisa in più subnet.

* **Interfaccia di rete (NIC)**. La scheda di interfaccia di rete consente alla VM di comunicare con la rete virtuale.

* **Indirizzo IP pubblico.** È necessario un indirizzo IP pubblico per comunicare con la macchina virtuale, ad esempio tramite SSH.

* **DNS di Azure**. [DNS di Azure][azure-dns] è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.

* **Gruppo di sicurezza di rete**. I [gruppi di sicurezza di rete][nsg] vengono usati per consentire o negare il traffico di rete verso le macchine virtuali. I gruppi di sicurezza di rete possono essere associati a subnet o singole istanze di macchina virtuale.

* **Diagnostica.** La registrazione diagnostica è essenziale per la gestione e la risoluzione dei problemi della macchina virtuale.

## <a name="vm-recommendations"></a>Indicazioni per le VM

Azure offre macchine virtuali di diverse dimensioni. Per altre informazioni, vedere [Dimensioni delle macchine virtuali in Azure][virtual-machine-sizes]. Se si sposta un carico di lavoro esistente in Azure, per iniziare scegliere le dimensioni della VM più simili a quelle dei server locali. Misurare quindi le prestazioni del carico di lavoro effettivo in relazione alla CPU, alla memoria e alle operazioni di input/output al secondo (IOPS) del disco e regolare le dimensioni in base alle necessità. Se sono necessarie più schede di interfaccia di rete per la macchina virtuale, tenere presente che per ogni [dimensione di macchina virtuale][vm-size-tables] è definito un numero massimo di schede.

È in genere consigliabile scegliere l'area di Azure più vicina agli utenti interni o ai clienti. È tuttavia possibile che non tutte le dimensioni di macchina virtuale siano disponibili in tutte le aree. Per altre informazioni, vedere i [servizi disponibili in base all'area][services-by-region]. Per visualizzare un elenco delle dimensioni di macchina virtuale disponibili in un'area specifica, eseguire il comando seguente dall'interfaccia della riga di comando di Azure:

```
az vm list-sizes --location <location>
```

Per informazioni sulla scelta di un'immagine di macchina virtuale pubblicata, vedere [Come trovare immagini di macchine virtuali Linux][select-vm-image].

Abilitare il monitoraggio e la diagnostica, tra cui le metriche di base sull'integrità, i log relativi all'infrastruttura di diagnostica e la [diagnostica di avvio][boot-diagnostics]. La diagnostica di avvio permette di diagnosticare gli errori di avvio quando la VM passa a uno stato non avviabile. Per altre informazioni, vedere [Abilitare il monitoraggio e la diagnostica][enable-monitoring].  

## <a name="disk-and-storage-recommendations"></a>Indicazioni per il disco e l'archiviazione

Per ottimizzare le prestazioni I/O del disco, si consiglia di usare [Archiviazione Premium][premium-storage], che archivia i dati in unità SSD (Solid State Drive). I costi dipendono dalla capacità del disco sottoposto a provisioning. Anche IOPS e velocità effettiva, ovvero la velocità di trasferimento dati, dipendono dalle dimensioni del disco. Quando si effettua il provisioning di un disco è quindi consigliabile tenere in considerazione tutti e tre i fattori, ovvero capacità, IOPS e velocità effettiva. 

È anche consigliabile usare [Managed Disks][managed-disks]. I dischi gestiti non richiedono un account di archiviazione. È sufficiente specificare le dimensioni e il tipo di disco per distribuirlo come risorsa a disponibilità elevata.

Aggiungere uno o più dischi dati. Quando si crea un VHD, il disco non è formattato. Accedere alla VM per formattare il disco. Nella shell di Linux, i dischi dati vengono visualizzati come `/dev/sdc`, `/dev/sdd` e così via. È possibile eseguire `lsblk` per elencare i dispositivi a blocchi, ad esempio i dischi. Per usare un disco dati, creare una partizione e un file system, quindi montare il disco. Ad esempio: 

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Quando si aggiunge un disco dati, al disco viene assegnato un ID numero di unità logica (LUN). È anche possibile specificare l'ID LUN &mdash; ad esempio se si intende sostituire un disco e si vuole mantenere lo stesso ID LUN o se si ha un'applicazione che cerca un ID LUN specifico. Tuttavia, tenere presente che gli ID LUN devono essere univoci per ogni disco.

È possibile modificare l'utilità di pianificazione I/O per ottimizzare le prestazioni nelle unità SSD, dato che i dischi delle VM negli account di archiviazione Premium sono SSD. Normalmente si consiglia di usare l'utilità di pianificazione NOOP per le unità SSD, ma è necessario usare uno strumento come [iostat] per monitorare le prestazioni di I/O del disco relative al proprio carico di lavoro.

Creare un account di archiviazione per conservare i log di diagnostica. Un account di archiviazione con ridondanza locale standard è sufficiente per i log di diagnostica.

> [!NOTE]
> Se non si usa Managed Disks, creare account di archiviazione di Azure separati per ogni macchina virtuale che dovrà contenere i dischi rigidi virtuali, per evitare di raggiungere i [limiti di operazioni di I/O al secondo][vm-disk-limits] per gli account di archiviazione. Prestare attenzione ai limiti totali di I/O dell'account di archiviazione. Per altre informazioni, vedere [Limiti relativi ai dischi della macchina virtuale][vm-disk-limits].

## <a name="network-recommendations"></a>Indicazioni per la rete

L'indirizzo IP pubblico può essere dinamico o statico. Per impostazione predefinita, è dinamico.

* Riservare un [indirizzo IP statico][static-ip] se è necessario un indirizzo IP fisso che non subirà modifiche &mdash; ad esempio se è necessario creare un record A nel DNS o se è necessario aggiungere l'indirizzo IP a un elenco di indirizzi attendibili.
* È inoltre possibile creare un nome di dominio completo (FQDN) per l'indirizzo IP. È quindi possibile registrare un [record CNAME][cname-record] nel DNS che punta al nome FQDN. Per altre informazioni, vedere [Creare un nome di dominio completo nel portale di Azure][fqdn]. È possibile usare [DNS di Azure][azure-dns] o un altro servizio DNS.

Tutti i gruppi di sicurezza di rete contengono un set di [regole predefinite][nsg-default-rules], inclusa una regola che blocca tutto il traffico Internet in ingresso. Le regole predefinite non possono essere eliminate, ma altre regole possono eseguirne l'override. Per consentire il traffico Internet, creare regole per indirizzare il traffico in ingresso a determinate porte &mdash; ad esempio la porta 80 per il traffico HTTP.

Per abilitare l'accesso SSH, aggiungere una regola al gruppo di sicurezza di rete per consentire il traffico in arrivo sulla porta TCP 22.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

È possibile aumentare o ridurre le prestazioni di una VM [cambiando le dimensioni della macchina virtuale][vm-resize]. Per ottenere la scalabilità orizzontale, inserire due o più macchine virtuali nell'ambito di un servizio di bilanciamento del carico. Per altre informazioni, vedere l'[architettura di riferimento a più livelli](./n-tier-cassandra.md).

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Per una disponibilità più elevata, distribuire più macchine virtuali in un set di disponibilità. Sarà così disponibile anche un [contratto di servizio][vm-sla] di livello più elevato.

È possibile che la VM sia interessata da attività di [manutenzione pianificata][planned-maintenance] o [manutenzione non pianificata][manage-vm-availability]. È possibile usare i [log di riavvio della VM][reboot-logs] per determinare se un riavvio della VM è stato provocato da attività di manutenzione pianificata.

Per proteggersi dalla perdita accidentale di dati durante le operazioni normali, ad esempio a causa di un errore dell'utente, è consigliabile implementare anche i backup temporizzati usando [snapshot di BLOB][blob-snapshot] o un altro strumento.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

**Gruppi di risorse.** Inserire nello stesso [gruppo di risorse][resource-manager-overview] le risorse strettamente associate che condividono lo stesso ciclo di vita. I gruppi di risorse consentono di distribuire e monitorare le risorse come gruppo, tenendo traccia dei costi per ogni gruppo di risorse. È inoltre possibile eliminare un intero set di risorse, operazione molto utile nelle distribuzioni di test. Assegnare nomi di risorsa significativi per semplificare l'individuazione di una risorsa specifica e comprenderne il ruolo. Per altre informazioni, vedere [Convenzioni di denominazione][naming-conventions].

**SSH**. Prima di creare una VM Linux, generare una coppia di chiavi RSA pubblica/privata a 2.048 bit. Quando si crea la VM, utilizzare il file di chiave pubblica. Per altre informazioni, vedere [Come usare SSH con Linux e Mac in Azure][ssh-linux].

**Arresto di una VM.** Azure distingue tra gli stati "Arrestato" e "Deallocato". L'addebito avviene quando lo stato della VM viene arrestato, ma non quando la VM viene deallocata. Anche il pulsante **Arresta** nel portale di Azure consente di deallocare la VM. Se l'arresto viene effettuato tramite il sistema operativo ad accesso eseguito, la VM viene arrestata ma **non** deallocata, quindi gli addebiti continueranno a essere effettuati.

**Eliminazione di una VM.** Se si elimina una VM, i VHD non vengono eliminati. È quindi possibile eliminare in modo sicuro la macchina virtuale senza perdere dati. Verranno tuttavia applicati comunque addebiti per l'archiviazione. Per eliminare il VHD, eliminare il file dall'[archivio BLOB][blob-storage]. Per impedire l'eliminazione accidentale, usare un [blocco di risorsa][resource-lock] per bloccare l'intero gruppo di risorse o le singole risorse, ad esempio una macchina virtuale.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Usare il [Centro sicurezza di Azure][security-center] per ottenere una visualizzazione centrale dello stato di sicurezza delle risorse di Azure. Il Centro sicurezza monitora potenziali problemi di sicurezza e offre un'immagine completa dello stato della sicurezza della distribuzione. Il Centro sicurezza è configurato per ogni sottoscrizione di Azure. Abilitare la raccolta dei dati di sicurezza come descritto nella [Guida introduttiva per il Centro sicurezza di Azure][security-center-get-started]. Quando la raccolta dei dati è abilitata, il Centro sicurezza analizza automaticamente tutte le macchine virtuali create nell'ambito della sottoscrizione.

**Gestione delle patch.** Se abilitato, Centro sicurezza controlla se mancano aggiornamenti critici e della sicurezza. 

**Antimalware.** Se abilitato, Centro sicurezza PC controlla se è installato il software antimalware. È inoltre possibile utilizzare Centro sicurezza PC per installare il software antimalware all'interno del portale di Azure.

**Operazioni.** Usare il [controllo degli accessi in base al ruolo][rbac] per controllare l'accesso alle risorse di Azure da distribuire. Il controllo degli accessi in base al ruolo consente di assegnare i ruoli di autorizzazione ai membri del proprio team DevOps. Ad esempio, il ruolo di lettura permette di visualizzare le risorse di Azure, ma non di crearle, gestirle o eliminarle. Alcuni ruoli sono specifici di determinati tipi di risorse di Azure. Ad esempio, il ruolo di Collaboratore Macchina virtuale consente di riavviare o deallocare una VM, reimpostare la password di amministratore, creare una nuova VM e così via. Altri [ruoli predefiniti di Controllo degli accessi in base al ruolo][rbac-roles] che potrebbero essere utili per questa architettura includono [Utente DevTest Labs][rbac-devtest] e [Collaboratore Rete][rbac-network]. Oltre a poter assegnare un utente a più ruoli, è possibile creare ruoli personalizzati per autorizzazioni ancora più dettagliate.

> [!NOTE]
> Il controllo degli accessi in base al ruolo non limita le azioni eseguibili da un utente registrato in una VM. Le autorizzazioni sono determinate dal tipo di account sul sistema operativo guest.   

Per verificare le azioni di provisioning e altri eventi della VM, usare i [log di controllo][audit-logs].

**Crittografia dei dati.** Se è necessario crittografare i dischi del sistema operativo e i dischi dati, usare [Crittografia dischi di Azure ][disk-encryption]. 

**Protezione DDoS**. Si consiglia di abilitare [Protezione DDoS standard](/azure/virtual-network/ddos-protection-overview), che offre una mitigazione DDoS aggiuntiva per le risorse in una rete virtuale. Anche se la protezione DDoS di base è abilitata automaticamente come parte della piattaforma Azure, Protezione DDoS standard offre funzionalità di mitigazione ottimizzate in modo specifico per le risorse di rete virtuale di Azure.  

## <a name="deploy-the-solution"></a>Distribuire la soluzione

È disponibile una distribuzione in [GitHub][github-folder]. Ecco cosa viene distribuito:

  * Una rete virtuale con una sola subnet denominata **Web** e usata come host della macchina virtuale.
  * Un gruppo di sicurezza di rete con due regole in ingresso per consentire il traffico SSH e HTTP alla macchina virtuale.
  * Una macchina virtuale che esegue la versione più recente di Ubuntu 16.04.3 LTS.
  * Un'estensione script personalizzata di esempio che formatta i due dischi rigidi e distribuisce Apache HTTP Server sulla macchina virtuale Ubuntu.

### <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

5. Creare una coppia di chiavi SSH. Per altre informazioni, vedere [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

### <a name="deploy-the-solution-using-azbb"></a>Distribuire la soluzione mediante azbb

1. Passare alla cartella `virtual-machines/single-vm/parameters/linux` per il repository scaricato nel passaggio dei prerequisiti precedente.

2. Aprire il file `single-vm-v2.json` e immettere un nome utente e una chiave SSH pubblica tra virgolette, quindi salvare il file.

  ```bash
  "adminUsername": "<your username>",
  "sshPublicKey": "ssh-rsa AAAAB3NzaC1...",
  ```

3. Eseguire `azbb` per distribuire la macchina virtuale di esempio, come illustrato di seguito.

  ```bash
  azbb -s <subscription_id> -g <resource_group_name> -l <location> -p single-vm-v2.json --deploy
  ```

Per verificare la distribuzione, eseguire questo comando dell'interfaccia della riga di comando di Azure per trovare l'indirizzo IP pubblico della macchina virtuale:

```bash
az vm show -n ra-single-linux-vm1 -g <resource-group-name> -d -o table
```

Se si passa a questo indirizzo in un Web browser, verrà visualizzata la home page di Apache2 predefinita.

<!-- links -->
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
[azure-dns]: /azure/dns/dns-overview
[fqdn]: /azure/virtual-machines/virtual-machines-linux-portal-create-fqdn
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/single-vm
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[managed-disks]: /azure/storage/storage-managed-disks-overview
[naming-conventions]: /azure/architecture/best-practices/naming-conventions.md
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-default-rules]: /azure/virtual-network/virtual-networks-nsg#default-rules
[planned-maintenance]: /azure/virtual-machines/virtual-machines-linux-planned-maintenance
[premium-storage]: /azure/virtual-machines/linux/premium-storage
[premium-storage-supported]: /azure/virtual-machines/linux/premium-storage#supported-vms
[rbac]: /azure/active-directory/role-based-access-control-what-is
[rbac-roles]: /azure/active-directory/role-based-access-built-in-roles
[rbac-devtest]: /azure/active-directory/role-based-access-built-in-roles#devtest-labs-user
[rbac-network]: /azure/active-directory/role-based-access-built-in-roles#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[resource-lock]: /azure/resource-group-lock-resources
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[security-center]: /azure/security-center/security-center-intro
[security-center-get-started]: /azure/security-center/security-center-get-started
[select-vm-image]: /azure/virtual-machines/virtual-machines-linux-cli-ps-findimage
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssh-linux]: /azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys
[static-ip]: /azure/virtual-network/virtual-networks-reserved-public-ip
[virtual-machine-sizes]: /azure/virtual-machines/virtual-machines-linux-sizes
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-resize]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
[vm-size-tables]: /azure/virtual-machines/virtual-machines-linux-sizes
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[0]: ./images/single-vm-diagram.png "Singola VM Linux in Azure"
