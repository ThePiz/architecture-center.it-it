---
title: 'CAF: Grandi imprese - Evoluzione della gestione dei costi'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Grandi imprese - Evoluzione della gestione dei costi
author: BrianBlanchard
ms.openlocfilehash: 6bf63e36f6fb19dd0e5f9a16a7f66eb6e0ed54ff
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901684"
---
# <a name="large-enterprise-cost-management-evolution"></a>Grandi imprese: Evoluzione della gestione dei costi

Questo articolo approfondisce la presentazione dello scenario aggiungendo controlli dei costi alla governance del prodotto minimo funzionante (MVP).

## <a name="evolution-of-the-narrative"></a>Evoluzione dello scenario

Il livello di adozione del cloud ha superato l'indicatore di tolleranza definito nell'MVP per la governance. In seguito agli aumenti riscontrati nella spesa, è opportuno che il team di governance del cloud investa del tempo per monitorare e controllare i modelli di spesa.

Come chiaro promotore dell'innovazione, l'IT non viene più considerato principalmente un centro di costo. Poiché l'organizzazione IT ha assunto un'importanza più rilevante, il CIO e il CFO sono concordi sul fatto che è giunto il momento di evolvere il ruolo dell'IT nell'azienda. Tra le varie modifiche, il CFO vuole testare un approccio al cloud accounting basato su pagamenti diretti per il ramo canadese di una delle business unit. Uno dei due data center ritirati ospitava esclusivamente asset per le operazioni canadesi di tale business unit. In questo modello, alla filiale canadese della business unit verranno addebitate direttamente le spese operative relative agli asset ospitati. Questo modello consente all'IT di concentrarsi meno sulla gestione delle spese di altri e di dare priorità alla creazione di valore. Tuttavia, prima di iniziare questa transizione, è necessario implementare gli opportuni strumenti per la gestione dei costi.

### <a name="evolution-of-current-state"></a>Evoluzione dello stato attuale

Nella fase precedente di questo scenario, il team IT ha iniziato attivamente a spostare in Azure i carichi di lavoro di produzione con i dati protetti.

Da allora, sono avvenuti alcuni cambiamenti che influiranno sulla governance:

- 5000 asset sono stati rimossi dai due data center contrassegnati per il ritiro. I responsabili dell'approvvigionamento e della sicurezza IT stanno effettuando il deprovisioning degli asset fisici rimanenti.
- I team di sviluppo delle applicazioni hanno implementato pipeline di integrazione continua/recapito continuo per distribuire una serie di applicazioni native del cloud, con un impatto significativo sulle esperienze dei clienti.
- Il team di BI ha creato processi di aggregazione, controllo, acquisizione di informazioni dettagliate e generazione di stime in grado di offrire vantaggi tangibili per le operazioni aziendali. I risultati delle stime danno un ampio contributo alla creatività per la realizzazione di nuovi prodotti e servizi.

### <a name="evolution-of-future-state"></a>Evoluzione dello stato futuro

- La soluzione cloud deve ora includere anche funzionalità per la generazione di report e il monitoraggio dei costi. I report dovrebbero correlare direttamente le spese operative alle funzioni che comportano l'addebito di costi del cloud. Dovrebbero inoltre consentire all'IT di monitorare le spese e fornire indicazioni tecniche sulla gestione dei costi. Per il ramo canadese, le spese verranno addebitate direttamente al reparto.

## <a name="evolution-of-tangible-risks"></a>Evoluzione dei rischi tangibili

**Controllo del budget**: esiste un rischio intrinseco che le funzionalità self-service comportino costi eccessivi e imprevisti nella nuova piattaforma. I processi di governance per il monitoraggio dei costi e la mitigazione dei rischi dei costi di esercizio devono essere in grado di garantire un costante allineamento con il budget pianificato.

Questo rischio aziendale può tradursi in alcuni rischi tecnici:

- I costi effettivi possono superare quelli pianificati.
- Le condizioni aziendali possono cambiare. In tal caso, è possibile che una funzione di business debba utilizzare più servizi cloud del previsto, sfociando in anomalie di spesa. Esiste il rischio che questi costi aggiuntivi vengano interpretati come eccedenze anziché come necessità di rettifica per il piano. Se l'esperimento canadese va a buon fine, dovrebbe aiutare a mitigare questo rischio.
- Si può verificare un eccessivo provisioning dei sistemi con un conseguente eccesso di spesa.

