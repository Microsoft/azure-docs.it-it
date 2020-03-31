---
title: Configurazione post-distribuzione tramite estensioni
description: Informazioni su come usare le estensioni dei modelli di Azure Resource Manager per fornire configurazioni post-distribuzione.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023498"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fornire configurazioni post-distribuzione tramite le estensioni

Le estensioni del modello sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione nelle risorse di Azure. Le più conosciute sono le estensioni della macchina virtuale. Vedere [Estensioni e funzionalità della macchina virtuale per Windows](../../virtual-machines/extensions/features-windows.md) e [Estensioni e funzionalità della macchina virtuale per Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Estensioni

Le estensioni esistenti sono:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql servers/databases/extensions](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Per scoprire tutte le estensioni disponibili, selezionare la [documentazione di riferimento dei modelli](https://docs.microsoft.com/azure/templates/). In **Filtra in base al titolo** immettere **estensione**.

Per informazioni su come usare queste estensioni, vedere:

- [Esercitazione: Distribuire le estensioni](template-tutorial-deploy-vm-extensions.md)delle macchine virtuali con i modelli di Azure Resource Manager.
- [Esercitazione: Importare file BACPAC di SQL con modelli di Azure Resource Manager](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire estensioni di macchina virtuale con modelli di Azure Resource Manager](template-tutorial-deploy-vm-extensions.md)
