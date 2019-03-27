---
title: 'CAF: Reti definite dal software - Solo PaaS'
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Informazioni sul modello solo PaaS per le funzionalità di rete basate sul cloud
author: rotycenh
ms.openlocfilehash: 2f3f82d781ddb6544721e82e7b7d795222a2f8ff
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241202"
---
# <a name="software-defined-networks-paas-only"></a><span data-ttu-id="39b27-103">Reti definite dal software: solo PaaS</span><span class="sxs-lookup"><span data-stu-id="39b27-103">Software Defined Networks: PaaS-only</span></span>

<span data-ttu-id="39b27-104">Quando si implementa una risorsa piattaforma distribuita come servizio (PaaS), il processo di distribuzione crea automaticamente una rete sottostante presunta con un numero limitato di controlli su tale rete, inclusi bilanciamento del carico, blocco delle porte e connessioni ad altri servizi PaaS.</span><span class="sxs-lookup"><span data-stu-id="39b27-104">When you implement a platform as a service (PaaS) resource, the deployment process automatically creates an assumed underlying network with a limited number of controls over that network, including load balancing, port blocking, and connections to other PaaS services.</span></span>

<span data-ttu-id="39b27-105">In Azure diversi tipi di risorsa PaaS possono essere [distribuiti](/azure/virtual-network/virtual-network-for-azure-services) o [connessi a](/azure/virtual-network/virtual-network-service-endpoints-overview) una rete virtuale, consentendo a queste risorse di integrarsi con l'infrastruttura di rete virtuale esistente.</span><span class="sxs-lookup"><span data-stu-id="39b27-105">In Azure, several PaaS resource types can be [deployed into](/azure/virtual-network/virtual-network-for-azure-services) or [connected to](/azure/virtual-network/virtual-network-service-endpoints-overview) a virtual network, allowing these resources to integrate with your existing virtual networking infrastructure.</span></span> <span data-ttu-id="39b27-106">In molti casi, tuttavia, un'architettura di rete solo PaaS, basata esclusivamente su queste funzionalità di rete predefinite fornite in modo nativo dalle risorse PaaS, è sufficiente per soddisfare i requisiti relativi ai carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="39b27-106">However, in many cases a PaaS only networking architecture, relying only on these default networking capabilities natively provided by PaaS resources, is sufficient to meet workload requirements.</span></span>

<span data-ttu-id="39b27-107">Se si sta valutando un'architettura di rete solo PaaS, verificare che i presupposti obbligatori siano allineati ai requisiti.</span><span class="sxs-lookup"><span data-stu-id="39b27-107">If you are considering a PaaS only networking architecture, be sure you validate that the required assumptions align with your requirements.</span></span>

## <a name="paas-only-assumptions"></a><span data-ttu-id="39b27-108">Presupposti solo PaaS</span><span class="sxs-lookup"><span data-stu-id="39b27-108">PaaS-only assumptions</span></span>

<span data-ttu-id="39b27-109">La distribuzione di un'architettura di rete solo PaaS presuppone quanto segue:</span><span class="sxs-lookup"><span data-stu-id="39b27-109">Deploying a PaaS-only networking architecture assumes the following:</span></span>

- <span data-ttu-id="39b27-110">L'applicazione da distribuire è un'applicazione autonoma OPPURE dipende solo da altre risorse PaaS.</span><span class="sxs-lookup"><span data-stu-id="39b27-110">The application being deployed is a standalone application OR is dependent on only other PaaS resources.</span></span>
- <span data-ttu-id="39b27-111">I team responsabili delle operazioni IT possono aggiornare gli strumenti, il training e i processi per supportare la gestione, la configurazione e la distribuzione delle applicazioni PaaS autonome.</span><span class="sxs-lookup"><span data-stu-id="39b27-111">Your IT operations teams can update their tools, training, and processes to support management, configuration, and deployment of standalone PaaS applications.</span></span>
- <span data-ttu-id="39b27-112">L'applicazione PaaS non fa parte di un più ampio processo di migrazione cloud che includerà risorse IaaS.</span><span class="sxs-lookup"><span data-stu-id="39b27-112">The PaaS application is not part of a broader cloud migration effort that will include IaaS resources.</span></span>

<span data-ttu-id="39b27-113">Questi presupposti sono qualificatori minimi allineati alla distribuzione di una rete solo PaaS.</span><span class="sxs-lookup"><span data-stu-id="39b27-113">These assumptions are minimum qualifiers aligned to deploying a PaaS-only network.</span></span> <span data-ttu-id="39b27-114">Anche se questo approccio può allinearsi ai requisiti di distribuzione di una singola applicazione, il team responsabile dell'adozione cloud deve rispondere a queste domande con una prospettiva a lungo termine:</span><span class="sxs-lookup"><span data-stu-id="39b27-114">While this approach may align with the requirements of a single application deployment, your Cloud Adoption Team should examine these long-term questions:</span></span>

- <span data-ttu-id="39b27-115">L'ambito o le dimensioni di questa distribuzione si espanderanno rendendo necessario l'accesso ad altre risorse non PaaS?</span><span class="sxs-lookup"><span data-stu-id="39b27-115">Will this deployment expand in scope or scale to require access to other non-PaaS resources?</span></span>
- <span data-ttu-id="39b27-116">Sono pianificate altre distribuzioni PaaS oltre alla soluzione corrente?</span><span class="sxs-lookup"><span data-stu-id="39b27-116">Are other PaaS deployments planned beyond the current solution?</span></span>
- <span data-ttu-id="39b27-117">L'organizzazione ha in programma altre migrazioni cloud in futuro?</span><span class="sxs-lookup"><span data-stu-id="39b27-117">Does the organization have plans for other future cloud migrations?</span></span>

<span data-ttu-id="39b27-118">Le risposte a queste domande non precludono a un team la scelta di un'opzione solo PaaS, ma è opportuno valutarle prima di prendere una decisione finale.</span><span class="sxs-lookup"><span data-stu-id="39b27-118">The answers to these questions would not preclude a team from choosing a PaaS only option but should be considered before making a final decision.</span></span>
