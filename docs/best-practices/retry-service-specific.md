---
title: Indicazioni specifiche del servizio per la ripetizione di tentativi
titleSuffix: Best practices for cloud applications
description: Indicazioni specifiche del servizio per impostare il meccanismo di ripetizione dei tentativi.
author: dragon119
ms.date: 08/13/2018
ms.topic: best-practice
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 170a38f6b8a6c107670561e63f236e43af948d7d
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59641043"
---
# <a name="retry-guidance-for-specific-services"></a><span data-ttu-id="89539-103">Materiale sussidiario su come eseguire nuovi tentativi per servizi specifici</span><span class="sxs-lookup"><span data-stu-id="89539-103">Retry guidance for specific services</span></span>

<span data-ttu-id="89539-104">La maggior parte dei servizi di Azure e degli SDK client include un meccanismo di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-104">Most Azure services and client SDKs include a retry mechanism.</span></span> <span data-ttu-id="89539-105">Ogni servizio, tuttavia, presenta caratteristiche e requisiti diversi e, quindi, ogni meccanismo di ripetizione dei tentativi è ottimizzato per il servizio specifico in cui è incorporato.</span><span class="sxs-lookup"><span data-stu-id="89539-105">However, these differ because each service has different characteristics and requirements, and so each retry mechanism is tuned to a specific service.</span></span> <span data-ttu-id="89539-106">Questo articolo riepiloga le caratteristiche dei meccanismi di ripetizione dei tentativi relativi alla maggior parte dei servizi di Azure e include utili informazioni per usare, adattare o estendere il meccanismo di ripetizione dei tentativi per ogni servizio.</span><span class="sxs-lookup"><span data-stu-id="89539-106">This guide summarizes the retry mechanism features for the majority of Azure services, and includes information to help you use, adapt, or extend the retry mechanism for that service.</span></span>

<span data-ttu-id="89539-107">Per indicazioni generali sulla gestione degli errori temporanei e sulla ripetizione dei tentativi di eseguire connessioni e operazioni su servizi e risorse, vedere [Indicazioni generali per la ripetizione di tentativi](./transient-faults.md).</span><span class="sxs-lookup"><span data-stu-id="89539-107">For general guidance on handling transient faults, and retrying connections and operations against services and resources, see [Retry guidance](./transient-faults.md).</span></span>

<span data-ttu-id="89539-108">La tabella seguente riepiloga le caratteristiche dei meccanismi di ripetizione dei tentativi associati ai servizi di Azure descritti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="89539-108">The following table summarizes the retry features for the Azure services described in this guidance.</span></span>

