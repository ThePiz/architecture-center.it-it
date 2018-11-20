---
title: Monitoraggio delle applicazioni Web in Azure
description: Monitorare un'applicazione Web ospitata in Servizio app di Azure.
author: adamboeglin
ms.date: 09/12/2018
ms.openlocfilehash: ba008035c37d1d4e2d2f823463344e4941c0b4c4
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610754"
---
# <a name="web-application-monitoring-on-azure"></a>Monitoraggio delle applicazioni Web in Azure

Le offerte PaaS (Platform as a Service) di Azure gestiscono automaticamente le risorse di calcolo e influiscono sulle modalità di monitoraggio delle distribuzioni. Azure include più servizi di monitoraggio, ognuno dei quali esegue un ruolo specifico. Insieme, questi servizi offrono una soluzione completa per la raccolta, l'analisi e l'uso dei dati di telemetria provenienti dalle applicazioni e dalle risorse di Azure utilizzate.

Questo scenario è relativo ai servizi di monitoraggio che è possibile usare e descrive un modello di flusso di dati da usare con più origini dati. Quando si parla di monitoraggio, molti strumenti e servizi usano le distribuzioni di Azure. In questo scenario vengono scelti servizi immediatamente disponibili proprio perché sono facili da usare. Più avanti in questo articolo sono descritte altre opzioni di monitoraggio.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

- Strumentazione di un'applicazione Web per il monitoraggio dei dati di telemetria.
- Raccolta dei dati di telemetria front-end e back-end per un'applicazione distribuita in Azure.
- Monitoraggio delle metriche e delle quote associate ai servizi in Azure.

## <a name="architecture"></a>Architettura

