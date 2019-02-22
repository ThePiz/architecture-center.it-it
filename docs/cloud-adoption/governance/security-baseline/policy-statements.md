---
title: 'CAF: Definizioni dei criteri di esempio per la baseline di sicurezza'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Definizioni dei criteri di esempio per la baseline di sicurezza
author: BrianBlanchard
ms.openlocfilehash: ac40e022f8dff0c3021c04cd9d6ae42d28afaf1f
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55901383"
---
# <a name="security-baseline-sample-policy-statements"></a>Definizioni dei criteri di esempio per la baseline di sicurezza

Le singole definizioni dei criteri del cloud sono linee guida per affrontare i rischi specifici identificati durante il processo di valutazione dei rischi. Queste definizioni devono fornire un riassunto conciso dei rischi e dei piani per gestirli. Ogni definizione di istruzione deve includere i tipi di informazioni seguenti:

- Rischio tecnico - Riepilogo del rischio a cui sono applicabili questi criteri.
- Definizione dei criteri - Una spiegazione chiara e riepilogativa dei requisiti dei criteri.
- Opzioni di progettazione: suggerimenti pratici, specifiche o altre linee guida che gli sviluppatori e i team IT possono usare quando implementano i criteri.

Gli esempi seguenti di definizioni dei criteri riguardano alcuni rischi aziendali comuni relativi alla sicurezza e vengono forniti a scopo di riferimento per creare effettive definizioni di criteri in base alle esigenze della propria organizzazione. Questi esempi non devono essere considerati vincolanti. Esistono potenzialmente diverse opzioni per definire criteri che consentano di affrontare qualsiasi tipo di rischio. Lavorare a stretto contatto con i team responsabili delle attività aziendali, della sicurezza e dell'IT per identificare le migliori soluzioni di criteri per i propri particolari rischi di sicurezza.  

## <a name="asset-classification"></a>Classificazione degli asset

**Rischio tecnico**: è possibile che gli asset non correttamente identificati come cruciali o che interessano dati sensibili non siano sufficientemente protetti, con conseguenti potenziali perdite di dati o interruzioni delle attività aziendali.

**Definizione dei criteri**: tutti gli asset distribuiti devono essere ordinati in categorie in base a criticità e classificazione dei dati. Le classificazioni devono essere esaminate dal team di governance del cloud e dal proprietario dell'applicazione prima della distribuzione nel cloud.

**Opzione di progettazione potenziale**: stabilire [standard di applicazione di tag alle risorse](../../decision-guides/resource-tagging/overview.md) e assicurarsi che il personale IT li applichi in modo coerente alle risorse distribuite usando i [tag delle risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags).

## <a name="data-encryption"></a>Crittografia dei dati

**Rischio tecnico**: esiste il rischio che i dati protetti vengano esposti durante l'archiviazione.

**Definizione dei criteri**: tutti i dati protetti devono essere crittografati quando inattivi.

**Opzione di progettazione potenziale**: vedere l'articolo [Panoramica della crittografia di Azure](/azure/security/security-azure-encryption-overview) per informazioni su come viene eseguita la crittografia dei dati inattivi nella piattaforma Azure.  

## <a name="network-isolation"></a>Isolamento rete

**Rischio tecnico**: la connettività tra le reti e le subnet all'interno delle reti introduce potenziali vulnerabilità che possono comportare perdite di dati o interruzioni di servizi critici.

**Definizione dei criteri**: le subnet di rete contenenti dati protetti devono essere isolate dalle altre subnet. Il traffico di rete tra le subnet di dati protetti deve essere controllato regolarmente.

**Opzione di progettazione potenziale**: In Azure l'isolamento delle reti e delle subnet viene gestito con [Reti virtuali di Azure](/azure/virtual-network/virtual-networks-overview).

## <a name="secure-external-access"></a>Accesso esterno sicuro

**Rischio tecnico**: consentire l'accesso ai carichi di lavoro dalla rete Internet pubblica introduce un rischio di intrusione con conseguente esposizione non autorizzata dei dati e interruzione delle attività aziendali.

