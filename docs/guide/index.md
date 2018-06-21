---
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: 80cb7fde0694257a5c413b702505e27f18aed8d3
ms.sourcegitcommit: d702b4d27e96e7a5a248dc4f2f0e25cf6e82c134
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
ms.locfileid: "31771687"
---
# <a name="azure-application-architecture-guide"></a>Guida all'architettura delle applicazioni in Azure

Questa guida presenta un approccio strutturato alla progettazione di applicazioni in Azure che assicurano disponibilità elevata, resilienza e scalabilità. È basata su procedure comprovate apprese nel corso delle interazioni con i clienti.

<br/>

<img src="./images/guide-steps.svg" style="max-width:800px;"/>

## <a name="introduction"></a>Introduzione

Il cloud cambia la modalità di progettazione delle applicazioni. Anziché essere concepite come monoliti, le applicazioni vengono scomposte in servizi decentralizzati e di dimensioni inferiori. Questi servizi comunicano tramite API o usando la gestione eventi o la messaggistica asincrona. Le applicazioni scalano orizzontalmente aggiungendo nuove istanza in base alle esigenze. 

Queste tendenze pongono nuove sfide. Lo stato dell'applicazione viene distribuito. Le operazioni vengono eseguite in parallelo e in modo asincrono. In caso di errori il sistema nel suo complesso deve essere resiliente. Le distribuzioni devono essere automatizzate e prevedibili. Il monitoraggio e la telemetria sono fondamentali per ottenere informazioni dettagliate sul sistema. La Guida all'architettura delle applicazioni in Azure è stata ideata per individuare queste modifiche con facilità. 

<table>
<thead>
    <tr><th>Locale tradizionale</th><th>Cloud moderno</th></tr>
</thead>
<tbody>
<tr><td>Monolitico, centralizzato<br/>
Progettazione per una scalabilità prevedibile<br/>
Database relazionale<br/>
Coerenza di alto livello<br/>
Elaborazione seriale e sincronizzata<br/>
Progettazione per evitare errori (MTBF)<br/>
Aggiornamenti occasionali di grandi dimensioni<br/>
Gestione manuale<br/>
Server con schema snowflake</td>
<td>
Scomposto e decentralizzato<br/>
Progettazione per la scalabilità elastica<br/>
Persistenza Polyglot (combinazione di tecnologie di archiviazione)<br/>
Coerenza finale<br/>
Elaborazione asincrona e parallela<br/>
Progettazione in funzione del rischio di errori (MTTR)<br/>
Aggiornamenti frequenti di piccole dimensioni<br/>
Autogestione automatizzata<br/>
Infrastruttura non modificabile<br/>
</td>
</tbody>
</table>

Questa guida è rivolta ai progettisti, agli sviluppatori e ai team operativi. Non è concepita come guida dettagliata all'utilizzo dei singoli servizi Azure. Illustra gli schemi architetturali e le procedure consigliate da applicare in fase di compilazione sulla piattaforma cloud di Azure. È anche possibile scaricare una [versione e-book della guida][ebook].

## <a name="how-this-guide-is-structured"></a>Struttura della guida

La Guida all'architettura delle applicazione in Azure è concepita come una serie di passaggi, dall'architettura e la progettazione fino all'implementazione. Per ogni passaggio è disponibile materiale sussidiario utile per la progettazione dell'architettura dell'applicazione.

### <a name="architecture-styles"></a>Stili di architettura

Il primo punto di decisione è il più importante. Che stile di architettura si intende costruire? Sarà un'architettura di microservizi, un'applicazione a più livelli maggiormente tradizionale o una soluzione per Big Data? Sono stati identificati alcuni stili distinti. Ogni stile offre vantaggi e pone sfide.

Altre informazioni:

- [Stili di architettura][arch-styles]
- [Architetture di riferimento di Azure][ref-archs]

### <a name="technology-choices"></a>Scelte di tecnologia

Nella fase iniziale è necessario adottare due scelte tecnologiche in quanto incidono sull'intera architettura. Si tratta della scelta del servizio di calcolo e degli archivi dati. *Calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui vengono eseguite le applicazioni. Gli *archivi dati* includono i database ma anche l'archiviazione per le code di messaggi, le cache, i log e qualsiasi altro elemento in cui è possibile salvare in modo permanente un'applicazione. 

Altre informazioni:

- [Scelta di un servizio di calcolo](./technology-choices/compute-overview.md)
- [Scelta di un archivio dati](./technology-choices/data-store-overview.md)

### <a name="design-principles"></a>Principi di progettazione

Sono stati identificati i principi di progettazione generali che consentiranno di rendere l'applicazione più scalabile, resiliente e gestibile. Questi principi di progettazione sono applicabili a qualunque stile di architettura. Durante il processo di progettazione tenere presente questi dieci principi di alto livello. È quindi possibile prendere in considerazione il set di procedure consigliate per aspetti specifici dell'architettura, ad esempio il ridimensionamento automatico, la memorizzazione nella cache, il partizionamento dei dati, la progettazione delle API e altro ancora.

Altre informazioni:

- [Principi di progettazione per le applicazioni Azure][design-principles]
- [Procedure consigliate per lo sviluppo per il cloud][best-practices]

### <a name="quality-pillars"></a>Concetti fondamentali per la qualità

Un'applicazione cloud efficace è basata su cinque concetti fondamentali per la qualità del software: scalabilità, disponibilità, resilienza, gestione e sicurezza. Usare gli elenchi di controllo per la revisione dell'architettura per rivedere la progettazione in base a questi concetti fondamentali.

Altre informazioni:

- [Concetti fondamentali della qualità del software][pillars]
- [Elenchi di controllo per l'analisi della progettazione][checklists] 

### <a name="cloud-design-patterns"></a>Schemi progettuali per il cloud

Gli schemi progettuali sono soluzioni generali per problemi comuni di progettazione del software. Sono stati identificati alcuni schemi progettuali che risultano particolarmente utili durante la progettazione di applicazioni distribuite per il cloud.

Altre informazioni:

- [Catalogo di schemi progettuali per il cloud](../patterns/index.md)


[arch-styles]: ./architecture-styles/index.md
[best-practices]: ../best-practices/index.md
[checklists]: ../checklist/index.md
[compute-options]: ./technology-choices/compute-comparison.md
[design-principles]: ./design-principles/index.md
[ebook]: https://azure.microsoft.com/campaigns/cloud-application-architecture-guide/
[patterns]: ../patterns/index.md?toc=/azure/architecture/guide/toc.json
[pillars]: ./pillars.md
[ref-archs]: ../reference-architectures/index.md
[storage-options]: ./technology-choices/data-store-comparison.md
[technology-choices]: ./technology-choices/index.md

