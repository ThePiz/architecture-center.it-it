---
title: Principi di progettazione per le applicazioni Azure
titleSuffix: Azure Application Architecture Guide
description: Principi di progettazione per le applicazioni Azure.
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
---

# <a name="ten-design-principles-for-azure-applications"></a><span data-ttu-id="8d92d-103">Dieci principi di progettazione per le applicazioni Azure</span><span class="sxs-lookup"><span data-stu-id="8d92d-103">Ten design principles for Azure applications</span></span>

<span data-ttu-id="8d92d-104">Seguire questi principi di progettazione per rendere l'applicazione più scalabile, resiliente e gestibile.</span><span class="sxs-lookup"><span data-stu-id="8d92d-104">Follow these design principles to make your application more scalable, resilient, and manageable.</span></span>

<span data-ttu-id="8d92d-105">**[Progettazione per la correzione automatica](self-healing.md)**.</span><span class="sxs-lookup"><span data-stu-id="8d92d-105">**[Design for self healing](self-healing.md)**.</span></span> <span data-ttu-id="8d92d-106">In un sistema distribuito possono verificarsi errori.</span><span class="sxs-lookup"><span data-stu-id="8d92d-106">In a distributed system, failures happen.</span></span> <span data-ttu-id="8d92d-107">Progettare l'applicazione in modo che sia in grado di eseguire la correzione automatica dei propri errori.</span><span class="sxs-lookup"><span data-stu-id="8d92d-107">Design your application to be self healing when failures occur.</span></span>

<span data-ttu-id="8d92d-108">**[Rendere ogni elemento ridondante](redundancy.md)**.</span><span class="sxs-lookup"><span data-stu-id="8d92d-108">**[Make all things redundant](redundancy.md)**.</span></span> <span data-ttu-id="8d92d-109">Applicare la ridondanza nell'applicazione per evitare singoli punti di errore.</span><span class="sxs-lookup"><span data-stu-id="8d92d-109">Build redundancy into your application, to avoid having single points of failure.</span></span>

<span data-ttu-id="8d92d-110">**[Ridurre al minimo il coordinamento](minimize-coordination.md)**.</span><span class="sxs-lookup"><span data-stu-id="8d92d-110">**[Minimize coordination](minimize-coordination.md)**.</span></span> <span data-ttu-id="8d92d-111">Ridurre al minimo il coordinamento tra i servizi delle applicazioni per ottenere la scalabilità.</span><span class="sxs-lookup"><span data-stu-id="8d92d-111">Minimize coordination between application services to achieve scalability.</span></span>

<span data-ttu-id="8d92d-112">**[Progettare per la scalabilità orizzontale](scale-out.md)**. Progettare l'applicazione in modo che possa scalare orizzontalmente, aggiungendo o rimuovendo nuove istanze in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="8d92d-112">**[Design to scale out](scale-out.md)**. Design your application so that it can scale horizontally, adding or removing new instances as demand requires.</span></span>

<span data-ttu-id="8d92d-113">**[Creare partizioni per ovviare i limiti](partition.md)**.</span><span class="sxs-lookup"><span data-stu-id="8d92d-113">**[Partition around limits](partition.md)**.</span></span> <span data-ttu-id="8d92d-114">Usare il partizionamento per risolvere il problema dei limiti a livello di calcolo, database e rete.</span><span class="sxs-lookup"><span data-stu-id="8d92d-114">Use partitioning to work around database, network, and compute limits.</span></span>

<span data-ttu-id="8d92d-115">**[Progettare per le esigenze del team operativo](design-for-operations.md)**.</span><span class="sxs-lookup"><span data-stu-id="8d92d-115">**[Design for operations](design-for-operations.md)**.</span></span> <span data-ttu-id="8d92d-116">Progettare l'applicazione in modo che il team operativo disponga degli strumenti necessari.</span><span class="sxs-lookup"><span data-stu-id="8d92d-116">Design your application so that the operations team has the tools they need.</span></span>

<span data-ttu-id="8d92d-117">**[Usare i servizi gestiti](managed-services.md)**.</span><span class="sxs-lookup"><span data-stu-id="8d92d-117">**[Use managed services](managed-services.md)**.</span></span> <span data-ttu-id="8d92d-118">Quando possibile, usare la piattaforma distribuita come servizio (PaaS) invece dell'infrastruttura distribuita come servizio (IaaS).</span><span class="sxs-lookup"><span data-stu-id="8d92d-118">When possible, use platform as a service (PaaS) rather than infrastructure as a service (IaaS).</span></span>

<span data-ttu-id="8d92d-119">**[Usare il migliore archivio dati per il processo](use-the-best-data-store.md)**.</span><span class="sxs-lookup"><span data-stu-id="8d92d-119">**[Use the best data store for the job](use-the-best-data-store.md)**.</span></span> <span data-ttu-id="8d92d-120">Scegliere la tecnologia di archiviazione più adatta ai dati e alle modalità d'utilizzo previste.</span><span class="sxs-lookup"><span data-stu-id="8d92d-120">Pick the storage technology that is the best fit for your data and how it will be used.</span></span>

<span data-ttu-id="8d92d-121">**[Progettare in un'ottica evolutiva](design-for-evolution.md)**.</span><span class="sxs-lookup"><span data-stu-id="8d92d-121">**[Design for evolution](design-for-evolution.md)**.</span></span> <span data-ttu-id="8d92d-122">Tutte le applicazioni efficienti cambiano nel tempo.</span><span class="sxs-lookup"><span data-stu-id="8d92d-122">All successful applications change over time.</span></span> <span data-ttu-id="8d92d-123">Una progettazione in grado di evolversi è fondamentale per l'innovazione continua.</span><span class="sxs-lookup"><span data-stu-id="8d92d-123">An evolutionary design is key for continuous innovation.</span></span>

<span data-ttu-id="8d92d-124">**[Compilare l'applicazione per le esigenze aziendali](build-for-business.md)**.</span><span class="sxs-lookup"><span data-stu-id="8d92d-124">**[Build for the needs of business](build-for-business.md)**.</span></span> <span data-ttu-id="8d92d-125">Ogni decisione di progettazione deve essere giustificata da un requisito aziendale.</span><span class="sxs-lookup"><span data-stu-id="8d92d-125">Every design decision must be justified by a business requirement.</span></span>
