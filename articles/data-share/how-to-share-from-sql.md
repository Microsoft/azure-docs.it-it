---
title: Condividere e ricevere i dati da Database SQL di Azure e Azure Synapse Analytics
description: Informazioni su come condividere e ricevere dati dal database SQL di Azure e da Azure sinapsi Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/24/2021
ms.openlocfilehash: f87ad76e9bb1db4d71716bf860d5fee2d413e8e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740376"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Condividere e ricevere i dati da Database SQL di Azure e Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

La condivisione di dati di Azure supporta la condivisione basata su snapshot del database SQL di Azure e di Azure sinapsi Analytics. Questo articolo illustra come condividere e ricevere dati da queste origini.

La condivisione di dati di Azure supporta la condivisione di tabelle e viste dal database SQL di Azure e da Azure sinapsi Analytics (in precedenza Azure SQL DW) e la condivisione di tabelle da Azure sinapsi Analytics (area di lavoro) pool SQL dedicato. La condivisione da Azure sinapsi Analytics (area di lavoro) pool SQL senza server non è attualmente supportata. I consumer di dati possono scegliere di accettare i dati in Azure Data Lake Storage Gen2 o nell'archiviazione BLOB di Azure come file con estensione CSV o parquet, oltre che nel database SQL di Azure e in Azure sinapsi Analytics come tabelle.

Quando si accettano dati in Azure Data Lake Store Gen2 o nell'archiviazione BLOB di Azure, gli snapshot completi sovrascrivono il contenuto del file di destinazione, se già esistente.
Quando i dati vengono ricevuti nella tabella SQL e se la tabella di destinazione non esiste già, Azure Data Share crea la tabella SQL con lo schema di origine. Se una tabella di destinazione esiste già con lo stesso nome, verrà eliminata e sovrascritta con lo snapshot completo più recente. Gli snapshot incrementali non sono attualmente supportati.

## <a name="share-data"></a>Condividere dati

### <a name="prerequisites-to-share-data"></a>Prerequisiti per la condivisione di dati

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Indirizzo di posta elettronica per l'accesso ad Azure dei destinatari (gli alias di posta elettronica non funzionano).
* Se l'archivio dati di Azure di origine si trova in una sottoscrizione di Azure diversa da quella che verrà usata per creare la risorsa Condivisione dati, registrare il [provider di risorse Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) nella sottoscrizione in cui risiede l'archivio dati di Azure. 

