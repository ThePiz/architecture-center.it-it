---
title: Elaborazione del linguaggio naturale
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: c03e2d017f9b4eb955a0e3494b5bc6c2603d1058
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="natural-language-processing"></a>Elaborazione del linguaggio naturale

L'elaborazione del linguaggio naturale (NLP, Natural Language Processing) viene usata per attività quali l'analisi del sentiment, il rilevamento di argomenti, il rilevamento della lingua, l'estrazione di frasi chiave e la categorizzazione di documenti.

![](./images/nlp-pipeline.png)

## <a name="when-to-use-this-solution"></a>Quando usare questa soluzione

L'elaborazione del linguaggio naturale può essere usata per classificare i documenti, ad esempio per etichettarli come sensibili o indesiderati. L'output dell'elaborazione del linguaggio naturale può essere usato per attività successive di elaborazione o ricerca. Un altro possibile uso è quello di fornire una sintesi di un documento identificando le entità presenti nel testo. Queste entità possono essere usate anche per assegnare tag ai documenti usando parole chiave, in modo da facilitarne la ricerca e il recupero in base al contenuto. Le entità possono essere combinate in argomenti, con informazioni di riepilogo che descrivono gli argomenti importanti presenti in ogni documento. Gli argomenti rilevati possono essere usati per classificare i documenti e facilitarne l'esplorazione o per enumerare i documenti correlati in base a un argomento selezionato. Un altro uso dell'elaborazione del linguaggio naturale consiste nell'assegnare un punteggio al testo per valutare il tono positivo o negativo di un documento, ovvero il relativo sentiment. Queste soluzioni d'uso sfruttano molte tecniche adottate nell'elaborazione del linguaggio naturale, ad esempio: 

- **Tokenizer**. Suddivisione del testo in parole o combinazioni di parole.
- **Stemming e lemmatizzazione**. Normalizzazione delle parole in modo da consentire l'associazione delle diverse forme alla parola canonica con lo stesso significato. Ad esempio, "avuto" e "ha" sono associate ad "avere". 
- **Estrazione di entità**. Identificazione di argomenti nel testo.
- **Rilevamento delle parti del discorso**. Identificazione delle parti del testo come verbo, nome, participio, sintagma verbale e così via.
- **Rilevamento dei limiti delle frasi**. Rilevamento di frasi intere all'interno dei paragrafi di testo.

Quando si usa l'elaborazione del linguaggio naturale per estrarre informazioni e dati significativi da testo in formato libero, il punto di partenza è costituito in genere da documenti non elaborati archiviati in un servizio per l'archiviazione di oggetti come Archiviazione di Azure o Azure Data Lake Store. 

## <a name="challenges"></a>Problematiche

- L'elaborazione di una raccolta di documenti di testo in formato libero è in genere un'operazione impegnativa a livello di calcolo che richiede lunghi tempi di elaborazione.
- Senza un formato standardizzato per i documenti, può essere molto difficile ottenere risultati precisi e coerenti usando l'elaborazione di testo in formato libero per estrarre dati specifici da un documento. Se si considera ad esempio una rappresentazione testuale di una fattura, può essere difficile creare un processo che consenta di estrarre correttamente il numero e la data delle fatture di più fornitori.

## <a name="architecture"></a>Architettura

In una soluzione di elaborazione del linguaggio naturale, l'elaborazione di testo in formato libero viene eseguita su documenti che contengono paragrafi di testo. L'architettura globale può essere basata su una pipeline di [elaborazione batch](./batch-processing.md) o di [elaborazione del flusso in tempo reale](./real-time-processing.md).

Il processo di elaborazione effettivo varia a seconda del risultato desiderato, ma al livello della pipeline, l'elaborazione del linguaggio naturale può essere applicata in modalità batch o in tempo reale. Si prenda ad esempio un'analisi del sentiment applicata a blocchi di testo per generare un punteggio di valutazione. Un'analisi di questo tipo può essere eseguita con un processo batch sui dati presenti nel servizio di archiviazione oppure in tempo reale, usando blocchi di dati più piccoli trasmessi in flusso tramite un servizio di messaggistica.

## <a name="technology-choices"></a>Scelte di tecnologia

- [Elaborazione del linguaggio naturale](../technology-choices/natural-language-processing.md)