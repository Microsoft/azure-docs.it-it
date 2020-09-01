---
title: Diagnostica di avvio per le macchine virtuali in Azure | Microsoft Docs
description: Panoramica delle due funzionalità di debug per le macchine virtuali in Azure
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: ef907aea940a4f01475cebf72528b9bcdde50f4d
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180236"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Come usare la diagnostica di avvio per risolvere i problemi delle macchine virtuali in Azure

Una macchina virtuale può entrare in uno stato non di avvio per diversi motivi. Per risolvere i problemi relativi alle macchine virtuali create con Gestione risorse modello di distribuzione, è possibile usare le funzionalità di debug seguenti: output della console e supporto dello screenshot per le macchine virtuali di Azure. 

Per le macchine virtuali Linux è possibile visualizzare l'output del log della console dal portale. Per le macchine virtuali Windows e Linux, Azure consente di visualizzare uno screenshot della macchina virtuale dall'hypervisor. Entrambe le funzionalità sono supportate per le macchine virtuali di Azure in tutte le aree. Si noti che le schermate e l'output possono richiedere fino a 10 minuti per essere visualizzate nell'account di archiviazione.

È possibile selezionare l'opzione **Diagnostica di avvio** per visualizzare il log e lo screenshot.

![Gestione risorse](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Errori di avvio comuni

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Sistema operativo non trovato](https://support.microsoft.com/help/4010142)
- [Errore di avvio o INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Abilitare la diagnostica in una macchina virtuale creata usando il portale di Azure

La procedura seguente si riferisce a una macchina virtuale creata usando il modello di distribuzione di Resource Manager.

Nella scheda **Gestione**, nella sezione **Monitoraggio**, verificare che l'opzione **Diagnostica di avvio** sia attivata. Nell'elenco a discesa **Account di archiviazione di diagnostica** selezionare un account di archiviazione in cui inserire i file di diagnostica.
 
![Creare una macchina virtuale](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> La funzionalità di diagnostica di avvio non supporta gli account di archiviazione premium. Se si usa l'account di archiviazione premium per la diagnostica di avvio, può verificarsi l'errore StorageAccountTypeNotSupported quando si avvia la macchina virtuale.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Distribuzione da un modello di Azure Resource Manager

Se si esegue la distribuzione da un modello di Azure Resource Manager, passare alla risorsa macchina virtuale e aggiungere la sezione del profilo di diagnostica. Impostare l'intestazione della versione dell'API su "2015-06-15" o versioni successive. La versione più recente è "2018-10-01".

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

Il profilo di diagnostica consente di selezionare l'account di archiviazione in cui si vogliono inserire questi log.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Per altre informazioni sulla distribuzione di risorse usando i modelli, vedere [Guida introduttiva: creare e distribuire modelli di Azure Resource Manager usando il portale di Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Abilitare la diagnostica di avvio in una macchina virtuale esistente 

Per abilitare la diagnostica di avvio in una macchina virtuale esistente, seguire la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com), quindi selezionare la macchina virtuale.
2. Nella sezione **Supporto e risoluzione dei problemi** selezionare **Diagnostica di avvio** e quindi scegliere la scheda **Impostazioni**.
3. In **Diagnostica di avvio** impostare lo stato su **Attivato** e nell'elenco a discesa **Account di archiviazione** selezionare un account di archiviazione. 
4. Salvare la modifica.

    ![Aggiornare una VM esistente](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Abilitare la diagnostica di avvio tramite l'interfaccia della riga di comando di Azure

Per abilitare la diagnostica di avvio in una macchina virtuale di Azure esistente, è possibile usare l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [az vm boot-diagnostics](/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
