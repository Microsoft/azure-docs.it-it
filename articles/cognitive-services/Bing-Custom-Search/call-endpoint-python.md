---
title: "Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando Python | Microsoft Docs"
titleSuffix: Azure Cognitive Services
description: Seguire questo argomento di avvio rapido per iniziare a richiedere risultati della ricerca all'istanza di Ricerca personalizzata Bing con Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 9c0a5b849ff3984c7fccc83a149100b462295da5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352032"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando Python

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Seguire questo argomento di avvio rapido per informazioni su come richiedere risultati della ricerca all'istanza di Ricerca personalizzata Bing. Anche se l'applicazione è scritta in Python, l'API Ricerca personalizzata Bing è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza di Ricerca personalizzata Bing. Per altre informazioni, vedere [Avvio rapido: Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).
- [Python](https://www.python.org/) 2.x o 3.x.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

- Creare un nuovo file Python nell'IDE o nell'editor preferito e aggiungere le istruzioni di importazione seguenti. Creare variabili per la chiave di sottoscrizione, l'ID configurazione personalizzato e un termine di ricerca.

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Inviare e ricevere una richiesta di ricerca 

1. Creare l'URL della richiesta aggiungendo il termine di ricerca al parametro di query `q=` e l'ID configurazione personalizzato dell'istanza di ricerca al parametro `customconfig=`. Separare i parametri con una e commerciale (`&`). È possibile usare l'endpoint globale nel codice seguente o l'endpoint di [sottodominio personalizzato](../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Inviare la richiesta all'istanza di Ricerca personalizzata Bing e stampare i risultati della ricerca restituiti.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](./tutorials/custom-search-web-page.md)