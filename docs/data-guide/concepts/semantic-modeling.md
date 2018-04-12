---
title: Modellazione semantica
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 343d17af0d933d515c724a062237c8d5df3a9e31
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2018
---
# <a name="semantic-modeling"></a>Modellazione semantica

Un modello di dati semantico è un modello concettuale che descrive il significato degli elementi di dati in esso contenuti. Le organizzazioni usano spesso i propri termini per elementi o operazioni, talvolta con sinonimi, o persino diversi significati per lo stesso termine. Un database di inventario potrebbe ad esempio registrare un dispositivo con un ID asset e un numero di serie, ma un database di vendite potrebbe fare riferimento al numero di serie come ID asset. Non esiste un modo semplice per correlare questi valori senza un modello che descriva la relazione. 

La modellazione semantica garantisce un livello di astrazione dello schema del database, in modo che non sia necessario che gli utenti conoscano le strutture di dati sottostanti. Questo semplifica l'esecuzione di query sui dati per gli utenti finali senza eseguire aggregazioni e join nello schema sottostante. Le colonne inoltre vengono in genere rinominate usando nomi più descrittivi, in modo da rendere più ovvi il contesto e il significato dei dati.

La modellazione semantica è prevalentemente usata per gli scenari con intensa attività di lettura, ad esempio l'analisi e la business intelligence (OLAP), anziché per l'elaborazione dei dati transazionali con più intensa attività di scrittura (OLTP). Ciò è dovuto principalmente alla natura di un livello semantico tipico:

- I comportamenti di aggregazione vengono impostati in modo che gli strumenti di report li visualizzino correttamente.
- Vengono definiti i calcoli e la logica di business.
- Vengono inclusi calcoli basati sul tempo.
- I dati sono spesso integrati da più origini. 

Per questi motivi in genere il livello semantico è posizionato in un data warehouse.

![Diagramma di esempio di un livello semantico tra un data warehouse e uno strumento di report](./images/semantic-modeling.png)

Esistono due tipi principali di modelli semantici:

* **Tabulare**. Usa costrutti di modellazione relazionale (modello, tabelle, colonne). Internamente i metadati vengono ereditati da costrutti di modellazione OLAP (cubi, dimensioni, misure). Il codice e lo script usano metadati OLAP.
* **Multidimensionale**. Usa costrutti di modellazione OLAP tradizionali (cubi, dimensioni, misure).

Servizio di Azure pertinente:
- [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/)

## <a name="example-use-case"></a>Esempio di caso d'uso

I dati di un'organizzazione sono archiviati in un database di grandi dimensioni. L'organizzazione vuole rendere questi dati disponibili per gli utenti di business e i clienti per la creazione di report e l'esecuzione di analisi. Un'opzione consiste nel dare agli utenti accesso diretto al database. Questa operazione presenta tuttavia numerosi svantaggi, tra cui la gestione della sicurezza e il controllo dell'accesso. La progettazione del database, inclusi i nomi delle tabelle e delle colonne, potrebbe inoltre essere difficile da comprendere per l'utente. Gli utenti dovrebbero sapere su quali tabelle eseguire le query, come unire le tabelle e conoscere altre regole di logica di business da applicare per ottenere risultati corretti. Per poter anche solo iniziare, gli utenti dovrebbero anche conoscere un linguaggio di query come SQL. Di conseguenza, spesso più utenti usano le stesse metriche ma ottengono risultati diversi.

Un'altra opzione consiste nell'incapsulare tutte le informazioni necessarie in un modello semantico. Per gli utenti può essere più semplice eseguire query su un modello semantico con uno strumento di report di propria scelta. Il pull dei dati forniti dal modello semantico è stato eseguito da un data warehouse, consentendo in tal modo a tutti gli utenti di vedere un'unica versione della realtà. Il modello semantico garantisce anche nomi descrittivi di colonna e tabella, relazioni tra tabelle, descrizioni, calcoli e sicurezza a livello di riga.

## <a name="typical-traits-of-semantic-modeling"></a>Caratteristiche tipiche della modellazione semantica

La modellazione semantica e l'elaborazione analitica hanno in genere le caratteristiche seguenti:

| Requisito | DESCRIZIONE |
| --- | --- |
| SCHEMA | Schema durante la scrittura, fortemente applicato|
| Uso delle transazioni | No  |
| Strategia di blocco | Nessuna |
| Aggiornabile | No (in genere richiede il ricalcolo del cubo) |
| Accodamento | No (in genere richiede il ricalcolo del cubo) |
| Carico di lavoro | Operazioni di lettura intense, sola lettura |
| Indicizzazione | Indicizzazione multidimensionale |
| Dimensioni dati | Da piccole a medie |
| Modello | Multidimensionale |
| Forma dei dati:| Cubo o schema star/snowflake |
| Flessibilità query | Flessibilità elevata |
| Scalabilità: | Grande (10-100 GB) |

## <a name="see-also"></a>Vedere anche 

- [Data warehousing](../scenarios/data-warehousing.md)
- [OLAP (Online Analytical Processing)](../scenarios/online-analytical-processing.md)
