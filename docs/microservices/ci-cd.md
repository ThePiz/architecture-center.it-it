---
title: CI/CD per microservizi
description: Integrazione continua e recapito continuo per microservizi
author: MikeWasson
ms.date: 12/08/2017
ms.openlocfilehash: 7d8a81b7bc236e50d722a68a0115b9220d4e094f
ms.sourcegitcommit: 786bafefc731245414c3c1510fc21027afe303dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="designing-microservices-continuous-integration"></a>Progettazione di microservizi: integrazione continua

Integrazione continua e recapito continuo (CI/CD) sono un requisito chiave per la riuscita dei microservizi. Senza un processo CI/CD, non si otterrà la flessibilità offerta dai microservizi. Alcune problematiche di CI/CD per i microservizi derivano dalla presenza di più basi di codice e ambienti di compilazione eterogenei per i vari servizi. Questo capitolo descrive le problematiche e alcuni approcci consigliabili.

![](./images/ci-cd.png)

La maggiore rapidità dei cicli di rilascio è uno dei principali motivi per adottare un'architettura di microservizi. 

In un'applicazione puramente monolitica esiste un'unica pipeline di compilazione il cui output è l'eseguibile dell'applicazione. Tutte le attività di sviluppo confluiscono in questa pipeline. Se viene trovato un bug ad alta priorità, è necessario integrare, testare e pubblicare una correzione e questo può ritardare il rilascio di nuove funzionalità. Questi problemi possono essere attenuati con un factoring efficiente dei moduli e l'uso di rami di funzionalità per ridurre al minimo l'impatto delle modifiche al codice. Con l'aumento della complessità dell'applicazione e l'aggiunta di altre funzionalità, tuttavia, il processo di rilascio di un'applicazione monolitica tende a diventare più delicato e soggetto a errori. 

Seguendo la filosofia dei microservizi, non è mai previsto un lungo processo di rilascio che richiede l'allineamento di ogni team. Il team che compila il servizio "A" può rilasciare un aggiornamento in qualsiasi momento, senza attendere che vengano unite, testate e distribuite modifiche nel servizio "B". Il processo CI/CD è essenziale per consentire tutto questo. La pipeline di versione deve essere automatizzata e altamente affidabile, in modo da ridurre al minimo i rischi associati alla distribuzione di aggiornamenti. Se il rilascio in produzione viene eseguito ogni giorno o più volte al giorno, le regressioni o le interruzioni dei servizi devono essere molto rare. Al tempo stesso, in caso di distribuzione di un aggiornamento non valido è necessario poter eseguire in modo rapido e affidabile il rollback o il rollforward a una versione precedente di un servizio.

![](./images/cicd-monolith.png)

L'espressione "CI/CD" viene usata per fare riferimento a diversi processi correlati: integrazione continua, recapito continuo e distribuzione continua.

- Con l'integrazione continua, le modifiche al codice vengono unite frequentemente nel ramo principale usando processi di compilazione e test automatizzati in modo da garantire che il codice del ramo principale sia sempre di qualità idonea ad ambienti di produzione.

- Con il recapito continuo, le modifiche al codice che superano il processo di integrazione continua vengono pubblicate automaticamente in un ambiente di simil-produzione. La distribuzione nell'ambiente di produzione live può richiedere l'approvazione manuale, ma per il resto è automatizzata. L'obiettivo è fare in modo che il codice sia sempre *pronto* per la distribuzione in produzione.

- Con la distribuzione continua, le modifiche al codice che superano il processo CI/CD vengono distribuite automaticamente in produzione.

Nel contesto di Kubernetes e dei microservizi, la fase dell'integrazione continua riguarda la compilazione e il test di immagini di contenitori e il push di tali immagini in un registro contenitori. Nella fase della distribuzione, le specifiche dei pod vengono aggiornate in modo da usare l'immagine di produzione più recente.

## <a name="challenges"></a>Problematiche

- **Molte basi di codice indipendenti di piccole dimensioni**. Ogni team è responsabile della compilazione del proprio servizio, con una propria pipeline di compilazione. In alcune organizzazioni, i team possono usare repository di codice separati. È quindi possibile che le conoscenze per la compilazione del sistema siano distribuite tra i vari team e nessuno nell'organizzazione sappia come distribuire l'intera applicazione. Che cosa accade ad esempio in uno scenario di ripristino di emergenza, se è necessario eseguire rapidamente la distribuzione in un nuovo cluster?   

