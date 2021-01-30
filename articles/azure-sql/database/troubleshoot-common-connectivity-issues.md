---
title: Utilizzo degli errori temporanei
description: Informazioni su come risolvere, diagnosticare ed evitare un errore di connessione SQL o un errore temporaneo durante la connessione al database SQL di Azure, al Istanza gestita di Azure SQL e ad Azure sinapsi Analytics.
keywords: connessione sql,stringa di connessione,problemi di connettività,errore temporaneo,errore di connessione
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: dalechen
ms.author: ninarn
ms.reviewer: sstein, vanto
ms.date: 01/14/2020
ms.openlocfilehash: 9f2e755047910aefa89c2f187cda956aca608b98
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093758"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>Risolvere gli errori di connessione temporanei nel database SQL e in SQL Istanza gestita

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Questo articolo descrive come prevenire, risolvere i problemi, diagnosticare e mitigare gli errori di connessione e gli errori temporanei che l'applicazione client incontra quando interagisce con il database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics. Informazioni su come configurare la logica di ripetizione dei tentativi, compilare la stringa di connessione e modificare altre impostazioni di connessione.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Errori temporanei

Un errore temporaneo è un errore la cui causa sottostante si risolverà automaticamente in modo rapido. Una causa occasionale di errori temporanei è costituita dal cambio rapido di risorse hardware da parte del sistema Azure per ottenere un bilanciamento migliore dei diversi carichi di lavoro. La maggior parte di questi eventi di riconfigurazione viene completata in meno di 60 secondi. Durante questo intervallo di riconfigurazione, è possibile che si verifichino problemi di connessione al database nel database SQL. Le applicazioni che si connettono al database devono essere compilate in modo da prevedere questi errori temporanei. Per gestirli, implementare la logica di ripetizione nel codice anziché mostrarli agli utenti come errori dell'applicazione.

Se il programma client usa ADO.NET, l'errore temporaneo verrà segnalato al programma tramite la generazione di un'eccezione **SqlException**.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Connessione e comando

Ritentare il database SQL e la connessione SQL Istanza gestita o ristabilirlo, a seconda di quanto segue:

- **Si verifica un errore temporaneo durante un tentativo di connessione**

Attendere alcuni secondi e quindi tentare nuovamente la connessione.

- **Si verifica un errore temporaneo durante un comando di query di SQL Istanza gestita e database SQL**

Non ripetere immediatamente il comando. È invece consigliabile stabilire una nuova connessione dopo un breve intervallo di tempo. Quindi eseguire di nuovo il comando.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logica di ripetizione dei tentativi per errori temporanei

I programmi client in cui occasionalmente si verifica un errore temporaneo sono più affidabili se contengono una logica di ripetizione dei tentativi. Quando il programma comunica con il database nel database SQL tramite middleware di terze parti, chiedere al fornitore se il middleware contiene la logica di ripetizione dei tentativi per gli errori temporanei.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principi per la ripetizione dei tentativi

- Se l'errore è temporaneo, provare a ristabilire la connessione.
- Non ritentare direttamente un database SQL o un' `SELECT` istruzione sql istanza gestita che non è riuscita con un errore temporaneo. Stabilire invece una nuova connessione e quindi provare a eseguire di nuovo l'istruzione `SELECT`.
- Quando un database SQL o un'istruzione SQL Istanza gestita `UPDATE` non riesce con un errore temporaneo, stabilire una nuova connessione prima di ritentare l'aggiornamento. La logica di ripetizione dei tentativi deve assicurare il completamento dell'intera transazione di database o il rollback dell'intera transazione.

### <a name="other-considerations-for-retry"></a>Altre considerazioni per la ripetizione dei tentativi

- Un programma batch avviato automaticamente dopo l'orario di lavoro e con completamento previsto prima del mattino può permettersi di attendere a lungo tra i diversi tentativi.
- Un programma di interfaccia utente deve tenere conto della tendenza degli utenti a desistere dopo un'attesa troppo lunga. La soluzione non deve prevedere nuovi tentativi con intervalli di pochi secondi, perché un criterio simile può inondare il sistema con un numero eccessivo di richieste.

