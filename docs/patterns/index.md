---
title: Modelli di progettazione cloud
titleSuffix: Azure Architecture Center
description: Schemi progettuali cloud per la creazione di applicazioni affidabili, scalabili e sicure nel cloud
keywords: Azure
author: dragon119
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 003bef866b0cd873122cfb8d4730b95ba49d3d7f
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54011294"
---
# <a name="cloud-design-patterns"></a>Modelli di progettazione cloud

Questi schemi progettuali sono utili per la compilazione di applicazioni affidabili, scalabili e sicure nel cloud.

Ogni schema descrive il problema che risolve, le considerazioni per l'applicazione dello schema e un esempio basato su Microsoft Azure. La maggior parte degli schemi include esempi o frammenti di codice che illustrano come implementare lo schema in Azure. Tuttavia, la maggior parte degli schemi è pertinente a qualsiasi sistema distribuito, sia in hosting su Azure sia su altre piattaforme cloud.

## <a name="challenges-in-cloud-development"></a>Sfide per lo sviluppo nel cloud

<!-- markdownlint-disable MD033 -->
<table>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/availability.md"><img src="_images/category/availability.svg" alt="Availability" /></a></td>
    <td>
        <h3><a href="./category/availability.md">Disponibilità</a></h3>
        <p>La disponibilità è la percentuale di tempo per cui il sistema funziona ed è in esecuzione, in genere misurata come percentuale del tempo di attività. Può essere condizionata da errori di sistema, problemi di infrastruttura, attacchi dannosi, e dal carico di sistema.  Le applicazioni cloud solitamente offrono agli utenti un contratto di servizio e quindi devono essere progettate in modo da ottimizzare la disponibilità.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/data-management.md"><img src="_images/category/data-management.svg" alt="Data Management" /></a></td>
    <td>
        <h3><a href="./category/data-management.md">Gestione dei dati</a></h3>
        <p>La gestione dei dati è l'elemento chiave delle applicazioni cloud e incide sulla maggior parte degli attributi di qualità. In genere i dati risiedono in percorsi diversi e sono dislocati tra più server per motivi di prestazioni, scalabilità o disponibilità e ciò può implicare una serie di sfide. Ad esempio, è necessario mantenere la coerenza dei dati e sincronizzare in genere i dati tra posizioni diverse.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/design-implementation.md"><img src="_images/category/design-implementation.svg" alt="Design and Implementation" /></a></td>
    <td>
        <h3><a href="./category/design-implementation.md">Progettazione e implementazione</a></h3>
        <p>Una progettazione efficace comprende fattori come la coerenza e la logica nella progettazione e nella distribuzione dei componenti, la manutenibilità per semplificare l'amministrazione e lo sviluppo e la riusabilità per consentire l'uso di componenti e sottosistemi in altri scenari e applicazioni. Le decisioni prese in fase di progettazione e implementazione hanno un notevole impatto sulla qualità e sul costo totale di proprietà delle applicazioni e dei servizi ospitati nel cloud.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/messaging.md"><img src="_images/category/messaging.svg" alt="Messaging" /></a></td>
    <td>
        <h3><a href="./category/messaging.md">Messaggistica</a></h3>
        <p>La tipologia distribuita delle applicazioni cloud richiede un'infrastruttura di messaggistica in grado di connettere componenti e servizi, idealmente con un approccio "a regime di controllo libero" per ottimizzare la scalabilità. La messaggistica asincrona è ampiamente usata e, pur offrendo numerosi vantaggi, comporta anche sfide come l'ordinamento dei messaggi, la gestione dei messaggi non elaborabili, l'idempotenza e altro ancora.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/management-monitoring.md"><img src="_images/category/management-monitoring.svg" alt="Management and Monitoring" /></a></td>
    <td>
        <h3><a href="./category/management-monitoring.md">Gestione e monitoraggio</a></h3>
        <p>Le applicazioni cloud vengono eseguite in un data center remoto, in cui non si dispone del controllo completo dell'infrastruttura o, in alcuni casi, del sistema operativo. Ciò può rendere la gestione e il monitoraggio più complessi rispetto a una distribuzione locale. Le applicazioni devono esporre le informazioni sul runtime che gli amministratori e gli operatori possono usare per gestire e monitorare il sistema, nonché supportare i nuovi requisiti aziendali e la personalizzazione, senza che sia necessario arrestare o ridistribuire l'applicazione.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/performance-scalability.md"><img src="_images/category/performance-scalability.svg" alt="Performance and Scalability" /></a></td>
    <td>
        <h3><a href="./category/performance-scalability.md">Prestazioni e scalabilità</a></h3>
        <p>Le prestazioni sono un'indicazione della velocità di risposta con cui un sistema esegue qualsiasi azione all'interno di un determinato intervallo di tempo, mentre la scalabilità è la capacità di un sistema di gestire gli incrementi del carico senza compromettere le prestazioni o di aumentare facilmente le risorse disponibili. In genere le applicazioni cloud hanno carichi di lavoro e picchi di attività variabili. La previsione di questi aspetti, soprattutto in uno scenario multi-tenant, è quasi impossibile. Le applicazioni, invece, devono essere in grado di aumentare le risorse entro i limiti per soddisfare i picchi di domanda e di ridurle quando la domanda diminuisce. La scalabilità non riguarda solo le istanze di calcolo, ma anche altri elementi, come l'archiviazione dei dati, l'infrastruttura di messaggistica e altro ancora.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/resiliency.md"><img src="_images/category/resiliency.svg" alt="Resiliency" /></a></td>
    <td>
        <h3><a href="./category/resiliency.md">Resilienza</a></h3>
        <p>La resilienza è la capacità di un sistema di gestire correttamente gli errori e il ripristino dagli errori. La natura dell'hosting nel cloud, dove le applicazioni sono spesso multi-tenant, usano servizi di piattaforma condivisi, si contendono le risorse e la larghezza di banda, comunicano tramite Internet e vengono eseguite su hardware apposito, implica una maggiore probabilità che si verifichino errori temporanei o permanenti. Per mantenere la resilienza è necessario poter rilevare gli errori e correggerli in modo rapido ed efficiente.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="./category/security.md"><img src="_images/category/security.svg" alt="Security" /></a></td>
    <td>
        <h3><a href="./category/security.md">Sicurezza</a></h3>
        <p>La sicurezza è la capacità di un sistema di impedire azioni dannose o accidentali al di fuori dell'utilizzo designato e di impedire la divulgazione o la perdita di informazioni. Le applicazioni cloud sono esposte in Internet fuori dai limiti locali attendibili, spesso sono aperte al pubblico e possono essere usate da utenti non attendibili. Le applicazioni devono essere progettate e distribuite in modo da proteggerle da attacchi dannosi, limitare l'accesso solo agli utenti approvati e proteggere i dati sensibili.</p>
    </td>
