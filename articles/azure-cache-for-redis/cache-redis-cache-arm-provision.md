---
title: Distribuire cache di Azure per Redis con Azure Resource Manager
description: Informazioni su come usare un modello di Azure Resource Manager per distribuire una cache di Azure per la risorsa Redis. Sono disponibili modelli per gli scenari comuni.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75412415"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Creare una Cache Redis di Azure usando un modello

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Questo argomento illustra come creare un modello di Azure Resource Manager che consente di distribuire un'istanza di Cache Redis di Azure. La cache è utilizzabile con un account di archiviazione esistente per mantenere i dati di diagnostica. Verrà anche illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Le impostazioni di diagnostica sono attualmente condivise da tutte le cache nella stessa area di una sottoscrizione. L'aggiornamento di una cache nell'area ha effetto su tutte le altre cache presenti nell'area.

Per ulteriori informazioni sulla creazione di modelli, vedere Creazione di modelli di [Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Per informazioni sulla sintassi e le proprietà JSON per i tipi di risorse cache, vedere [Tipi di risorse Microsoft.Cache](/azure/templates/microsoft.cache/allversions).

Per il modello completo, vedere [Modello di Cache Redis di Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Sono disponibili modelli di Resource Manager per il nuovo [livello Premium](cache-premium-tier-intro.md) . 
> 
> * [Creare un'istanza di Cache Redis di Azure Premium con clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Creare una Cache Redis Premium di Azure con persistenza dei dati](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Creare una cache Redis Premium distribuita in una rete virtuale](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Per verificare gli ultimi modelli, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) e cercare `Azure Cache for Redis`.
> 
> 

## <a name="what-you-will-deploy"></a>Elementi distribuiti
In questo modello, verrà distribuita una Cache Redis di Azure che utilizza un account di archiviazione esistente per dati di diagnostica.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri
Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata Parametri che contiene tutti i valori dei parametri.
È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Posizione della cache Redis di Azure. Per prestazioni ottimali, usare lo stesso percorso dell'app da usare con la cache.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Nome dell'account di archiviazione esistente da utilizzare per le diagnostiche. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Valore booleano che indica se è consentito l'accesso tramite le porte non SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Valore che indica se la diagnostica è abilitata. Utilizzare ON o OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Risorse da distribuire
### <a name="azure-cache-for-redis"></a>Cache Redis di Azure
Crea la cache Redis di Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2017-05-01-preview",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Comandi per eseguire la distribuzione
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
