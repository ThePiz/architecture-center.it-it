---
title: Razionalizzare il digital estate
titleSuffix: Enterprise Cloud Adoption
description: Processo per valutare le risorse digitali e trovare il modo migliore per ospitarle sul cloud.
author: BrianBlanchard
ms.date: 12/10/2018
ms.openlocfilehash: 05509bdd047b93b4a3b41907836022c837f9c7b4
ms.sourcegitcommit: e7f8676bbffe500fc4d6deb603b7c0b7ba1884a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53179682"
---
# <a name="enterprise-cloud-adoption-rationalize-the-digital-estate"></a>Adozione del cloud nell'organizzazione: Razionalizzare il digital estate

La razionalizzazione del cloud è il processo di valutazione delle risorse per determinare l'approccio migliore per l’hosting delle risorse stesse nel cloud. Una volta determinato un [approccio](approach.md) e aggregato l’[inventario](inventory.md), è possibile iniziare la razionalizzazione del cloud. Nelle [5 R della razionalizzazione](5-rs-of-rationalization.md) sono descritte le opzioni più comuni di razionalizzazione.

## <a name="traditional-view-of-rationalization"></a>Visione tradizionale della razionalizzazione

È facile comprendere la razionalizzazione, se si guarda al tradizionale processo di razionalizzazione come a una struttura decisionale complessa. Ciascuna risorsa del digital estate viene immessa tramite un processo si traduce in una delle cinque risposte (le 5 R). Per piccoli estate, questo processo funziona bene. Per gli estate più grandi, non è molto efficiente e può causare ritardi significativi. Cerchiamo di capirne il motivo esaminando il processo. In seguito presenteremo un modello più efficiente.

**Inventario**. Per completare una razionalizzazione completa usando i modelli tradizionali, è necessario un inventario preciso delle risorse, comprese applicazioni, software, hardware, sistemi operativi e metriche delle prestazioni dei sistemi.

**Analisi quantitativa**. Nella struttura decisionale, le domande quantitative consentono di trovare le risposte per il primo livello di decisioni. Domande frequenti: La risorsa è al momento utilizzata? In questo caso, è ottimizzata e valutata in modo corretto? Quali dipendenze esistono tra le risorse? Queste domande sono fondamentali per la classificazione dell'inventario.

**Analisi qualitativa**. Il set successivo di decisioni richiede l’intelligenza umana per realizzare un’analisi qualitativa. Spesso tali domande sono univoche per la soluzione e solo gli stakeholder dell’azienda e gli utenti esperti possono fornire una risposta. Queste decisioni riguardano in genere i ritardi dei processi che rallentano notevolmente le attività. Questa analisi richiede in genere 40&ndash;80 ore ETP per ogni applicazione. Le linee guida sulla creazione di un elenco di domande di analisi qualitativa sono disponibili in [Approcci per la pianificazione del digital estate](approach.md).

**Decisione sulla razionalizzazione**. Nelle mani di un team di razionalizzazione esperto, i dati quantitativi e qualitativi consentono di prendere decisioni chiare. Sfortunatamente, i team con un elevato livello di esperienza sulla razionalizzazione sono costosi e il training richiede mesi.

## <a name="rationalization-at-enterprise-scale"></a>Razionalizzazione su scala aziendale

Se questa operazione richiede molto tempo ed è molto complessa per un digital estate di 50 macchine virtuali, possiamo immaginare la difficoltà di portare a termine una trasformazione aziendale in un ambiente con migliaia di macchine virtuali e centinaia di applicazioni. Il lavoro richiesto supererà facilmente 1.500 ore ETP e 9 mesi di pianificazione.

La razionalizzazione completa è l’obiettivo finale e un modo eccezionale per andare avanti, ma raramente produce un ROI elevato rispetto alle energie e ai tempi richiesti.

Se la razionalizzazione è essenziale per prendere decisioni finanziarie, vale la pena di prendere in considerazione un'organizzazione di servizi professionali specializzata nella razionalizzazione su cloud per accelerare il processo. Anche in questo caso la razionalizzazione completa può comportare costi e tempi significativi, che possono ritardare la trasformazione o i risultati aziendali.

Nel resto di questo articolo è descritto un approccio alternativo, noto come razionalizzazione incrementale.

## <a name="incremental-rationalization"></a>Razionalizzazione incrementale

La razionalizzazione completa di un grande digital estate comporta dei rischi e può comportare ritardi a causa della complessità. Il presupposto che sta alla base dell'approccio incrementale è che il ritardo nelle decisioni deve scaglionare il carico sull’azienda per ridurre il rischio di ostacoli. Nel corso del tempo, questo approccio consente di creare un modello organico per sviluppare processi e l'esperienza necessari per prendere decisioni qualificate sulla razionalizzazione e farlo in modo più efficiente.

### <a name="inventory-reduce-discovery-data-points"></a>Inventario: consente di ridurre i punti dati di individuazione

Pochissime organizzazioni investono il tempo, l’energia e le spese necessari per gestire un inventario accurato e in tempo reale dell’intero digital estate. Perdita, furto, cicli di aggiornamento e assunzione di personale spesso giustificano la verifica delle risorse dei dispositivi degli utenti finali. Tuttavia, il ritorno sugli investimenti (ROI) della gestione di un server accurato e di un inventario delle applicazioni in un data center locale tradizionale, spesso è ridotto. La maggior parte delle organizzazioni IT ha problemi più pressanti da risolvere rispetto alla verifica dell’uso delle risorse fisse di un data center.

In una trasformazione cloud, l’inventario è direttamente correlato ai costi operativi. Per una pianificazione corretta sono necessari dati di inventario precisi. Sfortunatamente, le opzioni di analisi dell'ambiente correnti, che comportano l’analisi e il catalogo dell’intero inventario, possono ritardare le decisioni di settimane o mesi. Per fortuna, esistono alcuni trucchi per accelerare la raccolta dei dati.

L'analisi basata sugli agenti è la causa di ritardo più citata. I dati affidabili necessari per una razionalizzazione tradizionale spesso dipendono dai dati che possono essere raccolti solo con un agente in esecuzione in ogni risorsa. Questa dipendenza dagli agenti rallenta spesso lo stato di avanzamento, poiché è possibile richiedere commenti dalle funzioni di sicurezza, operative e amministrative.

In un processo di razionalizzazione incrementale, è possibile utilizzare una soluzione senza agente per un’individuazione preliminare, in modo da accelerare le prime decisioni. A seconda del livello di complessità dell'ambiente, potrebbe essere ancora necessaria una soluzione basata su agenti, ma può essere rimossa dal percorso fondamentale per la modifica delle attività.

### <a name="quantitative-analysis-streamline-decisions"></a>Analisi quantitativa: Decisioni più semplici

Indipendentemente dall'approccio per l'individuazione dell’inventario, l'analisi quantitativa può favorire diverse decisioni e presupposti iniziali. Ciò vale soprattutto quando si tenta di identificare il primo carico di lavoro o quando l'obiettivo della razionalizzazione è un confronto dei costi di alto livello. In un processo di razionalizzazione incrementale, i team che si occupano della strategia e dell’adozione del cloud limitano le [5 R della razionalizzazione](5-rs-of-rationalization.md) a due decisioni concise e applicano solo tali fattori quantitativi, semplificando l'analisi e riducendo la quantità di dati iniziali richiesti per le modifiche.

Ad esempio, se un'organizzazione si trova nel mezzo di una migrazione IaaS nel cloud, si può presupporre che la maggior parte dei carichi di lavoro verrà ritirata o riallocata.

### <a name="qualitative-analysis-temporary-assumptions"></a>Analisi qualitativa: Presupposti temporanei

Riducendo il numero di risultati potenziali, è più facile raggiungere una decisione iniziale sullo stato futuro di una risorsa. Riducendo le opzioni, si riduce anche il numero di domande dell'azienda in questa fase iniziale.

Continuando con l'esempio precedente, se le opzioni sono limitate al rehost o al ritiro, la domanda porre all’azienda durante la razionalizzazione iniziale è solo una &mdash; vale a dire, se si desidera ritirare.

"L’analisi suggerisce che non sono presenti utenti che sfruttano attivamente questa risorsa. È giusto o abbiamo trascurato qualcosa?" Una simile domanda binaria è in genere molto più semplice da porre con un'analisi qualitativa.

Questo approccio semplificato genera linee di base, piani finanziari, strategia e direzione. Nelle attività successive, ciascuna risorsa viene sottoposta a ulteriore razionalizzazione e analsi qualitativa per valutare altre opzioni. Tutti i presupposti creati in questa prima razionalizzazione devono essere verificati prima dell’implementazione.

