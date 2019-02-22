---
title: 'CAF: Processi di conformità ai criteri della Baseline di sicurezza'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Processi di conformità ai criteri della Baseline di sicurezza
author: BrianBlanchard
ms.openlocfilehash: 8f115436d7021fe725118dcc0dfd64167c4cbfa1
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901988"
---
# <a name="security-baseline-policy-compliance-processes"></a>Processi di conformità ai criteri della Baseline di sicurezza

Questo articolo illustra un approccio ai processi conformità ai criteri che regolano la [Baseline di sicurezza](./overview.md). Una governance di protezione cloud efficace include processi manuali ricorrenti progettati per rilevare le vulnerabilità e imporre criteri per attenuare i rischi relativi alla sicurezza. A questo scopo è necessario coinvolgere regolarmente il team di governance del cloud con gli azionisti aziendali e IT, per rivedere e aggiornare i criteri e garantirne la conformità. Inoltre, molti processi di applicazione e di monitoraggio continuo possono essere automatizzati o eseguiti con strumenti per ridurre il sovraccarico di governance e consentire una risposta più rapida alla deviazione dai criteri.

## <a name="planning-review-and-reporting-processes"></a>Pianificazione, revisione e creazione di report per i processi

I migliori strumenti per la Baseline di sicurezza nel cloud, sono validi quanto i processi e i criteri che supportano. Il set seguente contiene processi di esempio comunemente coinvolti nella disciplina Baseline di sicurezza. Usare questi esempi come punto di partenza per la pianificazione di processi che consentono di continuare ad aggiornare i criteri di sicurezza in base alle modifiche dell'azienda e ai feedback da parte del team IT incaricato di concretizzare le indicazioni sulla governance.

**Valutazione iniziale dei rischi e pianificazione**: Come parte dell'adozione iniziale della disciplina Baseline di sicurezza, identificare i principali rischi aziendali e le tolleranze correlate alla sicurezza nel cloud. Usare queste informazioni per discutere di rischi tecnici specifici con i membri del reparto IT e del team per la sicurezza, in modo da poter sviluppare un set di base relativo ai criteri di sicurezza per attenuare tali rischi e per stabilire la strategia di governance iniziale.

**Pianificazione della distribuzione**: Prima della distribuzione di qualsiasi carico di lavoro o risorsa, eseguire una verifica della sicurezza per identificare eventuali nuovi rischi e per assicurarsi che siano soddisfatti tutti i requisiti dei criteri di sicurezza dei dati.

**Test di distribuzione**: Come parte del processo di distribuzione per qualsiasi carico di lavoro o risorsa, il team di governance del cloud, in collaborazione con i team di protezione aziendale, sarà responsabile della verifica della distribuzione per convalidare la conformità ai criteri di sicurezza.

**Pianificazione annuale**: Su base annuale, eseguire una verifica generale della Baseline di sicurezza. Esplorare le priorità aziendali future e le strategie di adozione cloud aggiornate per identificare potenziali aumenti dei rischi e altre esigenze di sicurezza emergenti. È consigliabile usare questo momento anche per esaminare le più recenti procedure consigliate riguardo la Baseline di sicurezza per integrarle all'interno dei propri criteri e processi di revisione.

**Pianificazione e revisione trimestrale**: Eseguire con cadenza trimestrale una revisione del controllo di sicurezza dei dati e dei report degli incidenti per identificare eventuali modifiche da apportare ai criteri di sicurezza. Come parte di questo processo, esaminare l'attuale panorama di sicurezza informatica per prevedere in modo proattivo le minacce emergenti e aggiornare i criteri in maniera appropriata. Dopo aver completato la revisione, allineare le indicazioni per la progettazione con i criteri aggiornati.

Durante questo processo di pianificazione è inoltre opportuno valutare se il team di governance del cloud è a conoscenza delle lacune relative a criteri nuovi o in evoluzione e ai rischi relativi alla sicurezza. Invitare il personale IT pertinente a partecipare a revisioni e pianificazione come consulenti tecnici temporanei o come membri permanenti del team.

**Didattica e training**: Offrire sessioni di training su base bimestrale per assicurarsi che gli sviluppatori e il personale IT siano sempre aggiornati sui requisiti più recenti dei criteri di sicurezza. Come parte di questo processo, rivedere e aggiornare qualsiasi documentazione, indicazione o altre risorse di training per assicurarsi che siano in linea con le istruzioni dei criteri aziendali più recenti.

