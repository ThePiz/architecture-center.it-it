---
title: Gestione delle identità
description: Illustra e confronta i diversi metodi disponibili per la gestione delle identità in sistemi ibridi che estendono il limite in locale/cloud con Azure.
layout: LandingPage
ms.openlocfilehash: de98ee30306f5e712759ab7140bd430cb6d4cd75
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
<!-- This file is generated! -->
<!-- See the templates in ./build/reference-architectures  -->
<!-- See data in index.json -->

# <a name="identity-management"></a>Gestione delle identità

Queste architetture di riferimento mostrano le opzioni per l'integrazione dell'ambiente di Active Directory (AD) locale con una rete di Azure. <br/>[Qual è la scelta migliore?](./considerations.md)

<section class="series">
    <ul class="panelContent">
    <!-- Integrate with Azure Active Directory -->
<li style="display: flex; flex-direction: column;">
    <a href="./azure-ad.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/azure-ad.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Integrazione con Azure Active Directory</h3>
                        <p>Integrare foreste e domini di Active Directory locali con Azure AD.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- Extend AD DS to Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./adds-extend-domain.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/adds-extend-domain.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Estendere Active Directory Domain Services ad Azure</h3>
                        <p>Estendere l'ambiente Active Directory in Azure tramite Active Directory Domain Services.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- Create an AD DS forest in Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./adds-forest.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/adds-forest.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Creare una foresta di Active Directory Domain Services in Azure</h3>
                        <p>Creare un altro dominio di Active Directory separato in Azure che è considerato attendibile dai domini nella foresta locale.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- Extend AD FS to Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./adfs.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/adfs.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Estendere AD FS in Azure</h3>
                        <p>Usare Active Directory Federation Services per l'autenticazione federata e l'autorizzazione in Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    </ul>
</section>

<ul class="panelContent cardsI">
</ul>