---
title: Modello di archivio di configurazione esterno
titleSuffix: Cloud Design Patterns
description: È possibile estrarre le informazioni di configurazione dal pacchetto di distribuzione dell'applicazione e spostarle in una posizione centralizzata.
keywords: schema progettuale
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: fd006437aab934d951d0a0bc947d32878edbf9d8
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244402"
---
# <a name="external-configuration-store-pattern"></a>Modello di archivio di configurazione esterno

[!INCLUDE [header](../_includes/header.md)]

È possibile estrarre le informazioni di configurazione dal pacchetto di distribuzione dell'applicazione e spostarle in una posizione centralizzata. In questo modo può essere più facile gestire e controllare i dati di configurazione e si può avere l'opportunità di condividere i dati di configurazione tra applicazioni e istanze dell'applicazione.

## <a name="context-and-problem"></a>Contesto e problema

La maggior parte degli ambienti di runtime delle applicazioni includono informazioni di configurazione contenute in file distribuiti con l'applicazione. In alcuni casi è possibile modificare questi file per cambiare il comportamento dell'applicazione dopo che è stata distribuita. Tuttavia, se si modifica la configurazione sarà necessario ridistribuire l'applicazione e spesso questo causa tempi di inattività inaccettabili e altro sovraccarico amministrativo.

I file di configurazione locale limitano anche la configurazione a una sola applicazione, ma a volte può essere utile condividere le impostazioni di configurazione tra più applicazioni. Alcuni esempi sono le stringhe di connessione del database, le informazioni sui temi dell'interfaccia utente o gli URL delle code e delle risorse di archiviazione usate da un insieme di applicazioni correlate.

È difficile gestire le modifiche apportate alle configurazioni locale tra più istanze in esecuzione dell'applicazione, soprattutto in uno scenario ospitato nel cloud. Di conseguenza, è possibile avere istanze che usano impostazioni di configurazione diverse durante la distribuzione dell'aggiornamento.

Inoltre, gli aggiornamenti di applicazioni e componenti possono richiedere modifiche agli schemi di configurazione. Molti sistemi di configurazione non supportano versioni diverse delle informazioni di configurazione.

## <a name="solution"></a>Soluzione

Archiviare le informazioni di configurazione in un archivio esterno e rendere disponibile un'interfaccia che consenta di leggere e aggiornare le impostazioni di configurazione in modo rapido ed efficiente. Il tipo di archivio esterno dipende dall'ambiente di hosting e di runtime dell'applicazione. In uno scenario ospitato nel cloud si tratta in genere di un servizio di archiviazione basato su cloud, ma potrebbe essere un database ospitato o un altro sistema.

L'archivio di backup che si sceglie per le informazioni di configurazione deve avere un'interfaccia con accesso semplice e coerente. Le informazioni devono essere esposte in un formato correttamente tipizzato e strutturato. L'implementazione può anche richiedere l'autorizzazione dell'accesso da parte degli utenti per proteggere i dati di configurazione ed essere sufficientemente flessibile da consentire l'archiviazione di più versioni della configurazione, ad esempio sviluppo, gestione temporanea o produzione, ognuna con più versioni di rilascio.

