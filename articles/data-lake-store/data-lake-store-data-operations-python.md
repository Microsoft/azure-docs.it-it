---
title: 'Python: Operazioni del file system in Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Informazioni su come usare Python SDK con il file system di Data Lake Storage Gen1.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 8ca455a802b180163579f67104a61f455dd54f94
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109222"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operazioni del file system in Azure Data Lake Storage Gen1 con Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Questo articolo fornisce informazioni per l'uso di Python SDK per eseguire operazioni del file system in Azure Data Lake Storage Gen1. Per istruzioni su come eseguire le operazioni di gestione di account in Data Lake Storage Gen1 con Python, consultare [Operazioni di gestione di account in Data Lake Storage Gen1 con Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Prerequisiti

* **Python**. È possibile scaricare Python [qui](https://www.python.org/downloads/). Questo articolo fa riferimento a Python 3.6.2.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Account Azure Data Lake Storage Gen1**. Seguire le istruzioni fornite in [Introduzione ad Azure Data Lake Storage Gen1 con il portale di Azure](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Installare i moduli

Per usare Data Lake Storage Gen1 con Python, è necessario installare tre moduli.

* Il modulo `azure-mgmt-resource` che include i moduli di Azure per Active Directory e così via.
* Il modulo `azure-mgmt-datalake-store` che include le operazioni di gestione di account di Azure Data Lake Storage Gen1. Per altre informazioni su questo modulo, vedere le [informazioni di riferimento sul modulo azure-mgmt-datalake-store](/python/api/azure-mgmt-datalake-store/).
* Il modulo `azure-datalake-store` che include le operazioni del file system di Azure Data Lake Storage Gen1. Per altre informazioni su questo modulo, vedere le [informazioni di riferimento sul modulo del file system azure-datalake-store](/python/api/azure-datalake-store/azure.datalake.store.core/).

Per installare i moduli, usare i comandi seguenti.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

1. In un IDE a scelta creare una nuova applicazione Python, ad esempio **mysample.py**.

2. Aggiungere le righe seguenti per importare i moduli necessari

   ```python
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Salvare le modifiche a mysample.py.

## <a name="authentication"></a>Authentication

Questa sezione descrive le diverse modalità di autenticazione con Azure AD. Le opzioni disponibili sono:

* Per l'autenticazione dell'utente finale nell'applicazione, consultare [Autenticazione dell'utente finale con Data Lake Storage Gen1 con Python](data-lake-store-end-user-authenticate-python.md).
* Per l'autenticazione da servizio a servizio per l'applicazione, vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Creare il client del file system

Il frammento di codice seguente crea innanzitutto il client dell'account Data Lake Storage Gen1. Usa l'oggetto client per creare un account Data Lake Storage Gen1. e infine crea un oggetto client per il file system.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'

## Create a filesystem client object
adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)
```

## <a name="create-a-directory"></a>Creare una directory

```python
## Create a directory
adlsFileSystemClient.mkdir('/mysampledirectory')
```

## <a name="upload-a-file"></a>Caricare un file

```python
## Upload a file
multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```


## <a name="download-a-file"></a>Scaricare un file

```python
## Download a file
multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```

## <a name="delete-a-directory"></a>Eliminare una directory

```python
## Delete a directory
adlsFileSystemClient.rm('/mysampledirectory', recursive=True)
```

## <a name="next-steps"></a>Passaggi successivi
* [Operazioni di gestione degli account in data Lake storage Gen1 tramite Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Vedi anche

* [Informazioni di riferimento su Python con Azure Data Lake Storage Gen1 - file system](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aprire le applicazioni Big Data di origine compatibili con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)