---
title: Indicazioni specifiche del servizio per la ripetizione di tentativi
description: Indicazioni specifiche del servizio per impostare il meccanismo di ripetizione dei tentativi.
author: dragon119
ms.date: 07/13/2016
pnp.series.title: Best Practices
ms.openlocfilehash: 6aba60dc3a60e96e59e2034d4a1e380e0f1c996a
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="retry-guidance-for-specific-services"></a><span data-ttu-id="e8400-103">Materiale sussidiario su come eseguire nuovi tentativi per servizi specifici</span><span class="sxs-lookup"><span data-stu-id="e8400-103">Retry guidance for specific services</span></span>

<span data-ttu-id="e8400-104">La maggior parte dei servizi di Azure e degli SDK client include un meccanismo di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-104">Most Azure services and client SDKs include a retry mechanism.</span></span> <span data-ttu-id="e8400-105">Ogni servizio, tuttavia, presenta caratteristiche e requisiti diversi e, quindi, ogni meccanismo di ripetizione dei tentativi è ottimizzato per il servizio specifico in cui è incorporato.</span><span class="sxs-lookup"><span data-stu-id="e8400-105">However, these differ because each service has different characteristics and requirements, and so each retry mechanism is tuned to a specific service.</span></span> <span data-ttu-id="e8400-106">Questo articolo riepiloga le caratteristiche dei meccanismi di ripetizione dei tentativi relativi alla maggior parte dei servizi di Azure e include utili informazioni per usare, adattare o estendere il meccanismo di ripetizione dei tentativi per ogni servizio.</span><span class="sxs-lookup"><span data-stu-id="e8400-106">This guide summarizes the retry mechanism features for the majority of Azure services, and includes information to help you use, adapt, or extend the retry mechanism for that service.</span></span>

<span data-ttu-id="e8400-107">Per indicazioni generali sulla gestione degli errori temporanei e sulla ripetizione dei tentativi di eseguire connessioni e operazioni su servizi e risorse, vedere [Indicazioni generali per la ripetizione di tentativi](./transient-faults.md).</span><span class="sxs-lookup"><span data-stu-id="e8400-107">For general guidance on handling transient faults, and retrying connections and operations against services and resources, see [Retry guidance](./transient-faults.md).</span></span>

<span data-ttu-id="e8400-108">La tabella seguente riepiloga le caratteristiche dei meccanismi di ripetizione dei tentativi associati ai servizi di Azure descritti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="e8400-108">The following table summarizes the retry features for the Azure services described in this guidance.</span></span>

