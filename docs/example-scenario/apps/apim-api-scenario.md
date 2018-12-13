---
title: Migrazione di un'applicazione Web legacy in un'architettura basata su API in Azure
description: Usare Gestione API di Azure per modernizzare un'applicazione Web legacy.
author: begim
ms.date: 09/13/2018
ms.custom: fasttrack
ms.openlocfilehash: ea063653b4962e42cbec7f9d98c16e22e987efd1
ms.sourcegitcommit: a0e8d11543751d681953717f6e78173e597ae207
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "53004710"
---
# <a name="migrating-a-legacy-web-application-to-an-api-based-architecture-on-azure"></a>Migrazione di un'applicazione Web legacy in un'architettura basata su API in Azure

Una società di e-commerce del settore dei viaggi sta modernizzando lo stack software legacy basato su browser. Anche se lo stack esistente è prevalentemente monolitico, alcuni [servizi HTTP basati su SOAP][soap] sono presenti in un progetto recente. La società sta prendendo in considerazione la creazione di flussi di ricavi aggiuntivi per monetizzare parte della proprietà intellettuale interna che è stata sviluppata.

Gli obiettivi per il progetto includono la gestione del debito tecnico, il miglioramento della manutenzione periodica e l'accelerazione dello sviluppo di funzionalità con meno bug di regressione. Il progetto userà un processo iterativo per evitare rischi, con alcuni passaggi eseguiti in parallelo:

* Il team di sviluppo modernizzerà il back-end dell'applicazione, costituito da database relazionali ospitati in macchine virtuali.
* Il team di sviluppo interno scriverà nuove funzionalità di business che verranno esposte tramite nuove API HTTP.
* Un team di sviluppo di contratti compilerà una nuova interfaccia utente basata su browser, che sarà ospitata in Azure.

Le nuove funzionalità dell'applicazione verranno distribuite in più fasi. Queste funzionalità sostituiranno gradualmente le funzionalità esistenti dell'interfaccia utente di tipo client-server basata su browser (ospitata in locale) che è attualmente alla base dell'attività di e-commerce.

Il team di gestione non vuole modernizzare inutilmente l'applicazione. Vuole anche mantenere il controllo dell'ambito e dei costi. A tale scopo, ha deciso di mantenere i servizi SOAP HTTP esistenti. Intende anche ridurre al minimo le modifiche all'interfaccia utente esistente. [Gestione API di Azure][apim] può essere utilizzato per soddisfare molti dei requisiti e dei vincoli del progetto.

## <a name="architecture"></a>Architettura

![Diagramma dell'architettura][architecture]

La nuova interfaccia utente verrà ospitata come applicazione PaaS (Platform as a Service, piattaforma distribuita come servizio) in Azure e dipenderà dalle API HTTP sia nuove che esistenti. Queste API verranno rilasciate con un set di interfacce progettato in modo più efficiente, che garantisce prestazioni migliori, un'integrazione più semplice ed estendibilità futura.

### <a name="components-and-security"></a>Sicurezza e componenti

1. L'applicazione Web locale esistente continuerà a usare direttamente i servizi Web locali esistenti.
2. Le chiamate dalle app Web esistenti ai servizi HTTP esistenti rimarranno invariate. Queste chiamate sono interne alla rete aziendale.
3. Le chiamate in ingresso vengono effettuate da Azure ai servizi interni esistenti:
    * Il team responsabile della sicurezza consente al traffico proveniente dall'istanza di Gestione API di Azure di passare attraverso il firewall aziendale ai servizi locali [usando il trasporto sicuro (HTTPS/SSL)][apim-ssl].
    * Il team operativo consentirà le chiamate in ingresso ai servizi solo dall'istanza di Gestione API di Azure. Questo requisito viene soddisfatto [inserendo nell'elenco elementi consentiti l'indirizzo IP dell'istanza di Gestione API di Azure][apim-whitelist-ip] entro il perimetro della rete aziendale.
    * Nella pipeline di richieste di servizi HTTP locali viene configurato un nuovo modulo (**solo** per le connessioni con origine esterna), che convaliderà [un certificato che verrà fornito da Gestione API di Azure][apim-mutualcert-auth].
