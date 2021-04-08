---
title: Analizzare la sicurezza di rete-visualizzazione del gruppo di sicurezza-API REST di Azure
titleSuffix: Azure Network Watcher
description: Questo articolo descrive come usare l'API REST di Azure per analizzare una sicurezza delle macchine virtuali con la visualizzazione dei gruppi di sicurezza.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960584"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analizzare la protezione della macchina virtuale visualizzando un gruppo di sicurezza con l'API REST

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfaccia della riga di comando di Azure](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> L'API visualizzazione del gruppo di sicurezza non è più gestita e verrà presto deprecata. Utilizzare la [funzionalità regole di sicurezza effettive](./network-watcher-security-group-view-overview.md) che fornisce la stessa funzionalità. 

La visualizzazione di un gruppo di sicurezza consente di recuperare le regole di sicurezza di rete configurate ed effettive applicate a una macchina virtuale. Questa funzionalità è utile per controllare e diagnosticare i gruppi di sicurezza di rete e le regole configurate in una macchina virtuale per verificare che il traffico viene consentito o negato in modo corretto. Questo articolo illustra come recuperare le regole di sicurezza effettive ed applicate a una macchina virtuale tramite l'API REST


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Prima di iniziare

In questo scenario, si chiama l'API REST di Network Watcher per ottenere la visualizzazione del gruppo di sicurezza per una macchina virtuale. ARMclient viene usato per chiamare l'API REST con PowerShell. ARMClient è disponibile su Chocolate in [ARMClient su Chocolate](https://chocolatey.org/packages/ARMClient)

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher. Lo scenario presuppone inoltre che esista e possa essere usato un gruppo di risorse con una macchina virtuale valida.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo recupera le regole di sicurezza effettive e applicate a una determinata macchina virtuale.

## <a name="log-in-with-armclient"></a>Accedere con ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperare una macchina virtuale

Eseguire lo script seguente per restituire una macchina virtuale Il codice seguente necessita delle variabili:

- **SubscriptionId** : l'ID sottoscrizione può essere recuperato anche con il cmdlet **Get-AzSubscription** .
- **resourceGroupName**: il nome di un gruppo di risorse contenente le macchine virtuali.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Le informazioni necessarie sono l'**id** sotto il tipo di `Microsoft.Compute/virtualMachines` in risposta, come illustrato nell'esempio seguente:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Ottenere la visualizzazione del gruppo di sicurezza per la macchina virtuale

Nell'esempio seguente viene richiesta la visualizzazione del gruppo di sicurezza di una macchina virtuale di destinazione. I risultati di questo esempio possono essere usati per confrontare le regole e la sicurezza definite dall'origine per cercare la deviazione della configurazione.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Visualizzare la risposta

L'esempio seguente costituisce la risposta del comando precedente. I risultati mostrano tutte le regole di sicurezza effettive e applicate alla macchina virtuale, suddivise nei gruppi **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** e **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Consultare [Auditing Network Security Groups (NSG) with Network Watcher](network-watcher-security-group-view-powershell.md) (Verifica dei gruppi di sicurezza di rete con Network Watcher) per informazioni su come automatizzare la verifica dei gruppi di sicurezza di rete.