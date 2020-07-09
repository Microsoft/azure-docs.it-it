---
title: Ottimizzare gli inserimenti bulk-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come è possibile ottimizzare le operazioni di inserimento bulk in un database di Azure per PostgreSQL-server singolo.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4d10f06577738364e3f4a0ea40221d37ebfb31c0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116285"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Ottimizzare gli inserimenti bulk e usare i dati temporanei in un database di Azure per PostgreSQL-server singolo 
Questo articolo descrive come ottimizzare le operazioni di inserimento bulk e usare dati temporanei in un server di Database di Azure per PostgreSQL.

## <a name="use-unlogged-tables"></a>Usare tabelle non registrate
In caso di operazioni di carico di lavoro che coinvolgono dati temporanei o che prevedono l'inserimento bulk di grandi set di dati, prendere in considerazione l'uso di tabelle non registrate.

Le tabelle non registrate sono una funzionalità di PostgreSQL che può essere usata in modo efficace per ottimizzare gli inserimenti bulk. PostgreSQL usa la registrazione write-ahead (WAL). Per impostazione predefinita, offre atomicità e durabilità. Atomicità, coerenza, isolamento e durabilità costituiscono le proprietà ACID. 

L'inserimento in una tabella non registrata comporta l'inserimento di PostgreSQL senza la scrittura nel log delle transazioni, che a sua volta è un'operazione di I/O. Di conseguenza, queste tabelle sono notevolmente più veloci rispetto alle tabelle tradizionali.

Per creare una tabella non registrata, usare le opzioni seguenti:
- Creare una nuova tabella non registrata con la sintassi `CREATE UNLOGGED TABLE <tableName>`.
- Convertire una tabella registrata esistente in una tabella non registrata con la sintassi `ALTER TABLE <tableName> SET UNLOGGED`.  

Per invertire il processo, usare la sintassi `ALTER TABLE <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Compromessi per le tabelle non registrate
Le tabelle non registrate non sono protette dagli arresti anomali del sistema. Una tabella non registrata viene troncata automaticamente dopo un arresto anomalo o in seguito a un arresto non corretto. Il contenuto di una tabella non registrata non viene inoltre replicato nei server di standby. Gli indici creati in una tabella non registrata sono automaticamente non registrati. Al termine dell'operazione di inserimento, convertire la tabella in registrata in modo che l'inserimento sia durevole.

Per alcuni carichi di lavoro dei clienti, è stato registrato un miglioramento delle prestazioni dal 15% al 20% in seguito all'uso di tabelle non registrate.

## <a name="next-steps"></a>Passaggi successivi
Esaminare il carico di lavoro per l'uso di dati temporanei e inserimenti bulk di grandi dimensioni. Vedere la documentazione di PostgreSQL seguente:
 
- [Create Table SQL commands](https://www.postgresql.org/docs/current/static/sql-createtable.html) (Comandi SQL CREATE TABLE)