1. La nuova API:
    * Viene esposta solo tramite l'istanza di Gestione API di Azure, che fornirà la facciata dell'API. Non sarà possibile accedere direttamente alla nuova API.
    * Viene sviluppata e pubblicata come [app per le API Web PaaS di Azure][azure-api-apps].
    * Viene inserita nell'elenco elementi consentiti (tramite le [impostazioni dell'app Web][azure-appservice-ip-restrict]) per accettare solo l'[indirizzo VIP di Gestione API di Azure][apim-faq-vip].
    * Viene ospitata in App Web di Azure con il trasporto sicuro/SSL attivato.
    * Ha l'autorizzazione abilitata, [fornita dal Servizio app di Azure][azure-appservice-auth] tramite Azure Active Directory e OAuth2.
2. La nuova applicazione Web basata sul browser dipenderà dall'istanza di Gestione API di Azure per **entrambe** le API, quella HTTP esistente e quella nuova.

L'istanza di Gestione API di Azure verrà configurata per eseguire il mapping dei servizi HTTP legacy a un nuovo contratto API. In questo modo, la nuova interfaccia utente Web non è a conoscenza dell'integrazione con un set di nuove API e di servizi/API legacy. In futuro, il team del progetto convertirà gradualmente la funzionalità alle nuove API e ritirerà i servizi originali. Queste modifiche verranno gestite all'interno della configurazione di Gestione API di Azure, lasciando l'interfaccia utente front-end immutata ed evitando nuove attività di sviluppo.

### <a name="alternatives"></a>Alternative

* Se l'organizzazione volesse spostare l'intera infrastruttura in Azure, incluse le macchine virtuali che ospitano le applicazioni legacy, Gestione API di Azure rimarrebbe l'opzione ideale perché può fungere da facciata per qualsiasi endpoint HTTP indirizzabile.
* Se il cliente decidesse di mantenere privati gli endpoint esistenti e di non esporli pubblicamente, l'istanza di Gestione API potrebbe essere collegata a una [rete virtuale di Azure][azure-vnet]:
  * In uno [scenario in modalità lift-and-shift di Azure][azure-vm-lift-shift] collegato alla rete virtuale di Azure distribuita il cliente potrebbe gestire direttamente il servizio back-end tramite gli indirizzi IP privati.
  * Nello scenario locale l'istanza di Gestione API potrebbe raggiungere il servizio interno privatamente tramite un [gateway VPN di Azure e una connessione VPN IPSec da sito a sito][azure-vpn] o tramite [ExpressRoute][azure-er] trasformando questo scenario in uno [scenario locale e di Azure ibrido][azure-hybrid].
* L'istanza di Gestione API può essere mantenuta privata distribuendo l'istanza di Gestione API in modalità interna. La distribuzione potrebbe quindi essere usata con un [gateway applicazione di Azure][azure-appgw] per abilitare l'accesso pubblico per alcune API mentre altre rimangono interne. Per altre informazioni, vedere [Connessione di Gestione API di Azure in modalità interna a una rete virtuale][apim-vnet-internal].

> [!NOTE]
> Per informazioni generali sulla connessione di Gestione API a una rete virtuale, [vedere qui][apim-vnet].

### <a name="availability-and-scalability"></a>Disponibilità e scalabilità

* È possibile [aumentare il numero di istanze][apim-scaleout] di Gestione API di Azure scegliendo un piano tariffario e quindi aggiungendo unità.
* È anche possibile implementare la [scalabilità automatica][apim-autoscale].
* La [distribuzione in più aree][apim-multi-regions] abilita le opzioni di failover e può essere eseguita nel [livello Premium][apim-pricing].
* Considerare l'[integrazione con Azure Application Insights][azure-apim-ai], che espone le metriche tramite [Monitoraggio di Azure][azure-mon] per il monitoraggio.