## <a name="challenging-assumptions"></a>Presupposti complessi

Il risultato della sezione precedente è una razionalizzazione approssimativa piena di presupposti. A questo punto è tempo di verificare alcuni di questi presupposti.

### <a name="retiring-assets"></a>Ritiro delle risorse

In un ambiente locale tradizionale, che ospita risorse di piccole dimensioni e non usate, raramente causa un notevole impatto sui costi annuali. Con poche eccezioni, i risparmi associati all'eliminazione e alla rimozione di tali risorse sono compensate dallo sforzo ETP necessario per analizzare e ritirare la risorsa effettiva.

Tuttavia, quando si passa a un modello di accounting cloud, il ritiro delle risorse può produrre risparmi significativi nei costi operativi annuali e nelle attività di migrazione iniziale.

Frequentemente le organizzazioni ritirano almeno il 20% del digital estate dopo aver completato l’analisi quantitativa. È consigliabile un'ulteriore analisi qualitativa prima di prendere una decisione del genere. Una volta confermato, il ritiro di tali risorse può favorire la prima vittoria in termini di ROI della migrazione cloud. In molti casi, questo è uno dei principali fattori di risparmio sui costi. Di conseguenza, è consigliabile che il team di strategia cloud verifichi la convalida e il ritiro delle risorse, parallelamente alla fase di compilazione del processo di migrazione, per consentire un rapido successo finanziario.

### <a name="program-adjustments"></a>Regolazioni del programma

Raramente una società intraprende una sola trasformazione. La scelta tra riduzione dei costi, crescita del mercato e nuovi flussi di ricavi raramente è una decisione binaria. Di conseguenza, è consigliabile che il team di strategia cloud collabori con il team IT per identificare le risorse tramite sforzi di trasformazione paralleli che non rientrino nell'ambito del percorso di trasformazione principale.

Nell'esempio di migrazione IaaS usato in questo articolo:

- Chiedere al team DevOps di identificare le risorse che fanno già parte di un’automazione delle distribuzioni e rimuoverle dal piano di migrazione principale.

- Chiedere ai team Dati e R&S di identificare le risorse che favoriscono nuovi flussi di ricavi e rimuoverle dal piano di migrazione principale.

Questa analisi qualitativa incentrata sul programma può essere eseguita rapidamente e consente di creare un allineamento tra diversi backlog di migrazione.

Per un certo periodo, potrebbe essere ancora necessario trattare alcune risorse come risorse di rehost da sottoporre a una razionalizzazione successiva dopo la migrazione iniziale.

## <a name="selecting-the-first-workload"></a>Selezione del primo carico di lavoro

L’implementazione del primo carico di lavoro è fondamentale per le verifiche e l’apprendimento. È la prima opportunità per dimostrare e costruire una mentalità di crescita.

### <a name="business-criteria"></a>Criteri aziendali

Identificare un carico di lavoro supportato da un membro dell’unità aziendale del team di strategia cloud per garantire la trasparenza aziendale. Preferibilmente, sceglierne uno per il qule il team ha un interesse personale e una forte motivazione al passaggio al cloud.

### <a name="technical-criteria"></a>Criteri tecnici

Selezionare un carico di lavoro con dipendenze minime e che possa essere spostato come piccolo gruppo di risorse. Per agevolare la convalida, suggeriamo di scegliere un carico di lavoro con un percorso di verifica definito.

Il primo carico di lavoro è spesso distribuito in un ambiente sperimentale senza capacità operativa o di governance. È molto importante selezionare un carico di lavoro che non interagisca con dati protetti.

### <a name="qualitative-analysis"></a>Analisi qualitativa

I team di adozione e strategia cloud possono collaborare per analizzare questo piccolo carico di lavoro. In questo modo si stabilisce un'opportunità per creare e verificare i criteri di analisi qualitativa. La quantità ridotta consente di sondare gli utenti interessati, in modo da completare un’analisi qualitativa dettagliata in una settimana o meno. Per fattori di analisi qualitativa comuni, vedere l’obiettivo specifico della razionalizzazione nlle [5 R della razionalizzazione](5-rs-of-rationalization.md).

### <a name="migration"></a>Migrazione

In parallelo alla razionalizzazione continuata, il team di adozione cloud può iniziare la migrazione del piccolo carico di lavoro per espandere l’apprendimento nelle aree principali seguenti:

