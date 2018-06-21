---
title: Indicazioni sulla scalabilità automatica
description: Indicazioni su come implementare la scalabilità automatica per allocare in modo dinamico le risorse richieste da un'applicazione.
author: dragon119
ms.date: 05/17/2017
pnp.series.title: Best Practices
ms.openlocfilehash: a8489aaabab2b8523fbc9f026f4f435bb6d1ad29
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
ms.locfileid: "29478460"
---
# <a name="autoscaling"></a>Scalabilità automatica
[!INCLUDE [header](../_includes/header.md)]

La scalabilità automatica è il processo di allocazione dinamica delle risorse in base ai requisiti di corrispondenza delle prestazioni. Man mano che aumenta il volume di lavoro, un'applicazione potrebbe richiedere altre risorse per gestire i livelli di prestazioni desiderate e soddisfare i contratti di servizio. Quando la richiesta diminuisce e le risorse aggiuntive non sono più necessarie, è possibile deallocarle per ridurre al minimo i costi.

La scalabilità automatica sfrutta la flessibilità degli ambienti ospitati nel cloud, attenuando contemporaneamente il sovraccarico di gestione. Riduce la necessità che un operatore monitori continuamente le prestazioni di un sistema e prenda decisioni sull'aggiunta o la rimozione di risorse.

È possibile scalare un'applicazione in due modi principali: 

* **Scalabilità verticale**, definita anche come aumento o riduzione delle prestazioni, indica la modifica della capacità di una risorsa. Ad esempio, è possibile spostare un'applicazione su una macchina virtuale di dimensioni più grandi. La scalabilità verticale spesso prevede che il sistema sia temporaneamente non disponibile mentre è in fase di ridistribuzione. Pertanto, è meno comune automatizzare la scalabilità verticale.
* **Scalabilità orizzontale**, definita anche come aumento o riduzione delle istanze, implica l'aggiunta o la rimozione delle istanze di una risorsa. L'applicazione può ancora essere eseguita senza interruzioni durante il provisioning delle nuove risorse. Quando il processo di provisioning viene completato, la soluzione viene distribuita su queste risorse aggiuntive. Se la domanda cala, le risorse aggiuntive possono essere totalmente arrestate e deallocate. 

Numerosi sistemi basati sul cloud, tra cui Microsoft Azure, supportano l'automazione della scalabilità orizzontale. Il resto di questo articolo è incentrato sulla scalabilità orizzontale.

> [!NOTE]
> La scalabilità automatica si applica soprattutto alle risorse di calcolo. Sebbene sia possibile scalare orizzontalmente una coda di database o di messaggio, questo in genere comporta il [partizionamento dei dati][data-partitioning], che di solito non è automatico.
>

## <a name="overview"></a>Panoramica

Una strategia di scalabilità automatica in genere coinvolge i componenti seguenti:

* Strumentazione e monitoraggio dei sistemi a livello di applicazione, servizio e infrastruttura. Questi sistemi acquisiscono la metrica chiave, ad esempio tempi di risposta, lunghezza delle code, utilizzo della CPU e della memoria.
* Logica decisionale che valuta le metriche rispetto alle soglie predefinite o pianifica e decide se applicare la scalabilità.
* Componenti che ridimensionano il sistema.
* Test, monitoraggio e ottimizzazione della strategia di scalabilità automatica in modo che funzioni come previsto.

Azure offre meccanismi di scalabilità automatica incorporati appropriati per scenari comuni. Se un servizio o una tecnologia specifica non dispone di funzionalità predefinite per la scalabilità automatica, o se i requisiti specifici della scalabilità automatica superano le funzionalità disponibili, è possibile considerare un'implementazione personalizzata. L'implementazione personalizzata raccoglie metriche di sistema e operative, le analizza e quindi ridimensiona le risorse di conseguenza.

## <a name="configure-autoscaling-for-an-azure-solution"></a>Configurare la scalabilità automatica per una soluzione di Azure

Azure offre la scalabilità automatica incorporata per la maggior parte delle opzioni di calcolo.

* **Macchine virtuali** supporta la scalabilità automatica tramite l'uso di [set di scalabilità di macchine virtuali di Microsoft Azure][vm-scale-sets], un modo per gestire un set di macchine virtuali di Azure come gruppo. Vedere [Come usare la scalabilità automatica e set di scalabilità di macchine virtuali di Microsoft Azure][vm-scale-sets-autoscale].

* Anche **Service Fabric** supporta la scalabilità automatica tramite set di scalabilità di macchine virtuali di Microsoft Azure. Ogni tipo di nodo in un cluster di Service Fabric è configurato come un set di scalabilità di macchine virtuali separato. In questo modo, per ogni tipo di nodo è possibile aumentare o ridurre le istanze in modo indipendente. Vedere [Aumentare o ridurre le istanze del cluster di Service Fabric con le regole di scalabilità automatica][service-fabric-autoscale].

* Per **Servizio app di Azure** la scalabilità automatica è incorporata. Le impostazioni di scalabilità automatica si applicano a tutte le app all'interno di un servizio app. Vedere [Scalare il conteggio delle istanze manualmente o automaticamente][app-service-autoscale].

* Per **Servizi cloud di Azure** la scalabilità automatica è incorporata a livello di ruolo. Vedere [Come configurare la scalabilità automatica per un servizio cloud nel portale][cloud-services-autoscale].

Questi opzioni di calcolo usano la [scalabilità automatica di Monitoraggio di Azure][monitoring] per offrire un set comune di funzionalità per la scalabilità automatica.

* **Funzioni di Azure** differisce dalle precedenti opzioni di calcolo, in quanto non è necessario configurare le regole di scalabilità automatica. Al contrario, Funzioni di Azure alloca automaticamente la potenza di calcolo quando viene eseguito il codice, riducendo le istanze in base alle esigenze per gestire il carico. Per altre informazioni vedere [Scegliere il piano di hosting corretto per Funzioni di Azure][functions-scale].

Infine, una soluzione personalizzata di scalabilità automatica può talvolta essere utile. Ad esempio, è possibile usare la diagnostica e le metriche basate su applicazione di Azure, insieme al codice personalizzato per monitorare ed esportare le metriche dell'applicazione. È quindi possibile definire regole personalizzate basate su queste metriche e usare le API REST di Gestione risorse per attivare la scalabilità automatica. Una soluzione personalizzata non è tuttavia facile da implementare e deve essere considerata solo se nessuno dei precedenti approcci può soddisfare i requisiti.

Usare le funzionalità di scalabilità automatica predefinite della piattaforma, nel caso soddisfino i requisiti. In caso contrario, valutare attentamente se le funzionalità di scalabilità più complesse sono davvero necessarie. Gli esempi di requisiti aggiuntivi possono includere una maggiore granularità di controllo, diversi modi per rilevare gli eventi di attivazione per la scalabilità, la scalabilità tra sottoscrizioni e la scalabilità di altri tipi di risorse.

## <a name="use-azure-monitor-autoscale"></a>Usare la scalabilità automatica di Monitoraggio di Azure

La [scalabilità automatica di Monitoraggio di Azure][monitoring] offre un set comune di funzionalità di scalabilità automatica per il set di scalabilità di macchine virtuali di Microsoft Azure, Servizio app di Azure e Servizio cloud di Azure. La scalabilità può essere eseguita su una pianificazione o in base a una metrica di runtime, ad esempio di uso della CPU o della memoria. Esempi:

- Aumentare a 10 istanze nei giorni feriali e diminuire a 4 istanze il sabato e la domenica. 
- Aumentare di un'istanza se l'uso medio della CPU è superiore al 70% e diminuire di un'istanza se l'uso della CPU è inferiore al 50%.
- Aumentare di un'istanza se il numero di messaggi in una coda supera una determinata soglia.

