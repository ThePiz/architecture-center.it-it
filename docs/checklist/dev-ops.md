# <a name="devops-checklist"></a>Elenco di controllo DevOps

DevOps rappresenta l'integrazione di sviluppo, controllo di qualità e operazioni IT in un set di processi e impostazioni cultura unificati per la distribuzione del software. 

Usare questo elenco di controllo come punto di partenza per valutare le impostazioni cultura e il processo DevOps.

## <a name="culture"></a>Impostazioni cultura

**Verificare l'allineamento di business tra organizzazioni e team.** I conflitti tra risorse, scopo, obiettivi e priorità all'interno di un'organizzazione possono compromettere la riuscita delle operazioni. Verificare che i team responsabili di business, sviluppo e operazioni siano allineati.

**Verificare che il team intero comprenda il ciclo di vita del software.** Il team deve comprendere il ciclo di vita complessivo dell'applicazione e la parte del ciclo in cui si trova al momento. In questo modo tutti i membri del team sapranno quali attività svolgere e quali programmare e predisporre per il futuro.

**Ridurre la durata del ciclo.** Puntare a ridurre al minimo il tempo richiesto per tradurre le idee in software sviluppato fruibile. Limitare le dimensioni e l'ambito delle singole versioni per mantenere ridotto il carico di test. Automatizzare i processi di compilazione, test, configurazione e distribuzione laddove possibile. Rimuovere eventuali ostacoli alle comunicazioni tra sviluppatori e tra gli sviluppatori e le operazioni. 

**Esaminare e migliorare i processi.** I processi e le procedure, automatici e manuali, non sono mai finali. Programmare revisioni periodiche dei flussi di lavoro, delle procedure e della documentazione correnti, puntando a un miglioramento costante.

**Eseguire una pianificazione proattiva.** Pianificare in modo proattivo per identificare gli errori. Predisporre processi per identificare rapidamente i problemi quando si verificano, eseguire l'escalation ai membri del team idoneo per le correzioni e confermare la risoluzione.

**Apprendere dagli errori.** Gli errori sono inevitabili, ma è importante riconoscerli per evitare di ripeterli. Se si verifica un errore operativo, valutare il problema, documentarne causa e soluzione e condividere eventuali informazioni utili apprese. Quando possibile, aggiornare i processi di compilazione per il rilevamento automatico del tipo di errore in questione nel futuro.

**Ottimizzare la velocità e raccogliere i dati.** Ogni miglioramento pianificato è ipotetico. Procedere sempre con incrementi ridotti il più possibile. Considerare le nuove idee come esperimenti. Instrumentare gli esperimenti in modo da poter raccogliere i dati di produzione per valutarne l'efficacia. Essere preparati a esiti negativi rapidi se l'ipotesi non è corretta.

**Consentire tempo per l'apprendimento.** Successi e fallimenti offrono entrambi ottime opportunità di apprendimento. Prima di passare a nuovi progetti, consentire tempo a sufficienza per acquisire le nozioni più importanti e assicurarsi che vengano apprese dal team. Concedere al team anche il tempo di acquisire competenze, sperimentare e conoscere nuovi strumenti e tecniche. 

**Operazioni sui documenti.** Documentare tutti gli strumenti, i processi e le attività automatizzate con lo stesso livello di qualità del codice del prodotto. Documentare la struttura e l'architettura correnti di tutti i sistemi supportati, insieme ai processi di recupero e ad altre procedure di manutenzione. Concentrarsi sui passaggi effettivamente eseguiti, non su processi teoricamente ottimali. Rivedere e aggiornare periodicamente la documentazione. Per il codice, verificare che siano inclusi commenti significativi, in particolare nelle API pubbliche, e usare strumenti per generare automaticamente la documentazione del codice quando possibile. 

**Condivisione delle conoscenze.** La documentazione è utile solo se altri ne sono a conoscenza e possono attingervi. Accertarsi che la documentazione sia organizzata e facilmente individuabile. La creatività è importante: pranzi durante presentazioni informali, video o newsletter sono strumenti ideali per condividere le conoscenze.

## <a name="development"></a>Sviluppo.

