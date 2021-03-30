---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67180540"
---
### <a name="troubleshoot-azure-diagnostics"></a>Risolvere i problemi relativi a Diagnostica di Azure

Se viene visualizzato il messaggio di errore seguente, il provider di risorse Microsoft.insights non è registrato:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Per registrare il provider di risorse, seguire questa procedura nel portale di Azure:

1.  Nel pannello di navigazione a sinistra fare clic su *Sottoscrizioni*.
2.  Selezionare la sottoscrizione identificata nel messaggio di errore.
3.  Fare clic su *Provider di risorse*.
4.  Trovare il provider *Microsoft.insights*.
5.  Fare clic sul collegamento *Registra*.

![Registrare il provider di risorse Microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Dopo aver registrato il provider di risorse *Microsoft.insights*, provare di nuovo a configurare la diagnostica.


Se in PowerShell viene visualizzato il messaggio di errore seguente è necessario aggiornare la versione di PowerShell:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Aggiornare la versione di Azure PowerShell seguendo le istruzioni riportate nell'articolo [Installare Azure PowerShell](/powershell/azure/install-az-ps).
