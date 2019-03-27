---
title: 'CAF: panoramica della disciplina di accelerazione della distribuzione'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Spiegazione dell'accelerazione della distribuzione in relazione alla governance del cloud.
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: 8a9cd359f631708d07ab601c4488b969dc8294a0
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246592"
---
# <a name="caf-deployment-acceleration-discipline-overview"></a>CAF: panoramica della disciplina di accelerazione della distribuzione

L'accelerazione della distribuzione è una delle [cinque discipline della governance del cloud](../governance-disciplines.md) nell'ambito del [modello di governance CAF](../overview.md). Questa disciplina è incentrata sulle modalità di definizione dei criteri per definire la configurazione o la distribuzione di risorse. All'interno delle cinque discipline della governance del cloud, l'accelerazione della distribuzione include distribuzione, allineamento della configurazione e riusabilità degli script, tramite attività manuali o tramite attività DevOps completamente automatizzate. In entrambi i casi, i criteri rimangono in gran parte gli stessi. Man mano che questo disciplina viene perfezionata, il team di governance del cloud può fungere da partner nelle strategie DevOps e di distribuzione promuovendo l'accelerazione delle distribuzioni e la rimozione degli ostacoli per l'adozione del cloud tramite l'applicazione di asset riutilizzabili.

Questo articolo illustra il processo di accelerazione della distribuzione in un'azienda durante la pianificazione, la creazione, l'adozione e le fasi operative dell'implementazione di una soluzione cloud. Non è possibile rendere conto in un documento di tutti i requisiti per tutte le aziende. Ogni sezione di questo articolo descrive quindi solo attività minime e potenziali. L'obiettivo di queste attività è quello di consentire la creazione di un [prodotto minimo funzionante per i criteri](../policy-compliance/overview.md#minimum-viable-product-mvp-for-policy), stabilendo un framework per l'evoluzione [incrementale dei criteri](../policy-compliance/overview.md#incremental-policy-growth). Il team della governance del cloud deve stabilire in che misura investire in queste attività per migliorare la posizione dell'accelerazione della distribuzione.

> [!NOTE]
> La disciplina di accelerazione della distribuzione non sostituisce i team, i processi e le procedure IT esistenti che consentono alle organizzazioni di distribuire e configurare le risorse basate sul cloud in modo efficace. Lo scopo principale di questa disciplina è identificare i rischi potenziali per l'azienda e fornire indicazioni per l'attenuazione dei rischi al personale IT responsabile della gestione delle risorse nel cloud. Quando si sviluppano i criteri e i processi di governance, assicurarsi di coinvolgere nelle attività di pianificazione e revisione i team IT appropriati.

I principali destinatari di queste linee guida sono gli architetti dell'infrastruttura cloud delle organizzazioni e gli altri membri del team di governance del cloud. Le decisioni, i criteri e i processi che emergono da questa disciplina, tuttavia, devono coinvolgere anche i membri appropriati dei team delle attività aziendali e IT, soprattutto i responsabili della distribuzione e della configurazione dei carichi di lavoro basati sul cloud.

## <a name="policy-statements"></a>Policy statements

Una disciplina di accelerazione della distribuzione si basa principalmente sulle definizioni dei criteri in base ai quali prendere decisioni operative e sui requisiti dell'architettura risultanti. Per esempi di definizioni dei criteri, vedere l'articolo [Definizioni dei criteri per l'accelerazione della distribuzione](./policy-statements.md). Questi esempi possono essere usati come punto di partenza per definire i criteri di governance della propria organizzazione.

> [!CAUTION]
> I criteri di esempio sono stati sviluppati in base a esperienze comuni dei clienti. Per allineare questi criteri a specifiche esigenze di governance del cloud e creare definizioni dei criteri che siano in grado di soddisfare le esigenze esclusive della propria organizzazione, eseguire i passaggi seguenti.

## <a name="developing-deployment-acceleration-governance-policy-statements"></a>Sviluppo di definizioni dei criteri di governance dell'accelerazione della distribuzione

I sei passaggi seguenti consentono di definire i criteri di governance per controllare la distribuzione e la configurazione delle risorse nell'ambiente cloud.

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsE">
<li style="display: flex; flex-direction: column;">
    <a href="./template.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-template.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>Modello di accelerazione della distribuzione</h3>
                        <p class="x-hidden-focus">Scaricare il modello per documentare una disciplina di accelerazione della distribuzione</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li><li style="display: flex; flex-direction: column;">
    <a href="./business-risks.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-risks.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>Rischi aziendali</h3>
                        <p class="x-hidden-focus">Comprendere le motivazioni e i rischi comunemente associati alla disciplina di accelerazione della distribuzione.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./metrics-tolerance.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-metrics.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>Indicatori e metriche</h3>
                        <p class="x-hidden-focus">Definire indicatori per capire se è il momento giusto di investire nella disciplina di accelerazione della distribuzione.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./compliance-processes.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-enforce.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>Processi di adesione ai criteri</h3>
                        <p class="x-hidden-focus">Seguire i suggerimenti per creare processi che consentono di assicurare la conformità ai criteri della disciplina di accelerazione della distribuzione.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./discipline-improvement.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-maturity.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>Livello di maturità</h3>
                        <p class="x-hidden-focus">Allineare il livello di maturità della gestione del cloud alle fasi di adozione del cloud.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./toolchain.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-toolchain.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>Toolchain</h3>
                        <p class="x-hidden-focus">Implementare i servizi di Azure che possono supportare la disciplina di accelerazione della distribuzione.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="next-steps"></a>Passaggi successivi

Iniziare con la valutazione dei [rischi aziendali](./business-risks.md) in un ambiente specifico.

> [!div class="nextstepaction"]
> [Informazioni sui rischi aziendali](./business-risks.md)

<!-- markdownlint-enable MD033 -->