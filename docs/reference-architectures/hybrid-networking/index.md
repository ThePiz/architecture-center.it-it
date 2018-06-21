---
title: Connettere una rete locale ad Azure
description: Architetture consigliate per connessioni di rete sicure e solide tra le reti locali e Azure.
layout: LandingPage
ms.openlocfilehash: 372efb8ecf69245a5895c51e3da156a348bd665e
ms.sourcegitcommit: 2123c25b1a0b5501ff1887f98030787191cf6994
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
ms.locfileid: "29782328"
---
<!-- This file is generated! -->
<!-- See the templates in ./build/reference-architectures  -->
<!-- See data in index.json -->

# <a name="connect-an-on-premises-network-to-azure"></a>Connettere una rete locale ad Azure

Queste architetture di riferimento mostrano procedure comprovate per la creazione di una connessione di rete solida tra una rete locale e Azure. [Qual è la scelta migliore?](./considerations.md)

<section class="series">
    <ul class="panelContent">
    <!-- VPN -->
<li style="display: flex; flex-direction: column;">
    <a href="./vpn.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/vpn.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>VPN</h3>
                        <p>Estende una rete locale ad Azure tramite una rete privata virtuale (VPN) da sito a sito.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- ExpressRoute -->
<li style="display: flex; flex-direction: column;">
    <a href="./expressroute.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/expressroute.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>ExpressRoute</h3>
                        <p>Estende una rete locale ad Azure tramite Azure ExpressRoute.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- ExpressRoute with VPN failover -->
<li style="display: flex; flex-direction: column;">
    <a href="./expressroute-vpn-failover.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/expressroute-vpn-failover.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>ExpressRoute con failover VPN</h3>
                        <p>Estende una rete locale ad Azure tramite Azure ExpressRoute, usando una rete VPN come connessione di failover.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- Hub-spoke topology -->
<li style="display: flex; flex-direction: column;">
    <a href="./hub-spoke.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/hub-spoke.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Topologia hub-spoke</h3>
                        <p>L'hub è il punto centrale della connettività alla rete locale. Gli spoke sono le reti virtuali peer con l'hub, e possono essere usati per isolare i carichi di lavoro.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
    <!-- Hub-spoke topology with shared services -->
<li style="display: flex; flex-direction: column;">
    <a href="./shared-services.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./images/shared-services.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Topologia hub-spoke con servizi condivisi</h3>
                        <p>Distribuire una topologia hub-spoke che include servizi condivisi, con servizi di Active Directory e un'appliance virtuale di rete. I servizi condivisi potranno essere utilizzati da tutti gli spoke.</p>
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