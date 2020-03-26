---
title: Funzionalità e sintassi supportate dell'API di Azure Cosmos DB per MongoDB (versione 3.2)
description: Informazioni su funzionalità e sintassi supportate dell'API di Azure Cosmos DB per MongoDB (versione 3.2).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 12e5dba0339b6092564e5d35c1a6250b0c47f50f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "72754994"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>API di Azure Cosmos DB per MongoDB (versione 3.2): funzionalità e sintassi supportate

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile comunicare con l'API di Azure Cosmos DB per MongoDB tramite uno dei [driver](https://docs.mongodb.org/ecosystem/drivers) open source del client MongoDB. L'API di Azure Cosmos DB per MongoDB consente di usare driver client esistenti aderendo al [protocollo di collegamento](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Usando l'API di Azure Cosmos DB per MongoDB è possibile sfruttare i noti vantaggi di MongoDB con tutte le funzionalità aziendali offerte da Cosmos DB: [distribuzione globale](distribute-data-globally.md), [partizionamento orizzontale automatico](partition-data.md), garanzie di disponibilità e latenza, indicizzazione automatica di ogni campo, crittografia di dati inattivi, backup e molto altro.

> [!NOTE]
> Questo articolo riguarda l'API di Azure Cosmos DB per MongoDB 3.2. Per la versione MongoDB 3.6, vedere [Funzionalità e sintassi supportate di MongoDB 3.6](mongodb-feature-support-36.md).

## <a name="protocol-support"></a>Protocolli supportati

Tutti i nuovi account per l'API di Azure Cosmos DB per MongoDB sono compatibili con la versione **3.6** del server MongoDB. Questo articolo riguarda la versione 3.2 di MongoDB. Gli operatori supportati con i relativi limiti ed eccezioni sono elencati di seguito. I driver client che identificano questi protocolli dovrebbero essere in grado di collegarsi all'API di Cosmos DB per MongoDB.

## <a name="query-language-support"></a>Linguaggi di query supportati

L'API di Azure Cosmos DB per MongoDB offre il supporto completo dei costrutti del linguaggio di query MongoDB. Di seguito è possibile trovare l'elenco dettagliato di operazioni, operatori, fasi, comandi e opzioni attualmente supportati.

## <a name="database-commands"></a>Comandi del database

L'API di Azure Cosmos DB per MongoDB supporta i comandi di database seguenti:

### <a name="query-and-write-operation-commands"></a>Comandi per le operazioni di query e scrittura

- delete
- trovare
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Comandi di autenticazione

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Comandi di amministrazione

- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Comandi di diagnostica

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Pipeline di aggregazione</a>

Cosmos DB supporta la pipeline di aggregazione per MongoDB 3.2 nella versione di anteprima pubblica. Per istruzioni sull'onboarding nell'anteprima pubblica, vedere il [blog di Azure](https://aka.ms/mongodb-aggregation).

### <a name="aggregation-commands"></a>Comandi di aggregazione

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Fasi di aggregazione

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Espressioni di aggregazione

#### <a name="boolean-expressions"></a>Espressioni booleane

- $and
- $or
- $not

#### <a name="set-expressions"></a>Espressioni Set

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Espressioni di confronto

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Espressioni aritmetiche

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Espressioni stringa

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Espressioni di matrice

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Espressioni di data

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Espressioni condizionali

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Accumulatori di aggregazione

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operatori

Sono supportati gli operatori seguenti con esempi di uso. Vedere questo documento di esempio usato nelle query seguenti:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operatore | Esempio |
--- | --- |
$eq | `{ "Volcano Name": { $eq: "Rainier" } }` |  | -
$gt | `{ "Elevation": { $gt: 4000 } }` |  | -
$gte | `{ "Elevation": { $gte: 4392 } }` |  | -
$lt | `{ "Elevation": { $lt: 5000 } }` |  | -
$lte | `{ "Elevation": { $lte: 5000 } }` | | -
$ne | `{ "Elevation": { $ne: 1 } }` |  | -
$in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |  | -
$nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` | | -
$or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$not | `{ "Elevation": { $not: { $gt: 5000 } } }`|  | -
$nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |  | -
$exists | `{ "Status": { $exists: true } }`|  | -
$type | `{ "Status": { $type: "string" } }`|  | -
$mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` |  | -
$regex | `{ "Volcano Name": { $regex: "^Rain"} }`|  | -

### <a name="notes"></a>Note

Nelle query $regex, le espressioni ancorate a sinistra consentono la ricerca nell'indice. L'uso del modificatore 'i' (senza distinzione tra maiuscole e minuscole) e 'm' (su più righe) provoca l'analisi della raccolta in tutte le espressioni.
Quando è necessario includere '$' o '|' è consigliabile creare due o più query regex.
La query originale ```find({x:{$regex: /^abc$/})``` deve essere ad esempio modificata in ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
La prima parte userà l'indice per limitare la ricerca ai documenti che iniziano con ^abc, mentre la seconda parte individuerà le voci esatte.
L'operatore barra '|' funge da funzione "or". La query ```find({x:{$regex: /^abc|^def/})``` individua i documenti in cui il campo 'x' ha un valore che inizia con "abc" o "def". Per usare l'indice è consigliabile per suddividere la query in due query diverse unite dall'operatore $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Operatori di aggiornamento

#### <a name="field-update-operators"></a>Operatori di aggiornamento di campo

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operatori di aggiornamento di matrice

- $addToSet
- $pop
- $pullAll
- $pull (nota: $pull con una condizione non è supportato)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Operatore di aggiornamento bit per bit

- $bit

### <a name="geospatial-operators"></a>Operatori geospaziali

Operatore | Esempio | |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sì |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sì |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sì |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sì |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sì |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Sì |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sì |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Sì |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sì |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Sì |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sì |

## <a name="sort-operations"></a>Operazioni di ordinamento

Quando si usa l'operazione `findOneAndUpdate`, sono supportate le operazioni di ordinamento in un singolo campo, ma non quelle su più campi.

## <a name="additional-operators"></a>Altri operatori

Operatore | Esempio | Note
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | Non supportato. In alternativa, usare $regex.

## <a name="unsupported-operators"></a>Operatori non supportati

Gli operatori ```$where``` e ```$eval``` non sono supportati da Azure Cosmos DB.

### <a name="methods"></a>Metodi

Sono supportati i metodi seguenti:

#### <a name="cursor-methods"></a>Metodi di cursore

Metodo | Esempio | Note
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | I documenti senza chiave di ordinamento non vengono restituiti

## <a name="unique-indexes"></a>Indici univoci

Cosmos DB indicizza tutti i campi dei documenti scritti nel database per impostazione predefinita. Gli indici univoci assicurano che un campo specifico non abbia valori duplicati in tutti i documenti di una raccolta, in modo simile alla preservazione dell'univocità per la chiave `_id` predefinita. È possibile creare indici personalizzati in Cosmos DB usando il comando createIndex, includendo il vincolo 'unique'.

Gli indici univoci sono disponibili per tutti gli account Cosmos tramite l'API di Azure Cosmos DB per MongoDB.

## <a name="time-to-live-ttl"></a>Durata (TTL)

Cosmos DB supporta una durata (TTL) in base al timestamp del documento. La durata può essere abilitata per le raccolte passando al [portale di Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Gestione di utenti e ruoli

Cosmos DB non supporta ancora utenti e ruoli. Cosmos DB, tuttavia, supporta il controllo degli accessi in base al ruolo e chiavi/password di lettura/scrittura e sola lettura ottenibili tramite il [portale di Azure](https://portal.azure.com) nella pagina Stringa di connessione.

## <a name="replication"></a>Replica

Cosmos DB supporta la replica automatica e nativa ai livelli più bassi. Questa logica viene estesa per ottenere anche una replica globale a bassa latenza. Cosmos DB non supporta comandi di replica manuali.

## <a name="write-concern"></a>Write concern

Alcune applicazioni usano un [write concern](https://docs.mongodb.com/manual/reference/write-concern/) che definisce il numero di risposte necessarie durante un'operazione di scrittura. A causa della modalità in cui Cosmos DB gestisce la replica in background, per impostazione predefinita tutte le operazioni di scrittura sono automaticamente Quorum. Qualsiasi write concern specificato dal codice client viene ignorato. Per altre informazioni, vedere [Uso dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni](consistency-levels.md).

## <a name="sharding"></a>Partizionamento orizzontale

Azure Cosmos DB supporta il partizionamento orizzontale automatico lato server. Gestisce automaticamente la creazione, la selezione e il bilanciamento delle partizioni. Azure Cosmos DB non supporta i comandi di partizionamento orizzontale manuali, ovvero non è necessario richiamare comandi come pshardCollection, addShard, balancerStart, moveChunk e così via. È sufficiente specificare la chiave di partizione durante la creazione dei contenitori o l'esecuzione di query sui dati.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.

<sup>Nota: Questo articolo illustra una funzionalità di Azure Cosmos DB che fornisce la compatibilità del protocollo di collegamento con i database di MongoDB. Microsoft non esegue database MongoDB per fornire questo servizio. Azure Cosmos DB non è affiliato a MongoDB, Inc.</sup>
