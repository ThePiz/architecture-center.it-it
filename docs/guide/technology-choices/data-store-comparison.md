---
title: Criteri per la scelta di un archivio dati
titleSuffix: Azure Application Architecture Guide
description: Panoramica delle opzioni di calcolo di Azure.
author: MikeWasson
ms.date: 06/01/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 3fd3badd66edbe561bea88576bb80d9fc3e0bb79
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068923"
---
# <a name="criteria-for-choosing-a-data-store"></a>Criteri per la scelta di un archivio dati

Azure supporta molti tipi di soluzioni di archiviazione dei dati, ognuna delle quali offre funzionalità e capacità diverse. Questo articolo descrive i criteri di confronto che è consigliabile usare per la valutazione di un archivio dati. L'obiettivo è facilitare l'individuazione dei tipi di archiviazione dati in grado di soddisfare i requisiti della soluzione.

## <a name="general-considerations"></a>Considerazioni generali

Per avviare il confronto, raccogliere il maggior numero possibile delle informazioni seguenti rispetto alle proprie esigenze in termini di dati. Queste informazioni consentiranno di determinare quali tipi di archivi dati possono soddisfare le proprie esigenze.

### <a name="functional-requirements"></a>Requisiti funzionali

- **Formato dati**. Che tipo di dati si intende archiviare? I tipi comuni includono dati transazionali, oggetti JSON, dati di telemetria, indici di ricerca o file flat.

- **Dimensioni dei dati**. Quali sono le dimensioni delle entità che occorre archiviare? Queste entità dovranno essere gestite come un singolo documento o possono essere suddivise tra più documenti, tabelle, raccolte e così via?

- **Scala e struttura**. Qual è la quantità totale di capacità di archiviazione necessaria? Si prevede il partizionamento dei dati?

- **Relazioni tra i dati**. I dati dovranno supportare relazioni uno-a-molti o molti-a-molti? Le relazioni stesse rappresentano una parte importante dei dati? Sarà necessario creare join o combinare in altri modi i dati all'interno dello stesso set o provenienti da set di dati esterni?

- **Modello di coerenza**. Quanto è importante che gli aggiornamenti eseguiti in un nodo compaiano negli altri nodi prima che sia possibile apportare altre modifiche? È accettabile la coerenza finale? Sono necessarie garanzie ACID per le transazioni?

- **Flessibilità dello schema**. Che tipo di schemi verranno applicati ai dati? Si userà uno schema fisso, un approccio di schema in scrittura o un approccio di schema in lettura?

- **Concorrenza**. Che tipo di meccanismo di concorrenza si vuole usare per l'aggiornamento e la sincronizzazione dei dati? L'applicazione eseguirà numerosi aggiornamenti che potrebbero essere potenzialmente in conflitto? In questo caso, potrebbero essere necessari il blocco dei record e il controllo della concorrenza pessimistica. In alternativa, si possono supportare controlli di concorrenza ottimistica? In questo caso, è sufficiente il semplice controllo della concorrenza basato su timestamp o è necessaria la funzionalità aggiuntiva di controllo della concorrenza per più versioni?

- **Spostamento dei dati**. La soluzione dovrà eseguire operazioni di estrazione, trasformazione e caricamento per spostare i dati in altri archivi o data warehouse?

- **Ciclo di vita dei dati**. I dati sono di tipo WORM? Possono essere spostati in un'archiviazione offline sicura o ad accesso sporadico?

- **Altre funzionalità supportate**. Sono necessarie altre funzionalità specifiche, ad esempio convalida dello schema, aggregazione, indicizzazione, ricerca full-text, MapReduce o altre funzionalità di query?

### <a name="non-functional-requirements"></a>Requisiti non funzionali

- **Prestazioni e scalabilità**. Quali sono i requisiti di prestazioni dei dati? Si hanno requisiti specifici relativi alla frequenza di inserimento e alla velocità di elaborazione dei dati? Quali sono i tempi di risposta accettabili per l'esecuzione di query e l'aggregazione dei dati dopo l'inserimento? Di quanto dovranno aumentare le prestazioni dell'archivio dati? Il carico di lavoro è a elevato utilizzo delle risorse di scrittura o a elevato utilizzo delle risorse di lettura?

- **Affidabilità**. Quale contratto di servizio complessivo è necessario supportare? Che livello di tolleranza di errore è necessario fornire per i consumer di dati? Quali tipi di funzionalità di backup e ripristino sono necessari?

- **Replica**. I dati dovranno essere distribuiti tra più repliche o aree? Quali tipi di funzionalità di replica dei dati sono necessari?

