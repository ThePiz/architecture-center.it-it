---
title: Motore di ricerca di prodotti intelligente per l'e-commerce
titleSuffix: Azure Example Scenarios
description: Fornire un'esperienza di ricerca di qualità elevata in un'applicazione di e-commerce.
author: jelledruyts
ms.date: 09/14/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-ecommerce-search.png
ms.openlocfilehash: 12829c0b765831efdd08c8116a8fd847bd4e6abd
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908177"
---
# <a name="intelligent-product-search-engine-for-e-commerce"></a>Motore di ricerca di prodotti intelligente per l'e-commerce

Questo scenario di esempio illustra in che modo l'uso di un servizio di ricerca dedicato può aumentare drasticamente la pertinenza dei risultati della ricerca per i clienti dei siti di e-commerce.

La ricerca è il meccanismo principale attraverso cui i clienti trovano e infine acquistano i prodotti. È quindi essenziale che i risultati della ricerca siano pertinenti alla _finalità_ della query di ricerca e che l'esperienza di ricerca end-to-end corrisponda a quella offerta dai giganti della ricerca, fornendo risultati quasi immediati, analisi linguistica, corrispondenza in base alla posizione geografica, filtri, facet, completamento automatico, evidenziazione dei risultati e così via.

Si immagini un'applicazione Web di e-commerce tipica con i dati dei prodotti archiviati in un database relazionale, ad esempio SQL Server o il database SQL di Azure. Le query di ricerca vengono spesso gestite all'interno del database usando query `LIKE` oppure funzionalità di [ricerca full-text][docs-sql-fts]. Usando [Ricerca di Azure][docs-search] è possibile liberare il database operativo dall'elaborazione delle query, iniziando facilmente a sfruttare i vantaggi di quelle funzionalità difficili da implementare che però forniscono ai clienti l'esperienza di ricerca migliore possibile. Inoltre, poiché Ricerca di Azure è una piattaforma distribuita come servizio (PaaS), non è necessario preoccuparsi della gestione dell'infrastruttura né diventare esperti nella ricerca.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

- Trovare annunci immobiliari o negozi vicino alla posizione fisica dell'utente.
- Cercare articoli in un sito di notizie o cercare risultati sportivi, con preferenza per le informazioni più _recenti_.
- Eseguire ricerche in archivi di grandi dimensioni di organizzazioni _incentrate sui documenti_, ad esempio studi notarili e responsabili delle politiche.

In ultima analisi, _qualsiasi_ applicazione con funzionalità di ricerca può trarre vantaggio da un servizio di ricerca dedicato.

## <a name="architecture"></a>Architettura

![Panoramica dell'architettura dei componenti di Azure coinvolti in un motore di ricerca prodotti intelligente per l'e-commerce][architecture]

Questo scenario illustra una soluzione di e-commerce in cui i clienti possono eseguire ricerche all'interno di un catalogo prodotti.

1. I clienti accedono all'**applicazione Web di e-commerce** da qualsiasi dispositivo.
2. Il catalogo dei prodotti viene mantenuto in un **database SQL di Azure** per l'elaborazione transazionale.
3. Ricerca di Azure usa un **indicizzatore di ricerca** per mantenere automaticamente aggiornato l'indice tramite il rilevamento delle modifiche integrato.
4. Le query di ricerca dei clienti vengono inviate al servizio **Ricerca di Azure**, che elabora la query e restituisce i risultati più pertinenti.
5. Come alternativa all'esperienza di ricerca basata sul Web, i clienti possono anche usare un **bot di conversazione** nei social media o direttamente un assistente digitale per cercare prodotti e perfezionare le query di ricerca e i risultati in modo incrementale.
6. Facoltativamente, si può usare la funzionalità di **ricerca cognitiva** per applicare l'intelligenza artificiale e ottenere un'elaborazione ancora più intelligente.

### <a name="components"></a>Componenti

- Le [app Web del servizio app][docs-webapps] ospitano applicazioni Web con scalabilità automatica e disponibilità elevata senza che sia necessario gestire l'infrastruttura.
- Il [database SQL][docs-sql-database] è un servizio gestito di database relazionale per uso generico in Microsoft Azure che supporta strutture come dati relazionali, JSON, dati spaziali e XML.
- [Ricerca di Azure][docs-search] è una soluzione cloud di ricerca distribuita come servizio che offre un'esperienza di ricerca avanzata su contenuti eterogenei e privati nelle applicazioni Web, per dispositivi mobili e aziendali.
- Il [servizio Bot][docs-botservice] offre gli strumenti per creare, testare, distribuire e gestire bot intelligenti.
- [Servizi cognitivi][docs-cognitive] consente di usare algoritmi intelligenti per vedere, ascoltare, parlare, comprendere e interpretare le esigenze degli utenti tramite metodi di comunicazione naturali.

