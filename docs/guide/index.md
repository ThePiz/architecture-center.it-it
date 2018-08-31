---
layout: LandingPage
ms.topic: landing-page
ms.date: 08/30/2018
ms.openlocfilehash: a1cac753d384c0fee0af204cddaeea1e63213b9f
ms.sourcegitcommit: ae8a1de6f4af7a89a66a8339879843d945201f85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43325860"
---
# <a name="azure-application-architecture-guide"></a>Guida all'architettura delle applicazioni in Azure

Questa guida presenta un approccio strutturato alla progettazione di applicazioni in Azure che assicurano disponibilità elevata, resilienza e scalabilità. È basata su procedure comprovate apprese nel corso delle interazioni con i clienti.

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

- [Stili di architettura](./architecture-styles/index.md)

### <a name="technology-choices"></a>Scelte di tecnologia

Nella fase iniziale è necessario adottare due scelte tecnologiche in quanto incidono sull'intera architettura. Si tratta della scelta del servizio di calcolo e degli archivi dati. *Calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui vengono eseguite le applicazioni. Gli *archivi dati* includono i database ma anche l'archiviazione per le code di messaggi, le cache, i log e qualsiasi altro elemento in cui è possibile salvare in modo permanente un'applicazione. 

Altre informazioni:

- [Scelta di un servizio di calcolo](./technology-choices/compute-overview.md)
- [Scelta di un archivio dati](./technology-choices/data-store-overview.md)

### <a name="design-principles"></a>Principi di progettazione

Sono stati identificati i principi di progettazione generali che consentiranno di rendere l'applicazione più scalabile, resiliente e gestibile. Questi principi di progettazione sono applicabili a qualunque stile di architettura. Durante il processo di progettazione tenere presente questi dieci principi di alto livello. È quindi possibile prendere in considerazione il set di procedure consigliate per aspetti specifici dell'architettura, ad esempio il ridimensionamento automatico, la memorizzazione nella cache, il partizionamento dei dati, la progettazione delle API e altro ancora.

Altre informazioni:

- [Principi di progettazione](./design-principles/index.md)


### <a name="quality-pillars"></a>Concetti fondamentali per la qualità

Un'applicazione cloud efficace è basata su cinque concetti fondamentali per la qualità del software: scalabilità, disponibilità, resilienza, gestione e sicurezza. Usare gli elenchi di controllo per la revisione dell'architettura per rivedere la progettazione in base a questi concetti fondamentali.

- [Concetti fondamentali per la qualità](./pillars.md)


[ebook]: https://azure.microsoft.com/campaigns/cloud-application-architecture-guide/
