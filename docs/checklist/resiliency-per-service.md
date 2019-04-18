---
title: Elenco di controllo per la resilienza per i servizi di Azure
titleSuffix: Azure Design Review Framework
description: Elenco di controllo contenente indicazioni per la resilienza per vari servizi di Azure.
author: petertaylor9999
ms.date: 11/26/2018
ms.topic: checklist
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency, checklist
ms.openlocfilehash: db42bd259bf71ef2ffa3e9efc5e4cd6ba2078e6b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59639700"
---
# <a name="resiliency-checklist-for-specific-azure-services"></a>Elenco di controllo per la resilienza per servizi di Azure specifici

La resilienza è la capacità di un sistema di correggere gli errori e continuare a funzionare. Ogni tecnologia ha modalità di errore specifiche che è necessario tenere in considerazione durante la progettazione e l'implementazione di un'applicazione. Usare questo elenco di controllo per esaminare le considerazioni relative alla resilienza per servizi di Azure specifici. Per altre informazioni sulla progettazione di applicazioni resilienti, vedere [progettare le applicazioni Azure affidabili](../reliability/index.md).

## <a name="app-service"></a>Servizio app

**Usare il livello Standard o Premium.** Questi livelli supportano gli slot di staging e i backup automatici. Per altre informazioni, vedere [Panoramica approfondita dei piani per Servizio app di Azure](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/)

**Evitare il ridimensionamento.** Selezionare invece un livello e una dimensione di istanza che soddisfino i requisiti di prestazioni in condizioni di carico tipico e quindi [scalare orizzontalmente](/azure/app-service-web/web-sites-scale/) le istanze per gestire le modifiche nel volume del traffico. Il ridimensionamento può determinare il riavvio dell'applicazione.

**Archiviare la configurazione come impostazioni dell'app.** Usare le impostazioni dell'app per conservare le impostazioni di configurazione come impostazioni dell'app. Definire le impostazioni nei modelli di Resource Manager oppure tramite PowerShell, in modo da poterle applicare come parte di un processo di distribuzione/aggiornamento automatizzato, che è più affidabile. Per altre informazioni, vedere [Configurazione delle app Web in Servizio app di Azure](/azure/app-service-web/web-sites-configure/).

**Creare piani di servizio app separati per la produzione e per il test.** Non usare gli slot nella distribuzione di produzione per il test.  Tutte le app contenute nello stesso piano di servizio app condividono le stesse istanze di VM. L'inserimento delle distribuzioni di produzione e di test nello stesso piano può influire negativamente sulla distribuzione di produzione. I test di carico, ad esempio, possono ridurre le prestazioni del sito di produzione attivo. Se si inseriscono le distribuzioni di test in un piano separato, le si isola dalla versione di produzione.

**Separare le app Web dalle API Web.** Se la soluzione contiene sia un front-end Web che un'API Web, si può considerare di scomporli in app del Servizio app separate. Questa progettazione rende più semplice scomporre la soluzione in base al carico di lavoro. Si possono eseguire l'app Web e l'API Web in piani di servizio app separati in modo che le si possa scalare in maniera indipendente. Se inizialmente questo livello di scalabilità non è necessario, è possibile distribuire le app nello stesso piano e spostarle successivamente in piani separati, se necessario.

**Evitare di usare la funzionalità di backup del Servizio app per eseguire il backup dei database SQL di Azure.** Usare invece i [backup automatici del database SQL][sql-backup]. Il backup del Servizio app di Azure esporta il database in un file SQL con estensione bacpac, che ha un costo in DTU.

