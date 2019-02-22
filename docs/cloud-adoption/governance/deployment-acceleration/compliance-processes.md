---
title: "CAF: Processi di conformità ai criteri per l'accelerazione della distribuzione"
description: Processi di conformità ai criteri per l'accelerazione della distribuzione
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
author: alexbuckgit
ms.openlocfilehash: 08046a4ae199a8714393d2aba3841dd84008d836
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901542"
---
# <a name="deployment-acceleration-policy-compliance-processes"></a>Processi di conformità ai criteri per l'accelerazione della distribuzione

Questo articolo illustra un approccio ai processi di conformità ai criteri che regolano l'[accelerazione della distribuzione](./overview.md). Una governance efficace della configurazione del cloud inizia con processi manuali ricorrenti progettati per rilevare eventuali problemi e imporre criteri per mitigare i rischi correlati. È tuttavia possibile automatizzare questi processi e integrarli con strumenti per ridurre il carico della governance e ottenere risposte più rapide in caso di deviazioni.

## <a name="planning-review-and-reporting-processes"></a>Processi di pianificazione, verifica e creazione di report

L'efficacia degli strumenti per l'accelerazione della distribuzione nel cloud dipende dai processi e dai criteri che supportano. Di seguito è riportato un set di processi di esempio comunemente usati per la disciplina Baseline di sicurezza. Usare questi esempi come punto di partenza per la pianificazione di processi che consentono di continuare ad aggiornare i criteri di sicurezza in base alle modifiche dell'azienda e al feedback da parte dei team responsabili della sicurezza e dell'IT che sono incaricati di concretizzare le indicazioni sulla governance.

**Valutazione iniziale dei rischi e pianificazione**: come parte dell'adozione iniziale della disciplina Accelerazione della distribuzione, identificare i principali rischi aziendali e le tolleranze correlate alla distribuzione delle applicazioni aziendali. Usare queste informazioni per discutere dei rischi tecnici specifici con i membri dei team responsabili delle operazioni IT e della sicurezza, in modo da poter sviluppare un set di base di criteri di distribuzione e configurazione per l'attenuazione di tali rischi e stabilire così la strategia di governance iniziale.

**Pianificazione della distribuzione**: prima della distribuzione di qualsiasi asset, eseguire una verifica della sicurezza per identificare eventuali nuovi rischi e per assicurarsi che siano soddisfatti tutti i requisiti dei criteri di accesso e sicurezza dei dati.

**Test della distribuzione**: come parte del processo di distribuzione per qualsiasi asset, il team di governance del cloud, in collaborazione con i team responsabili della sicurezza aziendale, è incaricato della verifica della distribuzione per convalidare la conformità ai criteri di sicurezza.

**Pianificazione annuale**: su base annuale, eseguire una verifica generale della strategia di accelerazione della distribuzione. Esplorare le priorità future dell'azienda e le strategie di adozione del cloud aggiornate per identificare un potenziale aumento dei rischi e altre esigenze e opportunità emergenti per la configurazione. In questa fase, esaminare anche le procedure consigliate più aggiornate per la distribuzione dell'accelerazione e integrarle all'interno dei criteri e dei processi di verifica dell'azienda.

**Pianificazione e verifica trimestrale**: eseguire con cadenza trimestrale una revisione dei dati di controllo della sicurezza e dei report degli incidenti per identificare eventuali modifiche da apportare ai criteri per l'accelerazione della distribuzione. Come parte di questo processo, esaminare l'attuale panorama della sicurezza informatica per prevedere in modo proattivo le minacce emergenti e aggiornare i criteri in maniera appropriata. Al termine della revisione, allineare ai criteri aggiornati le linee guida per la progettazione di applicazioni e sistemi.

Questo processo di pianificazione offre anche l'opportunità di valutare se esistono lacune tra i membri attuali del team di governance del cloud in merito a criteri e rischi nuovi o in evoluzione correlati a DevOps e all'accelerazione della distribuzione. Invitare il personale IT pertinente a partecipare alle attività di verifica e pianificazione come consulenti tecnici temporanei o come membri permanenti del team.

**Didattica e training**: offrire sessioni di training su base bimestrale per assicurarsi che gli sviluppatori e il personale IT siano sempre aggiornati sui requisiti e sulle strategie più recenti dell'accelerazione della distribuzione. Come parte di questo processo, rivedere e aggiornare qualsiasi documentazione, le linee guida o altre risorse di training per assicurarsi che siano in linea con le definizioni dei criteri aziendali più recenti.

**Controllo mensile e creazione di report sulle revisioni**: eseguire un controllo mensile su tutte le distribuzioni cloud per garantire il costante allineamento con i criteri di configurazione. Esaminare le attività correlate alla sicurezza con il personale IT e identificare eventuali problemi di conformità non ancora gestiti come parte del processo continuo di monitoraggio e imposizione. Il risultato di questa revisione è un report destinato al team per la strategia cloud e a ogni team di adozione del cloud per comunicare l'aderenza complessiva ai criteri. Il report viene inoltre archiviato a scopo legale e di controllo.

## <a name="ongoing-monitoring-processes"></a>Processi di monitoraggio continuo

La determinazione del buon esito della strategia di governance dell'accelerazione della distribuzione dipende dalla visibilità dello stato corrente e passato dell'infrastruttura cloud. Senza la possibilità di analizzare le metriche e i dati pertinenti sull'integrità e sulle attività correlate alla sicurezza delle risorse cloud, non è possibile identificare cambiamenti nei rischi o rilevare violazioni delle tolleranze ai rischi. I processi continui di governance illustrati in precedenza richiedono dati di qualità per garantire che i criteri possano essere modificati in modo da proteggere l'infrastruttura da mutevoli minacce e rischi provenienti da risorse non configurate correttamente.

Verificare che i team responsabili della sicurezza e dell'IT abbiano implementato sistemi di monitoraggio automatizzati per l'infrastruttura cloud che consentono di acquisire i dati di log pertinenti per la valutazione dei rischi. Essere proattivi nel monitoraggio di questi sistemi garantisce la rapida individuazione e mitigazione di potenziali violazioni di criteri e assicura che la strategia di monitoraggio sia in linea con le esigenze di distribuzione e configurazione.

## <a name="violation-triggers-and-enforcement-actions"></a>Trigger per le violazioni e azioni di imposizione

Poiché la mancata conformità ai criteri di configurazione può comportare rischi critici di interruzione del servizio, il team di governance del cloud dovrebbe avere visibilità riguardo a gravi violazioni dei criteri. Verificare che il personale IT possa fare riferimento a percorsi di escalation chiari per segnalare i problemi di conformità ai criteri di configurazione ai membri del team di governance più adatti a identificare tali problemi e controllare che vengano mitigati.  

Quando vengono rilevate violazioni, si devono intraprendere azioni per riallinearsi il prima possibile ai criteri. Il team IT può automatizzare la maggior parte dei trigger per le violazioni usando gli strumenti descritti nella [toolchain dell'accelerazione della distribuzione per Azure](toolchain.md).

I trigger e le azioni di imposizione seguenti sono esempi a cui è possibile fare riferimento quando si parla dell'uso dei dati di monitoraggio per risolvere le violazioni dei criteri:

- **Modifiche impreviste nella configurazione rilevata**. Se la configurazione di una risorsa cambia in modo imprevisto, collaborare con il personale IP e con i proprietari del carico di lavoro per identificare la causa principale e sviluppare un piano correttivo.
- **Configurazione di nuove risorse non conforme ai criteri**. Collaborare con i team di DevOps e i proprietari del carico di lavoro per rivedere i criteri di accelerazione della distribuzione durante l'avvio del progetto in modo che le persone coinvolte comprendano i requisiti dei criteri pertinenti.
- **Ritardi nelle pianificazioni di progetto a causa di errori di distribuzione o problemi di configurazione**. Collaborare con i team di sviluppo e i proprietari del carico di lavoro per assicurarsi che comprendano come automatizzare la distribuzione di risorse basate sul cloud per coerenza e ripetibilità. Le distribuzioni completamente automatizzate devono essere configurate nelle prime fasi del ciclo di sviluppo. Se si rimandano a una fase successiva, si verificano in genere problemi e ritardi imprevisti.

## <a name="next-steps"></a>Passaggi successivi

Usare il [modello di gestione del cloud](./template.md) per documentare i processi e i trigger in linea con il piano di adozione del cloud attuale.

Per istruzioni sull'esecuzione dei criteri di gestione del cloud in linea con i piani di adozione, vedere l'articolo sul miglioramento della disciplina.

> [!div class="nextstepaction"]
> [Miglioramento della disciplina Accelerazione della distribuzione](./discipline-improvement.md)
