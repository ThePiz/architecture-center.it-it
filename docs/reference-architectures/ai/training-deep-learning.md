---
title: Training distribuito di modelli di Deep Learning in Azure
description: Questa architettura di riferimento mostra come svolgere un training distribuito dei modelli di Deep Learning tra cluster di VM abilitate per GPU usando Azure Batch per intelligenza artificiale.
author: njray
ms.date: 01/14/19
ms.custom: azcat-ai
ms.openlocfilehash: 800defeb851f5a31dc730038c3699e1a3d54b923
ms.sourcegitcommit: d5ea427c25f9f7799cc859b99f328739ca2d8c1c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54307784"
---
# <a name="distributed-training-of-deep-learning-models-on-azure"></a>Training distribuito di modelli di Deep Learning in Azure

Questa architettura di riferimento mostra come svolgere un training distribuito dei modelli di Deep Learning tra cluster di VM abilitate per GPU. Lo scenario riguarda la classificazione di immagini, ma la soluzione può essere generalizzata per altri scenari di Deep Learning, come la segmentazione e il rilevamento di oggetti.

Un'implementazione di riferimento per questa architettura è disponibile in [GitHub][github].

![Architettura per Deep Learning distribuito][0]

**Scenario**: la classificazione di immagini è una tecnica ampiamente diffusa nel campo della visione artificiale, spesso applicata tramite training di una rete neurale convoluzionale (CNN, Convolutional Neural Network). Per modelli particolarmente grandi con set di dati di grandi dimensioni, il processo di training può richiedere diverse settimane o anche mesi con una singola GPU. In alcune situazioni i modelli sono talmente grandi che non è possibile inserire batch di dimensioni ragionevoli nella GPU. Usando il training distribuito in queste situazioni è possibile abbreviare i tempi.

In questo scenario specifico viene eseguito il training di un [modello di CNN ResNet50][resnet] usando [Horovod][horovod] sul [set di dati Imagenet][imagenet] e su dati sintetici. L'implementazione di riferimento mostra come realizzare questa attività con tre framework di Deep Learning tra i più diffusi: TensorFlow, Keras e PyTorch.

Il training di un modello di Deep Learning in modalità distribuita può essere eseguito in vari modi, tra cui con approcci basati su parallelismo dei dati e parallelismo dei modelli e con aggiornamenti sincroni o asincroni. Attualmente lo scenario più comune è il parallelismo dei dati con aggiornamenti sincroni. Si tratta dell'approccio più facile da implementare e si rivela sufficiente per la maggior parte dei casi d'uso.

Nel training distribuito con parallelismo dei dati e aggiornamenti sincroni, il modello viene replicato tra *n* dispositivi hardware. Un mini-batch di campioni di training viene diviso in *n* micro-batch. Ogni dispositivo esegue i passaggi in avanti e a ritroso per un micro-batch. Quando un dispositivo completa il processo, condivide gli aggiornamenti con gli altri dispositivi. Questi valori vengono usati per calcolare i pesi aggiornati dell'intero mini-batch, che vengono quindi sincronizzati tra i modelli. Questo scenario viene trattato nel repository [GitHub][github].

![Training distribuito con parallelismo dei dati][1]

Questa architettura può essere usata anche per il parallelismo dei modelli e gli aggiornamenti asincroni. Nel training distribuito con parallelismo dei modelli, il modello viene diviso tra *n* dispositivi hardware, ognuno dei quali ne mantiene una parte. Nell'implementazione più semplice, ogni dispositivo può mantenere un livello della rete e le informazioni vengono passate tra i dispositivi durante il passaggio in avanti e a ritroso. Il training delle reti neurali più grandi può essere eseguito in questo modo, ma a discapito delle prestazioni, perché i dispositivi rimangono costantemente in attesa che gli altri completino il passaggio in avanti o a ritroso. Con alcune tecniche avanzate è possibile provare ad alleviare parzialmente questo problema usando gradienti sintetici.

I passaggi del training sono i seguenti:

1. Creare script che verranno eseguiti nel cluster per il training del modello, quindi trasferirli nella risorsa di archiviazione file.

1. Scrivere i dati nell'archiviazione BLOB.

1. Creare un file server di Batch per intelligenza artificiale e scaricarvi i file dall'archiviazione BLOB.

1. Creare i contenitori Docker per ogni framework di Deep Learning e trasferirli in un registro contenitori (hub Docker).

1. Creare un pool di Batch per intelligenza artificiale che monta anche il relativo file server.

1. Inviare i processi. Ogni processo esegue il pull nell'immagine Docker e negli script appropriati.

1. Una volta completato il processo, scrivere tutti i risultati nell'archiviazione file.

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

**[Azure Batch per intelligenza artificiale][batch-ai]**, che ricopre un ruolo centrale in questa architettura, ridimensionando le risorse secondo necessità. Batch per intelligenza artificiale è un servizio che agevola il provisioning e la gestione di cluster di VM, la pianificazione dei processi, la raccolta dei risultati, la scalabilità delle risorse, la gestione degli errori e la creazione di risorse di archiviazione appropriate. Supporta VM abilitate per GPU per i carichi di lavoro di Deep Learning. Per Batch per intelligenza artificiale sono disponibili un SKD Python e un'interfaccia della riga di comando.

> [!NOTE]
> Il servizio Azure Batch per intelligenza artificiale verrà ritirato a marzo 2019 e le relative funzionalità di training e assegnazione dei punteggi su larga scala sono ora disponibili nel [servizio Azure Machine Learning][amls]. Questa architettura di riferimento verrà presto aggiornata per l'uso di Machine Learning, che offre una destinazione di calcolo gestita, l'[ambiente di calcolo di Machine Learning di Azure][aml-compute] per il training, la distribuzione e l'assegnazione di punteggi dei modelli di Machine Learning.

**[Archiviazione BLOB][azure-blob]**, per la gestione temporanea dei dati. Questi dati vengono scaricati in un file server di Batch per intelligenza artificiale durante il training.

**[File di Azure][files]**, per archiviare script, log e risultati finali del training. L'archiviazione file è particolarmente indicata per archiviare log e script, ma non offre le stesse prestazioni di Archiviazione BLOB, quindi non è consigliabile usarla per attività a uso intensivo di dati.

**[File server di Batch per intelligenza artificiale][batch-ai-files]**, una condivisione NFS a nodo singolo usata in questa architettura per archiviare i dati di training. Batch per intelligenza artificiale crea una condivisione NFS e la monta sul cluster. I file server di Batch per intelligenza artificiale rappresentano la scelta consigliata per inviare i dati al cluster con la velocità effettiva necessaria.

**[Hub Docker][docker]**, per archiviare l'immagine Docker usata da Batch per intelligenza artificiale per eseguire il training. L'hub Docker è stato scelto per questa architettura perché è facile da usare ed è il repository di immagini predefinito per gli utenti di Docker. È possibile usare anche il [Registro contenitori di Azure][acr].

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Azure offre quattro [tipi di VM abilitate per GPU][gpu] indicate per il training dei modelli di Deep Learning. Sono disponibili in una gamma di fasce di prezzo e livelli di velocità, come indicato di seguito:

| **Serie di VM di Azure** | **GPU NVIDIA** |
|---------------------|----------------|
| NC                  | K80            |
| ND                  | P40            |
| NCv2                | P100           |
| NCv3                | V100           |

Per il training è consigliabile adottare la scalabilità verticale prima di quella orizzontale. Provare ad esempio a usare una singola VM V100 prima di un cluster di K80.

Il grafico seguente illustra le differenze di prestazioni dei vari tipi di GPU in base ai [test di benchmarking][benchmark] eseguiti usando TensorFlow e Horovod con Batch per intelligenza artificiale. Il grafico mostra la velocità effettiva di 32 cluster di GPU di vari modelli, con tipi di GPU e versioni di MPI differenti. I modelli sono stati implementati in TensorFlow 1.9

![Risultati della velocità effettiva per i modelli TensorFlow con cluster di GPU][2]

Ogni serie di VM indicata nella tabella precedente include una configurazione con InfiniBand. Usare le configurazioni di InfiniBand quando si esegue il training distribuito per velocizzare la comunicazione tra i nodi. InfiniBand offre anche una maggiore efficienza della scalabilità del training per i framework che possono trarne vantaggio. Per i dettagli, vedere il [confronto dei benchmark][benchmark] di InfiniBand.

