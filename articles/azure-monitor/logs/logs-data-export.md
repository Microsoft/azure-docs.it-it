---
title: Log Analytics l'esportazione dei dati dell'area di lavoro in monitoraggio di Azure (anteprima)
description: Log Analytics esportazione dei dati consente di esportare continuamente i dati delle tabelle selezionate dall'area di lavoro Log Analytics a un account di archiviazione di Azure o a hub eventi di Azure al momento della raccolta.
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 02/07/2021
ms.openlocfilehash: 981ebbecd4783ae529c5b0d97c82ea052511f77f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384188"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics l'esportazione dei dati dell'area di lavoro in monitoraggio di Azure (anteprima)
Log Analytics l'esportazione dei dati dell'area di lavoro in monitoraggio di Azure consente di esportare in modo continuativo i dati dalle tabelle selezionate nell'area di lavoro Log Analytics a un account di archiviazione di Azure o a hub eventi di Azure al momento della raccolta. Questo articolo fornisce informazioni dettagliate su questa funzionalità e i passaggi per configurare l'esportazione dei dati nelle aree di lavoro.

## <a name="overview"></a>Panoramica
Una volta configurata l'esportazione dei dati per l'area di lavoro Log Analytics, tutti i nuovi dati inviati alle tabelle selezionate nell'area di lavoro vengono automaticamente esportati nell'account di archiviazione in BLOB di Accodamento orario o nell'hub eventi in tempo quasi reale.

![Panoramica sull'esportazione dei dati](media/logs-data-export/data-export-overview.png)

Tutti i dati delle tabelle incluse vengono esportati senza un filtro. Ad esempio, quando si configura una regola di esportazione dei dati per la tabella *SecurityEvent* , tutti i dati inviati alla tabella *SecurityEvent* vengono esportati a partire dall'ora di configurazione.


## <a name="other-export-options"></a>Altre opzioni di esportazione
Log Analytics esportazione dei dati dell'area di lavoro Esporta continuamente i dati da un'area di lavoro di Log Analytics. Di seguito sono riportate altre opzioni per esportare i dati per scenari specifici:

- Esportazione pianificata da una query di log tramite un'app per la logica. Questa operazione è simile alla funzionalità di esportazione dei dati, ma consente di inviare dati filtrati o aggregati in archiviazione di Azure. Questo metodo è soggetto ai [limiti delle query di log](../service-limits.md#log-analytics-workspaces), vedere [archiviare dati da un'area di lavoro log Analytics ad archiviazione di Azure tramite l'app per la logica](logs-export-logic-app.md).
- Esportare una volta nel computer locale usando lo script di PowerShell. Vedere [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="limitations"></a>Limitazioni

- La configurazione può essere eseguita attualmente usando l'interfaccia della riga di comando o le richieste REST. Portale di Azure o PowerShell non sono ancora supportati.
- L' ```--export-all-tables``` opzione nell'interfaccia della riga di comando e REST non è supportata e verrà rimossa. È necessario specificare in modo esplicito l'elenco delle tabelle nelle regole di esportazione.
- Le tabelle supportate sono attualmente limitate a quelle specifiche nella sezione [tabelle supportate](#supported-tables) riportata di seguito. Le tabelle di log personalizzate, ad esempio, non sono attualmente supportate.
- Se la regola di esportazione dei dati include una tabella non supportata, l'operazione avrà esito positivo, ma non verranno esportati dati per tale tabella finché la tabella non viene supportata. 
- Se la regola di esportazione dei dati include una tabella che non esiste, l'operazione avrà esito negativo e si verificherà un errore ```Table <tableName> does not exist in the workspace``` .
- L'area di lavoro Log Analytics può trovarsi in qualsiasi area, ad eccezione di quanto segue:
  - Aree di Azure per enti pubblici
  - Giappone occidentale
  - Brasile meridionale orientale
  - Norvegia orientale
  - Emirati Arabi Uniti settentrionali
- È possibile creare due regole di esportazione in un'area di lavoro: in può essere una regola per hub eventi e una regola per l'account di archiviazione.
- L'account di archiviazione di destinazione o l'hub eventi deve trovarsi nella stessa area dell'area di lavoro Log Analytics.
- I nomi delle tabelle da esportare non possono contenere più di 60 caratteri per un account di archiviazione e non più di 47 caratteri in un hub eventi. Le tabelle con nomi più lunghi non verranno esportate.
- Il supporto per il BLOB di Accodamento per Azure Data Lake Storage è ora disponibile in [anteprima pubblica limitata](https://azure.microsoft.com/updates/append-blob-support-for-azure-data-lake-storage-preview/)

## <a name="data-completeness"></a>Completezza dei dati
L'esportazione dei dati continuerà a ritentare l'invio dei dati per un massimo di 30 minuti nel caso in cui la destinazione non sia disponibile. Se non è ancora disponibile dopo 30 minuti, i dati verranno rimossi finché la destinazione non sarà disponibile.

## <a name="cost"></a>Costo
Non sono attualmente previsti addebiti aggiuntivi per la funzionalità di esportazione dei dati. I prezzi per l'esportazione dei dati verranno annunciati in futuro e un avviso fornito prima dell'avvio della fatturazione. Se si sceglie di continuare a usare l'esportazione dei dati dopo il periodo di preavviso, l'addebito sarà addebitato alla tariffa applicabile.

## <a name="export-destinations"></a>Esporta destinazioni

### <a name="storage-account"></a>Account di archiviazione
I dati vengono inviati agli account di archiviazione mentre raggiungono il monitoraggio di Azure e archiviati nei BLOB di Accodamento orario. La configurazione di esportazione dei dati consente di creare un contenitore per ogni tabella nell'account di archiviazione con il nome *am,* seguito dal nome della tabella. Ad esempio, la tabella *SecurityEvent* viene inviata a un contenitore denominato *am-SecurityEvent*.

Il percorso del BLOB dell'account di archiviazione è *WorkspaceResourceId =/subscriptions/Subscription-ID/ResourceGroups/ \<resource-group\> /providers/Microsoft.operationalinsights/Workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h = \<two-digit 24-hour clock hour\> /m = 00/PT1H.json*. Poiché i BLOB di Accodamento sono limitati a 50.000 scritture nell'archivio, il numero di BLOB esportati può estendersi se il numero di Append è elevato. Il modello di denominazione per i BLOB in tal caso verrebbe PT1H_ #. JSON, dove # è il numero di BLOB incrementali.

Il formato dei dati dell'account di archiviazione è di [righe JSON](../essentials/resource-logs-blob-format.md). Questo significa che ogni record è delimitato da una nuova riga, senza matrice di record esterni e senza virgole tra record JSON. 

[![Dati di esempio di archiviazione](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics esportazione dei dati può scrivere BLOB di Accodamento in account di archiviazione non modificabili quando i criteri di conservazione basati sul tempo hanno l'impostazione *allowProtectedAppendWrites* abilitata. In questo modo è possibile scrivere nuovi blocchi in un BLOB di Accodamento, mantenendo al tempo stesso la protezione e la conformità. Vedere [Consenti scritture di BLOB di Accodamento protette](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

> [!NOTE]
> Il supporto per il BLOB di Accodamento per Azure Data Lake archiviazione è ora disponibile in anteprima in tutte le aree di Azure. [Eseguire la registrazione all'anteprima pubblica limitata](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pURDk2NjMzUTVEVzU5UU1XUlRXSTlHSlkxQS4u) prima di creare una regola di esportazione per Azure Data Lake archiviazione. L'esportazione non funzionerà senza la registrazione.

### <a name="event-hub"></a>Hub eventi
I dati vengono inviati all'hub eventi in tempo quasi reale mentre raggiunge monitoraggio di Azure. Viene creato un hub eventi per ogni tipo di dati esportato con il nome *am,* seguito dal nome della tabella. Ad esempio, la tabella *SecurityEvent* viene inviata a un hub eventi denominato *am-SecurityEvent*. Se si vuole che i dati esportati raggiungano un hub eventi specifico o se si dispone di una tabella con un nome che supera il limite di 47 caratteri, è possibile specificare il nome dell'hub eventi ed esportare tutti i dati per le tabelle definite.

> [!IMPORTANT]
> Il [numero di hub eventi supportati per spazio dei nomi è 10](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). Se si esportano più di 10 tabelle, specificare il nome dell'hub eventi per esportare tutte le tabelle nell'hub eventi.

Considerazioni:
1. Lo SKU dell'hub eventi ' Basic ' supporta il [limite](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) di dimensioni degli eventi inferiore e alcuni log nell'area di lavoro possono essere superati ed eliminati. È consigliabile usare l'hub eventi "standard" o "dedicato" come destinazione di esportazione.
2. Il volume dei dati esportati aumenta spesso nel tempo e la scalabilità dell'hub eventi deve essere aumentata per gestire velocità di trasferimento maggiori ed evitare la limitazione degli scenari e la latenza dei dati. È consigliabile usare la funzionalità di aumento automatico degli hub eventi per aumentare automaticamente le prestazioni e aumentare il numero di unità di velocità effettiva e soddisfare le esigenze di utilizzo. Per informazioni dettagliate, vedere [ridimensionare automaticamente le unità di velocità effettiva di hub eventi di Azure](../../event-hubs/event-hubs-auto-inflate.md) .

## <a name="prerequisites"></a>Prerequisiti
Di seguito sono riportati i prerequisiti che devono essere completati prima di configurare Log Analytics esportazione dei dati.

- L'account di archiviazione e l'hub eventi devono essere già stati creati e devono trovarsi nella stessa area dell'area di lavoro Log Analytics. Se è necessario replicare i dati in altri account di archiviazione, è possibile usare una qualsiasi delle [Opzioni di ridondanza di archiviazione di Azure](../../storage/common/storage-redundancy.md).  
- L'account di archiviazione deve essere StorageV1 o archiviazione V2. Archiviazione classica non supportata  
- Se l'account di archiviazione è stato configurato per consentire l'accesso da reti selezionate, è necessario aggiungere un'eccezione nelle impostazioni dell'account di archiviazione per consentire a monitoraggio di Azure di scrivere nell'archiviazione.

## <a name="enable-data-export"></a>Abilitare l'esportazione dei dati
Per abilitare l'esportazione dei dati Log Analytics, è necessario eseguire i passaggi seguenti. Vedere le sezioni seguenti per altre informazioni su ognuna di esse.

- Registrare il provider di risorse.
- Consenti servizi Microsoft attendibili.
- Creare una o più regole di esportazione dei dati che definiscono le tabelle da esportare e la relativa destinazione.

### <a name="register-resource-provider"></a>Registrare il provider di risorse
Per abilitare l'esportazione dei dati Log Analytics, è necessario registrare il provider di risorse di Azure seguente per la sottoscrizione. 

- Microsoft.Insights

Questo provider di risorse probabilmente sarà già registrato per la maggior parte degli utenti di monitoraggio di Azure. Per verificare, passare a **sottoscrizioni** nella portale di Azure. Selezionare la sottoscrizione e quindi fare clic su **provider di risorse** nella sezione **Impostazioni** del menu. Individuare **Microsoft. Insights**. Se lo stato è **registrato**, è già registrato. In caso contrario, fare clic su **registra** per registrarlo.

È anche possibile usare uno dei metodi disponibili per registrare un provider di risorse, come descritto in [tipi e provider di risorse di Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Di seguito è riportato un comando di esempio con PowerShell:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Consenti servizi Microsoft attendibili
Se l'account di archiviazione è stato configurato per consentire l'accesso da reti selezionate, è necessario aggiungere un'eccezione per consentire a monitoraggio di Azure di scrivere nell'account. Da **firewall e reti virtuali** per l'account di archiviazione selezionare **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**.

[![Firewall e reti virtuali dell'account di archiviazione](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>Crea o Aggiorna regola di esportazione dei dati
Una regola di esportazione dei dati consente di definire le tabelle per le quali i dati vengono esportati e la destinazione. È possibile creare una singola regola per ogni destinazione.

La regola di esportazione deve includere le tabelle presenti nell'area di lavoro. Eseguire questa query per un elenco delle tabelle disponibili nell'area di lavoro.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando seguente per creare una regola di esportazione dei dati in un account di archiviazione usando l'interfaccia della riga di comando.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

Usare il comando seguente per creare una regola di esportazione dei dati in un hub eventi usando l'interfaccia della riga di comando. Viene creato un hub eventi separato per ogni tabella.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

Usare il comando seguente per creare una regola di esportazione dei dati in un hub eventi specifico usando l'interfaccia della riga di comando. Tutte le tabelle vengono esportate nel nome dell'hub eventi specificato. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

Usare la richiesta seguente per creare una regola di esportazione dei dati usando l'API REST. La richiesta deve usare bearer token autorizzazione e il tipo di contenuto application/json.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

Il corpo della richiesta specifica la destinazione Tables. Di seguito è riportato un corpo di esempio per la richiesta REST per un account di archiviazione.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Di seguito è riportato un corpo di esempio per la richiesta REST per un hub eventi.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Di seguito è riportato un corpo di esempio per la richiesta REST per un hub eventi in cui viene fornito il nome dell'hub eventi. In questo caso, tutti i dati esportati vengono inviati a questo hub eventi.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[Modello](#tab/json)

Usare il comando seguente per creare una regola di esportazione dei dati in un account di archiviazione usando il modello.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Usare il comando seguente per creare una regola di esportazione dei dati in un hub eventi usando il modello. Viene creato un hub eventi separato per ogni tabella.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Usare il comando seguente per creare una regola di esportazione dei dati in un hub eventi specifico usando il modello. Tutte le tabelle vengono esportate nel nome dell'hub eventi specificato.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>Visualizzazione della configurazione delle regole di esportazione dei dati

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando seguente per visualizzare la configurazione di una regola di esportazione dei dati tramite l'interfaccia della riga di comando.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Usare la richiesta seguente per visualizzare la configurazione di una regola di esportazione dei dati usando l'API REST. La richiesta deve usare bearer token autorizzazione.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Modello](#tab/json)

N/D

---

## <a name="disable-an-export-rule"></a>Disabilitare una regola di esportazione

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile disabilitare le regole di esportazione per consentire l'arresto dell'esportazione quando non è necessario conservare i dati per un determinato periodo di tempo, ad esempio durante l'esecuzione del test. Usare il comando seguente per disabilitare una regola di esportazione dei dati tramite l'interfaccia della riga di comando.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

È possibile disabilitare le regole di esportazione per consentire l'arresto dell'esportazione quando non è necessario conservare i dati per un determinato periodo di tempo, ad esempio durante l'esecuzione del test. Usare la richiesta seguente per disabilitare una regola di esportazione dei dati usando l'API REST. La richiesta deve usare bearer token autorizzazione.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[Modello](#tab/json)

È possibile disabilitare le regole di esportazione per consentire l'arresto dell'esportazione quando non è necessario conservare i dati per un determinato periodo di tempo, ad esempio durante l'esecuzione del test. Impostare ```"enable": false``` nel modello per disabilitare un'esportazione dei dati.

---

## <a name="delete-an-export-rule"></a>Eliminare una regola di esportazione

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando seguente per eliminare una regola di esportazione dei dati tramite l'interfaccia della riga di comando.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Usare la richiesta seguente per eliminare una regola di esportazione dei dati usando l'API REST. La richiesta deve usare bearer token autorizzazione.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[Modello](#tab/json)

N/D

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Visualizzare tutte le regole di esportazione dei dati in un'area di lavoro

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/D

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando seguente per visualizzare tutte le regole di esportazione dei dati in un'area di lavoro usando CLI.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

Usare la richiesta seguente per visualizzare tutte le regole di esportazione dei dati in un'area di lavoro usando l'API REST. La richiesta deve usare bearer token autorizzazione.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[Modello](#tab/json)

N/D

---

## <a name="unsupported-tables"></a>Tabelle non supportate
Se la regola di esportazione dei dati include una tabella non supportata, la configurazione avrà esito positivo, ma non verranno esportati dati per tale tabella. Se la tabella è supportata in un secondo momento, i relativi dati verranno esportati in quel momento.

Se la regola di esportazione dei dati include una tabella che non esiste, avrà esito negativo con l'errore "la tabella non <tableName> esiste nell'area di lavoro".


## <a name="supported-tables"></a>Tabelle supportate
Le tabelle supportate sono attualmente limitate a quelle specificate di seguito. Tutti i dati della tabella verranno esportati a meno che non vengano specificate limitazioni. Questo elenco verrà aggiornato con l'aggiunta del supporto per ulteriori tabelle.


| Tabella | Limitazioni |
|:---|:---|
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADServicePrincipalSignInLogs |  |
| ABSBotRequests |  |
| ACSBillingUsage |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFTriggerRun |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| Avviso |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | Supporto parziale: alcuni dati vengono inseriti tramite servizi interni non supportati per l'esportazione. Questa parte non è attualmente presente nell'esportazione. |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DnsEvents |  |
| : Inventario DNS |  |
| Dynamics365Activity |  |
| Evento | Supporto parziale: alcuni dati di questa tabella vengono inseriti tramite un account di archiviazione. Questa parte non è attualmente presente nell'esportazione. |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| Heartbeat |  |
| HuntingBookmark |  |
| InsightsMetrics | Supporto parziale: alcuni dati vengono inseriti tramite servizi interni non supportati per l'esportazione. Questa parte non è attualmente presente nell'esportazione. |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | Supporto parziale: alcuni dati da inserire tramite webhook da O365 a LA. Questa parte non è attualmente presente nell'esportazione. |
| Operazione | Supporto parziale: alcuni dati vengono inseriti tramite servizi interni non supportati per l'esportazione. Questa parte non è attualmente presente nell'esportazione. |
| Perf | Supporto parziale: attualmente sono supportati solo i dati delle prestazioni di Windows. Attualmente mancano i dati delle prestazioni di Linux nell'esportazione. |
| PowerBIDatasetsTenant |  |
| PowerBIDatasetsWorkspace |  |
| PowerBIDatasetsWorkspacePreview |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| SecurityEvent | Supporto parziale: alcuni dati di questa tabella vengono inseriti tramite un account di archiviazione. Questa parte non è attualmente presente nell'esportazione. |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| syslog | Supporto parziale: alcuni dati di questa tabella vengono inseriti tramite un account di archiviazione. Questa parte non è attualmente presente nell'esportazione. |
| ThreatIntelligenceIndicator |  |
| Aggiornamento | Supporto parziale: alcuni dati vengono inseriti tramite servizi interni non supportati per l'esportazione. Questa parte non è attualmente presente nell'esportazione. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| Utilizzo |  |
| Watchlist |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | Supporto parziale: alcuni dati vengono inseriti tramite servizi interni non supportati per l'esportazione. Questa parte non è attualmente presente nell'esportazione. |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una query sui dati esportati dal Esplora dati di Azure](../logs/azure-data-explorer-query-storage.md).