**Fornire agli sviluppatori ambienti di simil-produzione.** Se gli ambienti di test e sviluppo non riflettono l'ambiente di produzione, è difficile testare e diagnosticare i problemi. Pertanto, mantenere gli ambienti di sviluppo e test il più vicino possibile all'ambiente di produzione. Verificare che i dati di test siano coerenti con i dati usati in produzione, anche se si tratta di dati di esempio e non reali (per motivi di privacy o conformità). Pianificare di generare e anonimizzare i dati di test di esempio.

**Verificare che tutti i membri del team autorizzati possano eseguire il provisioning dell'infrastruttura e distribuire l'applicazione.** La configurazione di risorse di simil-produzione e la distribuzione dell'applicazione non devono implicare attività manuali complesse o una conoscenza tecnica dettagliata del sistema. Chiunque abbia le autorizzazioni appropriate deve essere in grado di creare o distribuire risorse di simil-produzione senza l'intervento del team addetto alle operazioni. 

> Questa indicazione non sottintende che chiunque possa eseguire il push di aggiornamenti in tempo reale alla distribuzione di produzione. Si tratta di ridurre l'attrito per i team di sviluppo e controllo qualità per creare ambienti di simil-produzione.

**Instrumentare l'applicazione per informazioni dettagliate.** Per comprendere l'integrità dell'applicazione, è necessario conoscerne le prestazioni e sapere se si verificano errori o problemi al suo interno. Includere sempre la strumentazione come requisito di progettazione e integrarla fin dall'inizio nell'applicazione. La strumentazione deve includere la registrazione degli eventi per l'analisi delle cause principali, ma anche dati di telemetria e metriche per monitorare l'integrità generale e l'uso dell'applicazione.

**Tenere traccia del debito tecnico.** In molti progetti le pianificazioni delle versioni sono spesso prioritarie rispetto alla qualità del codice a un livello o un altro. Tenere sempre traccia di queste situazioni, quando si verificano. Documentare eventuali scorciatoie o altre implementazioni non ottimali e pianificare del tempo in futuro per rivedere questi problemi.

**Valutare l'eventualità di eseguire il push degli aggiornamenti direttamente nell'ambiente di produzione.** Per ridurre il tempo del ciclo di rilascio complessivo, valutare l'eventualità di eseguire il push di commit di codice precedentemente testati direttamente nell'ambiente di produzione. Usare [feature toggle][feature-toggles] per controllare le funzionalità abilitate. In questo modo è possibile passare rapidamente dallo sviluppo al rilascio, usando i toggle per abilitare o disabilitare le funzionalità. Questi toggle sono utili anche in fase di test di [versioni canary][canary-release], in cui una determinata funzionalità viene distribuita in un subset dell'ambiente di produzione.

## <a name="testing"></a>Test

**Automatizzare i test.** Il test manuale del software è un'attività tediosa e soggetta a errori. Automatizzare attività di test comuni e integrare i test nei processi di compilazione. I test automatizzati garantiscono coerenza a livello di copertura e riproducibilità. Anche i test integrati dell'interfaccia utente dovrebbero essere eseguiti da uno strumento automatico. Azure offre risorse di sviluppo e test che consentono di configurare ed eseguire i test. Per altre informazioni, vedere [Sviluppo e test][dev-test].

**Test per l'individuazione degli errori.** Se un sistema non riesce a connettersi a un servizio, come risponde? Può eseguire il recupero dopo che il servizio sarà tornato disponibile? Rendere i test di fault injection parte integrante della revisione negli ambienti di test e gestione temporanea. Quando il processo e le procedure sono collaudati, prendere in considerazione l'eventualità di eseguire questi test in produzione. 

**Eseguire i test in produzione** Il processo di rilascio non termina con la distribuzione nell'ambiente di produzione. Predisporre una serie di test per garantire che il codice distribuito funzioni come previsto. Per le distribuzioni aggiornate di rado, pianificare test di produzione nell'ambito della manutenzione standard.

**Automatizzare i test delle prestazioni per identificare in anticipo problemi di prestazioni.** L'impatto di un problema importante nelle prestazioni può essere grave tanto quanto un bug nel codice. Sebbene i test funzionali automatizzati possano evitare bug nelle applicazioni, potrebbero non essere in grado di rilevare problemi di prestazioni. Definire obiettivi di prestazioni accettabili per metriche quali latenza, tempi di caricamento e uso delle risorse. Includere test automatizzati delle prestazioni nella pipeline di versione, per verificare che l'applicazione soddisfi questi obiettivi.

**Eseguire test di capacità.** Un'applicazione potrebbe funzionare normalmente in condizioni di test, ma riscontrare problemi in produzione per via di limitazioni in termini di scala o risorse. Definire sempre i limiti massimi di capacità e uso previsti. Testare l'applicazione per accertarsi che possa gestire questi limiti, ma anche verificare cosa accade quando questi limiti vengono superati. È opportuno eseguire test di capacità a intervalli regolari.

Dopo il rilascio iniziale, è consigliabile eseguire test delle prestazioni e della capacità ogni volta che vengono applicati aggiornamenti al codice di produzione. Usare i dati cronologici per ottimizzare i test e determinare quali tipi di test devono essere eseguiti.

**Eseguire test di penetrazione di sicurezza automatizzati.** Verificare che l'applicazione sia protetta è importante come testare qualsiasi altra funzionalità. Rendere i test di penetrazione automatizzati parte integrante del processo di compilazione e distribuzione. Pianificare test periodici di sicurezza e analisi delle vulnerabilità sulle applicazioni distribuite, monitorando eventuali porte aperte, endpoint e attacchi. I test automatizzati non rimuovono l'esigenza di analisi di sicurezza dettagliate a intervalli regolari.

**Eseguire test automatizzati di continuità aziendale.** Sviluppare test per la continuità aziendale su larga scala, inclusi failover e ripristino da backup. Configurare processi automatizzati per eseguire questi test periodicamente.

## <a name="release"></a>Versione

**Automatizzare le distribuzioni.** Automatizzare la distribuzione dell'applicazione in ambienti di test, gestione temporanea e produzione. L'automazione garantisce distribuzioni più veloci, affidabili e coerenti in qualsiasi ambiente supportato. Rimuove il rischio di errori umani causato dalle distribuzioni manuale. Semplifica anche la pianificazione di rilasci in periodi convenienti, per ridurre al minimo gli effetti di potenziali tempi di inattività.

**Usare l'integrazione continua.** L'integrazione continua è la procedura che consiste nell'unire il codice di tutti gli sviluppatori in una base di codici centrale a intervalli regolari e poi nell'eseguire automaticamente processi standard di test e compilazione. Permette a un intero team di lavorare contemporaneamente su una base di codici senza conflitti. Garantisce anche l'individuazione preventiva di difetti nel codice. È preferibile eseguire l'integrazione continua a ogni commit o archiviazione del codice. È consigliabile eseguirla almeno una volta al giorno.

> Valutare la possibilità di adottare un [modello di sviluppo basato su trunk][trunk-based]. In questo modello gli sviluppatori eseguono il commit a un singolo ramo (trunk). I commit non devono interrompere mai la compilazione. Questo modello agevola l'integrazione continua, poiché tutte le funzionalità vengono eseguite nel trunk ed eventuali conflitti di unione vengono risolti in fase di commit.

**Valutare la possibilità di usare il recapito continuo.** Il recapito continuo è la procedura che permette di garantire che il codice sia sempre pronto per la distribuzione mediante compilazione, test e distribuzione automatici del codice in ambienti di simil-produzione. L'aggiunta di recapito continuo per creare una pipeline completa di integrazione e recapito continui consente di rilevare preventivamente i difetti nel codice e garantisce il rilascio di aggiornamenti testati in modo appropriato in breve tempo.

> La *distribuzione* continua è un processo aggiuntivo che accetta automaticamente eventuali aggiornamenti che hanno superato la pipeline di integrazione e recapito continui e li distribuisce in produzione. La distribuzione continua richiede test automatici affidabili e una pianificazione avanzata dei processi e potrebbe non essere indicata per tutti i team.

**Apportare minime modifiche incrementali.** Modifiche significative al codice possono contribuire maggiormente all'introduzione di bug. Quando possibile, limitare l'entità delle modifiche. In questo modo si limitano anche i potenziali effetti di ogni modifica ed è più semplice comprendere ed eseguire il debug di eventuali problemi.

**Controllare l'esposizione a modifiche.** È importante essere sempre consapevoli delle situazioni in cui gli aggiornamenti sono visibili agli utenti finali. È consigliabile usare i feature toggle per controllare i casi in cui le funzionalità sono abilitate per gli utenti finali.

**Implementare strategie di gestione dei rilasci per ridurre il rischio di distribuzione.** La distribuzione degli aggiornamenti di un'applicazione in produzione comporta sempre dei rischi. Per ridurre al minimo questi rischi, adottare strategie come ad esempio le [versioni canary][ canary-release] o le [distribuzioni di tipo blu-verde][blue-green] per distribuire gli aggiornamenti per un subset di utenti. Verificare che l'aggiornamento funzioni come previsto e distribuirlo nel resto del sistema.

**Documentare tutte le modifiche.** Aggiornamenti e modifiche di configurazione di entità minore possono generare confusione e conflitti di versioni. Tenere sempre traccia di tutte le modifiche, indipendentemente dalla loro entità. Registrare qualsiasi modifica, incluse le patch applicate e le modifiche ai criteri e alle configurazioni. Non includere dati sensibili in questi registri. Ad esempio, registrare l'aggiornamento di credenziali e l'autore della modifica, ma non le credenziali aggiornate. Il record delle modifiche deve essere visibile all'intero team. 

**Automatizzare le distribuzioni.** Automatizzare tutte le distribuzioni e predisporre i sistemi in modo che possano rilevare eventuali problemi durante l'implementazione. Adottare un processo di mitigazione dei rischi per preservare il codice esistente e i dati nell'ambiente di produzione, prima che l'aggiornamento li sostituisca in tutte le istanze di produzione. Disporre di una procedura automatica per eseguire il roll forward delle correzioni o il rollback delle modifiche.

**Valutare la possibilità di rendere l'infrastruttura non modificabile.** L'infrastruttura non modificabile è il principio secondo cui non si dovrebbe modificare l'infrastruttura dopo la distribuzione nell'ambiente di produzione, altrimenti ci si può trovare in uno stato in cui sono state applicate modifiche ad hoc, ed è difficile sapere esattamente cosa sia cambiato. Un'infrastruttura non modificabile opera attraverso la sostituzione di interi server nell'ambito di qualsiasi nuova distribuzione. In questo modo è possibile testare e distribuire in blocco il codice e l'ambiente di hosting. Dopo la distribuzione, i componenti dell'infrastruttura non verranno più modificati fino al ciclo successivo di compilazione e distribuzione. 

## <a name="monitoring"></a>Monitoraggio

**Rendere i sistemi osservabili.** Il team addetto alle operazioni deve sempre avere massima visibilità sull'integrità e sullo stato di un sistema o un servizio. Configurare endpoint di integrità esterni per monitorare lo stato e verificare che le applicazioni siano codificate per instrumentare le metriche delle operazioni. Usare uno schema comune e coerente che consenta di correlare gli eventi tra i sistemi. [Diagnostica di Azure][azure-diagnostics] e [Application Insights][ app-insights] rappresentano i metodi standard per rilevare l'integrità e lo stato delle risorse di Azure. Anche Microsoft [Operations Management Suite] [oms] offre monitoraggio e gestione centralizzati per soluzioni cloud o ibride.

**Aggregare e correlare i registri e le metriche**. Un sistema di telemetria correttamente instrumentato garantisce una grande quantità di dati delle prestazioni non elaborati e log eventi. Verificare che i dati di telemetria e log vengano elaborati e correlati in un breve periodo di tempo, in modo che il personale addetto alle operazioni abbia sempre un quadro aggiornato dell'integrità del sistema. Organizzare e visualizzare i dati in modi che offrano una visuale uniforme di eventuali problemi, in modo che, quando possibile, la correlazione tra gli eventi sia chiaramente deducibile.

> Consultare i criteri di conservazione aziendali per conoscere i requisiti relativi alla modalità di elaborazione dei dati e alla durata di archiviazione. 

**Implementare notifiche e avvisi automatizzati.** Configurare strumenti di monitoraggio come [Monitoraggio di Azure][azure-monitor] per rilevare modelli e condizioni che indichino problemi potenziali o esistenti e inviare avvisi ai membri del team in grado di risolvere questi problemi. Ottimizzare gli avvisi per evitare falsi positivi.

**Monitorare la scadenza di asset e risorse.** Alcuni asset e risorse, ad esempio i certificati, scadono dopo un determinato periodo di tempo. Verificare di tenere traccia degli asset soggetti a scadenza, della loro data e dei servizi o delle funzionalità dipendenti da questi. Usare processi automatizzati per monitorare questi asset. Inviare una notifica al team addetto alle operazioni prima della scadenza di un asset ed eseguire l'escalation se una scadenza rischia di interrompere l'applicazione.

## <a name="management"></a>gestione

**Automatizzare le attività operative.** La gestione manuale di processi operativi ripetitivi è soggetta a errori. Automatizzare queste attività laddove possibile per garantire coerenza nell'esecuzione e nella qualità. È consigliabile sottoporre al controllo delle versioni il codice che implementa l'automazione nel controllo del codice sorgente. Come per qualsiasi altro codice, è necessario testare anche gli strumenti di automazione.

**Adottare un approccio di infrastruttura come codice al provisioning.** Ridurre al minimo la quantità di configurazione manuale necessaria per il provisioning delle risorse. In alternativa, usare script e modelli di [Azure Resource Manager][resource-manager]. Mantenere gli script e i modelli nel controllo del codice sorgente, come per qualsiasi altro codice gestito. 

**Valutare la possibilità di usare contenitori.** I contenitori presentano un'interfaccia standard basata su pacchetti per la distribuzione di applicazioni. Con i contenitori, un'applicazione viene distribuita usando pacchetti autonomi che includono eventuali software, dipendenze e file necessari per eseguire l'applicazione, semplificando notevolmente il processo di distribuzione. 

I contenitori creano anche un livello di astrazione tra l'applicazione e il sistema operativo sottostante, garantendo coerenza tra gli ambienti. Questa astrazione può anche isolare un contenitore da altri processi o applicazioni in esecuzione in un host. 

**Implementare resilienza e riparazione automatica.** La resilienza è la capacità di recupero da errori di un'applicazione. Alcune strategie per la resilienza includono nuovi tentativi di risoluzione degli errori temporanei e il failover a un'istanza secondaria o addirittura un'altra regione. Per altre informazioni, vedere [Progettazione di applicazioni resilienti per Azure][resiliency]. Instrumentare le applicazioni in modo che i problemi vengano segnalati immediatamente per gestire interruzioni o altri errori di sistema.

**Predisporre un manuale operativo.** Un manuale operativo o *runbook* documenta le procedure e le informazioni di gestione necessarie per la gestione di un sistema da parte del personale addetto alle operazioni. Documentare anche eventuali scenari operativi e piani di mitigazione dei rischi che potrebbero entrare in caso di errore o altre interruzioni del servizio. Creare questa documentazione durante il processo di sviluppo e mantenerla sempre aggiornata. Si tratta di un documento in evoluzione che deve essere periodicamente esaminato, testato e migliorato. 

È essenziale condividere la documentazione. Incoraggiare i membri del team a contribuire alle informazioni e condividerle. L'intero team deve poter accedere ai documenti. Semplificare per tutti i membri del team la capacità di aggiornare i documenti.

**Documentare le procedure su richiesta.** Verificare che compiti, pianificazioni e procedure su richiesta siano documentati e condivisi con tutti i membri del team. Mantenere queste informazioni sempre aggiornate.

**Documentare le procedure di escalation per le dipendenze di terze parti.** Se un'applicazione dipende da servizi di terze parti esterne non direttamente controllate, è necessario predisporre un piano per far fronte alle interruzioni. Creare la documentazione per i processi di mitigazione dei rischi pianificati. Includere contatti di supporto tecnico e percorsi di escalation.

