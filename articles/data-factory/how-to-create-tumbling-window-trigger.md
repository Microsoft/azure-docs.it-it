---
title: Crea trigger finestra a cascata in Azure Data Factory
description: Informazioni su come creare un trigger in Azure Data Factory per l'esecuzione di una pipeline in una finestra a cascata.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: c35fa28457e3cb9a063fa29c20d8651fcb4eeb45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856486"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Creare un trigger per l'esecuzione di una pipeline in una finestra a cascata
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive la procedura per creare, avviare e monitorare un trigger di finestra a cascata. Per informazioni generali sui trigger e i tipi supportati, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md).

I trigger di finestra a cascata vengono attivati in base a un intervallo di tempo periodico a partire da un'ora di inizio specificata, mantenendo al tempo stesso lo stato. Le finestre a cascata sono costituite da una serie di intervalli temporali di dimensioni fisse, contigui e non sovrapposti. Un trigger di finestra a cascata ha una relazione uno a uno con una pipeline e può fare riferimento solo a una singola pipeline. Il trigger di finestra a cascata è un'alternativa più pesante per il trigger di pianificazione che offre una suite di funzionalità per scenari complessi ([dipendenza da altri trigger di finestra a cascata](#tumbling-window-trigger-dependency), [riesecuzione di un processo non riuscito](tumbling-window-trigger-dependency.md#monitor-dependencies) e [impostazione del tentativo utente per le pipeline](#user-assigned-retries-of-pipelines)). Per comprendere ulteriormente la differenza tra il trigger di pianificazione e il trigger di finestra a cascata, visitare [qui](concepts-pipeline-execution-triggers.md#trigger-type-comparison).

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

1. Per creare un trigger di finestra a cascata nell'interfaccia utente di Data Factory, selezionare la scheda **trigger** , quindi selezionare **nuovo**. 
1. Dopo aver aperto il riquadro Configurazione trigger, selezionare **finestra a cascata**e quindi definire le proprietà del trigger della finestra a cascata. 
1. Al termine, selezionare **Salva**.

![Creare un trigger di finestra a cascata nel portale di Azure](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Proprietà del tipo di trigger di finestra a cascata

Una finestra a cascata ha le proprietà del tipo di trigger seguenti:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

La tabella seguente fornisce una panoramica generale degli elementi JSON principali correlati alla ricorrenza e alla pianificazione di un trigger di finestra a cascata:

| Elemento JSON | Descrizione | Type | Valori consentiti | Obbligatoria |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Tipo di trigger. Il tipo è il valore fisso "TumblingWindowTrigger". | string | "TumblingWindowTrigger" | Sì |
| **runtimeState** | Stato attuale del runtime del trigger.<br/>**Nota**: questo elemento è \<readOnly> . | string | "Started", "Stopped", "Disabled" | Sì |
| **frequenza** | Stringa che rappresenta l'unità di frequenza (minuti o ore) con cui si ripete il trigger. Se i valori di data di **startTime** sono più granulari del valore di **frequency**, le date di **startTime** sono considerate quando vengono calcolati i limiti della finestra. Ad esempio, se il valore di **frequency** è ogni ora e il valore di **startTime** è 2017-09-01T10:10:10Z, la prima finestra è (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | string | "minute", "hour"  | Sì |
| **intervallo** | Numero intero positivo indicante l'intervallo per il valore **frequency**, che determina la frequenza con cui viene eseguito il trigger. Se, ad esempio, **interval** è 3 e **frequency** è "hour", il trigger si ripete ogni 3 ore. <br/>**Nota**: l'intervallo minimo della finestra è 5 minuti. | Integer | Numero intero positivo. | Sì |
| **startTime**| Prima occorrenza, che può essere nel passato. Il primo intervallo di trigger è (**startTime**, **startTime** + **interval**). | Datetime | Valore DateTime. | Sì |
| **endTime**| Ultima occorrenza, che può essere nel passato. | Datetime | Valore DateTime. | Sì |
| **ritardo** | Periodo di tempo in base a cui ritardare l'avvio dell'elaborazione dei dati per la finestra. L'esecuzione della pipeline viene avviata dopo il tempo di esecuzione previsto più il periodo di **ritardo**. Il valore **delay** definisce per quanto tempo il trigger rimane in attesa dopo la scadenza prima di attivare una nuova esecuzione. Il valore **delay** non modifica il valore **startTime** della finestra. Ad esempio, un valore di **delay** pari a 00:10:00 implica un ritardo di 10 minuti. | TimeSpan<br/>(hh:mm:ss)  | Valore di un intervallo di tempo il cui il valore predefinito è 00:00:00. | No |
| **maxConcurrency** | Il numero di esecuzioni di trigger simultanee che vengono generate per le finestre che sono pronte. Ad esempio, per recuperare le informazioni relative alle esecuzioni ogni ora per i risultati del giorno prima in 24 finestre. Se **maxConcurrency** = 10, gli eventi di attivazione vengono generati solo per le prime 10 finestre (00:00-01:00 - 09:00-10:00). Al termine delle prime 10 esecuzioni di pipeline attivate, le esecuzioni di trigger vengono generate per le 10 finestre successive (10:00-11:00 - 19:00-20:00). Continuando con questo esempio di **maxConcurrency** = 10, se sono pronte 10 finestre, ci sono 10 esecuzioni di pipeline totali. Se c'è solo una finestra pronta, è disponibile solo un'esecuzione di pipeline. | Integer | Numero intero compreso tra 1 e 50. | Sì |
| **retryPolicy: Count** | Numero di tentativi prima che l'esecuzione della pipeline venga contrassegnata come "Non riuscita".  | Integer | Numero intero, in cui il valore predefinito è 0 (nessun tentativo). | No |
| **retryPolicy: intervalInSeconds** | Ritardo tra i tentativi di ripetizione specificato in secondi. | Integer | Numero di secondi, in cui il valore predefinito è 30. | No |
| **dependsOn: tipo** | Tipo di TumblingWindowTriggerReference. Obbligatorio se è impostata una dipendenza. | string |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | No |
| **dependsOn: dimensioni** | Dimensione della finestra a cascata delle dipendenze. | TimeSpan<br/>(hh:mm:ss)  | Valore TimeSpan positivo in cui il valore predefinito è la dimensione della finestra del trigger figlio  | No |
| **dependsOn: offset** | Offset del trigger di dipendenza. | TimeSpan<br/>(hh:mm:ss) |  Valore TimeSpan che deve essere negativo in una dipendenza indipendente. Se non viene specificato alcun valore, la finestra è uguale a quella del trigger. | Dipendenza autonoma: Sì<br/>Altro: No  |

> [!NOTE]
> Dopo la pubblicazione di un trigger di finestra a cascata, non è possibile modificare l' **intervallo** e la **frequenza** .

### <a name="windowstart-and-windowend-system-variables"></a>Variabili di sistema WindowStart e WindowEnd

È possibile usare le variabili di sistema **WindowStart** e **WindowEnd** del trigger di finestra a cascata nella definizione della **pipeline** (ovvero per una parte di una query). Passare le variabili di sistema come parametri alla pipeline nella definizione del **trigger**. L'esempio seguente mostra come passare tali variabili come parametri:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Per usare i valori delle variabili di sistema **WindowStart** e **WindowEnd** nella definizione della pipeline, usare i parametri "MyWindowStart" e "MyWindowEnd" di conseguenza.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ordine di esecuzione delle finestre in uno scenario di recupero delle informazioni

Se il startTime del trigger è nel passato, in base a questa formula, M = (CurrentTime-TriggerStartTime)/TumblingWindowSize, il trigger genererà {M} il recupero (paste) viene eseguito in parallelo, rispettando la concorrenza dei trigger, prima di eseguire le esecuzioni future. L'ordine di esecuzione per Windows è deterministico, dall'intervallo più vecchio a quello più recente. Attualmente non è possibile modificare questo comportamento.

### <a name="existing-triggerresource-elements"></a>Elementi TriggerResource esistenti

I punti seguenti si applicano all'aggiornamento degli elementi **TriggerResource** esistenti:

* Il valore per l'elemento **Frequency** (o le dimensioni della finestra) del trigger insieme all'elemento **Interval** non può essere modificato dopo la creazione del trigger. Questa operazione è necessaria per il corretto funzionamento dei riesecuzioni di triggerRun e delle valutazioni delle dipendenze
* Se il valore per l'elemento **endTime** del trigger viene modificato (aggiunto o aggiornato), lo stato delle finestre che sono già elaborate *non* viene reimpostato. Il trigger rispetta il nuovo valore **endTime**. Se il nuovo valore **endTime** è precedente alle finestre già eseguite, il trigger si interrompe. In caso contrario, il trigger si interrompe quando viene rilevato il nuovo valore **endTime**.

### <a name="user-assigned-retries-of-pipelines"></a>Tentativi assegnati dall'utente di pipeline

In caso di errori della pipeline, il trigger di finestra a cascata può ritentare l'esecuzione automatica della pipeline a cui viene fatto riferimento, usando gli stessi parametri di input, senza l'intervento dell'utente. Questa operazione può essere specificata utilizzando la proprietà "retryPolicy" nella definizione del trigger.

### <a name="tumbling-window-trigger-dependency"></a>Dipendenza trigger finestra a cascata

Per assicurarsi che un trigger di finestra a cascata venga eseguito solo dopo la corretta esecuzione di un altro trigger della finestra a cascata nella data factory, [creare una dipendenza del trigger della finestra a cascata](tumbling-window-trigger-dependency.md). 

## <a name="sample-for-azure-powershell"></a>Esempio per Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Questa sezione illustra come usare Azure PowerShell per creare, avviare e monitorare un trigger.

1. Creare un file JSON denominato **MyTrigger.json** nella cartella C:\ADFv2QuickStartPSH con il contenuto seguente:

    > [!IMPORTANT]
    > Prima di salvare il file JSON, impostare il valore dell'elemento **startTime** sull'ora UTC corrente. Impostare il valore dell'elemento **endTime** su un'ora dopo l'ora UTC corrente.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Creare un trigger usando il cmdlet **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Verificare che lo stato del trigger venga **interrotto** con il cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Avviare il trigger usando il cmdlet **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Verificare che lo stato del trigger venga **avviato** con il cmdlet **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Ottenere il trigger viene eseguito in Azure PowerShell usando il cmdlet **Get-AzDataFactoryV2TriggerRun** . Per ottenere informazioni sulle esecuzioni di trigger, eseguire periodicamente il comando seguente. Aggiornare i valori di **TriggerRunStartedAfter** e **TriggerRunStartedBefore** in modo che corrispondano a quelli nella definizione del trigger:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Per monitorare le esecuzioni di trigger e pipeline nel portale di Azure, vedere [Monitorare le esecuzioni di pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Creare una dipendenza del trigger di finestra a cascata](tumbling-window-trigger-dependency.md)
