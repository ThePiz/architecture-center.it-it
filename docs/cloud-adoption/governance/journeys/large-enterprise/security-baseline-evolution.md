---
title: 'CAF: Grandi imprese - Evoluzione della baseline di sicurezza '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Grandi imprese - Evoluzione della baseline di sicurezza
author: BrianBlanchard
ms.openlocfilehash: 59fb3655f1ff2a5f0a30abc760c27c77b8f802af
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902084"
---
# <a name="large-enterprise-security-baseline-evolution"></a>Grandi imprese: Evoluzione della baseline di sicurezza

Questo articolo approfondisce la presentazione dello scenario aggiungendo i controlli di sicurezza a supporto dello spostamento dei dati protetti nel cloud.

## <a name="evolution-of-the-narrative"></a>Evoluzione dello scenario

Il CIO ha trascorso alcuni mesi collaborando con i colleghi e i legali dell'azienda. Un consulente di gestione con esperienza in materia di sicurezza informatica è stato assunto per aiutare i team che si occupano di sicurezza IT e governance IT a delineare nuovi criteri per i dati protetti. Il gruppo è stato di grande aiuto nel sostituire i criteri esistenti, facendo in modo che le informazioni personali e i dati finanziari venissero ospitati da provider di servizi cloud approvati. A tale scopo, è stato necessario adottare una serie di requisiti di sicurezza e un processo di governance per verificare e documentare la conformità a tali criteri.

Negli ultimi 12 mesi, i team responsabili dell'adozione del cloud hanno cancellato la maggior parte dei 5.000 asset dai due data center da ritirare. I 350 asset incompatibili sono stati spostati in un data center alternativo. Rimangono solo le 1.250 macchine virtuali che contengono dati protetti.

### <a name="evolution-of-the-cloud-governance-team"></a>Evoluzione del team di governance del cloud

Il team di governance del cloud continua a evolversi per tutto lo scenario. I due membri fondatori del team sono ora tra i più apprezzati cloud architect dell'azienda. La raccolta di script di configurazione si è ampliata man mano che nuovi team affrontano nuove distribuzioni innovative. Anche il team di governance del cloud si è ampliato. Di recente, i membri del team responsabile delle operazioni IT hanno preso parte alle attività del team di governance del cloud per lavorare alla preparazione delle operazioni nel cloud. I cloud architect che hanno favorito lo sviluppo di questa community sono visti sia come i guardiani del cloud che come i promotori del cloud.

Sebbene la differenza sia sottile, si tratta di una distinzione importante per lo sviluppo di una cultura IT orientata alla governance. Un custode del cloud mette a posto i pasticci di cloud architect innovativi e i due ruoli hanno obiettivi naturalmente in conflitto e opposti. Un guardiano del cloud si occupa di mantenere sicuro il cloud, in modo che altri cloud architect possano procedere più rapidamente, senza troppi pasticci. Un promotore del cloud svolge entrambe le funzioni, ma è coinvolto anche nella creazione di modelli per accelerare la distribuzione e l'adozione e diventa quindi sia un promotore dell'innovazione che un difensore delle cinque discipline cloud.

### <a name="evolution-of-the-current-state"></a>Evoluzione dello stato attuale

Nella fase precedente di questo scenario, l'azienda ha iniziato il processo di ritiro due data center. Questo processo continuativo include la migrazione di alcune applicazioni con requisiti di autenticazione legacy, che hanno richiesto un'evoluzione della baseline di identità, descritta nell'[articolo precedente](identity-baseline-evolution.md).

Da allora, sono avvenuti alcuni cambiamenti che influiranno sulla governance:

