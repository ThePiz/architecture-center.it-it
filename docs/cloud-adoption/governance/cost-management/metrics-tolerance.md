---
title: 'CAF: Metriche sulla Gestione costi, indicatori e tolleranza ai rischi'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Spiegazione sulla Gestione costi in relazione alla governance cloud
author: BrianBlanchard
ms.openlocfilehash: 76e6b1b32dd862322f6cafd9aa63c6c4f79f4f5d
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241732"
---
# <a name="cost-management-metrics-indicators-and-risk-tolerance"></a>Metriche della Gestione costi, indicatori e tolleranza ai rischi

Questo articolo è concepito per aiutare l'utente a quantificare la tolleranza ai rischi aziendali, in relazione alla Gestione costi. La definizione di metriche e indicatori consente di creare un business case per effettuare un investimento riguardo alla disciplina Gestione costi.

## <a name="metrics"></a>Metriche

Il servizio Gestione costi generalmente è incentrato sulle metriche relative ai costi. Come parte dell'analisi dei rischi, è necessario raccogliere dati relativi alle proprie spese correnti e pianificate sui carichi di lavoro basati sul cloud per determinare il livello di rischio da affrontare e quanto sia importante investire nella governance dei costi come strategia di adozione del cloud.

Sono riportati alcuni esempi di metriche utili che è necessario raccogliere per valutare la tolleranza ai rischi all'interno della disciplina Baseline di sicurezza:

- Spesa annuale: costo totale annuale dei servizi forniti da un provider di servizi cloud
- Spesa mensile: costo totale mensile dei servizi forniti da un provider di servizi cloud
- Percentuale prevista rispetto a quella effettiva: il rapporto è dato dal confronto tra la spesa prevista e quella effettiva (mensile o annuale)
- Evoluzione della percentuale di adozione (MOM): percentuale dei delta nei costi del cloud su base mensile
- Costo accumulato: spese giornaliere accumulate totali, a partire dall'inizio del mese
- Tendenze di spesa: tendenza di spesa rispetto al budget

## <a name="risk-tolerance-indicators"></a>Indicatori sulla tolleranza ai rischi

Durante le distribuzioni iniziali, ad esempio Sviluppo/test o primi carichi di lavoro sperimentali, Gestione costi probabilmente comporta un rischio relativamente ridotto. Dato che vengono distribuite più risorse, il rischio aumenta e la tolleranza delle aziende ai rischi probabilmente diminuisce. Inoltre, visto che altri team che adottano il cloud hanno la possibilità di configurare o distribuire le risorse nel cloud, il rischio aumenta e la tolleranza diminuisce. Al contrario, l'aumento della disciplina della Gestione costi porterà gli utenti dalla fase di adozione del cloud alla distribuzione di nuove tecnologie più innovative.

Nelle prime fasi di adozione del cloud, si dovrà stabilire una baseline di tolleranza ai rischi insieme alla propria azienda. Dopo aver creato una baseline, è necessario determinare i criteri che attiverebbero un investimento nella disciplina della Gestione costi. Questi criteri saranno probabilmente diversi per ogni organizzazione.

Dopo aver identificato [i rischi aziendali](./business-risks.md), si useranno con la propria azienda per identificare i benchmark che è possibile usare per identificare i trigger che potrebbero potenzialmente incrementare tali rischi. Sono indicati alcuni esempi del modo in cui le metriche, ad esempio quelle precedentemente indicate, possono essere confrontate con la tolleranza ai rischi della baseline per indicare la necessità dell'azienda di investire ancora di più in Gestione costi.

- Basato sull'impegno (il più comune): Un'azienda che quest'anno s'impegna a spendere $ X.000.000 per un fornitore di cloud. È necessaria una disciplina di Gestione costi per assicurarsi che l'azienda non superi i relativi obiettivi di spesa di più del 20% e che venga usato almeno il 90% di tale impegno.
- Trigger di percentuale: Un'azienda con una spesa per il cloud che è stabile per i sistemi di produzione. In caso di modifiche di più del X%, una disciplina di Gestione costi sarà un saggio investimento.
- Trigger con provisioning eccessivo: Una società che ritiene che le soluzioni distribuite abbiano un provisioning eccessivo. Gestione costi è un investimento prioritario fino a quando non è possibile illustrare l'allineamento corretto dell'uso di provisioning e risorse.
- Trigger della spesa mensile: Un'azienda che spende più di $ X.000 al mese, in cui tale importo viene considerato un costo considerevole. Se la spesa supera tale valore in un determinato mese, sarà necessario investire in Gestione costi.
- Trigger della spesa annuale: Un'azienda con un budget IT destinato a ricerca e sviluppo che consente una spesa annuale di $ X.000 nella sperimentazione di cloud. Possono eseguire carichi di lavoro di produzione nel cloud, ma vengono comunque considerati soluzioni sperimentali se il budget non supera tale importo. In caso di superamento, è necessario trattare il budget come un investimento di produzione e gestire la spesa minuziosamente.
- Spesa operativa negativa (non comune): Un'azienda è molto contraria alle spesa operativa e deve controllare Gestione costi in vigore, prima della distribuzione di un flusso di lavoro Sviluppo/test.

## <a name="next-steps"></a>Passaggi successivi

Usare il [Cloud Management template (modello Gestione cloud)](./template.md), documentare le metriche e gli indicatori di tolleranza ai rischi che si allineano al piano di adozione del cloud attuale.

Sulla base dei rischi e della tolleranza, stabilire un processo per controllare e comunicare la conformità ai criteri di Gestione costi.

> [!div class="nextstepaction"]
> [Establish policy compliance processes (Stabilire i processi di conformità ai criteri)](compliance-processes.md)
