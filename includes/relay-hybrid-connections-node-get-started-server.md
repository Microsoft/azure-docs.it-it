---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67180737"
---
### <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

Creare un nuovo file JavaScript denominato `listener.js`.

### <a name="add-the-relay-npm-package"></a>Aggiungere il pacchetto NPM di inoltro

Eseguire `npm install hyco-ws` dal prompt dei comandi di Node nella cartella del progetto.

### <a name="write-some-code-to-receive-messages"></a>Scrivere codice per ricevere messaggi

1. Aggiungere la costante seguente all'inizio del file `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Aggiungere le costanti seguenti al file `listener.js` per i dettagli della connessione ibrida. Sostituire i segnaposto tra parentesi con i valori ottenuti durante la creazione della connessione ibrida.
   
   1. `const ns`: spazio dei nomi dell'inoltro. Assicurarsi di usare il nome completo dello spazio dei nomi, ad esempio `{namespace}.servicebus.windows.net`.
   2. `const path`: nome della connessione ibrida.
   3. `const keyrule`: nome della chiave di firma di accesso condiviso.
   4. `const key`: valore della chiave di firma di accesso condiviso.

3. Aggiungere il codice seguente al file `listener.js`:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Il file listener.js sarà simile al seguente:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

