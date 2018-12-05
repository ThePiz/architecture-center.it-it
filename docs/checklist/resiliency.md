---
title: Elenco di controllo per la resilienza
description: Elenco di controllo in cui vengono fornite le linee guida per gestire l'aspetto della resilienza durante la progettazione.
author: petertaylor9999
ms.date: 01/10/2018
ms.custom: resiliency, checklist
ms.openlocfilehash: ce538a0b234a5b120415980e983096f567f9cf86
ms.sourcegitcommit: 1b5411f07d74f0a0680b33c266227d24014ba4d1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52305945"
---
# <a name="resiliency-checklist"></a>Elenco di controllo per la resilienza

La resilienza è la capacità di un sistema di recuperare in caso di errore e continuare a funzionare ed è uno dei [punti chiave della qualità del software](../guide/pillars.md). La progettazione di un'applicazione resiliente richiede la pianificazione e la prevenzione di un'ampia gamma di possibili modalità di errore. Usare questo elenco di controllo per esaminare l'architettura delle applicazioni in relazione alla resilienza. Vedere anche l'[elenco di controllo per la resilienza per servizi di Azure specifici](./resiliency-per-service.md).

## <a name="requirements"></a>Requisiti

**Definire i requisiti di disponibilità del cliente.** Il cliente ha sicuramente la necessità di soddisfare alcuni requisiti di disponibilità per i componenti dell'applicazione e ciò influisce sulla progettazione dell'applicazione. È quindi necessario concordare con il cliente gli obiettivi di disponibilità di ogni parte dell'applicazione. In caso contrario, il progetto potrebbe non soddisfare le aspettative del cliente. Per altre informazioni, vedere [Progettazione di applicazioni resilienti per Azure](../resiliency/index.md).

## <a name="application-design"></a>Progettazione di applicazioni

**Eseguire un'analisi delle modalità di errore per l'applicazione.** L'analisi delle modalità di errore è un processo che consente di integrare la resilienza in un'applicazione nelle prime fasi di progettazione. Per altre informazioni, vedere [Analisi della modalità di errore][fma]. Gli obiettivi di un'analisi delle modalità di errore includono:  

* Identificazione dei tipi di errori che potrebbero verificarsi in un'applicazione.
* Acquisizione degli effetti e dell'impatto potenziali di ogni tipo di errore sull'applicazione.
* Individuazione delle strategie di ripristino.
  

**Distribuire più istanze di servizi.** Se l'applicazione dipende da una singola istanza di un servizio, crea un singolo punto di errore. Il provisioning di più istanze migliora sia la resilienza che la scalabilità. Per [Servizio app di Azure](/azure/app-service/app-service-value-prop-what-is/) selezionare un [Piano di servizio app](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/) che offra più istanze. Per Servizi cloud di Azure, configurare ognuno dei ruoli in modo da usare [più istanze](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management). Per [Macchine virtuali di Microsoft Azure](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), verificare che l'architettura VM includa più macchine virtuali e che ogni macchina virtuale sia inclusa in un [set di disponibilità][availability-sets].   

**Usare la scalabilità automatica per rispondere a un aumento di carico.** Se l'applicazione non è configurata per la scalabilità orizzontale automatica man mano che il carico aumenta, è possibile che i servizi dell'applicazione abbiano esito negativo se vengono saturati dalle richieste degli utenti. Per altri dettagli, vedere gli argomenti seguenti:

* Generale: [Elenco di controllo per la scalabilità](./scalability.md)
* Servizio app di Azure: [Scalare il conteggio delle istanze manualmente o automaticamente][app-service-autoscale]
* Servizi cloud: [Come configurare la scalabilità automatica di un servizio cloud][cloud-service-autoscale]
* Macchine virtuali: [Panoramica della scalabilità automatica con i set di scalabilità di macchine virtuali di Azure][vmss-autoscale]

