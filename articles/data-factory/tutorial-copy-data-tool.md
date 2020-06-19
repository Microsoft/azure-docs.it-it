---
title: Copiare i dati da Archiviazione BLOB di Azure a SQL con lo strumento Copia dati
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati per copiare i dati da un archivio BLOB di Azure a un database SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/08/2020
ms.openlocfilehash: 2165efd6b522d3809dba285cf2c3050fc50b2d28
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660973"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Copiare dati da un archivio BLOB di Azure a un database SQL con lo strumento Copia dati

> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory usato:"]
> * [Versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione corrente](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione si usa il portale di Azure per creare una data factory. Viene quindi usato lo strumento Copia dati per creare una pipeline che copia i dati da un archivio BLOB di Azure a un database SQL.

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si segue questa procedura:
> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: Usare un archivio BLOB come archivio dati di _origine_. Se non è disponibile un account di archiviazione di Azure, vedere le istruzioni fornite in [Creare un account di archiviazione](../storage/common/storage-account-create.md).
* **Database SQL di Azure**: Usare un database SQL come archivio dati _sink_. Se non si ha un database SQL, vedere le istruzioni in [Creare un database SQL](../azure-sql/database/single-database-create-quickstart.md).

### <a name="create-a-blob-and-a-sql-table"></a>Creare un BLOB e una tabella SQL

Preparare l'archivio BLOB e il database SQL per l'esercitazione seguendo questa procedura.

#### <a name="create-a-source-blob"></a>Creare un BLOB di origine

1. Avviare il **Blocco note**. Copiare il testo seguente e salvarlo in un file denominato **inputEmp.txt** sul disco:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Creare un contenitore denominato **adfv2tutorial** e caricare il file inputEmp.txt nel contenitore. Per eseguire queste attività è possibile usare il portale di Azure o vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Creare una tabella SQL sink

1. Usare lo script SQL seguente per creare una tabella denominata **dbo.emp** nel database SQL:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Consentire ai servizi di Azure di accedere a SQL Server. Verificare che per il server che esegue il database SQL sia abilitata l'impostazione **Consenti ai servizi di Azure di accedere al server**, che consente a Data Factory di scrivere dati nell'istanza di database. Per verificare e attivare l'impostazione, passare al server SQL logico > Sicurezza > Firewall e reti virtuali, quindi impostare l'opzione **Consenti ai servizi di Azure di accedere al server** su **SÌ**.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory**:

    ![Creazione di una nuova data factory](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

    Il nome della data factory deve essere _univoco a livello globale_. Potrebbe essere visualizzato il messaggio di errore seguente:

    ![Messaggio di errore per nuova data factory](./media/doc-common-process/name-not-available-error.png)

    Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
1. Selezionare la **sottoscrizione** di Azure in cui creare la nuova data factory.
1. In **Gruppo di risorse** eseguire una di queste operazioni:

    a. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.
    
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md).

1. In **Versione** selezionare la versione **V2**.
1. In **Località** selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dalla data factory possono trovarsi in altre località e aree.
1. Selezionare **Crea**.

1. Al termine della creazione verrà visualizzata la home page **Data factory**.

    ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)
1. Per avviare l'interfaccia utente di Azure Data Factory in una scheda separata, selezionare il riquadro **Crea e monitora**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nella pagina **Attività iniziali** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati.

    ![Riquadro dello strumento Copia dati](./media/doc-common-process/get-started-page.png)
