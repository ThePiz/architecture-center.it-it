---
title: Aggiornare una risorsa in un modello di Azure Resource Manager
description: Viene descritto come estendere la funzionalità dei Modelli di Azure Resource Manager per aggiornare una risorsa
author: petertay
ms.date: 06/09/2017
ms.openlocfilehash: f235f0b4d54d65ccc2fa67876916e922d75f6d07
ms.sourcegitcommit: 94d50043db63416c4d00cebe927a0c88f78c3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47429039"
---
# <a name="update-a-resource-in-an-azure-resource-manager-template"></a><span data-ttu-id="d7710-103">Aggiornare una risorsa in un modello di Azure Resource Manager</span><span class="sxs-lookup"><span data-stu-id="d7710-103">Update a resource in an Azure Resource Manager template</span></span>

<span data-ttu-id="d7710-104">Esistono alcuni scenari in cui è necessario aggiornare una risorsa durante una distribuzione.</span><span class="sxs-lookup"><span data-stu-id="d7710-104">There are some scenarios in which you need to update a resource during a deployment.</span></span> <span data-ttu-id="d7710-105">Questo scenario si potrebbe verificare quando non è possibile specificare tutte le proprietà per una risorsa finché non vengono create altre risorse dipendenti.</span><span class="sxs-lookup"><span data-stu-id="d7710-105">You might encounter this scenario when you cannot specify all the properties for a resource until other, dependent resources are created.</span></span> <span data-ttu-id="d7710-106">Ad esempio, se si crea un pool di back-end per un bilanciamento del carico, si potrebbero aggiornare le interfacce di rete (NIC) nelle macchine virtuali (VM) per includerle nel pool di back-end.</span><span class="sxs-lookup"><span data-stu-id="d7710-106">For example, if you create a backend pool for a load balancer, you might update the network interfaces (NICs) on your virtual machines (VMs) to include them in the backend pool.</span></span> <span data-ttu-id="d7710-107">Sebbene Gestione risorse supporti l'aggiornamento delle risorse durante la distribuzione, è necessario progettare il modello in modo corretto per evitare errori e verificare che la distribuzione venga gestita come un aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="d7710-107">And while Resource Manager supports updating resources during deployment, you must design your template correctly to avoid errors and to ensure the deployment is handled as an update.</span></span>

<span data-ttu-id="d7710-108">In primo luogo, è necessario fare riferimento alla risorsa quando si trova nel modello per crearla, successivamente è necessario fare riferimento alla risorsa con lo stesso nome per aggiornarla in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="d7710-108">First, you must reference the resource once in the template to create it and then reference the resource by the same name to update it later.</span></span> <span data-ttu-id="d7710-109">Tuttavia, se due risorse hanno lo stesso nome in un modello, Resource Manager genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="d7710-109">However, if two resources have the same name in a template, Resource Manager throws an exception.</span></span> <span data-ttu-id="d7710-110">Per evitare questo errore, specificare la risorsa aggiornata in un secondo modello che è collegato o incluso come sotto-modello usando il tipo di risorsa `Microsoft.Resources/deployments`.</span><span class="sxs-lookup"><span data-stu-id="d7710-110">To avoid this error, specify the updated resource in a second template that's either linked or included as a subtemplate using the `Microsoft.Resources/deployments` resource type.</span></span>

<span data-ttu-id="d7710-111">In seguito specificare il nome della proprietà esistente da modificare o un nuovo nome per una proprietà da aggiungere nel modello annidato.</span><span class="sxs-lookup"><span data-stu-id="d7710-111">Second, you must either specify the name of the existing property to change or a new name for a property to add in the nested template.</span></span> <span data-ttu-id="d7710-112">È anche necessario specificare anche le proprietà originali e i relativi valori originali.</span><span class="sxs-lookup"><span data-stu-id="d7710-112">You must also specify the original properties and their original values.</span></span> <span data-ttu-id="d7710-113">Se non si riesce a specificare le proprietà e i valori originali, Gestione risorse presuppone che si desideri creare una nuova risorsa ed elimina la risorsa originale.</span><span class="sxs-lookup"><span data-stu-id="d7710-113">If you fail to provide the original properties and values, Resource Manager assumes you want to create a new resource and deletes the original resource.</span></span>

## <a name="example-template"></a><span data-ttu-id="d7710-114">Modello di esempio</span><span class="sxs-lookup"><span data-stu-id="d7710-114">Example template</span></span>

