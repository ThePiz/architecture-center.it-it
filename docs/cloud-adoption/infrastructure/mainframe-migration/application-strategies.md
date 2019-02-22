---
title: 'Migrazione dei mainframe: Migrazione delle applicazioni mainframe'
description: Informazioni su come eseguire la migrazione di applicazioni da ambienti mainframe ad Azure, un'infrastruttura scalabile, collaudata e a disponibilità elevata per i sistemi attualmente in esecuzione su mainframe
author: njray
ms.date: 12/26/2018
ms.openlocfilehash: dcae5077e26ab8ba9b08e0da71a5e69d0d9f62e3
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901431"
---
# <a name="mainframe-application-migration"></a>Migrazione delle applicazioni mainframe

Per la migrazione delle applicazioni da ambienti mainframe ad Azure, la maggior parte dei team adotta un approccio pragmatico: riutilizzare tutto ciò che è possibile, quindi avviare una distribuzione in più fasi in cui le applicazioni vengono riscritte o sostituite.

La migrazione delle applicazioni in genere prevede una o più delle seguenti strategie:

- Rehosting: è possibile spostare dal mainframe il codice, i programmi e le applicazioni esistenti e quindi ricompilare il codice per l'esecuzione in un emulatore mainframe ospitato in un'istanza cloud. Questo approccio in genere inizia con lo spostamento delle applicazioni in un emulatore basato sul cloud e quindi con la migrazione del database in un database basato sul cloud. Sono necessarie alcune operazioni di progettazione e refactoring, oltre che conversioni di dati e file.

    In alternativa, è possibile eseguire il rehosting mediante un provider di hosting tradizionale. Uno dei principali vantaggi del cloud è l'outsourcing della gestione dell'infrastruttura. È possibile trovare un provider di data center che ospiterà i carichi di lavoro del mainframe. Questo modello può consentire di risparmiare tempo, ridurre la dipendenza dal fornitore e produrre temporanee riduzioni dei costi.

- Ritiro: tutte le applicazioni che non sono più necessarie devono essere ritirate prima della migrazione.

- Ricostruzione: alcune organizzazioni scelgono di riscrivere completamente i programmi usando tecniche moderne. Per i costi e la complessità superiori, questo approccio è meno comune rispetto al trasferimento in modalità lift-and-shift. Spesso dopo questo tipo di migrazione è opportuno iniziare a sostituire i moduli e il codice tramite motori di trasformazione del codice.

- Sostituire: Questo approccio sostituisce le funzionalità del mainframe con funzionalità equivalenti nel cloud. Un'opzione è il software come un servizio (SaaS), che usa una soluzione creata in modo specifico per un problema aziendale, ad esempio in ambito finanziario, delle risorse umane, della produzione o ERP (Enterprise Resource Planning). Inoltre, ora sono disponibili molte app specifiche del settore per risolvere i problemi che in precedenza venivano risolti tramite soluzioni mainframe personalizzate.

È consigliabile valutare se partire dalla pianificazione dei carichi di lavoro di cui eseguire la migrazione inizialmente e quindi determinare i requisiti per lo spostamento delle applicazioni, delle basi di codice legacy e dei database associati.

## <a name="mainframe-emulation-in-azure"></a>Emulazione di mainframe in Azure

I servizi cloud di Azure sono in grado di emulare gli ambienti mainframe tradizionali, consentendo di riutilizzare le applicazioni e il codice mainframe esistenti. Alcuni componenti server comuni che è possibile emulare includono OLTP (Online Transaction Processing), batch e sistemi di inserimento dati.

### <a name="oltp-systems"></a>Sistemi OLTP

Numerosi mainframe dispongono di sistemi OLTP che elaborano migliaia o milioni di aggiornamenti per un numero molto elevato di utenti. Queste applicazioni spesso usano software per l'elaborazione delle transazioni e la gestione di moduli e schermate, come sistemi CICS (Customer Information Control System), IMS (Information Management System) e TIP (Terminal Interface Processor).

