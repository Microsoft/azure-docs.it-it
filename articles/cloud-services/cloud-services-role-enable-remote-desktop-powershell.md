---
title: Usare PowerShell per abilitare Desktop remoto per un ruolo
description: Come configurare l'applicazione del servizio cloud di Azure utilizzando PowerShell per consentire le connessioni Desktop remoto
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5b1650edb575de8fd59ad2495dafcd628a717c02
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102610400"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-powershell"></a>Abilitare Connessione Desktop remoto per un ruolo in servizi cloud di Azure (versione classica) con PowerShell

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Desktop remoto consente di accedere al desktop di un ruolo in esecuzione in Azure. È possibile usare una connessione Desktop remoto per risolvere e diagnosticare i problemi dell'applicazione mentre è in esecuzione.

Questo articolo descrive come abilitare Desktop remoto nei ruoli dei servizi cloud con PowerShell. Per i prerequisiti necessari per questo articolo, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/) . PowerShell usa l'estensione di Desktop remoto per poter abilitare Desktop remoto dopo la distribuzione dell'applicazione.

## <a name="configure-remote-desktop-from-powershell"></a>Configurare Remote Desktop da PowerShell
Il cmdlet [Set AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) consente di abilitare Desktop remoto su tutti o specifici ruoli di distribuzione del servizio cloud. Il cmdlet consente di specificare il nome utente e la password per l'utente Desktop remoto tramite il parametro *Credential* che accetta un oggetto PSCredential.

Se si usa PowerShell in modo interattivo è possibile impostare facilmente l'oggetto PSCredential chiamando il cmdlet [Get-Credentials](/powershell/module/microsoft.powershell.security/get-credential) .

```powershell
$remoteusercredentials = Get-Credential
```

Il comando visualizza una finestra di dialogo in cui immettere in modo sicuro il nome utente e la password per l'utente remoto.

Poiché PowerShell è utile negli scenari di automazione, è anche possibile configurare l'oggetto **PSCredential** in modo che non richieda l'interazione dell'utente. In questo caso, è necessario configurare una password di protezione. Per iniziare, specificare una password non crittografata e convertirla in una stringa sicura con [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). Quindi è necessario convertire questa stringa sicura in una stringa standard crittografata con [ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring). Ora è possibile salvare questa stringa crittografata standard in un file con [Set-Content](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176959(v=technet.10)).

È anche possibile creare un file della password di protezione in modo che non sia necessario digitarla ogni volta. Inoltre, un file della password di protezione è preferibile a un file di testo normale. Usare il codice di PowerShell seguente per creare un file della password sicura:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Quando si imposta la password, assicurarsi che soddisfi i [requisiti di complessità](/previous-versions/windows/it-pro/windows-server-2003/cc786468(v=ws.10)).

Per creare l'oggetto credential dal file della password di protezione, è necessario leggere il contenuto del file e riconvertirlo in una stringa sicura con [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring).

Il cmdlet [Set AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) accetta anche un parametro *Expiration* che specifica un valore **DateTime** che specifica la scadenza dell'account utente. Ad esempio, è possibile impostare la scadenza dell'account alcuni giorni dopo la data e l'ora correnti.

Questo esempio di PowerShell illustra come impostare l'estensione di Desktop remoto in un servizio cloud:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
È anche possibile specificare lo slot di distribuzione e i ruoli per i quali si vuole abilitare Desktop remoto. Se questi parametri non vengono specificati, il cmdlet abilita Desktop remoto su tutti i ruoli nello slot di distribuzione **Produzione** .

L'estensione per il Desktop remoto è associata a una distribuzione. Se si crea una nuova distribuzione per il servizio, si dovrà abilitare Desktop remoto in quella distribuzione. Se si vuole che Desktop remoto sia sempre abilitato, considerare la possibilità di integrare gli script di PowerShell nel flusso di lavoro di distribuzione.

## <a name="remote-desktop-into-a-role-instance"></a>Desktop remoto in un'istanza del ruolo

Il cmdlet [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) viene usato per integrare Desktop remoto in una specifica istanza del ruolo del servizio cloud. È possibile usare il parametro *LocalPath* per scaricare il file RDP in locale. In alternativa si può usare il parametro *Launch* per avviare direttamente la finestra di dialogo Connessione Desktop remoto per accedere all'istanza del ruolo del servizio cloud.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Verificare se l'estensione di Desktop remoto è attivata in un servizio

Il cmdlet [Get AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) indica che Desktop remoto è abilitato o disabilitato in una distribuzione del servizio. Il cmdlet restituisce il nome utente per l'utente Desktop remoto e i ruoli per quali è abilitata l'estensione di Desktop remoto. Per impostazione predefinita, ciò avviene nello slot di distribuzione ed è possibile scegliere di usare in alternativa lo slot di staging.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Rimuovere l'estensione di Desktop remoto da un servizio

Se già stata abilitata l'estensione di Desktop remoto in una distribuzione ed è necessario aggiornare le impostazioni di Desktop remoto, occorre prima di tutto rimuovere l'estensione e quindi riabilitarla con le nuove impostazioni. Ad esempio, se si vuole impostare una nuova password per l'account utente remoto o l'account scaduto. Questa operazione è necessaria nelle distribuzioni esistenti in cui è abilitata l'estensione di Desktop remoto. Per le nuove distribuzioni è possibile applicare semplicemente l'estensione direttamente.

Per rimuovere l'estensione di Desktop remoto dalla distribuzione, è possibile usare il cmdlet [Remove AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension) . È anche possibile specificare lo slot di distribuzione e il ruolo dal quale si vuole rimuovere l'estensione di Desktop remoto.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Per rimuovere completamente la configurazione dell'estensione, è necessario chiamare il cmdlet *remove* con il parametro **UninstallConfiguration** .
>
> Il parametro **UninstallConfiguration** disinstalla qualsiasi configurazione dell'estensione applicata al servizio. Ogni configurazione dell'estensione è associata alla configurazione del servizio. Se si chiama il cmdlet *remove* senza **UninstallConfiguration**, la <mark>distribuzione</mark> verrà dissociata dalla configurazione dell'estensione, che verrà così rimossa. La configurazione dell'estensione rimane comunque associata al servizio.

## <a name="additional-resources"></a>Risorse aggiuntive

[Come configurare i servizi cloud](cloud-services-how-to-configure-portal.md)