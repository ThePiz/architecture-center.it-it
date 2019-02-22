---
title: Grandi imprese - Evoluzione della coerenza delle risorse
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Grandi imprese - Evoluzione della coerenza delle risorse
author: BrianBlanchard
ms.openlocfilehash: 9fc6ca015310c02338463d3c8aa53ddfc74699df
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901632"
---
# <a name="large-enterprise-resource-consistency-evolution"></a>Grandi imprese: Evoluzione della coerenza delle risorse

Questo articolo approfondisce la presentazione dello scenario aggiungendo controlli di coerenza delle risorse all'MVP per la governance per supportare app cruciali.

## <a name="evolution-of-the-narrative"></a>Evoluzione dello scenario

I team di adozione del cloud hanno soddisfatto tutti i requisiti per lo spostamento dei dati protetti. Con tali applicazioni entrano in gioco anche gli impegni per i contratti di servizio con l'azienda e la necessità di supporto dal team responsabile delle operazioni IT. A sostegno del team responsabile della migrazione dei due data center sono pronti più team di sviluppo di app e di business intelligence per dare il via al lancio di nuove soluzioni in produzione. Per il team responsabile delle operazioni IT il concetto di operazioni nel cloud è una novità ed è necessario individuare un modo per integrare velocemente i processi operativi esistenti.

### <a name="evolution-of-current-state"></a>Evoluzione dello stato attuale

- Il reparto IT si sta occupando attivamente dello spostamento dei carichi di lavoro di produzione con dati protetti in Azure. Alcuni carichi di lavoro con priorità bassa gestiscono il traffico di produzione. Per altri si potrà procedere al cutover, non appena il team responsabile delle operazioni IT conferma l'idoneità per il supporto dei carichi di lavoro.
- I team di sviluppo delle applicazioni sono pronti per il traffico di produzione.
- Il team di business intelligence è pronto per l'integrazione di stime e informazioni dettagliate nei sistemi che eseguono le operazioni per le tre business unit.

### <a name="evolution-of-the-future-state"></a>Evoluzione dello stato futuro

- Per il team responsabile delle operazioni IT il concetto di operazioni nel cloud è una novità ed è necessario individuare un modo per integrare velocemente i processi operativi esistenti.

I cambiamenti che riguardano lo stato attuale e quello futuro creano nuovi rischi che richiederanno nuove definizioni di criteri.

## <a name="evolution-of-tangible-risks"></a>Evoluzione dei rischi tangibili

**Interruzione delle attività aziendali**: vi è un rischio intrinseco che qualsiasi nuova piattaforma possa causare l'interruzione di processi aziendali cruciali. Il team responsabile delle operazioni IT e i team che si occupano in vari modi delle adozioni del cloud sono relativamente inesperti per quanto riguarda le operazioni nel cloud. Questo aspetto aumenta il rischio di interruzione, che deve essere mitigato e gestito.

Questo rischio aziendale può tradursi in diversi rischi tecnici:

- La presenza di processi operativi disallineati potrebbe causare interruzioni del servizio che non possono essere rilevate o risolte rapidamente.
- Eventuali intrusioni dall'esterno o attacchi Denial of Service potrebbero causare un'interruzione delle attività aziendali.
- È possibile che asset cruciali non vengano individuati nel modo appropriato e pertanto non vengano gestiti correttamente.
- Gli asset non individuati o mal definiti possono non essere supportati dai processi di gestione operativa esistenti.
- La configurazione degli asset distribuiti potrebbe non soddisfare le aspettative in termini di prestazioni.
- La registrazione potrebbe non essere effettuata e centralizzata correttamente per consentire la risoluzione dei problemi di prestazioni.
- Le procedure di ripristino possono non riuscire o impiegare più tempo del previsto.
- Processi di distribuzione incoerenti potrebbero produrre lacune a livello di sicurezza che possono causare perdite di dati o interruzioni.
- Deviazioni di configurazione e patch non applicate potrebbero produrre lacune a livello di sicurezza non intenzionali che possono causare perdite di dati o interruzioni.
- La configurazione potrebbe non soddisfare i requisiti dei contratti di servizio definiti o quelli di ripristino concordati.
- Le applicazioni o i sistemi operativi distribuiti potrebbero non soddisfare i requisiti di protezione avanzata del sistema operativo o delle applicazioni.
- Esiste il rischio di incoerenza a causa dei numerosi team che lavorano nel cloud.

