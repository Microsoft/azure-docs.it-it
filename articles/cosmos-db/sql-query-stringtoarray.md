---
title: StringToArray in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL StringToArray in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7ae1f69e92e890daae528eb1f4dfb95f76560043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337983"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce l'espressione convertita in una matrice. Se l'espressione non può essere convertita, restituisce undefined.  
  
## <a name="syntax"></a>Sintassi
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da analizzare come espressione di matrice JSON. 
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di matrice o undefined. 
  
## <a name="remarks"></a>Commenti
  I valori stringa annidati devono essere scritti con virgolette doppie per essere JSON validi. Per informazioni dettagliate sul formato JSON, vedere [JSON.org](https://json.org/). Questa funzione di sistema non utilizzerà l'indice.
  
## <a name="examples"></a>Esempio
  
  Nell'esempio seguente viene illustrato il comportamento di `StringToArray` tra tipi diversi. 
  
 Di seguito sono riportati esempi con input valido.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Questo è il set di risultati.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Di seguito è riportato un esempio di input non valido. 
   
 Le virgolette singole all'interno della matrice non sono JSON valide.
Anche se sono valide all'interno di una query, non verranno analizzate in matrici valide. Per le stringhe all'interno della stringa di matrice deve essere preceduto da un carattere di escape "[ \\ " \\ "]" oppure la virgoletta circostante deve essere singola "[" "]".

```sql
SELECT
    StringToArray("['5','6','7']")
```

Questo è il set di risultati.

```json
[{}]
```

Di seguito sono riportati alcuni esempi di input non valido.
   
 L'espressione passata verrà analizzata come matrice JSON. gli elementi seguenti non restituiscono una matrice di tipi e pertanto restituiscono undefined.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Questo è il set di risultati.

```json
[{}]
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
