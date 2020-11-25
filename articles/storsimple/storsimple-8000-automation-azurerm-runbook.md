---
title: Usare Runbook di automazione di Azure per gestire i dispositivi StorSimple
description: Informazioni su come usare Runbook di automazione di Azure per gestire il dispositivo della serie StorSimple 8000 in portale di Azure.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: b5f2a86fb7c776436ea707ef293a66c033014c33
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021079"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Usare i runbook di automazione di Azure per gestire i dispositivi StorSimple

Questo articolo descrive come usare i runbook di automazione di Azure per gestire il dispositivo StorSimple serie 8000 nel portale di Azure. È incluso un runbook di esempio per illustrare i passaggi per configurare l'ambiente per l'esecuzione di questo runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Configurare, aggiungere ed eseguire un runbook di Azure

In questa sezione viene usato un esempio di script di Windows PowerShell per StorSimple e vengono descritti in dettaglio i vari passaggi necessari per importare lo script in un runbook e quindi pubblicare ed eseguire il runbook.

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di disporre di:

* Una sottoscrizione Azure attiva associata al servizio Gestione dispositivi StorSimple registrato con un dispositivo StorSimple serie 8000.

* Windows PowerShell 5.0 installato nel computer o l'host Windows Server per StorSimple, se disponibile.

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Creare il modulo del runbook di automazione in Windows PowerShell

Per creare un modulo di automazione per la gestione dei dispositivi StorSimple serie 8000, seguire questa procedura:

1. Avviare Windows PowerShell. Creare una nuova cartella e cambiare directory impostandola sulla nuova cartella.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Scaricare l'interfaccia della riga di comando NuGet](https://www.nuget.org/downloads) nella cartella creata nel passaggio precedente. Esistono varie versioni di _nuget.exe_. Scegliere la versione corrispondente all'SDK in uso. Ogni collegamento per il download punta direttamente a un file con estensione _exe_. Assicurarsi di fare clic con il pulsante destro del mouse e di salvare il file nel computer anziché eseguirlo dal browser.

    È anche possibile eseguire il comando seguente per scaricare e archiviare lo script nella stessa cartella creata in precedenza.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Scaricare l'SDK dipendente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Scaricare lo script dal progetto GitHub di esempio.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Creare un modulo del runbook di automazione di Azure per la gestione dei dispositivi StorSimple serie 8000. Digitare i comandi seguenti nella finestra di Windows PowerShell:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Verificare che venga creato un file ZIP del modulo di automazione in `C:\scripts\StorSimpleSDKTools`.

    ![Screenshot mostra la cartella StorSimpleSDKTools con il file zip del modulo di automazione](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Quando il modulo di automazione viene creato tramite Windows PowerShell viene presentato l'output seguente.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importare, pubblicare ed eseguire il runbook di automazione

1. Creare un account di automazione RunAs di Azure nel portale di Azure. A tale scopo, passare ad **Azure Marketplace > Tutto** e quindi cercare **Automazione**. Selezionare **account di automazione**.

    ![Ricerca di Automazione](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Nel pannello **Aggiungi account di Automazione**:

   1. Specificare il **nome** dell'account di automazione.
   2. Selezionare la **Sottoscrizione** collegata al servizio Gestione dispositivi StorSimple.
   3. Creare un nuovo gruppo di risorse o selezionarne uno esistente.
   4. Selezionare un **Percorso** (se possibile lo stesso in cui è in esecuzione il servizio).
   5. Lasciare selezionata l'opzione predefinita **Crea account RunAs**.
   6. Selezionare facoltativamente **Aggiungi al dashboard**. Fare clic su **Crea**.

       ![Screenshot mostra il riquadro Aggiungi account di automazione con i valori per sottoscrizione, crea account RunAs di Azure e crea 2](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Quando la creazione dell'account di automazione è completata, si riceve una notifica. Per altre informazioni su come creare un account di automazione, vedere [Creare un account RunAs](../automation/manage-runas-account.md).

3. Per assicurarsi che l'account di automazione creato possa accedere al servizio Gestione dispositivi StorSimple, è necessario assegnare le autorizzazioni appropriate all'account di automazione. Passare a **Controllo di accesso** nel servizio Gestione dispositivi StorSimple. Fare clic su **+ Aggiungi** e specificare il nome dell'account di automazione di Azure. Fare clic su **Save** (Salva) per salvare le impostazioni.

    ![Aggiungere autorizzazioni per l'account di automazione](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Nell'account appena creato passare a **Risorse condivise > Moduli** e fare clic su **+ Aggiungi modulo**.

5. Nel pannello **Aggiungi modulo** passare al percorso del modulo compresso, quindi selezionare e aprire il modulo. Fare clic su **OK**.

    ![Screenshot mostra il riquadro Aggiungi modulo con un valore di file di caricamento e OK](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Passare ad **Automazione processi > Runbook e fare clic su + Aggiungi runbook**. Nel pannello **Aggiungi runbook** fare clic su **Importa un runbook esistente**. Selezionare il file di script di Windows PowerShell in **File di runbook**. Il tipo di runbook viene selezionato automaticamente. Specificare un nome e una descrizione facoltativa per il runbook. Fare clic su **Crea**.

    ![Aggiungere il modulo](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Il runbook viene aggiunto all'elenco di runbook. Selezionare e fare clic su questo runbook.

    ![Fare clic sul nuovo runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Modificare il Runbook e fare clic su **riquadro di test**. Specificare i parametri, ad esempio il nome del servizio Gestione dispositivi StorSimple, il nome del dispositivo StorSimple e la sottoscrizione. **Avviare** il test. Il report viene generato al termine dell'esecuzione. Per altre informazioni, vedere [How to test a Runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Screenshot che mostra il Pan di test in cui si canenter i valori dei parametri e si avvia un test](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Esaminare l'output dal runbook nel riquadro di test. Se soddisfatti, chiudere il riquadro. Fare clic su **Pubblica** e, quando viene richiesta la conferma, confermare e pubblicare il runbook.

    ![Screenshot mostra il riquadro Modifica Runbook di PowerShell che richiede di procedere alla pubblicazione del Runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Passaggi successivi

[Usare il servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).