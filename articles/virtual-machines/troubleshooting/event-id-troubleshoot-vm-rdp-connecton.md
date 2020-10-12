---
title: Risolvere i problemi di connessione RDP a macchine virtuali di Azure in base all'ID evento | Microsoft Docs
description: Usare gli ID evento per risolvere vari problemi che impediscono una connessione RDP (Desktop remoto Protocol) a una macchina virtuale (VM) di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 507cd6cfe9f251dbc304b579d634ff986b001264
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088598"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Risolvere i problemi di connessione RDP a macchine virtuali di Azure in base all'ID evento 

Questo articolo illustra come usare gli ID evento per risolvere i problemi che impediscono di stabilire una connessione Remote Desktop Protocol (RDP) a una macchina virtuale di Azure.

## <a name="symptoms"></a>Sintomi

Si prova a usare una sessione Remote Desktop Protocol (RDP) per connettersi a una macchina virtuale di Azure. Dopo aver immesso le credenziali, la connessione ha esito negativo e viene visualizzato il messaggio di errore seguente:

**Il computer non è in grado di connettersi al computer remoto. Provare a connettersi di nuovo. se il problema persiste, contattare il proprietario del computer remoto o l'amministratore di rete.**

Per risolvere questo problema, esaminare i log eventi nella macchina virtuale e quindi fare riferimento agli scenari seguenti.

## <a name="before-you-troubleshoot"></a>Operazioni preliminari alla risoluzione dei problemi

### <a name="create-a-backup-snapshot"></a>Creare uno snapshot di backup

Per creare uno snapshot di backup, seguire i passaggi descritti in [Creare uno snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Connettersi alla macchina virtuale in modalità remota

Per connettersi alla macchina virtuale in modalità remota, usare uno dei metodi descritti in [How to use remote tools to troubleshoot Azure VM issues](remote-tools-troubleshoot-azure-vm-issues.md) (Usare gli strumenti remoti per risolvere i problemi delle macchine virtuali di Azure).

## <a name="scenario-1"></a>Scenario 1

### <a name="event-logs"></a>Log eventi

In un'istanza CMD eseguire i comandi seguenti per controllare se nelle ultime 24 ore è stato registrato l'evento 1058 o l'evento 1057 nel log di sistema:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome registro:**      Sistema <br />
**Origine:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *data e ora* <br />
**ID evento:** 1058 <br />
**Categoria attività:** Nessuno <br />
**Livello:**         Errore <br />
**Parole chiave:**      Classico <br />
**Utente:**          N/A <br />
**Computer:**      *computer* <br />
**Descrizione:** Il server Host sessione Desktop remoto non è stato in grado di sostituire il certificato autofirmato scaduto usato per l'autenticazione del server Host sessione Desktop remoto nelle connessioni TLS. Il codice di stato pertinente era Accesso negato.

**Nome registro:**      Sistema <br />
**Origine:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *data e ora* <br />
**ID evento:** 1058 <br />
**Categoria attività:** Nessuno <br />
**Livello:**         Errore <br />
**Parole chiave:**      Classico <br />
**Utente:**          N/A <br />
**Computer:**      *computer* <br />
**Descrizione:** Il server Host sessione Desktop remoto non è stato in grado di creare un nuovo certificato autofirmato da utilizzare per l'autenticazione del server Host sessione Desktop remoto nelle connessioni TLS. il codice di stato pertinente era oggetto già esistente.

**Nome registro:**      Sistema <br />
**Origine:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *data e ora* <br />
**ID evento:** 1057 <br />
**Categoria attività:** Nessuno <br />
**Livello:**         Errore <br />
**Parole chiave:**      Classico <br />
**Utente:**          N/A <br />
**Computer:**      *computer* <br />
**Descrizione:** Il server Host sessione Desktop remoto non è stato in grado di creare un nuovo certificato autofirmato da utilizzare per l'autenticazione del server Host sessione Desktop remoto nelle connessioni TLS. Il codice di stato pertinente era Keyset does not exist (Il set di chiavi non esiste).

È anche possibile cercare gli eventi di errore SCHANNEL 36872 e 36870 eseguendo i comandi seguenti:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome registro:**      Sistema <br />
**Origine:** Schannel <br />
**Data**: - <br />
**ID evento:** 36870 <br />
**Categoria attività:** Nessuno <br />
**Livello:**         Errore <br />
**Parole**       <br />
**Utente:** SYSTEM <br />
**Computer:**      *computer* <br />
**Descrizione:** Si è verificato un errore irreversibile durante il tentativo di accedere alla chiave privata delle credenziali del server TLS. Il codice errore restituito dal modulo di crittografia è 0x8009030D.  <br />
Lo stato dell'errore interno è 10001.

### <a name="cause"></a>Causa
Questo problema si verifica perché le chiavi di crittografia RSA locali nella cartella MachineKeys sulla macchina virtuale non sono accessibili. Questo problema può verificarsi per uno dei motivi seguenti:

1. Configurazione delle autorizzazioni non corretta per la cartella Machinekeys o per i file RSA.

2. Chiave RSA danneggiata o mancante.

### <a name="resolution"></a>Soluzione

Per risolvere il problema, è necessario configurare le autorizzazioni corrette per il certificato RDP seguendo questa procedura.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Concedere l'autorizzazione alla cartella MachineKeys

1. Creare uno script usando il contenuto seguente:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Eseguire questo script per reimpostare le autorizzazioni della cartella MachineKey e reimpostare i file RSA sui valori predefiniti.

3.  Provare ad accedere di nuovo alla macchina virtuale.

Dopo aver eseguito lo script, è possibile controllare i file seguenti in cui si verificano problemi di autorizzazioni:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Rinnovare il certificato autofirmato RDP

Se il problema persiste, eseguire lo script seguente per assicurarsi che il certificato autofirmato RDP venga rinnovato:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Se non è possibile rinnovare il certificato, seguire questa procedura per provare a eliminare il certificato:

1. In un'altra macchina virtuale della stessa rete virtuale aprire la casella **Esegui**, digitare **mmc** e quindi fare clic su **OK**. 

2. Nel menu **File** selezionare **Aggiungi/Rimuovi snap-in**.

3. Nell'elenco **Snap-in disponibili** selezionare **Certificati** e quindi selezionare **Aggiungi**.

4. Selezionare **Account computer** e quindi selezionare **Avanti**.

5. Selezionare **Altro computer** e quindi aggiungere l'indirizzo IP della macchina virtuale che presenta problemi.
   >[!Note]
   >Provare a usare la rete interna per evitare di usare un indirizzo IP virtuale.

6. Selezionare **Fine** e quindi **OK**.

   ![Selezionare il computer](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Espandere i certificati, passare alla cartella Remote Desktop\Certificates, fare clic con il pulsante destro del mouse sul certificato e quindi scegliere **Elimina**.

8. Riavviare il servizio di configurazione di Desktop remoto:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >A questo punto, se si aggiorna l'archivio da mmc, il certificato viene nuovamente visualizzato. 

Provare nuovamente ad accedere alla macchina virtuale usando RDP.

#### <a name="update-tlsssl-certificate"></a>Aggiornare il certificato TLS/SSL

Se si configura la macchina virtuale per l'uso di un certificato TLS/SSL, eseguire il comando seguente per ottenere l'identificazione personale. Controllare quindi se si tratta della stessa identificazione personale del certificato:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Se non lo è, modificare l'identificazione personale:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

È anche possibile provare a eliminare la chiave in modo che RDP usi il certificato autofirmato per RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scenario 2

### <a name="event-log"></a>Registro eventi

In un'istanza CMD eseguire i comandi seguenti per controllare se nelle ultime 24 ore è stato registrato l'evento di errore SCHANNEL 36871 nel log di sistema:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome registro:**      Sistema <br />
**Origine:** Schannel <br />
**Data**: - <br />
**ID evento:** 36871 <br />
**Categoria attività:** Nessuno <br />
**Livello:**         Errore <br />
**Parole**       <br />
**Utente:** SYSTEM <br />
**Computer:**      *computer* <br />
**Descrizione:** si è verificato un errore irreversibile durante la creazione di una credenziale del server TLS. Lo stato dell'errore interno è 10013.
 
### <a name="cause"></a>Causa

Questo problema è causato dai criteri di sicurezza. Quando le versioni precedenti di TLS (ad esempio, la versione 1.0) sono disabilitate, l'accesso RDP non riesce.

### <a name="resolution"></a>Soluzione

RDP usa TLS 1.0 come protocollo predefinito. Tuttavia, il protocollo può essere sostituito con TLS 1.1, che è il nuovo standard.

Per risolvere questo problema, vedere [Risolvere gli errori di autenticazione quando si usa RDP per connettersi a una macchina virtuale di Azure](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Scenario 3

Se nella macchina virtuale è stato installato il ruolo **Gestore connessione Desktop remoto** controllare se nelle ultime 24 ore si è verificato l'evento 2056 o l'evento 1296. In un'istanza CMD eseguire i comandi seguenti: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome log:** Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Origine:** Microsoft-Windows-TerminalServices-SessionBroker <br />
**Data:**          *data e ora* <br />
**ID evento:** 2056 <br />
**Categoria attività:** (109) <br />
**Livello:**         Errore <br />
**Parole**       <br />
**Utente:** NETWORK SERVICE <br />
**Computer:**      *FQDN del computer* <br />
**Descrizione:** non è possibile trovare la descrizione dell'ID evento 2056 da Microsoft-Windows-TerminalServices-SessionBroker. Il componente che genera questo evento non è installato nel computer locale o l'installazione è danneggiata. È possibile installare o riparare il componente nel computer locale. <br />
Se l'evento ha avuto origine in un altro computer, le informazioni visualizzate dovevano essere salvate con l'evento. <br />
Le informazioni seguenti sono state incluse con l'evento: <br />
NULL <br />
NULL <br />
L'accesso al database non è riuscito.

**Nome log:** Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Origine:** Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Data:**          *data e ora* <br />
**ID evento:** 1296 <br />
**Categoria attività:** (104) <br />
**Livello:**         Errore <br />
**Parole**       <br />
**Utente:** NETWORK SERVICE <br />
**Computer:**      *FQDN del computer* <br />
**Descrizione:** non è possibile trovare la descrizione dell'ID evento 1296 da Microsoft-Windows-TerminalServices-SessionBroker-Client. Il componente che genera questo evento non è installato nel computer locale o l'installazione è danneggiata. È possibile installare o riparare il componente nel computer locale.
Se l'evento ha avuto origine in un altro computer, le informazioni visualizzate dovevano essere salvate con l'evento.
Le informazioni seguenti sono state incluse con l'evento:  <br />
*text* <br />
*text* <br />
Gestore connessione Desktop remoto non è pronto per la comunicazione RPC.

### <a name="cause"></a>Causa

Questo problema è dovuto al fatto che viene modificato il nome host del server Gestore connessione Desktop remoto, ma questa modifica non è supportata. 

Il nome host ha voci e dipendenze in Database interno di Windows, che è necessario per il funzionamento di servizi Desktop Remoto. La modifica del nome host dopo che la farm è già stata compilata comporta molti errori e può impedire il funzionamento del server gestore.

### <a name="resolution"></a>Soluzione 

Per risolvere questo problema, è necessario reinstallare il ruolo Gestore connessione Desktop remoto e Database interno di Windows.

## <a name="next-steps"></a>Passaggi successivi

[Schannel Events (Eventi di Schannel)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786445(v=ws.11))

[Panoramica tecnica di SSP Schannel](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786429(v=ws.11))

[RDP Fails with Event ID 1058 & Event 36870 with Remote Desktop Session Host Certificate & SSL Communication (RDP ha esito negativo con l'ID evento 1058 e l'evento 36870 con il certificato host sessione Desktop remoto e la comunicazione SSL)](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)

[Schannel 36872 or Schannel 36870 on a Domain Controller (Evento Schannel 36872 o Schannel 36870 in un controller di dominio)](/archive/blogs/instan/schannel-36872-or-schannel-36870-on-a-domain-controller)

[Event ID 1058 — Remote Desktop Services Authentication and Encryption (ID evento 1058 - Autenticazione e crittografia di Servizi Desktop remoto)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee890862(v=ws.10))
