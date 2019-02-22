---
title: 'CAF: Idoneità del CISO'
description: Come può un CISO prepararsi per il cloud
author: BrianBlanchard
ms.date: 10/03/2018
ms.openlocfilehash: cedb86488304e2fc84897e1da373730768adce66
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902171"
---
# <a name="ciso-cloud-readiness-guide"></a>Guida all'idoneità del CISO per il cloud

Le linee guida offerte da Microsoft come Azure Cloud Adoption Framework (CAF) non sono intese a determinare o guidare i vincoli di sicurezza specifici delle migliaia di aziende supportate da questa documentazione. Quando si passa al cloud, il ruolo di Chief Information Security Officer o del Chief Information Security Office (CISO) non viene sostituito dalle tecnologie cloud. Al contrario, il CISO e il relativo reparto diventano più integrati. Questa guida presuppone che il lettore abbia familiarità con i processi del CISO e abbia come obiettivo quello di modernizzarli per consentire la trasformazione per il cloud.

L'adozione del cloud assicura servizi che non vengono presi spesso in considerazione negli ambienti IT tradizionali. Le distribuzioni automatizzate o in modalità self-service vengono eseguite comunemente dal reparto Sviluppo di applicazioni o da altri team IT tradizionalmente non allineati alla distribuzione di produzione. In alcune organizzazioni i componenti aziendali dispongono in modo analogo di funzionalità self-service. Ciò può comportare nuovi requisiti di sicurezza che non erano richiesti in precedenza nell'ambiente locale. La sicurezza centralizzata è più complessa e la protezione diventa spesso una responsabilità condivisa tra l'azienda e la cultura IT. Questo articolo può aiutare un CISO a prepararsi per tale approccio e ad avvicinarsi alla governance incrementale.

## <a name="how-can-the-ciso-prepare-for-the-cloud"></a>Come può un CISO prepararsi per il cloud

Come la maggior parte dei criteri, quelli di governance e sicurezza all'interno di un'organizzazione tendono a crescere in modo organico. Quando si verificano problemi di sicurezza, vengono plasmati nuovi criteri per informare gli utenti e ridurre la probabilità che si ripetano. Per quanto naturale, questo approccio comporta un aumento spropositato dei criteri oltre a dipendenze tecniche. I percorsi di trasformazione per il cloud creano un'opportunità unica per modernizzare e reimpostare i criteri. Mentre si prepara per un qualsiasi percorso di trasformazione, il CISO può realizzare un valore immediato e significativo fungendo da stakeholder principale nell'ambito di una [revisione dei criteri](./what-is-a-cloud-policy-review.md).

In questo tipo di revisione il ruolo del CISO è quello di creare un equilibrio sicuro tra i vincoli dei criteri/requisiti di conformità esistenti e il comportamento di sicurezza migliorato dei provider di servizi cloud. La valutazione di questi progressi può assumere molte forme; spesso vengono misurati in base al numero di criteri di sicurezza che possono essere trasferiti in modo sicuro al provider di servizi cloud.

**Trasferimento dei rischi di sicurezza**: man mano che i servizi vengono spostati in modelli di hosting dell'infrastruttura distribuita come servizio (IaaS), l'azienda si assume meno rischi diretti riguardanti il provisioning dell'hardware. I rischi non vengono meno, ma sono trasferiti al fornitore di servizi cloud. Se l'approccio del fornitore di servizi cloud al provisioning dell'hardware garantisce lo stesso livello di mitigazione dei rischi, nell'ambito di un processo ripetibile e sicuro, il rischio di esecuzione del provisioning dell'hardware viene rimosso dai criteri aziendali. A questo punto può essere sostituito con un nuovo criterio per la convalida di tali processi, ma il rischio di esecuzione viene riallocato, riducendo i rischi complessivi in termini di sicurezza.

Man mano che le soluzioni si evolvono "nello stack" incorporando modelli di piattaforma come servizio (PaaS) o di software come servizio (SaaS), possono essere mitigati, trasferiti e sostituiti altri rischi. Quando il rischio viene trasferito in modo sicuro a un provider di servizi cloud, anche il costo di esecuzione, monitoraggio e applicazione dei criteri di sicurezza o di altri criteri di conformità può essere ridotto in modo sicuro.

**Mentalità orientata alla crescita**: il cambiamento può far paura a chi lo implementa all'interno dell'azienda, così come a livello tecnico. Quando il CISO introduce un cambiamento di mentalità orientata alla crescita in un'organizzazione, a questi timori naturali si sostituisce un interesse sempre maggiore per la sicurezza e la conformità ai criteri. Avvicinarsi a una [revisione dei criteri](./what-is-a-cloud-policy-review.md), un percorso di trasformazione o una semplice revisione dell'implementazione con una mentalità orientata alla crescita consente al team di eseguire rapidamente la transizione, ma non al costo di un profilo di rischio legittimo e gestibile.

## <a name="resources-for-the-chief-information-security-officer"></a>Risorse per il Chief Information Security Officer

La conoscenza del cloud è fondamentale per l'approccio a una [revisione dei criteri](./what-is-a-cloud-policy-review.md) con una mentalità orientata alla crescita. Le risorse seguenti possono aiutare il CISO a comprendere meglio il comportamento di sicurezza della piattaforma Microsoft Azure.

Risorse della piattaforma di sicurezza:

* [Ciclo di sviluppo per la sicurezza, controlli interni](https://www.microsoft.com/sdl/)
* [Formazione sulla sicurezza obbligatoria, controlli in background](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx)
* [Test di penetrazione, rilevamento delle intrusioni, DDoS, controlli e registrazione](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging)
* [Data center all'avanguardia](https://www.microsoft.com/cloud-platform/global-datacenters), sicurezza fisica, [rete protetta](/azure/security/security-network-overview)
* [Microsoft Azure Security Response in the Cloud (PDF)](http://aka.ms/SecurityResponsePaper) (Risposta della sicurezza di Microsoft Azure nel cloud)

Privacy e controlli:

* [Gestione costante dei propri dati](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data)
* [Controllo sulla posizione dei dati](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located)
* [Accesso ai dati in base ai propri termini](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)
* [Risposta alle richieste delle forze dell'ordine](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data)
* [Standard di privacy rigorosi](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards)

Conformità:

* [Centro protezione](https://www.microsoft.com/trustcenter/default.aspx)
* [Common Controls Hub](https://www.microsoft.com/trustcenter/Common-Controls-Hub)
* [Elenco di controllo della due diligence per i servizi cloud](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist)
* [Conformità in base al servizio, alla località e al settore](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

Trasparenza:

* [Come vengono protetti da Microsoft i dati dei clienti nei servizi di Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx)
* [Come viene gestita da Microsoft la posizione dei dati nei servizi di Azure](http://azuredatacentermap.azurewebsites.net/)
* [Personale Microsoft che ha accesso ai dati e in quali termini](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)
* [Come vengono protetti da Microsoft i dati dei clienti nei servizi di Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx)
* [Certificazione di verifica per i servizi di Azure, hub per la trasparenza](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

## <a name="next-steps"></a>Passaggi successivi

Il primo passo per mettere in atto una strategia di governance è una [revisione dei criteri](./what-is-a-cloud-policy-review.md). [Criteri e conformità](./overview.md) potrebbe essere una guida utile durante la revisione dei criteri.

> [!div class="nextstepaction"]
> [Prepararsi alla revisione dei criteri](./what-is-a-cloud-policy-review.md)