---
title: 'CAF: Raccogliere i dati di inventario per un digital estate'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Come raccogliere i dati di inventario per un digital estate.
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 0c68ff1e5ff51395698d37fb9b59c7a76c9479b7
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55897253"
---
# <a name="gather-inventory-data-for-a-digital-estate"></a>Raccogliere i dati di inventario per un digital estate

Lo sviluppo di un inventario è il primo passaggio nella [pianificazione del digital estate](overview.md). In questo processo, sarà raccolto un elenco di asset IT che supportano funzioni aziendali specifiche per le analisi e razionalizzazioni successive. In questo articolo si presuppone che un approccio bottom-up all'analisi sia il più adeguato per le esigenze di pianificazione. Per altre informazioni, consultare [Approcci per la pianificazione del digital estate](./approach.md).

## <a name="take-inventory-of-a-digital-estate"></a>Raccogliere i dati di inventario per un digital estate

L'inventario a supporto di un digital estate cambia a seconda della trasformazione digitale desiderata e del percorso di trasformazione corrispondente.

- **Trasformazione operativa**. Durante una trasformazione operativa, è spesso consigliabile la raccolta dell'inventario da parte di strumenti di analisi in grado di creare un elenco centralizzato di tutte le macchine virtuali e i server. Alcuni strumenti possono anche creare i mapping e le dipendenze di rete, permettendo la definizione dell'allineamento del carico di lavoro.

- **Trasformazione incrementale**. L'inventario per una trasformazione incrementale inizia con il cliente. L'esecuzione il mapping dell'esperienza del cliente dall'inizio alla fine è un buon punto di partenza. L'allineamento di tale mapping ad applicazioni, API, dati e altri asset permetterà la creazione di un inventario dettagliato per l'analisi.

- **Trasformazione esponenziale**. La trasformazione esponenziale è incentrata sul prodotto o servizio. In questo caso, un inventario include un mapping di tutte le opportunità di rivoluzionare il mercato e le funzionalità necessarie.

## <a name="accuracy-and-completeness-of-an-inventory"></a>Accuratezza e completezza di un inventario

Un inventario è raramente del tutto completo alla sua prima iterazione. È altamente consigliabile che i vari membri del team di strategia per il cloud eseguano l'allineamento di stakeholder e utenti avanzati per la convalida dell'inventario. Quando possibile, sono utilizzabili strumenti aggiuntivi, come l'analisi di rete e dipendenze, per identificare gli asset che ricevono traffico, ma non sono presenti nell'inventario.

## <a name="next-steps"></a>Passaggi successivi

Una volta compilato e convalidato, l'inventario può essere razionalizzato. La razionalizzazione dell'inventario è il passaggio successivo della pianificazione del digital estate.

> [!div class="nextstepaction"]
> [Razionalizzare il digital estate](rationalize.md)