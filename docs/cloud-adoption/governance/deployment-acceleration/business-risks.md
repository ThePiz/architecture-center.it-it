---
title: "CAF: Motivazioni e rischi aziendali che determinano l'accelerazione della distribuzione"
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sulla disciplina Accelerazione della distribuzione come parte di una strategia di governance del cloud.
author: alexbuckgit
ms.openlocfilehash: 854b1fd420de605a665922e9b207e6aecbfab2f0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901279"
---
# <a name="deployment-acceleration-motivations-and-business-risks"></a>Motivazioni e rischi aziendali di Accelerazione della distribuzione

Questo articolo illustra i motivi per cui i clienti in genere adottano la disciplina Accelerazione della distribuzione all'interno di una strategia di governance del cloud. Offre inoltre alcuni esempi di rischi aziendali che determinano le definizioni sui criteri.

<!-- markdownlint-disable MD026 -->

## <a name="is-deployment-acceleration-relevant"></a>È rilevante l'accelerazione della distribuzione?

I sistemi locali spesso sono distribuiti usando immagini di base o script di installazione. Di solito è necessaria una configurazione aggiuntiva, che può comportare più fasi o l'intervento dell'utente. Questi processi manuali sono soggetti a errori e spesso generano una "deviazione della configurazione", che richiede attività di correzione e risoluzione dei problemi dispendiose in termini di tempo.

La maggior parte delle risorse di Azure può essere distribuita e configurata manualmente tramite il portale di Azure. Questo approccio potrebbe essere sufficiente per le proprie esigenze se si hanno poche risorse da gestire. Tuttavia, man mano che l'intero cloud è in crescita, l'organizzazione deve automatizzare la distribuzione delle risorse cloud per sfruttare il ridimensionamento, il failover e le funzionalità di ripristino di emergenza offerti da Azure. L'adozione di un approccio DevOps o DevSecOps rappresenta spesso il modo migliore per gestire le distribuzioni.

Un piano di Accelerazione della distribuzione affidabile assicura che le risorse del cloud vengano distribuite, aggiornate, configurate in modo corretto e coerente e rimangano tali. Il livello di maturità della strategia di Accelerazione della distribuzione può costituire inoltre un fattore significativo nella [Cost Management strategy (strategia di Gestione costi)](../cost-management/overview.md). La configurazione e il provisioning automatizzato delle risorse cloud consentono di ridurre o deallocare le risorse quando la richiesta è scarsa o limitata nel tempo, in modo da pagare per le risorse solo quando servono.

## <a name="business-risk"></a>Rischio aziendale

La disciplina Accelerazione della distribuzione cerca di risolvere i rischi aziendali seguenti. Durante l'adozione del cloud, monitorare la pertinenza di ognuno degli aspetti seguenti:

- **Interruzioni del servizio**. La mancanza di processi di distribuzione ripetibili e prevedibili o modifiche non gestite alle configurazioni di sistema possono compromettere il normale funzionamento e causare una perdita di produttività o una perdita economica.
- **Costi eccessivi**. Le modifiche impreviste nella configurazione delle risorse di sistema possono rendere più difficoltosa l'identificazione della causa radice dei problemi, aumentando i costi di sviluppo, operazioni e manutenzione.
- **Inefficienze organizzative**. Le barriere tra team di sviluppo, operazioni e sicurezza possono causare numerose sfide all'adozione efficace delle tecnologie cloud e allo sviluppo di un modello di governance del cloud unificato.

## <a name="next-steps"></a>Passaggi successivi

Usare il [Cloud Management Template (modello di Gestione cloud)](./template.md), documentare i rischi aziendali che potrebbero essere introdotti dal piano di adozione del cloud attuale.

Dopo aver stabilito il riconoscimento di rischi aziendali realistici, il passaggio successivo consiste nel documentare le metriche e gli indicatori principali sulla tolleranza ai rischi aziendali per monitorare tale tolleranza.

> [!div class="nextstepaction"]
> [Metriche, indicatori e tolleranza ai rischi](./metrics-tolerance.md)
