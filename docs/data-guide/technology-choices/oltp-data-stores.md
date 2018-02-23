---
title: Scelta di un archivio dati OLTP
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 1c27d7d5f3b78f40822de6b77664dbf49b1367f6
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-an-oltp-data-store-in-azure"></a>Scelta di un archivio dati OLTP in Azure

OLTP (Online Transaction Processing) è una tecnologia per la gestione dei dati transazionali e l'elaborazione delle transazioni. Questo argomento mette a confronto le opzioni disponibili in Azure per le soluzioni OLTP.

> [!NOTE]
> Per altre informazioni sull'uso di un archivio dati OLTP, vedere [OLTP (Online Transaction Processing)](../scenarios/online-analytical-processing.md).

## <a name="what-are-your-options-when-choosing-an-oltp-data-store"></a>Opzioni disponibili per la scelta di un archivio dati OLTP

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
| | Database SQL di Azure | SQL Server in una macchina virtuale di Azure | Database di Azure per MySQL | Database di Azure per PostgreSQL |
| --- | --- | --- | --- | --- | --- |
| Servizio gestito | Sì | No  | Sì | Sì |
| Piattaforma | N/D | Windows, Linux, Docker | N/D | N/D |
| Programmabilità <sup>1</sup> | T-SQL, .NET, R | T-SQL, .NET, R, Python | T-SQL, .NET, R, Python | SQL | SQL |

[1] Non è incluso il supporto per i driver client, che consente a molti linguaggi di programmazione di connettersi all'archivio dati OLTP e usarne le risorse.

### <a name="scalability-capabilities"></a>Funzionalità di scalabilità
| | Database SQL di Azure | SQL Server in una macchina virtuale di Azure| Database di Azure per MySQL | Database di Azure per PostgreSQL|
| --- | --- | --- | --- | --- | --- |
| Dimensione massima delle istanze di database | [4 TB](/azure/sql-database/sql-database-resource-limits) | 256 TB | [1 TB](/azure/mysql/concepts-limits) | [1 TB](/azure/postgresql/concepts-limits) |
| Supporto per pool di capacità  | Sì | Sì | No  | No  |
| Supporto per la scalabilità orizzontale di cluster  | No  | Sì | No  | No  |
| Scalabilità dinamica (aumento delle prestazioni)  | Sì | No  | Sì | Sì |

### <a name="analytic-workload-capabilities"></a>Funzionalità per carichi di lavoro di analisi
| | Database SQL di Azure | SQL Server in una macchina virtuale di Azure| Database di Azure per MySQL | Database di Azure per PostgreSQL|
| --- | --- | --- | --- | --- | --- | 
| Tabelle temporali | Sì | Sì | No  | No  |
| Tabelle in memoria (ottimizzate per la memoria) | Sì | Sì | No  | No  |
| Supporto per columnstore | Sì | Sì | No  | No  |
| Elaborazione di query adattiva | Sì | Sì | No  | No  |

### <a name="availability-capabilities"></a>Funzionalità per la disponibilità
| | Database SQL di Azure | SQL Server in una macchina virtuale di Azure| Database di Azure per MySQL | Database di Azure per PostgreSQL|
| --- | --- | --- | --- | --- | --- | 
| Repliche secondarie leggibili | Sì | Sì | No  | No  | 
| Replica geografica | Sì | Sì | No  | No  | 
| Failover automatico in replica secondaria | Sì | No  | No  | No |
| Ripristino temporizzato | Sì | Sì | Sì | Sì |

### <a name="security-capabilities"></a>Funzionalità di sicurezza
| | Database SQL di Azure | SQL Server in una macchina virtuale di Azure| Database di Azure per MySQL | Database di Azure per PostgreSQL|
| --- | --- | --- | --- | --- | --- | 
| Sicurezza a livello di riga | Sì | Sì | Sì | Sì |
| Maschera dati | Sì | Sì | No  | No  |
| Transparent Data Encryption | Sì | Sì | Sì | Sì |
| Limitazione dell'accesso a specifici indirizzi IP | Sì | Sì | Sì | Sì |
| Limitazione dell'accesso alla rete virtuale | Sì | Sì | No  | No  |
| Autenticazione di Azure Active Directory | Sì | Sì | No  | No  |
| Autenticazione di Active Directory | No  | Sì | No  | No  |
| Autenticazione a più fattori | Sì | Sì | No  | No  |
| Supporto per [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Sì | Sì | Sì | No  | No  |
| IP privato | No  | Sì | Sì | No  | No  |