| <span data-ttu-id="89539-109">**Servizio**</span><span class="sxs-lookup"><span data-stu-id="89539-109">**Service**</span></span> | <span data-ttu-id="89539-110">**Funzionalità per la ripetizione di tentativi**</span><span class="sxs-lookup"><span data-stu-id="89539-110">**Retry capabilities**</span></span> | <span data-ttu-id="89539-111">**Configurazione dei criteri**</span><span class="sxs-lookup"><span data-stu-id="89539-111">**Policy configuration**</span></span> | <span data-ttu-id="89539-112">**Ambito**</span><span class="sxs-lookup"><span data-stu-id="89539-112">**Scope**</span></span> | <span data-ttu-id="89539-113">**Funzionalità di telemetria**</span><span class="sxs-lookup"><span data-stu-id="89539-113">**Telemetry features**</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="89539-114">**[Azure Active Directory](#azure-active-directory)**</span><span class="sxs-lookup"><span data-stu-id="89539-114">**[Azure Active Directory](#azure-active-directory)**</span></span> |<span data-ttu-id="89539-115">Nativo nella libreria ADAL</span><span class="sxs-lookup"><span data-stu-id="89539-115">Native in ADAL library</span></span> |<span data-ttu-id="89539-116">Incorporato nella libreria ADAL</span><span class="sxs-lookup"><span data-stu-id="89539-116">Embedded into ADAL library</span></span> |<span data-ttu-id="89539-117">Interno</span><span class="sxs-lookup"><span data-stu-id="89539-117">Internal</span></span> |<span data-ttu-id="89539-118">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-118">None</span></span> |
| <span data-ttu-id="89539-119">**[Cosmos DB](#cosmos-db)**</span><span class="sxs-lookup"><span data-stu-id="89539-119">**[Cosmos DB](#cosmos-db)**</span></span> |<span data-ttu-id="89539-120">Native nel servizio</span><span class="sxs-lookup"><span data-stu-id="89539-120">Native in service</span></span> |<span data-ttu-id="89539-121">Non configurabili</span><span class="sxs-lookup"><span data-stu-id="89539-121">Non-configurable</span></span> |<span data-ttu-id="89539-122">Globale</span><span class="sxs-lookup"><span data-stu-id="89539-122">Global</span></span> |<span data-ttu-id="89539-123">TraceSource</span><span class="sxs-lookup"><span data-stu-id="89539-123">TraceSource</span></span> |
| <span data-ttu-id="89539-124">**Data Lake Store**</span><span class="sxs-lookup"><span data-stu-id="89539-124">**Data Lake Store**</span></span> |<span data-ttu-id="89539-125">Native nel client</span><span class="sxs-lookup"><span data-stu-id="89539-125">Native in client</span></span> |<span data-ttu-id="89539-126">Non configurabili</span><span class="sxs-lookup"><span data-stu-id="89539-126">Non-configurable</span></span> |<span data-ttu-id="89539-127">Singole operazioni</span><span class="sxs-lookup"><span data-stu-id="89539-127">Individual operations</span></span> |<span data-ttu-id="89539-128">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-128">None</span></span> |
| <span data-ttu-id="89539-129">**[Hub eventi](#event-hubs)**</span><span class="sxs-lookup"><span data-stu-id="89539-129">**[Event Hubs](#event-hubs)**</span></span> |<span data-ttu-id="89539-130">Native nel client</span><span class="sxs-lookup"><span data-stu-id="89539-130">Native in client</span></span> |<span data-ttu-id="89539-131">Programmatica</span><span class="sxs-lookup"><span data-stu-id="89539-131">Programmatic</span></span> |<span data-ttu-id="89539-132">Client</span><span class="sxs-lookup"><span data-stu-id="89539-132">Client</span></span> |<span data-ttu-id="89539-133">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-133">None</span></span> |
| <span data-ttu-id="89539-134">**[Hub IoT](#iot-hub)**</span><span class="sxs-lookup"><span data-stu-id="89539-134">**[IoT Hub](#iot-hub)**</span></span> |<span data-ttu-id="89539-135">Native nell'SDK client</span><span class="sxs-lookup"><span data-stu-id="89539-135">Native in client SDK</span></span> |<span data-ttu-id="89539-136">Programmatica</span><span class="sxs-lookup"><span data-stu-id="89539-136">Programmatic</span></span> |<span data-ttu-id="89539-137">Client</span><span class="sxs-lookup"><span data-stu-id="89539-137">Client</span></span> |<span data-ttu-id="89539-138">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-138">None</span></span> |
| <span data-ttu-id="89539-139">**[Cache Redis](#azure-redis-cache)**</span><span class="sxs-lookup"><span data-stu-id="89539-139">**[Redis Cache](#azure-redis-cache)**</span></span> |<span data-ttu-id="89539-140">Native nel client</span><span class="sxs-lookup"><span data-stu-id="89539-140">Native in client</span></span> |<span data-ttu-id="89539-141">Programmatica</span><span class="sxs-lookup"><span data-stu-id="89539-141">Programmatic</span></span> |<span data-ttu-id="89539-142">Client</span><span class="sxs-lookup"><span data-stu-id="89539-142">Client</span></span> |<span data-ttu-id="89539-143">TextWriter</span><span class="sxs-lookup"><span data-stu-id="89539-143">TextWriter</span></span> |
| <span data-ttu-id="89539-144">**[Ricerca](#azure-search)**</span><span class="sxs-lookup"><span data-stu-id="89539-144">**[Search](#azure-search)**</span></span> |<span data-ttu-id="89539-145">Native nel client</span><span class="sxs-lookup"><span data-stu-id="89539-145">Native in client</span></span> |<span data-ttu-id="89539-146">Programmatica</span><span class="sxs-lookup"><span data-stu-id="89539-146">Programmatic</span></span> |<span data-ttu-id="89539-147">Client</span><span class="sxs-lookup"><span data-stu-id="89539-147">Client</span></span> |<span data-ttu-id="89539-148">ETW o personalizzato</span><span class="sxs-lookup"><span data-stu-id="89539-148">ETW or Custom</span></span> |
| <span data-ttu-id="89539-149">**[Bus di servizio](#service-bus)**</span><span class="sxs-lookup"><span data-stu-id="89539-149">**[Service Bus](#service-bus)**</span></span> |<span data-ttu-id="89539-150">Native nel client</span><span class="sxs-lookup"><span data-stu-id="89539-150">Native in client</span></span> |<span data-ttu-id="89539-151">Programmatica</span><span class="sxs-lookup"><span data-stu-id="89539-151">Programmatic</span></span> |<span data-ttu-id="89539-152">Gestore dello spazio dei nomi, factory di messaggistica e client</span><span class="sxs-lookup"><span data-stu-id="89539-152">Namespace Manager, Messaging Factory, and Client</span></span> |<span data-ttu-id="89539-153">ETW</span><span class="sxs-lookup"><span data-stu-id="89539-153">ETW</span></span> |
| <span data-ttu-id="89539-154">**[Service Fabric](#service-fabric)**</span><span class="sxs-lookup"><span data-stu-id="89539-154">**[Service Fabric](#service-fabric)**</span></span> |<span data-ttu-id="89539-155">Native nel client</span><span class="sxs-lookup"><span data-stu-id="89539-155">Native in client</span></span> |<span data-ttu-id="89539-156">Programmatica</span><span class="sxs-lookup"><span data-stu-id="89539-156">Programmatic</span></span> |<span data-ttu-id="89539-157">Client</span><span class="sxs-lookup"><span data-stu-id="89539-157">Client</span></span> |<span data-ttu-id="89539-158">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-158">None</span></span> |
| <span data-ttu-id="89539-159">**[Database SQL con ADO.NET](#sql-database-using-adonet)**</span><span class="sxs-lookup"><span data-stu-id="89539-159">**[SQL Database with ADO.NET](#sql-database-using-adonet)**</span></span> |[<span data-ttu-id="89539-160">Polly</span><span class="sxs-lookup"><span data-stu-id="89539-160">Polly</span></span>](#transient-fault-handling-with-polly) |<span data-ttu-id="89539-161">Dichiarativa e a livello di codice</span><span class="sxs-lookup"><span data-stu-id="89539-161">Declarative and programmatic</span></span> |<span data-ttu-id="89539-162">Singole istruzioni o blocchi di codice</span><span class="sxs-lookup"><span data-stu-id="89539-162">Single statements or blocks of code</span></span> |<span data-ttu-id="89539-163">Personalizzate</span><span class="sxs-lookup"><span data-stu-id="89539-163">Custom</span></span> |
| <span data-ttu-id="89539-164">**[Database SQL con Entity Framework](#sql-database-using-entity-framework-6)**</span><span class="sxs-lookup"><span data-stu-id="89539-164">**[SQL Database with Entity Framework](#sql-database-using-entity-framework-6)**</span></span> |<span data-ttu-id="89539-165">Native nel client</span><span class="sxs-lookup"><span data-stu-id="89539-165">Native in client</span></span> |<span data-ttu-id="89539-166">Programmatica</span><span class="sxs-lookup"><span data-stu-id="89539-166">Programmatic</span></span> |<span data-ttu-id="89539-167">Globale per AppDomain</span><span class="sxs-lookup"><span data-stu-id="89539-167">Global per AppDomain</span></span> |<span data-ttu-id="89539-168">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-168">None</span></span> |
| <span data-ttu-id="89539-169">**[Database SQL con Entity Framework Core](#sql-database-using-entity-framework-core)**</span><span class="sxs-lookup"><span data-stu-id="89539-169">**[SQL Database with Entity Framework Core](#sql-database-using-entity-framework-core)**</span></span> |<span data-ttu-id="89539-170">Native nel client</span><span class="sxs-lookup"><span data-stu-id="89539-170">Native in client</span></span> |<span data-ttu-id="89539-171">Programmatica</span><span class="sxs-lookup"><span data-stu-id="89539-171">Programmatic</span></span> |<span data-ttu-id="89539-172">Globale per AppDomain</span><span class="sxs-lookup"><span data-stu-id="89539-172">Global per AppDomain</span></span> |<span data-ttu-id="89539-173">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-173">None</span></span> |
| <span data-ttu-id="89539-174">**[Archiviazione](#azure-storage)**</span><span class="sxs-lookup"><span data-stu-id="89539-174">**[Storage](#azure-storage)**</span></span> |<span data-ttu-id="89539-175">Native nel client</span><span class="sxs-lookup"><span data-stu-id="89539-175">Native in client</span></span> |<span data-ttu-id="89539-176">Programmatica</span><span class="sxs-lookup"><span data-stu-id="89539-176">Programmatic</span></span> |<span data-ttu-id="89539-177">Client e singole operazioni</span><span class="sxs-lookup"><span data-stu-id="89539-177">Client and individual operations</span></span> |<span data-ttu-id="89539-178">TraceSource</span><span class="sxs-lookup"><span data-stu-id="89539-178">TraceSource</span></span> |

> [!NOTE]
> <span data-ttu-id="89539-179">Per la maggior parte dei meccanismi di ripetizione dei tentativi predefiniti di Azure, non è attualmente possibile applicare criteri di ripetizione differenti per diversi tipi di errori o eccezioni.</span><span class="sxs-lookup"><span data-stu-id="89539-179">For most of the Azure built-in retry mechanisms, there is currently no way apply a different retry policy for different types of error or exception.</span></span> <span data-ttu-id="89539-180">È necessario configurare un criterio che fornisca prestazioni e disponibilità medie ottimali.</span><span class="sxs-lookup"><span data-stu-id="89539-180">You should configure a policy that provides the optimum average performance and availability.</span></span> <span data-ttu-id="89539-181">I criteri possono essere successivamente ottimizzati analizzando i file di log per determinare i tipi di errori temporanei che si sono verificati.</span><span class="sxs-lookup"><span data-stu-id="89539-181">One way to fine-tune the policy is to analyze log files to determine the type of transient faults that are occurring.</span></span>

<!-- markdownlint-disable MD024 MD033 -->

## <a name="azure-active-directory"></a><span data-ttu-id="89539-182">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="89539-182">Azure Active Directory</span></span>

<span data-ttu-id="89539-183">Azure Active Directory è una soluzione cloud completa per la gestione delle identità e dell'accesso che combina servizi directory di importanza strategica, governance avanzata delle identità e gestione della sicurezza e dell'accesso alle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="89539-183">Azure Active Directory (Azure AD) is a comprehensive identity and access management cloud solution that combines core directory services, advanced identity governance, security, and application access management.</span></span> <span data-ttu-id="89539-184">Azure AD offre inoltre agli sviluppatori una piattaforma di gestione delle identità per consentire il controllo dell'accesso alle applicazioni in base a regole e criteri centralizzati.</span><span class="sxs-lookup"><span data-stu-id="89539-184">Azure AD also offers developers an identity management platform to deliver access control to their applications, based on centralized policy and rules.</span></span>

> [!NOTE]
> <span data-ttu-id="89539-185">Per informazioni sulla ripetizione dei tentativi negli endpoint dell'identità del servizio gestita, vedere [Come usare un'identità del servizio gestita di una macchina virtuale di Azure per l'acquisizione di token](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).</span><span class="sxs-lookup"><span data-stu-id="89539-185">For retry guidance on Managed Service Identity endpoints, see [How to use an Azure VM Managed Service Identity (MSI) for token acquisition](/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-186">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-186">Retry mechanism</span></span>

<span data-ttu-id="89539-187">Nella libreria di autenticazione di Active Directory è previsto un meccanismo di ripetizione dei tentativi incorporato per Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="89539-187">There is a built-in retry mechanism for Azure Active Directory in the Active Directory Authentication Library (ADAL).</span></span> <span data-ttu-id="89539-188">Per evitare blocchi imprevisti, è consigliabile che le librerie di terze parti e il codice dell'applicazione **non** ripetano i tentativi di connessione non riusciti, ma consentano ad ADAL di gestire i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-188">To avoid unexpected lockouts, we recommend that third party libraries and application code do **not** retry failed connections, but allow ADAL to handle retries.</span></span>

### <a name="retry-usage-guidance"></a><span data-ttu-id="89539-189">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-189">Retry usage guidance</span></span>

<span data-ttu-id="89539-190">Quando si usa Azure Active Directory, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="89539-190">Consider the following guidelines when using Azure Active Directory:</span></span>

- <span data-ttu-id="89539-191">Quando possibile, usare la libreria ADAL e il supporto incorporato per le ripetzioni dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-191">When possible, use the ADAL library and the built-in support for retries.</span></span>
- <span data-ttu-id="89539-192">Se si usa l'API REST per Azure Active Directory, ripetere l'operazione se il codice di risultato è 429 (Troppe richieste) oppure un errore compreso nell'intervallo 5xx.</span><span class="sxs-lookup"><span data-stu-id="89539-192">If you are using the REST API for Azure Active Directory, retry the operation if the result code is 429 (Too Many Requests) or an error in the 5xx range.</span></span> <span data-ttu-id="89539-193">Non ripetere nuovi tentativi per altri tipi di errore.</span><span class="sxs-lookup"><span data-stu-id="89539-193">Do not retry for any other errors.</span></span>
- <span data-ttu-id="89539-194">Negli scenari in batch di Azure Active Directory è opportuno usare criteri di backoff esponenziale.</span><span class="sxs-lookup"><span data-stu-id="89539-194">An exponential back-off policy is recommended for use in batch scenarios with Azure Active Directory.</span></span>

<span data-ttu-id="89539-195">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="89539-195">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="89539-196">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="89539-196">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="89539-197">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="89539-197">**Context**</span></span> | <span data-ttu-id="89539-198">**Destinazione di esempio E2E<br />latenza massima**</span><span class="sxs-lookup"><span data-stu-id="89539-198">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="89539-199">**Strategia di ripetizione dei tentativi**</span><span class="sxs-lookup"><span data-stu-id="89539-199">**Retry strategy**</span></span> | <span data-ttu-id="89539-200">**Impostazioni**</span><span class="sxs-lookup"><span data-stu-id="89539-200">**Settings**</span></span> | <span data-ttu-id="89539-201">**Valori**</span><span class="sxs-lookup"><span data-stu-id="89539-201">**Values**</span></span> | <span data-ttu-id="89539-202">**Funzionamento**</span><span class="sxs-lookup"><span data-stu-id="89539-202">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="89539-203">Interattivo, interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="89539-203">Interactive, UI,</span></span><br /><span data-ttu-id="89539-204">o in primo piano</span><span class="sxs-lookup"><span data-stu-id="89539-204">or foreground</span></span> |<span data-ttu-id="89539-205">2 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-205">2 sec</span></span> |<span data-ttu-id="89539-206">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="89539-206">FixedInterval</span></span> |<span data-ttu-id="89539-207">Numero tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-207">Retry count</span></span><br /><span data-ttu-id="89539-208">Intervallo tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-208">Retry interval</span></span><br /><span data-ttu-id="89539-209">Primo tentativo rapido</span><span class="sxs-lookup"><span data-stu-id="89539-209">First fast retry</span></span> |<span data-ttu-id="89539-210">3</span><span class="sxs-lookup"><span data-stu-id="89539-210">3</span></span><br /><span data-ttu-id="89539-211">500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-211">500 ms</span></span><br /><span data-ttu-id="89539-212">true</span><span class="sxs-lookup"><span data-stu-id="89539-212">true</span></span> |<span data-ttu-id="89539-213">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="89539-213">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="89539-214">Tentativo 2 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-214">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="89539-215">Tentativo 3 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-215">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="89539-216">Background</span><span class="sxs-lookup"><span data-stu-id="89539-216">Background or</span></span><br /><span data-ttu-id="89539-217"> o batch</span><span class="sxs-lookup"><span data-stu-id="89539-217">batch</span></span> |<span data-ttu-id="89539-218">60 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-218">60 sec</span></span> |<span data-ttu-id="89539-219">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-219">ExponentialBackoff</span></span> |<span data-ttu-id="89539-220">Numero tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-220">Retry count</span></span><br /><span data-ttu-id="89539-221">Interruzione temporanea minima</span><span class="sxs-lookup"><span data-stu-id="89539-221">Min back-off</span></span><br /><span data-ttu-id="89539-222">Interruzione temporanea massima</span><span class="sxs-lookup"><span data-stu-id="89539-222">Max back-off</span></span><br /><span data-ttu-id="89539-223">Interruzione temporanea delta</span><span class="sxs-lookup"><span data-stu-id="89539-223">Delta back-off</span></span><br /><span data-ttu-id="89539-224">Primo tentativo rapido</span><span class="sxs-lookup"><span data-stu-id="89539-224">First fast retry</span></span> |<span data-ttu-id="89539-225">5</span><span class="sxs-lookup"><span data-stu-id="89539-225">5</span></span><br /><span data-ttu-id="89539-226">0 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-226">0 sec</span></span><br /><span data-ttu-id="89539-227">60 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-227">60 sec</span></span><br /><span data-ttu-id="89539-228">2 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-228">2 sec</span></span><br /><span data-ttu-id="89539-229">false</span><span class="sxs-lookup"><span data-stu-id="89539-229">false</span></span> |<span data-ttu-id="89539-230">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="89539-230">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="89539-231">Tentativo 2 - intervallo di ~2 sec</span><span class="sxs-lookup"><span data-stu-id="89539-231">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="89539-232">Tentativo 3 - intervallo di ~6 sec</span><span class="sxs-lookup"><span data-stu-id="89539-232">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="89539-233">Tentativo 4 - intervallo di ~14 sec</span><span class="sxs-lookup"><span data-stu-id="89539-233">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="89539-234">Tentativo 5 - intervallo di 30 sec</span><span class="sxs-lookup"><span data-stu-id="89539-234">Attempt 5 - delay ~30 sec</span></span> |

### <a name="more-information"></a><span data-ttu-id="89539-235">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-235">More information</span></span>

- <span data-ttu-id="89539-236">[Librerie di autenticazione di Azure Active Directory][adal]</span><span class="sxs-lookup"><span data-stu-id="89539-236">[Azure Active Directory Authentication Libraries][adal]</span></span>

## <a name="cosmos-db"></a><span data-ttu-id="89539-237">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="89539-237">Cosmos DB</span></span>

<span data-ttu-id="89539-238">Cosmos DB è un database multi-modello completamente gestito che supporta i dati JSON senza schema.</span><span class="sxs-lookup"><span data-stu-id="89539-238">Cosmos DB is a fully-managed multi-model database that supports schema-less JSON data.</span></span> <span data-ttu-id="89539-239">Offre prestazioni affidabili e configurabili, consente l'elaborazione transazionale JavaScript nativa e, grazie alla scalabilità elastica, è ottimizzato per il cloud.</span><span class="sxs-lookup"><span data-stu-id="89539-239">It offers configurable and reliable performance, native JavaScript transactional processing, and is built for the cloud with elastic scale.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-240">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-240">Retry mechanism</span></span>

<span data-ttu-id="89539-241">La classe `DocumentClient` esegue nuovi tentativi in automatico.</span><span class="sxs-lookup"><span data-stu-id="89539-241">The `DocumentClient` class automatically retries failed attempts.</span></span> <span data-ttu-id="89539-242">Per impostare il numero di tentativi e il tempo di attesa massimo, configurare [ConnectionPolicy.RetryOptions].</span><span class="sxs-lookup"><span data-stu-id="89539-242">To set the number of retries and the maximum wait time, configure [ConnectionPolicy.RetryOptions].</span></span> <span data-ttu-id="89539-243">Le eccezioni generate dal client escludono il criterio di ripetizione oppure non sono errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="89539-243">Exceptions that the client raises are either beyond the retry policy or are not transient errors.</span></span>

<span data-ttu-id="89539-244">Se Cosmos DB limita il client, viene restituito un errore HTTP 429.</span><span class="sxs-lookup"><span data-stu-id="89539-244">If Cosmos DB throttles the client, it returns an HTTP 429 error.</span></span> <span data-ttu-id="89539-245">Verificare il codice di stato in `DocumentClientException`.</span><span class="sxs-lookup"><span data-stu-id="89539-245">Check the status code in the `DocumentClientException`.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="89539-246">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="89539-246">Policy configuration</span></span>

<span data-ttu-id="89539-247">La tabella seguente mostra le impostazioni predefinite per la classe `RetryOptions`.</span><span class="sxs-lookup"><span data-stu-id="89539-247">The following table shows the default settings for the `RetryOptions` class.</span></span>

| <span data-ttu-id="89539-248">Impostazione</span><span class="sxs-lookup"><span data-stu-id="89539-248">Setting</span></span> | <span data-ttu-id="89539-249">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="89539-249">Default value</span></span> | <span data-ttu-id="89539-250">DESCRIZIONE</span><span class="sxs-lookup"><span data-stu-id="89539-250">Description</span></span> |
| --- | --- | --- |
| <span data-ttu-id="89539-251">MaxRetryAttemptsOnThrottledRequests</span><span class="sxs-lookup"><span data-stu-id="89539-251">MaxRetryAttemptsOnThrottledRequests</span></span> |<span data-ttu-id="89539-252">9</span><span class="sxs-lookup"><span data-stu-id="89539-252">9</span></span> |<span data-ttu-id="89539-253">Il numero massimo di tentativi se la richiesta ha esito negativo a causa della limitazione applicata da Cosmos DB alla velocità del client.</span><span class="sxs-lookup"><span data-stu-id="89539-253">The maximum number of retries if the request fails because Cosmos DB applied rate limiting on the client.</span></span> |
| <span data-ttu-id="89539-254">MaxRetryWaitTimeInSeconds</span><span class="sxs-lookup"><span data-stu-id="89539-254">MaxRetryWaitTimeInSeconds</span></span> |<span data-ttu-id="89539-255">30</span><span class="sxs-lookup"><span data-stu-id="89539-255">30</span></span> |<span data-ttu-id="89539-256">Il tempo massimo per i tentativi in secondi.</span><span class="sxs-lookup"><span data-stu-id="89539-256">The maximum retry time in seconds.</span></span> |

### <a name="example"></a><span data-ttu-id="89539-257">Esempio</span><span class="sxs-lookup"><span data-stu-id="89539-257">Example</span></span>

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); ;
var options = client.ConnectionPolicy.RetryOptions;
options.MaxRetryAttemptsOnThrottledRequests = 5;
options.MaxRetryWaitTimeInSeconds = 15;
```

### <a name="telemetry"></a><span data-ttu-id="89539-258">Telemetria</span><span class="sxs-lookup"><span data-stu-id="89539-258">Telemetry</span></span>

<span data-ttu-id="89539-259">I tentativi vengono registrati come messaggi di traccia non strutturati tramite un oggetto **TraceSource**in .NET.</span><span class="sxs-lookup"><span data-stu-id="89539-259">Retry attempts are logged as unstructured trace messages through a .NET **TraceSource**.</span></span> <span data-ttu-id="89539-260">È necessario configurare un oggetto **TraceListener** per acquisire gli eventi e scriverli in un log di destinazione appropriato.</span><span class="sxs-lookup"><span data-stu-id="89539-260">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span>

<span data-ttu-id="89539-261">Ad esempio, se si aggiunge il seguente codice al file App.config in uso, verranno generate tracce in un file di testo nello stesso percorso dell'eseguibile:</span><span class="sxs-lookup"><span data-stu-id="89539-261">For example, if you add the following to your App.config file, traces will be generated in a text file in the same location as the executable:</span></span>

```xml
<configuration>
  <system.diagnostics>
    <switches>
      <add name="SourceSwitch" value="Verbose"/>
    </switches>
    <sources>
      <source name="DocDBTrace" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" >
        <listeners>
          <add name="MyTextListener" type="System.Diagnostics.TextWriterTraceListener" traceOutputOptions="DateTime,ProcessId,ThreadId" initializeData="CosmosDBTrace.txt"></add>
        </listeners>
      </source>
    </sources>
  </system.diagnostics>
</configuration>
```

## <a name="event-hubs"></a><span data-ttu-id="89539-262">Hub eventi</span><span class="sxs-lookup"><span data-stu-id="89539-262">Event Hubs</span></span>

<span data-ttu-id="89539-263">Hub eventi di Azure è un servizio di inserimento di dati di telemetria su vastissima scala che raccoglie, trasforma e archivia milioni di eventi.</span><span class="sxs-lookup"><span data-stu-id="89539-263">Azure Event Hubs is a hyper-scale telemetry ingestion service that collects, transforms, and stores millions of events.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-264">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-264">Retry mechanism</span></span>

<span data-ttu-id="89539-265">Il comportamento di ripetizione dei tentativi nella libreria client dell'Hub eventi di Azure è controllato dalla proprietà `RetryPolicy` nella classe `EventHubClient`.</span><span class="sxs-lookup"><span data-stu-id="89539-265">Retry behavior in the Azure Event Hubs Client Library is controlled by the `RetryPolicy` property on the `EventHubClient` class.</span></span> <span data-ttu-id="89539-266">I criteri predefiniti eseguono nuovi tentativi con backoff esponenziale quando Hub eventi di Azure restituisce `EventHubsException` o `OperationCanceledException` temporanei.</span><span class="sxs-lookup"><span data-stu-id="89539-266">The default policy retries with exponential backoff when Azure Event Hub returns a transient `EventHubsException` or an `OperationCanceledException`.</span></span>

### <a name="example"></a><span data-ttu-id="89539-267">Esempio</span><span class="sxs-lookup"><span data-stu-id="89539-267">Example</span></span>

```csharp
EventHubClient client = EventHubClient.CreateFromConnectionString("[event_hub_connection_string]");
client.RetryPolicy = RetryPolicy.Default;
```

### <a name="more-information"></a><span data-ttu-id="89539-268">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-268">More information</span></span>

[<span data-ttu-id="89539-269">Libreria client di .NET standard per gli Hub eventi di Azure</span><span class="sxs-lookup"><span data-stu-id="89539-269">.NET Standard client library for Azure Event Hubs</span></span>](https://github.com/Azure/azure-event-hubs-dotnet)

## <a name="iot-hub"></a><span data-ttu-id="89539-270">Hub IoT</span><span class="sxs-lookup"><span data-stu-id="89539-270">IoT Hub</span></span>

<span data-ttu-id="89539-271">Hub IoT di Azure è un servizio per la connessione, il monitoraggio e la gestione di dispositivi per lo sviluppo di applicazioni Internet delle cose (IoT).</span><span class="sxs-lookup"><span data-stu-id="89539-271">Azure IoT Hub is a service for connecting, monitoring, and managing devices to develop Internet of Things (IoT) applications.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-272">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-272">Retry mechanism</span></span>

<span data-ttu-id="89539-273">Azure IoT SDK per dispositivi è in grado di rilevare errori nella rete, nel protocollo o nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="89539-273">The Azure IoT device SDK can detect errors in the network, protocol, or application.</span></span> <span data-ttu-id="89539-274">In base al tipo di errore, l'SDK verifica se è necessario eseguire un nuovo tentativo.</span><span class="sxs-lookup"><span data-stu-id="89539-274">Based on the error type, the SDK checks whether a retry needs to be performed.</span></span> <span data-ttu-id="89539-275">Se l'errore è *reversibile*, l'SDK avvia un nuovo tentativo usando i criteri di ripetizione configurati.</span><span class="sxs-lookup"><span data-stu-id="89539-275">If the error is *recoverable*, the SDK begins to retry using the configured retry policy.</span></span>

<span data-ttu-id="89539-276">I criteri di ripetizione predefiniti prevedono il *backoff esponenziale con instabilità casuale*, ma possono essere configurati.</span><span class="sxs-lookup"><span data-stu-id="89539-276">The default retry policy is *exponential back-off with random jitter*, but it can be configured.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="89539-277">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="89539-277">Policy configuration</span></span>

<span data-ttu-id="89539-278">La configurazione dei criteri varia a seconda del linguaggio.</span><span class="sxs-lookup"><span data-stu-id="89539-278">Policy configuration differs by language.</span></span> <span data-ttu-id="89539-279">Per maggiori dettagli, vedere la [configurazione dei criteri di ripetizione per l'hub IoT](/azure/iot-hub/iot-hub-reliability-features-in-sdks#retry-policy-apis).</span><span class="sxs-lookup"><span data-stu-id="89539-279">For more details, see [IoT Hub retry policy configuration](/azure/iot-hub/iot-hub-reliability-features-in-sdks#retry-policy-apis).</span></span>

### <a name="more-information"></a><span data-ttu-id="89539-280">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-280">More information</span></span>

- [<span data-ttu-id="89539-281">Criteri di ripetizione per l'hub IoT</span><span class="sxs-lookup"><span data-stu-id="89539-281">IoT Hub retry policy</span></span>](/azure/iot-hub/iot-hub-reliability-features-in-sdks)
- [<span data-ttu-id="89539-282">Risoluzione dei problemi di disconnessione dei dispositivi hub IoT</span><span class="sxs-lookup"><span data-stu-id="89539-282">Troubleshoot IoT Hub device disconnection</span></span>](/azure/iot-hub/iot-hub-troubleshoot-connectivity)

## <a name="azure-redis-cache"></a><span data-ttu-id="89539-283">Cache Redis di Azure</span><span class="sxs-lookup"><span data-stu-id="89539-283">Azure Redis Cache</span></span>

<span data-ttu-id="89539-284">Cache Redis di Azure è un servizio cache a bassa latenza e di rapido accesso ai dati basato sulla nota Cache Redis open source.</span><span class="sxs-lookup"><span data-stu-id="89539-284">Azure Redis Cache is a fast data access and low latency cache service based on the popular open source Redis Cache.</span></span> <span data-ttu-id="89539-285">È un servizio protetto, gestito da Microsoft e accessibile da qualsiasi applicazione in Azure.</span><span class="sxs-lookup"><span data-stu-id="89539-285">It is secure, managed by Microsoft, and is accessible from any application in Azure.</span></span>

<span data-ttu-id="89539-286">Le indicazioni fornite in questa sezione presuppongono che si usi il client StackExchange.Redis per accedere alla cache.</span><span class="sxs-lookup"><span data-stu-id="89539-286">The guidance in this section is based on using the StackExchange.Redis client to access the cache.</span></span> <span data-ttu-id="89539-287">Nel [sito Web di Redis](https://redis.io/clients)è disponibile un elenco di altri client idonei, a cui possono essere associati vari meccanismi di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-287">A list of other suitable clients can be found on the [Redis website](https://redis.io/clients), and these may have different retry mechanisms.</span></span>

<span data-ttu-id="89539-288">Il client StackExchange.Redis usa il multiplexing tramite un'unica connessione.</span><span class="sxs-lookup"><span data-stu-id="89539-288">Note that the StackExchange.Redis client uses multiplexing through a single connection.</span></span> <span data-ttu-id="89539-289">È consigliabile quindi creare un'istanza del client all'avvio dell'applicazione e usarla per tutte le operazioni eseguite sulla cache.</span><span class="sxs-lookup"><span data-stu-id="89539-289">The recommended usage is to create an instance of the client at application startup and use this instance for all operations against the cache.</span></span> <span data-ttu-id="89539-290">Per questo motivo, la connessione alla cache viene eseguita una sola volta e quindi tutte le indicazioni fornite in questa sezione è correlata ai criteri di ripetizione dei tentativi per la connessione iniziale &mdash; e non per ogni operazione che accede alla cache.</span><span class="sxs-lookup"><span data-stu-id="89539-290">For this reason, the connection to the cache is made only once, and so all of the guidance in this section is related to the retry policy for this initial connection &mdash; and not for each operation that accesses the cache.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-291">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-291">Retry mechanism</span></span>

<span data-ttu-id="89539-292">Il client StackExchange.Redis usa una classe di gestione della connessione configurata tramite un set di opzioni, che include:</span><span class="sxs-lookup"><span data-stu-id="89539-292">The StackExchange.Redis client uses a connection manager class that is configured through a set of options, including:</span></span>

- <span data-ttu-id="89539-293">**ConnectRetry**.</span><span class="sxs-lookup"><span data-stu-id="89539-293">**ConnectRetry**.</span></span> <span data-ttu-id="89539-294">Il numero di volte in cui una connessione non riuscita per la cache verrà ritentata.</span><span class="sxs-lookup"><span data-stu-id="89539-294">The number of times a failed connection to the cache will be retried.</span></span>
- <span data-ttu-id="89539-295">**ReconnectRetryPolicy**.</span><span class="sxs-lookup"><span data-stu-id="89539-295">**ReconnectRetryPolicy**.</span></span> <span data-ttu-id="89539-296">La strategia di ripetizione dei tentativi da usare.</span><span class="sxs-lookup"><span data-stu-id="89539-296">The retry strategy to use.</span></span>
- <span data-ttu-id="89539-297">**ConnectTimeout**.</span><span class="sxs-lookup"><span data-stu-id="89539-297">**ConnectTimeout**.</span></span> <span data-ttu-id="89539-298">Il tempo di attesa massimo in millisecondi.</span><span class="sxs-lookup"><span data-stu-id="89539-298">The maximum waiting time in milliseconds.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="89539-299">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="89539-299">Policy configuration</span></span>

<span data-ttu-id="89539-300">I criteri di ripetizione dei tentativi vengono configurati a livello di codice impostando le opzioni per il client prima di connettersi alla cache.</span><span class="sxs-lookup"><span data-stu-id="89539-300">Retry policies are configured programmatically by setting the options for the client before connecting to the cache.</span></span> <span data-ttu-id="89539-301">Questa operazione può essere eseguita creando un'istanza della classe **ConfigurationOptions**, popolandone le proprietà e passandola al metodo **Connect**.</span><span class="sxs-lookup"><span data-stu-id="89539-301">This can be done by creating an instance of the **ConfigurationOptions** class, populating its properties, and passing it to the **Connect** method.</span></span>

<span data-ttu-id="89539-302">Le classi incorporate supportano i ritardi lineari, ovvero il tempo d'intervallo costante, e i backoff esponenziali con intervalli di ripetizione dei tentativi casuali.</span><span class="sxs-lookup"><span data-stu-id="89539-302">The built-in classes support linear (constant) delay and exponential backoff with randomized retry intervals.</span></span> <span data-ttu-id="89539-303">È anche possibile creare un criterio di ripetizione dei tentativi personalizzato implementando l'interfaccia **IReconnectRetryPolicy**.</span><span class="sxs-lookup"><span data-stu-id="89539-303">You can also create a custom retry policy by implementing the **IReconnectRetryPolicy** interface.</span></span>

<span data-ttu-id="89539-304">L'esempio seguente consente di configurare una strategia di ripetizione dei tentativi con backoff esponenziale.</span><span class="sxs-lookup"><span data-stu-id="89539-304">The following example configures a retry strategy using exponential backoff.</span></span>

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

<span data-ttu-id="89539-305">In alternativa, è possibile specificare le opzioni sotto forma di stringa, per poi passarla al metodo **Connect** .</span><span class="sxs-lookup"><span data-stu-id="89539-305">Alternatively, you can specify the options as a string, and pass this to the **Connect** method.</span></span> <span data-ttu-id="89539-306">Si noti che la proprietà **ReconnectRetryPolicy** non può essere impostata in questo modo, ma solo tramite codice.</span><span class="sxs-lookup"><span data-stu-id="89539-306">Note that the **ReconnectRetryPolicy** property cannot be set this way, only through code.</span></span>

```csharp
var options = "localhost,connectRetry=3,connectTimeout=2000";
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="89539-307">È anche possibile specificare le opzioni direttamente durante la connessione alla cache.</span><span class="sxs-lookup"><span data-stu-id="89539-307">You can also specify options directly when you connect to the cache.</span></span>

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

<span data-ttu-id="89539-308">Per altre informazioni, vedere [Configurazione di Stack Exchange Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration) nella documentazione di StackExchange.Redis.</span><span class="sxs-lookup"><span data-stu-id="89539-308">For more information, see [Stack Exchange Redis Configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration) in the StackExchange.Redis documentation.</span></span>

<span data-ttu-id="89539-309">La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.</span><span class="sxs-lookup"><span data-stu-id="89539-309">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="89539-310">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="89539-310">**Context**</span></span> | <span data-ttu-id="89539-311">**Impostazione**</span><span class="sxs-lookup"><span data-stu-id="89539-311">**Setting**</span></span> | <span data-ttu-id="89539-312">**Valore predefinito**</span><span class="sxs-lookup"><span data-stu-id="89539-312">**Default value**</span></span><br /><span data-ttu-id="89539-313">(v 1.2.2)</span><span class="sxs-lookup"><span data-stu-id="89539-313">(v 1.2.2)</span></span> | <span data-ttu-id="89539-314">**Significato**</span><span class="sxs-lookup"><span data-stu-id="89539-314">**Meaning**</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="89539-315">Opzioni di configurazione</span><span class="sxs-lookup"><span data-stu-id="89539-315">ConfigurationOptions</span></span> |<span data-ttu-id="89539-316">ConnectRetry</span><span class="sxs-lookup"><span data-stu-id="89539-316">ConnectRetry</span></span><br /><br /><span data-ttu-id="89539-317">ConnectTimeout</span><span class="sxs-lookup"><span data-stu-id="89539-317">ConnectTimeout</span></span><br /><br /><span data-ttu-id="89539-318">SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="89539-318">SyncTimeout</span></span><br /><br /><span data-ttu-id="89539-319">ReconnectRetryPolicy</span><span class="sxs-lookup"><span data-stu-id="89539-319">ReconnectRetryPolicy</span></span> |<span data-ttu-id="89539-320">3</span><span class="sxs-lookup"><span data-stu-id="89539-320">3</span></span><br /><br /><span data-ttu-id="89539-321">Massimo 5000 ms più SyncTimeout</span><span class="sxs-lookup"><span data-stu-id="89539-321">Maximum 5000 ms plus SyncTimeout</span></span><br /><span data-ttu-id="89539-322">1000</span><span class="sxs-lookup"><span data-stu-id="89539-322">1000</span></span><br /><br /><span data-ttu-id="89539-323">LinearRetry 5.000 ms</span><span class="sxs-lookup"><span data-stu-id="89539-323">LinearRetry 5000 ms</span></span> |<span data-ttu-id="89539-324">Numero di nuovi tentativi di connessione durante l'operazione di connessione iniziale.</span><span class="sxs-lookup"><span data-stu-id="89539-324">The number of times to repeat connect attempts during the initial connection operation.</span></span><br /><span data-ttu-id="89539-325">Timeout (ms) per operazioni di connessione.</span><span class="sxs-lookup"><span data-stu-id="89539-325">Timeout (ms) for connect operations.</span></span> <span data-ttu-id="89539-326">Non un intervallo tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-326">Not a delay between retry attempts.</span></span><br /><span data-ttu-id="89539-327">Tempo (ms) concesso per consentire operazioni sincrone.</span><span class="sxs-lookup"><span data-stu-id="89539-327">Time (ms) to allow for synchronous operations.</span></span><br /><br /><span data-ttu-id="89539-328">Ripetizione dei tentativi ogni 5.000 ms.</span><span class="sxs-lookup"><span data-stu-id="89539-328">Retry every 5000 ms.</span></span>|

> [!NOTE]
> <span data-ttu-id="89539-329">Per le operazioni sincrone, è possibile aggiungere `SyncTimeout` alla latenza end-to-end, ma impostare un valore troppo basso può causare timeout eccessivi.</span><span class="sxs-lookup"><span data-stu-id="89539-329">For synchronous operations, `SyncTimeout` can add to the end-to-end latency, but setting the value too low can cause excessive timeouts.</span></span> <span data-ttu-id="89539-330">Vedere [Come risolvere i problemi di Cache Redis di Azure][redis-cache-troubleshoot].</span><span class="sxs-lookup"><span data-stu-id="89539-330">See [How to troubleshoot Azure Redis Cache][redis-cache-troubleshoot].</span></span> <span data-ttu-id="89539-331">In generale, evitare di usare operazioni sincrone, usare invece operazioni asincrone.</span><span class="sxs-lookup"><span data-stu-id="89539-331">In general, avoid using synchronous operations, and use asynchronous operations instead.</span></span> <span data-ttu-id="89539-332">Per altre informazioni, vedere [Pipeline e multiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/PipelinesMultiplexers.md).</span><span class="sxs-lookup"><span data-stu-id="89539-332">For more information see [Pipelines and Multiplexers](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/PipelinesMultiplexers.md).</span></span>

### <a name="retry-usage-guidance"></a><span data-ttu-id="89539-333">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-333">Retry usage guidance</span></span>

<span data-ttu-id="89539-334">Quando si usa Cache Redis di Azure, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="89539-334">Consider the following guidelines when using Azure Redis Cache:</span></span>

- <span data-ttu-id="89539-335">Il client StackExchange Redis gestisce autonomamente i propri tentativi, ma solo se viene stabilita una connessione alla cache al primo avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="89539-335">The StackExchange Redis client manages its own retries, but only when establishing a connection to the cache when the application first starts.</span></span> <span data-ttu-id="89539-336">È possibile configurare il timeout di connessione, il numero di ripetizione di tentativi e l'intervallo di tempo tra i tentativi per stabilire la connessione, ma i criteri di ripetizione non si applicano alle operazioni eseguite sulla cache.</span><span class="sxs-lookup"><span data-stu-id="89539-336">You can configure the connection timeout, the number of retry attempts, and the time between retries to establish this connection, but the retry policy does not apply to operations against the cache.</span></span>
- <span data-ttu-id="89539-337">Anziché ricorrere a un numero elevato di tentativi, valutare la possibilità di eseguire il fallback accedendo alla sorgente originale dei dati.</span><span class="sxs-lookup"><span data-stu-id="89539-337">Instead of using a large number of retry attempts, consider falling back by accessing the original data source instead.</span></span>

### <a name="telemetry"></a><span data-ttu-id="89539-338">Telemetria</span><span class="sxs-lookup"><span data-stu-id="89539-338">Telemetry</span></span>

<span data-ttu-id="89539-339">È possibile raccogliere informazioni sulle connessioni, ma su non altre operazioni, usando un **TextWriter**.</span><span class="sxs-lookup"><span data-stu-id="89539-339">You can collect information about connections (but not other operations) using a **TextWriter**.</span></span>

```csharp
var writer = new StringWriter();
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

<span data-ttu-id="89539-340">Seguito è riportato un esempio di output generato.</span><span class="sxs-lookup"><span data-stu-id="89539-340">An example of the output this generates is shown below.</span></span>

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

### <a name="examples"></a><span data-ttu-id="89539-341">Esempi</span><span class="sxs-lookup"><span data-stu-id="89539-341">Examples</span></span>

<span data-ttu-id="89539-342">L'esempio di codice seguente consente di configurare un intervallo costante, ovvero lineare, tra le ripetizioni di tentativi durante l'inizializzazione del client StackExchange.Redis.</span><span class="sxs-lookup"><span data-stu-id="89539-342">The following code example configures a constant (linear) delay between retries when initializing the StackExchange.Redis client.</span></span> <span data-ttu-id="89539-343">Questo esempio illustra come impostare la configurazione usando un'istanza **ConfigurationOptions**.</span><span class="sxs-lookup"><span data-stu-id="89539-343">This example shows how to set the configuration using a **ConfigurationOptions** instance.</span></span>

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

<span data-ttu-id="89539-344">L'esempio successivo imposta la configurazione specificando le opzioni sotto forma di stringa.</span><span class="sxs-lookup"><span data-stu-id="89539-344">The next example sets the configuration by specifying the options as a string.</span></span> <span data-ttu-id="89539-345">Il timeout di connessione è l'intervallo di tempo massimo di attesa per la connessione alla cache, non l'intervallo tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-345">The connection timeout is the maximum period of time to wait for a connection to the cache, not the delay between retry attempts.</span></span> <span data-ttu-id="89539-346">Si noti che la proprietà **ReconnectRetryPolicy** può essere impostata solo dal codice.</span><span class="sxs-lookup"><span data-stu-id="89539-346">Note that the **ReconnectRetryPolicy** property can only be set by code.</span></span>

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

<span data-ttu-id="89539-347">Per altri esempi, vedere la sezione relativa alla [configurazione](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Configuration.md) sul sito Web di progetto.</span><span class="sxs-lookup"><span data-stu-id="89539-347">For more examples, see [Configuration](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Configuration.md) on the project website.</span></span>

### <a name="more-information"></a><span data-ttu-id="89539-348">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-348">More information</span></span>

- [<span data-ttu-id="89539-349">Sito Web di Redis</span><span class="sxs-lookup"><span data-stu-id="89539-349">Redis website</span></span>](https://redis.io/)

## <a name="azure-search"></a><span data-ttu-id="89539-350">Ricerca di Azure</span><span class="sxs-lookup"><span data-stu-id="89539-350">Azure Search</span></span>

<span data-ttu-id="89539-351">Lo strumento Ricerca di Azure può essere usato per aggiungere sofisticate e avanzate funzionalità di ricerca in un sito Web o un'applicazione, ottimizzare i risultati di ricerca in modo semplice e rapido e creare avanzati modelli di classificazione.</span><span class="sxs-lookup"><span data-stu-id="89539-351">Azure Search can be used to add powerful and sophisticated search capabilities to a website or application, quickly and easily tune search results, and construct rich and fine-tuned ranking models.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-352">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-352">Retry mechanism</span></span>

<span data-ttu-id="89539-353">Il comportamento di ripetizione dei tentativi nell'SDK di Ricerca di Azure è controllato dal metodo `SetRetryPolicy` nelle classi [SearchServiceClient] e [SearchIndexClient].</span><span class="sxs-lookup"><span data-stu-id="89539-353">Retry behavior in the Azure Search SDK is controlled by the `SetRetryPolicy` method on the [SearchServiceClient] and [SearchIndexClient] classes.</span></span> <span data-ttu-id="89539-354">I criteri predefiniti eseguono nuovi tentativi con backoff esponenziale quando Ricerca di Azure restituisce una risposta 5xx o 408 (Timeout della richiesta).</span><span class="sxs-lookup"><span data-stu-id="89539-354">The default policy retries with exponential backoff when Azure Search returns a 5xx or 408 (Request Timeout) response.</span></span>

### <a name="telemetry"></a><span data-ttu-id="89539-355">Telemetria</span><span class="sxs-lookup"><span data-stu-id="89539-355">Telemetry</span></span>

<span data-ttu-id="89539-356">Tracciare con ETW o registrando un provider di traccia personalizzato.</span><span class="sxs-lookup"><span data-stu-id="89539-356">Trace with ETW or by registering a custom trace provider.</span></span> <span data-ttu-id="89539-357">Per altre informazioni, consultare la [documentazione di AutoRest][autorest].</span><span class="sxs-lookup"><span data-stu-id="89539-357">For more information, see the [AutoRest documentation][autorest].</span></span>

## <a name="service-bus"></a><span data-ttu-id="89539-358">Bus di servizio</span><span class="sxs-lookup"><span data-stu-id="89539-358">Service Bus</span></span>

<span data-ttu-id="89539-359">Il bus di servizio è una piattaforma di messaggistica basata sul cloud che offre una funzione di scambio dei messaggi di tipo "loosely coupled" con alti livelli di scalabilità e resilienza per i componenti di un'applicazione (ospitata nel cloud o in locale).</span><span class="sxs-lookup"><span data-stu-id="89539-359">Service Bus is a cloud messaging platform that provides loosely coupled message exchange with improved scale and resiliency for components of an application, whether hosted in the cloud or on-premises.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-360">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-360">Retry mechanism</span></span>

<span data-ttu-id="89539-361">Il bus di servizio implementa la ripetizione dei tentativi usando implementazioni della classe di base [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) .</span><span class="sxs-lookup"><span data-stu-id="89539-361">Service Bus implements retries using implementations of the [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) base class.</span></span> <span data-ttu-id="89539-362">Tutti i client del bus di servizio espongono una proprietà **RetryPolicy** che può essere impostata in una delle implementazioni della classe di base **RetryPolicy**.</span><span class="sxs-lookup"><span data-stu-id="89539-362">All of the Service Bus clients expose a **RetryPolicy** property that can be set to one of the implementations of the **RetryPolicy** base class.</span></span> <span data-ttu-id="89539-363">Le implementazioni predefinite sono:</span><span class="sxs-lookup"><span data-stu-id="89539-363">The built-in implementations are:</span></span>

- <span data-ttu-id="89539-364">La [classe RetryExponential](/dotnet/api/microsoft.servicebus.retryexponential).</span><span class="sxs-lookup"><span data-stu-id="89539-364">The [RetryExponential class](/dotnet/api/microsoft.servicebus.retryexponential).</span></span> <span data-ttu-id="89539-365">Espone proprietà che controllano l'intervallo di backoff, il numero di tentativi e la proprietà **TerminationTimeBuffer** usata per limitare il tempo complessivo per il completamento dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="89539-365">This exposes properties that control the back-off interval, the retry count, and the **TerminationTimeBuffer** property that is used to limit the total time for the operation to complete.</span></span>

- <span data-ttu-id="89539-366">La [classe NoRetry](/dotnet/api/microsoft.servicebus.noretry).</span><span class="sxs-lookup"><span data-stu-id="89539-366">The [NoRetry class](/dotnet/api/microsoft.servicebus.noretry).</span></span> <span data-ttu-id="89539-367">Viene usata quando non è necessario eseguire nuovi tentativi al livello dell'API del bus di servizio, ad esempio quando i nuovi tentativi vengono gestiti da un altro processo nell'ambito di un'operazione in batch o composta da più passaggi.</span><span class="sxs-lookup"><span data-stu-id="89539-367">This is used when retries at the Service Bus API level are not required, such as when retries are managed by another process as part of a batch or multiple step operation.</span></span>

<span data-ttu-id="89539-368">Le azioni del bus di servizio possono restituire una serie di eccezioni, elencate in [Eccezioni di messaggistica del bus di servizio](/azure/service-bus-messaging/service-bus-messaging-exceptions).</span><span class="sxs-lookup"><span data-stu-id="89539-368">Service Bus actions can return a range of exceptions, as listed in [Service Bus messaging exceptions](/azure/service-bus-messaging/service-bus-messaging-exceptions).</span></span> <span data-ttu-id="89539-369">L'elenco fornisce informazioni su quali delle eccezioni indicano che è opportuno ripetere l'operazione.</span><span class="sxs-lookup"><span data-stu-id="89539-369">The list provides information about which if these indicate that retrying the operation is appropriate.</span></span> <span data-ttu-id="89539-370">**ServerBusyException** , ad esempio, indica che il client deve attendere un determinato intervallo di tempo, quindi ripetere l'operazione.</span><span class="sxs-lookup"><span data-stu-id="89539-370">For example, a **ServerBusyException** indicates that the client should wait for a period of time, then retry the operation.</span></span> <span data-ttu-id="89539-371">Un'occorrenza di **ServerBusyException** induce inoltre il bus di servizio a passare a una modalità diversa, in cui viene aggiunto un ulteriore intervallo di 10 secondi all'intervallo tra i tentativi elaborato.</span><span class="sxs-lookup"><span data-stu-id="89539-371">The occurrence of a **ServerBusyException** also causes Service Bus to switch to a different mode, in which an extra 10-second delay is added to the computed retry delays.</span></span> <span data-ttu-id="89539-372">Questa modalità viene reimpostata dopo un breve intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="89539-372">This mode is reset after a short period.</span></span>

<span data-ttu-id="89539-373">Le eccezioni restituite dal bus di servizio espongono la proprietà **IsTransient** che indica se il client deve ripetere l'operazione.</span><span class="sxs-lookup"><span data-stu-id="89539-373">The exceptions returned from Service Bus expose the **IsTransient** property that indicates if the client should retry the operation.</span></span> <span data-ttu-id="89539-374">I criteri **RetryExponential** incorporati si basano sulla proprietà **IsTransient** della classe **MessagingException**, che costituisce la classe di base per tutte le eccezioni del bus di servizio.</span><span class="sxs-lookup"><span data-stu-id="89539-374">The built-in **RetryExponential** policy relies on the **IsTransient** property in the **MessagingException** class, which is the base class for all Service Bus exceptions.</span></span> <span data-ttu-id="89539-375">Se si creano implementazioni personalizzate della classe di base **RetryPolicy**, è possibile usare una combinazione del tipo di eccezione e della proprietà **IsTransient** per ottenere un controllo più preciso sulle azioni di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-375">If you create custom implementations of the **RetryPolicy** base class you could use a combination of the exception type and the **IsTransient** property to provide more fine-grained control over retry actions.</span></span> <span data-ttu-id="89539-376">Se si identifica un'eccezione di tipo **QuotaExceededException** , ad esempio, è possibile intraprendere le azioni necessarie per esaurire la coda prima di riprovare a inviare un messaggio.</span><span class="sxs-lookup"><span data-stu-id="89539-376">For example, you could detect a **QuotaExceededException** and take action to drain the queue before retrying sending a message to it.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="89539-377">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="89539-377">Policy configuration</span></span>

<span data-ttu-id="89539-378">I criteri di ripetizione dei tentativi vengono impostati a livello di codice e possono essere impostati come criteri predefiniti per **NamespaceManager** e **MessagingFactory** oppure impostati singolarmente per ogni client di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="89539-378">Retry policies are set programmatically, and can be set as a default policy for a **NamespaceManager** and for a **MessagingFactory**, or individually for each messaging client.</span></span> <span data-ttu-id="89539-379">Per impostare criteri di ripetizione dei tentativi predefiniti per una sessione di messaggistica, impostare la proprietà **RetryPolicy** di **NamespaceManager**.</span><span class="sxs-lookup"><span data-stu-id="89539-379">To set the default retry policy for a messaging session you set the **RetryPolicy** of the **NamespaceManager**.</span></span>

```csharp
namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                                maxBackoff: TimeSpan.FromSeconds(30),
                                                                maxRetryCount: 3);
```

<span data-ttu-id="89539-380">Per impostare criteri di ripetizione dei tentativi predefiniti per tutti i client da una factory di messaggistica, impostare la proprietà **RetryPolicy** di **MessagingFactory**.</span><span class="sxs-lookup"><span data-stu-id="89539-380">To set the default retry policy for all clients created from a messaging factory, you set the **RetryPolicy** of the **MessagingFactory**.</span></span>

```csharp
messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                                    maxBackoff: TimeSpan.FromSeconds(30),
                                                    maxRetryCount: 3);
```

<span data-ttu-id="89539-381">Per impostare criteri di ripetizione dei tentativi per un client di messaggistica o per ignorare i criteri predefiniti, impostarne la proprietà **RetryPolicy** usando un'istanza della classe di criteri necessaria:</span><span class="sxs-lookup"><span data-stu-id="89539-381">To set the retry policy for a messaging client, or to override its default policy, you set its **RetryPolicy** property using an instance of the required policy class:</span></span>

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
                                            maxBackoff: TimeSpan.FromSeconds(30),
                                            maxRetryCount: 3);
```

<span data-ttu-id="89539-382">I criteri di ripetizione dei tentativi non possono essere impostati a livello di singola operazione.</span><span class="sxs-lookup"><span data-stu-id="89539-382">The retry policy cannot be set at the individual operation level.</span></span> <span data-ttu-id="89539-383">Questo vale per tutte le operazioni relative al client di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="89539-383">It applies to all operations for the messaging client.</span></span>
<span data-ttu-id="89539-384">La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati.</span><span class="sxs-lookup"><span data-stu-id="89539-384">The following table shows the default settings for the built-in retry policy.</span></span>

| <span data-ttu-id="89539-385">Impostazione</span><span class="sxs-lookup"><span data-stu-id="89539-385">Setting</span></span> | <span data-ttu-id="89539-386">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="89539-386">Default value</span></span> | <span data-ttu-id="89539-387">Significato</span><span class="sxs-lookup"><span data-stu-id="89539-387">Meaning</span></span> |
|---------|---------------|---------|
| <span data-ttu-id="89539-388">Policy</span><span class="sxs-lookup"><span data-stu-id="89539-388">Policy</span></span> | <span data-ttu-id="89539-389">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="89539-389">Exponential</span></span> | <span data-ttu-id="89539-390">Backoff esponenziale.</span><span class="sxs-lookup"><span data-stu-id="89539-390">Exponential back-off.</span></span> |
| <span data-ttu-id="89539-391">MinimalBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-391">MinimalBackoff</span></span> | <span data-ttu-id="89539-392">0</span><span class="sxs-lookup"><span data-stu-id="89539-392">0</span></span> | <span data-ttu-id="89539-393">Intervallo minimo di backoff.</span><span class="sxs-lookup"><span data-stu-id="89539-393">Minimum back-off interval.</span></span> <span data-ttu-id="89539-394">Viene aggiunto all'intervallo per i tentativi calcolato a partire da deltaBackoff.</span><span class="sxs-lookup"><span data-stu-id="89539-394">This is added to the retry interval computed from deltaBackoff.</span></span> |
| <span data-ttu-id="89539-395">MaximumBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-395">MaximumBackoff</span></span> | <span data-ttu-id="89539-396">30 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-396">30 seconds</span></span> | <span data-ttu-id="89539-397">Intervallo massimo di backoff.</span><span class="sxs-lookup"><span data-stu-id="89539-397">Maximum back-off interval.</span></span> <span data-ttu-id="89539-398">Si usa MaximumBackoff se l'intervallo tra i tentativi calcolato è maggiore di MaxBackoff.</span><span class="sxs-lookup"><span data-stu-id="89539-398">MaximumBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> |
| <span data-ttu-id="89539-399">DeltaBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-399">DeltaBackoff</span></span> | <span data-ttu-id="89539-400">3 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-400">3 seconds</span></span> | <span data-ttu-id="89539-401">Intervallo di backoff tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-401">Back-off interval between retries.</span></span> <span data-ttu-id="89539-402">Per i successivi tentativi verranno utilizzati i multipli di questi timespan.</span><span class="sxs-lookup"><span data-stu-id="89539-402">Multiples of this timespan will be used for subsequent retry attempts.</span></span> |
| <span data-ttu-id="89539-403">TimeBuffer</span><span class="sxs-lookup"><span data-stu-id="89539-403">TimeBuffer</span></span> | <span data-ttu-id="89539-404">5 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-404">5 seconds</span></span> | <span data-ttu-id="89539-405">Il buffer temporale associato al tentativo.</span><span class="sxs-lookup"><span data-stu-id="89539-405">The termination time buffer associated with the retry.</span></span> <span data-ttu-id="89539-406">I tentativi verranno abbandonati se il tempo rimanente è minore del valore di TimeBuffer.</span><span class="sxs-lookup"><span data-stu-id="89539-406">Retry attempts will be abandoned if the remaining time is less than TimeBuffer.</span></span> |
| <span data-ttu-id="89539-407">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="89539-407">MaxRetryCount</span></span> | <span data-ttu-id="89539-408">10</span><span class="sxs-lookup"><span data-stu-id="89539-408">10</span></span> | <span data-ttu-id="89539-409">Numero massimo di tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-409">The maximum number of retries.</span></span> |
| <span data-ttu-id="89539-410">ServerBusyBaseSleepTime</span><span class="sxs-lookup"><span data-stu-id="89539-410">ServerBusyBaseSleepTime</span></span> | <span data-ttu-id="89539-411">10 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-411">10 seconds</span></span> | <span data-ttu-id="89539-412">Se l'ultima eccezione riscontrata è **ServerBusyException**, questo valore verrà aggiunto all'intervallo di ripetizione dei tentativi calcolato.</span><span class="sxs-lookup"><span data-stu-id="89539-412">If the last exception encountered was **ServerBusyException**, this value will be added to the computed retry interval.</span></span> <span data-ttu-id="89539-413">Questo valore non può essere modificato.</span><span class="sxs-lookup"><span data-stu-id="89539-413">This value cannot be changed.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="89539-414">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-414">Retry usage guidance</span></span>

<span data-ttu-id="89539-415">Quando si usa il bus di servizio, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="89539-415">Consider the following guidelines when using Service Bus:</span></span>

- <span data-ttu-id="89539-416">Se si sceglie l'implementazione **RetryExponential** incorporata, non implementare un'operazione di fallback poiché i criteri reagiscono alle eccezioni di tipo "server occupato" e viene automaticamente attivata una modalità di ripetizione dei tentativi appropriata al nuovo scenario.</span><span class="sxs-lookup"><span data-stu-id="89539-416">When using the built-in **RetryExponential** implementation, do not implement a fallback operation as the policy reacts to Server Busy exceptions and automatically switches to an appropriate retry mode.</span></span>
- <span data-ttu-id="89539-417">Il bus di servizio supporta una funzionalità relativa agli spazi dei nomi associati che implementa il failover automatico su una coda di backup di uno spazio dei nomi diverso in caso di esito negativo della coda nello spazio dei nomi primario.</span><span class="sxs-lookup"><span data-stu-id="89539-417">Service Bus supports a feature called Paired Namespaces, which implements automatic failover to a backup queue in a separate namespace if the queue in the primary namespace fails.</span></span> <span data-ttu-id="89539-418">I messaggi ricevuti dalla coda secondaria possono essere nuovamente inviati alla coda primaria nel momento in cui viene ripristinata.</span><span class="sxs-lookup"><span data-stu-id="89539-418">Messages from the secondary queue can be sent back to the primary queue when it recovers.</span></span> <span data-ttu-id="89539-419">Questa funzionalità consente di risolvere eventuali problemi temporanei.</span><span class="sxs-lookup"><span data-stu-id="89539-419">This feature helps to address transient failures.</span></span> <span data-ttu-id="89539-420">Per altre informazioni, vedere [Modelli di messaggistica asincrona e disponibilità elevata](/azure/service-bus-messaging/service-bus-async-messaging).</span><span class="sxs-lookup"><span data-stu-id="89539-420">For more information, see [Asynchronous Messaging Patterns and High Availability](/azure/service-bus-messaging/service-bus-async-messaging).</span></span>

<span data-ttu-id="89539-421">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="89539-421">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="89539-422">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="89539-422">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="89539-423">Context</span><span class="sxs-lookup"><span data-stu-id="89539-423">Context</span></span> | <span data-ttu-id="89539-424">Latenza massima di esempio</span><span class="sxs-lookup"><span data-stu-id="89539-424">Example maximum latency</span></span> | <span data-ttu-id="89539-425">Criteri di ripetizione</span><span class="sxs-lookup"><span data-stu-id="89539-425">Retry policy</span></span> | <span data-ttu-id="89539-426">Impostazioni</span><span class="sxs-lookup"><span data-stu-id="89539-426">Settings</span></span> | <span data-ttu-id="89539-427">Funzionamento</span><span class="sxs-lookup"><span data-stu-id="89539-427">How it works</span></span> |
|---------|---------|---------|---------|---------|
| <span data-ttu-id="89539-428">Interattivo, interfaccia utente o in primo piano</span><span class="sxs-lookup"><span data-stu-id="89539-428">Interactive, UI, or foreground</span></span> | <span data-ttu-id="89539-429">2 secondi\*</span><span class="sxs-lookup"><span data-stu-id="89539-429">2 seconds\*</span></span>  | <span data-ttu-id="89539-430">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="89539-430">Exponential</span></span> | <span data-ttu-id="89539-431">MinimumBackoff = 0</span><span class="sxs-lookup"><span data-stu-id="89539-431">MinimumBackoff = 0</span></span> <br/> <span data-ttu-id="89539-432">MaximumBackoff = 30 sec</span><span class="sxs-lookup"><span data-stu-id="89539-432">MaximumBackoff = 30 sec.</span></span> <br/> <span data-ttu-id="89539-433">DeltaBackoff = 300 msec</span><span class="sxs-lookup"><span data-stu-id="89539-433">DeltaBackoff = 300 msec.</span></span> <br/> <span data-ttu-id="89539-434">TimeBuffer = 300 msec</span><span class="sxs-lookup"><span data-stu-id="89539-434">TimeBuffer = 300 msec.</span></span> <br/> <span data-ttu-id="89539-435">MaxRetryCount = 2</span><span class="sxs-lookup"><span data-stu-id="89539-435">MaxRetryCount = 2</span></span> | <span data-ttu-id="89539-436">Tentativo 1: Intervallo di 0 sec.</span><span class="sxs-lookup"><span data-stu-id="89539-436">Attempt 1: Delay 0 sec.</span></span> <br/> <span data-ttu-id="89539-437">Tentativo 2: Intervallo di ~300 msec.</span><span class="sxs-lookup"><span data-stu-id="89539-437">Attempt 2: Delay ~300 msec.</span></span> <br/> <span data-ttu-id="89539-438">Tentativo 3: Intervallo di ~900 msec.</span><span class="sxs-lookup"><span data-stu-id="89539-438">Attempt 3: Delay ~900 msec.</span></span> |
| <span data-ttu-id="89539-439">Background o batch</span><span class="sxs-lookup"><span data-stu-id="89539-439">Background or batch</span></span> | <span data-ttu-id="89539-440">30 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-440">30 seconds</span></span> | <span data-ttu-id="89539-441">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="89539-441">Exponential</span></span> | <span data-ttu-id="89539-442">MinimumBackoff = 1</span><span class="sxs-lookup"><span data-stu-id="89539-442">MinimumBackoff = 1</span></span> <br/> <span data-ttu-id="89539-443">MaximumBackoff = 30 sec</span><span class="sxs-lookup"><span data-stu-id="89539-443">MaximumBackoff = 30 sec.</span></span> <br/> <span data-ttu-id="89539-444">DeltaBackoff = 1,75 sec</span><span class="sxs-lookup"><span data-stu-id="89539-444">DeltaBackoff = 1.75 sec.</span></span> <br/> <span data-ttu-id="89539-445">TimeBuffer = 5 sec</span><span class="sxs-lookup"><span data-stu-id="89539-445">TimeBuffer = 5 sec.</span></span> <br/> <span data-ttu-id="89539-446">MaxRetryCount = 3</span><span class="sxs-lookup"><span data-stu-id="89539-446">MaxRetryCount = 3</span></span> | <span data-ttu-id="89539-447">Tentativo 1: Intervallo di ~1 sec.</span><span class="sxs-lookup"><span data-stu-id="89539-447">Attempt 1: Delay ~1 sec.</span></span> <br/> <span data-ttu-id="89539-448">Tentativo 2: Intervallo di ~3 sec.</span><span class="sxs-lookup"><span data-stu-id="89539-448">Attempt 2: Delay ~3 sec.</span></span> <br/> <span data-ttu-id="89539-449">Tentativo 3: Intervallo di ~6 msec.</span><span class="sxs-lookup"><span data-stu-id="89539-449">Attempt 3: Delay ~6 msec.</span></span> <br/> <span data-ttu-id="89539-450">Tentativo 4: Intervallo di ~13 msec.</span><span class="sxs-lookup"><span data-stu-id="89539-450">Attempt 4: Delay ~13 msec.</span></span> |

<span data-ttu-id="89539-451">\* Non è incluso l'ulteriore intervallo aggiunto se viene restituita una risposta di server occupato.</span><span class="sxs-lookup"><span data-stu-id="89539-451">\* Not including additional delay that is added if a Server Busy response is received.</span></span>

### <a name="telemetry"></a><span data-ttu-id="89539-452">Telemetria</span><span class="sxs-lookup"><span data-stu-id="89539-452">Telemetry</span></span>

<span data-ttu-id="89539-453">Il bus di servizio registra i tentativi come eventi ETW tramite un oggetto **EventSource**.</span><span class="sxs-lookup"><span data-stu-id="89539-453">Service Bus logs retries as ETW events using an **EventSource**.</span></span> <span data-ttu-id="89539-454">È necessario associare un **EventListener** all'origine eventi per acquisire gli eventi e visualizzarli nel visualizzatore delle prestazioni o scriverli in un log di destinazione appropriato.</span><span class="sxs-lookup"><span data-stu-id="89539-454">You must attach an **EventListener** to the event source to capture the events and view them in Performance Viewer, or write them to a suitable destination log.</span></span> <span data-ttu-id="89539-455">Gli eventi di ripetizione dei tentativi sono caratterizzati dal formato seguente:</span><span class="sxs-lookup"><span data-stu-id="89539-455">The retry events are of the following form:</span></span>

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

### <a name="examples"></a><span data-ttu-id="89539-456">Esempi</span><span class="sxs-lookup"><span data-stu-id="89539-456">Examples</span></span>

<span data-ttu-id="89539-457">L'esempio di codice seguente illustra come impostare i criteri di ripetizione dei tentativi per:</span><span class="sxs-lookup"><span data-stu-id="89539-457">The following code example shows how to set the retry policy for:</span></span>

- <span data-ttu-id="89539-458">Un gestore dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="89539-458">A namespace manager.</span></span> <span data-ttu-id="89539-459">I criteri si applicano a tutte le operazioni eseguite sul gestore e non possono essere sostituiti a favore di singole operazioni.</span><span class="sxs-lookup"><span data-stu-id="89539-459">The policy applies to all operations on that manager, and cannot be overridden for individual operations.</span></span>
- <span data-ttu-id="89539-460">Una factory di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="89539-460">A messaging factory.</span></span> <span data-ttu-id="89539-461">I criteri si applicano a tutti i client creati dalla factory e non possono essere sostituiti durante la creazione di client individuali.</span><span class="sxs-lookup"><span data-stu-id="89539-461">The policy applies to all clients created from that factory, and cannot be overridden when creating individual clients.</span></span>
- <span data-ttu-id="89539-462">Un singolo client di messaggistica.</span><span class="sxs-lookup"><span data-stu-id="89539-462">An individual messaging client.</span></span> <span data-ttu-id="89539-463">Dopo aver creato un client, è possibile impostarne i criteri di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-463">After a client has been created, you can set the retry policy for that client.</span></span> <span data-ttu-id="89539-464">I criteri di applicano a tutte le operazioni eseguite sul client.</span><span class="sxs-lookup"><span data-stu-id="89539-464">The policy applies to all operations on that client.</span></span>

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

### <a name="more-information"></a><span data-ttu-id="89539-465">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-465">More information</span></span>

- [<span data-ttu-id="89539-466">Modelli di messaggistica asincrona e disponibilità elevata</span><span class="sxs-lookup"><span data-stu-id="89539-466">Asynchronous messaging patterns and high availability</span></span>](/azure/service-bus-messaging/service-bus-async-messaging)

## <a name="service-fabric"></a><span data-ttu-id="89539-467">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="89539-467">Service Fabric</span></span>

<span data-ttu-id="89539-468">La distribuzione di servizi affidabili in un cluster di Service Fabric protegge dalla maggior parte degli errori temporanei potenziali descritti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="89539-468">Distributing reliable services in a Service Fabric cluster guards against most of the potential transient faults discussed in this article.</span></span> <span data-ttu-id="89539-469">Tuttavia, alcuni errori temporanei sono comunque possibili.</span><span class="sxs-lookup"><span data-stu-id="89539-469">Some transient faults are still possible, however.</span></span> <span data-ttu-id="89539-470">Ad esempio, il servizio di denominazione potrebbe essere all'interno di una modifica di routing quando rileva una richiesta, provocando la generazione di un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="89539-470">For example, the naming service might be in the middle of a routing change when it gets a request, causing it to throw an exception.</span></span> <span data-ttu-id="89539-471">Se la stessa richiesta arriva 100 millisecondi dopo, verrà probabilmente completata.</span><span class="sxs-lookup"><span data-stu-id="89539-471">If the same request comes 100 milliseconds later, it will probably succeed.</span></span>

<span data-ttu-id="89539-472">Service Fabric gestisce internamente questo tipo di errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="89539-472">Internally, Service Fabric manages this kind of transient fault.</span></span> <span data-ttu-id="89539-473">È possibile configurare alcune impostazioni tramite la classe `OperationRetrySettings` durante la configurazione dei servizi.</span><span class="sxs-lookup"><span data-stu-id="89539-473">You can configure some settings by using the `OperationRetrySettings` class while setting up your services.</span></span> <span data-ttu-id="89539-474">Il codice seguente mostra un esempio.</span><span class="sxs-lookup"><span data-stu-id="89539-474">The following code shows an example.</span></span> <span data-ttu-id="89539-475">Nella maggior parte dei casi ciò non dovrebbe essere necessario e le impostazioni predefinite sono adeguate.</span><span class="sxs-lookup"><span data-stu-id="89539-475">In most cases, this should not be necessary, and the default settings will be fine.</span></span>

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

### <a name="more-information"></a><span data-ttu-id="89539-476">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-476">More information</span></span>

- [<span data-ttu-id="89539-477">Gestione delle eccezioni da remoto</span><span class="sxs-lookup"><span data-stu-id="89539-477">Remote exception handling</span></span>](/azure/service-fabric/service-fabric-reliable-services-communication-remoting#remoting-exception-handling)

## <a name="sql-database-using-adonet"></a><span data-ttu-id="89539-478">Database SQL con ADO.NET</span><span class="sxs-lookup"><span data-stu-id="89539-478">SQL Database using ADO.NET</span></span>

<span data-ttu-id="89539-479">Il database SQL, in questo caso, è un database SQL ospitato, di varie dimensioni, disponibile come servizio standard (condiviso) o premium (non condiviso).</span><span class="sxs-lookup"><span data-stu-id="89539-479">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-480">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-480">Retry mechanism</span></span>

<span data-ttu-id="89539-481">Il database SQL non offre il supporto incorporato per la ripetizione dei tentativi quando si accede ad esso con ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="89539-481">SQL Database has no built-in support for retries when accessed using ADO.NET.</span></span> <span data-ttu-id="89539-482">Per determinare il motivo per cui una richiesta ha avuto esito negativo, è possibile usare i codici restituiti dalle richieste.</span><span class="sxs-lookup"><span data-stu-id="89539-482">However, the return codes from requests can be used to determine why a request failed.</span></span> <span data-ttu-id="89539-483">Per altre informazioni sulla limitazione delle richieste del database SQL, vedere [Limiti delle risorse del database SQL di Azure](/azure/sql-database/sql-database-resource-limits).</span><span class="sxs-lookup"><span data-stu-id="89539-483">For more information about SQL Database throttling, see [Azure SQL Database resource limits](/azure/sql-database/sql-database-resource-limits).</span></span> <span data-ttu-id="89539-484">Per un elenco dei codici di errore pertinenti, vedere [Codici di errore SQL per le applicazioni client del database SQL](/azure/sql-database/sql-database-develop-error-messages).</span><span class="sxs-lookup"><span data-stu-id="89539-484">For a list of relevant error codes, see [SQL error codes for SQL Database client applications](/azure/sql-database/sql-database-develop-error-messages).</span></span>

<span data-ttu-id="89539-485">È possibile usare la libreria Polly per implementare la ripetizione di tentativi per il database SQL.</span><span class="sxs-lookup"><span data-stu-id="89539-485">You can use the Polly library to implement retries for SQL Database.</span></span> <span data-ttu-id="89539-486">Vedere [Gestione degli errori temporanei con Polly](#transient-fault-handling-with-polly).</span><span class="sxs-lookup"><span data-stu-id="89539-486">See [Transient fault handling with Polly](#transient-fault-handling-with-polly).</span></span>

### <a name="retry-usage-guidance"></a><span data-ttu-id="89539-487">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-487">Retry usage guidance</span></span>

<span data-ttu-id="89539-488">Quando si accede al database SQL con ADO.NET, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="89539-488">Consider the following guidelines when accessing SQL Database using ADO.NET:</span></span>

- <span data-ttu-id="89539-489">Scegliere il tipo di servizio appropriato (condiviso o premium).</span><span class="sxs-lookup"><span data-stu-id="89539-489">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="89539-490">Un'istanza condivisa può subire limitazioni o ritardi di connessione maggiori rispetto alla media perché può essere usata da altri tenant del server condiviso.</span><span class="sxs-lookup"><span data-stu-id="89539-490">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="89539-491">Se sono necessarie prestazioni più prevedibili o affidabili operazioni di bassa latenza, valutare la possibilità di scegliere il tipo servizio premium.</span><span class="sxs-lookup"><span data-stu-id="89539-491">If more predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>
- <span data-ttu-id="89539-492">Assicurarsi di eseguire i nuovi tentativi al livello o nell'ambito appropriato per evitare operazioni non idempotenti che possono causare incoerenze nei dati.</span><span class="sxs-lookup"><span data-stu-id="89539-492">Ensure that you perform retries at the appropriate level or scope to avoid non-idempotent operations causing inconsistency in the data.</span></span> <span data-ttu-id="89539-493">In teoria, tutte le operazioni dovrebbero essere idempotenti in modo da poter essere ripetute senza causare incoerenze.</span><span class="sxs-lookup"><span data-stu-id="89539-493">Ideally, all operations should be idempotent so that they can be repeated without causing inconsistency.</span></span> <span data-ttu-id="89539-494">Se questo non avviene, i nuovi tentativi devono essere eseguiti a un livello o in un ambito che consenta l'annullamento di tutte le modifiche correlate in caso di esito negativo di un'operazione, ad esempio un ambito transazionale.</span><span class="sxs-lookup"><span data-stu-id="89539-494">Where this is not the case, the retry should be performed at a level or scope that allows all related changes to be undone if one operation fails; for example, from within a transactional scope.</span></span> <span data-ttu-id="89539-495">Per altre informazioni, vedere l'articolo sul [livello di accesso ai dati di Cloud Service Fundamentals e la gestione degli errori temporanei](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).</span><span class="sxs-lookup"><span data-stu-id="89539-495">For more information, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).</span></span>
- <span data-ttu-id="89539-496">Non è consigliabile una strategia basata su intervalli fissi per il database SQL di Azure, se non negli scenari interattivi in cui vengono eseguiti solo alcuni tentativi a intervalli molto brevi.</span><span class="sxs-lookup"><span data-stu-id="89539-496">A fixed interval strategy is not recommended for use with Azure SQL Database except for interactive scenarios where there are only a few retries at very short intervals.</span></span> <span data-ttu-id="89539-497">In alternativa, valutare la possibilità di usare una strategia di backoff esponenziale per la maggior parte degli scenari.</span><span class="sxs-lookup"><span data-stu-id="89539-497">Instead, consider using an exponential back-off strategy for the majority of scenarios.</span></span>
- <span data-ttu-id="89539-498">Quando si definiscono le connessioni, scegliere un valore appropriato per i timeout di connessione e dei comandi.</span><span class="sxs-lookup"><span data-stu-id="89539-498">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="89539-499">Un timeout troppo breve può comportare errori di connessione prematuri quando il database è occupato,</span><span class="sxs-lookup"><span data-stu-id="89539-499">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="89539-500">mentre un timeout troppo lungo può impedire che la logica di ripetizione dei tentativi funzioni correttamente, poiché l'intervallo di attesa prima che venga rilevata una connessione non riuscita è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="89539-500">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="89539-501">Il valore di timeout è un componente della latenza end-to-end; per ogni nuovo tentativo, viene aggiunto all'intervallo tra i tentativi specificato nei criteri di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-501">The value of the timeout is a component of the end-to-end latency; it is effectively added to the retry delay specified in the retry policy for every retry attempt.</span></span>
- <span data-ttu-id="89539-502">Chiudere la connessione dopo un certo numero di nuovi tentativi, anche se si usa una logica di ripetizione dei tentativi con backoff esponenziale, e ripetere l'operazione su una nuova connessione.</span><span class="sxs-lookup"><span data-stu-id="89539-502">Close the connection after a certain number of retries, even when using an exponential back off retry logic, and retry the operation on a new connection.</span></span> <span data-ttu-id="89539-503">Se si ripete più volte la stessa operazione su un'unica connessione, infatti, è possibile che si verifichino problemi di connessione.</span><span class="sxs-lookup"><span data-stu-id="89539-503">Retrying the same operation multiple times on the same connection can be a factor that contributes to connection problems.</span></span> <span data-ttu-id="89539-504">Per altre informazioni su questa tecnica, vedere l'articolo sul [livello di accesso ai dati di Cloud Service Fundamentals e la gestione degli errori temporanei](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).</span><span class="sxs-lookup"><span data-stu-id="89539-504">For an example of this technique, see [Cloud Service Fundamentals Data Access Layer – Transient Fault Handling](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).</span></span>
- <span data-ttu-id="89539-505">Se si usa il pool di connessioni (impostazione predefinita), è probabile che venga scelta la stessa connessione, anche dopo aver chiuso e riaperto una connessione.</span><span class="sxs-lookup"><span data-stu-id="89539-505">When connection pooling is in use (the default) there is a chance that the same connection will be chosen from the pool, even after closing and reopening a connection.</span></span> <span data-ttu-id="89539-506">Se si verifica questo problema, è possibile risolverlo chiamando il metodo **ClearPool** della classe **SqlConnection** per contrassegnare la connessione come non riutilizzabile.</span><span class="sxs-lookup"><span data-stu-id="89539-506">If this is the case, a technique to resolve it is to call the **ClearPool** method of the **SqlConnection** class to mark the connection as not reusable.</span></span> <span data-ttu-id="89539-507">Questo metodo, tuttavia, deve essere adottato solo dopo vari tentativi di connessione non riusciti e solo quando è presente la classe di errori temporanei, come i timeout SQL (codice di errore -2), specifica delle connessioni difettose.</span><span class="sxs-lookup"><span data-stu-id="89539-507">However, you should do this only after several connection attempts have failed, and only when encountering the specific class of transient failures such as SQL timeouts (error code -2) related to faulty connections.</span></span>
- <span data-ttu-id="89539-508">Se il codice di accesso ai dati usa transazioni avviate come istanze **TransactionScope** , la logica di ripetizione dei tentativi deve riaprire la connessione e avviare un nuovo ambito della transazione.</span><span class="sxs-lookup"><span data-stu-id="89539-508">If the data access code uses transactions initiated as **TransactionScope** instances, the retry logic should reopen the connection and initiate a new transaction scope.</span></span> <span data-ttu-id="89539-509">Per questo motivo, il blocco di codice non irreversibile deve interessare l'intero ambito della transazione.</span><span class="sxs-lookup"><span data-stu-id="89539-509">For this reason, the retryable code block should encompass the entire scope of the transaction.</span></span>

<span data-ttu-id="89539-510">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="89539-510">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="89539-511">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="89539-511">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="89539-512">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="89539-512">**Context**</span></span> | <span data-ttu-id="89539-513">**Destinazione di esempio E2E<br />latenza massima**</span><span class="sxs-lookup"><span data-stu-id="89539-513">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="89539-514">**Strategia di ripetizione dei tentativi**</span><span class="sxs-lookup"><span data-stu-id="89539-514">**Retry strategy**</span></span> | <span data-ttu-id="89539-515">**Impostazioni**</span><span class="sxs-lookup"><span data-stu-id="89539-515">**Settings**</span></span> | <span data-ttu-id="89539-516">**Valori**</span><span class="sxs-lookup"><span data-stu-id="89539-516">**Values**</span></span> | <span data-ttu-id="89539-517">**Funzionamento**</span><span class="sxs-lookup"><span data-stu-id="89539-517">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="89539-518">Interattivo, interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="89539-518">Interactive, UI,</span></span><br /><span data-ttu-id="89539-519">o in primo piano</span><span class="sxs-lookup"><span data-stu-id="89539-519">or foreground</span></span> |<span data-ttu-id="89539-520">2 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-520">2 sec</span></span> |<span data-ttu-id="89539-521">FixedInterval</span><span class="sxs-lookup"><span data-stu-id="89539-521">FixedInterval</span></span> |<span data-ttu-id="89539-522">Numero tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-522">Retry count</span></span><br /><span data-ttu-id="89539-523">Intervallo tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-523">Retry interval</span></span><br /><span data-ttu-id="89539-524">Primo tentativo rapido</span><span class="sxs-lookup"><span data-stu-id="89539-524">First fast retry</span></span> |<span data-ttu-id="89539-525">3</span><span class="sxs-lookup"><span data-stu-id="89539-525">3</span></span><br /><span data-ttu-id="89539-526">500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-526">500 ms</span></span><br /><span data-ttu-id="89539-527">true</span><span class="sxs-lookup"><span data-stu-id="89539-527">true</span></span> |<span data-ttu-id="89539-528">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="89539-528">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="89539-529">Tentativo 2 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-529">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="89539-530">Tentativo 3 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-530">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="89539-531">Background</span><span class="sxs-lookup"><span data-stu-id="89539-531">Background</span></span><br /><span data-ttu-id="89539-532">o batch</span><span class="sxs-lookup"><span data-stu-id="89539-532">or batch</span></span> |<span data-ttu-id="89539-533">30 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-533">30 sec</span></span> |<span data-ttu-id="89539-534">ExponentialBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-534">ExponentialBackoff</span></span> |<span data-ttu-id="89539-535">Numero tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-535">Retry count</span></span><br /><span data-ttu-id="89539-536">Interruzione temporanea minima</span><span class="sxs-lookup"><span data-stu-id="89539-536">Min back-off</span></span><br /><span data-ttu-id="89539-537">Interruzione temporanea massima</span><span class="sxs-lookup"><span data-stu-id="89539-537">Max back-off</span></span><br /><span data-ttu-id="89539-538">Interruzione temporanea delta</span><span class="sxs-lookup"><span data-stu-id="89539-538">Delta back-off</span></span><br /><span data-ttu-id="89539-539">Primo tentativo rapido</span><span class="sxs-lookup"><span data-stu-id="89539-539">First fast retry</span></span> |<span data-ttu-id="89539-540">5</span><span class="sxs-lookup"><span data-stu-id="89539-540">5</span></span><br /><span data-ttu-id="89539-541">0 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-541">0 sec</span></span><br /><span data-ttu-id="89539-542">60 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-542">60 sec</span></span><br /><span data-ttu-id="89539-543">2 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-543">2 sec</span></span><br /><span data-ttu-id="89539-544">false</span><span class="sxs-lookup"><span data-stu-id="89539-544">false</span></span> |<span data-ttu-id="89539-545">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="89539-545">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="89539-546">Tentativo 2 - intervallo di ~2 sec</span><span class="sxs-lookup"><span data-stu-id="89539-546">Attempt 2 - delay ~2 sec</span></span><br /><span data-ttu-id="89539-547">Tentativo 3 - intervallo di ~6 sec</span><span class="sxs-lookup"><span data-stu-id="89539-547">Attempt 3 - delay ~6 sec</span></span><br /><span data-ttu-id="89539-548">Tentativo 4 - intervallo di ~14 sec</span><span class="sxs-lookup"><span data-stu-id="89539-548">Attempt 4 - delay ~14 sec</span></span><br /><span data-ttu-id="89539-549">Tentativo 5 - intervallo di 30 sec</span><span class="sxs-lookup"><span data-stu-id="89539-549">Attempt 5 - delay ~30 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="89539-550">Gli obiettivi di latenza end-to-end presuppongono il timeout predefinito per le connessioni al servizio.</span><span class="sxs-lookup"><span data-stu-id="89539-550">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="89539-551">Se si specifica un timeout di connessione più lungo, la latenza end-to-end verrà estesa di questo intervallo di tempo aggiuntivo per ogni nuovo tentativo.</span><span class="sxs-lookup"><span data-stu-id="89539-551">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>

### <a name="examples"></a><span data-ttu-id="89539-552">Esempi</span><span class="sxs-lookup"><span data-stu-id="89539-552">Examples</span></span>

<span data-ttu-id="89539-553">Questa sezione illustra come usare Polly per accedere al database SQL di Azure usando un set di criteri di ripetizione dei tentativi configurato nella classe `Policy`.</span><span class="sxs-lookup"><span data-stu-id="89539-553">This section shows how you can use Polly to access Azure SQL Database using a set of retry policies configured in the `Policy` class.</span></span>

<span data-ttu-id="89539-554">Il codice seguente illustra un metodo di estensione nella classe `SqlCommand` che richiama `ExecuteAsync` con backoff esponenziale.</span><span class="sxs-lookup"><span data-stu-id="89539-554">The following code shows an extension method on the `SqlCommand` class that calls `ExecuteAsync` with exponential backoff.</span></span>

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

<span data-ttu-id="89539-555">Questo metodo di estensione asincrona può essere usato come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="89539-555">This asynchronous extension method can be used as follows.</span></span>

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync())
{
    // Do something with the values
}
```

### <a name="more-information"></a><span data-ttu-id="89539-556">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-556">More information</span></span>

- [<span data-ttu-id="89539-557">Livello di accesso ai dati di Cloud Service Fundamentals e gestione degli errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="89539-557">Cloud Service Fundamentals Data Access Layer – Transient Fault Handling</span></span>](https://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

<span data-ttu-id="89539-558">Per indicazioni generali su come ottenere il massimo dal database SQL, vedere la [guida all'elasticità e alle prestazioni del database SQL di Azure](https://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx).</span><span class="sxs-lookup"><span data-stu-id="89539-558">For general guidance on getting the most from SQL Database, see [Azure SQL Database performance and elasticity guide](https://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx).</span></span>

## <a name="sql-database-using-entity-framework-6"></a><span data-ttu-id="89539-559">Database SQL con Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="89539-559">SQL Database using Entity Framework 6</span></span>

<span data-ttu-id="89539-560">Il database SQL, in questo caso, è un database SQL ospitato, di varie dimensioni, disponibile come servizio standard (condiviso) o premium (non condiviso).</span><span class="sxs-lookup"><span data-stu-id="89539-560">SQL Database is a hosted SQL database available in a range of sizes and as both a standard (shared) and premium (non-shared) service.</span></span> <span data-ttu-id="89539-561">Entity Framework, invece, è un mapper relazionale a oggetti che consente agli sviluppatori .NET di usare dati relazionali mediante oggetti specifici di dominio.</span><span class="sxs-lookup"><span data-stu-id="89539-561">Entity Framework is an object-relational mapper that enables .NET developers to work with relational data using domain-specific objects.</span></span> <span data-ttu-id="89539-562">Elimina la necessità di gran parte del codice di accesso ai dati che in genere gli sviluppatori devono scrivere.</span><span class="sxs-lookup"><span data-stu-id="89539-562">It eliminates the need for most of the data-access code that developers usually need to write.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-563">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-563">Retry mechanism</span></span>

<span data-ttu-id="89539-564">Quando si accede al database SQL con Entity Framework 6.0 o versione superiore, il supporto per la ripetizione di tentativi viene fornito attraverso un meccanismo denominato [resilienza delle connessioni / logica di ripetizione dei tentativi](/ef/ef6/fundamentals/connection-resiliency/retry-logic).</span><span class="sxs-lookup"><span data-stu-id="89539-564">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher through a mechanism called [Connection resiliency / retry logic](/ef/ef6/fundamentals/connection-resiliency/retry-logic).</span></span> <span data-ttu-id="89539-565">Di seguiti sono illustrate le caratteristiche principali del meccanismo di ripetizione dei tentativi:</span><span class="sxs-lookup"><span data-stu-id="89539-565">The main features of the retry mechanism are:</span></span>

- <span data-ttu-id="89539-566">L'astrazione primaria è l'interfaccia **IDbExecutionStrategy** .</span><span class="sxs-lookup"><span data-stu-id="89539-566">The primary abstraction is the **IDbExecutionStrategy** interface.</span></span> <span data-ttu-id="89539-567">Questa interfaccia:</span><span class="sxs-lookup"><span data-stu-id="89539-567">This interface:</span></span>
  - <span data-ttu-id="89539-568">Definisce i metodi **Execute**\* sincroni e asincroni.</span><span class="sxs-lookup"><span data-stu-id="89539-568">Defines synchronous and asynchronous **Execute**\* methods.</span></span>
  - <span data-ttu-id="89539-569">Definisce le classi che possono essere usate direttamente o configurate in un contesto di database come strategia predefinita, quindi mappate a un nome di provider o a un nome di provider e un nome di server.</span><span class="sxs-lookup"><span data-stu-id="89539-569">Defines classes that can be used directly or can be configured on a database context as a default strategy, mapped to provider name, or mapped to a provider name and server name.</span></span> <span data-ttu-id="89539-570">Se configurate in un contesto, i nuovi tentativi vengono eseguiti a livello di singole operazioni di database, che possono essere molteplici per una determinata operazione di contesto.</span><span class="sxs-lookup"><span data-stu-id="89539-570">When configured on a context, retries occur at the level of individual database operations, of which there might be several for a given context operation.</span></span>
  - <span data-ttu-id="89539-571">Definisce quando tentare di eseguire nuovamente una connessione non riuscita e in che modo.</span><span class="sxs-lookup"><span data-stu-id="89539-571">Defines when to retry a failed connection, and how.</span></span>
- <span data-ttu-id="89539-572">Include diverse implementazioni incorporate dell'interfaccia **IDbExecutionStrategy** :</span><span class="sxs-lookup"><span data-stu-id="89539-572">It includes several built-in implementations of the **IDbExecutionStrategy** interface:</span></span>
  - <span data-ttu-id="89539-573">Predefinita - Nessun nuovo tentativo.</span><span class="sxs-lookup"><span data-stu-id="89539-573">Default - no retrying.</span></span>
  - <span data-ttu-id="89539-574">Predefinita per il database SQL (automatica) - Nessun nuovo tentativo, ma controlla le eccezioni e ne esegue il wrapping con il suggerimento di usare la strategia relativa al database SQL.</span><span class="sxs-lookup"><span data-stu-id="89539-574">Default for SQL Database (automatic) - no retrying, but inspects exceptions and wraps them with suggestion to use the SQL Database strategy.</span></span>
  - <span data-ttu-id="89539-575">Predefinita per il database SQL - Esponenziale (ereditata dalla classe di base), combinata con la logica di rilevamento del database SQL.</span><span class="sxs-lookup"><span data-stu-id="89539-575">Default for SQL Database - exponential (inherited from base class) plus SQL Database detection logic.</span></span>
- <span data-ttu-id="89539-576">Implementa una strategia di backoff esponenziale che include la sequenza casuale.</span><span class="sxs-lookup"><span data-stu-id="89539-576">It implements an exponential back-off strategy that includes randomization.</span></span>
- <span data-ttu-id="89539-577">Le classi di ripetizione dei tentativi incorporate sono classi con stato di tipo non thread-safe.</span><span class="sxs-lookup"><span data-stu-id="89539-577">The built-in retry classes are stateful and are not thread safe.</span></span> <span data-ttu-id="89539-578">Possono comunque essere riusate dopo il completamento dell'operazione corrente.</span><span class="sxs-lookup"><span data-stu-id="89539-578">However, they can be reused after the current operation is completed.</span></span>
- <span data-ttu-id="89539-579">Se viene superato il numero di tentativi specificato, i risultati vengono inclusi in una nuova eccezione</span><span class="sxs-lookup"><span data-stu-id="89539-579">If the specified retry count is exceeded, the results are wrapped in a new exception.</span></span> <span data-ttu-id="89539-580">e l'eccezione corrente non viene visualizzata.</span><span class="sxs-lookup"><span data-stu-id="89539-580">It does not bubble up the current exception.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="89539-581">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="89539-581">Policy configuration</span></span>

<span data-ttu-id="89539-582">Quando si accede al database SQL con Entity Framework 6.0, o versioni successive, è già disponibile il supporto per la ripetizione di tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-582">Retry support is provided when accessing SQL Database using Entity Framework 6.0 and higher.</span></span> <span data-ttu-id="89539-583">I criteri di ripetizione dei tentativi sono configurati a livello di codice.</span><span class="sxs-lookup"><span data-stu-id="89539-583">Retry policies are configured programmatically.</span></span> <span data-ttu-id="89539-584">La configurazione non può essere modificata a livello di singola operazione.</span><span class="sxs-lookup"><span data-stu-id="89539-584">The configuration cannot be changed on a per-operation basis.</span></span>

<span data-ttu-id="89539-585">Quando si configura una strategia di contesto come impostazione predefinita, è necessario specificare anche una funzione che consente di creare una nuova strategia su richiesta.</span><span class="sxs-lookup"><span data-stu-id="89539-585">When configuring a strategy on the context as the default, you specify a function that creates a new strategy on demand.</span></span> <span data-ttu-id="89539-586">Il codice seguente mostra come creare una classe di configurazione di nuovi tentativi che estende la classe di base **DbConfiguration** .</span><span class="sxs-lookup"><span data-stu-id="89539-586">The following code shows how you can create a retry configuration class that extends the **DbConfiguration** base class.</span></span>

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

<span data-ttu-id="89539-587">È quindi possibile impostarla come strategia di ripetizione dei tentativi predefinita per tutte le operazioni usando il metodo **SetConfiguration** dell'istanza **DbConfiguration** all'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="89539-587">You can then specify this as the default retry strategy for all operations using the **SetConfiguration** method of the **DbConfiguration** instance when the application starts.</span></span> <span data-ttu-id="89539-588">Per impostazione predefinita, Entity Framework rileverà e userà automaticamente questa classe di configurazione.</span><span class="sxs-lookup"><span data-stu-id="89539-588">By default, EF will automatically discover and use the configuration class.</span></span>

```csharp
DbConfiguration.SetConfiguration(new BloggingContextConfiguration());
```

<span data-ttu-id="89539-589">È possibile specificare la classe di configurazione di nuovi tentativi per un contesto specifico annotando la classe del contesto con un attributo **DbConfigurationType** .</span><span class="sxs-lookup"><span data-stu-id="89539-589">You can specify the retry configuration class for a context by annotating the context class with a **DbConfigurationType** attribute.</span></span> <span data-ttu-id="89539-590">Tuttavia, se è presente una sola classe di configurazione, Entity Framework la userà anche senza dover annotare il contesto.</span><span class="sxs-lookup"><span data-stu-id="89539-590">However, if you have only one configuration class, EF will use it without the need to annotate the context.</span></span>

```csharp
[DbConfigurationType(typeof(BloggingContextConfiguration))]
public class BloggingContext : DbContext
```

<span data-ttu-id="89539-591">Se per determinate operazioni è necessario usare strategie di ripetizione dei tentativi diverse o si disabilita la ripetizione dei tentativi, è possibile creare una classe di configurazione che consente di sospendere o cambiare strategie impostando un flag in **CallContext**.</span><span class="sxs-lookup"><span data-stu-id="89539-591">If you need to use different retry strategies for specific operations, or disable retries for specific operations, you can create a configuration class that allows you to suspend or swap strategies by setting a flag in the **CallContext**.</span></span> <span data-ttu-id="89539-592">Questo flag può essere usato dalla classe di configurazione per cambiare strategie o per disabilitare la strategia specificata e usare invece una strategia predefinita.</span><span class="sxs-lookup"><span data-stu-id="89539-592">The configuration class can use this flag to switch strategies, or disable the strategy you provide and use a default strategy.</span></span> <span data-ttu-id="89539-593">Per altre informazioni, vedere l'articolo sulla [strategia di esecuzione della sospensione](/ef/ef6/fundamentals/connection-resiliency/retry-logic#workaround-suspend-execution-strategy) (Entity Framework 6 e versioni successive).</span><span class="sxs-lookup"><span data-stu-id="89539-593">For more information, see [Suspend Execution Strategy](/ef/ef6/fundamentals/connection-resiliency/retry-logic#workaround-suspend-execution-strategy) (EF6 onwards).</span></span>

<span data-ttu-id="89539-594">Un'altra tecnica per usare strategie di ripetizione specifiche per singole operazioni consiste nel creare un'istanza della classe di strategia necessaria, fornire le impostazioni desiderate attraverso dei parametri</span><span class="sxs-lookup"><span data-stu-id="89539-594">Another technique for using specific retry strategies for individual operations is to create an instance of the required strategy class and supply the desired settings through parameters.</span></span> <span data-ttu-id="89539-595">e quindi richiamare il metodo **ExecuteAsync**.</span><span class="sxs-lookup"><span data-stu-id="89539-595">You then invoke its **ExecuteAsync** method.</span></span>

```csharp
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
```

<span data-ttu-id="89539-596">Il modo più semplice per usare una classe **DbConfiguration** consiste nell'individuarla nello stesso assembly della classe **DbContext**.</span><span class="sxs-lookup"><span data-stu-id="89539-596">The simplest way to use a **DbConfiguration** class is to locate it in the same assembly as the **DbContext** class.</span></span> <span data-ttu-id="89539-597">Questo metodo, tuttavia, non è appropriato quando lo stesso contesto è richiesto in scenari diversi, ad esempio in varie strategie di ripetizione dei tentativi, interattiva o in background.</span><span class="sxs-lookup"><span data-stu-id="89539-597">However, this is not appropriate when the same context is required in different scenarios, such as different interactive and background retry strategies.</span></span> <span data-ttu-id="89539-598">Se i diversi contesti vengono eseguiti in AppDomain distinti, è possibile usare il supporto incorporato per specificare le classi di configurazione nel file di configurazione o impostarle in modo esplicito tramite codice.</span><span class="sxs-lookup"><span data-stu-id="89539-598">If the different contexts execute in separate AppDomains, you can use the built-in support for specifying configuration classes in the configuration file or set it explicitly using code.</span></span> <span data-ttu-id="89539-599">Se invece i diversi contesti devono essere eseguiti nello stesso AppDomain, sarà necessaria una soluzione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="89539-599">If the different contexts must execute in the same AppDomain, a custom solution will be required.</span></span>

<span data-ttu-id="89539-600">Per altre informazioni, vedere l'articolo sulla [configurazione basata su codice](/ef/ef6/fundamentals/configuring/code-based) (Entity Framework 6 o versione successiva).</span><span class="sxs-lookup"><span data-stu-id="89539-600">For more information, see [Code-Based Configuration](/ef/ef6/fundamentals/configuring/code-based) (EF6 onwards).</span></span>

<span data-ttu-id="89539-601">La tabella seguente mostra le impostazioni predefinite per i criteri di ripetizione dei tentativi incorporati quando si usa Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="89539-601">The following table shows the default settings for the built-in retry policy when using EF6.</span></span>

| <span data-ttu-id="89539-602">Impostazione</span><span class="sxs-lookup"><span data-stu-id="89539-602">Setting</span></span> | <span data-ttu-id="89539-603">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="89539-603">Default value</span></span> | <span data-ttu-id="89539-604">Significato</span><span class="sxs-lookup"><span data-stu-id="89539-604">Meaning</span></span> |
|---------|---------------|---------|
| <span data-ttu-id="89539-605">Policy</span><span class="sxs-lookup"><span data-stu-id="89539-605">Policy</span></span> | <span data-ttu-id="89539-606">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="89539-606">Exponential</span></span> | <span data-ttu-id="89539-607">Backoff esponenziale.</span><span class="sxs-lookup"><span data-stu-id="89539-607">Exponential back-off.</span></span> |
| <span data-ttu-id="89539-608">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="89539-608">MaxRetryCount</span></span> | <span data-ttu-id="89539-609">5</span><span class="sxs-lookup"><span data-stu-id="89539-609">5</span></span> | <span data-ttu-id="89539-610">Numero massimo di tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-610">The maximum number of retries.</span></span> |
| <span data-ttu-id="89539-611">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="89539-611">MaxDelay</span></span> | <span data-ttu-id="89539-612">30 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-612">30 seconds</span></span> | <span data-ttu-id="89539-613">Ritardo massimo tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-613">The maximum delay between retries.</span></span> <span data-ttu-id="89539-614">Questa valore non influisce sulla modalità di calcolo della serie di ritardi.</span><span class="sxs-lookup"><span data-stu-id="89539-614">This value does not affect how the series of delays are computed.</span></span> <span data-ttu-id="89539-615">Definisce solo un limite superiore.</span><span class="sxs-lookup"><span data-stu-id="89539-615">It only defines an upper bound.</span></span> |
| <span data-ttu-id="89539-616">DefaultCoefficient</span><span class="sxs-lookup"><span data-stu-id="89539-616">DefaultCoefficient</span></span> | <span data-ttu-id="89539-617">1 secondo</span><span class="sxs-lookup"><span data-stu-id="89539-617">1 second</span></span> | <span data-ttu-id="89539-618">Coefficiente per il calcolo del backoff esponenziale.</span><span class="sxs-lookup"><span data-stu-id="89539-618">The coefficient for the exponential back-off computation.</span></span> <span data-ttu-id="89539-619">Questo valore non può essere modificato.</span><span class="sxs-lookup"><span data-stu-id="89539-619">This value cannot be changed.</span></span> |
| <span data-ttu-id="89539-620">DefaultRandomFactor</span><span class="sxs-lookup"><span data-stu-id="89539-620">DefaultRandomFactor</span></span> | <span data-ttu-id="89539-621">1.1</span><span class="sxs-lookup"><span data-stu-id="89539-621">1.1</span></span> | <span data-ttu-id="89539-622">Moltiplicatore usato per aggiungere un ritardo casuale per ogni voce.</span><span class="sxs-lookup"><span data-stu-id="89539-622">The multiplier used to add a random delay for each entry.</span></span> <span data-ttu-id="89539-623">Questo valore non può essere modificato.</span><span class="sxs-lookup"><span data-stu-id="89539-623">This value cannot be changed.</span></span> |
| <span data-ttu-id="89539-624">DefaultExponentialBase</span><span class="sxs-lookup"><span data-stu-id="89539-624">DefaultExponentialBase</span></span> | <span data-ttu-id="89539-625">2</span><span class="sxs-lookup"><span data-stu-id="89539-625">2</span></span> | <span data-ttu-id="89539-626">Moltiplicatore usato per calcolare il ritardo successivo.</span><span class="sxs-lookup"><span data-stu-id="89539-626">The multiplier used to calculate the next delay.</span></span> <span data-ttu-id="89539-627">Questo valore non può essere modificato.</span><span class="sxs-lookup"><span data-stu-id="89539-627">This value cannot be changed.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="89539-628">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-628">Retry usage guidance</span></span>

<span data-ttu-id="89539-629">Quando si accede al database SQL con Entity Framework 6, tenere presente le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="89539-629">Consider the following guidelines when accessing SQL Database using EF6:</span></span>

- <span data-ttu-id="89539-630">Scegliere il tipo di servizio appropriato (condiviso o premium).</span><span class="sxs-lookup"><span data-stu-id="89539-630">Choose the appropriate service option (shared or premium).</span></span> <span data-ttu-id="89539-631">Un'istanza condivisa può subire limitazioni o ritardi di connessione maggiori rispetto alla media perché può essere usata da altri tenant del server condiviso.</span><span class="sxs-lookup"><span data-stu-id="89539-631">A shared instance may suffer longer than usual connection delays and throttling due to the usage by other tenants of the shared server.</span></span> <span data-ttu-id="89539-632">Se sono necessarie prestazioni prevedibili o affidabili operazioni di bassa latenza, valutare la possibilità di scegliere il tipo servizio premium.</span><span class="sxs-lookup"><span data-stu-id="89539-632">If predictable performance and reliable low latency operations are required, consider choosing the premium option.</span></span>

- <span data-ttu-id="89539-633">Per il database SQL di Azure non è consigliabile usare una strategia a intervalli fissi,</span><span class="sxs-lookup"><span data-stu-id="89539-633">A fixed interval strategy is not recommended for use with Azure SQL Database.</span></span> <span data-ttu-id="89539-634">bensì una strategia di backoff esponenziale, poiché il servizio può essere sovraccarico e intervalli più lunghi concedono più tempo per il ripristino del servizio.</span><span class="sxs-lookup"><span data-stu-id="89539-634">Instead, use an exponential back-off strategy because the service may be overloaded, and longer delays allow more time for it to recover.</span></span>

- <span data-ttu-id="89539-635">Quando si definiscono le connessioni, scegliere un valore appropriato per i timeout di connessione e dei comandi.</span><span class="sxs-lookup"><span data-stu-id="89539-635">Choose a suitable value for the connection and command timeouts when defining connections.</span></span> <span data-ttu-id="89539-636">I timeout devono essere definiti in base alla progettazione della logica di business e tramite una serie di test.</span><span class="sxs-lookup"><span data-stu-id="89539-636">Base the timeout on both your business logic design and through testing.</span></span> <span data-ttu-id="89539-637">Potrebbe essere necessario modificare questi valori nel tempo man mano che i volumi dei dati o dei processi di business cambiano.</span><span class="sxs-lookup"><span data-stu-id="89539-637">You may need to modify this value over time as the volumes of data or the business processes change.</span></span> <span data-ttu-id="89539-638">Un timeout troppo breve può comportare errori di connessione prematuri quando il database è occupato,</span><span class="sxs-lookup"><span data-stu-id="89539-638">Too short a timeout may result in premature failures of connections when the database is busy.</span></span> <span data-ttu-id="89539-639">mentre un timeout troppo lungo può impedire che la logica di ripetizione dei tentativi funzioni correttamente, poiché l'intervallo di attesa prima che venga rilevata una connessione non riuscita è troppo lungo.</span><span class="sxs-lookup"><span data-stu-id="89539-639">Too long a timeout may prevent the retry logic working correctly by waiting too long before detecting a failed connection.</span></span> <span data-ttu-id="89539-640">Sebbene il valore di timeout sia un componente della latenza end-to-end, non è possibile determinare facilmente il numero di comandi che verranno eseguiti durante il salvataggio del contesto.</span><span class="sxs-lookup"><span data-stu-id="89539-640">The value of the timeout is a component of the end-to-end latency, although you cannot easily determine how many commands will execute when saving the context.</span></span> <span data-ttu-id="89539-641">È possibile modificare il timeout predefinito impostando la proprietà **CommandTimeout** dell'istanza **DbContext**.</span><span class="sxs-lookup"><span data-stu-id="89539-641">You can change the default timeout by setting the **CommandTimeout** property of the **DbContext** instance.</span></span>

- <span data-ttu-id="89539-642">Entity Framework supporta le configurazioni di ripetizione dei tentativi definite nei file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="89539-642">Entity Framework supports retry configurations defined in configuration files.</span></span> <span data-ttu-id="89539-643">Per ottenere la massima flessibilità in Azure, tuttavia, è opportuno valutare la possibilità di creare la configurazione a livello di codice all'interno dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="89539-643">However, for maximum flexibility on Azure you should consider creating the configuration programmatically within the application.</span></span> <span data-ttu-id="89539-644">I parametri specifici dei criteri di ripetizione dei tentativi, ad esempio il numero di tentativi e l'intervallo tra di essi, possono essere archiviati nel file di configurazione del servizio e usati in fase di esecuzione per creare i criteri appropriati.</span><span class="sxs-lookup"><span data-stu-id="89539-644">The specific parameters for the retry policies, such as the number of retries and the retry intervals, can be stored in the service configuration file and used at runtime to create the appropriate policies.</span></span> <span data-ttu-id="89539-645">In questo modo è possibile modificare le impostazioni senza dover riavviare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="89539-645">This allows the settings to be changed without requiring the application to be restarted.</span></span>

<span data-ttu-id="89539-646">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="89539-646">Consider starting with the following settings for retrying operations.</span></span> <span data-ttu-id="89539-647">Non è possibile specificare l'intervallo tra i tentativi (si tratta di un valore fisso generato come sequenza esponenziale);</span><span class="sxs-lookup"><span data-stu-id="89539-647">You cannot specify the delay between retry attempts (it is fixed and generated as an exponential sequence).</span></span> <span data-ttu-id="89539-648">è possibile specificare solo i valori massimi, come illustrato di seguito, a meno che non sia stata creata una strategia di ripetizione dei tentativi personalizzata.</span><span class="sxs-lookup"><span data-stu-id="89539-648">You can specify only the maximum values, as shown here; unless you create a custom retry strategy.</span></span> <span data-ttu-id="89539-649">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="89539-649">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="89539-650">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="89539-650">**Context**</span></span> | <span data-ttu-id="89539-651">**Destinazione di esempio E2E<br />latenza massima**</span><span class="sxs-lookup"><span data-stu-id="89539-651">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="89539-652">**Criteri di ripetizione**</span><span class="sxs-lookup"><span data-stu-id="89539-652">**Retry policy**</span></span> | <span data-ttu-id="89539-653">**Impostazioni**</span><span class="sxs-lookup"><span data-stu-id="89539-653">**Settings**</span></span> | <span data-ttu-id="89539-654">**Valori**</span><span class="sxs-lookup"><span data-stu-id="89539-654">**Values**</span></span> | <span data-ttu-id="89539-655">**Funzionamento**</span><span class="sxs-lookup"><span data-stu-id="89539-655">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="89539-656">Interattivo, interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="89539-656">Interactive, UI,</span></span><br /><span data-ttu-id="89539-657">o in primo piano</span><span class="sxs-lookup"><span data-stu-id="89539-657">or foreground</span></span> |<span data-ttu-id="89539-658">2 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-658">2 seconds</span></span> |<span data-ttu-id="89539-659">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="89539-659">Exponential</span></span> |<span data-ttu-id="89539-660">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="89539-660">MaxRetryCount</span></span><br /><span data-ttu-id="89539-661">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="89539-661">MaxDelay</span></span> |<span data-ttu-id="89539-662">3</span><span class="sxs-lookup"><span data-stu-id="89539-662">3</span></span><br /><span data-ttu-id="89539-663">750 ms</span><span class="sxs-lookup"><span data-stu-id="89539-663">750 ms</span></span> |<span data-ttu-id="89539-664">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="89539-664">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="89539-665">Tentativo 2 - intervallo di 750 ms</span><span class="sxs-lookup"><span data-stu-id="89539-665">Attempt 2 - delay 750 ms</span></span><br /><span data-ttu-id="89539-666">Tentativo 3 - intervallo di 750 ms</span><span class="sxs-lookup"><span data-stu-id="89539-666">Attempt 3 – delay 750 ms</span></span> |
| <span data-ttu-id="89539-667">Background</span><span class="sxs-lookup"><span data-stu-id="89539-667">Background</span></span><br /> <span data-ttu-id="89539-668">o batch</span><span class="sxs-lookup"><span data-stu-id="89539-668">or batch</span></span> |<span data-ttu-id="89539-669">30 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-669">30 seconds</span></span> |<span data-ttu-id="89539-670">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="89539-670">Exponential</span></span> |<span data-ttu-id="89539-671">MaxRetryCount</span><span class="sxs-lookup"><span data-stu-id="89539-671">MaxRetryCount</span></span><br /><span data-ttu-id="89539-672">MaxDelay</span><span class="sxs-lookup"><span data-stu-id="89539-672">MaxDelay</span></span> |<span data-ttu-id="89539-673">5</span><span class="sxs-lookup"><span data-stu-id="89539-673">5</span></span><br /><span data-ttu-id="89539-674">12 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-674">12 seconds</span></span> |<span data-ttu-id="89539-675">Tentativo di 1 - intervallo di 0 sec</span><span class="sxs-lookup"><span data-stu-id="89539-675">Attempt 1 - delay 0 sec</span></span><br /><span data-ttu-id="89539-676">Tentativo 2 - intervallo di ~1 sec</span><span class="sxs-lookup"><span data-stu-id="89539-676">Attempt 2 - delay ~1 sec</span></span><br /><span data-ttu-id="89539-677">Tentativo 3 - intervallo di ~3 sec</span><span class="sxs-lookup"><span data-stu-id="89539-677">Attempt 3 - delay ~3 sec</span></span><br /><span data-ttu-id="89539-678">Tentativo 4 - intervallo di ~7 sec</span><span class="sxs-lookup"><span data-stu-id="89539-678">Attempt 4 - delay ~7 sec</span></span><br /><span data-ttu-id="89539-679">Tentativo 5 - intervallo di 12 sec</span><span class="sxs-lookup"><span data-stu-id="89539-679">Attempt 5 - delay 12 sec</span></span> |

> [!NOTE]
> <span data-ttu-id="89539-680">Gli obiettivi di latenza end-to-end presuppongono il timeout predefinito per le connessioni al servizio.</span><span class="sxs-lookup"><span data-stu-id="89539-680">The end-to-end latency targets assume the default timeout for connections to the service.</span></span> <span data-ttu-id="89539-681">Se si specifica un timeout di connessione più lungo, la latenza end-to-end verrà estesa di questo intervallo di tempo aggiuntivo per ogni nuovo tentativo.</span><span class="sxs-lookup"><span data-stu-id="89539-681">If you specify longer connection timeouts, the end-to-end latency will be extended by this additional time for every retry attempt.</span></span>

### <a name="examples"></a><span data-ttu-id="89539-682">Esempi</span><span class="sxs-lookup"><span data-stu-id="89539-682">Examples</span></span>

<span data-ttu-id="89539-683">L'esempio di codice seguente definisce una soluzione di accesso ai dati semplice che usa Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="89539-683">The following code example defines a simple data access solution that uses Entity Framework.</span></span> <span data-ttu-id="89539-684">Imposta una strategia di ripetizione dei tentativi specifica tramite la definizione di un'istanza di una classe denominata **BlogConfiguration** che estende **DbConfiguration**.</span><span class="sxs-lookup"><span data-stu-id="89539-684">It sets a specific retry strategy by defining an instance of a class named **BlogConfiguration** that extends **DbConfiguration**.</span></span>

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

<span data-ttu-id="89539-685">Altri esempi sull'uso del meccanismo di ripetizione dei tentavi in Entity Framework sono disponibili in [resilienza delle connessioni / logica di ripetizione dei tentativi](/ef/ef6/fundamentals/connection-resiliency/retry-logic).</span><span class="sxs-lookup"><span data-stu-id="89539-685">More examples of using the Entity Framework retry mechanism can be found in [Connection Resiliency / Retry Logic](/ef/ef6/fundamentals/connection-resiliency/retry-logic).</span></span>

### <a name="more-information"></a><span data-ttu-id="89539-686">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-686">More information</span></span>

- [<span data-ttu-id="89539-687">Guida relativa all'elasticità e alle prestazioni del database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="89539-687">Azure SQL Database performance and elasticity guide</span></span>](https://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## <a name="sql-database-using-entity-framework-core"></a><span data-ttu-id="89539-688">Database SQL con Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="89539-688">SQL Database using Entity Framework Core</span></span>

<span data-ttu-id="89539-689">[Entity Framework Core](/ef/core/) è un mapper relazionale a oggetti che consente agli sviluppatori .NET Core di usare dati mediante oggetti specifici di dominio.</span><span class="sxs-lookup"><span data-stu-id="89539-689">[Entity Framework Core](/ef/core/) is an object-relational mapper that enables .NET Core developers to work with data using domain-specific objects.</span></span> <span data-ttu-id="89539-690">Elimina la necessità di gran parte del codice di accesso ai dati che in genere gli sviluppatori devono scrivere.</span><span class="sxs-lookup"><span data-stu-id="89539-690">It eliminates the need for most of the data-access code that developers usually need to write.</span></span> <span data-ttu-id="89539-691">Questa versione di Entity Framework è stata scritta da zero e non eredita automaticamente tutte le funzionalità di EF6.x.</span><span class="sxs-lookup"><span data-stu-id="89539-691">This version of Entity Framework was written from the ground up, and doesn't automatically inherit all the features from EF6.x.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-692">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-692">Retry mechanism</span></span>

<span data-ttu-id="89539-693">Quando si accede al database SQL con Entity Framework Core, il supporto per la ripetizione di tentativi viene offerto attraverso un meccanismo denominato [Resilienza della connessione](/ef/core/miscellaneous/connection-resiliency).</span><span class="sxs-lookup"><span data-stu-id="89539-693">Retry support is provided when accessing SQL Database using Entity Framework Core through a mechanism called [connection resiliency](/ef/core/miscellaneous/connection-resiliency).</span></span> <span data-ttu-id="89539-694">Resilienza connessione è stato introdotto in EF Core 1.1.0.</span><span class="sxs-lookup"><span data-stu-id="89539-694">Connection resiliency was introduced in EF Core 1.1.0.</span></span>

<span data-ttu-id="89539-695">La principale astrazione è l'interfaccia `IExecutionStrategy`.</span><span class="sxs-lookup"><span data-stu-id="89539-695">The primary abstraction is the `IExecutionStrategy` interface.</span></span> <span data-ttu-id="89539-696">La strategia di esecuzione per SQL Server, che include SQL Azure, riconosce i tipi di eccezione per cui è possibile ripetere i tentativi e dispone di impostazioni predefinite ragionevoli per un numero massimo ripetizione dei tentativi, intervalli tra di essi e così via.</span><span class="sxs-lookup"><span data-stu-id="89539-696">The execution strategy for SQL Server, including SQL Azure, is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, and so on.</span></span>

### <a name="examples"></a><span data-ttu-id="89539-697">Esempi</span><span class="sxs-lookup"><span data-stu-id="89539-697">Examples</span></span>

<span data-ttu-id="89539-698">Il codice seguente abilita le ripetizioni automatiche di tentativi quando si configura l'oggetto DbContext, che rappresenta una sessione con il database.</span><span class="sxs-lookup"><span data-stu-id="89539-698">The following code enables automatic retries when configuring the DbContext object, which represents a session with the database.</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(
            @"Server=(localdb)\mssqllocaldb;Database=EFMiscellanous.ConnectionResiliency;Trusted_Connection=True;",
            options => options.EnableRetryOnFailure());
}
```

<span data-ttu-id="89539-699">Il codice seguente illustra come eseguire una transazione con la ripetizione di tentativi automatica, usando una strategia di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="89539-699">The following code shows how to execute a transaction with automatic retries, by using an execution strategy.</span></span> <span data-ttu-id="89539-700">La transazione viene definita in un delegato.</span><span class="sxs-lookup"><span data-stu-id="89539-700">The transaction is defined in a delegate.</span></span> <span data-ttu-id="89539-701">Se si verifica un errore temporaneo, la strategia di esecuzione chiamerà nuovamente il delegato.</span><span class="sxs-lookup"><span data-stu-id="89539-701">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

```csharp
using (var db = new BloggingContext())
{
    var strategy = db.Database.CreateExecutionStrategy();

    strategy.Execute(() =>
    {
        using (var transaction = db.Database.BeginTransaction())
        {
            db.Blogs.Add(new Blog { Url = "https://blogs.msdn.com/dotnet" });
            db.SaveChanges();

            db.Blogs.Add(new Blog { Url = "https://blogs.msdn.com/visualstudio" });
            db.SaveChanges();

            transaction.Commit();
        }
    });
}
```

## <a name="azure-storage"></a><span data-ttu-id="89539-702">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="89539-702">Azure Storage</span></span>

<span data-ttu-id="89539-703">I servizi di archiviazione di Azure includono funzioni di archiviazione di BLOB e tabelle, file e code di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="89539-703">Azure Storage services include table and blob storage, files, and storage queues.</span></span>

### <a name="retry-mechanism"></a><span data-ttu-id="89539-704">Meccanismo di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-704">Retry mechanism</span></span>

<span data-ttu-id="89539-705">I nuovi tentativi si compiono a livello di singola operazione REST e sono parte integrante dell'implementazione dell'API client.</span><span class="sxs-lookup"><span data-stu-id="89539-705">Retries occur at the individual REST operation level and are an integral part of the client API implementation.</span></span> <span data-ttu-id="89539-706">L'SDK di archiviazione dei client usa classi che implementano l'interfaccia [IExtendedRetryPolicy Interface](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy).</span><span class="sxs-lookup"><span data-stu-id="89539-706">The client storage SDK uses classes that implement the [IExtendedRetryPolicy Interface](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy).</span></span>

<span data-ttu-id="89539-707">Esistono diverse implementazioni di questa interfaccia.</span><span class="sxs-lookup"><span data-stu-id="89539-707">There are different implementations of the interface.</span></span> <span data-ttu-id="89539-708">I client di archiviazione possono scegliere tra vari tipi di criteri, progettati per l'accesso a tabelle, BLOB o code.</span><span class="sxs-lookup"><span data-stu-id="89539-708">Storage clients can choose from policies specifically designed for accessing tables, blobs, and queues.</span></span> <span data-ttu-id="89539-709">Ogni implementazione usa una strategia di ripetizione dei tentativi diversa, che essenzialmente definisce l'intervallo di tempo tra i tentativi e altri dettagli.</span><span class="sxs-lookup"><span data-stu-id="89539-709">Each implementation uses a different retry strategy that essentially defines the retry interval and other details.</span></span>

<span data-ttu-id="89539-710">Le classi incorporate forniscono il supporto per intervalli lineari (tempo d'intervallo costante) ed esponenziali con sequenza casuale.</span><span class="sxs-lookup"><span data-stu-id="89539-710">The built-in classes provide support for linear (constant delay) and exponential with randomization retry intervals.</span></span> <span data-ttu-id="89539-711">Esiste anche un criterio di tipo "nessun tentativo" se la gestione dei tentativi viene eseguita da un altro processo a un livello superiore.</span><span class="sxs-lookup"><span data-stu-id="89539-711">There is also a no retry policy for use when another process is handling retries at a higher level.</span></span> <span data-ttu-id="89539-712">Se, tuttavia, sono presenti requisiti specifici non previsti dalle classi incorporate, è possibile implementare classi di ripetizione dei tentativi personalizzate.</span><span class="sxs-lookup"><span data-stu-id="89539-712">However, you can implement your own retry classes if you have specific requirements not provided by the built-in classes.</span></span>

<span data-ttu-id="89539-713">Se si usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è possibile, ad esempio, alternare i tentativi tra il percorso del servizio di archiviazione primario e quello del servizio secondario e il risultato della richiesta è un errore non irreversibile.</span><span class="sxs-lookup"><span data-stu-id="89539-713">Alternate retries switch between primary and secondary storage service location if you are using read access geo-redundant storage (RA-GRS) and the result of the request is a retryable error.</span></span> <span data-ttu-id="89539-714">Per altre informazioni, vedere [Opzioni di ridondanza dell'archiviazione di Azure](/azure/storage/common/storage-redundancy) .</span><span class="sxs-lookup"><span data-stu-id="89539-714">See [Azure Storage Redundancy Options](/azure/storage/common/storage-redundancy) for more information.</span></span>

### <a name="policy-configuration"></a><span data-ttu-id="89539-715">Configurazione dei criteri</span><span class="sxs-lookup"><span data-stu-id="89539-715">Policy configuration</span></span>

<span data-ttu-id="89539-716">I criteri di ripetizione dei tentativi sono configurati a livello di codice.</span><span class="sxs-lookup"><span data-stu-id="89539-716">Retry policies are configured programmatically.</span></span> <span data-ttu-id="89539-717">La procedura tipica consiste nel creare e popolare un'istanza **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions** o **QueueRequestOptions**.</span><span class="sxs-lookup"><span data-stu-id="89539-717">A typical procedure is to create and populate a **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions**, or **QueueRequestOptions** instance.</span></span>

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

<span data-ttu-id="89539-718">L'istanza delle opzioni di richiesta può essere quindi impostata sul client in modo che tutte le operazioni eseguite sul client usino le opzioni di richiesta specificate.</span><span class="sxs-lookup"><span data-stu-id="89539-718">The request options instance can then be set on the client, and all operations with the client will use the specified request options.</span></span>

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

<span data-ttu-id="89539-719">È comunque possibile ignorare le opzioni di richiesta del client passando ai metodi dell'operazione un'istanza popolata della classe delle opzioni di richiesta sotto forma di parametro.</span><span class="sxs-lookup"><span data-stu-id="89539-719">You can override the client request options by passing a populated instance of the request options class as a parameter to operation methods.</span></span>

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

<span data-ttu-id="89539-720">Si userà in questo caso un'istanza **OperationContext** per specificare il codice da eseguire quando si ripete un tentativo o quando, invece, viene completata un'operazione.</span><span class="sxs-lookup"><span data-stu-id="89539-720">You use an **OperationContext** instance to specify the code to execute when a retry occurs and when an operation has completed.</span></span> <span data-ttu-id="89539-721">Questo codice è in grado di raccogliere informazioni sull'operazione, da usare nei log e nella telemetria.</span><span class="sxs-lookup"><span data-stu-id="89539-721">This code can collect information about the operation for use in logs and telemetry.</span></span>

```csharp
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
```

<span data-ttu-id="89539-722">Oltre a indicare se un errore può essere risolto eseguendo nuovi tentativi, i criteri estesi di ripetizione dei tentativi restituiscono un oggetto **RetryContext** , che indica il numero di tentativi, i risultati dell'ultima richiesta e se il tentativo successivo verrà eseguito nel percorso primario o secondario (vedere la tabella seguente per maggiori dettagli).</span><span class="sxs-lookup"><span data-stu-id="89539-722">In addition to indicating whether a failure is suitable for retry, the extended retry policies return a **RetryContext** object that indicates the number of retries, the results of the last request, whether the next retry will happen in the primary or secondary location (see table below for details).</span></span> <span data-ttu-id="89539-723">Le proprietà dell'oggetto **RetryContext** possono essere inoltre usate per decidere se e quando eseguire un nuovo tentativo.</span><span class="sxs-lookup"><span data-stu-id="89539-723">The properties of the **RetryContext** object can be used to decide if and when to attempt a retry.</span></span> <span data-ttu-id="89539-724">Per altre informazioni, vedere [IExtendedRetryPolicy.Evaluate Method](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate).</span><span class="sxs-lookup"><span data-stu-id="89539-724">For more details, see [IExtendedRetryPolicy.Evaluate Method](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate).</span></span>

<span data-ttu-id="89539-725">Le tabelle seguenti mostrano le impostazioni predefinite per i criteri di ripetizione dei tentativi predefiniti.</span><span class="sxs-lookup"><span data-stu-id="89539-725">The following tables show the default settings for the built-in retry policies.</span></span>

<span data-ttu-id="89539-726">**Opzioni richiesta:**</span><span class="sxs-lookup"><span data-stu-id="89539-726">**Request options:**</span></span>

| <span data-ttu-id="89539-727">**Impostazione**</span><span class="sxs-lookup"><span data-stu-id="89539-727">**Setting**</span></span> | <span data-ttu-id="89539-728">**Valore predefinito**</span><span class="sxs-lookup"><span data-stu-id="89539-728">**Default value**</span></span> | <span data-ttu-id="89539-729">**Significato**</span><span class="sxs-lookup"><span data-stu-id="89539-729">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="89539-730">MaximumExecutionTime</span><span class="sxs-lookup"><span data-stu-id="89539-730">MaximumExecutionTime</span></span> | <span data-ttu-id="89539-731">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-731">None</span></span> | <span data-ttu-id="89539-732">Tempo di esecuzione massimo per la richiesta, inclusi tutti i potenziali tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-732">Maximum execution time for the request, including all potential retry attempts.</span></span> <span data-ttu-id="89539-733">Se non è specificato, il tempo che una richiesta è autorizzata a impiegare è illimitato.</span><span class="sxs-lookup"><span data-stu-id="89539-733">If it is not specified, then the amount of time that a request is permitted to take is unlimited.</span></span> <span data-ttu-id="89539-734">In altre parole, la richiesta potrebbe bloccarsi.</span><span class="sxs-lookup"><span data-stu-id="89539-734">In other words, the request might hang.</span></span> |
| <span data-ttu-id="89539-735">ServerTimeout</span><span class="sxs-lookup"><span data-stu-id="89539-735">ServerTimeout</span></span> | <span data-ttu-id="89539-736">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-736">None</span></span> | <span data-ttu-id="89539-737">Intervallo di timeout del server per la richiesta (il valore viene arrotondato a secondi).</span><span class="sxs-lookup"><span data-stu-id="89539-737">Server timeout interval for the request (value is rounded to seconds).</span></span> <span data-ttu-id="89539-738">Se non specificato, verrà usato il valore predefinito per tutte le richieste al server.</span><span class="sxs-lookup"><span data-stu-id="89539-738">If not specified, it will use the default value for all requests to the server.</span></span> <span data-ttu-id="89539-739">In genere, la scelta migliore è omettere questa impostazione in modo che venga usato il valore predefinito del server.</span><span class="sxs-lookup"><span data-stu-id="89539-739">Usually, the best option is to omit this setting so that the server default is used.</span></span> |
| <span data-ttu-id="89539-740">LocationMode</span><span class="sxs-lookup"><span data-stu-id="89539-740">LocationMode</span></span> | <span data-ttu-id="89539-741">Nessuna</span><span class="sxs-lookup"><span data-stu-id="89539-741">None</span></span> | <span data-ttu-id="89539-742">Se l'account di archiviazione viene creato con l'opzione di replica "archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)", è possibile usare la modalità percorso per indicare in corrispondenza di quale percorso deve essere ricevuta la richiesta.</span><span class="sxs-lookup"><span data-stu-id="89539-742">If the storage account is created with the Read access geo-redundant storage (RA-GRS) replication option, you can use the location mode to indicate which location should receive the request.</span></span> <span data-ttu-id="89539-743">Ad esempio, se è specificata l'opzione **PrimaryThenSecondary** , le richieste vengono sempre inviate prima al percorso primario.</span><span class="sxs-lookup"><span data-stu-id="89539-743">For example, if **PrimaryThenSecondary** is specified, requests are always sent to the primary location first.</span></span> <span data-ttu-id="89539-744">Se una richiesta ha esito negativo, viene quindi inviata al percorso secondario.</span><span class="sxs-lookup"><span data-stu-id="89539-744">If a request fails, it is sent to the secondary location.</span></span> |
| <span data-ttu-id="89539-745">RetryPolicy</span><span class="sxs-lookup"><span data-stu-id="89539-745">RetryPolicy</span></span> | <span data-ttu-id="89539-746">ExponentialPolicy</span><span class="sxs-lookup"><span data-stu-id="89539-746">ExponentialPolicy</span></span> | <span data-ttu-id="89539-747">Per informazioni dettagliate su ogni opzione, vedere le sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="89539-747">See below for details of each option.</span></span> |

<span data-ttu-id="89539-748">**Criteri esponenziali:**</span><span class="sxs-lookup"><span data-stu-id="89539-748">**Exponential policy:**</span></span>

| <span data-ttu-id="89539-749">**Impostazione**</span><span class="sxs-lookup"><span data-stu-id="89539-749">**Setting**</span></span> | <span data-ttu-id="89539-750">**Valore predefinito**</span><span class="sxs-lookup"><span data-stu-id="89539-750">**Default value**</span></span> | <span data-ttu-id="89539-751">**Significato**</span><span class="sxs-lookup"><span data-stu-id="89539-751">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="89539-752">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="89539-752">maxAttempt</span></span> | <span data-ttu-id="89539-753">3</span><span class="sxs-lookup"><span data-stu-id="89539-753">3</span></span> | <span data-ttu-id="89539-754">Numero di tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-754">Number of retry attempts.</span></span> |
| <span data-ttu-id="89539-755">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-755">deltaBackoff</span></span> | <span data-ttu-id="89539-756">4 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-756">4 seconds</span></span> | <span data-ttu-id="89539-757">Intervallo di backoff tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-757">Back-off interval between retries.</span></span> <span data-ttu-id="89539-758">Per i tentativi successivi verranno usati multipli di questo intervallo di tempo, combinati con un elemento casuale.</span><span class="sxs-lookup"><span data-stu-id="89539-758">Multiples of this timespan, including a random element, will be used for subsequent retry attempts.</span></span> |
| <span data-ttu-id="89539-759">MinBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-759">MinBackoff</span></span> | <span data-ttu-id="89539-760">3 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-760">3 seconds</span></span> | <span data-ttu-id="89539-761">I valori vengono aggiunti a tutti gli intervalli tra i tentativi, calcolati a partire da deltaBackoff.</span><span class="sxs-lookup"><span data-stu-id="89539-761">Added to all retry intervals computed from deltaBackoff.</span></span> <span data-ttu-id="89539-762">Questo valore non può essere modificato.</span><span class="sxs-lookup"><span data-stu-id="89539-762">This value cannot be changed.</span></span>
| <span data-ttu-id="89539-763">MaxBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-763">MaxBackoff</span></span> | <span data-ttu-id="89539-764">120 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-764">120 seconds</span></span> | <span data-ttu-id="89539-765">MaxBackoff viene usato se l'intervallo tra i tentativi è maggiore di MaxBackoff.</span><span class="sxs-lookup"><span data-stu-id="89539-765">MaxBackoff is used if the computed retry interval is greater than MaxBackoff.</span></span> <span data-ttu-id="89539-766">Questo valore non può essere modificato.</span><span class="sxs-lookup"><span data-stu-id="89539-766">This value cannot be changed.</span></span> |

<span data-ttu-id="89539-767">**Criteri lineari:**</span><span class="sxs-lookup"><span data-stu-id="89539-767">**Linear policy:**</span></span>

| <span data-ttu-id="89539-768">**Impostazione**</span><span class="sxs-lookup"><span data-stu-id="89539-768">**Setting**</span></span> | <span data-ttu-id="89539-769">**Valore predefinito**</span><span class="sxs-lookup"><span data-stu-id="89539-769">**Default value**</span></span> | <span data-ttu-id="89539-770">**Significato**</span><span class="sxs-lookup"><span data-stu-id="89539-770">**Meaning**</span></span> |
| --- | --- | --- |
| <span data-ttu-id="89539-771">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="89539-771">maxAttempt</span></span> | <span data-ttu-id="89539-772">3</span><span class="sxs-lookup"><span data-stu-id="89539-772">3</span></span> | <span data-ttu-id="89539-773">Numero di tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-773">Number of retry attempts.</span></span> |
| <span data-ttu-id="89539-774">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-774">deltaBackoff</span></span> | <span data-ttu-id="89539-775">30 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-775">30 seconds</span></span> | <span data-ttu-id="89539-776">Intervallo di backoff tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-776">Back-off interval between retries.</span></span> |

### <a name="retry-usage-guidance"></a><span data-ttu-id="89539-777">Linee guida sull'uso dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-777">Retry usage guidance</span></span>

<span data-ttu-id="89539-778">Quando si accede ai servizi di archiviazione di Azure tramite l'API del client di archiviazione, tenere presenti le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="89539-778">Consider the following guidelines when accessing Azure storage services using the storage client API:</span></span>

- <span data-ttu-id="89539-779">Usare i criteri di ripetizione dei tentativi dello spazio dei nomi Microsoft.WindowsAzure.Storage.RetryPolicies se appropriati per le proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="89539-779">Use the built-in retry policies from the Microsoft.WindowsAzure.Storage.RetryPolicies namespace where they are appropriate for your requirements.</span></span> <span data-ttu-id="89539-780">Nella maggior parte dei casi, questi criteri saranno sufficienti.</span><span class="sxs-lookup"><span data-stu-id="89539-780">In most cases, these policies will be sufficient.</span></span>

- <span data-ttu-id="89539-781">Usare invece i criteri **ExponentialRetry** nelle operazioni in batch, nelle attività in background e negli scenari non interattivi.</span><span class="sxs-lookup"><span data-stu-id="89539-781">Use the **ExponentialRetry** policy in batch operations, background tasks, or non-interactive scenarios.</span></span> <span data-ttu-id="89539-782">In questi scenari è generalmente possibile concedere più tempo per il ripristino del servizio &mdash; con aumentando così le probabilità dell'operazione abbia esito positivo.</span><span class="sxs-lookup"><span data-stu-id="89539-782">In these scenarios, you can typically allow more time for the service to recover &mdash; with a consequently increased chance of the operation eventually succeeding.</span></span>

- <span data-ttu-id="89539-783">Valutare la possibilità di specificare la proprietà **MaximumExecutionTime** del parametro **RequestOptions** per limitare il tempo di esecuzione complessivo, ma tenere conto del tipo e delle dimensioni dell'operazione quando si sceglie un valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="89539-783">Consider specifying the **MaximumExecutionTime** property of the **RequestOptions** parameter to limit the total execution time, but take into account the type and size of the operation when choosing a timeout value.</span></span>

- <span data-ttu-id="89539-784">Se è necessario implementare un meccanismo di ripetizione dei tentativi personalizzato, evitare di creare wrapper per le classi dei client di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="89539-784">If you need to implement a custom retry, avoid creating wrappers around the storage client classes.</span></span> <span data-ttu-id="89539-785">Usare invece questa funzionalità per estendere i criteri esistenti tramite l'interfaccia **IExtendedRetryPolicy** .</span><span class="sxs-lookup"><span data-stu-id="89539-785">Instead, use the capabilities to extend the existing policies through the **IExtendedRetryPolicy** interface.</span></span>

- <span data-ttu-id="89539-786">Se si usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è possibile adottare la modalità **LocationMode** per specificare che i nuovi tentativi accederanno alla copia secondaria di sola lettura dell'archivio, nel caso in cui l'accesso alla versione principale abbia esito negativo.</span><span class="sxs-lookup"><span data-stu-id="89539-786">If you are using read access geo-redundant storage (RA-GRS) you can use the **LocationMode** to specify that retry attempts will access the secondary read-only copy of the store should the primary access fail.</span></span> <span data-ttu-id="89539-787">Quando si usa questa opzione, tuttavia, è necessario verificare che l'applicazione possa gestire correttamente anche dati non aggiornati, nel caso in cui la replica dall'archivio primario non sia ancora stata completata.</span><span class="sxs-lookup"><span data-stu-id="89539-787">However, when using this option you must ensure that your application can work successfully with data that may be stale if the replication from the primary store has not yet completed.</span></span>

<span data-ttu-id="89539-788">È consigliabile iniziare le operazioni di ripetizione dei tentativi usando le impostazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="89539-788">Consider starting with following settings for retrying operations.</span></span> <span data-ttu-id="89539-789">Si tratta di impostazioni di uso generale ed è quindi necessario monitorare le operazioni e personalizzare i valori in base allo scenario.</span><span class="sxs-lookup"><span data-stu-id="89539-789">These are general purpose settings, and you should monitor the operations and fine tune the values to suit your own scenario.</span></span>

| <span data-ttu-id="89539-790">**Contesto**</span><span class="sxs-lookup"><span data-stu-id="89539-790">**Context**</span></span> | <span data-ttu-id="89539-791">**Destinazione di esempio E2E<br />latenza massima**</span><span class="sxs-lookup"><span data-stu-id="89539-791">**Sample target E2E<br />max latency**</span></span> | <span data-ttu-id="89539-792">**Criteri di ripetizione**</span><span class="sxs-lookup"><span data-stu-id="89539-792">**Retry policy**</span></span> | <span data-ttu-id="89539-793">**Impostazioni**</span><span class="sxs-lookup"><span data-stu-id="89539-793">**Settings**</span></span> | <span data-ttu-id="89539-794">**Valori**</span><span class="sxs-lookup"><span data-stu-id="89539-794">**Values**</span></span> | <span data-ttu-id="89539-795">**Funzionamento**</span><span class="sxs-lookup"><span data-stu-id="89539-795">**How it works**</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="89539-796">Interattivo, interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="89539-796">Interactive, UI,</span></span><br /><span data-ttu-id="89539-797">o in primo piano</span><span class="sxs-lookup"><span data-stu-id="89539-797">or foreground</span></span> |<span data-ttu-id="89539-798">2 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-798">2 seconds</span></span> |<span data-ttu-id="89539-799">Lineari</span><span class="sxs-lookup"><span data-stu-id="89539-799">Linear</span></span> |<span data-ttu-id="89539-800">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="89539-800">maxAttempt</span></span><br /><span data-ttu-id="89539-801">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-801">deltaBackoff</span></span> |<span data-ttu-id="89539-802">3</span><span class="sxs-lookup"><span data-stu-id="89539-802">3</span></span><br /><span data-ttu-id="89539-803">500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-803">500 ms</span></span> |<span data-ttu-id="89539-804">Tentativo di 1 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-804">Attempt 1 - delay 500 ms</span></span><br /><span data-ttu-id="89539-805">Tentativo 2 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-805">Attempt 2 - delay 500 ms</span></span><br /><span data-ttu-id="89539-806">Tentativo 3 - intervallo di 500 ms</span><span class="sxs-lookup"><span data-stu-id="89539-806">Attempt 3 - delay 500 ms</span></span> |
| <span data-ttu-id="89539-807">Background</span><span class="sxs-lookup"><span data-stu-id="89539-807">Background</span></span><br /><span data-ttu-id="89539-808">o batch</span><span class="sxs-lookup"><span data-stu-id="89539-808">or batch</span></span> |<span data-ttu-id="89539-809">30 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-809">30 seconds</span></span> |<span data-ttu-id="89539-810">Esponenziali</span><span class="sxs-lookup"><span data-stu-id="89539-810">Exponential</span></span> |<span data-ttu-id="89539-811">maxAttempt</span><span class="sxs-lookup"><span data-stu-id="89539-811">maxAttempt</span></span><br /><span data-ttu-id="89539-812">deltaBackoff</span><span class="sxs-lookup"><span data-stu-id="89539-812">deltaBackoff</span></span> |<span data-ttu-id="89539-813">5</span><span class="sxs-lookup"><span data-stu-id="89539-813">5</span></span><br /><span data-ttu-id="89539-814">4 secondi</span><span class="sxs-lookup"><span data-stu-id="89539-814">4 seconds</span></span> |<span data-ttu-id="89539-815">Tentativo di 1 - intervallo di ~3 sec</span><span class="sxs-lookup"><span data-stu-id="89539-815">Attempt 1 - delay ~3 sec</span></span><br /><span data-ttu-id="89539-816">Tentativo 2 - intervallo di ~7 sec</span><span class="sxs-lookup"><span data-stu-id="89539-816">Attempt 2 - delay ~7 sec</span></span><br /><span data-ttu-id="89539-817">Tentativo 3 - intervallo di ~15 sec</span><span class="sxs-lookup"><span data-stu-id="89539-817">Attempt 3 - delay ~15 sec</span></span> |

### <a name="telemetry"></a><span data-ttu-id="89539-818">Telemetria</span><span class="sxs-lookup"><span data-stu-id="89539-818">Telemetry</span></span>

<span data-ttu-id="89539-819">Il numero di tentativi viene registrato in un'origine **TraceSource**.</span><span class="sxs-lookup"><span data-stu-id="89539-819">Retry attempts are logged to a **TraceSource**.</span></span> <span data-ttu-id="89539-820">È necessario configurare un oggetto **TraceListener** per acquisire gli eventi e scriverli in un log di destinazione appropriato.</span><span class="sxs-lookup"><span data-stu-id="89539-820">You must configure a **TraceListener** to capture the events and write them to a suitable destination log.</span></span> <span data-ttu-id="89539-821">È possibile usare **TextWriterTraceListener** o **XmlWriterTraceListener** per scrivere i dati in un file di log, **EventLogTraceListener** per scriverli nel Registro eventi di Windows o **EventProviderTraceListener** per scrivere i dati di traccia nel sottosistema ETW.</span><span class="sxs-lookup"><span data-stu-id="89539-821">You can use the **TextWriterTraceListener** or **XmlWriterTraceListener** to write the data to a log file, the **EventLogTraceListener** to write to the Windows Event Log, or the **EventProviderTraceListener** to write trace data to the ETW subsystem.</span></span> <span data-ttu-id="89539-822">È inoltre possibile configurare lo svuotamento automatico del buffer e il livello di dettaglio degli eventi che verranno registrati nel log (specificando, ad esempio, i valori Error, Warning, Informational e Verbose).</span><span class="sxs-lookup"><span data-stu-id="89539-822">You can also configure auto-flushing of the buffer, and the verbosity of events that will be logged (for example, Error, Warning, Informational, and Verbose).</span></span> <span data-ttu-id="89539-823">Per altre informazioni, vedere [Registrazione lato client con la libreria client di archiviazione .NET](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library).</span><span class="sxs-lookup"><span data-stu-id="89539-823">For more information, see [Client-side Logging with the .NET Storage Client Library](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library).</span></span>

<span data-ttu-id="89539-824">Le operazioni possono ricevere un'istanza **OperationContext**, che espone un evento **Retrying** di cui è possibile usufruire per associare una logica personalizzata per i dati telemetrici.</span><span class="sxs-lookup"><span data-stu-id="89539-824">Operations can receive an **OperationContext** instance, which exposes a **Retrying** event that can be used to attach custom telemetry logic.</span></span> <span data-ttu-id="89539-825">Per altre informazioni, vedere [Evento OperationContext.Retrying](/dotnet/api/microsoft.windowsazure.storage.operationcontext.retrying).</span><span class="sxs-lookup"><span data-stu-id="89539-825">For more information, see [OperationContext.Retrying Event](/dotnet/api/microsoft.windowsazure.storage.operationcontext.retrying).</span></span>

### <a name="examples"></a><span data-ttu-id="89539-826">Esempi</span><span class="sxs-lookup"><span data-stu-id="89539-826">Examples</span></span>

<span data-ttu-id="89539-827">L'esempio di codice seguente mostra come creare due istanze **TableRequestOptions** con due diverse impostazioni di ripetizione dei tentativi: una per le richieste interattive e una per le richieste in background.</span><span class="sxs-lookup"><span data-stu-id="89539-827">The following code example shows how to create two **TableRequestOptions** instances with different retry settings; one for interactive requests and one for background requests.</span></span> <span data-ttu-id="89539-828">L'esempio imposta quindi questi due criteri di ripetizione dei tentativi in modo che vengano applicati a tutte le richieste. Imposta inoltre la strategia interattiva su una richiesta specifica in modo che ignori le impostazioni predefinite applicate al client.</span><span class="sxs-lookup"><span data-stu-id="89539-828">The example then sets these two retry policies on the client so that they apply for all requests, and also sets the interactive strategy on a specific request so that it overrides the default settings applied to the client.</span></span>

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
                // Maximum execution time based on the business use case.
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

### <a name="more-information"></a><span data-ttu-id="89539-829">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-829">More information</span></span>

- [<span data-ttu-id="89539-830">Suggerimenti per i criteri di ripetizione dei tentativi nella libreria client dell'archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="89539-830">Azure Storage client Library retry policy recommendations</span></span>](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)

- [<span data-ttu-id="89539-831">Libreria client di archiviazione 2.0 - Implementazione dei criteri di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-831">Storage Client Library 2.0 – Implementing retry policies</span></span>](https://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## <a name="general-rest-and-retry-guidelines"></a><span data-ttu-id="89539-832">Linee guida generali su REST e sulla ripetizione di tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-832">General REST and retry guidelines</span></span>

<span data-ttu-id="89539-833">Quando si accede a servizi di Azure o di terze parti, tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="89539-833">Consider the following when accessing Azure or third party services:</span></span>

- <span data-ttu-id="89539-834">Usare un approccio sistematico per gestire la ripetizione di tentativi, ad esempio sotto forma di codice riusabile, in modo da poter applicare una metodologia coerente tra tutti i client e le soluzioni.</span><span class="sxs-lookup"><span data-stu-id="89539-834">Use a systematic approach to managing retries, perhaps as reusable code, so that you can apply a consistent methodology across all clients and all solutions.</span></span>

- <span data-ttu-id="89539-835">Valutare la possibilità di usare un framework di ripetizione dei tentativi come [Polly][polly] per gestire la ripetizione di tentativi se nel client o nel servizio di destinazione non è incorporato un meccanismo di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-835">Consider using a retry framework such as [Polly][polly] to manage retries if the target service or client has no built-in retry mechanism.</span></span> <span data-ttu-id="89539-836">In questo modo, sarà possibile implementare un comportamento coerente per la gestione dei nuovi tentativi e usufruire di un'adeguata strategia di ripetizione dei tentativi predefinita per il servizio di destinazione.</span><span class="sxs-lookup"><span data-stu-id="89539-836">This will help you implement a consistent retry behavior, and it may provide a suitable default retry strategy for the target service.</span></span> <span data-ttu-id="89539-837">Potrebbe essere necessario, tuttavia, creare un codice di ripetizione dei tentativi personalizzato per i servizi che presentano un comportamento non standard, ovvero non basato sulle eccezioni per stabilire gli errori temporanei, oppure se si desidera usare un metodo **Retry-Response** per gestire il comportamento di ripetizione dei tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-837">However, you may need to create custom retry code for services that have non-standard behavior, that do not rely on exceptions to indicate transient failures, or if you want to use a **Retry-Response** reply to manage retry behavior.</span></span>

- <span data-ttu-id="89539-838">La logica di rilevamento degli errori temporanei dipenderà dall'API client effettiva usata per richiamare le chiamate REST.</span><span class="sxs-lookup"><span data-stu-id="89539-838">The transient detection logic will depend on the actual client API you use to invoke the REST calls.</span></span> <span data-ttu-id="89539-839">Alcuni client, ad esempio la nuova classe **HttpClient** , non genereranno eccezioni per le richieste completate con un codice di stato HTTP di errore.</span><span class="sxs-lookup"><span data-stu-id="89539-839">Some clients, such as the newer **HttpClient** class, will not throw exceptions for completed requests with a non-success HTTP status code.</span></span>

- <span data-ttu-id="89539-840">Il codice di stato HTTP restituito dal servizio consente di stabilire se l'errore è temporaneo.</span><span class="sxs-lookup"><span data-stu-id="89539-840">The HTTP status code returned from the service can help to indicate whether the failure is transient.</span></span> <span data-ttu-id="89539-841">È possibile che sia necessario esaminare le eccezioni generate da un client o dal framework di ripetizione dei tentativi per accedere al codice di stato o determinare il tipo di eccezione equivalente.</span><span class="sxs-lookup"><span data-stu-id="89539-841">You may need to examine the exceptions generated by a client or the retry framework to access the status code or to determine the equivalent exception type.</span></span> <span data-ttu-id="89539-842">I seguenti codici HTTP indicano, in genere, che è opportuno eseguire un nuovo tentativo:</span><span class="sxs-lookup"><span data-stu-id="89539-842">The following HTTP codes typically indicate that a retry is appropriate:</span></span>
  
  - <span data-ttu-id="89539-843">408 - Timeout richiesta</span><span class="sxs-lookup"><span data-stu-id="89539-843">408 Request Timeout</span></span>
  - <span data-ttu-id="89539-844">429 - Numero eccessivo di richieste</span><span class="sxs-lookup"><span data-stu-id="89539-844">429 Too Many Requests</span></span>
  - <span data-ttu-id="89539-845">500 - Errore interno del server</span><span class="sxs-lookup"><span data-stu-id="89539-845">500 Internal Server Error</span></span>
  - <span data-ttu-id="89539-846">502 - Gateway non valido</span><span class="sxs-lookup"><span data-stu-id="89539-846">502 Bad Gateway</span></span>
  - <span data-ttu-id="89539-847">503 - Servizio non disponibile</span><span class="sxs-lookup"><span data-stu-id="89539-847">503 Service Unavailable</span></span>
  - <span data-ttu-id="89539-848">504 - Timeout gateway</span><span class="sxs-lookup"><span data-stu-id="89539-848">504 Gateway Timeout</span></span>

- <span data-ttu-id="89539-849">Se si usa una logica di ripetizione dei tentativi basata sulle eccezioni, i valori seguenti indicano un errore temporaneo in cui non è possibile stabilire una connessione:</span><span class="sxs-lookup"><span data-stu-id="89539-849">If you base your retry logic on exceptions, the following typically indicate a transient failure where no connection could be established:</span></span>

  - <span data-ttu-id="89539-850">WebExceptionStatus.ConnectionClosed</span><span class="sxs-lookup"><span data-stu-id="89539-850">WebExceptionStatus.ConnectionClosed</span></span>
  - <span data-ttu-id="89539-851">WebExceptionStatus.ConnectFailure</span><span class="sxs-lookup"><span data-stu-id="89539-851">WebExceptionStatus.ConnectFailure</span></span>
  - <span data-ttu-id="89539-852">WebExceptionStatus.Timeout</span><span class="sxs-lookup"><span data-stu-id="89539-852">WebExceptionStatus.Timeout</span></span>
  - <span data-ttu-id="89539-853">WebExceptionStatus.RequestCanceled</span><span class="sxs-lookup"><span data-stu-id="89539-853">WebExceptionStatus.RequestCanceled</span></span>

- <span data-ttu-id="89539-854">In caso di stato non disponibile di un servizio, l'intervallo di tempo appropriato prima di un nuovo tentativo può essere riportato nell'intestazione della risposta **Retry-After** o in un'altra intestazione personalizzata del servizio.</span><span class="sxs-lookup"><span data-stu-id="89539-854">In the case of a service unavailable status, the service might indicate the appropriate delay before retrying in the **Retry-After** response header or a different custom header.</span></span> <span data-ttu-id="89539-855">Alcuni servizi possono anche inviare informazioni aggiuntive come intestazioni personalizzate o incorporate nel contenuto della risposta.</span><span class="sxs-lookup"><span data-stu-id="89539-855">Services might also send additional information as custom headers, or embedded in the content of the response.</span></span>

- <span data-ttu-id="89539-856">Non eseguire nuovi tentativi in caso di codici di stato che rappresentano errori client (errori nell'intervallo 4xx), ad eccezione di 408 - Timeout richiesta.</span><span class="sxs-lookup"><span data-stu-id="89539-856">Do not retry for status codes representing client errors (errors in the 4xx range) except for a 408 Request Timeout.</span></span>

- <span data-ttu-id="89539-857">Verificare accuratamente le strategie e i meccanismi di ripetizione dei tentativi in condizioni diverse, ad esempio in vari stati di rete e carichi di sistema.</span><span class="sxs-lookup"><span data-stu-id="89539-857">Thoroughly test your retry strategies and mechanisms under a range of conditions, such as different network states and varying system loadings.</span></span>

### <a name="retry-strategies"></a><span data-ttu-id="89539-858">Strategie di ripetizione dei tentativi</span><span class="sxs-lookup"><span data-stu-id="89539-858">Retry strategies</span></span>

<span data-ttu-id="89539-859">Di seguito sono riportati i tipi intervallo più comuni nelle strategie di ripetizione dei tentativi:</span><span class="sxs-lookup"><span data-stu-id="89539-859">The following are the typical types of retry strategy intervals:</span></span>

- <span data-ttu-id="89539-860">**Esponenziale**.</span><span class="sxs-lookup"><span data-stu-id="89539-860">**Exponential**.</span></span> <span data-ttu-id="89539-861">Criteri di ripetizione dei tentativi che eseguono un numero prestabilito di nuovi tentativi e usano un approccio di backoff esponenziale casuale per determinare l'intervallo di tempo tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-861">A retry policy that performs a specified number of retries, using a randomized exponential back off approach to determine the interval between retries.</span></span> <span data-ttu-id="89539-862">Ad esempio: </span><span class="sxs-lookup"><span data-stu-id="89539-862">For example:</span></span>

    ```csharp
    var random = new Random();

    var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
                random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
                (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
    var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
                    this.maxBackoff.TotalMilliseconds);
    retryInterval = TimeSpan.FromMilliseconds(interval);
    ```

- <span data-ttu-id="89539-863">**Incrementale**.</span><span class="sxs-lookup"><span data-stu-id="89539-863">**Incremental**.</span></span> <span data-ttu-id="89539-864">Strategia di ripetizione dei tentativi con un numero prestabilito di nuovi tentativi e un intervallo di tempo incrementale tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-864">A retry strategy with a specified number of retry attempts and an incremental time interval between retries.</span></span> <span data-ttu-id="89539-865">Ad esempio: </span><span class="sxs-lookup"><span data-stu-id="89539-865">For example:</span></span>

    ```csharp
    retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
                    (this.increment.TotalMilliseconds * currentRetryCount));
    ```

- <span data-ttu-id="89539-866">**LinearRetry**.</span><span class="sxs-lookup"><span data-stu-id="89539-866">**LinearRetry**.</span></span> <span data-ttu-id="89539-867">Criteri di ripetizione dei tentativi che eseguono un numero prestabilito di nuovi tentativi e usano un intervallo di tempo fisso tra i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-867">A retry policy that performs a specified number of retries, using a specified fixed time interval between retries.</span></span> <span data-ttu-id="89539-868">Ad esempio: </span><span class="sxs-lookup"><span data-stu-id="89539-868">For example:</span></span>

    ```csharp
    retryInterval = this.deltaBackoff;
    ```

### <a name="transient-fault-handling-with-polly"></a><span data-ttu-id="89539-869">Gestione degli errori temporanei con Polly</span><span class="sxs-lookup"><span data-stu-id="89539-869">Transient fault handling with Polly</span></span>

<span data-ttu-id="89539-870">[Polly] [ polly] è una libreria per gestire a livello di codice di ripetizione dei tentativi e [interruttore](../patterns/circuit-breaker.md) strategie.</span><span class="sxs-lookup"><span data-stu-id="89539-870">[Polly][polly] is a library to programmatically handle retries and [circuit breaker](../patterns/circuit-breaker.md) strategies.</span></span> <span data-ttu-id="89539-871">Il progetto Polly è membro di [.NET Foundation][dotnet-foundation].</span><span class="sxs-lookup"><span data-stu-id="89539-871">The Polly project is a member of the [.NET Foundation][dotnet-foundation].</span></span> <span data-ttu-id="89539-872">Per i servizi in cui il client non supporta in modo nativo la ripetizione dei tentativi, Polly è una valida alternativa ed evita la necessità di scrivere il codice personalizzato per la ripetizione dei tentativi, la cui corretta implementazione può risultare difficile.</span><span class="sxs-lookup"><span data-stu-id="89539-872">For services where the client does not natively support retries, Polly is a valid alternative and avoids the need to write custom retry code, which can be hard to implement correctly.</span></span> <span data-ttu-id="89539-873">Polly offre anche un modo per tracciare gli errori quando questi si verificano, in modo che sia possibile registrare i tentativi.</span><span class="sxs-lookup"><span data-stu-id="89539-873">Polly also provides a way to trace errors when they occur, so that you can log retries.</span></span>

### <a name="more-information"></a><span data-ttu-id="89539-874">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="89539-874">More information</span></span>

- [<span data-ttu-id="89539-875">Resilienza della connessione</span><span class="sxs-lookup"><span data-stu-id="89539-875">Connection resiliency</span></span>](/ef/core/miscellaneous/connection-resiliency)
- [<span data-ttu-id="89539-876">Punto dati - EF Core 1.1</span><span class="sxs-lookup"><span data-stu-id="89539-876">Data Points - EF Core 1.1</span></span>](https://msdn.microsoft.com/magazine/mt745093.aspx)

<!-- links -->

[adal]: /azure/active-directory/develop/active-directory-authentication-libraries
[autorest]: https://github.com/Azure/autorest/tree/master/docs
[ConnectionPolicy.RetryOptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.retryoptions.aspx
[dotnet-foundation]: https://dotnetfoundation.org/
[polly]: http://www.thepollyproject.org
[redis-cache-troubleshoot]: /azure/redis-cache/cache-how-to-troubleshoot
[SearchIndexClient]: https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx
[SearchServiceClient]: https://msdn.microsoft.com/library/microsoft.azure.search.searchserviceclient.aspx
