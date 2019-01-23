---
title: Antipattern del database occupato
titleSuffix: Performance antipatterns for cloud apps
description: L'esecuzione del processo di offload in un server del database può causare problemi di prestazioni e scalabilità.
author: dragon119
ms.date: 06/05/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 7d3fe47407eff7168dfd227a1dd1bd5917c7d431
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487033"
---
# <a name="busy-database-antipattern"></a><span data-ttu-id="30790-103">Antipattern del database occupato</span><span class="sxs-lookup"><span data-stu-id="30790-103">Busy Database antipattern</span></span>

<span data-ttu-id="30790-104">L'esecuzione del processo di offload in un server del database può causarne una perdita significativa di tempo nell'operazione di esecuzione del codice, invece che in quella di rispondere alle richieste per archiviare e recuperare i dati.</span><span class="sxs-lookup"><span data-stu-id="30790-104">Offloading processing to a database server can cause it to spend a significant proportion of time running code, rather than responding to requests to store and retrieve data.</span></span>

## <a name="problem-description"></a><span data-ttu-id="30790-105">Descrizione del problema</span><span class="sxs-lookup"><span data-stu-id="30790-105">Problem description</span></span>

<span data-ttu-id="30790-106">Molti sistemi di database possono eseguire il codice.</span><span class="sxs-lookup"><span data-stu-id="30790-106">Many database systems can run code.</span></span> <span data-ttu-id="30790-107">Alcuni esempi comprendono stored procedure e trigger.</span><span class="sxs-lookup"><span data-stu-id="30790-107">Examples include stored procedures and triggers.</span></span> <span data-ttu-id="30790-108">Spesso, è più efficiente eseguire questa elaborazione vicino ai dati, invece che trasmettere i dati a un'applicazione del client per l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="30790-108">Often, it's more efficient to perform this processing close to the data, rather than transmitting the data to a client application for processing.</span></span> <span data-ttu-id="30790-109">Tuttavia, un uso eccessivo di queste funzionalità può danneggiare le prestazioni, per diversi motivi:</span><span class="sxs-lookup"><span data-stu-id="30790-109">However, overusing these features can hurt performance, for several reasons:</span></span>

- <span data-ttu-id="30790-110">Il server del database può impiegare troppo tempo per l'elaborazione, invece che accettare nuove richieste dei client e recuperare i dati.</span><span class="sxs-lookup"><span data-stu-id="30790-110">The database server may spend too much time processing, rather than accepting new client requests and fetching data.</span></span>
- <span data-ttu-id="30790-111">Un database è in genere una risorsa condivisa, pertanto può diventare un collo di bottiglia durante i periodi di utilizzo elevato.</span><span class="sxs-lookup"><span data-stu-id="30790-111">A database is usually a shared resource, so it can become a bottleneck during periods of high use.</span></span>
- <span data-ttu-id="30790-112">I costi di runtime possono essere eccessivi se l'archivio dati è a consumo.</span><span class="sxs-lookup"><span data-stu-id="30790-112">Runtime costs may be excessive if the data store is metered.</span></span> <span data-ttu-id="30790-113">Ciò vale in particolar modo per i servizi dei database gestiti.</span><span class="sxs-lookup"><span data-stu-id="30790-113">That's particularly true of managed database services.</span></span> <span data-ttu-id="30790-114">Ad esempio, il Database SQL di Azure ha costi per [Unità di transazione di database][dtu] (DTU).</span><span class="sxs-lookup"><span data-stu-id="30790-114">For example, Azure SQL Database charges for [Database Transaction Units][dtu] (DTUs).</span></span>
- <span data-ttu-id="30790-115">I database hanno una capacità limitata per la scalabilità verticale e non è facile ridimensionare orizzontalmente un database.</span><span class="sxs-lookup"><span data-stu-id="30790-115">Databases have finite capacity to scale up, and it's not trivial to scale a database horizontally.</span></span> <span data-ttu-id="30790-116">Di conseguenza, potrebbe essere consigliabile spostare l'elaborazione in una risorsa di calcolo, ad esempio un'app della macchina virtuale o del servizio app, che può essere facilmente ridimensionata orizzontalmente.</span><span class="sxs-lookup"><span data-stu-id="30790-116">Therefore, it may be better to move processing into a compute resource, such as a VM or App Service app, that can easily scale out.</span></span>

