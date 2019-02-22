---
title: 'CAF: Reti definite dal software - Cloud native'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Discussione relativa ai servizi di rete virtuale cloud nativa
author: rotycenh
ms.openlocfilehash: c6200491bc9ba35a9f00e0003e51716b58628980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901240"
---
# <a name="software-defined-networks-cloud-native"></a>Reti definite dal software: Cloud nativo

In fase di distribuzione delle risorse IaaS, come le macchine virtuali in una piattaforma cloud, è necessaria una rete virtuale cloud nativa. Per l'accesso alle reti virtuali da origini esterne, come il Web, è necessario eseguire esplicitamente il provisioning. Questi tipi di reti virtuali supportano la creazione di subnet, regole di gestione, firewall virtuali e dispositivi per la gestione del traffico.

Una rete virtuale cloud nativa, per supportare i carichi di lavoro ospitati sul cloud, non ha dipendenze su risorse dell'organizzazione in locale o su altre risorse non cloud. A tutte le risorse necessarie viene effettuato il provisioning nella stessa rete virtuale o tramite le offerte PaaS gestite.

## <a name="cloud-native-assumptions"></a>Presupposti del cloud nativo

La distribuzione di una rete virtuale cloud nativa presuppone quanto segue:

- I carichi di lavoro distribuiti nella rete virtuale non hanno dipendenze su applicazioni o servizi che sono accessibili solo dall'interno della rete locale. A meno che non forniscano un endpoint accessibile tramite rete Internet pubblica, le applicazioni e i servizi ospitati internamente in locale, non possono essere usati dalle risorse ospitate in una piattaforma cloud.
- La gestione dell'identità e il controllo di accesso e del carico di lavoro dipende dai servizi di gestione delle identità della piattaforma cloud o da server IaaS ospitati nell'ambiente cloud. Non è necessario connettersi direttamente a servizi di gestione delle identità ospitati in locale o ad altre posizioni esterne.
- Non è necessario che i servizi di gestione delle identità supportino Single Sign-On (SSO) con le directory locali.

Le reti virtuali cloud native non hanno dipendenze esterne. Questo le rende facili da distribuire e configurare; di conseguenza questa architettura è spesso la scelta migliore per esperimenti o altre distribuzioni più piccole autosufficienti o di rapida iterazione.

Altri problemi che il team di adozione cloud deve prendere in considerazione quando si parla di un'architettura di rete virtuale cloud nativa, includono:

- I carichi di lavoro esistenti progettati per l'esecuzione in un data center locale possono richiedere ampie modifiche per sfruttare le funzionalità basate su cloud, come i servizi di archiviazione o di autenticazione.
- Le reti cloud native vengono gestite esclusivamente tramite gli strumenti di gestione della piattaforma cloud, pertanto col passare del tempo possono provocare divergenze a livello di gestione e criteri rispetto agli standard IT esistenti.

## <a name="learn-more"></a>Altre informazioni

Vedere gli argomenti seguenti per altre informazioni sulle reti virtuali cloud native nella piattaforma Azure.

- [Rete virtuale di Azure: guide alle procedure](/azure/virtual-network/virtual-network-vnet-plan-design-arm). Le reti virtuali di Azure appena create sono cloud native per impostazione predefinita. Usare queste guide per facilitare la pianificazione della progettazione e la distribuzione di reti virtuali.
- [Limiti delle sottoscrizioni: Rete](/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Qualsiasi rete virtuale singola e le risorse connesse possono esistere soltanto all'interno di una singola sottoscrizione e operare con i limiti della stessa.
