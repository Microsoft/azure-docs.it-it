---
title: Ripristinare l'errore alla corretta distribuzione
description: Specificare che una distribuzione non riuscita deve eseguire il rollback a una distribuzione corretta.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460144"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Rollback in caso di errore per la corretta distribuzione

Quando una distribuzione non riesce, è possibile ridistribuire automaticamente una distribuzione precedente con esito positivo dalla cronologia della distribuzione. Questa funzionalità è utile se si ha uno stato valido noto per la distribuzione dell'infrastruttura e si desidera ripristinare questo stato. Ci sono una serie di avvertimenti e restrizioni:

- La ridistribuzione viene eseguita esattamente come è stata eseguita in precedenza con gli stessi parametri. Non è possibile modificare i parametri.
- La distribuzione precedente viene eseguita utilizzando la [modalità completa](./deployment-modes.md#complete-mode). Tutte le risorse non incluse nella distribuzione precedente vengono eliminate e tutte le configurazioni delle risorse vengono impostate sullo stato precedente. Assicurarsi di aver compreso appieno le [modalità](./deployment-modes.md)di distribuzione .
- La ridistribuzione influisce solo sulle risorse, le modifiche ai dati non sono interessate.
- È possibile usare questa funzionalità solo con le distribuzioni di gruppi di risorse, non con distribuzioni a livello di sottoscrizione o di gruppo di gestione. Per altre informazioni sulla distribuzione a livello di sottoscrizione, vedere Creare gruppi di risorse e risorse a livello di [sottoscrizione.](./deploy-to-subscription.md)
- È possibile usare questa opzione solo con le distribuzioni a livello di radice. Le distribuzioni di un modello annidato non sono disponibili per la ridistribuzione.

Per usare questa opzione, le distribuzioni devono avere nomi univoci in modo che sia possibile identificarle nella cronologia. Se non si dispone di nomi univoci, la distribuzione non riuscita corrente potrebbe sovrascrivere quella eseguita in modo corretto nella cronologia.

## <a name="powershell"></a>PowerShell

Per eseguire nuovamente l'ultima distribuzione con esito positivo, aggiungere il parametro `-RollbackToLastDeployment` come contrassegno.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Per eseguire nuovamente una distribuzione specifica, usare il parametro `-RollBackDeploymentName` e specificare il nome della distribuzione. La distribuzione specificata deve aver avuto esito positivo.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per eseguire nuovamente l'ultima distribuzione con esito positivo, aggiungere il parametro `--rollback-on-error` come contrassegno.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Per eseguire nuovamente una distribuzione specifica, usare il parametro `--rollback-on-error` e specificare il nome della distribuzione. La distribuzione specificata deve aver avuto esito positivo.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>API REST

Per eseguire nuovamente l'ultima distribuzione con esito positivo se la distribuzione corrente non riesce, usare:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Per eseguire nuovamente una distribuzione specifica se la distribuzione corrente non riesce, usare:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

La distribuzione specificata deve aver avuto esito positivo.

## <a name="next-steps"></a>Passaggi successivi

- Per implementare in modo sicuro il servizio in più di un'area, vedere [Azure Deployment Manager](deployment-manager-overview.md).
- Per specificare la modalità di gestione delle risorse che sono presenti nel gruppo, ma non sono definite nel modello, vedere [Modalità di distribuzione di Azure Resource Manager](deployment-modes.md).
- Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
- Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](secure-template-with-sas-token.md).