- Migliaia di asset IT e aziendali sono stati distribuiti nel cloud.
- Il team di sviluppo delle applicazioni ha implementato una pipeline di integrazione continua e distribuzione continua per distribuire un'applicazione nativa del cloud con un'esperienza utente migliorata. L'applicazione non interagisce ancora con i dati protetti, pertanto non è pronta per l'ambiente di produzione.
- Il team che si occupa di business intelligence in ambito IT cura attivamente i dati nel cloud, dalla logistica, all'inventario fino ai dati di terze parti. Questi dati vengono usati per effettuare nuove stime, che possono influire sui processi aziendali. Tuttavia, queste stime e informazioni dettagliate non hanno utilità pratica fino a quando i dati finanziari e dei clienti non possono essere integrati nella piattaforma di dati.
- Il team IT sta procedendo con i piani del CIO e del CFO per il ritiro di due data center. Quasi 3.500 asset presenti nei due data center sono stati ritirati oppure ne è stata eseguita la migrazione.
- I criteri relativi alle informazioni personali e ai dati finanziari sono stati modernizzati. Tuttavia, i nuovi criteri aziendali dipendono dall'implementazione dei criteri di governance e sicurezza correlati. I team sono ancora bloccati.

### <a name="evolution-of-the-future-state"></a>Evoluzione dello stato futuro

- Dagli esperimenti iniziali dei team di sviluppo delle applicazioni e di business intelligence è emersa la possibilità di apportare miglioramenti per quanto riguarda l'esperienza dei clienti e le decisioni basate sui dati. Entrambi i team vorrebbero espandere l'adozione del cloud nei prossimi 18 mesi distribuendo tali soluzioni nell'ambiente di produzione.
- L'IT ha sviluppato una motivazione aziendale per eseguire la migrazione di altri cinque data center in Azure, per ridurre ulteriormente i costi IT e offrire maggiore flessibilità aziendale. Sebbene di dimensioni più contenute, si prevede che il ritiro di tali data center consentirà di raddoppiare i risparmi sui costi totali.
- Sono stati approvati i budget relativi alla spesa in conto capitale e alla spesa operativa per implementare i criteri di governance e sicurezza, gli strumenti e i processi richiesti. I risparmi sui costi previsti in seguito al ritiro dei data center sono più che sufficienti per finanziare questa nuova iniziativa. I responsabili IT e aziendali sono sicuri che questo investimento garantirà un più rapido ritorno in altre aree. Il team iniziale di governance del cloud è diventato un team riconosciuto con personale e responsabili dedicati.
- Collettivamente, i team responsabili dell'adozione del cloud, il team di governance del cloud, il team responsabile della sicurezza IT e il team che si occupa di governance IT implementeranno i requisiti di governance e sicurezza per consentire ai team responsabili dell'adozione del cloud di eseguire la migrazione dei dati protetti nel cloud.

## <a name="evolution-of-tangible-risks"></a>Evoluzione dei rischi tangibili

**Violazione dei dati**: quando si adotta una nuova piattaforma di dati, si verifica un aumento inerente delle responsabilità correlate alle violazioni dei dati. I tecnici che adottano le tecnologie cloud hanno maggiori responsabilità di implementare soluzioni in grado di ridurre questo rischio. È necessario implementare una solida strategia di governance e sicurezza per fare in modo che i tecnici adempiano a queste responsabilità.

Questo rischio aziendale può tradursi in alcuni rischi tecnici:

- App cruciali o dati protetti potrebbero venire distribuiti involontariamente.
- Dati protetti potrebbero venire esposti in fase di archiviazione a causa di scelte non adeguate in relazione alla crittografia.
- Utenti non autorizzati potrebbero accedere ai dati protetti.
- Un'intrusione esterna potrebbe consentire l'accesso ai dati protetti.
- Eventuali intrusioni dall'esterno o attacchi Denial of Service potrebbero causare un'interruzione delle attività aziendali.
- Modifiche all'interno dell'organizzazione o tra i dipendenti potrebbero consentire l'accesso non autorizzato ai dati protetti.
- Nuovi exploit potrebbero creare opportunità di intrusione o accesso non autorizzato.
- Processi di distribuzione incoerenti potrebbero produrre lacune a livello di sicurezza che possono causare perdite di dati o interruzioni.
- Deviazioni di configurazione e patch non applicate potrebbero produrre lacune a livello di sicurezza non intenzionali che possono causare perdite di dati o interruzioni.
- Dispositivi perimetrali di diverso tipo potrebbero causare un aumento dei costi delle operazioni di rete.
- Configurazioni dei dispositivi di diverso tipo potrebbero portare a errori nella configurazione e compromettere la sicurezza.
- Il team che si occupa di sicurezza informatica ribadisce che sussiste il rischio di blocco da parte del fornitore per la generazione di chiavi di crittografia nella piattaforma di un singolo provider di servizi cloud. Sebbene questa affermazione non sia dimostrata, per il momento è stata accettata dal team.

