---
title: Fare riferimento a una rete virtuale esistente in un modello di set di scalabilità di AzureReference an existing virtual network in an Azure scale set template
description: Informazioni su come aggiungere una rete virtuale in un modello di set di scalabilità di macchine virtuali di Azure esistente
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273665"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Aggiungere un riferimento a una rete virtuale esistente in un modello di set di scalabilità di Azure

Questo articolo illustra come modificare il modello di set di [scalabilità](virtual-machine-scale-sets-mvss-start.md) di base da distribuire in una rete virtuale esistente anziché crearne una nuova.

## <a name="change-the-template-definition"></a>Modificare la definizione del modello

In un [articolo precedente](virtual-machine-scale-sets-mvss-start.md) avevamo creato un modello di set di scalabilità di base. Ora useremo il modello precedente e lo modificheremo per creare un modello che distribuisce un set di scalabilità in una rete virtuale esistente. 

Prima di tutto aggiungere un parametro `subnetId`. Questa stringa viene passata nella configurazione del set di scalabilità e permette al set di scalabilità di identificare la subnet creata in precedenza in cui distribuire le macchine virtuali. Questa stringa deve essere nel formato:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Ad esempio, per distribuire il set di scalabilità in una rete virtuale esistente denominata `myvnet`, subnet `mysubnet`, gruppo di risorse `myrg` e sottoscrizione`00000000-0000-0000-0000-000000000000`, l'ID subnet sarebbe il seguente: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Dal momento che si usa una rete virtuale esistente e non è necessario distribuirne una nuova, eliminare successivamente la risorsa rete virtuale dalla matrice `resources`.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

La rete virtuale esiste già prima della distribuzione del modello, non è quindi necessario specificare una clausola dependsOn dal set di scalabilità alla rete virtuale. Individuare le righe seguenti:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Infine, passare `subnetId` il set di parametri dall'utente (anziché utilizzare `resourceId` per ottenere l'ID di una rete virtuale nella stessa distribuzione, ovvero ciò che fa il modello di set di scalabilità di base.

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
