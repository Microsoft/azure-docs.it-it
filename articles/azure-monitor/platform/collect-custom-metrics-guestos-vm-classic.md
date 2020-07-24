---
title: Inviare metriche di macchine virtuali Windows classiche al database di metriche di monitoraggio di Azure
description: Inviare le metriche del sistema operativo guest all'archivio dati di Monitoraggio di Azure per una macchina virtuale Windows (versione classica)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 87277c0c61f6d63e453386724dd472d2663e3148
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045205"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Inviare metriche del sistema operativo guest al database di metriche di monitoraggio di Azure per una macchina virtuale Windows (classico)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

L'[estensione Diagnostica](./diagnostics-extension-overview.md) (conosciuta come "WAD" o "Diagnostica") di Monitoraggio di Azure consente di raccogliere le metriche e i log dal sistema operativo guest eseguito come parte di un cluster di macchine virtuali, di un servizio cloud o di Service Fabric. L'estensione può inviare dati di telemetria a [molte posizioni diverse](./data-platform.md?toc=/azure/azure-monitor/toc.json).

Questo articolo descrive il processo per l'invio di metriche delle prestazioni del sistema operativo guest per una macchina virtuale Windows (classica) al database delle metriche di monitoraggio di Azure. A partire dalla versione 1.11 di Diagnostica è possibile scrivere le metriche direttamente nell'archivio delle metriche di Monitoraggio di Azure in cui sono già state raccolte le metriche standard della piattaforma. 

L'archiviazione in questo percorso consente di accedere alle stesse azioni eseguite per le metriche della piattaforma. Le azioni includono quasi in tempo reale gli avvisi, i grafici, il routing, l'accesso dall'API REST e altro ancora. Le versioni precedenti dell'estensione Diagnostica eseguono operazioni di scrittura in Archiviazione di Azure, ma non nell'archivio dati di Monitoraggio di Azure. 

Il processo illustrato in questo articolo funziona solo sulle macchine virtuali classiche con il sistema operativo Windows.

## <a name="prerequisites"></a>Prerequisiti

- È necessario essere un [amministratore del servizio o un coamministratore](../../cost-management-billing/manage/add-change-subscription-administrator.md) nella sottoscrizione di Azure. 

- La sottoscrizione deve essere registrata con [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- È necessario avere installato [Azure PowerShell](/powershell/azure) o [Azure Cloud Shell](../../cloud-shell/overview.md).

- La risorsa della macchina virtuale deve trovarsi in un'[area che supporta le metriche personalizzate](metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Creare una macchina virtuale classica e un account di archiviazione

1. Creare una macchina virtuale classica usando il portale di Azure.
   ![Creare una macchina virtuale classica](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Quando si crea questa macchina virtuale, scegliere l’opzione di creare un nuovo account di archiviazione classico. Questo account di archiviazione verrà usato nei passaggi successivi.

1. Nel portale di Azure passare al pannello della risorsa **Account di archiviazione**. Scegliere **Chiavi** e annotare il nome dell'account di archiviazione e della chiave dell'account di archiviazione. Queste informazioni sono necessarie nei passaggi successivi.
   ![Chiavi di accesso alle risorse di archiviazione](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Creare un'entità servizio nel tenant di Azure Active Directory usando le istruzioni in [creare un'entità servizio](../../active-directory/develop/howto-create-service-principal-portal.md). Tenere presente quanto segue durante questo processo: 
- Creare un nuovo segreto client per l'app.
- Salvare la chiave e l'ID client, in quanto saranno necessari nei passaggi successivi.

Assegnare all'app le autorizzazioni "Monitoring Metrics Publisher" (Autore delle metriche di monitoraggio) per la risorsa di cui si desidera generare le metriche. È possibile usare un gruppo di risorse o un'intera sottoscrizione.  

> [!NOTE]
> L'estensione Diagnostica usa l'entità servizio per eseguire l'autenticazione in Monitoraggio di Azure e per generare le metriche per la macchina virtuale classica.

## <a name="author-diagnostics-extension-configuration"></a>Creare la configurazione dell'estensione Diagnostica

1. Preparare il file di configurazione dell'estensione Diagnostica. Questo file determina quali log e contatori delle prestazioni deve raccogliere l'estensione di diagnostica per la macchina virtuale classica. Di seguito è illustrato un esempio:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. Nella sezione SinksConfig del file di diagnostica definire un nuovo sink di Monitoraggio di Azure, come segue:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Nella sezione del file di configurazione in cui è presente l'elenco dei contatori delle prestazioni da raccogliere, indirizzare i contatori delle prestazioni al sink di Monitoraggio di Azure "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Nella configurazione privata, definire l'account di Monitoraggio di Azure. Quindi aggiungere le informazioni dell'entità servizio da utilizzare per generare metriche.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Salvare il file in locale.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Distribuire l'estensione Diagnostica al servizio cloud

1. Avviare PowerShell ed eseguire l'accesso.

    ```powershell
    Login-AzAccount
    ```

1. Iniziare impostando il contesto per la macchina virtuale classica.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Impostare il contesto dell'account di archiviazione classico creato con la macchina virtuale.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Impostare il percorso del file di diagnostica su una variabile usando il comando seguente:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Preparare l'aggiornamento per la macchina virtuale classica con il file di diagnostica che dispone del sink di Monitoraggio di Azure configurato.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Distribuire l'aggiornamento alla VM eseguendo il comando seguente:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Resta obbligatorio specificare un account di archiviazione nell'ambito dell'installazione dell'estensione Diagnostica. Tutti i log o i contatori delle prestazioni specificati nel file di configurazione di diagnostica verranno scritti nell'account di archiviazione specificato.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Tracciare le metriche nel portale di Azure

1.  Accedere al portale di Azure. 

1.  Nel menu a sinistra selezionare **monitoraggio.**

1.  Nel pannello **Monitoraggio** selezionare **Metriche**.

    ![Esplorare le metriche](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. Nell'elenco a discesa della risorsa selezionare la macchina virtuale classica.

1. Nel menu a discesa spazi dei nomi selezionare **Azure. VM. Windows. Guest**.

1. Nel menu a discesa metriche selezionare **Memoria\Byte vincolati byte in uso**.
   ![Metriche del tracciato](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).
