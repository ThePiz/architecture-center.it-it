---
title: Pipeline di integrazione continua/distribuzione continua con Azure DevOps
description: Compilare e rilasciare un'app .NET per App Web di Azure usando Azure DevOps.
author: christianreddington
ms.date: 07/11/18
ms.openlocfilehash: 97f16b2d3d9c15bc6f5db6fad4c9d8097243ad3d
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610788"
---
# <a name="cicd-pipeline-with-azure-devops"></a>Pipeline di integrazione continua/distribuzione continua con Azure DevOps

DevOps integra sviluppo, controllo di qualità e operazioni IT. e richiede una cultura unificata e un set consolidato di processi per la distribuzione di software.

Questo scenario di esempio illustra come i team di sviluppo possono usare Azure DevOps per distribuire un'applicazione Web .NET a due livelli nel servizio app di Azure. L'applicazione Web dipende da servizi PaaS (piattaforma distribuita come servizio) di Azure downstream. Questo documento evidenzia anche alcune considerazioni da valutare quando si progetta uno scenario di questo tipo con Azure PaaS.

L'adozione di un approccio moderno allo sviluppo di applicazioni con integrazione continua e distribuzione continua consente di offrire più rapidamente valore agli utenti tramite un servizio di compilazione, test, distribuzione e monitoraggio affidabile. Usando una piattaforma come Azure DevOps con i servizi di Azure, ad esempio il servizio app, le organizzazioni possono restare concentrate sullo sviluppo del proprio scenario invece che sulla gestione dell'infrastruttura di supporto.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Prendere in considerazione DevOps per i casi d'uso seguenti:

* Accelerazione dello sviluppo di applicazioni e dei cicli di vita dello sviluppo
* Integrazione di qualità e coerenza in un processo di compilazione e rilascio automatizzato

## <a name="architecture"></a>Architettura

![Panoramica dell'architettura dei componenti di Azure coinvolti in uno scenario DevOps con Azure DevOps e Servizio app di Azure][architecture]

Questo scenario illustra una pipeline di integrazione continua/distribuzione continua per un'applicazione Web .NET con Azure DevOps. Il flusso dei dati nello scenario avviene come segue:

1. Modificare il codice sorgente dell'applicazione.
2. Eseguire il commit del codice dell'applicazione e del file web.config di App Web.
3. L'integrazione continua attiva la compilazione dell'applicazione e gli unit test.
4. Il trigger di distribuzione continua orchestra la distribuzione degli artefatti dell'applicazione con *valori di configurazione con parametri specifici dell'ambiente*.
5. Viene eseguita la distribuzione nel servizio app di Azure.
6. Azure Application Insights raccoglie e analizza i dati relativi a integrità, prestazioni e utilizzo.
7. Esaminare le informazioni su integrità, prestazioni e utilizzo.

### <a name="components"></a>Componenti

* [Azure DevOps][vsts] è un servizio per gestire il ciclo di vita dello sviluppo end-to-end, dalla pianificazione e dalla gestione dei progetti alla gestione del codice per proseguire con la compilazione e il rilascio.
* [App Web di Azure][web-apps] è un servizio PaaS per l'hosting di applicazioni Web, API REST e back-end per dispositivi mobili. Questo articolo è incentrato su .NET, ma sono supportate diverse altre opzioni di piattaforma di sviluppo.
* [Application Insights][application-insights] è un servizio proprietario estendibile di gestione delle prestazioni applicative (APM, Application Performance Management) per sviluppatori Web in più piattaforme.

### <a name="alternative-devops-tooling-options"></a>Opzioni alternative come strumenti DevOps

Questo articolo è incentrato su Azure DevOps, ma è possibile anche usare [Team Foundation Server][team-foundation-server] come strumento sostitutivo locale. In alternativa, è anche possibile usare un set di tecnologie per una pipeline di sviluppo open source con [Jenkins][jenkins-on-azure].

Per un'infrastruttura distribuita come codice, nel progetto Azure DevOps sono inclusi [modelli di Azure Resource Manager][arm-templates], ma si può prendere in considerazione [Terraform][terraform] o [Chef][chef]. Se si preferisce una distribuzione basata su IaaS (infrastruttura distribuita come servizio) ed è necessaria la gestione della configurazione, si può prendere in considerazione [Automation State Configuration per Azure][desired-state-configuration], [Ansible][ansible] o [Chef][chef].

### <a name="alternatives-to-azure-web-apps"></a>Alternative ad App Web di Azure

È possibile prendere in considerazione queste alternative all'hosting in App Web di Azure:

* [Macchine virtuali di Azure][compare-vm-hosting]: per carichi di lavoro che richiedono un livello elevato di controllo o dipendono da servizi e componenti del sistema operativo non consentiti con App Web (ad esempio, COM o GAC di Windows).
* [Service Fabric][service-fabric]: opzione valida se l'architettura dei carichi di lavoro è incentrata su componenti distribuiti per cui sono utili la distribuzione e l'esecuzione in un cluster con un livello elevato di controllo. È possibile usare Service Fabric anche per l'hosting di contenitori.
* [Funzioni di Azure][azure-functions]: approccio serverless efficace se l'architettura dei carichi di lavoro è incentrata su componenti distribuiti specifici che richiedono dipendenze minime, nei casi in cui l'esecuzione dei singoli componenti è prevista solo su richiesta (non in modo continuo) e non è necessaria l'orchestrazione dei componenti.

Questo [albero delle decisioni](/azure/architecture/guide/technology-choices/compute-decision-tree) può essere utile quando si sceglie il percorso appropriato per una migrazione.

### <a name="devops"></a>DevOps

L'**[integrazione continua][continuous-integration]** garantisce una compilazione stabile, con il commit di piccole modifiche frequenti a una base di codice condivisa da parte di più sviluppatori. Nell'ambito della pipeline di integrazione continua è consigliabile:
* Eseguire con frequenza il commit di modifiche del codice di minore entità. Evitare l'invio in batch di modifiche più complesse o di maggiore entità che potrebbero essere difficili da unire correttamente.
* Eseguire il testing unità dei componenti dell'applicazione con code coverage sufficiente, includendo il test dei percorsi non corretti.
* Assicurarsi che la compilazione venga eseguita sul ramo principale (o trunk) condiviso. Questo ramo dovrà essere stabile ed essere gestito come "pronto per la distribuzione". Le modifiche incomplete o in corso dovranno essere isolate in un ramo con merge di tipo "forward integration" frequenti per evitare successivi conflitti.

La **[distribuzione continua][continuous-delivery]** garantisce non solo una compilazione stabile, ma anche una distribuzione stabile. Realizzare la distribuzione continua risulta così un po' più difficile, perché sono necessari una configurazione specifica dell'ambiente e un meccanismo per impostare correttamente tali valori. Di seguito sono elencate altre considerazioni sulla distribuzione continua:
* È necessario un coverage dei test di integrazione sufficiente a verificare che i vari componenti siano configurati e funzionino correttamente end-to-end.
* La distribuzione continua potrebbe anche richiedere la configurazione e la reimpostazione di dati specifici dell'ambiente e la gestione di versioni dello schema del database.
* Il recapito continuo deve anche estendersi ad ambienti di test di carico e test di accettazione utente.
* Per il recapito continuo è utile il monitoraggio continuo, possibilmente in tutti gli ambienti.
* La coerenza e l'affidabilità delle distribuzioni e dei test di integrazione negli ambienti sono facilitate dall'esecuzione di script per la creazione e la configurazione dell'infrastruttura di hosting. Questa operazione è notevolmente più semplice per carichi di lavoro basati sul cloud. Per altre informazioni, vedere [Infrastructure as Code][infra-as-code] (Infrastruttura distribuita come codice).
* Iniziare il recapito continuo il prima possibile nel ciclo di vita del progetto. Più viene rimandato, più difficile diventa incorporarlo.
* A test di integrazione e unit test dovrà essere assegnata la stessa priorità delle funzionalità dell'applicazione.
* Usare pacchetti di distribuzione indipendenti dall'ambiente e gestire la configurazione specifica dell'ambiente nel processo di rilascio.
* Proteggere le informazioni di configurazione riservate usando gli strumenti di gestione del rilascio oppure ricorrendo a un modulo di protezione hardware o ad [Azure Key Vault][azure-key-vault] durante il processo di rilascio. Non archiviare informazioni di configurazione riservate nel controllo del codice sorgente.

**Apprendimento continuo**. Il monitoraggio più efficace di un ambiente di distribuzione continua è offerto dagli strumenti di monitoraggio delle prestazioni applicative, come ad esempio [Application Insights][application-insights]. Un monitoraggio sufficientemente approfondito del carico di lavoro di un'applicazione è essenziale per conoscere i bug o le prestazioni in condizioni di carico. Application Insights può essere integrato in VSTS per supportare il [monitoraggio continuo della pipeline di distribuzione continua][app-insights-cd-monitoring]. In questo modo è possibile abilitare l'avanzamento automatico alla fase successiva senza intervento umano oppure il rollback se viene rilevato un avviso.

## <a name="considerations"></a>Considerazioni

### <a name="availability"></a>Disponibilità

Durante la creazione di un'applicazione cloud, valutare la possibilità di sfruttare i [modelli di progettazione tipici per la disponibilità][design-patterns-availability].

Esaminare le considerazioni sulla disponibilità riportate in relazione all'[architettura di riferimento per applicazioni Web del servizio app][app-service-reference-architecture] appropriata.

Per altri argomenti relativi alla disponibilità, vedere l'[elenco di controllo per la disponibilità][availability] in Centro architetture Azure.

### <a name="scalability"></a>Scalabilità

Durante la creazione di un'applicazione cloud, tenere presenti i [modelli di progettazione tipici per la scalabilità][design-patterns-scalability].

Esaminare le considerazioni sulla scalabilità riportate in relazione all'[architettura di riferimento per applicazioni Web del servizio app][app-service-reference-architecture] appropriata.

Per altri argomenti relativi alla scalabilità, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Sicurezza

Valutare la possibilità di sfruttare i [modelli di progettazione tipici per la sicurezza][design-patterns-security], quando opportuno.

Esaminare le considerazioni sulla sicurezza riportate in relazione all'[architettura di riferimento per applicazioni Web del servizio app][app-service-reference-architecture] appropriata.

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

Valutare la possibilità di implementare i [modelli di progettazione tipici per la resilienza][design-patterns-resiliency], quando opportuno.

In Centro architetture di Azure sono disponibili diverse [procedure consigliate per il servizio app][resiliency-app-service].

Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].

## <a name="deploy-the-scenario"></a>Distribuire lo scenario

### <a name="prerequisites"></a>Prerequisiti

* È necessario un account Azure esistente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-free-account] prima di iniziare.
* È necessario iscriversi per ottenere un'organizzazione di Azure DevOps. Per altre informazioni, vedere [Guida introduttiva: Creare l'organizzazione][vsts-account-create].

### <a name="walk-through"></a>Procedura dettagliata

In questo scenario si userà il progetto di Azure DevOps per creare la pipeline di integrazione continua/distribuzione continua.

Il progetto di Azure DevOps distribuirà un piano di servizio app, il servizio app e una risorsa di Application Insights e configurerà il progetto di Azure DevOps.

Una volta implementato il progetto di Azure DevOps e completata la compilazione, esaminare le modifiche al codice, gli elementi di lavoro e i risultati dei test associati. Si rileverà che non vengono visualizzati risultati dei test, perché il codice non contiene test da eseguire.

Esaminare le definizioni di versione. Si noti che è stata configurata una pipeline di versione con cui l'applicazione viene rilasciata nell'ambiente di sviluppo. Osservare la presenza di un **trigger di distribuzione continua** impostato dall'elemento di compilazione **Drop**, con rilasci automatici nell'ambiente di sviluppo. In un processo di distribuzione continua, i rilasci possono estendersi a più ambienti. Un rilascio può riguardare l'infrastruttura (con tecniche come l'infrastruttura distribuita come codice) nonché distribuire i pacchetti dell'applicazione con tutte le attività di post-configurazione.

## <a name="additional-considerations"></a>Ulteriori considerazioni

* Valutare la possibilità di sfruttare una delle [attività di tokenizzazione][vsts-tokenization] disponibili nel marketplace di VSTS.
* Valutare la possibilità di usare l'attività [Deploy: Azure Key Vault][download-keyvault-secrets] (Distribuzione: Azure Key Vault) di VSTS per scaricare segreti da un'istanza di Azure Key Vault alla propria versione. Sarà quindi possibile usare tali segreti come variabili nella definizione di versione ed evitare quindi di archiviarli nel controllo del codice sorgente.
* Valutare la possibilità di usare [variabili di versione][vsts-release-variables] nelle definizioni di versione per introdurre modifiche alla configurazione per gli ambienti. Le variabili di versione possono avere come abito un'intera versione o un determinato ambiente. Quando si usano le variabili per le informazioni dei segreti, assicurarsi di selezionare l'icona a forma di lucchetto.
* Valutare la possibilità di usare [attività di controllo di distribuzione][vsts-deployment-gates] nella pipeline di versione. In questo modo è possibile sfruttare i dati di monitoraggio in associazione a sistemi esterni, ad esempio sistemi di gestione degli eventi imprevisti o altri sistemi personalizzati, per determinare se una versione deve essere alzata di livello.
* Quando è necessario l'intervento manuale in una pipeline di versione, valutare la possibilità di usare la funzionalità delle [approvazioni][vsts-approvals].
* Valutare la possibilità di usare [Application Insights][application-insights] e altri strumenti di monitoraggio il prima possibile nella pipeline di versione. Molte organizzazioni iniziano il monitoraggio solo nell'ambiente di produzione. Monitorando gli altri ambienti, è possibile identificare i bug in fasi precedenti del processo di sviluppo ed evitare problemi nell'ambiente di produzione.

## <a name="pricing"></a>Prezzi

I costi di Azure DevOps dipendono dal numero di utenti dell'organizzazione che devono accedere, nonché da fattori come il numero di processi di compilazione/rilascio simultanei necessari e il numero di utenti di test. Per altre informazioni, vedere [Prezzi per Azure DevOps][vsts-pricing-page].

* [Azure DevOps][vsts-pricing-calculator] è un servizio che consente di gestire il ciclo di vita dello sviluppo. Il pagamento è per utente su base mensile. Addebiti aggiuntivi possono essere previsti a seconda delle pipeline simultanee necessarie e di eventuali licenze utente Basic o utenti di test aggiuntivi.

## <a name="related-resources"></a>Risorse correlate

* [Informazioni su DevOps][devops-whatis]
* [DevOps at Microsoft - How we work with Azure DevOps][devops-microsoft] (DevOps in Microsoft - Come viene usato Azure DevOps)
* [Step-by-step Tutorials: DevOps with Azure DevOps][devops-with-vsts] (Esercitazioni dettagliate: DevOps con Azure DevOps)
* [Elenco di controllo DevOps][devops-checklist]
* [Creare una pipeline di CI/CD per .NET con il progetto DevOps di Azure][devops-project-create]

<!-- links -->
[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[azure-free-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[architecture]: ./media/architecture-devops-dotnet-webapp.png
[availability]: /azure/architecture/checklist/availability
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
[resiliency]: /azure/architecture/checklist/resiliency
[scalability]: /azure/architecture/checklist/scalability
[vsts]: /vsts/?view=vsts#pivot=services
[continuous-integration]: /azure/devops/what-is-continuous-integration
[continuous-delivery]: /azure/devops/what-is-continuous-delivery
[web-apps]: /azure/app-service/app-service-web-overview
[terraform]: /azure/terraform/
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
[team-foundation-server]: https://visualstudio.microsoft.com/tfs/
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[service-fabric]: /azure/service-fabric/
[azure-functions]: /azure/azure-functions/
[azure-containers]: https://azure.microsoft.com/overview/containers/
[compare-vm-hosting]: /azure/app-service/choose-web-site-cloud-service-vm
[app-insights-cd-monitoring]: /azure/application-insights/app-insights-vsts-continuous-monitoring
[azure-region-pair-bcdr]: /azure/best-practices-availability-paired-regions
[devops-project-create]: /azure/devops-project/azure-devops-project-aspnet-core
[security]: /azure/security/