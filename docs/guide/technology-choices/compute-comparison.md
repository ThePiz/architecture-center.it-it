---
title: Criteri per la scelta di un'opzione di calcolo di Azure
description: Confrontare i servizi di calcolo di Azure in base a diverse coordinate.
author: MikeWasson
layout: LandingPage
ms.openlocfilehash: 36b57d1fb674b5a1452a0e8208de836963b2b01b
ms.sourcegitcommit: c53adf50d3a787956fc4ebc951b163a10eeb5d20
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="criteria-for-choosing-an-azure-compute-option"></a>Criteri per la scelta di un'opzione di calcolo di Azure

Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione. Le tabelle seguenti confrontano i servizi di calcolo di Azure in base a diverse coordinate. Fare riferimento a queste tabelle per scegliere un'opzione di calcolo per l'applicazione.

## <a name="hosting-model"></a>Modello di hosting

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Servizi cloud | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Composizione dell'applicazione | Agnostico | Applicazioni | Servizi, eseguibili guest, contenitori | Funzioni | Contenitori | Ruoli | Scheduled jobs  |
| Densità | Agnostico | Più app per istanza tramite piani di app | Più servizi per VM | Nessuna istanza dedicata<a href="#note1"><sup>1</sup></a> | Più contenitori per VM | Un'istanza del ruolo per VM | Più app per VM |
| Numero minimo di nodi | 1 <a href="#note2"><sup>2</sup></a>  | 1 | 5 <a href="#note3"><sup>3</sup></a> | Nessun nodo dedicato<a href="#note1"><sup>1</sup></a> | 3 | 2 | 1 <a href="#note4"><sup>4</sup></a> |
| Gestione dello stato | Senza stato o con stato | Senza stato | Senza stato o con stato | Senza stato | Senza stato o con stato | Senza stato | Senza stato |
| Hosting Web | Agnostico | Integrato | Agnostico | Non applicabile | Agnostico | Integrato (IIS) | No |
| OS | Windows, Linux | Windows, Linux  | Windows, Linux | Non applicabile | Windows (anteprima), Linux | Windows | Windows, Linux |
| Può essere distribuito in una rete virtuale dedicata? | Supportato | Supportato<a href="#note5"><sup>5</sup></a> | Supportato | Non supportate | Supportato | Supportato<a href="#note6"><sup>6</sup></a> | Supportato |
| Connettività ibrida | Supportato | Supportato<a href="#note1"><sup>7</sup></a>  | Supportato | Non supportate | Supportato | Supportato<a href="#note8"><sup>8</sup></a> | Supportato |

Note

1. <span id="note1">Se si usa un piano a consumo. Se si usa un piano di servizio app, le funzioni vengono eseguite nelle macchine virtuali allocate per il piano di servizio app. Vedere [Scegliere il piano di servizio corretto per Funzioni di Azure][function-plans].</a>
2. <span id="note2">Contratto di servizio con disponibilità superiore con due o più istanze.</a>
3. <span id="note3">Per gli ambienti di produzione.</a>
4. <span id="note4">Possibile riduzione fino a zero dopo il completamento del processo.</a>
5. <span id="note5">Richiede Ambiente del servizio app.</a>
6. <span id="note6">Solo rete virtuale classica.</a>
7. <span id="note7">Richiede Ambiente del servizio app o Connessioni ibride BizTalk</a>
8. <span id="note8">Rete virtuale classica o rete virtuale di Resource Manager tramite peering reti virtuali</a>

## <a name="devops"></a>DevOps

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Servizi cloud | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Debug locale | Agnostico | IIS Express, altri<a href="#note1b"><sup>1</sup></a> | Cluster a nodi locali | Interfaccia della riga di comando di Funzioni di Azure | Runtime del contenitore locale | Emulatore locale | Non supportate |
| Modello di programmazione | Agnostico | Applicazione Web, processi Web per attività in background | Eseguibile guest, modello del servizio, modello Actor, contenitori | Funzioni con trigger | Agnostico | Ruolo Web, ruolo di lavoro | Applicazione della riga di comando |
| Gestione risorse | Supportato | Supportato | Supportato | Supportato | Supportato | Limitato<a href="#note2b"><sup>2</sup></a> | Supportato |  
| Aggiornamento dell'applicazione | Nessun supporto integrato | Slot di distribuzione | Aggiornamento in sequenza (per servizio) | Nessun supporto integrato | Dipende dall'agente di orchestrazione. La maggior parte supporta gli aggiornamenti in sequenza | Scambio di indirizzi VIP o aggiornamento in sequenza | Non applicabile |

Note

1. <span id="note1b">Le opzioni disponibili includono IIS Express per ASP.NET o node.js (iisnode), server Web PHP, Azure Toolkit for IntelliJ, Azure Toolkit for Eclipse. Servizio app supporta anche il debug remoto dell'app Web distribuita.</a>
2. <span id="note2b">Vedere [Provider, aree, versioni API e schemi di Resource Manager][resource-manager-supported-services]. 


