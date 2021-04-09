---
title: Clausola limite OFFSET in Azure Cosmos DB
description: Informazioni su come usare la clausola di limite di OFFSET per ignorare e prendere alcuni valori durante l'esecuzione di query in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 459bd8511577067766cf488f53df57c1dc33fad1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338296"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Clausola limite OFFSET in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La clausola LIMIT OFFSET è una clausola facoltativa da ignorare, quindi è necessario un certo numero di valori dalla query. Il numero di OFFSET e il numero di limiti sono necessari nella clausola limite di OFFSET.

Quando si utilizza il limite di OFFSET insieme a una clausola ORDER BY, il set di risultati viene prodotto tramite Skip e Take sui valori ordinati. Se non viene utilizzata alcuna clausola ORDER BY, verrà generato un ordine deterministico dei valori.

## <a name="syntax"></a>Sintassi
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argomenti

- `<offset_amount>`

   Specifica il numero intero di elementi che i risultati della query devono ignorare.

- `<limit_amount>`
  
   Specifica il numero intero di elementi che devono essere inclusi nei risultati della query

## <a name="remarks"></a>Commenti
  
  `OFFSET`Nella clausola sono necessari sia il conteggio che il `LIMIT` numero `OFFSET LIMIT` . Se `ORDER BY` viene utilizzata una clausola facoltativa, il set di risultati viene prodotto facendo ignorare i valori ordinati. In caso contrario, la query restituirà un ordine fisso di valori.

  L'addebito delle UR di una query con aumenterà `OFFSET LIMIT` man mano che aumenta il numero di termini di offset. Per le query con [più pagine di risultati](sql-query-pagination.md), in genere è consigliabile usare i [token di continuazione](sql-query-pagination.md#continuation-tokens). I token di continuazione sono un "segnalibro" per la posizione in cui la query può essere ripresa in un secondo momento. Se si usa `OFFSET LIMIT` , non è presente alcun "segnalibro". Se si vuole restituire la pagina successiva della query, è necessario iniziare dall'inizio.
  
  È consigliabile usare `OFFSET LIMIT` nei casi in cui si desidera ignorare completamente gli elementi e salvare le risorse del client. Ad esempio, è consigliabile usare `OFFSET LIMIT` se si vuole passare al risultato della query 1000 e non è necessario visualizzare i risultati da 1 a 999. Nel back-end `OFFSET LIMIT` carica sempre ogni elemento, inclusi quelli che vengono ignorati. Il vantaggio in termini di prestazioni è un risparmio nelle risorse client evitando l'elaborazione di elementi che non sono necessari.

## <a name="examples"></a>Esempio

Ad esempio, di seguito viene illustrata una query che ignora il primo valore e restituisce il secondo valore (in ordine di nome della città residente):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

I risultati sono:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Ecco una query che ignora il primo valore e restituisce il secondo valore (senza ordinamento):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

I risultati sono:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Clausola SELECT](sql-query-select.md)
- [Clausola ORDER BY](sql-query-order-by.md)
