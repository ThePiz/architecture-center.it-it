---
title: Panoramica delle opzioni di calcolo di Azure
description: Panoramica delle opzioni di calcolo di Azure
author: MikeWasson
ms.date: 06/13/2018
ms.openlocfilehash: cb59c4472b183d9a14031497f0b6db673938c9a9
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818820"
---
# <a name="overview-of-azure-compute-options"></a>Panoramica delle opzioni di calcolo di Azure

Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione. 

## <a name="overview"></a>Panoramica

A un'estremità dello spettro si colloca l'**infrastruttura distribuita come servizio** (IaaS). Con IaaS, si esegue il provisioning delle macchine virtuali necessarie, oltre che dei componenti di archiviazione e di rete associati. In seguito, si distribuiscono il software e le applicazioni desiderate sulle macchine virtuali. Questo modello è il più simile a un ambiente locale tradizionale, con la differenza che l'infrastruttura viene gestita da Microsoft. È comunque possibile gestire le singole macchine virtuali.  

Il modello di **piattaforma distribuita come servizio** (PaaS, Platform-as-a-Service) offre un ambiente di hosting gestito, in cui è possibile distribuire l'applicazione senza bisogno di gestire le macchine virtuali o le risorse di rete. Ad esempio, anziché creare singole macchine virtuali, si specifica un numero di istanze e il servizio eseguirà il provisioning, la configurazione e la gestione delle risorse necessarie. Servizio app di Azure è un esempio di servizio PaaS.

Da IaaS al PaaS puro esiste una gamma di possibilità. Ad esempio, le VM di Azure supportano il ridimensionamento automatico tramite set di scalabilità di macchine virtuali di Microsoft Azure. Il ridimensionamento automatico non è un'esclusiva di PaaS, ma è il tipo di funzionalità di gestione che potrebbe essere disponibile in un servizio PaaS.

Il modello di **funzioni distribuite come servizio** (FaaS, Functions-as-a-Service) si spinge ancora oltre rispetto all'eliminazione della necessità di gestire l'ambiente di hosting. Anziché creare istanze di calcolo e distribuire codice alle istanze, è sufficiente distribuire il codice e il servizio lo eseguirà automaticamente. Non è necessario amministrare le risorse di calcolo. Questi servizi usano l'architettura senza server e aumentano o riducono automaticamente le prestazioni al livello necessario per gestire il traffico. Funzioni di Azure è un servizio FaaS.

IaaS offre il massimo in termini di controllo, flessibilità e portabilità. FaaS offre semplicità, scalabilità elastica e potenzialmente risparmi sui costi, poiché si paga solo in base ai tempi di esecuzione del codice. PaaS è un modello intermedio. In generale, maggiore è la flessibilità offerta da un servizio, più si è responsabili in prima persona della configurazione e gestione delle risorse. I servizi FaaS gestiscono automaticamente quasi tutti gli aspetti dell'esecuzione di un'applicazione, mentre le soluzioni IaaS prevedono il provisioning, la configurazione e la gestione autonomi delle macchine virtuali e dei componenti di rete creati.

## <a name="azure-compute-options"></a>Opzioni di calcolo di Azure

Ecco le principali opzioni di calcolo attualmente disponibili in Azure:

- [Macchine virtuali](/azure/virtual-machines/) è un servizio IaaS, che consente di distribuire e gestire macchine virtuali all'interno di una rete virtuale (VNet).
- Il [servizio app](/azure/app-service/app-service-value-prop-what-is) è un'offerta PaaS gestita per l'hosting di app Web, back-end di app per dispositivi mobili, API REST o processi aziendali automatizzati.
- [Service Fabric](/azure/service-fabric/service-fabric-overview) è una piattaforma di sistemi distribuiti che è possibile eseguire in molti ambienti, ad esempio in Azure o in locale. Service Fabric è un agente di orchestrazione dei microservizi in un cluster di computer. 
- Il [servizio contenitore di Azure](/azure/container-service/container-service-intro) consente di creare, configurare e gestire un cluster di macchine virtuali preconfigurate per l'esecuzione delle applicazioni all'interno di contenitori.
- [Istanze di contenitore di Azure](/azure/container-instances/container-instances-overview) rappresenta il modo più semplice e rapido per eseguire un contenitore in Azure, senza dover effettuare il provisioning di macchine virtuali o adottare un servizio di livello superiore.
- [Funzioni di Azure](/azure/azure-functions/functions-overview) è un servizio FaaS gestito.
- [Azure Batch](/azure/batch/batch-technical-overview) è un servizio gestito per l'esecuzione di applicazioni parallele e HPC (High Performance Computing) su larga scala.
- [Servizi cloud](/azure/cloud-services/cloud-services-choose-me) è un servizio gestito per l'esecuzione di applicazioni cloud. Usa un modello di hosting PaaS. 

Ecco alcuni fattori da considerare per la scelta di un'opzione di calcolo:

- Modello di hosting. Come viene ospitato il servizio? Quali requisiti e limitazioni impone questo ambiente di hosting? 
- DevOps. È disponibile il supporto predefinito per gli aggiornamenti delle applicazioni? Qual è il modello di distribuzione?
- Scalabilità. In che modo il servizio gestisce l'aggiunta e la rimozione di istanze? Offre scalabilità automatica in base al carico e ad altre metriche? 
- Disponibilità. Cosa prevede il contratto di servizio? 
- Costi. Oltre al costo del servizio stesso, prendere in considerazione il costo delle operazioni per la gestione di una soluzione basata su tale servizio. Ad esempio, le soluzioni IaaS potrebbero avere un costo operativo superiore.
- Quali sono le limitazioni complessive di ogni servizio? 
- Quali tipi di architetture applicative sono appropriati per questo servizio? 

## <a name="next-steps"></a>Passaggi successivi

Per selezionare un servizio di calcolo per l'applicazione, usare l'[Albero delle decisioni per i servizi di calcolo di Azure](./compute-decision-tree.md)

Per un confronto più dettagliato delle opzioni di calcolo di Azure, vedere [Criteri per la scelta di un servizio di calcolo di Azure](./compute-comparison.md).
