---
title: 'CAF: Processi di conformità ai criteri per la coerenza delle risorse'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Processi di conformità ai criteri per la coerenza delle risorse
author: BrianBlanchard
ms.openlocfilehash: 26c2d60635a98a3ce061352979ddf01426947e08
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901763"
---
# <a name="resource-consistency-policy-compliance-processes"></a>Processi di conformità ai criteri per la coerenza delle risorse

Questo articolo illustra un approccio ai processi di conformità ai criteri che regolano la [coerenza delle risorse](./overview.md). Una governance efficace della coerenza delle risorse del cloud inizia con processi manuali ricorrenti progettati per identificare inefficienze operative, migliorare la gestione delle risorse distribuite e garantire interruzioni minime per i carichi di lavoro cruciali. Questi processi manuali sono supportati da attività di monitoraggio, automazione e strumenti per ridurre il carico della governance e consentire risposte più rapide in caso di deviazioni dai criteri.

## <a name="planning-review-and-reporting-processes"></a>Processi di pianificazione, verifica e creazione di report

Le piattaforme cloud offrono un'ampia gamma di strumenti e funzionalità di gestione che è possibile usare per organizzazione, provisioning, scalabilità e contenimento dei tempi di inattività. L'uso di questi strumenti per strutturare e gestire in modo efficace le distribuzioni nel cloud in modo da ridurre i rischi potenziali, richiede processi e criteri ben congegnati oltre alla stretta collaborazione con i team responsabile delle operazioni IT e gli stakeholder dell'azienda.

Il set seguente contiene processi di esempio comunemente coinvolti nella disciplina Coerenza delle risorse. Usare questi esempi come punto di partenza per la pianificazione di processi che consentono di continuare ad aggiornare i criteri di coerenza delle risorse in base ai cambiamenti aziendali e al feedback da parte dei team di sviluppo e IT incaricati di trasformare le linee guida in azioni.

**Valutazione iniziale dei rischi e pianificazione**: come parte dell'adozione iniziale della disciplina Coerenza delle risorse, identificare i principali rischi aziendali e le tolleranze correlate alle operazioni e alla gestione IT. Usare queste informazioni per discutere dei rischi tecnici specifici con i membri dei team IT e i proprietari dei carichi di lavoro, in modo da poter sviluppare un set di base di criteri per la coerenza delle risorse progettati per mitigare tali rischi, stabilendo la strategia di governance iniziale.

**Pianificazione della distribuzione**: prima della distribuzione di qualsiasi asset, eseguire una verifica per identificare eventuali nuovi rischi operativi. Stabilire i requisiti a livello di risorse e i modelli di richiesta previsti, oltre a identificare le esigenze di scalabilità e le potenziali opportunità di ottimizzazione dell'utilizzo. Assicurarsi inoltre che siano presenti piani di backup e ripristino.

**Test della distribuzione**: come parte della distribuzione, il team di governance del cloud, in collaborazione con i team responsabili delle operazioni nel cloud, sarà responsabile della verifica della distribuzione per convalidare la conformità ai criteri di coerenza delle risorse.

**Pianificazione annuale**: su base annuale, eseguire una verifica generale della strategia di coerenza delle risorse. Esplorare i piani per l'espansione aziendale futura o le priorità dell'azienda e aggiornare le strategie di adozione del cloud per identificare potenziali aumenti dei rischi e altre esigenze emergenti per la coerenza delle risorse. In questa fase, esaminare anche le procedure consigliate più aggiornate per la coerenza delle risorse del cloud e integrarle nei criteri e nei processi di verifica dell'azienda.

**Pianificazione e verifica trimestrale**: eseguire con cadenza trimestrale una verifica dei report sui dati operativi e degli incidenti per identificare eventuali modifiche necessarie nei criteri per la coerenza delle risorse. Come parte di questo processo, esaminare le modifiche nell'utilizzo delle risorse e delle prestazioni per identificare gli asset che richiedono aumenti o diminuzioni nell'allocazione delle risorse e identificare eventuali carichi di lavoro o asset candidati per il ritiro.

Questo processo di pianificazione offre anche l'opportunità di valutare se esistono lacune tra i membri correnti del team di governance del cloud in merito a criteri e rischi nuovi o in evoluzione correlati alla coerenza delle risorse come disciplina. Invitare il personale IT pertinente a partecipare alle attività di verifica e pianificazione come consulenti tecnici temporanei o come membri permanenti del team.

