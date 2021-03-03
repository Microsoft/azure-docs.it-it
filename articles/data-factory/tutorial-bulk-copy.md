---
title: Copiare dati in blocco con PowerShell
description: Usare Azure Data Factory e l'attività di copia per copiare dati in blocco da un archivio dati di origine a un archivio dati di destinazione.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: fc539ababf4cb240fbe78de0d87b1f127807f604
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740429"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-using-powershell"></a>Copiare più tabelle in blocco con Azure Data Factory e PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questa esercitazione illustra come **copiare alcune tabelle dal database SQL di Azure in Azure Synapse Analytics**. È possibile applicare lo stesso modello anche in altri scenari di copia, ad esempio per la copia di tabelle da SQL Server/Oracle in database SQL di Azure/SQL Data Warehouse/archivio BLOB di Azure o la copia di percorsi diversi dall'archivio BLOB alle tabelle del database SQL di Azure.

A livello generale, questa esercitazione prevede la procedura seguente:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare i servizi collegati Database SQL di Azure, Azure Synapse Analytics e Archiviazione di Azure.
> * Creare set di dati di Database SQL di Azure e Azure Synapse Analytics.
> * Creare una pipeline per cercare le tabelle da copiare e un'altra pipeline per eseguire l'operazione di copia effettiva. 
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

In questa esercitazione viene usato Azure PowerShell. Per informazioni sull'uso di altri strumenti/SDK per creare una data factory, vedere le [Guide introduttive](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Flusso di lavoro end-to-end
In questo scenario diverse tabelle di Database SQL di Azure verranno copiate in Azure Synapse Analytics. Ecco la sequenza logica di passaggi nel flusso di lavoro che si verifica nelle pipeline:

![Flusso di lavoro](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* La prima pipeline cerca l'elenco di tabelle da copiare negli archivi dati sink.  In alternativa è possibile mantenere una tabella di metadati che elenca tutte le tabelle da copiare nell'archivio dati sink. La pipeline attiva quindi un'altra pipeline, che esegue l'iterazione di ogni tabella nel database ed esegue l'operazione di copia dei dati.
* La seconda pipeline esegue la copia effettiva. Accetta l'elenco di tabelle come parametro. Per ogni tabella dell'elenco è necessario copiare quella specifica di Database SQL di Azure in quella corrispondente di Azure Synapse Analytics usando una [copia in fasi tramite archiviazione BLOB e PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) per prestazioni ottimali. In questo esempio la prima pipeline passa l'elenco di tabelle come valore per il parametro. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure PowerShell**. Seguire le istruzioni in [Come installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps).
* **Account di archiviazione di Azure**. L'account di archiviazione di Azure viene usato come archivio BLOB di staging nell'operazione di copia in blocco. 
* **Database SQL di Azure**. Questo database contiene i dati di origine. 
* **Azure Synapse Analytics**. Questo data warehouse include i dati copiati dal database SQL. 

### <a name="prepare-sql-database-and-azure-synapse-analytics"></a>Preparare Database SQL e Azure Synapse Analytics

**Preparare il database SQL di Azure di origine**:

Creare un database con i dati dell'esempio Adventure Works LT nel database SQL, seguendo l'articolo [Creare un database nel database SQL di Azure](../azure-sql/database/single-database-create-quickstart.md). Questa esercitazione copia tutte le tabelle da questo database di esempio in Azure Synapse Analytics.

**Preparare l'istanza sink di Azure Synapse Analytics**:

1. Se non è disponibile un'area di lavoro di Azure Synapse Analytics, vedere l'articolo [Introduzione ad Azure Synapse Analytics](..\synapse-analytics\get-started.md) per la procedura per crearne una.

2. Creare gli schemi di tabella corrispondenti in Azure Synapse Analytics. Azure Data Factory viene usato in un passaggio successivo per la migrazione/copia dei dati.

## <a name="azure-services-to-access-sql-server"></a>Accesso dei servizi di Azure a SQL Server

Per Database SQL e Azure Synapse Analytics, consentire ai servizi di Azure di accedere a SQL Server. Assicurarsi che l'impostazione **Consenti l'accesso a Servizi di Azure** sia attivata (**ON**) per il server. Questa impostazione consente al servizio Data Factory di leggere dati da Database SQL di Azure e di scriverli in Azure Synapse Analytics. Per verificare e attivare l'impostazione, seguire questa procedura:

1. Fare clic su **Tutti i servizi** a sinistra e quindi su **Server SQL**.
2. Selezionare il server e fare clic su **Firewall** in **IMPOSTAZIONI**.
3. Nella pagina **Impostazioni del firewall** fare clic su **SÌ** per **Consenti l'accesso a Servizi Azure**.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare **PowerShell**. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.

    Eseguire questo comando e immettere il nome utente e la password usati per accedere al portale di Azure:
        
    ```powershell
    Connect-AzAccount
    ```
    Eseguire questo comando per visualizzare tutte le sottoscrizioni per l'account:

    ```powershell
    Get-AzSubscription
    ```
    Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **SubscriptionId** con l'ID della sottoscrizione di Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Eseguire il cmdlet **Set-AzDataFactoryV2** per creare una data factory. Sostituire i segnaposto con i propri valori prima di eseguire il comando. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Tenere presente quanto segue:

    * È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Per creare istanze di Data Factory è necessario essere un collaboratore o amministratore della sottoscrizione di Azure.
    * Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente, quindi espandere **Analytics** per individuare **Data Factory**: [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

## <a name="create-linked-services"></a>Creare servizi collegati

In questa esercitazione vengono creati tre servizi collegati rispettivamente per origine, sink e BLOB di staging, incluse le connessioni agli archivi dati:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Creare il servizio collegato database SQL di Azure di origine

1. Creare un file JSON denominato **AzureSqlDatabaseLinkedService.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente: Creare la cartella ADFv2TutorialBulkCopy, se non esiste già.

    > [!IMPORTANT]
    > Sostituire &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; con i valori del database SQL di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. In **Azure PowerShell** passare alla cartella **ADFv2TutorialBulkCopy**.

3. Eseguire il cmdlet **Set-AzDataFactoryV2LinkedService** per creare il servizio collegato: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```console
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Creare il servizio collegato del sink di Azure Synapse Analytics

1. Creare un file JSON denominato **AzureSqlDWLinkedService.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente:

    > [!IMPORTANT]
    > Sostituire &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;password&gt; con i valori del database SQL di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Per creare il servizio collegato **AzureSqlDWLinkedService**, eseguire il cmdlet **Set-AzDataFactoryV2LinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```console
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Creare il servizio collegato di staging Archiviazione di Azure

In questa esercitazione l'archivio BLOB di Azure viene usato come area di staging intermedia per abilitare PolyBase per ottimizzare le prestazioni della copia.

1. Creare un file JSON denominato **AzureStorageLinkedService.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente:

    > [!IMPORTANT]
    > Sostituire &lt;accountname&gt; e &lt;accountkey&gt; con il nome e la chiave dell'account di archiviazione di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```

2. Per creare il servizio collegato **AzureStorageLinkedService**, eseguire il cmdlet **Set-AzDataFactoryV2LinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>Creare set di dati

In questa esercitazione vengono creati i set di dati di origine e sink, che specificano la posizione in cui vengono archiviati i dati:

### <a name="create-a-dataset-for-source-sql-database"></a>Creare un set di dati per il database SQL di origine

1. Creare un file JSON denominato **AzureSqlDatabaseDataset.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente: Il valore "tableName" è un valore fittizio, perché successivamente viene usata la query SQL nell'attività di copia per recuperare i dati.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Per creare il set di dati **AzureSqlDatabaseDataset**, eseguire il cmdlet **Set-AzDataFactoryV2Dataset**.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```console
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Creare un set di dati per il sink di Azure Synapse Analytics

1. Creare un file JSON denominato **AzureSqlDWDataset.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente: Il valore "tableName" viene impostato come parametro e successivamente l'attività di copia che fa riferimento a questo set di dati passa il valore effettivo nel set di dati.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Per creare il set di dati **AzureSqlDWDataset**, eseguire il cmdlet **Set-AzDataFactoryV2Dataset**.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```console
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Creare le pipeline

In questa esercitazione vengono create due pipeline:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Creare la pipeline "IterateAndCopySQLTables"

Questa pipeline accetta l'elenco di tabelle come parametro. Per ogni tabella dell'elenco, copia i dati dalla tabella di Database SQL di Azure ad Azure Synapse Analytics usando una copia in fasi e PolyBase.

1. Creare un file JSON denominato **IterateAndCopySQLTables.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from Azure SQL Database to Azure Synapse Analytics",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Per creare la pipeline **IterateAndCopySQLTables**, eseguire il cmdlet **Set-AzDataFactoryV2Pipeline**.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```console
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Creare la pipeline "GetTableListAndTriggerCopyData"

Questa pipeline esegue due passaggi:

* Ricerca della tabella di sistema del database SQL di Azure per ottenere l'elenco di tabelle da copiare.
* Attivazione della pipeline "IterateAndCopySQLTables" per l'esecuzione della copia effettiva dei dati.

1. Creare un file JSON denominato **GetTableListAndTriggerCopyData.json** nella cartella **C:\ADFv2TutorialBulkCopy** con il contenuto seguente:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Per creare la pipeline **GetTableListAndTriggerCopyData**, eseguire il cmdlet **Set-AzDataFactoryV2Pipeline**.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```console
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Avviare e monitorare un'esecuzione della pipeline

1. Avviare un'esecuzione di pipeline per la pipeline "GetTableListAndTriggerCopyData" principale e quindi acquisire l'ID di esecuzione della pipeline per il monitoraggio futuro. Viene attivata di conseguenza l'esecuzione per la pipeline "IterateAndCopySQLTables", come specificato nell'attività ExecutePipeline.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Eseguire lo script seguente per verificare continuamente lo stato di esecuzione della pipeline **GetTableListAndTriggerCopyData** e stampare il risultato finale dell'esecuzione della pipeline e dell'attività.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Ecco l'output dell'esecuzione di esempio:

    ```console
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. È possibile ottenere l'ID dell'esecuzione della pipeline "**IterateAndCopySQLTables**" e verificare il risultato dettagliato dell'esecuzione attività come illustrato di seguito.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Ecco l'output dell'esecuzione di esempio:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Connettersi al sink di Azure Synapse Analytics e verificare che i dati siano stati copiati correttamente da Database SQL di Azure.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare i servizi collegati Database SQL di Azure, Azure Synapse Analytics e Archiviazione di Azure.
> * Creare set di dati di Database SQL di Azure e Azure Synapse Analytics.
> * Creare una pipeline per cercare le tabelle da copiare e un'altra pipeline per eseguire l'operazione di copia effettiva. 
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

Passare all'esercitazione successiva per ottenere informazioni sulla copia incrementale di dati da un'origine a una destinazione:
> [!div class="nextstepaction"]
>[Copiare i dati in modo incrementale](tutorial-incremental-copy-powershell.md)
