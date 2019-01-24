---
title: Ripristino dal danneggiamento o dall'eliminazione accidentale dei dati
description: Informazioni sul ripristino in seguito a danneggiamento o eliminazione accidentale dei dati, progettazione di applicazioni resilienti a disponibilità elevata e tolleranza di errore e pianificazione del ripristino di emergenza.
author: MikeWasson
ms.date: 11/11/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency
ms.openlocfilehash: e28f26683c6d7dba196d4351ef3942830c9e7fc2
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486310"
---
# <a name="recover-from-data-corruption-or-accidental-deletion"></a>Ripristino dal danneggiamento o dall'eliminazione accidentale dei dati

Un piano affidabile per la continuità aziendale deve includere un piano in caso di danneggiamento o eliminazione accidentale dei dati. Le informazioni seguenti illustrano il ripristino dopo il danneggiamento o l'eliminazione accidentale di dati a causa di errori dell'applicazione o di errore dell'operatore.

## <a name="virtual-machines"></a>Macchine virtuali

Per proteggere le macchine virtuali (VM) di Azure da errori delle applicazioni o dall'eliminazione accidentale, usare [Backup di Azure](/azure/backup/). Backup di Azure consente la creazione di backup coerenti in più dischi di macchine virtuali. È inoltre possibile replicare un insieme di credenziali per il backup in diverse aree per offrire il ripristino dalla perdita di un'area.

## <a name="storage"></a>Archiviazione

Archiviazione di Azure offre la resilienza dei dati tramite repliche automatizzate. Ciò non impedisce tuttavia al codice delle applicazioni o agli utenti di danneggiare i dati, intenzionalmente o accidentalmente. Per mantenere la conformità dei dati in caso di errore dell'applicazione o da parte dell'utente sono richieste tecniche più avanzate, ad esempio la copia dei dati in un percorso di archiviazione secondario con un log di controllo.

- **BLOB in blocchi**. Creare uno snapshot temporizzato di ogni BLOB in blocchi. Per ulteriori informazioni, vedere [Creazione di uno Snapshot di un BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob). Per ogni snapshot viene addebitato solo lo spazio richiesto per l'archiviazione delle differenze all'interno del BLOB dall'ultimo stato dello snapshot. Gli snapshot dipendono dall'esistenza del BLOB originale su cui si basano, quindi è consigliabile un'operazione di copia in un altro BLOB o in un altro account di archiviazione. In questo modo si garantisce che i dati di backup vengano correttamente protetti da eliminazioni accidentali. È possibile usare [AzCopy](/azure/storage/common/storage-use-azcopy) o [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full) per copiare i BLOB in un altro account di archiviazione.

- **File**. Usare gli [snapshot di condivisione](/azure/storage/files/storage-snapshots-files), AzCopy o PowerShell per copiare i file in un altro account di archiviazione.

- **Tabelle**. Usare AzCopy per esportare i dati delle tabelle in un altro account di archiviazione in un'altra area.

## <a name="database"></a>Database

### <a name="azure-sql-database"></a>Database SQL di Azure

Il database SQL esegue automaticamente una combinazione di backup completi su base settimanale, backup differenziali del database di backup ogni ora e backup dei log delle transazioni ogni 5-10 minuti per proteggere l'azienda dalla perdita di dati. Usare il ripristino temporizzato per ripristinare un database a un momento precedente. Per altre informazioni, vedere:

- [Ripristinare un database SQL di Azure mediante i backup automatici del database](/azure/sql-database/sql-database-recovery-using-backups)

- [Panoramica della continuità aziendale del database SQL di Azure](/azure/sql-database/sql-database-business-continuity)

### <a name="sql-server-on-vms"></a>SQL Server in VM

In caso di esecuzione di SQL Server in VM, sono disponibili due opzioni: backup tradizionali e log shipping. I backup tradizionali consentono il ripristino a un momento specifico, ma il processo di recupero è lento. Il ripristino dei backup tradizionali richiede inizialmente l'esecuzione di un backup completo e quindi l'applicazione di tutti i backup eseguiti successivamente. La seconda opzione consiste nel configurare una sessione di log shipping per ritardare il ripristino dei backup del log, ad esempio di due ore, in modo da disporre di una finestra per il ripristino da errori nel server primario.

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB esegue automaticamente il backup a intervalli regolari. I backup vengono archiviati separatamente in un altro servizio di archiviazione e replicati a livello globale per garantire la resilienza in caso di emergenze a livello di area. Se si elimina accidentalmente il database o la raccolta, è possibile inviare un ticket di supporto o contattare il supporto di Azure per ripristinare i dati dall'ultimo backup automatico. Per altre informazioni, vedere [Backup online automatico e ripristino con Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore).

### <a name="azure-database-for-mysql-azure-database-for-postgresql"></a>Database di Azure per MySQL e Database di Azure per PostgreSQL

Quando si usa Database di Azure per MySQL o Database di Azure per PostgreSQL, il servizio di database esegue automaticamente un backup del servizio ogni cinque minuti. L'uso di questa funzionalità di backup automatico permette di ripristinare il server e tutti i suoi database in un nuovo server a un precedente punto temporizzato. Per altre informazioni, vedere:

- [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per MySQL tramite il portale di Azure](/azure/mysql/howto-restore-server-portal)

- [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando il portale di Azure](/azure/postgresql/howto-restore-server-portal)
