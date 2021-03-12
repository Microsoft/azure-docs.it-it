---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 1b0d929e667023640906baa87a2fd25c61c5d488
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620083"
---
## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Una risorsa dello strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory. Seguire [queste istruzioni](../../how-to-create-immersive-reader.md) per configurare l'ambiente.  Per la configurazione delle proprietà dell'ambiente, saranno necessari alcuni dei valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.
* [Node.js](https://nodejs.org/) e [Yarn](https://yarnpkg.com)
* Un ambiente di sviluppo integrato, ad esempio [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Creare un'app Web Node.js con Express

Creare un'app Web Node.js con lo strumento `express-generator`.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Installare le dipendenze di Yarn e aggiungere le dipendenze `request` e `dotenv`, che verranno usate più avanti nell'esercitazione.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Acquisire un token di autenticazione di Azure AD

Successivamente, scrivere un'API back-end per recuperare un token di autenticazione di Azure AD.

Per questa parte sono necessari alcuni valori del passaggio relativo ai prerequisiti di configurazione dell'autenticazione di Azure AD. Fare riferimento al file di testo salvato della sessione.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Dopo aver recuperato i valori necessari, creare un nuovo file denominato _.env_ e incollare il codice seguente al suo interno, specificando i valori delle proprietà personalizzate dal passaggio precedente. Non includere virgolette o i caratteri "{" e "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Fare attenzione a non eseguire il commit di questo file nel controllo del codice sorgente, poiché contiene segreti che non devono essere resi pubblici.

Aprire quindi _app.js_ e aggiungere il codice seguente all'inizio del file. Verranno caricate le proprietà definite nel file .env come variabili di ambiente in Node.

```javascript
require('dotenv').config();
```

Aprire il file _routes\index.js_ e sostituirne il contenuto con il codice seguente.

Questo codice crea un endpoint API che acquisisce un token di autenticazione di Azure AD usando la password dell'entità servizio e recupera anche il sottodominio, restituendo quindi un oggetto che contiene il token e il sottodominio.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

L'endpoint API **getimmersivereaderlaunchparams** deve essere protetto da qualche forma di autenticazione, ad esempio [OAuth](https://oauth.net/2/), per impedire agli utenti non autorizzati di ottenere token da usare per il servizio Strumento di lettura immersiva e la fatturazione, ma la relativa procedura esula dall'ambito di questa esercitazione.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

1. Aprire _views\layout.pug_ e aggiungere il codice seguente sotto il tag `head`, prima del tag `body`. Questi tag `script` caricano [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Aprire _views\index.pug_ e sostituirne il contenuto con il codice seguente. Questo codice popola la pagina con contenuto di esempio e aggiunge un pulsante che avvia lo strumento di lettura immersiva.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. L'app Web è ora pronta. Avviare l'app con il comando seguente:

    ```bash
    npm start
    ```

4. Aprire il browser e passare a _http://localhost:3000_ . Nella pagina verrà visualizzato il contenuto sopra riportato. Fare clic sul pulsante **Immersive Reader** per avviare lo strumento di lettura immersiva con il contenuto.

## <a name="specify-the-language-of-your-content"></a>Specificare la lingua del contenuto

Lo strumento di lettura immersiva include il supporto per molte lingue diverse. È possibile specificare la lingua del contenuto seguendo la procedura seguente.

1. Aprire _views\index.pug_ e inserire il codice seguente sotto il tag `p(id=content)` aggiunto nel passaggio precedente. Questo codice aggiunge del contenuto in spagnolo alla pagina.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. Nel codice JavaScript aggiungere il codice seguente prima della chiamata a `ImmersiveReader.launchAsync`. Questo codice passa il contenuto in spagnolo allo strumento di lettura immersiva.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Passare di _http://localhost:3000_ nuovo a. Il testo in spagnolo viene visualizzato nella pagina e, quando si fa clic su **Immersive Reader**, viene visualizzato anche nello strumento di lettura immersiva.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Specificare la lingua dell'interfaccia dello strumento di lettura immersiva

Per impostazione predefinita, la lingua dell'interfaccia dello strumento di lettura immersiva corrisponde a quella definita per il browser. È anche possibile specificare la lingua dell'interfaccia dello strumento di lettura immersiva con il codice seguente.

1. In _views\index.pug_ sostituire la chiamata a `ImmersiveReader.launchAsync(token, subdomain, content)` con il codice seguente.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Passare a _http://localhost:3000_. Quando si avvierà lo strumento di lettura immersiva, l'interfaccia verrà visualizzata in francese.

## <a name="launch-the-immersive-reader-with-math-content"></a>Avviare lo strumento di lettura immersiva con dati matematici

È possibile includere dati matematici nello strumento di lettura immersiva usando [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Modificare _views\index.pug_ in modo da includere il codice seguente prima della chiamata a `ImmersiveReader.launchAsync`:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Passare a _http://localhost:3000_. Quando si avvierà lo strumento di lettura immersiva, si noterà la formula matematica alla fine della schermata.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../../reference.md)
* Visualizzare gli esempi di codice su [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
