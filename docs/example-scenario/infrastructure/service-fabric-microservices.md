---
title: Uso di Service Fabric per scomporre applicazioni
titleSuffix: Azure Example Scenarios
description: Scomporre un'applicazione monolitica di grandi dimensioni in microservizi.
author: timomta
ms.date: 09/20/2018
ms.custom: fasttrack
ms.openlocfilehash: 90159b0cbfd3e7af542a79d050d153b4a3435a0d
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643816"
---
# <a name="using-service-fabric-to-decompose-monolithic-applications"></a>Uso di Service Fabric per scomporre applicazioni monolitiche

In questo scenario di esempio viene illustrato un approccio basato sull'uso di [Service Fabric](/azure/service-fabric/service-fabric-overview) come piattaforma per la scomposizione di un'applicazione monolitica difficile da gestire. Questo articolo prende in considerazione un approccio iterativo per la scomposizione di un sito Web IIS/ASP.NET in un'applicazione costituita da più microservizi gestibili.

Il passaggio da un'architettura monolitica a un'architettura di microservizi offre i vantaggi seguenti:

- È possibile modificare una piccola unità di codice comprensibile e distribuire solo tale unità.
- Ogni unità di codice richiede solo pochi minuti per la distribuzione.
- Se si verifica un errore in tale piccola unità, solo quell'unità smette di funzionare e non l'intera applicazione.
- Piccole unità di codice possono essere distribuite separatamente e in modo semplice tra più team di sviluppo.
- I nuovi sviluppatori possono comprendere rapidamente e facilmente la funzionalità distinta di ogni unità.

In questo esempio viene usata un'applicazione IIS di grandi dimensioni in una server farm, ma i concetti di scomposizione e hosting iterativi possono essere usati per qualsiasi tipo di applicazione di dimensioni elevate. Benché questa soluzione usi Windows, Service Fabric può essere eseguito anche in Linux. Può essere eseguito in locale, in Azure o nei nodi della macchina virtuale del provider di servizi cloud scelto.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Questo scenario è pertinente per le organizzazioni con applicazioni Web monolitiche di grandi dimensioni che hanno riscontrato:

- Errori in modifiche minime al codice che causano il malfunzionamento dell'intero sito Web.
- Versioni che richiedono più giorni a causa della necessità di rilasciare l'intero sito Web.
- Tempi di addestramento prolungati per l'inserimento di nuovi sviluppatori o team a causa della complessità della codebase, che richiede l'apprendimento di più informazioni di quanto sia possibile immagazzinarne.

## <a name="architecture"></a>Architettura

Usando Service Fabric come piattaforma di hosting, è possibile convertire un sito Web IIS di grandi dimensioni in una raccolta di microservizi, come illustrato di seguito:

![Diagramma dell'architettura](./media/architecture-service-fabric-complete.png)

Nell'immagine precedente tutte le parti di un'applicazione IIS di grandi dimensioni sono state scomposte in:

- Un servizio di routing o gateway che accetta le richieste in ingresso del browser, le analizza per determinare quale servizio debba gestirle e inoltra la richiesta a tale servizio.
- Quattro applicazioni ASP.NET Core che erano formalmente directory virtuali nel singolo sito IIS in esecuzione come applicazioni ASP.NET. Le applicazioni sono state separate in microservizi indipendenti. L'effetto è che possono essere modificate, rilasciate in nuove versioni e aggiornate separatamente. In questo esempio ogni applicazione è stata riscritta con .NET Core e ASP.NET Core. Le applicazioni sono state scritte come [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction) affinché possano accedere in modo nativo alle funzionalità e ai vantaggi completi della piattaforma Service Fabric (servizi di comunicazione, report sull'integrità, notifiche e così via).
- Un servizio Windows denominato *servizio di indicizzazione*, inserito in un contenitore Windows in modo che non apporti più modifiche dirette al Registro di sistema del server sottostante, ma che possa essere eseguito in modo autonomo e distribuito con tutte le relative dipendenze come singola unità.
- Un servizio di archiviazione, che è semplicemente un eseguibile che viene eseguito in base a una pianificazione e svolge alcune attività per i siti. Questo servizio è ospitato direttamente come eseguibile autonomo perché è stato determinato che esegue le operazioni necessarie senza richiedere modifiche e non vale la pena investire per cambiarlo.

