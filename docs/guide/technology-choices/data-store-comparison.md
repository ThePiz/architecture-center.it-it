---
title: Criteri per la scelta di un archivio dati
description: Panoramica delle opzioni di calcolo di Azure
author: MikeWasson
ms.openlocfilehash: 7fb75cd334438c5b985fa04ad8afe3236f2391f8
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="criteria-for-choosing-a-data-store"></a><span data-ttu-id="b4b6e-103">Criteri per la scelta di un archivio dati</span><span class="sxs-lookup"><span data-stu-id="b4b6e-103">Criteria for choosing a data store</span></span>

<span data-ttu-id="b4b6e-104">Azure supporta molti tipi di soluzioni di archiviazione dei dati, ognuna delle quali offre funzionalità e capacità diverse.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-104">Azure supports many types of data storage solutions, each providing different features and capabilities.</span></span> <span data-ttu-id="b4b6e-105">Questo articolo descrive i criteri di confronto che è consigliabile usare per la valutazione di un archivio dati.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-105">This article describes the comparison criteria you should use when evaluating a data store.</span></span> <span data-ttu-id="b4b6e-106">L'obiettivo è facilitare l'individuazione dei tipi di archiviazione dati in grado di soddisfare i requisiti della soluzione.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-106">The goal is to help you determine which data storage types can meet your solution's requirements.</span></span>

## <a name="general-considerations"></a><span data-ttu-id="b4b6e-107">Considerazioni generali</span><span class="sxs-lookup"><span data-stu-id="b4b6e-107">General Considerations</span></span>

<span data-ttu-id="b4b6e-108">Per avviare il confronto, raccogliere il maggior numero possibile delle informazioni seguenti rispetto alle proprie esigenze in termini di dati.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-108">To start your comparison, gather as much of the following information as you can about your data needs.</span></span> <span data-ttu-id="b4b6e-109">Queste informazioni consentiranno di determinare quali tipi di archivi dati possono soddisfare le proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-109">This information will help you to determine which data storage types will meet your needs.</span></span>

### <a name="functional-requirements"></a><span data-ttu-id="b4b6e-110">Requisiti funzionali</span><span class="sxs-lookup"><span data-stu-id="b4b6e-110">Functional requirements</span></span>

- <span data-ttu-id="b4b6e-111">**Formato dati**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-111">**Data format**.</span></span> <span data-ttu-id="b4b6e-112">Che tipo di dati si intende archiviare?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-112">What type of data are you intending to store?</span></span> <span data-ttu-id="b4b6e-113">I tipi comuni includono dati transazionali, oggetti JSON, dati di telemetria, indici di ricerca o file flat.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-113">Common types include transactional data, JSON objects, telemetry, search indexes, or flat files.</span></span>
- <span data-ttu-id="b4b6e-114">**Dimensioni dei dati**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-114">**Data size**.</span></span> <span data-ttu-id="b4b6e-115">Quali sono le dimensioni delle entità che occorre archiviare?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-115">How large are the entities you need to store?</span></span> <span data-ttu-id="b4b6e-116">Queste entità dovranno essere gestite come un singolo documento o possono essere suddivise tra più documenti, tabelle, raccolte e così via?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-116">Will these entities need to be maintained as a single document, or can they be split across multiple documents, tables, collections, and so forth?</span></span>
- <span data-ttu-id="b4b6e-117">**Scala e struttura**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-117">**Scale and structure**.</span></span> <span data-ttu-id="b4b6e-118">Qual è la quantità totale di capacità di archiviazione necessaria?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-118">What is the overall amount of storage capacity you need?</span></span> <span data-ttu-id="b4b6e-119">Si prevede il partizionamento dei dati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-119">Do you anticipate partitioning your data?</span></span> 
- <span data-ttu-id="b4b6e-120">**Relazioni tra i dati**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-120">**Data relationships**.</span></span> <span data-ttu-id="b4b6e-121">I dati dovranno supportare relazioni uno-a-molti o molti-a-molti?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-121">Will your data need to support one-to-many or many-to-many relationships?</span></span> <span data-ttu-id="b4b6e-122">Le relazioni stesse rappresentano una parte importante dei dati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-122">Are relationships themselves an important part of the data?</span></span> <span data-ttu-id="b4b6e-123">Sarà necessario creare join o combinare in altri modi i dati all'interno dello stesso set o provenienti da set di dati esterni?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-123">Will you need to join or otherwise combine data from within the same dataset, or from external datasets?</span></span> 
- <span data-ttu-id="b4b6e-124">**Modello di coerenza**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-124">**Consistency model**.</span></span> <span data-ttu-id="b4b6e-125">Quanto è importante che gli aggiornamenti eseguiti in un nodo compaiano negli altri nodi prima che sia possibile apportare altre modifiche?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-125">How important is it for updates made in one node to appear in other nodes, before further changes can be made?</span></span> <span data-ttu-id="b4b6e-126">È accettabile la coerenza finale?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-126">Can you accept eventual consistency?</span></span> <span data-ttu-id="b4b6e-127">Sono necessarie garanzie ACID per le transazioni?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-127">Do you need ACID guarantees for transactions?</span></span>
- <span data-ttu-id="b4b6e-128">**Flessibilità dello schema**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-128">**Schema flexibility**.</span></span> <span data-ttu-id="b4b6e-129">Che tipo di schemi verranno applicati ai dati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-129">What kind of schemas will you apply to your data?</span></span> <span data-ttu-id="b4b6e-130">Si userà uno schema fisso, un approccio di schema in scrittura o un approccio di schema in lettura?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-130">Will you use a fixed schema, a schema-on-write approach, or a schema-on-read approach?</span></span>
- <span data-ttu-id="b4b6e-131">**Concorrenza**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-131">**Concurrency**.</span></span> <span data-ttu-id="b4b6e-132">Che tipo di meccanismo di concorrenza si vuole usare per l'aggiornamento e la sincronizzazione dei dati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-132">What kind of concurrency mechanism do you want to use when updating and synchronizing data?</span></span> <span data-ttu-id="b4b6e-133">L'applicazione eseguirà numerosi aggiornamenti che potrebbero essere potenzialmente in conflitto?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-133">Will the application perform many updates that could potentially conflict.</span></span> <span data-ttu-id="b4b6e-134">In questo caso, potrebbero essere necessari il blocco dei record e il controllo della concorrenza pessimistica.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-134">If so, you may requiring record locking and pessimistic concurrency control.</span></span> <span data-ttu-id="b4b6e-135">In alternativa, si possono supportare controlli di concorrenza ottimistica?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-135">Alternatively, can you support optimistic concurrency controls?</span></span> <span data-ttu-id="b4b6e-136">In questo caso, è sufficiente il semplice controllo della concorrenza basato su timestamp o è necessaria la funzionalità aggiuntiva di controllo della concorrenza per più versioni?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-136">If so, is simple timestamp-based concurrency control enough, or do you need the added functionality of multi-version concurrency control?</span></span>
- <span data-ttu-id="b4b6e-137">**Spostamento dei dati**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-137">**Data movement**.</span></span> <span data-ttu-id="b4b6e-138">La soluzione dovrà eseguire operazioni di estrazione, trasformazione e caricamento per spostare i dati in altri archivi o data warehouse?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-138">Will your solution need to perform ETL tasks to move data to other stores or data warehouses?</span></span>
- <span data-ttu-id="b4b6e-139">**Ciclo di vita dei dati**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-139">**Data lifecycle**.</span></span> <span data-ttu-id="b4b6e-140">I dati sono di tipo WORM?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-140">Is the data write-once, read-many?</span></span> <span data-ttu-id="b4b6e-141">Possono essere spostati in un'archiviazione offline sicura o ad accesso sporadico?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-141">Can it be moved into cool or cold storage?</span></span>
- <span data-ttu-id="b4b6e-142">**Altre funzionalità supportate**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-142">**Other supported features**.</span></span> <span data-ttu-id="b4b6e-143">Sono necessarie altre funzionalità specifiche, ad esempio convalida dello schema, aggregazione, indicizzazione, ricerca full-text, MapReduce o altre funzionalità di query?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-143">Do you need any other specific features, such as schema validation, aggregation, indexing, full-text search, MapReduce, or other query capabilities?</span></span>

