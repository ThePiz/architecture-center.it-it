---
title: Monitoraggio di Azure Databricks con Monitoraggio di Azure
description: Libreria Scala per abilitare il monitoraggio di Azure Databricks in Azure Log Analytics
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: 93798ccf74735a880eab2999008b1495e6a63e10
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640533"
---
# <a name="monitoring-azure-databricks"></a>Monitoraggio di Azure Databricks

[Azure Databricks](/azure/azure-databricks/) è un servizio di analisi veloce e potente basato su [Apache Spark](https://spark.apache.org/) che consente di sviluppare e distribuire rapidamente soluzioni di intelligenza artificiale e analisi dei Big Data. Molti utenti sfruttano la semplicità dei notebook nelle proprie soluzioni Azure Databricks. Per gli utenti che richiedono opzioni di elaborazione più affidabili, Azure Databricks supporta l'esecuzione distribuita di codice dell'applicazione personalizzato.

Il monitoraggio costituisce un aspetto essenziale di qualsiasi soluzione a livello di produzione e Azure Databricks offre potenti funzionalità per il monitoraggio di metriche dell'applicazione personalizzate, eventi di query in streaming e messaggi di log dell'applicazione. Azure Databricks può inviare questi dati di monitoraggio a diversi servizi di registrazione.

Gli articoli seguenti illustrano come inviare i dati di monitoraggio da Azure Databricks a [Monitoraggio di Azure](/azure/azure-monitor/overview), la piattaforma dei dati di monitoraggio per Azure. È consigliabile leggere questi articoli nell'ordine indicato.

1. [Configurare Azure Databricks per l'invio delle metriche a Monitoraggio di Azure](./configure-cluster.md)
1. [Inviare i log dell'applicazione di Azure Databricks a Monitoraggio di Azure](./application-logs.md)
1. [Usare i dashboard per visualizzare le metriche di Azure Databricks](./dashboards.md)

La libreria di codice fornita con questi articoli consente di estendere la funzionalità di base di monitoraggio di Azure Databricks per l'invio di metriche, eventi e informazioni di monitoraggio Spark a Monitoraggio di Azure.

Questi articoli e la libreria di codice fornita sono destinati a sviluppatori di soluzioni Apache Spark e Azure Databricks. Il codice deve essere compilato in file JAR (Java Archive) e quindi distribuito in un cluster Azure Databricks. Il codice è una combinazione di [Scala](https://www.scala-lang.org/) e Java, con un set corrispondente di file POM (Project Object Model) [Maven](https://maven.apache.org) per la compilazione dei file JAR di output. La conoscenza di Java, Scala e Maven è un prerequisito consigliato.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, compilare la libreria di codice e distribuirla nel cluster Azure Databricks.

> [!div class="nextstepaction"]
> [Configurare Azure Databricks per l'invio delle metriche a Monitoraggio di Azure](./configure-cluster.md)