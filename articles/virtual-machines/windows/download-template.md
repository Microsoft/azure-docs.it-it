---
title: Scaricare il modello per una macchina virtuale di Azure
description: Scaricare il modello per una VM usando il portale o PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 5b7e50ebe6f09de2555af03a47641ef6ca92e92a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288304"
---
# <a name="download-the-template-for-a-vm"></a>Scaricare il modello per una VM
Quando si crea una macchina virtuale in Azure con il portale o con PowerShell, viene creato automaticamente un modello di Resource Manager. È possibile usare questo modello per duplicare rapidamente una distribuzione. Il modello contiene informazioni su tutte le risorse in un gruppo di risorse. Per una macchina virtuale, questo significa che il modello contiene tutto ciò che viene creato in supporto della macchina virtuale all'interno del gruppo di risorse, comprese le risorse di rete.

## <a name="download-the-template-using-the-portal"></a>Scaricare il modello usando il portale
1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra selezionare **Macchine virtuali**.
3. Selezionare la macchina virtuale dall'elenco.
4. Selezionare **Esporta modello**.
5. Selezionare **Scarica** dal menu nella parte superiore e salvare il file con estensione zip nel computer locale.
6. Aprire il file .zip ed estrarne i file in una cartella. Il file con estensione zip contiene:
   
   * parameters.json
   * template.json

Il file template.json è il modello.

## <a name="download-the-template-using-powershell"></a>Scaricare il modello con PowerShell
È anche possibile scaricare il file di modello .json usando il cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup). È possibile usare il parametro `-path` per fornire il nome file e il percorso per il file .json. In questo esempio viene illustrato come scaricare il modello per il gruppo di risorse denominato **myResourceGroup** nella cartella **C:\users\public\downloads** del computer locale.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla distribuzione di risorse usando i modelli, vedere [Procedura dettagliata del modello di Resource Manager](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
