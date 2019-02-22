---
title: 'CAF: Definire informative sui criteri aziendali'
description: Come stabilire i criteri per riflettere e attenuare i rischi?
author: BrianBlanchard
ms.date: 01/02/2019
ms.openlocfilehash: 97a2c25fea621e5418e505375eb0e80007ddb0de
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902051"
---
<!---
I understand risk and tolerance, now what do I do?
Define the policy... [aspirational statement to move towards 2/1] If you need help defining policies, each discipline includes references to common business risks and policies to mitigate the risks...
--->

# <a name="defining-corporate-policy-for-cloud-governance"></a>Definizione di criteri aziendali per la governance del cloud

Dopo aver analizzato le tolleranze di rischi noti e correlati, il passaggio successivo del percorso di trasformazione del cloud dell'organizzazione consiste nello stabilire criteri che affrontino esplicitamente i rischi e definiscano i passaggi necessari per ridurre tali rischi laddove possibile.

<!-- markdownlint-disable MD026 -->

## <a name="how-can-corporate-it-policy-become-cloud-ready"></a>Come predisporre i criteri IT aziendali per il cloud?

All'interno della governance tradizionale e incrementale, i criteri aziendali creano la definizione operativa di governance. La maggior parte delle azioni di governance IT cerca di implementare la tecnologia per monitorare, applicare, operare e automatizzare tali criteri aziendali. La governance del cloud si basa su concetti simili.

![Governance aziendale e discipline di governance](../../_images/operational-transformation-govern.png)

*Figura 1. Governance aziendale e discipline di governance.*

L'immagine precedente illustra l'interazione tra rischio aziendale, criteri, conformità con monitoraggio e applicazione per creare una strategia di governance. È seguita dalle cinque discipline della governance del cloud da usare per realizzare la strategia.

La governance del cloud è il prodotto di un continuo sforzo di adozione nel tempo, poiché una vera e propria trasformazione duratura non si verifica durante la notte. Il tentativo di fornire una governance del cloud completa con un metodo rapido e aggressivo prima di affrontare i principali cambiamenti dei criteri aziendali, produce raramente i risultati desiderati. In alternativa, è consigliabile un approccio incrementale.

La differenza in un framework di adozione del cloud è data dal ciclo di acquisto e dall'impatto di come tale ciclo può consentire un'autentica trasformazione. Poiché non è presente un requisito di acquisizione delle spese in conto capitale (CapEx), i tecnici possono iniziare prima la sperimentazione e l'adozione. Nella maggior parte delle culture aziendali, l'eliminazione della barriera CapEx all'adozione può portare a cicli di feedback più stretti, crescita organica ed esecuzione incrementale.

Il cambiamento verso l'adozione del cloud richiede un cambiamento nella governance. In molte organizzazioni, la trasformazione dei criteri aziendali porta ad avere una governance migliore e maggiori frequenze di conformità tramite modifiche incrementali ai criteri e all'applicazione automatizzata di tali modifiche, basata su funzionalità definite da configurare con il provider di servizi cloud.

<!-- markdownlint-enable MD026 -->

## <a name="review-existing-policies"></a>Revisione dei criteri esistenti

Man mano che la distribuzione del cloud matura e la quantità di risorse IT trasferite nel cloud aumenta, anche i rischi e le relative esigenze dei criteri cambieranno. La governance è un processo in corso, i criteri devono essere rivisti regolarmente con il personale IT e gli azionisti aziendali per garantire che le risorse ospitate nel cloud continuino a mantenere la conformità ai requisiti e gli obiettivi aziendali complessivi. Il riconoscimento di nuovi rischi e la tolleranza accettabile rende possibile potenziare una [revisione dei criteri esistenti](what-is-a-cloud-policy-review.md)per determinare il livello necessario di governance appropriata per l'organizzazione.

> [!TIP]
> Se l'organizzazione è disciplinata dalla conformità di terze parti, uno dei rischi aziendali maggiori da prendere in considerazione può essere un rischio di aderenza alle [normative di conformità](what-is-regulatory-compliance.md). Spesso questo rischio non può essere attenuato e può richiedere invece una rigorosa aderenza. Assicurarsi di riconoscere i requisiti di conformità di terze parti prima di iniziare una revisione dei criteri.

## <a name="create-cloud-policy-statements"></a>Creare istruzioni dei criteri del cloud

I criteri IT basati sul cloud stabiliscono i requisiti, gli standard e gli obiettivi che i sistemi automatizzati e il personale IT dovranno supportare. Le decisioni relative ai criteri sono un fattore primario all'interno della [Progettazione dell'architettura cloud](align-governance-journeys.md) e nell'implementazione dei [processi di adesione ai criteri](processes.md).

Le istruzioni individuali dei criteri del cloud sono linee guida per affrontare i rischi specifici identificati durante il processo di valutazione dei rischi. Mentre questi criteri possono essere integrati nella documentazione più ampia relativa ai criteri aziendali, le istruzioni dei criteri del cloud, discusse in tutta la guida CAF, tendono ad essere una sintesi più concisa dei rischi e dei piani per affrontarli. Ogni definizione deve includere i tipi di informazioni seguenti:

- **Rischio aziendale**. Riepilogo del rischio che verrà risolto da questi criteri.
- **Istruzioni dei criteri**. Una spiegazione concisa dei requisiti dei criteri e gli obiettivi.
- **Indicazioni tecniche o progettazione**. Suggerimenti pratici, specifiche o altro materiale sussidiario per supportare e applicare i criteri, che i team IT e gli sviluppatori possono usare per progettare e realizzare le distribuzioni cloud.

Se occorre assistenza per le attività iniziali con la definizione dei criteri, consultare le [discipline di governance](../governance-disciplines.md) introdotte nella panoramica della sezione di governance. Negli articoli per ognuna di queste discipline sono inclusi alcuni esempi di rischi aziendali comuni rilevati durante lo spostamento nel cloud e i criteri di esempio usati per ridurre tali rischi (ad esempio, vedere la disciplina di Gestione dei costi [definizioni dei criteri di esempio](../cost-management/policy-statements.md)).

## <a name="incremental-governance-and-integrating-with-existing-policy"></a>Governance incrementale e integrazione con i criteri esistenti

Le aggiunte pianificate all'ambiente cloud devono sempre essere esaminate per conformità con i criteri esistenti e con i criteri di aggiornamento da considerare per eventuali problemi non trattati. È consigliabile anche eseguire con regolarità una [revisione dei criteri del cloud](what-is-a-cloud-policy-review.md) per garantire che i criteri del cloud siano sincronizzati con tutti i criteri aziendali nuovi e aggiornati.

La necessità di integrare i criteri del cloud con i criteri IT legacy, dipende in larga misura dalla maturità dei processi di governance del cloud e dalle dimensioni dei tuoi cloud. Consultare l'articolo sulle [governance incrementali e i criteri di MVP](overview.md) per una discussione più ampia sulla gestione di integrazione dei criteri durante la trasformazione cloud.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver definito le vostre politiche, redigere una guida alla progettazione dell'architettura per fornire al personale IT e agli sviluppatori una guida pratica.

> [!div class="nextstepaction"]
> [Bozza delle guide di progettazione dell'architettura](align-governance-journeys.md)
