---
title: 'CAF: Rischi aziendali associati a una trasformazione cloud'
description: Spiegazione dei rischi aziendali associati a una trasformazione cloud.
author: BrianBlanchard
ms.date: 10/10/2018
ms.openlocfilehash: 774a6703b353a3c670b3764505185aecb794784f
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068940"
---
# <a name="evaluating-risk-tolerance"></a>Valutazione della tolleranza ai rischi

Ogni decisione aziendale business comporta nuovi rischi. Qualsiasi investimento comporta il rischio di perdite. I nuovi prodotti o servizi comportano rischi di fallimento sul mercato. Le modifiche apportate a prodotti o servizi correnti potrebbero ridurre la quota di mercato. La trasformazione cloud non offre una soluzione magica per rischi aziendali quotidiani. Al contrario, le soluzioni connesse (cloud o locali) creano nuovi rischi. La distribuzione di asset in qualsiasi struttura connessa in rete espande anche il potenziale profilo di rischio esponendo le vulnerabilità della sicurezza a una più ampia community globale. Fortunatamente i provider di servizi cloud, essendo consapevoli di come i rischi cambiano e aumentano e che ne nascono di nuovi, Che investono molto per ridurre e gestire tali rischi per conto dei clienti.

L'argomento di questo articolo non sono i rischi del cloud, ma i rischi aziendali associati a varie forme di trasformazione cloud. Più avanti in questo articolo, viene illustrato come determinare la tolleranza al rischio dell'azienda.

<!-- markdownlint-disable MD026 -->

## <a name="what-business-risks-are-associated-with-a-cloud-transformation"></a>Rischi aziendali associati a una trasformazione cloud

I rischi aziendali true si baserà le novità e come dietro trasformazioni specifiche. ma fortunatamente esistono diversi rischi comuni che possono essere usati come punto di partenza per comprendere i rischi personali specifici.

* * Prima di leggere quanto segue, prestare attenzione che ognuno di tali rischi può essere gestito. L'obiettivo di questo articolo consiste nell'informare e preparare i lettori, in modo che discussione di gestione di rischio può essere più efficace. **

- **Violazione dei dati**: il rischio più grave associato a qualsiasi trasformazione è quello relativo alla protezione dei dati. Perdite di dati può causare danni significativi per l'azienda, causando una perdita di clienti, business o legali anche non diminuisce. Qualsiasi modifica al modo in cui i dati vengono archiviati, elaborati o usati comporta un rischio. Le trasformazioni cloud apportano modifiche significative alla gestione dei dati e per questo è importante non sottovalutare il rischio. [Baseline della sicurezza](../security-baseline/overview.md), [classificazione dei dati](./what-is-data-classification.md), e [razionalizzazione incrementale](../../digital-estate/rationalize.md#incremental-rationalization) ognuno consentono di gestire questo rischio.

