---
title: 'Indicazioni: Struttura di un gruppo di risorse di Azure'
description: Indicazioni per la progettazione di un gruppo di risorse di Azure nell'ambito di una strategia di adozione del cloud di base
author: petertay
ms.openlocfilehash: ac6cbb03be8cdba020641d3b9034ad9d20101acf
ms.sourcegitcommit: 2e8b06e9c07875d65b91d5431bfd4bc465a7a242
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
ms.locfileid: "29062099"
---
# <a name="guidance-azure-resource-group-design"></a>Indicazioni: Struttura di un gruppo di risorse di Azure

In Azure un [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) è un contenitore logico in cui sono raggruppate le risorse. Ogni risorsa in Azure deve essere distribuita in un singolo gruppo di risorse.

## <a name="design-considerations"></a>Considerazioni sulla progettazione

- Tutte le risorse all'interno di un gruppo devono avere lo stesso ciclo di vita. In altre parole, la procedura da seguire è quella di distribuire, aggiornare ed eliminare in gruppo le risorse con lo stesso ciclo di vita. Ad esempio, le risorse di calcolo di un'applicazione Web vengono in genere distribuite come singola unità, mentre un database condiviso con altre applicazioni Web viene presumibilmente gestito in un ciclo di vita diverso e deve quindi essere incluso nel relativo gruppo di risorse.
- Un gruppo di risorse può contenere le risorse che risiedono in aree diverse.
- Tutte le risorse di uno stesso gruppo devono essere associate a una singola sottoscrizione. 
- Una risorsa può essere spostata da un gruppo all'altro, ma non in un gruppo contenente una risorsa distribuita in un'altra sottoscrizione.
- L'assegnazione a un gruppo non influisce sulla connettività o sull'interazione della risorsa con quelle incluse in altri gruppi. Ad esempio, una macchina virtuale assegnata a un determinato gruppo può connettersi a un database assegnato a un altro gruppo, se tra di loro è attiva la connettività di rete.
- Un gruppo di risorse consente di definire l'ambito di controllo di accesso per operazioni amministrative. È possibile applicare autorizzazioni di controllo degli accessi in base al ruolo (RBAC) a livello di sottoscrizione o di gruppo di risorse. Le autorizzazioni assegnate a livello di sottoscrizione vengono ereditate a livello di gruppo di risorse. Altre informazioni sulle autorizzazioni RBAC e su quelle relative alle risorse verranno fornite nelle fasi di adozione intermedia e avanzata.

## <a name="proven-practices"></a>Procedure consolidate

- Nella fase di base si gestiscono in genere solo alcuni progetti modello di verifica (PoC), ciascuno con un numero limitato di risorse. Poiché le risorse PoC condividono in genere lo stesso ciclo di vita, è possibile creare un singolo gruppo di risorse per ogni progetto di questo tipo.
- Durante la fase di adozione intermedia si gestiranno più progetti. Tipi diversi di progetti possono sfruttare altre strutture di gruppi di risorse. Se si intende passare uno dei progetti PoC iniziali all'ambiente di produzione, è possibile spostare le risorse in un altro gruppo, purché appartenga alla stessa sottoscrizione. In questa fase è pertanto necessario distribuire le risorse nella stessa sottoscrizione in modo da poterle riorganizzare in futuro.

## <a name="next-steps"></a>Passaggi successivi

* A questo punto, dopo avere appreso le procedure consolidate relative alla fase di adozione di base, è possibile creare gruppi di risorse e aggiungervi altre risorse. In questa fase il numero di risorse gestite è limitato, ma le attività di gestione diventano più complesse man mano che si aggiungono altre risorse. Leggere le [informazioni sulle convenzioni di denominazione di Azure e sull'assegnazione di tag](/azure/architecture/best-practices/naming-conventions?toc=/azure/architecture/cloud-adoption-guide/toc.json) per assegnare nomi e tag alle risorse come attività preliminare alla fase di adozione intermedia.
