---
title: Personalizzare il feed per gli utenti di desktop virtuali Windows-Azure
description: Come personalizzare il feed per gli utenti di desktop virtuali Windows con i cmdlet di PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e47486f29537cb948aaae7cf17e97bae14b60700
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084295"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>Personalizzare il feed per gli utenti di desktop virtuali Windows

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).

È possibile personalizzare il feed in modo che le risorse RemoteApp e desktop remoto vengano visualizzate in modo riconoscibile per gli utenti.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già stato scaricato e installato il modulo PowerShell per desktop virtuale di Windows. In caso contrario, seguire le istruzioni riportate in [configurare il modulo PowerShell](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Personalizzare il nome visualizzato per un RemoteApp

È possibile modificare il nome visualizzato per un RemoteApp pubblicato impostando il nome descrittivo. Per impostazione predefinita, il nome descrittivo corrisponde al nome del programma RemoteApp.

Per recuperare un elenco di RemoteApp pubblicati per un gruppo di app, eseguire il cmdlet di PowerShell seguente:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Per assegnare un nome descrittivo a un RemoteApp, eseguire il cmdlet seguente con i parametri obbligatori:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Si immagini, ad esempio, che le applicazioni correnti siano state recuperate con il cmdlet di esempio seguente:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

L'output avrà un aspetto simile al seguente:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Per aggiornare il nome descrittivo, eseguire questo cmdlet:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Per confermare che il nome descrittivo è stato aggiornato correttamente, eseguire il cmdlet seguente:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

Il cmdlet dovrebbe fornire l'output seguente:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Personalizzare il nome visualizzato per un Desktop remoto

È possibile modificare il nome visualizzato per un desktop remoto pubblicato impostando un nome descrittivo. Se è stato creato manualmente un pool di host e un gruppo di app desktop tramite PowerShell, il nome descrittivo predefinito è "sessione Desktop". Se è stato creato un pool host e un gruppo di app desktop tramite il modello di Azure Resource Manager GitHub o l'offerta di Azure Marketplace, il nome descrittivo predefinito corrisponde al nome del pool host.

Per recuperare la risorsa Desktop remoto, eseguire il cmdlet PowerShell seguente:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Per assegnare un nome descrittivo alla risorsa Desktop remoto, eseguire il cmdlet PowerShell seguente:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Personalizzare un nome visualizzato in portale di Azure

È possibile modificare il nome visualizzato per un desktop remoto pubblicato impostando un nome descrittivo usando il portale di Azure.

1. Accedere al portale di Azure all'indirizzo <https://portal.azure.com>.

2. Cercare **desktop virtuale di Windows**.

3. In servizi selezionare **desktop virtuale di Windows**.

4. Nella pagina desktop virtuale di Windows selezionare **gruppi di applicazioni** sul lato sinistro della schermata, quindi selezionare il nome del gruppo di app che si desidera modificare. Se, ad esempio, si desidera modificare il nome visualizzato del gruppo di app desktop, selezionare il gruppo di app denominato **Desktop**.

5. Selezionare **applicazioni** nel menu sul lato sinistro della schermata.

6. Selezionare l'applicazione che si desidera aggiornare, quindi immettere un nuovo **nome visualizzato**.

7. Selezionare **Salva**. L'applicazione modificata dovrebbe ora visualizzare il nome aggiornato.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato personalizzato il feed per gli utenti, è possibile accedere a un client desktop virtuale di Windows per testarlo. A tale scopo, continuare con le procedure per connettersi a desktop virtuali Windows:

 * [Connessione con Windows 10 o Windows 7](connect-windows-7-10.md)
 * [Connettersi con il client Web](connect-web.md)
 * [Connettersi con il client Android](connect-android.md)
 * [Connettersi con il client iOS](connect-ios.md)
 * [Connettersi con il client macOS](connect-macos.md)
