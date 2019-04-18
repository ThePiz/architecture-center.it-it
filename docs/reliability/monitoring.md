---
title: Monitoraggio dell'integrità dell'applicazione per l'affidabilità in Azure
description: Come usare il monitoraggio per migliorare l'affidabilità dell'applicazione in Azure
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: 46f9f3fb623a2facf4b9f9c6ec57376ae59e41dc
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646671"
---
# <a name="monitoring-application-health-for-reliability-in-azure"></a>Monitoraggio dell'integrità dell'applicazione per l'affidabilità in Azure

Il monitoraggio e la diagnostica sono fondamentali per la resilienza, Se qualcosa non funziona, è necessario conoscere *che* non è riuscita, *quando* non è riuscita &mdash; e *perché*.

*Monitoring* non è identico *rilevamento degli errori*. Ad esempio, l'applicazione potrebbe rilevare un temporaneo errori e ripetizione dei tentativi, evitando tempi di inattività. Ma deve anche registrare l'operazione di ripetizione dei tentativi in modo che è possibile monitorare il tasso di errore per ottenere un quadro complessivo dell'integrità dell'applicazione.

Il processo di monitoraggio e diagnostica può essere paragonato a una pipeline con quattro fasi distinte: Strumentazione, raccolta e archiviazione, analisi e diagnosi e visualizzazione e avvisi.

## <a name="instrumentation"></a>Strumentazione

Non è pratico monitorare l'applicazione direttamente, quindi la strumentazione è fondamentale. Un sistema distribuito su larga scala può essere eseguita su decine di macchine virtuali (VM), che vengono aggiunti e rimossi nel corso del tempo. Analogamente, un'applicazione cloud potrebbe usare un numero di archivi dati e un'azione utente singola può estendersi a più sottosistemi.

Fornire strumenti avanzati:

- Per gli errori che sono probabili ma non sono ancora stati si sono verificati, fornire dati sufficienti per determinare la causa, ridurre la situazione e garantire che il sistema rimanga disponibile.
- Per gli errori che sono già verificati, l'applicazione deve restituire un messaggio di errore appropriato per l'utente, ma deve tentare di continuare l'esecuzione, sebbene con funzionalità ridotte.

Sistemi di monitoraggio devono acquisire i dettagli completi in modo che le applicazioni possono essere ripristinate in modo efficiente e, se necessario, progettisti e sviluppatori possono modificare il sistema per impedire la situazione in futuro.

I dati non elaborati per il monitoraggio possono provenire da svariate origini, tra cui:

- Log applicazioni, ad esempio quelli prodotti dal [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview) servizio.
- Le metriche delle prestazioni del sistema operativo raccolti dal [agenti di monitoraggio Azure](/azure/azure-monitor/platform/agents-overview).
- [Le risorse di Azure](/azure/azure-monitor/platform/metrics-supported), incluse le metriche raccolte da monitoraggio di Azure.
- [Azure Service Health](/azure/service-health/service-health-overview), che offre un dashboard che consentono di rilevare gli eventi attivi.
- [Log di Azure AD](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) integrate nella piattaforma Azure.

Servizi di Azure più sono le metriche e diagnostica che è possibile configurare per analizzare e determinare la causa dei problemi. Per altre informazioni, vedere [i dati di monitoraggio raccolti da monitoraggio di Azure](/azure/azure-monitor/platform/data-collection).

## <a name="collection-and-storage"></a>Raccolta e l'archiviazione

Dati di strumentazione non elaborati possono essere contenuti in diverse posizioni e i formati, tra cui:

- Log di traccia delle applicazioni
- Log di IIS
- Contatori delle prestazioni

Queste origini diverse sono raccolte, consolidate e inserite in archivi dati affidabile in Azure, ad esempio Application Insights, monitoraggio di Azure le metriche, integrità dei servizi, gli account di archiviazione e Analitica di Log di Azure.

## <a name="analysis-and-diagnosis"></a>Analisi e diagnosi

