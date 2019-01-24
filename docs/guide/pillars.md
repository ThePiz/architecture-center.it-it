---
title: Concetti fondamentali della qualità del software
titleSuffix: Azure Application Architecture Guide
description: 'Descrive i cinque concetti fondamentali per la qualità del software: scalabilità, disponibilità, resilienza, gestione e sicurezza.'
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 76870f58fc957f6d82f6dc176d1c538c795a7d20
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486234"
---
# <a name="pillars-of-software-quality"></a>Concetti fondamentali della qualità del software

Un'applicazione cloud efficace è basata su cinque concetti fondamentali per la qualità del software: scalabilità, disponibilità, resilienza, gestione e sicurezza.

| Concetto fondamentale | DESCRIZIONE |
|--------|-------------|
| Scalabilità | La capacità di un sistema di gestire carichi maggiori. |
| Disponibilità | La percentuale di tempo per cui il sistema funziona ed è in esecuzione. |
| Resilienza | La capacità di un sistema di correggere gli errori e continuare a funzionare. |
| Gestione | Processi operativi che mantengono un sistema in esecuzione in produzione. |
| Security | Protezione delle applicazioni e dei dati dalle minacce. |

## <a name="scalability"></a>Scalabilità

La capacità di un sistema di gestire carichi maggiori. È possibile eseguire la scalabilità di un'applicazione in due modi principali. La scalabilità verticale (*aumento* delle prestazioni) aumenta la capacità di una risorsa, ad esempio usando dimensioni di macchina virtuale maggiori. La scalabilità orizzontale (*aumento* del numero di istanze) aggiunge nuove istanze di una risorsa, ad esempio macchine virtuali o repliche di database.

La scalabilità orizzontale presenta vantaggi significativi rispetto alla scalabilità verticale:

- Scalabilità reale del cloud. Le applicazioni possono essere progettate per l'esecuzione su centinaia o migliaia di nodi, raggiungendo un livello di scalabilità non possibile in un singolo nodo.
- La scalabilità orizzontale è elastica. È possibile aggiungere più istanze se il carico aumenta o rimuoverle nei periodi in cui il carico è inferiore.
- La scalabilità orizzontale può essere attivata automaticamente, in base a una pianificazione o in risposta alle variazioni del carico.
- La scalabilità orizzontale può essere più economica rispetto alla scalabilità verticale. L'esecuzione di diverse macchine virtuali di piccole dimensioni può costare meno di una singola macchina virtuale di grandi dimensioni.
- La scalabilità orizzontale può anche migliorare le resilienza aggiungendo la ridondanza. Se si arresta un'istanza, l'applicazione continua a funzionare normalmente.

Un vantaggio della scalabilità verticale è la possibilità di eseguirla senza apportare modifiche all'applicazione. Tuttavia a un certo punto si raggiungerà un limite massimo. A questo punto è possibile procedere solo con la scalabilità orizzontale.

La scalabilità orizzontale deve essere progettata nel sistema. Ad esempio, è possibile scalare orizzontalmente le macchine virtuali posizionandole dietro un bilanciamento del carico. Ogni macchina virtuale nel pool deve tuttavia essere in grado di gestire qualsiasi richiesta del client e quindi l'applicazione deve essere stateless o archiviare lo stato esternamente, ad esempio in una cache distribuita. Nei servizi PaaS gestiti la scalabilità orizzontale e la scalabilità automatica integrata sono spesso integrate. La facilità di scalabilità di questi servizi è un importante vantaggio associato all'utilizzo di servizi PaaS.

Aggiungere più istanze non significa tuttavia che verrà eseguita la scalabilità dell'applicazione. Il collo di bottiglia potrebbe semplicemente verificarsi in un altro punto. Ad esempio, se si esegue la scalabilità di un front-end Web per gestire più richieste client, si potrebbe causare il conflitto di blocchi nel database. È quindi necessario considerare misure aggiuntive, ad esempio la concorrenza ottimistica o il partizionamento di dati, per consentire una velocità effettiva maggiore nel database.

