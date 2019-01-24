---
title: Stile di architettura di microservizi
titleSuffix: Azure Application Architecture Guide
description: Illustra i vantaggi, le problematiche e le procedure consigliate per le architetture di microservizi in Azure.
author: MikeWasson
ms.date: 11/13/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19, microservices
ms.openlocfilehash: cc72f61003f4146fd65e501feebda0c0d1d27993
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485129"
---
# <a name="microservices-architecture-style"></a>Stile di architettura di microservizi

Un'architettura di microservizi è costituita da un insieme di servizi ridotti autonomi. Ogni servizio è indipendente e deve implementare una singola funzionalità di business.

![Diagramma logico dello stile di un'architettura di microservizi](./images/microservices-logical.svg)

Per alcuni aspetti, i microservizi sono l'evoluzione naturale delle architetture orientate ai servizi (SOA), ma esistono alcune differenze tra i microservizi e le architetture SOA. Ecco alcune caratteristiche specifiche di un microservizio:

- In un'architettura di microservizi i servizi sono ridotti, indipendenti e a regime di controllo libero.

- Ogni servizio è una base di codici distinta, che può essere gestita da un team di sviluppo di piccole dimensioni.

- I servizi possono essere distribuiti in modo indipendente. Un team può aggiornare un servizio esistente senza ricompilare e ridistribuire l'intera applicazione.

- I servizi sono responsabili della persistenza dei propri dati o dello stato esterno. Questo comportamento differisce dal modello tradizionale, in cui la persistenza dei dati viene gestita da un livello dati distinto.

- I servizi comunicano tra loro tramite API ben definite. I dettagli di implementazione interna di ogni servizio sono nascosti da altri servizi.

- Non è necessario che i servizi condividano lo stesso stack di tecnologie, le stesse librerie o gli stessi framework.

Oltre ai servizi, in un'architettura di microservizi tipica compaiono alcuni altri componenti:

**Gestione**. Il componente di gestione è responsabile del posizionamento dei servizi sui nodi, dell'identificazione degli errori, del ribilanciamento dei servizi tra i nodi e così via.

**Individuazione del servizio**. Gestisce un elenco di servizi e dei nodi in cui si trovano. Consente la ricerca del servizio per individuare l'endpoint per un servizio.

**Gateway API**. Il gateway API è il punto di ingresso per i client. I client non chiamano direttamente i servizi, ma chiamano il gateway API, che inoltra la chiamata ai servizi appropriati sul back-end. Il gateway API potrebbe aggregare le risposte da diversi servizi e restituire la risposta aggregata.

Tra i vantaggi dell'uso di un gateway API rientrano i seguenti:

- Separa i client dai servizi, di cui può essere effettuato il refactoring o il controllo delle versioni senza la necessità di aggiornare tutti i client.

- I servizi possono usare protocolli di messaggistica non compatibili con il Web, come AMQP.

- Il gateway API può eseguire altre funzioni trasversali, come l'autenticazione, la registrazione, la terminazione SSL e il bilanciamento del carico.

## <a name="when-to-use-this-architecture"></a>Quando usare questa architettura

Prendere in considerazione questo stile di architettura per:

- Applicazioni di grandi dimensioni che richiedono una velocità elevata.

- Applicazioni complesse che devono essere estremamente scalabili.

- Applicazioni con domini avanzati o con un numero elevato di sottodomini.

- Un'organizzazione costituita da team di sviluppo di piccole dimensioni.

## <a name="benefits"></a>Vantaggi

- **Distribuzioni indipendenti**. È possibile aggiornare un servizio senza ridistribuire l'intera applicazione ed eseguire il rollback o il roll forward di un aggiornamento in caso di errore. Le correzioni di bug e i rilasci di funzionalità sono più semplici da gestire e meno rischiosi.

- **Sviluppo indipendente**. Un singolo team di sviluppo può compilare, testare e distribuire un servizio. Il risultato è un'innovazione continua e un ritmo di rilascio più veloce.

- **Team focalizzati di piccole dimensioni**. I team possono concentrarsi su un servizio. L'ambito più ridotto di ciascun servizio rende la base di codici più facile da comprendere ed è più semplice per i nuovi membri del team essere immediatamente operativi.

- **Isolamento degli errori**. Se un servizio smette di funzionare, il problema non influirà sull'intera applicazione. Ciò non implica tuttavia che la resilienza sia assicurata senza mettere in atto misure adeguate. È necessario seguire gli schemi progettuali e le procedure consigliate per la resilienza. Vedere [Progettazione di applicazioni resilienti per Azure][resiliency-overview].

- **Stack di tecnologie miste**. I team possono scegliere la tecnologia che meglio si adatta ai servizi in uso.

- **Ridimensionamento granulare**. I servizi possono essere ridimensionati in modo indipendente. Allo stesso tempo, la maggiore densità dei servizi per ogni macchina virtuale significa che le risorse delle macchine virtuali vengono pienamente usate. Tramite i vincoli di posizionamento, un servizio può essere associato a un profilo di macchina virtuale (uso elevato della CPU, memoria alta e così via).

## <a name="challenges"></a>Problematiche

- **Complessità**. Un'applicazione di microservizi ha più parti mobili rispetto all'applicazione monolitica equivalente. Ogni servizio è più semplice, ma l'intero sistema nella sua totalità è più complesso.

- **Sviluppo e test**. L'attività di sviluppo rispetto alle dipendenze dei servizi richiede un approccio diverso. Gli strumenti esistenti non sono necessariamente progettati per poter essere usati con le dipendenze dei servizi. Effettuare il refactoring tra i limiti dei servizi può essere difficile. Può risultare complesso anche testare le dipendenze dei servizi, specialmente quando l'applicazione è in rapida evoluzione.

- **Mancanza di governance**. L'approccio decentralizzato alla creazione di microservizi presenta alcuni vantaggi, ma può anche causare problemi. Potrebbero esserci così tanti linguaggi e framework diversi che l'applicazione diventa difficile da gestire. Può essere utile applicare alcuni standard a livello di progetto, senza limitare eccessivamente la flessibilità dei team. Questo vale in special modo per le funzionalità trasversali come la registrazione.

- **Congestione e latenza di rete**. L'uso di molti servizi granulari di dimensioni ridotte può comportare una maggiore comunicazione tra i servizi. Inoltre, se la catena delle dipendenze dei servizi diventa troppo lunga (il servizio A chiama il servizio B, che a sua volta chiama il servizio C...), l'ulteriore latenza può diventare un problema. Sarà necessario progettare le API con attenzione. Evitare API eccessivamente "frammentate", considerare i formati di serializzazione e cercare posizioni appropriate in cui usare modelli di comunicazione asincrona.

- **Integrità dei dati**. Con ogni microservizio responsabile della persistenza dei propri dati, la conseguenza è che la coerenza dei dati può risultare un problema. Implementare la coerenza finale dove possibile.

- **Gestione**. Per una corretta implementazione dei microservizi è necessaria una conoscenza avanzata di DevOps. La registrazione correlata tra i servizi può risultare complessa. In genere, la registrazione deve correlare più chiamate al servizio per un'operazione richiesta da un singolo utente.

- **Controllo delle versioni**. Gli aggiornamenti a un servizio non devono interrompere i servizi che dipendono da esso. Potrebbero essere aggiornati più servizi in un dato momento, pertanto senza un'attenta progettazione si potrebbero verificare problemi di compatibilità con le versioni precedenti o successive.

- **Competenze**. I microservizi sono sistemi altamente distribuiti. Valutare attentamente se il team dispone delle competenze e dell'esperienza per implementarli correttamente.

## <a name="best-practices"></a>Procedure consigliate

- Modellare i servizi attorno al dominio aziendale.

- Decentralizzare tutti gli elementi. I singoli team sono responsabili della progettazione e della creazione dei servizi. Evitare di condividere schemi di dati o codice.

- L'archiviazione dei dati dovrebbe essere privata per il servizio proprietario dei dati. Usare l'archiviazione migliore per ogni servizio e tipo di dati.

- I servizi comunicano attraverso API ben progettate. Evitare la divulgazione dei dettagli dell'implementazione. Le API dovrebbero modellare il dominio, non l'implementazione interna del servizio.

- Evitare l'accoppiamento tra servizi. Le cause dell'accoppiamento includono schemi del database condivisi e protocolli di comunicazione rigidi.

- Delegare al gateway le questioni trasversali, come l'autenticazione e la terminazione SSL.

- Non comunicare al gateway le informazioni di dominio. Il gateway dovrebbe gestire e instradare le richieste client senza dover necessariamente conoscere le regole business o la logica di dominio. In caso contrario, il gateway diventa una dipendenza e può causare l'accoppiamento tra servizi.

- I servizi dovrebbero avere un regime di controllo libero e un'elevata coesione funzionale. Le funzioni che potrebbero essere modificate contemporaneamente devono essere inserite in pacchetto e distribuite insieme. Se si trovano in servizi separati, tali servizi finiranno per risultare strettamente accoppiati, perché una modifica in un servizio richiederà l'aggiornamento dell'altro servizio. Le comunicazioni eccessivamente "frammentate" tra due servizi possono essere un sintomo di accoppiamento stretto e bassa coesione.

- Isolare gli errori. Usare strategie di resilienza per impedire che gli errori all'interno di un servizio si propaghino. Vedere [Resiliency patterns][resiliency-patterns] (Modelli di resilienza) e [Progettazione di applicazioni resilienti][resiliency-overview].

## <a name="next-steps"></a>Passaggi successivi

Per indicazioni dettagliate per la creazione di un'architettura di microservizi in Azure, vedere [Progettazione, creazione e gestione di microservizi in Azure](../../microservices/index.md).

<!-- links -->

[resiliency-overview]: ../../resiliency/index.md
[resiliency-patterns]: ../../patterns/category/resiliency.md
