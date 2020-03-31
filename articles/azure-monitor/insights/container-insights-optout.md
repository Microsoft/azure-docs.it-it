---
title: Come arrestare il monitoraggio del cluster del servizio Azure Kubernetes | Microsoft Docs
description: Questo articolo descrive come interrompere il monitoraggio del cluster del servizio Azure Kubernetes con Monitoraggio di Azure per contenitori.
ms.topic: conceptual
ms.date: 08/19/2019
ms.openlocfilehash: 618a4d7e10212dd2b042724b1ea11c97920dad57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275256"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Come arrestare il monitoraggio del servizio Azure Kubernetes con Monitoraggio di Azure per contenitori

Dopo aver abilitato il monitoraggio di un cluster del servizio Azure Kubernetes, è possibile interrompere il monitoraggio del cluster se non lo si ritiene più necessario. Questo articolo illustra come eseguire questa operazione usando l'interfaccia della riga di comando di Azure o i modelli di Azure Resource Manager disponibili.  


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare il comando [az servizio Azure Kubernetes disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) per disabilitare Monitoraggio di Azure per contenitori. Il comando rimuove l'agente dai nodi del cluster, non rimuove la soluzione o i dati già raccolti e archiviati nella risorsa di Monitoraggio di Azure.The command removes the agent from the cluster nodes, it does not remove the solution or the data already collected and stored in your Azure Monitor resource.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Per riabilitare il monitoraggio per il cluster, vedere [Abilitare il monitoraggio usando l'interfaccia della riga di comando di Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

Sono disponibili due modelli di Azure Resource Manager per poter rimuovere le risorse della soluzione in modo coerente e ripetuto nel gruppo di risorse. Uno è un modello JSON che specifica la configurazione per interrompere il monitoraggio, mentre l'altro contiene i valori dei parametri da configurare per specificare l'ID risorsa del cluster del servizio Azure Kubernetes e il gruppo di risorse in cui è distribuito il cluster.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Il modello deve essere distribuito nello stesso gruppo di risorse del cluster. Se si omettono altre proprietà o altri componenti aggiuntivi quando si usa questo modello, è possibile che vengano rimossi dal cluster. Ad esempio, *enableRBAC* per i criteri RBAC implementati nel cluster o *aksResourceTagValues* se i tag sono specificati per il cluster AKS.  
>

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-template"></a>Creare il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
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
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Sostituire i valori di **servizio Azure KubernetesResourceId** e **servizio Azure KubernetesResourceLocation** con i valori del cluster servizio Azure Kubernetes, indicati nella pagina **Proprietà** per il cluster selezionato.

    ![Pagina delle proprietà del contenitore](media/container-insights-optout/container-properties-page.png)

    Nella pagina **Proprietà** copiare anche l'**ID risorsa dell'area di lavoro**. Questo valore è necessario se si decide di eliminare l'area di lavoro Log Analytics in un secondo momento. Questa operazione che non viene eseguita nell'ambito di questo processo.

    Modificare i valori di **aksResourceTagValues** in modo che corrispondano ai valori di tag esistenti specificati per il cluster AKS.

5. Salvare il file con il nome **OptOutParam.json** in una cartella locale.

6. A questo punto è possibile distribuire il modello.

### <a name="remove-the-solution-using-azure-cli"></a>Rimuovere la soluzione tramite l'interfaccia della riga di comando di Azure

Eseguire il comando seguente con l'interfaccia della riga di comando di Azure in Linux per rimuovere la soluzione ed eseguire la pulizia della configurazione nel cluster del servizio Azure Kubernetes.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene restituito un messaggio simile al seguente che include il risultato:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Rimuovere la soluzione tramite PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Eseguire i comandi di PowerShell seguenti nella cartella che contiene il modello per rimuovere la soluzione ed eseguire la pulizia della configurazione nel cluster del servizio Azure Kubernetes.    

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

Se l'area di lavoro è stata creata solo per supportare il monitoraggio del cluster e non è più richiesta, è necessario eliminarla manualmente. Se non si ha familiarità con le procedure per l'eliminazione di un'area di lavoro, vedere [Eliminare un'area di lavoro Azure Log Analytics con il portale di Azure](../../log-analytics/log-analytics-manage-del-workspace.md). Non dimenticare **l'ID risorsa dell'area** di lavoro copiata in precedenza nel passaggio 4, è necessario.
