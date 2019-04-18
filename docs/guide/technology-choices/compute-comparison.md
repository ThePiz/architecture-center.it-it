---
title: Criteri per la scelta di un servizio di calcolo di Azure
titleSuffix: Azure Application Architecture Guide
description: Confrontare i servizi di calcolo di Azure in base a diverse coordinate.
author: MikeWasson
ms.date: 08/08/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: c4ce7a5da196e0fcf8f85376439e53683432883e
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640465"
---
# <a name="criteria-for-choosing-an-azure-compute-service"></a>Criteri per la scelta di un servizio di calcolo di Azure

Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione. Le tabelle seguenti confrontano i servizi di calcolo di Azure in base a diverse coordinate. Fare riferimento a queste tabelle per scegliere un'opzione di calcolo per l'applicazione.

## <a name="hosting-model"></a>Modello di hosting

<!-- markdownlint-disable MD033 -->

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio Azure Kubernetes | Istanze di Container | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Composizione dell'applicazione | Agnostico | Applicazioni, contenitori | Servizi, eseguibili guest, contenitori | Funzioni | Contenitori | Contenitori | Processi pianificati  |
| Densità | Agnostico | Più app per istanza tramite piani di servizio app | Più servizi per VM | Senza server <a href="#note1"><sup>1</sup></a> | Più contenitori per nodo |Nessuna istanza dedicata | Più app per VM |
| Numero minimo di nodi | 1 <a href="#note2"><sup>2</sup></a>  | 1 | 5 <a href="#note3"><sup>3</sup></a> | Senza server <a href="#note1"><sup>1</sup></a> | 3 <a href="#note3"><sup>3</sup></a> | Nessun nodo dedicato | 1 <a href="#note4"><sup>4</sup></a> |
| Gestione dello stato | Senza stato o con stato | Senza stato | Senza stato o con stato | Senza stato | Senza stato o con stato | Senza stato | Senza stato |
| Hosting Web | Agnostico | Integrato | Agnostico | Non applicabile | Agnostico | Agnostico | No  |
| Può essere distribuito in una rete virtuale dedicata? | Supportato | Supportato<a href="#note5"><sup>5</sup></a> | Supportato | Supportato<a href="#note5"><sup>5</sup></a> | [Supportato](/azure/aks/networking-overview) | Non supportate | Supportato |
| Connettività ibrida | Supportato | Supportato<a href="#note6"><sup>6</sup></a>  | Supportato | Supportato<a href="#note7"><sup>7</sup></a> | Supportato | Non supportate | Supportato |

Note

1. <span id="note1">Se si usa un piano a consumo. Se si usa un piano di servizio app, le funzioni vengono eseguite nelle macchine virtuali allocate per il piano di servizio app. Vedere [Scegliere il piano di servizio corretto per Funzioni di Azure][function-plans].</span>
2. <span id="note2">Contratto di servizio con disponibilità superiore con due o più istanze.</span>
3. <span id="note3">Consigliato per ambienti di produzione.</span>
4. <span id="note4">Possibile riduzione fino a zero dopo il completamento del processo.</span>
5. <span id="note5">Richiede Ambiente del servizio app.</span>
6. <span id="note6">Usare le [connessioni ibride del servizio app di Azure][app-service-hybrid].</span>
7. <span id="note7">Richiede il piano di servizio app.</span>

## <a name="devops"></a>DevOps

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio Azure Kubernetes | Istanze di Container | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Debug locale | Agnostico | IIS Express, altri<a href="#note1b"><sup>1</sup></a> | Cluster a nodi locali | Visual Studio o interfaccia della riga di comando di Funzioni di Azure | Minikube, altri | Runtime del contenitore locale | Non supportate |
| Modello di programmazione | Agnostico | Applicazioni Web e API, processi Web per attività in background | Eseguibile guest, modello del servizio, modello Actor, contenitori | Funzioni con trigger | Agnostico | Agnostico | Applicazione della riga di comando |
| Aggiornamento dell'applicazione | Nessun supporto integrato | Slot di distribuzione | Aggiornamento in sequenza (per servizio) | Slot di distribuzione | Aggiornamento in sequenza | Non applicabile |

Note

