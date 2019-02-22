---
title: 'CAF: Le cinque discipline della governance del cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Panoramica del contenuto di governance all'interno del CAF
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: 1721e2ff7b4e7879637a7c09a40a5571b73c9ace
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901908"
---
# <a name="the-five-disciplines-of-cloud-governance"></a>Le cinque discipline della governance del cloud

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsI">
<li style="display: flex; flex-direction: column;">
    <div class="cardSize">
        <div class="cardPadding" style="padding-bottom:10px;">
            <div class="card" style="padding-bottom:10px;">
                <div class="cardText" style="padding-left:0px;">
Qualsiasi modifica apportata a processi di business o piattaforme tecnologiche introduce dei rischi. I team di governance del cloud, i cui membri sono talvolta definiti come responsabili del cloud, hanno il compito di ridurre tali rischi, con un'interruzione minima per gli interventi di adozione o innovazione.<br/><br/>Il modello di governance CAF guida queste decisioni indipendentemente dalla piattaforma cloud scelta, concentrandosi sullo <a href="#corporate-policy">sviluppo di criteri aziendali</a> e sulle <a href="#disciplines-of-cloud-governance">discipline della governance del cloud</a>. Le <a href="#actionable-journeys">Guide alla progettazione operativa</a> illustrano questo modello usando i servizi di Azure. Questo articolo funge da pagina di destinazione per le cinque discipline del modello di governance CAF.
                </div>
            </div>
        </div>
    </div>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../_images/operational-transformation-govern-highres.png" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize">
            <div class="cardPadding" style="padding-bottom:10px;">
                <div class="card" style="padding-bottom:10px;">
                    <div class="cardText" style="padding-left:0px;">
<img src="../_images/operational-transformation-govern-highres.png" alt="Diagram of the CAF governance model: Corporate policy and governance disciplines">
<br>
<i>Figura 1. Oggetto visivo relativo alle cinque discipline di governance del cloud e ai criteri aziendali</i>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="disciplines-of-cloud-governance"></a>Discipline della governance del cloud

In ogni provider di servizi cloud è presente una serie di discipline di governance del cloud comuni che possono essere usate come guida informativa sui criteri e sull'allineamento di toolchain. Le discipline guidano le decisioni riguardo il livello appropriato di automazione e riguardo l'imposizione di criteri aziendali nei vari provider di servizi cloud.

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsA">
<li style="display: flex; flex-direction: column;">
    <a href="./cost-management/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/cost-management.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Gestione costi</h3>
                        <p>Il costo è una delle preoccupazioni principali per gli utenti del cloud. Sviluppare criteri per il controllo dei costi per tutte le piattaforme cloud.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./security-baseline/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/security-baseline.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Baseline di sicurezza</h3>
                        <p>La sicurezza è un argomento complesso e personale, univoco per ogni azienda. Una volta stabiliti i requisiti di sicurezza, i criteri e l'imposizione di governance del cloud applicano questi requisiti nelle configurazioni di rete, dati e asset.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./identity-baseline/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/identity-baseline.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Baseline di identità</h3>
                        <p>Le incoerenze nell'applicazione dei requisiti di identità possono aumentare il rischio di violazione. La disciplina Baseline di identità è incentrata sui modi per verificare che l'identità venga applicata in modo uniforme tra gli interventi di adozione del cloud.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./resource-consistency/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/resource-consistency.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Coerenza delle risorse</h3>
                        <p>Le operazioni cloud dipendono dalla coerenza nelle configurazioni delle risorse. Tramite gli strumenti di governance, le risorse possono essere configurate in modo coerente per ridurre i rischi correlati a onboarding, desincronizzazione, individuabilità e ripristino.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./deployment-acceleration/overview.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/deployment-acceleration.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Accelerazione della distribuzione</h3>
                        <p>Centralizzazione, standardizzazione e coerenza negli approcci di distribuzione e configurazione migliorano le procedure di governance. Quando resi disponibili tramite gli strumenti di governance basati sul cloud, creano un fattore cloud che consente di velocizzare le attività di distribuzione.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->