### <a name="prerequisites-for-sql-source"></a>Prerequisiti per l'origine SQL
Di seguito è riportato l'elenco di prerequisiti per la condivisione da un'origine SQL. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Prerequisiti per la condivisione da Database SQL di Azure o Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse)
Per configurare i prerequisiti, è possibile seguire la [demo dettagliata](https://youtu.be/hIE-TjJD8Dc).

* Un'istanza di Database SQL di Azure o di Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse) con tabelle e viste da condividere.
* Autorizzazione per la scrittura nei database del server SQL, disponibile in *Microsoft.Sql/servers/databases/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**.
* Autorizzazione per l'identità gestita della risorsa Condivisione dati per l'accesso al database. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare al server SQL e impostare se stessi come **amministratore di Azure Active Directory**.
    1. Connettersi all'istanza di Database SQL di Azure/Data Warehouse usando l'[editor di query](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) o SQL Server Management Studio con l'autenticazione di Azure Active Directory. 
    1. Eseguire lo script seguente per aggiungere l'identità gestita della risorsa Condivisione dati come db_datareader. È necessario connettersi usando Active Directory e non l'autenticazione di SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati. Se ancora non si è provveduto a creare una risorsa Condivisione dati, è possibile tornare a questo prerequisito in un secondo momento.  

* Un utente di Database SQL di Azure con accesso **'db_datareader'** per individuare e selezionare le tabelle e/o le viste da condividere. 

* Accesso al firewall di SQL Server. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare al server SQL. Selezionare *Firewall e reti virtuali* nel riquadro di spostamento sinistro.
    1. Fare clic su **Sì** per *Consenti alle risorse e ai servizi di Azure di accedere a questo server*.
    1. Fare clic su **+ Aggiungi IP client**. L'indirizzo IP client è soggetto a modifiche. Potrebbe essere necessario ripetere questo processo la volta successiva che si condividono i dati SQL dal portale di Azure. È anche possibile aggiungere un intervallo di indirizzi IP.
    1. Fare clic su **Salva**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Prerequisiti per la condivisione dal pool SQL di Azure Synapse Analytics (area di lavoro)

* Un pool SQL dedicato di Azure Synapse Analytics (area di lavoro) con le tabelle da condividere. La condivisione di viste non è attualmente supportata. La condivisione dal pool SQL serverless non è attualmente supportata.
* Autorizzazione per scrivere nel pool SQL nell'area di lavoro di Synapse, presente in *Microsoft.Synapse/workspaces/sqlPools/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**.
* Autorizzazione per l'identità gestita della risorsa Condivisione dati per l'accesso al pool SQL dell'area di lavoro di Synapse. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare all'area di lavoro di Synapse. Selezionare Amministratore SQL Active Directory nel riquadro di spostamento sinistro e impostare se stessi come **amministratore di Azure Active Directory**.
    1. Aprire Synapse Studio e selezionare *Gestisci* nel riquadro di spostamento sinistro. Selezionare *Controllo di accesso* in Sicurezza. Assegnare a se stessi il ruolo di **amministratore SQL** o **amministratore dell'area di lavoro**.
    1. In Synapse Studio selezionare *Sviluppo* nel riquadro di spostamento sinistro. Eseguire lo script seguente nel pool SQL per aggiungere l'identità gestita della risorsa Condivisione dati come db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati. Se ancora non si è provveduto a creare una risorsa Condivisione dati, è possibile tornare a questo prerequisito in un secondo momento.  

* Accesso al firewall dell'area di lavoro di Synapse. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare all'area di lavoro di Synapse. Selezionare *Firewall* nel riquadro di spostamento sinistro.
    1. Fare clic su **Sì** per *Consenti alle risorse e ai servizi di Azure di accedere a questa area di lavoro*.
    1. Fare clic su **+ Aggiungi IP client**. L'indirizzo IP client è soggetto a modifiche. Potrebbe essere necessario ripetere questo processo la volta successiva che si condividono i dati SQL dal portale di Azure. È anche possibile aggiungere un intervallo di indirizzi IP.
    1. Fare clic su **Salva**. 

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Creare un account di condivisione dati

Creare una risorsa di condivisione dati di Azure in un gruppo di risorse di Azure.

1. Selezionare il pulsante del menu nell'angolo superiore sinistro del portale e quindi selezionare **+ Crea una risorsa**.

1. Cercare *Condivisione dati*.

1. Selezionare Condivisione dati e quindi **Crea**.

1. Compilare i dettagli di base della risorsa di condivisione dati di Azure con le informazioni seguenti. 

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per l'account di condivisione dati.|
    | Gruppo di risorse | *test-resource-group* | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Posizione | *Stati Uniti orientali 2* | Selezionare un'area per l'account di condivisione dati.
    | Nome | *datashareaccount* | Specificare un nome per l'account di condivisione dati. |
    | | |

1. Selezionare **Rivedi e crea**, quindi **Crea** per effettuare il provisioning dell'account di condivisione dati. Il completamento del provisioning di un nuovo account di condivisione dati in genere impiega al massimo 2 minuti. 

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

### <a name="create-a-share"></a>Creare una condivisione

1. Passare alla pagina di panoramica della condivisione dati.

    ![Condividere i dati](./media/share-receive-data.png "Condividere i dati") 

1. Selezionare **Start sharing your data** (Inizia a condividere i dati).

1. Selezionare **Crea**.   

1. Immettere i dettagli della condivisione. Specificare un nome, un tipo di condivisione, una descrizione del contenuto della condivisione e le condizioni per l'utilizzo (facoltativo). 

    ![Immettere i dettagli della condivisione](./media/enter-share-details.png "Immettere i dettagli della condivisione") 

1. Selezionare **Continua**.

1. Per aggiungere set di dati alla condivisione, selezionare **Aggiungi set di dati**. 

    ![Aggiungere set di dati alla condivisione](./media/datasets.png "Set di dati")

1. Selezionare il tipo di set di dati da aggiungere. Verrà visualizzato un elenco di tipi di set di dati diverso a seconda del tipo di condivisione (snapshot o sul posto) selezionato nel passaggio precedente. 

    ![Aggiungere i set di dati](./media/add-datasets.png "Aggiungere i set di dati")    

1. Selezionare l'area di lavoro di SQL Server o sinapsi, fornire le credenziali, se richiesto, e selezionare **Avanti** per passare all'oggetto che si vuole condividere e selezionare "Aggiungi set di DataSet". È possibile selezionare le tabelle e le viste del database SQL di Azure e di Azure sinapsi Analytics (in precedenza Azure SQL DW) oppure le tabelle del pool SQL dedicato ad analisi sinapsi di Azure (area di lavoro). 

    ![Selezionare i set di dati](./media/select-datasets-sql.png "Selezionare i set di dati")    

1. Nella scheda Destinatari immettere l'indirizzo di posta elettronica del consumer di dati selezionando "+ Aggiungi il destinatario". L'indirizzo di posta elettronica deve corrispondere al messaggio di posta elettronica di accesso di Azure del destinatario.

    ![Aggiungere i destinatari](./media/add-recipient.png "Aggiungere destinatari") 

1. Selezionare **Continua**.

1. Se è stato selezionato tipo di condivisione snapshot, è possibile configurare la pianificazione degli snapshot per fornire aggiornamenti dei dati al consumer di dati. 

    ![Abilitare gli snapshot](./media/enable-snapshots.png "Abilitare gli snapshot") 

1. Selezionare un'ora di inizio e un intervallo di ricorrenza. 

1. Selezionare **Continua**.

1. Nella scheda Rivedi e crea verificare il contenuto del pacchetto, le impostazioni, i destinatari e le impostazioni di sincronizzazione. Selezionare **Crea**.

La condivisione dati di Azure è stata creata e il suo destinatario è ora pronto ad accettare l'invito. 

## <a name="receive-data"></a>Ricevere dati

### <a name="prerequisites-to-receive-data"></a>Prerequisiti per la ricezione dei dati
Prima di accettare un invito alla condivisione dei dati, è necessario effettuare il provisioning di una serie di risorse di Azure, elencate di seguito. 

Assicurarsi che tutti i prerequisiti siano soddisfatti prima di accettare un invito alla condivisione dei dati. 

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un invito alla Condivisione dati: un invito da Microsoft Azure con un oggetto denominato "Azure Data Share invitation from **<yourdataprovider@domain.com>** " (Invito alla Condivisione dati di Azure da...).
* Registrare il [provider di risorse Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) nella sottoscrizione di Azure verrà creata una risorsa Condivisione dati e nella sottoscrizione di Azure in cui si trovano gli archivi dati di Azure di destinazione.

### <a name="prerequisites-for-target-storage-account"></a>Prerequisiti per l'account di archiviazione di destinazione
Se si sceglie di ricevere i dati in archiviazione di Azure, di seguito è riportato l'elenco dei prerequisiti.

* Un account di Archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di Archiviazione di Azure](../storage/common/storage-account-create.md). 
* Autorizzazione per la scrittura nell'account di archiviazione, disponibile in *Microsoft.Storage/storageAccounts/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**. 
* Autorizzazione per aggiungere l'assegnazione di ruolo dell'identità gestita della risorsa di condivisione dati all'account di archiviazione, presente in *Microsoft. Authorization/Role assegnations/Write*. Questa autorizzazione è presente nel ruolo di **proprietario**.  

