---
title: 'Data center virtuale di Azure: una prospettiva di rete'
description: Informazioni su come creare il data center virtuale in Azure
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.date: 09/24/2018
ms.author: jonor
ms.openlocfilehash: b10930ac6d6458872d8b626825d21bd0bed2b807
ms.sourcegitcommit: 16bc6a91b6b9565ca3bcc72d6eb27c2c4ae935e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52550462"
---
# <a name="azure-virtual-datacenter-a-network-perspective"></a>Data center virtuale di Azure: una prospettiva di rete

## <a name="overview"></a>Panoramica
La migrazione delle applicazioni locali ad Azure, anche senza modifiche significative, offre alle organizzazioni il vantaggio di un'infrastruttura protetta e conveniente. Questo approccio è noto come **lift-and-shift**. Per trarre il meglio dall'agilità del cloud computing, le aziende devono sviluppare la propria architettura per poter sfruttare le funzionalità di Azure. Microsoft Azure offre servizi e infrastruttura su scala elevata, capacità e affidabilità di livello aziendale e molte opzioni per la connettività ibrida. 

I clienti possono scegliere di accedere a questi servizi cloud tramite Internet o con Azure ExpressRoute, che offre connettività di rete privata. La piattaforma Microsoft Azure consente ai clienti di estendere con facilità la propria infrastruttura nel cloud e di sviluppare architetture a più livelli. I partner Microsoft mettono inoltre a disposizione funzionalità avanzate offrendo servizi di sicurezza e appliance virtuali ottimizzati per Azure.

Questo articolo offre una panoramica di modelli e progettazioni che consentono di risolvere le problematiche di sicurezza, prestazioni e scalabilità a livello di architettura che devono essere affrontate da molti clienti che stanno considerando il passaggio al cloud in massa. Viene anche illustrato come adattare ruoli IT aziendali diversi alla gestione e alla governance del sistema, con particolare attenzione ai requisiti di sicurezza e all'ottimizzazione dei costi.

## <a name="what-is-a-virtual-datacenter"></a>Che cos'è un data center virtuale?
Le prime soluzioni cloud erano progettate per ospitare singole applicazioni relativamente isolate in ambito pubblico. Questo approccio è andato bene per alcuni anni, ma, man mano che i vantaggi delle soluzioni cloud si facevano evidenti e sempre più carichi di lavoro su vasta scala venivano ospitati nel cloud, diventava indispensabile risolvere i problemi di sicurezza, affidabilità, prestazioni e costi derivanti dalle distribuzioni in una o più aree, per tutto il ciclo di vita del servizio cloud.

Il diagramma distribuzione cloud seguente illustra un esempio di lacune nella sicurezza nella **casella rossa** e di opportunità di ottimizzazione delle appliance virtuali di rete nei carichi di lavoro nella **casella gialla**.

[![0]][0]

Il data center virtuale è nato da questa necessità di ridimensionamento per poter supportare carichi di lavoro aziendali e dall'esigenza di gestire i problemi derivanti dal supporto di applicazioni su larga scala nel cloud pubblico.

Un data center virtuale non comprende solo i carichi di lavoro delle applicazioni nel cloud, ma anche la rete, la sicurezza, la gestione e l'infrastruttura (ad esempio, DNS e servizi directory). In genere offre una connessione privata a una rete o data center locale. Poiché sempre più carichi di lavoro passano ad Azure, è importante considerare l'infrastruttura di supporto e gli oggetti in cui questi carichi di lavoro vengono inseriti. Un'attenta valutazione di come le risorse sono strutturate può evitare il proliferare di centinaia di **isole di carichi di lavoro** che devono essere gestite separatamente, ognuna con i propri flussi di dati, modelli di sicurezza e problematiche di conformità.

Un data center virtuale è una raccolta di entità separate, ma correlate, con funzioni, funzionalità e infrastruttura di supporto comuni. Visualizzando i carichi di lavoro come data center virtuale integrato, è possibile ridurre i costi grazie alle economie di scala, ottimizzare la sicurezza tramite componenti e flussi di dati centralizzati e semplificare operazioni, gestione e controlli della conformità.

> [!NOTE]  
> È importante comprendere che il data center virtuale **non** è un prodotto di Azure separato, ma la combinazione di diverse funzionalità e caratteristiche che permette di soddisfare requisiti specifici. Il data center virtuale è un modo di concepire i carichi di lavoro e l'utilizzo di Azure per ottimizzare le risorse e le funzionalità del cloud. Il data center virtuale è quindi un approccio modulare alla creazione di servizi IT in Azure, nel rispetto dei ruoli e delle responsabilità aziendali.

Il data center virtuale consente alle aziende di spostare carichi di lavoro e applicazioni in Azure per gli scenari seguenti:

-   Hosting di più carichi di lavoro correlati
-   Migrazione dei carichi di lavoro da un ambiente locale ad Azure
-   Implementazione di requisiti di sicurezza e accesso condivisi o centralizzati nei carichi di lavoro
-   Combinazione di Azure DevOps e IT centralizzato in modo appropriato per un'organizzazione di grandi dimensioni

La chiave per usufruire dei vantaggi del data center virtuale è una topologia centralizzata (hub-spoke) con una combinazione di funzionalità di Azure: 

