---
title: Implementare un trasformatore e un agente di raccolta della proprietà in un modello di Azure Resource Manager
description: Illustra come implementare un trasformatore e un agente di raccolta di proprietà in un modello di Azure Resource Manager.
author: petertay
ms.date: 10/30/2018
ms.openlocfilehash: 1a6a01ee513609132d8522a79ccb81b7938651b5
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113808"
---
# <a name="implement-a-property-transformer-and-collector-in-an-azure-resource-manager-template"></a><span data-ttu-id="3daaf-103">Implementare un trasformatore e un agente di raccolta della proprietà in un modello di Azure Resource Manager</span><span class="sxs-lookup"><span data-stu-id="3daaf-103">Implement a property transformer and collector in an Azure Resource Manager template</span></span>

<span data-ttu-id="3daaf-104">In [Usare un oggetto come parametro in un modello di Azure Resource Manager][objects-as-parameters] si è appreso come archiviare i valori della proprietà della risorsa in un oggetto e applicarli a una risorsa durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="3daaf-104">In [use an object as a parameter in an Azure Resource Manager template][objects-as-parameters], you learned how to store resource property values in an object and apply them to a resource during deployment.</span></span> <span data-ttu-id="3daaf-105">Sebbene questa operazione sia molto utile per gestire i parametri, richiede tuttavia la mappatura delle proprietà dell'oggetto rispetto alle proprietà della risorse ogni volta che la si usa nel modello.</span><span class="sxs-lookup"><span data-stu-id="3daaf-105">While this is a very useful way to manage your parameters, it still requires you to map the object's properties to resource properties each time you use it in your template.</span></span>

<span data-ttu-id="3daaf-106">Per risolvere il problema, è possibile implementare un modello di trasformazione e dell'agente di raccolta della proprietà che esegue l'iterazione della matrice dell'oggetto e la trasforma nello schema JSON previsto dalla risorsa.</span><span class="sxs-lookup"><span data-stu-id="3daaf-106">To work around this, you can implement a property transform and collector template that iterates your object array and transforms it into the JSON schema expected by the resource.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3daaf-107">Per attuare questo approccio è necessario conoscere approfonditamente i modelli e le funzioni di Resource Manager.</span><span class="sxs-lookup"><span data-stu-id="3daaf-107">This approach requires that you have a deep understanding of Resource Manager templates and functions.</span></span>

<span data-ttu-id="3daaf-108">Viene ora illustrato come implementare un agente di raccolta e un trasformatore della proprietà con un esempio che consente di distribuire un [gruppo di sicurezza di rete][nsg].</span><span class="sxs-lookup"><span data-stu-id="3daaf-108">Let's take a look at how we can implement a property collector and transformer with an example that deploys a [network security group (NSG)][nsg].</span></span> <span data-ttu-id="3daaf-109">Il diagramma seguente mostra la relazione tra i modelli e le risorse all'interno di questi modelli:</span><span class="sxs-lookup"><span data-stu-id="3daaf-109">The diagram below shows the relationship between our templates and our resources within those templates:</span></span>

