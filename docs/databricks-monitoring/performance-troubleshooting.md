---
title: Risoluzione dei problemi per Azure Databricks tramite il monitoraggio di Azure
titleSuffix: ''
description: Dashboard di Grafana usare per risolvere i problemi di prestazioni in Azure Databricks
author: petertaylor9999
ms.date: 04/02/2019
ms.topic: ''
ms.service: ''
ms.subservice: ''
ms.openlocfilehash: 49ec63d0c45ab388ca83b3ab0562428327539619
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59740375"
---
# <a name="troubleshoot-performance-bottlenecks-in-azure-databricks"></a>Risolvere i problemi di colli di bottiglia delle prestazioni in Azure Databricks

Questo articolo descrive come usare i dashboard di monitoraggio per individuare colli di bottiglia delle prestazioni nei processi di Spark in Azure Databricks.

[Azure Databricks](/azure/azure-databricks/) è un [Apache Spark](https://spark.apache.org/)– basato su servizio analitica che rende più semplice sviluppare e distribuire analitica dei big data rapidamente. Monitoraggio e risoluzione dei problemi di prestazioni è critico quando i carichi di lavoro di produzione Azure Databricks. Per identificare problemi di prestazioni comuni, è utile usare monitoraggio di visualizzazioni basate sui dati di telemetria.

## <a name="prerequisites"></a>Prerequisiti

Per configurare il dashboard di Grafana mostrato in questo articolo:

- Configurare il cluster Databricks per l'invio di dati di telemetria a un'area di lavoro di Log Analitica usando la libreria di monitoraggio di Azure Databricks. Per informazioni dettagliate, vedere [configurare Azure Databricks per inviare le metriche a monitoraggio di Azure](./configure-cluster.md).

- Distribuire Grafana in una macchina virtuale. Visualizzare [Usa i dashboard per visualizzare le metriche di Azure Databricks](./dashboards.md).

Il dashboard di Grafana distribuito include un set di visualizzazioni di serie temporali. Ogni grafico viene tracciato di serie temporali delle metriche correlate a un processo Apache Spark, le fasi del processo e attività che costituiscono ogni fase.

## <a name="azure-databricks-performance-overview"></a>Cenni preliminari sulle prestazioni di Azure Databricks

Azure Databricks è basato su Apache Spark, un sistema di elaborazione distribuito per utilizzo generico. Codice dell'applicazione, noto come un **processo**, viene eseguita in un cluster Apache Spark, coordinato dalla gestione di cluster. In generale, un processo è l'unità di livello più elevato di calcolo. Un processo rappresenta l'operazione completa eseguita dall'applicazione Spark. Un'operazione tipica include la lettura dei dati da un'origine, l'applicazione di trasformazioni di dati e scrittura i risultati nell'archiviazione o un'altra destinazione.

I processi sono suddivise **fasi**. Il processo passa attraverso le fasi in sequenza, il che significa che le fasi successive devono attendere per i passaggi precedenti per il completamento. Fasi contengono gruppi di identici **attività** che possono essere eseguiti in parallelo su più nodi del cluster Spark. Le attività sono l'unità più granulare di esecuzione in esecuzione su un subset dei dati.

Le sezioni successive descrivono alcune visualizzazioni dashboard che sono utili per la risoluzione dei problemi di prestazioni.

## <a name="job-and-stage-latency"></a>Latenza processo e fase

Latenza del processo è la durata dell'esecuzione del processo da quando viene avviato finché non verrà completata. Viene visualizzato come i percentili di esecuzione del processo per ogni ID cluster e dell'applicazione, per consentire la visualizzazione degli outlier. Cronologia di un processo il grafico seguente mostra dove il 90 ° percentile raggiunto 50 secondi, anche se il 50.mo percentile è stato in modo coerente di circa 10 secondi.

![Grafico che mostra la latenza processo](./_images/grafana-job-latency.png)

Esaminare l'esecuzione del processo dal cluster e dell'applicazione, cercando i picchi della latenza. Una volta identificate i cluster e applicazioni con una latenza elevata, passare ad analizzare la latenza di fase.

Latenza di fase vengono visualizzata anche come i percentili per consentire la visualizzazione degli outlier. Latenza di fase è influenzata dalla cluster, l'applicazione e nome fase. Identificare i picchi della latenza di attività nel grafico per determinare le attività che mantengono attivi nuovamente il completamento della fase.

![Grafico che mostra la latenza di fase](./_images/grafana-stage-latency.png)

Il grafico della velocità effettiva del cluster Mostra il numero di processi, fasi e le attività completate al minuto. Ciò aiuta a comprendere il carico di lavoro in termini di numero di fasi e attività per processo relativo. Qui noterete che il numero di processi al minuto è compreso tra 2 e 6, mentre il numero di fasi è circa 12 &ndash; 24 al minuto.

![Velocità effettiva di cluster con grafico](./_images/grafana-cluster-throughput.png)

## <a name="sum-of-task-execution-latency"></a>Somma di latenza di esecuzione attività

Questa visualizzazione mostra la somma della latenza di esecuzione di attività per ogni host in esecuzione in un cluster. Usare questo grafico per rilevare le attività eseguite lentamente a causa di un host rallentano le prestazioni in un cluster o un inadeguata di attività per ogni executor. Nel grafico seguente, la maggior parte degli host ha una somma di circa 30 secondi. Tuttavia, due degli host hanno somme che passa il mouse circa 10 minuti. Gli host sono lenti o il numero di attività per ogni executor è misallocated.

![Somma di grafico che mostra dell'esecuzione di attività per ogni host](./_images/grafana-sum-task-exec.png)

Il numero di attività per ogni executor mostra che due executors sono assegnati un numero sproporzionato di attività, causando un collo di bottiglia.

![Grafico che mostra l'attività per ogni executor](./_images/grafana-tasks-per-exec.png)

## <a name="task-metrics-per-stage"></a>Metriche di attività per ogni fase

La visualizzazione di metriche di attività fornisce la scomposizione dei costi per l'esecuzione di un'attività. È possibile usare vedere tempo relativo impiegato in attività quali la serializzazione e deserializzazione. Tali dati potrebbero mostrare le opportunità di ottimizzazione &mdash; ad esempio, usando [broadcast variabili](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#broadcast-variables) per evitare la distribuzione dei dati. Le metriche di attività mostrano anche le dimensioni dei dati di riproduzione casuale per un'attività e il sequenza casuale di lettura e scrittura volte. Se questi valori sono elevati, significa che una grande quantità di dati è in movimento attraverso la rete.

Un'altra metrica di attività è il ritardo dell'utilità di pianificazione, che misura il tempo necessario per pianificare un'attività. In teoria, questo valore deve essere basso rispetto all'ora di calcolo di executor, ovvero il tempo impiegato per effettivamente l'esecuzione dell'attività.

Il grafico seguente viene illustrato un tempo di ritardo dell'utilità di pianificazione (3.7 s) che supera il tempo di calcolo dell'executor (1.1 s). Ciò significa che viene impiegato il tempo più in attesa di attività da pianificare rispetto a svolgere il lavoro effettivo.

![Grafico che mostra le metriche di attività per ogni fase](./_images/grafana-metrics-per-stage.png)

In questo caso, il problema è stato causato da un numero eccessivo di partizioni, che ha causato una grande quantità di overhead. Riduzione del numero di partizioni ridotto il tempo di ritardo dell'utilità di pianificazione. Il grafico successivo viene illustrata la maggior parte dei casi viene impiegato per l'esecuzione dell'attività.

![Grafico che mostra le metriche di attività per ogni fase](./_images/grafana-metrics-per-stage2.png)

## <a name="streaming-throughput-and-latency"></a>La latenza e velocità effettiva dello streaming

Lo streaming a velocità effettiva è direttamente correlato al flusso strutturato. Esistono due metriche importanti associate con velocità effettiva dello streaming: Righe di input al secondo ed elaborate righe al secondo. Se le righe di input al secondo outpaces righe elaborate al secondo, significa che il flusso di sistema di elaborazione è in ritardo. Inoltre, se i dati di input provengono dall'hub eventi o Kafka, le righe di input al secondo devono mantenere con la frequenza di inserimento dei dati nel front-end.

Due processi possono avere una velocità effettiva del cluster simile ma molto diverse metriche di streaming. Lo screenshot seguente illustra due diversi carichi di lavoro. Sono simili in termini di velocità effettiva di cluster (processi, fasi e attività al minuto). Ma la seconda esecuzione elabora 12.000 righe/sec rispetto a 4.000 righe/sec.

![Velocità effettiva dello streaming di grafico che mostra](./_images/grafana-streaming-throughput.png)

Velocità effettiva dello streaming è spesso una metrica aziendale migliore rispetto a una velocità effettiva del cluster, in quanto misura il numero di record dei dati che vengono elaborati.

## <a name="resource-consumption-per-executor"></a>Utilizzo delle risorse per ogni executor

Queste metriche consentono di comprendere il lavoro che esegue ogni executor.

**Le metriche percentuale** quanto tempo un executor trascorso delle varie operazioni, espressi come una percentuale di tempo impiegato rispetto al tempo di calcolo complessivo dell'executor di misure. Le metriche sono:

- % Tempo di serializzare
- % Tempo di deserializzare
- % Executor tempo CPU
- % Tempo di JVM

Queste visualizzazioni mostrano quanto ognuna di queste metriche contribuisce a complessiva dell'executor di elaborazione.

![Grafico che mostra le metriche percentuale](./_images/grafana-percentage.png)

**Le metriche di riproduzione casuale** metriche correlate ai dati eseguendo la riproduzione casuale tra executor.

- / O casuale
- Memoria di riproduzione casuale
- Utilizzo del file system
- Utilizzo del disco

## <a name="common-performance-bottlenecks"></a>Colli di bottiglia delle prestazioni comuni

Sono due colli di bottiglia comuni delle prestazioni di Spark *attività ritardi* e una *numero di partizioni non ottimale shuffle*.

### <a name="task-stragglers"></a>Ritardi di attività

Le fasi di un processo vengono eseguite in sequenza, con le fasi successive di blocco nelle fasi precedenti. Se un'attività una partizione casuale viene eseguita più lentamente rispetto ad altre attività, tutte le attività del cluster devono attendere per l'attività lenta possono essere aggiornati prima possibile terminare la fase. Questa situazione può verificarsi per i motivi seguenti:

1. Un host o un gruppo di host sono lenti. Sintomi: Attività ad alta, fase, o processo latenza e velocità effettiva bassa cluster. La somma di latenze di attività per ogni host non verrà distribuita in modo uniforme. Utilizzo delle risorse, tuttavia, verrà distribuito in modo uniforme tra executor.

1. Le attività presentano un'aggregazione costosa da eseguire (inclinazione di dati). Sintomi: Attività elevato, fase o processo e velocità effettiva bassa cluster, ma la somma di latenza per ogni host viene distribuito uniformemente. Utilizzo delle risorse verrà distribuito in modo uniforme tra executor.

1. Se le partizioni sono di dimensioni differenti, una partizione più grande può causare l'esecuzione dell'attività sbilanciata (inclinazione di partizione). Sintomi: Consumo di risorse dell'executor è elevato rispetto a altro executor in esecuzione nel cluster. Tutte le attività in esecuzione su tale executor verranno eseguito lentamente e tenere premuto l'esecuzione della fase della pipeline. Queste fasi rientrano *fase barriere*.

### <a name="non-optimal-shuffle-partition-count"></a>Numero di partizioni shuffle non ottimale

Durante una query di streaming strutturata, l'assegnazione di un'attività da parte di un esecutore è un'operazione a elevato utilizzo di risorse per il cluster. Se i dati di riproduzione casuale non sono la dimensione ottima, la quantità di ritardo per un'attività influirà negativamente velocità effettiva e latenza. Se sono presenti un numero troppo ridotto di partizioni, saranno sottoutilizzate le memorie centrali del cluster che può comportare l'elaborazione di inefficienza. Viceversa, se sono presenti troppe partizioni, vi è una grande quantità di sovraccarico di gestione per un numero limitato di attività.

Usare le metriche di consumo di risorse per risolvere i problemi di inclinazione di partizione e inadeguata di executor nel cluster. Se una partizione è sfasata e le risorse di executor verranno elevate rispetto ad altri executor in esecuzione nel cluster.

Ad esempio, il grafico seguente mostra che la memoria usata da una riproduzione casuale sui primi due executor è 90 X dimensioni superiori a altri executor:

![Grafico che mostra le metriche percentuale](./_images/grafana-shuffle-memory.png)