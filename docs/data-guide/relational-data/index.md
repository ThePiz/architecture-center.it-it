---
title: Dati relazionali
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 499e7c632bd6bfee31c0625f4d647825107ea2bb
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111819"
---
# <a name="traditional-relational-database-solutions"></a>Soluzioni per i database relazionali tradizionali

I dati relazioni sono dati modellati tramite il modello relazionale, in cui i dati sono espressi sotto forma di tuple. Una *tupla* è un set di coppie attributo/valore, come ad esempio (itemid = 5, orderid = 1, item = "Chair", amount = 200.00). Un set di tuple che condividono gli stessi attributi prende il nome di *relazione*.

Le relazioni sono naturalmente rappresentate come tabelle, in cui ogni tupla è esposta come una riga della tabella. A differenza delle tuple, tuttavia, le righe hanno un ordine esplicito. Lo schema del database definisce le colonne (intestazioni) di ogni tabella, mentre ogni colonna viene definita con un nome e un tipo di dati per tutti i valori archiviati in tutte le righe della tabella che compongono la colonna.

![Esempio di dati che usano un database relazionale](../images/example-relational.png)

Un archivio dati che organizza i dati usando il modello relazionale prende il nome di database relazionale. Le chiavi primarie identificano in modo univoco le righe di una tabella. I campi di chiave esterna, invece, vengono usati in una tabella per fare riferimento a una riga di un'altra tabella facendo riferimento alla chiave primaria dell'altra tabella. Le chiavi esterne consentono di mantenere l'integrità referenziale verificando che le righe a cui una riga fa riferimento non vengano modificate o eliminate.

![Esempio di dati che usano un database relazionale](../images/example-relational2.png)

Per assicurare l'integrità dei dati, i database relazionali supportano vari tipi di vincoli:

- I vincoli univoci garantiscono che tutti i valori di una colonna siano univoci.

- I vincoli di chiave esterna applicano un collegamento tra i dati di due tabelle. Una chiave esterna fa riferimento alla chiave primaria o a un'altra chiave univoca di un'altra tabella. Un vincolo di chiave esterna applica anche l'integrità referenziale, impedendo modifiche che possano determinare valori di chiave esterna non validi.

- I vincoli CHECK, noti anche come vincoli di integrità di entità, limitano i valori che possono essere archiviati in una colonna o in relazione ai valori di altre colonne della stessa riga.

La maggior parte dei database relazionali usa il linguaggio SQL (Structured Query Language), che prevede un approccio dichiarativo alla creazione di query. In questo caso, la query descrive il risultato desiderato, non i passaggi necessari per eseguire la query, ed è il motore a decidere il modo migliore per eseguire la query. Questo processo è diverso dall'approccio procedurale, in cui il programma della query specifica in modo esplicito i passaggi di elaborazione. I database relazionali, tuttavia, possono archiviare routine di codice eseguibile sotto forma di stored procedure e funzioni, in modo da consentire una combinazione di approcci dichiarativi e procedurali.

Per migliorare le prestazioni delle query, i database relazionali si avvalgono di *indici*. Gli indici primari, usati dalla chiave primaria, definiscono l'ordine in cui sono posizionati i dati sul disco. Gli indici secondari offrono invece una combinazione alternativa di campi, in modo da poter eseguire query sulle righe desiderate in modo efficiente, senza dover riordinare l'intero volume di dati sul disco.

Poiché i database relazionali applicano l'integrità referenziale, ridimensionarli può risultare complicato. Qualsiasi operazione di query o di inserimento, infatti, può coinvolgere un numero qualsiasi di tabelle. È possibile estendere un database relazionale eseguendo il *partizionamento orizzontale* dei dati, ma questa operazione richiede un'attenta progettazione dello schema. Per altre informazioni, vedere il [Modello di partizionamento orizzontale](../../patterns/sharding.md).

Se i dati non sono relazionali o hanno requisiti non adatti a un database relazionale, prendere in considerazione un archivio dati [Non relazionale o NoSQL](../big-data/non-relational-data.md).
