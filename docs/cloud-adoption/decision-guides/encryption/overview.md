---
title: 'CAF: Crittografia'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sulla crittografia come servizio di base nelle migrazioni di Azure.
author: rotycenh
ms.openlocfilehash: 660206d57ded9a93d73c57ba9cb8058020d87525
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901452"
---
# <a name="encryption-decision-guide"></a><span data-ttu-id="908ce-103">Guida alle decisioni relative alla crittografia</span><span class="sxs-lookup"><span data-stu-id="908ce-103">Encryption decision guide</span></span>

<span data-ttu-id="908ce-104">La crittografia protegge i dati dall'accesso non autorizzato.</span><span class="sxs-lookup"><span data-stu-id="908ce-104">Encrypting data protects it against unauthorized access.</span></span> <span data-ttu-id="908ce-105">Criteri di crittografia correttamente implementati forniscono ulteriori livelli di sicurezza per i carichi di lavoro basati sul cloud e li proteggono dalle azioni di utenti malintenzionati e altri utenti non autorizzati sia all'interno che all'esterno dell'organizzazione e delle reti.</span><span class="sxs-lookup"><span data-stu-id="908ce-105">Properly implemented encryption policy provides additional layers of security for your cloud-based workloads and guards against attackers and other unauthorized users from both inside and outside your organization and networks.</span></span>

<span data-ttu-id="908ce-106">Per quanto sia generalmente desiderabile, la crittografia delle risorse comporta dei costi che possono aumentare la latenza e l'utilizzo complessivo delle risorse.</span><span class="sxs-lookup"><span data-stu-id="908ce-106">While encrypting resources is generally desirable, encryption does have costs that can increase latency and overall resource usage.</span></span> <span data-ttu-id="908ce-107">Per i carichi di lavoro complessi, trovare il giusto equilibrio fra crittografia e prestazioni è essenziale.</span><span class="sxs-lookup"><span data-stu-id="908ce-107">For demanding workloads, striking the correct balance between encryption and performance is essential.</span></span>

![Grafico delle opzioni di crittografia, dalla meno complessa alla più complessa, allineato con i collegamenti sotto](../../_images/discovery-guides/discovery-guide-encryption.png)

<span data-ttu-id="908ce-109">Passare a: [Gestione delle chiavi](#key-management) | [Crittografia dei dati](#data-encryption) | [Altre informazioni](#learn-more)</span><span class="sxs-lookup"><span data-stu-id="908ce-109">Jump to: [Key management](#key-management) | [Data encryption](#data-encryption) | [Learn more](#learn-more)</span></span>

<span data-ttu-id="908ce-110">Il punto di flesso nella determinazione di una strategia di crittografia cloud è incentrato sui criteri aziendali e sui requisiti di conformità.</span><span class="sxs-lookup"><span data-stu-id="908ce-110">The inflection point when determining a cloud encryption strategy focuses on corporate policy and compliance mandates.</span></span>

<span data-ttu-id="908ce-111">È possibile implementare la crittografia in un ambiente cloud in diversi modi, che si differenziano per costo e complessità.</span><span class="sxs-lookup"><span data-stu-id="908ce-111">There are multiple ways to implement encryption in a cloud environment, with varying cost and complexity.</span></span> <span data-ttu-id="908ce-112">I criteri aziendali e la conformità di terze parti sono fattori decisivi nella pianificazione di una strategia di crittografia.</span><span class="sxs-lookup"><span data-stu-id="908ce-112">Corporate policy and third-party compliance are the biggest drivers when planning an encryption strategy.</span></span> <span data-ttu-id="908ce-113">Quasi tutte le soluzioni basate sul cloud offrono meccanismi standard per la crittografia dei dati, inattivi o in transito.</span><span class="sxs-lookup"><span data-stu-id="908ce-113">Most cloud-based solutions provide standard mechanisms for encrypting data, whether at rest or in transit.</span></span> <span data-ttu-id="908ce-114">Tuttavia, per i criteri e i requisiti di conformità che richiedono controlli più rigorosi, come i segreti standardizzati e la gestione delle chiavi, la crittografia dei dati in uso o la crittografia specifica dei dati, sarà probabilmente necessario implementare una soluzione complessa.</span><span class="sxs-lookup"><span data-stu-id="908ce-114">However, for policies and compliance requirements that demand tighter controls, such as standardized secrets and key management, encryption in-use, or data specific encryption, you will likely need to implement a complex solution.</span></span>

## <a name="key-management"></a><span data-ttu-id="908ce-115">Gestione delle chiavi</span><span class="sxs-lookup"><span data-stu-id="908ce-115">Key management</span></span>

<span data-ttu-id="908ce-116">I moderni sistemi di gestione delle chiavi dovrebbero offrire supporto per l'archiviazione delle chiavi usando moduli di protezione hardware per una maggiore protezione.</span><span class="sxs-lookup"><span data-stu-id="908ce-116">Modern key management systems should offer support for storing keys using hardware security modules (HSMs) for increased protection.</span></span> <span data-ttu-id="908ce-117">Pertanto, un sistema di gestione delle chiavi è fondamentale per la capacità dell'organizzazione di creare e archiviare chiavi di crittografia, password importanti, stringhe di connessione e altre informazioni IT riservate.</span><span class="sxs-lookup"><span data-stu-id="908ce-117">Thus, a key management system is critical to your organization's ability to create and store cryptographic keys, important passwords, connection strings, and other IT confidential information.</span></span>

<span data-ttu-id="908ce-118">Nell'ambito della pianificazione di una migrazione al cloud, la tabella seguente descrive come è possibile archiviare e gestire le chiavi, i certificati e i segreti di crittografia, che hanno un'importanza fondamentale per la creazione di distribuzioni cloud sicure e gestibili:</span><span class="sxs-lookup"><span data-stu-id="908ce-118">When planning a cloud migration, the following table describes how you can store and manage encryption keys, certificates, and secrets, which are critical for creating secure and manageable cloud deployments:</span></span>

| <span data-ttu-id="908ce-119">Domanda</span><span class="sxs-lookup"><span data-stu-id="908ce-119">Question</span></span> | <span data-ttu-id="908ce-120">Reti cloud native</span><span class="sxs-lookup"><span data-stu-id="908ce-120">Cloud Native</span></span> | <span data-ttu-id="908ce-121">Ibrido</span><span class="sxs-lookup"><span data-stu-id="908ce-121">Hybrid</span></span> | <span data-ttu-id="908ce-122">Locale</span><span class="sxs-lookup"><span data-stu-id="908ce-122">On-premises</span></span> |
|---------------------------------------------------------------------------------------------------------------------------------------|--------------|--------|-------------|
| <span data-ttu-id="908ce-123">Nell'organizzazione manca una soluzione di gestione centralizzata di chiavi e segreti?</span><span class="sxs-lookup"><span data-stu-id="908ce-123">Does your organization lack centralized key and secret management?</span></span>                                                                    | <span data-ttu-id="908ce-124">Sì</span><span class="sxs-lookup"><span data-stu-id="908ce-124">Yes</span></span>          | <span data-ttu-id="908ce-125">No </span><span class="sxs-lookup"><span data-stu-id="908ce-125">No</span></span>     | <span data-ttu-id="908ce-126">No </span><span class="sxs-lookup"><span data-stu-id="908ce-126">No</span></span>          |
| <span data-ttu-id="908ce-127">Sarà necessario limitare la creazione di chiavi e segreti nei dispositivi all'hardware locale, quando si usano queste chiavi nel cloud?</span><span class="sxs-lookup"><span data-stu-id="908ce-127">Will you need to limit the creation of keys and secrets to devices to your on-premises hardware, while using these keys in the cloud?</span></span> | <span data-ttu-id="908ce-128">No </span><span class="sxs-lookup"><span data-stu-id="908ce-128">No</span></span>           | <span data-ttu-id="908ce-129">Sì</span><span class="sxs-lookup"><span data-stu-id="908ce-129">Yes</span></span>    | <span data-ttu-id="908ce-130">No </span><span class="sxs-lookup"><span data-stu-id="908ce-130">No</span></span>          |
| <span data-ttu-id="908ce-131">Nell'organizzazione sono in vigore regole o criteri che impediscono di archiviare chiavi e segreti fuori sede?</span><span class="sxs-lookup"><span data-stu-id="908ce-131">Does your organization have rules or policies in place that would prevent keys and secrets from being stored offsite?</span></span>                | <span data-ttu-id="908ce-132">No </span><span class="sxs-lookup"><span data-stu-id="908ce-132">No</span></span>           | <span data-ttu-id="908ce-133">No </span><span class="sxs-lookup"><span data-stu-id="908ce-133">No</span></span>     | <span data-ttu-id="908ce-134">Sì</span><span class="sxs-lookup"><span data-stu-id="908ce-134">Yes</span></span>         |

### <a name="cloud-native"></a><span data-ttu-id="908ce-135">Cloud nativo</span><span class="sxs-lookup"><span data-stu-id="908ce-135">Cloud native</span></span>

<span data-ttu-id="908ce-136">Con la gestione delle chiavi nativa del cloud, tutte le chiavi e i segreti vengono generati, gestiti e archiviati in un insieme di credenziali basato sul cloud.</span><span class="sxs-lookup"><span data-stu-id="908ce-136">With cloud native key management, all keys and secrets are generated, managed, and stored in a cloud-based vault.</span></span> <span data-ttu-id="908ce-137">Questo approccio semplifica molte attività IT correlate alla gestione delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="908ce-137">This approach simplifies many IT tasks related to key management.</span></span>

<span data-ttu-id="908ce-138">Presupposti relativi alla gestione delle chiavi nativa del cloud. L'uso di un sistema di gestione delle chiavi nativo del cloud presuppone le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="908ce-138">Cloud native key management assumptions: Using a cloud native key management system assumes the following:</span></span>

- <span data-ttu-id="908ce-139">Si considera attendibile la soluzione cloud di gestione delle chiavi con la creazione, la gestione e l'hosting di chiavi e segreti dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="908ce-139">You trust the cloud key management solution with creating, managing, and hosting your organization's secrets and keys.</span></span>
- <span data-ttu-id="908ce-140">Si abilitano tutte le applicazioni e i servizi locali che si basano sull'accesso a servizi di crittografia o segreti ad accedere al sistema di gestione delle chiavi nel cloud.</span><span class="sxs-lookup"><span data-stu-id="908ce-140">You enable all on-premises applications and services that rely on accessing encryption services or secrets to access the cloud key management system.</span></span>

### <a name="hybrid-bring-your-own-key"></a><span data-ttu-id="908ce-141">Gestione ibrida (BYOK, Bring Your Own Key)</span><span class="sxs-lookup"><span data-stu-id="908ce-141">Hybrid (bring your own key)</span></span>

<span data-ttu-id="908ce-142">L'approccio BYOK consiste nel generare chiavi nell'hardware HSM dedicato nell'ambiente locale, per poi trasferirle in un sistema di gestione delle chiavi sicuro nel cloud e usarle con le risorse cloud.</span><span class="sxs-lookup"><span data-stu-id="908ce-142">With a bring-your-own-key approach, you generate keys on dedicated HSM hardware within your on-premises environment, then transfer the keys to a secure cloud key management system for use with cloud resources.</span></span>

<span data-ttu-id="908ce-143">Presupposti relativi alla gestione delle chiavi ibrida. L'uso di un sistema di gestione delle chiavi ibrido presuppone le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="908ce-143">Hybrid key management assumptions: Using a hybrid key management system assumes the following:</span></span>

- <span data-ttu-id="908ce-144">Si considera attendibile l'infrastruttura sottostante della piattaforma cloud a livello di sicurezza e controllo di accesso per l'hosting e l'uso di chiavi e segreti.</span><span class="sxs-lookup"><span data-stu-id="908ce-144">You trust the underlying security and access control infrastructure of the cloud platform for hosting and using your keys and secrets.</span></span>
- <span data-ttu-id="908ce-145">I criteri normativi o organizzativi richiedono di mantenere nell'ambiente locale la creazione e la gestione dei segreti e delle chiavi dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="908ce-145">You are required by regulatory or organizational policy to keep the creation and management of your organization's secrets and keys on-premises.</span></span>

### <a name="on-premises-hold-your-own-key"></a><span data-ttu-id="908ce-146">Gestione locale (HYOK, Hold Your Own Key)</span><span class="sxs-lookup"><span data-stu-id="908ce-146">On-premises (hold your own key)</span></span>

<span data-ttu-id="908ce-147">In alcune situazioni potrebbero esserci normative, criteri o motivi tecnici che impediscono di archiviare le chiavi in un sistema di gestione delle chiavi fornito da un servizio cloud pubblico.</span><span class="sxs-lookup"><span data-stu-id="908ce-147">In certain scenarios, there may be regulatory, policy, or technical reasons why you can't store keys on a key management system provided by a public cloud service.</span></span> <span data-ttu-id="908ce-148">In questi casi è necessario mantenere le chiavi nell'hardware locale e fornire un meccanismo che consenta alle risorse basate sul cloud di accedere a queste chiavi ai fini della crittografia.</span><span class="sxs-lookup"><span data-stu-id="908ce-148">In these cases, you must maintain keys using on-premises hardware, and provision a mechanism to allow cloud-based resource to access these keys for encryption purposes.</span></span> <span data-ttu-id="908ce-149">Tenere presente che un approccio HYOK potrebbe non essere compatibile con tutti i servizi cloud.</span><span class="sxs-lookup"><span data-stu-id="908ce-149">Note that a hold your own key approach may not be compatible with all cloud services.</span></span>

<span data-ttu-id="908ce-150">Presupposti relativi alla gestione delle chiavi locale. L'uso di un sistema di gestione delle chiavi locale presuppone le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="908ce-150">On-premises key management assumptions: Using an on-premises key management system assumes the following:</span></span>

- <span data-ttu-id="908ce-151">I criteri normativi o organizzativi richiedono di mantenere nell'ambiente locale la creazione, la gestione e l'hosting dei segreti e delle chiavi dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="908ce-151">You are required by regulatory or organizational policy to keep the creation, management, and hosting of your organization's secrets and keys on-premises.</span></span>
- <span data-ttu-id="908ce-152">Tutte le applicazioni o i servizi basati sul cloud che si basano sull'accesso a servizi di crittografia o segreti possono accedere al sistema di gestione delle chiavi locale.</span><span class="sxs-lookup"><span data-stu-id="908ce-152">Any cloud-based applications or services that rely on accessing encryption services or secrets can access the on-premises key management system.</span></span>

## <a name="data-encryption"></a><span data-ttu-id="908ce-153">Crittografia dei dati</span><span class="sxs-lookup"><span data-stu-id="908ce-153">Data encryption</span></span>

<span data-ttu-id="908ce-154">I dati possono avere numerosi stati diversi con esigenze di crittografia diverse da considerare quando si pianificano i criteri di crittografia:</span><span class="sxs-lookup"><span data-stu-id="908ce-154">There are several different states of data with different encryption needs to consider when planning your encryption policy:</span></span>

| <span data-ttu-id="908ce-155">Stato dei dati</span><span class="sxs-lookup"><span data-stu-id="908ce-155">Data state</span></span> | <span data-ttu-id="908ce-156">Dati</span><span class="sxs-lookup"><span data-stu-id="908ce-156">Data</span></span> |
|-----|-----|
| <span data-ttu-id="908ce-157">Dati in transito</span><span class="sxs-lookup"><span data-stu-id="908ce-157">Data in transit</span></span> | <span data-ttu-id="908ce-158">Traffico di rete interno, connessioni Internet, connessioni fra data center o reti virtuali</span><span class="sxs-lookup"><span data-stu-id="908ce-158">Internal network traffic, internet connections, connections between datacenters or virtual networks</span></span> |
| <span data-ttu-id="908ce-159">Dati inattivi</span><span class="sxs-lookup"><span data-stu-id="908ce-159">Data at rest</span></span>    | <span data-ttu-id="908ce-160">Database, file, unità virtuali, archivio PaaS</span><span class="sxs-lookup"><span data-stu-id="908ce-160">Databases, files, virtual drives, PaaS storage</span></span> |
| <span data-ttu-id="908ce-161">Dati in uso</span><span class="sxs-lookup"><span data-stu-id="908ce-161">Data in use</span></span>     | <span data-ttu-id="908ce-162">Dati caricati nella RAM o in cache della CPU</span><span class="sxs-lookup"><span data-stu-id="908ce-162">Data loaded in RAM or in CPU caches</span></span> |

### <a name="data-in-transit"></a><span data-ttu-id="908ce-163">Dati in transito</span><span class="sxs-lookup"><span data-stu-id="908ce-163">Data in transit</span></span>

<span data-ttu-id="908ce-164">I dati in transito sono dati che si spostano tra le risorse nella rete interna, tra i data center, in reti esterne o su Internet.</span><span class="sxs-lookup"><span data-stu-id="908ce-164">Data in transit is data moving between resources on the internal, between datacenters or external networks, or over the internet.</span></span>

<span data-ttu-id="908ce-165">La crittografia dei dati in transito avviene in genere richiedendo i protocolli SSL/TLS per il traffico.</span><span class="sxs-lookup"><span data-stu-id="908ce-165">Encrypting data in transit is usually done by requiring SSL/TLS protocols for traffic.</span></span> <span data-ttu-id="908ce-166">Il traffico in transito tra le risorse ospitate nel cloud e una rete esterna o la rete Internet pubblica deve sempre essere crittografato.</span><span class="sxs-lookup"><span data-stu-id="908ce-166">Traffic transiting between your cloud-hosted resources to external network or the public internet should always be encrypted.</span></span> <span data-ttu-id="908ce-167">Per impostazione predefinita, le risorse PaaS in genere applicano anche la crittografia SSL/TLS al traffico.</span><span class="sxs-lookup"><span data-stu-id="908ce-167">PaaS resources generally also enforce SSL/TLS encryption to traffic by default.</span></span> <span data-ttu-id="908ce-168">La decisione di applicare la crittografia al traffico tra le risorse IaaS ospitate all'interno della rete virtuale spetta al team di adozione del cloud e al proprietario del carico di lavoro ed è generalmente consigliata.</span><span class="sxs-lookup"><span data-stu-id="908ce-168">Whether you enforce encryption for traffic between IaaS resources hosted inside your virtual networks is a decision for your Cloud Adoption Team and workload owner and is generally recommended.</span></span>

<span data-ttu-id="908ce-169">**Presupposti relativi alla crittografia dei dati in transito**.</span><span class="sxs-lookup"><span data-stu-id="908ce-169">**Encrypting data in transit assumptions**.</span></span> <span data-ttu-id="908ce-170">L'implementazione dei criteri di crittografia corretti per i dati in transito presuppone le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="908ce-170">Implementing proper encryption policy for data in transit assumes the following:</span></span>

- <span data-ttu-id="908ce-171">Tutti gli endpoint accessibili pubblicamente nell'ambiente cloud comunicheranno con la rete Internet pubblica tramite i protocolli SSL/TLS.</span><span class="sxs-lookup"><span data-stu-id="908ce-171">All publicly accessible endpoints in your cloud environment will communicate with the public internet using SSL/TLS protocols.</span></span>
- <span data-ttu-id="908ce-172">Per stabilire la connessione tra una rete cloud e la rete locale o un'altra rete esterna attraverso la rete Internet pubblica, si usano i protocolli VPN crittografati.</span><span class="sxs-lookup"><span data-stu-id="908ce-172">When connecting cloud networks with on-premises or other external network over the public internet, use encrypted VPN protocols.</span></span>
- <span data-ttu-id="908ce-173">Per stabilire la connessione tra una rete cloud e la rete locale o un'altra rete esterna attraverso una connessione WAN dedicata come ExpressRoute, si userà una VPN o un'altra appliance di crittografia locale associata a una VPN o un'altra appliance di crittografia virtuale corrispondente distribuita nella rete cloud.</span><span class="sxs-lookup"><span data-stu-id="908ce-173">When connecting cloud networks with on-premises or other external network using a dedicated WAN connection such as ExpressRoute, you will use a VPN or other encryption appliance on-premises paired with a corresponding virtual VPN or encryption appliance deployed to your cloud network.</span></span>
- <span data-ttu-id="908ce-174">In presenza di dati sensibili che non devono essere inclusi nei log sul traffico o in altri report di diagnostica visibili al personale IT, si crittograferà tutto il traffico tra le risorse nella rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="908ce-174">If you have sensitive data that shouldn't be included in traffic logs or other diagnostics reports visible to IT staff, you will encrypt all traffic between resources in your virtual network.</span></span>

### <a name="data-at-rest"></a><span data-ttu-id="908ce-175">Dati inattivi</span><span class="sxs-lookup"><span data-stu-id="908ce-175">Data at rest</span></span>

<span data-ttu-id="908ce-176">I dati inattivi sono i dati che non vengono spostati o elaborati attivamente e includono file, database, unità di macchine virtuali, account di archiviazione PaaS o risorse simili.</span><span class="sxs-lookup"><span data-stu-id="908ce-176">Data at rest represents any data not being actively moved or processed, including files, databases, virtual machine drives, PaaS storage accounts, or similar assets.</span></span> <span data-ttu-id="908ce-177">La crittografia dei dati archiviati protegge i dispositivi virtuali o i file dall'accesso non autorizzato che può avvenire tramite penetrazione da una rete esterna, rilasci accidentali o utenti interni malintenzionati.</span><span class="sxs-lookup"><span data-stu-id="908ce-177">Encrypting stored data protects virtual devices or files against unauthorized access either from external network penetration, rogue internal users, or accidental releases.</span></span>

<span data-ttu-id="908ce-178">Per le risorse di database e archiviazione PaaS generalmente la crittografia è applicata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="908ce-178">PaaS storage and database resources generally enforce encryption by default.</span></span> <span data-ttu-id="908ce-179">Le risorse virtuali IaaS possono essere protette mediante crittografia del disco virtuale usando le chiavi di crittografia archiviate nel sistema di gestione delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="908ce-179">IaaS virtual resources can be secured through virtual disk encryption using cryptographic keys stored in your key management system.</span></span>

<span data-ttu-id="908ce-180">La crittografia dei dati inattivi comprende anche tecniche di crittografia dei database più avanzate, come la crittografia a livello di colonna e di riga, che offre un controllo notevolmente superiore sui dati esatti da proteggere.</span><span class="sxs-lookup"><span data-stu-id="908ce-180">Encryption for data at rest also encompasses more advanced database encryption techniques, such as column-level and row level encryption, which provides much more control over exactly what data is being secured.</span></span>

<span data-ttu-id="908ce-181">I criteri e i requisiti di conformità complessivi, il livello di riservatezza dei dati archiviati e i requisiti di prestazioni dei carichi di lavoro dovrebbero determinare quali risorse debbano essere crittografate.</span><span class="sxs-lookup"><span data-stu-id="908ce-181">Your overall policy and compliance requirements, the sensitivity of the data being stored, and the performance requirements of your workloads should determine which assets require encryption.</span></span>

<span data-ttu-id="908ce-182">**Presupposti relativi alla crittografia dei dati inattivi**.</span><span class="sxs-lookup"><span data-stu-id="908ce-182">**Encrypting Data at Rest Assumptions**.</span></span> <span data-ttu-id="908ce-183">La crittografia dei dati inattivi presuppone le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="908ce-183">Encrypting data at rest assumes the following:</span></span>

- <span data-ttu-id="908ce-184">Si archiviano i dati che non sono destinati all'utilizzo pubblico.</span><span class="sxs-lookup"><span data-stu-id="908ce-184">You are storing data that is not meant for public consumption.</span></span>
- <span data-ttu-id="908ce-185">I carichi di lavoro possono accettare i costi di latenza aggiuntivi per la crittografia dei dischi.</span><span class="sxs-lookup"><span data-stu-id="908ce-185">Your workloads can accept the added latency cost of disk encryption.</span></span>

### <a name="data-in-use"></a><span data-ttu-id="908ce-186">Dati in uso</span><span class="sxs-lookup"><span data-stu-id="908ce-186">Data in use</span></span>

<span data-ttu-id="908ce-187">La crittografia dei dati in uso implica la protezione dei dati in una risorsa di archiviazione non permanente, come la RAM o la cache della CPU,</span><span class="sxs-lookup"><span data-stu-id="908ce-187">Encryption for data in use involves securing data in nonpersistent storage, such as RAM or CPU caches.</span></span> <span data-ttu-id="908ce-188">oltre all'uso di tecnologie come la crittografia della memoria completa o di tecnologie enclave come Secure Guard Extensions (SGX) di Intel.</span><span class="sxs-lookup"><span data-stu-id="908ce-188">Use of technologies such as full memory encryption, enclave technologies, such as Intel's Secure Guard Extensions (SGX).</span></span> <span data-ttu-id="908ce-189">Sono incluse anche tecniche di crittografia come la crittografia omomorfica, che può essere usata per creare ambienti di esecuzione sicuri e affidabili.</span><span class="sxs-lookup"><span data-stu-id="908ce-189">This also includes cryptographic techniques, such as homomorphic encryption that can be used to create secure, trusted execution environments.</span></span>

<span data-ttu-id="908ce-190">**Presupposti relativi alla crittografia dei dati in uso**.</span><span class="sxs-lookup"><span data-stu-id="908ce-190">**Encrypting data in use assumptions**.</span></span> <span data-ttu-id="908ce-191">La crittografia dei dati in uso presuppone le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="908ce-191">Encrypting data in use assumes the following:</span></span>

- <span data-ttu-id="908ce-192">La proprietà dei dati deve essere sempre mantenuta separata dalla piattaforma cloud sottostante, anche a livello di RAM e CPU.</span><span class="sxs-lookup"><span data-stu-id="908ce-192">You are required to maintain data ownership separate from the underlying cloud platform at all times, even at the RAM and CPU level.</span></span>

## <a name="learn-more"></a><span data-ttu-id="908ce-193">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="908ce-193">Learn more</span></span>

<span data-ttu-id="908ce-194">Vedere i collegamenti seguenti per altre informazioni sulla crittografia e la gestione delle chiavi nella piattaforma Azure.</span><span class="sxs-lookup"><span data-stu-id="908ce-194">See the following for more information about encryption and key management in the Azure platform.</span></span>

- <span data-ttu-id="908ce-195">[Panoramica della crittografia di Azure](/azure/security/security-azure-encryption-overview).</span><span class="sxs-lookup"><span data-stu-id="908ce-195">[Azure encryption overview](/azure/security/security-azure-encryption-overview).</span></span> <span data-ttu-id="908ce-196">Descrizione dettagliata del modo in cui Azure usa la crittografia per proteggere i dati inattivi e i dati in transito.</span><span class="sxs-lookup"><span data-stu-id="908ce-196">A detailed description of how Azure uses encryption to secure both data at rest and data in transit.</span></span>
- <span data-ttu-id="908ce-197">[Azure Key Vault](/azure/key-vault/key-vault-overview).</span><span class="sxs-lookup"><span data-stu-id="908ce-197">[Azure Key Vault](/azure/key-vault/key-vault-overview).</span></span> <span data-ttu-id="908ce-198">Key Vault è il principale sistema di gestione delle chiavi per l'archiviazione e la gestione di chiavi, segreti e certificati di crittografia in Azure.</span><span class="sxs-lookup"><span data-stu-id="908ce-198">Key Vault is the primary key management system for storing and managing cryptographic keys, secrets, and certificates within Azure.</span></span>
- <span data-ttu-id="908ce-199">[Confidential computing in Azure](/solutions/confidential-compute).</span><span class="sxs-lookup"><span data-stu-id="908ce-199">[Confidential computing in Azure](/solutions/confidential-compute).</span></span> <span data-ttu-id="908ce-200">L'iniziativa Confidential computing di Azure offre strumenti e tecnologie per creare ambienti di esecuzione affidabili o altri meccanismi di crittografia per la protezione dei dati in uso.</span><span class="sxs-lookup"><span data-stu-id="908ce-200">Azure's confidential computing initiative provides tools and technology to create trusted execution environments or other encryption mechanisms to secure data in use.</span></span>

## <a name="next-steps"></a><span data-ttu-id="908ce-201">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="908ce-201">Next steps</span></span>

<span data-ttu-id="908ce-202">Informazioni su come le reti definite dal software forniscono funzionalità di rete virtuale per le distribuzioni cloud.</span><span class="sxs-lookup"><span data-stu-id="908ce-202">Learn how Software Defined Networks provide virtualized networking capabilities for cloud deployments.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="908ce-203">Qual è il modello di rete definita dal software migliore per la propria distribuzione?</span><span class="sxs-lookup"><span data-stu-id="908ce-203">Which Software Defined Network pattern is best for my deployment?</span></span>](../software-defined-network/overview.md)