**Definizione dei criteri**: alle subnet contenenti dati protetti non deve essere possibile accedere direttamente tramite la rete Internet pubblica o tra data center. L'accesso a tali subnet deve essere instradato tramite una subnet intermedia. Tutti gli accessi a tali subnet devono passare attraverso una soluzione firewall in grado di eseguire la scansione dei pacchetti e applicare funzioni di blocco.

**Opzione di progettazione potenziale**: in Azure proteggere gli endpoint pubblici distribuendo una [rete perimetrale tra la rete Internet pubblica e la rete basata sul cloud](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

## <a name="ddos-protection"></a>Protezione DDoS

**Rischio tecnico**: gli attacchi Distributed Denial of Service (DDoS) possono comportare un'interruzione delle attività aziendali.

**Definizione dei criteri**: distribuire meccanismi automatizzati di mitigazione degli attacchi DDoS in tutti gli endpoint di rete accessibili pubblicamente.

**Opzione di progettazione potenziale**: usare [Protezione DDoS di Azure](/azure/virtual-network/ddos-protection-overview) per ridurre al minimo le interruzioni causate dagli attacchi DDoS.

## <a name="secure-on-premises-connectivity"></a>Proteggere la connettività locale

**Rischio tecnico**: il traffico non crittografato tra la rete cloud e l'ambiente locale tramite la rete Internet pubblica è vulnerabile alle intercettazioni e comporta quindi il rischio di esposizione dei dati.

**Definizione dei criteri**: tutte le connessioni tra le reti locali e cloud devono essere stabilite tramite una connessione VPN sicura crittografata o un collegamento WAN dedicato privato.

**Opzione di progettazione potenziale**: in Azure usare ExpressRoute o la VPN di Azure per stabilire connessioni private tra le reti locali e cloud.

## <a name="network-monitoring-and-enforcement"></a>Monitoraggio della rete e applicazione di controlli

**Rischio tecnico**: le modifiche alla configurazione della rete possono comportare nuove vulnerabilità e rischi di esposizione dei dati.

**Definizione dei criteri**: gli strumenti di governance devono controllare e soddisfare i requisiti di configurazione definiti dal team della baseline di sicurezza.

**Opzione di progettazione potenziale**: In Azure l'attività di rete può essere monitorata con [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview), mentre il [Centro sicurezza di Azure](/azure/security-center/security-center-network-recommendations) consente di identificare le vulnerabilità di sicurezza. Criteri di Azure consente di limitare le risorse di rete e i criteri di configurazione delle risorse in base ai limiti definiti dal team responsabile della sicurezza.

## <a name="security-review"></a>Verifica della sicurezza

**Rischio tecnico**: nel corso del tempo, emergono nuove minacce alla sicurezza e tipi di attacchi, che aumentano il rischio di esposizione o di interruzione delle risorse cloud.

**Definizione dei criteri**: tendenze e potenziali exploit che possono influire sulle distribuzioni cloud devono essere esaminati regolarmente dal team responsabile della sicurezza in modo da fornire aggiornamenti per gli strumenti della baseline di sicurezza usati nel cloud.

**Opzione di progettazione potenziale**: pianificare riunioni periodiche con i membri principali dei team responsabili dell'IT e della governance al fine di verificare la sicurezza. Verificare i dati e le metriche di sicurezza per identificare le lacune negli strumenti e nei criteri della baseline di sicurezza correnti e aggiornare i criteri per attenuare nuovi possibili rischi.

## <a name="next-steps"></a>Passaggi successivi

Usare gli esempi citati in questo articolo come punto di partenza per elaborare criteri applicabili a rischi per la sicurezza specifici, in linea con i piani di adozione del cloud.

Per iniziare a sviluppare definizioni dei criteri personalizzate correlate alla baseline di sicurezza, scaricare il [modello di baseline di sicurezza](template.md).

Per accelerare l'adozione di questa disciplina, scegliere il [percorso di governance di utilità pratica](../journeys/overview.md) più adatto al proprio ambiente. Modificare quindi la progettazione per incorporare le decisioni relative a criteri aziendali specifici.

> [!div class="nextstepaction"]
> [Percorsi di governance di utilità pratica](../journeys/overview.md)
