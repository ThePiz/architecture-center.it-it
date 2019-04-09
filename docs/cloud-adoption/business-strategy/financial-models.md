---
title: 'CAF: Creare un modello finanziario per la trasformazione del cloud'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: Come creare un modello finanziario per la trasformazione del cloud.
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.openlocfilehash: 1f3ed8a84b84ba577ad5e5db8b1becd318dc04a3
ms.sourcegitcommit: 0a8a60d782facc294f7f78ec0e9033e3ee16bf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068872"
---
# <a name="create-a-financial-model-for-cloud-transformation"></a>Creare un modello finanziario per la trasformazione del cloud

Creare un modello finanziario che rappresenti accuratamente l'intero valore aziendale di una trasformazione del cloud può essere complicato. I modelli finanziari e le motivazioni aziendali tendono a essere diversi tra un'organizzazione e l'altra. Questo articolo definisce alcune formule e indica alcuni fattori che solitamente non vengono considerati durante la creazione di un modello finanziario.

## <a name="return-on-investment-roi"></a>Ritorno sugli investimenti (ROI)

Il ritorno sugli investimenti (ROI) è spesso un criterio importante per la dirigenza o il consiglio di amministrazione. Il ROI consente di confrontare diversi modi di investire risorse di capitale limitate. La formula per il ROI è piuttosto semplice. I dettagli necessari per creare ciascun input per la formula potrebbero non essere altrettanto semplici. In pratica, il ritorno sugli investimenti è la quantità di ritorno prodotta in seguito a un investimento iniziale. In genere viene rappresentato come una percentuale:

![Ritorno sugli investimenti (ROI) è uguale a (guadagno dall'investimento - costi di investimento) / costi di investimento](../_images/formula-roi.png)

<!-- markdownlint-disable MD036 -->
<!--*ROI = (Gain from Investment &minus; Initial Investment) / Initial Investment*-->
<!-- markdownlint-enable MD036 -->

Nelle sezioni successive, si illustreranno i dati necessari per calcolare l'investimento iniziale e il guadagno dall'investimento (utili).

## <a name="calculating-initial-investment"></a>Calcolo dell'investimento iniziale

L'investimento iniziale è costituito dalle spese in conto capitale (CapEx) e le spese operative (OpEx) necessarie per completare una trasformazione. La classificazione dei costi può variare a seconda delle preferenze del direttore delle finanze e dei modelli di contabilità. Tuttavia, questa categoria include fattori come: Servizi professionali da trasformare, licenze software usate esclusivamente durante la trasformazione, costi dei servizi cloud durante la trasformazione e costi potenziali dei dipendenti stipendiati durante la trasformazione.

Per creare una stima dell'investimento iniziale, è necessario sommare questi costi.

## <a name="calculating-the-gain-from-investment"></a>Calcolo del guadagno dell'investimento

Il guadagno dell'investimento iniziale spesso richiede una seconda formula di calcolo, la quale è molto specifica per i risultati aziendali e le relative modifiche tecniche. Il calcolo degli utili non è semplice come quello della riduzione dei costi.

Per calcolare gli utili, sono necessarie due variabili:

![Il guadagno dell'investimento è uguale a delta dei ricavi + delta dei costi](../_images/formula-gain-from-investment.png)

<!-- markdownlint-disable MD036 -->
<!--*Gain from Investment = Revenue Deltas + Cost Deltas*-->
<!-- markdownlint-enable MD036 -->

Ciascun elemento è descritto di seguito.

## <a name="revenue-delta"></a>Delta dei ricavi

Il delta dei ricavi deve essere previsto in collaborazione con l'azienda. Quando gli stakeholder aziendali si accordano su un impatto dei ricavi, questo può essere utile per migliorare la posizione di guadagno.

## <a name="cost-deltas"></a>Delta dei costi

I delta dei costi sono la quantità di aumento o calo che risulterà dalla trasformazione. Esistono una serie di variabili indipendenti che possono influire sul costo delta. Gli utili si basano prevalentemente su costi fissi come la riduzione delle spese in conto capitale, i costi evitati, la riduzione dei costi operativi e la riduzione degli ammortamenti. Le seguenti sezioni sono esempi di delta dei costi da prendere in considerazione.

### <a name="depreciation-reductions-or-acceleration"></a>Riduzione o accelerazione degli ammortamenti

Per indicazioni sugli ammortamenti, contattare il direttore delle finanze o il team finanziario. Seguono riferimenti generali sul tema degli ammortamenti.

Quando si investe capitale nell'acquisizione di un asset, tale investimento può essere usato a scopi finanziari o fiscali per generare vantaggi continui per tutta la vita utile prevista dell'asset. Alcune aziende vedono gli ammortamenti come un vantaggio fiscale positivo. Altri li considerano una spesa continua e inevitabile, simile ad altri costi periodici attribuiti alla spesa informatica annuale.

Rivolgersi all'ufficio finanziario per vedere se è possibile eliminare gli ammortamenti e se questo può avere un impatto positivo sui delta dei costi.

### <a name="physical-asset-recovery"></a>Recupero di asset fisico

In alcuni casi, gli asset dismessi possono essere venduti come fonte di ricavi. Spesso, questi ricavi sono compresi nella riduzione dei costi per semplicità. Tuttavia, si tratta di un vero aumento dei ricavi e, in quanto tale, può essere soggetto a tasse. Rivolgersi all'ufficio finanziario per valutare l'attuabilità di questa alternativa e come contabilizzare i ricavi risultanti.

### <a name="operational-cost-reductions"></a>Riduzione dei costi operativi

Le spese ricorrenti necessarie per gestire l'azienda sono spesso definite spese operative (OpEx). La categoria delle spese operative è molto ampia. Nella maggior parte dei modelli di contabilità, questa includerebbe: gestione delle licenze software, spese di hosting, bollette dell'elettricità, affitti immobiliari, costi di raffreddamento, personale temporaneo necessario per le operazioni, noleggio di apparecchiature, parti di ricambio, contratti di manutenzione, servizi di riparazione, servizi di continuità aziendale e ripristino di emergenza (BC/DR) e numerose altri costi che non richiedono le approvazioni di spesa in conto capitale.

Questa categoria è uno dei principali utili quando si considera una trasformazione operativa. Il tempo investito a stilare una lista esauriente è raramente sprecato. Rivolgersi al direttore informatico e al team finanziario per assicurarsi che tutti i costi operativi siano presi in considerazione.

### <a name="cost-avoidance"></a>Costi evitati

Quando è prevista una spesa operativa (OpEx) che non rientra ancora in un budget approvato, potrebbe non essere adatta per la categoria di riduzione dei costi. Ad esempio, se le licenze di Microsoft e VMWare devono essere rinegoziate e pagate l'anno successivo, non sono ancora considerabili come costi completi. Le riduzioni di tali costi previsti sarebbero considerate come costi operativi ai fini del calcolo del delta dei costi. Tuttavia, andrebbero informalmente definite come "costi evitati" in fino al completamento della negoziazione e dell'approvazione del budget.

### <a name="soft-cost-reductions"></a>Riduzione dei costi flessibili

Alcune aziende potrebbero includere anche costi flessibili come la riduzione della complessità operativa o del personale a tempo pieno per la gestione di un data center. Tuttavia, includere i costi flessibili può essere una scelta non ideale. L'inclusione dei costi flessibili implica un'ipotesi non documentata secondo cui la riduzione dei costi corrisponderà a risparmi concreti. I progetti tecnologici raramente comportano un recupero reale dei costi flessibili.

### <a name="headcount-reductions"></a>Riduzione di organico

Il tempo risparmiato dal personale è spesso incluso nella riduzione dei costi flessibili. Quando questi risparmi di tempo sono associati a una riduzione reale di stipendi o personale nel settore informatico, possono essere calcolati separatamente come una riduzione di organico.

Detto questo, le competenze necessarie in locale sono in genere associate a una serie di competenze simili (o di livello più alto) necessarie all'interno del cloud. Questo significa che, in genere, i dipendenti non perdono il lavoro dopo una migrazione del cloud.

Un'eccezione è quando la capacità operativa viene fornita da terze parti o provider di servizi gestiti (MSP). Se i sistemi informatici sono gestiti da terze parti, i costi operativi potrebbero essere sostituiti da una soluzione nativa in cloud o da un MSP nativo in cloud. È probabile che un MSP nativo in cloud che operi in modo più efficiente e a costi potenzialmente inferiori. In tal caso, la riduzione dei costi operativi va inclusa nel calcolo dei costi fissi.

### <a name="capital-expense-reductions-or-avoidance"></a>Riduzione o costi evitati di spesa in conto capitale

Le spese in conto capitale (CapEx) sono leggermente diverse dalle spese operative. In genere, questa categoria è determinata dai cicli di aggiornamento o dall'espansione dei data center. Un esempio di un'espansione dei data center può essere un nuovo cluster ad alte prestazioni per l'hosting di una soluzione di Big Data o di un data warehouse e in genere può rientrare nella categoria di spese in conto capitale. I cicli di aggiornamento di base sono più comuni. Alcune aziende dispongono di hardware rigida cicli di aggiornamento, gli asset di significato sono ritiro e verrà sostituiti durante un ciclo regolare (in genere ogni tre, cinque o otto anni). Questi cicli spesso coincidono con cicli di noleggio di asset o cicli di vita prevista di apparecchiature. Quando è il momento di un ciclo di aggiornamento, il settore IT attinge dalle spese in conto capitale per acquistare l'apparecchiatura nuova.

Se un ciclo di aggiornamento viene approvato e preventivato, la trasformazione del cloud può essere utile per eliminare tale costo. Se un ciclo di aggiornamento è pianificato ma non ancora approvato, la trasformazione del cloud può evitare dei costi di spesa in conto capitale. Entrambi gli scenari sarebbero inclusi nel delta dei costi.

## <a name="next-steps"></a>Passaggi successivi

Leggere alcuni esempi di esiti fiscali nel contesto di una trasformazione cloud.

> [!div class="nextstepaction"]
> [Esempi di esiti fiscali](./business-outcomes/fiscal-outcomes.md)