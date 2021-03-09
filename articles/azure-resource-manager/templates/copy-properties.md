---
title: Definire più istanze di una proprietà
description: Usare l'operazione di copia in un modello di Azure Resource Manager (modello ARM) per eseguire un'iterazione più volte durante la creazione di una proprietà in una risorsa.
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 958deba6152ffa3bcb1d2d79cd026c0cb2eebcbe
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521662"
---
# <a name="property-iteration-in-arm-templates"></a>Iterazione delle proprietà nei modelli ARM

Questo articolo illustra come creare più di un'istanza di una proprietà nel modello di Azure Resource Manager (modello ARM). Aggiungendo l' `copy` elemento alla sezione Properties di una risorsa nel modello, è possibile impostare dinamicamente il numero di elementi per una proprietà durante la distribuzione. È anche possibile evitare di ripetere la sintassi del modello.

È possibile usare solo `copy` con le risorse di primo livello, anche quando si applica `copy` a una proprietà. Per informazioni sulla modifica di una risorsa figlio in una risorsa di livello superiore, vedere [iterazione per una risorsa figlio](copy-resources.md#iteration-for-a-child-resource).

È anche possibile usare copia con [risorse](copy-resources.md), [variabili](copy-variables.md)e [output](copy-outputs.md).

## <a name="syntax"></a>Sintassi

Il formato generale dell'elemento Copy è il seguente:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Per `name` , specificare il nome della proprietà della risorsa che si desidera creare.

La `count` proprietà specifica il numero di iterazioni desiderate per la proprietà.

La `input` proprietà specifica le proprietà che si desidera ripetere. Si crea una matrice di elementi costruita dal valore della proprietà `input`.

## <a name="copy-limits"></a>Limiti di copia

Il conteggio non può essere maggiore di 800.

Il conteggio non può essere un numero negativo. Può essere zero se si distribuisce il modello con una versione recente dell'interfaccia della riga di comando di Azure, PowerShell o l'API REST. In particolare, è necessario usare:

* Azure PowerShell **2,6** o versione successiva
* INTERFACCIA della riga di comando di Azure **2.0.74** o versione successiva
* API REST versione **2019-05-10** o successiva
* Le [distribuzioni collegate](linked-templates.md) devono usare l'API versione **2019-05-10** o successiva per il tipo di risorsa di distribuzione

Le versioni precedenti di PowerShell, l'interfaccia della riga di comando e l'API REST non supportano zero per Count.

## <a name="property-iteration"></a>Iterazione delle proprietà

Nell'esempio seguente viene illustrato come applicare `copy` alla `dataDisks` Proprietà in una macchina virtuale:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Si noti che quando si usa `copyIndex` all'interno di un'iterazione di proprietà, è necessario specificare il nome dell'iterazione. L'iterazione della proprietà supporta anche un argomento offset. L'offset deve essere successivo al nome dell'iterazione, ad esempio `copyIndex('dataDisks', 1)` .

Resource Manager espande la matrice `copy` durante la distribuzione. Il nome della matrice diventa il nome della proprietà. I valori di input diventano le proprietà dell'oggetto. Il modello distribuito diventa:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

L'operazione di copia è utile quando si lavora con le matrici in quanto è possibile iterare ogni elemento della matrice. Usare la funzione `length` nella matrice per specificare il conteggio per le iterazioni e `copyIndex` per recuperare l'indice corrente nella matrice.

Il modello di esempio seguente crea un gruppo di failover per i database passati come matrice.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

L' `copy` elemento è una matrice, quindi è possibile specificare più di una proprietà per la risorsa.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

È possibile usare l'iterazione di risorse e di proprietà contemporaneamente. Fare riferimento all'iterazione di proprietà con il nome.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="example-templates"></a>Modelli di esempio

Nell'esempio seguente viene illustrato uno scenario comune per la creazione di più di un valore per una proprietà.

|Modello  |Descrizione  |
|---------|---------|
|[Distribuzione VM con un numero variabile di dischi dati](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuisce più dischi dati con una macchina virtuale. |

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire un'esercitazione, vedere [esercitazione: creare più istanze di risorse con i modelli ARM](template-tutorial-create-multiple-instances.md).
* Per altri usi dell'elemento Copy, vedere:
  * [Iterazione delle risorse nei modelli ARM](copy-resources.md)
  * [Iterazione delle variabili nei modelli ARM](copy-variables.md)
  * [Iterazione di output nei modelli ARM](copy-outputs.md)
* Per informazioni sulle sezioni di un modello, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).
* Per informazioni su come distribuire il modello, vedere [distribuire le risorse con i modelli ARM e Azure PowerShell](deploy-powershell.md).
