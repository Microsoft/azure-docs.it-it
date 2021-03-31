---
title: Spostare il gruppo di sicurezza di rete di Azure (NSG) in un'altra area di Azure usando Azure PowerShell
description: Usare il modello di Azure Resource Manager per spostare il gruppo di sicurezza di rete di Azure da un'area di Azure a un'altra usando Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: ad73ef03aa9623fb724f1397697fac18f659a90c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934987"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Spostare il gruppo di sicurezza di rete di Azure (NSG) in un'altra area usando Azure PowerShell

Esistono diversi scenari in cui si vuole spostare il gruppi esistente da un'area a un'altra. Ad esempio, è possibile creare un NSG con le stesse regole di configurazione e sicurezza per il test. Potrebbe anche essere necessario spostare un NSG in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

I gruppi di sicurezza di Azure non possono essere spostati da un'area all'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare le regole di sicurezza e configurazione esistenti di un NSG.  È quindi possibile organizzare la risorsa in un'altra area esportando il NSG in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area.  Per altre informazioni su Gestione risorse e sui modelli, vedere [esportare gruppi di risorse nei modelli](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Prerequisiti

- Verificare che il gruppo di sicurezza di rete di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- I gruppi di sicurezza di rete di Azure non possono essere spostati tra le aree.  È necessario associare il nuovo NSG alle risorse nell'area di destinazione.

- Per esportare una configurazione NSG e distribuire un modello per creare un NSG in un'altra area, è necessario il ruolo Collaboratore rete o versione successiva.
   
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a bilanciamento del carico, indirizzi IP pubblici e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare gruppi nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di gruppi per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Preparazione e spostamento
I passaggi seguenti illustrano come preparare il gruppo di sicurezza di rete per la configurazione e la regola di sicurezza spostare usando un modello di Gestione risorse e spostare le regole di sicurezza e configurazione NSG nell'area di destinazione usando Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Esportare il modello e distribuirlo da uno script

1. Accedere alla propria sottoscrizione di Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e seguire le istruzioni visualizzate:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Ottenere l'ID risorsa di NSG che si vuole spostare nell'area di destinazione e inserirlo in una variabile usando [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Esportare il NSG di origine in un file con estensione JSON nella directory in cui si esegue il comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Il file scaricato avrà il nome del gruppo di risorse da cui è stata esportata la risorsa.  Individuare il file che è stato esportato dal comando **\<resource-group-name>.json** e aprilo in un editor a propria scelta:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Per modificare il parametro del nome NSG, modificare la proprietà **DefaultValue** del nome NSG di origine con il nome del NSG di destinazione, verificare che il nome sia racchiuso tra virgolette:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Per modificare l'area di destinazione in cui verranno spostate le regole di sicurezza e configurazione NSG, modificare la proprietà **location** in **Resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Per ottenere i codici di posizione dell'area, è possibile usare il cmdlet di Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation) eseguendo questo comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. È anche possibile modificare altri parametri nel file con **\<resource-group-name> estensione JSON** se si sceglie e sono facoltativi in base ai requisiti:

    * **Regole di sicurezza** : è possibile modificare le regole distribuite nel NSG di destinazione aggiungendo o rimuovendo regole nella sezione **SecurityRules** del file con **\<resource-group-name> estensione JSON** :

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Per completare l'aggiunta o la rimozione delle regole nel NSG di destinazione, è necessario modificare anche i tipi di regole personalizzati alla fine del file con **\<resource-group-name> estensione JSON** nel formato dell'esempio seguente:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Salvare il file **\<resource-group-name>.json**.

10. Creare un gruppo di risorse nell'area di destinazione per la distribuzione di NSG di destinazione usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Distribuire il file **\<resource-group-name>.json** modificato nel gruppo di risorse creato nel passaggio precedente utilizzando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Per verificare che le risorse siano state create nell'area di destinazione, usare [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) e [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Discard 

Dopo la distribuzione, se si vuole ricominciare o rimuovere il NSG nella destinazione, eliminare il gruppo di risorse creato nella destinazione e il NSG spostato verrà eliminato.  Per rimuovere il gruppo di risorse, usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento del NSG, eliminare il NSG di origine o il gruppo di risorse, usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) o [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un gruppo di sicurezza di rete di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo spostamento di risorse tra aree e sul ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Spostare macchine virtuali di Azure in un'altra area](../site-recovery/azure-to-azure-tutorial-migrate.md)
