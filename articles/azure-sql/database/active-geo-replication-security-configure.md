---
title: Configurare la sicurezza per il ripristino di emergenza
description: Informazioni sulle considerazioni sulla configurazione e la gestione della sicurezza dopo il ripristino di un database o il failover in un server secondario.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 92a0c7fd3733b5e27c34c6fd0fe157bfb466a0fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444884"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Configurare e gestire la sicurezza dei database SQL di Azure per il ripristino geografico o il failover
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo descrive i requisiti di autenticazione per configurare e controllare [replica geografica attiva](active-geo-replication-overview.md) e [gruppi di failover automatico](auto-failover-group-overview.md). Fornisce inoltre i passaggi necessari per configurare l'accesso utente al database secondario. Infine, descrive anche come abilitare l'accesso al database ripristinato dopo il [ripristino geografico](recovery-using-backups.md#geo-restore). Per altre informazioni sulle opzioni di ripristino, vedere [Continuità aziendale del database SQL di Azure](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="disaster-recovery-with-contained-users"></a>Ripristino di emergenza con gli utenti indipendenti

A differenza degli utenti tradizionali per i quali deve essere eseguito il mapping agli account di accesso nel database master, un utente indipendente viene gestito completamente dal database stesso. Questo approccio presenta due vantaggi. Nello scenario di ripristino di emergenza, gli utenti possono continuare a connettersi al nuovo database primario o al database ripristinato con il ripristino geografico senza alcuna configurazione aggiuntiva, perché il database gestisce gli utenti. Dal punto di vista dell'accesso, questa configurazione offre anche vantaggi a livello di scalabilità e prestazioni. Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).

Il compromesso principale è rappresentato dal fatto che il processo di ripristino di emergenza su vasta scala è più complesso da gestire. Quando si dispone di più database che usano lo stesso account di accesso, la gestione delle credenziali usate dagli utenti indipendenti in più database può annullare il vantaggio rappresentato dagli utenti indipendenti. Ad esempio, i criteri di rotazione delle password richiedono l'applicazione coerente delle modifiche in più database anziché la modifica singola della password per l'accesso nel database master. Per questo motivo l'uso degli utenti indipendenti non è consigliato in presenza di molti database che usano lo stesso nome utente e la stessa password.

## <a name="how-to-configure-logins-and-users"></a>Come configurare gli account di accesso e gli utenti

Se si usano account di accesso e utenti tradizionali, invece di utenti indipendenti, è necessario eseguire altri passaggi per assicurare che nel database master siano presenti gli stessi account di accesso. Le sezioni seguenti illustrano i passaggi da eseguire, oltre a considerazioni aggiuntive.

  >[!NOTE]
  > È anche possibile usare gli account di accesso di Azure Active Directory (AAD) per gestire i database. Per altre informazioni, vedere [Accessi e utenti di SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Impostare l'accesso utente a un database secondario o ripristinato

Per fare in modo che il database secondario sia utilizzabile come database secondario di sola lettura e per garantire l'accesso appropriato al nuovo database primario o al database ripristinato con il ripristino geografico, è necessario che nel database master del server di destinazione sia presente la configurazione di sicurezza appropriata prima del ripristino.

Le autorizzazioni specifiche per ogni passaggio sono descritte più avanti in questo argomento.

La preparazione dell'accesso utente a un database secondario con replica geografica deve essere eseguita durante la configurazione della replica geografica. La preparazione dell'accesso utente ai database ripristinati con il ripristino geografico deve essere eseguita in qualsiasi momento quando il server originale è online, ad esempio nel contesto dell'analisi del ripristino di emergenza.

> [!NOTE]
> Se si esegue il failover o il ripristino geografico in un server che non presenta account di accesso configurati adeguatamente, l'accesso sarà limitato all'account amministratore del server.

La configurazione degli account di accesso nel server di destinazione prevede i tre passaggi seguenti:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. determinare gli account di accesso con accesso al database primario

Il primo passaggio della procedura consiste nel determinare quali account di accesso devono essere duplicati nel server di destinazione. A questo scopo, è necessario usare una coppia di istruzioni SELECT, una nel database master logico nel server di origine e una nel database primario stesso.

Solo l'amministratore del server o un membro del ruolo del server **LoginManager** può determinare gli account di accesso nel server di origine con l'istruzione SELECT seguente.

```sql
SELECT [name], [sid]
FROM [sys].[sql_logins]
WHERE [type_desc] = 'SQL_Login'
```

Solo un membro del ruolo del database db_owner, l'utente dbo o l'amministratore del server può determinare tutte le entità utente nel database primario.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. trovare il SID per gli account di accesso identificati nel passaggio 1

Confrontando l'output delle query illustrate nella sezione precedente e trovando una corrispondenza per i SID, è possibile eseguire il mapping dell'account di accesso del server all'utente del database. Gli account di accesso che hanno un utente del database con un SID corrispondente hanno anche l'accesso utente a quel database come entità utente.

La query seguente può essere usata per visualizzare tutte le entità utente e i relativi SID in un database. Questa query può essere eseguita solo da un membro del ruolo del database db_owner o dall'amministratore del server.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

> [!NOTE]
> Gli utenti **INFORMATION_SCHEMA** e **sys** avranno SID *NULL*, mentre il SID **guest** è **0x00**. Il SID **dbo** può iniziare con *0x01060000000001648000000000048454* se il database è stato creato dall'amministratore del server invece che da un membro di **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. creare gli account di accesso nel server di destinazione

L'ultimo passaggio consiste nel generare gli account di accesso con i SID appropriati nel server o nei server di destinazione. La sintassi di base è la seguente.

```sql
CREATE LOGIN [<login name>]
WITH PASSWORD = <login password>,
SID = <desired login SID>
```

> [!NOTE]
> Se si vuole concedere l'accesso utente al database secondario, ma non al database primario, è possibile modificare l'account di accesso utente nel server primario usando la sintassi seguente.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> DISABLE non modifica la password, pertanto è sempre possibile abilitare l'accesso, se necessario.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla gestione dell'accesso al database e degli account di accesso, vedere [Protezione del database SQL: gestire l'accesso al database e la sicurezza degli account di accesso](logins-create-manage.md).
* Per altre informazioni sugli utenti di database indipendente, vedere [Utenti di database indipendente: rendere portabile un database](https://msdn.microsoft.com/library/ff929188.aspx).
* Per informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](active-geo-replication-overview.md).
* Per informazioni sui gruppi di failover automatico, vedere [gruppi di failover automatico](auto-failover-group-overview.md).
* Per informazioni sull'uso del ripristino geografico, vedere l'argomento sul [ripristino geografico](recovery-using-backups.md#geo-restore)
