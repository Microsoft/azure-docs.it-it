---
title: Inviare metriche dei servizi cloud classici al database di metriche di monitoraggio di Azure
description: Descrive il processo per l'invio di metriche delle prestazioni del sistema operativo guest per i servizi cloud di Azure classico all'archivio delle metriche di monitoraggio di Azure.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: 0ef9d8118a8ff1d9fdd69566dd60033f5847f810
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048963"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Inviare le metriche del sistema operativo guest ai Servizi cloud classici dell'archivio delle metriche di Monitoraggio di Azure 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

L'[estensione Diagnostica](../agents/diagnostics-extension-overview.md) di Monitoraggio di Azure consente di raccogliere le metriche e i log dal sistema operativo guest eseguito come parte di un cluster di macchine virtuali, di un servizio cloud o di un cluster di Service Fabric. L'estensione può inviare dati di telemetria a [molte posizioni diverse](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

Questo articolo descrive il processo di invio delle metriche sulle prestazioni del sistema operativo guest per i Servizi cloud classici di Azure all'archivio delle metriche di Monitoraggio di Azure. A partire dalla versione 1.11 di Diagnostica è possibile scrivere le metriche direttamente nell'archivio delle metriche di Monitoraggio di Azure in cui sono già state raccolte le metriche standard della piattaforma. 

L'archiviazione in questo percorso consente di accedere alle stesse azioni disponibili per le metriche della piattaforma. Le azioni includono quasi in tempo reale gli avvisi, i grafici, il routing, l'accesso dall'API REST e altro ancora.  Le versioni precedenti dell'estensione Diagnostica eseguono operazioni di scrittura in Archiviazione di Azure, ma non nell'archivio dati di Monitoraggio di Azure.  

Il processo illustrato in questo articolo funziona solo per i contatori delle prestazioni nei Servizi cloud di Azure, mentre non funziona per altre metriche personalizzate. 

## <a name="prerequisites"></a>Prerequisiti

- È necessario essere un [amministratore del servizio o un coamministratore](../../cost-management-billing/manage/add-change-subscription-administrator.md) nella sottoscrizione di Azure. 

- La sottoscrizione deve essere registrata con [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- È necessario avere installato [Azure PowerShell](/powershell/azure) o [Azure Cloud Shell](../../cloud-shell/overview.md).

- Il servizio cloud deve trovarsi in un' [area che supporta le metriche personalizzate](./metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Eseguire il provisioning del servizio cloud e dell'account di archiviazione 

1. Creare e distribuire un servizio cloud classico. Un esempio dell'applicazione di Servizi cloud classica e della distribuzione è reperibile in [Introduzione a Servizi cloud di Azure e ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. È possibile usare un account di archiviazione esistente o distribuirne uno nuovo. È consigliabile che l'account di archiviazione si trovi nella stessa area del servizio cloud classico appena creato. Nel portale di Azure passare al pannello della risorsa **Account di archiviazione** e scegliere **Chiavi**. Annotare il nome dell'account di archiviazione e della chiave dell'account di archiviazione. Queste informazioni sono necessarie nei passaggi successivi.

   ![Chiavi dell'account di archiviazione](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Creare un'entità servizio 

Creare un'entità servizio nel tenant di Azure Active Directory usando le istruzioni disponibili in [usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md). Tenere presente quanto segue durante questo processo: 

- È possibile inserire qualsiasi URL come URL di accesso.  
- Creare un nuovo segreto client per l'app.  
- Salvare la chiave e l'ID client, in quanto saranno necessari nei passaggi successivi.  

Assegnare all'app creata nel passaggio precedente *Monitoring Metrics Publisher* (Autore delle metriche di monitoraggio) le autorizzazioni per la risorsa di cui si desidera generare le metriche. Se si prevede di usare l'app per generare metriche personalizzate di numerose risorse, è possibile concedere queste autorizzazioni a livello di gruppo di risorse o di sottoscrizione.  

> [!NOTE]
> L'estensione Diagnostica usa l'entità servizio per eseguire l'autenticazione in Monitoraggio di Azure e per generare le metriche per il servizio cloud.

## <a name="author-diagnostics-extension-configuration"></a>Creare la configurazione dell'estensione Diagnostica 

Preparare il file di configurazione dell'estensione Diagnostica. In questo file si stabilisce quali log e contatori delle prestazioni devono essere raccolti dall'estensione Diagnostica per il servizio cloud. Di seguito è riportato un file di configurazione di Diagnostica di esempio:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

Nella sezione "SinksConfig" del file di diagnostica definire un nuovo sink di Monitoraggio di Azure: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Nella sezione del file di configurazione in cui si elencano i contatori delle prestazioni da raccogliere, aggiungere il sink di Monitoraggio di Azure. Questa voce assicura che tutti i contatori delle prestazioni specificati vengano instradati verso Monitoraggio di Azure sotto forma di metriche. È possibile aggiungere o rimuovere contatori delle prestazioni in base alle esigenze. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Nella configurazione privata infine aggiungere una sezione per l'*account di Monitoraggio di Azure*. Immettere l'ID client dell'entità servizio e il segreto che sono stati creati nel passaggio precedente. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Salvare questo file di diagnostica in locale.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Distribuire l'estensione Diagnostica al servizio cloud 

Avviare PowerShell e accedere ad Azure. 

```powershell
Login-AzAccount 
```

Usare i comandi seguenti per archiviare i dettagli dell'account di archiviazione creato in precedenza. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Impostare anche il percorso del file di diagnostica su una variabile usando il comando seguente:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Distribuire l'estensione Diagnostica al servizio cloud con il file di diagnostica con il sink di Monitoraggio di Azure configurato usando il comando seguente:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Resta obbligatorio specificare un account di archiviazione nell'ambito dell'installazione dell'estensione Diagnostica. Tutti i log o i contatori delle prestazioni specificati nel file di configurazione di diagnostica vengono scritti nell'account di archiviazione specificato.  

## <a name="plot-metrics-in-the-azure-portal"></a>Tracciare le metriche nel portale di Azure 

1. Accedere al portale di Azure. 

   ![Screenshot mostra la portale di Azure con monitoraggio, quindi la metrica selezionata.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. Nel menu a sinistra selezionare **monitoraggio.**

3. Nel pannello **Monitoraggio** fare clic sulla scheda **Metriche (anteprima)**.

4. Nell'elenco a discesa della risorsa selezionare Servizio cloud (versione classica).

5. Nel menu a discesa spazi dei nomi selezionare **Azure. VM. Windows. Guest**. 

6. Nel menu a discesa metriche selezionare **Memoria\Byte vincolati byte in uso**. 

È possibile scegliere di visualizzare la memoria totale usata da un ruolo specifico e ogni istanza del ruolo usando le funzionalità di filtro e di suddivisione delle dimensioni. 

 ![Screenshot che mostra i dati di metrica.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [metriche personalizzate](./metrics-custom-overview.md).