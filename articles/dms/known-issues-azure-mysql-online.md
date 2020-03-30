---
title: 'Problemi noti: Migrazioni online al database di Azure per MySQLKnown issues: Online migrations to Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Informazioni sui problemi noti e sulle limitazioni della migrazione con le migrazioni online al database di Azure per MySQL quando si usa il servizio migrazione del database di Azure.Learn about known issues and migration limitations with online migrations to Azure Database for MySQL when using the Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235289"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problemi di migrazione online & limitazioni al database di Azure per MySQL con il servizio di migrazione del database di AzureOnline migration issues to Azure DB for MySQL with Azure Database Migration Service

Le sezioni seguenti illustrano i problemi noti e le limitazioni associati alle migrazioni online da MySQL a Database di Azure per MySQL.

## <a name="online-migration-configuration"></a>Configurazione della migrazione online


- La versione del server MySQL di origine deve essere 5.6.35, 5.7.18 o successive
- Database di Azure per MySQL supporta:
  - Edizione MySQL Community
  - Motore InnoDB
- La migrazione alla stessa versione. La migrazione di MySQL 5.6 a Database di Azure per MySQL 5.7 non è supportata.
- Abilitare la registrazione binaria in my.ini (Windows) o my.cnf (Unix)
  - Impostare Server_id su un numero qualsiasi maggiore o uguale a 1, ad esempio Server_id=1 (solo per MySQL 5.6)
  - Set log-bin \<- percorso> (solo per MySQL 5.6)
  - Impostare binlog_format = row
  - Expire_logs_days = 5 (impostazione consigliata: solo per MySQL 5.6)
- L'utente deve avere il ruolo ReplicationAdmin.
- Le regole di confronto definite per il database MySQL di origine sono le stesse di quelle definite nell'istanza di Database di Azure per MySQL di destinazione.
- Lo schema del database MySQL di origine deve corrispondere a quello dell'istanza di Database di Azure per MySQL di destinazione.
- Lo schema nell'istanza di Database di Azure per MySQL di destinazione non deve avere chiavi esterne. Per rilasciare le chiavi esterne usare la query seguente:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Eseguire drop foreign key, ovvero la seconda colonna, sul risultato della query.
- Lo schema nell'istanza di Database di Azure per MySQL di destinazione non deve avere trigger. Per rilasciare i trigger nel database di destinazione:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitazioni relative ai tipi di dati

- **Limitazione**: se è presente un tipo di dati JSON nel database MySQL di origine, la migrazione ha esito negativo durante la sincronizzazione continua.

    **Soluzione alternativa**: modificare il tipo di dati JSON impostando Medium text o Longtext nel database MySQL di origine.

- **Limitazione**: se non è presente alcuna chiave primaria nelle tabelle, la sincronizzazione continua ha esito negativo.

    **Soluzione alternativa**: impostare temporaneamente una chiave primaria per la tabella per consentire alla migrazione di continuare. Al termine della migrazione dei dati, è possibile rimuovere la chiave primaria.

## <a name="lob-limitations"></a>Limitazioni relative ai tipi di dati LOB

Le colonne LOB (Large Object) sono colonne che possono raggiungere dimensioni elevate. Per MySQL, Testo medio, Longtext, Blob, Mediumblob, Longblob e così via, sono alcuni dei tipi di dati di LOB.

- **Limitazione**: se come chiavi primarie vengono usati tipi di dati LOB, la migrazione ha esito negativo.

    **Soluzione alternativa**: sostituire la chiave primaria con altri tipi di dati o colonne non LOB.

