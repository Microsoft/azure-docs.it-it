---
title: Azure Data Lake Storage Gen2 Python SDK per file & ACL
description: Usare Python per gestire directory e elenchi di controllo di accesso di file e directory (ACL) negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).
author: normesta
ms.service: storage
ms.date: 01/22/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 5036930c7bb49578582fbc1b347b11518579b53e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740619"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare Python per gestire directory, file e ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare Python per creare e gestire directory, file e autorizzazioni negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS). 

[Pacchetto (indice pacchetto Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  di [Riferimento API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Mapping tra Gen1 [e Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](../common/storage-account-create.md) istruzioni.

## <a name="set-up-your-project"></a>Configurare il progetto

Installare la libreria client di Azure Data Lake Storage per Python usando [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Aggiungere queste istruzioni Import all'inizio del file di codice.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Effettuare la connessione all'account

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di **DataLakeServiceClient** che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-an-account-key"></a>Connettersi tramite una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Sostituire il valore segnaposto `storage_account_name` con il nome del proprio account di archiviazione.

- Sostituire il `storage_account_key` valore del segnaposto con la chiave di accesso dell'account di archiviazione.

### <a name="connect-by-using-azure-active-directory-ad"></a>Connettersi tramite Azure Active Directory (AD)

È possibile usare la [libreria client Azure Identity per Python](https://pypi.org/project/azure-identity/) per autenticare l'applicazione con Azure ad.

Questo esempio crea un'istanza di **DataLakeServiceClient** usando un ID client, un segreto client e un ID tenant.  Per ottenere questi valori, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Per altri esempi, vedere la documentazione della [libreria client Azure Identity per Python](https://pypi.org/project/azure-identity/) .

## <a name="create-a-container"></a>Creare un contenitore

Un contenitore funge da file system per i file. È possibile crearne una chiamando il metodo **FileSystemDataLakeServiceClient.create_file_system** .

Questo esempio crea un contenitore denominato `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory chiamando il metodo **FileSystemClient.create_directory** .

In questo esempio viene aggiunta una directory denominata `my-directory` a un contenitore. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory chiamando il metodo **DataLakeDirectoryClient.rename_directory** . Passare il percorso della directory desiderata a un parametro. 

Questo esempio rinomina una sottodirectory con il nome `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory chiamando il metodo **DataLakeDirectoryClient.delete_directory** .

Questo esempio illustra come eliminare una directory denominata `my-directory`.  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory 

Per prima cosa, creare un riferimento a un file nella directory di destinazione creando un'istanza della classe **DataLakeFileClient** . Caricare un file chiamando il metodo **DataLakeFileClient.append_data** . Assicurarsi di completare il caricamento chiamando il metodo **DataLakeFileClient.flush_data** .

Questo esempio consente di caricare un file di testo in una directory denominata `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Se le dimensioni del file sono elevate, il codice dovrà effettuare più chiamate al metodo **DataLakeFileClient.append_data** . Provare a usare invece il metodo **DataLakeFileClient.upload_data** . In questo modo, è possibile caricare l'intero file in una singola chiamata. 

## <a name="upload-a-large-file-to-a-directory"></a>Caricare un file di grandi dimensioni in una directory

Usare il metodo **DataLakeFileClient.upload_data** per caricare file di grandi dimensioni senza dover eseguire più chiamate al metodo **DataLakeFileClient.append_data** .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Scaricare da una directory 

Aprire un file locale per la scrittura. Quindi, creare un'istanza di **DataLakeFileClient** che rappresenti il file che si desidera scaricare. Chiamare il **DataLakeFileClient.read_file** per leggere i byte dal file e quindi scriverli nel file locale. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto della directory chiamando il metodo **FileSystemClient.get_paths** e quindi enumerando i risultati.

In questo esempio viene stampato il percorso di ogni sottodirectory e file che si trova in una directory denominata `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>Gestire gli elenchi di controllo di accesso (ACL)

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di file e directory.

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare l'accesso, assicurarsi che all'entità di sicurezza sia stato assegnato il [ruolo proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-directory-acls"></a>Gestisci ACL directory

Ottenere l'elenco di controllo di accesso (ACL) di una directory chiamando il metodo **DataLakeDirectoryClient.get_access_control** e impostare l'ACL chiamando il metodo **DataLakeDirectoryClient.set_access_control** .

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che l'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stata assegnata al [ruolo proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

Questo esempio Mostra come ottenere e impostare l'ACL di una directory denominata `my-directory` . La stringa `rwxr-xrw-` fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

È anche possibile ottenere e impostare l'ACL della directory radice di un contenitore. Per ottenere la directory radice, chiamare il metodo **FileSystemClient._get_root_directory_client** .

### <a name="manage-file-permissions"></a>Gestisci autorizzazioni file

Ottenere l'elenco di controllo di accesso (ACL) di un file chiamando il metodo **DataLakeFileClient.get_access_control** e impostare l'ACL chiamando il metodo **DataLakeFileClient.set_access_control** .

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che l'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stata assegnata al [ruolo proprietario dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

Questo esempio Mostra come ottenere e impostare l'ACL di un file denominato `my-file.txt` . La stringa `rwxr-xrw-` fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>Impostare un ACL in modo ricorsivo

È possibile aggiungere, aggiornare e rimuovere gli ACL in modo ricorsivo negli elementi figlio esistenti di una directory padre senza dover apportare queste modifiche singolarmente per ogni elemento figlio. Per altre informazioni, vedere [impostare elenchi di controllo di accesso (ACL) in modo ricorsivo per Azure Data Lake storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Vedere anche

* [Documentazione di riferimento delle API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [Pacchetto (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
* [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-python/issues)