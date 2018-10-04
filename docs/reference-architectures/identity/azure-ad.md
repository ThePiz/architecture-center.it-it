---
title: Integrare i domini AD locali con Azure Active Directory
description: Come implementare un'architettura di rete ibrida protetta con Azure Active Directory.
author: telmosampaio
pnp.series.title: Identity management
ms.date: 11/28/2016
pnp.series.next: adds-extend-domain
pnp.series.prev: ./index
cardTitle: Integrate on-premises AD with Azure AD
ms.openlocfilehash: b5cd4d353c6d6b149c9c1b5547f8da8c25ad5a85
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47429231"
---
# <a name="integrate-on-premises-active-directory-domains-with-azure-active-directory"></a>Integrare i domini Active Directory locali con Azure Active Directory

Azure Active Directory (Azure AD) è un servizio per la gestione delle identità e delle directory multi-tenant, basato sul cloud. Questa architettura di riferimento illustra le procedure consigliate per l'integrazione di domini Active Directory locali con Azure AD per fornire l'autenticazione delle identità basata sul cloud. [**Distribuire questa soluzione**.](#deploy-the-solution)

[![0]][0] 

*Scaricare un [file Visio][visio-download] di questa architettura.*

> [!NOTE]
> Per semplicità questo diagramma mostra solo le connessioni direttamente correlate ad Azure AD e non il traffico correlato al protocollo che può verificarsi durante l'autenticazione e la federazione delle identità. Ad esempio, un'applicazione Web può reindirizzare il Web browser per autenticare la richiesta mediante Azure AD. Una volta autenticata, la richiesta può essere passata nuovamente all'applicazione Web, con le informazioni sull'identità appropriate.
> 

Gli usi tipici di questa architettura di riferimento includono:

* Le applicazioni Web distribuite in Azure che forniscono l'accesso agli utenti remoti che appartengono all'organizzazione.
* L'implementazione delle capacità self-service per gli utenti finali, ad esempio la reimpostazione della password e la delega della gestione dei gruppi. Si noti che questa operazione richiede Azure AD Premium edition.
* Le architetture in cui la rete locale e la rete virtuale di Azure dell'applicazione non sono connesse tramite un tunnel VPN o un circuito ExpressRoute.

> [!NOTE]
> Azure AD può autenticare l'identità di utenti e applicazioni presenti nella directory di un'organizzazione. Alcune applicazioni e alcuni servizi, ad esempio SQL Server, potrebbero richiedere l'autenticazione computer. In questo caso questa soluzione non è appropriata.
> 

Per altre considerazioni, vedere l'articolo su come [scegliere una soluzione per l'integrazione di Active Directory locale con Azure][considerations]. 

## <a name="architecture"></a>Architettura

L'architettura include i componenti indicati di seguito.

* **Tenant di Azure AD**. Istanza di [Azure Active Directory][azure-active-directory] creata dall'organizzazione dell'utente. Funge da servizio di directory per le applicazioni cloud archiviando oggetti copiati da Active Directory locale e fornisce servizi di identità.
* **Subnet di livello Web**. Questa subnet include macchine virtuali che eseguono un'applicazione Web. Azure Active Directory può fungere da broker di identità per questa applicazione.
* **Server Active Directory Domain Services locale**. Servizio di identità e directory locale. La directory Active Directory Domain Services può essere sincronizzata con Azure Active Directory per consentire l'autenticazione di utenti locali.
* **Server di sincronizzazione di Azure AD Connect**. Un computer locale che esegue il servizio di sincronizzazione [Azure AD Connect][azure-ad-connect]. Questo servizio consente di sincronizzare le informazioni contenute in Active Directory locale in Azure AD. Ad esempio, se si esegue il provisioning o il deprovisioning di gruppi e utenti locali, queste modifiche verranno propagate in Azure AD. 
  
  > [!NOTE]
  > Per motivi di sicurezza, Azure AD archivia le password dell'utente come hash. Se un utente richiede la reimpostazione della password, questa deve essere eseguita in locale e il nuovo valore hash deve essere inviato ad Azure AD. Le Azure AD Premium Edition includono funzionalità in grado di automatizzare questa attività per consentire agli utenti di reimpostare la propria password.
  > 

* **Macchine virtuali per l'applicazione a più livelli**. La distribuzione include l'infrastruttura per un'applicazione a più livelli. Per altre informazioni su queste risorse, vedere [Eseguire macchine virtuali Windows per un'applicazione a più livelli][implementing-a-multi-tier-architecture-on-Azure].

## <a name="recommendations"></a>Consigli

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda. 

### <a name="azure-ad-connect-sync-service"></a>Servizio di sincronizzazione Azure AD Connect

Il servizio di sincronizzazione Azure AD Connect garantisce che le informazioni sull'identità archiviate nel cloud siano coerenti con quelle conservate in locale. Installare questo servizio usando il software Azure AD Connect. 

Prima di implementare la sincronizzazione Azure AD Connect, stabilire i requisiti di sincronizzazione della propria organizzazione. Ad esempio, cosa sincronizzare, da quali domini e con quale frequenza. Per altre informazioni, vedere [Determinare i requisiti di sincronizzazione delle directory][aad-sync-requirements].

È possibile eseguire il servizio di sincronizzazione Azure AD Connect in una macchina virtuale o in un computer ospitato in locale. A causa della volatilità delle informazioni nella directory di Active Directory, è improbabile che il carico sul servizio di sincronizzazione Azure AD Connect sia elevato dopo la sincronizzazione iniziale con Azure AD. L'esecuzione del servizio su una macchina virtuale rende più semplice scalare il server, se necessario. Monitorare l'attività nella macchina virtuale come descritto nella sezione Considerazioni di monitoraggio per determinare se è necessario scalare.

Se in una foresta sono presenti più domini locali, si consiglia di archiviare e sincronizzare le informazioni per l'intera foresta in un singolo tenant di Azure AD. Filtrare le informazioni per le identità che presenti in più di un dominio, in modo che ogni identità non sia duplicata ma venga visualizzata una sola volta in Azure AD. La duplicazione può determinare incoerenze quando i dati vengono sincronizzati. Per altre informazioni, vedere la sezione Topologia di seguito. 

Usare i filtri in modo che in Azure AD siano archiviati solo i dati necessari. Ad esempio, l'organizzazione potrebbe non essere interessata ad archiviare le informazioni sugli account inattivi in Azure AD. I filtri possono essere basati sui gruppi, sui domini, sull'unità organizzativa (OU) o sugli attributi. È possibile combinare i filtri per generare regole più complesse. Ad esempio, è possibile sincronizzare gli oggetti inclusi in un dominio che presentano un valore specifico in un attributo selezionato. Per informazioni dettagliate, vedere [Servizio di sincronizzazione Azure AD Connect: Configurare il filtro][aad-filtering].

Per implementare la disponibilità elevata per il servizio di sincronizzazione AD Connect, eseguire un server di gestione temporanea secondario. Per altre informazioni, vedere la sezione relativa alle raccomandazioni sulla topologia di seguito.

### <a name="security-recommendations"></a>Suggerimenti per la sicurezza

**Gestione delle password utente.** Le edizioni di Azure AD Premium supportano il ripristino delle password, consentendo agli utenti locali di eseguire la reimpostazione della password self-service dal portale di Azure. Questa funzionalità deve essere abilitata solo dopo avere esaminato i criteri di sicurezza delle password dell'organizzazione. Ad esempio, è possibile limitare gli utenti che possono modificare la password e personalizzare l'esperienza di gestione delle password. Per altre informazioni, vedere [Personalizzare la funzionalità di Azure AD per la reimpostazione della password self-service][aad-password-management]. 

**Proteggere le applicazioni locali accessibili dall'esterno.** Usare il proxy di applicazione di Azure AD per offrire accesso controllato alle applicazioni Web locali per gli utenti esterni tramite Azure AD. Solo gli utenti che dispongono di credenziali valide nella directory di Azure sono autorizzati a usare l'applicazione. Per altre informazioni, vedere l'articolo [Attività iniziali del proxy di applicazione e installazione del connettore][aad-application-proxy].

