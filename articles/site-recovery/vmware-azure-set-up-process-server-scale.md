---
title: Configurare un server di elaborazione con scalabilità orizzontale durante il ripristino di emergenza di macchine virtuali VMware e server fisici con Azure Site Recovery | Microsoft Docs '
description: Questo articolo descrive come configurare un server di elaborazione con scalabilità orizzontale durante il ripristino di emergenza di macchine virtuali VMware e server fisici.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 40f912122e6ffb9cccbd32a747f6f0d46fd6c330
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019141"
---
# <a name="scale-with-additional-process-servers"></a>Scalabilità con server di elaborazione aggiuntivi

Per impostazione predefinita, quando si esegue la replica di server fisici o macchine virtuali VMware in Azure con [Site Recovery](site-recovery-overview.md), nel server di configurazione viene installato un server di elaborazione che è usato per coordinare il trasferimento dei dati tra Site Recovery e l'infrastruttura locale. Per aumentare la capacità e la scalabilità orizzontale della distribuzione della replica, è possibile aggiungere server di elaborazione autonomi aggiuntivi. Questo articolo descrive come configurare un server di elaborazione con scalabilità orizzontale.

## <a name="before-you-start"></a>Prima di iniziare

### <a name="capacity-planning"></a>Pianificazione della capacità

Assicurarsi di aver [pianificato la capacità](site-recovery-plan-capacity-vmware.md) per la replica VMware. Ciò consente di identificare come e quando è necessario distribuire i server di elaborazione aggiuntivi.

Dalla versione 9,24, vengono aggiunte informazioni aggiuntive durante la selezione del server di elaborazione per le nuove repliche. Il server di elaborazione verrà contrassegnato come integro, avviso e critico in base a determinati criteri. Per comprendere scenari diversi che possono influenzare lo stato del server di elaborazione, esaminare gli [avvisi del server di elaborazione](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> L'uso di un componente server di processo clonato non è supportato. Seguire i passaggi in questo articolo per ciascun scale-out PS.

### <a name="sizing-requirements"></a>Requisiti di dimensione 

Verificare i requisiti di dimensione riepilogati nella tabella. In generale, se è necessario ridimensionare la distribuzione oltre 200 computer di origine oppure se la varianza totale giornaliera è superiore a 2 TB, sono necessari server di elaborazione aggiuntivi per gestire il volume di traffico.

| **Server di elaborazione aggiuntivo** | **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti** |
| --- | --- | --- | --- |
|4 vCPU (2 socket * 2 core \@ 2,5 GHz), 8 GB di memoria |300 GB |250 GB o inferiore |Replicare un massimo di 85 computer. |
|8 vCPU (2 socket * 4 core \@ 2,5 GHz), 12 GB di memoria |600 GB |Da 250 GB a 1 TB |Replicare tra 85 e 150 computer. |
|12 vCPU (2 socket * 6 core \@ 2,5 GHz), 24 GB di memoria |1 TB |Da 1 TB a 2 TB |Replicare tra 150 e 225 computer. |

In cui ogni computer di origine protetto è configurato con tre dischi da 100 GB.

### <a name="prerequisites"></a>Prerequisiti

Nella tabella seguente sono riepilogati i prerequisiti per il server di elaborazione aggiuntivo.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Scaricare il file di installazione

Scaricare il file di installazione del server di elaborazione come indicato di seguito:

1. Accedere al portale di Azure e passare all'insieme di credenziali di servizi di ripristino.
2. Aprire **Site Recovery infrastruttura**  >  **VMware e computer fisici**  >  **server di configurazione** (in per VMware & computer fisici).
3. Selezionare il server di configurazione per eseguire il drill-down della relativa pagina delle informazioni. Fare clic su **+ Server di elaborazione**.
4. In **Aggiungi server di elaborazione**  >   **scegliere la posizione in cui si vuole distribuire il server di elaborazione**, selezionare **Distribuisci un server di elaborazione con scalabilità orizzontale in locale**.

   ![Pagina di aggiunta server](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Scaricare il programma di **installazione unificata di Microsoft Azure Site Recovery**. Viene scaricata la versione più recente del file di installazione.

   > [!WARNING]
   > La versione di installazione del server di elaborazione deve essere uguale o precedente alla versione del server di configurazione in esecuzione. Un modo semplice per garantire la compatibilità tra le versioni è l'utilizzo dello stesso programma di installazione usato per installare o aggiornare il server di configurazione.

## <a name="install-from-the-ui"></a>Installazione dall'interfaccia utente

Eseguire l'installazione come indicato di seguito. Dopo aver configurato il server è possibile eseguire la migrazione dei computer di origine per usarlo.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Eseguire l'installazione dalla riga di comando

Eseguire l'installazione con questo comando:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMware/NonVMware>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

I parametri della riga di comando sono i seguenti:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Ad esempio:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMware" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Creare un file di impostazioni proxy

Se è necessario configurare un proxy, il parametro ProxySettingsFilePath prende un file come input. Creare il file come indicato di seguito e passarlo come parametro ProxySettingsFilePath di input.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulla [gestione delle impostazioni del server di elaborazione](vmware-azure-manage-process-server.md)
