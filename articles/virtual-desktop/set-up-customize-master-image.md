---
title: Preparare e personalizzare un'immagine del disco rigido virtuale Master-Azure
description: Come preparare, personalizzare e caricare un'immagine master di un desktop virtuale Windows in Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9e4cb4fe8a701600290d24c055e2be5187a711c5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023395"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Preparare e personalizzare un'immagine master di disco rigido virtuale

Questo articolo illustra come preparare un'immagine del disco rigido virtuale (VHD) master per il caricamento in Azure, tra cui come creare macchine virtuali (VM) e installare software su di essi. Le istruzioni riguardano una configurazione specifica di Desktop virtuale Windows che può essere usata con processi esistenti dell'organizzazione.

## <a name="create-a-vm"></a>Creare una macchina virtuale

La funzionalità multisessione Enterprise di Windows 10 è disponibile nella raccolta immagini di Azure. Sono disponibili due opzioni per la personalizzazione di questa immagine.

La prima opzione consiste nel provisioning di una macchina virtuale (VM) in Azure seguendo le istruzioni riportate in [creare una VM da un'immagine gestita](../virtual-machines/windows/create-vm-generalized-managed.md), quindi passare alla [preparazione e all'installazione del software](set-up-customize-master-image.md#software-preparation-and-installation).

La seconda opzione consiste nel creare l'immagine localmente scaricando l'immagine, eseguendo il provisioning di una macchina virtuale Hyper-V e personalizzarla in base alle proprie esigenze, illustrata nella sezione seguente.

### <a name="local-image-creation"></a>Creazione di un'immagine locale

Una volta scaricata l'immagine in un percorso locale, aprire la console di **gestione di Hyper-V** per creare una macchina virtuale con il disco rigido virtuale copiato. Le istruzioni seguenti sono una versione semplice, ma è possibile trovare istruzioni più dettagliate in [creare una macchina virtuale in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Per creare una macchina virtuale con il disco rigido virtuale copiato:

1. Aprire la **procedura guidata nuova macchina virtuale**.

2. Nella pagina specifica generazione selezionare **generazione 1**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina Impostazione generazione. È selezionata l'opzione "generazione 1".](media/a41174fd41302a181e46385e1e701975.png)

3. In tipo di checkpoint disabilitare i checkpoint deselezionando la casella di controllo.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della sezione del tipo di checkpoint della pagina checkpoints.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

È anche possibile eseguire il cmdlet seguente in PowerShell per disabilitare i checkpoint.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disco fisso

Se si crea una macchina virtuale da un disco rigido virtuale esistente, per impostazione predefinita viene creato un disco dinamico. Può essere modificato in un disco fisso selezionando **modifica disco...** come illustrato nell'immagine seguente. Per istruzioni più dettagliate, vedere [preparare un disco rigido virtuale Windows o VHDX per il caricamento in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!div class="mx-imgBorder"]
> ![Screenshot dell'opzione modifica disco.](media/35772414b5a0f81f06f54065561d1414.png)

È anche possibile eseguire il cmdlet di PowerShell seguente per modificare il disco in un disco fisso.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Preparazione e installazione del software

Questa sezione illustra come preparare e installare FSLogix e Windows Defender, nonché alcune opzioni di configurazione di base per le app e il registro di sistema dell'immagine.

Se si installano Microsoft 365 app per Enterprise e OneDrive nella macchina virtuale, passare a [installa Office in un'immagine del disco rigido virtuale Master](install-office-on-wvd-master-image.md) e seguire le istruzioni per installare le app. Al termine, tornare a questo articolo.

Se gli utenti devono accedere ad alcune applicazioni LOB, è consigliabile installarle dopo aver completato le istruzioni della sezione.

### <a name="set-up-user-profile-container-fslogix"></a>Configurare il contenitore profili utente (FSLogix)

Per includere il contenitore FSLogix come parte dell'immagine, seguire le istruzioni riportate in [creare un contenitore di profili per un pool di host usando una condivisione file](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). È possibile testare la funzionalità del contenitore FSLogix con [questa Guida introduttiva](/fslogix/configure-cloud-cache-tutorial/).

### <a name="configure-windows-defender"></a>Configurare Windows Defender

Se Windows Defender è configurato nella macchina virtuale, assicurarsi che sia configurato in modo da non eseguire l'analisi dell'intero contenuto dei file VHD e VHDX durante l'allegato.

Questa configurazione rimuove solo l'analisi dei file VHD e VHDX durante l'allegato, ma non influisce sull'analisi in tempo reale.

Per istruzioni più dettagliate su come configurare Windows Defender in Windows Server, vedere [configurare le esclusioni di Windows Defender antivirus in Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Per altre informazioni su come configurare Windows Defender in modo da escludere determinati file dall'analisi, vedere [configurare e convalidare le esclusioni in base all'estensione di file e al percorso della cartella](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Disabilitare Aggiornamenti automatici

Per disabilitare Aggiornamenti automatici tramite Criteri di gruppo locale:

1. Aprire **Editor criteri di gruppo locali \\ \\ componenti di Windows modelli amministrativi \\ Windows Update**.
2. Fare clic con il pulsante destro del mouse su **Configura aggiornamento automatico** e impostarlo su **disabilitato**.

È anche possibile eseguire il comando seguente al prompt dei comandi per disabilitare Aggiornamenti automatici.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Specificare il layout iniziale per i PC Windows 10 (facoltativo)

Eseguire questo comando per specificare un layout iniziale per i PC Windows 10.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurare il reindirizzamento del fuso orario

Il reindirizzamento del fuso orario può essere applicato a livello di Criteri di gruppo perché tutte le macchine virtuali in un pool di host fanno parte dello stesso gruppo di sicurezza.

Per reindirizzare i fusi orari:

1. Nel server Active Directory aprire il **console Gestione criteri di gruppo**.
2. Espandere il dominio e gli oggetti Criteri di gruppo.
3. Fare clic con il pulsante destro del mouse sull' **oggetto Criteri di gruppo** creato per le impostazioni di criteri di gruppo e scegliere **modifica**.
4. Nella **Editor gestione criteri di gruppo** passare a **Configurazione computer**  >  **criteri**  >  **modelli amministrativi**  >  **componenti di Windows**  >  **Servizi Desktop remoto**  >  **host sessione Desktop remoto**  >  **Reindirizzamento risorse e dispositivi**.
5. Abilitare l'impostazione **Consenti reindirizzamento del fuso orario** .

È anche possibile eseguire questo comando sull'immagine master per reindirizzare i fusi orari:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Disabilitare il rilevamento dell'archiviazione

Per gli host sessione Desktop virtuale Windows che usano Windows 10 Enterprise o Windows 10 Enterprise multisessione è consigliabile disabilitare Sensore memoria. È possibile disabilitare il rilevamento dell'archiviazione nel menu impostazioni sotto **archiviazione**, come illustrato nello screenshot seguente:

> [!div class="mx-imgBorder"]
> ![Screenshot del menu archiviazione in impostazioni. L'opzione "rilevamento archiviazione" è disattivata.](media/storagesense.png)

È anche possibile modificare l'impostazione con il registro di sistema eseguendo il comando seguente:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Includi supporto per lingue aggiuntive

Questo articolo non illustra come configurare la lingua e il supporto a livello di area. Per altre informazioni, vedere gli articoli seguenti:

- [Aggiungere lingue alle immagini Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Funzionalità su richiesta](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Funzionalità della lingua e dell'area su richiesta (Dom)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Altre applicazioni e configurazione del registro di sistema

In questa sezione viene illustrata la configurazione dell'applicazione e del sistema operativo. Tutte le configurazioni in questa sezione vengono eseguite tramite le voci del registro di sistema che possono essere eseguite dagli strumenti da riga di comando e Regedit.

>[!NOTE]
>È possibile implementare procedure consigliate nella configurazione con oggetti Criteri di gruppo (GPO) o importazioni del registro di sistema. L'amministratore può scegliere una delle opzioni in base ai requisiti dell'organizzazione.

Per la raccolta di dati di telemetria per l'hub di feedback in Windows 10 Enterprise multisessione, eseguire questo comando:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Eseguire il comando seguente per correggere gli arresti anomali di Watson:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Immettere i comandi seguenti nell'editor del registro di sistema per correggere il supporto della risoluzione 5K. Prima di poter abilitare lo stack affiancato, è necessario eseguire i comandi.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Preparare l'immagine per il caricamento in Azure

Al termine della configurazione e dell'installazione di tutte le applicazioni, seguire le istruzioni riportate in [preparare un disco rigido virtuale Windows o VHDX per il caricamento in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) per preparare l'immagine.

Dopo aver preparato l'immagine per il caricamento, assicurarsi che la macchina virtuale rimanga nello stato disattivato o deallocato.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Caricare l'immagine master in un account di archiviazione in Azure

Questa sezione si applica solo quando l'immagine master è stata creata localmente.

Le istruzioni seguenti illustrano come caricare l'immagine master in un account di archiviazione di Azure. Se non si ha già un account di archiviazione di Azure, seguire le istruzioni riportate in [questo articolo](/azure/developer/javascript/tutorial-vscode-static-website-node-03) per crearne uno.

1. Convertire l'immagine di macchina virtuale (VHD) in corretta, se non è già stata eseguita. Se l'immagine non viene convertita in fixed, non è possibile crearla correttamente.

2. Caricare il disco rigido virtuale in un contenitore BLOB nell'account di archiviazione. È possibile caricare rapidamente con lo [strumento Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Per altre informazioni sullo strumento Storage Explorer, vedere [questo articolo](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows).

    > [!div class="mx-imgBorder"]
    > ![Screenshot della finestra di ricerca dello strumento Microsoft Azure Storage Explorer. È selezionata la casella di controllo "carica file con estensione VHD o VHDX come BLOB di pagine (scelta consigliata)".](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Passare quindi al portale di Azure nel browser e cercare "immagini". La ricerca dovrebbe condurre alla pagina **Crea immagine** , come illustrato nello screenshot seguente:

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina Create image del portale di Azure, con valori di esempio per l'immagine.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Una volta creata l'immagine, viene visualizzata una notifica come quella riportata nello screenshot seguente:

    > [!div class="mx-imgBorder"]
    > ![Screenshot della notifica di "immagine creata correttamente".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di un'immagine, è possibile creare o aggiornare i pool host. Per ulteriori informazioni su come creare e aggiornare i pool host, vedere gli articoli seguenti:

- [Creare un pool di host con un modello di Azure Resource Manager](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Esercitazione: Creare un pool di host con Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Creare un pool di host con PowerShell](create-host-pools-powershell.md)
- [Creare un contenitore di profili per un pool di host con una condivisione file](create-host-pools-user-profile.md)
- [Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows](configure-host-pool-load-balancing.md)