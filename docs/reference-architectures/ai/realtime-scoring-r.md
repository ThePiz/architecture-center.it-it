---
title: Assegnazione dei punteggi in tempo reale per i modelli R di Machine Learning
description: Implementare un servizio di stima in tempo reale in R con Machine Learning Server in esecuzione nel servizio Azure Kubernetes.
author: njray
ms.date: 12/12/18
ms.custom: azcat-ai
ms.openlocfilehash: a6069704c48fbc1f1a1e4b5df428011d6b5b883d
ms.sourcegitcommit: 62d2211badd1d6950e8cb819d70c9a4ab1ee01d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318993"
---
# <a name="real-time-scoring-of-r-machine-learning-models"></a>Assegnazione dei punteggi in tempo reale per i modelli R di Machine Learning

Questa architettura di riferimento mostra come implementare un servizio di stima in tempo reale (sincrono) in R con Microsoft Machine Learning Server in esecuzione nel servizio Azure Kubernetes (AKS). Questa architettura generica è progettata per essere adatta a qualsiasi modello predittivo compilato in R che si desidera distribuire come servizio in tempo reale. **[Distribuire questa soluzione][github]**.

## <a name="architecture"></a>Architettura

![Assegnazione dei punteggi in tempo reale per i modelli R di Machine Learning in Azure][0]

Questa architettura di riferimento adotta un approccio basato su contenitori. Un'immagine del Docker viene compilata con R, insieme ai vari artefatti necessari per valutare nuovi dati. Questi includono l'oggetto modello stesso e uno script di assegnazione dei punteggi. Il push dell'immagine viene eseguito in un registro di Docker ospitato in Azure. Successivamente, l'immagine viene distribuita in un cluster Kubernetes, anch'esso in Azure.

L'architettura di questo flusso di lavoro include i seguenti componenti.

- Il **[Registro Azure Container][acr]** è usato per archiviare le immagini per questo flusso di lavoro. I registri creati con il Registro Azure Container possono essere gestiti tramite l'[API Docker Registry V2][docker] e il client standard.

- Il **[servizio Azure Kubernetes][aks]** è usato per ospitare la distribuzione e il servizio. I cluster creati con il servizio Azure Kubernetes possono essere gestiti tramite l'[API Kubernetes][k-api] e il client standard (kubectl).

- **[Microsoft Machine Learning Server][mmls]** è usato per definire l'API REST per il servizio e include l'[operazionalizzazione del modello][operationalization]. Questo processo di server web orientato sui servizi rimane in ascolto in attesa di richieste, che vengono quindi passate ad altri processi in background, i quali eseguono il codice R effettivo per generare i risultati. In questa configurazione, tutti questi processi vengono eseguiti in un singolo nodo, di cui viene eseguito il wrapping in un contenitore. Per informazioni dettagliate sull'uso di questo servizio all'esterno di un ambiente di sviluppo o test, contattare il rappresentante Microsoft.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

I carichi di lavoro di apprendimento automatico tendono a essere processi di calcolo complessi, sia durante il training che nella classificazione di dati nuovi. Come regola generale, bisogna provare a non eseguire più di un processo di assegnazione dei punteggi per ogni core. Microsoft Machine Learning Server consente di definire il numero di processi R in esecuzione in ciascun contenitore. Il valore predefinito è di cinque processi. Quando si crea un modello relativamente semplice, ad esempio una regressione lineare con un numero ridotto di variabili o un albero delle decisioni di piccole dimensioni, è possibile aumentare il numero di processi. Monitorare il carico della CPU nei nodi del cluster per determinare il limite di numero di contenitori adeguato.

Un cluster abilitato per GPU può accelerare alcuni tipi di carichi di lavoro e, in particolare, alcuni modelli di deep learning. Non tutti i carichi di lavoro possono sfruttare i vantaggi della GPU, ma solo quelli che fanno un uso massiccio dell'algebra matriciale. I modelli basati su albero, ad esempio, tra cui le foreste casuali e i modelli di miglioramento, in genere non traggono alcun vantaggio dalle GPU.

Alcuni tipi di modello, come le foreste casuali, sono altamente parallelizzabili sulle CPU. In questi casi, è possibile velocizzare l'assegnazione dei punteggi di una singola richiesta distribuendo il carico di lavoro su più core. Tuttavia, questo riduce la capacità di gestire più richieste di assegnazione dei punteggi data una dimensione del cluster predefinita.

In generale, i modelli R open source archiviano tutti i dati in memoria, pertanto è necessario verificare che i nodi abbiano memoria sufficiente per supportare i processi che si desiderano eseguire simultaneamente. Se si usa Microsoft Machine Learning Server per i modelli, usare librerie in grado di elaborare i dati su disco, anziché leggerli tutti in memoria. Questo consente di ridurre sensibilmente i requisiti di memoria. Indipendentemente dall'uso di Microsoft Machine Learning Server o R open source, il monitoraggio dei nodi assicura che i processi di assegnazione dei punteggi non esauriscano la memoria.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

### <a name="network-encryption"></a>Crittografia di rete

