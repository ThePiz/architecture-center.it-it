---
title: 'CAF: Motivazioni e rischi aziendali della Baseline di identità'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Motivazioni e rischi aziendali della Baseline di identità
author: BrianBlanchard
ms.openlocfilehash: ef831d1b3b01251b27f1f7b18e551c49996a2468
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246522"
---
# <a name="identity-baseline-motivations-and-business-risks"></a>Motivazioni e rischi aziendali della Baseline di identità

Questo articolo illustra i motivi per cui i clienti in genere adottano la disciplina Baseline di identità all'interno di una strategia di governance del cloud. Offre anche alcuni esempi di rischi aziendali che determinano le informative sui criteri.

<!-- markdownlint-disable MD026 -->

## <a name="is-identity-baseline-relevant"></a>La Baseline di identità è rilevante?

Le directory locali tradizionali sono progettate per consentire alle aziende di controllare accuratamente le autorizzazioni e i criteri per gli utenti, i gruppi e i ruoli all'interno delle reti interne e dei datacenter. Questo avviene per supportare le implementazioni di singoli tenant, con servizi applicabili solo all'interno dell'ambiente locale.

I servizi di gestione delle identità cloud sono progettati per espandere le funzionalità di autenticazione e controllo di accesso a Internet di un'organizzazione. Supportano il multi-tenancy e possono essere usati per gestire utenti e criteri di accesso tra le applicazioni cloud e le distribuzioni. Le piattaforme cloud pubbliche hanno una qualche forma di servizi di gestione delle identità cloud nativa che supporta le attività di gestione e distribuzione e sono in grado di [variare i livelli di integrazione](../../decision-guides/identity/overview.md) con le soluzioni di gestione delle identità locali esistenti. Tutte queste caratteristiche possono rendere i criteri di identità cloud più complicati di quanto richiesto dalle tradizionali soluzioni locali.

L'importanza della disciplina Baseline di identità per la distribuzione cloud, dipenderà dalle dimensioni del team e dalla necessità di integrare la soluzione di gestione delle identità basata su cloud con un servizio di gestione delle identità locale esistente. Le distribuzione dei test iniziali possono non richiedere molto in termini di organizzazione utente o di gestione, ma con la maturazione della struttura cloud, è probabile che sia necessario supportare un'integrazione organizzativa più complessa e una gestione centralizzata.

## <a name="business-risk"></a>Rischio aziendale

La disciplina Baseline di identità cerca di affrontare i rischi aziendali principali relativi ai servizi di gestione delle identità e al controllo di accesso. La disciplina lavora con l'azienda per identificare questi rischi e monitorarne la pertinenza durante la pianificazione e l'implementazione delle distribuzioni cloud.

I rischi variano da un'organizzazione all'altra, tuttavia i seguenti servono come rischi comuni relativi alla sicurezza che è possibile usare come punto di partenza per le discussioni all'interno del team di governance del cloud:

- **Accesso non autorizzato**. Dati e risorse sensibili a cui possono accedere utenti non autorizzati possono portare a fughe di dati o interruzioni del servizio, violando il perimetro di sicurezza della vostra organizzazione e rischiando di incorrere in responsabilità commerciali o legali.
- **Inefficienza dovuta a più soluzioni di gestione delle identità**. Le organizzazioni con più tenant di servizi di gestione delle identità possono richiedere più account per gli utenti. Ciò può portare a inefficienze per gli utenti, che devono ricordare più set di credenziali e per l'IT nella gestione degli account su più sistemi. Se le assegnazioni di accesso degli utenti non vengono aggiornate tra le soluzioni di gestione delle identità se il personale, i team e gli obiettivi aziendali cambiano, le risorse cloud possono risultare vulnerabili all'accesso non autorizzato o rendere impossibile agli utenti accedere alle risorse richieste.
- **Impossibilità di condividere le risorse con partner esterni**. La difficoltà di aggiungere partner commerciali esterni alle soluzioni di gestione delle identità esistenti può impedire un'efficiente condivisione delle risorse e comunicazione aziendale.
- **Dipendenze da servizi di gestione delle identità locali**. I meccanismi di autenticazione legacy o l'autenticazione a più fattori di terze parti potrebbero non essere disponibili nel cloud, richiedendo la migrazione dei carichi di lavoro da riattrezzare o l'implementazione di servizi di gestione delle identità aggiuntivi nel cloud. Entrambi i requisiti potrebbero ritardare o impedire la migrazione e aumentare i costi.

## <a name="next-steps"></a>Passaggi successivi

Usare il [modello di gestione del cloud](./template.md) per documentare i rischi aziendali che è probabile che vengano introdotti dal piano di adozione del cloud attuale.

Dopo aver stabilito in modo realistico i rischi aziendali, il passaggio successivo consiste nel documentare le metriche e gli indicatori principali della tolleranza ai rischi dell'azienda allo scopo di monitorare tale tolleranza.

> [!div class="nextstepaction"]
> [Indicatori, metriche e tolleranza ai rischi](./metrics-tolerance.md)