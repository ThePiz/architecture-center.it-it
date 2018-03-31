---
title: Analisi avanzata
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 31ba357fe37b1de35a6eea324d2d1d6766e172e5
ms.sourcegitcommit: 29fbcb1eec44802d2c01b6d3bcf7d7bd0bae65fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="advanced-analytics"></a>Analisi avanzata

Le soluzioni di analisi avanzata offrono funzionalità più potenti rispetto alla creazione di report cronologici e all'aggregazione di dati delle soluzioni di business intelligence tradizionali, usando tecniche di modellazione matematica, probabilistica e statistica per consentire l'elaborazione predittiva e l'automazione dei processi decisionali.

Le soluzioni di analisi avanzata richiedono in genere i carichi di lavoro seguenti:

* Esplorazione e visualizzazione dei dati in modalità interattiva
* Training dei modelli di apprendimento automatico
* Elaborazione predittiva in tempo reale o in batch

La maggior parte delle architetture di analisi avanzata include alcuni o tutti i componenti illustrati di seguito:

* **Archiviazione dei dati**. Le soluzioni di analisi avanzata richiedono dati per il training dei modelli di apprendimento automatico. I data scientist devono in genere esplorare i dati per identificare le caratteristiche predittive, le relazioni statistiche che intercorrono tra di loro e i valori che sono in grado di stimare, noti come etichette. L'etichetta stimata può essere costituita da un valore quantitativo, ad esempio il valore finanziario di un evento futuro o il ritardo di un volo in minuti, oppure può rappresentare una classe di categorizzazione, come "vero" o "falso", "ritardo volo" o "nessun ritardo volo", o categorie come "basso rischio", "medio rischio" o "alto rischio".

* **Elaborazione batch**. Per eseguire il training di un modello di apprendimento automatico è in genere necessario elaborare una quantità notevole di dati di training. Training del modello può richiedere del tempo, nell'ordine di minuti ma anche di ore. Questo training può essere eseguito mediante script, creati ad esempio in linguaggio Python o R, e può essere distribuito tra più sistemi, per limitare i tempi di elaborazione, usando piattaforme di elaborazione distribuita come Apache Spark ospitato in HDInsight o un contenitore Docker.

* **Inserimento di messaggi in tempo reale**. Negli ambienti di produzione molte soluzioni di analisi avanzata alimentano flussi di dati in tempo reale destinati a un modello predittivo pubblicato come servizio Web. Il flusso di dati in ingresso viene in genere acquisito sotto forma di coda. Un motore di elaborazione del flusso esegue il pull dei dati dalla coda e applica la stima ai dati di input quasi in tempo reale.  

* **Elaborazione del flusso**. Una volta completato il training di un modello, la stima (o assegnazione di un punteggio) per un determinato set di caratteristiche è in genere un'operazione molto rapida, nell'ordine di millisecondi. Dopo l'acquisizione dei messaggi in tempo reale, i valori significativi delle caratteristiche possono essere passati al servizio predittivo per generare un'etichetta stimata.

* **Archivio dati analitici**. In alcuni casi, i valori delle etichette stimate vengono scritti nell'archivio dati analitici per la creazione di report e per analisi future.

* **Analisi e creazione di report**. Come suggerito dal nome, le soluzioni di analisi avanzata producono in genere una sorta di feed per analisi e report che include valori di dati stimati. I valori delle etichette stimate vengono usati per popolare i dashboard in tempo reale.

* **Orchestrazione**. Anche se le attività iniziali di esplorazione e modellazione dei dati vengono eseguite dai data scientist in modo interattivo, molte soluzioni di analisi avanzata ripetono periodicamente il training dei modelli con nuovi dati, in modo da perfezionare costantemente la precisione dei modelli. La ripetizione del processo di training può essere automatizzata mediante un flusso di lavoro orchestrato.

## <a name="machine-learning"></a>Machine learning
L'apprendimento automatico è una tecnica di modellazione matematica usata per eseguire il training di un modello predittivo. Il principio generale consiste nell'applicazione di un algoritmo statistico a un ampio set di dati cronologici per individuare le relazioni tra i vari campi.

La modellazione dell'apprendimento automatico viene normalmente eseguita da data scientist, che devono esaminare attentamente i dati e prepararli prima di eseguire il training di un modello. L'esplorazione e la preparazione dei dati richiedono in genere molte attività interattive di visualizzazione e analisi, che vengono eseguite tramite linguaggi come Python e R in strumenti e ambienti interattivi, appositamente progettati per queste attività.

In alcuni casi possono essere disponibili [modelli sottoposti a training preliminare](/machine-learning-server/install/microsoftml-install-pretrained-models), che contengono dati di training ottenuti e sviluppati da Microsoft. Questi modelli offrono il vantaggio di poter assegnare un punteggio a nuovi contenuti e classificarli immediatamente, anche se non si hanno i dati di training necessari, le risorse per gestire grandi set di dati o eseguire il training di modelli complessi.

Esistono due tipologie generali di apprendimento automatico:

