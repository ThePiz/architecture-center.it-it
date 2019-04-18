---
title: Verifica della disponibilità e resilienza delle applicazioni Azure
description: Approcci per testare l'affidabilità delle applicazioni in Azure
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: cf33a859540810279b1cb85be5a6fb2ebe4500dc
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646642"
---
# <a name="testing-azure-applications-for-resiliency-and-availability"></a>Verifica della disponibilità e resilienza delle applicazioni Azure

Per testare la resilienza, è necessario verificare come viene eseguita il carico di lavoro end-to-end con le condizioni di errore intermittente.

Eseguire i test nell'ambiente di produzione usando sia i dati utente reali e sintetici. Test e produzione raramente sono identici. pertanto è importante convalidare l'applicazione in produzione usando un [blu-verde](https://martinfowler.com/bliki/BlueGreenDeployment.html) oppure [distribuzione canary](https://martinfowler.com/bliki/CanaryRelease.html). In questo modo, si sta testando l'applicazione in condizioni reali, in modo da avere la certezza che funzionerà come previsto al termine della distribuzione.

Come parte del piano di test, includere:

- Esecuzione di test pre-distribuzione automatizzati
- Il test fault injection
- Il test di carico di picco
- Test di ripristino di emergenza
- La verifica del servizio di terze parti

Il test è un processo iterativo: testare l'applicazione, misurare il risultato, analizzare e indirizzare gli eventuali errori che si verificano e ripetere il processo.

## <a name="perform-fault-injection-testing"></a>Eseguire il test fault injection

Per il test fault injection, verificare la resilienza del sistema durante gli errori, attivando errori reali sia simulandoli. Ecco alcune strategie per indurre errori:

- Arrestare le istanze di macchina virtuale (VM).
- Arrestare in modo anomalo i processi.
- Far scadere i certificati.
- Cambiare le chiavi di accesso.
- Arrestare il servizio DNS nei controller di dominio.
- Limitare le risorse di sistema disponibili, come la RAM o il numero di thread.
- Smontare i dischi.
- Ridistribuire una VM.

Il piano di test dovrebbe incorporare possibili punti di errore identificati durante la fase di progettazione, oltre a scenari di errore comuni:

- Testare l'applicazione in un ambiente più vicino possibile produzione.
- Errori di test nella combinazione.
- Misurare i tempi di ripristino e assicurarsi che siano soddisfatti i requisiti aziendali.
- Verificare che gli errori non supporta la propagazione e vengono gestiti in modo isolato.

Per altre informazioni sugli scenari di errore, vedere [errori e ripristino di emergenza per le applicazioni Azure](./disaster-recovery.md).

## <a name="test-under-peak-loads"></a>Test con carichi di picco

Il test di carico è fondamentale per l'identificazione di errori che si verificano solo in condizioni di carico, ad esempio il database back-end sovraccarico o la limitazione delle richieste di servizio. Test per carico di picco e aumento previsto nel carico di picco, usando dati di produzione oppure dati sintetici che siano il più vicino possibile i dati di produzione. L'obiettivo consiste nel verificare il comportamento dell'applicazione in condizioni reali.

## <a name="conduct-disaster-recovery-drills"></a>Condurre esercitazioni sul ripristino di emergenza

Iniziare con un piano di ripristino di emergenza valida e testarlo periodicamente per verificarne il che corretto funzionamento.

Macchine virtuali di Azure, è possibile usare [Azure Site Recovery](/azure/site-recovery/azure-to-azure-quickstart/) replicare ed eseguire [esercitazioni sul ripristino di emergenza](/azure/site-recovery/azure-to-azure-tutorial-dr-drill/) senza conseguenze per le applicazioni di produzione o sulla replica continua.

### <a name="failover-and-failback-testing"></a>Failover e failback di test

Testare il failover e failback per verificare che i servizi dipendenti dell'applicazione tornare attivo in modo sincronizzato durante il ripristino di emergenza. I sistemi e alle operazioni potrebbero influire sul failover e il failback funzioni, ma l'impatto potrebbe non essere rilevato fino a quando il sistema principale ha esito negativo o è in overload. Le funzionalità di failover di test *prima di* sono tenuti a compensare un problema in tempo reale. Assicurarsi anche che i servizi dipendenti failover e nuovamente nell'ordine corretto.

Se si usa [Azure Site Recovery](/azure/site-recovery/) per replicare le macchine virtuali, eseguire emergenza esercitazioni sul ripristino periodicamente in questo modo i failover di test per convalidare la strategia di replica. Un failover di test non influisce sulla replica di VM in corso o sull'ambiente di produzione. Per altre informazioni, vedere [eseguire un'esercitazione sul ripristino di emergenza in Azure](/azure/site-recovery/site-recovery-test-failover-to-azure).

### <a name="simulation-testing"></a>I test di simulazione

Test di simulazione prevedono la creazione di situazioni di piccole dimensioni, reale. Le simulazioni di illustrano l'efficacia delle soluzioni nel piano di ripristino ed evidenziano eventuali problemi che non sono stati risolti in modo adeguato.

Quando si eseguono i test di simulazione, seguire le procedure consigliate:

- Eseguire simulazioni in modo che non interrompano le attività aziendali effettive ma l'aspetto di una situazione reale.
- Assicurarsi che gli scenari simulati sono completamente controllabili. Se il piano di ripristino ha esito negativo, è possibile ripristinare la situazione al normale senza causare danni.
- Informare i dirigenti sulla come e quando verranno eseguiti gli esercizi di simulazione. Il piano deve descrivere in dettaglio l'intervallo di tempo e le risorse coinvolte durante la simulazione.

## <a name="test-health-probes-for-load-balancers-and-traffic-managers"></a>Testare i probe di integrità per i bilanciamenti del carico e strumenti di gestione traffico

Configurare e testare i probe di integrità per i servizi di bilanciamento del carico e strumenti di gestione traffico. Assicurarsi che l'endpoint di integrità controlli le parti critiche del sistema e risponda in modo appropriato.

- Per la [gestione traffico di Azure](/azure/traffic-manager/traffic-manager-overview/), il probe di integrità determina se eseguire il failover in un'altra area. L'endpoint di integrità deve verificare eventuali dipendenze critiche che vengono distribuiti nella stessa area.
- Per la [Azure Load Balancer](/azure/load-balancer/load-balancer-overview/), il probe di integrità determina se si desidera rimuovere una macchina virtuale dalla rotazione. L'endpoint di integrità deve segnalare l'integrità della macchina virtuale. Non includere altri livelli o servizi esterni. In caso contrario, se si verifica un errore all'esterno della macchina virtuale, il servizio di bilanciamento del carico rimuove la macchina virtuale dalla rotazione.

Per informazioni sull'implementazione del monitoraggio dell'integrità nell'applicazione, vedere [Modello di monitoraggio dell’integrità dell’endpoint](../patterns/health-endpoint-monitoring.md).

## <a name="test-monitoring-systems"></a>Testare sistemi di monitoraggio

Includono sistemi di monitoraggio nel piano di test. I sistemi automatizzati di failover e failback dipendono dal corretto funzionamento del monitoraggio e della strumentazione. Dashboard per visualizzare gli avvisi di integrità e l'operatore di sistema dipendono anche disponendo accurata di monitoraggio e strumentazione. In caso di errore di questi elementi, in assenza di informazioni importanti o in caso di segnalazione di dati non precisi, un operatore potrebbe non rendersi conto che il sistema è danneggiato o guasto.

## <a name="include-third-party-services-in-test-scenarios"></a>Includere servizi di terze parti in scenari di test

Se l'applicazione ha dipendenze in servizi di terze parti, identificare dove e come questi servizi possono avere esito negativo e sugli effetti di tali errori avrà sull'applicazione. Tenere presente il contratto di servizio (SLA) per il servizio di terze parti e l'effetto che potrebbe avere del piano di ripristino di emergenza.

Un servizio di terze parti potrebbe non fornire le funzionalità di monitoraggio e diagnostica, pertanto è importante per le chiamate di log e metterle in correlazione con l'integrità dell'applicazione e di diagnostica utilizzando un identificatore univoco di registrazione. Per altre informazioni sulle pratiche comprovate di monitoraggio e diagnostica, vedere [indicazioni di monitoraggio e diagnostica](../best-practices/monitoring.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire per la resilienza e disponibilità](./deploy.md)
