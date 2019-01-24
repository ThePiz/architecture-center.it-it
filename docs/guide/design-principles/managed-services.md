---
title: Usare servizi gestiti
titleSuffix: Azure Application Architecture Guide
description: Quando possibile, usare una piattaforma distribuita come servizio (PaaS) anziché un'infrastruttura distribuita come servizio (IaaS).
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: f08914e1eacc4f02fdb16093fe590f46249e3da3
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485809"
---
# <a name="use-managed-services"></a>Usare servizi gestiti

## <a name="when-possible-use-platform-as-a-service-paas-rather-than-infrastructure-as-a-service-iaas"></a>Quando possibile, usare la piattaforma distribuita come servizio (PaaS) invece dell'infrastruttura distribuita come servizio (IaaS)

L'infrastruttura distribuita come servizio (IaaS) è come una scatola di mattoncini per le costruzioni: si può costruire tutto quello che si vuole, ma occorre assemblarlo autonomamente. I servizi gestiti sono più semplici da configurare e amministrare. Non è necessario eseguire il provisioning di macchine virtuali, configurare reti virtuali, gestire patch e aggiornamenti e occuparsi di tutto il sovraccarico restante associato all'esecuzione del software in una macchina virtuale.

Si supponga, ad esempio, che l'applicazione necessiti di una coda di messaggi. È possibile configurare il proprio servizio di messaggistica in una macchina virtuale, usando uno strumento come RabbitMQ. Ma il bus di servizio di Azure fornisce già funzionalità di messaggistica affidabili sotto forma di servizio ed è più semplice da configurare. È sufficiente creare uno spazio dei nomi del bus di servizio (nell'ambito di uno script di distribuzione) e quindi chiamare il bus di servizio usando l'SDK del client.

Naturalmente, l'applicazione può avere requisiti specifici che rendono più appropriato un approccio IaaS. Tuttavia, anche se l'applicazione è basata su IaaS, identificare scenari in cui potrebbe essere naturale incorporare i servizi gestiti, tra cui cache, code e archiviazione dei dati.

| Invece di eseguire... | Considerare l'uso di... |
|-----------------------|-------------|
| Active Directory | Servizi di dominio Azure Active Directory |
| Elasticsearch | Ricerca di Azure |
| Hadoop | HDInsight |
| IIS | Servizio app |
| MongoDB | Cosmos DB |
| Redis | Cache Redis di Azure |
| SQL Server | Database SQL di Azure |