1. Nella pagina **Proprietà** immettere **CopyFromBlobToSqlPipeline** in **Nome attività**. Fare quindi clic su **Avanti**. L'interfaccia utente di Data Factory crea una pipeline con il nome di attività specificato.
    ![Creare una pipeline](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. Fare clic su **+ Crea nuova connessione** per aggiungere una connessione

    b. Selezionare **Archiviazione BLOB di Azure** nella raccolta e quindi **Continua**.

    c. Nella pagina **Nuovo servizio collegato** selezionare la sottoscrizione di Azure e l'account di archiviazione nell'elenco **Nome account di archiviazione**. Testare la connessione e quindi selezionare **Crea**.

    d. Selezionare il servizio collegato appena creato come origine, quindi fare clic su **Avanti**.

    ![Selezionare il servizio collegato di origine](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) completare questa procedura:

    a. Fare clic su **Sfoglia** per passare alla cartella **adfv2tutorial/input**, selezionare il file **inputEmp.txt** e quindi fare clic su **Scegli**.

    b. Fare clic su **Avanti** per andare al passaggio successivo.

1. Nella pagina **Impostazioni del formato di file** abilitare la casella di controllo *Prima riga come intestazione*. Si noti che lo strumento rileva automaticamente i delimitatori di colonna e di riga. Selezionare **Avanti**. In questa pagina è anche possibile visualizzare l'anteprima dei dati e lo schema dei dati di input.

    ![Impostazioni sul formato del file](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Nella pagina **archivio dati di destinazione** completare la procedura seguente:

    a. Fare clic su **+ Crea nuova connessione** per aggiungere una connessione

    b. Selezionare **Database SQL di Azure** nella raccolta e quindi **Continua**.

    c. Nella pagina **Nuovo servizio collegato** selezionare il nome del server e il nome del database dall'elenco a discesa, specificare il nome utente e la password e quindi selezionare **Crea**.

    ![Configurare il database SQL di Azure](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selezionare il servizio collegato appena creato come sink e quindi fare clic su **Avanti**.

1. Nella pagina **Mapping tabella** selezionare la tabella **[dbo].[emp]** e quindi **Avanti**.

1. Nella pagina **Mapping colonne** si noti che la seconda e la terza colonna del file di input sono mappate alle colonne **FirstName** e **LastName** della tabella **emp**. Modificare il mapping per verificare che non siano presenti errori, quindi selezionare **Avanti**.

    ![Pagina Mapping colonne](./media/tutorial-copy-data-tool/column-mapping.png)

1. Nella pagina **Impostazioni** selezionare **Avanti**.

1. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**.

1. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).

    ![Monitorare la pipeline](./media/tutorial-copy-data-tool/monitor-pipeline.png)
    
1. Nella pagina Esecuzioni della pipeline selezionare **Aggiorna** per aggiornare l'elenco. Fare clic sul collegamento in **NOME PIPELINE** per visualizzare i dettagli dell'esecuzione dell'attività o eseguire di nuovo la pipeline. 
    ![Esecuzione della pipeline](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Nella pagina Esecuzioni attività selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Nome attività** per visualizzare informazioni dettagliate sull'operazione di copia. Per tornare alla visualizzazione Esecuzioni della pipeline, selezionare il collegamento **Tutte le esecuzioni della pipeline** nel menu di navigazione. Per aggiornare la visualizzazione, selezionare **Aggiorna**.

    ![Monitorare le esecuzioni delle attività](./media/tutorial-copy-data-tool/activity-monitoring.png)

1. Verificare che i dati siano inseriti nella tabella **dbo.emp** del database SQL.

1. Selezionare la scheda **Autore** a sinistra per passare alla modalità di modifica. L'editor consente di aggiornare i servizi collegati, i set di dati e le pipeline che sono stati creati tramite lo strumento. Per informazioni dettagliate sulla modifica di queste entità nell'interfaccia utente di Data Factory, vedere [la versione del portale di Azure di questa esercitazione](tutorial-copy-data-portal.md).

    ![Selezionare la scheda Crea](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Passaggi successivi
La pipeline di questo esempio copia i dati da un archivio BLOB a un database SQL. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

Passare all'esercitazione successiva per informazioni sulla copia di dati dall'ambiente locale al cloud:

>[!div class="nextstepaction"]
>[Copiare dati dall'ambiente locale al cloud](tutorial-hybrid-copy-data-tool.md)
