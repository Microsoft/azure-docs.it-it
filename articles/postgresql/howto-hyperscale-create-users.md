---
title: Creare utenti-iperscalabilità (CITUS)-database di Azure per PostgreSQL
description: Questo articolo descrive come creare nuovi account utente per interagire con un database di Azure per PostgreSQL-overscale (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 3d23ee6119b625e11ce44bb9ad11ce4b3ee0280d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91295737"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Creare utenti in database di Azure per PostgreSQL-iperscalabilità (CITUS)

> [!NOTE]
> Il termine "Users" si riferisce agli utenti all'interno di un gruppo di server con iperscalabilità (CITUS). Per informazioni sugli utenti della sottoscrizione di Azure e sui relativi privilegi, vedere l' [articolo controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/built-in-roles.md) o vedere [come personalizzare i ruoli](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Account amministratore del server

Il motore PostgreSQL usa i [ruoli](https://www.postgresql.org/docs/current/sql-createrole.html) per controllare l'accesso agli oggetti di database e un gruppo di server CITUS (iperscale) appena creato presenta diversi ruoli predefiniti:

* [Ruoli di PostgreSQL predefiniti](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Poiché iperscale (CITUS) è un servizio PaaS gestito, solo Microsoft può accedere con il `postgres` ruolo utente con privilegi avanzati. Per un accesso amministrativo limitato, iperscale (CITUS) fornisce il `citus` ruolo.

Autorizzazioni per il `citus` ruolo:

* Leggere tutte le variabili di configurazione, anche le variabili normalmente visibili solo agli utenti con superuser.
* Leggi tutte le \_ visualizzazioni PG stat \_ \* e usa varie estensioni correlate alle statistiche, anche viste o estensioni normalmente visibili solo agli utenti con superuser.
* Eseguire funzioni di monitoraggio che potrebbero richiedere l'accesso a blocchi di condivisione sulle tabelle, potenzialmente per molto tempo.
* [Creare estensioni PostgreSQL](concepts-hyperscale-extensions.md) , perché il ruolo è un membro di `azure_pg_admin` .

In particolare, il `citus` ruolo presenta alcune restrizioni:

* Non è possibile creare ruoli
* Non è possibile creare database

## <a name="how-to-create-additional-user-roles"></a>Come creare ruoli utente aggiuntivi

Come indicato in precedenza, l' `citus` account amministratore non dispone dell'autorizzazione per la creazione di altri utenti. Per aggiungere un utente, utilizzare l'interfaccia portale di Azure.

1. Passare alla pagina **ruoli** per il gruppo di server iperscalare (CITUS) e fare clic su **+ Aggiungi**:

   :::image type="content" source="media/howto-hyperscale-create-users/1-role-page.png" alt-text="Pagina ruoli":::

2. Immettere il nome e la password del ruolo. Fare clic su **Salva**.

   :::image type="content" source="media/howto-hyperscale-create-users/2-add-user-fields.png" alt-text="Aggiungi ruolo":::

L'utente verrà creato nel nodo coordinatore del gruppo di server e propagato a tutti i nodi del ruolo di lavoro. I ruoli creati tramite il portale di Azure hanno l' `LOGIN` attributo, che indica che si tratta di utenti veri che possono accedere al database.

## <a name="how-to-modify-privileges-for-user-role"></a>Come modificare i privilegi per il ruolo utente

I nuovi ruoli utente vengono comunemente usati per fornire l'accesso al database con privilegi limitati. Per modificare i privilegi utente, usare i comandi standard di PostgreSQL, usando uno strumento come PgAdmin o PSQL. Vedere la pagina relativa alla [connessione con PSQL](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) nella Guida introduttiva a scalabilità (CITUS).

Ad esempio, per consentire `db_user` a di leggere `mytable` , concedere l'autorizzazione:

```sql
GRANT SELECT ON mytable TO db_user;
```

Iperscale (CITUS) propaga le istruzioni GRANT a tabella singola attraverso l'intero cluster, applicando tali istruzioni in tutti i nodi del ruolo di lavoro. Propaga inoltre le concessioni a livello di sistema (ad esempio per tutte le tabelle in uno schema):

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Come eliminare un ruolo utente o modificare la password

Per aggiornare un utente, visitare la pagina **ruoli** per il gruppo di server iperscalabile (CITUS) e fare clic sui puntini di sospensione **...** accanto all'utente. I puntini di sospensione aprono un menu per eliminare l'utente o reimpostare la password.

   :::image type="content" source="media/howto-hyperscale-create-users/edit-role.png" alt-text="Modificare un ruolo":::

Il `citus` ruolo è con privilegi e non può essere eliminato.

## <a name="next-steps"></a>Passaggi successivi

Aprire il firewall per gli indirizzi IP dei computer dei nuovi utenti per consentire loro di connettersi: [creare e gestire regole del firewall di iperscala (CITUS) usando il portale di Azure](howto-hyperscale-manage-firewall-using-portal.md).

Per ulteriori informazioni sulla gestione degli account utente del database, vedere la documentazione del prodotto PostgreSQL:

* [Privilegi e ruoli del database](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Sintassi GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilegi](https://www.postgresql.org/docs/current/static/ddl-priv.html)
