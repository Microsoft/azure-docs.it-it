---
title: Strumento dati per copiare i file nuovi e aggiornati in modo incrementale
description: Creare una data factory di Azure e quindi usare lo strumento Copia dati per caricare in modo incrementale i nuovi file in base a LastModifiedDate.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399479"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Copia i file nuovi e modificati in modo incrementale in base a LastModifiedDate utilizzando lo strumento Copia dati

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione si utilizzerà il portale di Azure per creare una data factory. Si userà quindi lo strumento Copia dati per creare una pipeline che consente di copiare in modo incrementale solo i file nuovi e modificati, dall'archiviazione BLOB di Azure all'archiviazione BLOB di Azure. USA `LastModifiedDate` per determinare i file da copiare.

Al termine della procedura, Azure Data Factory analizzerà tutti i file nell'archivio di origine, applicherà il filtro di file `LastModifiedDate`in base a e copierà nell'archivio di destinazione solo i file nuovi o aggiornati dall'ultima volta. Si noti che se Data Factory esegue l'analisi di un numero elevato di file, è prevedibile che si verifichino durate lunghe. L'analisi dei file richiede molto tempo, anche in caso di riduzione della quantità di dati copiati.

> [!NOTE]
> Se non si ha familiarità con Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si completeranno le attività seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Usare lo strumento Copia dati per creare una pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: usare l'archiviazione BLOB per gli archivi dati di origine e sink. Se non si ha un account di archiviazione di Azure, seguire le istruzioni riportate in [creare un account di archiviazione](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Creare due contenitori nell'archivio BLOB

Preparare l'archiviazione BLOB per l'esercitazione completando i seguenti passaggi:

1. Creare un contenitore denominato **source**. Per eseguire questa attività, è possibile usare vari strumenti, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).

2. Creare un contenitore denominato **Destination**.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel riquadro a sinistra selezionare **Crea risorsa**. Selezionare **Analytics** > **Data Factory**di analisi:

   ![Selezionare Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory deve essere univoco a livello globale. È possibile che venga visualizzato il messaggio di errore seguente:

   ![Messaggio di errore nome non disponibile](./media/doc-common-process/name-not-available-error.png)

   Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
3. In **sottoscrizione**selezionare la sottoscrizione di Azure in cui verrà creata la nuova data factory.
4. In **gruppo di risorse**eseguire una di queste operazioni:

    * Selezionare **Usa esistente** e quindi selezionare un gruppo di risorse esistente nell'elenco.

    * Selezionare **Crea nuovo** e quindi immettere un nome per il gruppo di risorse.
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md).

