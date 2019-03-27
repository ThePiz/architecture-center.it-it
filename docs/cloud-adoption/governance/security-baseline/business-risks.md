---
title: 'CAF: Motivazioni e rischi aziendali della Baseline di sicurezza'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Motivazioni e rischi aziendali della Baseline di sicurezza
author: BrianBlanchard
ms.openlocfilehash: 8407ed358e5862e466176096ee6a82ad792027cb
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58247312"
---
# <a name="security-baseline-motivations-and-business-risks"></a>Motivazioni e rischi aziendali della Baseline di sicurezza

Questo articolo illustra i motivi per cui i clienti in genere adottano la disciplina Baseline di sicurezza all'interno di una strategia di governance del cloud. Offre inoltre alcuni esempi di rischi aziendali potenziali che possono determinare le istruzioni dei criteri.

<!-- markdownlint-disable MD026 -->

## <a name="is-a-security-baseline-relevant"></a>È rilevante per la Baseline di sicurezza?

La sicurezza è un aspetto chiave per qualsiasi organizzazione IT. Le distribuzioni cloud affrontano molti degli stessi rischi per la sicurezza dei carichi di lavoro ospitati nei data center locali tradizionali. Tuttavia, la natura delle piattaforme cloud pubbliche che non sono direttamente proprietarie dell'hardware fisico che archivia ed esegue i carichi di lavoro, fa sì che che la sicurezza del cloud necessiti di criteri e processi propri.

Uno degli aspetti principali che separano la governance della sicurezza del cloud dai criteri di sicurezza tradizionali, è la facilità con cui si possono creare le risorse, cosa che potenzialmente aggiunge vulnerabilità, se non si è presa in considerazione la sicurezza prima della distribuzione. La flessibilità offerta da tecnologie come le [software defined networking (SDN)](../../decision-guides/software-defined-network/overview.md) (soluzioni di rete basate sul software), che permette di cambiare rapidamente la topologia di rete basata su cloud, può tuttavia anche modificare facilmente e in modi imprevisti la superficie complessiva dell'attacco di rete. Le piattaforme cloud offrono inoltre strumenti e funzionalità che consentono di migliorare le funzionalità di sicurezza in modi che non sono sempre disponibili in ambienti locali.

Quanto viene investito nei criteri di sicurezza e nei processi dipende molto dalla natura della distribuzione del cloud. Le distribuzioni dei test iniziali potrebbero richiedere solo i criteri di sicurezza di base, mentre un carico di lavoro cruciale comporterà la necessità di soddisfare esigenze di sicurezza complesse ed estese. Tutte le distribuzioni dovranno coinvolgere la disciplina, a un certo livello.

La disciplina Baseline di sicurezza illustra i criteri aziendali e i processi manuali che è possibile mettere in atto per proteggere la distribuzione del cloud contro i rischi per la sicurezza.

> [!NOTE]
>Sebbene sia importante capire la [Baseline di identità](../identity-baseline/overview.md) nel contesto della Baseline di sicurezza e come ciò si lega al Servizio di controllo di accesso, è bene sottolineare che le [cinque discipline della governance del cloud](../overview.md) considerano la [Baseline di identità](../identity-baseline/overview.md) come una disciplina a sé, che non fa parte della Baseline di sicurezza.

## <a name="business-risk"></a>Rischio aziendale

La disciplina Baseline di sicurezza tenta di affrontare ai principali rischi per la sicurezza aziendale. La disciplina lavora con l'azienda per identificare questi rischi e monitorarne la pertinenza durante la pianificazione e l'implementazione delle distribuzioni cloud.

I rischi variano da un'organizzazione all'altra, tuttavia i seguenti servono come rischi comuni relativi alla sicurezza che è possibile usare come punto di partenza per le discussioni all'interno del team di governance del cloud:

- **Violazione dei dati**. L'esposizione o la perdita accidentale di dati sensibili ospitati nel cloud può comportare la perdita di clienti, problemi contrattuali o ripercussioni legali.
- **Interruzioni del servizio**. Le interruzioni e altri problemi di prestazione dovuti a un'infrastruttura non protetta, interrompono le normali operazioni e possono comportare la perdita di produttività o di fatturato.

## <a name="next-steps"></a>Passaggi successivi

Usare il [modello di gestione del cloud](./template.md) per documentare i rischi aziendali che è probabile che vengano introdotti dal piano di adozione del cloud attuale.

Dopo aver stabilito in modo realistico i rischi aziendali, il passaggio successivo consiste nel documentare le metriche e gli indicatori principali della tolleranza ai rischi dell'azienda allo scopo di monitorare tale tolleranza.

> [!div class="nextstepaction"]
> [Indicatori, metriche e tolleranza ai rischi](./metrics-tolerance.md)
