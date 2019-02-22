---
title: "CAF: Guida alle decisioni relative ai tag e all'organizzazione delle risorse"
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sull'organizzazione e sui tag delle risorse come servizio di base nelle migrazioni di Azure.
author: rotycenh
ms.openlocfilehash: 0da9f698fc7ac2db876409ad451adf7b12cf6e6b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901392"
---
# <a name="resource-organization-and-tagging-decision-guide"></a>Guida alle decisioni relative ai tag e all'organizzazione delle risorse

L'organizzazione delle risorse basate sul cloud è una delle attività più importanti per l'IT, a meno che le distribuzioni non siano molto semplici. L'organizzazione delle risorse ha tre scopi principali:

- **Gestione delle risorse**. I team IT dovranno trovare rapidamente le risorse associate a specifici carichi di lavoro, ambienti, gruppi di proprietà o altre importanti informazioni. L'organizzazione delle risorse è critica per l'assegnazione di ruoli aziendali e di autorizzazioni di accesso per la gestione delle risorse.
- **Operazioni**. Oltre a rendere più semplice per l'IT la gestione delle risorse, uno schema organizzativo appropriato consente di sfruttare i vantaggi dell'automazione come parte della creazione di risorse, del monitoraggio operativo e della creazione di processi DevOps.
- **Accounting**. Per fare in modo che i gruppi aziendali siano a conoscenza dell'utilizzo delle risorse cloud, è necessario che l'IT sappia quali sono le risorse usate dai carichi di lavoro e dai team. Per supportare approcci come l'accounting chargeback e showback, è necessario organizzare le risorse cloud in modo che rispecchino la proprietà e l'utilizzo.

## <a name="tagging-decision-guide"></a>Guida relativa alle decisioni sui tag

![Grafico delle opzioni relative ai tag, dalla meno complessa alla più complessa, allineato con i collegamenti sotto](../../_images/discovery-guides/discovery-guide-tagging.png)

Passare a: [Convenzioni di denominazione di base](#baseline-naming-conventions) | [Modelli di aggiunta di tag alle risorse](#resource-tagging-patterns) | [Criteri di denominazione e di aggiunta di tag](#naming-and-tagging-policy) | [Altre informazioni](#learn-more)

L'approccio ai tag può essere semplice o complesso, con particolare attenzione ad attività che vanno dal supporto dei team IT che gestiscono i carichi di lavoro cloud all'integrazione delle informazioni relative a tutti gli aspetti dell'intera azienda.

Un'attenzione ai tag allineata all'IT ridurrà la complessità del monitoraggio degli asset e renderà molto più semplice prendere decisioni di gestione basate sulla funzionalità e sulla classificazione.

Gli schemi di assegnazione di tag che includono anche criteri non IT potrebbero richiedere molto più tempo per creare standard di assegnazione di tag basati sugli interessi aziendali e per mantenere tali standard nel corso del tempo. Tuttavia, il risultato di questo processo è un sistema di assegnazione di tag che offre maggiori possibilità di tenere conto dei costi e del valore degli asset IT. Questa associazione tra il valore di un asset e il costo operativo è uno dei primi passi da fare per modificare la percezione dell'IT come centro di costo nell'organizzazione.

## <a name="baseline-naming-conventions"></a>Convenzioni di denominazione di base

Una convenzione di denominazione standard è il punto di partenza per organizzare le risorse ospitate nel cloud. Un sistema di denominazione strutturato correttamente consente di identificare rapidamente le risorse a fini di gestione e di accounting. Se in altre parti dell'organizzazione esistono convenzioni di denominazione IT, valutare se le convenzioni di denominazione delle risorse cloud devono essere allineate con queste convenzioni o se è meglio stabilire standard separati basati sul cloud.

Tenere anche presente che i [requisiti di denominazione](../../../best-practices/naming-conventions.md#naming-rules-and-restrictions) sono diversi a seconda dei tipi di risorsa di Azure. Le convenzioni di denominazione devono essere compatibili con questi requisiti di denominazione.

## <a name="resource-tagging-patterns"></a>Modelli di tag delle risorse

Per un'organizzazione più sofisticata di quella che può garantire una convenzione di denominazione coerente, le piattaforme cloud consentono di applicare tag alle risorse.

I *tag* sono elementi dei metadati collegati alle risorse. I tag sono costituiti da coppie di stringhe chiave/valore. I valori inclusi in queste coppie vengono decisi dall'utente, ma l'applicazione di un set coerente di tag globali, nell'ambito dei criteri di denominazione e applicazione di tag completi, è una parte essenziale dei criteri di governance generali.

Di seguito sono riportati alcuni esempi di modelli di tag comuni:

<!-- markdownlint-disable MD033 -->

| Tipo di tag | Esempi | DESCRIZIONE |
|-----|-----|-----|
| Funzionale            | app = catalogsearch1 <br/>tier = web <br/>webserver = apache<br/>env = prod <br/>env = staging <br/>env = dev                 | Classifica le risorse in relazione al loro scopo in un carico di lavoro, all'ambiente in cui sono state distribuite o ad altre funzionalità e dettagli operativi.                                 |
| classificazione        | confidentiality=private<br/>sla = 24hours                                 | Classifica una risorsa in base a come viene usata e ai criteri applicati                               |
| Contabilità            | department = finance <br/>project = catalogsearch <br/>region = northamerica | Consente di associare le risorse a determinati gruppi di un'organizzazione per la fatturazione |
| Collaborazione           | owner = jsmith <br/>contactalias = catsearchowners<br/>stakeholders = user1;user2;user3<br/>                       | Fornisce informazioni sulle persone (al di fuori di IT) correlate o altrimenti interessate dalla risorsa                      |
| Scopo               | businessprocess=support<br/>businessimpact=moderate<br/>revenueimpact=high   | Allinea le risorse alle funzioni aziendali per supportare meglio le decisioni relative agli investimenti  |

<!-- markdownlint-enable MD033 -->

## <a name="naming-and-tagging-policy"></a>Criteri di denominazione e applicazione di tag

I criteri di denominazione e applicazione di tag cambieranno nel tempo. È tuttavia importante stabilire le principali priorità aziendali all'inizio di una migrazione cloud. Durante il processo di pianificazione, considerare attentamente le domande seguenti:

- Qual è il modo migliore per integrare i criteri di denominazione e applicazione di tag con i criteri di denominazione e aziendali esistenti nell'organizzazione?
- Si implementerà un sistema di accounting chargeback o showback? Come sono rappresentati i reparti, i gruppi aziendali e i team in questa struttura organizzativa?
- Quali informazioni dei tag saranno necessarie per tutte le risorse? Quali informazioni dei tag verranno implementate o meno a discrezione dei singoli team?
- I tag devono rappresentare i dettagli come i requisiti di conformità alle normative per una risorsa? Quali sono i dettagli operativi, ad esempio i requisiti in termini di tempo di attività, le pianificazioni per l'applicazione di patch o i requisiti di sicurezza?

## <a name="learn-more"></a>Altre informazioni

Per altre informazioni sulla denominazione e l'applicazione di tag in Azure, vedere:

- [Convenzioni di denominazione per le risorse di Azure](../../../best-practices/naming-conventions.md). Fare riferimento a queste linee guida tratte dal sito Concetti fondamentali sul cloud di Azure per le convenzioni di denominazione consigliate per le risorse di Azure.
- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags?toc=/azure/billing/TOC.json). È possibile applicare i tag in Azure sia a livello di singole risorse che di gruppo di risorse, ottenendo la flessibilità necessaria in termini di granularità per qualsiasi report di accounting in base ai tag applicati.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come la crittografia viene usata per proteggere i dati negli ambienti cloud.

> [!div class="nextstepaction"]
> [Crittografia](../encryption/overview.md)
