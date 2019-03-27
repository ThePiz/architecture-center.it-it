---
title: Archivi data lake
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: d433867886ce0afc219fcc9f35eb7f8b3ce6bee1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244092"
---
# <a name="data-lakes"></a>Archivi data lake

Un archivio data lake è un repository che contiene una grande quantità di dati nel relativo formato nativo non elaborato. Gli archivi data lake sono ottimizzati per la scalabilità fino a terabyte e petabyte di dati. I dati provengono generalmente da più origini eterogenee e possono essere strutturati, semistrutturati o non strutturati. L'obiettivo di un archivio data lake è archiviare tutti gli elementi nello stato originale, senza trasformazioni. Questo approccio differisce dal tradizionale [data warehouse](../relational-data/data-warehousing.md), che invece trasforma ed elabora i dati al momento dell'inserimento.

I vantaggi di un archivio data lake sono i seguenti:

- I dati non vengono mai eliminati perché vengono archiviati nel formato non elaborato. Ciò è particolarmente utile in un ambiente Big Data, quando non si sa in anticipo quali informazioni siano disponibili nei dati.
- Gli utenti possono esplorare i dati e creare le proprie query.
- Può essere più rapido degli strumenti di estrazione, trasformazione e caricamento tradizionali.
- È più flessibile di un data warehouse perché consente di archiviare dati non strutturati e semistrutturati.

Una soluzione data lake completa prevede l'archiviazione e l'elaborazione. L'archiviazione in un data lake è progettata per la tolleranza di errore, la scalabilità illimitata e l'elevata velocità nell'inserimento dei dati con diverse forme e dimensioni. L'elaborazione in un data lake prevede l'uso di uno o più motori creati tenendo presenti questi obiettivi e che sono in grado di elaborare i dati archiviati nel data lake su larga scala.

## <a name="when-to-use-a-data-lake"></a>Quando usare un archivio data lake

L'archivio data lake viene tipicamente usato per l'[esplorazione dei dati](./interactive-data-exploration.md), l'analisi dei dati e le attività di Machine Learning.

Un archivio data lake può anche essere usato come origine dati di un data warehouse. Con questo approccio, i dati non elaborati vengono inseriti nell'archivio data lake e quindi trasformati in un formato strutturato disponibile per le query. Questa trasformazione usa in genere una pipeline [ELT](../relational-data/etl.md#extract-load-and-transform-elt) (Extract, Load, and Transform) in cui i dati vengono inseriti e trasformati sul posto. Un'origine dati già relazionale può essere inserita direttamente nel data warehouse usando un processo ETL, senza passare per l'archivio data lake.

Gli archivi di data lake vengono spesso usati in scenari di Internet delle cose o di flussi di eventi perché sono in grado di salvare in modo persistente grandi quantità di dati relazionali e non relazionali, senza alcuna trasformazione o definizione di schema. Vengono creati per gestire volumi elevati di piccole operazioni di scrittura a bassa latenza e sono ottimizzati per offrire un'enorme velocità effettiva.

## <a name="challenges"></a>Problematiche

- L'assenza di uno schema o di metadati descrittivi può rendere complesso l'utilizzo dei dati o l'esecuzione di query.
- L'assenza di coerenza semantica tra i dati può rendere complicato eseguire l'analisi dei dati, a meno che gli utenti non abbiano competenze particolarmente elevate in materia di analisi.
- Può essere difficile garantire la qualità dei dati che vengono inseriti nell'archivio data lake.
- Senza una governance appropriata, gli aspetti correlati al controllo degli accessi e alla privacy possono rappresentare problemi. È necessario stabilire quali informazioni verranno inserite nell'archivio data lake, chi potrà accedere ai dati e per quali fini.
- Un archivio data lake potrebbe non essere il modo migliore per integrare dati già relazionali.
- Un archivio data lake non fornisce di per sé visualizzazioni integrate o olistiche dell'intera organizzazione.
- Un archivio data lake può diventare una "discarica" di dati che non vengono mai effettivamente analizzati o sottoposti a data mining per ottenere informazioni dettagliate.

## <a name="relevant-azure-services"></a>Servizi di Azure pertinenti

- [Data Lake Store](/azure/data-lake-store/) è un repository con iperscalabilità compatibile con Hadoop.
- [Data Lake Analytics](/azure/data-lake-analytics/) è un servizio per processi di analisi su richiesta che semplifica l'analisi dei Big Data.
