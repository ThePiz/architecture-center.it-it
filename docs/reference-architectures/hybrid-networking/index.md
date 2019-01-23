---
title: Connettere una rete locale ad Azure
titleSuffix: Azure Reference Architectures
description: Confrontare le architetture di riferimento per connettersi a una rete locale in Azure.
author: telmosampaio
ms.date: 07/02/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: networking
ms.openlocfilehash: 6172866b08197b0ca1cd3aabb3c14c01b4f06f9c
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486850"
---
# <a name="choose-a-solution-for-connecting-an-on-premises-network-to-azure"></a>Scegliere una soluzione per la connessione di una rete locale ad Azure.

In questo articolo vengono confrontate le opzioni per la connessione di una rete locale a una rete virtuale di Azure (VNet). Per ogni opzione è disponibile un'architettura di riferimento più dettagliata.

## <a name="vpn-connection"></a>Connessione VPN

Un [gateway VPN](/azure/vpn-gateway/vpn-gateway-about-vpngateways) è un tipo di gateway di rete virtuale che invia traffico crittografato tra una rete virtuale di Azure e una posizione locale. Il traffico crittografato passa sulla rete Internet pubblica.

Questa architettura è adatta ad applicazioni ibride in cui è probabile che il traffico tra l'hardware locale e il cloud sia leggero o se si vuole scambiare la latenza leggermente estesa con la flessibilità e la potenza di elaborazione del cloud.

### <a name="benefits"></a>Vantaggi

- Semplice da configurare.

### <a name="challenges"></a>Problematiche

- Richiede un dispositivo VPN locale.
- Nonostante Microsoft garantisca il 99,9% di disponibilità per ogni gateway VPN, il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) copre esclusivamente il gateway VPN e non la connessione di rete per il gateway.
- Una connessione VPN tramite Gateway VPN di Azure supporta attualmente una larghezza di banda massima di 1,25 Gbps. Potrebbe essere necessario partizionare la rete virtuale di Azure tra più connessioni VPN se si prevede di superare la velocità effettiva.

### <a name="reference-architecture"></a>Architettura di riferimento

- [Rete ibrida con gateway VPN](./vpn.md)

<!-- markdownlint-disable MD024 -->

## <a name="azure-expressroute-connection"></a>Connessione ExpressRoute di Azure

Le [connessioni ExpressRoute](/azure/expressroute/) usano una connessione dedicata privata tramite un provider di connettività di terze parti. La connessione privata estende la rete locale in Azure.

Questa architettura è adatta ad applicazioni ibride che eseguono carichi di lavoro critici su larga scala e che richiedono un livello di scalabilità elevato.

### <a name="benefits"></a>Vantaggi

- Disponibilità di una maggiore larghezza di banda, che può arrivare fino a 10 Gbps a seconda del provider di connettività.
- Supporta la scalabilità dinamica della larghezza di banda per ridurre i costi durante i periodi di minore richiesta. Tuttavia, non tutti i provider di connettività dispongono di questa opzione.
- Può consentire all'organizzazione l'accesso diretto ai cloud nazionali, a seconda del provider di connettività.
- Offre il 99,9% di disponibilità del contratto di servizio attraverso l'intera stringa di connessione.

### <a name="challenges"></a>Problematiche

- La configurazione può risultare complessa. La creazione di una connessione ExpressRoute richiede l'uso di un provider di connettività di terze parti. Il provider è responsabile del provisioning della connessione di rete.
- Richiede un router locale a larghezza di banda elevata.

### <a name="reference-architecture"></a>Architettura di riferimento

- [Rete ibrida con ExpressRoute](./expressroute.md)

## <a name="expressroute-with-vpn-failover"></a>ExpressRoute con failover VPN

Questa opzione risulta essere una combinazione delle due precedenti, vale a dire l'uso di ExpressRoute in condizioni normali, ma l'esecuzione del failover in una connessione VPN se vi è una perdita di connettività del circuito ExpressRoute.

Questa architettura è adatta ad applicazioni ibride che necessitano di una larghezza di banda di ExpressRoute maggiore, oltre a una connettività di rete a disponibilità elevata.

### <a name="benefits"></a>Vantaggi

- Disponibilità elevata se il circuito ExpressRoute ha esito negativo, anche se la connessione fallback si trova in una rete a larghezza di banda inferiore.

### <a name="challenges"></a>Problematiche

- Complesso da configurare. È necessario configurare sia una connessione VPN sia un circuito ExpressRoute.
- Richiede sia un hardware ridondante (dispositivi VPN) che una connessione Gateway VPN di Azure ridondante, entrambi soggetti a tariffa.

### <a name="reference-architecture"></a>Architettura di riferimento

- [Rete ibrida con ExpressRoute e failover VPN](./expressroute-vpn-failover.md)

<!-- markdownlint-disable MD024 -->

## <a name="hub-spoke-network-topology"></a>Topologia di rete hub-spoke

Una topologia di rete hub-spoke è un modo per isolare i carichi di lavoro durante la condivisione di servizi quali identità e sicurezza. L'hub è una rete virtuale in Azure che funge da punto centrale di connettività alla rete locale. Gli spoke sono reti virtuali che eseguono il peering con l'hub. I servizi condivisi vengono distribuiti nell'hub, mentre i singoli carichi di lavoro vengono distribuiti come spoke.

### <a name="reference-architectures"></a>Architetture di riferimento

- [Topologia hub-spoke](./hub-spoke.md)
- [Hub-spoke con servizi condivisi](./shared-services.md)
