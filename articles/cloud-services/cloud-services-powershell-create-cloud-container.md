---
title: Creare un contenitore del servizio cloud con PowerShell | Documentazione Microsoft
description: Questo articolo illustra come creare un contenitore del servizio cloud con PowerShell. Il contenitore ospita il ruolo Web e il ruolo di lavoro.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: d40a5b64cc8018f45bf08158ce808b2baae27962
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049093"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Usare un prompt dei comandi di Azure PowerShell per creare un contenitore del servizio cloud vuoto

Questo articolo illustra come creare velocemente un contenitore di Servizi cloud usando i cmdlet di Azure PowerShell. Eseguire i passaggi seguenti:

1. Installare i cmdlet di Microsoft Azure PowerShell dalla pagina dei [download di Azure PowerShell](https://aka.ms/webpi-azps) .
2. Aprire il prompt dei comandi di PowerShell.
3. Usare [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-4.0.0) per accedere.

   > [!NOTE]
   > Per altre istruzioni sull'installazione del cmdlet di Azure PowerShell e sulla connessione alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/).
   >
   >
4. Usare il cmdlet **New-AzureService** per creare un contenitore del servizio cloud vuoto.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Per richiamare il cmdlet, seguire questo esempio:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Per altre informazioni sulla creazione del servizio cloud di Azure, eseguire:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Passaggi successivi

* Per gestire la distribuzione del servizio cloud, vedere i comandi [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService?view=azuresmps-4.0.0) e [Set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice?view=azuresmps-4.0.0). Per altre informazioni, è anche possibile vedere [Come configurare i servizi cloud](cloud-services-how-to-configure-portal.md) .
* Per pubblicare il progetto del servizio cloud in Azure, vedere l'esempio di codice **PublishCloudService.ps1** riportato nel [repository archiviato per i servizi cloud](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).