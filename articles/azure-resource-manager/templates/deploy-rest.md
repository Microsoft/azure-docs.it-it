---
title: Distribuire le risorse con l'API REST e il modello
description: Usare Azure Resource Manager e Gestione risorse API REST per distribuire le risorse in Azure. Le risorse sono definite in un modello di Resource Manager.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80153234"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Distribuire le risorse con i modelli ARM e Gestione risorse API REST

Questo articolo illustra come usare l'API REST di Gestione risorse con i modelli di Azure Resource Manager (ARM) per distribuire le risorse in Azure.

È possibile includere il modello nel corpo della richiesta o come collegamento a un file. Quando si usa un file, può trattarsi di un file locale oppure di un file esterno disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firma di accesso condiviso in fase di distribuzione.

## <a name="deployment-scope"></a>Ambito di distribuzione

La distribuzione può essere destinata a un gruppo di gestione, a una sottoscrizione di Azure o a un gruppo di risorse. Nella maggior parte dei casi, le distribuzioni verranno destinate a un gruppo di risorse. Utilizzare le distribuzioni di gruppi di gestione o sottoscrizioni per applicare criteri e assegnazioni di ruolo nell'ambito specificato. È anche possibile usare le distribuzioni delle sottoscrizioni per creare un gruppo di risorse e distribuirvi risorse. A seconda dell'ambito della distribuzione, si utilizzano comandi diversi.

Per eseguire la distribuzione in un **gruppo di risorse**, usare [distribuzioni-create](/rest/api/resources/deployments/createorupdate). La richiesta viene inviata a:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Per eseguire la distribuzione in una **sottoscrizione**, usare [distribuzioni-crea nell'ambito della sottoscrizione](/rest/api/resources/deployments/createorupdateatsubscriptionscope). La richiesta viene inviata a:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Per altre informazioni sulle distribuzioni a livello di sottoscrizione, vedere [creare gruppi di risorse e risorse a livello di sottoscrizione](deploy-to-subscription.md).

Per eseguire la distribuzione in un **gruppo di gestione**, usare [distribuzioni-crea nell'ambito del gruppo di gestione](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). La richiesta viene inviata a:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Per ulteriori informazioni sulle distribuzioni a livello di gruppo di gestione, vedere [creazione di risorse a livello di gruppo di gestione](deploy-to-management-group.md).

Gli esempi in questo articolo usano le distribuzioni di gruppi di risorse.

## <a name="deploy-with-the-rest-api"></a>Distribuire con l'API REST

1. Impostare [parametri e intestazioni comuni](/rest/api/azure/), tra cui i token di autenticazione.

1. Se non è già disponibile un gruppo di risorse, crearne uno. Specificare l'ID sottoscrizione, il nome del nuovo gruppo di risorse e il percorso per la soluzione. Per ulteriori informazioni, vedere [Create a resource group](/rest/api/resources/resourcegroups/createorupdate) (Creare un gruppo di risorse).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Con un corpo della richiesta del tipo:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Convalidare la distribuzione prima dell'esecuzione eseguendo l'operazione di [convalida della distribuzione di un modello](/rest/api/resources/deployments/validate) . Durante il test della distribuzione, specificare i parametri esattamente come quando si esegue la distribuzione (illustrata nel passaggio successivo).

1. Per distribuire un modello, specificare l'ID sottoscrizione, il nome del gruppo di risorse, il nome della distribuzione nell'URI della richiesta.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   Nel corpo della richiesta fornire un collegamento al file di modello e di parametri. Per altre informazioni sul file dei parametri, vedere [creare Gestione risorse file di parametri](parameter-files.md).

   Si noti che la **modalità** è impostata su **Incrementale**. Per eseguire una distribuzione completa, impostare la **modalità** su **completa**. Quando si utilizza la modalità di completamento, fare attenzione a non eliminare inavvertitamente le risorse non presenti nel modello.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Per registrare il contenuto della risposta, il contenuto della richiesta o entrambi, includere **debugSetting** nella richiesta.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    È possibile impostare l'account di archiviazione per l'utilizzzo di un token di firma di accesso condiviso (SAS). Per altre informazioni, vedere [Delegating Access with a Shared Access Signature](/rest/api/storageservices/delegating-access-with-a-shared-access-signature) (Delega dell'accesso con una firma di accesso condiviso).

    Se è necessario fornire un valore sensibile per un parametro (ad esempio una password), aggiungere tale valore a un insieme di credenziali delle chiavi. Recuperare l'insieme di credenziali delle chiavi durante la distribuzione, come illustrato nell'esempio precedente. Per ulteriori informazioni, vedere [passare valori protetti durante la distribuzione](key-vault-parameter.md).

1. Anziché eseguire il collegamento ai file per il modello e i parametri, è possibile includerli nel corpo della richiesta. Nell'esempio seguente viene illustrato il corpo della richiesta con il modello e il parametro inline:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Per ottenere lo stato della distribuzione del modello, usare [distribuzioni-Get](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Passaggi successivi

- Per eseguire il rollback a una distribuzione corretta quando viene visualizzato un errore, vedere eseguire [il rollback in caso di errore di distribuzione riuscita](rollback-on-error.md).
- Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
- Per altre informazioni sulla gestione delle operazioni REST asincrone, vedere [Track asynchronous Azure operations](../management/async-operations.md) (Tenere traccia delle operazioni asincrone di Azure).
- Per altre informazioni sui modelli, vedere [comprendere la struttura e la sintassi dei modelli ARM](template-syntax.md).

