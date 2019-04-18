---
title: Assegnazione dei punteggi con i modelli R in Azure batch
description: Eseguire con i modelli R con Azure Batch e un set di dati basato su vendite al dettaglio store le previsioni di vendita di assegnazione punteggio batch.
author: njray
ms.date: 03/29/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 72769cf078596f0312a1f4293205dda5a086ef41
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59639904"
---
# <a name="batch-scoring-of-r-machine-learning-models-on-azure"></a>Valutazione dei modelli di machine learning di R in Azure batch

Questa architettura di riferimento illustra come eseguire l'assegnazione del punteggio con modelli R con Batch di Azure batch. Lo scenario è basato sulla previsione della vendita store delle vendite al dettaglio, ma questa architettura può essere generalizzata per qualsiasi scenario che richiedono la generazione di stime su un grande scaler usando i modelli R. Un'implementazione di riferimento per questa architettura è disponibile in [GitHub][github].

![Diagramma dell'architettura][0]

**Scenario**: Una catena di supermercati deve prevedere le vendite di prodotti in trimestre imminente. La previsione consente all'azienda di gestire meglio la supply chain e verificare che è possibile soddisfare la domanda per i prodotti in ciascuno dei relativi oggetti Store. L'azienda Aggiorna rispettive previsioni ogni settimana di nuovi dati di vendita da settimana precedente diventano disponibili mentre il prodotto della strategia di marketing per trimestre successivo. Quantile previsioni vengono generate per stimare l'incertezza di previsioni di vendita singoli.

L'elaborazione prevede i passaggi seguenti:

1. Un'App per la logica di Azure attiva il processo di generazione previsione una volta alla settimana.

1. L'app per la logica viene avviata un'istanza di contenitore di Azure che esegue l'utilità di pianificazione contenitore Docker, che attiva i processi di assegnazione dei punteggi nel cluster di Batch.

1. Assegnazione dei punteggi i processi eseguiti in parallelo tra i nodi del cluster di Batch. Ogni nodo:

    1. Estrae il ruolo di lavoro come immagine Docker da Docker Hub e avvia un contenitore.

    1. Legge i dati di input e pre-Training modelli di R da un archivio Blob di Azure.

    1. Assegna un punteggio a dati per produrre previsioni.

    1. Scrive i risultati di previsione in archiviazione blob.

La figura seguente mostra le vendite previste per i quattro prodotti (SKU) in un archivio. La linea nera è la cronologia di vendita, la linea tratteggiata è il valore mediano (q50) di previsione, la banda rosa rappresenta i percentili venti quinto e fa quattrocentonovantadue quinto e la striscia di colore blu rappresenta i quinto e il 90 quinto percentili.

![Previsioni di vendita][1]

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

[Azure Batch] [ batch] viene usato per eseguire i processi di generazione di previsione in parallelo in un cluster di macchine virtuali. Le previsioni vengono eseguite usando macchina con training preliminare implementati in Azure Batch di R. i modelli di apprendimento può ridimensionare automaticamente il numero di macchine virtuali in base al numero di processi inviati al cluster. In ogni nodo, uno script R viene eseguito all'interno di un contenitore Docker da assegnare un punteggio dei dati e generare previsioni.

[Archiviazione Blob di Azure] [ blob] viene usato per archiviare i dati di input, i modelli di apprendimento automatico con training preliminare e i risultati di previsione. Offre archiviazione molto conveniente per le prestazioni che richiede questo carico di lavoro.

[Istanze di contenitore di Azure] [ aci] fornire calcolo senza server su richiesta. In questo caso, un'istanza di contenitore viene distribuita in una pianificazione per attivare i processi Batch che generano le previsioni. I processi di Batch vengono attivati da uno script R con la [doAzureParallel][doAzureParallel] pacchetto. L'istanza di contenitore arresta automaticamente dopo aver completato i processi.

[Le app di Azure per la logica] [ logic-apps] attivare l'intero flusso di lavoro distribuendo le istanze di contenitore in una pianificazione. Un connettore di istanze di contenitore di Azure nelle App per la logica consente a un'istanza per la distribuzione su un intervallo di eventi del trigger.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

### <a name="containerized-deployment"></a>Distribuzione in contenitori

Questa architettura, tutti gli script R eseguiti all'interno [Docker](https://www.docker.com/) contenitori. Ciò garantisce che gli script eseguiti in un ambiente coerente, con la stessa versione di R e le versioni dei pacchetti, ogni volta. Diverse immagini Docker vengono usate per i contenitori dell'utilità di pianificazione e di lavoro, perché ognuno ha un diverso set di dipendenze dei pacchetti R.

Istanze di contenitore di Azure offre un ambiente senza server per eseguire il contenitore dell'utilità di pianificazione. Il contenitore dell'utilità di pianificazione esegue uno script R che attiva l'assegnazione dei punteggi singoli processi in esecuzione in un cluster di Azure Batch.

Ogni nodo del cluster di Batch viene eseguito il contenitore del ruolo di lavoro, che esegue lo script di assegnazione dei punteggi.

### <a name="parallelizing-the-workload"></a>Parallelizzare il carico di lavoro

Quando batch assegnare punteggi ai dati con i modelli R, prendere in considerazione come parallelizzare il carico di lavoro. I dati di input devono essere partizionati in qualche modo, in modo che l'operazione di assegnazione dei punteggi può essere distribuito nei nodi del cluster. Tentare approcci diversi per individuare la scelta migliore per distribuire il carico di lavoro. Nel caso per caso, considerare quanto segue:

- La quantità di dati possono essere caricati ed elaborati nella memoria di un singolo nodo.
- Il sovraccarico di avvio del processo ogni batch.
- Il sovraccarico di caricamento dei modelli R.

Nello scenario usato in questo esempio, gli oggetti modello sono di grandi dimensioni e richiede solo pochi secondi per generare una previsione per i singoli prodotti. Per questo motivo, è possibile raggruppare i prodotti ed eseguire un singolo processo di Batch per ogni nodo. Un ciclo all'interno di ogni processo genera le previsioni per i prodotti in modo sequenziale. Questo metodo si rivela il modo più efficiente per parallelizzare il carico di lavoro specifico. Evita il sovraccarico di avvio molti processi Batch più piccoli e più volte il caricamento dei modelli R.

Un approccio alternativo consiste nell'attivare un processo Batch per ogni prodotto. Azure Batch automaticamente costituisce una coda dei processi e li invia a essere eseguito nel cluster di nodi appena disponibili. Uso [scalabilità automatica] [ autoscale] per regolare il numero di nodi del cluster in base al numero di processi. Questo approccio risulta più utile se impiegato un tempo relativamente lungo per il completamento di ogni operazione di assegnazione dei punteggi, giustificare il sovraccarico di avviare i processi e il ricaricamento di oggetti modello. Questo approccio è inoltre più semplice da implementare e ti offre la flessibilità necessaria per usare la scalabilità automatica, una considerazione importante se le dimensioni del carico di lavoro totale non sono noto in anticipo.

## <a name="monitoring-and-logging-considerations"></a>Considerazioni relative a monitoraggio e registrazione

### <a name="monitoring-azure-batch-jobs"></a>Monitoraggio dei processi di Azure Batch

Monitoraggio e terminazione dei processi di Batch dal **processi** riquadro dell'account Batch nel portale di Azure. Monitorare il cluster di batch, compreso lo stato dei singoli nodi, il **pool** riquadro.

### <a name="logging-with-doazureparallel"></a>Registrazione con il pacchetto doAzureParallel

Il pacchetto doAzureParallel raccoglie automaticamente i log di tutti i stdout/stderr per ogni processo inviato in Azure Batch. Questi sono disponibili nell'account di archiviazione creato al momento dell'installazione. Per visualizzarli, usare uno strumento di esplorazione di archiviazione, ad esempio [Azure Storage Explorer] [ storage-explorer] o il portale di Azure.

Per eseguire rapidamente il debug dei processi di Batch durante lo sviluppo, stampa i log di sessione R locale usando il [getJobFiles][getJobFiles] funzione del pacchetto doAzureParallel.

## <a name="cost-considerations"></a>Considerazioni sul costo

Le risorse di calcolo usate in questa architettura di riferimento sono i componenti più costosi. Per questo scenario, ogni volta che viene attivato e quindi chiusa dopo che il processo venga completato il processo viene creato un cluster di dimensioni fisse. Costi vengono addebitati solo mentre i nodi del cluster sono avvio, l'esecuzione o arresto. Questo approccio è adatto per uno scenario in cui le risorse di calcolo necessari per generare le previsioni rimangono abbastanza costanti da un processo a processo.

Negli scenari in cui la quantità di calcolo necessaria per completare il processo non è noto in anticipo, potrebbe essere più opportuno usare la scalabilità automatica. Con questo approccio, le dimensioni del cluster viene ridimensionata verso l'alto o verso il basso a seconda delle dimensioni del processo. Azure Batch supporta una gamma di formule di scalabilità automatica che è possibile impostare quando si definisce il cluster usando il [doAzureParallel][doAzureParallel] API.

Per alcuni scenari, il tempo tra i processi potrebbe essere troppo breve per arrestare e avviare il cluster. In questi casi, mantenere il cluster in esecuzione tra i processi se appropriato.

Azure Batch e doAzureParallel supporta l'uso di macchine virtuali con priorità bassa. Queste macchine virtuali includono un sconto significativo ma rischio viene prevista da altri carichi di lavoro con priorità superiore. L'uso di queste macchine virtuali non sono quindi consigliati per i carichi di lavoro di produzione critici. Tuttavia, sono molto utili per sperimentali o i carichi di lavoro di sviluppo.

## <a name="deployment"></a>Distribuzione

Per distribuire questa architettura di riferimento, seguire i passaggi descritti nel [GitHub][github] repository.

[0]: ./_images/batch-scoring-r-models.png
[1]: ./_images/sales-forecasts.png
[aci]: /azure/container-instances/container-instances-overview
[autoscale]: /azure/batch/batch-automatic-scaling
[batch]: /azure/batch/batch-technical-overview
[blob]: /azure/storage/blobs/storage-blobs-introduction
[doAzureParallel]: https://github.com/Azure/doAzureParallel/blob/master/docs/32-autoscale.md
[getJobFiles]: /azure/machine-learning/service/how-to-train-ml-models
[github]: https://github.com/Azure/RBatchScoring
[logic-apps]: /azure/logic-apps/logic-apps-overview
[storage-explorer]: /azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows