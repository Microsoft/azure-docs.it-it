---
title: Aggiungere un livello immagine a una mappa | Mappe Microsoft Azure
description: Informazioni su come aggiungere immagini a una mappa. Vedere come usare Azure Maps Web SDK per personalizzare i livelli di immagine e le immagini sovrapposte su set di coordinate fissi.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 4a804272a92d98156122397ee7427dcb467fbdf0
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010226"
---
# <a name="add-an-image-layer-to-a-map"></a>Aggiungere un livello per le immagini a una mappa

Questo articolo illustra come sovrapporre un'immagine a un set fisso di coordinate. Di seguito sono riportati alcuni esempi di tipi di immagini diversi che possono sovrapporsi alle mappe:

* Immagini acquisite dai droni
* Compilazione di planimetrie
* Immagini della mappa cronologica o altre specializzate
* Progetti per i siti dei processi
* Immagini radar meteo

> [!TIP]
> Un [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) è un modo semplice per sovrapporre un'immagine a una mappa. Si noti che i browser potrebbero avere difficoltà a caricare un'immagine di grandi dimensioni. In questo caso, provare a suddividere l'immagine in riquadri e a caricarli nella mappa come [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Il livello immagine supporta i formati di immagine seguenti:

- JPEG
- PNG
- BMP
- GIF (nessuna animazione)

## <a name="add-an-image-layer"></a>Aggiungere un livello per le immagini

Il codice seguente sovrappone un'immagine di una [mappa di Newark, New Jersey, da 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) sulla mappa. Un [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) viene creato passando un URL a un'immagine e le coordinate per i quattro angoli nel formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` .

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Ecco l'esempio di codice completo per l'esecuzione del codice precedente.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le immagini' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Penna <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Livello semplice per le immagini</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importa un file KML come sovrapposizione di base

In questo esempio viene illustrato come aggiungere informazioni sulla sovrapposizione di base KML come livello immagine sulla mappa. Le sovrapposizioni di base KML forniscono coordinate Nord, Sud, est e ovest e una rotazione in senso antiorario. Tuttavia, il livello immagine prevede le coordinate per ogni angolo dell'immagine. La sovrimpressione del terreno KML in questo esempio è per la Cattedrale di Chartres ed è originata da [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Il codice usa la funzione statica della `getCoordinatesFromEdges` classe [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) . Calcola i quattro angoli dell'immagine usando le informazioni relative a nord, Sud, est, ovest e rotazione della sovrimpressione del terreno KML.

<br/>

<iframe height='500' scrolling='no' title='Sovrapposizione di terra KML come livello per le immagini' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Penna <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>Sovrapposizione di terra KML come livello per le immagini</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Personalizzare un livello per le immagini

Il livello immagine include molte opzioni di stile. Ecco uno strumento per provarlo.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello per le immagini' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Penna <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opzioni del livello per le immagini</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](./map-add-tile-layer.md)