### <a name="alternatives"></a>Alternative

- È possibile usare funzionalità di **ricerca nel database**, ad esempio tramite la ricerca full-text di SQL Server, ma in questo caso l'archivio transazionale elabora anche le query (aumentando la necessità di potenza di elaborazione) e le funzionalità di ricerca all'interno del database sono più limitate.
- È possibile ospitare il software open source [Apache Lucene][apache-lucene] (su cui è basato Ricerca di Azure) in Macchine virtuali di Azure, ma in questo caso si torna a un'infrastruttura distribuita come servizio (IaaS) e non si usufruisce delle numerose funzionalità aggiuntive fornite da Ricerca di Azure rispetto a Lucene.
- Si può anche considerare la distribuzione di [Elasticsearch][elastic-marketplace] da Microsoft Azure Marketplace, che è un prodotto di ricerca alternativo e idoneo di un fornitore di terze parti, ma anche in questo caso si esegue un carico di lavoro IaaS.

Altre opzioni per il livello dati includono:

- [Cosmos DB](/azure/cosmos-db/introduction): database multimodello di Microsoft distribuito a livello globale. Cosmos DB offre una piattaforma per eseguire altri modelli di dati come Mongo DB, Cassandra, dati Graph o un semplice archivio tabelle. Ricerca di Azure supporta anche l'indicizzazione dei dati direttamente da Cosmos DB.

## <a name="considerations"></a>Considerazioni

### <a name="scalability"></a>Scalabilità

Il [piano tariffario][search-tier] del servizio Ricerca di Azure non determina le funzionalità disponibili, ma viene usato principalmente per la [pianificazione della capacità][search-capacity], perché definisce l'archiviazione massima disponibile e il numero di partizioni e repliche di cui è possibile eseguire il provisioning. Le **partizioni** consentono di indicizzare più documenti e ottenere una velocità effettiva di scrittura superiore, mentre le **repliche** forniscono più query al secondo (QPS) e disponibilità elevata.

È possibile modificare in modo dinamico il numero di partizioni e repliche, ma non il piano tariffario, quindi è necessario valutare attentamente il livello più adatto per il carico di lavoro di destinazione. Se è comunque necessario modificare il livello, occorrerà effettuare il provisioning di un nuovo servizio affiancato e ricaricarvi gli indici. Quindi, si potranno indirizzare le applicazioni al nuovo servizio.

### <a name="availability"></a>Disponibilità

Ricerca di Azure offre un [contratto di servizio con disponibilità garantita al 99,9%][search-sla] per le _letture_ (ossia l'esecuzione di query) se si hanno almeno due repliche e per gli _aggiornamenti_ (ossia l'aggiornamento degli indici di ricerca) se si hanno almeno tre repliche. È quindi opportuno effettuare il provisioning di almeno due repliche se si vuole che i clienti possano _cercare_ in modo affidabile e 3 se anche le _modifiche all'indice_ devono essere considerate operazioni a disponibilità elevata.

Se è necessario apportare all'indice modifiche che causano un'interruzione senza tempi di inattività, ad esempio modifica dei tipi di dati, eliminazione o ridenominazione di campi, sarà necessario ricompilare l'indice. Come per la modifica del livello di servizio, questo significa creare un nuovo indice, ripopolarlo con i dati e quindi aggiornare le applicazioni esistenti in modo che puntino al nuovo indice.

### <a name="security"></a>Security

Ricerca di Azure è conforme con molti [standard di sicurezza e privacy dei dati][search-security] e per questo può essere usato nella maggior parte dei settori.

Per proteggere l'accesso al servizio, Ricerca di Azure usa due tipi di chiavi: **chiavi amministratore**, che consentono di eseguire _qualsiasi_ attività sul servizio, e **chiavi di query**, che possono essere usate esclusivamente per le operazioni di sola lettura, ad esempio l'esecuzione di query. In genere l'applicazione che esegue la ricerca non aggiorna l'indice, quindi va configurata solo con una chiave di query e non con una chiave amministratore, in particolare se la ricerca viene eseguita da un dispositivo dell'utente finale, come ad esempio nel caso dell'esecuzione di uno script in un Web browser.

### <a name="search-relevance"></a>Pertinenza della ricerca

