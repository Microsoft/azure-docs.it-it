---
title: Gestire l'ambiente tramite i modelli di Azure Resource Manager - Azure Time Series Insights | Microsoft Docs
description: Informazioni su come gestire l'ambiente di Azure Time Series Insights a livello di codice tramite Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 493750e69b1fdc935b04d6dc705cfd046b6b086e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011660"
---
# <a name="create-azure-time-series-insights-gen-1-resources-using-azure-resource-manager-templates"></a>Creare risorse di Azure Time Series Insights generazione 1 usando modelli di Azure Resource Manager

> [!CAUTION]
> È un articolo di Gen1.

Questo articolo descrive come creare e distribuire Azure Time Series Insights risorse usando [modelli di Azure Resource Manager](../azure-resource-manager/index.yml), PowerShell e il provider di risorse di Azure Time Series Insights.

Azure Time Series Insights supporta le risorse seguenti:

   | Risorsa | Descrizione |
   | --- | --- |
   | Environment | Un ambiente Azure Time Series Insights è un raggruppamento logico di eventi che vengono letti dai broker di eventi, archiviati e resi disponibili per la query. Per altre informazioni, leggere [Pianificare un ambiente Azure Time Series Insights](time-series-insights-environment-planning.md) |
   | Origine evento | Un'origine evento è una connessione a un gestore eventi da cui Azure Time Series Insights legge e inserisce gli eventi nell'ambiente. Le origini eventi attualmente supportate sono hub IoT e hub eventi. |
   | Set di dati di riferimento | I set di dati di riferimento forniscono metadati sugli eventi nell'ambiente. I metadati nei set di dati di riferimento verranno aggiunto agli eventi durante l'ingresso. I set di dati di riferimento sono definiti come risorse dalle relative proprietà chiave evento. I metadati effettivi che costituiscono il set di dati di riferimento vengono caricati o modificati tramite le API del piano dati. |
   | Criterio di accesso | I criteri di accesso concedono le autorizzazioni per eseguire query sui dati, modificare i dati di riferimento nell'ambiente e condividere le prospettive e le query salvate associate all'ambiente. Per altre informazioni, vedere [concedere l'accesso ai dati a un ambiente Azure Time Series Insights usando portale di Azure](./concepts-access-policies.md) |

Un modello di Resource Manager è un file JSON che definisce l'infrastruttura e la configurazione delle risorse in un gruppo di risorse. I documenti seguenti descrivono i file modello in modo più dettagliato:

- [Distribuzione del modello di Azure Resource Manager](../azure-resource-manager/templates/overview.md)
- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Tipi di risorsa Microsoft.TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

Il modello di avvio rapido [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) viene pubblicato su GitHub. Questo modello crea un ambiente di Azure Time Series Insights, un'origine evento figlio configurato per utilizzare gli eventi da un hub eventi e i criteri di accesso che concedono l'accesso ai dati dell'ambiente. Se non è specificato un hub eventi esistente, ne verrà creato uno con la distribuzione.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Specificare i parametri e il modello di distribuzione

La procedura seguente descrive come usare PowerShell per distribuire un modello di Azure Resource Manager per creare un ambiente di Azure Time Series Insights, un'origine evento figlio configurato per utilizzare gli eventi da un hub eventi e i criteri di accesso che concedono l'accesso ai dati dell'ambiente. Se non è specificato un hub eventi esistente, ne verrà creato uno con la distribuzione.

1. Installare Azure PowerShell seguendo le istruzioni riportate in [Introduzione ai cmdlet di Azure PowerShell](/powershell/azure/get-started-azureps).

1. Clone o copiare il modello [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) da GitHub.

   - Creare un file di parametri

     Per creare un file di parametri, copiare il file [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json).

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   - Parametri obbligatori

     | Parametro | Descrizione |
     | --- | --- |
     | eventHubNamespaceName | Lo spazio dei nomi dello hub eventi di origine. |
     | eventHubName | Il nome dello hub eventi di origine. |
     | consumerGroupName | Nome del gruppo di consumer che il servizio Azure Time Series Insights utilizzerà per leggere i dati dall'hub eventi. **Nota:** Per evitare conflitti di risorse, questo gruppo di consumer deve essere dedicato al servizio Azure Time Series Insights e non condiviso con altri lettori. |
     | environmentName | Il nome dell'ambiente. Il nome non può includere i caratteri seguenti: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`, né caratteri di controllo. Sono consentiti tutti gli altri caratteri.|
     | eventSourceName | Specificare il nome della risorsa figlio dell'origine evento. Il nome non può includere i caratteri seguenti: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`, né caratteri di controllo. Sono consentiti tutti gli altri caratteri. |

    <div id="optional-parameters"></div>

   - Parametri facoltativi

     | Parametro | Descrizione |
     | --- | --- |
     | existingEventHubResourceId | ID di risorsa facoltativo di un hub eventi esistente che verrà connesso all'ambiente di Azure Time Series Insights tramite l'origine evento. **NOTA** l'utente che distribuisce il modello deve disporre delle autorizzazioni necessarie per eseguire l'operazione listkey nell'hub eventi di origine. Se non viene superato alcun valore, verrà creato un nuovo hub eventi dal modello. |
     | environmentDisplayName | Un nome descrittivo facoltativo da visualizzare nelle interfacce degli strumenti o utente al posto del nome dell'ambiente. |
     | environmentSkuName | Il nome dello SKU. Per ulteriori informazioni, leggere la [pagina relativa ai prezzi Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | La capacità unità dello Sku. Per ulteriori informazioni, leggere la [pagina relativa ai prezzi Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | L'intervallo di tempo minimo in cui gli eventi dell'ambiente saranno disponibili per la query. Il valore deve essere specificato nel formato ISO 8601, ad esempio `P30D` per i criteri di conservazione di 30 giorni. |
     | eventSourceDisplayName | Un nome descrittivo facoltativo da visualizzare nelle interfacce degli strumenti o utente al posto del nome dell'origine evento. |
     | eventSourceTimestampPropertyName | La proprietà di evento che verrà usata come timestamp dell'origine evento. Se non è specificato un valore per timestampPropertyName o se viene specificato null o una stringa vuota, verrà usata l'ora di creazione dell'evento. |
     | eventSourceKeyName | Nome della chiave di accesso condivisa che il servizio Azure Time Series Insights userà per connettersi all'hub eventi. |
     | accessPolicyReaderObjectIds | Un elenco di ID oggetto degli utenti o delle applicazioni in Azure AD che devono disporre dell'accesso in lettura all'ambiente. L'objectId dell'entità servizio può essere ottenuto chiamando il cmdlet **Get-AzADUser** o **Get-AzADServicePrincipal**. La creazione di criteri di accesso per i gruppi di Azure AD non è ancora supportata. |
     | accessPolicyContributorObjectIds | Un elenco di ID oggetto degli utenti o delle applicazioni in Azure AD che devono disporre dell'accesso per collaboratori all'ambiente. L'objectId dell'entità servizio può essere ottenuto chiamando il cmdlet **Get-AzADUser** o **Get-AzADServicePrincipal**. La creazione di criteri di accesso per i gruppi di Azure AD non è ancora supportata. |

   - Ad esempio, per creare un ambiente verrebbe usato il file dei parametri seguente e un'origine evento che legge gli eventi da un hub eventi esistente. Crea inoltre due criteri di accesso che concedono l'accesso per i collaboratori all'ambiente.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }
         }
     }
     ```

   - Per altre informazioni, leggere l'articolo [Parametri](../azure-resource-manager/templates/parameter-files.md).

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Distribuire il modello di avvio rapido in locale tramite PowerShell

> [!IMPORTANT]
> Le operazioni da riga di comando visualizzate di seguito descrivono il [modulo di AZ PowerShell](/powershell/azure/).

1. In PowerShell accedere all'account Azure.

    - Al prompt di PowerShell, eseguire il comando seguente:

      ```powershell
      Connect-AzAccount
      ```

    - Il sistema chiede di accedere all'account Azure. Dopo l'accesso, eseguire il comando seguente per visualizzare le sottoscrizioni disponibili:

      ```powershell
      Get-AzSubscription
      ```

    - Questo comando restituisce un elenco delle sottoscrizioni di Azure disponibili. Scegliere una sottoscrizione per la sessione corrente eseguendo il comando seguente. Sostituire `<YourSubscriptionId>` con il GUID della sottoscrizione di Azure che si vuole usare:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Se non esiste, creare un nuovo gruppo di risorse.

   - Se non è disponibile un gruppo di risorse esistente, creare un nuovo gruppo di risorse con il comando **New-AzResourceGroup**. Specificare il nome del gruppo di risorse e la posizione che si vuole usare, Ad esempio:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   - Se il nuovo gruppo di risorse è stato creato correttamente, viene visualizzato il relativo riepilogo.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Testare la distribuzione.

   - Convalidare la distribuzione eseguendo il cmdlet `Test-AzResourceGroupDeployment`. Durante il test della distribuzione, specificare esattamente gli stessi parametri di quando si esegue la distribuzione.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Creare la distribuzione

    - Per creare la nuova distribuzione, eseguire il cmdlet `New-AzResourceGroupDeployment` e specificare i parametri necessari quando viene richiesto. I parametri includono il nome della distribuzione, il nome del gruppo di risorse e il percorso o l'URL del file di modello. Se il parametro **Mode** non è specificato, viene usato il valore predefinito **Incremental**. Per altre informazioni, leggere [Distribuzioni incrementali e complete](../azure-resource-manager/templates/deployment-modes.md).

    - Il comando seguente richiede cinque parametri obbligatori nella finestra di PowerShell:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    - Per specificare invece un file di parametri, usare il comando seguente:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    - È anche possibile usare i parametri inline quando si esegue il cmdlet di distribuzione. Il comando è il seguente:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    - Per eseguire una distribuzione [completa](../azure-resource-manager/templates/deployment-modes.md), impostare il parametro **Mode** su **Complete**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Verificare la distribuzione

    - Se le risorse vengono distribuite correttamente, nella finestra di PowerShell viene visualizzato il riepilogo della distribuzione:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Distribuire il modello di avvio rapido tramite il portale di Azure

   - La home page del modello di avvio rapido GitHub include inoltre il **Distribuisci in Azure**. Facendo clic su di esso si apre la pagina Distribuzione personalizzata nel portale di Azure. In questa pagina, è possibile immettere o selezionare i valori per ognuno dei parametri dalle tabelle dei [parametri obbligatori](#required-parameters) o dei [parametri facoltativi](#optional-parameters). Dopo aver immesso le impostazioni, facendo clic sul pulsante **Acquisto** si avvierà la distribuzione del modello.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png" alt="The Deploy to Azure button."/>
    </a>

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla gestione a livello di codice delle risorse Azure Time Series Insights usando le API REST, vedere [gestione Azure Time Series Insights](/rest/api/time-series-insights-management/).