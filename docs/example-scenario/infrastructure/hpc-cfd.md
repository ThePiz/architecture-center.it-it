---
title: Esecuzione di simulazioni CFD
titleSuffix: Azure Example Scenarios
description: Eseguire simulazioni di fluidodinamica computazionale (CFD) in Azure.
author: mikewarr
ms.date: 09/20/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack
social_image_url: /azure/architecture/example-scenario/infrastructure/media/architecture-hpc-cfd.png
ms.openlocfilehash: 6972b701a608351104c23459bc2c38029a5c8d3d
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249586"
---
# <a name="running-computational-fluid-dynamics-cfd-simulations-on-azure"></a>Esecuzione di simulazioni di fluidodinamica computazionale (CFD) in Azure

Le simulazioni di fluidodinamica computazionale (CFD) richiedono un notevole tempo di calcolo e hardware specializzato. Man mano che aumenta l'utilizzo dei cluster, crescono i tempi di simulazione e l'uso complessivo della griglia, causando problemi in termini di capacità di riserva e tempi di attesa. L'aggiunta di hardware fisico può risultare costosa e non allineata alle fluttuazioni nell'utilizzo affrontate in un'azienda. Grazie all'uso di Azure, molti di questi problemi possono essere risolti senza investimenti di capitale.

Azure fornisce l'hardware necessario per eseguire i processi CFD in macchine virtuali CPU e GPU. Le dimensioni di VM abilitate per l'accesso diretto a memoria remota (RDMA) usano la rete InfiniBand FDR, che fornisce comunicazione MPI (Message Passing Interface) a bassa latenza. In combinazione con Avere vFXT, che fornisce un file system in cluster su scala aziendale, consente ai clienti di garantire la massima velocità effettiva per le operazioni di lettura in Azure.

Per semplificare la creazione, la gestione e l'ottimizzazione di cluster HPC, è possibile usare Azure CycleCloud per effettuare il provisioning dei cluster e orchestrare i dati in scenari ibridi e cloud. Monitorando i processi in sospeso, CycleCloud avvierà automaticamente il calcolo su richiesta, in cui si paga solo per le risorse usate, connesso all'utilità di pianificazione del carico di lavoro scelta.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri settori pertinenti per le applicazioni CFD includono:

- Aeronautica
- Automobilistico
- Sistemi HVAC di edifici
- Petrolio e gas
- Scienze biologiche

## <a name="architecture"></a>Architettura

