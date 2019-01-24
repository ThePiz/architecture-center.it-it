---
title: Esecuzione di carichi di lavoro di produzione SAP con un database Oracle
titleSuffix: Azure Example Scenarios
description: Eseguire una distribuzione di produzione SAP in Azure con un database Oracle.
author: DharmeshBhagat
ms.date: 09/12/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack, SAP, Windows, Linux
ms.openlocfilehash: 0f96f173d5db682ccc719869aaa22225345cb3f0
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487475"
---
# <a name="running-sap-production-workloads-using-an-oracle-database-on-azure"></a>Esecuzione di carichi di lavoro di produzione SAP con un database Oracle in Azure

I sistemi SAP vengono usati per eseguire applicazioni aziendali cruciali. Eventuali interruzioni compromettono i processi chiave e possono causare un aumento dei costi o una perdita di profitti. Per non incorrere in una simile situazione, è necessaria un'infrastruttura SAP in grado di garantire disponibilità elevata e resilienza in caso di errori.

La creazione di un ambiente SAP a disponibilità elevata richiede l'eliminazione di singoli punti di guasto nell'architettura di sistema e nei processi. I singoli punti di guasto possono essere causati da errori del sito, errori nei componenti di sistema o anche errori umani.

Questo scenario di esempio illustra una distribuzione SAP in macchine virtuali Windows o Linux in Azure, insieme a un database Oracle a disponibilità elevata. Per la distribuzione SAP, è possibile usare macchine virtuali di dimensioni diverse in base alle esigenze.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

- Carichi di lavoro cruciali in esecuzione in SAP.
- Carichi di lavoro SAP non critici.
- Ambienti di testing per SAP che simulano un ambiente a disponibilità elevata.

## <a name="architecture"></a>Architettura

