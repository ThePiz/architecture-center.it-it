---
title: 'CAF: Guida decisionale di imposizione dei criteri'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sulle sottoscrizioni di imposizione dei criteri come priorità di progettazione principale nelle migrazioni di Azure.
author: rotycenh
ms.openlocfilehash: 372926453ee4ae0502250e9b69fe8a0ea94f0ffe
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901311"
---
# <a name="policy-enforcement-decision-guide"></a>Guida decisionale di imposizione dei criteri

La definizione di criteri dell'organizzazione non è efficace a meno che non ci sia un modo di applicarla all'interno dell'organizzazione. Un aspetto fondamentale per la pianificazione di qualsiasi migrazione cloud consiste nel determinare il modo migliore di combinare gli strumenti implementati dalla piattaforma cloud con i processi IT esistenti, per ottimizzare la conformità ai criteri dell'intero gruppo di cloud.

![Grafico delle opzioni di imposizione dei criteri dalla meno alla più complessa, allineato con i collegamenti sotto](../../_images/discovery-guides/discovery-guide-policy-enforcement.png)

Passare a: [Procedure di base consigliate](#baseline-recommended-practices) | [Monitoraggio della conformità ai criteri](#policy-compliance-monitoring) | [Imposizione dei criteri](#policy-enforcement) | [Criteri tra organizzazioni](#cross-organization-policy) | [Imposizione automatizzata](#automated-enforcement)

All'aumentare dei cloud si avrà di conseguenza la necessità di mantenere e applicare criteri a un array più grande di risorse, sottoscrizioni e tenant. Più un estate è grande, più i meccanismi di imposizione dovranno essere complessi per garantire una conformità coerente e un rilevamento veloce delle violazioni. I meccanismi di imposizione dei criteri implementati dalla piattaforma a livello di risorse o sottoscrizione sono in genere sufficienti per distribuzioni cloud più piccole, mentre per le distribuzioni più grandi potrebbe essere necessario sfruttare i vantaggi dei meccanismi più sofisticati che interessano gli standard di distribuzione, il raggruppamento di risorse, l'organizzazione e l'integrazione di imposizione dei criteri con i sistemi di log e di report.

Il punto critico nella scelta della complessità di una strategia di imposizione dei criteri consiste principalmente nel numero di sottoscrizioni o di tenant richiesti dalla [progettazione di una sottoscrizione](../subscriptions/overview.md). Il grado di controllo concesso ai diversi ruoli utente all'interno di un gruppo di cloud potrebbe influenzare anche queste decisioni.

## <a name="baseline-recommended-practices"></a>Procedure di base consigliate

Per le distribuzioni cloud semplici e le sottoscrizioni singole, è possibile applicare molti criteri aziendali usando funzioni native alla maggior parte delle piattaforme cloud. Anche in questo livello relativamente basso di complessità di distribuzione, l'uso coerente dei modelli illustrati in tutte le [guide decisionali](../overview.md) del CAF contribuisce a stabilire un livello di conformità ai criteri di base.

Ad esempio: 

- [I modelli di distribuzione](../resource-consistency/overview.md) possono effettuare il provisioning delle risorse con struttura e configurazione standardizzate.
- [L'assegnazione di tag e standard di denominazione](../resource-tagging/overview.md) consente di organizzare le operazioni e supportare i requisiti di business e dell'account.
- Le restrizioni di rete e la gestione del traffico possono essere implementate grazie a [reti definite dal software](../software-defined-network/overview.md).
- Il [controllo degli accessi in base al ruolo](../identity/overview.md) consente di proteggere e isolare le risorse cloud.

La pianificazione dell'imposizione dei criteri del cloud si avvia esaminando come l'applicazione dei modelli standard descritti nel corso di queste guide consenta di soddisfare i requisiti dell'organizzazione.

## <a name="policy-compliance-monitoring"></a>Monitoraggio della conformità ai criteri

Un altro fattore importante, anche per le distribuzioni cloud di dimensioni relativamente ridotte, è la capacità di verificare che i servizi e le applicazioni basate su cloud siano conformi ai criteri dell'organizzazione, avvisando tempestivamente i responsabili se una risorsa diventasse non conforme. La [creazione di log e report](../log-and-report/overview.md) efficaci sullo stato di conformità dei carichi di lavoro nel cloud è una parte essenziale della strategia di imposizione dei criteri aziendali.

Se un gruppo di cloud aumenta, strumenti aggiuntivi, ad esempio il [Centro sicurezza di Azure](/azure/security-center/), garantiscono la sicurezza integrata e il rilevamento delle minacce e consentono di applicare la gestione centralizzata dei criteri e degli avvisi per entrambe le risorse locali e cloud.

## <a name="policy-enforcement"></a>Imposizione dei criteri

È inoltre possibile applicare le impostazioni di configurazione e le regole per la creazione di risorse a livello di sottoscrizione per assicurare l'allineamento dei criteri.

[Criteri di Azure](/azure/governance/policy/overview) è un servizio di Azure per la creazione, l'assegnazione e la gestione dei criteri. Questi criteri applicano regole ed effetti diversi alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda. Criteri di Azure valuta le risorse per la mancata conformità con i criteri assegnati. Ad esempio, è possibile limitare le dimensioni SKU di macchine virtuali nell'ambiente in uso. Dopo aver implementato i criteri corrispondenti, le risorse nuove ed esistenti vengono valutate per la conformità. Con i criteri corretti, le risorse esistenti possono essere rese conformi.

## <a name="cross-organization-policy"></a>Criteri tra organizzazioni

Se il gruppo di cloud aumenta estendendosi su più sottoscrizioni che richiedono un'imposizione, è necessario concentrarsi su una strategia per l'applicazione a livello di tenant per garantire la coerenza dei criteri.

La [progettazione di una sottoscrizione](../subscriptions/overview.md) dovrà prendere in considerazione i criteri relativi alla struttura dell'organizzazione. Oltre ad aiutare a supportare un'organizzazione complessa all'interno della progettazione di una sottoscrizione, i [gruppi di gestione di Azure](../subscriptions/overview.md#management-groups) possono essere usati per assegnare le regole dei criteri di Azure tra più sottoscrizioni.

## <a name="automated-enforcement"></a>Imposizione automatizzata

Anche se i modelli di distribuzione sono validi in scala ridotta, [Azure Blueprints](/azure/governance/blueprints/overview) consente di effettuare il provisioning e l'orchestrazione della distribuzione su larga scala e in maniera standard delle soluzioni di Azure. I carichi di lavoro tra più sottoscrizioni possono essere distribuiti con impostazioni dei criteri coerenti per tutte le risorse create.

Per gli ambienti IT che integrano risorse cloud e locali, potrebbe essere necessario usare sistemi di log e di report per offrire funzionalità di monitoraggio ibrido. I sistemi di monitoraggio operativi di terze parti o personalizzati possono offrire funzionalità aggiuntive di imposizione dei criteri. Per gruppi di cloud più complessi, prendere in considerazione il modo migliore per integrare i sistemi con le risorse cloud.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare la coerenza delle risorse per organizzare e standardizzare le distribuzioni cloud per supportare la progettazione della sottoscrizione e gli obiettivi di governance.

> [!div class="nextstepaction"]
> [Coerenza delle risorse](../resource-consistency/overview.md)