- **Limitazione**: se la lunghezza della colonna LOB è maggiore di 32 kB, è possibile che nella destinazione i dati vengano troncati. È possibile controllare la lunghezza della colonna LOB usando questa query:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Soluzione alternativa:** se si dispone di un oggetto LOB di dimensioni superiori a 32 KB, contattare il team di progettazione Chiedi a [Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Limitazioni durante la migrazione online da AWS RDS MySQL

Quando si tenta di eseguire una migrazione online da AWS RDS MySQL a Azure Database per MySQL, è possibile che si verifichino i seguenti errori.

- **Errore:** Il{0}database ' ha una/e di chiavi esterne nella destinazione. Correggere la destinazione e avviare una nuova attività di migrazione dei dati. Esegui lo script seguente nella destinazione per elencare le chiavi esterne

  **Limitazione:** se nello schema sono presenti chiavi esterne, il caricamento iniziale e la sincronizzazione continua della migrazione avranno esito negativo.
  **Soluzione alternativa:** eseguire lo script seguente in Workbench MySQL per estrarre lo script di chiave esterna e aggiungere uno script di chiave esterna:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Errore:** Database{0}' non esiste nel server. Il server MySQL di origine fornito fa distinzione tra maiuscole e minuscole. Verificare il nome del database.

  **Limitazione:** quando si esegue la migrazione di un database MySQL in Azure tramite l'interfaccia della riga di comando (CLI), gli utenti potrebbero riscontrare questo errore. Il servizio non è riuscito a individuare il database nel server di origine, il che potrebbe essere dovuto al fatto che è stato fornito un nome di database non corretto o che il database non esiste nel server elencato. Notare i nomi dei database sono case-sensitive.

  **Soluzione alternativa:** specificare il nome esatto del database, quindi riprovare.

- **Errore:** Nel database , sono presenti tabelle con lo stesso nome. Database di Azure per MySQL non supporta le tabelle che fanno distinzione tra maiuscole e minuscole.

  **Limitazione:** questo errore si verifica quando nel database di origine sono presenti due tabelle con lo stesso nome. Il database di Azure per MySQL non supporta le tabelle con distinzione tra maiuscole e minuscole.

  **Soluzione alternativa:** aggiornare i nomi delle tabelle in modo che siano univoci, quindi riprovare.

- **Errore:** Il database di destinazione ,database, è vuoto. Eseguire la migrazione dello schema.

  **Limitazione:** questo errore si verifica quando il database di Azure di destinazione per il database MySQL non dispone dello schema richiesto. La migrazione dello schema è necessaria per abilitare la migrazione dei dati nella destinazione.

  **Soluzione alternativa:** [eseguire la migrazione dello schema](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) dal database di origine al database di destinazione.

## <a name="other-limitations"></a>Altre limitazioni

- Le stringhe di password con parentesi graffe di apertura e chiusura {} all'inizio e alla fine non sono supportate. Questa limitazione si applica a entrambe le connessioni, sia al server MySQL di origine sia all'istanza di Database di Azure per MySQL.
- Le DDL seguenti non sono supportate:
  - Tutte le DDL di partizione
  - Eliminare una tabella
  - Rinominare tabella
- L'uso dell'istruzione *alter table <nome_tabella> add column <nome_colonna>* per aggiungere colonne all'inizio o al centro di una tabella non è supportato. L'istruzione *alter table <nome_tabella> add column <nome_colonna>* aggiunge la colonna alla fine della tabella.
- Gli indici creati solo su una parte dei dati della colonna non sono supportati. L'istruzione seguente è un esempio che crea un indice usando solo una parte dei dati della colonna:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Nel servizio Migrazione del database di Azure il limite dei database di cui eseguire la migrazione in una singola attività di migrazione è quattro.

- **Errore:** Dimensioni della riga troppo grandi (> 8126). La modifica di alcune colonne in TESTO o BLOB può essere utile. Nel formato di riga corrente, il prefisso BLOB pari a 0 byte viene archiviato inline.

  **Limitazione:** questo errore si verifica quando si esegue la migrazione al database di Azure per MySQL usando il motore di archiviazione InnoDB e le dimensioni delle righe della tabella sono troppo grandi (>8126 byte).

  **Soluzione alternativa:** aggiornare lo schema della tabella con una dimensione di riga maggiore di 8126 byte. Non è consigliabile modificare la modalità rigorosa perché i dati verranno troncati. La modifica della page_size non è supportata.
