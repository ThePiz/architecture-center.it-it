---
title: Scelta di una tecnologia per l'analisi dei dati e la creazione di report
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 5b4947f53edf595c206ef4f55572dadc1a9daa09
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111411"
---
# <a name="choosing-a-data-analytics-technology-in-azure"></a>Scelta di una tecnologia per l'analisi dei dati in Azure

L'obiettivo della maggior parte delle soluzioni per Big Data è fornire informazioni dettagliate sui dati tramite funzionalità per l'analisi e la creazione di report. Possono essere inclusi anche visualizzazioni e report preconfigurati o funzionalità per l'esplorazione dei dati in modalità interattiva.

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-when-choosing-a-data-analytics-technology"></a>Opzioni disponibili per la scelta di una tecnologia per l'analisi dei dati

<!-- markdownlint-disable MD026 -->

Sono disponibili diverse opzioni per l'analisi e la creazione di visualizzazioni e report in Azure, in base alle esigenze specifiche:

- [Power BI](/power-bi/)
- [Notebook di Jupyter](https://jupyter.readthedocs.io/en/latest/index.html)
- [Notebook di Zeppelin](https://zeppelin.apache.org/)
- [Microsoft Azure Notebooks](https://notebooks.azure.com/)

### <a name="power-bi"></a>Power BI

[Power BI](/power-bi/) è una famiglia di strumenti di analisi business che può connettersi a centinaia di origini dati e può essere usata per analisi ad hoc. Per informazioni sulle origini dati attualmente disponibili, vedere [questo elenco](/power-bi/desktop-data-sources). Usare [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded/) per integrare Power BI nelle applicazioni senza richiedere licenze aggiuntive.

Power BI può essere usato per generare report e pubblicarli all'interno di un'organizzazione. Qualsiasi utente può creare dashboard personalizzati, con governance e [sicurezza integrata](/power-bi/service-admin-power-bi-security). Per autenticare gli utenti che accedono al servizio Power BI viene usato [Azure Active Directory](/azure/active-directory/) (Azure AD) e le credenziali di accesso di Power BI vengono richieste ogni volta che un utente prova ad accedere alle risorse per le quali è necessaria l'autenticazione.

### <a name="jupyter-notebooks"></a>Notebook di Jupyter

L'ambiente per [notebook di Jupyter](https://jupyter.readthedocs.io/en/latest/index.html) offre una shell basata su browser che consente ai data scientist di creare file di *notebook* contenenti codice Python, Scala o R e testo di markdown e collaborare così in modo più efficiente condividendo e commentando codice e risultati in unico documento.

La maggior parte dei cluster HDInsight, ad esempio Spark o Hadoop, è [preconfigurata con notebook di Jupyter](/azure/hdinsight/spark/apache-spark-jupyter-notebook-kernels) per l'interazione con i dati e l'invio di processi per l'elaborazione. A seconda del tipo di cluster HDInsight in uso, sono disponibili kernel per l'interpretazione e l'esecuzione del codice. I cluster Spark in HDInsight, ad esempio, offrono kernel correlati a Spark che è possibile selezionare per eseguire il codice Python o Scala usando il motore Spark.

I notebook di Jupyter offrono un ambiente ideale per l'analisi, la visualizzazione e l'elaborazione dei dati prima di creare visualizzazioni più avanzate con uno strumento di business intelligence per la creazione di report come Power BI.

### <a name="zeppelin-notebooks"></a>Notebook di Zeppelin

I [notebook di Zeppelin](https://zeppelin.apache.org/) offrono un'altra opzione per l'uso di una shell basata su browser con funzionalità simili a quelle di Jupyter. Alcuni cluster HDInsight sono [preconfigurati con notebook di Zeppelin](/azure/hdinsight/spark/apache-spark-zeppelin-notebook). Se tuttavia si usa un cluster [HDInsight Interactive Query](/azure/hdinsight/interactive-query/apache-interactive-query-get-started) (Hive LLAP), [Zeppelin](/azure/hdinsight/hdinsight-connect-hive-zeppelin) offre attualmente l'unico tipo di notebook possibile per l'esecuzione di query Hive interattive. Se inoltre si usa un [cluster HDInsight aggiunto al dominio](/azure/hdinsight/domain-joined/apache-domain-joined-introduction), i notebook di Zeppelin sono l'unico tipo che consente di assegnare account di accesso utente diversi per controllare l'accesso ai notebook e alle tabelle Hive sottostanti.

### <a name="microsoft-azure-notebooks"></a>Microsoft Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com/) è un servizio online basato su notebook di Jupyter che consente ai data scientist di creare, eseguire e condividere notebook di Jupyter in librerie basate sul cloud. Azure Notebooks fornisce ambienti di esecuzione per Python 2, Python 3, F# e R e diverse librerie per creare grafici per la visualizzazione dei dati, ad esempio ggplot, matplotlib, bokeh e seaborn.

A differenza dei notebook di Jupyter in esecuzione su un cluster HDInsight, che sono connessi all'account di archiviazione predefinito del cluster, il servizio Azure Notebooks non fornisce dati. Per il [caricamento dei dati](https://notebooks.azure.com/Microsoft/libraries/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb) è possibile adottare diverse strategie, ad esempio scaricare i dati da un'origine online, interagire con l'archiviazione tabelle e BLOB di Azure, connettersi a un database SQL o caricare i dati con la Copia guidata per Azure Data Factory.

Vantaggi principali:

- Il servizio è gratuito: non è necessaria una sottoscrizione di Azure.
- Non è necessario installare in locale Jupyter né le distribuzioni R o Python di supporto: è sufficiente usare un browser.
- È possibile gestire le librerie online e accedervi da qualsiasi dispositivo.
- È possibile condividere i notebook con i collaboratori.

Considerazioni:

- Non è possibile accedere ai notebook in modalità offline.
- Le funzionalità di elaborazione limitate del servizio di notebook gratuito possono non essere sufficienti per eseguire il training di modelli complessi o di grandi dimensioni.

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- È necessario connettersi a numerose origini dati, fornendo una soluzione centralizzata per creare report per la distribuzione dei dati in tutto il dominio? In caso affermativo, scegliere un'opzione che consenta di connettersi a centinaia di origini dati.

- Si vogliono incorporare visualizzazioni dinamiche in un'applicazione o un sito Web esterno? In caso affermativo, scegliere un'opzione che fornisca funzionalità di incorporamento.

- Si vogliono progettare visualizzazioni e report in modalità offline? In caso affermativo, scegliere un'opzione con funzionalità disponibili offline.

- È richiesta una notevole potenza di elaborazione per eseguire il training di modelli di intelligenza artificiale grandi o complessi o gestire set di dati di dimensioni particolarmente elevate? In caso affermativo, scegliere un'opzione in grado di connettersi a un cluster di Big Data.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="general-capabilities"></a>Funzionalità generali

<!-- markdownlint-disable MD033 -->

| | Power BI | Notebook di Jupyter | Notebook di Zeppelin | Microsoft Azure Notebooks |
| --- | --- | --- | --- | --- |
| Connessione a un cluster di Big Data per l'elaborazione avanzata | Yes | Sì | Sì | No  |
| Servizi gestiti | Yes | Sì <sup>1</sup> | Sì <sup>1</sup> | Yes |
| Connessione a centinaia di origini dati | Yes | No  | No  | No  |
| Funzionalità offline | Sì <sup>2</sup> | No  | No  | No  |
| Funzionalità di incorporamento | Yes | No  | No  | No  |
| Aggiornamento automatico dei dati | Yes | No  | No  | No  |
| Accesso a numerosi pacchetti open source | No  | Sì <sup>3</sup> | Sì <sup>3</sup> | Sì <sup>4</sup> |
| Opzioni di trasformazione/pulizia dei dati | [Power Query](https://powerbi.microsoft.com/blog/getting-started-with-power-query-part-i/), R | 40 linguaggi, inclusi Python, R, Julia e Scala | Più di 20 interpreti, inclusi Python, JDBC e R | Python, F#, R |
| Prezzi | Gratuito per Power BI Desktop (Autore), vedere [Prezzi](https://powerbi.microsoft.com/pricing/) per le opzioni di hosting | Gratuito | Gratuito | Gratuito |
| Collaborazione multiutente | [Sì](/power-bi/service-how-to-collaborate-distribute-dashboards-reports) | Sì (tramite la condivisione o con un server multiutente come [JupyterHub](https://github.com/jupyterhub/jupyterhub)) | Yes | Sì (tramite la condivisione) |

<!-- markdownlint-enable MD033 -->

[1] Quando è usato come parte di un cluster HDInsight gestito.

[2] Con l'uso di Power BI Desktop.

[3] È possibile cercare i pacchetti creati con il contributo della community nel [repository Maven](https://search.maven.org/).

[4] I pacchetti Python possono essere installati tramite pip o conda. I pacchetti R possono essere installati da CRAN o GitHub. I pacchetti F# possono essere installati tramite nuget.org usando l'[utilità di gestione dipendenze Paket](https://fsprojects.github.io/Paket/).
