---
title: 'CAF: Piccole e medie imprese - Evoluzione della coerenza delle risorse '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 'Presentazione: Piccole e medie imprese - Evoluzione della coerenza delle risorse'
author: BrianBlanchard
ms.openlocfilehash: 402bb3ff4182123cdc8825522f965f7cf8637980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901732"
---
# <a name="small-to-medium-enterprise-resource-consistency-evolution"></a>Piccole e medie imprese: Evoluzione della coerenza delle risorse

Questo articolo approfondisce la presentazione dello scenario aggiungendo i controlli di coerenza delle risorse per supportare app cruciali.

## <a name="evolution-of-the-narrative"></a>Evoluzione dello scenario

Le esperienze dei clienti, gli strumenti di previsione e le infrastrutture migrate continuano a evolversi. L'azienda è ora pronta a iniziare a usare questi asset in un ambiente di produzione.

### <a name="evolution-of-the-current-state"></a>Evoluzione dello stato attuale

Nella fase precedente di questo scenario, i team di BI e sviluppo delle applicazioni erano quasi pronti a integrare dati finanziari e dei clienti nei carichi di lavoro di produzione. Il team IT stava ritirando il data center di ripristino di emergenza.

Da allora, sono avvenuti alcuni cambiamenti che influiranno sulla governance:

- L'IT ha ritirato al 100% il data center di ripristino di emergenza, in anticipo rispetto alla pianificazione. Nel corso di questo processo, diversi asset del data center di produzione sono stati identificati come ottimi candidati per la migrazione nel cloud.
- I team di sviluppo delle applicazioni sono ora pronti per il traffico dell'ambiente di produzione.
- Il team di BI è pronto a reinserire stime e informazioni dettagliate nei sistemi operativi nel data center di produzione.

### <a name="evolution-of-the-future-state"></a>Evoluzione dello stato futuro

Prima di usare distribuzioni di Azure nei processi aziendali di produzione, le operazioni nel cloud devono evolversi. Allo stesso tempo, è necessaria un'ulteriore evoluzione della governance per garantire una corretta gestione degli asset.

I cambiamenti che riguardano lo stato attuale e quello futuro creano nuovi rischi che richiederanno nuove definizioni di criteri.

## <a name="evolution-of-tangible-risks"></a>Evoluzione dei rischi tangibili

**Interruzione delle attività aziendali**: Vi è un rischio intrinseco che qualsiasi nuova piattaforma possa causare l'interruzione di processi aziendali cruciali. Il team responsabile delle operazioni IT e i team che si occupano in vari modi delle adozioni del cloud sono relativamente inesperti per quanto riguarda le operazioni nel cloud. Questo aspetto aumenta il rischio di interruzione, che deve essere mitigato e gestito.

Questo rischio aziendale può tradursi in diversi rischi tecnici:

- Eventuali intrusioni dall'esterno o attacchi Denial of Service potrebbero causare un'interruzione delle attività aziendali.
- Asset cruciali potrebbero non essere individuati nel modo appropriato e di conseguenza possono non essere gestiti correttamente.
- Gli asset non individuati o mal definiti possono non essere supportati dai processi di gestione operativa esistenti.
- La configurazione degli asset distribuiti potrebbe non soddisfare le aspettative in termini di prestazioni.
- La registrazione potrebbe non essere effettuata e centralizzata correttamente per consentire la risoluzione dei problemi di prestazioni.
- Le procedure di ripristino possono non riuscire o impiegare più tempo del previsto.
- Processi di distribuzione incoerenti potrebbero produrre lacune a livello di sicurezza che possono causare perdite di dati o interruzioni.
- Deviazioni di configurazione e patch non applicate potrebbero produrre lacune a livello di sicurezza non intenzionali che possono causare perdite di dati o interruzioni.
- La configurazione potrebbe non soddisfare i requisiti dei contratti di servizio definiti o quelli di ripristino concordati.
- Le applicazioni o i sistemi operativi distribuiti potrebbero non soddisfare i requisiti di protezione avanzata.
- I numerosi team che lavorano nel cloud pongono un rischio di incoerenza.

