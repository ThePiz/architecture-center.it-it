---
title: Scelta di una tecnologia di elaborazione del linguaggio naturale
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: dacf7bf9cf3e9efed212f34da93c1470954965cf
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="choosing-a-natural-language-processing-technology-in-azure"></a>Scelta di una tecnologia di elaborazione del linguaggio naturale in Azure

L'elaborazione di testo in formato libero viene eseguita su documenti che contengono paragrafi di testo, in genere allo scopo di supportare la ricerca, ma viene usata anche per eseguire altre attività di elaborazione del linguaggio naturale, ad esempio analisi del sentiment, rilevamento di argomenti, rilevamento della lingua, estrazione di frasi chiave e categorizzazione di documenti. Questo articolo illustra le opzioni di tecnologia disponibili a supporto delle attività di elaborazione del linguaggio naturale.

## <a name="what-are-your-options-when-choosing-an-nlp-service"></a>Opzioni disponibili per la scelta di un servizio di elaborazione del linguaggio naturale

In Azure, i servizi seguenti forniscono funzionalità di elaborazione del linguaggio naturale:

- [Azure HDInsight con Spark e Spark MLlib](/azure/hdinsight/spark/apache-spark-overview)
- [Servizi cognitivi Microsoft](/azure/#pivot=products&panel=cognitive)

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- Si desidera usare modelli predefiniti? In caso affermativo, può essere opportuno usare le API offerte da Servizi cognitivi Microsoft.

- È necessario eseguire il training di modelli personalizzati con una raccolta di grandi dimensioni di dati di testo? In caso affermativo, può essere opportuno usare Azure HDInsight con Spark MLlib e Spark NLP.

- Sono necessarie funzionalità di elaborazione del linguaggio naturale di base, quali tokenizzazione, stemming, lemmatizzazione e frequenza del termine/frequenza inversa del documento (TF/IDF)? In caso affermativo, può essere opportuno usare Azure HDInsight con Spark MLlib e Spark NLP.

- Sono necessarie funzionalità di elaborazione del linguaggio naturale avanzate, quali identificazione di entità e finalità, rilevamento dell'argomento, controllo ortografico o analisi del sentiment? In caso affermativo, può essere opportuno usare le API offerte da Servizi cognitivi Microsoft.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.  

### <a name="general-capabilities"></a>Funzionalità generali

| | Azure HDInsight | Servizi cognitivi Microsoft |
| --- | --- | --- |
| Fornisce modelli sottoposti a training come servizio | No | Sì |
| API REST | Sì | Sì |
| Programmabilità | Python, Scala, Java | C#, Java, Node.js, Python, PHP, Ruby |
| Supporta l'elaborazione di set di Big Data e documenti di grandi dimensioni | Sì | No |

### <a name="low-level-natural-language-processing-capabilities"></a>Funzionalità di elaborazione del linguaggio naturale di base

| | Azure HDInsight | Servizi cognitivi Microsoft |  
| --- | --- | --- | 
| Tokenizer | Sì (Spark NLP) | Sì (API Analisi linguistica) |
| Stemmer | Sì (Spark NLP) | No |
| Lemmatizer | Sì (Spark NLP) | No |
| Tag delle parti del discorso | Sì (Spark NLP) | Sì (API Analisi linguistica) |
| Frequenza del termine/Frequenza inversa del documento (TF/IDF) | Sì (Spark MLlib) | No  |
| Somiglianza tra stringhe&mdash;calcolo della distanza di modifiche | Sì (Spark MLlib) | No |
| Calcolo di n-grammi | Sì (Spark MLlib) | No |
| Eliminazione parole vuote | Sì (Spark MLlib) | No |

### <a name="high-level-natural-language-processing-capabilities"></a>Funzionalità di elaborazione del linguaggio naturale avanzate

| | Azure HDInsight | Servizi cognitivi Microsoft |
| --- | --- | --- | 
| Estrazione e identificazione di entità/finalità | No | Sì (API LUIS, Language Understanding Intelligent Service) |    
| Rilevamento di argomenti | Sì (Spark NLP) | Sì (API Analisi del testo) |
| Controllo ortografico | Sì (Spark NLP) | Sì (API Controllo ortografico Bing) |
| Analisi del sentiment | Sì (Spark NLP) | Sì (API Analisi del testo) |
| Rilevamento della lingua | No | Sì (API Analisi del testo) |
| Supporto di più lingue oltre all'inglese | No | Sì (varia in base all'API) |

## <a name="see-also"></a>Vedere anche

[Elaborazione del linguaggio naturale](../scenarios/natural-language-processing.md)