---
title: Distribuire in modo condizionale una risorsa in un modello di Azure Resource Manager
description: Viene descritto come estendere la funzionalità dei modelli di Azure Resource Manager per distribuire in modo condizionale una risorsa in base al valore di un parametro
author: petertay
ms.date: 06/09/2017
ms.openlocfilehash: e911e7dc41b4f71ebfaf13a00f8cdbb5b4e2578b
ms.sourcegitcommit: b0482d49aab0526be386837702e7724c61232c60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="conditionally-deploy-a-resource-in-an-azure-resource-manager-template"></a>Distribuire in modo condizionale una risorsa in un modello di Azure Resource Manager

Esistono alcuni scenari in cui è necessario progettare il modello per distribuire una risorsa in base a una condizione, ad esempio se un valore di parametro è presente o meno. Ad esempio, il modello può distribuire una rete virtuale e includere i parametri per specificare altre reti virtuali per il peering. Se i valori del parametro non sono stati specificati per il peering, Gestione risorse non dovrebbe distribuire la risorsa di peering.

A tale scopo, usare l'[`condition`elemento][azure-resource-manager-condition] nella risorsa per testare la lunghezza della matrice del parametro. Se la lunghezza è zero, restituire `false` per impedire la distribuzione, ma per tutti i valori maggiori di zero restituire `true` per consentire la distribuzione.

## <a name="example-template"></a>Modello di esempio

Viene ora illustrato un modello di esempio che illustra questa operazione. Il modello usa l'[`condition`elemento][azure-resource-manager-condition] per controllare la distribuzione della risorsa `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`. Questa risorsa crea un peering tra due reti virtuali di Azure nella stessa area.

Viene esaminata ora ciascuna sezione del modello.

L'elemento `parameters` definisce un singolo parametro denominato `virtualNetworkPeerings`: 

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
Il parametro `virtualNetworkPeerings` è un `array` e presenta lo schema seguente:

```json
"virtualNetworkPeerings": [
    {
        "remoteVirtualNetwork": {
            "name": "my-other-virtual-network"
        },
        "allowForwardedTraffic": true,
        "allowGatewayTransit": true,
        "useRemoteGateways": false
    }
]
```

Le proprietà di questo parametro specificano le [impostazioni correlate al peering delle reti virtuali][vnet-peering-resource-schema]. Verranno indicati i valori per queste proprietà quando si specificherà la risorsa `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` nella sezione `resources`:

```json
"resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('vnp-', copyIndex())]",
      "condition": "[greater(length(parameters('virtualNetworkPeerings')), 0)]",
      "dependsOn": [
        "virtualNetworks"
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
Esistono alcuni aspetti da considerare in questa parte del modello. Innanzitutto la risorsa effettiva viene distribuita in un modello di inline di tipo `Microsoft.Resources/deployments` che include il proprio modello che effettivamente distribuisce `Microsoft.Network/virtualNetworks/virtualNetworkPeerings`.

`name` del modello inline viene reso univoco concatenando l'iterazione corrente di `copyIndex()` con il prefisso `vnp-`. 

L'elemento `condition` specifica che la risorsa deve essere elaborata quando la funzione `greater()` restituisce `true`. Qui viene testato se la matrice del parametro `virtualNetworkPeerings` è `greater()` di zero. In questo caso, restituisce `true` e `condition` viene soddisfatta. In caso contrario, è `false`.

Successivamente si specifica il ciclo `copy`. Si tratta di un ciclo `serial` che implica che il ciclo viene eseguito in sequenza, mentre ogni risorsa è in attesa fino a quando l'ultima risorsa viene distribuita. La proprietà `count` specifica il numero di volte in cui il ciclo esegue l'iterazione. In genere si imposta sulla lunghezza della matrice `virtualNetworkPeerings` perché contiene gli oggetti parametro che specificano la risorsa che si desidera distribuire. Tuttavia, se la si imposta in questo modo la convalida avrà esito negativo se la matrice è vuota perché Gestione risorse si accorge che si sta tentando di accedere a proprietà che non esistono. Tuttavia è possibile tentare di risolvere questo problema. Vengono esaminate ora le variabili necessarie:

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

La variabile `workaround` include due proprietà, una denominata `true` e una denominata `false`. La proprietà `true` restituisce il valore della matrice del parametro `virtualNetworkPeerings`. La proprietà `false` restituisce un oggetto vuoto, incluse le proprietà denominate che Gestione risorse si aspetta di trovare &mdash; si noti che `false` è effettivamente una matrice, come il parametro `virtualNetworkPeerings`, che soddisferà la convalida. 

La variabile `peerings` usa la variabile `workaround` controllando nuovamente se la lunghezza della matrice del parametro `virtualNetworkPeerings` è maggiore di zero. In questo caso, `string` restituisce `true` e la variabile `workaround` restituisce matrice del parametro `virtualNetworkPeerings`. In caso contrario, restituisce `false` e la variabile `workaround` restituisce l'oggetto vuoto nel primo elemento della matrice.

Ora che è stato risolto il problema di convalida, basta specificare la distribuzione della risorsa `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` nel modello annidato, passando `name` e `properties` dalla matrice `virtualNetworkPeerings` del parametro. Questo può essere visualizzato nell'elemento annidato `template` nell'elemento `properties` della risorsa.

## <a name="next-steps"></a>Passaggi successivi

* Queste tecniche sono implementate nel [progetto dei blocchi predefiniti del modello](https://github.com/mspnp/template-building-blocks) e nelle [architetture di riferimento di Azure](/azure/architecture/reference-architectures/). È possibile usarle per creare la propria architettura o distribuire un'architettura di riferimento.

<!-- links -->
[azure-resource-manager-condition]: /azure/azure-resource-manager/resource-group-authoring-templates#resources
[azure-resource-manager-variable]: /azure/azure-resource-manager/resource-group-authoring-templates#variables
[vnet-peering-resource-schema]: /azure/templates/microsoft.network/virtualnetworks/virtualnetworkpeerings