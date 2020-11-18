---
title: 'Esercitazione: Progettare un database di Azure per PostgreSQL - Server singolo - Portale di Azure'
description: Questa esercitazione illustrata come progettare la prima istanza del database di Azure per server singolo PostgreSQL tramite il portale di Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: tutorial, mvc
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 179d4124e28abfffa83b16cfdb418d8e860192d8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337133"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Esercitazione: Progettare un'istanza del database di Azure per server singolo PostgreSQL usando il portale di Azure

Il database di Azure per PostgreSQL è un servizio gestito che consente di eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. Tramite il portale di Azure, è possibile gestire facilmente il server e progettare un database.

In questa esercitazione si userà il portale di Azure per imparare a:
> [!div class="checklist"]
> * Creare un database di Azure per il server PostgreSQL
> * Configurare il firewall del server
> * Usare l'utilità [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

## <a name="prerequisites"></a>Prerequisiti
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-azure-database-for-postgresql"></a>Creare un database di Azure per PostgreSQL

Verrà creato un database di Azure per il server PostgreSQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-pricing-tiers.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md).

Seguire questa procedura per creare un database di Azure per il server PostgreSQL:
1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Database** nella pagina **Nuovo** e selezionare **Database di Azure per PostgreSQL** nella pagina **Database**.
   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/1-create-database.png" alt-text="Database di Azure per PostgreSQL - Creare il database":::

3. Selezionare l'opzione di distribuzione **Server singolo**.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/select-deployment-option.png" alt-text="Selezionare l'opzione di distribuzione Database di Azure per PostgreSQL - Server singolo":::

4. Compilare il modulo **Informazioni di base** con le informazioni seguenti:

    :::image type="content" source="./media/tutorial-design-database-using-azure-portal/create-basics.png" alt-text="Creare un server":::

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    Subscription|Nome della sottoscrizione utente|Sottoscrizione di Azure da usare per il server. Se si hanno più sottoscrizioni, scegliere quella in cui viene fatturata la risorsa.
    Resource group|*myresourcegroup*| Nuovo nome di gruppo di risorse o uno esistente nella sottoscrizione.
    Nome server |*mydemoserver*|Nome univoco per identificare il database di Azure per il server PostgreSQL. Al nome del server specificato viene aggiunto il nome di dominio *postgres.database.azure.com*. Il server può contenere solo lettere minuscole, numeri e il segno meno (-). Deve contenere almeno da 3 a 63 caratteri.
    Origine dati | *Nessuno* | Selezionare *Nessuno* per creare un nuovo server da zero. Si selezionerebbe *Backup* se si stesse creando un server da un backup geografico di un server Database di Azure per PostgreSQL esistente.
    Nome utente amministratore |*myadmin*| Account di accesso da usare per la connessione al server. Il nome di accesso dell'amministratore non può essere **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** o **public**. Non può iniziare con **pg_** .
    Password |Password| Nuova password per l'account amministratore del server. Deve contenere tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (da 0 a 9) e caratteri non alfanumerici (!, $, #, % e così via).
    Location|Area più vicina ai propri utenti| Località più vicina agli utenti.
    Versione|La versione principale più recente| La versione principale più recente di PostgreSQL, a meno che non si abbiano requisiti specifici diversi.
    Calcolo e archiviazione | **Utilizzo generico**, **Generazione 5**, **2 vCore**, **5 GB**, **7 giorni**, **Con ridondanza geografica** | Configurazioni di calcolo, archiviazione e backup per il nuovo server. Selezionare **Configura server**. Selezionare quindi la scheda **Utilizzo generico**. *Generazione 5*, *4 vCore*, *100 GB* e *7 giorni* sono i valori predefiniti per **Generazione di calcolo**, **vCore**, **Archiviazione** e **Periodo di conservazione backup**. È possibile lasciare questi dispositivi di scorrimento nella posizione in cui si trovano oppure regolarli. Per abilitare l'archiviazione con ridondanza geografica dei backup del server, selezionare **Con ridondanza geografica** in **Opzioni di ridondanza per il backup**. Per salvare la selezione del piano tariffario, selezionare **OK**. Lo screenshot successivo mostra queste selezioni.

   > [!NOTE]
   > È consigliabile usare il piano tariffario Basic se le esigenze di calcolo e di prestazioni I/O sono adeguate per il carico di lavoro. Si noti che i server creati nel piano tariffario Basic non possono essere scalati in un secondo momento per utilizzo generico o ottimizzati per la memoria. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/postgresql/).
   > 

    :::image type="content" source="./media/quickstart-create-database-portal/2-pricing-tier.png" alt-text="Riquadro Piano tariffario":::

    > [!TIP]
    > Quando l'opzione per l'**aumento automatico** è abilitata, il server aumenta lo spazio di archiviazione disponibile quando si sta per raggiungere il limite allocato, senza alcun impatto sul carico di lavoro.