- **Più linguaggi e framework**. Se ogni team usa una propria combinazione di tecnologie, può essere difficile creare un unico processo di compilazione che possa essere usato nell'intera organizzazione. Il processo di compilazione deve essere sufficientemente flessibile da poter essere adattato da ogni team al linguaggio o al framework scelto. 

- **Integrazione e test di carico**. Se i team rilasciano aggiornamenti al proprio ritmo, può essere difficile progettare test end-to-end affidabili, soprattutto quando i servizi includono dipendenze da altri servizi. Dato che l'esecuzione di un cluster di produzione completo può essere costosa, inoltre, è improbabile che ogni team possa eseguire il proprio cluster completo a livello di produzione solo a scopo di test. 

- **Gestione del rilascio**. Ogni team dovrebbe avere la possibilità di distribuire un aggiornamento in produzione. Ciò non significa assegnare a ogni membro del team le autorizzazioni necessarie a tale scopo. Un ruolo di responsabile del rilascio centralizzato, tuttavia, può ridurre la velocità delle distribuzioni. Maggiore è il livello di automazione e affidabilità del processo CI/CD, minore sarà l'esigenza di un'autorità centrale. È tuttavia possibile usare criteri diversi per il rilascio degli aggiornamenti di funzionalità principali e delle correzioni di bug secondarie. La decentralizzazione non è sinonimo di zero governance.

- **Controllo delle versioni delle immagini di contenitori**. Durante il ciclo di sviluppo e di test, il processo CI/CD compilerà molte immagini di contenitori. Solo alcune di queste sono candidati validi per il rilascio e successivamente il push in produzione verrà eseguito solo per alcuni di questi candidati al rilascio. È consigliabile avere una strategia di controllo delle versioni ben definita, in modo da sapere quali immagini sono attualmente distribuite in produzione e poter eseguire il rollback a una versione precedente, se necessario. 

- **Aggiornamenti dei servizi**. L'aggiornamento di un servizio a una nuova versione non dovrà comportare interruzioni per gli altri servizi che dipendono da esso. In caso di aggiornamento in sequenza, per un periodo di tempo verrà eseguita una combinazione di versioni. 
 
Queste problematiche rispecchiano una tensione di fondo. Da un lato, i team devono lavorare con la massima indipendenza possibile. Dall'altro, un certo coordinamento è necessario per consentire a una singola persona di eseguire attività come un test di integrazione, la ridistribuzione dell'intera soluzione in un nuovo cluster o il rollback di un aggiornamento non valido. 
 
## <a name="cicd-approaches-for-microservices"></a>Approcci CI/CD per i microservizi

È consigliabile che il team di ogni servizio includa il proprio ambiente di compilazione in un contenitore e che in tale contenitore siano presenti tutti gli strumenti di compilazione necessari per compilare gli elementi di codice per il servizio. Spesso è disponibile un'immagine Docker ufficiale per il linguaggio e il framework. È quindi possibile usare `docker run` o Docker Compose per eseguire la compilazione. 

Con questo approccio, configurare un nuovo ambiente di compilazione è semplice. Uno sviluppatore che vuole compilare il proprio codice non deve necessariamente installare un set di strumenti di compilazione, ma esegue semplicemente l'immagine del contenitore. Un aspetto forse ancora più importante è che il server di compilazione può essere configurato per eseguire la stessa operazione. In questo modo, non è necessario installare tali strumenti nel server di compilazione o gestire versioni degli strumenti in conflitto. 

Per lo sviluppo e il test in locale, usare Docker per eseguire il servizio all'interno di un contenitore. Nell'ambito di questo processo, potrebbe essere necessario eseguire altri contenitori che includono servizi fittizi o database di test necessari per il test in locale. È possibile usare Docker Compose per coordinare questi contenitori oppure Minikube per eseguire Kubernetes in locale. 

Quando il codice è pronto, aprire una richiesta pull ed eseguire l'unione nel master. In questo modo verrà avviato un processo nel server di compilazione:

1. Compilare gli asset di codice. 
2. Eseguire unit test sul codice.
3. Compilare l'immagine del contenitore.
4. Testare l'immagine del contenitore eseguendo test funzionali su un contenitore in esecuzione. Questo passaggio consente di rilevare errori nel file Docker, ad esempio un punto di ingresso non valido.
5. Eseguire il push dell'immagine in un registro contenitori.
6. Aggiornare il cluster di test con la nuova immagine per eseguire test di integrazione.