Quando si spostano applicazioni OLTP in Azure, emulatori per il monitoraggio dell'elaborazione delle transazioni mainframe sono disponibili per l'esecuzione come infrastruttura distribuita come servizio (IaaS) tramite macchine virtuali in Azure. Anche le funzionalità di gestione dei moduli e delle schermate possono essere implementate tramite server Web. Questo approccio può essere combinato con API di database, ad esempio ADO (ActiveX Data Objects), ODBC (Open Database Connectivity) e JDBC (Java Database Connectivity) per l'accesso ai dati e le transazioni.

### <a name="time-constrained-batch-updates"></a>Aggiornamenti in batch con vincoli di tempo

Molti sistemi mainframe eseguono aggiornamenti mensili o annuali di milioni di record di account, come quelli usati nel settore bancario, nelle assicurazioni e nella Pubblica Amministrazione. I mainframe gestiscono questi tipi di carichi di lavoro offrendo sistemi di gestione dei dati con una velocità effettiva elevata. I processi batch mainframe sono generalmente di natura seriale e dipendono dalle operazioni di I/O al secondo (IOPS) fornite dal backbone del mainframe per le prestazioni.

Gli ambienti batch basati sul cloud usano soluzioni di calcolo parallele e reti ad alta velocità per le prestazioni. Se è necessario ottimizzare le prestazioni dei batch, Azure offre numerose opzioni di calcolo, archiviazione e rete.

### <a name="data-ingestion-systems"></a>Sistemi di inserimento dati

I mainframe eseguono l'inserimento di batch di dati di grandi dimensioni da soluzioni per la vendita al dettaglio, i servizi finanziari, la produzione e di altro tipo. Con Azure è possibile usare semplici utilità della riga di comando, come [AzCopy](/azure/storage/common/storage-use-azcopy), per copiare dati da e verso una posizione di archiviazione. È anche possibile usare il servizio [Azure Data Factory](/azure/data-factory/introduction), che consente di inserire dati da archivi dati eterogenei per creare e pianificare flussi di lavoro basati sui dati.

Oltre agli ambienti di emulazione, Azure fornisce funzionalità di piattaforma distribuita come servizio (PaaS) e servizi di analisi che possono migliorare gli ambienti mainframe esistenti.

## <a name="migrate-oltp-workloads-to-azure"></a>Migrazione dei carichi di lavoro OLTP ad Azure

L'approccio lift-and-shift è un'opzione senza codice per eseguire rapidamente la migrazione delle applicazioni esistenti in Azure. Ogni applicazione viene sottoposta a migrazione così com'è e ciò offre i vantaggi del cloud senza i rischi o i costi delle modifiche al codice. L'uso di un emulatore per il monitoraggio dell'elaborazione delle transazioni mainframe in Azure supporta questo approccio.

Soluzioni di monitoraggio dell'elaborazione delle transazioni sono disponibili da diversi produttori e possono essere eseguite in macchine virtuali, un'opzione di infrastruttura distribuita come servizio (IaaS) in Azure. I diagrammi seguenti illustrano una migrazione di un'applicazione online supportata da IBM DB2, un sistema di gestione di database relazionali (DBMS), in un mainframe IBM z/OS. DB2 per z/OS usa i file VSAM (Virtual Storage Access Method) per archiviare i dati e ISAM (Indexed Sequential Access Method) per i file flat. Questa architettura usa anche CICS per il monitoraggio delle transazioni.

![Trasferimento in modalità lift-and-shift di un ambiente mainframe in Azure tramite software di emulazione](../../_images/mainframe-migration/mainframe-vs-azure.png)

In Azure vengono usati ambienti di emulazione per eseguire la gestione dell'elaborazione delle transazioni e i processi batch che usano JCL. Nel livello dati, DB2 viene sostituito dal [database SQL di Azure](/azure/sql-database/sql-database-technical-overview), sebbene sia possibile usare anche Microsoft SQL Server, DB2 LUW o Oracle Database. Un emulatore supporta IMS, VSAM e SEQ. Gli strumenti di gestione dei sistemi del mainframe vengono sostituiti da servizi di Azure e software di altri fornitori, che vengono eseguiti in macchine virtuali.

Le funzionalità di gestione delle schermate e immissione nei moduli è solitamente implementata mediante server Web, che possono essere combinati con API di database, ad esempio ADO, ODBC e JDBC per l'accesso ai dati e le transazioni. L'esatta gamma di componenti IaaS di Azure da usare dipende dal sistema operativo preferito. Ad esempio: 

- Macchine virtuali basate su Windows: Internet Information Server (IIS) con ASP.NET per la gestione delle schermate e la logica di business. Usare ADO.NET per l'accesso ai dati e le transazioni.

- Macchine virtuali basate su Linux: i server applicazioni basati su Java disponibili, ad esempio Apache Tomcat per la gestione delle schermate e le funzionalità aziendali basate su Java. Usare JDBC per l'accesso ai dati e le transazioni.

## <a name="migrate-batch-workloads-to-azure"></a>Migrazione dei carichi di lavoro batch ad Azure

Le operazioni batch in Azure sono diverse dal tipico ambiente batch nei mainframe. I processi batch mainframe sono generalmente di natura seriale e dipendono dalle operazioni di I/O al secondo fornite dal backbone del mainframe per le prestazioni. Gli ambienti batch basati sul cloud usano soluzioni di calcolo parallele e reti ad alta velocità per le prestazioni.

Per ottimizzare le prestazioni batch con Azure, valutare le opzioni di [calcolo](/azure/virtual-machines/windows/overview), [archiviazione](/azure/storage/blobs/storage-blobs-introduction), [rete](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) e [monitoraggio](/azure/azure-monitor/overview) indicate di seguito.

### <a name="compute"></a>Calcolo

Usare:

- Macchine virtuali con la massima velocità di clock. Le applicazioni mainframe sono spesso a thread singolo e le CPU dei mainframe hanno una velocità di clock molto elevata.

- Macchine virtuali con grandi capacità di memoria per consentire la memorizzazione nella cache dei dati e delle aree di lavoro delle applicazioni.

- Macchine virtuali con vCPU di maggiore densità per sfruttare i vantaggi dell'elaborazione multithread, se l'applicazione supporta più thread.

- Elaborazione parallela, dal momento che Azure può essere facilmente ampliato per questo tipo di elaborazione, in modo da offrire maggiore potenza di calcolo per un'esecuzione batch.

### <a name="storage"></a>Archiviazione

Usare:

- [Azure Premium SSD](/azure/virtual-machines/windows/premium-storage) o [Azure Ultra SSD](/azure/virtual-machines/windows/disks-ultra-ssd) per il massimo di operazioni di I/O al secondo disponibili.

- Striping con più dischi per un aumento delle operazioni di I/O al secondo per ogni dimensione di archiviazione.

- Partizionamento dell'archiviazione per distribuire l'I/O su più dispositivi di archiviazione di Azure.

### <a name="networking"></a>Rete

- Usare [Rete accelerata di Azure](/azure/virtual-network/create-vm-accelerated-networking-powershell) per ridurre al minimo la latenza.

### <a name="monitoring"></a>Monitoraggio

- Gli strumenti di monitoraggio, [Monitoraggio di Azure](/azure/azure-monitor/overview), [Azure Application Insights](/azure/application-insights/app-insights-overview) e anche i log di Azure consentono agli amministratori di monitorare le prestazioni delle esecuzioni batch ed eliminare i colli di bottiglia.

## <a name="migrate-development-environments"></a>Migrazione degli ambienti di sviluppo

Le architetture distribuite del cloud sono basate su un diverso set di strumenti di sviluppo, che offrono i vantaggi delle procedure e dei linguaggi di programmazione moderni. Per facilitare la transizione, è possibile usare un ambiente di sviluppo con altri strumenti progettati per emulare gli ambienti IBM z/OS. L'elenco seguente illustra le opzioni offerte da Microsoft e altri fornitori:

| Componente        | Opzioni di Azure                                                                                                                                  |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| z/OS             | Windows, Linux o UNIX                                                                                                                      |
| CICS             | Servizi di Azure offerti da Micro Focus, Oracle, GT Software (Fujitsu), TmaxSoft, Raincode e NTT Data o riscrittura con Kubernetes |
| IMS              | Servizi di Azure offerti da Micro Focus e Oracle                                                                                  |
| Assembler        | Servizi di Azure offerti da Raincode e TmaxSoft, COBOL, C o Java oppure mapping alle funzioni del sistema operativo               |
| JCL              | JCL, PowerShell o altri strumenti di scripting                                                                                                   |
| COBOL            | COBOL, C o Java                                                                                                                            |
| Natural          | Natural, COBOL, C o Java                                                                                                                  |
| FORTRAN e PL/I | FORTRAN, PL/I, COBOL, C o Java                                                                                                           |
| REXX e PL/I    | REXX, PowerShell o altri strumenti di scripting                                                                                                  |

## <a name="migrate-databases-and-data"></a>Migrazione di database e dati

La migrazione delle applicazioni in genere comporta il rehosting del livello dati. È possibile eseguire con facilità la migrazione dei database SQL Server, open source e di altri database relazionali in soluzioni completamente gestite in Azure, come [Istanza gestita di database SQL di Azure](/azure/sql-database/sql-database-managed-instance), il [servizio Database di Azure per PostgreSQL](/azure/postgresql/overview) e [Database di Azure per MySQL](/azure/mysql/overview) con il [Servizio Migrazione del database di Azure](/azure/dms/dms-overview).

È ad esempio possibile eseguire la migrazione se il livello dati del mainframe utilizza:

- IBM DB2 o un database IMS - usare database SQL di Azure, SQL Server, DB2 LUW o Oracle Database in Azure.

- VSAM e altri file flat - usare i file flat ISAM (Indexed Sequential Access Method) per SQL di Azure, SQL Server, DB2 LUW o Oracle.

- Generation Data Group (GDG) - eseguire la migrazione a file in Azure con una convenzione di denominazione ed estensioni per i nomi di file con funzionalità simili ai GDG.

Il livello dati IBM include diversi altri componenti chiave di cui è necessario eseguire la migrazione. Ad esempio, quando si esegue la migrazione di un database, è anche necessario eseguire la migrazione di una raccolta di dati contenuti nei pool, ognuno dei quali contiene dbextent, costituiti da set di dati VSAM z/OS. La migrazione deve includere la directory che identifica i percorsi dei dati nei pool di archiviazione. Inoltre, il piano di migrazione deve prendere in considerazione il log del database, che contiene una registrazione delle operazioni eseguite nel database. Un database può avere uno, due (doppio o alternativo) oppure quattro log (doppio e alternativo).

La migrazione del database include anche questi componenti:

- Gestione database: fornisce l'accesso ai dati nel database. La funzionalità di gestione database viene eseguita in una specifica partizione in un ambiente z/OS.

- Richiedente applicazioni: accetta le richieste dalle applicazioni prima di passarle a un server applicazioni.

- Adattatore risorse online: include i componenti del richiedente applicazioni per l'uso nelle transazioni CICS.

- Adattatore risorse batch: implementa i componenti del richiedente applicazioni per le applicazioni batch z/OS.

- Interactive SQL (ISQL): viene eseguito come un'applicazione CICS e un'interfaccia che consente agli utenti di immettere istruzioni SQL o comandi dell'operatore.

- Applicazione CICS: viene eseguita sotto il controllo di CICS, usando le risorse e le origini dati disponibili in CICS.

- Applicazione batch: esegue la logica di elaborazione senza una comunicazione interattiva con gli utenti, ad esempio per produrre aggiornamenti dei dati in blocco o generare report da un database.

## <a name="optimize-scale-and-throughput-for-azure"></a>Ottimizzare la scalabilità e la velocità effettiva per Azure

In generale, i mainframe impiegano la scalabilità verticale, mentre nel cloud viene usata la scalabilità orizzontale. Per ottimizzare la scalabilità e la velocità effettiva delle applicazioni di tipo mainframe in esecuzione in Azure, è importante comprendere in che modo i mainframe possono separare e isolare le applicazioni. Un mainframe z/OS usa una funzionalità denominata partizioni logiche (LPAR) per isolare e gestire le risorse per un'applicazione specifica in una singola istanza.

Ad esempio, un mainframe potrebbe usare una partizione logica (LPAR) per un'area CICS con i programmi COBOL associati e una LPAR distinta per DB2. LPAR aggiuntive vengono spesso usate per attività di sviluppo, test e ambienti di gestione temporanea.

