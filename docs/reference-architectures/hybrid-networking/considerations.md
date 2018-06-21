---
title: Scegliere una soluzione per la connessione di una rete locale ad Azure.
description: Confrontare le architetture di riferimento per connettersi a una rete locale in Azure.
author: telmosampaio
ms.date: 04/06/2017
ms.openlocfilehash: 274b9df1817632a7f3eaafa8bf02e965fdc3feea
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
ms.locfileid: "24541050"
---
# <a name="choose-a-solution-for-connecting-an-on-premises-network-to-azure"></a><span data-ttu-id="0ec3d-103">Scegliere una soluzione per la connessione di una rete locale ad Azure.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-103">Choose a solution for connecting an on-premises network to Azure</span></span>

<span data-ttu-id="0ec3d-104">In questo articolo vengono confrontate le opzioni per la connessione di una rete locale a una rete virtuale di Azure (VNet).</span><span class="sxs-lookup"><span data-stu-id="0ec3d-104">This article compares options for connecting an on-premises network to an Azure Virtual Network (VNet).</span></span> <span data-ttu-id="0ec3d-105">Viene fornita un'architettura di riferimento e una soluzione distribuibile per ogni opzione.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-105">We provide a reference architecture and a deployable solution for each option.</span></span>

## <a name="vpn-connection"></a><span data-ttu-id="0ec3d-106">Connessione VPN</span><span class="sxs-lookup"><span data-stu-id="0ec3d-106">VPN connection</span></span>

<span data-ttu-id="0ec3d-107">Usare una rete privata virtuale (VPN) per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPSec.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-107">Use a virtual private network (VPN) to connect your on-premises network with an Azure VNet through an IPSec VPN tunnel.</span></span>

<span data-ttu-id="0ec3d-108">Questa architettura è adatta ad applicazioni ibride in cui è probabile che il traffico tra l'hardware locale e il cloud sia leggero o se si vuole scambiare la latenza leggermente estesa con la flessibilità e la potenza di elaborazione del cloud.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-108">This architecture is suitable for hybrid applications where the traffic between on-premises hardware and the cloud is likely to be light, or you are willing to trade slightly extended latency for the flexibility and processing power of the cloud.</span></span>

<span data-ttu-id="0ec3d-109">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="0ec3d-109">**Benefits**</span></span>

- <span data-ttu-id="0ec3d-110">Semplice da configurare.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-110">Simple to configure.</span></span>

<span data-ttu-id="0ec3d-111">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="0ec3d-111">**Challenges**</span></span>

- <span data-ttu-id="0ec3d-112">Richiede un dispositivo VPN locale.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-112">Requires an on-premises VPN device.</span></span>
- <span data-ttu-id="0ec3d-113">Nonostante Microsoft garantisca il 99,9% di disponibilità per ogni Gateway VPN, il contratto di servizio copre esclusivamente il gateway VPN e non la connessione di rete per il gateway.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-113">Although Microsoft guarantees 99.9% availability for each VPN Gateway, this SLA only covers the VPN gateway, and not your network connection to the gateway.</span></span>
- <span data-ttu-id="0ec3d-114">Attualmente una connessione VPN tramite il Gateway VPN di Azure supporta una larghezza di banda per un massimo di 200 Mbps.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-114">A VPN connection over Azure VPN Gateway currently supports a maximum of 200 Mbps bandwidth.</span></span> <span data-ttu-id="0ec3d-115">Potrebbe essere necessario partizionare la rete virtuale di Azure tra più connessioni VPN se si prevede di superare la velocità effettiva.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-115">You may need to partition your Azure virtual network across multiple VPN connections if you expect to exceed this throughput.</span></span>

<span data-ttu-id="0ec3d-116">**[Altre informazioni...][vpn]**</span><span class="sxs-lookup"><span data-stu-id="0ec3d-116">**[Read more...][vpn]**</span></span>

## <a name="azure-expressroute-connection"></a><span data-ttu-id="0ec3d-117">Connessione ExpressRoute di Azure</span><span class="sxs-lookup"><span data-stu-id="0ec3d-117">Azure ExpressRoute connection</span></span>

<span data-ttu-id="0ec3d-118">Le connessioni ExpressRoute usano una connessione privata dedicata tramite un provider di connettività di terze parti.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-118">ExpressRoute connections use a private, dedicated connection through a third-party connectivity provider.</span></span> <span data-ttu-id="0ec3d-119">La connessione privata estende la rete locale in Azure.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-119">The private connection extends your on-premises network into Azure.</span></span> 

<span data-ttu-id="0ec3d-120">Questa architettura è adatta ad applicazioni ibride che eseguono carichi di lavoro critici su larga scala e che richiedono un livello di scalabilità elevato.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-120">This architecture is suitable for hybrid applications running large-scale, mission-critical workloads that require a high degree of scalability.</span></span> 

<span data-ttu-id="0ec3d-121">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="0ec3d-121">**Benefits**</span></span>

- <span data-ttu-id="0ec3d-122">Disponibilità di una maggiore larghezza di banda, che può arrivare fino a 10 Gbps a seconda del provider di connettività.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-122">Much higher bandwidth available; up to 10 Gbps depending on the connectivity provider.</span></span>
- <span data-ttu-id="0ec3d-123">Supporta la scalabilità dinamica della larghezza di banda per ridurre i costi durante i periodi di minore richiesta.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-123">Supports dynamic scaling of bandwidth to help reduce costs during periods of lower demand.</span></span> <span data-ttu-id="0ec3d-124">Tuttavia, non tutti i provider di connettività dispongono di questa opzione.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-124">However, not all connectivity providers have this option.</span></span>
- <span data-ttu-id="0ec3d-125">Può consentire all'organizzazione l'accesso diretto ai cloud nazionali, a seconda del provider di connettività.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-125">May allow your organization direct access to national clouds, depending on the connectivity provider.</span></span>
- <span data-ttu-id="0ec3d-126">Offre il 99,9% di disponibilità del contratto di servizio attraverso l'intera stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-126">99.9% availability SLA across the entire connection.</span></span>

<span data-ttu-id="0ec3d-127">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="0ec3d-127">**Challenges**</span></span>

- <span data-ttu-id="0ec3d-128">La configurazione può risultare complessa.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-128">Can be complex to set up.</span></span> <span data-ttu-id="0ec3d-129">La creazione di una connessione ExpressRoute richiede l'uso di un provider di connettività di terze parti.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-129">Creating an ExpressRoute connection requires working with a third-party connectivity provider.</span></span> <span data-ttu-id="0ec3d-130">Il provider è responsabile del provisioning della connessione di rete.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-130">The provider is responsible for provisioning the network connection.</span></span>
- <span data-ttu-id="0ec3d-131">Richiede un router locale a larghezza di banda elevata.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-131">Requires high-bandwidth routers on-premises.</span></span>

<span data-ttu-id="0ec3d-132">**[Altre informazioni...][expressroute]**</span><span class="sxs-lookup"><span data-stu-id="0ec3d-132">**[Read more...][expressroute]**</span></span>

## <a name="expressroute-with-vpn-failover"></a><span data-ttu-id="0ec3d-133">ExpressRoute con failover VPN</span><span class="sxs-lookup"><span data-stu-id="0ec3d-133">ExpressRoute with VPN failover</span></span>

<span data-ttu-id="0ec3d-134">Questa opzione risulta essere una combinazione delle due precedenti, vale a dire l'uso di ExpressRoute in condizioni normali, ma l'esecuzione del failover in una connessione VPN se vi è una perdita di connettività del circuito ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-134">This options combines the previous two, using ExpressRoute in normal conditions, but failing over to a VPN connection if there is a loss of connectivity in the ExpressRoute circuit.</span></span>

<span data-ttu-id="0ec3d-135">Questa architettura è adatta ad applicazioni ibride che necessitano di una larghezza di banda di ExpressRoute maggiore, oltre a una connettività di rete a disponibilità elevata.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-135">This architecture is suitable for hybrid applications that need the higher bandwidth of ExpressRoute, and also require highly available network connectivity.</span></span> 

<span data-ttu-id="0ec3d-136">**Vantaggi**</span><span class="sxs-lookup"><span data-stu-id="0ec3d-136">**Benefits**</span></span>

- <span data-ttu-id="0ec3d-137">Disponibilità elevata se il circuito ExpressRoute ha esito negativo, anche se la connessione fallback si trova in una rete a larghezza di banda inferiore.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-137">High availability if the ExpressRoute circuit fails, although the fallback connection is on a lower bandwidth network.</span></span>

<span data-ttu-id="0ec3d-138">**Problematiche**</span><span class="sxs-lookup"><span data-stu-id="0ec3d-138">**Challenges**</span></span>

- <span data-ttu-id="0ec3d-139">Complesso da configurare.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-139">Complex to configure.</span></span> <span data-ttu-id="0ec3d-140">È necessario configurare sia una connessione VPN sia un circuito ExpressRoute.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-140">You need to set up both a VPN connection and an ExpressRoute circuit.</span></span>
- <span data-ttu-id="0ec3d-141">Richiede sia un hardware ridondante (dispositivi VPN) che una connessione Gateway VPN di Azure ridondante, entrambi soggetti a tariffa.</span><span class="sxs-lookup"><span data-stu-id="0ec3d-141">Requires redundant hardware (VPN appliances), and a redundant Azure VPN Gateway connection for which you pay charges.</span></span>

<span data-ttu-id="0ec3d-142">**[Altre informazioni...][expressroute-vpn-failover]**</span><span class="sxs-lookup"><span data-stu-id="0ec3d-142">**[Read more...][expressroute-vpn-failover]**</span></span>

<!-- links -->
[expressroute]: ./expressroute.md
[expressroute-vpn-failover]: ./expressroute-vpn-failover.md
[vpn]: ./vpn.md