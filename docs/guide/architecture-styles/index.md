---
title: Stili di architettura
titleSuffix: Azure Application Architecture Guide
description: Stili di architettura comuni per le applicazioni cloud.
layout: LandingPage
ms.topic: landing-page
ms.service: architecture-center
ms.subservice: reference-architecture
ms.date: 08/30/2018
---

# <a name="architecture-styles"></a>Stili di architettura

Uno *stile di architettura* è una famiglia di architetture che condividono determinate caratteristiche. Ad esempio lo stile [a più livelli][n-tier] è uno stile di architettura comune. Più di recente le [architetture di microservizi][microservices] hanno iniziato a guadagnare favore. Gli stili di architettura non richiedono l'uso di tecnologie particolari, ma alcune tecnologie sono più adatte per alcune architetture. Ad esempio i contenitori sono una scelta naturale per i microservizi.

È stato identificato un set di stili di architettura che si trovano in genere nelle applicazioni cloud. L'articolo per ogni stile include:

- Una descrizione e un diagramma logico dello stile.
- Raccomandazioni su quando scegliere lo stile.
- Vantaggi, sfide e procedure consigliate.
- Una distribuzione consigliata utilizzando servizi di Azure pertinenti.

## <a name="a-quick-tour-of-the-styles"></a>Una breve panoramica degli stili

Questa sezione fornisce una breve panoramica degli stili di architettura che abbiamo identificato insieme ad alcune considerazioni generali sul loro utilizzo. Altre informazioni sono disponibili negli argomenti collegati.

### <a name="n-tier"></a>A più livelli

<!-- markdownlint-disable MD033 -->

<img src="./images/n-tier-sketch.svg" style="float:left; margin-top:6px;"/>

<!-- markdownlint-enable MD033 -->

L'architettura **[a più livelli][n-tier]** è un'architettura tradizionale per le applicazioni aziendali. Le dipendenze vengono gestite suddividendo l'applicazione in *livelli* che eseguono funzioni logiche, come presentazione, logica di business e accesso ai dati. Un livello può chiamare solo il livelli che si trovano al di sotto. Tuttavia, questa sovrapposizione orizzontale può essere un ostacolo. Può essere difficile apportare modifiche in una sola parte dell'applicazione senza toccare il resto. Questo rende difficoltosi gli aggiornamenti frequenti, limitando la frequenza con cui è possibile aggiungere nuove funzionalità.

L'architettura a più livelli è una scelta naturale per la migrazione delle applicazioni esistenti che già usano un'architettura a livelli. Per questo motivo, l'architettura a più livelli si vede più spesso nelle soluzioni di infrastruttura distribuita come servizio (Iaas) o un'applicazione che usa una combinazione di IaaS e servizi gestiti.

### <a name="web-queue-worker"></a>Web-coda-ruolo di lavoro

<!-- markdownlint-disable MD033 -->

<img src="./images/web-queue-worker-sketch.svg" style="float:left; margin-top:6px;"/>

<!-- markdownlint-enable MD033 -->

Per una soluzione esclusivamente PaaS, prendere in considerazione un'architettura **[Web-coda-ruolo di lavoro](./web-queue-worker.md)**. In questo stile l'applicazione dispone di un front-end Web che gestisce le richieste HTTP e un ruolo di lavoro back-end che esegue attività a elevato utilizzo della CPU o operazioni a esecuzione prolungata. Il front-end comunica con il ruolo di lavoro tramite una coda di messaggi asincroni.

L'architettura Web-coda-ruolo di lavoro è adatta per i domini relativamente semplici con alcune attività a elevato utilizzo di risorse. Come quella a più livelli, anche questa architettura è facile da capire. L'uso dei servizi gestiti semplifica la distribuzione e le operazioni. Ma con un dominio complesso, può essere difficile gestire le dipendenze. Il front-end e il ruolo di lavoro possono diventare facilmente componenti monolitici di grandi dimensioni, difficoltosi da gestire e aggiornare. Come con più livelli, questo può ridurre la frequenza degli aggiornamenti e limitare l'innovazione.

