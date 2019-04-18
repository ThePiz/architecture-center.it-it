# <a name="choosing-a-compute-option-for-microservices"></a>Scegliendo un'opzione di calcolo per i microservizi

Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione. Per un'architettura di microservizi sono particolarmente diffusi due approcci:

- Un agente di orchestrazione dei servizi che gestisce i servizi in esecuzione in nodi dedicati (VM).
- Un'architettura senza server che usa le funzioni come servizio (FaaS).

Anche se queste non sono le uniche opzioni disponibili, si tratta di approcci comprovati alla creazione di microservizi. Un'applicazione può includere entrambi gli approcci.

## <a name="service-orchestrators"></a>Agenti di orchestrazione dei servizi

Un agente di orchestrazione gestisce attività correlate alla distribuzione e alla gestione di un set di servizi. Queste attività includono il posizionamento dei servizi nei nodi, il monitoraggio dell'integrità dei servizi, il riavvio dei servizi non integri, il bilanciamento del carico del traffico di rete tra istanze dei servizi, l'individuazione dei servizi, il ridimensionamento del numero di istanze di un servizio e l'applicazione degli aggiornamenti della configurazione. Gli agenti di orchestrazione più diffusi includono Kubernetes, Service Fabric, DC/OS, e Docker Swarm.

Nella piattaforma Azure prendere in considerazione le opzioni seguenti:

- Il [servizio Azure Kubernetes](/azure/aks/) è un ambiente Kubernetes gestito. Il servizio Azure Container effettua il provisioning di Kubernetes ed espone gli endpoint delle API Kubernetes, ma ospita e gestisce il pannello di controllo di Kubernetes, eseguendo aggiornamenti automatici, applicazione di patch automatica, ridimensionamento automatico e altre attività di gestione. Il servizio Azure Container può essere considerato "API Kubernetes come servizio".

- [Service Fabric](/azure/service-fabric/) è una piattaforma di sistemi distribuiti per la creazione di pacchetti, la distribuzione e la gestione di microservizi. I microservizi possono essere distribuiti in Service Fabric come contenitori, file binari eseguibili oppure come [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction). Con il modello di programmazione di Reliable Services, i servizi possono usare direttamente le API di programmazione di Service Fabric per eseguire query sul sistema, creare report di integrità, ricevere notifiche relative a modifiche di configurazione e codice e individuare altri servizi. Un fattore essenziale per la differenziazione di Service Fabric è la creazione di servizi con stato tramite [Reliable Collections](/azure/service-fabric/service-fabric-reliable-services-reliable-collections).