### <a name="non-functional-requirements"></a><span data-ttu-id="b4b6e-144">Requisiti non funzionali</span><span class="sxs-lookup"><span data-stu-id="b4b6e-144">Non-functional requirements</span></span>

- <span data-ttu-id="b4b6e-145">**Prestazioni e scalabilità**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-145">**Performance and scalability**.</span></span> <span data-ttu-id="b4b6e-146">Quali sono i requisiti di prestazioni dei dati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-146">What are your data performance requirements?</span></span> <span data-ttu-id="b4b6e-147">Si hanno requisiti specifici relativi alla frequenza di inserimento e alla velocità di elaborazione dei dati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-147">Do you have specific requirements for data ingestion rates and data processing rates?</span></span> <span data-ttu-id="b4b6e-148">Quali sono i tempi di risposta accettabili per l'esecuzione di query e l'aggregazione dei dati dopo l'inserimento?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-148">What are the acceptable response times for querying and aggregation of data once ingested?</span></span> <span data-ttu-id="b4b6e-149">Di quanto dovranno aumentare le prestazioni dell'archivio dati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-149">How large will you need the data store to scale up?</span></span> <span data-ttu-id="b4b6e-150">Il carico di lavoro è a elevato utilizzo delle risorse di scrittura o a elevato utilizzo delle risorse di lettura?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-150">Is your workload more read-heavy or write-heavy?</span></span>
- <span data-ttu-id="b4b6e-151">**Affidabilità**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-151">**Reliability**.</span></span> <span data-ttu-id="b4b6e-152">Quale contratto di servizio complessivo è necessario supportare?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-152">What overall SLA do you need to support?</span></span> <span data-ttu-id="b4b6e-153">Che livello di tolleranza di errore è necessario fornire per i consumer di dati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-153">What level of fault-tolerance do you need to provide for data consumers?</span></span> <span data-ttu-id="b4b6e-154">Quali tipi di funzionalità di backup e ripristino sono necessari?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-154">What kind of backup and restore capabilities do you need?</span></span> 
- <span data-ttu-id="b4b6e-155">**Replica**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-155">**Replication**.</span></span> <span data-ttu-id="b4b6e-156">I dati dovranno essere distribuiti tra più repliche o aree?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-156">Will your data need to be distributed among multiple replicas or regions?</span></span> <span data-ttu-id="b4b6e-157">Quali tipi di funzionalità di replica dei dati sono necessari?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-157">What kind of data replication capabilities do you require?</span></span> 
- <span data-ttu-id="b4b6e-158">**Limiti**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-158">**Limits**.</span></span> <span data-ttu-id="b4b6e-159">I limiti di un particolare archivio dati supporteranno i requisiti di scalabilità, numero di connessioni e velocità effettiva?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-159">Will the limits of a particular data store support your requirements for scale, number of connections, and throughput?</span></span> 

### <a name="management-and-cost"></a><span data-ttu-id="b4b6e-160">Gestione e costi</span><span class="sxs-lookup"><span data-stu-id="b4b6e-160">Management and cost</span></span>

