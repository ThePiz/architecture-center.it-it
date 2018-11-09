---
title: Distribuire in modo condizionale una risorsa in un modello di Azure Resource Manager
description: Viene descritto come estendere la funzionalità dei modelli di Azure Resource Manager per distribuire in modo condizionale una risorsa in base al valore di un parametro
author: petertay
ms.date: 10/30/2018
ms.openlocfilehash: 2c74e17a5f38f9225b696640a23b55b1285276bb
ms.sourcegitcommit: e9eb2b895037da0633ef3ccebdea2fcce047620f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251839"
---
# <a name="conditionally-deploy-a-resource-in-an-azure-resource-manager-template"></a><span data-ttu-id="8f082-103">Distribuire in modo condizionale una risorsa in un modello di Azure Resource Manager</span><span class="sxs-lookup"><span data-stu-id="8f082-103">Conditionally deploy a resource in an Azure Resource Manager template</span></span>

<span data-ttu-id="8f082-104">Esistono alcuni scenari in cui è necessario progettare il modello per distribuire una risorsa in base a una condizione, ad esempio se un valore di parametro è presente o meno.</span><span class="sxs-lookup"><span data-stu-id="8f082-104">There are some scenarios in which you need to design your template to deploy a resource based on a condition, such as whether or not a parameter value is present.</span></span> <span data-ttu-id="8f082-105">Ad esempio, il modello può distribuire una rete virtuale e includere i parametri per specificare altre reti virtuali per il peering.</span><span class="sxs-lookup"><span data-stu-id="8f082-105">For example, your template may deploy a virtual network and include parameters to specify other virtual networks for peering.</span></span> <span data-ttu-id="8f082-106">Se i valori del parametro non sono stati specificati per il peering, Gestione risorse non dovrebbe distribuire la risorsa di peering.</span><span class="sxs-lookup"><span data-stu-id="8f082-106">If you've not specified any parameter values for peering, you don't want Resource Manager to deploy the peering resource.</span></span>

<span data-ttu-id="8f082-107">A tale scopo, usare l'[elemento condition][azure-resource-manager-condition] nella risorsa per testare la lunghezza della matrice del parametro.</span><span class="sxs-lookup"><span data-stu-id="8f082-107">To accomplish this, use the [condition element][azure-resource-manager-condition] in the resource to test the length of your parameter array.</span></span> <span data-ttu-id="8f082-108">Se la lunghezza è zero, restituire `false` per impedire la distribuzione, ma per tutti i valori maggiori di zero restituire `true` per consentire la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="8f082-108">If the length is zero, return `false` to prevent deployment, but for all values greater than zero return `true` to allow deployment.</span></span>

## <a name="example-template"></a><span data-ttu-id="8f082-109">Modello di esempio</span><span class="sxs-lookup"><span data-stu-id="8f082-109">Example template</span></span>

<span data-ttu-id="8f082-110">Viene ora illustrato un modello di esempio che illustra questa operazione.</span><span class="sxs-lookup"><span data-stu-id="8f082-110">Let's look at an example template that demonstrates this.</span></span> <span data-ttu-id="8f082-111">Il modello usa l'[elemento condition][azure-resource-manager-condition] per controllare la distribuzione della risorsa `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`.</span><span class="sxs-lookup"><span data-stu-id="8f082-111">Our template uses the [condition element][azure-resource-manager-condition] to control deployment of the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` resource.</span></span> <span data-ttu-id="8f082-112">Questa risorsa crea un peering tra due reti virtuali di Azure nella stessa area.</span><span class="sxs-lookup"><span data-stu-id="8f082-112">This resource creates a peering between two Azure Virtual Networks in the same region.</span></span>

<span data-ttu-id="8f082-113">Viene esaminata ora ciascuna sezione del modello.</span><span class="sxs-lookup"><span data-stu-id="8f082-113">Let's take a look at each section of the template.</span></span>

<span data-ttu-id="8f082-114">L'elemento `parameters` definisce un singolo parametro denominato `virtualNetworkPeerings`:</span><span class="sxs-lookup"><span data-stu-id="8f082-114">The `parameters` element defines a single parameter named `virtualNetworkPeerings`:</span></span> 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualNetworkPeerings": {
      "type": "array",
      "defaultValue": []
    }
  },
```
<span data-ttu-id="8f082-115">Il parametro `virtualNetworkPeerings` è un `array` e presenta lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="8f082-115">Our `virtualNetworkPeerings` parameter is an `array` and has the following schema:</span></span>

