---
title: TimestampToDateTime in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL TimestampToDateTime in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: e9a77ab0ac32d627d59e2cb0fa4a680f174a6833
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587237"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>TimestampToDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Converte il valore timestamp specificato in un valore DateTime.
  
## <a name="syntax"></a>Sintassi
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>Argomenti

*Timestamp*  

Un valore numerico con segno, il numero corrente di millisecondi trascorsi dal periodo UNIX. In altre parole, il numero di millisecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.

## <a name="return-types"></a>Tipi restituiti

Restituisce il valore della stringa ISO 8601 di data e ora UTC nel formato in `YYYY-MM-DDThh:mm:ss.fffffffZ` cui:
  
|Formato|Descrizione|
|-|-|
|AAAA|anno a quattro cifre|
|MM|mese a due cifre (01 = gennaio e così via)|
|GG|giorno del mese a due cifre (da 01 a 31)|
|T|significato per l'inizio degli elementi Time|
|hh|ora a due cifre (da 00 a 23)|
|MM|minuti a due cifre (da 00 a 59)|
|ss|secondi a due cifre (da 00 a 59)|
|. fffffff|secondi frazionari a sette cifre|
|Z|Indicatore UTC (Coordinated Universal Time)|
  
  Per ulteriori informazioni sul formato ISO 8601, vedere [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Commenti

TimestampToDateTime restituisce `undefined` se il valore di timestamp specificato non è valido.

## <a name="examples"></a>Esempio
  
Nell'esempio seguente il timestamp viene convertito in un valore DateTime:

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
