---
title: Creare una nuova risorsa di Azure Application Insights | Microsoft Docs
description: Impostare manualmente il monitoraggio di Application Insights per una nuova applicazione live.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0c8b9ccaa70a2fd1bf46c6f4537f54d702ecc48f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537577"
---
# <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Application Insights di Azure visualizza dati relativi all'applicazione in una *risorsa* di Microsoft Azure. La creazione di una nuova risorsa fa dunque parte della [configurazione di Application Insights per monitorare una nuova applicazione][start]. Dopo aver creato la nuova risorsa, è possibile ottenere la relativa chiave di strumentazione e usarla per configurare Application Insights SDK. La chiave di strumentazione collega i dati di telemetria alla risorsa.

## <a name="sign-in-to-microsoft-azure"></a>Accedere a Microsoft AzureSign in to Microsoft Azure

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Accedere al [portale](https://portal.azure.com)di Azure e creare una risorsa Application Insights:Sign in to the Azure portal , and create an Application Insights resource:

![Fare clic sul segno ' ' nell'angolo in alto a sinistra. Selezionare Strumenti di sviluppo seguiti da Application Insights](./media/create-new-resource/new-app-insights.png)

   | Impostazioni        |  valore           | Descrizione  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valore univoco | Nome che identifica l'app che si sta monitorando. |
   | **Gruppo di risorse**     | myResourceGroup      | Nome del gruppo di risorse nuovo o esistente per ospitare i dati di App Insights.Name for the new or existing resource group to host App Insights data. |
   | **Posizione** | Stati Uniti orientali | Scegli una posizione vicino a te o vicino alla posizione in cui è ospitata l'app. |

> [!NOTE]
> Sebbene sia possibile usare lo stesso nome di risorsa in gruppi di risorse diversi, può essere utile usare un nome univoco a livello globale. Ciò può essere utile se si prevede di [eseguire query tra risorse](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) in quanto semplifica la sintassi richiesta.

Immettere i valori appropriati nei campi obbligatori, quindi selezionare **Revisione e creazione**.

![Immettere i valori nei campi obbligatori, quindi selezionare "revisione e creazione".](./media/create-new-resource/review-create.png)

Dopo la creazione dell'app, viene visualizzato un nuovo riquadro. In questo riquadro vengono visualizzati i dati sulle prestazioni e sull'utilizzo dell'applicazione monitorata. 

## <a name="copy-the-instrumentation-key"></a>Eseguire una copia della chiave di strumentazione

La chiave di strumentazione identifica la risorsa a cui si desidera associare i dati di telemetria. Sarà necessario copiare la chiave di strumentazione e aggiungerla al codice dell'applicazione.

![Fare clic e copiare la chiave di strumentazione](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Installare l’SDK nell'app

Installare Application Insights SDK nell'app. Questo passaggio dipende dal tipo di applicazione.

Usare la chiave di strumentazione per configurare l'[SDK installato nell'applicazione][start].

L'SDK include moduli standard che inviano dati di telemetria senza dover scrivere codice aggiuntivo. Per rilevare le azioni degli utenti o diagnosticare i problemi in modo più dettagliato, [usare l'API][api] per inviare dati di telemetria personalizzati.

## <a name="creating-a-resource-automatically"></a>Creazione automatica di una risorsa

### <a name="powershell"></a>PowerShell

Creare una nuova risorsa di Application Insights

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Esempio

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Risultati

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Per la documentazione completa di PowerShell per questo cmdlet e per informazioni su come recuperare la chiave di strumentazione, consultare la documentazione di [Azure PowerShell.](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)

### <a name="azure-cli-preview"></a>Interfaccia della riga di comando di Azure (anteprima)Azure CLI (preview)

Per accedere ai comandi dell'interfaccia della riga di comando di Azure di Business Insights di Business Insights di anteprima, è necessario eseguire innanzitutto:To access the preview Application Insights Azure CLI commands you first need to run:

```azurecli
 az extension add -n application-insights
```

Se non si esegue `az extension add` il comando verrà visualizzato un messaggio di errore che indica:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

A questo punto è possibile eseguire quanto segue per creare la risorsa Application Insights:Now you can run the following to create your Application Insights resource:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Esempio

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Risultati

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Per la documentazione completa dell'interfaccia della riga di comando di Azure per questo comando e per informazioni su come recuperare la chiave di strumentazione, vedere la [documentazione dell'interfaccia della riga](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)di comando di Azure.For the full Azure CLI documentation for this command, and to learn how to retrieve the instrumentation key consult the Azure CLI documentation.

## <a name="next-steps"></a>Passaggi successivi
* [Ricerca diagnostica](../../azure-monitor/app/diagnostic-search.md)
* [Esplora le metriche](../../azure-monitor/platform/metrics-charts.md)
* [Scrivere query di Analisi](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