- **Limiti**. I limiti di un particolare archivio dati supporteranno i requisiti di scalabilità, numero di connessioni e velocità effettiva?

### <a name="management-and-cost"></a>Gestione e costi

- **Servizio gestito**. Quando possibile, usare un servizio dati gestito, a meno che non siano necessarie funzionalità specifiche disponibili solo in un archivio dati basato su IaaS.

- **Disponibilità in base all'area geografica**. Per i servizi gestiti, il servizio è disponibile in tutte le aree di Azure? La soluzione deve essere ospitata in specifiche aree di Azure?

- **Portabilità**. I dati dovranno essere migrati a un'istanza locale, Data Center esterni o altri ambienti di hosting cloud?

- **Licenze**. Si hanno preferenze tra tipo di licenza OSS o proprietaria? Esistono altre restrizioni esterne rispetto al tipo di licenza che è possibile usare?

- **Costo complessivo**. Qual è il costo complessivo dell'uso del servizio all'interno della soluzione? Quante istanze sarà necessario eseguire per supportare i requisiti di velocità effettiva e tempo di attività? Considerare in questo calcolo i costi delle operazioni. Uno dei motivi per preferire i servizi gestiti è la riduzione dei costi operativi.

- **Convenienza**. È possibile partizionare i dati per archiviarli in modo più efficiente sotto il profilo dei costi? Ad esempio, è possibile spostare gli oggetti di grandi dimensioni da un costoso database relazionale a un archivio di oggetti?

### <a name="security"></a>Security

- **Sicurezza**. Che tipo di crittografia occorre implementare? È necessaria la crittografia dei dati inattivi? Che tipo di meccanismo di autenticazione si vuole usare per la connessione ai dati?

- **Controllo**. Che tipo di log di controllo è necessario generare?

- **Requisiti di rete**. È necessario limitare o gestire in altro modo l'accesso ai dati da altre risorse di rete? I dati devono essere accessibili solo dall'interno dell'ambiente Azure? I dati devono essere accessibili da specifici indirizzi IP o subnet? Devono essere accessibili da applicazioni o servizi ospitati in locale o in altri data center esterni?

### <a name="devops"></a>DevOps

- **Competenze**. Esistono specifici linguaggi di programmazione, sistemi operativi o altre tecnologie con cui il team ha particolare familiarità? Ne esistono altri che sarebbe difficile utilizzare per il team?

- **Client**. È disponibile un supporto client ottimale per i linguaggi di sviluppo usati?

Le sezioni seguenti presentano un confronto dei vari modelli di archivio dati in termini di profilo del carico di lavoro, tipi di dati e casi d'uso di esempio.

## <a name="relational-database-management-systems-rdbms"></a>Sistemi di gestione di database relazionali (RDBMS)

<!-- markdownlint-disable MD033 -->

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>La creazione di nuovi record e l'aggiornamento dei dati esistenti avvengono regolarmente.</li>
            <li>Occorre completare più operazioni in una singola transazione.</li>
            <li>Richiede funzioni di aggregazione per eseguire la tabulazione incrociata.</li>
            <li>È richiesta la stretta integrazione con gli strumenti di reporting.</li>
            <li>Le relazioni vengono applicate usando i vincoli del database.</li>
            <li>Per ottimizzare le prestazioni delle query vengono usati gli indici.</li>
            <li>Consente di accedere a subset di dati specifici.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>I dati sono altamente normalizzati.</li>
            <li>Sono necessari e vengono applicati schemi del database.</li>
            <li>Relazioni molti-a-molti tra le entità di dati nel database.</li>
            <li>Nello schema sono definiti vincoli imposti a tutti i dati nel database.</li>
            <li>I dati richiedono integrità elevata. Gli indici e le relazioni devono essere gestiti in modo accurato.</li>
            <li>I dati richiedono la coerenza assoluta. Le transazioni funzionano in modo da garantire che tutti i dati siano coerenti al 100% per tutti gli utenti e i processi.</li>
            <li>È previsto che le singole immissioni di dati siano di piccole o medie dimensioni.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>
        <ul>
            <li>Line-of-business (gestione risorse umane, CRM, ERP)</li>
            <li>Gestione dell'inventario</li>
            <li>Database di report</li>
            <li>Contabilità</li>
            <li>Gestione degli asset</li>
            <li>Gestione dei fondi</li>
            <li>Gestione degli ordini</li>
        </ul>
    </td>
</tr>
</table>

