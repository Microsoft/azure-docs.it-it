---
title: Applicare la licenza di Windows alle macchine virtuali host sessione-Azure
description: Viene descritto come applicare la licenza di Windows per le VM desktop virtuali Windows.
author: ChristianMontoya
ms.topic: how-to
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 5f3749be36f5f035e49fcb862f92180e4902101f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010141"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Applicare la licenza di Windows alle macchine virtuali host sessione

I clienti che dispongono di una licenza corretta per l'esecuzione di carichi di lavoro di desktop virtuali Windows sono idonei per l'applicazione di una licenza Windows alle macchine virtuali host della sessione ed eseguono le licenze senza pagare per un'altra licenza. Per ulteriori informazioni, vedere [prezzi di desktop virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Modalità di utilizzo della licenza per desktop virtuali Windows
Gestione licenze Desktop virtuali Windows consente di applicare una licenza a qualsiasi macchina virtuale Windows o Windows Server registrata come host sessione in un pool host e riceve le connessioni utente. Questa licenza non si applica alle macchine virtuali in esecuzione come server di condivisione file, controller di dominio e così via.

Esistono diversi modi per utilizzare la licenza per desktop virtuali di Windows:
- È possibile creare un pool host e le relative macchine virtuali host sessione usando l' [offerta di Azure Marketplace](./create-host-pools-azure-marketplace.md). Per le macchine virtuali create in questo modo viene automaticamente applicata la licenza.
- È possibile creare un pool host e le relative macchine virtuali host sessione usando il [modello di Azure Resource Manager di GitHub](./virtual-desktop-fall-2019/create-host-pools-arm-template.md). Per le macchine virtuali create in questo modo viene automaticamente applicata la licenza.
- È possibile applicare una licenza a una macchina virtuale host sessione esistente. A tale scopo, seguire prima le istruzioni in [creare un pool di host con PowerShell](./create-host-pools-powershell.md) per creare un pool host e le macchine virtuali associate, quindi tornare a questo articolo per apprendere come applicare la licenza.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Applicare una licenza Windows a una macchina virtuale host sessione
Verificare di aver prima [installato e configurato l'ultima versione di Azure PowerShell](/powershell/azure/). Eseguire il cmdlet di PowerShell seguente per applicare la licenza di Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Verificare che la macchina virtuale host della sessione stia usando il vantaggio della licenza
Dopo aver distribuito la macchina virtuale, eseguire questo cmdlet OT verificare il tipo di licenza:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Una macchina virtuale host sessione con la licenza di Windows applicata mostrerà un risultato simile al seguente:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Nelle VM senza la licenza di Windows applicata verrà visualizzato un risultato simile al seguente:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Eseguire il cmdlet seguente per visualizzare un elenco di tutte le VM host sessione per le quali è stata applicata la licenza di Windows nella sottoscrizione di Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
