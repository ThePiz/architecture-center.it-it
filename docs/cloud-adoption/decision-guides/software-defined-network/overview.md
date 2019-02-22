---
title: 'CAF: Reti definite dal software'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Discussione su reti definite dal software come un servizio di base nelle migrazioni Azure
author: rotycenh
ms.openlocfilehash: d164ba488552715dc97719329ae9de3fcf5d83ed
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901916"
---
# <a name="caf-software-defined-network-decision-guide"></a>CAF: Guida decisionale per la rete definita dal software

La rete definita dal software (SDN, Software Defined Networking) è un'architettura di rete progettata per consentire funzionalità di rete virtualizzata che possono essere gestite, configurate e modificate in modo centralizzato tramite software. La tecnologia SDN offre un livello di astrazione sull'infrastruttura di rete fisica e abilitagli equivalenti virtuali a di router, firewall e altri componenti hardware di rete fisici che si trovano in una rete locale.

L'SDN consente al personale IT di configurare e distribuire strutture di rete e funzionalità che supportano le esigenze del carico di lavoro usando risorse virtualizzate. La flessibilità della gestione della distribuzione basata su software consente rapide modifiche delle risorse di rete e può supportare entrambi i modelli di distribuzione, tradizionale e agile. Le reti virtualizzate create con tecnologia SDN sono fondamentali per creare reti sicure su una piattaforma di cloud pubblico.

## <a name="networking-decision-guide"></a>Guida decisionale di rete

![Grafico delle opzioni di rete dalla meno alla più complessa, allineato con i collegamenti sotto](../../_images/discovery-guides/discovery-guide-sdn.png)

Passare a: [Solo PaaS](paas-only.md) | [Reti cloud native](cloud-native.md) | | [Reti perimetrali nel cloud](cloud-dmz.md) [Ibrido](hybrid.md) | [Modello hub-spoke](hub-spoke.md) | [Altre informazioni](#learn-more)

LSDN offre diverse opzioni con vari livelli di prezzo e complessità. La guida di individuazione sopra riportata rappresenta un riferimento per personalizzare rapidamente queste opzioni per allinearsi al meglio con le specifiche strategie aziendali e tecnologiche.

Il punto critico in questa guida dipende da diverse decisioni chiave prese dal team per la strategia cloud prima di prendere altre sull'architettura di rete. Le più importanti tra queste sono le decisioni riguardanti la [definizione del digital estate](../../digital-estate/overview.md) e la [progettazione della sottoscrizione](../subscriptions/overview.md) (per cui possono essere necessari anche input da decisioni prese correlate alla contabilità cloud e alle strategie dei mercati globali).

Le distribuzioni di aree singole di piccole dimensioni di meno di 1.000 macchine virtuali hanno meno probabilità di essere influenzate in modo significativo da questo punto critico. Al contrario, il grande lavoro di adozione richiesto con più di 1.000 macchine virtuali, più business unit o più mercati geo-politici può essere influenzato notevolmente dalla scelta della SDN e da questo punto critico.

## <a name="choosing-the-right-virtual-networking-architectures"></a>Scegliere le architetture di rete virtuale appropriate

Questa sezione sviluppa la guida decisionale per aiutare a scegliere le architetture di rete virtuale appropriate.

Esistono diversi modi per implementare le tecnologie SDN per creare reti virtuali basate su cloud. Il modo in cui le reti virtuali usate nella migrazione si strutturano e interagiscono con l'infrastruttura IT esistente dipende da una combinazione dei requisiti del carico di lavoro e della governance.

Quando si pianifica quale architettura di rete virtuale o combinazione di architetture considerare durante la pianificazione della migrazione cloud, bisogna considerare le domande seguenti per determinare quale sia quella più appropriata per l'azienda:

| Domanda | Solo PaaS | Reti cloud native | Reti perimetrali nel cloud | Ibrido | Modello hub-spoke |
|-----|-----|-----|-----|-----|-----|
| Il carico di lavoro usa solo i servizi PaaS e non richiede funzionalità di rete oltre a quelle fornite dagli stessi servizi? | Sì | No  | No  | No  | No  |
| Il carico di lavoro richiede l'integrazione con applicazioni locali? | No  | No  | Sì | Sì | Sì |
| Sono stati stabiliti criteri di sicurezza avanzati e connettività sicura tra le reti locali e cloud? | No  | No  | No  | Sì | Sì |
| Il carico di lavoro richiede servizi di autenticazione non supportati tramite i servizi di identità cloud oppure serve un accesso diretto ai controller di dominio locali? | No  | No  | No  | Sì | Sì |
| È necessario distribuire e gestire un numero elevato di macchine virtuali e di carichi di lavoro? | No  | No  | No  | No  | Sì |
| È necessario fornire gestione centralizzata e connettività locale in fase di delega controllo sulle risorse ai singoli team del carico di lavoro? | No  | No  | No  | No  | Sì |

## <a name="virtual-networking-architectures"></a>Architetture di rete virtuale

Altre informazioni sulle architetture di rete primarie definite dal software:

- [**Solo PaaS**](paas-only.md): i prodotti PaaS (piattaforma distribuita come servizio) supportano un set limitato di funzionalità di rete predefinite e può non essere necessaria una rete definita dal software definita in modo esplicito per supportare i requisiti del carico di lavoro.
- [**Reti cloud native**](cloud-native.md): una rete virtuale cloud nativa è l'architettura di rete definita dal software predefinita quando si distribuiscono risorse a una piattaforma cloud.
- [**Reti perimetrali nel cloud**](cloud-dmz.md): forniscono connettività limitata tra la rete locale e quella cloud protetta tramite l'implementazione di una zona demilitarizzata nell'ambiente cloud.
- [**Ibrido**](hybrid.md): l'architettura di rete del cloud ibrido consente alle reti virtuali di accedere alle risorse in locale e viceversa.
- [**Modello hub-spoke**](hub-spoke.md): l'architettura hub-spoke consente di gestire la connettività esterna e i servizi condivisi, di isolare singoli carichi di lavoro e di superare i limiti potenziali della sottoscrizione in modo centralizzato.

## <a name="learn-more"></a>Altre informazioni

Vedere gli argomenti seguenti per altre informazioni sulle reti definite dal software nella piattaforma Azure.

- [Rete virtuale di Azure](/azure/virtual-network/virtual-networks-overview). In Azure la funzionalità chiave di SDN viene fornita dalla rete virtuale di Azure che agisce come un cloud analogo alle reti locali fisiche. Le reti virtuali fungono anche da limite di isolamento predefinito tra le risorse della piattaforma.
- [Procedure consigliate per la sicurezza della rete di Azure](/azure/security/azure-security-network-security-best-practices). Indicazioni del team di sicurezza di Azure su come configurare le reti virtuali per ridurre al minimo le vulnerabilità di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come log, monitoraggio e report vengono usati dai team di operazioni per gestire l'integrità e la conformità dei criteri dei carichi di lavoro cloud.

> [!div class="nextstepaction"]
> [Log e report](../log-and-report/overview.md)
