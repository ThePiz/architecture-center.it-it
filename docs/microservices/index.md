---
title: Progettazione, creazione e gestione di microservizi in Azure con Kubernetes
description: Progettazione, creazione e gestione di microservizi in Azure
author: MikeWasson
ms.date: 10/23/2018
ms.openlocfilehash: cac16c9212432c72aeaecac1a578828a00838431
ms.sourcegitcommit: fdcacbfdc77370532a4dde776c5d9b82227dff2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49962773"
---
# <a name="designing-building-and-operating-microservices-on-azure"></a>Progettazione, creazione e gestione di microservizi in Azure

![](./images/drone.svg)

I microservizi sono diventati uno stile di architettura diffuso per la creazione di applicazioni cloud che offrono resilienza, scalabilità elevata, possibilità di distribuzione indipendente e capacità di evolversi rapidamente. I microservizi non sono solo una moda, ma rappresentano un nuovo concetto che richiede un approccio diverso alla progettazione e alla creazione di applicazioni. 

In questo set di articoli viene analizzato come creare ed eseguire un'architettura di microservizi in Azure. Gli argomenti includono:

- Uso della progettazione basata su dominio per progettare un'architettura di microservizi. 
- Scelta delle tecnologie di Azure appropriate per calcolo, archiviazione, messaggistica e altri elementi del progetto.
- Comprensione degli schemi progettuali dei microservizi.
- Progettazione per la resilienza, la scalabilità e le prestazioni.
- Creazione di una pipeline di integrazione continua/distribuzione continua.


Nel corso degli articoli, viene esaminato uno scenario end-to-end: un servizio di recapito tramite drone che consente ai clienti di pianificare il prelievo e il recapito dei pacchetto tramite drone. Il codice per l'implementazione di riferimento è disponibile su GitHub

[![GitHub](../_images/github.png) Implementazione di riferimento][drone-ri]

Verranno analizzate, prima di tutto, le nozioni di base. Cosa sono i microservizi e quali sono i vantaggi dell'adozione di un'architettura di microservizi?

## <a name="why-build-microservices"></a>Perché creare microservizi?

In un'architettura di microservizi l'applicazione è composta da servizi di piccole dimensioni indipendenti. Ecco alcune caratteristiche specifiche dei microservizi:

- Ogni microservizio implementa una singola funzionalità di business.
- Le dimensioni di un microservizio sono sufficientemente piccole da consentirne la scrittura e la gestione da parte di un unico piccolo team di sviluppatori.
- I microservizi vengono eseguiti in processi separati, che comunicano tramite API o modelli di messaggistica ben definiti. 
- I microservizi non condividono archivi dati o schemi di dati. Ogni microservizio è responsabile della gestione dei propri dati. 
- I microservizi hanno basi di codice separate e non condividono il codice sorgente. Possono tuttavia usare librerie di utilità comuni.
- Ogni microservizio può essere distribuito e aggiornato in modo indipendente dagli altri servizi. 

Se eseguiti correttamente, i microservizi possono offrire molti vantaggi utili:

- **Flessibilità.** Poiché i microservizi vengono distribuiti in modo indipendente, è più facile gestire le correzioni di bug e i rilasci delle funzionalità. È possibile aggiornare un servizio senza ridistribuire l'intera applicazione ed eseguire il rollback di un aggiornamento in caso di errore. In molte applicazioni tradizionali, se viene trovato un bug in una parte dell'applicazione, può venire bloccato l'intero processo di rilascio. Di conseguenza, per le nuove funzionalità può essere necessario attendere l'integrazione, il test e la pubblicazione di una correzione di bug.  

- **Piccola quantità di codice e piccoli team.** Un microservizio deve essere sufficientemente piccolo da consentire a un singolo un team responsabile di una funzionalità di crearlo, testarlo e distribuirlo. Le basi di codice di piccole dimensioni sono più facili da comprendere. In un'applicazione monolitica di grandi dimensioni, con il tempo le dipendenze di codice tendono a diventare complesse, quindi per aggiungere una nuova funzionalità è necessario intervenire sul codice in molte posizioni. Un'architettura di microservizi non prevede la condivisione di codice o di archivi dati, quindi le dipendenze sono ridotte al minimo ed è più facile aggiungere nuove funzionalità. Team di piccole dimensioni lavorano inoltre in modo più flessibile. Secondo la "regola delle due pizze", un team deve essere sufficientemente piccolo da potersi nutrire con due pizze. Ovviamente non si tratta di una metrica esatta, visto che dipende dall'appetito del team. Il concetto, tuttavia, è che i gruppi di grandi dimensioni tendono a essere meno produttivi, perché la comunicazione è più lenta, la gestione è più complessa e la flessibilità diminuisce.  

- **Combinazione di tecnologie**. I team possono scegliere la tecnologia più adatta al servizio, usando la combinazione di stack tecnologici più appropriata. 

- **Resilienza**. Se un singolo microservizio smette di essere disponibile, non si interrompe l'intera applicazione, a condizione che i microservizi upstream siano progettati per gestire correttamente gli errori (ad esempio, implementando l'interruzione del circuito).

- **Scalabilità**. Un'architettura di microservizi consente la scalabilità di ogni microservizio indipendentemente dagli altri microservizi. È quindi possibile scalare orizzontalmente i sottosistemi che richiedono più risorse, senza scalare orizzontalmente l'intera applicazione. Se si distribuiscono servizi all'interno di contenitori, è anche possibile condensare un maggior numero di microservizi in un singolo host, per usare le risorse in modo più efficiente.

- **Isolamento dei dati**. È molto più semplice eseguire aggiornamenti dello schema, perché è interessato solo un singolo microservizio. In un'applicazione monolitica gli aggiornamenti dello schema possono diventare molto complessi, perché diverse parti dell'applicazione possono coinvolgere gli stessi dati, quindi apportare modifiche allo schema può essere rischioso.
 
## <a name="no-free-lunch"></a>Compromessi necessari

I vantaggi che si ottengono hanno un prezzo. Questa serie di articoli è pensata per esaminare alcune delle sfide associate alla creazione di microservizi resilienti, scalabili e facili da gestire.

- **Limiti dei servizi**. Quando si creano i microservizi, è necessario pensare attentamente a come definire i limiti tra i servizi. Una volta che i servizi sono stati creati e distribuiti nell'ambiente di produzione, può essere difficile effettuare il refactoring tra i limiti. La scelta dei giusti limiti dei servizi è una delle principali sfide quando si progetta un'architettura di microservizi. Quali dimensioni deve avere ogni servizio? Quando è necessario distribuire una funzionalità tra servizi diversi e quando deve essere manutenuta all'interno dello stesso servizio? In questa guida viene descritto un approccio che usa la progettazione basata su dominio per individuare i limiti dei servizi. Si inizia con l'[analisi del dominio](./domain-analysis.md) per trovare i contesti delimitati e quindi si applica un set di [schemi tattici di progettazione basata su dominio](./microservice-boundaries.md) in base ai requisiti funzionali e non funzionali. 

- **Coerenza e integrità dei dati**. Un principio alla base dei microservizi è che ogni servizio gestisce i propri dati. I servizi rimangono così separati, ma ciò può causare problemi relativi all'integrità o alla ridondanza dei dati. Alcuni di questi problemi vengono esaminati nell'articolo [Considerazioni sui dati](./data-considerations.md).

- **Congestione e latenza di rete**. L'uso di numerosi servizi granulari di piccole dimensioni può comportare un aumento delle comunicazioni tra i servizi e una maggiore latenza end-to-end. Il capitolo [Comunicazione tra i servizi](./interservice-communication.md) descrive le considerazioni relative alla messaggistica tra servizi. In un'architettura di microservizi la comunicazione avviene sia in modo sincrono che asincrono. Una buona [progettazione API](./api-design.md) è importante per garantire che i servizi rimangano a regime di controllo libero e possano essere distribuiti e aggiornati in modo indipendente.
 
- **Complessità**. Un'applicazione di microservizi ha più parti mobili. Ogni servizio può essere semplice, ma i servizi devono funzionare in combinazione come se si trattasse di un'unica soluzione. Una singola operazione utente potrebbe interessare più servizi. Nel capitolo [Inserimento e flusso di lavoro](./ingestion-workflow.md) vengono esaminati alcuni dei problemi relativi all'inserimento delle richieste a una velocità effettiva elevata, al coordinamento del flusso di lavoro e alla gestione degli errori. 

- **Comunicazione tra i client e l'applicazione.**  Quando si scompone un'applicazione in molti servizi di piccole dimensioni, come devono comunicare i client con tali servizi? Un client deve chiamare ogni singolo servizio direttamente o deve instradare le richieste attraverso un [gateway API](./gateway.md)?

- **Monitoraggio**. Il monitoraggio di un'applicazione distribuita può essere molto più complesso rispetto a quello di un'applicazione monolitica, perché è necessario mettere in correlazione i dati di telemetria di più servizi. Il capitolo [Registrazione e monitoraggio](./logging-monitoring.md) esamina questi problemi.

- **Integrazione continua e distribuzione continua**. Uno degli obiettivi principali dei microservizi è la flessibilità. Per raggiungere questo obiettivo, sono necessarie caratteristiche automatizzate e affidabili di [integrazione continua e distribuzione continua](./ci-cd.md), così da poter distribuire in modo rapido e affidabile singoli servizi negli ambienti di test e di produzione.

## <a name="the-drone-delivery-application"></a>Applicazione di recapito tramite drone

Per esaminare questi problemi e per illustrare alcune delle procedure consigliate per un'architettura di microservizi, è stata creata un'implementazione di riferimento, ovvero l'applicazione di recapito tramite drone. L'implementazione di riferimento è disponibile su [GitHub][drone-ri].

Fabrikam, Inc. sta avviando un servizio di recapito tramite drone. La società gestisce una flotta di droni. Le aziende possono registrarsi per usare il servizio e gli utenti possono richiedere un drone per prelevare merci da recapitare. Quando un cliente pianifica un prelievo, un sistema back-end assegna un drone e invia all'utente una notifica con un tempo di recapito stimato. Durante il recapito, il cliente può tenere traccia della posizione del drone, con un tempo stimato di arrivo che viene aggiornato continuamente.

Questo scenario prevede un dominio piuttosto complesso. Alcuni dei problemi aziendali da affrontare includono la pianificazione dei droni, il monitoraggio dei pacchetti, la gestione degli account utente e l'archiviazione e l'analisi dei dati cronologici. Fabrikam vuole inoltre accelerare i tempi di immissione sul mercato e di iterazione, aggiungendo nuove caratteristiche e funzionalità. L'applicazione deve operare a livello cloud, con un obiettivo del livello di servizio elevato. Fabrikam prevede inoltre che le diverse parti del sistema avranno requisiti molto diversi per l'archiviazione dei dati e le query. Tutte queste considerazioni hanno portato Fabrikam a scegliere un'architettura di microservizi per l'applicazione di recapito tramite drone.

> [!NOTE]
> Per un aiuto nella scelta tra un'architettura di microservizi e altri stili di architettura, vedere [Guida all'architettura delle applicazioni in Azure](../guide/index.md).

L'implementazione di riferimento usa Kubernetes con il [servizio Kubernetes di Azure](/azure/aks/) (AKS). Molte delle scelte e delle sfide generali relative all'architettura si applicano tuttavia a qualsiasi agente di orchestrazione di contenitori, tra cui [Azure Service Fabric](/azure/service-fabric/). 

> [!div class="nextstepaction"]
> [Analisi del dominio](./domain-analysis.md)


<!-- links -->

[drone-ri]: https://github.com/mspnp/microservices-reference-implementation