Eseguire sempre i test di carico e delle prestazioni per individuare i potenziali colli di bottiglia. I componenti stateful di un sistema, come i database, sono la causa più comune di colli di bottiglia e la scalabilità orizzontale richiede un'attenta progettazione. Risolvere un collo di bottiglia può rivelare altri colli di bottiglia altrove.

Usare l'[elenco di controllo della scalabilità] [ scalability-checklist] per rivedere la progettazione dal punto di vista della scalabilità.

### <a name="scalability-guidance"></a>Indicazioni sulla scalabilità

- [Schemi di progettazione per la scalabilità e le prestazioni][scalability-patterns]
- Procedure consigliate: [scalabilità automatica][autoscale], [processi in background][background-jobs], [memorizzazione nella cache] [ caching], [rete per la distribuzione di contenuti (CDN)][cdn] e [partizionamento dei dati][data-partitioning]

## <a name="availability"></a>Disponibilità

La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione. Viene in genere misurata come percentuale del tempo di attività. Gli errori dell'applicazione, i problemi dell'infrastruttura e il carico del sistema possono ridurre la disponibilità.

Un'applicazione cloud deve avere un obiettivo del livello di servizio che definisca chiaramente la disponibilità prevista e il modo in cui viene misurata. Quando si definisce la disponibilità, esaminare il percorso critico. Il front-end Web potrebbe essere in grado di servire le richieste dei client, ma se ogni transazione non viene eseguita correttamente perché non è possibile connettersi al database, l'applicazione non è disponibile per gli utenti.

La disponibilità viene spesso descritta in termini di "9", ovvero "quattro 9" significano ad esempio un tempo di disponibilità pari al 99,99%. La tabella seguente illustra il potenziale tempo totale di inattività per vari livelli di disponibilità.

| % del tempo di attività | Tempo di inattività settimanale | Tempo di inattività mensile | Tempo di inattività annuale |
|----------|-------------------|--------------------|-------------------|
| 99% | 1,68 ore | 7,2 ore | 3,65 giorni |
| 99,9% | 10 minuti | 43,2 minuti | 8,76 ore |
| 99,95% | 5 minuti | 21,6 minuti | 4,38 ore |
| 99,99% | 1 minuto | 4,32 minuti | 52,56 minuti |
| 99,999% | 6 secondi | 26 secondi | 5,26 minuti |

Si noti che il tempo di attività del 99% potrebbe implicare un'interruzione del servizio di quasi 2 ore a settimana. Per molte applicazioni, soprattutto le applicazioni per consumatori, questo valore SLO non è accettabile. D'altra parte, cinque 9 (99,999%) indicano non più di 5 minuti di inattività in un *anno*. In tempi così brevi è già abbastanza impegnativo rilevare un'interruzione, ma è ancora più difficile risolvere il problema. Per ottenere una disponibilità molto elevata (99,99% o superiore), non è possibile fare affidamento sull'intervento manuale per la correzione degli errori. L'applicazione deve prevedere la diagnosi e la riparazione automatiche, condizione in cui la resilienza è di importanza strategica.

In Azure il Contratto di servizio descrive gli impegni di Microsoft in merito a tempi di attività e connettività. Se il contratto per un determinato servizio è del 99,95%, significa che è necessario prevedere che il servizio sia disponibile per il 99,95% del tempo.

Le applicazioni spesso dipendono da più servizi. In generale la probabilità che un servizio subisca un tempo di inattività è indipendente. Si supponga, ad esempio, che l'applicazione dipenda da due servizi, ognuno con un contratto di servizio con disponibilità del 99,9%. Il contratto di servizio composito per entrambi i servizi è pari al 99,9% &times; 99,9% &asymp; 99,8% o leggermente inferiore rispetto a ogni servizio autonomo.

Usare l'[elenco di controllo della disponibilità] [ availability-checklist] per rivedere la progettazione dal punto di vista della disponibilità.

### <a name="availability-guidance"></a>Indicazioni sulla disponibilità

