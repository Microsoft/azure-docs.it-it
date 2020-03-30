---
title: Monitorare a livello di codice una data factory di Azure
description: Informazioni su come monitorare una pipeline in una data factory con diversi Software Development Kit (SDK).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 43a31d588ff6616d7200d9773883ce5da570b100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927366"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Monitorare a livello di codice una data factory di Azure
Informazioni su come monitorare una pipeline in una data factory con diversi Software Development Kit (SDK). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Intervallo di dati

Data Factory memorizza i dati di esecuzione della pipeline solo per 45 giorni. Quando si eseguono query a livello di codice relative ai dati delle esecuzioni della pipeline di Data Factory, ad esempio con il comando di PowerShell `Get-AzDataFactoryV2PipelineRun` non sono presenti date massime per i parametri facoltativi `LastUpdatedAfter` e `LastUpdatedBefore`. Se, invece, si eseguono query sui dati dell'anno precedente, la query non restituisce un errore ma solo i dati di esecuzione della pipeline degli ultimi 45 giorni.

Per rendere permanenti i dati di esecuzione della pipeline per più di 45 giorni, impostare la registrazione diagnostica con [Monitoraggio di Azure](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Per una descrizione completa di creazione e monitoraggio di una pipeline con .NET SDK, vedere [Creare una data factory e una pipeline con .NET SDK](quickstart-create-data-factory-dot-net.md).

1. Aggiungere il codice seguente per controllare continuamente lo stato dell'esecuzione della pipeline fino al termine della copia dei dati.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Aggiungere il codice seguente per recuperare i dettagli dell'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Per la documentazione completa su .NET SDK, vedere le [Data Factory .NET SDK reference](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet) (Informazioni di riferimento su .NET SDK di Data Factory).

## <a name="python"></a>Python
Per una descrizione completa di creazione e monitoraggio di una pipeline con Python SDK, vedere [Creare una data factory e una pipeline con Python](quickstart-create-data-factory-python.md).

Per monitorare l'esecuzione della pipeline, aggiungere il codice seguente

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Per la documentazione completa su Python SDK, vedere [Data Factory](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>API REST
Per una descrizione completa sulla creazione e sul monitoraggio di una pipeline con API REST, vedere [Creare una data factory di Azure e una pipeline usando l'API REST](quickstart-create-data-factory-rest-api.md).
 
1. Eseguire lo script seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Eseguire lo script seguente per recuperare i dettagli sull'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Per la documentazione completa sull'API REST, vedere [Data Factory REST API Reference](/rest/api/datafactory/) (Informazioni di riferimento sull'API REST di Data Factory).

## <a name="powershell"></a>PowerShell
Per una descrizione completa sulla creazione e sul monitoraggio di una pipeline con PowerShell, vedere [Creare una data factory e una pipeline con PowerShell](quickstart-create-data-factory-powershell.md).

1. Eseguire lo script seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Eseguire lo script seguente per recuperare i dettagli sull'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Per una documentazione completa sui cmdlet di PowerShell, vedere [Data Factory PowerShell cmdlet reference](/powershell/module/az.datafactory) (Informazioni di riferimento sui cmdlet di PowerShell per Data Factory).

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sull'utilizzo di Monitoraggio di Azure per monitorare le pipeline di Data Factory, vedere l'articolo sul [monitoraggio delle pipeline mediante Monitoraggio di Azure](monitor-using-azure-monitor.md). 

