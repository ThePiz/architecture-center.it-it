---
title: Guida all'architettura dei dati di Azure
description: 
author: zoinerTejada
ms:date: 02/12/2018
layout: LandingPage
ms.openlocfilehash: 848601f27faf56ea069852d8983e4d10fbad9d77
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="azure-data-architecture-guide"></a>Guida all'architettura dei dati di Azure

Questa guida offre un approccio strutturato alla progettazione di soluzioni basate sui dati in Microsoft Azure. È basata su procedure comprovate derivanti dall'ascolto dei clienti.

## <a name="introduction"></a>Introduzione

Il cloud sta cambiando il modo di progettare le applicazioni, inclusa la modalità di elaborazione e archiviazione dei dati. Anziché un unico database per utilizzo generico che gestisca tutti i dati di una soluzione, le soluzioni di _salvataggio permanente basato su più linguaggi_ usano diversi archivi dati specializzati, ognuno ottimizzato per offrire funzionalità specifiche. Di conseguenza, cambia anche la prospettiva sui dati all'interno della soluzione. Non esistono più i vari livelli di logica di business che eseguono operazioni di lettura e scrittura in un unico livello dati. Al contrario, le soluzioni sono progettate attorno a una *pipeline di dati* che descrive come avviene il flusso dei dati attraverso una soluzione, dove vengono elaborati e archiviati questi dati e come vengono utilizzati dal componente successivo della pipeline. 

## <a name="how-this-guide-is-structured"></a>Struttura della guida

Questa guida è imperniata sulla distinzione tra dati *relazionali* e dati *non relazionali*. 

![](./images/guide-steps.svg)

I dati relazionali vengono in genere archiviati in un sistema RDBMS tradizionale o in un data warehouse. Hanno uno schema predefinito ("schema on write") con un set di vincoli necessari per mantenere l'integrità referenziale. Quasi tutti i database relazionali usano il linguaggio SQL (Structured Query Language) per eseguire query. Le soluzioni che usano database relazionali includono OLTP (Online Transaction Processing) e OLAP (Online Analytical Processing).

I dati non relazionali sono quelli che non usano il [modello relazionale](https://en.wikipedia.org/wiki/Relational_model) presente nei sistemi RDBMS tradizionali. Può trattarsi di dati chiave-valore, dati JSON, dati a grafo, dati di serie temporali e altri tipi di dati. Il termine *NoSQL* si riferisce a database progettati per contenere vari tipi di dati non relazionali. Tuttavia, il termine è leggermente impreciso poiché molti archivi dati non relazionali supportano query compatibili con SQL. I dati non relazionali e i database NoSQL vengono spesso citati nelle descrizioni di soluzioni per *Big Data*. Un'architettura per Big Data è progettata per gestire l'inserimento, l'elaborazione e l'analisi di dati troppo grandi o complessi per i sistemi di database tradizionali. 

All'interno di ognuna di queste due categorie principali, la Guida all'architettura dei dati contiene le sezioni seguenti:

- **Concetti.** Articoli introduttivi ai concetti principali che è necessario conoscere quando si usano dati di questo tipo.
- **Scenari.** Set rappresentativo di scenari di dati, inclusa una descrizione dei servizi di Azure pertinenti e dell'architettura adatta allo scenario.
- **Scelte di tecnologia.** Confronti dettagliati tra varie tecnologie di dati disponibili in Azure, incluse le opzioni open source. All'interno di ogni categoria vengono descritti i criteri di scelta principali e viene illustrata una matrice delle funzionalità per consentire di scegliere la tecnologia adatta allo scenario specifico.

Questa guida non è uno strumento pensato per insegnare informatica o teoria dei database. Su questi argomenti, infatti, sono disponibili interi libri. Al contrario, lo scopo è quello di aiutare a selezionare l'architettura o la pipeline di dati adatta allo scenario specifico e quindi consentire di scegliere i servizi e le tecnologie di Azure che meglio soddisfano i requisiti. Se l'architettura è già stata definita, è possibile passare direttamente alle scelte di tecnologia.

## <a name="traditional-rdbms"></a>Sistemi RDBMS tradizionali

### <a name="concepts"></a>Concetti

- [Dati relazionali](./concepts/relational-data.md) 
- [Dati transazionali](./concepts/transactional-data.md) 
- [Modellazione semantica](./concepts/semantic-modeling.md) 

### <a name="scenarios"></a>Scenari

- [OLAP (Online Analytical Processing)](./scenarios/online-analytical-processing.md)
- [OLTP (Online Transaction Processing)](./scenarios/online-transaction-processing.md) 
- [Data warehousing e data mart](./scenarios/data-warehousing.md)
- [ETL](./scenarios/etl.md) 

## <a name="big-data-and-nosql"></a>Big Data e NoSQL

### <a name="concepts"></a>Concetti

- [Archivi dati non relazionali](./concepts/non-relational-data.md)
- [Uso dei file in formato CSV e JSON](./concepts/csv-and-json.md)
- [Architetture per Big Data](./concepts/big-data.md)
- [Analisi avanzata](./concepts/advanced-analytics.md) 
- [Machine Learning su larga scala](./concepts/machine-learning-at-scale.md)

### <a name="scenarios"></a>Scenari

- [Elaborazione batch](./scenarios/batch-processing.md)
- [Elaborazione in tempo reale](./scenarios/real-time-processing.md)
- [Ricerca di testo in formato libero](./scenarios/search.md)
- [Esplorazione interattiva dei dati](./scenarios/interactive-data-exploration.md)
- [Elaborazione del linguaggio naturale](./scenarios/natural-language-processing.md)
- [Soluzioni per serie temporali](./scenarios/time-series.md)

## <a name="cross-cutting-concerns"></a>Problematiche trasversali

- [Trasferimento dei dati](./scenarios/data-transfer.md) 
- [Estensione di soluzioni dati locali nel cloud](./scenarios/hybrid-on-premises-and-cloud.md) 
- [Sicurezza delle soluzioni dati](./scenarios/securing-data-solutions.md) 
