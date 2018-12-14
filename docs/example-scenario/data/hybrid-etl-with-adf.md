---
title: Processi ETL ibridi con distribuzioni SSIS locali esistenti e Azure Data Factory
description: Processi ETL ibridi con distribuzioni SQL Server Integration Services (SSIS) locali esistenti e Azure Data Factory
author: alhieng
ms.date: 9/20/2018
ms.custom: tsp-team
ms.openlocfilehash: cc6c2bfe85dc0d1eb8ad29e044611f1e435810c3
ms.sourcegitcommit: 4ba3304eebaa8c493c3e5307bdd9d723cd90b655
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53306790"
---
# <a name="hybrid-etl-with-existing-on-premises-ssis-and-azure-data-factory"></a>Processi ETL ibridi con distribuzioni SSIS locali esistenti e Azure Data Factory

Le organizzazioni che eseguono la migrazione dei relativi database di SQL Server nel cloud possono produrre enormi risparmi sui costi, miglioramenti delle prestazioni, maggiore flessibilità e maggiore scalabilità. Tuttavia, la rielaborazione dei processi esistenti di estrazione, trasformazione e caricamento (ETL) compilati con SQL Server Integration Services (SSIS) può costituire un ostacolo alla migrazione. In altri casi, il processo di caricamento dei dati richiede una logica complessa e/o componenti specifici dello strumento di dati che non sono ancora supportati da Azure Data Factory v2 (ADF). Le funzionalità SSIS comunemente usate includono le trasformazioni Raggruppamento fuzzy e Ricerca fuzzy, Change Data Capture (CDC), Dimensioni a modifica lenta (SCD) e Data Quality Services (DQS).

Per facilitare una migrazione lift-and-shift di un database SQL esistente, un approccio ETL ibrido potrebbe essere l'opzione più adatta. Un approccio ibrido usa ADF come motore di orchestrazione primario, ma continua a usare i pacchetti SSIS esistenti per pulire i dati e interagire con risorse locali. Questo approccio usa SQL Server Integration Runtime (IR) basato su ADF per abilitare la modalità lift-and-shift dei database esistenti nel cloud, quando si usano codice esistente e pacchetti SSIS.

Questo scenario di esempio riguarda le organizzazioni che stanno spostando i database nel cloud e pensando di usare ADF come motore ETL basato sul cloud primario, incorporando allo stesso tempo i pacchetti SSIS esistenti nel nuovo flusso di lavoro di dati cloud. Molte organizzazioni hanno investito significativamente nello sviluppo di pacchetti ETL SSIS per attività dati specifiche. La riscrittura di questi pacchetti può risultare scoraggiante. Inoltre, molti pacchetti di codice esistenti hanno dipendenze sulle risorse locali, impedendo la migrazione al cloud.

ADF permette ai clienti di sfruttare i pacchetti ETL esistenti pur limitando ulteriori investimenti nello sviluppo ETL in locale. Questo esempio illustra i potenziali casi d'uso per sfruttare i pacchetti SSIS esistenti come parte del nuovo flusso di lavoro di dati cloud con Azure Data Factory v2.

## <a name="potential-use-cases"></a>Potenziali casi d'uso

SSIS è da sempre lo strumento ETL scelto da molti professionisti per il caricamento e trasformazione dei dati di SQL Server. In alcuni casi, sono stati usati funzionalità specifiche di SSIS o componenti plug-in di terze parti per accelerare le attività di sviluppo. La sostituzione o lo sviluppo ulteriore di questi pacchetti potrebbe non essere un'opzione, impedendo ai clienti di eseguire la migrazione dei database nel cloud. I clienti cercano approcci a impatto ridotto alla migrazione dei database esistenti nel cloud e mirano a sfruttare i pacchetti SSIS esistenti.

Di seguito sono elencati diversi potenziali casi d'uso in locale:

* Caricamento di log del router di rete in un database per l'analisi.
* Preparazione dei dati relativi al rapporto di lavoro delle risorse umane per la creazione di report analitici.
* Caricamento di dati relativi al prodotto e alle vendite in un data warehouse per la previsione delle vendite.
* Automazione del caricamento di archivi dati operativi o data warehouse per finanza e contabilità.

## <a name="architecture"></a>Architettura

