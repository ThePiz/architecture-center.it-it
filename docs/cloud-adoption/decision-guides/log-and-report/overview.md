---
title: 'CAF: Guida alle decisioni relative a registrazione e creazione di report'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni su registrazione, creazione di report e monitoraggio come servizi di base nelle migrazioni di Azure.
author: rotycenh
ms.openlocfilehash: 36552488872622ec59e2fcf4816da4184c3d4fbf
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901716"
---
# <a name="logging-and-reporting-decision-guide"></a>Guida alle decisioni relative a registrazione e creazione di report

Tutte le organizzazioni hanno bisogno di meccanismi per notificare ai team IT le problematiche di prestazioni, tempo di attività e sicurezza prima che diventino problemi gravi. Una strategia di monitoraggio vincente consente di conoscere le prestazioni dei singoli componenti che costituiscono i carichi di lavoro e l'infrastruttura di rete. Nell'ambito di una migrazione cloud pubblico, per essere certi che l'organizzazione raggiunga gli obiettivi di tempo di attività, sicurezza e conformità ai criteri, è cruciale integrare la registrazione e la creazione di report con i sistemi di monitoraggio esistenti, inviando al contempo gli eventi e le metriche importanti al personale IT appropriato.

![Grafico delle opzioni relative a registrazione, creazione di report e monitoraggio, dalla meno complessa alla più complessa, allineato con i collegamenti sotto](../../_images/discovery-guides/discovery-guide-logs-and-reporting.png)

