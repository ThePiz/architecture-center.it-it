---
title: Relazione di trust decentralizzata tra le banche in Azure
description: Creare un ambiente attendibile per la comunicazione e la condivisione di informazioni senza dover ricorrere a un database centralizzato.
author: vitoc
ms.date: 09/09/2018
ms.openlocfilehash: fe27f885635ce5ae4ce368992affa1a85d7af416
ms.sourcegitcommit: 62945777e519d650159f0f963a2489b6bb6ce094
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48876759"
---
# <a name="decentralized-trust-between-banks-on-azure"></a>Relazione di trust decentralizzata tra le banche in Azure

Questo scenario di esempio è utile per le banche o altre organizzazioni che vogliono creare un ambiente attendibile per la condivisione delle informazioni senza dover ricorrere a un database centralizzato. Lo scenario descritto in questo esempio è relativo alla necessità di mantenere le informazioni sull'affidabilità creditizia nell'ambito delle banche, ma l'architettura può essere applicata a qualsiasi scenario in cui le organizzazioni di un consorzio vogliono condividere tra loro informazioni convalidate senza dover ricorrere a un sistema centrale eseguito da una sola parte.

In genere, le banche che fanno parte di un sistema finanziario si affidano a fonti centralizzate, ad esempio alle centrali dei rischi, per ottenere informazioni sulla storia e sull'affidabilità creditizie di una persona. Un approccio centralizzato presenta una serie di rischi operativi e, a volte, una terza parte non necessaria.

Con le tecnologie basate sul libro mastro distribuito (DLT, Distributed Ledger Technology), un consorzio di banche può creare un sistema decentralizzato che potrà essere più efficiente, meno soggetto ad attacchi ed essere usato come nuova piattaforma in cui implementate strutture innovative per risolvere le classiche problematiche relative a privacy, velocità e costi.

Questo esempio illustra come sia possibile effettuare rapidamente il provisioning dei servizi di Azure, ad esempio set di scalabilità di macchine virtuali, Rete virtuale, Key Vault, Archiviazione, Load Balancer e Monitoraggio, per la distribuzione di un efficiente blockchain Ethereum PoA privato in cui le banche membro possono stabilire i propri nodi.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Questi altri casi d'uso hanno schemi progettuali simili:

* Spostamento dei budget allocati tra diverse unità aziendali di una società multinazionale
* Pagamenti internazionali
* Scenari di trade finance
* Sistemi di fedelizzazione che coinvolgono diverse società
* Ecosistemi di catene di approvvigionamento e molto altro ancora

## <a name="architecture"></a>Architettura

![Diagramma dell'architettura di attendibilità di banche decentralizzata](./media/architecture-decentralized-trust.png)

Questo scenario illustra i componenti back-end necessari per creare una scalabile, una rete di blockchain aziendale privata scalabile, sicura e monitorata all'interno di un consorzio di due o più membri. I dettagli relativi a come effettuare il provisioning di questi componenti (ad esempio, all'interno di gruppi di risorse e sottoscrizioni diversi), nonché i requisiti di connettività (ad esempio, VPN o ExpressRoute) sono a discrezione dell'utente perché dipendono dai requisiti per i criteri dell'organizzazione. Ecco il flusso dei dati:

1. La banca A crea/aggiorna il record creditizio di una persona inviando una transazione alla rete di blockchain tramite JSON-RPC.
2. I dati passano dal server applicazioni privato della banca A ad Azure Load Balancer e successivamente a una macchina virtuale del nodo di convalida nel set di scalabilità di macchine virtuali.
3. La rete Ethereum PoA crea un blocco a un'ora prestabilita (2 secondi per questo scenario).
4. La transazione viene aggregata al blocco creato e convalidata nella rete di blockchain.
5. La banca B può leggere il record creditizio creato dalla banca A comunicando in modo simile con il proprio nodo tramite JSON-RPC.

### <a name="components"></a>Componenti

* Le macchine virtuali nei set di scalabilità di macchine virtuali forniscono la struttura di calcolo on demand per ospitare i processi dei validator per la blockchain
* Key Vault viene usato come struttura di archiviazione sicura per le chiavi private di ogni validator
* Load Balancer distribuisce le richieste di applicazioni decentralizzate per governance, peering e RPC
* Archiviazione ospita le informazioni di rete persistenti e coordinare il leasing
* Operations Management Suite (un bundle di alcuni servizi di Azure) fornisce informazioni approfondite su nodi disponibili, transazioni al minuto e membri del consorzio

### <a name="alternatives"></a>Alternative

Per questo esempio viene scelto l'approccio basato su Ethereum PoA perché è un punto di ingresso ideale per un consorzio di organizzazioni che vogliono creare un ambiente in cui le informazioni possono facilmente essere scambiate e condivise reciprocamente in modo attendibile, decentralizzato e semplice. Grazie ai modelli di soluzione di Azure disponibili, non solo il leader di un consorzio può avviare una blockchain Ethereum PoA in modo pratico e veloce, ma allo stesso modo le organizzazioni membro del consorzio possono creare le risorse di Azure nel proprio gruppo di risorse e nella propria sottoscrizione per accedere a una rete esistente.

Per altri scenari diversi o estesi, possono insorgere problematiche come la privacy relativa alle transazioni. In uno scenario di trasferimento di titoli, ad esempio, i membri in un consorzio potrebbero non volere che le transazioni siano visibili anche agli altri membri. Esistono alternative a Ethereum PoA, che gestiscono queste problematiche in modo specifico:

* Corda
* Quorum
* Hyperledger

## <a name="considerations"></a>Considerazioni

### <a name="availability"></a>Disponibilità

[Monitoraggio di Azure][monitor] viene usato per monitorare costantemente la rete di blockchain per poter rilevare eventuali problemi e garantire la disponibilità. Dopo il completamento della distribuzione del modello di soluzione blockchain usato in questo scenario, si riceverà un collegamento a un dashboard di monitoraggio personalizzato basato su Monitoraggio di Azure. Il dashboard mostra i nodi che segnalano heartbeat nei 30 minuti precedenti, nonché altri utili statistiche. 

Per altri argomenti relativi alla disponibilità, vedere l'[elenco di controllo per la disponibilità][availability] in Centro architetture Azure.

### <a name="scalability"></a>Scalabilità

Un problema comune per la blockchain è il numero di transazioni che può includere in un periodo di tempo prestabilito. Questo scenario usa Proof-of-Authority in cui tale scalabilità può essere gestita meglio che in Proof-of-Work. Nelle reti basate su Proof-of-Authority i partecipanti al consenso sono noti e gestiti ed è quindi più adatto a una blockchain privata per un consorzio di organizzazioni che si conoscono tra loro. I parametri come il tempo medio di blocco, le transazioni al minuto e il consumo delle risorse di calcolo possono essere monitorati con facilità tramite il dashboard personalizzato. Le risorse possono quindi essere modificate di conseguenza in base ai requisiti di scalabilità.

Per indicazioni generali sulla progettazione di uno scenario scalabile, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Sicurezza

[Azure Key Vault][vault] viene usato per archiviare e gestire facilmente le chiavi private dei validator. La distribuzione predefinita in questo esempio crea una rete di blockchain accessibile tramite Internet. Per gli scenari di produzione in cui è necessaria una rete privata, i membri possono essere connessi tra loro tramite connessioni gateway VPN tra reti virtuali. I passaggi per configurare una VPN sono inclusi nella sezione Risorse correlate di seguito.

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

La blockchain Ethereum PoA può offrire di per sé un certo livello di resilienza perché i nodi dei validator possono essere distribuiti in aree diverse. Azure consente di eseguire le distribuzioni in più di 54 aree in tutto il mondo. Una blockchain come quella usata in questo scenario offre possibilità di cooperazione uniche e sempre nuove per aumentare la resilienza. La resilienza della rete non viene fornita solo per una singola entità centralizzata, ma per tutti i membri del consorzio. Una blockchain basata su Proof-of-Authority consente di pianificare e valutare ancora meglio la resilienza della rete.

Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].

## <a name="pricing"></a>Prezzi

Per esaminare il costo di esecuzione dello scenario, nel calcolatore dei costi sono preconfigurati tutti i servizi. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base ai requisiti di prestazioni e disponibilità previsti.

Sono disponibili tre profili di costo di esempio, basati sul numero di istanze di macchina virtuale del set di scalabilità che eseguono le applicazioni. Le istanze possono trovarsi in aree diverse.

* [Small][small-pricing]: questo esempio di prezzo è correlato a 2 macchine virtuali al mese con il monitoraggio disattivato
* [Medium][medium-pricing]: questo esempio di prezzo è correlato a 7 macchine virtuali al mese con il monitoraggio attivato
* [Large][large-pricing]: questo esempio di prezzo è correlato a 15 macchine virtuali al mese con il monitoraggio attivato

I prezzi indicati in precedenza sono per un membro del consorzio che deve avviare o aggiungere una rete di blockchain. In genere, in un consorzio in cui sono coinvolte più aziende o organizzazioni ogni membro otterrà la propria sottoscrizione di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per un esempio di questo scenario, distribuire l'[applicazione demo di blockchain Ethereum PoA][deploy] in Azure, quindi esaminare il [file leggimi del codice sorgente dello scenario][source].

## <a name="related-resources"></a>Risorse correlate

Per altre informazioni sull'uso del modello di soluzione Ethereum Proof-of-Authority per Azure, vedere questa [guida all'utilizzo][guide].

<!-- links -->
[small-pricing]: https://azure.com/e/4e429d721eb54adc9a1558fae3e67990
[medium-pricing]: https://azure.com/e/bb42cd77437744be8ed7064403bfe2ef
[large-pricing]: https://azure.com/e/e205b443de3e4adfadf4e09ffee30c56
[guide]: /azure/blockchain-workbench/ethereum-poa-deployment
[deploy]: https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium
[source]: https://github.com/vitoc/creditscoreblockchain
[monitor]: /azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor
[availability]: /azure/architecture/checklist/availability
[scalability]: /azure/architecture/checklist/scalability
[resiliency]: ../../resiliency/index.md
[security]: /azure/security/
[vault]: https://azure.microsoft.com/services/key-vault/
