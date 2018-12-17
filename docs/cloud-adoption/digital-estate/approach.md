---
title: Approcci per la pianificazione del digital estate
titleSuffix: Enterprise Cloud Adoption
description: Descrive alcuni approcci per la pianificazione del digital estate
author: BrianBlanchard
ms.date: 12/10/2018
ms.openlocfilehash: 5803447ff87e733aa5a9c24ac626bba665b8394a
ms.sourcegitcommit: e7f8676bbffe500fc4d6deb603b7c0b7ba1884a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53179662"
---
# <a name="enterprise-cloud-adoption-approaches-to-digital-estate-planning"></a>Adozione del cloud nell'organizzazione: Approcci per la pianificazione del digital estate

La pianificazione del digital estate può assumere diverse forme, a seconda dei risultati desiderati e delle dimensioni dell'estate esistente. Esistono anche numerose opzioni relative all'approccio adottato. È importante definire le aspettative in relazione all'approccio all'inizio dei cicli di pianificazione. Le aspettative poco chiare sono spesso causa di ritardi associati a pratiche aggiuntive di raccolta dell'inventario. Questo articolo illustra tre approcci all'analisi.

## <a name="workload-driven-approach"></a>Approccio basato su carichi di lavoro

L'approccio di valutazione top-down consente di esaminare gli aspetti di sicurezza, come la categorizzazione dei dati (impatto aziendale alto, medio o basso), la conformità, la sovranità e i requisiti di sicurezza. Questo approccio consente quindi di valutare la complessità dell'architettura ad alto livello, esaminando aspetti come autenticazione, struttura dei dati, requisiti di latenza, dipendenze e aspettativa di vita dell'applicazione. Successivamente, l'approccio top-down calcola i requisiti operativi dell'applicazione, ad esempio i livelli di servizio, l'integrazione, le finestre di manutenzione, il monitoraggio e le informazioni dettagliate. Quando tutti questi aspetti sono stati analizzati e presi in considerazione, il risultato è un punteggio che riflette le difficoltà relative alla migrazione dell'applicazione in ognuna delle piattaforme cloud: IaaS, PaaS e SaaS.

In secondo luogo, la valutazione top-down valuta i vantaggi economici dell'applicazione, come l'efficienza operativa, il costo totale di proprietà, il ritorno sugli investimenti o altre metriche finanziarie appropriate. La valutazione esamina anche la stagionalità dell'applicazione (esistono periodi dell'anno in cui la richiesta raggiunge il picco) e il carico di calcolo complessivo. Inoltre, analizza i tipi di utenti supportati (casuale/esperto, sempre/occasionalmente connesso) e, di conseguenza, la scalabilità e l'elasticità richieste. Infine, la valutazione si conclude esaminando la continuità aziendale e gli eventuali requisiti di resilienza dell'applicazione, nonché le dipendenze per eseguire l'applicazione se dovesse verificarsi un'interruzione del servizio.

> [!TIP]
> Questo approccio richiede interviste e analisi di carattere aneddotico con stakeholder aziendali e tecnici. La disponibilità degli individui chiave è il rischio maggiore per la tempistica. La natura aneddotica delle origini dati rende più difficile l'elaborazione di stime accurate di costi o tempi. È necessario pianificare in anticipo e convalidare tutti i dati raccolti.

## <a name="asset-driven-approach"></a>Approccio basato su asset

L'approccio basato su asset offre un piano basato sulle risorse che supportano un'applicazione per eseguire la migrazione. In questo approccio, un database di gestione della configurazione (CMDB) o altri strumenti di valutazione dell'infrastruttura eseguono il pull dei dati di utilizzo statistico. Questo approccio, in genere, presuppone un modello IaaS di distribuzione come base. In questo processo, l'analisi valuta gli attributi di ogni asset, tra cui: memoria, numero di processori (core della CPU), spazio di archiviazione del sistema operativo, unità dati, schede dell'interfaccia di rete (NIC), IPv6, bilanciamento del carico di rete, clustering, versione del sistema operativo, versione del database (se richiesta), domini supportati e componenti o pacchetti software di terze parti. Gli asset inclusi nell'inventario con questo approccio sono quindi allineati a carichi di lavoro o applicazioni per scopi di raggruppamento o mapping delle dipendenze.

> [!TIP]
> Questo approccio richiede un'ingente origine di dati di utilizzo statistico. Il tempo di analisi dell'inventario e raccolta dei dati è il rischio più alto per la tempistica. Le origini dati di basso livello possono perdere le dipendenze tra applicazioni o asset. Dedicare almeno un mese per l'analisi dell'inventario. Convalidare le dipendenze prima della distribuzione.

## <a name="incremental-approach"></a>Approccio incrementale

Come in gran parte del framework aziendale di adozione del cloud, l'approccio incrementale è altamente consigliato. Nel caso della pianificazione del digital estate, questo comporta un processo in più fasi, come indicato di seguito:

- Analisi dei costi iniziali: se è necessaria la convalida finanziaria, iniziare con un approccio basato su asset, descritto in precedenza, per ottenere un calcolo dei costi iniziali per l'intero digital estate, con nessuna razionalizzazione. Questo consente di stabilire un benchmark per il peggiore dei casi.

- Pianificazione della migrazione: dopo aver nominato un team per la strategia del cloud, creare un backlog di migrazione iniziale usando un approccio basato su carichi di lavoro, considerando esclusivamente le conoscenze collettive e un numero limitato di interviste agli stakeholder. Questo approccio genera rapidamente una valutazione dei carichi di lavoro leggeri per favorire la collaborazione.

- Pianificazione del rilascio: a ogni rilascio, il backlog di migrazione viene eliminato e riclassificato per concentrare l'attenzione sull'impatto aziendale più rilevante. Durante questo processo, i successivi 5&ndash;10 carichi di lavoro saranno selezionati come rilasci in ordine di priorità. A questo punto, il team di strategia per il cloud può investire tempo nel completamento di un approccio basato su carichi di lavoro completo. Ritardare questa valutazione finché non è allineata a una versione rispetta meglio il tempo degli stakeholder. Inoltre, ritarda l'investimento in un'analisi completa finché l'azienda non inizia a vedere i risultati delle attività precedenti.

- Analisi di esecuzione: prima della migrazione, modernizzazione o replica di qualsiasi asset, questo deve essere valutato singolarmente e come parte di un rilascio collettivo. A questo punto, i dati dell'approccio iniziale basato su asset possono essere analizzati per assicurare un dimensionamento e vincoli operativi precisi.

> [!TIP]
> Questo approccio incrementale consente una pianificazione semplificata e risultati accelerati. È molto importante che tutte le parti interessate comprendano l'approccio al ritardo nel processo decisionale. È altrettanto importante che le ipotesi elaborate in ogni fase siano documentate per evitare la perdita di dettagli.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver selezionato un approccio, è possibile raccogliere l'inventario.

> [!div class="nextstepaction"]
> [Raccogliere i dati di inventario](inventory.md)