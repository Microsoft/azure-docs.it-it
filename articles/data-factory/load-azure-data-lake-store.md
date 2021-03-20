---
title: Caricare i dati in Azure Data Lake Storage Gen1
description: Usare Azure Data Factory per copiare dati in Azure Data Lake Storage Gen1
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 1e75fc65b4afea9dc895580e4711e0050cb7c64f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716415"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Caricare dati in Azure Data Lake Storage Gen1 tramite Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (in precedenza noto come Azure Data Lake Store) è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Data Lake Storage Gen1 consente di acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento. I dati vengono acquisiti in una singola posizione per le analisi esplorative e operative.

Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio per popolare il lake con i dati dal sistema esistente e risparmiare tempo durante la compilazione di soluzioni di analisi.

Azure Data Factory offre i vantaggi seguenti per il caricamento di dati in Azure Data Lake Storage Gen1:

* **Facilità di configurazione**: procedura guidata intuitiva in 5 passaggi, senza necessità di script.
* **Supporto completo per archivi dati**: supporto integrato per una vasta gamma di archivi dati locali e basati su cloud. Per un elenco dettagliato, vedere la tabella degli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
* **Sicurezza e conformità**: i dati vengono trasferiti tramite HTTPS o ExpressRoute. La presenza di un servizio globale garantisce che i dati non oltrepassino mai il confine geografico.
* **Prestazioni elevate**: fino a 1 GB/s di velocità di caricamento dati in Azure Data Lake Storage Gen1. Per informazioni, vedere [Prestazioni dell'attività di copia](copy-activity-performance.md).

Questo articolo illustra come usare lo strumento Copia dati di Data Factory per _caricare dati da Amazon S3 in Azure Data Lake Storage Gen1_. È possibile seguire una procedura simile a quella usata per copiare dati da altri tipi di archivi dati.

> [!NOTE]
> Per altre informazioni, vedere [Copiare dati da e in Azure Data Lake Storage Gen1 usando Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Account Data Lake Storage Gen1: se non si dispone di un account Azure Data Lake Storage Gen1, vedere le istruzioni in [Creare un account Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: In questo articolo viene illustrato come copiare i dati da Amazon S3. È possibile usare altri archivi dati seguendo una procedura simile.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory**:
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** specificare i valori per i campi mostrati nell'immagine seguente: 
      
   ![Pagina Nuova data factory](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Name**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Il nome \"LoadADLSG1Demo\" per la data factory non è disponibile", immettere un nome diverso per la data factory. Ad esempio, è possibile usare il nome _**nomeutente**_**ADFTutorialDataFactory**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. Questi archivi dati includono Data Lake Storage Gen1, Archiviazione di Azure, il database SQL di Azure e così via.

3. Selezionare **Crea**.
4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente: 
   
   ![Home page di Data factory](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="load-data-into-data-lake-storage-gen1"></a>Inserire i dati in Data Lake Storage Gen1

1. Nella pagina **Attività iniziali** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati: 

   ![Riquadro dello strumento Copia dati](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Nella pagina **Proprietà** specificare **CopyFromAmazonS3ToADLS** per il campo **Nome attività**, quindi selezionare **Avanti**:

    ![Pagina Proprietà](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Nella pagina **Archivio dati di origine** fare clic su **+ Crea nuova connessione**:

    ![Pagina Archivio dati di origine](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Selezionare **Amazon S3** e quindi **Continua**
    
    ![Pagina Archivio dati di origine s3](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Nella pagina **Specificare la connessione ad Amazon S3**, effettuare i passaggi seguenti: 
   1. Specificare il valore **ID della chiave di accesso**.
   2. Specificare il valore **Chiave di accesso segreta**.
   3. Selezionare **Fine**.
   
      ![Screenshot mostra il nuovo riquadro servizio collegato in cui è possibile immettere i valori.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Si noterà una nuova connessione. Selezionare **Avanti**.
   
   ![Screenshot che mostra la nuova connessione.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) passare alla cartella e al file da copiare. Selezionare la cartella o il file, selezionare **Scegli**, quindi selezionare **Avanti**:

    ![Scegliere il file o la cartella di input](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Scegliere il comportamento di copia selezionando le opzioni **Copia i file in modo ricorsivo** e **Copia binaria** (copia i file così come sono). Selezionare **Avanti**:

    ![Screenshot mostra il scegliere il file o la cartella di input in cui è possibile selezionare copia file in modo ricorsivo e copia binaria.](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Nella pagina **Archivio dati di destinazione** fare clic su **+ Crea nuova connessione**, quindi selezionare **Data Lake Storage Gen1** e **Continua**:

    ![Pagina dell'archivio dati di destinazione](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Nella pagina **Nuovo servizio collegato (Azure Data Lake Storage Gen1)** seguire questa procedura: 

   1. Selezionare l'account Data Lake Storage Gen1 per il **nome dell'account Data Lake Storage**.
   2. Specificare il **Tenant** e scegliere Fine.
   3. Selezionare **Avanti**.
   
   > [!IMPORTANT]
   > In questa procedura dettagliata si usa un'identità gestita delle risorse di Azure per autenticare l'account Data Lake Storage Gen1. Assicurarsi di concedere le autorizzazioni appropriate all'entità del servizio gestita in Data Lake Storage Gen1 seguendo [queste istruzioni](connector-azure-data-lake-store.md#managed-identity).
   
   ![Specificare un account Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) immettere **copyfroms3** come nome della cartella di output, quindi selezionare **Avanti**: 

    ![Screenshot indica il percorso della cartella immesso.](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Nella pagina **Impostazioni** selezionare **Avanti**:

    ![Pagina Impostazioni](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Nella pagina **Riepilogo** esaminare le impostazioni e quindi selezionare **Avanti**:

    ![Pagina Riepilogo](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline (attività):

    ![Pagina Distribuzione](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline:

    ![Monitorare le esecuzioni di pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco. 

    ![Monitorare le esecuzioni delle attività](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** in **Azioni** nella visualizzazione di monitoraggio delle attività. È possibile monitorare dettagli come il volume dei dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate:

    ![Monitorare i dettagli di esecuzione delle attività](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verificare che i dati vengano copiati nell'account Data Lake Storage Gen1: 

    ![Verificare l'output di Data Lake Storage Gen1](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per altre informazioni sul supporto di Azure Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Connettore Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
