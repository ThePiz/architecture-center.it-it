---
title: 'Cloud Adoption Framework: Esempi di esiti fiscali'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: Esempi di esiti fiscali
author: BrianBlanchard
ms.date: 10/10/2018
ms.topic: guide
ms.openlocfilehash: 5b28e22fca294bd1311d29ee5f70ca45e70fae3e
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640227"
---
# <a name="examples-of-fiscal-outcomes"></a>Esempi di esiti fiscali

A livello superiore, le conversazioni sui temi finanziari sono basate su tre concetti fondamentali:

* Ricavi: maggiore profitto per l'azienda come risultato della vendita di beni o servizi.
* Costo: meno denaro speso nella creazione, nelle attività di marketing, nella vendita o nella consegna di merci o servizi.
* Profitto: Sebbene accada raramente, alcune trasformazioni possono sia aumentare i ricavi che ridurre i costi. Si tratta di un risultato redditizio.

Il resto di questo articolo illustra questi risultati fiscali nel contesto di una trasformazione cloud.

> [!NOTE]
> Gli esempi seguenti sono ipotetici e non devono essere considerati come una garanzia di ritorno derivante dall'adozione di qualsiasi strategia cloud.

## <a name="revenue-outcomes"></a>Risultati in termini di ricavi

### <a name="new-revenue-streams"></a>Nuovi flussi di ricavi

Il cloud offre opportunità per distribuire nuovi prodotti ai clienti o consegnare prodotti esistenti in modo nuovo. I nuovi flussi di ricavi sono innovativi, intraprendenti e interessanti per molte persone nel mondo degli affari. I nuovi flussi di ricavi sono anche soggetti a errore e molte aziende li considerano ad alto rischio. Quando vengono proposti dal reparto IT, è molto probabile che vengano rifiutati. Per aggiungere credibilità a questi risultati, collaborare con un leader aziendale riconosciuto come innovatore. La convalida del flusso di ricavi nelle prime fasi del processo consente di evitare ostacoli posti dall'azienda.

* Esempio: Una società vende libri da più di 100 anni. Un dipendente della società si rende conto che il contenuto può essere distribuito in formato elettronico e crea un dispositivo, da poter vendere in libreria, che consente di scaricare gli stessi libri, favorendo nuove vendite di libri da parte di $X.

### <a name="revenue-increases"></a>Aumento dei profitti

Grazie alla portata globale e alla copertura digitale, il cloud consente alle aziende di aumentare i ricavi dei flussi di ricavi esistenti. Spesso, questo tipo di risultato potrebbe provenire da un allineamento con la leadership delle vendite e del marketing.

* Esempio: Un'azienda che vende widget potrebbe vendere più widget se il personale di vendita avesse la possibilità di accedere in modo sicuro ai livelli di scorte e al catalogo digitale dell'azienda. Sfortunatamente, questi dati si trovano solo nel sistema ERP aziendale, a cui si può accedere solo tramite un dispositivo connesso alla rete. La creazione di un ambiente di servizio che si interfaccia con l’ERP, esponendo l'elenco di cataloghi e i livelli di scorte non riservati a un'applicazione nel cloud consentirebbe al personale di vendita di accedere ai dati necessari durante mentre sono in visita presso la sede di un cliente. L’estensione di AD con Azure AD e l'integrazione dell’accesso basato sul ruolo nell'applicazione consentirebbe all'azienda di garantire che i dati siano al sicuro. Questo semplice progetto può influenzare i ricavi provenienti da una linea di prodotti esistente del X%.

### <a name="profit-increases"></a>Aumento dei profitti

Raramente una singola attività riesce simultaneamente ad aumentare i ricavi e a ridurre i costi. Tuttavia, quando questo accade, occorre allineare le dichiarazioni dei risultati da uno o più dei risultati dei ricavi con uno o più dei risultati dei costi per comunicare il risultato desiderato.

## <a name="cost-outcomes"></a>Risultati dei costi

### <a name="cost-reduction"></a>Riduzione dei costi

