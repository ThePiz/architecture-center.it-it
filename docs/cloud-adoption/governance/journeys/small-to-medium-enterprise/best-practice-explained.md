---
title: 'CAF: Enterprise di piccole e medie-procedure consigliate illustrate'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Enterprise di piccole e medie-procedure consigliate illustrate
author: BrianBlanchard
ms.openlocfilehash: b9b385be345098bf1b9e0e1cdce7fd3cceeb5523
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59639581"
---
# <a name="small-to-medium-enterprise-best-practice-explained"></a>Piccole e medie imprese: Spiegazione delle procedure consigliate

Il percorso di governance inizia con un set di [criteri aziendali](./initial-corporate-policy.md) iniziali. Questi criteri vengono usati per stabilire un MVP per la governance che rifletta le [procedure consigliate](./overview.md).

Questo articolo illustra le strategie di alto livello necessarie per creare un MVP per la governance. L'elemento centrale dell'MVP per la governance è la disciplina [Accelerazione della distribuzione](../../deployment-acceleration/overview.md). Gli strumenti e i modelli applicati in questa fase renderanno possibili le evoluzioni incrementali necessarie per espandere la governance in futuro.

## <a name="governance-mvp-cloud-adoption-foundation"></a>MVP per la governance (Cloud Adoption Foundation)

Un'adozione rapida dei criteri di governance e aziendali è possibile, grazie ad alcuni semplici principi e a strumenti di governance basati sul cloud. Questi sono i primi tre discipline approccio in qualsiasi processo di governance. Ognuna verrà approfondita in questo articolo.

Per iniziare, questo articolo illustra le strategie di alto livello alla base delle discipline Baseline di identità, Baseline di sicurezza e Accelerazione della distribuzione necessarie per creare un MVP per la governance, che verrà utilizzato come base per qualsiasi processo di adozione.

![Esempio di un MVP per la governance incrementale](../../../_images/governance/governance-mvp.png)

## <a name="implementation-process"></a>Processo di implementazione

L'implementazione dell'MVP per la governance dipende direttamente da identità, sicurezza e rete. Dopo aver risolto le dipendenze, il team di governance del cloud stabilirà alcuni aspetti relativi alla governance. Le decisioni del team di governance del cloud e dei team di supporto verranno implementate tramite un singolo pacchetto di asset per l'imposizione di criteri.

![Esempio di un MVP per la governance incrementale](../../../_images/governance/governance-mvp-implementation-flow.png)

Questa implementazione può essere descritta anche usando un semplice elenco di controllo:

1. Sollecitare decisioni relative alle dipendenze principali: identità, rete e crittografia.
2. Determinare il modello da usare durante l'imposizione dei criteri aziendali.
3. Determinare i modelli di governance appropriate per la coerenza di risorse, tag delle risorse e discipline di registrazione e report.
4. Implementare gli strumenti di governance conformi al modello di imposizione dei criteri scelto per applicare decisioni dipendenti e di governance.

[!INCLUDE [implementation-process](../../../../../includes/cloud-adoption/governance/implementation-process.md)]

## <a name="application-of-governance-defined-patterns"></a>Applicazione di modelli di governance

Il team di governance del cloud è responsabile delle decisioni e delle implementazioni seguenti. In molti casi, è necessario il contributo di altri team, ma è probabile che il team di governance del cloud si occupi sia delle decisioni che delle implementazioni. Le sezioni seguenti illustrano le decisioni prese per questo caso d'uso e i dettagli relativi a ogni decisione.

### <a name="subscription-model"></a>Modello di sottoscrizione

Per le sottoscrizioni di Azure, è stato scelto un modello di **categoria di applicazione**.

- Un archetipo di applicazione consente di raggruppare le applicazioni con esigenze simili. Alcuni esempi comuni sono: applicazioni con dati protetti, applicazioni gestite (ad esempio, HIPAA o FedRAMP), applicazioni a basso rischio, applicazioni con dipendenze in locale, SAP o altri sistemi mainframe in Azure o applicazioni che estendono SAP o sistemi mainframe in locale. Questi archetipi sono specifici di ogni organizzazione e dipendono dalla classificazione dei dati e dai tipi di applicazioni che supportano l'azienda. Il mapping delle dipendenze del patrimonio digitale consente di definire gli archetipi di applicazione in un'organizzazione.
- I reparti non sono necessari nello scenario corrente. Le distribuzioni devono essere vincolate all'interno di una singola unità di fatturazione. Nella fase di adozione, è possibile che non sia presente un contratto Enterprise sulla centralizzazione della fatturazione. È probabile che questo livello di adozione sia gestito da una singola sottoscrizione di Azure con pagamento a consumo.
- Indipendentemente dall'uso di EA Portal o dall'esistenza di un contratto Enterprise, è necessario definire e concordare un modello di sottoscrizione per ridurre al minimo un carico amministrativo che vada oltre la semplice fatturazione.
- Nel modello di **categoria di applicazione** vengono create sottoscrizioni per ogni archetipo di applicazione. Ogni sottoscrizione appartiene a un account specifico di un ambiente (sviluppo, test e produzione).
- Nell'ambito della progettazione delle sottoscrizioni, è opportuno concordare una convenzione di denominazione comune in base ai due punti precedenti.

### <a name="resource-consistency"></a>Coerenza delle risorse

Come modello di coerenza delle risorse è stata scelta la **coerenza di distribuzione**.

- Per ogni applicazione vengono creati gruppi di risorse. Per ogni archetipo di applicazione vengono creati gruppi di gestione. A tutte le sottoscrizioni nel gruppo di gestione associato è necessario applicare Criteri di Azure.
- Come parte del processo di distribuzione, i modelli di coerenza delle risorse di Azure per tutti i gruppi di risorse devono essere archiviati nel controllo del codice sorgente.
- Ogni gruppo di risorse è associato a un'applicazione o a un carico di lavoro specifico.
- I gruppi di gestione di Azure consentono l'aggiornamento delle progettazioni di governance man mano che maturano i criteri aziendali.
- Un'implementazione estesa di Criteri di Azure può richiedere al team più tempo del previsto e non offrire particolari vantaggi a questo punto. È tuttavia opportuno creare e applicare semplici criteri predefiniti per imporre le poche definizioni dei criteri di governance del cloud. Questi criteri consentono di definire l'implementazione di requisiti di governance specifici che può quindi essere applicata a tutti gli asset distribuiti.

### <a name="resource-tagging"></a>Tag delle risorse

Come modello per l'assegnazione di tag alle risorse è stata scelta la **classificazione**.

- Agli asset distribuiti devono essere assegnati tag con i valori seguenti: classificazione dei dati, criticità, contratto di servizio e ambiente.
- Questi quattro valori sono alla base delle decisioni relative a governance, operazioni e sicurezza.
- Se questo percorso di governance viene implementato in un'unità aziendale o in un team all'interno di un'azienda di dimensioni maggiori, l'assegnazione di tag deve includere anche metadati per l'unità di fatturazione.

### <a name="logging-and-reporting"></a>Registrazione e creazione di report

A questo punto, da qualsiasi team di sviluppo viene suggerito, ma non richiesto obbligatoriamente, un modello **nativo del cloud** per la registrazione e la creazione di report.

- Non sono stati definiti requisiti di governance relativi ai dati da raccogliere per la registrazione o la creazione di report.
- Sarà necessario eseguire altre analisi prima del rilascio di eventuali dati protetti o carichi di lavoro cruciali.

## <a name="evolution-of-governance-processes"></a>Evoluzione dei processi di governance

Man mano che la governance evolve, alcune definizioni dei criteri non possono o non devono essere controllate tramite strumenti automatizzati. Altri criteri comporteranno l'intervento del team responsabile della sicurezza IT e del team di gestione dell'identità locale. Per mitigare eventuali nuovi rischi, il team di governance del cloud controllerà i processi seguenti.

