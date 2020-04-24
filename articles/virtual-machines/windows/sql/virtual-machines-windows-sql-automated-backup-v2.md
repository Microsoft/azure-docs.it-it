---
title: Backup automatico v2 per macchine virtuali SQL Server 2016/2017 in Azure | Microsoft Docs
description: Viene illustrata la funzionalità di backup automatico per macchine virtuali SQL Server 2016/2017 in esecuzione in Azure. Questo articolo si applica alle macchine virtuali che usano Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 458012982531e228f7c4968f29e79e8b2e29aa48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651433"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Backup automatico v2 per macchine virtuali in Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Backup automatico v2 configura automaticamente il [backup gestito in Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) per tutti i database nuovi ed esistenti in una macchina virtuale di Azure con SQL Server 2016/2017 Standard, Enterprise o Developer in esecuzione. In questo modo è possibile configurare i backup periodici del database che utilizzano l'archiviazione BLOB di Azure durevole. Il backup automatico v2 dipende dall'[estensione SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Prerequisiti
Per usare il backup automatico v2, esaminare i seguenti prerequisiti:

**Sistema operativo**:

- Windows Server 2012 R2
- Windows Server 2016

**Versione/edizione di SQL Server**:

- SQL Server 2016: Developer, Standard o Enterprise
- SQL Server 2017: Developer, Standard o Enterprise

> [!IMPORTANT]
> Backup automatico v2 funziona con SQL Server 2016 o versioni successive. Se si usa SQL Server 2014, è possibile utilizzare Backup automatico v1 per eseguire il backup dei database. Per altre informazioni, vedere [Backup automatico per SQL Server 2014 in Macchine virtuali di Azure](virtual-machines-windows-sql-automated-backup.md).

**Configurazione del database**:

