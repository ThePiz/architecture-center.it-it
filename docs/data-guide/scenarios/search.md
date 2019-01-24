---
title: Elaborazione di testo in formato libero per la ricerca
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: ba1685d12834b04e7d231929c53453b51527cce2
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484585"
---
# <a name="processing-free-form-text-for-search"></a>Elaborazione di testo in formato libero per la ricerca

Per supportare la ricerca, è possibile eseguire l'elaborazione di testo in formato libero su documenti che contengono paragrafi di testo.

La ricerca di testo può essere eseguita creando un indice specifico che viene pre-calcolato su una raccolta di documenti. Un'applicazione client invia una query che contiene i termini di ricerca. La query restituisce un set di risultati, costituito da un elenco di documenti ordinati in base alla corrispondenza di ogni documento ai criteri di ricerca. Il set di risultati può includere anche il contesto in cui il documento corrisponde ai criteri. Ciò consente all'applicazione di evidenziare nel documento la frase corrispondente.

![Diagramma di una pipeline di ricerca](./images/search-pipeline.png)

L'elaborazione di testo in formato libero può produrre dati utili e utilizzabili a partire da grandi quantità di dati di testo non strutturato. I risultati possono assegnare a documenti non strutturati una struttura ben definita che è possibile sottoporre a query.

## <a name="challenges"></a>Problematiche

- L'elaborazione di una raccolta di documenti di testo in formato libero è in genere un'operazione a elevato utilizzo di calcolo e che richiede tempi lunghi.
- Per poter eseguire ricerche di testo in formato libero in modo efficace, l'indice di ricerca deve supportare la ricerca fuzzy, basata su termini che presentano una costruzione simile. Ad esempio, gli indici di ricerca vengono compilati con la lemmatizzazione e lo stemming linguistico, in modo che le query per "avere" trovino documenti che contengono "ha" e "avuto".

## <a name="architecture"></a>Architettura

Nella maggior parte degli scenari, i documenti di testo di origine vengono caricati in archiviazioni di oggetti, ad esempio Azure Storage o Azure Data Lake Store. Un'eccezione è rappresentata dall'uso della ricerca full-text in SQL Server o database SQL di Azure. In questo caso, i dati del documento vengono caricati in tabelle gestite dal database. Una volta archiviati, i documenti vengono elaborati in un batch per creare l'indice.

## <a name="technology-choices"></a>Scelte di tecnologia

Le opzioni disponibili per la creazione di un indice di ricerca includono Ricerca di Azure, Elasticsearch e HDInsight con Solr. Ognuna di queste tecnologie può popolare un indice di ricerca a partire da una raccolta di documenti. Ricerca di Azure offre indicizzatori che possono popolare automaticamente l'indice per documenti che vanno da testo normale a formati Excel e PDF. In HDInsight, Apache Solr è in grado di indicizzare file binari di molti tipi, inclusi testo normale, Word e PDF. Una volta costruito l'indice, i client possono accedere all'interfaccia di ricerca tramite un'API REST.

Se i dati di testo sono archiviati in SQL Server o database SQL di Azure, è possibile usare la ricerca full-text integrata nel database. Il database popola l'indice a partire da testo, file binari o dati XML archiviati all'interno del database stesso. I client eseguono la ricerca tramite query T-SQL.

Per altre informazioni, vedere [Scelta di un archivio dati di ricerca](../technology-choices/search-options.md).
