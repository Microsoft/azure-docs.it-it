---
title: 'Script di PowerShell: impostare le dimensioni di VM consentite in Azure Lab Services | Microsoft Docs'
description: Questo articolo include un esempio di script di PowerShell che imposta le dimensioni di macchina virtuale (VM) consentite in Azure Lab Services.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998134"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Usare PowerShell per impostare le dimensioni di VM consentite in Azure Lab Services

Questo esempio di script di PowerShell imposta le dimensioni di macchina virtuale (VM) consentite in Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisiti
* **Un lab**. Per usare lo script è necessario disporre di un lab esistente. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| Find-AzResource | Cerca le risorse in base a parametri specificati. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ottiene le risorse. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica una risorsa. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Creare una risorsa. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).

Altri esempi di script di PowerShell per Azure Lab Services sono disponibili in [Azure Lab Services PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Lab Services).
