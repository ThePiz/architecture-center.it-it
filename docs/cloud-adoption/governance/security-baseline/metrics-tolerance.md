---
title: 'CAF: Baseline di sicurezza: metriche, indicatori e tolleranza ai rischi'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 'Baseline di sicurezza: metriche, indicatori e tolleranza ai rischi'
author: BrianBlanchard
ms.openlocfilehash: 30deafca59b2e09c78432ad3b59d328fb27a1e2c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901820"
---
# <a name="security-baseline-metrics-indicators-and-risk-tolerance"></a>Baseline di sicurezza: metriche, indicatori e tolleranza ai rischi

Questo articolo è progettato per aiutare l'utente a quantificare la tolleranza al rischio aziendale in relazione alla Baseline di sicurezza. La definizione di metriche e indicatori consente di creare un business case per effettuare un investimento riguardo alla disciplina Baseline di sicurezza.

## <a name="metrics"></a>Metriche

La Baseline di sicurezza si concentra in genere sull'identificazione di potenziali vulnerabilità nelle distribuzioni cloud. Nell'ambito dell'analisi dei rischi è necessario raccogliere dati relativi all'ambiente di sicurezza per determinare l'entità dei rischi e l'importanza degli investimenti nella governance della baseline di sicurezza per le distribuzioni cloud pianificate.

Ogni organizzazione dispone di ambienti di sicurezza e requisiti diversi, oltre a potenziali origini di dati sulla sicurezza diverse. Di seguito sono riportati esempi di metriche utili che è necessario raccogliere per valutare la tolleranza ai rischi all'interno della disciplina Baseline di sicurezza:

- **Classificazione dei dati**. Numero di servizi e dati archiviati nel cloud non classificati in base a privacy, conformità o standard di impatto aziendale dell'organizzazione.
- **Numero di archivi dati sensibili**: numero di endpoint o database di archiviazione contenenti dati sensibili e quindi da proteggere.
- **Numero di archivi dati non crittografati**: numero di archivi dati sensibili che non sono crittografati.
- **Superficie di attacco**. Numero totale di origini dati, servizi e applicazioni che saranno ospitate nel cloud. Che percentuale di origini dati è classificata come sensibile? Che percentuale di applicazioni e servizi è cruciale?
- **Standard con copertura**. Numero di standard di sicurezza definiti dal team responsabile della sicurezza.
- **Risorse con copertura**. Risorse distribuite coperte da standard di sicurezza.
- **Conformità complessiva agli standard**. Percentuale di conformità agli standard di sicurezza.
- **Attacchi in base alla gravità**. Quanti tentativi coordinati di interrompere i servizi ospitati nel cloud, ad esempio tramite attacchi Distributed Denial of Service (DDoS), subisce l'infrastruttura? Quali sono le dimensioni e la gravità di questi attacchi?
- **Protezione anti-malware**. Percentuale di macchine virtuali (VM) distribuite che dispongono di tutto il software anti-malware, firewall o altro software di sicurezza necessario.
- **Latenza patch**. Quanto tempo è trascorso dall'applicazione di patch per il sistema operativo e software nelle macchine virtuali.
- **Consigli sull'integrità della sicurezza**. Numero di elementi consigliati relativi al software di sicurezza per la risoluzione di standard di integrità per le risorse distribuite, organizzati in base alla gravità.

## <a name="risk-tolerance-indicators"></a>Indicatori sulla tolleranza ai rischi

Le piattaforme cloud offrono un set di baseline di funzionalità che consente a piccoli team di distribuzione di configurare le impostazioni di sicurezza di base senza una pianificazione approfondita aggiuntiva. Di conseguenza, i primi carichi di lavoro sperimentali o di Sviluppo/test che non includono dati sensibili rappresentano un livello di rischio relativamente basso e probabilmente non necessitano di molto in termini di criteri formali su Baseline di sicurezza. Tuttavia, i rischi per la sicurezza aumentano non appena dati importanti o funzionalità cruciali vengono spostate nel cloud, mentre la tolleranza per tali rischi diminuisce rapidamente. Maggiore è la quantità di funzionalità e dati che vengono distribuiti nel cloud, più è probabile la necessità di un maggiore investimento nella disciplina Baseline di sicurezza.

