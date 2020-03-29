---
title: Connettersi all'API di Servizi multimediali di Azure - PythonConnect to Azure Media Services v3 API - Python
description: Questo articolo illustra come connettersi all'API di Servizi multimediali v3 con Python.This article demonstrates how to connect to Media Services v3 API with Python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888462"
---
# <a name="connect-to-media-services-v3-api---python"></a>Connettersi all'API di Servizi multimediali v3 - PythonConnect to Media Services v3 API - Python

Questo articolo illustra come connettersi a Azure Media Services v3 Python SDK usando il metodo di accesso dell'entità servizio.

## <a name="prerequisites"></a>Prerequisiti

- Scarica Python da [python.org](https://www.python.org/downloads/)
- Assicurarsi di `PATH` impostare la variabile di ambiente
- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di Servizi multimediali.
- Seguire i passaggi nell'argomento [API di accesso.](access-api-cli-how-to.md) Registrare l'ID sottoscrizione, l'ID applicazione (ID client), la chiave di autenticazione (segreto) e l'ID tenant necessari nel passaggio successivo.

> [!IMPORTANT]
> Esaminare [le convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Installare i moduli

Per usare Servizi multimediali di Azure usando Python, è necessario installare questi moduli.

* Modulo, che include moduli di Azure per Active Directory.The `azure-mgmt-resource` module, which includes Azure modules for Active Directory.
* Modulo, `azure-mgmt-media` che include le entità Servizi multimediali.

Aprire uno strumento da riga di comando e utilizzare i comandi seguenti per installare i moduli.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Connettersi al client Python

1. Creare un file `.py` con un'estensione
1. Aprire il file nell'editor preferito
1. Aggiungere il codice seguente al file. Il codice importa i moduli necessari e crea l'oggetto credenziali di Active Directory necessario per connettersi a Servizi multimediali.

      Impostare i valori delle variabili sui valori ottenuti dalle API di AccessSet the variables' values to the values you got from [Access APIs](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Eseguire il file

## <a name="next-steps"></a>Passaggi successivi

- Usare [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Esaminare la documentazione [Informazioni di riferimento su Python](https://aka.ms/ams-v3-python-ref) di Servizi multimediali.
