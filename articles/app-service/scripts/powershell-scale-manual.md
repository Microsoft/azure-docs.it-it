---
title: "PowerShell: Ridimensionare un'App Web manualmente"
description: Informazioni su come usare Azure PowerShell per automatizzare la distribuzione e la gestione di Servizio app. Questo esempio illustra come ridimensionare un'app manualmente.
author: msangapu-msft
tags: azure-service-management
ms.assetid: de5d4285-9c7d-4735-a695-288264047375
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: 1b88e5928d276b58339cb0266505295808bc7a7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335484"
---
# <a name="scale-a-web-app-manually-using-powershell"></a>Ridimensionare manualmente un'app Web con PowerShell

In questo scenario si apprenderà come creare un gruppo di risorse, un piano di servizio app e un'App Web. Quindi si dimensionerà il piano di servizio app da una singola istanza a più istanze.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/scale-manual/scale-manual.ps1 "Scale a web app manually")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo aver eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, l'App Web e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Consente di creare un piano di servizio app. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Modifica la configurazione di un piano di servizio app. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crea un'App Web. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifica la configurazione di un'app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
