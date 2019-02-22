---
title: 'CAF: Scenario di governance per grandi imprese'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Questa presentazione descrive un caso d'uso per un percorso di governance per grandi imprese.
author: BrianBlanchard
ms.openlocfilehash: 2f8e18a7a845d39fd5aa1632a9d025a502518a94
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902132"
---
# <a name="large-enterprise-the-narrative-behind-the-governance-strategy"></a>Grandi imprese: Presentazione dello scenario per la strategia di governance

La presentazione seguente descrive un caso d'uso per un [percorso di governance per grandi imprese](./overview.md). Prima di implementare il percorso, è importante conoscere i presupposti e i ragionamenti presentati in questo documento. Sarà quindi possibile allineare più facilmente la strategia di governance al percorso specifico per la propria organizzazione.

## <a name="back-story"></a>Scenario

I clienti stanno chiedendo un'esperienza migliore di interazione con questa società. L'esperienza attuale causato un'erosione del mercato, spingendo il consiglio di amministrazione ad assumere un Chief Digital Officer (CDO). Il CDO sta lavorando con i reparti marketing e vendite per promuovere una trasformazione digitale che consentirà di offrire esperienze migliorate. Inoltre, diverse business unit hanno recentemente assunto data scientist per il data farming e per migliorare molte delle esperienze manuali attraverso l'apprendimento e la stima. Ove possibile, l'IT supporta queste iniziative. Tuttavia, sono in corso attività "IT ombra" al di fuori dei necessari controlli di governance e di sicurezza.

L'organizzazione IT sta anche affrontando problematiche specifiche. Il reparto Finanza sta pianificando continue riduzioni del budget IT nei prossimi cinque anni, con conseguenti tagli di spesa necessari a partire da quest'anno. Di contro, il GDPR e altri requisiti di sovranità sui dati stanno costringendo l'IT a investire in asset in altri paesi per localizzare i dati. Due dei data center esistenti sono in ritardo sull'aggiornamento dell'hardware e questo causa ulteriori problemi in termini di soddisfazione dei dipendenti e dei clienti. Altri tre data center richiedono l'aggiornamento dell'hardware nel corso dell'esecuzione del piano quinquennale. Il CFO sta spingendo il CIO a considerare il cloud come alternativa per quei data center, per sbloccare spese in conto capitale.

Il CIO ha idee innovative che potrebbero aiutare la società, ma le attività sue e dei suoi team sono limitate a risolvere le emergenze e controllare i costi. Durante un pranzo con il CDO e uno dei responsabili delle business unit, la conversazione sulla migrazione cloud ha suscitato l'interesse dei colleghi del CIO. I tre responsabili intendono supportarsi reciprocamente usando il cloud per raggiungere i propri obiettivi di business e hanno intrapreso le fasi di esplorazione e pianificazione dell'adozione del cloud.

## <a name="business-characteristics"></a>Caratteristiche dell'azienda

Il profilo di business della società è il seguente:

- Le vendite e le operazioni si estendono su più aree geografiche con clienti internazionali in più mercati.
- L'azienda è cresciuta attraverso l'acquisizione e opera attraverso tre business unit in funzione della base di clienti di destinazione. La determinazione del budget è un intreccio complesso tra le esigenze di business unit e funzioni.
- Per l'azienda, la maggior parte delle attività IT è considerata come un deflusso di capitali o un centro di costo.

## <a name="current-state"></a>Stato corrente

Questo è lo stato corrente delle attività IT e cloud della società:

- L'IT gestisce più di 20 data center di proprietà privata in tutto il mondo.
- Ogni data center è connesso da una serie di linee dedicate regionali, che creano una rete WAN ad accoppiamento debole.
- L'accesso al cloud è avvenuto tramite la migrazione di tutti gli account di posta elettronica degli utenti finali a Office 365. Questa migrazione è stata completata più di sei mesi fa. Da allora, solo pochi altri asset IT sono stati distribuiti nel cloud.
- Il team di sviluppo principale del CDO sta operando in modalità sviluppo/test per scoprire altre capacità native del cloud.
- Una business unit sta sperimentando implementazioni di Big Data nel cloud. Il team di business intelligence all'interno del reparto IT partecipa all'iniziativa.
- I criteri di governance IT esistenti indicano che le informazioni personali dei clienti e i dati finanziari devono essere ospitati in asset di proprietà diretta della società. Questi criteri impediscono l'adozione del cloud per le app cruciali o i dati protetti.
- Gli investimenti IT sono gestiti in gran parte come spese in conto capitale (CapEx) e sono pianificati di anno in anno. Spesso includono piani di manutenzione ordinaria e cicli di aggiornamento prestabiliti da tre a cinque anni a seconda del data center.
- Gli investimenti in tecnologia non allineati con il piano annuale vengono affrontati per la maggior parte con iniziative IT ombra. In genere queste iniziative sono gestite dalle business unit e finanziate con le spese operative della business unit.

## <a name="future-state"></a>Stato futuro

Sono previste le modifiche seguenti per gli anni successivi:

- Il CIO sta gestendo un'iniziativa mirata a modernizzare i criteri relativi a informazioni personali e dati finanziari per supportare gli obiettivi futuri. Due membri del team di governance IT hanno visibilità sull'iniziativa.
- Se gli esperimenti iniziali dei team di sviluppo applicazioni e di business intelligence mostreranno indicatori di successo, ognuno dei due team intende rilasciare nel cloud soluzioni di produzione su scala ridotta nei prossimi 24 mesi.
- Il CIO e il CFO hanno incaricato un architetto e il Vicepresidente Infrastrutture di creare un'analisi dei costi e uno studio di fattibilità. Queste iniziative consentiranno di determinare se l'azienda può e dovrebbe spostare 5.000 asset nel cloud nei 36 mesi successivi. Una migrazione corretta consentirebbe al CIO di eliminare due data center, riducendo i costi di oltre 100 milioni di dollari nel corso del piano quinquennale. Se tre o quattro data center possono ottenere risultati simili, il budget sarà di nuovo in attivo e il CIO disporrà di risorse per finanziare iniziative più innovative.
    ![Costi locali rispetto ai costi di Azure in un grafico che dimostra un ritorno di 100 milioni di dollari USA nei prossimi cinque anni](../../../_images/governance/calculator-enterprise.png)
- In aggiunta a questi risparmi sui costi, l'azienda intende modificare la gestione di alcuni investimenti IT, riposizionando le spese in conto capitale come spese operative (OpEx) per il contesto IT. Questa modifica consentirà un maggiore controllo dei costi, che permetterà al reparto IT di accelerare altri progetti pianificati.

## <a name="next-steps"></a>Passaggi successivi

La società ha elaborato criteri aziendali per strutturare l'implementazione della governance. Tali criteri aziendali guidano molte delle decisioni tecniche.

> [!div class="nextstepaction"]
> [Esaminare i criteri aziendali iniziali](./initial-corporate-policy.md)
