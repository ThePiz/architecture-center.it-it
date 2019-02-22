---
title: 'CAF: Piccole e medie imprese - Evoluzione della baseline di sicurezza'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Spiegazione per piccole e medie imprese - Evoluzione della baseline di sicurezza
author: BrianBlanchard
ms.openlocfilehash: 5714b886ef63cc2392905250d97ea905839f6011
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901462"
---
# <a name="caf-small-to-medium-enterprise-security-baseline-evolution"></a>CAF: Piccole e medie imprese: Evoluzione della baseline di sicurezza

Questo articolo approfondisce la presentazione dello scenario aggiungendo i controlli di sicurezza a supporto dello spostamento dei dati protetti nel cloud.

## <a name="evolution-of-the-narrative"></a>Evoluzione dello scenario

I responsabili IT e aziendali sono soddisfatti dei risultati della sperimentazione iniziale condotta dai team responsabili di IT, sviluppo di app e business intelligence. Per concretizzare valore tangibile per l'azienda da questi esperimenti, tali team devono essere autorizzati a integrare i dati protetti nelle soluzioni. Ciò richiede modifiche dei criteri aziendali, ma anche un'evoluzione delle implementazioni di governance del cloud prima che i dati protetti possano raggiungere il cloud.

### <a name="evolution-of-the-cloud-governance-team"></a>Evoluzione del team di governance del cloud

Considerato l'impatto dei cambiamenti dello scenario e il supporto offerto finora, il team di governance del cloud è ora visto in modo diverso. I due amministratori di sistema che hanno avviato il team vengono ora considerati cloud architect esperti. Con lo sviluppo dello scenario, verranno percepiti inizialmente come custodi del cloud e infine più come guardiani del cloud.

Sebbene la differenza sia sottile, si tratta di una distinzione importante per lo sviluppo di una cultura IT orientata alla governance. Un custode del cloud mette a posto i pasticci di cloud architect innovativi. I due ruoli hanno obiettivi naturalmente in conflitto e opposti. Lo scopo di un guardiano del cloud è invece quello di mantenere sicuro il cloud, in modo che altri cloud architect possano procedere rapidamente, senza troppi pasticci. Un guardiano del cloud è anche coinvolto nella creazione di modelli per accelerare la distribuzione e l'adozione, diventando così un promotore di innovazione oltre che difensore delle cinque discipline del cloud.

### <a name="evolution-of-the-current-state"></a>Evoluzione dello stato attuale

All'inizio di questo scenario, i team di sviluppo delle applicazioni lavoravano ancora in modalità sviluppo/test e il team di business intelligence era ancora in fase di sperimentazione. Il reparto IT gestiva operativamente due ambienti di infrastruttura ospitati, denominati PROD e DR.

Da allora, sono avvenuti alcuni cambiamenti che influiranno sulla governance:

- Il team di sviluppo delle applicazioni ha implementato una pipeline CI/CD per distribuire un'applicazione nativa del cloud con un'esperienza utente migliorata. L'app interagisce ancora con i dati protetti, pertanto non è pronta per l'ambiente di produzione.
- Il team che si occupa di business intelligence in ambito IT cura attivamente i dati nel cloud, dalla logistica, all'inventario fino ai dati di terze parti. Questi dati vengono usati per effettuare nuove stime, che possono influire sui processi aziendali. Tuttavia, queste stime e informazioni dettagliate non hanno utilità pratica fino a quando i dati finanziari e dei clienti non possono essere integrati nella piattaforma di dati.
- Il team IT sta procedendo con i piani del CIO e del CFO per il ritiro del data center DR. Più di 1.000 dei 2.000 asset nel data center DR sono stati ritirati o migrati.
- I criteri definiti in modo generico relativi alle informazioni personali e ai dati finanziari sono stati modernizzai. Tuttavia, i nuovi criteri aziendali dipendono dall'implementazione dei criteri di governance e sicurezza correlati. I team sono ancora bloccati.

### <a name="evolution-of-the-future-state"></a>Evoluzione dello stato futuro

Dagli esperimenti iniziali dei team di sviluppo delle applicazioni e di business intelligence è emersa la possibilità di apportare miglioramenti per quanto riguarda l'esperienza dei clienti e le decisioni basate sui dati. Entrambi i team voglio espandere l'adozione del cloud nei prossimi 18 mesi distribuendo tali soluzioni nell'ambiente di produzione.

Nei sei mesi rimanenti, il team di governance del cloud implementerà i requisiti di governance e sicurezza per consentire ai team di adozione del cloud di eseguire la migrazione dei dati protetti in tali data center.

I cambiamenti che riguardano lo stato attuale e quello futuro creano nuovi rischi che richiedono nuove definizioni di criteri.

## <a name="evolution-of-tangible-risks"></a>Evoluzione dei rischi tangibili