**Usare il bilanciamento del carico per distribuire le richieste.** Il bilanciamento del carico distribuisce le richieste dell'applicazione tra le istanze del servizio integre, eliminando dalla rotazione le istanze che non sono integre. Se il servizio usa Servizio app di Azure o Servizi cloud di Azure, il carico viene bilanciato automaticamente. Se l'applicazione usa invece le macchine virtuali di Azure, è necessario eseguire il provisioning di un servizio di bilanciamento del carico. Per altri dettagli, vedere [Panoramica di Azure Load Balancer](/azure/load-balancer/load-balancer-overview/).

**Configurare i gateway applicazione di Azure per usare più istanze.** A seconda dei requisiti dell'applicazione, un [gateway applicazione di Azure](/azure/application-gateway/application-gateway-introduction/) potrebbe essere più adatto per distribuire le richieste ai servizi dell'applicazione. Le istanze singole del servizio gateway applicazione non sono tuttavia garantite da un contratto di servizio, pertanto è possibile che l'applicazione non funzioni se l'istanza del gateway applicazione ha esito negativo. Eseguire il provisioning di più di un'istanza di gateway applicazione di medie o grandi dimensioni per garantire la disponibilità del servizio ai sensi del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/application-gateway/).

**Usare set di disponibilità per ogni livello applicazione.** L'inserimento delle istanze in un [set di disponibilità][availability-sets] permette di accedere a un livello di [contratto di servizio](https://azure.microsoft.com/support/legal/sla/virtual-machines/) più elevato. 

**Replicare le macchine virtuali con Azure Site Recovery.** Quando si esegue la replica di macchine virtuali di Azure usando [Site Recovery][site-recovery], tutti i dischi delle macchine virtuali vengono replicati nell'area di destinazione in modo continuativo e asincrono. I punti di ripristino vengono creati a intervalli di pochi minuti. In tal modo, si ottiene un Obiettivo del punto di ripristino (RPO) nell'ordine di minuti.

**Considerare di distribuire l'applicazione tra più aree.** Se l'applicazione è distribuita in un'unica area, nel raro caso in cui l'intera area diventi non disponibile, anche l'applicazione diventa non disponibile. Questa circostanza potrebbe essere inaccettabile in base alle condizioni del contratto di servizio dell'applicazione. In questo caso, occorre considerare la possibilità di distribuire l'applicazione e i relativi servizi tra più aree. Una distribuzione in più aree può usare un modello attivo-attivo (distribuzione delle richieste tra più istanze attive) o un modello attivo-passivo (con un'istanza pronta di riserva, in caso di errore dell'istanza primaria). È consigliabile distribuire più istanze dei servizi dell'applicazione tra coppie di aree. Per altre informazioni, vedere [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](/azure/best-practices-availability-paired-regions).

**Usare Gestione traffico di Microsoft Azure per eseguire il routing del traffico dell'applicazione in aree diverse.**  [Gestione traffico di Microsoft Azure][traffic-manager] esegue il bilanciamento del carico a livello di DNS e indirizza il traffico ad aree diverse in base alla modalità di [routing][traffic-manager-routing] che si specifica e all'integrità degli endpoint dell'applicazione. Senza Gestione traffico, la distribuzione è limitata a una singola area, con conseguente limitazione della scalabilità, aumento della latenza per alcuni utenti e tempi di inattività dell'applicazione nel caso in cui si verifichi un'interruzione dei servizi a livello di area.

**Configurare e testare i probe di integrità per i servizi di bilanciamento del carico e di gestione del traffico.** Assicurarsi che la logica di integrità controlli le parti critiche del sistema e risponda in modo appropriato ai probe di integrità.

* I probe di integrità di [Gestione traffico di Microsoft Azure][traffic-manager] e di [Azure Load Balancer][load-balancer] hanno una funzione specifica. Per Gestione traffico, il probe di integrità determina se eseguire o meno il failover in un'altra area. Per un servizio di bilanciamento del carico, determina se rimuovere o meno una macchina virtuale dalla rotazione.      
* Per un probe di Gestione traffico, l'endpoint di integrità deve verificare eventuali dipendenze critiche che vengono distribuite nella stessa area e il cui errore deve attivare un failover in un'altra area.  
* Per un servizio di bilanciamento del carico, l'endpoint di integrità deve segnalare l'integrità della VM. Non includere altri livelli o servizi esterni. In caso contrario, se si verifica un errore all'esterno della macchina virtuale, il servizio di bilanciamento del carico rimuove la macchina virtuale dalla rotazione.
* Per informazioni sull'implementazione del monitoraggio dell'integrità nell'applicazione, vedere [Health Endpoint Monitoring Pattern](https://msdn.microsoft.com/library/dn589789.aspx) (Modello di monitoraggio degli endpoint di integrità).

