---
title: Usare un oggetto come parametro in un modello di Azure Resource Manager
description: Viene descritto come estendere la funzionalità dei modelli di Azure Resource Manager per usare oggetti come parametri
author: petertay
ms.date: 06/09/2017
ms.openlocfilehash: dd53c55a26b2452c375d8d1e1a98886b15febaeb
ms.sourcegitcommit: 62945777e519d650159f0f963a2489b6bb6ce094
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48876758"
---
# <a name="use-an-object-as-a-parameter-in-an-azure-resource-manager-template"></a><span data-ttu-id="06e74-103">Usare un oggetto come parametro in un modello di Azure Resource Manager</span><span class="sxs-lookup"><span data-stu-id="06e74-103">Use an object as a parameter in an Azure Resource Manager template</span></span>

<span data-ttu-id="06e74-104">Quando si [creano modelli di Azure Resource Manager][azure-resource-manager-create-template], è possibile specificare i valori della proprietà delle risorse direttamente nel modello o definire un parametro e specificare i valori durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="06e74-104">When you [author Azure Resource Manager templates][azure-resource-manager-create-template], you can either specify resource property values directly in the template or define a parameter and provide values during deployment.</span></span> <span data-ttu-id="06e74-105">È possibile usare un parametro per ogni valore della proprietà per distribuzioni di piccole dimensioni, ma è previsto un limite di 255 parametri per distribuzione.</span><span class="sxs-lookup"><span data-stu-id="06e74-105">It's fine to use a parameter for each property value for small deployments, but there is a limit of 255 parameters per deployment.</span></span> <span data-ttu-id="06e74-106">Quando si tratta di distribuzioni più grandi e complesse è possibile che i parametri terminino.</span><span class="sxs-lookup"><span data-stu-id="06e74-106">Once you get to larger and more complex deployments you may run out of parameters.</span></span>

<span data-ttu-id="06e74-107">Un modo per risolvere questo problema consiste nell'usare un oggetto, invece di un valore, come parametro.</span><span class="sxs-lookup"><span data-stu-id="06e74-107">One way to solve this problem is to use an object as a parameter instead of a value.</span></span> <span data-ttu-id="06e74-108">A tale scopo, definire il parametro nel modello e specificare un oggetto JSON invece di un solo valore durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="06e74-108">To do this, define the parameter in your template and specify a JSON object instead of a single value during deployment.</span></span> <span data-ttu-id="06e74-109">Quindi, fare riferimento alle sottoproprietà del parametro usando la [`parameter()`funzione][azure-resource-manager-functions] e l'operatore punto nel modello.</span><span class="sxs-lookup"><span data-stu-id="06e74-109">Then, reference the subproperties of the parameter using the [`parameter()` function][azure-resource-manager-functions] and dot operator in your template.</span></span>

<span data-ttu-id="06e74-110">Di seguito è riportato un esempio che distribuisce una risorsa di rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="06e74-110">Let's take a look at an example that deploys a virtual network resource.</span></span> <span data-ttu-id="06e74-111">Innanzitutto è necessario specificare un parametro `VNetSettings` nel modello e impostare `type` su `object`:</span><span class="sxs-lookup"><span data-stu-id="06e74-111">First, let's specify a `VNetSettings` parameter in our template and set the `type` to `object`:</span></span>

```json
...
"parameters": {
    "VNetSettings":{"type":"object"}
},
```
<span data-ttu-id="06e74-112">Successivamente inserire i valori per l'oggetto `VNetSettings`:</span><span class="sxs-lookup"><span data-stu-id="06e74-112">Next, let's provide values for the `VNetSettings` object:</span></span>

> [!NOTE]
> <span data-ttu-id="06e74-113">Per informazioni su come specificare i valori dei parametri durante la distribuzione, vedere la sezione **Parametri** di [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager][azure-resource-manager-authoring-templates].</span><span class="sxs-lookup"><span data-stu-id="06e74-113">To learn how to provide parameter values during deployment, see the **parameters** section of [understand the structure and syntax of Azure Resource Manager templates][azure-resource-manager-authoring-templates].</span></span> 

```json
"parameters":{
    "VNetSettings":{
        "value":{
            "name":"VNet1",
            "addressPrefixes": [
                {
                    "name": "firstPrefix",
                    "addressPrefix": "10.0.0.0/22"
                }
            ],
            "subnets":[
                {
                    "name": "firstSubnet",
                    "addressPrefix": "10.0.0.0/24"
                },
                {
                    "name":"secondSubnet",
                    "addressPrefix":"10.0.1.0/24"
                }
            ]
        }
    }
}
```

<span data-ttu-id="06e74-114">Come si può notare, l'unico parametro specifica effettivamente tre sottoproprietà: `name`, `addressPrefixes` e `subnets`.</span><span class="sxs-lookup"><span data-stu-id="06e74-114">As you can see, our single parameter actually specifies three subproperties: `name`, `addressPrefixes`, and `subnets`.</span></span> <span data-ttu-id="06e74-115">Ognuna di queste sottoproprietà specifica un valore o altre sottoproprietà.</span><span class="sxs-lookup"><span data-stu-id="06e74-115">Each of these subproperties either specifies a value or other subproperties.</span></span> <span data-ttu-id="06e74-116">Il risultato è che l'unico parametro specifica tutti i valori necessari per distribuire la rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="06e74-116">The result is that our single parameter specifies all the values necessary to deploy our virtual network.</span></span>

<span data-ttu-id="06e74-117">Viene riportata ora la restante parte del modello per vedere come viene usato l'oggetto `VNetSettings`:</span><span class="sxs-lookup"><span data-stu-id="06e74-117">Now let's have a look at the rest of our template to see how the `VNetSettings` object is used:</span></span>

```json
...
"resources": [
    {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "[parameters('VNetSettings').name]",
        "location":"[resourceGroup().location]",
        "properties": {
          "addressSpace":{
              "addressPrefixes": [
                "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
              ]
          },
          "subnets":[
              {
                  "name":"[parameters('VNetSettings').subnets[0].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
                  }
              },
              {
                  "name":"[parameters('VNetSettings').subnets[1].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
                  }
              }
          ]
        }
    }
  ]
```
<span data-ttu-id="06e74-118">I valori dell'oggetto `VNetSettings` vengono applicati alle proprietà richieste dalla risorsa di rete virtuale usando la funzione `parameters()` sia con l'indicizzatore `[]` che con l'operatore punto.</span><span class="sxs-lookup"><span data-stu-id="06e74-118">The values of our `VNetSettings` object are applied to the properties required by our virtual network resource using the `parameters()` function with both the `[]` array indexer and the dot operator.</span></span> <span data-ttu-id="06e74-119">Questo approccio funziona se si desidera applicare in modo statico solo i valori dell'oggetto parametro alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="06e74-119">This approach works if you just want to statically apply the values of the parameter object to the resource.</span></span> <span data-ttu-id="06e74-120">Tuttavia, se si desidera assegnare in modo dinamico una matrice di valori della proprietà durante la distribuzione è possibile usare un [ciclo di copia][azure-resource-manager-create-multiple-instances].</span><span class="sxs-lookup"><span data-stu-id="06e74-120">However, if you want to dynamically assign an array of property values during deployment you can use a [copy loop][azure-resource-manager-create-multiple-instances].</span></span> <span data-ttu-id="06e74-121">Per usare un ciclo di copia, indicare una matrice JSON dei valori della proprietà della risorsa; il ciclo di copia applica in modo dinamico questi valori alle proprietà della risorsa.</span><span class="sxs-lookup"><span data-stu-id="06e74-121">To use a copy loop, you provide a JSON array of resource property values and the copy loop dynamically applies the values to the resource's properties.</span></span> 

<span data-ttu-id="06e74-122">Se si usa l'approccio dinamico è possibile che si verifichi un problema.</span><span class="sxs-lookup"><span data-stu-id="06e74-122">There is one issue to be aware of if you use the dynamic approach.</span></span> <span data-ttu-id="06e74-123">Per illustrare il problema osservare una matrice standard dei valori della proprietà.</span><span class="sxs-lookup"><span data-stu-id="06e74-123">To demonstrate the issue, let's take a look at a typical array of property values.</span></span> <span data-ttu-id="06e74-124">In questo esempio i valori delle proprietà vengono archiviati in una variabile.</span><span class="sxs-lookup"><span data-stu-id="06e74-124">In this example the values for our properties are stored in a variable.</span></span> <span data-ttu-id="06e74-125">Si noti che qui sono disponibili due matrici &mdash; una denominata `firstProperty` e l'altra denominata `secondProperty`.</span><span class="sxs-lookup"><span data-stu-id="06e74-125">Notice we have two arrays here&mdash;one named `firstProperty` and one named `secondProperty`.</span></span> 

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two", "three"
    ]
}
```

<span data-ttu-id="06e74-126">Osservare ora il modo in cui si accede alle proprietà della variabile tramite un ciclo di copia.</span><span class="sxs-lookup"><span data-stu-id="06e74-126">Now let's take a look at the way we access the properties in the variable using a copy loop.</span></span>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```

