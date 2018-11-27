---
title: Scegliere una soluzione per la connessione di una rete locale ad Azure.
description: Confrontare le architetture di riferimento per connettersi a una rete locale in Azure.
author: telmosampaio
ms.date: 07/02/2018
ms.openlocfilehash: a9e2a212d65530e714635bbfae3a57766e77c3a6
ms.sourcegitcommit: 19a517a2fb70768b3edb9a7c3c37197baa61d9b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52295488"
---
# <a name="connect-an-on-premises-network-to-azure"></a>Connettere una rete locale ad Azure

In questo articolo vengono confrontate le opzioni per la connessione di una rete locale a una rete virtuale di Azure (VNet). Per ogni opzione è disponibile un'architettura di riferimento più dettagliata.

## <a name="vpn-connection"></a>Connessione VPN

Un [gateway VPN](/azure/vpn-gateway/vpn-gateway-about-vpngateways) è un tipo di gateway di rete virtuale che invia traffico crittografato tra una rete virtuale di Azure e una posizione locale. Il traffico crittografato passa sulla rete Internet pubblica.

Questa architettura è adatta ad applicazioni ibride in cui è probabile che il traffico tra l'hardware locale e il cloud sia leggero o se si vuole scambiare la latenza leggermente estesa con la flessibilità e la potenza di elaborazione del cloud.

**Vantaggi**

- Semplice da configurare.

**Problematiche**

- Richiede un dispositivo VPN locale.
- Nonostante Microsoft garantisca il 99,9% di disponibilità per ogni gateway VPN, il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) copre esclusivamente il gateway VPN e non la connessione di rete per il gateway.
- Una connessione VPN tramite Gateway VPN di Azure supporta attualmente una larghezza di banda massima di 1,25 Gbps. Potrebbe essere necessario partizionare la rete virtuale di Azure tra più connessioni VPN se si prevede di superare la velocità effettiva.

**Architettura di riferimento**

- [Rete ibrida con gateway VPN](./vpn.md)

## <a name="azure-expressroute-connection"></a>Connessione ExpressRoute di Azure

Le [connessioni ExpressRoute](/azure/expressroute/) usano una connessione dedicata privata tramite un provider di connettività di terze parti. La connessione privata estende la rete locale in Azure. 

Questa architettura è adatta ad applicazioni ibride che eseguono carichi di lavoro critici su larga scala e che richiedono un livello di scalabilità elevato. 

**Vantaggi**

- Disponibilità di una maggiore larghezza di banda, che può arrivare fino a 10 Gbps a seconda del provider di connettività.
- Supporta la scalabilità dinamica della larghezza di banda per ridurre i costi durante i periodi di minore richiesta. Tuttavia, non tutti i provider di connettività dispongono di questa opzione.
- Può consentire all'organizzazione l'accesso diretto ai cloud nazionali, a seconda del provider di connettività.
- Offre il 99,9% di disponibilità del contratto di servizio attraverso l'intera stringa di connessione.

**Problematiche**

- La configurazione può risultare complessa. La creazione di una connessione ExpressRoute richiede l'uso di un provider di connettività di terze parti. Il provider è responsabile del provisioning della connessione di rete.
- Richiede un router locale a larghezza di banda elevata.

**Architettura di riferimento**

- [Rete ibrida con ExpressRoute](./expressroute.md)

## <a name="expressroute-with-vpn-failover"></a>ExpressRoute con failover VPN

Questa opzione risulta essere una combinazione delle due precedenti, vale a dire l'uso di ExpressRoute in condizioni normali, ma l'esecuzione del failover in una connessione VPN se vi è una perdita di connettività del circuito ExpressRoute.

Questa architettura è adatta ad applicazioni ibride che necessitano di una larghezza di banda di ExpressRoute maggiore, oltre a una connettività di rete a disponibilità elevata. 

**Vantaggi**

- Disponibilità elevata se il circuito ExpressRoute ha esito negativo, anche se la connessione fallback si trova in una rete a larghezza di banda inferiore.

**Problematiche**

- Complesso da configurare. È necessario configurare sia una connessione VPN sia un circuito ExpressRoute.
- Richiede sia un hardware ridondante (dispositivi VPN) che una connessione Gateway VPN di Azure ridondante, entrambi soggetti a tariffa.

**Architettura di riferimento**

- [Rete ibrida con ExpressRoute e failover VPN](./expressroute-vpn-failover.md)


## <a name="hub-spoke-network-topology"></a>Topologia di rete hub-spoke

Una topologia di rete hub-spoke è un modo per isolare i carichi di lavoro durante la condivisione di servizi quali identità e sicurezza. L'hub è una rete virtuale in Azure che funge da punto centrale di connettività alla rete locale. Gli spoke sono reti virtuali che eseguono il peering con l'hub. I servizi condivisi vengono distribuiti nell'hub, mentre i singoli carichi di lavoro vengono distribuiti come spoke.


**Architetture di riferimento**

- [Topologia hub-spoke](./hub-spoke.md)
- [Hub-spoke con servizi condivisi](./shared-services.md)