**Didattica e training**: offrire sessioni di training su base bimestrale per assicurarsi che gli sviluppatori e il personale IT siano sempre aggiornati sui requisiti e le linee guida più recenti per i criteri di conformità delle risorse. Come parte di questo processo, rivedere e aggiornare qualsiasi documentazione o altre risorse di training per assicurarsi che siano in linea con le definizioni dei criteri aziendali più recenti.

**Controllo mensile e creazione di report sulle revisioni**: su base mensile, eseguire un controllo su tutte le distribuzioni cloud per garantire il costante allineamento con i criteri di coerenza delle risorse. Esaminare le attività correlate con il personale IT e identificare eventuali problemi di conformità non ancora gestiti come parte del processo continuo di monitoraggio e imposizione. Il risultato di questa revisione è un report destinato al team responsabile della strategia per il cloud e a ogni team responsabile dell'adozione del cloud per comunicare le prestazioni complessive e l'aderenza generale ai criteri. Il report viene inoltre archiviato a scopo legale e di controllo.

## <a name="ongoing-monitoring-processes"></a>Processi di monitoraggio continuo

La determinazione del buon esito della strategia di governance della coerenza delle risorse dipende dalla visibilità dello stato corrente e passato dell'infrastruttura cloud. Senza la possibilità di analizzare le metriche e i dati pertinenti sull'integrità e sulle attività dell'ambiente cloud, non è possibile identificare cambiamenti nei rischi o rilevare violazioni delle tolleranze ai rischi. I processi di governance regolari illustrati in precedenza richiedono dati di qualità per assicurarsi che i criteri possano essere modificati per ottimizzare l'utilizzo delle risorse cloud e migliorare le prestazioni complessive dei carichi di lavoro ospitati nel cloud.

Verificare che i team IT abbiano implementato sistemi di monitoraggio automatizzati per l'infrastruttura cloud, che consentono di acquisire i dati di log rilevanti di cui si ha bisogno per valutare i rischi. Essere proattivi nel monitoraggio di questi sistemi garantisce la rapida individuazione e mitigazione di potenziali violazioni dei criteri e assicura che la strategia di monitoraggio sia in linea con le esigenze operative.

## <a name="violation-triggers-and-enforcement-actions"></a>Trigger per le violazioni e azioni di imposizione

Poiché la conformità ai criteri di coerenza delle risorse può causare interruzioni di servizi cruciali o rischi significativi di sforamento dei costi, il team di governance del cloud deve avere visibilità sugli eventi imprevisti di non conformità. Verificare che il personale IT possa fare riferimento a percorsi di escalation chiari per segnalare questi problemi ai membri del team di governance più adatti a identificare e verificare che i problemi relativi ai criteri vengano risolti.  

Quando vengono rilevate violazioni, si devono intraprendere azioni per riallinearsi il prima possibile ai criteri. Il team IT può automatizzare la maggior parte dei trigger per le violazioni usando gli strumenti descritti nella [toolchain di coerenza delle risorse per Azure](toolchain.md).

I trigger e le azioni di imposizione seguenti sono esempi a cui è possibile fare riferimento quando si pianifica l'uso dei dati di monitoraggio per risolvere le violazioni dei criteri:

- Rilevamento di risorse con provisioning eccessivo: le risorse rilevate che risultano usare meno del 60% della capacità della CPU o di memoria devono essere ridotte automaticamente o è necessario effettuare il deprovisioning delle risorse per ridurre i costi.
- Rilevamento di risorse con provisioning insufficiente: le risorse rilevate che risultano usare più dell'80% della capacità della CPU o di memoria devono essere aumentate automaticamente o è necessario effettuare il provisioning di risorse aggiuntive per ottenere capacità aggiuntiva.
- Creazione di risorse senza tag: qualsiasi richiesta di creare una risorsa senza i tag META richiesti verrà rifiutata automaticamente.
- Rilevamento di interruzioni di risorse critiche: il personale IT riceve notifica per tutte le interruzioni rilevate per le risorse cruciali. Se l'interruzione non può essere risolta immediatamente, il personale inoltrerà il problema e ne informerà i proprietari del carico di lavoro e il team di governance del cloud. Il team di governance del cloud tiene traccia del problema fino alla risoluzione e aggiorna le linee guida nel caso risulti necessaria una revisione dei criteri per prevenire futuri eventi imprevisti.

## <a name="next-steps"></a>Passaggi successivi

Usare il [modello di gestione del cloud](./template.md) per documentare i processi e i trigger in linea con il piano di adozione del cloud attuale.

Per istruzioni sull'esecuzione dei criteri di gestione del cloud in linea con i piani di adozione, vedere l'articolo sul miglioramento della disciplina.

> [!div class="nextstepaction"]
> [Miglioramento della disciplina Coerenza delle risorse](./discipline-improvement.md)