**Distribuire in uno slot di staging.** Creare uno slot di distribuzione per lo staging. Distribuire gli aggiornamenti dell'applicazione nello slot di staging e verificare la distribuzione prima di scambiarla nell'ambiente di produzione. In questo modo, diminuiscono le probabilità che si esegua un aggiornamento non corretto nell'ambiente di produzione. Assicura inoltre che tutte le istanze siano pronte per essere scambiate nell'ambiente di produzione. Molte applicazioni presentano tempi di riscaldamento e di avvio a freddo piuttosto lunghi. Per altre informazioni, vedere [Configurare ambienti di staging per le app Web nel Servizio app di Azure](/azure/app-service-web/web-sites-staged-publishing/).

**Creare uno slot di distribuzione per contenere l'ultima distribuzione valida nota.** Quando si distribuisce un aggiornamento nell'ambiente di produzione, spostare la distribuzione di produzione precedente nell'ultimo slot valido noto. Questa operazione rende più semplice eseguire il rollback di una distribuzione non corretta. Se viene rilevato un problema in un secondo momento, è possibile ripristinare rapidamente l'ultima versione valida nota. Per altre informazioni, vedere [Applicazione Web di base](../reference-architectures/app-service-web-app/basic-web-app.md).

**Abilitare la registrazione diagnostica**, includendo la registrazione delle applicazioni e del server Web. La registrazione è importante per il monitoraggio e la diagnostica. Vedere [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure](/azure/app-service-web/web-sites-enable-diagnostic-log/).

**Registrare nell'archivio BLOB.** Questa operazione rende più semplice raccogliere e analizzare i dati.

**Creare un account di archiviazione separato per i log.** Non usare lo stesso account di archiviazione per i log e i dati dell'applicazione. In questo modo si impedisce che la registrazione riduca le prestazioni dell'applicazione.

**Monitorare le prestazioni.** Usare un servizio di monitoraggio delle prestazioni, ad esempio [New Relic](https://newrelic.com/) o [Application Insights](/azure/application-insights/app-insights-overview/) per monitorare le prestazioni dell'applicazione e il comportamento sotto carico.  Il monitoraggio delle prestazioni offre informazioni approfondite in tempo reale sull'applicazione. Consente di diagnosticare i problemi e di eseguire l'analisi della causa radice degli errori.

## <a name="application-gateway"></a>Gateway applicazione

**Eseguire il provisioning di almeno due istanze.** Distribuire il gateway applicazione con almeno due istanze. Un'istanza singola è un singolo punto di errore. Usare due o più istanze per la ridondanza e la scalabilità. Per essere idonei al [contratto di servizio](https://azure.microsoft.com/support/legal/sla/application-gateway), è necessario eseguire il provisioning di due o più istanze di medie o grandi dimensioni.

## <a name="cosmos-db"></a>Cosmos DB

**Replicare il database tra aree.** Azure Cosmos DB consente di associare qualsiasi numero di aree di Azure a un account del database Cosmos DB. Un database Microsoft Azure Cosmos DB può avere più aree di lettura e un'area di scrittura. Se si verifica un errore nell'area di scrittura, è possibile leggere da un'altra replica. Il client SDK gestisce questa situazione automaticamente. È anche possibile eseguire il failover dell'area di scrittura in un'altra area. Per altre informazioni, vedere [Come distribuire i dati a livello globale con Azure Cosmos DB](/azure/cosmos-db/distribute-data-globally).

## <a name="event-hubs"></a>Hub eventi

**Usare i checkpoint.**  Un consumer eventi deve scrivere la propria posizione corrente nell'archivio permanente a intervalli di tempo predefiniti. In tal modo, se si verifica un errore del consumer (ad esempio, in caso di arresto anomalo del consumer o errore dell'host), una nuova istanza può riprendere la lettura del flusso dall'ultima posizione registrata. Per altre informazioni, vedere [Consumer eventi](/azure/event-hubs/event-hubs-features#event-consumers).

**Gestire i messaggi duplicati.** In caso di errore di un consumer eventi, l'elaborazione dei messaggi viene ripresa dall'ultimo checkpoint registrato. I messaggi che sono già stati elaborati dopo l'ultimo checkpoint verranno elaborati di nuovo. È quindi necessario che la logica di elaborazione dei messaggi sia idempotente o che l'applicazione possa deduplicare i messaggi.

