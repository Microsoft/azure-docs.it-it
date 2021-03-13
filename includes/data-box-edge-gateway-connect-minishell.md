---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 881d22592e57ef14885eedf2330c71ffa154a136
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603382"
---
A seconda del sistema operativo del client, le procedure per connettersi in remoto al dispositivo sono diverse.

### <a name="remotely-connect-from-a-windows-client"></a>Connettersi in remoto da un client Windows

Prima di iniziare, verificare che il client Windows esegua Windows PowerShell 5,0 o versione successiva.

Per connettersi in remoto da un client Windows, seguire questa procedura.

1. Eseguire una sessione di Windows PowerShell come amministratore.
2. Verificare che il servizio Gestione remota Windows sia in esecuzione nel client. Al prompt dei comandi digitare:

    `winrm quickconfig`

    Per altre informazioni, vedere [Installazione e configurazione di Gestione remota Windows](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Assegnare una variabile all'indirizzo IP del dispositivo.

    $ip = "<device_ip>"

    Sostituire `<device_ip>` con l'indirizzo IP del dispositivo.

4. Per aggiungere l'indirizzo IP del dispositivo all'elenco di host attendibili del client, digitare il comando seguente:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Avviare una sessione di Windows PowerShell nel dispositivo:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Fornire la password quando richiesto. Usare la stessa password usata per accedere all'interfaccia utente Web locale. La password dell'interfaccia utente Web locale predefinita è *Password1*. Quando ci si connette correttamente al dispositivo usando PowerShell remoto, viene visualizzato l'output di esempio seguente:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Connettersi in remoto da un client Linux

Nel client Linux che verrà usato per la connessione:

- [Installare la versione più recente di PowerShell core per Linux](/powershell/scripting/install/installing-powershell-core-on-linux) da GitHub per ottenere la funzionalità di comunicazione remota SSH. 
- [Installare solo il `gss-ntlmssp` pacchetto dal modulo NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Per i client Ubuntu, usare il comando seguente:
    - `sudo apt-get install gss-ntlmssp`

Per altre informazioni, vedere la [comunicazione remota di PowerShell su SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Per connettersi in remoto da un client NFS, attenersi alla seguente procedura.

1. Per aprire la sessione di PowerShell, digitare:

    `pwsh`
 
2. Per la connessione tramite il client remoto, digitare:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Quando richiesto, specificare la password usata per accedere al dispositivo.
 
> [!NOTE]
> Questa procedura non funziona in Mac OS.