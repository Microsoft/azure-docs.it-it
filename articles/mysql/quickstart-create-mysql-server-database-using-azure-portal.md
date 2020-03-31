---
title: 'Guida di avvio rapido: Creare un server - Portale di Azure - Database di Azure per MySQL'
description: Questo articolo contiene informazioni dettagliate su come usare il portale di Azure per creare rapidamente un database di Azure di esempio per il server MySQL in circa cinque minuti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 3/20/2020
ms.openlocfilehash: 50a3eebaa18674a0d0a7e9da88b8d3386e5016c5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067778"
---
# <a name="create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Creare un database di Azure per il server MySQL con il portale di Azure

Database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e ridimensionare database MySQL a disponibilità elevata nel cloud. Questa guida di avvio rapido illustra come creare un server Database di Azure per MySQL in circa cinque minuti usando il portale di Azure.  

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Aprire il Web browser e quindi passare al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un server Database di Azure per MySQL
Si crea un database di Azure per il server MySQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-unit-and-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md).

Seguire questa procedura per creare un database di Azure per il server MySQL:

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** (+).

2. Selezionare **Database** > **Database di Azure per MySQL**. È anche possibile immettere **MySQL** nella casella di ricerca per trovare il servizio.

   ![Opzione Database di Azure per MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Compilare il modulo dei dettagli del nuovo server con le informazioni seguenti:
   
   ![Creare il modulo del server](./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo** 
    ---|---|---
    Nome server | Nome server univoco | Immettere un nome univoco che identifichi il server del database di Azure per MySQL. Ad esempio, mydemoserver. Al nome del server specificato viene aggiunto il nome di dominio *mysql.database.azure.com*. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
    Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per il server. Se si hanno più sottoscrizioni, scegliere quella in cui viene fatturata la risorsa.
    Resource group | *myresourcegroup* | Specificare il nome di un gruppo di risorse nuovo o esistente.
    Selezionare l'origine | *Vuoto* | Selezionare *Vuoto* per creare un nuovo server da zero. Selezionare *Backup* se si sta creando un server da un backup geografico di un server Database di Azure per MySQL esistente.
    Accesso amministratore server | myadmin | Account di accesso da usare per la connessione al server. Il nome di accesso dell'amministratore non può essere **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public**.
    Password | *A scelta dell'utente* | Specificare una nuova password per l'account dell'amministratore del server. Deve avere una lunghezza compresa tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (0-9) e caratteri non alfanumerici (!, $, #, % e così via).
    Conferma password | *A scelta dell'utente*| Confermare la password dell'account amministratore.
    Location | *Area più vicina ai propri utenti*| Scegliere la località più vicina agli utenti o alle altre applicazioni Azure.
    Versione | *La versione principale più recente*| La versione principale più recente, a meno che non si abbiano requisiti specifici per cui deve esserne usata un'altra.
    Piano tariffario | **Utilizzo generico**, **Generazione 5**, **2 vCore**, **5 GB**, **7 giorni**, **Con ridondanza geografica** | Configurazioni di calcolo, archiviazione e backup per il nuovo server. Selezionare **Piano tariffario**. Selezionare quindi la scheda **Utilizzo generico**. *Generazione 5*, *4 vCore*, *100 GB* e *7 giorni* sono i valori predefiniti per **Generazione di calcolo**, **vCore**, **Archiviazione** e **Periodo di conservazione backup**. È possibile lasciare questi dispositivi di scorrimento nella posizione in cui si trovano. Per abilitare l'archiviazione con ridondanza geografica dei backup del server, selezionare **Con ridondanza geografica** in **Opzioni di ridondanza per il backup**. Per salvare la selezione del piano tariffario, selezionare **OK**. Lo screenshot successivo mostra queste selezioni.
  
   > [!NOTE]
   > È consigliabile usare il piano tariffario Basic se le esigenze di calcolo e di prestazioni I/O sono adeguate per il carico di lavoro. Si noti che i server creati nel piano tariffario Basic non possono essere scalati in un secondo momento per utilizzo generico o ottimizzati per la memoria. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mysql/).
   > 

   ![Creare un server: finestra del piano tariffario](./media/quickstart-create-mysql-server-database-using-azure-portal/3-pricing-tier.png)

4. Selezionare **Rivedi e crea** per effettuare il provisioning del server. Il provisioning può richiedere fino a 20 minuti.
   
5. Selezionare **Notifiche** sulla barra degli strumenti (icona a forma di campana) per monitorare il processo di distribuzione.
   
   Per impostazione predefinita, vengono creati i database seguenti nel server: **information_schema**, **mysql**, **performance_schema** e **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Il servizio Database di Azure per MySQL crea un firewall a livello di server, che impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database nel server, a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. 

1. Al termine della distribuzione, individuare il server. Se necessario, è possibile eseguire una ricerca. Scegliere **Tutte le risorse** dal menu a sinistra e quindi immettere il nome del server, che in questo esempio è **mydemoserver**, per cercare il server appena creato. Selezionare il nome del server nell'elenco dei risultati della ricerca. Si apre la pagina **Panoramica** del server in cui vengono fornite le opzioni per una configurazione aggiuntiva.

2. Nella pagina del server selezionare **Sicurezza connessione**.

3. Sotto l'intestazione **Regole del firewall** selezionare la casella di testo vuota nella colonna **Nome regola** per iniziare a creare la regola del firewall. Immettere l'intervallo preciso di indirizzi IP dei client che accederanno a questo server.
   
   ![Sicurezza delle connessioni: regole del firewall](./media/quickstart-create-mysql-server-database-using-azure-portal/5-firewall-2.png)



4. Sulla barra degli strumenti superiore della pagina **Sicurezza connessione** selezionare **Salva**. Prima di continuare, attendere che venga visualizzata la notifica che informa che l'aggiornamento è stato completato. 

   > [!NOTE]
   > Le connessioni al database di Azure per MySQL comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, non è possibile connettersi al server a meno che il reparto IT non apra la porta 3306.
   > 

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione
Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile che si sia preso nota prima di questi valori durante la lettura dell'articolo relativo all'avvio rapido. In caso contrario, è possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Proprietà** o nella pagina **Panoramica** del server nel portale di Azure.

A tale scopo, seguire questa procedura: 

1. Aprire la pagina **Panoramica** del server. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**. 

2. Passare il puntatore del mouse su ogni campo per visualizzare l'icona di copia a destra del testo. Selezionare l'icona di copia necessaria per copiare i valori.

In questo esempio, il nome del server è **mydemoserver.mysql.database.azure.com** e l'account di accesso amministratore del server è **myadmin\@mydemoserver**.

## <a name="connect-to-mysql-by-using-the-mysql-command-line-tool"></a>Connettersi a MySQL con lo strumento da riga di comando mysql
Connettersi al server tramite lo strumento da riga di comando **mysql.exe**. È possibile scaricare MySQL da [qui](https://dev.mysql.com/downloads/) e installarlo nel computer in uso. 

1. Per connettersi a un database di Azure per il server MySQL con l'utilità mysql, usare il formato seguente:

    ```bash
    mysql --host <fully qualified server name> --user <server admin login name>@<server name> -p
    ```

    Ad esempio, il comando seguente stabilisce la connessione al server di esempio:

    ```bash
    mysql --host mydemoserver.mysql.database.azure.com --user myadmin@mydemoserver -p
    ```

    Parametro di mysql |Valore consigliato|Descrizione
    ---|---|---
    --host | *Nome server* | Valore del nome del server usato in precedenza al momento della creazione del database di Azure per il server MySQL. Il server dell'esempio è **mydemoserver.mysql.database.azure.com**. Usare il nome di dominio completo ( **\*.mysql.database.azure.com**) come illustrato nell'esempio. Se non si ricorda il nome del server, seguire la procedura descritta nella sezione precedente per ottenere le informazioni di connessione. 
    --user | *Nome di accesso amministratore del server* |Nome utente di accesso amministratore del server specificato in precedenza al momento della creazione del database di Azure per il server MySQL. Se non si ricorda il nome utente, seguire la procedura descritta nella sezione precedente per ottenere le informazioni di connessione. Il formato è *nome utente\@nome server*.
    -p | *Attendere finché non viene richiesta* |Quando richiesto, digitare la stessa password specificata al momento della creazione del server. Si noti che i caratteri della password digitati non vengono visualizzati nel prompt di Bash durante la digitazione. Dopo aver immesso la password, premere **INVIO**.

   Dopo che è stata stabilita la connessione, l'utilità mysql visualizza un prompt `mysql>` in cui è possibile digitare comandi. 

   Di seguito è riportato un output di mysql di esempio:

    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Se il firewall non è configurato per accettare l'indirizzo IP del client, verrà visualizzato l'errore seguente:
    >
    > ERRORE 2003 (28000): Al client con indirizzo IP 123.456.789.0 non è consentito l'accesso al server.
    >
    > Per risolvere l'errore, assicurarsi che la configurazione del server corrisponda alla procedura illustrata nella sezione "Configurare una regola del firewall a livello di server" di questo articolo.

4. Per verificare che la connessione funzioni, digitare `status` al prompt mysql> per visualizzare lo stato del server.

    ```sql
    status
    ```

   > [!TIP]
   > Per altri comandi, vedere il [capitolo 4.5.1 del manuale di riferimento di MySQL 5.7](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Creare un database vuoto al prompt **mysql>** digitando il comando seguente:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    Il completamento del comando potrebbe richiedere alcuni istanti. 

    In un database di Azure per il server MySQL è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o creare più database per condividere le risorse. Il numero di database che è possibile creare è illimitato, ma più database condividono le stesse risorse del server. 

6. Elencare i database al prompt **mysql>** digitando il comando seguente:

    ```sql
    SHOW DATABASES;
    ```

7.  Digitare `\q` e quindi premere **INVIO** per uscire dallo strumento mysql. 

Ora che si è connessi al database di Azure per il server MySQL e si è creato un database utente vuoto, passare alla sezione successiva per un esercizio simile. Nel prossimo esercizio viene stabilita la connessione allo stesso server usando un altro strumento comune, MySQL Workbench.

## <a name="connect-to-the-server-by-using-the-mysql-workbench-gui-tool"></a>Connettersi al server con lo strumento con interfaccia utente grafica MySQL Workbench
Per connettersi al server con lo strumento con interfaccia utente grafica MySQL Workbench, seguire questa procedura:

1.    Aprire l'applicazione MySQL Workbench nel computer client. È possibile scaricare e installare MySQL Workbench dalla pagina [Download MySQL Workbench](https://dev.mysql.com/downloads/workbench/) (Scaricare MySQL Workbench).

2. Crea una nuova connessione. Fare clic sull'icona del segno più (+) accanto all'intestazione **MySQL Connections** (Connessioni MySQL).

3. Nella finestra di dialogo **Setup New Connection** (Configura nuova connessione) immettere le informazioni di connessione server nella scheda **Parameters** (Parametri). I valori segnaposto sono visualizzati a titolo di esempio. Sostituire Hostname (Nome host), Username (Nome utente) e Password con valori personalizzati.

   ![Configurare una nuova connessione](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    |Impostazione |Valore consigliato|Descrizione campo|
    |---|---|---|
     Nome connessione | Demo Connection | Etichetta per la connessione. |
    Metodo di connessione | Standard (TCP/IP) | Standard (TCP/IP) è sufficiente. |
    nomehost | *Nome server* | Nome del server usato in precedenza al momento della creazione del database di Azure per il server MySQL. Il server dell'esempio è **mydemoserver.mysql.database.azure.com**. Usare il nome di dominio completo ( **\*.mysql.database.azure.com**) come illustrato nell'esempio. Se non si ricorda il nome del server, seguire la procedura descritta nella sezione precedente per ottenere le informazioni di connessione.|
     Porta | 3306 | Porta da usare per la connessione al database di Azure per il server MySQL. |
    Username |  *Nome di accesso amministratore del server* | Informazioni di accesso amministratore del server specificate in precedenza al momento della creazione del database di Azure per il server MySQL. Il nome utente dell'esempio viene **myadmin\@mydemoserver**. Se non si ricorda il nome utente, seguire la procedura descritta nella sezione precedente per ottenere le informazioni di connessione. Il formato è *nome utente\@nome server*.
    Password | *Password* | Per salvare la password, selezionare **Store in Vault** (Archivia nell'insieme di credenziali). |

4. Selezionare **Test Connection** (Test connessione) per verificare che tutti i parametri siano configurati correttamente. Selezionare quindi **OK** per salvare la connessione. 

    > [!NOTE]
    > SSL viene applicato per impostazione predefinita nel server e richiede una configurazione aggiuntiva per completare la connessione. Per altre informazioni, vedere [Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MySQL](./howto-configure-ssl.md). Per disabilitare SSL per questa guida di avvio rapido, passare al portale di Azure. Selezionare quindi la pagina Sicurezza connessione per disabilitare l'interruttore **Imponi connessione SSL**.

## <a name="clean-up-resources"></a>Pulire le risorse
È possibile eseguire la pulizia delle risorse create nel corso della guida di avvio rapido in due modi. Si può eliminare il [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md), in modo da includere tutte le risorse del gruppo. Se invece si vogliono mantenere intatte le altre risorse, eliminare solo la risorsa server.

> [!TIP]
> Altre guide di avvio rapido della raccolta si basano su questa. Se si intende continuare a usare le guide di avvio rapido, non eseguire la pulizia delle risorse create in questa. Se non si intende continuare, usare la procedura seguente per eliminare tutte le risorse create con questa guida di avvio rapido.
>

Per eliminare l'intero gruppo di risorse, incluso il server appena creato, seguire questa procedura:

1.    Individuare il gruppo di risorse nel portale di Azure. Scegliere **Gruppi di risorse** dal menu a sinistra e quindi selezionare il nome del gruppo di risorse, che in questo esempio è **myresourcegroup**.

2.    Nella pagina del gruppo di risorse selezionare **Elimina**. Immettere quindi nella casella il nome del gruppo di risorse, in questo esempio **myresourcegroup**, per confermare l'eliminazione e selezionare **Elimina**.

Per eliminare solo il server appena creato, seguire questa procedura:

1. Individuare il server nel portale di Azure, se non è già aperto. Scegliere **Tutte le risorse** dal menu a sinistra nel portale di Azure. Cercare quindi il server creato.

2. Nella pagina **Panoramica** selezionare **Elimina**. 

   ![Database di Azure per MySQL: eliminare il server](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)

3. Verificare il nome del server che si vuole eliminare e visualizzare i database sottostanti interessati. Immettere nella casella il nome del server, in questo esempio **mydemoserver**. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Progettare il primo database di Azure per il database MySQL](./tutorial-design-database-using-portal.md)

