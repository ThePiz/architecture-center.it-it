---
title: Errore e ripristino di emergenza per le applicazioni Azure
description: Panoramica del ripristino di emergenza si avvicina in Azure
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: f00341b06b8092c798d6260317226190cbace66b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646656"
---
# <a name="failure-and-disaster-recovery-for-azure-applications"></a>Errore e ripristino di emergenza per le applicazioni Azure

*Ripristino di emergenza* è il processo di ripristino di funzionalità dell'applicazione rispetto a una perdita irreversibile.

La tolleranza per funzionalità ridotte durante un'emergenza è una decisione aziendale che varia da un'applicazione al successivo. Può essere accettabile per alcune applicazioni sono completamente non disponibile o da una disponibilità parziale con funzionalità ridotte o ritardi nell'elaborazione per un periodo di tempo. Per altre applicazioni, qualsiasi funzionalità ridotte non è accettabile.

## <a name="disaster-recovery-plan"></a>Piano di ripristino di emergenza

Iniziare creando un piano di ripristino. Il piano viene considerato completo dopo che è stata testata completamente. Includere le persone, processi e le applicazioni necessarie per ripristinare la funzionalità all'interno del contratto a livello di servizio (SLA) sono stati definiti per i clienti.

Prendere in considerazione i suggerimenti seguenti durante la creazione e test del piano di ripristino di emergenza:

- Nel piano, includere il processo per contattare il supporto tecnico e per l'escalation dei problemi. Queste informazioni saranno utili per evitare tempi di inattività prolungato mentre si lavora il processo di ripristino per la prima volta.
- valutare l'impatto aziendale degli errori delle applicazioni,
- Scegliere un'architettura di ripristino tra aree per le applicazioni mission-critical.
- Identificare un proprietario specifico per il piano di ripristino di emergenza che comprende l'automazione e i test.
- Documentare il processo, in particolare passaggi manuali.
- Automatizzare il processo quanto più possibile.
- Stabilire una strategia di backup per tutti i riferimenti e i dati transazionali e ripristino dei backup di test regolarmente.
- Impostare gli avvisi per lo stack di servizi di Azure usato dall'applicazione.
- Eseguire il training di personale addetto alle operazioni di esecuzione del piano.
- Eseguire simulazioni regolari per convalidare e migliorare il piano.

Se si usa [Azure Site Recovery](/azure/site-recovery/) per replicare le macchine virtuali (VM), creare un piano di ripristino completamente automatizzata per eseguire il failover dell'intera applicazione.

## <a name="manual-responses"></a>Risposte manuali

Sebbene l'automazione è ideale, alcune strategie di ripristino di emergenza richiedono risposte manuali.

### <a name="alerts"></a>Avvisi

Monitorare l'applicazione per quei segnali di avviso che potrebbero richiedere un intervento proattivo. Ad esempio, se il Database SQL di Azure o Azure Cosmos DB limita in modo consistente l'applicazione, potrebbe essere necessario aumentare la capacità del database o ottimizzare le query. Anche se l'applicazione può gestire gli errori di limitazione delle richieste in modo trasparente, i dati di telemetria dovrebbero comunque generare un avviso in modo che si possa procedere.

Per i limiti del servizio e le soglie di quota, è consigliabile configurare gli avvisi sui log di diagnostica e metriche delle risorse di Azure. Quando possibile, impostare avvisi sulle metriche, che sono una latenza più bassa rispetto a log di diagnostica.

Attraverso [integrità risorse](/azure/service-health/resource-health-checks-resource-types), Azure offre alcuni integrità integrati i controlli di stato che possono aiutare a diagnosticare i problemi di limitazione il servizio di Azure.

### <a name="failover"></a>Failover

Configurare una strategia di ripristino di emergenza per ogni applicazione di Azure e i servizi di Azure. Strategie di distribuzione accettabili per supportare il ripristino di emergenza potrebbero variare in base i contratti di servizio necessari per tutti i componenti di ogni applicazione.  

