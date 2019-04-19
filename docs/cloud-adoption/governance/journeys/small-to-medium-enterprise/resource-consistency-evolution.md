---
title: 'CAF: Piccole e medie imprese - Evoluzione della coerenza delle risorse '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 'Presentazione: Piccole e medie imprese - Evoluzione della coerenza delle risorse'
author: BrianBlanchard
ms.openlocfilehash: 402bb3ff4182123cdc8825522f965f7cf8637980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901732"
---
# <a name="small-to-medium-enterprise-resource-consistency-evolution"></a><span data-ttu-id="2ca0b-103">Piccole e medie imprese: Evoluzione della coerenza delle risorse</span><span class="sxs-lookup"><span data-stu-id="2ca0b-103">Small-to-medium enterprise: Resource Consistency evolution</span></span>

<span data-ttu-id="2ca0b-104">Questo articolo approfondisce la presentazione dello scenario aggiungendo i controlli di coerenza delle risorse per supportare app cruciali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-104">This article evolves the narrative by adding Resource Consistency controls to support mission-critical apps.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="2ca0b-105">Evoluzione dello scenario</span><span class="sxs-lookup"><span data-stu-id="2ca0b-105">Evolution of the narrative</span></span>

<span data-ttu-id="2ca0b-106">Le esperienze dei clienti, gli strumenti di previsione e le infrastrutture migrate continuano a evolversi.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-106">New customer experiences, new prediction tools, and migrated infrastructure continue to progress.</span></span> <span data-ttu-id="2ca0b-107">L'azienda è ora pronta a iniziare a usare questi asset in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-107">The business is now ready to begin using those assets in a production capacity.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="2ca0b-108">Evoluzione dello stato attuale</span><span class="sxs-lookup"><span data-stu-id="2ca0b-108">Evolution of the current state</span></span>

<span data-ttu-id="2ca0b-109">Nella fase precedente di questo scenario, i team di BI e sviluppo delle applicazioni erano quasi pronti a integrare dati finanziari e dei clienti nei carichi di lavoro di produzione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-109">In the previous phase of this narrative, the application development and BI teams were nearly ready to integrate customer and financial data into production workloads.</span></span> <span data-ttu-id="2ca0b-110">Il team IT stava ritirando il data center di ripristino di emergenza.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-110">The IT team was in the process of retiring the DR datacenter.</span></span>

<span data-ttu-id="2ca0b-111">Da allora, sono avvenuti alcuni cambiamenti che influiranno sulla governance:</span><span class="sxs-lookup"><span data-stu-id="2ca0b-111">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="2ca0b-112">L'IT ha ritirato al 100% il data center di ripristino di emergenza, in anticipo rispetto alla pianificazione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-112">IT has retired 100% of the DR datacenter, ahead of schedule.</span></span> <span data-ttu-id="2ca0b-113">Nel corso di questo processo, diversi asset del data center di produzione sono stati identificati come ottimi candidati per la migrazione nel cloud.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-113">In the process, a number of assets in the Production datacenter were identified as cloud migration candidates.</span></span>
- <span data-ttu-id="2ca0b-114">I team di sviluppo delle applicazioni sono ora pronti per il traffico dell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-114">The application development teams are now ready for production traffic.</span></span>
- <span data-ttu-id="2ca0b-115">Il team di BI è pronto a reinserire stime e informazioni dettagliate nei sistemi operativi nel data center di produzione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-115">The BI team is ready to feed predictions and insights back into operation systems in the Production datacenter.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="2ca0b-116">Evoluzione dello stato futuro</span><span class="sxs-lookup"><span data-stu-id="2ca0b-116">Evolution of the future state</span></span>

<span data-ttu-id="2ca0b-117">Prima di usare distribuzioni di Azure nei processi aziendali di produzione, le operazioni nel cloud devono evolversi.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-117">Before using Azure deployments in production business processes, cloud operations must mature.</span></span> <span data-ttu-id="2ca0b-118">Allo stesso tempo, è necessaria un'ulteriore evoluzione della governance per garantire una corretta gestione degli asset.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-118">In conjunction, an additional governance evolution is required to ensure assets can be operated properly.</span></span>

<span data-ttu-id="2ca0b-119">I cambiamenti che riguardano lo stato attuale e quello futuro creano nuovi rischi che richiederanno nuove definizioni di criteri.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-119">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="2ca0b-120">Evoluzione dei rischi tangibili</span><span class="sxs-lookup"><span data-stu-id="2ca0b-120">Evolution of tangible risks</span></span>

<span data-ttu-id="2ca0b-121">**Interruzione delle attività aziendali**: Vi è un rischio intrinseco che qualsiasi nuova piattaforma possa causare l'interruzione di processi aziendali cruciali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-121">**Business Interruption**: There is an inherent risk of any new platform causing interruptions to mission-critical business processes.</span></span> <span data-ttu-id="2ca0b-122">Il team responsabile delle operazioni IT e i team che si occupano in vari modi delle adozioni del cloud sono relativamente inesperti per quanto riguarda le operazioni nel cloud.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-122">The IT Operations team and the teams executing on various cloud adoptions are relatively inexperienced with cloud operations.</span></span> <span data-ttu-id="2ca0b-123">Questo aspetto aumenta il rischio di interruzione, che deve essere mitigato e gestito.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-123">This increases the risk of interruption and must be mitigated and governed.</span></span>

<span data-ttu-id="2ca0b-124">Questo rischio aziendale può tradursi in diversi rischi tecnici:</span><span class="sxs-lookup"><span data-stu-id="2ca0b-124">This business risk can be expanded into a number of technical risks:</span></span>

- <span data-ttu-id="2ca0b-125">Eventuali intrusioni dall'esterno o attacchi Denial of Service potrebbero causare un'interruzione delle attività aziendali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-125">External intrusion or denial of service attacks might cause a business interruption</span></span>
- <span data-ttu-id="2ca0b-126">Asset cruciali potrebbero non essere individuati nel modo appropriato e di conseguenza possono non essere gestiti correttamente.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-126">Mission-critical assets may not be properly discovered, and therefore might not be properly operated</span></span>
- <span data-ttu-id="2ca0b-127">Gli asset non individuati o mal definiti possono non essere supportati dai processi di gestione operativa esistenti.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-127">Undiscovered or mislabeled assets might not be supported by existing operational management processes.</span></span>
- <span data-ttu-id="2ca0b-128">La configurazione degli asset distribuiti potrebbe non soddisfare le aspettative in termini di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-128">The configuration of deployed assets may not meet performance expectations</span></span>
- <span data-ttu-id="2ca0b-129">La registrazione potrebbe non essere effettuata e centralizzata correttamente per consentire la risoluzione dei problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-129">Logging might not be properly recorded and centralized to allow for remediation of performance issues.</span></span>
- <span data-ttu-id="2ca0b-130">Le procedure di ripristino possono non riuscire o impiegare più tempo del previsto.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-130">Recovery policies may fail or take longer than expected.</span></span>
- <span data-ttu-id="2ca0b-131">Processi di distribuzione incoerenti potrebbero produrre lacune a livello di sicurezza che possono causare perdite di dati o interruzioni.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-131">Inconsistent deployment processes might result in security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="2ca0b-132">Deviazioni di configurazione e patch non applicate potrebbero produrre lacune a livello di sicurezza non intenzionali che possono causare perdite di dati o interruzioni.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-132">Configuration drift or missed patches might result in unintended security gaps that could lead to data leaks or interruptions.</span></span>
- <span data-ttu-id="2ca0b-133">La configurazione potrebbe non soddisfare i requisiti dei contratti di servizio definiti o quelli di ripristino concordati.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-133">Configuration might not enforce the requirements of defined SLAs or committed recovery requirements.</span></span>
- <span data-ttu-id="2ca0b-134">Le applicazioni o i sistemi operativi distribuiti potrebbero non soddisfare i requisiti di protezione avanzata.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-134">Deployed operating systems or applications might fail to meet hardening requirements</span></span>
- <span data-ttu-id="2ca0b-135">I numerosi team che lavorano nel cloud pongono un rischio di incoerenza.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-135">With so many teams working in the cloud, there is a risk of inconsistency.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="2ca0b-136">Evoluzione delle definizioni dei criteri</span><span class="sxs-lookup"><span data-stu-id="2ca0b-136">Evolution of the policy statements</span></span>

