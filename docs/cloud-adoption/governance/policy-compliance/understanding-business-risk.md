---
title: 'CAF: Come cambiano i rischi aziendali nel cloud?'
description: Comprendere i rischi aziendali durante la migrazione
author: BrianBlanchard
ms.date: 10/10/2018
ms.openlocfilehash: 458474f3c94c5df4f7ffef439095adf138f33d78
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902140"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-business-risk-change-in-the-cloud"></a>Come cambiano i rischi aziendali nel cloud?

Comprendere quali sono i rischi per l'azienda è uno degli aspetti più importanti di qualsiasi trasformazione per il cloud. I rischi implicano la creazione di criteri e incidono sui requisiti di monitoraggio e applicazione. Inoltre, influenzano in modo determinante il modo in cui viene gestito il digital estate, in locale o nel cloud.

<!-- markdownlint-enable MD026 -->

## <a name="relativity-of-risk"></a>Relatività dei rischi

Il rischio è relativo. Una piccola impresa con poche risorse IT in un edificio chiuso ha un rischio minimo. Ma basta aumentare il numero di utenti e aggiungere una connessione Internet con accesso a tali risorse perché il rischio sia superiore. E quando questa piccola impresa entra a far parte delle Fortune 500, i rischi sono esponenzialmente maggiori. Con l'aumentare dei profitti, dei processi aziendali, del numero di dipendenti e degli asset IT, i rischi aumentano e si combinano. Gli asset IT che consentono di generare profitti sono a rischio tangibile di arrestare tale flusso di profitti in caso di interruzione. Ogni istante di inattività equivale a una perdita. Allo stesso modo, mentre i dati si accumulano, cresce il rischio di danneggiare i clienti.

Nell'ambiente tradizionale in locale i team di governance IT si concentrano sulla valutazione di questi rischi. La creazione di processi mitiga i rischi. La distribuzione di sistemi garantisce che le misure di mitigazione siano corrette e implementate. Ciò consente di bilanciare i rischi inevitabili in un ambiente aziendale moderno e connesso.

## <a name="understanding-business-risks-in-the-cloud"></a>Comprendere i rischi aziendali nel cloud

Durante una trasformazione si possono vedere gli stessi rischi relativi.

* Durante la sperimentazione iniziale alcune risorse vengono distribuite con pochi dati o con dati irrilevanti. I rischi sono minimi.
* Quando viene distribuito il primo carico di lavoro, il rischio aumenta leggermente. Questo rischio viene facilmente mitigato scegliendo un'applicazione intrinsecamente a basso rischio con una base utenti ridotta.
* Man mano che sempre più carichi di lavoro sono online, i rischi mutano a ogni rilascio. Con sempre più app operative, il rischio cambia.
* Quando un'azienda porta online le prime 10-20 applicazioni, il profilo di rischio è molto diverso rispetto a quando la millesima applicazione va in produzione nel cloud.

Gli asset che si sono accumulati nel mondo tradizionale in locale probabilmente si sono accumulati nel tempo. Il livello di maturità dell'azienda e dei team IT con molta probabilità è cresciuto in modo analogo. Questa crescita parallela può tendere a creare un bagaglio di criteri superflui.

Durante una trasformazione per il cloud, sia i team aziendali che quelli IT hanno l'opportunità di reimpostare tali criteri e crearne di nuovi con una mentalità più matura.

<!-- markdownlint-disable MD026 -->

## <a name="what-is-a-business-risk-mvp"></a>Che cos'è un prodotto minimo funzionante per i rischi aziendali?

Un **prodotto minimo funzionante** (MVP, Minimum Viable Product) è un termine standard del settore che definisce l'unità minima di un elemento in grado di produrre valore tangibile. In un MVP per i rischi aziendali il team parte dal presupposto che alcuni asset verranno distribuiti in un ambiente cloud. Non è noto al momento quali siano questi asset. Non si conoscono inoltre i tipi di dati che verranno elaborati da questi asset.

Il team di governance del cloud potrebbe ipotizzare lo scenario peggiore e associare tutti i criteri possibili al cloud. Non è consigliabile, ma è un'opzione.

Al contrario, il team potrebbe adottare un approccio basato su MVP e definire un punto di partenza e una serie di presupposti che devono essere soddisfatti per buona parte degli asset o interamente.
Di seguito sono riportati alcuni esempi estremamente semplici:

* Tutti gli asset sono a rischio di essere arrestati (a causa di problemi, errori o manutenzione)
* Tutti gli asset sono a rischio di generare troppe spese
* Tutti gli asset potrebbero essere compromessi da password vulnerabili
* Qualsiasi asset con tutte le porte aperte esposte a Internet è a rischio di essere compromesso

Gli esempi precedenti sono intesi a stabilire un MVP per i rischi aziendali in linea teorica. L'elenco effettivo sarà specifico per ogni ambiente.
Dopo che è stato stabilito un MVP per i rischi aziendali, può essere convertito in [criteri](overview.md) per mitigare i diversi rischi.

<!-- markdownlint-enable MD026 -->

## <a name="incremental-risk-mitigation"></a>Mitigazione dei rischi incrementale

Partendo dal presupposto che un MVP per i rischi aziendali sia il punto di partenza, la governance può maturare in parallelo alla distribuzione pianificata (anziché crescere in parallelo alla crescita aziendale). Si tratta di un modello molto più stabile per la maturità della governance. A ogni iterazione, i nuovi asset vengono replicati e gestiti in modo temporaneo. A ogni rilascio, i carichi di lavoro vengono preparati per la promozione della produzione. Naturalmente, il rischio relativo potrebbe crescere a ogni ciclo.

Quando il team di governance del cloud opera in parallelo con i team di adozione del cloud, la crescita dei rischi aziendali può essere trattata in modo analogo. Ogni asset gestito in modo temporaneo può essere facilmente classificato in base al rischio. I documenti per la classificazione dei dati possono essere creati in parallelo al processo di gestione temporanea. Il profilo di rischio e i punti di esposizione possono essere documentati in modo analogo. Nel tempo l'organizzazione metterà a fuoco una visione estremamente chiara dei rischi aziendali.

A ogni iterazione, il team di governance del cloud può lavorare fianco a fianco con quello di strategia cloud per comunicare rapidamente i nuovi rischi, le strategie di mitigazione, i compromessi e i costi potenziali. Questo consente ai partecipanti aziendali e ai responsabili IT di collaborare per prendere decisioni mature e ben informate, che ispireranno la maturità dei criteri. Quando richiesto, le modifiche ai criteri generano nuovi elementi di lavoro per la maturità dei sistemi di infrastruttura fondamentali. Quando sono richieste modifiche ai sistemi di gestione temporanea, i team di adozione del cloud hanno tempo a sufficienza per apportare le modifiche, mentre l'azienda testa i sistemi di gestione temporanea e sviluppa un piano di adozione per gli utenti.

Questo approccio riduce al minimo i rischi, assicurando al team la possibilità di muoversi rapidamente. Garantisce inoltre che i rischi vengano prontamente identificati e risolti prima della distribuzione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Valutare la tolleranza ai rischi](./risk-tolerance.md)
