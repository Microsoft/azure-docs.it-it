---
title: Ottenere notifiche di manutenzione usando l'interfaccia della riga di comando
description: Visualizzare le notifiche di manutenzione per le macchine virtuali in esecuzione in Azure e avviare la manutenzione Self-Service usando l'interfaccia della riga di comando di Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 289733c4cee23a37c26df0b613a470925756f0eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84674840"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Gestione delle notifiche di manutenzione pianificata tramite l'interfaccia della riga di comando

**Questo articolo si applica alle macchine virtuali che eseguono sia Linux che Windows.**

È possibile usare l'interfaccia della riga di comando per vedere quando le macchine virtuali sono pianificate per la [manutenzione](maintenance-notifications.md). Le informazioni di manutenzione pianificate sono disponibili in [AZ VM Get-instance-View](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Le informazioni vengono restituite solo se è presente una manutenzione pianificata. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Avvia manutenzione

La chiamata seguente avvierà la manutenzione in una macchina virtuale se `IsCustomerInitiatedMaintenanceAllowed` è impostato su true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Distribuzioni classiche

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Se si dispone ancora di macchine virtuali legacy distribuite tramite il modello di distribuzione classico, è possibile usare l'interfaccia della riga di comando classica di Azure per eseguire query per le macchine virtuali e avviare la manutenzione.

Verificare che sia attiva la modalità corretta per operare con una VM classica digitando:

```
azure config mode asm
```

Per ottenere lo stato di manutenzione della macchina virtuale denominata *myVM*, digitare:

```
azure vm show myVM 
``` 

Per avviare la manutenzione della macchina virtuale classica denominata *myVM* nel servizio *myService* e nella distribuzione *myDeployment*, digitare:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile gestire la manutenzione pianificata usando il [Azure PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md).
