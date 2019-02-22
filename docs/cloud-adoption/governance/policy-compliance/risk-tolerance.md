---
title: 'CAF: Rischi aziendali associati a una trasformazione cloud'
description: Spiegazione dei rischi aziendali associati a una trasformazione cloud.
author: BrianBlanchard
ms.date: 10/10/2018
ms.openlocfilehash: bfd91da42d20a85004debc6b767a1482ba3e158d
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902083"
---
# <a name="evaluating-risk-tolerance"></a>Valutazione della tolleranza ai rischi

Ogni decisione aziendale business comporta nuovi rischi. Qualsiasi investimento comporta il rischio di perdite. I nuovi prodotti o servizi comportano rischi di fallimento sul mercato. Le modifiche apportate a prodotti o servizi correnti potrebbero ridurre la quota di mercato. La trasformazione cloud non offre una soluzione magica per rischi aziendali quotidiani. Al contrario, le soluzioni connesse (cloud o locali) creano nuovi rischi. La distribuzione di asset in qualsiasi struttura connessa in rete espande anche il potenziale profilo di rischio esponendo le vulnerabilità della sicurezza a una più ampia community globale. Fortunatamente i provider di servizi cloud, essendo consapevoli di come i rischi cambiano e aumentano e che ne nascono di nuovi, investono molto per ridurre tali rischi per conto dei clienti.

L'argomento di questo articolo non sono i rischi del cloud, ma i rischi aziendali associati a varie forme di trasformazione cloud. Più avanti in questo articolo, viene illustrato come determinare la tolleranza al rischio dell'azienda.

<!-- markdownlint-disable MD026 -->

## <a name="what-business-risks-are-associated-with-a-cloud-transformation"></a>Rischi aziendali associati a una trasformazione cloud

Purtroppo, i veri rischi aziendali dipendono dall'oggetto e dalla modalità delle trasformazioni specifiche, ma fortunatamente esistono diversi rischi comuni che possono essere usati come punto di partenza per comprendere i rischi personali specifici.

** Prima di proseguire con la lettura, si ricordi che ognuno di questi rischi può essere eliminato. L'obiettivo di questo articolo è informare e preparare i lettori, in modo che le discussioni siano più efficaci nella mitigazione dei rischi. **

