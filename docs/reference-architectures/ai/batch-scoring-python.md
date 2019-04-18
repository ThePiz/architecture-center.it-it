---
title: Assegnazione del punteggio in batch per i modelli Python in Azure
description: Creare una soluzione scalabile per l'assegnazione del punteggio in batch per i modelli in base a una pianificazione in parallelo tramite il servizio Azure Machine Learning.
author: njray
ms.date: 01/30/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai, AI
ms.openlocfilehash: 9341b9e4c17025e9623902a6202076c352b237b9
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640550"
---
# <a name="batch-scoring-of-python-machine-learning-models-on-azure"></a>Valutazione dei modelli di machine learning di Python in Azure batch

Questa architettura di riferimento illustra come creare una soluzione scalabile per l'assegnazione del punteggio in batch a molti modelli in base a una pianificazione in parallelo usando il servizio Azure Machine Learning. La soluzione può essere usata come modello e supporta la generalizzazione per problemi diversi.

Un'implementazione di riferimento per questa architettura è disponibile in [GitHub][github].

![Assegnazione del punteggio in batch per i modelli Python in Azure](./_images/batch-scoring-python.png)

**Scenario**: Questa soluzione consente di monitorare il funzionamento di un numero elevato di dispositivi in uno scenario IoT in cui ogni dispositivo invia le letture dei sensori in modo continuo. Si presuppone che ogni dispositivo sia associato a modelli di rilevamento delle anomalie sottoposti precedentemente a training per prevedere se una serie di misurazioni, aggregate per un intervallo di tempo predefinito, corrispondono o meno a un'anomalia. In scenari reali, potrebbe trattarsi di un flusso di letture di sensori che devono essere filtrate e aggregate prima di essere usate per operazioni di training o per l'assegnazione del punteggio in tempo reale. Per semplicità, la soluzione usa lo stesso file di dati durante l'esecuzione dei processi di assegnazione del punteggio.

Questa architettura di riferimento è progettata per carichi di lavoro che vengono attivati in base a una pianificazione. L'elaborazione prevede i passaggi seguenti:

1. Inviare le letture dei sensori per l'inserimento in Hub eventi di Azure.
2. Eseguire l'elaborazione dei flussi e archiviare i dati non elaborati.
3. Inviare i dati a un cluster di Machine Learning pronto a iniziare ad assumere lavoro. Ogni nodo del cluster esegue un processo di assegnazione del punteggio per uno specifico sensore. 
4. Eseguire la pipeline di assegnazione del punteggio, che esegue i processi in parallelo usando script Python di Machine Learning. La pipeline viene creata, pubblicata ed eseguita in un intervallo di tempo predefinito e pianificato.
5. Generare previsioni e archiviarle in archiviazione BLOB per un utilizzo successivo.

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti:

[Hub eventi di Azure][event-hubs]. Questo servizio di inserimento dei messaggi può inserire milioni di messaggi di eventi al secondo. In questa architettura, i sensori inviano un flusso di dati all'hub eventi.

[Analisi di flusso di Azure][stream-analytics]. Un motore di elaborazione di eventi. Un processo di Analisi di flusso legge i flussi di dati dall'hub eventi ed esegue l'elaborazione dei flussi.

[Database SQL di Azure][sql-database]. I dati ricavati dalle letture dei sensori vengono caricati nel database SQL. SQL è uno strumento familiare in cui archiviare i flussi di dati elaborati (che sono tabulari e strutturati), ma è possibile usare altri archivi dati.

[Servizio Azure Machine Learning][amls]. Machine Learning è un servizio cloud per il training, l'assegnazione di punteggi, la distribuzione e la gestione di modelli di Machine Learning su vasta scala. Nel contesto dell'assegnazione di punteggi in batch, Machine Learning crea un cluster di macchine virtuali su richiesta con un'opzione di ridimensionamento automatico, in cui ogni nodo esegue un processo di assegnazione del punteggio per un sensore specifico. I processi di assegnazione del punteggio vengono eseguiti in parallelo con i passaggi dello script Python accodati e gestiti da Machine Learning. Questi passaggi fanno parte di una pipeline di Machine Learning che viene creata, distribuita ed eseguita in un intervallo di tempo predefinito e pianificato.

[Archiviazione BLOB di Azure][storage]. I contenitori BLOB vengono usati per archiviare i modelli già sottoposti a training, i dati e le stime di output. I modelli vengono caricati nell'archiviazione BLOB nel notebook [01_create_resources.ipynb][create-resources]. I modelli [one-class SVM][one-class-svm] vengono sottoposti a training su dati che rappresentano i valori di sensori diversi per diversi dispositivi. Questa soluzione presuppone che i valori dei dati vengano aggregati per un intervallo di tempo fisso.

[Registro Azure Container][acr]. Lo [script][pyscript] Python di assegnazione del punteggio viene eseguito in contenitori Docker che vengono creati in ogni nodo del cluster, in cui legge i dati del sensore pertinenti, genera previsioni e le archivia nell'archivio Blob.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Per i modelli Python standard, le CPU sono generalmente considerate sufficienti per gestire il carico di lavoro. Questa architettura usa CPU. Tuttavia, per [carichi di lavoro di apprendimento avanzato][deep], GPU in genere prestazioni CPU di una quantità considerevole &mdash; un cluster di numero di CPU in genere è necessaria per ottenere prestazioni analoghe.

### <a name="parallelizing-across-vms-versus-cores"></a>La parallelizzazione tra le macchine virtuali e core

Durante l'esecuzione dei processi di assegnazione del punteggio a molti modelli in modalità batch, i processi devono essere eseguiti in parallelo tra le macchine virtuali. Sono possibili due approcci:

* Creare un cluster più grande con macchine virtuali a basso costo.

* Creare un cluster più piccolo con macchine virtuali a prestazioni elevate con più core disponibili in ognuna.

In generale, l'assegnazione del punteggio a modelli di Python standard non è così impegnativa come l'assegnazione del punteggio a modelli di Deep Learning e un cluster piccolo dovrebbe deve essere in grado di gestire in modo efficiente un numero elevato di modelli in coda. È possibile aumentare il numero di nodi del cluster man mano che aumentano le dimensioni dei set di dati.

Per motivi di praticità, in questo scenario viene inviata un'unica attività di assegnazione del punteggio all'interno di un singolo passaggio della pipeline di Machine Learning. Tuttavia, può essere più efficiente assegnare il punteggio a più blocchi di dati all'interno dello stesso passaggio della pipeline. In questi casi, scrivere codice personalizzato per leggere in più set di dati ed eseguire lo script di assegnazione del punteggio per tali set durante l'esecuzione di un singolo passaggio.

## <a name="management-considerations"></a>Considerazioni sulla gestione

- **Monitoraggio dei processi**. È importante monitorare lo stato dei processi in esecuzione, ma può essere complesso eseguire il monitoraggio in un cluster di nodi attivi. Per esaminare lo stato dei nodi del cluster, usare il [portale di Azure][portal] per gestire l'[area di lavoro di Machine Learning][ml-workspace]. Se un nodo è inattivo oppure un processo ha avuto esito negativo, i log degli errori vengono salvati nell'archiviazione BLOB e sono accessibili anche nella sezione Pipeline. Per un monitoraggio più completo, connettere i log ad [Application Insights][app-insights] o eseguire processi separati per il polling dello stato del cluster e dei relativi processi.
- **Registrazione**. Il servizio Machine Learning registra tutti i flussi StdOut/StdErr nell'account di archiviazione di Azure associato. Per semplificare la visualizzazione dei file di log, usare uno strumento di esplorazione dell'archiviazione, ad esempio [Azure Storage Explorer][explorer].

## <a name="cost-considerations"></a>Considerazioni sul costo

I componenti più onerosi usati in questa architettura di riferimento sono le risorse di calcolo. Le dimensioni del cluster di calcolo possono aumentare o diminuire a seconda dei processi presenti nella coda. Abilitare il ridimensionamento automatico a livello di codice tramite l'SDK Python modificando la configurazione del provisioning del calcolo. In alternativa, usare l'[interfaccia della riga di comando di Azure][cli] per impostare i parametri di ridimensionamento automatico del cluster.

Per le operazioni che non richiedono un intervento immediato, configurare la formula di scalabilità automatica in modo che lo stato predefinito (minimo) sia rappresentato da un cluster con un numero di nodi pari a zero. Con questa configurazione, il cluster inizia con un numero di nodi pari a zero, per poi aumentare quando rileva processi nella coda. Se il processo di assegnazione del punteggio in batch si verifica poche volte al giorno, questa impostazione consente di ottenere un risparmio significativo sui costi.

La scalabilità automatica potrebbe non essere appropriata per i processi batch eseguiti a distanza troppo ravvicinata. Anche il tempo necessario per avviare e interrompere un cluster comporta dei costi. Pertanto, se un carico di lavoro batch inizia solo pochi minuti dopo il termine del processo precedente, potrebbe essere più conveniente lasciare il cluster attivo tra i processi. Ciò dipende dalla frequenza pianificata per l'esecuzione dei processi di assegnazione del punteggio, ovvero elevata (ad esempio, ogni ora) o meno frequente (ad esempio, una volta al mese).

## <a name="deployment"></a>Distribuzione

Per distribuire questa architettura di riferimento, seguire la procedura descritta nel [repository GitHub][github].

[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[automatic-scaling]: /azure/batch/batch-automatic-scaling
[azure-files]: /azure/storage/files/storage-files-introduction
[cli]: /cli/azure
[create-resources]: https://github.com/Microsoft/AMLBatchScoringPipeline/blob/master/01_create_resources.ipynb
[deep]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[event-hubs]: /azure/event-hubs/event-hubs-geo-dr
[explorer]: https://azure.microsoft.com/en-us/features/storage-explorer/
[github]: https://github.com/Microsoft/AMLBatchScoringPipeline
[one-class-svm]: http://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html
[portal]: https://portal.azure.com
[ml-workspace]: /azure/machine-learning/studio/create-workspace
[python-script]: https://github.com/Azure/BatchAIAnomalyDetection/blob/master/batchai/predict.py
[pyscript]: https://github.com/Microsoft/AMLBatchScoringPipeline/blob/master/scripts/predict.py
[storage]: /azure/storage/blobs/storage-blobs-overview
[stream-analytics]: /azure/stream-analytics/
[sql-database]: /azure/sql-database/
[app-insights]: /azure/application-insights/app-insights-overview