## <a name="evolution-of-the-policy-statements"></a>Evoluzione delle definizioni dei criteri

Le modifiche ai criteri indicate di seguito contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione. L'elenco può sembrare lungo, ma l'adozione di questi criteri può essere più semplice di quanto non sembri.

1. Tutti gli asset distribuiti devono essere ordinati in categorie in base a criticità e classificazione dei dati. Le classificazioni devono essere esaminate dal team di governance del cloud e dal proprietario dell'applicazione prima della distribuzione nel cloud.
2. Le subnet che contengono applicazioni cruciali devono essere protette tramite una soluzione firewall in grado di rilevare le intrusioni e rispondere agli attacchi.
3. Gli strumenti di governance devono controllare e soddisfare i requisiti di configurazione definiti dal team di gestione della sicurezza.
4. Gli strumenti di governance devono convalidare che tutti gli asset correlati alle app cruciali o ai dati protetti siano inclusi nel monitoraggio per l'esaurimento e l'ottimizzazione delle risorse.
5. Gli strumenti di governance devono convalidare che venga raccolto il livello appropriato di dati di registrazione per tutti i dati protetti o le applicazioni cruciali.
6. Il processo di governance deve convalidare che il backup, il ripristino e il rispetto dei contratti di servizio vengano implementati correttamente per le applicazioni cruciali e i dati protetti.
7. Gli strumenti di governance devono limitare le distribuzioni di macchine virtuali solo alle immagini approvate.
8. Gli strumenti di governance devono soddisfare il requisito in base al quale gli aggiornamenti automatici non devono essere applicati a tutti gli asset distribuiti che supportano applicazioni cruciali. Le violazioni devono essere esaminate con i team di gestione operativa e risolte in base ai criteri relativi alle operazioni. Gli asset che non vengono aggiornati automaticamente devono essere inclusi in processi di proprietà del team responsabile delle operazioni IT.
9. Gli strumenti di governance devono convalidare l'assegnazione di tag correlati a costo, criticità, contratto di servizio, applicazione e classificazione dei dati. Tutti i valori devono essere allineati ai valori predefiniti gestiti dal team di governance.
10. I processi di governance devono includere controlli in fase di distribuzione e in base a cicli regolari per garantire la coerenza tra tutti gli asset.
11. Tendenze ed exploit che possono influire sulle distribuzioni cloud devono essere esaminati regolarmente dal team responsabile della sicurezza in modo da fornire aggiornamenti per gli strumenti di gestione della sicurezza usati nel cloud.
12. Prima della distribuzione nell'ambiente di produzione, tutti i dati protetti e le app cruciali devono essere aggiunti alla soluzione di monitoraggio operativo designata. Gli asset che non possono essere individuati dagli strumenti per le operazioni IT scelti non devono essere distribuiti nell'ambiente di produzione. Qualsiasi modifica necessaria per rendere gli asset individuabili deve essere apportata ai processi di distribuzione pertinenti per garantire che gli asset siano individuabili nelle distribuzioni future.
13. Una volta individuati, i team di gestione operativa ridimensioneranno gli asset per garantire che rispondano ai requisiti di prestazioni.
14. Gli strumenti di distribuzione devono essere approvati dal team di governance del cloud per garantire la governance regolare degli asset distribuiti.
15. Gli script di distribuzione devono essere conservati in un archivio centrale accessibile dal team di governance del cloud per la revisione e il controllo periodici.
16. I processi di revisione della governance devono convalidare che gli asset distribuiti siano configurati correttamente in base ai requisiti relativi a contratti di servizio e ripristino.

## <a name="evolution-of-the-best-practices"></a>Evoluzione delle procedure consigliate

Questa sezione dell'articolo approfondisce la progettazione dell'MVP per la governance in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure. Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove definizioni dei criteri aziendali.

