---
title: includere file
description: includere file
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fdd4e26839661ab9765b7d496e7f60c3686ba637
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67180384"
---
### <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js

Creare un nuovo file JavaScript denominato `listener.js`.

### <a name="add-the-relay-npm-package"></a>Aggiungere il pacchetto NPM di inoltro

Eseguire `npm install hyco-https` dal prompt dei comandi di Node nella cartella del progetto.

### <a name="write-some-code-to-handle-requests"></a>Scrivere codice per gestire le richieste

1. Aggiungere la costante seguente all'inizio del file `listener.js`.

    ```js
    const https = require('hyco-https');
    ```
2. Aggiungere le costanti seguenti al file `listener.js` per i dettagli della connessione ibrida. Sostituire i segnaposto tra parentesi con i valori ottenuti durante la creazione della connessione ibrida.

   1. `const ns`: spazio dei nomi dell'inoltro. Assicurarsi di usare il nome completo dello spazio dei nomi, ad esempio `{namespace}.servicebus.windows.net`.
   2. `const path`: nome della connessione ibrida.
   3. `const keyrule`: nome della chiave di firma di accesso condiviso.
   4. `const key`: valore della chiave di firma di accesso condiviso.

3. Aggiungere il codice seguente al file `listener.js`. :

    Si noterà che il codice non è molto diverso rispetto a un semplice esempio di server HTTP disponibile nelle esercitazioni per principianti Node.js, con l'eccezione dell'uso di `createRelayedServer` anziché della tipica funzione `createServer`.

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    Il file listener.js sarà simile al seguente:
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

