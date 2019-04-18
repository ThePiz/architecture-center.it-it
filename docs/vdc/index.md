---
title: Data center virtuale di Azure
description: Risorse per il data center virtuale di Microsoft Azure
keywords: Azure
layout: LandingPage
ms.topic: landing-page
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: f49640f4a994bf1cdca8029260fc95a5da323ff2
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640618"
---
# <a name="azure-virtual-datacenter-and-the-enterprise-control-plane"></a>Data center virtuale di Azure e piano di controllo aziendale

Il data center virtuale di Azure rappresenta un approccio per ottenere il massimo dalle funzionalità delle piattaforme cloud di Azure, rispettando al tempo stesso i criteri di sicurezza e di rete esistenti. Quando le organizzazioni IT e le business unit distribuiscono carichi di lavoro aziendali sul cloud, devono bilanciare la governance con la flessibilità degli sviluppatori. Il data center virtuale di Azure offre modelli per raggiungere questo equilibrio, soffermandosi in particolare sulla governance.

## <a name="resources"></a>Risorse

<table>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="https://aka.ms/VDC/Concepts"><img src="../_images/virtual-datacenter.svg" alt="Virtual Datacenter eBook" /></a></td>
    <td>
        <h3><a href="https://aka.ms/VDC/Concepts">Data center virtuale di Azure: concetti</a></h3>
        <p>Questo eBook illustra come distribuire carichi di lavoro aziendali nella piattaforma cloud di Azure, rispettando al tempo stesso i criteri di sicurezza e di rete esistenti.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="/azure/networking/networking-virtual-datacenter"><img src="./images/vdc-network.png" alt="Network Perspective" /></a></td>
    <td>
        <h3><a href="networking-virtual-datacenter.md">Data center virtuale di Azure: una prospettiva di rete</a></h3>
        <p>Questo articolo online offre una panoramica di modelli e progettazioni di rete che consentono di risolvere le problematiche di sicurezza, prestazioni e scalabilità a livello di architettura affrontate da molti clienti che stanno considerando il passaggio al cloud in massa.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="https://aka.ms/VDC/Lift"><img src="./images/vdc-lift-and-shift.png" alt="Lift and Shift Guide" /></a></td>
    <td>
        <h3><a href="https://aka.ms/VDC/Lift">Data center virtuale di Azure: guida al trasferimento in modalità lift-and-shift</a></h3>
        <p>Questo white paper illustra il processo che il personale IT e i decisori aziendali possono usare per identificare e pianificare la migrazione delle applicazioni e dei server in Azure servendosi del metodo lift and shift, riducendo al minimo eventuali costi di sviluppo aggiuntivi, ottimizzando al tempo stesso le opzioni di hosting su cloud.</p>
    </td>
</tr>
<tr>
    <td style="width: 64px; vertical-align: middle;"><a href="https://aka.ms/VDC/Deck"><img src="./images/vdc-deck.png" alt="Presentation Deck" /></a></td>
    <td>
        <h3><a href="https://aka.ms/VDC/Deck">Data center virtuale di Azure: Presentation Deck </a></h3>
        <p>Questo gruppo di presentazioni illustra gli strumenti e le linee guida del data center virtuale di Azure. Descrive gli obiettivi del data center virtuale, i driver di clienti, le aree di Azure, gli elementi di automazione di un data center virtuale, i data center virtuali di Azure più industrializzati e attendibili e termina con un piano di azione sulle linee guida del CIO. Contiene anche informazioni di supporto tecnico e formazione.</p>
    </td>
</tr>
</table>

## <a name="what-is-the-azure-virtual-datacenter"></a>Definizione di data center virtuale di Azure

La distribuzione dei carichi di lavoro nel cloud introduce la necessità di sviluppare e mantenere l'attendibilità del cloud allo stesso livello di quella dei data center esistenti. Il primo modello di linee guida per il data center virtuale di Azure è progettato per soddisfare questa necessità tramite un approccio bloccato alle infrastrutture virtuali. Questo approccio non è adatto a tutti gli utenti. In particolare è progettato per guidare i gruppi IT aziendali nell'estensione della propria infrastruttura locale al cloud pubblico di Azure. Questo approccio viene definito modello di estensione attendibile del data center. Nel corso del tempo verranno proposti molti altri modelli, inclusi quelli che consentono un accesso sicuro a Internet direttamente da un data center virtuale.

<img src="./images/vdc-components.svg" alt="Virtual Datacenter components" style="max-width:700px;"/>

Il data center virtuale di Azure è possibile grazie a questi quattro componenti: identità, crittografia, rete software-defined e conformità, inclusi i log e la creazione di report.

Nel modello di data center virtuale di Azure è possibile applicare i criteri di isolamento, rendere il cloud più simile ai data center fisici conosciuti e raggiungere i livelli di sicurezza e attendibilità necessari. Tutto ciò è realizzabile grazie a quattro componenti che qualsiasi team IT aziendale riconoscerebbe: rete software-defined, crittografia, gestione dell'identità e standard e certificazioni di conformità alla base della piattaforma di Azure. Queste quattro componenti sono fondamentali per fare in modo che un data center virtuale diventi un'estensione attendibile degli investimenti dell'infrastruttura esistente.

Continuare a leggere l'eBook sui <a href="https://aka.ms/VDC/eBook">concetti del data center virtuale di Azure</a>.
