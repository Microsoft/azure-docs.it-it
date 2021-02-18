---
title: Esempi di PowerShell per Monitoraggio di Azure
description: Usare PowerShell per accedere alle funzionalità di Monitoraggio di Azure, ad esempio scalabilità automatica, avvisi, webhook e ricerca nei log attività.
ms.subservice: ''
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 2/14/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9f43531e6b81b9bd4a294d70e9b8091463cf507a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627458"
---
# <a name="azure-monitor-powershell-samples"></a>Esempi di PowerShell per Monitoraggio di Azure
Questo articolo illustra comandi di PowerShell di esempio per accedere rapidamente alle funzionalità di Monitoraggio di Azure.

> [!NOTE]
> Dal 25 settembre 2016 Monitoraggio di Azure è il nuovo nome di "Azure Insights". Tuttavia, gli spazi dei nomi e quindi i comandi seguenti contengono ancora il termine *insights*.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Configurare PowerShell
Se non è ancora stato fatto, configurare PowerShell per l'esecuzione sul computer. Per altre informazioni, vedere [Come installare e configurare PowerShell](/powershell/azure/).

## <a name="examples-in-this-article"></a>Esempi in questo articolo
Gli esempi in questo articolo illustrano come usare i cmdlet di Monitoraggio di Azure. È anche possibile esaminare l'elenco completo di cmdlet di PowerShell di Monitoraggio di Azure nell'argomento relativo ai [cmdlet di Monitoraggio di Azure(Azure Insights)](/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Eseguire l'acccesso e usare le sottoscrizioni
Per prima cosa, accedere alla sottoscrizione di Azure.

```powershell
Connect-AzAccount
```

Verrà visualizzata una schermata di accesso. Dopo aver effettuato l'accesso, vengono visualizzati l'account, l'ID tenant e l'ID della sottoscrizione predefinito. Tutti i cmdlet di Azure funzionano nel contesto della sottoscrizione predefinita. Per visualizzare l'elenco delle sottoscrizioni accessibili, usare il comando seguente:

```powershell
Get-AzSubscription
```

Per vedere il contesto di lavoro (la sottoscrizione in cui vengono eseguiti i comandi), usare il comando seguente:

```powershell
Get-AzContext
```
Per modificare il contesto di lavoro in una sottoscrizione diversa, usare il comando seguente:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log"></a>Recuperare i log attività
Usare il cmdlet [Get-AzLog](/powershell/module/az.monitor/get-azlog).  Di seguito sono riportati alcuni esempi comuni. Il log attività include gli ultimi 90 giorni di operazioni. L'uso di date precedenti a questo intervallo genera un messaggio di errore.  

Vedere qual è la data/ora corrente per verificare quali orari usare nei comandi seguenti:
```powershell
Get-Date
```

Ottenere le voci di log da questa data e ora fino a oggi:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Ottenere le voci di log in un intervallo di date e ore:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere le voci di log da un gruppo di risorse specifico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Ottenere le voci di log da un provider di risorse specifico in un intervallo di date e ore:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere tutte le voci di log con un chiamante specifico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Il comando seguente recupera gli ultimi 1000 eventi dal registro attività:

```powershell
Get-AzLog -MaxRecord 1000
```

`Get-AzLog` supporta diversi altri parametri. Per altre informazioni, vedere il riferimento `Get-AzLog` .

> [!NOTE]
> `Get-AzLog` fornisce solo 15 giorni di cronologia. L'uso del parametro **-MaxRecords** consente di eseguire una query sugli ultimi N eventi, oltre i 15 giorni. Per accedere agli eventi precedenti ai 15 giorni, usare l'API REST o l'SDK (esempio di C# tramite il SDK). Se non si include **StartTime**, il valore predefinito è **EndTime** meno un'ora. Se non si include **EndTime**, il valore predefinito è l’ora corrente. Tutte le ore sono in formato UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Recupero della cronologia di avvisi
Per visualizzare tutti gli eventi di avviso, è possibile eseguire query in Azure Resource Manager usando gli esempi seguenti.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Per visualizzare la cronologia per una regola avviso specifica, è possibile utilizzare il cmdlet `Get-AzAlertHistory` passando l'ID risorsa della regola avvisi.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Il cmdlet `Get-AzAlertHistory` supporta diversi parametri. Per altre informazioni, vedere [Get-AlertHistory](/previous-versions/azure/mt282453(v=azure.100)).

## <a name="retrieve-information-on-alert-rules"></a>Recupero delle informazioni sulle regole di avviso
Tutti i comandi seguenti agiscono su un gruppo di risorse chiamato "montest".

Visualizzare tutte le proprietà della regola di avviso:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperare tutti gli avvisi in un gruppo di risorse:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Recuperare tutte le regole di avviso impostate per una risorsa di destinazione. Ad esempio, tutte le regole di avviso impostate su una VM.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` supporta altri parametri. Per altre informazioni, vedere [Get-AlertRule](/previous-versions/azure/mt282459(v=azure.100)) .

## <a name="create-metric-alerts"></a>Creare avvisi delle metriche
È possibile usare il cmdlet `Add-AlertRule` per creare, aggiornare o disabilitare una regola di avviso.

È possibile creare proprietà di posta elettronica e webhook usando rispettivamente `New-AzAlertRuleEmail` e `New-AzAlertRuleWebhook`. Nel cmdlet della regola di avviso assegnare queste proprietà come azioni alla proprietà **Actions** della regola di avviso.

La tabella seguente descrive i parametri e valori usati per creare un avviso tramite una metrica.

| parametro | Valore |
| --- | --- |
| Nome |simpletestdiskwrite |
| Posizione di questa regola di avviso |Stati Uniti orientali |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName dell'avviso creato |\PhysicalDisk(_Total)\Disk Writes/sec. Vedere il cmdlet `Get-MetricDefinitions` per il recupero dei nomi esatti delle metriche |
| operator |GreaterThan |
| Valore soglia (conteggio al secondo per questa metrica) |1 |
| WindowSize (formato hh:mm:ss) |00:05:00 |
| aggregatore (statistica della metrica che usa il numero medio, in questo caso) |Media |
| indirizzi di posta elettronica personalizzati (matrice di stringhe) |'foo@example.com','bar@example.com' |
| invio di messaggi di posta elettronica a proprietari, collaboratori e lettori |-SendToServiceOwners |

Creare un'azione Email

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Creazione di un’azione Webhook

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creazione di una regola di avviso sulla metrica CPU% per una VM classica

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recupero di una regola di avviso

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Il cmdlet Aggiungi avviso aggiorna anche la regola se esiste già una regola di avviso per le proprietà specificate. Per disabilitare una regola di avviso, includere il parametro **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Acquisizione di un elenco delle metriche disponibili per gli avvisi
Usare il cmdlet `Get-AzMetricDefinition` per visualizzare l'elenco di tutte le metriche per una specifica risorsa.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

L'esempio seguente genera una tabella con la metrica Name e il  relativo valore Unit.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Un elenco completo delle opzioni disponibili per `Get-AzMetricDefinition` si trova in [Get-MetricDefinitions](/previous-versions/azure/mt282458(v=azure.100)).

## <a name="create-and-manage-activity-log-alerts"></a>Creare e gestire avvisi del log attività
È possibile usare il cmdlet `Set-AzActivityLogAlert` per impostare un avviso del log attività. Per un avviso del log attività è prima necessario definire le proprie condizioni come dizionario di condizioni, quindi creare un avviso che usi tali condizioni.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Le altre proprietà del webhook sono facoltative. È possibile ottenere il contenuto di un avviso del log attività usando `Get-AzActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Creazione e gestione delle impostazioni di scalabilità automatica

> [!NOTE] 
> Per i servizi cloud (Microsoft.ClassicCompute), la scalabilità automatica supporta un intervallo di tempo di 5 minuti (PT5M). Per gli altri servizi, la scalabilità automatica supporta un intervallo di tempo minimo di 1 minuto (PT1M)

Una risorsa, ad esempio un'app Web, una macchina virtuale, un servizio cloud o un set di scalabilità di macchine virtuali, può avere una sola impostazione di scalabilità automatica configurata.
Tuttavia, ogni impostazione di scalabilità automatica può includere diversi profili. Ad esempio, un profilo di scalabilità in base alle prestazioni e un altro profilo basato sulla pianificazione. Ogni profilo può avere più regole associate configurate. Per altre informazioni sulla scalabilità automatica, vedere [Come configurare la scalabilità automatica di un servizio cloud](../cloud-services/cloud-services-how-to-scale-portal.md).

Ecco i passaggi da seguire:

1. Creare le regole.
2. Creare i profili eseguendo il mapping delle regole create in precedenza.
3. Facoltativo: Creare notifiche per la scalabilità automatica configurando le proprietà di webhook e di posta elettronica.
4. Creare un'impostazione di scalabilità automatica con un nome per la risorsa di destinazione associando profili e notifiche creati nei passaggi precedenti.

Gli esempi seguenti illustrano come creare un'impostazione di scalabilità automatica per un set di scalabilità di macchine virtuali per un sistema operativo Windows in base alla metrica di utilizzo della CPU.

Per prima cosa, creare una regola per aumentare il numero di istanze, con un incremento del numero di istanze.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Creare poi una regola per ridurre il numero di istanze, con una diminuzione del numero di istanze.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

A questo punto, creare un profilo per le regole.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Creare una proprietà webhook.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Creare la proprietà di notifica per l'impostazione di scalabilità automatica, tra cui posta elettronica e webhook create in precedenza.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Infine, creare l'impostazione di scalabilità automatica da aggiungere al profilo creato in precedenza. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Per altre informazioni sulla gestione delle impostazioni di scalabilità automatica, vedere [Get-AutoscaleSetting](/previous-versions/azure/mt282461(v=azure.100)).

## <a name="autoscale-history"></a>Cronologia di scalabilità automatica
Il seguente esempio illustra come visualizzare gli eventi di scalabilità automatica e avviso recenti. Usare la ricerca dei registri attività per consultare la cronologia di scalabilità automatica.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

È possibile usare il cmdlet `Get-AzAutoScaleHistory` per recuperare la cronologia di scalabilità automatica.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Per altre informazioni, vedere [Get-AutoscaleHistory](/previous-versions/azure/mt282464(v=azure.100)).

### <a name="view-details-for-an-autoscale-setting"></a>Visualizzazione dei dettagli per un'impostazione di scalabilità automatica
È possibile usare il cmdlet `Get-Autoscalesetting` per recuperare altre informazioni sull'impostazione di scalabilità automatica.

L'esempio seguente mostra dettagli su tutte le impostazioni di scalabilità automatica nel gruppo di risorse 'myrg1'.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

L'esempio seguente mostra i dettagli su tutte le impostazioni di scalabilità automatica nel gruppo di risorse 'myrg1' e in particolare l'impostazione di scalabilità automatica denominata 'MyScaleVMSSSetting'.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Rimozione di un'impostazione di scalabilità automatica
È possibile usare il cmdlet `Remove-Autoscalesetting` per eliminare un'impostazione di scalabilità automatica.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Gestione dei profili di log per i registri attività
È possibile creare un *profilo di log* ed esportare i dati dai registri attività in un account di archiviazione ed è possibile configurare la relativa conservazione dei dati. Facoltativamente, è inoltre possibile trasmettere i dati all'hub eventi. Questa funzionalità attualmente è in anteprima ed è possibile creare solo un profilo di log per ogni sottoscrizione. Per creare e gestire i profili di log, è possibile usare i cmdlet seguenti con la sottoscrizione corrente. È anche possibile scegliere una sottoscrizione specifica. Anche se PowerShell usa la sottoscrizione corrente per impostazione predefinita, è sempre possibile modificarla usando `Set-AzContext`. È possibile configurare i registri attività per indirizzare i dati a qualsiasi account di archiviazione o all'hub eventi all'interno di tale sottoscrizione. I dati sono scritti come file di BLOB in formato JSON.

### <a name="get-a-log-profile"></a>Acquisizione di un profilo di log
Per recuperare i profili di log esistenti, usare il cmdlet `Get-AzLogProfile` .

### <a name="add-a-log-profile-without-data-retention"></a>Aggiunta di un profilo di log senza conservazione dei dati
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Rimozione di un profilo di log
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Aggiunta di un profilo di log con conservazione dei dati
È possibile specificare la proprietà **-RetentionInDays** con il numero di giorni, sotto forma di numero intero positivo, per i quali i dati vengono conservati.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Aggiunta di un profilo di log con conservazione e hub di eventi
Oltre a instradare i dati a un account di archiviazione, è anche possibile trasmetterli all'hub eventi. In questa versione di anteprima, la configurazione dell'account di archiviazione è obbligatoria, mentre quella dell'hub di eventi è facoltativa.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurazione dei log di diagnostica
Molti servizi di Azure offrono log e dati di telemetria aggiuntivi che possono eseguire una o più delle operazioni seguenti: 
 - essere configurati per il salvataggio dei dati nell'account di Archiviazione di Azure
 - essere inviati a Hub eventi
 - essere inviati a un'area di lavoro Log Analytics. 

L'operazione può essere eseguita solo a livello di risorse. L'account di archiviazione o l'hub eventi deve essere presente nella stessa area come risorsa di destinazione in cui viene configurata l'impostazione di diagnostica.

### <a name="get-diagnostic-setting"></a>Acquisizione dell’impostazione di diagnostica
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Disabilitazione dell’impostazione di diagnostica

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Abilitazione dell'impostazione di diagnostica senza conservazione

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Abilitazione dell'impostazione di diagnostica con conservazione

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Abilitazione dell’impostazione di diagnostica con conservazione per una categoria di log specifica

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Abilitazione dell'impostazione di diagnostica per hub eventi

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Abilitazione dell'impostazione di diagnostica per Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

La proprietà WorkspaceId accetta il valore dell'*ID risorsa* dell'area di lavoro. È possibile ottenere l'ID risorsa dell'area di lavoro Log Analytics usando il comando seguente:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Questi comandi possono essere combinati per inviare dati a più destinazioni.
