---
title: "Eseguire macchine virtuali con carico bilanciato in Azure per la scalabilità e la disponibilità"
description: "Come eseguire più macchine virtuali Linux in Azure per la scalabilità e la disponibilità."
author: telmosampaio
ms.date: 09/07/2017
pnp.series.title: Linux VM workloads
pnp.series.next: n-tier
pnp.series.prev: single-vm
ms.openlocfilehash: 0f772cdc596ea96073da3b9b2a2709ce3041889d
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="run-load-balanced-vms-for-scalability-and-availability"></a>Eseguire macchine virtuali con carico bilanciato per la scalabilità e la disponibilità

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di diverse macchine virtuali Linux in un set di scalabilità dietro a un servizio di bilanciamento del carico, in modo da migliorare la disponibilità e la scalabilità. Questa architettura può essere usata per qualsiasi carico di lavoro senza stato, ad esempio un server Web, ed è un blocco predefinito per la distribuzione di applicazioni a più livelli. [**Distribuire questa soluzione**.](#deploy-the-solution)

![[0]][0]

*Scaricare un [file di Visio][visio-download] di questa architettura.*

## <a name="architecture"></a>Architettura

Questa architettura si basa su quella illustrata in [Eseguire una macchina virtuale Linux in Azure][single vm]. Le raccomandazioni riportate in questo riferimento si applicano anche a questa architettura.

In questa architettura un carico di lavoro viene distribuito tra più istanze di macchina virtuale. L'indirizzo IP pubblico è unico e il traffico Internet viene distribuito alle macchine virtuali tramite un servizio di bilanciamento del carico. Questa architettura può essere usata per un'applicazione a livello singolo, ad esempio un'applicazione Web senza stato.

L'architettura include i componenti seguenti:

* **Gruppo di risorse.** I [*gruppi di risorse*][resource-manager-overview] vengono usati per raggruppare le risorse in modo che possano essere gestite in base alla durata, al proprietario e ad altri criteri.
* **Rete virtuale (VNet) e subnet.** Ogni VM in Azure viene distribuita in una rete virtuale (VNet), che viene a sua volta suddivisa in subnet.
* **Azure Load Balancer**. Il [servizio di bilanciamento del carico] distribuisce le richieste Internet in ingresso alle istanze delle macchine virtuali. 
* **Indirizzo IP pubblico**. Affinché il servizio di bilanciamento del carico possa ricevere il traffico Internet, è richiesto un indirizzo IP pubblico.
* **Set di scalabilità di macchine virtuali**. Un [set di scalabilità di macchine virtuali][vm-scaleset] è un set di macchine virtuali identiche usato per ospitare un carico di lavoro. I set di scalabilità consentono di aumentare o ridurre il numero di istanze delle macchine virtuali manualmente o in base a regole predefinite.
* **Set di disponibilità**. Il [set di disponibilità][availability set] include le macchine virtuali, rendendole idonee per un [contratto di servizio (SLA)][vm-sla] superiore. Per consentire l'applicazione del contratto di servizio superiore, il set di disponibilità deve includere almeno due macchine virtuali. I set di disponibilità sono impliciti nei set di scalabilità. Se si creano macchine virtuali al di fuori di un set di scalabilità, è necessario creare il set di disponibilità in modo indipendente.
* **Dischi gestiti**. Il servizio Azure Managed Disks gestisce i file di disco rigido virtuale (VHD) per i dischi delle macchine virtuali. 
* **Archiviazione**. Creare un account di archiviazione di Azure per contenere i log di diagnostica per le macchine virtuali.

## <a name="recommendations"></a>Raccomandazioni

I requisiti della propria organizzazione potrebbero essere diversi da quelli dell'architettura descritta in questo articolo. Usare queste indicazioni come punto di partenza. 

### <a name="availability-and-scalability-recommendations"></a>Raccomandazioni per la disponibilità e la scalabilità

Un'opzione per la disponibilità e la scalabilità consiste nell'usare un [set di scalabilità di macchine virtuali][vmss]. I set di scalabilità di macchine virtuali consentono di distribuire e gestire un set di macchine virtuali identiche. I set di scalabilità supportano il ridimensionamento automatico in base alle metriche delle prestazioni. Di pari passo con l'aumento del carico sulle macchine virtuali, vengono aggiunte automaticamente altre macchine virtuali al servizio di bilanciamento del carico. Prendere in considerazione i set di scalabilità se è necessario aumentare rapidamente le istanze delle macchine virtuali o se si necessita del ridimensionamento automatico.

Per impostazione predefinita, i set di scalabilità usano il "provisioning eccessivo", ovvero inizialmente il set di scalabilità esegue il provisioning di un numero di macchine virtuali maggiore di quello richiesto e quindi elimina le macchine virtuali aggiuntive. Ciò migliora la percentuale di successo complessiva durante il provisioning delle macchine virtuali. Se non si usano [dischi gestiti](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-managed-disks), si consigliano non più di 20 macchine virtuali per account di archiviazione con provisioning eccessivo abilitato o non più di 40 macchine virtuali con provisioning eccessivo disabilitato.

Esistono due modi per configurare le macchine virtuali distribuite in un set di scalabilità:

- Usare le estensioni per configurare la macchina virtuale dopo il provisioning. Con questo approccio, le nuove istanze delle macchine virtuali possono richiedere più tempo per l'avvio di una macchina virtuale senza estensioni.

- Distribuire un [disco gestito](/azure/storage/storage-managed-disks-overview) con un'immagine del disco personalizzata. Questa opzione può risultare più veloce da distribuire. Tuttavia, richiede che l'immagine sia mantenuta aggiornata.

Per altre considerazioni, vedere [Considerazioni sulla progettazione per i set di scalabilità][vmss-design].

> [!TIP]
> Quando si usa una soluzione di ridimensionamento automatico, occorre testarla in anticipo con carichi di lavoro a livello di produzione.

Se non si usa un set di scalabilità, prendere in considerazione almeno l'uso di un set di disponibilità. Creare almeno due macchine virtuali nel set di disponibilità in modo da supportare il [contratto di servizio relativo alla disponibilità per le macchine virtuali di Azure][vm-sla]. Azure Load Balancer richiede inoltre che le macchine virtuali con carico bilanciato appartengano allo stesso set di disponibilità.

Ogni sottoscrizione di Azure ha limiti predefiniti, tra cui il numero massimo di macchine virtuali per area. È possibile aumentare il limite presentando una richiesta di supporto. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi][subscription-limits].

### <a name="network-recommendations"></a>Indicazioni per la rete

Posizionare le macchine virtuali all'interno della stessa subnet. Non esporre le macchine virtuali direttamente su Internet, ma assegnare un indirizzo IP privato a ogni macchina virtuale. I client si connettono usando l'indirizzo IP pubblico del servizio di bilanciamento del carico.

Se è necessario accedere alle macchine virtuali dietro al servizio di bilanciamento del carico, prendere in considerazione l'aggiunta di una singola macchina virtuale come bastion host/jumpbox con un indirizzo IP pubblico a cui è possibile effettuare l'accesso. Accedere quindi alle macchine virtuali dietro al servizio di bilanciamento del carico dal jumpbox. In alternativa, è possibile configurare le regole NAT in ingresso nel servizio di bilanciamento del carico per lo stesso scopo. Tuttavia, un jumpbox è la soluzione migliore quando si ospitano carichi di lavoro a più livelli o più carichi di lavoro.

### <a name="load-balancer-recommendations"></a>Raccomandazioni per il servizio di bilanciamento del carico

Aggiungere tutte le macchine virtuali nel set di disponibilità al pool di indirizzi back-end del servizio di bilanciamento del carico.

Definire regole di bilanciamento del carico per indirizzare il traffico di rete alle macchine virtuali. Ad esempio, per consentire il traffico HTTP, creare una regola che esegua il mapping della porta 80 dalla configurazione front-end alla porta 80 sul pool di indirizzi back-end. Quando un client invia una richiesta HTTP alla porta 80, il servizio di bilanciamento del carico consente di selezionare un indirizzo IP back-end usando un [algoritmo di hash][load balancer hashing] che include l'indirizzo IP di origine. In tal modo, le richieste client vengono distribuite tra tutte le macchine virtuali.

Per indirizzare il traffico a una macchina virtuale specifica, usare le regole NAT. Ad esempio, per abilitare RDP per le macchine virtuali, creare una regola NAT distinta per ogni macchina virtuale. Ogni regola dovrebbe eseguire il mapping di un numero di porta distinto alla porta 3389, la porta predefinita per RDP. Ad esempio, usare la porta 50001 per "VM1", la porta 50002 per "VM2" e così via. Assegnare le regole NAT alle schede di interfaccia di rete nelle macchine virtuali.