## <a name="evolution-of-the-policy-statements"></a>Evoluzione delle definizioni dei criteri

Le modifiche ai criteri indicate di seguito contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione. L'elenco può sembrare lungo, ma l'adozione di questi criteri può essere più semplice di quanto non sembri.

1. Tutti gli asset distribuiti devono essere ordinati in categorie in base a criticità e classificazione dei dati. Le classificazioni devono essere esaminate dal team di governance del cloud e nell'ambito dell'applicazione prima della distribuzione nel cloud.
2. Le applicazioni che archiviano o accedono ai dati protetti devono essere gestite in modo diverso rispetto a quelle che non lo fanno. Come minimo, devono essere segmentate per evitare accessi non intenzionali ai dati protetti.
3. Tutti i dati protetti devono essere crittografati quando inattivi.
4. Le autorizzazioni con privilegi elevati in qualsiasi segmento contenente dati protetti devono essere un'eccezione. Qualsiasi eccezione verrà registrata dal team di governance del cloud e controllata regolarmente.
5. Le subnet di rete contenenti dati protetti devono essere isolate dalle altre subnet. Il traffico di rete tra le subnet di dati protetti verrà controllato regolarmente.
6. Alle subnet contenenti dati protetti non deve essere possibile accedere direttamente tramite la rete Internet pubblica o tra data center. L'accesso a tali subnet deve essere instradato tramite una subnet intermedia. Tutti gli accessi a tali subnet devono passare attraverso una soluzione firewall in grado di eseguire la scansione dei pacchetti e applicare funzioni di blocco.
7. Gli strumenti di governance devono controllare e soddisfare i requisiti di configurazione della rete definiti dal team di gestione della sicurezza.
8. Gli strumenti di governance devono limitare la distribuzione di macchine virtuali solo alle immagini approvate.
9. Quando possibile, la gestione della configurazione dei nodi deve applicare i requisiti dei criteri alla configurazione di qualsiasi sistema operativo guest. La gestione della configurazione dei nodi deve rispettare l'investimento esistente nell'oggetto Criteri di gruppo (GPO) per la configurazione delle risorse.
10. Gli strumenti di governance controlleranno che gli aggiornamenti automatici siano abilitati in tutti gli asset distribuiti. Quando possibile, gli aggiornamenti automatici verranno applicati. Quando non applicate dagli strumenti, le violazioni a livello di nodo devono essere esaminate con i team di gestione operativa e risolte in base ai criteri relativi alle operazioni. Gli asset che non vengono aggiornati automaticamente devono essere inclusi in processi di proprietà del team responsabile delle operazioni IT.
11. La creazione di nuovi gruppi di gestione o sottoscrizioni per le applicazioni cruciali o i dati protetti richiede una revisione da parte del team di governance del cloud per garantire la corretta assegnazione del progetto.
12. Un modello di accesso con privilegi minimi verrà applicato a qualsiasi sottoscrizione che contiene applicazioni cruciali o dati protetti.
13. Il fornitore di servizi cloud deve essere in grado di integrare le chiavi di crittografia gestite dalla soluzione locale esistente.
14. Il fornitore di servizi cloud deve essere in grado di supportare la soluzione di dispositivi perimetrali esistente e tutte le configurazioni necessarie per proteggere qualsiasi limite di rete esposto pubblicamente.
15. Il fornitore di servizi cloud deve essere in grado di supportare una connessione condivisa alla rete WAN globale, con la trasmissione dei dati instradata attraverso la soluzione di dispositivi perimetrali esistente.
16. Tendenze ed exploit che possono influire sulle distribuzioni cloud devono essere esaminati regolarmente dal team responsabile della sicurezza in modo da fornire aggiornamenti per gli strumenti della baseline di sicurezza usati nel cloud.
17. Gli strumenti di distribuzione devono essere approvati dal team di governance del cloud per garantire la governance regolare degli asset distribuiti.
18. Gli script di distribuzione devono essere conservati in un archivio centrale accessibile dal team di governance del cloud per la revisione e il controllo periodici.
19. I processi di governance devono includere controlli in fase di distribuzione e in base a cicli regolari per garantire la coerenza tra tutti gli asset.
20. Per la distribuzione di applicazioni che richiedono l'autenticazione del cliente è necessario usare un provider di identità approvato compatibile con il provider di identità primario per gli utenti interni.
21. I processi di governance del cloud devono includere esami trimestrali condotti con i team che si occupano della baseline di identità per identificare gli attori dannosi o i modelli di utilizzo da evitare nella configurazione degli asset cloud.

