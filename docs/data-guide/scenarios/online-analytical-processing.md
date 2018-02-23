---
title: OLAP (Online Analytical Processing)
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: f5ceea9c9dd03812e92fff811e54316edc22b59c
ms.sourcegitcommit: 90cf2de795e50571d597cfcb9b302e48933e7f18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="online-analytical-processing-olap"></a>OLAP (Online Analytical Processing)

OLAP (Online Analytical Processing) è una tecnologia che consente di organizzare i database aziendali di grandi dimensioni e supporta l'esecuzione di analisi complesse. Può essere usata per eseguire query di analisi complesse senza influire negativamente sui sistemi transazionali.

I database usati dalle aziende per archiviare tutti i record e le transazioni sono detti database [OLTP (Online Transaction Processing)](online-transaction-processing.md). Questi database includono in genere record che sono stati inseriti singolarmente e contengono una grande quantità di informazioni importanti per l'organizzazione. I database usati per l'elaborazione OLTP, tuttavia, non sono stati progettati per l'analisi. Il recupero di informazioni da questi database richiede quindi un impegno notevole in termini di tempo e prestazioni. Per facilitare l'estrazione di queste informazioni di business intelligence sono stati progettati i sistemi OLAP, che sono in grado di offrire prestazioni molto elevate. I database OLAP sono infatti ottimizzati per intensi carichi di lavoro in lettura e carichi di lavoro ridotti in scrittura.

![OLAP in Azure](./images/olap-data-pipeline.png) 

## <a name="when-to-use-this-solution"></a>Quando usare questa soluzione

Prendere in considerazione la tecnologia OLAP negli scenari seguenti:

- È necessario eseguire con rapidità query ad hoc e di analisi complesse senza influire negativamente sui sistemi OLTP. 
- Si vuole offrire agli utenti aziendali una soluzione semplice per generare report dai dati.
- Si vuole fornire una serie di aggregazioni che consenta agli utenti di ottenere risultati rapidi e coerenti. 

OLAP è particolarmente utile per applicare calcoli di aggregazione a grandi quantità di dati. I sistemi OLAP sono ottimizzati per gli scenari che richiedono intensi carichi di lavoro in lettura, ad esempio per le applicazioni di analisi e business intelligence. OLAP consente agli utenti di segmentare i dati multidimensionali in sezioni visualizzabili in due dimensioni, ad esempio in una tabella pivot, o di filtrare i dati in base a valori specifici. Questo processo di segmentazione dei dati può essere eseguito indipendentemente dal fatto che i dati si trovino in diverse origini. Ciò consente agli utenti di individuare tendenze, identificare schemi ricorrenti ed esplorare i dati senza dover conoscere i dettagli dell'analisi dei dati tradizionale.

I [modelli semantici](../concepts/semantic-modeling.md) consentono agli utenti aziendali di identificare relazioni complesse tra i dati ed eseguirne l'analisi più rapidamente.

## <a name="challenges"></a>Problematiche

Nonostante tutti i vantaggi offerti, i sistemi OLAP presentano alcune problematiche:

- Mentre i dati nei sistemi OLTP vengono costantemente aggiornati tramite transazioni provenienti da varie origini, gli archivi dati OLAP vengono in genere aggiornati a intervalli meno frequenti, a seconda delle esigenze aziendali. Ciò significa che i sistemi OLAP sono più adatti per prendere decisioni aziendali strategiche anziché reagire in modo tempestivo a eventuali cambiamenti. È inoltre necessario prevedere un certo livello di pulizia e orchestrazione dei dati per mantenere aggiornati gli archivi dati OLAP.
- A differenza delle tabelle tradizionali, normalizzate e relazionali presenti nei sistemi OLTP, i modelli di dati OLAP tendono a essere multidimensionali. Questo rende difficile o impossibile il mapping diretto a modelli di tipo entità-relazione o orientati a oggetti, dove ogni attributo è mappato a una singola colonna. In alternativa alla normalizzazione tradizionale, i sistemi OLAP usano in genere uno schema snowflake o a stella.

## <a name="olap-in-azure"></a>OLAP in Azure

In Azure, i dati contenuti nei sistemi OLTP, come il database SQL di Azure, vengono copiati in un sistema OLAP, ad esempio [Azure Analysis Services](/azure/analysis-services/analysis-services-overview). Gli strumenti per l'esplorazione e la visualizzazione dei dati, come [Power BI](https://powerbi.microsoft.com), Excel e strumenti di terze parti, si connettono ai server di Analysis Services e forniscono agli utenti informazioni dettagliate, altamente interattive e graficamente efficaci, dei dati presenti nel modello. Il flusso dei dati da OLTP a OLAP viene in genere orchestrato tramite SQL Server Integration Services, che è possibile eseguire usando [Azure Data Factory](/azure/data-factory/concepts-integration-runtime).

## <a name="technology-choices"></a>Scelte di tecnologia

- [Scelta di un archivio dati OLAP in Azure](../technology-choices/olap-data-stores.md)

