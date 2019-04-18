---
title: Scelta di una tecnologia di apprendimento automatico
description: Confrontare le opzioni disponibili per creare, distribuire e gestire i modelli di Machine Learning. Decidere quali prodotti Microsoft scegliere per la soluzione.
author: MikeWasson
ms.date: 03/06/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: e4c81add1a97254f427d67584ff7e2a4799f84a9
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640907"
---
# <a name="what-are-the-machine-learning-products-at-microsoft"></a>Informazioni sui prodotti di machine learning forniti da Microsoft

Machine Learning è una tecnica di analisi scientifica dei dati che consente ai computer di usare i dati esistenti per prevedere comportamenti, tendenze e risultati futuri. Con l'apprendimento automatico, i computer apprendono senza essere programmati in modo esplicito.

Soluzioni di Machine learning compilate in modo iterativo e sono fasi distinte:

- Preparazione dei dati
- Sperimentazione e training dei modelli
- Distribuzione di modelli con training
- La gestione di modelli distribuiti

Microsoft offre un'ampia gamma di opzioni di prodotto per preparare, compilare, distribuire e gestire modelli di machine learning. Confrontare questi prodotti e scegliere ciò che è necessario per sviluppare le soluzioni di machine learning nel modo più efficace.

## <a name="cloud-based-options"></a>Opzioni basate su cloud

Le opzioni seguenti sono disponibili per Machine Learning nel cloud di Azure.

