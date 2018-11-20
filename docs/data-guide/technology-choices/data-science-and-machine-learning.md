---
title: Scelta di una tecnologia di apprendimento automatico
description: ''
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 50167bafa49f8e6016f6ec12680db016830e2b81
ms.sourcegitcommit: 9293350ab66fb5ed042ff363f7a76603bf68f568
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577158"
---
# <a name="choosing-a-machine-learning-technology-in-azure"></a>Scelta di una tecnologia di apprendimento automatico in Azure

Il data science e l'apprendimento automatico costituiscono un carico di lavoro generalmente eseguito dai data scientist. Richiedono strumenti specializzati, molti dei quali appositamente progettati per il tipo di esplorazione interattiva dei dati e per le attività di modellazione che devono essere eseguite dai data scientist.

Le soluzioni di apprendimento automatico vengono compilate in modo iterativo e sono articolate in due fasi distinte:
* Preparazione e modellazione dei dati.
* Distribuzione e utilizzo di servizi predittivi.

## <a name="tools-and-services-for-data-preparation-and-modeling"></a>Strumenti e servizi per la preparazione e la modellazione dei dati
I data scientist preferiscono in genere eseguire operazioni sui dati usando codice personalizzato scritto in Python o R. Questo codice viene solitamente eseguito in modo interattivo e usato dai data scientist per eseguire query ed esplorare i dati, generando in questo modo visualizzazioni e statistiche utili a determinare le relazioni con essi. Esistono molti ambienti interattivi per R e Python che possono essere usati dai data scientist. L'ambiente preferito è costituito dai **notebook di Jupyter**, che offrono una shell basata su browser con cui i data scientist possono creare file di *notebook* contenenti codice R o Phyton e testo di markdown. Questo ambiente consente quindi di collaborare mediante la condivisione di codice e risultati in un unico documento.

Altri strumenti comunemente usati includono:
* **Spyder**: l'ambiente di sviluppo interattivo per Python fornito con la distribuzione Anaconda Python.
* **R Studio**: un ambiente di sviluppo interattivo per il linguaggio di programmazione R.
* **Visual Studio Code**: un ambiente di codifica multipiattaforma leggero che supporta Python e i framework comunemente usati per l'apprendimento automatico e lo sviluppo con intelligenza artificiale.

Oltre a questi strumenti, i data scientist possono usare anche i servizi di Azure per semplificare la gestione del codice e dei modelli.

### <a name="azure-notebooks"></a>Notebook di Azure
Azure Notebooks è un servizio online basato su notebook di Jupyter che consente ai data scientist di creare, eseguire e condividere notebook di Jupyter in librerie basate sul cloud.

Vantaggi principali:

* Il servizio è gratuito: non è necessaria una sottoscrizione di Azure.
* Non è necessario installare in locale Jupyter né le distribuzioni R o Python di supporto: è sufficiente usare un browser.
* È possibile gestire le librerie online e accedervi da qualsiasi dispositivo.
* È possibile condividere i notebook con i collaboratori.

Considerazioni:

* Non è possibile accedere ai notebook in modalità offline.
* Le funzionalità di elaborazione limitate del servizio di notebook gratuito possono non essere sufficienti per eseguire il training di modelli complessi o di grandi dimensioni.

### <a name="data-science-virtual-machine"></a>Macchina virtuale di data science
La macchina virtuale di data science è un'immagine di macchina virtuale di Azure che include gli strumenti e i framework comunemente usati dai data scientist, tra cui R, Python, notebook di Jupyter, Visual Studio Code e librerie per modellazione dell'apprendimento automatico, come Microsoft Cognitive Toolkit. Questi strumenti possono richiedere una procedura di installazione particolarmente lunga e complessa e contengono molte interdipendenze che spesso determinano problemi di gestione delle versioni. La disponibilità di un'immagine preinstallata può ridurre il tempo impiegato dai data scientist per risolvere i problemi relativi all'ambiente, consentendo loro di concentrarsi sulle attività di esplorazione e modellazione dei dati che devono eseguire.

Vantaggi principali:
* Riduzione del tempo necessario per installare, gestire e risolvere i problemi relativi ai framework e agli strumenti di data science.
* Sono incluse le versioni più recenti di tutti i framework e gli strumenti comuni.
* Le opzioni della macchina virtuale includono immagini altamente scalabili con funzionalità GPU per la modellazione di grandi quantità di dati.

Considerazioni:
* La macchina virtuale non è accessibile in modalità offline.
* L'esecuzione di una macchina virtuale comporta costi di Azure ed è quindi necessario fare attenzione ad eseguirla solo quando è effettivamente necessario.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning è un servizio basato sul cloud per la gestione di modelli ed esperimenti di apprendimento automatico. Include un servizio di sperimentazione che tiene traccia degli script di training per la preparazione e la modellazione dei dati, oltre a mantenere la cronologia di tutte le esecuzioni per poter confrontare le prestazioni del modello nelle varie iterazioni. Uno strumento client multipiattaforma denominato Azure Machine Learning Workbench offre un'interfaccia centrale per la gestione e la cronologia degli script, pur continuando a offrire ai data scientist la possibilità di creare script in qualsiasi altro strumento, ad esempio notebook di Jupyter o Visual Studio Code.

In Azure Machine Learning Workbench è possibile usare gli strumenti di preparazione interattiva dei dati per semplificare le comuni attività di trasformazione dei dati ed è possibile configurare l'ambiente di esecuzione dello script per eseguire script di training dei modelli in locale, in un contenitore Docker scalabile o in Spark.

Quando si è pronti per distribuire il modello, è possibile usare l'ambiente Workbench per creare il pacchetto del modello e distribuirlo come servizio Web in un contenitore Docker, in Spark in Azure HDinsight, in Microsoft Machine Learning Server o in SQL Server. Il servizio Gestione modelli di Machine Learning consente quindi di tenere traccia e gestire le distribuzioni di modelli nel cloud, in dispositivi perimetrali o a livello aziendale.

Vantaggi principali:

* Gestione centrale degli script e della cronologia di esecuzione, semplificando il confronto tra più versioni dei modelli.
* Trasformazione interattiva dei dati tramite un editor visivo.
* Semplicità di distribuzione e gestione dei modelli nel cloud o in dispositivi perimetrali.

Considerazioni:
* Richiede una certa conoscenza del modello di gestione dei modelli e dell'ambiente dello strumento Workbench.

### <a name="azure-batch-ai"></a>Azure Batch per intelligenza artificiale

Azure Batch per intelligenza artificiale consente di eseguire esperimenti di apprendimento automatico in parallelo e di eseguire il training dei modelli su larga scala in un cluster di macchine virtuali con GPU. Batch per intelligenza artificiale consente anche di aumentare le prestazioni dei processi di apprendimento avanzato in GPU in cluster tramite framework come Cognitive Toolkit, Caffe, Chainer, e TensorFlow. 

Gestione modelli di Azure Machine Learning consente di accettare modelli dal training di Batch per intelligenza artificiale per distribuirli, gestirli e monitorarli. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

Azure Machine Learning Studio è un ambiente di sviluppo visivo basato sul cloud per la creazione di esperimenti di dati, il training di modelli di apprendimento automatico e la relativa pubblicazione come servizi Web in Azure. L'interfaccia visiva con trascinamento della selezione consente ai data scientist e agli utenti esperti di creare rapidamente soluzioni di apprendimento automatico, supportando al tempo stesso la logica personalizzata di R e Python e un'ampia gamma di algoritmi statistici predefiniti e tecniche per attività di modellazione dell'apprendimento automatico, oltre al supporto incorporato per notebook di Jupyter.

Vantaggi principali:

* L'interfaccia visiva interattiva consente la modellazione dell'apprendimento automatico con una quantità minima di codice.
* Sono integrati i notebook di Jupyter per l'esplorazione dei dati.
* Distribuzione diretta di modelli sottoposti a training come servizi Web di Azure.

Considerazioni:

* Scalabilità limitata. La dimensione massima di un set di dati di training è di 10 GB.
* Solo online. Nessun ambiente di sviluppo offline.

## <a name="tools-and-services-for-deploying-machine-learning-models"></a>Strumenti e servizi per la distribuzione di modelli di apprendimento automatico

Dopo che un data scientist ha creato un modello di apprendimento automatico, è necessario in genere distribuirlo e usarlo all'interno di applicazioni o in altri flussi di dati. I modelli di apprendimento automatico hanno molte potenziali destinazioni di distribuzione.