- [Schemi progettuali per la disponibilità][availability-patterns]
- Procedure consigliate: [scalabilità automatica][autoscale] e [processi in background][background-jobs]

## <a name="resiliency"></a>Resilienza

La resilienza è la capacità di un sistema di correggere gli errori e continuare a funzionare. L'obiettivo della resilienza consiste nel ripristinare uno stato completamente funzionale dell'applicazione dopo un errore. La resilienza è strettamente correlata alla disponibilità.

Nello sviluppo di applicazioni tradizionali si è concentrati sulla riduzione del tempo medio tra gli errori (MTBF). L'obiettivo è stato soprattutto quello di evitare l'errore nel sistema. Nel cloud computing è necessaria una mentalità diversa, a causa di diversi fattori:

- I sistemi distribuiti sono complessi e un errore in un punto può potenzialmente propagarsi in tutto il sistema.
- I costi per gli ambienti cloud sono mantenuti bassi grazie all'utilizzo di hardware di base ed è quindi necessario prevedere guasti occasionali dell'hardware.
- Le applicazioni spesso dipendono da servizi esterni, che possono diventare temporaneamente non disponibili o limitare gli utenti con volumi elevati.
- Attualmente gli utenti si aspettano che un'applicazione sia disponibile 24 ore su 24, 7 giorni su 7, senza mai essere offline.

Tutti questi fattori significano che le applicazioni cloud devono essere progettate in modo da prevedere errori occasionali ed eseguire il ripristino a seguito dell'errore. Azure offre numerose funzioni di resilienza già integrate nella piattaforma. Ad esempio: 

- Archiviazione di Azure, il database SQL e Cosmos DB forniscono tutti la replica dei dati integrata, sia all'interno di un'area sia tra le aree.
- Azure Managed Disks vengono collocati automaticamente in diverse unità di scala di archiviazione, per limitare gli effetti dei guasti hardware.
- Le macchine virtuali in un set di disponibilità vengono distribuite in più domini di errore. Un dominio di errore è un gruppo di macchine virtuali che condividono un'unità di alimentazione o un commutatore di rete comune. La distribuzione di macchine virtuali nei domini di errore limita l'impatto di guasti dell'hardware fisico, interruzioni di rete o interruzioni dell'alimentazione.

Ciò premesso, è comunque necessario integrare la resilienza nell'applicazione. Le strategie di resilienza possono essere applicate a tutti i livelli dell'architettura. Alcune mitigazioni sono di natura più tattica, ad esempio ritentare una chiamata remota dopo un errore di rete temporaneo. Altre mitigazioni sono più strategiche, come il failover dell'intera applicazione in un'area secondaria. Le mitigazioni tattiche possono fare una grande differenza. Sebbene sia raro che un'intera area subisca un'interruzione, i problemi temporanei, come la congestione della rete sono più comuni ed è quindi opportuno affrontarli per primi. Eseguire correttamente il monitoraggio e la diagnostica è importante sia per rilevare gli errori, sia per individuare le cause radice.

Quando si progetta un'applicazione resiliente, è necessario comprendere i requisiti di disponibilità. Quanto tempo di inattività è accettabile? La risposta è in parte in funzione dei costi. Quanto costa un potenziale tempo di inattività all'azienda? Quanto si deve investire per rendere l'applicazione a disponibilità elevata?

Usare l'[elenco di controllo per la resilienza] [ resiliency-checklist] per rivedere la progettazione dal punto di vista della resilienza.

### <a name="resiliency-guidance"></a>Informazioni aggiuntive sulla resilienza

- [Progettazione di applicazioni resilienti per Azure][resiliency]
- [Progettare gli schemi per la resilienza][resiliency-patterns]
- Procedure consigliate: [gestione degli errori temporanei][transient-fault-handling] e [indicazioni per la ripetizione dei tentativi per servizi specifici][retry-service-specific]

## <a name="management-and-devops"></a>Gestione e DevOps

Questo concetto fondamentale copre i processi operativi che mantengono un'applicazione in esecuzione in produzione.