| Opzioni&nbsp;cloud | Che cos'è | Cosa permette di fare |
|-|-|-|
| [Servizio Azure Machine Learning](#azure-machine-learning-service) | Servizio cloud gestito per machine learning  | Eseguire il training, distribuire e gestire i modelli in Azure con Python e CLI |
| [Azure Machine Learning Studio](#azure-machine-learning-studio) | Trascinare&ndash;e&ndash;interfaccia visiva di trascinamento per machine learning | Compilare, provare e distribuire i modelli usando algoritmi preconfigurati |

Se si desidera utilizzare preesistente, modelli di machine learning e intelligenza artificiale [servizi cognitivi di Azure](#azure-cognitive-services) consente di aggiungere facilmente funzionalità intelligenti alle tue applicazioni.

## <a name="on-premises-options"></a>Opzioni in locale

Le opzioni seguenti sono disponibili per Machine Learning in locale. Anche i server locali possono essere eseguiti in una macchina virtuale nel cloud.

| Opzioni&nbsp;locali | Che cos'è | Cosa permette di fare |
|-|-|-|
| [SQL Server Machine Learning Services](#sql-server-machine-learning-services) | Motore di analisi incorporato in SQL | Creare e distribuire modelli all'interno di SQL Server |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | Server enterprise autonomo per l'analisi predittiva | Creare e distribuire modelli su dati pre-elaborati |

## <a name="development-platforms-and-tools"></a>Strumenti e piattaforme di sviluppo

Le seguenti piattaforme di sviluppo e gli strumenti sono disponibili per machine learning.

| Piattaforme/tools | Che cos'è | Cosa permette di fare |
|-|-|-|
| [Macchina virtuale di data science di Azure](#azure-data-science-virtual-machine) | Macchina virtuale con strumenti di data science preinstallati | Sviluppare soluzioni di machine learning in un ambiente di pre-configurata |
| [Azure Databricks](#azure-databricks) | Piattaforma di analisi basata su Spark | Compilare e distribuire modelli e flussi di lavoro dei dati |
| [ML.NET](#mlnet) | Open source e multipiattaforma di machine learning SDK | Sviluppare soluzioni di machine learning per le applicazioni .NET |
| [Windows ML](#windows-ml) | Piattaforma di Windows 10 machine learning | Valutare modelli con training su un dispositivo Windows 10 |

## <a name="azure-machine-learning-service"></a>Servizio Azure Machine Learning

[Il servizio Azure Machine Learning](/azure/machine-learning/service/overview-what-is-azure-ml.md) è un servizio cloud completamente gestito usato per eseguire il training, distribuire e gestire modelli di apprendimento automatico su larga scala. Supporta completamente le tecnologie open source e consente pertanto di usare decine di migliaia di pacchetti Python open source quali TensorFlow, PyTorch e scikit-learn. Sono inoltre disponibili strumenti avanzati, come [Azure Notebooks](https://notebooks.azure.com/), i [notebook di Jupyter](http://jupyter.org) o l'estensione [Azure Machine Learning per Visual Studio Code](https://aka.ms/vscodetoolsforai), per esplorare e trasformare i dati e quindi eseguire il training dei modelli e distribuirli con facilità. Il servizio Azure Machine Learning include anche funzionalità che consentono di automatizzare la generazione e l'ottimizzazione dei modelli in modo semplice, efficiente e accurato.

Usare il servizio di Azure Machine Learning per eseguire il training, distribuire e gestire modelli di machine learning con Python e CLI su scala cloud.

Provare la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

|||
|-|-|
|**Tipo**                   |Machine learning soluzioni basate su cloud|
|**Lingue supportate**    |Python|
|**Fasi di Machine learning**|Preparazione dei dati<br>Training dei modelli<br>Distribuzione<br>Gestione|
|**Vantaggi principali**           |Gestione centrale degli script e della cronologia di esecuzione, semplificando il confronto tra più versioni dei modelli.<br/><br/>Semplicità di distribuzione e gestione dei modelli nel cloud o in dispositivi perimetrali.|
|**Considerazioni**         |È necessaria una certa conoscenza del modello di gestione dei modelli.|

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

[Azure Machine Learning Studio](/azure/machine-learning/studio/) offre un'area di lavoro interattiva e visiva utilizzabile per compilare, testare e distribuire in modo facile e rapido i modelli usando algoritmi di machine learning predefiniti. Machine Learning Studio pubblica i modelli come servizi Web che possono essere facilmente usati da applicazioni personalizzate o strumenti di Business Intelligence, ad esempio Excel.
Non è necessaria alcuna programmazione: è possibile creare il modello di machine learning connettendo i set di dati e i moduli di analisi in una canvas interattiva, per poi distribuirlo con pochi clic.

Usare Machine Learning Studio quando si desidera sviluppare e distribuire modelli che non richiedono alcun codice.

Provare [Azure Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2&target=_blank), per cui sono disponibili opzioni a pagamento o gratuite.

|||
|-|-|
|**Tipo**                   |Basato sul cloud, trascinamento e rilascio soluzione di machine learning|
|**Lingue supportate**    |Python, R|
|**Fasi di Machine learning**|Preparazione dei dati<br>Training dei modelli<br>Distribuzione<br>Gestione|
|**Vantaggi principali**           |L'interfaccia visiva interattiva consente la modellazione dell'apprendimento automatico con una quantità minima di codice.<br/><br/>Sono integrati i notebook di Jupyter per l'esplorazione dei dati.<br/><br/>Distribuzione diretta di modelli sottoposti a training come servizi Web di Azure.|
|**Considerazioni**         |Scalabilità limitata. La dimensione massima di un set di dati di training è di 10 GB.<br/><br/>Solo online. Nessun ambiente di sviluppo offline.|

## <a name="azure-cognitive-services"></a>Servizi cognitivi di Azure

[Servizi cognitivi di Azure](/azure/cognitive-services/welcome) è un set di API che consente di creare app che usano metodi di comunicazione naturali. Queste API consentono alle app di vedere, sentire, parlare, comprendere e interpretare le esigenze degli utenti con poche righe di codice. È possibile aggiungere con facilità funzionalità intelligenti alle app, ad esempio:

- Rilevamento di emozioni e sentiment
- Riconoscimento visivo e vocale
- Comprensione del linguaggio (LUIS, Language Understanding)
- Conoscenza e ricerca

Usare i Servizi cognitivi per sviluppare app tra dispositivi e piattaforme diverse. Le API vengono migliorate continuamente e sono facili da installare.

|||
|-|-|
|**Tipo**                   |API per la compilazione di applicazioni intelligenti|
|**Lingue supportate**    |molte opzioni a seconda del servizio|
|**Fasi di Machine learning**|Distribuzione|
|**Vantaggi principali**           |Incorporare funzionalità di machine learning nelle applicazioni che utilizzano modelli con training preliminare.<br/><br/>Ampia gamma di modelli per i metodi di comunicazione naturali con sintesi vocale e visione artificiale.|
|**Considerazioni**         |Modelli di training preliminare e non sono personalizzabili.|

## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services

[SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) aggiunge analisi statistiche, visualizzazione dei dati e analisi predittiva in R e Python per i dati relazionali nel database SQL Server. Librerie di R e Python di Microsoft includono creare modelli e algoritmi di machine learning, che possono essere eseguito in parallelo e su larga scala, in SQL Server.

Usare SQL Server Machine Learning Services quando si necessita di intelligenza artificiale incorporata e di analisi predittive su dati relazionali in SQL Server.

|||
|-|-|
|**Tipo**                   |Analitica predittiva in locale per i dati relazionali|
|**Lingue supportate**    |Python, R|
|**Fasi di Machine learning**|Preparazione dei dati<br>Training dei modelli<br>Distribuzione|
|**Vantaggi principali**           |Possibilità di incapsulare la logica predittiva in una funzione di database, con conseguente possibilità di includere facilmente anche la logica a livello dati.|
|**Considerazioni**         |Si presuppone la presenza di un database di SQL Server come livello dati per l'applicazione.|

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server) è un server aziendale per l'hosting e la gestione di carichi di lavoro paralleli e distribuiti di processi R e Python. Microsoft Machine Learning Server viene eseguito su Linux, Windows, Hadoop e Apache Spark ed è disponibile anche su [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Fornisce un motore di esecuzione per le soluzione create tramite [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package), e [pacchetti MicrosoftML](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package), ed estende R open source e Python con il supporto per analisi ad alte prestazioni, analisi statistiche, machine learning e set di dati di grandi dimensioni. La funzionalità viene fornita tramite pacchetti proprietari installati insieme al server. Per lo sviluppo è possibile usare IDE come [R Tools per Visual Studio](https://www.visualstudio.com/vs/rtvs/) e [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Usare Microsoft Machine Learning Server quando è necessario compilare e rendere operativi i modelli creati con R e Python in un server, o distribuire training R e Python su larga scala in un cluster Hadoop o Spark.

|||
|-|-|
|**Tipo**                   |Server aziendale locale per analitica predittiva|
|**Lingue supportate**    |Python, R|
|**Fasi di Machine learning**|Training dei modelli<br>Distribuzione|
|**Vantaggi principali**           |Scalabilità elevata.|
|**Considerazioni**         |È necessario distribuire e gestire Machine Learning Server a livello aziendale.|

## <a name="azure-data-science-virtual-machine"></a>Macchina virtuale di data science di Azure

[Macchina virtuale di data science di Azure](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) è un ambiente di macchina virtuale personalizzato nel cloud di Microsoft Azure, creato in modo specifico per l'analisi scientifica dei dati. Include diversi strumenti comuni per l'analisi scientifica dei dati e altri strumenti preinstallati e preconfigurati per implementare rapidamente la creazione di applicazioni intelligenti per l'analisi avanzata.

La macchina virtuale di data science è supportata come destinazione per il servizio Machine Learning di Azure.
È disponibile nelle versioni per Windows e Linux Ubuntu. Il servizio Azure Machine Learning non è supportato in Linux CentOS.
Per informazioni sulla versione specifica e un elenco di quanto  incluso, vedere [Introduzione a Data Science Virtual Machine di Azure](/azure/machine-learning/data-science-virtual-machine/overview.md).

Usare la macchina virtuale di data science quando è necessario eseguire oppure ospitare processi in un singolo nodo. o se è necessario aumentare in modo remoto le prestazioni di elaborazione in un singolo computer.

|||
|-|-|
|**Tipo**                   |Ambiente di macchina virtuale personalizzata per l'analisi scientifica dei dati|
|**Vantaggi principali**           |Riduzione del tempo necessario per installare, gestire e risolvere i problemi relativi ai framework e agli strumenti di data science.<br/><br/>Sono incluse le versioni più recenti di tutti i framework e gli strumenti comuni.<br/><br/>Le opzioni della macchina virtuale includono immagini altamente scalabili con funzionalità GPU per la modellazione di grandi quantità di dati.|
|**Considerazioni**         |La macchina virtuale non è accessibile in modalità offline.<br/><br/>L'esecuzione di una macchina virtuale comporta costi di Azure ed è quindi necessario fare attenzione ad eseguirla solo quando è effettivamente necessario.|

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) è una piattaforma di analisi basata su Apache Spark ottimizzata per la piattaforma dei servizi cloud di Microsoft Azure. Databricks è integrato con Azure per offrire l'installazione con un clic, flussi di lavoro semplificati e un'area di lavoro interattiva che consente la collaborazione tra data scientist, ingegneri dei dati e business analyst.
Usare i codici Python, R, Scala e SQL nei notebook basati sul Web per eseguire una query, visualizzare e modellare i dati.

Usare Databricks quando si desidera collaborare alla creazione di soluzioni di machine learning in Spark Apache.

|||
|-|-|
|**Tipo**                   |Piattaforma di analisi basata su Apache Spark|
|**Lingue supportate**    |Python, R, Scala, SQL|
|**Fasi di Machine learning**|Query sui dati<br>Training dei modelli|

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) è un framework di machine learning gratuito, open source e multipiattaforma che consente di compilare soluzioni personalizzate di machine learning e di integrarle nelle applicazioni .NET.

Usare ML.NET quando si desidera integrare soluzioni di machine learning nelle applicazioni .NET.

|||
|-|-|
|**Tipo**                   |Framework open source per lo sviluppo di applicazioni personalizzate di machine learning|
|**Lingue supportate**    |.NET|

## <a name="windows-ml"></a>Windows ML

[Machine Learning Windows](https://docs.microsoft.com/windows/uwp/machine-learning/) motore a inferenza consente di usare sottoposto a training di machine learning i modelli nelle proprie applicazioni, la valutazione dei modelli con Training in locale nei dispositivi Windows 10.

Usare Windows ML quando si desidera utilizzare modelli di Machine Learning sottoposti a training all'interno delle proprie applicazioni Windows.

|||
|-|-|
|**Tipo**                   |Motore a inferenza per i modelli sottoposti a training in dispositivi Windows|
|**Lingue supportate**    |C#/C++, JavaScript|

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su tutti i prodotti di sviluppo per intelligenza artificiale (AI) disponibili da Microsoft, vedere [piattaforma Microsoft AI](https://www.microsoft.com/ai)
- Per il training sulle modalità di sviluppo di soluzioni di intelligenza artificiale, vedere [Microsoft AI School](https://aischool.microsoft.com/learning-paths)
