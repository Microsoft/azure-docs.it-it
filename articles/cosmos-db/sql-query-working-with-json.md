---
title: Uso di JSON in Azure Cosmos DB
description: Informazioni su come eseguire query e accedere alle proprietà JSON annidate e usare caratteri speciali in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 9a9300db1adc3ff238c44887012400702690b0e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337830"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Uso di JSON in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Nell'API SQL (Core) di Azure Cosmos DB, gli elementi vengono archiviati come file JSON. Il sistema di tipi e le espressioni sono quindi limitati all'interazione esclusiva con i tipi JSON. Per altre informazioni, vedere le [specifiche JSON](https://www.json.org/).

Verranno riepilogati alcuni aspetti importanti dell'uso di JSON:

- Gli oggetti JSON iniziano sempre con una parentesi graffa sinistra `{` e terminano con una parentesi graffa destra `}`
- È possibile avere proprietà JSON [annidate](#nested-properties) l'una all'interno dell'altra
- I valori delle proprietà JSON possono essere matrici
- I nomi delle proprietà JSON distinguono tra maiuscole e minuscole
- Il nome di una proprietà JSON può essere qualsiasi valore stringa (inclusi spazi o caratteri diversi da lettere)

## <a name="nested-properties"></a>Proprietà annidate

È possibile accedere a proprietà JSON annidate usando una funzione di accesso di tipo punto. È possibile usare proprietà JSON annidate in una query nello stesso modo in cui può essere usata qualsiasi altra proprietà.

Di seguito è riportato un documento con proprietà JSON annidate:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

In questo caso, le proprietà `state`, `country` e `city` sono tutte annidate nella proprietà `address`.

Nell'esempio seguente vengono proiettate due proprietà annidate: `f.address.state` e `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Utilizzo con matrici

Oltre alle proprietà annidate, il formato JSON supporta anche le matrici.

Di seguito è riportato un documento di esempio con una matrice:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

Quando si usano le matrici, è possibile accedere a un elemento specifico all'interno di una matrice facendo riferimento alla relativa posizione:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

Nella maggior parte dei casi, tuttavia, quando si usano le matrici si ricorre a una [sottoquery](sql-query-subquery.md) o a un [self-join](sql-query-join.md).

Di seguito, ad esempio, è riportato un documento che mostra il saldo giornaliero del conto corrente di un cliente.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Se si voleva eseguire una query che mostrasse tutti i clienti che in un dato momento avevano un saldo negativo, era possibile usare [EXISTS](sql-query-subquery.md#exists-expression) con una sottoquery:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Differenza tra null e undefined

Se una proprietà non è definita in un elemento, il relativo valore è `undefined` . Una proprietà con il valore `null` deve essere definita in modo esplicito e assegnare un `null` valore.

Si consideri, ad esempio, questo elemento di esempio:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

In questo esempio, la proprietà `isRegistered` ha un valore di `undefined` perché viene omessa dall'elemento. Il valore della proprietà `creationDate` è `null` .

Azure Cosmos DB supporta due funzioni di sistema di controllo dei tipi utili per `null` le `undefined` proprietà e:

* [IS_NULL](sql-query-is-null.md) : controlla se il valore di una proprietà è `null`
* [IS_DEFINED](sql-query-is-defined.md) : verifica se è stato definito un valore della proprietà

È possibile ottenere informazioni sugli [operatori supportati](sql-query-operators.md) e il relativo comportamento per `null` `undefined` i valori e.

## <a name="reserved-keywords-and-special-characters-in-json"></a>Parole chiave riservate e caratteri speciali in JSON

È possibile accedere alle proprietà mediante l'operatore della proprietà di delimitazione `[]`. Ad esempio, la sintassi di `SELECT c.grade` and `SELECT c["grade"]` sono equivalenti. Questa sintassi risulta utile quando di usano i caratteri di escape per una proprietà che contiene spazi, caratteri speciali o condivide lo stesso nome di una parola chiave SQL o una parola riservata.

Di seguito, ad esempio, è riportato un documento con una proprietà denominata `order` e una proprietà `price($)` contenente caratteri speciali:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Se si esegue una query che include la proprietà `order` o `price($)`, verrà visualizzato un errore di sintassi.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

Il risultato è:

`
Syntax error, incorrect syntax near 'order'
`

La query dovrebbe essere scritta come indicato di seguito:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>Espressioni JSON

La proiezione supporta anche le espressioni JSON, come illustrato nell'esempio seguente:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Nell'esempio precedente, la clausola `SELECT` deve creare un oggetto JSON e, poiché l'esempio non sono incluse chiavi, la clausola usa il nome della variabile di argomento implicita `$1`. La query seguente restituisce due variabili di argomento implicite: `$1` e `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Aliasing

È possibile eseguire l'aliasing esplicito dei valori nelle query. Nel caso in cui una query avesse due proprietà con lo stesso nome, è necessario usare l'aliasing per rinominare una o entrambe le proprietà, in modo da evitare ambiguità nel risultato proiettato.

### <a name="examples"></a>Esempi

La parola chiave `AS` usata per l'aliasing è facoltativa, come illustrato nell'esempio seguente quando si proietta il secondo valore come `NameInfo`:

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Aliasing con parole chiave riservate o caratteri speciali

Non è possibile usare l'aliasing per proiettare un valore come nome di proprietà con uno spazio, un carattere speciale o una parola riservata. Se invece si volesse modificare la proiezione di un valore in modo da avere, ad esempio, un nome di proprietà con uno spazio, sarebbe possibile usare un'[espressione JSON](#json-expressions).

Ad esempio:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Clausola SELECT](sql-query-select.md)
- [Clausola WHERE](sql-query-where.md)
