---
title: Creare gruppi di azioni con modelli di Gestione risorse
description: Informazioni su come creare un gruppo di azione usando un modello di Azure Resource Manager.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: a97fd56f1dc3409666ad86e81a7ac74fee076fa1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505687"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Creare un gruppo di azione con un modello di Resource Manager
L'articolo illustra come usare un [modello di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) per configurare gruppi di azione. Tramite i modelli è possibile configurare automaticamente i gruppi di azione che possono essere riusati in determinati tipi di avvisi. Questi gruppi di azione assicurano che tutte le parti corrette vengano notificate all'attivazione di un avviso.

I passaggi di base sono:

1. Creare un modello come file JSON che descriva come creare il gruppo di azione.

2. [Distribuire il modello con un metodo di distribuzione qualsiasi](../../azure-resource-manager/templates/deploy-powershell.md).

In primo luogo viene descritto come creare un modello di Resource Manager per un gruppo di azione, in cui le definizioni di azioni sono hardcoded nel modello. In secondo luogo viene descritto come creare un modello che accetta le informazioni di configurazione del webhook come parametri di input quando viene distribuito il modello.

## <a name="resource-manager-templates-for-an-action-group"></a>Modelli di Resource Manager per un gruppo di azione

Per creare un gruppo di azioni usando un modello di Resource Manager, si crea una risorsa di tipo `Microsoft.Insights/actionGroups`. Compilare quindi tutte le proprietà correlate. Ecco due modelli di esempio che creano un gruppo di azione.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sui [gruppi di azione](../../azure-monitor/platform/action-groups.md).
* Altre informazioni sugli [avvisi](alerts-overview.md).
* Informazioni su come aggiungere [avvisi usando un modello di Resource Manager](../../azure-monitor/platform/alerts-activity-log.md).
