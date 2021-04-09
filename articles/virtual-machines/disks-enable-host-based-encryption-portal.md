---
title: Abilitare la crittografia end-to-end usando la crittografia nei dischi portale di Azure gestiti dall'host
description: Usare la crittografia nell'host per abilitare la crittografia end-to-end su Azure Managed disks-portale di Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cdb22805e2e68893d3883272b66c2cfac13c807e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721869"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Usare la portale di Azure per abilitare la crittografia end-to-end usando la crittografia nell'host

Quando si Abilita la crittografia in host, i dati archiviati nell'host della macchina virtuale vengono crittografati a riposo e i flussi vengono crittografati nel servizio di archiviazione. Per informazioni concettuali sulla crittografia nell'host, nonché altri tipi di crittografia del disco gestito, vedere:

* Linux: [crittografia in host: crittografia end-to-end per i dati della VM](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [crittografia in host: crittografia end-to-end per i dati della VM](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di usare la proprietà EncryptionAtHost per la VM/VMSS, è necessario abilitare la funzionalità per la sottoscrizione. Per abilitare la funzionalità per la sottoscrizione, seguire questa procedura:

1. **Portale di Azure**: selezionare l'icona Cloud Shell nel [portale di Azure](https://portal.azure.com):

    ![Icona per avviare il Cloud Shell dalla portale di Azure](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
2.  Eseguire il comando seguente per registrare la funzionalità per la sottoscrizione

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

3.  Verificare che lo stato di registrazione sia registrato (richiede qualche minuto) usando il comando seguente prima di provare la funzionalità.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


Accedere al portale di Azure usando il [collegamento fornito](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Per accedere alla portale di Azure, è necessario utilizzare il [collegamento fornito](https://aka.ms/diskencryptionupdates) . La crittografia nell'host non è attualmente visibile nel portale di Azure pubblico senza usare il collegamento.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Creare una Azure Key Vault e un set di crittografia del disco

Quando la funzionalità è abilitata, è necessario configurare un Azure Key Vault e un set di crittografia del disco, se non è già stato fatto.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Distribuire una macchina virtuale

È necessario distribuire una nuova macchina virtuale per abilitare la crittografia nell'host, ma non può essere abilitata nelle macchine virtuali esistenti.

1. Cercare le **macchine virtuali** e selezionare **+ Aggiungi** per creare una macchina virtuale.
1. Creare una nuova macchina virtuale, selezionare un'area appropriata e una dimensione di macchina virtuale supportata.
1. Inserire gli altri valori nel pannello di **base** come si desidera, quindi passare al pannello **dischi** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Screenshot del pannello nozioni di base per la creazione di macchine virtuali, le dimensioni area e V M sono evidenziate.":::

1. Nel pannello **dischi** selezionare **Sì** per **crittografia nell'host**.
1. Eseguire le selezioni rimanenti desiderate.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Screenshot del pannello dischi di creazione della macchina virtuale. la crittografia nell'host è evidenziata.":::

1. Completare il processo di distribuzione della macchina virtuale, effettuare le selezioni adatte all'ambiente.

A questo punto è stata distribuita una macchina virtuale con la crittografia in host abilitata, tutti i dischi associati verranno crittografati usando la crittografia nell'host.

## <a name="next-steps"></a>Passaggi successivi

[Esempi di modelli di Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