Analizzare i dati consolidati in questi archivi dati a risolvere i problemi e ottenere una visualizzazione complessiva dello stato delle applicazioni. In generale, è possibile [cercare e analizzare](/azure/azure-monitor/log-query/log-query-overview) i dati in Application Insights e Log Analitica usando query Kusto o grafici visualizzazione preconfigurata tramite [soluzioni di gestione](/azure/azure-monitor/insights/solutions-inventory). Oppure usare Azure Advisor per visualizzare le raccomandazioni con particolare attenzione nel [resilienza](/azure/advisor/advisor-high-availability-recommendations) e [prestazioni](/azure/advisor/advisor-performance-recommendations).

## <a name="visualization-and-alerts"></a>Visualizzazione e avvisi

Presentare i dati di telemetria in un formato che rende più semplice per un operatore rilevare tendenze o problemi rapidamente, ad esempio un avviso di dashboard o un messaggio di posta elettronica.

Ottenere una visualizzazione dello stack completo dello stato dell'applicazione usando [i dashboard di Azure](/azure/azure-portal/azure-portal-dashboards) per creare una visualizzazione consolidata dei grafici di Application Insights, Log Analitica, le metriche di monitoraggio di Azure e integrità dei servizi di monitoraggio. Allo stesso modo [gli avvisi di monitoraggio di Azure](/azure/azure-monitor/platform/alerts-overview) per creare le notifiche sul servizio integrità, integrità risorse, le metriche di monitoraggio di Azure, i log in Log Analitica e Application Insights.

Per altre informazioni sul monitoraggio e diagnostica, vedere [monitoraggio e diagnostica](../best-practices/monitoring.md).

## <a name="health-probes-and-check-functions"></a>I probe di integrità e le funzioni di controllo

L'integrità e prestazioni di un'applicazione possono peggiorare nel tempo e riduzione delle prestazioni potrebbe non essere evidenti fino a quando l'applicazione non riesce.

Implementare i probe o controllare le funzioni ed eseguirli regolarmente dall'esterno dell'applicazione. Questi controlli possono essere semplici come la misurazione del tempo di risposta per l'applicazione nel suo complesso, per le singole parti dell'applicazione, per servizi specifici utilizzati dall'applicazione o per i singoli componenti.

Controllare le funzioni è possono eseguire processi per garantire che essi producono risultati validi, misurare la latenza e controllare la disponibilità ed estrarre informazioni dal sistema.

## <a name="long-running-workflow-failures"></a>Errori del flusso di lavoro a esecuzione prolungata

I flussi di lavoro a esecuzione prolungata includono spesso più passaggi, ognuno dei quali devono essere indipendente.

Tenere traccia dell'avanzamento dei processi a esecuzione prolungata per ridurre al minimo la probabilità che l'intero flusso di lavoro dovrà essere eseguito il rollback o che le transazioni di compensazione più dovrà essere eseguita.

>[!TIP]
> Monitorare e gestire lo stato di avanzamento dei flussi di lavoro a esecuzione prolungata implementando un modello, ad esempio [Scheduler Agent Supervisor](../patterns/scheduler-agent-supervisor.md).

## <a name="application-logs"></a>Log applicazioni

Per questo motivo i log applicazioni sono una fonte importante di dati di diagnostica. Per ottenere informazioni quando ti serve la maggior parte, seguire le procedure consigliate per la registrazione delle applicazioni.

### <a name="log-data-in-the-production-environment"></a>Dati di log nell'ambiente di produzione

Acquisire i dati di telemetria affidabili mentre l'applicazione è in esecuzione nell'ambiente di produzione, in modo che si avranno informazioni sufficienti per diagnosticare la causa dei problemi nello stato di produzione.

### <a name="log-events-at-service-boundaries"></a>Eventi del log in corrispondenza dei limiti di servizio

