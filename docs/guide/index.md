---
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: 530844a0d3b1256cec807e7bad509a40dca304f6
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="azure-application-architecture-guide"></a>Guida all'architettura delle applicazioni in Azure

Questa guida presenta un approccio strutturato alla progettazione di applicazioni in Azure che assicurano disponibilità elevata, resilienza e scalabilità. È basata su procedure comprovate apprese nel corso delle interazioni con i clienti.

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

**[Stili di architettura][arch-styles]**. Il primo punto di decisione è il più importante. Che stile di architettura si intende costruire? Sarà un'architettura di microservizi, un'applicazione a più livelli maggiormente tradizionale o una soluzione per Big Data? Sono stati identificati sette stili distinti. Ogni stile offre vantaggi e pone sfide.

> &#10148; Le [architetture di riferimento di Azure][ref-archs] indicano le distribuzioni consigliate in Azure, insieme a considerazioni sulla scalabilità, la disponibilità, la gestibilità e la sicurezza. La maggior parte include anche i modelli di Resource Manager.

**[Scelte tecnologiche][technology-choices]**. Nella fase iniziale è necessario adottare due scelte tecnologiche in quanto incidono sull'intera architettura. Le scelte riguardano le tecnologie di calcolo e di archiviazione. Il termine *calcolo* fa riferimento al modello di hosting per le risorse di calcolo in cui viene eseguita l'applicazione. L'archiviazione include i database ma anche l'archiviazione per le code di messaggi, le cache, i dati IoT, i dati di log non strutturati e qualsiasi altro elemento in cui è possibile salvare in modo permanente un'applicazione. 

> &#10148; Le [opzioni di calcolo][compute-options] e le [opzioni di archiviazione][storage-options] forniscono criteri di confronto dettagliati per la selezione dei servizi di calcolo e di archiviazione.

**[Principi di progettazione][design-principles]**. Durante il processo di progettazione tenere presente questi dieci principi di alto livello. 

> &#10148; L'articolo [Procedure consigliate][best-practices] fornisce indicazioni specifiche su aree, come la scalabilità automatica, la memorizzazione nella cache, il partizionamento dei dati, la progettazione di API e altro.   

**[Concetti fondamentali][pillars]**. Un'applicazione cloud efficace è basata su cinque concetti fondamentali per la qualità del software: scalabilità, disponibilità, resilienza, gestione e sicurezza. 

> &#10148; Usare gli [elenchi di controllo per la revisione della progettazione][checklists] per rivedere la progettazione in base a questi concetti fondamentali. 

**[Schemi progettuali per il cloud][patterns]**. Questi schemi progettuali sono utili per la compilazione di applicazioni affidabili, scalabili e sicure in Azure. Ogni modello descrive un problema, un modello che risolve il problema e un esempio basato su Azure.

> &#10148; Visualizzare il [catalogo degli schemi progettuali per il cloud](../patterns/index.md) completo.


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

