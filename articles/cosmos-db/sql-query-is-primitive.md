---
title: IS_PRIMITIVE nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL IS_PRIMITIVE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04c8e41f1a431b329f2093851e4430e69ab6aee3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303784"
---
# <a name="is_primitive-azure-cosmos-db"></a>IS_PRIMITIVE (Azure Cosmos DB)
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un primitivo (stringa, valore booleano, numerico o null).  
  
## <a name="syntax"></a>Sintassi
  
```sql
IS_PRIMITIVE(<expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*expr*  
   Qualsiasi espressione.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente controlla gli oggetti di tipo booleano JSON, numero, stringa, null, oggetto, matrice e non definito `IS_PRIMITIVE` usando la funzione.  
  
```sql
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Questo è il set di risultati.  
  
```json
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trarrà vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di controllo dei tipi Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