**Violazione dei dati**: quando si adotta qualsiasi nuova piattaforma di dati, si verifica un aumento intrinseco delle responsabilità correlate alle potenziali violazioni dei dati. I tecnici che adottano le tecnologie cloud hanno maggiori responsabilità di implementare soluzioni in grado di ridurre questo rischio. È necessario implementare una solida strategia di governance e sicurezza per fare in modo che i tecnici adempiano a queste responsabilità.

Questo rischio aziendale può tradursi in alcuni rischi tecnici:

- Applicazioni cruciali o dati protetti potrebbero venire distribuiti involontariamente.
- Dati protetti potrebbero venire esposti in fase di archiviazione a causa di scelte non adeguate in relazione alla crittografia.
- Utenti non autorizzati potrebbero accedere ai dati protetti.
- Un'intrusione esterna potrebbe consentire l'accesso ai dati protetti.
- Eventuali intrusioni dall'esterno o attacchi Denial of Service potrebbero causare un'interruzione delle attività aziendali.
- Modifiche all'interno dell'organizzazione o tra i dipendenti potrebbero consentire l'accesso non autorizzato ai dati protetti.
- Nuovi exploit potrebbero creare nuove opportunità di intrusione o accesso.
- Processi di distribuzione incoerenti potrebbero produrre lacune a livello di sicurezza che possono causare perdite di dati o interruzioni.
- Deviazioni di configurazione e patch non applicate potrebbero produrre lacune a livello di sicurezza non intenzionali che possono causare perdite di dati o interruzioni.

## <a name="evolution-of-the-policy-statements"></a>Evoluzione delle definizioni dei criteri

Le modifiche ai criteri indicate di seguito contribuiranno a mitigare i nuovi rischi e a gestire l'implementazione. L'elenco può sembrare lungo, ma l'adozione di questi criteri può essere più semplice di quanto non sembri.

1. Tutti gli asset distribuiti devono essere ordinati in categorie in base a criticità e classificazione dei dati. Le classificazioni devono essere esaminate dal team di governance del cloud e dal proprietario dell'applicazione prima della distribuzione nel cloud.
2. Le applicazioni che archiviano o accedono ai dati protetti devono essere gestite in modo diverso rispetto a quelle che non lo fanno. Come minimo, devono essere segmentate per evitare accessi non intenzionali ai dati protetti.
3. Tutti i dati protetti devono essere crittografati quando inattivi.
4. Le autorizzazioni con privilegi elevati in qualsiasi segmento contenente dati protetti devono essere un'eccezione. Qualsiasi eccezione verrà registrata dal team di governance del cloud e controllata regolarmente.
5. Le subnet di rete contenenti dati protetti devono essere isolate dalle altre subnet. Il traffico di rete tra le subnet di dati protetti verrà controllato regolarmente.
6. Alle subnet contenenti dati protetti non deve essere possibile accedere direttamente tramite la rete Internet pubblica o tra data center. L'accesso a tali subnet deve essere instradato tramite subnet intermedie. Tutti gli accessi a tali subnet devono passare attraverso una soluzione firewall in grado di eseguire la scansione dei pacchetti e applicare funzioni di blocco.
7. Gli strumenti di governance devono controllare e soddisfare i requisiti di configurazione della rete definiti dal team di gestione della sicurezza.
8. Gli strumenti di governance devono limitare la distribuzione di macchine virtuali solo alle immagini approvate.
9. Quando possibile, la gestione della configurazione dei nodi deve applicare i requisiti dei criteri alla configurazione di qualsiasi sistema operativo guest.
10. Gli strumenti di governance devono imporre l'abilitazione degli aggiornamenti automatici per tutti gli asset distribuiti. Le violazioni devono essere esaminate con i team di gestione operativa e risolte in base ai criteri relativi alle operazioni. Gli asset che non vengono aggiornati automaticamente devono essere inclusi in processi di proprietà del team responsabile delle operazioni IT.
11. La creazione di nuovi gruppi di gestione o sottoscrizioni per le applicazioni cruciali o i dati protetti richiede una revisione da parte del team di governance del cloud per garantire la corretta assegnazione del progetto.
12. Un modello di accesso con privilegi minimi verrà applicato a qualsiasi gruppo di gestione o sottoscrizione che contiene app cruciali o dati protetti.
13. Tendenze ed exploit che possono influire sulle distribuzioni cloud devono essere esaminati regolarmente dal team responsabile della sicurezza in modo da fornire aggiornamenti per gli strumenti di gestione della sicurezza usati nel cloud.
14. Gli strumenti di distribuzione devono essere approvati dal team di governance del cloud per garantire la governance regolare degli asset distribuiti.
15. Gli script di distribuzione devono essere conservati in un archivio centrale accessibile dal team di governance del cloud per la revisione e il controllo periodici.
16. I processi di governance devono includere controlli in fase di distribuzione e in base a cicli regolari per garantire la coerenza tra tutti gli asset.
17. Per la distribuzione di applicazioni che richiedono l'autenticazione del cliente è necessario usare un provider di identità approvato compatibile con il provider di identità primario per gli utenti interni.
18. I processi di governance del cloud devono includere revisioni trimestrali con i team di gestione delle identità. Queste revisioni possono essere utili per identificare attori o modelli di utilizzo dannosi, che dovrebbero essere bloccati dalla configurazione degli asset del cloud.