Azure offre diverse funzionalità all'interno di molti servizi di Azure per consentire il failover manuale, ad esempio [geo-repliche di cache redis](/azure/azure-cache-for-redis/cache-how-to-geo-replication), o per failover automatizzato, ad esempio [gruppi di failover automatico di SQL](/azure/sql-database/sql-database-auto-failover-group). Ad esempio: 

- Per un'applicazione che Usa principalmente le macchine virtuali, è possibile usare Azure Site Recovery per i livelli web e per la logica. Per altre informazioni, vedere [architettura di ripristino di emergenza da Azure ad Azure](/azure/site-recovery/azure-to-azure-architecture). Per SQL Server in macchine virtuali, usare [gruppi di disponibilità di SQL Server Always On](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- Per un'applicazione che Usa servizio App e Database SQL di Azure, è possibile usare un piano di servizio App di livello più piccolo configurato nell'area secondaria, che viene ridimensionato automaticamente quando si verifica un failover. Usare i gruppi di failover per il livello di database.

In entrambi gli scenari, un' [gestione traffico di Azure](/azure/traffic-manager/traffic-manager-overview) profilo offre per il failover automatico in aree geografiche. [Bilanciamento del carico](/azure/load-balancer/load-balancer-overview) oppure [i gateway applicazione](/azure/application-gateway/overview) dovrebbe essere configurato nell'area secondaria per supportare la disponibilità più veloce in caso di failover.

### <a name="operational-readiness-testing"></a>Test della conformità operativa

Eseguire un test della conformità operativa per il failover all'area secondaria e per il failback nell'area primaria. Molti servizi di Azure supportano il failover manuale o il failover di test per le esercitazioni sul ripristino di emergenza. In alternativa, è possibile simulare un'interruzione del servizio in corso l'arresto o la rimozione di servizi di Azure.

## <a name="application-failure"></a>Errore dell'applicazione

Errori dell'applicazione sono reversibile o irreversibile. È possibile prevenire un errore reversibile, ma gli errori irreversibili, verranno arrestato l'applicazione.

- Alcuni errori possono essere indirizzati in modo trasparente la gestione degli errori automaticamente ed eseguendo azioni alternative. Ad esempio, Traffic Manager gestisce automaticamente gli errori derivanti dal software di hardware o sistema operativo sottostante nella macchina virtuale host.
- Con alcuni errori, l'applicazione può continuare gestisce le richieste degli utenti con funzionalità ridotte.
- Più gravi interruzioni del servizio potrebbero eseguire il rendering dell'applicazione non disponibile.

Un sistema ben strutturato separa le responsabilità a livello di servizio &mdash; in fase di progettazione e in fase di esecuzione. Questa separazione impedisce un'interruzione del servizio dipendente da causi l'intera applicazione. Ad esempio, si consideri un'applicazione di e-commerce web con i moduli seguenti:

![AGGIUNGI COLLEGAMENTO A UN'IMMAGINE](./_images/disaster-recovery.png)

Se il database che ospita gli ordini diventa inattivo, il servizio di elaborazione degli ordini non può elaborare le transazioni di vendita. A seconda dell'architettura, potrebbe essere impossibile per i servizi di invio e l'elaborazione degli ordini continuare. Tuttavia, se i dati del prodotto sono archiviati in una posizione diversa, il catalogo dei prodotti è ancora disponibile, anche se altre parti dell'applicazione potrebbero non essere disponibile.

Spetta all'utente per determinare come l'applicazione informerà gli utenti di eventuali problemi temporanei e per le notifiche appropriate al sistema di compilazione. Nell'esempio precedente, l'applicazione può consentire di visualizzare i prodotti e per aggiungerli al carrello acquisti. Tuttavia, quando il cliente tenta di effettuare un acquisto, l'applicazione deve inviare una notifica che la funzionalità di gestione degli ordini è temporaneamente non disponibile. Anche se non è ideale per il cliente, questo approccio impedisce un'interruzione del servizio a livello di applicazione.

## <a name="data-corruption-and-restoration"></a>Ripristino e il danneggiamento dei dati

Se un archivio dati non riesce, potrebbe esserci incoerenze dei dati quando ritorna disponibile, soprattutto se i dati sono stati replicati. Comprendere l'obiettivo tempo di ripristino (RTO) e il punto di ripristino (RPO) descrive come gli archivi dati replicati consentono di stimare la quantità di perdita di dati.

Per comprendere se il failover tra aree viene avviato manualmente o da Microsoft, esaminare i contratti di servizio di Azure. Per i servizi con nessun contratti di servizio per il failover tra più aree, Microsoft in genere decide di eseguire il failover e in genere assegna la priorità il ripristino dei dati nell'area primaria. Se i dati nell'area primaria viene ritenuti non recuperabili, Microsoft effettua il failover all'area secondaria.

### <a name="restoring-data-from-backups"></a>Il ripristino dei dati dai backup

I backup è proteggono dalla perdita di un componente dell'applicazione a causa di eliminazione accidentale o danneggiamento dei dati. Consentono di mantenere una versione funzionante del componente da un'ora precedente, che è possibile usare per ripristinarla.

Strategie di ripristino di emergenza non siano una sostituzione per i backup, ma i backup regolari dei dati delle applicazioni supportano alcuni scenari di ripristino di emergenza. Le scelte di archiviazione di backup devono essere basate sulla strategia di ripristino di emergenza.

La frequenza di esecuzione del processo di backup determina il valore RPO. Ad esempio, se si eseguono backup orari e si verifica un'emergenza due minuti prima del backup, si perderanno 58 minuti di dati. Il piano di ripristino di emergenza deve includere come si risolverà perdita di dati.

È comune per i dati in un archivio dati per i dati di riferimento in un altro archivio. Si consideri ad esempio un Database SQL con una colonna che si collega a un blob in archiviazione di Azure. Se non si verifichino contemporaneamente i backup, il database potrebbe essere un puntatore a un blob che non è stato eseguito il backup prima dell'errore. L'applicazione o il piano di ripristino di emergenza deve implementare procedure per gestire questa incoerenza dopo un ripristino.

> [!NOTE]
> In alcuni scenari, ad esempio quello delle macchine virtuali sottoposti a backup mediante [Backup di Azure](/azure/backup/backup-azure-vms-first-look-arm), è possibile ripristinare solo da un backup nella stessa area. Ad altri servizi Azure, ad esempio [Cache di Azure per Redis](/azure/azure-cache-for-redis/cache-how-to-geo-replication), offrono backup con replica geografica, è possibile usare per ripristinare i servizi in aree geografiche.

### <a name="azure-storage-and-azure-sql-database"></a>Database SQL di Azure e archiviazione di Azure

Azure Archivia automaticamente i dati di archiviazione di Azure e Database SQL di tre volte all'interno di diversi domini di errore nella stessa area. Se si usa la replica geografica, i dati vengono archiviati altre tre volte in un'area diversa. Tuttavia, se i dati sono danneggiati o eliminati nella copia primaria (ad esempio, a causa di errori dell'utente), la replica delle modifiche in altre copie.

Sono disponibili due opzioni per la gestione potenziale danneggiamento dei dati o l'eliminazione:

- **Creare una strategia di backup personalizzata.** È possibile archiviare i backup in Azure o in locale, a seconda dei requisiti aziendali e alle normative di governance.
- **Usare l'opzione di ripristino temporizzato in** per ripristinare un Database SQL.

#### <a name="azure-storage-recovery"></a>Ripristino di archiviazione di Azure

È possibile sviluppare un processo di backup personalizzato per l'archiviazione di Azure o usare uno dei molti strumenti di backup di terze parti.

Archiviazione di Azure offre la resilienza dei dati tramite le repliche automatiche, ma non impedisce il codice dell'applicazione o agli utenti di danneggiare i dati. Mantenere i dati fedeltà dopo un errore di applicazione o un utente sono richieste tecniche più avanzate, ad esempio la copia dei dati in una posizione di archiviazione secondario con un log di controllo. Sono disponibili diverse opzioni:

- [**I BLOB in blocchi.**](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) Creare uno snapshot temporizzato di ogni BLOB in blocchi. Per ogni snapshot, vengono addebitati solo per l'archiviazione necessaria per archiviare le differenze all'interno del blob perché lo stato dello snapshot precedente. Gli snapshot dipendono del blob originale, pertanto si consiglia di copiare in un altro blob o persino in un altro account di archiviazione. Questo approccio assicura che i dati di backup siano protetti da eliminazioni accidentali. Uso [AzCopy](/azure/storage/common/storage-use-azcopy) oppure [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full) per copiare i BLOB in un altro account di archiviazione.

    Per ulteriori informazioni, vedere [Creazione di uno Snapshot di un BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob).

- [**File di Azure.**](/azure/storage/files/storage-files-introduction) Uso [snapshot di condivisione](/azure/storage/files/storage-snapshots-files), AzCopy o PowerShell per copiare i file in un altro account di archiviazione.
- [**Archiviazione tabelle di Azure.**](/azure/storage/tables/table-storage-overview) Usare AzCopy per esportare i dati delle tabelle in un altro account di archiviazione in un'altra area.

#### <a name="sql-database-recovery"></a>Ripristino del Database SQL

Per proteggere automaticamente l'azienda dalla perdita di dati, Database SQL esegue una combinazione di backup completi del database ogni settimana, backup differenziale del database su base oraria e transazione i backup del log ogni 5 a 10 minuti. Per i livelli Basic, Standard e Premium del Database di SQL, usare point-in-time ripristino per ripristinare un database a un momento precedente. Vedere gli articoli seguenti per altre informazioni:

- [Ripristinare un database SQL di Azure mediante i backup automatici del database](/azure/sql-database/sql-database-recovery-using-backups)
- [Panoramica della continuità aziendale del database SQL di Azure](/azure/sql-database/sql-database-business-continuity)

Un'altra opzione consiste nell'usare la replica geografica attiva per Database SQL, che replica automaticamente le modifiche del database al database secondari nell'area di Azure uguali o diverso. Per altre informazioni, vedere [creazione e uso di replica geografica attiva](/azure/sql-database/sql-database-active-geo-replication).

È anche possibile usare un approccio più manuale per il backup e ripristino:

- Usare la **copia del DATABASE** comando per creare una copia di backup del database con coerenza transazionale.
- Usare Azure SQL Database di importazione/esportazione Service, che supporta l'esportazione dei database in file BACPAC (file compressi contenenti lo schema di database e i dati associati) archiviati in archiviazione Blob di Azure. Per evitare un'interruzione del servizio a livello di area, copiare i file BACPAC in un'area alternativa.

### <a name="sql-server-on-vms"></a>SQL Server in VM

Per SQL Server in esecuzione nelle macchine virtuali, sono disponibili due opzioni: backup tradizionali e log shipping.

- Con i backup tradizionali, è possibile ripristinare per uno specifico punto nel tempo, ma il processo di recupero è lento. Il ripristino dei backup tradizionali, è necessario iniziare con un backup completo iniziale e quindi applicare i backup incrementali.
- È possibile configurare una sessione di log shipping per ritardare il ripristino dei backup del log. Ciò fornisce una finestra per il ripristino da errori nella replica primaria.

### <a name="azure-database-for-mysql-and-azure-database-for-postgresql"></a>Database di Azure per MySQL e Database di Azure per PostgreSQL

Nel Database di Azure per MySQL e Database di Azure per PostgreSQL, il servizio di database esegue automaticamente il backup ogni cinque minuti. È possibile usare i backup automatici per ripristinare il server e i relativi database da un punto precedente nel tempo in un nuovo server. Per altre informazioni, vedere:

- [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per MySQL tramite il portale di Azure](/azure/mysql/howto-restore-server-portal)
- [Come eseguire il backup e ripristino di un server nel Database di Azure per PostgreSQL usando il portale di Azure](/azure/postgresql/howto-restore-server-portal)

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

COSMOS DB esegue automaticamente il backup a intervalli regolari. I backup vengono archiviati separatamente in un altro servizio di archiviazione e vengono replicati a livello globale per proteggersi da emergenze a livello di area. Se si elimina accidentalmente il database o la raccolta, è possibile inviare un ticket di supporto o contattare il supporto di Azure per ripristinare i dati dall'ultimo backup automatico. Per altre informazioni, vedere [backup Online e ripristino on demand in Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore).

### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

Per proteggere le macchine virtuali di Azure da errori dell'applicazione o dall'eliminazione accidentale, usare [Backup di Azure](/azure/backup/). I backup creati sono coerenti tra più dischi di macchina virtuale. Inoltre, l'insieme di credenziali di Backup di Azure possono essere replicate tra le aree per supportare il ripristino da una perdita a livello di area.

## <a name="network-outage"></a>Interruzione della rete

Se parti della rete di Azure sono accessibili, potrebbe non essere in grado di accedere all'applicazione o i dati. In questo caso, è consigliabile progettare la strategia di ripristino di emergenza per eseguire la maggior parte delle applicazioni con funzionalità ridotte.

Se servizi con funzionalità ridotte non è un'opzione, le opzioni rimanenti sono tempi di inattività dell'applicazione o il failover a un'area alternativa.

In uno scenario con funzionalità ridotte:

- Se l'applicazione non è possibile accedere ai dati a causa di un'interruzione di rete di Azure, è possibile eseguire in locale con funzionalità ridotte utilizzando dati memorizzati nella cache.
- Potrebbe essere in grado di archiviare i dati in una posizione alternativa finché non viene ripristinata la connettività.

## <a name="dependent-service-failure"></a>Errore servizio dipendente da

Per ogni servizio dipendente, è necessario comprendere le implicazioni di un'interruzione del servizio e il modo in cui l'applicazione risponde. Molti servizi includono funzionalità che supportano la resilienza e disponibilità, in modo che la valutazione in modo indipendente ogni servizio è probabile che migliorare il piano di ripristino di emergenza. Ad esempio, hub eventi di Azure supporta [failover](/azure/event-hubs/event-hubs-geo-dr#setup-and-failover-flow) allo spazio dei nomi secondario.

## <a name="region-wide-service-disruptions"></a>Interruzioni del servizio a livello di area

Numero di errori è gestibili nella stessa area di Azure. Tuttavia, nell'improbabile caso di un'interruzione del servizio a livello di area, le copie ridondanti locali dei dati non sono disponibili. Se è stata abilitata la replica geografica, esistono altre tre copie dei BLOB e tabelle in un'area diversa. Se Microsoft dichiara l'area perdita, Azure esegue un nuovo mapping tutte le voci DNS all'area secondaria.

> [!NOTE]
> Questo processo si verifica solo per le interruzioni del servizio a livello di area e non all'interno del controllo. Per ottenere valori migliori di RPO e RTO, provare a usare [Azure Site Recovery](/azure/site-recovery/). Usa Site Recovery, si decide cosa è accettabile un'interruzione e quando eseguire il failover di macchine virtuali replicate.

La risposta a un'interruzione del servizio a livello di area varia a seconda della distribuzione e il piano di ripristino di emergenza.

- Come strategia di controllo dei costi, per le applicazioni non critiche che non richiedono un tempo di ripristino garantito, si potrebbe avere senso per la ridistribuzione in un'area diversa.
- Per le applicazioni che sono ospitate in un'altra area con i ruoli distribuiti ma non distribuisce il traffico tra aree (*distribuzione attiva/passiva*), passare al servizio ospitato secondario nell'area alternativa.
- Per le applicazioni con una distribuzione secondaria completa in un'altra area (*distribuzione attiva/attiva*), instradare il traffico a tale area.

Per altre informazioni sul ripristino dopo un'interruzione di servizio a livello di area, vedere [ripristino dopo un'interruzione di servizio a livello di area](../resiliency/recovery-loss-azure-region.md).

### <a name="vm-recovery"></a>Ripristino di VM

Per le applicazioni critiche, pianificare il ripristino di macchine virtuali in caso di un'interruzione del servizio a livello di area.

- Usare Backup di Azure o un altro metodo di backup per creare backup coerenti con l'applicazione tra più aree. (La replica dell'insieme di credenziali di Backup deve essere configurata al momento della creazione).
- Usare Site Recovery per la replica tra aree per il failover dell'applicazione con un clic e test del failover.
- Usare Gestione traffico per automatizzare il failover del traffico utente in un'altra area.

Per altre informazioni, vedere [ripristino da interruzione del servizio a livello di area, le macchine virtuali](../resiliency/recovery-loss-azure-region.md#virtual-machines).

### <a name="storage-recovery"></a>Ripristino di archiviazione

Per proteggere il dispositivo di archiviazione in caso di un'interruzione del servizio a livello di area:

- Usare l'archiviazione con ridondanza geografica.
- Conosce la posizione di archiviazione con replica geografica. Ciò interessa in cui si distribuire altre istanze dei dati che richiedono affinità di area con l'archiviazione.
- Controllare i dati per la coerenza dopo il failover e, se necessario, ripristinare da un backup.

Per altre informazioni, vedere [progettazione di applicazioni a disponibilità elevata con RA-GRS](/azure/storage/common/storage-designing-ha-apps-with-ragrs).

### <a name="sql-database-and-sql-server"></a>Database SQL e SQL Server

Il database SQL di Azure offre due tipi di ripristino:

- Usare il ripristino geografico per ripristinare un database da una copia di backup in un'altra area. Per altre informazioni, vedere [ripristinare un database SQL di Azure mediante i backup automatici del database](/azure/sql-database/sql-database-recovery-using-backups).
- Usare la replica geografica attiva per il failover a un database secondario. Per altre informazioni, vedere [creazione e uso di replica geografica attiva](/azure/sql-database/sql-database-active-geo-replication).

Per SQL Server in esecuzione nelle macchine virtuali, vedere [elevata disponibilità e ripristino di emergenza per SQL Server in macchine virtuali Azure](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/).

## <a name="service-specific-guidance"></a>Indicazioni specifiche dei servizi

Gli articoli seguenti descrivono il ripristino di emergenza per servizi specifici di Azure:

| Service                       | Articolo                                                                                                                                                                                       |
|-------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Database di Azure per MySQL      | [Panoramica della continuità aziendale con Database di Azure per MySQL](/azure/mysql/concepts-business-continuity)                                                  |
| Database di Azure per PostgreSQL | [Panoramica della continuità aziendale con Database di Azure per PostgreSQL](/azure/postgresql/concepts-business-continuity)                                        |
| Servizi cloud di Azure          | [Operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sui servizi cloud di Azure](/azure/cloud-services/cloud-services-disaster-recovery-guidance) |
| Cosmos DB                     | [Disponibilità elevata con Azure Cosmos DB](/azure/cosmos-db/high-availability)                                                                                |
| Azure Key Vault               | [Disponibilità e ridondanza in Azure Key Vault](/azure/key-vault/key-vault-disaster-recovery-guidance)                                                        |
| Archiviazione di Azure                 | [Ripristino di emergenza e failover dell'account di archiviazione (anteprima) in Archiviazione di Azure](/azure/storage/common/storage-disaster-recovery-guidance)                       |
| Database SQL                  | [Ripristinare un Database SQL di Azure o eseguire il failover in un'area secondaria](/azure/sql-database/sql-database-disaster-recovery)                                       |
| Macchine virtuali              | [Gli elementi da un'istanza di Azure in caso di interruzione del servizio influisce su Cloud di Azure](/azure/cloud-services/cloud-services-disaster-recovery-guidance)               |
| Rete virtuale di Azure         | [Rete virtuale - Continuità aziendale](/azure/virtual-network/virtual-network-disaster-recovery-guidance)                                                  |

## <a name="next-steps"></a>Passaggi successivi

- [Ripristino dal danneggiamento dei dati o dall'eliminazione accidentale](../resiliency/recovery-data-corruption.md)
- [Ripristino da un'interruzione del servizio a livello di area](../resiliency/recovery-loss-azure-region.md)