---
title: 'CAF: distribuire un carico di lavoro di base'
description: Descrive come distribuire un carico di lavoro di base in Azure
author: petertaylor9999
ms.date: 12/31/2018
ms.openlocfilehash: bd3d006100e68f2fa1d71deeb0c72180ad4c19ea
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55902060"
---
# <a name="deploy-a-basic-workload-in-azure"></a>Distribuire un carico di lavoro di base in Azure

Il termine *carico di lavoro* viene in genere definito come un'unità arbitraria di funzionalità, ad esempio un'applicazione o un servizio. Per facilitare la comprensione, si può pensare a un carico di lavoro in termini di artefatti di codice distribuiti in un server, nonché di altri servizi specifici di un'applicazione. Questa può essere una definizione utile per un'applicazione o un servizio locale, ma nel caso delle applicazioni cloud tale definizione deve essere ampliata.

Nel cloud un carico di lavoro non include solo tutti gli artefatti, ma comprende anche le risorse cloud. Le risorse cloud sono incluse nella definizione in base a un concetto noto come "infrastruttura come codice". Come illustrato in [Funzionamento di Azure](../../getting-started/what-is-azure.md), le risorse in Azure vengono distribuite da un servizio agente di orchestrazione. Il servizio agente di orchestrazione espone la funzionalità tramite un'API Web, che è possibile chiamare tramite diversi strumenti, ad esempio PowerShell, l'interfaccia della riga di comando di Azure e il portale di Azure. Ciò significa che è possibile specificare risorse di Azure in un file leggibile dal computer che può essere archiviato insieme agli artefatti di codice associati all'applicazione.

In questo modo è possibile definire un carico di lavoro in termini di artefatti di codice e di risorse cloud necessarie, in modo da isolare ulteriormente i carichi di lavoro. È possibile isolare i carichi di lavoro in base al modo in cui sono organizzate le risorse, alla topologia di rete o ad altri attributi. L'obiettivo dell'isolamento dei carichi di lavoro è quello di associare le risorse specifiche di un carico di lavoro a un team, così che il team possa gestire in modo indipendente tutti gli aspetti di tali risorse. Ciò consente a più team di condividere i servizi di gestione delle risorse in Azure, impedendo l'eliminazione o la modifica non intenzionale delle risorse di altri team.

Questo isolamento rende possibile anche un altro concetto, noto come DevOps. DevOps include procedure di sviluppo software che comprendono sia lo sviluppo di software che le operazioni IT di cui sopra, con l'aggiunta dell'uso del massimo livello possibile di automazione. Uno dei principi relativi a DevOps è noto come integrazione continua e recapito continuo (CI/CD). L'integrazione continua si riferisce ai processi di compilazione automatizzati eseguiti ogni volta che uno sviluppatore esegue il commit di una modifica del codice. Il recapito continuo si riferisce ai processi automatizzati di distribuzione del codice in diversi ambienti, ad esempio un ambiente di sviluppo per i test o un ambiente di produzione per la distribuzione finale.

## <a name="basic-workload"></a>Carico di lavoro di base

Un *carico di lavoro di base* è in genere definito come applicazione Web singola o come rete virtuale con una macchina virtuale.

> [!NOTE]
> Questa guida non tratta lo sviluppo di applicazioni. Per altre informazioni sullo sviluppo di applicazioni in Azure, vedere [Guida all'architettura delle applicazioni in Azure](/azure/architecture/guide/).

Indipendentemente dal fatto che il carico di lavoro sia un'applicazione Web o una macchina virtuale, ognuna di queste distribuzioni richiede un *gruppo di risorse*. Un utente dotato dell'autorizzazione appropriata deve creare il gruppo di risorse prima di eseguire la procedura seguente.

## <a name="basic-web-application-paas"></a>Applicazione Web di base (PaaS)

Per un'applicazione Web di base, selezionare una delle guide introduttive di 5 minuti dalla [documentazione delle app Web](/azure/app-service?toc=/azure/architecture/cloud-adoption-guide/toc.json) e seguire le procedure.

> [!NOTE]
> Alcune delle guide introduttive distribuiscono un gruppo di risorse per impostazione predefinita. In questo caso non è necessario creare un gruppo di risorse in modo esplicito. In caso contrario, distribuire l'applicazione Web nel gruppo di risorse creato in precedenza.

Dopo aver distribuito un carico di lavoro semplice, è possibile ottenere altre informazioni sulle procedure collaudate per la distribuzione di un'[applicazione Web di base](/azure/architecture/reference-architectures/app-service-web-app/basic-web-app?toc=/azure/architecture/cloud-adoption-guide/toc.json) in Azure.

## <a name="single-windows-or-linux-vm-iaas"></a>Singola VM Windows o Linux (IaaS)

Per un carico di lavoro semplice che viene eseguito in una VM, il primo passo è distribuire una rete virtuale. Tutte le risorse IaaS (Infrastructure as a Service, infrastruttura distribuita come servizio) in Azure, come le macchine virtuali, i servizi di bilanciamento del carico e i gateway, richiedono una rete virtuale. Per altre informazioni sulle [reti virtuali di Azure](/azure/virtual-network/virtual-networks-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json), seguire i passaggi per [distribuire una rete virtuale in Azure tramite il portale](/azure/virtual-network/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json). Quando si specificano le impostazioni per la rete virtuale nel portale di Azure, assicurarsi di indicare il nome del gruppo di risorse creato in precedenza.

Il passo successivo è decidere se distribuire una singola VM Windows o Linux. Per una VM Windows, seguire i passaggi per [distribuire una VM Windows in Azure con il portale](/azure/virtual-machines/windows/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json). Anche in questo caso, quando si specificano le impostazioni per la macchina virtuale nel portale di Azure, indicare il nome del gruppo di risorse creato in precedenza.

Dopo aver seguito la procedura e distribuito la macchina virtuale, è possibile apprendere [procedure consolidate per l'esecuzione di una macchina virtuale Windows in Azure](/azure/architecture/reference-architectures/virtual-machines-windows/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json). Per una macchina virtuale Linux, seguire i passaggi per [distribuire una macchina virtuale Linux in Azure con il portale](/azure/virtual-machines/linux/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json). È anche possibile ottenere altre informazioni su [procedure consolidate per l'esecuzione di una macchina virtuale Linux in Azure](/azure/architecture/reference-architectures/virtual-machines-linux/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json).

## <a name="next-steps"></a>Passaggi successivi

Vedere [Architectural decision guides](../../decision-guides/overview.md) (Guide per le decisioni relative all'architettura) per informazioni su come usare i componenti di base dell'infrastruttura nel cloud di Azure.