- <span data-ttu-id="b4b6e-161">**Servizio gestito**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-161">**Managed service**.</span></span> <span data-ttu-id="b4b6e-162">Quando possibile, usare un servizio dati gestito, a meno che non siano necessarie funzionalità specifiche disponibili solo in un archivio dati basato su IaaS.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-162">When possible, use a managed data service, unless you require specific capabilities that can only be found in an IaaS-hosted data store.</span></span>
- <span data-ttu-id="b4b6e-163">**Disponibilità in base all'area geografica**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-163">**Region availability**.</span></span> <span data-ttu-id="b4b6e-164">Per i servizi gestiti, il servizio è disponibile in tutte le aree di Azure?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-164">For managed services, is the service available in all Azure regions?</span></span> <span data-ttu-id="b4b6e-165">La soluzione deve essere ospitata in specifiche aree di Azure?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-165">Does your solution need to be hosted in certain Azure regions?</span></span>
- <span data-ttu-id="b4b6e-166">**Portabilità**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-166">**Portability**.</span></span> <span data-ttu-id="b4b6e-167">Sarà necessario eseguire la migrazione dei dati in locale, in data center esterni o in altri ambienti di hosting cloud?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-167">Will your data need to migrated to on-premises, external datacenters, or other cloud hosting environments?</span></span>
- <span data-ttu-id="b4b6e-168">**Licenze**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-168">**Licensing**.</span></span> <span data-ttu-id="b4b6e-169">Si hanno preferenze tra tipo di licenza OSS o proprietaria?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-169">Do you have a preference of a proprietary versus OSS license type?</span></span> <span data-ttu-id="b4b6e-170">Esistono altre restrizioni esterne rispetto al tipo di licenza che è possibile usare?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-170">Are there any other external restrictions on what type of license you can use?</span></span>
- <span data-ttu-id="b4b6e-171">**Costo complessivo**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-171">**Overall cost**.</span></span> <span data-ttu-id="b4b6e-172">Qual è il costo complessivo dell'uso del servizio all'interno della soluzione?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-172">What is the overall cost of using the service within your solution?</span></span> <span data-ttu-id="b4b6e-173">Quante istanze sarà necessario eseguire per supportare i requisiti di velocità effettiva e tempo di attività?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-173">How many instances will need to run, to support your uptime and throughput requirements?</span></span> <span data-ttu-id="b4b6e-174">Considerare in questo calcolo i costi delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-174">Consider operations costs in this calculation.</span></span> <span data-ttu-id="b4b6e-175">Uno dei motivi per preferire i servizi gestiti è la riduzione dei costi operativi.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-175">One reason to prefer managed services is the reduced operational cost.</span></span>
- <span data-ttu-id="b4b6e-176">**Convenienza**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-176">**Cost effectiveness**.</span></span> <span data-ttu-id="b4b6e-177">È possibile partizionare i dati per archiviarli in modo più efficiente sotto il profilo dei costi?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-177">Can you partition your data, to store it more cost effectively?</span></span> <span data-ttu-id="b4b6e-178">Ad esempio, è possibile spostare gli oggetti di grandi dimensioni da un costoso database relazionale a un archivio di oggetti?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-178">For example, can you move large objects out of an expensive relational database into an object store?</span></span>

### <a name="security"></a><span data-ttu-id="b4b6e-179">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="b4b6e-179">Security</span></span>

- <span data-ttu-id="b4b6e-180">**Sicurezza**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-180">**Security**.</span></span> <span data-ttu-id="b4b6e-181">Che tipo di crittografia occorre implementare?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-181">What type of encryption do you require?</span></span> <span data-ttu-id="b4b6e-182">È necessaria la crittografia dei dati inattivi?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-182">Do you need encryption at rest?</span></span> <span data-ttu-id="b4b6e-183">Che tipo di meccanismo di autenticazione si vuole usare per la connessione ai dati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-183">What authentication mechanism do you want to use to connect to your data?</span></span>
- <span data-ttu-id="b4b6e-184">**Controllo**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-184">**Auditing**.</span></span> <span data-ttu-id="b4b6e-185">Che tipo di log di controllo è necessario generare?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-185">What kind of audit log do you need to generate?</span></span>
- <span data-ttu-id="b4b6e-186">**Requisiti di rete**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-186">**Networking requirements**.</span></span> <span data-ttu-id="b4b6e-187">È necessario limitare o gestire in altro modo l'accesso ai dati da altre risorse di rete?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-187">Do you need to restrict or otherwise manage access to your data from other network resources?</span></span> <span data-ttu-id="b4b6e-188">I dati devono essere accessibili solo dall'interno dell'ambiente Azure?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-188">Does data need to be accessible only from inside the Azure environment?</span></span> <span data-ttu-id="b4b6e-189">I dati devono essere accessibili da specifici indirizzi IP o subnet?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-189">Does the data need to be accessible from specific IP addresses or subnets?</span></span> <span data-ttu-id="b4b6e-190">Devono essere accessibili da applicazioni o servizi ospitati in locale o in altri data center esterni?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-190">Does it need to be accessible from applications or services hosted on-premises or in other external datacenters?</span></span>

### <a name="devops"></a><span data-ttu-id="b4b6e-191">DevOps</span><span class="sxs-lookup"><span data-stu-id="b4b6e-191">DevOps</span></span>

- <span data-ttu-id="b4b6e-192">**Competenze**.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-192">**Skill set**.</span></span> <span data-ttu-id="b4b6e-193">Esistono specifici linguaggi di programmazione, sistemi operativi o altre tecnologie con cui il team ha particolare familiarità?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-193">Are there particular programming languages, operating systems, or other technology that your team is particularly adept at using?</span></span> <span data-ttu-id="b4b6e-194">Ne esistono altri che sarebbe difficile utilizzare per il team?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-194">Are there others that would be difficult for your team to work with?</span></span>
- <span data-ttu-id="b4b6e-195">**Client**. È disponibile un supporto client ottimale per i linguaggi di sviluppo usati?</span><span class="sxs-lookup"><span data-stu-id="b4b6e-195">**Clients** Is there good client support for your development languages?</span></span>

<span data-ttu-id="b4b6e-196">Le sezioni seguenti presentano un confronto dei vari modelli di archivio dati in termini di profilo del carico di lavoro, tipi di dati e casi d'uso di esempio.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-196">The following sections compare various data store models in terms of workload profile, data types, and example use cases.</span></span>

## <a name="relational-database-management-systems-rdbms"></a><span data-ttu-id="b4b6e-197">Sistemi di gestione di database relazionali (RDBMS)</span><span class="sxs-lookup"><span data-stu-id="b4b6e-197">Relational database management systems (RDBMS)</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-198">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-198">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-199">La creazione di nuovi record e l'aggiornamento dei dati esistenti avvengono regolarmente.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-199">Both the creation of new records and updates to existing data happen regularly.</span></span></li>
            <li><span data-ttu-id="b4b6e-200">Occorre completare più operazioni in una singola transazione.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-200">Multiple operations have to be completed in a single transaction.</span></span></li>
            <li><span data-ttu-id="b4b6e-201">Richiede funzioni di aggregazione per eseguire la tabulazione incrociata.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-201">Requires aggregation functions to perform cross-tabulation.</span></span></li>
            <li><span data-ttu-id="b4b6e-202">È richiesta la stretta integrazione con gli strumenti di reporting.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-202">Strong integration with reporting tools is required.</span></span></li>
            <li><span data-ttu-id="b4b6e-203">Le relazioni vengono applicate usando i vincoli del database.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-203">Relationships are enforced using database constraints.</span></span></li>
            <li><span data-ttu-id="b4b6e-204">Per ottimizzare le prestazioni delle query vengono usati gli indici.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-204">Indexes are used to optimize query performance.</span></span></li>
            <li><span data-ttu-id="b4b6e-205">Consente di accedere a subset di dati specifici.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-205">Allows access to specific subsets of data.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-206">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-206">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-207">I dati sono altamente normalizzati.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-207">Data is highly normalized.</span></span></li>
            <li><span data-ttu-id="b4b6e-208">Sono necessari e vengono applicati schemi del database.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-208">Database schemas are required and enforced.</span></span></li>
            <li><span data-ttu-id="b4b6e-209">Relazioni molti-a-molti tra le entità di dati nel database.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-209">Many-to-many relationships between data entities in the database.</span></span></li>
            <li><span data-ttu-id="b4b6e-210">Nello schema sono definiti vincoli imposti a tutti i dati nel database.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-210">Constraints are defined in the schema and imposed on any data in the database.</span></span></li>
            <li><span data-ttu-id="b4b6e-211">I dati richiedono integrità elevata.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-211">Data requires high integrity.</span></span> <span data-ttu-id="b4b6e-212">Gli indici e le relazioni devono essere gestiti in modo accurato.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-212">Indexes and relationships need to be maintained accurately.</span></span></li>
            <li><span data-ttu-id="b4b6e-213">I dati richiedono la coerenza assoluta.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-213">Data requires strong consistency.</span></span> <span data-ttu-id="b4b6e-214">Le transazioni funzionano in modo da garantire che tutti i dati siano coerenti al 100% per tutti gli utenti e i processi.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-214">Transactions operate in a way that ensures all data are 100% consistent for all users and processes.</span></span></li>
            <li><span data-ttu-id="b4b6e-215">È previsto che le singole immissioni di dati siano di piccole o medie dimensioni.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-215">Size of individual data entries is intended to be small to medium-sized.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-216">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-216">**Examples**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-217">Line-of-business (gestione risorse umane, CRM, ERP)</span><span class="sxs-lookup"><span data-stu-id="b4b6e-217">Line of business  (human capital management, customer relationship management, enterprise resource planning)</span></span></li>
            <li><span data-ttu-id="b4b6e-218">Gestione dell'inventario</span><span class="sxs-lookup"><span data-stu-id="b4b6e-218">Inventory management</span></span></li>
            <li><span data-ttu-id="b4b6e-219">Database di report</span><span class="sxs-lookup"><span data-stu-id="b4b6e-219">Reporting database</span></span></li>
            <li><span data-ttu-id="b4b6e-220">Contabilità</span><span class="sxs-lookup"><span data-stu-id="b4b6e-220">Accounting</span></span></li>
            <li><span data-ttu-id="b4b6e-221">Gestione degli asset</span><span class="sxs-lookup"><span data-stu-id="b4b6e-221">Asset management</span></span></li>
            <li><span data-ttu-id="b4b6e-222">Gestione dei fondi</span><span class="sxs-lookup"><span data-stu-id="b4b6e-222">Fund management</span></span></li>
            <li><span data-ttu-id="b4b6e-223">Gestione degli ordini</span><span class="sxs-lookup"><span data-stu-id="b4b6e-223">Order management</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="document-databases"></a><span data-ttu-id="b4b6e-224">Database di documenti</span><span class="sxs-lookup"><span data-stu-id="b4b6e-224">Document databases</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-225">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-225">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-226">Utilizzo generico.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-226">General purpose.</span></span></li>
            <li><span data-ttu-id="b4b6e-227">Le operazioni di inserimento e aggiornamento sono comuni.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-227">Insert and update operations are common.</span></span> <span data-ttu-id="b4b6e-228">La creazione di nuovi record e l'aggiornamento dei dati esistenti avvengono regolarmente.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-228">Both the creation of new records and updates to existing data happen regularly.</span></span></li>
            <li><span data-ttu-id="b4b6e-229">Nessun mancata corrispondenza dell'impedenza tra modello a oggetti e relazionale.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-229">No object-relational impedance mismatch.</span></span> <span data-ttu-id="b4b6e-230">I documenti possono offrire una corrispondenza migliore con le strutture di oggetti usate nel codice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-230">Documents can better match the object structures used in application code.</span></span></li>
            <li><span data-ttu-id="b4b6e-231">Viene usata comunemente la concorrenza ottimistica.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-231">Optimistic concurrency is more commonly used.</span></span></li>
            <li><span data-ttu-id="b4b6e-232">I dati devono essere modificati ed elaborati dall'applicazione che li utilizza.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-232">Data must be modified and processed by consuming application.</span></span></li>
            <li><span data-ttu-id="b4b6e-233">I dati richiedono un indice in più campi.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-233">Data requires index on multiple fields.</span></span></li>
            <li><span data-ttu-id="b4b6e-234">I singoli documenti vengono recuperati e scritti come un unico blocco.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-234">Individual documents are retrieved and written as a single block.</span></span></li>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-235">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-235">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-236">I dati possono essere gestiti in modo denormalizzato.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-236">Data can be managed in de-normalized way.</span></span></li>
            <li><span data-ttu-id="b4b6e-237">Le dimensioni dei dati dei singoli documenti sono relativamente ridotte.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-237">Size of individual document data is relatively small.</span></span></li>
            <li><span data-ttu-id="b4b6e-238">Ogni tipo di documento può usare uno schema proprio.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-238">Each document type can use its own schema.</span></span></li>
            <li><span data-ttu-id="b4b6e-239">I documenti possono contenere campi facoltativi.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-239">Documents can include optional fields.</span></span></li>
            <li><span data-ttu-id="b4b6e-240">I dati dei documenti sono semistrutturati, vale a dire che i tipi di dati di ogni campo non sono definiti in modo rigido.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-240">Document data is semi-structured, meaning that data types of each field are not strictly defined.</span></span></li>
            <li><span data-ttu-id="b4b6e-241">L'aggregazione dei dati è supportata.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-241">Data aggregation is supported.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-242">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-242">**Examples**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-243">Catalogo prodotti</span><span class="sxs-lookup"><span data-stu-id="b4b6e-243">Product catalog</span></span></li>
            <li><span data-ttu-id="b4b6e-244">Account utente</span><span class="sxs-lookup"><span data-stu-id="b4b6e-244">User accounts</span></span></li>
            <li><span data-ttu-id="b4b6e-245">Distinta base</span><span class="sxs-lookup"><span data-stu-id="b4b6e-245">Bill of materials</span></span></li>
            <li><span data-ttu-id="b4b6e-246">Personalizzazione</span><span class="sxs-lookup"><span data-stu-id="b4b6e-246">Personalization</span></span></li>
            <li><span data-ttu-id="b4b6e-247">Gestione del contenuto</span><span class="sxs-lookup"><span data-stu-id="b4b6e-247">Content management</span></span></li>
            <li><span data-ttu-id="b4b6e-248">Dati delle operazioni</span><span class="sxs-lookup"><span data-stu-id="b4b6e-248">Operations data</span></span></li>
            <li><span data-ttu-id="b4b6e-249">Gestione dell'inventario</span><span class="sxs-lookup"><span data-stu-id="b4b6e-249">Inventory management</span></span></li>
            <li><span data-ttu-id="b4b6e-250">Dati di cronologia delle transazioni</span><span class="sxs-lookup"><span data-stu-id="b4b6e-250">Transaction history data</span></span></li>
            <li><span data-ttu-id="b4b6e-251">Vista materializzata di altri archivi NoSQL.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-251">Materialized view of other NoSQL stores.</span></span> <span data-ttu-id="b4b6e-252">Sostituisce l'indicizzazione file/BLOB.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-252">Replaces file/BLOB indexing.</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="keyvalue-stores"></a><span data-ttu-id="b4b6e-253">Archivi chiave-valore</span><span class="sxs-lookup"><span data-stu-id="b4b6e-253">Key/value stores</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-254">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-254">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-255">I dati sono identificabili e accessibili tramite un'unica chiave ID, come un dizionario.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-255">Data is identified and accessed using a single ID key, like a dictionary.</span></span></li>
            <li><span data-ttu-id="b4b6e-256">Scalabilità elevata.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-256">Massively scalable.</span></span></li>
            <li><span data-ttu-id="b4b6e-257">Non sono necessari join, blocchi o unioni.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-257">No joins, lock, or unions are required.</span></span></li>
            <li><span data-ttu-id="b4b6e-258">Non vengono usati meccanismi di aggregazione.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-258">No aggregation mechanisms are used.</span></span></li>
            <li><span data-ttu-id="b4b6e-259">In genere non vengono usati indici secondari.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-259">Secondary indexes are generally not used.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-260">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-260">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-261">I dati tendono a essere di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-261">Data size tends to be large.</span></span></li>
            <li><span data-ttu-id="b4b6e-262">Ogni chiave è associata a un singolo valore, ovvero un BLOB di dati non gestiti.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-262">Each key is associated with a single value, which is an unmanaged data BLOB.</span></span></li>
            <li><span data-ttu-id="b4b6e-263">Non viene applicata alcuna imposizione dello schema.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-263">There is no schema enforcement.</span></span></li>
            <li><span data-ttu-id="b4b6e-264">Non esistono relazioni tra le entità.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-264">No relationships between entities.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-265">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-265">**Examples**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-266">Memorizzazione dei dati nella cache</span><span class="sxs-lookup"><span data-stu-id="b4b6e-266">Data caching</span></span></li>
            <li><span data-ttu-id="b4b6e-267">Gestione delle sessioni</span><span class="sxs-lookup"><span data-stu-id="b4b6e-267">Session management</span></span></li>
            <li><span data-ttu-id="b4b6e-268">Gestione profilo e preferenze utente</span><span class="sxs-lookup"><span data-stu-id="b4b6e-268">User preference and profile management</span></span></li>
            <li><span data-ttu-id="b4b6e-269">Raccomandazione prodotti e visualizzazione annunci</span><span class="sxs-lookup"><span data-stu-id="b4b6e-269">Product recommendation and ad serving</span></span></li>
            <li><span data-ttu-id="b4b6e-270">Dizionari</span><span class="sxs-lookup"><span data-stu-id="b4b6e-270">Dictionaries</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="graph-databases"></a><span data-ttu-id="b4b6e-271">Database di grafi</span><span class="sxs-lookup"><span data-stu-id="b4b6e-271">Graph databases</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-272">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-272">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-273">Le relazioni tra gli elementi di dati sono molto complesse e prevedono molti hop tra gli elementi di dati correlati.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-273">The relationships between data items are very complex, involving many hops between related data items.</span></span></li>
            <li><span data-ttu-id="b4b6e-274">Le relazioni tra gli elementi di dati sono dinamiche e cambiano nel tempo.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-274">The relationship between data items are dynamic and change over time.</span></span></li>
            <li><span data-ttu-id="b4b6e-275">Le relazioni tra oggetti sono entità di prima classe e non richiedono chiavi esterne e join da attraversare.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-275">Relationships between objects are first-class citizens, without requiring foreign-keys and joins to traverse.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-276">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-276">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-277">I dati sono costituiti da nodi e relazioni.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-277">Data is comprised of nodes and relationships.</span></span></li>
            <li><span data-ttu-id="b4b6e-278">I nodi sono simili a righe di tabella o documenti JSON.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-278">Nodes are similar to table rows or JSON documents.</span></span></li>
            <li><span data-ttu-id="b4b6e-279">Le relazioni sono importanti quanto i nodi e sono esposte direttamente nel linguaggio di query.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-279">Relationships are just as important as nodes, and are exposed directly in the query language.</span></span></li>
            <li><span data-ttu-id="b4b6e-280">Gli oggetti compositi, ad esempio una persona con più numeri di telefono, tendono a essere suddivisi in nodi separati di dimensioni inferiori, combinati con relazioni attraversabili</span><span class="sxs-lookup"><span data-stu-id="b4b6e-280">Composite objects, such as a person with multiple phone numbers, tend to be broken into separate, smaller nodes, combined with traversable relationships</span></span> </li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-281">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-281">**Examples**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-282">Organigrammi</span><span class="sxs-lookup"><span data-stu-id="b4b6e-282">Organization charts</span></span></li>
            <li><span data-ttu-id="b4b6e-283">Grafi sociali</span><span class="sxs-lookup"><span data-stu-id="b4b6e-283">Social graphs</span></span></li>
            <li><span data-ttu-id="b4b6e-284">Rilevamento delle frodi</span><span class="sxs-lookup"><span data-stu-id="b4b6e-284">Fraud detection</span></span></li>
            <li><span data-ttu-id="b4b6e-285">Analytics</span><span class="sxs-lookup"><span data-stu-id="b4b6e-285">Analytics</span></span></li>
            <li><span data-ttu-id="b4b6e-286">Motore di raccomandazione</span><span class="sxs-lookup"><span data-stu-id="b4b6e-286">Recommendation engines</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="column-family-databases"></a><span data-ttu-id="b4b6e-287">Database a colonne</span><span class="sxs-lookup"><span data-stu-id="b4b6e-287">Column-family databases</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-288">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-288">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-289">La maggior parte dei database a colonne esegue le operazioni di scrittura molto rapidamente.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-289">Most column-family databases perform write operations extremely quickly.</span></span></li>
            <li><span data-ttu-id="b4b6e-290">Le operazioni di aggiornamento ed eliminazione sono rare.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-290">Update and delete operations are rare.</span></span></li>
            <li><span data-ttu-id="b4b6e-291">Progettato per offrire accesso a bassa latenza e velocità effettiva elevata.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-291">Designed to provide high throughput and low-latency access.</span></span></li>
            <li><span data-ttu-id="b4b6e-292">Supporta l'accesso query semplice a un particolare set di campi all'interno di un record di dimensioni molto superiori.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-292">Supports easy query access to a particular set of fields within a much larger record.</span></span></li>
            <li><span data-ttu-id="b4b6e-293">Scalabilità elevata.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-293">Massively scalable.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-294">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-294">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-295">I dati vengono archiviati in tabelle composte da una colonna chiave e una o più famiglie di colonne.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-295">Data is stored in tables consisting of a key column and one or more column families.</span></span></li>
            <li><span data-ttu-id="b4b6e-296">Le specifiche colonne possono variare per singole righe.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-296">Specific columns can vary by individual rows.</span></span></li>
            <li><span data-ttu-id="b4b6e-297">Le singole celle sono accessibili tramite comandi Get e Put</span><span class="sxs-lookup"><span data-stu-id="b4b6e-297">Individual cells are accessed via get and put commands</span></span></li>
            <li><span data-ttu-id="b4b6e-298">Usando un comando di analisi vengono restituite più righe.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-298">Multiple rows are returned using a scan command.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-299">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-299">**Examples**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-300">Raccomandazioni</span><span class="sxs-lookup"><span data-stu-id="b4b6e-300">Recommendations</span></span></li>
            <li><span data-ttu-id="b4b6e-301">Personalizzazione</span><span class="sxs-lookup"><span data-stu-id="b4b6e-301">Personalization</span></span></li>
            <li><span data-ttu-id="b4b6e-302">Dati di sensori</span><span class="sxs-lookup"><span data-stu-id="b4b6e-302">Sensor data</span></span></li>
            <li><span data-ttu-id="b4b6e-303">Telemetria</span><span class="sxs-lookup"><span data-stu-id="b4b6e-303">Telemetry</span></span></li>
            <li><span data-ttu-id="b4b6e-304">Messaggistica</span><span class="sxs-lookup"><span data-stu-id="b4b6e-304">Messaging</span></span></li>
            <li><span data-ttu-id="b4b6e-305">Analisi dei social media</span><span class="sxs-lookup"><span data-stu-id="b4b6e-305">Social media analytics</span></span></li>
            <li><span data-ttu-id="b4b6e-306">Web analytics</span><span class="sxs-lookup"><span data-stu-id="b4b6e-306">Web analytics</span></span></li>
            <li><span data-ttu-id="b4b6e-307">Monitoraggio attività</span><span class="sxs-lookup"><span data-stu-id="b4b6e-307">Activity monitoring</span></span></li>
            <li><span data-ttu-id="b4b6e-308">Meteo e altri dati di serie temporali</span><span class="sxs-lookup"><span data-stu-id="b4b6e-308">Weather and other time-series data</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="search-engine-databases"></a><span data-ttu-id="b4b6e-309">Database di motori di ricerca</span><span class="sxs-lookup"><span data-stu-id="b4b6e-309">Search engine databases</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-310">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-310">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-311">Indicizzazione dei dati da più origini e servizi.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-311">Indexing data from multiple sources and services.</span></span></li>
            <li><span data-ttu-id="b4b6e-312">Le query sono ad hoc e possono essere complesse.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-312">Queries are ad-hoc and can be complex.</span></span></li>
            <li><span data-ttu-id="b4b6e-313">Richiede l'aggregazione.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-313">Requires aggregation.</span></span></li>
            <li><span data-ttu-id="b4b6e-314">È necessaria la ricerca full-text.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-314">Full text search is required.</span></span></li>
            <li><span data-ttu-id="b4b6e-315">Sono necessarie query self-service ad hoc.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-315">Ad hoc self-service query is required.</span></span></li>
            <li><span data-ttu-id="b4b6e-316">È richiesta l'analisi dei dati con indici in tutti i campi.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-316">Data analysis with index on all fields is required.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-317">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-317">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-318">Semistrutturati o non strutturati</span><span class="sxs-lookup"><span data-stu-id="b4b6e-318">Semi-structured or unstructured</span></span></li>
            <li><span data-ttu-id="b4b6e-319">Text</span><span class="sxs-lookup"><span data-stu-id="b4b6e-319">Text</span></span></li>
            <li><span data-ttu-id="b4b6e-320">Testo con riferimento a dati strutturati</span><span class="sxs-lookup"><span data-stu-id="b4b6e-320">Text with reference to structured data</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-321">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-321">**Examples**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-322">Cataloghi prodotti</span><span class="sxs-lookup"><span data-stu-id="b4b6e-322">Product catalogs</span></span></li>
            <li><span data-ttu-id="b4b6e-323">Ricerca sito</span><span class="sxs-lookup"><span data-stu-id="b4b6e-323">Site search</span></span></li>
            <li><span data-ttu-id="b4b6e-324">Registrazione</span><span class="sxs-lookup"><span data-stu-id="b4b6e-324">Logging</span></span></li>
            <li><span data-ttu-id="b4b6e-325">Analytics</span><span class="sxs-lookup"><span data-stu-id="b4b6e-325">Analytics</span></span></li>
            <li><span data-ttu-id="b4b6e-326">Siti di acquisti</span><span class="sxs-lookup"><span data-stu-id="b4b6e-326">Shopping sites</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="data-warehouse"></a><span data-ttu-id="b4b6e-327">Data warehouse</span><span class="sxs-lookup"><span data-stu-id="b4b6e-327">Data warehouse</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-328">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-328">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-329">Analisi dei dati</span><span class="sxs-lookup"><span data-stu-id="b4b6e-329">Data analytics</span></span></li>
            <li><span data-ttu-id="b4b6e-330">Business intelligence aziendale</span><span class="sxs-lookup"><span data-stu-id="b4b6e-330">Enterprise BI</span></span>   </li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-331">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-331">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-332">Dati cronologici da più origini.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-332">Historical data from multiple sources.</span></span></li>
            <li><span data-ttu-id="b4b6e-333">In genere denormalizzato in uno schema "star" o "snowflake", costituito da tabelle dei fatti e delle dimensioni.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-333">Usually denormalized in a "star" or "snowflake" schema, consisting of fact and dimension tables.</span></span></li>
            <li><span data-ttu-id="b4b6e-334">In genere caricato con nuovi dati in base a una pianificazione.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-334">Usually loaded with new data on a scheduled basis.</span></span></li>
            <li><span data-ttu-id="b4b6e-335">Le tabelle delle dimensioni includono spesso più versioni cronologiche di un'entità, detta *dimensione a modifica lenta*.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-335">Dimension tables often include multiple historic versions of an entity, referred to as a *slowly changing dimension*.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-336">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-336">**Examples**</span></span></td>
    <td><span data-ttu-id="b4b6e-337">Un data warehouse aziendale che fornisce dati per modelli di analisi, report e dashboard.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-337">An enterprise data warehouse that provides data for analytical models, reports, and dashboards.</span></span>
    </td>
