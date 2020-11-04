---
title: EXP nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL esponente (EXP) in Azure Cosmos DB per restituire il valore esponenziale dell'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adf80d36edbe8f9a5535e8fa04501918a62ea7ca
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335624"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce il valore esponenziale dell'espressione numerica specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="remarks"></a>Commenti
  
  La costante **e** (2.718281…) è la base dei logaritmi naturali.  
  
  L'esponente di un numero è la costante **e** elevata alla potenza del numero. Ad esempio, EXP(1.0) = e^1.0 = 2,71828182845905 e EXP(10) = e^10 = 22026,4657948067.  
  
  Il valore esponenziale del logaritmo naturale di un numero è il numero stesso: EXP (LOG (n)) = n. E il logaritmo naturale dell'esponente di un numero è il numero stesso: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene dichiarata una variabile e restituito il valore esponenziale della variabile specificata (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Questo è il set di risultati.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 L'esempio seguente restituisce il valore esponenziale del logaritmo naturale di 20 e il logaritmo naturale dell'esponente di 20. Poiché queste funzioni sono inverse l'una dell'altra, il valore restituito con arrotondamento per il calcolo a virgola mobile in entrambi i casi è 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Questo è il set di risultati.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