**Monitorare attivamente Azure AD per rilevare segnali di attività sospette.**    È consigliabile usare l'edizione Azure AD Premium P2 che include Azure AD Identity Protection. Identity Protection usa l'euristica e algoritmi di apprendimento automatico adattivi per rilevare anomalie ed eventi di rischio che possono indicare la compromissione di un'identità. Ad esempio, è possibile rilevare attività potenzialmente insolite, come attività di accesso irregolari, accessi da origini sconosciute o da indirizzi IP con attività sospette oppure accessi da dispositivi che potrebbero essere infetti. Sulla base di tali dati, Identity Protection genera report e avvisi che consentono di analizzare gli eventi di rischio e adottare le azioni appropriate. Per altre informazioni, vedere [Azure Active Directory Identity Protection][aad-identity-protection].
  
È possibile usare la funzionalità di creazione di report di Azure Active Directory nel portale di Azure per monitorare le attività relative alla sicurezza che si verificano nel sistema. Per altre informazioni sull'uso di questi report, vedere [Creazione di report in Azure Active Directory][aad-reporting-guide].

### <a name="topology-recommendations"></a>Raccomandazioni sulla topologia

Configurare Azure AD Connect per implementare una topologia che corrisponda il più possibile ai requisiti dell'organizzazione. Di seguito sono elencate le topologie supportate da Azure AD Connect.

* **Foresta singola, singola directory di Azure AD**. In questa topologia Azure AD Connect sincronizza oggetti e informazioni sull'identità da uno o più domini in una singola foresta locale in un unico tenant di Azure AD. Questa è la topologia predefinita implementata dall'installazione rapida di Azure AD Connect.
  
  > [!NOTE]
  > Non usare più server di sincronizzazione di Azure AD Connect per connettersi a domini diversi nella stessa foresta locale per lo stesso tenant di Azure AD, a meno che non si esegua un server in modalità di gestione temporanea, come descritto di seguito.
  > 
  > 

* **Più foreste, singola directory di Azure AD**. In questa topologia Azure AD Connect sincronizza gli oggetti e le informazioni sull'identità da più foreste in un unico tenant di Azure AD. Usare questa topologia se l'organizzazione dispone di più di una foresta locale. È possibile consolidare le informazioni sull'identità in modo che ogni singolo utente venga rappresentato una volta nella directory di Azure AD, anche se lo stesso utente è presente in più di una foresta. Tutte le foreste usano lo stesso server di sincronizzazione di Azure AD Connect. Non è necessario che il server di sincronizzazione di Azure AD Connect faccia parte di un dominio, ma deve essere raggiungibile da tutte le foreste.
  
  > [!NOTE]
  > In questa topologia non usare server di sincronizzazione separati di Azure AD Connect per connettere ogni foresta locale a un singolo tenant di Azure AD. Ciò può generare informazioni sull'identità duplicate in Azure AD, se gli utenti sono presenti in più foreste.
  > 
  > 

* **Più foreste, topologie separate**. Questa topologia unisce le informazioni sull'identità da foreste separate in un singolo tenant di Azure AD, considerando tutte le foreste come entità separate. Questa topologia è utile se si desidera combinare le foreste di organizzazioni diverse e le informazioni sull'identità per ogni utente si trovano in una sola foresta.
  
  > [!NOTE]
  > Se gli elenchi di indirizzi globali in ogni foresta sono sincronizzati, è possibile che un utente in una foresta sia presente in un'altra come contatto. Ciò può verificarsi se l'organizzazione ha implementato GALSync con Forefront Identity Manager 2010 o Microsoft Identity Manager 2016. In questo scenario è possibile specificare che gli utenti devono essere identificati dal proprio attributo *Mail*. È inoltre possibile associare le identità usando gli attributi *ObjectSID* e *msExchMasterAccountSID*. Questo approccio risulta utile in presenza di una o più foreste risorse con account disabilitati.
  > 
  > 

