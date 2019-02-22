<!-- TEMPLATE FILE - DO NOT ADD METADATA -->

### <a name="governance-of-resources"></a>Governance delle risorse

L'applicazione di una governance a livello di sottoscrizioni può essere realizzata con un progetto Azure Blueprints e con gli asset associati all'interno del progetto.

1. Creare un progetto Azure Blueprints denominato "MVP per la governance".
    1. Imporre l'uso dei ruoli di Azure standard.
    2. Stabilire che gli utenti possano eseguire l'autenticazione solo con un'implementazione di controllo degli accessi in base al ruolo esistente.
    3. Applicare questo progetto a tutte le sottoscrizioni all'interno del gruppo di gestione.
2. Creare un'istanza di Criteri di Azure per imporre o applicare quanto segue:
    1. Per aggiungere tag alle risorse sono necessari valori per la funzione aziendale, la classificazione dei dati, la criticità, il contratto di servizio, l'ambiente e l'applicazione.
    2. Il valore del tag dell'applicazione deve corrispondere al nome del gruppo di risorse.
    3. Convalidare le assegnazioni di ruolo per ogni risorsa e gruppo di risorse.
3. Pubblicare e applicare il progetto Azure Blueprints "MVP per la governance" a ogni gruppo di gestione.

Questi modelli consentono di rilevare e monitorare le risorse e di applicare la gestione dei ruoli di base.

### <a name="demilitarized-zone-dmz"></a>Rete perimetrale

Accade spesso che specifiche sottoscrizioni richiedano un certo livello di accesso alle risorse locali. È il caso, ad esempio, degli scenari di migrazione o di sviluppo in cui alcune risorse dipendenti si trovano ancora nel data center locale. In questo caso, un MVP per la governance aggiunge le procedure consigliate seguenti:

1. Creare una rete perimetrale nel cloud.
    1. L'[architettura di riferimento di una rete perimetrale nel cloud](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) definisce un modello di distribuzione per la creazione di un gateway VPN in Azure.
    2. Verificare che siano soddisfatti i requisiti di connettività e sicurezza della rete perimetrale appropriati per un dispositivo perimetrale locale presente nel data center locale.
    3. Verificare che il dispositivo perimetrale locale sia compatibile con i requisiti di un gateway VPN di Azure.
    4. Dopo aver verificato la connessione alla rete VPN locale, acquisire il modello di Resource Manager creato dall'architettura di riferimento.
2. Creare un secondo progetto denominato "Rete Perimetrale".
    1. Aggiungere al progetto il modello di Resource Manager per il gateway VPN.
3. Applicare il progetto Rete perimetrale a tutte le sottoscrizioni che richiedono la connettività locale. Questo progetto deve essere applicato in aggiunta al progetto "MVP per la governance".

Una delle principali preoccupazioni espresse dai team di sicurezza IT e di governance tradizionale è il rischio che nella fase iniziale di adozione del cloud vengano compromessi asset esistenti. L'approccio sopra descritto consente ai team di adozione del cloud di creare ed eseguire la migrazione di soluzioni ibride, con meno rischi per gli asset locali. In un'evoluzione successiva, questa soluzione temporanea potrà essere eliminata.

> [!NOTE]
> Questo approccio costituisce un punto di partenza per creare rapidamente un MVP per la governance della baseline, ma è solo l'inizio del percorso di governance. Sarà infatti necessaria un'evoluzione successiva man mano che l'azienda prosegue il processo di adozione del cloud e aumentano i rischi nelle aree seguenti:
>
> - Carichi di lavoro cruciali
> - Dati protetti
> - Gestione dei costi
> - Scenari multi-cloud
>
>I dettagli specifici di questo MVP sono basati sul percorso di esempio di una società fittizia descritto negli articoli seguenti. Prima di implementare questa procedura consigliata è opportuno acquisire familiarità con gli altri articoli di questa serie.
