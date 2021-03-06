---
title: Visualizzare le indicazioni del percorso su una mappa | Mappe di Microsoft Azure
description: Questo articolo illustra come visualizzare le indicazioni stradali tra due punti di una mappa con Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9d0197a16c8074ce961c2b403724149929f566f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890718"
---
# <a name="show-directions-from-a-to-b"></a>Visualizzare le indicazioni stradali da A a B

Questo articolo illustra come effettuare una richiesta di pianificazione percorso e visualizzare il percorso sulla mappa.

È possibile eseguire questa operazione in due modi. Il primo modo consiste nell'eseguire una query all'[API di pianificazione percorso di Mappe di Azure](/rest/api/maps/route/getroutedirections) tramite un modulo del servizio. Il secondo consiste nell'usare l'[API Fetch](https://fetch.spec.whatwg.org/) per effettuare una richiesta di ricerca all'[API Route di Mappe di Azure](/rest/api/maps/route/getroutedirections). Entrambe le modalità vengono descritte di seguito.

## <a name="query-the-route-via-service-module"></a>Eseguire una query del percorso tramite il modulo del servizio

<iframe height='500' scrolling='no' title='Visualizzare le indicazioni stradali da A a B su una mappa (Modulo del servizio)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Show directions from A to B on a map</a> (Visualizzare le indicazioni stradali da A a B su una mappa (Modulo del servizio)) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco costruisce un oggetto mappa e imposta il meccanismo di autenticazione per l'uso del token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un oggetto `TokenCredential` per autenticare le richieste HTTP a Mappe di Azure con il token di accesso. Passa quindi `TokenCredential` a `atlas.service.MapsURL.newPipeline()` e crea un'istanza di [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline). `routeURL` rappresenta un URL per le operazioni di [pianificazione del percorso](/rest/api/maps/route) di Mappe di Azure.

Il terzo blocco di codice crea e aggiunge un oggetto [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) alla mappa.

Il quarto blocco di codice crea due oggetti [point](/javascript/api/azure-maps-control/atlas.data.point) (uno iniziale e uno finale) e li aggiunge all'oggetto dataSource.

Una linea è un oggetto [Feature](/javascript/api/azure-maps-control/atlas.data.feature) per LineString. [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) esegue il rendering degli oggetti linea con wrapping in [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) come linee sulla mappa. Il quarto blocco di codice crea e aggiunge un livello linea alla mappa. Vedere le proprietà di un livello linea in [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Un [livello simbolo](/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa testi o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource). Il rendering dei testi o delle icone viene eseguito come simboli sulla mappa. Il quinto blocco di codice crea e aggiunge un livello simbolo alla mappa.

Il sesto blocco di codice esegue query sul servizio di pianificazione percorso di Mappe di Azure, che fa parte del [modulo del servizio](how-to-use-services-module.md). Il metodo [calculateRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) di RouteURL viene usato per ottenere un percorso tra i punti iniziale e finale. Dalla risposta viene estratta una raccolta di funzionalità GeoJSON con il metodo `geojson.getFeatures()` e viene aggiunta all'origine dati. Viene quindi eseguito il rendering della risposta come un percorso sulla mappa. Per altre informazioni sull'aggiunta di una linea alla mappa, vedere [Aggiungere una linea](map-add-line-layer.md).

L'ultimo blocco di codice imposta i limiti della mappa usando la proprietà [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa.

La query sul percorso, l'origine dati, il simbolo, i livelli linea e i limiti della fotocamera vengono creati all'interno del [listener di eventi](/javascript/api/azure-maps-control/atlas.map#events). Questa struttura di codice assicura che i risultati vengano visualizzati solo dopo il caricamento completo della mappa.

## <a name="query-the-route-via-fetch-api"></a>Eseguire query sul percorso tramite l'API Fetch

<iframe height='500' scrolling='no' title='Visualizzare le indicazioni stradali da A a B su una mappa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a> (Visualizzare le indicazioni stradali da A a B su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa e imposta il meccanismo di autenticazione per l'uso del token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea e aggiunge un oggetto [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) alla mappa.

Il terzo blocco di codice crea i punti di inizio e di destinazione per il percorso e quindi li aggiunge all'origine dati. Per istruzioni sull'uso di [addPins](/javascript/api/azure-maps-control/atlas.map), è possibile vedere [Aggiungere i segnaposto alla mappa](map-add-pin.md).

[LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) esegue il rendering degli oggetti linea con wrapping in [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) come linee sulla mappa. Il quarto blocco di codice crea e aggiunge un livello linea alla mappa. Vedere le proprietà di un livello linea in [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions).

Un [livello simbolo](/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) come simboli sulla mappa. Il quinto blocco di codice crea e aggiunge un livello simbolo alla mappa. Vedere le proprietà di un livello simbolo in [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Il blocco di codice successivo crea i punti `SouthWest` e `NorthEast` dai punti di inizio e di destinazione e imposta i limiti della mappa tramite la proprietà [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa.

L'ultimo blocco di codice usa l'[API Fetch](https://fetch.spec.whatwg.org/) per effettuare una richiesta di ricerca all'[API Route di Mappe di Azure](/rest/api/maps/route/getroutedirections). La risposta viene quindi analizzata. Se la risposta ha esito positivo, le informazioni di latitudine e longitudine vengono usate per creare una matrice, ovvero una linea, connettendo tali punti. I dati della linea vengono quindi aggiunti all'origine dati per eseguire il rendering del percorso sulla mappa. Per le istruzioni, è possibile vedere [add a line on the map](map-add-line-layer.md) (Aggiungere una linea sulla mappa).

La query sul percorso, l'origine dati, il simbolo, i livelli linea e i limiti della fotocamera vengono creati all'interno del [listener di eventi](/javascript/api/azure-maps-control/atlas.map#events). Anche in questo caso, è necessario assicurarsi che i risultati vengano visualizzati dopo il caricamento completo della mappa.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per l'uso del servizio di pianificazione percorso](how-to-use-best-practices-for-search.md)

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](/javascript/api/azure-maps-control/atlas.map)

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Mostrare il traffico sulla mappa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interazione con la mappa - eventi del mouse](./map-events.md)