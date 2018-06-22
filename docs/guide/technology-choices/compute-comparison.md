---
title: Criteri per la scelta di un servizio di calcolo di Azure
description: Confrontare i servizi di calcolo di Azure in base a diverse coordinate
author: MikeWasson
layout: LandingPage
ms.date: 06/13/2018
ms.openlocfilehash: 29c21c44bdf3a3bfa29f17015565eecf5f86163b
ms.sourcegitcommit: 26b04f138a860979aea5d253ba7fecffc654841e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36206659"
---
# <a name="criteria-for-choosing-an-azure-compute-service"></a>Criteri per la scelta di un servizio di calcolo di Azure

Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione. Le tabelle seguenti confrontano i servizi di calcolo di Azure in base a diverse coordinate. Fare riferimento a queste tabelle per scegliere un'opzione di calcolo per l'applicazione.

## <a name="hosting-model"></a>Modello di hosting

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Istanze di contenitore | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Composizione dell'applicazione | Agnostico | Applicazioni, contenitori | Servizi, eseguibili guest, contenitori | Funzioni | Contenitori | Contenitori | Scheduled jobs  |
| Densità | Agnostico | Più app per istanza tramite piani di app | Più servizi per VM | Nessuna istanza dedicata<a href="#note1"><sup>1</sup></a> | Più contenitori per VM |Nessuna istanza dedicata | Più app per VM |
| Numero minimo di nodi | 1 <a href="#note2"><sup>2</sup></a>  | 1 | 5 <a href="#note3"><sup>3</sup></a> | Nessun nodo dedicato<a href="#note1"><sup>1</sup></a> | 3 | Nessun nodo dedicato | 1 <a href="#note4"><sup>4</sup></a> |
| Gestione dello stato | Senza stato o con stato | Senza stato | Senza stato o con stato | Senza stato | Senza stato o con stato | Senza stato | Senza stato |
| Hosting Web | Agnostico | Integrato | Agnostico | Non applicabile | Agnostico | Agnostico | No  |
| OS | Windows, Linux | Windows, Linux  | Windows, Linux | Non applicabile | Windows (anteprima), Linux | Windows, Linux | Windows, Linux |
| Può essere distribuito in una rete virtuale dedicata? | Supportato | Supportato<a href="#note5"><sup>5</sup></a> | Supportato | Non supportate | Supportato | Non supportate | Supportato |
| Connettività ibrida | Supportato | Supportato<a href="#note1"><sup>6</sup></a>  | Supportato | Non supportate | Supportato | Non supportate | Supportato |

Note

1. <span id="note1">Se si usa un piano a consumo. Se si usa un piano di servizio app, le funzioni vengono eseguite nelle macchine virtuali allocate per il piano di servizio app. Vedere [Scegliere il piano di servizio corretto per Funzioni di Azure][function-plans].</span>
2. <span id="note2">Contratto di servizio con disponibilità superiore con due o più istanze.</span>
3. <span id="note3">Per gli ambienti di produzione.</span>
4. <span id="note4">Possibile riduzione fino a zero dopo il completamento del processo.</span>
5. <span id="note5">Richiede Ambiente del servizio app.</span>
6. <span id="note7">Richiede Ambiente del servizio app o Connessioni ibride BizTalk</span>

## <a name="devops"></a>DevOps

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Istanze di contenitore | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Debug locale | Agnostico | IIS Express, altri<a href="#note1b"><sup>1</sup></a> | Cluster a nodi locali | Interfaccia della riga di comando di Funzioni di Azure | Runtime del contenitore locale | Runtime del contenitore locale | Non supportate |
| Modello di programmazione | Agnostico | Applicazione Web, processi Web per attività in background | Eseguibile guest, modello del servizio, modello Actor, contenitori | Funzioni con trigger | Agnostico | Agnostico | Applicazione della riga di comando |
| Aggiornamento dell'applicazione | Nessun supporto integrato | Slot di distribuzione | Aggiornamento in sequenza (per servizio) | Nessun supporto integrato | Dipende dall'agente di orchestrazione. La maggior parte supporta gli aggiornamenti in sequenza | Aggiornare l'immagine del contenitore | Non applicabile |

Note

1. <span id="note1b">Le opzioni disponibili includono IIS Express per ASP.NET o node.js (iisnode), server Web PHP, Azure Toolkit for IntelliJ, Azure Toolkit for Eclipse. Servizio app supporta anche il debug remoto dell'app Web distribuita.</span>
2. <span id="note2b">Vedere [Provider, aree, versioni API e schemi di Resource Manager][resource-manager-supported-services].</span> 


