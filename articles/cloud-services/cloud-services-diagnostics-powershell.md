---
title: Abilitare la diagnostica nei servizi cloud di Azure (versione classica) con PowerShell | Microsoft Docs
description: Informazioni su come usare PowerShell per abilitare la raccolta di dati di diagnostica da un servizio cloud di Azure con l'estensione Diagnostica di Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0b6096f980234c09bf1c2da2b4621b8ed66b495d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578176"
---
# <a name="enable-diagnostics-in-azure-cloud-services-classic-using-powershell"></a>Abilitare la diagnostica nei servizi cloud di Azure (versione classica) con PowerShell

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

È possibile raccogliere dati di diagnostica come log applicazioni, contatori delle prestazioni e così via da un servizio cloud mediante l'estensione Diagnostica di Azure. Questo articolo descrive come abilitare l'estensione Diagnostica di Azure per un servizio Cloud tramite PowerShell.  Per i prerequisiti necessari per questo articolo, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/) .

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Abilitare l'estensione delle funzionalità di diagnostica come parte della distribuzione di un servizio Cloud
Questo approccio è applicabile al tipo di scenari di integrazione continua, in cui l'estensione Diagnostica può essere abilitata come parte della distribuzione del servizio cloud. Quando si crea una nuova distribuzione del servizio cloud, è possibile abilitare l'estensione di diagnostica passando il parametro *ExtensionConfiguration* al cmdlet [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment?view=azuresmps-3.7.0&preserve-view=true&preserve-view=true) . Il parametro *ExtensionConfiguration* accetta una matrice di configurazioni di diagnostica che può essere creata utilizzando il cmdlet [New AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0&preserve-view=true) .

L'esempio seguente mostra come abilitare la diagnostica per un servizio cloud con un WebRole e un WorkerRole, ognuno dei quali ha una configurazione di diagnostica diversa.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Se il file di configurazione della diagnostica specifica un elemento `StorageAccount` con il nome di un account di archiviazione, il cmdlet `New-AzureServiceDiagnosticsExtensionConfig` userà automaticamente tale account di archiviazione. A questo scopo, l'account di archiviazione deve trovarsi nella stessa sottoscrizione del servizio cloud da distribuire.

Da Azure SDK 2.6 in poi, i file di configurazione dell'estensione generati dall'output della destinazione di pubblicazione di MSBuild includeranno il nome dell'account di archiviazione basato sulla stringa di configurazione della diagnostica specificata nel file di configurazione del servizio (cscfg). Lo script seguente mostra come analizzare i file di configurazione dell'estensione dall'output della destinazione di pubblicazione e configurare l'estensione Diagnostica per ogni ruolo quando si distribuisce il servizio cloud.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online usa un approccio simile per le distribuzioni automatiche di Servizi cloud con l'estensione Diagnostica. Per un esempio completo, vedere [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) .

Se nella configurazione di diagnostica non è specificato alcun elemento `StorageAccount`, è necessario passare il parametro *StorageAccountName* al cmdlet. Se viene specificato il parametro *StorageAccountName* , il cmdlet userà sempre l'account di archiviazione specificato nel parametro e non quello specificato nel file di configurazione della diagnostica.

Se l'account di archiviazione di diagnostica si trova in una sottoscrizione diversa da quella del servizio cloud, è necessario passare al cmdlet i parametri *StorageAccountName* e *StorageAccountKey* in modo esplicito. Il parametro *StorageAccountKey* non è necessario quando l'account di archiviazione di diagnostica si trova nella stessa sottoscrizione, perché il cmdlet può eseguire automaticamente una query e impostare il valore della chiave durante l'abilitazione dell'estensione di diagnostica. Tuttavia, se l'account di archiviazione di diagnostica si trova in una sottoscrizione diversa, il cmdlet potrebbe non essere in grado di ottenere automaticamente la chiave ed è necessario specificare la chiave in modo esplicito tramite il parametro *StorageAccountKey* .

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Abilitare l'estensione della diagnostica in un servizio Cloud esistente
È possibile usare il cmdlet [Set AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension?view=azuresmps-3.7.0&preserve-view=true) per abilitare o aggiornare la configurazione della diagnostica in un servizio cloud già in esecuzione.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Ottenere la configurazione di estensione della diagnostica corrente
Utilizzare il cmdlet [Get AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension?view=azuresmps-3.7.0&preserve-view=true) per ottenere la configurazione di diagnostica corrente per un servizio cloud.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Rimuovere l'estensione della diagnostica
Per disattivare la diagnostica in un servizio cloud, è possibile usare il cmdlet [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0&preserve-view=true) .

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se è stata abilitata l'estensione di diagnostica usando *set-AzureServiceDiagnosticsExtension* o *New-AzureServiceDiagnosticsExtensionConfig* senza il parametro *Role* , è possibile rimuovere l'estensione usando *Remove-AzureServiceDiagnosticsExtension* senza il parametro *Role* . Se il parametro *Role* è stato usato quando si Abilita l'estensione, è necessario usarlo anche quando si rimuove l'estensione.

Per rimuovere l'estensione della diagnostica da ogni singolo ruolo:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre istruzioni sull'uso della diagnostica di Azure e di altre tecniche per la risoluzione dei problemi, vedere [Abilitazione di Diagnostica in servizi cloud e macchine virtuali di Azure](cloud-services-dotnet-diagnostics.md).
* Lo [Schema di configurazione della diagnostica](../azure-monitor/agents/diagnostics-extension-schema-windows.md) illustra le varie opzioni di configurazione xml per l'estensione della diagnostica.
* Per informazioni su come abilitare l'estensione della diagnostica per le macchine virtuali, vedere [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Gestione risorse di Azure](../virtual-machines/extensions/diagnostics-template.md)