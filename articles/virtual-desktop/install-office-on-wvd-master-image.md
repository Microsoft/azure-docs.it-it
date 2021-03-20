---
title: Installare Office in un'immagine del disco rigido virtuale Master-Azure
description: Come installare e personalizzare Office in un'immagine master di un desktop virtuale Windows in Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6369013d605ae538ad611a28a90e9c099bb7d80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326049"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installare Office in un'immagine master di disco rigido virtuale

Questo articolo descrive come installare Microsoft 365 app per Enterprise, OneDrive e altre applicazioni comuni in un'immagine del disco rigido virtuale (VHD) master per il caricamento in Azure. Se gli utenti devono accedere ad alcune applicazioni line-of-business (LOB), è consigliabile installarle dopo aver completato le istruzioni riportate in questo articolo.

Questo articolo presuppone che sia già stata creata una macchina virtuale (VM). In caso contrario, vedere [preparare e personalizzare un'immagine del disco rigido virtuale Master](set-up-customize-master-image.md#create-a-vm)

Questo articolo presuppone anche che sia stato eseguito l'accesso con privilegi elevati alla macchina virtuale, indipendentemente dal fatto che venga effettuato il provisioning in Azure o nella console di gestione di Hyper In caso contrario, vedere [elevare l'accesso per gestire tutti i gruppi di gestione e la sottoscrizione di Azure](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Le istruzioni riguardano una configurazione specifica di Desktop virtuale Windows che può essere usata con processi esistenti dell'organizzazione.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installare Office in modalità di attivazione computer condiviso

Con l'attivazione di computer condivisi è possibile distribuire Microsoft 365 app per Enterprise a un computer dell'organizzazione a cui si accede da più utenti. Per ulteriori informazioni sull'attivazione dei computer condivisi, vedere [Panoramica dell'attivazione di computer condivisi per le app Microsoft 365](/deployoffice/overview-shared-computer-activation).

Usare lo [strumento di distribuzione di Office](https://www.microsoft.com/download/details.aspx?id=49117) per installare Office. Windows 10 Enterprise multisessione supporta solo le seguenti versioni di Office:

   - Microsoft 365 Apps for enterprise
   - Microsoft 365 app for business fornita con una sottoscrizione Microsoft 365 Business Premium

Per lo strumento di distribuzione di Office è necessario un file XML di configurazione. Per personalizzare l'esempio seguente, vedere le [Opzioni di configurazione per lo strumento di distribuzione di Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Questo codice XML di configurazione di esempio fornito eseguirà le operazioni seguenti:

   - Installare Office dal canale Enterprise mensile e recapitare gli aggiornamenti dal canale Enterprise mensile.
   - Usare l'architettura x64.
   - Disabilitare gli aggiornamenti automatici.
   - Rimuovere le installazioni esistenti di Office ed eseguire la migrazione delle impostazioni.
   - Abilitare l'attivazione di computer condivisi.

>[!NOTE]
>La funzionalità di ricerca degli stencil di Visio potrebbe non funzionare come previsto nel desktop virtuale di Windows.

Ecco il codice XML di configurazione di esempio:

   - Installare Skype for business
   - Installare OneDrive in modalità per utente. Per altre informazioni, vedere [installare OneDrive in modalità per computer](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>L'attivazione di computer condivisi può essere configurata tramite oggetti Criteri di gruppo (GPO) o impostazioni del registro di sistema. L'oggetto Criteri di gruppo si trova in **criteri di configurazione del Computer \\ \\ modelli amministrativi \\ Microsoft Office \\ le impostazioni di licenza 2016 (computer)**

Lo strumento di distribuzione di Office contiene setup.exe. Per installare Office, eseguire il comando seguente in una riga di comando:

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>configuration.xml di esempio

Nell'esempio XML seguente viene installata la versione mensile Enterprise Channel.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Il team di Office consiglia di usare l'installazione a 64 bit per il parametro **OfficeClientEdition** .

Dopo l'installazione di Office, è possibile aggiornare il comportamento predefinito di Office. Eseguire i comandi seguenti singolarmente o in un file batch per aggiornare il comportamento.

```cmd
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Installare OneDrive in modalità per computer

OneDrive viene in genere installato per utente. In questo ambiente deve essere installato per computer.

Di seguito viene illustrato come installare OneDrive in modalità per computer:

1. Per prima cosa, creare un percorso per organizzare il programma di installazione di OneDrive. Una cartella del disco locale o un \\ \\ percorso [UNC] (file://UNC) è un problema.

2. Scaricare OneDriveSetup.exe nel percorso di gestione temporanea con il collegamento seguente: <https://aka.ms/OneDriveWVD-Installer>

3. Se Office è stato installato con OneDrive omettendo **\<ExcludeApp ID="OneDrive" /\>** , disinstallare le installazioni OneDrive per utente esistenti da un prompt dei comandi con privilegi elevati eseguendo il comando seguente:

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Eseguire questo comando da un prompt dei comandi con privilegi elevati per impostare il valore del registro di sistema **AllUsersInstall** :

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Eseguire questo comando per installare OneDrive in modalità per computer:

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Eseguire questo comando per configurare OneDrive per avviare l'accesso a tutti gli utenti:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Abilitare l' **account utente per la configurazione invisibile all'utente** eseguendo il comando seguente.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Reindirizzare e spostare le cartelle note di Windows in OneDrive eseguendo il comando seguente.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft teams e Skype for business

Desktop virtuale Windows non supporta Skype for business.

Per informazioni sull'installazione di Microsoft teams, vedere [usare Microsoft teams sul desktop virtuale di Windows](teams-on-wvd.md). L'ottimizzazione dei supporti per Microsoft teams su desktop virtuale Windows è disponibile in anteprima.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato aggiunto Office all'immagine, è possibile continuare a personalizzare l'immagine del disco rigido virtuale master. Vedere [preparare e personalizzare un'immagine del disco rigido virtuale Master](set-up-customize-master-image.md).