- Altre opzioni, ad esempio Mesosphere DC/OS e Docker Enterprise Edition è possono eseguire in un ambiente IaaS in Azure. È possibile trovare i modelli di distribuzione sul [Azure Marketplace](https://azuremarketplace.microsoft.com).

## <a name="containers"></a>Contenitori

Talvolta si parla di contenitori e microservizi come se fossero sinonimi. Anche se ciò non corrisponde al vero &mdash; non sono necessari contenitori per creare microservizi&mdash; i contenitori offrono effettivamente alcuni vantaggi particolarmente significativi per i microservizi, tra cui:

- **Portabilità**. Un'immagine del contenitore è un pacchetto autonomo che viene eseguito senza dover installare librerie o altre dipendenze. Ciò ne semplifica la distribuzione. I contenitori possono essere avviati e arrestati rapidamente, quindi è possibile creare nuove istanze per gestire un carico maggiore o eseguire il ripristino dopo un errore di nodo.

- **Densità**. I contenitori sono leggeri rispetto all'esecuzione di una macchina virtuale, perché condividono risorse del sistema operativo. È quindi possibile riunire più contenitori in un singolo nodo, operazione particolarmente utile quando l'applicazione è costituita da molti servizi di piccole dimensioni.

- **Isolamento delle risorse**. È possibile limitare la quantità di memoria e CPU disponibile per un contenitore, assicurando così che un processo con eccessivo tempo di esecuzione non esaurisca le risorse dell'host. Per altre informazioni, vedere [Modello A scomparti](../../patterns/bulkhead.md).

## <a name="serverless-functions-as-a-service"></a>Senza server (funzioni come servizio)

Con un'architettura [serverless](https://azure.microsoft.com/solutions/serverless/) non si gestiscono le macchine virtuali o l'infrastruttura di rete virtuale, ma si distribuisce il codice e il servizio di hosting gestisce l'inserimento di tale codice in una VM e la relativa esecuzione. Questo approccio tende a preferire piccole funzioni granulari coordinate tramite trigger basati su eventi. Ad esempio, un messaggio inserito in una coda può attivare una funzione che legge la coda ed elabora il messaggio.

[Funzioni di Azure](/azure/azure-functions/) è un servizio di calcolo senza server che supporta diversi trigger di funzione, inclusi le richieste HTTP, le code del Bus di servizio e gli eventi di hub eventi. Per un elenco completo, vedere [funzioni di Azure concetti di trigger e associazioni](/azure/azure-functions/functions-triggers-bindings). È anche consigliabile [griglia di eventi di Azure](/azure/event-grid/), ovvero un servizio di routing dell'evento gestito in Azure.

<!-- markdownlint-disable MD026 -->

## <a name="orchestrator-or-serverless"></a>Agente di orchestrazione o senza server?

<!-- markdownlint-enable MD026 -->

Ecco alcuni fattori da considerare nella scelta tra un approccio con agente di orchestrazione e un approccio senza server.

**Gestibilità**. Un'applicazione senza server è semplice da gestire perché la piattaforma gestisce automaticamente tutte le risorse di calcolo. Anche se l'agente di orchestrazione estrae alcuni aspetti della gestione e configurazione di un cluster, non nasconde completamente le macchine virtuali di sottostanti. Con un agente di orchestrazione è necessario tener conto di aspetti quali il bilanciamento del carico, l'utilizzo della CPU e la rete.

**Flessibilità e controllo**. Un agente di orchestrazione offre un elevato livello di controllo sulla configurazione e la gestione dei servizi e del cluster. Il compromesso è la maggiore complessità. Con un'architettura senza server si rinuncia a parte del controllo perché questi dettagli vengono estratti.

**Portabilità**. Tutti gli agenti di orchestrazione qui elencati (Kubernetes, DC/OS, Docker Swarm e Service Fabric) possono essere eseguiti in locale oppure in più cloud pubblici.

**Integrazione delle applicazioni**. Può essere complicato creare un'applicazione complessa usando un'architettura senza server. In Azure è possibile usare le [app per la logica di Azure](/azure/logic-apps/) per coordinare un set di Funzioni di Azure. Per un esempio di questo approccio, vedere [Creare una funzione che si integra con le app per la logica di Azure](/azure/azure-functions/functions-twitter-email).

**Costo**. Con un agente di orchestrazione si pagano le macchine virtuali in esecuzione nel cluster. Con un'applicazione senza server si pagano solo le risorse di calcolo effettivamente utilizzate. In entrambi i casi è necessario tener conto del costo di servizi aggiuntivi, ad esempio archiviazione, database e servizi di messaggistica.

**Scalabilità**. Funzioni di Azure si ridimensiona automaticamente per soddisfare le richieste, in base al numero di eventi in ingresso. Con un agente di orchestrazione è possibile aumentare il numero di istanze del servizio in esecuzione nel cluster. È anche possibile aumentare il numero di istanze aggiungendo macchine virtuali al cluster.

L'implementazione di riferimento usa principalmente Kubernetes, ma è stato usato Funzioni di Azure per un servizio, ovvero il servizio cronologia di recapito. Funzioni di Azure era un'ottima soluzione per questo particolare servizio, perché si tratta di un carico di lavoro basato su eventi. Usando un trigger di Hub eventi per richiamare la funzione, per il servizio è stata necessaria una quantità di codice minima. Il servizio cronologia di recapito non fa parte del flusso di lavoro principale, quindi la sua esecuzione all'esterno del cluster Kubernetes non influisce sulla latenza end-to-end delle operazioni avviate dall'utente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Comunicazione tra i servizi](./interservice-communication.md)