### <a name="storage-account-recommendations"></a>Raccomandazioni per gli account di archiviazione

Creare account di archiviazione di Azure distinti per ogni macchina virtuale per contenere i dischi rigidi virtuali, in modo da evitare di raggiungere i [limiti di operazioni di input/output al secondo][vm-disk-limits] per gli account di archiviazione.

Si consiglia l'uso di [dischi gestiti](/azure/storage/storage-managed-disks-overview) con [archiviazione Premium][premium]. I dischi gestiti non richiedono un account di archiviazione. Basta specificare le dimensioni e il tipo di disco per distribuirlo in modalità a disponibilità elevata.

Creare un account di archiviazione per i log di diagnostica. Questo account di archiviazione può essere condiviso da tutte le macchine virtuali. Può trattarsi di un account di archiviazione non gestito basato sull'uso di dischi standard.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Il set di disponibilità rende l'applicazione più resiliente agli eventi di manutenzione pianificata e non pianificata.

* La *manutenzione pianificata* si verifica quando Microsoft aggiorna la piattaforma sottostante, causando talvolta il riavvio delle macchine virtuali. Azure garantisce che le macchine virtuali in un set di disponibilità non siano tutte riavviate nello stesso momento. Almeno una viene mantenuta in esecuzione mentre le altre si riavviano.
* La *manutenzione non pianificata* ha luogo se si verifica un errore hardware. Azure garantisce che sia eseguito il provisioning delle macchine virtuali in un set di disponibilità tra più rack di server. Ciò consente di ridurre l'impatto di errori hardware, interruzioni di rete, interruzioni di alimentazione e così via.

Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali][availability set]. Il video seguente offre inoltre una valida panoramica dei set di disponibilità: [How Do I Configure an Availability Set to Scale VMs][availability set ch9] (Come configurare un set di disponibilità per ridimensionare le macchine virtuali).

> [!WARNING]
> Assicurarsi di configurare il set di disponibilità quando si esegue il provisioning della macchina virtuale. Attualmente, non è possibile aggiungere una macchina virtuale di Gestione risorse a un set di disponibilità dopo che è stato eseguito il provisioning della macchina virtuale.

Il servizio di bilanciamento del carico usa i [probe di integrità] per monitorare la disponibilità delle istanze delle macchine virtuali. Se un probe non riesce a raggiungere un'istanza entro il periodo di timeout, il bilanciamento del carico interrompe l'invio di traffico a quella macchina virtuale. Tuttavia, il servizio di bilanciamento del carico continuerà a eseguire la verifica e, se la macchina virtuale diventa nuovamente disponibile, il servizio di bilanciamento del carico riprenderà a inviare il traffico a quella macchina virtuale.

Ecco alcune raccomandazioni per i probe di integrità del servizio di bilanciamento del carico:

* I probe possono testare protocolli HTTP o TCP. Se le macchine virtuali eseguono un server HTTP, creare un probe HTTP. In caso contrario, creare un probe TCP.
* Per un probe HTTP, specificare il percorso di un endpoint HTTP. Il probe controlla una risposta HTTP 200 da questo percorso. Può trattarsi del percorso radice ("/") o di un endpoint di monitoraggio dell'integrità che implementa una logica personalizzata per controllare l'integrità dell'applicazione. L'endpoint deve consentire richieste HTTP anonime.
* Il probe viene inviato da un indirizzo IP [noto][health-probe-ip], 168.63.129.16. Assicurarsi di non bloccare il traffico da o verso questo indirizzo IP in eventuali criteri del firewall o regole del gruppo di sicurezza di rete.
* Usare i [log dei probe di integrità][health probe log] per visualizzare lo stato dei probe di integrità. Abilitare la registrazione nel portale di Azure per ogni servizio di bilanciamento del carico. I log vengono scritti in Archiviazione BLOB di Azure. Il log mostra il numero di macchine virtuali sul back-end che non ricevono traffico di rete a causa di risposte del probe non riuscite.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Con più macchine virtuali, è importante automatizzare i processi in modo che siano ripetibili e affidabili. È possibile usare [Automazione di Azure][azure-automation] per automatizzare la distribuzione, l'applicazione di patch del sistema operativo e altre attività.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Le reti virtuali sono un limite di isolamento del traffico in Azure. Le macchine virtuali in una rete virtuale possono comunicare direttamente con quelle in una rete virtuale diversa. Le macchine virtuali all'interno della stessa rete virtuale possono comunicare, a meno che non si creino [gruppi di sicurezza di rete][nsg] per limitare il traffico. Per altre informazioni, vedere [Servizi cloud Microsoft e sicurezza di rete][network-security].

