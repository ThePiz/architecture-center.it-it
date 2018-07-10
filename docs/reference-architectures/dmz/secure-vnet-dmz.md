---
title: Implementazione di una rete perimetrale tra Azure e Internet
description: Come implementare un'architettura di rete ibrida sicura con accesso a Internet in Azure.
author: telmosampaio
ms.date: 07/02/2018
pnp.series.title: Network DMZ
pnp.series.next: nva-ha
pnp.series.prev: secure-vnet-hybrid
cardTitle: DMZ between Azure and the Internet
ms.openlocfilehash: 7a062d2394ae8b3bd1b17c19cbdf512327f9a766
ms.sourcegitcommit: 9b459f75254d97617e16eddd0d411d1f80b7fe90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403148"
---
# <a name="dmz-between-azure-and-the-internet"></a>Rete perimetrale tra Azure e Internet

Questa architettura di riferimento consente di visualizzare una rete ibrida sicura che estende una rete locale in Azure e accetta anche il traffico Internet. [**Distribuire questa soluzione**.](#deploy-the-solution)

[![0]][0] 

*Scaricare un [file Visio][visio-download] di questa architettura.*

Questa architettura di riferimento amplia l'architettura descritta in [Implementing a DMZ between Azure and your on-premises datacenter][implementing-a-secure-hybrid-network-architecture] (Implementazione di una rete perimetrale tra Azure e il data center locale). Tale architettura aggiunge una rete perimetrale pubblica che gestisce il traffico Internet, in aggiunta alla rete perimetrale privata che gestisce il traffico proveniente dalla rete locale 

Gli usi tipici di questa architettura includono:

* Applicazioni ibride in cui i carichi di lavoro vengono eseguiti in parte localmente e in parte in Azure.
* Infrastruttura di Azure che instrada il traffico in ingresso proveniente da reti locali e da Internet.

## <a name="architecture"></a>Architecture

L'architettura è costituita dai componenti seguenti.

* **Indirizzo IP Pubblico (PIP)**. L'indirizzo IP dell'endpoint pubblico. Gli utenti esterni connessi a Internet possono accedere al sistema attraverso questo indirizzo.
* **Appliance virtuale di rete (NVA)**. Questa architettura include un pool separato di NVA per il traffico che ha origine in Internet.
* **Azure Load Balancer**. Tutte le richieste in ingresso provenienti da Internet passano attraverso il bilanciamento del carico e vengono distribuite alle NVA nella rete perimetrale pubblica.
* **Subnet in ingresso della rete perimetrale pubblica**. Questa subnet accetta le richieste provenienti da Azure Load Balancer. Le richieste in ingresso vengono passate a una delle NVA nella rete perimetrale pubblica.
* **Subnet in uscita della rete perimetrale pubblica**. Le richieste approvate dalla NVA passano attraverso questa subnet verso il bilanciamento del carico interno per il livello Web.

## <a name="recommendations"></a>Raccomandazioni

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda. 

### <a name="nva-recommendations"></a>Raccomandazioni per le appliance virtuali di rete

È consigliabile usare un set di NVA per il traffico che ha origine in Internet e un altro per il traffico che ha origine in locale. L'uso di un solo set di NVA per entrambi i tipi di traffico è un rischio per la sicurezza, poiché non fornisce alcun perimetro di sicurezza tra i due set di traffico di rete. L'uso di NVA separate riduce la complessità del controllo delle regole di sicurezza e definisce chiaramente la corrispondenza tra le regole e le richieste di rete in ingresso. Un set di NVA implementa le regole per il solo traffico Internet, mentre un altro set implementa le regole relative al solo traffico locale.

Includere una NVA di livello 7 per terminare le connessioni dell'applicazione al livello della NVA e mantenere la compatibilità con i livelli back-end. In questo modo si garantisce la connettività simmetrica in cui il traffico di risposta proveniente dai livelli back-end viene restituito tramite la NVA.  

### <a name="public-load-balancer-recommendations"></a>Raccomandazioni sul servizio di bilanciamento del carico pubblico

Per la scalabilità e la disponibilità, distribuire le NVA della rete perimetrale pubblica in un [set di disponibilità][availability-set] e usare un [bilanciamento del carico con connessione Internet][load-balancer] per distribuire le richieste Internet tra le NVA nel set di disponibilità.  

Configurare il bilanciamento del carico per accettare richieste solo sulle porte necessarie al traffico Internet. Ad esempio, limitare le richieste HTTP in ingresso alla porta 80 e le richieste HTTPS in ingresso alla porta 443.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Anche se inizialmente l'architettura richiede una singola NVA nella rete perimetrale pubblica, è consigliabile inserire un bilanciamento del carico davanti a tale rete fin dall'inizio: questo renderà più facile eseguire la scalabilità tra più NVA in futuro, se necessario.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Il servizio di bilanciamento del carico con connessione Internet richiede che ogni NVA nella subnet in ingresso della rete perimetrale pubblica implementi un [probe integrità][lb-probe]. Un probe integrità che non risponda a questo endpoint viene considerato non disponibile e il bilanciamento del carico invierà le richieste ad altre NVA presenti nello stesso set di disponibilità. Si noti che l'applicazione non verrà eseguita in caso di non risposta da parte di tutte le NVA, pertanto è importante che il monitoraggio sia configurato perché avvisi DevOps quando il numero di istanze di integrità di NVA scende al di sotto di una soglia definita.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Il monitoraggio e la gestione delle appliance virtuali di rete nella rete perimetrale pubblica devono essere eseguiti dal jumpbox nella subnet di gestione. Come descritto in [Implementing a DMZ between Azure and your on-premises datacenter][implementing-a-secure-hybrid-network-architecture] (Implementazione di una rete perimetrale tra Azure e il data center locale), definire una route di rete singola proveniente dalla rete locale al jumpbox tramite il gateway, per limitare l'accesso.

Se la connettività gateway dalla rete locale ad Azure non è attiva, è comunque possibile raggiungere il jumpbox attraverso la distribuzione di un indirizzo IP pubblico, aggiungendolo al jumpbox ed eseguendo la registrazione da Internet.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Questa architettura di riferimento implementa più livelli di sicurezza:

* Il servizio di bilanciamento del carico con connessione Internet invia le richieste alle NVA nella subnet della rete perimetrale pubblica in ingresso e solo sulle porte necessarie all'applicazione.
* Le regole dei gruppi di sicurezza di rete per le subnet (NSG) in ingresso e in uscita della rete perimetrale pubblica impediscono che le NVA vengano compromesse bloccando le richieste che non rientrano nelle regole NSG.
* La configurazione del routing NAT per le NVA invia le richieste in entrata sulle porte 80 e 443 al servizio di bilanciamento del carico di livello Web, ma ignora le richieste su tutte le altre porte.

È consigliabile registrare tutte le richieste in ingresso su tutte le porte. Controllare periodicamente i registri prestando attenzione alle richieste che non rientrano nei parametri previsti, in quanto queste potrebbero indicare tentativi di intrusione.


## <a name="deploy-the-solution"></a>Distribuire la soluzione

È disponibile una distribuzione per un'architettura di riferimento che implementa queste raccomandazioni su [GitHub][github-folder]. 

### <a name="prerequisites"></a>prerequisiti

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-resources"></a>Distribuire le risorse

1. Passare alla cartella `/dmz/secure-vnet-hybrid` del repository GitHub di architetture di riferimento.

2. Eseguire il comando seguente:

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p onprem.json --deploy
    ```

3. Eseguire il comando seguente:

    ```bash
    azbb -s <subscription_id> -g <resource_group_name> -l <region> -p secure-vnet-hybrid.json --deploy
    ```

### <a name="connect-the-on-premises-and-azure-gateways"></a>Connettere i gateway locali e di Azure

In questo passaggio verranno connessi i due gateway di rete locali.

1. Nel portale di Azure passare al gruppo di risorse creato. 

2. Trovare la risorsa denominata `ra-vpn-vgw-pip` e copiare l'indirizzo IP mostrato nel pannello **Panoramica**.

3. Trovare la risorsa denominata `onprem-vpn-lgw`.

4. Fare clic sul pannello **Configurazione**. In **Indirizzo IP** incollare l'indirizzo IP dal passaggio 2.

    ![](./images/local-net-gw.png)

5. Fare clic su **Salva** e attendere il completamento dell'operazione. L'operazione potrebbe richiedere circa 5 minuti.

6. Trovare la risorsa denominata `onprem-vpn-gateway1-pip`. Copiare l'indirizzo IP mostrato nel pannello **Panoramica**.

7. Trovare la risorsa denominata `ra-vpn-lgw`. 

8. Fare clic sul pannello **Configurazione**. In **Indirizzo IP** incollare l'indirizzo IP dal passaggio 6.

9. Fare clic su **Salva** e attendere il completamento dell'operazione.

10. Per verificare la connessione, passare al pannello **Connessioni** per ogni gateway. Lo stato dovrebbe essere **Connesso**.

### <a name="verify-that-network-traffic-reaches-the-web-tier"></a>Verificare che il traffico di rete raggiunga il livello Web

1. Nel portale di Azure passare al gruppo di risorse creato. 

2. Trovare la risorsa denominata `pub-dmz-lb`, che corrisponde al servizio di bilanciamento del carico situato davanti alla rete perimetrale pubblica. 

3. Copiare l'indirizzo IP pubblico dal pannello **Panoramica** e aprire tale indirizzo in un Web browser. Dovrebbe essere visualizzata la home page predefinita del server Apache2.

4. Trovare la risorsa denominata `int-dmz-lb`, che corrisponde al servizio di bilanciamento del carico situato davanti alla rete perimetrale privata. Copiare l'indirizzo IP privato dal pannello **Panoramica**.

5. Trovare la macchina virtuale denominata `jb-vm1`. Fare clic su **Connetti** e usare Desktop remoto per connettersi alla VM. Il nome utente e la password sono specificati nel file onprem.json.

6. Dalla sessione di Desktop remoto aprire un Web browser e passare all'indirizzo IP specificato nel passaggio 4. Dovrebbe essere visualizzata la home page predefinita del server Apache2.

[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/dmz/secure-vnet-dmz

[implementing-a-secure-hybrid-network-architecture]: ./secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview
[load-balancer]: /azure/load-balancer/load-balancer-Internet-overview
[network-security-group]: /azure/virtual-network/virtual-networks-nsg

[visio-download]: https://archcenter.blob.core.windows.net/cdn/dmz-reference-architectures.vsdx


[0]: ./images/dmz-public.png "Architettura di rete ibrida sicura"
