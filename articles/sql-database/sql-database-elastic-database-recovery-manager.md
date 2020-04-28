---
title: Gestione ripristino per risolvere i problemi della mappa partizioni
description: Usare la classe RecoveryManager per risolvere i problemi relativi alle mappe partizioni.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 6101e00ab98b0d8d901f2e42bf4083d40d0a3227
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73823840"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Uso della classe RecoveryManager per correggere i problemi delle mappe partizioni

La classe [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) offre alle applicazioni ADO.NET la possibilità di rilevare e correggere facilmente eventuali incoerenze tra la mappa globale partizioni (GSM) e la mappa partizioni locale (LSM) in un ambiente di database partizionato.

La mappa globale partizioni e la mappa locale partizioni tengono traccia del mapping di ogni database in un ambiente partizionato. Si verifica a volte un'interruzione tra la mappa globale partizioni e la mappa locale partizioni. In tal caso usare la classe RecoveryManager per rilevare e correggere l'interruzione.

La classe RecoveryManager fa parte della [Libreria client dei database elastici](sql-database-elastic-database-client-library.md).

![Mappa partizioni][1]

Per le definizioni dei termini, vedere il [glossario degli strumenti del database elastico](sql-database-elastic-scale-glossary.md). Per informazioni su come usare **ShardMapManager** per gestire dati in una soluzione partizionata, vedere [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Perché usare Gestione ripristino

In un ambiente di database partizionato c'è un unico tenant per database e ci sono molti database per server. Nell'ambiente possono anche esserci molti server. Ogni database è mappato nella mappa partizioni, quindi le chiamate possono essere instradate correttamente al server e al database appropriati. I database vengono rilevati in base a una **chiave di partizionamento orizzontale** e a ogni partizione viene assegnato un **intervallo di valori di chiave**. Ad esempio, una chiave di partizionamento orizzontale può rappresentare i nomi dei clienti da "D" a "F." Il mapping di tutte le partizioni (ovvero i database) e i relativi intervalli di mapping sono inclusi nella **mappa globale partizioni**. Ogni database include anche una mappa degli intervalli presenti nella partizione, ovvero la **mappa locale partizioni**. Quando un'app si connette a una partizione, il mapping viene memorizzato nella cache insieme all'app per consentire un recupero rapido. La mappa locale partizioni si usa per convalidare i dati memorizzati nella cache.

La mappa globale e locale delle partizioni potrebbero perdere la sincronizzazione per i motivi seguenti:

1. L'eliminazione di una partizione con un intervallo che si ritiene non più usato o la ridenominazione della partizione. Eliminazione dei risultati di una partizione in un **mapping di partizione orfana**. Analogamente, anche un database rinominato può causare un mapping di partizione orfana. A seconda della finalità della modifica, potrebbe essere necessario rimuovere la partizione o aggiornarne il percorso. Per ripristinare un database eliminato, vedere [Ripristinare un database SQL di Azure con il portale di Azure](sql-database-recovery-using-backups.md).
2. Si verifica un evento di failover geografico. Per continuare, è necessario aggiornare il nome del server e il nome del database dello strumento di gestione della mappa partizioni all'interno dell'applicazione e quindi aggiornare i dettagli del mapping di partizione per tutte le partizioni nella mappa partizioni. In caso di failover geografico, è necessario che la logica di ripristino sia automatizzata nel flusso di lavoro di failover. L'automazione delle azioni di ripristino rende possibile la gestibilità senza problemi dei database abilitati per la replica geografica, evitando interventi manuali. Per informazioni sulle opzioni per il ripristino di un database in caso di interruzione del data center, vedere [continuità aziendale](sql-database-business-continuity.md) e [ripristino di emergenza](sql-database-disaster-recovery.md).
3. Una partizione o un database ShardMapManager viene ripristinato a una condizione precedente. Per informazioni sul recupero temporizzato tramite i backup, vedere l'articolo sul [ripristino mediante backup](sql-database-recovery-using-backups.md).

Per altre informazioni sugli strumenti di database elastici per i database SQL di Azure, la replica geografica e il ripristino, vedere:

* [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](sql-database-business-continuity.md)
* [Inizia a usare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md)  
* [Gestione di mappe partizioni](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Ripristino di RecoveryManager da un oggetto ShardMapManager

Il primo passaggio consiste nel creare un'istanza di RecoveryManager. Il [metodo GetRecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) restituisce la funzionalità di Gestione ripristino per l'istanza corrente di [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager). Per risolvere eventuali incoerenze in una mappa partizioni, è necessario ripristinare prima di tutto RecoveryManager per una mappa partizioni specifica.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

In questo esempio RecoveryManager viene inizializzato da ShardMapManager. Anche ShardMapManager che contiene un oggetto ShardMap è già inizializzato.

Poiché questo codice dell'applicazione manipola la mappa partizioni stessa, le credenziali usate nel metodo factory, nell'esempio precedente smmConnectionString, devono essere credenziali con autorizzazioni di lettura/scrittura per il database GSM a cui si fa riferimento nella stringa di connessione. Queste credenziali sono in genere diverse da quelle usate per aprire connessioni per il routing dipendente dai dati. Per ulteriori informazioni, vedere [Gestione delle credenziali nella libreria client dei database elastici](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Rimozione di una partizione da ShardMap dopo l'eliminazione di una partizione

Il [metodo DetachShard](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) scollega la partizione specificata dalla mappa partizioni ed elimina i mapping associati alla partizione.  

* Il parametro location corrisponde al percorso della partizione, cioè il nome del server e il nome del database, della partizione che viene scollegata.
* Il parametro shardMapName è il nome della mappa partizioni. È richiesto solo quando più mappe partizioni sono gestite dallo stesso gestore delle mappe partizioni. Facoltativa.

> [!IMPORTANT]
> usare questa tecnica solo se si è certi che l'intervallo per il mapping aggiornato sia vuoto. Poiché i metodi descritti precedentemente non controllano i dati dell'intervallo da spostare, è consigliabile includere i controlli nel codice.

Questo esempio rimuove le partizioni dalla mappa partizioni.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

La mappa partizioni riflette il percorso della partizione nella mappa globale delle partizioni precedente l'eliminazione della partizione. Poiché la partizione è stata eliminata, si presuppone che questa operazione sia stata intenzionale che l'intervallo di chiavi di partizionamento orizzontale non venga più usato. Se non è questo il caso, è possibile eseguire un ripristino temporizzato. Per ripristinare le partizioni da un punto nel tempo precedente. In tal caso, rivedere la sezione seguente per rilevare le incoerenze della partizione. Per eseguire il ripristino, vedere [recupero temporizzato](sql-database-recovery-using-backups.md).

Presupponendo che l'eliminazione del database sia stata intenzionale, l'azione di pulizia amministrativa finale consiste nell'eliminare la voce relativa alla partizione nel gestore delle mappe partizioni. In questo modo si impedisce all'applicazione di scrivere inavvertitamente informazioni in un intervallo non previsto.

## <a name="to-detect-mapping-differences"></a>Per rilevare le differenze nei mapping

Il [metodo DetectMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) seleziona e restituisce una della mappe partizioni, locale o globale, come origine di dati reali e riconcilia i mapping in entrambi i tipi di mappa partizioni, globale e locale.

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* Il *percorso* specifica il nome del server e il nome del database.
* Il parametro *shardMapName* è il nome della mappa partizioni. È richiesto solo se più mappe partizioni sono gestite dallo stesso gestore delle mappe partizioni. Facoltativa.

## <a name="to-resolve-mapping-differences"></a>Per risolvere le differenze nei mapping

Il [metodo ResolveMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) seleziona una delle mappe partizioni, locale o globale, come origine di dati reali e riconcilia i mapping in entrambi i tipi di mappa partizioni, globale e locale.

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* Il parametro *RecoveryToken* enumera le differenze nei mapping tra la mappa globale di partizioni e la mappa locale di partizioni per la partizione specifica.
* L' [enumerazione MappingDifferenceResolution](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) viene usata per indicare il metodo per risolvere la differenza tra i mapping di partizione.
* È consigliabile usare **MappingDifferenceResolution.KeepShardMapping** qualora la mappa locale partizioni contenga il mapping corretto e quindi si dovrà usare il mapping presente nella partizione. Questo si verifica in genere nel caso di un failover, dove la partizione ora risiede in un nuovo server. Poiché la partizione deve essere prima rimossa dalla mappa globale partizioni, tramite il metodo RecoveryManager.DetachShard, non esiste più un mapping nella mappa globale partizioni. Per ristabilire il mapping della partizione è quindi necessario usare la mappa locale partizioni.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Collegare una partizione a ShardMap dopo il ripristino di una partizione

Il [metodo AttachShard](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) collega la partizione specificata alla mappa partizioni. Rileva quindi eventuali incoerenze nella mappa partizioni e aggiorna i mapping in modo che la partizione corrisponda al punto di ripristino della partizione. Si presuppone che venga rinominato anche il database per riflettere il nome del database originale, precedente al ripristino della partizione, perché per impostazione predefinita il ripristino temporizzato usa un nuovo database a cui aggiunge il timestamp.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* Il parametro *location* corrisponde al nome del server e al nome del database della partizione che viene collegata.
* Il parametro *shardMapName* è il nome della mappa partizioni. È richiesto solo quando più mappe partizioni sono gestite dallo stesso gestore delle mappe partizioni. Facoltativa.

Questo esempio aggiunge una partizione alla mappa partizioni ripristinata di recente da una condizione precedente. Poiché la partizione, ovvero il mapping per la partizione nella mappa locale partizioni, è stata ripristinata, è potenzialmente incoerenze con la voce della partizione nella mappa globale partizioni. Esternamente a questo codice di esempio, la partizione è stata ripristinata e rinominata con il nome originale del database. Essendo stata ripristinata, si presuppone che il mapping nella mappa locale partizioni sia quello attendibile.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Aggiornamento dei percorsi della partizioni dopo un failover geografico, o ripristino, delle partizioni

Nel caso di failover geografico, il database secondario è reso accessibile in scrittura e diventa il database primario. Il nome del server, e potenzialmente il database a seconda della configurazione, può essere diverso da quello primario originale. È quindi necessario correggere le voci dei mapping per la partizione nella mappa globale partizioni e nella mappa locale partizioni. In modo analogo, se il database viene ripristinato con un nome e un percorso diversi, oppure a una condizione precedente, potrebbero verificarsi incoerenze nelle mappe partizioni. Il gestore delle mappe partizioni controlla la distribuzione delle connessioni aperte ai database corretti. La distribuzione si basa sui dati contenuti nella mappa partizioni e sul valore della chiave di partizionamento orizzontale di destinazione della richiesta dell'applicazione. Dopo un failover geografico queste informazioni devono essere aggiornate con il nome del server, il nome del database e il mapping di partizione corretti del database ripristinato.

## <a name="best-practices"></a>Procedure consigliate

Il failover geografico e il ripristino sono operazioni che di solito sono gestite da un amministratore cloud dell'applicazione usando intenzionalmente una delle funzionalità di continuità aziendale dei database SQL di Azure. La pianificazione della continuità aziendale richiede la definizione di processi, procedure e misure che garantiscano la continuità delle operazioni aziendali senza interruzioni. In questo flusso di lavoro è necessario usare i metodi disponibili come parte della classe RecoveryManager per assicurare che la mappa globale partizioni e la mappa locale partizioni siano mantenute aggiornate con l'azione di ripristino eseguita. Per assicurarsi che la mappa globale partizioni e la mappa locale partizioni riflettano le informazioni corrette dopo un evento di failover, occorre eseguire cinque passaggi. Il codice dell'applicazione per eseguire questi passaggi può essere integrato negli strumenti e nel flusso di lavoro esistenti.

1. Recuperare Gestione ripristino da ShardMapManager.
2. Scollegare la partizione precedente dalla mappa partizioni.
3. Collegare la nuova partizione alla mappa partizioni, includendo il percorso della nuova partizione.
4. Rilevare le incoerenze nel mapping tra la mappa globale partizioni e la mappa locale partizioni.
5. Risolvere le differenze tra la mappa globale partizioni e la mappa locale partizioni, considerando attendibile la mappa locale partizioni.

L'esempio segue questa procedura:

1. Rimuove le partizioni dalla mappa partizioni che riflette i percorsi precedenti all'evento di failover.
2. Collega le partizioni alla mappa partizioni riflettendo i nuovi percorsi delle partizioni. Il parametro "Configuration.SecondaryServer" è il nuovo nome del server, ma lo stesso nome del database.
3. Recupera i token di ripristino rilevando le differenze dei mapping tra la mappa globale partizioni e la mappa locale partizioni per ogni partizione.
4. Risolve le incoerenze considerando attendibile il mapping dalla mappa locale partizioni di ogni partizione.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
