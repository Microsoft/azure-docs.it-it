---
title: Eseguire la copia incrementale di una tabella con il portale di Azure
description: In questa esercitazione si crea una pipeline di Azure Data Factory che copia i dati in modo incrementale da un database SQL di Azure a un archivio BLOB di Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 78cb58bca9b06b6dcf8549eefa5ebf0eb2b4b01c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409325"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>Caricare i dati in modo incrementale da un database SQL di Azure ad archiviazione BLOB di Azure tramite il portale di Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione si creerà una data factory di Azure con una pipeline che carica dati delta da una tabella di un database SQL di Azure a un archivio BLOB di Azure.

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Preparare l'archivio dati per l'archiviazione del valore limite.
> * Creare una data factory.
> * Creare servizi collegati.
> * Creare i set di dati di origine, sink e limite.
> * Creare una pipeline.
> * Eseguire la pipeline.
> * Monitorare l'esecuzione della pipeline.
> * Esaminare i risultati
> * Aggiungere altri dati all'origine.
> * Eseguire di nuovo la pipeline.
> * Monitorare la seconda esecuzione della pipeline
> * Esaminare i risultati della seconda esecuzione.


## <a name="overview"></a>Panoramica
Il diagramma generale della soluzione è il seguente:

![Caricare i dati in modo incrementale](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Di seguito sono descritti i passaggi fondamentali per la creazione di questa soluzione:

1. **Selezionare la colonna del limite**.
    Selezionare una colonna nell'archivio dati di origine, che può essere usata per analizzare approfonditamente i record nuovi o aggiornati per ogni esecuzione. I dati della colonna selezionata (ad esempio last_modify_time o ID) continuano in genere ad aumentare quando le righe vengono create o aggiornate. Il valore massimo di questa colonna viene usato come limite.

2. **Preparare un archivio dati per l'archiviazione del valore limite**. In questa esercitazione si archivia il valore limite in un database SQL.

3. **Creare una pipeline con il flusso di lavoro seguente**:

    La pipeline di questa soluzione contiene le attività seguenti:

    * Creare due attività di ricerca. Usare la prima attività di ricerca per recuperare l'ultimo valore limite. Usare la seconda attività di ricerca per recuperare il nuovo valore limite. Questi valori limite vengono passati all'attività di copia.
    * Creare un'attività di copia che copi le righe dell'archivio dati di origine con il valore della colonna del limite maggiore del valore limite precedente e minore del nuovo valore limite. L'attività copierà quindi i dati delta dall'archivio dati di origine a un archivio BLOB come nuovo file.
    * Creare un'attività stored procedure che aggiorni il valore limite per la pipeline che verrà eseguita la volta successiva.


Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
* **Database SQL di Azure**. Usare il database come archivio dati di origine. Se non è disponibile un database SQL, vedere [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md) per crearne uno.
* **Archiviazione di Azure**. Usare l'archivio BLOB come archivio dati sink. Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione](../storage/common/storage-account-create.md) per informazioni su come crearne uno. Creare un contenitore denominato adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Creare una tabella di origine dati nel database SQL
1. Aprire SQL Server Management Studio. In **Esplora server** fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**.

2. Eseguire questo comando SQL sul database SQL per creare una tabella denominata `data_source_table` come archivio dell'origine dati:

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    In questa esercitazione si usa LastModifytime come colonna del valore limite. I dati nell'archivio dell'origine dati sono visualizzati nella tabella seguente:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Creare un'altra tabella nel database SQL per archiviare il valore del limite massimo
1. Eseguire questo comando SQL sul database SQL per creare una tabella denominata `watermarktable` in cui archiviare il valore limite:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Impostare il valore predefinito del limite massimo con il nome tabella dell'archivio dei dati di origine. In questa esercitazione, il nome della tabella è data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Esaminare i dati nella tabella `watermarktable`.

    ```sql
    Select * from watermarktable
    ```
    Output:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Creare una stored procedure nel database SQL

Eseguire questo comando per creare una stored procedure nel database SQL:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory**:

   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Nella pagina **Nuova data factory** immettere **ADFIncCopyTutorialDF** per **Nome**.

   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato un punto esclamativo rosso con l'errore seguente, modificare il nome della data factory, ad esempio nomeutenteADFIncCopyTutorialDF, e riprovare. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).

       `Data factory name "ADFIncCopyTutorialDF" is not available`
4. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
5. Per il **gruppo di risorse**, eseguire una di queste operazioni:

      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
        Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
6. Selezionare **V2** per **version**.
7. Selezionare la **località** per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.
8. Fare clic su **Crea**.      
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.

   ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'interfaccia utente di Azure Data Factory in una scheda separata.

## <a name="create-a-pipeline"></a>Creare una pipeline
In questa esercitazione si crea una pipeline con due attività di ricerca, un'attività di copia e un'attività di stored procedure concatenate in una pipeline.

1. Nella pagina **Attività iniziali** dell'interfaccia utente di Data Factory fare clic sul riquadro **Create pipeline** (Crea pipeline).

   ![Pagina Attività iniziali dell'interfaccia utente di Data Factory](./media/doc-common-process/get-started-page.png)    
3. Nella pagina **Generale** della finestra **Proprietà** per la pipeline immettere il nome **IncrementalCopyPipeline**.

4. Verrà ora aggiunta la prima attività di ricerca per recuperare il valore limite precedente. Nella casella degli strumenti **Attività** espandere **Generale** e trascinare l'attività **Cerca** nell'area di progettazione della pipeline. Modificare il nome dell'attività in **LookupOldWaterMarkActivity**.

   ![Prima attività di ricerca: nome](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Passare alla scheda **Impostazioni** e fare clic su **+ Nuovo** in corrispondenza di **Source Dataset** (Set di dati di origine). In questo passaggio viene creato un set di dati per rappresentare i dati in **watermarktable**. Questa tabella contiene il limite precedente che è stato usato nella precedente operazione di copia.

6. Nella finestra **Nuovo set di dati** selezionare **Database SQL di Azure** e fare clic su **Continua**. Verrà aperta una nuova finestra per il set di dati.

7. Nella finestra **Imposta proprietà** per il set di dati immettere **WatermarkDataset** per **Nome**.

8. Per **Servizio collegato**, selezionare **Nuovo** e quindi seguire questa procedura:

    1. Immettere **AzureSqlDatabaseLinkedService** per **Nome**.
    2. Selezionare il server di Azure SQL per **Nome server**.
    3. Selezionare il **Nome del database** dall'elenco a discesa.
    4. Completare i campi **Nome utente** & **Password**.
    5. Per testare la connessione al database SQL di Azure, fare clic su **Test connessione**.
    6. Fare clic su **Fine**.
    7. Verificare che per **Servizio collegato** sia selezionato **AzureSqlDatabaseLinkedService**.

        ![Finestra New linked service (Nuovo servizio collegato)](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. Selezionare **Fine**.
9. Nella scheda **Connessione** selezionare **[dbo].[watermarktable]** per **Tabella**. Se si vuole visualizzare un'anteprima dei dati nella tabella, fare clic su **Anteprima dati**.

    ![Set di dati limite: impostazioni di connessione](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. Passare all'editor di pipeline facendo clic sulla scheda della pipeline in alto oppure sul nome della pipeline nella visualizzazione albero a sinistra. Nella finestra delle proprietà per l'attività **Cerca** verificare che nel campo **Source Dataset** (Set di dati di origine) sia selezionato **WatermarkDataset**.

11. Nella casella degli strumenti **Attività** espandere **Generale**, trascinare un'altra attività **Cerca** nell'area di progettazione della pipeline e impostare il nome su **LookupNewWaterMarkActivity** nella scheda **Generale** della finestra delle proprietà. Questa attività di ricerca recupera il nuovo valore limite dalla tabella con i dati di origine da copiare nella destinazione.

12. Nella finestra delle proprietà per la seconda attività **Cerca** passare alla scheda **Impostazioni** e fare clic su **Nuovo**. Si creerà un set di dati per fare riferimento alla tabella di origine contenente il nuovo valore limite, ossia il valore massimo di LastModifyTime.

13. Nella finestra **Nuovo set di dati** selezionare **Database SQL di Azure** e fare clic su **Continua**.
14. Nella finestra **Imposta proprietà** immettere **SourceDataset** per **Nome**. Selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**.
15. Selezionare **[dbo].[data_source_table]** per Tabella. Più avanti nell'esercitazione si specificherà una query su questo set di dati. La query avrà la precedenza rispetto alla tabella specificata in questo passaggio.
16. Selezionare **Fine**.
17. Passare all'editor di pipeline facendo clic sulla scheda della pipeline in alto oppure sul nome della pipeline nella visualizzazione albero a sinistra. Nella finestra delle proprietà per l'attività **Cerca** verificare che nel campo **Source Dataset** (Set di dati di origine) sia selezionato **SourceDataset**.
18. Selezionare **Query** per il campo **Use Query** (Usa query) e immettere la query seguente, con cui si seleziona solo il valore massimo di **LastModifytime** da **data_source_table**. Assicurarsi di aver selezionato anche **Solo prima riga**.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Seconda attività di ricerca: query](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. Nella casella degli strumenti **Attività** espandere **Move & Transform** (Sposta e trasforma), trascinare l'attività **Copia** dalla casella degli strumenti Attività e impostare il nome su **IncrementalCopyActivity**.

20. **Connettere entrambe le attività di ricerca all'attività di copia** trascinando il **pulsante verde** associato alle attività di ricerca sull'attività di copia. Rilasciare il pulsante del mouse quando il bordo dell'attività di copia diventa di colore blu.

    ![Connessione delle attività di ricerca all'attività di copia](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Selezionare l'attività **Copia** e verificare che le relative proprietà vengano visualizzate nella finestra **Proprietà**.

22. Passare alla scheda **Origine** nella finestra **Proprietà** e seguire questa procedura:

    1. Selezionare **SourceDataset** nel campo **Source Dataset** (Set di dati di origine).
    2. Selezionare **Query** per il campo **Use Query** (Usa query).
    3. Immettere la query SQL seguente nel campo **Query**.

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```

        ![Attività di copia: origine](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Passare alla scheda **Sink** e fare clic su **+ Nuovo** in corrispondenza del campo **Sink Dataset** (Set di dati sink).

24. In questa esercitazione, l'archivio di dati sink è un archivio BLOB di Azure. Selezionare quindi **Archiviazione BLOB di Azure** e fare clic su **Continua** nella finestra **Nuovo set di dati**.
25. Nella pagina **Select Format** (Seleziona formato) selezionare il tipo di formato dei dati e fare clic su **Continua**.
25. Nella finestra **Imposta proprietà** immettere **SinkDataset** per **Nome**. Per **Servizio collegato** selezionare **+ Nuovo**. In questo passaggio si crea una connessione all'**archivio BLOB di Azure**, ossia un servizio collegato.
26. Nella finestra **New Linked Service (Azure Blog Storage)** (Nuovo servizio collegato - Archiviazione BLOB di Azure), procedere come segue:

    1. Immettere **AzureStorageLinkedService** per **Nome**.
    2. Selezionare il proprio account di archiviazione di Azure per **Nome account di archiviazione**.
    3. Verificare la connessione e quindi fare clic su **Fine**.

27. Nella finestra **Imposta proprietà** verificare che per **Servizio collegato** sia selezionato **AzureStorageLinkedService**. Quindi selezionare **Fine**.
28. Passare alla scheda **Connessione** di SinkDataset e seguire questa procedura:
    1. Per il campo **Percorso file**, immettere **adftutorial/incrementalcopy**. **adftutorial** è il nome del contenitore BLOB e **incrementalcopy** è il nome della cartella. Questo frammento di codice presuppone che nell'archivio BLOB sia presente un contenitore BLOB denominato adftutorial. Creare il contenitore se non esiste oppure impostare il nome di un contenitore esistente. Se non esiste, la cartella di output **incrementalcopy** viene creata automaticamente da Azure Data Factory. È anche possibile usare il pulsante **Sfoglia** per **Percorso file** per passare a una cartella in un contenitore BLOB.
    2. Per la parte **File** del campo **Percorso file**, fare clic su **Aggiungi contenuto dinamico [AL+P]** e quindi immettere `@CONCAT('Incremental-', pipeline().RunId, '.txt')` nella finestra che viene aperta. Quindi selezionare **Fine**. Il nome file viene generato in modo dinamico usando l'espressione. Ogni esecuzione della pipeline ha un ID univoco, che viene usato dall'attività di copia per generare il nome file.

28. Passare all'editor di **pipeline** facendo clic sulla scheda della pipeline in alto oppure sul nome della pipeline nella visualizzazione albero a sinistra.
29. Nella casella degli strumenti **Attività** espandere **Generale** e trascinare l'attività **Stored procedure** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. **Connettere** l'output contrassegnato in verde (come operazione riuscita) dell'attività **Copia** all'attività **Stored procedure**.

24. Selezionare l'attività **Stored procedure** nella finestra di progettazione della pipeline e modificarne il nome in **StoredProceduretoWriteWatermarkActivity**.

25. Passare alla scheda **Account SQL** e selezionare **AzureSqlDatabaseLinkedService** per **Servizio collegato**.

26. Passare alla scheda **Stored procedure** e seguire questa procedura:

    1. In **Nome stored procedure** selezionare **usp_write_watermark**.
    2. Per specificare i valori dei parametri della stored procedure, fare clic su **Import parameter** (Importa parametro) e immettere i valori seguenti per i parametri:

        | Nome | Type | valore |
        | ---- | ---- | ----- |
        | LastModifiedtime | Datetime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | string | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Attività stored procedure: impostazioni della stored procedure](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Per convalidare le impostazioni della pipeline, fare clic su **Convalida** sulla barra degli strumenti. Verificare che non siano presenti errori di convalida. Per chiudere la finestra del **report di convalida della pipeline** fare clic su >>.   

28. Pubblicare le entità (servizi collegati, set di dati e pipeline) nel servizio Azure Data Factory selezionando il pulsante **Pubblica tutti**. Attendere fino alla visualizzazione del messaggio che informa che la pubblicazione è riuscita.


## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline
1. Fare clic su **Aggiungi trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso).

2. Nella finestra **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**.

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Verrà visualizzato lo stato dell'esecuzione della pipeline attivata dal trigger manuale. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco.

2. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul primo collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. È possibile tornare alla visualizzazione precedente facendo clic su **Pipeline** in alto. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco.


## <a name="review-the-results"></a>Esaminare i risultati
1. Connettersi all'account di archiviazione di Azure usando strumenti come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Verificare che sia stato creato un file di output nella cartella **incrementalcopy** del contenitore **adftutorial**.

    ![Primo file di output](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Aprire il file di output. Come si può notare, tutti i dati sono stati copiati da **data_source_table** al file BLOB.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Verificare il valore più recente da `watermarktable`. Si noterà che il valore limite è stato aggiornato.

    ```sql
    Select * from watermarktable
    ```

    Ecco l'output:

    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 |

## <a name="add-more-data-to-source"></a>Aggiungere altri dati all'origine

Inserire nuovi dati nel database SQL (archivio dell'origine dati).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
```

I dati aggiornati nel database SQL sono i seguenti:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Attivare un'altra esecuzione della pipeline
1. Passare alla scheda **Modifica**. Fare clic sulla pipeline nella visualizzazione albero, se non è aperta nella finestra di progettazione.

2. Fare clic su **Aggiungi trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso).


## <a name="monitor-the-second-pipeline-run"></a>Monitorare la seconda esecuzione della pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Verrà visualizzato lo stato dell'esecuzione della pipeline attivata dal trigger manuale. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco.

2. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sul primo collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. È possibile tornare alla visualizzazione precedente facendo clic su **Pipeline** in alto. Fare clic sul pulsante **Aggiorna** per aggiornare l'elenco.


## <a name="verify-the-second-output"></a>Verificare il secondo output

1. Nell'archivio BLOB è stato creato un altro file. In questa esercitazione, il nome del nuovo file è `Incremental-<GUID>.txt`. Aprendo il file si noteranno due righe di record.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Verificare il valore più recente da `watermarktable`. Si noterà che il valore limite è stato aggiornato di nuovo.

    ```sql
    Select * from watermarktable
    ```
    Output di esempio:

    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |



## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti:

> [!div class="checklist"]
> * Preparare l'archivio dati per l'archiviazione del valore limite.
> * Creare una data factory.
> * Creare servizi collegati.
> * Creare i set di dati di origine, sink e limite.
> * Creare una pipeline.
> * Eseguire la pipeline.
> * Monitorare l'esecuzione della pipeline.
> * Esaminare i risultati
> * Aggiungere altri dati all'origine.
> * Eseguire di nuovo la pipeline.
> * Monitorare la seconda esecuzione della pipeline
> * Esaminare i risultati della seconda esecuzione.

In questa esercitazione, la pipeline ha copiato dati da una singola tabella di un database SQL a un archivio BLOB. Passare all'esercitazione successiva per informazioni sulla copia di dati da più tabelle di un database di SQL Server locale a un database SQL.

> [!div class="nextstepaction"]
>[Caricare dati in modo incrementale da più tabelle in SQL Server al database SQL di Azure](tutorial-incremental-copy-multiple-tables-portal.md)
