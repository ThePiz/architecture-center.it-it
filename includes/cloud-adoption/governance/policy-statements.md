<!-- TEMPLATE FILE - DO NOT ADD METADATA -->

## <a name="policy-statements"></a>Policy statements

Le informative sui criteri seguenti stabiliscono i requisiti necessari per mitigare i rischi definiti. Questi criteri definiscono i requisiti funzionali per il prodotto minimo funzionante (MVP, Minimum Viable Product) per la governance. Ognuno sarà rappresentato nell'implementazione dell'MVP per la governance.

Accelerazione della distribuzione:

- tutte le risorse devono essere raggruppate e contrassegnate in base a strategie definite di raggruppamento e assegnazione dei tag.
- Tutti gli asset devono usare un modello di distribuzione approvato.
- Una volta stabilita una base di governance per un provider di servizi cloud, eventuali strumenti di distribuzione devono essere compatibili con quelli definiti dal team di governance.

Baseline di identità:

- tutti gli asset distribuiti nel cloud devono essere controllati tramite identità e ruoli approvati da criteri di governance correnti.
- Tutti i gruppi con privilegi elevati nell'infrastruttura di Active Directory locale dovrebbero essere associati a un ruolo approvato di controllo degli accessi in base al ruolo.

Baseline di sicurezza:

- qualsiasi asset distribuito nel cloud deve avere una classificazione dei dati approvata.
- Nessun asset identificato con un livello di dati protetto può essere distribuito nel cloud, finché non possono essere approvati e implementati i requisiti minimi per la sicurezza e la governance.
- Finché non possono essere convalidati e disciplinati i requisiti minimi relativi alla sicurezza di rete, gli ambienti cloud vengono visti come zona demilitarizzata e devono soddisfare requisiti di connessione simili ad altri data center o reti interne.

Gestione costi:

- per motivi di rilevamento, tutti gli asset devono essere assegnati a un proprietario dell'applicazione all'interno di una delle funzioni aziendali principali.
- Quando si verificano problemi di costo, verranno stabiliti ulteriori requisiti di governance con il team finanziario.

Coerenza delle risorse:

- poiché in questa fase non vengono distribuiti carichi di lavoro di importanza strategica, non sussistono requisiti da disciplinare relativi a Contratti di servizio, prestazioni o continuità aziendale e ripristino di emergenza.
- Quando vengono distribuiti carichi di lavoro cruciali, verranno stabiliti ulteriori requisiti di governance con il reparto IT.

## <a name="processes"></a>Processi

Non è stato allocato alcun budget per il monitoraggio e l'applicazione continui di questi criteri di governance. Per questo motivo, il team di governance del cloud ha a disposizione alcuni strumenti ad hoc per monitorare la conformità alle informative sui criteri.

- **Formazione**: il team di governance del cloud investe tempo per informare i team di adozione del cloud relativamente ai percorsi di governance che supportano questi criteri.
- Revisioni della **distribuzione**: prima di distribuire qualsiasi asset, il team di governance del cloud esaminerà il percorso di governance con i team di adozione del cloud.
