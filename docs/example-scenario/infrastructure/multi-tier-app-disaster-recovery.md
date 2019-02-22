---
title: Applicazione Web multilivello creata per la disponibilità elevata e il ripristino di emergenza
titleSuffix: Azure Example Scenarios
description: Creare un'applicazione Web multilivello per la disponibilità elevata e il ripristino di emergenza in Azure usando macchine virtuali di Azure, set di disponibilità, zone di disponibilità, Azure Site Recovery e Gestione traffico di Azure.
author: sujayt
ms.date: 11/16/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: product-team
social_image_url: /azure/architecture/example-scenario/infrastructure/media/arhitecture-disaster-recovery-multi-tier-app.png
ms.openlocfilehash: 1f82f0bf5421bb251bda2eb60349cc74014fd454
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55898103"
---
# <a name="multitier-web-application-built-for-high-availability-and-disaster-recovery-on-azure"></a>Applicazione Web multilivello per disponibilità elevata e ripristino di emergenza in Azure

Questo scenario di esempio è applicabile a qualsiasi settore che deve eseguire la distribuzione di applicazioni resilienti multilivello create per la disponibilità elevata e il ripristino di emergenza. In questo scenario, l'applicazione è costituita da tre livelli.

- Livello Web: il livello più alto che include l'interfaccia utente. Questo livello analizza le interazioni dell'utente e passa le azioni al livello successivo per l'elaborazione.
- Livello Business: elabora le interazioni degli utenti e prende decisioni logiche sui passaggi successivi. Questo livello connette il livello Web al livello dati.
- Livello dati: archivia i dati dell'applicazione. In genere viene usato un database, l'archiviazione di oggetti o l'archiviazione file.

Scenari applicativi comuni includono eventuali applicazioni di importanza cruciale in esecuzione su Windows o Linux. Può trattarsi di un'applicazione commerciale, ad esempio SAP e SharePoint, o di un'applicazione line-of-business personalizzata.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

- Distribuzione di applicazioni altamente resilienti, come SAP e SharePoint
- Progettazione di un piano di continuità aziendale e ripristino di emergenza per applicazioni line-of-business
- Configurare il ripristino di emergenza ed eseguire esercitazioni correlate per motivi di conformità

## <a name="architecture"></a>Architettura

Questo scenario dimostra un'applicazione multilivello che usa ASP.NET e Microsoft SQL Server. Nelle [aree di Azure che supportano le zone di disponibilità](/azure/availability-zones/az-overview#regions-that-support-availability-zones), è possibile distribuire le macchine virtuali (VM) in un'area di origine tra le zone di disponibilità e replicare le macchine virtuali nell'area di destinazione usata per il ripristino di emergenza. Nelle aree di Azure che non supportano le zone di disponibilità, è possibile distribuire le macchine virtuali nell'ambito di una zona di disponibilità e replicare le macchine virtuali nell'area di destinazione.

![Panoramica dell'architettura di un'applicazione Web multilivello altamente resiliente][architecture]

- Distribuire le macchine virtuali in ogni livello tra due zone di disponibilità in aree che supportano le zone. In altre aree, distribuire le macchine virtuali in ogni livello all'interno di un set di disponibilità.
- Il livello di database può essere configurato per l'uso dei gruppi di disponibilità AlwaysOn. Con questa configurazione di SQL Server, un database primario in un cluster viene configurato con un massimo di otto database secondari. Se si verifica un problema con il database primario, il cluster esegue il failover a uno dei database secondari, favorendo la disponibilità continua dell'applicazione. Per altre informazioni, vedere [Panoramica di Gruppi di disponibilità AlwaysOn (SQL Server)][docs-sql-always-on].
- Per scenari di ripristino di emergenza, è possibile configurare la replica nativa asincrona SQL Always On nell'area di destinazione usata per il ripristino di emergenza. È anche possibile configurare la replica di Azure Site Recovery nell'area di destinazione se la frequenza di modifica dei dati rientra nei limiti supportati di Azure Site Recovery.
- Gli utenti accedono al livello Web ASP.NET front-end dall'endpoint di Gestione traffico.
- Gestione traffico reindirizza il traffico all'endpoint IP pubblico primario nell'area di origine primaria.
- L'indirizzo IP pubblico reindirizza la chiamata a una delle istanze di VM a livello web con un servizio di bilanciamento del carico pubblico. Tutte le istanze VM a livello Web si trovano in un'unica subnet.
- Dalla VM a livello Web ogni chiamata viene instradata a una delle istanze VM nel livello business attraverso un servizio di bilanciamento del carico interno per l'elaborazione. Tutte le VM a livello business si trovano in una subnet separata.
- L'operazione viene elaborata nel livello business e l'applicazione ASP.NET si connette al cluster Microsoft SQL Server in un livello back-end tramite un servizio di bilanciamento del carico interno di Azure. Le istanze di SQL Server di back-end si trovano in una subnet separata.
- L'endpoint secondario di gestione traffico è configurato come indirizzo IP pubblico nell'area di destinazione usata per il ripristino di emergenza.
- In caso di interruzione di un'area primaria, richiamare il failover di Azure Site Recovery per attivare l'applicazione nell'area di destinazione.
- L'endpoint di gestione traffico reindirizza automaticamente il traffico dei client all'indirizzo IP pubblico nell'area di destinazione.

### <a name="components"></a>Componenti

- I [set di disponibilità][docs-availability-sets] assicurano che le macchine virtuali distribuite in Azure vengano distribuite tra più nodi hardware isolati in un cluster. Se si verifica un errore hardware o software all'interno di Azure, solo un subset delle macchine virtuali viene interessato e l'intera soluzione rimane disponibile e operativa.
- Le [zone di disponibilità][docs-availability-zones] proteggono le applicazioni e i dati dai guasti del data center. Le zone di disponibilità sono località fisiche separate all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete.
- [Azure Site Recovery (ASR)][docs-azure-site-recovery] consente di replicare le macchine virtuali in un'altra area di Azure per esigenze di continuità aziendale e ripristino di emergenza. È possibile condurre esercitazioni periodiche sul ripristino di emergenza per assicurarsi di soddisfare le esigenze di conformità. La VM verrà replicata con le impostazioni specificate nell'area selezionata in modo da consentire il ripristino delle applicazioni in caso di interruzioni nell'area di origine.
- [Gestione traffico di Azure][docs-traffic-manager] è un servizio di bilanciamento del carico basato su DNS che distribuisce il traffico in modo ottimale ai servizi nelle aree globali di Azure, offrendo al tempo stesso disponibilità e velocità di risposta elevate.
- [Azure Load Balancer][docs-load-balancer] distribuisce il traffico in ingresso in base a regole e probe di integrità definiti. Un servizio di bilanciamento del carico offre bassa latenza e velocità effettiva elevata, aumentando le prestazioni fino a milioni di flussi per tutte le applicazioni TCP e UDP. In questo scenario viene usato un servizio di bilanciamento del carico pubblico per distribuire il traffico client nel livello Web. Questo scenario usa un servizio di bilanciamento del carico interno per distribuire il traffico dal livello business al cluster SQL Server di back-end.

### <a name="alternatives"></a>Alternative

- Windows può essere sostituito da altri sistemi operativi, perché l'infrastruttura non dipende dal sistema operativo.
- [SQL Server per Linux][docs-sql-server-linux] può sostituire l'archivio dati di back-end.
- Il database può essere sostituito da qualsiasi applicazione di database standard disponibile.

## <a name="other-considerations"></a>Altre considerazioni

### <a name="scalability"></a>Scalabilità

È possibile aggiungere o rimuovere le macchine virtuali in ogni livello in base ai requisiti di ridimensionamento. Poiché questo scenario usa servizi di bilanciamento del carico, è possibile aggiungere altre macchine virtuali a un livello senza influire sul tempo di attività dell'applicazione.

Per altri argomenti relativi alla scalabilità, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Security

Tutto il traffico di rete virtuale nel livello applicazione front-end è protetto da gruppi di sicurezza di rete. Le regole limitano il flusso del traffico in modo che solo le istanze di macchina virtuale a livello applicazione front-end possano accedere al livello di database di backend. Non è consentito il traffico Internet in uscita dal livello business o di database. Per ridurre la superficie di attacco, non sono aperte porte di gestione remota diretta. Per altre informazioni, vedere [Gruppi di sicurezza di rete di Azure][docs-nsg].

Per indicazioni generali sulla progettazione di scenari sicuri, vedere la [documentazione sulla sicurezza di Azure][security].

## <a name="pricing"></a>Prezzi

Per la configurazione del ripristino di emergenza per macchine virtuali di Azure con Azure Site Recovery verranno regolarmente applicati gli addebiti seguenti.

- Costo della licenza di Azure Site Recovery per ogni VM.
- Costi per l'uscita dei dati in rete per replicare le modifiche ai dati dai dischi della macchina virtuale di origine in un'altra area di Azure. Azure Site Recovery usa funzionalità di compressione incorporata per ridurre i requisiti di trasferimento dei dati di circa il 50%.
- Costi di archiviazione nel sito di ripristino. Ciò equivale in genere allo spazio di archiviazione dell'area di origine oltre a qualsiasi spazio di archiviazione aggiuntivo necessario per mantenere i punti di ripristino come snapshot per il ripristino.

Microsoft ha fornito una [calcolatore dei costi di esempio][calculator] per la configurazione del ripristino di emergenza per un'applicazione a tre livelli con sei macchine virtuali. Nel calcolatore dei costi sono preconfigurati tutti i servizi. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate per stimare il costo.

<!-- links -->
[architecture]: ./media/arhitecture-disaster-recovery-multi-tier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: ../../checklist/availability.md
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[docs-availability-zones]: /azure/availability-zones/az-overview
[docs-load-balancer]: /azure/load-balancer/load-balancer-overview
[docs-nsg]: /azure/virtual-network/security-overview
[docs-vmss]: /azure/virtual-machine-scale-sets/overview
[docs-sql-always-on]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[docs-vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[docs-vnet]: /azure/virtual-network/virtual-networks-overview
[docs-sql-server-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[docs-traffic-manager]: /azure/traffic-manager/
[docs-azure-site-recovery]: /azure/site-recovery/azure-to-azure-quickstart/
[docs-availability-sets]: /azure/virtual-machines/windows/manage-availability/
[calculator]: https://azure.com/e/6835332265044d6d931d68c917979e6d/
