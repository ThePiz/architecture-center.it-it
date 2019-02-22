---
title: 'Cloud Adoption Framework: Informazioni sulla classificazione dei dati'
description: Che cos'è la classificazione dei dati?
author: BrianBlanchard
ms.date: 2/11/2019
ms.openlocfilehash: 07268e7242d92ac2581bf28b378a3c43d166620c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901631"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-data-classification"></a>Che cos'è la classificazione dei dati?

Questo è un articolo introduttivo sull'argomento generale relativo alla classificazione dei dati. La classificazione dei dati è un punto di partenza molto comune nell'ambito della governance.

## <a name="business-risks-and-governance"></a>Governance e rischi aziendali

Nella maggior parte delle organizzazioni, i motivi principali per cui investire nella governance possono essere illustrati in sintesi da tre rischi aziendali:

* Rischio associato alla violazione dei dati
* Interruzione dell'attività aziendale
* Spese non pianificate o impreviste

Esistono molte varianti di questi rischi aziendali, ma i tre indicati tendono a essere i più comuni.

## <a name="understand-then-mitigate"></a>Comprendere e mitigare i rischi

Prima di poter mitigare eventuali rischi, è necessario riconoscerli. Nel caso di rischio di violazione dei dati, l'identificazione è basata principalmente sulla classificazione dei dati. La classificazione dei dati è il processo di associazione di una caratteristica di metadati a ogni asset di un patrimonio digitale, che identifica il tipo di dati associati a tale asset.

Microsoft suggerisce che qualsiasi asset identificato come candidato potenziale per la migrazione o la distribuzione nel cloud debba disporre di metadati documentati per registrare la classificazione dei dati, il livello di criticità aziendale e la responsabilità di fatturazione. Questi tre punti di classificazione risultano molto utili per comprendere e mitigare i rischi.

## <a name="microsofts-data-classification"></a>Classificazione dei dati Microsoft

Di seguito è riportato un elenco delle classificazioni usate da Microsoft. A seconda del settore di appartenenza o dei requisiti di sicurezza esistenti, è possibile che siano già definiti standard per la classificazione dei dati nell'ambito dell'organizzazione. Se non esistono standard, è possibile usare questa classificazione di esempio, che consente di comprendere meglio il profilo di rischio e del patrimonio digitale.  

* **Non aziendali:** dati personali non appartenenti a Microsoft
* **Pubblici**: dati aziendali approvati e disponibili liberamente per l'uso pubblico
* **Generali:** dati aziendali non destinati a un gruppo di destinatari pubblico
* **Riservati:** dati aziendali che possono causare danni a Microsoft se eccessivamente condivisi
* **Strettamente riservati:** dati aziendali che possono causare danni ingenti a Microsoft se eccessivamente condivisi

## <a name="tagging-data-classification-in-azure"></a>Assegnazione di tag alla classificazione di dati in Azure

Ogni provider di servizi cloud deve offrire un meccanismo per la registrazione dei metadati relativi a qualsiasi asset. I metadati sono fondamentali per la gestione degli asset nel cloud. Nel caso di Azure, i tag delle risorse costituiscono l'approccio consigliato per l'archiviazione dei metadati. Per altre informazioni sull'assegnazione di tag alle risorse, vedere l'articolo [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags).

## <a name="next-steps"></a>Passaggi successivi

Applicare le classificazioni dei dati durante uno dei percorsi di governance di utilità pratica.

> [!div class="nextstepaction"]
> [Iniziare un percorso di governance di utilità pratica](../journeys/overview.md)