</tr>
</table>


## <a name="time-series-databases"></a><span data-ttu-id="b4b6e-338">Database di serie temporali</span><span class="sxs-lookup"><span data-stu-id="b4b6e-338">Time series databases</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-339">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-339">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-340">Le operazioni sono prevalentemente (95-99%) di scrittura.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-340">An overwhelmingly proportion of operations (95-99%) are writes.</span></span></li>
            <li><span data-ttu-id="b4b6e-341">I record vengono in genere aggiunti in sequenza in ordine temporale.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-341">Records are generally appended sequentially in time order.</span></span></li>
            <li><span data-ttu-id="b4b6e-342">Gli aggiornamenti sono rari.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-342">Updates are rare.</span></span></li>
            <li><span data-ttu-id="b4b6e-343">Le eliminazioni vengono eseguite in blocco e in blocchi o record contigui.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-343">Deletes occur in bulk, and are made to contiguous blocks or records.</span></span></li>
            <li><span data-ttu-id="b4b6e-344">Le richieste di lettura possono essere superiori rispetto alla memoria disponibile.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-344">Read requests can be larger than available memory.</span></span></li>
            <li><span data-ttu-id="b4b6e-345">È comune che avvengano più letture simultanee.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-345">It's common for multiple reads to occur simultaneously.</span></span></li>
            <li><span data-ttu-id="b4b6e-346">I dati vengono letti in sequenza in ordine temporale crescente o decrescente.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-346">Data is read sequentially in either ascending or descending time order.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-347">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-347">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-348">Come chiave primaria e meccanismo di ordinamento viene usato un timestamp.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-348">A time stamp that is used as the primary key and sorting mechanism.</span></span></li>
            <li><span data-ttu-id="b4b6e-349">Misurazioni dalla voce o descrizioni di ciò che rappresenta la voce.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-349">Measurements from the entry or descriptions of what the entry represents.</span></span></li>
            <li><span data-ttu-id="b4b6e-350">Tag che definiscono informazioni aggiuntive sul tipo, l'origine e altre informazioni sulla voce.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-350">Tags that define additional information about the type, origin, and other information about the entry.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-351">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-351">**Examples**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-352">Monitoraggio e telemetria eventi.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-352">Monitoring and event telemetry.</span></span></li>
            <li><span data-ttu-id="b4b6e-353">Dati di sensori o altri dati IoT.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-353">Sensor or other IoT data.</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="object-storage"></a><span data-ttu-id="b4b6e-354">Archiviazione di oggetti</span><span class="sxs-lookup"><span data-stu-id="b4b6e-354">Object storage</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-355">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-355">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-356">Identificato dalla chiave.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-356">Identified by key.</span></span></li>
            <li><span data-ttu-id="b4b6e-357">Gli oggetti possono essere accessibili pubblicamente o privatamente.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-357">Objects may be publicly or privately accessible.</span></span></li>
            <li><span data-ttu-id="b4b6e-358">Il contenuto in genere è una risorsa, ad esempio un foglio di calcolo, un'immagine o un file video.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-358">Content is typically an asset such as a spreadsheet, image, or video file.</span></span></li>
            <li><span data-ttu-id="b4b6e-359">Il contenuto deve essere durevole (persistente) ed esterno a qualsiasi livello di applicazione o macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-359">Content must be durable (persistent), and external to any application tier or virtual machine.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-360">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-360">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-361">I dati sono di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-361">Data size is large.</span></span></li>
            <li><span data-ttu-id="b4b6e-362">Dati BLOB.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-362">Blob data.</span></span></li>
            <li><span data-ttu-id="b4b6e-363">Il valore è opaco.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-363">Value is opaque.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-364">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-364">**Examples**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-365">Immagini, video, documenti di Office, file PDF</span><span class="sxs-lookup"><span data-stu-id="b4b6e-365">Images, videos, office documents, PDFs</span></span></li>
            <li><span data-ttu-id="b4b6e-366">CSS, script, CSV</span><span class="sxs-lookup"><span data-stu-id="b4b6e-366">CSS, Scripts, CSV</span></span></li>
            <li><span data-ttu-id="b4b6e-367">HTML statico, JSON</span><span class="sxs-lookup"><span data-stu-id="b4b6e-367">Static HTML, JSON</span></span></li>
            <li><span data-ttu-id="b4b6e-368">File di log e di controllo</span><span class="sxs-lookup"><span data-stu-id="b4b6e-368">Log and audit files</span></span></li>
            <li><span data-ttu-id="b4b6e-369">Backup del database</span><span class="sxs-lookup"><span data-stu-id="b4b6e-369">Database backups</span></span></li>
        </ul>
    </td>
