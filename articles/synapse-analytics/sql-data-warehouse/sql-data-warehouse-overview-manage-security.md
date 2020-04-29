---
title: Proteggere un database
description: Suggerimenti per la protezione di un database e lo sviluppo di soluzioni in una risorsa del pool SQL sinapsi.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 27d3a242d91a79ea00974748f4a8b5460d2dd247
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416063"
---
# <a name="secure-a-database-in-azure-synapse"></a>Proteggere un database in una sinapsi di Azure

> [!div class="op_single_selector"]
>
> * [Panoramica della sicurezza](sql-data-warehouse-overview-manage-security.md)
> * [autenticazione](sql-data-warehouse-authentication.md)
> * [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
> * [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)

Questo articolo illustra le nozioni di base per la protezione del pool SQL sinapsi. In particolare, questo articolo consente di iniziare a usare le risorse per limitare l'accesso, proteggere i dati e monitorare le attività in un database di cui viene effettuato il provisioning tramite il pool SQL.

## <a name="connection-security"></a>Sicurezza delle connessioni

La sicurezza delle connessioni fa riferimento al modo che si limitano e proteggono le connessioni al database mediante regole del firewall e crittografia di connessione.

Le regole del firewall vengono usate sia dal server che dal database per rifiutare i tentativi di connessione da indirizzi IP che non sono stati inseriti in modo esplicito nell'elenco elementi consentiti. Prima di consentire le connessioni dall'applicazione o dall'indirizzo IP pubblico del computer client, è necessario creare una regola del firewall di livello server tramite il portale di Azure, l'API REST o PowerShell.

Come procedura consigliata, si suggerisce di limitare gli intervalli di indirizzi IP consentiti attraverso il firewall del server quanto più possibile.  Per accedere al pool SQL dal computer locale, verificare che il firewall in rete e nel computer locale consenta le comunicazioni in uscita sulla porta TCP 1433.  

Azure sinapsi Analytics usa le regole del firewall IP a livello di server. Non supporta le regole del firewall IP a livello di database. Per altre informazioni, vedere [regole del firewall del database SQL di Azure](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Le connessioni al pool SQL sono crittografate per impostazione predefinita.  La modifica delle impostazioni di connessione per disabilitare la crittografia viene ignorata.

## <a name="authentication"></a>Autenticazione

Per autenticazione si intende il modo in cui viene dimostrata la propria identità durante la connessione al database. Il pool SQL supporta attualmente l'autenticazione SQL Server con un nome utente e una password e con Azure Active Directory.

Durante la creazione del server logico per il database, è stato specificato un account di accesso "amministratore del server" con un nome utente e una password. Con queste credenziali è possibile eseguire l'autenticazione in qualsiasi database di tale server come proprietario del database, o "dbo", tramite l'autenticazione di SQL Server.

Tuttavia, come procedura consigliata, è consigliabile che gli utenti dell'organizzazione usino un account diverso per l'autenticazione. Questo modo può possibile limitare le autorizzazioni concesse all'applicazione e ridurre i rischi di attività dannose nel caso in cui il codice dell'applicazione sia vulnerabile a un attacco SQL injection.

Per creare un utente autenticato in SQL Server, connettersi al database **master** nel proprio server con l'account di accesso amministratore e creare un nuovo accesso al server.  È consigliabile creare anche un utente nel database master. La creazione di un utente nel database master consente all'utente di accedere tramite strumenti come SSMS senza specificare un nome di database.  Consente inoltre di usare Esplora oggetti per visualizzare tutti i database in SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Connettersi quindi al database del **pool SQL** con l'account di accesso amministratore del server e creare un utente del database in base all'account di accesso al server creato.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Per concedere a un utente l'autorizzazione a eseguire altre operazioni, ad esempio la creazione di account di accesso o di nuovi database, assegnare all'utente stesso anche i ruoli `Loginmanager` e `dbmanager` nel database master.

Per altre informazioni sui questi ruoli aggiuntivi e sull'autenticazione a un database SQL, vedere [Gestione di database e account di accesso nel database SQL di Azure](../../sql-database/sql-database-manage-logins.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Per ulteriori informazioni sulla connessione tramite Azure Active Directory, vedere [connessione mediante l'autenticazione di Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorizzazione

L'autorizzazione si riferisce alle operazioni che è possibile eseguire all'interno di un database dopo l'autenticazione e la connessione. I privilegi di autorizzazione sono determinati dalle appartenenze ai ruoli e dalle autorizzazioni. Come procedura consigliata, è opportuno concedere agli utenti i privilegi minimi necessari. Per gestire i ruoli, è possibile usare le stored procedure seguenti:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

L'account di amministrazione del server a cui ci si sta connettendo è un membro del ruolo db_owner, che è autorizzato a eseguire qualsiasi operazione all'interno del database. Salvare questo account per la distribuzione degli aggiornamenti allo schema e altre operazioni di gestione. Utilizzare l'account "ApplicationUser" con autorizzazioni più limitate per la connessione dall'applicazione al database con i privilegi minimi richiesti dall'applicazione.

Esistono modi per limitare ulteriormente le operazioni che un utente può eseguire nel database:

* Le [autorizzazioni](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) granulari consentono di controllare le operazioni possibili per colonne, tabelle, viste e procedure singole, nonché per altri oggetti nel database. Usare le autorizzazioni granulari per avere il massimo controllo e concedere solo le autorizzazioni necessarie.
* È possibile usare [ruoli del database](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) diversi da db_datareader e db_datawriter per creare account utente dell'applicazione più potenti o account di gestione meno potenti. I ruoli del database predefiniti incorporati sono una soluzione semplice per la concessione di autorizzazioni, ma possono concedere più autorizzazioni di quelle necessarie.
* È possibile usare [stored procedure](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per limitare le operazioni che possono essere eseguite nel database.

L'esempio seguente concede l'accesso in lettura a uno schema definito dall'utente.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

La gestione di database e server logici dal portale di Azure o tramite l'API di Azure Resource Manager viene controllata dalle assegnazioni di ruolo degli account degli utenti del portale. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Crittografia

Transparent Data Encryption (Transparent Data Encryption) consente di proteggersi dalla minaccia di attività dannose mediante la crittografia e la decrittografia dei dati inattivi. Quando si crittografa il database, i file di log delle transazioni e i backup associati vengono crittografati senza bisogno di apportare modifiche alle applicazioni. TDE esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database".

Nel database SQL la chiave di crittografia del database è protetta da un certificato del server incorporato. Il certificato server incorporato è univoco per ogni server di database SQL. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni. L'algoritmo di crittografia usato è AES-256. Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

È possibile crittografare il database usando il [portale di Azure](sql-data-warehouse-encryption-tde.md) o [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate ed esempi sulla connessione al warehouse con protocolli diversi, vedere [connettersi al pool SQL](../sql/connect-overview.md).