<span data-ttu-id="2ca0b-137">Le modifiche ai criteri indicate di seguito contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-137">The following changes to policy will help mitigate the new risks and guide implementation.</span></span> <span data-ttu-id="2ca0b-138">L'elenco può sembrare lungo, ma l'adozione di questi criteri può essere più semplice di quanto non sembri.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-138">The list looks long, but adopting these policies may be easier than it appears.</span></span>

1. <span data-ttu-id="2ca0b-139">Tutti gli asset distribuiti devono essere ordinati in categorie in base a criticità e classificazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-139">All deployed assets must be categorized by criticality and data classification.</span></span> <span data-ttu-id="2ca0b-140">Le classificazioni devono essere esaminate dal team di governance del cloud e dal proprietario dell'applicazione prima della distribuzione nel cloud.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-140">Classifications are to be reviewed by the Cloud Governance team and the application owner before deployment to the cloud</span></span>
2. <span data-ttu-id="2ca0b-141">Le subnet che contengono applicazioni cruciali devono essere protette tramite una soluzione firewall in grado di rilevare le intrusioni e rispondere agli attacchi.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-141">Subnets containing mission-critical applications must be protected by a firewall solution capable of detecting intrusions and responding to attacks.</span></span>
3. <span data-ttu-id="2ca0b-142">Gli strumenti di governance devono controllare e soddisfare i requisiti di configurazione definiti dal team di gestione della sicurezza.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-142">Governance tooling must audit and enforce network configuration requirements defined by the Security Management team</span></span>
4. <span data-ttu-id="2ca0b-143">Gli strumenti di governance devono convalidare che tutti gli asset correlati alle app cruciali o ai dati protetti siano inclusi nel monitoraggio per l'esaurimento e l'ottimizzazione delle risorse.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-143">Governance tooling must validate that all assets related to mission-critical apps or protected data are included in monitoring for resource depletion and optimization.</span></span>
5. <span data-ttu-id="2ca0b-144">Gli strumenti di governance devono convalidare che venga raccolto il livello appropriato di dati di registrazione per tutti i dati protetti o le applicazioni cruciali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-144">Governance tooling must validate that the appropriate level of logging data is being collected for all mission-critical applications or protected data.</span></span>
6. <span data-ttu-id="2ca0b-145">Il processo di governance deve convalidare che il backup, il ripristino e il rispetto dei contratti di servizio vengano implementati correttamente per le applicazioni cruciali e i dati protetti.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-145">Governance process must validate that backup, recovery, and SLA adherence are properly implemented for mission-critical applications and protected data.</span></span>
7. <span data-ttu-id="2ca0b-146">Gli strumenti di governance devono limitare le distribuzioni di macchine virtuali solo alle immagini approvate.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-146">Governance tooling must limit virtual machine deployments to approved images only.</span></span>
8. <span data-ttu-id="2ca0b-147">Gli strumenti di governance devono soddisfare il requisito in base al quale gli aggiornamenti automatici non devono essere applicati a tutti gli asset distribuiti che supportano applicazioni cruciali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-147">Governance tooling must enforce that automatic updates are prevented on all deployed assets that support mission-critical applications.</span></span> <span data-ttu-id="2ca0b-148">Le violazioni devono essere esaminate con i team di gestione operativa e risolte in base ai criteri relativi alle operazioni.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-148">Violations must be reviewed with operational management teams and remediated in accordance with operations policies.</span></span> <span data-ttu-id="2ca0b-149">Gli asset che non vengono aggiornati automaticamente devono essere inclusi in processi di proprietà del team responsabile delle operazioni IT.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-149">Assets that are not automatically updated must be included in processes owned by IT Operations.</span></span>
9. <span data-ttu-id="2ca0b-150">Gli strumenti di governance devono convalidare l'assegnazione di tag correlati a costo, criticità, contratto di servizio, applicazione e classificazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-150">Governance tooling must validate tagging related to cost, criticality, SLA, application, and data classification.</span></span> <span data-ttu-id="2ca0b-151">Tutti i valori devono essere allineati ai valori predefiniti gestiti dal team di governance.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-151">All values must align to predefined values managed by the governance team.</span></span>
10. <span data-ttu-id="2ca0b-152">I processi di governance devono includere controlli in fase di distribuzione e in base a cicli regolari per garantire la coerenza tra tutti gli asset.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-152">Governance processes must include audits at the point of deployment and at regular cycles to ensure consistency across all assets.</span></span>
11. <span data-ttu-id="2ca0b-153">Tendenze ed exploit che possono influire sulle distribuzioni cloud devono essere esaminati regolarmente dal team responsabile della sicurezza in modo da fornire aggiornamenti per gli strumenti di gestione della sicurezza usati nel cloud.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-153">Trends and exploits that could affect cloud deployments should be reviewed regularly by the Security team to provide updates to security management tooling used in the cloud.</span></span>
12. <span data-ttu-id="2ca0b-154">Prima della distribuzione nell'ambiente di produzione, tutti i dati protetti e le app cruciali devono essere aggiunti alla soluzione di monitoraggio operativo designata.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-154">Before release into production, all mission-critical apps and protected data must be added to the designated operational monitoring solution.</span></span> <span data-ttu-id="2ca0b-155">Gli asset che non possono essere individuati dagli strumenti per le operazioni IT scelti non devono essere distribuiti nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-155">Assets that cannot be discovered by the chosen IT operations tooling, cannot be released for production use.</span></span> <span data-ttu-id="2ca0b-156">Qualsiasi modifica necessaria per rendere gli asset individuabili deve essere apportata ai processi di distribuzione pertinenti per garantire che gli asset siano individuabili nelle distribuzioni future.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-156">Any changes required to make the assets discoverable must be made to the relevant deployment processes to ensure assets will be discoverable in future deployments.</span></span>
13. <span data-ttu-id="2ca0b-157">Una volta individuati, i team di gestione operativa ridimensioneranno gli asset per garantire che rispondano ai requisiti di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-157">Upon discovery, operational management teams will size assets, to ensure that assets meet performance requirements</span></span>
14. <span data-ttu-id="2ca0b-158">Gli strumenti di distribuzione devono essere approvati dal team di governance del cloud per garantire la governance regolare degli asset distribuiti.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-158">Deployment tooling must be approved by the Cloud Governance team to ensure ongoing governance of deployed assets.</span></span>
15. <span data-ttu-id="2ca0b-159">Gli script di distribuzione devono essere conservati in un archivio centrale accessibile dal team di governance del cloud per la revisione e il controllo periodici.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-159">Deployment scripts must be maintained in a central repository accessible by the Cloud Governance team for periodic review and auditing.</span></span>
16. <span data-ttu-id="2ca0b-160">I processi di revisione della governance devono convalidare che gli asset distribuiti siano configurati correttamente in base ai requisiti relativi a contratti di servizio e ripristino.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-160">Governance review processes must validate that deployed assets are properly configured in alignment with SLA and recovery requirements.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="2ca0b-161">Evoluzione delle procedure consigliate</span><span class="sxs-lookup"><span data-stu-id="2ca0b-161">Evolution of the best practices</span></span>

<span data-ttu-id="2ca0b-162">Questa sezione dell'articolo approfondisce la progettazione dell'MVP per la governance in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-162">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="2ca0b-163">Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove definizioni dei criteri aziendali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-163">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="2ca0b-164">Il team responsabile delle operazioni nel cloud definirà gli strumenti di monitoraggio operativo e di correzione automatica.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-164">The Cloud Operations team will define operational monitoring tooling and automated remediation tooling.</span></span> <span data-ttu-id="2ca0b-165">Il team di governance del cloud supporterà questi processi di individuazione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-165">The Cloud Governance team will support those discovery processes.</span></span> <span data-ttu-id="2ca0b-166">In questo caso d'uso il team responsabile delle operazioni nel cloud ha scelto Monitoraggio di Azure come strumento principale per il monitoraggio delle applicazioni cruciali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-166">In this use case, the Cloud Operations team chose Azure Monitor as the primary tool for monitoring mission-critical applications.</span></span>
2. <span data-ttu-id="2ca0b-167">Creare un archivio in Azure DevOps per l'archiviazione e il controllo delle versioni di tutti i modelli di Resource Manager e le configurazioni tramite script pertinenti.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-167">Create a repository in Azure DevOps to store and version all relevant Resource Manager templates and scripted configurations.</span></span>
3. <span data-ttu-id="2ca0b-168">Implementazione dell'insieme di credenziali di Azure:</span><span class="sxs-lookup"><span data-stu-id="2ca0b-168">Azure Vault implementation:</span></span>
    1. <span data-ttu-id="2ca0b-169">Definire e distribuire l'insieme di credenziali di Azure per i processi di backup e ripristino.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-169">Define and deploy Azure Vault for backup and recovery processes.</span></span>
    2. <span data-ttu-id="2ca0b-170">Creare un modello di Resource Manager per la creazione di un insieme di credenziali in ogni sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-170">Create a Resource Manager template for creation of a vault in each subscription.</span></span>
4. <span data-ttu-id="2ca0b-171">Aggiornare Criteri di Azure per tutte le sottoscrizioni:</span><span class="sxs-lookup"><span data-stu-id="2ca0b-171">Update Azure Policy for all subscriptions:</span></span>
    1. <span data-ttu-id="2ca0b-172">Controllare e applicare la criticità e la classificazione dei dati in tutte le sottoscrizioni per identificare quelle con asset cruciali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-172">Audit and enforce criticality and data classification across all subscriptions to identify any subscriptions with mission-critical assets.</span></span>
    2. <span data-ttu-id="2ca0b-173">Controllare e imporre l'uso delle sole immagini approvate.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-173">Audit and enforce the use of approved images only.</span></span>
5. <span data-ttu-id="2ca0b-174">Implementazione di Monitoraggio di Azure:</span><span class="sxs-lookup"><span data-stu-id="2ca0b-174">Azure Monitor implementation:</span></span>
    1. <span data-ttu-id="2ca0b-175">Una volta identificata una sottoscrizione cruciale, creare un'area di lavoro di Monitoraggio di Azure tramite PowerShell.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-175">Once a mission-critical subscription is identified, create an Azure Monitor workspace using PowerShell.</span></span> <span data-ttu-id="2ca0b-176">Questo è un processo di pre-distribuzione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-176">This is a pre-deployment process.</span></span>
    2. <span data-ttu-id="2ca0b-177">Durante i test di distribuzione, il team responsabile delle operazioni nel cloud distribuisce gli agenti necessari e testa l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-177">During deployment testing, the Cloud Operations team deploys the necessary agents and tests discovery.</span></span>
6. <span data-ttu-id="2ca0b-178">Aggiornare Criteri di Azure per tutte le sottoscrizioni che contengono applicazioni cruciali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-178">Update Azure Policy for all subscriptions that contain mission-critical applications.</span></span>
    1. <span data-ttu-id="2ca0b-179">Controllare e imporre l'applicazione di un gruppo di sicurezza di rete per tutte le schede di interfaccia di rete e subnet.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-179">Audit and enforce the application of an NSG to all NICs and subnets.</span></span> <span data-ttu-id="2ca0b-180">I team responsabili delle reti e della sicurezza IT definiscono il gruppo di sicurezza di rete.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-180">Networking and IT Security define the NSG.</span></span>
    2. <span data-ttu-id="2ca0b-181">Controllare e imporre l'uso di reti virtuali e subnet di rete approvate per ogni interfaccia di rete.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-181">Audit and enforce the use of approved network subnets and VNets for each network interface.</span></span>
    3. <span data-ttu-id="2ca0b-182">Controllare e imporre la limitazione delle tabelle di routing definite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-182">Audit and enforce the limitation of user-defined routing tables.</span></span>
    4. <span data-ttu-id="2ca0b-183">Controllare e applicare la distribuzione degli agenti di Monitoraggio di Azure per tutte le macchine virtuali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-183">Audit and enforce deployment of Azure Monitor agents for all virtual machines.</span></span>
    5. <span data-ttu-id="2ca0b-184">Controllare e imporre la presenza dell'insieme di credenziali di Azure nella sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-184">Audit and enforce that Azure Vault exists in the subscription.</span></span>