### <a name="microservices"></a>Microservizi

<!-- markdownlint-disable MD033 -->

<img src="./images/microservices-sketch.svg" style="float:left; margin-top:6px;"/>

<!-- markdownlint-enable MD033 -->

Se l'applicazione dispone di un dominio più complesso, può essere preferibile passare a un'architettura di **[microservizi][microservices]**. Un'applicazione di microservizi è composta da molti servizi di piccole dimensioni indipendenti. Ogni servizio implementa una singola funzionalità di business. I servizi sono accoppiati in modo debole e comunicano tramite contratti API.

Ogni servizio può essere compilato da un team di sviluppo mirato di piccole dimensioni. I singoli servizi possono essere distribuiti senza molta coordinamento tra i team, che incoraggia aggiornamenti frequenti. Un'architettura di microservizi è più complessa da creare e gestire rispetto sia all'architettura a più livelli sia a quella Web-coda-ruolo di lavoro. Richiede uno sviluppo maturo e una cultura DevOps. Ma eseguito correttamente, questo stile può portare a una maggiore velocità di rilascio, un'innovazione più veloce e un'architettura più resiliente.

### <a name="cqrs"></a>CQRS

<!-- markdownlint-disable MD033 -->

<img src="./images/cqrs-sketch.svg" style="float:left; margin-top:6px;"/>

<!-- markdownlint-enable MD033 -->

Lo stile **[CQRS](./cqrs.md)** (Command and Query Responsibility Segregation) separa le operazioni di lettura e scrittura in modelli separati. Questo isola le parti del sistema che aggiornano i dati dalle parti che leggono i dati. Inoltre, le operazioni di lettura possono essere eseguite su una vista materializzata, fisicamente separata dal database di scrittura. Questo consente di ridimensionare i carichi di lavoro di lettura e scrittura in modo indipendente e di ottimizzare la vista materializzata per le query.

CQRS è più appropriata quando la si applica a un sottosistema di un'architettura più ampia. In genere non deve essere imposta nell'intera applicazione, in quanto questo creerebbe soltanto inutili complessità. È preferibile considerarla per i domini di collaborazione in cui molti utenti accedono agli stessi dati.

### <a name="event-driven-architecture"></a>Architettura basata su eventi

<!-- markdownlint-disable MD033 -->

<img src="./images/event-driven-sketch.svg" style="float:left; margin-top:6px;"/>

L'**[architettura basata su eventi](./event-driven.md)** usa un modello pubblicazione-sottoscrizione (pub-sub) in cui i produttori pubblicano eventi e i consumer li sottoscrivono. I produttori sono indipendenti dai consumer e i consumer sono indipendenti tra loro.

Si consideri un'architettura basata su eventi per le applicazioni che accettano ed elaborano una grande quantità di dati con una latenza molto bassa, come le soluzioni IoT. Lo stile è utile anche quando sottosistemi diversi devono eseguire diversi tipi di elaborazione sugli stessi dati di evento.

<br />

<!-- markdownlint-enable MD033 -->

### <a name="big-data-big-compute"></a>Big Data e Big Compute

**[Big Data](./big-data.md)** e **[Big Compute](./big-compute.md)** sono stili architettura specializzati per carichi di lavoro che soddisfano determinati profili specifici. Lo stile Big Data divide un set di dati molto grandi in blocchi, eseguendo elaborazione parallela sull'intero set per analisi e creazione di report. Lo stile Big Compute, anche chiamato calcolo ad alte prestazioni (HPC, high-performance computing), esegue calcoli paralleli in un numero elevato (migliaia) di core. I domini includono simulazioni, modellazione e rendering 3D.

## <a name="architecture-styles-as-constraints"></a>Stili di architettura e vincoli

Uno stile di architettura impone vincoli sulla progettazione, incluso il set di elementi che possono essere visualizzati e le relazioni consentite tra gli elementi. I vincoli determinano la "forma" di un'architettura limitando l'universo di scelte. Quando un'architettura è conforme ai vincoli di un determinato stile, emerge determinate proprietà auspicabili.

