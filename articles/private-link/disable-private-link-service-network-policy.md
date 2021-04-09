---
title: "Disabilitare i criteri di rete per l'indirizzo IP di origine del servizio di collegamento privato di Azure "
description: Informazioni su come disabilitare i criteri di rete per il collegamento privato di Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7528d12dcbfba09b4dae4ac07a5c683a7c11bd88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99548800"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Disabilitare i criteri di rete per l'indirizzo IP di origine del servizio di collegamento privato

Per scegliere un indirizzo IP di origine per il servizio di collegamento privato, `privateLinkServiceNetworkPolicies` nella subnet è necessaria un'impostazione di disabilitazione esplicita. Questa impostazione è applicabile solo per l'indirizzo IP privato specifico scelto come IP di origine del servizio di collegamento privato. Per altre risorse nella subnet, l'accesso viene controllato in base alla definizione delle regole di sicurezza dei gruppi di sicurezza di rete (NSG). 
 
Quando si usa il portale per creare un servizio di collegamento privato, questa impostazione viene disabilitata automaticamente come parte del processo di creazione. Le distribuzioni che usano qualsiasi client di Azure (PowerShell, interfaccia della riga di comando o modelli) richiedono un passaggio aggiuntivo per modificare questa proprietà. È possibile disabilitare il criterio usando cloud shell dalla portale di Azure o dalle installazioni locali di Azure PowerShell, dall'interfaccia della riga di comando di Azure o usare modelli di Azure Resource Manager.  
 
Attenersi alla procedura seguente per disabilitare i criteri di rete del servizio di collegamento privato per una rete virtuale denominata *myVirtualNetwork* con una subnet *predefinita* ospitata in un gruppo di risorse denominato *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Questa sezione descrive come disabilitare i criteri dell'endpoint privato della subnet usando Azure PowerShell.
Nel codice sostituire "default" con il nome della subnet virtuale.

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Questa sezione descrive come disabilitare i criteri dell'endpoint privato della subnet usando l'interfaccia della riga di comando di Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Uso di un modello
Questa sezione descrive come disabilitare i criteri dell'endpoint privato della subnet usando Azure Resource Manager modello.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull' [endpoint privato di Azure](private-endpoint-overview.md)
 
