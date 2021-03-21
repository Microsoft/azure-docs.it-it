---
title: ST_DISTANCE nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni di sistema SQL ST_DISTANCE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f01f5faf68821fe9f85657c74111efdbb02bd204
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559935"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce la distanza tra le due espressioni punto GeoJSON, poligono, multipoligono o LineString. Per altre informazioni, vedere l'articolo relativo ai [dati sulla posizione geospaziale e GeoJSON](sql-query-geospatial-intro.md) .
  
## <a name="syntax"></a>Sintassi
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*spatial_expr*  
   È qualsiasi espressione di oggetto punto GeoJSON, poligono o LineString valida.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica che contiene la distanza. È espressa in metri per il sistema di riferimento predefinito.  
  
## <a name="examples"></a>Esempio
  
  Nell'esempio seguente viene illustrato come restituire tutti i documenti della famiglia che rientrano in 30 km del percorso specificato utilizzando la `ST_DISTANCE` funzione incorporata. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Questo è il set di risultati.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trarrà vantaggio da un [Indice geospaziale](index-policy.md#spatial-indexes) tranne che nelle query con aggregazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni spaziali Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
