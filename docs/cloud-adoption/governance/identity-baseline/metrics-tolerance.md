---
title: 'CAF: Baseline di identità: metriche, indicatori e tolleranza ai rischi'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 'Baseline di identità: metriche, indicatori e tolleranza ai rischi'
author: BrianBlanchard
ms.openlocfilehash: 4722de66308f3d18885ca930925e68e0e756ec03
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902067"
---
# <a name="identity-baseline-metrics-indicators-and-risk-tolerance"></a>Baseline di identità: metriche, indicatori e tolleranza ai rischi

Questo articolo è concepito per aiutare l'utente a quantificare la tolleranza ai rischi aziendale in relazione alla baseline di identità. La definizione di metriche e indicatori consente di creare un business case per effettuare un investimento riguardo alla disciplina Baseline di identità.

## <a name="metrics"></a>Metriche

La disciplina Baseline di identità è incentrata sull'identificazione, autenticazione e autorizzazione di individui, gruppi di utenti o processi automatizzati e sul fornire a queste entità l'accesso appropriato alle risorse nelle distribuzioni cloud. Nell'ambito dell'analisi dei rischi è necessario raccogliere dati relativi ai servizi gestione delle identità per determinare l'entità dei rischi e l'importanza degli investimenti nella governance della baseline di identità per le distribuzioni cloud pianificate.

Di seguito sono riportati esempi di metriche utili che è necessario raccogliere per valutare la tolleranza ai rischi all'interno della disciplina Baseline di identità:

- **Dimensioni dei sistemi di gestione delle identità**. Numero totale di utenti, gruppi o altri oggetti gestiti tramite i sistemi di gestione delle identità.
- **Dimensioni complessive dell'infrastruttura dei servizi di directory**. Numero di foreste di directory, domini e tenant usati dall'organizzazione.
- **Dipendenza da meccanismi di autenticazione legacy o locale**. Numero di carichi di lavoro dipendenti da servizi di autenticazione a più fattori (MFA) di terze parti o meccanismi di autenticazione legacy.
- **Portata dei servizi directory distribuiti nel cloud**. Numero di foreste di directory, domini e tenant distribuiti nel cloud.
- **Istanze di Active Directory distribuite nel cloud**. Numero di server Active Directory distribuiti nel cloud.
- **Unità organizzative distribuite nel cloud**. Numero di unità organizzative di Active Directory distribuite nel cloud.
- **Portata della federazione**. Numero di sistemi per la baseline di identità federati con i sistemi dell'organizzazione.  
- **Utenti con privilegi elevati**. Numero di account utente che dispongono di accesso con privilegi elevati a risorse o strumenti di gestione.
- **Uso del controllo degli accessi in base al ruolo**. Numero di sottoscrizioni, gruppi di risorse o singole risorse non gestite tramite il controllo degli accessi in base al ruolo.
- **Attestazioni di autenticazione**. Numero di tentativi di autenticazione utente riusciti e non riusciti.
- **Attestazioni di autorizzazione**. Numero di tentativi riusciti e falliti di accedere alle risorse da parte degli utenti.
- **Account compromessi**. Numero di account utente che sono stati compromessi.

## <a name="risk-tolerance-indicators"></a>Indicatori sulla tolleranza ai rischi

I rischi correlati alla baseline di identità sono in gran parte legati alla complessità dell'infrastruttura di gestione delle identità dell'organizzazione. Se tutti gli utenti e i gruppi vengono gestiti usando una sola directory o un solo provider di identità cloud nativo, con integrazioni minime con altri servizi, il livello di rischio sarà probabilmente basso. Tuttavia, man mano che l'azienda cresce, i sistemi per la baseline di identità potrebbero dover supportare scenari più complessi, ad esempio molteplici directory per supportare l'organizzazione interna o federazione con provider di identità esterni. Man mano che i sistemi diventano più complessi, il rischio aumenta.

Nelle prime fasi di adozione del cloud è necessario lavorare con il team addetto alla sicurezza IT e con gli stakeholder aziendali per identificare i [rischi aziendali](business-risks.md) correlati alle identità, quindi definire una baseline accettabile per la tolleranza ai rischi per le identità. In questa sezione della guida CAF vengono offerti alcuni esempi, ma i rischi dettagliati e le baseline per la società o le distribuzioni potrebbero essere diversi.

