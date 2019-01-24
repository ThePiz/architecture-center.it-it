---
title: Raccogliere i dati di inventario per un digital estate
titleSuffix: Enterprise Cloud Adoption
description: Come creare un inventario per un digital estate
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 5c2f270cf8de81c8a94d1f924f51611e657ed0ed
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481797"
---
# <a name="enterprise-cloud-adoption-gather-inventory-data-for-a-digital-estate"></a><span data-ttu-id="9c6de-103">Adozione del cloud nell'organizzazione: Raccogliere i dati di inventario per un digital estate</span><span class="sxs-lookup"><span data-stu-id="9c6de-103">Enterprise Cloud Adoption: Gather inventory data for a digital estate</span></span>

<span data-ttu-id="9c6de-104">Lo sviluppo di un inventario è il primo passaggio nella [pianificazione del digital estate](overview.md).</span><span class="sxs-lookup"><span data-stu-id="9c6de-104">Developing an inventory is the first step in [Digital Estate Planning](overview.md).</span></span> <span data-ttu-id="9c6de-105">In questo processo, sarà raccolto un elenco di asset IT che supportano funzioni aziendali specifiche per le analisi e razionalizzazioni successive.</span><span class="sxs-lookup"><span data-stu-id="9c6de-105">In this process, a list of IT assets that support specific business functions would be collected for later analysis and rationalization.</span></span> <span data-ttu-id="9c6de-106">In questo articolo si presuppone che un approccio bottom-up all'analisi sia il più adeguato per le esigenze di pianificazione.</span><span class="sxs-lookup"><span data-stu-id="9c6de-106">This article assumes that a bottom-up approach to analysis is most appropriate for planning needs.</span></span> <span data-ttu-id="9c6de-107">Per altre informazioni, consultare [Approcci per la pianificazione del digital estate](./approach.md).</span><span class="sxs-lookup"><span data-stu-id="9c6de-107">For more information, see [Approaches to digital estate planning](./approach.md).</span></span>

## <a name="how-can-a-digital-estate-be-inventoried"></a><span data-ttu-id="9c6de-108">Come si può includere un digital estate nell'inventario?</span><span class="sxs-lookup"><span data-stu-id="9c6de-108">How can a digital estate be inventoried?</span></span>

<span data-ttu-id="9c6de-109">L'inventario che supporta un digital estate cambia a seconda della trasformazione digitale desiderata e il percorso di trasformazione corrispondente.</span><span class="sxs-lookup"><span data-stu-id="9c6de-109">The inventory supporting a digital estate changes depending on the desired Digital Transformation and corresponding Transformation Journey.</span></span>

- <span data-ttu-id="9c6de-110">Trasformazione operativa: Durante una trasformazione operativa, è spesso consigliabile la raccolta dell'inventario da parte di strumenti di analisi in grado di creare un elenco centralizzato di tutte le macchine virtuali e i server.</span><span class="sxs-lookup"><span data-stu-id="9c6de-110">Operational Transformation: During an operational transformation, it is often advised that the inventory be collected from scanning tools which can create a centralized list of all VMs and servers.</span></span> <span data-ttu-id="9c6de-111">Alcuni strumenti possono anche creare i mapping e le dipendenze di rete, permettendo la definizione dell'allineamento del carico di lavoro.</span><span class="sxs-lookup"><span data-stu-id="9c6de-111">Some tools can also create network mappings and dependencies, which will help define workload alignment.</span></span>

- <span data-ttu-id="9c6de-112">Trasformazione incrementale: L'inventario per una trasformazione incrementale inizia con il cliente.</span><span class="sxs-lookup"><span data-stu-id="9c6de-112">Incremental Transformation: Inventory for an incremental transformation begins with the customer.</span></span> <span data-ttu-id="9c6de-113">L'esecuzione il mapping dell'esperienza del cliente dall'inizio alla fine è un buon punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="9c6de-113">Mapping the customer experience from start to finish is a good place to begin.</span></span> <span data-ttu-id="9c6de-114">L'allineamento di tale mapping ad applicazioni, API, dati e altri asset permetterà la creazione di un inventario dettagliato per l'analisi.</span><span class="sxs-lookup"><span data-stu-id="9c6de-114">Aligning that map to applications, APIs, data, and other assets will create a detailed inventory for analysis.</span></span>

- <span data-ttu-id="9c6de-115">Trasformazione esponenziale: La trasformazione esponenziale è incentrata sul prodotto o servizio.</span><span class="sxs-lookup"><span data-stu-id="9c6de-115">Disruptive Transformation: Disruptive transformation focuses on the product or service.</span></span> <span data-ttu-id="9c6de-116">In questo caso, un inventario include un mapping di tutte le opportunità di rivoluzionare il mercato e le funzionalità necessarie.</span><span class="sxs-lookup"><span data-stu-id="9c6de-116">From there an inventory would include a mapping of the opportunities to disrupt the market and the capabilities needed.</span></span>

## <a name="accuracy-and-completeness-of-an-inventory"></a><span data-ttu-id="9c6de-117">Accuratezza e completezza di un inventario</span><span class="sxs-lookup"><span data-stu-id="9c6de-117">Accuracy and completeness of an inventory</span></span>

<span data-ttu-id="9c6de-118">Un inventario è raramente del tutto completo alla sua prima iterazione.</span><span class="sxs-lookup"><span data-stu-id="9c6de-118">An inventory is seldom fully complete in its first iteration.</span></span> <span data-ttu-id="9c6de-119">È altamente consigliabile che i vari membri del team di strategia per il cloud eseguano l'allineamento di stakeholder e utenti avanzati per la convalida dell'inventario.</span><span class="sxs-lookup"><span data-stu-id="9c6de-119">It is highly advised that various members of the Cloud Strategy team align stakeholders and power users to validate the inventory.</span></span> <span data-ttu-id="9c6de-120">Quando possibile, sono utilizzabili strumenti aggiuntivi, come l'analisi di rete e dipendenze, per identificare gli asset che ricevono traffico, ma non sono presenti nell'inventario.</span><span class="sxs-lookup"><span data-stu-id="9c6de-120">When possible, additional tools like network and dependency analysis can be used to identify assets that are being sent traffic, but are not in the inventory.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9c6de-121">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="9c6de-121">Next steps</span></span>

<span data-ttu-id="9c6de-122">Una volta compilato e convalidato, l'inventario può essere razionalizzato.</span><span class="sxs-lookup"><span data-stu-id="9c6de-122">Once an inventory is compiled and validated, it can rationalized.</span></span> <span data-ttu-id="9c6de-123">La razionalizzazione dell'inventario è il passaggio successivo della pianificazione del digital estate.</span><span class="sxs-lookup"><span data-stu-id="9c6de-123">Inventory Rationalization is the next step to digital estate planning.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9c6de-124">Razionalizzare il digital estate</span><span class="sxs-lookup"><span data-stu-id="9c6de-124">Rationalize the digital estate</span></span>](rationalize.md)