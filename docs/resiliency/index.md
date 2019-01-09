---
title: Progettazione di applicazioni resilienti per Azure
description: Come creare applicazioni resilienti in Azure per disponibilità elevata e ripristino di emergenza.
author: MikeWasson
ms.date: 12/18/2018
ms.custom: resiliency
ms.openlocfilehash: 28ad589c6d54a1574b5cd5c4f08e3c6adfe349c3
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113128"
---
# <a name="designing-resilient-applications-for-azure"></a>Progettazione di applicazioni resilienti per Azure

In un sistema distribuito si presentano errori. E gli hardware possono danneggiarsi. La rete, poi, può avere errori temporanei. Raramente un intero servizio o area può subire delle interruzioni, tuttavia è necessario pianificare anche questo tipo di problemi.

La creazione di un'applicazione affidabile nel cloud è diversa rispetto alla creazione di un'applicazione affidabile in un contesto aziendale. Mentre in passato si potrebbe aver acquistato un hardware di fascia alta per aumentare le prestazioni, in un ambiente cloud è necessario scalare orizzontalmente anziché verticalmente. I costi degli ambienti cloud sono mantenuti bassi dall'uso di hardware appositi. Invece di provare a evitare completamente gli errori, l'obiettivo deve essere quello di ridurre al minimo gli effetti di un errore all'interno del sistema.

Questo articolo fornisce una panoramica sulla creazione di applicazioni resilienti in Microsoft Azure e inizia con una definizione del termine *resilienza* e dei concetti correlati. Quindi, viene descritto un processo per ottenere la resilienza, adottando un approccio strutturato in base alla durata di un'applicazione, dalla progettazione e implementazione alla distribuzione, fino alle operazioni.

<!-- markdownlint-disable MD026 -->

## <a name="what-is-resiliency"></a>Cos'è la resilienza?

<!-- markdownlint-enable MD026 -->

La **resilienza** è la capacità di un sistema di correggere gli errori e continuare a funzionare. Non significa *evitare*, ma *rispondere* agli errori in modo da evitare tempi di inattività o perdita di dati. L'obiettivo della resilienza consiste nel ripristinare uno stato completamente funzionale dell'applicazione dopo un errore.

Due aspetti importanti della resilienza sono la disponibilità elevata e il ripristino di emergenza.

- La **disponibilità elevata** è la capacità dell'applicazione di continuare a essere eseguita in uno stato integro, senza tempi di inattività significativi. Per "stato integro" si intende che l'applicazione è reattiva e gli utenti possono connettersi all'applicazione e interagire con essa.  
- Il **ripristino di emergenza** è la capacità di correggere eventi imprevisti rari ma gravi, ad esempio errori non temporanei su larga scala, come un'interruzione del servizio che interessa un'intera area. Il ripristino di emergenza include il backup dei dati e l'archiviazione e può includere l'intervento manuale, ad esempio il ripristino di un database dal backup.

Un modo per pensare alla disponibilità elevata e al ripristino di emergenza è che il secondo ha inizio quando l'impatto di un errore supera la capacità che la prima ha di gestirlo.  

Quando si progetta per la resilienza, è necessario comprendere i requisiti di disponibilità. Quanto tempo di inattività è accettabile? La risposta è in parte in funzione dei costi. Quanto costa un potenziale tempo di inattività all'azienda? Quanto si dovrebbe investire per rendere l'applicazione a disponibilità elevata? È necessario anche definire cosa significa che l'applicazione debba essere disponibile. Ad esempio, se un cliente può inviare un ordine, ma il sistema non è in grado di elaborarlo nel normale intervallo di tempo, si può dire che l'applicazione non sia disponibile? È necessario anche considerare le probabilità che un tipo particolare di interruzione si verifichi e se sia conveniente una strategia di mitigazione.

Un altro termine comune è la **continuità aziendale**, cioè la capacità di eseguire funzioni di business essenziali durante e dopo il verificarsi di condizioni avverse, come una calamità naturale o l'inattività di un servizio. La continuità aziendale copre tutte le operazioni di un'azienda, tra cui le strutture fisiche, le persone, le comunicazioni, i trasporti e i servizi IT. Questo articolo si focalizza sulle applicazioni cloud, ma la pianificazione della resilienza deve essere eseguita nel contesto dei requisiti complessivi della continuità aziendale.

Il **backup dei dati** costituisce una parte essenziale del ripristino di emergenza. In caso di errore nei componenti senza stato di un'applicazione, è comunque possibile ridistribuirli. Ma se si verificano perdite di dati, non è possibile riportare il sistema a uno stato stabile. In caso di un'emergenza a livello di area, è necessario eseguire il backup dei dati, possibilmente in un'area diversa.

Il backup è un'operazione diversa rispetto alla *replica dei dati*. La replica dei dati implica la copia dei dati quasi in tempo reale, in modo che il sistema possa effettuare rapidamente il failover a una replica. Molti sistemi di database supportano la replica, ad esempio SQL Server supporta i gruppi di disponibilità Always On di SQL Server. La funzionalità di replica dei dati può ridurre il tempo necessario per il ripristino da un'interruzione, garantendo la disponibilità costante di una replica dei dati, ma non offre alcuna protezione in caso di errore umano. Se i dati risultano danneggiati a causa di un errore umano, vengono comunque copiati nelle repliche. È quindi necessario includere comunque il backup a lungo termine nella strategia di ripristino di emergenza.

## <a name="process-to-achieve-resiliency"></a>Processo per ottenere la resilienza

La resilienza non è un componente aggiuntivo, ma deve essere progettata nel sistema e messa in pratica operativa. Di seguito è riportato un modello generale da seguire:

1. **Definire** i requisiti di disponibilità in base alle esigenze aziendali.
2. **Progettare** l'applicazione ai fini della resilienza: iniziare con un'architettura che segue procedure comprovate e quindi identificare i possibili punti di errore presenti.
3. **Implementare** strategie per rilevare e correggere gli errori.
4. **Testare** l'implementazione attraverso la simulazione di errori e l'attivazione di failover forzati.
5. **Distribuire** l'applicazione nell'ambiente di produzione servendosi di un processo ripetibile e affidabile.
6. **Monitorare** l'applicazione per rilevare gli errori: monitorando il sistema, è possibile misurare l'integrità dell'applicazione e rispondere agli eventi imprevisti, se necessario.
7. **Rispondere** se si verificano errori che richiedono interventi manuali.

