---
title: Filtro avanzato-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: Filtro avanzato in griglia di eventi in IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001049"
---
# <a name="advanced-filtering"></a>Filtro avanzato
Griglia di eventi consente di specificare i filtri per qualsiasi proprietà nel payload JSON. Questi filtri sono modellati come set di `AND` condizioni, con ogni condizione esterna con condizioni interne facoltative `OR` . Per ogni `AND` condizione specificare i valori seguenti:

* `OperatorType` : Tipo di confronto.
* `Key` : Percorso JSON della proprietà su cui applicare il filtro.
* `Value` : Valore di riferimento sul quale viene eseguito il filtro (o) `Values` , ovvero il set di valori di riferimento rispetto al quale viene eseguito il filtro.

## <a name="json-syntax"></a>Sintassi JSON

La sintassi JSON per un filtro avanzato è la seguente:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Applicazione di filtri ai valori di matrice

Griglia di eventi non supporta attualmente l'applicazione di filtri a una matrice di valori. Se un evento in ingresso ha un valore di matrice per la chiave del filtro avanzato, l'operazione di corrispondenza ha esito negativo. L'evento in ingresso non corrisponde alla sottoscrizione di eventi.

## <a name="and-or-not-semantics"></a>Semantica AND-OR-NOT

Si noti che nell'esempio JSON riportato in precedenza `AdvancedFilters` è una matrice. Si pensi a ogni `AdvancedFilter` elemento della matrice come una `AND` condizione.

Per gli operatori che supportano più valori, ad esempio `NumberIn` , `NumberNotIn` , `StringIn` e così via, ogni valore viene considerato come una `OR` condizione. Un oggetto `StringBeginsWith("a", "b", "c")` corrisponderà quindi a qualsiasi valore stringa che inizia con `a` o `b` o `c` .

> [!CAUTION]
> Gli operatori NOT `NumberNotIn` e si `StringNotIn` comportano come condizioni e per ogni valore specificato nel `Values` campo.
>
> Se non si esegue questa operazione, il filtro Accept-All filtro e ne dedurrà lo scopo.

## <a name="floating-point-rounding-behavior"></a>Comportamento di arrotondamento a virgola mobile

Griglia di eventi usa il `decimal` tipo .NET per gestire tutti i valori numerici. Il numero di valori specificati nel file JSON della sottoscrizione di eventi non è soggetto a un comportamento di arrotondamento a virgola mobile.

## <a name="case-sensitivity-of-string-filters"></a>Distinzione maiuscole/minuscole dei filtri stringa

Tutti i confronti di stringhe non fanno distinzione tra maiuscole e minuscole. Attualmente non è possibile modificare questo comportamento.

## <a name="allowed-advanced-filter-keys"></a>Chiavi di filtro avanzate consentite

La `Key` proprietà può essere una proprietà di primo livello nota o essere un percorso JSON con più punti, dove ogni punto indica l'esecuzione di un'istruzione in un oggetto JSON annidato.

Griglia di eventi non ha un significato speciale per il `$` carattere nella chiave, a differenza della specifica JSONPath.

### <a name="event-grid-schema"></a>Schema griglia di eventi

Per gli eventi nello schema di griglia di eventi:

* ID
* Argomento
* Oggetto
* EventType
* DataVersion
* Data. Prop1
* Data. prop * Prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>Schema di eventi personalizzati

Non esiste alcuna restrizione `Key` in uno schema di evento personalizzato perché griglia di eventi non impone alcuno schema della busta nel payload.

## <a name="numeric-single-value-filter-examples"></a>Esempi di filtri a valore singolo numerici

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Intervalli numerici-esempi di filtro valore

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Intervallo di stringhe-esempi di filtro valore

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Esempi di filtri a valore singolo booleani

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
