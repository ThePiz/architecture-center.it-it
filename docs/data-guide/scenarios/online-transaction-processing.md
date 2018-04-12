---
title: OLTP (Online Transaction Processing)
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 07e7f680c8ee5e8589ff7cd2236ff95f6ee84f4c
ms.sourcegitcommit: c441fd165e6bebbbbbc19854ec6f3676be9c3b25
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2018
---
# <a name="online-transaction-processing-oltp"></a>OLTP (Online Transaction Processing)

La gestione di [dati transazionali](../concepts/transactional-data.md) tramite sistemi informatici prende il nome di Online Transaction Processing (OLTP). I sistemi OLTP registrano le interazioni aziendali man mano che si verificano nell'ambito delle operazioni giornaliere eseguite all'interno dell'organizzazione e supportano la creazione di query su questi dati per l'inserimento di inferenze.

![OLTP in Azure](./images/oltp-data-pipeline.png)

## <a name="when-to-use-this-solution"></a>Quando usare questa soluzione

Scegliere OLTP nei casi in cui è necessario elaborare e archiviare in modo efficiente transazioni aziendali, nonché renderle immediatamente disponibili alle applicazioni client in modo coerente. Usare questa architettura anche quando eventuali ritardi tangibili nell'elaborazione avrebbero un impatto negativo sulle operazioni quotidiane dell'azienda.

I sistemi OLTP sono progettati per elaborare e archiviare in modo efficiente le transazioni, nonché per eseguire query sui dati transazionali. L'obiettivo dei sistemi OLTP di elaborare e archiviare in modo efficiente singole transazioni viene parzialmente soddisfatto dalla normalizzazione dei dati, ovvero dalla suddivisione dei dati in blocchi più piccoli meno ridondanti. Questo meccanismo favorisce l'efficienza perché consente al sistema OLTP di elaborare grandi quantità di transazioni in modo indipendente ed evita le attività di elaborazione altrimenti necessarie per mantenere l'integrità dei dati in presenza di dati ridondanti.

## <a name="challenges"></a>Problematiche
L'implementazione e l'utilizzo di un sistema OLTP può creare alcune problematiche:

- I sistemi OLTP non sono sempre adatti per la gestione di aggregazioni di grandi quantità di dati, sebbene esistano alcune eccezioni, come una soluzione basata su SQL Server ben pianificata. L'analisi dei dati, che si basa su calcoli aggregati di milioni di singole transazioni, richiede l'utilizzo di moltissime risorse per un sistema OLTP. L'esecuzione dei calcoli, infatti, può essere molto lunga e causare rallentamenti bloccando le altre transazioni nel database.
- Quando vengono eseguite operazioni di analisi e report su dati altamente normalizzati, le query tendono a essere complesse, poiché la maggior parte delle query deve denormalizzare i dati per mezzo di join. Nei sistemi OLTP, inoltre, le convenzioni di denominazione per oggetti di database tendono a essere concise e sintetiche. Questa elevata normalizzazione, associata alle concisione delle convenzioni di denominazione, rende più difficile per gli utenti aziendali eseguire query sui sistemi OLTP senza l'aiuto di un amministratore di database o di uno sviluppatore di dati.
- L'archiviazione della cronologia delle transazioni per un periodo illimitato e l'archiviazione di una quantità eccessiva di dati in una tabella può generare inoltre un rallentamento delle prestazioni delle query, in funzione del numero di transazioni archiviate. Una possibile soluzione consiste nel mantenere una finestra temporale rilevante (ad esempio l'anno fiscale corrente) nel sistema OLTP e scaricare i dati storici su altri sistemi, ad esempio un data mart o un [data warehouse](../technology-choices/data-warehouses.md).

## <a name="oltp-in-azure"></a>OLTP in Azure

Le applicazioni come i siti Web ospitati nell'[app Web del servizio app](/azure/app-service/app-service-web-overview), le API REST in esecuzione nel servizio app o applicazioni desktop o per dispositivi mobili comunicano in genere con il sistema OLTP tramite un intermediario dell'API REST.

In pratica, la maggior parte dei carichi di lavoro non è esclusivamente OLTP ma tende a essere anche un [componente analitico](../scenarios/online-analytical-processing.md). Si assiste anche a una domanda crescente di strumenti per la creazione di report in tempo reale, ad esempio per l'esecuzione di report inerenti al sistema operativo. Questi processo prende il nome di Hybrid Transactional and Analytical Processing (HTAP). Per altre informazioni, vedere [OLAP (Online Analytical Processing)](../technology-choices/olap-data-stores.md).

## <a name="technology-choices"></a>Scelte di tecnologia

Archiviazione dati:

- [Database SQL di Azure](/azure/sql-database/)
- [SQL Server in una macchina virtuale di Azure](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
- [Database di Azure per MySQL](/azure/mysql/)
- [Database di Azure per PostgreSQL](/azure/postgresql/)

Per altre informazioni, vedere [Scelta di un archivio dati OLTP](../technology-choices/oltp-data-stores.md)

Origini dati:

- [Servizio app](/azure/app-service/)
- [App per dispositivi mobili](/azure/app-service-mobile/)

