---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: b838e411e2795405c439a4107daab7aa8f033059
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76020854"
---
## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output nel contenitore BLOB adftutorial, quindi copia il file emp.txt dalla cartella di input a quella di output. 

1. Nella pagina del contenitore **adftutorial** del portale di Azure fare clic su **Aggiorna** per visualizzare la cartella di output. 
    
    ![Aggiorna](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)
2. Fare clic su **output** nell'elenco di cartelle. 
2. Verificare che **emp.txt** venga copiato nella cartella di output. 

    ![Aggiorna](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Pulire le risorse
È possibile eseguire la pulizia delle risorse create nel corso della guida introduttiva in due modi. Si può eliminare il [gruppo di risorse di Azure](../articles/azure-resource-manager/management/overview.md), in modo da includere tutte le risorse del gruppo. Se invece si vogliono mantenere intatte le altre risorse, eliminare solo la data factory creata in questa esercitazione.

Se si elimina un gruppo di risorse, vengono eliminate tutte le risorse in esso contenute, incluse le data factory. Eseguire il comando seguente per eliminare l'intero gruppo di risorse: 
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Nota: l'eliminazione di un gruppo di risorse può richiedere tempo. Attendere il completamento del processo.

Per eliminare solo la data factory e non l'intero gruppo di risorse, eseguire il comando seguente: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```