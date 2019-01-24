---
title: Aggiornare una risorsa in un modello di Azure Resource Manager
description: Illustra come estendere le funzionalità dei modelli di Azure Resource Manager per aggiornare una risorsa.
author: petertay
ms.date: 10/31/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: de76e69e94917bbbe94c0f87fda2cdbe415181dc
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487144"
---
# <a name="update-a-resource-in-an-azure-resource-manager-template"></a><span data-ttu-id="1d71c-103">Aggiornare una risorsa in un modello di Azure Resource Manager</span><span class="sxs-lookup"><span data-stu-id="1d71c-103">Update a resource in an Azure Resource Manager template</span></span>

<span data-ttu-id="1d71c-104">Esistono alcuni scenari in cui è necessario aggiornare una risorsa durante una distribuzione.</span><span class="sxs-lookup"><span data-stu-id="1d71c-104">There are some scenarios in which you need to update a resource during a deployment.</span></span> <span data-ttu-id="1d71c-105">Questo scenario si potrebbe verificare quando non è possibile specificare tutte le proprietà per una risorsa finché non vengono create altre risorse dipendenti.</span><span class="sxs-lookup"><span data-stu-id="1d71c-105">You might encounter this scenario when you cannot specify all the properties for a resource until other, dependent resources are created.</span></span> <span data-ttu-id="1d71c-106">Ad esempio, se si crea un pool di back-end per un bilanciamento del carico, si potrebbero aggiornare le interfacce di rete (NIC) nelle macchine virtuali (VM) per includerle nel pool di back-end.</span><span class="sxs-lookup"><span data-stu-id="1d71c-106">For example, if you create a backend pool for a load balancer, you might update the network interfaces (NICs) on your virtual machines (VMs) to include them in the backend pool.</span></span> <span data-ttu-id="1d71c-107">Sebbene Gestione risorse supporti l'aggiornamento delle risorse durante la distribuzione, è necessario progettare il modello in modo corretto per evitare errori e verificare che la distribuzione venga gestita come un aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="1d71c-107">And while Resource Manager supports updating resources during deployment, you must design your template correctly to avoid errors and to ensure the deployment is handled as an update.</span></span>

<span data-ttu-id="1d71c-108">In primo luogo, è necessario fare riferimento alla risorsa quando si trova nel modello per crearla, successivamente è necessario fare riferimento alla risorsa con lo stesso nome per aggiornarla in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="1d71c-108">First, you must reference the resource once in the template to create it and then reference the resource by the same name to update it later.</span></span> <span data-ttu-id="1d71c-109">Tuttavia, se due risorse hanno lo stesso nome in un modello, Resource Manager genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="1d71c-109">However, if two resources have the same name in a template, Resource Manager throws an exception.</span></span> <span data-ttu-id="1d71c-110">Per evitare questo errore, specificare la risorsa aggiornata in un secondo modello che è collegato o incluso come sotto-modello usando il tipo di risorsa `Microsoft.Resources/deployments`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-110">To avoid this error, specify the updated resource in a second template that's either linked or included as a subtemplate using the `Microsoft.Resources/deployments` resource type.</span></span>

<span data-ttu-id="1d71c-111">In seguito specificare il nome della proprietà esistente da modificare o un nuovo nome per una proprietà da aggiungere nel modello annidato.</span><span class="sxs-lookup"><span data-stu-id="1d71c-111">Second, you must either specify the name of the existing property to change or a new name for a property to add in the nested template.</span></span> <span data-ttu-id="1d71c-112">È anche necessario specificare anche le proprietà originali e i relativi valori originali.</span><span class="sxs-lookup"><span data-stu-id="1d71c-112">You must also specify the original properties and their original values.</span></span> <span data-ttu-id="1d71c-113">Se non si riesce a specificare le proprietà e i valori originali, Gestione risorse presuppone che si desideri creare una nuova risorsa ed elimina la risorsa originale.</span><span class="sxs-lookup"><span data-stu-id="1d71c-113">If you fail to provide the original properties and values, Resource Manager assumes you want to create a new resource and deletes the original resource.</span></span>

## <a name="example-template"></a><span data-ttu-id="1d71c-114">Modello di esempio</span><span class="sxs-lookup"><span data-stu-id="1d71c-114">Example template</span></span>

<span data-ttu-id="1d71c-115">Viene ora illustrato un modello di esempio che illustra questa operazione.</span><span class="sxs-lookup"><span data-stu-id="1d71c-115">Let's look at an example template that demonstrates this.</span></span> <span data-ttu-id="1d71c-116">Il modello distribuisce una rete virtuale denominata `firstVNet` che include una subnet denominata `firstSubnet`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-116">Our template deploys a virtual network named `firstVNet` that has one subnet named `firstSubnet`.</span></span> <span data-ttu-id="1d71c-117">Distribuisce quindi un'interfaccia di rete virtuale denominata `nic1` e la associa alla subnet.</span><span class="sxs-lookup"><span data-stu-id="1d71c-117">It then deploys a virtual network interface (NIC) named `nic1` and associates it with our subnet.</span></span> <span data-ttu-id="1d71c-118">Quindi, una risorsa di distribuzione denominata `updateVNet` include un modello annidato che aggiorna la risorsa `firstVNet` mediante l'aggiunta di una seconda subnet denominata `secondSubnet`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-118">Then, a deployment resource named `updateVNet` includes a nested template that updates our `firstVNet` resource by adding a second subnet named `secondSubnet`.</span></span>

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

