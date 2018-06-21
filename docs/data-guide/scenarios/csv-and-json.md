---
title: Elaborazione dei file in formato CSV e JSON
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 02e684d562cfe555f9e3596ad0a2f1a00d05c7a7
ms.sourcegitcommit: 51f49026ec46af0860de55f6c082490e46792794
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
ms.locfileid: "30298609"
---
# <a name="working-with-csv-and-json-files-for-data-solutions"></a>Uso dei file in formato CSV e JSON per le soluzioni di dati

CSV e JSON sono probabilmente i formati più comuni usati per l'inserimento, lo scambio e l'archiviazione dei dati non strutturati o semistrutturati. 

## <a name="about-csv-format"></a>Informazioni sul formato CSV

I file in formato CSV (Comma-Separated Values, valori delimitati da virgole) vengono comunemente usati per lo scambio di dati tabulari tra i sistemi in testo normale. Contengono in genere una riga di intestazione per i nomi delle colonne, ma sono considerati come dati semistrutturati poiché il loro formato non può naturalmente rappresentare dati gerarchici o relazionali. Le relazioni tra i dati vengono in genere gestite con più file in formato CSV. In tal caso, le chiavi esterne sono archiviate nelle colonne di uno o più file, ma le relazioni tra i file non sono espresse dal formato stesso. Oltre alle virgole, nei file in formato CSV possono essere usati altri delimitatori, ad esempio le tabulazioni o gli spazi.

Nonostante queste limitazioni, i file in formato CSV sono comunemente usati per lo scambio di dati poiché sono supportati da un'ampia gamma di applicazioni aziendali, di tipo consumer o per uso scientifico. I programmi per database e fogli di calcolo, ad esempio, possono importare ed esportare file in questo formato. Analogamente, la maggior parte dei motori di elaborazione dei dati di flussi e batch, come Spark e Hadoop, include il supporto nativo per la serializzazione e la deserializzazione di file in formato CSV e offre tecniche per applicare uno schema in lettura. In questo modo, i dati possono essere gestiti più facilmente grazie alla possibilità di eseguire query e archiviare le informazioni in un formato efficace per una rapida elaborazione.

## <a name="about-json-format"></a>Informazioni sul formato JSON

I dati JSON (JavaScript Object Notation) sono rappresentati come coppie chiave-valore in un formato semistrutturato. Il formato JSON viene spesso paragonato al formato XML, poiché entrambi sono in grado di archiviare i dati in modo gerarchico, con i dati figlio rappresentati in linea con il relativo padre. Entrambi i formati sono inoltre autodescrittivi e leggibili, anche se i documenti JSON tendono ad avere dimensioni più piccole. Questa caratteristica ne ha determinato la diffusione per lo scambio dei dati online, soprattutto con l'introduzione dei servizi Web basati su REST. 

I file in formato JSON presentano alcuni vantaggi rispetto a quelli in formato CSV:

* JSON mantiene le strutture gerarchiche e quindi consente di includere dati correlati in un unico documento e rappresentare relazioni complesse con più facilità.
* La maggior parte dei linguaggi di programmazione offre il supporto nativo per la deserializzazione JSON in oggetti o fornisce librerie di tipo lightweight per la serializzazione JSON.
* JSON supporta gli elenchi di oggetti, consentendo così di evitare la conversione non corretta degli elenchi in un modello dati relazionale.
* JSON è un formato di file molto diffuso per i database NoSQL, ad esempio MongoDB, Couchbase e Azure Cosmos DB.

Poiché una grande quantità di dati trasferiti attraverso la rete è già in formato JSON, la maggior parte dei linguaggi di programmazione basati sul Web supporta l'uso di JSON in modo nativo o tramite l'uso di librerie esterne per serializzare e deserializzare i dati JSON. Questo supporto universale per JSON ne ha determinato la diffusione in formati logici tramite la rappresentazione di strutture di dati, in formati di scambio per i dati ad accesso frequente e in formati di archiviazione per i dati ad accesso sporadico.