- Rafforzare le competenze sulla piattaforma del provider di servizi cloud.
- Definire i servizi principali (e gli standard di Azure) necessari per soddisfare la visione a lungo termine.
- Comprendere meglio in che modo le operazioni potrebbero evolversi durante la trasformazione.
- Comprendere i rischi inerenti per le attività e la tolleranza per tali rischi.
- Stabilire una linea di base o un prodotto con validità minima (MVP) per una governance basata sulla tolleranza del rischio aziendale

## <a name="release-planning"></a>Pianificazione del rilascio

Mentre il team di adozione del cloud esegue la migrazione o l'implementazione del primo carico di lavoro, il team di strategia cloud può iniziare ad assegnare la priorità ai rimanenti carichi di lavoro/applicazioni.

### <a name="power-of-ten"></a>Dieci potenti applicazioni

L'approccio tradizionale alla razionalizzazione è un tentativo disperato. Per fortuna, spesso non è necessario un piano per tutte le applicazioni per avviare un processo di trasformazione. In un modello incrementale, le dieci potenti applicazioni sono un buon punto di partenza. In questo modello, il team di strategia cloud seleziona le prime dieci applicazioni da migrare. Questi dieci carichi di dieci lavoro devono contenere una combinazione di carichi di lavoro semplici e complessi.

### <a name="building-the-first-backlogs"></a>Creare i primi backlog

I team di strategia e di adozione cloud possono collaborare all'analisi qualitativa dei primi dieci carichi di lavoro. In questo modo è possibile creare il primo backlog di migrazione e il primo backlog di rilascio in ordine di priorità. Questo approccio consente ai team di iterare l’approccio e fornisce il tempo sufficiente per creare un processo adeguato per l'analisi qualitativa.

### <a name="maturing-the-process"></a>Sviluppo del processo

Quando i due team hanno concordato i criteri di analisi qualitativa, la valutazione può diventare un’attività all’interno di ciascuna iterazione. Per raggiungere un accordo sui criteri di valutazione di solito sono necessari 2&ndash;3 rilasci.

Quando si passa a valutare i processi di esecuzione della migrazione, il team di adozione cloud può iterare più rapidamente la valutazione e l’architettura. In questa fase, il team di strategia cloud può anche ritrovare le risorse per ridurre il tempo necessario. Ciò consente anche al team di strategia cloud di concentrarsi sulla definizione della priorità delle applicazioni che non hanno ancora in una versione specifica per seguire da vicino le variazioni delle condizioni del mercato.

Non tutte le applicazioni prioritarie possono essere pronte per la migrazione. L’ordine è soggetto a variazioni, perché quando il team esegue un’analisi qualitativa più approfondita, può individuare eventi e dipendenze che possono modificare la priorità del backlog. Alcune versioni potrebbero raggruppare un numero ridotto di carichi di lavoro. Altre potrebbero contenere un singolo carico di lavoro.

Il team di adozione cloud probabilmente eseguirà iterazioni che non produrranno una migrazione completa del carico di lavoro. Minori sono il carico di lavoro e le dipendenze, più è probabile che un carico di lavoro rientri in un’unico sprint o iterazione. Per questo motivo, si consiglia di scegliere applicazioni piccole e con poche dipendenze esterne per il primo backlog di rilascio.

## <a name="end-state"></a>Stato finale

Nel corso del tempo, i team di adozione e di stragegia cloud completeranno una razionalizzazione completa dell’inventario. Tuttavia, questo approccio incrementale consente ai team di rendere il processo di razionalizzazione sempre più veloce. Inoltre la trasformazione porterà risultati tangibili sempre prima, senza necessità di nuove analisi iniziali.

In alcuni casi, il modello finanziario potrebbe essere troppo ridotto per consentire di stabilire delle azioni senza una razionalizzazione aggiuntiva. In questi casi, potrebbe essere necessario un approccio più tradizionale alla razionalizzazione.

## <a name="next-steps"></a>Passaggi successivi

L'output delle attività di razionalizzazione è un backlog ordinato in base alle priorità di tutte le risorse che devono essere interessate dalla trasformazione scelta. Questo backlog sarà la base per i modelli di determinazione dei costi dei servizi cloud.

> [!div class="nextstepaction"]
> [Allineare i modelli di costo con il digital estate](calculate.md)