![Diagramma dell'architettura][architecture]

Questo diagramma mostra una panoramica generale di una progettazione ibrida tipica che fornisce il monitoraggio dei processi dei nodi su richiesta in Azure:

1. Connettersi al server Azure CycleCloud per configurare il cluster.
2. Configurare e creare il nodo head del cluster, usando macchine virtuali abilitate per RDMA per la comunicazione MPI.
3. Aggiungere e configurare il nodo head locale.
4. Se le risorse sono insufficienti, Azure CycleCloud aumenterà (o ridurrà) le risorse di calcolo in Azure. È possibile definire un limite predeterminato per evitare la sovrassegnazione.
5. Attività allocate ai nodi di esecuzione.
6. Dati memorizzati nella cache di Azure dal server NFS locale.
7. Dati letti dalla cache Avere vFXT per Azure.
8. Informazioni di attività e processi inoltrate al server Azure CycleCloud.

### <a name="components"></a>Componenti

- [Azure CycleCloud][cyclecloud], uno strumento per la creazione, la gestione, l'uso e l'ottimizzazione di cluster HPC e Big Compute in Azure.
- [Avere vFXT per Azure][avere] viene usato per fornire un file system in cluster su scala aziendale creato per il cloud.
- Le [macchine virtuali di Azure][vms] vengono usate per creare un set statico di istanze di calcolo.
- I [set di scalabilità di macchine virtuali][vmss] forniscono un gruppo di macchine virtuali identiche di cui è possibile aumentare o ridurre le prestazioni tramite Azure CycleCloud.
- Gli [account di archiviazione di Azure](/azure/storage/common/storage-introduction) vengono usati per la sincronizzazione e la conservazione dei dati.
- Le [reti virtuali](/azure/virtual-network/virtual-networks-overview) consentono a diversi tipi di risorse di Azure, ad esempio macchine virtuali di Azure, di comunicare in modo sicuro tra loro, con Internet e con le reti locali.

### <a name="alternatives"></a>Alternative

I clienti possono anche usare Azure CycleCloud per creare una griglia interamente in Azure. In questa configurazione, il server Azure CycleCloud viene eseguito all'interno della sottoscrizione di Azure.

Per un approccio moderno alle applicazioni, in cui non è necessaria la gestione di un'utilità di pianificazione del carico di lavoro, [Azure Batch][batch] può essere di aiuto. Azure Batch può eseguire in modo efficiente applicazioni parallele e HPC (High Performance Computing) su larga scala nel cloud. Azure Batch consente di definire le risorse di calcolo di Azure per eseguire le applicazioni in parallelo o in scala, senza necessità di configurare o gestire manualmente l'infrastruttura. Azure Batch pianifica le attività a elevato utilizzo di calcolo e aggiunge o rimuove dinamicamente le risorse in base alle esigenze.

### <a name="scalability-and-security"></a>Scalabilità e sicurezza

Il ridimensionamento dei nodi di esecuzione in Azure CycleCloud può essere eseguito manualmente o tramite la scalabilità automatica. Per altre informazioni, vedere l'articolo sulla [scalabilità automatica in CycleCloud][cycle-scale].

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

## <a name="deploy-the-scenario"></a>Distribuire lo scenario

### <a name="prerequisites"></a>Prerequisiti

Seguire questi passaggi prima di distribuire il modello di Resource Manager:

1. Creare un'[entità servizio][cycle-svcprin] per recuperare appId, displayName, nome, password e tenant.
2. Generare una [coppia di chiavi SSH][cycle-ssh] per accedere in modo sicuro al server CycleCloud.

    <!-- markdownlint-disable MD033 -->

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FCycleCloudCommunity%2Fcyclecloud_arm%2Fmaster%2Fazuredeploy.json" target="_blank">
        <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

    <!-- markdownlint-enable MD033 -->

3. [Accedere al server CycleCloud][cycle-login] per configurare e creare un nuovo cluster.
4. [Creare un cluster][cycle-create].

La cache Avere è una soluzione opzionale che può aumentare notevolmente la velocità effettiva di lettura per i dati del processo dell'applicazione. Avere vFXT per Azure risolve il problema di eseguire queste applicazioni aziendali HPC nel cloud e allo stesso tempo usare i dati archiviati in locale o nell'archiviazione BLOB di Azure.

Per le organizzazioni che stanno pianificando un'infrastruttura ibrida, con archiviazione locale e cloud computing, le applicazioni HPC possono eseguire il burst in Azure usando i dati archiviati in dispositivi NAS e attivare CPU virtuali in base alle esigenze. Il set di dati non viene mai spostato completamente nel cloud. I byte richiesti vengono temporaneamente memorizzati nella cache usando un cluster Avere durante l'elaborazione.

Per impostare e configurare un'installazione di Avere vFXT, seguire le indicazioni della [guida alla configurazione e installazione di Avere][avere].

## <a name="pricing"></a>Prezzi

Il costo dell'esecuzione di un'implementazione HPC tramite un server CycleCloud varia in base a diversi fattori. Ad esempio, CycleCloud viene addebitato in base alla quantità di tempo di calcolo usato, con il server master e il server CycleCloud in genere costantemente allocati e in esecuzione. Il costo dei nodi di esecuzione dipenderà dalle relative dimensioni e dal tempo per cui sono operativi. Si applicano anche i normali costi di Azure per l'archiviazione e la rete.

Questo scenario illustra come è possibile eseguire applicazioni CFD in Azure. Le macchine virtuali richiederanno funzionalità RDMA, disponibili solo in macchine virtuali di dimensioni specifiche. Di seguito sono riportati esempi dei costi che potrebbero essere addebitati per un set di scalabilità allocato in modo continuativo per otto ore al giorno per un mese, con dati in uscita per 1 TB. Include anche i costi per il server Azure CycleCloud e l'installazione di Avere vFXT per Azure:

- Area: Europa settentrionale
- Server Azure CycleCloud: 1 x D3 Standard (4 x CPU, 14 GB di memoria, HDD Standard da 32 GB)
- Server master CycleCloud Azure: 1 x D12 Standard (4 x CPU, 28 GB di memoria, HDD Standard da 32 GB)
- Matrice nodo Azure CycleCloud: 10 x H16r Standard (16 x CPU, 112 GB di memoria)
- Avere vFXT in Cluster di Azure: 3 x D16s v3 (200 GB sistema operativo, disco dati da 1 TB SSD Premium)
- Dati in uscita: 1 TB

Vedere questa [stima dei prezzi][pricing] per l'hardware elencato in precedenza.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito l'esempio, leggere altre informazioni su [Azure CycleCloud][cyclecloud].

## <a name="related-resources"></a>Risorse correlate

- [Istanze di macchine virtuali con supporto per RDMA][rdma]
- [Personalizzazione di un'istanza di macchina virtuale con supporto per RDMA][rdma-custom]

<!-- links -->
[architecture]: ./media/architecture-hpc-cfd.png
[calculator]: https://azure.com/e/
[availability]: /azure/architecture/checklist/availability
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[vmss]: /azure/virtual-machine-scale-sets/overview
[cyclecloud]: /azure/cyclecloud/
[rdma]: /azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances
[gpu]: /azure/virtual-machines/windows/sizes-gpu
[hpcsizes]: /azure/virtual-machines/windows/sizes-hpc
[vms]: /azure/virtual-machines/
[low-pri]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority
[batch]: /azure/batch/
[avere]: https://github.com/Azure/Avere/blob/master/README.md
[cycle-prereq]: /azure/cyclecloud/quickstart-install-cyclecloud#prerequisites
[cycle-svcprin]: /azure/cyclecloud/quickstart-install-cyclecloud#service-principal
[cycle-ssh]: /azure/cyclecloud/quickstart-install-cyclecloud#ssh-keypair
[cycle-login]: /azure/cyclecloud/quickstart-install-cyclecloud#log-into-the-cyclecloud-application-server
[cycle-create]: /azure/cyclecloud/quickstart-create-and-run-cluster
[rdma]: /azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances
[rdma-custom]: /azure/virtual-machines/linux/classic/rdma-cluster#customize-the-vm
[pricing]: https://azure.com/e/53030a04a2ab47a289156e2377a4247a
[cycle-scale]: /azure/cyclecloud/autoscale