1. Il team responsabile delle operazioni nel cloud definirà gli strumenti di monitoraggio operativo e di correzione automatica. Il team di governance del cloud supporterà questi processi di individuazione. In questo caso d'uso il team responsabile delle operazioni nel cloud ha scelto Monitoraggio di Azure come strumento principale per il monitoraggio delle applicazioni cruciali.
2. Creare un archivio in Azure DevOps per l'archiviazione e il controllo delle versioni di tutti i modelli di Resource Manager e le configurazioni tramite script pertinenti.
3. Implementazione dell'insieme di credenziali di Azure:
    1. Definire e distribuire l'insieme di credenziali di Azure per i processi di backup e ripristino.
    2. Creare un modello di Resource Manager per la creazione di un insieme di credenziali in ogni sottoscrizione.
4. Aggiornare Criteri di Azure per tutte le sottoscrizioni:
    1. Controllare e applicare la criticità e la classificazione dei dati in tutte le sottoscrizioni per identificare quelle con asset cruciali.
    2. Controllare e imporre l'uso delle sole immagini approvate.
5. Implementazione di Monitoraggio di Azure:
    1. Una volta identificata una sottoscrizione cruciale, creare un'area di lavoro di Monitoraggio di Azure tramite PowerShell. Questo è un processo di pre-distribuzione.
    2. Durante i test di distribuzione, il team responsabile delle operazioni nel cloud distribuisce gli agenti necessari e testa l'individuazione.
6. Aggiornare Criteri di Azure per tutte le sottoscrizioni che contengono applicazioni cruciali.
    1. Controllare e imporre l'applicazione di un gruppo di sicurezza di rete per tutte le schede di interfaccia di rete e subnet. I team responsabili delle reti e della sicurezza IT definiscono il gruppo di sicurezza di rete.
    2. Controllare e imporre l'uso di reti virtuali e subnet di rete approvate per ogni interfaccia di rete.
    3. Controllare e imporre la limitazione delle tabelle di routing definite dall'utente.
    4. Controllare e applicare la distribuzione degli agenti di Monitoraggio di Azure per tutte le macchine virtuali.
    5. Controllare e imporre la presenza dell'insieme di credenziali di Azure nella sottoscrizione.
7. Configurazione del firewall:
    1. Identificare una configurazione di Firewall di Azure che soddisfi i requisiti di sicurezza. In alternativa, identificare un dispositivo di terze parti compatibile con Azure.
    2. Creare un modello di Resource Manager per distribuire il firewall con le configurazioni necessarie.
8. Azure Blueprints:
    1. Creare un progetto Azure Blueprints denominato `protected-data`.
    2. Aggiungere il firewall e i modelli dell'insieme di credenziali di Azure al progetto.
    3. Aggiungere i nuovi criteri per le sottoscrizioni con dati protetti.
    4. Pubblicare il progetto in qualsiasi gruppo di gestione destinato a ospitare applicazioni cruciali.
    5. Applicare il nuovo progetto a ogni sottoscrizione interessata, nonché ai progetti esistenti.

## <a name="conclusion"></a>Conclusioni

Questi processi e modifiche aggiuntivi integrati nell'MVP per la governance contribuiscono a mitigare molti dei rischi associati alla governance delle risorse. Insieme, aggiungono controlli di ripristino, ridimensionamento e monitoraggio che semplificano le operazioni basate sul cloud.

## <a name="next-steps"></a>Passaggi successivi

In un'epoca in cui l'adozione del cloud continua a evolversi e a offrire valore aziendale, anche i rischi e le esigenze di governance sono destinati a evolversi. Per la società fittizia presentata in questo percorso, il prossimo fattore scatenante sarà il momento in cui le dimensioni della distribuzione supereranno 100 asset nel cloud o la spesa mensile supererà 1.000 euro al mese. A questo punto, il team di governance del cloud aggiungerà controlli di gestione dei costi.

> [!div class="nextstepaction"]
> [Evoluzione della gestione dei costi](./cost-management-evolution.md)