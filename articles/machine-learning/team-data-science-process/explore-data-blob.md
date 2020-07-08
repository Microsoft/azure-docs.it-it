---
title: Esplorare i dati nell'archiviazione BLOB di Azure con Pandas - Processo di analisi scientifica dei dati del team
description: Come esplorare i dati archiviati nel contenitore BLOB di Azure con il pacchetto Python Pandas.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e429dce497411305964cb1ec5298228dc4093b1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685948"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Esplorare i dati nell'archiviazione BLOB di Azure con Pandas

Questo articolo descrive come esplorare i dati archiviati nel contenitore BLOB di Azure con il pacchetto Python [Pandas](https://pandas.pydata.org/).

Questa attività è un passaggio del [processo di data science per i team](overview.md).

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../../storage/common/storage-account-create.md)
* I dati sono stati archiviati in un account di archiviazione BLOB di Azure. Per le istruzioni, vedere [Spostamento dei dati da e verso Archiviazione di Azure](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Caricare i dati in un frame di dati Pandas
Per esplorare e modificare un set di dati, è necessario innanzitutto scaricarlo dall'origine BLOB in un file locale che può essere quindi caricato in un frame di dati Pandas. Ecco i passaggi da seguire per questa procedura:

1. Scaricare i dati da BLOB Azure con l’esempio di codice Python riportato di seguito utilizzando il servizio BLOB. Sostituire la variabile nel codice seguente con i valori specifici:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

1. Leggere i dati in un frame di dati Pandas dal file scaricato.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

A questo punto si è pronti per esplorare i dati e generare le funzionalità di questo set di dati.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Esempi di esplorazione dei dati tramite Pandas
Di seguito sono riportati alcuni esempi dei modi per esplorare i dati usando Pandas:

1. Controllare il **numero di righe e colonne**

    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```

1. **Controllare** le prime o le ultime **righe** nel set di dati seguente:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Controllare il **tipo di dati** importato in ogni colonna mediante il seguente codice di esempio

    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```

1. Controllare le **statistiche di base** per le colonne nel set di dati come segue

    ```python
    dataframe_blobdata.describe()
    ```

1. Esaminare il numero di voci per ogni valore della colonna come indicato di seguito

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Contare i valori mancanti** rispetto al numero effettivo di voci in ogni colonna utilizzando il seguente codice di esempio

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```

1. Se si dispongono di **valori mancanti** per una colonna specifica nei dati, è possibile eliminarli come indicato di seguito:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    È possibile sostituire i valori mancanti anche con la funzione modalità:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Creare un grafico **istogramma** utilizzando un numero variabile di contenitori per tracciare la distribuzione di una variabile

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Esaminare le **correlazioni** tra le variabili utilizzando un grafico di dispersione o la funzione di correlazione incorporata

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
