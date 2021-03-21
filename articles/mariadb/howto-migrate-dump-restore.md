---
title: Eseguire la migrazione con dump e ripristino-database di Azure per MariaDB
description: Questo articolo illustra due modi comuni per eseguire il backup e il ripristino dei database nel database di Azure per MariaDB usando strumenti come mysqldump, MySQL Workbench e PHPMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 8678304e72f11c486911ff4de00633224e878147
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564474"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Eseguire la migrazione del database MariaDB a Database di Azure per MariaDB tramite dump e ripristino
Questo articolo illustra due modi comuni per eseguire il backup e il ripristino dei database nel database di Azure per MariaDB
- Dump e ripristino dalla riga di comando( tramite mysqldump) 
- Dump e ripristino con PHPMyAdmin

## <a name="before-you-begin"></a>Prima di iniziare
Per proseguire con questa guida è necessario:
- [Creare un database di Azure per il server MariaDB - portale di Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- Avere installato su un computer l'utilità della riga di comando [mysqldump](https://mariadb.com/kb/en/library/mysqldump/).
- MySQL Workbench [Download di MySQL Workbench](https://dev.mysql.com/downloads/workbench/) o di un altro strumento MySQL di terze parti per i comandi di dump e ripristino.

## <a name="use-common-tools"></a>Usare strumenti comuni
Usare utilità e strumenti comuni, come MySQL Workbench o mysqldump, per connettersi in modalità remota e ripristinare i dati nel database di Azure per MariaDB. Usare tali strumenti sul computer client con una connessione internet per connettersi al database di Azure per MariaDB. Usare una connessione SSL crittografata per le procedure di sicurezza consigliate. Vedere anche [Configure SSL connectivity in Azure Database for MariaDB](concepts-ssl-connection-security.md) (Configurare la connettività SSL nel database di Azure per MariaDB). Durante la migrazione al database di Azure per MariaDB non è necessario spostare i file di dump in alcun percorso cloud speciale. 

## <a name="common-uses-for-dump-and-restore"></a>Usi comuni per il dump e ripristino
È possibile usare le utilità di MySQL come mysqldump e mysqlpump per il dump e il caricamento di database in un database di Azure per MariaDB in diversi scenari comuni. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Usare i dump del database quando si esegue la migrazione dell'intero database. Questa indicazione è utile quando si sposta una grande quantità di dati o quando si vuole ridurre al minimo l'interruzione del servizio per applicazioni o siti live. 
-  Verificare che tutte le tabelle nel database usino il motore di archiviazione InnoDB quando si caricano dati nel database di Azure per MariaDB. Il database di Azure per MariaDB supporta solo il motore di archiviazione InnoDB e pertanto non sono supportati motori di archiviazione alternativi. Se le tabelle sono configurate con motori di archiviazione alternativi, convertirli nel formato di motore InnoDB prima della migrazione al database di Azure per MariaDB.
   Se ad esempio si possiede WordPress o WebApp con le tabelle MyISAM, convertire le tabelle eseguendo la migrazione nel formato InnoDB prima del ripristino al database di Azure per MariaDB. Usare la clausola `ENGINE=InnoDB` per impostare il motore usato per la creazione di una nuova tabella e quindi trasferire i dati nella tabella compatibile prima del ripristino. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Per evitare eventuali problemi di compatibilità, verificare che venga usata la stessa versione di MariaDB nei sistemi di origine e di destinazione durante il dump dei database. Ad esempio, se il server MariaDB esistente è versione 10.2, è necessario eseguire la migrazione al database di Azure per MariaDB configurato per l'esecuzione della versione 10.2. Il comando `mysql_upgrade` non funziona in un database di Azure per il server MariaDB e non è supportato. Se è necessario eseguire l'aggiornamento in tutte le versioni di MariaDB, eseguire prima il dump o esportare il database di una versione inferiore in una versione successiva di MariaDB nel proprio ambiente. Eseguire quindi `mysql_upgrade` prima di tentare la migrazione in un database di Azure per MariaDB.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni
Per ottimizzare le prestazioni, tenere presenti le considerazioni seguenti durante il dump di database di grandi dimensioni:
-   Usare l'opzione `exclude-triggers` in mysqldump durante il dump dei database. Escludere i trigger dai file di dump per evitare l'attivazione dei comandi di trigger durante il ripristino dei dati. 
-   Usare l'opzione `single-transaction` per impostare la modalità di isolamento della transazione su REPEATABLE READ e inviare un'istruzione SQL START TRANSACTION al server prima di creare un dump dei dati. Il dump di molte tabelle all'interno di una singola transazione causa l'uso di spazio di archiviazione aggiuntivo durante il ripristino. Le opzioni `single-transaction` e `lock-tables` si escludono a vicenda in quanto LOCK TABLES causa il commit implicito di eventuali transazioni in sospeso. Per eseguire il dump di tabelle di grandi dimensioni, combinare l'opzione `single-transaction` con l'opzione `quick`. 
-   Usare la sintassi a più righe `extended-insert` che include vari elenchi VALUES. Ciò consente di ottenere un file di dump più piccolo e di velocizzare gli inserimenti quando il file viene ricaricato.
-  Usare l'opzione `order-by-primary` in mysqldump durante il dump dei database, in modo che i dati vengano inseriti nello script nell'ordine delle chiavi primarie.
-   Usare l'opzione `disable-keys` in mysqldump durante il dump dei dati, per disabilitare i vincoli della chiave esterna prima del caricamento. La disabilitazione dei controlli della chiave esterna offre miglioramenti delle prestazioni. Abilitare i vincoli e verificare i dati dopo il caricamento per garantire l'integrità referenziale.
-   Usare le tabelle partizionate quando appropriato.
-   Caricare i dati in parallelo. Evitare un eccessivo parallelismo che comporterebbe il raggiungimento del limite di risorse e monitorare le risorse con le metriche offerta nel portale di Azure. 
-   Usare l'opzione `defer-table-indexes` in mysqlpump durante il dump dei database, in modo che la creazione dell'indice venga eseguita dopo il caricamento dei dati delle tabelle.
-   Copiare i file di backup in un archivio/BLOB di Azure ed eseguire il ripristino da tale posizione. In questo modo, l'operazione dovrebbe essere eseguita in modo molto più veloce rispetto al ripristino attraverso Internet.

## <a name="create-a-backup-file"></a>Creare un file di backup
Per eseguire il backup di un database MariaDB esistente nel server locale o in una macchina virtuale, eseguire il comando seguente tramite mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

I parametri da specificare sono:
- [uname] Username del database 
- [pass] Password del database (si noti che non è presente alcuno spazio vuoto tra -p e la password) 
- [dbname] Nome del database 
- [backupfile.sql] Filename per il backup del database 
- [-opt] Opzione mysqldump 

Ad esempio, per eseguire il backup di un database denominato "testdb" nel proprio server MariaDB con il nome utente "testuser" e senza password in un file testdb_backup.sql, usare il comando seguente. Il comando esegue il backup del database `testdb` in un file denominato `testdb_backup.sql`, che contiene tutte le istruzioni SQL necessarie per ricreare il database. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Per selezionare tabelle specifiche nel database di cui si deve eseguire il backup, elencare i nomi delle tabelle separati da spazi. Ad esempio, per eseguire il backup delle sole tabelle table1 e table2 in "testdb", seguire questo esempio: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Per eseguire il backup di più database contemporaneamente, usare lo switch -database ed elencare i nomi dei database separati da spazi. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Creare un database sul server di destinazione
Creare un database vuoto nel database di Azure per il server MariaDB di destinazione in cui si vuole eseguire la migrazione dei dati. Usare uno strumento come MySQL Workbench per creare il database. Il database può avere lo stesso nome del database che contiene i dati di dump; in alternativa, è possibile creare un database con un nome diverso.

Per la connessione, individuare le informazioni di connessione nella pagina **Panoramica** del database di Azure per MariaDB.

![Trovare le informazioni di connessione nel portale di Azure](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Aggiungere le informazioni di connessione in MySQL Workbench.

![Stringa di connessione MySQL Workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Ripristinare il database di MariaDB
Dopo avere creato il database di destinazione è possibile usare il comando mysql o MySQL Workbench per ripristinare i dati nel database appena creato specificatamente dal file di dump.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
In questo esempio, ripristinare i dati nel database appena creato nel database di Azure per il server MariaDB di destinazione.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Esportazione mediante PHPMyAdmin
Per l'esportazione è possibile usare lo strumento comune phpMyAdmin, che potrebbe essere già installato in locale nel proprio ambiente. Per esportare il database MariaDB mediante PHPMyAdmin:
1. Aprire phpMyAdmin.
2. Selezionare il database. Fare clic sul nome del database nell'elenco a sinistra. 
3. Fare clic sul collegamento **Export** (Esporta). Viene visualizzata una nuova pagina per eseguire il dump del database.
4. Nell'area Export (Esporta) fare clic sul collegamento **Select All** (Seleziona tutto) per scegliere le tabelle nel database. 
5. Nell'area delle opzioni SQL, fare clic sulle opzioni appropriate. 
6. Fare clic sull'opzione **Save as file** (Salva come file) e sull'opzione di compressione corrispondente e quindi fare clic sul pulsante **Go** (Vai). Verrà visualizzata una finestra di dialogo che richiede di salvare il file in locale.

## <a name="import-using-phpmyadmin"></a>Importazione mediante PHPMyAdmin
L'importazione del database è simile all'esportazione. Procedere come segue:
1. Aprire phpMyAdmin. 
2. Nella pagina di impostazione di phpMyAdmin fare clic su **Add** (Aggiungi) per aggiungere il database di Azure per il server MariaDB. Specificare i dettagli della connessione e le informazioni di accesso.
3. Creare un database denominato in modo appropriato e selezionarlo a sinistra della schermata. Per riscrivere il database esistente, fare clic sul nome del database, selezionare tutte le caselle di controllo accanto ai nomi delle tabelle e selezionare **Drop** (Elimina) per eliminare le tabelle esistenti. 
4. Fare clic sul collegamento **SQL** per visualizzare la pagina in cui è possibile digitare i comandi SQL o caricare il file SQL. 
5. Usare il pulsante **Browse** (Sfoglia) per trovare il file nel database. 
6. Fare clic sul pulsante **Go** (Vai) per esportare il backup, eseguire i comandi SQL e ricreare il database.

## <a name="next-steps"></a>Passaggi successivi
- [Connettere le applicazioni a Database di Azure per MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