## <a name="evolution-of-the-best-practices"></a>Evoluzione delle procedure consigliate

La progettazione dell'MVP per la governance si evolverà in modo da includere nuovi criteri di Azure e un'implementazione di Gestione costi di Azure. Insieme, queste due modifiche di progettazione riusciranno a soddisfare le nuove definizioni dei criteri aziendali.

1. I team responsabili della rete e della sicurezza IT definiranno i requisiti di rete. Il team di governance del cloud offrirà supporto per la conversazione.
2. I team responsabili delle identità e della sicurezza IT definiranno i requisiti di identità e apporteranno le eventuali modifiche necessarie all'implementazione di Active Directory locale. Il team di governance del cloud verificherà le modifiche.
3. Creare un archivio in Azure DevOps per l'archiviazione e il controllo delle versioni di tutti i modelli di Azure Resource Manager e le configurazioni tramite script pertinenti.
4. Implementazione del Centro sicurezza di Azure:
    1. Configurare il Centro sicurezza di Azure per qualsiasi gruppo di gestione che contiene le classificazioni dei dati protetti.
    2. Attivare il provisioning automatico per impostazione predefinita per garantire la conformità delle patch.
    3. Definire configurazioni di sicurezza del sistema operativo. La configurazione verrà definita dal team responsabile della sicurezza IT.
    4. Supportare il team responsabile della sicurezza IT per l'uso iniziale del Centro sicurezza. Passare la responsabilità dell'uso del Centro sicurezza al team responsabile della sicurezza IT, ma mantenere l'accesso allo scopo di migliorare continuamente la governance.
    5. Creare un modello di Resource Manager che riflette le modifiche necessarie per la configurazione del Centro sicurezza all'interno di una sottoscrizione.
5. Aggiornare i criteri di Azure per tutte le sottoscrizioni:
    1. Controllare e applicare la criticità e la classificazione dei dati in tutti i gruppi di gestione e le sottoscrizioni per identificare quelle con classificazioni di dati protetti.
    2. Controllare e imporre l'uso delle sole immagini approvate.
6. Aggiornare i criteri di Azure per tutte le sottoscrizioni che contengono classificazioni di dati protetti:
    1. Controllare e imporre l'uso dei soli ruoli di controllo degli accessi in base al ruolo di Azure.
    2. Controllo e imporre la crittografia per tutti gli account di archiviazione e i file inattivi nei singoli nodi.
    3. Controllare e imporre l'applicazione di un gruppo di sicurezza di rete per tutte le schede di interfaccia di rete e subnet. I team responsabili della rete e della sicurezza IT definiranno il gruppo di sicurezza di rete.
    4. Controllare e imporre l'uso di reti virtuali e subnet di rete approvate per ogni interfaccia di rete.
    5. Controllare e imporre la limitazione delle tabelle di routing definite dall'utente.
    6. Applicare i criteri predefiniti per la configurazione guest come indicato di seguito:
        1. Controllare che i server Web Windows usino protocolli di comunicazione sicuri
        2. Controllare che le impostazioni di sicurezza della password siano configurate correttamente nelle macchine virtuali Linux e Windows
7. Configurazione del firewall:
    1. Identificare una configurazione di Firewall di Azure che soddisfi i requisiti di sicurezza necessari. In alternativa, identificare un'appliance di terze parti compatibile con Azure.
    2. Creare un modello di Resource Manager per distribuire il firewall con le configurazioni necessarie.
8. Azure Blueprints:
    1. Creare un nuovo progetto denominato `protected-data`.
    2. Aggiungere il firewall e i modelli del Centro sicurezza di Azure al progetto.
    3. Aggiungere i nuovi criteri per le sottoscrizioni con dati protetti.
    4. Pubblicare il progetto in qualsiasi gruppo di gestione che prevede attualmente l'hosting di dati protetti.
    5. Applicare il nuovo progetto a ogni sottoscrizione interessata, nonché ai progetti esistenti.

## <a name="conclusion"></a>Conclusioni

L'aggiunta di questi processi e modifiche all'MVP per la governance aiutano a mitigare molti dei rischi associati alla governance della sicurezza. Nel loro insieme aggiungono gli strumenti per il monitoraggio di rete, identità e sicurezza necessari per proteggere i dati.

## <a name="next-steps"></a>Passaggi successivi

In un'epoca in cui l'adozione del cloud continua a evolversi e a offrire valore aziendale aggiunto, anche i rischi e le esigenze di governance del cloud sono destinati a evolversi. Per la società fittizia in questo percorso, il passaggio successivo è supportare i carichi di lavoro cruciali. Questo è il punto in cui diventano necessari i controlli di coerenza delle risorse.

> [!div class="nextstepaction"]
> [Evoluzione della coerenza delle risorse](./resource-consistency-evolution.md)