### <a name="prerequisites-for-sql-target"></a>Prerequisiti per la destinazione SQL
Se si sceglie di ricevere dati in Database SQL di Azure o Azure Synapse Analytics, ecco l'elenco di prerequisiti. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Prerequisiti per la ricezione di dati in Database SQL di Azure o Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse)
Per configurare i prerequisiti, è possibile seguire la [demo dettagliata](https://youtu.be/aeGISgK1xro).

* Un'istanza di Database SQL di Azure o di Azure Synapse Analytics (in precedenza SQL Data Warehouse).
* Autorizzazione per la scrittura nei database del server SQL, disponibile in *Microsoft.Sql/servers/databases/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**. 
* Autorizzazione per l'identità gestita della risorsa Condivisione dati per l'accesso a Database SQL di Azure o ad Azure Synapse Analytics. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare al server SQL e impostare se stessi come **amministratore di Azure Active Directory**.
    1. Connettersi all'istanza di Database SQL di Azure/Data Warehouse usando l'[editor di query](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) o SQL Server Management Studio con l'autenticazione di Azure Active Directory. 
    1. Eseguire lo script seguente per aggiungere l'identità gestita di Condivisione dati come 'db_datareader, db_datawriter, db_ddladmin'. È necessario connettersi usando Active Directory e non l'autenticazione di SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati. Se ancora non si è provveduto a creare una risorsa Condivisione dati, è possibile tornare a questo prerequisito in un secondo momento.         

* Accesso al firewall di SQL Server. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure in SQL Server passare a *Firewall e reti virtuali*
    1. Fare clic su **Sì** per *Consenti alle risorse e ai servizi di Azure di accedere a questo server*.
    1. Fare clic su **+ Aggiungi IP client**. L'indirizzo IP client è soggetto a modifiche. Potrebbe essere necessario ripetere questo processo la volta successiva che si condividono i dati SQL dal portale di Azure. È anche possibile aggiungere un intervallo di indirizzi IP.
    1. Fare clic su **Salva**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Prerequisiti per la ricezione di dati nel pool SQL di Azure Synapse Analytics (area di lavoro)

* Un pool SQL dedicato di Azure Synapse Analytics (area di lavoro). La ricezione di dati nel pool SQL serverless non è attualmente supportata.
* Autorizzazione per scrivere nel pool SQL nell'area di lavoro di Synapse, presente in *Microsoft.Synapse/workspaces/sqlPools/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**.
* Autorizzazione per l'identità gestita della risorsa Condivisione dati per l'accesso al pool SQL dell'area di lavoro di Synapse. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare all'area di lavoro di Synapse. Selezionare Amministratore SQL Active Directory nel riquadro di spostamento sinistro e impostare se stessi come **amministratore di Azure Active Directory**.
    1. Aprire Synapse Studio e selezionare *Gestisci* nel riquadro di spostamento sinistro. Selezionare *Controllo di accesso* in Sicurezza. Assegnare a se stessi il ruolo di **amministratore SQL** o **amministratore dell'area di lavoro**.
    1. In Synapse Studio selezionare *Sviluppo* nel riquadro di spostamento sinistro. Eseguire lo script seguente nel pool SQL per aggiungere l'identità gestita della risorsa Condivisione dati come 'db_datareader, db_datawriter, db_ddladmin'. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati. Se ancora non si è provveduto a creare una risorsa Condivisione dati, è possibile tornare a questo prerequisito in un secondo momento.  

* Accesso al firewall dell'area di lavoro di Synapse. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare all'area di lavoro di Synapse. Selezionare *Firewall* nel riquadro di spostamento sinistro.
    1. Fare clic su **Sì** per *Consenti alle risorse e ai servizi di Azure di accedere a questa area di lavoro*.
    1. Fare clic su **+ Aggiungi IP client**. L'indirizzo IP client è soggetto a modifiche. Potrebbe essere necessario ripetere questo processo la volta successiva che si condividono i dati SQL dal portale di Azure. È anche possibile aggiungere un intervallo di indirizzi IP.
    1. Fare clic su **Salva**. 

### <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

### <a name="open-invitation"></a>Aprire l'invito

1. È possibile aprire l'invito da un messaggio di posta elettronica o direttamente dal portale di Azure. 

   Per aprire l'invito da un messaggio di posta elettronica, controllare la posta in arrivo per verificare di aver ricevuto un invito dal provider di dati. L'invito proviene da Microsoft Azure con l'oggetto **Azure Data Share invitation from<yourdataprovider@domain.com>** (Invito alla Condivisione dati di Azure da...). Fare clic su **Visualizza invito** per visualizzare l'invito in Azure. 

   Per aprire direttamente l'invito dal portale di Azure, cercare **Inviti alla condivisione dati** nel portale di Azure. In questo modo viene visualizzato l'elenco di inviti alla condivisione dati.

   ![Elenco di inviti](./media/invitations.png "Elenco di inviti") 

1. Selezionare la condivisione che si vuole visualizzare. 

### <a name="accept-invitation"></a>Accettare l'invito
1. Assicurarsi di rivedere tutti i campi, tra cui le **condizioni per l'utilizzo**. Se si accettano le condizioni per l'utilizzo, verrà richiesto di selezionare la casella per confermare l'accettazione. 

   ![Condizioni d'uso](./media/terms-of-use.png "Condizioni per l'utilizzo") 

1. In *Target Data Share Account* (Account di condivisione dati di destinazione), selezionare la sottoscrizione e il gruppo di risorse in cui verrà distribuita la condivisione dati. 

   Nel campo **Data Share Account** (Account di condivisione dati), selezionare **Create new** (Crea nuovo) se non si ha un account di Condivisione dati esistente. In caso contrario, selezionare un account esistente di Condivisione dati in cui si vuole accettare la condivisione dei dati. 

   Nel campo **Received Share Name** (Nome condivisione ricevuta) si può lasciare il valore predefinito specificato dal provider di dati o specificare un nuovo nome per la condivisione ricevuta. 

   Dopo aver accettato le condizioni per l'utilizzo e specificato un account Condivisione dati per gestire la condivisione ricevuta, selezionare **Accept and Configure** (Accetta e configura). Verrà creata una nuova sottoscrizione di condivisione. 

   ![Opzioni di accettazione](./media/accept-options.png "Opzioni di accettazione") 

   In questo modo si passa alla condivisione ricevuta nell'account di condivisione dati. 

   Se non si vuole accettare l'invito, selezionare *Reject* (Rifiuta). 

### <a name="configure-received-share"></a>Configurare la condivisione ricevuta
Seguire questa procedura per configurare la posizione in cui si desidera ricevere i dati.

1. Selezionare la scheda **Set di dati**. Selezionare la casella accanto al set di dati a cui si vuole assegnare una destinazione. Selezionare **+ Esegui mapping alla destinazione** per scegliere un archivio dati di destinazione. 

   ![Esegui mapping alla destinazione](./media/dataset-map-target.png "Esegui mapping alla destinazione") 

1. Selezionare un archivio dati di destinazione in cui si vogliono inserire i dati. Eventuali tabelle o file di dati nell'archivio dati di destinazione con lo stesso percorso e nome verranno sovrascritti. 

   ![Account di archiviazione di destinazione](./media/dataset-map-target-sql.png "Archivio dati di destinazione") 

1. Per la condivisione basata su snapshot, se il provider di dati ha creato una pianificazione degli snapshot per fornire aggiornamenti regolari ai dati, è anche possibile abilitare la pianificazione degli snapshot selezionando la scheda **Pianificazione degli snapshot**. Selezionare la casella accanto alla pianificazione degli snapshot e selezionare **+ Abilita**.

   ![Abilitare la pianificazione degli snapshot](./media/enable-snapshot-schedule.png "Abilitare la pianificazione degli snapshot")

### <a name="trigger-a-snapshot"></a>Attivazione di uno snapshot
Questa procedura si applica solo alla condivisione basata su snapshot.

1. È possibile attivare uno snapshot selezionando la scheda **Dettagli** e quindi **Attiva snapshot**. Qui è possibile attivare uno snapshot completo o incrementale dei dati. Se è la prima volta che si ricevono i dati dal provider, selezionare Full copy (Copia completa). Per le origini SQL è supportato solo lo snapshot completo. Quando uno snapshot è in esecuzione, gli snapshot successivi non vengono avviati fino al completamento di quello precedente.

   ![Attivazione dello snapshot](./media/trigger-snapshot.png "Attivazione dello snapshot") 

1. Quando l'ultimo stato di esecuzione è *riuscito*, passare all'archivio dati di destinazione per visualizzare i dati ricevuti. Selezionare **Set di dati** e fare clic sul collegamento nel percorso di destinazione. 

   ![Set di dati consumer](./media/consumer-datasets.png "Mapping dei set di dati consumer") 

### <a name="view-history"></a>Visualizzare la cronologia
Questo passaggio si applica solo alla condivisione basata su snapshot. Per visualizzare la cronologia degli snapshot, selezionare la scheda **Cronologia**. Qui è presente una cronologia di tutti gli snapshot generati negli ultimi 30 giorni. 

## <a name="supported-data-types"></a>Tipi di dati supportati
Quando si condividono dati da un'origine SQL, vengono usati i mapping seguenti tra i tipi di dati di SQL Server e i tipi di dati provvisori della condivisione dati di Azure durante il processo di snapshot. 

| Tipo di dati di SQL Server | Tipo di dati interim della condivisione dati di Azure |
|:--- |:--- |
| bigint |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| Data |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Oggetto |
| text |String, Char[] |
| time |TimeSpan |
|  timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |string |

>[!NOTE]
> 1. Per i tipi di dati che vengono mappati al tipo provvisorio decimale, attualmente lo snapshot supporta la precisione fino a 28. Se sono presenti dati che richiedono precisione maggiore di 28, provare a eseguire la conversione in una stringa. 
> 1.  Se si condividono dati dal database SQL di Azure ad Azure sinapsi Analytics, non tutti i tipi di dati sono supportati. Per informazioni dettagliate, fare riferimento ai [tipi di dati della tabella nel pool SQL dedicato](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) . 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>Always Encrypted SQL o Dynamic Data Masking
Attualmente, la condivisione di dati di Azure non supporta i database SQL di Azure con Always Encrypted configurata. 

Per le tabelle SQL di origine con maschera dati dinamica, i dati verranno visualizzati in maschera sul lato del destinatario.

## <a name="sql-snapshot-performance"></a>Prestazioni snapshot SQL
Le prestazioni degli snapshot SQL sono influenzate da diversi fattori. È sempre consigliabile eseguire il test delle prestazioni. Di seguito sono riportati alcuni fattori di esempio che influiscono sulle prestazioni.

* Configurazione hardware (ad esempio, Vcore, Memory, DWU) dell'archivio dati SQL di origine e di destinazione. 
* Accesso simultaneo agli archivi dati di origine e di destinazione. Se si condividono più tabelle e viste dallo stesso archivio dati SQL o si ricevono più tabelle e viste nello stesso archivio dati SQL, le prestazioni saranno compromesse.   
* Percorso degli archivi dati di origine e di destinazione. 

## <a name="troubleshoot-sql-snapshot-failure"></a>Risolvere gli errori di snapshot SQL
La causa più comune dell'errore di snapshot è che la condivisione dati non dispone dell'autorizzazione per l'archivio dati di origine o di destinazione. Per concedere l'autorizzazione di condivisione dati al database SQL di Azure di origine o di destinazione o ad Azure sinapsi Analytics (in precedenza Azure SQL DW), è necessario eseguire lo script SQL specificato quando ci si connette al database SQL tramite l'autenticazione Azure Active Directory. Per risolvere un errore aggiuntivo di snapshot SQL, vedere [risolvere i problemi relativi agli errori snapshot](data-share-troubleshoot.md#snapshots).

## <a name="next-steps"></a>Passaggi successivi
Si è appreso come condividere e ricevere dati da origini SQL usando il servizio di condivisione dati di Azure. Per ulteriori informazioni sulla condivisione da altre origini dati, continuare con gli [archivi dati supportati](supported-data-stores.md).