**Usare la gestione della configurazione.** È consigliabile pianificare, registrare e rendere visibili al personale addetto alle operazioni le modifiche di configurazione. A tale scopo è possibile usare un database di gestione o adottare un approccio di configurazione come codice. La configurazione deve essere periodicamente controllata per verificare che funzioni come previsto.

**Ottenere un piano di supporto di Azure e comprendere il processo.** Azure offre una serie di [piani di supporto][azure-support-plans]. Valutare il piano più adatto alle specifiche esigenze e verificare che l'intero team sia in grado di usarlo. I membri del team devono comprendere i dettagli del piano, il processo di supporto e la procedura per aprire un ticket di supporto con Azure. Se è previsto un evento su vasta scala, il supporto di Azure può offrire assistenza per aumentare i limiti del servizio. Per altre informazioni, vedere le [domande frequenti sul supporto di Azure](https://azure.microsoft.com/en-us/support/faq/).

**Seguire i principi dei privilegi minimi in fase di concessione dell'accesso alle risorse.** Gestire con cautela l'accesso alle risorse. L'accesso deve essere negato per impostazione predefinita, a meno che a un utente non venga concesso in modo esplicito di accedere a una risorsa. Concedere l'accesso utente solo in misura necessaria a completare le attività. Tenere traccia delle autorizzazioni utente ed eseguire controlli periodici di sicurezza.

**Usare il controllo degli accessi in base al ruolo.** L'assegnazione di account utente e accessi alle risorse non deve essere un processo manuale. Usare il [controllo degli accessi in base al ruolo] [rbac] per concedere l'accesso in base a identità e gruppi di [Azure Active Directory][azure-ad]. 

**Usare un sistema di rilevamento dei bug per tenere traccia dei problemi.** Senza una procedura efficace per tenere traccia dei problemi, è facile perdere elementi, duplicare il lavoro o introdurre altri problemi. Non basarsi sulle comunicazioni a voce tra gli utenti per tenere traccia dello stato dei bug. Usare un apposito strumento per registrare dettagli sui problemi, assegnare le risorse in grado di risolverli e fornire un audit trail sull'avanzamento e sullo stato. 

**Gestire tutte le risorse in un sistema di gestione delle modifiche.** Tutti gli aspetti di un processo DevOps devono essere inclusi in un sistema di gestione e controllo delle versioni, in modo da semplificare il monitoraggio e il controllo delle modifiche. Gli aspetti includono codice, infrastruttura, configurazione, documentazione e script. Considerare tutti questi tipi di risorse come codice durante il processo di test/compilazione/revisione. 

**Usare elenchi di controllo.** Creare elenchi di controllo delle operazioni per garantire l'aderenza ai processi. In un manuale di grandi dimensioni può capitare di perdere alcuni elementi: un elenco di controllo permette di concentrarsi su dettagli altrimenti trascurabili. Gestire gli elenchi di controllo e individuare sempre nuovi modi per automatizzare le attività e semplificare i processi.

Per altre informazioni su DevOps, vedere l'[introduzione a DevOps][what-is-devops] nel sito di Visual Studio.

<!-- links -->

[app-insights]: /azure/application-insights/
[azure-ad]: https://azure.microsoft.com/services/active-directory/
[azure-diagnostics]: /azure/monitoring-and-diagnostics/azure-diagnostics
[azure-monitor]: /azure/monitoring-and-diagnostics/monitoring-overview
[azure-support-plans]: https://azure.microsoft.com/support/plans/
[blue-green]: https://martinfowler.com/bliki/BlueGreenDeployment.html
[canary-release]:https://martinfowler.com/bliki/CanaryRelease.html
[dev-test]: https://azure.microsoft.com/solutions/dev-test/
[feature-toggles]: https://www.martinfowler.com/articles/feature-toggles.html
[oms]: https://www.microsoft.com/cloud-platform/operations-management-suite
[rbac]: /azure/active-directory/role-based-access-control-what-is
[resiliency]: ../resiliency/index.md
[resource-manager]: /azure/azure-resource-manager/
[trunk-based]: https://trunkbaseddevelopment.com/
[what-is-devops]: https://www.visualstudio.com/learn/what-is-devops/