Nelle prime fasi di adozione del cloud è necessario lavorare con il team addetto alla sicurezza IT e con gli azionisti aziendali per identificare i [rischi aziendali](business-risks.md) correlati alla sicurezza, quindi definire una baseline accettabile per la tolleranza ai rischi di sicurezza. In questa sezione del Cloud Adoption Framework vengono offerti alcuni esempi, ma i rischi dettagliati e le baseline per la società o le distribuzioni potrebbe essere diverse.

Dopo aver creato una baseline, è necessario stabilire i benchmark minimi che rappresentano un aumento inaccettabile di rischi identificati. Questi benchmark fungono da trigger nel momento in cui è necessario intervenire per limitare tali rischi. Di seguito sono riportati alcuni esempi del modo in cui le metriche di sicurezza, ad esempio quelle descritte in precedenza, possono giustificare un maggiore investimento nella disciplina Baseline di sicurezza.

- **Trigger di carichi di lavoro cruciali**. Una società che distribuisce carichi di lavoro cruciali nel cloud deve investire nella disciplina Baseline di sicurezza per evitare potenziali interruzioni del servizio o esposizione di dati sensibili.
- **Trigger di dati protetti**. Una società che ospita nel cloud dati che possono essere classificati come riservati, privati o comunque soggetti a problemi normativi. È necessaria una disciplina Baseline di sicurezza per garantire che tali dati non siano soggetti a rischi di perdita, esposizione o furto.
- **Trigger di attacchi esterni**. Una società che subisce gravi attacchi all'infrastruttura di rete X volte al mese potrebbe beneficiare della disciplina Baseline di sicurezza.  
- **Trigger di conformità agli standard**. Una società con oltre il X% di risorse non conformi agli standard di sicurezza deve investire nella disciplina Baseline di sicurezza per garantire che gli standard vengano applicati in modo coerente in tutta l'infrastruttura IT.
- **Trigger di dimensioni nell'intero cloud**. Una società ospita oltre un numero X di origini dati, servizi o applicazioni. Le distribuzioni cloud di grandi dimensioni possono trarre vantaggio dagli investimenti nella disciplina Baseline di sicurezza per garantire che la superficie di attacco complessiva sia adeguatamente protetta da accessi non autorizzati o altre minacce esterne.
- **Trigger di conformità del software di sicurezza**. Una società in cui meno del X% delle macchine virtuali distribuite dispone di tutto il software di sicurezza necessario. È possibile usare una disciplina Baseline di sicurezza per garantire che il software sia installato in modo coerente in tutto il software.
- **Trigger di applicazione di patch**. Una società in cui non sono state applicate patch per il sistema operativo o software negli ultimi X giorni su macchine virtuali o su servizi distribuiti. È possibile usare una disciplina Baseline di sicurezza per garantire che l'applicazione di patch venga mantenuta aggiornata all'interno di una pianificazione obbligatoria.
- **Sicurezza in evidenza**. Alcune società dispongono di requisiti rigorosi in termini di sicurezza e riservatezza, anche per carichi di lavoro di test e sperimentali. Queste società hanno la necessità di investire nella disciplina Baseline di sicurezza prima di poter iniziare qualsiasi distribuzione.

Le metriche e i trigger esatti da usare per misurare la tolleranza ai rischi e il grado di investimento nella disciplina Baseline di sicurezza saranno specifici per ogni organizzazione, ma gli esempi precedenti possono fungere da base utile per una discussione all'interno del team addetto alla governance del cloud.  

## <a name="next-steps"></a>Passaggi successivi

Usare il [Cloud Management template (modello di Gestione cloud)](./template.md), documentare le metriche e gli indicatori di tolleranza al rischio che si allineano al piano di adozione del cloud attuale.

Sulla base dei rischi e della tolleranza, stabilire un processo per controllare e comunicare la conformità ai criteri della Baseline di sicurezza.

> [!div class="nextstepaction"]
> [Stabilire i processi di conformità ai criteri](compliance-processes.md)