```json
"virtualNetworkPeerings": [
    {
      "name": "firstVNet/peering1",
      "properties": {
          "remoteVirtualNetwork": {
              "id": "[resourceId('Microsoft.Network/virtualNetworks','secondVNet')]"
          },
          "allowForwardedTraffic": true,
          "allowGatewayTransit": true,
          "useRemoteGateways": false
      }
    }
]
```

<span data-ttu-id="8f082-116">Le proprietà di questo parametro specificano le [impostazioni correlate al peering delle reti virtuali][vnet-peering-resource-schema].</span><span class="sxs-lookup"><span data-stu-id="8f082-116">The properties in our parameter specify the [settings related to peering virtual networks][vnet-peering-resource-schema].</span></span> <span data-ttu-id="8f082-117">Verranno indicati i valori per queste proprietà quando si specificherà la risorsa `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` nella sezione `resources`:</span><span class="sxs-lookup"><span data-stu-id="8f082-117">We'll provide the values for these properties when we specify the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` resource in the `resources` section:</span></span>

```json
"resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('vnp-', copyIndex())]",
      "condition": "[greater(length(parameters('virtualNetworkPeerings')), 0)]",
      "dependsOn": [
        "firstVNet", "secondVNet"
      ],
      "copy": {
          "name": "iterator",
          "count": "[length(variables('peerings'))]",
          "mode": "serial"
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
              "apiVersion": "2016-06-01",
              "location": "[resourceGroup().location]",
              "name": "[variables('peerings')[copyIndex()].name]",
              "properties": "[variables('peerings')[copyIndex()].properties]"
            }
          ],
          "outputs": {
          }
        }
      }
    }
]
```
<span data-ttu-id="8f082-118">Esistono alcuni aspetti da considerare in questa parte del modello.</span><span class="sxs-lookup"><span data-stu-id="8f082-118">There are a couple of things going on in this part of our template.</span></span> <span data-ttu-id="8f082-119">Innanzitutto la risorsa effettiva viene distribuita in un modello di inline di tipo `Microsoft.Resources/deployments` che include il proprio modello che effettivamente distribuisce `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`.</span><span class="sxs-lookup"><span data-stu-id="8f082-119">First, the actual resource being deployed is an inline template of type `Microsoft.Resources/deployments` that includes its own template that actually deploys the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`.</span></span>

<span data-ttu-id="8f082-120">`name` del modello inline viene reso univoco concatenando l'iterazione corrente di `copyIndex()` con il prefisso `vnp-`.</span><span class="sxs-lookup"><span data-stu-id="8f082-120">Our `name` for the inline template is made unique by concatenating the current iteration of the `copyIndex()` with the prefix `vnp-`.</span></span> 

<span data-ttu-id="8f082-121">L'elemento `condition` specifica che la risorsa deve essere elaborata quando la funzione `greater()` restituisce `true`.</span><span class="sxs-lookup"><span data-stu-id="8f082-121">The `condition` element specifies that our resource should be processed when the `greater()` function evaluates to `true`.</span></span> <span data-ttu-id="8f082-122">Qui viene testato se la matrice del parametro `virtualNetworkPeerings` è `greater()` di zero.</span><span class="sxs-lookup"><span data-stu-id="8f082-122">Here, we're testing if the `virtualNetworkPeerings` parameter array is `greater()` than zero.</span></span> <span data-ttu-id="8f082-123">In questo caso, restituisce `true` e `condition` viene soddisfatta.</span><span class="sxs-lookup"><span data-stu-id="8f082-123">If it is, it evaluates to `true` and the `condition` is satisfied.</span></span> <span data-ttu-id="8f082-124">In caso contrario, è `false`.</span><span class="sxs-lookup"><span data-stu-id="8f082-124">Otherwise, it's `false`.</span></span>

<span data-ttu-id="8f082-125">Successivamente si specifica il ciclo `copy`.</span><span class="sxs-lookup"><span data-stu-id="8f082-125">Next, we specify our `copy` loop.</span></span> <span data-ttu-id="8f082-126">Si tratta di un ciclo `serial` che implica che il ciclo viene eseguito in sequenza, mentre ogni risorsa è in attesa fino a quando l'ultima risorsa viene distribuita.</span><span class="sxs-lookup"><span data-stu-id="8f082-126">It's a `serial` loop that means the loop is done in sequence, with each resource waiting until the last resource has been deployed.</span></span> <span data-ttu-id="8f082-127">La proprietà `count` specifica il numero di volte in cui il ciclo esegue l'iterazione.</span><span class="sxs-lookup"><span data-stu-id="8f082-127">The `count` property specifies the number of times the loop iterates.</span></span> <span data-ttu-id="8f082-128">In genere si imposta sulla lunghezza della matrice `virtualNetworkPeerings` perché contiene gli oggetti parametro che specificano la risorsa che si desidera distribuire.</span><span class="sxs-lookup"><span data-stu-id="8f082-128">Here, normally we'd set it to the length of the `virtualNetworkPeerings` array because it contains the parameter objects specifying the resource we want to deploy.</span></span> <span data-ttu-id="8f082-129">Tuttavia, se la si imposta in questo modo la convalida avrà esito negativo se la matrice è vuota perché Gestione risorse si accorge che si sta tentando di accedere a proprietà che non esistono.</span><span class="sxs-lookup"><span data-stu-id="8f082-129">However, if we do that, validation will fail if the array is empty because Resource Manager notices that we are attempting to access properties that do not exist.</span></span> <span data-ttu-id="8f082-130">Tuttavia è possibile tentare di risolvere questo problema.</span><span class="sxs-lookup"><span data-stu-id="8f082-130">We can work around this, however.</span></span> <span data-ttu-id="8f082-131">Vengono esaminate ora le variabili necessarie:</span><span class="sxs-lookup"><span data-stu-id="8f082-131">Let's take a look at the variables we'll need:</span></span>

```json
  "variables": {
    "workaround": {
       "true": "[parameters('virtualNetworkPeerings')]",
       "false": [{
           "name": "workaround",
           "properties": {}
       }]
     },
     "peerings": "[variables('workaround')[string(greater(length(parameters('virtualNetworkPeerings')), 0))]]"
  },
