---
title: Eseguire un server Jenkins in Azure
titleSuffix: Azure Reference Architectures
description: Architettura consigliata che illustra come distribuire e gestire un server Jenkins scalabile di livello aziendale in Azure, con la protezione dell'accesso Single Sign-On (SSO).
author: njray
ms.date: 04/30/2018
ms.custom: seodec18
ms.openlocfilehash: 26bf9cadc8db0cd4fcc61023619ca61bb7b87855
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53644156"
---
# <a name="run-a-jenkins-server-on-azure"></a>Eseguire un server Jenkins in Azure

Questa architettura di riferimento illustra come distribuire e gestire un server Jenkins scalabile di livello aziendale in Azure, con la protezione dell'accesso Single Sign-On (SSO). L'architettura usa anche Monitoraggio di Azure per monitorare lo stato del server Jenkins. [**Distribuire questa soluzione**](#deploy-the-solution).

![Server Jenkins eseguito in Azure][0]

*Scaricare un [file di Visio](https://archcenter.blob.core.windows.net/cdn/Jenkins-architecture.vsdx) contenente questo diagramma dell'architettura.*

Questa architettura supporta il ripristino di emergenza con i servizi di Azure, ma non copre scenari di aumento del numero di istanze più avanzati che prevedono più master o disponibilità elevata senza tempi di inattività. Per informazioni generali sui vari componenti di Azure e un'esercitazione dettagliata per la creazione di una pipeline di integrazione continua/distribuzione continua in Azure, vedere [Jenkins in Azure][jenkins-on-azure].

Questo documento è incentrato sulle operazioni di base in Azure necessarie per supportare Jenkins, come l'uso di Archiviazione di Azure per gestire gli elementi di compilazione, gli elementi di sicurezza necessari per l'accesso SSO, gli altri servizi che possono essere integrati e la scalabilità per la pipeline. L'architettura è progettata per essere usata con un repository di controllo del codice sorgente esistente. Uno scenario comune, ad esempio, consiste nell'avviare i processi Jenkins in base a commit GitHub.

## <a name="architecture"></a>Architettura

Questa architettura è costituita dai componenti seguenti:

- **Gruppo di risorse**. Un [gruppo di risorse][rg] viene usato per raggruppare gli asset di Azure in modo che possano essere gestiti in base alla durata, al proprietario e ad altri criteri. Usare gruppi di risorse per distribuire e monitorare gli asset di Azure come gruppo e tenere traccia dei costi per la fatturazione in base al gruppo di risorse. È inoltre possibile eliminare un intero set di risorse, operazione molto utile nelle distribuzioni di test.

- **Server Jenkins**. Viene distribuita una macchina virtuale che eseguirà [Jenkins][azure-market] come server di automazione e fungerà da master Jenkins. Questa architettura di riferimento usa il [modello di soluzione per Jenkins in Azure][solution], installato in una macchina virtuale Linux (Ubuntu 16.04 LTS) in Azure. In Azure Marketplace sono disponibili altre offerte Jenkins.

  > [!NOTE]
  > Nginx viene installato nella VM per fungere da proxy inverso per Jenkins. È possibile configurare Nginx per abilitare SSL per il server Jenkins.
  >

- **Rete virtuale**. Una [rete virtuale][vnet] connette tra loro le risorse di Azure e offre l'isolamento logico. In questa architettura, il server Jenkins viene eseguito in una rete virtuale.

- **Subnet**. Il server Jenkins viene isolato in una [subnet][subnet] per facilitare la gestione e la separazione del traffico di rete senza alcun impatto sulle prestazioni.

- **Gruppi di sicurezza di rete**. Usare [gruppi di sicurezza di rete][nsg] (NSG) per limitare il traffico di rete da Internet alla subnet di una rete virtuale.

- **Dischi gestiti**. Un [disco gestito][managed-disk] è un disco rigido virtuale permanente usato per l'archiviazione delle applicazioni nonché per mantenere lo stato del server Jenkins e offrire il ripristino di emergenza. I dischi dati vengono archiviati in Archiviazione di Azure. Per prestazioni elevate, è consigliabile usare [Archiviazione Premium][premium].

- **Archivio BLOB di Azure**. Il [plug-in Windows Azure Storage][configure-storage] usa l'archivio BLOB di Azure per archiviare gli elementi di compilazione che vengono creati e condivisi con altre compilazioni Jenkins.

- **Azure Active Directory (Azure AD)**. [Azure AD][azure-ad] supporta l'autenticazione degli utenti, consentendo di configurare l'accesso SSO. Le [entità servizio][service-principal] di Azure AD definiscono i criteri e le autorizzazioni per ogni autorizzazione di ruolo nel flusso di lavoro tramite il [controllo degli accessi in base al ruolo][rbac]. Ogni entità servizio è associata a un processo Jenkins.

- **Azure Key Vault**. Per gestire i segreti e le chiavi crittografiche usati, quando necessari, per il provisioning delle risorse di Azure, questa architettura usa [Key Vault][key-vault]. Per un maggiore supporto per l'archiviazione dei segreti associati all'applicazione nella pipeline, vedere anche il plug-in [Azure Credentials][configure-credential] per Jenkins.

- **Servizi di monitoraggio di Azure**. Il servizio [monitora][monitor] la macchina virtuale di Azure che ospita Jenkins. Questa distribuzione monitora l'utilizzo della CPU e lo stato della macchina virtuale e invia avvisi.

## <a name="recommendations"></a>Consigli

I consigli seguenti sono validi per la maggior parte degli scenari. Seguire questi consigli, a meno che non si disponga di un requisito specifico che li escluda.

### <a name="azure-ad"></a>Azure AD

Il tenant di [Azure AD][azure-ad] per la sottoscrizione di Azure viene usato per abilitare l'accesso SSO per gli utenti di Jenkins e configurare le [entità servizio][service-principal] che consentono ai processi Jenkins di accedere alle risorse di Azure.

L'autenticazione e l'autorizzazione con SSO vengono implementate dal plug-in Azure AD installato nel server Jenkins. L'accesso SSO consente di eseguire l'autenticazione usando le credenziali dell'organizzazione di Azure AD per l'accesso al server Jenkins. Durante la configurazione del plug-in Azure AD, è possibile specificare il livello di accesso autorizzato al server Jenkins di un utente.

Per consentire ai processi Jenkins di accedere alle risorse di Azure, un amministratore di Azure AD crea entità servizio. Le entità servizio concedono alle applicazioni (in questo caso ai processi Jenkins) l'[accesso autenticato e autorizzato][ad-sp] alle risorse di Azure.

Il [controllo degli accessi in base al ruolo][rbac] definisce e controlla ulteriormente l'accesso alle risorse di Azure da parte di utenti o entità servizio tramite il rispettivo ruolo assegnato. Sono supportati ruoli sia predefiniti che personalizzati. I ruoli consentono anche di proteggere la pipeline e garantiscono che le responsabilità di un utente o un agente vengano assegnate e autorizzate correttamente. Il controllo degli accessi in base al ruolo può anche essere configurato in modo da limitare l'accesso agli asset di Azure. È ad esempio possibile limitare un utente all'uso degli asset di un determinato gruppo di risorse.

### <a name="storage"></a>Archiviazione

Usare il [plug-in Windows Azure Storage][storage-plugin] per Jenkins, installato da Azure Marketplace, per archiviare elementi di compilazione che potranno essere condivisi con altri test e compilazioni. Affinché questo plug-in possa essere usato dai processi Jenkins, è prima necessario configurare un account di archiviazione di Azure.

### <a name="jenkins-azure-plugins"></a>Plug-in Azure per Jenkins

Il modello di soluzione per Jenkins in Azure installa diversi plug-in Azure. Il team Azure DevOps crea e gestisce il modello di soluzione e i plug-in seguenti, che possono essere usati con le altre offerte Jenkins in Azure Marketplace nonché con qualsiasi master Jenkins configurato in locale:

- Il [plug-in Azure AD][configure-azure-ad] consente al server Jenkins di supportare l'accesso SSO degli utenti in base ad Azure AD.

- Il plug-in [Azure VM Agents][configure-agent] usa un modello di Azure Resource Manager per creare agenti di Jenkins nelle macchine virtuali di Azure.

- Il plug-in [Azure Credentials][configure-credential] consente di archiviare le credenziali delle entità servizio di Azure in Jenkins.

- Il [plug-in Windows Azure Storage][configure-storage] carica gli elementi di compilazione nell'[archivio BLOB di Azure][blob] oppure scarica le dipendenze di compilazione da tale archivio.

È consigliabile esaminare il crescente elenco di tutti i plug-in Azure disponibili che possono essere usati con le risorse di Azure. Per visualizzare l'elenco completo più recente, passare all'[indice dei plug-in per Jenkins][index] e cercare Azure. Per la distribuzione, ad esempio, sono disponibili i plug-in seguenti:

- [Azure Container Agents][container-agents] consente di eseguire un contenitore come agente in Jenkins.

- [Kubernetes Continuous Deploy](https://aka.ms/azjenkinsk8s) distribuisce le configurazioni delle risorse in un cluster Kubernetes.

- [Azure Container Service][acs] distribuisce le configurazioni nel servizio contenitore di Azure con Kubernetes, DC/OS con Marathon o Docker Swarm.

- [Azure Function][functions] distribuisce il progetto in Funzioni di Azure.

- [Azure App Service][app-service] esegue la distribuzione nel servizio app di Azure.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

È possibile ridimensionare Jenkins per supportare carichi di lavoro di dimensioni molto elevate. Per compilazione elastiche, non eseguire le compilazioni nel server master Jenkins. Eseguire invece l'offload delle attività di compilazione negli agenti di Jenkins, di cui è possibile ridurre o aumentare le istanze in modo elastico in base alle esigenze. Per ridimensionare gli agenti sono disponibili due opzioni:

- Usare il plug-in [Azure VM Agents][vm-agent] per creare agenti di Jenkins eseguiti in VM di Azure. Questo plug-in consente di aumentare il numero di istanze degli agenti in modo elastico e può usare tipi distinti di macchine virtuali. È possibile selezionare una diversa immagine di base in Azure Marketplace o usare un'immagine personalizzata. Per informazioni dettagliate sul ridimensionamento degli agenti di Jenkins, vedere [Architecting for Scale][scale] (Progettazione per la scalabilità) nella documentazione di Jenkins.

- Usare il plug-in [Azure Container Agents][container-agents] per eseguire un contenitore come agente nel [servizio contenitore di Azure con Kubernetes](/azure/container-service/kubernetes/) o in [Istanze di contenitore di Azure](/azure/container-instances/).

Il ridimensionamento di macchine virtuali presenta in genere un costo superiore rispetto a quello di contenitori. Per usare contenitori per il ridimensionamento, tuttavia, è necessario che il processo di compilazione venga eseguito con contenitori.

Usare inoltre Archiviazione di Azure per condividere gli elementi di compilazione che potrebbero essere usati nella fase successiva della pipeline da altri agenti di compilazione.

### <a name="scaling-the-jenkins-server"></a>Ridimensionamento del server Jenkins

È possibile aumentare o ridurre le prestazioni della macchina virtuale del server Jenkins modificando la dimensione di VM. Per impostazione predefinita, il [modello di soluzione per Jenkins in Azure][azure-market] specifica la dimensione DS2 v2, con due CPU e 7 GB, che gestisce il carico di lavoro di un team medio-piccolo. Modificare la dimensione di VM scegliendo una diversa opzione durante la creazione del server.

La selezione della dimensione corretta del server dipende dalle dimensioni del carico di lavoro previsto. La community Jenkins gestisce una [guida alla selezione][selection-guide] che consente di identificare la configurazione più appropriata per i requisiti specifici. Azure offre molte [dimensioni per le VM Linux][sizes-linux], per soddisfare tutti i requisiti. Per altre informazioni sul ridimensionamento del master Jenkins, vedere i dettagli in proposito inclusi nelle [procedure consigliate][best-practices] della community Jenkins.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Nel contesto di un server Jenkins, disponibilità significa poter ripristinare tutte le informazioni relative allo stato associate al flusso di lavoro, come i risultati dei test, le librerie create o altri elementi. Per ripristinare il flusso di lavoro in caso di arresto del server Jenkins è necessario mantenere gli elementi o lo stato del flusso di lavoro di importanza critica. Per valutare i requisiti di disponibilità, considerare due metriche comuni:

- L'obiettivo del tempo di ripristino (RTO) specifica per quanto tempo è possibile non usare Jenkins.

- L'obiettivo del punto di ripristino (RPO) indica quanti dati è possibile permettersi di perdere se un'interruzione del servizio influisce su Jenkins.

In pratica, RTO e RPO implicano ridondanza e backup. La disponibilità non riguarda il ripristino dell'hardware, che fa parte di Azure, ma piuttosto il mantenimento dello stato del server Jenkins. Microsoft offre un [contratto di servizio][sla] per le singole istanze di VM. Se questo contratto di servizio non soddisfa gli specifici requisiti in termini di tempo di attività, assicurarsi di avere un piano per il ripristino di emergenza oppure valutare la possibilità di usare una distribuzione di [server Jenkins multimaster][multi-master] (non illustrata in questo documento).

Valutare la possibilità di usare gli [script][disaster] per il ripristino di emergenza nel passaggio 7 della distribuzione per creare un account di archiviazione di Azure con dischi gestiti per archiviare lo stato del server Jenkins. In caso di arresto di Jenkins, è possibile ripristinarne lo stato archiviato in questo account di archiviazione separato.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Usare gli approcci seguenti per bloccare la sicurezza in un server Jenkins di base, perché nello stato di base non è sicuro.

- Configurare un modo sicuro per accedere al server Jenkins. Questa architettura usa HTTP e ha un indirizzo IP pubblico, ma HTTP non è sicuro per impostazione predefinita. Per un accesso sicuro, valutare la possibilità di configurare [HTTPS nel server Nginx][nginx] in uso.

    > [!NOTE]
    > Quando si aggiunge SSL al server, creare una regola NSG per la subnet Jenkins per aprire la porta 443. Per altre informazioni, vedere [Come aprire le porte per una macchina virtuale con il portale di Azure][port443].
    >

- Verificare che la configurazione di Jenkins impedisca richieste intersito false, selezionando Manage Jenkins (Gestisci Jenkins) \> Configure Global Security (Configura sicurezza globale). Questa è l'impostazione predefinita per il server Microsoft Jenkins.

- Configurare l'accesso di sola lettura al dashboard di Jenkins usando il [plug-in Matrix Authorization Strategy][matrix].

- Installare il plug-in [Azure Credentials][configure-credential] per usare Key Vault per gestire i segreti degli asset di Azure, gli agenti nella pipeline e i componenti di terze parti.

- Usare il controllo degli accessi in base al ruolo per limitare l'accesso dell'entità servizio al minimo necessario per eseguire i processi. Ciò consente di limitare eventuali danni dovuti a processi non autorizzati.

I processi Jenkins spesso richiedono segreti per accedere ai servizi di Azure per cui è necessaria l'autorizzazione, come il servizio contenitore di Azure. Per gestire in modo sicuro questi segreti, usare [Key Vault][key-vault] insieme al [plug-in Azure Credentials][configure-credential]. Usare Key Vault per archiviare le credenziali delle entità servizio, le password, i token e altri segreti.

Per ottenere una visualizzazione centrale dello stato di sicurezza delle risorse di Azure, usare il [Centro sicurezza di Azure][security-center]. Il Centro sicurezza monitora potenziali problemi di sicurezza e offre un'immagine completa dello stato della sicurezza della distribuzione. Il Centro sicurezza è configurato per ogni sottoscrizione di Azure. Abilitare la raccolta dei dati di sicurezza come descritto nella [Guida introduttiva per il Centro sicurezza di Azure][quick-start]. Quando la raccolta di dati è abilitata, il Centro sicurezza analizza automaticamente tutte le macchine virtuali create nell'ambito della sottoscrizione.

Il server Jenkins ha un proprio sistema di gestione degli utenti e la community Jenkins offre procedure consigliate per la [protezione di un'istanza di Jenkins in Azure][secure-jenkins]. Il modello di soluzione per Jenkins in Azure implementa tali procedure consigliate.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Usare gruppi di risorse per organizzare le risorse di Azure distribuite. Distribuire gli ambienti di produzione e gli ambienti di sviluppo/test in gruppi di risorse separati, per poter monitorare le risorse di ogni ambiente ed eseguire il rollup dei costi per la fatturazione in base al gruppo di risorse. È inoltre possibile eliminare un intero set di risorse, operazione molto utile nelle distribuzioni di test.

Azure offre diverse funzionalità per [il monitoraggio e la diagnostica][monitoring-diag] dell'infrastruttura generale. Per monitorare l'utilizzo della CPU, questa architettura distribuisce Monitoraggio di Azure. Ad esempio, è possibile usare Monitoraggio di Azure per monitorare l'utilizzo della CPU e inviare una notifica se questo supera l'80%. Un utilizzo elevato della CPU indica che potrebbe essere opportuno aumentare le prestazioni della VM del server Jenkins. È anche possibile inviare una notifica a un utente designato se si verifica un errore nella VM o se la macchina virtuale non è più disponibile.

## <a name="communities"></a>Community

Le community possono rispondere alle domande ed essere utili per configurare correttamente la distribuzione. Valutare gli aspetti seguenti:

- [Blog della community Jenkins](https://jenkins.io/node/)
- [Forum di Azure](https://azure.microsoft.com/support/forums/)
- [Jenkins in Stack Overflow](https://stackoverflow.com/tags/jenkins/info)

Per altre procedure consigliate della community Jenkins, vedere [Jenkins Best Practices][jenkins-best] (Procedure consigliate per Jenkins).

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Per distribuire questa architettura, seguire questa procedura per installare il [modello di soluzione per Jenkins in Azure][azure-market] e quindi installare gli script di configurazione del monitoraggio e del ripristino di emergenza nei passaggi successivi.

### <a name="prerequisites"></a>Prerequisiti

- Questa architettura di riferimento richiede una sottoscrizione di Azure.
- Per creare un'entità servizio di Azure, è necessario avere diritti di amministratore per il tenant di Azure AD associato al server Jenkins distribuito.
- Queste istruzioni presuppongono che l'amministratore di Jenkins sia anche un utente di Azure con almeno i privilegi di collaboratore.

### <a name="step-1-deploy-the-jenkins-server"></a>Passaggio 1: Distribuire il server Jenkins

1. Aprire l'[immagine di Azure Marketplace per Jenkins][azure-market] nel Web browser e selezionare **SCARICA ADESSO** sul lato sinistro della pagina.

2. Esaminare i dettagli relativi ai prezzi e selezionare **Continua** e quindi **Crea** per configurare il server Jenkins nel portale di Azure.

Per istruzioni dettagliate, vedere [Creare un server Jenkins in una VM Linux di Azure dal portale di Azure][create-jenkins]. Per questa architettura di riferimento, è sufficiente rendere operativo il server con l'account di accesso amministratore. È quindi possibile effettuarne il provisioning per usare vari altri servizi.

### <a name="step-2-set-up-sso"></a>Passaggio 2: Configurare l'accesso SSO

Il passaggio viene eseguito dall'amministratore di Jenkins, che deve avere anche un account utente nella directory di Azure AD della sottoscrizione ed essere assegnato al ruolo Collaboratore.

Usare il [plug-in Azure AD][configure-azure-ad] dal centro aggiornamenti Jenkins nel server Jenkins e seguire le istruzioni per configurare l'accesso SSO.

### <a name="step-3-provision-jenkins-server-with-azure-vm-agent-plugin"></a>Passaggio 3: Effettuare il provisioning del server Jenkins con il plug-in Azure VM Agents

Il passaggio viene eseguito dall'amministratore di Jenkins per configurare il plug-in Azure VM Agents, che è già installato.

[Seguire questa procedura per configurare il plug-in][configure-agent]. Per un'esercitazione sulla configurazione delle entità servizio per il plug-in, vedere [Ridimensionare le distribuzioni di Jenkins per soddisfare la richiesta con agenti di macchine virtuali di Azure][scale-agent].

### <a name="step-4-provision-jenkins-server-with-azure-storage"></a>Passaggio 4: Effettuare il provisioning del server Jenkins con Archiviazione di Azure

Il passaggio viene eseguito dall'amministratore di Jenkins, che configura il plug-in Windows Azure Storage già installato.

[Seguire questa procedura per configurare il plug-in][configure-storage].

### <a name="step-5-provision-jenkins-server-with-azure-credential-plugin"></a>Passaggio 5: Effettuare il provisioning del server Jenkins con il plug-in Azure Credentials

Il passaggio viene eseguito dall'amministratore di Jenkins per configurare il plug-in Azure Credentials, che è già installato.

[Seguire questa procedura per configurare il plug-in][configure-credential].

### <a name="step-6-provision-jenkins-server-for-monitoring-by-the-azure-monitor-service"></a>Passaggio 6: Effettuare il provisioning del server Jenkins per il monitoraggio con il servizio Monitoraggio di Azure

Per configurare il monitoraggio per il server Jenkins, seguire le istruzioni riportate in [Creare avvisi sulle metriche in Monitoraggio di Azure per i servizi di Azure][create-metric].

### <a name="step-7-provision-jenkins-server-with-managed-disks-for-disaster-recovery"></a>Passaggio 7: Effettuare il provisioning del server Jenkins con dischi gestiti per il ripristino di emergenza

Il team Microsoft Jenkins ha creato script per il ripristino di emergenza che creano un disco gestito usato per salvare lo stato di Jenkins. In caso di arresto del server, è possibile ripristinarne lo stato più recente.

Scaricare ed eseguire gli script per il ripristino di emergenza da[GitHub][disaster].

È consigliabile esaminare lo [scenario di esempio di Azure](/azure/architecture/example-scenario) seguente, che illustra le soluzioni specifiche usando alcune delle stesse tecnologie:

- [Pipeline di integrazione continua/distribuzione continua per carichi di lavoro basati su contenitori](/azure/architecture/example-scenario/apps/devops-with-aks)

<!-- links -->

[acs]: https://aka.ms/azjenkinsacs
[ad-sp]: /azure/active-directory/develop/active-directory-integrating-applications
[app-service]: https://plugins.jenkins.io/azure-app-service
[azure-ad]: /azure/active-directory/
[azure-market]: https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview
[best-practices]: https://jenkins.io/doc/book/architecting-for-scale/
[blob]: /azure/storage/common/storage-java-jenkins-continuous-integration-solution
[configure-azure-ad]: https://plugins.jenkins.io/azure-ad
[configure-agent]: https://plugins.jenkins.io/azure-vm-agents
[configure-credential]: https://plugins.jenkins.io/azure-credentials
[configure-storage]: https://plugins.jenkins.io/windows-azure-storage
[container-agents]: https://aka.ms/azcontaineragent
[create-jenkins]: /azure/jenkins/install-jenkins-solution-template
[create-metric]: /azure/monitoring-and-diagnostics/insights-alerts-portal
[disaster]: https://github.com/Azure/jenkins/tree/master/disaster_recovery
[functions]: https://aka.ms/azjenkinsfunctions
[index]: https://plugins.jenkins.io
[jenkins-best]: https://wiki.jenkins.io/display/JENKINS/Jenkins+Best+Practices
[jenkins-on-azure]: /azure/jenkins/
[key-vault]: /azure/key-vault/
[managed-disk]: /azure/virtual-machines/linux/managed-disks-overview
[matrix]: https://plugins.jenkins.io/matrix-auth
[monitor]: /azure/monitoring-and-diagnostics/
[monitoring-diag]: /azure/architecture/best-practices/monitoring
[multi-master]: https://jenkins.io/doc/book/architecting-for-scale/
[nginx]: https://www.digitalocean.com/community/tutorials/how-to-create-an-ssl-certificate-on-nginx-for-ubuntu-14-04
[nsg]: /azure/virtual-network/virtual-networks-nsg
[quick-start]: /azure/security-center/security-center-get-started
[port443]: /azure/virtual-machines/windows/nsg-quickstart-portal
[premium]: /azure/virtual-machines/linux/premium-storage
[rbac]: /azure/active-directory/role-based-access-control-what-is
[rg]: /azure/azure-resource-manager/resource-group-overview
[scale]: https://jenkins.io/doc/book/architecting-for-scale/
[scale-agent]: /azure/jenkins/jenkins-azure-vm-agents
[selection-guide]: https://jenkins.io/doc/book/hardware-recommendations/
[service-principal]: /azure/active-directory/develop/active-directory-application-objects
[secure-jenkins]: https://jenkins.io/blog/2017/04/20/secure-jenkins-on-azure/
[security-center]: /azure/security-center/security-center-intro
[sizes-linux]: /azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json
[solution]: https://azure.microsoft.com/blog/announcing-the-solution-template-for-jenkins-on-azure/
[sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
[storage-plugin]: https://wiki.jenkins.io/display/JENKINS/Windows+Azure+Storage+Plugin
[subnet]: /azure/virtual-network/virtual-network-manage-subnet
[vm-agent]: https://wiki.jenkins.io/display/JENKINS/Azure+VM+Agents+plugin
[vnet]: /azure/virtual-network/virtual-networks-overview
[0]: ./images/jenkins-server.png