## <a name="document-databases"></a>Database di documenti

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>Utilizzo generico.</li>
            <li>Le operazioni di inserimento e aggiornamento sono comuni. La creazione di nuovi record e l'aggiornamento dei dati esistenti avvengono regolarmente.</li>
            <li>Nessun mancata corrispondenza dell'impedenza tra modello a oggetti e relazionale. I documenti possono offrire una corrispondenza migliore con le strutture di oggetti usate nel codice dell'applicazione.</li>
            <li>Viene usata comunemente la concorrenza ottimistica.</li>
            <li>I dati devono essere modificati ed elaborati dall'applicazione che li utilizza.</li>
            <li>I dati richiedono un indice in più campi.</li>
            <li>I singoli documenti vengono recuperati e scritti come un unico blocco.</li>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>I dati possono essere gestiti in modo denormalizzato.</li>
            <li>Le dimensioni dei dati dei singoli documenti sono relativamente ridotte.</li>
            <li>Ogni tipo di documento può usare uno schema proprio.</li>
            <li>I documenti possono contenere campi facoltativi.</li>
            <li>I dati dei documenti sono semistrutturati, vale a dire che i tipi di dati di ogni campo non sono definiti in modo rigido.</li>
            <li>L'aggregazione dei dati è supportata.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>
        <ul>
            <li>Catalogo prodotti</li>
            <li>Account utente</li>
            <li>Distinta base</li>
            <li>Personalizzazione</li>
            <li>Gestione del contenuto</li>
            <li>Dati delle operazioni</li>
            <li>Gestione dell'inventario</li>
            <li>Dati di cronologia delle transazioni</li>
            <li>Vista materializzata di altri archivi NoSQL. Sostituisce l'indicizzazione file/BLOB.</li>
        </ul>
    </td>
</tr>
</table>

## <a name="keyvalue-stores"></a>Archivi chiave/valore

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>I dati sono identificabili e accessibili tramite un'unica chiave ID, come un dizionario.</li>
            <li>Scalabilità elevata.</li>
            <li>Non sono necessari join, blocchi o unioni.</li>
            <li>Non vengono usati meccanismi di aggregazione.</li>
            <li>In genere non vengono usati indici secondari.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>I dati tendono a essere di grandi dimensioni.</li>
            <li>Ogni chiave è associata a un singolo valore, ovvero un BLOB di dati non gestiti.</li>
            <li>Non viene applicata alcuna imposizione dello schema.</li>
            <li>Non esistono relazioni tra le entità.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>
        <ul>
            <li>Memorizzazione dei dati nella cache</li>
            <li>Gestione delle sessioni</li>
            <li>Gestione profilo e preferenze utente</li>
            <li>Raccomandazione prodotti e visualizzazione annunci</li>
            <li>Dizionari</li>
        </ul>
    </td>
</tr>
</table>

## <a name="graph-databases"></a>Database di grafi

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>Le relazioni tra gli elementi di dati sono molto complesse e prevedono molti hop tra gli elementi di dati correlati.</li>
            <li>Le relazioni tra gli elementi di dati sono dinamiche e cambiano nel tempo.</li>
            <li>Le relazioni tra oggetti sono entità di prima classe e non richiedono chiavi esterne e join da attraversare.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>I dati sono costituiti da nodi e relazioni.</li>
            <li>I nodi sono simili a righe di tabella o documenti JSON.</li>
            <li>Le relazioni sono importanti quanto i nodi e sono esposte direttamente nel linguaggio di query.</li>
            <li>Gli oggetti compositi, ad esempio una persona con più numeri di telefono, tendono a essere suddivisi in nodi separati di dimensioni inferiori, combinati con relazioni attraversabili </li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>
        <ul>
            <li>Organigrammi</li>
            <li>Grafi sociali</li>
            <li>Rilevamento delle frodi</li>
            <li>Analytics</li>
            <li>Motore di raccomandazione</li>
        </ul>
    </td>
</tr>
</table>

## <a name="column-family-databases"></a>Database a colonne

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>La maggior parte dei database a colonne esegue le operazioni di scrittura molto rapidamente.</li>
            <li>Le operazioni di aggiornamento ed eliminazione sono rare.</li>
            <li>Progettato per offrire accesso a bassa latenza e velocità effettiva elevata.</li>
            <li>Supporta l'accesso query semplice a un particolare set di campi all'interno di un record di dimensioni molto superiori.</li>
            <li>Scalabilità elevata.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>I dati vengono archiviati in tabelle composte da una colonna chiave e una o più famiglie di colonne.</li>
            <li>Le specifiche colonne possono variare per singole righe.</li>
            <li>Le singole celle sono accessibili tramite comandi Get e Put</li>
            <li>Usando un comando di analisi vengono restituite più righe.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>
        <ul>
            <li>Consigli</li>
            <li>Personalizzazione</li>
            <li>Dati di sensori</li>
            <li>Telemetria</li>
            <li>Messaggistica</li>
            <li>Analisi dei social media</li>
            <li>Web analytics</li>
            <li>Monitoraggio attività</li>
            <li>Meteo e altri dati di serie temporali</li>
        </ul>
    </td>
