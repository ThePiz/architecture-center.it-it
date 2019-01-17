---
title: Punteggio batch per i modelli di apprendimento avanzato
titleSuffix: Azure Reference Architectures
description: Questa architettura di riferimento mostra come applicare la procedura di neural style transfer a un video tramite Azure Batch per intelligenza artificiale.
author: jiata
ms.date: 10/02/2018
ms.custom: azcat-ai
ms.openlocfilehash: 0396903a39d00a4131df65872a63f4b3fde8dce7
ms.sourcegitcommit: 88a68c7e9b6b772172b7faa4b9fd9c061a9f7e9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53119881"
---
# <a name="batch-scoring-on-azure-for-deep-learning-models"></a>Punteggio batch in Azure per modelli di apprendimento avanzato

Questa architettura di riferimento mostra come applicare la procedura di neural style transfer a un video tramite Azure Batch per intelligenza artificiale. Lo *style transfer* è una tecnica di apprendimento avanzato che ridefinisce un'immagine esistente usando lo stile di un'altra immagine. Questa architettura può essere generalizzata per qualsiasi scenario che usa l'assegnazione del punteggio batch con l'apprendimento avanzato. [**Distribuire questa soluzione**](#deploy-the-solution).

![Diagramma dell'architettura per modelli di deep learning con Azure Batch per intelligenza artificiale](./_images/batch-ai-deep-learning.png)

**Scenario**: Un'organizzazione di supporti dispone di un video di cui desidera modificare l'aspetto, rendendolo simile a un dipinto specifico. L'organizzazione vuole applicare questo stile a tutti i frame del video in modo tempestivo e automatico. Per altre informazioni sugli algoritmi di neural style transfer, vedere il PDF [Image Style Transfer Using Convolutional Neural Networks][image-style-transfer] (Style transfer di immagini tramite reti neurali convoluzionali).

| Immagine stile: | Video di input/contenuto: | Video di output: |
|--------|--------|---------|
| <img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/style_image.jpg" width="300"> | [<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/input_video.mp4 "Video di input") *fare clic per visualizzare il video* | [<img src="https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video_image_0.jpg" width="300" height="300">](https://happypathspublic.blob.core.windows.net/assets/batch_scoring_for_dl/output_video.mp4 "Video di output") *fare clic per visualizzare il video* |

Questa architettura di riferimento è progettata per carichi di lavoro che vengono attivati dalla presenza di nuovi supporti in Archiviazione di Azure. L'elaborazione prevede i passaggi seguenti:

1. Caricare un'immagine di stile selezionata (ad esempio, un dipinto di Van Gogh) e uno script di style transfer in Archiviazione BLOB.
1. Creare un cluster Azure Batch per intelligenza artificiale con scalabilità automatica, pronto per avviare l'esecuzione del lavoro.
1. Suddividere il file video in singoli frame e caricarli in Archiviazione BLOB.
1. Una volta caricati tutti i frame, caricare un file di trigger in Archiviazione BLOB.
1. Questo file attiva un'app per la logica che crea un contenitore in esecuzione in Istanze di Azure Container.
1. Il contenitore esegue uno script che crea i processi di Azure Batch per intelligenza artificiale. Ogni processo applica la procedura di neural style transfer in parallelo tra i nodi del cluster Batch per intelligenza artificiale.
1. Una volta generate, le immagini vengono salvate in Archiviazione BLOB di Azure.
1. Scaricare i frame generati e unire nuovamente le immagini in un video.

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

### <a name="compute"></a>Calcolo

**[Azure Batch per intelligenza artificiale][batch-ai]** viene usato per eseguire l'algoritmo di neural style transfer. Azure Batch per intelligenza artificiale supporta carichi di lavoro di apprendimento avanzato, fornendo ambienti strutturati in contenitori e preconfigurati per framework di apprendimento automatico su macchine virtuali abilitate per GPU. Con Azure Batch per intelligenza artificiale è anche possibile connettere il cluster di elaborazione ad Archiviazione BLOB.

### <a name="storage"></a>Archiviazione

**[Archiviazione BLOB][blob-storage]** viene usata per archiviare tutte le immagini (di input, di stile e di output), così come tutti i log prodotti da Azure Batch per intelligenza artificiale. Archiviazione BLOB si integra con Azure Batch per intelligenza artificiale tramite [blobfuse][blobfuse], un file system virtuale open source supportato da Archiviazione BLOB. Archiviazione BLOB è anche molto conveniente per le prestazioni richieste da questo carico di lavoro.

### <a name="trigger--scheduling"></a>Trigger/pianificazione

**[App per la logica di Azure][logic-apps]** viene usato per attivare il flusso di lavoro. Quando App per la logica rileva che un BLOB è stato aggiunto al contenitore, attiva il processo di Batch per intelligenza artificiale. App per la logica rappresenta una buona base per questa architettura di riferimento perché offre un modo semplice per rilevare le modifiche apportate ad Archiviazione BLOB e fornisce una procedura semplice per la modifica del trigger.

**[Istanze di contenitore di Azure][container-instances]** viene usato per eseguire gli script Python che creano i processi Batch per intelligenza artificiale. Un modo pratico per eseguirli su richiesta consiste nell'eseguire questi script all'interno di un contenitore Docker. Per questa architettura viene usato Istanze di Container, poiché esiste un connettore App per la logica predefinito che consente ad App per la logica di attivare il processo Batch per intelligenza artificiale. Istanze di Container può attivare rapidamente i processi senza stato.

**[DockerHub][dockerhub]** viene usato per archiviare l'immagine Docker che Istanze di contenitore usa per la creazione del processo. DockerHub è stato scelto per questa architettura perché è facile da usare ed è il repository di immagini predefinito per gli utenti di Docker. È possibile usare anche il [Registro contenitori di Azure][container-registry].

### <a name="data-preparation"></a>Preparazione dei dati

Questa architettura di riferimento usa un filmato di repertorio di un orangutan in una struttura ad albero. È possibile scaricare il filmato da [qui][source-video] ed elaborarlo per il flusso di lavoro seguendo questa procedura:

1. Usare [AzCopy][azcopy] per scaricare il video dal BLOB pubblico.
2. Usare [FFmpeg][ffmpeg] per estrarre il file audio, in modo da riunirlo al video di output in un secondo momento.
3. Usare FFmpeg per suddividere il video in singoli frame. I frame verranno elaborati in modo indipendente, in parallelo.
4. Usare AzCopy per copiare i singoli frame nel contenitore BLOB.

In questa fase, il video è in un formato utilizzabile per il neural style transfer.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

### <a name="gpu-vs-cpu"></a>GPU e CPU a confronto

Per carichi di lavoro di apprendimento avanzato, le GPU in genere garantiscono prestazioni nettamente superiori rispetto alle CPU, per cui di norma è necessario un cluster consistente di CPU per ottenere prestazioni analoghe. Anche se in questa architettura è possibile usare soltanto CPU, le GPU offrono comunque un rapporto costi/prestazioni di gran lunga superiore. Si consiglia l'utilizzo della più recente combinazione macchina virtuale-dimensioni-gpu [serie NCv3] di macchine virtuali ottimizzate per la GPU.

Le GPU non sono abilitate per impostazione predefinita in tutte le aree. Assicurarsi di selezionare un'area con GPU abilitate. Le sottoscrizioni prevedono anche una quota predefinita di un numero di core pari a zero per macchine virtuali ottimizzate per la GPU. È possibile aumentare questa quota inoltrando una richiesta di supporto. Assicurarsi che la sottoscrizione disponga di una quota sufficiente per eseguire il carico di lavoro.

### <a name="parallelizing-across-vms-vs-cores"></a>Esecuzione in parallelo tra macchine virtuali e core a confronto

Quando si esegue un processo di style transfer come processo batch, i processi che vengono eseguiti principalmente nelle GPU dovranno essere eseguiti in parallelo tra le macchine virtuali. Sono possibili due approcci: è possibile creare un cluster di dimensioni maggiori tramite macchine virtuali con una GPU singola o creare un cluster più piccolo tramite macchine virtuali con più GPU.

Per questo carico di lavoro, queste due opzioni garantiranno prestazioni analoghe. L'utilizzo di un minor numero di macchine virtuali con più GPU per ognuna può contribuire a ridurre lo spostamento dati. Tuttavia, il volume di dati per processo per questo carico di lavoro non è molto grande, per cui non si osserverà una limitazione eccessiva da parte dell'archiviazione BLOB.

### <a name="images-batch-size-per-batch-ai-job"></a>Dimensioni batch delle immagini per singolo processo Batch per intelligenza artificiale

Un altro parametro che deve essere configurato è rappresentato dal numero di immagini da elaborare per ogni processo Batch per intelligenza artificiale. Da un lato, è necessario assicurarsi che il lavoro venga distribuito su vasta scala tra i nodi e che, se un processo ha esito negativo, non sia necessario ripetere troppe immagini. In questo modo si avranno molti processi Batch per intelligenza artificiale e pertanto un numero ridotto di immagini da elaborare per ogni processo. Dall'altro lato, se per ogni processo viene elaborato un numero eccessivamente ridotto di immagini, il tempo di installazione/avvio diventa estremamente lungo. È possibile impostare un numero di processi uguale al numero massimo di nodi del cluster. In questo modo si otterranno i massimi risultati, supponendo che nessun processo abbia esito negativo, perché i costi di installazione/avvio vengono ridotti al minimo. Tuttavia, se un processo ha esito negativo, potrebbe essere necessario rielaborare un numero elevato di immagini.

### <a name="file-servers"></a>File server

Quando si usa Azure Batch per intelligenza artificiale, è possibile scegliere più opzioni di archiviazione, a seconda della velocità effettiva necessaria per lo scenario. Per carichi di lavoro con requisiti di bassa velocità effettiva, l'uso di Archiviazione BLOB (tramite blobfuse) dovrebbe essere sufficiente. In alternativa, Azure Batch per intelligenza artificiale supporta anche un file server Batch per intelligenza artificiale, un NFS a nodo singolo gestito che può essere montato automaticamente su nodi del cluster per offrire una posizione di archiviazione accessibile a livello centrale per i processi. Nella maggior parte dei casi è necessario un solo file server in un'area di lavoro ed è possibile separare i dati per i processi di training in directory diverse. Se un NFS a nodo singolo non è appropriato per i carichi di lavoro, Azure Batch per intelligenza artificiale supporta altre opzioni di archiviazione, tra cui File di Azure o soluzioni personalizzate, come ad esempio un file system Gluster o Lustre.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

### <a name="restricting-access-to-azure-blob-storage"></a>Limitazione dell'accesso ad Archiviazione BLOB di Azure

In questa architettura di riferimento, Archiviazione BLOB di Azure è il componente di archiviazione principale che deve essere protetto. La distribuzione della baseline illustrata nel repository GitHub usa chiavi dell'account di archiviazione per accedere all'archiviazione BLOB. Per livelli maggiori di controllo e protezione, provare a usare una firma di accesso condiviso (SAS), che consente l'accesso limitato agli oggetti presenti nell'archiviazione, senza la necessità di codificare le chiavi dell'account o salvarle in testo non crittografato. Questo approccio è particolarmente utile in quanto le chiavi dell'account sono visibili in testo non crittografato nell'interfaccia della finestra di progettazione di App per la logica. L'uso di una firma di accesso condiviso consente anche di garantire che l'account di archiviazione disponga di una governance appropriata e che l'accesso sia concesso soltanto agli utenti desiderati.

Per gli scenari con più dati sensibili, assicurarsi che tutte le chiavi di archiviazione siano protette, poiché concedono un accesso completo a tutti i dati di input e output dal carico di lavoro.

### <a name="data-encryption-and-data-movement"></a>Crittografia e spostamento dei dati

Questa architettura di riferimento usa lo style transfer come esempio di processo di punteggio batch. Per altri scenari basati su dati sensibili, i dati contenuti nella risorsa di archiviazione dovranno essere crittografati a riposo. Ogni volta che i dati vengono spostati da una posizione a quella successiva, usare SSL per proteggere il trasferimento dei dati. Per altre informazioni, vedere la [Guida alla sicurezza di Archiviazione di Azure][storage-security].

### <a name="securing-data-in-a-virtual-network"></a>Protezione dei dati in una rete virtuale

Quando si distribuisce un cluster Batch per intelligenza artificiale, è possibile configurare il cluster perché venga eseguito il provisioning all'interno di una subnet di una rete virtuale. In questo modo i nodi di calcolo nel cluster possono comunicare in modo sicuro con altre macchine virtuali o addirittura con una rete locale. È anche possibile usare [endpoint di servizio][service-endpoints] con archiviazione BLOB per concedere l'accesso da una rete virtuale, oppure usare un NFS a nodo singolo all'interno della rete virtuale con Batch per intelligenza artificiale per garantire che i dati siano sempre protetti.

### <a name="protecting-against-malicious-activity"></a>Protezione da attività dannose

Negli scenari in cui sono presenti più utenti, assicurarsi che i dati sensibili siano protetti da attività dannose. Se l'accesso a questa distribuzione è concesso anche ad altri utenti per personalizzare i dati di input, prendere nota delle precauzioni e delle considerazioni seguenti:

- Usare RBAC per limitare l'accesso degli utenti alle sole risorse di cui necessitano.
- Fornire due account di archiviazione separati. Archiviare i dati di input e output nel primo account. Gli utenti esterni potranno avere accesso a questo account. Archiviare script eseguibili e file di log di output nell'altro account. Gli utenti esterni non dovranno avere accesso a questo account. In questo modo gli utenti esterni non potranno modificare alcun file eseguibile (per inserire codici dannosi), né avranno accesso ai file di log, che potrebbero contenere informazioni riservate.
- Gli utenti malintenzionati possono perpetrare attacchi DDoS alla coda processi o inserire in essa messaggi non elaborabili in formato non valido, causando il blocco del sistema o errori di rimozione dalla coda.

## <a name="monitoring-and-logging"></a>Monitoraggio e registrazione

### <a name="monitoring-batch-ai-jobs"></a>Monitoraggio dei processi Batch per intelligenza artificiale

Quando si esegue un processo, è importante monitorare lo stato di avanzamento e assicurarsi che tutto funzioni come previsto. Tuttavia, il monitoraggio in un cluster di nodi attivi può rivelarsi un problema.

Per ottenere un'idea dello stato complessivo del cluster, passare al pannello Batch per intelligenza artificiale del portale di Azure per controllare lo stato dei nodi del cluster. Se un nodo è inattivo oppure un processo ha avuto esito negativo, i log degli errori vengono salvati nell'archiviazione BLOB e sono accessibili anche dal pannello Processi nel portale di Azure.

È possibile arricchire ulteriormente il monitoraggio connettendo i log ad Application Insights o eseguendo processi separati per il polling dello stato del cluster Batch per intelligenza artificiale e relativi processi.

### <a name="logging-in-batch-ai"></a>Registrazione in Batch per intelligenza artificiale

Batch per intelligenza artificiale registra automaticamente tutti i percorsi StdOut/StdEr per l'account di archiviazione BLOB associato. L'uso di uno strumento di esplorazione dell'archiviazione, come ad esempio Storage Explorer, offre un'esperienza molto più semplice per scorrere i file di log.

La procedura di distribuzione per questa architettura di riferimento mostra anche come configurare un sistema di registrazione più semplice, in modo che tutti i log tra i diversi processi siano salvati nella stessa directory nel contenitore BLOB, come illustrato di seguito. Usare questi log per monitorare il tempo necessario per l'elaborazione di ogni processo e immagine. In questo modo si avrà un'idea più precisa su come ottimizzare ulteriormente il processo.

![Screenshot del log di Azure Batch per intelligenza artificiale](./_images/batch-ai-logging.png)

## <a name="cost-considerations"></a>Considerazioni sul costo

Rispetto ai componenti di pianificazione e archiviazione, le risorse di calcolo usate in questa architettura di riferimento sono nettamente superiori in termini di costi. Una delle sfide principali consiste nel riuscire ad abbinare efficacemente in parallelo il lavoro all'interno di un cluster di computer abilitati per GPU.

Le dimensioni del cluster Batch per intelligenza artificiale possono aumentare automaticamente a seconda dei processi nella coda. La scalabilità automatica con Batch per intelligenza artificiale può essere abilitata in due modi. È possibile eseguirla a livello programmatico, tramite configurazione nel file `.env`, nel quadro della [procedura di distribuzione][deployment], oppure modificando la formula di scalabilità direttamente nel portale dopo la creazione del cluster.

Per i lavori che non richiedono un intervento immediato, configurare la formula di scalabilità automatica in modo che lo stato predefinito (minimo) sia rappresentato da un cluster con un numero di nodi pari a zero. Con questa configurazione, il cluster inizia con un numero di nodi pari a zero, per poi aumentare quando rileva processi nella coda. Se il processo di assegnazione punteggio batch si verifica poche volte al giorno, questa impostazione consente di ottenere risparmi significativi sui costi.

La scalabilità automatica potrebbe non essere appropriata per i processi batch eseguiti in tempi troppo ravvicinati. Anche il tempo necessario per avviare e interrompere un cluster comporta dei costi. Pertanto, se un carico di lavoro batch inizia solo pochi minuti dopo il termine del processo precedente, potrebbe essere più conveniente lasciare il cluster attivo tra i processi.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Per distribuire questa architettura di riferimento, seguire la procedura descritta nel [repository GitHub][deployment].

<!-- links -->

[azcopy]: /azure/storage/common/storage-use-azcopy-linux
[batch-ai]: /azure/batch-ai/
[blobfuse]: https://github.com/Azure/azure-storage-fuse
[blob-storage]: /azure/storage/blobs/storage-blobs-introduction
[container-instances]: /azure/container-instances/
[container-registry]: /azure/container-registry/
[deployment]: https://github.com/Azure/batch-scoring-for-dl-models
[dockerhub]: https://hub.docker.com/
[ffmpeg]: https://www.ffmpeg.org/
[image-style-transfer]: https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Gatys_Image_Style_Transfer_CVPR_2016_paper.pdf
[logic-apps]: /azure/logic-apps/
[service-endpoints]: /azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network
[source-video]: https://happypathspublic.blob.core.windows.net/videos/orangutan.mp4
[storage-security]: /azure/storage/common/storage-security-guide
[vm-sizes-gpu]: /azure/virtual-machines/windows/sizes-gpu