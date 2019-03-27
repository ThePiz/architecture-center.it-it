---
title: Creare partizioni per ovviare ai limiti
titleSuffix: Azure Application Architecture Guide
description: Usare il partizionamento per risolvere il problema dei limiti a livello di calcolo, database e rete.
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 76590d2a0c16df9d599e7d4a856a84b5e3bdcec8
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241782"
---
# <a name="partition-around-limits"></a>Creare partizioni per ovviare ai limiti

## <a name="use-partitioning-to-work-around-database-network-and-compute-limits"></a>Usare il partizionamento per risolvere il problema dei limiti a livello di calcolo, database e rete

Nel cloud tutti i servizi prevedono limiti di scalabilità verticale. I limiti dei servizi di Azure sono indicati in [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi][azure-limits]. I limiti includono il numero di core, le dimensioni dei database, la velocità effettiva di query e la velocità effettiva della rete. Se le dimensioni del sistema aumentano notevolmente, si potrebbero raggiungere uno o più di questi limiti. Usare il partizionamento per ovviare a questi limiti.

Ci sono molti modi per partizionare un sistema, ad esempio:

- Partizionare un database per ovviare ai limiti relativi a dimensioni del database, I/O dei dati o numero di sessioni simultanee.

- Partizionare una coda o un bus di messaggi per ovviare ai limiti relativi al numero di richieste o al numero di connessioni simultanee.

- Partizionare un'app Web del servizio app per ovviare ai limiti relativi al numero di istanze per ogni piano di servizio app.

Un database può essere partizionato *orizzontalmente*, *verticalmente* o dal punto di vista *funzionale*.

- Con il partizionamento orizzontale, ogni partizione contiene i dati di un subset del set di dati totale. Le partizioni condividono lo stesso schema dei dati. Ad esempio, i clienti i cui nomi iniziano per A&ndash;M vengono inseriti in una partizione, quelli con i nomi che iniziano per N&ndash;Z in un'altra partizione.

- Con il partizionamento verticale, ogni partizione contiene un subset dei campi per gli elementi nell'archivio dati. Inserire, ad esempio, i campi usati di frequente in una partizione e quelli usati raramente in un'altra partizione.

- Con il partizionamento funzionale, i dati vengono partizionati in base al modo in cui vengono usati da ogni contesto limitato nel sistema. Archiviare, ad esempio, i dati delle fatture in una partizione e quelli relativi all'inventario dei prodotti in un'altra. Gli schemi sono indipendenti.

Per informazioni più dettagliate, vedere [Data partitioning][data-partitioning-guidance] (Partizionamento dei dati).

## <a name="recommendations"></a>Consigli

**Partizionare diverse parti dell'applicazione**. I database sono un candidato ottimale per il partizionamento, ma prendere in considerazione anche le risorse di archiviazione, la cache, le code e le istanze di calcolo.

**Progettare la chiave di partizione in modo da evitare sovraccarichi**. Se si partiziona un database, ma una partizione riceve comunque la maggior parte delle richieste, il problema non si risolve. Idealmente, il carico deve essere distribuito uniformemente tra le partizioni. Ad esempio, eseguire l'hashing in base all'ID cliente e non alla prima lettera del nome del cliente, perché alcune lettere sono più frequenti. Lo stesso principio vale per il partizionamento di una coda di messaggi. Scegliere una chiave di partizione che consenta una distribuzione uniforme dei messaggi nel set di code. Per altre informazioni, vedere [Sharding][sharding] (Partizionamento).

**Creare partizioni per ovviare ai limiti dei servizi e della sottoscrizione di Azure**. I singoli componenti e servizi prevedono limiti, ma ci sono limiti anche per le sottoscrizioni e i gruppi di risorse. Per le applicazioni molto grandi, potrebbe essere necessario creare partizioni per ovviare a tali limiti.

**Creare partizioni a livelli diversi**. Si consideri un server di database distribuito in una macchina virtuale. La macchina virtuale ha un disco rigido virtuale supportato da Archiviazione di Azure. L'account di archiviazione appartiene a una sottoscrizione di Azure. Si noti che ogni passaggio nella gerarchia prevede limiti. Il server di database può avere un limite relativo al pool di connessioni. Le macchine virtuali hanno limiti relativi a CPU e rete. Le risorse di archiviazione hanno limiti relativi alle operazioni di I/O al secondo. La sottoscrizione ha limiti relativi al numero di core delle macchine virtuali. In genere, è più facile creare partizioni ai livelli più bassi della gerarchia. Solo le applicazioni di grandi dimensioni richiedono partizioni a livello di sottoscrizione.

<!-- links -->

[azure-limits]: /azure/azure-subscription-service-limits
[data-partitioning-guidance]: ../../best-practices/data-partitioning.md
[sharding]: ../../patterns/sharding.md
