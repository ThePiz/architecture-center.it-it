---
title: 'CAF: Accelerazione della distribuzione: metriche, indicatori e tolleranza ai rischi'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 'Accelerazione della distribuzione: metriche, indicatori e tolleranza ai rischi'
author: alexbuckgit
ms.openlocfilehash: 87d6f9f67b98d5761aced560392c9d38fa682ee7
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901551"
---
# <a name="deployment-acceleration-metrics-indicators-and-risk-tolerance"></a>Accelerazione della distribuzione: metriche, indicatori e tolleranza ai rischi

Questo articolo è stato progettato per aiutare a quantificare la tolleranza ai rischi aziendali in relazione all'accelerazione della distribuzione. La definizione di metriche e indicatori consente di creare un business case per effettuare un investimento riguardo alla disciplina Accelerazione della distribuzione.

## <a name="metrics"></a>Metriche

L'accelerazione della distribuzione riguarda principalmente la distribuzione, l'aggiornamento e la gestione delle risorse cloud configurate per il corretto funzionamento dei sistemi. Quando si adotta questa disciplina di governance del cloud sono utili le informazioni seguenti:

- **Obiettivo del tempo di ripristino (RTO)**. Il tempo massimo accettabile in cui un'applicazione può rimanere non disponibile dopo un evento imprevisto.
- **Obiettivo del punto di ripristino (RPO)**. La durata massima di perdita dei dati accettabile durante un'emergenza. Per esempio, se si archiviano i dati in un singolo database, senza replica in altri database, e si eseguono backup orari, si potrebbero perdere fino a un'ora di dati.
- **Tempo medio di recupero (MTTR)**. Il tempo medio necessario per il ripristino di un componente dopo un guasto.
- **Tempo medio tra gli errori (MTBF)**. L'intervallo di tempo in cui è ragionevole aspettarsi che un componente rimanga in esecuzione tra un'interruzione del servizio e l'altra. Questa metrica può essere utile per calcolare la frequenza con cui un servizio non sarà più disponibile.
- **Contratti di servizio**. Possono includere gli impegni di Microsoft in termini di tempo di attività e connettività dei servizi di Azure e gli impegni dell'azienda nei confronti di clienti interni ed esterni.
- **Tempo di distribuzione**. La quantità di tempo necessaria per distribuire gli aggiornamenti in un sistema esistente.
- **Asset non conformi**. Il numero o la percentuale delle risorse che non sono conformi ai criteri definiti.

## <a name="risk-tolerance-indicators"></a>Indicatori sulla tolleranza ai rischi

I rischi dell'accelerazione della distribuzione sono in gran parte correlati al numero e alla complessità dei sistemi basati sul cloud distribuiti per l'azienda. Di pari passo con la crescita dell'ambiente cloud, si assiste a un aumento del numero di sistemi distribuiti e della frequenza di aggiornamento delle risorse cloud. Le dipendenze tra le risorse rendono ancora più importante la corretta configurazione delle risorse e della progettazione dei sistemi per la resilienza, in caso di tempi di inattività imprevisti di una o più risorse.

<!-- "en-us" location is required for the URL below. -->

Valutare l'opportunità di adottare una cultura organizzativa basata su DevOps o [DevSecOps](https://www.microsoft.com/en-us/securityengineering/devsecops) sin dalle prime fasi del percorso di adozione del cloud. Nelle organizzazioni IT aziendali di tipo tradizionale, i team responsabili delle operazioni, della sicurezza e dello sviluppo spesso non collaborano nel modo corretto o hanno addirittura un rapporto conflittuale. Se si identificano questi problemi in anticipo e si favorisce l'integrazione degli stakeholder principali di ogni team, sarà più facile assicurare un'adozione del cloud più agile mantenendo al tempo stesso un ambiente sicuro e gestito in modo efficiente.

Collaborare con il team di DevSecOps e con gli stakeholder aziendali per identificare i [rischi aziendali](business-risks.md) correlati alla configurazione e definire quindi una baseline accettabile per la tolleranza ai rischi di configurazione. In questa sezione della guida CAF sono riportati alcuni esempi, ma i dettagli su rischi e baseline saranno probabilmente diversi a seconda dell'azienda o delle distribuzioni.

Dopo aver creato una baseline, è necessario stabilire i benchmark minimi che rappresentano un aumento inaccettabile dei rischi identificati. Questi benchmark svolgono la funzione di trigger da attivare nel momento in cui è necessario intervenire per mitigare questi rischi. Di seguito sono riportati alcuni esempi del modo in cui le metriche correlate alla configurazione, come quelle descritte in precedenza, possono giustificare un maggiore investimento nella disciplina Accelerazione della distribuzione.

- **Trigger in base a contratto di servizio (SLA)**. Se un'azienda non è in grado di soddisfare i contratti di servizio con i clienti esterni o i partner interni, dovrebbe investire nella disciplina Accelerazione della distribuzione per ridurre i tempi di inattività dei sistemi.
- **Trigger in base a tempo di ripristino**. Se un'azienda supera le soglie da rispettare per il tempo di ripristino in seguito un errore di sistema, dovrebbe investire per migliorare la disciplina Accelerazione della distribuzione e la progettazione dei sistemi in modo da ridurre o eliminare i guasti o l'effetto dei tempi di inattività dei singoli componenti.
- **Trigger in base a deviazione di configurazione**. Se un'azienda riscontra cambiamenti imprevisti nella configurazione dei componenti di sistema principali, oppure errori nella distribuzione o negli aggiornamenti dei sistemi, dovrebbe investire nella disciplina Accelerazione della distribuzione per identificare le cause principali e le operazioni correttive da intraprendere.  
- **Trigger in base a non conformità**. Se il numero di risorse non conformi supera una soglia definita (come numero totale di risorse o come percentuale del totale), un'azienda dovrebbe investire per migliorare la disciplina Accelerazione della distribuzione in modo da assicurare che la configurazione di ogni risorsa rimanga conforme per tutto il suo ciclo di vita.
- **Trigger in base a pianificazione del progetto**. Se il tempo necessario per distribuire le risorse e le applicazioni dell'azienda supera spesso una soglia definita, un'azienda dovrebbe investire nei processi di distribuzione dell'accelerazione per introdurre o migliorare distribuzioni automatizzate in modo da garantire coerenza e prevedibilità. I tempi di distribuzione espressi in giorni o addirittura settimane indicano in genere una strategia di distribuzione dell'accelerazione non ottimale.

## <a name="next-steps"></a>Passaggi successivi

Usare il [modello di gestione del cloud](./template.md) per documentare le metriche e gli indicatori di tolleranza in linea con il piano di adozione del cloud attuale.

Sulla base dei rischi e della tolleranza, stabilire un processo per controllare e comunicare la conformità ai criteri di Accelerazione della distribuzione.

> [!div class="nextstepaction"]
> [Stabilire processi di conformità ai criteri](compliance-processes.md)
