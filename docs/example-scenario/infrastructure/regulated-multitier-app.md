---
title: Applicazione Web Windows sicura per i settori regolamentati
description: Scenario collaudato per la creazione di un'applicazione Web multilivello sicura con Windows Server in Azure che usa set di scalabilità, gateway applicazione e servizi di bilanciamento del carico.
author: iainfoulds
ms.date: 07/11/2018
ms.openlocfilehash: 3572f215d9134a6650d76e1b14458226334c6f42
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389285"
---
# <a name="secure-windows-web-application-for-regulated-industries"></a>Applicazione Web Windows sicura per i settori regolamentati

Questo scenario di esempio è applicabile ai settori regolamentati che devono proteggere le applicazioni multilivello. In questo scenario, un'applicazione ASP.NET front-end si connette in modo sicuro a un cluster Microsoft SQL Server di back-end protetto.

Esempi di scenari di applicazioni includono esecuzione di applicazioni per la sala operatoria, gestione di appuntamenti e cartelle cliniche dei pazienti oppure rinnovo di prescrizioni e ordini di farmaci. In passato, le organizzazioni dovevano gestire applicazioni e servizi legacy locali per questi scenari. Con una modalità sicura e scalabile per distribuire queste applicazioni Windows Server in Azure, le organizzazioni possono modernizzare le proprie distribuzioni e ridurre i costi operativi e le spese di gestione locali.

## <a name="related-use-cases"></a>Casi d'uso correlati

Prendere in considerazione questo scenario per i casi d'uso seguenti:

* Modernizzazione delle distribuzioni di applicazioni in un ambiente cloud sicuro.
* Minore gestione di applicazioni e servizi legacy locali.
* Miglioramento dell'assistenza e dell'esperienza dei pazienti con nuove piattaforme applicative.

## <a name="architecture"></a>Architettura

![Panoramica dell'architettura dei componenti di Azure usati nell'applicazione Windows Server multilivello per i settori regolamentati][architecture]

Questo scenario descrive un'applicazione multilivello per settori regolamentati che usa ASP.NET e Microsoft SQL Server. Il flusso dei dati nello scenario avviene come segue:

1. Gli utenti accedono all'applicazione front-end ASP.NET del settore regolamentato tramite un gateway applicazione di Azure.
2. Il gateway applicazione distribuisce il traffico alle istanze di macchina virtuale all'interno di un set di scalabilità di macchine virtuali di Azure.
3. L'applicazione ASP.NET si connette al cluster Microsoft SQL Server in un livello back-end tramite un servizio di bilanciamento del carico di Azure. Queste istanze di SQL Server di back-end si trovano in una rete virtuale di Azure separata, protetta da regole dei gruppi di sicurezza di rete che limitano il flusso di traffico.
4. Il servizio di bilanciamento del carico distribuisce il traffico di SQL Server alle istanze di macchina virtuale in un altro set di scalabilità di macchine virtuali.
5. Archiviazione BLOB di Azure funge da cloud di controllo per il cluster SQL Server nel livello back-end.  La connessione dall'interno della rete virtuale è abilitata con un endpoint di servizio di rete virtuale per Archiviazione di Azure.

### <a name="components"></a>Componenti

* Il [gateway applicazione di Azure][appgateway-docs] è un servizio di bilanciamento del carico del traffico Web di livello 7, con riconoscimento delle applicazioni e in grado di distribuire il traffico in base a regole di routing specifiche. Il gateway applicazione può anche gestire l'offload SSL per migliorare le prestazioni del server Web.
* La [rete virtuale di Azure][vnet-docs] consente a risorse quali le macchine virtuali di comunicare in modo sicuro tra loro, con Internet e con le reti locali. Le reti virtuali forniscono isolamento e segmentazione, filtrano e instradano il traffico e consentono la connessione tra posizioni. Questo scenario usa due reti virtuali combinate con i gruppi di sicurezza di rete appropriati per fornire una [rete perimetrale][dmz] e l'isolamento dei componenti dell'applicazione. Il peering di rete virtuale collega le due reti.
* I [set di scalabilità di macchine virtuali di Azure][scaleset-docs] consentono di creare e gestire un gruppo di macchine virtuali con bilanciamento del carico identiche. Il numero di istanze di macchine virtuali può aumentare o diminuire automaticamente in risposta alla domanda o a una pianificazione definita. Questo scenario usa due set di scalabilità di macchine virtuali separati, uno per le istanze dell'applicazione ASP.NET di front-end e uno per le istanze di macchina virtuale del cluster SQL Server di back-end. È possibile usare la configurazione dello stato desiderato di PowerShell o l'estensione di script personalizzati di Azure per fornire alle istanze di macchina virtuale il software e le impostazioni di configurazione necessari.
* I [gruppi di sicurezza di rete][nsg-docs] contengono un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in ingresso o in uscita in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo. Le reti virtuali in questo scenario sono protette da regole dei gruppi di sicurezza di rete che limitano il flusso del traffico tra i componenti dell'applicazione.
* [Azure Load Balancer][loadbalancer-docs] distribuisce il traffico in ingresso in base a regole e probe di integrità. Un servizio di bilanciamento del carico offre bassa latenza e velocità effettiva elevata, oltre a una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP. Questo scenario usa un servizio di bilanciamento del carico interno per distribuire il traffico dal livello dell'applicazione front-end al cluster SQL Server di back-end.
* [Archiviazione BLOB di Azure][cloudwitness-docs] funge da posizione di cloud di controllo per il cluster SQL Server. Questo controllo viene usato per operazioni e decisioni di cluster che richiedono un voto aggiuntivo per stabilire il quorum. L'uso del cloud di controllo elimina la necessità di una VM aggiuntiva che funga da controllo di condivisione file tradizionale.