## <a name="deployment"></a>Distribuzione

Per iniziare, [creare un'istanza di Gestione API di Azure nel portale][apim-create].

In alternativa, è possibile scegliere un [modello di avvio rapido][azure-quickstart-templates-apim] di Azure Resource Manager adatto al caso d'uso specifico.

## <a name="pricing"></a>Prezzi

Gestione API è disponibile in quattro livelli: Developer, Basic, Standard e Premium. È possibile trovare indicazioni dettagliate sulla differenza tra questi livelli nella [guida ai prezzi di Gestione API di Azure][apim-pricing].

I clienti possono sfruttare la scalabilità di Gestione API aggiungendo o rimuovendo unità. La capacità di ogni unità dipende dal livello.

> [!NOTE]
> Il livello Developer è utilizzabile per la valutazione delle funzionalità di Gestione API. Il livello Developer non deve essere usato per la produzione.

Per visualizzare i costi previsti ed eseguire la personalizzazione in base alle specifiche esigenze di distribuzione, è possibile modificare il numero di unità di scala e di istanze del servizio app nel [calcolatore dei prezzi di Azure][pricing-calculator].

## <a name="related-resources"></a>Risorse correlate

Vedere le estese informazioni disponibili [nella documentazione e negli articoli di riferimento][apim] su Gestione API di Azure.


<!-- links -->
[architecture]: ./media/architecture-apim-api-scenario.png
[apim-create]: /azure/api-management/get-started-create-service-instance
[apim-git]: /azure/api-management/api-management-configuration-repository-git
[apim-multi-regions]: /azure/api-management/api-management-howto-deploy-multi-region
[apim-autoscale]: /azure/api-management/api-management-howto-autoscale
[apim-scaleout]: /azure/api-management/upgrade-and-scale
[azure-apim-ai]: /azure/api-management/api-management-howto-app-insights
[azure-ai]: /azure/application-insights/
[azure-mon]: /azure/monitoring-and-diagnostics/monitoring-overview
[azure-appgw]: /azure/application-gateway/application-gateway-introduction
[apim-vnet-internal]: /azure/api-management/api-management-howto-integrate-internal-vnet-appgateway
[apim-vnet]: /azure/api-management/api-management-using-with-vnet
[azure-hybrid]: /azure/architecture/reference-architectures/hybrid-networking/
[azure-er]: /azure/expressroute/expressroute-introduction
[azure-vpn]: /azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[azure-vnet]: /azure/virtual-network/virtual-networks-overview
[azure-appservice-auth]: /azure/app-service/app-service-authentication-overview#identity-providers
[apim-faq-vip]: /azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules
[azure-appservice-ip-restrict]: /azure/app-service/app-service-ip-restrictions
[azure-api-apps]: /azure/app-service/
[apim-ssl]: /azure/api-management/api-management-howto-manage-protocols-ciphers
[apim-mutualcert-auth]: /azure/api-management/api-management-howto-mutual-certificates
[apim-whitelist-ip]: /azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules
[anti-corruption-layer-pattern]: /azure/architecture/patterns/anti-corruption-layer
[apim]: /azure/api-management/api-management-key-concepts
[apim-api-design-guidance]: /azure/architecture/best-practices/api-design
[visualstudio-youtube-solid-design]: https://youtu.be/agkWYPUcLpg
[azure-vm-lift-shift]: https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/
[standard-pricing-calc]: https://azure.com/e/
[premium-pricing-calc]: https://azure.com/e/
[apim-pricing]: https://azure.microsoft.com/pricing/details/api-management/
[azure-quickstart-templates-apim]: https://azure.microsoft.com/resources/templates/?term=API+Management&pageNumber=1
[soap]: https://en.wikipedia.org/wiki/SOAP
[pricing-calculator]: https://azure.com/e/0e916a861fac464db61342d378cc0bd6