Dopo aver creato una baseline, è necessario stabilire i benchmark minimi che rappresentano un aumento inaccettabile dei rischi identificati. Questi benchmark svolgono la funzione di trigger da attivare nel momento in cui è necessario intervenire per mitigare questi rischi. Di seguito sono riportati alcuni esempi del modo in cui le metriche correlate alle identità, ad esempio quelle descritte in precedenza, possono giustificare un maggiore investimento nella disciplina Baseline di identità.

- **Trigger per numero di account utente**. Un'azienda con un numero superiore a X di utenti, gruppi o altri oggetti gestiti dai sistemi di gestione delle identità può trarre vantaggio da investimenti nella disciplina Baseline di identità per assicurare una governance efficiente su un numero elevato di account.
- **Trigger per le dipendenze da servizi di gestione delle identità locali**. Una società che sta pianificando la migrazione nel cloud di carichi di lavoro che richiedono funzionalità di autenticazione legacy o MFA di terze parti dovrebbe investire nella disciplina Baseline di identità per ridurre i rischi correlati al refactoring o alla distribuzione di infrastrutture cloud aggiuntive.
- **Trigger per la complessità dei servizi directory**. Una società che gestisce un numero superiore a X di singoli domini, foreste o tenant di directory, dovrebbe investire nella disciplina Baseline di identità per ridurre i rischi correlati alla gestione degli account e i problemi di efficienza associati alla distribuzione delle credenziali utente in più sistemi.
- **Trigger per servizi directory ospitati nel cloud**. Una società che ha un numero superiore a X di macchine virtuali server Active Directory ospitate nel cloud o che ha un numero X di unità organizzative gestite in tali server basati su cloud può trarre vantaggio da investimenti nella disciplina Baseline di identità per ottimizzare l'integrazione con qualsiasi servizio di gestione delle identità locale o esterno.
- **Trigger per federazione**. Una società che implementa la federazione delle identità con un numero X di sistemi per la baseline di identità esterni può trarre vantaggio da investimenti nella disciplina Baseline di identità per garantire criteri aziendali coerenti tra i membri della federazione.
- **Trigger per accessi con privilegi elevati**. Una società con una percentuale superiore a X% di utenti con autorizzazioni elevate alla gestione di risorse e strumenti dovrebbe valutare la possibilità di investire nella disciplina Baseline di identità per ridurre al minimo il rischio di overprovisioning accidentale dell'accesso agli utenti.
- **Trigger per il controllo degli accessi in base al ruolo**. Una società in cui una percentuale inferiore a X% delle risorse usa metodi di controllo degli accessi in base al ruolo dovrebbe valutare la possibilità di investire nella disciplina Baseline di identità per identificare modalità ottimizzate per assegnare l'accesso alle risorse.
- **Trigger per errori di autenticazione**. Una società in cui gli errori di autenticazione rappresentano una percentuale superiore a X% dei tentativi dovrebbe investire nella disciplina Baseline di identità per assicurarsi che i metodi di autenticazione non siano soggetti ad attacchi esterni e che gli utenti siano in grado di usare correttamente i metodi di autenticazione.
- **Trigger per errori di autorizzazione**. Una società in cui i tentativi di accesso vengono rifiutati per una percentuale superiore a X% dovrebbe investire nella disciplina Baseline di identità per migliorare l'applicazione, aggiornare i controlli di accesso e identificare gli accessi potenzialmente dannosi.
- **Trigger per account compromessi**. Una società con un numero superiore a X di account compromessi dovrebbe investire nella disciplina Baseline di identità per migliorare l'affidabilità e la sicurezza dei meccanismi di autenticazione e migliorare i meccanismi per mitigare i rischi correlati agli account compromessi.

Le metriche e i trigger esatti da usare per misurare la tolleranza ai rischi e il livello degli investimenti nella disciplina Baseline di identità saranno specifici per ogni organizzazione, ma gli esempi precedenti possono fungere da base utile per una discussione all'interno del team addetto alla governance del cloud.

## <a name="next-steps"></a>Passaggi successivi

Usare il [modello di gestione del cloud](./template.md) per documentare le metriche e gli indicatori di tolleranza in linea con il piano di adozione del cloud attuale.

Sulla base dei rischi e della tolleranza, stabilire un processo per controllare e comunicare la conformità ai criteri di Baseline di identità.

> [!div class="nextstepaction"]
> [Stabilire processi di conformità ai criteri](compliance-processes.md)
