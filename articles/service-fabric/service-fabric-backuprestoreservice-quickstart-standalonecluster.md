---
title: Backup/ripristino periodico nell'infrastruttura del servizio di Azure autonoma
description: Usare la funzionalità di backup e ripristino periodici di Service Fabric per abilitare il backup periodico dei dati delle applicazioni.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 938cbbde9f53c52350ef64715f6c61c4aa961057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526244"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Backup e ripristino periodici in un Service Fabric autonomo
> [!div class="op_single_selector"]
> * [Cluster in Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Cluster autonomi](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica lo sviluppo e la gestione di applicazioni cloud basate su microservizi distribuiti e affidabili. Consente l'esecuzione di microservizi senza stato e con stato. I servizi con stato possono mantenere lo stato modificabile e autorevole oltre alla richiesta e alla risposta o una transazione completa. Se un servizio con stato non funziona per molto tempo o perde informazioni a causa di un'emergenza, potrebbe essere necessario ripristinare un backup recente del relativo stato per poter continuare a fornire il servizio dopo il back up.

Service Fabric consente di replicare lo stato tra più nodi per assicurarsi che il servizio sia a disponibilità elevata. Anche in caso di errore di un nodo nel cluster, il servizio rimarrà quindi comunque disponibile. In alcuni casi, tuttavia, è comunque preferibile che i dati del servizio siano protetti contro errori maggiori.
 
Ad esempio, è possibile che in un servizio sia consigliabile eseguire il backup dei dati per evitare gli scenari seguenti:
- Perdita definitiva di un intero cluster di Service Fabric.
- Perdita definitiva della maggior parte delle repliche di una partizione del servizio.
- Errori amministrativi che provocano l'eliminazione o il danneggiamento accidentale dello stato. Ad esempio, un amministratore con i privilegi sufficienti elimina accidentalmente il servizio.
- Bug nel servizio che provocano il danneggiamento dei dati. Ad esempio, questo problema può verificarsi quando un aggiornamento del codice di servizio inizia a scrivere dati non corretti in una raccolta Reliable Collections. In tal caso, potrebbe essere necessario ripristinare uno stato precedente sia per il codice che per i dati.
- Elaborazione dati offline. Potrebbe essere utile eseguire offline l'elaborazione dei dati per la business intelligence separatamente dal servizio che genera i dati.

Service Fabric fornisce un'API incorporata per eseguire [backup e ripristino](service-fabric-reliable-services-backup-restore.md) in un determinato momento. Gli sviluppatori di applicazioni possono usare queste API per eseguire periodicamente il backup dello stato del servizio. Inoltre, se gli amministratori del servizio desiderano attivare un backup dall'esterno del servizio in un momento specifico, ad esempio prima di aggiornare l'applicazione, gli sviluppatori devono esporre il backup (e il ripristino) come API dal servizio. Mantenere i backup rappresenta un costo aggiuntivo ulteriore. Ad esempio, potrebbe essere necessario eseguire cinque backup incrementali ogni mezz'ora, seguiti da un backup completo. Dopo il backup completo, è possibile eliminare i backup incrementali precedenti. Questo approccio richiede un codice aggiuntivo che comporta costi ulteriori durante lo sviluppo delle applicazioni.

Il backup dei dati delle applicazioni a cadenza periodica è una necessità fondamentale per la gestione di un'applicazione distribuita e per proteggersi contro la perdita di dati o la perdita prolungata della disponibilità del servizio. Service Fabric fornisce un servizio opzionale di backup e ripristino, che consente di configurare il backup periodico dei servizi Reliable con stato (inclusi i servizi Actor) senza dover scrivere alcun codice aggiuntivo. Facilita inoltre il ripristino dei backup precedentemente eseguiti. 

Service Fabric fornisce un set di API per ottenere le seguenti funzionalità relative alle funzioni di backup e ripristino periodico:

- Pianificazione del backup periodico dei servizi Reliable con stato e Reliable Actors con supporto per il caricamento del backup in posizioni di archiviazione (esterne). Posizioni di archiviazione supportate
    - Archiviazione di Azure
    - Condivisione di file (locale)
- Enumerazione di backup
- Attivare un backup ad hoc di una partizioneTrigger an ad hoc backup of a partition
- Ripristino di una partizione utilizzando un backup precedente
- Sospensione temporanea dei backup
- Gestione della memorizzazione dei backup (a breve)

## <a name="prerequisites"></a>Prerequisiti
* Cluster di Service Fabric con Fabric versione 6.4 o successiva. Fare riferimento all'[articolo](service-fabric-cluster-creation-for-windows-server.md) per i passaggi per scaricare il pacchetto richiesto.
* Certificato X.509 per la crittografia dei dati, necessario per connettersi alla risorsa di archiviazione e archiviare i backup. Fare riferimento all’[articolo](service-fabric-windows-cluster-x509-security.md) per sapere come acquisire o come creare un certificato X.509 autofirmato.

* Applicazione Reliable di Service Fabric con informazioni sullo stato, creata utilizzando Service Fabric SDK versione 3.0 o versione successiva. Per applicazioni destinate a .net Core 2.0, l'applicazione deve essere generata utilizzando Service Fabric SDK versione 3.1 o successiva.
* Installare Microsoft.ServiceFabric.Powershell.Http Module [In Anteprima] per effettuare chiamate di configurazione.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Assicurarsi che Cluster sia `Connect-SFCluster` connesso utilizzando il comando prima di effettuare qualsiasi richiesta di configurazione utilizzando Microsoft.ServiceFabric.Powershell.Http Module.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Abilitare il servizio di backup e ripristino
È innanzitutto necessario abilitare il _servizio di backup e ripristino_ nel cluster. Ottenere il modello per il cluster che si vuole distribuire. È possibile usare i [modelli di esempio](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Per abilitare il _servizio di backup e ripristino_, seguire questa procedura:

1. Verificare che `apiversion` sia impostato su `10-2017` nel file di configurazione del cluster e, se non lo è, aggiornarlo come illustrato nel frammento seguente:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. A questo punto, abilitare il _servizio di backup e ripristino_ aggiungendo la sezione `addonFeatures`seguente sotto la sezione `properties` , come illustrato nel frammento seguente: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Configurare il certificato X.509 per la crittografia delle credenziali. Ciò è importante per assicurarsi che le eventuali credenziali fornite per connettersi alla risorsa di archiviazione siano crittografate prima di continuare. Configurare il certificato di crittografia aggiungendo la sezione `BackupRestoreService` sotto la sezione `fabricSettings` come illustrato nel frammento seguente: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Dopo avere aggiornato il file di configurazione del cluster con le modifiche precedenti, applicarle e consentire il completamento dell'aggiornamento/distribuzione. Al termine della procedura, il _servizio di backup e ripristino_ inizia l'esecuzione del cluster. L'Uri di questo servizio è `fabric:/System/BackupRestoreService` e il servizio può essere individuato in Service Fabric Explorer. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Abilita i backup periodici per servizio Reliable con stato e Reliable Actors
Seguire il procedimento per abilitare i backup periodici per servizio Reliable con stato e Reliable Actors. Questi passaggi presuppongono che
- Che il cluster sia configurato con il _servizio di backup e ripristino_.
- Un servizio Reliable con stato venga distribuito nel cluster. Ai fini della presente Guida rapida, l'applicazione Uri è `fabric:/SampleApp` e il servizio Uri per il servizio Reliable con stato appartenente a questa applicazione è `fabric:/SampleApp/MyStatefulService`. Questo servizio viene distribuito con singola partizione e l'ID di partizione è `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Creare criteri di backup

Il primo passo consiste nel creare criteri di backup che descrivano la pianificazione del backup, la risorsa di archiviazione di destinazione per i dati di backup, il nome dei criteri e i backup incrementali massimi consentiti prima dell'avvio del backup completo e i criteri di conservazione per l'archivio di backup. 

Per l'archiviazione di backup, creare la condivisione file e consentire l'accesso ReadWrite a questa condivisione file per tutti i computer di nodo del Service Fabric. Questo esempio presuppone che la condivisione con nome `BackupStore` sia presente su `StorageServer`.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell

Eseguire lo script PowerShell seguente per richiamare le API REST necessarie per creare un nuovo criterio.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

#### <a name="using-service-fabric-explorer"></a>Uso di Service Fabric ExplorerUsing Service Fabric Explorer

1. In Esplora service Fabric passare alla scheda Backup e selezionare Azioni > Crea criterio di backup.

    ![Crea criterio di backup][6]

2. Compilare le informazioni. Per i cluster autonomi, FileShare deve essere selezionato.

    ![Crea criteri di backup FileShare][7]

### <a name="enable-periodic-backup"></a>Abilitare il backup periodico
Dopo aver definito il criterio per soddisfare i requisiti di protezione dei dati dell'applicazione, il criterio di backup deve essere associato all'applicazione. A seconda del requisito, il criterio di backup può essere associato a un'applicazione, un servizio o una partizione.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell
Eseguire lo script PowerShell seguente per richiamare l'API REST necessaria per associare il criterio di backup al nome `BackupPolicy1` creato nel passaggio precedente con l'applicazione `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Uso di Service Fabric ExplorerUsing Service Fabric Explorer

1. Selezionare un'applicazione e passare all'azione. Fare clic su Abilita/Aggiorna backup applicazioni.

    ![Abilita backup applicazioni][3] 

2. Infine, selezionare il criterio desiderato e fare clic su Abilita backup.

    ![Seleziona criterio][4]

### <a name="verify-that-periodic-backups-are-working"></a>Verificare che i backup periodici funzionino

Dopo aver abilitato il backup per l'applicazione, tutte le partizioni appartenenti ai servizi Reliable con stato e Reliable Actors sotto l'applicazione inizieranno a essere sottoposti periodicamente a backup secondo il criterio di backup associato.

![Evento di integrità del backup della partizione][0]

### <a name="list-backups"></a>Elenco dei backup

I backup associati a tutte le partizioni appartenenti ai servizi Reliable con stato e agli Reliable Actors dell'applicazione possono essere enumerati utilizzando gli API _GetBackups_. A seconda del requisito, i backup possono essere enumerati per applicazione, servizio o partizione.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell con Microsoft.ServiceFabric.Powershell.Http Module

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Rest Call con Powershell

Eseguire il seguente script PowerShell per richiamare l'API HTTP ed elencare i backup creati per tutte le partizioni all'interno dell'applicazione `SampleApp`.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Esempio di output per l’esecuzione sopra indicata:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

#### <a name="using-service-fabric-explorer"></a>Uso di Service Fabric ExplorerUsing Service Fabric Explorer

Per visualizzare i backup in Esplora service Fabric, passare a una partizione e selezionare la scheda Backup.

![Enumerazione dei backup][5]

## <a name="limitation-caveats"></a>Limitazioni/avvertenze
- I cmdlet powerShell di Service Fabric sono in modalità anteprima.
- Nessun supporto per i cluster Service Fabric su Linux.

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni sulla configurazione del backup periodico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Informazioni di riferimento sull'API REST di ripristino backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png