- **Interruzione del servizio**: Le operazioni aziendali e le esperienze dei clienti dipendono in modo significativo operazioni tecniche. Le trasformazioni cloud modificheranno le operazioni tecniche. In alcune organizzazioni, che cambia è piccolo e semplice regolata. In altre, le modifiche alle operazioni tecniche potrebbero richiedere l'adozione di nuovi strumenti e competenze o di nuovi approcci per il supporto. Più grande della modifica, maggiori saranno le dimensioni i potenziali effetti sulle operazioni aziendali e l'esperienza dei clienti. La gestione di tale rischio richiederà il coinvolgimento dell'azienda nella pianificazione della trasformazione. Le sezioni Pianificazione del rilascio e Selezione del primo carico di lavoro dell'articolo [Razionalizzazione incrementale](../../digital-estate/rationalize.md#incremental-rationalization) illustrano come scegliere i carichi di lavoro per i progetti di trasformazione. Ruolo di business in quanto attività consiste nel comunicare il rischio di operazioni di Business di cambiare la priorità i carichi di lavoro. IT per aiutarti a scegliere i carichi di lavoro che hanno un impatto minore sulle operazioni si riduce il rischio complessivo.

- **Controllo del budget**: I modelli di costo sono diversi nel cloud. Questa modifica può comportare rischi associati ai costi eccessivi o all'aumento del costo del venduto, soprattutto delle spese operative direttamente attribuite. Quando business opera in stretta collaborazione con IT, è possibile creare la trasparenza dei costi e i servizi usati da diverse business unit, programmi, progetti e così via... [Gestione dei costi](../cost-management/overview.md) vengono forniti esempi dei modi Business e IT possono collaborare in questo argomento.

Quelli precedenti sono alcuni dei rischi più comuni indicati dai clienti. Il team di governance cloud e i team di adozione del cloud possono iniziare a sviluppare un profilo di rischio, man mano che viene eseguita la migrazione dei carichi di lavoro e viene preparata la versione di produzione. Prepararsi per le conversazioni definire, ottimizzare e gestire i rischi in base i risultati di business desiderato e lo sforzo di trasformazione.

## <a name="understanding-risk-tolerance"></a>Informazioni sulla tolleranza ai rischi

L'identificazione dei rischi è un processo abbastanza diretto. I rischi correlati all'IT sono in genere standard in vari settori. Tuttavia, tolleranza di errore per questi rischi è specifica per ogni organizzazione. È qui che le discussioni tra azienda e IT tendono a interrompersi. Ogni parte coinvolta sta essenzialmente parlando una lingua diversa. I confronti e le domande seguenti sono pensati per dare inizio a discussioni che consentono a ogni parte di comprendere e calcolare meglio la tolleranza ai rischi.

## <a name="simple-use-case-for-comparison"></a>Caso d'uso semplice per il confronto

Per comprendere la tolleranza ai rischi, esaminiamo i dati dei clienti. Se un'azienda in qualsiasi settore invia i dati del cliente in un server non protetto, il rischio tecnico dei dati viene compromesso o rubato è all'incirca lo stesso. Tuttavia, tolleranza dell'azienda per tale rischio variano notevolmente base il valore di natura e la potenziale dei dati.

- Le società che operano nei settori sanitario e finanziario negli Stati Uniti sono regolate da rigidi requisiti di conformità di terze parti. Si presuppone che le informazioni personali o i dati sanitari siano estremamente riservati. Questi tipi di società, se coinvolti nello scenario di rischio precedente, incorrono in gravi conseguenze. La loro tolleranza sarà estremamente bassa. I dati dei clienti pubblicati all'interno o all'esterno della rete dovranno essere regolati da tali criteri di conformità di terze parti.
- Una società di giochi con i dati dei clienti è limitati a un nome utente, tempi di riproduzione e i punteggi migliori non è sempre ad accusare eventuali conseguenze significative, se sono coinvolgere il comportamento a rischio precedente. Anche se tutti i dati non protetti sono a rischio, l'impatto di tale rischio è ridotto. Pertanto, la tolleranza ai rischi in questo caso è elevata.
- Un'azienda di medie dimensioni che fornisce servizi per migliaia di clienti di pulizia carpet sarebbe rientrano tra questi estremi due tolleranza. I dati dei clienti potrebbero esserci più affidabile, che contiene i dettagli, ad esempio indirizzo o numero di telefono. Entrambi possono essere considerate informazioni personali e devono essere protetti. Potrebbe tuttavia non essere previsto alcun requisito di governance specifico che imponga di proteggere i dati. Dal punto di vista dell'IT, la risposta è semplice: proteggere i dati. Dal punto di vista dell'azienda, potrebbe non essere così semplice. L'azienda avrà bisogno di altre informazioni prima di poter determinare un livello di tolleranza a questo rischio.

Nella sezione successiva condivide alcune domande di esempio che può aiutare le aziende a determinare un livello di tolleranza al rischio per il caso d'uso precedenti o altri utenti.

## <a name="risk-tolerance-questions"></a>Domande sulla tolleranza di rischio

In questa sezione elenca conversazione stimolanti domande in tre categorie: impatto di perdita, probabilità di perdita e i costi di monitoraggio e aggiornamento. Quando business e partner IT che per ognuna di queste aree, la decisione di impiegare sforzi sulla gestione dei rischi e la tolleranza complessiva a un rischio per la specifica di indirizzi può essere determinate con facilità.

**Impatto di una perdita**: domande per determinare l'impatto di un rischio. Può essere difficile (talvolta impossibile) rispondere a queste domande. Quantificare l'impatto è l'ideale, ma in alcuni casi la discussione da sola è sufficiente a comprendere la tolleranza. Anche gli intervalli sono accettabili, soprattutto se includono i presupposti che li hanno determinati.

- Questo rischio viola i requisiti di conformità di terze parti?
- Questo rischio viola i criteri aziendali interni?
- Questo rischio potrebbe comportare la perdita di clienti o della quota di mercato? In questo caso, può essere quantificato questo costo?
- Questo rischio potrebbe comportare esperienze negative per i clienti? È probabile che influiscono sulle vendite o realizzazione di ricavi tali esperienze?
- Questo rischio potrebbe comportare nuove responsabilità legali? In tal caso, esiste una precedenza per il risarcimento dei danni in questi tipi di casi?
- Questo rischio potrebbe arrestare le operazioni aziendali? In questo caso, per quanto tempo le operazioni sarebbero ferme?
- Questo rischio potrebbe rallentare le operazioni aziendali? In questo caso, come lento e per quanto tempo?
- In questa fase della trasformazione, si tratta di un rischio occasionale o si ripeterà?
- La frequenza di questo rischio aumenta o diminuisce con l'avanzamento della trasformazione?
- La probabilità di questo rischio aumenta o diminuisce nel corso del tempo?
- Il rischio è per natura soggetto a variazioni nel tempo? Il rischio passerà o peggiorerà, se non viene affrontato?

Queste domande di base ne susciteranno molte altre. Dopo aver avuto un dialogo costruttivo, è consigliabile registrare e, se possibile, quantificare i rischi pertinenti.

**I costi di monitoraggio e aggiornamento di rischio**: Domande per determinare il costo della rimozione o in caso contrario, riducendo al minimo il rischio. Queste domande possono essere piuttosto dirette, soprattutto se poste in successione.

- È disponibile una soluzione definitiva? Quanto costa?
- Sono presenti opzioni per prevenire o ridurre al minimo tale rischio? Qual è la fascia di costo di tali soluzioni?
- Che cosa serve all'azienda per scegliere la migliore soluzione definiva?
- Che cosa serve all'azienda per convalidare i costi?
- Quali altri vantaggi possono provenire dalla soluzione per rimuoverebbe questo rischio?

Queste domande su semplificano le soluzioni ai problemi tecnici necessari per gestire o rimuovere i rischi. ma comunicano tali soluzioni in modo che l'azienda possa integrarle rapidamente in un processo decisionale.

**Probabilità di perdita**: domande per determinare la probabilità che il rischio diventi reale. Questa è la parte più difficile quantificare. In alternativa, è consigliabile che il team di governance cloud crei le categorie per comunicare la probabilità, in base ai dati di supporto. Le domande seguenti consentono di creare categorie significative per il team.

- Sono state eseguite ricerche riguardanti la probabilità che questo rischio diventi reale?
- Il fornitore può mettere a disposizione riferimenti o statistiche sulla probabilità dell'impatto?
- Esistono altre società del settore o del segmento verticale pertinente che hanno corso questo rischio?
- In generale, esistono altre società che hanno corso questo rischio?
- Questo rischio è dovuto esclusivamente a qualcosa che la società ha fatto in modo non adeguato?

Dopo aver risposto a queste domande con domande, come determinato dal team di Governance Cloud, verranno probabilmente emergere raggruppamenti di probabilità. Ecco alcuni esempi di raggruppamento da cui iniziare:

- Nessuna indicazione: Non è sufficiente research è stata completata per determinare la probabilità.
- Rischio basso: Ricerca corrente viene suggerito che il rischio non sembra essere realizzati.
- Rischio futuro: la probabilità corrente è di rischio basso, Tuttavia, continua adozione attiverà un'analisi aggiornata.
- Rischio medio: È probabile che il rischio influirà il business.
- Rischio elevato: Nel tempo, l'aumento meno probabile che l'azienda eviterà l'impatto di questo rischio.
- Rischio in diminuzione: il rischio è da medio a elevato, ma le azioni nell'IT o nell'azienda stanno riducendo la probabilità di conseguenze.

**Determinazione della tolleranza:**

i tre gruppi di domande precedenti dovrebbero fornire dati sufficienti per determinare i livelli di tolleranza iniziali. Quando rischio e probabilità sono basse e bonifica dei rischi è elevati, l'azienda è improbabile che investono in Monitoraggio e aggiornamento. Quando rischio e probabilità sono elevati, l'azienda è probabile che si consideri un investimento, purché i costi non superino i potenziali rischi.

## <a name="next-steps"></a>Passaggi successivi

Questo tipo di discussione può aiutare l'azienda e l'IT valutare più efficacemente la tolleranza. Queste discussioni possono essere usare durante la creazione di criteri MVP e durante l'analisi incrementale dei criteri.

> [!div class="nextstepaction"]
> [Definire i criteri aziendali](./define-policy.md)