</tr>
</table>
<!-- markdownlint-disable MD033 -->

## <a name="catalog-of-patterns"></a>Catalogo dei modelli

|                                Modello                                |                                                                                                         Summary                                                                                                         |
|-----------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                     [Ambasciata](./ambassador.md)                     |                                                            Creare servizi helper che inviano richieste di rete per conto di un servizio consumer o di un'applicazione.                                                            |
|          [Livello antidanneggiamento](./anti-corruption-layer.md)          |                                                                  Implementare un livello adapter o di interfaccia tra un'applicazione moderna e un sistema legacy.                                                                  |
|         [Back-end per front-end](./backends-for-frontends.md)         |                                                            Creare servizi back-end separati che vengono usati da interfacce o applicazioni front-end specifiche.                                                             |
|                       [A scomparti](./bulkhead.md)                       |                                                        Isolare gli elementi di un'applicazione in pool in modo che un eventuale problema in uno dei componenti non blocchi il funzionamento degli altri componenti.                                                        |
|                    [Cache-aside](./cache-aside.md)                    |                                                                                   Caricare i dati su richiesta in una cache da un archivio dati.                                                                                    |
|                [Interruttore](./circuit-breaker.md)                |                                                     Gestire gli errori la cui correzione potrebbe richiedere una quantità variabile di tempo in fase di connessione a una risorsa o a un servizio remoto.                                                     |
|                           [CQRS](./cqrs.md)                           |                                                           Consente di segregare le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati attraverso l'utilizzo di interfacce separate.                                                            |
|       [Transazione di compensazione](./compensating-transaction.md)       |                                                         Annullare il lavoro eseguito da una serie di passaggi che insieme definiscono un'operazione coerente.                                                         |
|            [Consumer concorrenti](./competing-consumers.md)            |                                                            Consentire a più consumer concorrenti di elaborare i messaggi ricevuti sullo stesso canale di messaggistica.                                                             |
| [Consolidamento delle risorse di calcolo](./compute-resource-consolidation.md) |                                                                        Consolidare più attività o operazioni in un'unica unità di calcolo                                                                        |
|                 [Origine eventi](./event-sourcing.md)                 |                                                      Usare un archivio di solo accodamento per registrare la serie completa di eventi che descrivono le azioni eseguite sui dati di un dominio.                                                      |
|   [Archivio di configurazione esterno](./external-configuration-store.md)   |                                                           È possibile estrarre le informazioni di configurazione dal pacchetto di distribuzione dell'applicazione e spostarle in una posizione centralizzata.                                                           |
|             [Identità federativa](./federated-identity.md)             |                                                                                È possibile delegare l'autenticazione a un provider di identità esterno.                                                                                |
|                     [Gatekeeper](./gatekeeper.md)                     | Proteggere le applicazioni e i servizi usando un'istanza host dedicata che funga da broker tra i client e l'applicazione o il servizio, convalidi e purifichi le richieste e passi le richieste e i dati tra di essi. |
|            [Aggregazione gateway](./gateway-aggregation.md)            |                                                                     Usare un gateway per aggregare più richieste singole in un'unica richiesta.                                                                      |
|             [Offload del gateway](./gateway-offloading.md)             |                                                                         Eseguire l'offload delle funzionalità dei servizi condivise o specializzate in un proxy gateway.                                                                         |
|                [Routing del gateway](./gateway-routing.md)                |                                                                              Eseguire il routing delle richieste a più servizi, usando un singolo endpoint.                                                                               |
|     [Monitoraggio endpoint di integrità](./health-endpoint-monitoring.md)     |                                              Implementare controlli funzionali all'interno di un'applicazione a cui strumenti esterni possono accedere tramite endpoint esposti a intervalli regolari.                                               |
|                    [Tabella degli indici](./index-table.md)                    |                                                                Creare indici sui campi negli archivi dati spesso referenziati dalle query.                                                                 |
|                [Designazione leader](./leader-election.md)                |   Coordinare le azioni eseguite da una raccolta di istanze di attività di collaborazione in un'applicazione distribuita designando un'istanza come leader, con la responsabilità di gestire le altre istanze.    |
|              [Vista materializzata](./materialized-view.md)              |                                        Generare viste prepopolate sui dati in uno o più archivi dati quando i dati non sono formattati in modo ideale per le operazioni di query necessarie.                                        |
|              [Pipe e filtri](./pipes-and-filters.md)              |                                                        Scomporre un'attività che esegue un'elaborazione complessa in una serie di elementi distinti riutilizzabili.                                                        |
|                 [Coda di priorità](./priority-queue.md)                 |                                 Assegnare una priorità alle richieste inviate ai servizi in modo che le richieste con una priorità più alta vengano ricevute ed elaborate più rapidamente rispetto a quelle con priorità più bassa.                                  |
| [Autore/Sottoscrittore](./publisher-subscriber.md) | Abilitare un'applicazione all'annuncio di eventi a più consumer interessati in modalità asincrona, senza accoppiamento di mittenti e destinatari. |
|      [Livellamento del carico basato sulle code](./queue-based-load-leveling.md)      |                                               Usare una coda che funge da buffer tra un'attività e un servizio richiamato per alleggerire i carichi di lavoro elevati intermittenti.                                               |
|                          [Nuovo tentativo](./retry.md)                          |               È possibile abilitare un'applicazione per gestire gli errori temporanei previsti durante il tentativo di connessione a un servizio o a una risorsa di rete ritentando in modo trasparente un'operazione non riuscita in precedenza.                |
|     [Supervisione agente di pianificazione](./scheduler-agent-supervisor.md)     |                                                              Coordinare un set di azioni in un set distribuito di servizi e di altre risorse remote.                                                               |
|                       [Partizionamento orizzontale](./sharding.md)                       |                                                                           Dividere un archivio dati in un set di partizioni orizzontali o partizioni.                                                                            |
|                        [Collaterale](./sidecar.md)                        |                                                    Distribuire i componenti di un'applicazione in un processo o in un contenitore separato per fornire isolamento e incapsulamento.                                                     |
|         [Hosting di contenuto statico](./static-content-hosting.md)         |                                                          Distribuire contenuto statico in un servizio di archiviazione basato sul cloud in grado di inviarlo direttamente al client.                                                           |
|                      [Sostituzione](./strangler.md)                      |                                            Migrare in maniera incrementale un sistema legacy, sostituendo gradualmente parti specifiche di funzionalità con nuove applicazioni e servizi.                                            |
|                     [Limitazione](./throttling.md)                     |                                                 Controllare il consumo delle risorse usate da un'istanza di un'applicazione, un singolo tenant o un intero servizio.                                                 |
|                      [Passepartout](./valet-key.md)                      |                                                        Usare un token o una chiave che fornisca ai client l'accesso diretto limitato a una specifica risorsa o a un servizio.                                                        |
