---
title: Assegnazione dei punteggi in batch per i modelli Spark in Azure Databricks
description: Creare una soluzione scalabile per l'assegnazione del punteggio batch a un modello di classificazione Apache Spark in una pianificazione usando Azure Databricks.
author: njray
ms.date: 02/07/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 1b6f10edf098ed8d9fa14c16de113fc765372835
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58231408"
---
# <a name="batch-scoring-of-spark-models-on-azure-databricks"></a>Assegnazione dei punteggi in batch per i modelli Spark in Azure Databricks

Questa architettura di riferimento illustra come creare una soluzione scalabile per l'assegnazione del punteggio batch a un modello di classificazione Apache Spark in una pianificazione usando Azure Databricks, una piattaforma di strumenti analitici basata su Apache Spark ottimizzata per Azure. La soluzione può essere usata come modello che può essere generalizzato per altri scenari.

Un'implementazione di riferimento per questa architettura è disponibile in  [GitHub][github].

![Assegnazione dei punteggi in batch per i modelli Spark in Azure Databricks](./_images/batch-scoring-spark.png)

**Scenario**: un'azienda di un settore che fa largo uso di risorse vuole ridurre al minimo i costi e i tempi di inattività associati a guasti meccanici imprevisti. Usando i dati IoT raccolti dai computer aziendali, è possibile creare un modello di manutenzione predittiva. Questo modello consente all'azienda di gestire i componenti in maniera proattiva, riparandoli prima che si guastino. Ottimizzando l'uso dei componenti meccanici, l'azienda può controllare i costi e ridurre i tempi di inattività.

Un modello di manutenzione predittiva raccoglie i dati dai computer e conserva esempi cronologici dei guasti dei componenti. Il modello può quindi essere usato per monitorare lo stato corrente dei componenti e stimare se un determinato componente si guasterà nel prossimo futuro. Per casi d'uso e metodi di modellazione comuni, vedere [Guida di Azure AI per soluzioni di manutenzione predittiva][ai-guide].

Questa architettura di riferimento è progettata per carichi di lavoro che vengono attivati dalla presenza di nuovi dati dai computer dei componenti. L'elaborazione prevede i passaggi seguenti:

1. Inserire i dati dall'archivio dati esterno in un archivio dati di Azure Databricks.

2. Eseguire il training di un modello di Machine Learning trasformando i dati in un set di dati di training e quindi compilando un modello Spark MLlib. MLlib è costituito dagli algoritmi e le utilità di Machine Learning più comuni, ottimizzati per sfruttare i vantaggi delle funzionalità di scalabilità dei dati di Spark.

3. Applicare il modello sottoposto a training per stimare (classificare) i guasti dei componenti trasformando i dati in un set di dati di punteggio. Assegnare un punteggio ai dati con il modello Spark MLlib.

4. Archiviare i risultati nell'archivio dati di Databricks per l'utilizzo post-elaborazione.

Su  [GitHub][github] sono disponibili dei notebook per eseguire ognuna di queste attività.

## <a name="architecture"></a>Architettura

L'architettura definisce un flusso di dati interamente contenuto in [Azure Databricks][databricks] e basato su un set di [notebook][notebooks] eseguiti in sequenza. È costituita dai componenti seguenti:

**[File di dati][github]**. L'implementazione di riferimento usa un set di dati simulato contenuto in cinque file di dati statici.

**[Inserimento][notebooks]**. Il notebook di inserimento dati scarica i file di dati di input in una raccolta di set di dati di Databricks. In uno scenario reale, i dati dei dispositivi IoT verrebbero trasmessi a un archivio accessibile da Databricks, come un archivio BLOB di Azure o Azure SQL Server. Databricks supporta più [origini dati][data-sources].

**Pipeline di training**. Questo notebook esegue il notebook di progettazione delle funzionalità per creare un set di dati di analisi dai dati inseriti. Esegue quindi un notebook di compilazione modello che esegue il training del modello di Machine Learning usando la libreria di Machine Learning scalabile [Apache Spark MLlib][mllib].