5. In **Versione** selezionare **V2**.
6. In **percorso**selezionare il percorso per il data factory. Nell'elenco vengono visualizzati solo i percorsi supportati. Gli archivi dati (ad esempio, archiviazione di Azure e il database SQL di Azure) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dal data factory possono trovarsi in altre località e aree.
8. Selezionare **Create** (Crea).
9. Dopo la creazione del data factory, viene visualizzata la home page data factory.
10. Per aprire l'interfaccia utente di Azure Data Factory in una scheda separata, selezionare il riquadro **autore & monitoraggio** :

    ![Home page di Data factory](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Usare lo strumento Copia dati per creare una pipeline

1. Nella pagina attività **iniziali** selezionare il riquadro **copia dati** per aprire lo strumento copia dati:

   ![Riquadro Copia dati](./media/doc-common-process/get-started-page.png)

2. Nella pagina **Proprietà** seguire questa procedura:

    a. In **nome attività**immettere **DeltaCopyFromBlobPipeline**.

    b. In **cadenza attività o pianificazione attività**selezionare **Esegui regolarmente in base alla pianificazione**.

    c. In **tipo di trigger**selezionare **finestra a cascata**.

    d. In **ricorrenza**immettere **15 minuto/i**.

    e. Selezionare **Avanti**.

    Data Factory crea una pipeline con il nome dell'attività specificato.

    ![Pagina Copia proprietà dati](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Nella pagina **archivio dati di origine** completare i passaggi seguenti:

    a. Selezionare **Crea nuova connessione** per aggiungere una connessione.

    b. Selezionare **archiviazione BLOB di Azure** dalla raccolta e quindi selezionare **continue (continua**):

    ![Seleziona archiviazione Blog di Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Nella pagina **nuovo servizio collegato (archiviazione BLOB di Azure)** selezionare l'account di archiviazione dall'elenco **nome account di archiviazione** . Testare la connessione e quindi selezionare **Crea**.

    d. Selezionare il nuovo servizio collegato e quindi fare clic su **Avanti**:

   ![Selezionare il nuovo servizio collegato](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) completare questa procedura:

    a. Cercare e selezionare la cartella di **origine** , quindi **scegliere Scegli**.

    ![Scegliere il file o la cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. In **comportamento caricamento file**selezionare **caricamento incrementale: LastModifiedDate**.

    c. Selezionare **copia binaria** , quindi selezionare **Avanti**:

     ![Scegliere la pagina file o cartella di input](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Nella pagina **archivio dati di destinazione** selezionare il servizio **AzureBlobStorage** creato. Si tratta dello stesso account di archiviazione dell'archivio dati di origine. Fare quindi clic su **Avanti**.

6. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) completare questa procedura:

    a. Cercare e selezionare la cartella di **destinazione** , quindi **scegliere Scegli**:

    ![Scegliere la pagina file o cartella di output](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selezionare **Avanti**.

7. Nella pagina **Impostazioni** selezionare **Avanti**.

8. Nella pagina **Riepilogo** verificare le impostazioni e quindi fare clic su **Avanti**.

    ![Pagina Riepilogo](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività).

    ![Pagina Distribuzione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. L'applicazione passa alla scheda **monitoraggio** . Viene visualizzato lo stato della pipeline. Selezionare **Aggiorna** per aggiornare l'elenco. Selezionare il collegamento in **nome pipeline** per visualizzare i dettagli dell'esecuzione dell'attività o per eseguire di nuovo la pipeline.

    ![Aggiornare l'elenco e visualizzare i dettagli dell'esecuzione dell'attività](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Nella pipeline è presente una sola attività (attività di copia), quindi viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona degli occhiali) nella colonna **nome attività** . Per informazioni dettagliate sulle proprietà, vedere [Cenni preliminari sull'attività di copia](copy-activity-overview.md).

    ![Attività di copia nella pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Poiché non sono presenti file nel contenitore di origine nell'account di archiviazione BLOB, non verranno visualizzati i file copiati nel contenitore di destinazione nell'account:

    ![Nessun file nel contenitore di origine o nel contenitore di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Creare un file di testo vuoto e denominarlo **file1. txt**. Caricare questo file di testo nel contenitore di origine nell'account di archiviazione. È possibile utilizzare vari strumenti per eseguire queste attività, ad esempio [Azure Storage Explorer](https://storageexplorer.com/).

    ![Creare File1. txt e caricarlo nel contenitore di origine](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Per tornare alla visualizzazione delle **esecuzioni di pipeline** , selezionare **tutte le esecuzioni di pipeline**e attendere che la stessa pipeline venga nuovamente attivata automaticamente.  

    ![Seleziona tutte le esecuzioni di pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Al termine della seconda esecuzione della pipeline, attenersi alla stessa procedura descritta in precedenza per esaminare i dettagli dell'esecuzione dell'attività.  

    Si noterà che un file (file1. txt) è stato copiato dal contenitore di origine al contenitore di destinazione dell'account di archiviazione BLOB:

    ![file1. txt è stato copiato dal contenitore di origine al contenitore di destinazione](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Creare un altro file di testo vuoto e denominarlo **file2. txt**. Caricare questo file di testo nel contenitore di origine nell'account di archiviazione BLOB.

16. Ripetere i passaggi 13 e 14 per il secondo file di testo. Si noterà che durante l'esecuzione della pipeline è stato copiato solo il nuovo file (file2. txt) dal contenitore di origine al contenitore di destinazione dell'account di archiviazione.  

    È inoltre possibile verificare che sia stato copiato un solo file utilizzando [Azure Storage Explorer](https://storageexplorer.com/) per eseguire la scansione dei file:

    ![Analizza i file usando Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come trasformare i dati usando un cluster Apache Spark in Azure, vedere l'esercitazione seguente:

> [!div class="nextstepaction"]
>[Trasformare i dati nel cloud usando un cluster Apache Spark](tutorial-transform-data-spark-portal.md)
