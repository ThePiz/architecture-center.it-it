---
title: 'CAF: Reti definite dal software - Reti perimetrali nel cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Questa architettura di rete consente un accesso limitato fra la rete locale e quella basata sul cloud
author: rotycenh
ms.openlocfilehash: a192541dcfb0f3d713f4139a2ab0541d0c7202db
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901668"
---
# <a name="software-defined-networks-cloud-dmz"></a>Reti definite dal software: Reti perimetrali nel cloud

L'architettura di rete perimetrale nel cloud consente un accesso limitato fra la rete locale e quella basata sul cloud, usando una rete privata virtuale (VPN) per connettere le reti. Una rete perimetrale viene distribuita nel cloud per proteggere l'accesso alla rete locale da risorse basate sul cloud.

![Architettura di rete ibrida sicura](../../../reference-architectures/dmz/images/dmz-private.png)

Questa architettura è progettata per supportare scenari in cui l'organizzazione vuole iniziare a integrare carichi di lavoro basati sul cloud con carichi di lavoro locali, ma potrebbe non aver ancora maturato completamente i criteri di sicurezza nel cloud o acquisito una connessione WAN dedicata sicura fra i due ambienti. Di conseguenza le reti cloud devono essere trattate come una rete perimetrale per garantire la sicurezza dei servizi locali.

La rete perimetrale distribuisce appliance virtuali di rete per implementare funzionalità di sicurezza quali firewall e ispezione dei pacchetti. Il traffico che passa tra le applicazioni o i servizi locali e quelli basati sul cloud deve passare attraverso la rete perimetrale, dove può essere controllato. Le connessioni VPN e le regole che determinano il traffico consentito attraverso la rete perimetrale sono rigidamente controllate dai team di sicurezza IT.

## <a name="cloud-dmz-assumptions"></a>Presupposti relativi alle reti perimetrali nel cloud

La distribuzione di una rete perimetrale nel cloud presuppone le condizioni seguenti:

- I team di sicurezza non hanno allineato completamente i requisiti e i criteri di sicurezza locali e basati sul cloud.
- I carichi di lavoro basati sul cloud richiedono accesso limitato ai servizi ospitati nelle reti locali o di terze parti oppure gli utenti o le applicazioni nell'ambiente locale hanno bisogno di accesso limitato alle risorse ospitate nel cloud.
- L'implementazione di una connessione VPN tra le reti locali e un provider di servizi cloud non è impedita da criteri aziendali, requisiti normativi o problemi di compatibilità tecnica.
- I carichi di lavoro non richiedono più sottoscrizioni per ignorare i limiti delle risorse di sottoscrizione oppure comportano più sottoscrizioni, ma non richiedono la gestione centrale della connettività o dei servizi condivisi usati dalle risorse distribuite in più sottoscrizioni.

Il team di adozione del cloud deve tenere presenti i problemi seguenti relativamente all'implementazione di un'architettura di rete virtuale basata su rete perimetrale nel cloud:

- La connessione di reti locali a reti cloud aumenta la complessità dei requisiti di sicurezza. Anche se le connessioni tra reti cloud e ambiente locale sono protette, è sempre necessario assicurarsi che le risorse cloud siano protette.
- L'architettura di rete perimetrale nel cloud viene generalmente usata come tappa in attesa che la connettività venga ulteriormente protetta e i criteri di sicurezza tra le reti locale e cloud vengano allineati, consentendo una più ampia adozione di un'architettura di rete ibrida completa.

## <a name="learn-more"></a>Altre informazioni

Vedere il collegamento seguente per altre informazioni sull'implementazione di una rete perimetrale nel cloud nella piattaforma Azure.

- [Implementare una rete perimetrale tra Azure e il data center locale](../../../reference-architectures/dmz/secure-vnet-hybrid.md). Questo articolo illustra come implementare un'architettura di rete ibrida sicura in Azure.