## <a name="scalability"></a>Scalabilità

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Servizi cloud | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Scalabilità automatica | Set di scalabilità di macchine virtuali | Servizio integrato | Set di scalabilità delle VM | Servizio integrato | Non supportate | Servizio integrato | N/D |
| Bilanciamento del carico | Azure Load Balancer | Integrato | Azure Load Balancer | Integrato | Azure Load Balancer | Integrato | Azure Load Balancer |
| Limite di scalabilità | Immagine della piattaforma: 1000 nodi per set di scalabilità di macchine virtuali, immagine personalizzata: 100 nodi per set di scalabilità di macchine virtuali | 20 istanze, 50 con Ambiente del servizio app | 100 nodi per set di scalabilità di macchine virtuali | Senza limiti<a href="#note1c"><sup>1</sup></a> | 100 | Nessun limite definito, massimo 200 consigliati | Limite di 20 core per impostazione predefinita. Contattare il servizio clienti per aumentare il limite. |

Note

1. <span id="note1c">Se si usa un piano a consumo. Se si usa un piano di servizio app, si applicano i limiti di scalabilità di Servizio app. Vedere [Scegliere il piano di servizio corretto per Funzioni di Azure][function-plans].</a>

## <a name="availability"></a>Disponibilità

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Servizi cloud | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Contratto di servizio | [Contratto di servizio per Macchine virtuali][sla-vm] | [Contratto di servizio per Servizio app][sla-app-service] | [Contratto di servizio per Service Fabric][sla-sf] | [Contratto di servizio per Funzioni][sla-functions] | [Contratto di servizio per il servizio contenitore di Azure][sla-acs] | [Contratto di servizio per Servizi cloud][sla-cloud-service] | [Contratto di servizio per Azure Batch][sla-batch] |
| Failover in più aree | Gestione traffico | Gestione traffico | Gestione traffico, cluster in più aree | Non supportate  | Gestione traffico | Gestione traffico | Non supportato |

## <a name="security"></a>Sicurezza

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Servizi cloud | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| SSL | Configurato in VM | Supportato | Supportato  | Supportato | Configurato in VM | Supportato | Supportato |
| Controllo degli accessi in base al ruolo | Supportato | Supportato | Supportato | Supportato | Supportato | Non supportate | Supportato |

## <a name="other"></a>Altre

| Criteri | Macchine virtuali | Servizio app | Service Fabric | Funzioni di Azure | Servizio contenitore di Azure | Servizi cloud | Azure Batch |
|----------|-----------------|-------------|----------------|-----------------|-------------------------|----------------|-------------|
| Costi | [Windows][cost-windows-vm], [Linux][cost-linux-vm] | [Prezzi di Servizio app][cost-app-service] | [Prezzi di Service Fabric][cost-service-fabric] | [Prezzi di Funzioni di Azure][cost-functions] | [Prezzi del servizio contenitore di Azure][cost-acs] | [Prezzi di Servizi cloud][cost-cloud-services] | [Prezzi di Azure Batch][cost-batch]
| Stili di architettura adatti | N livelli, Big Compute (HPC) | Web/coda/ruolo di lavoro | Microservizi, architettura guidata dagli eventi | Microservizi, architettura guidata dagli eventi | Microservizi, architettura guidata dagli eventi | Web/coda/ruolo di lavoro | Big Compute |

[cost-linux-vm]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/
[cost-windows-vm]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[cost-app-service]: https://azure.microsoft.com/pricing/details/app-service/
[cost-service-fabric]: https://azure.microsoft.com/pricing/details/service-fabric/
[cost-functions]: https://azure.microsoft.com/pricing/details/functions/
[cost-acs]: https://azure.microsoft.com/pricing/details/container-service/
[cost-cloud-services]: https://azure.microsoft.com/pricing/details/cloud-services/
[cost-batch]: https://azure.microsoft.com/pricing/details/batch/

[function-plans]: /azure/azure-functions/functions-scale
[sla-acs]: https://azure.microsoft.com/support/legal/sla/container-service/
[sla-app-service]: https://azure.microsoft.com/support/legal/sla/app-service/
[sla-batch]: https://azure.microsoft.com/support/legal/sla/batch/
[sla-cloud-service]: https://azure.microsoft.com/support/legal/sla/cloud-services/
[sla-functions]: https://azure.microsoft.com/support/legal/sla/functions/
[sla-sf]: https://azure.microsoft.com/support/legal/sla/service-fabric/
[sla-vm]: https://azure.microsoft.com/support/legal/sla/virtual-machines/

[resource-manager-supported-services]: /azure/azure-resource-manager/resource-manager-supported-services