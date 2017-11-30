---
title: Implementazione di una rete perimetrale tra Azure e Internet
description: Come implementare un'architettura di rete ibrida sicura con accesso a Internet in Azure.
author: telmosampaio
ms.date: 11/23/2016
pnp.series.title: Network DMZ
pnp.series.next: nva-ha
pnp.series.prev: secure-vnet-hybrid
cardTitle: DMZ between Azure and the Internet
ms.openlocfilehash: 372d5bb0fc0e3c272843e062210dec5c15b2b78a
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="dmz-between-azure-and-the-internet"></a>Rete perimetrale tra Azure e Internet

Questa architettura di riferimento consente di visualizzare una rete ibrida sicura che estende una rete locale in Azure e accetta anche il traffico Internet. 

[![0]][0] 

*Scaricare un [file di Visio][visio-download] di questa architettura.*

Questa architettura di riferimento amplia l'architettura descritta in [Implementing a DMZ between Azure and your on-premises datacenter][implementing-a-secure-hybrid-network-architecture] (Implementazione di una rete perimetrale tra Azure e il data center locale). Tale architettura aggiunge una rete perimetrale pubblica che gestisce il traffico Internet, in aggiunta alla rete perimetrale privata che gestisce il traffico proveniente dalla rete locale 

Gli usi tipici di questa architettura includono:

* Applicazioni ibride in cui i carichi di lavoro vengono eseguiti in parte localmente e in parte in Azure.
* Infrastruttura di Azure che instrada il traffico in ingresso proveniente da reti locali e da Internet.

## <a name="architecture"></a>Architettura

L'architettura è costituita dai componenti seguenti.

* **Indirizzo IP Pubblico (PIP)**. L'indirizzo IP dell'endpoint pubblico. Gli utenti esterni connessi a Internet possono accedere al sistema attraverso questo indirizzo.
* **Appliance virtuale di rete (NVA)**. Questa architettura include un pool separato di NVA per il traffico che ha origine in Internet.
* **Azure Load Balancer**. Tutte le richieste in ingresso provenienti da Internet passano attraverso il bilanciamento del carico e vengono distribuite alle NVA nella rete perimetrale pubblica.
* **Subnet in ingresso della rete perimetrale pubblica**. Questa subnet accetta le richieste provenienti da Azure Load Balancer. Le richieste in ingresso vengono passate a una delle NVA nella rete perimetrale pubblica.
* **Subnet in uscita della rete perimetrale pubblica**. Le richieste approvate dalla NVA passano attraverso questa subnet verso il bilanciamento del carico interno per il livello Web.

## <a name="recommendations"></a>Raccomandazioni

Le raccomandazioni seguenti sono valide per la maggior parte degli scenari. Seguire queste indicazioni, a meno che non si disponga di un requisito specifico che le escluda. 

### <a name="nva-recommendations"></a>Raccomandazioni sulle NVA

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

Il monitoraggio e la gestione delle NVA nella rete perimetrale pubblica devono essere eseguiti dal jumpbox nella subnet di gestione. Come descritto in [Implementing a DMZ between Azure and your on-premises datacenter][implementing-a-secure-hybrid-network-architecture] (Implementazione di una rete perimetrale tra Azure e il data center locale), definire una route di rete singola proveniente dalla rete locale al jumpbox tramite il gateway, per limitare l'accesso.

Se la connettività gateway dalla rete locale ad Azure non è attiva, è comunque possibile raggiungere il jumpbox attraverso la distribuzione di un indirizzo IP pubblico, aggiungendolo al jumpbox ed eseguendo la registrazione da Internet.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Questa architettura di riferimento implementa più livelli di sicurezza:

* Il servizio di bilanciamento del carico con connessione Internet invia le richieste alle NVA nella subnet della rete perimetrale pubblica in ingresso e solo sulle porte necessarie all'applicazione.
* Le regole dei gruppi di sicurezza di rete per le subnet (NSG) in ingresso e in uscita della rete perimetrale pubblica impediscono che le NVA vengano compromesse bloccando le richieste che non rientrano nelle regole NSG.
* La configurazione del routing NAT per le NVA invia le richieste in entrata sulle porte 80 e 443 al servizio di bilanciamento del carico di livello Web, ma ignora le richieste su tutte le altre porte.

È consigliabile registrare tutte le richieste in ingresso su tutte le porte. Controllare periodicamente i registri prestando attenzione alle richieste che non rientrano nei parametri previsti, in quanto queste potrebbero indicare tentativi di intrusione.

## <a name="solution-deployment"></a>Distribuzione della soluzione

È disponibile una distribuzione per un'architettura di riferimento che implementa queste raccomandazioni su [GitHub][github-folder]. L'architettura di riferimento può essere distribuita sia con Windows sia con le macchine virtuali di Linux seguendo le istruzioni riportate di seguito:

1. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2FvirtualNetwork.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
2. Dopo avere aperto il collegamento nel portale di Azure, è necessario immettere i valori per alcune impostazioni:
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-public-dmz-network-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Selezionare **Tipo di sistema operativo** dalla casella di riepilogo a discesa **Windows** o **Linux**.
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
3. Attendere il completamento della distribuzione.
4. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2Fworkload.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
5. Dopo avere aperto il collegamento nel portale di Azure, è necessario immettere i valori per alcune impostazioni:
   * Poiché il nome del **gruppo di risorse** è già definito nel file dei parametri, selezionare **Crea nuovo** e immettere `ra-public-dmz-wl-rg` nella casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
6. Attendere il completamento della distribuzione.
7. Fare clic sul pulsante seguente:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fdmz%2Fsecure-vnet-dmz%2Fsecurity.azuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
8. Dopo avere aperto il collegamento nel portale di Azure, è necessario immettere i valori per alcune impostazioni:
   * Poiché il nome del **Gruppo di risorse** è già definito nel file di parametri, selezionare **Usa esistente** e accedere `ra-public-dmz-network-rg` alla casella di testo.
   * Selezionare l'area dalla casella di riepilogo a discesa **Località**.
   * Non modificare le caselle di testo **Template Root Uri** (URI radice modello) né **Parameter Root Uri** (URI radice parametro).
   * Leggere i termini e le condizioni, quindi fare clic sulla casella di controllo **Accetto le condizioni riportate sopra**.
   * Fare clic sul pulsante **Acquista**.
9. Attendere il completamento della distribuzione.
10. I file di parametri includono una password e un nome utente amministratore hardcoded. È fortemente consigliato sostituirli immediatamente. Selezionare ogni macchina virtuale presente nella distribuzione nel portale di Azure, quindi fare clic su **Reimposta password** nel pannello **Supporto e risoluzione dei problemi**. Selezionare **Reimposta password** nella casella di riepilogo a discesa **Modalità**, quindi selezionare un nuovo **Nome utente** e una nuova **Password**. Fare clic sul pulsante **Aggiorna** per salvare.


[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/dmz/secure-vnet-dmz

[implementing-a-secure-hybrid-network-architecture]: ./secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: /azure/load-balancer/load-balancer-custom-probe-overview
[load-balancer]: /azure/load-balancer/load-balancer-Internet-overview
[network-security-group]: /azure/virtual-network/virtual-networks-nsg

[visio-download]: https://archcenter.azureedge.net/cdn/dmz-reference-architectures.vsdx


[0]: ./images/dmz-public.png "Architettura di rete ibrida sicura"