## <a name="evolution-of-the-policy-statements"></a>Evoluzione delle definizioni dei criteri

Le modifiche ai criteri indicate di seguito contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione. L'elenco può sembrare lungo, ma l'adozione di questi criteri può essere più semplice di quanto non sembri.

1. Tutti gli asset distribuiti devono essere ordinati in categorie in base a criticità e classificazione dei dati. Le classificazioni devono essere esaminate dal team di governance del cloud e dal proprietario dell'applicazione prima della distribuzione nel cloud.
2. Le subnet che contengono applicazioni cruciali devono essere protette tramite una soluzione firewall in grado di rilevare le intrusioni e rispondere agli attacchi.
3. Gli strumenti di governance devono controllare e soddisfare i requisiti di configurazione definiti dal team della baseline di sicurezza.
4. Gli strumenti di governance devono convalidare che tutti gli asset correlati alle applicazioni cruciali o ai dati protetti siano inclusi nel monitoraggio per l'esaurimento e l'ottimizzazione delle risorse.
5. Gli strumenti di governance devono convalidare che venga raccolto il livello appropriato di dati di registrazione per tutti i dati protetti o le applicazioni cruciali.
6. Il processo di governance deve convalidare che il backup, il ripristino e il rispetto dei contratti di servizio vengano implementati correttamente per le applicazioni cruciali e i dati protetti.
7. Gli strumenti di governance devono limitare la distribuzione di macchine virtuali solo alle immagini approvate.
8. Gli strumenti di governance devono soddisfare il requisito in base al quale gli aggiornamenti automatici **non** devono essere applicati a tutti gli asset distribuiti che supportano applicazioni cruciali. Le violazioni devono essere esaminate con i team di gestione operativa e risolte in base ai criteri relativi alle operazioni. Gli asset che non vengono aggiornati automaticamente devono essere inclusi in processi di proprietà del team responsabile delle operazioni IT.
9. Gli strumenti di governance devono convalidare l'assegnazione di tag correlati a costo, criticità, contratto di servizio, applicazione e classificazione dei dati. Tutti i valori devono essere allineati ai valori predefiniti gestiti dal team di governance del cloud.
10. I processi di governance devono includere controlli in fase di distribuzione e in base a cicli regolari per garantire la coerenza tra tutti gli asset.
11. Tendenze ed exploit che possono influire sulle distribuzioni cloud devono essere esaminati regolarmente dal team responsabile della sicurezza in modo da fornire aggiornamenti per gli strumenti della baseline di sicurezza usati nel cloud.
12. Prima della distribuzione nell'ambiente di produzione, tutti i dati protetti e le applicazioni cruciali devono essere aggiunti alla soluzione di monitoraggio operativo designata. Gli asset che non possono essere individuati dagli strumenti per le operazioni IT scelti non possono essere distribuiti nell'ambiente di produzione. Qualsiasi modifica necessaria per rendere gli asset individuabili deve essere apportata ai processi di distribuzione pertinenti per garantire che gli asset siano individuabili nelle distribuzioni future.
13. Dopo l'individuazione, le dimensioni dell'asset devono essere convalidate dai team di gestione operativa per verificare che l'asset soddisfi i requisiti relativi alle prestazioni.
14. Gli strumenti di distribuzione devono essere approvati dal team di governance del cloud per garantire la governance regolare degli asset distribuiti.
15. Gli script di distribuzione devono essere conservati in un archivio centrale accessibile dal team di governance del cloud per la revisione e il controllo periodici.
16. I processi di revisione della governance devono convalidare che gli asset distribuiti siano configurati correttamente in base ai requisiti relativi a contratti di servizio e ripristino.