1. <span id="note1b">Le opzioni disponibili includono IIS Express per ASP.NET o node.js (iisnode), server Web PHP, Azure Toolkit for IntelliJ, Azure Toolkit for Eclipse. Servizio app supporta anche il debug remoto dell'app Web distribuita.</span>
2. <span id="note2b">Vedere [Provider, aree, versioni API e schemi di Resource Manager][resource-manager-supported-services].</span>

## <a name="scalability"></a>Scalabilità

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio Azure Kubernetes | Istanze di Container | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Scalabilità automatica | Set di scalabilità di macchine virtuali | Servizio integrato | Set di scalabilità delle VM | Servizio integrato | Non supportate | Non supportate | N/D |
| Bilanciamento del carico | Azure Load Balancer | Integrato | Azure Load Balancer | Integrato | Integrato |  Nessun supporto integrato | Azure Load Balancer |
| Limite di scalabilità<a href="#note1c"><sup>1</sup></a> | Immagine della piattaforma: 1000 nodi per set di scalabilità di macchine virtuali. Immagine personalizzata: 100 nodi per set di scalabilità di macchine virtuali | 20 istanze, 100 con l'ambiente del servizio app | 100 nodi per set di scalabilità di macchine virtuali | 200 istanze per ogni app per le funzioni | 100 nodi per cluster (limite predefinito) |20 gruppi di contenitori per sottoscrizione (limite predefinito) | 20 core (limite predefinito) |

Note

1. <span id="note1c">Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](/azure/azure-subscription-service-limits)</span>.

## <a name="availability"></a>Disponibilità

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio Azure Kubernetes | Istanze di Container | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Contratto di servizio | [Contratto di servizio per Macchine virtuali][sla-vm] | [Contratto di servizio per Servizio app][sla-app-service] | [Contratto di servizio per Service Fabric][sla-sf] | [Contratto di servizio per Funzioni][sla-functions] | [Contratto di servizio per il servizio Azure Kubernetes][sla-acs] | [Contratto di servizio per Istanze di Container](https://azure.microsoft.com/support/legal/sla/container-instances/) | [Contratto di servizio per Azure Batch][sla-batch] |
| Failover in più aree | Gestione traffico | Gestione traffico | Gestione traffico, cluster in più aree | Non supportate | Gestione traffico | Non supportate | Non supportato |

## <a name="other"></a>Altri

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio Azure Kubernetes | Istanze di Container | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| SSL | Configurato in VM | Supportato | Supportato  | Supportato | [Controller di ingresso](/azure/aks/ingress) | Usare il contenitore [sidecar](../../patterns/sidecar.md) | Supportato |
| Costi | [Windows][cost-windows-vm], [Linux][cost-linux-vm] | [Prezzi di Servizio app][cost-app-service] | [Prezzi di Service Fabric][cost-service-fabric] | [Prezzi di Funzioni di Azure][cost-functions] | [Prezzi del servizio Azure Kubernetes][cost-acs] | [Prezzi di Istanze di Container](https://azure.microsoft.com/pricing/details/container-instances/) | [Prezzi di Azure Batch][cost-batch]
| Stili di architettura adatti | [Livello N][n-tier], [Big Compute][big-compute] (HPC) | [Web-Queue-Worker][w-q-w], [a più livelli][n-tier] | [Microservizi][microservices], [Architettura guidata dagli eventi][event-driven] | [Microservizi][microservices], [Architettura guidata dagli eventi][event-driven] | [Microservizi][microservices], [Architettura guidata dagli eventi][event-driven] | [Microservizi][microservices], automazione delle attività, processi batch  | [Big Compute][big-compute] (HPC) |

<!-- markdownlint-enable MD033 -->

[cost-linux-vm]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/
[cost-windows-vm]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[cost-app-service]: https://azure.microsoft.com/pricing/details/app-service/
[cost-service-fabric]: https://azure.microsoft.com/pricing/details/service-fabric/
[cost-functions]: https://azure.microsoft.com/pricing/details/functions/
[cost-acs]: https://azure.microsoft.com/pricing/details/kubernetes-service/
[cost-batch]: https://azure.microsoft.com/pricing/details/batch/

[function-plans]: /azure/azure-functions/functions-scale
[sla-acs]: https://azure.microsoft.com/support/legal/sla/kubernetes-service
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

[app-service-hybrid]: /azure/app-service/app-service-hybrid-connections