Cloud computing può ridurre le spese (CapEx) correlate all'acquisto di hardware e software, configurazione di Data Center, che eseguono i data center locali e così via... I rack di server, l’elettricità sempre necessaria per l'alimentazione e il raffreddamento e gli esperti IT necessari per la gestione dell'infrastruttura si aggiungono rapidamente ai costi. Arresto di un Data Center, è possibile ridurre gli impegni di spese in conto capitale. Ciò è talvolta detta "Introduzione all'esterno dell'azienda di Data Center". Riduzione dei costi viene in genere misurato in dollari del budget corrente, che potrebbe estendersi su un massimo di cinque anni a seconda del modo in cui la responsabile amministrativa gestisce le finanze.

* Esempio 1: Account di Data Center dell'azienda per un'alta percentuale della annual budget IT. IT scelga di eseguire una trasformazione Operational un viene eseguita la migrazione gli asset in Data Center in questione all'infrastruttura come soluzioni di servizio (IaaS), la creazione di una riduzione dei costi di tre anni.
* Esempio 2 Una società finanziaria ha acquisito di recente una nuova azienda. Nell'acquisizione, le condizioni indicano che la nuova entità da rimuovere dal Data Center di corrente entro sei mesi. La mancata esecuzione di questa operazione comporterà una sanzione di 1 milione di dollari al mese per la società madre. Spostare le risorse digitali nel cloud in una trasformazione operativa potrebbe consentire una rapida dismissione delle vecchie risorse.
* Esempio 3: Una società di imposta sul reddito che si rivolge ai consumatori registra il 70% del reddito annuo durante i primi tre mesi dell'anno. Il resto dell'anno, il loro grande investimento IT è relativamente inattivo. Una trasformazione operativa consentirebbe all'IT di implementare la capacità di calcolo/hosting richiesta per quei tre mesi. Durante i restanti nove mesi, i costi IaaS potrebbero essere significativamente ridotti riducendo il footprint di calcolo.

### <a name="coverdell"></a>Coverdell

Coverdell modernizza la propria infrastruttura per ottenere risparmi sui costi dei record con Azure. La decisione di Coverdell di investire in Azure e di unire la loro rete di siti web, applicazioni, dati e infrastrutture in questo ambiente, ha portato a maggiori risparmi sui costi di quanto l'azienda avrebbe mai potuto prevedere. La migrazione a un ambiente solo Azure eliminata 54.000 di dollari in costi mensili per i servizi con più sedi. Con l'infrastruttura dell'azienda nuovo, uniti da solo, Coverdell si aspetta di salvare un stimato $1 USD M nei prossimi due o tre anni.
"Che hanno accesso allo stack di tecnologie Azure apre le porte per alcuni scalabile, facile da implementare e soluzioni a disponibilità elevata che sono economicamente conveniente. In questo modo i progettisti in modo molto più creativo con le soluzioni che forniscono."
Ryan Sorensen, Director of Application Development and Enterprise Architecture Coverdell

### <a name="cost-avoidance"></a>Costi evitati

Terminare i Data Center può comportare anche evitare che insorgano costo, impedendo che i cicli di aggiornamento future. Un ciclo di aggiornamento è il processo di acquisto di nuovo hardware e software per sostituire i sistemi locali obsoleti. In Azure, l'hardware e il sistema operativo sono sottoposti a regolare manutenzione, con patch e aggiornamenti gratuiti per i clienti. Questo permette al CFO di eliminare le spese future pianificate dalle previsioni finanziarie a lungo termine. I costi evitati sono misurati in dollari. Si differenzia dalla riduzione dei costi, in quanto si concentra generalmente su un bilancio futuro che non è stato ancora completamente approvato.

* Esempio: Data Center dell'azienda è in scadenza un rinnovo del lease in sei mesi. Nel servizio per otto anni è stata Data Center in questione. Quattro anni fa, tutti i server sono stati aggiornati e virtualizzato determinazione dei costi della società $ milioni. L'anno prossimo, l'azienda prevede di rinnovare l'hardware e il software. Eseguire la migrazione di asset nel Data Center, come parte di una trasformazione operativa, creerebbe prevenzione di costo, rimuovendo l'aggiornamento pianificato del budget previsto del prossimo anno. Potrebbe anche produrre una riduzione dei costi diminuendo o eliminando i costi di locazione immobiliare.

### <a name="capex-versus-opex"></a>Spese in conto capitale e spese operative

Prima di discutere i risultati dei costi, è importante comprendere le due opzioni di costo principali: spese in conto capitale (CapEx) e spese operative (OpEx).

I seguenti termini sono utili per comprendere le differenze tra CapEx e OpEx quando si parla con l'azienda delle operazioni di trasformazione.

* Il **capitale** è il denaro o le risorse in possesso dell'azienda per contribuire a un determinato scopo, come ad esempio, aumentare la capacità del server o sviluppare un'applicazione.
* **Maiuscole delle spese (CapEx)** è una nota spese che genera i vantaggi per un lungo periodo. Tale spesa è generalmente non ricorrente e comporta l'acquisizione di beni permanenti. Lo sviluppo di un'applicazione potrebbe essere considerata una spesa in conto capitale.
* La **spesa operativa (OpEx)** è una spesa ricorrente per l’azienda. L’utilizzo di servizi cloud in un modello di pagamento a consumo può essere considerata come spesa operativa.
* L'asset è una risorsa economica che può essere posseduta o controllata per produrre valore. Server, Data Lake e applicazioni possono essere considerati asset.
* Il **deprezzamento** è il modo in cui il valore di un asset diminuisce nel tempo. Più rilevante per la conversazione CapEx/OpEx, è il modo in cui i costi di un bene sono ripartiti tra i periodi in cui sono utilizzati. Ad esempio, se si compila un'applicazione, quest'anno, ma è previsto per avere una durata di conservazione Media di cinque anni (ad esempio applicazioni commerciali più), quindi il costo del team di sviluppo e gli strumenti necessari necessari per creare e distribuire la base di codice verrebbe rimosso in modo uniforme in cinque anni.
* La **valutazione** è il processo di stima del valore di un'azienda. Nella maggior parte dei settori, la valutazione si basa sulla capacità dell'azienda di generare ricavi e profitti, rispettando al contempo i costi operativi necessari per creare i beni che forniscono tali ricavi. In alcuni settori come il commercio al dettaglio, o in alcuni tipi di transazioni come il private equity, le attività e gli ammortamenti possono svolgere un ruolo importante nella valutazione dell'azienda.

Spesso è importante un confronto tra i vari dirigenti, tra cui il CIO, su come sfruttare meglio il capitale per far crescere l'azienda nella direzione desiderata. Dare al CIO uno strumento per convertire conversazioni CapEx altamente competitive in una chiara affidabilità OpEx potrebbe essere di per sé un risultato interessante. In molti settori, i direttori finanziari (CFO) sono attivamente alla ricerca di modi per associare meglio l'affidabilità finanziaria al costo dei beni venduti.

Tuttavia, prima di associare qualsiasi percorso di trasformazione a questo tipo di conversione da CapEx a OpEx, è consigliabile incontrare i membri dei team CFO o CIO per vedere se l'azienda preferisce strutture di costo CapEx o OpEx. In alcune organizzazioni, la riduzione di CapEx a favore di OpEx è in realtà un risultato non idoneo. Come descritto in precedenza, questo aspetto si nota a volte nelle aziende di vendita al dettaglio, nelle holding e nelle transazioni di private equity, che attribuiscono un valore più elevato ai modelli tradizionali di contabilità degli asset e scarso valore all'IT. Può anche essere visto in organizzazioni che hanno avuto esperienze negative quando hanno esternalizzato in passato il personale IT o altre funzioni.

Se OpEx è auspicabile, l'esempio seguente potrebbe costituire un valido risultato aziendale:

* Esempio: Data Center dell'azienda è attualmente ammortamento a $X all'anno per tre anni. Si prevede di richiedere un ulteriore Y dollari per aggiornare l'hardware nei prossimi anni. È possibile convertire tutto questo CapEx in un modello OpEx a un tasso pari di Z dollari al mese, consentendo una migliore gestione e affidabilità dei costi operativi della tecnologia.
