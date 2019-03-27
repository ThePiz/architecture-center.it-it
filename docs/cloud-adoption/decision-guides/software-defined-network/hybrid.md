---
title: 'CAF: Reti definite dal software - Reti ibride'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Discussione sul modo in cui le reti ibride consentono alle reti virtuali del cloud di connettersi alle risorse locali
author: rotycenh
ms.openlocfilehash: 02d181db0ae9baef3b453b8623d212b624f6b16a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243362"
---
# <a name="software-defined-networks-hybrid-network"></a>Reti definite dal software: Rete ibrida

L'architettura di rete del cloud ibrido consente alle reti virtuali di accedere a risorse locali e servizi e vice versa usando una connessione WAN dedicata, ad esempio ExpressRoute o un altro metodo di connessione per connettersi direttamente alle reti.

![Rete ibrida](../../../reference-architectures/hybrid-networking/images/expressroute.png)

Durante la compilazione dell'architettura di rete virtuale nativa del cloud, viene isolata una rete virtuale ibrida al momento della creazione. L'aggiunta della connettività all'ambiente locale concede l'accesso da e verso la rete locale, anche se è necessario consentire in modo esplicito tutte le altre risorse di destinazione di traffico in ingresso nella rete virtuale. È possibile proteggere la connessione tramite i dispositivi virtuali firewall e le regole di gestione per limitare l'accesso oppure specificare esattamente quali servizi possono essere accessibili tra le due reti, usando le funzionalità di routing nativo del cloud o la distribuzione di appliance virtuali di rete (NVA) per la gestione del traffico.

Anche se l'architettura di rete ibrida supporta le connessioni VPN, le connessioni WAN dedicate, ad esempio ExpressRoute, sono in genere preferibili grazie alle prestazioni più elevate e a una maggiore sicurezza.

## <a name="hybrid-assumptions"></a>Presupposti della connessione ibrida

La distribuzione di una rete virtuale ibrida presuppone quanto segue:

- I team della sicurezza IT si sono allineati ai criteri di sicurezza di rete locale e basata su cloud, per garantire che le reti virtuali basate su cloud possano essere considerate attendibili per comunicare direttamente con i sistemi locali.
- I carichi di lavoro basati su cloud richiedono l'accesso alle risorse di archiviazione, alle applicazioni e ai servizi ospitati sulle reti locali o di terze parti, oppure agli utenti e alle applicazioni in locale che devono accedere alle risorse ospitate nel cloud.
- È necessario eseguire la migrazione delle applicazioni e dei servizi esistenti che dipendono da risorse locali, ma non si vogliono impiegare le risorse in un nuovo sviluppo per rimuovere tali dipendenze.
- L'implementazione di una VPN o di una connessione WAN dedicata tra le reti locali e un provider di servizi cloud non è impedita da criteri aziendali, requisiti normativi o problemi di compatibilità tecnica.
- I carichi di lavoro non richiedono più sottoscrizioni per ignorare i limiti delle risorse di sottoscrizione OPPURE i carichi di lavoro comportano più sottoscrizioni, ma non richiedono la gestione centrale della connettività o i servizi di condivisione usati dalle risorse distribuite in più sottoscrizioni.

Il team di adozione del cloud deve tenere presente i problemi seguenti, quando si esamina l'implementazione di un'architettura di rete virtuale ibrida:

- La connessione di reti locali a reti cloud aumenta la complessità dei requisiti di sicurezza. Entrambe le reti devono essere protette contro le vulnerabilità esterne e gli accessi non autorizzati da entrambi i lati dell'ambiente ibrido.
- Il ridimensionamento del numero e delle dimensioni di carichi di lavoro all'interno di un ambiente cloud ibrido può implicare una complessità significativa nella gestione di routing e traffico.
- Sarà necessario sviluppare criteri di controllo di accesso e di gestione compatibili per mantenere la governance coerente in tutta l'organizzazione.

## <a name="learn-more"></a>Altre informazioni

Vedere gli argomenti seguenti per altre informazioni sulle reti ibride nella piattaforma Azure.

- [Architetture di riferimento della rete ibrida di Azure](../../../reference-architectures/hybrid-networking/expressroute.md). Le reti virtuali ibride di Azure usano entrambe un circuito ExpressRoute o VPN di Azure per connettere la rete virtuale alle esistenti risorse IT non di Azure ospitate dell'organizzazione. Questo articolo illustra le opzioni per la creazione di una rete ibrida in Azure.