<span data-ttu-id="1d71c-119">Osservare innanzitutto l'oggetto risorsa per la risorsa `firstVNet`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-119">Let's take a look at the resource object for our `firstVNet` resource first.</span></span> <span data-ttu-id="1d71c-120">Si noti che vengono specificate nuovamente le impostazioni per `firstVNet` in un modello annidato &mdash; perché Gestione risorse non consente lo stesso nome di distribuzione nello stesso modello e i modelli annidati sono considerati come modelli diversi.</span><span class="sxs-lookup"><span data-stu-id="1d71c-120">Notice that we respecify the settings for our `firstVNet` in a nested template&mdash;this is because Resource Manager doesn't allow the same deployment name within the same template and nested templates are considered to be a different template.</span></span> <span data-ttu-id="1d71c-121">Specificando di nuovo i valori per la risorsa `firstSubnet`, si richiede a Gestione risorse l'aggiornamento della risorsa esistente invece dell'eliminazione e della ridistribuzione.</span><span class="sxs-lookup"><span data-stu-id="1d71c-121">By respecifying our values for our `firstSubnet` resource, we are telling Resource Manager to update the existing resource instead of deleting it and redeploying it.</span></span> <span data-ttu-id="1d71c-122">Infine, le nuove impostazioni per `secondSubnet` vengono prelevate durante l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="1d71c-122">Finally, our new settings for `secondSubnet` are picked up during this update.</span></span>

## <a name="try-the-template"></a><span data-ttu-id="1d71c-123">Provare il modello</span><span class="sxs-lookup"><span data-stu-id="1d71c-123">Try the template</span></span>

<span data-ttu-id="1d71c-124">Un modello di esempio è disponibile in [GitHub][github].</span><span class="sxs-lookup"><span data-stu-id="1d71c-124">An example template is available on [GitHub][github].</span></span> <span data-ttu-id="1d71c-125">Per distribuire il modello, eseguire questi comandi dell'[interfaccia della riga di comando di Azure][cli]:</span><span class="sxs-lookup"><span data-stu-id="1d71c-125">To deploy the template, run the following [Azure CLI][cli] commands:</span></span>

```bash
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example1-update/deploy.json
```

<span data-ttu-id="1d71c-126">Al termine della distribuzione, aprire il gruppo di risorse specificato nel portale.</span><span class="sxs-lookup"><span data-stu-id="1d71c-126">Once deployment has finished, open the resource group you specified in the portal.</span></span> <span data-ttu-id="1d71c-127">Viene visualizzata una rete virtuale denominata `firstVNet` e una scheda di interfaccia di rete denominata `nic1`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-127">You see a virtual network named `firstVNet` and a NIC named `nic1`.</span></span> <span data-ttu-id="1d71c-128">Fare clic su `firstVNet`, quindi fare clic su `subnets`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-128">Click `firstVNet`, then click `subnets`.</span></span> <span data-ttu-id="1d71c-129">Viene visualizzato il `firstSubnet` che è stato originariamente creato e viene visualizzato il `secondSubnet` che è stato aggiunto nella risorsa `updateVNet`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-129">You see the `firstSubnet` that was originally created, and you see the `secondSubnet` that was added in the `updateVNet` resource.</span></span>

![Subnet originale e subnet aggiornata](../_images/firstVNet-subnets.png)

<span data-ttu-id="1d71c-131">Quindi, tornare al gruppo di risorse, fare clic su `nic1` e quindi fare clic su `IP configurations`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-131">Then, go back to the resource group and click `nic1` then click `IP configurations`.</span></span> <span data-ttu-id="1d71c-132">Nella sezione `IP configurations` il `subnet` è impostato su `firstSubnet (10.0.0.0/24)`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-132">In the `IP configurations` section, the `subnet` is set to `firstSubnet (10.0.0.0/24)`.</span></span>

![impostazioni di configurazioni IP NIC1](../_images/nic1-ipconfigurations.png)

<span data-ttu-id="1d71c-134">L'originale `firstVNet` è stato aggiornato anziché ricreato.</span><span class="sxs-lookup"><span data-stu-id="1d71c-134">The original `firstVNet` has been updated instead of recreated.</span></span> <span data-ttu-id="1d71c-135">Se `firstVNet` era stato ricreato, `nic1` potrebbe non essere associato a `firstVNet`.</span><span class="sxs-lookup"><span data-stu-id="1d71c-135">If `firstVNet` had been recreated, `nic1` would not be associated with `firstVNet`.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1d71c-136">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="1d71c-136">Next steps</span></span>

* <span data-ttu-id="1d71c-137">Informazioni su come distribuire una risorsa in base a una condizione, come ad esempio la presenza o meno del valore di un parametro.</span><span class="sxs-lookup"><span data-stu-id="1d71c-137">Learn how deploy a resource based on a condition, such as whether a parameter value is present.</span></span> <span data-ttu-id="1d71c-138">Vedere [Distribuire in modo condizionale una risorsa in un modello di Azure Resource Manager](./conditional-deploy.md).</span><span class="sxs-lookup"><span data-stu-id="1d71c-138">See [Conditionally deploy a resource in an Azure Resource Manager template](./conditional-deploy.md).</span></span>

[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples
