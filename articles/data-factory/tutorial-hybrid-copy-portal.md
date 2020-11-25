---
title: Copiare dati da SQL Server ad Archiviazione BLOB con il portale di Azure
description: Informazioni su come copiare dati da un archivio dati locale al cloud usando un runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 172ebb5f5b7896b6b642c1fe6c5d01afb1dbf479
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553608"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Copiare dati da un database di SQL Server all'archiviazione BLOB di Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione si usa l'interfaccia utente di Azure Data Factory per creare una pipeline di data factory che copia i dati da un database di SQL Server all'archiviazione BLOB di Azure. Si crea e si usa un runtime di integrazione self-hosted, che sposta i dati tra gli archivi dati locali e cloud.

> [!NOTE]
> Questo articolo non offre una presentazione dettagliata di Data Factory. Per altre informazioni, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si segue questa procedura:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare servizi collegati per SQL Server e Archiviazione di Azure.
> * Creare set di dati per SQL Server e BLOB di Azure.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

## <a name="prerequisites"></a>Prerequisiti
### <a name="azure-subscription"></a>Sottoscrizione di Azure
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-roles"></a>Ruoli di Azure
Per creare istanze di Data Factory, all'account utente usato per accedere ad Azure deve essere assegnato un ruolo *collaboratore* o *proprietario* oppure l'account utente deve essere un *amministratore* della sottoscrizione di Azure.

Per visualizzare le autorizzazioni disponibili nella sottoscrizione, passare al portale di Azure. Nell'angolo superiore destro selezionare il nome utente e quindi **Autorizzazioni**. Se si accede a più sottoscrizioni, selezionare quella appropriata. Per istruzioni di esempio su come aggiungere un utente a un ruolo, vedere [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
In questa esercitazione si usa un database di SQL Server come archivio dati di *origine*. La pipeline nella data factory creata in questa esercitazione copia i dati da questo database di SQL Server (origine) all'archiviazione BLOB (sink). Si crea quindi una tabella denominata **emp** nel database di SQL Server e si inseriscono alcune voci di esempio nella tabella.

1. Avviare SQL Server Management Studio. Se non è già installato nel computer, passare a [Scaricare SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Connettersi all'istanza di SQL Server usando le credenziali.

1. Creare un database di esempio. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse su **Database** e scegliere **Nuovo database**.
1. Nella finestra **Nuovo database** immettere un nome per il database e fare clic su **OK**.

1. Per creare la tabella **emp** e inserirvi alcuni dati di esempio, eseguire questo script di query sul database. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse sul database creato e scegliere **Nuova query**.

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa esercitazione, come archivio dati di destinazione/sink si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB). Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-account-create.md). La pipeline nella data factory creata in questa esercitazione copia i dati dal database di SQL Server (origine) all'archiviazione BLOB (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa esercitazione si usano il nome e la chiave dell'account di archiviazione. Per recuperare il nome e la chiave dell'account di archiviazione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con nome utente e password di Azure.

1. Nel riquadro sinistro selezionare **Tutti i servizi**. Usare la parola chiave **Archiviazione** come filtro e quindi selezionare **Account di archiviazione**.

    ![Ricerca dell'account di archiviazione](media/doc-common-process/search-storage-account.png)

1. Nell'elenco degli account di archiviazione filtrare, se necessario, il proprio account di archiviazione. Selezionare quindi l'account di archiviazione.

1. Nella finestra **Account di archiviazione** selezionare **Chiavi di accesso**.

1. Nelle caselle **Nome account di archiviazione** e **key1** copiare i valori e incollarli nel Blocco note o in un altro editor per usarli in seguito nell'esercitazione.

#### <a name="create-the-adftutorial-container"></a>Creare il contenitore adftutorial
In questa sezione si crea un contenitore BLOB denominato **adftutorial** nell'archivio BLOB.

1. Nella finestra **Account di archiviazione** passare a **Panoramica** e quindi selezionare **Contenitori**.

    ![Selezionare l'opzione BLOB](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Nella finestra **Contenitori** selezionare **+ Contenitore** per creare un nuovo contenitore.

1. Nella finestra **Nuovo contenitore** immettere **adftutorial** in **Nome**. Selezionare quindi **Crea**.

1. Selezionare il contenitore **adftutorial** appena creato nell'elenco di contenitori.

1. Tenere aperta la finestra **Contenitore** per **adftutorial** Verrà usata per verificare l'output alla fine di questa esercitazione. Data Factory crea automaticamente la cartella di output in questo contenitore, quindi non è necessario crearne uno.

## <a name="create-a-data-factory"></a>Creare una data factory
In questo passaggio si crea una data factory e si avvia l'interfaccia utente di Data Factory per creare una pipeline nella data factory.

1. Aprire il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Nel menu sinistro selezionare **Crea una risorsa** > **Integrazione** > **Data factory**:

   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory deve essere *univoco a livello globale*. Se viene visualizzato il messaggio di errore seguente per il campo Nome, modificare il nome della data factory, ad esempio usando nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).

   ![Nome della nuova data factory](./media/doc-common-process/name-not-available-error.png)

1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
1. In **Gruppo di risorse** eseguire una di queste operazioni:

   - Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.

   - Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
        
     Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md).
1. In **Versione** selezionare **V2**.
1. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati da Data Factory possono trovarsi in altre aree.
1. Selezionare **Crea**.

1. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine:

    ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)
1. Selezionare il riquadro **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.


## <a name="create-a-pipeline"></a>Creare una pipeline

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). Verrà creata automaticamente una pipeline. La pipeline sarà visibile nella visualizzazione albero e verrà aperto il relativo editor.

   ![Pagina Attività iniziali](./media/doc-common-process/get-started-page.png)

1. Nel pannello Generale, in **Proprietà**, specificare **SQLServerToBlobPipeline** per **Nome**. Comprimere quindi il pannello facendo clic sull'icona Proprietà nell'angolo in alto a destra.

1. Nella casella degli strumenti **Attività** espandere **Move & Transform** (Sposta e trasforma). Trascinare l'attività **Copia** nell'area di progettazione della pipeline. Impostare il nome dell'attività su **CopySqlServerToAzureBlobActivity**.

1. Nella finestra **Proprietà** passare alla scheda **Origine** e selezionare **+ Nuovo**.

1. Nella finestra di dialogo **Nuovo set di dati** cercare **SQL Server**. Selezionare **SQL Server** e quindi **Continua**.
    ![Nuovo set di dati SQL Server](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. Nella finestra di dialogo **Imposta proprietà**, in **Nome**, immettere **SqlServerDataset**. In **Servizio collegato** selezionare **+ Nuovo**. In questo passaggio si crea una connessione all'archivio dati di origine (database SQL Server).

1. Nella finestra di dialogo **New Linked Service** (Nuovo servizio collegato) aggiungere **Nome** come **SqlServerLinkedService**. In **Connect via integration runtime** (Connetti tramite runtime di integrazione) selezionare **+ Nuovo**.  In questa sezione si crea un runtime di integrazione self-hosted e lo si associa a un computer locale con il database di SQL Server. Il runtime di integrazione self-hosted è il componente che copia i dati dal database di SQL Server presente nel computer all'archivio BLOB.

1. Nella finestra **Installazione di Integration Runtime** selezionare **Self-Hosted** e quindi **Continua**.

1. In Nome immettere **TutorialIntegrationRuntime**. Selezionare quindi **Crea**.

1. In Impostazioni selezionare **Fare clic qui per avviare l'installazione rapida per questo computer**. Questa azione installa il runtime di integrazione nel computer e lo registra in Data Factory. In alternativa è possibile usare l'opzione di installazione manuale per scaricare il file di installazione, eseguirlo e usare la chiave per registrare il runtime di integrazione.
    ![Installazione di Integration Runtime](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. Nella finestra **Installazione rapida di Integration Runtime (self-hosted)** selezionare **Chiudi** al termine del processo.

    ![Installazione rapida di Integration Runtime (self-hosted)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Nella finestra di dialogo **Nuovo servizio collegato (SQL Server)** verificare che nel campo **Connetti tramite il runtime di integrazione** sia selezionato **TutorialIntegrationRuntime**. Seguire quindi questa procedura:

    a. In **Nome** immettere **SqlServerLinkedService**.

    b. In **Nome server** immettere il nome dell'istanza di SQL Server.

    c. In **Nome database** immettere il nome del database con la tabella **emp**.

    d. In **Tipo di autenticazione** selezionare il tipo di autenticazione appropriato che dovrà essere usato da Data Factory per la connessione al database di SQL Server.

    e. In **Nome utente** e **Password** immettere il nome utente e la password. Usare *mydomain\\myuser* come nome utente, se necessario.

    f. Selezionare **Test connessione**. Eseguire questo passaggio per verificare che Data Factory possa connettersi al database SQL Server usando il runtime di integrazione self-hosted creato.

    g. Selezionare **Crea** per salvare il servizio collegato.
 
    ![Nuovo servizio collegato (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. Al termine della creazione del servizio collegato verrà visualizzata di nuovo la pagina **Imposta proprietà** per il set di dati di SQL Server. Eseguire questa procedura:

    a. In **Servizio collegato** verificare che venga visualizzato **SqlServerLinkedService**.

    b. In **Nome tabella** selezionare **[dbo].[emp]** .
    
    c. Selezionare **OK**.

1. Passare alla scheda con **SQLServerToBlobPipeline** oppure selezionare **SQLServerToBlobPipeline** nella visualizzazione albero.

1. Passare alla scheda **Sink** nella parte inferiore della finestra **Proprietà** e selezionare **+ Nuovo**.

1. Nella finestra di dialogo **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure**. Selezionare quindi **Continua**.

1. Nella finestra di dialogo **Select Format** (Seleziona formato) scegliere il tipo di formato dei dati. Selezionare quindi **Continua**.

    ![Selezione del formato dati](./media/doc-common-process/select-data-format.png)

1. Nella finestra di dialogo **Imposta proprietà** immettere **AzureBlobDataset** come nome. Selezionare **+ Nuovo** accanto alla casella di testo **Servizio collegato**.

1. Nella finestra **New Linked Service (Azure Blob Storage)** (Nuovo servizio collegato - Archiviazione BLOB di Azure) immettere **AzureStorageLinkedService** come nome e selezionare l'account di archiviazione nell'elenco **Nome account di archiviazione**. Testare la connessione e quindi selezionare **Crea** per distribuire il servizio collegato.

1. Al termine della creazione del servizio collegato verrà visualizzata di nuovo la pagina **Imposta proprietà**. Selezionare **OK**.

1. Aprire il set di dati sink. Nella scheda **Connessione** seguire questa procedura:

    a. In **Servizio collegato** verificare che sia selezionato **AzureStorageLinkedService**.

    b. Nella parte **Contenitore/directory** di **Percorso file** immettere **adftutorial/fromonprem**. Se la cartella di output non esiste nel contenitore adftutorial, verrà creata automaticamente da Data Factory.

    c. Nella parte **File** selezionare **Aggiungi contenuto dinamico**.
    ![espressione dinamica per la risoluzione del nome file](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Aggiungere `@CONCAT(pipeline().RunId, '.txt')`, quindi selezionare **Fine**. Questa azione rinomina il file in PipelineRunID.txt.

1. Passare alla scheda con la pipeline aperta oppure selezionare la pipeline nella visualizzazione albero. In **Sink Dataset** (Set di dati sink) verificare che sia selezionato **AzureBlobDataset**.

1. Per convalidare le impostazioni della pipeline, selezionare **Convalida** sulla barra degli strumenti della pipeline. Per chiudere l'**output della convalida della pipe**, selezionare l'icona **>>** .
    ![convalidare la pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Per pubblicare le entità create in Data Factory, selezionare **Pubblica tutti**.

1. Attendere fino alla visualizzazione del messaggio popup **La pubblicazione è stata completata**. Per verificare lo stato della pubblicazione, selezionare il collegamento **Mostra notifiche** nella parte superiore della finestra. Per chiudere la finestra delle notifiche, selezionare **Chiudi**.


## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline
Selezionare **Add Trigger** (Aggiungi trigger) sulla barra degli strumenti della pipeline e quindi **Trigger Now** (Attiva adesso).

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio**. Verrà visualizzata la pipeline attivata manualmente nel passaggio precedente.

1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **SQLServerToBlobPipeline** in *NOME DELLA PIPELINE*. 
    ![Monitorare le esecuzioni di pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. Per visualizzare informazioni dettagliate sull'operazione di copia, selezionare il collegamento Dettagli (icona a forma di occhiali) nella pagina **Esecuzioni attività**. Per tornare alla visualizzazione Esecuzioni della pipeline, selezionare **Esecuzioni della pipeline** in alto.

## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output denominata *fromonprem* nel contenitore BLOB `adftutorial`. Assicurarsi che nella cartella di output sia presente il file *[pipeline().RunId].txt*.


## <a name="next-steps"></a>Passaggi successivi
La pipeline di questo esempio copia i dati da una posizione a un'altra in un archivio BLOB. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare servizi collegati per SQL Server e Archiviazione.
> * Creare i set di dati per SQL Server e l'archivio BLOB.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

Per un elenco degli archivi dati supportati da Data Factory, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Per informazioni sulla copia di dati in blocco da un'origine a una destinazione, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
>[Copiare dati in blocco](tutorial-bulk-copy-portal.md)