## <a name="evolution-of-the-policy-statements"></a>Evoluzione delle definizioni dei criteri

Le modifiche ai criteri indicate di seguito contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione.

1. Tutti i costi del cloud devono essere monitorati ogni settimana rispetto al piano dal team di governance del cloud. I report sulle deviazioni tra i costi del cloud e il piano stabilito devono essere condivisi con i responsabili IT e l'amministrazione con frequenza mensile. Tutti i costi del cloud e gli aggiornamenti del piano devono essere controllati ogni mese con i responsabili IT e l'amministrazione.
2. Tutti i costi devono essere allocati a una funzione di business ai fini della rendicontazione.
3. Gli asset del cloud devono essere monitorati costantemente per identificare eventuali opportunità di ottimizzazione.
4. Gli strumenti di governance del cloud devono limitare le opzioni relative alle dimensioni degli asset a un elenco approvato di configurazioni. Gli strumenti devono assicurare che tutti gli asset siano individuabili e tracciati dalla soluzione di monitoraggio dei costi.
5. Durante la pianificazione della distribuzione, è necessario documentare tutte le risorse cloud necessarie associate all'hosting dei carichi di lavoro di produzione. Questa documentazione è utile per ottimizzare i budget e preparare altre soluzioni di automazione per impedire l'uso delle opzioni più costose. Durante questo processo, è consigliabile prestare attenzione alle diverse opportunità di sconto offerte dal provider di servizi cloud, ad esempio istanze riservate o riduzioni dei costi di licenza.
6. Tutti i proprietari di applicazioni devono partecipare a training sulle procedure consigliate al fine di ottimizzare i carichi di lavoro per un controllo più efficace dei costi del cloud.

## <a name="evolution-of-the-best-practices"></a>Evoluzione delle procedure consigliate

Questa sezione dell'articolo approfondisce la progettazione dell'MVP per la governance in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure. Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove definizioni dei criteri aziendali.

1. Introdurre modifiche nel portale aziendale di Azure per imputare la distribuzione canadese all'amministratore del reparto.
2. Implementare Gestione costi di Azure.
    1. Stabilire il livello corretto dell'ambito di accesso in linea con il modello di sottoscrizione e il modello di raggruppamento delle risorse. Presupponendo che questo sia allineato all'MVP per la governance definito negli articoli precedenti, ciò significa che il team di governance del cloud incaricato della generazione di report di alto livello dovrà disporre dell'accesso all'**ambito dell'account di registrazione**. Altri team esterni alla governance, come quello di approvvigionamento della filiale canadese, avranno bisogno dell'accesso all'**ambito del gruppo di risorse**.
    2. Stabilire un budget in Gestione costi di Azure.
    3. Esaminare e implementare i consigli iniziali. È consigliabile pianificare l'esecuzione ricorrente di un processo per la creazione di report.
    4. Configurare ed eseguire la funzionalità per la creazione di report di Gestione costi di Azure, sia nella fase iniziale sia con frequenza periodica.
3. Aggiornare Criteri di Azure.
    1. Aggiungere valori per tag di controllo, gruppo di gestione, sottoscrizione e gruppo di risorse per identificare tutte le deviazioni.
    2. Definire le opzioni relative alle dimensioni di SKU per limitare le distribuzioni agli SKU elencati nella documentazione relativa alla pianificazione della distribuzione.

## <a name="conclusion"></a>Conclusioni

L'aggiunta di questi processi e modifiche all'MVP per la governance aiutano a mitigare molti dei rischi associati alla governance dei costi. Nel loro insieme offrono le caratteristiche di visibilità, attendibilità e ottimizzazione necessarie per controllare i costi.

## <a name="next-steps"></a>Passaggi successivi

In un'epoca in cui l'adozione del cloud continua a evolversi e a offrire valore aziendale, anche i rischi e le esigenze di governance sono destinati a evolversi. Per l'azienda fittizia presentata in questo percorso, il passaggio successivo è usare questo investimento nella governance per gestire più cloud.

> [!div class="nextstepaction"]
> [Evoluzione multi-cloud](./multi-cloud-evolution.md)