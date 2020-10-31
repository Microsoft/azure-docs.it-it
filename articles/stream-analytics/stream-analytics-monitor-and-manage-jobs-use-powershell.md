---
title: Monitorare e gestire processi di Analisi di flusso di Azure con PowerShell
description: Questo articolo descrive come usare Azure PowerShell e i cmdlet per monitorare e gestire i processi di Analisi di flusso di Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: dc2f00b859321986b9de7c15462c72049bf49ee2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096904"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorare e gestire i processi di Analisi di flusso con i cmdlet di Azure PowerShell
Informazioni su come monitorare e gestire le risorse di Analisi di flusso con i cmdlet di Azure PowerShell e gli script di PowerShell che eseguono attività di base di Analisi di flusso.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Prerequisiti per l'esecuzione dei cmdlet di Azure PowerShell per Analisi dei flussi
* Creare un gruppo di risorse di Azure nella sottoscrizione. Di seguito è riportato un esempio di script di Azure PowerShell: Per informazioni su Azure PowerShell , vedere [Installare e configurare Azure PowerShell](/powershell/azure/);  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Nei processi di Analisi di flusso creati a livello di codice il monitoraggio non è abilitato per impostazione predefinita.  Il monitoraggio può essere attivato manualmente nel portale di Azure passando alla pagina dedicata al monitoraggio del processo e facendo clic sul pulsante Attiva oppure è possibile farlo a livello di codice seguendo i passaggi in [Analisi di flusso di Azure - Creare un monitoraggio dei processi di Analisi di flusso a livello di codice](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Cmdlet di Azure PowerShell per Analisi dei flussi
I cmdlet di Azure PowerShell indicati di seguito possono essere utilizzati per monitorare e gestire i processi di Analisi dei flussi di Azure. Si noti che sono disponibili diverse versioni di Azure PowerShell. 
**Negli esempi elencati il primo comando è per Azure PowerShell 0.9.8, il secondo comando è per Azure PowerShell 1.0.** Nei comandi di Azure PowerShell 1.0 è sempre presente "Az".

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Elenca tutti i processi di Analisi dei flussi definiti nella sottoscrizione di Azure o nel gruppo di risorse specificato oppure ottiene informazioni su uno specifico processo all'interno di un gruppo di risorse.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Questo comando PowerShell restituisce informazioni su tutti i processi di Analisi di flusso nella sottoscrizione di Azure.

**Esempio 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Questo comando di PowerShell restituisce informazioni su tutti i processi di Analisi di flusso nel gruppo di risorse StreamAnalytics-Default-Central-US.

**Esempio 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Questo comando di PowerShell restituisce informazioni sul processo StreamingJob di Analisi di flusso nel gruppo di risorse StreamAnalytics-Default-West-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Elenca tutti gli input definiti in un processo di Analisi dei flussi specificato oppure ottiene informazioni su un input specifico.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Questo comando di PowerShell restituisce informazioni su tutti gli input definiti nel processo StreamingJob.

**Esempio 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Questo comando di PowerShell restituisce informazioni sull'input denominato EntryStream definito nel processo StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Elenca tutti gli output definiti in un processo di Analisi dei flussi specificato oppure ottiene informazioni su un output specifico.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Questo comando di PowerShell restituisce informazioni sugli output definiti nel processo StreamingJob.

**Esempio 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Questo comando di PowerShell restituisce informazioni sull'output denominato Output definito nel processo StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Ottiene informazioni sulla quota di unità di streaming di un'area specificata.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Questo comando di PowerShell restituisce informazioni sulla quota di unità di streaming e sul relativo utilizzo nell'area Stati Uniti centrali.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Ottiene informazioni su una specifica trasformazione definita nel processo di Analisi dei flussi.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Questo comando di PowerShell restituisce informazioni sulla trasformazione denominata StreamingJob nel processo StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Crea un nuovo input all'interno di un processo di Analisi dei flussi o aggiorna un input esistente specificato.

Il nome dell'input può essere specificato nel file json o nella riga di comando. Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica un input già esistente e non si specifica il parametro -Force, il cmdlet chiederà se si desidera sostituire l'input esistente.

Se si specifica un nome di input esistente e il parametro -Force, l'input verrà sostituito senza chiedere conferma.

Per informazioni dettagliate sulla struttura e sul contenuto dei file JSON, vedere la sezione [Creare l'input (Analisi di flusso di Azure)][msdn-rest-api-create-stream-analytics-input] della [libreria di riferimento dell'API REST di gestione di analisi di flusso][stream.analytics.rest.api.reference].

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Questo comando di PowerShell crea un nuovo input dal file Input.json. Se è già definito un input esistente con il nome specificato nel file di definizione dell'input, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Questo comando di PowerShell crea un nuovo input nel processo denominato EntryStream. Se un input esistente con questo nome è già definito, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Questo comando di PowerShell sostituisce la definizione dell'origine di input esistente, denominata EntryStream, con la definizione presente nel file.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Crea un nuovo processo di Analisi dei flussi in Microsoft Azure o aggiorna la definizione di un processo esistente specificato.

Il nome del processo può essere specificato nel file json o nella riga di comando. Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica un nome di processo già esistente e non si specifica il parametro -Force, il cmdlet chiederà se si desidera sostituire il processo esistente.

Se si specifica un nome di processo esistente e il parametro -Force, la definizione del processo verrà sostituita senza chiedere conferma.

Per informazioni dettagliate sulla struttura e sul contenuto dei file JSON, vedere la sezione [Creare il processo di analisi di flusso][msdn-rest-api-create-stream-analytics-job] della [libreria di riferimento dell'API REST di gestione di analisi di flusso][stream.analytics.rest.api.reference].

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Questo comando di PowerShell crea un nuovo processo dalla definizione presente in JobDefinition.json. Se è già definito un processo esistente con il nome specificato nel file di definizione del processo, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Questo comando di PowerShell sostituisce la definizione del processo per StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Crea un nuovo output all'interno di un processo di Analisi dei flussi o aggiorna un output esistente.  

Il nome dell'output può essere specificato nel file json o nella riga di comando. Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica un output già esistente e non si specifica il parametro -Force, il cmdlet chiederà se si desidera sostituire l'output esistente.

Se si specifica un nome di output esistente e il parametro -Force, l'output verrà sostituito senza chiedere conferma.

Per informazioni dettagliate sulla struttura e sul contenuto dei file JSON, vedere la sezione [Creare l'output (Analisi di flusso di Azure)][msdn-rest-api-create-stream-analytics-output] della [libreria di riferimento dell'API REST di gestione di analisi di flusso][stream.analytics.rest.api.reference].

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Questo comando di PowerShell crea un nuovo output denominato "output" nel processo StreamingJob. Se un output esistente con questo nome è già definito, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Questo comando di PowerShell sostituisce la definizione di "output" nel processo StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Crea una nuova trasformazione all'interno di un processo di Analisi dei flussi o aggiorna la trasformazione esistente.

Il nome della trasformazione può essere specificato nel file json o nella riga di comando. Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica una trasformazione già esistente e non si specifica il parametro -Force, il cmdlet chiederà se si desidera sostituire la trasformazione esistente.

Se si specifica un nome di trasformazione esistente e il parametro -Force, la trasformazione verrà sostituita senza chiedere conferma.

Per informazioni dettagliate sulla struttura e sul contenuto dei file JSON, vedere la sezione [Creare una trasformazione (Analisi di flusso di Azure)][msdn-rest-api-create-stream-analytics-transformation] della [libreria di riferimento dell'API REST di gestione di analisi di flusso][stream.analytics.rest.api.reference].

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Questo comando di PowerShell crea una nuova trasformazione denominata StreamingJobTransform nel processo StreamingJob. Se una trasformazione esistente con questo nome è già definita, il cmdlet chiederà se si desidera sostituirla.

**Esempio 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Questo comando di PowerShell sostituisce la definizione di StreamingJobTransform nel processo StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Elimina in modo asincrono uno specifico input da un processo di Analisi dei flussi in Microsoft Azure.  
Se si specifica il parametro -Force, l'input verrà eliminato senza chiedere conferma.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Questo comando di PowerShell rimuove l'input EventStream nel processo StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Elimina in modo asincrono uno specifico processo di Analisi dei flussi in Microsoft Azure.  
Se si specifica il parametro -Force, il processo verrà eliminato senza chiedere conferma.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Questo comando di PowerShell rimuove il processo StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Elimina in modo asincrono uno specifico output da un processo di Analisi dei flussi in Microsoft Azure.  
Se si specifica il parametro -Force, l’output verrà eliminato senza chiedere conferma.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Questo comando di PowerShell rimuove l'output Output nel processo StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
In modo asincrono e avvia un processo di Analisi dei flussi in Microsoft Azure.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Questo comando di PowerShell avvia il processo StreamingJob con un'ora di inizio output personalizzata impostata su 12 dicembre 2012 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Interrompe in modo asincrono l'esecuzione di un processo di Analisi dei flussi in Microsoft Azure e dealloca le risorse in uso. La definizione del processo e i metadati rimarranno disponibili all'interno della sottoscrizione tramite il portale di Azure e le API di gestione, in modo che sia possibile modificare e riavviare il processo. Non si incorre in alcun addebito per un processo in stato Arrestato.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Questo comando di PowerShell arresta il processo StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Verifica la capacità di Analisi dei flussi di connettersi a un input specificato.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Questo comando di PowerShell verifica lo stato di connessione dell'input EntryStream in StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Verifica la capacità di Analisi dei flussi di connettersi a un output specificato.

**Esempio 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Questo comando di PowerShell verifica lo stato di connessione dell'output Output in StreamingJob.  

## <a name="get-support"></a>Supporto
Per ulteriore supporto, provare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal
[msdn-rest-api-create-stream-analytics-input]: https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-inputs
[msdn-rest-api-create-stream-analytics-output]: https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs
[msdn-rest-api-create-stream-analytics-transformation]: https://docs.microsoft.com/cli/azure/ext/stream-analytics/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