* **Server di gestione temporanea**. In questa configurazione viene eseguita una seconda istanza del server di sincronizzazione Azure AD Connect in parallelo con il primo. Questa struttura supporta scenari come quelli illustrati di seguito.
  
  * Disponibilità elevata.
  * Test e distribuzione di una nuova configurazione del server di sincronizzazione Azure AD Connect.
  * Introduzione di un nuovo server e rimozione di una configurazione precedente. 
    
    In questi scenari la seconda istanza viene eseguita in *modalità di gestione temporanea*. Il server registra gli oggetti importati e i dati di sincronizzazione nel proprio database, ma non passa i dati in Azure AD. Se si disattiva la modalità di gestione temporanea, il server inizia la scrittura dei dati in Azure AD e avvia l'esecuzione del ripristino delle password nelle directory locali, laddove appropriato. Per altre informazioni, vedere [Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni][aad-connect-sync-operational-tasks].

* **Più directory di Azure AD**. È consigliabile creare una singola directory di Azure AD per un'organizzazione, ma potrebbero verificarsi situazioni in cui è necessario partizionare le informazioni in directory di Azure AD separate. In questo caso è possibile evitare i problemi di sincronizzazione e di ripristino delle password verificando che ogni oggetto dalla foresta locale sia presente in una sola directory di Azure AD. Per implementare questo scenario, configurare server di sincronizzazione Azure AD Connect separati per ogni directory di Azure AD e usare i filtri in modo che ogni server di sincronizzazione di Azure AD Connect operi su set di oggetti che si escludono a vicenda. 

Per altre informazioni su queste topologie, vedere [Topologie per Azure AD Connect][aad-topologies].

### <a name="user-authentication"></a>Autenticazione utente

Per impostazione predefinita, il server di sincronizzazione di Azure AD Connect consente di configurare la sincronizzazione degli hash delle password tra il dominio locale e Azure AD e il servizio di Azure AD presuppone che gli utenti eseguano l'autenticazione con la stessa password che usano in locale. Per molte organizzazioni questo comportamento è appropriato, ma è necessario considerare l'infrastruttura e i criteri esistenti dell'organizzazione specifica. Ad esempio: 

* I criteri di sicurezza dell'organizzazione potrebbero non consentire la sincronizzazione degli hash delle password nel cloud. In questo caso l'organizzazione dovrà prendere in considerazione l'[autenticazione pass-through](/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).
* Quando si accede alle risorse del cloud da computer appartenenti al dominio nella rete aziendale, è necessario che gli utenti non vadano incontro ad alcun problema nell’uso dell’SSO.
* L'organizzazione potrebbe avere già distribuito Active Directory Federation Services (ADFS) o provider federativi di terze parti. È possibile configurare Azure AD in modo che per implementare l'autenticazione e SSO usi questa infrastruttura anziché le informazioni relative alle password che si trovano nel cloud.

Per altre informazioni, vedere [Opzioni di accesso utente di Azure AD Connect][aad-user-sign-in].

### <a name="azure-ad-application-proxy"></a>Proxy di applicazione di Azure AD 

Usare Azure AD per consentire l'accesso alle applicazioni locali.

Esporre le applicazioni Web locali usando connettori proxy di applicazione gestiti dal componente proxy di applicazione Azure AD. Il connettore proxy di applicazione apre una connessione di rete in uscita per il proxy di applicazione Azure AD e le richieste degli utenti remoti vengono reinstradate da Azure AD alle app Web tramite questa connessione. Ciò elimina la necessità di aprire porte in ingresso nel firewall locale e riduce la superficie di attacco esposta dall'organizzazione.

Per altre informazioni, vedere [Attività iniziali del proxy di applicazione e installazione del connettore][aad-application-proxy].

### <a name="object-synchronization"></a>Sincronizzazione degli oggetti 

La configurazione predefinita di Azure AD Connect sincronizza gli oggetti dalla directory di Active Directory locale in base alle regole specificate nell'articolo [Servizio di sincronizzazione Azure AD Connect: Informazioni sulla configurazione predefinita][aad-connect-sync-default-rules]. Gli oggetti che soddisfano queste regole vengono sincronizzati mentre tutti gli altri oggetti vengono ignorati. Alcune regole di esempio:

* Gli oggetti utente devono avere un attributo *sourceAnchor* univoco e l'attributo *accountEnabled*.
* Gli oggetti utente devono avere un attributo *sAMAccountName* e non possono iniziare con il testo *Azure AD_* o *MSOL_*.

Azure AD Connect applica diverse regole agli oggetti User, Contact, Group, ForeignSecurityPrincipal e Computer. Usare l'Editor regole di sincronizzazione installato con Azure AD Connect, se è necessario modificare il set predefinito di regole. Per altre informazioni, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sulla configurazione predefinita][aad-connect-sync-default-rules].

È inoltre possibile definire filtri personalizzati per limitare gli oggetti che devono essere sincronizzati dal dominio o dall'unità organizzativa. In alternativa, è possibile implementare filtri personalizzati più complessi, come descritto in [Servizio di sincronizzazione Azure AD Connect: Configurare il filtro][aad-filtering].

### <a name="monitoring"></a>Monitoraggio 

Il monitoraggio dell'integrità viene eseguito dagli agenti elencati di seguito installati in locale:

* Azure AD Connect consente di installare un agente che acquisisce le informazioni sulle operazioni di sincronizzazione. Usare il pannello di Azure AD Connect Health nel portale di Azure per monitorarne lo stato e le prestazioni. Per altre informazioni, vedere [Monitorare la sincronizzazione di Azure AD Connect con Azure AD Connect Health][aad-health].
* Per monitorare l'integrità dei domini di Active Directory e delle directory da Azure, installare Azure AD Connect Health per l'agente Active Directory Domain Services in un computer all'interno del dominio locale. Usare il pannello di Azure Active Directory Connect Health nel portale di Azure per il monitoraggio dello stato. Per altre informazioni, vedere [Uso di Azure AD Connect Health con Servizi di dominio Active Directory][aad-health-adds]. 
* Installare Azure AD Connect Health per l'agente Active Directory Federation Services per monitorare l'integrità dei servizi in esecuzione in locale e usare il pannello di Azure Active Directory Connect Health nel portale di Azure per il monitoraggio di AD FS. Per altre informazioni, vedere [Monitorare AD FS con Azure AD Connect Health][aad-health-adfs].

Per altre informazioni sull'installazione di agenti AD Connect Health e sui relativi requisiti, vedere [Installazione dell'agente di Azure AD Connect Health][aad-agent-installation].

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Il servizio di Azure AD supporta la scalabilità in base alle repliche, con una singola replica primaria che gestisce le operazioni di scrittura e più repliche secondarie di sola lettura. Azure AD reindirizza in modo trasparente i tentativi di scrittura elaborati in risposta alle repliche secondarie alla replica primaria garantendo la coerenza finale. Tutte le modifiche apportate alla replica primaria vengono propagate alle repliche secondarie. Questa architettura consente di scalare in quanto le operazioni eseguite in Azure AD sono per la maggior parte letture e non scritture. Per altre informazioni, vedere l'articolo relativo alla [ directory cloud distribuita a disponibilità elevata con ridondanza geografica di Azure AD][aad-scalability].

Per il server di sincronizzazione di Azure AD Connect, determinare il numero di oggetti che probabilmente verranno sincronizzati dalla directory locale. Se si dispone di meno di 100.000 oggetti, è possibile usare il software SQL Server Express Local DB predefinito fornito con Azure AD Connect. Se si dispone di un numero maggiore di oggetti, è necessario installare una versione di produzione di SQL Server ed eseguire un'installazione personalizzata di Azure AD Connect, specificando che deve usare un'istanza esistente di SQL Server.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Il servizio Azure AD è distribuito geograficamente e viene eseguito in più data center distribuito nel mondo con failover automatico. Se un data center diventa non disponibile, Azure AD garantisce che i dati di directory siano disponibili per l'accesso di istanza in almeno due più centri dati dislocati nella regione.

> [!NOTE]
> Il contratto di servizio (SLA) per Azure AD Basic e i servizi Premium garantisce almeno il 99,9% di disponibilità. Non esiste un contratto di servizio per il livello gratuito di Azure Active Directory. Per altre informazioni, vedere [Contratto di Servizio per Azure Active Directory][sla-aad].
> 
> 

Prendere in considerazione il provisioning di una seconda istanza del server di sincronizzazione di Azure AD Connect in modalità di gestione temporanea per aumentare la disponibilità, come descritto nella sezione relativa alle raccomandazioni sulla topologia. 

Se non si usa l'istanza di SQL Server Express Local DB fornita con Azure AD Connect, considerare l'uso del clustering SQL per ottenere la disponibilità elevata. Soluzioni come il mirroring e Always On non sono supportate da Azure AD Connect.

Per altre considerazioni su come ottenere un'elevata disponibilità del server di sincronizzazione di Azure AD Connect e su come eseguire il ripristino dopo un errore, vedere [Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni] [ aad-sync-disaster-recovery].

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Esistono due aspetti legati alla gestione di Azure AD:

* Amministrazione di Azure AD nel cloud.
* Gestione dei server di sincronizzazione di Azure AD Connect.

Azure Active Directory offre le opzioni seguenti per la gestione dei domini e delle directory nel cloud: 

* **Modulo Azure Active Directory PowerShell**. Usare questo [modulo][aad-powershell] se è necessario eseguire lo script di comuni attività amministrative di Azure AD, ad esempio la gestione di utenti, la gestione di domini e la configurazione di single sign-on.
* **Pannello di gestione di Azure Active Directory nel portale di Azure**. Questo pannello offre una visualizzazione di gestione interattiva della directory e consente di controllare e configurare la maggior parte degli aspetti di Azure Active Directory. 

Azure AD Connect installa gli strumenti seguenti per consentire la gestione di servizi di sincronizzazione di Azure AD Connect dai computer locali:
  
* **Console di Microsoft Azure Active Directory Connect**. Questo strumento consente di modificare la configurazione del server Azure AD Sync, di personalizzare la modalità di esecuzione della sincronizzazione, abilitare o disabilitare la modalità di gestione temporanea e passare alla modalità di accesso utente. Si noti che è possibile abilitare l'accesso ad Active Directory ADFS con l'infrastruttura locale.
* **Synchronization Service Manager**. Usare la scheda *Operazioni* in questo strumento per gestire il processo di sincronizzazione e rilevare se una parte del processo non è riuscita. È possibile attivare le sincronizzazioni manualmente usando questo strumento. La scheda *Connettori* consente di controllare le connessioni per i domini a cui è collegato il motore di sincronizzazione.
* **Editor regole di sincronizzazione**. Usare questo strumento per personalizzare il modo in cui gli oggetti vengono trasformati quando vengono copiati tra una directory locale e Azure AD. Questo strumento consente di specificare attributi e oggetti aggiuntivi per la sincronizzazione, quindi esegue i filtri per determinare quali oggetti devono o non devono essere sincronizzati. Per altre informazioni, vedere la sezione Editor delle regole di sincronizzazione nel documento [Servizio di sincronizzazione Azure AD Connect: Informazioni sulla configurazione predefinita][aad-connect-sync-default-rules].

Per altre informazioni e suggerimenti per la gestione di Azure AD Connect, vedere [Servizio di sincronizzazione Azure AD Connect: procedure consigliate per modificare la configurazione predefinita][aad-sync-best-practices].

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Usare il controllo di accesso condizionale per negare le richieste di autenticazione provenienti da origini impreviste:

- Attivare [Azure Multi-Factor Authentication (MFA)][azure-multifactor-authentication] se un utente prova a connettersi da una posizione non attendibile, ad esempio su Internet anziché una rete attendibile.

- Usare il tipo di piattaforma del dispositivo dell'utente (iOS, Android, Windows Mobile, Windows) per determinare i criteri di accesso alle applicazioni e alle funzionalità.

- Registrare lo stato di abilitazione/disabilitazione dei dispositivi degli utenti e incorporare le informazioni nei controlli dei criteri di accesso. Ad esempio, se il telefono di un utente viene smarrito o rubato, deve essere registrato come disabilitato per evitare che venga usato per ottenere l'accesso.

- Controllare l'accesso utente alle risorse in base all'appartenenza al gruppo. Usare le [regole basate per l'appartenenza dinamica di Azure Active Directory] [ aad-dynamic-membership-rules] per semplificare l'amministrazione dei gruppi. Per una breve panoramica del funzionamento, vedere [Introduction to Dynamic Memberships for Groups][aad-dynamic-memberships].

- Usare i criteri di rischio per l'accesso condizionale con Azure AD Identity Protection per assicurare la protezione avanzata in base ad attività di accesso anomale o altri eventi.

Per altre informazioni, vedere l'articolo relativo all'[accesso condizionale di Azure Active Directory][aad-conditional-access].

## <a name="deploy-the-solution"></a>Distribuire la soluzione

È disponibile una distribuzione per un'architettura di riferimento che implementa queste raccomandazioni e considerazioni su GitHub. Questa architettura di riferimento distribuisce in Azure una rete locale simulata che è possibile usare per eseguire test ed esperimenti. L'architettura di riferimento può essere distribuita con macchine virtuali sia Windows sia Linux seguendo le istruzioni riportate di seguito: 

1. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fidentity%2Fazure-ad%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Dopo avere aperto il collegamento nel portale di Azure, è necessario immettere i valori per alcune impostazioni: 
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-aad-onpremise-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Selezionare **Windows** o **Linux** nella casella di riepilogo a discesa **Tipo di sistema operativo**.
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
3. Attendere il completamento della distribuzione.
4. I file dei parametri includono un nome utente e una password amministratore hardcoded. È consigliabile cambiarli immediatamente in tutte le macchine virtuali. Fare clic su ogni macchina virtuale nel portale di Azure, quindi su **Reimposta password** nel pannello **Supporto e risoluzione dei problemi**. Selezionare **Reimposta password** nella casella di riepilogo a discesa **Modalità**, quindi selezionare un nuovo **Nome utente** e una nuova **Password**. Fare clic sul pulsante **Aggiorna** per rendere permanenti il nuovo nome utente e la nuova password.

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ../virtual-machines-windows/n-tier.md

[aad-agent-installation]: /azure/active-directory/active-directory-aadconnect-health-agent-install
[aad-application-proxy]: /azure/active-directory/active-directory-application-proxy-enable
[aad-conditional-access]: /azure/active-directory//active-directory-conditional-access
[aad-connect-sync-default-rules]: /azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration
[aad-connect-sync-operational-tasks]: /azure/active-directory/hybrid/how-to-connect-sync-operations
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-dynamic-membership-rules]: /azure/active-directory/active-directory-accessmanagement-groups-with-advanced-rules
[aad-editions]: /azure/active-directory/active-directory-editions
[aad-filtering]: /azure/active-directory/hybrid/how-to-connect-sync-configure-filtering
[aad-health]: /azure/active-directory/active-directory-aadconnect-health-sync
[aad-health-adds]: /azure/active-directory/active-directory-aadconnect-health-adds
[aad-health-adfs]: /azure/active-directory/active-directory-aadconnect-health-adfs
[aad-identity-protection]: /azure/active-directory/active-directory-identityprotection
[aad-password-management]: /azure/active-directory/active-directory-passwords-customize
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-reporting-guide]: /azure/active-directory/active-directory-reporting-guide
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-sync-best-practices]: /azure/active-directory/hybrid/how-to-connect-sync-best-practices-changing-default-configuration
[aad-sync-disaster-recovery]: /azure/active-directory/hybrid/how-to-connect-sync-operations#disaster-recovery
[aad-sync-requirements]: /azure/active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements
[aad-topologies]: /azure/active-directory/hybrid/plan-connect-topologies
[aad-user-sign-in]: /azure/active-directory/hybrid/plan-connect-user-signin
[azure-active-directory]: /azure/active-directory-domain-services/active-directory-ds-overview
[azure-ad-connect]: /azure/active-directory/hybrid/whatis-hybrid-identity
[azure-multifactor-authentication]: /azure/multi-factor-authentication/multi-factor-authentication
[considerations]: ./considerations.md
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx


[0]: ./images/azure-ad.png "Architettura identità cloud tramite Azure Active Directory"
