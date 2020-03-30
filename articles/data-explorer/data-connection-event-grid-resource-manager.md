---
title: Creare una connessione dati di Griglia di eventi per Azure Data Explorer usando il modello di Azure Resource ManagerCreate an Event Grid data connection for Azure Data Explorer by using Azure Resource Manager template
description: In questo articolo viene illustrato come creare una connessione dati di Griglia di eventi per Azure Data Explorer usando il modello di Azure Resource Manager.In this article, you learn how to create an Event Grid data connection for Azure Data Explorer by using Azure Resource Manager template.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: a2e29b9811ab163642e7f65ded584c4889c199db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74669259"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-azure-resource-manager-template"></a>Creare una connessione dati di Griglia di eventi per Azure Data Explorer usando il modello di Azure Resource ManagerCreate an Event Grid data connection for Azure Data Explorer by using Azure Resource Manager template

> [!div class="op_single_selector"]
> * [Portale](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](data-connection-event-grid-resource-manager.md)


Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Azure Data Explorer offre l'inserimento (caricamento dei dati) da hub eventi, hub IoT e BLOB scritti in contenitori BLOB. In questo articolo viene creata una connessione dati griglia di eventi per Azure Data Explorer usando il modello di Azure Resource Manager.In this article, you create an Event Grid data connection for Azure Data Explorer by using Azure Resource Manager template.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Creare [un cluster e un databaseCreate a cluster and database](create-cluster-database-portal.md)
* Creare [un mapping di tabelle e colonne](ingest-data-event-grid.md#create-a-target-table-in-azure-data-explorer)
* Creare [un hub eventiCreate an event hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)
* Creare [un account di archiviazione con una sottoscrizione griglia di eventi.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

## <a name="azure-resource-manager-template-for-adding-an-event-grid-data-connection"></a>Modello di Azure Resource Manager per l'aggiunta di una connessione dati griglia di eventiAzure Resource Manager template for adding an Event Grid data connection

L'esempio seguente mostra un modello di Azure Resource Manager per l'aggiunta di una connessione dati griglia eventi.  È possibile [modificare e distribuire il modello nel portale](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) di Azure usando il modulo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namespaces_eventhubns_name": {
            "type": "string",
            "defaultValue": "eventhubns",
            "metadata": {
                "description": "Specifies the Event Hub Namespace name."
            }
        },
        "EventHubs_eventhubdemo_name": {
            "type": "string",
            "defaultValue": "eventhubdemo",
            "metadata": {
                "description": "Specifies the Event Hub name."
            }
        },
        "consumergroup_default_name": {
            "type": "string",
            "defaultValue": "$Default",
            "metadata": {
                "description": "Specifies the consumer group of the Event Hub."
            }
        },
        "StorageAccounts_storagedemo_name": {
            "type": "string",
            "defaultValue": "storagedemo",
            "metadata": {
                "description": "Specifies the storage account name"
            }
        },
        "Clusters_kustocluster_name": {
            "type": "string",
            "defaultValue": "kustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databases_kustodb_name": {
            "type": "string",
            "defaultValue": "kustodb",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "tables_kustotable_name": {
            "type": "string",
            "defaultValue": "kustotable",
            "metadata": {
                "description": "Specifies the name of the table"
            }
        },
        "mapping_kustomapping_name": {
            "type": "string",
            "defaultValue": "kustomapping",
            "metadata": {
                "description": "Specifies the name of the mapping rule"
            }
        },
        "dataformat_type": {
            "type": "string",
            "defaultValue": "csv",
            "metadata": {
                "description": "Specifies the data format"
            }
        },
        "dataconnections_kustodc_name": {
            "type": "string",
            "defaultValue": "kustodc",
            "metadata": {
                "description": "Name of the data connection to create"
            }
        },
        "subscriptionId": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "Specifies the subscriptionId of the resources"
            }
        },
        "resourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "metadata": {
                "description": "Specifies the resourceGroup of the resources"
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
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/DataConnections",
            "apiVersion": "2019-09-07",
            "name": "[concat(parameters('Clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'), '/', parameters('dataconnections_kustodc_name'))]",
            "location": "[parameters('location')]",
            "kind": "EventGrid",
            "properties": {
                "storageAccountResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('StorageAccounts_storagedemo_name'))]",
                "eventHubResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.EventHub/namespaces/eventhubs', parameters('namespaces_eventhubns_name'), parameters('EventHubs_eventhubdemo_name'))]",
                "consumerGroup": "[parameters('consumergroup_default_name')]",
                "tableName": "[parameters('tables_kustotable_name')]",
                "mappingRuleName": "[parameters('mapping_kustomapping_name')]",
                "dataFormat": "[parameters('dataformat_type')]"
            }
        }
    ]
}
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]
