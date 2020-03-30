---
title: Eventi estesi
description: Vengono descritti gli eventi estesi (XEvent) in Database SQL di Azure e come le sessioni di eventi sono leggermente diverse da sessioni di eventi in Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213955"
---
# <a name="extended-events-in-sql-database"></a>Eventi estesi nel database SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

In questo argomento viene illustrato come l'implementazione di eventi estesi nel database SQL di Azure è leggermente diversa rispetto agli eventi estesi in Microsoft SQL Server.

- SQL Database V12 ha acquisito la funzionalità degli eventi estesi nella seconda metà del calendario 2015.
- SQL Server ha gli eventi estesi dal 2008.
- Il set di funzionalità degli eventi estesi nel database SQL è un subset affidabile delle funzionalità in SQL Server.

*XEvent* è un nome alternativo informale utilizzato talvolta per "eventi estesi" in blog e altri percorsi informali.

Per altre informazioni sugli eventi estesi, per il database SQL di Azure e Microsoft SQL Server, vedere l'articolo:

- [Guida introduttiva: Eventi estesi in SQL ServerQuick Start: Extended events in SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Eventi estesi](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Prerequisiti

In questo argomento si presuppone che si dispone già di una conoscenza di:

- [Servizio database SQL di Azure](https://azure.microsoft.com/services/sql-database/).
- [Eventi estesi](https://msdn.microsoft.com/library/bb630282.aspx) in Microsoft SQL Server.

- La maggior parte della nostra documentazione sugli eventi estesi si applica sia a SQL Server che al database SQL.

Un’esposizione precedente a quanto riportato di seguito è utile quando si sceglie il file evento come [destinazione](#AzureXEventsTargets):

- [Servizio di Archiviazione di Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Utilizzo di Azure PowerShell con Archiviazione di Azure](../storage/common/storage-powershell-guide-full.md) : fornisce informazioni complete su PowerShell e il servizio Archiviazione di Azure.

## <a name="code-samples"></a>Esempi di codice

Gli argomenti correlati forniscono due esempi di codice:


- [Codice di destinazione del buffer circolare per eventi estesi nel database SQL](sql-database-xevent-code-ring-buffer.md)
    - Breve script Transact-SQL semplice.
    - Nell'argomento dell'esempio di codice si evidenzia che, una volta completata la destinazione del buffer circolare, è necessario rilasciarne le risorse tramite l'esecuzione di un'istruzione `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` alter-drop. Successivamente è possibile aggiungere un'altra istanza del buffer circolare da `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Codice di destinazione del file evento per eventi estesi nel database SQL](sql-database-xevent-code-event-file.md)
    - Fase 1 è PowerShell per creare un contenitore di Archiviazione di Azure.
    - Fase 2 è Transact-SQL che utilizza il contenitore di Archiviazione di Azure.

## <a name="transact-sql-differences"></a>Differenze di Transact-SQL


- Quando si esegue il comando [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) su SQL Server, si utilizza la clausola **ON SERVER** . Ma nel database SQL si utilizza invece la clausola **ON DATABASE** .


- La clausola **ON DATABASE** riguarda anche i comandi Transact-SQL [ALTER EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) e [DROP EVENT SESSION](https://msdn.microsoft.com/library/bb630257.aspx).


- Una procedura consigliata consiste nell'includere l'opzione della sessione eventi di **STARTUP_STATE = ON** nelle istruzioni **CREATE EVENT SESSION** o **ALTER EVENT SESSION**.
    - Il valore **= ON** supporta un riavvio automatico dopo una riconfigurazione del database logico a causa di un failover.

## <a name="new-catalog-views"></a>Nuove viste del catalogo

La funzionalità degli eventi estesi è supportata da diverse [viste del catalogo](https://msdn.microsoft.com/library/ms174365.aspx). Le viste del catalogo indicano i *metadati o le definizioni* di sessioni di eventi create dall'utente nel database corrente. Le viste non restituiscono informazioni sulle istanze delle sessioni di eventi attivi.

| Nome della<br/>vista del catalogo | Descrizione |
|:--- |:--- |
| **sys.database_event_session_actions** |Restituisce una riga per ogni azione su ogni evento di una sessione dell'evento. |
| **sys.database_event_session_events** |Restituisce una riga per ogni evento in una sessione di eventi. |
| **sys.database_event_session_fields** |Restituisce una riga per ogni colonna personalizzabile impostata in modo esplicito su eventi e destinazioni. |
| **sys.database_event_session_targets** |Restituisce una riga per ogni destinazione di evento per una sessione eventi. |
| **sys.database_event_sessions** |Restituisce una riga per ogni sessione di eventi nel database SQL del database. |

In Microsoft SQL Server le viste del catalogo simili hanno nomi che includono *.server\_* anziché *.database\_*. Il modello del nome è simile a **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>[DMV](https://msdn.microsoft.com/library/ms188754.aspx)

Il database SQL di Azure include [viste a gestione dinamica (DMV)](https://msdn.microsoft.com/library/bb677293.aspx) che supportano gli eventi estesi. Le DMV indicano le sessioni di eventi *attive* .

| Nome della DMV | Descrizione |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Restituisce informazioni sulle azioni di sessione di evento. |
| **sys.dm_xe_database_session_events** |Restituisce informazioni sugli eventi di sessione. |
| **sys.dm_xe_database_session_object_columns** |Mostra i valori di configurazione per gli oggetti associati a una sessione. |
| **sys.dm_xe_database_session_targets** |Restituisce informazioni sulle destinazioni della sessione. |
| **sys.dm_xe_database_sessions** |Restituisce una riga per ogni sessione di eventi con ambito nel database corrente. |

In Microsoft SQL Server, viste del * \_* catalogo simili sono denominate senza la parte di database del nome, ad esempio:

- **sys.dm_xe_sessions**, anziché il nome<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMV comuni a entrambi
Per gli eventi estesi sono disponibili DMV aggiuntive comuni a Microsoft SQL Server e database SQL di Azure:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Trovare gli eventi estesi, le azioni e le destinazioni disponibili

È possibile eseguire un semplice **SELECT** di SQL per ottenere un elenco di eventi, azioni e destinazioni disponibili.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Destinazioni per le sessioni di eventi del database SQL

Di seguito si trovano le destinazioni che possono acquisire i risultati dalle sessioni di eventi nel database SQL:

- [Destinazione buffer circolare](https://msdn.microsoft.com/library/ff878182.aspx) : contiene per un tempo breve i dati degli eventi nella memoria.
- [Destinazione contatore eventi](https://msdn.microsoft.com/library/ff878025.aspx) : conta tutti gli eventi che si verificano durante una sessione di eventi estesi.
- [Destinazione file evento](https://msdn.microsoft.com/library/ff878115.aspx) : scrive buffer completi in un contenitore di Archiviazione di Azure.

L’API [Tracciamento eventi per Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) non è disponibile per gli eventi estesi nel database SQL.

## <a name="restrictions"></a>Restrizioni

Esistono un paio di differenze relative alla sicurezza adatte all'ambiente cloud del database SQL:

- Gli eventi estesi si basano sul modello di isolamento single-tenant. Una sessione di eventi in un database non può accedere a dati o eventi da un altro database.
- Non è possibile emettere un'istruzione **CREATE EVENT SESSION** nel contesto del database **master**.

## <a name="permission-model"></a>Modello di autorizzazione

È necessario avere l'autorizzazione **Controllo** nel database per emettere un'istruzione **CREATE EVENT SESSION**. Il proprietario del database (dbo) dispone dell’autorizzazione **controllo** .

### <a name="storage-container-authorizations"></a>Autorizzazioni del contenitore di archiviazione

Il token della firma di accesso condiviso generato per il contenitore di Archiviazione di Azure deve specificare **rwl** per le autorizzazioni. Questo valore **rwl** fornisce le autorizzazioni seguenti:

- Lettura
- Scrittura
- Elenco

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Esistono scenari in cui un uso intensivo di eventi estesi può accumulare più memoria attiva di quanto è adatto per l'intero sistema. Pertanto il sistema di Database SQL di Azure imposta e regola in modo dinamico i limiti sulla quantità di memoria attiva che può essere accumulata da una sessione di eventi. Molti fattori vengono utilizzati nel calcolo dinamico.

Se si riceve un messaggio di errore che indica che è stato applicato un massimo di memoria, alcune azioni correttive da eseguire sono:

- Eseguire meno sessioni di eventi simultanee.
- Tramite le istruzioni **CREATE** e **ALTER** per le sessioni di eventi, ridurre la quantità di memoria specificata nella clausola **MAX\_MEMORY**.

### <a name="network-latency"></a>Latenza di rete

La destinazione del **file evento** potrebbe subire una latenza di rete o errori durante il mantenimento dei dati nei BLOB di archiviazione di Azure . Altri eventi nel database SQL potrebbero subire un ritardo mentre rimangono in attesa del completamento della comunicazione di rete. Questo ritardo può rallentare il carico di lavoro.

- Per ridurre questo rischio delle prestazioni, evitare di impostare l'opzione **EVENT_RETENTION_MODE** su **NO_EVENT_LOSS** nelle definizioni della sessione di eventi.

## <a name="related-links"></a>Collegamenti correlati

- [Uso di Azure PowerShell con Archiviazione di Azure.](../storage/common/storage-powershell-guide-full.md)
- [Cmdlet di Archiviazione di Azure](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Utilizzo di Azure PowerShell con Archiviazione di Azure](../storage/common/storage-powershell-guide-full.md) : fornisce informazioni complete su PowerShell e il servizio Archiviazione di Azure.
- [Come usare l'archiviazione BLOB da .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Post del blog di Jonathan Kehayias sugli eventi estesi in Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- La pagina Web *Aggiornamenti di Azure*, con visualizzazione limitata dal parametro ai soli aggiornamenti relativi al database SQL di Azure:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Altri argomenti con esempi di codice per gli eventi estesi sono disponibili ai collegamenti seguenti. È comunque necessario controllare regolarmente qualsiasi esempio per verificare se è destinato a Microsoft SQL Server o al database SQL di Azure. È quindi possibile decidere se sono necessarie alcune modifiche per eseguire l'esempio.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