## <a name="evolution-of-the-best-practices"></a>Evoluzione delle procedure consigliate

Questa sezione dell'articolo approfondisce la progettazione dell'MVP per la governance in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure. Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove definizioni dei criteri aziendali.

Le nuove procedure consigliate rientrano in due categorie: IT aziendale (hub) e adozione cloud (spoke).

**Definizione di una sottoscrizione di tipo hub/spoke IT aziendale per centralizzare la baseline di sicurezza**: In questa procedura consigliata la capacità di governance esistente è racchiusa in una [topologia hub-spoke con servizi condivisi][shared-services], con poche aggiunte da parte del team di governance del cloud.

1. Archivio di Azure DevOps. Creare un archivio in Azure DevOps per l'archiviazione e il controllo delle versioni di tutti i modelli di Azure Resource Manager e le configurazioni tramite script pertinenti
2. Modello hub-spoke.
    1. Le indicazioni fornite nell'[architettura hub-spoke con servizi condivisi di riferimento][shared-services] possono essere usate per generare modelli di Resource Manager per gli asset necessari in un hub IT aziendale.
    2. Usando questi modelli, questa struttura può essere resa ripetibile, come parte di una strategia di governance centrale.
    3. Oltre all'architettura di riferimento corrente, è consigliabile creare un modello di gruppo di sicurezza di rete acquisendo eventuali requisiti di blocco delle porte o di inserimento nell'elenco elementi consentiti per la rete virtuale che deve ospitare il firewall. Questo gruppo di sicurezza di rete sarà diverso rispetto ai gruppi di sicurezza di rete precedenti, perché sarà il primo a consentire il traffico pubblico in una rete virtuale.
3. Creare i criteri di Azure. Creare criteri denominati `Hub NSG Enforcement` per applicare la configurazione del gruppo di sicurezza di rete assegnato a qualsiasi rete virtuale creata in questa sottoscrizione. Applicare i criteri predefiniti per la configurazione guest come indicato di seguito:
    1. Controllare che i server Web Windows usino protocolli di comunicazione sicuri.
    2. Controllare che le impostazioni di sicurezza della password siano configurate correttamente nelle macchine virtuali Linux e Windows.
4. Progetto IT aziendale
    1. Creare un progetto di Azure denominato `corporate-it-subscription`.
    2. Aggiungere i modelli hub/spoke e i criteri del gruppo di sicurezza di rete dell'hub.
