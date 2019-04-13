---
title: Creazione di una pipeline CI/CD per microservizi in Kubernetes
description: Descrive una pipeline di integrazione continua/recapito Continuo di esempio per la distribuzione di microservizi di Azure Kubernetes Service (AKS).
author: MikeWasson
ms.date: 04/11/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: e7da8a1b4111fb7856b919b40d033833a4e475e0
ms.sourcegitcommit: d58e6b2b891c9c99e951c59f15fce71addcb96b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59533195"
---
<!-- markdownlint-disable MD040 -->

# <a name="building-a-cicd-pipeline-for-microservices-on-kubernetes"></a>Creazione di una pipeline CI/CD per microservizi in Kubernetes

Può essere difficile creare un processo di integrazione continua/distribuzione affidabile per un'architettura di microservizi. I singoli team deve essere in grado di rilasciare i servizi in modo rapido e affidabile, senza interrompere altri team o destabilizzare l'applicazione nel suo complesso.

Questo articolo illustra una pipeline di integrazione continua/recapito Continuo di esempio per la distribuzione di microservizi di Azure Kubernetes Service (AKS). Ogni team e progetto è diversa, in modo che non accettano questo articolo come un set di regole categoriche. In alternativa, è fatta per essere un punto di partenza per la progettazione di un proprio processo di integrazione continua/recapito Continuo.

