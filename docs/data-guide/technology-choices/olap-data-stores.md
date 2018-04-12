---
title: Scelta di un archivio dati OLAP
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: f3041b95696c9408a2c9ab747fe1ec3041db0743
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2018
---
# <a name="choosing-an-olap-data-store-in-azure"></a>Scelta di un archivio dati OLAP in Azure

OLAP (Online Analytical Processing) è una tecnologia che consente di organizzare i database aziendali di grandi dimensioni e supporta l'esecuzione di analisi complesse. Questo argomento mette a confronto le opzioni disponibili in Azure per le soluzioni OLAP.

> [!NOTE]
> Per altre informazioni sull'uso di un archivio dati OLAP, vedere [OLAP (Online Analytical Processing)](../scenarios/online-analytical-processing.md).

## <a name="what-are-your-options-when-choosing-an-olap-data-store"></a>Opzioni disponibili per la scelta di un archivio dati OLAP

In Azure tutti gli archivi dati elencati di seguito soddisfano i requisiti di base per OLAP:

- [SQL Server con indici columnstore](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)
- [Azure Analysis Services](/azure/analysis-services/analysis-services-overview)
- [SQL Server Analysis Services (SSAS)](/sql/analysis-services/analysis-services)

SQL Server Analysis Services (SSAS) offre funzionalità OLAP e di data mining per applicazioni di business intelligence. È possibile installare SSAS nei server locali oppure ospitarlo all'interno di una macchina virtuale in Azure. Azure Analysis Services è un servizio completamente gestito che fornisce le stesse funzionalità principali di SSAS. Azure Analysis Services supporta la connessione a [varie origini dati](/azure/analysis-services/analysis-services-datasource) sul cloud e locali nell'organizzazione.

Gli indici columnstore cluster sono disponibili in SQL Server 2014 e versioni successive, oltre che nel database SQL di Azure, e sono ideali per i carichi di lavoro OLAP. Tuttavia, a partire da SQL Server 2016 (incluso il database SQL di Azure), è possibile sfruttare la tecnologia di elaborazione HTAP (Hybrid Transactional/Analytics Processing) usando indici columnstore non cluster aggiornabili. HTAP consente di eseguire l'elaborazione OLTP e OLAP sulla stessa piattaforma, eliminando così la necessità di archiviare più copie dei dati e di usare sistemi OLTP e OLAP distinti. Per altre informazioni, vedere [Introduzione a columnstore per l'analisi operativa in tempo reale](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics).

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- Si vuole usare un servizio gestito anziché gestire direttamente i propri server?

- Si richiede l'autenticazione sicura con Azure Active Directory (Azure AD)?

- Si vogliono eseguire analisi in tempo reale? In caso affermativo, limitare la scelta alle opzioni che supportano l'analisi in tempo reale. 

    In questo contesto l'*analisi in tempo reale* si applica a una singola origine dati, ad esempio un'applicazione ERP (Enterprise Resource Planning), che gestisce contemporaneamente un carico di lavoro operativo e un carico di lavoro di analisi. Se si ha la necessità di integrare dati provenienti da più origini o di ottenere prestazioni di analisi estremamente elevate con l'uso di dati preaggregati, ad esempio i cubi, è possibile richiedere un data warehouse separato.

- È necessario usare dati preaggregati, ad esempio per fornire modelli semantici che consentano un'analisi più intuitiva a livello aziendale? In caso affermativo, scegliere un'opzione con il supporto per cubi multidimensionali o modelli semantici tabulari. 

    La disponibilità di dati preaggregati può aiutare gli utenti a calcolare le aggregazioni di dati in modo coerente. I dati preaggregati consentono inoltre di migliorare notevolmente le prestazioni quando si gestiscono più colonne per un numero elevato righe. I dati possono essere preaggregati in cubi multidimensionali o in modelli semantici tabulari.

- È necessario integrare dati provenienti da più origini, oltre all'archivio dati OLTP? In caso affermativo, prendere in considerazione le opzioni che facilitano l'integrazione di più origini dati.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="general-capabilities"></a>Funzionalità generali

| | Azure Analysis Services | SQL Server Analysis Services | SQL Server con indici columnstore | Database SQL di Azure con indici columnstore |
| --- | --- | --- | --- | --- |
| Servizio gestito | Sì | No  | No  | Sì |
| Supporto per i cubi multidimensionali | No  | Sì | No  | No  |
| Supporto per i modelli semantici tabulari | Sì | Sì | No  | No  |
| Facilità di integrazione di più origini dati | Sì | Sì | No <sup>1</sup> | No <sup>1</sup> |
| Supporto per l'analisi in tempo reale | No  | No  | Sì | Sì |
| Necessità di un processo per copiare i dati da una o più origini | Sì | Sì | No  | No  |
| Integrazione di Azure AD | Sì | No  | No <sup>2</sup> | Sì |

[1] Anche se SQL Server e il database SQL di Azure non possono essere usati per eseguire query da più origini dati esterne e integrare tali origini, è possibile creare una pipeline che esegua automaticamente questa operazione usando [SSIS](/sql/integration-services/sql-server-integration-services) o [Azure Data Factory](/azure/data-factory/). Con SQL Server ospitato in una macchina virtuale di Azure, è possibile sfruttare opzioni aggiuntive, ad esempio i server collegati e [PolyBase](/sql/relational-databases/polybase/polybase-guide). Per altre informazioni, vedere [Scelta di una tecnologia di orchestrazione di una pipeline di dati in Azure](../technology-choices/pipeline-orchestration-data-movement.md).

[2] La connessione a SQL Server in esecuzione in una macchina virtuale di Azure non è supportata con un account Azure AD. Usare un account Active Directory di dominio.

### <a name="scalability-capabilities"></a>Funzionalità di scalabilità

| | Azure Analysis Services | SQL Server Analysis Services | SQL Server con indici columnstore | Database SQL di Azure con indici columnstore |
| --- | --- | --- | --- | --- |
| Server regionali ridondanti per disponibilità elevata  | Sì | No  | Sì | Sì |
| Supporto per la scalabilità orizzontale delle query  | Sì | No  | Sì | No  |
| Scalabilità dinamica (aumento delle prestazioni)  | Sì | No  | Sì | No  |

