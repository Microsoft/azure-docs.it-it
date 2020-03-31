---
title: Funzioni modello - risorse
description: Informazioni sulle funzioni da usare in un modello di Azure Resource Manager per recuperare i valori relativi alle risorse.
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e9e1d700282652304f0bede5e697ba8625f5a5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156294"
---
# <a name="resource-functions-for-arm-templates"></a>Funzioni delle risorse per i modelli ARM

Resource Manager offre le funzioni seguenti per ottenere i valori delle risorse nel modello di Azure Resource Manager (ARM):

* [extensionResourceId (idEstensione)extensionResourceId](#extensionresourceid)
* [l'elenco di indirizzi](#list)
* [Provider](#providers)
* [Riferimento](#reference)
* [ResourceGroup (Gruppo di risorse)](#resourcegroup)
* [Resourceid](#resourceid)
* [Sottoscrizione](#subscription)
* [subscriptionResourceId (informazioni in stato inabbonamentoResourceId](#subscriptionresourceid)
* [tenantResourceId (informazioni in base al nome in com](#tenantresourceid)

Per ottenere valori dai parametri, dalle variabili o dalla distribuzione corrente, vedere [Funzioni dei valori della distribuzione](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId (idEstensione)extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Restituisce l'ID risorsa per una risorsa di [estensione,](../management/extension-resource-types.md)ovvero un tipo di risorsa applicato a un'altra risorsa da aggiungere alle relative funzionalità.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| resourceId |Sì |string |ID risorsa per la risorsa a cui viene applicata la risorsa di estensione. |
| resourceType |Sì |string |Tipo di risorsa, incluso lo spazio dei nomi del provider di risorse. |
| resourceName1 |Sì |string |Nome della risorsa. |
| resourceName2 |No |string |Segmento successivo del nome della risorsa, se necessario. |

Continuare ad aggiungere nomi di risorse come parametri quando il tipo di risorsa include più segmenti.

### <a name="return-value"></a>Valore restituito

Il formato di base dell'ID risorsa restituito da questa funzione è:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Il segmento di ambito varia in base alla risorsa da estendere.

Quando la risorsa di estensione viene applicata a una **risorsa**, l'ID della risorsa viene restituito nel formato seguente:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando la risorsa di estensione viene applicata a un **gruppo di risorse,** il formato è:When the extension resource is applied to a resource group , the format is:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando la risorsa di estensione viene applicata a una **sottoscrizione,** il formato è:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando la risorsa di estensione viene applicata a un gruppo di **gestione**, il formato è:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>Esempio extensionResourceId

Nell'esempio seguente viene restituito l'ID risorsa per un blocco del gruppo di risorse.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

La sintassi per questa funzione varia in base al nome delle operazioni list. Ogni implementazione restituisce i valori per il tipo di risorsa che supporta un'operazione list. Il nome dell'operazione deve iniziare con `list`. Tra gli utilizzi comuni vi sono `listKeys` e `listSecrets`.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| resourceName o resourceIdentifier |Sì |string |Identificatore univoco della risorsa. |
| apiVersion |Sì |string |Versione dell'API dello stato di runtime della risorsa. In genere il formato è **aaaa-mm-gg**. |
| functionValues |No |object | Oggetto che contiene valori per la funzione. Specificare solo questo oggetto per le funzioni che supportano la ricezione di un oggetto con valori di parametro, ad esempio **listAccountSas** per un account di archiviazione. Questo articolo illustra un esempio di passaggio dei valori di funzione. |

### <a name="valid-uses"></a>Usi validi

Le funzioni di elenco possono essere utilizzate solo nelle proprietà di una definizione di risorsa e nella sezione output di un modello o di una distribuzione. Se utilizzata con l'iterazione delle `input` [proprietà](copy-properties.md), è possibile utilizzare le funzioni di elenco per perché l'espressione viene assegnata alla proprietà della risorsa. Non è possibile utilizzarli perché `count` il conteggio deve essere determinato prima che la funzione di elenco venga risolta.

### <a name="implementations"></a>Implementazioni

Gli utilizzi possibili della funzione list* sono visualizzati nella tabella seguente.

| Tipo di risorsa | Nome della funzione |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | ListKeys (Tasti di elenchi) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys (tasti elenco)](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEventi](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registri/esecuzione | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registri/attività | [listDettagli](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSincronizzazioni](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/account/shareSottoscrizioni | [ListSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/account/shareSottoscrizioni | [listSynchronizationDetails (Informazioni in cui si è intra](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/account/shareSottoscrizioni | [listSincronizzazioni](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys (tasti elenco)](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys (tasti elenco)](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys (tasti elenco)](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys (tasti elenco)](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domini | [listKeys](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys (tasti elenco)](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys (tasti elenco)](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys (tasti elenco)](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Cluster/Database | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/utenti | [ListAmbienti](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/utenti | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTracce](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTracce](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys (tasti elenco)](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [ListNodes (Nosdi elenco)](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurazioni |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys (tasti elenco)](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys (tasti elenco)](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [listDistribuzioni](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys (tasti elenco)](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys (tasti elenco) |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys (tasti elenco)](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys (tasti elenco)](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys (tasti elenco)](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys (tasti elenco)](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys (tasti elenco)](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys (tasti elenco)](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys (tasti elenco)](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys (tasti elenco)](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys (chiave di elencoconnessioni) |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/siti/backup | [Elenco](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [Elenco](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys (tasti elenco)](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys (tasti elenco)](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slot/backups | [Elenco](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slot/config | [Elenco](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Per determinare quali tipi di risorse dispongono di un'operazione list, usare le opzioni seguenti:

* Visualizzare le [operazioni dell'API REST](/rest/api/) di un provider di risorse e individuare le operazioni list. Gli account di archiviazione, ad esempio, dispongono dell'[operazione listKeys](/rest/api/storagerp/storageaccounts).
* Usare il cmdlet di PowerShell [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation). L'esempio seguente ottiene tutte le operazioni list degli account di archiviazione:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Usare il comando seguente dell'interfaccia della riga di comando di Azure per filtrare solo le operazioni list:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Valore restituito

L'oggetto restituito varia a seconda della funzione list usata. La funzione listKeys per un account di archiviazione, ad esempio, restituisce il formato seguente:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Altre funzioni list possono avere formati di restituzione diversi. Per visualizzare il formato di una funzione, includerlo nella sezione outputs, come mostrato nel modello di esempio.

### <a name="remarks"></a>Osservazioni

Specificare la risorsa usando il nome della risorsa stessa o la [funzione resourceId](#resourceid). Quando si usa una funzione list nello stesso modello che distribuisce la risorsa di riferimento, usare il nome della risorsa.

Se si usa una funzione **di elenco** in una risorsa distribuita in modo condizionale, la funzione viene valutata anche se la risorsa non è distribuita. Se la funzione **di elenco** fa riferimento a una risorsa che non esiste, viene visualizzato un errore. Utilizzare la funzione **if** per assicurarsi che la funzione venga valutata solo quando la risorsa viene distribuita. Vedere la [funzione if](template-functions-logical.md#if) per un modello di esempio che usa if ed elenco con una risorsa distribuita in modo condizionale.

### <a name="list-example"></a>Esempio di elenco

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) seguente mostra come restituire le chiavi primaria e secondaria da un account di archiviazione nella sezione outputs. Restituisce anche un token SAS per l'account di archiviazione.

Per ottenere il token di firma di accesso condiviso, passare un oggetto per il tempo di scadenza. L'ora di scadenza deve essere futura. Lo scopo di questo esempio è illustrare come usare le funzioni list. In genere, è necessario usare il token SAS in un valore di risorsa invece di restituirlo come valore di output. I valori di output vengono archiviati nella cronologia di distribuzione e non sono protetti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>provider

```json
providers(providerNamespace, [resourceType])
```

Restituisce informazioni su un provider di risorse e i relativi tipi di risorse supportati. Se non si specifica un tipo di risorsa, la funzione restituisce tutti i tipi supportati per il provider di risorse.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| providerNamespace |Sì |string |Spazio dei nomi del provider |
| resourceType |No |string |Il tipo di risorsa all'interno dello spazio dei nomi specificato. |

### <a name="return-value"></a>Valore restituito

Ogni tipo supportato viene restituito nel formato seguente:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

L'ordine della matrice dei valori restituiti non è garantito.

### <a name="providers-example"></a>Esempio di provider

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) seguente mostra come usare la funzione provider:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Per il provider di risorse **Microsoft** e il tipo di risorsa **siti**, l'esempio precedente restituisce un oggetto nel formato seguente:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Restituisce un oggetto che rappresenta lo stato di runtime di una risorsa.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| resourceName o resourceIdentifier |Sì |string |Nome o identificatore univoco di una risorsa. Quando si fa riferimento a una risorsa nel modello corrente, specificare solo il nome della risorsa come parametro. Quando si fa riferimento a una risorsa distribuita in precedenza o quando il nome della risorsa è ambiguo, specificare l'ID della risorsa. |
| apiVersion |No |string |Versione dell'API della risorsa specificata. Includere questo parametro quando non viene effettuato il provisioning della risorsa nello stesso modello. In genere il formato è **aaaa-mm-gg**. Per le versioni API valide per la risorsa, vedere [riferimento al modello](/azure/templates/). |
| 'Full' |No |string |Valore che specifica se restituire l'oggetto risorsa completo. Se non si specifica `'Full'`, viene restituito solo l'oggetto proprietà della risorsa. L'oggetto completo include valori quali l'ID e la posizione della risorsa. |

### <a name="return-value"></a>Valore restituito

Ogni tipo di risorsa restituisce proprietà diverse per la funzione di riferimento. La funzione non restituisce un singolo formato predefinito. Il valore restituito, poi, è diverso a seconda del fatto che sia stato richiesto l'oggetto completo o meno. Per visualizzare le proprietà per un tipo di risorsa, restituire l'oggetto nella sezione output, come illustrato nell'esempio.

### <a name="remarks"></a>Osservazioni

La funzione reference recupera lo stato di runtime di una risorsa distribuita in precedenza o di una risorsa distribuita nel modello corrente. Questo articolo contiene esempi relativi a entrambi gli scenari.

In genere, si usa la funzione di **riferimento** per restituire un valore specifico da un oggetto, ad esempio l'URI dell'endpoint BLOB o il nome di dominio completo.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Usare `'Full'` se sono necessari valori della risorsa che non fanno parte dello schema delle proprietà. Per impostare criteri di accesso all'insieme di credenziali delle chiavi, ad esempio, ottenere le proprietà di identità per una macchina virtuale.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Usi validi

La funzione reference può essere usata solo nelle proprietà di una definizione di risorsa e nella sezione outputs di un modello o una distribuzione. Se utilizzata con l'iterazione delle `input` [proprietà](copy-properties.md), è possibile utilizzare la funzione di riferimento perché l'espressione viene assegnata alla proprietà della risorsa. Non è possibile utilizzarlo perché `count` il conteggio deve essere determinato prima che la funzione di riferimento venga risolta.

Non è possibile usare la funzione di riferimento negli output di un [modello annidato](linked-templates.md#nested-template) per restituire una risorsa distribuita nel modello annidato. Utilizzare invece un [modello collegato.](linked-templates.md#linked-template)

Se si usa la funzione di **riferimento** in una risorsa distribuita in modo condizionale, la funzione viene valutata anche se la risorsa non è distribuita.  Se la funzione di **riferimento** fa riferimento a una risorsa che non esiste, viene visualizzato un errore. Utilizzare la funzione **if** per assicurarsi che la funzione venga valutata solo quando la risorsa viene distribuita. Vedere la [funzione if](template-functions-logical.md#if) per un modello di esempio che usa if e fare riferimento con una risorsa distribuita in modo condizionale.

### <a name="implicit-dependency"></a>Dipendenza implicita

Usando la funzione di riferimento, si dichiara implicitamente che una risorsa dipende da un'altra se il provisioning della risorsa cui si fa riferimento viene effettuato nello stesso modello e si fa riferimento alla risorsa tramite il nome, non tramite l'ID risorsa. Non è necessario usare anche la proprietà dependsOn. La funzione non viene valutata fino a quando la risorsa cui si fa riferimento ha completato la distribuzione.

### <a name="resource-name-or-identifier"></a>Nome o identificatore della risorsa

Quando si fa riferimento a una risorsa distribuita nello stesso modello, specificare il nome della risorsa.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Quando si fa riferimento a una risorsa non distribuita nello stesso modello, specificare l'ID risorsa.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Per evitare ambiguità sulla risorsa a cui si fa riferimento, è possibile fornire un identificatore di risorsa completo.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

Quando si crea un riferimento completo a una risorsa, l'ordine di combinazione dei segmenti dal tipo e dal nome non è semplicemente una concatenazione dei due elementi. Dopo lo spazio dei nomi, usare invece una sequenza di coppie *tipo/nome* dal meno specifico al più specifico:

**"spazio dei nomi del provider-risorse"/ / . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .**

Ad esempio:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` è corretto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` non è corretto

Per semplificare la creazione di `resourceId()` qualsiasi ID risorsa, utilizzare `concat()` le funzioni descritte in questo documento anziché la funzione.

### <a name="get-managed-identity"></a>Ottenere l'identità gestitaGet managed identity

[Le identità gestite per le risorse](../../active-directory/managed-identities-azure-resources/overview.md) di Azure sono [tipi di risorse di estensione](../management/extension-resource-types.md) creati in modo implicito per alcune risorse. Poiché l'identità gestita non è definita in modo esplicito nel modello, è necessario fare riferimento alla risorsa a cui viene applicata l'identità. Utilizzare `Full` per ottenere tutte le proprietà, inclusa l'identità creata in modo implicito.

Ad esempio, per ottenere l'ID tenant per un'identità gestita applicata a un set di scalabilità di macchine virtuali, usare:For example, to get the tenant ID for a managed identity that is applied to a virtual machine scale set, use:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Esempio di riferimento

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) seguente distribuisce una risorsa e fa riferimento a tale risorsa.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": {
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

L'esempio precedente restituisce i due oggetti. L'oggetto proprietà è nel formato seguente:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

L'oggetto completo è nel formato seguente:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) seguente fa riferimento a un account di archiviazione non distribuito in questo modello. L'account di archiviazione esiste già nella stessa sottoscrizione.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Restituisce un oggetto che rappresenta il gruppo di risorse corrente.

### <a name="return-value"></a>Valore restituito

L'oggetto restituito è nel formato seguente:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

La proprietà **managedBy** viene restituita solo per i gruppi di risorse che contengono risorse gestite da un altro servizio. Per le applicazioni gestite, Databrick s e AKS, il valore della proprietà è l'ID risorsa della risorsa di gestione.

### <a name="remarks"></a>Osservazioni

La funzione `resourceGroup()` non può essere usata in un modello che viene [distribuito a livello di sottoscrizione](deploy-to-subscription.md). Può essere usata solo nei modelli distribuiti in un gruppo di risorse. È possibile `resourceGroup()` usare la funzione in un [modello collegato o annidato (con ambito interno)](linked-templates.md) destinato a un gruppo di risorse, anche quando il modello padre viene distribuito nella sottoscrizione. In questo scenario, il modello collegato o annidato viene distribuito a livello di gruppo di risorse. Per altre informazioni sull'assegnazione di un gruppo di risorse in una distribuzione a livello di sottoscrizione, vedere Distribuire risorse di [Azure in più sottoscrizioni o gruppi di risorse.](cross-resource-group-deployment.md)

Un utilizzo comune della funzione resourceGroup consiste nel creare risorse nello stesso percorso del gruppo di risorse. Nell'esempio seguente viene usata la posizione del gruppo di risorse per un valore di parametro predefinito.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

È anche possibile usare la funzione resourceGroup per applicare tag dal gruppo di risorse a una risorsa. Per altre informazioni, vedere [Applicare tag dal gruppo di risorse.](../management/tag-resources.md#apply-tags-from-resource-group)

Quando si usano modelli annidati per la distribuzione in più gruppi di risorse, è possibile specificare l'ambito per la valutazione della funzione resourceGroup.When using nested templates to deploy to multiple resource groups, you can specify the scope for evaluating the resourceGroup function. Per altre informazioni, vedere [Distribuire le risorse di Azure in più gruppi di sottoscrizioni e risorse](cross-resource-group-deployment.md).

### <a name="resource-group-example"></a>Esempio di gruppo di risorse

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) seguente restituisce le proprietà del gruppo di risorse.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

L'esempio precedente restituisce un oggetto nel formato seguente:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Restituisce l'identificatore univoco di una risorsa. Questa funzione viene usata quando il nome della risorsa è ambiguo o non è stato sottoposto a provisioning all'interno dello stesso modello. Il formato dell'identificatore restituito varia a seconda che la distribuzione avvenga nell'ambito di un gruppo di risorse, una sottoscrizione, un gruppo di gestione o un tenant.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |Stringa (in formato GUID) |Il valore predefinito è la sottoscrizione corrente. Specificare questo valore quando si vuole recuperare una risorsa in un'altra sottoscrizione. Fornire questo valore solo quando si esegue la distribuzione nell'ambito di un gruppo di risorse o di una sottoscrizione. |
| resourceGroupName |No |string |Il valore predefinito è il gruppo di risorse corrente. Specificare questo valore quando si vuole recuperare una risorsa in un altro gruppo di risorse. Fornire questo valore solo quando si esegue la distribuzione nell'ambito di un gruppo di risorse. |
| resourceType |Sì |string |Tipo di risorsa, incluso lo spazio dei nomi del provider di risorse. |
| resourceName1 |Sì |string |Nome della risorsa. |
| resourceName2 |No |string |Segmento successivo del nome della risorsa, se necessario. |

Continuare ad aggiungere nomi di risorse come parametri quando il tipo di risorsa include più segmenti.

### <a name="return-value"></a>Valore restituito

Quando il modello viene distribuito nell'ambito di un gruppo di risorse, l'ID risorsa viene restituito nel formato seguente:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Se utilizzato in una distribuzione a [livello di sottoscrizione,](deploy-to-subscription.md)l'ID risorsa viene restituito nel formato seguente:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Se utilizzato in una distribuzione a livello di gruppo di gestione o a livello di tenant, l'ID risorsa viene restituito nel formato seguente:When used in a [management group-level deployment](deploy-to-management-group.md) or tenant-level deployment, the resource ID is returned in the following format:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Per ottenere l'ID in altri formati, vedere:

* [extensionResourceId (idEstensione)extensionResourceId](#extensionresourceid)
* [subscriptionResourceId (informazioni in stato inabbonamentoResourceId](#subscriptionresourceid)
* [tenantResourceId (informazioni in base al nome in com](#tenantresourceid)

### <a name="remarks"></a>Osservazioni

Il numero di parametri forniti varia a seconda che la risorsa sia una risorsa padre o figlio e che la risorsa si trovi nella stessa sottoscrizione o nello stesso gruppo di risorse.

Per ottenere l'ID risorsa per una risorsa padre nella stessa sottoscrizione e nello stesso gruppo di risorse, specificare il tipo e il nome della risorsa.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Per ottenere l'ID risorsa per una risorsa figlio, prestare attenzione al numero di segmenti nel tipo di risorsa. Specificare un nome di risorsa per ogni segmento del tipo di risorsa. Il nome del segmento corrisponde alla risorsa esistente per quella parte della gerarchia.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Per ottenere l'ID risorsa per una risorsa nella stessa sottoscrizione ma gruppo di risorse diverso, specificare il nome del gruppo di risorse.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Per ottenere l'ID risorsa per una risorsa in una sottoscrizione e un gruppo di risorse diversi, specificare l'ID sottoscrizione e il nome del gruppo di risorse.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Spesso è necessario usare questa funzione quando si usa un account di archiviazione o una rete virtuale in un gruppo di risorse alternative. L'esempio seguente mostra come usare facilmente una risorsa di un gruppo di risorse esterno:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Esempio di ID risorsa

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) seguente restituisce l'ID della risorsa per un account di archiviazione nel gruppo di risorse:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

L'output dell'esempio precedente con i valori predefiniti è il seguente:

| Nome | Type | valore |
| ---- | ---- | ----- |
| sameRGOutput | string | /subscriptions/{id-sott-corrente}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | string | /subscriptions/{id-sott-corrente}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | string | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | string | /subscriptions/{id-sott-corrente}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>sottoscrizione

```json
subscription()
```

Restituisce i dettagli sulla sottoscrizione per la distribuzione corrente.

### <a name="return-value"></a>Valore restituito

La funzione restituisce il formato seguente:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Osservazioni

Quando si usano modelli annidati per la distribuzione in più sottoscrizioni, è possibile specificare l'ambito per la valutazione della funzione di sottoscrizione. Per altre informazioni, vedere [Distribuire le risorse di Azure in più gruppi di sottoscrizioni e risorse](cross-resource-group-deployment.md).

### <a name="subscription-example"></a>Esempio di sottoscrizione

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) seguente mostra la funzione subscription chiamata nella sezione outputs.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>subscriptionResourceId (informazioni in stato inabbonamentoResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Restituisce l'identificatore univoco per una risorsa distribuita a livello di sottoscrizione.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |string (in formato GUID) |Il valore predefinito è la sottoscrizione corrente. Specificare questo valore quando si vuole recuperare una risorsa in un'altra sottoscrizione. |
| resourceType |Sì |string |Tipo di risorsa, incluso lo spazio dei nomi del provider di risorse. |
| resourceName1 |Sì |string |Nome della risorsa. |
| resourceName2 |No |string |Segmento successivo del nome della risorsa, se necessario. |

Continuare ad aggiungere nomi di risorse come parametri quando il tipo di risorsa include più segmenti.

### <a name="return-value"></a>Valore restituito

L'identificatore viene restituito nel formato seguente:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Osservazioni

Utilizzare questa funzione per ottenere l'ID risorsa per le risorse [distribuite nella sottoscrizione](deploy-to-subscription.md) anziché in un gruppo di risorse. L'ID restituito è diverso dal valore restituito dalla funzione [resourceId](#resourceid) non includendo un valore del gruppo di risorse.

### <a name="subscriptionresourceid-example"></a>Esempio subscriptionResourceID

Il modello seguente assegna un ruolo predefinito. È possibile distribuirlo in un gruppo di risorse o in una sottoscrizione. Usa la funzione subscriptionResourceId per ottenere l'ID risorsa per i ruoli predefiniti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId (informazioni in base al nome in com

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Restituisce l'identificatore univoco per una risorsa distribuita a livello di tenant.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| resourceType |Sì |string |Tipo di risorsa, incluso lo spazio dei nomi del provider di risorse. |
| resourceName1 |Sì |string |Nome della risorsa. |
| resourceName2 |No |string |Segmento successivo del nome della risorsa, se necessario. |

Continuare ad aggiungere nomi di risorse come parametri quando il tipo di risorsa include più segmenti.

### <a name="return-value"></a>Valore restituito

L'identificatore viene restituito nel formato seguente:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Osservazioni

Utilizzare questa funzione per ottenere l'ID risorsa per una risorsa distribuita nel tenant. L'ID restituito è diverso dai valori restituiti da altre funzioni ID risorsa non includendo i valori del gruppo di risorse o della sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione delle sezioni in un modello di Azure Resource Manager, vedere Creazione di modelli di [Azure Resource Manager.](template-syntax.md)
* Per unire più modelli, vedere [Utilizzo di modelli collegati con Azure Resource Manager.](linked-templates.md)
* Per scorrere un numero specificato di volte durante la creazione di un tipo di risorsa, vedere [Creare più istanze di risorse in Azure Resource Manager.To](copy-resources.md)iterate a specified number of times when creating a type of resource, see Create multiple instances of resources in Azure Resource Manager.
* Per informazioni su come distribuire il modello che è stato creato, vedere [Distribuire un'applicazione con un modello di Azure Resource Manager](deploy-powershell.md).