**Pipeline di assegnazione del punteggio**. Questo notebook esegue il notebook di progettazione delle funzionalità per creare un set di dati di punteggio dai dati inseriti, quindi esegue il notebook di assegnazione del punteggio. Quest'ultimo usa il modello [Spark MLlib][mllib-spark] sottoposto a training per generare stime per le osservazioni nel set di dati di punteggio. Le stime vengono archiviate nell'archivio risultati, un nuovo set di dati nell'archivio dati di Databricks.

**Utilità di pianificazione**. Un [processo][job] pianificato di Databricks gestisce l'assegnazione del punteggio batch con il modello Spark. Il processo esegue il notebook della pipeline di assegnazione del punteggio, passando argomenti di variabile tramite i parametri del notebook per specificare i dettagli per la costruzione del set di dati di punteggio e la posizione in cui archiviare il set di dati dei risultati.

Lo scenario viene costruito come un flusso della pipeline. Ogni notebook è ottimizzato per l'esecuzione in un'impostazione batch per ciascuna operazione: inserimento, progettazione delle funzionalità, compilazione del modello e assegnazione del punteggio al modello. Per questo motivo il notebook di progettazione delle funzionalità è stato creato in modo da generare un set di dati generico per una qualsiasi delle operazioni di training, calibrazione, test o assegnazione del punteggio. In questo scenario viene usata una strategia di suddivisione temporale per queste operazioni, quindi i parametri del notebook vengono usati per impostare il filtro degli intervalli di date.

Poiché lo scenario crea una pipeline batch, viene fornito un set di notebook di esame facoltativi per esplorare l'output dei notebook di pipeline. Questi notebook sono disponibili nel repository GitHub:

- `1a_raw-data_exploring`
- `2a_feature_exploration`
- `2b_model_testing`
- `3b_model_scoring_evaluation`

## <a name="recommendations"></a>Consigli

Databricks è configurato in modo da consentire di caricare e distribuire i modelli sottoposti a training per eseguire stime con i nuovi dati. Per questo scenario è stato usato Databricks per offrire i vantaggi aggiuntivi seguenti:

- Supporto del Single Sign-On mediante credenziali di Azure Active Directory.
- Utilità di pianificazione dei processi per eseguire i processi per le pipeline di produzione.
- Notebook completamente interattivo con funzionalità di collaborazione, dashboard, API REST.
- Cluster illimitati scalabili a qualsiasi dimensione.
- Sicurezza avanzata, controllo degli accessi in base al ruolo e log di controllo.

Per interagire con il servizio Azure Databricks, usare l'interfaccia [Workspace][workspace] di Databricks in un Web browser oppure l'[Interfaccia della riga di comando][cli]. Accedere all'interfaccia della riga di comando di Databricks da qualsiasi piattaforma che supporti Python versioni da 2.7.9 a 3.6.

L'implementazione di riferimento usa i [notebook][notebooks] per eseguire le attività in sequenza. Ogni notebook archivia artefatti dei dati intermedi (set di dati di training, test, punteggio o risultati) nello stesso archivio dati dei dati di input. L'obiettivo è semplificarne l'uso in base alle esigenze del proprio caso d'uso. In pratica, occorre connettere l'origine dati all'istanza di Azure Databricks per consentire ai notebook di leggere e scrivere direttamente nell'archivio.

È possibile monitorare l'esecuzione dei processi tramite l'interfaccia utente di Databricks, l'archivio dati o l'[interfaccia della riga di comando][cli] di Databricks, secondo le necessità. Monitorare il cluster usando il [log eventi][log] e altre [metriche][metrics] fornite da Databricks.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Un cluster di Azure Databricks abilita la scalabilità automatica per impostazione predefinita, in modo che, in fase di runtime, Databricks possa riallocare dinamicamente i ruoli di lavoro all'account per le caratteristiche del processo. Alcune parti della pipeline potrebbero richiedere più risorse di calcolo di altre. Durante queste fasi del processo Databricks aggiunge altri ruoli di lavoro, rimuovendoli in seguito quando non sono più necessari. Con la scalabilità automatica è più facile ottenere un [utilizzo del cluster][cluster] elevato, perché non è necessario effettuare il provisioning del cluster per soddisfare un carico di lavoro.

