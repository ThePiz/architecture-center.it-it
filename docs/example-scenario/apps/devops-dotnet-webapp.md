---
title: Progettare una pipeline CI/CD con Azure DevOps
description: Compilare e rilasciare un'app .NET per App Web di Azure usando Azure DevOps.
author: christianreddington
ms.date: 12/06/2018
ms.custom:
- fasttrack
- seodec18
ms.openlocfilehash: 23945493115522d099b6b26922f567653da0367e
ms.sourcegitcommit: 4ba3304eebaa8c493c3e5307bdd9d723cd90b655
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53307283"
---
# <a name="design-a-cicd-pipeline-using-azure-devops"></a>Progettare una pipeline CI/CD con Azure DevOps

Questo scenario fornisce materiale sussidiario di architettura e progettazione per la compilazione di una pipeline di integrazione continua (CI) e distribuzione continua (CD).  In questo esempio, la pipeline CI/CD consente di distribuire un'applicazione web .NET a due livelli nel servizio app di Azure.

La migrazione in processi CI/CD moderni offre numerosi vantaggi per le compilazioni, le distribuzioni, i test e il monitoraggio delle applicazioni. Usando Azure DevOps con altri servizi come il Servizio app, le organizzazioni possono concentrarsi sullo sviluppo delle proprie app invece che sulla gestione dell'infrastruttura di supporto.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Prendere in considerazione i processi di CI/CD e Azure DevOps per:

- Accelerazione dello sviluppo di applicazioni e dei cicli di vita dello sviluppo
- Integrazione di qualità e coerenza in un processo di compilazione e rilascio automatizzato
- Aumento della stabilità e del tempo di attività dell'applicazione

## <a name="architecture"></a>Architettura

![Diagramma dell'architettura dei componenti di Azure coinvolti in uno scenario DevOps con Azure DevOps e Servizio app di Azure][architecture]

Il flusso dei dati nello scenario avviene come segue:

1. Uno sviluppatore modifica il codice sorgente dell'applicazione.
2. Si esegue il commit del codice dell'applicazione con il file web.config nel repository del codice sorgente in Azure Repos.
3. L'integrazione continua attiva la compilazione dell'applicazione e gli unit test tramite Azure Test Plans.
4. La distribuzione continua in Azure Pipelines attiva una distribuzione automatizzata degli artefatti dell'applicazione con *valori di configurazione specifici dell'ambiente*.
5. Gli artefatti vengono distribuiti nel Servizio app di Azure.
6. Azure Application Insights raccoglie e analizza i dati relativi a integrità, prestazioni e utilizzo.
7. Gli sviluppatori monitorano e gestiscono informazioni relative a integrità, prestazioni e utilizzo.
8. Le informazioni di backlog sono usate per classificare le nuove funzionalità e le correzioni di bug in ordine di priorità mediante Azure Boards.

### <a name="components"></a>Componenti

- [Azure DevOps][vsts] è un servizio per gestire il ciclo di vita dello sviluppo end-to-end, dalla pianificazione e dalla gestione dei progetti alla gestione del codice per proseguire con la compilazione e il rilascio.

- [App Web di Azure][web-apps] è un servizio PaaS per l'hosting di applicazioni Web, API REST e back-end per dispositivi mobili. Questo articolo è incentrato su .NET, ma sono supportate diverse altre opzioni di piattaforma di sviluppo.

- [Application Insights][application-insights] è un servizio proprietario estendibile di gestione delle prestazioni applicative (APM, Application Performance Management) per sviluppatori Web in più piattaforme.

### <a name="alternatives"></a>Alternative

Questo articolo è incentrato su Azure DevOps, ma è possibile anche usare [Azure DevOps Server][azure-devops-server] (precedentemente noto come Microsoft Team Foundation Server) come strumento sostitutivo locale. In alternativa, è anche possibile usare un set di tecnologie per una pipeline di sviluppo open source con [Jenkins][jenkins-on-azure].

Per un'infrastruttura distribuita come codice, nel progetto Azure DevOps sono stati usati [modelli di Resource Manager][arm-templates], ma si possono prendere in considerazione altre tecnologie di gestione come [Terraform][terraform] o [Chef][chef]. Se si preferisce una distribuzione basata su IaaS (infrastruttura distribuita come servizio) ed è necessaria la gestione della configurazione, si può prendere in considerazione [Automation State Configuration per Azure][desired-state-configuration], [Ansible][ansible] o [Chef][chef].

È possibile prendere in considerazione queste alternative all'hosting in App Web di Azure:

- [Macchine virtuali di Azure][compare-vm-hosting] gestisce i carichi di lavoro che richiedono un livello elevato di controllo o dipendono da servizi e componenti del sistema operativo non consentiti con App Web (ad esempio, COM o GAC di Windows).

- [Service Fabric][service-fabric] è un'opzione valida se l'architettura dei carichi di lavoro è incentrata su componenti distribuiti per cui sono utili la distribuzione e l'esecuzione in un cluster con un livello elevato di controllo. È possibile usare Service Fabric anche per l'hosting di contenitori.

- [Funzioni di Azure][azure-functions] offre un approccio serverless efficace se l'architettura dei carichi di lavoro è incentrata su componenti distribuiti specifici che richiedono dipendenze minime, nei casi in cui l'esecuzione dei singoli componenti è prevista solo su richiesta (non in modo continuo) e non è necessaria l'orchestrazione dei componenti.

Questo [albero delle decisioni per i servizi di calcolo di Azure](/azure/architecture/guide/technology-choices/compute-decision-tree) può essere utile quando si sceglie il percorso appropriato per una migrazione.

## <a name="management-and-security-considerations"></a>Considerazioni relative alla gestione e alla sicurezza

- Valutare la possibilità di sfruttare una delle [attività di tokenizzazione][vsts-tokenization] disponibili nel marketplace di VSTS.

- Con le attività di [Azure Key Vault][download-keyvault-secrets] è possibile scaricare segreti da un'istanza di Azure Key Vault alla propria versione. Sarà quindi possibile usare tali segreti come variabili nella definizione di versione, evitando di archiviarli nel controllo del codice sorgente.

- Usare [variabili di versione][vsts-release-variables] nelle definizioni di versione per introdurre modifiche alla configurazione per gli ambienti. Le variabili di versione possono avere come abito un'intera versione o un determinato ambiente. Quando si usano le variabili per le informazioni dei segreti, assicurarsi di selezionare l'icona a forma di lucchetto.

- Sarebbe opportuno usare [attività di controllo di distribuzione][vsts-deployment-gates] nella pipeline di versione. In questo modo è possibile sfruttare i dati di monitoraggio in associazione a sistemi esterni, ad esempio sistemi di gestione degli eventi imprevisti o altri sistemi personalizzati, per determinare se una versione deve essere alzata di livello.

- Quando è necessario l'intervento manuale in una pipeline di versione, usare la funzionalità delle [approvazioni][vsts-approvals].

- Valutare la possibilità di usare [Application Insights][application-insights] e altri strumenti di monitoraggio il prima possibile nella pipeline di versione. Molte organizzazioni iniziano il monitoraggio solo nel loro ambiente di produzione. Monitorando gli altri ambienti, è possibile identificare i bug in fasi precedenti del processo di sviluppo ed evitare problemi nel proprio ambiente di produzione.

## <a name="deploy-the-scenario"></a>Distribuire lo scenario

### <a name="prerequisites"></a>Prerequisiti

- È necessario un account Azure esistente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-free-account] prima di iniziare.

- È necessario iscriversi per ottenere un'organizzazione di Azure DevOps. Per ulteriori informazioni, consultare [Avvio rapido: Creare l'organizzazione][vsts-account-create].

### <a name="walk-through"></a>Procedura dettagliata

Il [progetto di Azure DevOps](/azure/devops-project/azure-devops-project-github) distribuirà un piano di servizio app, il servizio app e una risorsa di Application Insights e configurerà il progetto di Azure DevOps.

Una volta implementato il progetto di Azure DevOps e completata la compilazione, esaminare le modifiche al codice, gli elementi di lavoro e i risultati dei test associati. Si rileverà che non vengono visualizzati risultati dei test, perché il codice non contiene test da eseguire.

Il progetto crea una pipeline di versione e un trigger di distribuzione continua, distribuendo l'applicazione nell'ambiente di sviluppo. In un processo di distribuzione continua, i rilasci possono estendersi a più ambienti. Un rilascio può riguardare l'infrastruttura (con tecniche come l'infrastruttura distribuita come codice) nonché distribuire i pacchetti dell'applicazione con tutte le attività di post-configurazione.

## <a name="pricing"></a>Prezzi

I costi di Azure DevOps dipendono dal numero di utenti dell'organizzazione che devono accedere, nonché da fattori come il numero di processi di compilazione/rilascio simultanei necessari e il numero di utenti di test. Per altre informazioni, vedere [Prezzi per Azure DevOps][vsts-pricing-page].

Questo [calcolatore dei prezzi][vsts-pricing-calculator] offre una stima per l'esecuzione di Azure DevOps con 20 utenti.

Azure DevOps viene fatturato su base mensile per ogni singolo utente. Addebiti aggiuntivi possono essere previsti a seconda delle pipeline simultanee necessarie e di eventuali licenze utente Basic o utenti di test aggiuntivi.

## <a name="related-resources"></a>Risorse correlate

Rivedere le risorse seguenti per altre informazioni relative ai processi di CI/CD e Azure DevOps:

- [Informazioni su DevOps][devops-whatis]
- [DevOps at Microsoft - How we work with Azure DevOps][devops-microsoft] (DevOps in Microsoft - Come viene usato Azure DevOps)
- [Esercitazioni dettagliate: DevOps con Azure DevOps][devops-with-vsts]
- [Elenco di controllo DevOps][devops-checklist]
- [Creare una pipeline di CI/CD per .NET con il progetto DevOps di Azure][devops-project-create]

<!-- links -->

[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[azure-free-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[architecture]: ./media/architecture-devops-dotnet-webapp.svg
[chef]: /azure/chef/
[design-patterns-availability]: /azure/architecture/patterns/category/availability
[design-patterns-resiliency]: /azure/architecture/patterns/category/resiliency
[design-patterns-scalability]: /azure/architecture/patterns/category/performance-scalability
[design-patterns-security]: /azure/architecture/patterns/category/security
[desired-state-configuration]: /azure/automation/automation-dsc-overview
[devops-microsoft]: /azure/devops/devops-at-microsoft/
[devops-with-vsts]: https://almvm.azurewebsites.net/labs/vsts/
[devops-checklist]: /azure/architecture/checklist/dev-ops
[application-insights]: https://azure.microsoft.com/services/application-insights/
[cloud-based-load-testing]: https://visualstudio.microsoft.com/team-services/cloud-load-testing/
[cloud-based-load-testing-on-premises]: /vsts/test/load-test/clt-with-private-machines?view=vsts
[jenkins-on-azure]: /azure/jenkins/
[devops-whatis]: /azure/devops/what-is-devops
[download-keyvault-secrets]: /vsts/pipelines/tasks/deploy/azure-key-vault?view=vsts
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency-app-service]: /azure/architecture/checklist/resiliency-per-service#app-service
[vsts]: /vsts/?view=vsts#pivot=services
[continuous-integration]: /azure/devops/what-is-continuous-integration
[continuous-delivery]: /azure/devops/what-is-continuous-delivery
[web-apps]: /azure/app-service/app-service-web-overview
[vsts-account-create]: /azure/devops/organizations/accounts/create-organization-msa-or-work-student?view=vsts
[vsts-approvals]: /vsts/pipelines/release/approvals/approvals?view=vsts
[devops-project]: https://portal.azure.com/?feature.customportal=false#create/Microsoft.AzureProject
[vsts-deployment-gates]: /vsts/pipelines/release/approvals/gates?view=vsts
[vsts-pricing-calculator]: https://azure.com/e/498aa024454445a8a352e75724f900b1
[vsts-pricing-page]: https://azure.microsoft.com/pricing/details/visual-studio-team-services/
[vsts-release-variables]: /vsts/pipelines/release/variables?view=vsts&tabs=batch
[vsts-tokenization]: https://marketplace.visualstudio.com/search?term=token&target=VSTS&category=All%20categories&sortBy=Relevance
[azure-key-vault]: /azure/key-vault/key-vault-overview
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[azure-devops-server]: https://visualstudio.microsoft.com/tfs/
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[service-fabric]: /azure/service-fabric/
[azure-functions]: /azure/azure-functions/
[azure-containers]: https://azure.microsoft.com/overview/containers/
[compare-vm-hosting]: /azure/app-service/choose-web-site-cloud-service-vm
[app-insights-cd-monitoring]: /azure/application-insights/app-insights-vsts-continuous-monitoring
[azure-region-pair-bcdr]: /azure/best-practices-availability-paired-regions
[devops-project-create]: /azure/devops-project/azure-devops-project-aspnet-core
[terraform]: /azure/terraform/
