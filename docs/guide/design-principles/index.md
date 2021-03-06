---
title: Principi di progettazione per le applicazioni Azure
titleSuffix: Azure Application Architecture Guide
description: Principi di progettazione per le applicazioni Azure.
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 8aab710ef6ffde493b80810750d2c0bc299ffaa6
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58344258"
---
# <a name="ten-design-principles-for-azure-applications"></a>Dieci principi di progettazione per le applicazioni Azure

Seguire questi principi di progettazione per rendere l'applicazione più scalabile, resiliente e gestibile.

**[Progettazione per la correzione automatica](self-healing.md)**. In un sistema distribuito possono verificarsi errori. Progettare l'applicazione in modo che sia in grado di eseguire la correzione automatica dei propri errori.

**[Rendere ogni elemento ridondante](redundancy.md)**. Applicare la ridondanza nell'applicazione per evitare singoli punti di errore.

**[Ridurre al minimo il coordinamento](minimize-coordination.md)**. Ridurre al minimo il coordinamento tra i servizi delle applicazioni per ottenere la scalabilità.

**[Progettare per la scalabilità orizzontale](scale-out.md)**. Progettare l'applicazione in modo che possa scalare orizzontalmente, aggiungendo o rimuovendo nuove istanze in base alle esigenze.

**[Creare partizioni per ovviare i limiti](partition.md)**. Usare il partizionamento per risolvere il problema dei limiti a livello di calcolo, database e rete.

**[Progettare per le esigenze del team operativo](design-for-operations.md)**. Progettare l'applicazione in modo che il team operativo disponga degli strumenti necessari.

**[Usare i servizi gestiti](managed-services.md)**. Quando possibile, usare la piattaforma distribuita come servizio (PaaS) invece dell'infrastruttura distribuita come servizio (IaaS).

**[Usare il migliore archivio dati per il processo](use-the-best-data-store.md)**. Scegliere la tecnologia di archiviazione più adatta ai dati e alle modalità d'utilizzo previste.

**[Progettare in un'ottica evolutiva](design-for-evolution.md)**. Tutte le applicazioni efficienti cambiano nel tempo. Una progettazione in grado di evolversi è fondamentale per l'innovazione continua.

**[Compilare l'applicazione per le esigenze aziendali](build-for-business.md)**. Ogni decisione di progettazione deve essere giustificata da un requisito aziendale.