Per un elenco delle metriche predefinite, vedere [Metriche comuni per la scalabilità automatica di Monitoraggio di Azure][autoscale-metrics]. È anche possibile implementare le metriche personalizzate tramite Application Insights. 

È possibile configurare la scalabilità automatica tramite PowerShell, l'interfaccia della riga di comando di Azure, un modello di Azure Resource Manager o il portale di Azure. Per un controllo più dettagliato, usare l'[API REST di Azure Resource Manager](https://msdn.microsoft.com//library/azure/dn790568.aspx). [Azure Monitoring Service Management Library](http://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring) e [Microsoft Insights Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/) (anteprima) sono SDK che consentono di raccogliere metriche da diverse risorse ed eseguire il ridimensionamento automatico usando le API REST. Per le risorse in cui non è disponibile il supporto di Azure Resource Manager o se si usa Servizi cloud di Azure, è possibile usare l'API REST di Gestione dei servizi per la scalabilità automatica. In tutti gli altri casi, usare Azure Resource Manager.

Quando si usa la scalabilità automatica di Azure, considerare i punti seguenti:

* Valutare se è possibile prevedere il carico sull'applicazione con una precisione sufficiente per usare la scalabilità automatica pianificata, aggiungendo e rimuovendo le istanze per soddisfare i picchi previsti della domanda. Se questo non è possibile, usare la scalabilità automatica reattiva basata sulle metriche di runtime per gestire le variazioni della domanda impreviste. In genere, questi approcci possono essere combinati. Creare ad esempio una strategia per aggiungere risorse in base a una pianificazione dei momenti in cui si prevede che l'applicazione sia più occupata. In questo modo è possibile garantire la disponibilità della capacità quando è necessaria, senza il ritardo nell'avvio di nuove istanze. Per ogni regola pianificata, definire le metriche che consentono la scalabilità automatica reattiva durante tale periodo per garantire che l'applicazione sia in grado di gestire picchi imprevisti ma prolungati della domanda.
* Spesso è difficile capire la relazione tra requisiti di capacità e metrica, specialmente al momento della distribuzione iniziale di un'applicazione. Eseguire il provisioning all'inizio di una capacità aggiuntiva limitata e quindi monitorare e ottimizzare le regole di scalabilità automatica per avvicinare la capacità al carico effettivo.
* Configurare le regole di scalabilità automatica e quindi monitorare le prestazioni dell'applicazione nel tempo. Usare i risultati del monitoraggio per regolare il modo in cui il sistema implementa la scalabilità, se necessario. Tuttavia, tenere presente che la scalabilità automatica non è un processo immediato. Richiede tempo per reagire a una metrica, ad esempio un uso medio della CPU superiore o inferiore a una soglia specificata.
* Le regole di scalabilità automatica che usano un meccanismo di rilevamento basato su un attributo di attivazione misurato, ad esempio l'utilizzo della CPU o la lunghezza della coda, usano un valore aggregato nel tempo, invece di valori istantanei, per attivare un'azione di scalabilità automatica. Per impostazione predefinita, l'aggregazione è una media dei valori. Ciò impedisce al sistema di reagire troppo velocemente o di causare un'oscillazione rapida. Fornisce inoltre il tempo per consentire alle nuove istanze avviate automaticamente di stabilizzarsi nella modalità di esecuzione, evitando azioni di scalabilità automatica aggiuntive durante l'avvio delle nuove istanze. Per Servizi cloud di Azure e Macchine virtuali di Azure il periodo predefinito per l'aggregazione è 45 minuti, quindi può essere necessario questo periodo di tempo prima che la metrica attivi la scalabilità automatica in risposta a picchi della domanda. È possibile modificare il periodo di aggregazione usando l'SDK, ma occorre tenere presente che periodi inferiori a 25 minuti possono causare risultati imprevisti. Per altre informazioni, vedere il post di blog [Auto Scaling Cloud Services on CPU Percentage with the Azure Monitoring Services Management Library](http://rickrainey.com/2013/12/15/auto-scaling-cloud-services-on-cpu-percentage-with-the-windows-azure-monitoring-services-management-library/) (Scalabilità automatica di Servizi cloud in base alla percentuale CPU con Azure Monitoring Services Management Library). Per App Web il periodo medio è più breve, consentendo la disponibilità di nuove istanze in circa cinque minuti dopo una modifica della misura di attivazione media.
* Se si configura la scalabilità automatica tramite l'SDK anziché tramite il portale, è possibile specificare una pianificazione più dettagliata durante la quale le regole sono attive. È anche possibile creare proprie metriche e usarle con o senza quelle esistenti nelle regole di scalabilità automatica. È ad esempio possibile usare contatori alternativi, come il numero di richieste al secondo o la disponibilità di memoria media oppure contatori personalizzati che misurano processi aziendali specifici.
* Quando si esegue la scalabilità automatica di Service Fabric, i tipi di nodi nel cluster sono costituiti da set di scalabilità di macchine virtuali nel back-end, pertanto è necessario configurare le regole di scalabilità automatica per ogni tipo di nodo. Considerare il numero di nodi che essere disponibili prima di configurare il ridimensionamento automatico. Il numero minimo di nodi necessari per il tipo di nodo primario è determinato dal livello di affidabilità scelto. Per altre informazioni vedere [Aumentare o ridurre le istanze del cluster di Service Fabric con le regole di scalabilità automatica](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down).
* È possibile usare il portale per il collegamento di risorse come le code e le istanze del database SQL a un'istanza del servizio cloud. In questo modo è possibile accedere più facilmente alle opzioni di configurazione di scalabilità manuale e automatica separate per ognuna delle risorse collegate. Per altre informazioni, vedere [Procedura: Collegare una risorsa a un servizio cloud](/azure/cloud-services/cloud-services-how-to-manage).
* Quando si configurano più criteri e regole, è possibile che si verifichi un conflitto. La scalabilità automatica usa le regole di risoluzione dei conflitti seguenti per garantire che sia sempre disponibile un numero sufficiente di istanze in esecuzione:
  * Le operazioni di aumento delle istanze hanno sempre la priorità su quelle di riduzione delle istanze.
  * Se si verifica un conflitto tra le operazioni di aumento delle istanze, la regola che avvia l'aumento maggiore del numero di istanze ha la precedenza.
  * Se si verifica un conflitto tra le operazioni di riduzione delle istanze, la regola che avvia la riduzione inferiore del numero di istanze ha la precedenza.
* In un ambiente del servizio app, per definire le regole di scalabiità automatica è possibile usare qualsiasi metrica del pool di lavoro o del front-end. Per altre informazioni, vedere [Ridimensionamento automatico e ambiente del servizio app (versione 1)](/azure/app-service/app-service-environment-auto-scale).

## <a name="application-design-considerations"></a>Considerazioni sulla progettazione di applicazioni
La scalabilità automatica non è una soluzione immediata. La semplice aggiunta di risorse a un sistema o l'esecuzione di più istanze di un processo non garantisce il miglioramento delle prestazioni del sistema. Quando si progetta una strategia di scalabilità automatica, tenere presente quanto segue:

* Il sistema deve essere progettato per la scalabilità orizzontale. Evitare di fare ipotesi sulle affinità delle istanze. Non progettare soluzioni che richiedono che il codice sia sempre in esecuzione in un'istanza specifica di un processo. Quando si implementa l'aumento del numero di istanze di un servizio cloud o di sito Web, evitare di presupporre che una serie di richieste provenienti dalla stessa origine venga sempre instradata alla stessa istanza. Per lo stesso motivo, progettare servizi senza stato per evitare che richiedano l'instradamento di una serie di richieste provenienti da un'applicazione sempre alla stessa istanza di un servizio. Quando si progetta un servizio che legge i messaggi da una coda e li elabora, non presupporre quale sarà l'istanza del servizio che gestirà un messaggio specifico. La scalabilità automatica potrebbe avviare istanze aggiuntive di un servizio man mano che aumenta la lunghezza della coda. L'articolo [Competing Consumers Pattern][competing-consumers] (Modello di consumer concorrenti) descrive come gestire questo scenario.
* Se la soluzione implementa un'attività a esecuzione prolungata, progettare questa attività per supportare sia l'aumento che la riduzione delle istanze. Senza la dovuta attenzione, questa operazione potrebbe impedire l'arresto corretto di un'istanza di un processo quando viene ridotto il numero di istanze del sistema o potrebbe verificarsi una perdita di dati se il processo viene terminato in modo forzato. In teoria, è consigliabile effettuare il refactoring di un'attività a esecuzione prolungata e suddividere l'elaborazione in blocchi discreti più piccoli. L'articolo [Pipes and Filters Pattern][pipes-and-filters] (Modello di pipe e filtri) offre un esempio di come è possibile ottenere questo risultato.
* In alternativa, è possibile implementare un meccanismo di checkpoint che registra informazioni sullo stato dell'attività a intervalli regolari e salva lo stato nell'archivio permanente accessibile da qualsiasi istanza del processo che esegue l'attività. In questo modo, se il processo viene arrestato, il lavoro che stava eseguendo può essere ripreso a partire dall'ultimo checkpoint mediante un'altra istanza.
* Se le attività in background vengono eseguite in istanze di calcolo separate, ad esempio nei ruoli di lavoro di un'applicazione ospitata da servizi cloud, potrebbe essere necessario ridimensionare diverse parti dell'applicazione con criteri di scalabilità diversi. Ad esempio, può essere necessario distribuire altre istanze di calcolo dell'interfaccia utente senza aumentare il numero di istanze di calcolo in background o viceversa. Se si offrono diversi livelli di servizio (ad esempio pacchetti di servizi di base e premium), potrebbe essere necessario scalare orizzontalmente le risorse di calcolo per i pacchetti di servizi premium in modo più aggressivo rispetto ai pacchetti di servizi di base al fine di soddisfare i contratti di servizio.
* È possibile usare la lunghezza della coda su cui l'interfaccia utente e le istanze di calcolo in background comunicano come criterio per la strategia di scalabilità automatica. Questo è il migliore indicatore di uno squilibrio o di una differenza tra il carico corrente e la capacità di elaborazione dell'attività in background.
* Se la strategia di scalabilità automatica si basa su contatori che misurano i processi aziendali, ad esempio il numero di ordini per ogni ora o il tempo di esecuzione medio di una transazione complessa, assicurarsi di capire appieno la relazione tra i risultati di questi tipi di contatori e i requisiti di capacità di calcolo effettivi. Potrebbe essere necessario implementare la scalabilità di più di un componente o unità di calcolo in risposta a cambiamenti nei contatori di processi aziendali.  
* Per impedire al sistema di implementare una scalabilità orizzontale eccessiva e per evitare i costi associati all'esecuzione di molte migliaia di istanze, provare a limitare il numero massimo di istanze che possono essere aggiunte automaticamente. La maggior parte dei meccanismi di scalabilità automatica consente di specificare il numero minimo e massimo di istanze per una regola. Prendere in considerazione anche la possibilità di ridurre gradualmente le funzionalità fornite dal sistema, se è stato distribuito il numero massimo di istanze e il sistema è ancora in overload.
* Tenere presente che la scalabilità automatica potrebbe non essere il meccanismo più appropriato per gestire un improvviso aumento del carico di lavoro. Il provisioning e l'avvio di nuove istanze di un servizio o l'aggiunta di risorse a un sistema richiede tempo e il picco della domanda potrebbe essere passato quando queste risorse aggiuntive vengono rese disponibili. In questo scenario potrebbe essere preferibile limitare il servizio. Per altre informazioni, vedere l'articolo [Throttling Pattern][throttling](Modello di limitazione).
* Se invece è necessario avere la capacità per elaborare tutte le richieste quando il volume è soggetto a fluttuazioni rapide e i costi non sono un fattore determinante, è consigliabile usare una strategia di scalabilità automatica aggressiva che avvii istanze aggiuntive più rapidamente. È anche possibile usare criteri pianificati che avviano un numero sufficiente di istanze per soddisfare il carico massimo prima che il carico sia previsto.
* Il meccanismo di scalabilità automatica deve monitorare il processo e registrare i dettagli di ogni evento di scalabilità automatica, come la causa dell'attivazione, quali risorse sono state aggiunte o rimosse e quando. Se si crea un meccanismo di scalabilità automatica personalizzato, assicurarsi che incorpori questa funzionalità. Analizzare le informazioni per misurare l'efficacia della strategia di scalabilità automatica e ottimizzarla, se necessario. L'ottimizzazione può essere applicata sia a breve termine, man mano che i modelli di utilizzo diventano più evidenti, sia a lungo termine parallelamente all'espansione dell'azienda o all'evolversi dei requisiti dell'applicazione. Se un'applicazione raggiunge il limite superiore definito per la scalabilità automatica, il meccanismo potrebbe anche avvisare un operatore che a sua volta potrà avviare manualmente risorse aggiuntive, se necessario. Si noti che in queste circostanze l'operatore può anche essere responsabile della rimozione manuale di queste risorse con la riduzione del carico di lavoro.

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Anche i modelli e le indicazioni seguenti possono risultare pertinenti per il proprio scenario quando si implementa la scalabilità automatica:

* [Modello di limitazione][throttling]. Questo modello descrive come un'applicazione può continuare a funzionare e soddisfare i contratti di servizio quando un aumento della domanda genera un carico eccessivo sulle risorse. È possibile usare la limitazione con la scalabilità automatica per evitare che un sistema venga sovraccaricato durante l'aumento delle istanze del sistema.
* [Modello di consumer concorrenti][competing-consumers]. Questo modello descrive come implementare un pool di istanze del servizio in grado di gestire i messaggi da un'istanza dell'applicazione. La scalabilità automatica può essere usata per avviare e arrestare le istanze del servizio per soddisfare il carico di lavoro previsto. Questo modello consente a un sistema di elaborare più messaggi contemporaneamente per ottimizzare la velocità effettiva, per migliorare la scalabilità e la disponibilità e per bilanciare il carico di lavoro.
* [Monitoraggio e diagnostica](./monitoring.md). La strumentazione e la telemetria sono fondamentali per raccogliere le informazioni che consentono di gestire il processo di scalabilità automatica.


<!-- links -->

[monitoring]: /azure/monitoring-and-diagnostics/monitoring-overview-autoscale
[app-service-autoscale]: /azure/monitoring-and-diagnostics/insights-how-to-scale?toc=%2fazure%2fapp-service-web%2ftoc.json#scaling-based-on-a-pre-set-metric
[app-service-plan]: /azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
[autoscale-metrics]: /azure/monitoring-and-diagnostics/insights-autoscale-common-metrics
[cloud-services-autoscale]: /azure/cloud-services/cloud-services-how-to-scale-portal
[competing-consumers]: ../patterns/competing-consumers.md
[data-partitioning]: ./data-partitioning.md
[functions-scale]: /azure/azure-functions/functions-scale
[link-resource-to-cloud-service]: /azure/cloud-services/cloud-services-how-to-manage#how-to-link-a-resource-to-a-cloud-service
[pipes-and-filters]: ../patterns/pipes-and-filters.md
[service-fabric-autoscale]: /azure/service-fabric/service-fabric-cluster-scale-up-down
[throttling]: ../patterns/throttling.md
[vm-scale-sets]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[vm-scale-sets-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