**Gestire le eccezioni.** Un consumer eventi in genere elabora un batch di messaggi in un ciclo. È consigliabile gestire le eccezioni all'interno di questo ciclo di elaborazione per evitare di perdere un intero batch di messaggi se un singolo messaggio causa un'eccezione.

**Usare una coda di messaggi non recapitabili.** Se l'elaborazione di un messaggio determina un errore non temporaneo, inserire il messaggio in una coda di messaggi non recapitabili per poterne monitorare lo stato. A seconda dello scenario, è possibile eseguire un nuovo tentativo in un secondo momento, applicare una transazione di compensazione o effettuare altre azioni. Si noti che Hub eventi non ha funzionalità predefinite per l'inserimento in una coda di messaggi non recapitabili. È possibile usare l'archivio code o il bus di servizio di Azure per implementare una coda di messaggi non recapitabili oppure usare Funzioni di Azure o altri meccanismi di gestione eventi.

**Implementare il ripristino di emergenza effettuando il failover in uno spazio dei nomi di Hub eventi secondario.** Per altre informazioni, vedere [Ripristino di emergenza geografico nel servizio Hub eventi di Azure](/azure/event-hubs/event-hubs-geo-dr).

## <a name="redis-cache"></a>Cache Redis

**Configurare la replica geografica**. La replica geografica fornisce un meccanismo per il collegamento di due istanze di Cache Redis di Azure di livello Premium. I dati scritti nella cache primaria vengono replicati in una cache secondaria di sola lettura. Per altre informazioni, vedere [Come configurare la replica geografica per Cache Redis di Azure](/azure/redis-cache/cache-how-to-geo-replication).

**Configurare la persistenza dei dati.** La persistenza di Redis consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Per altre informazioni, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](/azure/redis-cache/cache-how-to-premium-persistence).

Se si usa Redis Cache come cache di dati temporanea e non come archivio persistente, questi consigli potrebbero non essere applicabili.

## <a name="search"></a>Ricerca

**Eseguire il provisioning di più repliche.** Usare almeno due repliche per la disponibilità elevata in lettura o tre per la disponibilità elevata in scrittura.

**Configurare gli indicizzatori per le distribuzioni in più aree.** Con una distribuzione in più aree, si possono considerare le opzioni per la continuità nell'indicizzazione.

- Se l'origine dati viene replicata geograficamente, occorre puntare ogni indicizzatore di ogni servizio di Ricerca di Azure regionale sulla replica dell'origine dati locale. Tale approccio non è tuttavia consigliato per i set di dati di grandi dimensioni archiviati nel database di SQL Azure. Il motivo è che Ricerca di Azure non può eseguire l'indicizzazione incrementale da repliche di database SQL secondarie, ma solo da repliche primarie. Puntare quindi tutti gli indicizzatori sulla replica primaria. Dopo un failover, puntare gli indicizzatori di Ricerca di Azure sulla nuova replica primaria.

- Se l'origine dati non è replicata geograficamente, puntare più indicizzatori sulla stessa origine dati, in modo che i servizi di Ricerca di Azure in più aree eseguano l'indicizzazione dall'origine dati in modo continuativo e indipendente. Per altre informazioni, vedere [Considerazioni sulle prestazioni e sull'ottimizzazione di Ricerca di Azure][search-optimization].

## <a name="service-bus"></a>Bus di servizio

**Usare il livello Premium per i carichi di lavoro di produzione**. La [messaggistica Premium del bus di servizio](/azure/service-bus-messaging/service-bus-premium-messaging) offre risorse di elaborazione dedicate e riservate, oltre alla capacità di memoria per supportare velocità effettiva e prestazioni prevedibili. Il livello di messaggistica Premium consente anche di accedere alle nuove funzionalità disponibili in un primo momento solo per i clienti Premium. È possibile decidere il numero di unità di messaggistica in base ai carichi di lavoro previsti.

