---
title: SAP per carichi di lavoro di sviluppo/test
description: Scenario SAP per un ambiente di sviluppo/test
author: AndrewDibbins
ms.date: 7/11/18
ms.openlocfilehash: 675a5cb4b1ee4001ca50d24c145ce1a177f90da4
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060962"
---
# <a name="sap-for-devtest-workloads"></a>SAP per carichi di lavoro di sviluppo/test

Questo esempio offre indicazioni su come eseguire un'implementazione di sviluppo/test di SAP NetWeaver in un ambiente Windows o Linux in Azure. Il database usato è AnyDB, che nella terminologia SAP indica qualsiasi DBMS supportato diverso da SAP HANA. Dato che è progettata per ambienti non di produzione, l'architettura viene distribuita con una sola macchina virtuale (VM) ed è possibile modificarne le dimensioni in base alle esigenze dell'organizzazione.

Per casi d'uso di produzione, esaminare le architetture di riferimento SAP disponibili di seguito:

* [SAP NetWeaver per AnyDB][sap-netweaver]
* [SAP S/4Hana][sap-hana]
* [SAP in istanze Large di Azure][sap-large]

## <a name="related-use-cases"></a>Casi d'uso correlati

Prendere in considerazione questo scenario per i casi d'uso seguenti:

* Carichi di lavoro SAP non di produzione e non critici (sandbox, sviluppo, test, controllo di qualità)
* Carichi di lavoro SAP Business One non critici

## <a name="architecture"></a>Architettura

![Diagramma](media/sap-2tier/SAP-Infra-2Tier_finalversion.png)

Questo scenario prevede il provisioning di un singolo database di sistema SAP e un server applicazioni SAP in un'unica macchina virtuale. Il flusso dei dati nello scenario avviene come segue:

1. I clienti dal livello presentazione usano in locale l'interfaccia utente grafica SAP o altre interfacce utente (Internet Explorer, Excel o un'altra applicazione Web) per accedere al sistema SAP basato su Azure.
2. Per la connettività viene usata la connessione ExpressRoute stabilita, che termina nel gateway ExpressRoute in Azure. Il traffico di rete viene indirizzato attraverso il gateway ExpressRoute alla subnet del gateway, da questa alla subnet spoke del livello applicazione (vedere il modello [hub-spoke][hub-spoke]) e quindi tramite un gateway di sicurezza di rete alla macchina virtuale dell'applicazione SAP.
3. I server di gestione delle identità offrono servizi di autenticazione.
4. Il jumpbox offre funzionalità di gestione locale.

### <a name="components"></a>Componenti

* I [gruppi di risorse](/azure/azure-resource-manager/resource-group-overview#resource-groups) sono un contenitore logico per le risorse di Azure.
* Le [reti virtuali](/azure/virtual-network/virtual-networks-overview) costituiscono la base delle comunicazioni di rete in Azure.
* Le [macchine virtuali](/azure/virtual-machines/windows/overview) di Azure offrono un'infrastruttura virtualizzata sicura, a scalabilità elevata e su richiesta con Windows o Linux Server.
* [ExpressRoute](/azure/expressroute/expressroute-introduction) consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività.
* Un [gruppo di sicurezza di rete](/azure/virtual-network/security-overview) consente di limitare il traffico di rete verso le risorse in una rete virtuale. Un gruppo di sicurezza di rete contiene un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in ingresso o in uscita in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo. 

## <a name="considerations"></a>Considerazioni

### <a name="availability"></a>Disponibilità

 Microsoft offre un contratto di servizio per le singole istanze di VM. Per altre informazioni sul contratto di servizio di Microsoft Azure per le macchine virtuali, vedere [Contratto di Servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines).

### <a name="scalability"></a>Scalabilità

Per indicazioni generali sulla progettazione di soluzioni scalabili, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Sicurezza

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].

## <a name="pricing"></a>Prezzi

Esaminare il costo di esecuzione dello scenario. Nel calcolatore dei costi sono preconfigurati tutti i servizi.  Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base al traffico previsto.

Sono stati definiti quattro profili di costo di esempio in base alla quantità di traffico prevista:

|Dimensione|SAP|Tipo macchina virtuale|Archiviazione|Calcolatore prezzi di Azure|
|----|----|-------|-------|---------------|
|Piccolo|8000|D8s_v3|2xP20, 1xP10|[Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)|
|Media|16000|D16s_v3|3xP20, 1xP10|[Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd)|
Grande|32000|E32s_v3|3xP20, 1xP10|[Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)|
Molto grande|64000|M64s|4xP20, 1xP10|[Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)|

Nota: i prezzi offrono una guida e indicano solo le VM e i costi di archiviazione (escludendo gli addebiti per rete, archivio di backup e dati in ingresso/uscita).

* [Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1): un sistema di piccole dimensioni è costituito da una VM di tipo D8s_v3 con 8 vCPU, 32 GB di RAM e 200 GB di archiviazione temporanea, nonché due dischi di archiviazione Premium da 512 GB e uno da 128 GB.
* [Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd): un sistema di medie dimensioni è costituito da una VM di tipo D16s_v3 con 16 vCPU, 64 GB di RAM e 400 GB di archiviazione temporanea, nonché tre dischi di archiviazione Premium da 512 GB e uno da 128 GB.
* [Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931): un sistema di grandi dimensioni è costituito da una VM di tipo E32s_v3 con 32 vCPU, 256 GB di RAM e 512 GB di archiviazione temporanea, nonché tre dischi di archiviazione Premium da 512 GB e uno da 128 GB.
* [Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef): un sistema di dimensioni molto grandi è costituito da una VM di tipo M64s con 64 vCPU, 1024 GB di RAM e 2000 GB di archiviazione temporanea, nonché quattro dischi di archiviazione Premium da 512 GB e uno da 128 GB.

## <a name="deployment"></a>Distribuzione

Per distribuire un'infrastruttura sottostante simile allo scenario descritto sopra, usare il pulsante di seguito.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-2tier%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

\* SAP non verrà installato. L'installazione dovrà essere eseguita manualmente al termine della creazione dell'infrastruttura.

<!-- links -->
[reference architecture]:  /azure/architecture/reference-architectures/sap
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[sap-netweaver]: /azure/architecture/reference-architectures/sap/sap-netweaver
[sap-hana]: /azure/architecture/reference-architectures/sap/sap-s4hana
[sap-large]: /azure/architecture/reference-architectures/sap/hana-large-instances
[hub-spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke