---
title: Risoluzione dettagliata dei problemi di Desktop remoto in Azure | Documentazione Microsoft
description: Rivedere i passaggi dettagliati della procedura di risoluzione dei problemi riguardo all'impossibilità di Desktop remoto di connettersi a una macchina virtuale di Windows in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: non è possibile connettersi a Remote Desktop, risolvere i problemi di Remote Desktop, Remote Desktop non riesce a connettersi, errori di Remote Desktop, risoluzione dei problemi di Remote Desktop, problemi di Remote Desktop
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 91f15e32866cca008553286f7585247909d9a4ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87009867"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Procedura dettagliata per la risoluzione dei problemi di connessione di Desktop remoto con le macchine virtuali Windows in Azure
Questo articolo contiene una procedura dettagliata sulla risoluzione dei problemi per diagnosticare e risolvere errori di Desktop remoto complessi per le macchine virtuali di Azure basate su Windows.

> [!IMPORTANT]
> Per eliminare gli errori più comuni di Desktop remoto, verificare di aver letto l'articolo relativo alla [risoluzione dei problemi di base di Desktop remoto](troubleshoot-rdp-connection.md) prima di procedere.

È possibile che vanga visualizzato un messaggio di errore di Desktop remoto che non corrisponde a uno dei messaggi di errore specifici descritti nella [guida alla risoluzione dei problemi di base di Desktop remoto](troubleshoot-rdp-connection.md). Seguire questa procedura per capire il motivo per cui il client Desktop remoto (o RDP) non riesce a connettersi al servizio RDP nella VM di Azure.


Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottieni supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [domande frequenti sul supporto Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Componenti di una connessione Desktop remoto
Di seguito sono riportati i componenti di una connessione RDP:

![Diagramma che mostra i componenti necessari in una connessione di Desktop remoto (RDP).](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Prima di procedere, può essere utile riflettere su cosa è stato modificato dall'ultima connessione Desktop remoto riuscita alla VM. Ad esempio:

* L'indirizzo IP pubblico della VM o il servizio cloud contenente la VM, detto anche indirizzo IP virtuale o [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address), è cambiato. L'errore RDP può essere causato dal fatto che la cache del client DNS ha ancora l' *indirizzo IP precedente* registrato per il nome DNS. Svuotare la cache del client DNS e riprovare a connettersi alla VM. Oppure, provare a connettersi direttamente con il nuovo indirizzo VIP.
* Per gestire le connessioni Desktop remoto si usa un'applicazione di terze parti anziché la connessione generata dal portale di Azure. Verificare che la configurazione dell'applicazione includa la porta TCP corretta per il traffico di Desktop remoto. È possibile controllare questa porta per una macchina virtuale classica nel [portale di Azure](https://portal.azure.com) facendo clic sulle impostazioni della VM > Endpoint.

## <a name="preliminary-steps"></a>Operazioni preliminari
Prima di procedere alla risoluzione dei problemi dettagliata

* Controllare lo stato della macchina virtuale nel portale di Azure per verificare l'eventuale presenza di errori evidenti.
* Attenersi alla [procedura di correzione rapida per gli errori RDP comuni nella Guida alla risoluzione dei problemi di base](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Per le immagini personalizzate, assicurarsi che il disco rigido virtuale sia preparato correttamente prima di caricarlo. Per altre informazioni, vedere [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](../windows/prepare-for-upload-vhd-image.md).


Provare a riconnettersi alla VM tramite Desktop remoto dopo aver eseguito questi passaggi.

## <a name="detailed-troubleshooting-steps"></a>Procedura di risoluzione dei problemi dettagliata
Il client Desktop remoto potrebbe non essere in grado di raggiungere il servizio Desktop remoto nella VM di Azure a causa di problemi nelle origini seguenti:

* [Computer client Desktop remoto](#source-1-remote-desktop-client-computer)
* [Dispositivo periferico dell’Intranet dell’organizzazione](#source-2-organization-intranet-edge-device)
* [Endpoint del servizio cloud ed elenco di controllo di accesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Gruppi di sicurezza di rete](#source-4-network-security-groups)
* [Macchina virtuale di Azure basata su Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Origine 1: computer client Desktop remoto
Verificare che il computer sia in grado di stabilire connessioni Desktop remoto a un altro computer locale basato su Windows.

![Un diagramma dei componenti in una connessione di Desktop remoto (RDP) con il client RDP evidenziato e una freccia che punta a un altro computer locale che indica una connessione.](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Se non è possibile, verificare le impostazioni seguenti sul computer:

* Un'impostazione firewall locale che blocca il traffico di Desktop remoto.
* Software proxy client installato localmente che impedisce le connessioni Desktop remoto.
* Software di monitoraggio della rete installato localmente che impedisce le connessioni Desktop remoto.
* Altri tipi di software di sicurezza che effettuano il monitoraggio del traffico o consentono/non consentono tipi di traffico che impediscano le connessioni Desktop remoto.

In tutti questi casi, disabilitare temporaneamente il software e provare a connettersi a un computer locale tramite Desktop remoto. Se si riesce a trovare la causa effettiva in questo modo, contattare l'amministratore di rete per correggere le impostazioni del software in modo da consentire le connessioni Desktop remoto.

## <a name="source-2-organization-intranet-edge-device"></a>Origine 2: dispositivo periferico dell’Intranet dell’organizzazione
Verificare che un computer connesso direttamente a Internet possa eseguire connessioni Desktop remoto alla macchina virtuale di Azure.

![Un diagramma dei componenti in una connessione di Desktop remoto (RDP) con un client RDP connesso a Internet evidenziato e una freccia che punta a una macchina virtuale di Azure che indica una connessione.](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Se non si ha un computer connesso direttamente a Internet, creare una nuova macchina virtuale di Azure in un gruppo di risorse o servizio cloud per eseguire un test. Per ulteriori informazioni, vedere [Creare una macchina virtuale con Windows in Azure](../windows/quick-create-portal.md). Dopo aver completato il test, eliminare la macchina virtuale e il gruppo di risorse o il servizio cloud.

Se è possibile creare una connessione Desktop remoto con un computer collegato direttamente a Internet, controllare se nel dispositivo periferico dell’Intranet dell’organizzazione si verificano le seguenti condizioni:

* Firewall interno che blocca le connessioni HTTPS a Internet.
* Server proxy che impedisce le connessioni Desktop remoto.
* Software di rilevamento delle intrusioni o software per il monitoraggio della rete in esecuzione sui dispositivi nella rete perimetrale che impedisce le connessioni Desktop remoto.

Rivolgersi all'amministratore di rete per correggere le impostazioni del dispositivo periferico dell’Intranet dell’organizzazione per consentire connessioni Desktop remoto a Internet basate su HTTPS.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origine 3: endpoint del servizio cloud e ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Per le VM create mediante il modello di distribuzione classico, verificare che un'altra VM di Azure che si trova nello stesso servizio cloud o rete virtuale sia in grado di stabilire connessioni Desktop remoto alla VM di Azure.

![Un diagramma dei componenti in una connessione di Desktop remoto (RDP) con una macchina virtuale di Azure evidenziata e una freccia che punta a un'altra VM di Azure all'interno dello stesso servizio cloud che indica una connessione.](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Per le macchine virtuali create in Gestione risorse, andare su [Origine 4: gruppi di sicurezza di rete](#source-4-network-security-groups).

Se non si dispone di un'altra macchina virtuale nello stesso servizio cloud o rete virtuale, crearne una. Seguire la procedura riportata in [Creazione rapida di una macchina virtuale che esegue Linux in Azure](../windows/quick-create-portal.md). Eliminare la macchina virtuale di test al completamento del test.

Se è possibile connettersi tramite Desktop remoto a una macchina virtuale nello stesso servizio cloud o rete virtuale, verificare le impostazioni seguenti:

* La configurazione dell'endpoint per il traffico di Desktop remoto nella VM di destinazione: la porta TCP privata dell'endpoint deve corrispondere alla porta TCP in cui è in ascolto il servizio Desktop remoto della macchina virtuale (l'impostazione predefinita è 3389).
* L'ACL per l'endpoint del traffico di Desktop remoto sulla VM di destinazione: gli ACL consentono di specificare il traffico in ingresso da Internet consentito o negato in base al relativo indirizzo IP di origine. ACL configurati in modo errato possono impedire il traffico di Desktop remoto in ingresso all'endpoint. Verificare gli ACL per assicurarsi che il traffico in ingresso dagli indirizzi IP pubblici del proxy o da altri server periferici sia consentito. Per ulteriori informazioni, vedere [che cos'è un elenco di controllo di accesso di rete (ACL)?](/previous-versions/azure/virtual-network/virtual-networks-acl)

Per controllare se l'endpoint è l'origine del problema, rimuovere l'endpoint corrente e creare un nuovo endpoint, scegliendo una porta casuale nell'intervallo tra 49152 e 65535 per il numero di porta esterna. Per ulteriori informazioni, vedere [come configurare gli endpoint in una macchina virtuale](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

## <a name="source-4-network-security-groups"></a>Origine 4: gruppi di sicurezza di rete
I gruppi di sicurezza di rete consentono un controllo più granulare del traffico in entrata e in uscita consentito. È possibile creare regole che si estendono alle subnet e ai servizi cloud in una rete virtuale di Azure.

Usare la [verifica del flusso IP](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) per verificare che una regola in un gruppo di sicurezza di rete blocchi il traffico verso o da una macchina virtuale. È anche possibile esaminare le regole del gruppo di sicurezza effettive per verificare che la regola NSG di consenso in ingresso esista e abbia la priorità per la porta RDP, ovvero la porta 3389 predefinita. Per altre informazioni, vedere [Uso di regole di sicurezza effettive per risolvere i problemi di flusso del traffico delle VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Origine 5: Macchina virtuale di Azure basata su Windows
![Un diagramma dei componenti in una connessione di Desktop remoto (RDP) con una macchina virtuale di Azure evidenziata in un servizio cloud e un messaggio che potrebbe essere una "possibile fonte di problemi". Una linea blu indica che le regole del gruppo di sicurezza di rete potrebbero bloccare il traffico da o verso la macchina virtuale di Azure.](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Seguire le istruzioni riportate in [questo articolo](./reset-rdp.md). per reimpostare il servizio Desktop remoto nella macchina virtuale:

* Verrà abilitata la regola predefinita "Desktop remoto" di Windows Firewall (porta TCP 3389).
* Verranno abilitate le connessioni Desktop remoto impostando il valore del Registro di sistema HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections su 0.

Provare nuovamente la connessione dal computer. Se non si è ancora in grado di connettersi tramite Desktop remoto, verificare se sono presenti i problemi seguenti:

* Il servizio Desktop remoto non è in esecuzione nella VM di destinazione.
* Il servizio Desktop remoto non è in ascolto sulla porta TCP 3389.
* Windows Firewall o un altro firewall locale dispone di una regola in uscita che impedisce il traffico di Desktop remoto.
* Il software di rilevamento delle intrusioni o il software per il monitoraggio della rete in esecuzione nella macchina virtuale di Azure impedisce le connessioni Desktop remoto.

Per le VM create usando il modello di distribuzione classico, usare una sessione remota di Azure PowerShell per connettersi alla macchina virtuale di Azure. In primo luogo, è necessario installare un certificato per il servizio cloud di hosting della macchina virtuale. Passare alla pagina dello script per [Configure Secure Remote PowerShell Access to Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) (Configurare l'accesso remoto PowerShell sicuro alle macchine virtuali di Azure) e scaricare il file di script **InstallWinRMCertAzureVM.ps1** nel computer locale.

Successivamente, installare Azure PowerShell, se non è stato già installato. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/).

Successivamente, aprire un prompt dei comandi di Azure PowerShell e modificare la cartella corrente nel percorso del file di script **InstallWinRMCertAzureVM.ps1** . Per eseguire uno script di Azure PowerShell, è necessario impostare i criteri di esecuzione corretti. Eseguire il comando **Get-ExecutionPolicy** per determinare il livello di criterio corrente. Per informazioni sull'impostazione del livello appropriato, vedere [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-5.1).

Immettere quindi il nome della sottoscrizione di Azure, il nome del servizio cloud e il nome della macchina virtuale (rimuovendo i caratteri < e >) e infine eseguire questi comandi.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

È possibile ottenere il nome della sottoscrizione corretto dalla proprietà *SubscriptionName* della visualizzazione del comando **Get-AzureSubscription**. È possibile ottenere il nome del servizio cloud per la macchina virtuale dalla colonna *ServiceName* della visualizzazione del comando **Get-AzureVM**.

Verificare di avere il nuovo certificato. Aprire uno snap-in Certificati per l'utente corrente e cercare nella cartella **Autorità di certificazione radice attendibili\Certificati**. Verrà visualizzato un certificato con il nome DNS del servizio cloud nella colonna Rilasciato a (esempio: cloudservice4testing.cloudapp.net).

Successivamente, avviare una sessione remota di Azure PowerShell utilizzando questi comandi.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Dopo aver immesso le credenziali di amministratore valide, verrà visualizzato un prompt di Azure PowerShell simile al seguente:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

La prima parte di questo prompt è il nome del servizio cloud che contiene la VM di destinazione, che può essere diverso da "cloudservice4testing.cloudapp.net". È ora possibile inviare comandi di Azure PowerShell per questo servizio cloud, per analizzare i problemi menzionati in precedenza e apportare correzioni alla configurazione.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Per correggere manualmente Servizi Desktop remoto in ascolto sulla porta TCP
Al prompt della sessione remota di Azure PowerShell, eseguire questo comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

La proprietà PortNumber indica il numero di porta corrente. Se necessario, modificare il numero di porta Desktop remoto nuovamente al valore predefinito (3389) utilizzando questo comando.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Verificare che la porta sia stata modificata in 3389 utilizzando questo comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Chiudere la sessione remota di Azure PowerShell utilizzando questo comando.

```powershell
Exit-PSSession
```

Verificare che anche l'endpoint Desktop remoto per la VM di Azure usi la porta TCP 3398 come porta interna. Riavviare la VM di Azure e tentare nuovamente la connessione Desktop remoto.

## <a name="additional-resources"></a>Risorse aggiuntive
[Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali di Windows](./reset-rdp.md)

[Come installare e configurare Azure PowerShell](/powershell/azure/)

[Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)

[Risolvere i problemi di accesso a un'applicazione in esecuzione in una macchina virtuale di Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