Nella parte restante di questo articolo verrà approfondito più nel dettaglio ognuno di questi passaggi.

## <a name="define-your-availability-requirements"></a>Definire i requisiti di disponibilità

La pianificazione della resilienza inizia con i requisiti aziendali. Di seguito sono illustrati alcuni approcci per considerare la resilienza in questi termini.

### <a name="decompose-by-workload"></a>Scomporre in base al carico di lavoro

Molte soluzioni cloud sono composte da carichi di lavoro di più applicazioni. Il termine "carico di lavoro" in questo contesto indica una capacità discreta, o attività di elaborazione, che può essere logicamente separata da altre attività, in termini di requisiti di logica di business e di archiviazione dei dati. Per esempio, un'applicazione di e-commerce potrebbe includere i carichi di lavoro seguenti:

- Esplorare e cercare un catalogo di prodotti.
- Creare e tenere traccia degli ordini.
- Visualizzare le raccomandazioni.

Questi carichi di lavoro potrebbero avere requisiti diversi di disponibilità, scalabilità, coerenza dei dati e ripristino di emergenza. Per prendere alcune decisioni aziendali, è necessario bilanciare costi e rischi.

È necessario considerare anche i modelli di utilizzo: esistono determinati periodi critici in cui il sistema deve essere disponibile? Ad esempio, un servizio di dichiarazione dei redditi non può interrompersi proprio prima della scadenza, o ancora, un servizio di streaming video deve rimanere in piedi durante un grande evento sportivo e così via. Durante i periodi critici, potrebbero esserci distribuzioni ridondanti in più aree, perciò l'applicazione potrebbe effettuare il failover se si presentano errori in un'area. Tuttavia, una distribuzione in più aree è potenzialmente più costosa, perciò, nei periodi meno critici, si potrebbe eseguire l'applicazione in una sola area. In alcuni casi, è possibile ridurre i costi aggiuntivi ricorrendo alle moderne tecniche serverless, che si basano sulla fatturazione in base al consumo, in modo da evitare addebiti per le risorse di calcolo sottoutilizzate.

### <a name="rto-and-rpo"></a>RTO e RPO

Due metriche importanti da considerare sono l'obiettivo del tempo di ripristino e l'obiettivo del punto di ripristino, dal momento che sono riconducibili al ripristino di emergenza.

- L'**obiettivo del tempo di ripristino** (RTO) è il tempo massimo accettabile che un'applicazione non sia disponibile dopo un evento imprevisto. Se l'obiettivo RTO è di 90 minuti, è necessario essere in grado di ripristinare l'applicazione a uno stato di esecuzione entro 90 minuti dall'inizio di un'emergenza. Se si ha un obiettivo RTO basso, si potrebbe mantenere una seconda distribuzione a livello di area che esegue continuamente una configurazione attiva/passiva in standby, per evitare un'interruzione a livello di area. In alcuni casi è possibile distribuire una configurazione attiva/attiva per raggiungere un obiettivo RTO persino inferiore.

- **Obiettivo del punto di ripristino** (RPO) è la durata massima di perdita dei dati accettabile durante un'emergenza. Per esempio, se si archiviano i dati in un singolo database, senza replica in altri database, e si eseguono backup orari, si potrebbero perdere fino a un'ora di dati.

Gli obiettivi RTO e RPO sono requisiti non funzionali di un sistema e dipendono dai requisiti aziendali. Per ottenere questi valori, è consigliabile condurre una valutazione dei rischi e comprendere chiaramente i costi correlati a tempo di inattività o perdita di dati.

### <a name="mttr-and-mtbf"></a>MTTR e MTBF

Due altre misure comuni della disponibilità sono il tempo medio di recupero (MTTR) e il tempo medio tra gli errori (MTBF). Queste misure vengono in genere usate internamente dal provider di servizi per determinare i punti in cui aggiungere ridondanza ai servizi cloud e quali contratti di servizio offrire ai clienti.

Il **tempo medio di recupero**  (MTTR) corrisponde al tempo medio necessario per recuperare un componente dopo un errore. Il valore MTTR è un dato empirico relativo a un componente. In base al valore MTTR di ogni componente, è possibile stimare il valore MTTR di un'intera applicazione. Se si creano applicazioni da più componenti con valori MTTR bassi, si otterrà un'applicazione con un valore MTTR complessivo basso, che consente di recuperare rapidamente in caso di errore.

Il **tempo medio tra gli errori** (MTBF) corrisponde al tempo di esecuzione previsto di un componente tra un'interruzione e l'altra. Questa metrica può essere utile per calcolare la frequenza con cui un servizio non sarà più disponibile. Un componente non affidabile presenta un valore MTBF basso e di conseguenza anche il numero del contratto di servizio per il componente sarà basso. È però possibile attenuare l'impatto di un valore MTBF basso distribuendo più istanze del componente e implementando il failover tra di esse.

> [!NOTE]
> Se uno qualsiasi dei valori MTTR dei componenti in una configurazione a disponibilità elevata supera l'obiettivo RTO del sistema, un errore di sistema causerà un'interruzione delle attività inaccettabile. Non sarà possibile ripristinare il sistema entro l'obiettivo RTO definito.

### <a name="slas"></a>Contratti di servizio

In Azure i [Contratti di servizio][sla] descrivono gli impegni di Microsoft in merito a tempi di attività e connettività. Se il contratto per un determinato servizio è del 99,9%, significa che è necessario prevedere che il servizio sia disponibile per il 99,9% del tempo.

> [!NOTE]
> Il contratto di servizio di Azure include non solo indicazioni per ottenere un credito per il servizio qualora il contratto di servizio non venga soddisfatto, ma anche definizioni specifiche di "disponibilità" per ogni servizio. Questo aspetto del contratto di servizio funge da criterio di imposizione.

È necessario definire i propri contratti di servizio di destinazione per ogni carico di lavoro nella propria soluzione. Un contratto di servizio consente di valutare se l'architettura soddisfi i requisiti aziendali. Ad esempio, se un carico di lavoro richiede il 99,99% del tempo di attività, ma dipende da un servizio con un contratto del 99,9%, tale servizio non può essere un punto singolo di errore nel sistema. Un rimedio è avere un percorso di fallback nel caso in cui il servizio si interrompa, o intraprenda altre misure per il correggere un errore nel servizio.

La tabella seguente illustra il potenziale tempo totale di inattività per vari livelli di contratto di servizio.

| Contratto di servizio | Tempo di inattività settimanale | Tempo di inattività mensile | Tempo di inattività annuale |
| --- | --- | --- | --- |
| 99% |1,68 ore |7,2 ore |3,65 giorni |
| 99,9% |10,1 minuti |43,2 minuti |8,76 ore |
| 99,95% |5 minuti |21,6 minuti |4,38 ore |
| 99,99% |1,01 minuti |4,32 minuti |52,56 minuti |
| 99,999% |6 secondi |25,9 secondi |5,26 minuti |

Naturalmente, a parità di condizioni, una maggiore disponibilità è migliore. Tuttavia, quando si cercano di ottenere più 9, i costi e la complessità per raggiungere quel livello di disponibilità aumentano. Un tempo di attività pari al 99,99% si traduce in circa 5 minuti di inattività totale al mese. Vale la pena sostenere questa maggiore complessità e questi costi aggiuntivi per raggiungere cinque 9? La risposta dipende dai requisiti aziendali.

Di seguito sono riportate alcune altre considerazioni che emergono quando si definisce un contratto di servizio:

- Per ottenere quattro 9 (99,99%), non ci si può probabilmente basare su un intervento manuale per correggere gli errori. L'applicazione, infatti, deve essere in grado di eseguire automaticamente la diagnosi e la riparazione.
- Dopo il quarto 9, è difficile rilevare le interruzioni in modo sufficientemente rapido per soddisfare il contratto di servizio.
- Si pensi all'intervallo di tempo rispetto al quale viene misurato il contratto di servizio: minore è la finestra, più ristretto sarà il livello di tolleranza. Probabilmente non ha senso definire il contratto di servizio in termini di tempo di attività oraria o giornaliera.
- Prendere in considerazione le misurazioni MTBF e MTTR. Più basso è il livello del contratto di servizio, meno frequentemente il servizio può subire interruzioni e più rapidamente deve essere ripristinato.

### <a name="composite-slas"></a>Contratti di servizio compositi

Si consideri un'app Web del servizio app che scrive nel database SQL di Azure. Al momento della redazione del presente documento, tali servizi Azure dispongono dei contratti di servizio seguenti:

- App Web del servizio app = 99,95%
- Database SQL = 99,99%

![Contratto di servizio composito](./images/sla1.png)

A quanto corrisponde il tempo di inattività massimo che ci si aspetta da questa applicazione? Se il servizio presenta errori, di conseguenza l'intera applicazione presenterà errori. In generale, la probabilità di interruzione di ogni servizio è indipendente, pertanto il contratto di servizio composito per l'applicazione è pari a 99,95% &times; 99,99% = 99,94%. Questa percentuale è inferiore ai singoli contratti di servizio e questo non è sorprendente, poiché un'applicazione che si basa su più servizi ha anche più punti di errore potenziali.

D'altra parte, è possibile migliorare il contratto di servizio composito creando percorsi di fallback indipendenti. Ad esempio, se il database SQL non è disponibile, inserire le transazioni in una coda, in modo tale che vengano elaborate successivamente.

![Contratto di servizio composito](./images/sla2.png)

Con questa struttura, l'applicazione è ancora disponibile anche se non si può connettere al database. Tuttavia, se la coda e il database si interrompono allo stesso tempo, l'applicazione si interromperà. La percentuale di tempo prevista per un errore simultaneo è pari a 0,0001 &times; 0,001 e perciò il contratto di servizio composito per questo percorso combinato equivale a:  

- Database O coda = 1,0 &minus; (0,0001 &times; 0,001) = 99,99999%

Il contratto di servizio composito totale è:

- App Web E (database O coda) = 99,95% &times; 99,99999% = ~99,95%

Ci sono, però, compromessi a questo approccio. La logica dell'applicazione è più complessa, si sta pagando per la coda e ci possono essere problemi di coerenza dei dati da considerare.

**Contratto di servizio per distribuzioni in più aree**. Un'altra tecnica a disponibilità elevata consiste nel distribuire l'applicazione in più aree e usare Gestione traffico di Azure per effettuare il failover, se l'applicazione si interrompe in un'unica area. Per una distribuzione in più aree, il contratto di servizio viene calcolato come segue.

Si consideri *N* come il contratto di servizio composito per l'applicazione distribuita in un'area e *R* il numero di aree in cui viene distribuita l'applicazione. La probabilità prevista che l'applicazione si interrompa contemporaneamente in tutte le aree è data dalla formula ((1 &minus N) ^ R).

Ad esempio, se il contratto di servizio per area singola è pari al 99,95%:

- Contratto di servizio combinato per due aree = (1 &minus; (0,9995 ^ 2)) = 99,999975%
- Contratto di servizio combinato per quattro aree = (1 &minus; (0,9995 ^ 4)) = 99,999999%

È inoltre necessario tenere in considerazione il [Contratto di Servizio per Gestione traffico][tm-sla]. Al momento della redazione del presente documento, il contratto di servizio per Gestione traffico è pari a 99,99%.

Il failover non è inoltre istantaneo nelle configurazioni attiva-passiva e questa situazione può causare periodi di inattività durante un failover. Vedere [Monitoraggio degli endpoint di Gestione traffico][tm-failover].

Il numero calcolato dei contratti di servizio è una baseline utile, ma non fornisce tutte le informazioni riguardanti la disponibilità. Spesso un'applicazione può ridurre le prestazioni in modo non drastico e graduale nel caso di errore di un percorso non critico. Si consideri un'applicazione in cui viene visualizzato un catalogo di libri. Nel caso in cui l'applicazione non possa recuperare l'immagine di anteprima di una copertina, si potrebbe visualizzare un'immagine segnaposto. In tal caso, l'errore nell'ottenimento dell'immagine non riduce i tempi di attività dell'applicazione, sebbene condizioni l'esperienza utente.  

## <a name="design-for-resiliency"></a>Progettare per la resilienza

Durante la fase di progettazione, è consigliabile eseguire un'analisi della modalità di errore (FMA). L'obiettivo di quest'analisi è di identificare i possibili punti di errore e definire il modo in cui l'applicazione risponde a tali errori.

- Come rileverà l'applicazione questo tipo di errore?
- Come risponderà l'applicazione a questo tipo di errore?
- Come si potrà registrare e monitorare questo tipo di errore?

Per altre informazioni sul processo dell'analisi FMA, con indicazioni specifiche per Azure, vedere [Guida alla resilienza di Azure: Analisi della modalità di errore][fma].

### <a name="example-of-identifying-failure-modes-and-detection-strategy"></a>Esempio di identificazione di modalità di errore e di strategia di rilevamento

**Punto di errore:** chiamata a un servizio Web / API esterno.

| Modalità di errore | Strategia di rilevamento |
| --- | --- |
| Il servizio non è disponibile |HTTP 5xx |
| Limitazione |HTTP 429 (Troppe richieste) |
| Authentication |HTTP 401 (Non autorizzato) |
| Risposta lenta |Timeout della richiesta |

### <a name="redundancy-and-designing-for-failure"></a>Ridondanza e progettazione per gli errori

L'ambito di impatto degli errori può variare. Alcuni errori hardware, come nel caso di un disco danneggiato, possono influire su un singolo computer host. Un commutatore di rete non funzionante può invece influire su un intero rack di server. Gli errori che causano l'interruzione di un intero data center, ad esempio l'interruzione dell'alimentazione in un data center, sono invece meno comuni. Sono infine rari i casi in cui un'intera area risulta non disponibile.

Uno dei principali modi per rendere resiliente un'applicazione consiste nell'applicare la ridondanza. Ma la ridondanza va pianificata quando si progetta l'applicazione. Il livello di ridondanza necessario dipende inoltre dai requisiti aziendali: non tutte le applicazioni richiedono la ridondanza tra aree per proteggersi da un'interruzione dell'alimentazione a livello di area. In generale, è opportuno trovare il giusto compromesso tra incremento della ridondanza e dell'affidabilità e aumento dei costi e della complessità.  

In Azure sono disponibili numerose funzionalità per rendere ridondante un'applicazione a ogni livello di errore, a partire da una singola macchina virtuale fino a un'intera area.

![Funzionalità di resilienza di Azure](./images/redundancy.svg)

**Singola macchina virtuale**. Azure offre un [contratto di servizio relativo al tempo di attività](https://azure.microsoft.com/support/legal/sla/virtual-machines) per le singole macchine virtuali. La macchina virtuale deve usare l'archiviazione Premium per tutti i dischi di sistemi operativi e dischi dati. Anche se è possibile ottenere un contratto di servizio di livello superiore eseguendo due o più macchine virtuali, è possibile che una singola macchina virtuale sia già abbastanza affidabile per alcuni carichi di lavoro. Per i carichi di lavoro di produzione è tuttavia consigliabile usare due o più macchine virtuali per garantire la ridondanza.

**Set di disponibilità**. Per proteggersi da errori hardware localizzati, ad esempio un disco o un commutatore di rete non funzionante, distribuire due o più macchine virtuali in un set di disponibilità. Un set di disponibilità è costituito da due o più *domini di errore* che condividono una fonte di alimentazione e uno switch di rete comuni. Le macchine virtuali in un set di disponibilità vengono distribuite tra i domini di errore, in modo che, se un dominio di errore è interessato da un errore hardware, il traffico di rete possa comunque essere indirizzato alle macchine virtuali negli altri domini di errore. Per altre informazioni sui set di disponibilità, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure](/azure/virtual-machines/windows/manage-availability).

**Zone di disponibilità**.  Una zona di disponibilità è una zona fisicamente separata in un'area di Azure. Ogni zona di disponibilità può contare su risorse di alimentazione, rete e raffreddamento a sé. La distribuzione di macchine virtuali tra zone di disponibilità consente di proteggere un'applicazione in caso di errori a livello di data center. Le zone di disponibilità non sono supportate in tutte le aree. Per un elenco delle aree e dei servizi supportati, vedere [Informazioni sulle zone di disponibilità di Azure](/azure/availability-zones/az-overview).

Se si prevede di usare le zone di disponibilità nella distribuzione, verificare prima che l'architettura dell'applicazione e la codebase possano supportare questa configurazione. Se si intende distribuire software per componenti COTS, rivolgersi al fornitore del software ed eseguire test adeguati prima della distribuzione nell'ambiente di produzione. Un'applicazione deve essere in grado di mantenere lo stato ed evitare la perdita di dati durante un'interruzione all'interno della zona configurata. L'applicazione deve supportare l'esecuzione in un'infrastruttura elastica e distribuita senza dover specificare componenti dell'infrastruttura hardcoded nella codebase.

**Azure Site Recovery**.  Replicare le macchine virtuali di Azure in un'altra area di Azure per esigenze di continuità aziendale e ripristino di emergenza. È possibile condurre esercitazioni periodiche sul ripristino di emergenza per assicurarsi di soddisfare le esigenze di conformità. La VM verrà replicata con le impostazioni specificate nell'area selezionata in modo da consentire il ripristino delle applicazioni in caso di interruzioni nell'area di origine. Per altre informazioni, vedere l'articolo su come [replicare le VM di Azure con Azure Site Recovery][site-recovery]. Prendere in considerazione i valori di RTO e RPO per la soluzione e assicurarsi che durante il test il tempo e il punto di recupero siano adeguati alle proprie esigenze.

**Aree associate**. Per proteggere un'applicazione da un'interruzione dell'alimentazione a livello di area, è possibile distribuire l'applicazione in più aree, tramite Gestione traffico di Microsoft Azure in modo da distribuire il traffico Internet in aree diverse. Ogni area di Azure è associata a un'altra area e la combinazione di queste aree costituisce una [coppia di aree](/azure/best-practices-availability-paired-regions). Ad eccezione del Brasile meridionale, le coppie di aree hanno la stessa collocazione geografica in modo da soddisfare i requisiti di residenza dei dati ai fini della giurisdizione per le imposizioni fiscali e normative.

Quando si progetta un'applicazione con più aree, tenere presente che la latenza di rete tra più aree è superiore rispetto a quella all'interno di una singola area. Se, ad esempio, se si sta eseguendo la replica di un database per abilitare il failover, usare la replica di dati sincrona all'interno di un'area e la replica dei dati asincrona tra aree diverse.

| &nbsp; | Set di disponibilità | Zona di disponibilità | Azure Site Recovery/Area associata |
|--------|------------------|-------------------|---------------|
| Ambito dell'errore | Rack | Data center | Region |
| Routing delle richieste | Load Balancer | Bilanciamento del carico tra zone | Gestione traffico |
| Latenza di rete | Molto bassa | Basso | Medio-alta |
| Rete virtuale  | VNet | VNet | Peering reti virtuali tra aree |

## <a name="implement-resiliency-strategies"></a>Implementare strategie di resilienza

Questa sezione fornisce un sondaggio su alcune strategie di resilienza comuni. La maggior parte di queste sono limitate a una tecnologia particolare. Le descrizioni in questa sezione fanno un riepilogo dell'idea generale alla base di ogni tecnica, con collegamenti ad altri documenti.

**Riprovare in caso di errori temporanei**. Gli errori temporanei possono dipendere da una perdita temporanea della connettività di rete, da una connessione a un database eliminato o da un timeout quando un servizio è occupato. Spesso, un errore temporaneo può essere risolto semplicemente eseguendo nuovamente la richiesta. Per molti servizi di Azure, il client SDK implementa automaticamente nuovi tentativi, in modo trasparente al chiamante. Vedere [Retry service specific guidance][retry-service-specific guidance] (Linee guida specifiche del servizio di ripetizione dei tentativi).

Ogni nuovo tentativo si aggiunge alla latenza totale e, in aggiunta, un numero eccessivo di richieste non riuscite può causare un collo di bottiglia, poiché le richieste in sospeso si accumulano nella coda. Queste richieste bloccate potrebbero tenere in sospeso risorse di sistema critiche, come la memoria, i thread, le connessioni di database e così via, che possono causare errori a catena. Per evitare questa problematica, aumentare il ritardo tra ogni nuovo tentativo e l'altro e limitare il numero totale di richieste non riuscite.

![Diagramma del numero di tentativi](./images/retry.png)

**Bilanciare il carico tra più istanze**. Ai fini della scalabilità, un'applicazione cloud deve essere in grado di scalare orizzontalmente aggiungendo altre istanze. Questo approccio migliora anche la resilienza, poiché le istanze danneggiate possono essere rimosse dalla rotazione. Ad esempio: 

- Inserire due o più macchine virtuali dietro al bilanciamento del carico: quest'ultimo distribuisce il traffico a tutte le macchine virtuali. Vedere [Run load-balanced VMs for scalability and availability][ra-multi-vm] (Eseguire macchine virtuali con carico bilanciato per la scalabilità e la disponibilità).
- Scalare orizzontalmente un'applicazione del servizio app di Azure a più istanze: il servizio app bilancia automaticamente il carico tra le istanze. Vedere [Basic web application][ra-basic-web] (Applicazione Web di base).
- Usare [Gestione traffico][tm] per distribuire il traffico in un set di endpoint.

**Replicare i dati**. La replica dei dati è una strategia generale per la gestione degli errori non temporanei in un archivio dati. Molte tecnologie di archiviazione offrono funzioni di replica predefinite, tra cui Archiviazione di Azure, Database SQL di Azure, Cosmos DB e Apache Cassandra. È importante considerare i percorsi sia di lettura che di scrittura: in base alla tecnologia di archiviazione, potrebbero essere fornite più repliche scrivibili, o una singola replica scrivibile e più repliche di sola lettura.

Per ottimizzare la disponibilità, è possibile posizionare le repliche in più aree. Tuttavia, ciò aumenta la latenza quando si replicano i dati. In genere, la replica tra aree viene eseguita in modo asincrono e ciò implica un modello di coerenza finale e la potenziale perdita di dati se una replica ha esito negativo.

È possibile usare [Azure Site Recovery][site-recovery] per replicare le macchine virtuali di Azure da un'area a un'altra. Site Recovery replica i dati in modo continuo nell'area di destinazione. In caso di interruzione nel sito primario, viene effettuato il failover nella località secondaria.

**Ridurre le prestazioni in modo graduale**. Se un servizio ha esito negativo e non è presente un percorso di failover, l'applicazione potrebbe essere in grado di ridurre le prestazioni gradualmente pur fornendo un'esperienza utente accettabile. Ad esempio: 

- Inserire un elemento di lavoro in una coda, in modo da gestirlo in un secondo momento.
- Restituire un valore stimato.
- Usare i dati memorizzati nella cache locale.
- Far visualizzare all'utente un messaggio di errore. (Questa opzione è migliore dell'applicazione che smette di risponde alle richieste).

**Limitare gli utenti con volumi elevati**. A volte un numero ridotto di utenti crea un carico eccessivo e ciò può avere un impatto anche sugli altri utenti, riducendo la disponibilità complessiva dell'applicazione.

Quando un singolo client esegue un numero eccessivo di richieste, l'applicazione può limitare il client per un determinato periodo di tempo. Durante il periodo di limitazione, infatti, l'applicazione rifiuta alcune o tutte le richieste provenienti da quel client (a seconda della precisa strategia di limitazione delle richieste applicata). La soglia per la limitazione potrebbe dipendere dal livello di servizio del cliente.

La limitazione delle richieste non implica necessariamente che il client debba eseguire azioni malintenzionate, me solo che il client superi la propria quota del servizio. In alcuni casi, un consumer potrebbe superare di molto la quota o, altrimenti, potrebbe comportarsi in modo errato. In tal caso, si potrebbe andare avanti e bloccare l'utente. In genere questo avviene bloccando una chiave API o un intervallo di indirizzi IP. Per altre informazioni, vedere l'articolo [Throttling Pattern][throttling-pattern] (Modello di limitazione).

**Usare un interruttore**. Il [modello a interruttore][circuit-breaker-pattern] può impedire a un'applicazione di tentare ripetutamente un'operazione che probabilmente continuerà a restituire un errore. L'interruttore esegue il wrapping delle chiamate a un servizio e tiene traccia del numero di errori recenti. Se il conteggio degli errori supera una soglia, l'interruttore inizia a restituire un codice di errore senza chiamare il servizio. In questo modo il servizio ha il tempo di ripristinarsi.

**Usare il livellamento del carico per contenere i picchi di traffico**.
Nelle applicazioni possono verificarsi picchi improvvisi di traffico, che possono sovraccaricare i servizi nel back-end. Se un servizio back-end non può rispondere alle richieste in modo sufficientemente rapido, ciò potrebbe comportare che le richieste si mettano in coda (backup) o che il servizio limiti l'applicazione. Per evitare questo problema, è possibile usare una coda come un buffer. Quando è presente un nuovo elemento di lavoro, anziché chiamare subito il servizio back-end, l'applicazione mette in coda un elemento di lavoro per eseguirlo in modo asincrono. La coda si comporta come un buffer che contiene i picchi di carico. Per altre informazioni, vedere [Queue-Based Load Leveling Pattern][load-leveling-pattern] (Modello di livellamento del carico basato sulle code).

**Isolare le risorse critiche**. In alcuni casi gli errori in un sottosistema si possono propagare a catena, causando errori in altre parti dell'applicazione. Questa situazione può verificarsi se un errore porta alcune risorse, come i thread o i socket, a non essere liberate in modo tempestivo, causandone l'esaurimento.

Per evitare questo problema, è possibile partizionare un sistema in gruppi isolati, in modo che un errore in una partizione non causi l'arresto dell'intero sistema. Questo modello talvolta viene denominato Bulkhead pattern (modello a scomparti).

Esempi:

- Partizionare un database (per esempio, in base al tenant) e assegnare a ogni partizione un pool separato di istanze del server Web.  
- Usare pool di thread distinti per isolare le chiamate a servizi diversi: questo consente di evitare errori a catena in caso di errore di uno dei servizi. Per un esempio, vedere [Hystrix library][hystrix] di Netflix.
- Usare i [containers][containers] (visualizzazione a livello di sistema operativo) per limitare le risorse disponibili a un sottosistema specifico.

![Diagramma del modello A scomparti](./images/bulkhead.png)

**Applicare le transazioni di compensazione**. Una [transazione di compensazione][compensating-transaction-pattern] è una transazione che annulla gli effetti di un'altra transazione completata. In un sistema distribuito può essere molto difficile ottenere una consistenza transazionale di alto livello. Le transazioni di compensazione sono un modo per ottenere la consistenza attraverso una serie di transazioni singole di dimensioni inferiori, che possono essere annullate a ogni passaggio.

Per organizzare un viaggio, ad esempio, un cliente potrebbe prenotare un'automobile, una camera di albergo e un volo. Se uno di questi passaggi fallisce, l'intera operazione ha esito negativo. Anziché tentare di usare una singola transazione distribuita per l'intera operazione, è possibile definire una transazione di compensazione per ogni passaggio. Per esempio, per annullare la prenotazione di un'auto, si cancella tale prenotazione. Per completare l'intera operazione, un coordinatore esegue ogni passaggio: se un passaggio ha esito negativo, si applicano le transazioni di compensazione per annullare qualunque passaggio che sia stato completato.

## <a name="test-for-resiliency"></a>Eseguire test per la resilienza

In genere non è possibile testare resilienza nello stesso modo in cui si esegue il test della funzionalità di un'applicazione (eseguendo gli unit test e così via). Infatti, è necessario testare il modo in cui il carico di lavoro end-to-end viene eseguito in condizioni di errore che si verificano solo in modo intermittente.

Il test è un processo iterativo: testare l'applicazione, misurare il risultato, analizzare e indirizzare gli eventuali errori che si verificano e ripetere il processo.

**Test dell'inserimento degli errori**. Testare la resilienza del sistema durante gli errori, o attivando errori reali, oppure stimolandoli. Di seguito sono riportati alcuni scenari di errore comuni da testare:

- Arrestare le istanze di macchina virtuale.
- Arrestare in modo anomalo i processi.
- Far scadere i certificati.
- Cambiare le chiavi di accesso.
- Arrestare il servizio DNS nei controller di dominio.
- Limitare le risorse di sistema disponibili, come la RAM o il numero di thread.
- Smontare i dischi.
- Ridistribuire una VM.

Misurare i tempi di ripristino e verificare che i requisiti aziendali siano soddisfatti. Testare le combinazioni di modalità di errore. Assicurarsi che gli errori non si propaghino a cascata e che vengano gestiti in modo isolato.

Questa è un'altra ragione per cui è importante analizzare i punti di errore possibili durante la fase di progettazione. I risultati dell'analisi devono essere di input per il piano di test.

**Test di carico**. Il test di carico è fondamentale per l'identificazione di errori che si verificano solo in condizioni di carico come, ad esempio, il database back-end sovraccarico o la limitazione delle richieste di servizio. Eseguire il test per il carico di picco, usando dati di produzione oppure dati sintetici che siano più simili possibile ai dati di produzione. L'obiettivo consiste nel verificare il comportamento dell'applicazione in condizioni reali.

**Esercitazioni sul ripristino di emergenza**. Non è sufficiente aver definito un piano di ripristino di emergenza adeguato. È necessario testarlo periodicamente per verificare che funzioni in modo appropriato quando necessario. Per le macchine virtuali di Azure è possibile usare [Azure Site Recovery][site-recovery] per replicare ed [eseguire esercitazioni sul ripristino di emergenza][site-recovery-test-failover] senza influire sulle applicazioni di produzione o sulla replica in corso.

## <a name="deploy-using-reliable-processes"></a>Distribuire con processi affidabili

Una volta che un'applicazione viene distribuita nell'ambiente di produzione, gli aggiornamenti sono una possibile fonte di errori. Nel peggiore dei casi, un aggiornamento non valido può causare tempi di inattività. Per evitare questo problema, il processo di distribuzione deve essere prevedibile e ripetibile. La distribuzione include il provisioning delle risorse di Azure, la distribuzione del codice dell'applicazione e l'applicazione delle impostazioni di configurazione. Un aggiornamento può interessare tutte e tre le fasi oppure un subset.

Il punto essenziale è che le distribuzioni manuali sono inclini all'errore. Perciò, è consigliabile disporre di un processo automatico e idempotente che si possa eseguire su richiesta ed eseguire nuovamente in caso di problemi.

- Usare i modelli di Azure Resource Manager per automatizzare il provisioning delle risorse di Azure.
- Usare [Panoramica della piattaforma DSC di Automazione di Azure][dsc] per configurare le VM.
- Usare un processo di distribuzione automatizzata per il codice dell'applicazione.

Due concetti collegati alla distribuzione resiliente sono l'*infrastruttura come codice* e l'*infrastruttura immutabile*.

- L'**infrastruttura come codice** consiste nell'usare il codice per eseguire il provisioning e configurare l'infrastruttura. L'infrastruttura come codice può usare un approccio dichiarativo o un approccio imperativo (o una combinazione di entrambi). I modelli di Resource Manager sono un esempio di approccio dichiarativo, mentre gli script di PowerShell, invece, sono un esempio di approccio imperativo.
- L'**infrastruttura immutabile** è il principio secondo cui non si dovrebbe modificare l'infrastruttura dopo la distribuzione nell'ambiente di produzione, altrimenti ci si può trovare in uno stato in cui sono state applicate modifiche ad hoc, ed è quindi difficile sapere esattamente cosa sia cambiato e ragionare sul sistema.

Un'altra domanda è come si possa implementare un aggiornamento dell'applicazione. Sono consigliate tecniche come la distribuzione di tipo blu-verde o le versioni canary, che eseguono il push degli aggiornamenti in modalità altamente controllate per ridurre al minimo gli impatti possibili da una distribuzione non valida.

- La [distribuzione di tipo blu-verde][blue-green] è una tecnica in cui un aggiornamento viene distribuito in un ambiente di produzione separato dall'applicazione live. Dopo la convalida della distribuzione, commutare il routing del traffico alla versione aggiornata. Ad esempio, l'App Web del servizio app di Azure consente quest'operazione attraverso gli slot di staging.
- Le [versioni canary][canary-release] sono simili alle distribuzioni di tipo blu-verde. Anziché commutare tutto il traffico alla versione aggiornata, si distribuisce l'aggiornamento a una piccola percentuale di utenti, instradando una parte del traffico alla nuova distribuzione. Se si verificano problemi, interrompere e ripristinare la distribuzione precedente, altrimenti instradare sempre più traffico alla nuova versione, fino a quando non si raggiunge il 100% del traffico.

Qualunque approccio si adotti, nel caso in cui la nuova versione non funzioni, assicurarsi che sia possibile eseguire il rollback all'ultima distribuzione valida conosciuta. In aggiunta, se si verificano errori, i registri applicazioni indicano quale versione ha causato l'errore.

## <a name="monitor-to-detect-failures"></a>Monitorare per rilevare gli errori

Il monitoraggio e la diagnostica sono fondamentali per la resilienza, infatti, se qualcosa non riesce, è necessario sapere che è sorto un problema ed è indispensabile avere informazioni approfondite sulla causa dell'errore.

Il monitoraggio di un sistema distribuito su vasta scala costituisce una sfida significativa. Si pensi a un'applicazione che esegue alcune dozzine di macchine virtuali &mdash; non è pratico accedere a ogni macchina virtuale, una alla volta, ed esaminare il file di log, tentando di risolvere un problema. In aggiunta, il numero di istanze di VM probabilmente non è statico. Le VM, infatti, vengono aggiunte e rimosse con l'aumentare e il ridursi dell'applicazione e, in alcuni casi, un'istanza può avere esito negativo e può essere necessario che venga sottoposta a un nuovo provisioning. Si consideri anche che un'applicazione cloud tipica potrebbe usare più archivi di dati (archiviazione di Azure, database SQL, Cosmos DB, cache redis) e un'azione utente singola può estendersi a più sottosistemi.

Si può pensare al processo di monitoraggio e diagnostica come a una pipeline con varie fasi distinte:

![Contratto di servizio composito](./images/monitoring.png)

- **Strumentazione**. I dati non elaborati per il monitoraggio e la diagnostica provengano da un'ampia gamma di origini, tra cui: i registri applicazioni, i log del server Web, i contatori delle prestazioni del sistema operativo, i registri database e la diagnostica incorporata nella piattaforma Azure. La maggior parte dei servizi di Azure dispongono di funzionalità di diagnostica che è possibile usare per determinare la causa dei problemi.
- **Raccolta e archiviazione**. I dati di strumentazione non elaborati possono essere contenuti in diverse posizioni e con formati diversi (ad esempio, log di traccia delle applicazioni, log di IIS, contatori delle prestazioni). Queste origini diverse sono raccolte, consolidate e inserite in un archivio affidabile.
- **Analisi e diagnosi**. Dopo che vengono consolidati, i dati possono essere analizzati per risolvere i problemi e fornire una visione complessiva dell'integrità dell'applicazione.
- **Visualizzazione e avvisi**. In questa fase i dati di telemetria vengono presentati in modo tale che un operatore possa notare rapidamente i problemi o le tendenze. Un esempio è rappresentato dai dashboard o dagli avvisi di posta elettronica.  

Il monitoraggio è diverso dal rilevamento degli errori: l'applicazione, per esempio, potrebbe rilevare un errore temporaneo e riprovare, non restituendo tempi di inattività. Allo stesso tempo, però, dovrebbe anche registrare l'operazione del nuovo tentativo, in modo che si possa monitorare la frequenza degli errori e avere un quadro generale dello stato di integrità dell'applicazione.

Per questo motivo i registri applicazioni sono una fonte importante di dati di diagnostica. Fra le procedure consigliate per la registrazione delle applicazioni vi sono:

- Eseguire la registrazione in ambiente di produzione, altrimenti si potrebbero perdere informazioni là dove sono più necessarie.
- Registrare gli eventi in base ai limiti del servizio. Includere un ID di correlazione che passi attraverso i limiti di servizio: se una transazione passa attraverso più servizi e uno di questi ha esito negativo, l'ID di correlazione consente di individuare il motivo per cui la transazione non è riuscita.
- Usare la registrazione semantica, conosciuta anche come registrazione strutturata: i log non strutturati rendono difficile automatizzare il consumo e l'analisi dei dati di log, che sono necessari a livello di scalabilità cloud.
- Usare la registrazione asincrona: in caso contrario, il sistema di registrazione può provocare un errore nell'applicazione, generando il backup delle richieste che si bloccano durante l'attesa di scrivere un evento di registrazione.
- La registrazione delle applicazioni è diversa dal controllo: quest'ultimo, infatti, può avvenire per motivi normativi o di conformità. Di conseguenza, i record di controllo devono essere completati e non possono essere eliminati durante l'elaborazione delle transazioni. Se un'applicazione richiede un controllo, questo deve essere mantenuto separato dalla registrazione diagnostica.

Per maggiori informazioni sul monitoraggio e la diagnostica, vedere [Monitoring and diagnostics guidance][monitoring-guidance] (Monitoraggio e diagnostica).

## <a name="respond-to-failures"></a>Rispondere agli errori

Nelle sezioni precedenti sono state trattate le strategie di ripristino automatico, che sono fondamentali per la disponibilità elevata. Tuttavia, a volte, è necessario intervenire manualmente.

- **Avvisi**. Monitorare l'applicazione per quei segnali di avviso che potrebbero richiedere un intervento proattivo. Se viene visualizzato, ad esempio, che il database SQL o Cosmos DB limitano in modo consistente l'applicazione, potrebbe essere necessario aumentare la capacità del database o ottimizzare le query. In questo esempio, anche se l'applicazione può gestire gli errori di limitazione delle richieste in modo trasparente, i dati di telemetria dovrebbero comunque generare un avviso in modo che si possa procedere nel controllo.  
- **Failover manuale**. Alcuni sistemi non possono effettuare il failover automaticamente e richiedono un failover manuale. Per le macchine virtuali di Azure configurate con [Azure Site Recovery][site-recovery], è possibile [eseguire il failover][site-recovery-failover] e ripristinare le macchine virtuali in un'altra area in pochi minuti.
- **Test della conformità operativa**. Se l'applicazione effettua il failover in un'area secondaria, è consigliabile eseguire un test della conformità operativa prima di eseguire il failback nell'area primaria. Il test dovrebbe verificare che l'area primaria sia integra e pronta a ricevere nuovamente traffico.
- **Controllo della coerenza dei dati**. Nel caso di un errore in un archivio dati, è possibile che ci siano incoerenze dei dati quando l'archivio diventa nuovamente disponibile, soprattutto se i dati sono stati replicati.
- **Ripristino da backup**. Per esempio, il database SQL, nel caso in cui si verifichi un'interruzione di area, può essere ripristinato a livello geografico dal backup più recente.

È consigliabile eseguire le operazioni seguenti: documentare e testare il piano di ripristino di emergenza, valutare l'impatto aziendale degli errori delle applicazioni, automatizzare il processo il più possibile e documentare eventuali passaggi manuali, come ad esempio il failover manuale o il ripristino dei dati dai backup, testare regolarmente, infine, il processo di ripristino di emergenza per convalidare e migliorare il piano.

## <a name="summary"></a>Summary

Questo articolo si è focalizzato sulla resilienza da una prospettiva olistica, enfatizzando alcune delle problematiche del cloud. Fra queste sono inclusi la natura distribuita del cloud computing, l'uso di hardware appositi e la presenza di errori di rete temporanei.

Di seguito sono riportati alcuni punti chiave illustrati nell'articolo:

- La resilienza comporta una maggiore disponibilità e un minore tempo medio per il ripristino dagli errori.
- La resilienza nel cloud richiede un set di tecniche diverso dalle soluzioni locali tradizionali.
- La resilienza non avviene per errore: deve essere progettata e integrata dall'inizio.
- La resilienza coinvolge ogni aspetto del ciclo di vita di un'applicazione: dalla pianificazione e la codifica alle operazioni.
- Testa e monitora!

<!-- links -->

[blue-green]: https://martinfowler.com/bliki/BlueGreenDeployment.html
[canary-release]: https://martinfowler.com/bliki/CanaryRelease.html
[circuit-breaker-pattern]: https://msdn.microsoft.com/library/dn589784.aspx
[compensating-transaction-pattern]: https://msdn.microsoft.com/library/dn589804.aspx
[containers]: https://en.wikipedia.org/wiki/Operating-system-level_virtualization
[dsc]: /azure/automation/automation-dsc-overview
[contingency-planning-guide]: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-34r1.pdf
[fma]: failure-mode-analysis.md
[hystrix]: https://medium.com/netflix-techblog/introducing-hystrix-for-resilience-engineering-13531c1ab362
[jmeter]: https://jmeter.apache.org/
[load-leveling-pattern]: ../patterns/queue-based-load-leveling.md
[monitoring-guidance]: ../best-practices/monitoring.md
[ra-basic-web]: ../reference-architectures/app-service-web-app/basic-web-app.md
[ra-multi-vm]: ../reference-architectures/virtual-machines-windows/multi-vm.md
[checklist]: ../checklist/resiliency.md
[retry-pattern]: ../patterns/retry.md
[retry-service-specific guidance]: ../best-practices/retry-service-specific.md
[sla]: https://azure.microsoft.com/support/legal/sla/
[throttling-pattern]: ../patterns/throttling.md
[tm]: https://azure.microsoft.com/services/traffic-manager/
[tm-failover]: /azure/traffic-manager/traffic-manager-monitoring
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager
[site-recovery]:/azure/site-recovery/azure-to-azure-quickstart/
[site-recovery-test-failover]:/azure/site-recovery/azure-to-azure-tutorial-dr-drill/
[site-recovery-failover]:/azure/site-recovery/azure-to-azure-tutorial-failover-failback/
