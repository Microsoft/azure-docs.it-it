---
title: Usare Azure PowerShell per configurare il caricamento dei file | Microsoft Docs
description: Come usare i cmdlet di Azure PowerShell per configurare l'hub IoT per abilitare i caricamenti di file da dispositivi connessi. Include informazioni sulla configurazione dell'account di archiviazione di Azure di destinazione.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: e2b106e64aed08b0586575d4d77602329454a673
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536012"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Configurare i caricamenti dei file nell'hub IoT con PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Per usare la [funzionalità di caricamento di file nell'hub IoT](iot-hub-devguide-file-upload.md), è prima di tutto necessario associare un account di archiviazione di Azure all'hub IoT. È possibile usare un account di archiviazione esistente o crearne uno nuovo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* [Cmdlet di Azure PowerShell](/powershell/azure/install-Az-ps).

* Un hub IoT di Azure. Se non si ha un hub Internet delle cose, è possibile usare il [cmdlet New-AzIoTHub](/powershell/module/az.iothub/new-aziothub) per crearne uno o usare il portale per [creare un hub](iot-hub-create-through-portal.md)Internet.

* Un account di archiviazione di Azure. Se non si dispone di un account di archiviazione di Azure, è possibile usare i [cmdlet PowerShell di Archiviazione di Azure](/powershell/module/az.storage/) per crearne uno o usare il portale per [creare un account di archiviazione](../storage/common/storage-account-create.md)

## <a name="sign-in-and-set-your-azure-account"></a>Accedere all'account Azure e impostarlo

Accedere al proprio account Azure e selezionare la sottoscrizione.

1. Al prompt di PowerShell eseguire il cmdlet **Connect-AzAccount** :

    ```powershell
    Connect-AzAccount
    ```

2. Se si usano più sottoscrizioni Azure e si esegue l'accesso ad Azure, è possibile accedere a tutte le sottoscrizioni di Azure associate alle credenziali. Usare il comando seguente per elencare gli account Azure che è possibile usare:

    ```powershell
    Get-AzSubscription
    ```

    Usare il comando seguente per selezionare la sottoscrizione che si vuole usare per eseguire i comandi per gestire l'hub IoT. È possibile usare il nome o l'ID della sottoscrizione dall'output del comando precedente:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Recuperare i dettagli dell'account di archiviazione

I passaggi seguenti presuppongono che l'account di archiviazione sia stato creato tramite il modello di distribuzione di **Resource Manager** e non tramite quello **Classico**.

Per configurare i caricamenti dei file dai propri dispositivi, è necessario disporre della stringa di connessione di un account di Archiviazione di Azure. L'account di archiviazione deve trovarsi nella stessa sottoscrizione dell'hub IoT. È inoltre necessario il nome del contenitore BLOB nell'account di archiviazione. Usare il comando seguente per recuperare le chiavi dell'account di archiviazione:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Prendere nota del valore della chiave dell'account di archiviazione **key1**. sarà necessario nei passaggi successivi.

Per i caricamenti dei file, è possibile usare un contenitore BLOB esistente oppure crearne uno nuovo:

* Per elencare i contenitori BLOB esistente nell'account di archiviazione, usare i comandi seguenti:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Per creare un contenitore BLOB nell'account di archiviazione, usare i comandi seguenti:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Configurare l'hub IoT

È ora possibile configurare l'hub IoT per [caricare i file nell'hub IoT](iot-hub-devguide-file-upload.md) usando i dettagli dell'account di archiviazione.

La configurazione richiede i valori seguenti:

* **Contenitore di archiviazione**: un contenitore BLOB in un account di archiviazione di Azure nella sottoscrizione corrente da associare all'hub IoT. Le informazioni necessarie sull'account di archiviazione sono state recuperate nella sezione precedente. L'hub IoT genera automaticamente URI di firma di accesso condiviso con autorizzazioni di scrittura per questo contenitore BLOB che possono essere usati dai dispositivi durante il caricamento di file.

* **Receive notifications for uploaded files** (Ricezione di notifiche per i file caricati): abilitare o disabilitare le notifiche di caricamento del file.

* **SAS TTL**(TTL di firma di accesso condiviso): questa impostazione indica la durata degli URI di firma di accesso condiviso restituiti dal dispositivo tramite l’hub IoT. Il valore è un'ora per impostazione predefinita.

* **File notification settings default TTL**(TTL predefinito per le impostazioni di notifica dei file): durata di una notifica di caricamento del file. Il valore è un giorno per impostazione predefinita.

* **File notification maximum delivery count**(Numero massimo di recapiti per le notifiche dei file): numero di tentativi che verranno eseguiti dall'hub IoT per distribuire una notifica di caricamento del file. Il valore è 10 per impostazione predefinita.

Usare il cmdlet PowerShell seguente per configurare le impostazioni di caricamento dei file nell'hub IoT:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di caricamento dei file dell'hub IoT, vedere [Caricare file da un dispositivo](iot-hub-devguide-file-upload.md).

Per ulteriori informazioni sulla gestione dell'hub IoT di Azure, consultare questi collegamenti:

* [Gestire in blocco i dispositivi IoT](iot-hub-bulk-identity-mgmt.md)
* [Monitorare l'hub Internet delle cose](monitor-iot-hub.md)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Proteggere la soluzione IoT sin dall'inizio](../iot-fundamentals/iot-security-ground-up.md)