In questa architettura di riferimento è abilitato il protocollo HTTPS per la comunicazione con il cluster, mentre viene usato un certificato di gestione temporanea di [Let's Encrypt][encrypt]. Per scopi di produzione, sostituire il proprio certificato di un'autorità di firma adeguata.

### <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

L'[operazionalizzazione del modello][operationalization] di Microsoft Machine Learning Server richiede l'autenticazione delle richieste di assegnazione dei punteggi. In questa distribuzione, vengono usati un nome utente e una password. In uno scenario aziendale, è possibile abilitare l'autenticazione usando [Azure Active Directory][AAD] oppure creare un front end separato usando [Gestione API di Azure][API].

È necessario installare un certificato token JSON Web (JWT) perché l'operazionalizzazione del modello funzioni correttamente con Microsoft Machine Learning Server nei contenitori. Questa distribuzione usa un certificato fornito da Microsoft. In uno scenario di produzione, è necessario fornire il proprio certificato.

Per il traffico tra il servizio Azure Kubernetes e il Registro Azure Container, provare ad abilitare il [controllo degli accessi in base al ruolo][rbac] (RBAC) per limitare i privilegi di accesso solo a quelli necessari. 

### <a name="separate-storage"></a>Archiviazione separata

Questa architettura di riferimento raggruppa l'applicazione (R) e i dati (oggetto modello e script di assegnazione dei punteggi) in un'unica immagine. In alcuni casi, può risultare vantaggioso separarli. È possibile inserire i dati e il codice del modello nel blob o file di [archiviazione][storage] di Azure e recuperarli durante la fase di inizializzazione del contenitore. In questo caso, assicurarsi che l'account di archiviazione sia configurato in modo da consentire l'accesso solo tramite autenticazione e con protocollo HTTPS.

## <a name="monitoring-and-logging-considerations"></a>Considerazioni relative a monitoraggio e registrazione

Usare la [dashboard di Kubernetes][dashboard] per monitorare lo stato complessivo del cluster del servizio Azure Kubernetes. Per altri dettagli, vedere il pannello di panoramica del cluster nel portale di Azure. Le risorse di [GitHub][github] illustrano anche come visualizzare la dashboard da R.

Anche se la dashboard offre una panoramica dell'integrità complessiva del cluster, è anche importante tenere traccia dello stato dei singoli contenitori. A tale scopo, abilitare [Informazioni dettagliate sul monitoraggio di Azure][monitor] dal pannello di panoramica del cluster nel portale di Azure, oppure consultare [Monitoraggio di Azure per contenitori][monitor-containers] (in anteprima).

## <a name="cost-considerations"></a>Considerazioni sul costo

Microsoft Machine Learning Server viene concesso in licenza in base ai core, compresi tutti i core del cluster che eseguiranno Microsoft Machine Learning Server. Se si fa parte di un'impresa cliente Machine Learning Server o Microsoft SQL Server, contattare il rappresentante Microsoft per i dettagli sui prezzi.

Un'alternativa open source a Microsoft Machine Learning Server è rappresentata da [Plumber][plumber], un pacchetto R che trasforma il codice in un'API REST. Plumber dispone di meno funzionalità rispetto a Microsoft Machine Learning Server. Ad esempio, per impostazione predefinita non include le funzionalità che forniscono l'autenticazione richiesta. Se si usa Plumber, si consiglia di abilitare la [Gestione API di Azure][API] per gestire i dettagli di autenticazione.

Oltre alle licenze, i maggiori costi sono rappresentati dalle risorse di calcolo del cluster Kubernetes. Il cluster deve essere sufficientemente grande per gestire il volume di richiesta previsto nei periodi di picco, ma questo approccio lascia le risorse inattive in altri momenti. Per limitare l'impatto delle risorse inattive, abilitare la [scalabilità automatica orizzontale][autoscaler] per il cluster usando lo strumento kubectl. In alternativa, usare il [ridimensionamento automatico del cluster][cluster-autoscaler] del servizio Azure Kubernetes.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

L'implementazione di riferimento per questa architettura è disponibile in [GitHub][github]. Seguire la procedura indicata per distribuire un semplice modello predittivo come servizio.

<!-- links -->
[AAD]: /azure/active-directory/fundamentals/active-directory-whatis
[API]: /azure/api-management/api-management-key-concepts
[ACR]: /azure/container-registry/container-registry-intro
[AKS]: /azure/aks/intro-kubernetes
[autoscaler]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[cluster-autoscaler]: /azure/aks/autoscaler
[monitor]: /azure/monitoring/monitoring-container-insights-overview
[dashboard]: /azure/aks/kubernetes-dashboard
[docker]: https://docs.docker.com/registry/spec/api/
[encrypt]: https://letsencrypt.org/
[gitHub]: https://github.com/Azure/RealtimeRDeployment
[K-API]: https://kubernetes.io/docs/reference/
[MMLS]: /machine-learning-server/what-is-machine-learning-server
[monitor-containers]: /azure/azure-monitor/insights/container-insights-overview
[operationalization]: /machine-learning-server/what-is-operationalization
[plumber]: https://www.rplumber.io
[RBAC]: /azure/role-based-access-control/overview
[storage]: /azure/storage/common/storage-introduction
[0]: ./_images/realtime-scoring-r.png
