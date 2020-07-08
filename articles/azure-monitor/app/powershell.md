---
title: Automatizzare Azure Application Insights con PowerShell | Microsoft Docs
description: Automatizzare la creazione e la gestione di risorse, avvisi e test di disponibilità in PowerShell usando un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: c4e7c4fe14d829338e98a4b7e73726b1e605707c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485414"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Gestire risorse Application Insights con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

L'articolo descrive come automatizzare la creazione e l'aggiornamento di risorse di [Application Insights](../../azure-monitor/app/app-insights-overview.md) usando Azure Resource Manager. Questo procedimento potrebbe, ad esempio, essere utilizzato come parte di un processo di compilazione. Insieme alla risorsa di base di Application Insights, è possibile creare [test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md), configurare [avvisi](../../azure-monitor/platform/alerts-log.md), impostare lo [schema dei prezzi](pricing.md) e creare altre risorse di Azure.

La chiave per la creazione di queste risorse è rappresentata dai modelli JSON per [Gestione risorse di Azure](../../azure-resource-manager/management/manage-resources-powershell.md). La procedura di base consiste in: scaricare le definizioni JSON delle risorse esistenti; impostare i parametri per determinati valori, come ad esempio nomi; ed eseguire il modello ogni volta che si desidera creare una nuova risorsa. È possibile raggruppare diverse risorse per crearle tutte in un’unica volta - ad esempio, un monitoraggio app con test di disponibilità, avvisi e risorsa di archiviazione per l'esportazione continua. Esistono alcune sottigliezze di alcuni parametri, che verranno illustrate di seguito.

## <a name="one-time-setup"></a>Installazione singola
Se non si è mai usato PowerShell con la sottoscrizione di Azure:

Installare il modulo Azure PowerShell nel computer in cui verranno eseguiti gli script:

