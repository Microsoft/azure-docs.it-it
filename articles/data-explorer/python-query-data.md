---
title: Eseguire query sui dati usando la libreria di Esplora dati di Azure per Python
description: In this article, you learn how to query data from Azure Data Explorer using Python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: ebd65f2dcbb0040b764290627bbfd2901aa9a7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443976"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>Eseguire query sui dati usando la libreria di Esplora dati di Azure per Python

In this article, you query data using the Azure Data Explorer. Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria.

Esplora i dati di Azure mette a disposizione una [libreria client per i dati per Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Questa libreria consente di eseguire query sui dati dal codice. Connettersi a una tabella nel cluster di *assistenza* che è stato configurato per facilitare l'apprendimento. È possibile eseguire una query su una tabella in tale cluster e restituire i risultati.

Questo articolo è disponibile anche come blocco appunti di [Azure.This article](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb)is also available as an Azure Notebook .

## <a name="prerequisites"></a>Prerequisiti

* [Pitone 3.4](https://www.python.org/downloads/)

* Un account di posta elettronica dell'organizzazione che sia membro di Azure Active Directory

## <a name="install-the-data-library"></a>Installare la libreria per i dati

Installare *azure-kusto-data*.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Aggiungere le costanti e le istruzioni import

Importare le classi dalla libreria, nonché *pandas*, una libreria di analisi dei dati.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Per autenticare un'applicazione, Esplora dati di Azure usa l'ID tenant AAD. Per trovare l'ID del tenant, usare l'URL seguente, sostituendo il dominio a *YourDomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ad esempio, se il dominio è *contoso.com*, l'URL è: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Fare clic su questo URL per visualizzare i risultati; la prima riga è come indicato di seguito.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Il tenant ID in questo caso è `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Prima di eseguire questo codice, impostare il valore di AAD_TENANT_ID.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE = "Samples"
```

Costruire ora la stringa di connessione. Questo esempio usa l'autenticazione del dispositivo per accedere al cluster. È inoltre possibile utilizzare il [certificato dell'applicazione AAD,](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24)la [chiave dell'applicazione AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)e [l'utente e la password di AAD.](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Connettersi a Esplora dati di Azure ed eseguire una query

Eseguire una query sul cluster e archiviare l'output in un frame di dati. Quando viene eseguito questo codice, restituisce un messaggio simile al seguente: *Per accedere, usare un web browser per aprire la pagina https://microsoft.com/devicelogin e immettere il codice F3W4VWZDM per l'autenticazione*. Seguire i passaggi per l'accesso, quindi tornare per eseguire il blocco di codice successivo.

```python
KUSTO_CLIENT = KustoClient(KCSB)
KUSTO_QUERY = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Esplorare i dati in DataFrame

Dopo che è stato eseguito l'accesso, la query restituisce risultati, che vengono archiviati in un frame di dati. È possibile usare i risultati in modo analogo a qualsiasi altro frame di dati.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

I primi dieci risultati visualizzati provengono dalla tabella StormEvents.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire l'inserimento di dati usando la libreria Python di Azure Data ExplorerIngest data using the Azure Data Explorer Python library](python-ingest-data.md)