<span data-ttu-id="06e74-127">La funzione `copyIndex()` restituisce l'iterazione corrente del ciclo di copia che viene usata come un indice in ognuna delle due matrici contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="06e74-127">The `copyIndex()` function returns the current iteration of the copy loop, and we use that as an index into each of the two arrays simultaneously.</span></span>

<span data-ttu-id="06e74-128">Tutto ciò funziona correttamente quando le due matrici hanno la stessa lunghezza.</span><span class="sxs-lookup"><span data-stu-id="06e74-128">This works fine when the two arrays are the same length.</span></span> <span data-ttu-id="06e74-129">Il problema si presenta se si è verificato un errore e le due matrici hanno lunghezze diverse &mdash; in questo caso il modello non eseguirà la convalida durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="06e74-129">The issue arises if you've made a mistake and the two arrays are different lengths&mdash;in this case your template will fail validation during deployment.</span></span> <span data-ttu-id="06e74-130">È possibile evitare questo problema includendo tutte le proprietà in un singolo oggetto, in quanto è molto più semplice vedere quando manca un valore.</span><span class="sxs-lookup"><span data-stu-id="06e74-130">You can avoid this issue by including all your properties in a single object, because it is much easier to see when a value is missing.</span></span> <span data-ttu-id="06e74-131">Ad esempio, è possibile esaminare un altro oggetto parametro in cui ogni elemento della matrice `propertyObject` è l'unione delle matrici `firstProperty` e `secondProperty` precedenti.</span><span class="sxs-lookup"><span data-stu-id="06e74-131">For example, let's take a look another parameter object in which each element of the `propertyObject` array is the union of the `firstProperty` and `secondProperty` arrays from earlier.</span></span>

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

<span data-ttu-id="06e74-132">Si noti il terzo elemento nella matrice.</span><span class="sxs-lookup"><span data-stu-id="06e74-132">Notice the third element in the array?</span></span> <span data-ttu-id="06e74-133">Manca la proprietà `number`, tuttavia è molto più semplice accorgersi che manca quando si creano i valori del parametro in questo modo.</span><span class="sxs-lookup"><span data-stu-id="06e74-133">It's missing the `number` property, but it's much easier to notice that you've missed it when you're authoring the parameter values this way.</span></span>

## <a name="using-a-property-object-in-a-copy-loop"></a><span data-ttu-id="06e74-134">Usare un oggetto proprietà in un ciclo di copia</span><span class="sxs-lookup"><span data-stu-id="06e74-134">Using a property object in a copy loop</span></span>

<span data-ttu-id="06e74-135">Questo approccio diventa ancora più utile in combinazione con [serial copy loop][azure-resource-manager-create-multiple], in particolare per la distribuzione di risorse figlio.</span><span class="sxs-lookup"><span data-stu-id="06e74-135">This approach becomes even more useful when combined with the [serial copy loop][azure-resource-manager-create-multiple], particularly for deploying child resources.</span></span> 

<span data-ttu-id="06e74-136">Per dimostrare questo concetto, si osservi un modello che distribuisce un [gruppo di sicurezza di rete][nsg] con due regole di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="06e74-136">To demonstrate this, let's look at a template that deploys a [network security group (NSG)][nsg] with two security rules.</span></span> 

<span data-ttu-id="06e74-137">Osservare innanzitutto i parametri.</span><span class="sxs-lookup"><span data-stu-id="06e74-137">First, let's take a look at our parameters.</span></span> <span data-ttu-id="06e74-138">Se si osserva il modello si nota che è stato definito un parametro denominato `networkSecurityGroupsSettings` che include una matrice denominata `securityRules`.</span><span class="sxs-lookup"><span data-stu-id="06e74-138">When we look at our template we'll see that we've defined one parameter named `networkSecurityGroupsSettings` that includes an array named `securityRules`.</span></span> <span data-ttu-id="06e74-139">Questa matrice contiene due oggetti JSON che specificano molte impostazioni per la regola di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="06e74-139">This array contains two JSON objects that specify a number of settings for a security rule.</span></span>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
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