1. Installare [Installazione guidata piattaforma Web Microsoft (v5 o versione successiva)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Usarla per installare Microsoft Azure PowerShell.

Oltre a usare i modelli di Gestione risorse, è disponibile un set completo di [cmdlet di PowerShell per Application Insights](https://docs.microsoft.com/powershell/module/az.applicationinsights), che semplificano la configurazione a livello di codice delle risorse di Application Insights. Le funzionalità abilitate dai cmdlet includono:

* Creare ed eliminare risorse di Application Insights
* Ottenere gli elenchi di risorse di Application Insights e le relative proprietà
* Creare e gestire l'esportazione continua
* Creare e gestire le chiavi delle applicazioni
* Impostare il limite giornaliero
* Impostare il piano tariffario

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Creazione di risorse Application Insights con un cmdlet di PowerShell

Di seguito viene illustrato come creare una nuova risorsa di Application Insights nel data center degli Stati Uniti orientali di Azure usando il cmdlet [New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights):

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Creare risorse di Application Insights tramite il modello di Resource Manager

Di seguito viene illustrato come creare una nuova risorsa di Application Insights usando un modello di Gestione risorse.

### <a name="create-the-azure-resource-manager-template"></a>Creare il modello di Azure Resource Manager

Creare un nuovo file con estensione .json - definirlo `template1.json` in questo esempio. Copiare questo contenuto al suo interno:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Usare il modello di Resource Manager per creare una nuova risorsa di Application Insights

1. In PowerShell accedere ad Azure usando `$Connect-AzAccount`
2. Impostare il contesto su una sottoscrizione con `Set-AzContext "<subscription ID>"`
2. Eseguire una nuova distribuzione per creare una nuova risorsa di Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` è il gruppo in cui si vogliono creare le nuove risorse.
   * `-TemplateFile` deve precedere i parametri personalizzati.
   * `-appName` è il nome della risorsa da creare.

È possibile aggiungere altri parametri le cui descrizioni sono disponibili nella sezione del modello dedicata ai parametri.

## <a name="get-the-instrumentation-key"></a>Ottenere la chiave di strumentazione

Dopo la creazione di una risorsa applicazione, è necessaria la chiave di strumentazione: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Per visualizzare un elenco di molte altre proprietà della risorsa di Application Insights, usare:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Ulteriori proprietà sono disponibili tramite i cmdlet:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Per i parametri di questi cmdlet, fare riferimento alla [documentazione dettagliata](https://docs.microsoft.com/powershell/module/az.applicationinsights).  

## <a name="set-the-data-retention"></a>Impostare la conservazione dei dati

Di seguito sono riportati tre metodi per impostare a livello di codice la conservazione dei dati in una risorsa di Application Insights.

### <a name="setting-data-retention-using-a-powershell-commands"></a>Impostare la conservazione dei dati usando i comandi di PowerShell

Ecco un semplice set di comandi di PowerShell per impostare la conservazione dei dati per la risorsa di Application Insights:

```PS
$Resource = Get-AzResource -ResourceType Microsoft.Insights/components -ResourceGroupName MyResourceGroupName -ResourceName MyResourceName
$Resource.Properties.RetentionInDays = 365
$Resource | Set-AzResource -Force
```

### <a name="setting-data-retention-using-rest"></a>Impostare la conservazione dei dati tramite REST

Per ottenere la conservazione dei dati corrente per la risorsa di Application Insights, è possibile usare lo strumento OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Per altre informazioni su ARMClient, vedere gli articoli di [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Di seguito è riportato un esempio di uso di `ARMClient`per ottenere la conservazione corrente:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Per impostare la conservazione, il comando è un PUT simile:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Per impostare la conservazione dei dati su 365 giorni usando il modello precedente, eseguire:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

### <a name="setting-data-retention-using-a-powershell-script"></a>Impostare la conservazione dei dati usando lo script di PowerShell

Lo script seguente può essere usato anche per modificare la conservazione. Copiare questo script per salvarlo come `Set-ApplicationInsightsRetention.ps1`.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Questo script può quindi essere usato come:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Impostare il limite giornaliero

Per ottenere le proprietà del limite giornaliero, usare il cmdlet [set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan): 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Per impostare le proprietà del limite giornaliero, usare lo stesso cmdlet. Ad esempio, per impostare il limite di 300 GB al giorno,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

È anche possibile usare [ARMClient](https://github.com/projectkudu/ARMClient) per ottenere e impostare parametri del limite giornaliero.  Per ottenere i valori correnti, usare:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Impostare l'ora di reimpostazione del limite di uso giornaliero

Per impostare l'ora di reimpostazione del limite di uso giornaliero, è possibile usare [ARMClient](https://github.com/projectkudu/ARMClient). Di seguito è riportato un esempio che usa `ARMClient` per impostare l'ora di reimpostazione su una nuova ora (in questo esempio 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Impostare il piano tariffario 

Per ottenere il piano tariffario corrente, usare il cmdlet [set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan):

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Per impostare il piano tariffario, usare lo stesso cmdlet con il `-PricingPlan` specificato:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

È anche possibile impostare il piano tariffario su una risorsa di Application Insights esistente usando il modello di Gestione risorse precedente, omettendo la risorsa "microsoft.insights/components" e il nodo `dependsOn` dalla risorsa di fatturazione. Ad esempio, per impostarlo sul piano Per GB (denominato in precedenza piano Basic), eseguire:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

`priceCode` viene definito come:

|priceCode|piano|
|---|---|
|1|Per GB (in precedenza denominato piano Basic)|
|2|Per nodo (denominato in precedenza il piano Enterprise)|

Infine, è possibile usare [ARMClient](https://github.com/projectkudu/ARMClient) per ottenere e impostare piani tariffari e i parametri del limite giornaliero.  Per ottenere i valori correnti, usare:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

È possibile impostare tutti questi parametri usando:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Questo imposterà il limite giornaliero su 200 GB/giorno, configurerà l'ora di ripristino del limite di uso giornaliero su 12:00 UTC, invierà messaggi di posta elettronica quando viene raggiunto il limite e il livello di avviso e imposterà la soglia di avviso sul 90% del limite.  

## <a name="add-a-metric-alert"></a>Aggiungere un avviso per la metrica

Per automatizzare la creazione di avvisi delle metriche, vedere l'[articolo relativo al modello di avvisi della metrica](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Aggiungere un test di disponibilità

Per automatizzare i test di disponibilità, vedere l'[articolo relativo al modello di avvisi della metrica ](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>Aggiungere altre risorse

Per automatizzare la creazione di altre risorse di qualsiasi tipo, creare un esempio manualmente e quindi copiare il codice e impostarne i parametri da [Azure Resource Manager](https://resources.azure.com/). 

1. Aprire [Gestione risorse di Azure](https://resources.azure.com/). Scorrere verso il basso `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` fino alla risorsa dell'applicazione. 
   
    ![Navigare in Esplora risorse di Azure](./media/powershell/01.png)
   
    *Componenti* sono le risorse base di Application Insights per la visualizzazione di applicazioni. Sono disponibili risorse separate per le regole di avviso associate e i test web di disponibilità.
2. Copiare i JSON del componente nella posizione appropriata in `template1.json`.
3. Eliminare queste proprietà:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Aprire le sezioni `webtests` e `alertrules` e copiare i JSON per i singoli elementi nel modello. (Non copiare dai nodi `webtests` o `alertrules`: passare agli elementi sotto ad essi.)
   
    Ciascun test web dispone di una regola di avviso associata, perciò è necessario copiarli entrambi.
   
    È possibile includere anche avvisi nelle metriche. [Nomi delle metriche](powershell-alerts.md#metric-names).
5. Inserire questa riga in ciascuna risorsa:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Impostazione dei parametri per il modello
È necessario sostituire i nomi specifici con i parametri. Per [impostare i parametri di un modello](../../azure-resource-manager/templates/template-syntax.md), si scrivono espressioni mediante un [set di funzioni di supporto](../../azure-resource-manager/templates/template-functions.md). 

È Impossibile impostare i parametri per una sola parte di una stringa, quindi utilizzare `concat()` per compilare stringhe.

Di seguito sono riportati esempi delle sostituzioni che si possono apportare. Sono presenti più occorrenze di ogni sostituzione. Potrebbero esserne necessarie altre nel modello. Questi esempi utilizzano i parametri e le variabili definite nella parte superiore del modello.

| trovare | sostituire con |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (minuscolo) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Impostazione di dipendenze tra le risorse
Azure deve configurare le risorse in ordine fisso. Per assicurarsi che un programma di installazione venga completato prima che inizi il successivo, aggiungere le righe delle dipendenze:

* Nella risorsa del test di disponibilità:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Nella risorsa avviso per un test di disponibilità:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Passaggi successivi
Altri articoli di automazione:

* [Creare una risorsa di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) : metodo rapido che esclude l'uso di un modello.
* [Configurare avvisi](powershell-alerts.md)
* [Creare test Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Inviare i dati del servizio Diagnostica di Azure ad Application Insights](powershell-azure-diagnostics.md)
* [Creare annotazioni di rilascio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)