![Panoramica dell'architettura di un processo ETL ibrido con Azure Data Factory][architecture-diagram]

1. I dati vengono originati dall'archivio BLOB in Data Factory.
2. La pipeline di Azure Data Factory richiama una stored procedure per eseguire un processo SSIS ospitato in locale con Integration Runtime.
3. Per preparare i dati per l'utilizzo a valle vengono eseguiti i processi di pulizia dei dati.
4. Una volta completata l'attività di pulizia dei dati, viene eseguita un'attività di copia per caricare i dati puliti in Azure.
5. I dati puliti vengono quindi ricaricati in tabelle in SQL Data Warehouse.

### <a name="components"></a>Componenti

* [Archiviazione BLOB][docs-blob-storage] viene usato per archiviare i file e come origine di recupero dati per Data Factory.
* [SQL Server Integration Services][docs-ssis] contiene i pacchetti ETL locali usati per eseguire carichi di lavoro specifici dell'attività.
* [Azure Data Factory][docs-data-factory] è il motore di orchestrazione cloud che accetta dati provenienti da più origini e combina, orchestra e carica i dati in un data warehouse.
* [SQL Data Warehouse][docs-sql-data-warehouse] centralizza i dati nel cloud per semplificare l'accesso usando le query SQL ANSI standard.

### <a name="alternatives"></a>Alternative

Data Factory potrebbe richiamare le procedure di pulizia dei dati implementate usando altre tecnologie, ad esempio un notebook di Databricks, uno script Python o un'istanza SSIS in esecuzione in una macchina virtuale. L'[installazione di componenti personalizzati, a pagamento o concessi in licenza, per il runtime di integrazione Azure-SSIS](/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components), potrebbe essere un'alternativa fattibile all'approccio ibrido.

## <a name="considerations"></a>Considerazioni

Integration Runtime (IR) supporta due modelli: runtime di integrazione self-hosted o runtime di integrazione ospitato in Azure. Prima di tutto, è necessario decidere tra queste due opzioni. Il self-hosting è più conveniente ma ha un sovraccarico maggiore per la gestione e manutenzione. Per altre informazioni, vedere [Runtime di integrazione self-hosted](/azure/data-factory/concepts-integration-runtime#self-hosted-integration-runtime). Per assistenza nel determinare quale runtime di integrazione usare, vedere [Determinazione del runtime di integrazione da usare](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use).

Per l'approccio ospitato in Azure, è necessario stabilire quanta energia è necessaria per elaborare i dati. La configurazione ospitata in Azure consente di selezionare le dimensioni delle VM come parte dei passaggi di configurazione. Per altre informazioni sulla selezione delle dimensioni delle macchine virtuali, vedere [VM performance considerations](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations) (Considerazioni sulle prestazioni della macchina virtuale).

La decisione è molto più semplice quando si hanno già pacchetti SSIS esistenti che presentano dipendenze a livello locale, ad esempio origini dati o i file che non sono accessibili da Azure. In questo scenario, l'unica opzione è il runtime di integrazione self-hosted. Questo approccio offre la massima flessibilità per sfruttare il cloud come motore di orchestrazione, senza dover riscrivere i pacchetti esistenti.

In definitiva, la finalità è spostare i dati elaborati nel cloud per un ulteriore perfezionamento o per la combinazione con altri dati archiviati nel cloud. Come parte del processo di progettazione, tenere traccia del numero di attività usate nelle pipeline di ADF. Per altre informazioni, vedere [Pipeline e attività in Azure Data Factory](/azure/data-factory/concepts-pipelines-activities).

## <a name="pricing"></a>Prezzi

Azure Data Factory è un modo economico per orchestrare lo spostamento dei dati nel cloud. Il costo si basa su diversi fattori.

* Numero di esecuzioni di pipeline
* Numero di entità/attività usate all'interno della pipeline
* Numero di operazioni di monitoraggio
* Numero di esecuzioni di integrazione (runtime di integrazione ospitato in Azure o runtime di integrazione self-hosted)

ADF usa la fatturazione in base al consumo. Di conseguenza, i costi vengono addebitati solo durante le esecuzioni e il monitoraggio della pipeline. L'esecuzione di una pipeline di base costerebbe appena 50 centesimi e il monitoraggio appena 25 centesimi. Si può usare il [calcolatore dei costi Azure](https://azure.microsoft.com/pricing/calculator/) per creare una stima più accurata basata sul carico di lavoro specifico.

Quando si esegue un carico di lavoro ETL ibrido, è necessario tener conto del costo della macchina virtuale usata per ospitare i pacchetti SSIS. Questo costo è basato sulle dimensioni della macchina virtuale da D1v2 (1 core, 3,5 GB di RAM, disco da 50 GB) a E64V3 (64 core, 432 GB di RAM, disco da 1600 GB).  Per ulteriori indicazioni sulla selezione delle dimensioni appropriate della macchina virtuale, vedere [VM performance considerations](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations) (Considerazioni sulle prestazioni della macchina virtuale).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Azure Data Factory](https://azure.microsoft.com/services/data-factory/).
* Introduzione ad Azure Data Factory con l'[esercitazione dettagliata](/azure/data-factory/#step-by-step-tutorials).
* [Eseguire il provisioning di Azure-SSIS Integration Runtime in Azure Data Factory](/azure/data-factory/tutorial-deploy-ssis-packages-azure).

<!-- links -->
[architecture-diagram]: ./media/architecture-diagram-hybrid-etl-with-adf.png
[small-pricing]: https://azure.com/e/
[medium-pricing]: https://azure.com/e/
[large-pricing]: https://azure.com/e/
[availability]: /azure/architecture/checklist/availability
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[docs-blob-storage]: /azure/storage/blobs/
[docs-data-factory]: /azure/data-factory/introduction
[docs-resource-groups]: /azure/azure-resource-manager/resource-group-overview
[docs-ssis]: /sql/integration-services/sql-server-integration-services
[docs-sql-data-warehouse]: /azure/sql-data-warehouse/sql-data-warehouse-overview-what-is