**Gestire i messaggi duplicati**. Se in un server di pubblicazione si verifica un errore subito dopo l'invio di un messaggio o se si riscontrano problemi di rete o di sistema, potrebbe erroneamente non venire registrato che il messaggio è stato recapitato e lo stesso messaggio potrebbe essere inviato due volte al sistema. Il bus di servizio può gestire questo problema abilitando il rilevamento dei messaggi duplicati. Per altre informazioni, vedere [Rilevamento duplicati](/azure/service-bus-messaging/duplicate-detection).

**Gestire le eccezioni**. Le API di messaggistica generano eccezioni quando si verifica un errore dell'utente, un errore di configurazione o un errore di altro tipo. Il codice client (mittenti e ricevitori) dovrebbe gestire queste eccezioni nel codice. Questo aspetto è particolarmente importante nell'elaborazione batch, in cui la gestione delle eccezioni può essere usata per evitare di perdere un intero batch di messaggi. Per altre informazioni, vedere [Eccezioni di messaggistica del bus di servizio](/azure/service-bus-messaging/service-bus-messaging-exceptions).

**Criteri di ripetizione**. Il bus di servizio consente di selezionare i criteri di ripetizione più appropriati per le applicazioni. Il criterio predefinito consente un massimo di 9 tentativi di ripetizione e un'attesa di 30 secondi, ma può essere modificato ulteriormente. Per altre informazioni, vedere [Criteri di ripetizione - Bus di servizio](/azure/architecture/best-practices/retry-service-specific#service-bus).

**Usare una coda di messaggi non recapitabili**. Se un messaggio non può essere elaborato o recapitato ad alcun destinatario dopo più tentativi, viene spostato in una coda di messaggi non recapitabili. Implementare un processo per leggere i messaggi dalla coda di messaggi non recapitabili, controllarli e risolvere il problema. A seconda dello scenario, è possibile riprovare a inviare il messaggio così com'è, apportare le modifiche e riprovare oppure eliminare il messaggio. Per altre informazioni, vedere [Panoramica delle code dei messaggi non recapitabili del bus di servizio](/azure/service-bus-messaging/service-bus-dead-letter-queues).

**Usare il ripristino di emergenza geografico**. Il ripristino di emergenza geografico assicura che l'elaborazione dei dati continui a funzionare in un'area o un data center diverso se un'intera area o data center di Azure diventa non disponibile a causa di una situazione di emergenza. Per altre informazioni, vedere [Ripristino di emergenza geografico per il bus di servizio di Azure](/azure/service-bus-messaging/service-bus-geo-dr).

## <a name="storage"></a>Archiviazione

**Per i dati dell'applicazione, usare l'Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).** L'archiviazione RA-GRS replica i dati in un'area secondaria e fornisce l'accesso in sola lettura dall'area secondaria. Se si verifica un'interruzione dell'archiviazione nell'area primaria, l'applicazione può leggere i dati dall'area secondaria. Per altre informazioni, vedere [Replica di Archiviazione di Azure](/azure/storage/storage-redundancy/).

**Per i dischi di VM, usare Managed Disks.** Il servizio [Managed Disks][managed-disks] offre una maggiore affidabilità per le VM in un set di disponibilità, perché fa in modo che i dischi siano sufficientemente isolati gli uni dagli altri per evitare singoli punti di errore. I dischi gestiti, inoltre, non sono soggetti ai limiti delle operazioni di I/O al secondo dei dischi rigidi virtuali creati in un account di archiviazione. Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure][vm-manage-availability].