> Molti sistemi di configurazione predefiniti leggono i dati all'avvio dell'applicazione e memorizzano i dati nella cache per consentire un accesso rapido e ridurre al minimo l'impatto sulle prestazioni dell'applicazione. In base al tipo di archivio di backup in uso e alla latenza di tale archivio, potrebbe essere utile implementare un meccanismo di memorizzazione nella cache all'interno dell'archivio di configurazione esterno. Per altre informazioni, vedere [Informazioni aggiuntive sulla memorizzazione nella cache](https://msdn.microsoft.com/library/dn589802.aspx). La figura illustra una panoramica del modello di archivio di configurazione esterno con cache locale facoltativa.

![Panoramica del modello di archivio di configurazione esterno con cache locale facoltativa](./_images/external-configuration-store-overview.png)

## <a name="issues-and-considerations"></a>Considerazioni e problemi

Prima di decidere come implementare questo modello, considerare quanto segue:

Scegliere un archivio di backup che offre prestazioni accettabili, elevata disponibilità, affidabilità e di cui si può eseguire il backup come parte del processo di manutenzione e amministrazione dell'applicazione. In un'applicazione ospitata nel cloud, l'uso di un meccanismo di archiviazione cloud è in genere una scelta ottimale per soddisfare questi requisiti.

Progettare lo schema dell'archivio di backup per garantire la flessibilità nei tipi di informazioni che può contenere. Verificare che soddisfi tutti i requisiti di configurazione, ad esempio dati tipizzati, raccolte di impostazioni, più versioni delle impostazioni e altre funzionalità richieste dalle applicazioni che lo usano. Lo schema deve essere facile da estendere per supportare impostazioni aggiuntive quando cambiano i requisiti.

Considerare le funzionalità fisiche dell'archivio di backup, la sua relazione con la modalità di archiviazione delle informazioni di configurazione e l'impatto sulle prestazioni. Ad esempio, l'archiviazione di un documento XML contenente informazioni di configurazione richiederà che l'interfaccia di configurazione o l'applicazione analizzi il documento in modo da leggere le singole impostazioni. In questo modo aggiornare un'impostazione diventerà più complicato, anche se memorizzare nella cache le impostazioni può compensare il rallentamento delle prestazioni di lettura.

Considerare il modo in cui l'interfaccia di configurazione consente il controllo dell'ambito e dell'ereditarietà delle impostazioni di configurazione. Ad esempio, potrebbe essere un requisito esaminare le impostazioni di configurazione a livello di organizzazione, applicazione e computer. L'operazione può essere necessaria per supportare la delega del controllo dell'accesso a diversi ambiti e per impedire o consentire alle singole applicazioni di ignorare le impostazioni.

Verificare che l'interfaccia di configurazione sia in grado di esporre i dati di configurazione nei formati richiesti, ad esempio valori tipizzati, raccolte, coppie chiave/valore o contenitori di proprietà.

Prendere in considerazione il comportamento dell'interfaccia dell'archivio di configurazione quando le impostazioni di contengono errori o non esistono nell'archivio di backup. Può essere appropriato restituire le impostazioni predefinite e registrare gli errori. Considerare anche aspetti come la distinzione tra maiuscole e minuscole per le chiavi o i nomi delle impostazioni di configurazione, l'archiviazione e la gestione dei dati binari e i modi in cui vengono gestiti i valori null o vuoti.

È necessario proteggere i dati di configurazione per consentire l'accesso solo alle applicazioni e agli utenti appropriati. Si tratta probabilmente di una funzionalità dell'interfaccia dell'archivio di configurazione, ma è anche necessario assicurarsi che non sia possibile accedere ai dati contenuti nell'archivio di backup senza l'autorizzazione appropriata. Verificare che esista una separazione rigorosa tra le autorizzazioni necessarie per leggere e scrivere i dati di configurazione. Considerare anche la necessità di crittografare alcune o tutte le impostazioni di configurazione e le relative modalità di implementazione nell'interfaccia dell'archivio di configurazione.

Le configurazioni archiviate centralmente, che modificano il comportamento dell'applicazione in fase di esecuzione, sono estremamente importanti e devono essere distribuite, aggiornate e gestite con gli stessi meccanismi usati per la distribuzione di codice dell'applicazione. Ad esempio, le modifiche che possono interessare più di un'applicazione devono essere apportate usando un approccio con test completi e distribuzione a fasi per garantire che le modifiche siano appropriate per tutte le applicazioni che usano questa configurazione. Se un amministratore modifica un'impostazione per aggiornare una sola applicazione, ciò potrebbe influire negativamente sulle altre applicazioni che usano la stessa impostazione.

Se un'applicazione memorizza nella cache le informazioni di configurazione, l'applicazione deve ricevere un avviso se la configurazione viene modificata. Può essere possibile implementare un criterio di scadenza sui dati di configurazione memorizzati nella cache in modo che periodicamente queste informazioni vengano automaticamente aggiornate e le eventuali modifiche vengano selezionate e gestite.

## <a name="when-to-use-this-pattern"></a>Quando usare questo modello

Questo modello è utile per:

- Impostazioni di configurazione che vengono condivise tra più applicazioni e istanze dell'applicazione o quando è necessario usare una configurazione standard in più applicazioni e istanze dell'applicazione.

- Un sistema di configurazione standard che non supporta tutte le impostazioni di configurazione necessarie, ad esempio l'archiviazione di immagini o tipi di dati complessi.

- Come archivio complementare per alcune delle impostazioni per le applicazioni, per consentire ad esempio alle applicazioni di ignorare alcune o tutte le impostazioni archiviate centralmente.

- Come un modo per semplificare l'amministrazione di più applicazioni e, facoltativamente, monitorare l'uso delle impostazioni di configurazione registrando alcuni o tutti i tipi di accesso all'archivio di configurazione.

## <a name="example"></a>Esempio

In un'applicazione ospitata in Microsoft Azure la scelta più comune per l'archiviazione esterna delle informazioni di configurazione è Archiviazione di Azure. Si tratta di una soluzione flessibile, che offre prestazioni elevate e viene replicata tre volte con failover automatico per assicurare una disponibilità elevata. Tabella di Azure offre un archivio di chiavi/valori con la possibilità di usare uno schema flessibile per i valori. L'archiviazione BLOB di Azure offre un archivio gerarchico basato su contenitore in grado di contenere qualsiasi tipo di dati in BLOB denominati singolarmente.

L'esempio seguente illustra come implementare un archivio di configurazione con l'archiviazione BLOB per archiviare ed esporre le informazioni di configurazione. La classe `BlobSettingsStore` astrae l'archiviazione BLOB in modo che contenga le informazioni di configurazione e implementa l'interfaccia `ISettingsStore` specificata nel codice seguente.

> Questo codice è riportato nel progetto _ExternalConfigurationStore.Cloud_ nella soluzione _ExternalConfigurationStore_, disponibile da [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/external-configuration-store).

```csharp
public interface ISettingsStore
{
    Task<string> GetVersionAsync();

    Task<Dictionary<string, string>> FindAllAsync();
}
```

Questa interfaccia definisce i metodi per il recupero e l'aggiornamento delle impostazioni di configurazione contenute nell'archivio di configurazione e include un numero di versione che può essere usato per rilevare se le impostazioni di configurazione sono state modificate di recente. La classe `BlobSettingsStore` usa la proprietà `ETag` del BLOB per implementare il controllo delle versioni. La proprietà `ETag` viene aggiornata automaticamente ogni volta che viene scritto il BLOB.

> Per impostazione predefinita, questa soluzione semplice espone tutte le impostazioni di configurazione come valori stringa anziché come valori tipizzati.

La classe `ExternalConfigurationManager` definisce un wrapper per un oggetto `BlobSettingsStore`. Un'applicazione può usare questa classe per archiviare e recuperare le informazioni di configurazione. La classe usa la libreria di [estensioni reattive](https://msdn.microsoft.com/library/hh242985.aspx) di Microsoft per esporre tutte le modifiche apportate alla configurazione attraverso un'implementazione dell'interfaccia di `IObservable`. Se un'impostazione viene modificata chiamando il metodo `SetAppSetting`, viene generato l'evento `Changed` e tutti i sottoscrittori dell'evento ricevono una notifica.

Si noti che tutte le impostazioni vengono anche memorizzate nella cache in un oggetto `Dictionary` all'interno della classe `ExternalConfigurationManager` per l'accesso rapido. Il metodo `GetSetting` usato per recuperare un'impostazione di configurazione legge i dati dalla cache. Se l'impostazione non viene individuata nella cache, viene recuperata dall'oggetto `BlobSettingsStore`.

Il metodo `GetSettings` richiama il metodo `CheckForConfigurationChanges` per verificare se le informazioni di configurazione presenti nell'archiviazione BLOB sono state modificate. Il numero di versione viene esaminato e confrontato con il numero di versione corrente contenuto nell'oggetto `ExternalConfigurationManager`. Se sono state apportate una o più modifiche, viene generato l'evento `Changed` e le impostazioni di configurazione memorizzate nella cache nell'oggetto `Dictionary` vengono aggiornate. Si tratta di un'applicazione del [modello cache-aside](./cache-aside.md).

L'esempio di codice che segue illustra come vengono implementati l'evento `Changed`, il metodo `GetSettings` e il metodo `CheckForConfigurationChanges`:

```csharp
public class ExternalConfigurationManager : IDisposable
{
  // An abstraction of the configuration store.
  private readonly ISettingsStore settings;
  private readonly ISubject<KeyValuePair<string, string>> changed;
  ...
  private readonly ReaderWriterLockSlim settingsCacheLock = new ReaderWriterLockSlim();
  private readonly SemaphoreSlim syncCacheSemaphore = new SemaphoreSlim(1);  
  ...
  private Dictionary<string, string> settingsCache;
  private string currentVersion;
  ...
  public ExternalConfigurationManager(ISettingsStore settings, ...)
  {
    this.settings = settings;
    ...
  }
  ...
  public IObservable<KeyValuePair<string, string>> Changed => this.changed.AsObservable();
  ...

  public string GetAppSetting(string key)
  {
    ...
    // Try to get the value from the settings cache.
    // If there's a cache miss, get the setting from the settings store and refresh the settings cache.

    string value;
    try
    {
        this.settingsCacheLock.EnterReadLock();

        this.settingsCache.TryGetValue(key, out value);
    }
    finally
    {
        this.settingsCacheLock.ExitReadLock();
    }

    return value;
  }
  ...
  private void CheckForConfigurationChanges()
  {
    try
    {
        // It is assumed that updates are infrequent.
        // To avoid race conditions in refreshing the cache, synchronize access to the in-memory cache.
        await this.syncCacheSemaphore.WaitAsync();

        var latestVersion = await this.settings.GetVersionAsync();

        // If the versions are the same, nothing has changed in the configuration.
        if (this.currentVersion == latestVersion) return;

        // Get the latest settings from the settings store and publish changes.
        var latestSettings = await this.settings.FindAllAsync();

        // Refresh the settings cache.
        try
        {
            this.settingsCacheLock.EnterWriteLock();

            if (this.settingsCache != null)
            {
                //Notify settings changed
                latestSettings.Except(this.settingsCache).ToList().ForEach(kv => this.changed.OnNext(kv));
            }
            this.settingsCache = latestSettings;
        }
        finally
        {
            this.settingsCacheLock.ExitWriteLock();
        }

        // Update the current version.
        this.currentVersion = latestVersion;
    }
    catch (Exception ex)
    {
        this.changed.OnError(ex);
    }
    finally
    {
        this.syncCacheSemaphore.Release();
    }
  }
}
```

> La classe `ExternalConfigurationManager` offre anche una proprietà denominata `Environment`. Questa proprietà supporta configurazioni diverse per un'applicazione in esecuzione in ambienti diversi, ad esempio gestione temporanea e produzione.

Un oggetto `ExternalConfigurationManager` può anche eseguire periodicamente una query sull'oggetto `BlobSettingsStore` per verificare le modifiche. Nel codice seguente, il metodo `StartMonitor` chiama `CheckForConfigurationChanges` a un intervallo specificato per rilevare le modifiche e generare l'evento `Changed`, come descritto in precedenza.

```csharp
public class ExternalConfigurationManager : IDisposable
{
  ...
  private readonly ISubject<KeyValuePair<string, string>> changed;
  private Dictionary<string, string> settingsCache;
  private readonly CancellationTokenSource cts = new CancellationTokenSource();
  private Task monitoringTask;
  private readonly TimeSpan interval;

  private readonly SemaphoreSlim timerSemaphore = new SemaphoreSlim(1);
  ...
  public ExternalConfigurationManager(string environment) : this(new BlobSettingsStore(environment), TimeSpan.FromSeconds(15), environment)
  {
  }
  
  public ExternalConfigurationManager(ISettingsStore settings, TimeSpan interval, string environment)
  {
      this.settings = settings;
      this.interval = interval;
      this.CheckForConfigurationChangesAsync().Wait();
      this.changed = new Subject<KeyValuePair<string, string>>();
      this.Environment = environment;
  }
  ...
  /// <summary>
  /// Check to see if the current instance is monitoring for changes
  /// </summary>
  public bool IsMonitoring => this.monitoringTask != null && !this.monitoringTask.IsCompleted;

  /// <summary>
  /// Start the background monitoring for configuration changes in the central store
  /// </summary>
  public void StartMonitor()
  {
      if (this.IsMonitoring)
          return;

      try
      {
          this.timerSemaphore.Wait();

          // Check again to make sure we are not already running.
          if (this.IsMonitoring)
              return;

          // Start running our task loop.
          this.monitoringTask = ConfigChangeMonitor();
      }
      finally
      {
          this.timerSemaphore.Release();
      }
  }

  /// <summary>
  /// Loop that monitors for configuration changes
  /// </summary>
  /// <returns></returns>
  public async Task ConfigChangeMonitor()
  {
      while (!cts.Token.IsCancellationRequested)
      {
          await this.CheckForConfigurationChangesAsync();
          await Task.Delay(this.interval, cts.Token);
      }
  }

  /// <summary>
  /// Stop monitoring for configuration changes
  /// </summary>
  public void StopMonitor()
  {
      try
      {
          this.timerSemaphore.Wait();

          // Signal the task to stop.
          this.cts.Cancel();

          // Wait for the loop to stop.
          this.monitoringTask.Wait();

          this.monitoringTask = null;
      }
      finally
      {
          this.timerSemaphore.Release();
      }
  }

  public void Dispose()
  {
      this.cts.Cancel();
  }
  ...
}
```

Un'istanza della classe `ExternalConfigurationManager` viene creata come istanza singleton dalla classe `ExternalConfiguration` illustrata di seguito.

```csharp
public static class ExternalConfiguration
{
    private static readonly Lazy<ExternalConfigurationManager> configuredInstance = new Lazy<ExternalConfigurationManager>(
        () =>
        {
            var environment = CloudConfigurationManager.GetSetting("environment");
            return new ExternalConfigurationManager(environment);
        });

    public static ExternalConfigurationManager Instance => configuredInstance.Value;
}
```

Il codice seguente è tratto dalla classe `WorkerRole` del progetto _ExternalConfigurationStore.Cloud_. Indica come l'applicazione usa la classe `ExternalConfiguration` per leggere un'impostazione.

```csharp
public override void Run()
{
  // Start monitoring configuration changes.
  ExternalConfiguration.Instance.StartMonitor();

  // Get a setting.
  var setting = ExternalConfiguration.Instance.GetAppSetting("setting1");
  Trace.TraceInformation("Worker Role: Get setting1, value: " + setting);

  this.completeEvent.WaitOne();
}
```

Il codice seguente, anch'esso dalla classe `WorkerRole`, illustra in che modo l'applicazione sottoscrive gli eventi di configurazione.

```csharp
public override bool OnStart()
{
  ...
  // Subscribe to the event.
  ExternalConfiguration.Instance.Changed.Subscribe(
     m => Trace.TraceInformation("Configuration has changed. Key:{0} Value:{1}",
          m.Key, m.Value),
     ex => Trace.TraceError("Error detected: " + ex.Message));
  ...
}
```

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive

- Un esempio che illustra questo modello è disponibile su [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/external-configuration-store).
