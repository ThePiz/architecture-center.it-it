---
title: Un servizio CAE (Computer-Aided Engineering)
titleSuffix: Azure Example Scenarios
description: Fornire una piattaforma software come un servizio (SaaS) per CAE (Computer-Aided Engineering) in Azure.
author: alexbuckgit
ms.date: 08/22/2018
ms.openlocfilehash: 0e8ce29639e4e189acef633585191b178c6c8721
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643867"
---
# <a name="a-computer-aided-engineering-service-on-azure"></a>Un servizio CAE (Computer-Aided Engineering) in Azure

Questo scenario di esempio illustra l'implementazione di una piattaforma software come un servizio (SaaS) basata sulle funzionalità HPC di Azure. Questo scenario si basa su una soluzione software di ingegneria. Tuttavia, l'architettura è pertinente per altri settori che richiedono risorse HPC, ad esempio rendering delle immagini, modellazione complessa e calcolo dei rischi finanziari.

Questo esempio illustra un provider di software di ingegneria che fornisce applicazioni CAE (Computer-Aided Engineering) a studi di ingegneria e aziende manifatturiere. Le soluzioni CAE favoriscono l'innovazione, riducono i tempi di sviluppo e abbassano i costi per l'intera durata della progettazione di un prodotto. Queste soluzioni richiedono notevoli risorse di calcolo e spesso elaborano volumi di dati elevati. I costi elevati di un'appliance HPC locale o delle workstation di fascia alta spesso pongono queste tecnologie fuori dalla portata di piccole società di ingegneria, imprenditori e studenti.

L'azienda vuole ampliare il mercato delle proprie applicazioni creando una piattaforma SaaS supportata da tecnologie HPC basate sul cloud. I clienti devono poter pagare le risorse di calcolo in base alle esigenze e accedere a un'enorme potenza di calcolo che altrimenti sarebbe economicamente insostenibile.

Gli obiettivi dell'azienda includono:

- Sfruttare i vantaggi offerti dalle funzionalità HPC in Azure per accelerare il processo di progettazione e testing dei prodotti.
- Usare le più recenti innovazioni hardware per eseguire simulazioni complesse, riducendo al minimo i costi per le simulazioni più semplici.
- Abilitare la visualizzazione e il rendering realistici in un Web browser senza bisogno di una workstation di ingegneria di fascia alta.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

- Ricerca nel campo della genomica
- Simulazioni meteo
- Applicazioni di chimica computazionale

## <a name="architecture"></a>Architettura

![Architettura di una soluzione SaaS con funzionalità HPC][architecture]

