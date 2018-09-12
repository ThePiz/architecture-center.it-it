---
title: "Adozione del cloud nell'organizzazione: distribuire un carico di lavoro di base"
description: Descrive come distribuire un carico di lavoro di base in Azure
author: petertaylor9999
ms.openlocfilehash: d6dd8e107b4b9e48697c4c7bc4d32018eb79de0b
ms.sourcegitcommit: ae8a1de6f4af7a89a66a8339879843d945201f85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43327756"
---
# <a name="enterprise-cloud-adoption-deploy-a-basic-workload"></a>Adozione del cloud nell'organizzazione: distribuire un carico di lavoro di base

Il termine **carico di lavoro** viene in genere inteso come un'unità arbitraria di funzionalità, ad esempio un'applicazione o un servizio. Il carico di lavoro può essere immaginato in termini di elementi di codice distribuiti in un server, nonché di altri servizi necessari. Questa definizione è utile per un'applicazione o un servizio locale, ma nel cloud è necessario estenderla.

Nel cloud un carico di lavoro non include solo tutti gli elementi, ma comprende anche le risorse cloud. Le risorse cloud vengono incluse come parte della definizione in base a un concetto noto come infrastruttura come codice. Come illustrato nel [funzionamento di Azure](../getting-started/what-is-azure.md), le risorse in Azure vengono distribuite da un servizio agente di orchestrazione. Il servizio agente di orchestrazione espone questa funzionalità tramite un'API Web e tale API Web può essere chiamata usando diversi strumenti, ad esempio Powershell, l'interfaccia della riga di comando di Azure e il portale di Azure. Ciò significa che è possibile specificare le risorse in un file leggibile dal computer che può essere archiviato insieme agli elementi di codice associati all'applicazione.

In questo modo, è possibile definire un carico di lavoro in termini di elementi di codice e di risorse cloud necessarie, in modo da isolare ulteriormente i carichi di lavoro. I carichi di lavoro possono essere isolati in base al modo in cui sono organizzate le risorse, in base alla topologia di rete o in base ad altri attributi. L'obiettivo dell'isolamento dei carichi di lavoro è quello di associare le risorse specifiche di un carico di lavoro a un team, così che il team possa gestire in modo indipendente tutti gli aspetti di tali risorse. Ciò consente a più team di condividere i servizi di gestione delle risorse in Azure, impedendo l'eliminazione o la modifica non intenzionale delle risorse di altri team.

Questo isolamento rende possibile anche un altro concetto, noto come DevOps. DevOps include le procedure di sviluppo software che comprendono sia lo sviluppo di software che le operazioni IT, con l'aggiunta dell'uso del massimo livello possibile di automazione. Uno dei principi relativi a DevOps è noto come integrazione continua e recapito continuo (CI/CD). L'integrazione continua si riferisce ai processi di compilazione automatizzati eseguiti ogni volta che uno sviluppatore esegue il commit di una modifica del codice, mentre il recapito continuo si riferisce ai processi automatizzati di distribuzione del codice in diversi ambienti, ad esempio un ambiente di sviluppo per i test o un ambiente di produzione per la distribuzione finale.

## <a name="basic-workload"></a>Carico di lavoro di base

Un **carico di lavoro di base** è in genere definito come un'unica applicazione Web o una rete virtuale con la macchina virtuale. 

> [!NOTE]
> Questa guida non tratta lo sviluppo di applicazioni. Per altre informazioni sullo sviluppo di applicazioni in Azure, vedere [Guida all'architettura delle applicazioni in Azure](/azure/architecture/guide/).

Indipendentemente dal fatto che il carico di lavoro sia un'applicazione Web o una macchina virtuale, ognuna di queste distribuzioni richiede un **gruppo di risorse**. Un utente con l'autorizzazione per creare un gruppo di risorse deve farlo prima di seguire la procedura seguente.

## <a name="basic-web-application-paas"></a>Applicazione Web di base (PaaS)

Per un'applicazione Web di base, selezionare una delle guide introduttive di 5 minuti dalla [documentazione delle app Web](/azure/app-service?toc=/azure/architecture/cloud-adoption-guide/toc.json) e seguire le procedure. 

> [!NOTE]
> Alcune delle guide introduttive distribuiscono un gruppo di risorse per impostazione predefinita. In questo caso non è necessario creare un gruppo di risorse in modo esplicito. In caso contrario, distribuire l'applicazione Web nel gruppo di risorse creato in precedenza.

Dopo aver distribuito questo carico di lavoro semplice, è possibile ottenere altre informazioni sulle procedure collaudate per la distribuzione di un'[un'applicazione Web di base](/azure/architecture/reference-architectures/app-service-web-app/basic-web-app?toc=/azure/architecture/cloud-adoption-guide/toc.json) in Azure.

## <a name="single-windows-or-linux-vm-iaas"></a>Singola VM Windows o Linux (IaaS)

Per un carico di lavoro semplice che viene eseguito su una macchina virtuale, il primo passo è distribuire una rete virtuale. Tutte le risorse IaaS in Azure, come le macchine virtuali, i servizi di bilanciamento del carico e i gateway, richiedono una rete virtuale. Per altre informazioni sulle [reti virtuali di Azure](/azure/virtual-network/virtual-networks-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json), seguire i passaggi per [distribuire una rete virtuale in Azure tramite il portale](/azure/virtual-network/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json). Quando si specificano le impostazioni per la rete virtuale nel portale di Azure, indicare il nome del gruppo di risorse creato in precedenza.

Il passo successivo è decidere se distribuire una singola VM Windows o Linux. Per una macchina virtuale Windows, seguire i passaggi per [distribuire una macchina virtuale Windows in Azure con il portale](/azure/virtual-machines/windows/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json). Anche in questo caso, quando si specificano le impostazioni per la macchina virtuale nel portale di Azure, indicare il nome del gruppo di risorse creato in precedenza.

Dopo aver seguito la procedura e distribuito la macchina virtuale, è possibile apprendere [procedure consolidate per l'esecuzione di una macchina virtuale Windows in Azure](/azure/architecture/reference-architectures/virtual-machines-windows/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json). Per una macchina virtuale Linux, seguire i passaggi per [distribuire una macchina virtuale Linux in Azure con il portale](/azure/virtual-machines/linux/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json). È anche possibile ottenere altre informazioni su [procedure consolidate per l'esecuzione di una macchina virtuale Linux in Azure](/azure/architecture/reference-architectures/virtual-machines-linux/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json).