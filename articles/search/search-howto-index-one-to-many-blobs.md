---
title: BLOB di indici contenenti più documenti
titleSuffix: Azure Cognitive Search
description: Eseguire la ricerca per indicizzazione di BLOB di Azure per contenuti di testo usando l'indicizzatore BLOB di ricerca di Azure cognitivi, in cui ogni BLOB può restituire uno o più documenti dell'indice
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112273"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indicizzazione di BLOB per produrre più documenti di ricerca
Per impostazione predefinita, un indicizzatore BLOB considererà il contenuto di un BLOB come singolo documento di ricerca. Alcuni valori **parsingMode** supportano scenari in cui un singolo BLOB può produrre più documenti di ricerca. I diversi tipi di **parsingMode** che consentono a un indicizzatore di estrarre più di un documento di ricerca da un BLOB sono:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chiave documento uno-a-molti
Ogni documento visualizzato in un indice di ricerca cognitiva di Azure viene identificato in modo univoco da una chiave del documento. 

Quando non viene specificata alcuna modalità di analisi e non esiste alcun mapping esplicito per il campo chiave nell'indice Azure ricerca cognitiva [esegue](search-indexer-field-mappings.md) automaticamente il mapping `metadata_storage_path` della proprietà come chiave. Questo mapping garantisce che ogni BLOB venga visualizzato come documento di ricerca distinto.

Quando si usa una delle modalità di analisi sopra elencate, un BLOB viene mappato a "molti" documenti di ricerca, rendendo una chiave del documento esclusivamente basata sui metadati del BLOB non idonei. Per ovviare a questo vincolo, Azure ricerca cognitiva è in grado di generare una chiave di documento "uno-a-molti" per ogni singola entità estratta da un BLOB. Questa proprietà è denominata `AzureSearch_DocumentKey` e viene aggiunta a ogni singola entità estratta dal BLOB. Il valore di questa proprietà è sicuramente univoco per ogni singola entità _nei BLOB_ e le entità verranno visualizzate come documenti di ricerca distinti.

Per impostazione predefinita, quando non viene specificato alcun mapping esplicito dei campi per il campo indice `AzureSearch_DocumentKey` chiave, viene eseguito il mapping di `base64Encode` a tale campo, usando la funzione di mapping dei campi.

## <a name="example"></a>Esempio
Si supponga di avere una definizione di indice con i campi seguenti:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E il contenitore BLOB contiene BLOB con la struttura seguente:

_Blob1. JSON_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2. JSON_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando si crea un indicizzatore e si imposta **parsingMode** su `jsonLines` -senza specificare alcun mapping esplicito dei campi per il campo chiave, il mapping seguente verrà applicato in modo implicito
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Questa installazione determinerà l'indice del ricerca cognitiva di Azure contenente le informazioni seguenti (ID con codifica Base64 abbreviato per brevità)

| id | temperatura | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapping dei campi personalizzati per il campo chiave di indice

Supponendo la stessa definizione di indice dell'esempio precedente, si supponga che il contenitore BLOB disponga di BLOB con la struttura seguente:

_Blob1. JSON_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2. JSON_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando si crea un indicizzatore con `delimitedText` **parsingMode**, potrebbe sembrare naturale impostare una funzione di mapping dei campi nel campo chiave come indicato di seguito:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Tuttavia, questo mapping _non_ comporterà la visualizzazione di 4 documenti nell'indice, perché il `recordid` campo non è univoco _tra i BLOB_. È quindi consigliabile usare il mapping di campi implicito applicato dalla `AzureSearch_DocumentKey` proprietà al campo indice chiave per le modalità di analisi "uno-a-molti".

Se si vuole impostare un mapping di campi esplicito, assicurarsi che _campoOrigine_ sia distinto per ogni singola entità **in tutti i BLOB**.

> [!NOTE]
> L'approccio utilizzato da `AzureSearch_DocumentKey` per garantire l'univocità per entità estratta è soggetto a modifiche e pertanto non è necessario basarsi sul valore per le esigenze dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se non si ha già familiarità con la struttura di base e il flusso di lavoro di indicizzazione BLOB, è consigliabile esaminare prima di tutto l' [indicizzazione dell'archiviazione BLOB di Azure con azure ricerca cognitiva](search-howto-index-json-blobs.md) Per altre informazioni sulle modalità di analisi per i diversi tipi di contenuto BLOB, vedere gli articoli seguenti.

> [!div class="nextstepaction"]
> [Indicizzazione](search-howto-index-csv-blobs.md)
> di BLOB CSV indicizzazione di[BLOB JSON](search-howto-index-json-blobs.md)
