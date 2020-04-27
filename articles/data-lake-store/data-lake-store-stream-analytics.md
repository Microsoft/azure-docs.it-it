---
title: Trasmettere i dati da Analisi di flusso ad Azure Data Lake Storage Gen1 | Microsoft Docs
description: Usare Analisi di flusso di Azure per trasmettere i dati in Azure Data Lake Storage Gen1
services: data-lake-store,stream-analytics
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: d3dbacd58b3bda3fbf8ee8ad5f175eccc2cb2a24
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60194945"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Trasmettere i dati dal BLOB del servizio di archiviazione di Azure a Data Lake Storage Gen1 usando Analisi di flusso di Azure
In questo articolo viene descritto come usare Azure Data Lake Storage Gen1 come output per un processo di Analisi di flusso di Azure. Questo articolo illustra uno scenario semplice in cui i dati vengono letti da un BLOB del servizio di archiviazione di Azure (input) e scritti in Data Lake Storage Gen1 (output).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Account di archiviazione di Azure**. Per l'input dei dati per un processo di Analisi di flusso viene usato un contenitore BLOB da questo account. Per questa esercitazione, si supponga di disporre di un account di archiviazione **storageforasa** e di un contenitore incluso nell'account denominato **storageforasacontainer**. Dopo aver creato il contenitore, caricare un file di dati di esempio. 
  
* **Un account data Lake storage Gen1**. Seguire le istruzioni [riportate in Introduzione all'Azure Data Lake storage Gen1 usando il portale di Azure](data-lake-store-get-started-portal.md). Si supponga di avere un account Data Lake Storage Gen1 denominato **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso
Iniziare creando un processo di Analisi di flusso che include un'origine di input e una destinazione di output. Per questa esercitazione, l'origine è un contenitore BLOB di Azure e la destinazione è Data Lake Storage Gen1.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro sinistro, fare clic su **Processi di Analisi di flusso**, quindi fare clic su **Aggiungi**.

    ![Creare un processo di analisi di flusso](./media/data-lake-store-stream-analytics/create.job.png "Creare un processo di Analisi di flusso.")

    > [!NOTE]
    > Assicurarsi di creare il processo nella stessa area dell'account di archiviazione per non incorrere in costi aggiuntivi per lo spostamento dei dati tra le aree.
    >

## <a name="create-a-blob-input-for-the-job"></a>Creare un input BLOB per il processo

1. Aprire la pagina per il processo di Analisi di flusso, nel riquadro sinistro fare clic sulla scheda **Input** e quindi fare clic su **Aggiungi**.

    ![Aggiungere un input al processo](./media/data-lake-store-stream-analytics/create.input.1.png "Aggiungere un input al processo")

2. Nel pannello **Nuovo input** specificare i valori seguenti.

    ![Aggiungere un input al processo](./media/data-lake-store-stream-analytics/create.input.2.png "Aggiungere un input al processo")

   * In **Alias dell'input** inserire un nome univoco per l'input del processo.
   * Per **Tipo di origine** selezionare **Flusso dati**.
   * Per **Origine** selezionare **Archiviazione BLOB**.
   * Per **Sottoscrizione** selezionare **Usa l'archiviazione BLOB della sottoscrizione corrente**.
   * Per **Account di archiviazione** selezionare l'account di archiviazione creato come parte dei prerequisiti. 
   * Per **Contenitore** selezionare il contenitore creato nell'account di archiviazione selezionato.
   * In **Formato di serializzazione eventi** scegliere **CSV**.
   * Per **Delimitatore **selezionare **scheda**.
   * Per **Codifica** selezionare **UTF-8**.

     Scegliere **Crea**. Il portale ora aggiunge l'input e verifica la connessione allo stesso.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Creare un output di Data Lake Storage Gen1 per il processo

1. Aprire la pagina per il processo di Analisi di flusso, fare clic sulla scheda **Output**, quindi su **Aggiungi** e selezionare **Data Lake Storage Gen1**.

    ![Aggiungere un output al processo](./media/data-lake-store-stream-analytics/create.output.1.png "Aggiungere un output al processo")

2. Nel pannello **Nuovo output** specificare i valori seguenti.

    ![Aggiungere un output al processo](./media/data-lake-store-stream-analytics/create.output.2.png "Aggiungere un output al processo")

    * In **Alias dell'output** inserire un nome univoco per l'output del processo. È un nome descrittivo usato nelle query per indirizzare l'output delle query all'account Data Lake Storage Gen1.
    * Verrà richiesto di autorizzare l'accesso all'account Data Lake Storage Gen1. Fare clic su **Autorizza**.

3. Nel pannello **Nuovo output** continuare a specificare i valori seguenti.

    ![Aggiungere un output al processo](./media/data-lake-store-stream-analytics/create.output.3.png "Aggiungere un output al processo")

   * Per **Nome account** selezionare l'account Data Lake Storage Gen1 già creato a cui si vuole inviare l'output del processo.
   * In **Schema prefisso percorso** immettere un percorso di file usato per scrivere i file nell'account Data Lake Storage Gen1 specificato.
   * Per **Formato data**, se nel percorso di prefisso viene usato un token di data, è possibile selezionare il formato della data in cui sono organizzati i file.
   * Per **Formato ora**, se nel percorso di prefisso viene usato un token di ora, specificare il formato dell'ora in cui sono organizzati i file.
   * In **Formato di serializzazione eventi** scegliere **CSV**.
   * Per **Delimitatore **selezionare **scheda**.
   * Per **Codifica** selezionare **UTF-8**.
    
     Scegliere **Crea**. Il portale ora aggiunge l'output e verifica la connessione allo stesso.
    
## <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

1. Per eseguire un processo di analisi di flusso, è necessario eseguire una query dalla scheda **query** . Per questa esercitazione, è possibile eseguire la query di esempio sostituendo i segnaposto con gli alias di input e output del processo, come illustrato nella schermata seguente.

    ![Esegui query](./media/data-lake-store-stream-analytics/run.query.png "Esegui query")

2. Fare clic su **Salva** nella parte superiore dello schermo, quindi sulla scheda **Panoramica** e su **Avvia**. Nella finestra di dialogo selezionare **Ora personalizzata**, quindi selezionare la data e l'ora correnti.

    ![Impostare l'ora del processo](./media/data-lake-store-stream-analytics/run.query.2.png "Impostare l'ora del processo")

    Fare clic su **Avvia** per avviare il processo. L'avvio del processo può richiedere un paio di minuti.

3. Per attivare la raccolta dei dati dal BLOB da parte del processo, copiare un file dati campione nel contenitore BLOB. È possibile ottenere un file di dati di esempio dal [repository Git di Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Per questa esercitazione verrà copiato il file **vehicle1_09142014.csv**. È possibile usare vari tipi di client, ad esempio [Azure Storage Explorer](https://storageexplorer.com/), per caricare i dati in un contenitore BLOB.

4. Nella scheda **Panoramica**, in **Monitoraggio** è possibile visualizzare come sono stati elaborati i dati.

    ![Monitorare il processo](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorare il processo")

5. Infine, è possibile verificare la disponibilità dei dati di output del processo nell'account Data Lake Storage Gen1. 

    ![Verificare l'output](./media/data-lake-store-stream-analytics/run.query.4.png "Verificare l'output")

    Nel riquadro Esplora dati l'output viene scritto in un percorso di cartella come specificato nelle impostazioni di output di Data Lake Storage Gen1 (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Vedi anche
* [Creare un cluster HDInsight per usare Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
