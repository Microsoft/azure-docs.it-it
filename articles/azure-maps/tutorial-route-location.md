---
title: 'Esercitazione: Trovare il percorso per una località | Mappe di Microsoft Azure'
description: In questa esercitazione viene illustrato come eseguire il rendering del percorso verso una località (punto di interesse) su una mappa usando il servizio di pianificazione percorso di Mappe di Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 98c36176ecd2996e5f735c52017162a076ef4bde
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333771"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Esercitazione: Trovare il percorso per raggiungere un punto di interesse usando Mappe di Azure

Questa esercitazione illustra come usare l'account Mappe di Azure e l'SDK del servizio di pianificazione percorso per trovare il percorso per raggiungere un determinato punto di interesse. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Impostare le coordinate di un indirizzo
> * Inviare una query al servizio di pianificazione percorso per ottenere le indicazioni stradali per un punto di interesse

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare, creare una sottoscrizione con il piano tariffario S1 seguendo le istruzioni riportate in [Creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps). Per ottenere la chiave primaria per l'account, seguire la procedura illustrata in [Ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account). Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](how-to-manage-authentication.md).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Creare una nuova mappa

La procedura seguente illustra come creare una pagina HTML statica incorporata usando l'API del controllo mappa.

1. Nel computer locale creare un nuovo file con il nome **MapRoute.html**.
2. Aggiungere al file i componenti HTML seguenti:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    Si noti che l'intestazione HTML include i file di risorse CSS e JavaScript ospitati dalla libreria del controllo mappa di Azure. Notare l'evento `onload` nel corpo della pagina, che chiamerà la funzione `GetMap` dopo il caricamento del corpo della pagina. Questa funzione deve contenere il codice JavaScript inline per l'accesso alle API di Mappe di Azure. 

3. Aggiungere il codice JavaScript seguente alla funzione `GetMap`. Sostituire la stringa `<Your Azure Maps Key>` con la chiave primaria copiata dall'account Mappe.

    ```JavaScript
   //Instantiate a map object
   var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
           authType: 'subscriptionKey',
           subscriptionKey: '<Your Azure Maps Key>'
        }
   });
   ```

    `atlas.Map` fornisce il controllo per una mappa Web visiva e interattiva ed è un componente dell'API del controllo mappa di Azure.

4. Salvare il file e aprirlo nel browser. A questo punto si ha una mappa di base che è possibile sviluppare ulteriormente.

   ![Visualizzare la mappa di base](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definire il rendering dell'itinerario

In questa esercitazione verrà eseguito il rendering di un itinerario semplice usando un'icona di simbolo per l'inizio e la fine dell'itinerario e una linea per il percorso dell'itinerario.

1. Dopo aver inizializzato la mappa, aggiungere il codice JavaScript seguente.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    Nel gestore dell'evento `ready` delle mappe viene creata un'origine dati per archiviare la linea relativa all'itinerario e i punti di partenza e di arrivo. Viene creato un livello linea che viene allegato all'origine dati per definire il rendering della linea dell'itinerario. Il rendering della linea dell'itinerario verrà eseguito con una sfumatura di blu. Avrà una larghezza di cinque pixel, giunzioni di linee arrotondate e le estremità. Quando si aggiunge il livello alla mappa, viene passato un secondo parametro con valore `'labels'`, per indicare che il rendering di questo livello deve essere eseguito sotto le etichette della mappa. In questo modo la linea dell'itinerario non coprirà le etichette delle strade. Viene creato un livello simboli che viene allegato all'origine dati. Questo livello specifica la modalità di rendering del punto di partenza e del punto di arrivo. In questo caso sono state aggiunte espressioni per recuperare l'immagine dell'icona e le informazioni sull'etichetta di testo dalle proprietà di ogni oggetto punto. 
    
2. Per questa esercitazione impostare Microsoft come punto di partenza e una stazione di rifornimento a Seattle come punto di destinazione. Nel gestore dell'evento `ready` delle mappe aggiungere il codice seguente.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Questo codice crea due [oggetti punto GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) per rappresentare i punti di partenza e di arrivo dell'itinerario e aggiunge i punti all’origine dati. A ogni punto vengono aggiunge le proprietà `title` e `icon`. L'ultimo blocco imposta la visualizzazione della fotocamera tramite le informazioni di latitudine e longitudine del punto di partenza e di arrivo tramite la proprietà [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa.

3. Salvare il file **MapRoute.html** e aggiornare il browser. Ora la mappa è centrata su Seattle ed è possibile vedere il segnaposto blu che indica il punto di partenza e il segnaposto blu che indica il punto di arrivo.

   ![Visualizzare il punto di partenza e di arrivo di un percorso sulla mappa](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Ottenere le indicazioni stradali

Questa sezione mostra come usare l'API del servizio di pianificazione percorso di Mappe di Azure. L'API del servizio di pianificazione percorso trova l'itinerario dal punto di partenza specificato al punto di arrivo. In questo servizio sono disponibili le API per pianificare il percorso *più veloce*, *più breve*, *più ecologico* o *più interessante* tra due posizioni. Consente inoltre agli utenti di pianificare percorsi per il futuro tramite il database dei dati storici sul traffico di Azure che fornisce previsioni della durata dei percorsi per qualsiasi giorno e ora. Per altre informazioni, vedere [Get route directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Ottenere le indicazioni stradali). Tutte le funzionalità seguenti devono essere aggiunte **all'interno del listener ready della mappa** per garantire che vengano caricate quando le risorse della mappa sono pronte per l'accesso.

1. Nella funzione GetMap aggiungere il codice JavaScript seguente.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` crea un elemento `SubscriptionKeyCredentialPolicy` per autenticare le richieste HTTP in Mappe di Azure con la chiave di sottoscrizione. `atlas.service.MapsURL.newPipeline()` acquisisce il criterio `SubscriptionKeyCredential` e crea un'istanza [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). `routeURL` rappresenta un URL per le operazioni di [pianificazione del percorso](https://docs.microsoft.com/rest/api/maps/route) di Mappe di Azure.

2. Dopo aver configurato le credenziali e l'URL, aggiungere il codice JavaScript seguente per creare il percorso dal punto iniziale a quello finale. `routeURL` chiede al servizio di pianificazione percorso Mappe di Azure di calcolare le indicazioni. Dalla risposta viene estratta una raccolta di funzionalità GeoJSON con il metodo `geojson.getFeatures()` e viene aggiunta all'origine dati.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Salvare il file **MapRoute.html** e aggiornare il Web browser. Se la connessione con le API di Mappe è stata stabilita correttamente, verrà visualizzata una mappa simile alla seguente.

    ![Controllo mappa e servizio di pianificazione percorso di Azure](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare una nuova pagina Web usando l'API del controllo mappa
> * Impostare le coordinate di un indirizzo
> * Inviare una query al servizio di pianificazione percorso per ottenere le indicazioni stradali per un punto di interesse

> [!div class="nextstepaction"]
> [Visualizzare il codice sorgente completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Visualizzare l'esempio in tempo reale](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

L'esercitazione successiva illustra come creare una query del percorso con restrizioni, ad esempio la modalità di trasporto o il tipo di carico, e come visualizzare più percorsi sulla mappa stessa.

> [!div class="nextstepaction"]
> [Find routes for different modes of travel (Trovare i percorsi per diverse modalità di trasporto)](./tutorial-prioritized-routes.md)
