---
title: ATN2 in linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema ATN2 SQL in Azure Cosmos DB restituisce il valore principale dell'arcotangente di y/x, espresso in radianti
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6db42713ec6f0eac64e0f1123825c21a4206fa99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341944"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce il valore principale dell'arcotangente di y/x, espresso in radianti.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempio
  
  L'esempio seguente calcola l'arcotangente per i componenti x e y specificati.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Questo è il set di risultati.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
