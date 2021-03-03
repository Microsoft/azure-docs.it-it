---
title: Configurare la replica dei dati in database di Azure per MySQL
description: Questo articolo descrive come configurare la replica dei dati in ingresso in Database di Azure per MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 29ac0c5991964de48cedd15622d15e929bc9d733
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709547"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Come configurare la replica dei dati in ingresso in Database di Azure per MySQL

Questo articolo descrive come configurare [replica dei dati in ingresso](concepts-data-in-replication.md) in database di Azure per MySQL tramite la configurazione dei server di origine e di replica. Questo articolo presuppone che l'utente disponga di un'esperienza precedente con i server e i database MySQL.

> [!NOTE]
> Questo articolo contiene riferimenti al termine _slave_, un termine che Microsoft non usa più. Quando il termine verrà rimosso dal software, verrà rimosso anche dall'articolo.
>

Per creare una replica nel servizio database di Azure per MySQL, [replica dei dati in ingresso](concepts-data-in-replication.md)  sincronizza i dati da un server MySQL di origine locale, in macchine virtuali (VM) o in servizi di database cloud. La replica dei dati in ingresso si basa sulla replica nativa di MySQL in base alla posizione di file di log binari (binlog). Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL).

Esaminare le [limitazioni e i requisiti](concepts-data-in-replication.md#limitations-and-considerations) di replica dei dati prima di eseguire i passaggi descritti in questo articolo.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Creare un server MySQL da usare come replica

1. Creare un server di Database di Azure per MySQL

   Creare un server MySQL, ad esempio "replica.mysql.database.azure.com". Per istruzioni su come creare il server, vedere [Creare un server di Database di Azure per MySQL usando il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md). Questo è il server di "replica" per la replica dei dati in ingresso.

   > [!IMPORTANT]
   > Il server di Database di Azure per MySQL deve essere creato con il piano tariffario per utilizzo generico o ottimizzato per la memoria.
   >

2. Creare gli stessi account utente e i privilegi corrispondenti

   Gli account utente non vengono replicati dal server di origine al server di replica. Se si prevede di fornire agli utenti l'accesso al server di replica, è necessario creare manualmente tutti gli account e i privilegi corrispondenti nel database di Azure per il server MySQL appena creato.

3. Aggiungere l'indirizzo IP del server di origine alle regole del firewall della replica.

   Aggiornare le regole firewall usando il [portale di Azure](howto-manage-firewall-using-portal.md) o l'[interfaccia della riga di comando di Azure](howto-manage-firewall-using-cli.md).

## <a name="configure-the-source-server"></a>Configurare il server di origine

I passaggi seguenti consentono di preparare e configurare il server MySQL ospitato in locale, in una macchina virtuale o un servizio di database ospitato da altri provider di cloud per la replica dei dati in ingresso. Questo server è l'"origine" nella replica dei dati.

1. Prima di procedere, esaminare i [requisiti del server di origine](concepts-data-in-replication.md#requirements) .

2. Verificare che il server di origine consenta il traffico in ingresso e in uscita sulla porta 3306 e che disponga di un **indirizzo IP pubblico**, che il DNS sia accessibile pubblicamente o disponga di un nome di dominio completo (FQDN).

   Testare la connettività al server di origine provando a connettersi da uno strumento come la riga di comando MySQL ospitata in un altro computer o dalla [Azure cloud Shell](../cloud-shell/overview.md) disponibile nel portale di Azure.

   Se l'organizzazione dispone di rigidi criteri di sicurezza e non consente a tutti gli indirizzi IP nel server di origine di abilitare la comunicazione da Azure al server di origine, è possibile usare il comando seguente per determinare l'indirizzo IP del server MySQL.

   1. Accedere al database di Azure per MySQL usando uno strumento come la riga di comando di MySQL.

   2. Eseguire la query seguente.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      Di seguito è riportato un output di esempio:

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. Uscire dalla riga di comando di MySQL.
   4. Eseguire il comando seguente nell'utilità ping per ottenere l'indirizzo IP.

      ```bash
      ping <output of step 2b>
      ```

      Ad esempio:

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Configurare le regole del firewall del server di origine per includere l'indirizzo IP dell'output del passaggio precedente sulla porta 3306.

   > [!NOTE]
   > Questo indirizzo IP può cambiare a causa di operazioni di manutenzione/distribuzione. Questo metodo di connettività è solo per i clienti che non possono permettersi di consentire tutti gli indirizzi IP sulla porta 3306.
  
3. Attivare la registrazione binaria

   Per verificare se è stata abilitata la registrazione binaria nell'origine, eseguire il comando seguente: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se la variabile [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) viene restituita con il valore "on", la registrazione binaria è abilitata nel server.
   
   Se `log_bin` viene restituito con il valore "off", 
   1. Individuare il file di configurazione di MySQL (My. cnf) nel server di origine. Ad esempio:/etc/my.cnf
   2. Aprire il file di configurazione per modificarlo e individuare la sezione **mysqld** nel file.
   3.  Nella sezione mysqld aggiungere la riga seguente
   
       ```bash
       log-bin=mysql-bin.log
       ```
     
   4. Riavviare il server di origine MySQL per rendere effettive le modifiche.
   5. Una volta riavviato il server, verificare che la registrazione binaria sia abilitata eseguendo la stessa query precedente:
   
      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```
   
4. Impostazioni del server di origine

   Replica dei dati in ingresso richiede `lower_case_table_names` che il parametro sia coerente tra i server di origine e di replica. Per impostazione predefinita, in Database di Azure per MySQL questo parametro è impostato su 1.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Creare un nuovo ruolo di replica e configurare le autorizzazioni

   Creare un account utente nel server di origine configurato con privilegi di replica. Questa operazione può essere eseguita tramite comandi SQL o uno strumento come MySQL Workbench. Valutare se si prevede di eseguire la replica con SSL, poiché è necessario specificare questa impostazione quando si crea l'utente. Per informazioni su come [aggiungere gli account utente](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) nel server di origine, vedere la documentazione di MySQL.

   Nei comandi seguenti, il nuovo ruolo di replica creato può accedere all'origine da qualsiasi computer, non solo dal computer che ospita l'origine stessa. Questa operazione viene eseguita specificando "syncuser@'%'" nel comando per la creazione dell'utente. Per altre informazioni su come [specificare i nomi degli account](https://dev.mysql.com/doc/refman/5.7/en/account-names.html), vedere la documentazione di MySQL.

   **Comando SQL**

   *Replica con SSL*

   Se SSL deve essere obbligatorio per tutte le connessioni utente, quando si crea un utente usare il comando seguente:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replica senza SSL*

   Se SSL non è necessario per tutte le connessioni, utilizzare il comando seguente per creare un utente:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Per creare il ruolo di replica in MySQL Workbench, aprire il pannello **utenti e privilegi** dal pannello di **gestione** e quindi selezionare **Aggiungi account**.

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Utenti e privilegi":::

   Digitare il nome utente nel campo **Login Name** (ID di accesso).

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Sincronizzazione utente":::

   Selezionare il pannello **ruoli amministrativi** , quindi selezionare **slave di replica** nell'elenco dei **privilegi globali**. Quindi selezionare **applica** per creare il ruolo di replica.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Slave di replica":::

6. Impostazione del server di origine sulla modalità di sola lettura

   Prima di avviare il dump del database, il server deve essere impostato in modalità di sola lettura. In modalità di sola lettura, l'origine non sarà in grado di elaborare le transazioni di scrittura. Valutare l'impatto che questa impostazione può avere sulle attività aziendali e pianificare l'intervallo di impostazione in sola lettura in un orario di minore attività, se necessario.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Ottenere l'offset e il nome del file di log binario

   Eseguire il [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) comando per determinare il nome e l'offset del file di log binario corrente.

   ```sql
    show master status;
   ```

   I risultati verranno visualizzati in modo simile al seguente. Assicurarsi di annotare il nome del file binario, che verrà usato nei passaggi successivi.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Risultati stato master":::

## <a name="dump-and-restore-source-server"></a>Dump e ripristino del server di origine

1. Determinare quali database e tabelle si vuole replicare nel database di Azure per MySQL ed eseguire il dump dal server di origine.

    Per eseguire il dump dei database dal server master è possibile usare mysqldump. Per informazioni dettagliate, vedere [Dump e ripristino](concepts-migrate-dump-restore.md). Non è necessario eseguire il dump della libreria MySQL e della libreria di test.

2. Impostazione del server di origine sulla modalità di lettura/scrittura.

   Al termine del dump del database, riportare il server MySQL di origine in modalità di lettura/scrittura.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Ripristinare il file dump nel nuovo server.

   Ripristinare il file di dump nel server creato nel servizio Database di Azure per MySQL. Per informazioni su come ripristinare un file di dump in un server MySQL, vedere [Dump e ripristino](concepts-migrate-dump-restore.md). Se il file di dump ha grandi dimensioni, caricarlo in una macchina virtuale in Azure nella stessa area del server di replica. Ripristinare quindi il file nel server di Database di Azure per MySQL dalla macchina virtuale.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Collegare i server di origine e di replica per avviare Replica dei dati in ingresso

1. Impostazione del server di origine.

   Tutte le funzioni di replica dei dati in ingresso vengono eseguite tramite stored procedure. Per informazioni su tali procedure, vedere [Stored procedure per la replica dei dati in ingresso](./reference-stored-procedures.md). Le stored procedure possono essere eseguite nella shell di MySQL o in MySQL Workbench.

   Per collegare due server e avviare la replica, accedere al server di replica di destinazione nel servizio database di Azure per MySQL e impostare l'istanza esterna come server di origine. Questa operazione viene eseguita tramite la stored procedure `mysql.az_replication_change_master` nel server di Database di Azure per MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: nome host del server di origine
   - master_user: nome utente per il server di origine
   - master_password: password per il server di origine
   - master_log_file: nome del file di log binario da `show master status` in esecuzione
   - master_log_pos: posizione del file di log binario da `show master status` in esecuzione
   - master_ssl_ca: contesto del certificato della CA. Se non si usa SSL, passare una stringa vuota.

     È consigliabile passare questo parametro in come variabile. Per altre informazioni, vedere gli esempi seguenti.

   > [!NOTE]
   > Se il server di origine è ospitato in una macchina virtuale di Azure, impostare "Consenti l'accesso ai servizi di Azure" su "ON" per consentire la comunicazione tra i server di origine e di replica. Questa impostazione può essere modificata dalle opzioni di **sicurezza delle connessioni**. Per altre informazioni, vedere [Manage firewall rules using the Portal](howto-manage-firewall-using-portal.md) .

   **esempi**

   *Replica con SSL*

   La variabile `@cert` viene creata eseguendo i comandi MySQL seguenti:

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   La replica con SSL è configurata tra un server di origine ospitato nel dominio "companya.com" e un server di replica ospitato nel database di Azure per MySQL. Sulla replica viene eseguita questa stored procedure.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Replica senza SSL*

   La replica senza SSL è configurata tra un server di origine ospitato nel dominio "companya.com" e un server di replica ospitato nel database di Azure per MySQL. Sulla replica viene eseguita questa stored procedure.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. Filtro.

   Se si vuole ignorare la replica di alcune tabelle dal database master, aggiornare il `replicate_wild_ignore_table` parametro Server nel server di replica. È possibile specificare più di un modello di tabella usando un elenco delimitato da virgole.

   Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table).

   Per aggiornare il parametro, è possibile usare l' [portale di Azure](howto-server-parameters.md) o l'interfaccia della riga di comando di [Azure](howto-configure-server-parameters-using-cli.md).

3. Avviare la replica.

   Chiamare il `mysql.az_replication_start` stored procedure per avviare la replica.

   ```sql
   CALL mysql.az_replication_start;
   ```

4. Controllare lo stato della replica.

   Chiamare il [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) comando sul server di replica per visualizzare lo stato della replica.

   ```sql
   show slave status;
   ```

   Se lo stato di `Slave_IO_Running` e è `Slave_SQL_Running` "Yes" e il valore di `Seconds_Behind_Master` è "0", la replica funziona correttamente. `Seconds_Behind_Master` indica il ritardo della replica. Se il valore non è "0", significa che la replica sta elaborando gli aggiornamenti.

## <a name="other-stored-procedures"></a>Altre stored procedure

### <a name="stop-replication"></a>Arrestare la replica

Per arrestare la replica tra il server di origine e quello di replica, usare i stored procedure seguenti:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Rimuovere la relazione di replica

Per rimuovere la relazione tra il server di origine e quello di replica, usare la stored procedure seguente:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Ignorare un errore di replica

Per ignorare un errore di replica e consentire la continuazione della replica, usare la stored procedure seguente:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [replica dei dati in ingresso](concepts-data-in-replication.md) in Database di Azure per MySQL.