- [Rete virtuale di Azure][VNet] 
- [Gruppi di sicurezza di rete (NSG)][NSG]
- [Peering di rete virtuale][VNetPeering] 
- [Route definite dall'utente][UDR]
- Servizi di gestione delle identità di Azure con il [controllo degli accessi in base al ruolo][RBAC] 
- Facoltativamente, [Firewall di Azure][AzFW], [DNS di Azure][DNS], [servizio Frontdoor di Azure][AFD] e [rete WAN virtuale di Azure][vWAN].

## <a name="who-should-implement-a-virtual-datacenter"></a>Chi deve implementare un data center virtuale?
I clienti di Azure che hanno la necessità di spostare un certo numero di carichi di lavoro in Azure possono trarre vantaggio dall'uso di risorse comuni. A seconda della grandezza, anche le singole applicazioni possono trarre vantaggio dall'uso dei modelli e dei componenti usati per creare un data center virtuale.

Se l'organizzazione ha un team o un reparto centralizzato per IT, rete, sicurezza o conformità, un data center virtuale facilita l'applicazione di elementi normativi, la separazione dei compiti e l'uniformità dei componenti comuni sottostanti offrendo contemporaneamente ai team delle applicazioni la libertà e il controllo necessari per i propri requisiti.

Le organizzazioni che stanno prendendo in considerazione Azure DevOps, possono utilizzare i concetti relativi al data center virtuale per fornire gruppi autorizzati di risorse di Azure e assicurarsi di avere il controllo totale all'interno di un gruppo. I gruppi sono sottoscrizioni o gruppi di risorse in una sottoscrizione comune. I limiti di rete e di sicurezza rimangono tuttavia conformi a quanto stabilito da un criterio centralizzato in una rete virtuale dell'hub e in un gruppo di risorse.

## <a name="considerations-when-you-implement-a-virtual-datacenter"></a>Considerazioni sull'implementazione di un data center virtuale
Quando si progetta un data center virtuale, è opportuno considerare alcuni punti fondamentali:

-   Identità e servizi directory
-   Infrastruttura di sicurezza
-   Connettività al cloud
-   Connettività all'interno del cloud

### <a name="identity-and-directory-services"></a>Identità e servizi directory
L'identità e i servizi directory sono aspetti chiave di tutti i data center, sia locali che nel cloud. L'identità è correlata a tutti gli aspetti dell'accesso e dell'autorizzazione per i servizi nel data center virtuale. Per fare in modo che solo utenti e processi autorizzati accedano all'account e alle risorse di Azure, Azure usa diversi tipi di credenziali per l'autenticazione, tra cui password (per accedere all'account Azure), chiavi crittografiche, firme digitali e certificati. 

[Azure Multi-Factor Authentication][MFA] è un ulteriore livello di sicurezza per l'accesso ai servizi di Azure. Offre un'autenticazione avanzata con una gamma di opzioni di verifica semplici. I clienti possono scegliere il metodo preferito. Le opzioni includono telefonata, SMS o notifica tramite app per dispositivi mobili. 

Le organizzazioni di grandi dimensioni hanno la necessità di definire un processo di gestione delle identità che descriva la gestione delle singole identità, l'autenticazione, l'autorizzazione, i ruoli e i privilegi all'interno o attraverso il data center virtuale. Gli obiettivi di questo processo sono da un lato l'aumento della sicurezza e della produttività e dall'altro la riduzione dei costi, dei tempi di inattività e delle attività manuali ripetitive.

Le aziende e le organizzazioni possono richiedere una combinazione complessa di servizi per le diverse line-of-business e i dipendenti spesso hanno ruoli diversi a seconda del progetto in cui sono coinvolti. Un data center virtuale richiede una buona cooperazione tra i diversi team, ognuno con definizioni dei ruoli specifiche, in modo che i sistemi vengano eseguiti con una governance efficiente. L'insieme di responsabilità, accesso e diritti può essere complesso. La gestione delle identità nel data center virtuale viene implementata tramite [Azure Active Directory (Azure AD)][AAD] e il controllo degli accessi in base al ruolo.

Un servizio directory è un'infrastruttura di informazioni condivisa per individuare, gestire, amministrare e organizzare quotidianamente elementi e risorse di rete. Queste risorse possono includere volumi, cartelle, file, stampanti, utenti, gruppi, dispositivi e altri oggetti. Ogni risorsa presente nella rete è considerata un oggetto dal server di directory. Le informazioni su una risorsa vengono archiviate come raccolta di attributi associati a tale risorsa o oggetto.

Tutti i servizi aziendali online di Microsoft si basano su Azure Active Directory (Azure AD) per le esigenze di gestione delle identità e degli accessi. Azure Active Directory è una soluzione cloud completa di gestione di identità e accessi ad alta disponibilità che riunisce servizi di directory di base, governance delle identità avanzata e gestione dell'accesso alle applicazioni. Azure AD può essere integrato con Active Directory locale per abilitare Single Sign-On per tutte le applicazioni basate sul cloud e ospitate in locale. Gli attributi utente di Active Directory locale possono essere automaticamente sincronizzati con Azure AD.

Non è necessario un singolo amministratore globale per assegnare tutte le autorizzazioni in un'implementazione di data center virtuale. Ogni specifico reparto (o gruppo di utenti o servizi nel servizio directory) può invece avere le autorizzazioni necessarie per gestire le proprie risorse nel data center virtuale. La struttura delle autorizzazioni deve essere ben bilanciata. Troppe autorizzazioni possono ostacolare le prestazioni, mentre autorizzazioni non sufficienti o approssimative possono aumentare i rischi per la sicurezza. Il controllo degli accessi in base al ruolo di Azure aiuta a risolvere questo problema offrendo la gestione specifica degli accessi per le risorse del data center virtuale.

#### <a name="security-infrastructure"></a>Infrastruttura di sicurezza
L'infrastruttura di sicurezza fa riferimento alla separazione del traffico nel segmento di rete virtuale specifico del data center virtuale e al controllo dei flussi in ingresso e in uscita nel data center virtuale. Azure si basa sull'architettura multi-tenant che impedisce il traffico non autorizzato e accidentale tra le distribuzioni, usando l'isolamento della rete virtuale, gli elenchi di controllo di accesso (ACL), i servizi di bilanciamento del carico, i filtri IP e i criteri del flusso di traffico. Network Address Translation (NAT) separa il traffico di rete interno dal traffico esterno.

L'infrastruttura di Azure alloca le risorse di infrastruttura ai carichi di lavoro dei tenant e gestisce le comunicazioni verso e dalle macchine virtuali (VM). L'hypervisor di Azure impone la separazione di memoria e processi tra le VM e instrada in modo sicuro il traffico di rete ai tenant del sistema operativo guest.

#### <a name="connectivity-to-the-cloud"></a>Connettività al cloud
L'implementazione di data center virtuale richiede connettività con le reti esterne per offrire i servizi a clienti, partner e utenti interni, quindi in genere è necessaria la connettività non solo a Internet, ma anche alle reti e ai data center locali.

I clienti controllano quali servizi sono accessibili a/da Internet mediante [Firewall di Azure][AzFW] o altri tipi di appliance virtuali di rete, i criteri di routing personalizzati mediante le [route definite dall'utente][UDR] e i filtri di rete mediante i [gruppi di sicurezza di rete][NSG]. È consigliabile che tutte le risorse con connessione Internet siano protette anche tramite la [protezione DDOS di Azure Standard][DDOS].

Le organizzazioni spesso hanno la necessità di connettere le implementazioni di data center virtuale a data center o altre risorse locali. La connettività tra Azure e le reti locali è quindi un aspetto fondamentale della progettazione di un'architettura efficace. Le organizzazioni possono creare un'interconnessione tra le implementazioni di data center virtuale e le risorse locali in Azure in due modi diversi: transito sulla rete Internet o connessioni dirette private.

Una [VPN da sito a sito di Azure][VPN] connette una rete locale aziendale alla relativa implementazione di data center virtuale tramite Internet pubblico. Il traffico tramite la connessione VPN viene crittografato usando il tunneling IPSec e IKE. La connessione da sito a sito di Azure è flessibile, veloce da creare e non richiede ulteriore approvvigionamento, perché tutte le connessioni vengono stabilite tramite Internet.

Per un numero elevato di connessioni VPN, la [rete WAN virtuale di Azure][vWAN] è un servizio di rete che offre connettività tra succursali ottimizzata e automatizzata tramite Azure. La rete WAN virtuale consente di connettere e configurare i dispositivi delle succursali per la comunicazione con Azure. Questa connessione può essere eseguita manualmente oppure usando i dispositivi di provider preferiti tramite un partner di rete WAN virtuale. L'uso di dispositivi di provider preferiti consente la facilità d'uso, semplifica le operazioni di connettività e agevola la gestione della configurazione. Il dashboard predefinito della rete WAN di Azure offre informazioni dettagliate immediate per la risoluzione dei problemi che consentono di risparmiare tempo e permette di visualizzare con facilità la connettività da sito a sito su larga scala.

[ExpressRoute][ExR] è un servizio di connettività di Azure che consente connessioni private tra la rete locale aziendale e la relativa implementazione di data center virtuale. Offre un livello di sicurezza superiore, maggiore affidabilità e velocità più elevate (fino a 10 Gbps), oltre a una latenza coerente. ExpressRoute è utile per le implementazioni di data center virtuale perché i clienti di ExpressRoute sfruttano i vantaggi offerti dalle regole di conformità associate alle connessioni private. Con [ExpressRoute Direct][ExRD] è possibile connettersi direttamente ai router Microsoft a 100 Gbps per i clienti con maggiori esigenze di larghezza di banda.

Per distribuire le connessioni ExpressRoute, in genere è necessario un provider di servizi ExpressRoute. I clienti che devono iniziare subito di solito usano prima una VPN da sito a sito per stabilire la connettività tra il data center virtuale e le risorse locali e quindi eseguono la migrazione alla connessione ExpressRoute quando l'interconnessione fisica con il provider di servizi è completa.

#### <a name="connectivity-within-the-cloud"></a>Connettività all'interno del cloud
Le [reti virtuali][VNet] e il [peering di rete virtuale][VNetPeering] sono i servizi di connettività di rete di base in un data center virtuale. Una rete virtuale garantisce un limite naturale di isolamento per le risorse del data center virtuale e il peering di rete virtuale consente la comunicazione tra reti virtuali diverse nella stessa area di Azure o anche in diverse aree. Il controllo del traffico in una rete virtuale e tra reti virtuali deve rispettare un set di regole di sicurezza specificate tramite elenchi di controllo di accesso. Vedere le informazioni sui [gruppi di sicurezza di rete][NSG], sulle [appliance virtuali di rete][NVA] e sulle [tabelle di routing personalizzate per le route definite dall'utente][UDR].

## <a name="virtual-datacenter-overview"></a>Panoramica del data center virtuale

### <a name="topology"></a>Topologia
Il modello **hub-spoke** consente di estendere il data center virtuale in una singola area di Azure.

[![1]][1]

L'hub è la zona della rete centrale che controlla e ispeziona il traffico in ingresso o in uscita tra zone diverse: Internet, locale e gli spoke. La topologia hub-spoke consente al reparto IT di applicare in modo efficace i criteri di sicurezza in una posizione centrale, riducendo al contempo il rischio di configurazione non corretta ed esposizione.

L'hub contiene i componenti dei servizi comuni utilizzati dagli spoke. Di seguito sono riportati alcuni esempi di servizi centrali comuni:

-   Infrastruttura di Windows Active Directory necessaria per l'autenticazione utente delle terze parti che accedono da reti non attendibili prima di ottenere l'accesso ai carichi di lavoro nello spoke. Include il componente correlato Active Directory Federation Services (AD FS).
-   Servizio DNS per risolvere la denominazione per il carico di lavoro negli spoke, per accedere alle risorse in locale e in Internet se non viene usato [DNS di Azure][DNS].
-   Infrastruttura a chiave pubblica (PKI) per implementare Single Sign-On nei carichi di lavoro.
-   Controllo di flusso del traffico TCP e UDP tra le zone della rete degli spoke e Internet.
-   Controllo di flusso tra gli spoke e le zone locali.
-   Se necessario, controllo di flusso tra uno spoke e un altro.

Il data center virtuale riduce il costo complessivo usando l'infrastruttura condivisa dell'hub.

Il ruolo di ogni spoke può essere quello di ospitare tipi diversi di carichi di lavoro. Gli spoke consentono anche un approccio modulare per le distribuzioni ripetibili degli stessi carichi di lavoro. Ad esempio, sviluppo e test, test di accettazione utente, pre-produzione e produzione. Gli spoke possono anche essere usati per isolare e consentire gruppi diversi all'interno dell'organizzazione, ad esempio gruppi Azure DevOps. In uno spoke è possibile distribuire un carico di lavoro di base o carichi di lavoro complessi multilivello con il controllo del traffico tra i livelli.

#### <a name="subscription-limits-and-multiple-hubs"></a>Limiti della sottoscrizione e hub multipli
In Azure ogni componente, indipendentemente dal tipo, viene distribuito in una sottoscrizione di Azure. L'isolamento dei componenti di Azure in diverse sottoscrizioni di Azure può soddisfare i requisiti di diverse line-of-business, come la configurazione di livelli differenziati di accesso e autorizzazione.

Una singola implementazione di data center virtuale è ridimensionabile fino a un numero elevato di spoke, anche se, come con ogni sistema IT, esistono limiti per le piattaforme. La distribuzione dell'hub è associata a una specifica sottoscrizione di Azure, che ha restrizioni e limiti, ad esempio un numero massimo di peering di rete virtuale. Per informazioni dettagliate, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi][Limits]. Nei casi in cui i limiti possono costituire un problema, è possibile aumentare ulteriormente le prestazioni per l'architettura estendendo il modello da un singolo hub-spoke a un cluster di hub e spoke. Più hub in una o più aree di Azure possono essere interconnessi usando il peering di rete virtuale, ExpressRoute, la rete WAN virtuale o una VPN da sito a sito.

[![2]][2]

L'introduzione di più hub aumenta il costo e l'impegno di gestione del sistema ed è giustificabile solo con la scalabilità come limiti del sistema o ridondanza e con la replica a livello di area come prestazioni dell'utente finale o ripristino di emergenza. Negli scenari in cui sono necessari più hub, tutti gli hub devono cercare di offrire lo stesso set di servizi per facilitare le operazioni.

#### <a name="interconnection-between-spokes"></a>Interconnessione tra spoke
In un singolo spoke è possibile implementare carichi di lavoro complessi multilivello. Le configurazioni multilivello possono essere implementate usando una subnet per ogni livello nella stessa rete virtuale e filtrando i flussi con i gruppi di sicurezza di rete.

Un architetto potrebbe voler distribuire un carico di lavoro multilivello in più reti virtuali. Usando il peering di rete virtuale, gli spoke possono connettersi ad altri spoke nello stesso hub o in hub diversi. Un esempio tipico di questo scenario è quello in cui i server di elaborazione delle applicazioni sono in uno spoke o rete virtuale, mentre il database viene distribuito in un altro spoke o rete virtuale. In questo caso, è facile interconnettere gli spoke con il peering di rete virtuale ed evitare in tal modo il transito dall'hub. È consigliabile eseguire un'attenta revisione dell'architettura e della sicurezza per assicurarsi che il bypass dell'hub non comporti il bypass di importanti punti di sicurezza e di controllo che potrebbero esistere solo nell'hub.

[![3]][3]

Gli spoke possono anche essere interconnessi a uno spoke che funge da hub. Questo approccio crea una gerarchia a due livelli: lo spoke nel livello superiore (livello 0) diventa l'hub degli spoke inferiori (livello 1) nella gerarchia. Gli spoke di un'implementazione di data center virtuale devono inoltrare il traffico all'hub centrale per raggiungere la rete locale o Internet. Un'architettura con due livelli di hub crea un routing complesso che elimina i vantaggi di una relazione hub-spoke semplice.

Anche se Azure consente topologie complesse, uno dei principi fondamentali del data center virtuale è il concetto di ripetibilità e semplicità. Per ridurre al minimo l'impegno di gestione, la progettazione hub-spoke semplice è l'architettura di riferimento consigliata per il data center virtuale.

### <a name="components"></a>Componenti
Un'implementazione di data center virtuale è costituita da quattro tipi di componenti di base: **infrastruttura**, **reti perimetrali**, **carichi di lavoro** e **monitoraggio**.

Ogni tipo di componente è costituito da diverse funzionalità e risorse di Azure. L'implementazione di data center virtuale aziendale è costituita da istanze di più tipi di componenti e di più varianti dello stesso tipo di componente. È possibile, ad esempio, avere più istanze di carico di lavoro diverse separate in modo logico che rappresentano applicazioni diverse. Questi diversi tipi di componenti e istanze vengono usati per creare il data center virtuale.

[![4]][4]

L'architettura generale precedente di un'implementazione di data center virtuale illustra tipi di componenti diversi usati in zone diverse della topologia hub-spoke. Il diagramma illustra i componenti dell'infrastruttura in svariate parti dell'architettura.

Per un data center locale o un'implementazione di data center virtuale è consigliabile che i diritti e i privilegi di accesso siano basati sui gruppi. Gestire gruppi, invece di singoli utenti, consente di assicurare la coerenza dei criteri di accesso tra i team e di ridurre al minimo gli errori di configurazione. L'assegnazione e la rimozione di utenti nei gruppi appropriati consente di mantenere aggiornati i privilegi di un utente specifico.

Ogni gruppo di ruoli deve avere un prefisso univoco nel nome per identificare più facilmente il gruppo associato a un determinato carico di lavoro. Un carico di lavoro che ospita un servizio di autenticazione, ad esempio, potrebbe avere gruppi denominati **AuthServiceNetOps**, **AuthServiceSecOps**, **AuthServiceDevOps** e **AuthServiceInfraOps**. I ruoli centralizzati o i ruoli non correlati a un servizio specifico potrebbero essere preceduti da **Corp**, ad esempio **CorpNetOps**.

Molte organizzazioni usano una variante dei gruppi seguenti per una suddivisione primaria dei ruoli:

-   Il gruppo IT centrale **Corp** ha i diritti di proprietà per controllare i componenti dell'infrastruttura (ad esempio, rete e sicurezza) e quindi deve avere il ruolo Collaboratore nella sottoscrizione (e avere il controllo dell'hub) e i diritti di Collaboratore Rete negli spoke. Le grandi organizzazioni spesso suddividono queste responsabilità di gestione tra più team, ad esempio un gruppo per le operazioni di rete **CorpNetOps**, dedicato esclusivamente alle rete, e un gruppo per le operazioni di sicurezza **CorpSecOps**, responsabile del firewall e dei criteri di sicurezza. In questo caso specifico è necessario creare due gruppi diversi per l'assegnazione di questi ruoli personalizzati.
-   Il gruppo dedicato a sviluppo e test **AppDevOps** ha la responsabilità di distribuire i carichi di lavoro di app o servizi. Questo gruppo ha il ruolo Collaboratore Macchina virtuale per le distribuzioni IaaS oppure uno o più ruoli del collaboratore PaaS. Vedere [Ruoli predefiniti per le risorse di Azure][Roles]. Per il team sviluppo e test potrebbe essere necessario avere visibilità sui criteri di sicurezza (gruppi di sicurezza di rete) e sui criteri di routing (route definite dall'utente) all'hub o in uno spoke specifico. Oltre ai ruoli di collaboratore per i carichi di lavoro, questo gruppo avrà anche bisogno del ruolo di lettore di rete.
-   Il gruppo dedicato a operazioni e manutenzione **CorpInfraOps** o **AppInfraOps** ha la responsabilità di gestire i carichi di lavoro in produzione. Questo gruppo deve essere un collaboratore della sottoscrizione per i carichi di lavoro in qualsiasi sottoscrizione di produzione. Alcune organizzazioni potrebbero anche valutare la necessità di un gruppo aggiuntivo come team di supporto per l'escalation con il ruolo di collaboratore della sottoscrizione nella produzione e nella sottoscrizione dell'hub centrale, per risolvere potenziali problemi di configurazione nell'ambiente di produzione.

Un data center virtuale è strutturato in modo che i gruppi creati per i gruppi IT centrali che gestiscono l'hub abbiano gruppi corrispondenti a livello di carico di lavoro. Oltre a gestire le risorse dell'hub, i soli gruppi IT centrali potranno controllare l'accesso esterno e le autorizzazioni di primo livello per la sottoscrizione. I gruppi di carico di lavoro potranno tuttavia controllare le risorse e le autorizzazioni della rete virtuale indipendentemente dall'IT centrale.

Il data center virtuale deve essere partizionato per ospitare in modo sicuro più progetti in line-of-business diverse. Tutti i progetti richiedono ambienti isolati diversi, ad esempio sviluppo, test di accettazione utente, produzione. Sottoscrizioni di Azure separate per ognuno di questi ambienti offrono un naturale isolamento.

[![5]][5]

Il diagramma precedente illustra la relazione tra i progetti, gli utenti, i gruppi di un'organizzazione e gli ambienti in cui vengono distribuiti i componenti di Azure.

Nell'ambito IT un ambiente o livello è in genere un sistema in cui vengono distribuite ed eseguite più applicazioni. Le grandi imprese usano un ambiente di sviluppo per apportare e testare le modifiche e un ambiente di produzione usato dall'utente finale. Tali ambienti sono separati, spesso da diversi ambienti di staging, per consentire la distribuzione a fasi (implementazione), il test e il ripristino dello stato precedente in caso di problemi. Le architetture di distribuzione variano in modo significativo, ma in genere è ancora seguito il processo di base, che prevede l'avvio con lo sviluppo **(DEV)** e la fine con la produzione **(PROD)**.

Un'architettura comune per questi tipi di ambienti multilivello è costituita dagli ambienti Azure DevOps (sviluppo e test), test di accettazione utente (staging) e produzione. Le organizzazioni possono sfruttare uno o più tenant di Azure AD per definire l'accesso e i diritti per questi ambienti. Il diagramma precedente illustra un caso in cui vengono usati due diversi tenant di Azure AD: uno per Azure DevOps e il test di accettazione utente e l'altro esclusivamente per la produzione.

La presenza di tenant di Azure AD diversi impone la separazione tra gli ambienti. Lo stesso gruppo di utenti (ad esempio, l'IT centrale) deve eseguire l'autenticazione con un URI diverso per accedere a un tenant di Azure AD diverso e modificare i ruoli o le autorizzazioni degli ambienti Azure DevOps o di produzione di un progetto. La presenza di autenticazioni utente diverse per accedere ad ambienti diversi riduce le possibili interruzioni e gli altri problemi causati dagli errori umani.

#### <a name="component-type-infrastructure"></a>Tipo di componente: infrastruttura
Questo tipo di componente è quello in cui si trova la maggior parte dell'infrastruttura di supporto e a cui i team di IT centralizzato, sicurezza e conformità dedicano la maggior parte del tempo.

[![6]][6]

I componenti dell'infrastruttura forniscono un'interconnessione tra i diversi componenti del data center virtuale e sono presenti sia nell'hub che negli spoke. La responsabilità della gestione e della manutenzione dei componenti dell'infrastruttura è in genere affidata al team dell'IT centrale o della sicurezza.

Una delle attività principali del team dell'infrastruttura IT è di garantire la coerenza degli schemi degli indirizzi IP in tutta l'organizzazione. Lo spazio indirizzi IP privato assegnato al data center virtuale deve essere coerente e non sovrapporsi agli indirizzi IP privati assegnati alle reti locali.

Anche se NAT nei router perimetrali locali o negli ambienti di Azure può evitare i conflitti di indirizzi IP, crea complicazioni nei componenti dell'infrastruttura. La semplicità di gestione è uno degli obiettivi chiave del data center virtuale, quindi l'uso di NAT per gestire le problematiche IP non è consigliato.

I componenti dell'infrastruttura contengono le funzionalità seguenti:

-   [**Identità e servizi directory**][AAD]. L'accesso a ogni tipo di risorsa in Azure è controllato da un'identità archiviata in un servizio directory. Il servizio directory archivia l'elenco di utenti e i diritti di accesso alle risorse in una sottoscrizione di Azure specifica. Questi servizi possono esistere solo nel cloud o essere sincronizzati con l'identità locale archiviata in Azure Active Directory.
-   [**Rete virtuale**][VPN]. Le reti virtuali sono uno dei componenti principali del data center virtuale e consentono di creare un limite di isolamento del traffico nella piattaforma Azure. Una rete virtuale è costituita da uno o più segmenti di rete virtuale, ognuno con un prefisso di rete IP specifico (una subnet). La rete virtuale definisce un'area perimetrale interna in cui le macchine virtuali IaaS e i servizi PaaS possono stabilire comunicazioni private. Le macchine virtuali e i servizi PaaS in una rete virtuale non possono comunicare direttamente con le macchine virtuali e i servizi PaaS in una rete virtuale diversa, anche se entrambe le reti virtuali vengono create dallo stesso cliente e nella stessa sottoscrizione. L'isolamento è una proprietà essenziale che assicura che le macchine virtuali e le comunicazioni dei clienti rimangano private entro una rete virtuale.
-   [**Route definite dall'utente**][UDR]. Per impostazione predefinita, il traffico in una rete virtuale viene instradato in base alla tabella di routing di sistema. Una route definita dall'utente è una tabella di routing personalizzata che gli amministratori di rete possono associare a una o più subnet per sovrascrivere il comportamento della tabella di routing di sistema e definire un percorso di comunicazione in una rete virtuale. La presenza di route definite dall'utente garantisce che il traffico in uscita dallo spoke transiti da macchine virtuali personalizzate o appliance virtuali di rete e dai servizi di bilanciamento del carico presenti nell'hub e negli spoke.
-   [**Gruppo di sicurezza di rete**][NSG]. Un gruppo di sicurezza di rete è un elenco di regole di sicurezza che funge da filtro del traffico in origini IP, destinazione IP, protocolli, porte di origine IP e porte di destinazione IP. Il gruppo di sicurezza di rete può essere applicato a una subnet, a una scheda di interfaccia di rete virtuale associata una macchina virtuale di Azure o a entrambe. I gruppi di sicurezza di rete sono essenziali per implementare un controllo di flusso corretto nell'hub e negli spoke. Il livello di sicurezza offerto dal gruppo di sicurezza di rete dipende da quali porte si aprono e per quale scopo. I clienti devono applicare filtri aggiuntivi per ogni macchina virtuale con firewall basati su host, ad esempio IPtables o Windows Firewall.
-   [**DNS**][DNS]. La risoluzione dei nomi delle risorse nelle reti virtuali di un data center virtuale viene fornita tramite DNS. Azure offre servizi DNS per la risoluzione dei nomi sia [pubblica][DNS] che [privata][PrivateDNS]. Le zone private consentono la risoluzione dei nomi sia all'interno di una rete virtuale che tra diverse reti virtuali. È possibile avere zone private che si estendono su più reti virtuali nella stessa area e anche in diverse aree e sottoscrizioni. Per la risoluzione pubblica, DNS di Azure offre un servizio di hosting per i domini DNS, che esegue la risoluzione dei nomi tramite l'infrastruttura di Microsoft Azure. L'hosting dei domini in Azure consente di gestire i record DNS usando gli stessi strumenti, credenziali, API e fatturazione usati per altri servizi Azure.
-   [**Sottoscrizione**][SubMgmt] e [**gestione dei gruppi di risorse**][RGMgmt]. Una sottoscrizione definisce un limite naturale per creare più gruppi di risorse in Azure. Le risorse in una sottoscrizione vengono assemblate in contenitori logici denominati **gruppi di risorse**. Il gruppo di risorse rappresenta un gruppo logico per organizzare le risorse di un data center virtuale.
-   [**Controllo degli accessi in base al ruolo**][RBAC]. Tramite il controllo degli accessi in base al ruolo, è possibile eseguire il mapping dei ruoli aziendali ai diritti di accesso a risorse di Azure specifiche e consentire agli utenti solo un determinato subset di azioni. Con il controllo degli accessi in base al ruolo, è possibile concedere l'accesso assegnando i ruoli appropriati a utenti, gruppi e applicazioni nell'ambito pertinente. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione di Azure, un gruppo di risorse o una singola risorsa. Il controllo degli accessi in base al ruolo consente l'ereditarietà delle autorizzazioni. Un ruolo assegnato a un ambito padre concede anche l'accesso agli elementi figlio contenuti al suo interno. Usando il controllo degli accessi in base al ruolo, è possibile separare i compiti e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro. Ad esempio, usare il controllo degli accessi in base al ruolo per consentire a un dipendente di gestire le macchine virtuali in una sottoscrizione, mentre un altro utente può gestire i database SQL della stessa sottoscrizione.
-   [**Peering di rete virtuale**][VNetPeering]. La funzionalità fondamentale usata per creare l'infrastruttura di un data center virtuale è il peering di rete virtuale, un meccanismo che connette due reti virtuali nella stessa area tramite la rete di data center di Azure o usando il backbone di Azure a livello mondiale tra diverse aree.

#### <a name="component-type-perimeter-networks"></a>Tipo di componente: reti perimetrali
I componenti di tipo [rete perimetrale][DMZ] consentono di fornire la connettività di rete alle reti dei data center locali o fisici, oltre alla connettività a e da Internet. Sono anche i componenti a cui probabilmente i team responsabili della rete e della sicurezza dedicano più tempo.

I pacchetti in ingresso devono attraversare le appliance di sicurezza nell'hub prima di raggiungere i server back-end negli spoke. Esempi sono il firewall, IDS e IPS. Prima di lasciare la rete, anche i pacchetti diretti a Internet dai carichi di lavoro devono attraversare le appliance di sicurezza nella rete perimetrale. Le finalità di questa operazione sono l'applicazione dei criteri, l'ispezione e il controllo.

I componenti di tipo rete perimetrale offrono le funzionalità seguenti:

-   [Reti virtuali][VNet], [route definite dall'utente][UDR] e [gruppi di sicurezza di rete][NSG]
-   [Appliance virtuali di rete][NVA]
-   [Azure Load Balancer][ALB]
-   [Gateway applicazione di Azure][AppGW] e [web application firewall (WAF)][WAF]
-   [IP pubblici][PIP]
-   [Frontdoor di Azure][AFD]
-   [Firewall di Azure][AzFW]

I team dedicati all'IT centrale e alla sicurezza hanno in genere la responsabilità della definizione dei requisiti e delle operazioni delle reti perimetrali.

[![7]][7]

Il diagramma precedente illustra l'imposizione di due perimetri con accesso a Internet e alla rete locale, entrambi residenti negli hub della rete perimetrale e della rete WAN virtuale. Nell'hub della rete perimetrale la rete perimetrale verso Internet può aumentare le prestazioni per supportare un numero elevato di applicazioni line-of-business, usando più farm di web application firewall (WAF) o di istanze di Firewall di Azure. Nell'hub della rete WAN virtuale si ottiene una connettività altamente scalabile da ramo a ramo e da ramo ad Azure tramite la VPN o ExpressRoute a seconda delle esigenze.

[**Reti virtuali**][VNet]. L'hub si basa in genere su una rete virtuale con più subnet per ospitare i diversi tipi di servizi che filtrano ed esaminano il traffico verso o da Internet tramite appliance virtuali di rete, WAF e istanze del gateway applicazione di Azure.

[**Route definite dall'utente**][UDR]. Con le route definite dall'utente, i clienti possono distribuire firewall, IDS o IPS e altre appliance virtuali nonché instradare il traffico di rete attraverso queste appliance di sicurezza per l'applicazione dei criteri relativi ai limiti di sicurezza, il controllo e l'ispezione. Le route definite dall'utente possono essere create sia nell'hub che negli spoke per garantire che il traffico transiti da specifiche macchine virtuali personalizzate, appliance virtuali di rete e servizi di bilanciamento del carico usati dal data center virtuale. Per garantire che il traffico generato dalle macchine virtuali residenti nello spoke passi alle appliance virtuali corrette, configurare una route definita dall'utente nelle subnet dello spoke impostando l'indirizzo IP front-end del servizio di bilanciamento del carico interno come hop successivo. Il servizio di bilanciamento del carico interno distribuisce il traffico interno alle appliance virtuali o al pool back-end di bilanciamento del carico.

[**Firewall di Azure**][AzFW] è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali. Firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale consentendo ai firewall esterni di identificare il traffico proveniente dalla rete virtuale. Il servizio è completamente integrato con Monitoraggio di Azure per la registrazione e l'analisi.

[**Appliance virtuali di rete**][NVA]. Nell'hub la rete perimetrale con accesso a Internet in genere viene gestita tramite un'istanza di Firewall di Azure oppure una farm di firewall o web application firewall (WAF).

Line-of-business diverse usano in genere numerose applicazioni Web che tendono a essere soggette a svariate vulnerabilità e potenziali exploit. I web application firewall sono uno speciale tipo di prodotto usato per rilevare gli attacchi contro le applicazioni Web (HTTP/HTTPS) in modo più approfondito di quanto farebbe un firewall generico. Rispetto alla tradizionale tecnologia firewall, i Web application firewall hanno un set di funzionalità specifiche per proteggere i server Web interni dalle minacce.

Tuttavia, è necessario che una sola implementazione di data center virtuale sia ospitata all'interno di una singola area perché i requisiti della sottoscrizione non consentono lo spanning delle aree. Questo requisito rende una singola implementazione di data center virtuale vulnerabile alle interruzioni a livello di area.

Un'istanza di Firewall di Azure o una farm di firewall delle appliance virtuali di rete usano un piano di amministrazione comune, con un set di regole di sicurezza per proteggere i carichi di lavoro ospitati negli spoke e controllare l'accesso alle reti locali. Firewall di Azure ha incorporata la scalabilità, mentre i firewall delle appliance virtuali di rete possono essere ridimensionati manualmente dietro un servizio di bilanciamento del carico. In genere, una farm di firewall ha un software meno specializzato rispetto a un WAF, ma ha un ambito dell'applicazione più ampio per filtrare ed esaminare ogni tipo di traffico in uscita e in ingresso. Se viene usato un approccio basato sulle appliance virtuali di rete, queste ultime sono disponibili e possono essere distribuite da Azure Marketplace.

È consigliabile usare un set di istanze di Firewall di Azure (o di appliance virtuali di rete) per il traffico che ha origine in Internet e un altro per il traffico che ha origine in locale. L'uso di un solo set di firewall per entrambi i tipi di traffico è un rischio per la sicurezza, perché non viene creato un perimetro di sicurezza tra i due set di traffico. L'uso di livelli di firewall separati riduce la complessità del controllo delle regole di sicurezza e indica chiaramente la corrispondenza tra le regole e le richieste di rete in ingresso.

La maggior parte delle grandi imprese gestisce più domini. Il servizio [DNS di Azure][DNS] può essere usato per ospitare i record DNS per un particolare dominio. Ad esempio, l'indirizzo IP virtuale (indirizzo VIP) del servizio di bilanciamento del carico esterno di Azure (o WAF) può essere registrato nel record **A** di un record DNS di Azure. I [**DNS privati**][PrivateDNS] sono disponibili anche per la gestione di spazi di indirizzi privati all'interno delle reti virtuali.

[**Azure Load Balancer**][ALB] offre un servizio a disponibilità elevata di livello 4 (TCP o UDP) che consente di distribuire il traffico in ingresso tra le istanze del servizio definite in un set con carico bilanciato. Il traffico inviato al servizio di bilanciamento del carico dagli endpoint front-end (endpoint IP pubblici o endpoint IP privati) può essere ridistribuito con o senza conversione degli indirizzi in un set di pool di indirizzi IP back-end (ad esempio, appliance virtuali di rete o macchine virtuali).

Azure Load Balancer può anche esaminare l'integrità delle varie istanze del server e, quando un probe non riesce a rispondere, il servizio di bilanciamento del carico smette di inviare il traffico all'istanza non integra. In un data center virtuale è presente ad esempio un servizio di bilanciamento del carico esterno nell'hub per bilanciare il traffico alle appliance virtuali di rete e negli spoke per eseguire attività come il bilanciamento del traffico tra le diverse macchine virtuali di un'applicazione multilivello.

Il [**servizio Frontdoor di Azure**][AFD] è un servizio Microsoft a scalabilità e disponibilità elevata di piattaforma di accelerazione dell'applicazione Web, bilanciamento del carico HTTP globale, protezione dell'applicazione e rete per la distribuzione di contenuti. In esecuzione in oltre 100 posizioni sul perimetro della rete globale Microsoft, il servizio Frontdoor di Azure consente di compilare, gestire e scalare orizzontalmente le applicazioni Web dinamiche e il contenuto statico. Il servizio Frontdoor di Azure fornisce all'applicazione i vantaggi seguenti: 
* Prestazioni per l'utente finale di livello superiore
* Automazione della manutenzione regionale unificata
* Automazione della continuità aziendale e ripristino di emergenza
* Informazioni client o utente unificate 
* Memorizzazione nella cache 
* Informazioni dettagliate sul servizio La piattaforma garantisce prestazioni, affidabilità e contratti di servizio di supporto tecnico, oltre a certificazioni di conformità e procedure di sicurezza controllabili sviluppate, gestite e supportate in modo nativo da Azure.

Il [**gateway applicazione**][AppGW] è un'appliance virtuale dedicata che offre un servizio di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller) e varie funzionalità di bilanciamento del carico di livello 7 per l'applicazione. Consente di ottimizzare la produttività delle Web farm eseguendo l'offload all'istanza del gateway applicazione della terminazione SSL con utilizzo elevato di CPU. Offre inoltre funzionalità di routing di livello 7, tra cui gli esempi seguenti: 
* Distribuzione round-robin del traffico in ingresso 
* Affinità di sessione basata su cookie 
* Routing basato su percorsi URL 
* Possibilità di ospitare più siti Web dietro un'unica istanza del gateway applicazione Nello SKU WAF del gateway applicazione è incluso anche un web application firewall (WAF). Questo SKU offre alle applicazioni Web la protezione da exploit e vulnerabilità Web comuni. Il gateway applicazione può essere configurato come gateway con connessione Internet, come gateway solo interno o come una combinazione di queste due opzioni. 

[**IP pubblici**][PIP]. Alcune funzionalità di Azure consentono di associare gli endpoint di servizio a un indirizzo IP pubblico in modo che la risorsa sia accessibile da Internet. Questo endpoint usa il processo NAT (Network Address Translation) per instradare il traffico fino all'indirizzo e alla porta interni nella rete virtuale di Azure. È questa la modalità primaria che consente al traffico esterno di passare attraverso la rete virtuale. Gli indirizzi IP pubblici possono essere configurati per determinare il traffico autorizzato a passare e come/dove viene convertito nella rete virtuale.

[**Protezione DDoS di Azure Standard**][DDOS] offre funzionalità aggiuntive di mitigazione tramite il [servizio Basic][DDOS] ottimizzate in modo specifico per le risorse di Rete virtuale di Microsoft Azure. Protezione DDoS Standard è semplice da abilitare e non richiede alcuna modifica alle applicazioni. I criteri di protezione sono ottimizzati tramite il monitoraggio del traffico dedicato e algoritmi di apprendimento automatico. I criteri vengono applicati agli indirizzi IP pubblici associati alle risorse distribuite nelle reti virtuali, ad esempio le istanze di Azure Load Balancer, del gateway applicazione di Azure e di Azure Service Fabric. La telemetria in tempo reale è disponibile tramite le viste di Monitoraggio di Azure durante un attacco e come informazione cronologica. È possibile aggiungere protezione al livello dell'applicazione tramite il web application firewall del gateway applicazione di Azure. La protezione viene fornita per gli indirizzi IP pubblici di Azure IPv4.

#### <a name="component-type-monitoring"></a>Tipo di componente: monitoraggio
I componenti di monitoraggio forniscono visibilità e avvisi da tutti gli altri tipi di componenti. Tutti i team devono avere accesso al monitoraggio per i componenti e i servizi a cui hanno accesso. Se è presente un help desk centralizzato o team operativi, questi dovranno avere accesso integrato ai dati forniti da tali componenti.

Azure offre tipi diversi di servizi di registrazione e monitoraggio per tenere traccia del comportamento delle risorse ospitate di Azure. La governance e il controllo dei carichi di lavoro in Azure si basano non solo sulla raccolta dei dati dei log, ma anche sulla possibilità di attivare azioni in base a specifici eventi segnalati.

[**Monitoraggio di Azure**][Monitor]. Azure include più servizi che singolarmente eseguono un'attività o un ruolo specifico nell'area di monitoraggio. Insieme, questi servizi offrono una soluzione completa per la raccolta, l'analisi e l'utilizzo dei dati di telemetria dall'applicazione e dalle risorse di supporto di Azure sottostanti. Possono anche essere usati per monitorare le risorse locali critiche in modo da fornire un ambiente di monitoraggio ibrido. Conoscere gli strumenti e i dati disponibili è il primo passo per sviluppare una strategia di monitoraggio completa per l'applicazione.

In Azure sono disponibili due tipi principali di log:

-   I [log attività di Azure][ActLog], denominati in precedenza **log operativi**, offrono informazioni approfondite in merito alle operazioni eseguite sulle risorse nella sottoscrizione di Azure. Questi log segnalano gli eventi del piano di controllo per le sottoscrizioni. Ogni risorsa di Azure genera log di controllo.

-   I [log di diagnostica di Monitoraggio di Azure][DiagLog] vengono generati da una risorsa e forniscono dati avanzati e frequenti sul funzionamento di tale risorsa. Il contenuto di questi log varia in base al tipo di risorsa.

[![9]][9]

In un data center virtuale è importante tenere traccia dei log dei gruppi di sicurezza di rete, in particolare di queste informazioni:

-   I [registri eventi][NSGLog] forniscono informazioni su quali regole dei gruppi di sicurezza di rete sono applicate alle macchine virtuali e ai ruoli delle istanze in base all'indirizzo MAC.
-   I [log contatori][NSGLog] tengono traccia di quante volte ogni regola dei gruppi di sicurezza di rete è stata eseguita per rifiutare o consentire il traffico.

Tutti i log possono essere archiviati negli account di archiviazione di Azure a scopo di controllo, analisi statica o backup. Quando i log vengono archiviati in un account di archiviazione di Azure, i clienti possono usare tipi diversi di framework per recuperare, preparare, analizzare e visualizzare questi dati per segnalare lo stato e l'integrità delle risorse cloud. 

Le grandi imprese devono avere già acquisito un framework standard per il monitoraggio dei sistemi locali e possono estendere tale framework per integrare i log generati dalle distribuzioni cloud. Con [Azure Log Analytics][https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-queries] le organizzazioni possono tenere tutte le registrazioni nel cloud. Poiché Log Analytics viene implementato come servizio basato sul cloud, è possibile renderlo operativo rapidamente con un investimento minimo nei servizi di infrastruttura. Log Analytics può anche essere integrato con i componenti di System Center, come System Center Operations Manager, per estendere al cloud gli investimenti per la gestione già esistenti. 

Log Analytics è un servizio di Azure che consente di raccogliere, correlare, cercare e modificare i dati dei log e delle prestazioni generati da sistemi operativi, applicazioni e componenti cloud dell'infrastruttura. Fornisce ai clienti informazioni operative in tempo reale usando la ricerca integrata e i dashboard personalizzati per analizzare tutti i record in tutti i carichi di lavoro di un data center virtuale.

[Azure Network Watcher][NetWatch] fornisce gli strumenti per il monitoraggio, la diagnostica, la visualizzazione delle metriche e l'abilitazione o la disabilitazione dei log per le risorse in una rete virtuale di Azure. È un servizio con più sfaccettature che consente le funzionalità seguenti e altro ancora:
-    Monitorare la comunicazione tra una macchina virtuale e un endpoint
-    Visualizzare le risorse in una rete virtuale e le relative relazioni
-    Diagnosticare i problemi di filtro del traffico di rete da o verso una macchina virtuale
-    Diagnosticare i problemi di routing di rete da una macchina virtuale
-    Diagnosticare i problemi delle connessioni in uscita da una macchina virtuale
-    Acquisire i pacchetti da e verso una macchina virtuale
-    Diagnosticare i problemi relativi a un gateway di rete virtuale e alle connessioni di Azure
-    Determinare le latenze relative tra aree di Azure e provider di servizi Internet
-    Visualizzare le regole di sicurezza per un'interfaccia di rete
-    Visualizzare le metriche di rete
-    Analizzare il traffico da o verso un gruppo di sicurezza di rete
-    Visualizzare i log di diagnostica per le risorse di rete

La soluzione [Monitoraggio prestazioni rete][NPM] all'interno di Operations Management Suite può fornire dettagliate informazioni end-to-end sulla rete, tra cui una singola visualizzazione delle reti di Azure e delle reti locali, con monitoraggi specifici per ExpressRoute e servizi pubblici.

#### <a name="component-type-workloads"></a>Tipo di componente: carichi di lavoro
Nei componenti di tipo carico di lavoro si trovano le applicazioni e i servizi effettivi. Sono anche i componenti a cui i team di sviluppo di applicazioni dedicano più tempo.

Le possibilità dei carichi di lavoro sono infinite. I seguenti sono solo alcuni dei possibili tipi di carichi di lavoro:

Le **applicazioni line-of-business interne** sono applicazioni informatiche cruciali per il normale funzionamento di un'azienda. Le applicazioni line-of-business presentano alcune caratteristiche comuni:

-   Sono **interattive** per natura. Vengono immessi dati e vengono restituiti risultati o report.
-   Sono **basate sui dati**&mdash;sono a elevato utilizzo di dati con accesso frequente ai database o alle altre risorse di archiviazione.
-   Sono **integrate**&mdash;consentono l'integrazione con gli altri sistemi interni o esterni all'organizzazione.

**Siti Web per i clienti (per Internet o Intranet)**. La maggior parte delle applicazioni che interagiscono con Internet è costituita da siti Web. Azure offre la possibilità di eseguire un sito Web in una macchina virtuale IaaS o da un sito per la [funzionalità App Web del Servizio app di Microsoft Azure][WebApps] (PaaS). App Web supporta l'integrazione con le reti virtuali che consentono la distribuzione di App Web nello spoke di un data center virtuale. Per quanto riguarda i siti Web interni, con l'integrazione con le reti virtuali, non è necessario esporre un endpoint Internet per le applicazioni, ma è invece possibile usare le risorse tramite indirizzi instradabili non Internet privati dalla rete virtuale privata.

**Big Data e analisi**. Quando è necessario aumentare di molto il volume dei dati, è possibile che le prestazioni dei database non aumentino correttamente. La tecnologia Apache Hadoop offre un sistema per eseguire parallelamente le query distribuite in un numero elevato di nodi. I clienti hanno la possibilità di eseguire i carichi di lavoro dei dati nelle macchine virtuali IaaS o in ambiente PaaS ([Azure HDInsight][HDI]). HDInsight supporta la distribuzione in una rete virtuale basata sulla posizione, in un cluster in uno spoke del data center virtuale.

**Eventi e messaggistica**. [Hub eventi di Azure][EventHubs] è un servizio di inserimento di dati di telemetria su vastissima scala che raccoglie, trasforma e archivia milioni di eventi. In quanto piattaforma di streaming distribuita, offre bassa latenza e tempo di conservazione configurabile, permettendo di inserire quantità molto elevate di dati di telemetria in Azure e leggere tali dati da più applicazioni. Con Hub eventi, un solo flusso può supportare pipeline sia in tempo reale che basate su batch.

Un servizio di messaggistica cloud altamente affidabile tra applicazioni e servizi può essere implementato tramite il [bus di servizio di Azure][ServiceBus] che offre la messaggistica negoziata asincrona tra il client e il server, insieme a funzionalità di messaggistica e pubblicazione e sottoscrizione FIFO (First-In-First-Out) strutturate.

[![10]][10]

### <a name="multiple-vdc-implementations"></a>Implementazioni di data center virtuale multiple


Un data center virtuale singolo viene tuttavia ospitato in una sola area ed è soggetto a gravi interruzioni che potrebbero interessare l'intera area. I clienti che vogliono ottenere contratti di servizio elevati devono proteggere i servizi distribuendo lo stesso progetto in due o più implementazioni di data center virtuale, inseriti in aree diverse.

A parte le preoccupazioni relative al contratto di servizio, sono diversi gli scenari comuni in cui la distribuzione di più implementazioni di data center virtuale è la scelta migliore:

-   Presenza a livello di area o globale
-   Ripristino di emergenza
-   Meccanismo per deviare il traffico tra data center

#### <a name="regional-and-global-presence"></a>Presenza a livello di area e globale
I data center di Azure sono presenti in molte aree in tutto il mondo. Quando selezionano più data center di Azure, i clienti devono considerare due fattori correlati: le distanze geografiche e la latenza. Per offrire la miglior esperienza utente possibile, i clienti devono valutare la distanza geografica tra le implementazioni di data center virtuale e la distanza tra le implementazioni di data center virtuale e gli utenti finali.

L'area di Azure in cui sono ospitate le implementazioni di data center virtuale deve anche essere conforme ai requisiti normativi stabiliti dalla giurisdizione legale in cui opera l'organizzazione.

#### <a name="disaster-recovery"></a>Ripristino di emergenza
L'implementazione di un piano di ripristino di emergenza è strettamente correlata al tipo di carico di lavoro interessato e alla possibilità di sincronizzare lo stato del carico di lavoro tra implementazioni di data center virtuale diverse. Idealmente, la maggior parte dei clienti vuole sincronizzare i dati dell'applicazione tra le distribuzioni in esecuzione in due implementazioni di data center virtuale diverse per implementare un meccanismo di failover veloce. La maggior parte delle applicazioni è sensibile alla latenza e può quindi causare potenziali timeout e ritardi nella sincronizzazione dei dati.

Per il monitoraggio della sincronizzazione o dell'heartbeat in implementazioni di data center virtuale diverse, è necessario che i data center comunichino tra loro. Due implementazioni di data center virtuale in aree diverse possono essere connesse tramite:

-   Peering di rete virtuale che può connettere hub in diverse aree.
-   Peering privato di ExpressRoute quando gli hub dei data center virtuali sono connessi allo stesso circuito di ExpressRoute.
-   Più circuiti di ExpressRoute connessi tramite il backbone aziendale e la rete di data center virtuali connessi ai circuiti di ExpressRoute.
-   Connessioni VPN da sito a sito tra gli hub dei data center virtuali in ogni area di Azure.

Le connessioni tramite il peering di rete virtuale o ExpressRoute sono in genere il meccanismo preferito per la maggiore larghezza di banda e la latenza coerente durante il transito attraverso il backbone Microsoft.

Non esiste un modo infallibile per convalidare un'applicazione distribuita tra due o più implementazioni di data center virtuale diverse che si trovano in aree diverse. I clienti devono eseguire test di qualifica della rete per verificare la latenza e la larghezza di banda delle connessioni e determinare se sia appropriata la replica dei dati sincrona o asincrona e quale possa essere l'obiettivo del tempo di ripristino ottimale per i carichi di lavoro.

#### <a name="mechanism-to-divert-traffic-between-datacenters"></a>Meccanismo per deviare il traffico tra data center
Una tecnica efficace per deviare il traffico in ingresso in una data center a un altro si basa su Domain Name System (DNS). [Gestione traffico di Azure][TM] usa il meccanismo DNS per indirizzare il traffico dell'utente finale all'endpoint pubblico più appropriato in un data center virtuale specifico. Tramite i probe, Gestione traffico controlla regolarmente l'integrità del servizio degli endpoint pubblici in implementazioni di data center virtuale diverse e, in caso di errore in tali endpoint, esegue automaticamente il routing al data center virtuale secondario.

Gestione traffico utilizza gli endpoint pubblici di Azure e può essere usato, ad esempio, per controllare o deviare il traffico alle macchine virtuali di Azure e alle app Web nel data center virtuale appropriato. Gestione traffico è resiliente anche nel caso in cui si verifichino errori in un'intera area di Azure e può controllare la distribuzione del traffico degli utenti per gli endpoint di servizio in implementazioni di data center virtuale diverse in base a più criteri, ad esempio errore di un servizio in un data center virtuale specifico o selezione del data center virtuale con la latenza di rete più bassa per il client.

### <a name="conclusion"></a>Conclusioni
Il data center virtuale è un approccio alla migrazione del data center nel cloud, che usa una combinazione di funzionalità e caratteristiche per creare un'architettura scalabile in Azure che ottimizzi l'uso delle risorse del cloud, riducendo i costi e semplificando la governance di sistema. Il concetto di data center virtuale si basa sulla topologia hub-spoke che fornisce servizi condivisi comuni nell'hub e applicazioni o carichi di lavoro specifici negli spoke. Un data center virtuale corrisponde alla struttura dei ruoli aziendali, in cui reparti diversi collaborano tra loro. Esempi di reparti sono IT centrale, Azure DevOps, operazioni e manutenzione. Ciascun reparto ha un elenco specifico di ruoli e compiti. Un data center virtuale soddisfa i requisiti della migrazione **lift-and-shift**, ma offre anche molti vantaggi per le distribuzioni cloud native.

## <a name="references"></a>Riferimenti
Per altre informazioni sulle funzionalità illustrate in questo articolo, vedere i collegamenti seguenti:

| | | |
|-|-|-|
|Funzionalità di rete|Bilanciamento del carico.|Connettività|
|[Rete virtuale di Azure][VNet]</br>[Gruppi di sicurezza di rete][NSG]</br>[Log per i gruppi di sicurezza di rete][NSGLog]</br>[Route definite dall'utente][UDR]</br>[Appliance virtuali di rete][NVA]</br>[Indirizzi IP pubblici][PIP]</br>[Protezione DDoS di Azure][DDOS]</br>[Firewall di Azure][AzFW]</br>[DNS di Azure][DNS]|[Frontdoor di Azure][AFD]</br>[Azure Load Balancer (L3)][ALB]</br>[Gateway applicazione (L7)][AppGW]</br>[Web application firewall][WAF]</br>[Gestione traffico di Azure][TM]</br></br></br></br></br> |[Peering di rete virtuale][VNetPeering]</br>[Rete privata virtuale][VPN]</br>[Rete WAN virtuale][vWAN]</br>[ExpressRoute][ExR]</br>[ExpressRoute Direct][ExRD]</br></br></br></br></br>
|Identità</br>|Monitoraggio</br>|Procedure consigliate</br>|
|[Azure Active Directory][AAD]</br>[Multi-Factor Authentication][MFA]</br>[Controllo degli accessi in base al ruolo][RBAC]</br>[Ruoli predefiniti di Azure AD][Roles]</br></br></br> |[Network Watcher][NetWatch]</br>[Monitoraggio di Azure][Monitor]</br>[Log attività][ActLog]</br>[Log di diagnostica di Monitoraggio][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br>[Monitoraggio prestazioni rete][NPM]|[Procedure consigliate per le reti perimetrali][DMZ]</br>[Gestione sottoscrizioni][SubMgmt]</br>[Gestione di gruppi di risorse][RGMgmt]</br>[Limiti delle sottoscrizioni di Azure][Limits] </br></br></br>|
|Altri servizi di Azure|
|[App Web][WebApps]</br>[HDInsight (Hadoop)][HDI]</br>[Hub eventi][EventHubs]</br>[Bus di servizio][ServiceBus]|

## <a name="next-steps"></a>Passaggi successivi
 - Esplorare il [peering di rete virtuale][VNetPeering], la tecnologia fondamentale per le progettazioni di hub e spoke di un data center virtuale.
 - Implementare [Azure AD][AAD] per iniziare l'esplorazione del [controllo degli accessi in base al ruolo][RBAC].
 - Sviluppare un modello di gestione della sottoscrizione e delle risorse per rispettare la struttura, i requisiti e i criteri dell'organizzazione. L'attività più importante è la pianificazione. Da un punto di vista pratico, è consigliabile pianificare riorganizzazioni, fusioni, nuove linee di prodotti e altre possibilità.

<!--Image References-->
[0]: ./images/networking-redundant-equipment.png "Esempi di sovrapposizione di componenti" 
[1]: ./images/networking-vdc-high-level.png "Esempio generale di data center virtuale con hub e spoke"
[2]: ./images/networking-hub-spokes-cluster.png "Cluster di hub e spoke"
[3]: ./images/networking-spoke-to-spoke.png "Da spoke a spoke"
[4]: ./images/networking-vdc-block-level-diagram.png "Diagramma del livello di blocco del data center virtuale"
[5]: ./images/networking-users-groups-subsciptions.png "Utenti, gruppi, sottoscrizioni e progetti"
[6]: ./images/networking-infrastructure-high-level.png "Diagramma dell'infrastruttura generale"
[7]: ./images/networking-highlevel-perimeter-networks.png "Diagramma dell'infrastruttura generale"
[8]: ./images/networking-vnet-peering-perimeter-neworks.png "Peering reti virtuali e reti perimetrali"
[9]: ./images/networking-high-level-diagram-monitoring.png "Diagramma generale per il monitoraggio"
[10]: ./images/networking-high-level-workloads.png "Diagramma generale per il carico di lavoro"

<!--Link References-->
[Limits]: /azure/azure-subscription-service-limits
[Roles]: /azure/role-based-access-control/built-in-roles
[VNet]: /azure/virtual-network/virtual-networks-overview
[NSG]: /azure/virtual-network/virtual-networks-nsg
[DNS]: /azure/dns/dns-overview
[PrivateDNS]: /azure/dns/private-dns-overview
[VNetPeering]: /azure/virtual-network/virtual-network-peering-overview 
[UDR]: /azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: /azure/role-based-access-control/overview
[MFA]: /azure/multi-factor-authentication/multi-factor-authentication
[AAD]: /azure/active-directory/active-directory-whatis
[VPN]: /azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: /azure/expressroute/expressroute-introduction
[ExRD]: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-erdirect-about
[vWAN]: /azure/virtual-wan/virtual-wan-about
[NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[AzFW]: /azure/firewall/overview
[SubMgmt]: /azure/architecture/cloud-adoption/appendix/azure-scaffold 
[RGMgmt]: /azure/azure-resource-manager/resource-group-overview
[DMZ]: /azure/best-practices-network-security
[ALB]: /azure/load-balancer/load-balancer-overview
[DDOS]: /azure/virtual-network/ddos-protection-overview
[PIP]: /azure/virtual-network/resource-groups-networking#public-ip-address
[AFD]: https://docs.microsoft.com/en-us/azure/frontdoor/front-door-overview
[AppGW]: /azure/application-gateway/application-gateway-introduction
[WAF]: /azure/application-gateway/application-gateway-web-application-firewall-overview
[Monitor]: /azure/monitoring-and-diagnostics/
[ActLog]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: /azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: /azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: /azure/operations-management-suite/operations-management-suite-overview
[NPM]: /azure/log-analytics/log-analytics-network-performance-monitor
[NetWatch]: /azure/network-watcher/network-watcher-monitoring-overview
[WebApps]: /azure/app-service/
[HDI]: /azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: /azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: /azure/service-bus-messaging/service-bus-messaging-overview
[TM]: /azure/traffic-manager/traffic-manager-overview

