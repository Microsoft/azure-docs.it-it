---
title: Creare utenti-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come è possibile creare nuovi account utente per interagire con un database di Azure per PostgreSQL-singolo server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: 1dbbdde03d1c24882be298d8c81362744debeecf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91704940"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Creare utenti in database di Azure per PostgreSQL-server singolo

Questo articolo descrive come creare utenti in un database di Azure per il server PostgreSQL.

Per informazioni su come creare e gestire gli utenti della sottoscrizione di Azure e i relativi privilegi, vedere l' [articolo controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/built-in-roles.md) o vedere [come personalizzare i ruoli](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Account amministratore del server

Quando si è creato il database di Azure per PostgreSQL, si sono specificati un nome utente amministratore del server e una password. Per altre informazioni, è possibile seguire le istruzioni della [Guida introduttiva](quickstart-create-server-database-portal.md) che illustra un approccio graduale. Poiché il nome utente amministratore del server è un nome personalizzato, è possibile individuare il nome utente amministratore del server scelto dal portale di Azure.

Il database di Azure per il server PostgreSQL viene creato con 3 ruoli predefiniti. Per visualizzare questi ruoli, eseguire il comando: `SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- utente amministratore del server

L'utente amministratore del server è un membro del ruolo azure_pg_admin. L'account amministratore del server non fa tuttavia parte del ruolo azure_superuser. Poiché questo è un servizio PaaS gestito, solo Microsoft fa parte del ruolo utente con privilegi avanzati.

Il motore PostgreSQL usa i privilegi per controllare l'accesso agli oggetti di database, come illustrato nella [documentazione del prodotto PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). In Database di Azure per PostgreSQL all'utente amministratore del server vengono concessi questi privilegi: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

L'account utente amministratore del server può essere usato per creare altri utenti e assegnarli al ruolo azure_pg_admin. L'account amministratore del server può anche essere usato per creare utenti e ruoli con privilegi meno elevati che possono accedere a singoli database e schemi.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Come creare altri utenti amministratore in Database di Azure per PostgreSQL

1. Ottenere le informazioni per la connessione e il nome dell'utente amministratore.
   Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Proprietà** o nella pagina **Panoramica** del server nel portale di Azure.

2. Usare l'account amministratore e la password per connettersi al server di database. Usare lo strumento client preferito, ad esempio pgAdmin o psql.
   Se non si è certi della modalità di connessione, vedere [la guida introduttiva](./quickstart-create-server-database-portal.md)

3. Modificare ed eseguire il codice SQL seguente. Sostituire il nuovo nome utente al valore del segnaposto <new_user> e sostituire la password segnaposto con la propria password complessa. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Come creare utenti di database in Database di Azure per PostgreSQL

1. Ottenere le informazioni per la connessione e il nome dell'utente amministratore.
   Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Proprietà** o nella pagina **Panoramica** del server nel portale di Azure.

2. Usare l'account amministratore e la password per connettersi al server di database. Usare lo strumento client preferito, ad esempio pgAdmin o psql.

3. Modificare ed eseguire il codice SQL seguente. Sostituire il valore segnaposto `<db_user>` con il nuovo nome utente che si intende creare e il valore segnaposto `<newdb>` con il nome del database. Sostituire la password segnaposto con la propria password complessa.

   Questa sintassi di codice SQL crea un nuovo database denominato testdb a scopo di esempio e quindi crea un nuovo utente nel servizio PostgreSQL e concede i privilegi di connessione al nuovo database per tale utente.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Potrebbe essere necessario concedere altri privilegi per proteggere gli oggetti nel database, usando un account amministratore. Per altre informazioni sui ruoli e i privilegi del database, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html). Ad esempio:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Accedere al server, specificando il database designato, con il nuovo nome utente e la nuova password. Questo esempio illustra la riga di comando psql. Con questo comando, viene chiesto di specificare la password per il nome utente. Sostituire i valori segnaposto con il nome del server, il nome del database e il nome utente effettivi.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Passaggi successivi

Aprire il firewall per gli indirizzi IP dei computer dei nuovi utenti per consentire loro di connettersi: [creare e gestire regole del firewall di Database di Azure per PostgreSQL con il portale di Azure](howto-manage-firewall-using-portal.md) o l'[interfaccia della riga di comando di Azure](howto-manage-firewall-using-cli.md).

Per altre informazioni sulla gestione degli account utente, vedere la documentazione di PostgreSQL per [ruoli e privilegi del database](https://www.postgresql.org/docs/current/static/user-manag.html),[sintassi di GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) e [privilegi](https://www.postgresql.org/docs/current/static/ddl-priv.html).
