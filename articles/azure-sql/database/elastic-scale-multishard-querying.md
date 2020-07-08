---
title: Eseguire query su database partizionati
description: Eseguire query tra partizioni utilizzando la libreria client dei database elastici.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: fe8b977d3385f8ef4e4ceaac4dde7c0ac6a79839
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84034632"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Esecuzione di query su più partizioni tramite strumenti di database elastici
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="overview"></a>Panoramica

Con gli [strumenti dei database elastici](elastic-scale-introduction.md) è possibile creare soluzioni di database partizionati. La funzionalità di **query su più partizioni** viene usata per attività che richiedono l'esecuzione di una query su più partizioni, ad esempio la raccolta o la creazione di report sui dati, a differenza del [routing dipendente dai dati](elastic-scale-data-dependent-routing.md), che consente di eseguire tutto il lavoro su una singola partizione.

1. Ottenere **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) or **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) con il metodo **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) o **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)). Vedere [Creazione di un oggetto ShardMapManager](elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [Ottenere una mappa RangeShardMap o ListShardMap](elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Creare un oggetto **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)).
3. Creare un oggetto **MultiShardStatement o MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Impostare la **proprietà CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) su un comando T-SQL.
5. Eseguire il comando chiamando il metodo **ExecuteQueryAsync or ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
6. Visualizzare i risultati usando la classe **MultiShardResultSet or MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)).

## <a name="example"></a>Esempio

Il codice seguente illustra l'uso delle query su più partizioni mediante uno specifico oggetto **ShardMap** denominato *myShardMap*.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

Un'importante differenza risiede nella costruzione delle connessioni a più partizioni. Mentre **SqlConnection** opera su un singolo database, **MultiShardConnection** accetta come input una ***raccolta di partizioni***. Popolare la raccolta di partizioni da una mappa partizioni. La query viene quindi eseguita sulla raccolta di partizioni usando la semantica di **UNION ALL** per assemblare un unico risultato complessivo. Facoltativamente, è possibile aggiungere all'output il nome della partizione di origine della riga usando la proprietà **ExecutionOptions** nel comando.

Notare la chiamata a **myShardMap.GetShards()**. Questo metodo recupera tutte le partizioni dalla mappa partizioni e fornisce un metodo semplice per eseguire una query su tutti i database rilevanti. La raccolta di partizioni per una query su più partizioni può essere ulteriormente perfezionata eseguendo una query LINQ sulla raccolta restituita dalla chiamata a **myShardMap.GetShards()**. In combinazione con i criteri sui risultati parziali, la funzionalità corrente di query su più partizioni è stata progettata per supportare correttamente da decine fino a centinaia di partizioni.

Un'attuale limitazione delle query su più partizioni è la mancanza di convalida delle partizioni e degli shardlet interrogati. Mentre il routing dipendente dai dati verifica che una determinata partizione faccia parte della mappa partizioni al momento dell'esecuzione delle query, le query su più partizioni non eseguono questo controllo. Questo può determinare l'esecuzione di query su più partizioni in database che sono stati rimossi dalla mappa partizioni.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Query su più partizioni e operazioni di suddivisione e unione

Le query su più partizioni non verificano se gli shardlet presenti nel database interrogato sono oggetto di operazioni di divisione/unione in corso. (Vedere [ridimensionamento con lo strumento di suddivisione-unione dei database elastici](elastic-scale-overview-split-and-merge.md)). Questo può causare incoerenze laddove le righe dello stesso shardlet vengono visualizzate per più database nella stessa query su più partizioni. Tenere presente queste limitazioni e completare le operazioni di suddivisione-unione in corso e le modifiche alla mappa partizioni prima dell'esecuzione di query su più partizioni.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]