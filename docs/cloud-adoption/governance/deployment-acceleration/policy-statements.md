---
title: 'CAF: Esempi di istruzioni dei criteri di Accelerazione della distribuzione'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Esempi di istruzioni dei criteri di Accelerazione della distribuzione
author: alexbuckgit
ms.openlocfilehash: 4f7d59e6653c29db03f966d1c7105524b72586fc
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901827"
---
# <a name="deployment-acceleration-sample-policy-statements"></a>Esempi di istruzioni dei criteri di Accelerazione della distribuzione

Le istruzioni individuali dei criteri del cloud sono linee guida per affrontare i rischi specifici identificati durante il processo di valutazione dei rischi. Queste istruzioni devono fornire un riassunto conciso dei rischi e i piani per gestirli. Ogni definizione di istruzione deve includere i tipi di informazione seguenti:

- **Rischi tecnici.** Riepilogo del rischio che verrà risolto da questi criteri.
- **Istruzione dei criteri.** Una spiegazione chiara e riepilogativa dei requisiti dei criteri.
- **Opzioni di progettazione.** Suggerimenti pratici, specifiche o altre linee guida che i team IT e gli sviluppatori possono usare quando implementano i criteri.

Gli esempi seguenti di istruzioni dei criteri riguardano alcuni rischi aziendali comuni relativi alla configurazione e vengono forniti come esempi a cui fare riferimento durante la creazione di istruzioni dei criteri per soddisfare le esigenze dell'organizzazione. Si noti che questi esempi non devono essere considerati prescrittivi; esistono potenzialmente diverse opzioni relative ai criteri per affrontare qualsiasi tipo di rischio. Lavorare a stretto contatto con i team IT serve a identificare le migliori soluzioni di criteri per il proprio set di rischi specifico.

## <a name="reliance-on-manual-deployment-or-configuration-of-systems"></a>Dipendenza dalla distribuzione manuale o configurazione dei sistemi

**Rischi tecnici**: Basarsi sull'intervento dell'utente durante la distribuzione o la configurazione aumenta la probabilità di errori umani e riduce ripetibilità e prevedibilità della configurazione e delle distribuzioni del sistema. Tale operazione comporta in genere anche alla distribuzione più lenta delle risorse di sistema.

**Istruzioni dei criteri**: Tutte le risorse distribuite nel cloud devono essere distribuite tramite modelli o script di automazione laddove possibile.

**Opzioni di progettazione potenziali**: [I modelli di Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) forniscono un approccio di infrastruttura come codice alla distribuzione delle risorse in Azure. [Blocchi predefiniti di Azure](https://github.com/mspnp/template-building-blocks/wiki) fornisce uno strumento da riga di comando e set di modelli di Resource Manager progettati per semplificare la distribuzione delle risorse di Azure.

## <a name="lack-of-visibility-into-system-issues"></a>Mancanza di visibilità per problemi di sistema

**Rischi tecnici**: Monitoraggio e diagnostica insufficienti per i sistemi aziendali impediscono al personale operativo di identificare, monitorare e aggiornare i problemi prima che si verifichi un'interruzione del sistema; ciò può aumentare notevolmente il tempo necessario per risolvere in modo corretto un'interruzione del servizio.

**Istruzioni dei criteri**: Verranno implementati i criteri seguenti:

- Misure di diagnostica e metriche chiave saranno identificate per tutti i componenti e sistemi di produzione, gli strumenti di monitoraggio e diagnostica verranno applicati a questi sistemi e monitorati regolarmente dal personale operativo.
- Le operazioni prenderanno in considerazione l'uso di strumenti di monitoraggio e diagnostica in ambienti non di produzione, ad esempio Gestione temporanea e Controllo di qualità, per identificare i problemi di sistema prima che si verifichino nell'ambiente di produzione.

**Opzioni di progettazione potenziali**: [Monitoraggio di Azure](/azure/azure-monitor/), che include anche Log Analytics e Application Insights, fornisce strumenti per la raccolta e l'analisi dei dati di telemetria per agevolare la comprensione delle prestazioni delle applicazioni e identificare in modo proattivo i problemi che li interessano e le risorse da cui dipendono.

## <a name="configuration-security-reviews"></a>Verifiche di protezione della configurazione

**Rischi tecnici**: Nel corso del tempo, nuovi problemi o minacce di sicurezza possono aumentare i rischi di accesso non autorizzato a risorse sicure.

**Istruzioni dei criteri**: I processi di governance del cloud devono includere un esame trimestrale condotto con i team che si occupano della gestione della configurazione per identificare gli attori dannosi o i modelli di uso da evitare nella configurazione degli asset del cloud.

**Opzioni di progettazione potenziali**: Stabilire una riunione di revisione trimestrale per la sicurezza che includa i membri del team governance e il personale IT responsabile della configurazione delle risorse e delle applicazioni cloud. Revisionare i dati di sicurezza e le metriche esistenti per stabilire i gap negli strumenti e nel criterio corrente Accelerazione della distribuzione, aggiornare i criteri per attenuare nuovi possibili rischi.

## <a name="next-steps"></a>Passaggi successivi

Usare gli esempi citati in questo articolo come punto di partenza per sviluppare criteri che consentano di risolvere rischi aziendali specifici, che si allineano con i piani di adozione del cloud.

Per iniziare a sviluppare le proprie istruzioni di criteri personalizzate correlate alla gestione delle identità, scaricare [Identity Baseline template](template.md) (modello di Baseline di identità).

Per accelerare l'adozione di questa disciplina, scegliere un [Actionable Governance Journey](../journeys/overview.md) (Percorsi operativi di governance) maggiormente allineato con l'ambiente. Quindi modificare la progettazione per incorporare le decisioni relative a criteri aziendali specifici.

> [!div class="nextstepaction"]
> [Actionable Governance Journeys](../journeys/overview.md) (Percorsi operativi di governance)