![Panoramica dell'architettura di un ambiente SAP di produzione in Azure][architecture]

Questo esempio include una configurazione a disponibilità elevata per un database Oracle, SAP Central Services e più server applicazioni SAP in esecuzione in macchine virtuali diverse. La rete di Azure usa una [topologia hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) per motivi di sicurezza. Il flusso dei dati attraverso la soluzione avviene come segue:

1. Gli utenti accedono al sistema SAP tramite l'interfaccia utente SAP, un Web browser o altri strumenti client come Microsoft Excel. Una connessione ExpressRoute fornisce l'accesso dalla rete locale dell'organizzazione alle risorse in esecuzione in Azure.
2. ExpressRoute termina nel gateway di rete virtuale di ExpressRoute in Azure. Il traffico di rete viene indirizzato a una subnet del gateway tramite il gateway ExpressRoute creato nella rete virtuale dell'hub.
3. Viene eseguito il peering della rete virtuale dell'hub a una rete virtuale spoke. La subnet del livello applicazione ospita le macchine virtuali che eseguono SAP in un set di disponibilità.
4. I server di gestione delle identità offrono servizi di autenticazione per la soluzione.
5. Il jumpbox viene usato dagli amministratori di sistema per gestire in modo sicuro le risorse distribuite in Azure.

### <a name="components"></a>Componenti

- Le [reti virtuali](/azure/virtual-network/virtual-networks-overview) vengono usate in questo scenario per creare una topologia hub-spoke virtuale in Azure.

- Le [macchine virtuali](/azure/virtual-machines/windows/overview) offrono le risorse di calcolo per ogni livello della soluzione. Ogni cluster di macchine virtuali è configurato come un [set di disponibilità](/azure/virtual-machines/windows/regions-and-availability#availability-sets).

- [ExpressRoute](/azure/expressroute/expressroute-introduction) estende la rete locale nel cloud Microsoft tramite una connessione privata stabilita da un provider di connettività.

- I [gruppi di sicurezza di rete (NSG)](/azure/virtual-network/security-overview) limitano l'accesso alla rete alle risorse in una rete virtuale. Un NSG contiene un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo.

- I [gruppi di risorse](/azure/azure-resource-manager/resource-group-overview#resource-groups) fungono da contenitori logici per le risorse di Azure.

### <a name="alternatives"></a>Alternative

SAP offre opzioni flessibili per diverse combinazioni di sistema operativo, sistema di gestione di database e tipi di macchine virtuali in un ambiente Azure. Per altre informazioni, consultare la [nota SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533) "Applicazioni SAP in Azure: prodotti e tipi di macchine virtuali di Azure supportati".

## <a name="considerations"></a>Considerazioni

- È stata definita una serie di procedure consigliate per la creazione di ambienti SAP a disponibilità elevata in Azure. Per altre informazioni, vedere [Architettura e scenari di disponibilità elevata per SAP NetWeaver](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios). Vedere anche [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure](/azure/virtual-machines/workloads/sap/high-availability-guide).

- Anche per i database Oracle sono disponibili procedure consigliate per Azure. Per altre informazioni, vedere [Progettazione e implementazione di un database Oracle in Azure](/azure/virtual-machines/workloads/oracle/oracle-design).

- Oracle Data Guard viene usato per eliminare i singoli punti di guasto per i database Oracle cruciali. Per altre informazioni, vedere [Implementazione di Oracle Data Guard su una macchina virtuale Linux in Azure](/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard).

- Microsoft Azure offre servizi di infrastruttura che possono essere usati per distribuire prodotti SAP con un database Oracle. Per altre informazioni, vedere [Distribuzione di un sistema DBMS di Oracle in Azure per un carico di lavoro SAP](/azure/virtual-machines/workloads/sap/dbms_guide_oracle).

## <a name="pricing"></a>Prezzi

Per semplificare il calcolo del costo di esecuzione dello scenario, tutti i servizi sono stati preconfigurati negli esempi del calcolatore dei costi riportati di seguito. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base

Sono stati definiti quattro profili di costo di esempio in base alla quantità di traffico prevista:

|Dimensione|SAP|Tipo di macchina virtuale del database|Archiviazione database|Macchina virtuale (A)SCS|Archiviazione (A)SCS|Tipo di macchina virtuale dell'app|Archiviazione app|Calcolatore prezzi di Azure|
|----|----|-------|-------|-----|---|---|--------|---------------|
|Piccolo|30000|DS13_v2|4xP20, 1xP20|DS11_v2|1x P10|DS13_v2|1x P10|[Small](https://azure.com/e/45880ba0bfdf47d497851a7cf2650c7c)|
|Media|70000|DS14_v2|6xP20, 1xP20|DS11_v2|1x P10|4x DS13_v2|1x P10|[Medium](https://azure.com/e/9a523f79591347ca9a48c3aaa1406f8a)|
Grande|180000|E32s_v3|5xP30, 1xP20|DS11_v2|1x P10|6x DS14_v2|1x P10|[Large](https://azure.com/e/f70fccf571e948c4b37d4fecc07cbf42)|
Molto grande|250000|M64s|6xP30, 1xP30|DS11_v2|1x P10|10x DS14_v2|1x P10|[Extra Large](https://azure.com/e/58c636922cf94faf9650f583ff35e97b)|

> [!NOTE]
> Queste indicazioni dei prezzi costituiscono una guida e indicano esclusivamente le macchine virtuali e i costi di archiviazione. Sono esclusi i costi relativi a rete, archivio di backup e dati in ingresso/uscita.

- [Small](https://azure.com/e/45880ba0bfdf47d497851a7cf2650c7c): un sistema di piccole dimensioni è costituito da una macchina virtuale di tipo DS13_v2 per il server di database con 8 vCPU, 56 GB di RAM e 112 GB di archiviazione temporanea, nonché cinque dischi di archiviazione Premium da 512 GB. Un server di istanza di SAP Central che usa una macchina virtuale di tipo DS11_v2 con 2 vCPU, 14 GB di RAM e 28 GB di archiviazione temporanea. Una singola macchina virtuale di tipo DS13_v2 per il server applicazioni SAP con 8 vCPU, 56 GB di RAM e 400 GB di archiviazione temporanea, nonché un disco di archiviazione Premium da 128 GB.

- [Medium](https://azure.com/e/9a523f79591347ca9a48c3aaa1406f8a): un sistema di medie dimensioni è costituito da una macchina virtuale di tipo DS14_v2 per il server di database con 16 vCPU, 112 GB di RAM e 800 GB di archiviazione temporanea, nonché sette dischi di archiviazione Premium da 512 GB. Un server di istanza di SAP Central che usa una macchina virtuale di tipo DS11_v2 con 2 vCPU, 14 GB di RAM e 28 GB di archiviazione temporanea. Quattro macchine virtuali di tipo DS13_v2 per il server applicazioni SAP con 8 vCPU, 56 GB di RAM e 400 GB di archiviazione temporanea, nonché un disco di archiviazione Premium da 128 GB.

- [Large](https://azure.com/e/f70fccf571e948c4b37d4fecc07cbf42): un sistema di grandi dimensioni è costituito da una macchina virtuale di tipo E32s_v3 per il server di database con 32 vCPU, 256 GB di RAM e 800 GB di archiviazione temporanea, nonché tre dischi di archiviazione Premium da 512 GB e uno da 128 GB. Un server di istanza di SAP Central che usa una macchina virtuale di tipo DS11_v2 con 2 vCPU, 14 GB di RAM e 28 GB di archiviazione temporanea. Sei macchine virtuali di tipo DS14_v2 per il server applicazioni SAP con 16 vCPU, 112 GB di RAM e 224 GB di archiviazione temporanea, nonché sei dischi di archiviazione Premium da 128 GB.

- [Extra Large](https://azure.com/e/58c636922cf94faf9650f583ff35e97b): un sistema di dimensioni molto grandi è costituito da una macchina virtuale di tipo M64s per il server di database con 64 vCPU, 1024 GB di RAM e 2000 GB di archiviazione temporanea, nonché sette dischi di archiviazione Premium da 1024 GB. Un server di istanza di SAP Central che usa una macchina virtuale di tipo DS11_v2 con 2 vCPU, 14 GB di RAM e 28 GB di archiviazione temporanea. Dieci macchine virtuali di tipo DS14_v2 per il server applicazioni SAP con 16 vCPU, 112 GB di RAM e 224 GB di archiviazione temporanea, nonché dieci dischi di archiviazione Premium da 128 GB.

## <a name="deployment"></a>Distribuzione

Usare il collegamento seguente per distribuire l'infrastruttura sottostante per questo scenario.

<!-- markdownlint-disable MD033 -->

<a
href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-3tier-distributed-ora%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

<!-- markdownlint-enable MD033 -->

> [!NOTE]
> SAP e Oracle non vengono installati durante la distribuzione. Questi componenti dovranno essere distribuiti separatamente.

## <a name="related-resources"></a>Risorse correlate

Per altre informazioni sull'esecuzione di carichi di lavoro di produzione SAP in Azure, vedere le architetture di riferimento seguenti:

- [SAP NetWeaver per AnyDB](/azure/architecture/reference-architectures/sap/sap-netweaver)
- [SAP S/4HANA](/azure/architecture/reference-architectures/sap/sap-s4hana)
- [SAP HANA in istanze Large](/azure/architecture/reference-architectures/sap/hana-large-instances)

<!-- links -->
[architecture]: media/architecture-sap-production.png
