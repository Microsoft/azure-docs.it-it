---
title: Gestire un account Azure Data Lake Storage Gen1 con Python
description: Informazioni su come usare Python SDK per Azure Data Lake Storage Gen1 operazioni di gestione degli account.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 35e71b80c6f47bb13f7a2b490b493b0cb42acf04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103510"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operazioni di gestione di account in Azure Data Lake Storage Gen1 con Python
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Informazioni su come usare Python SDK per Azure Data Lake Storage Gen1 per eseguire operazioni di gestione di account di base, ad esempio creare un account di Data Lake Storage Gen1, elencare gli account di Data Lake Storage Gen1 e così via. Per istruzioni su come eseguire operazioni del file System su Data Lake Storage Gen1 usando Python, vedere [operazioni del file System su data Lake storage Gen1 con Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Prerequisiti

* **Python**. È possibile scaricare Python [qui](https://www.python.org/downloads/). Questo articolo fa riferimento a Python 3.6.2.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Un gruppo di risorse di Azure**. Per istruzioni, vedere [Creare un gruppo di risorse di Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Installare i moduli

Per usare Data Lake Storage Gen1 con Python, è necessario installare tre moduli.

* Il modulo `azure-mgmt-resource` che include i moduli di Azure per Active Directory e così via.
* Il modulo `azure-mgmt-datalake-store` che include le operazioni di gestione di account di Azure Data Lake Storage Gen1. Per altre informazioni su questo modulo, vedere le [informazioni di riferimento sul modulo di gestione di Azure Data Lake Storage Gen1](/python/api/azure-mgmt-datalake-store/).
* Il modulo `azure-datalake-store` che include le operazioni del file system di Azure Data Lake Storage Gen1. Per altre informazioni su questo modulo, vedere le [informazioni di riferimento sul modulo del file system azure-datalake-store](/python/api/azure-datalake-store/azure.datalake.store.core/).

Per installare i moduli, usare i comandi seguenti.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

1. In un IDE a scelta creare una nuova applicazione Python, ad esempio **mysample.py**.

2. Aggiungere il frammento di codice seguente per importare i moduli necessari

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
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

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Creare un client e un account Data Lake Storage Gen1

Il frammento di codice seguente crea innanzitutto il client dell'account Data Lake Storage Gen1. Usa l'oggetto client per creare un account Data Lake Storage Gen1. e infine crea un oggetto client per il file system.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Visualizzare l'elenco degli account Data Lake Storage Gen1

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Eliminare l'account Data Lake Storage Gen1

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>Passaggi successivi
* [Operazioni del file system in Data Lake Storage Gen1 con Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Vedi anche

* [Informazioni di riferimento su Python azure-datalake-store (file system)](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aprire le applicazioni Big Data di origine compatibili con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)