**Controllo mensile e creazione di report sulle revisioni**: Su base mensile, eseguire un controllo su tutte le distribuzioni cloud per garantire il costante allineamento con i criteri di sicurezza. Esaminare le attività correlate alla sicurezza con il personale IT e identificare eventuali problemi di conformità non ancora gestiti come parte del processo continuo di monitoraggio e applicazione. Il risultato di questa revisione è un report destinato al team per la strategia cloud e a ogni team di adozione cloud per comunicare l'aderenza complessiva ai criteri. Il report viene inoltre archiviato a scopo legale e di controllo.

## <a name="ongoing-monitoring-processes"></a>Processi di monitoraggio continuo

La determinazione del buon esito della strategia di governance di sicurezza dipende dalla visibilità dello stato corrente e passato dell'infrastruttura cloud. Senza la possibilità di analizzare le metriche e i dati pertinenti sull'integrità della sicurezza e sulle attività delle risorse del cloud, non è possibile identificare cambiamenti nei rischi o rilevare violazioni delle tolleranze di rischio. I processi continui di governance illustrati in precedenza richiedono dati di qualità per garantire che i criteri possano essere modificati per proteggere meglio l'infrastruttura da mutevoli minacce e requisiti di sicurezza.

Verificare che i team IT e di sicurezza abbiano implementato sistemi di monitoraggio automatizzati per l'infrastruttura cloud che consentono di acquisire i dati di log rilevanti di cui si ha bisogno per valutare i rischi. Essere proattivi nel monitoraggio di questi sistemi garantisce la rapida individuazione e mitigazione di potenziali violazioni di criteri e assicura che la strategia di monitoraggio sia in linea con le esigenze di sicurezza.

## <a name="violation-triggers-and-enforcement-actions"></a>Violazione di trigger e azioni di applicazione

Poiché la mancata conformità alla sicurezza può comportare rischi critici di esposizione ai dati e di interruzione del servizio, il team di governance del cloud dovrebbe avere visibilità in caso di gravi violazioni dei criteri. Verificare che il personale IT disponga di percorsi di escalation per segnalare eventuali problemi di sicurezza ai membri del team governance più adatti a identificare e verificare che i problemi relativi ai criteri vengano attenuati.  

Quando vengono rilevate violazioni, si devono intraprendere azioni per un riallineamento con i criteri il prima possibile. Il team IT può automatizzare la maggior parte delle violazioni di trigger usando gli strumenti descritti in [Security Baseline toolchain for Azure](toolchain.md) (Toolchain di Baseline di sicurezza per Azure).

I trigger seguenti e le azioni di applicazione forniscono esempi a cui è possibile fare riferimento quando si pianifica l'uso dei dati di monitoraggio per risolvere le violazioni dei criteri:

- Rilevato un aumento negli attacchi: Se nelle risorse si verifica un aumento del 25% di attacchi DDoS o attacchi di forza bruta, discutere con il personale di sicurezza IT e con il proprietario del carico di lavoro per stabilire i rimedi. Tenere traccia del problema e aggiornare le indicazioni in caso fosse necessaria una revisione dei criteri per prevenire futuri incidenti.
- Rilevati dati non classificati: Qualsiasi origine dati senza una classificazione appropriata su privacy, sicurezza o impatto aziendale non avrà accesso finché non vengono applicati dal proprietario dei dati classificazione e livello appropriato di protezione dati.
- Rilevato problema di integrità della sicurezza: Disattivare l'accesso a tutte le macchine virtuali (VM) che hanno un accesso noto o vulnerabilità malware identificate, fino a quando non vengono installate patch appropriate o software di sicurezza. Aggiornare le indicazioni sui criteri per tenere conto di eventuali nuove minacce rilevate.
- Rilevata vulnerabilità di rete: L'accesso a qualsiasi risorsa non esplicitamente autorizzata dai criteri di accesso alla rete dovrebbe attivare un avviso per il personale di sicurezza IT e per il relativo proprietario del carico di lavoro. Tenere traccia del problema e aggiornare le indicazioni in caso sia necessaria una revisione dei criteri per attenuare futuri incidenti.

## <a name="next-steps"></a>Passaggi successivi

Usare il [modello di Gestione cloud](./template.md), documentare i processi e i trigger al rischio che si allineano al piano di adozione del cloud attuale.

Per istruzioni sull'esecuzione di criteri di gestione cloud in allineamento con i piani di adozione, consultare l'articolo sul miglioramento della disciplina.

> [!div class="nextstepaction"]
> [Security Baseline discipline improvement](./discipline-improvement.md) (Miglioramento della disciplina Baseline di sicurezza)
