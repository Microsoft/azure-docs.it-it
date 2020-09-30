---
title: " Creare un'app Web a pagina singola - Ricerca visiva Bing"
titleSuffix: Azure Cognitive Services
description: Imparare a integrare l'API Ricerca visiva Bing in un'applicazione Web a pagina singola.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 28f7d279d1328ff750bf7095f32fbf3ff65e591b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324523"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Esercitazione: Creare un'app Web a pagina singola con Ricerca visiva

L'API Ricerca visiva Bing restituisce informazioni dettagliate relative a un'immagine. È possibile caricare un'immagine o specificarne l'URL. Le informazioni dettagliate sono immagini visivamente simili, luoghi di acquisto degli oggetti raffigurati nelle immagini, pagine Web che includono l'immagine e altro ancora. Le informazioni dettagliate restituite dall'API Ricerca visiva Bing sono simili a quelle visualizzate nel sito Bing.com/images.

Questa esercitazione illustra come estendere un'app Web a pagina singola per l'API Ricerca immagini Bing. Per visualizzare l'esercitazione o ottenere il codice sorgente utilizzato qui, vedere Esercitazione [: Creare un'app a pagina singola con l'API Ricerca immagini Bing](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Il codice sorgente completo per questa applicazione, estesa in modo che usi l'API Ricerca visiva Bing, è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Chiamare l'API Ricerca visiva Bing e gestire la risposta

Modificare l'esercitazione su Ricerca immagini Bing e aggiungere il codice seguente alla fine dell'elemento `<script>` e prima del tag `</script>` di chiusura. Il codice seguente gestisce una risposta dell'API Ricerca visiva, esegue un'iterazione sui risultati e li visualizza:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

Il codice seguente invia una richiesta di ricerca all'API usando un listener di eventi per chiamare `handleVisualSearchResponse()`:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Acquisire il token imageInsightsToken

Aggiungere il codice seguente all'oggetto `searchItemsRenderer`. Questo codice aggiunge un collegamento **find similar** che chiama la funzione `bingVisualSearch` quando si fa clic. La funzione riceve `imageInsightsToken` come argomento.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Visualizzare immagini simili

Aggiungere il codice HTML seguente alla riga 601. Questo codice di markup aggiunge un elemento per la visualizzazione dei risultati della chiamata all'API Ricerca visiva Bing:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Dopo avere inserito tutto il nuovo codice JavaScript e gli elementi HTML, è possibile usare il collegamento **find similar** per visualizzare i risultati delle ricerche. Fare clic sul collegamento per popolare la sezione **Similar** con immagini simili a quella selezionata. Può essere necessario espandere la sezione **Similar** per mostrare le immagini.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Ritagliare un'immagine con l'SDK di Ricerca visiva Bing per C#](tutorial-visual-search-crop-area-results.md)
