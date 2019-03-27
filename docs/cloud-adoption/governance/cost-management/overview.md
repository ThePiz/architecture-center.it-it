---
title: 'CAF: Disciplina Gestione costi'
description: Spiegazione della disciplina Gestione costi in relazione alla governance del cloud
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: a86b1a75da57cec9c9aaf88abb70049f70731561
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243702"
---
# <a name="cost-management-discipline-overview"></a>Panoramica della disciplina Gestione costi

Gestione costi è una delle [cinque discipline della governance del cloud](../governance-disciplines.md) nell'ambito del [modello di governance CAF](../overview.md). Per molti clienti, la gestione dei costi è una delle principali preoccupazioni riguardo all'adozione di tecnologie cloud. Trovare il giusto equilibrio tra esigenze di prestazioni, tempi di implementazione e costi dei servizi cloud può rivelarsi un'impresa difficile. Ciò è particolarmente importante per le trasformazioni aziendali di maggiore entità in cui vengono implementate tecnologie cloud. Questa sezione illustra l'approccio da adottare per sviluppare una disciplina Gestione costi come parte di una strategia di governance del cloud.  

> [!NOTE]
> La disciplina di governance Gestione costi non sostituisce i team di gestione aziendale, le pratiche contabili e le procedure già esistenti nell'ambito della gestione finanziaria dei costi correlati all'IT di un'organizzazione. Lo scopo principale di questa disciplina è identificare i potenziali rischi del cloud in relazione alla spesa IT e fornire indicazioni per la mitigazione di tali rischi ai team responsabili delle attività aziendali e dell'IT che sono incaricati dell'implementazione e della gestione delle distribuzioni cloud. Quando si sviluppano i criteri e i processi di governance, assicurarsi di coinvolgere nelle attività di pianificazione e revisione il personale competente dei team responsabili delle attività aziendali e IT.

I principali destinatari di queste linee guida sono gli architetti dell'infrastruttura cloud delle organizzazioni e gli altri membri del team di governance del cloud. Tuttavia, le decisioni, i criteri e i processi che emergono da questa disciplina dovrebbero coinvolgere anche i membri appropriati dei team che si occupano delle attività aziendali e dell'IT, soprattutto i responsabili della proprietà, della gestione e dei pagamenti relativi ai carichi di lavoro basati sul cloud.

## <a name="policy-statements"></a>Policy statements

Una disciplina Gestione costi si basa principalmente sulle definizioni dei criteri in base ai quali prendere decisioni operative e sui requisiti dell'architettura risultanti. Per esempi di definizioni dei criteri, vedere l'articolo [Definizioni dei criteri di esempio per la gestione dei costi](./policy-statements.md). Questi esempi possono essere usati come punto di partenza per definire i criteri di governance della propria organizzazione.

> [!CAUTION]
> I criteri di esempio sono stati sviluppati in base a esperienze comuni dei clienti. Per allineare questi criteri a specifiche esigenze di governance del cloud e creare definizioni dei criteri che siano in grado di soddisfare le esigenze esclusive della propria organizzazione, eseguire i passaggi seguenti.

## <a name="developing-cost-management-governance-policy-statements"></a>Sviluppo di definizioni dei criteri di governance per la gestione dei costi

I sei passaggi seguenti consentono di definire i criteri di governance per controllare i costi nel proprio ambiente.

<!-- markdownlint-disable MD033 -->

<ul  class="panelContent cardsE">
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
                        <h3>Modello di gestione dei costi</h3>
                        <p class="x-hidden-focus">Scaricare il modello per documentare una disciplina Gestione costi.</p>
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
                        <p class="x-hidden-focus">Comprendere le motivazioni e i rischi comunemente associati alla disciplina Gestione costi.</p>
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
                        <p class="x-hidden-focus">Definire indicatori per capire se è il momento giusto di investire nella disciplina Gestione costi.</p>
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
                        <p class="x-hidden-focus">Seguire i suggerimenti per creare processi che consentono di assicurare la conformità ai criteri della disciplina Gestione costi.</p>
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
                        <p class="x-hidden-focus">Implementare i servizi di Azure che possono supportare la disciplina Gestione costi.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="next-steps"></a>Passaggi successivi

Iniziare con la valutazione dei rischi aziendali in un determinato ambiente.

> [!div class="nextstepaction"]
> [Informazioni sui rischi aziendali](./business-risks.md)

<!-- markdownlint-enable MD033 -->
