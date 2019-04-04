---
title: Implementazione di riferimento di microservizi per il servizio Azure Kubernetes
description: Questa implementazione di riferimento illustra le procedure consigliate per un'architettura di microservizi
author: MikeWasson
ms.date: 02/26/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: 15e9aa16c0e2cfccecbfb84d217c275cc99a66fd
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58344411"
---
# <a name="designing-a-microservices-architecture"></a>Progettazione di un'architettura di microservizi

I microservizi sono diventati uno stile di architettura diffuso per la creazione di applicazioni cloud che offrono resilienza, scalabilità elevata, possibilità di distribuzione indipendente e capacità di evolversi rapidamente. I microservizi non sono solo una moda, ma rappresentano un nuovo concetto che richiede un approccio diverso alla progettazione e alla creazione di applicazioni.

In questo set di articoli viene analizzato come creare ed eseguire un'architettura di microservizi in Azure. Gli argomenti includono:

- [Comunicazione tra i servizi](./interservice-communication.md)
- [Progettazione di API](./api-design.md)
- [Gateway API](./gateway.md)
- [Considerazioni sui dati](./data-considerations.md)
- [Schemi progettuali](./patterns.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di leggere questi articoli, è consigliabile iniziare con quanto segue:

- [Introduzione alle architetture di microservizi](../introduction.md). Informazioni su vantaggi e sfide dei microservizi e sui casi in cui usare questo stile di architettura.
- [Uso dell'analisi del dominio per modellare i microservizi](../model/domain-analysis.md). Informazioni su un approccio basato su dominio per la modellazione di microservizi.

## <a name="reference-implementation"></a>Implementazione di riferimento

Per illustrare le procedure consigliate per un'architettura di microservizi, è stata creata un'implementazione di riferimento, ovvero l'applicazione di recapito tramite drone. Questa implementazione viene eseguita in Kubernetes usando il servizio Azure Kubernetes (AKS). L'implementazione di riferimento è disponibile su [GitHub][drone-ri].

![Architettura dell'applicazione di recapito tramite drone](../images/drone-delivery.png)

## <a name="scenario"></a>Scenario

Fabrikam, Inc. sta avviando un servizio di recapito tramite drone. La società gestisce una flotta di droni. Le aziende possono registrarsi per usare il servizio e gli utenti possono richiedere un drone per prelevare merci da recapitare. Quando un cliente pianifica un prelievo, un sistema back-end assegna un drone e invia all'utente una notifica con un tempo di recapito stimato. Durante il recapito, il cliente può tenere traccia della posizione del drone, con un tempo stimato di arrivo che viene aggiornato continuamente.

Questo scenario prevede un dominio piuttosto complesso. Alcuni dei problemi aziendali da affrontare includono la pianificazione dei droni, il monitoraggio dei pacchetti, la gestione degli account utente e l'archiviazione e l'analisi dei dati cronologici. Fabrikam vuole inoltre accelerare i tempi di immissione sul mercato e di iterazione, aggiungendo nuove caratteristiche e funzionalità. L'applicazione deve operare a livello cloud, con un obiettivo del livello di servizio elevato. Fabrikam prevede inoltre che le diverse parti del sistema avranno requisiti molto diversi per l'archiviazione dei dati e le query. Tutte queste considerazioni hanno portato Fabrikam a scegliere un'architettura di microservizi per l'applicazione di recapito tramite drone.

> [!NOTE]
> Per un aiuto nella scelta tra un'architettura di microservizi e altri stili di architettura, vedere [Guida all'architettura delle applicazioni in Azure](../../guide/index.md).

L'implementazione di riferimento usa Kubernetes con il [servizio Azure Kubernetes](/azure/aks/). Molte delle scelte e delle sfide generali relative all'architettura si applicano tuttavia a qualsiasi agente di orchestrazione di contenitori, tra cui [Azure Service Fabric](/azure/service-fabric/).

<!-- links -->

[drone-ri]: https://github.com/mspnp/microservices-reference-implementation

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere un'opzione di calcolo](./compute-options.md)