**Monitorare i servizi di terze parti.** Se l'applicazione include dipendenze da servizi di terze parti, identificare dove e come questi servizi possono generare errori e quali effetti possono produrre tali errori sull'applicazione. È possibile che un servizio di terze parti non includa il monitoraggio e la diagnostica. È pertanto importante registrarne le chiamate e associarle alla registrazione diagnostica e di integrità dell'applicazione tramite un identificatore univoco. Per altre informazioni sulle pratiche comprovate di monitoraggio e di diagnostica, vedere [Monitoraggio e diagnostica][monitoring-and-diagnostics-guidance].

**Assicurarsi che qualsiasi servizio di terze parti in uso sia accompagnato da un contratto di servizio.** Se l'applicazione dipende da un servizio di terze parti la cui disponibilità non è garantita da un contratto di servizio, anche la disponibilità dell'applicazione non può essere garantita. Il contratto di servizio dell'applicazione vale tanto quanto il componente meno disponibile dell'applicazione.

**Implementare modelli di resilienza per operazioni remote laddove appropriato.** Se l'applicazione dipende dalla comunicazione tra servizi remoti, seguire gli [schemi progettuali](../patterns/category/resiliency.md) per la gestione di errori temporanei, ad esempio il [modello di ripetizione dei tentativi][retry-pattern] e il [modello a interruttore][circuit-breaker]. 

**Implementare le operazioni asincrone ogni volta che sia possibile.** Le operazioni sincrone possono monopolizzare le risorse e bloccare altre operazioni mentre il chiamante attende il completamento del processo. Progettare ogni parte dell'applicazione in modo da consentire le operazioni asincrone ogni volta che sia possibile. Per altre informazioni su come implementare la programmazione asincrona in C#, vedere [Asynchronous Programming with async and await][asynchronous-c-sharp] (Programmazione asincrona con async e await).

## <a name="data-management"></a>Gestione dei dati

**Comprendere i metodi di replica per le origini dati dell'applicazione.** I dati dell'applicazione vengono archiviati in origini dati diverse e hanno requisiti di disponibilità diversi. Valutare i metodi di replica per ogni tipo di archiviazione dei dati in Azure, tra cui [Replica di Archiviazione di Azure](/azure/storage/storage-redundancy/) e [Replica geografica attiva di database SQL](/azure/sql-database/sql-database-geo-replication-overview/), per assicurarsi che i requisiti dei dati dell'applicazione vengano soddisfatti. Se si esegue la replica di macchine virtuali di Azure usando [Site Recovery][site-recovery], tutti i dischi delle macchine virtuali vengono replicati nell'area di destinazione in modo continuativo e asincrono. I punti di ripristino vengono creati a intervalli di pochi minuti. 

**Assicurarsi che nessun account utente singolo disponga dell'accesso sia ai dati di produzione che a quelli di backup.** Se un singolo account utente dispone dell'autorizzazione di scrittura sia nelle origini dati di backup che nelle origini dati di produzione, i backup dei dati risultano compromessi. Un utente malintenzionato potrebbe eliminare intenzionalmente tutti i dati, mentre un utente normale potrebbe eliminarli accidentalmente. Progettare l'applicazione in modo da limitare le autorizzazioni di ogni account utente affinché solo gli utenti che necessitano dell'accesso in scrittura dispongano di questo privilegio e solo sui dati di produzione o di backup, ma non su entrambi.

