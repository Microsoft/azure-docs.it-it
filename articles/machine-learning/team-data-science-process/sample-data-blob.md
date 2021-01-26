---
title: Dati di esempio nell'archiviazione BLOB di Azure-processo di Data Science per i team
description: Campionare i dati archiviati nell'archivio BLOB di Azure scaricarli a livello di codice e quindi campionarli usando le procedure scritte in Python.
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
ms.openlocfilehash: c5827a0e07e537b66684f852d8f3e1500cd9febb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788842"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Dati di esempio nell'archivio BLOB di Azure

Questo articolo descrive i dati di campionamento archiviati nell'archivio BLOB di Azure, eseguendone il download a livello di codice e quindi eseguendone il campionamento usando le procedure scritte in Python

**Perché campionare i dati?**
Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Il campionamento semplifica la comprensione dei dati, l'esplorazione e la progettazione delle funzionalità. Il suo ruolo nel Cortana Analytics Process consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli per l'apprendimento automatico.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](./index.yml).

## <a name="download-and-down-sample-data"></a>Download e sotto-campionamento dei dati
1. Scaricare i dati dall'archiviazione BLOB di Azure usando il servizio BLOB dal codice Python di esempio seguente: 

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

2. Leggere i dati all'interno di un frame di dati Pandas dal file scaricato in precedenza.

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. Eseguire il sotto-campionamento dei dati usando `numpy`di `random.choice` nel modo seguente:

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

A questo punto è possibile usare il frame di dati sopra riportato con l'esempio di percentuale per l'esplorazione e la generazione di funzionalità.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Caricamento e lettura dei dati in Azure Machine Learning
Per sottocampionare i dati e usarli direttamente in Azure Machine Learning, è possibile usare il codice di esempio seguente:

1. Scrivere il frame di dati su un file locale

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Caricare il file locale su un BLOB di Azure usante il seguente codice di esempio:

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. Leggere i dati del BLOB di Azure con [Import Data](/azure/machine-learning/studio-module-reference/import-data) (Importazione dati) di Azure Machine Learning, come illustrato nell'immagine seguente:

![lettore BLOB](./media/sample-data-blob/reader_blob.png)