### <a name="interval-increase-between-retries"></a>Incremento dell'intervallo tra i tentativi

È consigliabile attendere 5 secondi prima di riprovare. Al primo tentativo con un ritardo inferiore a 5 secondi, si rischia di sovraccaricare il servizio cloud. Per ogni tentativo successivo, aumentare in modo esponenziale il ritardo, fino a un massimo di 60 secondi.

Per una descrizione del periodo di blocco per i client che usano ADO.NET, vedere [pool di connessioni (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

È anche possibile che si voglia impostare un numero massimo di nuovi tentativi prima dell'autoterminazione del programma.

### <a name="code-samples-with-retry-logic"></a>Esempi di codice con logica di ripetizione dei tentativi

Esempi di codice di logica di ripetizione dei tentativi sono disponibili in:

- [Connettersi in modo resiliente a SQL di Azure con ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Connettersi in modo resiliente a SQL di Azure con PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Eseguire test sulla logica di ripetizione tentativi

Per testare la logica di ripetizione dei tentativi, è necessario simulare o provocare un errore che può essere corretto mentre il programma è ancora in esecuzione.

#### <a name="test-by-disconnecting-from-the-network"></a>Eseguire il test mediante la disconnessione dalla rete

Uno dei modi per testare la logica di ripetizione dei tentativi consiste nel disconnettere il computer client dalla rete mentre il programma è in esecuzione. L'errore è:

- **SqlException.Number** = 11001
- Messaggio: "Host sconosciuto"

Come parte del primo tentativo, è possibile riconnettere il computer client alla rete, quindi tentare di connettersi.

Per semplificare il test, disconnettere il computer dalla rete prima di avviare il programma. Il programma riconoscerà quindi un parametro di runtime che ha le conseguenze seguenti sul programma:

- Aggiunta temporanea di 11001 al rispettivo elenco di errori da considerare temporanei.
- Tentativo della prima connessione come di consueto.
- Dopo il rilevamento dell'errore, rimozione di 11001 dall'elenco.
- Visualizzazione di un messaggio che richiede all'utente di connettere il computer alla rete.
- Sospensione delle ulteriori esecuzioni con il metodo **Console.ReadLine** o una finestra di dialogo con un pulsante OK. L'utente preme il tasto INVIO dopo la connessione del computer alla rete.
- Nuovo tentativo di connessione, con esito positivo previsto.

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>Eseguire un test eseguendo l'errore ortografico del nome utente durante la connessione

Il programma può intenzionalmente digitare in modo errato il nome utente prima del primo tentativo di connessione. L'errore è:

- **SqlException.Number** = 18456
- Messaggio: "Accesso non riuscito per l'utente 'WRONG_MyUserName'."

Come parte del primo tentativo, il programma può correggere l'errore di digitazione e quindi provare a connettersi.

Per semplificare il test, il programma riconosce un parametro di runtime che ha le conseguenze seguenti sul programma:

- Aggiunta temporanea di 18456 al rispettivo elenco di errori da considerare temporanei.
- Aggiunta intenzionale di 'WRONG_' al nome utente.
- Dopo il rilevamento dell'errore, rimozione di 18456 dall'elenco.
- Rimozione di 'WRONG_' dal nome utente.
- Nuovo tentativo di connessione, con esito positivo previsto.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametri di SqlConnection di .NET per nuovi tentativi di connessione

Se il programma client si connette al database nel database SQL usando la classe .NET Framework **System. Data. SqlClient. SqlConnection**, usare .NET 4.6.1 o versioni successive (o .NET Core) per poter usare la funzionalità di ripetizione dei tentativi di connessione. Per ulteriori informazioni sulla funzionalità, vedere [SqlConnection. ConnectionString Property](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=netframework-4.8&preserve-view=true).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Quando si crea la [stringa di connessione](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring) per l'oggetto **SqlConnection**, coordinare i valori tra i parametri seguenti:

- **ConnectRetryCount**: il &nbsp; &nbsp; valore predefinito è 1. L'intervallo consentito è tra 0 e 255.
- **ConnectRetryInterval**: il &nbsp; &nbsp; valore predefinito è 10 secondi. L'intervallo consentito è tra 1 e 60.
- **Timeout connessione**: il &nbsp; &nbsp; valore predefinito è 15 secondi. L'intervallo consentito è tra 0 e 2147483647.

In particolare, i valori scelti devono rendere vera l'uguaglianza seguente: Timeout di connessione = ConnectRetryCount * ConnectionRetryInterval

Ad esempio, se il numero = 3 e l'intervallo = 10 secondi, un timeout di soli 29 secondi non garantisce al sistema il tempo sufficiente per il terzo tentativo e il tentativo di connessione finale: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Connessione e comando

I parametri **ConnectRetryCount** e **ConnectRetryInterval** consentono all'oggetto **SqlConnection** di ripetere l'operazione di connessione senza interferire con il programma, ad esempio per restituire il controllo al programma. I tentativi possono verificarsi nelle situazioni seguenti:

- Chiamata al metodo SqlConnection. Open
- SqlConnection.Exechiamata al metodo carino

È importante sottolineare che, se si verifica un errore temporaneo durante l'esecuzione della *query*, l'oggetto **SqlConnection** non ripete l'operazione di connessione e certamente non ritenta l'esecuzione della query. Prima di inviare la query per l'esecuzione, tuttavia, **SqlConnection** controlla rapidamente la connessione e, se viene rilevato un problema, **SqlConnection** ritenta l'operazione di connessione. Se il tentativo ha esito positivo, la query viene inviata per l'esecuzione.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Opportunità di combinare ConnectRetryCount con la logica di ripetizione dei tentativi nell'applicazione

Si supponga che l'applicazione disponga di una logica di ripetizione dei tentativi particolarmente avanzata, in cui l'operazione di connessione può essere ritentata fino a quattro volte. Se si aggiunge **ConnectRetryInterval** e **ConnectRetryCount** = 3 alla stringa di connessione, il numero dei tentativi aumenterà a 4 * 3 = 12 tentativi. Un numero così elevato di tentativi potrebbe non essere consigliabile.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>Connessioni al database nel database SQL

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Connessione: stringa di connessione

La stringa di connessione necessaria per connettersi al database è leggermente diversa dalla stringa utilizzata per connettersi a SQL Server. È possibile copiare la stringa di connessione per il database dal [portale di Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Connessione: indirizzo IP

È necessario configurare il database SQL in modo che accetti la comunicazione dall'indirizzo IP del computer che ospita il programma client. Per impostare questa configurazione, modificare le impostazioni del firewall tramite il [portale di Azure](https://portal.azure.com/).

Se si dimentica di configurare l'indirizzo IP, il programma restituirà un messaggio di errore che indica la necessità dell'indirizzo IP.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

Per altre informazioni, vedere [configurare le impostazioni del firewall nel database SQL](firewall-configure.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Connessione: porte

In genere è sufficiente assicurarsi che soltanto la porta 1433 sia aperta per le comunicazioni in uscita sul computer che ospita il programma client.

Ad esempio, se il programma client è ospitato in un computer Windows, è possibile usare Windows Firewall nell'host per aprire la porta 1433.

1. Aprire il Pannello di controllo.
2. Selezionare **tutti gli elementi del pannello di controllo**  >  **Windows Firewall**  >  **Impostazioni avanzate**  >  **regole in uscita**  >  **azioni**  >  **nuova regola**.

Se il programma client si trova su una macchina virtuale (VM) di Azure, leggere [Porte superiori a 1433 per ADO.NET 4.5 e il database SQL](adonet-v12-develop-direct-route-ports.md).

Per informazioni complementari sulla configurazione di porte e indirizzi IP nel database, vedere [firewall del database SQL di Azure](firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Connessione: ADO.NET 4.6.2 o versioni successive

Se il programma usa classi ADO.NET come **System.Data.SqlClient.SqlConnection** per la connessione al database SQL, è consigliabile usare .NET Framework 4.6.2 o versioni successive.

#### <a name="starting-with-adonet-462"></a>A partire da ADO.NET 4.6.2

- Il tentativo di apertura della connessione è stato ritentato immediatamente per SQL di Azure, migliorando così le prestazioni delle app abilitate per il cloud.

#### <a name="starting-with-adonet-461"></a>A partire da ADO.NET 4.6.1

- Per il database SQL, è possibile migliorare l'affidabilità aprendo una connessione con il metodo **SqlConnection.Open**. Il metodo **Open** incorpora ora meccanismi di ripetizione dei tentativi di tipo "massimo sforzo" in risposta agli errori temporanei, per alcuni errori entro l'intervallo di durata della connessione.
- È supportato il pool di connessioni, inclusa una verifica efficiente del corretto funzionamento dell'oggetto connessione fornito al programma.

Quando si usa un oggetto connessione da un pool di connessioni, è consigliabile che il programma chiuda temporaneamente la connessione se questa non deve essere usata immediatamente. Riaprire una connessione non ha alcun costo, a differenza della creazione di una nuova connessione.

Se si usa ADO.NET 4.0 o versioni precedenti, è consigliabile eseguire l'aggiornamento alla versione più recente di ADO.NET. A partire da agosto 2018, è possibile [scaricare ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostica

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostica: verificare se le utilità si possono connettere

Se il programma non riesce a connettersi al database nel database SQL, un'opzione di diagnostica consiste nel provare a connettersi con un programma di utilità. Idealmente l'utilità si connette mediante la stessa libreria usata dal programma.

In qualsiasi computer Windows è possibile provare queste utilità:

- SQL Server Management Studio (ssms.exe), che si connette tramite ADO.NET
- `sqlcmd.exe`, che si connette tramite [ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)

Dopo la connessione del programma, verificare il funzionamento di una breve query SQL SELECT.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostica: verificare le porte aperte

Se si ritiene che i tentativi di connessione abbiano esito negativo a causa di problemi di porta, è possibile eseguire sul computer un'utilità che segnala le configurazioni delle porte.

In Linux possono risultare utili le utilità seguenti:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: modificare il valore di esempio con il proprio indirizzo IP.

In Windows è possibile usare l'utilità [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148). Di seguito è riportato un esempio di esecuzione che ha eseguito una query sulla situazione della porta su un database nel database SQL ed è stata eseguita in un computer portatile:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostica: registrare gli errori

La diagnosi di un problema intermittente è spesso agevolata dal rilevamento di uno schema generale nel corso di giorni o settimane.

Il client può supportare l'analisi tramite la registrazione di tutti gli errori rilevati. È possibile che si riesca a correlare le voci del log con i dati di errore registrati internamente dal database SQL.

Enterprise Library 6 (EntLib60) offre classi .NET gestite per semplificare la registrazione. Per altre informazioni, vedere [5 - Più facile che mai: uso del blocco applicazione di registrazione](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostica: cercare errori nei log di sistema

Ecco alcune istruzioni Transact-SQL SELECT che eseguono query nei log degli errori e alla ricerca di altre informazioni.

| Query di un log | Descrizione |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |La visualizzazione [sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database) offre informazioni sui singoli eventi, inclusi quelli che possono causare errori temporanei o di connettività.<br/><br/>In teoria, è possibile correlare i valori **start_time** o **end_time** con le informazioni relative al momento in cui si sono verificati problemi nel programma client.<br/><br/>È necessario connettersi al database *master* per eseguire questa query. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |La visualizzazione [sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database) offre conteggi aggregati di tipi di eventi per la diagnostica aggiuntiva.<br/><br/>È necessario connettersi al database *master* per eseguire questa query. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostica: cercare eventi relativi a problemi nel log del database SQL

È possibile cercare voci relative agli eventi problematici nel log del database SQL. Provare a eseguire l'istruzione Transact-SQL SELECT seguente nel database *master* :

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Alcune righe restituite da sys.fn_xe_telemetry_blob_target_read_file

Nell'esempio seguente viene illustrato il possibile aspetto di una riga restituita. I valori Null mostrati sono spesso non Null in altre righe.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

Enterprise Library 6 (EntLib60) è un Framework di classi .NET che consente di implementare client affidabili di servizi cloud, uno dei quali è il database SQL. Gli argomenti dedicati a ogni area per cui EntLib60 può risultare utile sono disponibili in [Enterprise Library 6 - Aprile 2013](/previous-versions/msp-n-p/dn169621(v=pandp.10)).

Logica di ripetizione dei tentativi per la gestione degli errori temporanei è un'area in cui EntLib60 può essere utile. Per altre informazioni, vedere [4 - Perseveranza, il segreto di tutti i successi: uso del Blocco di applicazioni per la gestione degli errori temporanei](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

> [!NOTE]
> Il codice sorgente per EntLib60 è disponibile per il download pubblico nell'[Area download](https://github.com/MicrosoftArchive/enterprise-library). Microsoft non prevede di fornire altre funzionalità o aggiornamenti di manutenzione per EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classi di EntLib60 per errori temporanei e ripetizione dei tentativi

Le classi seguenti di EntLib60 sono particolarmente utili per la logica di ripetizione dei tentativi. Tutte queste classi sono disponibili nello spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** o nei livelli sottostanti.

Nello spazio dei nomi **Microsoft. practices. enterpriselibrary. TransientFaultHandling**:

- **RetryPolicy**
  - **ExecuteAction**
- Classe **ExponentialBackoff**
- **SqlDatabaseTransientErrorDetectionStrategy**
- **ReliableSqlConnection**
  - **ExecuteCommand**

Nello spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy**
- **NeverTransientErrorDetectionStrategy**

Ecco alcuni collegamenti alle informazioni relative a EntLib60:

- Download gratuito dell'eBook [Guida per sviluppatori di Microsoft Enterprise Library, seconda edizione](https://www.microsoft.com/download/details.aspx?id=41145).
- Procedure consigliate: [Indicazioni generali per la ripetizione di tentativi](/azure/architecture/best-practices/transient-faults) offre un'eccellente discussione approfondita della logica di ripetizione dei tentativi.
- Download NuGet di [Enterprise Library - Blocco applicazione per la gestione di errori temporanei 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: il blocco di registrazione

- Il blocco di registrazione è una soluzione a flessibilità e configurabilità elevata che consente di:
  - Creare e archiviare messaggi di log in diverse posizioni.
  - Classificare e filtrare i messaggi.
  - Raccogliere informazioni contestuali utili per il debug e la traccia, oltre che per i requisiti di controllo e di registrazione generale.
- Il blocco di registrazione astrae la funzionalità di registrazione dalla destinazione di registrazione, in modo che il codice applicazione sia coerente, indipendentemente dalla posizione e dal tipo di archivio di registrazione di destinazione.

Per altre informazioni, vedere [5 - Più facile che mai: uso del blocco applicazione di registrazione](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Codice sorgente del metodo IsTransient di EntLib60

La classe **SqlDatabaseTransientErrorDetectionStrategy** include anche il codice sorgente C# per il metodo **IsTransient**. Il codice sorgente chiarisce gli errori considerati temporanei e idonei alla ripetizione dei tentativi, a partire da aprile 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Raccolte di connessioni per database SQL e Server SQL](connect-query-content-reference-guide.md#libraries)
- [Pool di connessioni (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [*Retrying* è una libreria generica Apache 2.0 di ripetizione dei tentativi scritta in Python](https://pypi.python.org/pypi/retrying) per semplificare l'attività di aggiunta del comportamento di ripetizione dei tentativi a qualsiasi codice.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php