Molti motori di elaborazione dei dati di flussi e batch includono il supporto nativo per la serializzazione e la deserializzazione JSON. Anche se i dati contenuti all'interno dei documenti JSON possono essere memorizzati in formati più efficienti, ottimizzati per le prestazioni, come Parquet o Avro, possono servire anche come fonte attendibile di dati non elaborati, che sono molto utili nel caso di specifiche esigenze di rielaborazione.

## <a name="when-to-use-csv-or-json-formats"></a>Quando usare i formati CSV o JSON

I file in formato CSV sono comunemente usati per l'esportazione e l'importazione di dati o per l'elaborazione mediante sistemi di analisi e apprendimento automatico. I file in formato JSON offrono gli stessi vantaggi, ma sono più diffusi nelle soluzioni per lo scambio di dati ad accesso frequente. I documenti JSON vengono spesso inviati dal Web e dai dispositivi mobili che eseguono transazioni online, dai dispositivi di Internet delle cose (IoT) per le comunicazioni unidirezionali o bidirezionali o dalle applicazioni client che comunicano con servizi SaaS e PaaS o architetture senza server. 

Entrambi i formati di file CSV e JSON presentano il vantaggio di facilitare lo scambio di dati tra sistemi o dispositivi diversi. Il carattere semistrutturato dei dati offre la flessibilità per trasferire quasi tutti i tipi di dati e il supporto universale disponibile per questi formati ne facilita notevolmente l'uso. Entrambi possono essere usati come fonte affidabile di dati non elaborati, nel caso in cui quelli elaborati siano archiviati in formati binari, per eseguire query in modo più efficiente. 

## <a name="working-with-csv-and-json-data-in-azure"></a>Uso dei dati in formato CSV e JSON in Azure

Azure offre diverse soluzioni per l'uso dei file in formato CSV e JSON, a seconda delle esigenze. Le principali aree di destinazione per i file in questo formato sono costituite da Archiviazione di Azure e Azure Data Lake Store. Quasi tutti i servizi di Azure che fanno uso di questi e di altri file basati su testo sono integrati con uno di questi due servizi di archiviazione di oggetti. In alcuni casi, tuttavia, è possibile scegliere di importare direttamente i dati in un database SQL di Azure o in un altro archivio dati. SQL Server offre il supporto nativo per l'archiviazione e l'uso di documenti JSON e consente quindi di [importare ed elaborare questi tipi di file](/sql/relational-databases/json/import-json-documents-into-sql-server) con facilità. È inoltre possibile usare l'utilità SQL Bulk Import per eseguire facilmente l'[importazione di file in formato CSV](/sql/relational-databases/json/import-json-documents-into-sql-server).

A seconda dello scenario, è possibile eseguire l'[elaborazione batch](../big-data/batch-processing.md) o l'[elaborazione in tempo reale](../big-data/real-time-processing.md) dei dati.

## <a name="challenges"></a>Problematiche

Quando si usano questi formati è necessario tenere conto di alcune problematiche:

* Indipendentemente dal modello di dati, i file in formato CSV e JSON sono soggetti al rischio di danneggiamento dei dati ("garbage in, garbage out"). Ad esempio, in nessuno dei due formati di file è prevista la nozione di oggetto data/ora e pertanto non esiste alcun vincolo che impedisce l'inserimento di testo generico, come "ABC123", in un campo data.

* L'uso di file in formato CSV e JSON come soluzione di archiviazione offline sicura non consente una scalabilità adeguata quando si gestiscono Big Data. Nella maggior parte dei casi, i file di questo tipo non possono essere suddivisi in partizioni per l'elaborazione parallela e non consentono una compressione efficace come quella dei binari formati. È per questo motivo che questi dati vengono spesso elaborati e archiviati in formati ottimizzati per la lettura, come Parquet e ORC (Optimized Row Columnar), che forniscono anche indici e statistiche inline sui dati contenuti.

* Può essere necessario applicare uno schema ai dati semistrutturati per facilitare l'esecuzione di query e analisi. Ciò richiede in genere l'archiviazione dei dati in un formato diverso, conforme ai requisiti di archiviazione dell'ambiente in uso, ad esempio all'interno di un database.

