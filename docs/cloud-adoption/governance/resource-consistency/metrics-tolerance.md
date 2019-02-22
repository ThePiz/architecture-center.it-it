---
title: 'CAF: Metriche di Coerenza delle risorse, indicatori e tolleranza al rischio'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Metriche di Coerenza delle risorse, indicatori e tolleranza al rischio
author: BrianBlanchard
ms.openlocfilehash: 3a2561a6d1d81a6395bb256e921a7a26898b45a0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901899"
---
# <a name="resource-consistency-metrics-indicators-and-risk-tolerance"></a>Metriche di Coerenza delle risorse, indicatori e tolleranza al rischio

Questo articolo è concepito per aiutare l'utente a quantificare la tolleranza ai rischi aziendali in relazione alla Coerenza delle risorse. La definizione di metriche e indicatori consente di creare un business case per effettuare un investimento riguardo alla disciplina Coerenza delle risorse.

## <a name="metrics"></a>Metriche

La disciplina Coerenza delle risorse è incentrata sulla capacità di affrontare i rischi correlati alla gestione operativa delle distribuzioni cloud. Nell'ambito dell'analisi dei rischi è necessario raccogliere i dati relativi alle operazioni IT per determinare l'entità dei rischi e l'importanza degli investimenti nella Coerenza delle risorse per le distribuzioni cloud pianificate.

Ogni organizzazione dispone di diversi scenari operativi, ma gli elementi seguenti rappresentano esempi utili delle metriche da raccogliere durante la valutazione della tolleranza al rischio riguardo alla disciplina Coerenza delle risorse:

- **Cloud assets**. Numero totale delle risorse distribuite sul cloud.
- **Risorse senza tag**. Numero di risorse prive di accounting obbligatorio, impatto aziendale o tag dell'organizzazione.
- **Asset sottoutilizzati**. Numero di risorse in cui memoria, CPU o funzionalità di rete sono tutte sottoutilizzate in modo coerente.
- **Esaurimento delle risorse**. Numero di risorse in cui memoria, CPU o funzionalità di rete sono state esaurite dal carico.
- **Età della risorsa**. Tempo trascorso dall'ultima implementazione o modifica della risorsa.
- **Disponibilità del servizio**. Percentuale del tempo effettivo dei carichi di lavoro ospitati nel cloud rispetto al tempo di attività previsto.
- **Macchine virtuali in condizioni critiche**. Numero di macchine virtuali distribuite dove sono stati rilevati uno o più problemi critici che devono essere risolti per ripristinarne il normale funzionamento.
- **Avvisi in base alla gravità**. Numero totale di avvisi su un asset distribuito, suddivisi per livello di gravità.
- **Esempio di collegamenti subnet non integri**. Numero di risorse con problemi di connettività di rete.
- **Endpoint di servizio non integri**. Numero di problemi con gli endpoint di rete esterni.
- **Imprevisti di integrità del provider dei servizi cloud**. Numero di interruzioni o problemi di prestazione causati dal provider cloud.
- **Backup dello stato**. Numero di backup attivamente in fase di sincronizzazione.
- **Integrità di ripristino**. Numero di operazioni di ripristino eseguite correttamente.

## <a name="risk-tolerance-indicators"></a>Indicatori sulla tolleranza al rischio

Le piattaforme cloud offrono un set di funzionalità baseline che consentono la distribuzione di team per gestire efficacemente le distribuzioni di piccole dimensioni senza una pianificazione o processi approfonditi aggiuntivi. Di conseguenza, i primi carichi di lavoro sperimentali o di Sviluppo/test che includono asset basati sul cloud rappresentano un livello di rischio basso e probabilmente non necessitano di molto in termini di criteri formali di Coerenza delle risorse.

Tuttavia, man mano che aumentano le dimensioni del cloud diventa molto più difficile la gestione delle complessità degli asset. Con altri asset nel cloud, la capacità di identificare il proprietario delle risorse e il controllo delle risorse diventa fondamentale per ridurre al minimo i rischi. Man mano che aumentano i carichi di lavoro cruciali distribuiti nel cloud, il tempo di attività di servizio diventa più importante e la tolleranza per le interruzioni del servizio può essere ridotta rapidamente.

Nelle prime fasi di adozione del cloud è necessario lavorare con il team addetto alla sicurezza IT e con gli azionisti aziendali per identificare i [rischi commerciali](business-risks.md) correlati alla Coerenza delle risorse, quindi definire una baseline accettabile per la tolleranza ai rischi di sicurezza. In questa sezione del CAF vengono offerti alcuni esempi, ma i rischi dettagliati e le baseline per la società o le distribuzioni potrebbe essere diverse.

Dopo aver creato una baseline, è necessario stabilire i benchmark minimi che rappresentano un aumento inaccettabile dei rischi identificati. Questi benchmark fungono da trigger nel momento in cui è necessario intervenire per limitare tali rischi. Di seguito sono riportati alcuni esempi del modo in cui le metriche operative, ad esempio quelle descritte in precedenza, possono giustificare un maggiore investimento nella disciplina Coerenza delle risorse.

- **L'assegnazione di tag e la denominazione del trigger**. Un'azienda con più di X risorse priva delle informazioni necessarie per l'assegnazione di tag o che non obbedisce agli standard di denominazione, deve valutare la possibilità di investire nella disciplina Coerenza delle risorse per ottimizzare tali standard e garantirne l'applicazione coerente agli asset distribuiti sul cloud.
- **Trigger di risorse con provisioning eccessivo**. Se un'azienda ha più dell'X% degli asset che regolarmente usa piccole quantità di memoria disponibile, la CPU o le funzionalità di rete, dovrebbe investire nella disciplina Coerenza delle risorse per ottimizzare l'utilizzo delle risorse per tali elementi.
- **Trigger di risorse con provisioning insufficiente**. Se un'azienda ha più dell'X% degli asset che esaurisce regolarmente la maggior parte della memoria disponibile, la CPU o le funzionalità di rete, dovrebbe investire nella disciplina Coerenza delle risorse per garantire che tali asset dispongano delle risorse necessarie per prevenire le interruzioni del servizio.
- **Trigger di età delle risorse**. Un'azienda con più di X risorse che non sono state aggiornate in oltre X mesi potrebbe trarre vantaggio dall'investimento nella disciplina Coerenza delle risorse che mira a garantire che le risorse attive siano integre e con patch, mentre gli asset inutilizzati o obsoleti vengono ritirati.  
- **Trigger di disponibilità del servizio**. Una società che ha riscontrato un tempo di attività per i servizi cruciali al di sotto dell'X% dovrebbe investire nella disciplina Coerenza delle risorse per migliorare l'affidabilità del servizio.
- **Trigger di integrità della macchina virtuale**. Una società in cui oltre l'X% delle macchine virtuali riscontra problemi di integrità cruciali dovrebbe investire nella disciplina Coerenza delle risorse per identificare i problemi e migliorare la stabilità della macchina virtuale.
- **Trigger di integrità della rete**. Una società in cui oltre l'%X delle subnet di rete o degli endpoint riscontra problemi di connettività dovrebbe investire nella disciplina Coerenza delle risorse per identificare e risolvere i problemi di rete.
- **Trigger di copertura per il backup**. Un'azienda con l'X% di asset cruciali senza backup aggiornato può trarre vantaggio da un aumento degli investimenti nella disciplina Coerenza delle risorse per garantire una strategia di backup coerente.
- **Trigger di integrità del backup**. Una società che riscontra più dell'X% di errori nelle operazioni di ripristino dovrebbe investire nella disciplina Coerenza delle risorse per identificare i problemi con il backup e garantire che le risorse importanti siano protette.

Le metriche e i trigger esatti da usare per misurare la tolleranza ai rischi e il grado di investimento nella disciplina Coerenza delle risorse saranno specifici per ogni organizzazione, ma gli esempi precedenti possono fungere da base utile per una discussione all'interno del team addetto alla governance del cloud.  

## <a name="next-steps"></a>Passaggi successivi

Usare il [Cloud Management template](./template.md) (modello Gestione cloud), documentare le metriche e gli indicatori di tolleranza ai rischi che si allineano al piano di adozione del cloud attuale.

Sulla base dei rischi e della tolleranza, stabilire un processo per controllare e comunicare la conformità ai criteri di Coerenza delle risorse.

> [!div class="nextstepaction"]
> [Establish policy compliance processes](compliance-processes.md) (Stabilire i processi di conformità ai criteri)
