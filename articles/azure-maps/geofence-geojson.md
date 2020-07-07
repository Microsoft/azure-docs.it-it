---
title: Formato dati GeoJSON per Geofence | Mappe Microsoft Azure
description: In questo articolo si apprenderà come preparare i dati di georecinzione che possono essere usati nell'API Microsoft Azure Maps GET e POST geofence.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335609"
---
# <a name="geofencing-geojson-data"></a>Geofencing dei dati GeoJSON

Le API delle Mappe di Azure [Recinto virtuale GET](/rest/api/maps/spatial/getgeofence) e [Recinto virtuale POST](/rest/api/maps/spatial/postgeofence) consentono di recuperare la prossimità di una coordinata relativa a un recinto virtuale specificato o un set di recinti. Questo articolo illustra come preparare i dati del recinto virtuale che possono essere usati nelle API delle Mappe di Azure GET e POST.

I dati per recinto virtuale o set di recinti virtuali sono rappresentati dall'oggetto `Feature` e oggetto `FeatureCollection` nel formato`GeoJSON`, che viene definito in [rfc7946](https://tools.ietf.org/html/rfc7946). Inoltre:

* Il tipo di oggetto GeoJSON può essere un oggetto `Feature` o un oggetto `FeatureCollection`.
* Il tipo di oggetto Geometry può essere un `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`, e `GeometryCollection`.
* Tutte le proprietà della funzionalità devono contenere un `geometryId`, che viene usato per l'identificazione del recinto virtuale.
* La funzionalità con `Point`, `MultiPoint`, `LineString`, `MultiLineString` deve contenere `radius` nelle proprietà. Il valore `radius` viene misurato in metri, il valore `radius` è compreso tra 1 e 10000.
* La funzionalità con il tipo di geometria `polygon` e `multipolygon` dispone di una proprietà di raggio.
* `validityTime` è una proprietà facoltativa che consente all'utente di impostare un'ora di scadenza e la validità del periodo di tempo per i dati del recinto virtuale. Se non specificata, i dati non scadano mai ed sono sempre validi.
* `expiredTime` è la data e l'ora di scadenza dei dati di geofencing. Se il valore di `userTime` nella richiesta è successivo rispetto a tale valore, i dati corrispondenti del recinto virtuale sono considerati come dati scaduti e non viene eseguita una query. Su cui, il geometryId dei dati di questo recinto virtuale sarà incluso nella matrice `expiredGeofenceGeometryId` all'interno della risposta del recinto virtuale.
* `validityPeriod` è un elenco del periodo di validità del recinto virtuale. Se il valore di `userTime` nella richiesta non rientra nel periodo di validità, i dati corrispondenti del recinto virtuale vengono considerati come non validi e non viene eseguita una query. Il geometryId dei dati di questo recinto virtuale è incluso nella matrice `invalidPeriodGeofenceGeometryId` all'interno della risposta del recinto virtuale. Nella tabella seguente vengono illustrate le proprietà dell'elemento validityPeriod.

| Nome | Type | Obbligatoria  | Descrizione |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | Data e ora di inizio del periodo di validità. |
| endTime   | Datetime  | true |  Data e ora di fine del periodo di validità. |
| recurrenceType | string | false |   Il tipo di ricorrenza del periodo. Il valore può essere `Daily`, `Weekly`, `Monthly` o `Yearly`. Il valore predefinito è `Daily`.|
| businessDayOnly | Boolean | false |  Indica se i dati sono validi solo durante i giorni lavorativi. Il valore predefinito è `false`.|


* Tutti i valori delle coordinate sono rappresentati come [Longitudine, latitudine] definito in `WGS84` .
* Per ciascuna funzionalità, che contiene `MultiPoint`, `MultiLineString`, `MultiPolygon` o `GeometryCollection`, le proprietà vengono applicate a tutti gli elementi. ad esempio: tutti i punti in `MultiPoint` utilizzeranno lo stesso raggio per formare una georecinzione a più cerchi.
* In uno scenario punto-cerchio, una geometria circolare può essere rappresentata usando un oggetto di geometria `Point` con proprietà elaborate nell'[Estensione delle geometrie GeoJSON](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Di seguito è riportato un esempio del corpo della richiesta per un recinto virtuale rappresentato come una geometria circolare del recinto virtuale in `GeoJSON` usando un punto centrale e un raggio. Il periodo di validità dei dati del recinto virtuale inizia dalle ore 9 alle 17 del 22.10.2018, ripetuto ogni giorno, ad eccezione dei fine settimana. `expiredTime` indica che i dati del recinto virtuale verranno considerati scaduti, se `userTime` nella richiesta è successivo a `2019-01-01`.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
