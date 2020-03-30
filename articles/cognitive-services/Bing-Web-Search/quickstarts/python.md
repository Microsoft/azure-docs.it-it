---
title: 'Guida introduttiva: Eseguire una ricerca con Python - API Ricerca Web Bing'
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per inviare richieste all'API REST Ricerca Web Bing usando Python e ricevere una risposta JSON
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c1af1142faca76cc58b6b3ca9a7106bc0433ea18
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74976367"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Guida introduttiva: Usare Python per chiamare l'API Ricerca Web Bing  

Usare questa guida introduttiva per eseguire la prima chiamata all'API Ricerca Web Bing e ricevere la risposta JSON. Questa applicazione Python invia una richiesta di ricerca all'API e visualizza la risposta. L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

Questo esempio viene eseguito come notebook di Jupyter su [MyBinder](https://mybinder.org). Selezionare la notifica di avvio di Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Prerequisites

* [Python 2.x o 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definire le variabili

Sostituire il valore di `subscription_key` con una chiave di sottoscrizione valida del proprio account Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Dichiarare l'endpoint dell'API Ricerca Web Bing. È possibile usare l'endpoint globale seguente o l'endpoint [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

È possibile personalizzare la query di ricerca sostituendo il valore per `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Effettuare una richiesta

Questo blocco usa la libreria `requests` per chiamare l'API Ricerca Web Bing e restituire i risultati come oggetto JSON. La chiave API viene passata nel dizionario `headers`, mentre il termine di ricerca e i parametri della query vengono passati nel dizionario `params`. Vedere la documentazione dell'[API Ricerca Web Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) per un elenco completo di opzioni e parametri.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formattare e visualizzare la risposta

L'oggetto `search_results` contiene i risultati della ricerca e i metadati, ad esempio le pagine e le query correlate. Questo codice usa la libreria `IPython.display` per formattare e visualizzare la risposta nel browser.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Codice di esempio su GitHub

Se si vuole eseguire questo codice in locale, l'intero [esempio è disponibile in GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca Web Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