Per il traffico Internet in ingresso, le regole di bilanciamento del carico definiscono il tipo di traffico che può raggiungere il back-end. Tuttavia, le regole di bilanciamento del carico non supportano gli elenchi di indirizzi IP attendibili, pertanto se si vogliono aggiungere determinati indirizzi IP pubblici a un elenco di indirizzi attendibili, aggiungere un gruppo di sicurezza di rete alla subnet.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una distribuzione di questa architettura è disponibile in [GitHub][github-folder]. Include una rete virtuale, un gruppo di sicurezza di rete e tre macchine virtuali in un set di scalabilità dietro al servizio di bilanciamento del carico, come descritto di seguito:

  * Una rete virtuale con una sola subnet denominata **Web** e usata come host delle macchine virtuali.
  * Un set di scalabilità di macchine virtuali contenente le macchine virtuali che eseguono la versione più recente di Ubuntu 16.04.3 LTS. Il ridimensionamento automatico è abilitato.
  * Un servizio di bilanciamento del carico posizionato davanti al set di scalabilità di macchine virtuali.
  * Un gruppo di sicurezza di rete con regole in ingresso per consentire il traffico HTTP al set di scalabilità di macchine virtuali.

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

1. Passare alla cartella `virtual-machines\multi-vm\parameters\linux` per il repository scaricato nel passaggio dei prerequisiti precedente.

2. Aprire il file `multi-vm-v2.json` e immettere un nome utente e una chiave SSH tra virgolette, come illustrato di seguito, quindi salvare il file.

  ```bash
  "adminUsername": "",
  "sshPublicKey": "",
  ```

3. Eseguire `azbb` per distribuire le macchine virtuali, come illustrato di seguito.

  ```bash
  azbb -s <subscription_id> -g <resource_group_name> -l <location> -p multi-vm-v2.json --deploy
  ```

Per altre informazioni sulla distribuzione di questa architettura di riferimento di esempio, visitare il [repository GitHub][git].

<!-- Links -->
[github-folder]: http://github.com/mspnp/reference-architectures/tree/master/virtual-machines/multi-vm
[ref-arch-repo]: https://github.com/mspnp/reference-architectures
[n-tier-linux]: ../virtual-machines-linux/n-tier.md
[n-tier-windows]: n-tier.md
[single vm]: single-vm.md
[premium]: /azure/storage/common/storage-premium-storage
[naming conventions]: /azure/guidance/guidance-naming-conventions
[vm-scaleset]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[availability set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[availability set ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azure-automation]: https://azure.microsoft.com/documentation/services/automation/
[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-automation]: /azure/automation/automation-intro
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/multi-vm
[health probe log]: /azure/load-balancer/load-balancer-monitor-log
[probe di integrità]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[health-probe-ip]: /azure/virtual-network/virtual-networks-nsg#special-rules
[servizio di bilanciamento del carico]: /azure/load-balancer/load-balancer-get-started-internet-arm-cli
[load balancer hashing]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[network-security]: /azure/best-practices-network-security
[nsg]: /azure/virtual-network/virtual-networks-nsg
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview 
[Runbook Gallery]: /azure/automation/automation-runbook-gallery#runbooks-in-runbook-gallery
[subscription-limits]: /azure/azure-subscription-service-limits
[visio-download]: https://archcenter.azureedge.net/cdn/vm-reference-architectures.vsdx
[vm-disk-limits]: /azure/azure-subscription-service-limits#virtual-machine-disk-limits
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/
[vmss]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[vmss-design]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[0]: ./images/multi-vm-diagram.png "Architettura di una soluzione di più macchine virtuali in Azure che comprende un set di disponibilità con due macchine virtuali e un servizio di bilanciamento del carico"
[azure-cli-2]: /azure/install-azure-cli?view=azure-cli-latest
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Template-Building-Blocks-Version-2-(Linux)
[git]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/multi-vm