Le distribuzioni devono essere affidabili e prevedibili. Dovrebbero essere automatizzate per ridurre la possibilità di errore umano. Dovrebbero essere un processo rapido e di routine, che non rallenta il rilascio di nuove funzionalità o correzioni di bug. Altrettanto importante è essere in grado di eseguire rapidamente il rollback o il roll forward in caso di problemi di aggiornamento.

Il monitoraggio e la diagnostica sono fondamentali. Le applicazioni cloud vengono eseguite in un data center remoto, in cui non si dispone del controllo completo dell'infrastruttura o, in alcuni casi, del sistema operativo. In un'applicazione di grandi dimensioni non è pratico accedere alle macchine virtuali per risolvere un problema o esaminare al dettaglio i file di log. Con i servizi PaaS potrebbe non essere nemmeno presente una macchina virtuale dedicata per l'accesso. Il monitoraggio e la diagnostica forniscono informazioni dettagliate sul sistema, in modo da conoscere quando e dove si verificano gli errori. Tutti i sistemi devono essere osservabili. Usare uno schema di registrazione comune e coerente che consente di correlare gli eventi tra i sistemi.

Il processo di monitoraggio e diagnostica ha diverse fasi distinte:

- Strumentazione. Generazione di dati non elaborati da log di applicazioni e server Web, dalla diagnostica integrata nella piattaforma Azure e da altre origini.
- Raccolta e archiviazione. Consolidamento dei dati in un'unica posizione.
- Analisi e diagnosi. Per risolvere i problemi ed esaminare l'integrità generale.
- Visualizzazione e avvisi. Utilizzo dei dati di telemetria per individuare tendenze o inviare avvisi ai team operativi.

Usare l'[elenco di controllo di DevOps] [ devops-checklist] per esaminare il progetto da un punto di vista della gestione e di DevOps.

### <a name="management-and-devops-guidance"></a>Indicazioni su Gestione e DevOps

- [Schemi progettuali per la gestione e il monitoraggio][management-patterns]
- Procedure consigliate: [Monitoraggio e diagnostica][monitoring]

## <a name="security"></a>Security

È necessario tenere presente la sicurezza durante l'intero ciclo di vita di un'applicazione, dalla progettazione e implementazione alla distribuzione e alle operazioni. La piattaforma Azure offre la protezione da un'ampia gamma di minacce, quali le intrusioni di rete e gli attacchi DDoS. È tuttavia necessario implementare la sicurezza nell'applicazione e nei processi DevOps.

Ecco alcune aree di sicurezza generali da considerare.

### <a name="identity-management"></a>Gestione delle identità

Prendere in considerazione l'utilizzo di Azure Active Directory (Azure AD) per autenticare e autorizzare gli utenti. Azure AD è un servizio di gestione delle identità e degli accessi completamente gestito. È possibile usarlo per creare domini esistenti esclusivamente in Azure o che si integrano con le identità di Active Directory locali. Azure AD si integra anche con Office 365, Dynamics CRM Online e numerose applicazioni SaaS di terze parti. Per le applicazioni rivolte al consumer, Azure Active Directory B2C consente agli utenti di autenticarsi con i propri account social esistenti (come Facebook, Google o LinkedIn) o di creare un nuovo account utente gestito da Azure AD.

Se si vuole integrare un ambiente Active Directory locale con una rete di Azure, sono possibili diversi approcci a seconda delle esigenze. Per altre informazioni, vedere le architetture di riferimento di [gestione delle identità] [ identity-ref-arch].

### <a name="protecting-your-infrastructure"></a>Protezione dell'infrastruttura

Controllare l'accesso alle risorse di Azure da distribuire. Ogni sottoscrizione di Azure ha una [relazione di trust][ad-subscriptions] con un tenant di Azure AD.
Usare il [controllo degli accessi in base al ruolo][rbac] per concedere agli utenti all'interno dell'organizzazione le autorizzazioni corrette per le risorse di Azure. Concedere l'accesso assegnando il ruolo RBAC agli utenti o ai gruppi in un determinato ambito. L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. [Controllare] [ resource-manager-auditing] tutte le modifiche all'infrastruttura.