Anche se Batch per intelligenza artificiale può montare l'archiviazione BLOB usando l'adattatore [blobfuse][blobfuse], questa scelta non è consigliata per il training distribuito, perché le prestazioni non sono sufficienti per gestire la necessaria velocità effettiva. Spostare invece i dati in un file server di Batch per intelligenza artificiale, come illustrato nel diagramma dell'architettura.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

L'efficienza della scalabilità del training distribuito è sempre inferiore al 100% a causa del sovraccarico di rete. La sincronizzazione dell'intero modello tra dispositivi diventa infatti un collo di bottiglia. Pertanto, il training distribuito è più indicato per i modelli più grandi, per cui non è possibile usare dimensioni ragionevoli dei batch in una singola GPU, oppure per problemi non risolvibili distribuendo il modello in un semplice modo parallelo.

Il training distribuito non è consigliato per l'esecuzione di ricerche di iperparametri. L'efficienza della scalabilità influisce sulle prestazioni e riduce l'efficacia di un approccio distribuito rispetto al training di più configurazioni di modelli separatamente.

Uno dei modi per aumentare l'efficienza della scalabilità consiste nell'aumentare le dimensioni dei batch. In questo caso è tuttavia necessario prestare una particolare attenzione, perché l'aumento delle dimensioni dei batch senza una corrispondente regolazione degli altri parametri può incidere negativamente sulle prestazioni finali del modello.

## <a name="storage-considerations"></a>Considerazioni sulle risorse di archiviazione

Quando si esegue il training dei modelli di Deep Learning, un aspetto spesso sottovalutato riguarda la risorsa in cui vengono archiviati i file. Se la risorsa di archiviazione è troppo lenta per tenere il passo con i requisiti delle GPU, le prestazioni del training possono diminuire.

Batch per intelligenza artificiale supporta molte soluzioni di archiviazione. Questa architettura usa il file server di Batch per intelligenza artificiale, perché offre il miglior compromesso tra facilità d'uso e prestazioni. Per prestazioni ottimali, caricare i dati in locale. Questo approccio può tuttavia rivelarsi poco efficiente, perché tutti i nodi devono scaricare i file da Archiviazione BLOB e con il set di dati ImageNet la procedura può richiedere diverse ore. La soluzione [Archiviazione BLOB di Azure Premium][blob] (anteprima pubblica limitata) è un'altra opzione valida da considerare.

Non montare le risorse di archiviazione BLOB e archiviazione file come archivi dati per il training distribuito. Sono infatti troppo lente e pregiudicheranno le prestazioni del training.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

### <a name="restrict-access-to-azure-blob-storage"></a>Limitare l'accesso ad Archiviazione BLOB di Azure

