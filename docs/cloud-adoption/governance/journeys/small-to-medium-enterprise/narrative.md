---
title: 'CAF: Piccole e medie imprese - Presentazione iniziale dello scenario per la strategia di governance'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Questa presentazione descrive un caso d'uso per un percorso di governance per piccole e medie imprese.
author: BrianBlanchard
ms.openlocfilehash: 6173b01f310169017761110d6641acfa51d45df8
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902211"
---
# <a name="small-to-medium-enterprise-the-narrative-behind-the-governance-strategy"></a>Piccole e medie imprese: presentazione dello scenario per la strategia di governance

La presentazione seguente descrive il caso d'uso per il [percorso di governance per le piccole e medie imprese](./overview.md). Prima di implementare il percorso, è importante conoscere i presupposti e i ragionamenti presentati in questo documento. Sarà quindi possibile allineare più facilmente la strategia di governance al percorso specifico per la propria organizzazione.

## <a name="back-story"></a>Scenario

Il consiglio di amministrazione ha avviato l'anno con piani per dare nuova energia alle attività aziendali in diversi modi, invitando la dirigenza a migliorare le esperienze dei clienti per conquistare nuove quote di mercato. Il consiglio spinge anche verso la realizzazione di nuovi prodotti e servizi per garantire all'azienda una posizione leader riconosciuta nel settore. Sono state anche avviate iniziative parallele per ridurre gli sprechi e tagliare i costi non necessari. Anche se possono intimorire, le azioni del consiglio di amministrazione e della dirigenza dimostrano che gli investimenti sono incentrati al massimo sulla crescita futura.

In passato, il CIO (Chief Information Officer) dell'azienda è stato escluso da queste conversazioni strategiche. Tuttavia, dato che la visione futura è intrinsecamente collegata allo sviluppo tecnico, il reparto IT partecipa alla discussione per contribuire alla definizione di questi piani di notevole portata. Ci si aspetta ora che il reparto IT contribuisca in modi nuovi. Il team non è in effetti preparato per questi cambiamenti ed è probabile che debba affrontare le difficoltà della curva di apprendimento.

## <a name="business-characteristics"></a>Caratteristiche dell'azienda

Il profilo di business della società è il seguente:

- Tutte le attività di vendita e operative sono concentrate in un singolo paese, con una bassa percentuale di clienti globali.
- L'azienda opera come una singola business unit, con budget allineato alle funzioni, tra cui Vendite, Marketing, Operazioni e IT.
- Per l'azienda, la maggior parte delle attività IT è considerata come un deflusso di capitali o un centro di costo.

## <a name="current-state"></a>Stato corrente

Questo è lo stato corrente delle attività IT e cloud della società:

- Il reparto IT gestisce operativamente due ambienti di infrastruttura ospitati. Un ambiente contiene gli asset di produzione. Il secondo ambiente contiene gli asset per il ripristino di emergenza e alcuni asset per sviluppo e test. Questi ambienti sono ospitati da due diversi provider. Il reparto IT fa riferimento a questi due data center rispettivamente come Prod e DR.
- L'accesso al cloud è avvenuto tramite la migrazione di tutti gli account di posta elettronica degli utenti finali a Office 365. Questa migrazione è stata completata sei mesi fa. Pochi altri asset IT sono stati distribuiti nel cloud.
- I team di sviluppo delle applicazioni operano in modalità sviluppo/test per scoprire altre capacità native del cloud.
- Il team di business intelligence (BI) sta sperimentando implementazioni di Big Data nel cloud e la gestione dei dati in nuove piattaforme.
- La società ha definito criteri generali che stabiliscono che le informazioni personali dei clienti e i dati finanziari non possono essere ospitati nel cloud, con conseguenti limiti per le applicazioni cruciali nelle distribuzioni correnti.
- Gli investimenti IT sono gestiti in gran parte come spese in conto capitale (CapEx) e sono pianificati di anno in anno. Nel corso degli ultimi anni, gli investimenti sono stati limitati a poco più dei requisiti di manutenzione di base.

## <a name="future-state"></a>Stato futuro

Sono previste le modifiche seguenti per gli anni successivi:

- Il CIO sta rivedendo i criteri per la gestione dei dati personali e finanziari, in modo da tenere conto degli obiettivi futuri.
- I team di sviluppo delle applicazioni e di business intelligence intendono rilasciare soluzioni basate sul cloud in produzione nel corso dei 24 mesi successivi, in linea con la visione aziendale per l'engagement dei clienti e i nuovi prodotti.
- Nel corso dell'anno, il team IT terminerà il ritiro dei carichi di lavoro di ripristino di emergenza del data center DR con la migrazione di 2.000 macchine virtuali nel cloud. È previsto che questa misura consenta risparmi sui costi stimati per 25 milioni di dollari USA nel corso dei prossimi cinque anni.
    ![Costi locali rispetto ai costi di Azure in un grafico che dimostra un ritorno di 25 milioni di dollari USA nei prossimi cinque anni](../../../_images/governance/calculator-small-to-medium-enterprise.png)
- L'azienda intende modificare la modalità di gestione degli investimenti, riposizionando le spese in conto capitale come spese operative (OpEx) per il contesto IT. Questa modifica consentirà un maggiore controllo dei costi e permetterà al reparto IT di accelerare altri progetti pianificati.

## <a name="next-steps"></a>Passaggi successivi

La società ha elaborato criteri aziendali per strutturare l'implementazione della governance. Tali criteri aziendali guidano molte delle decisioni tecniche.

> [!div class="nextstepaction"]
> [Esaminare i criteri aziendali iniziali](./initial-corporate-policy.md)
