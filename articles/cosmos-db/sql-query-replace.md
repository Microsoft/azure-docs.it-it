---
title: SOSTITUISCi nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL SOSTITUISCi in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8c9b7ffdf8395944cf75dabbbf4c42cea0e0c9b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341621"
---
# <a name="replace-azure-cosmos-db"></a>SOSTITUISCi (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Sostituisce tutte le occorrenze di un valore stringa specificato con un altro valore stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Espressione stringa in cui eseguire la ricerca.  
  
*str_expr2*  
   Espressione stringa da trovare.  
  
*str_expr3*  
   Espressione stringa che consente di sostituire le occorrenze di *str_expr2* in *str_expr1*.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempio
  
  Nell'esempio seguente viene illustrato come utilizzare `REPLACE` in una query.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Questo è il set di risultati.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