**Accelerazione dell'adozione**: il team di governance del cloud ha esaminato gli script di distribuzione tra più team. Ha mantenuto un set di script da utilizzare come modelli di distribuzione. Tali modelli vengono usati dai team di adozione del cloud e dai team di DevOps per definire più rapidamente le distribuzioni. Ognuno di questi script contiene i requisiti necessari per imporre alcuni criteri di governance, senza interventi aggiuntivi da parte dei tecnici dell'adozione del cloud. Come responsabile di questi script, il team di governance del cloud può implementare più rapidamente le modifiche dei criteri. In conseguenza alla gestione degli script, il team di governance del cloud viene considerato come acceleratore del processo di adozione. Ciò consente di ottenere coerenza tra le distribuzioni, senza necessariamente imporre la conformità.

**Training per i tecnici**: il team di governance del cloud offre sessioni di training bimestrali e ha messo a punto due video destinati ai tecnici. Questi materiali consentono ai tecnici di implementare più velocemente la cultura della governance e la modalità di esecuzione delle attività di distribuzione. Il team sta provvedendo ad aggiungere asset di training per illustrare la differenza tra le distribuzioni di produzione e quelle non di produzione, per consentire ai tecnici di comprendere l'impatto dei nuovi criteri sul processo di adozione. Ciò consente di ottenere coerenza tra le distribuzioni, senza necessariamente imporre la conformità.

**Pianificazione della distribuzione**: prima della distribuzione di asset contenenti dati protetti, il team di governance del cloud esaminerà gli script di distribuzione per convalidarne la conformità alla governance. I team esistenti con le distribuzioni approvate in precedenza verranno controllati tramite strumenti automatici.

**Controllo mensile e creazione di report**: ogni mese, il team di governance del cloud esegue un controllo di tutte le distribuzioni cloud per convalidarne la costante conformità ai criteri. Eventuali deviazioni individuate vengono documentate e condivise con i team di adozione del cloud. Quando non si rischia un'interruzione dell'attività o una perdita dei dati, i criteri vengono imposti automaticamente. Al termine del controllo, il team di governance del cloud crea un report destinato al team per la strategia cloud e a ogni team di adozione del cloud per comunicare l'aderenza complessiva ai criteri. Il report viene inoltre archiviato a scopo legale e di controllo.

**Revisione trimestrale dei criteri**: ogni trimestre, il team di governance del cloud e il team per la strategia cloud esaminano i risultati dei controlli e suggeriscono le modifiche necessarie ai criteri aziendali. Molti di questi suggerimenti sono il risultato di strategie di miglioramento costanti e dell'osservazione dei modelli di utilizzo. Le modifiche ai criteri approvate vengono integrate negli strumenti di governance durante i cicli di controllo successivi.

## <a name="alternative-patterns"></a>Modelli alternativi

Se uno dei modelli selezionati in questo percorso di governance non è conforme ai requisiti del lettore, sono disponibili alternative per ogni modello:

- [Modelli di crittografia](../../../decision-guides/encryption/overview.md)
- [Modelli di identità](../../../decision-guides/identity/overview.md)
- [Modelli di registrazione e creazione di report](../../../decision-guides/log-and-report/overview.md)
- [Modelli di imposizione dei criteri](../../../decision-guides/policy-enforcement/overview.md)
- [Modelli di coerenza delle risorse](../../../decision-guides/resource-consistency/overview.md)
- [Modelli di assegnazione di tag alle risorse](../../../decision-guides/resource-tagging/overview.md)
- [Modelli di reti definite dal software](../../../decision-guides/software-defined-network/overview.md)
- [Modelli di progettazione delle sottoscrizioni](../../../decision-guides/subscriptions/overview.md)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver implementato queste linee guida, ogni team di adozione del cloud può contare su una solida base di conoscenze relative alla governance. Il team di governance del cloud agirà in parallelo per aggiornare continuamente i criteri aziendali e le discipline di governance.

I due team useranno gli indicatori di tolleranza per identificare la successiva evoluzione necessaria per continuare a supportare l'adozione del cloud. Per la società fittizia in questo percorso, il passaggio successivo riguarda l'evoluzione della baseline di sicurezza per il supporto dello spostamento dei dati protetti nel cloud.

> [!div class="nextstepaction"]
> [Evoluzione della baseline di sicurezza](./security-baseline-evolution.md)