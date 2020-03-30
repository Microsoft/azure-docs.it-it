---
title: Dati di esempio nell'archiviazione BLOB di Azure - Processo di analisi scientifica dei dati per i team
description: Dati di campionamento che è possibile memorizzare nell'archivio BLOB di Azure scaricandoli a livello di programmazione ed eseguendo il successivo campionamento tramite routine scritte in Python.
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
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720283"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Dati di esempio nell'archiviazione BLOB di AzureSample data in Azure blob storage

Questo articolo descrive i dati di campionamento che è possibile memorizzare nell'archivio BLOB di Azure scaricandoli a livello di programmazione ed eseguendo il successivo campionamento tramite routine scritte in Python.

**Perché campionare i dati?**
Se il set di dati da analizzare è grande, è in genere opportuno sottocampionare i dati per ridurlo e ottenere dimensioni inferiori più facilmente gestibili ma comunque rappresentative. Il campionamento facilita la comprensione, l'esplorazione e la progettazione delle funzionalità dei dati. Il suo ruolo nel Cortana Analytics Process consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli per l'apprendimento automatico.

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Download e sotto-campionamento dei dati
1. Scaricare i dati dall'archiviazione BLOB di Azure usando il servizio BLOB dal codice Python di esempio seguente:Download the data from Azure blob storage using the Blob service from the following sample Python code: 
   
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

2. Leggere i dati all'interno di un frame di dati Pandas dal file scaricato in precedenza.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Eseguire il sotto-campionamento dei dati usando `numpy`di `random.choice` nel modo seguente:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Ora è possibile lavorare con il frame di dati sopra riportato con l'esempio one Percent per ulteriori esplorazioni e generazione di funzionalità.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Caricamento e lettura dei dati in Azure Machine Learning
Per sottocampionare i dati e usarli direttamente in Azure Machine Learning, è possibile usare il codice di esempio seguente:

1. Scrivere il frame di dati su un file locale
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Caricare il file locale su un BLOB di Azure usante il seguente codice di esempio:
   
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

3. Leggere i dati del BLOB di Azure con [Import Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) (Importazione dati) di Azure Machine Learning, come illustrato nell'immagine seguente:

![lettore BLOB](./media/sample-data-blob/reader_blob.png)

