---
title: Come arrestare il monitoraggio del cluster di Azure Red Hat OpenShift V3 | Microsoft Docs
description: Questo articolo descrive come arrestare il monitoraggio del cluster OpenShift di Azure Red Hat con informazioni dettagliate sul contenitore.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6a278898942e40d3347791d31a3c38341df01f7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731817"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Come arrestare il monitoraggio del cluster Azure Red Hat OpenShift V3

>[!IMPORTANT]
> Azure Red Hat OpenShift 3,11 verrà ritirato il 2022 giugno.
>
> A partire dal 2020 ottobre non sarà più possibile creare nuovi cluster 3,11.
> I cluster 3,11 esistenti continueranno a funzionare fino al giugno 2022, ma non saranno più supportati dopo tale data.
>
> Seguire questa guida per [creare un cluster Azure Red Hat OpenShift 4](../../openshift/tutorial-create-cluster.md).
> Per domande specifiche, [Contattaci](mailto:aro-feedback@microsoft.com).

Dopo aver abilitato il monitoraggio del cluster Azure Red Hat OpenShift versione 3. x, è possibile arrestare il monitoraggio del cluster con informazioni dettagliate sul contenitore se si decide di non monitorarlo più. Questo articolo illustra come eseguire questa operazione usando il modello di Azure Resource Manager fornito.  

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Sono disponibili due modelli di Azure Resource Manager per poter rimuovere le risorse della soluzione in modo coerente e ripetuto nel gruppo di risorse. Uno è un modello JSON che specifica la configurazione per arrestare il monitoraggio e l'altro contiene i valori dei parametri configurati per specificare l'ID risorsa cluster OpenShift e l'area di Azure in cui è distribuito il cluster.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="create-template"></a>Creare il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Salvare il file con il nome **OptOutTemplate.json** in una cartella locale.

3. Incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Modificare i valori per **aroResourceId** e **aroResourceLocation** usando i valori del cluster OpenShift, che è possibile trovare nella pagina **proprietà** del cluster selezionato.

    ![Pagina delle proprietà del contenitore](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Salvare il file con il nome **OptOutParam.json** in una cartella locale.

6. A questo punto è possibile distribuire il modello.

### <a name="remove-the-solution-using-azure-cli"></a>Rimuovere la soluzione tramite l'interfaccia della riga di comando di Azure

Eseguire il comando seguente con l'interfaccia della riga di comando di Azure in Linux per rimuovere la soluzione e pulire la configurazione nel cluster.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene restituito un messaggio simile al seguente che include il risultato:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Rimuovere la soluzione tramite PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Eseguire i comandi di PowerShell riportati di seguito nella cartella che contiene il modello per rimuovere la soluzione e pulire la configurazione dal cluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene restituito un messaggio simile al seguente che include il risultato:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Passaggi successivi

Se l'area di lavoro è stata creata solo per supportare il monitoraggio del cluster e non è più richiesta, è necessario eliminarla manualmente. Se non si ha familiarità con la modalità di eliminazione di un'area di lavoro, vedere [eliminare un'area di lavoro di Azure log Analytics](../logs/delete-workspace.md).