---
title: Scelta di un archivio dati di ricerca
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: ead07e307e96696faa5ddf48505eee378027523c
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30848615"
---
# <a name="choosing-a-search-data-store-in-azure"></a>Scelta di un archivio dati di ricerca in Azure

Questo articolo mette a confronto le tecnologie disponibili per gli archivi dati di ricerca in Azure. Un archivio dati di ricerca viene usato per creare e archiviare indici specializzati per l'esecuzione di ricerche di testo in formato libero. Il testo indicizzato può trovarsi in un archivio dati separato, ad esempio una risorsa di archiviazione BLOB. Un'applicazione invia una query all'archivio dati di ricerca e ottiene come risultato un elenco di documenti corrispondenti. Per altre informazioni su questo scenario, vedere [Elaborazione di testo in formato libero per la ricerca](../scenarios/search.md). 

## <a name="what-are-your-options-when-choosing-a-search-data-store"></a>Opzioni disponibili per la scelta di un archivio dati di ricerca
In Azure tutti gli archivi dati elencati di seguito soddisfano i requisiti di base per la ricerca sui dati di testo in formato libero fornendo un indice di ricerca:
- [Ricerca di Azure](/azure/search/search-what-is-azure-search)
- [Elasticsearch](https://azuremarketplace.microsoft.com/marketplace/apps/elastic.elasticsearch?tab=Overview)
- [HDInsight con Solr](/azure/hdinsight/hdinsight-hadoop-solr-install-linux)
- [Database SQL di Azure con ricerca full-text](/sql/relational-databases/search/full-text-search)


## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per gli scenari di ricerca, rispondere prima di tutto a queste domande per scegliere l'archivio dati di ricerca adatto alle proprie esigenze:

- Si vuole usare un servizio gestito anziché gestire direttamente i propri server?

- È possibile specificare lo schema dell'indice in fase di progettazione? Se la risposta è negativa, scegliere un'opzione con il supporto per schemi aggiornabili.

- È necessario usare un indice solo per la ricerca full-text o anche per l'aggregazione rapida di dati numerici e altre funzionalità di analisi? Se si ha l'esigenza di usare funzionalità di analisi oltre alla ricerca full-text, prendere in considerazione le opzioni che includono il supporto per tali funzionalità.

- È necessario un indice di ricerca per l'analisi dei log, con supporto per la raccolta di log, l'aggregazione e le visualizzazioni su dati indicizzati? In caso affermativo, prendere in considerazione Elasticsearch, che fa parte di uno stack per l'analisi di log.

- È necessario indicizzare i dati in formati di documento comuni, ad esempio PDF, Word, PowerPoint ed Excel? In caso affermativo, scegliere un'opzione in grado di fornire indicizzatori di documenti.

- Il database ha specifici requisiti di sicurezza? In caso affermativo, prendere in considerazione le funzionalità di sicurezza elencate di seguito.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="general-capabilities"></a>Funzionalità generali

| | Ricerca di Azure | Elasticsearch | HDInsight con Solr | Database SQL | 
| --- | --- | --- | --- | --- | 
| Servizio gestito | Sì | No  | Sì | Sì |  
| API REST | Sì | Sì | Sì | No  |
| Programmabilità | .NET | Java | Java | T-SQL | 
| Indicizzatori di documenti per tipi di file comuni (PDF, DOCX, TXT e così via) | Sì | No  | Sì | No  |

### <a name="manageability-capabilities"></a>Funzionalità per la gestibilità

| | Ricerca di Azure | Elasticsearch | HDInsight con Solr | Database SQL | 
| --- | --- | --- | --- | --- |
| Schema aggiornabile | No  | Sì | Sì | Sì |
| Supporto per la scalabilità orizzontale  | Sì | Sì | Sì | No  |

### <a name="analytic-workload-capabilities"></a>Funzionalità per carichi di lavoro di analisi

| | Ricerca di Azure | Elasticsearch | HDInsight con Solr | Database SQL | 
| --- | --- | --- | --- | --- | 
| Supporto per funzionalità di analisi oltre alla ricerca full-text | No  | Sì | Sì | Sì |
| Parte di uno stack per l'analisi di log | No  | Sì (ELK) |  No  | No  |
| Supporto per la ricerca semantica | Sì (trova solo documenti simili) | Sì | Sì | Sì | 

### <a name="security-capabilities"></a>Funzionalità di sicurezza

| | Ricerca di Azure | Elasticsearch | HDInsight con Solr | Database SQL | 
| --- | --- | --- | --- | --- | 
| Sicurezza a livello di riga | Parziale (richiede una query di applicazione per filtrare in base all'ID del gruppo) | Parziale (richiede una query di applicazione per filtrare in base all'ID del gruppo) | Sì | Sì | 
| Transparent Data Encryption | No  | No  | No  | Sì |  
| Limitazione dell'accesso a specifici indirizzi IP | No  | Sì | Sì | Sì |   
| Limitazione dell'accesso alla rete virtuale | No  | Sì | Sì | Sì |  
| Autenticazione di Active Directory (integrata) | No  | No  | No  | Sì | 

## <a name="see-also"></a>Vedere anche 

[Elaborazione di testo in formato libero per la ricerca](../scenarios/search.md)