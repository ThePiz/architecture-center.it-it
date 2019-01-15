---
title: Usare un oggetto come parametro in un modello di Azure Resource Manager
description: Illustra come estendere le funzionalità dei modelli di Azure Resource Manager per usare oggetti come parametri.
author: petertay
ms.date: 10/30/2018
ms.openlocfilehash: f0826d8ed1ce446d295ebdacc66d8b0bef0b0dec
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54111207"
---
# <a name="use-an-object-as-a-parameter-in-an-azure-resource-manager-template"></a>Usare un oggetto come parametro in un modello di Azure Resource Manager

Quando si [creano modelli di Azure Resource Manager][azure-resource-manager-create-template], è possibile specificare i valori della proprietà delle risorse direttamente nel modello o definire un parametro e specificare i valori durante la distribuzione. È possibile usare un parametro per ogni valore della proprietà per distribuzioni di piccole dimensioni, ma è previsto un limite di 255 parametri per distribuzione. Quando si tratta di distribuzioni più grandi e complesse è possibile che i parametri terminino.

Un modo per risolvere questo problema consiste nell'usare un oggetto, invece di un valore, come parametro. A tale scopo, definire il parametro nel modello e specificare un oggetto JSON invece di un solo valore durante la distribuzione. Quindi, fare riferimento alle sottoproprietà del parametro usando la [`parameter()`funzione][azure-resource-manager-functions] e l'operatore punto nel modello.

Di seguito è riportato un esempio che distribuisce una risorsa di rete virtuale. Innanzitutto è necessario specificare un parametro `VNetSettings` nel modello e impostare `type` su `object`:

```json
...
"parameters": {
    "VNetSettings":{"type":"object"}
},
```

Successivamente inserire i valori per l'oggetto `VNetSettings`:

> [!NOTE]
> Per informazioni su come specificare i valori dei parametri durante la distribuzione, vedere la sezione **Parametri** di [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager][azure-resource-manager-authoring-templates].

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

Come si può notare, l'unico parametro specifica effettivamente tre sottoproprietà: `name`, `addressPrefixes` e `subnets`. Ognuna di queste sottoproprietà specifica un valore o altre sottoproprietà. Il risultato è che l'unico parametro specifica tutti i valori necessari per distribuire la rete virtuale.

Viene riportata ora la restante parte del modello per vedere come viene usato l'oggetto `VNetSettings`:

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

I valori dell'oggetto `VNetSettings` vengono applicati alle proprietà richieste dalla risorsa di rete virtuale usando la funzione `parameters()` sia con l'indicizzatore `[]` che con l'operatore punto. Questo approccio funziona se si desidera applicare in modo statico solo i valori dell'oggetto parametro alla risorsa. Tuttavia, se si desidera assegnare in modo dinamico una matrice di valori della proprietà durante la distribuzione è possibile usare un [ciclo di copia][azure-resource-manager-create-multiple-instances]. Per usare un ciclo di copia, indicare una matrice JSON dei valori della proprietà della risorsa; il ciclo di copia applica in modo dinamico questi valori alle proprietà della risorsa.

Se si usa l'approccio dinamico è possibile che si verifichi un problema. Per illustrare il problema osservare una matrice standard dei valori della proprietà. In questo esempio i valori delle proprietà vengono archiviati in una variabile. Si noti che qui sono disponibili due matrici &mdash; una denominata `firstProperty` e l'altra denominata `secondProperty`.

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

Osservare ora il modo in cui si accede alle proprietà della variabile tramite un ciclo di copia.

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

La funzione `copyIndex()` restituisce l'iterazione corrente del ciclo di copia che viene usata come un indice in ognuna delle due matrici contemporaneamente.

Tutto ciò funziona correttamente quando le due matrici hanno la stessa lunghezza. Il problema si presenta se si è verificato un errore e le due matrici hanno lunghezze diverse &mdash; in questo caso il modello non eseguirà la convalida durante la distribuzione. È possibile evitare questo problema includendo tutte le proprietà in un singolo oggetto, in quanto è molto più semplice vedere quando manca un valore. Ad esempio, è possibile esaminare un altro oggetto parametro in cui ogni elemento della matrice `propertyObject` è l'unione delle matrici `firstProperty` e `secondProperty` precedenti.

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

Si noti il terzo elemento nella matrice. Manca la proprietà `number`, tuttavia è molto più semplice accorgersi che manca quando si creano i valori del parametro in questo modo.

## <a name="using-a-property-object-in-a-copy-loop"></a>Usare un oggetto proprietà in un ciclo di copia

Questo approccio diventa ancora più utile in combinazione con [serial copy loop][azure-resource-manager-create-multiple], in particolare per la distribuzione di risorse figlio.

Per dimostrare questo concetto, si osservi un modello che distribuisce un [gruppo di sicurezza di rete][nsg] con due regole di sicurezza.

Osservare innanzitutto i parametri. Se si osserva il modello si nota che è stato definito un parametro denominato `networkSecurityGroupsSettings` che include una matrice denominata `securityRules`. Questa matrice contiene due oggetti JSON che specificano molte impostazioni per la regola di sicurezza.

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

Osservare ora il modello. La prima risorsa denominata `NSG1` distribuisce il gruppo di sicurezza di rete. La seconda risorsa denominata `loop-0` esegue due funzioni: prima di tutto, la funzione `dependsOn` da NSG e pertanto la distribuzione non viene avviata fino a che `NSG1` non viene completato; questa è la prima iterazione del ciclo di sequenza. La terza risorsa è un modello annidato che distribuisce le regole di sicurezza usando un oggetto per i valori del parametro come illustrato nell'ultimo esempio.

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

È opportuno esaminare più approfonditamente come si specificano i valori delle proprietà nella risorsa figlio `securityRules`. A tutte la proprietà viene fatto riferimento tramite la funzione `parameter()`, si usa poi l'operatore punto per fare riferimento alla matrice `securityRules`, indicizzata in base al valore attuale dell'iterazione. Infine, si usa un altro operatore punto per fare riferimento al nome dell'oggetto.

## <a name="try-the-template"></a>Provare il modello

Un modello di esempio è disponibile in [GitHub][github]. Per distribuire il modello, clonare il repository ed eseguire questi comandi dell'[interfaccia della riga di comando di Azure][cli]:

```bash
git clone https://github.com/mspnp/template-examples.git
cd template-examples/example3-object-param
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example3-object-param/deploy.json \
    --parameters deploy.parameters.json
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come creare un modello che scorre una matrice di oggetti e la trasforma in uno schema JSON. Vedere [Implementare un trasformatore e un agente di raccolta di proprietà in un modello di Azure Resource Manager](./collector.md)

<!-- links -->

[azure-resource-manager-authoring-templates]: /azure/azure-resource-manager/resource-group-authoring-templates
[azure-resource-manager-create-template]: /azure/azure-resource-manager/resource-manager-create-first-template
[azure-resource-manager-create-multiple-instances]: /azure/azure-resource-manager/resource-group-create-multiple
[azure-resource-manager-functions]: /azure/azure-resource-manager/resource-group-template-functions-resource
[nsg]: /azure/virtual-network/virtual-networks-nsg
[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples
