---
title: 'CAF: Implementare una strategia di governance del cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: Informazioni sull'uso di Microsoft Cloud Adoption Framework (CAF) per Azure per implementare una strategia di governance del cloud.
author: BrianBlanchard
ms.date: 1/3/2019
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: 88aeb92157a37baacc34884de67024ec87b20980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901812"
---
# <a name="implement-a-cloud-governance-strategy"></a>Implementare una strategia di governance del cloud

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsI">
<li style="display: flex; flex-direction: column;">
    <div class="cardSize">
        <div class="cardPadding" style="padding-bottom:10px;">
            <div class="card" style="padding-bottom:10px;">
                <div class="cardText" style="padding-left:0px;">
Qualsiasi modifica apportata a processi aziendali o a piattaforme tecnologiche introduce dei rischi per il business. I team di governance del cloud, i cui membri sono talvolta definiti come responsabili del cloud, hanno il compito di ridurre tali rischi, con un'interruzione minima per gli interventi di adozione o innovazione.<br/><br/>La governance del cloud, tuttavia, richiede più della semplice implementazione tecnica. Piccole modifiche nei flussi o nei criteri aziendali possono influire in modo significativo sugli sforzi destinati alle attività di adozione. Prima dell'implementazione, è importante definire criteri aziendali guardando al di là delle attività IT.<br/><br/>
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
<i>Figura 1. Oggetto visivo relativo ai criteri aziendali e alle cinque discipline di governance del cloud</i>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="define-corporate-policy"></a>Definire i criteri aziendali

La definizione di criteri aziendali si concentra sull'identificazione e sulla mitigazione dei rischi aziendali indipendentemente dalla piattaforma cloud. Una strategia di governance del cloud efficace inizia da criteri aziendali validi. Il processo in tre passaggi seguente illustra lo sviluppo iterativo di tali criteri.

<!-- markdownlint-disable MD033 -->

<ul  class="panelContent cardsF">
<li style="display: flex; flex-direction: column;">
    <a href="./policy-compliance/understanding-business-risk.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/business-risk.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Rischio aziendale</h3>
                        <p>Per identificare i rischi per l'azienda, esaminare i piani di adozione del cloud e la classificazione dei dati attualmente in uso. Collaborare con l'azienda per trovare un equilibrio tra tolleranza ai rischi e costi di mitigazione.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./policy-compliance/define-policy.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/corporate-policy.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Criteri e conformità</h3>
                        <p>Valutare la tolleranza ai rischi per dare forma a criteri meno invasivi possibile che consentano di regolare l'adozione del cloud e ridurre i rischi. In alcuni settori, la conformità delle terze parti influisce sulla creazione iniziale dei criteri.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./policy-compliance/processes.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/governance/enforcement.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Processi</h3>
                        <p>Il ritmo delle attività di adozione e innovazione crea naturalmente violazioni ai criteri. L'esecuzione di processi pertinenti contribuisce al monitoraggio e all'imposizione del rispetto dei criteri.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="next-steps"></a>Passaggi successivi

Una strategia di governance del cloud efficace inizia dalla comprensione dei rischi aziendali.

> [!div class="nextstepaction"]
> [Comprendere i rischi aziendali](./policy-compliance/understanding-business-risk.md)
