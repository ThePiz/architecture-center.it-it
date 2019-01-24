---
title: OLTP (Online Transaction Processing)
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: c6cc628977b438578f2d88d1928afcd75ddddbcd
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481814"
---
# <a name="online-transaction-processing-oltp"></a>OLTP (Online Transaction Processing)

La gestione di dati transazionali tramite sistemi informatici prende il nome di Online Transaction Processing (OLTP). I sistemi OLTP registrano le interazioni aziendali man mano che si verificano nell'ambito delle operazioni giornaliere eseguite all'interno dell'organizzazione e supportano la creazione di query su questi dati per l'inserimento di inferenze.

## <a name="transactional-data"></a>Dati transazionali

I dati transazionali sono informazioni che tengono traccia delle interazioni correlate alle attività di un'organizzazione. Tali interazioni sono in genere transazioni aziendali, ad esempio pagamenti ricevuti dai clienti, pagamenti effettuati ai fornitori, movimentazione di prodotti dell'inventario, elaborazione di ordini o fornitura di servizi. Gli eventi transazionali, che rappresentano le transazioni stesse, in genere contengono una dimensione temporale, alcuni valori numerici e riferimenti ad altri dati.

In genere, le transazioni devono essere *atomiche* e *coerenti*. L'atomicità indica che un'intera transazione ha sempre esito positivo o negativo come unità di lavoro e non viene mai lasciata in uno stato parziale. Se una transazione non può essere completata, il sistema di database deve eseguire il rollback di eventuali passaggi già eseguiti come parte di tale transazione. In un sistema di gestione di database relazionali (RDBMS) tradizionale, il rollback viene eseguito automaticamente se una transazione non può essere completata. La coerenza indica che le transazioni lasciano sempre i dati in uno stato valido. Queste sono descrizioni molto informali dei concetti di atomicità e coerenza. Sono tuttavia disponibili definizioni più formali di queste proprietà, ad esempio [ACID](https://en.wikipedia.org/wiki/ACID).

I database transazionali possono supportare la coerenza assoluta per le transazioni che usano diverse strategie di blocco, ad esempio il blocco pessimistico, per garantire che tutti i dati siano fortemente coerenti nel contesto aziendale per tutti gli utenti e i processi.

L'architettura di distribuzione più comune che usa i dati transazionali è il livello di archivio dati in un'architettura a 3 livelli. Un'architettura a 3 livelli in genere è costituita da livello di presentazione, livello di logica di business e livello di archivio dati. Un'architettura di distribuzione correlata è l'architettura [a più livelli](/azure/architecture/guide/architecture-styles/n-tier), che può avere più livelli intermedi che gestiscono la logica di business.

## <a name="typical-traits-of-transactional-data"></a>Caratteristiche tipiche dei dati transazionali

I dati transazionali hanno, in genere, le caratteristiche seguenti:

| Requisito | DESCRIZIONE |
| --- | --- |
| Normalizzazione | Normalizzazione elevata |
| SCHEMA | Schema durante la scrittura, fortemente applicato|
| Consistency | Coerenza assoluta, garanzia ACID |
| Integrità | Integrità elevata |
| Uso delle transazioni | Yes |
| Strategia di blocco | Ottimistica o pessimistica|
| Aggiornabile | Yes |
| Accodamento | Yes |
| Carico di lavoro | Operazioni di scrittura intense e lettura moderate |
| Indicizzazione | Indici primari e secondari |
| Dimensioni dato | Da piccole a medie |
| Modello | Relazionale |
| Forma dei dati | Tabulare |
| Flessibilità query | Flessibilità elevata |
| Scalabilità | Da piccole (MB) a grandi (alcuni TB) dimensioni |

## <a name="when-to-use-this-solution"></a>Quando usare questa soluzione

Scegliere OLTP nei casi in cui è necessario elaborare e archiviare in modo efficiente transazioni aziendali, nonché renderle immediatamente disponibili alle applicazioni client in modo coerente. Usare questa architettura anche quando eventuali ritardi tangibili nell'elaborazione avrebbero un impatto negativo sulle operazioni quotidiane dell'azienda.

I sistemi OLTP sono progettati per elaborare e archiviare in modo efficiente le transazioni, nonché per eseguire query sui dati transazionali. L'obiettivo dei sistemi OLTP di elaborare e archiviare in modo efficiente singole transazioni viene parzialmente soddisfatto dalla normalizzazione dei dati, ovvero dalla suddivisione dei dati in blocchi più piccoli meno ridondanti. Questo meccanismo favorisce l'efficienza perché consente al sistema OLTP di elaborare grandi quantità di transazioni in modo indipendente ed evita le attività di elaborazione altrimenti necessarie per mantenere l'integrità dei dati in presenza di dati ridondanti.

## <a name="challenges"></a>Problematiche

L'implementazione e l'utilizzo di un sistema OLTP può creare alcune problematiche:

- I sistemi OLTP non sono sempre adatti per la gestione di aggregazioni di grandi quantità di dati, sebbene esistano alcune eccezioni, come una soluzione basata su SQL Server ben pianificata. L'analisi dei dati, che si basa su calcoli aggregati di milioni di singole transazioni, richiede l'utilizzo di moltissime risorse per un sistema OLTP. L'esecuzione dei calcoli, infatti, può essere molto lunga e causare rallentamenti bloccando le altre transazioni nel database.
- Quando vengono eseguite operazioni di analisi e report su dati altamente normalizzati, le query tendono a essere complesse, poiché la maggior parte delle query deve denormalizzare i dati per mezzo di join. Nei sistemi OLTP, inoltre, le convenzioni di denominazione per oggetti di database tendono a essere concise e sintetiche. Questa elevata normalizzazione, associata alle concisione delle convenzioni di denominazione, rende più difficile per gli utenti aziendali eseguire query sui sistemi OLTP senza l'aiuto di un amministratore di database o di uno sviluppatore di dati.
- L'archiviazione della cronologia delle transazioni per un periodo illimitato e l'archiviazione di una quantità eccessiva di dati in una tabella può generare inoltre un rallentamento delle prestazioni delle query, in funzione del numero di transazioni archiviate. Una possibile soluzione consiste nel mantenere una finestra temporale rilevante (ad esempio l'anno fiscale corrente) nel sistema OLTP e scaricare i dati storici su altri sistemi, ad esempio un data mart o un [data warehouse](./data-warehousing.md).

## <a name="oltp-in-azure"></a>OLTP in Azure

Le applicazioni come i siti Web ospitati nell'[app Web del servizio app](/azure/app-service/app-service-web-overview), le API REST in esecuzione nel servizio app o applicazioni desktop o per dispositivi mobili comunicano in genere con il sistema OLTP tramite un intermediario dell'API REST.

In pratica, la maggior parte dei carichi di lavoro non è esclusivamente OLTP ma tende a essere anche un componente analitico. Si assiste anche a una domanda crescente di strumenti per la creazione di report in tempo reale, ad esempio per l'esecuzione di report inerenti al sistema operativo. Questi processo prende il nome di Hybrid Transactional and Analytical Processing (HTAP). Per altre informazioni, vedere [OLAP (Online Analytical Processing)](./online-analytical-processing.md).

In Azure tutti gli archivi dati elencati di seguito soddisfano i requisiti di base per OLTP e la gestione dei dati transazionali:

- [Database SQL di Azure](/azure/sql-database/)
- [SQL Server in una macchina virtuale di Azure](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
- [Database di Azure per MySQL](/azure/mysql/)
- [Database di Azure per PostgreSQL](/azure/postgresql/)

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- Si vuole usare un servizio gestito anziché gestire direttamente i propri server?

- Si ha una soluzione con specifiche dipendenze per la compatibilità con Microsoft SQL Server, MySQL o PostgreSQL? L'applicazione può limitare gli archivi dati che è possibile scegliere in base ai driver supportati per la comunicazione con l'archivio dati o può essere in grado di formulare solo alcune ipotesi riguardo al database usato.

- Si hanno requisiti particolarmente elevati per la velocità effettiva di scrittura? In caso affermativo, scegliere un'opzione in grado di fornire tabelle in memoria.

- Si ha una soluzione multi-tenant? In caso affermativo, prendere in considerazione le opzioni che supportano pool di capacità, in cui più istanze di database estraggono le risorse da un pool elastico, anziché risorse fisse per singolo database. In questo modo verrà ottimizzata la distribuzione della capacità in tutte le istanze di database e la soluzione risulterà economicamente più vantaggiosa.

- I dati devono essere leggibili con bassa latenza in più aree? In caso affermativo, scegliere un'opzione con il supporto per repliche secondarie leggibili.

- Il database deve garantire disponibilità elevata nelle diverse aree geografiche? In caso affermativo, scegliere un'opzione con il supporto per la replica geografica. Prendere in considerazione anche le opzioni che supportano il failover automatico dalla replica primaria a una replica secondaria.

- Il database ha specifici requisiti di sicurezza? In caso affermativo, esaminare le opzioni che forniscono funzionalità quali la sicurezza a livello di riga, la maschera dati e la crittografia TDE (Transparent Data Encryption).

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="general-capabilities"></a>Funzionalità generali

<!-- markdownlint-disable MD033 -->

|                              | Database SQL di Azure | SQL Server in una macchina virtuale di Azure | Database di Azure per MySQL | Database di Azure per PostgreSQL |
|------------------------------|--------------------|----------------------------------------|--------------------------|-------------------------------|
|      Servizio gestito      |        Yes         |                   No                    |           Yes            |              Yes              |
|       Piattaforma       |        N/D         |         Windows, Linux, Docker         |           N/D            |              N/D              |
| Programmabilità <sup>1</sup> |   T-SQL, .NET, R   |         T-SQL, .NET, R, Python         |  T-SQL, .NET, R, Python  |              SQL              |

<!-- markdownlint-enable MD033 -->

[1] Non è incluso il supporto per i driver client, che consente a molti linguaggi di programmazione di connettersi all'archivio dati OLTP e usarne le risorse.

### <a name="scalability-capabilities"></a>Funzionalità di scalabilità

| | Database SQL di Azure | SQL Server in una macchina virtuale di Azure| Database di Azure per MySQL | Database di Azure per PostgreSQL|
| --- | --- | --- | --- | --- | --- |
| Dimensione massima delle istanze di database | [4 TB](/azure/sql-database/sql-database-resource-limits) | 256 TB | [1 TB](/azure/mysql/concepts-limits) | [1 TB](/azure/postgresql/concepts-limits) |
| Supporto per pool di capacità  | Yes | Sì | No  | No  |
| Supporto per la scalabilità orizzontale di cluster  | No  | Yes | No  | No  |
| Scalabilità dinamica (aumento delle prestazioni)  | Yes | No  | Yes | Yes |

### <a name="analytic-workload-capabilities"></a>Funzionalità per carichi di lavoro di analisi

| | Database SQL di Azure | SQL Server in una macchina virtuale di Azure| Database di Azure per MySQL | Database di Azure per PostgreSQL|
| --- | --- | --- | --- | --- | --- |
| Tabelle temporali | Yes | Sì | No  | No  |
| Tabelle in memoria (ottimizzate per la memoria) | Yes | Sì | No  | No  |
| Supporto per columnstore | Yes | Sì | No  | No  |
| Elaborazione di query adattiva | Yes | Sì | No  | No  |

### <a name="availability-capabilities"></a>Funzionalità per la disponibilità

| | Database SQL di Azure | SQL Server in una macchina virtuale di Azure| Database di Azure per MySQL | Database di Azure per PostgreSQL|
| --- | --- | --- | --- | --- | --- |
| Repliche secondarie leggibili | Yes | Sì | No  | No  |
| Replica geografica | Yes | Sì | No  | No  |
| Failover automatico in replica secondaria | Yes | No  | No  | No |
| Ripristino temporizzato | Yes | Sì | Sì | Yes |

### <a name="security-capabilities"></a>Funzionalità di sicurezza

|                                                                                                             | Database SQL di Azure | SQL Server in una macchina virtuale di Azure | Database di Azure per MySQL | Database di Azure per PostgreSQL |
|-------------------------------------------------------------------------------------------------------------|--------------------|----------------------------------------|--------------------------|-------------------------------|
|                                             Sicurezza a livello di riga                                              |        Yes         |                  Sì                   |           Sì            |              Yes              |
|                                                Maschera dati                                                 |        Yes         |                  Sì                   |            No             |              No                |
|                                         Transparent Data Encryption                                         |        Yes         |                  Sì                   |           Sì            |              Yes              |
|                                  Limitazione dell'accesso a specifici indirizzi IP                                   |        Yes         |                  Sì                   |           Sì            |              Yes              |
|                                  Limitazione dell'accesso alla rete virtuale                                  |        Yes         |                  Sì                   |            No             |              No                |
|                                    Autenticazione di Azure Active Directory                                    |        Yes         |                  Sì                   |            No             |              No                |
|                                       Autenticazione di Active Directory                                       |         No          |                  Yes                   |            No             |              No                |
|                                         Autenticazione a più fattori                                         |        Yes         |                  Sì                   |            No             |              No                |
| Supporto per [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) |        Yes         |                  Sì                   |           Sì            |              No                |
|                                                 IP privato                                                  |         No          |                  Yes                   |           Sì            |              No                |