| <span data-ttu-id="e8400-109">**Servizio**</span><span class="sxs-lookup"><span data-stu-id="e8400-109">**Service**</span></span> | <span data-ttu-id="e8400-110">**Funzionalità per la ripetizione di tentativi**</span><span class="sxs-lookup"><span data-stu-id="e8400-110">**Retry capabilities**</span></span> | <span data-ttu-id="e8400-111">**Configurazione dei criteri**</span><span class="sxs-lookup"><span data-stu-id="e8400-111">**Policy configuration**</span></span> | <span data-ttu-id="e8400-112">**Ambito**</span><span class="sxs-lookup"><span data-stu-id="e8400-112">**Scope**</span></span> | <span data-ttu-id="e8400-113">**Funzionalità di telemetria**</span><span class="sxs-lookup"><span data-stu-id="e8400-113">**Telemetry features**</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="e8400-114">**[Archiviazione di Azure](#azure-storage-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-114">**[Azure Storage](#azure-storage-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-115">Native nel client</span><span class="sxs-lookup"><span data-stu-id="e8400-115">Native in client</span></span> |<span data-ttu-id="e8400-116">Programmatica</span><span class="sxs-lookup"><span data-stu-id="e8400-116">Programmatic</span></span> |<span data-ttu-id="e8400-117">Client e singole operazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-117">Client and individual operations</span></span> |<span data-ttu-id="e8400-118">TraceSource</span><span class="sxs-lookup"><span data-stu-id="e8400-118">TraceSource</span></span> |
| <span data-ttu-id="e8400-119">**[Database SQL con Entity Framework](#sql-database-using-entity-framework-6-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-119">**[SQL Database with Entity Framework](#sql-database-using-entity-framework-6-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-120">Native nel client</span><span class="sxs-lookup"><span data-stu-id="e8400-120">Native in client</span></span> |<span data-ttu-id="e8400-121">Programmatica</span><span class="sxs-lookup"><span data-stu-id="e8400-121">Programmatic</span></span> |<span data-ttu-id="e8400-122">Globale per AppDomain</span><span class="sxs-lookup"><span data-stu-id="e8400-122">Global per AppDomain</span></span> |<span data-ttu-id="e8400-123">Nessuno</span><span class="sxs-lookup"><span data-stu-id="e8400-123">None</span></span> |
| <span data-ttu-id="e8400-124">**[Database SQL con Entity Framework Core](#sql-database-using-entity-framework-core-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-124">**[SQL Database with Entity Framework Core](#sql-database-using-entity-framework-core-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-125">Native nel client</span><span class="sxs-lookup"><span data-stu-id="e8400-125">Native in client</span></span> |<span data-ttu-id="e8400-126">Programmatica</span><span class="sxs-lookup"><span data-stu-id="e8400-126">Programmatic</span></span> |<span data-ttu-id="e8400-127">Globale per AppDomain</span><span class="sxs-lookup"><span data-stu-id="e8400-127">Global per AppDomain</span></span> |<span data-ttu-id="e8400-128">None</span><span class="sxs-lookup"><span data-stu-id="e8400-128">None</span></span> |
| <span data-ttu-id="e8400-129">**[Database SQL con ADO.NET](#sql-database-using-adonet-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-129">**[SQL Database with ADO.NET](#sql-database-using-adonet-retry-guidelines)**</span></span> |[<span data-ttu-id="e8400-130">Polly</span><span class="sxs-lookup"><span data-stu-id="e8400-130">Polly</span></span>](#transient-fault-handling-with-polly) |<span data-ttu-id="e8400-131">Dichiarativa e a livello di codice</span><span class="sxs-lookup"><span data-stu-id="e8400-131">Declarative and programmatic</span></span> |<span data-ttu-id="e8400-132">Singole istruzioni o blocchi di codice</span><span class="sxs-lookup"><span data-stu-id="e8400-132">Single statements or blocks of code</span></span> |<span data-ttu-id="e8400-133">Personalizzate</span><span class="sxs-lookup"><span data-stu-id="e8400-133">Custom</span></span> |
| <span data-ttu-id="e8400-134">**[Bus di servizio](#service-bus-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-134">**[Service Bus](#service-bus-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-135">Native nel client</span><span class="sxs-lookup"><span data-stu-id="e8400-135">Native in client</span></span> |<span data-ttu-id="e8400-136">Programmatica</span><span class="sxs-lookup"><span data-stu-id="e8400-136">Programmatic</span></span> |<span data-ttu-id="e8400-137">Gestore dello spazio dei nomi, factory di messaggistica e client</span><span class="sxs-lookup"><span data-stu-id="e8400-137">Namespace Manager, Messaging Factory, and Client</span></span> |<span data-ttu-id="e8400-138">ETW</span><span class="sxs-lookup"><span data-stu-id="e8400-138">ETW</span></span> |
| <span data-ttu-id="e8400-139">**[Cache Redis di Azure](#azure-redis-cache-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-139">**[Azure Redis Cache](#azure-redis-cache-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-140">Native nel client</span><span class="sxs-lookup"><span data-stu-id="e8400-140">Native in client</span></span> |<span data-ttu-id="e8400-141">Programmatica</span><span class="sxs-lookup"><span data-stu-id="e8400-141">Programmatic</span></span> |<span data-ttu-id="e8400-142">Client</span><span class="sxs-lookup"><span data-stu-id="e8400-142">Client</span></span> |<span data-ttu-id="e8400-143">TextWriter</span><span class="sxs-lookup"><span data-stu-id="e8400-143">TextWriter</span></span> |
| <span data-ttu-id="e8400-144">**[API di DocumentDB](#documentdb-api-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-144">**[DocumentDB API](#documentdb-api-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-145">Native nel servizio</span><span class="sxs-lookup"><span data-stu-id="e8400-145">Native in service</span></span> |<span data-ttu-id="e8400-146">Non configurabili</span><span class="sxs-lookup"><span data-stu-id="e8400-146">Non-configurable</span></span> |<span data-ttu-id="e8400-147">Globale</span><span class="sxs-lookup"><span data-stu-id="e8400-147">Global</span></span> |<span data-ttu-id="e8400-148">TraceSource</span><span class="sxs-lookup"><span data-stu-id="e8400-148">TraceSource</span></span> |
| <span data-ttu-id="e8400-149">**[Ricerca di Azure](#azure-storage-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-149">**[Azure Search](#azure-storage-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-150">Native nel client</span><span class="sxs-lookup"><span data-stu-id="e8400-150">Native in client</span></span> |<span data-ttu-id="e8400-151">Programmatica</span><span class="sxs-lookup"><span data-stu-id="e8400-151">Programmatic</span></span> |<span data-ttu-id="e8400-152">Client</span><span class="sxs-lookup"><span data-stu-id="e8400-152">Client</span></span> |<span data-ttu-id="e8400-153">ETW o personalizzato</span><span class="sxs-lookup"><span data-stu-id="e8400-153">ETW or Custom</span></span> |
| <span data-ttu-id="e8400-154">**[Azure Active Directory](#azure-active-directory-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-154">**[Azure Active Directory](#azure-active-directory-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-155">Nativo nella libreria ADAL</span><span class="sxs-lookup"><span data-stu-id="e8400-155">Native in ADAL library</span></span> |<span data-ttu-id="e8400-156">Incorporato nella libreria ADAL</span><span class="sxs-lookup"><span data-stu-id="e8400-156">Embeded into ADAL library</span></span> |<span data-ttu-id="e8400-157">Interno</span><span class="sxs-lookup"><span data-stu-id="e8400-157">Internal</span></span> |<span data-ttu-id="e8400-158">Nessuno</span><span class="sxs-lookup"><span data-stu-id="e8400-158">None</span></span> |
| <span data-ttu-id="e8400-159">**[Service Fabric](#service-fabric-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-159">**[Service Fabric](#service-fabric-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-160">Native nel client</span><span class="sxs-lookup"><span data-stu-id="e8400-160">Native in client</span></span> |<span data-ttu-id="e8400-161">Programmatica</span><span class="sxs-lookup"><span data-stu-id="e8400-161">Programmatic</span></span> |<span data-ttu-id="e8400-162">Client</span><span class="sxs-lookup"><span data-stu-id="e8400-162">Client</span></span> |<span data-ttu-id="e8400-163">Nessuno</span><span class="sxs-lookup"><span data-stu-id="e8400-163">None</span></span> | 
| <span data-ttu-id="e8400-164">**[Hub eventi di Azure](#azure-event-hubs-retry-guidelines)**</span><span class="sxs-lookup"><span data-stu-id="e8400-164">**[Azure Event Hubs](#azure-event-hubs-retry-guidelines)**</span></span> |<span data-ttu-id="e8400-165">Native nel client</span><span class="sxs-lookup"><span data-stu-id="e8400-165">Native in client</span></span> |<span data-ttu-id="e8400-166">Programmatica</span><span class="sxs-lookup"><span data-stu-id="e8400-166">Programmatic</span></span> |<span data-ttu-id="e8400-167">Client</span><span class="sxs-lookup"><span data-stu-id="e8400-167">Client</span></span> |<span data-ttu-id="e8400-168">Nessuno</span><span class="sxs-lookup"><span data-stu-id="e8400-168">None</span></span> |

> [!NOTE]
> <span data-ttu-id="e8400-169">Per la maggior parte dei meccanismi di ripetizione dei tentativi incorporati in Azure, non è attualmente possibile applicare criteri di ripetizione dei tentativi differenti per diversi tipi di errore o eccezione, oltre alle funzionalità previste dai criteri stessi.</span><span class="sxs-lookup"><span data-stu-id="e8400-169">For most of the Azure built-in retry mechanisms, there is currently no way apply a different retry policy for different types of error or exception beyond the functionality include in the retry policy.</span></span> <span data-ttu-id="e8400-170">Al momento della stesura di questo documento, quindi, il consiglio migliore è quello di configurare criteri che forniscano una combinazione ottimale di prestazioni e disponibilità.</span><span class="sxs-lookup"><span data-stu-id="e8400-170">Therefore, the best guidance available at the time of writing is to configure a policy that provides the optimum average performance and availability.</span></span> <span data-ttu-id="e8400-171">I criteri possono essere successivamente ottimizzati analizzando i file di log per determinare i tipi di errori temporanei che si sono verificati.</span><span class="sxs-lookup"><span data-stu-id="e8400-171">One way to fine-tune the policy is to analyze log files to determine the type of transient faults that are occurring.</span></span> <span data-ttu-id="e8400-172">Ad esempio, se la maggior parte degli errori è correlata a problemi di connettività di rete, si potrebbe optare per un tentativo immediato anziché attendere molto tempo per ripetere il primo tentativo.</span><span class="sxs-lookup"><span data-stu-id="e8400-172">For example, if the majority of errors are related to network connectivity issues, you might attempt an immediate retry rather than wait a long time for the first retry.</span></span>
>
>

## <a name="azure-storage-retry-guidelines"></a><span data-ttu-id="e8400-173">Archiviazione di Azure - Linee guida per la ripetizione di tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-173">Azure Storage retry guidelines</span></span>
<span data-ttu-id="e8400-174">I servizi di archiviazione di Azure includono funzioni di archiviazione di BLOB e tabelle, file e code di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-174">Azure storage services include table and blob storage, files, and storage queues.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-175">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-175">Retry mechanism</span></span>
<span data-ttu-id="e8400-176">I nuovi tentativi si compiono a livello di singola operazione REST e sono parte integrante dell'implementazione dell'API client.</span><span class="sxs-lookup"><span data-stu-id="e8400-176">Retries occur at the individual REST operation level and are an integral part of the client API implementation.</span></span> <span data-ttu-id="e8400-177">L'SDK di archiviazione dei client usa classi che implementano l'interfaccia [IExtendedRetryPolicy Interface](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-177">The client storage SDK uses classes that implement the [IExtendedRetryPolicy Interface](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).</span></span>

<span data-ttu-id="e8400-178">Esistono diverse implementazioni di questa interfaccia.</span><span class="sxs-lookup"><span data-stu-id="e8400-178">There are different implementations of the interface.</span></span> <span data-ttu-id="e8400-179">I client di archiviazione possono scegliere tra vari tipi di criteri, progettati per l'accesso a tabelle, BLOB o code.</span><span class="sxs-lookup"><span data-stu-id="e8400-179">Storage clients can choose from policies specifically designed for accessing tables, blobs, and queues.</span></span> <span data-ttu-id="e8400-180">Ogni implementazione usa una strategia di ripetizione dei tentativi diversa, che essenzialmente definisce l'intervallo di tempo tra i tentativi e altri dettagli.</span><span class="sxs-lookup"><span data-stu-id="e8400-180">Each implementation uses a different retry strategy that essentially defines the retry interval and other details.</span></span>

<span data-ttu-id="e8400-181">Le classi incorporate forniscono il supporto per intervalli lineari (tempo d'intervallo costante) ed esponenziali con sequenza casuale.</span><span class="sxs-lookup"><span data-stu-id="e8400-181">The built-in classes provide support for linear (constant delay) and exponential with randomization retry intervals.</span></span> <span data-ttu-id="e8400-182">Esiste anche un criterio di tipo "nessun tentativo" se la gestione dei tentativi viene eseguita da un altro processo a un livello superiore.</span><span class="sxs-lookup"><span data-stu-id="e8400-182">There is also a no retry policy for use when another process is handling retries at a higher level.</span></span> <span data-ttu-id="e8400-183">Se, tuttavia, sono presenti requisiti specifici non previsti dalle classi incorporate, è possibile implementare classi di ripetizione dei tentativi personalizzate.</span><span class="sxs-lookup"><span data-stu-id="e8400-183">However, you can implement your own retry classes if you have specific requirements not provided by the built-in classes.</span></span>

<span data-ttu-id="e8400-184">Se si usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è possibile, ad esempio, alternare i tentativi tra il percorso del servizio di archiviazione primario e quello del servizio secondario e il risultato della richiesta è un errore non irreversibile.</span><span class="sxs-lookup"><span data-stu-id="e8400-184">Alternate retries switch between primary and secondary storage service location if you are using read access geo-redundant storage (RA-GRS) and the result of the request is a retryable error.</span></span> <span data-ttu-id="e8400-185">Per altre informazioni, vedere [Opzioni di ridondanza dell'archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx) .</span><span class="sxs-lookup"><span data-stu-id="e8400-185">See [Azure Storage Redundancy Options](http://msdn.microsoft.com/library/azure/dn727290.aspx) for more information.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="e8400-186">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="e8400-186">Policy configuration</span></span>
<span data-ttu-id="e8400-187">I criteri di ripetizione dei tentativi sono configurati a livello di codice.</span><span class="sxs-lookup"><span data-stu-id="e8400-187">Retry policies are configured programmatically.</span></span> <span data-ttu-id="e8400-188">La procedura tipica consiste nel creare e popolare un'istanza **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions** o **QueueRequestOptions**.</span><span class="sxs-lookup"><span data-stu-id="e8400-188">A typical procedure is to create and populate a **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions**, or **QueueRequestOptions** instance.</span></span>

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

<span data-ttu-id="e8400-189">L'istanza delle opzioni di richiesta può essere quindi impostata sul client in modo che tutte le operazioni eseguite sul client usino le opzioni di richiesta specificate.</span><span class="sxs-lookup"><span data-stu-id="e8400-189">The request options instance can then be set on the client, and all operations with the client will use the specified request options.</span></span>

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

<span data-ttu-id="e8400-190">È comunque possibile ignorare le opzioni di richiesta del client passando ai metodi dell'operazione un'istanza popolata della classe delle opzioni di richiesta sotto forma di parametro.</span><span class="sxs-lookup"><span data-stu-id="e8400-190">You can override the client request options by passing a populated instance of the request options class as a parameter to operation methods.</span></span>

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

<span data-ttu-id="e8400-191">Si userà in questo caso un'istanza **OperationContext** per specificare il codice da eseguire quando si ripete un tentativo o quando, invece, viene completata un'operazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-191">You use an **OperationContext** instance to specify the code to execute when a retry occurs and when an operation has completed.</span></span> <span data-ttu-id="e8400-192">Questo codice è in grado di raccogliere informazioni sull'operazione, da usare nei log e nella telemetria.</span><span class="sxs-lookup"><span data-stu-id="e8400-192">This code can collect information about the operation for use in logs and telemetry.</span></span>

    // Set up notifications for an operation
    var context = new OperationContext();
    context.ClientRequestID = "some request id";
    context.Retrying += (sender, args) =>
    {
      /* Collect retry information */
    };
    context.RequestCompleted += (sender, args) =>
    {
      /* Collect operation completion information */
    };
    var stats = await client.GetServiceStatsAsync(null, context);

<span data-ttu-id="e8400-193">Oltre a indicare se un errore può essere risolto eseguendo nuovi tentativi, i criteri estesi di ripetizione dei tentativi restituiscono un oggetto **RetryContext** , che indica il numero di tentativi, i risultati dell'ultima richiesta e se il tentativo successivo verrà eseguito nel percorso primario o secondario (vedere la tabella seguente per maggiori dettagli).</span><span class="sxs-lookup"><span data-stu-id="e8400-193">In addition to indicating whether a failure is suitable for retry, the extended retry policies return a **RetryContext** object that indicates the number of retries, the results of the last request, whether the next retry will happen in the primary or secondary location (see table below for details).</span></span> <span data-ttu-id="e8400-194">Le proprietà dell'oggetto **RetryContext** possono essere inoltre usate per decidere se e quando eseguire un nuovo tentativo.</span><span class="sxs-lookup"><span data-stu-id="e8400-194">The properties of the **RetryContext** object can be used to decide if and when to attempt a retry.</span></span> <span data-ttu-id="e8400-195">Per altre informazioni, vedere [IExtendedRetryPolicy.Evaluate Method](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-195">For more details, see [IExtendedRetryPolicy.Evaluate Method](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).</span></span>

<span data-ttu-id="e8400-196">La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.</span><span class="sxs-lookup"><span data-stu-id="e8400-196">The following table shows the default settings for the built-in retry policies.</span></span>

| <span data-ttu-id="e8400-197">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="e8400-197">**Context**</span></span> | <span data-ttu-id="e8400-198">**Impostazione**</span><span class="sxs-lookup"><span data-stu-id="e8400-198">**Setting**</span></span> | <span data-ttu-id="e8400-199">**Valore predefinito**</span><span class="sxs-lookup"><span data-stu-id="e8400-199">**Default value**</span></span> | <span data-ttu-id="e8400-200">**Significato**</span><span class="sxs-lookup"><span data-stu-id="e8400-200">**Meaning**</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="e8400-201">QueueRequestOptions</span><span class="sxs-lookup"><span data-stu-id="e8400-201">Table / Blob / File</span></span><br /><span data-ttu-id="e8400-202">QueueRequestOptions</span><span class="sxs-lookup"><span data-stu-id="e8400-202">QueueRequestOptions</span></span> |<span data-ttu-id="e8400-203">MaximumExecutionTime</span><span class="sxs-lookup"><span data-stu-id="e8400-203">MaximumExecutionTime</span></span><br /><br /><span data-ttu-id="e8400-204">ServerTimeout</span><span class="sxs-lookup"><span data-stu-id="e8400-204">ServerTimeout</span></span><br /><br /><br /><br /><br /><span data-ttu-id="e8400-205">LocationMode</span><span class="sxs-lookup"><span data-stu-id="e8400-205">LocationMode</span></span><br /><br /><br /><br /><br /><br /><br /><span data-ttu-id="e8400-206">RetryPolicy</span><span class="sxs-lookup"><span data-stu-id="e8400-206">RetryPolicy</span></span> |<span data-ttu-id="e8400-207">120 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-207">120 seconds</span></span><br /><br /><span data-ttu-id="e8400-208">Nessuno</span><span class="sxs-lookup"><span data-stu-id="e8400-208">None</span></span><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><span data-ttu-id="e8400-209">ExponentialPolicy</span><span class="sxs-lookup"><span data-stu-id="e8400-209">ExponentialPolicy</span></span> |<span data-ttu-id="e8400-210">Tempo di esecuzione massimo per la richiesta, inclusi tutti i potenziali tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-210">Maximum execution time for the request, including all potential retry attempts.</span></span><br /><span data-ttu-id="e8400-211">Intervallo di timeout del server per la richiesta (il valore viene arrotondato a secondi).</span><span class="sxs-lookup"><span data-stu-id="e8400-211">Server timeout interval for the request (value is rounded to seconds).</span></span> <span data-ttu-id="e8400-212">Se non specificato, verrà usato il valore predefinito per tutte le richieste al server.</span><span class="sxs-lookup"><span data-stu-id="e8400-212">If not specified, it will use the default value for all requests to the server.</span></span> <span data-ttu-id="e8400-213">In genere, la scelta migliore è omettere questa impostazione in modo che venga usato il valore predefinito del server.</span><span class="sxs-lookup"><span data-stu-id="e8400-213">Usually, the best option is to omit this setting so that the server default is used.</span></span><br /><span data-ttu-id="e8400-214">Se l'account di archiviazione viene creato con l'opzione di replica "archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)", è possibile usare la modalità percorso per indicare in corrispondenza di quale percorso deve essere ricevuta la richiesta.</span><span class="sxs-lookup"><span data-stu-id="e8400-214">If the storage account is created with the Read access geo-redundant storage (RA-GRS) replication option, you can use the location mode to indicate which location should receive the request.</span></span> <span data-ttu-id="e8400-215">Ad esempio, se è specificata l'opzione **PrimaryThenSecondary** , le richieste vengono sempre inviate prima al percorso primario.</span><span class="sxs-lookup"><span data-stu-id="e8400-215">For example, if **PrimaryThenSecondary** is specified, requests are always sent to the primary location first.</span></span> <span data-ttu-id="e8400-216">Se una richiesta ha esito negativo, viene quindi inviata al percorso secondario.</span><span class="sxs-lookup"><span data-stu-id="e8400-216">If a request fails, it is sent to the secondary location.</span></span><br /><span data-ttu-id="e8400-217">Per informazioni dettagliate su ogni opzione, vedere le sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="e8400-217">See below for details of each option.</span></span> |
| <span data-ttu-id="e8400-218">Criteri esponenziali</span><span class="sxs-lookup"><span data-stu-id="e8400-218">Exponential policy</span></span> |<span data-ttu-id="e8400-219">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="e8400-219">maxAttempt</span></span><br /><span data-ttu-id="e8400-220">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="e8400-220">deltaBackoff</span></span><br /><br /><br /><span data-ttu-id="e8400-221">MinBackoff</span><span class="sxs-lookup"><span data-stu-id="e8400-221">MinBackoff</span></span><br /><br /><span data-ttu-id="e8400-222">MaxBackoff</span><span class="sxs-lookup"><span data-stu-id="e8400-222">MaxBackoff</span></span> |<span data-ttu-id="e8400-223">3</span><span class="sxs-lookup"><span data-stu-id="e8400-223">3</span></span><br /><span data-ttu-id="e8400-224">4 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-224">4 seconds</span></span><br /><br /><br /><span data-ttu-id="e8400-225">3 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-225">3 seconds</span></span><br /><br /><span data-ttu-id="e8400-226">120 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-226">120 seconds</span></span> |<span data-ttu-id="e8400-227">Numero di tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-227">Number of retry attempts.</span></span><br /><span data-ttu-id="e8400-228">Intervallo di backoff tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-228">Back-off interval between retries.</span></span> <span data-ttu-id="e8400-229">Per i tentativi successivi verranno usati multipli di questo intervallo di tempo, combinati con un elemento casuale.</span><span class="sxs-lookup"><span data-stu-id="e8400-229">Multiples of this timespan, including a random element, will be used for subsequent retry attempts.</span></span><br /><span data-ttu-id="e8400-230">I valori vengono aggiunti a tutti gli intervalli tra i tentativi, calcolati a partire da deltaBackoff.</span><span class="sxs-lookup"><span data-stu-id="e8400-230">Added to all retry intervals computed from deltaBackoff.</span></span> <span data-ttu-id="e8400-231">Questo valore non può essere modificato.</span><span class="sxs-lookup"><span data-stu-id="e8400-231">This value cannot be changed.</span></span><br /><span data-ttu-id="e8400-232">MaxBackoff viene usato se l'intervallo tra i tentativi è maggiore di MaxBackoff.</span><span class="sxs-lookup"><span data-stu-id="e8400-232">MaxBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> <span data-ttu-id="e8400-233">Questo valore non può essere modificato.</span><span class="sxs-lookup"><span data-stu-id="e8400-233">This value cannot be changed.</span></span> |
| <span data-ttu-id="e8400-234">Criteri lineari</span><span class="sxs-lookup"><span data-stu-id="e8400-234">Linear policy</span></span> |<span data-ttu-id="e8400-235">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="e8400-235">maxAttempt</span></span><br /><span data-ttu-id="e8400-236">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="e8400-236">deltaBackoff</span></span> |<span data-ttu-id="e8400-237">3</span><span class="sxs-lookup"><span data-stu-id="e8400-237">3</span></span><br /><span data-ttu-id="e8400-238">30 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-238">30 seconds</span></span> |<span data-ttu-id="e8400-239">Numero di tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-239">Number of retry attempts.</span></span><br /><span data-ttu-id="e8400-240">Intervallo di backoff tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-240">Back-off interval between retries.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="e8400-241">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-241">Retry usage guidance</span></span>
<span data-ttu-id="e8400-242">Quando si accede ai servizi di archiviazione di Azure tramite l'API del client di archiviazione, tenere presenti le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="e8400-242">Consider the following guidelines when accessing Azure storage services using the storage client API:</span></span>

* <span data-ttu-id="e8400-243">Usare i criteri di ripetizione dei tentativi dello spazio dei nomi Microsoft.WindowsAzure.Storage.RetryPolicies se appropriati per le proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="e8400-243">Use the built-in retry policies from the Microsoft.WindowsAzure.Storage.RetryPolicies namespace where they are appropriate for your requirements.</span></span> <span data-ttu-id="e8400-244">Nella maggior parte dei casi, questi criteri saranno sufficienti.</span><span class="sxs-lookup"><span data-stu-id="e8400-244">In most cases, these policies will be sufficient.</span></span>
* <span data-ttu-id="e8400-245">Usare invece i criteri **ExponentialRetry** nelle operazioni in batch, nelle attività in background e negli scenari non interattivi.</span><span class="sxs-lookup"><span data-stu-id="e8400-245">Use the **ExponentialRetry** policy in batch operations, background tasks, or non-interactive scenarios.</span></span> <span data-ttu-id="e8400-246">In questi scenari, è generalmente possibile concedere più tempo per il ripristino del servizio, aumentando così le probabilità che l'operazione abbia esito positivo.</span><span class="sxs-lookup"><span data-stu-id="e8400-246">In these scenarios, you can typically allow more time for the service to recover—with a consequently increased chance of the operation eventually succeeding.</span></span>
* <span data-ttu-id="e8400-247">Valutare la possibilità di specificare la proprietà **MaximumExecutionTime** del parametro **RequestOptions** per limitare il tempo di esecuzione complessivo, ma tenere conto del tipo e delle dimensioni dell'operazione quando si sceglie un valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="e8400-247">Consider specifying the **MaximumExecutionTime** property of the **RequestOptions** parameter to limit the total execution time, but take into account the type and size of the operation when choosing a timeout value.</span></span>
* <span data-ttu-id="e8400-248">Se è necessario implementare un meccanismo di ripetizione dei tentativi personalizzato, evitare di creare wrapper per le classi dei client di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-248">If you need to implement a custom retry, avoid creating wrappers around the storage client classes.</span></span> <span data-ttu-id="e8400-249">Usare invece questa funzionalità per estendere i criteri esistenti tramite l'interfaccia **IExtendedRetryPolicy** .</span><span class="sxs-lookup"><span data-stu-id="e8400-249">Instead, use the capabilities to extend the existing policies through the **IExtendedRetryPolicy** interface.</span></span>
* <span data-ttu-id="e8400-250">Se si usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è possibile adottare la modalità **LocationMode** per specificare che i nuovi tentativi accederanno alla copia secondaria di sola lettura dell'archivio, nel caso in cui l'accesso alla versione principale abbia esito negativo.</span><span class="sxs-lookup"><span data-stu-id="e8400-250">If you are using read access geo-redundant storage (RA-GRS) you can use the **LocationMode** to specify that retry attempts will access the secondary read-only copy of the store should the primary access fail.</span></span> <span data-ttu-id="e8400-251">Quando si usa questa opzione, tuttavia, è necessario verificare che l'applicazione possa gestire correttamente anche dati non aggiornati, nel caso in cui la replica dall'archivio primario non sia ancora stata completata.</span><span class="sxs-lookup"><span data-stu-id="e8400-251">However, when using this option you must ensure that your application can work successfully with data that may be stale if the replication from the primary store has not yet completed.</span></span>

<span data-ttu-id="e8400-252">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="e8400-252">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="e8400-253">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="e8400-253">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>  

| <span data-ttu-id="e8400-254">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="e8400-254">**Context**</span></span> | <span data-ttu-id="e8400-255">**Destinazione di esempio E2E<br />latenza massima**</span><span class="sxs-lookup"><span data-stu-id="e8400-255">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="e8400-256">**Criteri di ripetizione**</span><span class="sxs-lookup"><span data-stu-id="e8400-256">**Retry policy**</span></span> | <span data-ttu-id="e8400-257">**Impostazioni**</span><span class="sxs-lookup"><span data-stu-id="e8400-257">**Settings**</span></span> | <span data-ttu-id="e8400-258">**Valori**</span><span class="sxs-lookup"><span data-stu-id="e8400-258">**Values**</span></span> | <span data-ttu-id="e8400-259">**Funzionamento**</span><span class="sxs-lookup"><span data-stu-id="e8400-259">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="e8400-260">Interattivo, interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="e8400-260">Interactive, UI,</span></span><br /><span data-ttu-id="e8400-261">o in primo piano</span><span class="sxs-lookup"><span data-stu-id="e8400-261">or foreground</span></span> |<span data-ttu-id="e8400-262">2 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-262">2 seconds</span></span> |<span data-ttu-id="e8400-263">Lineari</span><span class="sxs-lookup"><span data-stu-id="e8400-263">Linear</span></span> |<span data-ttu-id="e8400-264">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="e8400-264">maxAttempt</span></span><br /><span data-ttu-id="e8400-265">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="e8400-265">deltaBackoff</span></span> |<span data-ttu-id="e8400-266">3</span><span class="sxs-lookup"><span data-stu-id="e8400-266">3</span></span><br /><span data-ttu-id="e8400-267">500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-267">500 ms</span></span> |<span data-ttu-id="e8400-268">Tentativo di 1 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-268">Attempt 1 - delay 500 ms</span></span><br /><span data-ttu-id="e8400-269">Tentativo 2 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-269">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="e8400-270">Tentativo 3 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-270">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="e8400-271">Background</span><span class="sxs-lookup"><span data-stu-id="e8400-271">Background</span></span><br /><span data-ttu-id="e8400-272">o batch</span><span class="sxs-lookup"><span data-stu-id="e8400-272">or batch</span></span> |<span data-ttu-id="e8400-273">30 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-273">30 seconds</span></span> |<span data-ttu-id="e8400-274">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="e8400-274">Exponential</span></span> |<span data-ttu-id="e8400-275">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="e8400-275">maxAttempt</span></span><br /><span data-ttu-id="e8400-276">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="e8400-276">deltaBackoff</span></span> |<span data-ttu-id="e8400-277">5</span><span class="sxs-lookup"><span data-stu-id="e8400-277">5</span></span><br /><span data-ttu-id="e8400-278">4 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-278">4 seconds</span></span> |<span data-ttu-id="e8400-279">Tentativo di 1 - intervallo di ~3 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-279">Attempt 1 - delay ~3 sec</span></span><br /><span data-ttu-id="e8400-280">Tentativo 2 - intervallo di ~7 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-280">Attempt 2 - delay ~7 sec</span></span><br /><span data-ttu-id="e8400-281">Tentativo 3 - intervallo di ~15 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-281">Attempt 3 - delay ~15 sec</span></span> |

### <a name="telemetry"></a><span data-ttu-id="e8400-282">Telemetria</span><span class="sxs-lookup"><span data-stu-id="e8400-282">Telemetry</span></span>
<span data-ttu-id="e8400-283">Il numero di tentativi viene registrato in un'origine **TraceSource**.</span><span class="sxs-lookup"><span data-stu-id="e8400-283">Retry attempts are logged to a **TraceSource**.</span></span> <span data-ttu-id="e8400-284">È necessario configurare un oggetto **TraceListener** per acquisire gli eventi e scriverli in un log di destinazione appropriato.</span><span class="sxs-lookup"><span data-stu-id="e8400-284">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span> <span data-ttu-id="e8400-285">È possibile usare **TextWriterTraceListener** o **XmlWriterTraceListener** per scrivere i dati in un file di log, **EventLogTraceListener** per scriverli nel Registro eventi di Windows o **EventProviderTraceListener** per scrivere i dati di traccia nel sottosistema ETW.</span><span class="sxs-lookup"><span data-stu-id="e8400-285">You can use the **TextWriterTraceListener** or **XmlWriterTraceListener** to write the data to a log file, the **EventLogTraceListener** to write to the Windows Event Log, or the **EventProviderTraceListener** to write trace data to the ETW subsystem.</span></span> <span data-ttu-id="e8400-286">È inoltre possibile configurare lo svuotamento automatico del buffer e il livello di dettaglio degli eventi che verranno registrati nel log (specificando, ad esempio, i valori Error, Warning, Informational e Verbose).</span><span class="sxs-lookup"><span data-stu-id="e8400-286">You can also configure auto-flushing of the buffer, and the verbosity of events that will be logged (for example, Error, Warning, Informational, and Verbose).</span></span> <span data-ttu-id="e8400-287">Per altre informazioni, vedere [Registrazione lato client con la libreria client di archiviazione .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-287">For more information, see [Client-side Logging with the .NET Storage Client Library](http://msdn.microsoft.com/library/azure/dn782839.aspx).</span></span>

<span data-ttu-id="e8400-288">Le operazioni possono ricevere un'istanza **OperationContext**, che espone un evento **Retrying** di cui è possibile usufruire per associare una logica personalizzata per i dati telemetrici.</span><span class="sxs-lookup"><span data-stu-id="e8400-288">Operations can receive an **OperationContext** instance, which exposes a **Retrying** event that can be used to attach custom telemetry logic.</span></span> <span data-ttu-id="e8400-289">Per altre informazioni, vedere [Evento OperationContext.Retrying](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-289">For more information, see [OperationContext.Retrying Event](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).</span></span>

### <a name="examples"></a><span data-ttu-id="e8400-290">esempi</span><span class="sxs-lookup"><span data-stu-id="e8400-290">Examples</span></span>
<span data-ttu-id="e8400-291">L'esempio di codice seguente mostra come creare due istanze **TableRequestOptions** con due diverse impostazioni di ripetizione dei tentativi: una per le richieste interattive e una per le richieste in background.</span><span class="sxs-lookup"><span data-stu-id="e8400-291">The following code example shows how to create two **TableRequestOptions** instances with different retry settings; one for interactive requests and one for background requests.</span></span> <span data-ttu-id="e8400-292">L'esempio imposta quindi questi due criteri di ripetizione dei tentativi in modo che vengano applicati a tutte le richieste. Imposta inoltre la strategia interattiva su una richiesta specifica in modo che ignori le impostazioni predefinite applicate al client.</span><span class="sxs-lookup"><span data-stu-id="e8400-292">The example then sets these two retry policies on the client so that they apply for all requests, and also sets the interactive strategy on a specific request so that it overrides the default settings applied to the client.</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

### <a name="more-information"></a><span data-ttu-id="e8400-293">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-293">More information</span></span>
* [<span data-ttu-id="e8400-294">Suggerimenti per i criteri di ripetizione dei tentativi nella libreria client dell'archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="e8400-294">Azure Storage Client Library Retry Policy Recommendations</span></span>](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
* [<span data-ttu-id="e8400-295">Libreria client di archiviazione 2.0 - Implementazione dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-295">Storage Client Library 2.0 – Implementing Retry Policies</span></span>](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## <a name="sql-database-using-entity-framework-6-retry-guidelines"></a><span data-ttu-id="e8400-296">Database SQL con Entity Framework 6 - Linee guida per la ripetizione di tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-296">SQL Database using Entity Framework 6 retry guidelines</span></span>
<span data-ttu-id="e8400-297">Il database SQL, in questo caso, è un database SQL ospitato, di varie dimensioni, disponibile come servizio standard (condiviso) o premium (non condiviso).</span><span class="sxs-lookup"><span data-stu-id="e8400-297">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span> <span data-ttu-id="e8400-298">Entity Framework, invece, è un mapper relazionale a oggetti che consente agli sviluppatori .NET di usare dati relazionali mediante oggetti specifici di dominio.</span><span class="sxs-lookup"><span data-stu-id="e8400-298">Entity Framework is an object-relational mapper that enables .NET developers to work with relational data using domain-specific objects.</span></span> <span data-ttu-id="e8400-299">Elimina la necessità di gran parte del codice di accesso ai dati che in genere gli sviluppatori devono scrivere.</span><span class="sxs-lookup"><span data-stu-id="e8400-299">It eliminates the need for most of the data-access code that developers usually need to write.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-300">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-300">Retry mechanism</span></span>
<span data-ttu-id="e8400-301">Quando si accede al database SQL con Entity Framework 6.0 o versione superiore, il supporto per la ripetizione di tentativi viene fornito attraverso un meccanismo denominato [resilienza delle connessioni / logica di ripetizione dei tentativi](http://msdn.microsoft.com/data/dn456835.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-301">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher through a mechanism called [Connection Resiliency / Retry Logic](http://msdn.microsoft.com/data/dn456835.aspx).</span></span> <span data-ttu-id="e8400-302">Di seguiti sono illustrate le caratteristiche principali del meccanismo di ripetizione dei tentativi:</span><span class="sxs-lookup"><span data-stu-id="e8400-302">The main features of the retry mechanism are:</span></span>

* <span data-ttu-id="e8400-303">L'astrazione primaria è l'interfaccia **IDbExecutionStrategy** .</span><span class="sxs-lookup"><span data-stu-id="e8400-303">The primary abstraction is the **IDbExecutionStrategy** interface.</span></span> <span data-ttu-id="e8400-304">Questa interfaccia:</span><span class="sxs-lookup"><span data-stu-id="e8400-304">This interface:</span></span>
  * <span data-ttu-id="e8400-305">Definisce i metodi **Execute*** sincroni e asincroni.</span><span class="sxs-lookup"><span data-stu-id="e8400-305">Defines synchronous and asynchronous **Execute*** methods.</span></span>
  * <span data-ttu-id="e8400-306">Definisce le classi che possono essere usate direttamente o configurate in un contesto di database come strategia predefinita, quindi mappate a un nome di provider o a un nome di provider e un nome di server.</span><span class="sxs-lookup"><span data-stu-id="e8400-306">Defines classes that can be used directly or can be configured on a database context as a default strategy, mapped to provider name, or mapped to a provider name and server name.</span></span> <span data-ttu-id="e8400-307">Se configurate in un contesto, i nuovi tentativi vengono eseguiti a livello di singole operazioni di database, che possono essere molteplici per una determinata operazione di contesto.</span><span class="sxs-lookup"><span data-stu-id="e8400-307">When configured on a context, retries occur at the level of individual database operations, of which there might be several for a given context operation.</span></span>
  * <span data-ttu-id="e8400-308">Definisce quando tentare di eseguire nuovamente una connessione non riuscita e in che modo.</span><span class="sxs-lookup"><span data-stu-id="e8400-308">Defines when to retry a failed connection, and how.</span></span>
* <span data-ttu-id="e8400-309">Include diverse implementazioni incorporate dell'interfaccia **IDbExecutionStrategy** :</span><span class="sxs-lookup"><span data-stu-id="e8400-309">It includes several built-in implementations of the **IDbExecutionStrategy** interface:</span></span>
  * <span data-ttu-id="e8400-310">Predefinita - Nessun nuovo tentativo.</span><span class="sxs-lookup"><span data-stu-id="e8400-310">Default - no retrying.</span></span>
  * <span data-ttu-id="e8400-311">Predefinita per il database SQL (automatica) - Nessun nuovo tentativo, ma controlla le eccezioni e ne esegue il wrapping con il suggerimento di usare la strategia relativa al database SQL.</span><span class="sxs-lookup"><span data-stu-id="e8400-311">Default for SQL Database (automatic) - no retrying, but inspects exceptions and wraps them with suggestion to use the SQL Database strategy.</span></span>
  * <span data-ttu-id="e8400-312">Predefinita per il database SQL - Esponenziale (ereditata dalla classe di base), combinata con la logica di rilevamento del database SQL.</span><span class="sxs-lookup"><span data-stu-id="e8400-312">Default for SQL Database - exponential (inherited from base class) plus SQL Database detection logic.</span></span>
* <span data-ttu-id="e8400-313">Implementa una strategia di backoff esponenziale che include la sequenza casuale.</span><span class="sxs-lookup"><span data-stu-id="e8400-313">It implements an exponential back-off strategy that includes randomization.</span></span>
* <span data-ttu-id="e8400-314">Le classi di ripetizione dei tentativi incorporate sono classi con stato di tipo non thread-safe.</span><span class="sxs-lookup"><span data-stu-id="e8400-314">The built-in retry classes are stateful and are not thread safe.</span></span> <span data-ttu-id="e8400-315">Possono comunque essere riusate dopo il completamento dell'operazione corrente.</span><span class="sxs-lookup"><span data-stu-id="e8400-315">However, they can be reused after the current operation is completed.</span></span>
* <span data-ttu-id="e8400-316">Se viene superato il numero di tentativi specificato, i risultati vengono inclusi in una nuova eccezione</span><span class="sxs-lookup"><span data-stu-id="e8400-316">If the specified retry count is exceeded, the results are wrapped in a new exception.</span></span> <span data-ttu-id="e8400-317">e l'eccezione corrente non viene visualizzata.</span><span class="sxs-lookup"><span data-stu-id="e8400-317">It does not bubble up the current exception.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="e8400-318">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="e8400-318">Policy configuration</span></span>
<span data-ttu-id="e8400-319">Quando si accede al database SQL con Entity Framework 6.0, o versioni successive, è già disponibile il supporto per la ripetizione di tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-319">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher.</span></span> <span data-ttu-id="e8400-320">I criteri di ripetizione dei tentativi sono configurati a livello di codice.</span><span class="sxs-lookup"><span data-stu-id="e8400-320">Retry policies are configured programmatically.</span></span> <span data-ttu-id="e8400-321">La configurazione non può essere modificata a livello di singola operazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-321">The configuration cannot be changed on a per-operation basis.</span></span>

<span data-ttu-id="e8400-322">Quando si configura una strategia di contesto come impostazione predefinita, è necessario specificare anche una funzione che consente di creare una nuova strategia su richiesta.</span><span class="sxs-lookup"><span data-stu-id="e8400-322">When configuring a strategy on the context as the default, you specify a function that creates a new strategy on demand.</span></span> <span data-ttu-id="e8400-323">Il codice seguente mostra come creare una classe di configurazione di nuovi tentativi che estende la classe di base **DbConfiguration** .</span><span class="sxs-lookup"><span data-stu-id="e8400-323">The following code shows how you can create a retry configuration class that extends the **DbConfiguration** base class.</span></span>

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

<span data-ttu-id="e8400-324">È quindi possibile impostarla come strategia di ripetizione dei tentativi predefinita per tutte le operazioni usando il metodo **SetConfiguration** dell'istanza **DbConfiguration** all'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-324">You can then specify this as the default retry strategy for all operations using the **SetConfiguration** method of the **DbConfiguration** instance when the application starts.</span></span> <span data-ttu-id="e8400-325">Per impostazione predefinita, Entity Framework rileverà e userà automaticamente questa classe di configurazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-325">By default, EF will automatically discover and use the configuration class.</span></span>

    DbConfiguration.SetConfiguration(new BloggingContextConfiguration());

<span data-ttu-id="e8400-326">È possibile specificare la classe di configurazione di nuovi tentativi per un contesto specifico annotando la classe del contesto con un attributo **DbConfigurationType** .</span><span class="sxs-lookup"><span data-stu-id="e8400-326">You can specify the retry configuration class for a context by annotating the context class with a **DbConfigurationType** attribute.</span></span> <span data-ttu-id="e8400-327">Tuttavia, se è presente una sola classe di configurazione, Entity Framework la userà anche senza dover annotare il contesto.</span><span class="sxs-lookup"><span data-stu-id="e8400-327">However, if you have only one configuration class, EF will use it without the need to annotate the context.</span></span>

    [DbConfigurationType(typeof(BloggingContextConfiguration))]
    public class BloggingContext : DbContext
    { ...

<span data-ttu-id="e8400-328">Se per determinate operazioni è necessario usare strategie di ripetizione dei tentativi diverse o si disabilita la ripetizione dei tentativi, è possibile creare una classe di configurazione che consente di sospendere o cambiare strategie impostando un flag in **CallContext**.</span><span class="sxs-lookup"><span data-stu-id="e8400-328">If you need to use different retry strategies for specific operations, or disable retries for specific operations, you can create a configuration class that allows you to suspend or swap strategies by setting a flag in the **CallContext**.</span></span> <span data-ttu-id="e8400-329">Questo flag può essere usato dalla classe di configurazione per cambiare strategie o per disabilitare la strategia specificata e usare invece una strategia predefinita.</span><span class="sxs-lookup"><span data-stu-id="e8400-329">The configuration class can use this flag to switch strategies, or disable the strategy you provide and use a default strategy.</span></span> <span data-ttu-id="e8400-330">Per altre informazioni, vedere la sezione sulla [sospensione della strategia di esecuzione](http://msdn.microsoft.com/dn307226#transactions_workarounds) nella pagina relativa alle limitazioni quando si ripetono strategie di esecuzione (Entity Framework 6 o versione successiva).</span><span class="sxs-lookup"><span data-stu-id="e8400-330">For more information, see [Suspend Execution Strategy](http://msdn.microsoft.com/dn307226#transactions_workarounds) in the page Limitations with Retrying Execution Strategies (EF6 onwards).</span></span>

<span data-ttu-id="e8400-331">Un'altra tecnica per usare strategie di ripetizione specifiche per singole operazioni consiste nel creare un'istanza della classe di strategia necessaria, fornire le impostazioni desiderate attraverso dei parametri</span><span class="sxs-lookup"><span data-stu-id="e8400-331">Another technique for using specific retry strategies for individual operations is to create an instance of the required strategy class and supply the desired settings through parameters.</span></span> <span data-ttu-id="e8400-332">e quindi richiamare il metodo **ExecuteAsync**.</span><span class="sxs-lookup"><span data-stu-id="e8400-332">You then invoke its **ExecuteAsync** method.</span></span>

    var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
    var blogs = await executionStrategy.ExecuteAsync(
        async () =>
        {
            using (var db = new BloggingContext("Blogs"))
            {
                // Acquire some values asynchronously and return them
            }
        },
        new CancellationToken()
    );

<span data-ttu-id="e8400-333">Il modo più semplice per usare una classe **DbConfiguration** consiste nell'individuarla nello stesso assembly della classe **DbContext**.</span><span class="sxs-lookup"><span data-stu-id="e8400-333">The simplest way to use a **DbConfiguration** class is to locate it in the same assembly as the **DbContext** class.</span></span> <span data-ttu-id="e8400-334">Questo metodo, tuttavia, non è appropriato quando lo stesso contesto è richiesto in scenari diversi, ad esempio in varie strategie di ripetizione dei tentativi, interattiva o in background.</span><span class="sxs-lookup"><span data-stu-id="e8400-334">However, this is not appropriate when the same context is required in different scenarios, such as different interactive and background retry strategies.</span></span> <span data-ttu-id="e8400-335">Se i diversi contesti vengono eseguiti in AppDomain distinti, è possibile usare il supporto incorporato per specificare le classi di configurazione nel file di configurazione o impostarle in modo esplicito tramite codice.</span><span class="sxs-lookup"><span data-stu-id="e8400-335">If the different contexts execute in separate AppDomains, you can use the built-in support for specifying configuration classes in the configuration file or set it explicitly using code.</span></span> <span data-ttu-id="e8400-336">Se invece i diversi contesti devono essere eseguiti nello stesso AppDomain, sarà necessaria una soluzione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="e8400-336">If the different contexts must execute in the same AppDomain, a custom solution will be required.</span></span>

<span data-ttu-id="e8400-337">Per altre informazioni, vedere l'articolo sulla [configurazione basata su codice (Entity Framework 6 o versione successiva)](http://msdn.microsoft.com/data/jj680699.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-337">For more information, see [Code-Based Configuration (EF6 onwards)](http://msdn.microsoft.com/data/jj680699.aspx).</span></span>

<span data-ttu-id="e8400-338">La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati quando si usa Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="e8400-338">The following table shows the default settings for the built-in retry policy when using EF6.</span></span>

![Tabella del materiale sussidiario sulla ripetizione di tentativi](./images/retry-service-specific/RetryServiceSpecificGuidanceTable4.png)

### <a name="retry-usage-guidance"></a><span data-ttu-id="e8400-340">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-340">Retry usage guidance</span></span>
<span data-ttu-id="e8400-341">Quando si accede al database SQL con Entity Framework 6, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="e8400-341">Consider the following guidelines when accessing SQL Database using EF6:</span></span>

* <span data-ttu-id="e8400-342">Scegliere il tipo di servizio appropriato (condiviso o premium).</span><span class="sxs-lookup"><span data-stu-id="e8400-342">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="e8400-343">Un'istanza condivisa può subire limitazioni o ritardi di connessione maggiori rispetto alla media perché può essere usata da altri tenant del server condiviso.</span><span class="sxs-lookup"><span data-stu-id="e8400-343">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="e8400-344">Se sono necessarie prestazioni prevedibili o affidabili operazioni di bassa latenza, valutare la possibilità di scegliere il tipo servizio premium.</span><span class="sxs-lookup"><span data-stu-id="e8400-344">If predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="e8400-345">Per il database SQL di Azure non è consigliabile usare una strategia a intervalli fissi,</span><span class="sxs-lookup"><span data-stu-id="e8400-345">A fixed interval strategy is not recommended for use with Azure SQL Database.</span></span> <span data-ttu-id="e8400-346">bensì una strategia di backoff esponenziale, poiché il servizio può essere sovraccarico e intervalli più lunghi concedono più tempo per il ripristino del servizio.</span><span class="sxs-lookup"><span data-stu-id="e8400-346">Instead, use an exponential back-off strategy because the service may be overloaded, and longer delays allow more time for it to recover.</span></span>
* <span data-ttu-id="e8400-347">Quando si definiscono le connessioni, scegliere un valore appropriato per i timeout di connessione e dei comandi.</span><span class="sxs-lookup"><span data-stu-id="e8400-347">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="e8400-348">I timeout devono essere definiti in base alla progettazione della logica di business e tramite una serie di test.</span><span class="sxs-lookup"><span data-stu-id="e8400-348">Base the timeout on both your business logic design and through testing.</span></span> <span data-ttu-id="e8400-349">Potrebbe essere necessario modificare questi valori nel tempo man mano che i volumi dei dati o dei processi di business cambiano.</span><span class="sxs-lookup"><span data-stu-id="e8400-349">You may need to modify this value over time as the volumes of data or the business processes change.</span></span> <span data-ttu-id="e8400-350">Un timeout troppo breve può comportare errori di connessione prematuri quando il database è occupato,</span><span class="sxs-lookup"><span data-stu-id="e8400-350">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="e8400-351">mentre un timeout troppo lungo può impedire che la logica di ripetizione dei tentativi funzioni correttamente, poiché l'intervallo di attesa prima che venga rilevata una connessione non riuscita è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="e8400-351">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="e8400-352">Sebbene il valore di timeout sia un componente della latenza end-to-end, non è possibile determinare facilmente il numero di comandi che verranno eseguiti durante il salvataggio del contesto.</span><span class="sxs-lookup"><span data-stu-id="e8400-352">The value of the timeout is a component of the end-to-end latency, although you cannot easily determine how many commands will execute when saving the context.</span></span> <span data-ttu-id="e8400-353">È possibile modificare il timeout predefinito impostando la proprietà **CommandTimeout** dell'istanza **DbContext**.</span><span class="sxs-lookup"><span data-stu-id="e8400-353">You can change the default timeout by setting the **CommandTimeout** property of the **DbContext** instance.</span></span>
* <span data-ttu-id="e8400-354">Entity Framework supporta le configurazioni di ripetizione dei tentativi definite nei file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-354">Entity Framework supports retry configurations defined in configuration files.</span></span> <span data-ttu-id="e8400-355">Per ottenere la massima flessibilità in Azure, tuttavia, è opportuno valutare la possibilità di creare la configurazione a livello di codice all'interno dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-355">However, for maximum flexibility on Azure you should consider creating the configuration programmatically within the application.</span></span> <span data-ttu-id="e8400-356">I parametri specifici dei criteri di ripetizione dei tentativi, ad esempio il numero di tentativi e l'intervallo tra di essi, possono essere archiviati nel file di configurazione del servizio e usati in fase di esecuzione per creare i criteri appropriati.</span><span class="sxs-lookup"><span data-stu-id="e8400-356">The specific parameters for the retry policies, such as the number of retries and the retry intervals, can be stored in the service configuration file and used at runtime to create the appropriate policies.</span></span> <span data-ttu-id="e8400-357">In questo modo è possibile modificare le impostazioni senza dover riavviare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-357">This allows the settings to be changed without requiring the application to be restarted.</span></span>

<span data-ttu-id="e8400-358">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="e8400-358">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="e8400-359">Non è possibile specificare l'intervallo tra i tentativi (si tratta di un valore fisso generato come sequenza esponenziale);</span><span class="sxs-lookup"><span data-stu-id="e8400-359">You cannot specify the delay between retry attempts (it is fixed and generated as an exponential sequence).</span></span> <span data-ttu-id="e8400-360">è possibile specificare solo i valori massimi, come illustrato di seguito, a meno che non sia stata creata una strategia di ripetizione dei tentativi personalizzata.</span><span class="sxs-lookup"><span data-stu-id="e8400-360">You can specify only the maximum values, as shown here; unless you create a custom retry strategy.</span></span> <span data-ttu-id="e8400-361">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="e8400-361">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="e8400-362">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="e8400-362">**Context**</span></span> | <span data-ttu-id="e8400-363">**Destinazione di esempio E2E<br />latenza massima**</span><span class="sxs-lookup"><span data-stu-id="e8400-363">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="e8400-364">**Criteri di ripetizione**</span><span class="sxs-lookup"><span data-stu-id="e8400-364">**Retry policy**</span></span> | <span data-ttu-id="e8400-365">**Impostazioni**</span><span class="sxs-lookup"><span data-stu-id="e8400-365">**Settings**</span></span> | <span data-ttu-id="e8400-366">**Valori**</span><span class="sxs-lookup"><span data-stu-id="e8400-366">**Values**</span></span> | <span data-ttu-id="e8400-367">**Funzionamento**</span><span class="sxs-lookup"><span data-stu-id="e8400-367">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="e8400-368">Interattivo, interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="e8400-368">Interactive, UI,</span></span><br /><span data-ttu-id="e8400-369">o in primo piano</span><span class="sxs-lookup"><span data-stu-id="e8400-369">or foreground</span></span> |<span data-ttu-id="e8400-370">2 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-370">2 seconds</span></span> |<span data-ttu-id="e8400-371">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="e8400-371">Exponential</span></span> |<span data-ttu-id="e8400-372">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="e8400-372">MaxRetryCount</span></span><br /><span data-ttu-id="e8400-373">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="e8400-373">MaxDelay</span></span> |<span data-ttu-id="e8400-374">3</span><span class="sxs-lookup"><span data-stu-id="e8400-374">3</span></span><br /><span data-ttu-id="e8400-375">750 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-375">750 ms</span></span> |<span data-ttu-id="e8400-376">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-376">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="e8400-377">Tentativo 2 - intervallo di 750 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-377">Attempt 2 - delay 750 ms</span></span><br /><span data-ttu-id="e8400-378">Tentativo 3 - intervallo di 750 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-378">Attempt 3 – delay 750 ms</span></span> |
| <span data-ttu-id="e8400-379">Background</span><span class="sxs-lookup"><span data-stu-id="e8400-379">Background</span></span><br /> <span data-ttu-id="e8400-380">o batch</span><span class="sxs-lookup"><span data-stu-id="e8400-380">or batch</span></span> |<span data-ttu-id="e8400-381">30 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-381">30 seconds</span></span> |<span data-ttu-id="e8400-382">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="e8400-382">Exponential</span></span> |<span data-ttu-id="e8400-383">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="e8400-383">MaxRetryCount</span></span><br /><span data-ttu-id="e8400-384">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="e8400-384">MaxDelay</span></span> |<span data-ttu-id="e8400-385">5</span><span class="sxs-lookup"><span data-stu-id="e8400-385">5</span></span><br /><span data-ttu-id="e8400-386">12 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-386">12 seconds</span></span> |<span data-ttu-id="e8400-387">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-387">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="e8400-388">Tentativo 2 - intervallo di ~1 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-388">Attempt 2 - delay ~1 sec</span></span><br /><span data-ttu-id="e8400-389">Tentativo 3 - intervallo di ~3 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-389">Attempt 3 - delay ~3 sec</span></span><br /><span data-ttu-id="e8400-390">Tentativo 4 - intervallo di ~7 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-390">Attempt 4 - delay ~7 sec</span></span><br /><span data-ttu-id="e8400-391">Tentativo 5 - intervallo di 12 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-391">Attempt 5 - delay 12 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="e8400-392">Gli obiettivi di latenza end-to-end presuppongono il timeout predefinito per le connessioni al servizio.</span><span class="sxs-lookup"><span data-stu-id="e8400-392">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="e8400-393">Se si specifica un timeout di connessione più lungo, la latenza end-to-end verrà estesa di questo intervallo di tempo aggiuntivo per ogni nuovo tentativo.</span><span class="sxs-lookup"><span data-stu-id="e8400-393">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="e8400-394">esempi</span><span class="sxs-lookup"><span data-stu-id="e8400-394">Examples</span></span>
<span data-ttu-id="e8400-395">L'esempio di codice seguente definisce una soluzione di accesso ai dati semplice che usa Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e8400-395">The following code example defines a simple data access solution that uses Entity Framework.</span></span> <span data-ttu-id="e8400-396">Imposta una strategia di ripetizione dei tentativi specifica tramite la definizione di un'istanza di una classe denominata **BlogConfiguration** che estende **DbConfiguration**.</span><span class="sxs-lookup"><span data-stu-id="e8400-396">It sets a specific retry strategy by defining an instance of a class named **BlogConfiguration** that extends **DbConfiguration**.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
    public class BlogConfiguration : DbConfiguration
    {
        public BlogConfiguration()
        {
            // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
            // These values could be loaded from configuration rather than being hard-coded.
            this.SetExecutionStrategy(
                    "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
        }
    }

    // Specify the configuration type if more than one has been defined.
    // [DbConfigurationType(typeof(BlogConfiguration))]
    public class BloggingContext : DbContext
    {
        // Definition of content goes here.
    }

    class EF6CodeSamples
    {
        public async static Task Samples()
        {
            // Execution strategy configured by DbConfiguration subclass, discovered automatically or
            // or explicitly indicated through configuration or with an attribute. Default is no retries.
            using (var db = new BloggingContext("Blogs"))
            {
                // Add, edit, delete blog items here, then:
                await db.SaveChangesAsync();
            }
        }
    }
}
```

<span data-ttu-id="e8400-397">Altri esempi sull'uso del meccanismo di ripetizione dei tentavi in Entity Framework sono disponibili in [resilienza delle connessioni / logica di ripetizione dei tentativi](http://msdn.microsoft.com/data/dn456835.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-397">More examples of using the Entity Framework retry mechanism can be found in [Connection Resiliency / Retry Logic](http://msdn.microsoft.com/data/dn456835.aspx).</span></span>

### <a name="more-information"></a><span data-ttu-id="e8400-398">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-398">More information</span></span>
* [<span data-ttu-id="e8400-399">Guida relativa all'elasticità e alle prestazioni del database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="e8400-399">Azure SQL Database Performance and Elasticity Guide</span></span>](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## <a name="sql-database-using-entity-framework-core-retry-guidelines"></a><span data-ttu-id="e8400-400">Database SQL con Entity Framework Core - Linee guida per la ripetizione di tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-400">SQL Database using Entity Framework Core retry guidelines</span></span>
<span data-ttu-id="e8400-401">[Entity Framework Core](/ef/core/) è un mapper relazionale a oggetti che consente agli sviluppatori .NET Core di usare dati mediante oggetti specifici di dominio.</span><span class="sxs-lookup"><span data-stu-id="e8400-401">[Entity Framework Core](/ef/core/) is an object-relational mapper that enables .NET Core developers to work with data using domain-specific objects.</span></span> <span data-ttu-id="e8400-402">Elimina la necessità di gran parte del codice di accesso ai dati che in genere gli sviluppatori devono scrivere.</span><span class="sxs-lookup"><span data-stu-id="e8400-402">It eliminates the need for most of the data-access code that developers usually need to write.</span></span> <span data-ttu-id="e8400-403">Questa versione di Entity Framework è stata scritta da zero e non eredita automaticamente tutte le funzionalità di EF6.x.</span><span class="sxs-lookup"><span data-stu-id="e8400-403">This version of Entity Framework was written from the ground up, and doesn't automatically inherit all the features from EF6.x.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-404">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-404">Retry mechanism</span></span>
<span data-ttu-id="e8400-405">Quando si accede al database SQL con Entity Framework Core, il supporto per la ripetizione di tentativi viene offerto attraverso un meccanismo denominato [Resilienza connessione](/ef/core/miscellaneous/connection-resiliency).</span><span class="sxs-lookup"><span data-stu-id="e8400-405">Retry support is provided when accessing SQL Database using Entity Framework Core through a mechanism called [Connection Resiliency](/ef/core/miscellaneous/connection-resiliency).</span></span> <span data-ttu-id="e8400-406">Resilienza connessione è stato introdotto in EF Core 1.1.0.</span><span class="sxs-lookup"><span data-stu-id="e8400-406">Connection resiliency was introduced in EF Core 1.1.0.</span></span>

<span data-ttu-id="e8400-407">La principale astrazione è l'interfaccia `IExecutionStrategy`.</span><span class="sxs-lookup"><span data-stu-id="e8400-407">The primary abstraction is the `IExecutionStrategy` interface.</span></span> <span data-ttu-id="e8400-408">La strategia di esecuzione per SQL Server, che include SQL Azure, riconosce i tipi di eccezione per cui è possibile ripetere i tentativi e dispone di impostazioni predefinite ragionevoli per un numero massimo ripetizione dei tentativi, intervalli tra di essi e così via.</span><span class="sxs-lookup"><span data-stu-id="e8400-408">The execution strategy for SQL Server, including SQL Azure, is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, and so on.</span></span>

### <a name="examples"></a><span data-ttu-id="e8400-409">esempi</span><span class="sxs-lookup"><span data-stu-id="e8400-409">Examples</span></span>

<span data-ttu-id="e8400-410">Il codice seguente abilita le ripetizioni automatiche di tentativi quando si configura l'oggetto DbContext, che rappresenta una sessione con il database.</span><span class="sxs-lookup"><span data-stu-id="e8400-410">The following code enables automatic retries when configuring the DbContext object, which represents a session with the database.</span></span> 

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(
            @"Server=(localdb)\mssqllocaldb;Database=EFMiscellanous.ConnectionResiliency;Trusted_Connection=True;",
            options => options.EnableRetryOnFailure());
}
```

<span data-ttu-id="e8400-411">Il codice seguente illustra come eseguire una transazione con la ripetizione di tentativi automatica, usando una strategia di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e8400-411">The following code shows how to execute a transaction with automatic retries, by using an execution strategy.</span></span> <span data-ttu-id="e8400-412">La transazione viene definita in un delegato.</span><span class="sxs-lookup"><span data-stu-id="e8400-412">The transaction is defined in a delegate.</span></span> <span data-ttu-id="e8400-413">Se si verifica un errore temporaneo, la strategia di esecuzione chiamerà nuovamente il delegato.</span><span class="sxs-lookup"><span data-stu-id="e8400-413">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

```csharp
using (var db = new BloggingContext())
{
    var strategy = db.Database.CreateExecutionStrategy();

    strategy.Execute(() =>
    {
        using (var transaction = db.Database.BeginTransaction())
        {
            db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
            db.SaveChanges();

            db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/visualstudio" });
            db.SaveChanges();

            transaction.Commit();
        }
    });
}
```

### <a name="more-information"></a><span data-ttu-id="e8400-414">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-414">More information</span></span>
* [<span data-ttu-id="e8400-415">Resilienza connessione</span><span class="sxs-lookup"><span data-stu-id="e8400-415">Connection Resiliency</span></span>](/ef/core/miscellaneous/connection-resiliency)
* [<span data-ttu-id="e8400-416">Punto dati - EF Core 1.1</span><span class="sxs-lookup"><span data-stu-id="e8400-416">Data Points - EF Core 1.1</span></span>](https://msdn.microsoft.com/en-us/magazine/mt745093.aspx)

## <a name="sql-database-using-adonet-retry-guidelines"></a><span data-ttu-id="e8400-417">Database SQL con ADO.NET - Linee guida per la ripetizione di tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-417">SQL Database using ADO.NET retry guidelines</span></span>
<span data-ttu-id="e8400-418">Il database SQL, in questo caso, è un database SQL ospitato, di varie dimensioni, disponibile come servizio standard (condiviso) o premium (non condiviso).</span><span class="sxs-lookup"><span data-stu-id="e8400-418">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-419">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-419">Retry mechanism</span></span>
<span data-ttu-id="e8400-420">Il database SQL non offre il supporto incorporato per la ripetizione dei tentativi quando si accede ad esso con ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="e8400-420">SQL Database has no built-in support for retries when accessed using ADO.NET.</span></span> <span data-ttu-id="e8400-421">Per determinare il motivo per cui una richiesta ha avuto esito negativo, è possibile usare i codici restituiti dalle richieste.</span><span class="sxs-lookup"><span data-stu-id="e8400-421">However, the return codes from requests can be used to determine why a request failed.</span></span> <span data-ttu-id="e8400-422">Per altre informazioni sulla limitazione delle richieste del database SQL, vedere [Limiti delle risorse del database SQL di Azure](/azure/sql-database/sql-database-resource-limits).</span><span class="sxs-lookup"><span data-stu-id="e8400-422">For more information about SQL Database throttling, see [Azure SQL Database resource limits](/azure/sql-database/sql-database-resource-limits).</span></span> <span data-ttu-id="e8400-423">Per un elenco dei codici di errore pertinenti, vedere [Codici di errore SQL per le applicazioni client del database SQL](/azure/sql-database/sql-database-develop-error-messages).</span><span class="sxs-lookup"><span data-stu-id="e8400-423">For a list of relevant error codes, see [SQL error codes for SQL Database client applications](/azure/sql-database/sql-database-develop-error-messages).</span></span>

<span data-ttu-id="e8400-424">È possibile usare la libreria Polly per implementare la ripetizione di tentativi per il database SQL.</span><span class="sxs-lookup"><span data-stu-id="e8400-424">You can use the Polly library to implement retries for SQL Database.</span></span> <span data-ttu-id="e8400-425">Vedere [Gestione degli errori temporanei con Polly](#transient-fault-handling-with-polly).</span><span class="sxs-lookup"><span data-stu-id="e8400-425">See [Transient fault handling with Polly](#transient-fault-handling-with-polly).</span></span>

### <a name="retry-usage-guidance"></a><span data-ttu-id="e8400-426">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-426">Retry usage guidance</span></span>
<span data-ttu-id="e8400-427">Quando si accede al database SQL con ADO.NET, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="e8400-427">Consider the following guidelines when accessing SQL Database using ADO.NET:</span></span>

* <span data-ttu-id="e8400-428">Scegliere il tipo di servizio appropriato (condiviso o premium).</span><span class="sxs-lookup"><span data-stu-id="e8400-428">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="e8400-429">Un'istanza condivisa può subire limitazioni o ritardi di connessione maggiori rispetto alla media perché può essere usata da altri tenant del server condiviso.</span><span class="sxs-lookup"><span data-stu-id="e8400-429">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="e8400-430">Se sono necessarie prestazioni più prevedibili o affidabili operazioni di bassa latenza, valutare la possibilità di scegliere il tipo servizio premium.</span><span class="sxs-lookup"><span data-stu-id="e8400-430">If more predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
* <span data-ttu-id="e8400-431">Assicurarsi di eseguire i nuovi tentativi al livello o nell'ambito appropriato per evitare operazioni non idempotenti che possono causare incoerenze nei dati.</span><span class="sxs-lookup"><span data-stu-id="e8400-431">Ensure that you perform retries at the appropriate level or scope to avoid non-idempotent operations causing inconsistency in the data.</span></span> <span data-ttu-id="e8400-432">In teoria, tutte le operazioni dovrebbero essere idempotenti in modo da poter essere ripetute senza causare incoerenze.</span><span class="sxs-lookup"><span data-stu-id="e8400-432">Ideally, all operations should be idempotent so that they can be repeated without causing inconsistency.</span></span> <span data-ttu-id="e8400-433">Se questo non avviene, i nuovi tentativi devono essere eseguiti a un livello o in un ambito che consenta l'annullamento di tutte le modifiche correlate in caso di esito negativo di un'operazione, ad esempio un ambito transazionale.</span><span class="sxs-lookup"><span data-stu-id="e8400-433">Where this is not the case, the retry should be performed at a level or scope that allows all related changes to be undone if one operation fails; for example, from within a transactional scope.</span></span> <span data-ttu-id="e8400-434">Per altre informazioni, vedere l'articolo sul [livello di accesso ai dati di Cloud Service Fundamentals e la gestione degli errori temporanei](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).</span><span class="sxs-lookup"><span data-stu-id="e8400-434">For more information, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).</span></span>
* <span data-ttu-id="e8400-435">Non è consigliabile una strategia basata su intervalli fissi per il database SQL di Azure, se non negli scenari interattivi in cui vengono eseguiti solo alcuni tentativi a intervalli molto brevi.</span><span class="sxs-lookup"><span data-stu-id="e8400-435">A fixed interval strategy is not recommended for use with Azure SQL Database except for interactive scenarios where there are only a few retries at very short intervals.</span></span> <span data-ttu-id="e8400-436">In alternativa, valutare la possibilità di usare una strategia di backoff esponenziale per la maggior parte degli scenari.</span><span class="sxs-lookup"><span data-stu-id="e8400-436">Instead, consider using an exponential back-off strategy for the majority of scenarios.</span></span>
* <span data-ttu-id="e8400-437">Quando si definiscono le connessioni, scegliere un valore appropriato per i timeout di connessione e dei comandi.</span><span class="sxs-lookup"><span data-stu-id="e8400-437">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="e8400-438">Un timeout troppo breve può comportare errori di connessione prematuri quando il database è occupato,</span><span class="sxs-lookup"><span data-stu-id="e8400-438">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="e8400-439">mentre un timeout troppo lungo può impedire che la logica di ripetizione dei tentativi funzioni correttamente, poiché l'intervallo di attesa prima che venga rilevata una connessione non riuscita è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="e8400-439">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="e8400-440">Il valore di timeout è un componente della latenza end-to-end; per ogni nuovo tentativo, viene aggiunto all'intervallo tra i tentativi specificato nei criteri di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-440">The value of the timeout is a component of the end-to-end latency; it is effectively added to the retry delay specified in the retry policy for every retry attempt.</span></span>
* <span data-ttu-id="e8400-441">Chiudere la connessione dopo un certo numero di nuovi tentativi, anche se si usa una logica di ripetizione dei tentativi con backoff esponenziale, e ripetere l'operazione su una nuova connessione.</span><span class="sxs-lookup"><span data-stu-id="e8400-441">Close the connection after a certain number of retries, even when using an exponential back off retry logic, and retry the operation on a new connection.</span></span> <span data-ttu-id="e8400-442">Se si ripete più volte la stessa operazione su un'unica connessione, infatti, è possibile che si verifichino problemi di connessione.</span><span class="sxs-lookup"><span data-stu-id="e8400-442">Retrying the same operation multiple times on the same connection can be a factor that contributes to connection problems.</span></span> <span data-ttu-id="e8400-443">Per altre informazioni su questa tecnica, vedere l'articolo sul [livello di accesso ai dati di Cloud Service Fundamentals e la gestione degli errori temporanei](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-443">For an example of this technique, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).</span></span>
* <span data-ttu-id="e8400-444">Se si usa il pool di connessioni (impostazione predefinita), è probabile che venga scelta la stessa connessione, anche dopo aver chiuso e riaperto una connessione.</span><span class="sxs-lookup"><span data-stu-id="e8400-444">When connection pooling is in use (the default) there is a chance that the same connection will be chosen from the pool, even after closing and reopening a connection.</span></span> <span data-ttu-id="e8400-445">Se si verifica questo problema, è possibile risolverlo chiamando il metodo **ClearPool** della classe **SqlConnection** per contrassegnare la connessione come non riutilizzabile.</span><span class="sxs-lookup"><span data-stu-id="e8400-445">If this is the case, a technique to resolve it is to call the **ClearPool** method of the **SqlConnection** class to mark the connection as not reusable.</span></span> <span data-ttu-id="e8400-446">Questo metodo, tuttavia, deve essere adottato solo dopo vari tentativi di connessione non riusciti e solo quando è presente la classe di errori temporanei, come i timeout SQL (codice di errore -2), specifica delle connessioni difettose.</span><span class="sxs-lookup"><span data-stu-id="e8400-446">However, you should do this only after several connection attempts have failed, and only when encountering the specific class of transient failures such as SQL timeouts (error code -2) related to faulty connections.</span></span>
* <span data-ttu-id="e8400-447">Se il codice di accesso ai dati usa transazioni avviate come istanze **TransactionScope** , la logica di ripetizione dei tentativi deve riaprire la connessione e avviare un nuovo ambito della transazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-447">If the data access code uses transactions initiated as **TransactionScope** instances, the retry logic should reopen the connection and initiate a new transaction scope.</span></span> <span data-ttu-id="e8400-448">Per questo motivo, il blocco di codice non irreversibile deve interessare l'intero ambito della transazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-448">For this reason, the retryable code block should encompass the entire scope of the transaction.</span></span>

<span data-ttu-id="e8400-449">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="e8400-449">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="e8400-450">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="e8400-450">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="e8400-451">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="e8400-451">**Context**</span></span> | <span data-ttu-id="e8400-452">**Destinazione di esempio E2E<br />latenza massima**</span><span class="sxs-lookup"><span data-stu-id="e8400-452">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="e8400-453">**Strategia di ripetizione dei tentativi**</span><span class="sxs-lookup"><span data-stu-id="e8400-453">**Retry strategy**</span></span> | <span data-ttu-id="e8400-454">**Impostazioni**</span><span class="sxs-lookup"><span data-stu-id="e8400-454">**Settings**</span></span> | <span data-ttu-id="e8400-455">**Valori**</span><span class="sxs-lookup"><span data-stu-id="e8400-455">**Values**</span></span> | <span data-ttu-id="e8400-456">**Funzionamento**</span><span class="sxs-lookup"><span data-stu-id="e8400-456">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="e8400-457">Interattivo, interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="e8400-457">Interactive, UI,</span></span><br /><span data-ttu-id="e8400-458">o in primo piano</span><span class="sxs-lookup"><span data-stu-id="e8400-458">or foreground</span></span> |<span data-ttu-id="e8400-459">2 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-459">2 sec</span></span> |<span data-ttu-id="e8400-460">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="e8400-460">FixedInterval</span></span> |<span data-ttu-id="e8400-461">Numero tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-461">Retry count</span></span><br /><span data-ttu-id="e8400-462">Intervallo tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-462">Retry interval</span></span><br /><span data-ttu-id="e8400-463">Primo tentativo rapido</span><span class="sxs-lookup"><span data-stu-id="e8400-463">First fast retry</span></span> |<span data-ttu-id="e8400-464">3</span><span class="sxs-lookup"><span data-stu-id="e8400-464">3</span></span><br /><span data-ttu-id="e8400-465">500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-465">500 ms</span></span><br /><span data-ttu-id="e8400-466">true</span><span class="sxs-lookup"><span data-stu-id="e8400-466">true</span></span> |<span data-ttu-id="e8400-467">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-467">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="e8400-468">Tentativo 2 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-468">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="e8400-469">Tentativo 3 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-469">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="e8400-470">Background</span><span class="sxs-lookup"><span data-stu-id="e8400-470">Background</span></span><br /><span data-ttu-id="e8400-471">o batch</span><span class="sxs-lookup"><span data-stu-id="e8400-471">or batch</span></span> |<span data-ttu-id="e8400-472">30 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-472">30 sec</span></span> |<span data-ttu-id="e8400-473">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="e8400-473">ExponentialBackoff</span></span> |<span data-ttu-id="e8400-474">Numero tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-474">Retry count</span></span><br /><span data-ttu-id="e8400-475">Interruzione temporanea minima</span><span class="sxs-lookup"><span data-stu-id="e8400-475">Min back-off</span></span><br /><span data-ttu-id="e8400-476">Interruzione temporanea massima</span><span class="sxs-lookup"><span data-stu-id="e8400-476">Max back-off</span></span><br /><span data-ttu-id="e8400-477">Interruzione temporanea delta</span><span class="sxs-lookup"><span data-stu-id="e8400-477">Delta back-off</span></span><br /><span data-ttu-id="e8400-478">Primo tentativo rapido</span><span class="sxs-lookup"><span data-stu-id="e8400-478">First fast retry</span></span> |<span data-ttu-id="e8400-479">5</span><span class="sxs-lookup"><span data-stu-id="e8400-479">5</span></span><br /><span data-ttu-id="e8400-480">0 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-480">0 sec</span></span><br /><span data-ttu-id="e8400-481">60 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-481">60 sec</span></span><br /><span data-ttu-id="e8400-482">2 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-482">2 sec</span></span><br /><span data-ttu-id="e8400-483">false</span><span class="sxs-lookup"><span data-stu-id="e8400-483">false</span></span> |<span data-ttu-id="e8400-484">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-484">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="e8400-485">Tentativo 2 - intervallo di ~2 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-485">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="e8400-486">Tentativo 3 - intervallo di ~6 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-486">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="e8400-487">Tentativo 4 - intervallo di ~14 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-487">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="e8400-488">Tentativo 5 - intervallo di 30 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-488">Attempt 5 - delay ~30 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="e8400-489">Gli obiettivi di latenza end-to-end presuppongono il timeout predefinito per le connessioni al servizio.</span><span class="sxs-lookup"><span data-stu-id="e8400-489">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="e8400-490">Se si specifica un timeout di connessione più lungo, la latenza end-to-end verrà estesa di questo intervallo di tempo aggiuntivo per ogni nuovo tentativo.</span><span class="sxs-lookup"><span data-stu-id="e8400-490">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>
>
>

### <a name="examples"></a><span data-ttu-id="e8400-491">esempi</span><span class="sxs-lookup"><span data-stu-id="e8400-491">Examples</span></span>
<span data-ttu-id="e8400-492">Questa sezione illustra come usare Polly per accedere al database SQL di Azure usando un set di criteri di ripetizione dei tentativi configurato nella classe `Policy`.</span><span class="sxs-lookup"><span data-stu-id="e8400-492">This section shows how you can use Polly to access Azure SQL Database using a set of retry policies configured in the `Policy` class.</span></span>

<span data-ttu-id="e8400-493">Il codice seguente illustra un metodo di estensione nella classe `SqlCommand` che richiama `ExecuteAsync` con backoff esponenziale.</span><span class="sxs-lookup"><span data-stu-id="e8400-493">The following code shows an extension method on the `SqlCommand` class that calls `ExecuteAsync` with exponential backoff.</span></span>

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command)
{
    GuardConnectionIsNotNull(command);

    var policy = Policy.Handle<Exception>().WaitAndRetryAsync(
        retryCount: 3, // Retry 3 times
        sleepDurationProvider: attempt => TimeSpan.FromMilliseconds(200 * Math.Pow(2, attempt - 1)), // Exponential backoff based on an initial 200ms delay.
        onRetry: (exception, attempt) => 
        {
            // Capture some info for logging/telemetry.  
            logger.LogWarn($"ExecuteReaderWithRetryAsync: Retry {attempt} due to {exception}.");
        });

    // Retry the following call according to the policy.
    await policy.ExecuteAsync<SqlDataReader>(async token =>
    {
        // This code is executed within the Policy 

        if (conn.State != System.Data.ConnectionState.Open) await conn.OpenAsync(token);
        return await command.ExecuteReaderAsync(System.Data.CommandBehavior.Default, token);

    }, cancellationToken);
}

```

<span data-ttu-id="e8400-494">Questo metodo di estensione asincrona può essere usato come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="e8400-494">This asynchronous extension method can be used as follows.</span></span>

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync())
{
    // Do something with the values
}
```

### <a name="more-information"></a><span data-ttu-id="e8400-495">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-495">More information</span></span>
* [<span data-ttu-id="e8400-496">Livello di accesso ai dati di Cloud Service Fundamentals e gestione degli errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="e8400-496">Cloud Service Fundamentals Data Access Layer – Transient Fault Handling</span></span>](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

<span data-ttu-id="e8400-497">Per indicazioni generali su come ottenere il massimo dal database SQL, vedere la [guida all'elasticità e alle prestazioni del database SQL di Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-497">For general guidance on getting the most from SQL Database, see [Azure SQL Database Performance and Elasticity Guide](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx).</span></span>


## <a name="service-bus-retry-guidelines"></a><span data-ttu-id="e8400-498">Bus di servizio - Linee guida per la ripetizione di tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-498">Service Bus retry guidelines</span></span>
<span data-ttu-id="e8400-499">Il bus di servizio è una piattaforma di messaggistica basata sul cloud che offre una funzione di scambio dei messaggi di tipo "loosely coupled" con alti livelli di scalabilità e resilienza per i componenti di un'applicazione (ospitata nel cloud o in locale).</span><span class="sxs-lookup"><span data-stu-id="e8400-499">Service Bus is a cloud messaging platform that provides loosely coupled message exchange with improved scale and resiliency for components of an application, whether hosted in the cloud or on-premises.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-500">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-500">Retry mechanism</span></span>
<span data-ttu-id="e8400-501">Il bus di servizio implementa la ripetizione dei tentativi usando implementazioni della classe di base [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) .</span><span class="sxs-lookup"><span data-stu-id="e8400-501">Service Bus implements retries using implementations of the [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx) base class.</span></span> <span data-ttu-id="e8400-502">Tutti i client del bus di servizio espongono una proprietà **RetryPolicy** che può essere impostata in una delle implementazioni della classe di base **RetryPolicy**.</span><span class="sxs-lookup"><span data-stu-id="e8400-502">All of the Service Bus clients expose a **RetryPolicy** property that can be set to one of the implementations of the **RetryPolicy** base class.</span></span> <span data-ttu-id="e8400-503">Le implementazioni predefinite sono:</span><span class="sxs-lookup"><span data-stu-id="e8400-503">The built-in implementations are:</span></span>

* <span data-ttu-id="e8400-504">La [classe RetryExponential](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-504">The [RetryExponential Class](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx).</span></span> <span data-ttu-id="e8400-505">Espone proprietà che controllano l'intervallo di backoff, il numero di tentativi e la proprietà **TerminationTimeBuffer** usata per limitare il tempo complessivo per il completamento dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-505">This exposes properties that control the back-off interval, the retry count, and the **TerminationTimeBuffer** property that is used to limit the total time for the operation to complete.</span></span>
* <span data-ttu-id="e8400-506">La [classe NoRetry](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-506">The [NoRetry Class](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx).</span></span> <span data-ttu-id="e8400-507">Viene usata quando non è necessario eseguire nuovi tentativi al livello dell'API del bus di servizio, ad esempio quando i nuovi tentativi vengono gestiti da un altro processo nell'ambito di un'operazione in batch o composta da più passaggi.</span><span class="sxs-lookup"><span data-stu-id="e8400-507">This is used when retries at the Service Bus API level are not required, such as when retries are managed by another process as part of a batch or multiple step operation.</span></span>

<span data-ttu-id="e8400-508">Alcune azioni del bus di servizio possono restituire un intervallo di eccezioni, come elencato in [Appendice: eccezioni di messaggistica](http://msdn.microsoft.com/library/hh418082.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-508">Service Bus actions can return a range of exceptions, as listed in [Appendix: Messaging Exceptions](http://msdn.microsoft.com/library/hh418082.aspx).</span></span> <span data-ttu-id="e8400-509">L'elenco fornisce informazioni su quali delle eccezioni indicano che è opportuno ripetere l'operazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-509">The list provides information about which if these indicate that retrying the operation is appropriate.</span></span> <span data-ttu-id="e8400-510">[ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) , ad esempio, indica che il client deve attendere un determinato intervallo di tempo, quindi ripetere l'operazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-510">For example, a [ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) indicates that the client should wait for a period of time, then retry the operation.</span></span> <span data-ttu-id="e8400-511">Un'occorrenza di **ServerBusyException** induce inoltre il bus di servizio a passare a una modalità diversa, in cui viene aggiunto un ulteriore intervallo di 10 secondi all'intervallo tra i tentativi elaborato.</span><span class="sxs-lookup"><span data-stu-id="e8400-511">The occurrence of a **ServerBusyException** also causes Service Bus to switch to a different mode, in which an extra 10-second delay is added to the computed retry delays.</span></span> <span data-ttu-id="e8400-512">Questa modalità viene reimpostata dopo un breve intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="e8400-512">This mode is reset after a short period.</span></span>

<span data-ttu-id="e8400-513">Le eccezioni restituite dal bus di servizio espongono la proprietà **IsTransient** che indica se il client deve ripetere l'operazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-513">The exceptions returned from Service Bus expose the **IsTransient** property that indicates if the client should retry the operation.</span></span> <span data-ttu-id="e8400-514">I criteri **RetryExponential** incorporati si basano sulla proprietà **IsTransient** della classe **MessagingException**, che costituisce la classe di base per tutte le eccezioni del bus di servizio.</span><span class="sxs-lookup"><span data-stu-id="e8400-514">The built-in **RetryExponential** policy relies on the **IsTransient** property in the **MessagingException** class, which is the base class for all Service Bus exceptions.</span></span> <span data-ttu-id="e8400-515">Se si creano implementazioni personalizzate della classe di base **RetryPolicy**, è possibile usare una combinazione del tipo di eccezione e della proprietà **IsTransient** per ottenere un controllo più preciso sulle azioni di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-515">If you create custom implementations of the **RetryPolicy** base class you could use a combination of the exception type and the **IsTransient** property to provide more fine-grained control over retry actions.</span></span> <span data-ttu-id="e8400-516">Se si identifica un'eccezione di tipo **QuotaExceededException** , ad esempio, è possibile intraprendere le azioni necessarie per esaurire la coda prima di riprovare a inviare un messaggio.</span><span class="sxs-lookup"><span data-stu-id="e8400-516">For example, you could detect a **QuotaExceededException** and take action to drain the queue before retrying sending a message to it.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="e8400-517">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="e8400-517">Policy configuration</span></span>
<span data-ttu-id="e8400-518">I criteri di ripetizione dei tentativi vengono impostati a livello di codice e possono essere impostati come criteri predefiniti per **NamespaceManager** e **MessagingFactory** oppure impostati singolarmente per ogni client di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="e8400-518">Retry policies are set programmatically, and can be set as a default policy for a **NamespaceManager** and for a **MessagingFactory**, or individually for each messaging client.</span></span> <span data-ttu-id="e8400-519">Per impostare criteri di ripetizione dei tentativi predefiniti per una sessione di messaggistica, impostare la proprietà **RetryPolicy** di **NamespaceManager**.</span><span class="sxs-lookup"><span data-stu-id="e8400-519">To set the default retry policy for a messaging session you set the **RetryPolicy** of the **NamespaceManager**.</span></span>

    namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                                 maxBackoff: TimeSpan.FromSeconds(30),
                                                                 maxRetryCount: 3);

<span data-ttu-id="e8400-520">Per impostare criteri di ripetizione dei tentativi predefiniti per tutti i client da una factory di messaggistica, impostare la proprietà **RetryPolicy** di **MessagingFactory**.</span><span class="sxs-lookup"><span data-stu-id="e8400-520">To set the default retry policy for all clients created from a messaging factory, you set the **RetryPolicy** of the **MessagingFactory**.</span></span>

    messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                        maxBackoff: TimeSpan.FromSeconds(30),
                                                        maxRetryCount: 3);

<span data-ttu-id="e8400-521">Per impostare criteri di ripetizione dei tentativi per un client di messaggistica o per ignorare i criteri predefiniti, impostarne la proprietà **RetryPolicy** usando un'istanza della classe di criteri necessaria:</span><span class="sxs-lookup"><span data-stu-id="e8400-521">To set the retry policy for a messaging client, or to override its default policy, you set its **RetryPolicy** property using an instance of the required policy class:</span></span>

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                            maxBackoff: TimeSpan.FromSeconds(30),
                                            maxRetryCount: 3);
```

<span data-ttu-id="e8400-522">I criteri di ripetizione dei tentativi non possono essere impostati a livello di singola operazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-522">The retry policy cannot be set at the individual operation level.</span></span> <span data-ttu-id="e8400-523">Questo vale per tutte le operazioni relative al client di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="e8400-523">It applies to all operations for the messaging client.</span></span>
<span data-ttu-id="e8400-524">La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.</span><span class="sxs-lookup"><span data-stu-id="e8400-524">The following table shows the default settings for the built-in retry policy.</span></span>

![Tabella del materiale sussidiario sulla ripetizione di tentativi](./images/retry-service-specific/RetryServiceSpecificGuidanceTable7.png)

### <a name="retry-usage-guidance"></a><span data-ttu-id="e8400-526">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-526">Retry usage guidance</span></span>
<span data-ttu-id="e8400-527">Quando si usa il bus di servizio, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="e8400-527">Consider the following guidelines when using Service Bus:</span></span>

* <span data-ttu-id="e8400-528">Se si sceglie l'implementazione **RetryExponential** incorporata, non implementare un'operazione di fallback poiché i criteri reagiscono alle eccezioni di tipo "server occupato" e viene automaticamente attivata una modalità di ripetizione dei tentativi appropriata al nuovo scenario.</span><span class="sxs-lookup"><span data-stu-id="e8400-528">When using the built-in **RetryExponential** implementation, do not implement a fallback operation as the policy reacts to Server Busy exceptions and automatically switches to an appropriate retry mode.</span></span>
* <span data-ttu-id="e8400-529">Il bus di servizio supporta una funzionalità relativa agli spazi dei nomi associati che implementa il failover automatico su una coda di backup di uno spazio dei nomi diverso in caso di esito negativo della coda nello spazio dei nomi primario.</span><span class="sxs-lookup"><span data-stu-id="e8400-529">Service Bus supports a feature called Paired Namespaces, which implements automatic failover to a backup queue in a separate namespace if the queue in the primary namespace fails.</span></span> <span data-ttu-id="e8400-530">I messaggi ricevuti dalla coda secondaria possono essere nuovamente inviati alla coda primaria nel momento in cui viene ripristinata.</span><span class="sxs-lookup"><span data-stu-id="e8400-530">Messages from the secondary queue can be sent back to the primary queue when it recovers.</span></span> <span data-ttu-id="e8400-531">Questa funzionalità consente di risolvere eventuali problemi temporanei.</span><span class="sxs-lookup"><span data-stu-id="e8400-531">This feature helps to address transient failures.</span></span> <span data-ttu-id="e8400-532">Per altre informazioni, vedere [Modelli di messaggistica asincrona e disponibilità elevata](http://msdn.microsoft.com/library/azure/dn292562.aspx).</span><span class="sxs-lookup"><span data-stu-id="e8400-532">For more information, see [Asynchronous Messaging Patterns and High Availability](http://msdn.microsoft.com/library/azure/dn292562.aspx).</span></span>

<span data-ttu-id="e8400-533">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="e8400-533">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="e8400-534">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="e8400-534">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

![Tabella del materiale sussidiario sulla ripetizione di tentativi](./images/retry-service-specific/RetryServiceSpecificGuidanceTable8.png)

### <a name="telemetry"></a><span data-ttu-id="e8400-536">Telemetria</span><span class="sxs-lookup"><span data-stu-id="e8400-536">Telemetry</span></span>
<span data-ttu-id="e8400-537">Il bus di servizio registra i tentativi come eventi ETW tramite un oggetto **EventSource**.</span><span class="sxs-lookup"><span data-stu-id="e8400-537">Service Bus logs retries as ETW events using an **EventSource**.</span></span> <span data-ttu-id="e8400-538">È necessario associare un **EventListener** all'origine eventi per acquisire gli eventi e visualizzarli nel visualizzatore delle prestazioni o scriverli in un log di destinazione appropriato.</span><span class="sxs-lookup"><span data-stu-id="e8400-538">You must attach an **EventListener** to the event source to capture the events and view them in Performance Viewer, or write them to a suitable destination log.</span></span> <span data-ttu-id="e8400-539">A questo scopo, è possibile usare il [blocco applicativo di registrazione semantica](http://msdn.microsoft.com/library/dn775006.aspx) .</span><span class="sxs-lookup"><span data-stu-id="e8400-539">You could use the [Semantic Logging Application Block](http://msdn.microsoft.com/library/dn775006.aspx) to do this.</span></span> <span data-ttu-id="e8400-540">Gli eventi di ripetizione dei tentativi sono caratterizzati dal formato seguente:</span><span class="sxs-lookup"><span data-stu-id="e8400-540">The retry events are of the following form:</span></span>

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### <a name="examples"></a><span data-ttu-id="e8400-541">esempi</span><span class="sxs-lookup"><span data-stu-id="e8400-541">Examples</span></span>
<span data-ttu-id="e8400-542">L'esempio di codice seguente illustra come impostare i criteri di ripetizione dei tentativi per:</span><span class="sxs-lookup"><span data-stu-id="e8400-542">The following code example shows how to set the retry policy for:</span></span>

* <span data-ttu-id="e8400-543">Un gestore dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="e8400-543">A namespace manager.</span></span> <span data-ttu-id="e8400-544">I criteri si applicano a tutte le operazioni eseguite sul gestore e non possono essere sostituiti a favore di singole operazioni.</span><span class="sxs-lookup"><span data-stu-id="e8400-544">The policy applies to all operations on that manager, and cannot be overridden for individual operations.</span></span>
* <span data-ttu-id="e8400-545">Una factory di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="e8400-545">A messaging factory.</span></span> <span data-ttu-id="e8400-546">I criteri si applicano a tutti i client creati dalla factory e non possono essere sostituiti durante la creazione di client individuali.</span><span class="sxs-lookup"><span data-stu-id="e8400-546">The policy applies to all clients created from that factory, and cannot be overridden when creating individual clients.</span></span>
* <span data-ttu-id="e8400-547">Un singolo client di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="e8400-547">An individual messaging client.</span></span> <span data-ttu-id="e8400-548">Dopo aver creato un client, è possibile impostarne i criteri di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-548">After a client has been created, you can set the retry policy for that client.</span></span> <span data-ttu-id="e8400-549">I criteri di applicano a tutte le operazioni eseguite sul client.</span><span class="sxs-lookup"><span data-stu-id="e8400-549">The policy applies to all operations on that client.</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
    class ServiceBusCodeSamples
    {
        private const string connectionString =
            @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
                SharedAccessKeyName=RootManageSharedAccessKey;
                SharedAccessKey=C99..........Mk=";

        public async static Task Samples()
        {
            const string QueueName = "TestQueue";

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

            var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

            // The namespace manager will have a default exponential policy with 10 retry attempts
            // and a 3 second delay delta.
            // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
            // with an extra 10 sec added when receiving a ServiceBusyException.

            {
                // Set different values for the retry policy, used for all operations on the namespace manager.
                namespaceManager.Settings.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(0),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);

                // Policies cannot be specified on a per-operation basis.
                if (!await namespaceManager.QueueExistsAsync(QueueName))
                {
                    await namespaceManager.CreateQueueAsync(QueueName);
                }
            }


            var messagingFactory = MessagingFactory.Create(
                namespaceManager.Address, namespaceManager.Settings.TokenProvider);
            // The messaging factory will have a default exponential policy with 10 retry attempts
            // and a 3 second delay delta.
            // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
            // with an extra 10 sec added when receiving a ServiceBusyException.

            {
                // Set different values for the retry policy, used for clients created from it.
                messagingFactory.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(1),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);


                // Policies cannot be specified on a per-operation basis.
                var session = await messagingFactory.AcceptMessageSessionAsync();
            }


            {
                var client = messagingFactory.CreateQueueClient(QueueName);
                // The client inherits the policy from the factory that created it.


                // Set different values for the retry policy on the client.
                client.RetryPolicy =
                    new RetryExponential(
                        minBackoff: TimeSpan.FromSeconds(0.1),
                        maxBackoff: TimeSpan.FromSeconds(30),
                        maxRetryCount: 3);


                // Policies cannot be specified on a per-operation basis.
                var session = await client.AcceptMessageSessionAsync();
            }
        }
    }
}
```

### <a name="more-information"></a><span data-ttu-id="e8400-550">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-550">More information</span></span>
* [<span data-ttu-id="e8400-551">Modelli di messaggistica asincrona e disponibilità elevata.</span><span class="sxs-lookup"><span data-stu-id="e8400-551">Asynchronous Messaging Patterns and High Availability</span></span>](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## <a name="azure-redis-cache-retry-guidelines"></a><span data-ttu-id="e8400-552">Linee guida per la ripetizione di tentativi di Cache Redis di Azure</span><span class="sxs-lookup"><span data-stu-id="e8400-552">Azure Redis Cache retry guidelines</span></span>
<span data-ttu-id="e8400-553">Cache Redis di Azure è un servizio cache a bassa latenza e di rapido accesso ai dati basato sulla nota Cache Redis open source.</span><span class="sxs-lookup"><span data-stu-id="e8400-553">Azure Redis Cache is a fast data access and low latency cache service based on the popular open source Redis Cache.</span></span> <span data-ttu-id="e8400-554">È un servizio protetto, gestito da Microsoft e accessibile da qualsiasi applicazione in Azure.</span><span class="sxs-lookup"><span data-stu-id="e8400-554">It is secure, managed by Microsoft, and is accessible from any application in Azure.</span></span>

<span data-ttu-id="e8400-555">Le indicazioni fornite in questa sezione presuppongono che si usi il client StackExchange.Redis per accedere alla cache.</span><span class="sxs-lookup"><span data-stu-id="e8400-555">The guidance in this section is based on using the StackExchange.Redis client to access the cache.</span></span> <span data-ttu-id="e8400-556">Nel [sito Web di Redis](http://redis.io/clients)è disponibile un elenco di altri client idonei, a cui possono essere associati vari meccanismi di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-556">A list of other suitable clients can be found on the [Redis website](http://redis.io/clients), and these may have different retry mechanisms.</span></span>

<span data-ttu-id="e8400-557">Il client StackExchange.Redis usa il multiplexing tramite un'unica connessione.</span><span class="sxs-lookup"><span data-stu-id="e8400-557">Note that the StackExchange.Redis client uses multiplexing through a single connection.</span></span> <span data-ttu-id="e8400-558">È consigliabile quindi creare un'istanza del client all'avvio dell'applicazione e usarla per tutte le operazioni eseguite sulla cache.</span><span class="sxs-lookup"><span data-stu-id="e8400-558">The recommended usage is to create an instance of the client at application startup and use this instance for all operations against the cache.</span></span> <span data-ttu-id="e8400-559">In questo modo, la connessione alla cache viene eseguita una sola volta e tutte le indicazioni fornite in questa sezione fanno riferimento ai criteri di ripetizione dei tentativi definiti per la connessione iniziale e non per ogni operazione che accede alla cache.</span><span class="sxs-lookup"><span data-stu-id="e8400-559">For this reason, the connection to the cache is made only once, and so all of the guidance in this section is related to the retry policy for this initial connection—and not for each operation that accesses the cache.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-560">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-560">Retry mechanism</span></span>
<span data-ttu-id="e8400-561">Il client StackExchange.Redis usa una classe di gestione della connessione configurata tramite un set di opzioni, che include:</span><span class="sxs-lookup"><span data-stu-id="e8400-561">The StackExchange.Redis client uses a connection manager class that is configured through a set of options, incuding:</span></span>

- <span data-ttu-id="e8400-562">**ConnectRetry**.</span><span class="sxs-lookup"><span data-stu-id="e8400-562">**ConnectRetry**.</span></span> <span data-ttu-id="e8400-563">Il numero di volte in cui una connessione non riuscita per la cache verrà ritentata.</span><span class="sxs-lookup"><span data-stu-id="e8400-563">The number of times a failed connection to the cache will be retried.</span></span>
- <span data-ttu-id="e8400-564">**ReconnectRetryPolicy**.</span><span class="sxs-lookup"><span data-stu-id="e8400-564">**ReconnectRetryPolicy**.</span></span> <span data-ttu-id="e8400-565">La strategia di ripetizione dei tentativi da usare.</span><span class="sxs-lookup"><span data-stu-id="e8400-565">The retry strategy to use.</span></span>
- <span data-ttu-id="e8400-566">**ConnectTimeout**.</span><span class="sxs-lookup"><span data-stu-id="e8400-566">**ConnectTimeout**.</span></span> <span data-ttu-id="e8400-567">Il tempo di attesa massimo in millisecondi.</span><span class="sxs-lookup"><span data-stu-id="e8400-567">The maximum waiting time in milliseconds.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="e8400-568">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="e8400-568">Policy configuration</span></span>
<span data-ttu-id="e8400-569">I criteri di ripetizione dei tentativi vengono configurati a livello di codice impostando le opzioni per il client prima di connettersi alla cache.</span><span class="sxs-lookup"><span data-stu-id="e8400-569">Retry policies are configured programmatically by setting the options for the client before connecting to the cache.</span></span> <span data-ttu-id="e8400-570">Questa operazione può essere eseguita creando un'istanza della classe **ConfigurationOptions**, popolandone le proprietà e passandola al metodo **Connect**.</span><span class="sxs-lookup"><span data-stu-id="e8400-570">This can be done by creating an instance of the **ConfigurationOptions** class, populating its properties, and passing it to the **Connect** method.</span></span>

<span data-ttu-id="e8400-571">Le classi incorporate supportano i ritardi lineari, ovvero il tempo d'intervallo costante, e i backoff esponenziali con intervalli di ripetizione dei tentativi casuali.</span><span class="sxs-lookup"><span data-stu-id="e8400-571">The built-in classes support linear (constant) delay and exponential backoff with randomized retry intervals.</span></span> <span data-ttu-id="e8400-572">È anche possibile creare un criterio di ripetizione dei tentativi personalizzato implementando l'interfaccia **IReconnectRetryPolicy**.</span><span class="sxs-lookup"><span data-stu-id="e8400-572">You can also create a custom retry policy by implementing the **IReconnectRetryPolicy** interface.</span></span>

<span data-ttu-id="e8400-573">L'esempio seguente consente di configurare una strategia di ripetizione dei tentativi con backoff esponenziale.</span><span class="sxs-lookup"><span data-stu-id="e8400-573">The following example configures a retry strategy using exponential backoff.</span></span>

```csharp
var deltaBackOffInMilliseconds = TimeSpan.FromSeconds(5).Milliseconds;
var maxDeltaBackOffInMilliseconds = TimeSpan.FromSeconds(20).Milliseconds;
var options = new ConfigurationOptions
{
    EndPoints = {"localhost"},
    ConnectRetry = 3,
    ReconnectRetryPolicy = new ExponentialRetry(deltaBackOffInMilliseconds, maxDeltaBackOffInMilliseconds),
    ConnectTimeout = 2000
};
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="e8400-574">In alternativa, è possibile specificare le opzioni sotto forma di stringa, per poi passarla al metodo **Connect** .</span><span class="sxs-lookup"><span data-stu-id="e8400-574">Alternatively, you can specify the options as a string, and pass this to the **Connect** method.</span></span> <span data-ttu-id="e8400-575">Si noti che la proprietà **ReconnectRetryPolicy** non può essere impostata in questo modo, ma solo tramite codice.</span><span class="sxs-lookup"><span data-stu-id="e8400-575">Note that the **ReconnectRetryPolicy** property cannot be set this way, only through code.</span></span>

```csharp
    var options = "localhost,connectRetry=3,connectTimeout=2000";
    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="e8400-576">È anche possibile specificare le opzioni direttamente durante la connessione alla cache.</span><span class="sxs-lookup"><span data-stu-id="e8400-576">You can also specify options directly when you connect to the cache.</span></span>

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

<span data-ttu-id="e8400-577">Per altre informazioni, vedere [Configurazione di Stack Exchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration) nella documentazione di StackExchange.Redis.</span><span class="sxs-lookup"><span data-stu-id="e8400-577">For more information, see [Stack Exchange Redis Configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration) in the StackExchange.Redis documentation.</span></span>

<span data-ttu-id="e8400-578">La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.</span><span class="sxs-lookup"><span data-stu-id="e8400-578">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="e8400-579">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="e8400-579">**Context**</span></span> | <span data-ttu-id="e8400-580">**Impostazione**</span><span class="sxs-lookup"><span data-stu-id="e8400-580">**Setting**</span></span> | <span data-ttu-id="e8400-581">**Valore predefinito**</span><span class="sxs-lookup"><span data-stu-id="e8400-581">**Default value**</span></span><br /><span data-ttu-id="e8400-582">(v 1.2.2)</span><span class="sxs-lookup"><span data-stu-id="e8400-582">(v 1.2.2)</span></span> | <span data-ttu-id="e8400-583">**Significato**</span><span class="sxs-lookup"><span data-stu-id="e8400-583">**Meaning**</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="e8400-584">Opzioni di configurazione</span><span class="sxs-lookup"><span data-stu-id="e8400-584">ConfigurationOptions</span></span> |<span data-ttu-id="e8400-585">ConnectRetry</span><span class="sxs-lookup"><span data-stu-id="e8400-585">ConnectRetry</span></span><br /><br /><span data-ttu-id="e8400-586">ConnectTimeout</span><span class="sxs-lookup"><span data-stu-id="e8400-586">ConnectTimeout</span></span><br /><br /><span data-ttu-id="e8400-587">SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="e8400-587">SyncTimeout</span></span><br /><br /><span data-ttu-id="e8400-588">ReconnectRetryPolicy</span><span class="sxs-lookup"><span data-stu-id="e8400-588">ReconnectRetryPolicy</span></span> |<span data-ttu-id="e8400-589">3</span><span class="sxs-lookup"><span data-stu-id="e8400-589">3</span></span><br /><br /><span data-ttu-id="e8400-590">Massimo 5000 ms più SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="e8400-590">Maximum 5000 ms plus SyncTimeout</span></span><br /><span data-ttu-id="e8400-591">1000</span><span class="sxs-lookup"><span data-stu-id="e8400-591">1000</span></span><br /><br /><span data-ttu-id="e8400-592">LinearRetry 5.000 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-592">LinearRetry 5000 ms</span></span> |<span data-ttu-id="e8400-593">Numero di nuovi tentativi di connessione durante l'operazione di connessione iniziale.</span><span class="sxs-lookup"><span data-stu-id="e8400-593">The number of times to repeat connect attempts during the initial connection operation.</span></span><br /><span data-ttu-id="e8400-594">Timeout (ms) per operazioni di connessione.</span><span class="sxs-lookup"><span data-stu-id="e8400-594">Timeout (ms) for connect operations.</span></span> <span data-ttu-id="e8400-595">Non un intervallo tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-595">Not a delay between retry attempts.</span></span><br /><span data-ttu-id="e8400-596">Tempo (ms) concesso per consentire operazioni sincrone.</span><span class="sxs-lookup"><span data-stu-id="e8400-596">Time (ms) to allow for synchronous operations.</span></span><br /><br /><span data-ttu-id="e8400-597">Ripetizione dei tentativi ogni 5.000 ms.</span><span class="sxs-lookup"><span data-stu-id="e8400-597">Retry every 5000 ms.</span></span>|

> [!NOTE]
> <span data-ttu-id="e8400-598">Per le operazioni sincrone, è possibile aggiungere `SyncTimeout` alla latenza end-to-end, ma impostare un valore troppo basso può causare timeout eccessivi.</span><span class="sxs-lookup"><span data-stu-id="e8400-598">For synchronous operations, `SyncTimeout` can add to the end-to-end latency, but setting the value too low can cause excessive timeouts.</span></span> <span data-ttu-id="e8400-599">Vedere [Come risolvere i problemi di Cache Redis di Azure][redis-cache-troubleshoot].</span><span class="sxs-lookup"><span data-stu-id="e8400-599">See [How to troubleshoot Azure Redis Cache][redis-cache-troubleshoot].</span></span> <span data-ttu-id="e8400-600">In generale, evitare di usare operazioni sincrone, usare invece operazioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="e8400-600">In general, avoid using synchronous operations, and use asynchronous operations instead.</span></span> <span data-ttu-id="e8400-601">Per altre informazioni, vedere [Pipeline e multiplexer](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).</span><span class="sxs-lookup"><span data-stu-id="e8400-601">For more information see [Pipelines and Multiplexers](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).</span></span>
>
>

### <a name="retry-usage-guidance"></a><span data-ttu-id="e8400-602">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-602">Retry usage guidance</span></span>
<span data-ttu-id="e8400-603">Quando si usa Cache Redis di Azure, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="e8400-603">Consider the following guidelines when using Azure Redis Cache:</span></span>

* <span data-ttu-id="e8400-604">Il client StackExchange Redis gestisce autonomamente i propri tentativi, ma solo se viene stabilita una connessione alla cache al primo avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-604">The StackExchange Redis client manages its own retries, but only when establishing a connection to the cache when the application first starts.</span></span> <span data-ttu-id="e8400-605">È possibile configurare il timeout di connessione, il numero di ripetizione di tentativi e l'intervallo di tempo tra i tentativi per stabilire la connessione, ma i criteri di ripetizione non si applicano alle operazioni eseguite sulla cache.</span><span class="sxs-lookup"><span data-stu-id="e8400-605">You can configure the connection timeout, the number of retry attempts, and the time between retries to establish this connection, but the retry policy does not apply to operations against the cache.</span></span>
* <span data-ttu-id="e8400-606">Anziché ricorrere a un numero elevato di tentativi, valutare la possibilità di eseguire il fallback accedendo alla sorgente originale dei dati.</span><span class="sxs-lookup"><span data-stu-id="e8400-606">Instead of using a large number of retry attempts, consider falling back by accessing the original data source instead.</span></span>

### <a name="telemetry"></a><span data-ttu-id="e8400-607">Telemetria</span><span class="sxs-lookup"><span data-stu-id="e8400-607">Telemetry</span></span>
<span data-ttu-id="e8400-608">È possibile raccogliere informazioni sulle connessioni, ma su non altre operazioni, usando un **TextWriter**.</span><span class="sxs-lookup"><span data-stu-id="e8400-608">You can collect information about connections (but not other operations) using a **TextWriter**.</span></span>

```csharp
var writer = new StringWriter();
...
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="e8400-609">Seguito è riportato un esempio di output generato.</span><span class="sxs-lookup"><span data-stu-id="e8400-609">An example of the output this generates is shown below.</span></span>

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

### <a name="examples"></a><span data-ttu-id="e8400-610">esempi</span><span class="sxs-lookup"><span data-stu-id="e8400-610">Examples</span></span>
<span data-ttu-id="e8400-611">L'esempio di codice seguente consente di configurare un intervallo costante, ovvero lineare, tra le ripetizioni di tentativi durante l'inizializzazione del client StackExchange.Redis.</span><span class="sxs-lookup"><span data-stu-id="e8400-611">The following code example configures a constant (linear) delay between retries when initializing the StackExchange.Redis client.</span></span> <span data-ttu-id="e8400-612">Questo esempio illustra come impostare la configurazione usando un'istanza **ConfigurationOptions**.</span><span class="sxs-lookup"><span data-stu-id="e8400-612">This example shows how to set the configuration using a **ConfigurationOptions** instance.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
    class CacheRedisCodeSamples
    {
        public async static Task Samples()
        {
            var writer = new StringWriter();

            {
                try
                {
                    var retryTimeInMilliseconds = TimeSpan.FromSeconds(4).Milliseconds; // delay between retries
                    
                    // Using object-based configuration.
                    var options = new ConfigurationOptions
                                        {
                                            EndPoints = { "localhost" },
                                            ConnectRetry = 3,
                                            ReconnectRetryPolicy = new LinearRetry(retryTimeInMilliseconds)
                                        };
                    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

                    // Store a reference to the multiplexer for use in the application.
                }
                catch
                {
                    Console.WriteLine(writer.ToString());
                    throw;
                }
            }
        }
    }
}
```

<span data-ttu-id="e8400-613">L'esempio successivo imposta la configurazione specificando le opzioni sotto forma di stringa.</span><span class="sxs-lookup"><span data-stu-id="e8400-613">The next example sets the configuration by specifying the options as a string.</span></span> <span data-ttu-id="e8400-614">Il timeout di connessione è l'intervallo di tempo massimo di attesa per la connessione alla cache, non l'intervallo tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-614">The connection timeout is the maximum period of time to wait for a connection to the cache, not the delay between retry attempts.</span></span> <span data-ttu-id="e8400-615">Si noti che la proprietà **ReconnectRetryPolicy** può essere impostata solo dal codice.</span><span class="sxs-lookup"><span data-stu-id="e8400-615">Note that the **ReconnectRetryPolicy** property can only be set by code.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
    class CacheRedisCodeSamples
    {
        public async static Task Samples()
        {
            var writer = new StringWriter();

            {
                try
                {
                    // Using string-based configuration.
                    var options = "localhost,connectRetry=3,connectTimeout=2000";
                    ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

                    // Store a reference to the multiplexer for use in the application.
                }
                catch
                {
                    Console.WriteLine(writer.ToString());
                    throw;
                }
            }
        }
    }
}
```

<span data-ttu-id="e8400-616">Per altri esempi, vedere la sezione relativa alla [configurazione](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) sul sito Web di progetto.</span><span class="sxs-lookup"><span data-stu-id="e8400-616">For more examples, see [Configuration](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) on the project website.</span></span>

### <a name="more-information"></a><span data-ttu-id="e8400-617">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-617">More information</span></span>
* [<span data-ttu-id="e8400-618">Sito Web di Redis</span><span class="sxs-lookup"><span data-stu-id="e8400-618">Redis website</span></span>](http://redis.io/)

## <a name="documentdb-api-retry-guidelines"></a><span data-ttu-id="e8400-619">Linee guida per la ripetizione di tentativi dell'API di DocumentDB</span><span class="sxs-lookup"><span data-stu-id="e8400-619">DocumentDB API retry guidelines</span></span>

<span data-ttu-id="e8400-620">Cosmos DB è un database multi-modello completamente gestito che supporta i dati JSON senza schema mediante l'uso dell'[API di DocumentDB][documentdb-api].</span><span class="sxs-lookup"><span data-stu-id="e8400-620">Cosmos DB is a fully-managed multi-model database that supports schema-less JSON data by using the [DocumentDB API][documentdb-api].</span></span> <span data-ttu-id="e8400-621">Offre prestazioni affidabili e configurabili, consente l'elaborazione transazionale JavaScript nativa e, grazie alla scalabilità elastica, è ottimizzato per il cloud.</span><span class="sxs-lookup"><span data-stu-id="e8400-621">It offers configurable and reliable performance, native JavaScript transactional processing, and is built for the cloud with elastic scale.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-622">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-622">Retry mechanism</span></span>
<span data-ttu-id="e8400-623">La classe `DocumentClient` esegue nuovi tentativi in automatico.</span><span class="sxs-lookup"><span data-stu-id="e8400-623">The `DocumentClient` class automatically retries failed attempts.</span></span> <span data-ttu-id="e8400-624">Per impostare il numero di tentativi e il tempo di attesa massimo, configurare [ConnectionPolicy.RetryOptions].</span><span class="sxs-lookup"><span data-stu-id="e8400-624">To set the number of retries and the maximum wait time, configure [ConnectionPolicy.RetryOptions].</span></span> <span data-ttu-id="e8400-625">Le eccezioni generate dal client escludono il criterio di ripetizione oppure non sono errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="e8400-625">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>

<span data-ttu-id="e8400-626">Se Cosmos DB limita il client, viene restituito un errore HTTP 429.</span><span class="sxs-lookup"><span data-stu-id="e8400-626">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="e8400-627">Verificare il codice di stato in `DocumentClientException`.</span><span class="sxs-lookup"><span data-stu-id="e8400-627">Check the status code in the `DocumentClientException`.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="e8400-628">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="e8400-628">Policy configuration</span></span>
<span data-ttu-id="e8400-629">La tabella seguente mostra le impostazioni predefinite per la classe `RetryOptions`.</span><span class="sxs-lookup"><span data-stu-id="e8400-629">The following table shows the default settings for the `RetryOptions` class.</span></span>

| <span data-ttu-id="e8400-630">Impostazione</span><span class="sxs-lookup"><span data-stu-id="e8400-630">Setting</span></span> | <span data-ttu-id="e8400-631">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="e8400-631">Default value</span></span> | <span data-ttu-id="e8400-632">Description</span><span class="sxs-lookup"><span data-stu-id="e8400-632">Description</span></span> |
| --- | --- | --- |
| <span data-ttu-id="e8400-633">MaxRetryAttemptsOnThrottledRequests</span><span class="sxs-lookup"><span data-stu-id="e8400-633">MaxRetryAttemptsOnThrottledRequests</span></span> |<span data-ttu-id="e8400-634">9</span><span class="sxs-lookup"><span data-stu-id="e8400-634">9</span></span> |<span data-ttu-id="e8400-635">Il numero massimo di tentativi se la richiesta ha esito negativo a causa della limitazione applicata da Cosmos DB alla velocità del client.</span><span class="sxs-lookup"><span data-stu-id="e8400-635">The maximum number of retries if the request fails because Cosmos DB applied rate limiting on the client.</span></span> |
| <span data-ttu-id="e8400-636">MaxRetryWaitTimeInSeconds</span><span class="sxs-lookup"><span data-stu-id="e8400-636">MaxRetryWaitTimeInSeconds</span></span> |<span data-ttu-id="e8400-637">30</span><span class="sxs-lookup"><span data-stu-id="e8400-637">30</span></span> |<span data-ttu-id="e8400-638">Il tempo massimo per i tentativi in secondi.</span><span class="sxs-lookup"><span data-stu-id="e8400-638">The maximum retry time in seconds.</span></span> |

### <a name="example"></a><span data-ttu-id="e8400-639">Esempio</span><span class="sxs-lookup"><span data-stu-id="e8400-639">Example</span></span>
```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); ;
var options = client.ConnectionPolicy.RetryOptions;
options.MaxRetryAttemptsOnThrottledRequests = 5;
options.MaxRetryWaitTimeInSeconds = 15;
```

### <a name="telemetry"></a><span data-ttu-id="e8400-640">Telemetria</span><span class="sxs-lookup"><span data-stu-id="e8400-640">Telemetry</span></span>
<span data-ttu-id="e8400-641">I tentativi vengono registrati come messaggi di traccia non strutturati tramite un oggetto **TraceSource**in .NET.</span><span class="sxs-lookup"><span data-stu-id="e8400-641">Retry attempts are logged as unstructured trace messages through a .NET **TraceSource**.</span></span> <span data-ttu-id="e8400-642">È necessario configurare un oggetto **TraceListener** per acquisire gli eventi e scriverli in un log di destinazione appropriato.</span><span class="sxs-lookup"><span data-stu-id="e8400-642">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span>

<span data-ttu-id="e8400-643">Ad esempio, se si aggiunge il seguente codice al file App.config in uso, verranno generate tracce in un file di testo nello stesso percorso dell'eseguibile:</span><span class="sxs-lookup"><span data-stu-id="e8400-643">For example, if you add the following to your App.config file, traces will be generated in a text file in the same location as the executable:</span></span>

```
<configuration>
  <system.diagnostics>
    <switches>
      <add name="SourceSwitch" value="Verbose"/>
    </switches>
    <sources>
      <source name="DocDBTrace" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" >
        <listeners>
          <add name="MyTextListener" type="System.Diagnostics.TextWriterTraceListener" traceOutputOptions="DateTime,ProcessId,ThreadId" initializeData="DocumentDBTrace.txt"></add>
        </listeners>
      </source>
    </sources>
  </system.diagnostics>
</configuration>
```


## <a name="azure-search-retry-guidelines"></a><span data-ttu-id="e8400-644">Linee guida per la ripetizione di tentativi di Ricerca di Azure</span><span class="sxs-lookup"><span data-stu-id="e8400-644">Azure Search retry guidelines</span></span>
<span data-ttu-id="e8400-645">Lo strumento Ricerca di Azure può essere usato per aggiungere sofisticate e avanzate funzionalità di ricerca in un sito Web o un'applicazione, ottimizzare i risultati di ricerca in modo semplice e rapido e creare avanzati modelli di classificazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-645">Azure Search can be used to add powerful and sophisticated search capabilities to a website or application, quickly and easily tune search results, and construct rich and fine-tuned ranking models.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-646">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-646">Retry mechanism</span></span>
<span data-ttu-id="e8400-647">Il comportamento di ripetizione dei tentativi nell'SDK di Ricerca di Azure è controllato dal metodo `SetRetryPolicy` nelle classi [SearchServiceClient] e [SearchIndexClient].</span><span class="sxs-lookup"><span data-stu-id="e8400-647">Retry behavior in the Azure Search SDK is controlled by the `SetRetryPolicy` method on the [SearchServiceClient] and [SearchIndexClient] classes.</span></span> <span data-ttu-id="e8400-648">I criteri predefiniti eseguono nuovi tentativi con backoff esponenziale quando Ricerca di Azure restituisce una risposta 5xx o 408 (Timeout della richiesta).</span><span class="sxs-lookup"><span data-stu-id="e8400-648">The default policy retries with exponential backoff when Azure Search returns a 5xx or 408 (Request Timeout) response.</span></span>

### <a name="telemetry"></a><span data-ttu-id="e8400-649">Telemetria</span><span class="sxs-lookup"><span data-stu-id="e8400-649">Telemetry</span></span>
<span data-ttu-id="e8400-650">Tracciare con ETW o registrando un provider di traccia personalizzato.</span><span class="sxs-lookup"><span data-stu-id="e8400-650">Trace with ETW or by registering a custom trace provider.</span></span> <span data-ttu-id="e8400-651">Per altre informazioni, consultare la [documentazione di AutoRest][autorest].</span><span class="sxs-lookup"><span data-stu-id="e8400-651">For more information, see the [AutoRest documentation][autorest].</span></span>

## <a name="azure-active-directory-retry-guidelines"></a><span data-ttu-id="e8400-652">Azure Active Directory - Linee guida per la ripetizione di tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-652">Azure Active Directory retry guidelines</span></span>
<span data-ttu-id="e8400-653">Azure Active Directory è una soluzione cloud completa per la gestione delle identità e dell'accesso che combina servizi directory di importanza strategica, governance avanzata delle identità e gestione della sicurezza e dell'accesso alle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="e8400-653">Azure Active Directory (Azure AD) is a comprehensive identity and access management cloud solution that combines core directory services, advanced identity governance, security, and application access management.</span></span> <span data-ttu-id="e8400-654">Azure AD offre inoltre agli sviluppatori una piattaforma di gestione delle identità per consentire il controllo dell'accesso alle applicazioni in base a regole e criteri centralizzati.</span><span class="sxs-lookup"><span data-stu-id="e8400-654">Azure AD also offers developers an identity management platform to deliver access control to their applications, based on centralized policy and rules.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-655">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-655">Retry mechanism</span></span>
<span data-ttu-id="e8400-656">Nella libreria di autenticazione di Active Directory è previsto un meccanismo di ripetizione dei tentativi incorporato per Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e8400-656">There is a built-in retry mechanism for Azure Active Directory in the Active Directory Authentication Library (ADAL).</span></span> <span data-ttu-id="e8400-657">Per evitare blocchi imprevisti, è consigliabile che le librerie di terze parti e il codice dell'applicazione *non* ripetano i tentativi di connessione non riusciti, ma consentano ad ADAL di gestire i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-657">To avoid unexpected lockouts, we recommend that third party libraries and application code do *not* retry failed connections, but allow ADAL to handle retries.</span></span> 

### <a name="retry-usage-guidance"></a><span data-ttu-id="e8400-658">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-658">Retry usage guidance</span></span>
<span data-ttu-id="e8400-659">Quando si usa Azure Active Directory, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="e8400-659">Consider the following guidelines when using Azure Active Directory:</span></span>

* <span data-ttu-id="e8400-660">Quando possibile, usare la libreria ADAL e il supporto incorporato per le ripetzioni dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-660">When possible, use the ADAL library and the built-in support for retries.</span></span>
* <span data-ttu-id="e8400-661">Se si usa l'API REST per Azure Active Directory, è necessario ripetere l'operazione solo se il risultato è un errore compreso nell'intervallo 5xx (ad esempio 500 - Errore interno del server, 502 - Gateway non valido, 503 - Servizio non disponibile o 504 - Timeout gateway).</span><span class="sxs-lookup"><span data-stu-id="e8400-661">If you are using the REST API for Azure Active Directory, you should retry the operation only if the result is an error in the 5xx range (such as 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable, and 504 Gateway Timeout).</span></span> <span data-ttu-id="e8400-662">Non ripetere nuovi tentativi per altri tipi di errore.</span><span class="sxs-lookup"><span data-stu-id="e8400-662">Do not retry for any other errors.</span></span>
* <span data-ttu-id="e8400-663">Negli scenari in batch di Azure Active Directory è opportuno usare criteri di backoff esponenziale.</span><span class="sxs-lookup"><span data-stu-id="e8400-663">An exponential back-off policy is recommended for use in batch scenarios with Azure Active Directory.</span></span>

<span data-ttu-id="e8400-664">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="e8400-664">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="e8400-665">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="e8400-665">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="e8400-666">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="e8400-666">**Context**</span></span> | <span data-ttu-id="e8400-667">**Destinazione di esempio E2E<br />latenza massima**</span><span class="sxs-lookup"><span data-stu-id="e8400-667">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="e8400-668">**Strategia di ripetizione dei tentativi**</span><span class="sxs-lookup"><span data-stu-id="e8400-668">**Retry strategy**</span></span> | <span data-ttu-id="e8400-669">**Impostazioni**</span><span class="sxs-lookup"><span data-stu-id="e8400-669">**Settings**</span></span> | <span data-ttu-id="e8400-670">**Valori**</span><span class="sxs-lookup"><span data-stu-id="e8400-670">**Values**</span></span> | <span data-ttu-id="e8400-671">**Funzionamento**</span><span class="sxs-lookup"><span data-stu-id="e8400-671">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="e8400-672">Interattivo, interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="e8400-672">Interactive, UI,</span></span><br /><span data-ttu-id="e8400-673">o in primo piano</span><span class="sxs-lookup"><span data-stu-id="e8400-673">or foreground</span></span> |<span data-ttu-id="e8400-674">2 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-674">2 sec</span></span> |<span data-ttu-id="e8400-675">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="e8400-675">FixedInterval</span></span> |<span data-ttu-id="e8400-676">Numero tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-676">Retry count</span></span><br /><span data-ttu-id="e8400-677">Intervallo tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-677">Retry interval</span></span><br /><span data-ttu-id="e8400-678">Primo tentativo rapido</span><span class="sxs-lookup"><span data-stu-id="e8400-678">First fast retry</span></span> |<span data-ttu-id="e8400-679">3</span><span class="sxs-lookup"><span data-stu-id="e8400-679">3</span></span><br /><span data-ttu-id="e8400-680">500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-680">500 ms</span></span><br /><span data-ttu-id="e8400-681">true</span><span class="sxs-lookup"><span data-stu-id="e8400-681">true</span></span> |<span data-ttu-id="e8400-682">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-682">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="e8400-683">Tentativo 2 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-683">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="e8400-684">Tentativo 3 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="e8400-684">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="e8400-685">Background</span><span class="sxs-lookup"><span data-stu-id="e8400-685">Background or</span></span><br /><span data-ttu-id="e8400-686">o batch</span><span class="sxs-lookup"><span data-stu-id="e8400-686">batch</span></span> |<span data-ttu-id="e8400-687">60 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-687">60 sec</span></span> |<span data-ttu-id="e8400-688">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="e8400-688">ExponentialBackoff</span></span> |<span data-ttu-id="e8400-689">Numero tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-689">Retry count</span></span><br /><span data-ttu-id="e8400-690">Interruzione temporanea minima</span><span class="sxs-lookup"><span data-stu-id="e8400-690">Min back-off</span></span><br /><span data-ttu-id="e8400-691">Interruzione temporanea massima</span><span class="sxs-lookup"><span data-stu-id="e8400-691">Max back-off</span></span><br /><span data-ttu-id="e8400-692">Interruzione temporanea delta</span><span class="sxs-lookup"><span data-stu-id="e8400-692">Delta back-off</span></span><br /><span data-ttu-id="e8400-693">Primo tentativo rapido</span><span class="sxs-lookup"><span data-stu-id="e8400-693">First fast retry</span></span> |<span data-ttu-id="e8400-694">5</span><span class="sxs-lookup"><span data-stu-id="e8400-694">5</span></span><br /><span data-ttu-id="e8400-695">0 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-695">0 sec</span></span><br /><span data-ttu-id="e8400-696">60 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-696">60 sec</span></span><br /><span data-ttu-id="e8400-697">2 secondi</span><span class="sxs-lookup"><span data-stu-id="e8400-697">2 sec</span></span><br /><span data-ttu-id="e8400-698">false</span><span class="sxs-lookup"><span data-stu-id="e8400-698">false</span></span> |<span data-ttu-id="e8400-699">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-699">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="e8400-700">Tentativo 2 - intervallo di ~2 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-700">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="e8400-701">Tentativo 3 - intervallo di ~6 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-701">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="e8400-702">Tentativo 4 - intervallo di ~14 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-702">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="e8400-703">Tentativo 5 - intervallo di 30 sec</span><span class="sxs-lookup"><span data-stu-id="e8400-703">Attempt 5 - delay ~30 sec</span></span> |

### <a name="more-information"></a><span data-ttu-id="e8400-704">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-704">More information</span></span>
* <span data-ttu-id="e8400-705">[Librerie di autenticazione di Azure Active Directory][adal]</span><span class="sxs-lookup"><span data-stu-id="e8400-705">[Azure Active Directory Authentication Libraries][adal]</span></span>

## <a name="service-fabric-retry-guidelines"></a><span data-ttu-id="e8400-706">Linee guida per la ripetizione di tentativi di Service Fabric</span><span class="sxs-lookup"><span data-stu-id="e8400-706">Service Fabric retry guidelines</span></span>

<span data-ttu-id="e8400-707">La distribuzione di servizi affidabili in un cluster di Service Fabric protegge dalla maggior parte degli errori temporanei potenziali descritti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="e8400-707">Distributing reliable services in a Service Fabric cluster guards against most of the potential transient faults discussed in this article.</span></span> <span data-ttu-id="e8400-708">Tuttavia, alcuni errori temporanei sono comunque possibili.</span><span class="sxs-lookup"><span data-stu-id="e8400-708">Some transient faults are still possible, however.</span></span> <span data-ttu-id="e8400-709">Ad esempio, il servizio di denominazione potrebbe essere all'interno di una modifica di routing quando rileva una richiesta, provocando la generazione di un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e8400-709">For example, the naming service might be in the middle of a routing change when it gets a request, causing it to throw an exception.</span></span> <span data-ttu-id="e8400-710">Se la stessa richiesta arriva 100 millisecondi dopo, verrà probabilmente completata.</span><span class="sxs-lookup"><span data-stu-id="e8400-710">If the same request comes 100 milliseconds later, it will probably succeed.</span></span>

<span data-ttu-id="e8400-711">Service Fabric gestisce internamente questo tipo di errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="e8400-711">Internally, Service Fabric manages this kind of transient fault.</span></span> <span data-ttu-id="e8400-712">È possibile configurare alcune impostazioni tramite la classe `OperationRetrySettings` durante la configurazione dei servizi.</span><span class="sxs-lookup"><span data-stu-id="e8400-712">You can configure some settings by using the `OperationRetrySettings` class while setting up your services.</span></span>  <span data-ttu-id="e8400-713">Il codice seguente mostra un esempio.</span><span class="sxs-lookup"><span data-stu-id="e8400-713">The following code shows an example.</span></span> <span data-ttu-id="e8400-714">Nella maggior parte dei casi ciò non dovrebbe essere necessario e le impostazioni predefinite sono adeguate.</span><span class="sxs-lookup"><span data-stu-id="e8400-714">In most cases, this should not be necessary, and the default settings will be fine.</span></span>

```csharp
    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        OperationTimeout = TimeSpan.FromSeconds(30)
    };

    var retrySettings = new OperationRetrySettings(TimeSpan.FromSeconds(15), TimeSpan.FromSeconds(1), 5);

    var clientFactory = new FabricTransportServiceRemotingClientFactory(transportSettings);

    var serviceProxyFactory = new ServiceProxyFactory((c) => clientFactory, retrySettings);

    var client = serviceProxyFactory.CreateServiceProxy<ISomeService>(
        new Uri("fabric:/SomeApp/SomeStatefulReliableService"),
        new ServicePartitionKey(0));
```

## <a name="more-information"></a><span data-ttu-id="e8400-715">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-715">More information</span></span>

* <span data-ttu-id="e8400-716">[Remote Exception Handling](https://github.com/Microsoft/azure-docs/blob/master/articles/service-fabric/service-fabric-reliable-services-communication-remoting.md#remoting-exception-handling) (Gestione delle eccezioni da remoto)</span><span class="sxs-lookup"><span data-stu-id="e8400-716">[Remote Exception Handling](https://github.com/Microsoft/azure-docs/blob/master/articles/service-fabric/service-fabric-reliable-services-communication-remoting.md#remoting-exception-handling)</span></span>


## <a name="azure-event-hubs-retry-guidelines"></a><span data-ttu-id="e8400-717">Linee guida per la ripetizione dei tentativi degli Hub eventi di Azure</span><span class="sxs-lookup"><span data-stu-id="e8400-717">Azure Event Hubs retry guidelines</span></span>

<span data-ttu-id="e8400-718">Hub eventi di Azure è un servizio di inserimento di dati di telemetria su vastissima scala che raccoglie, trasforma e archivia milioni di eventi.</span><span class="sxs-lookup"><span data-stu-id="e8400-718">Azure Event Hubs is a hyper-scale telemetry ingestion service that collects, transforms, and stores millions of events.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="e8400-719">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-719">Retry mechanism</span></span>
<span data-ttu-id="e8400-720">Il comportamento di ripetizione dei tentativi nella libreria client dell'Hub eventi di Azure è controllato dalla proprietà `RetryPolicy` nella classe `EventHubClient`.</span><span class="sxs-lookup"><span data-stu-id="e8400-720">Retry behavior in the Azure Event Hubs Client Library is controlled by the `RetryPolicy` property on the `EventHubClient` class.</span></span> <span data-ttu-id="e8400-721">I criteri predefiniti eseguono nuovi tentativi con backoff esponenziale quando Hub eventi di Azure restituisce `EventHubsException` o `OperationCanceledException` temporanei.</span><span class="sxs-lookup"><span data-stu-id="e8400-721">The default policy retries with exponential backoff when Azure Event Hub returns a transient `EventHubsException` or an `OperationCanceledException`.</span></span>

### <a name="example"></a><span data-ttu-id="e8400-722">Esempio</span><span class="sxs-lookup"><span data-stu-id="e8400-722">Example</span></span>
```csharp
EventHubClient client = EventHubClient.CreateFromConnectionString("[event_hub_connection_string]");
client.RetryPolicy = RetryPolicy.Default;
```

### <a name="more-information"></a><span data-ttu-id="e8400-723">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-723">More information</span></span>
[<span data-ttu-id="e8400-724">Libreria client di .NET standard per gli Hub eventi di Azure</span><span class="sxs-lookup"><span data-stu-id="e8400-724"> .NET Standard client library for Azure Event Hubs</span></span>](https://github.com/Azure/azure-event-hubs-dotnet)

## <a name="general-rest-and-retry-guidelines"></a><span data-ttu-id="e8400-725">Linee guida generali su REST e sulla ripetizione di tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-725">General REST and retry guidelines</span></span>
<span data-ttu-id="e8400-726">Quando si accede a servizi di Azure o di terze parti, tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="e8400-726">Consider the following when accessing Azure or third party services:</span></span>

* <span data-ttu-id="e8400-727">Usare un approccio sistematico per gestire la ripetizione di tentativi, ad esempio sotto forma di codice riusabile, in modo da poter applicare una metodologia coerente tra tutti i client e le soluzioni.</span><span class="sxs-lookup"><span data-stu-id="e8400-727">Use a systematic approach to managing retries, perhaps as reusable code, so that you can apply a consistent methodology across all clients and all solutions.</span></span>
* <span data-ttu-id="e8400-728">Valutare la possibilità di usare un framework di ripetizione dei tentativi, come il Blocco di applicazioni per la gestione degli errori temporanei, per gestire la ripetizione di tentativi se il client o il servizio di destinazione non dispone di un meccanismo di ripetizione dei tentativi incorporato.</span><span class="sxs-lookup"><span data-stu-id="e8400-728">Consider using a retry framework such as the Transient Fault Handling Application Block to manage retries if the target service or client has no built-in retry mechanism.</span></span> <span data-ttu-id="e8400-729">In questo modo, sarà possibile implementare un comportamento coerente per la gestione dei nuovi tentativi e usufruire di un'adeguata strategia di ripetizione dei tentativi predefinita per il servizio di destinazione.</span><span class="sxs-lookup"><span data-stu-id="e8400-729">This will help you implement a consistent retry behavior, and it may provide a suitable default retry strategy for the target service.</span></span> <span data-ttu-id="e8400-730">Potrebbe essere necessario, tuttavia, creare un codice di ripetizione dei tentativi personalizzato per i servizi che presentano un comportamento non standard, ovvero non basato sulle eccezioni per stabilire gli errori temporanei, oppure se si desidera usare un metodo **Retry-Response** per gestire il comportamento di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-730">However, you may need to create custom retry code for services that have non-standard behavior, that do not rely on exceptions to indicate transient failures, or if you want to use a **Retry-Response** reply to manage retry behavior.</span></span>
* <span data-ttu-id="e8400-731">La logica di rilevamento degli errori temporanei dipenderà dall'API client effettiva usata per richiamare le chiamate REST.</span><span class="sxs-lookup"><span data-stu-id="e8400-731">The transient detection logic will depend on the actual client API you use to invoke the REST calls.</span></span> <span data-ttu-id="e8400-732">Alcuni client, ad esempio la nuova classe **HttpClient** , non genereranno eccezioni per le richieste completate con un codice di stato HTTP di errore.</span><span class="sxs-lookup"><span data-stu-id="e8400-732">Some clients, such as the newer **HttpClient** class, will not throw exceptions for completed requests with a non-success HTTP status code.</span></span> <span data-ttu-id="e8400-733">In questo modo, le prestazioni risulteranno migliorate ma non sarà possibile usare il Blocco di applicazioni per la gestione degli errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="e8400-733">This improves performance but prevents the use of the Transient Fault Handling Application Block.</span></span> <span data-ttu-id="e8400-734">Una soluzione potrebbe essere quella di eseguire il wrapping della chiamata all'API REST con un codice che genera eccezioni per i codici di stato HTTP di errore, che verrebbero quindi elaborati dal blocco.</span><span class="sxs-lookup"><span data-stu-id="e8400-734">In this case you could wrap the call to the REST API with code that produces exceptions for non-success HTTP status codes, which can then be processed by the block.</span></span> <span data-ttu-id="e8400-735">In alternativa, è possibile usare un meccanismo diverso per la gestione dei nuovi tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-735">Alternatively, you can use a different mechanism to drive the retries.</span></span>
* <span data-ttu-id="e8400-736">Il codice di stato HTTP restituito dal servizio consente di stabilire se l'errore è temporaneo.</span><span class="sxs-lookup"><span data-stu-id="e8400-736">The HTTP status code returned from the service can help to indicate whether the failure is transient.</span></span> <span data-ttu-id="e8400-737">È possibile che sia necessario esaminare le eccezioni generate da un client o dal framework di ripetizione dei tentativi per accedere al codice di stato o determinare il tipo di eccezione equivalente.</span><span class="sxs-lookup"><span data-stu-id="e8400-737">You may need to examine the exceptions generated by a client or the retry framework to access the status code or to determine the equivalent exception type.</span></span> <span data-ttu-id="e8400-738">I seguenti codici HTTP indicano, in genere, che è opportuno eseguire un nuovo tentativo:</span><span class="sxs-lookup"><span data-stu-id="e8400-738">The following HTTP codes typically indicate that a retry is appropriate:</span></span>
  * <span data-ttu-id="e8400-739">408 - Timeout richiesta</span><span class="sxs-lookup"><span data-stu-id="e8400-739">408 Request Timeout</span></span>
  * <span data-ttu-id="e8400-740">500 - Errore interno del server</span><span class="sxs-lookup"><span data-stu-id="e8400-740">500 Internal Server Error</span></span>
  * <span data-ttu-id="e8400-741">502 - Gateway non valido</span><span class="sxs-lookup"><span data-stu-id="e8400-741">502 Bad Gateway</span></span>
  * <span data-ttu-id="e8400-742">503 - Servizio non disponibile</span><span class="sxs-lookup"><span data-stu-id="e8400-742">503 Service Unavailable</span></span>
  * <span data-ttu-id="e8400-743">504 - Timeout gateway</span><span class="sxs-lookup"><span data-stu-id="e8400-743">504 Gateway Timeout</span></span>
* <span data-ttu-id="e8400-744">Se si usa una logica di ripetizione dei tentativi basata sulle eccezioni, i valori seguenti indicano un errore temporaneo in cui non è possibile stabilire una connessione:</span><span class="sxs-lookup"><span data-stu-id="e8400-744">If you base your retry logic on exceptions, the following typically indicate a transient failure where no connection could be established:</span></span>
  * <span data-ttu-id="e8400-745">WebExceptionStatus.ConnectionClosed</span><span class="sxs-lookup"><span data-stu-id="e8400-745">WebExceptionStatus.ConnectionClosed</span></span>
  * <span data-ttu-id="e8400-746">WebExceptionStatus.ConnectFailure</span><span class="sxs-lookup"><span data-stu-id="e8400-746">WebExceptionStatus.ConnectFailure</span></span>
  * <span data-ttu-id="e8400-747">WebExceptionStatus.Timeout</span><span class="sxs-lookup"><span data-stu-id="e8400-747">WebExceptionStatus.Timeout</span></span>
  * <span data-ttu-id="e8400-748">WebExceptionStatus.RequestCanceled</span><span class="sxs-lookup"><span data-stu-id="e8400-748">WebExceptionStatus.RequestCanceled</span></span>
* <span data-ttu-id="e8400-749">In caso di stato non disponibile di un servizio, l'intervallo di tempo appropriato prima di un nuovo tentativo può essere riportato nell'intestazione della risposta **Retry-After** o in un'altra intestazione personalizzata del servizio.</span><span class="sxs-lookup"><span data-stu-id="e8400-749">In the case of a service unavailable status, the service might indicate the appropriate delay before retrying in the **Retry-After** response header or a different custom header.</span></span> <span data-ttu-id="e8400-750">Alcuni servizi possono anche inviare informazioni aggiuntive come intestazioni personalizzate o incorporate nel contenuto della risposta.</span><span class="sxs-lookup"><span data-stu-id="e8400-750">Services might also send additional information as custom headers, or embedded in the content of the response.</span></span> <span data-ttu-id="e8400-751">Il Blocco di applicazioni per la gestione degli errori temporanei non può usare intestazioni "retry-after" standard o personalizzate.</span><span class="sxs-lookup"><span data-stu-id="e8400-751">The Transient Fault Handling Application Block cannot use the standard or any custom “retry-after” headers.</span></span>
* <span data-ttu-id="e8400-752">Non eseguire nuovi tentativi in caso di codici di stato che rappresentano errori client (errori nell'intervallo 4xx), ad eccezione di 408 - Timeout richiesta.</span><span class="sxs-lookup"><span data-stu-id="e8400-752">Do not retry for status codes representing client errors (errors in the 4xx range) except for a 408 Request Timeout.</span></span>
* <span data-ttu-id="e8400-753">Verificare accuratamente le strategie e i meccanismi di ripetizione dei tentativi in condizioni diverse, ad esempio in vari stati di rete e carichi di sistema.</span><span class="sxs-lookup"><span data-stu-id="e8400-753">Thoroughly test your retry strategies and mechanisms under a range of conditions, such as different network states and varying system loadings.</span></span>

### <a name="retry-strategies"></a><span data-ttu-id="e8400-754">Strategie di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="e8400-754">Retry strategies</span></span>
<span data-ttu-id="e8400-755">Di seguito sono riportati i tipi intervallo più comuni nelle strategie di ripetizione dei tentativi:</span><span class="sxs-lookup"><span data-stu-id="e8400-755">The following are the typical types of retry strategy intervals:</span></span>

* <span data-ttu-id="e8400-756">**Esponenziale**: criteri di ripetizione dei tentativi che eseguono un numero prestabilito di nuovi tentativi e usano un approccio di backoff esponenziale casuale per determinare l'intervallo di tempo tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-756">**Exponential**: A retry policy that performs a specified number of retries, using a randomized exponential back off approach to determine the interval between retries.</span></span> <span data-ttu-id="e8400-757">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e8400-757">For example:</span></span>

        var random = new Random();

        var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
                    random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
                    (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
        var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
                       this.maxBackoff.TotalMilliseconds);
        retryInterval = TimeSpan.FromMilliseconds(interval);
* <span data-ttu-id="e8400-758">**Incrementale**: strategia di ripetizione dei tentativi con un numero prestabilito di nuovi tentativi e un intervallo di tempo incrementale tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-758">**Incremental**: A retry strategy with a specified number of retry attempts and an incremental time interval between retries.</span></span> <span data-ttu-id="e8400-759">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e8400-759">For example:</span></span>

        retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
                       (this.increment.TotalMilliseconds * currentRetryCount));
* <span data-ttu-id="e8400-760">**Lineare**: criteri di ripetizione dei tentativi che eseguono un numero prestabilito di nuovi tentativi e usano un intervallo di tempo fisso tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-760">**LinearRetry**: A retry policy that performs a specified number of retries, using a specified fixed time interval between retries.</span></span> <span data-ttu-id="e8400-761">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e8400-761">For example:</span></span>

        retryInterval = this.deltaBackoff;

### <a name="more-information"></a><span data-ttu-id="e8400-762">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="e8400-762">More information</span></span>
* [<span data-ttu-id="e8400-763">Strategie di interruttore</span><span class="sxs-lookup"><span data-stu-id="e8400-763">Circuit breaker strategies</span></span>](http://msdn.microsoft.com/library/dn589784.aspx)

## <a name="transient-fault-handling-with-polly"></a><span data-ttu-id="e8400-764">Gestione degli errori temporanei con Polly</span><span class="sxs-lookup"><span data-stu-id="e8400-764">Transient fault handling with Polly</span></span>
<span data-ttu-id="e8400-765">[Polly](http://www.thepollyproject.org) è una libreria per gestire a livello di codice la ripetizione dei tentativi e le strategie dell'[interruttore][circuit-breaker].</span><span class="sxs-lookup"><span data-stu-id="e8400-765">[Polly](http://www.thepollyproject.org) is a library to programatically handle retries and [circuit breaker][circuit-breaker] strategies.</span></span> <span data-ttu-id="e8400-766">Il progetto Polly è membro di [.NET Foundation][dotnet-foundation].</span><span class="sxs-lookup"><span data-stu-id="e8400-766">The Polly project is a member of the [.NET Foundation][dotnet-foundation].</span></span> <span data-ttu-id="e8400-767">Per i servizi in cui il client non supporta in modo nativo la ripetizione dei tentativi, Polly è una valida alternativa ed evita la necessità di scrivere il codice personalizzato per la ripetizione dei tentativi, la cui corretta implementazione può risultare difficile.</span><span class="sxs-lookup"><span data-stu-id="e8400-767">For services where the client does not natively support retries, Polly is a valid alternative and avoids the need to write custom retry code, which can be hard to implement correctly.</span></span> <span data-ttu-id="e8400-768">Polly offre anche un modo per tracciare gli errori quando questi si verificano, in modo che sia possibile registrare i tentativi.</span><span class="sxs-lookup"><span data-stu-id="e8400-768">Polly also provides a way to trace errors when they occur, so that you can log retries.</span></span>

<!-- links -->

[adal]: /azure/active-directory/develop/active-directory-authentication-libraries
[autorest]: https://github.com/Azure/autorest/tree/master/docs
[circuit-breaker]: ../patterns/circuit-breaker.md
[ConnectionPolicy.RetryOptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.retryoptions.aspx
[documentdb-api]: /azure/documentdb/documentdb-introduction
[dotnet-foundation]: https://dotnetfoundation.org/
[polly]: http://www.thepollyproject.org
[redis-cache-troubleshoot]: /azure/redis-cache/cache-how-to-troubleshoot
[SearchIndexClient]: https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx
[SearchServiceClient]: https://msdn.microsoft.com/library/microsoft.azure.search.searchserviceclient.aspx
