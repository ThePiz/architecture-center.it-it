---
title: Usare il migliore archivio dati per il processo
titleSuffix: Azure Application Architecture Guide
description: Scegliere la tecnologia di archiviazione più adatta ai dati e alle modalità d'utilizzo previste.
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 2f83e7a184e8fa94f49da4e8fd5252396ecfd632
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484602"
---
# <a name="use-the-best-data-store-for-the-job"></a>Usare il migliore archivio dati per il processo

## <a name="pick-the-storage-technology-that-is-the-best-fit-for-your-data-and-how-it-will-be-used"></a>Scegliere la tecnologia di archiviazione che rappresenta la soluzione migliore per i dati e per le modalità d'uso che si adopereranno successivamente

Sono ormai passati i tempi in cui i dati sarebbero solo stati bloccati in un grande database SQL relazionale. I database relazionali sono molto efficaci nelle loro operazioni, dal momento che &mdash; forniscono garanzie ACID per le transazioni sui dati relazionali, ma presentano alcuni costi:

- Le query possono richiedere unioni costose.
- I dati devono essere normalizzati e conformi a uno schema predefinito (schema-on-write).
- Contese di blocco possono influire sulla prestazioni.

In qualsiasi soluzione di grandi dimensioni, è probabile che una sola tecnologia di archivio dati non soddisfi tutte le esigenze. Alternative a database relazionali includono: store chiave/valore, database di documenti, database di motori di ricerca, database di serie temporali, database della famiglia di colonna e database grafo. Ognuno di essi presenta vantaggi e svantaggi, e diversi tipi di dati si adattano con maggiore naturalezza in una o nell'altra alternativa.

Ad esempio, è possibile archiviare un catalogo di prodotti in un database di documento, come Cosmos DB, che offre uno schema flessibile. In tal caso, la descrizione di ogni prodotto è un documento autonomo. Per le query sull'intero catalogo, è possibile indicizzare lo stesso e archiviare l'indice nella ricerca di Azure. L'inventario dei prodotti potrebbe passare in un database SQL, poiché tali dati richiedono garanzie ACID.

Tenere presente che i dati non includono solo i dati applicazioni persistenti, ma anche i registri applicazioni, gli eventi, i messaggi e le cache.

## <a name="recommendations"></a>Consigli

**Non usare un database relazionale per tutte le funzioni**. Prendere in considerazione altri archivi dati, quando è appropriato. Vedere [Choose the right data store][data-store-overview] (Scegliere il giusto archivio dati).

**Adottare la persistenza poliglotta**. In qualsiasi soluzione di grandi dimensioni, è probabile che una sola tecnologia di archivio dati non soddisfi tutte le esigenze.

**Considerare il tipo di dati**. Ad esempio, inserire i dati transazionali in SQL, inserire i documenti JSON in un database di documenti, inserire i dati di telemetria in un database di serie temporale, inserire i registri applicazioni in Elasticsearch e BLOB nell'archiviazione Blob di Azure.

**Scegliere la disponibilità rispetto alla (alta) coerenza** . Il teorema CAP implica che un sistema distribuito debba trovare un compromesso tra disponibilità e coerenza (le partizioni di rete, l'altro segmento del teorema CAP, non possono mai essere evitate completamente). Spesso, è possibile ottenere una maggiore disponibilità adottando un modello di *coerenza finale*.

**Prendere in considerazione le capacità del team di sviluppo**. L'uso della persistenza poliglotta ha dei vantaggi, ma è possibile che si ecceda. L'adozione di una nuova tecnologia di archiviazione di dati richiede un nuovo set di competenze. È necessario che il team di sviluppo comprenda come sfruttare al meglio la tecnologia, che comprenda i modelli di uso appropriati, come ottimizzare le query e le prestazioni e così via. Tenere in considerazione ciò quando si valutano le tecnologie di archiviazione.

**Usare la transazione di compensazione**. La singola transazione potrebbe scrivere i dati in più archivi e ciò rappresenta un effetto collaterale della persistenza poliglotta. Se qualcosa non ha un buon esito, è possibile usare le transazioni di compensazione per annullare eventuali passaggi precedentemente completati.

**Esaminare i contesti limitati**. *Contesto limitato* è un termine preso dalla progettazione basata sui domini. Un contesto limitato è una delimitazione esplicita attorno a un modello di dominio e definisce a quali parti del dominio il modello si debba applicare. In teoria, un contesto limitato esegue il mapping a un sottodominio del dominio aziendale. I contesti limitati nel sistema rappresentano il luogo naturale della persistenza poliglotta. Per esempio, "prodotti" può essere visualizzato sia nel sottodominio catalogo dei prodotti, sia in quello inventario dei prodotti. Tuttavia, è molto probabile che questi due sottodomini abbiano requisiti differenti per quel che concerne l'archiviazione, l'aggiornamento e le domande sui prodotti.

[data-store-overview]: ../technology-choices/data-store-overview.md