- Gli utenti possono accedere a macchine virtuali della serie NV tramite un Web browser con connessione RDP basata su HTML5 usando il [servizio Apache Guacamole](https://guacamole.apache.org/). Queste istanze di macchine virtuali forniscono GPU potenti per le attività di rendering e di collaborazione. Gli utenti possono modificare i progetti e visualizzare i risultati senza bisogno di portatili o dispositivi di elaborazione di fascia alta. L'utilità di pianificazione attiva altre macchine virtuali in base all'euristica definita dall'utente.
- Da una sessione CAD desktop, gli utenti possono inviare i carichi di lavoro per l'esecuzione nei nodi disponibili del cluster HPC. Questi carichi di lavoro eseguono attività come analisi delle sollecitazioni o calcoli di fluidodinamica computazionale, eliminando la necessità di cluster di calcolo locali dedicati. Questi nodi del cluster possono essere configurati per la scalabilità automatica in base al carico o alla profondità della coda a seconda della domanda di risorse di calcolo degli utenti attivi.
- Per ospitare le risorse Web disponibili per gli utenti finali viene usato il servizio Kubernetes di Azure (AKS).

### <a name="components"></a>Componenti

- [Le macchine virtuali serie H](/azure/virtual-machines/linux/sizes-hpc) vengono usate per eseguire simulazioni a elevato utilizzo di calcolo, come modellistica molecolare e fluidodinamica computazionale. La soluzione sfrutta anche tecnologie come connettività ad accesso diretto a memoria remota (RDMA) e rete InfiniBand.
- [Le macchine virtuali serie NV](/azure/virtual-machines/windows/sizes-gpu) offrono agli ingegneri le funzionalità di una workstation di fascia alta in un Web browser standard. Queste macchine virtuali hanno GPU NVIDIA Tesla M60 che supportano il rendering avanzato e possono eseguire carichi di lavoro con precisione singola.
- [Le macchine virtuali per utilizzo generico](/azure/virtual-machines/linux/sizes-general) che eseguono CentOS gestiscono i carichi di lavoro più tradizionali, ad esempio le applicazioni Web.
- [Il gateway applicazione](/azure/application-gateway/overview) bilancia il carico delle richieste in entrata nei server Web.
- [Il servizio Kubernetes di Azure (AKS)](/azure/aks/intro-kubernetes) viene usato per eseguire i carichi di lavoro scalabili a un costo inferiore per le simulazioni che non richiedono le funzionalità di fascia alta di macchine virtuali HPC o GPU.
- [La suite Altair PBS Works ](https://www.pbsworks.com/PBSProduct.aspx?n=PBS-Works-Suite&c=Overview-and-Capabilities) orchestra il flusso di lavoro HPC, assicurando che siano disponibili istanze di macchine virtuali sufficienti per gestire il carico corrente. Inoltre, dealloca le macchine virtuali quando la richiesta è inferiore per ridurre i costi.
- [L'archiviazione BLOB](/azure/storage/blobs/storage-blobs-introduction) archivia i file che supportano i processi pianificati.

### <a name="alternatives"></a>Alternative

- [Azure CycleCloud](/azure/cyclecloud/overview) semplifica la creazione, la gestione, il funzionamento e l'ottimizzazione dei cluster HPC. Offre funzionalità avanzate per criteri e governance. CycleCloud supporta qualsiasi utilità di pianificazione di processi o stack software.
- [HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options) può creare e gestire un cluster HPC di Azure per carichi di lavoro basati su Windows Server. HPC Pack non può essere usato per i carichi di lavoro basati su Linux.
- [Automation DSC per Azure](/azure/automation/automation-dsc-overview) fornisce un approccio di infrastruttura come codice alla definizione delle macchine virtuali e del software da distribuire. Le macchine virtuali possono essere distribuite come parte di un set di scalabilità di macchine virtuali, con regole di scalabilità automatica per i nodi di calcolo in base al numero di processi inviati alla coda processi. Quando è necessaria una nuova macchina virtuale, il provisioning viene eseguito usando l'immagine con patch più recente dalla raccolta immagini di Azure, quindi il software richiesto viene installato e configurato tramite uno script di configurazione DSC di PowerShell.
- [Funzioni di Azure](/azure/azure-functions/functions-overview)

## <a name="considerations"></a>Considerazioni

- Anche se l'uso di un approccio di infrastruttura come codice rappresenta un'ottima soluzione per gestire le definizioni di compilazione delle macchine virtuali, eseguire il provisioning di una nuova macchina virtuale mediante uno script può richiedere molto tempo. Questa soluzione ha trovato una buona via di mezzo usando lo script DSC per la creazione periodica di un'immagine master, che quindi può essere usata per eseguire il provisioning di una nuova macchina virtuale più velocemente rispetto alla creazione completa di una macchina virtuale su richiesta tramite DSC. Azure DevOps Services o altri strumenti CI/CD possono aggiornare periodicamente le immagini master usando script DSC.
- Bilanciare i costi complessivi della soluzione con la disponibilità rapida di risorse di calcolo è un fattore essenziale. Eseguire il provisioning di un pool di istanze di macchine virtuali serie N e porle in stato deallocato riduce i costi operativi. Quando è necessaria una macchina virtuale aggiuntiva, la riallocazione di un'istanza esistente comporterà l'attivazione della macchina virtuale in un host diverso, ma il tempo di rilevamento del bus PCI necessario al sistema operativo per identificare e installare i driver della GPU viene azzerato, perché al riavvio un macchina virtuale di cui viene effettuato il deprovisioning e quindi di nuovo il provisioning manterrà lo stesso bus PCI per la GPU.
- L'architettura originale si basava interamente su macchine virtuali di Azure per l'esecuzione delle simulazioni. Per ridurre i costi dei carichi di lavoro che non richiedevano tutte le funzionalità di una macchina virtuale, questi carichi di lavoro venivano inseriti in contenitori e distribuiti nel servizio Kubernetes di Azure (AKS).
- Il personale dell'azienda aveva competenze esistenti in tecnologie open source. Queste competenze sono state sfruttate usando tecnologie come Linux e Kubernetes.

## <a name="pricing"></a>Prezzi

Per semplificare il calcolo del costo di esecuzione dello scenario, molti dei servizi necessari sono stati preconfigurati in un [esempio di calcolatore dei costi][calculator]. I costi della soluzione dipendono dal numero e dalla scala dei servizi necessari per soddisfare i propri requisiti.

Le considerazioni seguenti determineranno una parte sostanziale dei costi per questa soluzione:

- I costi delle macchine virtuali di Azure aumentano in modo lineare man mano che viene effettuato il provisioning di istanze aggiuntive. Per le macchine virtuali deallocate verranno addebitati solo i costi di archiviazione e non i costi di calcolo. Queste macchine virtuali deallocate potranno essere riallocate quando la richiesta è elevata.
- I costi del servizio Kubernetes di Azure sono basati sul tipo di macchina virtuale scelto per supportare il carico di lavoro. I costi aumenteranno in modo lineare in base al numero di macchine virtuali nel cluster.

## <a name="next-steps"></a>Passaggi successivi

- Leggere la [storia del cliente Altair][source-document]. Questo scenario di esempio si basa su una versione della sua architettura.
- Esaminare le altre [soluzioni Big Compute](https://azure.microsoft.com/solutions/big-compute) disponibili in Azure.

<!-- links -->
[architecture]: ./media/architecture-hpc-saas.png
[source-document]: https://customers.microsoft.com/story/altair-manufacturing-azure
[calculator]: https://azure.com/e/3cb9ccdc893f41ffbcdb00c328178ccf
