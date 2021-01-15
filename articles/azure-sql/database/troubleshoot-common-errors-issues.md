---
title: Risoluzione dei problemi di connessione comuni al database SQL di Azure
description: Viene descritta la procedura per risolvere i problemi di connessione al database SQL di Azure e risolvere altri problemi specifici del database SQL di Azure o di Azure SQL Istanza gestita
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2021
ms.openlocfilehash: 7c797c7e002f40a28e4be674c125c6ea5d60a13f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219063"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Risoluzione dei problemi di connettività e di altri errori con il database SQL di Azure e Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Si ricevono messaggi di errore quando la connessione al database SQL di Azure o al Istanza gestita SQL di Azure non riesce. Questi problemi di connessione possono essere causati dalla riconfigurazione, dalle impostazioni del firewall, da un timeout di connessione, da informazioni di accesso non corrette o dalla mancata applicazione delle procedure consigliate e delle linee guida di progettazione durante il processo di [progettazione](develop-overview.md) Inoltre, se viene raggiunto il limite massimo per alcune risorse del database SQL di Azure o di SQL Istanza gestita, non è più possibile connettersi.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Messaggi di errore temporanei di errore (40197, 40613 e altri)

L'infrastruttura Azure è in grado di riconfigurare dinamicamente i server quando si verificano carichi di lavoro intensi nel servizio del database SQL.  Questo comportamento dinamico potrebbe causare la perdita della connessione del programma client al database o all'istanza. Questo tipo di errore è chiamato *errore temporaneo*. Gli eventi di riconfigurazione del database avvengono in seguito a eventi pianificati (ad esempio nel caso degli aggiornamenti software) o non pianificati (ad esempio per l'arresto anomalo di un processo o per il bilanciamento del carico). La maggior parte degli eventi di riconfigurazione è di breve durata e deve essere completata in meno di 60 secondi al massimo. Tuttavia, il completamento di questi eventi in alcuni casi può richiedere più tempo, ad esempio quando una transazione di grandi dimensioni provoca un ripristino a esecuzione prolungata. La tabella seguente elenca diversi errori temporanei che le applicazioni possono ricevere durante la connessione al database SQL

### <a name="list-of-transient-fault-error-codes"></a>Elenco di codici di errore di errore temporanei

| Codice di errore | Gravità | Descrizione |
| ---:| ---:|:--- |
| 4060 |16 |Impossibile aprire il database "%.&#x2a;ls" richiesto dall'account di accesso. Accesso non riuscito. Per ulteriori informazioni, vedere gli [errori 4000 in 4999](/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Il servizio ha rilevato un errore durante l'elaborazione della richiesta. Riprova. Codice di errore %d.<br/><br/>Questo errore viene visualizzato quando il servizio non è disponibile a causa di aggiornamenti software o hardware, guasti hardware o altri problemi di failover. Nel codice di errore (%d) incorporato nel messaggio di errore 40197 sono contenute ulteriori informazioni sul tipo di errore o failover che si è verificato. Alcuni esempi dei codici di errore incorporati nel messaggio di errore 40197 sono 40020, 40143, 40166 e 40540.<br/><br/>La riconnessione si connette automaticamente a una copia integra del database. L'applicazione deve rilevare l'errore 40197, registrare il codice di errore incorporato (%d) nel messaggio per la risoluzione dei problemi e tentare la riconnessione al database SQL finché le risorse non saranno disponibili e la connessione non sarà stata ristabilita. Per ulteriori informazioni, vedere [errori temporanei](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Il servizio è attualmente occupato. Ripetere la richiesta dopo 10 secondi. ID evento imprevisto: %ls. Codice: %d. Per altre informazioni, vedere: <br/>&bull;&nbsp; [Limiti delle risorse di SQL Server logiche](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limiti basati su DTU per database singoli](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limiti basati su DTU per i pool elastici](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limiti basati su vCore per database singoli](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limiti basati su vCore per i pool elastici](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limiti delle risorse di Azure SQL istanza gestita](../managed-instance/resource-limits.md).|
| 40613 |17 |Il database '%.&#x2a;ls' nel server '%.&#x2a;ls' non è attualmente disponibile. Eseguire nuovamente la connessione in un secondo momento. Se il problema persiste, contattare il supporto tecnico indicando l'ID di traccia della sessione di '%.&#x2a;ls'.<br/><br/> Questo errore può verificarsi se nel database è già stata stabilita una connessione amministrativa dedicata (DAC). Per ulteriori informazioni, vedere [errori temporanei](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Impossibile elaborare una richiesta. Risorse insufficienti per elaborare la richiesta.<br/><br/>Il servizio è attualmente occupato. Si prega di ripetere la richiesta più tardi. Per altre informazioni, vedere: <br/>&bull;&nbsp; [Limiti delle risorse di SQL Server logiche](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limiti basati su DTU per database singoli](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limiti basati su DTU per i pool elastici](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limiti basati su vCore per database singoli](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limiti basati su vCore per i pool elastici](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limiti delle risorse di Azure SQL istanza gestita](../managed-instance/resource-limits.md). |
| 49919 |16 |Il processo non può creare o aggiornare la richiesta. Troppe operazioni di creazione o aggiornamento in corso per "%ld" della sottoscrizione.<br/><br/>Il servizio è occupato nell'elaborazione di più creazioni o aggiornamenti delle richieste per sottoscrizione o server. Le richieste al momento sono bloccate per l'ottimizzazione delle risorse. Eseguire la query [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) per le operazioni in sospeso. Attendere che le richieste di creazione o aggiornamento in sospeso siano complete o cancellare una delle richieste in sospeso e ripetere la richiesta in un secondo momento. Per altre informazioni, vedere: <br/>&bull;&nbsp; [Limiti delle risorse di SQL Server logiche](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limiti basati su DTU per database singoli](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limiti basati su DTU per i pool elastici](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limiti basati su vCore per database singoli](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limiti basati su vCore per i pool elastici](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limiti delle risorse di Azure SQL istanza gestita](../managed-instance/resource-limits.md). |
| 49920 |16 |Impossibile elaborare una richiesta. Troppe operazioni di creazione o aggiornamento in corso per "%ld" della sottoscrizione.<br/><br/>Il servizio è occupato nell'esecuzione di più richieste per la presente sottoscrizione. Le richieste al momento sono bloccate per l'ottimizzazione delle risorse. Eseguire la query [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) per lo stato delle operazioni. Attendere che le richieste in sospeso siano complete o cancellare una delle richieste in sospeso e ripetere la richiesta in un secondo momento. Per altre informazioni, vedere: <br/>&bull;&nbsp; [Limiti delle risorse di SQL Server logiche](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limiti basati su DTU per database singoli](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limiti basati su DTU per i pool elastici](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limiti basati su vCore per database singoli](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limiti basati su vCore per i pool elastici](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limiti delle risorse di Azure SQL istanza gestita](../managed-instance/resource-limits.md). |
| 4221 |16 |L'accesso alla replica secondaria in lettura non è riuscito a causa del tempo di attesa lungo di 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. La replica non è disponibile per l'accesso perché mancano le versioni di riga per le transazioni che erano in esecuzione quando la replica è stata riciclata. Per risolvere il problema, eseguire il rollback o il commit delle transazioni attive nella replica primaria. È possibile ridurre le occorrenze di questa condizione evitando transazioni di scrittura lunghe nella replica primaria. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passaggi per risolvere problemi di connettività temporanei

1. Controllare nel [Dashboard dei servizi di Microsoft Azure](https://azure.microsoft.com/status) le eventuali interruzioni note che si sono verificate durante il periodo nel quale sono stati segnalati errori dall'applicazione.
2. Le applicazioni che si connettono a un servizio cloud, come il database SQL di Azure, devono prevedere il verificarsi periodico di eventi di riconfigurazione e implementare la logica di ripetizione per gestire gli errori, invece di lasciare che vengano visualizzati dagli utenti come errori dell'applicazione.
3. Quando un database sta per raggiungere i limiti delle risorse, può sembrare che si stia verificando un problema di connettività temporaneo. Vedere [Limiti delle risorse](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Se i problemi di connettività persistono oppure se l'applicazione rileva l'errore per più di 60 secondi o se vengono visualizzate più occorrenze dell'errore in un dato giorno, inoltrare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito [Supporto tecnico di Azure](https://azure.microsoft.com/support/options) .

#### <a name="implementing-retry-logic"></a>Implementare della logica di riesecuzione

È consigliabile dotare il programma client di logica di ripetizione dei tentativi, in modo che sia in grado di ristabilire una connessione dopo aver concesso all'errore temporaneo un tempo sufficiente per correggersi.  È consigliabile attendere 5 secondi prima di riprovare. Al primo tentativo con un ritardo inferiore a 5 secondi, si rischia di sovraccaricare il servizio cloud. Per ogni tentativo successivo, aumentare in modo esponenziale il ritardo, fino a un massimo di 60 secondi.

Per esempi di codice relativi alla logica di ripetizione dei tentativi, vedere:

- [Connettersi in modo resiliente a SQL con ADO.NET](/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Connettere in modo resiliente a SQL con PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Per ulteriori informazioni sulla gestione degli errori temporanei nell'applicazione, vedere [risoluzione degli errori di connessione temporanei al database SQL](troubleshoot-common-connectivity-issues.md)

Una descrizione del *periodo di blocco* per i client che usano ADO.NET è disponibile nel [pool di connessioni (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Si è verificato un errore specifico dell'istanza o relativo alla rete durante il tentativo di stabilire una connessione al server

Il problema si verifica se l'applicazione non è in grado di connettersi al server.

Per risolvere il problema, provare a eseguire i passaggi (nell'ordine presentato) nella sezione [passaggi per correggere i problemi di connessione comuni](#steps-to-fix-common-connection-issues) .

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Il server o l'istanza non è stato trovato o non è accessibile (errori 26, 40, 10053)

### <a name="error-26-error-locating-server-specified"></a>Errore 26: errore durante l'individuazione del server specificato

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Errore 40: non è stato possibile aprire una connessione al server

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Errore 10053: si è verificato un errore A livello di trasporto durante la ricezione dei risultati dal server

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Questi problemi si verificano se l'applicazione non è in grado di connettersi al server.

Per risolvere questi problemi, provare a eseguire i passaggi (nell'ordine presentato) nella sezione [passaggi per correggere i problemi di connessione comuni](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Impossibile connettersi al server a causa di problemi del firewall

### <a name="error-40615-cannot-connect-to--servername-"></a>Errore 40615: Impossibile connettersi a < ServerName >

Per risolvere questo problema, [configurare le impostazioni del firewall nel database SQL tramite il portale di Azure](firewall-configure.md).

### <a name="error-5-cannot-connect-to--servername-"></a>Errore 5: non è possibile connettersi al < ServerName >

Per risolvere questo problema, assicurarsi che la porta 1433 sia aperta per le connessioni in uscita su tutti i firewall tra il client e Internet.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Impossibile accedere al server (errori 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Accesso non riuscito per l'utente ' < nome utente >'

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Per risolvere questo problema, contattare l'amministratore del servizio per fornire un nome utente e una password validi.

In genere, l'amministratore del servizio può utilizzare la procedura seguente per aggiungere le credenziali di accesso:

1. Accedere al server usando SQL Server Management Studio (SSMS).
2. Eseguire la query SQL seguente per verificare se il nome dell'account di accesso è disabilitato:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Se il nome corrispondente è disabilitato, abilitarlo usando l'istruzione seguente:

   ```sql
   Alter login <User name> enable
   ```

4. Se il nome utente dell'account di accesso SQL non esiste, crearlo attenendosi alla procedura seguente:

   1. In SSMS fare doppio clic su **sicurezza** per espanderla.
   2. Fare clic con il pulsante destro del mouse su **accessi**, quindi scegliere **nuovo account di accesso**.
   3. Nello script generato con segnaposto modificare ed eseguire la query SQL seguente:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = '<password, sysname, Change_Password>'
   GO
   ```

5. Fare doppio clic su **database**.
6. Selezionare il database a cui si desidera concedere l'autorizzazione utente.
7. Fare doppio clic su **sicurezza**.
8. Fare clic con il pulsante destro del mouse su **utenti** e quindi scegliere **nuovo utente**.
9. Nello script generato con segnaposto modificare ed eseguire la query SQL seguente:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N'db_owner', N'<user_name, sysname, user_name>'
   GO
   ```

   > [!NOTE]
   > È inoltre possibile utilizzare `sp_addrolemember` per eseguire il mapping di utenti specifici a ruoli specifici del database.

Per altre informazioni, vedere [gestione di database e account di accesso nel database SQL di Azure](./logins-create-manage.md).

## <a name="connection-timeout-expired-errors"></a>Errori di timeout della connessione scaduti

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): timeout della connessione scaduto

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): timeout scaduto

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. Entity. Core. EntityException: non è stato possibile aprire il provider sottostante

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Non è possibile connettersi al nome del server < >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Queste eccezioni possono verificarsi a causa di problemi di connessione o di query. Per verificare che l'errore sia causato da problemi di connettività, vedere [verificare se un errore è causato da un problema di connettività](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

I timeout di connessione si verificano perché l'applicazione non è in grado di connettersi al server. Per risolvere il problema, provare a eseguire i passaggi (nell'ordine presentato) nella sezione [passaggi per correggere i problemi di connessione comuni](#steps-to-fix-common-connection-issues) .

## <a name="resource-governance-errors"></a>Errori di governance delle risorse

### <a name="error-10928-resource-id-d"></a>Errore 10928: ID risorsa:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Per risolvere questo problema, provare con uno dei metodi seguenti:

- Verificare se sono presenti query con esecuzione prolungata.

  > [!NOTE]
  > Si tratta di un approccio minimalista che potrebbe non risolvere il problema. Per informazioni dettagliate sulla risoluzione dei problemi relativi al blocco delle query, vedere [comprendere e risolvere i problemi di blocco di SQL Azure](understand-resolve-blocking.md).

1. Eseguire la query SQL seguente per controllare la visualizzazione [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) per visualizzare tutte le richieste di blocco:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. Determinare il **buffer di input** per il blocco Head.
3. Ottimizzare la query del blocco Head.

   Per una procedura dettagliata per la risoluzione dei problemi, vedere la pagina relativa all'esecuzione di una [query nel cloud](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud). 

Se il database raggiunge costantemente il limite nonostante l'indirizzamento delle query di blocco e con esecuzione prolungata, provare a eseguire l'aggiornamento a un'edizione con più [edizioni](https://azure.microsoft.com/pricing/details/sql-database/)di risorse.

Per ulteriori informazioni sui limiti dei database, vedere  [limiti delle risorse del database SQL per i server](./resource-limits-logical-server.md).

### <a name="error-10929-resource-id-1"></a>Errore 10929: ID risorsa: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Errore 40501: il servizio è attualmente occupato

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Si tratta di un errore di limitazione del motore, che indica che sono stati superati i limiti delle risorse.

Per ulteriori informazioni sui limiti delle risorse, vedere [limiti delle risorse di SQL server logici](./resource-limits-logical-server.md).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Errore 40544: il database ha raggiunto la quota di dimensioni

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Questo errore si verifica quando il database ha raggiunto la quota di dimensioni.

I passaggi seguenti possono essere utili per aggirare il problema o fornire opzioni aggiuntive:

1. Controllare le dimensioni correnti del database usando il dashboard nel portale di Azure.

   > [!NOTE]
   > Per identificare le tabelle che utilizzano la maggior parte dello spazio e sono quindi potenziali candidati per la pulizia, eseguire la query SQL seguente:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC;
   ```

2. Se le dimensioni correnti non superano le dimensioni massime supportate per l'edizione, è possibile utilizzare ALTER DATABASE per aumentare l'impostazione MAXSIZE.
3. Se il database ha già superato le dimensioni massime supportate per l'edizione, provare a eseguire uno o più dei passaggi seguenti:

   - Eseguire le normali attività di pulizia del database. Ad esempio, pulire i dati indesiderati usando TRUNCATE/DELETE oppure spostare i dati in uscita usando SQL Server Integration Services (SSIS) o l'utilità per la copia bulk (BCP).
   - Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni.
   - Per la scalabilità del database, vedere [ridimensionare le risorse di un database singolo](./single-database-scale.md) e [ridimensionare le risorse](./elastic-pool-scale.md)

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Errore 40549: sessione terminata perché è presente una transazione con esecuzione prolungata

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Se si verifica ripetutamente questo errore, provare a risolvere il problema attenendosi alla procedura seguente:

1. Controllare la visualizzazione sys.dm_exec_requests per visualizzare tutte le sessioni aperte con un valore elevato per la colonna total_elapsed_time. Eseguire questo controllo eseguendo lo script SQL seguente:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. Determinare il buffer di input per la query con esecuzione prolungata.
3. Ottimizzare la query.

Prendere in considerazione anche l'invio in batch delle query. Per informazioni sull'invio in batch, vedere [come usare l'invio in batch per migliorare le prestazioni delle applicazioni del database SQL](../performance-improve-use-batching.md).

Per una procedura dettagliata per la risoluzione dei problemi, vedere la pagina relativa all'esecuzione di una [query nel cloud](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Errore 40551: la sessione è stata terminata a causa di un utilizzo eccessivo di TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Per risolvere il problema, seguire questa procedura:

1. Modificare le query per ridurre l'utilizzo dello spazio della tabella temporanea.
2. Eliminare gli oggetti temporanei dopo che non sono più necessari.
3. Troncare le tabelle o rimuovere le tabelle inutilizzate.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Errore 40552: la sessione è stata terminata a causa di un utilizzo eccessivo dello spazio del log delle transazioni

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Per risolvere il problema, procedere con i metodi seguenti:

- Il problema può verificarsi a causa di operazioni di inserimento, aggiornamento o eliminazione.
Provare a ridurre il numero di righe utilizzate immediatamente implementando la suddivisione in batch o la suddivisione in più transazioni più piccole.
- Il problema può verificarsi a causa di operazioni di ricompilazione dell'indice. Per risolvere questo problema, assicurarsi che il numero di righe interessate nella tabella * (dimensione media del campo aggiornato in byte + 80) < 2 gigabyte (GB).

  > [!NOTE]
  > Per la ricompilazione di un indice, le dimensioni medie del campo aggiornato devono essere sostituite dalla dimensione media dell'indice.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Errore 40553: la sessione è stata terminata a causa di un utilizzo eccessivo della memoria

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Per risolvere il problema, provare a ottimizzare la query.

Per una procedura dettagliata per la risoluzione dei problemi, vedere la pagina relativa all'esecuzione di una [query nel cloud](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabella dei messaggi di errore di governance delle risorse aggiuntivi

| Codice di errore | Gravità | Descrizione |
| ---:| ---:|:--- |
| 10928 |20 |ID risorsa: %d. Il limite di %s per il database è %d ed è stato raggiunto. Per altre informazioni, vedere [Limiti delle risorse del database SQL per database singoli e in pool](resource-limits-logical-server.md).<br/><br/>L'ID risorsa indica la risorsa che ha raggiunto il limite. Per i thread di lavoro, l’ID risorsa = 1. Per le sessioni, l'ID risorsa = 2.<br/><br/>Per altre informazioni su questo errore e su come risolverlo, vedere: <br/>&bull;&nbsp; [Limiti delle risorse di SQL Server logiche](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limiti basati su DTU per database singoli](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limiti basati su DTU per i pool elastici](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limiti basati su vCore per database singoli](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limiti basati su vCore per i pool elastici](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limiti delle risorse di Azure SQL istanza gestita](../managed-instance/resource-limits.md). |
| 10929 |20 |ID risorsa: %d. La %s di garanzia minima è %d, il limite massimo è %d e l'uso corrente per il database è %d. Tuttavia, il server attualmente è troppo occupato per supportare richieste superiori a %d per questo database. L'ID risorsa indica la risorsa che ha raggiunto il limite. Per i thread di lavoro, l’ID risorsa = 1. Per le sessioni, l'ID risorsa = 2. Per altre informazioni, vedere: <br/>&bull;&nbsp; [Limiti delle risorse di SQL Server logiche](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Limiti basati su DTU per database singoli](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limiti basati su DTU per i pool elastici](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limiti basati su vCore per database singoli](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limiti basati su vCore per i pool elastici](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limiti delle risorse di Azure SQL istanza gestita](../managed-instance/resource-limits.md). <br/>In caso contrario, riprovare più tardi. |
| 40544 |20 |Il database ha raggiunto la quota delle dimensioni. Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni. Per la scalabilità del database, vedere [ridimensionare le risorse di un database singolo](single-database-scale.md) e [ridimensionare le risorse](elastic-pool-scale.md)|
| 40549 |16 |Sessione terminata perché è presente una transazione a lunga esecuzione. Provare ad abbreviare la transazione. Per informazioni sull'invio in batch, vedere [come usare l'invio in batch per migliorare le prestazioni delle applicazioni del database SQL](../performance-improve-use-batching.md).|
| 40550 |16 |Sessione terminata perché ha acquisito troppi blocchi. Provare a leggere o modificare meno righe in una sola transazione. Per informazioni sull'invio in batch, vedere [come usare l'invio in batch per migliorare le prestazioni delle applicazioni del database SQL](../performance-improve-use-batching.md).|
| 40551 |16 |La sessione è stata terminata a causa dell'utilizzo eccessivo di `TEMPDB` . Provare a modificare la query per ridurre l'utilizzo dello spazio delle tabelle temporanee.<br/><br/>Se si usano oggetti temporanei, liberare spazio nel database `TEMPDB` rimuovendo gli oggetti temporanei se non sono più necessari per la sessione. Per ulteriori informazioni sull'utilizzo di tempdb nel database SQL, vedere [database tempdb nel database SQL](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Sessione terminata a causa di un utilizzo eccessivo dello spazio del log delle transazioni. Provare a modificare meno righe in una sola transazione. Per informazioni sull'invio in batch, vedere [come usare l'invio in batch per migliorare le prestazioni delle applicazioni del database SQL](../performance-improve-use-batching.md).<br/><br/>Se si eseguono inserimenti bulk usando l'utilità `bcp.exe` o la classe `System.Data.SqlClient.SqlBulkCopy`, provare a usare le opzioni `-b batchsize` o `BatchSize` per limitare il numero di righe copiate nel server in ogni transazione. In caso di ricompilazione di un indice con l'istruzione `ALTER INDEX`, provare a usare l'opzione `REBUILD WITH ONLINE = ON`. Per informazioni sulle dimensioni del log delle transazioni per il modello di acquisto vCore, vedere: <br/>&bull;&nbsp; [limiti basati su vCore per database singoli](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limiti basati su vCore per i pool elastici](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limiti delle risorse di Azure SQL istanza gestita](../managed-instance/resource-limits.md).|
| 40553 |16 |Sessione terminata a causa di un utilizzo eccessivo della memoria. Provare a modificare la query per elaborare meno righe.<br/><br/>La riduzione del numero di operazioni `ORDER BY` e `GROUP BY` nel codice Transact-SQL consente di ridurre i requisiti di memoria della query. Per la scalabilità del database, vedere [ridimensionare le risorse di un database singolo](single-database-scale.md) e [ridimensionare le risorse](elastic-pool-scale.md)|

## <a name="elastic-pool-errors"></a>Errori relativi al pool elastico

Di seguito sono elencati gli errori riguardanti la creazione e l'uso di pool elastici:

| Codice di errore | Gravità | Descrizione | Azione correttiva |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Il pool elastico ha raggiunto il limite di archiviazione. L'utilizzo dell'archiviazione per il pool elastico non può superare (%d) MB. Tentativo di scrittura dei dati in un database quando viene raggiunto il limite di archiviazione del pool elastico. Per informazioni sui limiti delle risorse, vedere: <br/>&bull;&nbsp; [Limiti basati su DTU per i pool elastici](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limiti basati su vCore per i pool elastici](resource-limits-vcore-elastic-pools.md). <br/> |Prendere in considerazione l'aumento delle DTU e/o l'aggiunta di risorse di archiviazione al pool elastico, se possibile, per aumentare il limite di archiviazione, ridurre le risorse di archiviazione usate dai singoli database all'interno del pool elastico o rimuovere database dal pool elastico. Per il ridimensionamento dei pool elastici, vedere [ridimensionare le risorse del pool elastico](elastic-pool-scale.md).|
| 10929 | 16 |La %s di garanzia minima è %d, il limite massimo è %d e l'uso corrente per il database è %d. Tuttavia, il server attualmente è troppo occupato per supportare richieste superiori a %d per questo database. Per informazioni sui limiti delle risorse, vedere: <br/>&bull;&nbsp; [Limiti basati su DTU per i pool elastici](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limiti basati su vCore per i pool elastici](resource-limits-vcore-elastic-pools.md). <br/> In caso contrario, riprovare più tardi. Numero minimo DTU/vCore per database; numero massimo DTU/vCore per database. Il numero totale dei processi di lavoro simultanei (richieste) in tutti i database nel pool elastico ha tentato di superare il limite del pool. |Prendere in considerazione l'aumento delle DTU o dei vCore del pool elastico, se possibile, per aumentare il limite del ruolo di lavoro, o rimuovere database dal pool elastico. |
| 40844 | 16 |Il database '%ls' sul Server '%ls' è un database versione '%ls' in un pool elastico e non può avere una relazione di copia continua.  |N/D |
| 40857 | 16 |Pool elastico non trovato per il server: '%ls', nome del pool elastico: '%ls'. Il pool elastico specificato non esiste nel server specificato. | Fornire un nome pool elastico valido. |
| 40858 | 16 |Il pool elastico '%ls' esiste già nel server: '%ls'. Il pool elastico specificato esiste già nel server specificato. | Fornire un nuovo nome pool elastico. |
| 40859 | 16 |Il pool elastico non supporta il livello di servizio '%ls'. Il livello di servizio specificato non è supportato per il provisioning del pool elastico. |Fornire l'edizione corretta oppure lasciare vuoto il livello di servizio per utilizzare il livello di servizio predefinito. |
| 40860 | 16 |La combinazione di pool elastico '%ls' e di obiettivo di servizio '%ls' non è valida. Il pool elastico e il livello di servizio possono essere specificati insieme solo se il tipo di risorsa specificato è 'ElasticPool'. |Specificare la combinazione corretta di pool elastico e livello di servizio. |
| 40861 | 16 |L'edizione del database "%.*ls" non può essere diversa dal livello di servizio del pool elastico, ovvero "%.* ls". L'edizione del database è diversa dal livello di servizio del pool elastico. |Non specificare un'edizione di database diversa dal livello di servizio del pool elastico.  Si noti che non è necessario specificare l'edizione del database. |
| 40862 | 16 |Il nome del pool elastico deve essere specificato se viene specificato l'obiettivo di servizio del pool elastico. L’obiettivo di servizio del pool elastico non identifica in modo univoco un pool elastico. |Specificare il nome del pool elastico se si usa l'obiettivo di servizio del pool elastico. |
| 40864 | 16 |Le DTU per il pool elastico devono essere almeno (%d) DTU per il livello di servizio '%.*ls'. Tentativo di impostare le DTU per il pool elastico al di sotto del limite minimo. |Riprovare a impostare le DTU per il pool elastico almeno al limite minimo. |
| 40865 | 16 |Le DTU per il pool elastico non possono superare (%d) DTU per il livello di servizio '%.*ls'. Tentativo di impostare le DTU per il pool elastico al di sopra del limite massimo. |Riprovare a impostare le DTU per il pool elastico non oltre il limite massimo. |
| 40867 | 16 |Il numero massimo di DTU per database deve essere almeno (%d) per il livello di servizio "%.*ls". Tentativo di impostare il numero massimo di DTU per database al di sotto del limite supportato. | Prendere in considerazione l'uso del livello di servizio del pool elastico che supporta l'impostazione desiderata. |
| 40868 | 16 |Il numero massimo di DTU per database non può superare (%d) per il livello di servizio '%.*ls'. Tentativo di impostare il numero massimo di DTU per database oltre il limite supportato. | Prendere in considerazione l'uso del livello di servizio del pool elastico che supporta l'impostazione desiderata. |
| 40870 | 16 |Il numero minimo di DTU per database non può superare (%d) per il livello di servizio '%.*ls'. Tentativo di impostare il numero minimo di DTU per database oltre il limite supportato. | Prendere in considerazione l'uso del livello di servizio del pool elastico che supporta l'impostazione desiderata. |
| 40873 | 16 |Il numero di database (%d) e il numero minimo di DTU per ogni database (%d) non può superare le DTU del pool elastico (%d). Il tentativo di specificare il numero minimo di DTU per i database nel pool elastico che supera il numero di DTU del pool elastico. | Prendere in considerazione l'aumento delle DTU del pool elastico, ridurre il numero minimo di DTU per database o diminuire il numero di database nel pool elastico. |
| 40877 | 16 |Impossibile eliminare un pool elastico, a meno che non contenga alcun database. Il pool elastico contiene uno o più database e pertanto non può essere eliminato. |Rimuovere i database dal pool elastico per eliminarlo. |
| 40881 | 16 |Il pool elastico '%.*ls' ha raggiunto il limite del numero di database.  Il limite per il numero di database per il pool elastico non può superare (%d) per un pool elastico con (%d) DTU. Tentativo di creare o aggiungere il database al pool elastico quando è stato raggiunto il limite del numero di database del pool elastico. | Prendere in considerazione l'aumento delle DTU del pool elastico, se possibile, per aumentare il limite dei relativi database o rimuovere i database dal pool elastico. |
| 40889 | 16 |Impossibile ridurre il limite delle DTU o della memoria per il pool elastico '%.*ls' dal momento che tale operazione non fornirebbe spazio di archiviazione sufficiente per i relativi database. Tentativo di ridurre il limite di archiviazione del pool elastico al di sotto del relativo utilizzo di memoria. | Prendere in considerazione la riduzione dell'uso della memoria dei singoli database nel pool elastico o rimuovere i database dal pool per ridurre le relative DTU o il limite di archiviazione. |
| 40891 | 16 |Il numero minimo di DTU per database (%d) non può superare il numero massimo DTU per database (%d). Tentativo di impostare il numero minimo di DTU per database su un valore superiore al numero massimo di DTU per database. |Verificare che il numero minimo di DTU per database non superi il numero massimo di DTU per database. |
| TBD | 16 |Le dimensioni di archiviazione di un singolo database in un pool elastico non possono superare le dimensioni massime consentite dal pool elastico del livello di servizio "%.*ls". Le dimensioni massime per il database superano le dimensioni massime consentite per il livello di servizio del pool elastico. |Impostare le dimensioni massime del database entro i limiti delle dimensioni massime consentite dal livello di servizio del pool elastico. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Impossibile aprire il database "Master" richiesto dall'account di accesso. Accesso non riuscito

Questo problema si verifica perché l'account non dispone dell'autorizzazione per accedere al database master. Per impostazione predefinita, tuttavia, SQL Server Management Studio (SSMS) tenta di connettersi al database master.

Per risolvere questo problema, seguire questa procedura:

1. Nella schermata di accesso di SSMS selezionare **Opzioni** e quindi selezionare **Proprietà connessione**.
2. Nel campo **Connetti al database** immettere il nome del database predefinito dell'utente come database di accesso predefinito, quindi selezionare **Connetti**.

   ![Proprietà di connessione](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Verificare se un errore è causato da un problema di connettività

Per verificare se un errore è causato da un problema di connettività, esaminare l'analisi dello stack per i frame che mostrano le chiamate per aprire una connessione come le seguenti (si noti il riferimento alla classe **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Quando l'eccezione viene attivata dai problemi di query, si noterà uno stack di chiamate simile al seguente (si noti il riferimento alla classe **SqlCommand** ). In questa situazione, [ottimizzare le query](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Per istruzioni aggiuntive sull'ottimizzazione delle prestazioni, vedere le risorse seguenti:

- [Come gestire gli indici e le statistiche di Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [Ottimizzazione manuale delle prestazioni delle query in Database SQL di Azure](./performance-guidance.md)
- [Monitoraggio delle prestazioni del database SQL di Azure tramite le viste a gestione dinamica](./monitoring-with-dmvs.md)
- [Uso dell'archivio query nel database SQL di Azure](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)

## <a name="steps-to-fix-common-connection-issues"></a>Steps to fix common connection issues (Passaggi per risolvere i problemi di connessione comuni)

1. Verificare che TCP/IP sia abilitato come protocollo client nel server applicazioni. Per ulteriori informazioni, vedere [configure client Protocols](/sql/database-engine/configure-windows/configure-client-protocols). Nei server applicazioni in cui non sono installati gli strumenti SQL, verificare che TCP/IP sia abilitato eseguendo **cliconfg.exe** (SQL Server utilità di rete client).
2. Controllare la stringa di connessione dell'applicazione per assicurarsi che sia configurata correttamente. Verificare, ad esempio, che la stringa di connessione specifichi la porta corretta (1433) e il nome completo del server.
Vedere [ottenere le informazioni di connessione](./connect-query-ssms.md#get-server-connection-information).
3. Provare ad aumentare il valore di timeout della connessione. Si consiglia di usare un timeout di connessione di almeno 30 secondi.
4. Testare la connettività tra il server applicazioni e il database SQL di Azure usando [SQL Server Management Studio (SSMS)](./connect-query-ssms.md), un file UDL, un ping o un Telnet. Per ulteriori informazioni, vedere [risoluzione dei](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) problemi di connettività e [diagnostica per problemi di connettività](./troubleshoot-common-connectivity-issues.md#diagnostics).

   > [!NOTE]
   > Come passaggio per la risoluzione dei problemi, è inoltre possibile testare la connettività in un computer client diverso.

5. Come procedura consigliata, assicurarsi che la logica di ripetizione dei tentativi sia attiva. Per altre informazioni sulla logica di ripetizione dei tentativi, vedere [risolvere gli errori temporanei e gli errori di connessione al database SQL](./troubleshoot-common-connectivity-issues.md).

Se questi passaggi non consentono di risolvere il problema, provare a raccogliere più dati e quindi contattare il supporto tecnico. Se l'applicazione è un servizio cloud, abilitare la registrazione. Questo passaggio restituisce l'indicatore di data e ora UTC dell'errore. Inoltre, il database SQL restituisce l'ID traccia. Il [servizio supporto tecnico clienti Microsoft](https://azure.microsoft.com/support/options/) può utilizzare queste informazioni.

Per altre informazioni su come abilitare la registrazione, vedere [abilitare la registrazione diagnostica per le app nel servizio app Azure](../../app-service/troubleshoot-diagnostic-logs.md).

## <a name="next-steps"></a>Passaggi successivi

- [Architettura della connettività del database SQL di Azure](./connectivity-architecture.md)
- [Controlli di accesso alla rete del database SQL di Azure e di Azure sinapsi Analytics](./network-access-controls-overview.md)