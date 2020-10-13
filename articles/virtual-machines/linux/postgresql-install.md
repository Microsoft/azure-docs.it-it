---
title: Configurare PostgreSQL in una macchina virtuale Linux
description: Informazioni su come installare e configurare PostgreSQL in una macchina virtuale Linux in Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: fdde7613627c9fec0694f3985f78cf10e52f59c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397097"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Installare e configurare PostgreSQL in Azure
PostgreSQL è un database open source avanzato simile a Oracle e DB2. Questo database include funzionalità aziendali quali la conformità ACID completa, l'elaborazione transazionale affidabile e il controllo della concorrenza per più versioni. Supporta anche standard come ANSI SQL e SQL/MED (compresi wrapper di dati esterni per Oracle, MySQL, MongoDB e molti altri). È inoltre altamente estendibile, supportando oltre 12 linguaggi procedurali, gli indici GIN e GIST, i dati spaziali e più funzionalità di tipo NoSQL per le applicazioni basate su chiave-valore o JSON.

Questo articolo illustrerà come installare e configurare PostgreSQL in una macchina virtuale di Azure che esegue Linux.


## <a name="install-postgresql"></a>Installare PostgreSQL
> [!NOTE]
> Per poter completare questa esercitazione, è necessario disporre già di una macchina virtuale di Microsoft Azure che esegue Linux. Prima di procedere, vedere l' [esercitazione relativa alle macchine virtuali Linux di Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)per creare e impostare una macchina virtuale Linux.
> 
> 

In questo caso, usare la porta 1999 come porta di PostgreSQL.  