5. Ampliamento della gerarchia dei gruppi di gestione iniziale.
    1. Per ogni gruppo di gestione che ha richiesto il supporto per i dati protetti, il progetto `corporate-it-subscription-blueprint` offre una soluzione hub accelerata.
    2. Poiché i gruppi di gestione in questo esempio fittizio comprendono una gerarchia a livello di area oltre a una gerarchia di business unit, questo progetto verrà distribuito in ogni area.
    3. Per ogni area nella gerarchia dei gruppi di gestione, creare una sottoscrizione denominata `Corporate IT Subscription`.
    4. Applicare il progetto `corporate-it-subscription-blueprint` a ogni istanza a livello di area.
    5. In questo modo verrà stabilito un hub per ogni business unit in ogni area. Note: è possibile ottenere ulteriori risparmi sui costi, condividendo gli hub tra business unit in ogni area.
6. Integrare gli oggetti Criteri di gruppo tramite Desired State Configuration (DSC):
    1. Convertire l'oggetto Criteri di gruppo in DSC: il [progetto di gestione della baseline Microsoft](https://github.com/Microsoft/BaselineManagement) in GitHub può aiutare ad accelerare questa operazione. * Assicurarsi di archiviare la configurazione DSC nell'archivio in parallelo con i modelli di Resource Manager.
    2. Distribuire la configurazione dello stato di Automazione di Azure in tutte le istanze della sottoscrizione IT aziendale. È possibile usare Automazione di Azure per applicare la configurazione DSC alle macchine virtuali distribuite nelle sottoscrizioni supportate all'interno del gruppo di gestione.
    3. La roadmap corrente prevede l'abilitazione di criteri di configurazione guest personalizzati. Dopo il rilascio di questa funzionalità, non sarà più necessario usare Automazione di Azure per questa procedura consigliata.

**Applicazione di governance aggiuntiva a una sottoscrizione di adozione del cloud (spoke)**: Basandosi sulla sottoscrizione `Corporate IT Subscription`, piccole modifiche all'MVP per la governance applicate a ogni sottoscrizione dedicata al supporto degli archetipi dell'applicazione possono portare a una rapida evoluzione.

Nelle precedenti evoluzioni della procedura consigliata, venivano definiti gruppi di sicurezza di rete che bloccavano il traffico pubblico e consentivano il traffico interno. Il progetto di Azure creava inoltre temporaneamente funzionalità di rete perimetrale e Active Directory. In questa evoluzione, gli asset verranno leggermente perfezionati, creando una nuova versione del progetto di Azure.