## <a name="considerations"></a>Considerazioni

La prima sfida consiste nell'iniziare a identificare le parti più piccole del codice che possono essere ricavate dalla scomposizione dell'applicazione monolitica in microservizi chiamabili dal monolite. In modo iterativo nel corso del tempo, il monolite viene suddiviso in una raccolta di questi microservizi che gli sviluppatori possono comprendere e modificare facilmente, oltre a poterli distribuire velocemente con pochi rischi.

Service Fabric è stato scelto perché è in grado di supportare l'esecuzione di tutti i microservizi nelle varie forme. Ad esempio, si potrebbe usare una combinazione di file eseguibili autonomi, nuovi siti Web di piccole dimensioni, nuove API piccole, servizi in contenitori e così via. Service Fabric può combinare tutti questi tipi di servizio in un singolo cluster.

Per ottenere questa applicazione finale, scomposta, è stato usato un approccio iterativo. Si è iniziato da un sito Web IIS/ASP.NET di grandi dimensioni in una server farm. Un singolo nodo della server farm è illustrato di seguito. Contiene il sito Web originale con diverse directory virtuali, un servizio Windows aggiuntivo chiamato dal sito e un eseguibile che svolge alcune operazioni di manutenzione periodiche dell'archivio del sito.

![Diagramma dell'architettura monolitica](./media/architecture-service-fabric-monolith.png)

Nella prima iterazione dello sviluppo il sito IIS e le directory virtuali vengono inseriti in un [contenitore di Windows](/azure/service-fabric/service-fabric-containers-overview). In questo modo il sito rimane operativo, ma non è strettamente associato al sistema operativo del nodo del server sottostante. Il contenitore viene eseguito e orchestrato dal nodo di Service Fabric sottostante, ma il nodo non deve avere uno stato da cui dipende il sito (voci del Registro di sistema, file e così via). Tutti questi elementi sono nel contenitore. Anche il servizio di indicizzazione è stato inserito in un contenitore di Windows per gli stessi motivi. I contenitori possono essere distribuiti, aggiornati e ridimensionati in modo indipendente. Infine, il servizio di archiviazione è stato ospitato come semplice [file eseguibile autonomo](/azure/service-fabric/service-fabric-guest-executables-introduction) perché si tratta per l'appunto di un file EXE indipendente senza requisiti particolari.

L'immagine seguente mostra il sito Web di grandi dimensioni di partenza ora parzialmente scomposto in unità indipendenti e pronto per essere scomposto ulteriormente non appena possibile.

![Diagramma dell'architettura che mostra la scomposizione parziale](./media/architecture-service-fabric-midway.png)

La fase di sviluppo successiva è incentrata sulla separazione del singolo contenitore del sito Web predefinito di grandi dimensioni raffigurato nell'immagine precedente. Ogni app ASP.NET delle directory virtuali viene rimossa dal contenitore una alla volta e convertita in [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction) di ASP.NET Core.

Dopo la suddivisione di ogni directory virtuale, il sito Web predefinito viene scritto in forma di Reliable Services ASP.NET Core, che accetta le richieste dei browser in ingresso e le indirizza all'applicazione ASP.NET corretta.

### <a name="availability-scalability-and-security"></a>Disponibilità, scalabilità e sicurezza

Service Fabric è [in grado di supportare diverse forme di microservizi](/azure/service-fabric/service-fabric-choose-framework) semplificando e velocizzando le chiamate tra tali microservizi nello stesso cluster. Service Fabric è un cluster a [tolleranza di errore](/azure/service-fabric/service-fabric-availability-services) e a riparazione automatica che può eseguire contenitori ed eseguibili e ha persino un'API nativa per la scrittura di microservizi direttamente nel cluster ('Reliable Services' di cui sopra). La piattaforma facilita gli aggiornamenti in sequenza e il controllo delle versioni di ogni microservizio. È possibile indicare alla piattaforma di eseguire un numero maggiore o minore di un determinato microservizio distribuito nel cluster di Service Fabric in modo da gestire la [scalabilità](/azure/service-fabric/service-fabric-concepts-scalability) usando solo i microservizi necessari.

Service Fabric è un cluster basato su un'infrastruttura di nodi virtuali (o fisici), che dispongono di funzionalità di rete e archiviazione, nonché di un sistema operativo. Di conseguenza, include un set di attività amministrative, di manutenzione e di monitoraggio.

È anche opportuno tenere conto della governance e del controllo del cluster. Proprio come si vuole evitare che le persone possano distribuire in modo arbitrario database nel server di database di produzione, è preferibile non consentire la distribuzione di applicazioni nel cluster di Service Fabric senza supervisione.

Service Fabric è in grado di ospitare molti [scenari applicativi](/azure/service-fabric/service-fabric-application-scenarios) diversi, quindi si consiglia di dedicare un po' di tempo a individuare quelli appropriati per il proprio scenario.

## <a name="pricing"></a>Prezzi

Per un cluster di Service Fabric ospitato in Azure, la maggior parte dei costi è correlata al numero e alle dimensioni dei nodi nel cluster. Azure consente la creazione rapida e semplice di un cluster costituito dai nodi delle dimensioni specificate, ma i costi di calcolo sono basati sulle dimensioni del nodo moltiplicate per il numero di nodi.

Altri componenti di costo meno significativi sono i costi di archiviazione per i dischi virtuali di ogni nodo e le spese per il traffico di I/O di rete in uscita da Azure, ad esempio il traffico di rete in uscita da Azure verso il browser di un utente.

Per ottenere un'idea dei costi, è disponibile un esempio creato con alcuni valori predefiniti per dimensioni del cluster, funzionalità di rete e archiviazione: vedere [Calcolatore prezzi](https://azure.com/e/52dea096e5844d5495a7b22a9b2ccdde). È possibile aggiornare i valori in questo calcolatore predefinito selezionando quelli pertinenti per lo specifico scenario.

## <a name="next-steps"></a>Passaggi successivi

Prendersi un po' di tempo per acquisire familiarità con la piattaforma consultando la [documentazione](/azure/service-fabric/service-fabric-overview) ed esaminando i numerosi diversi [scenari applicativi](/azure/service-fabric/service-fabric-application-scenarios) per Service Fabric. Nella documentazione si possono trovare informazioni sugli elementi costitutivi di un cluster, sulle piattaforme supportate per l'esecuzione, sull'architettura software e sulla manutenzione.

Per visualizzare una dimostrazione di Service Fabric per un'applicazione .NET esistente, distribuire l'applicazione descritta in questa [guida introduttiva](/azure/service-fabric/service-fabric-quickstart-dotnet) per Service Fabric.

Dal punto di vista di un'applicazione esistente, iniziare a riflettere sulle diverse funzioni che ha. Sceglierne una e valutare come è possibile separare solo tale funzione dal resto. Affrontare una singola parte discreta e comprensibile alla volta.

## <a name="related-resources"></a>Risorse correlate

- [Creazione di microservizi in Azure](/azure/architecture/microservices)
- [Panoramica di Service Fabric](/azure/service-fabric/service-fabric-overview)
- [Modello di programmazione di Service Fabric](/azure/service-fabric/service-fabric-choose-framework)
- [Disponibilità dei servizi di Service Fabric](/azure/service-fabric/service-fabric-availability-services)
- [Scalabilità in Service Fabric](/azure/service-fabric/service-fabric-concepts-scalability)
- [Service Fabric e contenitori](/azure/service-fabric/service-fabric-containers-overview)
- [Distribuire un eseguibile esistente in Service Fabric](/azure/service-fabric/service-fabric-guest-executables-introduction)
- [Panoramica di Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction)
- [Scenari di applicazione di Service Fabric](/azure/service-fabric/service-fabric-application-scenarios)