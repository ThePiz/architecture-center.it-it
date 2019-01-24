---
title: Creazione di un business case per la migrazione cloud
titleSuffix: Enterprise Cloud Adoption
description: Aspetti da considerare durante la creazione di una motivazione aziendale per la migrazione cloud
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 8c27ce211f500ee2eec4f7775a7f68f214dba433
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54488325"
---
# <a name="enterprise-cloud-adoption-building-a-cloud-migration-business-case"></a>Adozione del cloud nell'organizzazione: Creazione di un business case per la migrazione cloud

Le migrazioni cloud possono generare un ritorno sugli investimenti (ROI) anticipato proveniente da attività di trasformazione cloud. Tuttavia, lo sviluppo di una motivazione aziendale chiara con i relativi costi e guadagni tangibili, può essere un processo complesso. Questo articolo consente di considerare quali dati sono necessari per creare un modello finanziario che si allinei con i risultati della migrazione cloud. Innanzitutto, sfatiamo alcuni miti sulla migrazione cloud, in modo che l'organizzazione possa evitare alcuni errori comuni.

## <a name="dispelling-cloud-migration-myths"></a>Miti sulla migrazione cloud

**Mito: il cloud è sempre più economico.** È una credenza comune che l’esecuzione di un data center nel cloud sia sempre più economica rispetto all’esecuzione in locale. Questo può essere vero, ma non è un imperativo. In alcuni casi, i costi operativi del cloud possono essere maggiori. Spesso questo è dovuto a governance a basso costo, disallineamento delle architetture di sistema, duplicazione dei processi, configurazioni del sistema insolite o all’aumento dei costi in termini di personale. Per fortuna, molti di questi problemi possono essere ridotti per creare un ritorno sugli investimenti anticipato. Le indicazioni fornite in [Creazione di una motivazione aziendale](#building-the-business-justification) consentono di rilevare ed evitare questi disallineamenti. Inoltre, anche sfatare altri miti elencati di seguito può aiutare.

**Mito: bisogna passare completamente al cloud.** In realtà, esistono un numero di driver di business che possono indurre a scegliere una soluzione ibrida. Prima di finalizzare il modello aziendale, è consigliabile completare una prima analisi quantitativa, come descritto negli [articoli sul digital estate](../digital-estate/5-rs-of-rationalization.md). Per ulteriori informazioni sui singoli driver quantitativi che fanno parte dalla razionalizzazione, vedere [Le 5 R della razionalizzazione](../digital-estate/5-rs-of-rationalization.md). Entrambi gli approcci usano i dati di inventario facilmente ottenuti e una breve analisi quantitativa per identificare i carichi di lavoro o applicazioni che potrebbero causare un aumento dei costi nel cloud. Questi approcci possono anche identificare le dipendenze o i modelli di traffico che richiedono una soluzione ibrida.

**Mito: il mirroring dell’ambiente locale permette di ridurre i costi del cloud.** Durante la pianificazione del digital estate, ai clienti può capitare di rilevare capacità inutilizzate che superano il 50% dell'ambiente sottoposto a provisioning. Se si effettuata il provisioning di risorse nel cloud in modo che corrisponda al provisioning corrente, sarà difficile ottenere una riduzione dei costi. Provare a ridurre la dimensione delle risorse distribuite in modo da allinearsi con i modelli di utilizzo, non con i modelli di provisioning.

**Mito: i costi dei server indirizzano i business case per la migrazione cloud.** In alcuni casi questo è vero. Per alcune aziende, è importante ridurre le spese in conto capitale costanti relative ai server. Tuttavia, questo dipende da fattori diversi. Per le aziende con un ciclo di aggiornamento hardware che va dai 5&ndash; agli 8&ndash;anni, è improbabile osservare ritorni veloci sugli investimenti dovuti alla migrazione cloud. Le aziende con cicli di aggiornamento standardizzati o imposti possano raggiungere rapidamente un punto di pareggio. In entrambi i casi, altre spese potrebbero essere i trigger finanziari che motivano la migrazione. Di seguito sono indicati alcuni esempi di costi che spesso vengono ignorati durante la considerazione dei costi basata solo sul server o sulle macchine virtuali:

- i costi software di virtualizzazione, server e middleware possono essere ingenti. I provider di servizi cloud eliminano alcuni di questi costi. Due esempi di provider di servizi cloud che riducono i costi di virtualizzazione sono i programmi [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/#services) e [Prenotazioni](https://azure.microsoft.com/reservations/).
- Le perdite aziendali dovute alle interruzioni possono superare rapidamente i costi hardware o software. Se il data center corrente è instabile, richiedere all'azienda di quantificare l'impatto delle interruzioni in termini di costi di opportunità o costi aziendali effettivi.
- Anche i costi ambientali possono avere un impatto elevato. Per una famiglia americana media, la casa è l'investimento maggiore e il costo più elevato nel proprio budget. Spesso vale lo stesso per i data center. I costi dei beni immobili, delle strutture e i costi di utilità rappresentano una parte equa dei costi locali. Quando i data center vengono dismessi, tali strutture possono essere riutilizzate dall'azienda o potenzialmente l'azienda può eliminare interamente i costi.

**Mito: le spese operative (OpEx) sono meglio delle spese in conto capitale (CapEx).** Come spiegato nell’articolo sui [risultati fiscali](business-outcomes/fiscal-outcomes.md), le spese operative possono essere un fattore positivo. Tuttavia, esistono diversi settori che possono vedere le spese operative come un fattore negativo. Di seguito sono riportati alcuni esempi che attivano un'integrazione maggiore con le unità di contabilità e aziendali relative alla conversazione sulle spese operative:

- Quando l'azienda vede le risorse di capitale come driver per la valutazione di business, le riduzioni delle spese in conto capitale potrebbero rappresentare un risultato negativo. Mentre non è uno standard universale, questa valutazione è più comune nel settore delle vendite al dettaglio, manifatturiero e delle costruzioni.
- Inoltre, l’aumento delle spese operative può essere visto come un risultato negativo nelle aziende di proprietà di una società a capitale privato o alla ricerca di un apporto di capitale.
- Se l'azienda è incentrata principalmente sul miglioramento dei margini di vendita o sulla riduzione del costo del venduto (COGS), le spese operative potrebbero essere un risultato negativo.

Le spese operative non sono sempre un fattore negativo. Generalmente le aziende considerano le spese operative più favorevoli delle spese in conto capitale. Ad esempio, questo approccio può essere adottato positivamente anche da aziende che stanno provando a migliorare il flusso di cassa, ridurre gli investimenti di capitale o diminuire le proprietà delle risorse.

Prima di fornire una motivazione aziendale incentrata su una conversione da spese in conto capitale a spese operative, individuare l'opzione migliore per l'azienda. Contabilità e approvvigionamento spesso possono aiutare ad allineare meglio il messaggio agli obiettivi finanziari.

**Mito: la migrazione al cloud è semplice come premere un interruttore.** Le migrazioni sono una trasformazione tecnica manualmente intensa. Quando si sviluppa una motivazione aziendale, in particolare le motivazioni che sono sensibili al tempo, prendere in considerazione i seguenti aspetti che potrebbero aumentare il tempo necessario per eseguire la migrazione delle risorse:

- Limitazioni della larghezza di banda: la quantità di larghezza di banda tra il datacenter corrente e il provider di servizi cloud determinerà le sequenze temporali durante la migrazione.
- Sequenze temporali del test aziendale: il test delle applicazioni con l'azienda per certificare la conformità e le prestazioni può richiedere tempi lunghi. L’allineamento agli utenti avanzati e i processi di test sono fondamentali.
- Sequenze temporali delle migrazioni: La quantità di tempo e risorse umane necessarie per eseguire la migrazione può aumentare i costi e ritardare le sequenze temporali. Inoltre, l’allocazione di dipendenti o la firma di contratti con i partner possono ritardare il processo e devono essere tenute in considerazione nel piano.

Gli ostacoli tecnici e culturali possono rallentare l'adozione del cloud. Quando il tempo è un aspetto importante della motivazione aziendale, la soluzione migliore è la corretta pianificazione. Esistono due approcci consigliati durante la pianificazione che consentono di ridurre i rischi della sequenza temporale.

- In primo luogo, investire tempo ed energie nella comprensione dei vincoli di adozione tecnici. Anche se la pressione di una migrazione veloce può essere elevata, è importante considerare sequenze temporali di esecuzione realistiche.
- In secondo luogo, se si presentano ostacoli culturali o relativi a persone, questi hanno un impatto più grave rispetto ai vincoli tecnici. L'adozione del cloud crea un cambiamento, che produce la trasformazione desiderata. Sfortunatamente, a volte le persone hanno paura del cambiamento e potrebbe essere necessario ulteriore supporto per l’allineamento al piano. Identificare le persone chiave del team che sono contrarie al cambiamento e coinvolgerle.

Per ottimizzare la conformità e la mitigazione dei rischi per la sequenza temporale, preparare gli stakeholder esecutivi allineando saldamente il valore aziendale e i risultati aziendali. Aiutare i suddetti stakeholder a comprendere il cambiamento portato da questa trasformazione. Essere chiari e impostare aspettative realistiche fin dall'inizio. Quando delle persone o una tecnologia rallentano il processo, sarà più facile ottenere il supporto della dirigenza.

## <a name="building-the-business-justification"></a>Creazione di una motivazione aziendale

Il processo seguente definisce un approccio allo sviluppo della motivazione aziendale per le migrazioni cloud. Durante la lettura di questo contenuto, se i calcoli o i termini finanziari richiedono una spiegazione aggiuntiva, consultare l'articolo sui [modelli finanziari](financial-models.md) per ulteriori chiarimenti.

Al livello più alto, la formula per una motivazione aziendale è semplice. Tuttavia, i punti dati complessi necessari per popolare la formula possono essere difficili da allineare. Al livello più alto, la motivazione aziendale è incentrata sul ritorno sugli investimenti (ROI) associato alla modifica tecnica proposta. La formula generica per il ROI è:

ROI = (guadagno proveniente dall’investimento &minus; Investimento iniziale) / investimento iniziale

L’analisi di questa formula crea un quadro specifico per la migrazione delle formule che guidano ogni variabile di input sul lato destro di questa equazione. Le altre sezioni di questo articolo offrono alcune considerazioni di cui tenere conto.

![Il ritorno sugli investimenti (ROI) è uguale al (guadagno proveniente dall'investimento - costo dell’investimento) / costo dell’investimento](../_images/formula-roi.png)

## <a name="migration-specific-initial-investment"></a>Investimento iniziale specifico per la migrazione

- Provider di servizi cloud come Azure offrono servizi di calcolo per stimare gli investimenti nel cloud. Un esempio è il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/en-in/pricing/).
- Alcuni provider di servizi cloud supportano anche calcolatori dei delta di costo. Un esempio di calcolatore dei delta dei costi è il [calcolatore dei costi totale di proprietà (TCO) di Azure](https://azure.com/tco).
- Per strutture di costo più precise, prendere in considerazione un esercizio di [pianificazione di digital estate](../digital-estate/overview.md).
- Stimare il costo della migrazione.
- Stimare il costo di qualsiasi opportunità di training prevista. [Microsoft Learn](https://docs.microsoft.com/learn/) potrebbe ridurre i costi.
- In alcune aziende, potrebbe essere necessario includere nei costi iniziali il tempo investito dai membri del personale esistente. Per istruzioni, consultare l'ufficio finanziario.
- Discutere eventuali costi aggiuntivi o l’onere dei costi con l'ufficio finanziario per la convalida.

## <a name="migration-specific-revenue-deltas"></a>Delta dei ricavi specifici per la migrazione

Spesso questo aspetto è trascurato durante la creazione di una motivazione aziendale per la migrazione. In alcune aree, il cloud può ridurre i costi. Tuttavia, l'obiettivo principale di qualsiasi trasformazione è ottenere risultati migliori nel tempo. Prendere in considerazione gli effetti a valle per comprendere i miglioramenti a lungo termine dei ricavi. Quali nuove tecnologie che non possono essere sfruttate oggi saranno disponibili per l'azienda dopo questa migrazione? Quali progetti o obiettivi aziendali sono bloccati dalle dipendenze da tecnologie obsolete? Quali programmi sono in sospeso, in attesa di elevati costi della tecnologia cap-ex?

Dopo aver considerato le opportunità sbloccate dal cloud, lavorare con l'azienda per calcolare l’aumento dei profitti che può provenire da tali opportunità.

## <a name="migration-specific-cost-deltas"></a>Delta dei costi specifici per la migrazione

Calcolare le modifiche ai costi dovute alla migrazione proposta. Consultare [modelli finanziari](financial-models.md) per informazioni dettagliate sui diversi tipi di delta dei costi. I provider di servizi cloud spesso offrono gli strumenti per il calcolo dei delta dei costi. Un esempio di calcolatore dei delta dei costi è il [calcolatore dei costi totale di proprietà (TCO) di Azure](https://azure.com/tco).

Altri esempi di costi che possono essere ridotti attraverso una migrazione cloud:

- Riduzione o dismissione dei data center (costi ambientali)
- Riduzione del consumo energetico (costi ambientali)
- Dismissione del rack (ripristino di risorse fisiche)
- Impedire un aggiornamento dell'hardware (contenimento dei costi)
- Evitare un rinnovo del software (riduzione dei costi operativi o contenimento dei costi)
- Consolidamento del fornitore (riduzione dei costi operativi e potenziale riduzione dei costi flessibili)

## <a name="when-roi-results-are-surprising"></a>Quando i risultati ROI sono sorprendenti

Se il ritorno sugli investimenti per la migrazione cloud non è in linea con le aspettative, potrebbe essere utile consultare nuovamente i miti comuni elencati all'inizio di questo articolo.

Tuttavia, è importante comprendere che un risultato di risparmio sui costi non è sempre possibile. Esistono applicazioni per cui i costi sono maggiori nel cloud rispetto a un'istanza locale. Queste applicazioni possono alterare significativamente i risultati di un'analisi.

Quando il ritorno sugli investimenti è inferiore al 20%, prendere in considerazione un esercizio di [pianificazione di digital estate](../digital-estate/overview.md), con particolare attenzione alla [razionalizzazione](../digital-estate/rationalize.md). Durante l'analisi quantitativa, eseguire una verifica di ogni applicazione per trovare i carichi di lavoro che alterano i risultati. Potrebbe essere opportuno rimuovere questi carichi di lavoro dal piano. Se sono disponibili i dati di utilizzo, provare a ridurre le dimensioni delle macchine virtuali in modo che corrispondano all’utilizzo.

Se il ritorno sugli investimenti è ancora disallineato, richiedere assistenza al rappresentante Microsoft oppure [coinvolgere un partner esperto](https://azure.microsoft.com/en-us/migration/partners/).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un modello finanziario per la trasformazione del cloud](financial-models.md)