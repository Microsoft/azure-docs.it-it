---
title: Pulire un processo di Analisi di flusso di Azure
description: Questo articolo illustra metodi diversi per eliminare i processi di Analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426478"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Arrestare o eliminare il processo di Analisi di flusso di AzureStop or delete your Azure Stream Analytics job

I processi di Analisi di flusso di Azure possono essere facilmente arrestati o eliminati tramite il portale di Azure, Azure PowerShell, Azure SDK per .Net o l'API REST. Un processo di Analisi di flusso non può essere recuperato dopo essere stato eliminato.

>[!NOTE] 
>Quando si arresta un processo di Analisi di flusso di Azure, i dati vengono mantenuti solo nell'archiviazione di input e output come ad esempio Hub eventi o Database SQL di Azure. Se è necessario rimuovere dati da Azure, assicurarsi di seguire il processo di rimozione per le risorse di input e output del processo di Analisi di flusso.

## <a name="stop-a-job-in-azure-portal"></a>Arrestare un processo nel portale di Azure

Quando si interrompe un processo, viene eseguito il deprovisioning delle risorse e si interrompe l'elaborazione degli eventi. Vengono interrotti anche gli addebiti relativi a questo processo. Tuttavia, tutta la configurazione viene mantenuta ed è possibile riavviare il processo in un secondo momento 

1. Accedere al [portale](https://portal.azure.com)di Azure . 

2. Individuare il processo di Analisi di flusso in esecuzione e selezionarlo.

3. Nella pagina del processo di Analisi di flusso selezionare **Arresta** per arrestare il processo. 

   ![Arrestare un processo di Analisi di flusso di Azure](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Eliminare un processo nel portale di Azure

>[!WARNING] 
>Un processo di Analisi di flusso non può essere recuperato dopo essere stato eliminato.

1. Accedere al portale di Azure. 

2. Individuare il processo di Analisi di flusso di Azure esistente e selezionarlo.

3. Nella pagina del processo di Analisi di flusso selezionare **Elimina** per eliminare il processo. 

   ![Eliminare un processo di Analisi di flusso di Azure](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Arrestare o eliminare un processo con PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per arrestare un processo tramite PowerShell, utilizzare il cmdlet [Stop-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Per eliminare un processo tramite PowerShell, utilizzare il cmdlet [Remove-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Arrestare o eliminare un processo con Azure SDK per .NET

Per arrestare un processo con Azure SDK per .NET, usare il metodo [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Per eliminare un processo con Azure SDK per .NET, usare il metodo [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Arrestare o eliminare un processo con API REST

Per arrestare un processo con API REST, vedere il metodo [Stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop). Per eliminare un processo con API REST, vedere il metodo [Delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete).