</tr>
</table>

## <a name="shared-files"></a><span data-ttu-id="b4b6e-370">File condivisi</span><span class="sxs-lookup"><span data-stu-id="b4b6e-370">Shared files</span></span>

<table>
<tr><td><span data-ttu-id="b4b6e-371">**Carico di lavoro**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-371">**Workload**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-372">Migrazione da app esistenti che interagiscono con il file system.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-372">Migration from existing apps that interact with the file system.</span></span></li>
            <li><span data-ttu-id="b4b6e-373">Richiede l'interfaccia SMB.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-373">Requires SMB interface.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-374">**Tipo di dati**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-374">**Data type**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-375">File in un set gerarchico di cartelle.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-375">Files in a hierarchical set of folders.</span></span></li>
            <li><span data-ttu-id="b4b6e-376">Accessibile con librerie di I/O standard.</span><span class="sxs-lookup"><span data-stu-id="b4b6e-376">Accessible with standard I/O libraries.</span></span></li>
        </ul>
    </td>
</tr>
<tr><td><span data-ttu-id="b4b6e-377">**esempi**</span><span class="sxs-lookup"><span data-stu-id="b4b6e-377">**Examples**</span></span></td>
    <td>
        <ul>
            <li><span data-ttu-id="b4b6e-378">File legacy</span><span class="sxs-lookup"><span data-stu-id="b4b6e-378">Legacy files</span></span></li>
            <li><span data-ttu-id="b4b6e-379">Contenuto condiviso accessibile tra più macchine virtuali o istanze di app</span><span class="sxs-lookup"><span data-stu-id="b4b6e-379">Shared content accessible among a number of VMs or app instances</span></span></li>
        </ul>
    </td>
</tr>
</table>
