---
title: Guida all'architettura dei dati di Azure
description: ''
author: zoinerTejada
ms.date: 02/12/2018
layout: LandingPage
ms.topic: landing-page
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 5fb74c6323f8dc571d827eb9a4c65a6c87d0ae36
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58344428"
---
# <a name="azure-data-architecture-guide"></a>Guida all'architettura dei dati di Azure

Questa guida offre un approccio strutturato alla progettazione di soluzioni basate sui dati in Microsoft Azure. È basata su procedure comprovate derivanti dall'ascolto dei clienti.

## <a name="introduction"></a>Introduzione

Il cloud sta cambiando il modo di progettare le applicazioni, inclusa la modalità di elaborazione e archiviazione dei dati. Anziché un unico database per utilizzo generico che gestisca tutti i dati di una soluzione, le soluzioni di _salvataggio permanente basato su più linguaggi_ usano diversi archivi dati specializzati, ognuno ottimizzato per offrire funzionalità specifiche. Di conseguenza, cambia anche la prospettiva sui dati all'interno della soluzione. Non esistono più i vari livelli di logica di business che eseguono operazioni di lettura e scrittura in un unico livello dati. Al contrario, le soluzioni sono progettate attorno a una *pipeline di dati* che descrive come avviene il flusso dei dati attraverso una soluzione, dove vengono elaborati e archiviati questi dati e come vengono utilizzati dal componente successivo della pipeline.

## <a name="how-this-guide-is-structured"></a>Struttura della guida

Questa guida è strutturata in base a due categorie generali di soluzione dati: i *carichi di lavoro RDBMS tradizionali* e le *soluzioni per Big Data*.

**[Carichi di lavoro RDBMS tradizionali](./relational-data/index.md)**. Questi carichi di lavoro includono elaborazione di transazioni online, ovvero OLTP, e Online Analytical Processing, ovvero OLAP. I dati nei sistemi OLTP in genere sono dati relazionali con uno schema predefinito e un set di vincoli per mantenere l'integrità referenziale. Spesso i dati provenienti da più origini nell'organizzazione possono essere consolidati in un data warehouse, usando un processo ETL per spostare e trasformare i dati di origine.

![Carichi di lavoro RDBMS tradizionali](./images/guide-rdbms.svg)

**[Soluzioni per Big Data](./big-data/index.md)**. Un'architettura per Big Data è progettata per gestire l'inserimento, l'elaborazione e l'analisi di dati troppo grandi o complessi per i sistemi di database tradizionali. I dati possono essere elaborati in batch o in tempo reale. Le soluzioni per Big Data in genere implicano l'uso di una grande quantità di dati non relazionali, ad esempio dati chiave-valore, documenti JSON o dati di Time Series. I sistemi RDBMS tradizionali spesso non sono adatti per archiviare questo tipo di dati. Il termine *NoSQL* si riferisce a una famiglia di database progettata per contenere dati non relazionali. Il termine è leggermente impreciso poiché molti archivi dati non relazionali supportano query compatibili con SQL.

![Soluzioni Big Data](./images/guide-big-data.svg)

Queste due categorie non si escludono a vicenda e si sovrappongono, tuttavia Microsoft ritiene che si tratti di un modo utile per contestualizzare la descrizione. In ogni categoria la guida illustra **scenari comuni**, inclusi i servizi di Azure pertinenti e l'architettura adatta allo scenario. In aggiunta la guida confronta le **opzioni tecnologiche** per le soluzioni per i dati in Azure, incluse le opzioni open source. All'interno di ogni categoria vengono descritti i criteri di scelta principali e viene illustrata una matrice delle funzionalità per consentire di scegliere la tecnologia adatta allo scenario specifico.

Questa guida non è uno strumento pensato per insegnare informatica o teoria dei database. Su questi argomenti, infatti, sono disponibili interi libri. Al contrario, lo scopo è quello di aiutare a selezionare l'architettura o la pipeline di dati adatta allo scenario specifico e quindi consentire di scegliere i servizi e le tecnologie di Azure che meglio soddisfano i requisiti. Se l'architettura è già stata definita, è possibile passare direttamente alle scelte di tecnologia.
