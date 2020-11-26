---
title: Copiare i dati con lo strumento Copia dati di Azure
description: Creare un'istanza di Azure Data Factory e quindi usare lo strumento Copia dati per copiare i dati da una posizione di archiviazione BLOB di Azure a un'altra.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 282917a6bbf7edb962a87ad87810adde56206d97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013514"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Guida introduttiva: Usare lo strumento Copia dati per copiare i dati

> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione corrente](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa guida introduttiva si userà il portale di Azure per creare una data factory. Viene quindi usato lo strumento Copia dati per creare una pipeline che copia i dati da una cartella dell'archivio BLOB di Azure a un'altra cartella. 

> [!NOTE]
> Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md) prima di seguire la guida introduttiva. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare il Web browser **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Nel menu del portale di Azure selezionare **Crea una risorsa** > **Integrazione** > **Data factory**:

    ![Creazione di una nuova data factory](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
 
   Il nome dell'istanza di Azure Data Factory deve essere *univoco globale*. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, **&lt;nomeutente&gt;ADFTutorialDataFactory**, e provare di nuovo a crearla. Per le regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
   ![Errore quando un nome non è disponibile](./media/doc-common-process/name-not-available-error.png)
1. Per **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
1. In **Gruppo di risorse** eseguire una di queste operazioni:
     
   - Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco. 
   - Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.   
         
   Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
1. Per **Versione** selezionare **V2**.
1. Per **Località** selezionare la località per la data factory.

   L'elenco mostra solo le località supportate da Data Factory e in cui verranno archiviati i metadati di Azure Data Factory. Gli archivi dati associati (come Archiviazione di Azure e il database SQL di Azure) e le risorse di calcolo (come Azure HDInsight) usati da Data Factory possono essere eseguiti in altre aree.

1. Selezionare **Create** (Crea).

1. Al termine della creazione verrà visualizzata la pagina **Data factory**. Selezionare il riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Azure Data Factory in una scheda separata.
   
   ![Home page della data factory, con il riquadro "Crea e monitora"](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Avviare lo strumento Copia dati

1. Nella pagina **Attività iniziali** selezionare il riquadro **Copia dati** per avviare lo strumento Copia dati. 

   ![Riquadro "Copia dati"](./media/doc-common-process/get-started-page.png)

1. Nella pagina **Proprietà** dello strumento Copia dati è possibile specificare un nome per la pipeline e la relativa descrizione, quindi selezionare **Avanti**. 

   ![Pagina "Proprietà"](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Nella pagina **Archivio dati di origine** completare la procedura seguente:

    a. Fare clic su **+ Crea nuova connessione** per aggiungere una connessione.

    b. Selezionare il tipo di servizio collegato da creare per la connessione di origine. In questa esercitazione si userà **Archiviazione BLOB di Azure**. Selezionare tale servizio nella raccolta e quindi **Continua**.
    
    ![Selezionare il servizio di archiviazione BLOB](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Nella pagina **New Linked Service (Azure BLOB Storage)** (Nuovo servizio collegato - Archiviazione BLOB di Azure) specificare un nome per il servizio collegato. Selezionare l'account di archiviazione nell'elenco **Nome account di archiviazione**, testare la connessione e quindi selezionare **Crea**. 

    ![Configurare l'account di archiviazione BLOB di Azure](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Selezionare il servizio collegato appena creato come origine, quindi fare clic su **Avanti**.


1. Nella pagina **Choose the input file or folder** (Scegliere il file o la cartella di input) completare questa procedura:

   a. Fare clic su **Sfoglia** per passare alla cartella **adftutorial/input**, selezionare il file **emp.txt**, quindi fare clic su **Scegli**. 

   d. Selezionare l'opzione **Binary copy** (Copia binaria) per copiare il file così com'è, quindi fare clic su **Avanti**. 

   ![Pagina "Choose the input file or folder" (Scegliere il file o la cartella di input)](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Nella pagina **Destination data store** (Archivio dati di destinazione) selezionare il servizio collegato **Archiviazione BLOB di Azure** appena creato e quindi fare clic su **Avanti**. 

1. Nella pagina **Choose the output file or folder** (Scegliere il file o la cartella di output) immettere **adftutorial/output** come percorso della cartella, quindi fare clic su **Avanti**. 

   ![Pagina "Choose the output file or folder" (Scegliere il file o la cartella di output)](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Nella pagina **Impostazioni** selezionare **Avanti** per usare le configurazioni predefinite. 

1. Nella pagina **Riepilogo** verificare tutte le impostazioni e selezionare **Avanti**. 

1. Nella pagina **Distribuzione completata** selezionare **Monitoraggio** per monitorare la pipeline creata. 

    ![Pagina "Distribuzione completata"](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. L'applicazione passa alla scheda **Monitoraggio**. In questa scheda viene visualizzato lo stato della pipeline. Selezionare **Aggiorna** per aggiornare l'elenco. Fare clic sul collegamento in **NOME PIPELINE** per visualizzare i dettagli dell'esecuzione dell'attività o eseguire di nuovo la pipeline. 
   
    ![Aggiornare la pipeline](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Nella pagina Esecuzioni attività selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Nome attività** per visualizzare informazioni dettagliate sull'operazione di copia. Per informazioni dettagliate sulle proprietà, vedere [Panoramica dell'attività Copia](copy-activity-overview.md). 

1. Per tornare alla visualizzazione Esecuzioni della pipeline, selezionare il collegamento **Tutte le esecuzioni della pipeline** nel menu di navigazione. Per aggiornare la visualizzazione, selezionare **Aggiorna**. 

1. Verificare che il file **emp.txt** sia stato creato nella cartella **output** del contenitore **adftutorial**. Se la cartella output non esiste, il servizio Data Factory la crea automaticamente. 

1. Passare alla scheda **Autore** sopra la scheda **Monitoraggio** nel pannello sinistro, per poter modificare i servizi collegati, i set di dati e le pipeline. Per informazioni sulla modifica di questi elementi nell'interfaccia utente di Data Factory, vedere [Creare una data factory con il portale di Azure](quickstart-create-data-factory-portal.md).

    ![Selezionare la scheda Crea](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra nell'archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, vedere le [esercitazioni](tutorial-copy-data-portal.md). 