<span data-ttu-id="30790-117">In genere questo antipattern si verifica perché:</span><span class="sxs-lookup"><span data-stu-id="30790-117">This antipattern typically occurs because:</span></span>

- <span data-ttu-id="30790-118">Il database viene visualizzato come un servizio piuttosto che come un repository.</span><span class="sxs-lookup"><span data-stu-id="30790-118">The database is viewed as a service rather than a repository.</span></span> <span data-ttu-id="30790-119">Un'applicazione può usare il server del database per formattare i dati (ad esempio, convertendoli in XML), per modificare i dati di tipo stringa o per eseguire calcoli complessi.</span><span class="sxs-lookup"><span data-stu-id="30790-119">An application might use the database server to format data (for example, converting to XML), manipulate string data, or perform complex calculations.</span></span>
- <span data-ttu-id="30790-120">Gli sviluppatori provano a scrivere query i cui risultati possano essere mostrati direttamente agli utenti.</span><span class="sxs-lookup"><span data-stu-id="30790-120">Developers try to write queries whose results can be displayed directly to users.</span></span> <span data-ttu-id="30790-121">Ad esempio una query potrebbe combinare campi, o date, ore e valuta del formato in base alle impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="30790-121">For example a query might combine fields, or format dates, times, and currency according to locale.</span></span>
- <span data-ttu-id="30790-122">Gli sviluppatori stanno cercando di correggere l'antipattern di [recupero estraneo][ExtraneousFetching] trasferendo i calcoli al database.</span><span class="sxs-lookup"><span data-stu-id="30790-122">Developers are trying to correct the [Extraneous Fetching][ExtraneousFetching] antipattern by pushing computations to the database.</span></span>
- <span data-ttu-id="30790-123">Stored procedure vengono usate per incapsulare la logica di business, probabilmente perché sono considerate più facili da gestire e aggiornare.</span><span class="sxs-lookup"><span data-stu-id="30790-123">Stored procedures are used to encapsulate business logic, perhaps because they are considered easier to maintain and update.</span></span>

<span data-ttu-id="30790-124">Nell'esempio seguente si recuperano i 20 ordini di maggior valore per un'area di vendita specifica e formatta i risultati nel formato XML.</span><span class="sxs-lookup"><span data-stu-id="30790-124">The following example retrieves the 20 most valuable orders for a specified sales territory and formats the results as XML.</span></span>
<span data-ttu-id="30790-125">Usa le funzioni Transact-SQL per analizzare i dati e convertire i risultati in formato XML.</span><span class="sxs-lookup"><span data-stu-id="30790-125">It uses Transact-SQL functions to parse the data and convert the results to XML.</span></span> <span data-ttu-id="30790-126">L'esempio completo è disponibile [qui][sample-app].</span><span class="sxs-lookup"><span data-stu-id="30790-126">You can find the complete sample [here][sample-app].</span></span>

```SQL
SELECT TOP 20
  soh.[SalesOrderNumber]  AS '@OrderNumber',
  soh.[Status]            AS '@Status',
  soh.[ShipDate]          AS '@ShipDate',
  YEAR(soh.[OrderDate])   AS '@OrderDateYear',
  MONTH(soh.[OrderDate])  AS '@OrderDateMonth',
  soh.[DueDate]           AS '@DueDate',
  FORMAT(ROUND(soh.[SubTotal],2),'C')
                          AS '@SubTotal',
  FORMAT(ROUND(soh.[TaxAmt],2),'C')
                          AS '@TaxAmt',
  FORMAT(ROUND(soh.[TotalDue],2),'C')
                          AS '@TotalDue',
  CASE WHEN soh.[TotalDue] > 5000 THEN 'Y' ELSE 'N' END
                          AS '@ReviewRequired',
  (
  SELECT
    c.[AccountNumber]     AS '@AccountNumber',
    UPPER(LTRIM(RTRIM(REPLACE(
    CONCAT( p.[Title], ' ', p.[FirstName], ' ', p.[MiddleName], ' ', p.[LastName], ' ', p.[Suffix]),
    '  ', ' '))))         AS '@FullName'
  FROM [Sales].[Customer] c
    INNER JOIN [Person].[Person] p
  ON c.[PersonID] = p.[BusinessEntityID]
  WHERE c.[CustomerID] = soh.[CustomerID]
  FOR XML PATH ('Customer'), TYPE
  ),

  (
  SELECT
    sod.[OrderQty]      AS '@Quantity',
    FORMAT(sod.[UnitPrice],'C')
                        AS '@UnitPrice',
    FORMAT(ROUND(sod.[LineTotal],2),'C')
                        AS '@LineTotal',
    sod.[ProductID]     AS '@ProductId',
    CASE WHEN (sod.[ProductID] >= 710) AND (sod.[ProductID] <= 720) AND (sod.[OrderQty] >= 5) THEN 'Y' ELSE 'N' END
                        AS '@InventoryCheckRequired'

  FROM [Sales].[SalesOrderDetail] sod
  WHERE sod.[SalesOrderID] = soh.[SalesOrderID]
  ORDER BY sod.[SalesOrderDetailID]
  FOR XML PATH ('LineItem'), TYPE, ROOT('OrderLineItems')
  )

FROM [Sales].[SalesOrderHeader] soh
WHERE soh.[TerritoryId] = @TerritoryId
ORDER BY soh.[TotalDue] DESC
FOR XML PATH ('Order'), ROOT('Orders')
```

<span data-ttu-id="30790-127">Chiaramente, si tratta di query complesse.</span><span class="sxs-lookup"><span data-stu-id="30790-127">Clearly, this is complex query.</span></span> <span data-ttu-id="30790-128">Come verrà visualizzato in un secondo momento, si passerà a usare le risorse di elaborazione significative nel server del database.</span><span class="sxs-lookup"><span data-stu-id="30790-128">As we'll see later, it turns out to use significant processing resources on the database server.</span></span>

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="30790-129">Come risolvere il problema</span><span class="sxs-lookup"><span data-stu-id="30790-129">How to fix the problem</span></span>

<span data-ttu-id="30790-130">Spostare l'elaborazione dal server del database ad altri livelli di applicazione.</span><span class="sxs-lookup"><span data-stu-id="30790-130">Move processing from the database server into other application tiers.</span></span> <span data-ttu-id="30790-131">Idealmente, è necessario limitare il database all'esecuzione delle operazioni di accesso ai dati, usando solo le funzionalità per cui il database è ottimizzato, ad esempio l'aggregazione in un sistema RDBMS.</span><span class="sxs-lookup"><span data-stu-id="30790-131">Ideally, you should limit the database to performing data access operations, using only the capabilities that the database is optimized for, such as aggregation in an RDBMS.</span></span>

<span data-ttu-id="30790-132">Ad esempio, il codice Transact-SQL precedente può essere sostituito con un'istruzione che recupera semplicemente i dati da elaborare.</span><span class="sxs-lookup"><span data-stu-id="30790-132">For example, the previous Transact-SQL code can be replaced with a statement that simply retrieves the data to be processed.</span></span>

