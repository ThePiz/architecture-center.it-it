---
title: Ripristino dal danneggiamento o dall'eliminazione accidentale dei dati
description: "Articolo incentrato sul ripristino dal danneggiamento dei dati o dall'eliminazione accidentale di dati e sulla progettazione di applicazioni resilienti, a disponibilità elevata e con tolleranza di errore, oltre che sulla pianificazione del ripristino di emergenza."
author: adamglick
ms.date: 08/18/2016
ms.openlocfilehash: b75c774f85c42f64472167897f08a7302ab50a3f
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
[!INCLUDE [header](../_includes/header.md)]
# <a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Informazioni tecniche sulla resilienza di Azure: ripristino dal danneggiamento o dall'eliminazione accidentale dei dati
Un piano affidabile per la continuità aziendale deve includere un piano in caso di danneggiamento o eliminazione accidentale dei dati. Le informazioni seguenti illustrano il ripristino dopo il danneggiamento o l'eliminazione accidentale di dati a causa di errori dell'applicazione o di errore dell'operatore.

## <a name="virtual-machines"></a>Macchine virtuali
Per proteggere le macchine virtuali di Azure, definite a volte VM IaaS (Infrastructure as a Service), da errori dell'applicazione o dall'eliminazione accidentale, usare [Backup di Azure](https://azure.microsoft.com/services/backup/). Backup di Azure consente la creazione di backup coerenti in più dischi di macchine virtuali. È inoltre possibile replicare un insieme di credenziali per il backup in diverse aree per offrire il ripristino dalla perdita di un'area.

## <a name="storage"></a>Archiviazione
Si noti che anche se l'Archiviazione di Azure offre la resilienza dei dati tramite le repliche automatiche, ciò non impedisce al codice dell'applicazione o agli sviluppatori/utenti di danneggiare i dati tramite eliminazione accidentale o indesiderata, aggiornamento e così via. Per mantenere la conformità dei dati in caso di errore dell'applicazione o da parte dell'utente sono richieste tecniche più avanzate, ad esempio la copia dei dati in un percorso di archiviazione secondario con un log di controllo. Gli sviluppatori possono usare la [funzionalità snapshot](https://msdn.microsoft.com/library/azure/ee691971.aspx)dei BLOB che consente di creare snapshot di sola lettura di momenti specifici del contenuto del BLOB. Questa tecnica può essere usata come base per una soluzione di conformità dei dati per i BLOB di Archiviazione di Azure.

### <a name="blob-and-table-storage-backup"></a>Backup dell'archivio BLOB e tabelle
Nonostante BLOB e tabelle siano altamente durevoli, rappresentano sempre lo stato corrente dei dati. Il recupero da una modifica o da un'eliminazione di dati indesiderata può richiedere il ripristino dei dati a uno stato precedente. A questo scopo, è possibile sfruttare le funzionalità disponibili in Azure per l'archiviazione e il mantenimento di copie temporizzate.

Per i BLOB di Azure è possibile eseguire backup temporizzati tramite la [funzionalità per la creazione di snapshot di BLOB](https://msdn.microsoft.com/library/ee691971.aspx). Per ogni snapshot viene addebitato solo lo spazio richiesto per l'archiviazione delle differenze all'interno del BLOB dall'ultimo stato dello snapshot. Gli snapshot dipendono dall'esistenza del BLOB originale su cui si basano, quindi è consigliabile un'operazione di copia in un altro BLOB o in un altro account di archiviazione. In questo modo si garantisce che i dati di backup vengano correttamente protetti da eliminazioni accidentali. Per le tabelle di Azure è possibile eseguire copie temporizzate in una tabella diversa o in BLOB di Azure. Informazioni aggiuntive più dettagliate ed esempi relativi all'esecuzione di backup a livello di applicazione di tabelle e BLOB sono disponibili qui:

* [Protezione delle tabelle da errori dell'applicazione](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
* [Protezione dei BLOB da errori dell'applicazione](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

## <a name="database"></a>Database
Sono disponibili alcune opzioni per la [continuità aziendale](/azure/sql-database/sql-database-business-continuity/) (backup, ripristino) per il database SQL di Azure. I database possono essere copiati tramite la funzionalità di [copia del database](/azure/sql-database/sql-database-copy/) o tramite l'[esportazione](/azure/sql-database/sql-database-export/) e l'[importazione](https://msdn.microsoft.com/library/hh710052.aspx) di un file bacpac di SQL Server. A differenza di un file bacpac generato dal servizio di importazione/esportazione, la funzionalità di copia del database fornisce risultati coerenti dal punto di vista transazionale. Entrambe le opzioni vengono eseguite come servizi basati su coda nel data center e non forniscono attualmente un contratto di servizio sul tempo necessario per il completamento.

> [!NOTE]
> Le opzioni per la copia del database e l'importazione/esportazione comportano un livello di carico significativo nel database di origine. Possono attivare eventi di contesa o di limitazione delle risorse.
> 
> 

### <a name="sql-database-backup"></a>Backup del database SQL
I backup temporizzati per il database SQL di Microsoft Azure vengono creati tramite la [copia del database SQL di Azure](/azure/sql-database/sql-database-copy/). È possibile usare questo comando per creare una copia coerente dal punto di vista transazionale di un database nello stesso server di database logico o in un server diverso. In entrambi i casi, la copia del database è completamente funzionale e indipendente dal database di origine. Ogni copia creata rappresenta un'opzione di recupero temporizzato. È possibile recuperare completamente lo stato del database rinominando il nuovo database con il nome del database di origine. In alternativa, è possibile recuperare un subset specifico di dati dal nuovo database usando query Transact-SQL. Per altre informazioni sul database SQL, vedere [Panoramica della continuità aziendale del database SQL di Azure](/azure/sql-database/sql-database-business-continuity/).

### <a name="sql-server-on-virtual-machines-backup"></a>Backup di SQL Server nelle macchine virtuali
Sono disponibili due opzioni per SQL Server usato con macchine virtuali IaaS (Infrastructure as a Service) di Azure, ovvero i backup tradizionali e il log shipping. I backup tradizionali consentono di ripristinare a un momento specifico, ma il processo di recupero è lento. Il ripristino dei backup tradizionali richiede inizialmente l'esecuzione di un backup completo e quindi l'applicazione di tutti i backup eseguiti successivamente. La seconda opzione consiste nel configurare una sessione di log shipping per ritardare il ripristino dei backup del log, ad esempio di due ore, in modo da disporre di una finestra per il ripristino da errori nel server primario.

## <a name="other-azure-platform-services"></a>Altri servizi della piattaforma Azure
Alcuni servizi della piattaforma Azure archiviano le informazioni in un account di archiviazione controllato dall'utente o nel database SQL di Azure. In caso di eliminazione o danneggiamento dell'account o della risorsa di archiviazione, si potrebbero verificare errori gravi relativi al servizio. In questi casi è importante mantenere i backup che consentono di ricreare tali risorse se vengono eliminate o danneggiate.

Per i siti Web di Azure e i servizi mobili di Azure è necessario eseguire il backup e gestire i database associati. Per i servizi multimediali e le macchine virtuali di Azure è necessario gestire l'account di archiviazione di Azure associato e tutte le risorse dell'account. Ad esempio, per le macchine virtuali è necessario eseguire il backup e gestire i dischi di macchina virtuale nel servizio di archiviazione BLOB di Azure.

## <a name="checklists-for-data-corruption-or-accidental-deletion"></a>Elenchi di controllo per il danneggiamento o l'eliminazione accidentale dei dati
## <a name="virtual-machines-checklist"></a>Elenco di controllo per le macchine virtuali
1. Vedere la sezione Macchine virtuali di questo documento.
2. Eseguire il backup e gestire i dischi delle VM con Backup di Azure oppure nel proprio sistema mediante il servizio di archiviazione BLOB di Azure e gli snapshot VHD.

## <a name="storage-checklist"></a>Elenco di controllo per l'archiviazione
1. Vedere la sezione Archiviazione di questo documento.
2. Eseguire regolarmente il backup delle risorse di archiviazione essenziali.
3. Prendere in considerazione l'uso della funzionalità snapshot per i BLOB.

## <a name="database-checklist"></a>Elenco di controllo per database
1. Vedere la sezione Database di questo documento.
2. Creare backup temporizzati usando il comando per la copia del database.

## <a name="sql-server-on-virtual-machines-backup-checklist"></a>Elenco di controllo per il backup di SQL Server nelle macchine virtuali
1. Vedere la sezione Backup di SQL Server nelle macchine virtuali di questo documento.
2. Usare le tecniche tradizionali di backup e ripristino.
3. Creare una sessione di log shipping ritardata.

## <a name="web-apps-checklist"></a>Elenco di controllo per app Web
1. Eseguire il backup e gestire l'eventuale database associato.

## <a name="media-services-checklist"></a>Elenco di controllo per Servizi multimediali
1. Eseguire il backup e gestire le risorse di archiviazione associate.

## <a name="more-information"></a>Altre informazioni
Per altre informazioni sulle funzionalità di backup e ripristino di Azure, vedere [Scenari di archiviazione, backup e ripristino](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).


