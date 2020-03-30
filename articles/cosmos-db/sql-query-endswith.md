---
title: ENDSWITH nel linguaggio di query di Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL ENDSWITH in Azure Cosmos DB per restituire un valore booleano che indica se la prima espressione stringa termina con la seconda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299449"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   È un'espressione stringa.  
  
*str_expr2*  
   È un'espressione stringa da confrontare con la fine di *str_expr1*.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce "abc" che termina con "b" e "bc".  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Questo è il set di risultati.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