```SQL
SELECT
soh.[SalesOrderNumber]  AS [OrderNumber],
soh.[Status]            AS [Status],
soh.[OrderDate]         AS [OrderDate],
soh.[DueDate]           AS [DueDate],
soh.[ShipDate]          AS [ShipDate],
soh.[SubTotal]          AS [SubTotal],
soh.[TaxAmt]            AS [TaxAmt],
soh.[TotalDue]          AS [TotalDue],
c.[AccountNumber]       AS [AccountNumber],
p.[Title]               AS [CustomerTitle],
p.[FirstName]           AS [CustomerFirstName],
p.[MiddleName]          AS [CustomerMiddleName],
p.[LastName]            AS [CustomerLastName],
p.[Suffix]              AS [CustomerSuffix],
sod.[OrderQty]          AS [Quantity],
sod.[UnitPrice]         AS [UnitPrice],
sod.[LineTotal]         AS [LineTotal],
sod.[ProductID]         AS [ProductId]
FROM [Sales].[SalesOrderHeader] soh
INNER JOIN [Sales].[Customer] c ON soh.[CustomerID] = c.[CustomerID]
INNER JOIN [Person].[Person] p ON c.[PersonID] = p.[BusinessEntityID]
INNER JOIN [Sales].[SalesOrderDetail] sod ON soh.[SalesOrderID] = sod.[SalesOrderID]
WHERE soh.[TerritoryId] = @TerritoryId
AND soh.[SalesOrderId] IN (
    SELECT TOP 20 SalesOrderId
    FROM [Sales].[SalesOrderHeader] soh
    WHERE soh.[TerritoryId] = @TerritoryId
    ORDER BY soh.[TotalDue] DESC)
ORDER BY soh.[TotalDue] DESC, sod.[SalesOrderDetailID]
```

<span data-ttu-id="30790-133">L'applicazione usa quindi le API di .NET Framework `System.Xml.Linq` per formattare i risultati in formato XML.</span><span class="sxs-lookup"><span data-stu-id="30790-133">The application then uses the .NET Framework `System.Xml.Linq` APIs to format the results as XML.</span></span>

```csharp
// Create a new SqlCommand to run the Transact-SQL query
using (var command = new SqlCommand(...))
{
    command.Parameters.AddWithValue("@TerritoryId", id);

    // Run the query and create the initial XML document
    using (var reader = await command.ExecuteReaderAsync())
    {
        var lastOrderNumber = string.Empty;
        var doc = new XDocument();
        var orders = new XElement("Orders");
        doc.Add(orders);

        XElement lineItems = null;
        // Fetch each row in turn, format the results as XML, and add them to the XML document
        while (await reader.ReadAsync())
        {
            var orderNumber = reader["OrderNumber"].ToString();
            if (orderNumber != lastOrderNumber)
            {
                lastOrderNumber = orderNumber;

                var order = new XElement("Order");
                orders.Add(order);
                var customer = new XElement("Customer");
                lineItems = new XElement("OrderLineItems");
                order.Add(customer, lineItems);

                var orderDate = (DateTime)reader["OrderDate"];
                var totalDue = (Decimal)reader["TotalDue"];
                var reviewRequired = totalDue > 5000 ? 'Y' : 'N';

                order.Add(
                    new XAttribute("OrderNumber", orderNumber),
                    new XAttribute("Status", reader["Status"]),
                    new XAttribute("ShipDate", reader["ShipDate"]),
                    ... // More attributes, not shown.

                    var fullName = string.Join(" ",
                        reader["CustomerTitle"],
                        reader["CustomerFirstName"],
                        reader["CustomerMiddleName"],
                        reader["CustomerLastName"],
                        reader["CustomerSuffix"]
                    )
                   .Replace("  ", " ") //remove double spaces
                   .Trim()
                   .ToUpper();

               customer.Add(
                    new XAttribute("AccountNumber", reader["AccountNumber"]),
                    new XAttribute("FullName", fullName));
            }

            var productId = (int)reader["ProductID"];
            var quantity = (short)reader["Quantity"];
            var inventoryCheckRequired = (productId >= 710 && productId <= 720 && quantity >= 5) ? 'Y' : 'N';

            lineItems.Add(
                new XElement("LineItem",
                    new XAttribute("Quantity", quantity),
                    new XAttribute("UnitPrice", ((Decimal)reader["UnitPrice"]).ToString("C")),
                    new XAttribute("LineTotal", RoundAndFormat(reader["LineTotal"])),
                    new XAttribute("ProductId", productId),
                    new XAttribute("InventoryCheckRequired", inventoryCheckRequired)
                ));
        }
        // Match the exact formatting of the XML returned from SQL
        var xml = doc
            .ToString(SaveOptions.DisableFormatting)
            .Replace(" />", "/>");
    }
}
```