![Diagramma dell'architettura di monitoraggio delle app][architecture]

Questo scenario usa un ambiente gestito di Azure per ospitare un livello dati e applicazione. Il flusso dei dati nello scenario avviene come segue:

1. Un utente interagisce con l'applicazione.
2. Il browser e il servizio app generano dati di telemetria.
3. Application Insights raccoglie e analizza i dati relativi a integrità, prestazioni e utilizzo dell'applicazione.
4. Gli sviluppatori e gli amministratori possono esaminare le informazioni su integrità, prestazioni e utilizzo.
5. Il database SQL di Azure genera i dati di telemetria.
6. Monitoraggio di Azure raccoglie e analizza le quote e le metriche dell'infrastruttura.
7. Log Analytics raccoglie e analizza i log e le metriche.
8. Gli sviluppatori e gli amministratori possono esaminare le informazioni su integrità, prestazioni e utilizzo.

### <a name="components"></a>Componenti

- [Servizio app di Azure](/azure/app-service/) è un servizio PaaS per la compilazione e l'hosting di app in macchine virtuali gestite. Le infrastrutture di calcolo sottostanti in cui vengono eseguite le app sono gestite automaticamente. Il servizio app monitora le quote di utilizzo delle risorse e le metriche delle app, registra le informazioni di diagnostica e genera avvisi basati sulle metriche. Ancora meglio, è possibile usare Application Insights per creare [test di disponibilità][availability-tests] per testare l'applicazione da aree diverse.
- [Application Insights][application-insights] è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori e supporta più piattaforme. Monitora l'applicazione, rileva le anomalie dell'applicazione, ad esempio un peggioramento delle prestazioni ed errori, e invia i dati di telemetria al portale di Azure. Application Insights è anche utilizzabile per la registrazione, l'analisi distribuita e le metriche dell'applicazione personalizzate.
- Attualmente [Monitoraggio di Azure][azure-monitor] offre [log e metriche][metrics] dell'infrastruttura di livello base per la maggior parte dei servizi in Azure. È possibile interagire con le metriche in diversi modi, tra cui la creazione di grafici nel portale di Azure, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Monitoraggio di Azure offre anche i dati direttamente in [Log Analytics e altri servizi], in cui è possibile eseguire query e combinarli con i dati di altre origini in locale o nel cloud.
- [Log Analytics][log-analytics] aiuta a correlare i dati sull'utilizzo e sulle prestazioni raccolti da Application Insights ai dati di configurazione e sulle prestazioni delle risorse di Azure che supportano l'app. Questo scenario usa l'[agente di Azure Log Analytics][Azure Log Analytics agent] per eseguire il push dei log di controllo di SQL Server in Log Analytics. È possibile scrivere le query e visualizzare i dati nel pannello Log Analytics del portale di Azure.

## <a name="considerations"></a>Considerazioni

È consigliabile aggiungere Application Insights al codice durante lo sviluppo usando [Application Insights SDK][Application Insights SDKs] ed eseguendo la personalizzazione per ogni applicazione. Questi SDK open source sono disponibili per la maggior parte dei framework applicazioni. Per arricchire e controllare i dati raccolti, incorporare l'uso degli SDK sia per le distribuzioni di test che per quelle di produzione nel processo di sviluppo. Il requisito principale è che l'app possa visualizzare direttamente o indirettamente l'endpoint di inserimento di Application Insights ospitato con un indirizzo per Internet. È quindi possibile aggiungere i dati di telemetria o arricchire una raccolta di dati di telemetria esistente.

Il monitoraggio in fase di esecuzione è un altro modo semplice per iniziare. I dati di telemetria raccolti devono essere controllati tramite i file di configurazione. È ad esempio possibile includere metodi di runtime che abilitano strumenti come [Application Insights Status Monitor][Application Insights Status Monitor] per distribuire gli SDK nella cartella corretta e aggiungere le configurazioni appropriate per iniziare il monitoraggio.

Come Application Insights, Log Analytics fornisce strumenti per l'[analisi dei dati nelle origini][analyzing data across sources], la creazione di query complesse e l'[invio di avvisi proattivi][sending proactive alerts] in condizioni specificate. È anche possibile visualizzare i dati di telemetria nel [portale di Azure][the Azure portal]. Log Analytics aggiunge valore ai servizi di monitoraggio esistenti, ad esempio [Monitoraggio di Azure][Monitoraggio di Azure] e può anche monitorare gli ambienti locali.

Sia Application Insights che Log Analytics usano il [linguaggio di query di Azure Log Analytics][Azure Log Analytics Query Language]. È anche possibile usare le [query in più risorse](https://azure.microsoft.com/blog/query-across-resources) per analizzare i dati di telemetria raccolti da Application Insights e Log Analytics in un'unica query.

Monitoraggio di Azure, Application Insights e Log Analytics inviano [avvisi](/azure/monitoring-and-diagnostics/monitoring-overview-alerts). Ad esempio, Monitoraggio di Azure invia avvisi sulle metriche a livello di piattaforma, come l'utilizzo della CPU, mentre Application Insights invia avvisi sulle metriche a livello di applicazione, come il tempo di risposta del server. Monitoraggio Azure invia avvisi sui nuovi eventi nel log attività di Azure, mentre Log Analytics può generare avvisi sui dati delle metriche o degli eventi per i servizi configurati per usarli. Gli [avvisi unificati in Monitoraggio di Azure](/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) sono una nuova esperienza di avvisi unificati in Azure che usa una tassonomia diversa.

### <a name="alternatives"></a>Alternative

Questo articolo descrive le opzioni di monitoraggio facilmente disponibili con le funzionalità più diffuse, ma sono possibili molte altre scelte, tra cui l'opzione per creare i propri meccanismi di registrazione. È consigliabile aggiungere i servizi di monitoraggio mentre si creano i livelli in una soluzione. Ecco alcune possibili estensioni e alternative:

- Consolidare le metriche di Monitoraggio di Azure e Application Insights in Grafana usando l'[origine dati di Monitoraggio di Azure per Grafana][Azure Monitor Data Source For Grafana].
- [Data Dog][data-dog] offre un connettore per Monitoraggio di Azure
- Automatizzare le funzioni di monitoraggio usando [Automazione di Azure][Azure Automation].
- Aggiungere la comunicazione con le [soluzioni di gestione dei servizi IT][ITSM solutions].
- Estendere Log Analytics con una [soluzione di gestione][management solution].

### <a name="scalability-and-availability"></a>Disponibilità e scalabilità

Questo scenario è in gran parte incentrato sulle soluzioni PaaS per il monitoraggio perché gestiscono facilmente e automaticamente la disponibilità e la scalabilità e sono supportate dai contratti di servizio. Servizi app, ad esempio, fornisce un [contratto di servizio][SLA] garantito per la disponibilità.

Application Insights prevede [limiti][app-insights-limits] per il numero di richieste al secondo che possono essere elaborate. Se si supera il limite di richieste, potrebbe verificarsi la limitazione dei messaggi. Per evitare tale limitazione, implementare [filtri][message-filtering] o il [campionamento][message-sampling] per ridurre la velocità dati.

Le considerazioni sulla disponibilità elevata per l'app eseguita, tuttavia, sono responsabilità dello sviluppatore. Per informazioni sulla scalabilità, ad esempio, vedere la sezione [Considerazioni sulla scalabilità](#scalability-considerations) nell'architettura di riferimento dell'applicazione Web di base. Dopo la distribuzione di un'app, è possibile configurare i test per [monitorarne la disponibilità][monitor its availability] usando Application Insights.

### <a name="security"></a>Sicurezza

I requisiti di conformità e le informazioni sensibili influiscono sulla raccolta, la conservazione e l'archiviazione dei dati. Sono disponibili altre informazioni su come [Application Insights][application-insights] e [Log Analytics][log-analytics] gestiscono i dati di telemetria.

Possono valere anche le considerazioni sulla sicurezza seguenti:

- Sviluppare un piano per gestire le informazioni personali se gli sviluppatori possono raccogliere i dati o arricchire i dati di telemetria esistenti.
- Considerare la conservazione dei dati. Ad esempio, Application Insights conserva i dati di telemetria per 90 giorni. Archiviare i dati a cui si vuole accedere per periodi più lunghi usando Microsoft Power BI, l'esportazione continua o l'API REST. Si applicano le tariffe di archiviazione.
- Limitare l'accesso alle risorse di Azure per controllare l'accesso ai dati e chi può visualizzare i dati di telemetria da un'applicazione specifica. Per consentire il blocco dell'accesso al monitoraggio dei dati di telemetria, vedere [Risorse, ruoli e controllo di accesso in Application Insights][Resources, roles, and access control in Application Insights].
- Considerare la possibilità di controllare l'accesso in lettura/scrittura nel codice dell'applicazione per impedire agli utenti di aggiungere marcatori di versione o tag che limitano l'inserimento dati dall'applicazione. Con Application Insights, non è possibile controllare i singoli elementi di dati dopo che sono stati inviati a una risorsa, quindi, se un utente ha accesso ad alcuni dati, ha accesso a tutti i dati in una singola risorsa.
- Aggiungere meccanismi di [governance](/azure/security/governance-in-azure) per applicare controlli per i criteri o i costi nelle risorse di Azure, se necessario. Ad esempio, usare Log Analytics per il monitoraggio relativo alla sicurezza, ad esempio con criteri e controllo degli accessi in base al ruolo, oppure usare [Criteri di Azure](/azure/azure-policy/azure-policy-introduction) per creare, assegnare e gestire le definizioni dei criteri.
- Per monitorare i potenziali problemi di sicurezza e ottenere un punto di vista centralizzato sullo stato della sicurezza delle risorse di Azure, valutare la possibilità di usare [Centro sicurezza di Azure](/azure/security-center/security-center-intro).

## <a name="pricing"></a>Prezzi

Poiché i costi di monitoraggio possono sommarsi rapidamente, tenere in considerazione i prezzi fin dall'inizio, determinare che cosa si sta monitorando e controllare le tariffe associate per ogni servizio. Monitoraggio di Azure fornisce gratuitamente le [metriche di base][basic metrics], mentre i costi di monitoraggio per [Application Insights][application-insights-pricing] e [Log Analytics][log-analytics] dipendono dalla quantità di dati inseriti e dal numero di test eseguiti.

Per iniziare, usare il [calcolatore dei prezzi][pricing] per valutare i costi. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le diverse opzioni in base alla distribuzione prevista.

I dati di telemetria da Application Insights vengono inviati al portale di Azure durante il debug e dopo la pubblicazione dell'app. A scopo di test e per evitare addebiti non necessari, viene instrumentato un volume limitato di dati di telemetria. Per aggiungere altri indicatori, è possibile aumentare il limite dei dati di telemetria. Per un controllo più granulare, vedere [Campionamento in Application Insights][Sampling in Application Insights].

Dopo la distribuzione, è possibile osservare un'istanza di [Live Metrics Stream][Live Metrics Stream] per gli indicatori delle prestazioni. Questi dati non vengono archiviati, perché si visualizzano le metriche in tempo reale, ma i dati di telemetria possono essere raccolti e analizzati in un secondo momento. Non sono previste spese per i dati di Live Stream.

La fatturazione di Log Analytics si basa su ogni gigabyte (GB) di dati inseriti nel servizio. I primi 5 GB di dati inseriti nel servizio Azure Log Analytics ogni mese sono gratuiti e i dati vengono conservati senza alcun addebito per i primi 31 giorni nell'area di lavoro di Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi

Vedere queste risorse progettate per poter iniziare con la propria soluzione di monitoraggio:

[Architettura di riferimento per le applicazioni Web di base][Basic web application reference architecture]

[Iniziare a monitorare l'applicazione Web ASP.NET][Start monitoring your ASP.NET Web Application]

[Raccogliere dati sulle macchine virtuali di Azure][Collect data about Azure Virtual Machines]

## <a name="related-resources"></a>Risorse correlate

[Monitoraggio di applicazioni e risorse di Azure][Monitoring Azure applications and resources]

[Rilevare e diagnosticare le eccezioni di runtime con Azure Application Insights][Find and diagnose run-time exceptions with Azure Application Insights]

<!-- links -->
[architecture]: ./media/architecture-app-monitoring.png
[availability-tests]: /azure/application-insights/app-insights-monitor-web-app-availability
[application-insights]: /azure/application-insights/app-insights-overview
[azure-monitor]: /azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor
[metrics]: /azure/monitoring-and-diagnostics/monitoring-supported-metrics
[Log Analytics e altri servizi]: /azure/log-analytics/log-analytics-azure-storage
[log-analytics]: /azure/log-analytics/log-analytics-overview
[Azure Log Analytics agent]: https://blogs.msdn.microsoft.com/sqlsecurity/2017/12/28/azure-log-analytics-oms-agent-now-collects-sql-server-audit-logs/
[application-insights-pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[Application Insights SDKs]: /azure/application-insights/app-insights-asp-net
[Application Insights Status Monitor]: https://azure.microsoft.com/updates/application-insights-status-monitor-and-sdk-updated/
[analyzing data across sources]: /azure/log-analytics/log-analytics-dashboards
[sending proactive alerts]: /azure/log-analytics/log-analytics-alerts
[the Azure portal]: /azure/log-analytics/log-analytics-tutorial-dashboards
[Azure Log Analytics Query Language]: https://docs.loganalytics.io/docs/Learn
[cross-resource queries]: https://azure.microsoft.com/blog/query-across-resources/
[alerts]: /azure/monitoring-and-diagnostics/monitoring-overview-alerts
[Alerts (Preview)]: /azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts
[Azure Monitor Data Source For Grafana]: https://grafana.com/plugins/grafana-azure-monitor-datasource
[Azure Automation]: /azure/automation/automation-intro
[ITSM solutions]: https://azure.microsoft.com/blog/itsm-connector-for-azure-is-now-generally-available/
[management solution]: /azure/monitoring/monitoring-solutions
[SLA]: https://azure.microsoft.com/support/legal/sla/app-service/v1_4/
[monitor its availability]: /azure/application-insights/app-insights-monitor-web-app-availability
[Resources, roles, and access control in Application Insights]: /azure/application-insights/app-insights-resources-roles-access-control
[basic metrics]: /azure/monitoring-and-diagnostics/monitoring-supported-metrics
[pricing]: https://azure.microsoft.com/pricing/calculator/#log-analyticsc126d8c1-ec9c-4e5b-9b51-4db95d06a9b1
[Sampling in Application Insights]: /azure/application-insights/app-insights-sampling
[Live Metrics Stream]: /azure/application-insights/app-insights-live-stream
[Basic web application reference architecture]: /azure/architecture/reference-architectures/app-service-web-app/basic-web-app#scalability-considerations
[Start monitoring your ASP.NET Web Application]: /azure/application-insights/quick-monitor-portal
[Collect data about Azure Virtual Machines]: /azure/log-analytics/log-analytics-quick-collect-azurevm
[Monitoring Azure applications and resources]: /azure/monitoring-and-diagnostics/monitoring-overview
[Find and diagnose run-time exceptions with Azure Application Insights]: /azure/application-insights/app-insights-tutorial-runtime-exceptions
[data-dog]: https://www.datadoghq.com/blog/azure-monitoring-enhancements/
[app-insights-limits]: /azure/azure-subscription-service-limits#application-insights-limits
[message-filtering]: /azure/application-insights/app-insights-api-filtering-sampling
[message-sampling]: /azure/application-insights/app-insights-sampling