</tr>
</table>

## <a name="search-engine-databases"></a>Database di motori di ricerca

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>Indicizzazione dei dati da più origini e servizi.</li>
            <li>Le query sono ad hoc e possono essere complesse.</li>
            <li>Richiede l'aggregazione.</li>
            <li>È necessaria la ricerca full-text.</li>
            <li>Sono necessarie query self-service ad hoc.</li>
            <li>È richiesta l'analisi dei dati con indici in tutti i campi.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>Semistrutturati o non strutturati</li>
            <li>Text</li>
            <li>Testo con riferimento a dati strutturati</li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>
        <ul>
            <li>Cataloghi prodotti</li>
            <li>Ricerca sito</li>
            <li>Registrazione</li>
            <li>Analytics</li>
            <li>Siti di acquisti</li>
        </ul>
    </td>
</tr>
</table>

## <a name="data-warehouse"></a>Data warehouse

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>Analisi dei dati</li>
            <li>Business intelligence aziendale</li>
        </ul>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>Dati cronologici da più origini.</li>
            <li>In genere denormalizzato in uno schema &quot;star&quot; o &quot;snowflake&quot;, costituito da tabelle dei fatti e delle dimensioni.</li>
            <li>In genere caricato con nuovi dati in base a una pianificazione.</li>
            <li>Le tabelle delle dimensioni includono spesso più versioni cronologiche di un'entità, detta <em>dimensione a modifica lenta</em>.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>Un data warehouse aziendale che fornisce dati per modelli di analisi, report e dashboard.
    </td>
</tr>
</table>

## <a name="time-series-databases"></a>Database di serie temporali

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>Le stragrande maggioranza delle operazioni (95-99%) è di scrittura.</li>
            <li>I record vengono in genere aggiunti in sequenza in ordine temporale.</li>
            <li>Gli aggiornamenti sono rari.</li>
            <li>Le eliminazioni vengono eseguite in blocco e in blocchi o record contigui.</li>
            <li>Le richieste di lettura possono essere superiori rispetto alla memoria disponibile.</li>
            <li>È comune che avvengano più letture simultanee.</li>
            <li>I dati vengono letti in sequenza in ordine temporale crescente o decrescente.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>Come chiave primaria e meccanismo di ordinamento viene usato un timestamp.</li>
            <li>Misurazioni dalla voce o descrizioni di ciò che rappresenta la voce.</li>
            <li>Tag che definiscono informazioni aggiuntive sul tipo, l'origine e altre informazioni sulla voce.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>
        <ul>
            <li>Monitoraggio e telemetria eventi.</li>
            <li>Dati di sensori o altri dati IoT.</li>
        </ul>
    </td>
</tr>
</table>

## <a name="object-storage"></a>Archiviazione di oggetti

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>Identificato dalla chiave.</li>
            <li>Gli oggetti possono essere accessibili pubblicamente o privatamente.</li>
            <li>Il contenuto in genere è una risorsa, ad esempio un foglio di calcolo, un'immagine o un file video.</li>
            <li>Il contenuto deve essere durevole (persistente) ed esterno a qualsiasi livello di applicazione o macchina virtuale.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>I dati sono di grandi dimensioni.</li>
            <li>Dati BLOB.</li>
            <li>Il valore è opaco.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>
        <ul>
            <li>Immagini, video, documenti di Office, file PDF</li>
            <li>CSS, script, CSV</li>
            <li>HTML statico, JSON</li>
            <li>File di log e di controllo</li>
            <li>Backup del database</li>
        </ul>
    </td>
</tr>
</table>

## <a name="shared-files"></a>File condivisi

<table>
<tr><td><strong>Carico di lavoro</strong></td>
    <td>
        <ul>
            <li>Migrazione da app esistenti che interagiscono con il file system.</li>
            <li>Richiede l'interfaccia SMB.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>Tipo di dati</strong></td>
    <td>
        <ul>
            <li>File in un set gerarchico di cartelle.</li>
            <li>Accessibile con librerie di I/O standard.</li>
        </ul>
    </td>
</tr>
<tr><td><strong>esempi</strong></td>
    <td>
        <ul>
            <li>File legacy</li>
            <li>Contenuto condiviso accessibile tra più macchine virtuali o istanze di app</li>
        </ul>
    </td>
</tr>
</table>

<!-- markdownlint-enable MD033 -->