> [!NOTE]
> <span data-ttu-id="30790-134">Questo codice è alquanto complesso.</span><span class="sxs-lookup"><span data-stu-id="30790-134">This code is somewhat complex.</span></span> <span data-ttu-id="30790-135">Per una nuova applicazione, è preferibile usare una libreria di serializzazione.</span><span class="sxs-lookup"><span data-stu-id="30790-135">For a new application, you might prefer to use a serialization library.</span></span> <span data-ttu-id="30790-136">Tuttavia, il presupposto è che il team di sviluppo esegua il refactoring di un'applicazione esistente, pertanto il metodo deve restituire lo stesso formato del codice originale.</span><span class="sxs-lookup"><span data-stu-id="30790-136">However, the assumption here is that the development team is refactoring an existing application, so the method needs to return the exact same format as the original code.</span></span>

## <a name="considerations"></a><span data-ttu-id="30790-137">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="30790-137">Considerations</span></span>

- <span data-ttu-id="30790-138">Molti sistemi di database sono altamente ottimizzati per eseguire determinati tipi di elaborazione dei dati, ad esempio calcolando i valori di aggregazione in grandi set di dati.</span><span class="sxs-lookup"><span data-stu-id="30790-138">Many database systems are highly optimized to perform certain types of data processing, such as calculating aggregate values over large datasets.</span></span> <span data-ttu-id="30790-139">Non spostare questi tipi di elaborazione fuori dal database.</span><span class="sxs-lookup"><span data-stu-id="30790-139">Don't move those types of processing out of the database.</span></span>

- <span data-ttu-id="30790-140">Evitare di spostare l'elaborazione se ciò porta il database a trasferire molti più dati attraverso la rete.</span><span class="sxs-lookup"><span data-stu-id="30790-140">Do not relocate processing if doing so causes the database to transfer far more data over the network.</span></span> <span data-ttu-id="30790-141">Vedere l'[antipattern di recupero estraneo][ExtraneousFetching].</span><span class="sxs-lookup"><span data-stu-id="30790-141">See the [Extraneous Fetching antipattern][ExtraneousFetching].</span></span>

- <span data-ttu-id="30790-142">Se si sposta l'elaborazione in un livello di applicazione, potrebbe essere necessario scalare orizzontalmente tale livello per gestire il lavoro aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="30790-142">If you move processing to an application tier, that tier may need to scale out to handle the additional work.</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="30790-143">Come rilevare il problema</span><span class="sxs-lookup"><span data-stu-id="30790-143">How to detect the problem</span></span>

<span data-ttu-id="30790-144">I sintomi di un database occupato comprendono una diminuzione sproporzionata dei tempi di risposta e della velocità effettiva nelle operazioni che accedono al database.</span><span class="sxs-lookup"><span data-stu-id="30790-144">Symptoms of a busy database include a disproportionate decline in throughput and response times in operations that access the database.</span></span>

<span data-ttu-id="30790-145">Per provare a identificare questo problema è possibile eseguire la procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="30790-145">You can perform the following steps to help identify this problem:</span></span>

1. <span data-ttu-id="30790-146">Usare il monitoraggio delle prestazioni per identificare quanto tempo impiega il sistema di produzione per eseguire l'attività del database.</span><span class="sxs-lookup"><span data-stu-id="30790-146">Use performance monitoring to identify how much time the production system spends performing database activity.</span></span>

2. <span data-ttu-id="30790-147">Esaminare il lavoro eseguito dal database durante tali periodi.</span><span class="sxs-lookup"><span data-stu-id="30790-147">Examine the work performed by the database during these periods.</span></span>

3. <span data-ttu-id="30790-148">Se si ritiene che delle operazioni particolari potrebbero portare una quantità eccessiva attività nel database, eseguire un test di carico in un ambiente controllato.</span><span class="sxs-lookup"><span data-stu-id="30790-148">If you suspect that particular operations might cause too much database activity, perform load testing in a controlled environment.</span></span> <span data-ttu-id="30790-149">Ogni test deve eseguire una combinazione delle operazioni sospette con un carico utente variabile.</span><span class="sxs-lookup"><span data-stu-id="30790-149">Each test should run a mixture of the suspect operations with a variable user load.</span></span> <span data-ttu-id="30790-150">Esaminare i dati di telemetria dal test di carico per osservare come viene usato il database.</span><span class="sxs-lookup"><span data-stu-id="30790-150">Examine the telemetry from the load tests to observe how the database is used.</span></span>

4. <span data-ttu-id="30790-151">Se l'attività del database rivela un'elaborazione significativa ma un traffico di dati minimo, esaminare il codice sorgente per determinare se è possibile ottimizzare l'elaborazione eseguita altrove.</span><span class="sxs-lookup"><span data-stu-id="30790-151">If the database activity reveals significant processing but little data traffic, review the source code to determine whether the processing can better be performed elsewhere.</span></span>

<span data-ttu-id="30790-152">Se il volume dell'attività del database è insufficiente o i tempi di risposta sono relativamente veloci, allora un database occupato non è dovuto a un problema nelle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="30790-152">If the volume of database activity is low or response times are relatively fast, then a busy database is unlikely to be a performance problem.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="30790-153">Diagnosi di esempio</span><span class="sxs-lookup"><span data-stu-id="30790-153">Example diagnosis</span></span>

<span data-ttu-id="30790-154">Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.</span><span class="sxs-lookup"><span data-stu-id="30790-154">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="monitor-the-volume-of-database-activity"></a><span data-ttu-id="30790-155">Monitorare il volume dell'attività del database</span><span class="sxs-lookup"><span data-stu-id="30790-155">Monitor the volume of database activity</span></span>

<span data-ttu-id="30790-156">Il grafico seguente mostra i risultati dell'esecuzione di un test di carico rispetto all'applicazione di esempio, usando un carico del passaggio per un massimo di 50 utenti simultanei.</span><span class="sxs-lookup"><span data-stu-id="30790-156">The following graph shows the results of running a load test against the sample application, using a step load of up to 50 concurrent users.</span></span> <span data-ttu-id="30790-157">Il volume di richieste raggiunge rapidamente un limite e rimane su quel livello, mentre il tempo medio di risposta viene incrementato costantemente.</span><span class="sxs-lookup"><span data-stu-id="30790-157">The volume of requests quickly reaches a limit and stays at that level, while the average response time steadily increases.</span></span> <span data-ttu-id="30790-158">Si noti che per queste due metriche viene usata una scala logaritmica.</span><span class="sxs-lookup"><span data-stu-id="30790-158">Note that a logarithmic scale is used for those two metrics.</span></span>

![Risultati del test di carico per eseguire l'elaborazione nel database][ProcessingInDatabaseLoadTest]

<span data-ttu-id="30790-160">Il grafico successivo mostra l'utilizzo della CPU e delle DTU come percentuale delle quote del servizio.</span><span class="sxs-lookup"><span data-stu-id="30790-160">The next graph shows CPU utilization and DTUs as a percentage of service quota.</span></span> <span data-ttu-id="30790-161">Le unità di trasmissione dati forniscono una misura delle prestazioni dell'elaborazione del database.</span><span class="sxs-lookup"><span data-stu-id="30790-161">DTUs provides a measure of how much processing the database performs.</span></span> <span data-ttu-id="30790-162">Il grafico mostra che sia l'uso della CPU sia quello della DTU raggiungono rapidamente il 100%.</span><span class="sxs-lookup"><span data-stu-id="30790-162">The graph shows that CPU and DTU utilization both quickly reached 100%.</span></span>

![Monitoraggio del Database SQL Azure, che mostra le prestazioni del database durante l'esecuzione dell'elaborazione][ProcessingInDatabaseMonitor]

### <a name="examine-the-work-performed-by-the-database"></a><span data-ttu-id="30790-164">Esaminare il lavoro eseguito dal database</span><span class="sxs-lookup"><span data-stu-id="30790-164">Examine the work performed by the database</span></span>

<span data-ttu-id="30790-165">È possibile che le attività eseguite dal database siano vere operazioni di accesso ai dati, piuttosto che elaborazioni, pertanto è importante comprendere le istruzioni SQL in esecuzione mentre il database è occupato.</span><span class="sxs-lookup"><span data-stu-id="30790-165">It could be that the tasks performed by the database are genuine data access operations, rather than processing, so it is important to understand the SQL statements being run while the database is busy.</span></span> <span data-ttu-id="30790-166">Monitorare il sistema per acquisire il traffico SQL e correlare le operazioni SQL con le richieste dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="30790-166">Monitor the system to capture the SQL traffic and correlate the SQL operations with application requests.</span></span>

<span data-ttu-id="30790-167">Se le operazioni del database sono esclusivamente operazioni di accesso ai dati, senza numerose attività di elaborazione, allora il problema potrebbe essere il [Recupero estraneo][ExtraneousFetching].</span><span class="sxs-lookup"><span data-stu-id="30790-167">If the database operations are purely data access operations, without a lot of processing, then the problem might be [Extraneous Fetching][ExtraneousFetching].</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="30790-168">Implementare la soluzione e verificare il risultato</span><span class="sxs-lookup"><span data-stu-id="30790-168">Implement the solution and verify the result</span></span>

<span data-ttu-id="30790-169">Il grafico seguente mostra un test di carico tramite il codice aggiornato.</span><span class="sxs-lookup"><span data-stu-id="30790-169">The following graph shows a load test using the updated code.</span></span> <span data-ttu-id="30790-170">La velocità effettiva è significativamente più elevata, oltre 400 richieste al secondo rispetto alle 12 precedenti.</span><span class="sxs-lookup"><span data-stu-id="30790-170">Throughput is significantly higher, over 400 requests per second versus 12 earlier.</span></span> <span data-ttu-id="30790-171">Il tempo di risposta medio è di molto inferiore, appena sopra gli 0,1 secondi rispetto ai più di 4 secondi.</span><span class="sxs-lookup"><span data-stu-id="30790-171">The average response time is also much lower, just above 0.1 seconds compared to over 4 seconds.</span></span>

![Risultati del test di carico per eseguire l'elaborazione nel database][ProcessingInClientApplicationLoadTest]

<span data-ttu-id="30790-173">L'utilizzo della CPU e della DTU mostra che il sistema ha impiegato più tempo per raggiungere la saturazione, nonostante l'aumento della velocità.</span><span class="sxs-lookup"><span data-stu-id="30790-173">CPU and DTU utilization shows that the system took longer to reach saturation, despite the increased throughput.</span></span>

![Monitoraggio del Database SQL Azure, che mostra le prestazioni del database durante l'esecuzione dell'elaborazione nell'applicazione del client][ProcessingInClientApplicationMonitor]

## <a name="related-resources"></a><span data-ttu-id="30790-175">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="30790-175">Related resources</span></span>

- <span data-ttu-id="30790-176">[Antipattern di recupero estraneo][ExtraneousFetching]</span><span class="sxs-lookup"><span data-stu-id="30790-176">[Extraneous Fetching antipattern][ExtraneousFetching]</span></span>

[dtu]: /azure/sql-database/sql-database-service-tiers-dtu
[ExtraneousFetching]: ../extraneous-fetching/index.md
[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/BusyDatabase

[ProcessingInDatabaseLoadTest]: ./_images/ProcessingInDatabaseLoadTest.jpg
[ProcessingInClientApplicationLoadTest]: ./_images/ProcessingInClientApplicationLoadTest.jpg
[ProcessingInDatabaseMonitor]: ./_images/ProcessingInDatabaseMonitor.jpg
[ProcessingInClientApplicationMonitor]: ./_images/ProcessingInClientApplicationMonitor.jpg
