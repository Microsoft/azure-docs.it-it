---
title: Spostare Load Balancer esterni di Azure in un'altra area di Azure usando Azure PowerShell
description: Usare Azure Resource Manager modello per spostare Load Balancer esterni di Azure da un'area di Azure a un'altra usando Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 94cb46536bcf029a9e71a7238772ccc7b186b1dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500275"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Spostare Load Balancer esterni di Azure in un'altra area usando Azure PowerShell

Esistono diversi scenari in cui si vuole spostare il servizio di bilanciamento del carico esterno esistente da un'area a un'altra. Ad esempio, è possibile creare un servizio di bilanciamento del carico esterno con la stessa configurazione per il test. È anche possibile spostare un servizio di bilanciamento del carico esterno in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

I bilanciamenti del carico esterno di Azure non possono essere spostati da un'area all'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente e l'indirizzo IP pubblico di un servizio di bilanciamento del carico esterno.  È quindi possibile organizzare la risorsa in un'altra area esportando il servizio di bilanciamento del carico e l'indirizzo IP pubblico in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire i modelli nella nuova area.  Per altre informazioni su Resource Manager e sui modelli, vedere [Esporta gruppi di risorse in modelli](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Prerequisiti

- Verificare che il servizio di bilanciamento del carico esterno di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- I bilanciamenti del carico esterno di Azure non possono essere spostati tra le aree.  È necessario associare il nuovo servizio di bilanciamento del carico alle risorse nell'area di destinazione.