![architettura dell'agente di raccolta e del trasformatore della proprietà](../_images/collector-transformer.png)

<span data-ttu-id="3daaf-111">Il **modello di chiamata** include due risorse:</span><span class="sxs-lookup"><span data-stu-id="3daaf-111">Our **calling template** includes two resources:</span></span>

- <span data-ttu-id="3daaf-112">Collegamento a un modello che richiama il **modello dell'agente di raccolta**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-112">A template link that invokes our **collector template**.</span></span>
- <span data-ttu-id="3daaf-113">Risorsa NSG da distribuire.</span><span class="sxs-lookup"><span data-stu-id="3daaf-113">The NSG resource to deploy.</span></span>

<span data-ttu-id="3daaf-114">Il **modello dell'agente di raccolta** include due risorse:</span><span class="sxs-lookup"><span data-stu-id="3daaf-114">Our **collector template** includes two resources:</span></span>

- <span data-ttu-id="3daaf-115">Risorsa di **ancoraggio**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-115">An **anchor** resource.</span></span>
- <span data-ttu-id="3daaf-116">Collegamento a un modello che richiama il modello di trasformazione in un ciclo di copia.</span><span class="sxs-lookup"><span data-stu-id="3daaf-116">A template link that invokes the transform template in a copy loop.</span></span>

<span data-ttu-id="3daaf-117">Il **modello di trasformazione** include una sola risorsa: un modello vuoto con una variabile che trasforma il JSON `source` nello schema JSON previsto dalla risorsa NSG nel **modello principale**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-117">Our **transform template** includes a single resource: an empty template with a variable that transforms our `source` JSON to the JSON schema expected by our NSG resource in the **main template**.</span></span>

## <a name="parameter-object"></a><span data-ttu-id="3daaf-118">Oggetto parametro</span><span class="sxs-lookup"><span data-stu-id="3daaf-118">Parameter object</span></span>

<span data-ttu-id="3daaf-119">Verrà usato l'oggetto parametro `securityRules` di [oggetti come parametri][objects-as-parameters].</span><span class="sxs-lookup"><span data-stu-id="3daaf-119">We'll be using our `securityRules` parameter object from [objects as parameters][objects-as-parameters].</span></span> <span data-ttu-id="3daaf-120">Il **modello di trasformazione** trasformerà ogni oggetto della matrice `securityRules` nello schema JSON previsto per la risorsa NSG nel **modello di chiamata**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-120">Our **transform template** will transform each object in the `securityRules` array into the JSON schema expected by the NSG resource in our **calling template**.</span></span>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "networkSecurityGroupsSettings": {
      "value": {
          "securityRules": [
            {
              "name": "RDPAllow",
              "description": "allow RDP connections",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.0.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            },
            {
              "name": "HTTPAllow",
              "description": "allow HTTP connections",
              "direction": "Inbound",
              "priority": 200,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.1.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      }
    }
  }
```

<span data-ttu-id="3daaf-121">Prima di tutto viene esaminato il **modello di trasformazione**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-121">Let's look at our **transform template** first.</span></span>

## <a name="transform-template"></a><span data-ttu-id="3daaf-122">Modello di trasformazione</span><span class="sxs-lookup"><span data-stu-id="3daaf-122">Transform template</span></span>

<span data-ttu-id="3daaf-123">Il **modello di trasformazione** include due parametri che vengono passati dal **modello dell'agente di raccolta**:</span><span class="sxs-lookup"><span data-stu-id="3daaf-123">Our **transform template** includes two parameters that are passed from the **collector template**:</span></span>

- <span data-ttu-id="3daaf-124">`source` è un oggetto che riceve uno degli oggetti valore della proprietà dalla matrice della proprietà.</span><span class="sxs-lookup"><span data-stu-id="3daaf-124">`source` is an object that receives one of the property value objects from the property array.</span></span> <span data-ttu-id="3daaf-125">In questo esempio, gli oggetti della matrice `"securityRules"` verranno passati uno alla volta.</span><span class="sxs-lookup"><span data-stu-id="3daaf-125">In our example, each object from the `"securityRules"` array will be passed in one at a time.</span></span>
- <span data-ttu-id="3daaf-126">`state` è una matrice che riceve i risultati concatenati di tutte le trasformazioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="3daaf-126">`state` is an array that receives the concatenated results of all the previous transforms.</span></span> <span data-ttu-id="3daaf-127">Questa è la raccolta di JSON trasformati.</span><span class="sxs-lookup"><span data-stu-id="3daaf-127">This is the collection of transformed JSON.</span></span>

<span data-ttu-id="3daaf-128">I parametri sono simili ai seguenti:</span><span class="sxs-lookup"><span data-stu-id="3daaf-128">Our parameters look like this:</span></span>

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "source": { "type": "object" },
    "state": {
      "type": "array",
      "defaultValue": [ ]
    }
  },
```

<span data-ttu-id="3daaf-129">Il modello definisce anche una variabile denominata `instance`.</span><span class="sxs-lookup"><span data-stu-id="3daaf-129">Our template also defines a variable named `instance`.</span></span> <span data-ttu-id="3daaf-130">Esegue la trasformazione effettiva dell'oggetto `source` nello schema JSON necessario:</span><span class="sxs-lookup"><span data-stu-id="3daaf-130">It performs the actual transform of our `source` object into the required JSON schema:</span></span>

```json
  "variables": {
    "instance": [
      {
        "name": "[parameters('source').name]",
        "properties":{
            "description": "[parameters('source').description]",
            "protocol": "[parameters('source').protocol]",
            "sourcePortRange": "[parameters('source').sourcePortRange]",
            "destinationPortRange": "[parameters('source').destinationPortRange]",
            "sourceAddressPrefix": "[parameters('source').sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('source').destinationAddressPrefix]",
            "access": "[parameters('source').access]",
            "priority": "[parameters('source').priority]",
            "direction": "[parameters('source').direction]"
        }
      }
    ]

  },
```

<span data-ttu-id="3daaf-131">Infine, `output` di questo modello consente di concatenare le trasformazioni raccolte del parametro `state` con la trasformazione corrente eseguita dalla variabile `instance`:</span><span class="sxs-lookup"><span data-stu-id="3daaf-131">Finally, the `output` of our template concatenates the collected transforms of our `state` parameter with the current transform performed by our `instance` variable:</span></span>

```json
    "resources": [],
    "outputs": {
    "collection": {
      "type": "array",
      "value": "[concat(parameters('state'), variables('instance'))]"
    }
```

<span data-ttu-id="3daaf-132">Successivamente, viene esaminato il **modello dell'agente di raccolta** per vedere come passa i valori nel parametro.</span><span class="sxs-lookup"><span data-stu-id="3daaf-132">Next, let's take a look at our **collector template** to see how it passes in our parameter values.</span></span>

## <a name="collector-template"></a><span data-ttu-id="3daaf-133">Modello dell'agente di raccolta</span><span class="sxs-lookup"><span data-stu-id="3daaf-133">Collector template</span></span>

<span data-ttu-id="3daaf-134">Il **modello dell'agente di raccolta** include tre parametri:</span><span class="sxs-lookup"><span data-stu-id="3daaf-134">Our **collector template** includes three parameters:</span></span>

- <span data-ttu-id="3daaf-135">`source` è la matrice completa dell'oggetto parametro.</span><span class="sxs-lookup"><span data-stu-id="3daaf-135">`source` is our complete parameter object array.</span></span> <span data-ttu-id="3daaf-136">Viene passata dal **modello di chiamata**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-136">It's passed in by the **calling template**.</span></span> <span data-ttu-id="3daaf-137">Ha lo stesso nome del parametro `source` nel **modello di trasformazione**, ma c'è una differenza fondamentale che probabilmente l'utente avrà già notato: è la matrice completa, ma solo un elemento alla volta della matrice viene passato al **modello di trasformazione**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-137">This has the same name as the `source` parameter in our **transform template** but there is one key difference that you may have already noticed: this is the complete array, but we only pass one element of this array to the **transform template** at a time.</span></span>
- <span data-ttu-id="3daaf-138">`transformTemplateUri` è l'URI del **modello di trasformazione**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-138">`transformTemplateUri` is the URI of our **transform template**.</span></span> <span data-ttu-id="3daaf-139">In questa sede viene definito come parametro per il riutilizzo del modello.</span><span class="sxs-lookup"><span data-stu-id="3daaf-139">We're defining it as a parameter here for template reusability.</span></span>
- <span data-ttu-id="3daaf-140">`state` è una matrice inizialmente vuota che viene passata al **modello di trasformazione**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-140">`state` is an initially empty array that we pass to our **transform template**.</span></span> <span data-ttu-id="3daaf-141">Archivia la raccolta di oggetti parametro trasformati quando il ciclo di copia è stato completato.</span><span class="sxs-lookup"><span data-stu-id="3daaf-141">It stores the collection of transformed parameter objects when the copy loop is complete.</span></span>

<span data-ttu-id="3daaf-142">I parametri sono simili ai seguenti:</span><span class="sxs-lookup"><span data-stu-id="3daaf-142">Our parameters look like this:</span></span>

```json
  "parameters": {
    "source": { "type": "array" },
    "transformTemplateUri": { "type": "string" },
    "state": {
      "type": "array",
      "defaultValue": [ ]
    }
```

<span data-ttu-id="3daaf-143">Successivamente, viene definita una variabile denominata `count`.</span><span class="sxs-lookup"><span data-stu-id="3daaf-143">Next, we define a variable named `count`.</span></span> <span data-ttu-id="3daaf-144">Il suo valore è la lunghezza della matrice dell'oggetto parametro `source`:</span><span class="sxs-lookup"><span data-stu-id="3daaf-144">Its value is the length of the `source` parameter object array:</span></span>

```json
  "variables": {
    "count": "[length(parameters('source'))]"
  },
```

<span data-ttu-id="3daaf-145">Come si può immaginare, viene usata per il numero di iterazioni nel ciclo di copia.</span><span class="sxs-lookup"><span data-stu-id="3daaf-145">As you might suspect, we use it for the number of iterations in our copy loop.</span></span>

<span data-ttu-id="3daaf-146">Osservare ora le risorse.</span><span class="sxs-lookup"><span data-stu-id="3daaf-146">Now let's take a look at our resources.</span></span> <span data-ttu-id="3daaf-147">Si definiscono due risorse:</span><span class="sxs-lookup"><span data-stu-id="3daaf-147">We define two resources:</span></span>

- <span data-ttu-id="3daaf-148">`loop-0` è la risorsa in base zero per il ciclo di copia.</span><span class="sxs-lookup"><span data-stu-id="3daaf-148">`loop-0` is the zero-based resource for our copy loop.</span></span>
- <span data-ttu-id="3daaf-149">`loop-` è concatenata al risultato della funzione `copyIndex(1)` per generare un nome univoco basato sull'iterazione per la risorsa, che inizia con `1`.</span><span class="sxs-lookup"><span data-stu-id="3daaf-149">`loop-` is concatenated with the result of the `copyIndex(1)` function to generate a unique iteration-based name for our resource, starting with `1`.</span></span>

<span data-ttu-id="3daaf-150">Le risorse sono simili a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="3daaf-150">Our resources look like this:</span></span>

```json
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2015-01-01",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": { },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": { },
          "variables": { },
          "resources": [ ],
          "outputs": {
            "collection": {
              "type": "array",
              "value": "[parameters('state')]"
            }
          }
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2015-01-01",
      "name": "[concat('loop-', copyindex(1))]",
      "copy": {
        "name": "iterator",
        "count": "[variables('count')]",
        "mode": "serial"
      },
      "dependsOn": [
        "loop-0"
      ],
      "properties": {
        "mode": "Incremental",
        "templateLink": { "uri": "[parameters('transformTemplateUri')]" },
        "parameters": {
          "source": { "value": "[parameters('source')[copyindex()]]" },
          "state": { "value": "[reference(concat('loop-', copyindex())).outputs.collection.value]" }
        }
      }
    }
  ],
```

<span data-ttu-id="3daaf-151">Vengono adesso descritti dettagliatamente i parametri passati al **modello di trasformazione** nel modello annidato.</span><span class="sxs-lookup"><span data-stu-id="3daaf-151">Let's take a closer look at the parameters we're passing to our **transform template** in the nested template.</span></span> <span data-ttu-id="3daaf-152">Come indicato in precedenza, il parametro `source` passa l'oggetto corrente nella matrice dell'oggetto parametro `source`.</span><span class="sxs-lookup"><span data-stu-id="3daaf-152">Recall from earlier that our `source` parameter passes the current object in the `source` parameter object array.</span></span> <span data-ttu-id="3daaf-153">Nel parametro `state` viene eseguita la raccolta, in quanto prende l'output dell'iterazione del ciclo copia precedente&mdash;si noti che la funzione `reference()` usa la funzione `copyIndex()` senza parametri per fare riferimento a `name` dell'oggetto modello collegato precedente&mdash;e lo passa all'iterazione corrente.</span><span class="sxs-lookup"><span data-stu-id="3daaf-153">The `state` parameter is where the collection happens, because it takes the output of the previous iteration of our copy loop&mdash;notice that the `reference()` function uses the `copyIndex()` function with no parameter to reference the `name` of our previous linked template object&mdash;and passes it to the current iteration.</span></span>

<span data-ttu-id="3daaf-154">Infine, `output` del modello restituisce `output` dell'ultima iterazione del **modello di trasformazione**:</span><span class="sxs-lookup"><span data-stu-id="3daaf-154">Finally, the `output` of our template returns the `output` of the last iteration of our **transform template**:</span></span>

```json
  "outputs": {
    "result": {
      "type": "array",
      "value": "[reference(concat('loop-', variables('count'))).outputs.collection.value]"
    }
  }
```

<span data-ttu-id="3daaf-155">Potrebbe sembrare illogico restituire `output` dell'ultima iterazione del **modello di trasformazione** al **modello di chiamata** perché sembrava che venisse archiviato nel parametro `source`.</span><span class="sxs-lookup"><span data-stu-id="3daaf-155">It may seem counterintuitive to return the `output` of the last iteration of our **transform template** to our **calling template** because it appeared we were storing it in our `source` parameter.</span></span> <span data-ttu-id="3daaf-156">Tenere tuttavia presente che l'ultima iterazione del **modello di trasformazione** contiene la matrice completa degli oggetti proprietà trasformati e il risultato che si desidera restituire.</span><span class="sxs-lookup"><span data-stu-id="3daaf-156">However, remember that it's the last iteration of our **transform template** that holds the complete array of transformed property objects, and that's what we want to return.</span></span>

<span data-ttu-id="3daaf-157">Infine, è opportuno esaminare come chiamare il **modello dell'agente di raccolta** dal **modello di chiamata**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-157">Finally, let's take a look at how to call the **collector template** from our **calling template**.</span></span>

## <a name="calling-template"></a><span data-ttu-id="3daaf-158">Modello di chiamata</span><span class="sxs-lookup"><span data-stu-id="3daaf-158">Calling template</span></span>

<span data-ttu-id="3daaf-159">Il **modello di chiamata** definisce un solo parametro denominato `networkSecurityGroupsSettings`:</span><span class="sxs-lookup"><span data-stu-id="3daaf-159">Our **calling template** defines a single parameter named `networkSecurityGroupsSettings`:</span></span>

```json
...
"parameters": {
    "networkSecurityGroupsSettings": {
        "type": "object"
    }
```

<span data-ttu-id="3daaf-160">Successivamente, il modello definisce una sola variabile denominata `collectorTemplateUri`:</span><span class="sxs-lookup"><span data-stu-id="3daaf-160">Next, our template defines a single variable named `collectorTemplateUri`:</span></span>

```json
"variables": {
    "collectorTemplateUri": "[uri(deployment().properties.templateLink.uri, 'collector.template.json')]"
  }
```

<span data-ttu-id="3daaf-161">Come previsto, si tratta dell'URI per il **modello dell'agente di raccolta** che verrà usato dalla risorsa del modello collegato:</span><span class="sxs-lookup"><span data-stu-id="3daaf-161">As you would expect, this is the URI for the **collector template** that will be used by our linked template resource:</span></span>

```json
{
    "apiVersion": "2015-01-01",
    "name": "collector",
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('collectorTemplateUri')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "source" : {"value": "[parameters('networkSecurityGroupsSettings').securityRules]"},
            "transformTemplateUri": { "value": "[uri(deployment().properties.templateLink.uri, 'transform.json')]"}
        }
    }
}
```

<span data-ttu-id="3daaf-162">Vengono passati due parametri al **modello dell'agente di raccolta**:</span><span class="sxs-lookup"><span data-stu-id="3daaf-162">We pass two parameters to the **collector template**:</span></span>

- <span data-ttu-id="3daaf-163">`source` è la matrice dell'oggetto proprietà.</span><span class="sxs-lookup"><span data-stu-id="3daaf-163">`source` is our property object array.</span></span> <span data-ttu-id="3daaf-164">In questo esempio è il parametro `networkSecurityGroupsSettings`.</span><span class="sxs-lookup"><span data-stu-id="3daaf-164">In our example, it's our `networkSecurityGroupsSettings` parameter.</span></span>
- <span data-ttu-id="3daaf-165">`transformTemplateUri` è la variabile definita con l'URI del **modello dell'agente di raccolta**.</span><span class="sxs-lookup"><span data-stu-id="3daaf-165">`transformTemplateUri` is the variable we just defined with the URI of our **collector template**.</span></span>

<span data-ttu-id="3daaf-166">Infine, la risorsa `Microsoft.Network/networkSecurityGroups` assegna direttamente `output` del modello collegato `collector` alla proprietà `securityRules`:</span><span class="sxs-lookup"><span data-stu-id="3daaf-166">Finally, our `Microsoft.Network/networkSecurityGroups` resource directly assigns the `output` of the `collector` linked template resource to its `securityRules` property:</span></span>

```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "networkSecurityGroup1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[reference('collector').outputs.result.value]"
      }
    }
  ],
  "outputs": {
      "instance":{
          "type": "array",
          "value": "[reference('collector').outputs.result.value]"
      }

  }
```

## <a name="try-the-template"></a><span data-ttu-id="3daaf-167">Provare il modello</span><span class="sxs-lookup"><span data-stu-id="3daaf-167">Try the template</span></span>

<span data-ttu-id="3daaf-168">Un modello di esempio è disponibile in [GitHub][github].</span><span class="sxs-lookup"><span data-stu-id="3daaf-168">An example template is available on [GitHub][github].</span></span> <span data-ttu-id="3daaf-169">Per distribuire il modello, clonare il repository ed eseguire questi comandi dell'[interfaccia della riga di comando di Azure][cli]:</span><span class="sxs-lookup"><span data-stu-id="3daaf-169">To deploy the template, clone the repo and run the following [Azure CLI][cli] commands:</span></span>

```bash
git clone https://github.com/mspnp/template-examples.git
cd template-examples/example4-collector
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example4-collector/deploy.json \
    --parameters deploy.parameters.json
```

<!-- links -->
[objects-as-parameters]: ./objects-as-parameters.md
[resource-manager-linked-template]: /azure/azure-resource-manager/resource-group-linked-templates
[resource-manager-variables]: /azure/azure-resource-manager/resource-group-template-functions-deployment
[nsg]: /azure/virtual-network/virtual-networks-nsg
[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples
