---
title: Antipattern I/O sincrono
description: Bloccare il thread chiamante durante il completamento dell'I/O può ridurre le prestazioni e influire sulla scalabilità verticale.
author: dragon119
ms.date: 06/05/2017
ms.openlocfilehash: 961eacb82344ec7e71aaa96fb4cd8bc530721e96
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47429010"
---
# <a name="synchronous-io-antipattern"></a>Antipattern I/O sincrono

Bloccare il thread chiamante durante il completamento dell'I/O può ridurre le prestazioni e influire sulla scalabilità verticale.

## <a name="problem-description"></a>Descrizione del problema

Un'operazione I/O sincrona blocca il thread chiamante durante il completamento dell'I/O. Il thread chiamante entra in uno stato di attesa e non è in grado di eseguire operazioni utili durante questo intervallo: un inutile consumo di risorse di elaborazione.

Alcuni esempi comuni sono:

- Recupero o archiviazione persistente di dati in un database o qualsiasi tipo di archivio permanente.
- Invio di una richiesta a un servizio Web.
- Inserimento di un messaggio o recupero di un messaggio da una coda.
- Scrittura o lettura in un file locale.

In genere questo antipattern si verifica perché:

- Sembra essere il modo più intuitivo per eseguire un'operazione. 
- L'applicazione richiede una risposta a una richiesta.
- L'applicazione usa una libreria che fornisce solo metodi sincroni per l'I/O. 
- Una libreria esterna esegue operazioni di I/O sincrone internamente. Una singola chiamata di I/O sincrona può bloccare un'intera sequenza di chiamate.

Il codice seguente carica un file nell'archiviazione BLOB di Azure. Esistono due posizioni in cui il codice blocca l'attesa per l'I/O sincrono: il metodo `CreateIfNotExists` e il metodo `UploadFromStream`.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference("uploadedfiles");

container.CreateIfNotExists();
var blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = File.OpenRead(HostingEnvironment.MapPath("~/FileToUpload.txt")))
{
    blockBlob.UploadFromStream(fileStream);
}
```

Di seguito è riportato un esempio di attesa di una risposta da un servizio esterno. Il metodo `GetUserProfile` chiama un servizio remoto che restituisce un `UserProfile`.

```csharp
public interface IUserProfileService
{
    UserProfile GetUserProfile();
}

public class SyncController : ApiController
{
    private readonly IUserProfileService _userProfileService;

    public SyncController()
    {
        _userProfileService = new FakeUserProfileService();
    }

    // This is a synchronous method that calls the synchronous GetUserProfile method.
    public UserProfile GetUserProfile()
    {
        return _userProfileService.GetUserProfile();
    }
}
```

È possibile trovare il codice completo per entrambi gli esempi [qui][sample-app].

## <a name="how-to-fix-the-problem"></a>Come risolvere il problema

Sostituire operazioni di I/O sincrone con operazioni asincrone. In questo modo il thread corrente si libera e può continuare a eseguire lavoro significativo anziché bloccarsi, inoltre può migliorare l'utilizzo delle risorse di calcolo. Eseguire l'I/O in modo asincrono è particolarmente efficace per la gestione di un carico imprevisto nelle richieste dalle applicazioni client. 

Molte librerie forniscono versioni sincrone e asincrone dei metodi. Se possibile, utilizzare le versioni asincrone. Di seguito è riportata la versione asincrona dell'esempio precedente che consente di caricare un file nell'archiviazione BLOB di Azure.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference("uploadedfiles");

await container.CreateIfNotExistsAsync();

var blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = File.OpenRead(HostingEnvironment.MapPath("~/FileToUpload.txt")))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

L'operatore `await` restituisce il controllo all'ambiente chiamante mentre viene eseguita l'operazione asincrona. Il copdice che segue questa istruzione funziona come una continuazione che viene eseguita quando l'operazione asincrona è stata completata.

Un servizio ben progettato deve fornire anche le operazioni asincrone. Di seguito è riportata una versione asincrona del servizio Web che restituisce profili utente. Il metodo `GetUserProfileAsync` dipende dalla presenza di una versione asincrona del servizio User Profile.

```csharp
public interface IUserProfileService
{
    Task<UserProfile> GetUserProfileAsync();
}

public class AsyncController : ApiController
{
    private readonly IUserProfileService _userProfileService;

    public AsyncController()
    {
        _userProfileService = new FakeUserProfileService();
    }

    // This is an synchronous method that calls the Task based GetUserProfileAsync method.
    public Task<UserProfile> GetUserProfileAsync()
    {
        return _userProfileService.GetUserProfileAsync();
    }
}
```

Per le librerie che non forniscono versioni asincrone delle operazioni, è possibile creare wrapper asincroni intorno a determinati metodi sincroni. Seguire questo approccio con cautela. Anche se potrebbe migliorare la velocità di risposta sul thread che richiama il wrapper asincrono, in realtà utilizza più risorse. Potrebbe essere creato un thread aggiuntivo e sincronizzare il lavoro svolto da questo thread comporta un sovraccarico. Alcuni svantaggi sono descritti in questo post di blog: [Should I expose asynchronous wrappers for synchronous methods?][async-wrappers] (È necessario esporre wrapper asincroni per i metodi sincroni)

Di seguito è riportato un esempio di un wrapper asincrono per un metodo sincrono.

```csharp
// Asynchronous wrapper around synchronous library method
private async Task<int> LibraryIOOperationAsync()
{
    return await Task.Run(() => LibraryIOOperation());
}
```

Il codice chiamante può ora attendere sul wrapper:

```csharp
// Invoke the asynchronous wrapper using a task
await LibraryIOOperationAsync();
```

## <a name="considerations"></a>Considerazioni

- Le operazioni di I/O per cui si prevede una durata molto breve e che hanno una bassa probabilità di provocare contesa potrebbero essere più efficienti come operazioni sincrone. Un esempio potrebbe essere la lettura di piccoli file in un'unità SSD. L'overhead di invio di un'attività a un altro thread e la sincronizzazione con tale thread al completamento dell'attività, potrebbe superare i vantaggi dell'I/O asincrono. Tuttavia questi casi sono relativamente rari e la maggior parte delle operazioni di I/O devono essere eseguite in modo asincrono.

- Il miglioramento delle prestazioni di I/O può causare colli di bottiglia in altre parti del sistema. Ad esempio, lo sblocco dei thread potrebbe comportare un volume maggiore di richieste simultanee a risorse condivise, che porta a sua volta all'esaurimento delle risorse o alla limitazione delle richieste. Nel caso di un problema, potrebbe essere necessario scalare orizzontalmente il numero di server Web o di archivi dati delle partizionare per ridurre la contesa.

## <a name="how-to-detect-the-problem"></a>Come rilevare il problema

Per gli utenti, l'applicazione potrebbe sembrare che non risponda o bloccarsi periodicamente. L'applicazione potrebbe bloccarsi con eccezioni di timeout. È possibile che vengano restituiti anche errori HTTP 500 (Server interno). Nel server, le richieste client in ingresso potrebbero essere bloccate fino a quando non diventa disponibile un thread, causando una lunghezza eccessiva della coda di richieste, che si manifesta sotto forma di errori HTTP 503 (servizio non disponibile).

È possibile eseguire la procedura seguente per identificare il problema:

1. Monitorare il sistema di produzione e determinare se i thread di lavoro bloccati stanno vincolando la velocità effettiva.

2. Se le richieste sono bloccate a causa di mancanza di thread, esaminare l'applicazione per determinare quali sono le operazioni che in quel momento potrebbero eseguire l'I/O in modo sincrono.

3. Eseguire test di carico controllato di ogni operazione che sta eseguendo L'I/O sincrono, per sapere se queste operazioni influiscono sulle prestazioni del sistema.

## <a name="example-diagnosis"></a>Diagnosi di esempio

Le sezioni seguenti applicano questa procedura all'applicazione di esempio descritta in precedenza.

### <a name="monitor-web-server-performance"></a>Monitorare le prestazioni del server Web

Per le applicazioni Web e i ruoli Web di Azure, è importante monitorare le prestazioni del server Web IIS. In particolare occorre prestare attenzione alla lunghezza della coda di richieste per stabilire se le richieste vengono bloccate in attesa di thread disponibili durante i periodi di intensa attività. È possibile raccogliere queste informazioni abilitando la diagnostica di Azure. Per altre informazioni, vedere:

- [Eseguire il monitoraggio delle app nel servizio app di Azure][web-sites-monitor]
- [Creare e usare contatori di prestazioni in un'applicazione Azure][performance-counters]

Instrumentare l'applicazione per vedere come le richieste vengono gestite dopo che sono state accettate. Tracciare il flusso di una richiesta può essere utili per identificare se sta eseguendo chiamate a esecuzione lenta e sta bloccando il thread corrente. Anche la profilatura di thread può evidenziare richieste che vengono bloccate.

### <a name="load-test-the-application"></a>Testare il carico dell'applicazione

Il grafico seguente mostra le prestazioni del metodo `GetUserProfile` sincrono illustrato in precedenza, con vari carichi fino a 4000 utenti simultanei. L'applicazione è un'applicazione ASP.NET eseguita in un ruolo Web servizio cloud di Azure.

![Grafico delle prestazioni per l'applicazione di esempio che esegue operazioni di I/O sincrone][sync-performance]

L'operazione sincrona prevede un'inattività di 2 secondi hardcoded, per simulare l'I/O sincrono, pertanto il tempo di risposta minimo è leggermente maggiore di 2 secondi. Quando il carico raggiunge circa 2500 utenti simultanei, il tempo di risposta medio diventa stabile, anche se il volume di richieste al secondo continua ad aumentare. Si noti che per queste due misure la scala è logaritmica. Il numero di richieste al secondo raddoppia tra il punto e la fine del test.

In isolamento, non sempre è chiaro da questo test se l'I/O sincrono sia un problema. In condizioni di carico pesante, l'applicazione può raggiungere un punto critico in cui il server Web non riesce più a elaborare le richieste in modo tempestivo, facendo in modo che le applicazioni client ricevano eccezioni di timeout.

Le richieste in ingresso vengono accodate dal server Web IIS e passate a un thread in esecuzione nel pool di thread ASP.NET. Poiché ogni operazione esegue l'I/O in modo sincrono, il thread è bloccato fino al completamento dell'operazione. Man mano che aumenta il carico di lavoro, infine tutti i thread ASP.NET nel pool di thread sono allocati e bloccati. A questo punto le altre richieste in arrivo devono attendere nella coda che un thread diventi disponibile. Man mano che aumenta la lunghezza della coda, le richieste iniziano ad andare in timeout.

### <a name="implement-the-solution-and-verify-the-result"></a>Implementare la soluzione e verificare il risultato

Il grafico successivo mostra i risultati dei test ti carico della versione asincrona del codice.

![Grafico delle prestazioni per l'applicazione di esempio che esegue operazioni di I/O asincrone][async-performance]

La velocità effettiva è molto superiore. Nella stessa durata del test precedente, il sistema gestisce correttamente una velocità effettiva quasi decuplicata, misurata in richieste al secondo. Inoltre, il tempo medio di risposta è relativamente costante e rimane circa 25 volte più piccolo rispetto al test precedente.


[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/SynchronousIO


[async-wrappers]: https://blogs.msdn.microsoft.com/pfxteam/2012/03/24/should-i-expose-asynchronous-wrappers-for-synchronous-methods/
[performance-counters]: /azure/cloud-services/cloud-services-dotnet-diagnostics-performance-counters
[web-sites-monitor]: /azure/app-service-web/web-sites-monitor

[sync-performance]: _images/SyncPerformance.jpg
[async-performance]: _images/AsyncPerformance.jpg