### <a name="alternatives"></a>Alternative

* *Unix e Windows possono essere sostituiti facilmente con una serie di altri sistemi operativi, perché l'infrastruttura non dipende dai sistemi operativi.

* [SQL Server per Linux][sql-linux] può sostituire l'archivio dati di back-end.

* [Cosmos DB][cosmos] rappresenta un'altra alternativa per l'archivio dati.

## <a name="considerations"></a>Considerazioni

### <a name="availability"></a>Disponibilità

Le istanze di macchina virtuale in questo scenario vengono distribuite tra zone di disponibilità. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Sono disponibili almeno tre zone in tutte le aree abilitate. Questa distribuzione di istanze di macchina virtuale tra zone garantisce la disponibilità elevata ai livelli dell'applicazione. Per altre informazioni, vedere [Informazioni sulle zone di disponibilità di Azure][azureaz-docs]

Il livello di database può essere configurato per l'uso dei gruppi di disponibilità AlwaysOn. Con questa configurazione di SQL Server, un database primario in un cluster viene configurato con un massimo di otto database secondari. Se si verifica un problema con il database primario, il cluster esegue il failover a uno dei database secondari e l'applicazione continua a essere disponibile. Per altre informazioni, vedere [Panoramica di Gruppi di disponibilità AlwaysOn (SQL Server)][sqlalwayson-docs].

Per altri argomenti relativi alla disponibilità, vedere l'[elenco di controllo per la disponibilità][availability] in Centro architetture Azure.

### <a name="scalability"></a>Scalabilità

Questo scenario usa i set di scalabilità di macchine virtuali per i componenti front-end e back-end. Con i set di scalabilità, il numero di istanze di macchina virtuale che eseguono il livello dell'applicazione front-end può essere ridimensionato automaticamente in base alle richieste dei clienti o a una pianificazione definita. Per altre informazioni, vedere [Panoramica della scalabilità automatica con i set di scalabilità di macchine virtuali di Azure][vmssautoscale-docs].

Per altri argomenti relativi alla scalabilità, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Sicurezza

Tutto il traffico di rete virtuale nel livello dell'applicazione front-end è protetto da gruppi di sicurezza di rete. Le regole limitano il flusso del traffico in modo che solo le istanze di macchina virtuale a livello di applicazione front-end possano accedere al livello di database di backend. Non è consentito il traffico Internet in uscita dal livello di database. Per ridurre la superficie di attacco, non sono aperte porte di gestione remota diretta. Per altre informazioni, vedere [Gruppi di sicurezza di rete di Azure][nsg-docs].

Per istruzioni sulla distribuzione di PCI DSS (Payment Card Industry Data Security Standards) 3.2, vedere l'[infrastruttura conforme][pci-dss]. Per indicazioni generali sulla progettazione di scenari sicuri, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

Insieme alle zone di disponibilità e ai set di scalabilità di macchine virtuali, questo scenario usa il gateway applicazione di Azure e Azure Load Balancer. Questi due componenti di rete distribuiscono il traffico alle istanze di macchina virtuale connesse e includono probe di integrità che garantiscono che il traffico venga distribuito solo alle macchine virtuali integre. Due istanze del gateway applicazione hanno una configurazione attiva-passiva e viene usato un servizio di bilanciamento del carico con ridondanza della zona. Questa configurazione rende le risorse di rete e le applicazioni resilienti rispetto a problemi che potrebbero altrimenti interrompere il traffico e compromettere l'accesso degli utenti finali.

Per indicazioni generali sulla progettazione di scenari resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].

## <a name="deploy-the-scenario"></a>Distribuire lo scenario

**Prerequisiti**

* È necessario un account Azure esistente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Per distribuire un cluster SQL Server nel set di scalabilità di back-end, è necessario un dominio di Azure Active Directory Domain Services.

Per distribuire l'infrastruttura di base per questo scenario con un modello di Azure Resource Manager, seguire questa procedura.

1. Selezionare il pulsante **Distribuisci in Azure**:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Finfrastructure%2Fregulated-multitier-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. Attendere l'apertura della distribuzione del modello nel portale di Azure e quindi completare la procedura seguente:
   * Scegliere **Crea nuovo** per creare un nuovo gruppo di risorse e quindi specificare un nome nella casella di testo, ad esempio *myWindowsscenario*.
   * Selezionare un'area nella casella a discesa **Località**.
   * Specificare un nome utente e una password sicura per le istanze di set di scalabilità di macchine virtuali.
   * Esaminare le condizioni e quindi selezionare **Accetto le condizioni riportate sopra**.
   * Selezionare il pulsante **Acquista**.

Il completamento della distribuzione può richiedere 15-20 minuti.

## <a name="pricing"></a>Prezzi

Per esaminare il costo di esecuzione dello scenario, nel calcolatore dei costi sono preconfigurati tutti i servizi.  Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base

Sono disponibili tre profili di costo di esempio, basati sul numero di istanze di macchina virtuale del set di scalabilità che eseguono le applicazioni.

* [Small][small-pricing]: in questo esempio di prezzi vengono correlate due istanze di macchina virtuale front-end e due istanze di macchina virtuale back-end.
* [Medium][medium-pricing]: in questo esempio di prezzi vengono correlate 20 istanze di macchina virtuale front-end e 5 istanze di macchina virtuale back-end.
* [Large][large-pricing]: in questo esempio di prezzi vengono correlate 100 istanze di macchina virtuale front-end e 10 istanze di macchina virtuale back-end.

## <a name="related-resources"></a>Risorse correlate

In questo scenario viene usato un set di scalabilità di macchine virtuali back-end che esegue un cluster Microsoft SQL Server. Azure Cosmos DB può anche essere usato come livello di database scalabile e sicuro per i dati dell'applicazione. Un [endpoint del servizio di rete virtuale][vnetendpoint-docs] consente di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. In questo scenario, gli endpoint della rete virtuale consentono di proteggere il traffico tra il livello dell'applicazione front-end e Cosmos DB. Per altre informazioni su Cosmos DB, vedere [Panoramica di Azure Cosmos DB][azurecosmosdb-docs].

È anche disponibile l'[architettura di riferimento completa per un'applicazione generica a più livelli con SQL Server][ntiersql-ra].

<!-- links -->
[appgateway-docs]: /azure/application-gateway/overview
[architecture]: ./media/regulated-multitier-app/architecture-regulated-multitier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: /architecture/checklist/availability
[azureaz-docs]: /azure/availability-zones/az-overview
[azurecosmosdb-docs]: /azure/cosmos-db/introduction
[cloudwitness-docs]: /windows-server/failover-clustering/deploy-cloud-witness
[loadbalancer-docs]: /azure/load-balancer/load-balancer-overview
[nsg-docs]: /azure/virtual-network/security-overview
[ntiersql-ra]: /azure/architecture/reference-architectures/n-tier/n-tier-sql-server
[resiliency]: /azure/architecture/resiliency/ 
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability 
[scaleset-docs]: /azure/virtual-machine-scale-sets/overview
[sqlalwayson-docs]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[vmssautoscale-docs]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[vnet-docs]: /azure/virtual-network/virtual-networks-overview
[vnetendpoint-docs]: /azure/virtual-network/virtual-network-service-endpoints-overview
[pci-dss]: /azure/security/blueprints/pcidss-iaaswa-overview
[dmz]: /azure/virtual-network/virtual-networks-dmz-nsg
[cosmos]: /azure/cosmos-db/
[sql-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017

[small-pricing]: https://azure.com/e/711bbfcbbc884ef8aa91cdf0f2caff72
[medium-pricing]: https://azure.com/e/b622d82d79b34b8398c4bce35477856f
[large-pricing]: https://azure.com/e/1d99d8b92f90496787abecffa1473a93