Connettersi tramite PuTTY alla macchina virtuale Linux creata. Se questa è la prima volta che si sta usando una macchina virtuale Linux di Azure, vedere [usare SSH con Linux in Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per imparare a usare PuTTY per connettersi a una VM Linux.

1. Eseguire il comando seguente per passare alla directory radice (admin):

    ```console
    # sudo su -
    ```

2. Alcune distribuzioni dispongono di dipendenze che è necessario installare prima di eseguire l'installazione di PostgreSQL. Nell'elenco individuare la distribuzione in uso ed eseguire il comando appropriato:
   
   * Linux basato su Red Hat:

        ```console
        # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

   * Linux basato su Debian:

        ```console
        # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y
        ```

   * SUSE Linux:

        ```console
        # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

3. Scaricare PostgreSQL nella directory radice e quindi decomprimere il pacchetto:

    ```console
    # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

    # tar jxvf  postgresql-9.3.5.tar.bz2
    ```

    Quello precedente è un esempio. Per l'indirizzo di download più dettagliato, vedere [Indice di /pub/source/](https://ftp.postgresql.org/pub/source/).

4. Per avviare la compilazione, eseguire questi comandi:

    ```console
    # cd postgresql-9.3.5

    # ./configure --prefix=/opt/postgresql-9.3.5
    ```

5. Se si desidera compilare tutti gli elementi che possono essere compilati, inclusa la documentazione (pagine HTML e Man) e i moduli aggiuntivi ( `contrib` ), eseguire invece il comando seguente:

    ```console
    # gmake install-world
    ```

    Dovrebbe venire visualizzato il messaggio di conferma seguente:

    ```output
    PostgreSQL, contrib, and documentation successfully made. Ready to install.
    ```

## <a name="configure-postgresql"></a>Configurare PostgreSQL
1. (Facoltativo) Creare un collegamento simbolico per abbreviare il riferimento a PostgreSQL in modo da non includere il numero di versione:

    ```console
    # ln -s /opt/postgresql-9.3.5 /opt/pgsql
    ```

2. Creare una directory per il database:

    ```console
    # mkdir -p /opt/pgsql_data
    ```

3. Creare un utente non ROOT e modificare il relativo profilo. Passare quindi a tale nuovo utente (denominato *postgres* nell'esempio):

    ```console
    # useradd postgres
   
    # chown -R postgres.postgres /opt/pgsql_data
   
    # su - postgres
    ```
   
   > [!NOTE]
   > Per motivi di sicurezza, PostgreSQL usa un utente non ROOT per inizializzare, avviare o arrestare il database.
   > 
   > 
4. Modificare il file *bash_profile* immettendo i comandi seguenti. Queste righe verranno aggiunte alla fine del file *bash_profile*:

    ```config
    cat >> ~/.bash_profile <<EOF
    export PGPORT=1999
    export PGDATA=/opt/pgsql_data
    export LANG=en_US.utf8
    export PGHOME=/opt/pgsql
    export PATH=\$PATH:\$PGHOME/bin
    export MANPATH=\$MANPATH:\$PGHOME/share/man
    export DATA=`date +"%Y%m%d%H%M"`
    export PGUSER=postgres
    alias rm='rm -i'
    alias ll='ls -lh'
    EOF
    ```

5. Eseguire il file *bash_profile*:

    ```console
    $ source .bash_profile
    ```

6. Convalidare l'installazione con il comando seguente:

    ```console
    $ which psql
    ```

    Se l'installazione ha avuto esito positivo, verrà visualizzata la risposta seguente:

    ```output
    /opt/pgsql/bin/psql
    ```

7. È anche possibile verificare la versione di PostgreSQL:

    ```sql
    $ psql -V
    ```

8. Inizializzare il database:

    ```console
    $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
    ```

    Dovrebbero venire visualizzato l'output seguente:

![Screenshot che mostra l'output dopo l'inizializzazione del database.](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Impostare PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Eseguire i comandi seguenti:

```console
# cd /root/postgresql-9.3.5/contrib/start-scripts

# cp linux /etc/init.d/postgresql
```

Modificare due variabili nel file /etc/init.d/postgresql. Il prefisso è impostato sul percorso di installazione di PostgreSQL: **/opt/pgsql**. PGDATA è impostato sul percorso di archiviazione dati di PostgreSQL: **/opt/pgsql_data**.

```config
# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql
```

![Screenshot che mostra il prefisso di installazione e la directory dei dati.](./media/postgresql-install/no2.png)

Modificare il file per renderlo eseguibile:

```console
# chmod +x /etc/init.d/postgresql
```

Avviare PostgreSQL:

```console
# /etc/init.d/postgresql start
```

Controllare se l'endpoint di PostgreSQL è attivo:

```console
# netstat -tunlp|grep 1999
```

Viene visualizzato l'output seguente:

![Screenshot che mostra l'endpoint di PostgreSQL è on.](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Connessione al database Postgres
Proseguire e passare di nuovo all'utente postgres:

```console
# su - postgres
```

Creare un database Postgres:

```console
$ createdb events
```

Connettersi al database events appena creato:

```console
$ psql -d events
```

## <a name="create-and-delete-a-postgres-table"></a>Come creare ed eliminare una tabella Postgres
Ora che ci si è connessi al database, è possibile crearvi tabelle.

Ad esempio, creare una nuova tabella Postgres di esempio con il comando seguente:

```sql
CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);
```

È stata così impostata una tabella di quattro colonne con i nomi e le restrizioni seguenti:

1. La colonna "name" è stata limitata dal comando VARCHAR a una lunghezza inferiore a 20 caratteri.
2. La colonna "food" indica la pietanza che verrà portata da ogni persona. VARCHAR limita questo testo a una lunghezza inferiore a 30 caratteri.
3. La colonna "confirmed" registra se la persona ha dato conferma della propria partecipazione. I valori consentiti sono "Y" e "N".
4. La colonna "date" indicherà quando la persona si è iscritta per l'evento. Postgres richiede che le date vengano immesse nel formato aaaa-mm-gg.

Se la tabella è stata creata correttamente, dovrebbe venire visualizzato quanto segue:

![Screenshot che mostra il messaggio visualizzato quando la tabella viene creata correttamente.](./media/postgresql-install/no4.png)

È anche possibile verificare la struttura della tabella con il comando seguente:

![Screenshot che mostra il comando per il controllo della struttura della tabella.](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Aggiungere dati a una tabella
Inserire innanzitutto le informazioni in una riga:

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');
```

Dovrebbe essere visualizzato questo output:

![Screenshot che mostra le informazioni sulle righe aggiunte.](./media/postgresql-install/no6.png)

È anche possibile aggiungere altre persone alla tabella. Ecco alcuni esempi oppure è possibile inserire i dati desiderati:

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');
```

### <a name="show-tables"></a>Mostrare le tabelle
Per mostrare una tabella, usare il comando seguente:

```sql
select * from potluck;
```

L'output è il seguente:

![Screenshot che mostra l'output del comando per la visualizzazione di una tabella.](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Eliminare dati in una tabella
Per eliminare dati in una tabella, usare il comando seguente:

```sql
delete from potluck where name=’John’;
```

Consente di eliminare tutte le informazioni nella riga "John". L'output è il seguente:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aggiornare dati in una tabella
Per aggiornare dati in una tabella, usare il comando seguente: Per questo motivo, Sandy ha confermato che sono partecipanti, quindi il RSVP verrà modificato da "N" a "Y":

```sql
UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';
```

## <a name="get-more-information-about-postgresql"></a>Per altre informazioni su PostgreSQL
Ora che è stata completata l'installazione di PostgreSQL in una macchina virtuale Linux di Azure, è possibile utilizzarlo in Azure. Per ulteriori informazioni su PostgreSQL, visitare il [sito Web PostgreSQL](https://www.postgresql.org/).
