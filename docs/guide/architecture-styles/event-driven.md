---
title: Stile di architettura guidato dagli eventi
description: Descrive i vantaggi, le problematiche e le procedure consigliate per le architetture guidate dagli eventi e IoT in Azure
author: MikeWasson
ms.openlocfilehash: 3289bf784b02d62e3d0c1a29b4839c9be3501134
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
ms.locfileid: "29478324"
---
# <a name="event-driven-architecture-style"></a>Stile di architettura guidato dagli eventi

Un'architettura guidata dagli eventi è costituita da **producer eventi** che generano un flusso di eventi e **consumer eventi** che sono in ascolto degli eventi. 

![](./images/event-driven.svg)

Gli eventi vengono recapitati praticamente in tempo reale, in modo che i consumer possano rispondervi immediatamente non appena si verificano. I producer sono separati dai consumer: un producer è all'oscuro dei consumer in ascolto. Anche i consumer sono separati tra loro e ognuno visualizza tutti gli eventi. Questo comportamento differisce da un modello con [consumer concorrenti][competing-consumers], in cui i consumer eseguono il pull di messaggi da una coda e un messaggio viene elaborato solo una volta (presupponendo l'assenza di errori). In alcuni sistemi, ad esempio nei sistemi IoT, gli eventi devono essere inseriti a volumi molto elevati.

Un'architettura guidata dagli eventi può usare un modello di pubblicazione/sottoscrizione o un modello di flusso di eventi. 

- **Pubblicazione/sottoscrizione**: l'infrastruttura di messaggistica tiene traccia delle sottoscrizioni. Quando viene pubblicato un evento, il modello lo invia a ogni sottoscrittore. Quando viene ricevuto un evento, non può essere riprodotto e i nuovi sottoscrittori non possono visualizzarlo. 

- **Flusso di eventi**: gli eventi vengono scritti in un log. Gli eventi sono rigorosamente ordinati (in una partizione) e durevoli. I client non sottoscrivono il flusso, ma un client può invece leggere da qualsiasi parte del flusso. Il client è responsabile di far avanzare la propria posizione nel flusso. Questo significa che un client può aggiungersi in qualsiasi momento e può riprodurre gli eventi.

Sul lato consumer si applicano alcune variazioni comuni:

- **Elaborazione semplice degli eventi**. Un evento attiva immediatamente un'azione nel consumer. Ad esempio, è possibile usare Funzioni di Azure con un trigger del bus di servizio, in modo da eseguire una funzione ogni volta che viene pubblicato un messaggio in un argomento del bus di servizio.

- **Elaborazione complessa degli eventi**. Un consumer elabora una serie di eventi, cercando i modelli nei dati di evento, tramite una tecnologia come Analisi di flusso di Azure o Apache Storm. Ad esempio, è possibile aggregare letture da un dispositivo incorporato in base a un intervallo di tempo e quindi generare una notifica se la media mobile supera una determinata soglia. 

- **Elaborazione di flussi di eventi**. Usare una piattaforma di flussi di dati, come l'hub IoT di Azure o Apache Kafka, come pipeline per inserire gli eventi e fornirli agli elaboratori di flussi. Gli elaboratori di flussi intervengono per elaborare o trasformare il flusso. Possono essere presenti più elaboratori di flussi per sottosistemi diversi nell'applicazione. Questo approccio è ideale per i carichi di lavoro IoT.

L'origine degli eventi può essere esterna al sistema, ad esempio può essere costituita da dispositivi fisici in una soluzione IoT. In questo caso, il sistema deve essere in grado di inserire i dati in base alla velocità effettiva e al volume richiesti dall'origine dati.

Nel diagramma logico raffigurato sopra ogni tipo di consumer viene mostrato come singola casella. In pratica, è prassi comune definire più istanze di un consumer, per evitare che il consumer diventi un singolo punto di guasto nel sistema. Possono essere necessarie più istanze anche per gestire il volume e la frequenza degli eventi. Inoltre, un singolo consumer può elaborare gli eventi in più thread. Questo approccio può rivelarsi problematico se gli eventi devono essere elaborati in ordine o se richiedono una semantica di recapito effettuato esattamente una volta. Vedere [Ridurre al minimo il coordinamento][minimize-coordination]. 

## <a name="when-to-use-this-architecture"></a>Quando usare questa architettura

- Più sottosistemi devono elaborare gli stessi eventi. 
- Elaborazione in tempo reale con ritardo minimo.
- Elaborazione complessa degli eventi, ad esempio con criteri di ricerca o aggregazione in base a intervalli di tempo.
- Volume elevato e alta velocità dei dati, ad esempio in sistemi IoT.

## <a name="benefits"></a>Vantaggi

- I producer e i consumer sono separati.
- Nessuna integrazione da punto a punto. È facile aggiungere nuovi consumer al sistema.
- I consumer possono rispondere agli eventi immediatamente, non appena arrivano. 
- Scalabilità e distribuzione elevate. 
- I sottosistemi hanno viste indipendenti del flusso di eventi.

## <a name="challenges"></a>Problematiche

- Recapito garantito. In alcuni sistemi, in particolare in scenari IoT, è essenziale garantire che gli eventi vengano recapitati.
- Elaborazione degli eventi in ordine o esattamente una volta. In genere, ogni tipo di consumer viene eseguito in più istanze, per motivi di resilienza e scalabilità. Questo approccio può rivelarsi problematico se gli eventi devono essere elaborati in ordine (all'interno di un tipo di consumer) o se la logica di elaborazione non è idempotente.

## <a name="iot-architecture"></a>Architettura IoT

Le architetture guidate dagli eventi sono centrali per le soluzioni IoT. Il diagramma seguente mostra una possibile architettura logica per IoT. Il diagramma evidenzia i componenti del flusso di eventi dell'architettura.

![](./images/iot.png)

Il **gateway cloud** inserisce gli eventi di dispositivo in corrispondenza dei limiti del cloud, usando un sistema di messaggistica a bassa latenza affidabile.

I dispositivi possono inviare eventi direttamente al gateway cloud oppure attraverso un **gateway sul campo**. Un gateway sul campo è un dispositivo o un software specializzato che si trova in genere nella stessa posizione dei dispositivi e che riceve gli eventi e li inoltra al gateway cloud. Il gateway sul campo può anche pre-elaborare gli eventi di dispositivo non elaborati, eseguendo funzioni come l'applicazione di filtri, l'aggregazione o la trasformazione del protocollo.

Dopo l'inserimento, gli eventi passano da uno o più **elaboratori di flussi**, che possono instradare i dati (ad esempio all'archiviazione) o eseguire analisi e altri tipi di elaborazione.

Di seguito vengono indicati alcuni tipi comuni di elaborazione. Naturalmente, l'elenco non è esaustivo.

- Scrittura dei dati di evento nell'archiviazione offline sicura per l'analisi batch.

- Analisi Percorso critico, che analizza il flusso di eventi (quasi) in tempo reale, per rilevare le anomalie, riconoscere i modelli in base a intervalli di tempo ricorrenti o attivare avvisi quando si verifica una condizione specifica nel flusso. 

- Gestione di tipi speciali di messaggi non di telemetria dai dispositivi, come le notifiche e gli allarmi. 

- Machine Learning.

Le caselle in grigio mostrano i componenti di un sistema IoT non direttamente correlati al flusso di eventi, ma che sono stati inclusi per completezza.

- Il **registro dei dispositivi** è un database dei dispositivi di cui è stato effettuato il provisioning e include gli ID dispositivo e in genere i metadati dei dispositivi, come la posizione.

- L'**API di provisioning** è un'interfaccia esterna comune per il provisioning e la registrazione di nuovi dispositivi.

- Alcune soluzioni IoT consentono l'invio di **messaggi di comando e controllo** ai dispositivi.

> Questa sezione ha presentato una visualizzazione di livello notevolmente elevato di uno scenario IoT, in cui potrebbe essere necessario tenere presenti alcune sottigliezze e problematiche. Per un'analisi più approfondita dell'architettura di riferimento, con tutte le considerazioni correlate, vedere [Microsoft Azure IoT Reference Architecture][iot-ref-arch] (Architettura di riferimento di Microsoft Azure IoT).

 <!-- links -->

[competing-consumers]: ../../patterns/competing-consumers.md
[iot-ref-arch]: https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/
[minimize-coordination]: ../design-principles/minimize-coordination.md


