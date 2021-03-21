---
title: Dump e ripristino-database di Azure per PostgreSQL-server singolo
description: Viene descritto come estrarre un database PostgreSQL in un file dump ed eseguire il ripristino da un file creato da pg_dump nel database di Azure per PostgreSQL-server singolo.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 16166183b56b371fe8338894f83dbacf2e659c53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563556"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Eseguire la migrazione del database PostgreSQL usando dump e ripristino
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

È possibile usare [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) per estrarre un database PostgreSQL in un file di dump e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) per ripristinare il database PostgreSQL da un file di archivio creato da pg_dump.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un [server di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md) con le regole del firewall per consentire l'accesso e il database sottostante.
- Utilità della riga di comando [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) installate

Seguire questi passaggi per eseguire il dump e il ripristino del database PostgreSQL:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Creare un file di dump usando pg_dump che contiene i dati da caricare
Per eseguire il backup di un database PostgreSQL in locale o in una macchina virtuale, eseguire questo comando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Se ad esempio è presente un server locale che contiene un database denominato **testdb**, eseguire:
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Ripristinare i dati nel database di Azure di destinazione per PostgreSQL usando pg_restore
Dopo avere creato il database di destinazione, è possibile usare il comando pg_restore e il parametro -d, --dbname per ripristinare i dati nel database di destinazione dal file di dump.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

Se si include il parametro --no-owner, tutti gli oggetti creati durante il ripristino saranno di proprietà dell'utente specificato con --username. Per altre informazioni, vedere la documentazione ufficiale di PostgreSQL per [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Se il server PostgreSQL richiede connessioni TLS/SSL (per impostazione predefinita nel database di Azure per i server PostgreSQL), impostare una variabile di ambiente in `PGSSLMODE=require` modo che lo strumento di pg_restore si connetta con TLS. Senza TLS, l'errore può essere letto  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Nella riga di comando di Windows, eseguire il comando `SET PGSSLMODE=require` prima di eseguire il comando pg_restore. Nella riga di comando di Linux o Bash, eseguire il comando `export PGSSLMODE=require` prima di eseguire il comando pg_restore.
>

In questo esempio i dati vengono ripristinati dal file di dump **testdb.dump** nel database **mypgsqldb** disponibile sul server di destinazione **mydemoserver.postgres.database.azure.com**.

Ecco un esempio di come usare questa **pg_restore** per un **singolo server**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```
Di seguito è riportato un esempio di come usare questa **pg_restore** per **server flessibili**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```
---

## <a name="optimizing-the-migration-process"></a>Ottimizzazione del processo di migrazione

Un modo per eseguire la migrazione di un database PostgreSQL esistente al servizio Database di Azure per PostgreSQL è effettuare il backup del database nell'origine e ripristinarlo in Azure. Per ridurre al minimo il tempo necessario per completare la migrazione, è consigliabile usare i parametri seguenti con i comandi di backup e ripristino.

> [!NOTE]
> Per informazioni dettagliate sulla sintassi, vedere gli articoli [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Per il backup
- Eseguire il backup con lo switch -Fc in modo che sia possibile eseguire il ripristino in parallelo per renderlo più rapido. Ad esempio:

    ```bash
    pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
    ```

### <a name="for-the-restore"></a>Per il ripristino
- Si consiglia di spostare il file di backup in una macchina virtuale di Azure nella stessa area del server del Database di Azure per PostgreSQL in cui si sta eseguendo la migrazione e di eseguire il pg_restore da tale VM per ridurre la latenza di rete. Si consiglia inoltre di creare la macchina virtuale con [la funzionalità rete accelerata](../virtual-network/create-vm-accelerated-networking-powershell.md) abilitata.

- Deve essere già eseguito per impostazione predefinita, ma aprire il file di dump per verificare che le istruzioni CREATE INDEX siano effettuate dopo l'inserimento dei dati. In caso contrario, spostare le istruzioni CREATE INDEX dopo aver inserito i dati.

- Ripristinare con le opzioni-FC e-j *#* per parallelizzare il ripristino. *#* è il numero di core nel server di destinazione. È anche possibile provare con *#* impostato su due volte il numero di core del server di destinazione per verificarne l'effetto. Ad esempio:

Ecco un esempio di come usare questa **pg_restore** per un **singolo server**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
```
Di seguito è riportato un esempio di come usare questa **pg_restore** per **server flessibili**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
 ```

- È anche possibile modificare il file di dump aggiungendo il comando *set synchronous_commit = off;* all'inizio e il comando *set synchronous_commit = on;* alla fine. Non accenderlo alla fine, prima che le applicazioni cambino i dati è possibile che si verifichi una successiva perdita di dati.

- Nel server di Database di Azure per PostgreSQL di destinazione è consigliabile eseguire le seguenti operazioni prima del ripristino:
    - Disattivare il rilevamento delle prestazioni delle query, poiché queste statistiche non sono necessarie durante la migrazione. È possibile eseguire questa operazione impostando pg_stat_statements.track, pg_qs.query_capture_mode e pgms_wait_sampling.query_capture_mode su NONE.

    - Usare uno SKU con prestazioni di calcolo e memoria elevate, ad esempio 32 vCore ottimizzati per la memoria, per completare la migrazione più velocemente. Sarà poi possibile tornare allo SKU preferito al termine della migrazione. Con uno SKU più potente, si può ottenere maggior parallelismo aumentando il parametro `-j` corrispondente nel comando pg_restore.

    - Un numero maggiore di operazioni di I/O al secondo nel server di destinazione può migliorare le prestazioni di ripristino. È possibile effettuare il provisioning di più operazioni di I/O al secondo aumentando le dimensioni delle risorse di archiviazione del server. Questa impostazione non è reversibile, ma è opportuno valutare se un aumento delle operazioni di I/O al secondo può contribuire a migliorare il carico di lavoro effettivo.

Ricordarsi di testare e validare questi comandi in un ambiente di test prima di usarli in produzione.

## <a name="next-steps"></a>Passaggi successivi
- Per eseguire la migrazione di un database PostgreSQL tramite esportazione e importazione, vedere [Eseguire la migrazione del database PostgreSQL usando le funzionalità di esportazione e importazione](howto-migrate-using-export-and-import.md).
- Per altre informazioni sulla migrazione dei database in Database di Azure per PostgreSQL, vedere [Database Migration Guide](https://aka.ms/datamigration) (Guida alla migrazione di database).