È inoltre possibile sviluppare pipeline pianificate più complesse usando [Azure Data Factory][adf] con Azure Databricks.

## <a name="storage-considerations"></a>Considerazioni sulle risorse di archiviazione

In questa implementazione di riferimento i dati vengono archiviati direttamente nell'archivio di Databricks per semplicità. In un'impostazione di produzione, tuttavia, i dati possono essere archiviati in un archivio dati cloud come [Archivio BLOB di Azure][blob]. [Databricks][databricks-connect] supporta anche Azure Data Lake Store, Azure SQL Data Warehouse, Azure Cosmos DB, Apache Kafka e Hadoop.

## <a name="cost-considerations"></a>Considerazioni sul costo

Azure Databricks è un'offerta Spark premium a cui è associato un costo. Sono inoltre disponibili [piani tariffari][pricing] standard e premium per Databricks.

Per questo scenario è sufficiente il piano tariffario standard. Tuttavia, se un'applicazione specifica deve poter ridimensionare automaticamente i cluster per gestire carichi di lavoro di grandi dimensioni o dashboard interattivi di Databricks, il livello premium potrebbe aumentare ulteriormente i costi.

I notebook della soluzione possono essere eseguiti su qualsiasi piattaforma basata su Spark con modifiche minime per rimuovere i pacchetti specifici di Databricks. Vedere le soluzioni simili seguenti per diverse piattaforme di Azure:

- [Python in Azure Machine Learning Studio][python-aml]
- [Servizi R per SQL Server][sql-r]
- [PySpark in Azure Data Science Virtual Machine][py-dvsm]

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Per distribuire questa architettura di riferimento, seguire la procedura descritta nel repository  [GitHub][github] per compilare una soluzione scalabile per l'assegnazione di punteggi ai modelli Spark in batch in Azure Databricks.

## <a name="related-architectures"></a>Architetture correlate

È disponibile anche un'architettura di riferimento che usa Spark per compilare [sistemi per raccomandazioni in tempo reale][recommendation] con punteggi offline precalcolati. Questi sistemi sono scenari comuni in cui i punteggi vengono elaborati in batch.

[adf]: https://azure.microsoft.com/blog/operationalize-azure-databricks-notebooks-using-data-factory/
[ai-guide]: /azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance
[blob]: https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[cli]: https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html
[cluster]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html
[databricks]: /azure/azure-databricks/
[databricks-connect]: /azure/azure-databricks/databricks-connect-to-data-sources
[data-sources]: https://docs.databricks.com/spark/latest/data-sources/index.html
[github]: https://github.com/Azure/BatchSparkScoringPredictiveMaintenance
[job]: https://docs.databricks.com/user-guide/jobs.html
[log]: https://docs.databricks.com/user-guide/clusters/event-log.html
[metrics]: https://docs.databricks.com/user-guide/clusters/metrics.html
[mllib]: https://docs.databricks.com/spark/latest/mllib/index.html
[mllib-spark]: https://docs.databricks.com/spark/latest/mllib/index.html#apache-spark-mllib
[notebooks]: https://docs.databricks.com/user-guide/notebooks/index.html
[pricing]: https://azure.microsoft.com/en-us/pricing/details/databricks/
[python-aml]: https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1
[py-dvsm]: https://gallery.azure.ai/Tutorial/Predictive-Maintenance-using-PySpark
[recommendation]: /azure/architecture/reference-architectures/ai/real-time-recommendation
[sql-r]: https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1
[workspace]: https://docs.databricks.com/user-guide/workspace.html
