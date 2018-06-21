---
title: Compilazione per le esigenze aziendali
description: Ogni decisione di progettazione deve essere giustificata da un requisito aziendale
author: MikeWasson
ms.openlocfilehash: 768f2298860d91774d93c1917cf95000bb2b873d
ms.sourcegitcommit: 26b04f138a860979aea5d253ba7fecffc654841e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36206639"
---
# <a name="build-for-the-needs-of-the-business"></a>Compilazione per le esigenze aziendali

## <a name="every-design-decision-must-be-justified-by-a-business-requirement"></a>Ogni decisione di progettazione deve essere giustificata da un requisito aziendale

Questo principio di progettazione può sembrare ovvio, ma è essenziale da tenere a mente quando si progetta una soluzione. Si prevedono milioni di utenti o solo alcune migliaia? Un'interruzione dell'applicazione di un'ora è accettabile? Si prevedono forti picchi di traffico o un carico di lavoro molto prevedibile? In ultima analisi, ogni decisione di progettazione deve essere giustificata da un requisito aziendale. 

## <a name="recommendations"></a>Raccomandazioni

**Definire gli obiettivi di business**, inclusi obiettivo del tempo di ripristino (RTO), obiettivo del punto di ripristino (RPO) e massima interruzione tollerabile (MTO). Questi dati devono guidare le decisioni sull'architettura. Per ottenere un RTO basso, ad esempio, è possibile implementare il failover automatico a un'area secondaria. Tuttavia, se la soluzione può tollerare un RTO superiore, questo livello di ridondanza potrebbe non essere necessario.

**Documentare i contratti di servizio (SLA) e gli obiettivi del livello di servizio (SLO)**, incluse le metriche di disponibilità e prestazioni. Si potrebbe compilare una soluzione che offre una disponibilità pari al 99,95%. È sufficiente? La risposta è una decisione aziendale. 

**Modellare l'applicazione attorno al dominio aziendale**. Iniziare dall'analisi dei requisiti aziendali. Usare questi requisiti per modellare l'applicazione. Considerare l'uso di tecniche di progettazione basate su domini per creare [modelli di dominio][domain-model] che riflettono i processi e i casi d'uso aziendali. 

**Acquisire i requisiti sia funzionali che non funzionali**. I requisiti funzionali consentono di valutare se l'applicazione esegue le operazioni giuste. I requisiti non funzionali consentono di valutare se l'applicazione esegue tali operazioni in modo *corretto*. In particolare, assicurarsi di aver compreso i propri requisiti di scalabilità, disponibilità e latenza. Questi requisiti incideranno sulle decisioni di progettazione e sulla scelta delle tecnologie.

**Scomporre in base al carico di lavoro**. Il termine "carico di lavoro" in questo contesto indica una capacità o attività di elaborazione discreta, che può essere logicamente separata da altre attività. Carichi di lavoro diversi possono avere requisiti diversi in termini di disponibilità, scalabilità, coerenza dei dati e ripristino di emergenza. 

**Pianificare la crescita**. Una soluzione può soddisfare le esigenze correnti in termini di numero di utenti, volume delle transazioni, archiviazione dei dati e così via. Un'applicazione solida, tuttavia, può gestire la crescita senza bisogno di apportare modifiche sostanziali all'architettura. Vedere [Progettazione per la scalabilità orizzontale](scale-out.md) e [Creare partizioni per ovviare i limiti](partition.md). Inoltre, considerare il fatto che il modello e i requisiti aziendali probabilmente cambieranno nel tempo. Se il modello di servizio e i modelli di dati di un'applicazione sono troppo rigidi, diventa difficile far evolvere l'applicazione per i nuovi scenari e casi d'uso. Vedere [Progettare in un'ottica evolutiva](design-for-evolution.md).

**Gestire i costi**. In un'applicazione locale tradizionale, si paga per l'hardware in modo anticipato (CAPEX). In un'applicazione cloud, si paga per le risorse utilizzate. Assicurarsi di aver compreso il modello di determinazione dei prezzi per i servizi che si utilizzano. Il costo totale includerà utilizzo della larghezza di banda di rete, archiviazione, indirizzi IP, utilizzo dei servizi e altri fattori. Per altre informazioni, vedere la pagina relativa ai [prezzi di Azure][pricing]. Considerare inoltre i costi delle operazioni. Nel cloud non è necessario gestire l'hardware o altre infrastrutture, ma è comunque necessario gestire le applicazioni, ad esempio DevOps, risposta agli eventi imprevisti, ripristino di emergenza e così via. 

[domain-model]: https://martinfowler.com/eaaCatalog/domainModel.html
[pricing]: https://azure.microsoft.com/pricing/
