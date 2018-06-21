---
title: Estensione di soluzioni dati locali al cloud
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 66fc225dc123202ba587d82f15ea0883e1bbf3b5
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
ms.locfileid: "29289687"
---
# <a name="extending-on-premises-data-solutions-to-the-cloud"></a>Estensione di soluzioni dati locali al cloud

Quando le organizzazioni spostano carichi di lavoro e dati nel cloud, spesso i data center locali continuano a svolgere un ruolo importante. Il termine *cloud ibrido* fa riferimento a una combinazione di cloud pubblico e data center locali, per creare un ambiente IT integrato che comprende entrambi. Alcune organizzazioni usano il cloud ibrido come percorso per eseguire nel tempo la migrazione dell'intero data center al cloud. Altre organizzazioni usano i servizi cloud per estendere l'infrastruttura locale esistente. 

Questo articolo illustra alcune considerazioni e procedure consigliate per la gestione dei dati in una soluzione di cloud ibrido.

## <a name="when-to-use-a-hybrid-solution"></a>Quando usare una soluzione ibrida

Si consideri l'uso di una soluzione ibrida negli scenari seguenti:

* Come strategia di transizione durante una migrazione a lungo termine a una soluzione cloud completamente nativa.
* Quando normative o criteri non consentono lo spostamento di carichi di lavoro o dati specifici nel cloud.
* Per il ripristino di emergenza e la tolleranza di errore, tramite la replica dei dati e dei servizi tra ambienti locali e cloud.
* Per ridurre la latenza tra posizioni remote e il data center locale, ospitando parte dell'architettura in Azure.

## <a name="challenges"></a>Problematiche

* Creazione di un ambiente coerente in termini di sicurezza, gestione e sviluppo e necessità di evitare la duplicazione del lavoro.

* Creazione di una connessione dati affidabile, a bassa latenza e sicura tra ambienti locali e cloud.

* Replica dei dati e modifica di applicazioni e strumenti per l'uso degli archivi dati corretti all'interno di ciascun ambiente.

* Protezione e crittografia dei dati ospitati nel cloud ma accessibili dall'ambiente locale o viceversa.

## <a name="on-premises-data-stores"></a>Archivi dati locali

Gli archivi dati locali includono database e file. Le ragioni per mantenere questi archivi locali possono essere diverse. Potrebbero essere presenti normative o criteri che non consentono lo spostamento di carichi di lavoro o dati specifici nel cloud. Preoccupazioni relative alla sicurezza, alla privacy o alla sovranità dei dati potrebbero favorire il posizionamento in locale. Durante una migrazione, può essere opportuno mantenere alcuni dati in locale in un'applicazione di cui non è stata ancora eseguita la migrazione.

Di seguito sono riportate alcune considerazioni sul posizionamento dei dati dell'applicazione in un cloud pubblico:

* **Costo**. Il costo dell'archiviazione in Azure può essere notevolmente inferiore rispetto al costo di gestione di una risorsa di archiviazione con caratteristiche simili in un data center locale. Naturalmente, molte aziende hanno investito in soluzioni SAN di fascia alta, pertanto tali vantaggi in termini di costi potrebbero raggiungere la piena produttività solo quando l'hardware esistente sarà ormai superato.
* **Scalabilità elastica**. La pianificazione e la gestione della crescita della capacità dei dati in un ambiente locale possono essere complesse, in particolare quando la crescita dei dati è difficile da prevedere. Queste applicazioni possono sfruttare l'archiviazione virtualmente illimitata e con capacità su richiesta disponibile nel cloud. Questa considerazione è meno rilevante per le applicazioni costituite da set di dati con dimensioni relativamente statiche.
* **Ripristino di emergenza**. I dati archiviati in Azure possono essere replicati automaticamente all'interno di un'area di Azure e in più aree geografiche. Negli ambienti ibridi queste stesse tecnologie possono essere usate per eseguire la replica tra archivi dati locali e basati su cloud.

## <a name="extending-data-stores-to-the-cloud"></a>Estensione degli archivi dati al cloud

Sono disponibili diverse opzioni per l'estensione degli archivi dati locali al cloud. Un'opzione consiste nell'avere repliche locali e cloud. Questa opzione consente di ottenere un livello elevato di tolleranza di errore, ma può richiedere l'apporto di modifiche alle applicazioni per la connessione all'archivio dati appropriato in caso di failover.

Un'altra opzione consiste nello spostare una parte dei dati nella risorsa di archiviazione cloud, mantenendo in locale i dati più recenti o a cui si accede più frequentemente. Questo metodo può rappresentare un'opzione più conveniente per l'archiviazione a lungo termine, nonché migliorare i tempi di risposta di accesso ai dati tramite la riduzione del set di dati operativo.

Una terza opzione consiste nel mantenere tutti i dati in locale e usare il cloud computing per ospitare le applicazioni. A tale scopo, è possibile ospitare l'applicazione nel cloud e connetterla all'archivio dati locale tramite una connessione sicura. 

## <a name="azure-stack"></a>Azure Stack

Per una soluzione completa di cloud ibrido, si consiglia di usare [Microsoft Azure Stack](/azure/azure-stack/). Azure Stack è una piattaforma di cloud ibrido che consente di fornire i servizi di Azure dal data center. In questo modo è possibile mantenere la coerenza tra gli ambienti locali e Azure, in quanto vengono usati strumenti identici e non è richiesta alcuna modifica del codice. 

Di seguito sono riportati alcuni casi d'uso per Azure e Azure Stack:

* **Soluzioni disconnesse ed edge**. Risolvere i problemi relativi ai requisiti di latenza e di connettività elaborando i dati localmente in Azure Stack e quindi aggregandoli in Azure per altre analisi, tramite la logica dell'applicazione comune in entrambi. 
* **Applicazioni cloud che soddisfano diverse normative**. Sviluppare e distribuire applicazioni in Azure, con la flessibilità necessaria per distribuire le stesse applicazioni in locale in Azure Stack per soddisfare i requisiti normativi o relativi ai criteri.
* **Modello di applicazioni cloud in locale**. Usare Azure per aggiornare ed estendere le applicazioni esistenti o creare di nuove. Usare processi coerenti per DevOps in Azure nel cloud e in Azure Stack in locale.

## <a name="sql-server-data-stores"></a>Archivi dati di SQL Server

Se si esegue SQL Server locale, è possibile usare il servizio di archiviazione BLOB di Microsoft Azure per il backup e il ripristino. Per altre informazioni, vedere [Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service). Questa funzionalità offre un'archiviazione esterna illimitata e la possibilità di condividere gli stessi backup tra un'istanza di SQL Server in esecuzione in locale e un'istanza di SQL Server in esecuzione in una macchina virtuale in Azure. 

Il [database SQLdi Azure](/azure/sql-database/) è un database relazionale gestito come servizio. Il database SQL di Azure usa il motore di Microsoft SQL Server, pertanto le applicazioni possono accedere ai dati nello stesso modo con entrambe le tecnologie. Il database SQL di Azure può anche essere combinato con SQL Server in diversi modi utili. Ad esempio, la funzionalità [SQL Server Stretch Database](/sql/sql-server/stretch-database/stretch-database) consente a un'applicazione di accedere a una singola tabella in un database di SQL Server anche se tutte le righe della tabella o parte di esse sono archiviate nel database SQL di Azure. Questa tecnologia sposta automaticamente nel cloud i dati a cui non si accede per un determinato periodo di tempo. Le applicazioni che leggono i dati non sono in grado di rilevare che i dati sono stati spostati nel cloud.

Mantenere gli archivi dati in locale e nel cloud può risultare complesso se si vuole mantenere i dati sincronizzati. È possibile risolvere questo problema tramite la [sincronizzazione dati SQL ](/azure/sql-database/sql-database-sync-data), un servizio basato sul database SQL di Azure che consente di sincronizzare bidirezionalmente i dati selezionati tra più database SQL di Azure e istanze di SQL Server. La sincronizzazione dati consente di mantenere i dati aggiornati nei diversi archivi dati, ma non deve essere usata per il ripristino di emergenza o per la migrazione dal server SQL locale al database SQL di Azure.

Per il ripristino di emergenza e la continuità aziendale è possibile usare i [gruppi di disponibilità AlwaysOn](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) per eseguire la replica dei dati in due o più istanze di SQL Server, alcune delle quali possono essere eseguite in macchine virtuali di Azure in un'altra area geografica.

## <a name="network-shares-and-file-based-data-stores"></a>Condivisioni di rete e archivi dati basati su file

In un'architettura di cloud ibrido un'organizzazione in genere mantiene i file più recenti in locale e archivia i file meno recenti nel cloud. Questa procedura, talvolta denominata suddivisione file, consente di accedere facilmente a set di file in locale e ospitati nel cloud. Questo approccio consente di ridurre al minimo l'utilizzo della larghezza di banda di rete e i tempi di accesso per i file più recenti, ai quali probabilmente si accede più spesso. Allo stesso tempo si ottengono i vantaggi dell'archiviazione basata su cloud per i dati archiviati. 

Le organizzazioni possono anche decidere di spostare completamente le condivisioni di rete nel cloud. Questa operazione è utile, ad esempio, se anche le applicazioni che vi accedono si trovano nel cloud. Questa procedura può essere eseguita tramite gli strumenti di [orchestrazione dati](../technology-choices/pipeline-orchestration-data-movement.md).


[Azure StorSimple](/azure/storsimple/) offre la soluzione di archiviazione integrata più completa per gestire le attività di archiviazione tra i dispositivi locali e l'archiviazione cloud di Azure. StorSimple è una soluzione SAN (storage area network) efficiente, dai costi contenuti e facilmente gestibile che elimina molti problemi e spese associati agli archivi dell'organizzazione e alla protezione dei dati. Usa il dispositivo proprietario StorSimple serie 8000, si integra con i servizi cloud e offre un set di strumenti di gestione integrati.

È possibile usare le condivisioni di rete locali insieme all'archiviazione file basata su cloud anche tramite [File di Azure](/azure/storage/files/storage-files-introduction). File di Azure offre condivisioni file completamente gestite, accessibili tramite il protocollo standard [SMB ](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx?f=255&MSPPError=-2147217396) (Server Message Block), noto anche come CIFS (Common Internet File System). È possibile installare File di Azure come condivisione file nel computer locale o usarlo con le applicazioni esistenti che accedono a file locali o a file di condivisione di rete.

Per sincronizzare le condivisioni file in File di Azure con Windows Server in locale, usare [Sincronizzazione file di Azure](/azure/storage/files/storage-sync-files-planning). Uno dei vantaggi principali di Sincronizzazione file di Azure è la possibilità di suddividere i file tra il server di file locale e File di Azure. In questo modo è possibile mantenere in locale solo i più file recenti e usati più di recente. 

Per altre informazioni, vedere [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](/azure/storage/common/storage-decide-blobs-files-disks).

## <a name="hybrid-networking"></a>Rete ibrida

Questo articolo ha illustrato le soluzioni di dati ibride, ma è opportuno considerare anche come estendere la rete locale ad Azure. Per altre informazioni su questo aspetto delle soluzioni ibride, vedere gli argomenti seguenti:

- [Scegliere una soluzione per la connessione di una rete locale ad Azure.](../../reference-architectures/hybrid-networking/considerations.md)
- [Hybrid network reference architectures](../../reference-architectures/hybrid-networking/index.md) (Architetture di riferimento di Azure)