## <a name="evolution-of-the-best-practices"></a>Evoluzione delle procedure consigliate

Questa sezione dell'articolo approfondisce la progettazione dell'MVP per la governance in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure. Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove definizioni dei criteri aziendali.

Seguendo l'esperienza di questo esempio fittizio, si presuppone che l'evoluzione dei dati protetti sia già avvenuta. Sulla base di tale procedura consigliata, di seguito verranno aggiunti requisiti di monitoraggio operativo, per preparare una sottoscrizione per applicazioni cruciali.

**Sottoscrizione IT aziendale**: Aggiungere quanto segue alla sottoscrizione IT aziendale, che funge da hub.

1. Come dipendenza esterna, il team responsabile delle operazioni nel cloud dovrà definire gli strumenti di monitoraggio operativo, gli strumenti di continuità aziendale e ripristino di emergenza (BCDR) e gli strumenti di correzione automatizzata. Il team di governance del cloud può quindi supportare i processi di individuazione necessari.
    1. In questo caso d'uso il team responsabile delle operazioni nel cloud ha scelto Monitoraggio di Azure come strumento principale per il monitoraggio delle applicazioni cruciali.
    2. Il team ha inoltre scelto Azure Site Recovery come strumento BCDR primario.
2. Implementazione di Azure Site Recovery
    1. Definire e distribuire Azure Key Vault per i processi di backup e ripristino
    2. Creare un modello di Gestione delle risorse di Azure per la creazione di un insieme di credenziali in ogni sottoscrizione
3. Implementazione di Monitoraggio di Azure
    1. Una volta identificata una sottoscrizione cruciale, è possibile creare un'area di lavoro di Log Analytics tramite PowerShell. Questo è un processo di pre-distribuzione.

**Singola sottoscrizione di adozione del cloud**: gli elementi seguenti assicurano che ogni sottoscrizione sia individuabile dalla soluzione di monitoraggio e pronta per essere inclusa nelle procedure BCDR.

1. Criteri di Azure per i nodi cruciali
    1. Controllare e imporre l'uso solo dei ruoli standard.
    2. Controllare e imporre l'applicazione della crittografia per tutti gli account di archiviazione.
    3. Controllare e imporre l'uso di reti virtuali e subnet di rete approvate per ogni interfaccia di rete.
    4. Controllare e imporre la limitazione delle tabelle di routing definite dall'utente.
    5. Controllare e imporre la distribuzione di agenti di Log Analytics per macchine virtuali Windows e Linux.
2. Progetto di Azure
    1. Creare un progetto denominato `mission-critical-workloads-and-protected-data`. Questo progetto applicherà gli asset oltre al progetto per i dati protetti.
    2. Aggiungere i nuovi criteri di Azure al progetto.
    3. Applicare il progetto a qualsiasi sottoscrizione che si prevede ospiti un'applicazione cruciale.

## <a name="conclusion"></a>Conclusioni

L'aggiunta di questi processi e modifiche all'MVP per la governance aiutano a mitigare molti dei rischi associati alla governance delle risorse. Insieme, aggiungono i controlli di ripristino, ridimensionamento e monitoraggio necessari per le operazioni basate sul cloud.

## <a name="next-steps"></a>Passaggi successivi

In un'epoca in cui l'adozione del cloud continua a evolversi e a offrire valore aziendale aggiunto, anche i rischi e le esigenze di governance del cloud sono destinati ad evolversi. Per la società fittizia presentata in questo percorso, il prossimo fattore scatenante sarà il momento in cui le dimensioni della distribuzione supereranno i 1.000 asset nel cloud o la spesa mensile supererà i 10.000 dollari USA al mese. A questo punto, il team di governance del cloud aggiungerà controlli di gestione dei costi.

> [!div class="nextstepaction"]
> [Evoluzione della gestione dei costi](./cost-management-evolution.md)