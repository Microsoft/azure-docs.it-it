---
title: Avvio rapido della libreria client JavaScript di Ricerca Web Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 0380dc8d2ff34cf9eecaad063a305491a357ca29
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924830"
---
La libreria client di Ricerca Web Bing facilita l'integrazione di Ricerca Web Bing nell'applicazione Node.js. In questa guida introduttiva si apprenderà come creare un'istanza di un client, inviare una richiesta e stampare la risposta.

Per vedere il codice subito, Esempi per le [librerie client di Ricerca Bing per JavaScript](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/tree/master/Samples) sono disponibili su GitHub.

## <a name="prerequisites"></a>Prerequisites
Prima di eseguire i passaggi illustrati in questa guida introduttiva, verificare di avere quanto segue:

* [Node.js 6](https://nodejs.org/en/download/) o versione successiva
* Una chiave di sottoscrizione  

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Si inizia configurando l'ambiente di sviluppo per il progetto Node. js.

1. Creare una nuova directory per il progetto:

    ```console
    mkdir YOUR_PROJECT
    ```

1. Creare un nuovo file di pacchetto:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. A questo punto è possibile installare alcuni moduli di Azure e aggiungerli a `package.json`:

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Creare un progetto e dichiarare i moduli necessari

Nella stessa directory di `package.json` creare un nuovo progetto Node.js usando l'editor o l'IDE preferito. Ad esempio: `sample.js`.

Copiare quindi il codice seguente nel progetto. Vengono caricati i moduli installati nella sezione precedente.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Creare un'istanza del client

Il codice crea un'istanza di un client usando il modulo `azure-cognitiveservices-websearch`. Immettere una chiave di sottoscrizione valida per l'account di Azure prima di continuare.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Eseguire una richiesta e stampare i risultati

Usare il client per inviare una query di ricerca a Ricerca Web Bing. Se la risposta include risultati per uno qualsiasi degli elementi della matrice `properties`, nella console viene stampato il valore di `result.value`.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Eseguire il programma

Il passaggio finale consiste nell'eseguire il programma creato.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver terminato di usare questo progetto, rimuovere la chiave di sottoscrizione dal codice del programma.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esempi di SDK Node.js per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Vedere anche

* [Azure Node SDK reference](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/) (Informazioni di riferimento su Azure Node SDK)
