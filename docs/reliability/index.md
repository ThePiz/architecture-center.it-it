---
title: Progettare applicazioni Azure affidabili
description: Introduzione alla creazione di applicazioni Azure affidabili e a disponibilità elevata
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: ''
ms.openlocfilehash: 16c1daeed9b1d79f33051eb69571f1574bf9be4d
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59644015"
---
# <a name="designing-reliable-azure-applications"></a>Progettazione di applicazioni Azure affidabili

La creazione di un'applicazione affidabile nel cloud è diversa rispetto alla tradizionale procedura di sviluppo delle applicazioni. In passato si acquistava infatti hardware di fascia alta per aumentare le prestazioni, mentre in un ambiente cloud è necessario ampliare il livello di servizio. Invece di provare a evitare completamente gli errori, l'obiettivo deve essere quello di ridurre al minimo gli effetti di un singolo componente in errore.

Le applicazioni affidabili sono:

- **Resilienti** e in grado di recuperare normalmente in caso di errore, continuando a funzionare con tempi di inattività e perdite di dati minimi prima del ripristino completo.
- **A disponibilità elevata** e in grado di continuare a essere eseguite in uno stato di integrità, senza tempi di inattività significativi.

Per creare un'applicazione affidabile, è fondamentale comprendere le interazioni tra questi elementi e il relativo impatto sui costi. Può essere utile determinare quale siano il tempo di inattività accettabile, il costo potenziale per l'azienda e quali funzioni sono necessarie durante un ripristino.

Questo articolo offre una breve panoramica su come integrare l'affidabilità in ogni passaggio del processo di progettazione di applicazioni Azure. Ogni sezione include un collegamento a un articolo di approfondimento su come integrare l'affidabilità nello specifico passaggio del processo. Per considerazioni sull'affidabilità relative a singoli servizi di Azure, vedere l'[elenco di controllo per servizi di Azure specifici](../checklist/resiliency-per-service.md).

## <a name="build-for-reliability"></a>Creare applicazioni per garantire l'affidabilità

Questa sezione illustra sei passaggi per creare un'applicazione Azure affidabile. Ogni passaggio è collegato a una sezione in cui vengono definiti ulteriormente il processo e i termini.

1. [**Definire i requisiti.**](#define-requirements) Sviluppare i requisiti di disponibilità e ripristino in base alla scomposizione dei carichi di lavoro e delle esigenze aziendali.
1. [**Usare le procedure consigliate per l'architettura.**](#use-architectural-best-practices) Seguire procedure comprovate, identificare i possibili punti di errore nell'architettura e determinare la risposta dell'applicazione all'errore.
1. [**Eseguire test con simulazioni e failover forzati.**](#test-with-simulations-and-forced-failovers) Simulare errori, attivare failover forzati e testare il rilevamento e il ripristino da questi errori.
1. [**Distribuire l'applicazione in modo coerente.**](#deploy-the-application-consistently) Eseguire il rilascio nell'ambiente di produzione usando processi ripetibili e affidabili.
1. [**Monitorare l'integrità delle applicazioni.**](#monitor-application-health) Rilevare gli errori, monitorare gli indicatori di potenziali errori e misurare l'integrità delle applicazioni.
1. [**Rispondere a errori e interruzioni.**](#respond-to-failures-and-disasters) Identificare quando si verifica un errore e determinare come risolverlo in base a strategie stabilite.

## <a name="define-requirements"></a>Definire i requisiti

Identificare le esigenze aziendali e creare il piano di affidabilità per soddisfarle. Valutare gli aspetti seguenti:

- **Identificare i carichi di lavoro e l'utilizzo.** Per *carico di lavoro* si intende una capacità o un'attività distinta, che è separata in modo logico da altre attività, in termini di requisiti di logica di business e di archiviazione dei dati. Ogni carico di lavoro prevede requisiti diversi in termini di disponibilità, scalabilità, coerenza dei dati e ripristino di emergenza.
- **Pianificare i modelli di utilizzo.** Anche i *modelli di utilizzo* sono rilevanti nei requisiti. Identificare i diversi requisiti previsti durante i periodi critici e quelli non critici. Ad esempio, un'applicazione per la dichiarazione dei redditi non può smettere di funzionare in prossimità della scadenza per la presentazione della dichiarazione. Per evitare tempi di inattività, pianificare la ridondanza tra aree diverse nel caso in cui si verifichi un errore in una di esse. Viceversa, per ridurre al minimo i costi durante i periodi non critici, è possibile eseguire l'applicazione in una singola area.
- **Definire le metriche della disponibilità, ovvero il *tempo medio di ripristino* (MTTR) e il *tempo medio tra gli errori* (MTBF).** Per tempo medio di ripristino (MTTR) si intende il tempo medio necessario per ripristinare un componente dopo un errore. Per tempo medio tra gli errori (MTBF) si intende invece l'intervallo di tempo in cui è ragionevole aspettarsi che un componente rimanga in esecuzione tra un'interruzione del servizio e l'altra. Usare queste misure per determinare i punti in cui aggiungere ridondanza e quali contratti di servizio offrire ai clienti.  
- **Stabilire le metriche di ripristino, ovvero l'obiettivo del tempo di ripristino (RTO) e l'obiettivo del punto di ripristino (RPO).** Per *obiettivo del tempo di ripristino* (RTO) si intende il tempo massimo accettabile in cui un'applicazione può rimanere non disponibile dopo un evento imprevisto. Per *obiettivo del punto di ripristino* (RPO) si intende la durata massima di perdita dei dati accettabile durante un'emergenza. Per ottenere questi valori, condurre una valutazione dei rischi e comprendere i costi e i rischi correlati a tempo di inattività o perdita di dati nell'organizzazione.
    > [!NOTE]
    > Se il valore del tempo MTTR di un *qualsiasi* componente critico in una configurazione a disponibilità elevata supera il valore dell'obiettivo RTO del sistema, un errore nel sistema potrebbe causare un'interruzione inaccettabile delle attività. Questo significa che non è possibile ripristinare il sistema entro l'obiettivo RTO definito.
- **Determinare le destinazioni di disponibilità dei carichi di lavoro.** Per essere certi che l'architettura dell'applicazione soddisfi i requisiti aziendali, definire contratti di servizio di destinazione per ogni carico di lavoro. Tenere in considerazione i costi e la complessità associati ai requisiti di disponibilità definiti, oltre alle dipendenze delle applicazioni.
- **Comprendere i contratti di servizio.** In Azure il contratto di servizio descrive gli impegni di Microsoft in merito a tempi di attività e connettività. Se il contratto per un determinato servizio è del 99,9%, è necessario prevedere che il servizio sia disponibile per il 99,9% del tempo.  

    Definire i propri contratti di servizio di destinazione per ogni carico di lavoro nella propria soluzione, in modo da poter determinare se l'architettura è conforme ai requisiti aziendali. Ad esempio, se un carico di lavoro richiede il 99,99% del tempo di attività, ma dipende da un servizio con un contratto del 99,9%, tale servizio non può essere un singolo punto di guasto nel sistema.

Per altre informazioni sullo sviluppo di requisiti per applicazioni affidabili, vedere [Sviluppo di requisiti per applicazioni Azure resilienti](./requirements.md).

## <a name="use-architectural-best-practices"></a>Usare le procedure consigliate per l'architettura

Durante la fase relativa all'architettura, concentrarsi sull'implementazione di procedure consigliate che consentano di soddisfare i requisiti aziendali, identificare i punti di errore e ridurre al minimo l'ambito degli errori.

- **Eseguire un'analisi della modalità di errore.** L'analisi della modalità di errore consente di integrare la resilienza in un'applicazione nelle prime fasi di progettazione. Permette di identificare i tipi di errori che potrebbero verificarsi nell'applicazione, i potenziali effetti di ognuno e le possibili strategie di ripristino.
- **Creare un piano di ridondanza.** Il livello di ridondanza richiesto per ogni carico di lavoro dipende dai requisiti aziendali e deve essere considerato nel costo complessivo dell'applicazione. 
- **Progettare per la scalabilità.** Un'applicazione cloud deve essere ridimensionabile in modo tale da supportare le variazioni nell'utilizzo. Iniziare con un numero ridotto di componenti e progettare l'applicazione in modo che, quando possibile, risponda automaticamente alle variazioni del carico. Tenere in considerazione i limiti di scalabilità durante la progettazione per prevedere una più agevole espansione in futuro.
- **Pianificare i requisiti relativi a sottoscrizioni e servizi.** Possono essere necessarie sottoscrizioni aggiuntive per il provisioning di risorse sufficienti in grado di soddisfare i requisiti aziendali in termini di archiviazione, connessioni, velocità effettiva e altro ancora. 
- **Usare il bilanciamento del carico per distribuire le richieste.** Il bilanciamento del carico distribuisce le richieste dell'applicazione tra le istanze del servizio integre, eliminando dalla rotazione le istanze che non sono integre.
- **Implementare strategie di resilienza.** La *resilienza* è la capacità di un sistema di correggere gli errori e continuare a funzionare. Implementare [schemi progettuali di resilienza](../patterns/category/resiliency.md), ad esempio isolando le risorse critiche, usando transazioni di compensazione ed eseguendo operazioni asincrone quando possibile.
- **Creare i requisiti di disponibilità nella progettazione.** Per *disponibilità* si intende la percentuale di tempo in cui il sistema funziona normalmente ed è in esecuzione. Adottare misure per assicurarsi che la disponibilità delle applicazioni sia conforme al contratto di servizio. Ad esempio, per evitare singoli punti di guasto, scomporre i carichi di lavoro in base all'obiettivo del livello di servizio e limitare volumi elevati di utenti.
- **Gestire i dati.** Le modalità di archiviazione, backup e replica dei dati sono di fondamentale importanza.

  - **Scegliere i metodi di replica per i dati dell'applicazione.** I dati dell'applicazione vengono archiviati in archivi dati diverse e prevedono requisiti di disponibilità diversi. Valutare i metodi e i percorsi di replica per ogni tipo di archivio dati per assicurarsi che soddisfino i requisiti.
  - **Documentare e testare i processi di failover e failback.** Documentare in modo chiaro le istruzioni per il failover in un nuovo archivio dati e testarle regolarmente per essere certi che siano accurate e facili da seguire.
  - **Proteggere i dati.** Eseguire regolarmente il backup e la convalida dei dati e assicurarsi che nessun account utente singolo disponga dell'accesso sia ai dati di produzione che a quelli di backup.
  - **Pianificare il ripristino dei dati.** Assicurarsi che la strategia di backup e replica preveda tempi di ripristino dei dati che soddisfano i requisiti del livello di servizio. Tenere in considerazione tutti i tipi di dati usati dall'applicazione, tra cui i dati di riferimento e i database.

Per altre informazioni sulla definizione dell'architettura per applicazioni affidabili, vedere [Architettura delle applicazioni di Azure per la resilienza e la disponibilità](./architect.md).

## <a name="test-with-simulations-and-forced-failovers"></a>Eseguire test con simulazioni e failover forzati

Per testare l'affidabilità, è necessario misurare in che modo il carico di lavoro end-to-end viene eseguito in condizioni di errore che si verificano solo saltuariamente.

- **Testare scenari di errore comuni attivando errori effettivi o simulandoli.** Usare test con fault injection per testare scenari comuni (tra cui combinazioni di errori) e tempo di ripristino.
- **Identificare gli errori che si verificano solo in condizioni di carico.** Eseguire il test per il carico di picco, usando dati di produzione oppure dati sintetici che siano più simili possibile ai dati di produzione, per verificare il comportamento dell'applicazione in condizioni reali.
- **Eseguire esercitazioni sul ripristino di emergenza.** Predisporre un piano di ripristino di emergenza e testarlo periodicamente per verificare che funzioni in modo appropriato.
- **Eseguire i test di failover e di failback.** Assicurarsi che il failover e il failback dei servizi dipendenti dell'applicazione vengano eseguiti nell'ordine corretto.
- **Eseguire test di simulazione.** I test di scenari reali consentono di evidenziare problemi che è necessario risolvere. Gli scenari devono essere controllabili e non causare interruzioni delle attività. Informare i responsabili in merito ai piani di test di simulazione.
- **Testare i probe di integrità.** Configurare i probe di integrità per i servizi di bilanciamento del carico e di gestione del traffico per controllare i componenti critici del sistema. Testarli per assicurarsi che rispondano in modo appropriato.
- **Testare i sistemi di monitoraggio.** Assicurarsi che i sistemi di monitoraggio segnalino in modo affidabile informazioni critiche e dati accurati per consentire l'identificazione di potenziali errori.
- **Includere servizi di terze parti negli scenari di test.** Oltre al ripristino, testare possibili punti di errore causati dall'interruzione di servizi di terze parti.

Il test è un processo iterativo: testare l'applicazione, misurare il risultato, analizzare e risolvere gli eventuali errori e ripetere il processo.

Per altre informazioni sui test per l'affidabilità delle applicazioni, vedere [Verifica della disponibilità e della resilienza delle applicazioni Azure](./testing.md).

## <a name="deploy-the-application-consistently"></a>Distribuire l'applicazione in modo coerente

La *distribuzione* include il provisioning delle risorse di Azure, la distribuzione del codice dell'applicazione e l'applicazione delle impostazioni di configurazione. Un aggiornamento può interessare tutte e tre le attività oppure solo una parte di esse.

Dopo che un'applicazione viene distribuita nell'ambiente di produzione, gli aggiornamenti costituiscono una possibile fonte di errori. Ridurre al minimo gli errori con processi di distribuzione prevedibili e ripetibili.

- **Automatizzare il processo di distribuzione dell'applicazione.** Automatizzare il più possibile i processi.
- **Progettare il processo di rilascio per ottimizzare la disponibilità.** Se il processo di rilascio prevede che i servizi vengano disconnessi durante la distribuzione, l'applicazione non sarà disponibile fino a quando i servizi non verranno riportati online. Sfruttare le funzionalità di produzione e staging della piattaforma. Usare versioni canary o di tipo blu-verde per distribuire gli aggiornamenti, in modo che in caso di errore è possibile eseguire rapidamente il rollback dell'aggiornamento.
- **Creare un piano di ripristino dello stato precedente alla distribuzione.** Progettare un processo di ripristino dello stato precedente per tornare all'ultima versione valida nota e ridurre al minimo i tempi di inattività se una distribuzione non riesce.
- **Registrare e controllare le distribuzioni.** Se si usano le tecniche di pre-distribuzione, nell'ambiente di produzione verrà eseguita più di una versione dell'applicazione. Implementare una strategia di registrazione affidabile per acquisire quante più informazioni specifiche della versione possibili.
- **Documentare il processo di rilascio dell'applicazione.** Definire e documentare chiaramente il processo di rilascio e assicurarsi che sia disponibile a tutto il team operativo.

Per altre informazioni sull'affidabilità e la distribuzione delle applicazioni, vedere [Distribuzione delle applicazioni di Azure per la resilienza e la disponibilità](./deploy.md).

## <a name="monitor-application-health"></a>Monitorare l'integrità delle applicazioni

Implementare procedure consigliate per il monitoraggio e gli avvisi nell'applicazione in modo da poter rilevare eventuali errori e avvisare un operatore di correggerli.

- **Implementare probe di integrità e funzioni di controllo.** Eseguirli regolarmente dall'esterno dell'applicazione per identificare problemi di prestazioni o di integrità dell'applicazione.
- **Controllare i flussi di lavoro a esecuzione prolungata.** Il rilevamento tempestivo di errori consente di ridurre al minimo la necessità di eseguire il rollback dell'intero flusso di lavoro o di eseguire più transazioni di compensazione.
- **Mantenere log dell'applicazione.**

  - Registrare le applicazioni in produzione e in corrispondenza dei limiti del servizio.
  - Usare la registrazione semantica e asincrona.
  - Separare i log dell'applicazione dai log di controllo.

- **Misurare le statistiche delle chiamate remote e condividere i dati con il team dell'applicazione.** Per offrire al team operativo un quadro immediato dell'integrità dell'applicazione, riepilogare le metriche di chiamata remota, ad esempio la latenza, la velocità effettiva e gli errori nei percentili 95 e 99. Eseguire l'analisi statistica sulle metriche per individuare gli errori che si verificano entro ogni percentile.
- **Tenere traccia delle eccezioni temporanee e dei tentativi in un intervallo di tempo appropriato.** Una tendenza all'aumento delle eccezioni nel tempo indica che il servizio ha un problema e potrebbe avere esito negativo.
- **Configurare un sistema di avvisi tempestivi.** Identificare gli indicatori di prestazioni chiave (KPI) dell'integrità di un'applicazione, ad esempio le eccezioni temporanee e la latenza delle chiamate remote, nonché impostare valori di soglia appropriati per ognuno. Inviare un avviso all'operatore quando viene raggiunto il valore di soglia.
- **Operare entro i limiti della sottoscrizione di Azure.** Le sottoscrizioni di Azure prevedono limiti relativi a determinati tipi di risorse, ad esempio il numero di gruppi di risorse, di core e di account di archiviazione. Tenere sotto controllo l'uso dei diversi tipi di risorsa.
- **Monitorare i servizi di terze parti.** Registrare le chiamate e correlarle alla registrazione diagnostica e di integrità dell'applicazione usando un identificatore univoco.
- **Addestrare più operatori al monitoraggio dell'applicazione e all'esecuzione delle procedure di ripristino manuale.** Assicurarsi che sia sempre presente almeno un operatore addestrato.

Per altre informazioni sul monitoraggio dell'affidabilità delle applicazioni, vedere [Monitoraggio dell'integrità dell'applicazione per l'affidabilità in Azure](./monitoring.md).

## <a name="respond-to-failures-and-disasters"></a>Rispondere a errori e interruzioni

Creare un piano di ripristino e assicurarsi che copra il ripristino dei dati, le interruzioni della rete, gli errori dei servizi dipendenti e le interruzioni del servizio a livello di area. Nella strategia di ripristino prendere in considerazione le macchine virtuali, lo spazio di archiviazione, i database e altri servizi della piattaforma Azure.

- **Pianificare le interazioni con il supporto tecnico di Azure.** Prima che diventi necessario, definire un processo per contattare il supporto tecnico di Azure.
- **Documentare e testare il piano di ripristino di emergenza.** Scrivere un piano di ripristino di emergenza che rispecchi l'impatto degli errori delle applicazioni a livello aziendale. Automatizzare il più possibile il processo di ripristino e documentare eventuali passaggi manuali. Testare regolarmente il processo di ripristino di emergenza per convalidare e migliorare il piano.
- **Effettuare il failover manuale se necessario.** Alcuni sistemi non possono effettuare il failover automaticamente e richiedono un failover manuale. Se un'applicazione effettua il failover in un'area secondaria, eseguire un test della conformità operativa. Verificare che l'area primaria sia integra e pronta a ricevere nuovamente traffico prima di effettuare il failback. Determinare l'impatto della funzionalità ridotta dell'applicazione e la modalità con cui l'app comunica agli utenti la presenza di problemi temporanei.
- **Prepararsi per gestire errori dell'applicazione.** Prepararsi a gestire vari tipi di errori, tra cui quelli che vengono gestiti automaticamente, quelli che comportano una riduzione della funzionalità e quelli che causano l'indisponibilità dell'applicazione. L'applicazione deve informare gli utenti dei problemi temporanei.
- **Eseguire il ripristino in caso di danneggiamento dei dati.** Nel caso di un errore in un archivio dati, controllare la presenza di incoerenze dei dati quando l'archivio diventa nuovamente disponibile, soprattutto se i dati sono stati replicati. Ripristinare i dati danneggiati da un backup.
- **Eseguire il ripristino dopo un'interruzione della rete.** Anche in caso di riduzione della funzionalità dell'applicazione deve essere possibile usare i dati memorizzati nella cache per l'esecuzione in locale. In caso contrario, mettere fuori servizio l'applicazione o effettuare il failover in un'altra area. Archiviare i dati in una posizione alternativa finché non viene ripristinata la connettività.
- **Eseguire il ripristino in caso di errore di un servizio dipendente.** Determinare quale funzionalità è ancora disponibile e la modalità di risposta dell'applicazione.
- **Eseguire il ripristino dopo un'interruzione di servizio a livello di area.** Le interruzioni del servizio a livello di area sono poco comuni, ma è comunque necessario predisporre una strategia per risolverle, in particolare nel caso di applicazioni critiche. È possibile distribuire l'applicazione in un'altra area o ridistribuire il traffico.

Per altre informazioni sulla risposta in caso di errori o ripristino di emergenza, vedere [Errore e ripristino di emergenza per le applicazioni Azure](./disaster-recovery.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Sviluppare requisiti per applicazioni resilienti](./requirements.md)
