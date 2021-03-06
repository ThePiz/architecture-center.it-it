---
title: 'CAF: Le 5 "R" della razionalizzazione'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: Esaminare le opzioni disponibili per la razionalizzazione di un digital estate.
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.openlocfilehash: ee196487e6f59b1e1b3c63bab9496cbbf805affd
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55897474"
---
# <a name="the-5-rs-of-rationalization"></a>Le 5 "R" della razionalizzazione

La razionalizzazione del cloud è il processo di valutazione degli asset per determinare l'approccio migliore per la migrazione o la modernizzazione di ogni asset nel cloud. Per altre informazioni sul processo di razionalizzazione, consultare [Che cos'è un digital estate?](overview.md)

Le 5 "R" della razionalizzazione elencate di seguito descrivono le opzioni più comuni per la razionalizzazione.

## <a name="rehost"></a>Rehosting

Nota anche come "lift and shift", un'attività di rehost consente di spostare l'asset dello stato corrente nel provider di servizi cloud scelto, con modifiche minime all'architettura complessiva.

I fattori comuni possono includere:

* Riduzione delle spese in conto capitale
* Spazio libero nel data center
* Rapido ritorno sugli investimenti nel cloud

Fattori di analisi quantitativa:

* Dimensioni della macchina virtuale (CPU, memoria, archiviazione)
* Dipendenze (traffico di rete)
* Compatibilità degli asset

Fattori di analisi qualitativa:

* Tolleranza alle modifiche
* Priorità aziendali
* Eventi aziendali critici
* Dipendenze dei processi

## <a name="refactor"></a>Refactoring

Le opzioni di piattaforma distribuita come un servizio (PaaS) possono ridurre i costi operativi associati a molte applicazioni. Può essere opportuno effettuare un leggero refactoring di un'applicazione per adattare un modello basato su PaaS.

Questo fa riferimento anche al refactoring del codice nel processo di sviluppo dell'applicazione per consentire a quest'ultima di mantenere nuove opportunità commerciali.

I fattori comuni possono includere:

* Aggiornamenti più veloci e più brevi
* Portabilità del codice
* Maggiore efficienza del cloud (risorse, velocità, costo)

Fattori di analisi quantitativa:

* Dimensioni degli asset dell'applicazione (CPU, memoria, archiviazione)
* Dipendenze (traffico di rete)
* Traffico utente (visualizzazioni pagina, tempo su una pagina, tempo di caricamento)
* Piattaforma di sviluppo (linguaggi, piattaforme di dati, servizi di livello intermedio)

Fattori di analisi qualitativa:

* Investimenti aziendali continui
* Aumento delle opzioni/sequenze temporali
* Dipendenze dei processi aziendali

## <a name="rearchitect"></a>Riprogettazione

Alcune applicazioni obsolete non sono compatibili con i provider di servizi cloud a causa delle decisioni di progettazione prese quando sono state compilate. In questi casi, l'applicazione potrebbe dover essere riprogettata prima della trasformazione.

In altri casi, le applicazioni compatibili con il cloud, ma senza vantaggi cloud-native, possono essere efficienti in termini di costi e operatività se la soluzione viene riprogettata per essere un'applicazione nativa del cloud.

I fattori comuni possono includere:

* Scalabilità e flessibilità dell'applicazione
* Adozione più semplice di nuove funzionalità del cloud
* Miscela di stack di tecnologie

Fattori di analisi quantitativa:

* Dimensioni degli asset dell'applicazione (CPU, memoria, archiviazione)
* Dipendenze (traffico di rete)
* Traffico utente (visualizzazioni pagina, tempo su una pagina, tempo di caricamento)
* Piattaforma di sviluppo (linguaggi, piattaforme di dati, servizi di livello intermedio)

Fattori di analisi qualitativa:

* Investimenti aziendali in crescita
* Costi operativi
* Potenziali cicli di feedback e investimenti per DevOps

## <a name="rebuild"></a>Ricompilazione

In alcuni scenari, il delta da risolvere per trasferire un'applicazione può essere troppo grande per giustificare un ulteriore investimento. Questo vale soprattutto per le applicazioni usate per soddisfare le esigenze dell'azienda, ma non più supportate o non più allineate con la modalità attuale di esecuzione dei processi. In questo caso, viene creata una nuova base di codice in linea con l'approccio [cloud nativo](https://azure.microsoft.com/overview/cloudnative/).

I fattori comuni possono includere:

* Accelerazione dell'innovazione
* Compilazione delle app più veloce
* Riduzione dei costi operativi

Fattori di analisi quantitativa:

* Dimensioni degli asset dell'applicazione (CPU, memoria, archiviazione)
* Dipendenze (traffico di rete)
* Traffico utente (visualizzazioni pagina, tempo su una pagina, tempo di caricamento)
* Piattaforma di sviluppo (linguaggi, piattaforme di dati, servizi di livello intermedio)

Fattori di analisi qualitativa:

* Calo di soddisfazione dell'utente finale
* Processi aziendali limitati per funzionalità
* Potenziali guadagni in termini di costi, esperienza o ricavi

## <a name="replace"></a>Replace

Le soluzioni, in genere, sono implementate usando la tecnologia e l'approccio migliore possibile al momento. In alcuni casi, le applicazioni di software come servizio (SaaS) possono soddisfare tutti i requisiti di funzionalità richiesti dall'applicazione ospitata. In questi scenari, si può destinare un carico di lavoro a una futura sostituzione, rimuovendolo con efficacia dall'attività di trasformazione.

I fattori comuni possono includere:

* Standardizzazione secondo le procedure consigliate del settore
* Accelerazione dell'adozione di approcci basati su processi aziendali
* Riallocazione di investimenti di sviluppo in applicazioni che creano una differenziazione o vantaggi competitivi

Fattori di analisi quantitativa:

* Riduzione dei costi operativi generali
* Dimensioni della macchina virtuale (CPU, memoria, archiviazione)
* Dipendenze (traffico di rete)
* Asset da ritirare

Fattori di analisi qualitativa:

* Analisi costi-benefici dell'architettura attuale rispetto alla soluzione SaaS
* Mapping dei processi aziendali
* Schemi di dati
* Processi personalizzati o automatizzati

## <a name="next-steps"></a>Passaggi successivi

Nel complesso, queste 5 "R" della razionalizzazione sono applicabili a un digital estate per decisioni in materia di razionalizzazione relative allo stato futuro di ciascuna applicazione.

> [!div class="nextstepaction"]
> [Informazioni sul digital estate](overview.md)