<span data-ttu-id="d7710-115">Viene ora illustrato un modello di esempio che illustra questa operazione.</span><span class="sxs-lookup"><span data-stu-id="d7710-115">Let's look at an example template that demonstrates this.</span></span> <span data-ttu-id="d7710-116">Il modello distribuisce una rete virtuale denominata `firstVNet` dotata di una subnet denominata `firstSubnet`.</span><span class="sxs-lookup"><span data-stu-id="d7710-116">Our template deploys a virtual network  named `firstVNet` that has one subnet named `firstSubnet`.</span></span> <span data-ttu-id="d7710-117">Distribuisce quindi un'interfaccia di rete virtuale denominata `nic1` e la associa alla subnet.</span><span class="sxs-lookup"><span data-stu-id="d7710-117">It then deploys a virtual network interface (NIC) named `nic1` and associates it with our subnet.</span></span> <span data-ttu-id="d7710-118">Quindi, una risorsa di distribuzione denominata `updateVNet` include un modello annidato che aggiorna la risorsa `firstVNet` mediante l'aggiunta di una seconda subnet denominata `secondSubnet`.</span><span class="sxs-lookup"><span data-stu-id="d7710-118">Then, a deployment resource named `updateVNet` includes a nested template that updates our `firstVNet` resource by adding a second subnet named `secondSubnet`.</span></span> 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[              
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

<span data-ttu-id="d7710-119">Osservare innanzitutto l'oggetto risorsa per la risorsa `firstVNet`.</span><span class="sxs-lookup"><span data-stu-id="d7710-119">Let's take a look at the resource object for our `firstVNet` resource first.</span></span> <span data-ttu-id="d7710-120">Si noti che vengono specificate nuovamente le impostazioni per `firstVNet` in un modello annidato &mdash; perché Gestione risorse non consente lo stesso nome di distribuzione nello stesso modello e i modelli annidati sono considerati come modelli diversi.</span><span class="sxs-lookup"><span data-stu-id="d7710-120">Notice that we respecify the settings for our `firstVNet` in a nested template&mdash;this is because Resource Manager doesn't allow the same deployment name within the same template and nested templates are considered to be a different template.</span></span> <span data-ttu-id="d7710-121">Specificando di nuovo i valori per la risorsa `firstSubnet`, si richiede a Gestione risorse l'aggiornamento della risorsa esistente invece dell'eliminazione e della ridistribuzione.</span><span class="sxs-lookup"><span data-stu-id="d7710-121">By respecifying our values for our `firstSubnet` resource, we are telling Resource Manager to update the existing resource instead of deleting it and redeploying it.</span></span> <span data-ttu-id="d7710-122">Infine, le nuove impostazioni per `secondSubnet` vengono prelevate durante l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="d7710-122">Finally, our new settings for `secondSubnet` are picked up during this update.</span></span>

## <a name="try-the-template"></a><span data-ttu-id="d7710-123">Provare il modello</span><span class="sxs-lookup"><span data-stu-id="d7710-123">Try the template</span></span>

<span data-ttu-id="d7710-124">Se si vuole sperimentare con questo modello, seguire questi passaggi:</span><span class="sxs-lookup"><span data-stu-id="d7710-124">If you would like to experiment with this template, follow these steps:</span></span>

1.  <span data-ttu-id="d7710-125">Accedere al portale di Azure, selezionare l'icona **+**, cercare il tipo di risorsa **distribuzione modelli** e selezionarla.</span><span class="sxs-lookup"><span data-stu-id="d7710-125">Go to the Azure portal, select the **+** icon, and search for the **template deployment** resource type, and select it.</span></span>
2.  <span data-ttu-id="d7710-126">Passare alla pagina **distribuzione modelli** e selezionare il pulsante **crea**.</span><span class="sxs-lookup"><span data-stu-id="d7710-126">Navigate to the **template deployment** page, select the **create** button.</span></span> <span data-ttu-id="d7710-127">Questo pulsante apre il pannello **distribuzione personalizzata**.</span><span class="sxs-lookup"><span data-stu-id="d7710-127">This button opens the **custom deployment** blade.</span></span>
3.  <span data-ttu-id="d7710-128">Selezionare l'icona **Modifica**.</span><span class="sxs-lookup"><span data-stu-id="d7710-128">Select the **edit** icon.</span></span>
4.  <span data-ttu-id="d7710-129">Eliminare il modello vuoto.</span><span class="sxs-lookup"><span data-stu-id="d7710-129">Delete the empty template.</span></span>
5.  <span data-ttu-id="d7710-130">Copiare e incollare il modello di esempio nel riquadro destro.</span><span class="sxs-lookup"><span data-stu-id="d7710-130">Copy and paste the sample template into the right pane.</span></span>
6.  <span data-ttu-id="d7710-131">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="d7710-131">Select the **save** button.</span></span>
7.  <span data-ttu-id="d7710-132">Tornare al riquadro **distribuzione personalizzata**: questa volta sono presenti alcuni elenchi a discesa.</span><span class="sxs-lookup"><span data-stu-id="d7710-132">You return to the **custom deployment** pane, but this time there are some drop-down list boxes.</span></span> <span data-ttu-id="d7710-133">Selezionare la propria sottoscrizione, creare un nuovo gruppo di risorse o usarne uno esistente e selezionare un percorso.</span><span class="sxs-lookup"><span data-stu-id="d7710-133">Select your subscription, either create new or use existing resource group, and select a location.</span></span> <span data-ttu-id="d7710-134">Esaminare i termini e condizioni e quindi selezionare il pulsante **Accetto**.</span><span class="sxs-lookup"><span data-stu-id="d7710-134">Review the terms and conditions, then select the **I agree** button.</span></span>
8.  <span data-ttu-id="d7710-135">Fare clic sul pulsante **Acquista**.</span><span class="sxs-lookup"><span data-stu-id="d7710-135">Select the **purchase** button.</span></span>

<span data-ttu-id="d7710-136">Al termine della distribuzione, aprire il gruppo di risorse specificato nel portale.</span><span class="sxs-lookup"><span data-stu-id="d7710-136">Once deployment has finished, open the resource group you specified in the portal.</span></span> <span data-ttu-id="d7710-137">Viene visualizzata una rete virtuale denominata `firstVNet` e una scheda di interfaccia di rete denominata `nic1`.</span><span class="sxs-lookup"><span data-stu-id="d7710-137">You see a virtual network named `firstVNet` and a NIC named `nic1`.</span></span> <span data-ttu-id="d7710-138">Fare clic su `firstVNet`, quindi fare clic su `subnets`.</span><span class="sxs-lookup"><span data-stu-id="d7710-138">Click `firstVNet`, then click `subnets`.</span></span> <span data-ttu-id="d7710-139">Viene visualizzato il `firstSubnet` che è stato originariamente creato e viene visualizzato il `secondSubnet` che è stato aggiunto nella risorsa `updateVNet`.</span><span class="sxs-lookup"><span data-stu-id="d7710-139">You see the `firstSubnet` that was originally created, and you see the `secondSubnet` that was added in the `updateVNet` resource.</span></span> 

![Subnet originale e subnet aggiornata](../_images/firstVNet-subnets.png)

<span data-ttu-id="d7710-141">Quindi, tornare al gruppo di risorse, fare clic su `nic1` e quindi fare clic su `IP configurations`.</span><span class="sxs-lookup"><span data-stu-id="d7710-141">Then, go back to the resource group and click `nic1` then click `IP configurations`.</span></span> <span data-ttu-id="d7710-142">Nella sezione `IP configurations` il `subnet` è impostato su `firstSubnet (10.0.0.0/24)`.</span><span class="sxs-lookup"><span data-stu-id="d7710-142">In the `IP configurations` section, the `subnet` is set to `firstSubnet (10.0.0.0/24)`.</span></span> 

![impostazioni di configurazioni IP NIC1](../_images/nic1-ipconfigurations.png)

<span data-ttu-id="d7710-144">L'originale `firstVNet` è stato aggiornato anziché ricreato.</span><span class="sxs-lookup"><span data-stu-id="d7710-144">The original `firstVNet` has been updated instead of recreated.</span></span> <span data-ttu-id="d7710-145">Se `firstVNet` era stato ricreato, `nic1` potrebbe non essere associato a `firstVNet`.</span><span class="sxs-lookup"><span data-stu-id="d7710-145">If `firstVNet` had been recreated, `nic1` would not be associated with `firstVNet`.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d7710-146">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="d7710-146">Next steps</span></span>

* <span data-ttu-id="d7710-147">Queste tecniche sono implementate nel [progetto dei blocchi predefiniti del modello](https://github.com/mspnp/template-building-blocks) e nelle [architetture di riferimento di Azure](/azure/architecture/reference-architectures/).</span><span class="sxs-lookup"><span data-stu-id="d7710-147">This technique is implemented in the [template building blocks project](https://github.com/mspnp/template-building-blocks) and the [Azure reference architectures](/azure/architecture/reference-architectures/).</span></span> <span data-ttu-id="d7710-148">È possibile usarle per creare la propria architettura o distribuire un'architettura di riferimento.</span><span class="sxs-lookup"><span data-stu-id="d7710-148">You can use these to create your own architecture or deploy one of our reference architectures.</span></span>
