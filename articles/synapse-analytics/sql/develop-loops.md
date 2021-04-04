---
title: Usare cicli T-SQL
description: Suggerimenti per l'uso di cicli T-SQL, la sostituzione di cursori e lo sviluppo di soluzioni correlate con sinapsi SQL in Azure sinapsi Analytics.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120887"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>Usare i cicli T-SQL con sinapsi SQL in Azure sinapsi Analytics

Questo articolo fornisce suggerimenti essenziali per l'uso di cicli T-SQL, la sostituzione di cursori e lo sviluppo di soluzioni correlate con sinapsi SQL.

## <a name="purpose-of-while-loops"></a>Scopo dei cicli WHILE

Sinapsi SQL supporta il ciclo [while](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) per eseguire ripetutamente blocchi di istruzioni. Il ciclo WHILE continua fino a quando le condizioni specificate sono vere o fino a quando il codice termina il ciclo in modo specifico usando la parola chiave BREAK. 

I cicli in sinapsi SQL sono utili per la sostituzione di cursori definiti nel codice SQL. Per fortuna, quasi tutti i cursori scritti in codice SQL sono del tipo avanzamento rapido, di sola lettura. Pertanto, i cicli WHILE rappresentano un'ottima alternativa per la sostituzione dei cursori.

## <a name="replace-cursors-in-synapse-sql"></a>Sostituisci cursori in sinapsi SQL

Prima di immergersi in, è necessario prendere in considerazione la domanda seguente: "è possibile riscrivere il cursore per l'uso di operazioni basate su set?" In molti casi, la risposta è sì ed è spesso l'approccio migliore. Un'operazione basata su set viene spesso eseguita più velocemente rispetto a un approccio iterativo riga per riga.

I cursori di sola lettura con avanzamento rapido sono facilmente sostituibili con un costrutto di ciclo. Il codice seguente è un esempio semplice. Questo esempio di codice aggiorna le statistiche per ogni tabella nel database. Scorrendo le tabelle nel ciclo, eseguire ogni comando in sequenza.

Prima di tutto, creare una tabella temporanea contenente un numero di riga univoco usato per identificare le singole istruzioni:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

In secondo luogo, inizializzare le variabili necessarie per eseguire il ciclo:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Ora eseguire un ciclo delle istruzioni, una alla volta:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Infine eliminare la tabella temporanea creata nel primo passaggio.

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](develop-overview.md).