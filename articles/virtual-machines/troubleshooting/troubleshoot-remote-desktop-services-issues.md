---
title: Servizi Desktop remoto non si avvia in una macchina virtuale di Azure| Microsoft Docs
description: Informazioni su come risolvere i problemi di Servizi Desktop remoto quando ci si connette a una macchina virtuale | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 0b00785fed7708986885e9da9102e8f1b4fd4539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508883"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Servizi Desktop remoto non si avvia in una macchina virtuale di Azure

Questo articolo descrive come risolvere i problemi quando ci si connette a una macchina virtuale di Azure e Servizi Desktop remoto, o TermService, non si avvia o restituisce un errore di avvio.


## <a name="symptoms"></a>Sintomi

Quando si prova a connettersi a una macchina virtuale, si verificano gli scenari seguenti:

- Lo screenshot della macchina virtuale indica che il sistema operativo è completamente caricato ed è in attesa delle credenziali.

    ![Screenshot dello stato della macchina virtuale](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Si visualizzano in modalità remota i log eventi nella macchina virtuale usando il Visualizzatore eventi. Si nota che Servizi Desktop remoto, o TermService, non si avvia o restituisce un errore di avvio. Di seguito è riportato un log di esempio:

    **Nome registro**: System </br>
    **Origine**: Gestione controllo servizi </br>
    **Data**:          12/16/2017 11:19:36 AM</br>
    **ID evento**: 7022</br>
    **Categoria attività**: Nessuna</br>
    **Livello**: errore</br>
    **Parole chiave**: classico</br>
    **Utente**: N/A</br>
    **Computer**:      vm.contoso.com</br>
    **Descrizione**: Servizi Desktop remoto non si avvia. 

    È anche possibile usare la funzionalità della console di accesso seriale per cercare questi errori eseguendo la query seguente: 

    ```console
   wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
    ```

## <a name="cause"></a>Causa
 
Questo problema si verifica perché Servizi Desktop remoto non è in esecuzione sulla macchina virtuale. La causa dipende dagli scenari seguenti: 

- Il servizio TermService è **disabilitato**. 
- Il servizio TermService si è arrestato in modo anomalo o non risponde. 
- Il TermService non viene avviato a causa di una configurazione non corretta.

## <a name="solution"></a>Soluzione

Per risolvere questo problema, usare la console seriale oppure [riparare la macchina virtuale in modalità offline](#repair-the-vm-offline) collegando il disco del sistema operativo della macchina virtuale a una macchina virtuale di ripristino.

### <a name="use-serial-console"></a>Usare la console seriale

1. Accedere alla [console seriale](serial-console-windows.md) selezionando **Supporto e risoluzione dei problemi** > **Console seriale**. Se la funzionalità è abilitata nella macchina virtuale, è possibile connettere correttamente la macchina virtuale.

2. Creare un nuovo canale per un'istanza CMD. Immettere **CMD** per avviare il canale e ottenere il relativo nome.

3. Passare al canale che esegue l'istanza di CMD. In questo caso, dovrebbe essere il canale 1:

   ```
   ch -si 1
   ```

4. Premere di nuovo **INVIO** e digitare un nome utente e una password validi, ID locale o di dominio, per la macchina virtuale.

5. Eseguire una query sullo stato del servizio TermService:

   ```
   sc query TermService
   ```

6. Se lo stato del servizio è indicato come **Arrestato**, provare ad avviare il servizio:

    ```
    sc start TermService
     ``` 

7. Eseguire di nuovo una query sul servizio per assicurarsi che il servizio sia stato avviato correttamente:

   ```
   sc query TermService
   ```
8. Se il servizio non si avvia, seguire la soluzione in base all'errore ricevuto:

    |  Errore |  Suggerimento |
    |---|---|
    |5- ACCESS DENIED |Vedere [Il servizio TermService viene arrestato a causa di un errore di accesso negato](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Vedere il [servizio TermService è disabilitato](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Vedere [Il servizio TermService si arresta in modo anomalo o si interrompe](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Contatta il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere rapidamente la risoluzione del problema.|
    |1067 - ERROR_PROCESS_ABORTED  |Vedere [Il servizio TermService si arresta in modo anomalo o si interrompe](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Contatta il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere rapidamente la risoluzione del problema.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Vedere [Il servizio TermService non funziona a causa di un errore di accesso](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Vedere [Il servizio TermService si arresta in modo anomalo o si interrompe](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Vedere il [servizio TermService è disabilitato](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Contatta il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere rapidamente la risoluzione del problema. |
    |1753   |[Contatta il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere rapidamente la risoluzione del problema.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>Il servizio TermService viene arrestato a causa di un problema di accesso negato

1. Connettersi alla [console seriale](serial-console-windows.md) e aprire un'istanza di PowerShell.
2. Scaricare lo strumento Process Monitor eseguendo lo script seguente:

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```

3. A questo punto, avviare una traccia **procmon** :

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
   ```

4. Riprodurre il problema avviando il servizio che restituisce l'errore **Accesso negato**: 

   ```
   sc start TermService 
   ```

   Quando si verifica l'errore, terminare la traccia di monitoraggio del processo:

   ```   
   procmon /Terminate 
   ```

5. Raccogliere il file **c:\temp\ProcMonTrace.PML**:

    1. [Collegare un disco dati alla macchina virtuale](../windows/attach-managed-disk-portal.md
).
    2. Con la console seriale è possibile copiare il file nella nuova unità. Ad esempio: `copy C:\temp\ProcMonTrace.PML F:\`. In questo comando F è la lettera di unità del disco dati collegato.
    3. Scollegare l'unità dati e collegarla a una macchina virtuale funzionante in cui è installato lo strumento di monitoraggio del processo.

6. Aprire **ProcMonTrace.PML** usando lo strumento di monitoraggio del processo nella macchina virtuale funzionante. Il filtro in base al **risultato è accesso negato**, come illustrato nello screenshot seguente:

    ![Filtra per risultato in monitoraggio processo](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Correggere le chiavi del Registro di sistema, le cartelle o i file che sono riportati nell'output. In genere, questo problema è dovuto al fatto che l'account di accesso usato per il servizio non ha l'autorizzazione ACL per accedere a questi oggetti. Per conoscere l'autorizzazione ACL corretta per l'account di accesso, è possibile controllarla in una macchina virtuale integra. 

#### <a name="termservice-service-is-disabled"></a>Il servizio TermService è disabilitato

1. Ripristinare il valore di avvio predefinito per il servizio:

   ```
   sc config TermService start= demand 
   ```

2. Avviare il servizio:

   ```
   sc start TermService
   ```

3. Eseguire nuovamente una query sullo stato per assicurarsi che il servizio sia in esecuzione:

   ```
   sc query TermService 
   ```

4. Provare a connettersi alla macchina virtuale usando Desktop remoto.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Il servizio TermService non funziona a causa di un errore di accesso

1. Questo problema si verifica se l'account di avvio del servizio è stato modificato. Ripristinare il valore predefinito: 

    ```console
    sc config TermService obj= 'NT Authority\NetworkService'
    ```

2. Avviare il servizio:

    ```console
    sc start TermService
    ```

3. Provare a connettersi alla macchina virtuale usando Desktop remoto.

#### <a name="termservice-service-crashes-or-hangs"></a>Il servizio TermService si arresta in modo anomalo o si interrompe
1. Se lo stato del servizio è bloccato sull'**avvio** o sull'**arresto**, provare ad arrestare il servizio: 

    ```console
    sc stop TermService
    ```

2. Isolare il servizio nel contenitore "svchost":

    ```console
    sc config TermService type= own
    ```

3. Avviare il servizio:

    ```console
    sc start TermService
    ```

4. Se non è ancora possibile avviare il servizio, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Riparare la macchina virtuale in modalità offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Collegare il disco del sistema operativo alla macchina virtuale di ripristino

1. [Alleghi il disco del sistema operativo a una macchina virtuale di ripristino](./troubleshoot-recovery-disks-portal-windows.md).
2. Avviare una connessione Desktop remoto alla macchina virtuale di ripristino. Verificare che il disco collegato sia contrassegnato come **Online** nella console di Gestione disco. Prendere nota della lettera di unità assegnata al disco del sistema operativo collegato.
3. Aprire un'istanza del prompt dei comandi con privilegi elevati (**Esegui come amministratore**). Eseguire quindi lo script seguente. Si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia **F**. Sostituirlo con il valore appropriato nella macchina virtuale. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Scollegare il disco del sistema operativo e ricreare la macchina virtuale](./troubleshoot-recovery-disks-portal-windows.md). Controllare quindi se il problema è stato risolto.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), per ottenere la risoluzione del problema.
