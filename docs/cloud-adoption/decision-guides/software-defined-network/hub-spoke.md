---
title: 'CAF: Reti definite dal software - Native per il cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sui servizi di rete virtuale cloud nativi
author: rotycenh
ms.openlocfilehash: e0ad6803f2ddc982ea0c42c59fdf2486e1710433
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901552"
---
# <a name="software-defined-networks-hub-and-spoke"></a>Reti definite dal software: hub-spoke

Il modello di rete hub-spoke organizza l'infrastruttura di rete cloud basata su Azure in più reti virtuali connesse. Questo modello consente di gestire in modo più efficiente i comuni requisiti di comunicazione o sicurezza e le potenziali limitazioni delle sottoscrizioni.

Nel modello hub-spoke l'*hub* è una rete virtuale che funge da posizione centrale per la gestione della connettività esterna e l'hosting dei servizi usati da più carichi di lavoro. Gli *spoke* sono reti virtuali che ospitano i carichi di lavoro e si connettono all'hub centrale tramite il [peering di rete virtuale](/virtual-network/virtual-network-peering-overview).

Tutto il traffico in ingresso o in uscita dalle reti spoke dei carichi di lavoro viene instradato tramite la rete hub dove può essere indirizzato, controllato o altrimenti gestito dalle regole o dai processi IT gestiti in modo centralizzato.

Questo modello contribuisce a risolvere i problemi seguenti:

- Risparmio sui costi ed efficienza della gestione. Centralizzando in un'unica posizione i servizi che possono essere condivisi da più carichi di lavoro, ad esempio appliance virtuali di rete e server DNS, l'IT può ridurre al minimo le risorse ridondanti e il lavoro richiesto dalla gestione in più carichi di lavoro.
- Superamento dei limiti delle sottoscrizioni. Per i carichi di lavoro di grandi dimensioni basati sul cloud, potrebbe essere necessario usare più risorse di quelle consentite in una singola sottoscrizione di Azure. Vedere [Limiti delle sottoscrizioni](/azure/azure-subscription-service-limits). Il peering delle reti virtuali dei carichi di lavoro da sottoscrizioni diverse a un hub centrale consente di superare tali limiti.
- Separazione dei compiti. Possibilità di distribuire singoli carichi di lavoro tra i team IT centrali e i team responsabili dei carichi di lavoro.

Il diagramma seguente illustra un'architettura hub-spoke di esempio che include la connettività ibrida gestita in modo centralizzato.

![Architettura di rete hub-spoke](../../../reference-architectures/hybrid-networking/images/hub-spoke.png)

L'architettura hub-spoke viene spesso usata insieme all'architettura di rete ibrida, fornendo una connessione gestita a livello centrale all'ambiente locale condiviso tra più carichi di lavoro. In questo scenario tutto il traffico esistente tra i carichi di lavoro e l'ambiente locale passa attraverso l'hub dove può essere gestito e protetto.

## <a name="hub-and-spoke-assumptions"></a>Presupposti dell'architettura hub-spoke

L'implementazione di un'architettura di rete virtuale hub-spoke presuppone quanto segue:

- Le distribuzioni cloud coinvolgono carichi di lavoro ospitati in ambienti di lavoro distinti, ad esempio sviluppo, test e produzione, tutti basati su un set di servizi comuni, ad esempio DNS o i servizi directory.
- Non è necessario che i carichi di lavoro comunichino tra loro, ma hanno comunicazioni esterne comuni e requisiti di servizi condivisi.
- I carichi di lavoro richiedono più risorse di quelle disponibili in una singola sottoscrizione di Azure.
- È necessario fornire ai team responsabili dei carichi di lavoro i diritti di gestione delegata sulle risorse mantenendo al contempo il controllo della sicurezza centrale sulla connettività esterna.

## <a name="global-hub-and-spoke"></a>Hub-spoke globale

Le architetture hub-spoke vengono comunemente implementate con le reti virtuali distribuite nella stessa area di Azure per ridurre al minimo la latenza tra le reti. Per le organizzazioni di grandi dimensioni con copertura globale potrebbe tuttavia essere necessario distribuire i carichi di lavoro tra più aree per soddisfare i requisiti di disponibilità, ripristino di emergenza o normativi. Usando il [peering di rete virtuale globale](/azure/virtual-network/virtual-network-peering-overview), il modello hub-spoke può estendere la gestione centralizzata e i servizi condivisi tra le aree per supportare la distribuzione dei carichi di lavoro in tutto il mondo.

## <a name="learn-more"></a>Altre informazioni

Per esempi di implementazione delle reti hub-spoke in Azure, vedere gli esempi seguenti nel sito Architetture di riferimento di Azure:

- [Implementare una topologia di rete hub-spoke in Azure](../../../reference-architectures/hybrid-networking/hub-spoke.md)
- [Implementare una topologia di rete hub-spoke con servizi condivisi in Azure](../../../reference-architectures/hybrid-networking/shared-services.md)
