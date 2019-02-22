---
title: 'CAF: Piccole e medie imprese - Evoluzione di Gestione costi  '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: Spiegazione di piccole e medie imprese - Evoluzione di Gestione costi
author: BrianBlanchard
ms.openlocfilehash: ca070bfca3efbf9548faa8cf28a1adffefd4a33a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901287"
---
# <a name="small-to-medium-enterprise-cost-management-evolution"></a>Piccole e medie imprese: Evoluzione di Gestione costi

L'articolo approfondisce la presentazione dello scenario aggiungendo controlli per l'MVP della governance.

## <a name="evolution-of-the-narrative"></a>Evoluzione dello scenario

L'adozione del cloud ha superato l'indicatore di tolleranza dei costi definito nell'MVP della governance. Ciò è positivo, dato che corrisponde alle migrazioni dal data center "DR". L'aumento della spesa ora giustifica un investimento di tempo da parte del team di Governance cloud.

### <a name="evolution-of-the-current-state"></a>Evoluzione dello stato attuale

Nella fase precedente di questo scenario, IT ha ritirato il 100% dei data center DR. Lo sviluppo dell'applicazione e i team di business intelligence (BI) sono pronti per il traffico di produzione.

Da allora, sono avvenuti alcuni cambiamenti che influiranno sulla governance:

- il team di migrazione ha iniziato la migrazione delle macchine virtuali all'esterno del data center di produzione.
- I team di sviluppo dell'applicazione eseguono attivamente il push di applicazioni di produzione al cloud, tramite le pipeline CI/CD. Tali applicazioni si possono ridimensionare in modo reattivo in base alle esigenze dell'utente.
- Il team di business intelligence all'interno di IT ha recapitato un numero di strumenti di analisi predittiva nel cloud. i volumi di dati aggregati nel cloud sono in continua crescita.
- Tale crescita supporta i risultati aziendali di esecuzione del commit. Tuttavia, i costi hanno iniziato ad aumentare velocemente. I budget previsti stanno crescendo più rapidamente di quanto ci si aspettava. Il Direttore finanziario (CFO) deve migliorare gli approcci per i costi di gestione.

### <a name="evolution-of-the-future-state"></a>Evoluzione dello stato futuro

Il monitoraggio dei costi e la creazione dei report deve essere aggiunta alla soluzione cloud. IT svolge ancora la funzione di fornitore di servizi di accesso a terze parti. Ciò significa che il pagamento per i servizi cloud, continua a provenire dall'approvvigionamento IT. Tuttavia, i report dovrebbero correlare direttamente le spese operative alle funzioni che comportano l'addebito di costi del cloud. Questo modello è definito come modello "Mostra indietro" nella contabilità del cloud.

I cambiamenti che riguardano lo stato attuale e quello futuro espongono a nuovi rischi che richiedono nuove definizioni di criteri.

## <a name="evolution-of-tangible-risks"></a>Evoluzione dei rischi tangibili

**Controllo del budget**: esiste un rischio intrinseco che le capacità self-service comportino costi eccessivi e imprevisti sulla nuova piattaforma. I processi di governance per i costi di monitoraggio e i costi di migrazione in corso devono poter garantire un costante allineamento con il budget pianificato.

Questo rischio aziendale può comportare alcuni rischi tecnici:

- i costi effettivi potrebbero superare il piano.
- Le condizioni aziendali possono cambiare. In tal caso, è possibile che una funzione aziendale debba usare più servizi cloud del previsto, portando ad anomalie di spesa. Sussiste il rischio che questa spesa aggiuntiva venga considerata in eccedenza, a differenza di una rettifica necessaria per il piano.
- I sistemi potrebbero avere un provisioning eccessivo, che causerebbe la spesa in eccesso.

## <a name="evolution-of-the-policy-statements"></a>Evoluzione delle istruzioni dei criteri

Le modifiche ai criteri seguenti contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione.

1. Tutti i costi del cloud dovrebbero essere monitorati rispetto al piano dal team di governance del cloud con frequenza settimanale. I report sulle deviazioni tra i costi del cloud e il piano stabilito devono essere condivisi con i responsabili IT e l'amministrazione con frequenza mensile. Tutti i costi del cloud e gli aggiornamenti del piano devono essere controllati ogni mese con i responsabili IT e l'amministrazione.
2. Tutti i costi devono essere allocati in una funzione aziendale ai fini della rendicontazione.
3. Gli asset del cloud devono essere monitorati costantemente per eventuali opportunità di ottimizzazione.
4. Gli strumenti di Governance cloud devono limitare le opzioni delle dimensioni degli asset a un elenco approvato di configurazioni. Gli strumenti devono assicurare che tutti gli asset siano individuabili e tracciati dalla soluzione di monitoraggio dei costi.
5. Durante la pianificazione della distribuzione, tutte le risorse richieste del cloud associate all'hosting dei flussi di lavoro di produzione dovrebbero essere documentate. Questa documentazione aiuterà a ridefinire i budget e a preparare altre soluzioni di automazione per impedire l'uso delle opzioni più costose. Durante questo processo si dovrebbero prendere in considerazione diversi strumenti di sconto offerti dal provider di servizi cloud, come istanze riservate o riduzioni dei costi di licenza.
6. A tutti i proprietari di applicazioni è richiesta la partecipazione con training eseguito su procedure consigliate, al fine di ottimizzare i carichi di lavoro per controllare al meglio i costi del cloud.

## <a name="evolution-of-the-best-practices"></a>Evoluzione delle procedure consigliate

Questa sezione dell'articolo sviluppa l'MVP della governance, in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure. Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove istruzioni dei criteri aziendali.

1. Implementare Gestione costi di Azure
    1. Stabilire l'ambito corretto dell'accesso in linea con il criterio di sottoscrizione e la disciplina di Coerenza delle risorse. Supponendo che sia allineato all'MVP della governance definito negli articoli precedenti, ciò richiede l'accesso **Ambito dell'account di registrazione** per il team di Governance cloud incaricato della generazione di report di alto livello. Altri team al di fuori della governance possono richiedere l'accesso **Ambito del gruppo di risorse**.
    2. Stabilire un budget in Gestione costi di Azure.
    3. Esaminare e implementare gli elementi consigliati. Disporre di un processo ricorrente per supportare la creazione di report.
    4. Configurare ed eseguire Creazione di report di Gestione costi di Azure, sia nella fase iniziale sia con frequenza periodica.
2. Aggiornare i Criteri di Azure
    1. Controllare l'assegnazione di tag, il gruppo di gestione, la sottoscrizione e i valori del gruppo di risorse per identificare ogni deviazione.
    2. Definire le opzioni relative alle dimensioni di SKU per limitare le distribuzioni agli SKU elencati nella documentazione relativa alla pianificazione della distribuzione.

## <a name="conclusion"></a>Conclusioni

L'aggiunta di questi processi e di modifiche all'MVP della governance aiuta a mitigare molti dei rischi associati alla governance dei costi. Nel loro insieme offrono le caratteristiche di visibilità, rendicontazione e ottimizzazione necessarie per controllare i costi.

## <a name="next-steps"></a>Passaggi successivi

Dato che l'adozione del cloud continua a evolversi e a offrire valore aziendale supplementare, anche i rischi e le esigenze di governance del cloud sono destinate a evolversi. Per l'azienda fittizia presentata in questo percorso, il passaggio successivo consiste nell'uso di questo investimento nella governance per gestire più cloud.

> [!div class="nextstepaction"]
> [Multi-cloud evolution](./multi-cloud-evolution.md) (Evoluzione di più cloud)
