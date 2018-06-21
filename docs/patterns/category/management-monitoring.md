---
title: Modelli di gestione e monitoraggio
description: Le applicazioni cloud vengono eseguite in un data center remoto in cui non si dispone del controllo completo dell'infrastruttura o, in alcuni casi, del sistema operativo. Ciò può rendere la gestione e il monitoraggio più complessi rispetto a una distribuzione locale. Le applicazioni devono esporre le informazioni sul runtime che gli amministratori e gli operatori possono usare per gestire e monitorare il sistema, nonché supportare i nuovi requisiti aziendali e la personalizzazione, senza che sia necessario arrestare o ridistribuire l'applicazione.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
pnp.series.title: Cloud Design Patterns
ms.openlocfilehash: faab137d59ce952e169839a71abdbbb3103ea772
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30846791"
---
# <a name="management-and-monitoring-patterns"></a>Modelli di gestione e monitoraggio

Le applicazioni cloud vengono eseguite in un data center remoto in cui non si dispone del controllo completo dell'infrastruttura o, in alcuni casi, del sistema operativo. Ciò può rendere la gestione e il monitoraggio più complessi rispetto a una distribuzione locale. Le applicazioni devono esporre le informazioni sul runtime che gli amministratori e gli operatori possono usare per gestire e monitorare il sistema, nonché supportare i nuovi requisiti aziendali e la personalizzazione, senza che sia necessario arrestare o ridistribuire l'applicazione.


|                              Modello                               |                                                              Summary                                                              |
|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
|                   [Ambasciata](../ambassador.md)                   |                 Creare servizi helper che inviano richieste di rete per conto di un servizio consumer o di un'applicazione.                 |
|        [Livello antidanneggiamento](../anti-corruption-layer.md)        |                       Implementare un'interfaccia o un livello adapter tra un'applicazione moderna e un sistema legacy.                       |
| [Archivio di configurazione esterno](../external-configuration-store.md) |                È possibile estrarre le informazioni di configurazione dal pacchetto di distribuzione dell'applicazione e spostarle in una posizione centralizzata.                |
|          [Aggregazione gateway](../gateway-aggregation.md)          |                          Usare un gateway per aggregare più richieste singole in un'unica richiesta.                           |
|           [Offload del gateway](../gateway-offloading.md)           |                              Eseguire l'offload delle funzionalità dei servizi condivise o specializzate in un proxy gateway.                              |
|              [Routing del gateway](../gateway-routing.md)              |                                   Eseguire il routing delle richieste a più servizi, usando un singolo endpoint.                                    |
|   [Monitoraggio endpoint di integrità](../health-endpoint-monitoring.md)   |   Implementare controlli funzionali all'interno di un'applicazione a cui gli strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari.    |
|                      [Collaterale](../sidecar.md)                      |         Distribuire i componenti di un'applicazione in un processo separato o in un contenitore, per fornire isolamento e incapsulamento.          |
|                    [Sostituzione](../strangler.md)                    | Migrare in maniera incrementale un sistema legacy, sostituendo gradualmente parti specifiche di funzionalità con nuove applicazioni e servizi. |

