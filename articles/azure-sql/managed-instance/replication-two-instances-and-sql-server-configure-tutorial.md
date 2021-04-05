---
title: Configurare la replica transazionale tra Istanza gestita di SQL di Azure e SQL Server
description: In questa esercitazione viene configurata la replica tra un'istanza gestita del server di pubblicazione, un'istanza gestita del server di distribuzione e un sottoscrittore di SQL Server in una macchina virtuale di Azure, insieme ai componenti di rete necessari quali una zona DNS privato e il peering reti virtuali.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/21/2019
ms.openlocfilehash: d2b45f5b51f4656294632aa46f679a7a09c06ed3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94593927"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>Esercitazione: Configurare la replica transazionale tra Istanza gestita di SQL di Azure e SQL Server
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

La replica transazionale consente di replicare i dati da un database a un altro ospitato in SQL Server o in un'[istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md). L'istanza gestita di SQL può essere un server di pubblicazione, un server di distribuzione o un sottoscrittore nella topologia di replica. Per le configurazioni disponibili, vedere [Configurazioni di replica transazionale](replication-transactional-overview.md#common-configurations). 

La replica transazionale è attualmente disponibile in anteprima pubblica per l'istanza gestita di SQL. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> - Configurare un'istanza gestita come server di pubblicazione di replica.
> - Configurare un'istanza gestita come server di distribuzione di replica.
> - Configurare un'istanza di SQL Server come sottoscrittore.

![Replica tra un server di pubblicazione dell'istanza gestita, un server di distribuzione dell'istanza gestita e un sottoscrittore di SQL Server](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

Questa esercitazione è destinata a un pubblico esperto e presuppone che l'utente abbia familiarità con la distribuzione e la connessione a entrambe le istanze gestite e con le macchine virtuali di SQL Server in Azure. 


> [!NOTE]
> Questo articolo descrive l'uso della [replica transazionale](/sql/relational-databases/replication/transactional/transactional-replication) nell'istanza gestita di SQL di Azure. Non è correlato ai [gruppi di failover](../database/auto-failover-group-overview.md), una funzionalità dell'istanza gestita di SQL di Azure che consente di creare repliche leggibili complete di singole istanze. Per la configurazione della [ replica transazionale con i gruppi di failover](replication-transactional-overview.md#with-failover-groups), è necessario tenere presenti alcune considerazioni aggiuntive.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare di avere i prerequisiti seguenti:

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Esperienza nella distribuzione di due istanze gestite all'interno della stessa rete virtuale.
- Un sottoscrittore di SQL Server, in locale o in una macchina virtuale di Azure. Questa esercitazione usa una VM di Azure.  
- [SQL Server Management Studio (SSMS) 18.0 o versione successiva](/sql/ssms/download-sql-server-management-studio-ssms).
- La versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps).
- Le porte 445 e 1433 consentono il traffico SQL in Firewall di Azure e Windows Firewall.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Usare il frammento di codice di PowerShell seguente per creare un nuovo gruppo di risorse:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="create-two-managed-instances"></a>Creare due istanze gestite

Creare due istanze gestite all'interno di questo nuovo gruppo di risorse usando il [portale di Azure](https://portal.azure.com).

- Il nome dell'istanza gestita del server di pubblicazione deve essere `sql-mi-publisher` (oltre ad alcuni caratteri per la sequenza casuale) e il nome della rete virtuale deve essere `vnet-sql-mi-publisher`.
- Il nome dell'istanza gestita del server di distribuzione deve essere `sql-mi-distributor` (oltre ad alcuni caratteri per la sequenza casuale) e deve trovarsi _nella stessa rete virtuale dell'istanza gestita del server di pubblicazione_.

   ![Usare la rete virtuale del server di pubblicazione per il server di distribuzione](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

Per altre informazioni sulla creazione di un'istanza gestita, vedere [Creare un'istanza gestita nel portale](instance-create-quickstart.md).

  > [!NOTE]
  > Per semplicità, e poiché si tratta della configurazione più comune, questa esercitazione suggerisce di inserire l'istanza del server di distribuzione nella stessa rete virtuale del server di pubblicazione. È tuttavia possibile creare il server di distribuzione in una rete virtuale distinta. A questo scopo è necessario configurare il peering reti virtuali tra la rete virtuale del server di pubblicazione e quella del server di distribuzione e quindi tra la rete virtuale del server di distribuzione e quella del sottoscrittore.

## <a name="create-a-sql-server-vm"></a>Creare una macchina virtuale di SQL Server

Creare una macchina virtuale di SQL server usando il [portale di Azure](https://portal.azure.com). La macchina virtuale di SQL Server deve avere le caratteristiche seguenti:

- Nome: `sql-vm-sub`
- Immagine: SQL Server 2016 o versione successiva
- Gruppo di risorse: uguale a quello dell'istanza gestita
- Rete virtuale: `sql-vm-sub-vnet`

Per altre informazioni sulla distribuzione di una VM di SQL Server in Azure, vedere [Avvio rapido: Creare una macchina virtuale di SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md).

## <a name="configure-vnet-peering"></a>Configurare il peering reti virtuali

Configurare il peering reti virtuali per consentire la comunicazione tra la rete virtuale delle due istanze gestite e la rete virtuale di SQL Server. A questo scopo, usare questo frammento di codice di PowerShell:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VNet peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VNet peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Una volta stabilito il peering reti virtuali, testare la connettività avviando SQL Server Management Studio (SSMS) in SQL Server e connettendosi a entrambe le istanze gestite. Per altre informazioni sulla connessione a un'istanza gestita con SSMS, vedere [Usare SSMS per connettersi all'istanza gestita di SQL](point-to-site-p2s-configure.md#connect-with-ssms).

![Testare la connettività alle istanze gestite](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="create-a-private-dns-zone"></a>Creare una zona DNS privato

Una zona DNS privato consente il routing DNS tra le istanze gestite e SQL Server.

### <a name="create-a-private-dns-zone"></a>Creare una zona DNS privato

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa** per creare una nuova risorsa di Azure.
1. Cercare `private dns zone` in Azure Marketplace.
1. Scegliere la risorsa **Zona DNS privato** pubblicata da Microsoft e quindi selezionare **Crea** per creare la zona DNS.
1. Scegliere la sottoscrizione e il gruppo di risorse dal menu a discesa.
1. Specificare un nome arbitrario per la zona DNS, ad esempio `repldns.com`.

   ![Creare la zona DNS privato](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. Selezionare **Rivedi e crea**. Rivedere i parametri della zona DNS privato e quindi selezionare **Crea** per creare la risorsa.

### <a name="create-an-a-record"></a>Creare un record A

1. Passare alla nuova **zona DNS privato** e selezionare **Panoramica**.
1. Selezionare **+ Set di record** per creare un nuovo record A.
1. Specificare il nome della VM di SQL Server oltre all'indirizzo IP interno privato.

   ![Configurare un record A](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. Selezionare **OK** per creare il record A.

### <a name="link-the-virtual-network"></a>Collegare la rete virtuale

1. Passare alla nuova **zona DNS privato** e selezionare **Collegamenti di rete virtuale**.
1. Selezionare **+ Aggiungi**.
1. Specificare un nome per il collegamento, ad esempio `Pub-link`.
1. Scegliere la sottoscrizione dal menu a discesa e quindi selezionare la rete virtuale per l'istanza gestita del server di pubblicazione.
1. Fare clic sulla casella accanto a **Abilita registrazione automatica**.

   ![Creare il collegamento di rete virtuale](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. Selezionare **OK** per collegarsi alla rete virtuale.
1. Ripetere questi passaggi per aggiungere un collegamento per la rete virtuale del server di sottoscrizione, con un nome come `Sub-link`.

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

[Creare un account di archiviazione di Azure](../../storage/common/storage-account-create.md#create-a-storage-account) per la directory di lavoro e quindi creare una [condivisione file](../../storage/files/storage-how-to-create-file-share.md) al suo interno.

Copiare il percorso della condivisione file nel formato `\\storage-account-name.file.core.windows.net\file-share-name`

Esempio: `\\replstorage.file.core.windows.net\replshare`

Copiare la stringa di connessione della chiave di accesso alle risorse di archiviazione nel formato `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Esempio: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../../storage/common/storage-account-keys-manage.md).

## <a name="create-a-database"></a>Creazione di un database

Creare un nuovo database nell'istanza gestita del server di pubblicazione. A questo scopo, attenersi alla procedura seguente:

1. Avviare SQL Server Management Studio in SQL Server.
1. Connettersi all'istanza gestita `sql-mi-publisher`.
1. Aprire una finestra **Nuova query** ed eseguire la query T-SQL seguente per creare il database.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="configure-distribution"></a>Configurare la distribuzione

Una volta stabilita la connettività e creato un database di esempio, è possibile configurare la distribuzione nell'istanza gestita `sql-mi-distributor`. A questo scopo, attenersi alla procedura seguente:

1. Avviare SQL Server Management Studio in SQL Server.
1. Connettersi all'istanza gestita `sql-mi-distributor`.
1. Aprire una finestra **Nuova query** ed eseguire il codice Transact-SQL seguente per configurare la distribuzione nell'istanza gestita del server di distribuzione:

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Assicurarsi di usare solo barre rovesciate (`\`) per il parametro @working_directory. L'uso di una barra (`/`) può generare un errore durante la connessione alla condivisione file.

1. Connettersi all'istanza gestita `sql-mi-publisher`.
1. Aprire una finestra **Nuova query** ed eseguire il codice Transact-SQL seguente per registrare il server di distribuzione nel server di pubblicazione:

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="create-the-publication"></a>Creare la pubblicazione

Una volta configurata la distribuzione, è ora possibile creare la pubblicazione. A questo scopo, attenersi alla procedura seguente:

1. Avviare SQL Server Management Studio in SQL Server.
1. Connettersi all'istanza gestita `sql-mi-publisher`.
1. In **Esplora oggetti** espandere il nodo **Replica** e fare clic con il pulsante destro del mouse sulla cartella **Local Publication**. Selezionare **Nuova pubblicazione**.
1. Selezionare **Avanti** per uscire dalla pagina iniziale.
1. Nella pagina **Database di pubblicazione** selezionare il database `ReplTutorial` creato in precedenza. Selezionare **Avanti**.
1. Nella pagina **Tipo di pubblicazione** selezionare **Pubblicazione transazionale**. Selezionare **Avanti**.
1. Nella pagina **Articoli** selezionare la casella accanto a **Tabelle**. Selezionare **Avanti**.
1. Nella pagina **Filtro righe tabella** selezionare **Avanti** senza aggiungere alcun filtro.
1. Nella pagina **Agente snapshot** selezionare la casella di controllo **Crea uno snapshot immediatamente e mantieni lo snapshot disponibile per l'inizializzazione delle sottoscrizioni**. Selezionare **Avanti**.
1. Nella pagina **Sicurezza agente** selezionare **Impostazioni di sicurezza**. Specificare le credenziali di accesso a SQL Server da usare per l'agente di snapshot e per connettersi al server di pubblicazione. Selezionare **OK** per chiudere la pagina **Sicurezza agente snapshot**. Selezionare **Avanti**.

   ![Configurare la sicurezza dell'agente di snapshot](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. Nella pagina **Azioni procedura guidata** scegliere **Crea la pubblicazione** e, facoltativamente, **Genera un file script con i passaggi per la creazione della pubblicazione**, se si vuole salvare lo script per un uso successivo.
1. Nella pagina **Completare la procedura guidata** assegnare alla pubblicazione il nome `ReplTest` e selezionare **Avanti** per creare la pubblicazione.
1. Dopo aver creato la pubblicazione, aggiornare il nodo **Replica** in **Esplora oggetti** ed espandere **Pubblicazioni locali** per visualizzare la nuova pubblicazione.

## <a name="create-the-subscription"></a>Creare la sottoscrizione

Una volta creata la pubblicazione, è possibile creare la sottoscrizione. A questo scopo, attenersi alla procedura seguente:

1. Avviare SQL Server Management Studio in SQL Server.
1. Connettersi all'istanza gestita `sql-mi-publisher`.
1. Aprire una finestra **Nuova query** ed eseguire il codice Transact-SQL seguente per aggiungere l'agente di sottoscrizione e distribuzione. Usare il DNS come parte del nome del sottoscrittore.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="test-replication"></a>Testare la replica

Dopo aver configurato la replica, è possibile testarla inserendo nuovi elementi nel server di pubblicazione e osservando le modifiche che si propagano nel sottoscrittore.

Eseguire il frammento di codice T-SQL seguente per visualizzare le righe nel sottoscrittore:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Eseguire il frammento di codice T-SQL seguente per inserire righe aggiuntive nel server di pubblicazione, quindi controllarle di nuovo nel sottoscrittore.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Pulire le risorse

1. Passare al gruppo di risorse nel [portale di Azure](https://portal.azure.com).
1. Selezionare l'istanza o le istanze gestite, quindi selezionare **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**. L'esecuzione di questa procedura in background può richiedere del tempo e, finché non viene completata, non sarà possibile eliminare il *cluster virtuale* o altre risorse dipendenti. Monitorare l'eliminazione nella scheda **Attività** per verificare che l'istanza gestita sia stata eliminata.
1. Una volta eliminata l'istanza gestita, eliminare il *cluster virtuale* selezionandolo nel gruppo di risorse e quindi scegliendo **Elimina**. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**.
1. Eliminare eventuali risorse rimanenti. Digitare `yes` nella casella di testo per confermare che si vuole eliminare la risorsa, quindi selezionare **Elimina**.
1. Eliminare il gruppo di risorse selezionando **Elimina gruppo di risorse**, digitando il relativo nome, `myResourceGroup`, quindi selezionando **Elimina**.

## <a name="known-errors"></a>Errori noti

### <a name="windows-logins-are-not-supported"></a>Gli account di accesso di Windows non sono supportati

`Exception Message: Windows logins are not supported in this version of SQL Server.`

L'agente è stato configurato con un account di accesso di Windows ed è necessario usare invece un account di accesso di SQL server. Usare la pagina **Sicurezza agente** di **Proprietà pubblicazione** per sostituire le credenziali di accesso con quelle di SQL Server.

### <a name="failed-to-connect-to-azure-storage"></a>Non è possibile connettersi ad Archiviazione di Azure

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Ottenuta la stringa di connessione di Archiviazione di Azure per replstorage 2019-11-19 02:21:05.07 Connessione ad Archiviazione di Azure '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Non è stato possibile connettersi all'archiviazione di Azure " con errore del sistema operativo: 53.

Questo problema è probabilmente dovuto al fatto che la porta 445 è chiusa in Firewall di Azure, Windows Firewall o entrambi.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

L'errore può essere causato dall'uso di una barra al posto di una barra rovesciata nel percorso della condivisione file.
  
  - Questo è il formato corretto: `\\replstorage.file.core.windows.net\replshare`
  - Questo formato può generare un errore 55 del sistema operativo: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Non è stato possibile connettersi al sottoscrittore

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Possibili soluzioni:

- Assicurarsi che la porta 1433 sia aperta.
- Assicurarsi che TCP/IP sia abilitato nel sottoscrittore.
- Verificare che sia stato usato il nome DNS quando è stato creato il sottoscrittore.
- Verificare che le reti virtuali siano correttamente collegate nella zona DNS privato.
- Verificare che il record A sia configurato correttamente.
- Verificare che il peering reti virtuali sia configurato correttamente.

### <a name="no-publications-to-which-you-can-subscribe"></a>Nessuna pubblicazione disponibile per la sottoscrizione

Quando si aggiunge una nuova sottoscrizione tramite la procedura guidata **Nuova sottoscrizione**, nella pagina **Pubblicazione** è possibile che non siano presenti database e pubblicazioni come opzioni disponibili e che venga visualizzato il messaggio di errore seguente:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

Anche se è possibile che questo messaggio di errore sia accurato e che non ci siano effettivamente pubblicazioni disponibili nel server di pubblicazione a cui si è connessi o che non si abbiano autorizzazioni sufficienti, questo errore può essere causato anche da una versione meno recente di SQL Server Management Studio. Provare a eseguire l'aggiornamento a SQL Server Management Studio 18.0 o versione successiva per escludere che sia questa la causa radice.

## <a name="next-steps"></a>Passaggi successivi

### <a name="enable-security-features"></a>Abilitare le funzionalità di sicurezza

Vedere l'articolo [Informazioni su Istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) per un elenco completo di soluzioni per proteggere il database. Sono illustrate le funzionalità di sicurezza seguenti:

- [Controllo delle istanze gestite di SQL](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Introduzione al rilevamento delle minacce](threat-detection-configure.md)
- [Maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking)
- [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security)
- [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Funzionalità delle istanze gestite di SQL

Per una panoramica completa delle funzionalità delle istanze gestite, vedere:

> [!div class="nextstepaction"]
> [Funzionalità delle istanze gestite di SQL](sql-managed-instance-paas-overview.md)