5. Selezionare **Revisione e creazione** per rivedere le selezioni effettuate. Selezionare **Crea** per effettuare il provisioning del server. Questa operazione può richiedere qualche minuto.

6. Sulla barra degli strumenti selezionare l'icona **Notifiche** a forma di campana per monitorare il processo di distribuzione. Al termine della distribuzione è possibile selezionare **Aggiungi al dashboard** per creare un riquadro di questo server nel dashboard del portale di Azure come collegamento alla pagina **Panoramica** del server. Selezionare **Vai alla risorsa** per aprire la pagina **Panoramica** del server.

    :::image type="content" source="./media/quickstart-create-database-portal/3-notifications.png" alt-text="Riquadro Notifiche":::
   
   Per impostazione predefinita, un database **postgres** viene creato nel server. Il database [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) è un database predefinito che può essere usato da utenti, utilità e applicazioni di terze parti. (L'altro database predefinito è **azure_maintenance**. La sua funzione è quella di separare i processi del servizio gestito dalle azioni dell'utente. Non è possibile accedere a questo database.)


## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Il servizio Database di Azure per PostgreSQL usa un firewall a livello di server. Per impostazione predefinita, questo firewall impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database nel server, a meno che non venga creata una regola del firewall per aprire il firewall per un intervallo di indirizzi IP specifici. 

1. Al termine della distribuzione, fare clic su **Tutte le risorse** nel menu a sinistra e digitare il nome **mydemoserver** per cercare il server appena creato. Fare clic sul nome del server elencato nei risultati della ricerca. Si apre la pagina **Panoramica** del server in cui vengono fornite le opzioni per una configurazione aggiuntiva.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Database di Azure per PostgreSQL - Cercare il server":::

2. Nella pagina del server selezionare **Sicurezza connessione**. 

3. Fare clic nella casella di testo in **Nome regola** e aggiungere una nuova regola del firewall per specificare l'intervallo IP per la connettività. Immettere l'intervallo IP. Fare clic su **Salva**.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/5-firewall-2.png" alt-text="Database di Azure per PostgreSQL: creare una regola del firewall":::

4. Fare clic su **Salva** e quindi fare clic su **X** per chiudere la pagina **Sicurezza connessione**.

   > [!NOTE]
   > Il server PostgreSQL Azure comunica sulla porta 5432. Se si sta cercando di connettersi da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. In questo caso, non è possibile connettersi al server di database SQL di Azure, a meno che il reparto IT non apra la porta 5432.
   >

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Quando si è creato il server del Database di Azure per PostgreSQL, è stato creato anche il database **postgres** predefinito. Per connettersi al server del database, è necessario fornire le informazioni sull'host e le credenziali di accesso.

1. Nel menu a sinistra nel portale di Azure fare clic su **Tutte le risorse** e cercare il server appena creato.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Database di Azure per PostgreSQL - Cercare il server":::

2. Fare clic sul nome del server **mydemoserver**.

3. Selezionare la pagina **Panoramica** del server. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/6-server-name.png" alt-text="Database di Azure per PostgreSQL - Accesso dell'amministratore del server":::


## <a name="connect-to-postgresql-database-using-psql"></a>Connettersi al database PostgreSQL tramite psql
Se nel computer client è installato PostgreSQL, è possibile usare un'istanza locale di [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) o la console Cloud di Azure per connettersi a un server PostgreSQL di Azure. Si usi ora l'utilità della riga di comando psql per connettersi al Database di Azure per il server PostgreSQL.

