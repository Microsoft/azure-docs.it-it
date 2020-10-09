---
title: Risorse figlio nei modelli
description: Viene descritto come impostare il nome e il tipo per le risorse figlio in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 3a69829e674925982c618807f49433a033d8c5f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80743845"
---
# <a name="set-name-and-type-for-child-resources"></a>Imposta il nome e il tipo per le risorse figlio

Le risorse figlio sono risorse che esistono solo nel contesto di un'altra risorsa. Ad esempio, un' [estensione della macchina virtuale](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) non può esistere senza una [macchina virtuale](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). La risorsa di estensione è un elemento figlio della macchina virtuale.

In un modello di Gestione risorse, è possibile specificare la risorsa figlio all'interno della risorsa padre o all'esterno della risorsa padre. Nell'esempio seguente viene illustrata la risorsa figlio inclusa nella proprietà Resources della risorsa padre.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

Nell'esempio seguente viene illustrata la risorsa figlio al di fuori della risorsa padre. Questo approccio può essere usato se la risorsa padre non viene distribuita nello stesso modello o se si vuole usare [Copy](copy-resources.md) per creare più di una risorsa figlio.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

I valori specificati per il nome della risorsa e il tipo variano a seconda che la risorsa figlio sia definita all'interno o all'esterno della risorsa padre.

## <a name="within-parent-resource"></a>All'interno della risorsa padre

Quando viene definito nel tipo di risorsa padre, i valori del tipo e del nome vengono formattati come una singola parola senza barre.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Nell'esempio seguente viene illustrata una rete virtuale e una subnet. Si noti che la subnet è inclusa nell'array di risorse per la rete virtuale. Il nome è impostato su **Subnet1** e il tipo è impostato su **Subnet**. La risorsa figlio è contrassegnata come dipendente dalla risorsa padre perché la risorsa padre deve esistere prima che la risorsa figlio possa essere distribuita.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Il tipo di risorsa completo è ancora **Microsoft. Network/virtualNetworks/Subnets**. Non si fornisce **Microsoft. Network/virtualNetworks/** perché si presuppone dal tipo di risorsa padre.

Il nome della risorsa figlio è impostato su **Subnet1** , ma il nome completo include il nome padre. Non viene fornito **VNet1** perché si presuppone dalla risorsa padre.

## <a name="outside-parent-resource"></a>Risorsa padre esterna

Quando viene definito all'esterno della risorsa padre, si formatta il tipo e con le barre per includere il tipo e il nome padre.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Nell'esempio seguente vengono illustrate una rete virtuale e una subnet che sono entrambe definite a livello di radice. Si noti che la subnet non è inclusa nell'array di risorse per la rete virtuale. Il nome è impostato su **VNet1/Subnet1** e il tipo è impostato su **Microsoft. Network/virtualNetworks/Subnets**. La risorsa figlio è contrassegnata come dipendente dalla risorsa padre perché la risorsa padre deve esistere prima che la risorsa figlio possa essere distribuita.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla creazione di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](template-syntax.md).

* Per informazioni sul formato del nome della risorsa quando si fa riferimento alla risorsa, vedere la [funzione Reference](template-functions-resource.md#reference).
