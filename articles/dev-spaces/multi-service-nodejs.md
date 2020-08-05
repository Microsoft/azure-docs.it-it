---
title: 'Esecuzione di più servizi dipendenti: Node.js e Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Questa esercitazione illustra come usare Azure Dev Spaces e Visual Studio Code per eseguire il debug di un'applicazione Node.js multiservizio nel servizio Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, Helm, rete mesh di servizi, routing rete mesh di servizi, kubectl, k8s
ms.custom: devx-track-javascript
ms.openlocfilehash: 7a8f97df976b7e989021308385ef930e539a1e56
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414854"
---
# <a name="running-multiple-dependent-services-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Esecuzione di più servizi dipendenti: Node.js e Visual Studio Code con Azure Dev Spaces

Questa esercitazione illustra lo sviluppo di applicazioni multiservizio con Azure Dev Spaces, insieme ad alcuni vantaggi aggiuntivi offerti da Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Chiamare un servizio in esecuzione in un contenitore separato

In questa sezione si creerà un secondo servizio, `mywebapi`, al quale `webfrontend` assegnerà un nome. Ogni servizio viene eseguito in contenitori separati. Verrà quindi eseguito il debug in entrambi i contenitori.

![Il diagramma mostra il servizio webfrontend che chiama (come indicato da una freccia) il servizio mywebapi.](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Aprire il codice di esempio per *mywebapi*
Il codice di esempio `mywebapi` per questa guida dovrebbe già essere disponibile in una cartella denominata `samples` (in caso contrario, passare a https://github.com/Azure/dev-spaces e selezionare **Clone or Download**, Clona o scarica, per scaricare il repository GitHub). Il codice per questa sezione è disponibile in `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Eseguire *mywebapi*
1. Aprire la cartella `mywebapi` in una *finestra di VS Code separata*.
1. Aprire il **riquadro comandi** (usando il menu **Visualizza | Riquadro comandi**) e usare il completamento automatico per digitare e selezionare questo comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Questo comando non deve essere confuso con il comando `azds prep` che configura il progetto per la distribuzione.
1. Premere F5 e attendere la compilazione e la distribuzione del servizio. Si saprà che è pronto quando viene visualizzato il messaggio *In attesa sulla porta 80* nella console di debug.
1. Prendere nota dell'URL dell'endpoint che sarà simile a `http://localhost:<portnumber>`. **Suggerimento: La barra di stato di VS Code diventa arancione e visualizza un URL selezionabile.** Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nell'ambiente di sviluppo in Azure. Il motivo dell'indirizzo localhost è che `mywebapi` non ha definito alcun endpoint pubblico ed è accessibile solo dall'interno dell'istanza di Kubernetes. Per praticità e per agevolare l'interazione con il servizio privato nel computer locale, Azure Dev Spaces crea un tunnel SSH temporaneo al contenitore in esecuzione in Azure.
1. Quando `mywebapi` è pronto, aprire il browser all'indirizzo localhost. Dovrebbe venire visualizzata una risposta dal servizio `mywebapi` ("Salve da mywebapi").


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Creare una richiesta da *webfrontend* a *mywebapi*
Ora scriviamo il codice in `webfrontend` che crea una richiesta a `mywebapi`.
1. Passare alla finestra di VS Code per `webfrontend`.
1. Aggiungere queste righe di codice nella parte superiore di `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Sostituire* il codice per il gestore GET `/api`. Quando si gestisce una richiesta, questa a sua volta effettua una chiamata a `mywebapi`, quindi restituisce i risultati da entrambi i servizi.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. *Rimuovere* la riga `server.close()` alla fine di `server.js`

L'esempio di codice precedente inoltra l'intestazione `azds-route-as` dalla richiesta in ingresso alla richiesta in uscita. Più avanti si apprenderà come ciò aiuti i team nello sviluppo collaborativo.

### <a name="debug-across-multiple-services"></a>Eseguire il debug tra più servizi
1. A questo punto `mywebapi` dovrebbe ancora essere in esecuzione con il debugger collegato. In caso contrario, premere F5 nel progetto `mywebapi`.
1. Impostare un punto di interruzione nel gestore GET `/`predefinito [alla riga 8 di `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8).
1. Nel progetto `webfrontend`, impostare un punto di interruzione prima che venga inviata una richiesta GET a `http://mywebapi`.
1. Premere F5 nel progetto `webfrontend`.
1. Aprire l'app Web e scorrere il codice in entrambi i servizi. Nell'app Web dovrebbe essere visualizzato un messaggio concatenato dai due servizi: "Salve da webfrontend e Salve da mywebapi."

### <a name="well-done"></a>Ecco fatto!
È ora disponibile un'applicazione multicontenitore in cui ogni contenitore può essere sviluppato e distribuito separatamente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sullo sviluppo in team in Dev Spaces](team-development-nodejs.md)
