---
title: Implementazione di Active Directory Federation Services (AD FS) in Azure
description: >-
  Come implementare un'architettura di rete ibrida protetta con l'autorizzazione di Active Directory Federation Services in Azure.

  linee guida, gateway vpn, expressroute, bilanciamento del carico, rete virtuale, active directory
author: telmosampaio
ms.date: 11/28/2016
pnp.series.title: Identity management
pnp.series.prev: adds-forest
cardTitle: Extend AD FS to Azure
ms.openlocfilehash: 87489b7b81cf323c221466c539ee14ea90e23c14
ms.sourcegitcommit: e67b751f230792bba917754d67789a20810dc76b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="extend-active-directory-federation-services-ad-fs-to-azure"></a>Estendere Active Directory Federation Services in Azure

Questa architettura di riferimento implementa una rete ibrida protetta che consente di estendere la rete locale in Azure e usa [Active Directory Federation Services (ADFS)][active-directory-federation-services] per eseguire l'autorizzazione e l'autenticazione federate per i componenti in esecuzione in Azure. [**Distribuire questa soluzione**.](#deploy-the-solution)

[![0]][0]

*Scaricare un [file Visio][visio-download] di questa architettura.*

AD FS può essere ospitato in locale ma, se l'applicazione è un ibrido in cui alcune parti vengono implementate in Azure, potrebbe essere più efficace replicare AD FS nel cloud. 

Il diagramma illustra gli scenari seguenti:

* Il codice dell'applicazione di un'organizzazione partner accede a un'applicazione Web ospitata all'interno della rete virtuale di Azure.
* Un utente esterno registrato con le credenziali archiviate all'interno di Active Directory Domain Services (AD DS) accede a un'applicazione Web ospitata nella rete virtuale di Azure.
* Un utente connesso alla rete virtuale con un dispositivo autorizzato esegue un'applicazione Web ospitata nella rete virtuale di Azure.

Tra gli usi tipici di questa architettura sono inclusi:

* Applicazioni ibride in cui i carichi di lavoro vengono eseguiti in parte in locale e in parte in Azure.
* Soluzioni che usano l'autorizzazione federata per esporre le applicazioni Web per le organizzazioni partner.
* Sistemi che supportano l'accesso da Web browser in esecuzione all'esterno del firewall aziendale.
* Sistemi che consentono agli utenti di accedere alle applicazioni Web tramite la connessione da dispositivi esterni autorizzati, ad esempio computer remoti, notebook e altri dispositivi mobili. 

Questa architettura di riferimento è incentrata sulla *federazione passiva*, in cui i server federativi decidono come e quando autenticare un utente. L'utente fornisce informazioni di accesso quando l'applicazione viene avviata. Questo meccanismo viene usato più comunemente dai Web browser e prevede un protocollo che reindirizza il browser a un sito in cui l'utente viene autenticato. AD FS supporta anche la *federazione attiva*, in cui un'applicazione si assume la responsabilità di fornire le credenziali senza ulteriore intervento dell'utente, ma questo scenario non rientra nell'ambito di questa architettura.

Per altre considerazioni, vedere l'articolo su come [scegliere una soluzione per l'integrazione di Active Directory locale con Azure][considerations]. 

## <a name="architecture"></a>Architecture

Questa architettura estende l'implementazione descritta nell'articolo relativo all'[estensione di Active Directory Domain Services (AD DS) in Azure][extending-ad-to-azure]. Contiene i componenti seguenti.

* **Subnet di Active Directory Domain Services**. I server Active Directory Domain Services sono inclusi nella propria subnet con regole NSG (network security group) che fungono da firewall.

* **Server Active Directory Domain Services**. Controller di dominio in esecuzione come macchine virtuali in Azure. Questi server forniscono l'autenticazione delle identità locali all'interno del dominio.

* **Subnet di AD FS**. I server AD FS si trovano all'interno della propria subnet con regole NSG che fungono da firewall.