Passare a: [Pianificazione dell'infrastruttura di monitoraggio](#planning-your-monitoring-infrastructure) | [Cloud nativo](#cloud-native) | [Estensione locale](#on-premises-extension) | [Aggregazione tramite il gateway](#gateway-aggregation) | [Monitoraggio ibrido (locale)](#hybrid-monitoring-on-premises) | [Monitoraggio ibrido (basato sul cloud)](#hybrid-monitoring-cloud-based) | [Multi-cloud](#multi-cloud) | [Altre informazioni](#learn-more)

Il punto di flesso quando si stabilisce una strategia di gestione delle identità cloud è determinato principalmente dagli investimenti esistenti fatti dall'organizzazione nei processi operativi e, in una certa misura, dagli eventuali requisiti necessari per supportare una strategia multi-cloud.

Ci sono diversi modi per registrare e creare report sulle attività nel cloud. Il cloud nativo e la registrazione centralizzata sono due comuni opzioni di software come un servizio (SaaS), basate sul tipo di sottoscrizione e sul numero di sottoscrizioni.

## <a name="planning-your-monitoring-infrastructure"></a>Pianificazione dell'infrastruttura di monitoraggio

Quando si pianifica la distribuzione, è necessario considerare dove sono archiviati i dati di registrazione e come si integreranno i servizi di monitoraggio e creazione di report basati su cloud con gli strumenti e i processi esistenti.

| Domanda | Cloud nativo | Estensione locale | Monitoraggio ibrido | Aggregazione tramite il gateway |
|-----|-----|-----|-----|-----|
| È già disponibile un'infrastruttura di monitoraggio locale? | No  | Sì | Sì |  No  |
| Esistono requisiti che impediscono l'archiviazione dei dati dei log in posizioni di archiviazione esterne? | No  | Sì | No  | No  |
| È necessario integrare il monitoraggio cloud con i sistemi locali? | No  | No  | Sì | No  |
È necessario elaborare o filtrare i dati di telemetria prima di inviarli ai sistemi di monitoraggio? | No  | No  | No  | Sì |

### <a name="cloud-native"></a>Cloud nativo

Se attualmente l'organizzazione è priva di sistemi di registrazione e creazione di report definiti o se non è necessario integrare la distribuzione cloud con i sistemi di monitoraggio locali o altri esterni esistenti, una soluzione SaaS cloud nativa è la scelta più semplice.

In questo scenario i dati dei log vengono registrati e archiviati nello stesso ambiente cloud del carico di lavoro, mentre gli strumenti di registrazione e creazione di report che elaborano e inviano le informazioni al personale IT sono offerti come parte della piattaforma cloud.

Si possono implementare soluzioni di registrazione cloud native ad hoc in base alla sottoscrizione o al carico di lavoro per le distribuzioni più piccole o sperimentali. Tali soluzioni vengono organizzate in modo centralizzato per monitorare i dati dei log nell'intero ambiente cloud.

**Presupposti del cloud nativo**. L'uso di un sistema cloud nativo per la registrazione e la creazione di report presuppone quanto segue:

- Non è necessario integrare nei sistemi locali esistenti i dati dei log dei carichi di lavoro cloud.
- Non si useranno i sistemi di creazione di report basati sul cloud per monitorare i sistemi locali.

### <a name="on-premises-extension"></a>Estensione locale

Negli scenari in cui è necessario integrare i dati di telemetria cloud con i sistemi locali che non supportano la registrazione e la creazione di report ibride o che supportano la migrazione di applicazioni e servizi con una quantità minima di nuove attività di sviluppo, sarà necessario distribuire gli agenti di monitoraggio nelle macchine virtuali che invieranno i dati dei log direttamente ai sistemi locali, invece di archiviarli nell'ambiente cloud.

Per supportare questo approccio, le risorse cloud dovranno poter comunicare direttamente con i sistemi locali tramite una combinazione di [rete ibrida](../software-defined-network/hybrid.md) e di [servizi del dominio ospitati nel cloud](../identity/overview.md#cloud-hosted-domain-services). In questo modo, la rete virtuale cloud funge da estensione di rete dell'ambiente locale. I carichi di lavoro ospitati nel cloud possono quindi comunicare direttamente con il sistema di registrazione e creazione di report locale.

Questo approccio sfrutta gli investimenti esistenti negli strumenti di monitoraggio con modifiche minime alle applicazioni o ai servizi distribuiti nel cloud. Si tratta spesso dell'approccio più rapido per supportare il monitoraggio durante una migrazione lift-and-shift. Non verranno tuttavia acquisiti i dati dei log generati dalle risorse PaaS e SaaS basate sul cloud e verranno omessi i log relativi alle macchine virtuali generati direttamente dalla piattaforma cloud, ad esempio quelli sullo stato delle macchine virtuali. Di conseguenza, questo modello dovrà essere una soluzione temporanea da adottare solo finché non verrà implementata una soluzione di monitoraggio ibrida più completa.

Presupposti solo dell'estensione locale:

- È necessario mantenere i dati dei log solo nell'ambiente locale, a supporto dei requisiti tecnici oppure a causa dei requisiti normativi o relativi ai criteri.
- I sistemi locali non supportano le soluzioni di registrazione e creazione di report ibride o di aggregazione tramite il gateway.
- Le applicazioni basate sul cloud possono inviare i dati di telemetria direttamente ai sistemi di registrazione locali oppure gli agenti di monitoraggio che inviano i dati all'ambiente locale possono essere distribuiti nelle macchine virtuali dei carichi di lavoro.
- I carichi di lavoro non dipendono da servizi PaaS o SaaS che richiedono la registrazione e la creazione di report basate sul cloud.

### <a name="gateway-aggregation"></a>Aggregazione tramite il gateway

Per gli scenari in cui la quantità di dati di telemetria basati sul cloud è molto elevata o in cui i sistemi di monitoraggio locali esistenti devono modificare i dati dei log prima di poterli elaborare, potrebbe essere necessario un servizio di [aggregazione tramite il gateway](../../../patterns/gateway-aggregation.md) dei dati del log.

Un servizio gateway viene distribuito al provider di servizi cloud. Le applicazioni e i servizi pertinenti vengono quindi configurati per inviare i dati di telemetria al gateway invece che a un sistema di registrazione predefinito. Il gateway può quindi elaborare i dati: prima li aggrega, li combina o li formatta in altro modo, quindi li invia al servizio di monitoraggio per l'inserimento e l'analisi.

Un gateway può essere usato anche per aggregare e pre-elaborare i dati di telemetria associati per i sistemi nativi del cloud o ibridi.

Presupposti dell'aggregazione tramite il gateway:

- Si prevedono livelli molto elevati di dati di telemetria dalle applicazioni o dai servizi basati sul cloud.
- È necessario formattare o ottimizzare in altro modo i dati di telemetria prima di inviarli ai sistemi di monitoraggio.
- I sistemi di monitoraggio dispongono di API o altri meccanismi per l'inserimento dei dati dei log dopo l'elaborazione tramite il gateway.

### <a name="hybrid-monitoring-on-premises"></a>Monitoraggio ibrido (locale)

Un soluzione di monitoraggio ibrido combina i dati dei log sia delle risorse locali che di quelle cloud per offrire una visualizzazione integrata dello stato operativo dell'ambiente IT.

Se è stato fatto un investimento in sistemi di monitoraggio locali che sarebbero difficili o costosi da sostituire, potrebbe essere necessario integrare dati di telemetria dei carichi di lavoro cloud nelle soluzioni di monitoraggio locali preesistenti. In un sistema di monitoraggio locale ibrido i dati di telemetria locali continuano a usare il sistema di monitoraggio locale esistente. I dati di telemetria basati sul cloud vengono inviati al sistema di monitoraggio cloud direttamente oppure vengono archiviati nel cloud insieme ai carichi di lavoro e compilati e inseriti nel sistema locale a intervalli regolari.

**Presupposti del monitoraggio ibrido locale**. L'uso di un sistema di registrazione e di creazione di report locale per il monitoraggio ibrido presuppone quanto segue:

- È necessario usare i sistemi di creazione di report locali esistenti per monitorare i carichi di lavoro cloud.
- È necessario mantenere la proprietà dei dati dei log in locale.
- I sistemi di gestione locali dispongono di API o di altri meccanismi per inserire i dati dei log dai sistemi basati su cloud.

> [!TIP]
> Data la natura iterativa della migrazione cloud, è probabile che si effettuerà la transizione da soluzioni di monitoraggio native del cloud e locali distinte a un approccio ibrido parziale. Assicurarsi che le modifiche apportate all'architettura di monitoraggio siano in linea con i processi IT e operativi generali.

### <a name="hybrid-monitoring-cloud-based"></a>Monitoraggio ibrido (basato sul cloud)

Se non è assolutamente necessario mantenere un sistema di monitoraggio locale o si vogliono sostituire i sistemi di monitoraggio locali con una soluzione SaaS, è anche possibile scegliere di integrare i dati dei log locali con un sistema di monitoraggio centralizzato basato sul cloud.

Rispecchiando l'approccio centralizzato locale, in questo scenario i carichi di lavoro cloud usano il meccanismo di registrazione cloud predefinito e le applicazioni e i servizi locali inviano la directory di telemetria al sistema di registrazione basato sul cloud o li aggregano per l'inserimento nel sistema cloud a intervalli regolari. Il sistema di monitoraggio basato su cloud funge quindi da sistema di monitoraggio e creazione di report principale per l'intero ambiente IT.

Presupposti del monitoraggio ibrido basato sul cloud: L'uso di sistemi di registrazione e di creazione di report basati sul cloud per il monitoraggio ibrido presuppone quanto segue:

- Non si è dipendenti dai sistemi di monitoraggio locali esistenti.
- I carichi di lavoro non hanno requisiti normativi o relativi ai criteri che prevedono l'archiviazione dei dati dei log in locale.
- I sistemi di monitoraggio basato sul cloud dispongono di API o di altri meccanismi per inserire i dati dei log dalle applicazioni e dai servizi locali.

### <a name="multi-cloud"></a>Multi-cloud

L'integrazione di funzionalità di registrazione e creazione di report in una piattaforma multi-cloud può risultare complessa. I servizi offerti nelle piattaforme spesso non sono direttamente confrontabili e sono diverse anche le funzionalità di registrazione e telemetria fornite da questi servizi.
Il supporto per la registrazione multi-cloud spesso richiede l'uso di servizi gateway per elaborare i dati dei log in un formato comune prima di inviare i dati a una soluzione di registrazione ibrida.

## <a name="learn-more"></a>Altre informazioni

[Monitoraggio di Azure](/azure/azure-monitor/overview) è il servizio predefinito di monitoraggio e creazione di report per Azure. Fornisce:

- Una piattaforma unificata per la raccolta di dati di telemetria delle app, dati di telemetria host (ad esempio, le macchine virtuali), metriche dei contenitori, metriche della piattaforma Azure e log eventi.
- Visualizzazione, query, avvisi e strumenti di analisi. Può fornire informazioni dettagliate su macchine virtuali, sistemi operativi guest, reti virtuali ed eventi delle applicazioni dei carichi di lavoro.
- [API REST](/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough) per l'integrazione con i servizi esterni e l'automazione dei servizi di monitoraggio e avviso
- [Integrazione](/azure/monitoring-and-diagnostics/monitoring-partners) con molti noti fornitori di terze parti.