Quando l'immagine è pronta per l'implementazione in produzione, aggiornare i file di distribuzione in base alle esigenze per specificare l'immagine più recente, includendo gli eventuali file di configurazione di Kubernetes. Applicare quindi l'aggiornamento al cluster di produzione.

Di seguito sono riportate alcune raccomandazioni per una maggiore affidabilità delle distribuzioni:
 
- Definire convenzioni a livello di organizzazione per i tag dei contenitori, il controllo delle versioni e la denominazione delle risorse distribuite nel cluster (pod, servizi e così via). Questo può facilitare la diagnosi dei problemi di distribuzione. 

- Creare due registri contenitori separati, uno per sviluppo e test e uno per la produzione. Non eseguire il push di un'immagine nel registro per la produzione finché non si è pronti a distribuire l'immagine in produzione. Combinando questa procedura con il versionamento semantico delle immagini di contenitori è possibile ridurre le probabilità di distribuire accidentalmente una versione il cui rilascio non è stato approvato.

## <a name="updating-services"></a>Aggiornamento dei servizi

Esistono varie strategie per aggiornare un servizio già in produzione. Di seguito vengono illustrare tre opzioni comuni: aggiornamento in sequenza, distribuzione di tipo blu-verde e versione canary.

### <a name="rolling-update"></a>Aggiornamento in sequenza 

In un aggiornamento in sequenza, si distribuiscono nuove istanze di un servizio e queste iniziano subito a ricevere richieste. Quando le nuove istanze diventano disponibili, quelle precedenti vengono rimosse.

Gli aggiornamenti in sequenza sono il comportamento predefinito in Kubernetes quando si aggiorna la specifica del pod per una distribuzione. Il controller di distribuzione crea un nuovo ReplicaSet per i pod aggiornati. Aumenta quindi le prestazioni del nuovo ReplicaSet riducendo al tempo stesso le prestazioni di quello precedente per gestire il numero di repliche desiderato. I pod precedenti non vengono eliminati finché non sono pronti quelli nuovi. Kubernetes mantiene una cronologia dell'aggiornamento ed è quindi possibile usare kubectl per eseguire il rollback di un aggiornamento, se necessario. 

Se il servizio esegue un'attività di avvio di lunga durata, è possibile definire un probe di idoneità, che segnala quando il contenitore è pronto per iniziare a ricevere traffico. Kubernetes non invierà traffico al pod finché il probe non segnala un esito positivo. 

Una problematica degli aggiornamenti in sequenza è rappresentata dal fatto che durante il processo di aggiornamento è in esecuzione e riceve traffico una combinazione di versioni nuove e precedenti. Durante tale periodo, qualsiasi richiesta potrebbe essere indirizzata a una delle due delle versioni. Questo potrebbe causare o meno problemi a seconda dell'ambito delle modifiche tra le due versioni. 

### <a name="blue-green-deployment"></a>Distribuzione di tipo blu-verde

In una distribuzione di tipo blu-verde, si distribuisce la nuova versione insieme alla precedente. Dopo aver convalidato la nuova versione, si trasferisce tutto il traffico contemporaneamente dalla versione precedente alla nuova. Dopo il trasferimento, si monitora l'applicazione per rilevare eventuali problemi. Se si verificano errori, è possibile tornare alla versione precedente. Supponendo che non sussistano problemi, è possibile eliminare la versione precedente.

Con un'applicazione monolitica o a più livelli più tradizionale, la distribuzione di tipo blu-verde comporta in genere il provisioning di due ambienti identici. Si distribuisce la nuova versione in un ambiente di staging, quindi si reindirizza il traffico client all'ambiente di staging, ad esempio scambiando gli indirizzi VIP.

In Kubernetes, non è necessario effettuare il provisioning di un cluster separato per eseguire distribuzioni di tipo blu-verde. È invece possibile sfruttare i selettori. Creare una nuova risorsa di distribuzione con una nuova specifica di pod e un diverso set di etichette. Creare questa distribuzione senza eliminare la precedente o modificare il servizio che vi fa riferimento. Quando i nuovi pod sono in esecuzione, è possibile aggiornare il selettore del servizio in base alla nuova distribuzione. 

Un vantaggio delle distribuzioni di tipo blu-verde è che il servizio esegue il trasferimento di tutti i pod contemporaneamente. Dopo l'aggiornamento del servizio, tutte le nuove richieste vengono indirizzate alla nuova versione. Uno svantaggio è che durante l'aggiornamento viene eseguito il doppio dei pod per il servizio, per la versione corrente e la successiva. Se i pod richiedono una grande quantità di risorse di memoria o CPU, potrebbe essere necessario aumentare temporaneamente il numero di istanze del cluster per gestire l'utilizzo delle risorse. 