**Per l'archiviazione code, creare una coda di backup in un'altra area.** Per l'archiviazione code, una replica di sola lettura ha un uso limitato, perché non è possibile accodare o rimuovere oggetti dalla coda. Creare invece una coda di backup in un account di archiviazione in un'altra area. Se si verifica un'interruzione nell'archiviazione, l'applicazione può usare la coda di backup finché l'area primaria non torna nuovamente disponibile. In questo modo, l'applicazione può comunque elaborare nuove richieste.

## <a name="sql-database"></a>Database SQL

**Usare il livello Standard o Premium.** Questi livelli offrono un periodo di ripristino temporizzato più lungo (35 giorni). Per altre informazioni vedere [SQL Database options and performance](/azure/sql-database/sql-database-service-tiers/) (Prestazioni e opzioni del database SQL).

**Abilitare il controllo del database SQL.** Il controllo può essere usato per diagnosticare errori umani o attacchi dannosi. Per altre informazioni, vedere l' [Introduzione al controllo del database SQL](/azure/sql-database/sql-database-auditing-get-started/).

**Usare la replica geografica attiva.** Usare la replica geografica attiva per crearne una secondaria leggibile in un'altra area.  Se il database primario restituisce un errore o deve semplicemente essere portato offline, eseguire un failover manuale al database secondario.  Nella fase di failover il database secondario rimane in sola lettura.  Per altre informazioni, vedere [SQL Database Active Geo-Replication](/azure/sql-database/sql-database-geo-replication-overview/) (Replica geografica attiva del database SQL).

**Usare il partizionamento orizzontale.** Considerare l'utilizzo del partizionamento orizzontale per il database. Il partizionamento orizzontale può isolare gli errori. Per altre informazioni, vedere [Aumentare il numero di istanze con il database SQL di Azure](/azure/sql-database/sql-database-elastic-scale-introduction/).

**Usare il ripristino temporizzato per recuperare da un errore umano.**  Il ripristino temporizzato riporta il database a un punto precedente nel tempo. Per altre informazioni, vedere [Ripristinare un database SQL di Azure mediante i backup automatici del database][sql-restore].

**Eseguire il ripristino a livello geografico per recuperare da un'interruzione del servizio.** Il ripristino geografico recupera un database da un backup con ridondanza geografica.  Per altre informazioni, vedere [Ripristinare un database SQL di Azure mediante i backup automatici del database][sql-restore].

## <a name="sql-data-warehouse"></a>SQL Data Warehouse

**Non disabilitare il backup geografico.** Per impostazione predefinita, SQL Data Warehouse esegue il backup completo dei dati ogni 24 ore per il ripristino di emergenza. È sconsigliato disattivare la funzionalità. Per altre informazioni, consultare [Backup geografici](/azure/sql-data-warehouse/backup-and-restore#geo-backups).

## <a name="sql-server-running-in-a-vm"></a>SQL Server in esecuzione in una VM

**Replicare il database.** Usare i gruppi di disponibilità AlwaysOn di SQL Server per replicare il database. Questo approccio offre disponibilità elevata se un'istanza di SQL Server ha esito negativo. Per altre informazioni, vedere [Eseguire macchine virtuali Windows per un'applicazione a più livelli](../reference-architectures/virtual-machines-windows/n-tier.md).

**Eseguire il backup del database.** Se si usa già [Backup di Azure](/azure/backup/) per eseguire il backup delle VM, considerare di usare [Backup di Azure per carichi di lavoro di SQL Server con DPM](/azure/backup/backup-azure-backup-sql/). Con questo approccio, si usano un solo ruolo di amministratore di backup per l'organizzazione e una procedura di ripristino unificata per le macchine virtuali e SQL Server. Usare altrimenti [Backup gestito di SQL Server in Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).

## <a name="traffic-manager"></a>Gestione traffico

**Eseguire il failback manuale.** Dopo un failover di Gestione traffico, eseguire il failback manuale anziché automatico. Prima del failback, verificare che tutti i sottosistemi dell'applicazione siano integri.  In caso contrario, si potrebbe creare una situazione in cui l'applicazione passa alternativamente da un data center all'altro. Per altre informazioni, vedere [Eseguire macchine virtuali in più aree per una disponibilità elevata](../reference-architectures/virtual-machines-windows/multi-region-application.md).