<span data-ttu-id="06e74-140">Osservare ora il modello.</span><span class="sxs-lookup"><span data-stu-id="06e74-140">Now let's take a look at our template.</span></span> <span data-ttu-id="06e74-141">La prima risorsa denominata `NSG1` distribuisce il gruppo di sicurezza di rete.</span><span class="sxs-lookup"><span data-stu-id="06e74-141">Our first resource named `NSG1` deploys the NSG.</span></span> <span data-ttu-id="06e74-142">La seconda risorsa denominata `loop-0` esegue due funzioni: prima di tutto, la funzione `dependsOn` da NSG e pertanto la distribuzione non viene avviata fino a che `NSG1` non viene completato; questa è la prima iterazione del ciclo di sequenza.</span><span class="sxs-lookup"><span data-stu-id="06e74-142">Our second resource named `loop-0` performs two functions: first, it `dependsOn` the NSG so its deployment doesn't begin until `NSG1` is completed, and it is the first iteration of the sequential loop.</span></span> <span data-ttu-id="06e74-143">La terza risorsa è un modello annidato che distribuisce le regole di sicurezza usando un oggetto per i valori del parametro come illustrato nell'ultimo esempio.</span><span class="sxs-lookup"><span data-stu-id="06e74-143">Our third resource is a nested template that deploys our security rules using an object for its parameter values as in the last example.</span></span>

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "networkSecurityGroupsSettings": {"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location":"[resourceGroup().location]",
      "properties": {
          "securityRules":[]
      }
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "loop-0",
        "dependsOn": [
            "NSG1"
        ],
        "properties": {
            "mode":"Incremental",
            "parameters":{},
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            }
        }       
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "[concat('loop-', copyIndex(1))]",
        "dependsOn": [
          "[concat('loop-', copyIndex())]"
        ],
        "copy": {
          "name": "iterator",
          "count": "[length(parameters('networkSecurityGroupsSettings').securityRules)]"
        },
        "properties": {
          "mode": "Incremental",
          "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
           "parameters": {},
            "variables": {},
            "resources": [
                {
                    "name": "[concat('NSG1/' , parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].name)]",
                    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
                    "apiVersion": "2016-09-01",
                    "location":"[resourceGroup().location]",
                    "properties":{
                        "description": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].description]",
                        "priority":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].priority]",
                        "protocol":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].protocol]",
                        "sourcePortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourcePortRange]",
                        "destinationPortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationPortRange]",
                        "sourceAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourceAddressPrefix]",
                        "destinationAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationAddressPrefix]",
                        "access":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].access]",
                        "direction":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].direction]"
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

<span data-ttu-id="06e74-144">È opportuno esaminare più approfonditamente come si specificano i valori delle proprietà nella risorsa figlio `securityRules`.</span><span class="sxs-lookup"><span data-stu-id="06e74-144">Let's take a closer look at how we specify our property values in the `securityRules` child resource.</span></span> <span data-ttu-id="06e74-145">A tutte la proprietà viene fatto riferimento tramite la funzione `parameter()`, si usa poi l'operatore punto per fare riferimento alla matrice `securityRules`, indicizzata in base al valore attuale dell'iterazione.</span><span class="sxs-lookup"><span data-stu-id="06e74-145">All of our properties are referenced using the `parameter()` function, and then we use the dot operator to reference our `securityRules` array, indexed by the current value of the iteration.</span></span> <span data-ttu-id="06e74-146">Infine, si usa un altro operatore punto per fare riferimento al nome dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="06e74-146">Finally, we use another dot operator to reference the name of the object.</span></span> 

## <a name="try-the-template"></a><span data-ttu-id="06e74-147">Provare il modello</span><span class="sxs-lookup"><span data-stu-id="06e74-147">Try the template</span></span>

<span data-ttu-id="06e74-148">Se si vuole sperimentare con questo modello, seguire questi passaggi:</span><span class="sxs-lookup"><span data-stu-id="06e74-148">If you would like to experiment with this template, follow these steps:</span></span> 