In Azure è più comune l'uso di macchine virtuali distinte per lo stesso scopo. In genere, nelle architetture di Azure vengono distribuiti un set di macchine virtuali per il livello applicazione, un altro set di macchine virtuali per il livello dati, un ulteriore set per lo sviluppo e così via. Ogni livello di elaborazione può essere ottimizzato usando il tipo di macchine virtuali e le funzionalità più adatti per l'ambiente corrispondente.

Inoltre, ogni livello può anche fornire servizi di ripristino di emergenza appropriati. Ad esempio, le macchine virtuali di produzione e dei database possono richiedere il ripristino a caldo, mentre le macchine virtuali di test e sviluppo supportano il ripristino a freddo.

La figura seguente illustra una possibile distribuzione di Azure usando un sito primario e uno secondario. Nel sito primario, le macchine virtuali per l'ambiente di produzione, pre-produzione e test sono distribuite con disponibilità elevata. Il sito secondario è destinato al backup e al ripristino di emergenza.

![Una possibile distribuzione di Azure usando un sito primario e uno secondario](../../_images/mainframe-migration/migration-backup-DR.png)

## <a name="perform-a-staged-mainframe-to-azure"></a>Eseguire una migrazione in diverse fasi di un mainframe in Azure

Lo spostamento di soluzioni da un mainframe in Azure può comportare una migrazione *in diverse fasi*, in cui prima vengono spostate alcune applicazioni, mentre altre rimangono sul mainframe temporaneamente o definitivamente. Questo approccio in genere richiede sistemi che consentano l'interazione delle applicazioni e dei database tra il mainframe e Azure.

Uno scenario comune consiste nello spostare un'applicazione in Azure, mantenendo i dati usati dall'applicazione sul mainframe. Viene usato software specifico per abilitare le applicazioni in Azure per l'accesso ai dati dal mainframe. Fortunatamente, una vasta gamma di soluzioni garantisce l'integrazione tra Azure e gli ambienti mainframe esistenti, il supporto per gli scenari ibridi e la migrazione nel corso del tempo. Numerosi partner Microsoft, fornitori di software indipendenti e integratori di sistemi possono offrire tutta l'assistenza necessaria.

Una possibile opzione è [Microsoft Host Integration Server](https://docs.microsoft.com/host-integration-server/) (HIS), una soluzione che fornisce l'architettura DRDA (Distributed Relational Database Architecture) necessaria per consentire alle applicazioni in Azure di accedere ai dati in DB2 che rimangono sul mainframe. Altre opzioni per l'integrazione dei mainframe in Azure includono soluzioni di IBM, Attunity, Codit, altri fornitori e opzioni open source.

## <a name="partner-solutions"></a>Soluzioni partner

Se si sta valutando la migrazione di un mainframe, è disponibile un vasto ecosistema di partner in grado di offrire assistenza.

Azure fornisce un'infrastruttura scalabile, collaudata e a disponibilità elevata per i sistemi attualmente in esecuzione su mainframe. La migrazione di alcuni carichi di lavoro può essere eseguita con relativa facilità. Per altri carichi di lavoro che dipendono dal software di sistemi legacy, come CICS e IMS, è possibile eseguire il rehosting tramite le soluzioni dei partner ed effettuare la migrazione ad Azure nel corso del tempo. Indipendentemente dall'opzione scelta, Microsoft e i relativi partner sono disponibili per semplificare l'ottimizzazione di Azure mantenendo le funzionalità del software del sistema mainframe.

Per indicazioni dettagliate sulla scelta di una soluzione dei partner, vedere [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

## <a name="learn-more"></a>Altre informazioni

Per altre informazioni, vedere le seguenti risorse:

- [Inizia a usare Azure](/azure)

- [Platform Modernization Alliance: Migrazione dei mainframe](https://www.platformmodernization.org/pages/mainframe.aspx)

- [Distribuire IBM DB2 pureScale in Azure](https://azure.microsoft.com/resources/deploy-ibm-db2-purescale-on-azure)

- [Documentazione di Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
