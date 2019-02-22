---
title: 'CAF: Processi per la conformità ai criteri della Baseline di identità'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Processi per la conformità ai criteri della Baseline di identità
author: BrianBlanchard
ms.openlocfilehash: e00427662c811fccb7e0a7261a4bb3dd3437103e
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901272"
---
# <a name="identity-baseline-policy-compliance-processes"></a>Processi per la conformità ai criteri della Baseline di identità

Questo articolo illustra un approccio ai processi per la conformità ai criteri che regolano la [Baseline di identità](./overview.md). Una governance efficace dell'identità inizia con processi manuali ricorrenti che guidano l'adozione di criteri di identità e le revisioni. Ciò richiede il coinvolgimento regolare del team di governance del cloud e gli azionisti aziendali e IT, per rivedere e aggiornare i criteri e garantirne la conformità. Inoltre, molti processi di applicazione e di monitoraggio continuo possono essere automatizzati o eseguiti con strumenti per ridurre il sovraccarico di governance e consentire una risposta più rapida alla deviazione dai criteri.

## <a name="planning-review-and-reporting-processes"></a>Processi di pianificazione, verifica e creazione di report

Gli strumenti di gestione delle identità offrono caratteristiche e funzionalità che semplificano notevolmente il controllo di accesso e la gestione dell'utente all'interno di una distribuzione cloud. Tuttavia, richiedono anche processi e criteri ben congegnati per supportare gli obiettivi dell'organizzazione. Il set seguente contiene esempi di processi comunemente coinvolti nella disciplina Baseline di identità. Usare questi esempi come punto di partenza per la pianificazione di processi che consentono di continuare ad aggiornare i criteri di identità in base alle modifiche dell'azienda e ai feedback da parte del team IT incaricato di concretizzare le indicazioni sulla governance.

**Valutazione iniziale dei rischi e pianificazione**: Come parte dell'adozione iniziale della disciplina Baseline di identità, identificare i principali rischi aziendali e le tolleranze correlate alla gestione delle identità nel cloud. Usare queste informazioni per discutere di rischi tecnici specifici con i membri del reparto IT e del team per la gestione delle identità, in modo da poter sviluppare un set di base relativo ai criteri di sicurezza per attenuare tali rischi e per stabilire la strategia di governance iniziale.

**Pianificazione della distribuzione**: Prima di eseguire qualsiasi distribuzione, esaminare le esigenze di accesso per i carichi di lavoro e sviluppare una strategia di controllo di accesso che sia in linea con i criteri di identità aziendali stabiliti. Documentare eventuali gap tra esigenze e criteri correnti per determinare se sia necessario aggiornare i criteri e modificarli se necessario.

**Test della distribuzione**: In quanto parte della distribuzione, il team di governance del cloud, in collaborazione con i team delle operazioni su cloud, sarà responsabile della verifica della distribuzione per convalidare la conformità ai criteri di coerenza delle risorse.

**Pianificazione annuale**: Su base annuale, eseguire una verifica generale della strategia di gestione delle identità. Esplorare le modifiche pianificate per l'ambiente dei servizi di identità e le strategie aggiornate di adozione del cloud per identificare il potenziale aumento del rischio o se sia necessario modificare i modelli di infrastruttura delle identità correnti. È consigliabile usare questo momento anche per esaminare le più recenti procedure consigliate riguardo la Gestione delle identità, per integrarle ai criteri e ai processi di revisione.

**Pianificazione trimestrale**: Con cadenza trimestrale eseguire una verifica dei dati di audit di identità e il controllo degli accessi e incontrare i team di adozione del cloud per identificare eventuali nuovi potenziali rischi o requisiti operativi che potrebbero richiedere aggiornamenti dei criteri di identità o modifiche alla strategia di controllo dell'accesso.

Durante questo processo di pianificazione è inoltre opportuno valutare se il team di governance del cloud è a conoscenza delle lacune relative a criteri nuovi o in evoluzione e ai rischi relativi alle identità. Invitare il personale IT pertinente a partecipare alle attività di verifica e pianificazione come consulenti tecnici temporanei o come membri permanenti del team.  

**Didattica e training**: Offrire sessioni di training su base bimestrale per assicurarsi che gli sviluppatori e il personale IT siano sempre aggiornati sui requisiti più recenti dei criteri delle identità. Come parte di questo processo, rivedere e aggiornare qualsiasi documentazione, indicazione o altre risorse di training per assicurarsi che siano in linea con le istruzioni dei criteri aziendali più recenti.

**Controllo mensile e creazione di report sulle revisioni**: Su base mensile, eseguire un controllo su tutte le distribuzioni cloud per garantire il costante allineamento con i criteri delle identità. Usare questa verifica per controllare l'accesso degli utenti in confronto alle modifiche dell'azienda per assicurarsi che gli utenti possano accedere correttamente alle risorse cloud e garantire che vengano seguite le strategie di accesso, ad esempio il controllo degli accessi in base al ruolo, in modo coerente. Identificare gli account con privilegi e documentarne lo scopo. Questo processo di verifica produce un report destinato al team per la strategia cloud e a ogni team di adozione cloud per dettagliare l'aderenza complessiva ai criteri. Il report viene inoltre archiviato a scopo legale e di controllo.

## <a name="ongoing-monitoring-processes"></a>Processi di monitoraggio continuo

La determinazione del buon esito della strategia di governance delle identità dipende dalla visibilità dello stato attuale e passato del sistema delle identità. Senza la possibilità di analizzare le metriche rilevanti per la distribuzione su cloud e i dati pertinenti, non è possibile identificare cambiamenti nei rischi o rilevare violazioni delle tolleranze ai rischi. I processi di governance in corso illustrati in precedenza richiedono dati di qualità per garantire che i criteri possano essere modificati per supportare le mutevoli esigenze dell'azienda.

Verificare che i team IT abbiano implementato sistemi di monitoraggio automatizzati per i servizi delle identità che consentono di acquisire i dati di log e controllo rilevanti necessari per valutare i rischi. Essere proattivi nel monitoraggio di questi sistemi garantisce la rapida individuazione e mitigazione di potenziali violazioni di criteri e assicura che le eventuali modifiche alla propria infrastruttura delle identità si rifletta sulla strategia di monitoraggio.

## <a name="violation-triggers-and-enforcement-actions"></a>Violazione di trigger e azioni di applicazione

Le violazioni dei criteri di identità possono comportare l'accesso non autorizzato ai dati sensibili e causare gravi interruzioni di servizi e applicazioni di importanza cruciale. Quando vengono rilevate violazioni, si devono intraprendere azioni in modo da riallineare i criteri il prima possibile. Il team IT può automatizzare la maggior parte delle violazioni di trigger usando gli strumenti descritti in [Identity Baseline toolchain for Azure](toolchain.md) (Toolchain di Baseline di identità per Azure).

I trigger seguenti e le azioni di applicazione forniscono esempi a cui è possibile fare riferimento quando si pianifica l'uso dei dati di monitoraggio per risolvere le violazioni dei criteri:

- Rilevamento di un'attività sospetta: Gli accessi utente rilevati dalle informazioni degli indirizzi IP proxy anonimi, le posizioni non note o successivi accessi da località geografiche troppo distanti possono indicare una potenziale violazione della sicurezza dell'account o il tentativo di un accesso non autorizzato. L'accesso verrà bloccato fino a quando non sarà possibile verificare l'identità dell'utente e la password sarà reimpostata.
- CREDENZIALI UTENTE PERSE: Un account il cui nome utente e la password sono andati persi in internet verrà disabilitato fino a quando non sarà possibile verificare l'identità dell'utente e la password sarà reimpostata.
- Rilevamento di controlli di accesso insufficienti: Tutti gli asset protetti in cui le restrizioni all'accesso non soddisfano i requisiti di sicurezza saranno bloccati fino a quando la risorsa non risulta conforme.

## <a name="next-steps"></a>Passaggi successivi

Usare il [modello di gestione del cloud](./template.md) per documentare i processi e i trigger in linea con il piano di adozione del cloud attuale.

Per istruzioni sull'esecuzione dei criteri di gestione del cloud in linea con i piani di adozione, vedere l'articolo sul miglioramento della disciplina.

> [!div class="nextstepaction"]
> [Miglioramento della disciplina Baseline di identità](./discipline-improvement.md)