L'efficacia dell'applicazione di e-commerce dipende in larga misura dalla pertinenza dei risultati della ricerca forniti ai clienti. Ottimizzare con cura il servizio di ricerca per ottenere risultati ottimali in base alle ricerche degli utenti o usare funzionalità incorporate, ad esempio l'[analisi del traffico di ricerca][search-analysis], per comprendere i criteri di ricerca dei clienti, consente di prendere decisioni basate sui dati.

Ecco alcuni modi tipici per ottimizzare il servizio di ricerca:

- Uso di [profili di punteggio][search-scoring] per influenzare la pertinenza dei risultati della ricerca, ad esempio in base al campo che corrisponde alla query, a quanto sono recenti i dati, alla distanza geografica dall'utente e così via.
- Uso di [analizzatori del linguaggio forniti da Microsoft][search-languages], che si servono di uno stack di elaborazione del linguaggio naturale per interpretare meglio le query
- Uso di [analizzatori personalizzati][search-analyzers] per assicurarsi che i prodotti vengano trovati correttamente, soprattutto se si vuole eseguire la ricerca su informazioni non basate sul linguaggio, come marca e modello di un prodotto.

## <a name="deploy-the-scenario"></a>Distribuire lo scenario

Per distribuire una versione di e-commerce più completa di questo scenario, è possibile seguire questa [esercitazione dettagliata][end-to-end-walkthrough], che fornisce un'applicazione di esempio .NET che esegue una semplice applicazione per l'acquisto di biglietti. Include inoltre Ricerca di Azure e usa molte delle funzionalità illustrate. È anche disponibile un modello di Resource Manager per automatizzare la distribuzione della maggior parte delle risorse di Azure.

## <a name="pricing"></a>Prezzi

Per esaminare il costo di esecuzione dello scenario, nel calcolatore dei costi sono preconfigurati tutti i servizi menzionati in precedenza. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base all'utilizzo previsto.

Sono stati definiti tre profili di costo di esempio in base alla quantità di traffico prevista.

- [Small][small-pricing]: in questo profilo vengono usati un'unica app Web `Standard S1` per ospitare il sito Web, il livello gratuito del servizio Azure Bot, un singolo servizio Ricerca di Azure `Basic` e un database SQL `Standard S2`.
- [Medium][medium-pricing]: in questo caso le prestazioni dell'app Web sono aumentate a due istanze del livello `Standard S3`, il servizio di ricerca è aggiornato al livello `Standard S1` e viene usato un database SQL `Standard S6`.
- [Large][large-pricing]: nel profilo più grande vengono usate quattro istanze di un'app Web`Premium P2V2`, il servizio Azure Bot è aggiornato al livello `Standard S1` (con 1.000.000 di messaggi nei canali Premium) e vengono usati 2 unità del servizio Ricerca di Azure `Standard S3` e un database SQL `Premium P6`.

## <a name="related-resources"></a>Risorse correlate

Per altre informazioni su Ricerca di Azure, visitare il [centro documentazione][docs-search], esaminare gli [esempi][search-samples] o visualizzare un [sito demo][search-demo] completo.

<!-- links -->
[architecture]: ./media/architecture-ecommerce-search.png
[docs-sql-fts]: /sql/relational-databases/search/query-with-full-text-search
[docs-search]: /azure/search/search-what-is-azure-search
[docs-sql-database]: /azure/sql-database/sql-database-technical-overview
[docs-webapps]: /azure/app-service/app-service-web-overview
[docs-botservice]: /azure/bot-service/
[docs-cognitive]: /azure/cognitive-services/
[apache-lucene]: https://lucene.apache.org/
[elastic-marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/elastic.elasticsearch
[end-to-end-walkthrough]: https://github.com/Azure/fta-customerfacingapps/tree/master/ecommerce/articles
[search-sla]: https://go.microsoft.com/fwlink/?LinkId=716855
[search-tier]: /azure/search/search-sku-tier
[search-capacity]: /azure/search/search-capacity-planning
[search-security]: /azure/search/search-security-overview
[search-analysis]: /azure/search/search-traffic-analytics
[search-languages]: /rest/api/searchservice/language-support
[search-analyzers]: /rest/api/searchservice/custom-analyzers-in-azure-search
[search-scoring]: /rest/api/searchservice/add-scoring-profiles-to-a-search-index
[search-samples]: https://azure.microsoft.com/resources/samples/?service=search&sort=0
[search-demo]: https://azjobsdemo.azurewebsites.net/
[small-pricing]: https://azure.com/e/db2672a55b6b4d768ef0060a8d9759bd
[medium-pricing]: https://azure.com/e/a5ad0706c9e74add811e83ef83766a1c
[large-pricing]: https://azure.com/e/57f95a898daa487795bd305599973ee6