7. <span data-ttu-id="2ca0b-185">Configurazione del firewall:</span><span class="sxs-lookup"><span data-stu-id="2ca0b-185">Firewall configuration:</span></span>
    1. <span data-ttu-id="2ca0b-186">Identificare una configurazione di Firewall di Azure che soddisfi i requisiti di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-186">Identify a configuration of Azure Firewall that meets security requirements.</span></span> <span data-ttu-id="2ca0b-187">In alternativa, identificare un dispositivo di terze parti compatibile con Azure.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-187">Alternatively, identify a third-party appliance that is compatible with Azure.</span></span>
    2. <span data-ttu-id="2ca0b-188">Creare un modello di Resource Manager per distribuire il firewall con le configurazioni necessarie.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-188">Create a Resource Manager template to deploy the firewall with required configurations.</span></span>
8. <span data-ttu-id="2ca0b-189">Azure Blueprints:</span><span class="sxs-lookup"><span data-stu-id="2ca0b-189">Azure blueprint:</span></span>
    1. <span data-ttu-id="2ca0b-190">Creare un progetto Azure Blueprints denominato `protected-data`.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-190">Create a new Azure blueprint named `protected-data`.</span></span>
    2. <span data-ttu-id="2ca0b-191">Aggiungere il firewall e i modelli dell'insieme di credenziali di Azure al progetto.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-191">Add the firewall and Azure Vault templates to the blueprint.</span></span>
    3. <span data-ttu-id="2ca0b-192">Aggiungere i nuovi criteri per le sottoscrizioni con dati protetti.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-192">Add the new policies for protected data subscriptions.</span></span>
    4. <span data-ttu-id="2ca0b-193">Pubblicare il progetto in qualsiasi gruppo di gestione destinato a ospitare applicazioni cruciali.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-193">Publish the blueprint to any management group intended to host mission-critical applications.</span></span>
    5. <span data-ttu-id="2ca0b-194">Applicare il nuovo progetto a ogni sottoscrizione interessata, nonché ai progetti esistenti.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-194">Apply the new blueprint to each affected subscription as well as existing blueprints.</span></span>

## <a name="conclusion"></a><span data-ttu-id="2ca0b-195">Conclusioni</span><span class="sxs-lookup"><span data-stu-id="2ca0b-195">Conclusion</span></span>

<span data-ttu-id="2ca0b-196">Questi processi e modifiche aggiuntivi integrati nell'MVP per la governance contribuiscono a mitigare molti dei rischi associati alla governance delle risorse.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-196">These additional processes and changes to the governance MVP help mitigate many of the risks associated with resource governance.</span></span> <span data-ttu-id="2ca0b-197">Insieme, aggiungono controlli di ripristino, ridimensionamento e monitoraggio che semplificano le operazioni basate sul cloud.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-197">Together they add recovery, sizing, and monitoring controls that empower cloud-aware operations.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2ca0b-198">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="2ca0b-198">Next steps</span></span>

<span data-ttu-id="2ca0b-199">In un'epoca in cui l'adozione del cloud continua a evolversi e a offrire valore aziendale, anche i rischi e le esigenze di governance sono destinati a evolversi.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-199">As cloud adoption continues to evolve and deliver additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="2ca0b-200">Per la società fittizia presentata in questo percorso, il prossimo fattore scatenante sarà il momento in cui le dimensioni della distribuzione supereranno 100 asset nel cloud o la spesa mensile supererà 1.000 euro al mese.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-200">For the fictional company in this journey, the next trigger is when the scale of deployment exceeds 100 assets to the cloud or monthly spending exceeds $1,000 per month.</span></span> <span data-ttu-id="2ca0b-201">A questo punto, il team di governance del cloud aggiungerà controlli di gestione dei costi.</span><span class="sxs-lookup"><span data-stu-id="2ca0b-201">At this point, the Cloud Governance team adds Cost Management controls.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2ca0b-202">Evoluzione della gestione dei costi</span><span class="sxs-lookup"><span data-stu-id="2ca0b-202">Cost Management evolution</span></span>](./cost-management-evolution.md)