```

<span data-ttu-id="8f082-132">La variabile `workaround` include due proprietà, una denominata `true` e una denominata `false`.</span><span class="sxs-lookup"><span data-stu-id="8f082-132">Our `workaround` variable includes two properties, one named `true` and one named `false`.</span></span> <span data-ttu-id="8f082-133">La proprietà `true` restituisce il valore della matrice del parametro `virtualNetworkPeerings`.</span><span class="sxs-lookup"><span data-stu-id="8f082-133">The `true` property evaluates to the value of the `virtualNetworkPeerings` parameter array.</span></span> <span data-ttu-id="8f082-134">La proprietà `false` restituisce un oggetto vuoto con le proprietà denominate previste da Resource Manager. Si noti che `false` è di fatto una matrice, così come il parametro `virtualNetworkPeerings`, e la convalida avrà quindi esito positivo.</span><span class="sxs-lookup"><span data-stu-id="8f082-134">The `false` property evaluates to an empty object including the named properties that Resource Manager expects to see &mdash; note that `false` is actually an array, just as our `virtualNetworkPeerings` parameter is, which will satisfy validation.</span></span> 

<span data-ttu-id="8f082-135">La variabile `peerings` usa la variabile `workaround` controllando nuovamente se la lunghezza della matrice del parametro `virtualNetworkPeerings` è maggiore di zero.</span><span class="sxs-lookup"><span data-stu-id="8f082-135">Our `peerings` variable uses our `workaround` variable by once again testing if the length of the `virtualNetworkPeerings` parameter array is greater than zero.</span></span> <span data-ttu-id="8f082-136">Se lo è, `string` restituisce `true` e la variabile `workaround` restituisce la matrice del parametro `virtualNetworkPeerings`.</span><span class="sxs-lookup"><span data-stu-id="8f082-136">If it is, the `string` evaluates to `true` and the `workaround` variable evaluates to the `virtualNetworkPeerings` parameter array.</span></span> <span data-ttu-id="8f082-137">In caso contrario, restituisce `false` e la variabile `workaround` restituisce l'oggetto vuoto nel primo elemento della matrice.</span><span class="sxs-lookup"><span data-stu-id="8f082-137">Otherwise, it evaluates to `false` and the `workaround` variable evaluates to our empty object in the first element of the array.</span></span>

<span data-ttu-id="8f082-138">Ora che è stato risolto il problema di convalida, basta specificare la distribuzione della risorsa `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` nel modello annidato, passando `name` e `properties` dalla matrice `virtualNetworkPeerings` del parametro.</span><span class="sxs-lookup"><span data-stu-id="8f082-138">Now that we've worked around the validation issue, we can simply specify the deployment of the `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` resource in the nested template, passing the `name` and `properties` from our `virtualNetworkPeerings` parameter array.</span></span> <span data-ttu-id="8f082-139">Questo può essere visualizzato nell'elemento annidato `template` nell'elemento `properties` della risorsa.</span><span class="sxs-lookup"><span data-stu-id="8f082-139">You can see this in the `template` element nested in the `properties` element of our resource.</span></span>

## <a name="try-the-template"></a><span data-ttu-id="8f082-140">Provare il modello</span><span class="sxs-lookup"><span data-stu-id="8f082-140">Try the template</span></span>

<span data-ttu-id="8f082-141">Un modello di esempio è disponibile in [GitHub][github].</span><span class="sxs-lookup"><span data-stu-id="8f082-141">An example template is available on [GitHub][github].</span></span> <span data-ttu-id="8f082-142">Per distribuire il modello, eseguire questi comandi dell'[interfaccia della riga di comando di Azure][cli]:</span><span class="sxs-lookup"><span data-stu-id="8f082-142">To deploy the template, run the following [Azure CLI][cli] commands:</span></span>

```bash
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example2-conditional/deploy.json
```

## <a name="next-steps"></a><span data-ttu-id="8f082-143">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="8f082-143">Next steps</span></span>

* <span data-ttu-id="8f082-144">Usare oggetti invece di valori scalari come parametri del modello.</span><span class="sxs-lookup"><span data-stu-id="8f082-144">Use objects instead of scalar values as template parameters.</span></span> <span data-ttu-id="8f082-145">Vedere [Usare un oggetto come parametro in un modello di Azure Resource Manager](./objects-as-parameters.md)</span><span class="sxs-lookup"><span data-stu-id="8f082-145">See [Use an object as a parameter in an Azure Resource Manager template](./objects-as-parameters.md)</span></span>

<!-- links -->
[azure-resource-manager-condition]: /azure/azure-resource-manager/resource-group-authoring-templates#resources
[azure-resource-manager-variable]: /azure/azure-resource-manager/resource-group-authoring-templates#variables
[vnet-peering-resource-schema]: /azure/templates/microsoft.network/virtualnetworks/virtualnetworkpeerings
[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples