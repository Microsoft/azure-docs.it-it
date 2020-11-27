---
title: 'PowerShell: eseguire la migrazione di SQL Server a SQL Istanza gestita'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione da SQL Server ad Azure SQL Istanza gestita tramite Azure PowerShell e il servizio migrazione del database di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: aa97fa5e92d4afe11cf8af5f7cfd3458a3c12014
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302730"
---
# <a name="migrate-sql-server-to-sql-managed-instance-with-powershell--azure-database-migration-service"></a>Eseguire la migrazione di SQL Server a SQL Istanza gestita con PowerShell & servizio migrazione del database di Azure

In questo articolo viene eseguita la migrazione del database **Adventureworks2016** ripristinato in un'istanza locale di SQL Server 2005 o versione successiva a una istanza gestita SQL di Azure sql utilizzando Microsoft Azure PowerShell. È possibile eseguire la migrazione dei database da un'istanza di SQL Server a una Istanza gestita SQL usando il `Az.DataMigration` modulo in Microsoft Azure PowerShell.

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
>
> * Creare un gruppo di risorse.
> * Creare un'istanza del servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione in un'istanza del servizio migrazione del database di Azure.
> * Eseguire la migrazione.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo include informazioni dettagliate su come eseguire migrazioni in linea e non in linea.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi è necessario disporre di:

* [SQL Server 2016 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads) (qualsiasi edizione).
* Una copia locale del database **AdventureWorks2016** , disponibile per il download [qui](/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Per abilitare il protocollo TCP/IP che è disabilitato per impostazione predefinita con l'installazione di SQL Server Express. Abilitare il protocollo TCP/IP seguendo l'articolo [Abilitare o disabilitare un protocollo di rete server](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Per configurare [Windows Firewall per l'accesso al motore di database](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Istanza gestita SQL. È possibile creare un Istanza gestita SQL seguendo le istruzioni riportate nell'articolo [creare un istanza gestita ASQL](../azure-sql/managed-instance/instance-create-quickstart.md).
* Per scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) versione 3.3 o successiva.
* Un Rete virtuale di Microsoft Azure creato usando il modello di distribuzione Azure Resource Manager, che fornisce il servizio migrazione del database di Azure con connettività da sito a sito ai server di origine locali tramite [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Una valutazione completata del database locale e della migrazione dello schema usando Data Migration Assistant, come descritto nell'articolo [esecuzione di una valutazione della migrazione del SQL Server](/sql/dma/dma-assesssqlonprem).
* Per scaricare e installare il `Az.DataMigration` modulo (versione 0.7.2 o successiva) dal PowerShell Gallery usando il [cmdlet di PowerShell Install-Module](/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Per assicurarsi che le credenziali utilizzate per connettersi all'istanza di SQL Server di origine dispongano dell'autorizzazione [Control Server](/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Per assicurarsi che le credenziali utilizzate per la connessione a SQL Istanza gestita di destinazione dispongano dell'autorizzazione CONTROL DATABASE sui database SQL Istanza gestita di destinazione.

    > [!IMPORTANT]
    > Per le migrazioni online, è necessario avere già configurato le credenziali Azure Active Directory. Per altre informazioni, vedere l'articolo [usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Accedere alla sottoscrizione di Microsoft Azure

Accedere alla sottoscrizione di Azure usando PowerShell. Per altre informazioni, vedere l'articolo [accedere con Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Per creare un gruppo di risorse, usare il [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando.

Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti orientali* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Creare un'istanza del Servizio Migrazione del database di Azure

È possibile creare una nuova istanza del Servizio Migrazione del database di Azure tramite il cmdlet `New-AzDataMigrationService`.
Questo cmdlet si aspetta i parametri obbligatori seguenti:

* *Nome del gruppo di risorse di Azure*. È possibile usare il [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando per creare un gruppo di risorse di Azure, come illustrato in precedenza, e specificarne il nome come parametro.
* *Nome del servizio*. Stringa che corrisponde al nome del servizio univoco desiderato per il servizio migrazione del database di Azure.
* *Località*. Specifica il percorso del servizio. Specificare una località di Azure data center, ad esempio Stati Uniti occidentali o Asia sud-orientale.
* *SKU*. Questo parametro corrisponde al nome Sku DMS. I nomi degli SKU attualmente supportati sono *Basic_1vCore*, *Basic_2vCores* *GeneralPurpose_4vCores*.
* *Identificatore della subnet virtuale*. È possibile utilizzare il cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) per creare una subnet.

L'esempio seguente crea un servizio denominato *mydms* nel gruppo di risorse *MyDMSResourceGroup* che si trova nell'area *Stati Uniti orientali* usando una rete *virtuale denominata* *MyVNET* e una subnet denominata subnet.

> [!IMPORTANT]
> Il frammento di codice riportato di seguito è per una migrazione offline, che non richiede un'istanza del servizio migrazione del database di Azure in base a uno SKU Premium. Per una migrazione in linea, il valore del parametro-SKU deve includere uno SKU Premium.

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del Servizio Migrazione del database di Azure, creare un progetto di migrazione. Un progetto Servizio Migrazione del database di Azure richiede informazioni di connessione per entrambe le istanze di origine e di destinazione, nonché un elenco dei database che si desidera migrare come parte del progetto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Creare un oggetto informazioni di connessione del database per le connessioni di origine e di destinazione

È possibile creare un oggetto informazioni di connessione del database usando il `New-AzDmsConnInfo` cmdlet, che prevede i parametri seguenti:

* *ServerType*. Il tipo di connessione al database richiesta, ad esempio SQL, Oracle o MySQL. Usare SQL per SQL Server e SQL Azure.
* *Origine dati*. Il nome o l'indirizzo IP di un'istanza di SQL Server o di un database SQL di Azure.
* *AuthType*. Il tipo di autenticazione per la connessione, che può essere SqlAuthentication o WindowsAuthentication.
* *TrustServerCertificate*. Questo parametro imposta un valore che indica se il canale è crittografato ignorando l'analisi della catena di certificati per convalidare l'attendibilità. Il valore può essere `$true` o `$false` .

Nell'esempio seguente viene creato un oggetto informazioni di connessione per un SQL Server di origine denominato *MySourceSQLServer* usando l'autenticazione SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Nell'esempio seguente viene illustrata la creazione di informazioni di connessione per una Istanza gestita SQL di Azure denominata ' targetmanagedinstance ':

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Fornire i database per il progetto di migrazione

Creare un elenco di `AzDataMigrationDatabaseInfo` oggetti che specifichi i database come parte del progetto del servizio migrazione del database di Azure, che può essere fornito come parametro per la creazione del progetto. È possibile utilizzare il cmdlet `New-AzDataMigrationDatabaseInfo` per creare `AzDataMigrationDatabaseInfo` .

Nell'esempio seguente viene creato il `AzDataMigrationDatabaseInfo` progetto per il database **AdventureWorks2016** e questo viene aggiunto all'elenco per essere specificato come parametro per la creazione del progetto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Creare un oggetto progetto

Infine, è possibile creare un progetto di servizio migrazione del database di Azure denominato *MyDMSProject* che si trova negli *Stati Uniti orientali* usando `New-AzDataMigrationProject` e aggiungere le connessioni di origine e di destinazione create in precedenza e l'elenco dei database di cui eseguire la migrazione.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Creare e avviare un'attività di migrazione

Successivamente, creare e avviare un'attività del servizio migrazione del database di Azure. Questa attività richiede le informazioni sulle credenziali di connessione per l'origine e la destinazione, nonché l'elenco delle tabelle di database di cui eseguire la migrazione e le informazioni già fornite con il progetto creato come prerequisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Creare i parametri delle credenziali per l'origine e la destinazione

Creare credenziali di sicurezza della connessione come oggetto [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) .

Nell'esempio seguente viene illustrata la creazione di oggetti *PSCredential* per le connessioni di origine e di destinazione, fornendo password come variabili stringa *$SourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Creazione di un oggetto FileShare di backup

Creare ora un oggetto FileShare che rappresenta la condivisione di rete SMB locale a cui il servizio migrazione del database di Azure può eseguire i backup del database di origine utilizzando il `New-AzDmsFileShare` cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Creare l'oggetto di database selezionato

Il passaggio successivo consiste nel selezionare i database di origine e di destinazione utilizzando il `New-AzDmsSelectedDB` cmdlet.

Nell'esempio seguente viene eseguita la migrazione di un singolo database da SQL Server a un Istanza gestita SQL di Azure:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Se un'istanza di SQL Server intera necessita di un lift-and-Shift in una Istanza gestita SQL di Azure, viene fornito un ciclo per eseguire tutti i database dall'origine. Nell'esempio seguente, per $Server, $SourceUserName e $SourcePassword, fornire i dettagli dell'SQL Server di origine.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>URI di firma di accesso condiviso per il contenitore di Archiviazione di Azure

Creare variabili che contengono l'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione in cui il servizio caricherà i file di backup per la migrazione.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Il servizio migrazione del database di Azure non supporta l'uso di un token SAS a livello di account. È necessario usare un URI di firma di accesso condiviso per il contenitore dell'account di archiviazione. [Informazioni su come ottenere l'URI di firma di accesso condiviso per un contenitore BLOB](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>Requisiti di configurazione aggiuntivi

Ci sono alcuni requisiti aggiuntivi che è necessario risolvere, ma variano a seconda che si stia eseguendo una migrazione offline o online.

#### <a name="offline-migrations"></a>Migrazioni offline

Solo per le migrazioni offline, eseguire le attività di configurazione aggiuntive seguenti.

* **Selezionare account di accesso**. Creare un elenco di account di accesso da migrare, come illustrato nell'esempio seguente:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Attualmente, il servizio migrazione del database di Azure supporta solo la migrazione di account di accesso SQL.

* **Selezionare processi di Agent**. Creare un elenco di processi di Agent da migrare, come illustrato nell'esempio seguente:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Attualmente, il servizio migrazione del database di Azure supporta solo i processi con i passaggi del processo del sottosistema T-SQL.

#### <a name="online-migrations"></a>Migrazioni online

Solo per le migrazioni online, eseguire le seguenti attività di configurazione aggiuntive.

* **Configurare Azure Active Directory app**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Configurare la risorsa di archiviazione**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Creare e avviare l'attività di migrazione

Usare il cmdlet `New-AzDataMigrationTask` per creare e avviare un'attività di migrazione.

#### <a name="specify-parameters"></a>Specificare i parametri

##### <a name="common-parameters"></a>Parametri comuni

Indipendentemente dal fatto che si stia eseguendo una migrazione offline o online, il `New-AzDataMigrationTask` cmdlet prevede i parametri seguenti:

* *TaskType*. Tipo di attività di migrazione da creare per la SQL Server di Azure SQL Istanza gestita tipo di migrazione *MigrateSqlServerSqlDbMi* è previsto. 
* *Nome del gruppo di risorse*. Nome del gruppo di risorse di Azure in cui creare l'attività.
* *ServiceName*. Istanza del Servizio Migrazione del database di Azure in cui creare l'attività.
* *NomeProgetto*. Nome del progetto di migrazione del Servizio Migrazione del database di Azure in cui creare l'attività. 
* *TaskName*. Nome dell'attività da creare. 
* *SourceConnection*. Oggetto AzDmsConnInfo che rappresenta la connessione SQL Server di origine.
* *TargetConnection*. Oggetto AzDmsConnInfo che rappresenta la connessione Istanza gestita SQL di Azure di destinazione.
* *SourceCred*. Oggetto [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) per la connessione al server di origine.
* *TargetCred*. Oggetto [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) per la connessione al server di destinazione.
* *SelectedDatabase*. Oggetto AzDataMigrationSelectedDB che rappresenta il mapping del database di origine e di destinazione.
* *BackupFileShare*. L'oggetto di condivisione di rete che rappresenta la condivisione di rete locale dove il Servizio Migrazione del database di Azure può salvare i backup del database di origine.
* *BackupBlobSasUri*. L'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione in cui il servizio caricherà i file di backup. Informazioni su come ottenere l'URI di firma di accesso condiviso per un contenitore BLOB.
* *SelectedLogins*. L'elenco di account di accesso selezionato per eseguire la migrazione.
* *SelectedAgentJobs*. L'elenco di processi dell'agente selezionati per eseguire la migrazione.

##### <a name="additional-parameters"></a>Parametri aggiuntivi

Il `New-AzDataMigrationTask` cmdlet prevede anche parametri univoci per il tipo di migrazione, offline o online, che si sta eseguendo.

* **Migrazioni offline**. Per le migrazioni offline, il `New-AzDataMigrationTask` cmdlet prevede anche i parametri seguenti:

  * *SelectedLogins*. L'elenco di account di accesso selezionato per eseguire la migrazione.
  * *SelectedAgentJobs*. L'elenco di processi dell'agente selezionati per eseguire la migrazione.

* **Migrazioni online**. Per le migrazioni online, il `New-AzDataMigrationTask` cmdlet prevede anche i parametri seguenti.

* *AzureActiveDirectoryApp*. Active Directory applicazione.
* *StorageResourceID*. ID risorsa dell'account di archiviazione.

#### <a name="create-and-start-an-offline-migration-task"></a>Creare e avviare un'attività di migrazione offline

Nell'esempio seguente viene creata e avviata un'attività di migrazione offline denominata **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Creare e avviare un'attività di migrazione in linea

Nell'esempio seguente viene creata e avviata un'attività di migrazione in linea denominata **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Monitorare la migrazione

Per monitorare la migrazione, eseguire le operazioni seguenti.

1. Consolidare tutti i dettagli della migrazione in una variabile denominata $CheckTask.

    Per combinare i dettagli della migrazione, ad esempio le proprietà, lo stato e le informazioni del database associati alla migrazione, usare il frammento di codice seguente:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Utilizzare la `$CheckTask` variabile per ottenere lo stato corrente dell'attività di migrazione.

    Per utilizzare la `$CheckTask` variabile per ottenere lo stato corrente dell'attività di migrazione, è possibile monitorare l'attività di migrazione in esecuzione eseguendo una query sulla proprietà state dell'attività, come illustrato nell'esempio seguente:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Esecuzione di cutover (solo migrazioni online)

Con una migrazione in linea, viene eseguito un backup completo e un ripristino dei database e quindi il lavoro continua a ripristinare i log delle transazioni archiviati in BackupFileShare.

Quando il database in un Istanza gestita SQL di Azure viene aggiornato con i dati più recenti ed è sincronizzato con il database di origine, è possibile eseguire un cutover.

Nell'esempio seguente viene completato il cutover\migration. Gli utenti richiamano questo comando a propria discrezione.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Eliminazione dell'istanza del servizio migrazione del database di Azure

Al termine della migrazione, è possibile eliminare l'istanza del servizio migrazione del database di Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Risorse aggiuntive

Per informazioni sugli scenari di migrazione aggiuntivi (coppie di origine/destinazione), vedere la [Guida alla migrazione di database](https://datamigration.microsoft.com/)Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio migrazione del database di Azure, vedere l'articolo [che cos'è il servizio migrazione del database di Azure?](./dms-overview.md).