**Creare un endpoint di probe di integrità.** Creare un endpoint personalizzato che segnali l'integrità generale dell'applicazione. Gestione traffico può così eseguire il failover se si verifica un errore in un percorso critico, non solo nel front-end. L'endpoint restituisce un codice di errore HTTP se una qualsiasi dipendenza critica non è integra o non è raggiungibile. Non segnalare tuttavia gli errori relativi ai servizi non critici. In caso contrario, il probe di integrità potrebbe generare il failover quando non è necessario e creare di conseguenza falsi positivi. Per altre informazioni, vedere [Traffic Manager endpoint monitoring and failover](/azure/traffic-manager/traffic-manager-monitoring/) (Monitoraggio degli endpoint di Gestione traffico e failover).

## <a name="virtual-machines"></a>Macchine virtuali

**Evitare di eseguire un carico di lavoro di produzione in una singola VM.** La distribuzione in una singola macchina virtuale non è resistente alle operazioni di manutenzione pianificate o non pianificate. Inserire invece più macchine virtuali in un set di disponibilità o [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/), con un servizio di bilanciamento del carico in primo piano.

**Specificare un set di disponibilità quando si esegue il provisioning della VM.** Non è possibile attualmente aggiungere una macchina virtuale a un set di disponibilità dopo avere eseguito il provisioning della macchina virtuale. Quando si aggiunge una nuova macchina virtuale a un set di disponibilità esistente, assicurarsi di creare una scheda di rete per la macchina virtuale e aggiungere la scheda di rete al pool di indirizzi back-end nel servizio di bilanciamento del carico. In caso contrario, il servizio di bilanciamento del carico non instrada il traffico di rete a tale macchina virtuale.

**Inserire ogni livello applicazione in un set di disponibilità separato.** In un'applicazione a più livelli, non inserire le macchine virtuali appartenenti a livelli differenti nello stesso set di disponibilità. Le macchine virtuali in un set di disponibilità sono posizionate in più domini di errore (FD) e di aggiornamento (UD). Per ottenere il vantaggio della ridondanza dei domini di errore e dei domini di aggiornamento, tuttavia, ogni macchina virtuale nel set di disponibilità deve essere in grado di gestire le stesse richieste del client.

**Replicare le macchine virtuali con Azure Site Recovery.** Quando si esegue la replica di macchine virtuali di Azure usando [Site Recovery][site-recovery], tutti i dischi delle macchine virtuali vengono replicati nell'area di destinazione in modo continuativo e asincrono. I punti di ripristino vengono creati a intervalli di pochi minuti. In tal modo, si ottiene un Obiettivo del punto di ripristino (RPO) nell'ordine di minuti. È possibile condurre esercitazioni sul ripristino di emergenza come numero di volte desiderato, senza influire sull'applicazione di produzione o sulla replica in corso. Per altre informazioni, vedere [Eseguire un'esercitazione sul ripristino di emergenza in Azure][site-recovery-test].

**Scegliere le dimensioni di VM corrette in base ai requisiti di prestazioni.** Quando si sposta un carico di lavoro esistente in Azure, per iniziare scegliere le dimensioni di VM più simili a quelle dei server locali. Misurare quindi le prestazioni del carico di lavoro effettivo in relazione agli aspetti di CPU, memoria e operazioni di I/O al secondo del disco e regolare le dimensioni secondo le necessità. Ciò aiuta a garantire il corretto funzionamento dell'applicazione in un ambiente cloud. Inoltre, se sono necessarie più schede di interfaccia di rete, tenerne presente il limite per ogni dimensione.

