---
title: 'CAF: Funzionamento di Azure'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
description: Spiegazione del funzionamento interno di Azure
author: petertaylor9999
ms.date: 02/11/2019
ms.openlocfilehash: 215e4e4954a2f3e722e3ac865fea19508f6edadd
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068821"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-azure-work"></a>Funzionamento di Azure

Azure è una piattaforma cloud pubblica di Microsoft. Azure offre un'ampia raccolta di servizi tra cui piattaforma distribuita come servizio (PaaS), infrastruttura distribuita come servizio (IaaS), database distribuito come una funzionalità del servizio (DBaaS). Ma che cos'è esattamente Azure e come funziona?

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ixGo]

<!-- markdownlint-enable MD034 -->

Come altre piattaforme cloud, Azure si basa su una tecnologia nota come **virtualizzazione**. Quasi tutto l'hardware di computer può essere emulato in software perché è costituito semplicemente da un set di istruzioni codificate in silicone in modo permanente o semipermanente. Usando un livello di emulazione che associa le istruzioni del software a quelle dell'hardware, un componente hardware virtualizzato può essere eseguito nel software come un componente fisico realmente esistente.

In pratica, il cloud è costituito da un set di server fisici in uno o più data center che eseguono hardware virtualizzato per conto dei clienti. In che modo il cloud riesce a creare, avviare, arrestare ed eliminare milioni di istanze di hardware virtualizzato per milioni di clienti allo stesso tempo?

Per comprendere questo concetto, si esaminerà l'architettura dell'hardware nel data center.  All'interno di ogni Data Center è una raccolta di server disposti in rack. Ogni rack di server contiene numerosi **blade** con un commutatore per la connettività di rete e un'unità PDU (Power Distribution Unit) per l'alimentazione. I rack sono talvolta raggruppati in unità più grandi, dette **cluster**.

All'interno di ogni rack o cluster, la maggior parte dei server ha il compito di eseguire le istanze di hardware virtualizzato per conto degli utenti. Tuttavia, alcuni server eseguire il software di gestione cloud noto come un controller di infrastruttura. Il **controller di infrastruttura** è un'applicazione distribuita a cui sono affidati numerosi compiti, tra cui l'allocazione dei servizi, il monitoraggio dell'integrità dei server e dei servizi in esecuzione su di essi e la correzione di eventuali errori dei server.

Ogni istanza del controller di infrastruttura è connessa a un altro set di server che eseguono il software di orchestrazione del cloud, in genere denominato **front-end**. Il front-end ospita i servizi Web, le API RESTful e i database interni di Azure usati per tutte le funzioni eseguite dal cloud.

Ad esempio, il front-end ospita i servizi che gestiscono le richieste dei clienti per allocare le risorse di Azure, ad esempio [reti virtuali](/azure/virtual-network/virtual-networks-overview), [macchine virtuali](/azure/virtual-machines)e i servizi come [Cosmos DB](/azure/cosmos-db/introduction). Per prima cosa, il front-end convalida l'utente e verifica che sia autorizzato ad allocare le risorse richieste. In questo caso, il front-end controlla un database per individuare un rack di server con capacità sufficiente e quindi indica il controller di infrastruttura in tale rack di allocare le risorse.

Quindi, fondamentalmente, Azure è un enorme insieme di server e componenti hardware di rete che esegue un set complesso di applicazioni distribuite per orchestrare la configurazione e il funzionamento del software e hardware virtualizzato su tali server. È questa orchestrazione a rende Azure così potente &mdash; gli utenti non sono più responsabili per la manutenzione e l'aggiornamento dell'hardware perché ci pensa Azure tutto ciò dietro le quinte.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono i meccanismi interni di Azure, informazioni sulla governance delle risorse cloud.

> [!div class="nextstepaction"]
> [Informazioni sulla governance delle risorse](what-is-governance.md)

<!-- Links -->

[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
