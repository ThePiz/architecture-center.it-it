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
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58249536"
---
# <a name="use-managed-services"></a><span data-ttu-id="0ea59-103">Usare servizi gestiti</span><span class="sxs-lookup"><span data-stu-id="0ea59-103">Use managed services</span></span>

## <a name="when-possible-use-platform-as-a-service-paas-rather-than-infrastructure-as-a-service-iaas"></a><span data-ttu-id="0ea59-104">Quando possibile, usare la piattaforma distribuita come servizio (PaaS) invece dell'infrastruttura distribuita come servizio (IaaS)</span><span class="sxs-lookup"><span data-stu-id="0ea59-104">When possible, use platform as a service (PaaS) rather than infrastructure as a service (IaaS)</span></span>

<span data-ttu-id="0ea59-105">L'infrastruttura distribuita come servizio (IaaS) è come una scatola di mattoncini per le costruzioni:</span><span class="sxs-lookup"><span data-stu-id="0ea59-105">IaaS is like having a box of parts.</span></span> <span data-ttu-id="0ea59-106">si può costruire tutto quello che si vuole, ma occorre assemblarlo autonomamente.</span><span class="sxs-lookup"><span data-stu-id="0ea59-106">You can build anything, but you have to assemble it yourself.</span></span> <span data-ttu-id="0ea59-107">I servizi gestiti sono più semplici da configurare e amministrare.</span><span class="sxs-lookup"><span data-stu-id="0ea59-107">Managed services are easier to configure and administer.</span></span> <span data-ttu-id="0ea59-108">Non è necessario eseguire il provisioning di macchine virtuali, configurare reti virtuali, gestire patch e aggiornamenti e occuparsi di tutto il sovraccarico restante associato all'esecuzione del software in una macchina virtuale.</span><span class="sxs-lookup"><span data-stu-id="0ea59-108">You don't need to provision VMs, set up VNets, manage patches and updates, and all of the other overhead associated with running software on a VM.</span></span>

<span data-ttu-id="0ea59-109">Si supponga, ad esempio, che l'applicazione necessiti di una coda di messaggi.</span><span class="sxs-lookup"><span data-stu-id="0ea59-109">For example, suppose your application needs a message queue.</span></span> <span data-ttu-id="0ea59-110">È possibile configurare il proprio servizio di messaggistica in una macchina virtuale, usando uno strumento come RabbitMQ.</span><span class="sxs-lookup"><span data-stu-id="0ea59-110">You could set up your own messaging service on a VM, using something like RabbitMQ.</span></span> <span data-ttu-id="0ea59-111">Ma il bus di servizio di Azure fornisce già funzionalità di messaggistica affidabili sotto forma di servizio ed è più semplice da configurare.</span><span class="sxs-lookup"><span data-stu-id="0ea59-111">But Azure Service Bus already provides reliable messaging as service, and it's simpler to set up.</span></span> <span data-ttu-id="0ea59-112">È sufficiente creare uno spazio dei nomi del bus di servizio (nell'ambito di uno script di distribuzione) e quindi chiamare il bus di servizio usando l'SDK del client.</span><span class="sxs-lookup"><span data-stu-id="0ea59-112">Just create a Service Bus namespace (which can be done as part of a deployment script) and then call Service Bus using the client SDK.</span></span>

<span data-ttu-id="0ea59-113">Naturalmente, l'applicazione può avere requisiti specifici che rendono più appropriato un approccio IaaS.</span><span class="sxs-lookup"><span data-stu-id="0ea59-113">Of course, your application may have specific requirements that make an IaaS approach more suitable.</span></span> <span data-ttu-id="0ea59-114">Tuttavia, anche se l'applicazione è basata su IaaS, identificare scenari in cui potrebbe essere naturale incorporare i servizi gestiti,</span><span class="sxs-lookup"><span data-stu-id="0ea59-114">However, even if your application is based on IaaS, look for places where it may be natural to incorporate managed services.</span></span> <span data-ttu-id="0ea59-115">tra cui cache, code e archiviazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="0ea59-115">These include cache, queues, and data storage.</span></span>

| <span data-ttu-id="0ea59-116">Invece di eseguire...</span><span class="sxs-lookup"><span data-stu-id="0ea59-116">Instead of running...</span></span> | <span data-ttu-id="0ea59-117">Considerare l'uso di...</span><span class="sxs-lookup"><span data-stu-id="0ea59-117">Consider using...</span></span> |
|-----------------------|-------------|
| <span data-ttu-id="0ea59-118">Active Directory</span><span class="sxs-lookup"><span data-stu-id="0ea59-118">Active Directory</span></span> | <span data-ttu-id="0ea59-119">Servizi di dominio Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="0ea59-119">Azure Active Directory Domain Services</span></span> |
| <span data-ttu-id="0ea59-120">Elasticsearch</span><span class="sxs-lookup"><span data-stu-id="0ea59-120">Elasticsearch</span></span> | <span data-ttu-id="0ea59-121">Ricerca di Azure</span><span class="sxs-lookup"><span data-stu-id="0ea59-121">Azure Search</span></span> |
| <span data-ttu-id="0ea59-122">Hadoop</span><span class="sxs-lookup"><span data-stu-id="0ea59-122">Hadoop</span></span> | <span data-ttu-id="0ea59-123">HDInsight</span><span class="sxs-lookup"><span data-stu-id="0ea59-123">HDInsight</span></span> |
| <span data-ttu-id="0ea59-124">IIS</span><span class="sxs-lookup"><span data-stu-id="0ea59-124">IIS</span></span> | <span data-ttu-id="0ea59-125">Servizio app</span><span class="sxs-lookup"><span data-stu-id="0ea59-125">App Service</span></span> |
| <span data-ttu-id="0ea59-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0ea59-126">MongoDB</span></span> | <span data-ttu-id="0ea59-127">Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="0ea59-127">Cosmos DB</span></span> |
| <span data-ttu-id="0ea59-128">Redis</span><span class="sxs-lookup"><span data-stu-id="0ea59-128">Redis</span></span> | <span data-ttu-id="0ea59-129">Cache Redis di Azure</span><span class="sxs-lookup"><span data-stu-id="0ea59-129">Azure Redis Cache</span></span> |
| <span data-ttu-id="0ea59-130">SQL Server</span><span class="sxs-lookup"><span data-stu-id="0ea59-130">SQL Server</span></span> | <span data-ttu-id="0ea59-131">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="0ea59-131">Azure SQL Database</span></span> |
