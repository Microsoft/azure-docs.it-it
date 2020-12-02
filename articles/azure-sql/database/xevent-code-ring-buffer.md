---
title: Codice del buffer circolare per eventi estesi
description: Fornisce un esempio di codice Transact-SQL reso semplice e veloce tramite l'uso della destinazione del buffer circolare nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: a646588616b874e40b1ed2a5a0b5e691b075075d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487304"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Codice di destinazione del buffer circolare per eventi estesi nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Si desidera un esempio di codice completo per il modo più semplice e rapido per acquisire e segnalare informazioni per un evento esteso durante un test. La destinazione più semplice per i dati degli eventi estesi è la [destinazione del buffer circolare](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)).

In questo argomento viene presentato un esempio di codice Transact-SQL che:

1. Crea una tabella con i dati a scopo dimostrativo.
2. Crea una sessione per un evento esteso esistente, ovvero **sqlserver.sql_statement_starting**.

   * L'evento è limitato a istruzioni SQL che contengono una determinata stringa di aggiornamento: **statement LIKE '%UPDATE tabEmployee%'**.
   * Sceglie di inviare l'output dell'evento a una destinazione di tipo buffer circolare, ovvero **package0.ring_buffer**.
3. Avvia la sessione dell'evento.
4. Esegue un paio di semplici istruzioni SQL UPDATE.
5. Esegue un'istruzione SQL SELECT per recuperare l'output dell'evento dal buffer circolare.

   * **sys.dm_xe_database_session_targets** e altre viste a gestione dinamica (DMV) sono unite.
6. Arresta la sessione dell'evento.
7. Elimina la destinazione del buffer circolare, per rilasciare le relative risorse.
8. Elimina la sessione dell'evento e la tabella dimostrativa.

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. È possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Qualsiasi database in cui è possibile creare una tabella.
  
  * Facoltativamente, è possibile [creare un database dimostrativo **AdventureWorksLT**](single-database-create-quickstart.md) in pochi minuti.
* SQL Server Management Studio (ssms.exe), idealmente l'ultima versione di aggiornamento mensile.
  È possibile scaricare la versione più recente di ssms.exe da:
  
  * Argomento intitolato [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
  * [Un collegamento diretto al download.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Esempio di codice

Con alcune lievi modifiche, è possibile eseguire il seguente esempio di codice di buffer circolare nel database SQL di Azure o in Microsoft SQL Server. La differenza è la presenza del nodo '_database' nel nome di alcune viste a gestione dinamica (DMV) nella clausola FROM nel passaggio 5. Ad esempio:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Contenuto del buffer circolare

Per eseguire l'esempio di codice, è stato usato `ssms.exe`.

Per visualizzare i risultati, è stato fatto clic sulla cella sotto l'intestazione di colonna **target_data_XML**.

Quindi, nel riquadro dei risultati, è stato fatto clic sulla cella sotto l'intestazione di colonna **target_data_XML**. Tramite questo clic del mouse è stata creata un'altra scheda del file in ssms.exe, in cui è stato visualizzato il contenuto della cella del risultato, come XML.

L'output è illustrato nella sezione seguente. Sembra lungo, ma è composto solo da due elementi **\<event>** .

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>Rilasciare le risorse usate dal buffer circolare

Al termine dell'uso del buffer circolare, è possibile rimuoverlo e rilasciare le relative risorse eseguendo un'istruzione **ALTER** simile alla seguente:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

La definizione della sessione dell'evento viene aggiornata, ma non eliminata. Successivamente, è possibile aggiungere un'altra istanza del buffer circolare alla sessione dell'evento:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>Ulteriori informazioni

L'argomento principale per gli eventi estesi in un database SQL di Azure è:

* [Considerazioni sugli eventi estesi nel database SQL di Azure](xevent-db-diff-from-svr.md), che illustra alcuni aspetti degli eventi estesi che presentano differenze tra il database SQL di Azure e Microsoft SQL Server.

Altri argomenti con esempi di codice per gli eventi estesi sono disponibili ai collegamenti seguenti. È comunque necessario controllare regolarmente qualsiasi esempio per verificare se è destinato a Microsoft SQL Server o al database SQL di Azure. È quindi possibile decidere se sono necessarie alcune modifiche per eseguire l'esempio.

* Esempio di codice per il database SQL di Azure: [Codice di destinazione del file evento per eventi estesi nel database SQL di Azure](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->