1. Eseguire il comando psql seguente per connettersi a un Database di Azure per PostgreSQL:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Ad esempio, il comando seguente consente di connettersi al database predefinito denominato **postgres** nel server PostgreSQL **mydemoserver.postgres.database.azure.com** usando le credenziali di accesso. Immettere il valore di `<server_admin_password>` scelto quando viene chiesta la password.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se si preferisce usare un percorso URL per connettersi a Postgres, l'URL codifica il segno @ nel nome utente con `%40`. Ad esempio la stringa di connessione per psql sarà
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Dopo aver eseguito la connessione al server, creare un database vuoto al prompt:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. Nel prompt, eseguire il comando seguente per cambiare la connessione nel database appena creato **mypgsqldb**:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database
Dopo aver appreso come connettersi al Database di Azure per PostgreSQL, si possono completare alcune attività di base:

In primo luogo, creare una tabella e caricarvi alcuni dati. Si crei ad esempio una tabella che tenga traccia delle informazioni riguardanti l'inventario usando questo codice SQL:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

È possibile visualizzare ora la tabella appena creata nell'elenco di tabelle digitando:
```sql
\dt
```

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle
Dopo aver creato una tabella, inserire alcuni dati. Nella finestra del prompt dei comandi aperta, eseguire la query seguente per inserire alcune righe di dati.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

A questo punto sono presenti due righe di dati di esempio nella tabella di inventario creata in precedenza.

## <a name="query-and-update-the-data-in-the-tables"></a>Eseguire una query e aggiornare i dati nelle tabelle
Eseguire la query seguente per recuperare informazioni dalla tabella di inventario del database. 
```sql
SELECT * FROM inventory;
```

Si possono anche aggiornare query e dati nelle tabelle.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Quando si recuperano i dati, è possibile visualizzare i valori aggiornati.
```sql
SELECT * FROM inventory;
```

## <a name="restore-data-to-a-previous-point-in-time"></a>Ripristinare i dati a un punto precedente nel tempo
Si supponga di aver eliminato accidentalmente questa tabella. Questa situazione non è facile da ripristinare. Database di Azure per PostgreSQL consente di tornare a qualsiasi momento specifico per cui sono disponibili backup del server (in base al periodo di conservazione dei backup configurato) ed eseguirne il ripristino in un nuovo server. È possibile usare questo nuovo server per ripristinare i dati eliminati. La procedura seguente ripristina il server **mydemoserver** a un momento precedente all'aggiunta della tabella di inventario.

1. Nella pagina **Panoramica** del server Database di Azure per PostgreSQL, fare clic su **Ripristino** sulla barra degli strumenti. Si apre la pagina **Ripristina** .

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/9-azure-portal-restore.png" alt-text="Screenshot che mostra la pagina **Panoramica ** del server Database di Azure per PostgreSQL ed evidenzia il pulsante Ripristina.":::

2. Compilare il modulo **Ripristina** con le informazioni obbligatorie:

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/10-azure-portal-restore.png" alt-text="Portale di Azure - Opzioni del modulo di ripristino":::

   - **Punto di ripristino**: selezionare un punto nel tempo precedente la modifica del server
   - **Server di destinazione**: specificare il nome del nuovo server in cui eseguire il ripristino
   - **Località**: non è possibile selezionare l'area, per impostazione predefinita è la stessa del server di origine
   - **Piano tariffario**: non è possibile modificare questo valore quando si ripristina un server. È uguale al server di origine. 
3. Fare clic su **OK** per [ripristinare il server a un momento specifico](./howto-restore-server-portal.md) precedente all'eliminazione della tabella. Il ripristino di un server a un momento diverso crea un nuovo server duplicato uguale al server originale nel momento specificato, a condizione che rientri nel periodo di conservazione previsto per il [piano tariffario](./concepts-pricing-tiers.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di server. Se non si prevede di avere bisogno di queste risorse in futuro, eliminare il gruppo di server. Fare clic sul pulsante *Elimina* nella pagina *Panoramica* per il gruppo di server. Quando viene visualizzata la richiesta in una pagina popup, verificare il nome del gruppo di server e fare clic sul pulsante *Elimina* in basso.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si apprenderà come usare il portale di Azure e altre utilità per:
> [!div class="checklist"]
> * Creare un database di Azure per il server PostgreSQL
> * Configurare il firewall del server
> * Usare l'utilità **psql** per creare un database
> * Caricare dati di esempio
> * Eseguire query sui dati
> * Aggiornare i dati
> * Ripristinare i dati

> [!div class="nextstepaction"]
>[Progettare il primo Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure](tutorial-design-database-using-azure-cli.md)