1.  <span data-ttu-id="06e74-149">Accedere al portale di Azure, selezionare l'icona **+**, cercare il tipo di risorsa **distribuzione modelli** e selezionarla.</span><span class="sxs-lookup"><span data-stu-id="06e74-149">Go to the Azure portal, select the **+** icon, and search for the **template deployment** resource type, and select it.</span></span>
2.  <span data-ttu-id="06e74-150">Passare alla pagina **distribuzione modelli** e selezionare il pulsante **crea**.</span><span class="sxs-lookup"><span data-stu-id="06e74-150">Navigate to the **template deployment** page, select the **create** button.</span></span> <span data-ttu-id="06e74-151">Questo pulsante apre il pannello **distribuzione personalizzata**.</span><span class="sxs-lookup"><span data-stu-id="06e74-151">This button opens the **custom deployment** blade.</span></span>
3.  <span data-ttu-id="06e74-152">Selezionare il pulsante **Modifica modello**.</span><span class="sxs-lookup"><span data-stu-id="06e74-152">Select the **edit template** button.</span></span>
4.  <span data-ttu-id="06e74-153">Eliminare il modello vuoto.</span><span class="sxs-lookup"><span data-stu-id="06e74-153">Delete the empty template.</span></span> 
5.  <span data-ttu-id="06e74-154">Copiare e incollare il modello di esempio nel riquadro destro.</span><span class="sxs-lookup"><span data-stu-id="06e74-154">Copy and paste the sample template into the right pane.</span></span>
6.  <span data-ttu-id="06e74-155">Fare clic sul pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="06e74-155">Select the **save** button.</span></span>
7.  <span data-ttu-id="06e74-156">Quando si torna al riquadro **distribuzione personalizzata**, selezionare il pulsante **modifica parametri**.</span><span class="sxs-lookup"><span data-stu-id="06e74-156">When you are returned to the **custom deployment** pane, select the **edit parameters** button.</span></span>
8.  <span data-ttu-id="06e74-157">Nel pannello **modifica parametri** eliminare il modello esistente.</span><span class="sxs-lookup"><span data-stu-id="06e74-157">On the **edit parameters** blade, delete the existing template.</span></span>
9.  <span data-ttu-id="06e74-158">Copiare e incollare il modello di parametro dall'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="06e74-158">Copy and paste the sample parameter template from above.</span></span>
10. <span data-ttu-id="06e74-159">Selezionare il pulsante **salva**, che rimanda al pannello **distribuzione personalizzata**.</span><span class="sxs-lookup"><span data-stu-id="06e74-159">Select the **save** button, which returns you to the **custom deployment** blade.</span></span>
11. <span data-ttu-id="06e74-160">Nel pannello **distribuzione personalizzata** selezionare la propria sottoscrizione, creare un nuovo gruppo di risorse o usarne uno esistente e selezionare una posizione.</span><span class="sxs-lookup"><span data-stu-id="06e74-160">On the **custom deployment** blade, select your subscription, either create new or use existing resource group, and select a location.</span></span> <span data-ttu-id="06e74-161">Esaminare i termini e le condizioni e selezionare la casella di controllo **I agree** (Accetto).</span><span class="sxs-lookup"><span data-stu-id="06e74-161">Review the terms and conditions, and select the **I agree** checkbox.</span></span>
12. <span data-ttu-id="06e74-162">Fare clic sul pulsante **Acquista**.</span><span class="sxs-lookup"><span data-stu-id="06e74-162">Select the **purchase** button.</span></span>

## <a name="next-steps"></a><span data-ttu-id="06e74-163">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="06e74-163">Next steps</span></span>

* <span data-ttu-id="06e74-164">È possibile espandere queste tecniche per implementare un [trasformatore e un agente di raccolta dell'oggetto proprietà](./collector.md).</span><span class="sxs-lookup"><span data-stu-id="06e74-164">You can expand upon these techniques to implement a [property object transformer and collector](./collector.md).</span></span> <span data-ttu-id="06e74-165">Le tecniche del trasformatore e dell'agente di raccolta sono più generali e possono essere collegate dai modelli.</span><span class="sxs-lookup"><span data-stu-id="06e74-165">The transformer and collector techniques are more general and can be linked from your templates.</span></span>
* <span data-ttu-id="06e74-166">Queste tecniche sono implementate anche nel [progetto dei blocchi predefiniti del modello](https://github.com/mspnp/template-building-blocks) e nelle [architetture di riferimento di Azure](/azure/architecture/reference-architectures/).</span><span class="sxs-lookup"><span data-stu-id="06e74-166">This technique is also implemented in the [template building blocks project](https://github.com/mspnp/template-building-blocks) and the [Azure reference architectures](/azure/architecture/reference-architectures/).</span></span> <span data-ttu-id="06e74-167">È possibile esaminare i modelli per vedere come è stata implementata questa tecnica.</span><span class="sxs-lookup"><span data-stu-id="06e74-167">You can review our templates to see how we've implemented this technique.</span></span>

<!-- links -->
[azure-resource-manager-authoring-templates]: /azure/azure-resource-manager/resource-group-authoring-templates
[azure-resource-manager-create-template]: /azure/azure-resource-manager/resource-manager-create-first-template
[azure-resource-manager-create-multiple-instances]: /azure/azure-resource-manager/resource-group-create-multiple
[azure-resource-manager-functions]: /azure/azure-resource-manager/resource-group-template-functions-resource
[nsg]: /azure/virtual-network/virtual-networks-nsg