Questa architettura usa [chiavi dell'account di archiviazione][security-guide] per l'accesso all'archiviazione BLOB. Per livelli maggiori di controllo e protezione, provare a usare una firma di accesso condiviso (SAS), che consente l'accesso limitato agli oggetti archiviati nella risorsa, senza la necessità di codificare le chiavi dell'account o salvarle in testo non crittografato. L'uso di una firma di accesso condiviso assicura anche una governance appropriata per l'account di archiviazione e che l'accesso venga concesso solo alle persone previste.

Per gli scenari con più dati sensibili, assicurarsi che tutte le chiavi di archiviazione siano protette, poiché concedono un accesso completo a tutti i dati di input e output dal carico di lavoro.

### <a name="encrypt-data-at-rest-and-in-motion"></a>Crittografare i dati inattivi e in movimento

Negli scenari in cui si usano dati sensibili, crittografare i dati inattivi, ossia quelli presenti nella risorsa di archiviazione. Ogni volta che i dati vengono spostati da una posizione a quella successiva, usare SSL per proteggerne il trasferimento. Per altre informazioni, vedere la [Guida alla sicurezza di Archiviazione di Azure][security-guide].

### <a name="secure-data-in-a-virtual-network"></a>Proteggere i dati in una rete virtuale

Per le distribuzioni di produzione, è consigliabile distribuire il cluster di Batch per intelligenza artificiale in una subnet di una rete virtuale specificata. In questo modo i nodi di calcolo nel cluster possono comunicare in modo sicuro con altre macchine virtuali o con una rete locale. È anche possibile usare [endpoint di servizio][endpoints] con archiviazione BLOB per concedere l'accesso da una rete virtuale oppure usare una condivisione NFS a nodo singolo all'interno della rete virtuale con Batch per intelligenza artificiale.

## <a name="monitoring-considerations"></a>Considerazioni sul monitoraggio

Quando si esegue un processo, è importante monitorare lo stato di avanzamento e assicurarsi che tutto funzioni come previsto. Tuttavia, il monitoraggio in un cluster di nodi attivi può rivelarsi un problema.

I file server di Batch per intelligenza artificiale possono essere gestiti tramite il portale di Azure o con l'[interfaccia della riga di comando di Azure][cli] e l'SDK Python. Per avere un'idea dello stato complessivo del cluster, passare al pannello **Batch per intelligenza artificiale** del portale di Azure e ispezionare lo stato dei nodi del cluster. Se un nodo è inattivo oppure un processo non riesce, i log degli errori vengono salvati nell'archiviazione BLOB e sono accessibili anche dal pannello **Processi** nel portale di Azure.

Per un monitoraggio più completo, è possibile connettere i log ad [Azure Application Insights][ai] o eseguire processi separati per il polling dello stato del cluster di Batch per intelligenza artificiale e relativi processi.

Batch per intelligenza artificiale registra automaticamente tutti i percorsi StdOut/StdErr per l'account di archiviazione BLOB associato. L'uso di uno strumento di esplorazione dell'archiviazione, come ad esempio [Azure Storage Explorer][storage-explorer], offre un'esperienza molto più semplice per scorrere i file di log.

È anche possibile trasmettere i log per ogni processo. Per i dettagli su questa opzione, vedere i passaggi di sviluppo in [GitHub][github].

## <a name="deployment"></a>Distribuzione

L'implementazione di riferimento per questa architettura è disponibile in [GitHub][github]. Seguire i passaggi descritti per svolgere un training distribuito dei modelli di Deep Learning tra cluster di VM abilitate per GPU.

## <a name="next-steps"></a>Passaggi successivi

L'output di questa architettura è un modello sottoposto a training salvato nell'archiviazione BLOB. È possibile usare questo modello per l'assegnazione di punteggi in tempo reale o in batch. Per altre informazioni, vedere le architetture di riferimento seguenti:

- [Assegnazione di punteggi in tempo reale per modelli Python scikit-learn e di Deep Learning in Azure][real-time-scoring]
- [Punteggio batch in Azure per modelli di apprendimento avanzato][batch-scoring]

[0]: ./_images/distributed_dl_architecture.png
[1]: ./_images/distributed_dl_flow.png
[2]: ./_images/distributed_dl_tests.png
[acr]: /azure/container-registry/container-registry-intro
[ai]: /azure/application-insights/app-insights-overview
[aml-compute]: /azure/machine-learning/service/how-to-set-up-training-targets#amlcompute
[amls]: /azure/machine-learning/service/overview-what-is-azure-ml
[azure-blob]: /azure/storage/blobs/storage-blobs-introduction
[batch-ai]: /azure/batch-ai/overview
[batch-ai-files]: /azure/batch-ai/resource-concepts#file-server
[batch-scoring]: /azure/architecture/reference-architectures/ai/batch-scoring-deep-learning
[benchmark]: https://github.com/msalvaris/BatchAIHorovodBenchmark
[blob]: https://azure.microsoft.com/en-gb/blog/introducing-azure-premium-blob-storage-limited-public-preview/
[blobfuse]: https://github.com/Azure/azure-storage-fuse
[cli]: https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md
[docker]: https://hub.docker.com/
[endpoints]: /azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network
[files]: /azure/storage/files/storage-files-introduction
[github]: https://github.com/Azure/DistributedDeepLearning/
[gpu]: /azure/virtual-machines/windows/sizes-gpu
[horovod]: https://github.com/uber/horovod
[imagenet]: http://www.image-net.org/
[real-time-scoring]: /azure/architecture/reference-architectures/ai/realtime-scoring-python
[resnet]: https://arxiv.org/abs/1512.03385
[security-guide]: /azure/storage/common/storage-security-guide
[storage-explorer]: /azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows
[tutorial]: https://github.com/Azure/DistributedDeepLearning