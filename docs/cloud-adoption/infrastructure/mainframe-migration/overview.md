---
title: 'Migrazione dei mainframe: Panoramica'
description: Informazioni su come eseguire la migrazione di applicazioni da ambienti mainframe ad Azure, un'infrastruttura scalabile, collaudata e a disponibilità elevata per i sistemi attualmente in esecuzione su mainframe.
author: njray
ms.date: 12/27/2018
ms.openlocfilehash: 41fc799f15500276ada1667121e5f1fce3413a3a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901368"
---
# <a name="mainframe-migration-overview"></a>Panoramica della migrazione dei mainframe

Molte aziende e organizzazioni possono trarre vantaggio della migrazione nel cloud di alcuni o tutti i carichi di lavoro, le applicazioni e i database su mainframe. Azure offre funzionalità simili a quelle dei mainframe su scala cloud, senza molti degli svantaggi associati ai mainframe.

Il termine mainframe in genere fa riferimento a un sistema informatico di grandi dimensioni, ma la maggior parte dei mainframe attualmente distribuiti è costituita da server IBM System Z o sistemi compatibili con IBM che eseguono MVS, DOS, VSE, OS/390 o z/OS. I sistemi mainframe continuano a essere usati in numerosi settori per eseguire sistemi con informazioni importanti e in scenari estremamente specifici, ad esempio ambienti IT con un uso intensivo di transazioni di grandi dimensioni e con volumi elevati.

La migrazione al cloud consente alle aziende di modernizzare la propria infrastruttura. Con i servizi cloud è possibile rendere le applicazioni mainframe, e il relativo valore, disponibili come un carico di lavoro ogni volta che l'organizzazione ne ha bisogno. Molti carichi di lavoro possono essere trasferiti in Azure con semplici modifiche del codice, ad esempio aggiornando i nomi dei database. È possibile eseguire la migrazione dei carichi di lavoro più complessi con un approccio graduale.

La maggior parte delle aziende Fortune 500 esegue già Azure per i propri carichi di lavoro critici. I notevoli vantaggi di Azure in termini economici incentivano molti progetti di migrazione. Le aziende in genere spostano in Azure prima i carichi di lavoro di sviluppo e test, quindi DevOps, la posta elettronica e il ripristino di emergenza come servizio.

## <a name="intended-audience"></a>Destinatari

Questa guida è destinata ai professionisti che stanno valutando una migrazione o l'aggiunta di servizi cloud come un'opzione per il proprio ambiente IT.

Le indicazioni fornite consentono alle organizzazioni IT di avviare la conversazione sulla migrazione. Poiché è probabile che si abbia maggiore familiarità con Azure e le infrastrutture basate sul cloud rispetto ai mainframe, la guida inizia con una panoramica del funzionamento dei mainframe e prosegue illustrando varie strategie per determinare di quali elementi eseguire la migrazione e come procedere.

## <a name="mainframe-architecture"></a>Architettura dei mainframe

I mainframe sono stati progettati alla fine degli anni '50 come server con scalabilità verticale per l'esecuzione di volumi elevati di transazioni online ed elaborazione batch. Per questo motivo, i mainframe dispongono di software per moduli di transazioni online (talvolta denominati "schermate verdi") e sistemi di I/O ad alte prestazioni per l'elaborazione delle esecuzioni batch.

I mainframe vengono considerati sistemi con una disponibilità e un'affidabilità molto elevate e sono noti per la capacità di eseguire un numero enorme di transazioni online e processi batch. Una transazione è costituita da un elemento di elaborazione avviato da una singola richiesta, in genere un utente a un terminale. Le transazioni possono anche provenire da altre origini, come pagine Web, workstation remote e applicazioni di altri sistemi informatici. Una transazione può anche essere attivata automaticamente a un orario predefinito, come mostrato nella figura seguente.

![Componenti di una tipica architettura mainframe IBM](../../_images/mainframe-migration/zOS-architectural-layers.png)

Una tipica architettura mainframe IBM include i seguenti componenti comuni:

- **Sistemi front-end:** gli utenti possono avviare le transazioni da terminali, pagine Web o workstation remote. Le applicazioni mainframe spesso dispongono di interfacce utente personalizzate che possono essere conservate dopo la migrazione ad Azure. Vengono ancora usati emulatori di terminali per l'accesso alle applicazioni mainframe, anch'essi denominati "schermate verdi".

- **Livello applicazione:** i mainframe in genere includono un sistema CICS (Customer Information Control System), una suite di gestione delle transazioni per i mainframe IBM z/OS che viene spesso usata con IBM Information Management System (IMS), una funzionalità di gestione delle transazioni basata su messaggi. I sistemi batch gestiscono gli aggiornamenti dei dati con una velocità effettiva elevata per grandi volumi di record di account.

- **Codice:** i linguaggi di programmazione usati dai mainframe includono COBOL, Fortran, PL/I e Natural. Job Control Language (JCL) viene usato per lavorare con z/OS.

- **Livello database:** un sistema di gestione di database relazionali (DBMS) molto comune per z/OS è IBM DD2. Gestisce strutture di dati denominate *dbspace*, che contengono una o più tabelle e vengono assegnate a pool di archiviazione di set di dati fisici denominati *dbextent*. Due importanti componenti dei database sono la directory che identifica i percorsi dei dati nei pool di archiviazione e il log che contiene una registrazione delle operazioni eseguite nel database. Sono supportati vari formati di dati per i file flat. DB2 per z/OS in genere usa set di dati VSAM (Virtual Storage Access Method) per archiviare i dati.

- **Livello di gestione:** i mainframe IBM includono software di pianificazione come TWS-OPC, strumenti per la gestione della stampa e dell'output come CA-SAR e SPOOL e un sistema di controllo del codice sorgente. Il controllo di accesso sicuro per z/OS viene gestito da Resource Access Control Facility (RACF). Una funzionalità di gestione database fornisce l'accesso ai dati nel database e viene eseguita in una specifica partizione in un ambiente z/OS.

- **LPAR:** le partizioni logiche, o LPAR, vengono usate per dividere le risorse di calcolo. Un mainframe fisico è suddiviso in più LPAR.

- **z/OS:** un sistema operativo a 64 bit usato principalmente per i mainframe IBM.

I sistemi IBM utilizzano un sistema di monitoraggio delle transazioni come CICS per registrare e gestire tutti gli aspetti di una transazione aziendale. CICS gestisce la condivisione delle risorse, l'integrità dei dati e l'assegnazione delle priorità di esecuzione. CICS autorizza gli utenti, alloca le risorse e passa le richieste per il database dall'applicazione a una funzionalità di gestione database, ad esempio IBM DB2.

Per un'ottimizzazione più precisa, CICS viene solitamente usato con IMS/TM (in precedenza denominato IMS/Data Communications o IMS/DC). IMS è stato progettato per ridurre la ridondanza dei dati tramite la gestione di una sola copia dei dati. Integra CICS nel monitoraggio delle transazioni, attraverso la gestione dello stato nel corso di tutto il processo e la registrazione delle funzioni di business in un archivio dati.

## <a name="mainframe-operations"></a>Operazioni dei mainframe

Di seguito sono illustrate le tipiche operazioni dei mainframe:

- **Online:** i carichi di lavoro includono l'elaborazione delle transazioni, la gestione dei database e le connessioni. Vengono spesso implementati usando connettori IBM DB2, CICS e z/OS.

- **Batch:** processi eseguiti senza alcuna interazione dell'utente, in genere a intervalli regolari, ad esempio ogni mattina nei giorni feriali. I processi batch possono essere eseguiti nei sistemi basati su Windows o Linux usando un emulatore JCL come il software Micro Focus Enterprise Server o BMC Control-M.

- **Job Control Language (JCL):** consente di specificare le risorse necessarie per l'elaborazione dei processi batch. JCL fornisce queste informazioni a z/OS attraverso un set di istruzioni di controllo dei processi. A livello di base, JCL contiene sei tipi di istruzioni: JOB, ASSGN, DLBL, EXTENT, LIBDEF ed EXEC. Un processo può contenere diverse istruzioni EXEC (passaggi) e ogni passaggio può avere diverse istruzioni LIBDEF, ASSGN, DLBL ed EXTENT.

- **Initial Program Load (IPL):**  indica il caricamento di una copia del sistema operativo dal disco nella memoria reale del processore e la relativa esecuzione. Gli IPL vengono usati per il ripristino in seguito a tempi di inattività. Un IPL è simile all'avvio del sistema operativo nelle macchine virtuali Windows o Linux.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Falsi miti e fatti](myths-and-facts.md)