* **Apprendimento supervisionato**. Questo è l'approccio più diffuso per l'apprendimento automatico. In un modello di apprendimento supervisionato i dati di origine sono costituiti da un set di campi dati di *caratteristica* che hanno una relazione matematica con uno o più campi dati di *etichetta*. Durante la fase di training del processo di apprendimento automatico, il set di dati include sia le caratteristiche sia le etichette note e viene applicato un algoritmo per modellare una funzione che agisce sulle caratteristiche per calcolare le etichette stimate corrispondenti. In genere, viene messo da parte un subset dei dati di training per convalidare le prestazioni del modello. Al termine del training, il modello può essere distribuito in produzione e usato per stimare valori sconosciuti. 

* **Apprendimento non supervisionato**. In un modello di apprendimento non supervisionato i dati di training non includono valori di etichetta noti. Diversamente da quanto avviene nell'altro modello, l'algoritmo esegue le stime in base alla prima esposizione ai dati. La forma più comune di apprendimento non supervisionato è definita *clustering*. In questo caso, l'algoritmo individua il modo migliore per suddividere i dati in un numero specificato di cluster in base ad analogie tra le caratteristiche determinate con criteri statistici. Nel clustering il risultato stimato è dato dal numero di cluster a cui appartengono le caratteristiche di input. Le tecniche di apprendimento non supervisionato possono talvolta essere applicate direttamente per generare stime utili, ad esempio usando il clustering per identificare gruppi di utenti in un database di clienti, ma sono più spesso usate per identificare i dati più utili da fornire a un algoritmo di apprendimento supervisionato durante il training di un modello.

Servizi di Azure pertinenti:

- [Azure Machine Learning](/azure/machine-learning/)
- [Machine Learning Server (R Server) in HDInsight](/azure/hdinsight/r-server/r-server-overview)

## <a name="deep-learning"></a>Apprendimento avanzato

Per un certo tempo sono stati disponibili modelli di apprendimento automatico basati su tecniche matematiche come la regressione lineare o logistica. Più di recente si sono diffuse tecniche di *apprendimento avanzato* basate sulle reti neurali. Questa diffusione è dovuta in parte alla disponibilità di sistemi di elaborazione altamente scalabili che consentono di ridurre il tempo necessario per eseguire il training di modelli complessi. Inoltre, grazie alla crescente prevalenza dei Big Data, è possibile eseguire più facilmente il training di modelli di apprendimento avanzato in un'ampia gamma di domini.

Quando si progetta un'architettura cloud per l'analisi avanzata, è opportuno valutare la necessità di elaborare modelli di apprendimento avanzato su larga scala. Questi possono essere forniti tramite piattaforme di elaborazione distribuita come Apache Spark e macchine virtuali di ultima generazione che includono l'accesso all'hardware di GPU.

Servizi di Azure pertinenti:

- [Macchina virtuale per l'apprendimento avanzato](/azure/machine-learning/data-science-virtual-machine/deep-learning-dsvm-overview)
- [Apache Spark in HDInsight](/azure/hdinsight/spark/apache-spark-overview)

## <a name="artificial-intelligence"></a>Intelligenza artificiale

Il termine "intelligenza artificiale" fa riferimento agli scenari in cui un computer è in grado di simulare le funzioni cognitive associate alla mente umana, ad esempio l'apprendimento e la risoluzione dei problemi. Poiché l'intelligenza artificiale fa uso di algoritmi di apprendimento automatico, questo termine può essere usato in riferimento a un concetto più generico. La maggior parte delle soluzioni di intelligenza artificiale si basa su una combinazione di servizi predittivi, spesso implementati come servizi Web, e interfacce basate su linguaggio naturale, ad esempio chatbot che interagiscono tramite SMS o audio, presentate da app di intelligenza artificiale eseguite su dispositivi mobili o altri client. In alcuni casi il modello di apprendimento automatico è incorporato con l'app di intelligenza artificiale. 

## <a name="model-deployment"></a>Distribuzione di modelli

I servizi predittivi che supportano applicazioni di intelligenza artificiale possono sfruttare modelli di apprendimento automatico personalizzati o servizi cognitivi preconfigurati che consentono di accedere a modelli sottoposti a training preliminare. Nel processo di distribuzione di modelli personalizzati nell'ambiente di produzione, gli stessi modelli di intelligenza artificiale sottoposti a training e testati nell'ambiente di elaborazione vengono serializzati e resi disponibili ad applicazioni e servizi esterni per l'esecuzione di stime in modalità batch o self-service. Per sfruttare la capacità predittiva del modello, questo viene deserializzato e caricato tramite la stessa libreria di apprendimento automatico che contiene l'algoritmo usato per il training iniziale. Questa libreria fornisce funzioni predittive (o per l'assegnazione di un punteggio) che accettano come input il modello e le caratteristiche e restituiscono la stima elaborata. Questa logica viene quindi sottoposta a wrapping in una funzione che può essere chiamata direttamente da un'applicazione oppure esposta come servizio Web. 

Servizi di Azure pertinenti:

- [Azure Machine Learning](/azure/machine-learning/)
- [Machine Learning Server (R Server) in HDInsight](/azure/hdinsight/r-server/r-server-overview)


## <a name="see-also"></a>Vedere anche 

- [Scelta di una tecnologia di servizi cognitivi](../technology-choices/cognitive-services.md)
- [Scelta di una tecnologia di apprendimento automatico](../technology-choices/data-science-and-machine-learning.md)