## <a name="scalability"></a>Scalabilità

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Istanze di contenitore | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Scalabilità automatica | Set di scalabilità di macchine virtuali | Servizio integrato | Set di scalabilità delle VM | Servizio integrato | Non supportate | Non supportate | N/D |
| Bilanciamento del carico | Azure Load Balancer | Integrato | Azure Load Balancer | Integrato | Azure Load Balancer |  Nessun supporto integrato | Azure Load Balancer |
| Limite di scalabilità | Immagine della piattaforma: 1000 nodi per set di scalabilità di macchine virtuali, immagine personalizzata: 100 nodi per set di scalabilità di macchine virtuali | 20 istanze, 50 con Ambiente del servizio app | 100 nodi per set di scalabilità di macchine virtuali | Senza limiti<a href="#note1c"><sup>1</sup></a> | 100 <a href="#note2c"><sup>2</sup></a> |20 gruppi di contenitori per sottoscrizione per impostazione predefinita. Contattare il servizio clienti per aumentare il limite. <a href="#note3c"><sup>3</sup></a> | Limite di 20 core per impostazione predefinita. Contattare il servizio clienti per aumentare il limite. |

Note

1. <span id="note1c">Se si usa un piano a consumo. Se si usa un piano di servizio app, si applicano i limiti di scalabilità di Servizio app. Vedere [Scegliere il piano di servizio corretto per Funzioni di Azure][function-plans].</span>
2. <span id="note2c">Vedere [Ridimensionare i nodi agente in un cluster del servizio contenitore][scale-acs]</span>.
3. <span id="note3c">Vedere [Quote e aree disponibili per Istanze di contenitore di Azure](/azure/container-instances/container-instances-quotas).</span>


## <a name="availability"></a>Disponibilità

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Istanze di contenitore | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Contratto di servizio | [Contratto di servizio per Macchine virtuali][sla-vm] | [Contratto di servizio per Servizio app][sla-app-service] | [Contratto di servizio per Service Fabric][sla-sf] | [Contratto di servizio per Funzioni][sla-functions] | [Contratto di servizio per il servizio contenitore di Azure][sla-acs] | [Contratto di servizio per le istanze di contenitore](https://azure.microsoft.com/support/legal/sla/container-instances/) | [Contratto di servizio per Azure Batch][sla-batch] |
| Failover in più aree | Gestione traffico | Gestione traffico | Gestione traffico, cluster in più aree | Non supportate  | Gestione traffico | Non supportate | Non supportato |

## <a name="other"></a>Altri

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Istanze di contenitore | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| SSL | Configurato in VM | Supportato | Supportato  | Supportato | Configurato in VM | Supportato con un contenitore collaterale | Supportato |
| Costi | [Windows][cost-windows-vm], [Linux][cost-linux-vm] | [Prezzi di Servizio app][cost-app-service] | [Prezzi di Service Fabric][cost-service-fabric] | [Prezzi di Funzioni di Azure][cost-functions] | [Prezzi del servizio contenitore di Azure][cost-acs] | [Prezzi delle istanze di contenitore](https://azure.microsoft.com/pricing/details/container-instances/) | [Prezzi di Azure Batch][cost-batch]
| Stili di architettura adatti | [Livello N][n-tier], [Big Compute][big-compute] (HPC) | [Web-coda-ruolo di lavoro][w-q-w] | [Microservizi][microservices], [Architettura guidata dagli eventi][event-driven] | [Microservizi][microservices], [Architettura guidata dagli eventi][event-driven] | [Microservizi][microservices], [Architettura guidata dagli eventi][event-driven] | [Microservizi][microservices], automazione delle attività, processi batch  | [Big Compute][big-compute] (HPC) |

[cost-linux-vm]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/
[cost-windows-vm]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[cost-app-service]: https://azure.microsoft.com/pricing/details/app-service/
[cost-service-fabric]: https://azure.microsoft.com/pricing/details/service-fabric/
[cost-functions]: https://azure.microsoft.com/pricing/details/functions/
[cost-acs]: https://azure.microsoft.com/pricing/details/container-service/
[cost-batch]: https://azure.microsoft.com/pricing/details/batch/

[function-plans]: /azure/azure-functions/functions-scale
[sla-acs]: https://azure.microsoft.com/support/legal/sla/container-service/
[sla-app-service]: https://azure.microsoft.com/support/legal/sla/app-service/
[sla-batch]: https://azure.microsoft.com/support/legal/sla/batch/
[sla-functions]: https://azure.microsoft.com/support/legal/sla/functions/
[sla-sf]: https://azure.microsoft.com/support/legal/sla/service-fabric/
[sla-vm]: https://azure.microsoft.com/support/legal/sla/virtual-machines/

[resource-manager-supported-services]: /azure/azure-resource-manager/resource-manager-supported-services
[scale-acs]: /azure/container-service/kubernetes/container-service-scale#scaling-considerations

[n-tier]: ../architecture-styles/n-tier.md
[w-q-w]: ../architecture-styles/web-queue-worker.md
[microservices]: ../architecture-styles/microservices.md
[event-driven]: ../architecture-styles/event-driven.md
[big-date]: ../architecture-styles/big-data.md
[big-compute]: ../architecture-styles/big-compute.md

