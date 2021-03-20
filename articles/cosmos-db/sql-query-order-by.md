---
title: Clausola ORDER BY in Azure Cosmos DB
description: Informazioni sulla clausola SQL ORDER BY per Azure Cosmos DB. Usare SQL come linguaggio di query JSON Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: 887dc13eb5e351688718d2a221e69499557b23e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338299"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Clausola ORDER BY in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La clausola facoltativa `ORDER BY` consente di specificare l'ordinamento dei risultati restituiti dalla query.

## <a name="syntax"></a>Sintassi
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argomenti
  
- `<sort_specification>`  
  
   Specifica una proprietà o espressione in cui ordinare il set di risultati della query. È possibile specificare una colonna di ordinamento come nome o alias di proprietà.  
  
   È possibile specificare più proprietà. I nomi delle proprietà devono essere univoci. La sequenza delle proprietà di ordinamento nella `ORDER BY` clausola definisce l'organizzazione del set di risultati ordinato. Ovvero, il set di risultati viene ordinato in base alla prima proprietà e quindi l'elenco così ordinato viene ordinato in base alla seconda proprietà e così via.  
  
   I nomi di proprietà a cui viene fatto riferimento nella `ORDER BY` clausola devono corrispondere a una proprietà nell'elenco di selezione o a una proprietà definita nella raccolta specificata nella `FROM` clausola senza ambiguità.  
  
- `<sort_expression>`  
  
   Specifica una o più proprietà o espressioni in cui ordinare il set di risultati della query.  
  
- `<scalar_expression>`  
  
   Vedere la sezione [Espressioni scalari](sql-query-scalar-expressions.md) per informazioni dettagliate.  
  
- `ASC | DESC`  
  
   Specifica che i valori nella colonna specificata devono essere ordinati in ordine crescente o decrescente. `ASC` Ordina dal valore più basso a quello più alto. `DESC` Ordina dal valore più alto a quello più basso. `ASC` ordinamento predefinito. I valori Null vengono considerati i valori in assoluto più piccoli.  
  
## <a name="remarks"></a>Commenti  
  
   `ORDER BY`Per la clausola è necessario che i criteri di indicizzazione includano un indice per i campi da ordinare. Il runtime di query di Azure Cosmos DB supporta l'ordinamento in base a un nome di proprietà e non alle proprietà calcolate. Azure Cosmos DB supporta più `ORDER BY` Proprietà. Per eseguire una query con più proprietà ORDER BY, è necessario definire un [indice composto](index-policy.md#composite-indexes) sui campi da ordinare.

> [!Note]
> Se le proprietà ordinate potrebbero non essere definite per alcuni documenti e si desidera recuperarle in una query ORDER BY, è necessario includere in modo esplicito questo percorso nell'indice. I criteri di indicizzazione predefiniti non consentiranno il recupero dei documenti in cui la proprietà di ordinamento non è definita. [Esaminare le query di esempio sui documenti con alcuni campi mancanti](#documents-with-missing-fields).

## <a name="examples"></a>Esempio

Ad esempio, ecco una query che recupera le famiglie in ordine crescente di nome della città residente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

I risultati sono:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

La query seguente recupera le famiglie `id` in ordine di data di creazione dell'elemento. `creationDate`L'elemento è un numero che rappresenta l' *ora di epoche* o il tempo trascorso da Jan. 1, 1970 in secondi.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

I risultati sono:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

È inoltre possibile ordinare in base a più proprietà. Una query che ordina in base a più proprietà richiede un [indice composto](index-policy.md#composite-indexes). Si consideri la query seguente:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Questa query recupera la famiglia `id` in ordine crescente in base al nome della città. Se più elementi hanno lo stesso nome di città, la query sarà ordinata `creationDate` in ordine decrescente.

## <a name="documents-with-missing-fields"></a>Documenti con campi mancanti

Le query con `ORDER BY` eseguite su contenitori con i criteri di indicizzazione predefiniti non restituiranno documenti in cui la proprietà di ordinamento non è definita. Se si desidera includere documenti in cui la proprietà di ordinamento non è definita, è necessario includere in modo esplicito questa proprietà nei criteri di indicizzazione.

Ad esempio, di seguito è riportato un contenitore con criteri di indicizzazione che non includono in modo esplicito alcun percorso oltre `"/*"` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se si esegue una query che include `lastName` nella `Order By` clausola, i risultati includeranno solo i documenti per i quali è stata `lastName` definita una proprietà. Non è stato definito un percorso incluso esplicito per `lastName` , pertanto tutti i documenti privi di `lastName` non verranno visualizzati nei risultati della query.

Ecco una query che esegue l'ordinamento `lastName` in base a due documenti, uno dei quali non dispone di una `lastName` definizione:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

I risultati includono solo il documento con una definizione `lastName` :

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Se si aggiornano i criteri di indicizzazione del contenitore per includere in modo esplicito un percorso per `lastName` , nei risultati della query si includeranno documenti con una proprietà di ordinamento non definita. È necessario definire in modo esplicito il percorso per condurre questo valore scalare (e non oltre). È consigliabile usare il `?` carattere nella definizione del percorso nei criteri di indicizzazione per assicurarsi di indicizzare in modo esplicito la proprietà `lastName` e senza ulteriori percorsi annidati oltre. Se la `Order By` query utilizza un [indice composto](index-policy.md#composite-indexes), i risultati includeranno sempre i documenti con una proprietà di ordinamento non definita nei risultati della query.

Di seguito sono riportati i criteri di indicizzazione di esempio che consentono di visualizzare i documenti con un valore non definito `lastName` nei risultati della query:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se si esegue di nuovo la stessa query, i documenti mancanti vengono `lastName` visualizzati per primi nei risultati della query:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

I risultati sono:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Se si modifica il tipo di ordinamento in `DESC` , i documenti mancanti vengono `lastName` visualizzati per ultimi nei risultati della query:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

I risultati sono:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> Solo la versione 3.4.0 o successiva di .NET SDK supporta ORDER BY con tipi misti. Se pertanto si desidera eseguire l'ordinamento in base a una combinazione di valori non definiti e definiti, è consigliabile utilizzare questa versione (o versioni successive).

Non è possibile controllare l'ordine in cui vengono visualizzati tipi diversi nei risultati. Nell'esempio precedente è stato illustrato il modo in cui i valori non definiti sono stati ordinati prima dei valori di stringa. Se, ad esempio, si desidera un maggiore controllo sull'ordinamento dei valori non definiti, è possibile assegnare a qualsiasi proprietà non definita un valore stringa "Aladin" o "zzzzzzzz" per assicurarsi che siano prima o per ultimi.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Indexing policies in Azure Cosmos DB](index-policy.md) (Criteri di indicizzazione in Azure Cosmos DB)
- [Clausola limite OFFSET](sql-query-offset-limit.md)
