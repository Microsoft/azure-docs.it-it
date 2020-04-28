---
title: GRADI nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema di SQL in Azure Cosmos DB per restituire l'angolo corrispondente in gradi per un angolo specificato in radianti
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299471"
---
# <a name="degrees-azure-cosmos-db"></a>GRADI (Azure Cosmos DB)
 Restituisce l'angolo corrispondente in gradi di un angolo specificato in radianti.  
  
## <a name="syntax"></a>Sintassi
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce il numero di gradi di un angolo di PI/2 radianti.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Questo è il set di risultati.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
