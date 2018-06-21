---
title: Scegliere una soluzione per la connessione di una rete locale ad Azure.
description: Confrontare le architetture di riferimento per connettersi a una rete locale in Azure.
author: telmosampaio
ms.date: 04/06/2017
ms.openlocfilehash: 274b9df1817632a7f3eaafa8bf02e965fdc3feea
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
ms.locfileid: "24541050"
---
# <a name="choose-a-solution-for-connecting-an-on-premises-network-to-azure"></a>Scegliere una soluzione per la connessione di una rete locale ad Azure.

In questo articolo vengono confrontate le opzioni per la connessione di una rete locale a una rete virtuale di Azure (VNet). Viene fornita un'architettura di riferimento e una soluzione distribuibile per ogni opzione.

## <a name="vpn-connection"></a>Connessione VPN

Usare una rete privata virtuale (VPN) per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPSec.

Questa architettura è adatta ad applicazioni ibride in cui è probabile che il traffico tra l'hardware locale e il cloud sia leggero o se si vuole scambiare la latenza leggermente estesa con la flessibilità e la potenza di elaborazione del cloud.

**Vantaggi**

- Semplice da configurare.

**Problematiche**

- Richiede un dispositivo VPN locale.
- Nonostante Microsoft garantisca il 99,9% di disponibilità per ogni Gateway VPN, il contratto di servizio copre esclusivamente il gateway VPN e non la connessione di rete per il gateway.
- Attualmente una connessione VPN tramite il Gateway VPN di Azure supporta una larghezza di banda per un massimo di 200 Mbps. Potrebbe essere necessario partizionare la rete virtuale di Azure tra più connessioni VPN se si prevede di superare la velocità effettiva.

**[Altre informazioni...][vpn]**

## <a name="azure-expressroute-connection"></a>Connessione ExpressRoute di Azure

Le connessioni ExpressRoute usano una connessione privata dedicata tramite un provider di connettività di terze parti. La connessione privata estende la rete locale in Azure. 

Questa architettura è adatta ad applicazioni ibride che eseguono carichi di lavoro critici su larga scala e che richiedono un livello di scalabilità elevato. 

**Vantaggi**

- Disponibilità di una maggiore larghezza di banda, che può arrivare fino a 10 Gbps a seconda del provider di connettività.
- Supporta la scalabilità dinamica della larghezza di banda per ridurre i costi durante i periodi di minore richiesta. Tuttavia, non tutti i provider di connettività dispongono di questa opzione.
- Può consentire all'organizzazione l'accesso diretto ai cloud nazionali, a seconda del provider di connettività.
- Offre il 99,9% di disponibilità del contratto di servizio attraverso l'intera stringa di connessione.

**Problematiche**

- La configurazione può risultare complessa. La creazione di una connessione ExpressRoute richiede l'uso di un provider di connettività di terze parti. Il provider è responsabile del provisioning della connessione di rete.
- Richiede un router locale a larghezza di banda elevata.

**[Altre informazioni...][expressroute]**

## <a name="expressroute-with-vpn-failover"></a>ExpressRoute con failover VPN

Questa opzione risulta essere una combinazione delle due precedenti, vale a dire l'uso di ExpressRoute in condizioni normali, ma l'esecuzione del failover in una connessione VPN se vi è una perdita di connettività del circuito ExpressRoute.

Questa architettura è adatta ad applicazioni ibride che necessitano di una larghezza di banda di ExpressRoute maggiore, oltre a una connettività di rete a disponibilità elevata. 

**Vantaggi**

- Disponibilità elevata se il circuito ExpressRoute ha esito negativo, anche se la connessione fallback si trova in una rete a larghezza di banda inferiore.

**Problematiche**

- Complesso da configurare. È necessario configurare sia una connessione VPN sia un circuito ExpressRoute.
- Richiede sia un hardware ridondante (dispositivi VPN) che una connessione Gateway VPN di Azure ridondante, entrambi soggetti a tariffa.

**[Altre informazioni...][expressroute-vpn-failover]**

<!-- links -->
[expressroute]: ./expressroute.md
[expressroute-vpn-failover]: ./expressroute-vpn-failover.md
[vpn]: ./vpn.md