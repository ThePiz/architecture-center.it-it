---
title: Rendere ogni elemento ridondante
description: Per evitare singoli punti di guasto, è consigliabile applicare la ridondanza nell'applicazione.
author: MikeWasson
ms.date: 08/30/2018
ms.openlocfilehash: 18f48ea37f90846e4d337819e6c897d56e472d49
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47428041"
---
# <a name="make-all-things-redundant"></a>Rendere ogni elemento ridondante

## <a name="build-redundancy-into-your-application-to-avoid-having-single-points-of-failure"></a>Applicare la ridondanza nell'applicazione per evitare singoli punti di guasto

Un'applicazione resiliente consente di risolvere più facilmente gli errori. È quindi opportuno identificare i percorsi critici nell'applicazione e verificare se in ogni punto del percorso sia prevista una certa ridondanza, in modo che in caso di errore di un sottosistema, l'applicazione possa effettuare il failover in un altro sottosistema.

## <a name="recommendations"></a>Consigli 

**Prendere in considerazione i requisiti aziendali**. La ridondanza implementata in un sistema può influire sui costi e sulla complessità del sistema stesso. L'architettura deve quindi tenere in considerazione i requisiti aziendali, tra cui il cosiddetto RTO (Recovery Time Objective), ovvero il tempo necessario per il recupero dell'operatività. Ad esempio, una distribuzione in più aree è più costosa rispetto a una in una singola area ed è più complicata da gestire. È quindi necessario definire apposite procedure operative per gestire il failover e il failback. L'incremento dei costi e la maggiore complessità potrebbero essere giustificabili per alcuni scenari aziendali e non per altri.

**Prevedere un servizio di bilanciamento del carico prima delle macchine virtuali**. Non usare una singola macchina virtuale per carichi di lavoro critici, ma prevedere un servizio di bilanciamento del carico da inserire prima delle macchine virtuali. In questo modo se una qualsiasi macchina virtuale non è più disponibile, il servizio di bilanciamento del carico distribuirà il traffico alle rimanenti macchine virtuali integre. Per informazioni su come distribuire questa configurazione, vedere [Run load-balanced VMs for scalability and availability][multi-vm-blueprint] (Eseguire macchine virtuali con bilanciamento del carico per la scalabilità e la disponibilità).

![](./images/load-balancing.svg)

**Replicare i database**. Il database SQL e Cosmos DB replicano automaticamente i dati in un'area. È inoltre possibile abilitare la replica geografica tra le diverse aree. Se si usa una soluzione con database IaaS, sceglierne una che supporti la replica e il failover, ad esempio i [gruppi di disponibilità Always On di SQL Server][sql-always-on]. 

**Abilitare la replica geografica**. La replica geografica per il [database SQL di Azure][sql-geo-replication] e [Cosmos DB][cosmosdb-geo-replication] consente di creare repliche leggibili secondarie dei dati in una o più aree secondarie. In caso di interruzione del servizio, il database potrà quindi effettuare il failover all'area secondaria per le operazioni di scrittura.

**Usare il partizionamento per garantire la disponibilità**. Il partizionamento del database viene spesso usato per migliorare la scalabilità, ma può anche consentire di migliorare la disponibilità. In caso di malfunzionamento di una partizione, è comunque possibile raggiungere le altre. Un errore in una partizione danneggerà inoltre solo un subset delle transazioni totali. 

**Eseguire la distribuzione in più aree**. Per garantire la massima disponibilità, distribuire l'applicazione in più aree. In tal modo, nel raro caso che un problema interessi un'intera area, l'applicazione potrà effettuare il failover a un'altra area. Il diagramma seguente mostra un'applicazione in più aree che usa Gestione traffico di Azure per gestire il failover.

![](images/failover.svg)

**Sincronizzare il failover front-end e back-end**. Usare Gestione traffico di Azure per effettuare il failover del front-end. Se il front-end diventa irraggiungibile in un'area, Gestione traffico inoltrerà le nuove richieste all'area secondaria. A seconda della soluzione di database usata, potrebbe essere necessario coordinare il failover del database. 

**Usare il failover automatico e il failback manuale**. Usare Gestione traffico per il failover automatico, ma non per il failback automatico. Il failback automatico è infatti rischioso perché il passaggio all'area primaria potrebbe avvenire prima che l'area sia completamente integra. Verificare invece che tutti i sottosistemi dell'applicazione siano integri prima di eseguire il failback manuale. A seconda del database, inoltre, potrebbe essere necessario verificare la coerenza dei dati prima del failback.

**Includere la ridondanza per Gestione traffico**. Gestione traffico costituisce un possibile punto di guasto. Rivedere il contratto di servizio di Gestione traffico e determinare se l'uso di Gestione traffico da solo soddisfa i requisiti aziendali per la disponibilità elevata. In caso contrario, provare ad aggiungere un'altra soluzione di gestione del traffico come failback. In caso di errore del servizio Gestione traffico di Azure, modificare i record CNAME in DNS in modo che puntino all'altro servizio di gestione del traffico.



<!-- links -->

[multi-vm-blueprint]: ../../reference-architectures/virtual-machines-windows/multi-vm.md

[cassandra]: https://cassandra.apache.org/
[cosmosdb-geo-replication]: /azure/cosmos-db/distribute-data-globally
[sql-always-on]: https://msdn.microsoft.com/library/hh510230.aspx
[sql-geo-replication]: /azure/sql-database/sql-database-geo-replication-overview
