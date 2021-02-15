---
title: Caricare i dati in modo incrementale tramite PowerShell
description: Questo script di PowerShell Mostra come usare Azure Data Factory per copiare i dati in modo incrementale da un database SQL di Azure a un archivio BLOB di Azure.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 736403696ec340e547547458cb62e243e6e660b9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389842"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>Script PowerShell: caricare i dati in modo incrementale usando Azure Data Factory

Questo script PowerShell di esempio carica solo i record nuovi o aggiornati da un archivio dati di origine a un archivio dati sink dopo la copia iniziale completa dei dati dall'origine al sink.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Vedere [Esercitazione: copia incrementale](../tutorial-incremental-copy-powershell.md#prerequisites) per i prerequisiti per l'esecuzione di questo esempio. 

## <a name="sample-script"></a>Script di esempio

> [!IMPORTANT]
> Questo script crea file JSON che definiscono entità di Data Factory (servizio collegato, set di dati e pipeline) nella cartella c:\ del disco rigido.

:::code language="powershell" source="~/powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1":::

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo aver eseguito lo script di esempio, è possibile eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse associate a questo:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Per rimuovere la data factory dal gruppo di risorse, eseguire il comando seguente: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Creare una data factory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Crea un servizio collegato nella data factory. Un servizio collegato collega un archivio dati o un ambiente di calcolo a una data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Crea un set di dati nella data factory. Il set di dati rappresenta l'input e l'output per un'attività in una pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Crea una pipeline nella data factory. Una pipeline contiene una o più attività che eseguono una determinata operazione. In questa pipeline l'attività di copia esegue la copia dei dati da una posizione a un'altra in un'archiviazione BLOB di Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Crea un'esecuzione per la pipeline. In altre parole, esegue la pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Ottiene i dettagli dell'esecuzione dell'attività nella pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).

Altri esempi di script PowerShell di Azure Data Factory sono disponibili in [Azure Data Factory PowerShell scripts](../samples-powershell.md) (Script PowerShell per Azure Data Factory).