* **Server AD FS**. I server AD FS forniscono autenticazione e autorizzazione federate. In questa architettura eseguono le attività seguenti:
  
  * Ricevono i token di sicurezza contenenti attestazioni eseguite da un server federativo partner per conto di un utente partner. AD FS verifica che i token siano validi prima di passare le attestazioni all'applicazione Web in esecuzione in Azure per autorizzare le richieste. 
  
    L'applicazione Web in esecuzione in Azure è la *relying party*. Il server federativo partner deve rilasciare attestazioni riconosciute dall'applicazione Web. I server federativi partner sono detti *partner account*, perché inviano richieste di accesso per conto di account autenticati nell'organizzazione partner. Il server AD FS sono denominati *partner risorse*, in quanto forniscono l'accesso alle risorse (l'applicazione Web).

  * Autenticano e autorizzano le richieste in entrata degli utenti esterni che eseguono un Web browser o un dispositivo che richiede l'accesso alle applicazioni Web usando Active Directory Domain Services e [AD Device Registration Service] [ ADDRS].
    
  I server AD FS sono configurati come una farm a cui si accede tramite un servizio di bilanciamento del carico di Azure. Questa implementazione migliora la disponibilità e la scalabilità. I server AD FS non sono esposti direttamente a Internet. Tutto il traffico Internet viene filtrato attraverso i server proxy delle applicazioni Web AD FS e una DMZ (definita anche rete perimetrale).

  Per altre informazioni sul funzionamento di AD FS, vedere la panoramica su [Active Directory Federation Services][active-directory-federation-services-overview]. Inoltre, l'articolo [Distribuzione di Active Directory Federation Services in Azure][adfs-intro] contiene un'introduzione dettagliata all'implementazione.

* **Subnet proxy di AD FS**. I server proxy AD FS possono essere inclusi nelle proprie subnet, con regole NSG che ne garantiscono la protezione. I server di questa subnet sono esposti a Internet tramite un set di dispositivi virtuali di rete che forniscono un firewall tra la rete virtuale di Azure e Internet.

* **Server WAP (web application proxy) AD FS**. Queste macchine virtuali fungono da server AD FS per le richieste in ingresso di organizzazioni partner e dispositivi esterni. I server WAP fungono da filtro, proteggendo i server AD FS dall'accesso diretto da Internet. Come accade con i server AD FS, la distribuzione dei server WAP in una farm con il bilanciamento del carico garantisce maggiore disponibilità e scalabilità rispetto alla distribuzione di una raccolta di server autonomi.
  
  > [!NOTE]
  > Per informazioni dettagliate sull'installazione dei server WAP, vedere [Installare e configurare il server del proxy dell'applicazione Web][install_and_configure_the_web_application_proxy_server]
  > 
  > 

* **Organizzazione partner**. Un'organizzazione partner che esegue un'applicazione Web che richiede l'accesso a un'applicazione Web in esecuzione in Azure. Il server federativo nell'organizzazione partner autentica le richieste in locale e invia i token di sicurezza contenenti le attestazioni ad AD FS in esecuzione in Azure. AD FS in Azure convalida i token di sicurezza e, se sono validi, può trasferire le attestazioni all'applicazione Web in esecuzione in Azure per autorizzarle.
  
  > [!NOTE]
  > È inoltre possibile configurare un tunnel VPN tramite il gateway di Azure per consentire ai partner attendibili l'accesso diretto ad AD FS. Le richieste ricevute da tali partner non passano attraverso i server WAP.
  > 
  > 

Per altre informazioni sulle parti dell'architettura che non sono correlate ad AD FS, vedere gli argomenti seguenti:
- [Implementazione di un'architettura di rete ibrida protetta in Azure][implementing-a-secure-hybrid-network-architecture]
- [Implementazione di un'architettura di rete ibrida protetta con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]
- [Implementazione di un'architettura di rete ibrida protetta con identità di Active Directory in Azure][extending-ad-to-azure].


## <a name="recommendations"></a>Raccomandazioni

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda. 

### <a name="vm-recommendations"></a>Indicazioni per le VM

Creare macchine virtuali con risorse sufficienti per gestire il volume di traffico previsto. Usare le dimensioni delle macchine esistenti che ospitano AD FS in locale come punto di partenza. Monitorare l'uso delle risorse. È possibile ridimensionare le macchine virtuali e ridurle se sono troppo grandi.

Seguire le raccomandazioni elencate in [Eseguire una macchina virtuale (VM) Windows in Azure][vm-recommendations].

### <a name="networking-recommendations"></a>Raccomandazioni di rete

Configurare l'interfaccia di rete per tutte le macchine virtuali che ospitano i server AD FS e WAP con indirizzi IP privati statici.

Non assegnare indirizzi IP pubblici alle macchine virtuali AD FS. Per altre informazioni, vedere la sezione relativa alle considerazioni sulla sicurezza.

Impostare l'indirizzo IP dei server DNS (domain name service) preferito e secondario per le interfacce di rete per ogni macchina virtuale AD FS e WAP che faccia riferimento alle macchine virtuali Active Directory DS. Sulle macchine virtuali AD DS deve essere in esecuzione DNS. Questo passaggio è necessario per consentire di aggiunger ogni macchina virtuale al dominio.

### <a name="ad-fs-availability"></a>Disponibilità AD FS 

Creare una farm AD FS con almeno due server per aumentare la disponibilità del servizio. Usare account di archiviazione diversi per ogni VM AD FS nella farm. Questo approccio garantisce che un errore in un singolo account di archiviazione non renda inaccessibile l'intera farm.

> [!IMPORTANT]
> È consigliabile usare [dischi gestiti](/azure/storage/storage-managed-disks-overview). I dischi gestiti non richiedono un account di archiviazione. È sufficiente specificare le dimensioni e il tipo di disco per distribuirlo in modalità a disponibilità elevata. Le nostre [architetture di riferimento](/azure/architecture/reference-architectures/) attualmente non distribuiscono dischi gestiti ma i [blocchi predefiniti del modello](https://github.com/mspnp/template-building-blocks/wiki) verranno aggiornati per distribuire i dischi gestiti nella versione 2.

Creare set di disponibilità di Azure separati per le macchine virtuali WAP e AD FS. Verificare che in ogni set siano presenti almeno due macchine virtuali. Ogni set di disponibilità deve avere almeno due domini di aggiornamento e due domini di errore.

Configurare i servizi di bilanciamento del carico per le macchine virtuali AD FS e WAP come segue:

* Usare un bilanciamento del carico di Azure per fornire l'accesso esterno alle macchine virtuali WAP e un bilanciamento del carico interno per distribuire il carico tra i server AD FS nella farm.
* Passare solo il traffico presente sulla porta 443 (HTTPS) ai server AD FS/WAP.
* Assegnare un indirizzo IP statico al bilanciamento del carico.
* Creare un probe di integrità tramite il protocollo TCP anziché HTTPS. È possibile eseguire il ping della porta 443 per verificare il funzionamento di un server AD FS.
  
  > [!NOTE]
  > I server AD FS usano il protocollo SNI (Server Name Indication), pertanto il tentativo di eseguire il probe tramite un endpoint HTTPS dal bilanciamento del carico non riesce.
  > 
  > 
* Aggiungere un record DNS *A* al dominio per il bilanciamento del carico di AD FS. Specificare l'indirizzo IP del bilanciamento del carico e assegnargli un nome nel dominio (ad esempio adfs.contoso.com). Si tratta del nome usato dai client e dai server WAP per accedere alla farm di server AD FS.

### <a name="ad-fs-security"></a>Sicurezza di AD FS 

Evitare l'esposizione diretta dei server AD FS a Internet. I server AD FS sono computer appartenenti a un dominio con autorizzazione completa di concedere token di sicurezza. Se un server viene compromesso, un utente malintenzionato può rilasciare token di accesso completo a tutte le applicazioni Web e a tutti i server federativi che sono protetti da AD FS. Usare i server WAP se il sistema deve gestire richieste di utenti esterni che non si connettono da siti partner attendibili. Per altre informazioni, vedere [Dove posizionare un Proxy Server federativo][where-to-place-an-fs-proxy].

Posizionare i server AD FS e WAP in subnet separate con i propri firewall. È possibile usare le regole NSG per definire le regole del firewall. Se è necessaria una protezione più completa, è possibile implementare un perimetro di sicurezza aggiuntivo per i server con una coppia di subnet e appliance di rete virtuali (NVA), come descritto nel documento [Implementazione di un'architettura di rete ibrida protetta con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Tutti i firewall devono consentire il traffico sulla porta 443 (HTTPS).

Limitare l'accesso diretto ai server AD FS e WAP. Solo il personale DevOps deve essere in grado di connettersi.

Non aggiungere i server WAP al dominio.

### <a name="ad-fs-installation"></a>Installazione di AD FS 

L'articolo [Distribuzione di Active Directory Federation Services in Azure] [ Deploying_a_federation_server_farm] fornisce istruzioni dettagliate per l'installazione e la configurazione di AD FS. Prima di configurare il primo server AD FS nella farm, eseguire le attività seguenti:

1. Ottenere un certificato attendibile pubblicamente per eseguire l'autenticazione server. Il *nome soggetto* deve contenere il nome usato dai client per accedere al servizio federativo. Può trattarsi del nome DNS registrato per il bilanciamento del carico, ad esempio *adfs.contoso.com* (per motivi di sicurezza, evitare di usare nomi con caratteri jolly, come **.contoso.com*). Usare lo stesso certificato in tutte le macchine virtuali del server AD FS. È possibile acquistare un certificato da un'autorità di certificazione attendibile ma, se l'organizzazione usa Servizi certificati Active Directory, è possibile crearne uno personalizzato. 
   
    Il *nome alternativo del soggetto* viene usato dal servizio Registrazione dispositivi (DRS) per consentire l'accesso dai dispositivi esterni. Deve essere nel formato *enterpriseregistration.contoso.com*.
   
    Per altre informazioni, vedere [Ottenere e configurare un certificato SSL (Secure Sockets Layer) per AD FS][adfs_certificates].

2. Nel controller di dominio generare una nuova chiave radice per il servizio di distribuzione chiavi. Impostare il periodo di validità sull'ora corrente meno 10 ore (questa configurazione riduce il ritardo che può verificarsi durante la distribuzione e la sincronizzazione delle chiavi all'interno del dominio). Questo passaggio è necessario per supportare la creazione dell'account del servizio di gruppo usato per eseguire il servizio AD FS. Il comando PowerShell seguente illustra un esempio di come eseguire questa operazione:
   
    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. Aggiungere ogni macchina virtuale del server AD FS al dominio.

> [!NOTE]
> Per installare AD FS, il controller di dominio che esegue il ruolo FSMO (flexible single master operation) emulatore PDC (primary domain controller) per il dominio deve essere in esecuzione e accessibile dalle macchine virtuali AD FS. <<RBC: Is there a way to make this less repetitive?>>
> 
> 

### <a name="ad-fs-trust"></a>Trust AD FS 

Stabilire una relazione di trust federativa tra l'installazione di AD FS e i server federativi di tutte le organizzazioni partner. Configurare i filtri e il mapping di attestazioni richiesti. 

* Il personale DevOps di ogni organizzazione partner deve aggiungere un trust della relying party per le applicazioni Web accessibili attraverso i server AD FS.
* Il personale DevOps dell'organizzazione deve configurare il trust del provider di attestazioni per consentire ai server AD FS di considerare attendibili le attestazioni fornite dalle organizzazioni partner.
* Il personale DevOps dell'organizzazione deve inoltre configurare AD FS per passare le attestazioni alle applicazioni Web dell'organizzazione.
  
Per altre informazioni, vedere l'articolo su come [stabilire una relazione di trust federativa][establishing-federation-trust].

Pubblicare le applicazioni Web dell'organizzazione e renderle disponibili ai partner esterni con la preautenticazione tramite i server WAP. Per altre informazioni, vedere [Pubblicare applicazioni con la preautenticazione di ADFS][publish_applications_using_AD_FS_preauthentication]

AD FS supporta il potenziamento e la trasformazione dei token. Azure Active Directory non offre questa funzionalità. Con AD FS, quando si configurano le relazioni di trust, è possibile:

* Configurare le trasformazioni delle attestazioni per le regole di autorizzazione. È ad esempio possibile eseguire il mapping di sicurezza del gruppo da una rappresentazione usata da un'organizzazione partner non Microsoft a un elemento che può autorizzare il servizio di Directory Active Directory nell'organizzazione.
* Trasformare le attestazioni da un formato a un altro. È ad esempio possibile eseguire il mapping da SAML 2.0 a SAML 1.1 se l'applicazione supporta solo attestazioni SAML 1.1.

### <a name="ad-fs-monitoring"></a>Monitoraggio AD FS 

Il [Management Pack Microsoft System Center per Active Directory Federation Services 2012 R2][oms-adfs-pack] offre il monitoraggio sia proattivo sia reattivo della distribuzione AD FS per il server federativo. Questo management pack esegue il monitoraggio di:

* Eventi che il servizio AD FS registra nel log eventi.
* Dati sulle prestazioni raccolti dai contatori delle prestazioni di AD FS. 
* Integrità generale del sistema AD FS e delle applicazioni Web (relying party) e fornisce avvisi per problemi critici e avvertenze. 

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Le considerazioni seguenti, riepilogate dall'articolo [Pianificazione della distribuzione di ADFS][plan-your-adfs-deployment], offrono un punto di partenza per il ridimensionamento delle farm di AD FS:

* Se gli utenti sono meno di 1000, non creare un server dedicato, ma installare AD FS in tutti i server di Active Directory DS nel cloud. Assicurarsi di avere almeno due server di dominio Active Directory DS per gestire la disponibilità. Creare un singolo server WAP.
* Se il numero di utenti è compreso tra 1.000 e 15.000, creare due server AD FS dedicati e due server WAP dedicati.
* Se il numero di utenti è compreso tra 15.000 e 60.000, creare tra tre e cinque server AD FS dedicati e almeno due server WAP dedicati.

Queste considerazioni presuppongono che si usi una macchina virtuale dual core/quad core (D4_v2 standard o versione successiva) in Azure.

Se si usa il database interno di Windows per archiviare i dati di configurazione di AD FS, i server AD FS della farm non possono essere più di otto. Se si prevede che in futuro ne servirà un numero maggiore, usare SQL Server. Per altre informazioni, vedere [Ruolo del database di configurazione di AD FS][adfs-configuration-database].

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

È possibile usare SQL Server o il database interno di Windows per memorizzare le informazioni di configurazione di AD FS. Il database interno di Windows fornisce la ridondanza di base. Le modifiche vengono scritte direttamente in uno solo dei database di AD FS nel cluster AD FS, mentre gli altri server usano la replica pull per mantenere aggiornati i database. L'uso di SQL Server consente di ottenere le ridondanza completa del database e disponibilità elevata tramite il clustering di failover o il mirroring.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Il personale DevOps deve essere preparato per eseguire le attività seguenti:

* Gestire i server federativi, inclusa la gestione della farm di AD FS, la gestione dei criteri di attendibilità su server federativi e la gestione dei certificati usati dai servizi di federazione.
* Gestire i server WAP, inclusa la gestione della farm WAP e dei certificati.
* Gestire le applicazioni Web, inclusa la configurazione delle relying party, dei metodi di autenticazione e dei mapping di attestazioni.
* Eseguire l backup dei componenti di AD FS.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

AD FS usa il protocollo HTTPS, pertanto verificare che le regole NSG per la subnet contenente le macchine virtuali del livello Web consentano le richieste HTTPS. Queste richieste possono avere origine dalla rete locale, dalle subnet contenenti il livello Web, dal livello business, dal livello dati, dalla DMZ privata, dalla DMZ pubblica e dalla subnet contenente i server AD FS.

È consigliabile usare un set di appliance virtuali di rete che registra informazioni dettagliate sul traffico che attraversa il perimetro della rete virtuale a fini di controllo.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Una soluzione per la distribuzione di questa architettura di riferimento è disponibile in [GitHub][github]. Per eseguire lo script di PowerShell che distribuisce la soluzione, è necessaria l'ultima versione dell'[interfaccia della riga di comando di Azure][azure-cli]. Per distribuire l'architettura di riferimento, eseguire la procedura seguente:

1. Scaricare o clonare la cartella della soluzione da [GitHub][github] al computer locale.

2. Aprire l'interfaccia della riga di comando di Azure e passare alla cartella della soluzione locale.

3. Eseguire il comando seguente:
   
    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```
   
    Sostituire `<subscription id>` con l'ID della sottoscrizione di Azure.
   
    Per `<location>`, specificare un'area di Azure, come `eastus` o `westus`.
   
    Il parametro `<mode>` controlla la granularità della distribuzione e può essere uno dei valori seguenti:
   
   * `Onpremise`: distribuisce l'ambiente locale simulato. È possibile usare questa distribuzione per testare e sperimentare, se non si dispone di una rete locale esistente o se si desidera testare questa architettura di riferimento senza modificare la configurazione della rete esistente locale.
   * `Infrastructure`: distribuisce l'infrastruttura di rete virtuale e il jumpbox.
   * `CreateVpn`: distribuisce un gateway di rete virtuale di Azure e lo connette alla rete locale simulata.
   * `AzureADDS`: distribuisce le macchine virtuali che fungono da server Active Directory Domain Services, distribuisce Active Directory in queste macchine virtuali e crea il dominio in Azure.
   * `AdfsVm`: distribuisce le macchine virtuali di AD FS e le aggiunge al dominio in Azure.
   * `PublicDMZ`: distribuisce la DMZ pubblica in Azure.
   * `ProxyVm`: distribuisce le macchine virtuali proxy di AD FS e le aggiunge al dominio in Azure.
   * `Prepare`: distribuisce tutte le distribuzioni precedenti. **Si tratta dell'opzione consigliata se si sta creando una distribuzione completamente nuova e non si dispone di un'infrastruttura locale esistente.** 
   * `Workload`: facoltativamente, distribuisce le macchine virtuali di livello Web, business e dati e la rete di supporto. Non disponibile nella modalità di distribuzione `Prepare`.
   * `PrivateDMZ`: distribuisce facoltativamente la rete Perimetrale privata in Azure di fronte alle macchine virtuali `Workload` distribuite in precedenza. Non disponibile nella modalità di distribuzione `Prepare`.

4. Attendere il completamento della distribuzione. Se è stata usata l'opzione `Prepare`, per il completamento della distribuzione sono richieste diverse ore e al termine viene visualizzato il messaggio `Preparation is completed. Please install certificate to all AD FS and proxy VMs.`

5. Riavviare il jumpbox (*ra-adfs-mgmt-vm1* nel gruppo *ra-adfs-security-rg* group) per rendere effettive le rispettive impostazioni DNS.

6. [Ottenere un certificato SSL per AD FS] [ adfs_certificates] e installarlo nelle macchine virtuali AD FS. Si noti che è possibile connettersi tramite il jumpbox. Gli indirizzi IP sono <em>10.0.5.4</em> e <em>10.0.5.5</em>. Il nome utente predefinito è <em>contoso\testuser</em> e la password è <em>AweSome@PW</em>.
   
   > [!NOTE]
   > A questo punto, i commenti nello script Deploy-ReferenceArchitecture.ps1 includono le istruzioni dettagliate per la creazione di un'autorità di certificazione di prova autofirmata con il comando `makecert`. È tuttavia consigliabile eseguire questi passaggi solo come **test** e non usare i certificati generati da makecert in un ambiente di produzione.
   > 
   > 

7. Eseguire il comando PowerShell seguente per distribuire la farm di server AD FS:
   
    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ``` 

8. Nel jumpbox passare a `https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm` per testare l'installazione di AD FS (potrebbe essere visualizzato un avviso certificato che è possibile ignorare per questo test). Verificare che venga visualizzata la pagina di accesso di Contoso Corporation. Accedere con nome utente <em>contoso\testuser</em> e password <em>AweS0me@PW</em>.

9. Installare il certificato SSL nelle macchine virtuali proxy AD FS. Gli indirizzi IP sono *10.0.6.4* e *10.0.6.5*.

10. Eseguire il comando PowerShell seguente per distribuire il primo server proxy AD FS:
   
    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

11. Seguire le istruzioni visualizzate nello script per testare l'installazione del primo server proxy.

12. Eseguire il comando PowerShell seguente per distribuire il secondo server proxy:
    
    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

13. Seguire le istruzioni visualizzate nello script per testare la configurazione proxy completa.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [Azure Active Directory][aad].
* Informazioni su [Azure Active Directory B2C][aadb2c].

<!-- links -->
[extending-ad-to-azure]: adds-extend-domain.md

[vm-recommendations]: ../virtual-machines-windows/single-vm.md
[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md
[hybrid-azure-on-prem-vpn]: ../hybrid-networking/vpn.md

[azure-cli]: /azure/azure-resource-manager/xplat-cli-azure-resource-manager
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[adfs-configuration-database]: https://technet.microsoft.com/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[active-directory-federation-services-overview]: https://technet.microsoft.com/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]:  https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-azure-adfs/
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: /azure/active-directory/active-directory-aadconnect-azure-adfs
[github]: https://github.com/mspnp/reference-architectures/tree/master/identity/adfs
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[considerations]: ./considerations.md
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx
[0]: ./images/adfs.png "Architettura di rete ibrida protetta con Active Directory"
