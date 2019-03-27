<!-- TEMPLATE FILE - DO NOT ADD METADATA -->

## <a name="dependent-decisions"></a>Decisioni dipendenti

Le decisioni seguenti sono state prese da team esterni al team di governance del cloud, che si occuperanno anche della loro implementazione. Il team di governance del cloud, tuttavia, è responsabile dell'implementazione di una soluzione che consenta di verificare che queste implementazioni siano state applicate in modo coerente.

### <a name="identity-baseline"></a>Baseline di identità

La baseline di identità è un requisito iniziale indispensabile per tutte le azioni di governance. Prima di tentare di applicare la governance, è infatti necessario definire l'identità. La strategia di identità definita verrà quindi applicata dalle soluzioni di governance.
In questo percorso di governance, il team di gestione delle identità implementa il modello **Sincronizzazione delle directory**:

- Il controllo degli accessi in base al ruolo verrà fornito da Azure Active Directory (Azure AD) tramite la sincronizzazione delle directory o l'approccio "Same Sign-On" implementato dall'azienda durante la migrazione a Office 365. Per altre informazioni sull'implementazione, vedere [Architettura di riferimento per l'integrazione di Azure AD](/azure/architecture/reference-architectures/identity/azure-ad).
- Il tenant di Azure AD controllerà anche l'autenticazione e l'accesso degli asset distribuiti in Azure.

Nell'MVP per la governance, il team di governance imporrà l'applicazione del tenant replicato tramite strumenti di governance delle sottoscrizioni, descritti [più avanti in questo articolo](#subscription-model). In evoluzioni future, il team di governance potrà anche applicare strumenti avanzati in Azure AD per estendere questa funzionalità.

### <a name="security-baseline-networking"></a>Baseline di sicurezza: Rete

La rete definita dal software è un aspetto iniziale importante della baseline di sicurezza. La definizione dell'MVP di governance dipende dalle decisioni iniziali del team di gestione della sicurezza, che stabilisce le modalità di configurazione sicura delle reti.

Data la mancanza di requisiti, il team di sicurezza IT non corre rischi e richiede un modello di **rete perimetrale nel cloud**. La governance delle distribuzioni Azure risulterà quindi poco intensa.

- Le sottoscrizioni di Azure possono connettersi a un data center esistente tramite VPN, ma devono rispettare tutti i criteri di governance IT locale esistenti in merito alla connessione di una rete perimetrale a risorse protette. Per altre informazioni sull'implementazione relative alla connettività VPN, vedere [Architettura di riferimento di una rete VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn).
- Le decisioni relative alla subnet, al firewall e al routing vengono affidate al responsabile di ogni applicazione/carico di lavoro.
- È necessario eseguire ulteriori analisi prima del rilascio di eventuali dati protetti o carichi di lavoro cruciali.

In questo modello, le reti cloud possono connettersi solo a risorse locali su un rete VPN pre-allocata compatibile con Azure. Il traffico della connessione verrà considerato come qualsiasi altro tipo di traffico proveniente da una rete perimetrale. Per gestire in sicurezza il traffico proveniente da Azure è possibile che siano necessarie considerazioni aggiuntive sul dispositivo perimetrale locale.

Il team di governance del cloud invita i membri dei team di rete e di sicurezza IT a riunioni periodiche per poter anticipare la domanda e i rischi delle connessioni di rete.

### <a name="security-baseline-encryption"></a>Baseline di sicurezza: Crittografia

La crittografia è un'altra decisione fondamentale relativa alla disciplina sulla baseline di sicurezza. Considerando che l'azienda non archivia ancora dati protetti nel cloud, il team di sicurezza ha optato per un modello di crittografia meno aggressivo.
A questo punto, da qualsiasi team di sviluppo viene suggerito, ma non richiesto obbligatoriamente, un modello di crittografia **ibrido**.

- In merito all'uso della crittografia, non sono stati definiti requisiti di governance perché i criteri aziendali correnti non consentono l'archiviazione nel cloud di dati protetti o cruciali.
- Sarà necessario eseguire altre analisi prima del rilascio di eventuali dati protetti o carichi di lavoro cruciali.

## <a name="policy-enforcement"></a>Imposizione dei criteri

La prima decisione da prendere in merito all'accelerazione della distribuzione riguarda il modello di applicazione. In questo testo descrittivo, il team di governance ha deciso di implementare il modello di **applicazione automatizzata**.

- I team di sicurezza e identità potranno usufruire del Centro sicurezza di Azure per monitorare i rischi di sicurezza. Con molta probabilità, entrambi i team ricorreranno al Centro sicurezza anche per identificare nuovi rischi e sviluppare i criteri aziendali.
- In tutte le sottoscrizioni è obbligatorio il controllo degli accessi in base al ruolo per controllare l'applicazione dell'autenticazione.
- In ogni gruppo verrà inoltre pubblicato Criteri di Azure, che verrà applicato a tutte le sottoscrizioni. Il livello dei criteri applicati sarà tuttavia limitato in questo MVP per la governance iniziale.
- Anche se vengono usati gruppi di gestione di Azure, è prevista una gerarchia relativamente semplice.
- Verranno usati progetti Azure Blueprints per distribuire e aggiornare le sottoscrizioni tramite l'applicazione di requisiti di controllo degli accessi in base al ruolo, modelli di Resource Manager e Criteri di Azure nei vari gruppi di gestione.

## <a name="applying-the-dependent-patterns"></a>Applicazione dei modelli dipendenti

Le decisioni seguenti rappresentano i modelli da applicare tramite la strategia di applicazione dei criteri sopra descritta:

**Baseline di identità**. I progetti Azure Blueprints definiranno i requisiti di controllo degli accessi in base al ruolo a livello di sottoscrizione per garantire che in tutte le sottoscrizioni sia configurata un'identità coerente.

**Baseline di sicurezza: Rete**. Il team di governance del cloud gestisce un modello di Resource Manager per creare un gateway VPN tra Azure e il dispositivo VPN locale. Quando il team di un'applicazione richiederà una connessione VPN, il team di governance del cloud applicherà il modello di Resource Manager del gateway tramite Azure Blueprints.

**Baseline di sicurezza: crittografia**. A questo punto del percorso, in questa area di interesse non è necessaria alcuna applicazione di criteri. Questo aspetto verrà affrontato durante le evoluzioni successive.
