---
title: "Adozione del cloud nell'organizzazione: funzionamento di Azure"
description: Spiegazione del funzionamento interno di Azure
author: petertaylor9999
ms.openlocfilehash: d98585decf46f81916030017443ff3d0ca986457
ms.sourcegitcommit: ae8a1de6f4af7a89a66a8339879843d945201f85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43327806"
---
# <a name="enterprise-cloud-adoption-how-does-azure-work"></a>Adozione del cloud nell'organizzazione: funzionamento di Azure

Azure è una piattaforma cloud pubblica di Microsoft. Azure offre un'ampia varietà di servizi di tipo piattaforma distribuita come servizio (PaaS), infrastruttura distribuita come servizio (IaaS), database distribuito come servizio (DBaaS) e molti altri. Ma che cos'è esattamente Azure e come funziona?

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ixGo] 

Come altre piattaforme cloud, Azure si basa su una tecnologia nota come **virtualizzazione**. Quasi tutto l'hardware di computer può essere emulato in software perché è costituito semplicemente da un set di istruzioni codificate in silicone in modo permanente o semipermanente. Usando un livello di emulazione che associa le istruzioni del software a quelle dell'hardware, un componente hardware virtualizzato può essere eseguito nel software come un componente fisico realmente esistente.

In pratica, il cloud è costituito da un set di server fisici in uno o più data center che eseguono hardware virtualizzato per conto dei clienti. In che modo il cloud riesce a creare, avviare, arrestare ed eliminare milioni di istanze di hardware virtualizzato per milioni di clienti allo stesso tempo?

Per comprendere questo concetto, si esaminerà l'architettura dell'hardware nel data center.  All'interno di ogni data center è presente un insieme di server disposti in rack. Ogni rack di server contiene numerosi **blade** con un commutatore per la connettività di rete e un'unità PDU (Power Distribution Unit) per l'alimentazione. I rack sono talvolta raggruppati in unità più grandi, dette **cluster**. 

All'interno di ogni rack o cluster, la maggior parte dei server ha il compito di eseguire le istanze di hardware virtualizzato per conto degli utenti. Alcuni server eseguono invece il software per la gestione del cloud, noto come controller di infrastruttura. Il **controller di infrastruttura** è un'applicazione distribuita a cui sono affidati numerosi compiti, tra cui l'allocazione dei servizi, il monitoraggio dell'integrità dei server e dei servizi in esecuzione su di essi e la correzione di eventuali errori dei server.

Ogni istanza del controller di infrastruttura è connessa a un altro set di server che eseguono il software di orchestrazione del cloud, in genere denominato **front-end**. Il front-end ospita i servizi Web, le API RESTful e i database interni di Azure usati per tutte le funzioni eseguite dal cloud. 

Tra i servizi ospitati dal front-end sono inclusi quelli che gestiscono le richieste dei clienti per allocare le risorse di Azure, ad esempio le [reti virtuali][vnet], le [macchine virtuali][vms] e i servizi come [CosmosDB][cosmosdb]. Per prima cosa, il front-end convalida l'utente e verifica che sia autorizzato ad allocare le risorse richieste. In caso affermativo, il front-end cerca in un database un rack di server con capacità sufficiente e quindi indica al controller di infrastruttura nel rack di allocare la risorsa.

In ultima analisi, Azure è semplicemente un enorme insieme di server e componenti hardware di rete, unito a un set complesso di applicazioni distribuite che orchestrano la configurazione e il funzionamento del software e dell'hardware virtualizzato su tali server. È proprio questa orchestrazione a rendere Azure così potente. Gli utenti non sono più responsabili della gestione e dell'aggiornamento dell'hardware, poiché tutte queste attività vengono svolte dietro le quinte da Azure. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compreso il funzionamento interno di Azure, leggere le informazioni sulla [governance dell'accesso alle risorse](what-is-governance.md). 

> [!div class="nextstepaction"]
> [Informazioni sulla governance delle risorse](what-is-governance.md)

<!-- Links -->

[cosmosdb]: /azure/cosmos-db/introduction
[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[vms]: /azure/virtual-machines/
[vnet]: /azure/virtual-network/virtual-networks-overview