### <a name="spark-on-azure-hdinsight"></a>Spark in Azure HDInsight

Apache Spark include Spark MLlib, composto da un framework e da una libreria di modelli di apprendimento automatico. La libreria Microsoft Machine Learning for Spark (MMLSpark) offre anche il supporto per algoritmi di apprendimento automatico per modelli predittivi in Spark.

Vantaggi principali:

* Spark è una piattaforma distribuita che offre alta scalabilità per processi di apprendimento automatico con volumi elevati.
* È possibile distribuire i modelli direttamente da Azure Machine Learning Workbench in Spark in HDinsight e gestirli tramite il servizio Gestione modelli di Azure Machine Learning.

Considerazioni:

* Spark viene eseguito in un cluster HDinsght che comporta addebiti per tutto il tempo in cui è in esecuzione. Se il servizio di apprendimento automatico viene usato solo in modo occasionale, può quindi determinare costi non necessari.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/) è una piattaforma di analisi basata su Apache Spark. Questa opzione può essere considerata una sorta di "Spark distribuito come servizio". È il modo più semplice per usare Spark nella piattaforma Azure. Per l'apprendimento automatico, è possibile usare [MLFlow](https://www.mlflow.org/), [Databricks Runtime ML](https://docs.azuredatabricks.net/user-guide/clusters/mlruntime.html), Apache Spark MLlib e altri strumenti. Per altre informazioni, vedere la documentazione sull'[apprendimento automatico in Azure Databricks](https://docs.azuredatabricks.net/spark/latest/mllib/index.html). 

### <a name="web-service-in-a-container"></a>Servizio Web in un contenitore

È possibile distribuire un modello di apprendimento automatico come servizio Web Python in un contenitore Docker. In particolare, è possibile distribuire il modello in Azure o in un dispositivo periferico, dove può essere usato in locale con i dati con cui opera.

Vantaggi principali:

* I contenitori offrono uno strumento semplice ed economicamente conveniente per creare pacchetti di servizi e distribuirli.
* La possibilità di distribuire in un dispositivo periferico consente di spostare la logica predittiva più vicina ai dati.
* È possibile distribuire da Azure Machine Learning Workbench direttamente in un contenitore.

Considerazioni:

* Questo modello di distribuzione è basato su contenitori Docker ed è quindi necessario avere familiarità con questa tecnologia prima di distribuire un servizio Web in questo modo.

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

Machine Learning Server (in precedenza Microsoft R Server) è una piattaforma scalabile per codice R e Python, appositamente progettata per scenari di apprendimento automatico.

Vantaggi principali:

* Scalabilità elevata.
* Possibilità di distribuzione diretta da Azure Machine Learning Workbench.

Considerazioni:

* È necessario distribuire e gestire Machine Learning Server a livello aziendale.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Microsoft SQL Server supporta R e Python in modo nativo, offrendo la possibilità di incapsulare i modelli di apprendimento automatico compilati in queste lingue come funzioni Transact-SQL in un database.

Vantaggi principali:

* Possibilità di incapsulare la logica predittiva in una funzione di database, con conseguente possibilità di includere facilmente anche la logica a livello dati.

Considerazioni:

* Si presuppone la presenza di un database di SQL Server come livello dati per l'applicazione.

### <a name="azure-machine-learning-web-service"></a>Servizio Web Azure Machine Learning

Quando si crea un modello di apprendimento automatico tramite Azure Machine Learning Studio, è possibile distribuirlo come servizio Web, che può essere quindi usato tramite un'interfaccia REST da qualsiasi applicazione client in grado di comunicare tramite HTTP.

Vantaggi principali:

* Semplicità di sviluppo e distribuzione.
* Portale di gestione dei servizi Web con metriche di monitoraggio di base.
* Supporto incorporato per chiamare servizi Web di Azure Machine Learning da Azure Data Lake Analytics, Azure Data Factory e Analisi di flusso di Azure.

Considerazioni:

* È disponibile solo per modelli compilati con Azure Machine Learning Studio.
* I modelli sottoposti a training con accesso solo basato sul Web non possono essere eseguiti in locale o in modalità offline.