### <a name="application-security"></a>Sicurezza delle applicazioni:

In generale, le procedure consigliate sulla sicurezza per lo sviluppo di applicazioni si applicano anche nel cloud. Sono inclusi elementi come l'utilizzo di SSL ovunque, la protezione da attacchi di tipo CSRF e XSS, la prevenzione di attacchi SQL injection e così via.

Le applicazioni cloud spesso usano servizi gestiti che dispongono di chiavi di accesso. Non eseguire mai questa verifica nel controllo del codice sorgente. È consigliabile archiviare i segreti dell'applicazione in Azure Key Vault.

### <a name="data-sovereignty-and-encryption"></a>Crittografia e sovranità dati

Assicurarsi che i dati rimangano nell'area geopolitica corretta quando si usa la disponibilità elevata di Azure. L'archiviazione di replica geografica di Azure usa il concetto di [area abbinata] [ paired-region] nella stessa area geopolitica.

Usare Key Vault per proteggere i segreti e le chiavi di crittografia. Con Key Vault è possibile crittografare chiavi e segreti usando chiavi protette da moduli di sicurezza hardware (HSM). Numerosi servizi di archiviazione e di database di Azure supportano la crittografia dei dati a riposo, tra cui [Archiviazione di Azure][storage-encryption], il [database SQL di Azure][sql-db-encryption], [ Microsoft Azure SQL Data Warehouse][data-warehouse-encryption] e [Cosmos DB][cosmosdb-encryption].

### <a name="security-resources"></a>Risorse di sicurezza

- [Centro sicurezza di Azure][security-center] integra il monitoraggio della sicurezza e la gestione dei criteri in tutte le sottoscrizioni di Azure.
- [Documentazione di Sicurezza di Azure][security-documentation]
- [Centro protezione Microsoft][trust-center]

<!-- links -->

[dr-guidance]: ../resiliency/disaster-recovery-azure-applications.md
[identity-ref-arch]: ../reference-architectures/identity/index.md
[resiliency]: ../resiliency/index.md

[ad-subscriptions]: /azure/active-directory/active-directory-how-subscriptions-associated-directory
[data-warehouse-encryption]: /azure/data-lake-store/data-lake-store-security-overview#data-protection
[cosmosdb-encryption]: /azure/cosmos-db/database-security
[rbac]: /azure/active-directory/role-based-access-control-what-is
[paired-region]: /azure/best-practices-availability-paired-regions
[resource-manager-auditing]: /azure/azure-resource-manager/resource-group-audit
[security-blog]: https://azure.microsoft.com/blog/tag/security/
[security-center]: https://azure.microsoft.com/services/security-center/
[security-documentation]: /azure/security/
[sql-db-encryption]: /azure/sql-database/sql-database-always-encrypted-azure-key-vault
[storage-encryption]: /azure/storage/storage-service-encryption
[trust-center]: https://azure.microsoft.com/support/trust-center/

<!-- patterns -->
[availability-patterns]: ../patterns/category/availability.md
[management-patterns]: ../patterns/category/management-monitoring.md
[resiliency-patterns]: ../patterns/category/resiliency.md
[scalability-patterns]: ../patterns/category/performance-scalability.md

<!-- practices -->
[autoscale]: ../best-practices/auto-scaling.md
[background-jobs]: ../best-practices/background-jobs.md
[caching]: ../best-practices/caching.md
[cdn]: ../best-practices/cdn.md
[data-partitioning]: ../best-practices/data-partitioning.md
[monitoring]: ../best-practices/monitoring.md
[retry-service-specific]: ../best-practices/retry-service-specific.md
[transient-fault-handling]: ../best-practices/transient-faults.md

<!-- checklist -->
[availability-checklist]: ../checklist/availability.md
[devops-checklist]: ../checklist/dev-ops.md
[resiliency-checklist]: ../checklist/resiliency.md
[scalability-checklist]: ../checklist/scalability.md