### <a name="canary-release"></a>Versione canary

Con una versione canary, si implementa una versione aggiornata in un numero ridotto di client. Si monitora quindi il comportamento del nuovo servizio prima di procedere all'implementazione in tutti i client. In questo modo è possibile eseguire un'implementazione lenta in modo controllato, osservare i dati reali e individuare i problemi prima che abbiano un impatto su tutti i clienti.

Una versione canary è più complessa da gestire rispetto a un aggiornamento in sequenza o di tipo blu-verde, perché è necessario indirizzare dinamicamente le richieste a versioni diverse del servizio. In Kubernetes, è possibile configurare un servizio in modo da includere due set di replica (uno per ogni versione) e modificare manualmente i numeri di repliche. Questo approccio presenta tuttavia una granularità piuttosto grossolana a causa del modo in cui Kubernetes bilancia il carico tra i pod. In presenza di un totale di dieci repliche, ad esempio, è possibile spostare il traffico solo in incrementi del 10%. Se si usa una rete mesh di servizi, è possibile usare le relative regole di routing per implementare una strategia di versione canary più sofisticata. Di seguito sono riportate alcune risorse che potrebbero rivelarsi utili.

- Kubernetes senza rete mesh di servizi: [Canary deployments](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/#canary-deployments) (Distribuzioni canary)
- Linkerd: [Dynamic request routing](https://linkerd.io/features/routing/) (Routing dinamico delle richieste)
- Istio: [Canary Deployments using Istio](https://istio.io/blog/canary-deployments-using-istio.html) (Distribuzioni canary con Istio)

## <a name="conclusion"></a>Conclusioni

Negli ultimi anni, nel settore si è verificato un cambiamento radicale, ossia il passaggio dalla creazione di *sistemi di registrazione* alla creazione di *sistemi di engagement*.

I sistemi di registrazione sono applicazioni di gestione dati back office tradizionali. Questi sistemi sono spesso basati su un sistema di gestione di database relazionali che costituisce l'unica origine di dati reali. Il termine "sistema di engagement" è attribuito a Geoffrey Moore, che lo ha adottato nel suo documento del 2011 *Systems of Engagement and the Future of Enterprise IT* (Sistemi di engagement e futuro dell'IT aziendale). I sistemi di engagement sono applicazioni incentrate sulla comunicazione e la collaborazione. Connettono le persone in tempo reale e devono essere disponibili 24 ore su 24, 7 giorni su 7. Le nuove funzionalità vengono introdotte regolarmente senza portare l'applicazione offline. Gli utenti hanno aspettative maggiori e minore pazienza nei confronti di tempi di inattività o ritardi imprevisti.

A livello di consumatori, un'esperienza utente migliore può avere un valore aziendale quantificabile. La quantità di tempo durante la quale un utente interagisce con un'applicazione può tradursi direttamente in ricavi. Nell'ambito dei sistemi aziendali, le aspettative degli utenti sono cambiate. Se l'obiettivo di questi sistemi è facilitare la comunicazione e la collaborazione, è necessario prendere spunto dalle applicazioni rivolte ai consumatori.

I microservizi sono una risposta a questo cambiamento del panorama. Scomponendo un'applicazione monolitica in un gruppo di servizi a regime di controllo libero, è possibile controllare il ciclo di rilascio di ogni servizio e consentire aggiornamenti frequenti senza tempi di inattività o modifiche di rilievo. I microservizi sono utili anche ai fini della scalabilità, dell'isolamento degli errori e della resilienza. Nel frattempo, le piattaforme cloud facilitano la compilazione e l'esecuzione di microservizi con il provisioning automatizzato delle risorse di calcolo, agenti di orchestrazione dei contenitori distribuiti come servizio e ambienti senza server guidati dagli eventi.

Come è stato illustrato, tuttavia, le architetture di microservizi presentano anche molte problematiche. Per la riuscita, è necessario partire da una progettazione valida e prestare attenzione nell'analisi del dominio, nella scelta delle tecnologie, nella modellazione dei dati, nella progettazione delle API e nella creazione di una cultura DevOps avanzata. Microsoft si augura che questa guida e l'[implementazione di riferimento](https://github.com/mspnp/microservices-reference-implementation) associata agevolino questo percorso. 