Includere un ID di correlazione che passi attraverso i limiti di servizio: Se una transazione passa attraverso più servizi e uno di essi non riesce, la correlazione ID consente di tenere traccia delle richieste tra l'applicazione e pinpoints perché la transazione non è riuscita.

### <a name="use-semantic-structured-logging"></a>Usare la registrazione semantica (strutturata)

Con i log strutturati, risulta più semplice automatizzare il consumo e l'analisi dei dati di log, sono particolarmente importanti su scala cloud. In genere, si consiglia di archiviare i dati di diagnostica e metriche delle risorse di Azure in un'area di lavoro di Log Analitica invece che in un account di archiviazione. In questo modo, è possibile usare le query Kusto per ottenere i dati desiderati in un formato strutturato e rapidamente. È anche possibile usare le API di monitoraggio di Azure e le API di Azure Log Analitica.

### <a name="use-asynchronous-logging"></a>Usare la registrazione asincrona

Operazioni di registrazione sincrone a volte bloccano il codice dell'applicazione, causando il backup come vengono scritti i log delle richieste. Usare la registrazione asincrona per preservare la disponibilità durante la registrazione delle applicazioni.

### <a name="separate-application-logging-from-auditing"></a>Registrazione di applicazione separata dal controllo

I record di controllo vengono mantenuti comunemente per i requisiti normativi o di conformità e devono essere completati. Per evitare transazioni eliminate, gestire i log di controllo separatamente dal log di diagnostica.

## <a name="remote-call-statistics"></a>Statistiche delle chiamate remote

Traccia e report remoto chiamare le statistiche in tempo reale e forniscono un modo semplice per esaminare queste informazioni, in modo che il team operativo disponga di una vista istantanea dello stato di integrità dell'applicazione. Riepilogare le metriche di chiamata remota, ad esempio la latenza, velocità effettiva e gli errori nei percentili 95 e 99.

## <a name="transient-exceptions-and-retries"></a>I tentativi e le eccezioni temporanee

Tenere traccia del numero di eccezioni temporanee e di ripetizione dei tentativi nel corso del tempo per individuare problemi o errori nella logica di ripetizione dei tentativi dell'applicazione. Una tendenza crescente di eccezioni nel tempo può indicare che il servizio ha un problema e potrebbe non riuscire. Per ulteriori informazioni, vedere [Linee guida specifiche del servizio di ripetizione dei tentativi](../best-practices/retry-service-specific.md).

## <a name="early-warning-system"></a>Sistema di avviso precoce

Monitorare l'applicazione per i segni di avviso che potrebbero richiedere un intervento proattivo. Strumenti di cui valutare l'integrità complessiva dell'applicazione e le relative dipendenze consentono di riconoscere rapidamente quando un sistema o i relativi componenti diventano improvvisamente non disponibili. Usarle per implementare un sistema di avviso precoce.

1. Identificare gli indicatori di prestazioni chiave dell'integrità dell'applicazione, ad esempio le eccezioni temporanee e la latenza di chiamata remota.
1. Impostare le soglie a livelli che identifichino i problemi prima che diventino critici e richiedono una risposta di ripristino.
1. Inviare un avviso all'operatore quando viene raggiunto il valore di soglia.

Prendere in considerazione [Microsoft System Center 2016](https://www.microsoft.com/cloud-platform/system-center) o strumenti di terze parti per offrire funzionalità di monitoraggio. La maggior parte delle soluzioni di monitoraggio controlla i contatori delle prestazioni chiave e la disponibilità dei servizi. [Integrità risorse di Azure](/azure/service-health/resource-health-checks-resource-types) fornisce alcuni integrità integrati i controlli di stato, che consentono di diagnosticare la limitazione delle richieste di servizi di Azure.

## <a name="subscription-and-service-limitations"></a>Limitazioni del servizio e sottoscrizione

Le sottoscrizioni di Azure hanno limiti su determinati tipi di risorse, ad esempio il numero di gruppi di risorse, i core e gli account di archiviazione. Per assicurarsi che l'applicazione non viene eseguito vantaggi limiti della sottoscrizione di Azure, creare avvisi che eseguono il polling per i servizi per raggiungere i limiti e quote.

Risolvere i seguenti limiti della sottoscrizione agli avvisi.

### <a name="individual-services"></a>I singoli servizi

Singoli servizi di Azure hanno limiti di consumo per archiviazione, la velocità effettiva, numero di connessioni, richieste al secondo e altre metriche. L'applicazione avrà esito negativo se tenta di usare le risorse oltre questi limiti, con conseguente limitazione delle richieste e possibili i tempi di inattività.

A seconda del servizio specifico e ai requisiti dell'applicazione, è spesso possibile restare sotto questi limiti dalla scalabilità verticale (scelta di un altro piano tariffario, ad esempio) o la scalabilità orizzontale (aggiunta di nuove istanze).

### <a name="azure-storage-scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni di archiviazione di Azure

Azure consente un numero massimo di account di archiviazione per ogni sottoscrizione. Se l'applicazione richiede più account di archiviazione rispetto a quelli attualmente disponibili nella sottoscrizione, creare una nuova sottoscrizione con altri account di archiviazione. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](/azure/azure-subscription-service-limits/#storage-limits).

Se si superano obiettivi di scalabilità e prestazioni di archiviazione di Azure, l'applicazione sarà soggetta la limitazione delle richieste di archiviazione. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](/azure/storage/storage-scalability-targets/).

### <a name="scalability-targets-for-virtual-machine-disks"></a>Obiettivi di scalabilità per i dischi della macchina virtuale

Un'infrastruttura come servizio (IaaS) della macchina virtuale di Azure supporta il collegamento di un numero di dischi dati, in base a diversi fattori, tra cui le dimensioni VM e il tipo di account di archiviazione. Se l'applicazione supera gli obiettivi di scalabilità per i dischi di macchina virtuale, eseguire il provisioning di account di archiviazione aggiuntivi e creare in essi i dischi di macchina virtuale. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks).

### <a name="vm-size"></a>Dimensioni macchina virtuale

Se la CPU, memoria, disco e i/o delle macchine virtuali effettivo si avvicinano al limite delle dimensioni della macchina virtuale, l'applicazione potrebbe verificarsi problemi di capacità. Per risolvere i problemi, aumentare le dimensioni VM. Dimensioni delle macchine Virtuali sono descritte nel [dimensioni delle macchine virtuali in Azure](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Se il carico di lavoro varia nel corso del tempo, prendere in considerazione l'utilizzo scalabilità di macchine virtuali di set per ridimensionare automaticamente il numero di istanze di VM. In caso contrario, è necessario aumentare o diminuire il numero di macchine virtuali manualmente. Per altre informazioni, vedere la [panoramica dei set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/).

### <a name="azure-sql-database"></a>Database SQL di Azure

Se il livello di Database SQL di Azure non è sufficiente per gestire i requisiti dell'applicazione Database Transaction Unit (DTU), verrà limitato l'utilizzo dei dati. Per altre informazioni sulla selezione del piano di servizio corretto, vedere [Database SQL di Azure i modelli di acquisto](/azure/sql-database/sql-database-service-tiers/).

## <a name="third-party-services"></a>Servizi di terze parti

Se l'applicazione ha dipendenze in servizi di terze parti, identificare la modalità con cui possono avere esito negativo di questi servizi e ciò che gli errori di effetto avrà sull'applicazione.

Un servizio di terze parti potrebbe non includere il monitoraggio e diagnostica. Registrare le chiamate a questi servizi e metterle in correlazione con l'integrità e la registrazione diagnostica mediante un identificatore univoco dell'applicazione. Per altre informazioni sulle pratiche comprovate di monitoraggio e diagnostica, vedere [indicazioni di monitoraggio e diagnostica](../best-practices/monitoring.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Piano di ripristino di emergenza](./disaster-recovery.md)