- I database di destinazione devono usare il modello di recupero con registrazione completa. Per altre informazioni sull'impatto del modello di recupero con registrazione completa sui backup, vedere [Backup con il modello di recupero con registrazione completa](https://technet.microsoft.com/library/ms190217.aspx).
- I database di sistema non devono necessariamente usare un modello di recupero con registrazione completa. Tuttavia, se è necessario effettuare il backup dei log per Model o MSDB, deve essere usato il modello di recupero con registrazione completa.
- I database di destinazione devono trovarsi nell'istanza predefinita di SQL Server o in un'istanza denominata [installata correttamente](virtual-machines-windows-sql-server-iaas-faq.md#administration) . 

> [!NOTE]
> Il backup automatico si basa sull' **estensione SQL Server agente IaaS**. Per impostazione predefinita, le attuali immagini della raccolta di macchine virtuali di SQL aggiungono questa estensione. Per altre informazioni, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Impostazioni
Nella seguente tabella sono descritte le opzioni che possono essere configurate per il backup automatico v2. I passaggi di configurazione effettivi variano a seconda che venga usato il portale di Azure o i comandi di Windows PowerShell di Azure.

### <a name="basic-settings"></a>Basic Settings

| Impostazione | Intervallo (impostazione predefinita) | Descrizione |
| --- | --- | --- |
| **Backup automatico** | Enable/Disable (disabilitato) | Abilita o disabilita il backup automatico per una macchina virtuale di Azure in cui viene eseguito SQL Server 2016/2017 Developer, Standard o Enterprise. |
| **Periodo di memorizzazione** | 1-30 giorni (30 giorni) | Numero di giorni di conservazione dei backup. |
| **Storage Account** | Account di archiviazione di Azure | Account di archiviazione di Azure da usare per archiviare i file del backup automatico nell'archiviazione BLOB. In questa posizione viene creato un contenitore per archiviare tutti i file di backup. La convenzione di denominazione dei file di backup include la data, l'ora e il GUID database. |
| **Crittografia** |Enable/Disable (disabilitato) | Abilita o disabilita la crittografia. Quando è abilitata la crittografia, i certificati usati per ripristinare il backup sono contenuti nell'account di archiviazione specificato USA lo stesso contenitore di **backup automatico** con la stessa convenzione di denominazione. Se la password viene modificata, viene generato un nuovo certificato con tale password, ma il certificato precedente viene mantenuto per ripristinare i backup precedenti. |
| **Password** |Testo della password | Password per le chiavi di crittografia. Questa password è necessaria solo se la crittografia è abilitata. Per ripristinare un backup crittografato, è necessario disporre della password corretta e del certificato correlato usato al momento dell'esecuzione del backup. |

### <a name="advanced-settings"></a>Impostazioni avanzate

| Impostazione | Intervallo (impostazione predefinita) | Descrizione |
| --- | --- | --- |
| **Backup del database di sistema** | Enable/Disable (disabilitato) | Quando abilitata, questa funzionalità esegue inoltre il backup dei database di sistema: Master, MSDB e Model. Per i database MSDB e Model, verificare che siano in modalità di ripristino completo se si desidera eseguire il backup dei log. Non vengono mai eseguiti backup di log per Master, né backup di alcun tipo per TempDB. |
| **Pianificazione backup** | Manual/Automated (Automated) (Manuale/Automatizzato - Automatizzato) | Per impostazione predefinita, la pianificazione del backup è determinata automaticamente in base all'aumento delle dimensioni dei log. Una pianificazione manuale del backup consente all'utente di specificare l'intervallo di tempo per i backup. In questo caso, i backup viene eseguiti unicamente con la frequenza e nell'intervallo di tempo specificati per il giorno in questione. |
| **Frequenza backup completo** | Giornaliera/settimanale | Frequenza dei backup completi. In entrambi i casi, i backup completi iniziano nell'intervallo di tempo pianificato successivo. Quando si seleziona la frequenza settimanale, i backup potrebbero durare più giorni fino ad aver incluso tutti i database. |
| **Ora di inizio backup completo** | 00:00 – 23:00 (01:00) | Ora di inizio di un determinato giorno in cui possono avere luogo i backup completi. |
| **Intervallo di tempo del backup completo** | 1-23 ore (1 ora) | Intervallo di tempo di un determinato giorno in cui possono avere luogo i backup completi. |
| **Frequenza di backup del log** | 5-60 minuti (60 minuti) | Frequenza dei backup dei log. |

## <a name="understanding-full-backup-frequency"></a>Informazioni sulla frequenza dei backup completi
È importante comprendere la differenza tra i backup completi giornalieri e settimanali. Esaminare i due scenari di esempio seguenti.

### <a name="scenario-1-weekly-backups"></a>Scenario 1: Backup settimanali
Si dispone di una macchina virtuale di SQL Server che contiene una serie di database di grandi dimensioni.

Il lunedì si abilita Backup automatico v2 con le seguenti impostazioni:

- Pianificazione backup: **manuale**
- Frequenza backup completo: **Settimanale**
- Ora di inizio backup completo: **01:00**
- Intervallo di tempo dei backup completi: **1 ora**

Ciò significa che l'intervallo di tempo disponibile successivo per il backup sarà martedì alle ore 01:00 per 1 ora. In quel momento, Backup automatico inizia a eseguire il backup dei database, uno alla volta. In questo scenario, date le dimensioni dei database, il tempo è sufficiente per eseguire il backup dei primi due. Passata l'ora, tuttavia, il backup non è stato eseguito per tutti i database.

In questo caso, Backup automatico inizierà a eseguire il backup dei database rimanenti il giorno dopo, ovvero mercoledì, sempre alle ore 01:00 per un'ora. Se anche dopo quell'intervallo di tempo non è stato eseguito il backup di tutti i database, Backup automatico riprova il giorno successivo, e così via fino a quando non viene stato eseguito il backup di tutti i database.

Il martedì successivo, Backup automatico comincia nuovamente il backup di tutti i database.

In questo scenario, Backup automatico opera unicamente nell'intervallo di tempo specificato e viene eseguito il backup di ogni database una volta alla settimana. Si noti anche come sia possibile distribuire i backup su più giorni nel caso in cui un unico giorno non sia sufficiente per completarli.

### <a name="scenario-2-daily-backups"></a>Scenario 2: Backup giornalieri
Si dispone di una macchina virtuale di SQL Server che contiene una serie di database di grandi dimensioni.

Il lunedì si abilita Backup automatico v2 con le seguenti impostazioni:

- Pianificazione backup: Manuale
- Frequenza backup completo: Ogni giorno
- Ora di inizio backup completo: 22:00
- Intervallo di tempo dei backup completi: 6 ore

Ciò significa che l'intervallo di tempo disponibile successivo per il backup sarà lunedì alle ore 22:00 per 6 ore. In quel momento, Backup automatico inizia a eseguire il backup dei database, uno alla volta.

Il backup viene quindi eseguito nuovamente martedì alle 22:00 per 6 ore.

> [!IMPORTANT]
> Quando si pianificano i backup giornalieri, si consiglia di scegliere un intervallo di tempo ampio che sia sufficiente per il backup di tutti i database. Questo aspetto è particolarmente importante nel caso in cui si debba eseguire il backup di una grande quantità di dati.

## <a name="configure-new-vms"></a>Configurare le nuove macchine virtuali

Usare il portale di Azure per configurare Backup automatico v2 quando si crea una nuova macchina virtuale di SQL Server 2016 o 2017 nel modello di distribuzione Resource Manager.

Nella scheda **impostazioni SQL Server** selezionare **Abilita** in **backup automatico**. Nella seguente schermata del portale di Azure vengono mostrate le impostazioni del **Backup automatico di SQL**.

![Configurazione del backup automatico di SQL nel Portale di Azure](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> L'opzione Backup automatico v2 è disabilitata per impostazione predefinita.

## <a name="configure-existing-vms"></a>Configurare le macchine virtuali esistenti

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Per le macchine virtuali SQL Server esistenti, passare alla [risorsa macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e quindi selezionare **backup** per configurare i backup automatici.

![Backup automatico di SQL per le VM esistenti](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


Al termine, fare clic sul pulsante **applica** nella parte inferiore della pagina impostazioni **backup** per salvare le modifiche.

Se si intende abilitare il backup automatico per la prima volta, Azure configura l'agente IaaS di SQL Server in background. Durante questo periodo, nel portale di Azure potrebbe non essere visualizzata l'informazione relativa alla configurazione del backup automatico. Attendere alcuni minuti per l'installazione e la configurazione dell'agente. A questo punto, nel portale di Azure verranno visualizzate le nuove impostazioni.

## <a name="configure-with-powershell"></a>Configurare con PowerShell

È possibile usare PowerShell per configurare Backup automatico v2. Prima di iniziare, è necessario eseguire queste operazioni:

- [Scaricare e installare la Azure PowerShell più recente](https://aka.ms/webpi-azps).
- Aprire Windows PowerShell e associarlo al proprio account con il comando **Connect-AzAccount**.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Installare l'estensione di SQL IaaS
Se è stato eseguito il provisioning di una macchina virtuale di SQL Server dal portale di Azure, l'estensione di SQL Server IaaS dovrebbe già essere installata. È possibile verificare se l'estensione è già stata installata per la macchina virtuale eseguendo il comando **Get-AzVM** ed esaminando la proprietà **Extensions**.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Se l'estensione Agente IaaS di SQL Server è installata, verrà visualizzata come "SqlIaaSAgent" o "SQLIaaSExtension". La proprietà **ProvisioningState** per l'estensione deve inoltre mostrare lo stato "Succeeded". 

Nel caso in cui l'estensione non sia installata o non ne sia stato eseguito il provisioning, è possibile installarla con il comando seguente. Oltre al nome della macchina virtuale e al gruppo di risorse, è necessario anche specificare l'area (**$region**) in cui si trova la macchina virtuale.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Verificare le impostazioni correnti
Se si è abilitato il backup automatico durante il provisioning, è possibile usare PowerShell per verificare la configurazione corrente. Eseguire il comando **Get-AzVMSqlServerExtension** ed esaminare la proprietà **AutoBackupSettings**:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

L'output dovrebbe essere simile al seguente:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Se l'output indica che **Enable** è impostato su **False**, è necessario abilitare il backup automatico. I passaggi per farlo sono identici a quelli per abilitare e configurare Backup automatico. Per altre informazioni, vedere la sezione seguente.

> [!NOTE] 
> Se si verificano le impostazioni subito dopo una modifica, è possibile che vengano visualizzati i valori precedenti. Attendere alcuni minuti e verificare nuovamente le impostazioni per assicurarsi che siano state applicate le modifiche.

### <a name="configure-automated-backup-v2"></a>Configurare Backup automatico v2
È possibile usare PowerShell per abilitare Backup automatico e per modificarne configurazione e comportamento in qualsiasi momento. 

Per prima cosa, selezionare o creare un account di archiviazione per i file di backup. Lo script seguente consente di selezionare un account di archiviazione o di crearne uno.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Backup automatico non supporta l'archiviazione di backup nell'archiviazione Premium, ma può ottenere i backup da dischi di macchine virtuali che usano l'archiviazione Premium.

Eseguire quindi il comando **New-AzVMSqlServerAutoBackupConfig** per abilitare e configurare le impostazioni di Backup automatizzato v2 per archiviare i backup nell'account di archiviazione di Azure. In questo esempio, viene impostata su 10 giorni la conservazione dei backup. I backup del database di sistema sono abilitati. I backup completi sono pianificati come settimanali, con un intervallo di tempo di due ore a partire dalle 20:00. I backup del log vengono eseguiti ogni 30 minuti. Il secondo comando, **Set-AzVMSqlServerExtension**, aggiorna la macchina virtuale di Azure specificata con queste impostazioni.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Potrebbero essere necessari diversi minuti per installare e configurare l'agente IaaS di SQL Server. 

Per abilitare la crittografia, modificare lo script precedente in modo da passare il parametro **EnableEncryption** e una password (stringa sicura) per il parametro **CertificatePassword**. Il seguente script abilita le impostazioni del backup automatico nell'esempio precedente e aggiunge la crittografia.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Per confermare l'applicazione delle impostazioni, [verificare la configurazione di Backup automatico](#verifysettings).

### <a name="disable-automated-backup"></a>Disabilitare Backup automatico
Per disabilitare Backup automatizzato, eseguire lo stesso script senza il parametro **-Enable** per il comando **New-AzVMSqlServerAutoBackupConfig**. L'assenza del parametro **-Enable** segnala il comando per disabilitare la funzionalità. Come per l'installazione, la disabilitazione del backup automatico può richiedere alcuni minuti.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script di esempio
Lo script seguente fornisce un set di variabili da personalizzare per abilitare e configurare Backup automatico per la propria macchina virtuale. In questo caso, potrebbe essere necessario personalizzare lo script in base alle esigenze. Ad esempio, potrebbe essere necessario apportare modifiche se si desidera disabilitare il backup dei database di sistema o abilitare la crittografia.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitoraggio

Per monitorare Backup automatico in SQL Server 2016/2017 esistono due opzioni principali. Poiché Backup automatico usa la funzionalità di Backup gestito di SQL Server, le stesse tecniche di monitoraggio sono valide per entrambi.

In primo luogo, è possibile eseguire il polling dello stato chiamando [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). In alternativa, eseguire una query su [msdb. managed_backup. fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) funzione con valori di tabella.

È inoltre possibile sfruttare la funzionalità incorporata di posta elettronica database per le notifiche.

1. Chiamare la stored procedure [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) per assegnare un indirizzo di posta elettronica al parametro **SSMBackup2WANotificationEmailIds**. 
1. Abilitare [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) per inviare i messaggi di posta elettronica dalla macchina virtuale di Azure.
1. Usare il nome utente e il server SMTP per configurare la posta elettronica database. È possibile configurare la posta elettronica database in SQL Server Management Studio o con i comandi Transact-SQL. Per altre informazioni, vedere [Posta elettronica database](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Configurare SQL Server Agent per usare la posta elettronica database](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Verificare che la porta SMTP sia consentita sia attraverso il firewall della macchina virtuale locale sia nel gruppo di sicurezza di rete per la macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
Backup automatico v2 configura backup gestito in Macchine virtuali di Azure. Pertanto è importante [esaminare la documentazione per il backup gestito](https://msdn.microsoft.com/library/dn449496.aspx) per comprendere il comportamento e le implicazioni.

È possibile trovare maggiori informazioni sul backup e sul ripristino per SQL Server in macchine virtuali di Azure nell'articolo seguente: [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

Per informazioni sulle altre attività di automazione disponibili, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Per altre informazioni sull'esecuzione di SQL Server nelle VM di Azure, vedere [Panoramica di SQL Server nelle macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

