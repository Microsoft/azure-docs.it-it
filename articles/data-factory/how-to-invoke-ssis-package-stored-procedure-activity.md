---
title: Eseguire il pacchetto SSIS con l'attività Stored procedure - AzureRun SSIS package with Stored Procedure Activity - Azure
description: Questo articolo descrive come eseguire un pacchetto di SQL Server Integration Services (SSIS) in una pipeline di Azure Data Factory usando l'attività stored procedure.
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: 063728c03c689c2eafec889bdee8276772ae685a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444024"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Eseguire un pacchetto SSIS con l'attività stored procedure in Azure Data Factory
Questo articolo descrive come eseguire un pacchetto SSIS in una pipeline di Azure Data Factory usando un'attività stored procedure. 

## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-sql-database"></a>database SQL di Azure 
La procedura dettagliata riportata in questo articolo usa un database SQL di Azure che ospita il catalogo SSIS. È anche possibile usare un'istanza gestita di database SQL di Azure.

## <a name="create-an-azure-ssis-integration-runtime"></a>Creare un runtime di integrazione SSIS di Azure
Se non è disponibile, creare un runtime di integrazione SSIS di Azure seguendo le istruzioni dettagliate riportate in [Esercitazione: Distribuire pacchetti SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interfaccia utente di Data Factory (portale di Azure)
In questa sezione verrà usata l'interfaccia utente di Data Factory per creare una pipeline di Data Factory con un'attività stored procedure che richiama un pacchetto SSIS.

### <a name="create-a-data-factory"></a>Creare una data factory
La prima operazione da eseguire è creare una data factory usando il portale di Azure. 

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Passare al [portale di Azure](https://portal.azure.com). 
3. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale.** Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
     ![Il nome non è disponibile - Errore](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il gruppo di **risorse,** effettuare una delle seguenti operazioni:
     
   - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
   - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
     Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
4. Selezionare **V2** per **version**.
5. Selezionare il **percorso** per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate da Data Factory. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre località.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

     ![Riquadro Deploying data factory (Distribuzione della data factory)](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
     ![Home page di Data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Azure Data Factory in una scheda separata. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Creare una pipeline con un'attività stored procedure
In questo passaggio viene usata l'interfaccia utente di Data Factory per creare una pipeline. Si aggiunge un'attività stored procedure alla pipeline e la si configura per eseguire il pacchetto SSIS usando la stored procedure sp_executesql. 

1. Nella pagina introduttiva fare clic su **Crea pipeline**: 

    ![Pagina delle attività iniziali](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Nella casella degli strumenti **Attività** espandere **Generale** e trascinare l'attività **Stored procedure** nell'area di progettazione della pipeline. 

    ![Trascinamento dell'attività stored procedure](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Nella finestra delle proprietà per l'attività stored procedure passare alla scheda **Account SQL** e fare clic su **+ Nuovo**. Viene creata una connessione al database SQL di Azure che ospita il catalogo SSIS (database SSISDB). 
   
    ![Pulsante Nuovo per il servizio collegato](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Selezionare **Database SQL di Azure** per **Tipo**.
    2. Selezionare il runtime di integrazione di Azure **predefinito** per connettersi al database SQL di Azure che ospita il database `SSISDB`.
    3. Selezionare il database SQL di Azure che ospita il database SSISDB per il campo**Nome server**.
    4. Selezionare **SSISDB** per **Nome database**.
    5. Per **Nome utente** immettere il nome dell'utente che ha accesso al database.
    6. Per **Password** immettere la password dell'utente. 
    7. Testare la connessione al database facendo clic sul pulsante **Test connessione**.
    8. Salvare il servizio collegato facendo clic sul pulsante **Salva**. 

        ![Servizio collegato per il database SQL di Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Nella finestra delle proprietà passare alla scheda **Stored procedure** dalla scheda **Account SQL** ed eseguire la procedura seguente: 

    1. Selezionare **Modifica**. 
    2. Per il campo Nome `sp_executesql`stored **procedure,** immettere . 
    3. Fare clic su **+ Nuovo** nella sezione **Parametri stored procedure**. 
    4. Per il **nome** del parametro, immettere **stmt**. 
    5. Per il **tipo** del parametro, immettere **Stringa**. 
    6. Per il **valore** del parametro, immettere la query SQL seguente.

        Nella query SQL specificare i valori corretti per i parametri **folder_name**, **project_name** e **package_name**. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Servizio collegato per il database SQL di Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Per convalidare la configurazione della pipeline, fare clic su **Convalida** sulla barra degli strumenti. Per chiudere il **Pipeline Validation Report** (Report di convalida della pipeline) fare clic su **>>**.

    ![Convalidare la pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Pubblicare la pipeline in Data Factory facendo clic sul pulsante **Publish All** (Pubblica tutto). 

    ![Pubblica](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Eseguire e monitorare la pipeline
In questa sezione si attiva un'esecuzione della pipeline e quindi la si monitora. 

1. Per attivare un'esecuzione della pipeline, fare clic su **Trigger** sulla barra degli strumenti e quindi su **Trigger ora**. 

    ![Trigger now (Attiva adesso)](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. Nella finestra **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**. 
3. Passare alla scheda **Monitoraggio** a sinistra. Verrà visualizzata l'esecuzione della pipeline e il relativo stato insieme ad altre informazioni (ad esempio l'Ora di inizio dell'esecuzione). Per aggiornare la visualizzazione, fare clic su **Aggiorna**.

    ![Esecuzioni di pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Fare clic sul collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Verrà visualizzata una sola esecuzione attività dal momento che la pipeline ha una sola attività (attività stored procedure).

    ![Esecuzioni attività](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. È possibile eseguire la **query** seguente sul database SSISDB nel server SQL Azure per verificare che il pacchetto sia stato eseguito. 

    ```sql
    select * from catalog.executions
    ```

    ![Verificare le esecuzioni del pacchetto](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> È anche possibile creare un trigger pianificato per la pipeline in modo che questa venga eseguita in base a una pianificazione (oraria, giornaliera e così via). Per un esempio, vedere [Creare una data factory tramite l'interfaccia utente di Azure Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In questa sezione verrà usato Azure PowerShell per creare una pipeline di Data Factory con un'attività stored procedure che richiama un pacchetto SSIS. 

Installare i moduli di Azure PowerShell più recenti seguendo le istruzioni in [Come installare e configurare Azure PowerShell.](/powershell/azure/install-az-ps) 

### <a name="create-a-data-factory"></a>Creare una data factory
È possibile usare la stessa data factory che ha il runtime di integrazione SSIS di Azure oppure crearne una distinta. La procedura seguente descrive le operazioni necessarie per creare una data factory. Viene creata una pipeline con un'attività stored procedure in questa data factory. L'attività stored procedure esegue una stored procedure nel database SSISDB per l'esecuzione del pacchetto SSIS. 

1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) tra virgolette doppie e quindi eseguire il comando. Ad esempio `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$ResourceGroupName` ed eseguire di nuovo il comando.
2. Per creare il gruppo di risorse di Azure, eseguire questo comando: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$ResourceGroupName` ed eseguire di nuovo il comando. 
3. Definire una variabile per il nome della data factory. 

    > [!IMPORTANT]
    >  Aggiornare il nome della data factory in modo che sia univoco a livello globale. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Per creare la data factory, eseguire il cmdlet **Set-AzDataFactoryV2** usando le proprietà Location e ResourceGroupName della variabile $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure.
* Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente, quindi espandere **Analytics** per individuare **Data Factory**: [ Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

### <a name="create-an-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure
Creare un servizio collegato per collegare il database SQL di Azure che ospita il catalogo SSIS alla data factory. Data Factory usa le informazioni in questo servizio collegato per connettersi al database SSISDB ed esegue una stored procedure per l'esecuzione di un pacchetto SSIS. 

1. Creare un file JSON denominato **AzureSqlDatabaseLinkedService.json** nella cartella **C:\ADF\RunSSISPackage** con il contenuto seguente: 

    > [!IMPORTANT]
    > Sostituire &lt;servername&gt;, &lt;username&gt; e &lt;password&gt; con i valori del database SQL di Azure prima di salvare il file.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. In **Azure PowerShell** passare alla cartella **C:\ADF\RunSSISPackage**.

3. Eseguire il cmdlet **Set-AzDataFactoryV2LinkedService** per creare il servizio collegato: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Creare una pipeline con un'attività stored procedure 
In questo passaggio viene creata una pipeline con un'attività stored procedure. L'attività chiama la stored procedure sp_executesql per l'esecuzione del pacchetto SSIS. 

1. Creare un file JSON denominato **RunSSISPackagePipeline.json** nella cartella **C:\ADF\RunSSISPackage** con il contenuto seguente:

    > [!IMPORTANT]
    > Sostituire &lt;FOLDER NAME&gt;, &lt;PROJECT NAME&gt; e &lt;PACKAGE NAME&gt; con i nomi di cartella, progetto e pacchetto nel catalogo SSIS prima di salvare il file. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Per creare la pipeline: **RunSSISPackagePipeline**, eseguire il cmdlet **Set-AzDataFactoryV2Pipeline.**

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline
Utilizzare il cmdlet **Invoke-AzDataFactoryV2Pipeline** per eseguire la pipeline. Il cmdlet restituisce l'ID di esecuzione della pipeline per il monitoraggio futuro.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

Eseguire lo script di PowerShell seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati. Copiare/Incollare lo script seguente nella finestra di PowerShell e premere INVIO. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Creare un trigger
Nel passaggio precedente è stata chiamata la pipeline su richiesta. È anche possibile creare un trigger di pianificazione per eseguire la pipeline in base a una pianificazione (oraria, giornaliera e così via).

1. Creare un file JSON denominato **MyTrigger.json** nella cartella **C:\ADF\RunSSISPackage** con il contenuto seguente: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. In **Azure PowerShell** passare alla cartella **C:\ADF\RunSSISPackage**.
3. Eseguire il cmdlet **Set-AzDataFactoryV2Trigger,** che crea il trigger. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Per impostazione predefinita, lo stato del trigger è arrestato. Avviare il trigger eseguendo il cmdlet **Start-AzDataFactoryV2Trigger.** 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Verificare che il trigger venga avviato eseguendo il cmdlet **Get-AzDataFactoryV2Trigger.** 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Eseguire il comando seguente dopo l'ora successiva. Ad esempio, se l'ora corrente è 15:25 UTC, eseguire il comando alle 16 UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    È possibile eseguire la query seguente rispetto al database SSISDB nel server SQL di Azure per verificare che il pacchetto sia stato eseguito. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Passaggi successivi
È anche possibile monitorare la pipeline tramite il portale di Azure. Per istruzioni dettagliate, vedere [Monitorare la pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