* Rischio relativo alla protezione dei dati: il rischio più grave associato a qualsiasi trasformazione è quello relativo alla protezione dei dati. Nell'odierna era digitale del business, i dati sono il nuovo carburante, che alimenta l'economia, mantiene efficiente il posto di lavoro, coinvolge i clienti e, quando viene a mancare, il risultato è altrettanto disastroso. Qualsiasi modifica al modo in cui i dati vengono archiviati, elaborati o usati comporta un rischio. Le trasformazioni cloud apportano modifiche significative alla gestione dei dati e per questo è importante non sottovalutare il rischio. [Baseline di sicurezza](../security-baseline/overview.md), [classificazione dei dati](./what-is-data-classification.md) e [razionalizzazione incrementale](../../digital-estate/rationalize.md#incremental-rationalization) consentono ognuna di mitigare questo rischio.

* Rischio relativo alle operazioni e all'esperienza cliente: le operazioni aziendali e le esperienze dei clienti si basano sulle operazioni tecniche. Le trasformazioni cloud modificheranno le operazioni tecniche. In alcune organizzazioni tale modifica è piccola e facilmente adattabile. In altre, le modifiche alle operazioni tecniche potrebbero richiedere l'adozione di nuovi strumenti e competenze o di nuovi approcci per il supporto. Più grande è il cambiamento, maggiore sarà l'impatto potenziale sulle operazioni aziendali e sull'esperienza cliente. Per mitigare questo rischio, sarà necessario il coinvolgimento dell'azienda nella pianificazione della trasformazione. Le sezioni Pianificazione del rilascio e Selezione del primo carico di lavoro dell'articolo [Razionalizzazione incrementale](../../digital-estate/rationalize.md#incremental-rationalization) illustrano come scegliere i carichi di lavoro per i progetti di trasformazione. Il ruolo dell'azienda in tale attività è quello di comunicare il rischio per le operazioni aziendali derivante dalle modifiche ai carichi di lavoro classificati in ordine di priorità. Aiutando l'IT a scegliere i carichi di lavoro con un impatto ridotto sulle operazioni, sarà possibile ridurre il rischio complessivo.

* Rischio relativo ai costi: I modelli di costo sono diversi nel cloud. Questa modifica può comportare rischi associati ai costi eccessivi o all'aumento del costo del venduto, soprattutto delle spese operative direttamente attribuite. Quando l'azienda opera in stretta collaborazione con l'IT, è possibile creare trasparenza per i costi e i servizi utilizzati da diverse business unit, programmi, progetti e così via. La sezione relativa alla [gestione dei costi] fornisce esempi di come l'azienda e l'IT possano collaborare in quest'ambito.

Quelli precedenti sono alcuni dei rischi più comuni indicati dai clienti. Il team di governance cloud e i team di adozione del cloud possono iniziare a sviluppare un profilo di rischio, man mano che viene eseguita la migrazione dei carichi di lavoro e viene preparata la versione di produzione. Prepararsi alle discussioni per definire, ottimizzare e mitigare i rischi in base ai risultati aziendali desiderati e al lavoro richiesto per la trasformazione.

## <a name="understanding-risk-tolerance"></a>Informazioni sulla tolleranza ai rischi

L'identificazione dei rischi è un processo abbastanza diretto. I rischi sono più o meno gli stessi nei vari settori, ma la tolleranza al rischio è estremamente soggettiva. È qui che le discussioni tra azienda e IT tendono a interrompersi. Ogni parte coinvolta sta essenzialmente parlando una lingua diversa. I confronti e le domande seguenti sono pensati per dare inizio a discussioni che consentono a ogni parte di comprendere e calcolare meglio la tolleranza ai rischi.

## <a name="simple-use-case-for-comparison"></a>Caso d'uso semplice per il confronto

Per conoscere la tolleranza ai rischi, verranno esaminati i dati dei clienti. Se una società in qualsiasi settore pubblica i dati di un cliente in un server non protetto, il rischio che tali dati vengano compromessi o rubati è sostanzialmente lo stesso, ma la natura dei dati e la tolleranza delle società a tale rischio cambiano notevolmente.

* Le società che operano nei settori sanitario e finanziario negli Stati Uniti sono regolate da rigidi requisiti di conformità di terze parti. Si presuppone che le informazioni personali o i dati sanitari siano estremamente riservati. Questi tipi di società, se coinvolti nello scenario di rischio precedente, incorrono in gravi conseguenze. La loro tolleranza sarà estremamente bassa. I dati dei clienti pubblicati all'interno o all'esterno della rete dovranno essere regolati da tali criteri di conformità di terze parti.
* È probabile che una società di giochi online, in cui i dati dei clienti sono limitati a nome utente, durata delle partite e punteggi migliori, non incorrerà in conseguenze significative, qualora adottasse il comportamento a rischio indicato sopra. Anche se i dati non protetti sono a rischio, l'impatto di tale rischio è minimo, quindi la tolleranza al rischio in questo caso è elevata.
* Un'azienda di medie dimensioni che fornisce servizi di pulizia di moquette a migliaia di clienti si collocherebbe tra questi due estremi di tolleranza. Potrebbero esserci dati dei clienti più affidabili, contenenti dettagli come indirizzi o numeri di telefono, considerati entrambi informazioni personali e quindi da proteggere. Potrebbe tuttavia non essere previsto alcun requisito di governance specifico che imponga di proteggere i dati. Dal punto di vista dell'IT, la risposta è semplice: proteggere i dati. Dal punto di vista dell'azienda, potrebbe non essere così semplice. L'azienda avrà bisogno di altre informazioni prima di poter determinare un livello di tolleranza a questo rischio.

La sezione successiva elenca alcune domande di esempio che possono aiutare l'azienda a determinare un livello di tolleranza al rischio per il caso d'uso precedente o anche per altri.

## <a name="risk-tolerance-questions"></a>Domande sulla tolleranza di rischio

Questa sezione elenca, in tre categorie, le domande che suscitano discussioni: impatto delle perdite, probabilità di perdita e costi della mitigazione. Quando l'azienda e l'IT collaborano per risolvere ognuna di queste problematiche, la decisione di mitigare i rischi e la tolleranza complessiva a un particolare rischio possono essere determinate con facilità.

**Impatto delle perdite**: domande per determinare l'impatto di un rischio. Può essere difficile (talvolta impossibile) rispondere a queste domande. Quantificare l'impatto è l'ideale, ma in alcuni casi la discussione da sola è sufficiente a comprendere la tolleranza. Anche gli intervalli sono accettabili, soprattutto se includono i presupposti che li hanno determinati.

* Questo rischio viola i requisiti di conformità di terze parti?
* Questo rischio viola i criteri aziendali interni?
* Questo rischio potrebbe comportare la perdita di clienti o della quota di mercato? In questo caso, è possibile quantificarne l'impatto?
* Questo rischio potrebbe comportare esperienze negative per i clienti? Tali esperienze potrebbero influire sulle vendite o sulla realizzazione dei ricavi?
* Questo rischio potrebbe comportare nuove responsabilità legali? In tal caso, esiste una precedenza per il risarcimento dei danni in questi tipi di casi?
* Questo rischio potrebbe arrestare le operazioni aziendali? In questo caso, per quanto tempo le operazioni sarebbero ferme?
* Questo rischio potrebbe rallentare le operazioni aziendali? In questo caso, di quanto e per quanto tempo?
* In questa fase della trasformazione, si tratta di un rischio occasionale o si ripeterà?
* La frequenza di questo rischio aumenta o diminuisce con l'avanzamento della trasformazione?
* La probabilità di questo rischio aumenta o diminuisce nel corso del tempo?
* Il rischio è per natura soggetto a variazioni nel tempo? Il rischio passerà o peggiorerà, se non viene affrontato?

Queste domande di base ne susciteranno molte altre. Dopo aver avuto un dialogo costruttivo, è consigliabile registrare e, se possibile, quantificare i rischi pertinenti.

**Costi della mitigazione**: domande per determinare il costo della mitigazione o dell'eliminazione del rischio. Queste domande possono essere piuttosto dirette, soprattutto se poste in successione.

* È disponibile una soluzione definitiva? Quanto costa?
* Sono disponibili opzioni per risolvere o mitigare questo rischio? Qual è la fascia di costo di tali soluzioni?
* Che cosa serve all'azienda per scegliere la migliore soluzione definiva?
* Che cosa serve all'azienda per convalidare i costi?
* Quali altri vantaggi possono derivare dalla soluzione che mitigherà questo rischio?

Le domande precedenti semplificano eccessivamente le soluzioni tecniche necessarie per mitigare i rischi, ma comunicano tali soluzioni in modo che l'azienda possa integrarle rapidamente in un processo decisionale.

**Probabilità di perdita**: domande per determinare la probabilità che il rischio diventi reale. Questa è la parte più difficile quantificare. In alternativa, è consigliabile che il team di governance cloud crei le categorie per comunicare la probabilità, in base ai dati di supporto. Le domande seguenti consentono di creare categorie significative per il team.

* Sono state eseguite ricerche riguardanti la probabilità che questo rischio diventi reale?
* Il fornitore può mettere a disposizione riferimenti o statistiche sulla probabilità dell'impatto?
* Esistono altre società del settore o del segmento verticale pertinente che hanno corso questo rischio?
* In generale, esistono altre società che hanno corso questo rischio?
* Questo rischio è dovuto esclusivamente a qualcosa che la società ha fatto in modo non adeguato?

Dopo aver risposto a queste e ad altre domande, come stabilito dal team di governance cloud, è probabile che emergano gruppi di probabilità. Ecco alcuni esempi di raggruppamento da cui iniziare:

* Nessuna indicazione: non è stata portata a termine una ricerca sufficiente per determinare la probabilità
* Rischio basso: la ricerca corrente suggerisce che è improbabile che il rischio diventi reale
* Rischio futuro: la probabilità corrente è di rischio basso, ma la persistenza del rischio avvierebbe una nuova analisi
* Rischio medio: è probabile che il rischio influirà sull'azienda
* Rischio elevato: nel corso del tempo sarà sempre meno probabile che l'azienda possa evitare le conseguenze di questo rischio
* Rischio in diminuzione: il rischio è da medio a elevato, ma le azioni nell'IT o nell'azienda stanno riducendo la probabilità di conseguenze.

**Determinazione della tolleranza:**

i tre gruppi di domande precedenti dovrebbero fornire dati sufficienti per determinare i livelli di tolleranza iniziali. Quando il rischio e la probabilità sono bassi e i costi per la mitigazione sono elevati, è improbabile che l'azienda investa nella correzione. Quando il rischio e la probabilità sono elevati, è probabile che l'azienda consideri l'opportunità di un investimento, purché i costi non superino i rischi potenziali.

## <a name="next-steps"></a>Passaggi successivi

Questo tipo di discussione può aiutare l'azienda e l'IT valutare più efficacemente la tolleranza. Queste discussioni possono essere usare durante la creazione di criteri MVP e durante l'analisi incrementale dei criteri.

> [!div class="nextstepaction"]
> [Definire i criteri aziendali](./define-policy.md)