La pipeline di esempio descritta di seguito è stata creata per un'implementazione di riferimento di microservizi chiamata l'applicazione di recapito tramite Drone, che sono disponibili nel [GitHub][ri]. Viene descritto lo scenario di applicazione [qui](./design/index.md#reference-implementation).

Gli obiettivi della pipeline possono essere riepilogati come segue:

- I team possono creare e distribuire i propri servizi in modo indipendente.
- Modifiche al codice che superano il processo di integrazione continua vengono distribuiti automaticamente in un ambiente di simil-produzione.
- In ogni fase della pipeline vengono applicati i controlli di qualità.
- Una nuova versione di un servizio può essere distribuita side alla versione precedente.

Per altre informazioni, vedere [integrazione continua/recapito Continuo per le architetture di microservizi](./ci-cd.md).

## <a name="assumptions"></a>Presupposti

Ai fini di questo esempio, ecco alcune supposizioni sul team di sviluppo e il codice di base:

- Il repository di codice è un monorepo, con cartelle organizzati per microservizi.
- La strategia di creazione dei rami del team è basata sullo [sviluppo basato su trunk](https://trunkbaseddevelopment.com/).
- Il team utilizza [i rami di rilascio](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases) per gestire i rilasci. Vengono create versioni separate per ogni microservizio.
- Usa il processo di integrazione continua/recapito Continuo [pipeline di Azure](/azure/devops/pipelines/?view=azure-devops) per compilare, testare e distribuire i microservizi in servizio contenitore di AZURE.
- Vengono archiviate le immagini del contenitore per ogni microservizio [registro contenitori di Azure](/azure/container-registry/).
- Il team Usa i grafici Helm per creare il pacchetto ogni microservizio.

Questi presupposti unità molte informazioni dettagliate specifiche della pipeline di integrazione continua/recapito Continuo. Tuttavia, l'approccio di base descritta di seguito essere adattato per altri processi, strumenti e servizi, come Jenkins o Hub Docker.

## <a name="validation-builds"></a>Compilazioni di convalida

Si supponga che uno sviluppatore sta lavorando a un microservizio denominato servizio di recapito. Durante lo sviluppo di una nuova funzionalità, lo sviluppatore archivia il codice in un ramo di funzionalità. Per convenzione, i rami di funzionalità sono denominati `feature/*`.

![Flusso di lavoro CI/CD](./images/aks-cicd-1.png)

Il file di definizione di compilazione include un trigger che filtra in base al nome del ramo e il percorso di origine:

```yaml
trigger:
  batch: true
  branches:
    include:
    - master
    - feature/*
    - topic/*

    exclude:
    - feature/experimental/*
    - topic/experimental/*

  paths:
     include:
     - /src/shipping/delivery/
```

&#11162;Vedere le [file di origine](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml).

Con questo approccio, ogni team può avere una propria pipeline di compilazione. Solo il codice archiviato nel `/src/shipping/delivery` cartella attiva una compilazione al servizio di recapito. Il push dei commit in un ramo che corrisponde al filtro attiva una compilazione CI. A questo punto nel flusso di lavoro, la compilazione CI esegue alcune verifiche minime del codice:

1. Compilare il codice.
1. Eseguire unit test.

L'obiettivo è mantenere brevi e tempi di compilazione in modo che lo sviluppatore può ottenere feedback rapido. La funzionalità sarà pronta per l'unione nel master, lo sviluppatore apre una richiesta pull. In questo modo viene attivata un'altra compilazione CI che esegue alcuni controlli aggiuntivi:

1. Compilare il codice.
1. Eseguire unit test.
1. Compilare l'immagine di contenitore di runtime.
1. Esegui analisi delle vulnerabilità sull'immagine.

![Flusso di lavoro CI/CD](./images/aks-cicd-2.png)

> [!NOTE]
> Nel repository di DevOps di Azure, è possibile definire [criteri](/azure/devops/repos/git/branch-policies) per proteggere i rami. Ad esempio, i criteri potrebbero richiedere una compilazione CI riuscita oltre all'approvazione da un responsabile per poter eseguire il merge nel master.

## <a name="full-cicd-build"></a>Compilazione di CI/CD completa

A un certo punto, il team è pronto per distribuire una nuova versione del servizio di recapito. Il responsabile del rilascio viene creato un ramo dal master con questo modello di denominazione: `release/<microservice name>/<semver>`. Ad esempio: `release/delivery/v1.0.2`.

![Flusso di lavoro CI/CD](./images/aks-cicd-3.png)

La creazione di questo ramo attiva una compilazione CI completa che consente di eseguire tutti i passaggi precedenti più:

1. Eseguire il push dell'immagine del contenitore per registro contenitori di Azure. L'immagine viene contrassegnata con il numero di versione ottenuto dal nome del ramo.
2. Eseguire `helm package` per creare un pacchetto il grafico Helm per il servizio. Il grafico anch'esso contrassegnato con un numero di versione.
3. Eseguire il push del pacchetto di Helm in Registro contenitori.

Supponendo che la compilazione ha esito positivo, attiva un processo di distribuzione (CD) tramite una pipeline di Azure [pipeline di rilascio](/azure/devops/pipelines/release/what-is-release-management). Questa pipeline include i passaggi seguenti:

1. Distribuire il grafico Helm in un ambiente di controllo di qualità.
1. Un responsabile dà l'approvazione prima che il pacchetto venga spostato nell'ambiente di produzione. Vedere [Release deployment control using approvals](/azure/devops/pipelines/release/approvals/approvals) (Controllo della distribuzione della versione tramite approvazioni).
1. Riapplicare tag immagine Docker per lo spazio dei nomi di ambiente di produzione in Registro contenitori di Azure. Ad esempio, se il tag corrente è `myrepo.azurecr.io/delivery:v1.0.2`, il tag di produzione è `myrepo.azurecr.io/prod/delivery:v1.0.2`.
1. Distribuire il grafico Helm nell'ambiente di produzione.

Anche in un monorepo, queste attività possono essere limitate a singoli microservizi, in modo che i team possono distribuire con velocità elevata. Il processo presenta alcuni passaggi manuali: Approvazione delle richieste pull, creazione di rami di rilascio e approvazione delle distribuzioni nel cluster di produzione. Questi passaggi sono manuali dai criteri &mdash; può essere automatizzate se si preferisce l'organizzazione.

## <a name="isolation-of-environments"></a>Isolamento degli ambienti

Si avranno più ambienti in cui si distribuiscono servizi, inclusi gli ambienti: per lo sviluppo, per lo smoke test, per il test di integrazione, per il test di carico e infine per la produzione. Per questi ambienti è necessario un certo livello di isolamento. In Kubernetes, è possibile scegliere tra isolamento fisico e isolamento logico. Per isolamento fisico si intende la distribuzione atta a separare i cluster. L'isolamento logico usa spazi dei nomi e criteri, come descritto in precedenza.

Si consiglia di creare un cluster di produzione dedicato insieme a un cluster separato per gli ambienti di sviluppo/prova. Usare l'isolamento logico per separare gli ambienti all'interno del cluster di sviluppo/prova. I servizi distribuiti nel cluster di sviluppo/prova non avranno accesso agli archivi dati che contengono dati aziendali.

## <a name="build-process"></a>Processo di compilazione

Quando possibile, il processo di compilazione del pacchetto in un contenitore Docker. Che consente di compilare gli artefatti del codice usando Docker, senza la necessità di configurare l'ambiente di compilazione in ogni computer di compilazione. Un processo di compilazione in contenitori semplifica per scalare orizzontalmente la pipeline di integrazione continua tramite l'aggiunta di nuovi agenti di compilazione. Inoltre, quando lo sviluppatore del team può compilare il codice semplicemente eseguendo il contenitore di compilazione.

Con le compilazioni in più fasi in Docker, è possibile definire l'ambiente di compilazione e l'immagine di runtime in un solo Dockerfile. Ad esempio, ecco un Dockerfile che compila un'applicazione ASP.NET Core:

```
FROM microsoft/dotnet:2.2-runtime AS base
WORKDIR /app

FROM microsoft/dotnet:2.2-sdk AS build
WORKDIR /src/Fabrikam.Workflow.Service

COPY Fabrikam.Workflow.Service/Fabrikam.Workflow.Service.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.csproj

COPY Fabrikam.Workflow.Service/. .
RUN dotnet build Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM build AS publish
RUN dotnet publish Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "Fabrikam.Workflow.Service.dll"]
```

&#11162;Vedere le [file di origine](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile).

Questo Dockerfile definisce diverse fasi di compilazione. Si noti che la fase denominata `base` Usa il runtime di ASP.NET Core, durante la fase denominata `build` Usa la versione completa di ASP.NET Core SDK. Il `build` fase viene usata per compilare il progetto ASP.NET Core. Ma viene compilato il contenitore di runtime finale da `base`, che contiene solo il runtime e sono notevolmente inferiori l'immagine SDK completo.

### <a name="building-a-test-runner"></a>Creazione di un test runner

Un'altra buona norma consiste nell'eseguire unit test nel contenitore. Ad esempio, qui è parte di un file Docker che consente di creare un test runner:

```
FROM build AS testrunner
WORKDIR /src/tests

COPY Fabrikam.Workflow.Service.Tests/*.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.Tests.csproj

COPY Fabrikam.Workflow.Service.Tests/. .
ENTRYPOINT ["dotnet", "test", "--logger:trx"]
```

Uno sviluppatore può utilizzare questo file di Docker per eseguire i test in locale:

```bash
docker build . -t delivery-test:1 --target=testrunner
docker run -p 8080:8080 delivery-test:1
```

La pipeline di integrazione continua deve inoltre eseguire i test come parte del passaggio di verifica compilazione.

Si noti che questo file Usa Docker `ENTRYPOINT` comando per eseguire i test, non il Docker `RUN` comando.

- Se si usa il `RUN` comando, i test eseguiti ogni volta che si compila l'immagine. Usando `ENTRYPOINT`, i test sono acconsenti esplicitamente. Vengono eseguiti solo quando la destinazione è in modo esplicito il `testrunner` fase.
- Un test non superato non causi Docker `build` comando esito negativo. In questo modo è possibile distinguere contenitore errori da errori del test di compilazione.
- I risultati dei test può essere salvati in un volume montato.

### <a name="container-best-practices"></a>Le procedure consigliate di contenitore

Ecco alcune altre procedure consigliate da prendere in considerazione per i contenitori:

- Definire convenzioni a livello di organizzazione per i tag dei contenitori, il controllo delle versioni e la denominazione delle risorse distribuite nel cluster (pod, servizi e così via). Questo può facilitare la diagnosi dei problemi di distribuzione.

- Durante il ciclo di sviluppo e di test, il processo CI/CD compilerà molte immagini di contenitori. Solo alcune di queste immagini sono candidati per il rilascio e quindi solo alcuni di tali release candidate verranno alzate nell'ambiente di produzione. Disporre di una strategia di controllo delle versioni ben, in modo da sapere quali immagini sono attualmente distribuite in produzione e possono eseguire il rollback a una versione precedente se necessario.

- Distribuire sempre i tag di versione specifico contenitore, non `latest`.

- Uso [spazi dei nomi](/azure/container-registry/container-registry-best-practices#repository-namespaces) in Registro contenitori di Azure per isolare le immagini approvate per la produzione di immagini che sono ancora stati individuati. Non spostare un'immagine nello spazio dei nomi di ambiente di produzione finché non si è pronti a distribuirla nell'ambiente di produzione. Combinando questa procedura con il versionamento semantico delle immagini di contenitori è possibile ridurre le probabilità di distribuire accidentalmente una versione il cui rilascio non è stato approvato.

- Attenersi al principio del privilegio minimo, contenitori in esecuzione come utente senza privilegi. In Kubernetes, è possibile creare criteri di sicurezza pod che impedisce l'esecuzione come contenitori *radice*. Visualizzare [impediscono che i POD in esecuzione con privilegi Root](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/).

## <a name="helm-charts"></a>Grafici Helm

È consigliabile usare Helm per gestire la compilazione e distribuzione di servizi. Ecco alcune delle funzionalità che consentono di con integrazione continua/recapito Continuo di Helm:

- Un singolo microservizio è spesso definito da più oggetti Kubernetes. Helm consente a questi oggetti essere compresse in un singolo grafico Helm.
- Un grafico può essere distribuito con un singolo comando di Helm, anziché una serie di comandi di kubectl.
- I grafici sono con controllo delle versioni in modo esplicito. Usare Helm per rilasciare una versione, visualizzare le versioni ed eseguire il rollback a una versione precedente. Rilevamento aggiornamenti e revisioni, con controllo delle versioni semantico, nonché la possibilità di eseguire il rollback a una versione precedente.
- I grafici Helm utilizzano modelli per evitare la duplicazione delle informazioni, ad esempio etichette e i selettori, più file.
- Helm è possibile gestire le dipendenze tra i grafici.
- I grafici possono essere archiviati in un repository Helm, ad esempio il registro contenitori di Azure e integrati nella pipeline di compilazione.

Per altre informazioni sull'uso del Registro Container come repository Helm, vedere [Usare il Registro Azure Container come repository Helm per i grafici di applicazione](/azure/container-registry/container-registry-helm-repos).

Un singolo microservizio può comprendere più file di configurazione di Kubernetes. Aggiornamento di un servizio può significare tocca tutti questi file per aggiornare i selettori, etichette e i tag di immagine. Helm considera queste informazioni come un singolo pacchetto chiamato grafico e consente di aggiornare facilmente i file YAML usando le variabili. Helm Usa un linguaggio del modello (basato su modelli di consumo) per consentire la scrittura dei file di configurazione YAML con parametri.

Ad esempio, qui è parte di un file YAML che definisce una distribuzione:

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "package.fullname" . | replace "." "" }}
  labels:
    app.kubernetes.io/name: {{ include "package.name" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}

...

  spec:
      containers:
      - name: &package-container_name fabrikam-package
        image: {{ .Values.dockerregistry }}/{{ .Values.image.repository }}:{{ .Values.image.tag }}
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        env:
        - name: LOG_LEVEL
          value: {{ .Values.log.level }}
```

&#11162;Vedere le [file di origine](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml).

È possibile vedere che il nome della distribuzione, le etichette e contenitore spec tutti i parametri del modello Usa, che vengono forniti in fase di distribuzione. Ad esempio, dalla riga di comando:

```bash
helm install $HELM_CHARTS/package/ \
     --set image.tag=0.1.0 \
     --set image.repository=package \
     --set dockerregistry=$ACR_SERVER \
     --namespace backend \
     --name package-v0.1.0
```

Anche se la pipeline di integrazione continua/recapito Continuo è stato possibile installare un grafico direttamente in Kubernetes, è consigliabile creare un archivio di grafico (file con estensione TGZ) e il push del grafico in un repository Helm, ad esempio registro contenitori di Azure. Per altre informazioni, vedere [le app basate su Docker pacchetto nei grafici di Helm in Azure pipeline](/azure/devops/pipelines/languages/helm?view=azure-devops).

Prendere in considerazione la distribuzione di Helm in un proprio spazio dei nomi e tramite il controllo di accesso basato sui ruoli (RBAC) per limitare quali spazi dei nomi è possibile distribuire. Per altre informazioni, vedere [Role-based Access Control](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control) nella documentazione di Helm.

### <a name="revisions"></a>Revisioni

I grafici Helm hanno sempre un numero di versione, è necessario usare [versionamento semantico](https://semver.org/). Un grafico può avere anche un `appVersion`. Questo campo è facoltativo e non deve necessariamente essere correlata a quella del grafico. Alcuni team potrebbe voler versioni dell'applicazione separatamente dagli aggiornamenti per i grafici. Ma un approccio più semplice consiste nell'usare un numero di versione, pertanto non c'è una relazione 1:1 tra la versione del grafico e versione dell'applicazione. In questo modo, è possibile archiviare un grafico per ogni versione e distribuire con facilità la versione desiderata:

```bash
helm install <package-chart-name> --version <desiredVersion>
```

Un'altra buona norma consiste nel fornire un'annotazione a causa di modifiche nel modello di distribuzione:

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "delivery.fullname" . | replace "." "" }}
  labels:
     ...
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}
```

Ciò consente di visualizzare il campo causa di modifiche per ogni revisione, utilizzando il `kubectl rollout history` comando. Nell'esempio precedente, la causa di modifiche viene fornita come parametro un grafico Helm.

```bash
$ kubectl rollout history deployments/delivery-v010 -n backend
deployment.extensions/delivery-v010
REVISION  CHANGE-CAUSE
1         Initial deployment
```

È anche possibile usare il `helm list` comando per visualizzare la cronologia delle revisioni:

```bash
~$ helm list
NAME            REVISION    UPDATED                     STATUS        CHART            APP VERSION     NAMESPACE
delivery-v0.1.0 1           Sun Apr  7 00:25:30 2019    DEPLOYED      delivery-v0.1.0  v0.1.0          backend
```

> [!TIP]
> Usare il `--history-max` flag durante l'inizializzazione di Helm. Questa impostazione limita il numero delle revisioni Tiller vengono salvati nella cronologia. Tiller archivia la cronologia delle revisioni in configmaps. Se si sta rilasciando spesso gli aggiornamenti, il configmaps può raggiungere dimensioni molto grandi a meno che non si limita la dimensione della cronologia.

## <a name="azure-devops-pipeline"></a>Azure DevOps Pipeline

Nelle pipeline di Azure, le pipeline sono suddivise *compilare le pipeline* e *pipeline di versione*. La pipeline di compilazione viene eseguito il processo di integrazione continua e crea gli artefatti di compilazione. Per un'architettura di microservizi in Kubernetes, questi elementi sono le immagini del contenitore e i grafici Helm che definiscono ogni microservizio. La pipeline di rilascio viene eseguito il processo CD che consente di distribuire un microservizio in un cluster.

Una pipeline di compilazione basato sul flusso di integrazione continua descritto in precedenza in questo articolo, potrebbe essere sono le attività seguenti:

1. Creare il contenitore di test runner.

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        arguments: '--pull --target testrunner'
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        imageName: '$(imageName)-test'
    ```

1. Eseguire i test, richiamando docker, eseguire il contenitore di test runner.

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'run'
        containerName: testrunner
        volumes: '$(System.DefaultWorkingDirectory)/TestResults:/app/tests/TestResults'
        imageName: '$(imageName)-test'
        runInBackground: false
    ```

1. Pubblicare i risultati del test. Visualizzare [integrare compilazione e attività di test](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks).

    ```yaml
    - task: PublishTestResults@2
      inputs:
        testResultsFormat: 'VSTest'
        testResultsFiles: 'TestResults/*.trx'
        searchFolder: '$(System.DefaultWorkingDirectory)'
        publishRunAttachments: true
    ```

1. Creare il contenitore di runtime.

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        includeLatestTag: false
        imageName: '$(imageName)'
    ```

1. Eseguire il push al contenitore per registro contenitori di Azure (o altro registro contenitori).

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'Push an image'
        imageName: '$(imageName)'
        includeSourceTags: false
    ```

1. Creare un pacchetto il grafico Helm.

    ```yaml
    - task: HelmDeploy@0
      inputs:
        command: package
        chartPath: $(chartPath)
        chartVersion: $(Build.SourceBranchName)
        arguments: '--app-version $(Build.SourceBranchName)'
    ```

1. Eseguire il push del pacchetto di Helm in Registro contenitori di Azure (o altri repository Helm).

    ```yaml
    task: AzureCLI@1
      inputs:
        azureSubscription: $(AzureSubscription)
        scriptLocation: inlineScript
        inlineScript: |
        az acr helm push $(System.ArtifactsDirectory)/$(repositoryName)-$(Build.SourceBranchName).tgz --name $(AzureContainerRegistry);
    ```

&#11162;Vedere le [file di origine](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml).

L'output della pipeline di integrazione continua è un'immagine del contenitore di produzione e un grafico Helm aggiornato per il microservizio. A questo punto, la pipeline di rilascio può intervenire. Esegue i passaggi seguenti:

- Configurare ambienti di sviluppo/controllo qualità/staging.
- Attendere che un responsabile approvazione di approvare o rifiutare la distribuzione.
- Riapplicare l'immagine del contenitore per il rilascio di tag
- Eseguire il push al tag di versione nel registro contenitori.
- Aggiornare il grafico Helm nel cluster di produzione.

Per altre informazioni sulla creazione di una pipeline di rilascio, vedere [pipeline di rilascio, le versioni in versione bozza e opzioni per la versione](/azure/devops/pipelines/release/?view=azure-devops).

Il diagramma seguente illustra il processo CI/CD end-to-end descritto in questo articolo:

![Pipeline di recapito Continuo/distribuzione continua](./images/aks-cicd-flow.png)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo si basava su un'implementazione di riferimento che sono disponibili nel [GitHub][ri].

[ri]: https://github.com/mspnp/microservices-reference-implementation