- Per esportare una configurazione del servizio di bilanciamento del carico esterno e distribuire un modello per creare un servizio di bilanciamento del carico esterno in un'altra area, è necessario il ruolo Collaboratore rete o versione successiva.
   
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a bilanciamento del carico, gruppi di sicurezza di rete, indirizzi IP pubblici e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare bilanciamenti del carico esterni nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta dei bilanciamenti del carico per questo processo.  Vedere [sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Preparazione e spostamento
I passaggi seguenti illustrano come preparare il servizio di bilanciamento del carico esterno per lo spostamento usando un modello di Gestione risorse e spostare la configurazione del servizio di bilanciamento del carico esterno nell'area di destinazione usando Azure PowerShell.  Come parte di questo processo, è necessario includere la configurazione IP pubblico del servizio di bilanciamento del carico esterno e prima di eseguire il bilanciamento del carico esterno.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Esportare il modello IP pubblico e distribuirlo da Azure PowerShell

1. Accedere alla propria sottoscrizione di Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e seguire le istruzioni visualizzate:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Ottenere l'ID risorsa dell'IP pubblico che si vuole spostare nell'area di destinazione e inserirlo in una variabile usando [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare l'IP pubblico di origine in un file con estensione JSON nella directory in cui si esegue il comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Il file scaricato avrà il nome del gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file che è stato esportato dal comando **\<resource-group-name>.json** e aprilo in un editor a propria scelta:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome IP pubblico, modificare la proprietà **defaultValue** del nome IP pubblico di origine con il nome dell'IP pubblico di destinazione assicurandosi che il nome sia tra virgolette:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Per modificare l'area di destinazione in spostare l'indirizzo IP pubblico, modificare la proprietà **location** nelle risorse:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet di Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation) eseguendo questo comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Se lo si desidera, è anche possibile modificare altri parametri nel modello, che sono facoltativi in base ai requisiti:

    * **SKU**: è possibile modificare il parametro SKU dell'indirizzo IP pubblico nella configurazione da standard a basic o da basic a standard modificando la proprietà s **sku** > **name** nel file **\<resource-group-name>.json**:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Per altre informazioni sulle differenze tra gli indirizzi IP pubblici con SKU basic o standard, vedere [Creare, modificare o eliminare un indirizzo IP pubblico](../virtual-network/virtual-network-public-ip-address.md).

    * **Metodo di allocazione dell'IP pubblico** e **timeout di inattività**: è possibile modificare entrambe queste opzioni nel modello modificando la proprietà **publicIPAllocationMethod** da **dinamica** a **statica** o da **statica** a **dinamica**. Il timeout di inattività può essere modificato impostando la proprietà **idleTimeoutInMinutes** sul valore desiderato.  Il valore predefinito è **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Per ulteriori informazioni sui metodi di allocazione e i valori di timeout di inattività, vedere [Creare, modificare o eliminare un indirizzo IP pubblico](../virtual-network/virtual-network-public-ip-address.md).


9. Salvare il file **\<resource-group-name>.json**.

10. Creare un gruppo di risorse nell'area di destinazione per l'IP pubblico di destinazione da distribuire utilizzando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuire il file **\<resource-group-name>.json** modificato nel gruppo di risorse creato nel passaggio precedente utilizzando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Per verificare che le risorse siano state create nell'area di destinazione, utilizzare [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) e [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Esportare il modello del servizio di bilanciamento del carico esterno e distribuirlo da Azure PowerShell

1. Accedere alla propria sottoscrizione di Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e seguire le istruzioni visualizzate:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ottenere l'ID risorsa del servizio di bilanciamento del carico esterno che si vuole spostare nell'area di destinazione e inserirlo in una variabile usando [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare la configurazione del servizio di bilanciamento del carico esterno di origine in un file con estensione JSON nella directory in cui si esegue il comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Il file scaricato avrà il nome del gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file che è stato esportato dal comando **\<resource-group-name>.json** e aprilo in un editor a propria scelta:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome del servizio di bilanciamento del carico esterno, modificare la proprietà **DefaultValue** del nome del servizio di bilanciamento del carico esterno di origine con il nome del servizio di bilanciamento del carico esterno di destinazione, verificare che il nome sia racchiuso tra virgolette:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Per modificare il valore dell'indirizzo IP pubblico di destinazione spostato in precedenza, è innanzitutto necessario ottenere l'ID risorsa e quindi copiarlo e incollarlo nel file con **\<resource-group-name> estensione JSON** .  Per ottenere l'ID, usare [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Digitare la variabile e premere INVIO per visualizzare l'ID della risorsa.  Evidenziare il percorso ID e copiarlo negli Appunti:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  Nel file **\<resource-group-name> JSON** incollare l' **ID risorsa** dalla variabile al posto del **DefaultValue** nel secondo parametro per l'indirizzo IP pubblico External ID, assicurarsi di racchiudere il percorso tra virgolette:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Se sono state configurate le regole NAT in uscita e in uscita per il servizio di bilanciamento del carico, in questo file sarà presente una terza voce per l'ID esterno per l'IP pubblico in uscita.  Ripetere i passaggi precedenti nell' **area di destinazione** per ottenere l'ID per l'indirizzo IP pubblico in uscita e incollare tale voce nel file con **\<resource-group-name> estensione JSON** :

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Per modificare l'area di destinazione in cui verrà spostata la configurazione del servizio di bilanciamento del carico esterno, modificare la proprietà **location** in **Resources** nel file con **\<resource-group-name> estensione JSON** :

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet di Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation) eseguendo questo comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Se lo si desidera, è anche possibile modificare altri parametri nel modello, che sono facoltativi in base ai requisiti:
    
    * **SKU** : è possibile modificare lo SKU del servizio di bilanciamento del carico esterno nella configurazione da standard a Basic o Basic a standard modificando la proprietà **SKU**  >  **Name** nel file con **\<resource-group-name> estensione JSON** :

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Per altre informazioni sulle differenze tra i bilanciamenti del carico SKU Basic e standard, vedere [Panoramica di Azure Load Balancer standard](./load-balancer-overview.md)

    * **Regole di bilanciamento del carico** : è possibile aggiungere o rimuovere le regole di bilanciamento del carico nella configurazione aggiungendo o rimuovendo le voci nella sezione **loadBalancingRules** del file **\<resource-group-name> JSON** :

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Per ulteriori informazioni sulle regole di bilanciamento del carico, vedere [che cos'è Azure Load Balancer?](./load-balancer-overview.md)

    * **Probe** : è possibile aggiungere o rimuovere un probe per il servizio di bilanciamento del carico nella configurazione aggiungendo o rimuovendo le voci nella sezione **Probe** del file con **\<resource-group-name> estensione JSON** :

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Per altre informazioni sui Probe di integrità di Azure Load Balancer, vedere [Probe di integrità Load Balancer](./load-balancer-custom-probe-overview.md)

    * **Regole NAT in ingresso** : è possibile aggiungere o rimuovere le regole NAT in ingresso per il servizio di bilanciamento del carico aggiungendo o rimuovendo le voci nella sezione **inboundNatRules** del file con **\<resource-group-name> estensione JSON** :

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Per completare l'aggiunta o la rimozione di una regola NAT in ingresso, è necessario che la regola sia presente o rimossa come proprietà del **tipo** alla fine del file con **\<resource-group-name> estensione JSON** :

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Per ulteriori informazioni sulle regole NAT in ingresso, vedere [che cos'è Azure Load Balancer?](./load-balancer-overview.md)

    * **Regole in uscita** : è possibile aggiungere o rimuovere regole in uscita nella configurazione modificando la proprietà **outboundRules** nel file **\<resource-group-name> JSON** :

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Per ulteriori informazioni sulle regole in uscita, vedere [Load Balancer regole in uscita](./load-balancer-outbound-connections.md#outboundrules)

13. Salvare il file **\<resource-group-name>.json**.
    
10. Creare o un gruppo di risorse nell'area di destinazione per la distribuzione del servizio di bilanciamento del carico esterno di destinazione usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Il gruppo di risorse esistente precedente può essere riutilizzato anche nell'ambito di questo processo:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuire il file **\<resource-group-name>.json** modificato nel gruppo di risorse creato nel passaggio precedente utilizzando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Per verificare che le risorse siano state create nell'area di destinazione, usare [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) e [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Discard 

Dopo la distribuzione, se si vuole ricominciare o rimuovere l'indirizzo IP pubblico e il servizio di bilanciamento del carico nella destinazione, eliminare il gruppo di risorse creato nella destinazione e l'indirizzo IP pubblico e il servizio di bilanciamento del carico spostati verranno eliminati.  Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento del NSG, eliminare il NSG di origine o il gruppo di risorse, usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) o [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) e [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un gruppo di sicurezza di rete di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo spostamento di risorse tra aree e sul ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Spostare macchine virtuali di Azure in un'altra area](../site-recovery/azure-to-azure-tutorial-migrate.md)