Ad esempio, i vincoli nei microservizi includono:

- Un servizio rappresenta un'unica responsabilità.
- Ogni servizio è indipendente dagli altri.
- I dati sono privati per il servizio che ne è proprietario. I servizi non condividono dati.

Aderendo a questi vincoli, emerge un sistema in cui i servizi possono essere distribuiti in modo indipendente, gli errori sono isolati, sono possibili aggiornamenti frequenti ed è facile inserire nuove tecnologie nell'applicazione.

Prima di scegliere uno stile di architettura, assicurarsi di conoscere i principi di base e i vincoli dello stile. In caso contrario, si può ottenere una progettazione che è conforme con lo stile a un livello superficiale, ma non realizza il pieno potenziale dello stile. È importante anche essere pratici. In alcuni casi è preferibile allentare un vincolo, anziché insistere sulla purezza dell'architettura.

La tabella seguente riepiloga il modo in cui ogni stile gestisce le dipendenze e i tipi di dominio più adatti.

| Stile di architettura | Gestione delle dipendenze | Tipo di dominio |
|--------------------|------------------------|-------------|
| A più livelli | Livelli orizzontali divisi per subnet | Dominio aziendale tradizionale. La frequenza degli aggiornamenti è bassa. |
| Web/coda/ruolo di lavoro | Processi front-end e back-end, disaccoppiati dalla messaggistica asincrona. | Dominio relativamente semplice con alcune attività con utilizzo intensivo di risorse. |
| Microservizi | Servizi scomposti verticalmente (funzionalmente) che di chiamano a vicenda attraverso le API. | Dominio complicato. Aggiornamenti frequenti. |
| CQRS | Separazione di lettura/scrittura. Lo schema e la scala sono ottimizzati separatamente. | Dominio di collaborazione in cui un numero elevato di utenti accede agli stessi dati. |
| Architettura basata su eventi. | Produttore/consumer. Vista indipendente per sottosistema. | Sistemi IoT e in tempo reale |
| Big Data | Dividere un set di dati di grandi dimensioni in piccoli blocchi. Elaborazione parallela in set di dati locali. | Analisi dei dati in catch e in tempo reale. Analisi predittiva mediante ML. |
| Big Compute| Allocazione di dati a migliaia di core. | Domini ad alta intensità di calcolo, come la simulazione. |

## <a name="consider-challenges-and-benefits"></a>Considerare problemi e vantaggi

I vincoli creano anche sfide, pertanto è importante conoscere i vantaggi e svantaggi dell'adozione di qualsiasi di questi stili. I vantaggi dello stile di architettura superano le sfide, *per questo sottodominio e il contesto associato*?

Ecco alcuni dei tipi di problematiche da prendere in considerazione quando si seleziona uno stile di architettura:

- **Complessità**. La complessità dell'architettura è giustificata per il dominio? Al contrario, lo stile è troppo semplicistico per il dominio? In tal caso, si rischia di ottenere [un'architettura inesistente][ball-of-mud], perché l'architettura non consente di gestire correttamente le dipendenze.

- **Messaggistica asincrona e coerenza finale**. La messaggistica asincrona consente di disaccoppiare i servizi e aumentare l'affidabilità (perché è possibile ritentare i messaggi) e la scalabilità. Tuttavia, questo crea anche sfide, ad esempio la semantica sempre-una volta e la coerenza finale.

- **Comunicazione fra i servizi**. Quando si scompone un'applicazione in servizi separati, c'è il rischio che la comunicazione tra i servizi causi una latenza accettabile o crei una congestione della rete (ad esempio in un'architettura di microservizi).

- **Gestibilità**. Quanto è difficile gestire l'applicazione, monitorare, distribuire gli aggiornamenti e così via?

[ball-of-mud]: https://en.wikipedia.org/wiki/Big_ball_of_mud
[microservices]: ./microservices.md
[n-tier]: ./n-tier.md