1. Modello di peering di rete. Questo modello consentirà il peering della rete virtuale in ogni sottoscrizione con la rete virtuale dell'hub nella sottoscrizione IT aziendale.
    1. Le indicazioni della sezione precedente, relativa all'[architettura hub-spoke con servizi condivisi di riferimento][shared-services] permettono di generare un modello di Resource Manager per l'abilitazione del peering reti virtuali.
    2. Questo modello può essere usato come guida per modificare il modello di rete perimetrale dell'evoluzione della governance precedente.
    3. Essenzialmente, si aggiunge ora il peering reti virtuali alla rete virtuale perimetrale connessa in precedenza al dispositivo perimetrale locale tramite VPN.
    4. *** È anche consigliabile rimuovere la VPN dal modello e assicurarsi che il traffico non venga instradato direttamente al data center locale, senza passare attraverso la soluzione di firewall e la sottoscrizione IT aziendale.
    5. Sarà necessaria una [configurazione di rete](/azure/automation/automation-dsc-overview#network-planning) aggiuntiva in Automazione di Azure per applicare la configurazione DSC alle macchine virtuali ospitate.
2. Modificare il gruppo di sicurezza di rete. Bloccare tutto il traffico pubblico E locale diretto nel gruppo di sicurezza di rete. Il traffico in ingresso deve arrivare solo attraverso il peering reti virtuali nella sottoscrizione IT aziendale.
    1. Nell'evoluzione precedente, veniva creato un gruppo di sicurezza di rete per bloccare tutto il traffico pubblico e consentire tutto il traffico interno. Ora si vuole modificare leggermente questo gruppo di sicurezza di rete.
    2. La nuova configurazione del gruppo di sicurezza di rete deve bloccare tutto il traffico pubblico e tutto il traffico dal data center locale.
    3. Il traffico in ingresso nella rete virtuale deve provenire solo dalla rete virtuale sull'altro lato del peering reti virtuali.
3. Implementazione del Centro sicurezza di Azure
    1. Configurare il Centro sicurezza di Azure per qualsiasi gruppo di gestione che contiene le classificazioni dei dati protetti.
    2. Attivare il provisioning automatico per impostazione predefinita per garantire la conformità delle patch.
    3. Definire configurazioni di sicurezza del sistema operativo. La configurazione verrà definita dal team responsabile della sicurezza IT.
    4. Supportare il team responsabile della sicurezza IT nell'uso iniziale del Centro sicurezza di Azure. Passare la responsabilità dell'uso del Centro sicurezza al team responsabile della sicurezza IT, ma mantenere l'accesso allo scopo di migliorare continuamente la governance.
    5. Creare un modello di Resource Manager che riflette le modifiche necessarie per la configurazione del Centro sicurezza di Azure all'interno di una sottoscrizione.
4. Aggiornare Criteri di Azure per tutte le sottoscrizioni.
    1. Controllare e applicare la criticità e la classificazione dei dati in tutti i gruppi di gestione e le sottoscrizioni per identificare eventuali sottoscrizioni con classificazioni di dati protetti.
    2. Controllare e imporre l'uso delle sole immagini del sistema operativo approvate.
    3. Controllare e applicare le configurazioni guest basate sui requisiti di sicurezza per ogni nodo.
5. Aggiornare Criteri di Azure per tutte le sottoscrizioni che contengono classificazioni di dati protetti.
    1. Controllare e imporre l'uso solo dei ruoli standard.
    2. Controllare e imporre l'uso della crittografia per tutti gli account di archiviazione e i file inattivi nei singoli nodi.
    3. Controllare e imporre l'applicazione della nuova versione del gruppo di sicurezza di rete della rete perimetrale.
    4. Controllare e imporre l'uso di reti virtuali e subnet di rete approvate per ogni interfaccia di rete.
    5. Controllare e imporre la limitazione delle tabelle di routing definite dall'utente.
6. Azure Blueprints:
    1. Creare un progetto di Azure denominato `protected-data`.
    2. Aggiungere il peering reti virtuali, il gruppo di sicurezza di rete e i modelli del Centro sicurezza di Azure al progetto.
    3. Verificare che il modello per Active Directory dell'evoluzione precedente NON sia incluso nel progetto. Eventuali dipendenze in Active Directory verranno fornite dalla sottoscrizione IT aziendale.
    4. Terminare le macchine virtuali Active Directory esistenti distribuite nell'evoluzione precedente.
    5. Aggiungere i nuovi criteri per le sottoscrizioni con dati protetti.
    6. Pubblicare il progetto in qualsiasi gruppo di gestione destinato a ospitare dati protetti.
    7. Applicare il nuovo progetto a ogni sottoscrizione interessata, nonché ai progetti esistenti.

## <a name="conclusion"></a>Conclusioni

L'aggiunta di questi processi e modifiche all'MVP per la governance aiuta a mitigare molti dei rischi associati alla governance della sicurezza. Nel loro insieme aggiungono gli strumenti per il monitoraggio di rete, identità e sicurezza necessari per proteggere i dati.

## <a name="next-steps"></a>Passaggi successivi

In un'epoca in cui l'adozione del cloud continua a evolversi e a offrire valore aziendale aggiunto, anche i rischi e le esigenze di governance del cloud sono destinati a evolversi. Per la società fittizia in questo percorso, il passaggio successivo è supportare i carichi di lavoro cruciali. Questo è il punto in cui diventano necessari i controlli di coerenza delle risorse.

> [!div class="nextstepaction"]
> [Evoluzione della coerenza delle risorse](./resource-consistency-evolution.md)

<!-- links -->

[shared-services]: ../../../../reference-architectures/hybrid-networking/shared-services.md