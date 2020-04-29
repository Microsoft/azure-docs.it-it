---
title: Filtri eventi per Griglia di eventi di Azure
description: Questo articolo illustra come filtrare gli eventi quando si crea una sottoscrizione di Griglia di eventi di Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: spelluru
ms.openlocfilehash: ab5dd716253875e4a992b94a4e143cb3e806a4b0
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509653"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Informazioni sui filtri eventi per le sottoscrizioni di Griglia di eventi

Questo articolo descrive i diversi modi per filtrare gli eventi che vengono inviati all'endpoint. Quando si crea una sottoscrizione di eventi, sono disponibili tre opzioni di filtro:

* Tipi di eventi
* L'oggetto inizia o termina con
* Campi e operatori avanzati

## <a name="event-type-filtering"></a>Filtro in base al tipo di evento

Per impostazione predefinita, tutti i [tipi di evento](event-schema.md) dell'origine eventi vengono inviati all'endpoint. È possibile decidere di inviare all'endpoint solo determinati tipi di evento. È ad esempio possibile ricevere le notifiche degli aggiornamenti alle risorse, ma non quelle relative ad altre operazioni, come le eliminazioni. In tal caso, filtrare in base al tipo di evento `Microsoft.Resources.ResourceWriteSuccess`. Fornire una matrice con i tipi di evento o specificare `All` per ottenere tutti i tipi di evento per l'origine eventi.

La sintassi JSON per l'applicazione di un filtro in base al tipo di evento è la seguente:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtro in base all'oggetto

Per applicare un filtro semplice in base all'oggetto, specificare un valore iniziale o finale per l'oggetto. È ad esempio possibile specificare che l'oggetto termina con `.txt` per ottenere solo gli eventi correlati al caricamento di un file di testo in un account di archiviazione. È anche possibile specificare che l'oggetto inizia con `/blobServices/default/containers/testcontainer` per ottenere tutti gli eventi per il contenitore specificato, ma non per altri contenitori nell'account di archiviazione.

Quando si pubblicano eventi per argomenti personalizzati, creare oggetti per gli eventi che facilitino i sottoscrittori a capire se sono interessati nell'evento. I sottoscrittori usano la proprietà subject per filtrare e instradare gli eventi. È consigliabile aggiungere il percorso in cui si è verificato l'evento, in modo che i sottoscrittori possano filtrare in base ai segmenti di tale percorso. Il percorso consente ai sottoscrittori di filtrare gli eventi a seconda della dimensione. Se ad esempio si specifica un percorso di tre segmenti, come `/A/B/C` nell'oggetto, i sottoscrittori possono filtrare in base al primo segmento `/A` per ottenere un ampio set di eventi. Tali sottoscrittori ricevono eventi con oggetti come `/A/B/C` o `/A/D/E`. Altri sottoscrittori possono filtrare in base a `/A/B` per ottenere un set di eventi più ristretto.

La sintassi JSON per il filtro in base al soggetto è:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtro avanzato

Per filtrare i valori nei campi dati e specificare l'operatore di confronto, usare l'opzione di filtro avanzato. Quando si applica un filtro avanzato si specificano gli elementi seguenti:

* operator type: il tipo di confronto.
* key: il campo nei dati dell'evento che viene usato per il filtro. Può essere un numero, un valore booleano o una stringa.
* values: valore o valori da confrontare con la chiave.

Se si specifica un singolo filtro con più valori, viene eseguita un'operazione **o** , pertanto il valore del campo chiave deve essere uno di questi valori. Esempio:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Se si specificano più filtri diversi, viene eseguita un'operazione and, quindi è necessario soddisfare ogni condizione **di** filtro. Esempio: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>Operatori

Gli operatori disponibili per i **numeri** sono:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

L'operatore disponibile per i **valori booleani** è: 
- BoolEquals

Gli operatori disponibili per le **stringhe** sono:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Tutti i confronti di stringhe **non** fanno distinzione tra maiuscole e minuscole

### <a name="key"></a>Chiave

Per gli eventi nello schema di Griglia di eventi, usare i valori seguenti per l'elemento key:

* ID
* Argomento
* Oggetto
* EventType
* DataVersion
* Dati dell'evento (ad esempio, Data.key1)

Per gli eventi nello schema di Eventi cloud, usare i valori seguenti per l'elemento key:

* EventId
* Source (Sorgente)
* EventType
* EventTypeVersion
* Dati dell'evento (ad esempio, Data.key1)

Per uno schema di input personalizzato, usare i campi dati degli eventi (ad esempio, Data.key1).

### <a name="values"></a>Valori

I valori possibili sono i seguenti.

* d'acquisto
* stringa
* boolean
* array

### <a name="limitations"></a>Limitazioni

I filtri avanzati presentano le limitazioni seguenti:

* Cinque filtri avanzati per ogni sottoscrizione di Griglia di eventi
* 512 caratteri per ogni valore stringa
* Cinque valori per gli operatori **in** e **not in**

È possibile usare l'elemento key in più filtri.

### <a name="examples"></a>Esempi

### <a name="stringcontains"></a>StringContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>StringIn

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>NumberIn

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'applicazione di filtri agli eventi con PowerShell e l'interfaccia della riga di comando di Azure, vedere [Filtrare gli eventi per Griglia di eventi](how-to-filter-events.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
