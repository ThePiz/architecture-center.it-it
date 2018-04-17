---
title: Azure per i professionisti AWS
description: Nozioni fondamentali sgli account, sulla piattaforma e sui servizi di Microsoft Azure. Informazioni sulle principali analogie e differenze tra le piattaforme AWS e Azure. Mettere a frutto l'esperienza con AWS in Azure.
keywords: Professionisti AWS, confronto con Azure, confronto con AWS, differenze tra azure e aws, azure e aws
author: lbrader
ms.date: 03/24/2017
pnp.series.title: Azure for AWS Professionals
ms.openlocfilehash: 0af0890d383d22db0ed9d3b445cdd5b561b498ae
ms.sourcegitcommit: f665226cec96ec818ca06ac6c2d83edb23c9f29c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="azure-for-aws-professionals"></a>Azure per i professionisti AWS

Questo articolo consente ai professionisti di Amazon Web Services (AWS) di acquisire le nozioni di base sugli account, la piattaforma e i servizi di Microsoft Azure. L'articolo prende anche in esame le principali analogie e differenze tra le piattaforme AWS e Azure.

Si apprenderà come:

* Organizzazione di account e risorse in Azure.
* Struttura delle soluzioni disponibili in Azure.
* Differenze tra i principali servizi di Azure e i servizi AWS.

Nel tempo, Azure e AWS hanno creato le proprie funzionalità in modo indipendente e oggi di conseguenza presentano importanti differenze di progettazione e implementazione.

## <a name="overview"></a>Panoramica

Analogamente ad AWS, Microsoft Azure si basa su un set di servizi di calcolo, archiviazione, database e rete. In molti casi i prodotti e i servizi offerti dalle piattaforme si equivalgono. Entrambe consentono, ad esempio, di compilare soluzioni a disponibilità elevata basate su host Windows o Linux. Se si è abituati allo sviluppo usando la tecnologia Linux e i sistemi operativi, entrambe le piattaforme sono valide.

Sebbene le capacità di entrambe le piattaforme siano simili, le risorse che mettono a disposizione tali capacità sono spesso organizzate in modo diverso. Non sempre sussistono relazioni perfettamente corrispondenti tra i servizi necessari per compilare una soluzione. In alcuni casi, un determinato servizio è disponibile in una piattaforma ma non nell'altra. Vedere i [grafici che mettono a confronto i servizi di Azure e di AWS ](services.md).

## <a name="accounts-and-subscriptions"></a>Account e sottoscrizioni

È possibile acquistare i servizi di Azure con diverse opzioni di prezzo, a seconda delle dimensioni e delle esigenze dell'organizzazione. Per informazioni dettagliate, vedere la [panoramica dei prezzi](https://azure.microsoft.com/pricing/).

Le [sottoscrizioni di Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-infrastructure-subscription-accounts-guidelines/) raggruppano le risorse sotto un proprietario assegnato, responsabile della gestione delle fatturazione e delle autorizzazioni. A differenza di AWS, in cui tutte le risorse create con l'account AWS sono vincolate a tale account, le sottoscrizioni di Azure esistono indipendentemente dagli account dei proprietari e possono essere riassegnate a nuovi proprietari in base alle esigenze.

![Confronto tra struttura e proprietà degli account AWS e delle sottoscrizioni di Azure](./images/azure-aws-account-compare.png "Confronto tra struttura e proprietà degli account AWS e delle sottoscrizioni di Azure")
<br/>*Confronto tra struttura e proprietà degli account AWS e delle sottoscrizioni di Azure*
<br/><br/>

Alle sottoscrizioni possono essere assegnati tre tipi di account amministratore:

-   **Amministratore account:**  il proprietario della sottoscrizione e l'account di fatturazione delle risorse usate nella sottoscrizione. L'amministratore account può essere modificato solo trasferendo la proprietà della sottoscrizione.

-   **Amministratore del servizio:** questo account dispone dei diritti per creare e gestire le risorse nella sottoscrizione, ma non è responsabile della fatturazione. Per impostazione predefinita, l'amministratore dell'account e l'amministratore del servizio vengono assegnati allo stesso account. L'amministratore dell'account può assegnare un utente diverso all'account amministratore del servizio per gestire gli aspetti tecnici e operativi di una sottoscrizione. Esiste un unico amministratore del servizio per ogni sottoscrizione.

-   **Coamministratore:** possono essere presenti più account di coamministratore assegnati a una sottoscrizione. I coamministratori non possono cambiare l'amministratore del servizio, ma hanno il controllo completo sulle risorse e sugli utenti della sottoscrizione.

Al di sotto del livello della sottoscrizione è possibile assegnare ruoli utente e singole autorizzazioni a risorse specifiche, analogamente a quanto accade con le autorizzazioni a utenti e gruppi IAM in AWS. In Azure tutti gli account utente sono associati a un account Microsoft o a un account aziendale, ovvero un account gestito tramite Azure Active Directory.

Analogamente agli account AWS, le sottoscrizioni presentano limiti e quote di servizio predefiniti. Per informazioni dettagliate su tali limiti, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/).
I limiti possono essere aumentati fino al valore massimo inviando [una richiesta di supporto tramite il portale di gestione](https://blogs.msdn.microsoft.com/girishp/2015/09/20/increasing-core-quota-limits-in-azure/).

### <a name="see-also"></a>Vedere anche 

-   [Come aggiungere o modificare i ruoli di amministratore di Azure](https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/)

-   [Come scaricare la fattura e i dati di utilizzo giornalieri di Azure](https://azure.microsoft.com/documentation/articles/billing-download-azure-invoice-daily-usage-date/)

## <a name="resource-management"></a>Resource management

Il termine "risorsa" viene usato in Azure e in AWS per indicare qualsiasi istanza di calcolo, oggetto di archiviazione, dispositivo di rete o altre entità che è possibile creare o configurare all'interno della piattaforma.

Le risorse di Azure vengono distribuite e gestite usando uno dei due modelli: il modello di [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) o il [modello di distribuzione classica](/azure/azure-resource-manager/resource-manager-deployment-model) meno recente di Azure.
Ogni nuova risorsa viene creata con il modello di Azure Resource Manager.

### <a name="resource-groups"></a>Gruppi di risorse

Sia Azure che AWS dispongono di entità denominate "gruppi di risorse", nei quali vengono organizzate le risorse quali macchine virtuali, risorse di archiviazione e dispositivi di rete virtuali. Tuttavia, i [gruppi di risorse di Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/) non sono direttamente confrontabili ai gruppi di risorse di AWS.

Mentre in AWS è possibile che una risorsa sia contrassegnata in più gruppi di risorse, una risorsa di Azure è sempre associata a un unico gruppo di risorse. Una risorsa può essere spostata da un gruppo di risorse a un altro gruppo, ma può trovarsi solo in un gruppo di risorse alla volta. I gruppi di risorse sono i raggruppamenti fondamentali usati da Azure Resource Manager.

È possibile organizzare le risorse anche usando i [tag](https://azure.microsoft.com/documentation/articles/resource-group-using-tags/),
ovvero coppie chiave-valore che consentono di raggruppare le risorse della sottoscrizione, indipendentemente dalla loro appartenenza al gruppo di risorse.

### <a name="management-interfaces"></a>Interfacce di gestione

Azure consente di gestire le risorse in modi diversi:

-   [Interfaccia Web](https://azure.microsoft.com/documentation/articles/resource-group-portal/).
    Analogamente al dashboard di AWS, il portale di Azure offre un'interfaccia di gestione completa basata sul Web per le risorse di Azure.

-   [API REST](https://azure.microsoft.com/documentation/articles/resource-manager-rest-api/).
    L'API REST di Azure Resource Manager consente l'accesso a livello di codice alla maggior parte delle funzionalità disponibili nel portale di Azure.

-   [Riga di comando](https://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/).
    L'interfaccia della riga di comando di Azure 2.0 è un'interfaccia in grado di creare e gestire le risorse di Azure. È disponibile per [Linux, Mac OS X e Windows](https://aka.ms/azurecli2).

-   [PowerShell](https://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).
    I moduli di Azure per PowerShell consentono di eseguire attività di gestione automatizzate tramite uno script. PowerShell è disponibile per [Linux, Mac OS X e Windows](https://github.com/PowerShell/PowerShell).

-   [Modelli](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)
    I modelli di Azure Resource Manager offrono capacità di gestione delle risorse basate sui modelli JSON, analogamente al servizio CloudFormation di AWS.

In ognuna di queste interfacce, il gruppo di risorse è fondamentale per la creazione, la distribuzione o la modifica delle risorse di Azure, e ha un ruolo simile a quello dello "stack" nel raggruppamento delle risorse di AWS durante le distribuzioni CloudFormation.

La sintassi e la struttura di queste interfacce sono diverse dai rispettivi equivalenti AWS, ma forniscono funzionalità simili. Molti strumenti di gestione di terze parti usati in AWS, ad esempio [Terraform di Hashicorp](https://www.terraform.io/docs/providers/azurerm/) e [Netflix Spinnaker](http://www.spinnaker.io/) sono disponibili anche in Azure.

### <a name="see-also"></a>Vedere anche 

-   [Linee guida sui gruppi di risorse di Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)

## <a name="regions-and-zones-high-availability"></a>Aree e zone (disponibilità elevata)

L'ambito di impatto degli errori può variare. Alcuni errori hardware, come nel caso di un disco danneggiato, possono influire su un singolo computer host. Un commutatore di rete non funzionante può invece influire su un intero rack di server. Gli errori che causano l'interruzione di un intero data center, ad esempio l'interruzione dell'alimentazione in un data center, sono invece meno comuni. Sono infine rari i casi in cui un'intera area risulta non disponibile.

Uno dei principali modi per rendere resiliente un'applicazione consiste nell'applicare la ridondanza. Ma la ridondanza va pianificata quando si progetta l'applicazione. Il livello di ridondanza necessario dipende inoltre dai requisiti aziendali: non tutte le applicazioni richiedono la ridondanza tra aree per proteggersi da un'interruzione dell'alimentazione a livello di area. In generale, è opportuno trovare il giusto compromesso tra incremento della ridondanza e dell'affidabilità e aumento dei costi e della complessità.  

In AWS, un'area è suddivisa in due o più zone di disponibilità. Una zona di disponibilità corrisponde a un data center fisicamente isolato nell'area geografica. Azure offre alcune funzionalità per rendere ridondante un'applicazione a ogni livello di errore, tra cui **set di disponibilità**, **zona di disponibilità** e **aree abbinate**. 

![](../resiliency/images/redundancy.svg)

La tabella seguente riepiloga ogni opzione.

| &nbsp; | Set di disponibilità | Zona di disponibilità | Area associata |
|--------|------------------|-------------------|---------------|
| Ambito dell'errore | Rack | Data center | Region |
| Routing delle richieste | Bilanciamento del carico | Bilanciamento del carico tra zone | servizio Gestione traffico |
| Latenza di rete | Molto bassa | Basso | Medio-alta |
| Reti virtuali  | VNet | VNet | Peering reti virtuali tra aree |

### <a name="availability-sets"></a>Set di disponibilità 

Per proteggersi da errori hardware localizzati, ad esempio un disco o un commutatore di rete non funzionante, distribuire due o più macchine virtuali in un set di disponibilità. Un set di disponibilità è costituito da due o più *domini di errore* che condividono una fonte di alimentazione e uno switch di rete comuni. Le macchine virtuali in un set di disponibilità vengono distribuite tra i domini di errore, in modo che, se un dominio di errore è interessato da un errore hardware, il traffico di rete possa comunque essere indirizzato alle macchine virtuali negli altri domini di errore. Per altre informazioni sui set di disponibilità, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure](/azure/virtual-machines/windows/manage-availability).

Quando le istanze delle macchine virtuali vengono aggiunte ai set di disponibilità, vengono anche assegnate a un [dominio di aggiornamento](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/). Un dominio di aggiornamento rappresenta un gruppo di macchine virtuali per il quale sono previsti eventi di manutenzione pianificata simultanei. La distribuzione delle macchine virtuali tra più domini di aggiornamento garantisce che, nel momento predefinito, gli eventi pianificati di aggiornamento e applicazione di patch interessino solo un sottoinsieme delle macchine virtuali.

Per garantire che in ogni ruolo sia presente un'istanza operativa, i set di disponibilità devono essere organizzati in base al ruolo dell'istanza dell'applicazione. Ad esempio, in un'applicazione Web a tre livelli è necessario creare set di disponibilità separati per il front-end, l'applicazione e i livelli dati.

![Set di disponibilità di Azure per ogni ruolo applicazione](./images/three-tier-example.png "Set di disponibilità di Azure per ogni ruolo applicazione")

### <a name="availability-zones"></a>Zone di disponibilità

Una [zona di disponibilità](/azure/availability-zones/az-overview) è una zona fisicamente separata in un'area di Azure. Ogni zona di disponibilità può contare su risorse di alimentazione, rete e raffreddamento a sé. La distribuzione di macchine virtuali tra zone di disponibilità consente di proteggere un'applicazione in caso di errori a livello di data center. 

### <a name="paired-regions"></a>Aree abbinate

Per proteggere un'applicazione da un'interruzione dell'alimentazione a livello di area, è possibile distribuire l'applicazione in più aree, tramite [Gestione traffico di Azure][traffic-manager] in modo da distribuire il traffico Internet in aree diverse. Ogni area di Azure è associata a un'altra area e la combinazione di queste aree costituisce una [coppia di aree][paired-regions]. Ad eccezione del Brasile meridionale, le coppie di aree hanno la stessa collocazione geografica in modo da soddisfare i requisiti di residenza dei dati ai fini della giurisdizione per le imposizioni fiscali e normative.

A differenza delle zone di disponibilità, che rappresentano data center fisicamente distinti ma relativamente prossimi a regioni geografiche, le aree abbinate sono in genere lontane almeno 300 miglia. Ciò garantisce che le emergenze su larga scala abbiamo conseguenze solo una delle regioni della coppia. È possibile configurare le coppie adiacenti in modo che i database e i dati del servizio di archiviazione siano sincronizzati e che l'implementazione degli aggiornamenti della piattaforma venga eseguita in un'area della coppia alla volta.

In Azure, il backup dell'[archiviazione con ridondanza geografica](https://azure.microsoft.com/documentation/articles/storage-redundancy/#geo-redundant-storage) viene eseguito automaticamente nell'area abbinata appropriata. Per tutte le altre risorse, la creazione di una soluzione completamente ridondante usando le aree abbinate implica la creazione di copia completa della soluzione in entrambe le aree.


### <a name="see-also"></a>Vedere anche 

-   [Aree e disponibilità per le macchine virtuali in Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-regions-and-availability/)

-   [Disponibilità elevata per le applicazioni Azure](../resiliency/high-availability-azure-applications.md)

-   [Ripristino di emergenza per le applicazioni basate su Azure](../resiliency/disaster-recovery-azure-applications.md)

-   [Manutenzione pianificata per macchine virtuali Linux in Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-planned-maintenance/)

## <a name="services"></a>Services

Consultare la [tabella di confronto dei servizi di AWS e di Azure](https://aka.ms/azure4aws-services) per un elenco completo delle corrispondenze tra servizi e piattaforme.

Non tutti i servizi e i prodotti Azure sono disponibili in tutte le regioni. Consultare la pagina [Prodotti in base all'area](https://azure.microsoft.com/regions/services/) per altre informazioni. Per informazioni sui tempi di attività garantiti e le politiche di credito per i tempi di inattività, leggere la pagina dei [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

Le sezioni seguenti riepilogano le differenze tra i servizi e le funzionalità comunemente usati di AWS e Azure.

### <a name="compute-services"></a>Servizi di calcolo

#### <a name="ec2-instances-and-azure-virtual-machines"></a>Istanze di ES2 e macchine virtuali di Azure

Sebbene i tipi di istanze AWS e le dimensioni delle macchine virtuali di Azure abbiano una suddivisione analoga, sussistono differenze tra RAM, CPU e capacità di archiviazione.

-   [Tipi di istanze di Amazon EC2](https://aws.amazon.com/ec2/instance-types/)

-   [Dimensioni delle macchine virtuali in Azure (Windows)](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)

-   [Dimensioni delle macchine virtuali in Azure (Linux)](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/)

A differenza della fatturazione al secondo di AWS, le macchine virtuali su richiesta di Azure hanno una fatturazione al minuto.

In Azure non sono presenti elementi equivalenti alle istanze Spot o agli host dedicati di EC2.

#### <a name="ebs-and-azure-storage-for-vm-disks"></a>EBS e Archiviazione di Azure per i dischi della macchina virtuale

L'archiviazione permanente dei dati delle macchine virtuali di Azure viene fornita dai [dischi dati](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-about-disks-vhds/) che risiedono nell'archivio BLOB, analogamente all'archiviazione dei volumi dei dischi da parte delle istanze di EC2 su Elastic Block Store (EBS). Anche l'[archiviazione temporanea di Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) offre alle macchine virtuali la stessa archiviazione temporanea in lettura/scrittura e a bassa latenza delle istanze Storage di EC2.

L'IO su disco con prestazioni superiori è supportato tramite l'utilizzo dell'[archiviazione Premium di Azure](https://docs.microsoft.com/azure/storage/storage-premium-storage).
Il metodo è analogo alle opzioni di archiviazione di Provisioned IOPS offerte da AWS.

#### <a name="lambda-azure-functions-azure-web-jobs-and-azure-logic-apps"></a>Lambda, Funzioni di Azure, Processi Web di Azure e App per la logica di Azure

[Funzioni di Azure](https://azure.microsoft.com/services/functions/) è il principale equivalente di Lambda AWS per quanto concerne l'offerta di codice senza server e su richiesta.
Le funzionalità Lambda si sovrappongono tuttavia anche ad altri servizi di Azure:

-   [Processi Web](https://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/): consente di creare attività in background pianificate o in esecuzione continua.

-   [App per la logica](https://azure.microsoft.com/services/logic-apps/): fornisce comunicazioni, integrazione e servizi di gestione delle regole aziendali.

#### <a name="autoscaling-azure-vm-scaling-and-azure-app-service-autoscale"></a>Ridimensionamento automatico, ridimensionamento della macchina virtuale di Azure e ridimensionamento automatico di Servizio App di Azure

Il ridimensionamento automatico in Azure è gestito da due servizi:

-   [Set di scalabilità delle macchine virtuali](https://azure.microsoft.com/documentation/articles/virtual-machine-scale-sets-overview/): consente di distribuire e gestire un set identico di macchine virtuali. È possibile ridimensionare il numero di istanze in base alle esigenze prestazionali.

-   [Ridimensionamento automatico del Servizio app](https://azure.microsoft.com/documentation/articles/web-sites-scale/): consente il ridimensionamento automatico delle soluzioni del Servizio App di Azure.


#### <a name="container-service"></a>Servizio contenitore
Il [servizio contenitore di Azure](https://docs.microsoft.com/azure/container-service/container-service-intro) supporta i contenitori Docker gestiti tramite Docker Swarm, Kubernetes o controller di dominio/sistema operativo.

#### <a name="other-compute-services"></a>Altri servizi di calcolo 


Azure offre numerosi servizi di calcolo che non hanno equivalenti diretti in AWS:

-   [Azure Batch](https://azure.microsoft.com/documentation/articles/batch-technical-overview/): consente di gestire attività con elevato utilizzo di calcolo in una raccolta scalabile di macchine virtuali.

-   [Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-overview/): piattaforma per lo sviluppo e l'hosting di soluzioni di [microservizi](https://azure.microsoft.com/documentation/articles/service-fabric-overview-microservices/) scalabili.

#### <a name="see-also"></a>Vedere anche 

-   [Creare una VM Linux in Azure usando il portale](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-quick-create-portal/)

-   [Architettura di riferimento per Azure - Esecuzione di una VM Linux su Azure](https://azure.microsoft.com/documentation/articles/guidance-compute-single-vm-linux/)

-   [Introduzione alle app Web Node.js nel servizio app di Azure](https://azure.microsoft.com/documentation/articles/app-service-web-nodejs-get-started/)

-   [Architettura di riferimento per Azure - Applicazione Web di base](https://azure.microsoft.com/documentation/articles/guidance-web-apps-basic/)

-   [Creare la prima funzione di Azure](https://azure.microsoft.com/documentation/articles/functions-create-first-azure-function/)

### <a name="storage"></a>Archiviazione

#### <a name="s3ebsefs-and-azure-storage"></a>S3/EBS/EFS e Archiviazione di Azure

Nella piattaforma AWS, l'archiviazione nel cloud è sostanzialmente suddivisa in tre servizi:

-   **Simple Storage Service (S3)**: archiviazione di base di oggetti. Rende disponibili i dati tramite un'API accessibile da Internet.

-   **Elastic Block Storage (EBS)**: archiviazione block-level progettata per l'accesso da parte di una sola macchina virtuale.

-   **Elastic File System (EFS)**: archiviazione di file progettata come archiviazione condivisa per migliaia di istanze di EC2.

In Archiviazione di Azure, [gli account di archiviazione](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) associati a una sottoscrizione consentono di creare e gestire i servizi di archiviazione seguenti:

-   [Archiviazione BLOB](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/): può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. Può essere impostata per l'accesso privato o per condividere contenuto pubblico su Internet. Archiviazione BLOB ha le stesse finalità di AWS S3 ed EBS.
-   [Archiviazione tabelle](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/): archivia set di dati strutturati. L'archiviazione tabelle usa un archivio dati chiave-attributo NoSQL, che consente lo sviluppo e l'accesso rapido a grandi quantità di dati. È simile ai servizi SimpleDB e DynamoDB di AWS.

-   [Archiviazione code](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/): offre un sistema di messaggistica per l'elaborazione del flusso di lavoro e per la comunicazione tra componenti dei servizi cloud.

-   [Archiviazione file](https://azure.microsoft.com/documentation/articles/storage-java-how-to-use-file-storage/): offre un'archiviazione condivisa per le applicazioni che usano il protocollo standard Server Message Block (SMB). L'archiviazione file viene usata in modo analogo a EFS nella piattaforma AWS.
 
#### <a name="glacier-and-azure-storage"></a>Glacier e Archiviazione di Azure 

Il livello di accesso [archivio del servizio Archiviazione BLOB di Azure](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier) è paragonabile al servizio di archiviazione Glacier di AWS. È destinato ai dati a cui si accede raramente archiviati per almeno 180 giorni e può tollerare numerose ore di latenza di recupero. 

Per i dati a cui si accede raramente che devono essere immediatamente disponibili, il [livello di accesso sporadico del servizio Archiviazione BLOB di Azure](/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) offre una soluzione di archiviazione più economica rispetto all'archiviazione BLOB standard. Questo livello di archiviazione è paragonabile al servizio di archiviazione ad accesso non frequente AWS S3.

#### <a name="see-also"></a>Vedere anche 

-   [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](https://azure.microsoft.com/documentation/articles/storage-performance-checklist/)

-   [Guida alla sicurezza di Archiviazione di Azure](https://azure.microsoft.com/documentation/articles/storage-security-guide/)

-   [Modelli e procedure - Indicazioni sulla rete per la distribuzione di contenuti (CDN)](https://azure.microsoft.com/documentation/articles/best-practices-cdn/)

### <a name="networking"></a>Rete

#### <a name="elastic-load-balancing-azure-load-balancer-and-azure-application-gateway"></a>Elastic Load Balancing, Azure Load Balancer e Gateway applicazione di Azure

Gli equivalenti di Azure dei due servizi di Elastic Load Balancing sono:

-   [Bilanciamento del carico](https://azure.microsoft.com/documentation/articles/load-balancer-overview/): offre le stesse funzionalità di Classic Load Balancer di AWS, che consente di distribuire il traffico su più macchine virtuali a livello di rete. Fornisce anche funzionalità di failover.

-   [Gateway applicazione](https://azure.microsoft.com/documentation/articles/application-gateway-introduction/): offre un sistema di routing basato su regole a livello di applicazione, paragonabile ad Application Load Balancer di AWS.

#### <a name="route-53-azure-dns-and-azure-traffic-manager"></a>Route 53, DNS di Azure e Gestione traffico di Azure

In AWS, Route 53 offre servizi di gestione del nome DNS e di routing del traffico DNS e servizi di failover. In Azure tutto ciò è gestito da due servizi:

-   Il servizio [DNS di Azure](https://azure.microsoft.com/documentation/services/dns/) offre la gestione di dominio e DNS.

-   Il servizio [Gestione traffico][traffic-manager] offre routing del traffico a livello DNS, bilanciamento del carico e funzionalità di failover.

#### <a name="direct-connect-and-azure-expressroute"></a>Direct Connect e Azure ExpressRoute

Azure consente di ottenere connessioni dedicate da sito a sito tramite il servizio [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/), che permette la connessione diretta della rete locale alle risorse di Azure tramite una connessione di rete privata dedicata. A un costo inferiore, Azure offre anche [connessioni VPN da sito a sito](https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-site-to-site-resource-manager-portal/) di tipo tradizionale.

#### <a name="see-also"></a>Vedere anche 

-   [Creare una rete virtuale usando il portale di Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/)

-   [Pianificare e progettare reti virtuali di Azure](https://azure.microsoft.com/documentation/articles/virtual-network-vnet-plan-design-arm/)

-   [Procedure consigliate per la sicurezza della rete di Azure](https://azure.microsoft.com/documentation/articles/azure-security-network-security-best-practices/)

### <a name="database-services"></a>Servizi di database

#### <a name="rds-and-azure-relational-database-services"></a>RDS e servizi di database relazionali di Azure

Azure offre diversi servizi di database relazionali equivalenti a RDS (Relational Database Service) di AWS.

-   [Database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)
-   [Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/overview)
-   [Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview)

È possibile distribuire altri motori di database, ad esempio [SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/), [Oracle](https://azure.microsoft.com/campaigns/oracle/) e [MySQL](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-classic-mysql-2008r2/) tramite le istanze di macchine virtuali di Azure.

I costi di RDS di AWS sono determinati dalla quantità di risorse hardware consumate dall'istanza, ad esempio CPU, RAM, archiviazione e larghezza di banda di rete. Nei servizi di database di Azure i costi dipendono dalla dimensione del database, dalle connessioni simultanee e dai livelli di velocità effettiva.

#### <a name="see-also"></a>Vedere anche 

-   [Esercitazioni sul database SQL di Azure](https://azure.microsoft.com/documentation/articles/sql-database-explore-tutorials/)

-   [Configurare la replica geografica per il database SQL di Azure con il portale di Azure](https://azure.microsoft.com/documentation/articles/sql-database-geo-replication-portal/)

-   [Introduzione a Cosmos DB: un database NoSQL JSON](/azure/cosmos-db/sql-api-introduction)

-   [Come usare l'archiviazione tabelle di Azure da Node.js](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/)

### <a name="security-and-identity"></a>Sicurezza e identità

#### <a name="directory-service-and-azure-active-directory"></a>Servizio Directory e Azure Active Directory

Azure suddivide i servizi di directory nelle offerte seguenti:

-   [Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/): servizio per la gestione delle identità e delle directory basato sul cloud.

-   [Azure Active Directory B2B](https://azure.microsoft.com/documentation/articles/active-directory-b2b-collaboration-overview/): consente di abilitare l'accesso alle applicazioni aziendali da identità gestite dai partner.

-   [Azure Active Directory B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-overview/): offre supporto per la gestione degli utenti e degli accessi single-sign-on per le applicazioni rivolte agli utenti.

-   [Azure Active Directory Domain Services](https://azure.microsoft.com/documentation/articles/active-directory-ds-overview/): servizio di hosting del controller di dominio, che consente l'aggiunta di domini compatibili con Active Directory e funzionalità di gestione utente.

#### <a name="web-application-firewall"></a>Web application firewall

Oltre al [Web application firewall di Gateway applicazione](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/), è anche possibile usare[Web application firewall](https://azure.microsoft.com/documentation/articles/application-gateway-webapplicationfirewall-overview/) di fornitori di terze parti come [Barracuda Networks](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/).

#### <a name="see-also"></a>Vedere anche 

-   [Introduzione alla sicurezza in Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-security-getting-started/)

-   [Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure](https://azure.microsoft.com/documentation/articles/azure-security-identity-management-best-practices/)

### <a name="application-and-messaging-services"></a>Applicazioni e servizi di messaggistica

#### <a name="simple-email-service"></a>Simple Email Service

AWS offre il servizio Simple Email Service (SES) per inviare messaggi di posta elettronica di notifica, transazionali o di marketing. In Azure, i servizi di posta elettronica vengono forniti da soluzioni di terze parti quali [Sendgrid](https://sendgrid.com/partners/azure/).

#### <a name="simple-queueing-service"></a>Simple Queueing Service

Simple Queueing Service (SQS) di AWS è un sistema di messaggistica per la connessione di applicazioni, servizi e dispositivi all'interno della piattaforma AWS. Azure offre due servizi dotati di funzionalità simili:

-   [Archiviazione code](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/): un servizio di messaggistica cloud che consente la comunicazione tra i componenti dell'applicazione nella piattaforma Azure.

-   [Bus di servizio](https://azure.microsoft.com/services/service-bus/): un sistema di messaggistica più affidabile per la connessione di applicazioni, servizi e dispositivi. Tramite l'utilizzo dell'[inoltro del bus di servizio](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it) correlato, Bus di servizio consente anche la connessione a servizi e applicazioni ospitati in modalità remota.

#### <a name="device-farm"></a>Farm di dispositivi

AWS Device Farm offre servizi di testing di app su più dispositivi. In Azure, [Xamarin Test Cloud](https://www.xamarin.com/test-cloud) offre servizi simili di testing front-end su più dispositivi per dispositivi mobili.

Oltre al testing front-end, [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) offre risorse di testing back-end per ambienti Linux e Windows.

#### <a name="see-also"></a>Vedere anche 

-   [Come usare l'archiviazione di accodamento da Node.js](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-queues/)

-   [Come usare le code del bus di servizio](https://azure.microsoft.com/documentation/articles/service-bus-nodejs-how-to-use-queues/)

### <a name="analytics-and-big-data"></a>Analisi e Big Data

[Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/) è il pacchetto Azure di prodotti e servizi progettati per acquisire, organizzare, analizzare e visualizzare grandi volumi di dati. La suite di Cortana include i servizi seguenti:

-   [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): distribuzione Apache gestita che include Hadoop, Spark, Storm o HBase.

-   [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/): offre servizi di orchestrazione dei dati e funzionalità di pipeline di dati.

-   [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/): archivio dati relazionale su larga scala.

-   [Data Lake Store](https://azure.microsoft.com/documentation/services/data-lake-store/): archiviazione su larga scala ottimizzata per carichi di lavoro di analisi dei Big Data.

-   [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/): creazione e applicazione di analisi predittive sui dati.

-   [Analisi di flusso](https://azure.microsoft.com/documentation/services/stream-analytics/): analisi dei dati in tempo reale.

-   [Data Lake Analytics](https://azure.microsoft.com/documentation/articles/data-lake-analytics-overview/): servizio di analisi su larga scala ottimizzato per l'utilizzo con Data Lake Store.

-   [Power BI](https://powerbi.microsoft.com/): usato per migliorare la visualizzazione dei dati.

#### <a name="see-also"></a>Vedere anche 

-   [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com/)

-   [Informazioni sulle soluzioni Microsoft per Big Data](https://msdn.microsoft.com/library/dn749804.aspx)

-   [Azure Data Lake e Blog di Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/)

### <a name="internet-of-things"></a>Internet delle cose

#### <a name="see-also"></a>Vedere anche 

-   [Introduzione all'hub IoT di Azure](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/)

-   [Confronto tra l'hub IoT e Hub eventi](https://azure.microsoft.com/documentation/articles/iot-hub-compare-event-hubs/)

### <a name="mobile-services"></a>Servizi mobili

#### <a name="notifications"></a>Notifiche

Gli hub di notifica non supportano l'invio di messaggi SMS o di posta elettronica; per queste tipologie di recapito è necessario usare servizi di terze parti.

#### <a name="see-also"></a>Vedere anche 

-   [Creare un'app Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-android-get-started/)

-   [Autenticazione e autorizzazione in App per dispositivi mobili di Azure](https://azure.microsoft.com/documentation/articles/app-service-mobile-auth/)

-   [Invio di notifiche push con Hub di notifica di Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-android-push-notification-google-fcm-get-started/)

### <a name="management-and-monitoring"></a>Gestione e monitoraggio

#### <a name="see-also"></a>Vedere anche 
-   [Indicazioni di monitoraggio e diagnostica](https://azure.microsoft.com/documentation/articles/best-practices-monitoring/)

-   [Procedure consigliate per la creazione di modelli di Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-manager-template-best-practices/)

-   [Modelli di avvio rapido di Azure Resource Manager](https://azure.microsoft.com/documentation/templates/)


## <a name="next-steps"></a>Passaggi successivi

-   [Tabella di confronto dei servizi di AWS e di Azure](https://aka.ms/azure4aws-services)

-   [Quadro generale interattivo della piattaforma Azure](http://azureplatform.azurewebsites.net/)

-   [Inizia a usare Azure](https://azure.microsoft.com/get-started/)

-   [Architetture delle soluzioni di Azure](https://azure.microsoft.com/solutions/architecture/)

-   [Architetture di riferimento di Azure](https://azure.microsoft.com/documentation/articles/guidance-architecture/)

-   [Modelli e procedure - Informazioni aggiuntive su Azure](https://azure.microsoft.com/documentation/articles/guidance/)

-   [Corso online gratuito: Microsoft Azure per i professionisti AWS](http://aka.ms/azureforaws)


<!-- links -->

[paired-regions]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[traffic-manager]: /azure/traffic-manager/