**Usare Managed Disks per i dischi rigidi virtuali.** Il servizio [Managed Disks][managed-disks] offre una maggiore affidabilità per le VM in un set di disponibilità, perché fa in modo che i dischi siano sufficientemente isolati gli uni dagli altri per evitare singoli punti di errore. I dischi gestiti, inoltre, non sono soggetti ai limiti delle operazioni di I/O al secondo dei dischi rigidi virtuali creati in un account di archiviazione. Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure][vm-manage-availability].

**Installare le applicazioni in un disco di dati, non nel disco del sistema operativo.** In caso contrario, è possibile raggiungere il limite delle dimensioni del disco.

**Usare Backup di Azure per eseguire il backup delle VM.** I backup proteggono contro la perdita di dati accidentale. Per altre informazioni, vedere [Protect Azure VMs with a recovery services vault](/azure/backup/backup-azure-vms-first-look-arm/) (Proteggere le VM di Azure con l'insieme di credenziali dei servizi di ripristino).

**Abilitare i log di diagnostica**, inclusi le metriche di integrità di base, i log dell'infrastruttura e la [diagnostica di avvio][boot-diagnostics]. La diagnostica di avvio permette di diagnosticare gli errori di avvio quando la VM passa a uno stato non avviabile. Per altre informazioni, vedere [Overview of Azure Diagnostic Logs][diagnostics-logs] (Panoramica dei log di diagnostica di Azure).

**Usare l'estensione AzureLogCollector.** Solo VM Windows. Questa estensione aggrega i log della piattaforma Azure e li carica in Archiviazione di Azure, senza che l'operatore acceda in remoto alla macchina virtuale. Per altre informazioni, vedere [Estensione AzureLogCollector](/azure/virtual-machines/virtual-machines-windows-log-collector-extension/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="virtual-network"></a>Rete virtuale

**Per inserire nell'elenco degli elementi consentiti o bloccare gli indirizzi IP pubblici, aggiungere un gruppo di sicurezza di rete (NSG) alla subnet.** Bloccare l'accesso di utenti malintenzionati o consentire l'accesso solo agli utenti che dispongono delle autorizzazioni per accedere all'applicazione.

**Creare un probe di integrità personalizzato.** I probe di integrità di Load Balancer possono testare i protocolli HTTP o TCP. Se una macchina virtuale viene eseguita in un server HTTP, il migliore indicatore dello stato di integrità è un probe HTTP rispetto a un probe TCP. Per un probe HTTP, usare un endpoint personalizzato che segnali l'integrità complessiva dell'applicazione, incluse tutte le dipendenze critiche. Per altre informazioni, vedere [Panoramica di Azure Load Balancer](/azure/load-balancer/load-balancer-overview/).

**Non bloccare il probe di integrità.** Il probe di integrità di Load Balancer viene inviato da un indirizzo IP noto, 168.63.129.16. Non bloccare il traffico da o verso questo indirizzo IP nei criteri del firewall o nelle regole del gruppo di sicurezza di rete. Se si blocca il probe di integrità, il servizio di bilanciamento del carico rimuove la macchina virtuale dalla rotazione.

**Abilitare la registrazione di Load Balancer.** I log mostrano il numero di macchine virtuali nel back-end che non ricevono il traffico di rete a causa di risposte del probe non riuscite. Per altre informazioni, vedere [Log analytics for Azure Load Balancer](/azure/load-balancer/load-balancer-monitor-log/) (Analisi dei log per Azure Load Balancer).

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[diagnostics-logs]: /azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs/
[managed-disks]: /azure/storage/storage-managed-disks-overview
[search-optimization]: /azure/search/search-performance-optimization/
[site-recovery]: /azure/site-recovery/
[site-recovery-test]: /azure/site-recovery/site-recovery-test-failover-to-azure
[sql-backup]: /azure/sql-database/sql-database-automated-backups/
[sql-restore]: /azure/sql-database/sql-database-recovery-using-backups/
[vm-manage-availability]: /azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set