**Documentare il processo di failover e di failback delle origini dati e testarlo.** Nel caso in cui l'origine dati sia soggetta a un evento catastrofico, un utente operatore dovrà seguire una serie di istruzioni documentate per eseguire il failover in una nuova origine dati. Se le procedure documentate sono errate, l'operatore non sarà in grado di seguirle correttamente e di eseguire il failover della risorsa. Testare regolarmente le istruzioni delle procedure per verificare che un operatore sia in grado di eseguire il failover e failback dell'origine dati.

**Convalidare i backup dei dati.** Verificare regolarmente che i dati di backup siano quelli previsti eseguendo uno script per convalidare le query, lo schema e l'integrità dei dati. Non ha senso avere un backup, se non può essere usato per ripristinare le origini dati. Accedere e segnalare eventuali incoerenze in modo che il servizio di backup possa essere riparato.

**Considerare di usare un tipo di account di archiviazione con ridondanza geografica.** I dati archiviati in un account di archiviazione di Azure vengono sempre replicati in locale. Esistono tuttavia più strategie di replica tra cui scegliere quando viene eseguito il provisioning di un account di archiviazione. Selezionare [Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)](/azure/storage/storage-redundancy/#read-access-geo-redundant-storage) per proteggere i dati delle applicazioni nel caso raro in cui un'intera area diventi non disponibile.

> [!NOTE]
> Per le macchine virtuali, non affidarsi alla replica RA-GRS per ripristinare i dischi di macchina virtuale (file VHD). Usare invece [Backup di Azure][azure-backup].   
>
>

## <a name="security"></a>Sicurezza

**Implementare la protezione a livello di applicazione contro gli attacchi denial of service distribuiti (DDoS).** I servizi di Azure sono protetti dagli attacchi DDoS a livello rete. Azure non può tuttavia proteggere contro gli attacchi a livello applicazione, perché è difficile distinguere tra le richieste di utente normali e le richieste di utenti malintenzionati. Per altre informazioni sulla protezione dagli attacchi DDoS a livello applicazione, vedere la sezione "Protecting against DDoS"(Protezione contro attacchi DDoS) del PDF scaricabile [Microsoft Azure Network Security](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (Sicurezza di rete di Microsoft Azure).

**Implementare il principio di privilegio minimo per l'accesso alle risorse dell'applicazione.** L'impostazione predefinita dell'accesso alle risorse dell'applicazione dovrebbe essere più restrittiva possibile. Le autorizzazioni di livello superiore devono essere concesse su approvazione. La concessione di un accesso eccessivamente permissivo alle risorse dell'applicazione può consentire a un utente di eliminare intenzionalmente o accidentalmente delle risorse. Azure offre il [controllo degli accessi in base al ruolo](/azure/active-directory/role-based-access-built-in-roles/) per la gestione dei privilegi degli utenti. È tuttavia importante verificare le autorizzazioni minime per altre risorse che dispongono di sistemi di autorizzazioni specifici, ad esempio SQL Server.

## <a name="testing"></a>Test

**Eseguire i test di failover e di failback per l'applicazione.** Senza un test completo del failover e del failback, non si può essere certi che il backup dei servizi dipendenti nell'applicazione avvenga in modo sincronizzato durante un ripristino di emergenza. Assicurarsi che il failover e il failback dei servizi dipendenti dell'applicazione vengano eseguiti nell'ordine corretto. Se si usa [Azure Site Recovery] [ site-recovery] per replicare le macchine virtuali, eseguire periodicamente esercitazioni sul ripristino di emergenza con un failover di test. Per altre informazioni, vedere [Eseguire un'esercitazione sul ripristino di emergenza in Azure][site-recovery-test].

**Eseguire il test di fault injection per l'applicazione.** L'applicazione può non riuscire per molti motivi diversi, ad esempio per scadenza del certificato, esaurimento delle risorse di sistema in una VM o errori di archiviazione. Testare l'applicazione in un ambiente più vicino possibile a quello di produzione, simulando o generando errori reali, ad esempio eliminando i certificati, consumando artificialmente le risorse di sistema o eliminando un'origine di archiviazione. Verificare la capacità dell'applicazione di recuperare da tutti i tipi di errori, da sola e in combinazione. Controllare che gli errori non si propaghino o si ripercuotano a cascata sul sistema.

**Eseguire test in produzione usando dati utente sia reali che sintetici.** Gli ambienti di test e di produzione raramente sono identici. È pertanto importante usare una distribuzione canary o Blue/Green e testare l'applicazione in produzione. Ciò consente di testare l'applicazione in produzione con un carico reale e di garantirne il funzionamento previsto dopo la distribuzione completa.

## <a name="deployment"></a>Distribuzione

**Documentare il processo di rilascio dell'applicazione.** Senza una dettagliata documentazione del processo di rilascio, un operatore potrebbe distribuire un aggiornamento non valido o configurare le impostazioni dell'applicazione in modo non adeguato. Definire e documentare chiaramente il processo di rilascio e assicurarsi che sia disponibile a tutto il team operativo. 

**Automatizzare il processo di distribuzione dell'applicazione.** Quando si richiede al personale operativo di distribuire manualmente l'applicazione, possono verificarsi errori umani che compromettono la distribuzione. 

**Progettare il processo di rilascio per ottimizzare la disponibilità dell'applicazione.** Se il processo di rilascio prevede che i servizi vengano disconnessi durante la distribuzione, l'applicazione non sarà disponibile fino a quando i servizi non verranno riportati online. Usare la tecnica di distribuzione del rilascio [Blue/Green](https://martinfowler.com/bliki/BlueGreenDeployment.html) o [canary](https://martinfowler.com/bliki/CanaryRelease.html) per distribuire l'applicazione alla produzione. Entrambe queste tecniche implicano la distribuzione del codice di rilascio insieme al codice di produzione in modo che gli utenti del codice di rilascio vengano reindirizzati al codice di produzione in caso di errore.

**Accedere e controllare le distribuzioni dell'applicazione.** Se si usano le tecniche di pre-distribuzione, quali i rilasci Blue/Green o canary, nell'ambiente di produzione sarà presente più di una versione dell'applicazione. Se si verifica un problema, è fondamentale determinare quale versione dell'applicazione lo causa. Implementare una strategia di registrazione affidabile per acquisire quante più informazioni specifiche della versione possibili.

**Creare un piano di ripristino dello stato precedente alla distribuzione.** È possibile che la distribuzione dell'applicazione abbia esito negativo e che l'applicazione diventi non disponibile. Progettare un processo di ripristino dello stato precedente per tornare all'ultima versione valida nota e ridurre al minimo i tempi di inattività. 

## <a name="operations"></a>Operazioni

**Implementare le procedure consigliate per il monitoraggio e avvisi nell'applicazione.** Senza un monitoraggio, una diagnostica e avvisi corretti, non è possibile rilevare eventuali errori nell'applicazione e avvisare un operatore di correggerli. Per altre informazioni, vedere [Monitoraggio e diagnostica][monitoring-and-diagnostics-guidance].

**Misurare le statistiche delle chiamate remote e mettere le informazioni a disposizione del team dell'applicazione.**  Se non si tiene traccia delle statistiche delle chiamate remote, non le si riporta in tempo reale e non si offre un modo facile per esaminarle, il team operativo non ha una vista istantanea dell'integrità dell'applicazione. Se inoltre si misura solo il tempo medio delle chiamate remote, non si ottengono informazioni sufficienti per individuare i problemi nei servizi. Riepilogare le metriche di chiamata remota, ad esempio la latenza, la velocità effettiva e gli errori nei percentili 95 e 99. Eseguire l'analisi statistica sulle metriche per individuare gli errori che si verificano entro ogni percentile.

**Tenere traccia del numero di eccezioni temporanee e di tentativi su un intervallo di tempo appropriato.** Se non si tiene traccia e non si monitorano le eccezioni temporanee e i nuovi tentativi nel tempo, è possibile che un problema o un errore venga nascosto dalla logica di ripetizione dell'applicazione. Se il monitoraggio e la registrazione mostrano solo l'esito positivo o negativo di un'operazione, il fatto che l'operazione sia stata ritentata più volte a causa di eccezioni verrà nascosto. Una tendenza all'aumento delle eccezioni nel tempo indica che il servizio ha un problema e potrebbe avere esito negativo. Per altre informazioni, vedere [Materiale sussidiario su come eseguire nuovi tentativi per servizi specifici][retry-service-guidance].

**Implementare un sistema di avvisi tempestivi per l'operatore.** Identificare gli indicatori di prestazioni chiave dell'integrità dell'applicazione, ad esempio le eccezioni temporanee e la latenza delle chiamate remote, e impostare valori di soglia appropriati per ognuno. Inviare un avviso all'operatore quando viene raggiunto il valore di soglia. Impostare queste soglie a livelli che identifichino i problemi prima che diventino critici e si debba ricorrere al ripristino per risolverli.

**Assicurarsi che più persone del team abbiano la formazione adeguata per monitorare l'applicazione ed eseguire eventuali procedure di ripristino manuale.** Se nel team esiste un solo operatore in grado di monitorare l'applicazione e di avviare la procedura di ripristino, tale utente diventa un singolo punto di errore. Formare più operatori sulle procedure di rilevamento e di ripristino e assicurarsi che in ogni momento ve ne sia almeno uno pronto ad agire.

**Assicurarsi che l'applicazione venga sempre eseguita in conformità ai [limiti della sottoscrizione di Azure](/azure/azure-subscription-service-limits/).** Le sottoscrizioni di Azure hanno limiti su determinati tipi di risorse, ad esempio il numero di gruppi di risorse, il numero di core e il numero di account di archiviazione.  Se i requisiti dell'applicazione superano i limiti di sottoscrizione di Azure, creare un'altra sottoscrizione di Azure ed eseguirne il provisioning con un numero sufficiente di risorse.

**Assicurarsi che l'applicazione venga sempre eseguita in conformità ai [limiti previsti per servizio](/azure/azure-subscription-service-limits/).** I singoli servizi di Azure hanno limiti di consumo, ad esempio i limiti per l'archiviazione, la velocità effettiva, il numero di connessioni, le richieste al secondo e altre metriche. Se l'applicazione tenta di usare le risorse oltre questi limiti, avrà esito negativo. Le conseguenze sono la limitazione del servizio e possibili tempi di inattività per gli utenti interessati. A seconda del servizio specifico e dei requisiti dell'applicazione, è spesso possibile evitare questi limiti mediante la scalabilità verticale (ad esempio, la scelta di un altro piano tariffario) o la scalabilità orizzontale (aggiunta di nuove istanze).  

**Progettare i requisiti di archiviazione dell'applicazione in modo da rientrare negli obiettivi di scalabilità e prestazioni di archiviazione di Azure.** Archiviazione di Azure è progettato per funzionare entro obiettivi di scalabilità e prestazioni predefiniti. È quindi opportuno progettare l'applicazione in modo da usare l'archiviazione entro tali obiettivi. Se li si supera, l'applicazione sarà soggetta a limitazioni nell'archiviazione. Per risolvere questo problema, eseguire il provisioning di account di archiviazione aggiuntivi. Se si supera il limite di account di archiviazione, eseguire il provisioning di sottoscrizioni di Azure aggiuntive e quindi eseguire il provisioning di altri account di archiviazione nelle nuove sottoscrizioni. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](/azure/storage/storage-scalability-targets/).

**Selezionare le dimensioni della VM corrette per l'applicazione.** Misurare la CPU, la memoria, il disco e l'I/O effettivi delle VM nell'ambiente di produzione e verificare che le dimensioni di VM selezionate siano sufficienti. In caso contrario, l'applicazione potrebbe essere soggetta a problemi di capacità man mano che le VM si avvicinano ai propri limiti. Le dimensioni delle VM sono descritte in dettaglio in [Dimensioni per le macchine virtuali Windows in Azure](/azure/virtual-machines/virtual-machines-windows-sizes/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

**Determinare se il carico di lavoro dell'applicazione è stabile o variabile nel tempo.** Se il carico di lavoro varia nel tempo, usare i set di scalabilità di macchine virtuali di Azure per scalare automaticamente il numero di istanze della VM. In caso contrario, è necessario aumentare o diminuire il numero di macchine virtuali manualmente. Per altre informazioni, vedere [Virtual Machine Scale Sets Overview](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview/) (Panoramica dei set di scalabilità di macchine virtuali).

**Selezionare il livello di servizio corretto per il database SQL di Azure.** Se l'applicazione usa il database SQL di Microsoft Azure, assicurarsi di avere selezionato il livello di servizio appropriato. Se si seleziona un livello che non è in grado di gestire i requisiti dell'unità di trasmissione dati (DTU) del database dell'applicazione, l'utilizzo dei dati verrà limitato. Per altre informazioni sul piano di servizio corretto, vedere [SQL Database options and performance: Understand what's available in each service tier](/azure/sql-database/sql-database-service-tiers/) (Prestazioni e opzioni del database SQL: offerte in ogni livello di servizio).

**Creare un processo per l'interazione con il supporto di Azure.** Se il processo per contattare [il supporto di Azure](https://azure.microsoft.com/support/plans/) non è impostato prima che sorga la necessità di contattare il supporto tecnico, il tempo di inattività sarà più lungo, in quanto si usa il processo di supporto per la prima volta. Includere fin dall'inizio il processo per contattare il supporto tecnico e l'escalation dei problemi come parte della resilienza dell'applicazione.

**Assicurarsi che l'applicazione non ecceda il numero massimo di account di archiviazione per sottoscrizione.** Azure consente l'uso di un massimo di 200 account di archiviazione per ogni sottoscrizione. Se l'applicazione richiede più account di archiviazione rispetto a quelli attualmente disponibili nella sottoscrizione, è necessario creare una nuova sottoscrizione e creare account di archiviazione aggiuntivi associati alla nuova sottoscrizione. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](/azure/azure-subscription-service-limits/#storage-limits).

**Assicurarsi che l'applicazione non superi gli obiettivi di scalabilità per i dischi di macchina virtuale.** Una macchina virtuale IaaS di Azure supporta l'associazione di un numero di dischi di dati in base a diversi fattori, tra cui la dimensione della macchina virtuale e il tipo di account di archiviazione. Se l'applicazione supera gli obiettivi di scalabilità per i dischi di macchina virtuale, eseguire il provisioning di account di archiviazione aggiuntivi e creare in essi i dischi di macchina virtuale. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](/azure/storage/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks).

## <a name="telemetry"></a>Telemetria

**Registrare i dati di telemetria mentre l'applicazione è in esecuzione nell'ambiente di produzione.** Occorre acquisire informazioni di telemetria affidabili mentre l'applicazione è in esecuzione nell'ambiente di produzione, altrimenti non si avranno informazioni sufficienti per diagnosticare la causa di problemi, mentre l'applicazione viene usata attivamente dagli utenti. Per altre informazioni, vedere [Monitoraggio e diagnostica][monitoring-and-diagnostics-guidance].

**Implementare la registrazione usando un modello asincrono.** Le operazioni di registrazione sincrone possono bloccare il codice dell'applicazione. Assicurarsi che le operazioni di registrazione vengano implementate come operazioni asincrone.

**Correlare i dati di log tra i limiti di servizio.** In una tipica applicazione a più livelli, una richiesta dell'utente può attraversare molti limiti del servizio. Una richiesta dell'utente, ad esempio, ha in genere origine nel livello Web e viene passata al livello business e resa infine persistente nel livello dati. In scenari più complessi, una richiesta dell'utente può essere distribuita a numerosi servizi e archivi dati diversi. Assicurarsi che il sistema di registrazione correli le chiamate tra i limiti del servizio, in modo che sia possibile tenere traccia della richiesta in tutta l'applicazione.

## <a name="azure-resources"></a>Risorse di Azure

**Usare i modelli di Azure Resource Manager per eseguire il provisioning delle risorse.** I modelli di Resource Manager semplificano l'automazione delle distribuzioni tramite PowerShell o l'interfaccia della riga di comando di Azure, con un conseguente aumento dell'affidabilità del processo di distribuzione. Per altre informazioni, vedere [Panoramica di Azure Resource Manager][resource-manager].

**Assegnare nomi significativi alle risorse.** In tal modo, è più semplice individuare una specifica risorsa e comprenderne il ruolo. Per altre informazioni, vedere [Naming conventions for Azure resources](../best-practices/naming-conventions.md) (Convenzioni di denominazione per le risorse di Azure)

**Usare il controllo degli accessi in base al ruolo (RBAC).** Usare questo tipo di controllo per controllare l'accesso alle risorse di Azure da distribuire. Il controllo degli accessi in base al ruolo consente di assegnare i ruoli di autorizzazione a membri del team DevOps, per impedire l'eliminazione accidentale o le modifiche delle risorse distribuite. Per altre informazioni, vedere [Get started with access management in the Azure portal](/azure/active-directory/role-based-access-control-what-is/) (Introduzione alla gestione degli accessi nel portale di Azure)

**Usare i blocchi di risorsa per le risorse critiche, ad esempio le macchine virtuali.** I blocchi di risorsa impediscono l'eliminazione accidentale di una risorsa da parte di un operatore. Per altre informazioni, vedere [Lock resources with Azure Resource Manager](/azure/azure-resource-manager/resource-group-lock-resources/) (Bloccare le risorse con Azure Resource Manager)

**Scegliere coppie di aree.** Per la distribuzione in due aree, scegliere aree della stessa coppia. Nel caso di un'interruzione su vasta scala, viene definita la priorità di ripristino di un'area per ogni coppia. Alcuni servizi, ad esempio l'archiviazione con ridondanza geografica, offrono la replica automatica nell'area abbinata. Per altre informazioni, vedere [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](/azure/best-practices-availability-paired-regions).

**Organizzare i gruppi di risorse in base alla funzione e al ciclo di vita.**  Un gruppo di risorse, in genere, contiene risorse che condividono lo stesso ciclo di vita. In questo modo, è più semplice gestire le distribuzioni, eliminare le distribuzioni di test e assegnare i diritti di accesso, riducendo le probabilità che una distribuzione di produzione venga accidentalmente eliminata o modificata. Creare gruppi di risorse separati per gli ambienti di produzione, sviluppo e test. In una distribuzione in più aree, inserire le risorse per ogni area in gruppi di risorse separati. In questo modo diventa più semplice ridistribuire un'area senza influire sulle altre.

## <a name="next-steps"></a>Passaggi successivi

- [Elenco di controllo per la resilienza per servizi di Azure specifici](./resiliency-per-service.md)
- [Analisi della modalità di errore](../resiliency/failure-mode-analysis.md)


<!-- links -->
[app-service-autoscale]: /azure/monitoring-and-diagnostics/insights-how-to-scale/
[asynchronous-c-sharp]: /dotnet/articles/csharp/async
[availability-sets]:/azure/virtual-machines/virtual-machines-windows-manage-availability/
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[circuit-breaker]: ../patterns/circuit-breaker.md
[cloud-service-autoscale]: /azure/cloud-services/cloud-services-how-to-scale/
[fma]: ../resiliency/failure-mode-analysis.md
[load-balancer]: /azure/load-balancer/load-balancer-overview/
[monitoring-and-diagnostics-guidance]: ../best-practices/monitoring.md
[resource-manager]: /azure/azure-resource-manager/resource-group-overview/
[retry-pattern]: ../patterns/retry.md
[retry-service-guidance]: ../best-practices/retry-service-specific.md
[site-recovery]: /azure/site-recovery/
[site-recovery-test]: /azure/site-recovery/site-recovery-test-failover-to-azure
[traffic-manager]: /azure/traffic-manager/traffic-manager-overview/
[traffic-manager-routing]: /azure/traffic-manager/traffic-manager-routing-methods/
[vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview/
