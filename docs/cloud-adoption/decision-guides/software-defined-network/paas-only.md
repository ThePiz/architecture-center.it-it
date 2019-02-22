---
title: 'CAF: Reti definite dal software - Solo PaaS'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sul modello solo PaaS per le funzionalità di rete basate sul cloud
author: rotycenh
ms.openlocfilehash: 2f3f82d781ddb6544721e82e7b7d795222a2f8ff
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901724"
---
# <a name="software-defined-networks-paas-only"></a>Reti definite dal software: solo PaaS

Quando si implementa una risorsa piattaforma distribuita come servizio (PaaS), il processo di distribuzione crea automaticamente una rete sottostante presunta con un numero limitato di controlli su tale rete, inclusi bilanciamento del carico, blocco delle porte e connessioni ad altri servizi PaaS.

In Azure diversi tipi di risorsa PaaS possono essere [distribuiti](/azure/virtual-network/virtual-network-for-azure-services) o [connessi a](/azure/virtual-network/virtual-network-service-endpoints-overview) una rete virtuale, consentendo a queste risorse di integrarsi con l'infrastruttura di rete virtuale esistente. In molti casi, tuttavia, un'architettura di rete solo PaaS, basata esclusivamente su queste funzionalità di rete predefinite fornite in modo nativo dalle risorse PaaS, è sufficiente per soddisfare i requisiti relativi ai carichi di lavoro.

Se si sta valutando un'architettura di rete solo PaaS, verificare che i presupposti obbligatori siano allineati ai requisiti.

## <a name="paas-only-assumptions"></a>Presupposti solo PaaS

La distribuzione di un'architettura di rete solo PaaS presuppone quanto segue:

- L'applicazione da distribuire è un'applicazione autonoma OPPURE dipende solo da altre risorse PaaS.
- I team responsabili delle operazioni IT possono aggiornare gli strumenti, il training e i processi per supportare la gestione, la configurazione e la distribuzione delle applicazioni PaaS autonome.
- L'applicazione PaaS non fa parte di un più ampio processo di migrazione cloud che includerà risorse IaaS.

Questi presupposti sono qualificatori minimi allineati alla distribuzione di una rete solo PaaS. Anche se questo approccio può allinearsi ai requisiti di distribuzione di una singola applicazione, il team responsabile dell'adozione cloud deve rispondere a queste domande con una prospettiva a lungo termine:

- L'ambito o le dimensioni di questa distribuzione si espanderanno rendendo necessario l'accesso ad altre risorse non PaaS?
- Sono pianificate altre distribuzioni PaaS oltre alla soluzione corrente?
- L'organizzazione ha in programma altre migrazioni cloud in futuro?

Le risposte a queste domande non precludono a un team la scelta di un'opzione solo PaaS, ma è opportuno valutarle prima di prendere una decisione finale.
